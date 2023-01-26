# Complete Roguelike Tutorial, using python+libtcod, part 7

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## The GUI

---

### Status bars

---

Lots of stuff happens under the hood of a game that players don't really appreciate, like the combat mechanics detailed in the last couple of sections. We'll now work on something much more flashy -- the Graphical User Interface! Using the full power of libtcod's true-color consoles, and a bit of creativity, you can make some truly amazing graphics. You may argue that the limitations of a console actually make it easier to create a polished game, rather than if you had the freedom to position per-pixel graphics like most other games.

We'll start by creating a GUI panel at the bottom of the screen. Of course, you're welcome to change this to suit your taste. For now, it will hold the player's health bar and a colored message log.

It's easier to manage GUI windows and panels with an off-screen console for each one, created before the main loop:

```python
panel = libtcod.console_new(SCREEN_WIDTH, PANEL_HEIGHT)
```

The constant _PANEL_HEIGHT_ is defined later, along with others. Let's jump right to the "status bar" rendering code! This is fully generic and can be used for experience bars, mana bars, recharge times, dungeon level, you name it.

The bar has two parts, one rectangle that changes size according to the proportion between the value and the maximum value, and a background rectangle. It just takes a simple formula to calculate that size, and a few calls to libtcod's _console_rect_ function for the rectangles.

```python
def render_bar(x, y, total_width, name, value, maximum, bar_color, back_color):
    #render a bar (HP, experience, etc). first calculate the width of the bar
    bar_width = int(float(value) / maximum * total_width)

    #render the background first
    libtcod.console_set_default_background(panel, back_color)
    libtcod.console_rect(panel, x, y, total_width, 1, False, libtcod.BKGND_SCREEN)

    #now render the bar on top
    libtcod.console_set_default_background(panel, bar_color)
    if bar_width > 0:
        libtcod.console_rect(panel, x, y, bar_width, 1, False, libtcod.BKGND_SCREEN)
```

For extra clarity, the actual value and maximum are displayed as text over the bar, along with a caption ('Health', 'Mana', etc).

```python
    #finally, some centered text with the values
    libtcod.console_set_default_foreground(panel, libtcod.white)
    libtcod.console_print_ex(panel, x + total_width / 2, y, libtcod.BKGND_NONE, libtcod.CENTER,
        name + ': ' + str(value) + '/' + str(maximum))
```

Now we'll modify the main rendering function to use this. First, define a few constants: the height of the panel, its position on the screen (it's a bottom panel so only the Y is needed) and the size of the health bar.

```python
#sizes and coordinates relevant for the GUI
BAR_WIDTH = 20
PANEL_HEIGHT = 7
PANEL_Y = SCREEN_HEIGHT - PANEL_HEIGHT
```

I also changed _MAP_HEIGHT_ to 43 to give the panel more room. At the end of _render_all_, replace the code that shows the player's stats as text with the following code. It re-initializes the panel to black, calls our _render_bar_ function to display the player's health, then shows the panel on the root console.

```python
    #prepare to render the GUI panel
    libtcod.console_set_default_background(panel, libtcod.black)
    libtcod.console_clear(panel)

    #show the player's stats
    render_bar(1, 1, BAR_WIDTH, 'HP', player.fighter.hp, player.fighter.max_hp,
        libtcod.light_red, libtcod.darker_red)

    #blit the contents of "panel" to the root console
    libtcod.console_blit(panel, 0, 0, SCREEN_WIDTH, PANEL_HEIGHT, 0, 0, PANEL_Y)
```

Time to test it -- that health bar looks pretty sweet! And you can easily make more like it with different colors and all.

A small detail, the console where the map is rendered (con) should be the size of the map, not the size of the screen. This is more noticeable now that the panel takes up quite a bit of space. Change _SCREEN_WIDTH_ and _SCREEN_HEIGHT_ to _MAP_WIDTH_ and _MAP_HEIGHT_ when creating this console and blitting it. It's the _con = libtcod.console_new(...)_ line before the main loop, and the first _console_blit_ in _render_all_.

### The message log

---

Until now the combat messages were dumped in the standard console -- not very user-friendly. We'll make a nice scrolling message log embedded in the GUI panel, and use colored messages so the player can know what happened with a single glance. It will also feature word-wrap!

The constants that define the message bar's position and size are:

```python
MSG_X = BAR_WIDTH + 2
MSG_WIDTH = SCREEN_WIDTH - BAR_WIDTH - 2
MSG_HEIGHT = PANEL_HEIGHT - 1
```

This is so it appears to the right of the health bar, and fills up the rest of the space. The messages will be stored in a list so they can be easily manipulated. Each message is a tuple with 2 fields: the message string, and its color.

```python
#create the list of game messages and their colors, starts empty
game_msgs = []
```

A simple-to-use function will handle adding messages to the list. It will use Python's textwrap module to split a message into several lines if it's too long! For that, put import textwrap at the top of the file, and create the function:

```python
def message(new_msg, color = libtcod.white):
    #split the message if necessary, among multiple lines
    new_msg_lines = textwrap.wrap(new_msg, MSG_WIDTH)

    for line in new_msg_lines:
        #if the buffer is full, remove the first line to make room for the new one
        if len(game_msgs) == MSG_HEIGHT:
            del game_msgs[0]

        #add the new line as a tuple, with the text and the color
        game_msgs.append( (line, color) )
```

After obtaining the broken up message as a list of strings, it adds them one at a time to the actual message log. This is so that, when the log gets full, the first line is removed to make space for the new line. Dealing with one line at a time makes it easy to ensure that the message log never has more than a maximum height.

