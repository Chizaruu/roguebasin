# Permissive Field of View

---

Permissive field of view defines visibility more loosely than other [Field of Vision](field_of_vision.md) methods. A destination square is visible from a source square if there is any unobstructed line from some point in the source square to some point in the destination square. This means that players and monsters will automatically 'peek' around corners, for example. It also means that field of view is symmetric. That is to say that if a destination square is visible from a source square, then that source square is also visible from the destination square. Some approximation algorithms might lose the property of guaranteed symmetry.

One tricky corner case are literally the corners. There are two questions that must be answered. Are corners of squares valid points in the source and destination squares for determining visibility? And do corners of walls obstruct line of sight? Different algorithms may answer these questions in different ways.

## Advantages

- Symmetry of field of view may make ranged combat more sensible.
- Monsters cannot sneak up on the player as easily.
- Reduced player exploitation of line of sight artifacts.
- There is an existing library implementing it.
- May make certain aspects of the game easier (realistic lighting).

## Disadvantages

- The most complicated method to understand and implement (and therefore debug).
- The player cannot sneak up on monsters more easily.
- May ruin play balance of current games.
- It is more difficult for the player to withdraw from ranged combat (but easier to stay out of range, see [Retreating is safe](discussion_field_of_vision.md#Extra_visibility_properties)).

## Algorithms

There are a number of articles describing different methods:

- [Isaac_s_fast_beamcasting_LOS](isaac_s_fast_beamcasting_los.md) -- An approximate algorithm using 'wide beams' sent out at fixed slopes. The larger the radius, the more beams must be sent out to avoid artifacts.
- [Mutual_Visibility_Field_Of_View](mutual_visibility_field_of_view.md) -- Uses the corners of squares to determine visibility. In some cases, it does not precisely capture Permissive Field of View. However, the algorithm guarantees symmetry in the field of view.
- A fast algorithm using pre-cached dependencies is described in the source code for [Dungeon Crawl Stone Soup](dungeon_crawl_stone_soup.md)
- [Precise Permissive Field of View](precise_permissive_field_of_view.md) -- A fast and, in theory, artifact free variation.

## Games using Permissive Field of View

- [Dungeon Crawl Stone Soup](dungeon_crawl_stone_soup.md)
- [Cataclysm](cataclysm.md)

## Implementations

<table>
  <tr>
    <td>Library</td>
    <td>Language(s)</td>
    <td>Algorithm(s)</td>
  </tr>
  <tr>
    <td colspan="1"><a href= "permissive_field_of_view_in_python.md">Permissive Field of View in Python</a></td>
    <td colspan="1">Python</td>
    <td rowspan="8"><a href="precise_permissive_field_of_view.md">Precise Permissive Field of View
    </a></td>
  </tr>
  <tr>
    <td colspan="1"><a href= "permissive-fov.md">permissive-fov</a></td>
    <td colspan="1">C/C++</td>
  </tr>
  <tr>
    <td colspan="1"><a href= "roguelike_library_for_java.md">Roguelike Library For Java</a></td>
    <td colspan="1">Java</td>
  </tr>
  <tr>
    <td colspan="1"><a href= "ruby_implementation.md">Ruby implementation</a></td>
    <td colspan="1">Ruby</td>
  </tr>
  <tr>
    <td colspan="1"><a href= "libtcod.md">libtcod</a></td>
    <td colspan="1">C/C++/python</td>
  </tr>
  <tr>
    <td colspan="1"><a href= "permissive_field_of_view_in_javascript.md">Permissive Field of View in Javascript</a></td>
    <td rowspan="2">Javascript</td>
  </tr>
  <tr>
    <td colspan="1"><a href="https://www.npmjs.com/package/permissive-fov">permissive-fov</a></td>
  </tr>
  <tr>
    <td colspan="1"><a href="https://github.com/LambdaHack/LambdaHack/wiki/Fov-and-los">Recursive shadow casting PFOV (and DFOV)</a>
    </td>
    <td colspan="1">Haskell</td>
  </tr>
</table>
