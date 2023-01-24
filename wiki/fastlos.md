# FastLOS

---

## Introduction

---

FastLOS is an algorithm for rapid approximate line-of-sight and field-of-view calculations. Because it is constant-time per square, it can be used for FOV as efficiently as any FOV algorithm. But its main strength is that it can calculate Line of Sight between any two squares in constant time, so it is the most efficient Line Of Sight algorithm known once the preprocessing is done.

It was designed for monsters but with a larger bitmask gives sufficiently good results that it may be used for the player characters too.

It is approximate. If your map is too complex, your sightmask bitfield too narrow, or your sight radius too large, sometimes you will get 'imperfect' tiles. These are tiles from which some other tiles that ought to be visible in a perfect Line of sight algorithm are not visible in FastLOS.

The precalculation process allows you to mark imperfect tiles. If BOTH cells in an LOS calculation are marked imperfect, AND FastLOS shows no line-of-sight, and the two tiles are within the sight radius of one another, you may check using a conventional LOS algorithm if you need perfection.

It is conservative. It will never reveal squares that ought not be visible.

It is symmetric. If FastLOS shows that A has line of sight on B, then it will also show that B has line of sight on A.

With FastLOS, lineofsight between any two tiles is checked using a distance check and a check of the result of an "and" operation on a bitmask to see if it is nonzero. If both checks succeed, the two tiles have lineofsight on each other. Each tile has a "sight mask" or bitfield that is used for this check. Most of this article is about how to find suitable values for the sight mask for each tile.

It is not well suited for some dungeon layouts. Caves and very complex maps will make it work hard in the preprocessing stage and the results may be noticeably inaccurate without larger "masks" than the 64-bit bitmasks I describe. These problems can be mitigated if sight radiuses are short (< 16 tiles) or if larger bitmasks are used.

The basic concept on which FastLOS is based is the "View Area." A View Area is a set of dungeon tiles having the property that from any tile in the View Area, all other tiles in the same View Area are visible. Any two squares that have Line of sight on each other, have at least one View Area that they are both members of.

Each View Area is assigned its own bit in the sight mask. Thus, when the bitwise AND of the sightmasks of two different tiles has a nonzero result, it means that there exists at least one View Area which both tiles are members of; thus the two tiles have line-of-sight on each other.

The bigger your dungeon map gets, the more View Areas there are. But because sight is limited to a particular range, the bits in the sightmask can be reused for any different View Areas A and B if they are far enough apart that no Line-of-sight could possibly exist between a square in A and a square in B.

The method described below is a heuristic; there may be better heuristics.

## Precalculation

---

Each cell needs a blindmask and a sightmask. These are two bitfields the same size. In a given cell, a bit is mapped to a particular view area (marking that cell as a member of it) if the bit is nonzero in the sightmask. The bit is available for mapping to a View Area if and only if it is nonzero in the blindmask.

Each cell also needs a 'status' variable. This can take three values; perfect, imperfect, and 'generator.'

In order to generate the sightmasks, we have this basic algorithm:

## Algorithm for generating sightmasks

---

```text
Start by setting every sightmask and every blindmask to all-0's.
Set the 'status' variable of every tile to 'generator'.

Repeat

   For all tiles whose status is 'generator',

      generate a Field of view using the current sightmasks
      generate a Field of View using a standard FOV algorithm.
      If they match, set the status to 'perfect'.
      Otherwise count the number of tiles difference.

   End for.

   Pick the 'generator' tile from the above set whose difference is
       largest.

   Use that tile as a generator to generate a View Area. (Algorithm below)

   If there is a zero bit in the blindmasks of all the cells in the view
   area, then
       Set the corresponding bit in the sightmasks of all the cells in the
       View Area.

       Set the corresponding bit in the blindmasks of all the cells within
       sight radius of any cell in the View Area.
   Otherwise
       Set the 'status' of that tile to 'imperfect.'

Until there are no cells whose status is 'generator'.
```

One thing we have to do in generating a View Area is Extended Field of View calculations. This is a standard Field of View, but not limited by sight radius.

In order to generate a View Area we use three lists of tiles: The first is "included," the second is "candidate," and the third is "priority."

## Algorithm for subroutine generating View Areas

---

```text
Initialize the "included" set to include (only) the seed tile.

Perform an Extended Field-of-View Calculation for the seed tile
and use the result as the "candidate" list.

Perform a Field-of-View Calculation for the seed tile using
FastLOS (limited to sight radius) and any bits already assigned.
The set of tiles in the Candidate list which are NOT in this
list is the "priority" list.

Repeat

   If there are any "Priority" cells left:

      For each "Priority" tile, find the sum of its distances from all
          the currently "Included" tiles.

      Add the highest-scored (most distant) "Priority" to the "Included"
         list and remove it from the "candidate" and "Priority" lists.
   else

      For each "Candidate" tile, find the sum of its distances from all
          the currently "Included" tiles.

      Add the highest-scored (most distant) "Candidate" to the "Included"
          list and remove it from the "Candidate" list.

    End If

   Find the set of tiles that is the Field of View for the new tile.

   Set the Candidate list to be set of tiles that are members of both
       the current Candidate List and the new tile's FOV.

   Set the Priority list to be the set of tiles that are members of both
       the current Priority List and the new tile's FOV.

Until the Candidate List is empty.

The "included" list now contains all the tiles in the generated View Area.
```
