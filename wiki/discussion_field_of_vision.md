# Discussion:Field of Vision

---

## Tenets

These were [originally posted by PaulBlay](http://angband.oook.cz/forum/showpost.php?p=20814&postcount=48), and were later expanded. All of these are not necessarily desirable, and some of them may be mutually exclusive.

Visibility properties:

1. **Symmetry**. Everything you see can see you, and vice versa.
1. **Expanding pillar shadows**. Standing directly next to a pillar produces an expanding shadow.
1. **Efficiency**. Reasonably fast code can be produced to implement the FOV, etc.
1. **No blind corners**. Moving diagonally around a corner does not place you in melee range of a previous non-visible tile.
1. **Expansive wall display**. You can see all of a room's walls while standing anywhere inside of it, and all walls of a long, straight corridor. This can either be a natural property of the algorithm used (in which case the walls are visible) or handled as a special case (considered displayed but not generally visible or targetable).
1. **No hidden ghosts**. Passwall monsters inside of visible walls are always visible.

Targeting properties:

1. **Visible = Targetable**. You can target any visible tile.
1. **Non-exploitable**. No trick shots required or possible to hit monsters that you can't target directly.
1. **No lost targeting**. Casting stone-to-mud on a walled, targetable ghost does not cause you to lose targeting on that tile.

Expansive visible walls with no hidden ghosts, visible = targetable and symmetry may allow pass-wall monsters to target players from a lot further away than now (see DFOV).

## Terminology

---

### Field of View

---

The field of view refers to the set of squares that are considered visible. For the current discussion, this should not include squares that are known to the player due to ESP, detection, etc. FOV algorithms under discussion all operate by determining if a line-of-sight (LOS) exists between the player and each other game square; actual implementations will probably optimize this calculation. One can imagine FOV algorithms which do not use LOS.

### Line of Sight

---

A path between the player and a visible square. Different LOS algorithms will use different strategies for specifying what part(s) of the source and destination squares are eligible to have lines between them (e.g. the center of the square), what part(s) of wall squares will block LOS, and how many lines must be drawn to have LOS.

### Projectile Path

---

Projectile path refers to the path that a projectile (e.g. an arrow) will travel on its way from a player to a visible square. This is important if intervening monsters can possibly block a projectile from reaching its destination. Some systems may not allow this (in which case LOS == projectile path is trivial) and some will check a path of squares in a given order which are either guaranteed to absorb the shot (as Angband does now), or have a percentage chance to do so. If LOS == targetability then it's important that squares which didn't block LOS not block a projectile. However, some systems may want to those squares absorb projectiles when occupied. Thus, in addition to LOS, it's important to be clear how projectile paths should work.

NOTE: Many of these systems don't make clear how they expect to handle projectile path; it would be great to add this, since implementing FOV/LOS changes in Angband without modifying project_path() won't result in useful changes to gameplay.

### Symmetrization

---

#### Logical-and and Logical-or Symmetrization

Any visibility algorithm that is not symmetric, admits two obvious ways of transforming to an algorithm that is symmetric. Assume that for any two squares x,y in a given collection, that a visibility function V(x,y) is defined, and returns true or false.

- The intuitive way of defining that the visibility function is symmetric: V(x,y)=V(y,x) .
- The least confusing formal way of defining that a visibility algorithm is symmetric, is to say that a visibility algorithm is symmetric if and only if the visibility function defined by the visibility algorithm is symmetric. (Informally, just conflate the visibility algorithm with the visibility function it defines.)
- Logical-and symmetrization: V<sub>AND</sub>(x,y) := V(x,y) AND V(y,x), where AND denotates the traditional logical-and logical connective. Corresponding functional notation: V |-> V<sub>AND</sub> .
- Logical-or symmetrization: V<sub>OR</sub>(x,y) := V(x,y) OR V(y,x), where OR denotates the traditional logical-or logical connective. Corresponding functional notation: V |-> V<sub>OR</sub>

Both V<sub>AND</sub> and V<sub>OR</sub> are symmetric visibility functions by construction.

While these definitions are meaningful for already-symmetric visibility functions, they both have no effect. Like any other possible way of construction symmetric visibility functions from asymmetric visibility functions, both V |-> V<sub>AND</sub> and V |-> V<sub>OR</sub> are idempotent functions of functions i.e. functors. [ V(x,y)=V<sub>AND</sub>(x,y)=V<sub>OR</sub>(x,y) for all symmetric visibility functions V, and squares x,y for which said visibility function is defined.

It is plausible that when a visibility algorithm V has **expansive wall display**, the logical-or symmetrized V<sub>OR</sub> never has **expanding pillar shadows**.

#### Visibility Ether

An alternate way of implementing the symmetry property for visibility and targetability, is to define a preferred viewpoint for visibility/targetability (usually @) -- then use this viewpoint for calculating both player visibility/targetability, and monster visibility/targetability.

> Bessarion: this is "visibility ether" in honor of the final effort to reconcile Maxwell's equations with the Galilean transform traditionally used in Newtonian physics, the ether.

This gets the symmetry property while retaining an asymmetric visibility function V. Even when V(x,y)≠V(y,x), symmetry is attained regardless: for @ with position x and monster with position y, both visibility checks are done with V(x,y). Of course, transposing the player and the monster will end up using V(y,x). For an asymmetric visibility function V, this does mean transposing a player and monster that can see each other, could end up rendering both player and monster mutually unable to see each other. This is **still** symmetric visibility.

Vanilla Angband has inconsistently used this for some time -- not to determine the monster's ability to target the player, but as part of determining whether the monster has a turn at all. NPP reportedly uses this symmetrization technique in recent versions for both visibility and targetability.

## Algorithms

---

### Digital FOV

---

#### Description

[Digital field of view](digital_field_of_view.md) was [first mentioned by Atanvarno](http://angband.oook.cz/forum/showpost.php?p=20799&postcount=38). The player and monsters are treated as diamonds, with the four points centered on the containing tile's edge. Drawing an unobstructed line from any part of one diamond to another diamond implies visibility. Walls may be treated as diamonds as well, both for visibility and obstruction.

#### Properties and advantages

Symmetry, efficiency, no blind corners, naturally expansive visible walls, no hidden ghosts. A demo implementation already exists, and has no known visibility artifacts.

#### Possible disadvantages

- DFOV lacks expanding pillar shadows. Small groups of adjacent wall will still produce a conical shadow effect.
- From any location inside a corridor, the player can see 1 tile around the corner. By standing on the corner, the player can see all the way down both corridors, including walls. Combined with a targeting system that implements _Visible = targetable_, pass-wall monsters may become significantly more challenging because they could use breath and spells from the safety of a wall tile at great distances, unless the player is given the ability to return fire with spells and/or ranged attacks. Being able to target the pass-waller is not enough; currently player ranged attacks fail when entering a ghost-occupied wall tile.
- This method can produce regions of discontinuous shadows at oblique angles and large distances.

#### Consequences

Fig 1. Digital FOV ex. %'s are walls out of sight.

```text
  %%%%#%%
  %  .
 %% ###%%
 % ...#
##....#
@.....#%%%%
##........#%
 % ...###...
 %  ..# %%%%
 %%%%##
```

Fig 2. Narrow and discontinuous pillar shadows

```text
...@...   @......   @............   @..................
...#...   .#.....   ..#..........   ...#...............
... ...   .. ....   .... ........   ...... ............
... ...   ... ...   ...... ......   ......... .........
... ...   .... ..   ........ ....   ............ ......
... ...   ..... .   .......... ..   ............... ...
... ...   ......    ............    ..................
```

Fig 3. A room 25% full of pillars.

```text
 ..    ...    ..       #.#     #.#     #.#
. .#   #.#   #. .       ...    ...    ...
.. ..  ...  .. ..        #.#   #.#   #.#
 #. .# #.# #. .#          ...  ...  ...
  .. ....... ..            #.# #.# #.#
   #. .#.#. .#              .........
    .. ... ..         .#.#.#.#.#.#.#.#.#.#.
.#.#.#.#.#.#.#.#.     .....................
........@........              #@#
.#.#.#.#.#.#.#.#.     .....................
    .. ... ..         .#.#.#.#.#.#.#.#.#.#.
   #. .#.#. .#              .........
  .. ....... ..            #.# #.# #.#
 #. .# #.# #. .#          ...  ...  ...
.. ..  ...  .. ..        #.#   #.#   #.#
. .#   #.#   #. .       ...    ...    ...
 ..    ...    ..       #.#     #.#     #.#
```

### Diamond walls, point visibility

---

#### Description

Walls are considered diamond-shaped obstructions, with the 4 points of the diamond centered on the edges of the containing tile. Visibility between two tiles is implied by drawing an unobstructed line between the centers of the two tiles. The four points of the obstructing diamond do not obstruct visibility unless that point is adjacent to another wall tile (this allows extra visibility around corners, but prevents sight through walls).

#### Properties and advantages

Symmetry, expanding pillar shadows, efficiency, no blind corners, no hidden ghosts. Ghosts are not visible in corridor walls from great distances, and thus pose no problem for a targeting system that uses visible = targetable.

#### Possible disadvantages

This method lacks naturally expansive visible walls which means part of a room's walls will not be visible to a player standing inside the room, if he is close to a wall or a corner. This is more pronounced the closer to a wall, and the larger the room. This could be corrected by automatically adding map memory for the walls of lit rooms, so while only some of the walls would be visible (and marked with a special color) all the walls of the room would appear to the player.

A second point-to-diamond "tile visibility" check could be made to add extra visibility for wall tiles, but this would break no hidden ghosts and possibly no lost targeting.

This method can produce regions of discontinuous visibility at oblique angles (see below).

#### Consequences

Fig 4. Conical pillar shadows

```text
...@...   @......   @............   @..................
...#...   .#.....   ..#  ........   ...#  .............
... ...   ..  ...   ...     .....   .....       .......
..   ..   ..    .   .....       .   .......           .
..   ..   ...       ......          .........
.     .   ...       .......         ...........
.     .   ....      .........       .............
```

Not all walls (or doors/monsters in those locations) are visible from all locations inside of a room.

Fig 5. Non-visible room walls.

```text
####?????     #####????     ?#####???     ??#####??
#@.......     #.@......     #..@.....     #...@....
#........     #........     #........     #........
#........     #........     #........     #........
?........     #........     #........     #........
?........     #........     #........     #........
?........     ?........     #........     #........
?........     ?........     #........     #........
```

Fig 6. Discontinuous point visibility

```text
@#?????
...
###.#
    .
     ?.?

        ?.?
```

![Diamond walls](./assets/images/articles/discussion_field_of_vision/diamond.webp)

### Half-width walls, center to center

---

Suggested by Eddie(PowerDiver).

This is a symmetrical system.

Consequences:

Fig 7. Corridor walls / monsters seen forever.

```text
################D
@
```

@ can see D, D can see @

Fig 8. Indeterminate (resolve to not visible).

```text
##D
##
@
```

Fig 9. Diagonal move safety.

```text
#m
#
@#
```

Vital that @ can see m in this case.

Fig 10. Pillar blocking sight.

```text
......................
.@#                 M
......................
```

@ cannot see M (by zero-width blockage sub-rule - see fig 5)

Fig 11. Discontinuous gaps in viewable area (by zero-width blockage)

```text
.......
.@.....
...#...
..... .
.......
```

### Monsters occupy half the width/height of grid

---

[Suggested by jv123](http://angband.oook.cz/forum/showpost.php?p=20779&postcount=21).

Monsters, characters, items are in the center of their grid's square taking up one-quarter of it's area (half the width/height). If lines from any point in the @'s sub-square can go to any point in the M's sub-square without crossing a wall then each is visible by the other. Walls take up the full grid square.

This is a symmetrical system.

Consequences.

Fig 12. @ cannot see D.

```text
#####D######
@
```

Fig 13. It is indeterminate whether @ can see D or not (zero-width cross).

```text
####D#######
@
```

Fig 14. @ can see D and D can see @

```text
###D########
@
```

### Center to Center, subdivided grid

---

[Suggested by Marble Dice](http://angband.oook.cz/forum/showpost.php?p=20786&postcount=26)

Any tile that can have a line drawn from the center of the @ to the center of the tile is without crossing an obstructed point is visible. Each wall takes up the middle 2x2 of the 4x4 sub-divided grid.

For visibility purposes a monster on a wall-tile is not treated differently from a monster on a floor tile.

Consequences.

Fig 15. From the entrance of a room.

```text
#######.#######
#######@#######
????.......????
?.............?
```

Fig 16. @ cannot see M.

```text
................?
.........????????
.@.###?????M?????
.........????????
................?
```

Fig 17. Expanding shadow triangle from pillar.

```text
@...........
...#?.......
.....????...
.......?????
.........???
```

Fig 18. @ cannot see D.

```text
####D
@...
```

Fig 19. @ can just see D (by allowing zero width cross).

```text
###D#
@...
```

### Traditional (Angband)

---

[First mentioned by Atanvarno](http://angband.oook.cz/forum/showpost.php?p=20799&postcount=38)

Trick shots are possible (e.g. you can shoot at indirectly targeted grids that you cannot see or target directly).

Consequences.

Fig 20. A cannot see X but can hit it by shooting at B.

```text
###X.B
A.....
```

### Zaiband FOV and targetting

---

Fig 21. One pillar cases.

```text
.....?
...???
@#????
...???
.....?
```

```text
...??
...??
..???
..??.
.#...
@....
```

In a plain rectangular room: identical to V, except you can target anything you can see that isn't in a wall. The projection path enabling visibility will automatically swerve as needed, thanks to Tyrecius' [Permissive Field of View](permissive_field_of_view.md) techniques.

Fig 22. Entering a room

T-intersections and entering rooms are a bit more dangerous in Zaiband:

```text
??.
??.
?..
#..
@..
#..
?..
??.
??.
```

has a reasonable ambush by D:

```text
###
#D.
#..
#..
#..
@..
#..
#..
#..
#..
```

Problem being that that with all trick shots being handled automatically, all visible/targetable floor squares are fair game for ground zero of a ball spell; @ can be targeted by D but not conversely.

Fig 23. Symmetrized viewability/projection algorithm (considered as an extra option for Zaiband)

```text
?##
?D.
?..
?..
#..
@..
#..
#..
#..
#..
```

Fig 24. No hockypucks.

Zaiband abolishes the hockey puck by allowing off-diagonal projections to start diagonally:

```text
??o
?x#
#x#
@.#
```

In V, this fails because the first step is into the wall to the north.

> I proceeded with Zaiband by first simplifying V's base projection algorithm; this stopped hockey pucks as a side effect. Cf. flow.c for end result. [V starts directly emulating fractional steps at the second step. Zaiband does so starting at the first step.
>
> If there are fixable problems with the initial projection path (offsets directly along an axis or primary diagonal do not admit fixing):
>
> - Check for standard one-off trick shots that anyone can hand-calculate: abs(Δx)==1, abs(Δy)==1, or abs(abs(Δx)-abs(Δy))==1. If they are applicable, use them.
> - If no standard one-off trick shot applies, fall back to [Permissive Field of View](permissive_field_of_view.md).
>   - We use rational tangent values as proxies for the actual angles.
>   - The simplified base projection algorithm admits trivial calculation of the upper and lower rational tangent values for both hitting the target square, and hitting the obstruction. Take the set difference and see if the resulting interval is nonempty.
>     - empty: target square is non-projectable.
>     - non-empty: use tangent angle summation and half-angle formulæ to determine a suitable positional target. Retry the base algorithm. If this path is obstructed, repeat the [Permissive Field of View](permissive_field_of_view.md) correction.
>
> Zaiband defines visibility as "illuminated, and projectable with a wand/rod of light".
>
> I was pleasantly surprised that this automatic fixup gets all advocated properties except symmetry; the player need never calculate trick shots. Imposing either logical-or symmetry or logical-and symmetry would be a rote change. -- Bessarion

### Pete Mack's Desiderata

---

Here's a summary of what I understand from this discussion, along with some of my preferences.

I will assume that walls are convex hulls around diamond-shaped wall grids--it makes more sense than anything else.

Definitions: LOS(p1, p2) line from point p1 to point p2 misses (or is tangent to) any intervening wall diamonds.

T(a, b) grid b is targetable from grid a. (Targeting Line of Sight TLOS)

V(a, b) grid b is visible from grid a. (Visual Line of Sight VLOS)

C(a) center point of grid a

These are my desiderata:

1 . Target symmetry

1a. assuming a is not a wall and b is not a wall.

T(a, b) == T(b, a)

```text
##o# If @ can target o then o can target @.
@..#
####
```

1b. For ghosts in the wall, ghost is treated as in an empty square for T(G,@), but is treated as a wall block for T(@, G)

```text
##D# D can target @ but @ can't target D.
@...
####
```

2 . LOS symmetry

V(a, b) == V(b, a)

```text
##o# If @ can see o then o can see @.
@..#
####
```

3 . Dominance of V over T. This guarantees VLOS for reverse T(G,@)

T(a, b) ⇒ V(a, b)

```text
##### All targetable o can be seen
.@ooo (but may not be able to target every o that can be seen).
###oo
```

4 . Unpermissive TLOS (I lean towards 4a)

4a By center:

LOS(C(a), C(b)) ⇔ T(a, b) [ ⇔ T(b, a) ]

![center2center](./assets/images/articles/discussion*field_of_vision/center2center.webp)

_A line of sight from center of grid a (@) to center of grid b (.) implies a is targetable by b and b is targetable by a._

4b Existential:

∀p1 ∈ a ∃p2 ∈ b LOS(p1, p2) ⇔ T(a, b)

![anywhere2anywhere](./assets/images/articles/discussion_field_of_vision/anywhere2anywhere.webp)

_A line of sight from any point in grid a (@) to any point in grid b (.) implies a can target b and b can target a._

5 . Permissive VLOS. (I lean towards 5a)

5a. By center

LOS(C(a), p2 ∈ b) | LOS(C(b), p1 ∈ a) ⇔ V(a, b)

5b. Existential:

∀p1 ∈ a , p2 ∈ b LOS(p1, p2) ⇔ V(a, b)

Am I missing anything in this? In this case, a picture may be worth a thousand words, but a little math is worth a thousand pictures. (You may conclude what you will by transitivity :D )

Additional desiderata:

6 . Shadows shall not be disconnected. If a single grid wall casts a shadow at a some point, there must exist a (diagonally) connected path of shadow from the wall to the point. (In ordinary parlance: it always becomes easier to hide behind something as you get closer to it.)

6b. Let a, b be two grids which are both in shadow by a column at grid w of grid @. Then there must exist a continuous path from @ to W which is in shadow of both a and b. (This requires a lot more definitions to pose it in set notation, but it's still pretty straightforward.)

I think that 6 & 6b pretty much eliminate all the proposed models but the diamond algorithm applied to diamond (or cylindrical) shaped wall grids. (Model 5a in my earlier post.) I will try to prove it...

BTW: 6 does not imply that ASC's will work, but only if the monsters are smart:

```text
##########
#W#D#P# #
##U#R#@# #
##########
```

Depending on permissiveness, T(D,@), or even T(W,@), but not T(R,@). Cutting down on possibilities, 4a is a model for T(D,@), but not for T(W,@).

4b is a model for both T(D,@) and T(W,@) (and any (10) similarly situated monsters, up to d(M,@) <= 20 spaces away.)

### Intentionally unsymmetrical

---

[Suggested by will_asher](http://angband.oook.cz/forum/showpost.php?p=20806&postcount=42)

Monsters caught in open hallway have no where to hide. Intelligent @'s can peek round corners without being spotted.

Fig 25. @ can see M, but M can't see @.

```text
    #.#
#####@#
..M...#
#######
```

## Other points for consideration

---

### Cases and examples

---

- Should @'s and M's have an infinite field of view?

Fig 26. Should @ see M ? How long should maximum sight range be?

```text
#################################################################################################
.@.............................................................................................M.
#################################################################################################
```

Special cases for walls (etc.)

- [Digital FOV](http://roguebasin.roguelikedevelopment.org/index.php?title=Discussion:Field_of_Vision#Digital_FOV) and [half-width walls](http://roguebasin.roguelikedevelopment.org/index.php?title=Discussion:Field_of_Vision#Half-width_walls.2C_center_to_center).

Fig 27. @ can see the wall, and monsters in the wall, for as far as it goes.

```text
################################################################################################
.@..............................................................................................
```

-[Monsters occupy half the width](http://roguebasin.roguelikedevelopment.org/index.php?title=Discussion:Field_of_Vision#Monsters_occupy_half_the_width.2Fheight_of_grid) and [Center to Center, subdivided grid](http://roguebasin.roguelikedevelopment.org/index.php?title=Discussion:Field_of_Vision#Center_to_Center.2C_subdivided_grid)

Fig 28. @ can see #, % are walls he can't see, ? is indeterminate

```text
####?%%%%%%
.@.........
```

The question is whether walls (but not monsters in walls) should be filled in when they are not visible, but are adjacent to room / corridor tiles that are visible and lit.

Fig 29. Special case wall visibility

```text
#####G#####
.@.........
```

@ can't see G (but can see wall G is in).

### Extra visibility properties

---

7 . **Retreating is safe**. If you keep retreating into a corridor, once you are hidden from an immovable monster's sight, you stay hidden; this applies for arbitrarily twisted corridors of width 1 (probably enough to consider digital lines). With symmetry, this implies that retreating doesn't improve your sight, either.

The Kuo Corridor from [Precise Permissive Field of View](precise_permissive_field_of_view.md) is an extreme example in that even an algorithm that checks visibility between all corners and middle edge points of two tiles does not make the retreat safe. The property 7. is only satisfied by algorithms that analyze visibility between continuous sections of each of the two tiles, spanning the width of the corridor, not a small set of points in one of the tiles. Such algorithms are beam casting (e.g, parallel beams starting from diagonals of a tile), Digital FOV (visibility from a cross dividing a tile into 4 squares) and Precise Permissive FOV (visibility from an 'X' dividing a tile into 4 triangles). See <https://github.com/LambdaHack/LambdaHack/wiki/Fov-and-los> for some more context. This property is related to the "shadows without holes" property, but here the holes may actually be between the shadow and a corridor wall, not inside the shadow. In other words, the property states that shadow borders intersect with any corridor at most once on each side of the player. Shadow aligns well with the corridor that casts it.

## See also

---

[Field of Vision](field_of_vision.md)
