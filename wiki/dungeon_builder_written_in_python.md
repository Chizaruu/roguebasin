# Dungeon builder written in Python

---

by Steve Wallace

## NOTES

---

First I'd like to say that my main experience with writing any sort of script has been with Flash actionscript, and having exceeded the boundaries of Flash's capabilities I decided to learn a language with more potential. I started learning Python just a few days ago, so my appologies if my code isn't elegant. I have tried to make it as straightforward and easy to follow as possible.

Next, my os is Windows, you will notice I have used the NumPy module, which is an array specific module for windows. Python has no (decent) native array support under Windows, so anyone using a different os will probably want to impliment their preffered array methods. Apart from that the following can be run on any os that supports Python.\*

- removed the need for numpy from the code below

## CLASS DESCRIPTION

---

dMap class

This is a fairly flexible class that will build a dungeon level. It is configurable to produce a wide variety of dungeon types, from small networks of rooms to huge mazes, and with a little modification it can easily be extended to incorporate your own features too.

As it stands it produces 2 different room types, the basic room and the corridor, and 4 types of joins, open wall, closed door, open door and secret door.

dMap has 3 attributes:

1. mapArr\[y][x] - this is the main array that stores the map data, being values from 0 to 5 (0-walkable floor, 1-blank/undiscovered, 2-wall, 3-open door, 4-closed door, 5-secret door)

2. roomList[n] - this is a list of every room (inc. corridors) and roomList[n] will be a list in the form of [y size of room, x size of room, x coord, y coord] ie a value of [4,5,2,6] will be a room 5 across and 4 down with its top left floor tile at coord 2,6. This shouldn't be edited as any changes made after its created will NOT reflect on the map, its purely a reference to the floor area of the map.

3.cList[n] - this is similar to roomList, but it purely contains the corridors only and is used for connecting corridors. Its value will be in the format [ref. no to roomList, start x coord, start y coord, direction it travels]. The reference number will give the index to its entry in roomList, x and y start coords will be the coordinate where the corridor actually starts (NOTE this may not be the same as its x and y pos) and direction travelled is a number from 0 to 3 (0-north, 1-east, 2-south,3-west). cList is purely a set of hints supplied to the joinCorridor() function, although it could be used to generate a list of only corridors for use in monster/item/etc placement. Alternatively its ref no could be used to exclude it from roomList to generate a list of rooms only. As with roomList, do not edit after its creation.

dMap has 1 main method that is used to create a map:

makeMap(xsize,ysize,fail,b1,mrooms)

xsize- x size of the map area

ysize- y size of the map area

fail- a value from 1 upwards. The higer the value of fail, the greater the chance of larger dungeons being created. A low value (>10) tends to produce only a few rooms, a high value (<50) raises the chance that the whole map area will be used to create rooms (up to the value of mrooms - see below)

b1- corridor bias. This is a value from 0 to 100 and represents the %chance a feature will be a corridor instead of a room. A value of 0 will produce rooms only, a value of 100 will produce corridors only

mrooms- maximum number of rooms to create. This, combined with fail, can be used to create a specific number of rooms

## USAGE

---

somename=dMap() somename.makeMap(50,50,10,50,20)

The above will make a map of maximum size 50 x 50, a low (10) chance of making new rooms with a 50% chance new rooms will be corridors, and a maximum of 20 rooms.

somevariable=somename.mapArr\[y][x] will give you access to the actual map, where x and y are the coordinates, and the value of somevariable will be from 0 to 5 as detailed above in mapArr.

Well I hope this all makes sence, and once again sorry if the code is kinda ugly, like I said I only started learning Python a few days ago (and this is my second attempt at writing a map algorithm). However, feel free to use and modify the code to suit your needs.

## IMPLEMENTATION

---

