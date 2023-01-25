# Creating Measurably "Fun" Maps

---

This idea is not originally mine. I read about it several years ago in a paper an undergraduate student had written in England. I forget the college, the student's name, the title of the paper and even the year I read it. It had something to do with automatically generating Quake maps which had measurable "fun" ratings using a level description language and a special compiler which generated dungeons.

I chose to disregard the compiler/grammar topics because the underlying idea of the paper was so appealing without it. Basically the premise was this:

**Q. What makes a dungeon level _NOT_ fun?**

**A. If the player can "beat" the level without having to explore all the areas you as the programmer have created for them and avoids all the tough monsters and traps and just gets to the "end" of that dungeon level with no effort.**

To prevent that kind of problem, this approach measures the amount of "fun" to be had in a dungeon by the percentage of the dungeon that the player must explore (or wants to explore) before that level can be "beat".

For my game [ADND](adnd.md), I actually implemented the algorithm I describe below and use it to very good effect to create mission-style dungeons.

First of all, a little bit of graph theory is necessary, so read up about Dijkstra's shortest path algorithm and find a good fast version of it.

The basic premise is to treat the dungeon level as a non-directed graph, where each "cell" is a node and the connections between the "cells" are the edges. You should have a version of DSP (Dijkstra's Shortest Path) that when given an (x, y) coordinate of the start and end cells, will return the list of (x, y) coordinates that trace the shortest path from (x_start, y_start) to (x_end, y_end) (if one exists).

This function is going to be your "tool" for implementing the rest of this algorithm.

First connect the dungeon beginning to the dungeon ending. The beginning is generally the cave entrance or the stairs up or wherever the player begins that dungeon level at. The end cell is up to you to define. Connect these two cells using DSP and stash the returned path. Now you have two sets of nodes. Nodes on the path (path nodes) and nodes not on the path (pointless nodes). The key idea is to "solve" the pointless nodes such that the player either wants to or has to visit most of those pointless nodes (thereby adding them to the path nodes).

There are many ways to "solve" pointless nodes. Before we discuss them, you must group the pointless nodes into pointless areas. A pointless area is a collection of all pointless nodes that can reach each other without crossing any path nodes. For example, if your dungeon is a square and the start node is at the bottom left and the goal node is at the top right, then the path is a straight diagonal line from left to right and it creates two large pointless areas (the triangle above the path and the tiangle of nodes below the path).

Anyway, perform the following actions in a loop until all pointless areas have less than some threshold of pointless nodes and then call it a day.

1. Select the largest pointless area (the one with the most number of pointless nodes).

2. "Solve" that pointless area by

```text
    a. Selecting the node with the greatest distance from any path node
    b. Performing one of the following actions (feel free to dream up others):
          i. Depending on the size of the pointless area, drop a small, medium or large reward in this pointless node.
         ii. Pick a node on the path that borders this pointless area and block the path with a door. Place
             the key to the door in this pointless node. (replace key/door with anything you want - ADND
             uses levers/gates).
        iii. If the size of the pointless area is fairly small, it can simply be marked "solved" without doing anything.
         iv. Place a nasty trap or other red-herring in the cell to keep the player guessing.
    c. Recompute the path from dungeon start to dungeon end, such that it now first visits this "solved" node.
       This will create a cycle in your graph (and you must figure out how to deal with that) and it should also
       remove this node and several others from the pointless list.
```

3. Now recompute the pointless areas and sort them by size.

4. As mentioned earlier, if the pointless area with the largest size (number of pointless nodes) is less than a threshold, than the algorithm is complete.

Well, that's the basic idea. In my game [ADND](adnd.md), I very rarely perform completely random dungeon stocking. I always run through the above algorithm and everything you meet in the dungeon was placed in a certain spot to "solve" some group of pointless nodes. The same idea can be applied to wilderness areas, underwater areas or what have you.

A final thing I did with my implementation was to "theme" my dungeons by creating the following inhabitant categories:

```text
a. Dungeon Leader
b. Dungeon Owners
c. Dungeon Guards
d. Dungeon Pets
e. Vermin
```

The stocking algorithm above can decide to "solve" a pointless node by placing a rather large treasure coupled with say, 2 Dungeon Owners and a Dungeon Pet. Now if this is a Kobold Grotto, then the Dungeon Owners will naturally be Kobolds and the Pets might be Jackals. If the dungeon is a Graveyard, the owner's might be ghouls or wights and the pets might be skeletons or zombies. You can easily sit down and write out about 6-10 different "themes" and just select 1 or 2 candidates for each of the 5 categories above, stick it all in a random table and voila -> automatic and intelligent dungeon stocking.

Two final notes:

1. I always placed the Dungeon Leader and several guards, owners and pets at the goal node, but you might choose some other configuration.
2. You may have noticed from the algorithm that all key/door combinations will be generated in such a way that the player is guaranteed to be able to find the key to open a particular locked door without it accidentally being on the inaccesible side of the door. It's really cool to watch a player scour the dungeon trying to find the key to a locked door and swearing at you the whole time that "There is no key! I can't find it...it must be a bug, it must be on the other side of the locked door", only to finally find it after examining virtually every accessible square and then sheepishly smile and say "Cool, now I can open that door and see what's behind it!".

_If you enjoyed this topic or want the Binary for ADND, please drop me a line at "Steve Segreto -> SFSEGRETO@HOTMAIL.COM"_

Update: Thanks to Victor Itzel for reminding about David Adam's paper. He was the author of the great undergraduate paper that lead me to this algorithm. Thanks David!

You can read David's original paper at: <http://www.dcs.shef.ac.uk/intranet/teaching/projects/archive/ug2002/pdf/u9da.pdf>

Alternative link: <http://www.google.com/url?sa=t&rct=j&q=&esrc=s&frm=1&source=web&cd=1&ved=0CB4QFjAA&url=http%3A%2F%2Fciteseerx.ist.psu.edu%2Fviewdoc%2Fdownload%3Fdoi%3D10.1.1.119.1445%26rep%3Drep1%26type%3Dpdf&ei=vJmJUP3uE6KRiQKdnYG4BQ&usg=AFQjCNFBfFZIXvL6XOJqLuLPwHlMhznX0Q>

Mirror PDF: <http://xenophule.com/girscloset/Dungeon_Procedural_Generation.pdf>
