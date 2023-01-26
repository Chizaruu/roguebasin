# Geometry

---

For tactical games, such as roguelikes, it is very important how individual cells in the game map are connected to each other. This article lists various geometries associated in roguelikes.

## Eight-directional movement

---

Most classic roguelikes use this geometry. Characters and monsters can move horizontally, vertically, or diagonally. Since it is the classic one, other geometries in this article are compared to it.

**Keyboard**. One problem with eight-directional movement is that many people use laptops nowadays, and it is hard to use on a laptop keyboard. Laptops do not have a numpad (which would be convenient), and the classical "hjklyubn" layout is not intuitive nor ergonomic. There is no really good solution to this problem; one solution is to use Shift and Ctrl keys together with left/right arrows (this method was probably invented in [Infra Arcana](infra_arcana.md), and is also made available to other roguelikes by [NotEye](noteye.md)). It is also possible to buy numpads to play roguelikes with them. Many people who are not used to roguelikes also often struggle to understand that they can move diagonally, as most games outside the genre have only 4-way movement.

**Examples**: [Rogue](rogue.md), [ADOM](adom.md), [NetHack](nethack.md), [Moria](moria.md), [Crawl](linleys_dungeon_crawl.md)

## Four-directional movement

---

Here, you are not allowed to move diagonally, only horizontally or vertically.

**Interaction with "no wait" mechanics**. An interesting feature of four-directional movement is that, if you want to attack the monster first, you will have to skip a turn, while in other geometries it is possible to make moves which switch the parity. Some games do not allow to skip turns, and this makes this feature very important (Zaga-33, MicRogue, and to a lesser extent [Fuel](fuel.md)).

**Examples**: [POWDER](powder.md), [Cardinal Quest](cardinal_quest.md), [Dungeons of Dredmor](dungeons_of_dredmor.md), Zaga-33

## Hexagonal tiles

---

Here, cells are arranged like in a honeycomb. PC and monsters can move in 6 directions. In some games, the hexes have two horizontal edges (A), and in other games, they have two vertical edges (B).

**Keyboard**: In (A), you can simulate the 6 directions on a keyboard quite intuitively with e.g. qweasd. In (B), you can use weazdx or uybngj (these sets of keys are arranged in the correct shape). You can also use the numpad without two keys (left/right for A, up/down for B).

**Display**: It is harder to display hex roguelikes on a ASCII terminal. The best way seems to be to use (B) and a font with tall characters (most fonts have this property) and use a checkerboard pattern, alternating spaces and map symbols. Since characters' height is greater than width, it is quite easy to learn that each cell is connected to four other cells at its diagonals, and to the non-empty cells 2 characters to the left and to the right from it. See this method in action in e.g. [Hydra Slayer](hydra_slayer.md).

**Examples**: Laser Spigot, a mode in [Hydra Slayer](hydra_slayer.md), add-on in ToME4

See the main article [Hexagonal Tiles](hexagonal_tiles.md) for other hints about display and implementation.

## Continuous map

---

Although most roguelikes use a discrete map made of cells, there are some where this is not in case. Objects can be at placed any point in the dungeon. This typically makes games less tactical, and it is natural to use real time (although there are games on a continuous map which are somewhat turn-based, like Smooth Rogue and [WazHack](wazhack.md)).

**Examples**: [Spelunky](spelunky.md), [Triangle Wizard](triangle_wizard.md), Diablo II, Smooth Rogue, [WazHack](wazhack.md)

## Gravity

---

In most roguelikes, everything on the map is supposed to be on the same level. An alternative interpretation is that lower cells are in fact below higher cells, and objects in the game are affected by gravity. This makes the game feel like a platformer somewhat.

Examples: [Earl Spork](earl_spork.md), [Fuel](fuel.md), [Spelunky](spelunky.md), [WazHack](wazhack.md), [Red Rogue](red_rogue.md)

## Full 3D

---

It is common for roguelikes to be a set of two-dimensional levels connected by stairs. However, there is no spatial relation between separate levels, they could be replaced with portals or tunnels and the game would be exactly the same.

Another solution is to have a true three-dimensional map. You can interact with objects not only on the same level as you, but also ones above and below you. Note that this is about geometry, not about graphics (you can have a 3D geometry with 2D graphics and vice versa).

**Examples**: [Dwarf Fortress](slaves_to_armok_II_dwarf_fortress.md), [Vapors of Insanity](vapors_of_insanity.md), [EmoSquid](emosquid.md), [City of the Damned](city_of_the_damned.md); X-Com is a known non-roguelike using this (see also [X@COM](xcom.md))

See the main article [Third_dimension_in_an_ASCII-based_roguelike](third_dimension_in_an_ascii-based_roguelike.md).

## Configurable geometry

---

It is possible to program a roguelike in a way which is independent of the underlying geometry, by consistently using general constructs such as "for all cells C2 which are in distance from Min to Max from C1:" and "distance from C1 to C2" instead of using (x,y) loops or specific formulae. This allows one to create a roguelike which gives the player a choice between the three major discrete geometries (eight-directional, four-directional and hex). This is surprisingly easy to do (most algorithms can be straightforwardly interpreted in other geometries). The balance will be different when using a different geometry (for example, four-directional movement makes the maps feel larger), but this can be fixed by also changing the AI, dungeon generation or game rules accordingly (like Hydra Slayer does). It is not necessary to make it perfectly balanced since the same thing happens with other choices (race and class).

**Examples**: [Hydra Slayer](hydra_slayer.md), [Vapors of Insanity](vapors_of_insanity.md)

## Exotic geometries

---

- [ChessRogue](chessrogue.md) has a square grid, but creatures move like chess pieces. A very interesting use of geometry.

- It is possible to mix 8-directional and 4-directional movement by making diagonal movement take 3/2 as much time as cardinal movement (Used in: [Zaiband](zaiband.md), and the non-roguelikes XCOM, Laser Squad, Rebelstar).

- [Hyperbolic Rogue](hyperbolic_rogue.md) uses hyperbolic geometry. The map consists of hexagons and heptagons.

- It is possible to have maps which locally look like normal Euclidean grids (where each cell has its unique (x,y) coordinates), but in fact, the topology is more complicated. [Jeff Lait](jeff_lait.md)'s 7DRLs ([Jacob's Matrix](jacobs_matrix.md), [Smart Kobold](smart_kobold.md), [Vicious Orcs](viscious_orcs.md), [Sword In Hand](sword_in_hand.md)) have this property: while each room is Euclidean, they do not match exactly (which is possibly more realistic, since real caves also would have such complicated structure and would be difficult to map). [KleinRL](kleinrl.md) and [Hydra Slayer](hydra_slayer.md) use topologies like Klein's bottle (which means that it is possible to return to the same place as a mirror image).

- [Decker](decker.md) looks grid-based, but in fact this grid is completely irrelevant: you can interact with anything which is in the same room. Such "geometrylessness" greatly reduces its roguelike feeling.
