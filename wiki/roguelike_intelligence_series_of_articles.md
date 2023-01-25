# Roguelike Intelligence

---

By **Ray Dillinger**

## Roguelike Game AI overview

---

Since I'm an AI engineer, and since I'm unemployed right now, and since I'm interested in roguelike games, I thought I'd write a series of articles on game AI for roguelikes as a contribution to the community.

Some of these techniques (Stateless AI's and State Machines in particular) are properly "pseudointelligence" rather than "artificial intelligence", because they don't involve any actual learning. But "AI" is how game designers refer to the decision-making code for their artificial antagonists, regardless of its actual status, so I'm going to use it.

The ground I plan to cover here includes roughly these articles; I may add an article or two to this or change the order of articles depending on what people are interested in reading.

1. Stateless AI
1. State Machine AI
1. "Evolving" stateless AI's or state machine AI's
1. Modeling the player
1. Minimaxing in application to roguelike domains
1. Neural Networks and training them with backprop
1. "Evolving" neural networks.
1. Recurrent Neural Networks (NN's with memory).

## Stateless AIs

---

The classic "zombie" AI is easy to code: It goes like this:

```text
       ZOMBIE AI
          if can-move-toward-player
            move-toward-player
          else if can-attack-player
            attack-player
          else stand-still
```

And off your zombie goes, closing on the player as fast as it can and beating the player as hard as possible once it gets there. It's the simplest possible monster AI, the first monster we all implement when we're testing monster movement code for the first time.

Suppose you want to have a different kind of brain dead monster called a "Golem", which uses ranged attacks at range. Consider this:

```text
        GOLEM AI
          If can-attack-player
            attack-player
          else if can-move-toward-player
            move-toward-player
          else stand-still
```

This is the same as the zombie if it's carrying a sword, but if you give it a ranged weapon its behavior is dramatically different. If it has a ranged attack, it will stop dead as soon as it catches sight of the player character and stand there shooting until the player kills it or gets out of range or it runs out of ammo. If it's not dead and still has ammo, it will run after the player until it's in range again and start shooting some more. If it has both a ranged and hand-to-hand attack, it shoots till it runs out of ammo, then switches to hand-to-hand and behaves like a zombie.

This seems like a smarter, more versatile monster, and it's no harder AI work than the simple, stupid zombie. It can really mess up the way the dungeon works though, if other monsters can't get past it in corridors.

These are (very) simple examples of a stateless AI. It's called stateless because every time it's the monster's turn to act, it starts in the same 'state' -- ready to just start on the if statements until it finds an action it can do. Angband is a popular game whose monsters use (nearly) stateless AI.

Not all stateless AI's are as stupid as those given above. You can make a simple modification of the zombie AI for another kind of AI for slightly-less stupid monsters.

```text
      GHOUL AI
        If can-move-toward-player
           AND (random < move-probability
                OR can't-attack-player)
              move-toward-player
        else if can-attack-player
             attack-player
        else stand-still
```

Now, ghouls behave a lot like a zombie or a golem, except that its move/attack decision has a random element not predictable by the player. It may pause on the way to the player to fire a ranged weapon, but it won't just stand there firing and being a roadblock to other monsters, and it won't just run toward the player allowing him to ignore it till it gets there. Its move probability can be set in monster configuration so you could make different types of monsters using the ghoul AI more or less likely to move, which will distinguish your monsters from each other a little bit more.

Now, one more complication and your monsters will have just about the same intelligence as Angband monsters.

```text
        ANIMAL AI
          If damage-taken > morale
      if can-run-away-from-player
                 run-away-from-player
             else if can-attack-player
                 attack-player
          else if can-move-toward-player
                     AND (random < move-probability
                         OR can't-attack-player)
                     move-toward-player
               else if can-attack-player
                    attack-player
               else stand-still
```

The complication was the damage check in the first line. Now you can assign your monsters different morale as well as different move probabilities, and the timid ones with morale less than one percent of their hitpoints will run away when they take even a tiny wound while the maniacal ones with morale equal to or higher than hitpoints will fight to the death. Monsters that run away will come back once they heal a little bit, fight when cornered, and generally behave about like you'd expect a bunch of really stupid animals to behave. The damage check is sort-of a mode, since damage carries from one round to the next. But this is still a modeless AI, because we make the decision every round. The random-state is also sort-of a mode, but the decision made is effectively purely random, so it doesn't count.

Now let's look at code smarter than Angband's, introducing the idea of a monster with a ranged attack and a preferred range. That will go like this:

```text
       ARCHER AI
   if can-move-away-from-player
             AND (damage-taken > morale
                 OR too-close-to-player)
             move-away-from-player
          else if can-move-toward-player
             AND damage-taken < morale
             AND too-far-from-player
             move-toward-player
          else if can-attack-player
      attack-player
          else stand-still
```

This creature will use a "gun-n-run" strategy, trying to use a ranged attack at range while staying out of the player's reach. If it's faster than the player character, or if a bunch of them are encountered together, it can be very dangerous to a character with no ranged attacks, even if it's ridiculously weak.

If it has no ranged attacks, it becomes a silly "spectator" in the dungeon, walking around and keeping an eye on the player character but not doing much of anything else. Give it a morale of 1, lots of movement and hitpoints, and name it The "Dungeon Survivor Camera Crew". Your players will report intense satisfaction from finally maneuvering it into a deadend and killing it.

Now, you can take this a lot further in terms of dreaming up stateless AI's that pursue different strategies in the dungeon, including AI's that stay in packs, trying not to get too widely separated from others of their kind. But before you do all of that, there's another opportunity to make your monsters smarter using these simple, stateless AIs.

You can increase the basic pseudointelligence of stateless monsters, and increase the differentiation between monsters, by making the various primitives involved in the stateless AI's more sophisticated and different on a per-monster basis.

For example, run-away-from-player can be stupid (moving to the adjacent square that maximizes distance to the player) or smarter (heading for a room exit - by preference, one that the monster is closer to than the player) or smart (heading for an exit, but avoiding dead ends).

Move-toward-player can be stupid (stepping to the adjacent square closest to the player) or smarter (following the player's scent track if the player's not in sight) or smart (doing pathfinding to find a way around obstacles when the player's not in sight) or exhibit both brains and teamwork (pathfinding if player's not in sight, trading places with a more badly-damaged monster of the same type if one is closer to the player).

Attack-player can be stupid (using whatever the monster's only attack is) or smarter (picking among several attack forms based on what's available, such as switching to meelee weapons when out of ammo) or smart (picking among different attacks based on what's available and known about the player character).

"stand-still" routines can do some monster-specific thing like throwing a healing spell at the most badly wounded monster in the room, which is another kind of monster teamwork, or casting a teleport spell, which will frequently get the monster out of whatever "stymied" situation it's in, or telling the player a rumor or insulting him.

If you have a few dozen different stateless AI's, and then a few different versions of most of the primitives they use, combinatorics is on your side. You will be able to make a menagerie of "simple" monsters that exhibit hundreds of different kinds of behavior.

That said, this type of pre-coded, stateless AI is still very limited; it is in fact the very simplest form of pseudointelligence for implementing game adversaries

Some of you will by now have noticed something. I first introduced the "zombie AI" which defaults to movement if possible, then I introduced the "golem AI" which defaults to attack if possible. Then I introduced the "ghoul AI" which makes a random choice between movement and attack when both choices are valid.

What I didn't point out in the first article was that the "ghoul AI" can be used to imitate both the "zombie AI" and the "golem AI" -- all you need to do is set the move-probability parameter to one or zero respectively.

I went on to introduce the "animal AI" -- but that can be used to imitate the "ghoul AI." All you need to do is set the morale to some value greater than the creature's hitpoints and it will never retreat.

Then I introduced a run-n-gun monster with the "archer AI". But here I wasn't strictly generalizing; The "animal AI" can't be imitated strictly within the code of the "archer AI".

This may not seem important, but bear with me for a little bit; here's an AI pseudocode that can be used to implement both the "archer AI" and the "animal AI".

```text
         TYPICAL AI
            If damage > morale
               if can-run-away-from-player
                  run-away-from-player
               else if can-attack-player
                  attack-player
            else if too-far-from-player
               AND can-attack-player
               AND can-move-toward-player
                   if  random < charge-probability
                       move-toward-player
                   else attack-player
            else if too-close-to-character
               AND can-attack-player
               AND can-move-away-from-player
                   if random < retreat-probability
                      move-away-from-player
                   else attack-player
            else if can-attack-player
               attack-player
            else if too-far-from-player
               AND can-move-toward-player
     move-toward-player
            else if too-close-to-player
               AND can-move-away-from-player
                   move-away-from-player
            else stand-still
```

Now, if we want an "archer AI" we set retreat-probability to 1 and charge-probability to 1.

If we want an "animal AI" we give it a too-close-to-player function that is never true, a too-far-from-player function that is always true, and copy the "animal AI's" move-probability parameter into the "typical AI's" charge-probability parameter.

The separation of charge-probability and retreat-probability means that the creature may pause while moving toward the player to fire a ranged weapon, or pause while moving away from the player to fire a ranged weapon. But since these are different tactical situations it seemed reasonable that the probabilities should be different.

One other thing I did in the above AI was to separate the functions run-away-from-player and move-away-from-player. The first is for panicked situations when the monster doesn't want to be anywhere close to the player; it could be implemented in a smart, spell-using monster as casting a long-range teleport spell. The second is tactical; it means the monster wishes to be further away from the player for tactical reasons. The smart, spell-casting monster could implement this as a classic "blink" - a limited teleport that moves it, but only ten to fifteen squares away. Less gifted creatures will implement this as stepping to an adjacent square further from the player.

What I wanted to demonstrate by folding both "animal" and "archer" into the same AI was this; if you are using stateless AI's you can have a universal AI routine that's shared between all monsters. The distinction between monster AI's is then reduced to a simple array of parameters and methods.

Even if no worthwhile combination of the two is possible, you can still combine any two stateless AI's into the same stateless AI code by just making an additional parameter that says which branch of the decision tree to follow and putting each of the original stateless AI's on one branch of the decision tree.

Instead of the more sensible code above, for example I could have just written

```text
      TYPICAL AI:
         if am-I-an-archer?
            {...archer AI ....}
         else
            {...animal AI ....}
```

But I do want to make a point: this kind of combination should not be done automatically or thoughtlessly; If I had done it without thinking, for example, then I would have redundant branches for "zombie" and "golem" and "ghoul" in my code (or redundant objects all inheriting from "monster", which is exactly the same kind of mental spaghetti) when all of those behaviors can easily be modeled by the "animal" code. Also, I wouldn't have the possibility of firing- while-advancing for archers, and without doing that I wouldn't have thought of the case of firing-while-retreating.

If you use OO a lot, or without seriously considering what is redundant or what would be better combined, it's probably worth taking a good long look at your code to see how much redundant stuff you have built in. This happens a lot with behavioral modeling code; frequently you'll create something more complex, adding capabilities incrementally, without noticing that it's made a bunch of other things redundant.

Another risk is that you might create something different, and miss out on other benefits or different behaviors you could model if instead you made the routine you had more general. This is what happened when I implemented the "golem" AI, and later the "archer" AI; in both cases there was a more general AI that included both kinds of behavior where I was drawing a line between them, and in both cases the more general AI also allowed new behaviors that neither of the previous two allowed.

The take-home lesson is that, as a design principle, it's always better to have a stateless AI that's more general than it is to have more different stateless AI's. OO programming can be handy, but it allows you to very easily miss benefits, redundancies, and synergies if you're not being particularly alert for them. It's equally valid to say that it helps you by keeping such issues outof your hair, but the fact is that your design decisions in response to those issues are things your game can benefit from.

Now I think I've said enough about stateless AI's and it's time to move on to state-machine AI's.

### Reference section

---

Here is a list of the primitives I've used in building stateless AI's in these first two articles.

- can-move-toward-player
- move-toward-player
- can-move-away-from-player
- move-away-from-player
- can-run-away-from-player
- run-away-from-player
- can-attack-player
- attack-player
- too-far-from-player
- too-close-to-player
- stand-still

Here are some other primitives you may find handy in building a good stateless AI:

- player-is-friend
- player-has-food
- player-too-powerful
- player-is-same-alignment
- player-is-opposed-alignment
- enough-buddies-in-pack
- too-far-from-pack-center
- can-move-toward-pack-center
- move-toward-pack-center
- too-close-to-pack-center
- can-move-away-from-pack-center
- move-away-from-pack-center

Stateless AI's aren't really general enough to model dynamic monster/monster relationships, but if you want to try it you'll need a lot more primitives than just these.

## Intrinsic Information and State Machine AIs

---

State machine AI for more complex monsters.

Stateless AIs as described in part 1, although they make your life as an implementor fairly simple, also make your monsters fairly simple. Stateless AI's, generally, become easy for the players to predict, and thus detract from the challenge of the game.

Using state machine AIs, you can challenge the player with more interesting monsters. State means that the decision routines of each monster have recourse to stored information which is both intrinsic (internal to the decision code for a particular monster) and mutable (changeable by the decision code in a particular monster).

We have already seen some examples of state in monsters, but these do not represent state in monster AI's: The "typical AI" presented in article 1B used information about the monster's level of damage to make its charge-or-flee decision each round. Similarly, the decision routine can-attack-player in the "archer AI" was presumed to check whether the monster still had arrows available. In both cases, these are extrinsic information to the monster AI. These refer to the state of the model of the physical body of the monster and its dungeon surroundings and equipment; these are things that the AI itself can't change arbitrarily.

(Note, archers are easier when you just presume they have an infinite supply of arrows; then you don't have to keep track of this information. Of course, then players learn to "farm" archers for an infinite number of arrows...).

Finally, such things as "morale" and "too-close-to-player" and "too-far-from-player" are considered to be extrinsic as long as the monster AI doesn't change these values.

You can get some interesting behavior in stateless AI's by creatively using extrinsic information. But state is fairly easy to add, and with a state machine you can create almost any degree of complex behavior in monsters.

There are two basic places to introduce state to your monster decision code: I call them "learned information" and "tactical state". Learned information is just that: something the monster has learned in the course of its brief life and dungeon encounters. Tactical state is high-level "what was I doing" or "what is my current goal" information that is used to choose between several different stateless AI's depending on the current plan.

As an example of learned information, consider an AI for a goblin which sets a memory variable if that goblin ever sees the player kill another goblin or stronger monster with a single attack, or sees the player kill more than four goblins in less than eight rounds. Before the goblin sees that, it will treat the player as just another @-hooman, which goblins regard as good sources of food. But after it sees that, it will regard the player as a threat to be avoided, attacked at range, trapped, or ganged up on. The goblin AI need never check this variable explicitly; instead, it just calls a routine named "observe" every round which updates information that its other routines use, and then it's AI, which otherwise looks just like a stateless AI, is calling functions like "player-is-too-powerful" which refer to things that "observe" has recorded.

You can implement "player-is-too-powerful" for a stateless AI too; in that case it will probably read the character's level, weapon damage, or kill list in order to make its assessment. But this is information the goblin shouldn't have access to; giving its AI recourse to it seems to make the critter omniscient in some ways. Of course, this omniscience is no stranger than that of monsters who don't have line-of-sight or any particular detection abilities bunching up on the other side of a wall trying to get closer to the player character, but the omnscient goblin presents the player no way to influence it.

What you want is for the player to learn something about cowardly monsters who attack in packs; if you kill a few of them, quickly, where all the others can see, then the others will run away or change tactics. Now knowing this, the player has a choice to make about how he wants to manage his goblin encounter. He can switch to a less-damaging weapon, pause a lot in combat, fight a running battle while giving ground, and (slowly) slaughter the entire goblin tribe without them twigging to the fact that he's toying with them; or he can just slice a few of them in half with his artifact sword and let the rest run away.

If the goblins are omniscient in their player-is-too-powerful assessment, the player doesn't get this tactical decision to make, and the gameplay gets one element less interesting.

As an example of tactical planning state, consider a dragon who is encountered asleep. His tactical state is "SLEEPING," and that means that the only things he can do in a given round are "sleep" and "wake up". Now, the instant he wakes up, his tactical state becomes "enraged" if there's treasure missing, "hungry" or "curious" otherwise. Depending on which state he enters, he will use different decision code in subsequent rounds.

You'd write this part of the dragon AI as something like this:

```text
       DRAGON AI (partial)

  State:  Obs: Action:          input L    input M    input H   NULL

  SLEEPING L   asleep-ai        WAKING:1              HUNGRY:1
  WAKING   C   none                        ENRAGED:1  HUNGRY:1  CURIOUS:1
  ENRAGED  C   typical-ai, p1                                   WAKING:1
  HUNGRY   C   typical-ai, p2              ENRAGED:1
  CURIOUS  A   curious-ai                  ENRAGED:1  HUNGRY:1  BORED:0.1
```

The inputs I've labeled L, M, H, and NULL mean, respectively, loud noise, missing treasure, hungry, and none of the above. Each state, except for the WAKING state, is associated with "Action:", a stateless AI that chooses the action for monsters in that state. The WAKING state is associated with "none", which means that, instead of running a stateless AI and performing the resulting action the system immediately checks further inputs and shifts to a different state. Finally, the column labeled Obs: is for Observation, which defines a creature's alertness in general. The SLEEPING AI has observation L, which means it observes loud noises; most of the other states have observation C, which means they observe anything casually visible. The Curious state has observation A, which means all; it observes carefully and intentionally, as though the dragon had performed a "look" command.

Note that the typical-AI is engaged for both "enraged" and "hungry" behavior, with different sets of parameters. This provides two different sets of similar behavior, but with different details. This is where the state machine sets the information that was formerly regarded as extrinsic to the AI for the stateless AI's.

The numbers that follow each state name in the table entries are probabilities of transition. The probabilities of transition are almost all 1, which makes it nearly deterministic. We can have more than one input true at a time; For example, a sleeping monster could wake up to both hunger and loud noise, and then would he be waking, or hungry? As matters stand, we check inputs from left to right and therefore he'll be WAKING.

But look what happens when our critter is CURIOUS and gets no input. Next round, he has a 90% chance of being CURIOUS and a 10% chance of being BORED. So this is, actually, an NFSA.

pseudocode for operating the DFSA looks something like this, although I have simplified it by pretending I don't have to bother with parameter lists for the stateless AI's:

```text
acted = 0;
while (!acted)
{
    observe(statemachine[obs][currentstate]);
    shifted = 0;
    for (inputs=FIRSTINPUT; inputs < LASTINPUT && !shifted; inputs++)
    {
       if (input_is_true(input))
       {  /* note that what's stored in the statemachine is an expression,
             not necessarily just a number. getshiftprob substitute in
             values from the monster's extrinsic info and solves the expr.*/
          probshift = getshiftprob
                 (statemachine[input][currentstate].probshift);
          if random() < probshift
          {
             currentstate = statemachine[input][currentstate].state;
             shifted = 1;
          }
       }
    }
    if statemachine[action][currentstate] != NULL
    {
       do_stateless_ai(statemachine[action][currentstate]);
       acted = 1;
    }
}
```

Given this code, plus a few mild complications like parameter lists for the stateless AI's, You can make arbitrarily complex state machines simply as arrays of states versus inputs, where each state additionally has an observation power and a stateless AI to pick actions. Your NFSA's for complex monsters can just be arrays, simple to store, handle, read from file, and so on. For each input you'll need additionally a way to detect that input.

It's common for roguelikes to bypass the need for a general "observe" function. For example, the dragon might have a chance to wake up, per round, if the character moves, depending on the character's stealth. This sort of thing can be reasonably effective (meaning that fixing it doesn't become a high priority) but always seems to wind up with flocks of weird special cases and inconsistencies.

It is more general to mediate things through the dungeon itself: When something moves, store noise information in the dungeon map with the current round number, and when something is listening, have it check the dungeon map for "recent" (ie, since its last action) noise through an observe routine. Basically, the idea is that any "observable" act should leave evidence in the dungeon map, and "observe" should pick up that evidence and notify the monsters of what they have seen, heard, or smelled. Observe will become complicated: there's no getting around that. But it's best, I think, to have one observe routine, or as few as you can possibly manage, because it keeps things consistent when everything that's supposed to be related uses the same routine.

If you are an OO fan, you may choose to bypass storing information in the dungeon map itself, instead favoring explicitly sending messages representing noise or observable acts from one actor to another in the dungeon. This works if you are disciplined, but it still manages to leave a lot more room for unhandled cases than the dungeon-map-as-sensorium model - and the dungeon-map-as-sensorium model, which is better, can also be implemented in OO.

If you don't do either of these approaches, having monsters respond to dungeon events becomes a huge mass of exception-based code with quirky holes and problems. For example, in games where the chance for a sleeping monster to wake up is based on player movement and player stealth, you can have fireballs from traps go off next to the sleeping monster's head, or a herd of rhinoceri wearing plate barding tramp through the room, and the monster will go on snoozing peacefully. And if, in the process of making all your sleeping monsters wake-up calls a bit more reliable, you overlook other behaviors that are (or should be) noise sensitive, you will develop monsters that hear more when they're asleep than when they're awake. Many examples of such idiosyncratic behavior can be found in existing games.

Oh, speaking of existing games, remember what I mentioned about angband's monsters being _mostly_ stateless? In fact they have three states. Angband's monster AI, expressed in these terms, may look roughly like this:

```text
State:      obs.   Action:        input-P       input-D              NULL

SLEEPING:    C    asleep-ai       ATTACKING:1   ATTACKING:1
ATTACKING:   A    typical-ai(p1)                RETREATING:dt/maxhp
RETREATING:  A    typical-ai(p2)                BERSERKING:0.05       ATTACKING:1
BERSERKING   A    typical-ai(p1)                RETREATING:dt/(4*maxhp)

input-P means "player has been detected".
input-D means "damaged".
dt is damage taken;
maxhp is maximum hitpoints.
```

This allows angband monsters to decide to go on the attack again even while badly damaged, which the typical-AI didn't do. These monsters start out asleep, then go on the attack when they wake up. Each round they are attacking, they have a chance to start retreating that depends on their damage taken and max hp. Once they retreat, they have a chance to start attacking again, only this time it's berserking, with a much lower chance of retreating. They alternate between berserking and retreating for as long as they're damaged. If one becomes undamaged, it will revert to ordinary attacking.

Observation is very rudimentary in Angband; the "observe" routine (or whatever angband uses for its functionality) checks the amount of damage the monster has taken, checks for lineofsight to the character, and, I think, that's it. I don't think these monsters are even aware of individual attacks, just of how badly damaged they are at that moment.

```text
typical-ai(P1) uses a "too close to player" function that is never true and
                    a "too far from player" function that is never false.
typical-ai(P2) uses a "too close to player" function that is never false and
                    a "too far from player" function that is never true.
```

Now, this looks simplistic, certainly; but remember that a lot of stuff is folded into typical-ai. And if typical-ai looked simplistic, remember that ten times that amount of stuff is folded into the various behaviors and checks it calls.

Anyway, this is my general architecture for monster AI:

behaviors and functions are implemented directly in the source language.

Stateless AI's are implemented in terms of a repertoire of interchangeable behaviors and functions. The Stateless AI serves as a "pattern" that brings these things together, but different kinds of behavior can fill in its specific elements.

and State machine AI's are implemented in terms of stateless AI's with parameter lists, and state transition tables. In the same way that the interchangeable behaviors and functions fit into the stateless AIs, the stateless AI's fit, as interchangeable building blocks, into the pattern formed by the state machine.

State transition tables are implemented in terms of the above routine for doing transitions and calling stateless AI's, and "observe", which runs over the dungeon map looking for evidence of specific observed events and changes intrinsic information that the behaviors and functions of a stateful AI can access.

I want to make one point about the "observe" function. You can spend as much time as you like writing it. Even a very simplistic "observe" such as that in Angband is functional. But there is always and forever stuff you can add to it and more patterns it hasn't learned. You have to pick your own point of diminishing returns here.

In the next article, before moving on to minimaxing, neural networks and genetic algorithms, I'll talk about "reactive" intelligence; how to store information about how to act on the objects that a creature interacts with.

## Displaced Actions

One technique that is fairly important, and requires support in the basic architecture of your system, is the use of Displaced Actions.

To recap the terminology I'm using in these articles:

\*Behaviors\* are simply actions that the monster can take. They have titles like "attack-player" or "move-toward-player" or whatever, and a given behavior can be implemented in different ways (by use of different \*actions\*) for different monsters.

\*Tests\* are boolean tests for conditions like "player-is-too-powerful" and "can-move-away-from-player" and so on. Tests are much like behaviors in that they can be implemented in different ways for different monsters. For example, "can-move-toward-player" may mean something different for a monster smart enough to do pathfinding.

Different specific code for tests and behaviors is a simple way to represent different monster capabilities or monster intelligence.

\*stateless AIs\* are basically nested if statements composed of tests and behaviors, in varying degrees of complexity. A stateless AI always picks a behavior to execute, in any conditions. Stateless AIs implement a pattern of different behaviors for a given type of monster; A given stateless AI will call on several to several-dozen tests and behaviors, and may accept as arguments any version of those tests and behaviors; thus the same stateless AI can produce many hundreds or thousands of different kinds of behavior depending on the exact versions of the tests and behaviors it calls on.

\*state machine AIs\* are transition tables a monster can follow, which switch it between states depending on inputs. Each state contains a stateless AI that selects actions while the monster is in that state, and a set of parameters for the "observe" routine indicating the monster's sensory abilities and general alertness.
The inputs to the state machine AI's are gathered by a general routine called "observe", which checks some central communications channel to discover things observed by that monster. I've advocated storing sense impressions in the dungeon itself, or in some central message queue, and having only one "observe" routine shared for all purposes by all monsters.

Okay. recap's over, now I'm going to explain what I mean by "Displaced actions."

Consider a Behavior called "switch-places-with-other-monster." When a monster executes this behavior, it is supposed to mean s/he trades places with an adjacent monster; typically this action will appear qualified by Tests such as "more-wounded-than-me" and "same-species-as-me" and "blocks-route-to-player," enabling a teamwork situation where wounded monsters facing the player are replaced in tag-team style by fresher reinforcements from the rear.

Now, the monster that executes this action has performed something relatively simple: they've moved one square (presumably one square closer to the player). But this action also involved the monster with which places were switched, and as far as that monster is concerned this is a "Displaced action:" That is, an action chosen for it and executed on it by some other monster.

The effects of a Displaced action on a monster are basically that the monster executes a Behavior just as though that monster's AI had chosen that behavior itself; it uses up time, moves the monster, may generate a message, etc.

Now this is a relatively simple idea, but it's powerful. It allows teamwork amongst your monsters in ways that are very difficult or impossible to achieve otherwise, it allows shoving and maneuvering as well as hitting for damage, and generally adds a lot to the game.

But, carried on to a slightly different conclusion, it allows you to save yourself a lot of coding work by building special behaviors into special objects or monsters.

For example, remember Adom's Altars? If the player catches a monster stepping onto an altar he can sacrifice it. Presumably a monster can do the same thing to the player, but who has time to build special behavior into a hundred different state machines and extend the "observe" routine to cover the special case? It's far far better, I think, for the altar itself to be a monster, with a special displaced action that it's ready to use on any co-aligned, adjacent monster if the player-character should be so foolish as to step onto the altar.

Thus, the code for sacrificing the player-character can be built into the altar itself. Now the altar AI is very simple:

```text
ALTAR_AI
   IF player-standing-on-same-square
   AND adjacent-coaligned-monster-can-sacrifice-player
       adjacent-coaligned-monster-sacrifices-player
   ELSE stand-still
```

From the player's point of view this looks like every monster in the dungeon suddenly got smart enough to know how to sacrifice him if he steps onto their altar. But it's absolutely unnecessary to give any mention of this behavior in the monster AI's themselves, except for the Altar AI.

Many other bits of your dungeon can work the same way; special levers that operate gates, special gateways that conjure demons, doorways that \*somebody\* has to lock before the orc guards flee the room, thrones that want one goblin king to sit on them, and all kinds of other things can work with generic monsters, just by having those objects have AI's that get other monsters to operate themselves using Displaced Actions.

In order for a monster to be eligible to commit a displaced action, it has to have enough time. This is trickier than it seems. In most rogulikes, the monsters choose actions and act, one at a time. This may mean that, by the time the Altar, or doorway, or gate or throne or whatever acts, all the monsters around it have already committed their time for the next round and it has no monster it can make into its operator. So things that use Displaced Actions, in a turn-based system, need to have a pretty high "initiative" score so that they pick their operator before the operator decidesto do something else that round. In roguelikes that use time or pulses, then you're going to have the situation where it becomes the Altar's turn and there are no monsters around it whose turn italso is. This means it picks its operator, but the operator isn't technically able to do anything until its turn comes around.

There are a couple of ways to handle this; my own favorite is that when the altar/door/gate/whatever chooses "stand-still" because it can't find an eligible operator, its "stand-still" behavior waits until the instant before its chosen operator's turn comes around. (alternatively, this may be a different behavior named wait-upto-monster or similar: wait-on-monster waits until just \*after\* the monster's turn). This requires a function that can query another monster to see when its next turn is, but that's fairly easy.

Then, when it wakes up an instant before its chosen operator, it uses its Displaced Action, which also uses up the operator's next turn -- and whatever the "instant" is (one 'pulse' or 'phase' for you guys who break up the round into even numbers of 'pulses' and 'phases' or maybe a tenth of a second for continuous-time games where dungeon time and next-turn are float values), that can be added to the time needed to complete the Displaced Action so it all comes out even.

Anyway, with Displaced actions, you can fill your dungeon with all kinds of tricky traps and mechanisms, and most of your monsters will "magically" get smart enough to use them.

In order to support Displaced Actions, you will need a bunch of tests allowing action-displacers to check on the status and location of nearby monsters, and, of course, a bunch of displaced-behaviors that the action-displacer can project. You will probably need a state variable that specifies which monster of the nearby ones is under consideration. Once you have those, you can implement stateless AI's and state machines that use displaced tests and displaced actions.

Uses range from the simple (monsters trading places) to ways to implement class abilities (priestly "turn-undead" effects) to funny (wand that forces any monster to spend its next few actions singing and dancing) to the arbitrarily complicated (lever that floods the next room, other lever that releases piranhas, other lever that flushes next room, fourth lever that uses the rushing water to power a "shredder" so as to properly compost the effluvient...). Creativity is encouraged, but try to avoid getting excessively capricous or vicious. Most of the "best" uses of this capability are in fact pretty simple.

## Genetic Algorithms and Evolving State Machine AIs

---

Genetic Algorithms and evolving stateless AIs.

Now we're going to get to the first technique that I'd refer to as "Artificial Intelligence" rather than "Pseudointelligence." The difference between these two terms, colloquially, is that when you're working with an Artificial Intelligence technique, you can be genuinely surprised by the results, where a pseudointelligence simply does what you tell it. But, as far as most people are concerned, if it's what decides monster actions in a game, they're happy to call it "AI" and such distinctions are merely pedantic.

Keep in mind that from this point onward, we are going "above and beyond the call" as far as implementing roguelike monsters. I do this kind of engineering for a living, and I'm not using it (at least not yet) even in my own game.

The fundamental algorithm for most kinds of artificial intelligence, when boiled down to first principles, is disarmingly simple:

1. Develop some structure capable of complicated behavior that's also capable of being adjusted or tweaked just about infinitely.
2. If it doesn't do what you want, make small changes in it in a way that's expected to improve its behavior.
3. If it does do what you want, stop the program.
4. goto 2.

In this article, I'm going to first give a general introduction to genetic algorithms and then talk about how to apply them to the kind of stateless and state-machine AI's I've talked about in the first couple of articles. The stateless-AI's and state-machine AI's constitute a structure capable of complicated behavior and also capable of being tweaked or adjusted a lot; For a genetic algorithm, the process of "adjusting it in a way that's expected to improve its behavior" means "adjusting members of a population randomly and then biasing survival vs. reproduction against the ones whose behavior is not improved."

Here is how a genetic algorithm, in general, works. You have a "population" of individuals. Each individual is judged according to a fitness function that says how good it is. You then pick a couple of the "better" individuals, and make a new individual by combining their sequences in some way. You replace one of the "worse" individuals with this new individual, and the cycle repeats. Every so often, you pick some individual in the population and change something about that individual at random.

There are an infinite number of variations on this general theme. The parameters that the engineer has to work with are population size, mutation rate, fitness function, gene map, degree of elitism, and method of combining individuals. Many systems change one or more of these parameters during the run. Some discussion of what these parameters are and how they influence the runs (read: guidance at troubleshooting) will help you figure stuff out and avoid some of the more popular mistakes.

Population size is the number of individuals in the population. If it's too big, your genetic algorithm will take a long time to converge on a solution. If it's too small, your genetic algorithm will quickly find a "solution" but it may not be a very good one.

The fitness function is absolutely critical. What you're going for is that, in most cases, small changes in the individuals should map to small changes in the overall fitness, and similar values at similar locations in the genomes should usually mean similar things. But it's often very hard to visualize in advance exactly how the fitness function will map things. For an extreme case, consider a fitness function for finding a key in a modern cipher system; basically, if the individual is the key, its fitness is perfect and if it's anything at all else, its fitness is perfectly wrong. A genetic algorithm can get no "traction" here, because there's nothing to base fitness decisions on. For another extreme case, consider a fitness function that looks at one number in the genome, takes four minus the number, and squares it, regarding the lowest numbers as the best fitness. This presents a simple fitness landscape with one "valley" centered on four. Every change to that number will map to a change in the fitness, and every change that makes an individual more fit will bring it closer to the global optimum. The first system will never converge except by blind luck; The second will converge trivially. Roguelike-AI fitness functions are particularly hard to come up with, but I'll talk more about that later.

The mutation rate has to be considered in combination with the mutation types and the fitness landscape. If you imagine that the fitness landscape has broad curves and few local optima, you'll want a high mutation rate with mutations that make smallish changes; this is called an "exploitation" or "hill climbing" strategy, because it "exploits" the area that the individual is in, making new individuals very near it in the expectation that those with higher fitness scores (those "higher on the hillside") will survive to the next generation, until the optimum is reached. If you imagine that the fitness landscape is very complex, you'll want a lower mutation rate because the changes it makes will necessarily be large. This is called an "exploration" strategy, because it makes individuals that are further away from its parent. Early in a run, lots of these will find themselves at a better level of fitness than their parent, and will in turn become parents themselves; late in the run almost all mutants die. This is the factor that is most commonly twiddled during a run. In a tactic called "simulated annealing", there are periodic 'cataclysms' during the run where the mutation rate is raised far above its normal level to put individuals out there all over the fitness landscape, then returned to a low level so that the unfit ones die out over succeeding generations. You also get a lot of workers who start with a sustained, high mutation rate and then gradually reduce it during the run. What you need to know about mutation rate is not where to set it, but rather what indicates that it's too high or too low. If the mutation rate is too low, then you'll get convergence, but usually on some local optimum. Successive runs will find "solutions" of significantly differing quality. If the mutation rate is too high, you'll get non-convergence; you'll have some highly fit individuals in your population, but the proportion of highly-fit to other individuals won't be increasing as generations pass. When it's just right, you'll get solutions in different runs that have very similar (about as good as there is) fitness.

The degree of elitism expresses how much of a reproductive advantage an individual in this population has as a result of being more fit. The classic "newbie mistake" in genetic algorithms is to always breed the very most fit individuals -- absolute elitism. This doesn't work. Selection entirely at random also doesn't work, as it confers no advantage to the more fit individuals. When you're selecting individuals to breed, or individuals to eliminate, or both, fitness has to influence the decision, but it can't dominate it. Even the "worst" individual in the population should have some chance of having offspring, and even the "best" should have some odds of failing to make it to the next generation.

What I usually do is pick three individuals at random, order them from best to worst in fitness, then assign chances to breed and chances to be replaced for each depending on their ranking within the three. For example:

```text
       "normal" elitism   "small" elitism  "slight" elitism
        %breed %replace   %breed %replace  %breed  %replace
best        50    20         40   25          35    32
medium      30    30         35   35          34    33
worst       20    50         25   40          31    35
```

A high degree of elitism will eliminate potentially valuable genetic material before that material gets considered in combination with other things it could be combined with; a low degree of elitism will preserve and experiment with genetic material in different combinations, gradually eliminating it only as it finds absolutely no use for it. The more complicated the problem you're trying to solve (the more complex the fitness function and the more complexity-rich the combinations of genes are), the lower the degree of elitism you want. You need to give the system time to make its experiments. Elitism has to be considered in combination with mutation rate; mutation introduces new genes, and if mutation is high and elitism is low new genes will be introduced faster than the experiments with different combinations can play themselves out. A very low degree of elitism requires a low mutation rate to work in your favor. If elitism is too low for the current mutation rate, you'll get non-convergence; however, unlike the situation where the mutation rate is too high in general, the fitness of even the most-fit individuals in the population will fluctuate wildly, sometimes hitting high notes but often just wandering.

The gene map and the method of combination also have to be considered together. The gene map is usually a vector or sequence of values, and the values control different things. The more those things influence each other's value in the fitness function, the more likely you want it to be that they are transferred \*together\* to an offspring. This is not actually essential, and it doesn't mean the difference between convergence and nonconvergence; but it can make the convergence process significantly faster. The most common method of combination for a vector is "crossover," which means that a copy is made of one of the parents, and then, values from the other parent are copied over them from a randomly selected "beginning" point to an "ending" point. The result of this is that values closest to each other in the gene map are also most likely to be transferred to offspring together. And what that means in practical terms is that you want to put values that form a particular strategy or behavior that's important, close together in the gene map.

Now how do we apply this technology to developing AI's for roguelike monsters? Well, it's hard. Some problems I can answer, like what would be a good gene map. Others, like finding a good fitness function, are hard but I can discuss a couple approaches to them.

First of all, let's talk about evolving stateless-AI's of the sort I talked about in article 1. Each of these is, basically, a nested set of if-then statements. That is, they have a tree structure:

```text
        test1              at left is an example of a
        /   \              a stateless AI, each node of the
    test2  test3           tree is a test or an action.
     / \     |  \
 act1 test4 act2 test5       below is this tree as a vector,
       / \       /  \        where leftchild(n) = 2n and
    act3 act4  act5 act6     rightchild(n) = 2n+1.

test1 test2 test3 act1 test4 act2 test5  act3 act4 act5 act6
1      2     3    4    5      6    7      10   11   14   15
```

For example test5 is found at index 7. its leftchild is found at index 7x2=14, and its rightchild is found at 7x2+1 = 15. Note that there is nothing at vector indexes 8 and 9, nor 12 and 13.

This remapping trick with trees is valuable; it lets you code with just a number what the relationships of nodes are within the tree. You get isomorphic structure between different individuals, which means the same gene will usually means the same thing in different individuals as convergence happens.

Also, you get the ability to use subtree swapping for your combination function, which preserves entire behavioral complexes from one parent to the next. That is, you just cut a branch off one parent tree and substitute a branch randomly selected from the other parent tree. That preserves complexes of behavior that work together. A good mutation function is one that picks a node and replaces it with a different node. You'd even have access to "small" mutations, since you can replace actions or tests with self-similar actions or tests having different parameters or different implementations. In practice, you'll want a mutation that frequently picks "small" mutations and occasionally picks "large" mutations. Just make sure that if the mutation function substitutes a test for an action, all the test's subbranches end in actions.

Certain things you'll want to telescope into this structure. Remember that in the handwritten stateless-ai's I had a test to "flight check" every action. In this structure, you don't want the flight checks to ever be separated from the actions, so you'd make all actions have a "left child" which could be another test or another action. The child node would be executed if the specified action couldn't be performed. Now, that implies that these trees are infinitely deep, which is not actually possible. So you'll need some "default behavior" that happens when they go off the bottom of the tree. You can go simplistic and pick "wait," relying on the GA to develop every bit of the smarts on its own, or you can use the GA to amplify handcoding by just having the "default" behavior be executing the handwritten (and guaranteed terminating) stateless-AI that you're trying to improve.

So, we have a gene mapping and a combination function. I'd make a decision that the "individuals" never got more than 1024 nodes (for animals) or more than 4096 nodes (for "intelligent" monsters) in them and that the index number of any node simply is not allowed to exceed MAXINT. I'd include code in the branch-swap function to truncate any branch that wanted an index number higher than that. I'd pick a population size around 150, a "normal" or "small" rate of elitism, restrict the range of tests and behaviors to things I considered "appropriate and possible" for that monster type, and then I'd be looking for a fitness function.

Fitness functions are hard to find in RPG game opponents. Sad but true, the fate of most monsters in a roguelike game is to be on screen for about twenty seconds and then get slaughtered. Now, you could take "survival time" as your fitness criterion, but all you would teach your monsters is to be very good at running away. If they got good enough at it, the player would never even see them. And that's probably not what you want. You could take "hits of damage inflicted on the player" as the measure of monster worth, but you run into another problem in that most monsters don't inflict any damage at all, and some of the most fun ones do their stuff in a form other than physical injuries.

About the best you can do before we cover modeling the player in the fourth section of this series of articles is a fitness function that evaluates, over a monster's lifetime, what it has cost the player. A few hitpoints? One- tenth of a charge off a fireball wand? Six charges from a rod of illumination? A round of sword swinging? A speed potion? Food and time spent digging? Whatever it is, you have to assign credit for getting the player to use it to one or more monsters, and then try to decide its value to the player. Remember that the last hitpoint has infinite value and the first has a very small value, so the monster who hits a player for 4 damage when he's low on HP should get more rewarded than the monster who hit for 4 damage when he was full up. Add it all up and assume that the monster who cost the player the most was probably the one with the best AI. You'd need to build this directly into your combat code; having selected the next three "kobold AI's" to test in its GA, the system would have to wait and use them in the next three kobolds the player meets and see which ones did better or worse.

Since there'd be a huge level of variance due to just plain luck, I'd use a very high degree of elitism on the principle that I'm just compensating for getting noisy measurements anyway.//

But, the real-time test of interacting with the player simply doesn't conclude enough experiments fast enough to make a GA approach work really well. You could do it, but it would take a lot of games to develop good monster AI's. A "nice" fitness function, ideally, should be something you can calculate without player input, so you can leave the GA chewing on a problem overnight (or for a week) and see what it comes up with, instead of waiting for hundreds of games by dozens of playtesters, per run, to provide the data.

Basically, a genetic algorithm allows you to say what goals to try to achieve without specifying the solution -- but you have to be able to state those goals, and usually the goals are hard to precisely define. What normally happens is that the first six or eight runs show you what's wrong with the ways you've formulated your fitness function by "raping" it in various ways with the simplest (and least interesting) possible behavior that gets a good fitness score in a way you didn't think of. After that you start getting results that point out subtle errors in your conception of how the monsters' actions and tests worked, or point out exploitable bugs in your combat code. And once you work through all of those, you'd start getting usable, novel results and you start runs where you do a lot of tweaking of your GA parameters such as mutation rate, elitism, etc. A lot of GA runs will result in things that are pathetic, silly, or too embarrassing to talk about.

But don't despair; there is a worthwhile fitness function that can be successfully applied to a roguelike game monster AI. I'll explain it, but we can't actually do it until after part 4, Modeling the Player.

The only worthwhile approach to a fitness function for monsters is run plus status evaluation. Basically, this takes a monster and its situation and tries to project it some number of rounds into the future with different AI's. At the end of that time, the status of the different "virtual monsters" (one for each AI) is evaluated, and the one with the most improved (or least degraded) status is regarded as the best for purposes of this test. It's much easier to write a status evaluation function than it is to write an AI evaluation function, so this is a method of leveraging what you can do relatively easily to get what is much much harder to do. The big problem with this approach is, since you're mainly interested in situations that involve the player, this requires you to guess what the player is going to do for the next few rounds. And that is hard.

## Evolving State Machine AIs

---

Last time, I introduced genetic algorithms and gave a brief guide to troubleshooting them, then presented some of the issues involved in using a genetic algorithm for evolving stateless AI's of the type covered in article 1. This article is about evolving state machine AI's of the type introduced in article 2, and a little bit about the limitations of genetic algorithms.

A state machine AI is, fundamentally, a state transition table. You can think of it as a 2-dimensional grid where inputs are mapped against states and each mapping gives a transition to another state.

The "intelligence" of state machine AI's is bound up in three things: The transition table itself, the intelligence of the stateless AI that's used in each state, and the decisions about when to give what input.

In my earlier article on state machine AI's, I focused on the state transition table: It's central to the idea itself. I limited the input signals that could happen in a given state by giving the monster different sensory capabilities, or a different "observe" function to call in each state. However, within the limits of what could be observed, I was hand-coding when the system decided to issue a state transition signal.

Now, when you're doing things by hand, you have a clear idea of what you want the monster to do and the state machine, including the stateless AI's, the transition table, and the decisions about input, follows from that. But if you're evolving monster behavior instead of hand-coding it, you'll have to take another look at how to decide when to issue a state-machine input.

You have to look at the inputs available from the dungeon and from the monster's perceptive power at the moment, and you have to pick which of several signals to issue.... wait a minute. Isn't that basically the same thing you were doing with a stateless AI? It is, isn't it? With a stateless AI, you built a tree of decision points and input parameters, with the leaf nodes leading to actions. Deciding which state machine inputs to turn on is exactly the same except that the leaf nodes are signals instead of actions. So you can use the same structure and method now, building a tree of decision points where the action is picking one or more a state machine input signals to turn on.

We've already got a good method for combining "genetic" material from decision trees; we just use branch swapping, the same technique we use for the stateless-AI of each state.

But whate about the structure they'll be controlling? That pretty much has to co-evolve with the controls. what remains is the transition table itself.

But tables are easy to evolve in a genetic algorithm; this is just an array, so we set up a fixed-size block in the genome for it. Each state invokes a stateless-AI with a given set of parameters: probabilities, thresholds, and default inputs.

In the state transition table we have the following: For each state, there is a designated stateless-AI to use, and a set of parameters for it designating its behaviors and tests from the set available to the monster. Each state also contains a set of tests (again from those available to the monster) to use as parameters for the input-selector. For each state, for each input, there is a datum naming a state and a function that gives a probability of transition. I'd recommend that these functions be limited; you could use a constant, or use the output of one of the monster's available tests, or a simple function of a test and a constant or a simple function of two tests.

And, based on these analyses and assumptions, we're ready to make a gene map, a combination function, and a mutator for our state machine AI.

To make a gene map, we're going to have to make some decisions in advance about the morphology of the state machine; For example we can say a priori that it has sixteen inputs and sixteen states. That means our genome needs one input-picker, 16 stateless-AI-with-parameter sets, 16 sets of parameters for the input-picker, and 256 transitions with transition probability functions (see why I advocated keeping them small and limited?).

So, assuming we develop the stateless-AI's in a separate step, and allow a thousand nodes (each about two words) for the input decision tree, a hundred words per state for stateless-AI parameters, and four coded words per transition function, we get a "genome" of about 8 thousand words, or 32 kilobytes per individual. The combination function will use branch swapping for the tree, crossover within each stateless-AI parameter set, and crossover within the table. On the principle of storing things whose influence on fitness depends on each other close together, the table will be in row-major order with the stateless-AI parameter set in the middle of each row (so that a state's state transitions remain as close to its behavior definition as possible).

32 kilobytes is very long, so you won't be able to support a huge population on a given machine. You have to have a reasonable sized population (at least 50 to 100 individuals) or it just won't work. In order to work with genomes this size and a small population, you need to use very low mutation rates and a very low degree of elitism. And one of two other things. Either you need a whole lot of patience (reconciliation to runtimes of a few weeks or longer) or you need a cluster.

Remember the fitness test we used with the stateless AI's? Basically, it amounted to this; considering the moves that each of the candidate AI's would make in a given situation, which virtual monster would have wound up better off? With state-machine AI's there is one additional complication; we have to know what state the monster is initially in. But given that additional information as part of the "situation", it becomes the same problem as evolving stateless AI's.

The "goodness" function for the situation can be the same one you wrote when you were evaluating stateless AI's; just take every possible thing you can think of, decide how "good" or "bad" it is according to this monster's goals in life, and combine them to get a total score. The difficulty, as usual, is projecting ahead.

If the "goodness" function is very good - if it takes everything your monster cares about into account and measures its situation realistically, then you don't have to project it more than one turn into the future, and you can do that without necessarily simulating the player. The "best" state-machine AI, in that case, is the one that most often produces the highest "goodness" score available from any move.

This brings up an important point; what we've been trying to achieve so far is stateless and state-machine AI's, because they are fast and unambiguous ways to decide what to do. There may be scores, or hundreds of actions available to a given monster at a given moment, and we've been trying to avoid the need to "try and test" each and every one and see what kind of goodness score we get from doing it. That kind of test is acceptable in _evolving_ a state machine, in order to "grade" state machine quality by how closely or how frequently the state machine matches it in fitness; but so far, we've been assuming that it's more CPU horsepower than we want to do for each and every monster at runtime. The whole point of the GA is to use the expensive function (projection plus measuring goodness of the situation) to evolve a cheap-to-compute function that gives us results as good.

If on the other hand you decide that it is acceptable to spend the compute time at runtime, and you don't want to sit around for weeks while a genetic algorithm converges, then by all means throw out the state machine and substitute the function that projects the results of actions and decides which resulting situation is best. This will make the AI in your game a lot "heavier" in terms of CPU, but for most "reasonable" goodness functions it can still be managed. And in fact it's the beginning of a reasonable approach for modeling the player, so we'll get to it in article 4.

The limitations of Genetic algorithms are several; first, they take a whole lot of computing power to arrive at a conclusion (remember, "patience or a cluster"). They're finicky and often mysterious and usually multiple runs are needed as the earlier runs point out problems with the way you set up the problem or with the parameters you're running the GA itself with (this is a motif you see a lot of with AI code). They require you to set up the gene map and say how it will be interpreted (which forces you to make decisions about, for example, number of inputs and number of states, whether or not you have a very clear idea what the results of those decisions are.

GA's produce systems that are usually incomprehensible and always at least nine or ten times more complicated than they need to be; google for "Emergence of Introns" in genetic algorithms if you want the long version of the story, but the short version of the story is there's got to be a bunch of apparently-useless complication in there before the evolutionary process really starts to work. We can expect introns (complex structures that serve no useful purpose in the end-result but which serve to make evolution via our mutation and combination functions easier and less risky) in our decision trees and in our stateless-ai's, and if we don't hand-optimize them, which is substantial work, we can expect that the result will spend about 3x as long as necessary to run. We can expect introns in our state transition table; look for sets of redundant states that clearly serve the same purpose/produce the same behavior and often transition from each to the other wildly. It will still be ten times as fast as the try-all-possibilities and score the results of each method, but if you hand-tweak it to get rid of introns, you can make it both faster and more comprehensible.

And anyway, that, finally, is getting close to all we can do with state machines and stateless AI's.

Trying to project a "goodness" function more than one turn into the future, of course, brings us up against the same hard rock that we ran into when we were developing stateless-AI's; in order to do this well, we are going to need something that simulates the player. And that is hard.

### A note about clusters and small-scale distributed computing

When I say "cluster" I don't really mean one of those amazing special-built clusters and distributed computing infrastructures, although one of them could certainly be used to advantage. Instead, I would advocate building a tiny console program to run the Genetic Algorithm as fast as possible, and, while running it, once a minute or so, submit an HTTP request containing a few genomes, and get an HTTP response containing a few other genomes. The server runs a simple script that responds to each request with a random set of a few genomes it's recieved in the last hundred requests it handled. Exchanging a few genomes now and then is really all that the system needs to keep the populations connected and allow examples of new genes and complexes developed elsewhere to drift between them; it's analogous to an "island chain" where a few individuals drift between islands occasionally.

Once you've got your GA program and your server script, find a LAN where you can set it up and run it. You can do it at work if you get permission first; you can either ask your coworkers to run your little console program and plug in a laptop to act as the server, or you can take over the office LAN while the office workers are away for Christmas vacation, or whatever. Don't do this without permission at a job you want to keep. A good relationship with coworkers and boss is needed. Or, you can just hook up all the computers in your house and wait for it to work.
