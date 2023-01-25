# The Incredible Power of Dijkstra Maps

---

This article describes a few simple, mechanical techniques that do not involve any hand-waving or complicated state machines, but that can nevertheless give rise to behaviour that is eerie in its seeming intelligence. I came up with these while developing [Brogue](brogue.md).

I don't know if there is a better name for these things. I call them Dijkstra maps because of their similarity to the basic Dijkstra pathfinding algorithm, but rather than conducting a strict breadth-first search from a single point or set of points, I mean doing the following:

To get a Dijkstra map, you start with an integer array representing your map, with some set of goal cells set to zero and all the rest set to a very high number. Iterate through the map's "floor" cells -- skip the impassable wall cells. If any floor tile has a value greater than 1 regarding to its lowest-value floor neighbour (in a cardinal direction - i.e. up, down, left or right; a cell next to the one we are checking), set it to be exactly 1 greater than its lowest value neighbor. Repeat until no changes are made. The resulting grid of numbers represents the number of steps that it will take to get from any given tile to the nearest goal.

The obvious application is pathfinding. Make a Dijkstra map around a single goal point, and then no matter where you're starting from, you can just "roll downhill" on the Dijkstra map and you will reach the goal in an optimal number of moves. Note that this 'rolling' works with both 4 and 8-way movement, though you might want to add additional code to ensure units don't clip through non-walkable cells if moving diagonally.

But there is SO MUCH MORE that these things can do. Here are the revelations I've had while making [Brogue](brogue.md):

## 1) Safety maps

---

Everyone likes monsters that know how to flee. The problem is that it's not easy to get them to flee intelligently. If they just run away from the player, they'll bang their heads against the nearest corner of the room that they start in. This happens even if you use a Dijkstra map and try to roll uphill instead of downhill. Solution: Start with a Dijkstra map calculated around goal points at the player's location and at any player allies from which your monsters will also want to flee. Treat monsters that haven't moved since last turn as obstacles. Multiply every value in the map by a negative number somewhere in the neighborhood of -1.2. Take the result and pass it back through the Dijkstra scanning function. Rolling downhill on the result (recalculated each time the player moves) will cause monsters to flee for the nearest door. If cornered, they will try to sprint past the player and escape. If you think of the amount of "safety" that a cell has as the distance between it and the player, the negative coefficient determines how much the monsters will prefer a global safety maximum to a local safety maximum -- e.g. how close the player has to get to a cornered monster before the monster tries to make a break for it. Using more negative numbers (i.e. negative numbers with a greater absolute value) will also make monsters more likely to exploit large loops in the level geometry and "pillar dance" as the player chases it. You can have as many fleeing monsters as you like, and they can all share this same map so that it needs to be updated only once per turn.

## 2) Autoexplore

---

We've all watched the Angband Borg, right? And the [Crawl](linleys_dungeon_crawl.md) autoexplore feature? With these methods, you can add an autoexplore feature in 20 minutes. For the goal points, use every tile that the player has not discovered. Pretend that undiscovered wall tiles are really floor tiles. Run the scan every turn. When the player rolls downhill on this continually updated map, he will automatically blaze around the level until there are no more unexplored tiles that he can reach. Have the function stop whenever a message appears on the screen or whenever a previously unseen monster comes into view so that the player can use it without extreme risk. Want him to collect items along the way? Set items that the player has not yet stepped on to be goal points along with the undiscovered tiles. Now your player can skip through mindless exploration with a single keystroke and get control back the moment something interesting occurs. In fact, it's trivial to take this a step further: cut out all of the interruptions and have the player attack any monster adjacent to him. When there is no more exploration to be done, set the downstairs as a goal point. When that is the case and he is standing on the downstairs tile, have him use the stairs and continue on the next level. In less than an hour, I had a single keystroke that would make the computer start playing automatically -- collecting items and fighting bad guys and descending -- until interrupted or killed.

## 3) Dynamic routing

---

