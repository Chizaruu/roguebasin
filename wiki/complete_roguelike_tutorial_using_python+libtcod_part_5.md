# Complete Roguelike Tutorial, using python+libtcod, part 5

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## Preparing for combat

---

### Populating the dungeon

---

Can you feel that? It's the sense of anticipation in the air! That's right, from now on we won't rest until our game lets us smite some pitiful minions of evil, for great justice! It'll be a long journey, and the code will become more complicated, but there's no point in tip-toeing around it any more; it's a game and we want to play it. Some sections will be a bit of a drag, but if you survive that, the next part will be much more fun and rewarding.

First, we must handle monster placement. While this may seem daunting, it's actually pretty simple, thanks to our generic object system! It only requires us to create a new object and append it to the objects list. Therefore, all we need to do is, for each room, create a few monsters in random positions. So we'll create a simple function to populate a room:

```python
def place_objects(room):
    #choose random number of monsters
    num_monsters = libtcod.random_get_int(0, 0, MAX_ROOM_MONSTERS)

    for i in range(num_monsters):
        #choose random spot for this monster
        x = libtcod.random_get_int(0, room.x1, room.x2)
        y = libtcod.random_get_int(0, room.y1, room.y2)

        if libtcod.random_get_int(0, 0, 100) < 80:  #80% chance of getting an orc
            #create an orc
            monster = Object(x, y, 'o', libtcod.desaturated_green)
        else:
            #create a troll
            monster = Object(x, y, 'T', libtcod.darker_green)

        objects.append(monster)
```

The constant _MAX_ROOM_MONSTERS = 3_ will be defined with the other constants so that it can be easily tweaked.

I decided to create orcs and trolls, but you can choose anything else. In fact, you should change this function as much as you want! This is probably the simplest method. If you want to add more monsters, you'll need to keep the random value in a variable and compare it multiple times, using this pattern:

```python
#chances: 20% monster A, 40% monster B, 10% monster C, 30% monster D:
choice = libtcod.random_get_int(0, 0, 100)
if choice < 20:
    #create monster A
elif choice < 20+40:
    #create monster B
elif choice < 20+40+10:
    #create monster C
else:
    #create monster D
```

As an alternative, you could define a number of pre-set squads and choose one of them randomly, each squad being a combination of some monsters (for example, one troll and a few orcs, or 50% orcs and 50% goblin archers). The sky is the limit! You can also place items in the same manner, but we'll get to that later.

Now, for the dungeon generator to place monsters in each room, call this function right before adding the new room to the list, inside _make_map_:

```python
            #add some contents to this room, such as monsters
            place_objects(new_room)
```

There! I also removed the dummy NPC from the initial objects list (before the main loop): it won't be needed any more.

### Blocking objects

---

Here, we'll add a few bits that are necessary before we can move on. First, blocking objects. We don't want more than one monster standing in the same tile, because only one will show up and the rest will be hidden! Some objects, especially items, don't block (it would be silly if you couldn't stand right next to a healing potion!), so each object will have an extra "blocks" property. We'll also take the opportunity to allow each object to have a name, which will be useful for game messages and the Graphical User Interface (GUI), which we'll go over later. Just add these two properties to the beginning of the Object 's ___init___ method:

```python
    def __init__(self, x, y, char, name, color, blocks=False):
        self.name = name
        self.blocks = blocks
```

Now, we'll create a function that tests if a tile is blocked, whether due to a wall or an object blocking it. It's very simple, but it will be useful in a lot of places, and will save you a lot of headaches further down the line.

```python
def is_blocked(x, y):
    #first test the map tile
    if map[x][y].blocked:
        return True

    #now check for any blocking objects
    for object in objects:
        if object.blocks and object.x == x and object.y == y:
            return True

    return False
```

OK, now it's time to give it some use! First of all, in the Object 's _move_ method, change the _if_ condition to:

```python
        if not is_blocked(self.x + dx, self.y + dy):
```

Anyone, including the player, can't move over a blocking object now! Next, in the _place_objects_ function, we'll see if the tile is unblocked before placing a new monster:

```python
        #only place it if the tile is not blocked
        if not is_blocked(x, y):
```

Don't forget to indent the lines after that. This guarantees that monsters don't overlap! And since objects have two more properties, we need to define them whenever we create one, such as the line that creates the player object. Replace the old object initialization code:

