# Abstract Dungeons

---

## Introduction

---

Abstract dungeons are a nice method for reduced try and error dungeon generation. They help designing more higher level features and reduce randomness in a way which could make the game much more interesting for the player.

This page is somewhat related to [Grid Based Dungeon Generator](grid_based_dungeon_generator.md).

### Terms

**Area** usually means a rectangle or generally a polygon which is used to define the borders of any part of the map. Convex areas are the easiest to process later on.

**Cell** descibes a single tile of the resulting map. This could even be an abstraction of the real map.

**Gateway** is a cell which serves as a final connection between two areas.

**A\*** is a pathfinding algorithm. A\* could be replaced with a more appropriate method but it already works very well.

### Benefits

- fast dungeon generation
- almost WYMIWYG
- reduced trial and error
- same dungeon can be painted with different styles
- easy rejection of unwanted dungeons layouts

### Drawbacks

- based upon the layout-algorithm maybe not good looking distribution

## Abstract layout

---

The first thing to do is do create the basic layout of our map. The layout consists of many areas which pieced together form the big map. The actual generation can be done in several ways, some of them i'll mention here:

### Uniform grid

This is the approach used in [Grid Based Dungeon Generator](grid_based_dungeon_generator.md) and perhaps many other games. The map is divided into a grid of areas, resulting in a mostly aligned dungeon layout.

Afterwards some areas can be joined together to make different shapes.

This grid can be a general starting point and may be usefull for more artificial maps, like a building, space stations and starships.

### Subdivision

Subdivision is somewhat more elegant as it results in a more uneven distributed layout. It starts by dividing the whole map into 2 smaller areas, and repeats the step for each new area until a specified area condition is met.

With this approach i usually pass parameters like minimum width/height/area as conditions. Furthermore i'm sometimes dividing areas into even distributed grids (3x1, 3x3 ...), so that a area is not always splitted into two minor parts.

Subdivision is usable for most fantasy dungeons.

### Custom evolving strategy

You could also implement a custom strategy for map layout. A nice example would be to start with a long corridor and attaching symetrical areas to the borders of already existing ones.

This could also result in nice symetric spaceship design.

Remember, each of this algorithm forms only a basic layout and can freely be replaced by other methods.

## Knowing your neighbours

---

After the map is created, each area is checked for neighbours. This is rather straightforward for rectangles, a example quick and dirty implementation could be as the following (D Code):

```d
private void getAreaNeighbours() {
  for (int source=0;source<_areas.length;source++) {
    for (int test=source+1;test<_areas.length;test++) {
      Area s = _areas[source];
      Area t = _areas[test];

      bool found = true;
      if (
        (s.x2 < t.x1) ||
        (s.y2 < t.y1) ||
        (s.x1 > t.x2) ||
        (s.y1 > t.y2)
        ) {
        found = false;
      }

      // do additional processing
      if (found) {
          // at least 2 cells shared by both areas
       bool testLines(int p1, int p2, int s1, int s2) {
         if (
         ((s1 >= p1) && (p2-s1>2)) ||
         ((s1 <= p1) && (s2-p1>2))
         ) {
           return true;
         }
         return false;
       }

       found = false;

       if (s.x1 == t.x2)
         found |= testLines(s.y1, s.y2, t.y1, t.y2);

       if (t.x1 == s.x2)
         found |= testLines(s.y1, s.y2, t.y1, t.y2);

       if (s.y1 == t.y2)
         found |= testLines(s.x1, s.x2, t.x1, t.x2);

       if (t.y1 == s.y2)
         found |= testLines(s.x1, s.x2, t.x1, t.x2);
      }

      if (found == true) {
        s.addNeighbour(t);  // adds s as a neighbour to t, too
      }
    }
  }
}
```

The above code also guarantees that two areas do not count as connected if they just touch by corners. The neighbours should be stored related to the area.

An arguably better approach is to only test against other rectangles that share an abscissa (x1, x2) or ordinate (y1, y2) with the tested rectangle. This information can be gathered while the rectangles are created so no additional loop is required. The data can be stored by having two maps, one for abscissas and one for ordinates. The map contains sets of rectangles, which can be accessed by an abscissa/ordinate. During creation, rectangles add themselves to up to four sets, using their coordinates as keys. Thus all rectangles in a set share at least one abscissa/ordinate.

