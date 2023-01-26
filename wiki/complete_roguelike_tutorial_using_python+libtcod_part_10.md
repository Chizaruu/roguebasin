# Complete Roguelike Tutorial, using python+libtcod, part 10

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## Main menu and saving

---

### Tidy initialization

---

Now that our game is bursting with gameplay potential, we can think about those little things that die-hard fans will surely miss during their long playing sessions. One of the most important is, of course, a save/load mechanism! This way they can go to sleep and dream about your scary monsters between play sessions.

To choose between continuing a previous game or starting a new one we need a main menu. But wait: our initialization logic and game loop are tightly bound, so they're not really prepared for these tasks. To avoid code duplication, we need to break them down into meaningful blocks (functions). We can then put them together to change between the menu and the game, start new games or load them, and even go to new dungeon levels. It's much easier than it sounds, so fear not!

Take a look at your initialization and game loop code, after all the functions. I can identify 4 blocks:

- **System initialization** (code between _console_set_custom_font_ and _panel =_ ...).
- **Setting up a new game** (everything else except for the game loop and the FOV map creation).
- **Creating the FOV map**.
- **Starting the game loop**.

The reason why I chose this separation is because I think they're the minimal blocks needed to do all the tasks. The system initialization must be done just once. Thinking out loud, here are outlines of all the tasks:

