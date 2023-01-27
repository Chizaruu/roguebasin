# Pre-Computed Visibility Tries

---

## Introduction

---

Pre-Computed Visibility Tries (PCVT) is an algorithm for calculating field of view. It exploits the idea that for the chain of cells A->B->C->D representing a line of sight, if cell B blocks line of sight, then cells C and D do not have to be checked for visibility. The algorithm is divided into two parts: a precalculation step which establishes the visibility relationships of cells (visibility chains), and a processing step which finds the visible cells for a given frame.

## Precalculation

---

This step determines which cells can be skipped when a cell in the view blocks line of sight. These relationships form a trie such that if a parent node blocks line of sight, its children are not visible. The positions are encoded in player-space i.e.: with the player positioned at cell (0,0) and lines of sight extending outward radially. We will have to transform between player-space coordinates and world-space coordinates when calculating the set of visible cells.

### Algorithm

We need to generate the set of all lines starting with the player at (0,0) and extending outward. Each cell in the field of view must be covered by at least one of these lines or it will not be present in the set of visible cells. Naively generating a set of lines from (0,0) to the points on a circle with radius r does not yield full coverage.

Cells without coverage shown in one quadrant:

![Cells without coverage shown in one quadrant](./assets/images/articles/pre-computed_visibility_tries/3urfNXC.png))

Instead, the lines from (0,0) to the points on a square (with sides of length r/2) does.

Full coverage:

![Full coverage](./assets/images/articles/pre-computed_visibility_tries/UvHDYw8.png)

#### Pseudocode

```text
r = maximum view distance
visibility_trie = empty_trie()
points = points_on_square(r/2)
for p in points
    line = line((0,0), p)
    los = remove_points_farther_than_distance(line, r)
    visibility_trie.add(los)
```

Now the visibility trie contains all of the chains of points radiating outward from the center at (0,0) and ending at distance r.

## Finding Visible Cells

---

To find the coordinates of the visible cells, we need to visit each node in the visibility trie in pre-order traversal. When visiting each node, we will add it to the set of visible coordinates, and if it does not block line of sight, we can visit each of its children. If it does block line of sight, we can skip all of its children!

### Pseudocode

```text
visible_coords = empty_set()

procedure visit_node(node)
    cell_coord = player-space-to-world-space(node)
    visible_coords.add(cell_coord)
    cell = get_cell(cell_coord)
    if cell does not block line of sight
      for each child in node
        visit_node(child)

visit_node(visibility_trie.root_node)
```

Cells blocking visibility:

![Cells blocking visibility](./assets/images/articles/pre-computed_visibility_tries/cScZWHk.png)

## Conclusion

---

PCVT's speed is inversely proportional to the number of visible cells. The more cells that block line of sight (especially near to the player) the faster the algorithm performs.

This algorithm can be adapted to allow for variable view distance. In that case, several visibility tries may be precomputed, one for each value in the range from min view distance to max view distance. When finding the set of visible coordinates, use the appropriate visibility trie corresponding to the current view distance.

## Implementations

---

Lua and C++ implementations of a symmetric version of PCVT can be found at <https://github.com/denismr/SymmetricPCVT> . Additionally, they provide fast and matching LOS.

An implementation of PCVT in javascript is available at <https://sokol815.github.io/tests/los/>.
