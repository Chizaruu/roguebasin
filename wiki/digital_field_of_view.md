# Digital field of view

---

## What is Digital Field of View?

Digital Field of View is a method of determining [Field of Vision](field_of_vision.md) based on [Digital lines](digital_lines.md). Geometrically, all objects are considered to be diamond shaped, and the source diamond can see the destination diamond if there is an unobstructed line connecting them.

## Advantages

- Symmetric field of view.
- Completely artifact free.
- O(N2) algorithm available.
- More permissive than [Permissive Field of View](permissive_field_of_view.md).
- Simpler and potentially faster than PFOV.
- It is easy to check by hand whether there is a digital line connecting two points.
- Targeting line is easily drawn in case of ranged combat

## Disadvantages

- Few implementations exist yet.
- Could be too permissive, especially with 1-tile wide corridors and room entrances.

## How do I implement it?

- [Digital field of view implementation](digital_field_of_view_implementation.md) is Zeb's explanation for his own algorithm, based on a method similar to beam casting.
- An improvement over beam casting in the spirit of shadow casting, where instead of beams, octants are cast in 8 directions, is a part of the 7DRL game Kusemono, with a freely available [C implementation](http://www.interq.or.jp/libra/oohara/digital-fov/index.html).
- Any working [Permissive Field of View](permissive_field_of_view.md) algorithm can be turned into a digital field of view algorithm by rotating the grid by 45 degrees, and changing some of the corner cases.
- One such conversion of PFOV is based on the recursive shadow casting variant of the [Precise_Permissive_Field_of_View](precise_permissive_field_of_view.md) algorithm that scans whole quadrants. Both the PFOV and DFOV implementations are a part of [LambdaHack](lambdahack.md) and [Allure of the Stars](allure_of_the_stars.md). The DFOV implementation is simpler and faster than the PFOV implementation, because DFOV has less special cases and inspects tiles in an order matching horizontal dungeon walls. See [a discussion of this DFOV (and PFOV) algorithm](https://github.com/Mikolaj/Allure/wiki/Fov-and-los) with links to the Haskell source code.

## What games use it?

- Kusemono ([7DRL_Contest_2011_Reviews#Oohara_Yuuma.27s_Kusemono](7drl_contest_2011_reviews.md#Oohara_Yuuma.27s_Kusemono), DFOV is good for sneaking in wide corridors)
- [Allure of the Stars](allure_of_the_stars.md)

## What libraries implement it?

None.
