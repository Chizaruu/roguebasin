# Complete Roguelike Tutorial, using python+libtcod, part 2

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## The object and the map

### Off-screen consoles

---

There's one small thing we need to get out of the way before we can continue. Notice that the drawing functions we called (_console_set_default_foreground_ and _console_put_char_) have their first argument set to 0, meaning that they draw on the root console. This is the buffer that is shown directly on screen.

It can be useful, however, to have other buffers to store the results of drawing functions without automatically showing them to the player. This is akin to drawing surfaces or buffers in other graphics libraries; but instead of pixels they store characters and colors so you can modify them at will. Some uses of these off-screen consoles include semi-transparency or fading effects and composing GUI panels by blitting them to different portions of the root console. We're going to draw on an off-screen console from now on. The main reason is that not doing so would mean that later on you can't compose different GUI panels as easily, or add certain effects.

First, create a new off-screen console, which for now will occupy the whole screen, but this can be changed later. We'll use a simple name like con because it will be used a lot! You can put this in the initialization, right after _console_init_root_.

```python
con = libtcod.console_new(SCREEN_WIDTH, SCREEN_HEIGHT)
```

Now change the first argument of _console_put_char_ (there are 2 calls to this function) and _console_set_default_foreground_, from 0 to con. They're now drawing on the new console.

Finally, just before _console_flush()_, blit the contents of the new console to the root console, to display them. The parameters may look a bit mysterious at first, but they're just saying that the source rectangle has its top-left corner at coordinates (0, 0) and is the same size as the screen; the destination coordinates are (0, 0) as well. Check the documentation on [the console_blit function](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/console_offscreen.html?c=false&cpp=false&cs=false&py=true&lua=false#6) for more details.

```python
libtcod.console_blit(con, 0, 0, SCREEN_WIDTH, SCREEN_HEIGHT, 0, 0, 0)
```

That was a lot of talk for so little code and no visible change! The next section will surely be much more interesting, as we'll introduce our first dummy NPC among other things. Remember to check back the above documentation page when you get to coding your GUI.

### Generalizing

---

Now that we have the @ walking around, it would be a good idea to step back and think a bit about the design. Having variables for the player's coordinates is easy, but it can quickly get out of control when you're defining things such as HP, bonuses, and inventory. We're going to take the opportunity to generalize a bit.

Now, there can be such a thing as over-generalization, but we'll try not to fall in that trap. What we're going to do is define the player as a game Object, by creating that class. It will hold all position and display information (character and color). The neat thing is that the player will just be one instance of the Object class -- it's general enough that you can re-use it to define items on the floor, monsters, doors, stairs; anything representable by a character on the screen. Here's the class, with the initialization, and three common methods move, draw and clear. The code for drawing and erasing is the same as the one we used for the player earlier.

```python
class Object:
    #this is a generic object: the player, a monster, an item, the stairs...
    #it's always represented by a character on screen.
    def __init__(self, x, y, char, color):
        self.x = x
        self.y = y
        self.char = char
        self.color = color

    def move(self, dx, dy):
        #move by the given amount
        self.x += dx
        self.y += dy

    def draw(self):
        #set the color and then draw the character that represents this object at its position
        libtcod.console_set_default_foreground(con, self.color)
        libtcod.console_put_char(con, self.x, self.y, self.char, libtcod.BKGND_NONE)

    def clear(self):
        #erase the character that represents this object
        libtcod.console_put_char(con, self.x, self.y, ' ', libtcod.BKGND_NONE)
```

Please note that there are two underscores on each side of `__init__`!

Now, before the main loop, instead of just setting the player's coordinates, we create it as an actual Object. We also add it to a list, that will hold all objects that are in the game. While we're at it we'll add a yellow @ that represents a non-playing character, like in an RPG, just to test it out!

```python
player = Object(SCREEN_WIDTH/2, SCREEN_HEIGHT/2, '@', libtcod.white)
npc = Object(SCREEN_WIDTH/2 - 5, SCREEN_HEIGHT/2, '@', libtcod.yellow)
objects = [npc, player]
```

We'll have to make a couple of changes now. First, in the handle_keys function, instead of dealing directly with the player's coordinates, we can use the player's move method with the appropriate displacement. Later this will come in handy as it can automatically check if the player (or another object) is about to hit a wall. Secondly, the main loop will now clear all objects like this:

```python
    for object in objects:
        object.clear()
```

And draw them like this:

```python
    for object in objects:
        object.draw()
```

Ok, that's all! A fully generic object system. Later, this class can be modified to have all the special info that items, monsters and all that will require. But we can add that as we go along!

