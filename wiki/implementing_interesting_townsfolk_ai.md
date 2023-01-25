# Implementing interesting townsfolk AI

---

```text
[Preface]

Hi, and welcome to this article about townfolk AI.

I'm Odin Jensen and I work on a roguelike multiplayer 3D game called
Curse of the Ancients. I have a small game programming company called
Nuke Software, which specializes in providing free game programming
tools for game programmers. We have libraries/tools for Win32, DirectX
and OpenGL. (If you want to know more check www.nukesoftware.dk)

As you all propably know, there's always your typical random jaywalkers
like the Blubbering idiot. This makes for some fun encounters and adds
to the credability of the town.

Only, what about the people who work in the shop? Why don't they go
home and sleep or walk to the nearby inn for lunch, thus keeping you
from buying that extra torches you need?

And that's exactly what this article is about. Adding life to your town.

[Prerequisites]

I assume knowledge of C.

(You can also just mail me at article_townai@nukesoftware.dk and ask :)

[What do we need?]

Well, we need to open up the shops.

Make them into rooms instead of big blocks with a door that magically
teleport you into the inventory list.

Below and image of a shop. (Notice the counter). The S represents the
shop keeper. A new command called T + &ltDIR> could be implemented for
talking. (You would ofcourse have to face the shop keeper and be within
2-3 tiles for him to hear you.) This could summon the regular shop
inventory list or you could make something more fancy. Up to you.

#############
#...#.......#
#..S#.......#
#...#........
#############

We also need to create townpeople as a new object type (Depends on
your implementation). A method to measure the current time of day is
also be needed. Now let's move some people.

(I'll use a single Inn keeper as an example)

[Moving the people using the pregenerated patterns AI technique]

To specify movement we'll generate patterns of N,S,E,W,H,X
N=Walk north, S=Walk south, E=Walk east,
W=Walk west, H=Stop, X=End of pattern.

(You can expand this to include open/lock doors, turn light on/off etc.
You get the idea.)

Lets make a simple example : (Normally you would keep all townfolks in
an array or linked list)

// Specify Inn keepers movement pattern when he goes to the inn for lunch.
unsigned char InnKeeper_GoToLunch[WALKPATT_MAXSIZE]={"NNSSEHWSSSWWWX"};

// Variable to keep track of Inn keepers current patttern position.
char InnKeeper_CurPattPos=0;

// Variable to keep track of what time the Inn keeper must go to lunch.
// We'll use a string for simplicity. (You could use strings for this.)
char InnKeeper_LunchTime[5]={"13:45"};

// Bool to keep track of if Inn keeper is going to lunch
int InnKeeper_GoingToLunch=0;

As you can see, this pattern would cause the Inn keeper to move
North x 2, South x 2, East, Wait one turn, West, South x 3, West x 3,
End pattern.

(HINT : Gather all this info in structs/objects for each town
 inhabitant. This is a simple sample, and therefore does not use
 the best techniques as optimized code has a tendency to be harder
 to understand.)

Here's a sample game loop to move the Inn keeper :

while (!EndGame)
{
// Check if time to got to lunch.
if (isTime(InnKeeper_LunchTime)) InnKeeper_GoingToLunch=1;

// Move Inkeeper if ok
if (InnKeeper_GoingToLunch)
{
// Move Inn keeper.
switch(InnKeeper_GoToLunch[InnKeeper_CurPattPos])
{
case 'N' : InnKeeperY--; break;
case 'S' : InnKeeperY++; break;
case 'E' : InnKeeperX++; break;
case 'W' : InnKeeperX--; break;
case 'H' : break;
case 'X' : {InnKeeper_CurPattPos=0;InnKeeper_GoingToLunch=0;};break;
}

// Advance pattern pointer (Index)
InnKeeper_CurPattPos++;
}

// Do your other game stuff.
DoOtherStuff();
} // End game loop

And now our Inn keeper is at the Inn for lunch. (Kinda wierd since he
owns the place, but whatever... :)

And that concludes my article. I really miss this feature in Roguelike
games, and I sincerely hope somebody will implement this in their
variant. (I know I'm going to). Please remember that this was only a
quick clarification on the subject. I hope you all understood it and
realize the potential for this technique. Mail comments/suggestions/bugs
to article_townai@nukesoftware.dk

Author : Odin Jensen
Company : Nuke Software
Roguelike : Curse of the Ancients
(Watch out for updates to this article...)
```
