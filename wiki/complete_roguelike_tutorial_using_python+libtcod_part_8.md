# Complete Roguelike Tutorial, using python+libtcod, part 8

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## Items and inventory

---

### Placing items

---

Now that our GUI is all spiffed up, let's put in some more core Roguelike functionality: the inventory! This has been a staple of Roguelikes and RPGs for literally decades. It's a way of gating the player's access to some abilities, and presents an incentive for exploration. Also, why else would you explore a dungeon if not to haul out as many precious items as you can?

We can place some items in each room in pretty much the same way we place monsters, at the end of the _place_objects_ function:

```python
    #choose random number of items
    num_items = libtcod.random_get_int(0, 0, MAX_ROOM_ITEMS)

    for i in range(num_items):
        #choose random spot for this item
        x = libtcod.random_get_int(0, room.x1+1, room.x2-1)
        y = libtcod.random_get_int(0, room.y1+1, room.y2-1)

        #only place it if the tile is not blocked
        if not is_blocked(x, y):
            #create a healing potion
            item = Object(x, y, '!', 'healing potion', libtcod.violet)

            objects.append(item)
            item.send_to_back()  #items appear below other objects
```

For this to work, we must define the new constant _MAX_ROOM_ITEMS = 2_ at the top. Later we'll expand this with a few magic scrolls in addition to the healing potions; this is the spot to add any items you want in your game. The healing potions don't have any special components for now; we'll get to that in a second.

The limits of the random position of the items (passed to _random_get_int_) are a bit different than for the monsters. In fact, the monsters' coordinates have been slightly off the whole time! They should be changed from this:

```python
        #choose random spot for this monster
        x = libtcod.random_get_int(0, room.x1, room.x2)
        y = libtcod.random_get_int(0, room.y1, room.y2)
```

To this:

```python
        #choose random spot for this monster
        x = libtcod.random_get_int(0, room.x1+1, room.x2-1)
        y = libtcod.random_get_int(0, room.y1+1, room.y2-1)
```

Which is one tile tighter than the room's walls in every direction. That's because the room's rectangle, as we defined it earlier, includes its walls too (_oops!_). Anyway, when the old code decided to place a monster on the walls, it wouldn't get created due to the _is_blocked check_, so there were less monsters on average -- now the game got a little harder! The healing potions should balance this effect, but of course you can always tweak the values to your liking.

After that embarrassing revelation, let's define the inventory! This goes before the main loop:

```python
inventory = []
```

Simple enough: the inventory is a list of items, and it starts empty. Now the Item component -- it will hold all data and functionality that makes an object behave like an item. For it to make its way into the player's inventory, we'll start by giving it a _pick_up_ method.

```python
class Item:
    #an item that can be picked up and used.
    def pick_up(self):
        #add to the player's inventory and remove from the map
        if len(inventory) >= 26:
            message('Your inventory is full, cannot pick up ' + self.owner.name + '.', libtcod.red)
        else:
            inventory.append(self.owner)
            objects.remove(self.owner)
            message('You picked up a ' + self.owner.name + '!', libtcod.green)
```

The limit of 26 is because later, in the inventory screen, items will be selected by pressing a key from A to Z, and there are 26 letters. You could overcome this restriction by implementing "pages" in the inventory, or a fancier interface with scrollbars. That would be a bit harder, so we'll stick to this for now. You could also assign weights to the items and limit the total weight here, as some games do.

This component must be accepted by the _Object's `__init__` method, like all other components. Just add another parameter to it `_item=None_`, and an initialization similar to the other components:

```python
        self.item = item
        if self.item:  #let the Item component know who owns it
            self.item.owner = self
```

Now that we have an Item component, you can add it to the healing potion in _place_objects_:

```python
            item_component = Item()
            item = Object(x, y, '!', 'healing potion', libtcod.violet, item=item_component)
```

How does the player pick up an item? It's very easy: just test for another key in the _handle_keys_ function. If it's pressed, look for an item under the player and pick it up. The new code goes between the else and the return _'didnt-take-turn'_ line:

```python
        else:
            #test for other keys
            key_char = chr(key.c)

            if key_char == 'g':
                #pick up an item
                for object in objects:  #look for an item in the player's tile
                    if object.x == player.x and object.y == player.y and object.item:
                        object.item.pick_up()
                        break

            return 'didnt-take-turn'
```

You can test it out now! There will be a few potions scattered around, and you'll get a message when you pick them up by pressing G. The inventory is still invisible though.

