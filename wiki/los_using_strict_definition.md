# LOS using strict definition

---

## Introduction

---

This article aim for developers looking for an uncommon elegant line of sight solution to implement themselves.

When looking for line of sight and field of view algorithms i was sure hoping there was the "simple and obvious way" to do things. There is not. In fact, there is close to a dozen of fov implementations to choose from in the wiki only, trying to emulate three main definitions (see next chapter).

Choice must depend of your programming skills of the RL programmer, and the desired behavior of your algorithm. There is several desired behaviors. If you haven't yet, check out [Comparative_study_of_field_of_view_algorithms_for_2D_grid_based_worlds](comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds.md) which is a great article to start with.

In this article, we are going to focus on the corner-peeking behavior, as it's a core problem of line of sight algorithmic.

## Different definitions in a nutshell

---

```text
########
.@......
####O###
---#.#--
```

The above example is a typical line of sight algorithm dilemma ; in the geometry, we may or may not consider walls obstruct @'s view to O, as well as we may or may not consider the reverse is true. It depends on how one defines the fact of "being able to see" in a grid based world. Here are the 3 valid (i.e. deterministic) ways to think about the problem :

- **@ and O should see each other**. That is to say, if there is any unobstructed line from viewer square to the object square the object is seen. This is [Permissive_Field_of_View](permissive_field_of_view.md) approach.

Pros : easier to explore. Symmetrical behavior. Lot of existing implementations.

Cons : Cautious gameplay made easier. Hard to hide from ranged combat. Single pillars can cast uncontinuous shadow.

- **@ should see O but not the reverse**. That is to say if there is any line from the center of the viewer square to the object square the object is seen. This is called [Shadow casting](shadow_casting.md) approach.

Pros : Fairly aesthetical. Asymetry can create interesting gameplay. Lot of existing implementations.

Cons : Asymetric thus easily abused in ranged combat. Implementation and definitions may differ.

- **@ and O shouldn't see each other**. That is to say there must be center to center unobstructed line from viewer to object so that object is seen. We are going to discuss this approach.

Pros : Claustrophobic gameplay. Symetrical. Fairly aestetical. Fairly unique.

Cons : Sometimes can create uncontinuous ray of lights. Do-it-yourself implementation (although, the algorithm isn't complex).

## Different situations

---

We are going to mark lit walls as '#' and unlit as '-', lit floor as '.', unlit as '?'.

Close pillars

```text
??????.......
.?????.......
...????....??
.....?#..????
.......@#????
.........????
...........??
```

Distant pillar

```text
..........???
........?????
......???????
....???????..
...#??.......
@............
```

## The wall guessing problem

The main problem with the stricter solution is that the final display contain less information than the algorithm have used to calculate it. Let's use a simple example : Player is standing in a corridor.

```text
------###------
.......@.......
------###------
```

Since I can't draw any unobstructed line to corridor walls center, according to definiton, player should only be able to see a single line and 6 wall units.

But the fact that his fov is reduced to a single line of floor clearly indicates the player he is standin in a corridor. If only the 6 lit walls were obstructing his vision, he'd see two clear cones of lit floor.

```text
...         ...
..... ### .....
.......@.......
..... ### .....
...         ...
```

This makes exploration very tedious by forcing player to go near objects he have an implicit knowledge of. So we have to make our algorithm a tiny more permissive, by differentiating vision-obstructing tiles from others.

We should start by litting any wall that obstruct the view and the player is able to guess.

```text
----#######----
.......@.......
----#####+#----
```

Now, the fix breaks something else : if a monster opens the door, the door is no longer obstructing player view. If we stick to original definition, it should go back in dark, thus allowing the player predict a monster opened it. This is unwanted.

In most roguelikes the player have a map and fills it through exploring. If we want to give player knowledge of the layout, without giving informations of monsters being in it, we should mark them as seen in the memory map and display them as memorised tile. The player would know there has been an obstructing tile further and know it's nature, but not whether it have been opened or not before having a real line of sight.

## Implementation

---

The strength of this algorithm is that implementation is simple, exact (no artifact or asymmetry) and somewhat efficient CPU wise. You're goingto be casting a ray to each tile within a circle, hence this algorithm should be considered to be somewhere on an order of radius^3 cycles to compute.

We will rely on iteration of a custom los algorithm. This los algorithm calculate points distance to a line using it's equation. If a wall tile is at less than 0.5 distance of source-destination line, then we know it's obstructed.

For every target tile we have to build a line. The final tile is lit if and only if the line is unobstructed. We must also tag obstructing walls as memorised in case a line of sight is obstructed.

Following C code compute a radius-wide fov :

```c
void fov(int x, int y)
{
    int i,j;
    for (i = -(int)radius; i <= radius; i++) //iterate out of map bounds as well (radius^1)
        for (j = -(int)radius; j <= radius; j++) //(radius^2)
            if(i * i + j * j < radius * radius)
                los(x, y, x + i, y + j);


};

/* Los calculation */
void los(int x0, int y0, int x1, int y1)
// By taking source by reference, litting can be done outside of this function which would be better made generic.
{
    int sx, sy, xnext, ynext, dx, dy;
    float dist;

    dx = x1 - x0;
    dy = y1 - y0;

    //determine which quadrant to we're calculating: we climb in these two directions
    sx = (x0 < x1) ? 1 : -1;
    sy = (y0 < y1) ? 1 : -1;

    xnext = x0;
    ynext = y0;

    //calculate length of line to cast (distance from start to final tile)
    dist = sqrt(dx * dx + dy * dy);

    while (xnext != x1 || ynext != y1) //essentially casting a ray of length radius: (radius^3)
    {
        // TODO: check map bounds if required
        if (map[xnext][ynext] == WALL) // or any equivalent
        {
            tag_memorised(xnext, ynext); // make a note of the wall
            return;
        }

        // Line-to-point distance formula < 0.5
        if(abs(dy * (xnext - x0 + sx) - dx * (ynext - y0)) / dist < 0.5f)
            xnext += sx;
        else if(abs(dy * (xnext - x0) - dx * (ynext - y0 + sy)) / dist < 0.5f)
            ynext += sy;
        else
        {
            xnext += sx;
            ynext += sy;
        }
    }
    lit (x1, y1)
};
```

## Efficiency

Differentiating field computation from line computation makes it very efficient algorithm which can be used for large datas. The fact it is perfectly symetric also enable re-use of computations ; monsters that are lit by the player can be considered as seeing the player without los check.
