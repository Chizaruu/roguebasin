# Delving a connected cavern

---

Both various maze (spanning tree) algorithms and cellular automata have been applied for generating dungeons in roguelikes. The algorithm described below is somewhere in between; it is related to the randomized Prim's and the growing tree maze algorithms; it has also a lot in common with cellular automata, although the cells are not processed simultaneously.

The algorithm operates on a rectangular grid of cells (a hex version exists), which can be WALL or FLOOR; other terrain is left untouched. Each cell has 8 neighbours - diagonals count. There is also a store with two operations: putting a cell on top, and drawing (pulling) a cell from the store; this cell is selected randomly from:

- all cells in store, if there are less than 125,
- top 25 \* cube_root(N_store) cells otherwise.

The cell that is drawn (unless it is the topmost one) is replaced by the topmost cell remaining in store.

The parameters that affect the behaviour of the algorithm:

- ngb_min, ngb_max: the minimum and maximum number of neighbouring floor cells that a WALL cell must have to become a FLOOR cell; 1 <= ngb_min <= 3; ngb_min <= ngb_max <= 8;
- connchance: the chance (in percent) that a new connection is allowed;
- cellnum: the desired number of FLOOR cells; the actual number may be less if there is not enough space.

The algorithm starts with most of the map filled with WALL, with a "seed" of some FLOOR cells; their neigbouring WALL cells are in store. Then the main loop is repeated:

```text
while the wanted FLOOR count isn't reached, and drawing a cell succeedes:

    if the drawn cell is within map limits, and

        it is a WALL cell, and
        it has from ngb_min to ngb_max of FLOOR neighbours, and
        making it FLOOR either won't open new connections, or a random

            chance (connchance percent) allows opening a new connection,

    then the cell is made FLOOR, and its WALL neighbours are put in

        store in a random order.
```

The check for new connections is done by a table lookup, after encoding the terrain type of the neighbouring cells in an 8-bit value: FLOOR is 1, anything else 0, the least significant bit is towards the right, then clockwise.

In C, the main loop looks as follows (flo is FLOOR, and ava is WALL):

```c
while ((count < cellnum) && rndpull(cstore, &x, &y))
{
   ngb_count = ngbcount(mpc, x, y, flo);
   ngb_groups = ngbgroups(mpc, x, y, flo);
   if ( inbord(mpc, x, y) && (mpc.map[x][y] == ava) &&
       (ngb_count >= ngb_min) && (ngb_count <= ngb_max) &&
       ((ngb_groups <= 1) || (rnd_i0(100) < connchance)) )
       {
          count += digcell(mpc, cstore, x, y, flo, ava);
       }
}
```

If connchance is 0, and the initial seed contains no loops, the algorithm won't create any; it also won't open connections to any other FLOOR areas that may happen to be on the map. If the initial seed is connected, so is the resultant pattern.

Depending on the values of the parameters (ngb_min, ngb_max, connchance), the algorithm can generate a variety of patterns:

