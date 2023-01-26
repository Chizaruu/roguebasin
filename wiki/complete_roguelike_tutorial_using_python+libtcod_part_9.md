# Complete Roguelike Tutorial, using python+libtcod, part 9

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## Spells and ranged combat

---

### Magic scrolls: the Lightning Bolt

---

The inventory we implemented has lots of untapped potential; I encourage you to explore it fully and create as many different items as possible! (Though the special category of items that can be equipped, like swords and armor, will be done in a later section.) To get the ball rolling, we'll start with some magic scrolls the player can use to cast spells. We'll sample a few different spell types; I'm sure you can then create tons of variants from these little examples.

The first spell is a lightning bolt that damages the nearest enemy. It's simple to code because it doesn't involve any targeting. On the other hand, it creates some interesting tactical challenges: if the nearest enemy is not the one you want to hit (for example, it's too weak to waste the spell on it), you have to maneuver into a good position. Just modify the _place_objects_ function to choose at random between a healing potion and a lightning bolt scroll, the same way it's done with monsters:

```python
            dice = libtcod.random_get_int(0, 0, 100)
            if dice < 70:
                #create a healing potion (70% chance)
                item_component = Item(use_function=cast_heal)
                
                item = Object(x, y, '!', 'healing potion', libtcod.violet, item=item_component)
            else:
                #create a lightning bolt scroll (30% chance)
                item_component = Item(use_function=cast_lightning)
                
                item = Object(x, y, '#', 'scroll of lightning bolt', libtcod.light_yellow, item=item_component)
```

When used, the new item calls the _cast_lightning_ function. It's quite simple, and many of the spells will be equally short if you define some functions for common tasks. One of those common tasks is finding the nearest monster up to a maximum range, which we'll get to in a second; but first, the lightning bolt spell:

```python
def cast_lightning():
    #find closest enemy (inside a maximum range) and damage it
    monster = closest_monster(LIGHTNING_RANGE)
    if monster is None:  #no enemy found within maximum range
        message('No enemy is close enough to strike.', libtcod.red)
        return 'cancelled'
    
    #zap it!
    message('A lighting bolt strikes the ' + monster.name + ' with a loud thunder! The damage is '
        + str(LIGHTNING_DAMAGE) + ' hit points.', libtcod.light_blue)
    monster.fighter.take_damage(LIGHTNING_DAMAGE)
```

It's a plain spell but an imaginative message can always give it some flavor! It returns a special string if cancelled to prevent the item from being destroyed in that case, like the healing potion. There are also a couple of new constants that have to be defined: _LIGHTNING_DAMAGE = 20_ and _LIGHTNING_RANGE = 5_.

Now the _closest_monster_ function. We just need to loop through all the monsters, and keep track of the closest one so far (and its distance). By initializing that distance at a bit more than the maximum range, any monster farther away is rejected. We also check that it's in FOV, so the player can't cast a spell through walls.

```python
def closest_monster(max_range):
    #find closest enemy, up to a maximum range, and in the player's FOV
    closest_enemy = None
    closest_dist = max_range + 1  #start with (slightly more than) maximum range
    
    for object in objects:
        if object.fighter and not object == player and libtcod.map_is_in_fov(fov_map, object.x, object.y):
            #calculate distance between this object and the player
            dist = player.distance_to(object)
            if dist < closest_dist:  #it's closer, so remember it
                closest_enemy = object
                closest_dist = dist
    return closest_enemy
```

This makes use of the distance_to method we wrote earlier for the AI. Alright, the lightning bolt is done! If you have one you can take down a Troll with a single hit, sparing you from a lot of damage.

### Spells that manipulate monsters: Confusion

---

There are many direct damage variants of the Lightning Bolt spell. So we'll move on to a different sort of spell: one that affects the monsters' actions. This can be done by replacing their AI with a different one, that makes it do something different -- run away in fear, stay knocked out for a few turns, even fight on the player's side for a while!

My choice was a Confusion spell, that makes the monster move around randomly, and not attack the player. The AI component that does this is very similar to a regular one, since it defines a _take_turn_ method. It could look like this:

```python
class ConfusedMonster:
    #AI for a confused monster.
    def take_turn(self):
        #move in a random direction
        self.owner.move(libtcod.random_get_int(0, -1, 1), libtcod.random_get_int(0, -1, 1))
```

Which simply moves in a random direction (random X and Y displacements between -1 and 1).

It shouldn't go on forever of course, and after the spell runs out the previous AI must restored. So it must accept the previous AI component as an argument to store it. It must also keep track of the number of turns left until the effect ends:

```python
class ConfusedMonster:
    #AI for a temporarily confused monster (reverts to previous AI after a while).
    def __init__(self, old_ai, num_turns=CONFUSE_NUM_TURNS):
        self.old_ai = old_ai
        self.num_turns = num_turns
    
    def take_turn(self):
        if self.num_turns > 0:  #still confused...
            #move in a random direction, and decrease the number of turns confused
            self.owner.move(libtcod.random_get_int(0, -1, 1), libtcod.random_get_int(0, -1, 1))
            self.num_turns -= 1
            
        else:  #restore the previous AI (this one will be deleted because it's not referenced anymore)
            self.owner.ai = self.old_ai
            message('The ' + self.owner.name + ' is no longer confused!', libtcod.red)
```

I defined the new constant _CONFUSE_NUM_TURNS = 10_. Now, the actual scroll that uses this AI! For it to appear in the dungeon it must be added to _place_objects_. Notice that the chance of getting a lightning bolt scroll must change:

```python
            elif dice < 70+15:
                #create a lightning bolt scroll (15% chance)
                item_component = Item(use_function=cast_lightning)
                
                item = Object(x, y, '#', 'scroll of lightning bolt', libtcod.light_yellow, item=item_component)
            else:
                #create a confuse scroll (15% chance)
                item_component = Item(use_function=cast_confuse)
                
                item = Object(x, y, '#', 'scroll of confusion', libtcod.light_yellow, item=item_component)
```

I'm making all scrolls look the same, but in your game that's up to you. The _cast_confuse_ function can now be defined. It hits the closest monster for now, like the lightning bolt; later we'll allow targeting.

```python
def cast_confuse():
    #find closest enemy in-range and confuse it
    monster = closest_monster(CONFUSE_RANGE)
    if monster is None:  #no enemy found within maximum range
        message('No enemy is close enough to confuse.', libtcod.red)
        return 'cancelled'
```

This uses the constant _CONFUSE_RANGE = 8_. Components are "plug & play" so they only take a couple of lines of code to replace! What this does is assign a new "confused AI" component to the monster, giving it the previous AI component as an argument. Then its "owner" property is set to the monster, otherwise the new component wouldn't know what monster it belongs to.

Setting the "owner" has to be done every time you replace a component in the middle of the game. To top it off, print a nice message describing what happened.

```python
    #replace the monster's AI with a "confused" one; after some turns it will restore the old AI
    old_ai = monster.ai
    monster.ai = ConfusedMonster(old_ai)
    monster.ai.owner = monster  #tell the new component who owns it
    message('The eyes of the ' + monster.name + ' look vacant, as he starts to stumble around!', libtcod.light_green)
```

This seems like a neat way to interrupt a monster's actions in reaction to something. It is, but not for every situation! You could create a full finite state machine by just swapping AI components, but such a system would be very hard to debug. Instead, for most types of AI that have different states, you can simply have a "state" property in the AI component, like this:

```python
class DragonAI:
    def __init__(self):
        self.state = 'chasing'
    
    def take_turn(self):
        if self.state == 'chasing': ...
        elif self.state == 'charging-fire-breath': ...
```

Swapping AI components is most useful for unexpected interruptions, such as when a spell is cast. The BasicMonster or DragonAI shouldn't know what to do when confused; that depends on the spell. They do know, however, what to do in normal situations (attacking, etc), and those behaviors usually don't belong in other AI components; if a single AI is composed of too many small interacting parts, they can be harder to maintain. This is just a piece of advice though, and in programming common sense always wins over any hard rules.

### Targeting: the Fireball

---

Given that we know how to make direct damage spells like Lightning Bolt, others like Blizzard or Fireball are just a matter of finding all monsters in an area and damaging them; you should have no trouble creating them. But it would be much more interesting if the player could choose the target properly, and that's a feature that will benefit many spells. In addition, you can use the same system for ranged weapons like crossbows or slings. So let's do that!

We're gonna build a mouse interface. It's also possible to make a classic keyboard interface, but it would be less intuitive and a bit harder to code; if you prefer that, consider it a small challenge!

We already have some code for getting the coordinates of the mouse, and checking for left-clicks is trivial -- when it happens _mouse.lbutton_pressed_ is True. So we just need to loop until the player clicks somewhere. By redrawing the screen with every loop, the names of objects under the mouse are automatically shown, and we erase the inventory from which the player chose the scroll (otherwise it would still be visible).

```python
def target_tile(max_range=None):
    #return the position of a tile left-clicked in player's FOV (optionally in a range), or (None,None) if right-clicked.
    global key, mouse
    while True:
        #render the screen. this erases the inventory and shows the names of objects under the mouse.
        libtcod.console_flush()
        libtcod.sys_check_for_event(libtcod.EVENT_KEY_PRESS|libtcod.EVENT_MOUSE,key,mouse)
        render_all()
        
        (x, y) = (mouse.cx, mouse.cy)
        
        if mouse.lbutton_pressed:
            return (x, y)
```

We have to "flush" the console to present the changes to the player, and _console_check_for_keypress_ absorbs any keys the player presses during the loop, doing nothing with them. Otherwise, if you pressed a key before clicking, it would be stored by libtcod and processed after you clicked, which is unexpected. This does the job, but there are a few things missing! First, there's no way to cancel. We'll define the right-click and the Escape key as ways to cancel, and return a special code:

```python
        if mouse.rbutton_pressed or key.vk == libtcod.KEY_ESCAPE:
            return (None, None)  #cancel if the player right-clicked or pressed Escape
```

If it returned _None_ instead, calling _(x,y) = target_tile()_ would give an error, since we expect 2 output arguments (in fact, it's a tuple that is _unpacked_ to variables _x_ and _y_). So target_tile must return (_None, None_) when canceling.

Ok, we also want to avoid targeting out-of-FOV, to prevent firing through walls. An optional maximum range would also be nice. Replace the _if mouse.lbutton_pressed_: line with:

```python
        #accept the target if the player clicked in FOV, and in case a range is specified, if it's in that range
        if (mouse.lbutton_pressed and libtcod.map_is_in_fov(fov_map, x, y) and
            (max_range is None or player.distance(x, y) <= max_range)):
```

The Object class already has a method that returns the distance between 2 objects, but we'll add another one, that returns the distance between that object and a tile. It's used in the above line of code and will be handy in the future.

```python
    def distance(self, x, y):
        #return the distance to some coordinates
        return math.sqrt((x - self.x) ** 2 + (y - self.y) ** 2)
```

That's all for targeting a tile! We can now create a simple fireball spell:

```python
def cast_fireball():
    #ask the player for a target tile to throw a fireball at
    message('Left-click a target tile for the fireball, or right-click to cancel.', libtcod.light_cyan)
    (x, y) = target_tile()
    if x is None: return 'cancelled'
    message('The fireball explodes, burning everything within ' + str(FIREBALL_RADIUS) + ' tiles!', libtcod.orange)
    
    for obj in objects:  #damage every fighter in range, including the player
        if obj.distance(x, y) <= FIREBALL_RADIUS and obj.fighter:
            message('The ' + obj.name + ' gets burned for ' + str(FIREBALL_DAMAGE) + ' hit points.', libtcod.orange)
            obj.fighter.take_damage(FIREBALL_DAMAGE)
```

With some new constants _FIREBALL_RADIUS = 3_ and _FIREBALL_DAMAGE = 12_. This also uses the new distance method. A scroll that casts the Fireball spell must be added to _place_objects_, before the Confuse scroll:

```python
            elif dice < 70+10+10:
                #create a fireball scroll (10% chance)
                item_component = Item(use_function=cast_fireball)
                
                item = Object(x, y, '#', 'scroll of fireball', libtcod.light_yellow, item=item_component)
```

And change all the "15" to "10"; since there are 3 scrolls now, each one has a 10% chance of appearing. You can now pick up Fireball scrolls; they're quite handy to roast large groups of Orcs! Try not to get burnt though, it also damages the player. I think it adds some strategic value, balancing the spell. (If you want the player to be immune, add "_and obj != player_" to the if condition in _cast_fireball_.)

### Targeting single monsters

---

Let's not stop there! Area spells like the Fireball are fine, but many spells affect single monsters. Can we make a handy function to target a single monster? Sure! It will simply wrap _target_tile_ and stop only when a monster is selected.

```python
def target_monster(max_range=None):
    #returns a clicked monster inside FOV up to a range, or None if right-clicked
    while True:
        (x, y) = target_tile(max_range)
        if x is None:  #player cancelled
            return None
        
        #return the first clicked monster, otherwise continue looping
        for obj in objects:
            if obj.x == x and obj.y == y and obj.fighter and obj != player:
                return obj
```

The Confuse spell is a bit weak, since monsters that move randomly can be hard to hit before the spell runs out. So we'll compensate a bit by letting the player choose any target for it; conveniently testing our new function. Just replace the first 5 lines of the _cast_confuse_ function with:

```python
    #ask the player for a target to confuse
    message('Left-click an enemy to confuse it, or right-click to cancel.', libtcod.light_cyan)
    monster = target_monster(CONFUSE_RANGE)
    if monster is None: return 'cancelled'
```

### Dropping items

---

Right, there's an inventory feature that didn't make it into Part 8, since it was getting too long. You'll miss it when you hit the maximum number of items in your inventory: dropping items. A new method in the Item component will do that. To drop an item you just add it to the map's objects and remove it from the inventory. Then you must set its coordinates to the player's, so it appears below the player:

```python
    def drop(self):
        #add to the map and remove from the player's inventory. also, place it at the player's coordinates
        objects.append(self.owner)
        inventory.remove(self.owner)
        self.owner.x = player.x
        self.owner.y = player.y
        message('You dropped a ' + self.owner.name + '.', libtcod.yellow)
```

To let the player choose an item to drop, we'll call the _inventory_menu_ function when the player presses the D key, then drop the chosen item. Add this to _handle_keys_, after the inventory key:

```python
            if key_char == 'd':
                #show the inventory; if an item is selected, drop it
                chosen_item = inventory_menu('Press the key next to an item to drop it, or any other to cancel.\n')
                if chosen_item is not None:
                    chosen_item.drop()
```

Some new spells, targeting, dropping items -- that's enough for now! See how the spells affect your strategy, they'll surely make things much more interesting!

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_9_code.md).

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_10.md).