### The inventory screen

---

We now get to what's probably the trickiest part: showing the inventory screen. Since the functionality is tightly bound to the user interface, it's hard to do it without super-messy code.

To minimize the amount of hacks, we'll define a single function to present a list of options to the player, and reuse the hell out of it! We'll start by defining its parameters so we can decide exactly what it's supposed to do:

```python
def menu(header, options, width):
```

This function should show a window with a string at the top, the header, which can be the title of the window and/or an explanatory text (say, "Choose an item to use" or "Choose an item to drop"). Following are the _options_, which are nothing more than a list of strings (for instance, the names of the items). We also need to define the window's width; the height is implicit, since it depends on the header height and number of options.

A letter will be shown next to each option (A, B, ...) so you can select it by pressing that key. Finally, the function returns the index of the selected option (starting with 0), or _None_ if the user pressed some other key. We'll start by just displaying the menu and worry about choosing an option later.

First, check if there are more options than allowed. Since the menu function is supposed to be reused, it's possible that in the future you'll get too carried away and try to give it more options than the letters from A to Z! It's better to get an early error and fix it than let it slide and get harder-to-track errors down the line.

```python
    if len(options) > 26: raise ValueError('Cannot have a menu with more than 26 options.')
```

Now we calculate the height of the window; as I said, it's implicit. The header will be shown using the _console_print_left_rect_ function, which can word-wrap long sentences so it fits a given width. The number of lines after word-wrapping can be calculated with _console_get_height_rect;_ so the total height is that plus the number of options.

```python
    #calculate total height for the header (after auto-wrap) and one line per option
    header_height = libtcod.console_get_height_rect(con, 0, 0, width, SCREEN_HEIGHT, header)
    height = len(options) + header_height
```

Given the window's size, we can create an off-screen console where the window's contents will be drawn first. The header is printed at the top, using the auto-wrap function.

```python
    #create an off-screen console that represents the menu's window
    window = libtcod.console_new(width, height)

    #print the header, with auto-wrap
    libtcod.console_set_default_foreground(window, libtcod.white)
    libtcod.console_print_rect_ex(window, 0, 0, width, height, libtcod.BKGND_NONE, libtcod.LEFT, header)
```