[Brogue](brogue.md) features Potions of Levitation, which cause the player to fly for a limited number of turns. It also features deadly lakes of lava that the player can fly over. It also features various automation features such as auto-travel and auto- explore that will plot routes based on what kinds of terrain are passable for the player. However, a levitating player can pass over lava, but he shouldn't get routed over a lava lake 20 spaces wide when his levitation had only 10 turns left. Nor do you want the player to wander out too far without realizing his error; much better to keep track of such details for him. What to do? Use a Dijkstra map, calculated once at level generation, to keep track of how many turns it takes to get out of a lava lake and back to solid ground from any given space in the lake -- just treat all land tiles as goals in the Dijkstra scan. If the number of turns to escape from the lake is equal to or one greater than the number of turns remaining in the levitation effect, a very stern warning pops up, and that interrupts auto-travel. To further constrain auto-travel and auto-explore, use a distance map calculated from the player's location as a way to determine how many turns it would take the player to reach each location. Then, if the player's levitation will have worn off before the player reaches a particular lava tile from his current location, mark that lava tile as impassible. If you calculate the auto-explore or auto-travel route with those tiles marked as impassible, you'll gracefully avoid taking the player over lava if (and only if) he doesn't have enough levitation to make it all the way across.

## 4) Desire-driven AI

---

Most of the things a monster will do involve moving toward something or moving away from it. Here are some examples:

- The player: approach or flee.
- Treasure: collect it.
- Fellow monsters: approach to aggregate into packs, flee to keep monsters wandering around by themselves.
- Food: Go to it and eat it.
- Water: Go to it and drink it.
- Sounds: Investigate them, or avoid them.
- Hive queen: Stay near her.
- Stealth: Prefer to be in cells that the player can't currently see.

Etc. Create one Dijkstra map for each of the above items, and update it every turn. (We also need another one if fleeing behavior is also relevant to that item -- see item (1) above for how to create a fleeing map). Now for each monster, keep track of how much it desires to be near that item; higher numbers indicate a desire to be near that item, zero indicates complete indifference, and negative numbers indicate a desire to be far away from that item. These numbers can change dynamically each turn. A monster's desire for food might steadily rise every turn and be set back to zero if the monster is on a tile that contains food. A monster that sees the player might start out indifferent but want to approach the player if it sees the player do something it doesn't like. Or if it sees the player reduce another monster to a smoldering crater with a single-syllable incantation, perhaps the monster will want very much not to be near the player and its number will go sharply negative. Each of these values should be set independently.

Now, the key insight is that Dijkstra maps can be multiplied by the strength of these desires and then added together. To calculate how much a monster desires to move in a given direction, take each desire, multiply the monster's coefficient for that desire by the value of the cell of the Dijkstra map that is in that direction, and sum over all desires. For positive desires, use the desire number as the coefficient; for negative numbers, turn the number positive and use it for the "fleeing" map of the same desire. Do this for each possible direction and let the monster move in the most desirable direction overall. With nine weighted sums, you will have a monster intelligently weighing conflicting ideas: "can't stay near the queen because the player is too powerful for that to be a useful fight, and while east is a slightly more efficient escape route, west will take me past water (I am very thirsty) and also toward another of my compatriots -- so I'll go west."

Want even more intelligence? Use more Dijkstra maps. Can a monster use wands but not swords? Instead of a single "items" map, have a separate "wands" map and "swords" map -- then you can have wizard monsters, tank monsters and gold-hoarding greedy monsters. The technique is scalable since (1) a Dijkstra map needs to be updated only when the underlying goal points change, so you need to recalculate the wands map only when a wand is picked up or dropped -- not every turn -- and (2) the Dijkstra maps will work for all monsters at the same time; adding more monsters to the map is cheap because each monster needs to keep track only of its desire coefficients, so even though monsters will have qualitatively different goals and behavior, there is no need to generate additional maps for each monster.

## Implementations

---

- [Javascript](javascript.md): [rot.js](http://ondras.github.io/rot.js/manual/#path)
- [Lua](lua.md): [rotLove](http://paulofmandown.github.io/rotLove/modules/ROT.DijkstraMap.html)
- [C#](c_sharp.md): [FloodSpill](https://github.com/azsdaja/FloodSpill-CSharp)
- [Go](go.md): [dmap](https://github.com/japanoise/dmap)
- [Python](python.md): [dijkstra-map](https://github.com/HenrYxZ/dijkstra-map)