```python
# Class to produce random map layouts
#from numpy import *
from random import *
from math import *
class dMap:
   def __init__(self):
       self.roomList=[]
       self.cList=[]
   def makeMap(self,xsize,ysize,fail,b1,mrooms):
       """Generate random layout of rooms, corridors and other features"""
       #makeMap can be modified to accept arguments for values of failed, and percentile of features.
       #Create first room
       self.mapArr=[] #ones((ysize,xsize))
       for y in range(ysize):
           tmp = []
           for x in range(xsize):
               tmp.append(1)
           self.mapArr.append( tmp )
       w,l,t=self.makeRoom()
       while len(self.roomList)==0:
           y=randrange(ysize-1-l)+1
           x=randrange(xsize-1-w)+1
           p=self.placeRoom(l,w,x,y,xsize,ysize,6,0)
       failed=0
       while failed<fail: #The lower the value that failed< , the smaller the dungeon
           chooseRoom=randrange(len(self.roomList))
           ex,ey,ex2,ey2,et=self.makeExit(chooseRoom)
           feature=randrange(100)
           if feature<b1: #Begin feature choosing (more features to be added here)
               w,l,t=self.makeCorridor()
           else:
               w,l,t=self.makeRoom()
           roomDone=self.placeRoom(l,w,ex2,ey2,xsize,ysize,t,et)
           if roomDone==0: #If placement failed increase possibility map is full
               failed+=1
           elif roomDone==2: #Possiblilty of linking rooms
               if self.mapArr[ey2][ex2]==0:
                   if randrange(100)<7:
                       self.makePortal(ex,ey)
                   failed+=1
           else: #Otherwise, link up the 2 rooms
               self.makePortal(ex,ey)
               failed=0
               if t<5:
                   tc=[len(self.roomList)-1,ex2,ey2,t]
                   self.cList.append(tc)
                   self.joinCorridor(len(self.roomList)-1,ex2,ey2,t,50)
           if len(self.roomList)==mrooms:
               failed=fail
       self.finalJoins()

   def makeRoom(self):
       """Randomly produce room size"""
       rtype=5
       rwide=randrange(8)+3
       rlong=randrange(8)+3
       return rwide,rlong,rtype
   def makeCorridor(self):
       """Randomly produce corridor length and heading"""
       clength=randrange(18)+3
       heading=randrange(4)
       if heading==0: #North
           wd=1
           lg=-clength
       elif heading==1: #East
           wd=clength
           lg=1
       elif heading==2: #South
           wd=1
           lg=clength
       elif heading==3: #West
           wd=-clength
           lg=1
       return wd,lg,heading
   def placeRoom(self,ll,ww,xposs,yposs,xsize,ysize,rty,ext):
       """Place feature if enough space and return canPlace as true or false"""
       #Arrange for heading
       xpos=xposs
       ypos=yposs
       if ll<0:
           ypos+=ll+1
           ll=abs(ll)
       if ww<0:
           xpos+=ww+1
           ww=abs(ww)
       #Make offset if type is room
       if rty==5:
           if ext==0 or ext==2:
               offset=randrange(ww)
               xpos-=offset
           else:
               offset=randrange(ll)
               ypos-=offset
       #Then check if there is space
       canPlace=1
       if ww+xpos+1>xsize-1 or ll+ypos+1>ysize:
           canPlace=0
           return canPlace
       elif xpos<1 or ypos<1:
           canPlace=0
           return canPlace
       else:
           for j in range(ll):
               for k in range(ww):
                   if self.mapArr[(ypos)+j][(xpos)+k]!=1:
                       canPlace=2
       #If there is space, add to list of rooms
       if canPlace==1:
           temp=[ll,ww,xpos,ypos]
           self.roomList.append(temp)
           for j in range(ll+2): #Then build walls
               for k in range(ww+2):
                   self.mapArr[(ypos-1)+j][(xpos-1)+k]=2
           for j in range(ll): #Then build floor
               for k in range(ww):
                   self.mapArr[ypos+j][xpos+k]=0
       return canPlace #Return whether placed is true/false
   def makeExit(self,rn):
       """Pick random wall and random point along that wall"""
       room=self.roomList[rn]
       while True:
           rw=randrange(4)
           if rw==0: #North wall
               rx=randrange(room[1])+room[2]
               ry=room[3]-1
               rx2=rx
               ry2=ry-1
           elif rw==1: #East wall
               ry=randrange(room[0])+room[3]
               rx=room[2]+room[1]
               rx2=rx+1
               ry2=ry
           elif rw==2: #South wall
               rx=randrange(room[1])+room[2]
               ry=room[3]+room[0]
               rx2=rx
               ry2=ry+1
           elif rw==3: #West wall
               ry=randrange(room[0])+room[3]
               rx=room[2]-1
               rx2=rx-1
               ry2=ry
           if self.mapArr[ry][rx]==2: #If space is a wall, exit
               break
       return rx,ry,rx2,ry2,rw
   def makePortal(self,px,py):
       """Create doors in walls"""
       ptype=randrange(100)
       if ptype>90: #Secret door
           self.mapArr[py][px]=5
           return
       elif ptype>75: #Closed door
           self.mapArr[py][px]=4
           return
       elif ptype>40: #Open door
           self.mapArr[py][px]=3
           return
       else: #Hole in the wall
           self.mapArr[py][px]=0
   def joinCorridor(self,cno,xp,yp,ed,psb):
       """Check corridor endpoint and make an exit if it links to another room"""
       cArea=self.roomList[cno]
       if xp!=cArea[2] or yp!=cArea[3]: #Find the corridor endpoint
           endx=xp-(cArea[1]-1)
           endy=yp-(cArea[0]-1)
       else:
           endx=xp+(cArea[1]-1)
           endy=yp+(cArea[0]-1)
       checkExit=[]
       if ed==0: #North corridor
           if endx>1:
               coords=[endx-2,endy,endx-1,endy]
               checkExit.append(coords)
           if endy>1:
               coords=[endx,endy-2,endx,endy-1]
               checkExit.append(coords)
           if endx<78:
               coords=[endx+2,endy,endx+1,endy]
               checkExit.append(coords)
       elif ed==1: #East corridor
           if endy>1:
               coords=[endx,endy-2,endx,endy-1]
               checkExit.append(coords)
           if endx<78:
               coords=[endx+2,endy,endx+1,endy]
               checkExit.append(coords)
           if endy<38:
               coords=[endx,endy+2,endx,endy+1]
               checkExit.append(coords)
       elif ed==2: #South corridor
           if endx<78:
               coords=[endx+2,endy,endx+1,endy]
               checkExit.append(coords)
           if endy<38:
               coords=[endx,endy+2,endx,endy+1]
               checkExit.append(coords)
           if endx>1:
               coords=[endx-2,endy,endx-1,endy]
               checkExit.append(coords)
       elif ed==3: #West corridor
           if endx>1:
               coords=[endx-2,endy,endx-1,endy]
               checkExit.append(coords)
           if endy>1:
               coords=[endx,endy-2,endx,endy-1]
               checkExit.append(coords)
           if endy<38:
               coords=[endx,endy+2,endx,endy+1]
               checkExit.append(coords)
       for xxx,yyy,xxx1,yyy1 in checkExit: #Loop through possible exits
           if self.mapArr[yyy][xxx]==0: #If joins to a room
               if randrange(100)<psb: #Possibility of linking rooms
                   self.makePortal(xxx1,yyy1)
   def finalJoins(self):
       """Final stage, loops through all the corridors to see if any can be joined to other rooms"""
       for x in self.cList:
           self.joinCorridor(x[0],x[1],x[2],x[3],10)
```

