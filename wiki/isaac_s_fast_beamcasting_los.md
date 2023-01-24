# Isaac s fast beamcasting LOS

---

See also Isaac Kuo's [java applet demo](http://www.geocities.com/mechdan/) of this algorithm.

```text
Isaac's fast beamcasting LOS - Isaac Kuo [mechdan@yahoo.com]

Here I present pseudocode for a fast LOS calculator.

This pseudocode calculates visibility in the first quadrant
by casting beams at 32 different slopes.  Despite the low
number of casting angles, the fact that wide beams are
thrown instead of thin lines ensures adequate coverage.

Conceptually, the beam's source is the diagonal from
(-.5,.5) to (.5,-.5).  Each step of throwing the beam
increments its position from the current diagonal line
to the next.  It is sufficient to check against collisions
with squares along the diagonals because that is where
their greatest extents will always be present.

This pseudocode uses a skewed coordinate system to assist
the calculations.  The transformed coordinate system is:

x=u-v/32  u=x+y
y=v/32    v=y*32

...initialize trans[x][y] to be the transparency map...
...initialize visible[x][y] to false...

// Build a circular border of opaque blocks so later
// there won\'t be any need for extra code to check
// against maximum range.
//
// Note that there are obvious optimizations to make
// this step MUCH faster.
for(x=0;x<=MAXRANGE;x++)
  for(y=0;y<=MAXRANGE;y++)
    if (distance(x,y)>=MAXRANGE) trans[x][y]=false;

// Set 0,0 to be visible even if the player is
// standing on something opaque
visible[0][0]=true;

// Check the orthogonal directions
for(x=1; trans[x][0]; x++) visible[x][0]=true;
for(y=1; trans[0][y]; y++) visible[0][y]=true;

// Now loop on the diagonal directions
for(slope=1; slope<=31; slope++) {

  // initialize the v coordinate and set the beam size
  // to maximum--mini and maxi store the beam's current
  // top and bottom positions.
  // As long as mini<maxi, the beam has some width.
  // When mini=maxi, the beam is a thin line.
  // When mini>maxi, the beam has been blocked.

  v=slope; mini=0; maxi=31;
  for(u=1; mini<=maxi; u++) { //loop on the u coordinate
    y=v>>5; x=u-y;  //Do the transform
    cor=32 - (v&31);  //calculate the position of block corner within beam

    if(mini<cor) {        //beam is low enough to hit (x,y) block
      visible[x][y]=true;
      if(!trans[x][y]) mini=cor;     //beam was partially blocked
    }
    if(maxi<cor) {        //beam is high enough to hit (x-1,y+1) block
      visible[x-1][y+1]=true;
      if(!trans[x-1][y+1]) maxi=cor;   //beam was partially blocked
    }
    v+=slope;  //increment the beam's v coordinate
  }
}


The general picture of what's happening is:
       |         |
       |         |
   \32 |         |
    \  |         |
     \maxi       |
      \|         |
 ------\cor------+----
       |\        |
       | \       |
       |  \      |
       |   \mini |
       |    \    |  Y
       |     \0  |  ^
       |         |  |
       |         |  |
       |X,Y BLOCK|  +-->X
 ------+---------+

The position of the beam is between the points designated "mini"
and "maxi".  The parts below "mini" and above "maxi" have already
been obscured by shadow.  If "cor" is between them, then this
beam hits two blocks (the one at X,Y and the one at X-1,Y+1).
Otherwise, only one of those blocks will be hit.

This algorithm should be pretty fast as it is--certainly faster
than the typical raycasting algorithm because this one throws
essentially a bundle of rays at a time for roughly the same
computational cost of two of the traditional rays.

It's also more elegant than traditional raycasting.  No additional
hacks are required to fill in wall gaps or beautify corners.

If an exact LOS path is required (i.e. for displaying ranged
weapon animation and/or determining the area of effect of a
linear attack spell), then a beam which hits the target can
be narrowed down the ray at either (mini+cor)/2 or (maxi+cor)/2,
depending on whether the target was in the (X,Y) or the
(X-1,Y+1) block.  This ray's path will look something like:

@######
***....
..***..
....**T

This can lead to near diagonal shots intersecting almost twice
as many blocks as you'd expect.  As such, I would only consider
the blocks where the ray segment covers at least 1/2 in either
the X or Y dimension.  This will reduce the ray's path to
something like:

@######
.**....
...**..
.....*T
--
    _____     Isaac Kuo  mechdan@yahoo.com
 __|_>o<_|__
/___________\ The most important way to defend one's nation
\=\>-----</=/ is to make it worth defending.  - a true patriot
```

Variant algorithm:

```text
Just to let you know--I'm pondering a new approach
to my beamcasting LOS concept which visits each
block exactly once.  My original algorithm visits
blocks multiple times, which is efficient with my
assumed data structures--a simple fast arrays for
visibility and opaqueness.  However, a lot of people
like to use a more complex or object oriented data
structure, where each access involves a lot of
overhead.

My basic idea is to cast all slopes in a quadrant
at once, keeping a small fast array of mini[slope]
and maxi[slope].

In the original algorithm, the slope is the outer
loop, and the radius is the inner loop.  In my
new algorithm, the radius is the outer loop while
the slope is the inner loop.  Roughly:


//initialize all mini and maxi
mini[1...slopes-1] = 1
maxi[1...slopes-1] = slopes-1

for u = 1 to maxradius  //loop on radius

 //initialize at slope=0

 x = u+ox
 y = 0+oy
 cor = 0
 opaqueA = opaque(x,y)
 opaqueB = opaque(x-1,y+1)

 for slope = 1 to slopes-1  //loop on slope

   cor += u         //increment cor
   if cor>=slopes   //check for overflow into next block
     cor -= slopes
     x--
     y++
     opaqueA = opaqueB
     opaqueB = opaque(x-1,y+1)

   //Now do the visibility stuff
   if maxi[slope]>cor
     visible[x,y] = true
     if opaqueA
       maxi[slope]=cor

   if mini[slope]<cor
     visible[x-1,y+1] = true
     if opaqueB
       mini[slope]=cor

 next slope

next u

Note that I've swapped the roles of "mini" and "maxi",
and replaced "cor" with num_slopes-cor.

The above is a rough idea, but it has some problems.
For one thing, it always visits every block within
the maximum radius, even if there are nearby blocking
walls.  This can be a major performance hit if much
of the terrain is made up of twisting narrow
corridors.

Isaac Kuo
```