```d
abscissas = new Map<int, Set<Rectangle>>();
ordinates = new Map<int, Set<Rectangle>>();

void createArea(...) {
  // ...
  // creating area etc.
  abscissas[area.x1] += area;
  abscissas[area.x2] += area;
  ordinates[area.y1] += area;
  ordinates[area.y2] += area;
}


Set<Rectangle> findNeighbors(Rectangle area) {
  Set<Rectangle> candidates = abscissas[area.x1] + abscissas[area.x2] +
                              ordinates[area.y1] + ordinates[area.y2];

  return every candidate in candidates where Rectangle.isNeighbor(candidate, area);
}

/* Determine whether rectangles a and b are neighbors
 * by projecting them onto both axes and comparing their
 * combined projections ("one-dimensional shadows") to
 * their actual sizes.
 * If a projection:
 *   - is smaller than both rectangles' width/height,
 *     then the rectangles overlap on the x/y-axis.
 *   - is equivalent to both rectangles' width/height,
 *     then the rectangles are touching on the x/y-axis.
 *   - is greater than both rectangles' width/height,
 *     then the rectangles can not be neighbors.
 *
 * Return true iff the overlap on one axis is greater than zero
 * while the overlap on the other axis is equal to zero.
 * (If both overlaps were greater than zero, the rectangles
 * would be overlapping. If both overlaps were equal to zero,
 * the rectangles would be touching on a corner only.)
 */
static bool Rectangle.isNeighbor(Rectangle a, Rectangle b) {
  int xProjection = max(a.x2, b.x2) - min(a.x1, b.x1));
  int xOverlap = a.width + b.Width - xProjection;

  int yProjection = max(a.y2, b.y2) - min(a.y1, b.y1);
  int yOverlap = a.height + b.height - yProjection;

  return xOverlap > 0 && yOverlap == 0 ||
         xOverlap == 0 && yOverlap > 0;
}
```

## Abstract connections

---

Since the map layout is finished and every area knows its neighbours, the hard work can begin.

Usually the map needs a entrance and a exit, no matter if they are implemented as stairs, lifts, teleports or doors to other dungeons. Two areas are marked for that purpose. (Its up to the implementation to chose areas far away from each other)

Now a simple A\* algorithm can traverse the map starting at the area marked as entrance. If it reaches the exit, we have a single, straight and guaranteed route to the exit. The route can be directed by "influence points", which could be spread whole over the map. They add costs for visiting a area by distance to itself. Of course you could give certain rooms additional costs if you want to place a special item there which should not be on the way to the exit.

In the below example a influence point was placed in the middle of the map to avoid a straight connection from start to exit: (The next maps look a bit strange, but believe; after the whole dungeon in generated noone will notice the "linear way to the exit" except the design is forced to)

```text
################################################
#      #    #     #   #   #      #    #        #
#      #    #     #   #   #      #    #        #
#      #    #     #   #   #      #    #        #
########    #     #   #   #      #    #        #
#      ####################      ###############
#      #    #     #       ########       #     #
#      #    #     #       #      #       #     #
#      #    #     #       #      #       #     #
#      #    #     #########      #       #     #
#      #    #     #   #   #      #       #     #
#############     #   #   ########       #     #
#    #      #     #   #   #      ###############
#    #      ###############      #   #   #     #
#    #      #     #       #      #   #   #     #
#    #      #     #       #      #   #   #     #
#    #      #     #       #      #   #   #     #
#    #      #     #       #      #   #   #     #
################################################
#   #   #   #       #       #      #   #   #   #
#   #   # S #       #       #      #   #   #   #
#   #   #   #       #       #      #   #   #   #
#   #   #   #       #       #      #   #   #   #
#########...#########       #      #   #   #   #
#       #      #    #       #      #   #   #   #
#       #      #    ############################
#       #      #    #     #   #    #     #     #
#########      ######     # IP#    #     #     #
#   #   #      #    #     #   #    #  E  #     #
#   #   #......#    #     ##########     #     #
#   #   #      #    #     #   #    #     #     #
#   #   #      #    #######   #    #     #     #
#   #   #      #    #     #   #    #.....#######
#########.....#######     #   #    #     #     #
#      #      #     #     #   #    #     #     #
#      #      #     #     #   #    #     #     #
#      #      #     #################....#######
#      #      #     #    #   #      #     #    #
#      #......#######    #   #      #     #    #
########       .    #    #   #      #     #    #
#      #       .    ##########      #     #    #
#      #       .    .    .   ########     #    #
#      #       .    .    .   .      #     #    #
#      #       .    .    .   .      #.....######
#      #       .    .    .   .      .     #    #
#      #       .    .    .   .      .     #    #
#      #       .    .    .   .      .     #    #
################################################
```

