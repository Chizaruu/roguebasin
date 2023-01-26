# Need-based AI in JavaScript

---

This article describes an implementation of a need-based AI system in JavaScript. It is somewhat similar to [Need driven AI](need_driven_ai.md).

## Basics

---

We are going to build the AI logic based on the famous [Hierarchy of needs](http://img.pandawhale.com/75824-Maslow-WiFi-4S8F.png). To keep the code clean, all stuff related to AI will be put into a separate (pluggable) JavaScript object "AI". Mixing this AI with a Being will be shown later.

First of all, let's define some "needs" our beings shall consider:

```javascript
this._needs = {
  survival: 1,
  health: 1,
  satiation: 1,
  revenge: 1,
};
```

Survival is the basic need to survive, i.e. to maintain at least a minimal amount of hitpoints. **Health** is a need to be healthy, to regenerate as many hitpoints as possible. **Satiation** represents the need to fight hunger. Finally, **Revenge** is a need to avenge any damage that was done to us.

These needs are initially set to **1**, meaning "satisfied". This article will only use **1** and **0** values (0 meaning unsatisfied), but for more complex scenarios, float values can be used to represent partially satisfied/unsatisfied needs.

## Observing need changes

---

The AI needs to do two major things: watch how the values influencing individual needs change and act accordingly. We can leverage the language used (JavaScript) to perform some major _monkey-patching_ (code surgery). Assuming that our `Being` object exposes the canonical damage and heal methods, the `AI` part can implement its own version of these functions:

```javascript
Being.prototype.heal = function () {
  /* a very basic healing that just maxes out the hitpoints */
  this._hp = 10;
};
AI.prototype.heal = function () {
  /* after a healing takes place, the AI no longer needs survival nor health */
  this._needs.survival = 1;
  this._needs.health = 1;
};

Being.prototype.damage = function (attacker) {
  this._hp--;
};
AI.prototype.damage = function (attacker) {
  /* the being took damage; the AI notes that "revenge" and "health" needs are now unsatisfied */
  this._needs.revenge = 0;
  this._needs.health = 0;

  /* if the HP are too low, the "survival" need is also unsatisfied */
  this._needs.survival = hitpoints < threshold;
};
```

With this in place, let's implant the AI code directly onto the being, creating a wrapper function that calls both the original and the AIfied method:

```javascript
var AI = function (being) {
  this._being = being;
  this._hook("heal");
  this._hook("damage");
};

/** Replace a given method of a being with our custom wrapper */
AI.prototype._hook = function (func) {
  var original = this._being[func];
  var ai = this;
  this._being[func] = function () {
    var result = original.apply(this, arguments);
    ai[func].apply(ai, arguments);
    return result;
  };
};
```

This approach is especially useful as it allows the original Being to exist without any knowledge of an AI. When an AI is created, it modifies the underlying being by adjusting all necessary methods.

## Acting

---

Our `Being` has a public `act` method, which gets called every time the being takes a turn. This method does not do much: it adjusts periodic counters (such as the hunger value), but does not actually perform any actions - the default `Being` is dumb. Let's hook into this method as well:

```javascript
var AI = function (being) {
  /* ... */
  this._hook("act");
};

AI.prototype.act = function () {
  /* observe changed values */
  if (this._being._hunger > 2) {
    this._needs.satiation = 0;
  }

  /* act! (we are paid for this...) */
};
```

How does the decision process look like? We need a prioritized list of needs, to know which of them must be satisfied before another. This list is used to find unsatisifed needs and to try satisfying them in a correct order:

```javascript
AI.prototype.act = function () {
  /* ... */
  var priorities = ["survival", "satiation", "revenge", "health"];

  for (var i = 0; i < priorities.length; i++) {
    var need = priorities[i];
    if (this._needs[need]) {
      continue;
    } /* already satisfied */
    if (this._satisfy(need)) {
      return;
    } /* managed to satisfy */
  }
};

AI.prototype._satisfy = function (need) {
  switch (need) {
    case "hunger":
      if (!haveFood) {
        return false; /* cannot satisfy */
      }
      this._being.eat();
      return true;
      break;

    case "survival":
      if (haveHealing) {
        this._being.heal();
      } else {
        /* move away from the danger... */
      }
      return true;
      break;

    /* ... */
  }
};
```

The `_satisfy` method tries to fix one need, but may not succeed (its return value can be false). This is why we try all of the unsatisifed needs until we find one that can be improved. The `_satisfy` implementation is a very generic one, you will have to adjust it to suit your needs.

## Further considerations

---

The code presented above implements only a very basic version of a need-based AI. An interactive [demo](http://ondras.github.io/need-based-ai/) is available; it uses the code above as well as a complete implementation of all four needs. The source code is freely available at [GitHub](https://github.com/ondras/need-based-ai/tree/master).

To refine the AI behavior, we can provide several implementations of the `_satisfy` function: some beings might want to satisfy their "hunger" need by eating a ration; some may want to seek food in the woods; some may want to rob other people and eat their dead bodies.

Finally, the `priorities` list is another important place: different ordering of needs will lead to interesting behavior patterns:

- A **commoner** focuses on `["survival","satiation"]` only;
- a **fighter** surely uses a `["survival","satiation","revenge"]` pattern;
- a **berserker** will probably change his order to `["revenge","survival","satiation"]`;
- **town sheriff** might prefer a "justice" need (when two different beings fight each other) before his own revenge;
- etc etc etc.
