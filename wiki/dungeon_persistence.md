# Dungeon persistence

---

Dungeons may be:  

* Non-persistent, where a level is different every time the user returns to it through an entry point, such as a set of stairs.
* Persistent, where once a level is generated it is fixed, and its contents will not change randomly upon re-entry.
* Mostly persistent, which is a persistent dungeon that may be slowly repopulated with wandering monsters.
* One-way only, meaning that once you leave a level, there is no return.  

## Non-persistent dungeons

---

In early roguelike games, the non-persistence of dungeons had more to do with memory limitations than it did with enhancing gameplay. Thus dungeons in most new roguelikes are not of this variety ([Angband](angband.md) being a notable exception).  

* The player doesn't have to venture far to come upon a new scenario.
* Easy for player to [farm](cheating.md) previous levels for experience/treasure.
* Dungeon size issues are pretty much solved.  

## Persistent dungeons

---

Supporting persistent dungeons makes life a little more difficult for the developer since it requires the dungeon state to be loaded and saved between games and, very likely, every time the player moves from one level to the next.  

* Items remain in place (can drop stuff off and it will be there if you come back (see [Stashing](stash.md))).
* Modification of dungeon (such as digging tunnels or blowing up trees, etc.) is permanent.
* Realistic, in the limited sense that a fantasy dungeon setting can be realistic.
* Sense of accomplishment in exploring an entire level can be attained.
  * Complete exploration is encouraged, which may be good or bad, depending on design goals.
* Bleak, though safe, if a level has been permanently cleared.
* The player is stuck with whatever level is initially generated, even if it has some undesirable features, such as several [out of depth](out_of_depth.md) monsters near the entrance.  

## Mostly persistent

---

The most complicated type of dungeon from a developer perspective, a mostly persistent dungeon requires code that will determine at what frequency the dungeon should be re-populated, and also to handle the actual re-population.  

* Arguably more realistic than the other options.
* Makes traversing levels that have been previously cleared more interesting (or perhaps tedious).  

An excellent way to keep the terrain persistent, but not the items and monsters, is to use a specific random seed when generating the level.  

For example, if the level seed is 24, the terrain will always generate the same. Setting the seed as the dungeon floor works very well. To add more variety, have the world seed factor into the level seed. So if the world seed is 3090, then add +4 for the fourth level of the dungeon, and use the seed 3094. (note: this has been tested only in C)  

## One-way

---

Not often used in roguelikes, but a valid method nonetheless. An example of this type of "persistency," or lack thereof, is [Cogmind](cogmind.md).

* Evades the whole question of persistence.
* Can act as a gameplay factor. ("Do I avoid the brain-gobbler by popping down the stairs and miss out on half the level, or do I risk battle with a dangerous monster to avoid hurrying on?")
* Can help set the mood. ("There is no going back.")
