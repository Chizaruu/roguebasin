# Rot.js tutorial

---

## Ananas aus Caracas: a sample game built using rot.js

---

Welcome to this tutorial! We are going to create a very simple roguelike game, playable in any modern browser. To achieve this, our language of choice will be [JavaScript](javascript.md); to simplify our work, we are going to use the [rot.js toolkit](rot.js.md).

This tutorial covers creation of a game to be played in a web browser. [See here](http://github.com/blinkdog/ananas-aus-caracas-node) for a version of the game ported to Node.js so it can be played in a terminal emulator.

### Requirements

- Basic JavaScript knowledge
- Modern web browser (Firefox, Chrome, Opera, Safari, IE9+)

### Game setting

The tutorial consists of three chapters, which gradually add more and more functionality. The game itself is pretty simple: motivated by [this video](https://www.youtube.com/watch?v=z4dMkYUlrR8), a player needs to find an ananas (pineapple) hidden within several boxes in an underground dungeon. It is necessary to find the ananas before Pedro (the ananas owner) finds and punishes you for sneaking into his warehouse.

### Tutorial contents

**NOTE:** Working code samples from this tutorial are hosted at jsfiddle.net and use rot.js from GitHub. Please note that GitHub is **not** a CDN; you should use your own servers to host all the necessary files.

- [Part 1: Basics, map](rotjs_tutorial_part_1.md)

  Create the basic data structures, initialize the output area, generate and draw the map

- [Part 2: Asynchronous game engine, player character](rotjs_tutorial_part_2.md)

  Introduce the player character, add it to the engine/scheduler, let it move around

- [Part 3: Interaction, Pedro](rotjs_tutorial_part_3.md)

  Let the player open boxes (to make the game winnable); implement Pedro and his pathfinding (to make the game losable)
