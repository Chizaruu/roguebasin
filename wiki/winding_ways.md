# Winding ways

---

The algorithm described below works on a rectangular grid and generates a sequence of cells connecting the given cells A and B in a random way, without sharp turns. It can be used to make roads, rivers, dungeon corridors, etc.

First generate the straight line from A to B using the Bresenham algorithm. Then, select a subset of the line cells - skipping randomly two or three cells at each step - as waypoints. In a loop, select randomly a waypoint (but not any of the ends), and try to perturb it, shifting by 1 cell in a random direction. The perturbation is accepted only if it doesn't make any angles too sharp (there are three angles involved), and the distances to the previous and the next waypoint stay within some limits (2-5 steps). The number of loop iterations is the number of waypoints times a caller-provided parameter; for roads of length 50 values 10 - 30 give decent results. After the perturbation, the waypoints are connected using short straight line segments. Here an 'L' shaped three cell sharp turn may be occasionally introduced; it is possible to get rid of it using a bit of postprocessing (and the code below does), but it is seldom needed.

The source is at: [[1]](http://b3x7q3.1fichier.com/en/windroad.c)

A sample path:

```text
##########################################################
#######################################################--#
#############################------####---#######------###
############################-######----###-------#########
#######################-----##############################
######################-###################################
#####################-####################################
###-----#############-####################################
##-#####------#####--#####################################
##############-----#######################################
##########################################################
```

The piece of code linked to above contains also two other simple road-makers: a random orthogonal zigzag routine and a closely related 'sigsag' one (a zigzag with the corner cells cut). The roads they generate look like this:

```text
#########################################################
######################################################--#
#################################################*----*##
#################################################-#######
###############*---------------------------------*#######
###############-#########################################
###############-#########################################
###############-#########################################
###############-#########################################
###############-#########################################
###############-#########################################
###############-#########################################
#--------------*#########################################
#########################################################
```

The cells marked '\*' would be part of the zigzag, but not of the sigsag path.

The source code can be found on the bottom of this page <https://kusigrosz.jimdo.com/roguelike-related/>