Now to the actual options, printed in a loop. The Y coordinate of the first option is right below the header; we print the option's text and increment it. We also want to start with the letter A and increment it each time, to show it next to the option's text. The ord function returns the ASCII code of the letter A; we can then increment it to get the codes of the remaining letters. To convert an ASCII code back to a character (string), we use the _chr_ function. These are built-in functions; you can read up on them (and others) in [the Python docs](http://docs.python.org/library/functions.html).

```python
    #print all the options
    y = header_height
    letter_index = ord('a')
    for option_text in options:
        text = '(' + chr(letter_index) + ') ' + option_text
        libtcod.console_print_ex(window, 0, y, libtcod.BKGND_NONE, libtcod.LEFT, text)
        y += 1
        letter_index += 1
```

Ok, all of the window's contents are stored in the off-screen console! It's a simple matter of calling _console_blit_ to display them on the screen. These little formulae calculate what the position of the top-left corner of the window should be, so that it's centered on the screen.

```python
    #blit the contents of "window" to the root console
    x = SCREEN_WIDTH/2 - width/2
    y = SCREEN_HEIGHT/2 - height/2
    libtcod.console_blit(window, 0, 0, width, height, 0, x, y, 1.0, 0.7)
```

The last 2 parameters to _console_blit_ hadn't been used in our game before: according to the libtcod docs, they define the foreground and background transparency, respectively. The first is 1.0 so the foreground (the text) is printed fully opaque, as usual. But since the second one is a smaller value, what happens is that the off-screen console's background (which is black by default) does not entirely replace the background colors that were previously on the screen. So what you see is a semi-transparent window, overlaying the game! As you can see, these neat effects are very easy to do with libtcod.

It's not complete though; this screen will be shown for a single frame and then vanish immediately, replaced by the new frame. We need to stop time until the player makes a choice, and only then can the game carry on. This is easy to do with _console_wait_for_keypress_. There's also the need to _flush_ the screen to present the changes before waiting for input:

```python
    #present the root console to the player and wait for a key-press
    libtcod.console_flush()
    key = libtcod.console_wait_for_keypress(True)
```

That was one really long function! But if you base most of your interfaces on this function, you won't need to create any more like it. As an example, here's how you show an inventory -- just build a list of the items' names, and call the _menu_ function:

```python
def inventory_menu(header):
    #show a menu with each item of the inventory as an option
    if len(inventory) == 0:
        options = ['Inventory is empty.']
    else:
        options = [item.name for item in inventory]

    index = menu(header, options, INVENTORY_WIDTH)
```

It also tells the player if the inventory is empty; simply displaying an empty list would be rude! The constant _INVENTORY_WIDTH = 50_ is defined at the top, as usual. The _header_ text is a parameter because we want to call this both for using and dropping items (and maybe other actions). Speaking of which, we can define the inventory key right now, in _handle_keys_ (after the code to pick up items). The line break \n after the header gives one line of separation between it and the options.

```python
            if key_char == 'i':
                #show the inventory
                inventory_menu('Press the key next to an item to use it, or any other to cancel.\n')
```

Finally, the inventory is visible! You can list the items you pick up by pressing I. Selecting them does nothing though; that is handled in the next section.

### Using items

---

What happens when you use an item? Well, it depends on which item you're talking about. They're all different, so the "use" behavior of each item must be defined as a different function. For the Item component to know which one is it, you need to pass it at initialization, just like the Fighter with its _death_function_.

```python
    def __init__(self, use_function=None):
        self.use_function = use_function
```

Then, a generic method can call the Item's _use_function_:

```python
    def use(self):
        #just call the "use_function" if it is defined
        if self.use_function is None:
            message('The ' + self.owner.name + ' cannot be used.')
        else:
            if self.use_function() != 'cancelled':
                inventory.remove(self.owner)  #destroy after use, unless it was cancelled for some reason
```

If it's undefined, the item can't be used. Otherwise the function is called, and the item is destroyed (since most items are single-use). We'll also allow the function to return a special string in case it found that it can't be used after all; for instance, the player is already at full health, so the potion shouldn't be destroyed.

Now let's make the function for the healing potion! It's quite straightforward, as it simply calls a _heal_ method of the Fighter component (which manages health).

```python
def cast_heal():
    #heal the player
    if player.fighter.hp == player.fighter.max_hp:
        message('You are already at full health.', libtcod.red)
        return 'cancelled'

    message('Your wounds start to feel better!', libtcod.light_violet)
    player.fighter.heal(HEAL_AMOUNT)
```

The heal method is very simple too; still, it's handy to keep it since it will probably be used multiple times. The constant _HEAL_AMOUNT = 4_ is defined at the top.

```python
    def heal(self, amount):
        #heal by the given amount, without going over the maximum
        self.hp += amount
        if self.hp > self.max_hp:
            self.hp = self.max_hp
```

To make the healing potion object call it on use, pass it as a parameter to the Item component, in _place_objects_.

```python
            item_component = Item(use_function=cast_heal)
```

That's it for creating usable items! You can make other items easily by just defining their _use_function_. This could also work for wielding weapons or wearing armor, zapping wands, rubbing a magic lamp and all that stuff we know and love.

We now need to get back to the _menu_ function, to finish it so it can actually select an item. It's only a few lines of code though; we already have the key from _console_wait_for_keypress_, and it's just a matter of converting it to the selected option's index. We do that by subtracting the ASCII code of the letter A, so we get key codes from 0 to 25 corresponding to letters A to Z. Anything out of that range means it's not a valid key. Actually, any index over the number of options is invalid too, since the number of options is usually smaller than 26.

```python
    #convert the ASCII code to an index; if it corresponds to an option, return it
    index = key.c - ord('a')
    if index >= 0 and index < len(options): return index
    return None
```

This returns a valid index, or _None_ if something else was pressed. The _inventory_menu_ function can make use of that index, and return the corresponding item, so this goes at the end:

```python
    #if an item was chosen, return it
    if index is None or len(inventory) == 0: return None
    return inventory[index].item
```

The _inventory_menu_ function is very handy; it returns the selected item directly, or _None_ if cancelled. We can now change the code in _handle_keys_ to use the selected item:

```python
            if key_char == 'i':
                #show the inventory; if an item is selected, use it
                chosen_item = inventory_menu('Press the key next to an item to use it, or any other to cancel.\n')
                if chosen_item is not None:
                    chosen_item.use()
```

There you go, the inventory code is complete! Well, minus dropping items. That's fairly easy with the _inventory_menu_, but to keep this from getting long we'll leave it to the next part: magic scrolls! That will really make the most of this inventory system.

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_8_code.md).

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_9.md).