[Here](complete_roguelike_tutorial_using_python+libtcod_part_2_code.md#Generalizing)'s the code so far.

### The Map

---

Just like how you generalized the concept of the player object, you'll now do the same thing with the dungeon map. Your map will be a two-dimensional array of tiles where all your dungeon adventuring will happen. We'll start by defining its size at the top of the file. It's not quite the same size as the screen, to leave some space for a panel to show up later (where you can show stats and all). We'll try to make this as configurable as possible, this should suffice for now!

```python
MAP_WIDTH = 80
MAP_HEIGHT = 45
```

Next, the tile colors. For now there are two tile types -- wall and ground. These will be their "dark" colors, which you'll see when they're not in FOV; their "lit" counterparts are not needed right now. Notice that their values are between 0 and 255, if you found colors on the web in hexadecimal format you'll have to convert them with a calculator. Finding RGB values by educated trial-and-error works at first but with time you'll have a set of colors that don't mix together very well (contrast and tone as perceived by the human eye, and all that stuff), so it's usually better to look at a chart of colors; just search for "html colors" and use one you like.

```python
color_dark_wall = libtcod.Color(0, 0, 100)
color_dark_ground = libtcod.Color(50, 50, 150)
```

What sort of info will each tile hold? We'll start simple, with two values that say whether a tile is passable or not, and whether it blocks sight. In this case, it's better to seperate them early, so later you can have see-through but unpassable tiles such as chasms, or passable tiles that block sight for secret passages. They'll be defined in a Tile class, that we'll add to as we go. Believe me, this class will quickly grow to have about a dozen different values for each tile!

```python
class Tile:
    #a tile of the map and its properties
    def __init__(self, blocked, block_sight = None):
        self.blocked = blocked

        #by default, if a tile is blocked, it also blocks sight
        if block_sight is None: block_sight = blocked
        self.block_sight = block_sight
```

As promised, the map is a two-dimensional array of tiles. The easiest way to do that is to have a list of rows, each row itself being a list of tiles, since there are no native multi-dimensional arrays in Python. We'll build it using a neat trick, [list comprehensions](http://docs.python.org/tutorial/datastructures.html#list-comprehensions). See, the usual way to build lists (from C++ land) is to create an empty list, then iterate with a for and add elements gradually. But in Python, the syntax [element for index in range], where index and range are the same as what you'd use in a for, will return a list of elements. Just take a second to understand that sentence if you never worked with that before. With two of those, one for rows and another for tiles in each row, we create the map in one fell swoop! The linked page has a ton of examples on that, and also an example of [nested list comprehensions](http://docs.python.org/tutorial/datastructures.html#nested-list-comprehensions) like we're using for the map. Well, that's an awful lot of words for such a tiny piece of code!

```python
def make_map():
    global map

    #fill map with "unblocked" tiles
    map = [[ Tile(False)
        for y in range(MAP_HEIGHT) ]
            for x in range(MAP_WIDTH) ]
```

Accessing the tiles is as easy as map\[x][y]. Here we add two pillars (blocked tiles) to demonstrate that, and provide a simple test.

```python
    map[30][22].blocked = True
    map[30][22].block_sight = True
    map[50][22].blocked = True
    map[50][22].block_sight = True
```

One very important piece of advice: in list comprehensions, always call the constructor of the objects you're creating, like we did with Tile(False). If we had tried to first create an unblocked tile like floor = Tile(False) and then in the list comprehension just refer to that same floor, we'd get all sorts of weird bugs! This is a common rookie (and veteran!) mistake in Python. That's because all elements in the list would point to the exact same Tile (the one you defined as floor), not copies of it. Changing a property of one element would appear to change it in other elements as well! Calling the constructor for every element ensures that each is a distinct instance.

Don't worry, we're already close to a playable version! Since we need to draw both the objects and the map, it now makes sense to put them all under a new function instead of directly in the main loop. Take the object rendering code to a new _render_all_ function, and in its place (in the main loop) call _render_all()_.

```python
def render_all():
    #draw all objects in the list
    for object in objects:
        object.draw()
```

Still in the same function, we can now go through all the tiles and draw them to the screen, with the background color of a console character representing the corresponding tile. This will render the map.

```python
    for y in range(MAP_HEIGHT):
        for x in range(MAP_WIDTH):
            wall = map[x][y].block_sight
            if wall:
                libtcod.console_set_char_background(con, x, y, color_dark_wall, libtcod.BKGND_SET )
            else:
                libtcod.console_set_char_background(con, x, y, color_dark_ground, libtcod.BKGND_SET )
```

Also, move the _console_blit_ call to the end of _render_all()_ since it's part of the rendering code, just to keep things tidy.

A little note on customization: if you want a more old-school look, using characters like ' . ' and ' # ' to represent floor and wall tiles, [check out this Extra](complete_roguelike_tutorial_using_python+libtcod_extras.md#Old-school_wall_and_floor_tiles).

Ok! Don't forget to call make_map() before the main loop, to set it up before the game begins. You should be able to see the two pillars and walk around the map now!

But wait, there's something wrong. The pillars show up, but the player can walk over them. That's easy to fix though, add this check to the beginning of the Object 's move method:

```python
if not map[self.x + dx][self.y + dy].blocked:
```

[Here](complete_roguelike_tutorial_using_python+libtcod_part_2_code.md#The_Map)'s the code so far.

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_3.md).
