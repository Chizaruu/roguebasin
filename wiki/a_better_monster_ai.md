# A Better Monster AI

---

```text
A Better Monster AI - Joseph Swing [JSwing@wport.com]

I've written this to document what I consider a more interesting monster
AI.  This article mainly concerns how a monster might evaluate its
environment.  Places where someone has pointed out a suggestion on how
to expand or improve the basic model are marked with a (*).  Special
thanks for the rec.games.roguelike.misc group for their comments and
suggestions.

Some of RL games use a simple AI.  It looks something like:

 if (ISeeHero) then (MoveToAttackHero)
 else (DoNothing)

Surprisingly, this works pretty well.  But let's expand on it to give
the monsters some personality, some style.

JSwing's guidelines (this is the pompous section - I'm not an expert)
1) Different monsters want different things.
2) Monsters recognize other monsters and objects in the vicinity and
interact with them, even when the hero is not around.
1) Each monster acts independently.
2) Groups of monsters need to recognize themselves and be recognized as
a group.
1) You can always make it more sophisticated, but we don't need perfect
AI.  We only need the illusion of some more interesting behavior.  The
average lifespan of a monster in a RL game is maybe 100 turns, and only
10% of that on the visible screen.

Monster behavior is broken down into 5 possible generic types: Desire,
Fear, Aggression, Wander, and Misc.

Aggression - monster seeks to attack another monster (or hero).
Fear - monster seeks to move away from a monster (or hero); this may
       be expanded to include other stuff.
Desire - monster seeks an object or objects, incl gold, food, weapons,
         etc.
Wander - A random function to prevent monsters from getting locked into
         a pattern.
Misc - This will cover special behavior like staying near an altar or
       going on patrol.

Data types used to make decisions:
Each monster will have a value to weight the 5 types listed above.  If
this value is zero then the monster ignores any behavior of that type.
The monster may also have one or more specific personality behaviors,
such as Hate Elves, or Desire Gold.  Each of these will also have a
relative strength and should be considered an addition to value the
basic behavior type.  In order for a monster to have a Desire, it is
necessary to have a specific personality behavior to indicate what
kind(s) of thing(s) it desires.

In order for the fear and aggression behaviors to work, the monster must
have a relative combat strength for itself and any monster it sees.
(*) Possible Improvement : Monsters will have two relative strengths, a
smart view and a dumb view, where a human peasant and a human wizard
might appear the same to a dumb monster,
but a smart monster can tell the difference.
(*) Possible Improvement : Reduce the perceived combat strength of a
monster if it's injured.  This can be simple. Ex:  if (HP<(MaxHP/2))
CombatStr--

A monster will need a list of friendly monsters. Make this as broad as
possible.  Ex: all orc types are friendly with all other orc types, so
an Orc Warrior will know that an Orc Priest is its friend.
(*)Possible Improvement: Expand this simple binary (friend/not friend)
to a range of values to indicate the strength of the relationship from
utter hatred to complete awe.

In order for Desire to work, each monster must have a perceived value of
goods or loot.  Each object needs this as well, but since objects
usually have a worth, use that.

During a monster turn it will:
1) Look around and evaluate it's surroundings.  The monster should look
in all 8 directions.  For each object or monster it finds, it should
perform a calculation to help determine where it wants to go.

2) If it finds a friendly monster, the monster adds (monstr str +1 -
distance) to it's own perceived combat strength.  Friends will try and
work together.

3) If it finds a monster that is not a friend, assume it's an enemy.
Here's where we get tricky.  The desire to kill something is only based
on how strong it thinks it is and how far away the target is.  The
target's combat strength doesn't matter here.  Further more, it is only
interested in attacking the nearest enemy in any given direction.  I
don't expect to be able to walk by one enemy to get to another one.  We
also need to do this to evaluate groups of monsters correctly.

For any given direction, the aggression value is (my str + my buddies
str [per step 2]) x (6 - nearest enemy distance) + 2x (basic aggressive
value + any special hates).  We don't know for certain how many friends
we have until we finish looking around.  This means that we need to
track some intermediate values.  Start by tracking the nearest monster
distance in each direction.  At the end we'll modify this to find the
real values.

4) For *each* non-friendly monster, calculate a fear value.  This is
(target's perceived str) x (6-target's distance) + 2x (any special
fears).  Since we calculate this for each monster in a particular
direction we can figure the value the first time through.  This means
that groups of monsters are considered more dangerous than individual
ones.

If there are any monsters (we return a non-zero value) then we add + 2x
(my base fear) for the direction.  This way your base fear is static for
each direction, and does not depend on the number of monsters.  The
total value should be added to the direction opposite from where we are
looking, so we represent fear as a desire to move the other way as
opposed to a negative desire to move in this direction.  This makes it
easier to normalize at the
end.

5) For each object or target monster, figure any Desires if applicable.
If the monster who is looking around wants gold or equipment, use the
perceived ($) value for any target monsters.  If they desire food, use a
function of the corpse nutrition value.  Objects on the ground should be
calculated the same way.  The total desire for a particulare direction
should be the sum of (obj value) + (6-obj distance) + (basic desire) +
(particular desire).  Note that these values are added, not multiplied.

6) If you see something that would trigger the Misc category, then
calculate a value for it.  Examples might include staying close to an
altar, or coming to chat with a hero, etc.

(*)Suggestion from Aidan Ryder : maybe an altar should subtract from the
Fear value to indicate that the monster is trying to protect it's holy
place

7) Calculate a wander value, which is a random direction with a value of
1 << (basic Wander value) and << 2 if the monster is confused.  The
basic wander value should be low, but should be incremented if the
monster returns to a previous location (waffling) or stands still.  This
is to avoid getting stuck.  If the monster does not move to a previous
location or stand still, decrement the wander value to the monster's
minimum (from the monster template).  Do not alter the wander value if
the monster is occupied with a task that takes more than one turn
(including sleeping).

(*)Suggestion from Aidan Ryder:  [When a monster is occupied, for
example ] "Eating" ... it takes an amount of time proportional to the
weight of the food eating it...and while it is doing this it's "vision
range" is reduced to one or two squares...allowing you to sneak up on
monsters feeding on their kills.

8) Finally, revisit the aggression values.  In each direction we should
now have stored a value for the distance to the nearest monster if there
is one, else 0.  If there is a monster, we should now have our total
perceived attack strength (from all our friends) and can convert it to
an aggressive behavior value.

9) Add up the values.  Normalize to a vector (in X and Y direction, or
maybe even Z).  This is the direction the monster desires to go.  Also
calculate which is the strongest motive of the 5, and assign this as to
the monster action.  This will enable a monster to use a ranged attack
if it has one.

(*) See notes on path finding below

10) Be sure to calculate all of the monster AI values in a turn before
they move.  This will keep groups together.


Some nice behaviors come out of this:

Ex #1:If Bobo the Ogre (relative str 4) has to choose between a close
weak monster and a slightly stronger one a little further away, he'll
move to mash the weaker one first.

#.........    # = wall    . = space
#..3..B.2.    3 = str 3 monster
#.........    B = Bobo    2 = str 2 monster

Here Bobo chases after the 2.

(*) Steven Salter argues that it is in the monster's best interest to
attack the strongest opponent it thinks it can kill.  This would let
groups pick off the more dangerous opposition before any insignificant
enemies.  I disagree.  Since most monsters on a given level have about
the same combat strength, attacking a weaker opponent is better than
getting yourself injured by a stronger one.  A lot depends on how you
set up your monsters, and how densely populated your levels are.  You
can decide what's best for you.

Ex#2:If Bobo the Ogre is presented with the following:

........
.1...8..
........
...B....
........

He will move directly left, try and spiral around the Str 1 monster,
mash it, and run away (assuming the other two are stationary).

Ex #3: You can define a cautious scavenger type.  If Nimble the RatThief
(relative str 1, aggressive 1, fear 4, greed 4) spots a Hero (relative
str 4, $ 4), he will try and stay about 3 spaces away, moving further
back as the hero encounters other monsters, moving in if the hero gets
injured (and weaker).  The rat thief 'shadows' the hero, picking up any
goodies the hero leaves behind, moving in to kill only if the hero gets
weaker.

In this example, Nimble wants the player's gold more than he wants to
kill the player, so Nimble would rather try and pickpocket over stabbing
the player.

Weaknesses of this model
There's a big problem with 'out of sight, out of mind'.  Our monsters
don't remember where they want to go from one turn to the next.  If a
monster loses sight of its objective, then if forgets completely that
the objective exists.

#..........     # = wall     . = space
#.B........     B = Bobo     $ = Gold
#......####
#........$.

Bobo travels east and loses sight of the gold.

One solution: This problem will mostly occur with Desires.  Other
monsters will move around and may pop back into view of their own
accord, or else move further away and out of interest.  One solution is
to remember, from turn to turn, the strongest Desire and direction.  If
the calculated desire for that direction is zero (the object
disappeared), add the remebered desire back in.

Obstacle avoidance.  If a monster encounters an obstacle that it can see
around, like a pit, it doesn't know how to walk around it.  Obstacles
only become a problem if they are exactly in one of the 8 directions,
but the monster will often move towards this condition.  By separating
the direction the monster wants to travel from the actual getting there
I've also made some more work if we want to incorporate any pathfinding.

#.....    # = wall     . = space
#.B.X$    B = Bobo     X = pit
#.....    $ = Gold

Bobo has a problem.

Aidan Ryder points out that pits or other traps should only affect
Aggression and Desire, not always Fear:

#...........    # = wall
#.......X...    X = trap
#...D.B.X...    B = Bobo The Unlucky
#.......X...    D = Greater HellWyrm
#...........

Bobo [should] now run for it as fast as possible, ignoring the traps
because he is so scared of the wyrm ... if he tried to go around, the
wyrm may well catch him.

A solution:  My first solution was a hack.  If the obstacle was directly
in oneof the 8 directions and you wanted to be on the other side of it,
split the attractive vectors (Aggression and Desire) to point 3/4 at
(desired direction + 45 degrees) and 1/4 at (desired direction -45
degrees).  If the obstacle is crossable, but undesireable, leave the
fears alone.  This would let you walk around small obstacles and still
account for other impulses that might direct you in one direction over
another.  It's not a good solution since monsters get stuck on large
obstacles and still get lost fairly easily.  But it's easy to implement
with the existing model.

A good solution: We should combine the behavior impulse with a
pathfinding algorithm where it makes sense.  We will need an additional
data member for an (x,y) location.  When we evaluate the monsters
desires (steps 1-10 above), if the monster's strongest behavior is
either Aggression or Desire, we store the target location as well as the
direction.  If the strongest behavior is Fear or Wander, we store a null
value.  For Misc behaviors, it will depend on the specific behavior.

When we're done with evaluating all of the squares, if our (x,y) is not
null, then we can employ a pathfinding algorithm to get to the target.
You only need to evaluate paths in the desired direction, or maybe the
desired direction and the two adjacent directions.  Remember to include
a factor for other behavior we have already evaluated (if you want to
travel east and you've got a strong fear from the north, weight a
southern route more desireable.  You dont need to re-evaluate the thing
up north that is generating the fear).

If the (x,y) is null, then revert to the simpler method, since it better
handles behaviors that are not target specific.

Consider this text public domain.  Comments are always welcome,
JSwing@wport.com
```
