# Complete Roguelike Tutorial, using python+libtcod, part 3

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## The Dungeon

---

### Dungeon building blocks

---

Alright then, it's about time our dungeon takes a recognizable shape! I never cared much for pillars anyway. What we're gonna do now is create functions to carve rooms and tunnels in the underground rock. These functions will be the building blocks used to generate the whole dungeon which you'll get to later.

First of all, a little helper class that will be very handy when dealing with rectangular rooms:

```python
class Rect:
    #a rectangle on the map. used to characterize a room.
    def __init__(self, x, y, w, h):
        self.x1 = x
        self.y1 = y
        self.x2 = x + w
        self.y2 = y + h
```

This will take top-left coordinates for a rectangle (in tiles, of course), and its size, to define it in terms of two points: top-left (x1, y1) and bottom-right (x2, y2). Why not store directly its top-left coordinates and size? Well, it's much easier to loop through the room's tiles this way, since Python's range function takes arguments in this form. It will also be apparent later that the code for intersecting rectangles (for example, to be sure rooms don't overlap) is easier to define this way.

Python's _range_ function, however, excludes the last element in the loop. For example, _for x in range(x1, x2)_ will only loop until _x2 - 1_. So the code to fill a room with unblocked tiles could be:

```python
def create_room(room):
    global map
    #go through the tiles in the rectangle and make them passable
    for x in range(room.x1, room.x2 + 1):
        for y in range(room.y1, room.y2 + 1):
            map[x][y].blocked = False
            map[x][y].block_sight = False
```

The + _1_ at the end takes care of the issue we mentioned. But we actually want to leave some walls at the border of the room, so we'll leave out one tile in all directions.

```python
def create_room(room):
    global map
    #go through the tiles in the rectangle and make them passable
    for x in range(room.x1 + 1, room.x2):
        for y in range(room.y1 + 1, room.y2):
            map[x][y].blocked = False
            map[x][y].block_sight = False
```

Subtle, but important! This way, even if two rooms are right next to each other (but not overlapping), there's always one wall separating them. Now we're going to modify the _make_map_ function to start out with all tiles blocked, and carve two rooms in the map with our new function.

```python
def make_map():
    global map

    #fill map with "blocked" tiles
    map = [[ Tile(True)
        for y in range(MAP_HEIGHT) ]
            for x in range(MAP_WIDTH) ]

    #create two rooms
    room1 = Rect(20, 15, 10, 15)
    room2 = Rect(50, 15, 10, 15)
    create_room(room1)
    create_room(room2)
```

Before testing out, make the player appear in the center of the first room:

```python
    player.x = 25
    player.y = 23
```

You can walk around the first room, but not reach the second. We'll define a function to carve a horizontal tunnel:

```python
def create_h_tunnel(x1, x2, y):
    global map
    for x in range(min(x1, x2), max(x1, x2) + 1):
        map[x][y].blocked = False
        map[x][y].block_sight = False
```

There's some creative use of the min and max functions there, so we'll explain that a bit. Feel free to skip this if you got it. They'll return the minimum or maximum of both arguments, but why are they needed? Well, if x1 < x2, x1 will be the minimum of both, and x2 the maximum. So that line will be the same as:

```python
    for x in range(x1, x2 + 1):
```

If they're reversed, the same line wouldn't work -- as it is, _for_ only loops from a small number to a bigger number. But returning to the original line, then x2 will be the minimum, and x1 maximum. You guessed it -- it will be the same as:

```python
    for x in range(x2, x1 + 1):
```

It could be solved with other logic: swapping their values, changing the _for_ 's step to negative, having an _if_ to choose between the two lines... The functions _min_ and _max_ tend to give the shortest code, though it may be harder to understand if you're not used to them much.

The function to carve vertical functions is pretty similar.

```python
def create_v_tunnel(y1, y2, x):
    global map
    #vertical tunnel
    for y in range(min(y1, y2), max(y1, y2) + 1):
        map[x][y].blocked = False
        map[x][y].block_sight = False
```

Now, in _make_map_, we can connect both rooms with a horizontal tunnel.

```python
create_h_tunnel(25, 55, 23)
```

That concludes the test for the carving functions! It's really starting to look better by the minute. You can create different dungeon configurations with them, but it's tricky defining the coordinates by hand and we're going to rush right away to the most interesting bit: random dungeon generation!