## Examples

---

~~Not to make this page any longer example outputs are placed here: Dungeon builder written in Python/Examples~~ - Examples moved here by Chizaruu 26/01/2023

### Example 1

```text
                   #########                    #################
 #######           #.......#                    #...............#   #####
 #.....#           #######=#           ##########=#############.#####...#
 #.....#                 #.#           #.......=........................#
 #.....###################.#############=###=#####=##################...#
 #.........................=.................#   #....#             #...#
 #.#######.#########.#####=###############.#=#####....#             #...#
 #.#   #.=...........#   #...........=.##.......##....#             #...#
 #.#   #.###.#####=##############=####.##.......##....#             #####
 #.#   #.# #.#   #................#  #.##.......##....#    ###
 ###   #.# #.### ######.#.####.####  #.##.......##....#    #.#
       #.# #.=.#  #.....=.#  #.#######.##.......##....#    #.#
       #.# #.#.#  #.....#.#  #.=....##.##.......#####=######.#
       #.# #.#.#  #.....#.#  #.#######.##.......##.........#.#
       #.# #.#.#  #.....#.#  #.##### #.##.......##.....#...#.#########
  ######=# #.#.#  #.....#=#  #.#...# #.##.......##.....#...#.#.......#
  #......# #.#.#  #.....#.#  #.....# #.#################...#.#.......#
  #......# #.#.#  #.....#.#  #.#...# #.#               #...#.#.......#
  #......# #...#  #.....#.#  #.#...# #.#               #...#.=.......#
  #......# #.#.# ########.#  #.#######.#               ###.###.......#
  #......# #.#.# #........#  #.......#.############      #...#.......#
###......# #.#.# ###.#=##.#  #.#.....#.##.........#      #...........#
#........# #.#.#   #...##.#  #.#.....#.##.#########      #...#.......#
#.####.#####=#.#   ######.#  #.#######.##.#              #...#.......#
#............#.#        #.#  #.#     #.##.#  ###         #####.......#
#######.######.#        #.#  #.#     ####.#  #.#      ######################
    #...#    #.#   ######.#  #.######## #.#  #.###    #.=..................#
    #=###    #.#   #.##.=.#  #.##.....# #.#  #.#.#    #.#=##################
    #.#      #.#   #.##.#.####=######.###.#  #.#.#    #.#.=........#######
    #.#      #.#   #.##.#.#....=........#.#  #.#.#    #.#.#........#.....#
    #.##############.##.#.#....#........#.#  #.#.#    #............#.....#
    #..............=.##.#.#....#........#.#  #.#.#  #####=#........#.....#
  ###=#.####=#.###=####.#.#....#........#.#  #.=.#  #.....#........#.....#
  #...#........................#........=.#  ###.#  #######........#.....#
  #...#####################....########=########.##########........=.....#
  #...#                   #....#      #...................=........#.....#
  #...#        #############.###      ##=#######.##=################.....#
  #####        #.............#         #.......=....#              #.....#
               ###############         ##############              #######

```

