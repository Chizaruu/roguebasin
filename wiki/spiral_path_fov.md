# Spiral Path FOV

---

[Original](http://roguebasin.roguelikedevelopment.org/index.php?oldid=4027) by Ray Dillinger

Okay; Basically it uses a table of squares, and a queue of pointers to the table elements. In the table, it keeps track of the minimum and maximum lit angle reaching the corresponding square, plus static precomputed information such as the angles from the origin to the square corners.

There are two things that can happen to a square; you can either pass light to it, or you can pass light from it. When you're passing light to it, you record (or expand) the minimum and maximum lit angles. When you're passing light from it, you mark the square as lit/seen, take the minimum and maximum angle of the light that's reaching it, and if it's transparent, pass light to the squares that are adjacent to it and further from the center.

The tricky bit with spiral-path is using the queue of pointers correctly, so no pointer to a square that's not lit/seen is ever put there, while at the same time putting the squares into the queue an the order that guarantees that all light will be passed to each before any light is passed from it. Each square can get light passed to it once, or twice; the first time light is passed to it, a pointer to it gets added to the processing queue. The second time light gets added to it, if there is a second time, its minimum and maximum lit angles are adjusted.

It has to be a queue of pointers (or the moral equivalent) because you have to be able to get at the squares two ways: First you have to be able to find it in constant time by its coordinates when you're adding light to it. Second, you've got to be able to get at it in constant time when you're taking it off the front of the queue. So basically, you have your choice whether to implement the queue as a queue of coordinates into the table or as a queue of literal pointers.

Spiral-path consists in loading the queue with the initial four squares, (one space east, north, west, and south of the center, in that order), marking each with the minimum and maximum angle of light reaching it (nominally equal to its minimum and maximum angles), then

```text
   repeat:
   take one square off the front of the queue.*1
   mark it visible/lit.
   If it's within the sight radius and not opaque then
      pass light from it
          (this may add things to the end of the queue,
      or add light to things that are already in the queue)
   : until the queue is empty.
```

Efficiency note:

\*1 There can never be more elements in the queue than twice the sight diameter, so you can use a static array of fixed size, plus head and tail indexes that get incremented modulo its length, for the queue. You halt processing when head is equal to tail.

The reason it's called spiral-path is that when you're passing light from a square, it MUST be passed to new squares in the correct order, or else you will wind up passing light from something before all its light has been passed to it. And the correct order, where light/vision is unobstructed, is a spiral.

The path in which squares actually get added to the queue (where unobstructed) is: (digits, then letters, beginning from \*)

```text
      I
     J8H
    K927G
   LA3*16F
    MB45E
     NCD
      0
```

etc... spiraling out from the center. Squares 1,2,3,4 get added in initialization. Squares 5,6,7 get added when passing light from square 1. Passing light from square 2 adds light to square 7 which is already on the queue, then adds squares 8 and 9 to the queue. Passing light from square 3 adds light to square 9 which is already on the queue, then adds squares A and B to the queue. Passing light from square 4 adds light to B, then adds square C to the queue, then adds light to square 5 which is already on the queue. Passing light from square 5 adds D and E to the queue. Passing light from square 6 adds light to E which is already on the queue, then adds F and G to the queue. And so on.

The tricky bit was making sure things got added in the right order so that all the light that 5 was going to get, got to it before light was passed from it. With the added condition that you don't know in advance which squares light will actually come through to a given square, and that cone effects or directional light can start on any angle, not just with square 1, that turns out to depend on the order of additions being a spiral.

So... when passing light from square S, you figure out from the minimum and maximum lit angles of S which of its neighbors it will pass light to, and then add whichever of those neighbor squares you need to add in this order:

If S is on the east axis:

```text
   3
   S2
   1
```

If S is in the northeast quadrant:

```text
    2
    S1
```

If S is on the North axis:

```text
    2
   3S1
```

If S is in the northwest quadrant:

```text
    1
   2S
```

If S is on the West axis:

```text
    1
   2S
    3
```

If S is in the southwest quadrant:

```text
   1S
    2
```

If S is on the South Axis:

```text
   1S3
    2
```

If S is in the southeast quadrant:

```text
    S2
    1
```

That way, no matter which squares get skipped (because no light is getting to them) the squares that get added will get added in such an order that none of them ever passes light to a square that light has already been passed from, and no square that light has been passed from will ever be re-added to the queue.

There is one more fiddly bit: Along one of your axes, you'll have the "zero line" of your angle measurement. So the code to pass light to and from squares on that axis has to take into account the wraparound in angles.

There's no need for floating-point in it; you're keeping track of angles, but all you have to do is look them up in tables and compare them to each other;It's simple enough to multiply the angles by a million and round them off to integers.

Finally, there's the corner patchup: If you want spiral- path to fill in the corners of your rooms so you get

```text
########              #######
.......#              .......#
.......#  instead of  .......#,
```

add that an opaque square whose maximum-angle corner is lit, passes an infinitely narrow beam of light through that corner to the _last_ square it would otherwise pass light to.

Here is a complete implementation:

```c
/* The following is a complete implementation of Spiral Path FOV code in C.
     Spiral Path visits each square with light passing through it or into it
     exactly once, and does not visit any other squares -- so it's in the most
     efficient class of FOV algorithms.  I have not measured its performance
     against comparable implementations of other algorithms in the class; it
     may be a constant factor faster or slower.

     It calls out to three functions in a different module; These functions are
     Map_Distance to calculate distances,
     Map_Transparent to tell whether light can pass through a square, and
     Map_FovCallback to actually do the marking of squares in the map.

     It assumes that two of its own functions (and only two) may be called from
     outside the module; these functions are
     FOV_Init, which is called exactly once to initialize the data structures, and
     FOV_SpiralPath, which is called whenever we want to do radius or arc lighting,
     whenever we want to calculate a field of view, whenever we want to mark
     squares to be subject to a radius or cone effect that can be blocked, etc.

     Module written by Ray Dillinger in 2011.  This is an adaptation/rewrite of an
     original implementation, also by Ray Dillinger, from 2004 which was harder
     to understand.   Use and enjoy, subject to any Debian-Approved
     free-software license of your choice.
*/


#include <math.h>
#include <assert.h>

#include "fov.h"
#include "map.h"

/* a data type that stores the minimum and maximum lit angles of a
   given tile. */
typedef struct{
  double minlit;
  double maxlit;
} FOVsquare;

/* FOV_grid stores the angle from the center of the origin square or
   (0.5,0.5) to the corner of the tile at the given coordinates (to
   the true (0,0) corner in the case of the origin square. From these
   we derive all corner coordinates we need for all tiles.  This has
   the benefit of avoiding repeating potentially-slow trig operations,
   but it's mainly for consistency.

   Since we're using a lot of doubles, and we need particular angles
   that we get to in several different ways, deriving all angles in an
   absolutely consistent way from this grid ensures that equality
   tests are meaningful and do what we want, despite possible roundoff
   errors and peculiarities of implementation.

   In principle this could use Integers instead of doubles, given a
   monotonic mapping of integers to angles occurring in the grid.  */

double FOV_grid[FOV_SIGHTDIAMETER+1 ][FOV_SIGHTDIAMETER+1 ];

/* This array stores the minimum and maximum lit angles of each square
   within the sight radius.  Unlit squares are represented as 0,0.
   each FOV operation must begin and end with minlit and maxlit angles
   in FOV_lightgrid set equal to zero. These angles change during the
   operation; if either (or both) are nonzero, then the corresponding
   element is in the queue. Angles related to each (xy) square are
   stored in FOV_grid(x-sightradius,y-sightradius) because C doesn't
   allow non-zerobased arrays. */

FOVsquare FOV_lightgrid[FOV_SIGHTDIAMETER][FOV_SIGHTDIAMETER];


/* this function is a 'shim' that handles the map referring to
   loctypes and the SpiralPath code referring to integer coordinates.
   We have loctypes available because we've included map.h above,
   but this FOV code doesn't use them outside of shim functions. */
int FOV_Transparent(maptype map, int x, int y, int z){
  loctype testloc;
  testloc.x = x;
  testloc.y = y;
  testloc.z = z;
  return(Loc_Transparent(map, testloc));
}

/* this function is a 'shim' that handles the map code referring to
   loctypes and the FOV code referring to integer coordinates.  I'm
   referring back to the map distance function in order to assure that
   the "distance" expressed by radius, etc, is the same distance as
   measured by the Map module's distance function. */
double FOV_Distance(int x, int y, int x2, int y2){
  loctype loc1;
  loctype loc2;

  loc1.x = x;
  loc2.x = x2;
  loc1.y = y;
  loc2.y = y2;
  /* For FOV purposes we're finding distances on the same z plane -
     hence the z coordinate (as long as it's > 0) does not actually
     matter. */
  loc1.z = loc2.z = 1;
  return(Map_Distance(loc1, loc2));
}


/* normalizes angles to between zero and 2pi. */
double FOV_Anglenorm(double angle){
  while (angle < 0.0) angle += 2*PI;
  while (angle > 2*PI) angle -= 2*PI;
  return(angle);
}

/* Initializes the light grid.  Called from FOV_Init.  After that we
   are meticulous in re-zeroing these whenever we dequeue them so the
   grid will always be ready when one Spiralpath traversal is done to
   begin another. */
void FOV_InitLightGrid() {
  int x; int y;
  for (x = 0; x < FOV_SIGHTDIAMETER; x++)
    for (y = 0; y < FOV_SIGHTDIAMETER; y++){
      FOV_lightgrid[x][y].minlit = 0.0;
      FOV_lightgrid[x][y].maxlit = 0.0;
    }
}

/* Initializes the geometry grid.  Called from FOV_Init.
   The geometry grid is never altered after initialization. */
void FOV_InitGrid(){
  int x;
  int y;
  for (x = 0; x <= FOV_SIGHTDIAMETER; x++)
    for (y = 0; y <= FOV_SIGHTDIAMETER; y++){
      /* this is the angle from the center of the 0,0 square to the
      closest-to-origin corner of each square.  All angles except
      the angles to the squares in the zero row/column will be in
      the range 0 to pi/2. */
      FOV_grid[x][y] = FOV_Anglenorm(atan2((y-0.5),(x-0.5)));
    }
}

/* Needs called once during game setup.  Handling it by calling
   it from Map_Init. */
void FOV_Init(){
  FOV_InitGrid();
  FOV_InitLightGrid();
}


/*returns the angle that "oughta" be in the geometry grid for given
  coordinates, if the grid went to those coordinates.*/
double FOV_Coordangle(int x, int y){

  if (x > FOV_SIGHTRADIUS || y > FOV_SIGHTRADIUS ||
      -x > FOV_SIGHTRADIUS || -y > FOV_SIGHTRADIUS )
    /* if it's outside of the precomputed zone and its rotations about
       the origin, then calculate it directly. */
    return(FOV_Anglenorm(atan2(y-0.5, x-0.5)));
  /* otherwise get it from the correct zone rotation & offset. */
  if (x >= 0 && y >= 0) return (FOV_grid[x][y]);
  if (x < 0 && y > 0) return (PI - FOV_grid[1-x][y]);
  if (x >= 0 && y < 0) return (2*PI - FOV_grid[x][1-y]);
  return(PI + FOV_grid[1-x][1-y]);
}


/* The minimum angle of the tile; that is, the angle of the
   smallest - angled corner.*/
double FOV_MinAngle(int x, int y){
  if (x == 0 && y == 0) return (0.0); /* origin special case */
  if (x >= 0 && y > 0) return (FOV_Coordangle(x+1,y)); /* first quadrant */
  if (x < 0 && y >= 0) return (FOV_Coordangle(x+1,y+1)); /* second quadrant*/
  if (x <= 0 && y < 0) return (FOV_Coordangle(x,y+1)); /* third quadrant */
  return(FOV_Coordangle(x,y)); /* fourth quadrant */
}


/* The maximum angle of the tile; that is, the angle of the
   largest-angled corner.*/
double FOV_MaxAngle(int x, int y){
  if (x == 0 && y == 0) return (2*PI);  /* origin special case */
  if (x > 0 && y >= 0) return (FOV_Coordangle(x,y+1)); /* first quadrant */
  if (x <= 0 && y > 0) return (FOV_Coordangle(x,y)); /* second quadrant */
  if (x < 0 && y <= 0) return (FOV_Coordangle(x+1,y));/* third quadrant */
  return(FOV_Coordangle(x+1,y+1)); /* fourth quadrant */
}

/* The angle of the outer corner of each tile: On the origin lines,
   the angle of the FIRST outer corner. */
double FOV_OuterAngle(int x, int y){
  if (x == 0 && y == 0) return (0.0); /* origin special case */
  if (x >= 0 && y > 0) return (FOV_Coordangle(x+1,y+1)); /* first quadrant with positive y axis*/
  if (x < 0 && y >= 0) return (FOV_Coordangle(x,y+1)); /* second quadrant with negative x axis*/
  if (x <= 0 && y < 0) return (FOV_Coordangle(x,y)); /* third quadrant with negative y axis */
  return(FOV_Coordangle(x+1,y)); /* fourth quadrant with positive x axis */
}

/* The squares on the axes (x or y == 0) have a second outer corner.
   This function identifies the angle from the center of the origin
   square to that corner.  */
double FOV_OuterAngle2(int x, int y){
  if (x != 0 && y != 0) return (0.0); /* meaningless on non-axis squares */
  if (x > 0) return(FOV_Coordangle(x+1,y+1));
  if (x < 0) return(FOV_Coordangle(x,y));
  if (y > 0) return(FOV_Coordangle(x, y+1));
  if (y < 0) return(FOV_Coordangle(x+1,y));
  return(0.0); /* meaningless on origin */
}

/* true iff a given square could pass any light inside a desired arc. */
int FOV_In_Arc(int x, int y, double arcstart, double arcend){
  if (arcstart > arcend)    /* arc includes anomaly line */
    return (FOV_MinAngle(x,y) < arcstart ||
     FOV_MaxAngle(x,y) < arcstart ||
     FOV_MinAngle(x,y) > arcend ||
     FOV_MaxAngle(x,y) > arcend);
  else     /* arc does not include anomaly line */
    return (FOV_MaxAngle(x,y) > arcstart ||
     FOV_MinAngle(x,y) < arcend);
}


/* find the first "child" tile to which light may be passed from x y.
   This is also the tile to be revealed as a corner touchup if x y is
   illuminated from its minimum-angle corner and is opaque. */
void FOV_FirstChild(int x, int y, int *childx, int *childy){
  if (x == 0 && y == 0) {*childx = x; *childy = y; return; } /* origin */
  if (x >= 0 && y > 0) {*childx = x+1; *childy = y; return; } /* quadrant 1 */
  if (x < 0 && y >= 0) {*childx = x; *childy = y+1; return; } /* quadrant 2 */
  if (x <= 0 && y < 0) {*childx = x-1; *childy = y; return; } /* quadrant 3 */
  *childx = x; *childy = y-1; return; /* quadrant 4 */
}

/* find the second "child" tile to which light may be passed from x y. */
void FOV_SecondChild(int x, int y, int *childx, int *childy){
  if (x == 0 && y == 0) {*childx = x; *childy = y; return; } /* origin */
  if (x >= 0 && y > 0) {*childx = x; *childy = y+1; return; } /* quadrant 1 */
  if (x < 0 && y >= 0) {*childx = x-1; *childy = y; return; } /* quadrant 2 */
  if (x <= 0 && y < 0) {*childx = x; *childy = y-1; return; } /* quadrant 3 */
  *childx = x+1; *childy = y; return; /* quadrant 4 */
}

/* find the third "child" tile to which light may be passed from x y.
   Meaningful only for axis tiles (x == 0 or y == 0) because they
   have a third child; non-axis tiles don't. */
void FOV_ThirdChild(int x, int y, int *childx, int *childy){
  if (x != 0 && y != 0) {*childx = *childy = 0; return;} /* non-axis*/
  if (x > 0) {*childx = x; *childy = y+1; return;}
  if (x < 0) {*childx = x; *childy = y-1; return;}
  if (y > 0) {*childx = x-1; *childy = y; return;}
  if (y < 0) {*childx = x+1; *childy = y; return;}
  *childx = 0;   *childy = 0;   return; /* origin */
}

void FOV_SetLitAngle(int x, int y, double minlit, double maxlit){
  /* boundary checking just to make sure.... I know of no way this
     code can ever violate these boundary checks, but the code that
     implements relevant preconditions is non-centralized and if
     they're violated we segfault, so leaving the checks in to catch
     any possible mistakes present or future. */

  assert(x+FOV_SIGHTRADIUS >= 0);
  assert(y+FOV_SIGHTRADIUS >= 0);
  assert(x+FOV_SIGHTRADIUS < FOV_SIGHTDIAMETER);
  assert(y+FOV_SIGHTRADIUS < FOV_SIGHTDIAMETER);

  FOV_lightgrid[x+FOV_SIGHTRADIUS][y+FOV_SIGHTRADIUS].minlit = minlit;
  FOV_lightgrid[x+FOV_SIGHTRADIUS][y+FOV_SIGHTRADIUS].maxlit = maxlit;
}

void FOV_GetLitAngle(int x, int y, double *minlit, double *maxlit){
  /* Again, boundary checking just to make sure.... */
  assert(x+FOV_SIGHTRADIUS >= 0);
  assert(y+FOV_SIGHTRADIUS >= 0);
  assert(x+FOV_SIGHTRADIUS < FOV_SIGHTDIAMETER);
  assert(y+FOV_SIGHTRADIUS < FOV_SIGHTDIAMETER);

  *minlit = FOV_lightgrid[x+FOV_SIGHTRADIUS][y+FOV_SIGHTRADIUS].minlit;
  *maxlit = FOV_lightgrid[x+FOV_SIGHTRADIUS][y+FOV_SIGHTRADIUS].maxlit;
}


/* we dequeue one item and set all the particulars.  Also, we set the
   squarelighting to zero for that tile so we know it's off the queue
   next time we come across it. */

void FOV_Dequeue(int *QX, int *QY, int *Qhead, int *curx, int *cury, int *child1X,
   int *child1Y, int *child2X, int *child2Y, int *child3X, int *child3Y,
   double *leastangle, double *outerangle, double *outerangle2,
   double *greatestangle, double *leastlitangle, double *greatestlitangle){
  *curx = QX[*Qhead];
  *cury = QY[*Qhead];
  *Qhead = (*Qhead + 1) % (2 * FOV_SIGHTDIAMETER);

  FOV_FirstChild(*curx, *cury, child1X, child1Y);
  FOV_SecondChild(*curx, *cury, child2X, child2Y);
  FOV_ThirdChild(*curx, *cury, child3X, child3Y);
  *leastangle = FOV_MinAngle(*curx, *cury);
  *outerangle = FOV_OuterAngle(*curx, *cury);
  *outerangle2 = FOV_OuterAngle2(*curx, *cury);
  *greatestangle = FOV_MaxAngle(*curx, *cury);
  FOV_GetLitAngle(*curx, *cury, leastlitangle, greatestlitangle);

  /* and this is off the queue now so we zero it for next time. */
  FOV_SetLitAngle(*curx, *cury, 0.0, 0.0);
}

void FOV_enqueue(int *QX, int *QY, int *Qtail, int x, int y){
    QX[*Qtail] = x;
    QY[*Qtail] = y;
    *Qtail = (*Qtail + 1) % (2 * FOV_SIGHTDIAMETER);
}

/* This adds light to a tile. Also, if a tile is not in the queue,
   it enqueues it.*/
void FOV_Mark(int *QX, int *QY, int *Qtail, int x, int y, double min, double max){
  double minlit, maxlit;
  FOV_GetLitAngle(x,y, &minlit, &maxlit);
  if (minlit == maxlit && maxlit == 0.0){
    /* no light -- implies not in queue, so we add it to the queue.  */
    FOV_SetLitAngle(x,y, min, max);
    FOV_enqueue(QX, QY, Qtail, x,y);
  }
  else {
    if (min < minlit) minlit = min;
    if (max > maxlit) maxlit = max;
    FOV_SetLitAngle(x,y,minlit,maxlit);
  }
}




/* The total lit angle is represented by leastlitangle,
   greatestlitangle.  minangle and maxangle are the minimum and
   maximum that can be illuminated in this operation.  Our task is to
   test and see if we can add light to the tile.  If any light is
   added, we call FOV_Mark. */
void FOV_TestMark(int *QX, int *QY, int *Qtail, int x, int y, double leastlitangle, double greatestlitangle,
    double minangle, double maxangle){
  if (leastlitangle > greatestlitangle)
    /* we're passing light along the anomaly axis. This takes
       advantage of the grid-geometric property that no
       less-than-total obstructions are possible. */
    FOV_Mark(QX, QY, Qtail, x, y, minangle, maxangle);
  else if (maxangle < leastlitangle || minangle > greatestlitangle)
    /* lightable area is outside the lighting. */
    return;
  else if (minangle <= leastlitangle && greatestlitangle <= maxangle)
    /* lightable area contains the lighting. */
    FOV_Mark(QX, QY, Qtail, x,y, leastlitangle, greatestlitangle);
  else if (minangle >= leastlitangle && greatestlitangle >= maxangle)
    /* lightable area contained by the lighting.  */
    FOV_Mark(QX, QY, Qtail, x,y, minangle, maxangle);
  else if (minangle >= leastlitangle && greatestlitangle <= maxangle)
    /* least of lightable area overlaps greatest of lighting. */
    FOV_Mark(QX, QY, Qtail, x,y, minangle, greatestlitangle);
  else if (minangle <= leastlitangle && greatestlitangle >= maxangle)
    /* greatest of lightable area overlaps least of lighting.  */
    FOV_Mark(QX, QY, Qtail, x,y, leastlitangle, maxangle);
  else /* This never happens. */
    assert(1 == 0); /* unhandled case, not on the anomaly line. */
}


/* Mapx, mapy, mapz, are the coordinates of the square where
   spiralpath starts. Radius is the range of the effect.  Radius must be less
   than FOV_SIGHTRADIUS.  Arcstart and arcend denote the minimum and
   maximum angles to mark.  They must be between 0.0 and 2*PI.  Action
   is a signal to Map_FovCallback what action to do to squares in the area.
   Its value doesn't matter to us, we just pass it back to the map
   module. */

void FOV_SpiralPath(maptype map, int mapx, int mapy, int mapz, double radius, double arcstart,
     double arcend, int action, int cornertouchup){

  /* The next 4 variables are a Queue of X Y coordinates. */
  int QX[2 * FOV_SIGHTDIAMETER];
  int QY[2 * FOV_SIGHTDIAMETER];
  int Qhead = 0;
  int Qtail = 0;

  int curx, cury,  child1X, child1Y,  child2X, child2Y, child3X, child3Y;

  double leastangle, greatestangle, outerangle, outerangle2, leastlitangle, greatestlitangle;

  /* the point of origin is always marked by the traverse. */
  Map_FovCallback(map, mapx, mapy, mapz, mapx, mapy, 0, 2*PI, 0, 2*PI, action);
  /* these 4 squares (in this order) are a valid "starting set" for Spiralpath traversal.
     A valid starting set is either a clockwise or counterclockwise traversal of all
     the points with manhattan distance 1 from the origin.   */
  FOV_TestMark(QX, QY, &Qtail, 1, 0, arcstart, arcend, FOV_MinAngle(1,0), FOV_MaxAngle(1,0));
  FOV_TestMark(QX, QY, &Qtail, 0, 1, arcstart, arcend, FOV_MinAngle(0,1), FOV_MaxAngle(0,1));
  FOV_TestMark(QX, QY, &Qtail, -1,0, arcstart, arcend, FOV_MinAngle(-1,0), FOV_MaxAngle(-1,0));
  FOV_TestMark(QX, QY, &Qtail, 0,-1, arcstart, arcend, FOV_MinAngle(0,-1), FOV_MaxAngle(0,-1));

  while(Qhead != Qtail){
    FOV_Dequeue(QX, QY, &Qhead, &curx, &cury, &child1X, &child1Y, &child2X, &child2Y, &child3X,
  &child3Y, &leastangle, &outerangle, &outerangle2, &greatestangle,
  &leastlitangle, &greatestlitangle);

    if (FOV_Distance(0,0,curx,cury) <= radius && FOV_In_Arc(curx, cury, arcstart, arcend)){
      Map_FovCallback(map, mapx, mapy, mapz, mapx+curx, mapy+cury,
        leastangle, greatestangle, leastlitangle, greatestlitangle, action);
      if (FOV_Transparent(map, mapx+curx,mapy+cury, mapz)){
 FOV_TestMark(QX, QY, &Qtail, child1X, child1Y, leastlitangle, greatestlitangle, leastangle, outerangle);
 if (outerangle2 != 0.0){
   FOV_TestMark(QX, QY, &Qtail, child2X, child2Y, leastlitangle, greatestlitangle, outerangle, outerangle2);
   FOV_TestMark(QX, QY, &Qtail, child3X, child3Y, leastlitangle, greatestlitangle, outerangle2, greatestangle);
 }
 else
   FOV_TestMark(QX, QY, &Qtail, child2X, child2Y, leastlitangle, greatestlitangle, outerangle, greatestangle);
      }
      else /* cell is opaque.  We pass an infinitely-narrow ray of
       light from its first corner to its first child if we
       are doing corner touchup. */
 if (cornertouchup && (leastlitangle == leastangle))
   FOV_Mark(QX, QY, &Qtail, child1X, child1Y, leastangle, leastangle);
    }
  }
}
```
