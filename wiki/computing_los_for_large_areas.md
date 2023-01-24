# Computing LOS for Large Areas

---

Computing LOS for Large Areas - Gordon Lipford <mailto:gclipford@sympatico.ca>

## Summary and Genesis

---

In most roguelike games, there is a need to calculate which dungeon grids are visible in a direct line of sight to the player. Closely tied to this is a need to calculate whether or not a dungeon grid is 'lit' by a player light source of potentially variable radius.

Using ray-tracing to calculate LOS is a well known and excellent method to address both these needs, if the radius involved is reasonably small. The problem with ray-traced LOS algorithms lies only with their memory consumption, which increases as O(radius3). As per most LOS algorithms, every cell within the radius of computation must be visited at least once; ray-traced LOS visits each cell only once and so takes O(radius2) to complete.

Using shadow casting, one may reduce the memory consumption to O(radius), while keeping computation time O(radius2) - though probably still slower than ray-tracing.

Two other features of shadow casting that may prove useful:

- radius may be changed dynamically; very little recomputation necessary.
- shadows may be 'relaxed' such that a blocker takes up less than the entire cell - normal calculations assume that a blocker takes up the entire (square) cell. This involves very little modification of the algorithm itself, and in fact may be parameterized such that it can be changed dynamically.

## General Method

---

