# Complete Roguelike Tutorial, using python+libtcod, part 11

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## Dungeon levels and character progression

---

### Second floor please

---

We're approaching the point where we have a complete game really fast. A big step will be taken in the next two parts, where we will focus on progression! They will deal with changing dungeon levels, advancing the character and acquiring skills, and finally varying the monsters and items as the player progresses. At the end you will have a game that can take the player quite some time to finish, and to master its strategies too.

We'll cover a lot of ground, but don't worry, this part is all familiar territory! We will reuse many functions from before.

A staple of roguelikes is the _stairs_, which the player must find to advance to the next dungeon level. We will start by placing them, when generating a level. Right at the end of _make_map_:

```python
    #create stairs at the center of the last room
    stairs = Object(new_x, new_y, '<', 'stairs', libtcod.white)
    objects.append(stairs)
    stairs.send_to_back()  #so it's drawn below the monsters
```

As you can see, it's just a regular object! To identify it in other functions, we'll make it global, so add stairs to the globals list at the top of _make_map_.

We must now let the player go down the stairs when standing on them and presses the '<' key (or '>' key if you prefer). It's easy to add this check at the end of _handle_keys_:

```python
            if key_char == '<':
                #go down stairs, if the player is on them
                if stairs.x == player.x and stairs.y == player.y:
                    next_level()
```

The _next_level_ function is the most important bit. What happens when the player goes down the stairs? Well, for now all we have to do is generate a brand new level, with _make_map()_ and _initialize_fov()_. I will also heal the player because I'm such a nice guy!

```python
def next_level():
    #advance to the next level
    message('You take a moment to rest, and recover your strength.', libtcod.light_violet)
    player.fighter.heal(player.fighter.max_hp / 2)  #heal the player by 50%
    
    message('After a rare moment of peace, you descend deeper into the heart of the dungeon...', libtcod.red)
    make_map()  #create a fresh new level!
    initialize_fov()
```

That's it! You can now advance indefinitely and fight as many monsters as you want.

However, we probably want to keep track of the dungeon level we're on. So create a variable for that, by initializing it in the function _new_game_, before _make_map()_:

```python
    dungeon_level = 1
```

Don't forget to add _dungeon_level_ to the list of globals at the top of the function! Otherwise you'll just be setting a local variable, that won't be visible in your other functions. The dungeon level can then be increased in _next_level_, before _make_map()_:

```python
    dungeon_level += 1
```

And declare it as global there too. To display it in the GUI, just print some informative text in _render_all_, after the call to _render_bar_:

```python
    libtcod.console_print_ex(panel, 1, 3, libtcod.BKGND_NONE, libtcod.LEFT, 'Dungeon level ' + str(dungeon_level))
```

It's done! An important detail, however, is that we want this information (what object is the stairs, and what's the dungeon level) to be saved and loaded properly. We just have to follow the usual pattern, by adding to _save_game_:

```python
    file['stairs_index'] = objects.index(stairs)
    file['dungeon_level'] = dungeon_level
```

And to _load_game_:

```python
    stairs = objects[file['stairs_index']]
    dungeon_level = file['dungeon_level']
```

Again, don't forget to declare them as global in _load_game_.

Now, since I like exploring a level thoroughly before going to the next, I found that quite often I can't remember where the stairs were! So let's add a bit of polish and allow some objects to be always visible, as long as they are in a tile that was explored once. This is most useful for stairs, but I think it makes sense for items as well. Just add a new optional property to objects, by modifying the Object's initialization:

```python
    def __init__(self, x, y, char, name, color, blocks=False, always_visible=False, fighter=None, ai=None, item=None):
        self.always_visible = always_visible
```

The behavior we talked about can be created by changing the _if_ in the Object's _draw_ method:

```python
        #only show if it's visible to the player; or it's set to "always visible" and on an explored tile
        if (libtcod.map_is_in_fov(fov_map, self.x, self.y) or
            (self.always_visible and map[self.x][self.y].explored)):
```

You can now set _always_visible=True_ when creating the stairs. I also set _item.always_visible = True_ in _place_objects_, for all items to do the same. If you added the optional room-numbering labels in part 3, set them to always be visible too. They haven't been much use since the fog-of-war code was added, but this makes them useful again.

### Character progression

---

What's next? Let's see... Now that the player can fight so many monsters, it makes sense to gain some skill during the heroic quest! The simplest way to do this is to keep track of experience and player level. We'll store the amount of experience each monster gives in the Fighter component, by adding a new parameter _xp_ to it:

```python
    def __init__(self, hp, defense, power, xp, death_function=None):
        self.xp = xp
```

When creating an orc's Fighter component in _place_objects_, I set their xp to 35, and for trolls _xp=100_ since they're harder to kill. I'm sure these numbers could use some tweaking!

When the player kills a monster, at the end of Fighter's take_damage method, it yields experience:

```python
                if self.owner != player:  #yield experience to the player
                    player.fighter.xp += self.xp
```

As you can see I'm storing it in the player's Fighter component as well, but I guess you could store it anywhere else since the player's xp is special.

But what does the player do with that experience? Let's level up! First, change the initialization of the player object to also set the experience and character level, in _new_game_:

