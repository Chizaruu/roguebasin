# Mutual Visibility Field Of View

---

by Jakub Debski

## INTRODUCTION

---

In this article I will not write what the Field Of View (FOV) is and will not write about all the possible ways to implement it. Instead I will focus an algorithm differing from the others in visibility. Mutual visibility guaranties, that if cell_1 is visible from cell_2, then cell_2 is visible from cell_1. The algorithm was designed mostly for turn based games with ranged weapons. In such games lack of mutual visibility cause unfair advantage of attacker, when in reality target should be on better position.

Let's look at the typical situation. We have a crossroad and two monsters with ranged weapons (1 and 2).

```text
######################
...................2..
#####1################
#####.################
```

With standard FOV Monster 2 will see the monsters 1...

```text
######################
...................2..
#####1################
%%%%%%%%%%%%%%%%%%%%%%
```

... but monster 1 will not see the monster 2!

```text
%%%#####%%%%%%%%%%%%%%
%%%%...%%%%%%%%%%%%%%%
%%%%#1#%%%%%%%%%%%%%%%
%%%%#.#%%%%%%%%%%%%%%%
```

'#' are cells that block LOS, dots are cells that do not block and '%â€™ are not visible cells.

Monster 2 has unfair advantage over 1 although monster 1 is hidden behind the corner. Monster 1 should be able to lean out the corner and score a headshot or throw grenade, but it even does not see the threat! This situation can lead to instant death of 1.

What we need is mutual visibility in field of view.

## MUTUAL FOV

---

To explain how mutual visibility can be achieved letâ€™s zoom the proper FOV. Simple observation gives us the answer. Visible are these cells, which have any corner visible from any corner of the starting cell.

```text
########........########........########.
########.       ########.       ########.
########.       ########.       ########.
########.       ########.       ########.
########.       ########.       ########.
########.       ########.       ########.
########.       ########.       ########.
*#######.       ########.       ########.
.*.......................................
. **    .       .       .       .       .
.   **  .       .       .       .       .
.     **.       .       .       .       .
.       **      .       .       .       .
.       . **    .       .       .       .
.       .   **  .       .       .       .
.       .     **.       .       .       .
%%%%%%%%########*.......########%%%%%%%%.
%%%%%%%%########.       ########%%%%%%%%.
%%%%%%%%########.       ########%%%%%%%%.
%%%%%%%%########.       ########%%%%%%%%.
%%%%%%%%########.   @   ########%%%%%%%%.
%%%%%%%%########.       ########%%%%%%%%.
%%%%%%%%########.       ########%%%%%%%%.
%%%%%%%%########.       ########%%%%%%%%.
................*#######********%%%%%%%%.
.       .      *#########*######%%%%%%%%.
.       .     * ##########*#####%%%%%%%%.
.       .    *  ###########*####%%%%%%%%.
.       .   *   ############*###%%%%%%%%.
.       .  *    #############*##%%%%%%%%.
.       . *     ##############*#%%%%%%%%.
.       .*      ###############*%%%%%%%%.
........*................................
```

Corners are mutually visible when connecting line (ray) does not intersect any blocking wall. The simplest implementation of this algorithm is to cast rays from 4 corners of the starting cell to 4 corners of every cell and check intersections. It would be extremely slow and we would have to find a proper way to find intersecting cells. We cannot use the Bresenhamâ€™s line algorithm for that, because the line would not go exactly through the required cell. Additional tests would slow the algorithm even more.

As an alternative we should use the observation, that the map is the domain of the right angles. It simplifies calculating of intersections. We also can see that the line is going from corner to corner, not from cell to cell. It reduces required calculations 4 times.

### RULES

These observations allow us to define rules of the algorithm.

Rule 1. We have to cast a ray from 4 corners of starting cell to all the corners in FOV.

Rule 2. If the corner is visible, then the 4 cells 'around' it (the corner belongs to) are visible.

Rule 3. If the line from start corner to end corner intersects any blocking cell, then the end corner is not visible.

Suppose that we cast the ray from top-left corner of cell 'S' to bottom-right corner of cell 'E'.

```text
S..     S#.
###     #.#
..E     .#E
```

In both cases line goes through 2 corners of central cell, but only in first case it is blocked.

Rule 4. When the line intersects corners, additional rules have to be applied. These rules will be shown later.

We have the rules defined and now we have to find the proper way of intersection calculation.

### INTERSECTIONS

As we live in the world of right angles and the distance between cells is always equal 1, calculating intersections is easy task.