This algorithm is given for a single octant (45 degrees of a circle, or one eighth of a square). It is left as an exercise for the reader to translate the algorithm to other octants; however, four arrays and some pseudo-code are given in [Octant Translation](#octant-translation) below.

The player is treated as a geometric point in the center of cell (0,0). Each grid cell (defined below) may contain something which blocks the player's line of sight.

Given a LOS radius of N, we traverse the octant from (0,0) to (N,N) one column at a time, from cell (0,y) to (y,y) within the column. We examine each cell to see if it is blocked. If so, we perform two calculations:

1. Find the inverse slope of a line from the center of (0,0) to the upper left corner of the cell, and
2. Find the inverse slope of a line from the center of (0,0) to the lower right corner of the same cell.

```text
                      ____________
                      |          |
                      |          |
                      |  (2,2)   |
                      |   U.     |
           ___________|_UU_______|
           |         UUU#########|
           |       UU |##########|
           |    UUU   |# (2,1) ##|
           |  UU      |##########|
___________|UU________|##########|LLLL..
|        UUU          |  LLLLLLLLL
|      UU  |   LLLLLLLLLL        |
|  (0UULLLLLLLL(1,0)  |  (2,0)   |
|          |          |          |
|__________|__________|__________|
```

In this example, (2,1) is blocked. The line corresponding to the upper bound of the shadow cast by the blocker at (2,1) has a slope of 1.5 / 1.5 = 1.0. The inverse of this slope is also 1.0. Similarly for the lower bound, slope is 0.5 / 2.5 = 0.2. The inverse slope of 0.2 is 5.0.

Note that any grid blocker in cell 0 will generate a _negative_ lower slope. When this happens, assign some arbitrarily large value.

The result of #1 is assigned to the 'upper shadow max', and the result of #2 is assigned to the 'lower shadow max' (see below).

The algorithm then uses these values to determine whether or not other cells farther out than the blocker are in it's shadow or not. In this example, the shadow will 'grow' upwards at a rate of 1 grid every 1.0 steps (i.e. every step), and the lower bound of the shadow will 'decay', exposing the cell(s) to light, at the rate of 1 grid every 5.0 steps.

Note that the use of floating point arithmetic is NOT necessary for this algorithm. All formulas are presented using floating point in order not to confuse the algorithm with its implementation.

Also note that the 'relaxing' of the algorithm can be done at precisely this point by assuming the blocking cell to occupy less than the full grid square.

## Algorithm Terms

---

Within the algorithm, a Cell is a working representation of a grid square. Each cell has several properties:

- upper shadow count (numeric)
- upper shadow maximum (numeric)
- lower shadow count (numeric)
- lower shadow maximum (numeric)
- visible (boolean)
- lit (boolean)
- lit_delay (boolean)

The first four of these will be referred to in the specific algorithm as Cell[n].upper_max, Cell[n].up_count, Cell[n].low_max, and Cell[n].low_count.

To 'initialize' a Cell, set all integer values to 0, and all Boolean values to 'true' except for lit_delay.

A Cell has 'reached' it's upper maximum if the upper maximum is non-zero, count+0.5 is >= the maximum, and count-0.5 <= the maximum.

Similarly for a Cell 'reaching' it's lower maximum.

## Specific Algorithm

---

```text
allocate an array of Cells.  The array is one dimensional and
indexed from 0 to N (so it has a size of N+1).  No cell initialization
is necessary at this time;  it occurs within the inner loop in very
specific places.  There is no need to re-initialize anything between
octant calculations.

begin function los_octant:

boolean variable VISIBLE_CORNER
// this is necessary for aesthetic reasons.  Without this variable
// and associated hack, a dead-end will appear as follows:
//       ######
//       .@....#
//       ######
// Although this is geometrically correct (at least if the blockers
// occupy the entire grid cell),  it is more pleasing to see this:
//       #######
//       .@....#
//       #######

boolean variable BLOCKER
// convenience: does the current cell represent a grid square that
// blocks LOS?

numeric UP_INC
numeric LOW_INC
numeric SOUTH
// always CELL-1.  Convenience.

// Cell (0,0) is assumed to be lit and visible in all cases.
initialize Cell[0]
VISIBLE_CORNER = false

// now for the main double loop:
for each COLUMN in (1.. N)
for each CELL in (0.. COLUMN)

assign TRUE to BLOCKER iff the object at grid (COLUMN, CELL) will
block the players LOS

UP_INC = 1
LOW_INC = 1
SOUTH = CELL - 1

// STEP 1 - inherit values from immediately preceding column
//          light up from lit_delay if appropriate
//          'steal' lower bound shadow from 'south' cell if
//          if it lit
if CELL < COLUMN
    if Cell[CELL].lit_delay
        if not BLOCKER
            if Cell[SOUTH].lit
                if Cell[SOUTH].low_max <> 0
                    Cell[CELL].lit = false
                    Cell[CELL].low_max = Cell[SOUTH].low_max
                    Cell[CELL].low_count = Cell[SOUTH].low_count
                    Cell[SOUTH].low_max = 0
                    Cell[SOUTH].low_count = 0
                    LOW_INC = 0
                else
                    Cell[CELL].lit = true
                endif
            endif
        endif
        Cell[CELL].lit_delay = false
    endif
else
    initialize Cell[CELL]
endif

// STEP 2 - check for blocker
//          a dark blocker in a shadows edge will be visible
//          (but still dark)
if BLOCKER
    if Cell[CELL].lit OR (CELL > 0 AND Cell[SOUTH].lit) OR VIS_CORNER
        VIS_CORNER = Cell[CELL].lit
        Cell[CELL].lit = false   // blockers are always dark
        Cell[CELL].visible = true  // but always visible if we get here..

        calculate temporary UPPER and LOWER values for this grid position

        if UPPER < Cell[CELL].up_max OR Cell[CELL].up_max == 0
            // new upper shadow
            Cell[CELL].up_max = UPPER
            Cell[CELL].up_count = 0;
            UP_INC = 0
        endif
        if LOWER > Cell[CELL].low_max OR Cell[CELL].lower == 0
            // new lower shadow
            Cell[CELL].low_max = LOWER
            Cell[CELL].low_count = -1
            LOW_INC = 0
            if LOWER <= 3  // somewhat arbitrary, but looks right
                Cell[CELL].lit_delay = true
            endif
        endif
    else
        Cell[CELL].visible = false
    endif
else
    Cell[CELL].visible = false
endif

// STEP 3 - add increments to upper and lower counts
add UP_INC to Cell[CELL].up_count
add LOW_INC to Cell[CELL].low_count

// STEP 4 - look south to see if we've been overtaken by shadow
if CELL > 0
    if Cell[SOUTH] has 'reached' upper maximum
        if Cell[CELL] has NOT 'reached' upper maximum
            Cell[CELL].up_max = Cell[SOUTH].up_max
            Cell[CELL].up_count = Cell[SOUTH].up_count
            subtract Cell[SOUTH].up_max from Cell[CELL].up_count
        endif
        Cell[CELL].lit = false
        Cell[CELL].visible = false
    endif

    // STEP 5 - erase current lower shadow if one is active in the
    //          cell to our south

    if Cell[SOUTH] has 'reached' lower maximum
        Cell[CELL].low_max = Cell[SOUTH].low_max
        Cell[CELL].low_count = Cell[SOUTH].low_count
        subtract Cell[SOUTH].low_max from Cell[CELL].low_count
        set both Cell[SOUTH].low_max and Cell[SOUTH].low_count to 0
    endif
    if Cell[SOUTH].low_max <> 0 OR (Cell[SOUTH].low_max == 0 AND NOT Cell[SOUTH].lit)
        Cell[CELL].low_count = Cell[CELL].low_max + 10
    endif
endif

// STEP 6 - light up if we've reached lower max (ie come out of shadow)
if Cell[CELL] has 'reached' lower maximum
    Cell[CELL].lit = true
endif

// STEP 7 - apply 'lit' value
This step in the algorithm is entirely dependent on how the map grid
is implemented.   The basic criterion for a lit square is as follows:
if Cell[CELL].lit OR (BLOCKER AND Cell[CELL].vislble)
    // do something appropriate
else
    // the cell is not visible by the player.
endif

end for : CELL
end for : COLUMN
end function : los_octant
```

## Possible Refinements

---

If an entire column is dark (ie Cell[CELL].lit is FALSE for each cell in the column), there is no need to continue calculations - every subsequent column will be dark and NOT visible.

It may be possible to reconstruct certain parts of this algorithm so that needless computation is avoided. This is left as an exercise in order to make the basic algorithm as readable as possible.

At no point does the algorithm assume sequential evaluation of expression terms. It may be possible to rewrite certain tests to take advantage of this compiler feature.

The above algorithm processes a square area, but can be trivially modified to process any other area possessing eightfold symmetry.

## Octant Translation

---

Any cell with coordinates (X,Y) may be translated to another octant with the following transformation (given in C-like code):

```c
int xxcomp[8] = { 1, 0, 0, -1, -1, 0, 0, 1 }
int xycomp[8] = { 0, 1, -1, 0, 0, -1, 1, 0 }
int yxcomp[8] = { 0, 1, 1, 0, 0, -1, -1, 0 }
int yycomp[8] = { 1, 0, 0, 1, -1, 0, 0, -1 }

tx = X * xxcomp[o] + Y * xycomp[o]
ty = X * yxcomp[o] + Y * yycomp[o]
```

Where o is the octant number, ranging from 0 to 7.

## The Author

---

I (Gordon Lipford) am a happily employed father of 2 boisterous boys who enjoys designing and coding games as a hobby. I am drawn to Roguelikes and their abstract representation of a game world - allowing my imagination free reign to visualize the environment.

Please send all correspondence to <mailto:gclipford@sympatico.ca>. If that fails, you may try my work address: <mailto:lipford@ca.ibm.com>.