```python
    #create object representing the player
    fighter_component = Fighter(hp=30, defense=2, power=5, xp=0, death_function=player_death)
    player = Object(0, 0, '@', 'player', libtcod.white, blocks=True, fighter=fighter_component)
    
    player.level = 1
```

Now, we need to regularly check if the player has leveled up. I want it to become more difficult every time, so it takes 350 xp points to level up at the first level, and this increases by 150 points with every new level. The formula for this is _200 + player.level * 150_, but I'll declare some constants so they're easier to adjust later:

```python
#experience and level-ups
LEVEL_UP_BASE = 200
LEVEL_UP_FACTOR = 150
```

And the function that handles this will simply check the formula:

```python
def check_level_up():
    #see if the player's experience is enough to level-up
    level_up_xp = LEVEL_UP_BASE + player.level * LEVEL_UP_FACTOR
    if player.fighter.xp >= level_up_xp:
        #it is! level up
        player.level += 1
        player.fighter.xp -= level_up_xp
        message('Your battle skills grow stronger! You reached level ' + str(player.level) + '!', libtcod.yellow)
```

Looks good so far! But when leveling up, the player should become stronger too. My idea is to allow the player between 3 choices: to increase agility (defense), strength (power) or constitution (hp). However, this is one area where you can really get creative -- you can let the player acquire new abilities, increase skills, stats, and even learn to cast new spells!

Using the _menu_ function, this is straightforward; just ask the player at the end of _check_level_up_:

```python
        choice = None
        while choice == None:  #keep asking until a choice is made
            choice = menu('Level up! Choose a stat to raise:\n',
                ['Constitution (+20 HP, from ' + str(player.fighter.max_hp) + ')',
                'Strength (+1 attack, from ' + str(player.fighter.power) + ')',
                'Agility (+1 defense, from ' + str(player.fighter.defense) + ')'], LEVEL_SCREEN_WIDTH)
        
        if choice == 0:
            player.fighter.max_hp += 20
            player.fighter.hp += 20
        elif choice == 1:
            player.fighter.power += 1
        elif choice == 2:
            player.fighter.defense += 1
```

I set the constant _LEVEL_SCREEN_WIDTH = 40_ at the top of the file. You can now call _check_level_up()_ in the main loop after _libtcod.console_flush()_, so the check happens before every turn. This way the menu renders properly (remember we erase all objects before processing a turn, so otherwise they wouldn't show up behind the level up menu).

This seems great, but how do you know it's working? You need a way to check the character info! The character screen can be just a message box that pops up when you press the 'C' key. It's a little messy since it's just pasting together all the info in a string. In _handle_keys_:

```python
            if key_char == 'c':
                #show character information
                level_up_xp = LEVEL_UP_BASE + player.level * LEVEL_UP_FACTOR
                msgbox('Character Information\n\nLevel: ' + str(player.level) + '\nExperience: ' + str(player.fighter.xp) +
                    '\nExperience to level up: ' + str(level_up_xp) + '\n\nMaximum HP: ' + str(player.fighter.max_hp) +
                    '\nAttack: ' + str(player.fighter.power) + '\nDefense: ' + str(player.fighter.defense), CHARACTER_SCREEN_WIDTH)
```

And set the constant _CHARACTER_SCREEN_WIDTH = 30_. It would also be polite to tell the player how much experience he or she gained when slaying a monster. So I modified the log message in _monster_death_:

```python
    message('The ' + monster.name + ' is dead! You gain ' + str(monster.fighter.xp) + ' experience points.', libtcod.orange)
```

That's it. You can now become ridiculously overpowered in no time, and see how you're doing by pressing 'C'. But don't worry, we'll take care of that shortly -- the monsters will grow stronger too!

By the way, if you're missing diagonal movement with the numpad keys, now's a good chance to do it. The [key codes](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/console_keycode_t.html?c=false&cpp=false&cs=false&py=true&lua=false) are KEY_KP0 to KEY_KP9. Have the player rest when the keypad '5' is pressed. This allows you to wait for a monster to come to you, rather than charging into a crowded room. Things are getting pretty serious!

```python
        #movement keys
        if key.vk == libtcod.KEY_UP or key.vk == libtcod.KEY_KP8:
            player_move_or_attack(0, -1)
        elif key.vk == libtcod.KEY_DOWN or key.vk == libtcod.KEY_KP2:
            player_move_or_attack(0, 1)
        elif key.vk == libtcod.KEY_LEFT or key.vk == libtcod.KEY_KP4:
            player_move_or_attack(-1, 0)
        elif key.vk == libtcod.KEY_RIGHT or key.vk == libtcod.KEY_KP6:
            player_move_or_attack(1, 0)
        elif key.vk == libtcod.KEY_HOME or key.vk == libtcod.KEY_KP7:
            player_move_or_attack(-1, -1)
        elif key.vk == libtcod.KEY_PAGEUP or key.vk == libtcod.KEY_KP9:
            player_move_or_attack(1, -1)
        elif key.vk == libtcod.KEY_END or key.vk == libtcod.KEY_KP1:
            player_move_or_attack(-1, 1)
        elif key.vk == libtcod.KEY_PAGEDOWN or key.vk == libtcod.KEY_KP3:
            player_move_or_attack(1, 1)
        elif key.vk == libtcod.KEY_KP5:
            pass  #do nothing ie wait for the monster to come to you
```

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_11_code.md).

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_12.md)
