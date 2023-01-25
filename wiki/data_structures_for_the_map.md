# Data structures for the map

---

```text
Is there a 'best way' to do this? I really don't want to waste time.
```

Definitely there's no 'best' way. It all depends on your preferences, the way your algorithms work, the way you draw the map on the screen, count [FOV](field_of_vision.md), determine next move for the monsters, etc.

I can tell you about my approach. There are 4 kinds of elements on the map:

- Terrain -- every square has one;
- Objects -- like doors, traps, etc., those are pretty rare on the map;
- Items -- these can be moved around and there can be many of them on a square;
- Monsters -- these will usually move around all the time, and there is only one per square most of the time;

Since every square has it's terrain, I store it in 2d array of ints, every int contains the index into the array of terrain kinds. You don't need to store the positions there -- you never need it at this level.

Now, the Objects, Items and Monsters are pretty sparse on the map, and on top of it will move or change it's state easily (open/close door). On top of that, you can have many of them per square. Storing them in the square directly is a waste.

So, there are lists of Objects, Items and Monsters on the given level. Every element of these lists must also store the position. Every time you want to know what items lie on given square, you have to iterate trough the list of all items and take the ones with proper position set. It might seem slow, but you won't have to do it often.

Now, to the operations you'll want to do on the map. Here's a short list:

- calculate [FOV](field_of_vision.md)
- display on the screen
- check whether movement is legal
- check items/objects/monsters around monster
- pick/drop items
- open/close doors (and generally change other objects)
- move monsters

It's not a full list, but these are the most common operations. You'll have to make them fast -- at least the ones that happen often.

Displaying the map on the screen seem to be important here, but it doesn't really occur that often. In fact, it's very rare -- it only occurs about once per player's turn -- provided you refresh whole screen every turn.

Now, [FOV](field_of_vision.md) and legality of movement are important. Calculating [FOV](field_of_vision.md) is already slow, and you don't want to slow it down further. Legality of move (is this square blocked) will be done often in your AI routines. You want them fast. But both of them depend not only on terrain, but also on the objects on the map. Looking up objects is slow, so you don't want to do it.

You can use a 2d array of flags. Again, if you use ints, you can fit more than enough flags there. For start, let's have HAS_OBJECT, HAS_MONSTER and HAS_ITEM flags. Then you don't have to do lookups for every square -- only for the squares that really contain something, and these are pretty rare. Every time you add/remove anything form the square, you update the flags.

But you want those operations to be even faster. [FOV](field_of_vision.md) code would still have to check the kind of object if there's one, and it's state (open doors don't block light, closed ones do). So, you can add more flags. BLOCKS_LIGHT, BLOCKS_MOVEMENT are probably a good choice. You have to update them on the square every time you open/close door, or tunnel trough wall, etc.

Now, your [FOV](field_of_vision.md) and path-finding only use this array of flags. If your monsters are supposed to be able to open door, you might consider adding an OPENABLE flag for the path-finder.

If your game is player-centric, you can also add IS_VISIBLE and IS_MAPPED, to mark which squares are in the player character's [FOV](field_of_vision.md) and which are remembered on his map. Monsters can use IS_VISIBLE to check whether they can see the player character.

Moving items and monsters, and modifying the objects is straightforward -- just change appropriate values in their objects. But you have to remember to update the flags both on the square you're moving from and on the one you're moving to.

Checking the monster environment has a trick to it. Normally you'd probably scan a part of map around the monster and check the items/objects/monsters there. This is bad with our structure, because you're doing multiple lookups. And you scan awful lot of empty squares, which is unnecessary.

Instead, just iterate trough the lists of items/monsters/objects (depending on what you're looking for) and select the ones that are close enough (calculate the distance using their stored positions) and are of the interesting kind (monsters will probably just ignore most of them). Then, for the items that are left, just calculate [LOS](line_of_sight.md) to see whether monster can see it. You can use IS_VISIBLE if the monster you're looking at is the player character, otherwise just draw a line and see whether it's blocked.

This approach is much faster than scanning even on large maps.

The last thing is displaying the map. You can have it done in variety of ways, I'll describe two of them.

The first one is similar to checking monster environment. First, scan the area to be displayed and show all the terrain (you can skip the squares that have HAS\_\* flag set for now). Then iterate trough the lists and put the missing elements. Check whether they are visible first (just look at their position and the square's flags). This procedure plays nicely with all sorts of 'detect monsters' spells and telepathy.

The other way is more traditional. Have yet another 2d array, in which you store the looks of the squares (again, probably just an index to the table of all possible looks). You update this array every time something changes on the square. Then you just display the array on the screen when needed. (ok, if you had no scrolling, you could use the screen directly, without the array).

Now, it's only an example of one possible way to do it. I hope you get what I mean -- you have to design your data structures with the operations you're going to do on them in your mind. Don't copy blindly what I described here -- just look on what you want to do and design your own approach (ok, if it comes up identically as mine, you can copy it ;) ).
