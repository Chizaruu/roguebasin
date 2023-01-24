# Precise Permissive Field of View

---

See [Permissive Field of View](permissive_field_of_view.md) for more information and existing implementations of the algorithm in C/C++, Haskell, Java, Python and Ruby.

A field of view is the set of destination squares which are visible from a source square. If the field of view is permissive, then a destination square is visible if and only if there is an unobstructed line from some point in the source square to some point in the destination square. I call it permissive because for a given source in a map, there are usually many more visible squares (including around corners) than in raytracing or shadowcasting algorithms.

A precise field of view calculation uses no approximation. It must take account of every piece of every square. If approximations are used, there are artifacts, especially as distance increases. Artifacts are usually squares that are not visible even when there is an unobstructed line from the source. Artifacts are problems because they allow inconsistencies to creep in. These are intuitive inconsistencies (I shoot an arrow at an orc and accidentally hit a goblin I cannot see). But they cause imperfect symmetry because artifacts are rarely symmetric. This causes situations where a player can shoot a creature who cannot shoot back, for instance, which is a situation ripe for player exploitation. Usually approximation algorithms run slower the more accurate they become. To remove all artifacts usually costs a huge amount of performance.

This algorithm was created by Jonathon Duerig. A [demo](http://jonathonduerig.com/permissive-fov/los-demo.zip) and source code for a reference implementation is available.

There is a library which implements this algorithm with bindings for C and C++ described at [permissive-fov](permissive-fov.md).

## Those pesky corners

---

First of all, tracing paths between the corners of squares is insufficient. This fails in the counterexample shown in Figure 1, created by Isaac Kuo. From the source square s, d should always be visible. This is in spite of the fact that there exists no line between s and d that intersects any corner, middle-edge point, or the centers of either s or d. Further, any approximate solution will fail given a sufficiently long hallway.

A Kuo Corridor (Figure 1):

```text
                    ####
  ###################..d
 ##...................##
 s..###################
 ####
```

Second, the extreme corners of a square must be considered open even if the square has a wall. Since movement can be in any of the eight directions, it makes intuitive sense that s can see d in Figure 2. If the corners of walls were considered obstructing, then this would not be the case.

Diagonal Wall (Figure 2):

```text
 #d
 s#
```

Third, corners are not considered valid points for creating an unobstructed line in the source and destination squares. If they were, then the previous rule would imply that s can see d in Figure 3, which is not desirable.

Corner Pillar (Figure 3):

```text
 ..d
 .#.
 s..
```

## Overall Algorithm

---

We use the point in the lower-left corner of a square to denote that square. The player is at position (0,0). We will discuss the top-left corner and bottom-right corners of squares. Given a square (x,y), the top-left corner is at point (x,y+1) and the bottom-right corner is at point (x+1,y). We will consider only the first quadrant because you can use the proper reflections to find the field of view in other quadrants easily.

The algorithm works from near to far. The map squares are visited in the following order, with @ representing the player:

```text
 .
 .
 .
 9
 5 8
 2 4 7
 @ 1 3 6...
```

Similar to shadowcasting, we will incrementally update our visible area as we move outward. We have one or more views, each containing two lines which represent the extremities of that view. As we encounter walls, we add new views, modify existing views, or end them as appropriate.

The intuition is that you are looking at a faraway object. You can move left and right between a left wall and a right wall. In between you and the object, there are a number of obstructions coming from the left and right walls. Now what happens when you are trying to see as much of the object as possible? If you want to see the right-most bit of the object, you go left as much as possible, until you get to the point where a left-side obstruction gets between you and the object or you get to the end of the line. It is the reverse if you want to see the leftmost part of the object.

Now we'll apply that inutition to the square that the player is on. Draw a line across the diagonal of the of the source square (Figure 4). The 'left wall' here is the top-left corner of the origin square. Therefore the 'rightmost' glimpse at the map is going to be from that point. Similarly, the bottom-right corner is the 'right wall' and the 'leftmost' glimpse at the map is from that point.

Figure 4:

![PPFOV-wide-view](./assets/images/articles/precise_permissive_field_of_view/PPFOV-wide-view.png)

The slope of these two lines is an invariant. Therefore, we will refer to them as the steep line and the shallow line.

The heart of the algorithm is how to incrementally update these two lines to deal with walls.

## Initial Conditions

---

The shallow line starts out going from (0,1) -> (infinity,0). The steep line starts out going from (1,0) -> (0,infinity). Approximate infinity with any sufficiently large number.

Note that it should be possible to tweak the initial conditions to calculate smaller fields of view than an entire quadrant, but there are subtleties here which I haven't worked out yet.

## Incremental Update

---

There can be many views. Each view has a shallow line and a steep line. Each line is defined by two points, the near point and the far point.

As the algorithm processes outwards, each view will contain a mutually exclusive section of map. A square can either contain a wall (obstructing visibility), or it can be empty. Given a particular view, there are five possible ways that a square can intrude upon it (Figure 5).

Figure 5:
![PPFOV-five-cases](./assets/images/articles/precise_permissive_field_of_view/PPFOV-five-cases.png)

(1) If a square is entirely below the shallow line or entirely above the steep line, then that square is not visible from this view, and does not change it. Note that because corners are not considered to obstruct, even if the top-left corner of a square is on the shallow line or the bottom-right corner of a square is on the steep line, that square is still ignored.

(2) If an empty square is between the two lines or at least one line intersects with it, then that square should be marked as visible. Since it is not an obstruction, it does not affect the lines of visibility.

(3) If both lines intersect with a wall, then that wall should be marked as visible, and the view is now completely blocked and should be destroyed.

(4) If a wall intersects just one line, then it is called a shallow bump or a steep bump if it intersects with the shallow line or the steep line respectively. The wall means that we must narrow are view appropriately. If it is a steep bump, then we replace the far point on the steep line with the bottom-right corner or the square. Similarly, if it is a shallow bump, we replace the far point on the shallow line with the top-left corner of the wall. There is a crucial subtlety, which I will discuss at length below.

(5) If a wall is between the lines, then we need to divide the current view into two smaller views. One view is created by using the shallow line from the original view and treating the bottom-right corner of the square as a steep bump. The other view is the oppposite, created by using the steep line from the original view and treating the top-left corner of the square as a shallow bump. If the top-left corner of the wall is on the steep line or the bottom-right corner of the wall is on the shallow line (or both), then it is still considered between, and an extremely narrow view is created.

## A Bump in the Night

---

Suppose that you were following the above incremental argument and you came across the situation in Figure 6. The green squares are walls that have already been processed and the green pair of lines are the initial before processing the red square. The red square is a wall that is about to be processed. It is between the lines, so we create a new view using the top-left corner of the red wall as a shallow bump. This creates the view denoted by the red lines. And there lies the problem. The new shallow line passes through a wall that we have already processed.

Figure 6:
![PPFOV-bump-conflict](./assets/images/articles/precise_permissive_field_of_view/PPFOV-bump-conflict.png)

Lets relate this to the intuitive scenario above with the faraway object. We said that we need to move left until we get to a point where a left-side obstruction gets between you and the object. That is what this wall is. It is a 'left side obstruction'. We can no longer view the map from the whole square. From now on, we can only use part of the square.

Figure 7 shows our new lines taking this into account. We replace the near point of the shallow line with the bottom-right corner of the wall. But how do we do this algorithmically? We have to maintain a list of previous shallow bumps and a list of previous steep bumps for every view. Then, when we move the shallow line because of a shallow bump, we can look through the list of steep bumps and ensure that the shallow line does not ignore any wall that was a steep bump previously. And vice versa.

Figure 7:
![PPFOV-bump-resolve](./assets/images/articles/precise_permissive_field_of_view/PPFOV-bump-resolve.png)

## Corners at the source

---

There is a final subtlety. Since the corners of the source square cannot themselves be used as sources, then whenever a bump moves a line or a new view is created, we need to check to see whether the two lines are actually the same line originating from one of the corners. In that case, we also need to consider the view blocked and destroy it.