Its up to the levedesign to create a new route, e.g. one with different enemies so that the player could "choose" a path. For that purpose every area established as a route gets additional costs (maybe their neighbours too). This avoids taking the same route twice until A\* can't get any further.

```text
################################################
#      #      #   .   .       #      #     #   #
#      #      #   .   .       #      #     #   #
#      #      #   .   .       #      #     #   #
#      ######## S .   .       ##############   #
########      #   .   .       #      #     #####
#      #      #...#   .       #      #     #   #
#      #      .   #   .       #      #     #   #
#      #      .   #####..#############     #   #
#      #      .   #      #    #      ###########
########....#######      #    #      #     #   #
#      #    #     #      #    #      #     #   #
#      #    #     #......#    #      #     #   #
#      #    #     #      #############     #####
#      #    #     #      #    #      #     #   #
#      #    #     #      #    #      #     #   #
#      #    #     #      #    #      #     #   #
########..#########......#######################
#     .   #   #   #      .    .      #   #     #
#     .   #   #   #      .    .      #   #     #
#     .   #   #   #      .    .      #   #     #
#     #########################      #   #######
#     #   #   #   #      #    #      #   #     #
#     #   #   # IP#      #    #      #   #     #
#     #   #   #   #      #    #      #   #     #
#....##########################.....############
#    #    #    #     #   #   #      #     #    #
#    #    #    #     #   #   #      #     #    #
#    #    #    #     #   #   #      #     #    #
#    #    #    #######   #   #      #     ######
#    #    #    #     #########      #######    #
#    #    #    #     #       #      #     #    #
#    #    #    #     #       #      #     #    #
#....###########     #       #      #     #    #
#      .       ###############......############
#      .       .      #     #       #      #   #
#      .       .      #     #       #      #   #
########       .      #     #       #      #   #
#      #########......#######.......#      #   #
#      #   #   #      #     #       ############
#      #   #   #      #     #       .     .    #
########   #   #      #     #       .     .    #
#      #########      #     #       .     . E  #
#      #       #      #####################    #
#      #       #      .     .   .   .     .    #
#      #       #      .     .   .   .     .    #
#      #       #      .     .   .   .     .    #
################################################
```

Even a mostly linear level with a walkaround for the big fat monster (BFM) could be possible. Just pick a area which features BFM and reroute using a new A\* algorithm; starting some areas before to some areas after the monster.

```text
################################################
#    #     #     #   #    #   #   #   #    #   #
#    #     #     #   #    #   #   #   #    #   #
#    #     #     #   #    #   #   #   #    #   #
#    #     #     #   #    #   #   #   #    #   #
#    #     #     #   #    #############    #   #
#    #     #     #   #    #      #    ##########
###########################      #    #    #   #
#      #   #      #       #      #    #    #   #
#      #   #      #       #      #    #    #   #
# S    #   #      #       #      #    #    #   #
#      #   #      #       #      #    #    #   #
#      #   #      #       ######################
#......#####      #       #   #    #    #      #
#      #   #      #       #   #    #    #      #
#      #   ################   #    #    #      #
#      #   #      #   #   ##########    ########
#...########      #   #   #   #    ######      #
#   .      #      #   #   #   #    #    #      #
#   .      #      #   #   #   #    #    #      #
#   .      #      #   #   #   #    #    #      #
#   .      #      #   #   #   #    #    #      #
#####..#########################################
# START.    .     #     #      #     #   #     #
#REROUTE    .     #     #      #     #   #     #
#      .    .     #     #      #     #   #     #
#......#    .     #######      #     #   #     #
#      ######.....#     #      #     #   #     #
#      #   #      #     #      #     #   #     #
#      #   #      #     ########################
#      #   #      #  IP #         #      #     #
#      #   #      #     #         #   E  #     #
#      #   #      #     #         #      #     #
#      #   #      #     #         #......#######
#...########...##########         #      #     #
#   #   #      #   #    ###########      #     #
#   #   #      #   #    #         #      #     #
#BFM#   #      #   #    #         #      #     #
#   #####      #   #    #         #......#######
#   #   #......##########         #      #     #
#   #   #      #    #   #         #      #     #
#   #   #      #    #   ###########      #     #
#...#####      #    #   .    .    #      #     #
#   .   #......######   .    .    #......#     #
#   .   .  END .    .   .    .    .      #     #
#   .   .REROUTE    .   .    .    .      #     #
#   .   .      .    .   .    .    .      #     #
################################################
```

