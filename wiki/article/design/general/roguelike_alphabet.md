# Roguelike Alphabet

---

A roguelike is a game which is inspired by other roguelikes. There are some very important features which are very important for being a roguelike, such as [permadeath](../../development/fundamentals/permadeath.md), randomness, [ASCII](../../ascii.md) graphics, and so on. However, there are also lots of other small things, which are very popular in roguelikes, but rare or not existant in other games. Here is the list of them in a alphabetic manner. Note how often big or small Roguelike features are logically linked to each other ---- a game with only few of these features would not make much sense.  

## ASCII Art

---

The authors of roguelikes concentrate on gameplay, and usually don't care about graphics and sound --- there is no sound at all, and the display is made in the simplest way possible. The original Rogue was made using [ASCII](../../ascii.md) characters to represent dungeon and monsters. Almost all classic roguelikes can also be played in this mode (although most of the popular ones also have a graphical mode too).  

## Bones of previous adventurers

---

In many roguelikes it is possible to meet ghosts of previous characters. For your new character, this has both good sides (you can find their equipment) and bad sides (you'll have to face whatever killed them and possibly the ghost themself). This happens in [NetHack](../../../game/nethack.md), [Ragnarok](../../../game/ragnarok.md), [Adom](../../../game/adom.md) (Steam version) and [IVAN](../../../game/ivan.md) (in IVAN you can even resurrect your old character and have him as a companion), and there are also simpler version of this feature in [Crawl](../../../game/linleys_dungeon_crawl.md) and some [Angband](../../../game/angband.md) variants. This feature now exists in some survilal horror games that have [Permadeath](../../development/fundamentals/permadeath.md).  

## Confusion

---

In all classic roguelikes it is possible to become confused, which means that the character picks directions randomly for some time. Yet another small thing which was present in [Rogue](../../../game/rogue.md) and has been inherited by many other roguelikes. Another similar such thing is hallucination, which makes the character see everything differently (e.g. monsters as different monsters).  

## Dungeon crawling

---

In all the classic roguelikes the game takes part in dungeons of some sort. However, in the modern roguelikes the area is usually more varied.  

## Ego Items

---

Items not only have an "item class" (like long sword), but they also can be of varying qualities. At the simplest level, we can have enchanted/high quality weapons and armor like +1 mace or +2,+0 mace. In many games, potions and scrolls can have blessed, uncursed or cursed status. Also, normal weapons (or other things) can get magical (or equivalent) prefixes or suffixes, like of orc slaying. In some new roguelikes things can be made of various materials. This is strongly linked to **Randomness**, and **Identification** (you have to identify the special qualities).  

## Free Software

---

Roguelikes are usually free. Often they are also open source. This is quite an important part of the roguelike culture.  

## Grid of Squares

---

Not only **time**, but also the space in a roguelike is not continuous, but rather divided into squares (hexes would probably also be good, but no classic roguelike uses them yet). On each square only one monster can stand. Also, most roguelikes have spatial consistency: for example, when entering a house you are not moving to another map, but rather this house is a part of the old map, and you can for example shoot your enemies outside from inside the house. (However, if the action takes part in a large area, usually there is a single overworld map which is played differently. Also, usually using stairs moves you to a different location, however some new games experiment with this.)  

## Hints

---

Most classic roguelikes ([NetHack](../../../game/nethack.md), [ADOM](../../../game/adom.md), [Angband](../../../game/angband.md)) have [fortune cookies](../../fortune_cookie.md) or another equivalent feature. This is an item (or another feature) which displays a random hint when used.  

## Identification

---

In all the classic roguelikes, the names of potions, scrolls, wands, jewelry etc. are shuffled. You need a method of identification before being able to safely use them. Because of randomness you have to identify things in each game.  

## Jewelry

---

You can wear things such as rings and amulets, which affect some properties of your character. In SF settings this might be brain implants or mutations, but the idea remains the same.  

## Key-driven Interface

---

Roguelikes have key-driven interface, which means that you have to press keys in order to execute commands. For example, to read a scroll, you have to press 'r', and then select the scroll you want to read (all items in your inventory, spells, etc. have assigned letters; many roguelikes are written in such a way that these letter usually do not change during the game). This is very convenient for experienced players. Of course, for newbie roguelike players it would be nice to have both key-driven and WIMP interface.  

## Lots of kinds of monsters/items/other things

---

Most classical roguelikes have hundreds of monsters and items. Because of **Randomness**, you do not always find all of them in each game. This may be linked with the fact that roguelikes have only **ASCII graphics** --- after all, to add the new monster to an ASCII game, you just have to type its name and stats in some kind of a data file, and implement its special properties (if any). Also, often the roguelikes increase replayability by allowing the player to choose their race, class and maybe some other properties at the beginning of the game. The gameplay might be different with another race or class.  

## Messages

---

The game interacts with players by writing lots of messages to them, for example _You hit the kobold. The kobold hits you.._  

## Not Multi-Player

---

Roguelikes are generally designed to be played by a single player. This does not mean that multiple players playing on the same game never interact --- they can share **Bones files** and Highscore lists (X). Of course, some people do experiment with full multiplayer features. However, since the roguelikes are **turn-based**, and turns pass quickly, this is hard to do.  

## One Player Character

---

In almost all roguelikes the player controls a single character, not a party. However, often it is possible for this character to have friends and servants.  

## Permanent Death

---

[Permadeath](../../development/fundamentals/permadeath.md) is a very important feature. Whatever bad happens in the game, it has happened, and you cannot reload it to the previous state. While Permadeath itself takes only one point in this list, many other points are strongly dependent on Permadeath.  

## Quaff, Read, Zap as different commands

---

In the Rogue tradition, we have different commands (keys) for Reading scrolls, Zapping wands, Quaffing potions, etc, rather than a single "use" command.  

## Randomness

---

In the classic roguelikes, the details of maps (where are the corridors, what items and special features you will find, what monster you will fight) are randomly generated each game. Even though **Permadeath** forces you to start a new game, this new game often will be very different due to randomness. In modern roguelikes this may be extended to randomly generated world map, or maybe even to random story or randomly generated classes of monsters.  

## Secret Doors & Traps

---

All classical roguelikes have secret doors and traps. And, because of **Permadeath** and **Randomness**, traps being a secret really makes sense.
  
## Turn-Based Tactical Gameplay

---

Roguelikes are turn-based, not real-time. And this is an important feature.  

## Using RPG elements

---

This is quite obvious. Roguelikes use RPG elements such as stats and experience level. Some people view roguelikes as a subgenre of cRPG (computer role-playing games), but due to the importance of resource management, **turn-based tactical gameplay**, and **permadeath** (you play to win, not necessarily play a role), it is also possible to view them rather as strategy games posing as RPG.  

## Various Item Interactions

---

In many roguelikes items/monsters can interact in some strange ways. It is possible that an item has some kind of a hidden other use, which is not obvious from its description. Some of these uses are revealed by **hints** like fortune cookies. Also see the next point **(wield)** for examples.  

## Wield anything as a weapon

---

In many roguelikes you can wield or throw anything. This feature is never seen in other games. Of course, it is nice if it is actually useful. For example, you can wield a dead petrifying monster and use it as a weapon, or throw potions to have them affect your enemies.  

## Extended High Score Lists

---

A high score table which lists 100 entries, which include death causes, is a roguelike tradition. This makes much more sense with Permadeath (without **Permadeath** you basically win all games... and usually there is only one of them), **Randomness** (because each character is unique), and **YASD**.  

## Yet Another Stupid Death

---

In many roguelikes there are lots of ways to stupidly die. Roguelike players often share the stories about how their character has died due to bad luck or too much self-confidence on the player's part. This is related to **Permadeath**, as none would care about that if death was not permanent.  

## Zero hardware requirements

---

Since roguelikes concentrate on gameplay and not on graphics (**ASCII**), you usually do not need the newest video card or processor to play them.