### Example 2

```text
                       ########################                       #########
                       #............=.........#       ########        #.......#
                       #.############.#########       #.#....#        #######.#
                       #.#          #.#               #.#....# ##############.#
                       #.#          #.#   #############.#....# #..............#
                       #.#          #.#   #...........=......# ############=#.#
                   ### #.#          #.#   #############.######            #...#
                   #.# #.#          #.# ######        #.#                 #.#.#
                   #.# #.#   ###    #.# #....#        #.#                 #.=.#
                   #.# #.#   #.#    #.###=##=##########.############      ###.#
                   #.# #.# ###.#    #.=...............=.#..........#        #.#
                   #.# #.# #.=.#   ####=#######=###.#.###......###.###      #.#
                   #.# #.# #.#.#   #....#######.........#............#      #.#
             ###   #.# #.# #.#.# ###....=.....=.........#......#.....#      #.#
             #.#   #.# #.# #.#.# #.#....#####=#.........#......#.....########.#
             #.#   #.# #.###.=.# #.#...........................#.....#......#=#
             #.#   #.# #.#.###.###......#######.........########.....#......=.#
             #.#   #.# #.#.# #.#...#....#     ##################.....#......#.#
             #.#####=###=#.# #.#.#.#....##########   #.........=............#.#
             #.=...........# #.#.#.=....#........#   #################......#.#
             ###########=#=###.#=#.#....=........#########           #......#.#
              #..........................................#           #......#.#
              #.##############.#######..####.##.######=#=#############......#.#
              #.### #..........#     #...# #.=....................=.##......#.#
              #.=.# #..........#     #...# #.####.###=#######=#####.#####==##.#
              #.#.###..........#     #...# #.#  #.#.......# #.#   #.#  #...##.#
              #.#.#.=..........#     #...# #.#  #.#.......# #.#   ###  #...##.#
              #.#.#.#..........#     #...# #.#  #.#.......# #.##########...##.#
              #.#.=.=..........#     #...# #.#  #.#.......# #.........###=###.#
              #.###.############     #...# #.#  #.#.......# #.=.......# #.....#
              #.# #.#                #...# #.#  #.#.......# #.#.......# #####.#
              ### #.#    #####   #####.### #.#  #.=.......# #.#.......#######.#
                  #.#    #...#   #.....# ###=######.......# #.#=#######.=.....#
                  #.# ####=#=#####.....# #.......##.......# #.#...#   #.#######
                  #.# #..........=.....# #.......###=###=####.#...#   #.#######
                  #.# ######.###=#.....# #.......# #...#...##.#...#   #.......#
                  #.#      #...........# #.......# #...#...##.#...#   #.#######
                  ############..######## #.......# #...=...##.#...#   #.#
                    #...........#        #.......# #...######.#####   #.#
                    #############        ######### #####    ###       ###
```