The next step could be to randomly attach every non connected room to a already connected one:

```text
################################################
#        .    #     #     .      .   .     .   #
#        .    #     #     .      .   .     .   #
#        .    #     #     .      .   .     .   #
#        .    #     #     .      .   #.....#   #
#        .    #     #     .      #####     #   #
#        .    #.....##....#      .   #     #   #
##########....#      .    #      .   #     #   #
#    #   .    .      .    #      .   #     #   #
#    #   .    .      .    #....#############...#
#    #   .    #......#....#    #    .    .     #
#    #...#    #      #    #    #    .    .     #
#....#   ######      #    #    #    .    .     #
#    #   .    #      #    #    #    ######.....#
#    #   .    #      #    #    #    #   #      #
#    #   .    #      #    ###########   #      #
#...##..##....########    .    .    #   #      #
#   #   #     #      .    .    .    #   #......#
#   #   #     #      .    .    .    #   .      #
#   #   #     #      .    .    .    #   .      #
#   #   #     #      .    .    .    #   .      #
#...##..##....##.....##...######..#########....#
#    .   #     #      #   #   #   #   .   .    #
#    .   #     #      #   #   #   #   .   .    #
#    .   #     #      #   #   #   #   .   .    #
#    #...#     ########   #   #   #   .   .    #
#    #   #     #      .   #   #   #   .   .    #
#    #   #     #      .   #   #   #...#####....#
#    #   #     #      #...#...#####   #   .    #
#....#######...########       .   #   #   .    #
#      #   #      #   .       .   #   #   .    #
#      #   #      #   .       .   #   #   .    #
#      #   #      #   .       .   #   #   .    #
#      #...#      #   .       .   #   #   .    #
#      .   ########   #####################....#
#      .   .      #...#     #    #   #   .     #
#......#   .      #   #     #    #   #   .     #
#      #####      #   #     #    #   #   .     #
#      #   #      #   #     #    #   #...#######
#      #   #      #   #     #    #   #   .     #
#......#   #      #   #.....#....#...#   .     #
#      #...############          .   #   .     #
#      .     .        .          .   #...#######
#      .     .        .          .   .    .    #
#      .     .        .          .   .    .    #
#      .     .        .          .   .    .    #
#      .     .        .          .   .    .    #
################################################
```

Connections have to be stored the same way as neighbours; if you keep this state it could also be usefull for fast pathfinding.

There are much more things possible:

Select a starting position. Randomly connect some of its neighbours, maybe 10% of the overall areas. Mark all these areas as "safe".

Select 3 different areas and connect them via A\* to the starting position (if they reach the "safe" zone its ok). Now you've got 3 routes. Randomly attach unconnected areas to the already connected ones, except the last 2 (maybe 3,4..) areas of route 3 (which have special purposes) and the safe zone.

Basically you've got 2 big connected zones (all areas connected to route 1 and 2), a smaller zone (those connected to route3) and a final exit (last area of route 3). Put a piece of a key in a random area of zone 1 and 2 and finally remember to place a door before the last area of route 3. Now the player has to visit zone 1 (maybe halls of the undead) and zone 2 (orc caves) to reach the last area of zone 3.

Areas marked as "safe" could feature less monsters and a different theme (which is explained later on)

You could also implement a labyrinth if the areas are small enough; i already created one with a even grid where each area is 4x4 cells.

```text
###############################
#  .  #  .  .  #  .  .  .  .  #
#  .  #  .  .  #  .  .  .  .  #
####..#######..#..#######..####
#  #  #  .  .  #  #  #  .  .  #
#  #  #  .  .  #  #  #  .  .  #
#..#..#######..#..#..####..#..#
#  .  .  #  .  .  #  .  #  #  #
#  .  .  #  .  .  #  .  #  #  #
#######..#..####..#..##########
#  #  .  #  #  .  .  .  #  .  #
#  #  .  #  #  .  .  .  #  .  #
#..####..#..####..#######..####
#  #  .  #  #  .  .  .  .  .  #
#  #  .  #  #  .  .  .  .  .  #
#..#..#..#..#######..#######..#
#  .  #  .  .  .  .  .  #  #  #
#  .  #  .  .  .  .  .  #  #  #
#..#..#######..#..#..####..####
#  #  .  #  .  #  #  .  .  .  #
#  #  .  #  .  #  #  .  .  .  #
#..#######..####..####..####..#
#  .  .  #  #  .  .  #  .  #  #
#  .  .  #  #  .  .  #  .  #  #
####..#..#######..#############
#  .  #  .  #  .  .  #  .  .  #
#  .  #  .  #  .  .  #  .  .  #
#..#..##########..####..#######
#  #  .  #  .  .  .  .  .  .  #
#  #  .  #  .  .  .  .  .  .  #
###############################
```

