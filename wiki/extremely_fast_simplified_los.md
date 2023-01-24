# Extremely fast simplified LOS

---

```text
Extremely fast simplified LOS - Radomir Dopieralski [sheep@atos.wmid.amu.edu.pl].txt

Have you noticed that most of your dungeons are just a bunch of rooms
and corridors connected with single-cell doorways?

It's usually something like this:

########
#......#         #########
#......###########.......#
#......'.........'.......#
#......#####'#####.......#
#### ###   #.#   ###'#####
 #....#    #.#####.....#
 #....#    #..... .....#
 #....#    #######.....#
 ######          #######

And it's the more complicated version, where you can have tw rooms without
corridor in between them. You can easily save the structure of rooms during
level generation -- room sizes, positions, door positions, etc.

You can use some an extremely simplified LOS algorithm for such levels,
provided you do save the room and door positions, your rooms are all convex,
your corridors are split into straight parts, and your doors are all 1 cell
wide. The results are not exactly perfect, but if you're using

First of all, check the distance between the player and the target. If it's
greater than maximum sight distance -- the target is not visible. If you're using
the `octagonal' metric this calculation should be pretty fast.

Now, determine which room is the player and the target cell in. If they are
both in the same room, the target is visible.
If the player or the monster stands on the doorway, it counts as if they were
standing in both rooms the doorway separates.

This is it, if you have `autoclosing' doors. But who would want such a thing? ^^))

So, now you must check whether the rooms are connected with any doorways. If not,
the target cannot be seen (it's not very good, more about it later). If there's
such a doorway, you must check whether the target is visible through this doorway.
There might be more than one doorway to check.

We've got several possible cases, I'll asume that the doorway is a vertical one
and the player is on the left of it (other cases are symmetrical).

1) The player, the doorway and the target are at the same height.
.....#.....
.....#.....
.@...'...x.
.....#.....
.....#.....
No calculations needed, target always visible.

2) The player and the target are on the same side of doorway
.@...#.....
.....#..x..
.....'.....
.....#.....
.....#.....

.....#.....
.....#.....
.....'.....
.....#..x..
.@...#.....
No calculations needed, target always not visible.

3) The player at the same height as doorway
.....#.....
.....#.....
.@...'.....
.....#.....
.....#.x...

.....#.....
.....#.....
...@.'.....
.....#....x
.....#.....

You need to calculate the slope of lines going from the player to
the doorway's sides, and from the doorway's sides to the target.
Pick the right corners of cells, especially if you want `peeking
around corner'. Compare the slopes to determine visibility of the
target. If done right, you can do it using several additions and
two multiplications, with integer variables.
The exact formulas are left for the reader as an exercise ^^)))


3) The player and the target on opposite sides of doorway
.....#.....
@....#.....
.....'.....
.....#.....
.....#.x...

...@.#.....
.....#.....
.....'.....
.....#....x
.....#.....

...@.#.....
.....#.....
.....'.....
.....#.x...
.....#.....

Here you do basically the same as with the previous case, but you
must pick the corners carefully based on which of target and player
are closer to the doorway. For some angles of player-doorway lines
you don't need to calculate the taget-doorway lines, becasue the
player is unable to see anything through the doorway.

That's all. I'm sorry I don;t give you exact formulas, but I would make
mistakes in them for sure anyways.  I think this algorithm is pretty
good if you want your monsters to have FOV, because it's fast and doesn't
need to be very accurate.

You use it even when your level is represented only by room sizes and
connections -- without a cell grid.

You can improve it to make it possible to see further than one room --
just check any visible doorway with the same direction. This again isn't
accurate, but cases like this are rather rare:

        ####
####### #.x#
#.....###..#
#.....'.'..#
#.....######
#.@...#
#.....#
#######

Unless you don;t display the whole FOV, noone will really notice ^^))). And
you can fix this bug by remembering the slopes from the first doorway and
taking minimum (maximum).

You can use this algorithm even if some of your rooms are caverns and are
not necessarily convex -- you just need a IS_COVEX for every room, and some
different LOS/FOV algorithm for the rooms without this flag. Yes, yes, this
further complicates things, but if you really need some speed...

In addition, any pathfinding is also very simple -- you use Dijkstra or A*
or anything on the graph of rooms, and inside the room -- straight line if
it has IS_CONVEX flag set and some A* again if not.

--
Radomir `The Sheep' Dopieralski
```
