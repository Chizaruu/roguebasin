# Ray casting

---

## What is Ray Casting?

---

Ray casting is a method for calculating [Field of Vision](field_of_vision.md) where rays are traced from the center of a source square to a select number of destination squares. Squares are marked as visible as the rays pass through them, and walls will block the rays.

There are a few ways to decide where rays are to be cast:

- Every potential destination -- This method is very slow, but results in a crude approximation of [Shadow casting](shadow_casting.md).
- Every square along the perimeter of the area being checked for [Field of Vision](field_of_vision.md) -- This is faster, but causes an increasing number of artifacts as the radius increases.
- A fixed number of rays as regular intervals -- Provides a tweakable knob that trades off between accuracy and speed.

## Advantages

---

- Easy to implement
- Builds intuitively on [Line of Sight](line_of_sight.md) algorithms.

## Disadvantages

---

- Slow compared to other methods. Even when casting only a few rays, squares close to the source will be visited many times.
- Many artifacts, even in common situations

## How do I implement it?

---

- [Ray-Tracing Field-Of-View Demo](ray-tracing_field-of-view_demo.md)
- [A Bucket Of LOS](a_bucket_of_los.md)
- [LOS by Odd](los_by_odd.md)
- [Line of Sight - Tobias Downer](line_of_sight_-_tobias_downer.md)
- [Simple and accurate LOS function for BlitzMax](simple_and_accurate_los_function_for_blitzmax.md)
- [Very simple line of sight pseudo-code.](eligloscode.md)
- [Raycasting in python](raycasting_in_python.md)

## What games use it?

---

[Moria](moria.md)

## What libraries implement it?

---

[libtcod](libtcod.md) contains an enhanced version of perimeter raycasting with a post-processing step removing most artifacts and making it equivalent to shadowcasting.
