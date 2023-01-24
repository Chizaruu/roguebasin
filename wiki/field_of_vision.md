# Field of Vision

---

Some of the most important features on roguelikes, including the presentation of the area that surrounds the player character, require to check the visibility of an object from a given point on the map. For example, it may be necessary to know which parts of map are visible from the place the player character is standing to draw those parts of map on the screen.

There are basically two ways to achieve this; by using [Line of Sight](line_of_sight.md) to check all the map squares in range, which is a slow method as it repeats most of the calculations multiple times, or by calculating the Field of Vision as a whole.

## Approaches

---

Some of the most common approaches to field of vision are:

- Light whole level. Just make everything visible, or at least everything close to the player. Many games use this approach and it seems to work.
- Light whole room. This is the approach used by the original [Rogue](rogue.md). You just make the whole room in which the player character is visible, and all other parts of map not visible. When the character is in a corridor, it can only see one square from where he stands, this doesn't require any sophisticated calculations.
- [Extremely fast simplified LOS](extremely_fast_simplified_los.md) -- Take advantage of the way most roguelike maps are drawn (square rooms connected with corridors).
- [FastLOS](fastlos.md) -- precalculated approximate LOS using a bitwise AND.
- [Ray casting](ray_casting.md)
- [Shadow casting](shadow_casting.md)
- [Restrictive Precise Angle Shadowcasting](restrictive_precise_angle_shadowcasting.md)
- [Permissive Field of View](permissive_field_of_view.md) (new and uncommon)
- [Digital field of view](digital_field_of_view.md) (new and unheard of)
- [Very simple line of sight pseudo-code.](eligloscode.md)
- [Quick and dirty FOV/LOS](quick_and_dirty_fovlos.md)
- [LOS using strict definition](los_using_strict_definition.md) (new and unheard of)

## Refinements

---

There are other refinements that can be applied to any approach:

- [An Efficient Observation Algorithm](an_efficient_observation_algorithm.md) may allow fewer squares to be visited.

## Studies

---

- [Comparative study of field of view algorithms for 2D grid based worlds](comparative_study_of_field_of_view_algorithms_for_2d_grid_based_worlds.md)

## Available Libraries

---

- [SquidLib](squidlib.md) is a [Java](java.md) library made by [Eben Howard](eben_howard.md) which supports several types of FoV, including translucence properties. The shadowcasting implementation is shorter and easier to read than most other implementations.
- [libfov](libfov.md) is a [C](c.md) library made by Blue Puyo, which supports both circular FoV and Beams using [Shadow casting](shadow_casting.md)
- [permissive-fov](permissive-fov.md) is a library for both C and C++ written by Jonathon Duerig. It uses [Permissive Field of View](permissive_field_of_view.md), and supports arbitrary shaping of FoV using visitation masks. It does not support Beams.
- [libtcod](libtcod.md) is a C/C++/python library which, while not dedicated to fov, contains implementations of basic raycasting, los_rays (a.k.a. diamond raycasting), recursive shadowcasting, restrictive precise angle shadowcasting and precise permissive fov.
- [rlforj](http://rlforj.sourceforge.net/) - Java library featuring several FoV algorithms.

## See Also

---

- [Discussion:Field_of_Vision](discussion_field_of_vision.md)
