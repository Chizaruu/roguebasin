# Code design basics

---

Here is a list of things you want to have absolutely known, standard functionality for dealing with, in place before you do much of anything else.

I guess this is a list of pitfalls that I've seen forcing major redesign decisions; get them out of the way early and you can have a consistent design where adding content is pretty easy.

- Time: You must have cold solutions for these 4 problems, because a change in any of them will hork most of the code you've written before the change.

  - part 1, Determining whose turn it is.
  - part 2, Keeping track of future events and making them happen on schedule.
  - part 3, Handling effects with intervals denoted in absolute time, player turns, or monster turns.
  - part 4, Making sure things still work right when player or monsters can have their speed change or be frozen/reanimated in time.

- Space: You must have cold solutions for the first four of these "space" problems. You must have a cold solution for the fifth if your game is ever going to do it. Six and seven should have standard solutions, but can be swapped in and out usually without too much trouble or effect on the rest of your code. And eight is something you should have a standard method and pattern for doing, including adding new ways to do it.

  - part 1, Representation of your map.
  - part 2, Saving and restoration of your map.
  - part 3, Ranged effects, aiming, and obstructions.
  - part 4, Area effects and how they interact with restricted areas.
  - part 5, Player and/or monster gametime alterations of the map.
  - part 6, Planning and pathfinding.
  - part 7, Illumination/field of view/line of sight.
  - part 8, Dungeon generation.

- Items: You must have the following standard functions that can be applied to items, and this will influence how you can represent items.

  - part 1, Picking it up.
  - part 2, Putting it down.
  - part 3, Wearing and wielding.
  - part 4, Using as a weapon and/or ammo.
  - part 5, Eating and drinking.
  - part 6, Putting it in a container.
  - part 7, Using it as a container.
  - part 8, Attempting to break it (if you will allow that).
  - part 9, Transforming one thing into another.

- Writing code for new item/monster behavior (your infrastructure should have known, standard places already to put that code and known, standard ways to make sure calls to it happen at appropriate moments).

- Determining time, mana, fatigue, damage, etc. effects of doing things (and your infrastructure should have known, standard places already to put the values for amounts, and known, standard routines for actually inflicting the effects).

- Determining how items and monsters will be represented in live game data (without tangling up your references so pointers to an item or a monster exist at more than one place and one can be left dangling when it is killed/destroyed! And your infrastructure should already have known, standard places and methodologies for such representation, ideally facilitating save/restore without you having to do anything more).

- Determining how items and monsters will be represented in saved games (and your infrastructure should handle saving and restoring easily and well, ideally by traversing "standardized" data structures for items automatically).

Original version submitted to [rec.games.roguelike.development](rgrd_wiki_project.md) by **Ray Dillinger**