Remember: Up until here everything is pure imagination.

## Abstract Theming

---

Now we've got a fully abstract, but boring dungeon. In fact, at this stage no walls are painted, the whole dungeon only exists as a graph. Lots of objects/structs, some routes and some areas marked for special purposes and as special zones. The next step is theming, which is done on a per area basis.

```text
###############################################
#     #        #      ##       ########       #
#     #        #      ##       ########       #
#     +        #      ##       #      #       #
#     #        #      ##       #      +       #
#     #        #      ##       #      #       #
#     #+########      +        #      #       #
####### ###########+####       +      #       #
#######  ########## ############      #########
########  ######### ###        ####+###       #
######## ########## ###        #     ##       #
########      ##### ###        #     ##       #
#### ###   #  ###   ###        #     ##       #
#### ##  #    ### #  ##        #     ##       #
####  + ####   +  ## ##        #     ##       #
####+########+######+#####+##################+#
###  ######## ###### ##### #####      #       #
### ####      ###### ##### #####      #       #
### ####      ###### ####   ####      #       #
###   ##      ###### #### # ####      #       #
##### ##      ######    + # ####      #       #
####  ##      ############# ####      #       #
####  ##################### ####      #       #
#####+###################   ####      +    ####
##    ###########       #+###########+#########
### #############       # ########### #########
##  #############       #  ######     #########
#   ###### ######       #     ###  ############
# # #####  ######       #   # ###   #####  ####
# #  ###   ######       + ###  ##   ####   ####
#      + # ######       ###### +      +    ####
###+######+##############################+#####
#      ###   ######### +    ########## +   ####
#      ####   #######  ### #########   ##   ###
#      #####  ######  #### #########  ###   ###
#      #####   +     ##### ######### ###### ###
#      #####  ############ #######   ###### ###
##########   #############+######  ########   #
########## ############### #####  ##########+##
#      ###+############### #####+#######      #
#      +       #       + #   ###  ######      #
#      ##      #       #  ## ###    ####      #
#      ##      #       #     ###  # ####      #
#      ##      #       #   #   +    ####      #
#      ##      #       #################      #
#########      #       #################      #
###############################################
```

```text
###############################################
####    +   ######    +   #####################
##   ###### ###### ###### ########    +    ####
##      +   ###### ######      +     ##### ####
##  #######    +      +        +   # ##### ####
##  ################################ #####  ###
##  ################################+###### ###
##++###################        ##### ###### ###
#       ###############        ##### ###### ###
#       +  ############        ##### ######+###
#       ## #######  ###        ##### ###### ###
#       ## #######  ###        #     ###### ###
#       ##     +    ###        #  ######### ###
#       +      +    ###        +      +       #
#       ##########  ###        # ###########  #
#+################++############+###########++#
# ################  ############ #      ####  #
# #######      ###  ############ #      ####  #
# #######      ###      +    ###        ####  #
#    ####      +     ####    #####      #### ##
#### ####      # ### #### ## #####      #### ##
#### ####      # ###    +    #####      #### ##
#### ####      # ########+##+###############+##
####+########### ######## ## ############### ##
#### ###########+########      +     ####### ##
#### ###########       ##      +       #      #
#### ###########       ############ ## # #### #
#### ###########       ############ ## # #### #
##   ###   ######      ############ ## # #### #
##  #### # ######      ############ ## # #### #
##     +   ######      ############ ## # #### #
###+####+##########################+##+#+####+#
#     ## ###############       +       +      #
#     ## ###############      ####  ## #      #
#     ##       +    ####      ####  ## #      #
#     ######## #### ####      ####     +      #
#     ######## ####   ##      ##########      #
############## ###### ##      ##########      #
############## ###### ##################### ###
############## ######+##################### ###
#     ########+###### #####################+###
#     +     ## ####   ####     +       +    ###
#     ##### ## #### ###### ######## ####### ###
#     #####    #### ###### ####################
#     ############# ###### ####################
#     #############    +   ####################
###############################################
```

