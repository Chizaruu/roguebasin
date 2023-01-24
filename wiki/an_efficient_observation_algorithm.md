# An Efficient Observation Algorithm

---

```text
An Efficient Observation Algorithm - Avier Taylor-Paul [taylor@tcnj.edu].txt

   This article covers the efficiency of observation algorithms,
that is, what is done so a player "see's what's around them."
Walking about in the world is an activity players will do most,
so fast algorithms are of great importance. This is especially
true of 'Netrogue: The Dark Gods' because of performance penalties
induced by the inherent inefficiency of java and propagation
delay of packets over the network. A fair question then would be
"Why bother with the discussion because most developers are making
their roguelikes to run locally in single-player mode anyway", thus
running at native speeds. Well, first it's always good to have a fast
program but more importantly it leaves a great deal of time to do
other things. For example doing an inverse square falloff lighting model.
Let's begin.

   In this case, "optimal algorithm" exactly means that we do
the absolute least amount of work to get the job done; our
routine will be so lazy, it flatly refuse to do any more than
what is minimally required to complete its task. As long as the
remaining jobs are simple in themselves, we get high preformance.

   Now let's make sure we agree on what we mean by 'vision'.
Circular vision is natural for most rogues so that is assumed
here. Imagine that you are standing in the center of a (very
large) sheet of paper shaped in a circle. You can see everything
within its bounds, possibly with the exception of areas blocked
by obstacles sitting on the paper. Then you take one step
to the south. How much work needs to be done to update what you
see? Most certainly you have to notice the new things entering in
your field of vision but you also must 'forget' what's no longer
in range. The situation looks like this:

-------------------7
-------------------                   NW N NE
------.......------5                   \ | /
-----.........-----                  W - . - E
----...........----                    / | \
----...........----                  SW  S SE
----...........----
----.....@.....----0    Player has radius of vision 5.
----...........----        '.' are areas in field of vision.
----...........----        '-' are areas outside of vision.
----...........----
-----.........-----
------.......------5
-------------------
-------------------7
(note the the player has 'perfect' vision here; no obstacles are in range.)

 The player now moved south:

-------------------7
-------------------
------:::::::------6
-----:XXXXXXX:-----5
----:XXXXXXXXX:----
----XXXXXXXXXXX----
----XXXXXXXXXXX----
----XXXXXXXXXXX----
----XXXXX@XXXXX----0    Player has radius of vision 5.
----XXXXXXXXXXX----        ':' are areas no longer in field of vision.
----XXXXXXXXXXX----        'X' are areas we have already seen and
----*XXXXXXXXX*----            have remained in the field of vision.
-----*XXXXXXX*-----        '*' are areas just entering field of vision.
------*******------5
-------------------
-------------------7

If the update routine simply redraws the entire circle of vision
to perform the update, it will recompute 98 positions that remained
exactly the same as they were before. Only 11 new coordinates
have entered the field of vision and 11 have departed. Therefore,
we do 98/11 = ~8.9, almost 900% more work than is necessary. If the
radius of sight had been 9 instead of 5, 244/21 = ~11.6 or almost 1200%
unnecessary work is done. As far as efficient algorithms are concerned,
this is not the very beautiful one. Fortunately, there is a better way
to do this.

The very first thing to do is allocate a two dimensional array,
called the 'sight matrix' that has the meaning 'have I seen this
position before?' Semantically, it is equivalent to the drawings
in the above example. The array is then used so that we never redraw
something that's been seen and remained same, then in a similar manner
is exploited to 'forget' only those things that need to be forgotten.
The array must be large enough to contain the 'circle of sight', so if
the vision radius at maximum can be N, both dimensions of the array need
to be (N*2)+1 (I can see N positions to the north of me and N positions
to the south of me = &ltN*2> plus the position I am standing on = <+1>
in total = (N*2)+1). Logically the player sits in the middle of the
circle, thus the center position in the array is the origin of sight.

When the rendering(the looking), routine is run, if the corresponding
position in the sight matrix is set to true, we immediately know:

	1) We've already seen this position before
	2) What's currently on the screen at that position
	   is up-to-date...

 [Keeping the matrix 'up-to-date' is trivial in a synchronous
  environment(single player rogue), and they are most common
  so I won't bother to discuss what needs to be done in an
  asynchronous environment.]

...so there is no reason to do any further work, the coordinate is
skipped. Otherwise if it is newly seen, the sight matrix is set to
true at the respective indices. On the other hand, when forgetting,
only those positions that are turned on need to be considered
(because we cannot 'forget' coordinates we weren't able to see in
the first place). Doing this we say, "Ok, you *think* you can see
what's there but can you really?". If the answer is no, probably
based on the output from a line of sight algorithm, a 'forget'
routine must be applied. The forgotten position is then marked false
in the sight matrix.

We then need just a bit more functionality to complete this algorithm;
the ability to shift the contents of the array, and to clear it.

When a character moves forward, the sight matrix must shift backward:

		.......
		..***..
		..*@*.. What the player sees before taking a step
		..***..
		.......

		.......
		..***..
		..***.. The player has moved a step forward
		..*@*..
		.......

Shifting is needed to keep the matrix logically consistent. If
the player were to take a step forward but the matrix stayed the
same, it will obviously not reflect the change in state.
Generally, when a move is detected in a direction, the matrix
is shifted in the *opposite* direction. "What I saw before is
now _behind_ me."

If something novel happens, as in the player moving to a new
dungeon, the matrix must be cleared (because the contents make
no sense now in the new context.)

psuedo code for the procedures follow:

   static allocation of the sight matrix of dimensions [N*2+1][N*2+1]
   where N is the maximal radius of vision

   Function move has input{integer change_in_y, integer change_in_x}
   begin
      shift the sight matrix by -change_in_y and -change_in_x
      call observe
      call forget
   end

   Function observe
   begin
      for Y is the smallest possible value for Y contained within
      the bounding circle;
       while Y is less than or equal to the largest value L
       contained within the bounding circle; increment Y
         begin
            for X is the smallest possible value for X contained in the
	    bounding circle;
              while X is less than or equal to the largest value M
              contained within the bounding circle: increment X
	       begin
	          if the position in the matrix at [Y, X] is false,
		  then if the associated position in the world can be
		  seen, then look at that position and set the matrix
		  position to the true value, otherwise do nothing.
	       end
         end
   end

The implementation of forget is similar to observe; the inner loop
will forget a position and set the matrix index to false only if
the matrix currently has [Y, X] set to true but the player cannot
see that position (You were able to see it before, and you think
you can still see it, but you really cannot.)

There are further optimizations that can be implemented, such
as in determining distances between coordinates (thus giving
the indices in the matrix that needs to be checked), but I will
answer such inquiries via email given someone has an interest.

Note: This algorithm naturally takes care of cases where the
      player does not have perfect sight; there thing such
      as walls and items scattered in the field of vision.
      No modifications need to be made.

I hope someone finds this helpfull! ;)
```
