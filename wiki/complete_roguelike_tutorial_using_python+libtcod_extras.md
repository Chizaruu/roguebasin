# Complete Roguelike Tutorial, using Python+libtcod, extras

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

## Extras

---

### A neat Python shortcut for Notepad++

---

Although Notepad++ is light and has many nice features, it can be tricky to set up a shortcut to quickly run your game and see any errors or debug output. After reading the docs on Run commands, you might try to open a console (cmd) that doesn't close after the program runs so you can see debug output (/k), and using the path to the currently open file:

```text
cmd /k "$(FULL_CURRENT_PATH)"
```

You then may puzzle over the following error (or similar), which doesn't seem connected to Notepad++ at all:

```text
WindowsError: [Error 126] The specified module could not be found
```

The problem is this: Notepad++ and Python are smart enough to run the file, but not smart enough to initialize the "current directory" to the file's directory. So when trying to load libtcod, Python looks for it in all the usual places except where you put libtcod. (Computers can be so thick sometimes!) The fix is to previously "change directory" there, which can be done with a batch file.

Here's my setup. I want to launch the file in a console, so I can see any debug output, and I wanna keep it around in case of errors (to see the traceback) but close it automatically if the program runs fine -- accumulating lots of console windows when there are no errors is annoying. (To explicitly "pause" the console even when there are no errors call the built-in function raw_input from your program.) I simply created a windows batch file _debug_py.bat_ with this:

```batch
@echo off

cd %1
%2

if not errorlevel 1 goto quit
echo.
echo.
pause
:quit
```

Assuming parameter %1 is the file's directory, and %2 is the file name, this just changes to that dir and runs it, pausing the console if there's an error. The notepad++ shortcut that executes it with the correct parameters is this:

```text
"C:\whatever\debug_py.bat" "$(CURRENT_DIRECTORY)" $(FILE_NAME)
```

Just change "C:\whatever\debug_py.bat" to the full path of the bat file you created. To create that shortcut in Notepad++ go to menu Run->Run..., paste that line into the textbox, then choose Save. No more problems, and the console closes automatically if there are no errors.

### Old-school wall and floor tiles

---

Not a big fan of the new-school look of the map tiles in the tutorial? Perhaps you want your game to look more like one of the [Major Roguelikes](major_roguelikes.md), with ' . ' characters for floor and ' # ' for wall tiles. No problem, just a couple of tweaks.

