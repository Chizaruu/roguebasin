# Random Walk Cave Generation

---

The random walk (a.k.a. "drunkard's walk") is a simple algorithm which can be used to generate a somewhat cave-like environment. Generating a level with a single drunkard's walk is guaranteed to produce a fully connected level, with a highly variable mix of narrow paths and open spaces.

For optimal results, this algorithm requires that the map be dynamically resized; without such measures, levels generated in this way can run into the edges with aesthetically unsatisfactory results.

## Pseudocode representation

---

This is a basic pseudocode representation of the algorithm, which abstracts away such issues as dealing with the walk trying to run off the edge.

```text
  initialize all map cells to walls.
  pick a map cell as the starting point.
  turn the selected map cell into floor.
  while insufficient cells have been turned into floor,
    take one step in a random direction.
    if the new map cell is wall,
      turn the new map cell into floor and increment the count of floor tiles.
```

## Examples of output

---

The example layout below was generated using steps in the four traditional cardinal directions only. This algorithm could also be set up to use whatever other step scheme (eight-way, hex, etc.) the author prefers.

```text
######################################
##############.#######################
###########.##...#####################
########..#.##...#####################
#########.........####################
##########........####################
###########......#####################
##########..<...#..###################
######.............###################
####.#..###.###..#.###..##########..##
####.#.####.######......##########..##
####.#.#......###......##########....#
####...###......#......#..#####......#
#####...####....#....#.............###
##.....####........................###
###..######...........#........>.#####
###..#########....#####........#.#####
###..########..#######.......#...#####
####.########..######........#.....###
###....#######.######..##...##....####
###....#######.######..###############
###.........##..######################
###..#..........######################
####........###.######################
######...#############################
######...#############################
######..##############################
#######.##############################
######################################
```

## See also

---

[Dynamically Sized Maze](dynamically_sized_maze.md)
