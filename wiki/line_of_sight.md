# Line of sight

---

One of the more popular (simple) methods of determining whether something in the game world is visibility (or targetable) is to input the x,y coords for the target and the player and then walk along a line between them. At each step on the line, the game checks to see if anything is in the world at these coords that would prevent a player's sight (like a wall).

There are a couple of articles describing how to do line of sight:

- [Bresenham's Line Algorithm](breshenhams_line_algorithm.md)
- [Another version of BLA](another_version_of_bla.md)
- [Simple Line of Sight](simple_line_of_sight.md)
- [Digital lines](digital_lines.md)
- [LUA 2d Line of sight](lua_2d_line_of_sight.md)