```text
--------------------------delta_x------------------------
start               x=3
0*************1*************2*************3.............4     |
  ..          .             .             *             .     |
    ..        .             .             *             .     |
      ..      .             .             *             .     |
        ..    .             .             *             .     |
          ..  .             .             *             .     |
            ...             .             *             .     |
              ..            .             *             .     |
              . ..          .             *             .     |
              .   ..        .            n_y            .     |
              .     ..      .             *             .     |
              .       ..    .             *             .     |
              .         ..  .             *             .     |
              .           ...             *             .     |
1.........................................*..............   delta_y
              .             . ..          *             .     |
              .             .   ..        *             .     |
              .             .     ..      *             .     |
              .             .       ..    *             .     |
              .             .         ..  *             .     |
              .             .           ..*             .     |
              .             .             ..            .     |
              .             .             . ..          .     |
              .             .             .   ..        .     |
              .             .             .     ..      .     |
              .             .             .       ..    .     |
              .             .             .         ..  .     |
              .             .             .           ...     |
2...................................................... E
                                                        end
```

```text
delta_x=abs(end.x-start.x);
delta_y=abs(end.y-start.y);
```

We are looking for the point (x,n_y) that the line intersects (here at x=3). From the Thales' theorms we know that:

```text
   x         n_y
-------  = -------
delta_x    delta_y
```

therefore

```text
n_y=x*delta_y/delta_x;
```

If delta_x is equal zero, we can skip checking vertical intersections and just focus on horizontal. To avoid float operations, "modulo" can be used:

```text
r_y=x*delta_y%delta_x;
```

If r_y is equal zero, then intersection is at the corner and special rules must be used. In other case the line intersects the wall at position (start.x + x,start.y + n_y).

As a result if cell[start.x + x][start.y + n_y] or cell[start.x + x - 1][start.y + n_y] blocks the LOS, then the end corner is not visible.

The same calculation we perform looking for horizontal intersections, but for known y:

```text
n_x=y*delta_x/delta_y;
```

### NEAREST CELLS

The ray is blocked if it intersects anything on the way, but we must not check the starting and ending corners. Stopping if the end corner blocks LOS would make the destination wall invisible. Stopping on start corners would block all the rays in such situation:

```text
#.#
.S.
#.#
```

Hence we have to add additional tests for nearest cells.

```text
S..
.#.
..E
```

In the case above between startâ€™s bottom-right corner and endâ€™s top-left there is nothing that blocks! Both corners of internal cell are the start and end corners and the E cell would be visible.

### ALGORITHM

Finally I can present the algorithm:

```text
1. Set all the corners as not visible
2. To each corner in the FOV (end corners)
        from each corner from start (4 corners)
      Check if ray from start to end is blocked
        If any ray these 4 was not blocked
      Then the end corner is visible from start.
3. For each visible corner in FOV
        Mark 4 cells around it as visible.
```

Checking if ray from start to end is blocked:

```text
1. Check the nearest cells (situation described in 2.3)
2. For each x from start.x to end.x calculate y
         If at (x,y) is a vertical wall, then ray is blocked
              If (x,y) is a corner, use additional rule
3. For each y from start.y to end.y calculate x
         If at (x,y) is a horizontal wall, then ray is blocked
              If (x,y) is a corner, use additional rule
```

ADDITIONAL RULES FOR LINES INTERSECTING CORNERS

The ray is blocked at the corner only sometimes. It is always blocked when the cells around the corner are placed this way:

```text
##  ..  #.  .#
..  ##  #.  .#
```

It's also blocked when ray goes diagonally to blocking cell.

```text
#.  .#
.#  #.
```

Ray is not blocked in other cases.

## OPTIMIZATION

---

Simple optimization can be performed, but it does not improve the speed very much.

If we check destination (end) corners in a spiral:

```text
13 14 15 16 17 18
32 1  2  3  4  19
31 12 S  S  5  20
30 11 S  S  6  21
29 10 9  8  7  22
28 27 26 25 24 23
```

then we can use 'shadow propagation'. It means that if corner is blocked, all the further corners in the same direction are blocked too:

```text
...............%..
.............%....
...........%......
.........%........
.......%..........
.....#............
...S..............
```

## PERFORMANCE

---

Performance was measured on Intel Pentium 1,8GHz and is presented in FOV calculations/sec (Fc/s). Calculations were done on demo level (80x25 cells).

Typical roguelike level, FOV radius 10: 13200 Fc/s Typical roguelike level, FOV radius 80: 3400 Fc/s

Open area with a few walls, FOV radius 10: 8300 Fc/s Open area with a few walls, FOV radius 80: 980 Fc/s

Without the "shadow propagation":

Typical roguelike level, FOV radius 10: 12500 Fc/s Typical roguelike level, FOV radius 80: 2700 Fc/s

Open area with a few walls, FOV radius 10: 7700 Fc/s Open area with a few walls, FOV radius 80: 880 Fc/s

The complexity of this algorithm is O(n^2), when n is the number of corners.

## CONCLUSIONS

---

The Mutual FOV is not the fastest FOV algorithm possible, but is fast enough (look at the previous section). It gives mutual visibility of cells, no problems with long corridors, no problems with corners... and just works very well :) You are free to use it.

## DEMO

---

C++ Source code + Windows executable is available here <http://www.alamak0ta.republika.pl/mutual_fov.zip>
