# Dungeon-Building Algorithm

---

(Originally written by [Mike Anderson](mike_anderson.md).)

Interesting random maps are one of the things that make roguelike games unique. They add greatly to the enjoyment of the game since the player will always be able to face fresh challenges and different problems.

But random maps are far from easy to implement. In most conventional games, you would have a level designer who can create cunningly crafted scenarios. In any roguelike worthy of the name, the programmer must take on the daunting task of creating a "virtual level designer" which will be able to build unlimited numbers of interesting dungeons and mazes with nothing more than a [random number generator](random_number_generator.md)......

In this article, I've documented the technique that I am developing for use in my own little roguelike called Tyrant. I doubt it's a truly original idea, but I have never seen this exact algorithm used for creating dungeons before. Anyhow, it works pretty well so I've decided to share it with the world.

## The goals of the dungeon builder

---

When writing any piece of code, it's always useful to decide on the objectives beforehand. This can help a lot when designing an algorithm, even if you end up making a lot of changes later.

A basic dungeon will need:

- A set of interconnected rooms, doors and tunnels
- An entrance (staircase up)
- An exit (staircase down)
- Every space must be reachable

The last condition is quite important; it's nice to know that the player will always be able to get through the level with enough perseverance, and it's also useful to know that when you add a unique artifact, it's not hidden away in some impenetrable cell.

## The Plan

---

When I came to write my dungeon builder for Tyrant, I decided to play with a whole host of different algorithms to see what I liked best. The one I will present here is the best one that I came up with, which is currently being implemented in the actual game.

The inspiration came from the thought:

"If I were a denizen of the underworld, how would I go about building my dungeon?"

One certain thing was that rooms wouldn't just appear in a nice uniform fashion and then have long snaking tunnels appear to join them together. No, when I needed more space for my goblin lunatics I'd just grab a pickaxe and dig a big hole. Add on a few new rooms when they were needed. Probably in a fairly haphazard way.

Some dungeon lords might be a bit more imaginative with a few portcullises, traps and suchlike to guard the more "interesting" rooms. But the basic idea was the same. Start with a small dungeon, then add extensions in all directions until the whole thing is finished.

## The algorithm

---

In this algorithm a "feature" is taken to mean any kind of map component e.g. large room, small room, corridor, circular arena, vault etc.

1. Fill the whole map with solid earth
1. Dig out a single room in the centre of the map
1. Pick a wall of any room
1. Decide upon a new feature to build
1. See if there is room to add the new feature through the chosen wall
1. If yes, continue. If no, go back to step 3
1. Add the feature through the chosen wall
1. Go back to step 3, until the dungeon is complete
1. Add the up and down staircases at random points in map
1. Finally, sprinkle some monsters and items liberally over dungeon

Step 1 and 2 are easy once you've got the map set up. I have found it very useful to write a "fillRect" command that fills a rectangular map area with a specified tile type.

Step 3 is trickier. You can't pick random squares to add new features because the rule is to always add to the existing dungeon. This makes the connections look good, and also guarantees that every square is reachable. The way Tyrant does it is to pick random squares on the map until it finds a wall square adjacent (horizontally or vertically) to a clear square. This is a good method, since it gives you a roughly even chance of picking any particular wall square.

Step 4 isn't too hard - I just use a random switch statement to determine which of a range of features to build. You can change the whole look of the map by weighting the probabilities of different features. Well-ordered dungeons will have lots of regular rooms and long straight corridors. Cave complexes will tend to have jagged caverns, twisting passages etc.

Step 5 is more tricky, and the key to the whole algorithm. For each feature, you need to know the area of the map that it will occupy. Then you need to scan outwards from the chosen wall to see if this area intersects any features that are already there. Tyrant does this in a fairly simplistic way - it just works out the rectangular space that the new feature will occupy plus a square on each side for walls, then checks to see if the entire rectangle is currently filled with solid earth.

Step 6 decides whether or not to add the feature. If the area under consideration contains anything other than solid earth already, then the routine loops back to step 3. Note that _most_ new features will be rejected in this way. This isn't a problem, as the processing time is negligible. Tyrant tries to add 300 or so features to each dungeon, but usually only 40 or so make it past this stage.

Step 7 draws the new feature once you've decided the area is OK. In this stage, you can also add any interesting room features, such as inhabitants, traps, secret doors and treasure.

Step 8 just loops back to build more rooms. The exact number of times that you want to do this will depend on map size and various other factors.

Step 9 is pretty self-explanatory. Easiest way to do it is to write a routine that picks random squares until it finds an empty one where the staircases can be added.

Step 10 just creates a host of extra random monsters in random locations to add some spice. Tyrant creates about most of the monsters at this point of the map generation, although it does add a few special creatures when individual rooms are generated.

That's it. Hey, it's all in pseudo-pseudo-code, but feel free to mail me if you want a specific implementation example. Specific means Java, in this case....

## Example

---

Well, after going through the whole process in laborious detail, I guess it's useful to do an example. Just to see how it all fits together.

Key:

```text
# = Floor
D = Door
W = Wall under consideration
```

1. The first room

```text
#####
#####
#####
```

2. Select a random wall

```text
#####
#####W
#####
```

3. Scan area for new corridor (including space on all sides)

```text
#####**********
#####W*********
#####**********
```

4. It's clear, so add new feature

```text
#####
#####D########
#####
```

5. Pick another wall:

```text
#####     W
#####D########
#####
```

6. Scan area for new Room

```text
       ******
       ******
       ******
       ******
       ******
#####  ***W**
#####D########
#####
```

7. Area is OK, so add new room. Throw in a chest (C) for good measure

```text
        ####
        ###C
        ####
        ####
#####     D
#####D########
#####
```

8. Add another corridor as before

```text
             #
             #
        #### #
        ###C #
        #### #
        #### #
#####     D  #
#####D########
#####
```

9. This time, we try to add corridor to the second room.

```text
             #
             #
        #### #
        ###C*******
        ####W******
        ####*******
#####     D  #
#####D########
#####
```

10. This fails since the area being scanned is already used.

```text
             #
             #
        #### #
        ###C #
        #### #
        #### #
#####     D  #
#####D########
#####
```

11. Fancy features. Add an octagonal room

```text
             #
             #   ###
        #### #  #####
        ###C # #######
        #### #D#######
        #### # #######
#####     D  #  #####
#####D########   ###
#####
```

12. A secret door hides a fiendishly trapped corridor:

```text
             #
             #   ###
        #### #  #####
        ###C # #######S###T##TT#T##
        #### #D#######
        #### # #######
#####     D  #  #####
#####D########   ###
#####
```

13. Hey, I could go on and on...

## Conclusion

---

Well, that's my algorithm. I hope you find it useful, or just an interesting perspective on how to solve this particular problem.

## C++ example

---

[C++ Example of Dungeon-Building Algorithm](c++_example_of_dungeon-building_algorithm.md)

## C# example

---

[CSharp Example of a Dungeon-Building Algorithm](csharp_example_of_a_dungeon-building_algorithm.md)
