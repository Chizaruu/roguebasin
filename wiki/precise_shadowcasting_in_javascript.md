# Precise Shadowcasting in JavaScript

---

This pages describes and explains the Precise Shadowcasting algorithm, developed and implemented by Ondřej Žára in [rot.js](rot.js.md).

## About

---

In a game level comprised of cells, this algorithm computes the set of all cells visible from a certain fixed (starting) point. This set is limited by a given maximum sight range, e.g. no cell at a distance larger than this sight range could be visible.

Cells can be either **blocking** (they are obstacles and stuff behind them cannot be seen) or **non-blocking**.

This shadowcasting is topology-invariant: its implementation is the same in all topologies. There are two basic concepts and tools:

1. **A ring** is a set of all cells with a constant distance from a center.

```text
..x..
.x.x.
x.@.x    Ring 2 in 4-topology (set of all cells with distance=2)
.x.x.
..x..
```

```text
xxxxx
x...x
x.@.x    Ring 2 in 8-topology (set of all cells with distance=2)
x...x
xxxxx
```

2. **Shadow queue** is a list of all angles which are blocked (by a blocking cells). This list is intially empty; as cells are examined, some of them (those who are blocking) cast shadows, which are added to the shadow queue.

## General algorithm workflow

---

1. Let [x,y] be the player coordinates
2. Initialize the empty shadow queue
3. For R=1 up to maximum visibility range do:
   1. Retrieve all cells whose range from [x,y] is R
   1. Make sure these cells are in correct order (clockwise or counter-clockwise; every iteration starting at the same angle)
   1. For every cell in this ring:
      1. Determine the corresponding arc [a1..a2]
      1. Consult the shadow queue to determine whether [a1..a2] is fully shadowed
      1. If no part of [a1..a2] is visible, mark the cell as not visible and advance to next cell
      1. If some part of [a1..a2] is visible, merge it into the shadow queue; mark the cell as visible

```text
.....    Sample scenario (topology 4). Cell "#" [3,2] is blocking. It is the first cell of ring1 and thus adds [-45 .. 45] to the shadow queue.
....b
..@#a    Cell "a" [4,2] is the first cell of ring2 and corresponds to arc [-22.5 .. 22.5]. Since this is a subset of the shadow queue, the cell is not visible.
.....
.....    Cell "b" [4,3] is the second cell of ring2 and corresponds to arc [22.5 .. 67.5]. It is not fully shadowed, so the cell is visible.
```

## Advanced topics: tricks and tweaks

---

### Half-angle backward shift

Determining the proper arc (pair of angles) for a cell can be tricky, as the first cell does not start at angle=0:

```text
.....    Sample scenario (topology 4).  Cell "A" is ring1 => size of arc is 90 degrees. Cell "B" is ring2 => size of arc is 45 degrees.
.....
.@AB.    Incorrect angle assignment: A = [0 .. 90], B = [0 .. 45]
.....
.....    Correct angle assignment: A = [-45 .. 45], B = [-22.5 .. 22.5]
```

### Cutoff and angle wrapping

Once the whole viewing area is shadowed, the algorithm can stop - no further cells can be seen. Detecting this situation can get tricky, based on how the shadow queue is implemented. I decided to implement the shadow queue as a list of monotonously increasing intervals. This presents a problem for cells whose angles contain zeros. A quick fix is available:

- First cell in ring0 corresponds to 90 degrees, i.e. [-45..45] after backward shift.
- Recursively split this into two sub-arcs: [0..45] and [315..360]
- The cell in question is visible when any of these two arcs is visible
- Cutoff happens when the shadow queue contains only one interval, [0..360]

Symbolic angles

To avoid floating point chaos, I decided to represent angle values as rational numbers: fractions of two integers. Furthermore, the whole circle (360 degrees) is represented as 1. How this works:

- First cell in ring1 (4-topology) corresponds to 90 degrees, which translates to 0..1/4
- Backward shift - subtract 1/8: resulting arc is -1/8..1/8
- Angle wrapping/splitting: two arcs 0/8..1/8, 7/8..8/8
- Angle P/Q can be compared to R/S using simple arithmetics: P*S == R*Q (integer equality)

Working with shadow queue

The shadow queue needs to be updated every time a visible AND blocking cell is encountered. Proper management of shadow queue is very important: it is necessary to merge a new arc into the existing list and this computation must be FAST. My implementation works in the following manner:

1. Merging arc [A1, A2] (both A1 and A2 are rational numbers) into a shadow queue
1. Shadow queue (SQ) is a simple JS array of rational numbers [S1, S2, S3, ... Sn]. There is an even number of angles in SQ.
1. Let Index1 be the lowest index of angle in SQ that is >= A1. If no such angle exists, let Index1 = length of SQ
1. Let Index2 be the largest index of angle in SQ that is <= A2. If no such angle exists, let Index2 = -1
1. Let REMOVE = Index2-Index1+1 (number of items in SQ to be removed)
1. If REMOVE is ODD:
   1. If Index1 is ODD:
      1. Example situation: inserting [2, 4] into [1, 3, 5, 6]
      1. SQ.splice(Index1, REMOVE, A2)
   1. Else (Index1 is EVEN):
      1. Example situation: inserting [3, 5] into [1, 2, 4, 6]
      1. SQ.splice(Index1, REMOVE, A1)
1. Else (REMOVE is EVEN):
   1. If Index1 is ODD:
      1. Example situation: inserting [2, 5] into [1, 3, 4, 6]
      1. SQ.splice(Index1, REMOVE)
   1. Else (Index1 is EVEN):
      1. Example situation: inserting [3, 4] into [1, 2, 5, 6]
      1. SQ.splice(Index1, REMOVE, A1, A2)

## Links

---

- [Interactive demo](http://jsfiddle.net/ondras/ycJVj/)
- [rot.js FOV manual](http://ondras.github.com/rot.js/manual/#fov)
- [Source code](https://raw.github.com/ondras/rot.js/master/src/fov/precise-shadowcasting.js)
