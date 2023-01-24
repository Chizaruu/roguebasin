# Shadow casting

---

## What is Shadow Casting?

---

Shadow casting is a way to implement [Field of Vision](field_of_vision.md). In shadow casting, a destination square is visible from a source square if there exists an unobstructed line from the center of the source square to any point in the destination square. There are several efficient algorithms to accomplish this, including two which only visits squares that are visible ([Spiral Path FOV](spiral_path_fov.md) and [FOV using recursive shadowcasting](fov_using_recursive_shadowcasting.md)).

## Advantages

---

- Moderately easy to implement
- Efficient
- Existing library

## Disadvantages

---

- Some artifacts (corners of rooms, for example).
- Non-symmetric, making it awkward for some implementations of ranged combat.

## How do I implement it?

---

- [Computing LOS for Large Areas](computing_los_for_large_areas.md)
- [FOV using recursive shadowcasting](fov_using_recursive_shadowcasting.md)
- [FOV using recursive shadowcasting - improved](fov_using_recursive_shadowcasting_-_improved.md)
- [Improved Shadowcasting in Java](improved_shadowcasting_in_java.md)
- [Spiral Path FOV](spiral_path_fov.md)
- [Precise Shadowcasting in JavaScript](precise_shadowcasting_in_javascript.md)

## What games use it?

---

Most modern games use shadow casting.

## What libraries implement it?

---

- [SquidLib](squidlib.md) is a Java library which includes an easy to read recursive shadowcasting algorithm.
- [libfov](libfov.md) is a C library which implements recursive shadow casting.
- [Roguelike Library For Java](roguelike_library_for_java.md) provides an implementation of shadow casting in Java.
- [libtcod](libtcod.md)
- [rot.js](rot.js.md) implements Precise Shadowcasting
- ([doryen-fov](https://github.com/jice-nospam/doryen-fov)) implements it in Rust