- (1, 1, 0) a narrow maze: [[1]](http://show.simpload.com/index.php?filename=0125497c6b59e400f.gif)
- (2, 3, 0) a wider maze (insect nest?): [[2]](http://show.simpload.com/index.php?filename=0125497c6c861018e.gif);
- (1, 8, 0) a cavern with narrow tunnels; [[3]](http://show.simpload.com/index.php?filename=0125497c6c4c40b2d.gif);
- (3, 8, 0) a wider and more rounded cavern: [[4]](http://show.simpload.com/index.php?filename=0125497c6cf196714.gif).

Nonzero connchance allows the algorithm to produce pillars and other WALL areas surrounded by FLOOR, for example this (2, 4, 5) one: [[5]](http://show.simpload.com/index.php?filename=0125497c6cb1d0e36.gif)

Large patterns look 'fluffy' as this 640x640 (1, 3, 0) pattern shows: [[6]](http://show.simpload.com/index.php?filename=0125497c6c163a905.gif) The formula in the store cell selection was chosen after some experimentation for that very reason. I don't know if they are actually fractal - checked up to 5500x5500, and they look similar; but how it scales for even larger sizes I don't know. However, this is probably beyond the typical dimensions of maps in roguelikes ;-)

In the above examples, the desired number of FLOOR cells was not given; the implementation linked to below tries to select a value so that the pattern occupies some 30-40 percent of the map. If the number is given, and it is close to the total number of available cells, the algorithm will fill all available space, For larger patterns this may create fragments of 'biased' maze - see lower right of this (2, 5, 0) pattern: [[7]](http://show.simpload.com/index.php?filename=0125497c6cd01fbcd.gif)

It is also possible that for some values of ngb_min and ngb_max the algorithm digs ouit far fewer cells than desired, despite terrain being available. This happens when all the neighbours of the FLOOR cells in the existing pattern have the wrong number of FLOOR neighbours. In the rectangular case, (2, 2, \*) and (3, 3, \*) are almost unusable, but for other parameter values such behaviour is very improbable.

A C implementation of the rectangular version: [[8]](http://angband.pl/files/delve_b.c)

A C implementation of the hex version: [[9]](http://angband.pl/files/delve_hex.c)

Many variants are possible, for example:

Choosing the drawn cell always randomly from all the store results in compact, often biased, seed-centric patterns.

If only the 4 non-diagonal neighbours are stored after a cell is dug, and connchance == 0, all points in the generated maze can be reached by moving in the 4 cardinal directions (as long as the seed allows this too).

Drawing always the bottom cell, and storing the 8 neighbours clockwise or anticlockwise, starting from a random one, makes winding, for some parameters spiral patterns like this (3, 8, 0) one: [[10]](http://show.simpload.com/index.php?filename=0125497c6d30581af.gif)

It is also possible to change the arguments after delving some cells, resulting in a mixture of various kind of mazes; in this (2*, 3*, 5) pattern the values of ngb_min and ngb_max were changed every 1000 cells: [[11]](http://show.simpload.com/index.php?filename=0125497c6c9cdce1c.gif)

Other things that might be worth trying: restricting the store so that a cell can be only stored once, or eliminating the shuffling done when the drawn cell is replaced by the topmost one.

One criticism of this algorithm is that cavern branches lead to dead ends, which in certain games may lead to restrictive gameplay, both in terms of making it harder to escape monsters and more repetitive to explore the level fully. For dungeon exploration based games one way to alleviate this is to add an additional step to connect branch ends together.
Generalization

The encoding of the WALL / FLOOR pattern of the cell's neighbours in an 8-bit value allows a relatively easy generalization of the algorithm. Instead of just counting the number of the WALL and FLOOR cells and their connections, one can use the lookup table to keep the probability of digging being permited for every possible neighbour pattern. One can make such a 'digperm' table for any given (ngb_min, ngb_max, connchance), so all dungeon types described above are still available. There are however many digperm tables not corresponding to any such triple, so new possibilities emerge.

A C implementation of this version of the algorithm (delve_dgp.c) is available at:[[12]](http://kusigrosz.jimdo.com/roguelike-related)

After compiling, it can be called:

```text
 a.out name pullflag storeflag cellnum
```

The name is the name of one of digperm tables hardwired in the code (try, e.g. 'rmaze5' or 'dmaze2'). If the first argument is not a valid name, a random digperm table is generated. The table used is dumped to a file (default name lastdigperm.txt), so it can be added to the program if so desired.

The arguments: pullflag, storeflag and cellnum are optional:

```text
    pullflag: 1, 2, or 3, determines how cells are selected from the store:

        1 (PULL_CUBEROOT, default), select the cell randomly from all the store if N_store < 125, and from the top 25 * cube_root(N_store) otherwise; this generates 'fluffy' dungeons,
        2 (PULL_ALL) selecting the cell randomly from all the store; generates compact dungeons,
        3 (PULL_BOTTOM) select always the bottom cell; usually generates winding, sometimes spiral dungeons.

    storeflag: 1, 2, or 3, specifies in what order the WALL neighbours of a freshly dug cell are stored:

        1 (STORE_PERM, default), store the WALL neighbours in random order,
        2 (STORE_CW), clockwise, starting from a random one,
        3 (STORE_CCW), counterclockwise, starting from a random one.

    cellnum - the desired number of cells to be dug.
```

A few things worth considering when making a custom digperm table:

If table entries corresponding to the same neighbour pattern differently oriented have significantly different probabilities, the generated dungeon tends to 'lean' in one direction. I didn't find such mazes useful, so in the code I ensure this symmetry when creating the table. This also means fewer values need to be specified in the strings describing the digperm tables.

If mirror images of the patterns have different digging chances, chirality ensues. This may have its uses - I chose not to enforce this symmetry.

Some patterns are very important for dungeon expansion:

```text
###   ###   ###   ###   ###
#c.   #c#   #c.   #c#   #c#
###   ##.   ##.   #..   ...
```

if all of them have low digging probabilities, the dungeon is likely to die young. When generating the table randomly. I make sure at least one of them has probability 1000 permil.

A sample of possible dungeon types can be seen here: [[13]](http://www.freeimagehosting.net/uploads/20c27738d9.png)

Custom digperm tables may result in a significant chance of the dungeon generation finishing well before the desired number of cells. It is recommended that a given table is tested thoroughly before using it in an actual game. Even for tested tables, it may be a good idea to enclose the call to the delving routine in a loop, undoing and retrying if necessary.

## See also

---

### Related topics

- [Maze Generation](maze_generation.md)