```python
#create object representing the player
player = Object(SCREEN_WIDTH/2, SCREEN_HEIGHT/2, '@', libtcod.white)

#create an NPC
npc = Object(SCREEN_WIDTH/2 - 5, SCREEN_HEIGHT/2, '@', libtcod.yellow)

#the list of objects with those two
objects = [npc, player]
```

With this:

```python
#create object representing the player
player = Object(0, 0, '@', 'player', libtcod.white, blocks=True)

#the list of objects starting with the player
objects = [player]
```

And update the code that creates the monsters:

```python
            if libtcod.random_get_int(0, 0, 100) < 80:  #80% chance of getting an orc
                #create an orc
                monster = Object(x, y, 'o', 'orc', libtcod.desaturated_green, blocks=True)
            else:
                #create a troll
                monster = Object(x, y, 'T', 'troll', libtcod.darker_green, blocks=True)
```

If you added the optional room "numbers" in part 3, you'll need to update the code to include a name.

```python
            room_no = Object(new_x, new_y, chr(65+num_rooms), 'room number', libtcod.white)
```

### Game states

---

Last stop before we get to the actual combat system! Our input system has a fatal flaw: player actions (movement, combat) and other keys (fullscreen, other options) are handled the same way. We need to separate them. This way, if the player pauses or dies he can't move or fight, but can press other keys. We also want to know if the player's input means he finished his turn or not; changing to fullscreen shouldn't count as a turn. I know they're just simple details - but the game would be incredibly annoying without them! We only need two global variables, the game state and the player's last action (set before the main loop).

```python
game_state = 'playing'
player_action = None
```

Inside _handle_keys_, the movement/combat keys can only be used if the game state is "playing":

```python
    if game_state == 'playing':
        #movement keys
```

We'll also change the same function so it returns a string with the type of player action. Instead of returning _True_ to exit the game, return a special string:

```python
        return 'exit'  #exit game
```

And testing for all the movement keys, if the player didn't press any, then he didn't take a turn, so return a special string in that case:

```python
    else:
        return 'didnt-take-turn'
```

After the call to _handle_keys_ in the main loop, we can check for the special string 'exit' to exit the game. Later we'll do other stuff according to the _player_action_ string.

```python
        player_action = handle_keys()
        if player_action == 'exit':
            break
```

### Fighting orderly

---

For the first part of the combat system we have to manage the player and monsters taking combat turns, as well as the player making an attack. To make it simple, the player takes his turn first, in _handle_keys_. If he took a turn, all the monsters take theirs. This goes after the _handle_keys_ block in the main loop:

```python
    #let monsters take their turn
    if game_state == 'playing' and player_action != 'didnt-take-turn':
        for object in objects:
            if object != player:
                print 'The ' + object.name + ' growls!'
```

That's just a debug message, in the next part we'll call an AI routine to move and attack. Now we'll take care of the player input. Since he can attack now, instead of calling move (inside _handle_keys_) like this:

```python
            player.move(0, -1)
            fov_recompute = True
```

...we'll make a function called _player_move_or_attack_ and replace all those 4 blocks with calls like this:

```python
            player_move_or_attack(0, -1)
```

The function itself has a few lines of code but doesn't do anything extraordinary. It has to check if there's an object in the direction the player wants to move. If so, a debug message will be printed (this will later be replaced by an actual attack) If not, the player will just move there.

```python
def player_move_or_attack(dx, dy):
    global fov_recompute

    #the coordinates the player is moving to/attacking
    x = player.x + dx
    y = player.y + dy

    #try to find an attackable object there
    target = None
    for object in objects:
        if object.x == x and object.y == y:
            target = object
            break

    #attack if target found, move otherwise
    if target is not None:
        print 'The ' + target.name + ' laughs at your puny efforts to attack him!'
    else:
        player.move(dx, dy)
        fov_recompute = True
```

Alright, the code is ready to test! No damage is done yet but you can see the monsters taking their turns after you (notice when you switch to fullscreen it doesn't count as a turn, yay!), and you can bump into them to heroically but unsuccessfully try to destroy them!

The whole code so far is available [here](complete_roguelike_tutorial_using_python+libtcod_part_5_code.md).

Guess what's next?

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_6.md).