The code to show the message log is simpler. Just loop through the lines and print them with the appropriate colors (right before rendering the health bar). Notice how we get the values of the tuple right in the for loop; this sort of feature in Python (called _unpacking_) allows you to write very concise code.

```python
    #print the game messages, one line at a time
    y = 1
    for (line, color) in game_msgs:
        libtcod.console_set_default_foreground(panel, color)
        libtcod.console_print_ex(panel, MSG_X, y, libtcod.BKGND_NONE, libtcod.LEFT, line)
        y += 1
```

Ready to test! Let's print a friendly message before the main loop to welcome the player to our dungeon of doom:

```python
#a warm welcoming message!
message('Welcome stranger! Prepare to perish in the Tombs of the Ancient Kings.', libtcod.red)
```

The long message allows us to test the word-wrap. You can now replace all the calls to the standard print with calls to our own message function (all 4 of them). I made the player death message red (_libtcod.red_), and the monster death message orange (_libtcod.orange_), others are the default. By the way, [here's the list of standard libtcod colors](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/color.html?c=false&cpp=false&cs=false&py=true&lua=false). It's very handy, if you don't mind using a pre-defined palette of colors!

This is just the kind of polish that our game needed, it's much more attractive, even for casual players. Don't let the die-hard roguelike players fool you, everyone likes a little bit of eye candy!

### Mouse-look

---

We'll now work some interactivity into our GUI. Roguelikes have a long tradition of using strict keyboard interfaces, and that's nice; but for a couple of tasks, like selecting a tile, a mouse interface is much easier. So we'll implement something like a "look" command, by automatically showing the name of any object the player hovers the mouse with! You could also use it for selecting targets of spells and ranged combat. Of course this is only a tutorial, showing you what you can do, and you may decide to replace this with a traditional "look" command!

Using libtcod it's very easy to know the position of the mouse, and if there were any clicks: _the sys_check_for_event_ function returns information on both keyboard and mouse activity: ([see the docs](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/console_non_blocking_input.html?c=false&cpp=false&cs=false&py=true&lua=false) for more details, and [here](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/mouse.html?c=false&cpp=false&cs=false&py=true&lua=false) for the mouse structure).

We need to restructure the program a little bit to use this combined mouse and keyboard detection. Just before the main loop, add

```python
mouse = libtcod.Mouse()
key = libtcod.Key()
```

and just after the main loop begins, but before the call to _render_all_, add

```python
    libtcod.sys_check_for_event(libtcod.EVENT_KEY_PRESS|libtcod.EVENT_MOUSE,key,mouse)
```

Now, declare key as a global in _handle_keys_, and remove the line which calls _libtcod.console_check_for_keypress_.

We'll access the _cx_ and _cy_ fields of the mouse structure, which are the coordinates of the tile (or cell) that the mouse is over. This goes in a new function, that is supposed to return a string with the names of objects under the mouse:

```python
def get_names_under_mouse():
    global mouse

    #return a string with the names of all objects under the mouse
    (x, y) = (mouse.cx, mouse.cy)
```

Now we need to gather a list of names of objects that satisfy a few conditions: they're under the mouse, and inside the player's FOV. (Otherwise he or she would be able to detect enemies through walls!) This can be done with list comprehensions, [using the "if" variant](http://docs.python.org/tutorial/datastructures.html#list-comprehensions).

```python
    #create a list with the names of all objects at the mouse's coordinates and in FOV
    names = [obj.name for obj in objects
        if obj.x == x and obj.y == y and libtcod.map_is_in_fov(fov_map, obj.x, obj.y)]
```

After that mouthful, it's a simple matter of joining the names into a single string, using commas. Python has neat functions for this and capitalizing the first letter:

```python
    names = ', '.join(names)  #join the names, separated by commas
    return names.capitalize()
```

The function _render_all_ can call this to get the string that depends on the mouse's position, after rendering the health bar:

```python
    #display names of objects under the mouse
    libtcod.console_set_default_foreground(panel, libtcod.light_gray)
    libtcod.console_print_ex(panel, 1, 0, libtcod.BKGND_NONE, libtcod.LEFT, get_names_under_mouse())
```

But wait! If you recall, in a turn-based game, the rendering is done only once per turn; the rest of the time, the game is blocked on _console_wait_for_keypress_. During this time (which is _most_ of the time) the code we wrote above would simply not be processed! We switched to real-time rendering by replacing the _console_wait_for_keypress_ call in _handle_keys_ with the _sys_check_for_event_ in the main loop.

Won't our game stop being turn-based then? It's funny, but surprisingly it won't! Before you question logic itself, let me tell you that we did some changes earlier that had the side-effect of enabling this.

When the player doesn't take a turn (doesn't press a movement/attack key), _handle_keys_ returns a special string ( _'didnt-take-turn'_ ). You'll notice that the main loop only allows enemies to take their turns if the value returned from _handle_keys_ is not _'didnt-take-turn'_ ! The main loop goes on, but the monsters don't move. The only real distinction between a real-time game and a turn-based game is that, in a turn-based game, the monsters wait until the player moves to make their move. Makes sense!

If you hadn't before, you now need to call _libtcod.sys_set_fps(LIMIT_FPS)_ before the main loop to limit the game's speed. You'll also probably find the movement keys respond too rapidly for a turn-based game where every key press should count as only one turn (see the earlier discussion about this in [Part 1](complete_roguelike_tutorial_using_python+libtcod_part_1.md#Moving_around)). Just replace the calls to _console_is_key_pressed_ in _handle_keys_ with calls like:

```python
        if key.vk == libtcod.KEY_UP:
```

And similarly for the other movement keys. That's it! You can move the mouse around to quickly know the names of every object in sight.

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_7_code.md).

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_8.md).
