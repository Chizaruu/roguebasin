# Line of Sight - Tobias Downer

---

```text
Line of Sight - Tobias Downer [toby @ iglou dot com].txt

This article describes my observation of existing line of sight
systems in roguelike games, and details the method I adopted for my
own experimental engine.

  Early LOS (Moria)
 -=-=-=-=-=-=-=-=-=-

When I first played Moria on my friends Amiga in 1990, I was instantly
hooked on roguelikes.  Dungeon exploration was one of the main 'hook
factors'.  As the player moved around the dungeon, a new part would
light up revealing a little bit more.  You really didn't know what
would be around the next corner, or how the dungeon terrain would turn
out.

Compared to more recent roguelikes, Moria uses very simple line of
sight determination.  The players light source can only extend 1 grid
square around the player.  When a player enters a room that is lit,
the entire room appears and is memorised by the player.  Determining
whether a monster is visible is handled by tracing a line from the
monster to the player.  If a line can be traced without intersecting
any wall grids, the monster is visible.

There is one rather large bottleneck in this system.  It is required
that for each monster that _may_ be visible, that a line is traced.
For a map with high concentrations of monsters (such as spawning
worms), this leads to poor performance.

For most cases however, the line of sight system in Moria is fast and
effective.  Moria was written in the second half of the 80's so it
needed to be.  With the increase of processor speeds and compiler
technology, newer roguelikes have refined the LOS process further to
create more realistic engines.

  A more realistic LOS (Angband)
 -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

In most recent roguelikes, the line of sight code has extended to
encompass a 'field of view'.  An easy way to visualise this, is to
imagine a light source emitting rays of light up tp a given radius.
Every grid that is hit by a ray from the light is flagged as visible.
However some grid squares may be in shadow of the light source. The
diagram below shows a simple example of this.

 %%%%%%%%%%%%%%%
 %%%%%%%%%%%%%%%      Key:
 %%      %%%%%%%
 %#...   %%%%%%%        @ Light source
 %#......#######        # Wall hit by ray from light
 %#.......@.....        % Wall in shadow
 %#......#######        . Floor hit by ray from light
 %#...   %%%%%%%          Floor in shadow (blank)
 %%      %%%%%%%
 %%%%%%%%%%%%%%%
 %%%%%%%%%%%%%%%

There are several advantages to implementing such a system.  If a grid
'knows' about any monsters that are standing on it, as well as
flagging grids as visible, we can also flag all the monsters that are
in line of sight of the player.  We no longer need to do an expensive
line trace between monster and player to determine monster visibility.
It also allows us to incorporate nice visual effects such as fading
out the areas of the map that are in the players memory but not
directly visible.

  How do we go about writing such as routine?
 -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-

For simplicity, lets say we have a field of view with a radius of 6
grids.  One approach would be to trace a line between the light to
every edge of the field of view.  If we simplify this process a
little, lets say our field of view is a square with the light source
at coordinates (0, 0).  The diagrams below shows the first three
iterations of this method.

1st Iteration: Trace line between (0, 0) and (6, 0)

  +-----+-----+
  |           |
  |           |
  |           |
  |           |
  |           |
  |           |
  +     @******
  |           |
  |           |
  |           |
  |           |
  |           |
  |           |
  +-----+-----+

2nd Iteration: Trace line between (0, 0) and (6, 1)

  +-----+-----+
  |           |
  |           |
  |           |
  |           |
  |           |
  |           |
  +     @***  +
  |         ***
  |           |
  |           |
  |           |
  |           |
  |           |
  +-----+-----+

3rd Iteration: Trace line between (0, 0) and (6, 2)

  +-----+-----+
  |           |
  |           |
  |           |
  |           |
  |           |
  |           |
  +     @**   +
  |        ** |
  |          **
  |           |
  |           |
  |           |
  |           |
  +-----+-----+

If any line trace hits a wall then the trace is finished and a new
trace is started.  Every grid that the line intersects is marked as
visible.

Implementing such a routine is very easy.  I won't go into how to
walk along a line here.  Look up existing line drawing routines or
work it out yourself.  One thing to avoid is using floating point
numbers - keep all calculations in the inner loop as integers.

One point to note here.  When you are tracing a line, you must be
careful that you are checking _every_ grid that the line is going
through.  This is a little more involved than you may at first
realise.  Consider the following diagram.

     0   1   2

   +---+---+---+
   |   |   |   |
 0 | @ |   |   |
   |  *|   |   |
   +--*+---+---+
   |   *   |   |
 1 |   |*  |   |
   |   |*  |   |
   +---+-*-+---+
   |   |  *|   |
 2 |   |  *|   |
   |   |   *   |
   +---+---+*--+

The line travels through grids (0, 1), (1, 1), (1, 2) and (2, 2).
It we were to walk the line at intervals of 1 unit along the y axis,
we would miss the intersection with grid (0, 1) and (2, 2).  To solve
this, we actually need to walk the line at intervals of .5 units.

This simple method outlines the basic approach for achieving line of
sight determination.  However if you were to use it, you will find the
performance very poor.  Let me explain the reasons why.  Say we have a
radius of 25 grids.  A walk from the centre to the field of view edge,
assuming no wall intersections, will take 50 iterations (walking at .5
units). There are a total of 50 grids on each of the 4 edges therefore
the total number of iterations will be (50 * 4) * 50 = 10000 assuming
no wall intersections.  There are a total of 2600 grids (not including
the centre grid) in our field of view.  That means the algorithm is
checking almost four times as many grids as there are in the field of
view!

Obviously, we need to find a way to optimise the performance so that
a smaller number of grids need to be checked.  For my own experimental
engine, I used the following method.  It is almost identical to the
algorithm first suggested and implemented in Angband 2.8.3.

  A better performance LOS (Angband 2.8.3)
 =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-

The most obvious way of optimising the performance is to subdivide the
field of view and concentrate our optimisation on just one section.
We can then mirror the process in all the other sections.  If we
subdivide the field of view into eight, we create 8 octant triangles
as shown in the diagram below.  For simplicity, I'm assuming a square
field of view.  The octants are numbered 0 to 7 and proceed clockwise
around the light source.  Each octant has shared edges with two other
octants.

    +------+------+
    |\     |     /|
    | \  5 | 6  / |
    |  \   |   /  |
    |   \  |  /   |
    | 4  \ | /  7 |
    |     \|/     |
    +------@------+
    |     /|\     |
    | 3  / | \  0 |
    |   /  |  \   |
    |  /   |   \  |
    | /  2 | 1  \ |
    |/     |     \|
    +------+------+

If we optimise our algorithm to work only for octant 0, we can easily
swap x and y, and change the signs of the terms to reflect on any of
the other 7 octants.  For example, walking through x in octant 0 is
the same as walking across -x in octant 3 or 4, across -y in octant 5
or 6, and across y in octant 1 or 2.

The second rule of optimisation is to pre-compute as much as you can
and that memory allows.  Since we only need to process 1/8th of our
field of view, we have decreased the amount of memory required to
store our lookup tables.

Remember we used the analogy of a light source emitting rays of light?
If we pre-calculated every ray of light that travels through the
octant, and also find the rays of light that are blocked if a given
grid is a wall.  At runtime, we can scan through the octant grid,
moving away from the light source, lighting up the grids that have at
least one ray of light hitting them.  Any grids that represent a wall
will flag the rays that hit the grid as blocked, thereby putting
others in shadow. The following diagram (hopefully) demonstrates
this principle.

        0       1       2       3       4

    +-------+-------+-------+-------+-------+
    |       |       |       |       |       |
    |       |       |       |       |       |
    |       |       |       |       |       |
  0 |   @11 |       |       |       |       |
    |    65322221111111     |       |       |
    |     65333 22222  11111111111  |       |
    |      6|54333  |22222  |     11111111  |
    +-------6-55--333#####22222-----+#####111
            |6  554 #333    #  22222#       #
            | 6   5444  333 #       22222   #
            |  6   55 444  333      #    2222
  1         |   6   #55  44 # 333   #       #
            |    6  #  5   44    333#       #
            |     6 #   55  #444    333     #
            |      6#     55#   444 #  333  #
            +-------6#######5------444####333
                    |6      |55     | 44    |
                    | 6     |  55   |   444 |
                    |  6    |    5  |      44
  2                 |   6   |     55|       |
                    |    6  |       55      |
                    |     6 |       | 5     |
                    |      6|       |  55   |
                    +-------6-------+----55-+

The diagram shows the pre-computation of 6 rays of light where each
line (except 1) intersects the grid at (2, 1).  Lines 2 and 6 'brush'
past the corners of the grid, and lines 3, 4 and 5 travel through the
middle of the grid.  Because there is a wall at grid (2, 1), lines
3, 4 and 5 would be flagged as 'blocked'.  This means no rays are
able to hit grid (3, 2) or (4, 2) thus putting them in shadow.

If we pre-calculate each line to every grid corner in an octant
discarding any identical lines, and sort into a list, we produce a
list of all unique lines sorted from the horizontal to the diagonal of
the octant.  We can then pre-calculate a lookup table for each grid
square in the octant, and store the range of rays that are blocked if
the given grid is a wall.  For example, in the above diagram the grid
at (2, 1) would block all rays between 2 and 6.  The grid at (4, 1)
would block all rays between 1 and 4.

We can store the list of rays in a bit vector, or boolean array.  If a
wall grid is found, we simply flag the bits between the outer bounds
of the given grid as blocked.  A similar test can be used to determine
if an unblocked ray hits a grid.  Simply test each bit in the vector
between the two outer corners, if an unblocked bit is found then the
grid is visible.

This method is very fast since it requires most grids to be scanned
only once.  The exception is along the horizontal, vertical and
diagonal which are shared between two octants.  These grids need to
be scanned twice, once by each shared octant.

It is easy to adapt the algorithm to produce a circular field of view
as opposed to a square field of view.  Simply use the Pythagorean
theorem to determine if a given coordinate (x, y) is within the
radius;

  (x * x) + (y * y) < (radius * radius)

Angband uses an octagonal field of view;

  max(x, y) + (min(x, y) / 2) <= radius

We could also use this algorithm to determine if a given grid is
visible from another.  There are two ways of doing this.  Find the
octant the destination grid is in relative to the source.  Then do
the 'blocking ray' method described above.  When we reach the
destination grid, if no rays are hitting it, its not visible.  The
second method is to find the destination grid, and for each ray that
intersects the grid, trace a line from the source.  If one of the
traced rays hits the grid without intersecting a wall, the grid is
visible.

  Performance
 =-=-=-=-=-=-=

I implemented this algorithm in Java and used a radius of 25 grids
with a circular field of view.  It performed particularly well when
there were only a few wall grids, or if the source was enclosed by
many wall grids.  It didn't perform so well between these extremes.
However on my P120, the worse case tested speed was 5 ms (200 a
second).

  Acknowledgments
 =-=-=-=-=-=-=-=-=

For a more detailed description of this method, download the Angband
2.8.3 source and look in the file 'cave.c'.  Thanks go to Mat
Hostetter and Ben Harrison for developing and implementing the
algorithm.


If you have any questions about this article, feel free to email me
at geoffd@iglou.com.
```
