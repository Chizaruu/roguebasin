# What a roguelike is

---

"What is a roguelike?" is a tricky question; it is tough to conceive a roguelike definition with which everybody will agree as this is a gaming genre that has evolved over time. As such, this article is not meant to define roguelikes, but merely to describe what they often have in common.  

Some of the most common elements in roguelikes are:  

* The [User Interface](../../user_interface.md): ASCII display of a tiled world has become a distinctive feature of roguelikes.
* The Game World: random world generation may be the most common feature of roguelike games. They usually provide little plot, and instead focus on creating unique layouts.
* The [Gameplay](../../../articles.md): turn-based gameplay and dungeon hack are most often proposed.  

Even among the "[major roguelikes](../../major_roguelikes.md)", it is not uncommon for one or several of the above guidelines to be broken, such as ASCII character display (Many offer a sprite mode and options.) or plotlessness. ([ADOM](../../../game/adom.md) is heavy on plot.)  

## User interface

---

* [ASCII](../../ascii.md) [character display](../../design/interface/user_interface_features.md): games use no sprites or wireframe models but rather a two-dimensional character grid viewed from above, in which each [character](character.md) represents an entity. For example, a human may be plotted as a '@', a dragon as a 'D', etc.
* Narrated action: short text descriptions are given for almost all game events except ordinary movement.
* Front-loaded commands: the player has knowledge of and access to all (or almost all) commands at the start of the game, often long before acquiring the objects or powers that make the command useful.
* Keyboard-based interaction: The keyboard is the traditional way to interact with the game world, as it provides the quickest way to access the several commands that a roguelike may have. There are certain [popular schemas](../../design/interface/preferred_key_controls.md) of keyboard usage.  

## Game world

---

* [Random](random_generation.md) world generation: some parts of the world in which the action is performed are generated using a random algorithm; this is made for the sake of replayability so that every gaming session is unique.
* [Spatial consistency](../../design/general/spatial_consistency.md): all the actions happen in a definite space. No warping to fight scenes or minigames on a different reality.
* Little [storyline](../../plot.md): a gripping plot is not typically the selling point of any roguelike. The story is usually kept to a minimum to enhance replayability.
* World interaction: few or no objects adorn the world; most of them have a use in the game.
* Setting: some of the common settings for the world include the personification of a [character](character.md) fighting whose way into a [dungeon](dungeon.md) and acquiring [items](items.md) via monster treasures or [town](../../town.md) supply. The world commonly has [magic](../../design/magic/magic.md) forces of different kinds to increase the possible interactions.  

## Gameplay

---

* Grid-based motion: all objects, monsters, players, terrain, dungeon features, etc. are restricted to a grid. The early roguelikes, being text-based, didn't have much choice regarding the grid, and grid-restricted play is considered a mandatory attribute of roguelikes out of tradition.
* [Permadeath](permadeath.md): once your character dies your savefile disappears; this encourages careful chosen tactics, cold sweat when fighting big baddies, and curses when your character dies, as well as a great sense of accomplishment in every victory.
* Freedom: the player may choose to do anything who wants in the game. There are no fixed plots; you can roam freely or look for the endgame goal.
* Turn-based: the time freezes in order to make the best of decisions when time comes.
* Dungeon hack: your goal is to kill [monsters](monster.md) and find powerful treasures in order to kill stronger ones and repeat the process.
* Tactical single-character play: the unit of action is based on the individual adventurer. The game is not twitch oriented (like Quake, rewarding reflexes and well trained actions) nor is it strategy oriented (like Civilization or Warcraft, requiring working on the large picture).  

## The Berlin Interpretation

---

The first International Roguelike Development Conference held in Berlin, Germany in 2008 addressed this question. The result of those discussions was the [Berlin Interpretation](../../berlin_interpretation.md).  

## External links

---

Many people have tried to define roguelike games. Here is a collection of links to pages that purport to do just that:  

* See [Roguelike Alphabet](../../design/general/roguelike_alphabet.md) for some other minor aspects of roguelikes, and how they relate to each other.
* [ADOM's](http://www.adom.de/adom/roguelike.php3) definition.
* [POWDER's](http://www.zincland.com/powder/index.php?pagename=about) definition.
* [Roguetemple's](https://blog.roguetemple.com/what-is-a-traditional-roguelike/) definition
* [Wikipedia's](http://en.wikipedia.org/wiki/Roguelike) definition.  
