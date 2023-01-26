# Rot.js tutorial, part 1

---

This is the first part of a [rot.js tutorial](rotjs_tutorial.md).

## Setting up the development environment

---

Our game is played within a web page; a rudimentary HTML file should be sufficient.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Ananas aus Caracas: rot.js tutorial game</title>
    <script src="https://cdn.jsdelivr.net/npm/rot-js@2/dist/rot.js"></script>
    <script src="/path/to/the/game.js"></script>
  </head>
  <body onload="Game.init()">
    <h1>Ananas aus Caracas</h1>
  </body>
</html>
```

We are going to put all the game code in one file, to maintain simplicity (when making larger games, it is far more useful to split the code across several files). We do not want to pollute the global name space with our variables; that's why we wrap all our code in an object named "Game".

```javascript
var Game = {
  init: function () {},
};
```

## Console output: ROT.Display

---

Being a JS app, our game can modify the HTML page in many ways. However, rot.js encourages only one kind of output: printing to its "tty console", which is represented by a HTML <canvas> tag. In order to draw anything, we first need to create this console and store it for later usage.

```javascript
var Game = {
  display: null,

  init: function () {
    this.display = new ROT.Display();
    document.body.appendChild(this.display.getContainer());
  },
};
```

Note that this console has a default size of 80x25 cells; if we wanted different default dimensions, we would configure them via `ROT.DEFAULT_WIDTH` and `ROT.DEFAULT_HEIGHT`.

## Generating a dungeon map

---

We will use one of rot.js's built-in map generators to create the game level. One of the design paradigms of rot.js is that people should not be forced to use some pre-defined data structures; this is why the generator is callback-based. We will pass our custom function to the generator; it will get called repeatedly during the process.

This might be a good time to check out the [rot.js manual](http://ondras.github.com/rot.js/manual/), which contains useful code samples and usage overview.

How should we store the resulting map data? We will use a very basic method of storage: an ordinary JS object ("hashmap"), indexed by strings (having the format "x,y"), values representing floor tiles. We are not going to store wall / solid cells.

NOTE: We are passing `digCallback.bind(this)` instead of just `digCallback` to the Digger. This is necessary to ensure that our callback is called within a correct context (_activation object_ in ECMA parlance).

```javascript
Game.map = {};
Game._generateMap = function () {
  var digger = new ROT.Map.Digger();

  var digCallback = function (x, y, value) {
    if (value) {
      return;
    } /* do not store walls */

    var key = x + "," + y;
    this.map[key] = ".";
  };
  digger.create(digCallback.bind(this));
};
```

We still cannot see anything, because we have not written a single character to the display yet. Time to fix this: iterate through all the floor tiles and draw their visual representation.

```javascript
Game._drawWholeMap = function () {
  for (var key in this.map) {
    var parts = key.split(",");
    var x = parseInt(parts[0]);
    var y = parseInt(parts[1]);
    this.display.draw(x, y, this.map[key]);
  }
};
```

Now make some changes so that this code is used: call this.\_drawWholeMap() at the end of the function Game.\_generateMap, and call this.\_generateMap(); from the end of your init function. If it's still not working, have a look at the same code linked to from the bottom of this page.

## Randomly generated boxes

---

Finally, let's create some boxes - potential ananas storage. We will hide the ananas in one of them in later parts of this tutorial. To place 10 random boxes around, we will leverage rot.js's [Random number generator](random_number_generator.md).

`ROT.RNG` can do a lot of stuff, but we need something simple: a random, evenly distributed number between zero (inclusive) and one (exclusive), just like the `Math.random` does. The proper way of doing this is calling `ROT.RNG.getUniform()`.

We will store the empty cells in an array; for each box placed, we will pick a random empty cell, remove it from a list and mark that place as a box (asterisk) in our storage structure.

```javascript
Game._generateMap = function () {
  var digger = new ROT.Map.Digger();
  var freeCells = [];

  var digCallback = function (x, y, value) {
    if (value) {
      return;
    } /* do not store walls */

    var key = x + "," + y;
    freeCells.push(key);
    this.map[key] = ".";
  };
  digger.create(digCallback.bind(this));

  this._generateBoxes(freeCells);

  this._drawWholeMap();
};

Game._generateBoxes = function (freeCells) {
  for (var i = 0; i < 10; i++) {
    var index = Math.floor(ROT.RNG.getUniform() * freeCells.length);
    var key = freeCells.splice(index, 1)[0];
    this.map[key] = "*";
  }
};
```

And that's all for part 1. The whole working code is available at [jsfiddle.net](http://jsfiddle.net/rotjs/BmEwg/). Feel free to continue in [Rot.js tutorial, part 2](rotjs_tutorial_part_2.md).