First, the most obvious one: in the _render_all()_ function, replace the _console_set_back_ calls, which only change the background color of a tile, with _console_put_char_ex_, which changes everything (here's the [relevant page of the manual.](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/console_draw_basic.html?c=false&cpp=false&cs=false&py=true&lua=false#7)). So a line like this:

```python
libtcod.console_set_back(con, x, y, color_dark_wall, libtcod.BKGND_SET)
```

Changes to something like this:

```python
libtcod.console_put_char_ex(con, x, y, '#', libtcod.white, libtcod.dark_blue)
```

Which will change it to a white ' # ' character on a dark blue background. This is only an example; I'm sure you'd like to choose other colors!

If you change the floor characters too, it doesn't quite behave as expected, since the player erases the floor characters it steps on. To fix this you need to change the _Object's_ _clear_ method, to this:

```python
    def clear(self):
        #erase the character that represents this object
        if libtcod.map_is_in_fov(fov_map, self.x, self.y):
            libtcod.console_put_char_ex(con, self.x, self.y, '.', libtcod.white, libtcod.dark_blue)
```

Assuming, of course, those are the colors and character you wanted for a lit ground tile!

### Real-time combat

---

Ok, so you're making a real-time game. You got through the section on combat, but the system described there is more or less turn-based. Don't despair! The only thing missing is a speed system.

Each object will have a wait value, which tells the number of frames it has to wait until it can take another action (move or attack). It's decreased by 1 every frame, and when it's 0, the object can move or hit again! Doing it will increase the wait value, so it has to wait again.

We'll start by defining a few speed constants, which are the values that wait is increased to when taking certain actions.

```python
#number of frames to wait after moving/attacking
PLAYER_SPEED = 2
DEFAULT_SPEED = 8
DEFAULT_ATTACK_SPEED = 20
```

As you can see, we're making it a bit easier on the player, so he can maneuver around the monsters fast, but both attack at the same rate. These numbers are entirely tweakable of course!

The _Object's `__init__`_ method must accept a move speed. Just add _speed=DEFAULT_SPEED_ as a parameter; if unspecified, the default speed is used. The initialization code stores it, and sets _wait_ to 0:

```python
        self.speed = speed
        self.wait = 0
```

Whenever the object moves, it has to wait. At the end of the _move_ method:

```python
self.wait = self.speed
```

The _Fighter_ class stores the attack speed, so it's very similar. Its `__init__` method accepts the parameter _attack_speed=DEFAULT_ATTACK_SPEED_, and stores it with _self.attack_speed = attack_speed_. At the end of the attack method, the object has to wait until it can attack or move again:

```python
        self.owner.wait = self.attack_speed
```

Ok, but how do we enforce these wait periods? For the player, before testing for the movement keys (in _handle_keys_), add the wait logic right after the _if game_state == 'playing'_ ... line:

```python
        if player.wait > 0:  #don't take a turn yet if still waiting
            player.wait -= 1
            return
```

So the movement/attack keys aren't used if the player has to wait. Next, the same behavior for the monsters! Replace the _object.ai.take_turn()_ line with the block:

```python
                if object.wait > 0:  #don't take a turn yet if still waiting
                    object.wait -= 1
                else:
                    object.ai.take_turn()
```

Also, there's a condition that only lets the monsters move/attack after the player moved or attacked. This is appropriate for a turn-based game, but for real-time, it has to be removed. A few lines above the code you just modified, replace the line _if game_state == 'playing'_ and _player_action != 'didnt-take-turn'_: with just _if game_state == 'playing'_:.

And don't forget to switch to the non-blocking function in _handle_keys()_:

```python
    key = libtcod.console_check_for_keypress()
```

There it is, a speed system for a real-time game! Don't forget to add _speed=PLAYER_SPEED_ when creating the player, or any other speeds you want to modify. I left all the others as default.

### Mouse-driven Menus

---

Adding basic mouse support to the menu system is really easy. If you've completed [Part 9: Spells and ranged combat](complete_roguelike_tutorial_using_python+libtcod_part_9.md), then you've already seen the code needed to check the mouse position and button presses. We're going to make a tiny change to code in [Part 10: Main menu and saving](complete_roguelike_tutorial_using_python+libtcod_part_10.md), so keep that in mind if you haven't gotten that far yet.

Most of our changes will be to _menu()_. Let's start by adding the globals "key" and "mouse" to the top, so we can use these already defined references:

```python
def menu(header, options, width):
    global key, mouse
```

At this point, also move the following two lines from _play_game()_ to the very end of the file, just before _main_menu()_ is called. We'll need to initialize them now, since we're going to be using them in all menu code, including the main menu.

```python
mouse = libtcod.Mouse()
key = libtcod.Key()
```

Next, we'll get the x- and y-offsets of the menu options. These will let us calculate coordinates for the mouse relative to the menu options, instead of the screen, making it simple to tell if the mouse is inside the menu and where:

```python
    #compute x and y offsets to convert console position to menu position
    x_offset = x #x is the left edge of the menu
    y_offset = y + header_height #subtract the height of the header from the top edge of the menu
```

Because the main loop is paused while a menu is open, if we try to check the mouse position now, we'll only get its position the last time it was updated, at the start of the main loop. The same issue arose in the _target_tile()_ and _target_monster()_ functions, with the same solution. We'll enclose the rest of the _menu()_ function in a loop of its own, so that we can continuously update the mouse and check for input. Only when the player makes a selection will we break out of it and return to the game loop.

```python
    while True:
        #present the root console to the player and check for input
        libtcod.console_flush()
        libtcod.sys_check_for_event(libtcod.EVENT_KEY_PRESS|libtcod.EVENT_MOUSE,key,mouse)
```

Notice that we changed from _console_wait_for_keypress_ to _sys_check_for_event_. This lets us both update the mouse and avoid pausing execution. If you play the game now, you'll see that the inventory is unusable - it immediately closes itself. That's because we haven't added any checks yet, so the function immediately returns None before the player can react. Here is the updated _menu()_ function:

```python
def menu(header, options, width):
    global key, mouse
 
    if len(options) > 26: raise ValueError('Cannot have a menu with more than 26 options.')
 
    #calculate total height for the header (after auto-wrap) and one line per option
    header_height = libtcod.console_get_height_rect(con, 0, 0, width, SCREEN_HEIGHT, header)
    if header == '':
        header_height = 0
    height = len(options) + header_height
 
    #create an off-screen console that represents the menu's window
    window = libtcod.console_new(width, height)
 
    #print the header, with auto-wrap
    libtcod.console_set_default_foreground(window, libtcod.white)
    libtcod.console_print_rect_ex(window, 0, 0, width, height, libtcod.BKGND_NONE, libtcod.LEFT, header)
 
    #print all the options
    y = header_height
    letter_index = ord('a')
    for option_text in options:
        text = '(' + chr(letter_index) + ') ' + option_text
        libtcod.console_print_ex(window, 0, y, libtcod.BKGND_NONE, libtcod.LEFT, text)
        y += 1
        letter_index += 1
 
    #blit the contents of "window" to the root console
    x = SCREEN_WIDTH/2 - width/2
    y = SCREEN_HEIGHT/2 - height/2
    libtcod.console_blit(window, 0, 0, width, height, 0, x, y, 1.0, 0.7)

    #compute x and y offsets to convert console position to menu position
    x_offset = x #x is the left edge of the menu
    y_offset = y + header_height #subtract the height of the header from the top edge of the menu
    
    while True:
        #present the root console to the player and check for input
        libtcod.console_flush()
        libtcod.sys_check_for_event(libtcod.EVENT_KEY_PRESS|libtcod.EVENT_MOUSE,key,mouse)
 
        if (mouse.lbutton_pressed):
            (menu_x, menu_y) = (mouse.cx - x_offset, mouse.cy - y_offset)
            #check if click is within the menu and on a choice
            if menu_x >= 0 and menu_x < width and menu_y >= 0 and menu_y < height - header_height:
                return menu_y
 
        if mouse.rbutton_pressed or key.vk == libtcod.KEY_ESCAPE:
            return None #cancel if the player right-clicked or pressed Escape
 
        if key.vk == libtcod.KEY_ENTER and key.lalt:
            #Alt+Enter: toggle fullscreen
            libtcod.console_set_fullscreen(not libtcod.console_is_fullscreen())
 
        #convert the ASCII code to an index; if it corresponds to an option, return it
        index = key.c - ord('a')
        if index >= 0 and index < len(options): return index
        #if they pressed a letter that is not an option, return None
        if index >= 0 and index <= 26: return None
```

The idea is to get the latest inputs from the mouse and keyboard, then check them against the menu contents. If the mouse is left-clicked, use the x- and y-offsets we calculated earlier to determine where in the menu the user clicked. The leftmost tile of the first option (not the header!) will be (0, 0), while the rightmost tile of the last option will be (width - 1, height - 1). Because the choices are numbered top to bottom starting from 0, and can only take up one line, _menu_y_ will always equal the index of a valid choice if it lies inside the bounds of the menu. The rest is just checking for a few different ways to cancel and allowing the player to toggle fullscreen from inside a menu.

### Creating a Binary

---

Source is great, but let's be honest, forcing your players to recreate your development environment isn't very polite. Let's do them the courtesy of packaging it all up in a nice and tidy executable.

#### Windows+py2exe

Py2exe is a set of tools for creating stand-alone Windows programs from python scripts. Perfect, this is exactly what we want! [Download py2exe](http://sourceforge.net/projects/py2exe/files/py2exe/) (version 0.6.9) for your installed version of Python and install it. The project homepage has a [nice tutorial](http://www.py2exe.org/index.cgi/Tutorial) which can help get you started, or you can use my script I've provided below.

If you are using my script, set the _target_file_ to the name of the script that serves as the main point of entry for your game. Then just run the script! You should see a deal of console text about byte-compiling and copying, and if all goes well there should be two new directories. The dist folder is the one we care about, so go check it out.

Inside the dist folder there should be a shiny new executable file and a few other files. In my test I had: main.exe, SDL.dll, lib-tcod.dll, and w9xpopen.exe. Don't delete these, you need them! Run the executable to verify everything works fine and you are ready to package and distribute your game!

```python
from distutils.core import setup
import py2exe
import os
import sys

sys.argv.append('py2exe')

# The filename of the script you use to start your program.
target_file = 'main.py'

# The root directory containing your assets, libraries, etc.
assets_dir = '.\\'

# Filetypes not to be included in the above.
excluded_file_types = ['py','pyc','project','pydevproject']

def get_data_files(base_dir, target_dir, list=[]):
    """
    " * get_data_files
    " *    base_dir:    The full path to the current working directory.
    " *    target_dir:  The directory of assets to include.
    " *    list:        Current list of assets. Used for recursion.
    " *
    " *    returns:     A list of relative and full path pairs. This is 
    " *                 specified by distutils.
    """
    for file in os.listdir(base_dir + target_dir):
        
        full_path = base_dir + target_dir + file
        if os.path.isdir(full_path):
            get_data_files(base_dir, target_dir + file + '\\', list)
        elif os.path.isfile(full_path):
            if (len(file.split('.')) == 2 and file.split('.')[1] not in excluded_file_types):
                list.append((target_dir, [full_path]))
            
    return list

# The directory of assets to include.
my_files = get_data_files(sys.path[0] + '\\', assets_dir)

# Build a dictionary of the options we want.
opts = { 'py2exe': {
                    'ascii':'True',
                    'excludes':['_ssl','_hashlib'],
                    'includes' : ['anydbm', 'dbhash'],
                    'bundle_files':'1',
                    'compressed':'True'}}

# Run the setup utility.
setup(console=[target_file],
      data_files=my_files,
      zipfile=None,
      options=opts)
```

#### Linux+cx_Freeze

[Cx_Freeze](http://cx-freeze.sourceforge.net/) is a set of tools for creating Linux executables.

#### Mac+py2app

[Py2app](http://svn.pythonmac.org/py2app/py2app/trunk/doc/index.html) is a set of tools for creating Mac OSX applications.

### A* Pathfinding

---

The basic tutorial contains a simple pathfinding, but there's a lot of issues: for example the monsters cannot do a diagonal movement into a corridor, and they cannot navigate around other monsters.

[Libtcod](libtcod.md) contains a pathfinding module that includes a simple A* pathfinding. The following function will fix all these issues.

First we will fix the AI class _BasicMonster_. We'll replace the movement function _move_towards(player.x, player.y)_ with _move_astar(player)_ that takes the player as the parameter into which to move.

```python
class BasicMonster:
    #AI for a basic monster.
    def take_turn(self):
        #a basic monster takes its turn. if you can see it, it can see you
        monster = self.owner
        if libtcod.map_is_in_fov(fov_map, monster.x, monster.y):
 
            #move towards player if far away
            if monster.distance_to(player) >= 2:
                monster.move_astar(player)
 
            #close enough, attack! (if the player is still alive.)
            elif player.fighter.hp > 0:
                monster.fighter.attack(player)
```

The second thing is to create this function inside the Object class. The function creates a new path each turn that takes into account other monsters or unwalkable objects (these could be pillars, wells, altars)

```python
    def move_astar(self, target):
        #Create a FOV map that has the dimensions of the map
        fov = libtcod.map_new(MAP_WIDTH, MAP_HEIGHT)
        
        #Scan the current map each turn and set all the walls as unwalkable
        for y1 in range(MAP_HEIGHT):
            for x1 in range(MAP_WIDTH):
                libtcod.map_set_properties(fov, x1, y1, not map[x1][y1].block_sight, not map[x1][y1].blocked)

        #Scan all the objects to see if there are objects that must be navigated around
        #Check also that the object isn't self or the target (so that the start and the end points are free)
        #The AI class handles the situation if self is next to the target so it will not use this A* function anyway   
        for obj in objects:
            if obj.blocks and obj != self and obj != target:
                #Set the tile as a wall so it must be navigated around
                libtcod.map_set_properties(fov, obj.x, obj.y, True, False)

        #Allocate a A* path
        #The 1.41 is the normal diagonal cost of moving, it can be set as 0.0 if diagonal moves are prohibited
        my_path = libtcod.path_new_using_map(fov, 1.41)
        
        #Compute the path between self's coordinates and the target's coordinates
        libtcod.path_compute(my_path, self.x, self.y, target.x, target.y)
        
        #Check if the path exists, and in this case, also the path is shorter than 25 tiles
        #The path size matters if you want the monster to use alternative longer paths (for example through other rooms) if for example the player is in a corridor
        #It makes sense to keep path size relatively low to keep the monsters from running around the map if there's an alternative path really far away        
        if not libtcod.path_is_empty(my_path) and libtcod.path_size(my_path) < 25:
            #Find the next coordinates in the computed full path
            x, y = libtcod.path_walk(my_path, True)
            if x or y:
                #Set self's coordinates to the next path tile
                self.x = x
                self.y = y
        else:
            #Keep the old move function as a backup so that if there are no paths (for example another monster blocks a corridor)
            #it will still try to move towards the player (closer to the corridor opening)
            self.move_towards(target.x, target.y)  

        #Delete the path to free memory
        libtcod.path_delete(my_path)
```

### Using Graphical Tiles

---

This tutorial shows how to use graphical tiles instead of ASCII or solid color graphics.

Here's a sample font file that's used in this tutorial. Save this file as TiledFont.png. The tiles are from Angband's free 16x16 tileset with the font from roarl's nethack font:

![TiledFont.png](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/extras/hji1UI8.png)

New tiles can be added to that file with an image editor that preserves the transparency.

There are some things that MUST be taken into account when using tiles with libtcod.

1. The tile size must be related to the font size. It cannot be smaller than the font size, but it can be larger. If it's larger, it's size must be related to the font size by a multiplier. If the font size is 16x16, you can use 16x16, 16x32, 32x32 etc. sized tiles. Larger tiles must be drawn in multiple chunks of 16x16 if the font size is 16x16.

2. If the tile is shown as a solid block instead of the tile, it's determined as a greyscale tile. Each tile must have at least one pixel that's RGB values are not the same. It's easiest to change one of the pixels for example from [132, 132, 132] to [132, 132, 133] RGB values. It doesn't look any different, but libtcod interpretes it differently.

3. The font file must have empty rows at the bottom. If the tiles won't appear, add empty rows to the file.

4. Tiles are not transparent in one sense, you cannot draw one tile on top of the other so that the bottom tile will be shown too. This results in items not showing floor below them. If you've got only one floor tile, you can actually draw the items to the floor tile so it won't look weird.

First we will load the font file by modifing the font row at the bottom:

```python
#The font has 32 chars in a row, and there's a total of 10 rows. Increase the "10" when you add new rows to the sample font file
libtcod.console_set_custom_font('TiledFont.png', libtcod.FONT_TYPE_GREYSCALE | libtcod.FONT_LAYOUT_TCOD, 32, 10)
```

Then we must load the custom rows from the font so the tiles can be used. Be sure also to call it from somewhere at the beginning:

```python
def load_customfont():
    #The index of the first custom tile in the file
    a = 256
    
    #The "y" is the row index, here we load the sixth row in the font file. Increase the "6" to load any new rows from the file
    for y in range(5,6):
        libtcod.console_map_ascii_codes_to_font(a, 32, 0, y)
        a += 32
```

Then we will add variables to the tiles at where the other constants are declared at the top of the file. Here's all the tiles needed for the tutorial:

```python
wall_tile = 256 
floor_tile = 257
player_tile = 258
orc_tile = 259
troll_tile = 260
scroll_tile = 261
healingpotion_tile = 262
sword_tile = 263
shield_tile = 264
stairsdown_tile = 265
dagger_tile = 266
```

Now we modify the _render_all()_ function to render the map with the tiles. In the _"console_put_char_ex"_ function the first color is how the tile will be tinted. The next color will tell how the transparent parts will be colored, black is good enough now. We tint the "seen, but not in the FOV" tiles as grey, others will be untinted with white:

```python
        for y in range(MAP_HEIGHT):
            for x in range(MAP_WIDTH):
                visible = libtcod.map_is_in_fov(fov_map, x, y)
                wall = map[x][y].block_sight
                if not visible:
                    #if it's not visible right now, the player can only see it if it's explored
                    if map[x][y].explored:
                        if wall:
                            libtcod.console_put_char_ex(con, x, y, wall_tile, libtcod.grey, libtcod.black)
                        else:
                            libtcod.console_put_char_ex(con, x, y, floor_tile, libtcod.grey, libtcod.black)
                else:
                    #it's visible
                    if wall:
                        libtcod.console_put_char_ex(con, x, y, wall_tile, libtcod.white, libtcod.black)
                    else:
                        libtcod.console_put_char_ex(con, x, y, floor_tile, libtcod.white, libtcod.black)
                        #since it's visible, explore it
                    map[x][y].explored = True
```

Then we can modify the actual objects to use the tiles. Here's the "**@**" changed into _player_tile_. Remember to also make the color white so it will be untinted:

```python
    player = Object(0, 0, player_tile, 'player', libtcod.white, blocks=True, fighter=fighter_component)
```

Now you can change the other objects also to use the graphical tiles' variables and then you can enjoy the results!

![the results](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/extras/S95piRG.png)

### BSP Dungeon Generator

---

This tutorial explains how to use the Binary Space Partitioning module in libtcod. It's used for generating dungeons. The advantage in here is that the dungeons generated fill the map completely with rooms, instead of placing random rectangles and digging corridors between them.

BSP simplified is that a root node is divided into two nodes and then the two resulting nodes will be divided in two and repeated for the **DEPTH** amount. Then each of the nodes, so called leafs, will be transformed into rooms.

This tutorial is adapted to fit the main tutorial from the samples provided by libtcod.

First, add this in the top of the file. It will be used when determining the stairs room and the starting room

```python
import random
```

Next we create some constants for use within the dungeon generator.

**DEPTH** determines the amount of recursive rooms.

**MIN_SIZE** is the smallest room size.

**FULL_ROOMS** when False will create random sized rooms, when True it will create rooms that will be largest possible within the node area. It is recommended to use False to create more natural looking dungeons. Using True might be good when adding this into an existing area, for example creating a house indoor areas.

```python
DEPTH = 10
MIN_SIZE = 5
FULL_ROOMS = False
```

The following function is the main function that can be called instead of the basic tutorial's _make_map()_ function.

It essentially creates a dungeon using the BSP module, picks one of the rooms for stairs, and one for the player start, and then for the rest of the rooms adds enemies and items.

```python
def make_bsp():
    global map, objects, stairs, bsp_rooms

    objects = [player]
    
    map = [[Tile(True) for y in range(MAP_HEIGHT)] for x in range(MAP_WIDTH)]

    #Empty global list for storing room coordinates
    bsp_rooms = []
    
    #New root node
    bsp = libtcod.bsp_new_with_size(0, 0, MAP_WIDTH, MAP_HEIGHT)

    #Split into nodes
    libtcod.bsp_split_recursive(bsp, 0, DEPTH, MIN_SIZE + 1, MIN_SIZE + 1, 1.5, 1.5)

    #Traverse the nodes and create rooms                            
    libtcod.bsp_traverse_inverted_level_order(bsp, traverse_node)

    #Random room for the stairs
    stairs_location = random.choice(bsp_rooms)
    bsp_rooms.remove(stairs_location)
    stairs = Object(stairs_location[0], stairs_location[1], '<', 'stairs', libtcod.white, always_visible=True)
    objects.append(stairs)
    stairs.send_to_back()

    #Random room for player start
    player_room = random.choice(bsp_rooms)
    bsp_rooms.remove(player_room)
    player.x = player_room[0]
    player.y = player_room[1]
    
    #Add monsters and items
    for room in bsp_rooms:
        new_room = Rect(room[0], room[1], 2, 2)
        place_objects(new_room)
    
    initialize_fov()
```

This function is where the BSP stuff happens

```python
def traverse_node(node, dat):
    global map, bsp_rooms

    #Create rooms
    if libtcod.bsp_is_leaf(node):
        minx = node.x + 1
        maxx = node.x + node.w - 1
        miny = node.y + 1
        maxy = node.y + node.h - 1

        if maxx == MAP_WIDTH - 1:
            maxx -= 1
        if maxy == MAP_HEIGHT - 1:
            maxy -= 1

        #If it's False the rooms sizes are random, else the rooms are filled to the node's size
        if FULL_ROOMS == False:
            minx = libtcod.random_get_int(None, minx, maxx - MIN_SIZE + 1)
            miny = libtcod.random_get_int(None, miny, maxy - MIN_SIZE + 1)
            maxx = libtcod.random_get_int(None, minx + MIN_SIZE - 2, maxx)
            maxy = libtcod.random_get_int(None, miny + MIN_SIZE - 2, maxy)

        node.x = minx
        node.y = miny
        node.w = maxx-minx + 1
        node.h = maxy-miny + 1

        #Dig room
        for x in range(minx, maxx + 1):
            for y in range(miny, maxy + 1):
                map[x][y].blocked = False
                map[x][y].block_sight = False
        
        #Add center coordinates to the list of rooms
        bsp_rooms.append(((minx + maxx) / 2, (miny + maxy) / 2))

    #Create corridors    
    else:
        left = libtcod.bsp_left(node)
        right = libtcod.bsp_right(node)
        node.x = min(left.x, right.x)
        node.y = min(left.y, right.y)
        node.w = max(left.x + left.w, right.x + right.w) - node.x
        node.h = max(left.y + left.h, right.y + right.h) - node.y
        if node.horizontal:
            if left.x + left.w - 1 < right.x or right.x + right.w - 1 < left.x:
                x1 = libtcod.random_get_int(None, left.x, left.x + left.w - 1)
                x2 = libtcod.random_get_int(None, right.x, right.x + right.w - 1)
                y = libtcod.random_get_int(None, left.y + left.h, right.y)
                vline_up(map, x1, y - 1)
                hline(map, x1, y, x2)
                vline_down(map, x2, y + 1)

            else:
                minx = max(left.x, right.x)
                maxx = min(left.x + left.w - 1, right.x + right.w - 1)
                x = libtcod.random_get_int(None, minx, maxx)

                # catch out-of-bounds attempts
                while x > MAP_WIDTH - 1:
                        x -= 1

                vline_down(map, x, right.y)
                vline_up(map, x, right.y - 1)

        else:
            if left.y + left.h - 1 < right.y or right.y + right.h - 1 < left.y:
                y1 = libtcod.random_get_int(None, left.y, left.y + left.h - 1)
                y2 = libtcod.random_get_int(None, right.y, right.y + right.h - 1)
                x = libtcod.random_get_int(None, left.x + left.w, right.x)
                hline_left(map, x - 1, y1)
                vline(map, x, y1, y2)
                hline_right(map, x + 1, y2)
            else:
                miny = max(left.y, right.y)
                maxy = min(left.y + left.h - 1, right.y + right.h - 1)
                y = libtcod.random_get_int(None, miny, maxy)

                # catch out-of-bounds attempts
                while y > MAP_HEIGHT - 1:
                         y -= 1

                hline_left(map, right.x - 1, y)
                hline_right(map, right.x, y)

    return True
```

All the corridor diggings are made within these for every direction

```python
def vline(map, x, y1, y2):
    if y1 > y2:
        y1,y2 = y2,y1

    for y in range(y1,y2+1):
        map[x][y].blocked = False
        map[x][y].block_sight = False
        
def vline_up(map, x, y):
    while y >= 0 and map[x][y].blocked == True:
        map[x][y].blocked = False
        map[x][y].block_sight = False
        y -= 1
        
def vline_down(map, x, y):
    while y < MAP_HEIGHT and map[x][y].blocked == True:
        map[x][y].blocked = False
        map[x][y].block_sight = False
        y += 1
        
def hline(map, x1, y, x2):
    if x1 > x2:
        x1,x2 = x2,x1
    for x in range(x1,x2+1):
        map[x][y].blocked = False
        map[x][y].block_sight = False
        
def hline_left(map, x, y):
    while x >= 0 and map[x][y].blocked == True:
        map[x][y].blocked = False
        map[x][y].block_sight = False
        x -= 1
        
def hline_right(map, x, y):
    while x < MAP_WIDTH and map[x][y].blocked == True:
        map[x][y].blocked = False
        map[x][y].block_sight = False
        x += 1
```

![FULL_ROOMS = True](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/extras/XP2oe0v.png)

FULL_ROOMS = True

![FULL_ROOMS = False](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/extras/GpCNShI.png)

FULL_ROOMS = False
