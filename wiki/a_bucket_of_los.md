# A Bucket Of LOS

---

```text
 A Bucket Of LOS
 By Pieter Droogendijk
 gin@binky.homeunix.org

This document describes an effective Line-Of-Sight algorithm. The description
goes so far as to separate FOV (Field Of Vision) and a Light Radius (or many),
Although naturally not everything described in this document has to be adhered.
Multiple light sources, and separate fields of vision may be implemented.
This algorithm depends heavily on precalculation, therefore it's memory usage
may be higher than the average LOS algorithm. It only precalculates for one
octant, and for a specified radius, translations made in the 'active' code. the
octant NWW is considered to be octant 0, the calculated octant. NNW is octant 1,
NNE octant 2, continuing in a clockwise manner up to octant 7.

The passive calculations adhere a base radius. In the 'active' code, it's
advisable to add another radius check to make sure you only 'see' the required
radius. This way you have to precalculate only once for all radii.
The base radius should be altered to match the game's specifications, for
instance, a lower-radius precalculation will limit the maximum field of view
radius in the game, but a base radius that is too high may reduce performance.
You could even go so far as to determine the highest radius that will be used on
the current map, and recalculate every time you enter a new level. The
possibilities are endless.

Let's go describe the algorithm, shall we?

STEP 1: The Squares
Before we can tackle the algorithm, we need a solid bunch of squares to
represent one octant. These squares will be referred to by the rays thrown in
step 2, and for step 4 we need the lines to share one pool of squares.
  #
  ##         octant 0
  ###
  ####
  ####@      @ is the origin


STEP 2: Ray Tracing
The first thing of note in the algorithm is ray tracing. We trace a line from
the center of the originating square, to the nearest corner of every square in
the octant (using the Pythagorean theorem to make a circular FOV).
Angle-like values are remembered, and duplicate rays are traced only once. Rays
don't stop after reaching their target, but continue until hitting the edge of
the circular FOV.
The trace stores pointers to all squares it touches in the Line structure. The
length of the pointer array is increased as the trace progresses. Touched
squares are given their correct coordinates within the octant.
  .             . is the target (also stored in the ray)
   ###
     ###
       #@


STEP 3: Sorting The Lines
Now that we have a collection of squares, a collection of lines, and each line
being a collection of pointers to squares, we sort the lines, in descending
order. The most diagonal line now comes first, and the most horizontal line now
comes last.
  #         <-- first in line
   #
    #
     #
  #####     <-- last in line


STEP 4: Last Square Hit
We now go through every line again. And every square of every line. And for
every square, we store the index of the line it belongs to in the square's
'last' element.
When this is done, and we remind ourselves that we've just sorted the lines in
descending order, we'll see that every square now contains the index of the last
line touching it.
  11####
  # 11 #    here, line 2 is the last line.
  22  11
  # 222#11
  #    22211
  ######  2211


MILESTONE: Passive calculations done
Now we've got our View struct up and we're ready to go to phase two of the
cunning plan. From here on, we'll be interfacing the View with the Map, so we
can remember important things like 'is this square lit', 'can i see this square'
and 'have i seen that square in the past'.
Know that there are two different active FOV functions. the one,
updatelitmatrix(), uses the View to light up squares as it goes. This function
is called many times, depending on how many light sources there are in the game.
The other, updatevismatrix(), uses the View to determine whether or not the
player can really see the squares within his FOV or not. This should be called
once every cycle, and probably encompasses a much greater radius.


STEP 5: Let There Be Light
A smart thing to do, before determining whether or not you can see a square, is
to set certain squares as being lit by a light source. We use updatelitmatrix()
for that.
For every octant, all lines are traced once. And for every line, every Square is
used. Per square, we transform it's relative octant coordinates to absolute map
coordinates. We then set every map square we get this way as being 'lit'.
However, we want to distinguish different light sources, especially if a square
is shone upon from more then one source. ESPECIALLY if that square happens to be
a wall. For reasons explained in step 6, we define a static variable, holding
the default value of one. Every time the updatelitmatrix() function is called,
the value of this variable is multiplied by two, and every time
updatevismatrix() is called, or MAX_VALUE is reached, we set it back to 1. The
value of this variable is then ORed to the 'lit' value of the current square.
Don't look so surprised, it'll be clearer in a few moments.
Once a map square is found that is not transparent (i.e. it's a 'ray-blocking
square'), the trace is broken off, and the next line processed is set to be the
line with an index one past the index set as 'last' on the current square.


STEP 6: I Can See!
The next, (and final) step is to mark squares within the view radius that are
marked as 'lit' in the previous step as being 'visible'. You'd say the process
is the same as the previous one, but that's not entirely true.
It's true, again we select all squares of all lines on all octants. And again we
break the trace if we find a non-transparent square. But it's the behaviour at
exactly those squares that differs from the step above. How? Consider the next
scenario:
... #..
.@. #&.
... #..
@ can light up one square around him. However, he could SEE way beyond the &,
which is a light source, if there was no wall in between them (denoted by '#').
So what does @ see if we only mark squares as 'lit'?
...
.@.   this would be logical. But this is not what @ sees, because...
...

... #
.@. # @ sees this!!!
... #

Oh shit! The wall squares were lit by the light source behind it... but we
shouldn't be able to see it! That's why, in step 5, we gave different light
sources different identifiers. In the case of our algorithm, the squares lit by
the light source have a different 'lit' value then the squares lit by @. How can
we use this to our advantage?
Should we store in the square the directions it's being shone upon? Might work.
But not in this algorithm, since that would take tracing to all SIDES of the
squares instead of the corners (effectively doubling the amount of lines by the
way). So NO, that's NOT what we want! You forget, we already have a system that
can tell us from which direction a square is being lit. By distinguishing the
different calls to updatelitmatrix(), and knowing that a square visible by us
should share at least ONE light source with the square BEFORE it in the trace!

Once we hit a non-transparent square, we compare the 'lit' value of it with the
'lit' value of the square processed before it in the current Line. Since we
OR-ed many light sources together in updatelitmatrix(), a simple square1&square2
will evaluate &gt0 if the two squares share at least one source. Consider the
following scenario:

visual
        lit values
#####   333110
#g@..   333310
#.g..   333310
 ..g.   033320
  ...   002220
where @ has a light radius of 2 squares, with a 'lit' value of 1, and every 'g'
has a light radius of 1 square, with a 'lit' value of 2. Note, that's just to
make the example easier to interpret and read.
If we would only distinguish between, 'player light' and 'monster light' like in
the above, exotic flaws like the following could appear:
visual:
... ...#..
.@. .g.#g.    logically, we shouldn't see the wall
... ...#..
lit values:
111 222222
111 222222    but we do, because we see squares that share a 'lit' value with
111 222222    the previous square.

And that's why we pick the next 'lit bit' every time we call updatelitmatrix()!


STEP 7: Drawing Is Fun
After all of this, the Map should contain a serious amount of information. It
knows which squares are lit (by everything on the map that emits light), and it
knows which squares are visible by the player. Now, to use this information (at
least the latter).
You could redraw the whole screen every cycle. But that's hardly efficient. A
better idea is to just redraw those parts of the screen that have actually
changed!
...    we move    o..n
.@. -> to the  -> o.@n
...     right     o..n
where o is a square that should be cleared, and n is a square that should now be
drawn.

So what we need is
 a.: which squares can I see now that I couldn't see before
 b.: which squares could I see before that I can't see now
 c.: which squares could I see before, and can still see.
We can get this, no problem.
By modifying updatevismatrix() to, instead of crudely setting some 'vis'
property on a square, behave in a smart way, and combining it with a little wit
from the map drawing function, it's a piece of cake.

When we set a square as 'visible', for it's 'vis' value:
if it's 0, change it to 1
if it's 2, change it to 3

at this point 0 means unseen, 1 means newly seen, 2 means no longer seen and 3
means still seen.

When drawing the Map, for a square's 'vis' value:
if it's 1, change it to 2, set it's 'discovered' value to true, draw the square.
if it's 2, change it to 0, draw the square 'boring' (we can't see it anymore)
if it's 3, change it to 2

drawing a square 'boring' means 'do not show dynamic content'. In other words,
hide monsters, moving light sources, or traps, objects, depending on what you
define as 'active dynamic content' really.
```
