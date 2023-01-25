# Quick Pathfinding in a Dungeon

---

```text
by Pieter Droogendijk
gin@binky.homeunix.org

This document describes a pathfinder, using a breadth-first, heuristicless algorithm.

In pathfinding, we start at the origin, and try to find the shortest way to the destination. We do this by counting for each square how far it is from the origin, and once we've reached the target, we trace back through the lowest numbers. This may sound confusing, but consider this:

8767D9876  D is the destination
765678765
654567656
##3####4#  # is a wall (impassable)
4321S1234  S is the start (origin)

The numbers represent the minimum amount of steps taken to get from the source to the given square. If we now trace back through the lowest numbers:

  67D
  567
  4
##3#### #
  21S

We can derive a path:

    D
  ...
  .
##.#### #
  ..S

which is what we want. One could also allow diagonal movement (which is what most rogue-like pathfinders will want), or let it be dependant on the terrain. It's the programmer's choice.

What follows is a way one could implement this algorithm. A 'square' is a coordinate on the map. This implementation will use the dungeon map, and a stack.
In a stack, 'push' is to push a value to the top, 'pop' is to pop the value last pushed off the stack, and 'shift' is to shift the value first pushed (reverse pop).

1: Origin is now the current square.
2: select an adjacent square who's still set to 0.
   if there's none, goto 5.
3: give the selected square a value of the current square +1.
   if the selected square is the target, goto 7
4: push selected square onto a list of unfinished squares. goto 2.
5: shift the first value from the unfinished list, make it the current square.
6: goto 2.
7: Target is now the current square.
8: select an adjacent square.
9: if selected square's value is current square -1, set it to the current square.
   if it's not, goto 8.
10:store the position of the current square relative to the selected square
   (Nort, East, South, West etc.)
   if it's the origin, goto 12 after that.
11:goto 8.
12:return list of directions


By not adding 'terrain-type weight' (one type takes more 'steps' to cross) and a heuristic function, we don't have to check if a pushed square already exists in the stack. The fastest way to the next square will always be the one that's there first. Not having to scan the stack every step saves a lot of cycles.

If we had a heuristic function, two stacks, and searching both of the stacks every cycle, We'd only lose a shitload of cycles, since there's a great possibility that there's not even a path that leads to the target! In other words, I believe that this is a very good algorithm which, even though it scans all squares reachable (unless it finds the target first), it does so FAST.


pathfinder demo:
..........#.........#################################.
....XXXXXXX.........########################........+.
...X......#XXXXXXXXXXXXXXXXXXXO######################.
..X.......#.........#####################.###########.
#X#########.........#####################.############
#X#########.........##########@XXXXXXXXXX.####...../..
#X##......+.........#####################X##########..
#X#############+######.......############.XXXXXXXXXX..
#X######.....+.......#.......############.##########X.
#X############.......#.......#######################X.
#X############......./.......#.......####.....XXXXXX..
+X############.......#.......#.......########X######..
#X############.......#.......#.......####...X.....####
#X####################.......#.......####..X......####
#X####.........###############.......####..X......####
#X####.........#.............#.......####..X......####
#X####.........#.............#.......####..X......#...
+X####.........#.............#.......######X#######...
#X####.........XXXXXXXXXXXXXXXXXX....###..X....####...
#X####........X#.............####X######.X.....####...
..XXXXXXXXXXXX.#.............#....XXXXXXX......####...
######.........#.............###########.......#######
```