### Example 3

```text
                                                            ######
         ###                                                #....#
         #.#                                                #.####
         #.#                       ###########              #.#
    ######.#        ################.........#              #.#
    #...##.#        #......=.......=.........#              #.#
  ###...##.#        ##############=#.........#              #.#
  #.=...##.##############        #.#.........####           #.#
  #.#...##.=............#        #.#.........##.#           #.#
  #.#...####=#=########.##########.#.........##.#######     #.#
  #.##### #....=.................#.#.........##.#.....#   ###.########
  #.#     ######################.#.####=#######.#.....#   #.#.#......#
  #.#################          #.#.#  #.......=.......#   #.#.=......#
  #.##..............#          #.#.#  ###########.....#   #.=.#......#
  #.############=##.######  ####.#.#            #######   ###.#########
  #.#....=.......#.......#  #.##.#.#  ############          #.........#
 ##=####=#.#######.......#  #.##.#.#  #..........#          #.#########
 #..........#    #.......#  #.##.#.####.####=#...#          #.=....#
###########=######.......#  #.##.#.#....#  #.#...#          #......#
#......##................#  #.##.#.#....#  #.#...#          #.#....#
#=####.##........#.......#  #.##.#=#....#  #.=...#          #.######
#.......#........#.......####=##...=....#  #=#...#          #.#
#.......=........#.......##...##.#.=....#  #.#...#          #.#
#................#.......##.####.=......#  #.#...#########  #.#
#.......#........##########....#.#.#....#  #.#...=.......#  #.#
#................##########....=.#.#=####  #.....#########  #.#
#.......###########......##....#.=.#...#   #=################=###
###=###############=####=##....#.#.#####   #....................#
#..........=...................#.#.################################
#.###.###=###########=.####....#.#....................#   #.......#
#.# #........#     #.....##....###...################=#####.......#
#.# #........###   #.....##....# #...#   ########.........#.......#############
#.# #........=.#   #.....##########.######......#.........#.......#...........#
#.# #........#.#   #...........=.........=................#.......#.###########
#.# #........#.#   #.....#######..........................=.......#.........#
#.# #........=.#   #.....#     #.........#......#.........#.......#.........#
### #........#.#   #.....#     #.........#......#=#######.#.................#
    #........###   #######     #.........#......#.........#.......###########
    ##########                 ####################################

```

### Example 4

```text





                                ###################
                                #.................#
                             ####=############.##=###
                             #..............##......# ########################
                             ##############.#####=#.###....................=.#
                               #########  #.=.....#.#.#=####################.#
                               #.......#  #.#.......#.=.#                  #.#
                               #######.####.#.....#.#.#.#                  #.#
                          ########   #......###=###.#.#.#                  #.#
                          #....=.#   #......###.# #.#.#.#                  #.#
                          ######.#####....#.=.#.# #.#.=.#                  #.#
                               #.#.=.=....#.#.#.# #.#.#########################
                               #.#.#.#......#.=.# ###.#.................##....#
                               #.#.#.#....###.### #.=.#=##################....#
                               #.=.#.#....# ###   #.#.#.#                #....#
                               #.=.#.#....#       #.=.#.#                #....#
                               ###.#.###=#######  ###.#.#                #....#
                               ###=#.# #...#...#    #.#.#   ##########   #....#
      ##############           #...#.###=###.#=######.=.#   #........#####....#
      #............#           #####.#.............####.#   #........=...=....#
      ############=######          #.=.......#######  #.#   #........#####....#
              #.........#          #.=.......#        #.#   #........#   ######
         ######.####################.######### ########=######=###############
         #.=..........=..............#         #.....=.........#      #......#
         #.################.#####.##.###################=##=###########==###=#
         #.#      #...............##.=.................=.....................#
         #.#    ####################=######.#######.#.######.#######.#####...#
         #.#    #...........##.......#   #........=...#    #...##.......##...#
         #.#    #.############.......#   #........#######  #...##.......######
    ######=#    #.=.........##.......#   #........=.....#  #...##.......#####
    #......#    #.#=#######=#####=#####  ####.#.#######.#########.......=...#
    ##.##=##    #...............=.....#     #.#............#    #.......#...#
     #....#     #.####.#.####=####.####     #.##############    #########...#
     ######     #.#  #...#  #......#        #.#                         #####
                ###  #####  ########        ###
```

