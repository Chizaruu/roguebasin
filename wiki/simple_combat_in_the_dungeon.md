# Simple Combat in the Dungeon

---

```text
Slimies Vs. Pointies -
[Note: C++ Class Definitions Will Be Used Throughout]

Section 1 : The Question

        One of the most defining aspects of a RL is its combat. Who can
say they don't feel the thrill of rapidly pounding a directional key until
that horrid mass of white Ws is but a scattering of corpses on the floor?
        The basics of combat are fairly simple, and below I'll summarize
some of the most important facets of a simple combat engine for a RL.

Section 2 : The Foundation

        Our combat system will be initiated when the player attempts to move
into a tile occupied by a monster, or a monster decides to attempt to eat
a nearby player.
        In designing our combat system, we need to take the player's and
the monster's statistics and abilities into account. One of the easiest
ways to do this is to combine a player's statistics, equipment and any other
magical bonuses he receives into two ratings, an Attack Rating (AR) and a
Defensive Rating (DR).
        We can then take a random roll (say 1-100) and add the attack rating,
if that number (AR+RandomRoll) is greater than the DR of the target, then the
hit deals damage, or saps energy, or turns the target into a plate of liver and
onions, or whatever other effect that hit might have. (See Section 3 : OW!)
        The hardest part of a system like this is the balancing of the
ratings... But then again, that's probably the hardest part of any game
design... ;)

        Example Of AR Calculation:

        AR = (Strength / 4)
             + A Modifier Based On Class & Level (More AR for Warrior-Types)
             + Any Magical Bonuses Granted By Special Equipment

        Example of DR Calculation:

        DR = (Dexterity * 4)
             + A Modifier Based On Class & Level (More DR for Thief-Types)
             + Any Magical Bonuses Granted By Special Equipment

        We might also allow higher-level characters or nasty monsters to hit
several times a round...

Section 3 : OW!

        Once the hit scores it's time to resolve the hit.

        The easiest way to represent health and wounds in a RL is a pool of
points that represents the damage a player or monster can take until they die.
Usually (thanks to D&D) these are referred to as Hit-Points (HP for short).
The player and all of the monsters will have some amount of Hit-Points.

(***Side Trek : Random Number Representation)

        Thanks to pen-and-paper RPGs (pNp), there is a common nomenclature for
representing a random number.
        If you haven't played or watched pNp RPGs, it might come as a surprise
that dice come in a number of different side-counts. There are 4,6,8,10,12,20,
and even 100-sided dice... Dice that often come in varying colors, shapes and
sizes.
        Usually pNp games represent a random roll as :
        XdY where:
        X = Number of Dice
        Y = Sides of the Dice... so:

        A Yahtzee roll is 5d6...
        A Monopoly roll 2d6...

        If we want to represent 3 eight-sided dice it would be 3d8,
        6 four-sided dice, 6d4... And so on...

        We might take advantage of this for our own purposes :

        class Die
        {
                public:
                void Roll( void ) { Number = 0; Type = 0; };
                void Roll( int x, int y ) { Number = x; Type = y; };

                int Number,Type;

                int Roll( void )
                {
                        int x;
                        int acc;

                        for( x=0;x&ltNumber;x++ )
                        {
                                acc += GetRandom(1,Type);
                        }

                        return acc;
                }
        };

(***End Side Trek : Random Number Representation)

        Each time a blow lands, that blow should subtract a number of HP based
on the weapon or creature that inflicts the damage...

        For Example:

        Weapons:
        Dagger          - 1d4 damage
        Sword           - 1d8 damage
        BIG Stick       - 2d6 damage

        Creatures:
        Kobold          - 1d3 damage
        Griffon         - 2d8 damage
        BIG Monkey      - 2 hits for 3d6 damage each


        That, in a nutshell, is a simple combat engine for a RL. The engine
itself is the easy part, balancing the system is what makes it fun!... heehee

The Author:
Brian Bucklew, 18
bbucklew@inteletek.com
Current RL Project : Dungeon or "This game needs a new name"... :)
Projected Beta Release : Early 98
```