You can find the whole code for this part [here](complete_roguelike_tutorial_using_python+libtcod_part_3_code.md#Dungeon_building_blocks).

### Dungeon generator

---

Finally, the dungeon generator. After this point you'll have accomplished something that is truly a turning point in the development of a roguelike, and that is no small feat! There are many different approaches to coding a dungeon generator, and there is no consensus; everyone has their own interpretation of the right way to do this. Perhaps after being content with this one for a while you'll think of different ways to improve it.

The basic idea is this. Pick a random location for the first room, and carve it. Then pick another location for the second; it cannot overlap with the first. Connect the two with a tunnel, and repeat. This will yield a sequence of connected rooms.

One important property is that the player is guaranteed to be able to reach every room. The downside is that there will be lots of overlaps between the tunnels, and even tunnels overlapping rooms, but that's not too bad since the player can't distinguish between tunnel floor and room floor. The overlaps actually give it a more complicated look than if it were just a simple string of rooms!

Alright, we can see that the Rect class needs a method to check for intersections, so that no two rooms can overlap. We'll also create a method to return the center coordinates of the room since that's where all tunnels will be connected.

```python
    def center(self):
        center_x = (self.x1 + self.x2) / 2
        center_y = (self.y1 + self.y2) / 2
        return (center_x, center_y)

    def intersect(self, other):
        #returns true if this rectangle intersects with another one
        return (self.x1 <= other.x2 and self.x2 >= other.x1 and
                self.y1 <= other.y2 and self.y2 >= other.y1)
```

Don't worry if the intersection logic seems alien, it's a direct formula that doesn't require interpretation! It's one of those copy-and-paste things. The constants for the dungeon generation are simply the room's minimum and maximum size, and the maximum number of rooms.

```python
ROOM_MAX_SIZE = 10
ROOM_MIN_SIZE = 6
MAX_ROOMS = 30
```

Ok, we've described the gist of the algorithm, took care of all helper functions and classes... Now to implement it in _make_map_. Remove the previous code that creates the example rooms and tunnel, and make a loop to iterate until the maximum number of rooms, assigning random coordinates and size to each one as we go.

```python
    rooms = []
    num_rooms = 0

    for r in range(MAX_ROOMS):
        #random width and height
        w = libtcod.random_get_int(0, ROOM_MIN_SIZE, ROOM_MAX_SIZE)
        h = libtcod.random_get_int(0, ROOM_MIN_SIZE, ROOM_MAX_SIZE)
        #random position without going out of the boundaries of the map
        x = libtcod.random_get_int(0, 0, MAP_WIDTH - w - 1)
        y = libtcod.random_get_int(0, 0, MAP_HEIGHT - h - 1)
```

The _random_get_int_ function returns a random integer between two numbers; the first parameter, zero, identifies the "stream" to get that number from. Random number streams are useful if you want to recreate sequences of random numbers; for our purposes it's not needed, so we use the default stream.

The _Rect_ class will really start to come in handy at this point! The rooms list stores all previous rooms as _Rect_ 's. That way we can compare the new room with previous ones for intersection, and reject it if it overlaps.

```python
        #"Rect" class makes rectangles easier to work with
        new_room = Rect(x, y, w, h)

        #run through the other rooms and see if they intersect with this one
        failed = False
        for other_room in rooms:
            if new_room.intersect(other_room):
                failed = True
                break
```

If the room is valid, we can carve it with _create_room_! We'll also handle a special case: the player will start in the center of the first room.

```python
        if not failed:
            #this means there are no intersections, so this room is valid

            #"paint" it to the map's tiles
            create_room(new_room)

            #center coordinates of new room, will be useful later
            (new_x, new_y) = new_room.center()

            if num_rooms == 0:
                #this is the first room, where the player starts at
                player.x = new_x
                player.y = new_y
```

The tunnels deserve some attention. (This is a long example, but please be patient!) Sometimes a strictly horizontal or vertical tunnel can't connect two rooms. Imagine one is on one corner of the map (e.g., top-left), and the other in the opposing corner (e.g., bottom-right.) You'd need two tunnels going from the first room: one horizontal tunnel to reach the right side of the map, and one vertical tunnel to go from there to the bottom of the map (reaching the second room). Or the other way around: one vertical tunnel to reach the bottom of the map, and another horizontal one to reach the right side (reaching the second room.) Both options are valid, so we choose between them randomly.

So we're going to code that, and you'll see that this not only covers extreme cases like the one in the example, but also works perfectly when two rooms are side-by-side! Since in that case one of the tunnel segments (horizontal or vertical) will be very small, and only the other segment will be visible. It's a bit hard to visualize though, you'll have to see it in action.

```python
            else:
                #all rooms after the first:
                #connect it to the previous room with a tunnel

                #center coordinates of previous room
                (prev_x, prev_y) = rooms[num_rooms-1].center()

                #draw a coin (random number that is either 0 or 1)
                if libtcod.random_get_int(0, 0, 1) == 1:
                    #first move horizontally, then vertically
                    create_h_tunnel(prev_x, new_x, prev_y)
                    create_v_tunnel(prev_y, new_y, new_x)
                else:
                    #first move vertically, then horizontally
                    create_v_tunnel(prev_y, new_y, prev_x)
                    create_h_tunnel(prev_x, new_x, new_y)

            #finally, append the new room to the list
            rooms.append(new_room)
            num_rooms += 1
```

You made it through all that in one piece, eh? Let's test it out! With some luck, some of the dungeons you get can be pretty amazing. Not too shabby for our own little algorithm!

The final code for the dungeon generator is [here](complete_roguelike_tutorial_using_python+libtcod_part_3_code.md#Dungeon_generator).

## Optional part: Visualizing the dungeon

---

If you're looking at the maps this program is creating and trying to figure out just how the code works, or you've tried playing around with the code to create a dungeon differently but it isn't doing quite what you expected, you can add a label to each room to show the order in which they were created. The game works with single characters, and we will often have more than 10 rooms, so a number doesn't work. We'll put a letter in the middle of each room instead. Add these lines after (new_x, new_y) = new_room.center().

```python
            #optional: print "room number" to see how the map drawing worked
            #          we may have more than ten rooms, so print 'A' for the first room, 'B' for the next...
            room_no = Object(new_x, new_y, chr(65+num_rooms), libtcod.white)
            objects.insert(0, room_no) #draw early, so monsters are drawn on top
```

This allows for 26 rooms. **MAX_ROOMS** is set to 30, but it would be extremely unlikely that all 30 would be created without any overlaps. However, if you tweak some of the code and do end up with more than 30 rooms, this will print various symbols such as '[' and then lower case letters. It won't fail unless you have more than 62 rooms.

Because this is optional code, it isn't included in the code samples linked from each part of the tutorial.

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_4.md).