### Example 5

```text
                   ######              #######
                   #....#            ###.....#
                 ###....#            #.=.....#
                 #......#            #.#.....#
                 #.#....#            #.......#
                 #......#            #.......#         ######
                 #.######            ###.....#         #....#             #####
     ##########  #.#            ########.....#######  ##.##################...#
     #........#  #.#            #.....##.....#.....#  #....#........#.....=...#
 #####=######=####.##############=###.##=#=###.###########=#........=.....#...#
 #.......................................=.##...#   #......=........#.....#...#
 ############################.####.#######.####.#####......####=.####.....#####
          ######     ###......#  #.#.#   #.................# #...#  #######
          #....#     #........#  #...#   #.##########......# #...###########
  #########=##.#######.#......#  #.#.#   #.#        #......# #...#.........#
  #..................=.=......#  #.#.#   #=####     #......# #...#.........#
  ############.#######.#......#  #.=.#   #....#     #......# #...#.........#
         #.....#     #.##..#=##  ###.#   #....#     #......###...=.........#
         #.....#     #.=......#    #.# ###....#     #........=...#.........#
         #.....#     ###......######.# #......#     #......#.#...####=######
         #.....#       #.............# #.#....#######=##.###.#...#.........#
       ###.....#  ##############=#=#.###.=....##.......=.# #.#####.........#
       #.......#  #..............#....##.#....##.#######.# #.#   #.........#
       #.#######  ###.#######.####=#####.#....##.........###=#   #.........#
       #.#          #.................##.####=##=#####.#.=...#   #.........#
  ####################=############=#.##.##......#   #.#.#####   #.........#
  #...............=....=..............##.##......#   #.#.#       #.........#
  ##################..########=######.##.##......### #...#       ########=###
     #.........................#    #.##.##........# ###.########     #.....#
     #.####=###.###.##.........#    #.##.##......#.#   #.##.....#     #.....#
     #.#  #.........##.........#    #.##.##......#.#   #=##=#.#.##    #.....#
     #.#  ############.........#    #.##.##......#.#   #.........#    #.....#
     #.#             #.........#    #.##.##......#.#   #.........#    #.....#
     #.#####         #.........#    #.##.##=##=###.#   #.........#    #.....#
     #.....#         #.........#    #.####.....# ###   #.........#    #.....#
     #########################.#    #.#  #.....#       #.........#    #.....#
           #...................#    #.#  #.....#       #.........#    #######
           #####################    ###  #.....#       #.........#
                                         #.....#       ###########
                                         #######
```

## Comment

---