The above dungeons are generated using two painters:

- Room painter (RP)
- Tunnel painter (TP)

The first one has TPs assigned to the main routes, everything else is a RP.

The second one has assigned a painter based upon the number of connections; RPs for endpoints, TPs for areas with more then one connection. Additional, the TPs got a parameter to draw more straight tunnels.

A third one could make the left part as tunnels and the right part as rooms, or assign painters based on treasures, monsters, or a minimum number and so on.

To recall the above example (the one with zones), zone 1 could mostly feature rooms, zone 2 mostly caves and zone 3 could pe purely random.

In any case, these assignment are made before actually painting.

## Actual painting made easy

---

Now the painting starts per Area. The only thing to worry about is to make the area accessible to each connected neighbour. This can be done as the following:

Given a area we already know its connections, the painter and the painter of the connected room(s). For each conncted area we need to get a list of cells which can serve as openings to the other area; again a example with some ugly, unoptimized D-Code:

```d
private Cell[] getNeighbourCells(Room s, Room t) {
  Cell[] result;

  int x1 = s.cellX1;
  int y1 = s.cellY1;
  int x2 = s.cellX2;
  int y2 = s.cellY2;

  int x1b = t.cellX1;
  int y1b = t.cellY1;
  int x2b = t.cellX2;
  int y2b = t.cellY2;

  // nested method
  void isIn(int x, int y) {
    if ( (x >= x1b) && (y >= y1b) && (x <= x2b) && (y <= y2b) ) {
      if (
      ((x == x1b) && (y == y1b)) ||
      ((x == x1b) && (y == y2b)) ||
      ((x == x2b) && (y == y1b)) ||
      ((x == x2b) && (y == y2b))
      )
      return;
      result ~= cellAt(x,y);
    }
  }

  // don't allow corners, so start and end with a offset
  for (int x=x1+1;x<=x2-1;x++) {
    isIn(x,y1);
    isIn(x,y2);
  }

  for (int y=y1+1;y<=y2-1;y++) {
    isIn(x1,y);
    isIn(x2,y);
  }

  return result;
}
```

The above code just loops through each bordercell of our current area and checks where the cells overlap with the connected one. After it is finished it returns a array of cells. Its almost a duplicate of the first algorithm.

Now based upon the painter of our current and the neighbouring area, we can choose how many cells should serve as a passage next to each other. This could be implemended as the following:

- Two room painters just use a random single cell for connectivity and maybe put a door there
- Two cave painters take the whole bunch of cells
- Two tunnelpainters just take one cell, sometimes two cells if possible (roudtrips)
- Neighbouring cave and room painters take either one cell and later on place a door to the opening cave, or take a range of cells to make something like a hole.

Anyway, these cells are marked as fixed and free and are stored as 'gateways' in that area; they are never allowed to be a wall. This can be done in advance for every area.

After this is done, the painter of a area is responsible for filling its area. A room painter would draw a wall on its borders (remember not overpainting fixed cells), a tunnel painter could fill the whole area with wall and draw a line from each gateway to the center, so they are guaranteed to connect. In fact, this step could be done for every painter, so that these cells are marked as fixed and free too.

Some examples of different painters i already used:

- "Simple Room Painter": Paints just a wall at the borders of the area, except gateways.
- "Simple Tunnel Painter": Fills the entire area solid and carves lines from each gateway to the center.
- "Multi Tunnel Painter": Fills the area solid and carves a line from each gateway to each other.
- "Circular Tunnel Painter": Fills the area and connects each gateway to the next in a circular way.
- "Cave painter": Same as "simple room painter", but draws tunnels which are "fixed free" and grows stone from the borders into the center.

Each painter can get some additional parameters like randomness and straightness.

Voila; as every room got painted the abstract dungeon turned into a real one. Now you can place treasures in areas which are only connected to one neighbour, place a trail of blood in areas belonging to the main route, or just place the all-mighty weapon in a area connected to the main route before the big fat monster.

## Remarks

---

If the resulting rooms look to evenly distributed, the painting algorithm for rooms can be tweaked to not fill the entire area and skip some columns/rows. Additional the room painter has to draw a corridor to its center to count for the offset.

Roundtrips as seen above are possible if two gateways are chosen between two tunnel areas.

to be continued...