- **Create a new game**: set up the game, create FOV map, start game loop. (This is what we have now.)
- **Load game**: load data (we won't deal with this block now), create FOV map, start game loop.
- **Advance level**: set up new level (we won't deal with this block now), create FOV map. (The game loop is already running and will just continue.)

Hopefully that will make at least some sense, it's not very detailed but gives us something to aim at.

The system initialization code will stay right where it is, it's the first thing the script executes. Now grab the rest of the code before the main loop and put it in a new function; except for FOV map creation (and _fov_recompute = True_), the lines _player_action = None_ and _objects = [player]_, which will go elsewhere:

```python
def new_game():
    global player, inventory, game_msgs, game_state
    
    #create object representing the player
    fighter_component = Fighter(hp=30, defense=2, power=5, death_function=player_death)
    player = Object(0, 0, '@', 'player', libtcod.white, blocks=True, fighter=fighter_component)

    #generate map (at this point it's not drawn to the screen)
    make_map()

    game_state = 'playing'
    inventory = []

    #create the list of game messages and their colors, starts empty
    game_msgs = []

    #a warm welcoming message!
    message('Welcome stranger! Prepare to perish in the Tombs of the Ancient Kings.', libtcod.red)
```

We have to declare some variables as global, since we're assigning them inside a function now. The FOV map creation code goes in its own function, declaring some globals as well:

```python
def initialize_fov():
    global fov_recompute, fov_map
    fov_recompute = True
    
    #create the FOV map, according to the generated map
    fov_map = libtcod.map_new(MAP_WIDTH, MAP_HEIGHT)
    for y in range(MAP_HEIGHT):
        for x in range(MAP_WIDTH):
            libtcod.map_set_properties(fov_map, x, y, not map[x][y].block_sight, not map[x][y].blocked)
```

Finally, the game loop and the _player_action = None_ line belong to their own function now:

```python
def play_game():
    global key, mouse

    player_action = None
    
    mouse = libtcod.Mouse()
    key = libtcod.Key()
    while not libtcod.console_is_window_closed():
        #render the screen
        libtcod.sys_check_for_event(libtcod.EVENT_KEY_PRESS|libtcod.EVENT_MOUSE,key,mouse)
        render_all()
        
        libtcod.console_flush()

        #erase all objects at their old locations, before they move
        for object in objects:
            object.clear()
        
        #handle keys and exit game if needed
        player_action = handle_keys()
        if player_action == 'exit':
            break
        
        #let monsters take their turn
        if game_state == 'playing' and player_action != 'didnt-take-turn':
            for object in objects:
                if object.ai:
                    object.ai.take_turn()
```

That was a lot of shuffling stuff around! Ok, just a couple of tiny changes left. The _new_game_ function should initialize FOV right after creating the map:

```python
    initialize_fov()
```

And, since the objects' list represents objects on the map, it makes sense to initialize it on map creation, so put it in _make_map_:

```python
def make_map():
    global map, objects
    
    #the list of objects with just the player
    objects = [player]
```

Finally, after system initialization you need to call the new functions to start playing:

```python
new_game()
play_game()
```

That's it! The code is much tidier. If you don't care much about that, though, and prefer visible changes, then the next section is just for you!

### The main menu

---

To keep our main menu from appearing a bit bland, it would be pretty cool to show a neat background image below it. Fortunately, libtcod already has a whole lot of [image-related functions](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/image.html?c=false&cpp=false&cs=false&py=true&lua=false)!

Of course, since libtcod emulates a console, we can't directly show arbitrary images, since we can't access the console's pixels. We can, however, modify the background color of every console cell to match the color of a pixel from the image. The downside is that the image will be in a very low resolution. However, Jice came up with a neat trick: by using specialized characters, and modifying both foreground and background colors, we can double the resolution! This is called subcell resolution, and [this page of the docs](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/image_blit.html?c=false&cpp=false&cs=false&py=true&lua=false) shows some images of the effect (at the end of the page).

This means that, for our 80x50 cells console, we need a 160x100 pixels image. [Here's the one I'm using](http://roguecentral.org/doryen/files/menu_background1.png), just download it and put it in your game's folder if you don't want to draw your own. I searched for real dungeon photos with a Creative Commons license using the advanced search in [Yahoo Images](http://images.search.yahoo.com/images/advanced) (I tried it on [Google Images](http://www.google.com/advanced_image_search) too but didn't get as many results), then fired up [Paint.net](http://www.getpaint.net/) to resize it and modify it as much as possibly to give it the feel of a fantasy setting: torches, no natural light, a guy with a sword, that sort of stuff. I found that stacking layers and using a brush with an extremely low alpha (a value of 3 or so) lets me draw things very gradually, since I'm not exactly an artist! It guess it's OK to spend some time with this since it may be the only actual image in the whole game.

Right, after that small detour, it's back to coding! Let's create a function for the main menu interface, and begin by loading and showing the image:

```python
def main_menu():
    img = libtcod.image_load('menu_background.png')
    
    #show the background image, at twice the regular console resolution
    libtcod.image_blit_2x(img, 0, 0, 0)
```

It's really easy. Notice the file name is a bit different from the image I linked earlier. We can now take advantage of our reusable _menu_ function to present 3 options: start game, continue, or quit. Since the function also returns if the player presses a different key (canceled), we need to wrap it in a loop to present the menu again in that situation.

```python
def main_menu():
    img = libtcod.image_load('menu_background.png')
    
    while not libtcod.console_is_window_closed():
        #show the background image, at twice the regular console resolution
        libtcod.image_blit_2x(img, 0, 0, 0)
        
        #show options and wait for the player's choice
        choice = menu('', ['Play a new game', 'Continue last game', 'Quit'], 24)
        
        if choice == 0:  #new game
            new_game()
            play_game()
        elif choice == 2:  #quit
            break
```

Let's test this out! In the main script, instead of starting a new game right away with _new_game()_ and _play_game()_, call our new function:

```python
main_menu()
```

The main menu is working now, apart from the "continue" feature which we'll get to later. Now it's time to add some fluff. Before calling the menu function, I want to show my game's title and, of course, the author's name! You will probably want to modify this for your own game, of course.

```python
        #show the game's title, and some credits!
        libtcod.console_set_default_foreground(0, libtcod.light_yellow)
        libtcod.console_print_ex(0, SCREEN_WIDTH/2, SCREEN_HEIGHT/2-4, libtcod.BKGND_NONE, libtcod.CENTER,
            'TOMBS OF THE ANCIENT KINGS')
        libtcod.console_print_ex(0, SCREEN_WIDTH/2, SCREEN_HEIGHT-2, libtcod.BKGND_NONE, libtcod.CENTER,
            'By Jotaf')
```

You'll notice that the menu rectangle itself starts with a blank line. This is because the header string is empty, but _console_height_left_rect_ reports its height as 1 by default. To make that line go away, we need to check that condition in the _menu_ function, between the lines _header_height =_ ... and _height =_ ....

```python
    if header == '':
        header_height = 0
```

Another detail is that the player may want to switch to fullscreen in the main menu. However, we only check that key during the game. It's fairly easy to copy that code to the _menu_ function too, right after asking for the key:

```python
    if key.vk == libtcod.KEY_ENTER and key.lalt:  #(special case) Alt+Enter: toggle fullscreen
        libtcod.console_set_fullscreen(not libtcod.console_is_fullscreen())
```

Finally, extensive testing shows that starting a new game, going back to the main menu with Escape and starting another game results in a bug! In the second game, parts from the first are still visible. For the screen to always start black we need to clear the console in _initialize_fov_.

```python
    libtcod.console_clear(con)  #unexplored areas start black (which is the default background color)
```

There it is, a neat main menu, and with only a handful of lines of code! If you want to add mouse support to your menus, this is a good time to do it. [See this extra](http://www.roguebasin.com/index.php?title=Complete_Roguelike_Tutorial,_using_Python%2Blibtcod,_extras#Mouse-driven_Menus) for a small code change that will do the trick!

### Saving and loading

---

If you've tried it before in other languages, this probably sounds scary. It usually means tracing every last bit of state information in your game and cramming it into a raw stream of bytes. Not in Python -- the Pickle module is capable of doing that with only a few function calls!

But we can do even better by using the Shelve module: its interface is a simple dictionary. If you haven't used dictionaries before, check the [Python tutorial on the subject](http://docs.python.org/tutorial/datastructures.html#dictionaries). They're extremely useful, replacing lists in situations where you wish to index elements not by their position on the list, but by an arbitrary name, number, or any other key.

A Shelve allows you to store any object in a file, indexed by a string (for example, the original variable's name). So to store the map tiles and game objects in a file called "savegame", we only need to call:

```python
def save_game():
    #open a new empty shelve (possibly overwriting an old one) to write the game data
    file = shelve.open('savegame', 'n')
    file['map'] = map
    file['objects'] = objects
    file.close()
```

I bet your jaw just dropped over how easy it is to save a game in Python; mine sure did the first time I learned this! Just rinse and repeat for all your game state variables, and don't forget to call close at the end. The Shelve module will recursively find everything referenced from these variables onwards, so tile instances referenced by the map will be saved, as well as components referenced by game objects, which in turn are referenced by the objects list.

The only quirk to keep in mind is when more than one variable references the same object. For example, the objects list references the player object, but the player variable also references it. Normally this doesn't present a problem, but the Shelve module doesn't keep track of shared references between different dictionary entries. So if you try this:

```python
    file['player'] = player
```

You'll get two player objects when you load! Because one instance will be saved by _file['objects'] = objects_ and another by _file['player'] = player_. To avoid this we'll just save the index of the player in the list:

```python
    file['player_index'] = objects.index(player)  #index of player in objects list
```

Here are a few more variables to keep. Also, don't forget to _import shelve_ at the top of your code.

```python
    file['inventory'] = inventory
    file['game_msgs'] = game_msgs
    file['game_state'] = game_state
```

To automatically save when the player quits, call the new function in _play_game_, right before the _break_ line:

```python
            save_game()
```

That's all for saving. Loading follows the same procedure, except we assign to our variables the contents of the Shelve dictionary. Since we're assigning values to global variables, they need to be declared as such. The _player_index_ we stored lets us assign the _player_ variable to the correct object in the objects list. Having completely restored the core variables of the game, we can initialize the FOV map based on the loaded tiles.

```python
def load_game():
    #open the previously saved shelve and load the game data
    global map, objects, player, inventory, game_msgs, game_state
    
    file = shelve.open('savegame', 'r')
    map = file['map']
    objects = file['objects']
    player = objects[file['player_index']]  #get index of player in objects list and access it
    inventory = file['inventory']
    game_msgs = file['game_msgs']
    game_state = file['game_state']
    file.close()
    
    initialize_fov()
```

We can now add new functionality to the _main_menu_ function, so the player can actually continue a previous game! It needs to be wrapped in a try-except clause, because Shelve will throw an error if loading fails somehow (file doesn't exist, is corrupted, etc). If something goes wrong we just display a message about it and carry on. I also defined a new handy _msgbox_ function for important messages like this.

```python
        if choice == 1:  #load last game
            try:
                load_game()
            except:
                msgbox('\n No saved game to load.\n', 24)
                continue
            play_game()
```

And _msgbox_ relies on the _menu_ function entirely. We could call it directly instead of this _msgbox_, but in my opinion this makes code more readable by clearly conveying the intent (a message box instead of a list of options).

```python
def msgbox(text, width=50):
    menu(text, [], width)  #use menu() as a sort of "message box"
```

That concludes the code for the main menu, saving and loading. A nice side-effect is that if the player quits after dying and then tries to continue, the character will still be dead! (Because _game_state == 'dead'_ .) So by saving to different files you could easily create "mortem" files so the player can remember previous lost games, which is pretty cool.

You can easily use the main menu as a template for other screens, like game options or character creation. And as you continually upgrade your game you'll sometimes have to add more state variables to the save/load functions; but hopefully with the Shelve module it won't be too bad. Keep an eye out for the quirk [I talked about earlier](http://roguebasin.roguelikedevelopment.org/index.php/Complete_Roguelike_Tutorial,_using_python%2Blibtcod,_part_10#Saving_and_loading), if you ever get duplicated objects or strange behavior after loading.

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_10_code.md).

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_11.md).