I fixed some bugs and wrote a simple script to show how maps look like (only shows walls (#), doors (=) and floor (.)) Although I'm still trying to figure out the script, but from my tests every map size below 80x40 can cause endless loops! --MilesTeg 00:08, 28 May 2009 (CEST)

```python
from dMap import *
startx=80
starty=40
somename= dMap()
somename.makeMap(startx,starty,110,50,60)
for y in range(starty):
        line = ""
        for x in range(startx):
                if somename.mapArr[y][x]==0:
                        line += "."
                if somename.mapArr[y][x]==1:
                        line += " "
                if somename.mapArr[y][x]==2:
                        line += "#"
                if somename.mapArr[y][x]==3 or somename.mapArr[y][x]==4 or somename.mapArr[y][x]==5:
                        line += "="
        print line
```

## Update

---

Here's the whole thing without the numpy requirement and any size map -- Mtvee 03:14, 24 January 2014 (CET)

```python
# Class to produce random map layouts
from random import *
from math import *

class dMap:
   def __init__(self):
       self.roomList=[]
       self.cList=[]

   def makeMap(self,xsize,ysize,fail,b1,mrooms):
       """Generate random layout of rooms, corridors and other features"""
       # makeMap can be modified to accept arguments for values of failed, and percentile of features.
       # Create first room
       self.size_x = xsize
       self.size_y = ysize
       # initialize map to all walls
       self.mapArr=[]
       for y in range(ysize):
           tmp = []
           for x in range(xsize):
               tmp.append(1)
           self.mapArr.append( tmp )

       w,l,t=self.makeRoom()
       while len(self.roomList)==0:
           y=randrange(ysize-1-l)+1
           x=randrange(xsize-1-w)+1
           p=self.placeRoom(l,w,x,y,xsize,ysize,6,0)
       failed=0
       while failed<fail: #The lower the value that failed< , the smaller the dungeon
           chooseRoom=randrange(len(self.roomList))
           ex,ey,ex2,ey2,et=self.makeExit(chooseRoom)
           feature=randrange(100)
           if feature<b1: #Begin feature choosing (more features to be added here)
               w,l,t=self.makeCorridor()
           else:
               w,l,t=self.makeRoom()
           roomDone=self.placeRoom(l,w,ex2,ey2,xsize,ysize,t,et)
           if roomDone==0: #If placement failed increase possibility map is full
               failed+=1
           elif roomDone==2: #Possiblilty of linking rooms
               if self.mapArr[ey2][ex2]==0:
                   if randrange(100)<7:
                       self.makePortal(ex,ey)
                   failed+=1
           else: #Otherwise, link up the 2 rooms
               self.makePortal(ex,ey)
               failed=0
               if t<5:
                   tc=[len(self.roomList)-1,ex2,ey2,t]
                   self.cList.append(tc)
                   self.joinCorridor(len(self.roomList)-1,ex2,ey2,t,50)
           if len(self.roomList)==mrooms:
               failed=fail
       self.finalJoins()

   def makeRoom(self):
       """Randomly produce room size"""
       rtype=5
       rwide=randrange(8)+3
       rlong=randrange(8)+3
       return rwide,rlong,rtype

   def makeCorridor(self):
       """Randomly produce corridor length and heading"""
       clength=randrange(18)+3
       heading=randrange(4)
       if heading==0: #North
           wd=1
           lg=-clength
       elif heading==1: #East
           wd=clength
           lg=1
       elif heading==2: #South
           wd=1
           lg=clength
       elif heading==3: #West
           wd=-clength
           lg=1
       return wd,lg,heading

   def placeRoom(self,ll,ww,xposs,yposs,xsize,ysize,rty,ext):
       """Place feature if enough space and return canPlace as true or false"""
       #Arrange for heading
       xpos=xposs
       ypos=yposs
       if ll<0:
           ypos+=ll+1
           ll=abs(ll)
       if ww<0:
           xpos+=ww+1
           ww=abs(ww)
       #Make offset if type is room
       if rty==5:
           if ext==0 or ext==2:
               offset=randrange(ww)
               xpos-=offset
           else:
               offset=randrange(ll)
               ypos-=offset
       #Then check if there is space
       canPlace=1
       if ww+xpos+1>xsize-1 or ll+ypos+1>ysize:
           canPlace=0
           return canPlace
       elif xpos<1 or ypos<1:
           canPlace=0
           return canPlace
       else:
           for j in range(ll):
               for k in range(ww):
                   if self.mapArr[(ypos)+j][(xpos)+k]!=1:
                       canPlace=2
       #If there is space, add to list of rooms
       if canPlace==1:
           temp=[ll,ww,xpos,ypos]
           self.roomList.append(temp)
           for j in range(ll+2): #Then build walls
               for k in range(ww+2):
                   self.mapArr[(ypos-1)+j][(xpos-1)+k]=2
           for j in range(ll): #Then build floor
               for k in range(ww):
                   self.mapArr[ypos+j][xpos+k]=0
       return canPlace #Return whether placed is true/false

   def makeExit(self,rn):
       """Pick random wall and random point along that wall"""
       room=self.roomList[rn]
       while True:
           rw=randrange(4)
           if rw==0: #North wall
               rx=randrange(room[1])+room[2]
               ry=room[3]-1
               rx2=rx
               ry2=ry-1
           elif rw==1: #East wall
               ry=randrange(room[0])+room[3]
               rx=room[2]+room[1]
               rx2=rx+1
               ry2=ry
           elif rw==2: #South wall
               rx=randrange(room[1])+room[2]
               ry=room[3]+room[0]
               rx2=rx
               ry2=ry+1
           elif rw==3: #West wall
               ry=randrange(room[0])+room[3]
               rx=room[2]-1
               rx2=rx-1
               ry2=ry
           if self.mapArr[ry][rx]==2: #If space is a wall, exit
               break
       return rx,ry,rx2,ry2,rw

   def makePortal(self,px,py):
       """Create doors in walls"""
       ptype=randrange(100)
       if ptype>90: #Secret door
           self.mapArr[py][px]=5
           return
       elif ptype>75: #Closed door
           self.mapArr[py][px]=4
           return
       elif ptype>40: #Open door
           self.mapArr[py][px]=3
           return
       else: #Hole in the wall
           self.mapArr[py][px]=0

   def joinCorridor(self,cno,xp,yp,ed,psb):
       """Check corridor endpoint and make an exit if it links to another room"""
       cArea=self.roomList[cno]
       if xp!=cArea[2] or yp!=cArea[3]: #Find the corridor endpoint
           endx=xp-(cArea[1]-1)
           endy=yp-(cArea[0]-1)
       else:
           endx=xp+(cArea[1]-1)
           endy=yp+(cArea[0]-1)
       checkExit=[]
       if ed==0: #North corridor
           if endx>1:
               coords=[endx-2,endy,endx-1,endy]
               checkExit.append(coords)
           if endy>1:
               coords=[endx,endy-2,endx,endy-1]
               checkExit.append(coords)
           if endx<self.size_x-2:
               coords=[endx+2,endy,endx+1,endy]
               checkExit.append(coords)
       elif ed==1: #East corridor
           if endy>1:
               coords=[endx,endy-2,endx,endy-1]
               checkExit.append(coords)
           if endx<self.size_x-2:
               coords=[endx+2,endy,endx+1,endy]
               checkExit.append(coords)
           if endy<self.size_y-2:
               coords=[endx,endy+2,endx,endy+1]
               checkExit.append(coords)
       elif ed==2: #South corridor
           if endx<self.size_x-2:
               coords=[endx+2,endy,endx+1,endy]
               checkExit.append(coords)
           if endy<self.size_y-2:
               coords=[endx,endy+2,endx,endy+1]
               checkExit.append(coords)
           if endx>1:
               coords=[endx-2,endy,endx-1,endy]
               checkExit.append(coords)
       elif ed==3: #West corridor
           if endx>1:
               coords=[endx-2,endy,endx-1,endy]
               checkExit.append(coords)
           if endy>1:
               coords=[endx,endy-2,endx,endy-1]
               checkExit.append(coords)
           if endy<self.size_y-2:
               coords=[endx,endy+2,endx,endy+1]
               checkExit.append(coords)
       for xxx,yyy,xxx1,yyy1 in checkExit: #Loop through possible exits
           if self.mapArr[yyy][xxx]==0: #If joins to a room
               if randrange(100)<psb: #Possibility of linking rooms
                   self.makePortal(xxx1,yyy1)

   def finalJoins(self):
       """Final stage, loops through all the corridors to see if any can be joined to other rooms"""
       for x in self.cList:
           self.joinCorridor(x[0],x[1],x[2],x[3],10)

# ----------------------------------------------------------------------------
# Main
# ----------------------------------------------------------------------------
startx=20   # map width
starty=10   # map height
themap= dMap()
themap.makeMap(startx,starty,110,50,60)
for y in range(starty):
        line = ""
        for x in range(startx):
                if themap.mapArr[y][x]==0:
                        line += "."
                if themap.mapArr[y][x]==1:
                        line += " "
                if themap.mapArr[y][x]==2:
                        line += "#"
                if themap.mapArr[y][x]==3 or themap.mapArr[y][x]==4 or themap.mapArr[y][x]==5:
                        line += "="
        print line
```
