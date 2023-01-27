# Strict Field of View

---

## What is Strict field of view?

---

Strict field of view is a way to implement [Field of Vision](field_of_vision.md). In this algorithm, a destination square is visible if the line from the center of the source square to the center of the destination square is unobstructed.

## Advantages

---

- Easy to implement
- Symetric, emphatises with ranged combat
- Strict, enforces stealth

## Disadvantages

---

- Strictness obstruct room corners when walking close to walls and make possible to see isolated squares (looking through a diagonal wall for example).
- It's sometimes possible to predict a tile is not a wall even if not seeing it properly.

## How do I implement it?

---

- [FOV using strict definition](los_using_strict_definition.md)

## What games use it?

---

My upcoming roguelike sure do. ;D
