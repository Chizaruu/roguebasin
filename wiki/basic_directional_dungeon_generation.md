# Basic directional dungeon generation

---

This article was written mainly based on roguelike dungeon generation, but also applies to other tile-based games, possibly.  

## Overview

---

This algorithm is meant to be small, quick, easy to understand or extend, but also meant to generate semi-realistic cave-like terrain. The algorithm is directional, and creates caves that follow a specific direction generally (but can be extended to be omni-directional). For the purposes of this article, down-towards-up is chosen. The basic algorithm will create a simple down-towards-up path, winding to straight.  

The article will aim to provide the concepts, rather than code or pseudo-code.  

## Initialisation

---

To begin with, we will define some variables which will help set what type of cave we create with our generator. Thus we have:  

```text
 length = How long the cave should be (in tiles). This can be either fixed (cave ends at THAT point exactly), or not fixed
(cave CAN end after that point).
 roughness = How much the cave varies in width. This should be a rough value, and should not reflect exactly in the level created.
For simplicity's sake, roughness can go from 1 to 100.
 windyness = How much the cave varies in positioning. How much a path through it needs to 'wind' and 'swerve'.
This should also be, for example, 1 to 100.
```

You can either define these as a global variable, a variable of the map generator, or even a variable of the map.  

From here on, I'll assume the mapping system works like this: Each tile is either a wall or an empty space. Each tile has an x and an y value. The map starts out completely filled out with walls, or you do this on your own prior to generation. You have a function for adding a rectangle filled with either walls or empty spaces (which, when used with the smallest rectangle possible, is used on one single tile).  

For convenience, x increases left to right, and y increases bottom to top.  

## The basic algorithm

---

Pick out the initialisation variables. Length, roughness, windyness. Pick out a starting x and a starting y. If you have no extra additions to the generator that require a different setting, it's generally best to pick the starting x as half the width (in the middle of the map) and the starting y as 1 or 2 (near the bottom of the map). Pick out a starting width. The width can be random, anywhere between 3 and almost as wide as the map itself. Either carve out a small spawning room (an entrance), or place a line at starting x, starting y, wide as the starting width. Thus, you're placing a rectangle of empty space, at starting x, starting y, up to starting x + starting width, starting y. Create 3 new variables: x, y and width. These take the values of starting x, starting y, and starting width.  

This is the entrance and init part of the algorithm. The following is the iterative part, which repeats until the map is compliant with your requests (in this case, only length).  

Increment y by 1. This moves us up a line, so we're working on the one above the starting one on the first step. Check if a random number out of 100 is smaller than or equal to roughness. If it is, roll a random number between -2 and 2 (excluding 0). Add this number to the current width. If width is now smaller than 3, set it to 3. If larger than the map width, set it to the map width. Check if a random number out of 100 is smaller than or equal to windyness. If it is, roll a random number between -2 and 2 (excluding 0). Add this number to the current x. If x is now smaller than 0, set it to 0. If larger than the map width-3, set it to the map width-3. Place a rectangle from current x, current y, to current x + width, current y.  

This is where you can add any by-row events you need for the generator (a chance of a secret exit appearing on a left-most or right-most wall, or adding pillars in the middle of the cave).  

Check if the current y - starting y matches the desired length. If so, end the iterative part. Increment the starting y again, and add a small room or rectangle which is the 'exit'.  

The following bit is the post-processing part. This is where you can do various other checks you might need for your map. Counting tiles, adding monsters, items, per-level secret areas. In the basic algorithm, there is no need for this, but I included this for extensibility.  

## Results

---

You end up with a semi-realistic looking cavern with a directional element. Your player will have to navigate a route through it towards a specific spot. This might seem bad for exploration, but knowing the direction you are supposed to go in (but exploring side-passages) is at points more enjoyable than having too many directions available for exploration.  

## Ideas/Tweaks/Extensions

---

One thing you can add is a complexity number. Thus, for a complexity number of 1, the entire algorithm is run once. For 2, it's run twice on the same map. Thus, you occasionally get 2 paths that intersect, walk along each other (or on top of each other), diverge, and connect in interesting fashions. For 3 it's run thrice. Higher numbers generally have a cave complex enough that the directional element matters little.  

Another addition is running it sequentially like for complex maps, only with different directional elements. Run it once top-down, once left-right, and you have a cave that has no consistent direction to it, but at least some available for the player to choose from.  

## Example

---

```text
 ##################################################################
 ##################################################################
 ##########################.....###################################
 ###########################...#################################### 
 ###########################...####################################
 ###########################...####################################
 ##########################......##################################
 #########################.........################################
 ########################.........#################################
 ##########################......##################################
 ########################....######################################
 #######################.....######################################
 ####################.....#########################################
 ###################......#########################################
 ######################...#########################################
 ########################...#######################################
 #######################....#######################################
 ######################.....#######################################
 #######################...########################################
 #######################...########################################
 ######################.....#######################################
 #######################...########################################
 #######################...########################################
 #######################...########################################
 ######################......######################################
 #####################.........####################################
 ####################.........#####################################
 ######################......######################################
 ####################....##########################################
 ###################.....##########################################
 ################.....#############################################
 ###############......#############################################
 ##################...#############################################
 ####################...###########################################
 ###################....###########################################
 ##################.....###########################################
 ###################...############################################
 ###################...############################################
 ##################################################################
```

This is an example of a short, medium roughness, medium windyness cave. The cave is extremely short, for easy displaying.  

This algorithm is used in the roguelike [Avendesora](avendesora.md). A more updated version of this article might be available [here](http://remike.homelinux.org/dungeon-generation-in-avendesora).
