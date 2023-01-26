# Rot.js tutorial, part 3

---

This is the third part of a [rot.js tutorial](rotjs_tutorial.md).

## Looking inside the box

---

The game generates several boxes, but so far, none of them contains the prized ananas. Let us store the ananas in the first box generated:

```javascript
Game.ananas = null;

Game._generateBoxes = function (freeCells) {
  for (var i = 0; i < 10; i++) {
    /* ...previous stuff... */
    if (!i) {
      this.ananas = key;
    } /* first box contains an ananas */
  }
};
```

Apart from moving, there is one more interaction a player must perform: looking into boxes. We will allow both Enter (keyCode 13) and Spacebar (keyCode 32) for this action:

```javascript
Player.prototype.handleEvent = function (e) {
  var code = e.keyCode;
  if (code == 13 || code == 32) {
    this._checkBox();
    return;
  }
};
```

Opening a box to verify its contents is as simple as comparing the player's current position with our list of boxes and the stored ananas position:

```javascript
Player.prototype._checkBox = function () {
  var key = this._x + "," + this._y;
  if (Game.map[key] != "*") {
    alert("There is no box here!");
  } else if (key == Game.ananas) {
    alert("Hooray! You found an ananas and won this game.");
    Game.engine.lock();
    window.removeEventListener("keydown", this);
  } else {
    alert("This box is empty :-(");
  }
};
```

## Pedro, the angry owner

---

The game is now winnable! Let's add a villain as a second actor. We will place him using the same algorithm we used previously. To do this, let's refactor the original `_createPlayer` method into a more useful parametrized factory `_createBeing` by passing a constructor function as an argument:

```javascript
var Pedro = function (x, y) {
  this._x = x;
  this._y = y;
  this._draw();
};

Pedro.prototype._draw = function () {
  Game.display.draw(this._x, this._y, "P", "red");
};

Game._createBeing = function (what, freeCells) {
  var index = Math.floor(ROT.RNG.getUniform() * freeCells.length);
  var key = freeCells.splice(index, 1)[0];
  var parts = key.split(",");
  var x = parseInt(parts[0]);
  var y = parseInt(parts[1]);
  return new what(x, y);
};

Game._generateMap = function () {
  /* ...previous stuff... */

  this.player = this._createBeing(Player, freeCells);
  this.pedro = this._createBeing(Pedro, freeCells);
};

Game.init = function () {
  /* ...previous stuff... */

  scheduler.add(this.player, true);
  scheduler.add(this.pedro, true);
};
```

This might be confusing to some, but passing functions around (as function arguments, for instance) is very common in JavaScript.

## Pathfinding-based AI

---

Pedro is missing its `act()` method so far. We are going to use one of rot.js's pathfinding functions to implement Pedro's behavior: `ROT.Path.AStar` (the A\* algorithm). Some rudimentary scaffolding is necessary:

1. The player must have public methods to read its position,
2. We need a _passableCallback_ function which tells the pathfinder what areas are passable,
3. We need a _pathCallback_ function, which will be called from within the pathfinder (to notify us about the shortest path found).

Moreover, to make Pedro somewhat weaker than player, we will use the pathfinder only in 4-topology.

```javascript
Player.prototype.getX = function () {
  return this._x;
};

Player.prototype.getY = function () {
  return this._y;
};

Pedro.prototype.act = function () {
  var x = Game.player.getX();
  var y = Game.player.getY();
  var passableCallback = function (x, y) {
    return x + "," + y in Game.map;
  };
  var astar = new ROT.Path.AStar(x, y, passableCallback, { topology: 4 });

  var path = [];
  var pathCallback = function (x, y) {
    path.push([x, y]);
  };
  astar.compute(this._x, this._y, pathCallback);
};
```

We now have the shortest path between Pedro and the player, stored in the `path` variable. Note that Pedro's current position is also part of the path; that's why we first discard the first item of our path. If the resulting path is only one-cell long, Pedro is standing close to the player and the game is over (player lost). Otherwise, we apply the same movement logic we used for the player in Part 2 of this tutorial.

```javascript
Pedro.prototype.act = function () {
  /* ...previous stuff... */

  path.shift(); /* remove Pedro's position */
  if (path.length == 1) {
    Game.engine.lock();
    alert("Game over - you were captured by Pedro!");
  } else {
    x = path[0][0];
    y = path[0][1];
    Game.display.draw(this._x, this._y, Game.map[this._x + "," + this._y]);
    this._x = x;
    this._y = y;
    this._draw();
  }
};
```

Ta-dah! The game is complete now; it is possible to win and lose. Some considerations for possible further improvements:

- Player can crash the game by moving onto Pedro's cell. Not only this is currently allowed, but it also disrupts Pedro's pathfinding (which expects the path to be at least two cells long).
- The `Game.map` structure should probably store positions of beings (player, Pedro) as well.
- It would be comfortable for users to increase the set of allowed navigation keys (number keys, vi keys).
- When a box is inspected, its appearance may change (to make it easier for player to distinguish between visited and unvisited boxes).

And that's all for part 3. The whole working code is available at [jsfiddle.net](http://jsfiddle.net/rotjs/qRnFY/).
