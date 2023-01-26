# Rot.js tutorial, part 2

---

This is the second part of a [rot.js tutorial](rotjs_tutorial.md).

## The Player Character

---

Time to make some interesting interactive shinies! First, the player needs a decent representation. It would be sufficient to use a plain JS object to represent the player, but it is generally more robust to define the player via its constructor function and instantialize it.

By this time, you probably got used to the fact that some variable names start with an underscore. This is a relatively common technique of marking them private. JavaScript does not offer true private variables, so this underscore-based nomenclature is just our useful way of marking stuff as "internal".

We would like to place the player on some spare floor tile: let's use exactly the same technique we used in Part 1 of this tutorial to place the boxes: just pick one free location from our list.

```javascript
var Player = function (x, y) {
  this._x = x;
  this._y = y;
  this._draw();
};

Player.prototype._draw = function () {
  Game.display.draw(this._x, this._y, "@", "#ff0");
};

Game.player = null;

Game._generateMap = function () {
  /* ...previous stuff... */
  this._createPlayer(freeCells);
};

Game._createPlayer = function (freeCells) {
  var index = Math.floor(ROT.RNG.getUniform() * freeCells.length);
  var key = freeCells.splice(index, 1)[0];
  var parts = key.split(",");
  var x = parseInt(parts[0]);
  var y = parseInt(parts[1]);
  this.player = new Player(x, y);
};
```

## Preparing the game turn engine

---

There will be two entities taking turns in our game: the Player Character and Pedro (The Enemy). To make things simple, let's just alternate their turns evenly. But even in this simple case, we can use the ROT.Engine timing framework to our advantage.

How does this work? First, the `ROT.Scheduler.Simple` will be fed with all available actors - this component will take care about fair turn scheduling. The `ROT.Engine` will then use this scheduler to automatically call relevant actors in a loop. (Note: we pass `true` as a second argument to `scheduler.add` - this means that our actor is not a one-shot event, but rather a recurring item.)

It is very important to embrace the fact that everything is asynchronous in the world of client-side JavaScript: there are basically no blocking calls. This eliminates the possibility of having a simple while loop as our main timing/scheduling instrument. Fortunately, the `ROT.Engine` is well prepared for this.

Creating the scheduler and the engine is just a matter of adding a few lines to our code:

```javascript
Game.engine = null;

Game.init = function () {
  var scheduler = new ROT.Scheduler.Simple();
  scheduler.add(this.player, true);
  this.engine = new ROT.Engine(scheduler);
  this.engine.start();
};
```

## Interaction between actors and the engine

---

There is a tight symbiotic relationship between the engine and its actors. When running, the engine repeatedly picks a proper actor (using the scheduler) and calls the actor's `act()` method. Actors are allowed to interrupt this loop (when waiting asynchronously, for example) by calling `ROT.Engine::lock` and resume it (`ROT.Engine::unlock`).

It is possible to have multiple lock levels (the lock is recursive); this allows for complex chaining of asynchronous calls. Fortunately, this won't be needed in our simple game.

So, what is an actor? Any JS object with the `act` method.

```javascript
Player.prototype.act = function () {
  Game.engine.lock();
  /* wait for user input; do stuff when user hits a key */
  window.addEventListener("keydown", this);
};

Player.prototype.handleEvent = function (e) {
  /* process user input */
};
```

We are using somewhat uncommon (but very useful!) technique of assigning event handlers: we pass a JS object as a second argument to the `addEventListener` call. Such object (`this`, in this case) must have the `handleEvent` method, which will be called once the event ("keydown") occurs.

## Working with the keyboard and moving the player around

---

There is one last bit remaining to implement: detect the pressed key, decide whether it is valid and move the player accordingly.

Our event handler (`handleEvent`) gets executed with one argument: the Event object. Its `keyCode` property is a number code of the key being pressed. Let's create a mapping of allowed key codes (this code sample uses numpad keys, but it is trivial to extend it to other layouts as well):

```javascript
var keyMap = {};
keyMap[38] = 0;
keyMap[33] = 1;
keyMap[39] = 2;
keyMap[34] = 3;
keyMap[40] = 4;
keyMap[35] = 5;
keyMap[37] = 6;
keyMap[36] = 7;
```

Numeric values are not chosen randomly: they correspond to directional constants in `rot.js` (8-topology, clockwise, starting at the top - the same as CSS does).

We need to perform a two-step validation of user input:

- If the key code is not present in `keyMap`, the user pressed a key which we cannot handle
- If the key code is present, we need to check whether the PC can move in that direction

To convert a directional constant (0..7) to a map coordinates, we can use the `ROT.DIRS` set of topological diffs:

```javascript
Player.prototype.handleEvent = function (e) {
  var keyMap = {};
  keyMap[38] = 0;
  keyMap[33] = 1;
  keyMap[39] = 2;
  keyMap[34] = 3;
  keyMap[40] = 4;
  keyMap[35] = 5;
  keyMap[37] = 6;
  keyMap[36] = 7;

  var code = e.keyCode;

  if (!(code in keyMap)) {
    return;
  }

  var diff = ROT.DIRS[8][keyMap[code]];
  var newX = this._x + diff[0];
  var newY = this._y + diff[1];

  var newKey = newX + "," + newY;
  if (!(newKey in Game.map)) {
    return;
  } /* cannot move in this direction */
};
```

The actual move is performed in two steps - redrawing the old position and redrawing the new position. After that, we remove our keyboard listener (the turn has ended!) and - importantly - resume the game engine (`unlock()`).

```javascript
Player.prototype.handleEvent = function (e) {
  /* ...previous stuff... */

  Game.display.draw(this._x, this._y, Game.map[this._x + "," + this._y]);
  this._x = newX;
  this._y = newY;
  this._draw();
  window.removeEventListener("keydown", this);
  Game.engine.unlock();
};
```

And that's all for part 2. The whole working code is available at [jsfiddle.net](http://jsfiddle.net/rotjs/CZ8YJ/). Feel free to continue in [Rot.js tutorial, part 3](rotjs_tutorial_part_3.md).
