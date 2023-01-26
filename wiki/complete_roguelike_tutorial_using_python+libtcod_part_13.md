# Complete Roguelike Tutorial, using python+libtcod, part 13

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## Adventure gear

---

### Simplifying

---

Now that you can explore a large dungeon, I'm sure you can't help but notice a few things missing. Where are all the swords, armor, enchanted boots and other assorted junk? Sure, we have some cool items, but they can only be used once. We can't really handle weapons and armor in the current system. How do we solve this?

First, we can add a new component to take care of the new functionality. An item with the Equipment component can be equipped or taken off, and while equipped will give the player some bonuses (more power, defense, etc). Sounds good!

Now we must plan ahead how this data will be stored in our game. It's time for a small detour into game architecture!

You see, the way you store your data can have a big impact on how easy it will be to handle and debug. There are two types. A brittle data structure can be easily put in an inconsistent state. A strong data structure cannot; it always makes sense, no matter how you change it. For example, you can keep a list of equipped items. To equip, you move an item to the "equipped" list. There are several inconsistent states: what does it mean for an item to be on both lists? What does it mean to be on no list? (Python will just delete an object if it's not referenced anywhere.) This is brittle.

A stronger data structure is to have a "is_equipped" property on each item, and if it's True the item is equipped. This is much harder to break, because in any case the item is either equipped or unequipped; there are no weird states.

We will use the same idea for bonuses, which you'll see later on. In a nutshell, try to store data in a way that allows a minimum of inconsistent states. Duplicated data or data that requires perfect coordination to make sense is usually a bad sign. This is more of an art than a science, though, and there is no absolute answer. So after the tutorial it will all be up to you!

### Basic equipment

---

Ok, it's time to code that up. We'll have an Equipment component that knows whether it's equipped. It will also have an associated slot, like 'hand' for weapons or 'head' for helmets. The slot will simply be indicated by a string.

```python
class Equipment:
    #an object that can be equipped, yielding bonuses. automatically adds the Item component.
    def __init__(self, slot):
        self.slot = slot
        self.is_equipped = False

    def toggle_equip(self):  #toggle equip/dequip status
        if self.is_equipped:
            self.dequip()
        else:
            self.equip()

    def equip(self):
        #equip object and show a message about it
        self.is_equipped = True
        message('Equipped ' + self.owner.name + ' on ' + self.slot + '.', libtcod.light_green)
    
    def dequip(self):
        #dequip object and show a message about it
        if not self.is_equipped: return
        self.is_equipped = False
        message('Dequipped ' + self.owner.name + ' from ' + self.slot + '.', libtcod.light_yellow)
```

Nothing fancy there! To allow objects to have this component, add _equipment=None_ to the parameters of the Object class's `__init__` function, and the usual component initialization code:

```python
        self.equipment = equipment
        if self.equipment:  #let the Equipment component know who owns it
            self.equipment.owner = self
```

At that point, we can also create an Item component automatically, because a piece of Equipment is always an Item (can be picked up and used).

```python
            #there must be an Item component for the Equipment component to work properly
            self.item = Item()
            self.item.owner = self
```

When the player goes to the inventory screen and tries to use a piece of equipment, it will be equipped or dequipped. So, in the _use_ function of the Item class, add to the beginning:

```python
        #special case: if the object has the Equipment component, the "use" action is to equip/dequip
        if self.owner.equipment:
            self.owner.equipment.toggle_equip()
            return
```

That's the basic functionality! To test it quickly, we can let a sword appear in the dungeon, by adding a new item choice in _place_objects_:

```python
            elif choice == 'sword':
                #create a sword
                equipment_component = Equipment(slot='right hand')
                item = Object(x, y, '/', 'sword', libtcod.sky, equipment=equipment_component)
```

And _item_chances['sword'] = 25_ after the other item's chances, at the top of that function.

Ready to test! Equipping the sword doesn't do much though. You'll also notice you can equip 2 swords at once (how cool is that?). But 3 swords or more is a bit unrealistic, so we'll take care of that.

### Equipment polish

---

We don't want to let the player equip more than one item in the same slot. Fair enough! Let's make a function to check if any item occupies a slot, and return it while we're at it:

```python
def get_equipped_in_slot(slot):  #returns the equipment in a slot, or None if it's empty
    for obj in inventory:
        if obj.equipment and obj.equipment.slot == slot and obj.equipment.is_equipped:
            return obj.equipment
    return None
```

We can use it to prevent a second item in the same slot, or better yet: dequip the old item to make room for the new one. In the _equip_ function:

```python
        #if the slot is already being used, dequip whatever is there first
        old_equipment = get_equipped_in_slot(self.slot)
        if old_equipment is not None:
            old_equipment.dequip()
```

Another nice behavior is to automatically equip picked up items, if their slots are available. In the _pick_up_ function, at the end:

```python
            #special case: automatically equip, if the corresponding equipment slot is unused
            equipment = self.owner.equipment
            if equipment and get_equipped_in_slot(equipment.slot) is None:
                equipment.equip()
```

It is necessary, though, that dropped items be dequipped; simply add to the drop function:

```python
        #special case: if the object has the Equipment component, dequip it before dropping
        if self.owner.equipment:
            self.owner.equipment.dequip()
```

Finally, another bit of polishing. We'd like to see in the inventory which items are equipped! So in _inventory_menu_, this information should be shown next to the item names. Replace the line _options = [item.name for item in inventory]_ with:

```python
        options = []
        for item in inventory:
            text = item.name
            #show additional information, in case it's equipped
            if item.equipment and item.equipment.is_equipped:
                text = text + ' (on ' + item.equipment.slot + ')'
            options.append(text)
```

That's it. You can check the equipment's state in the inventory screen, and it changes correctly as you pick up, drop, equip and dequip various items!

### Bonus round

---

The last bit is to make equipment useful, by letting it change the player's stats when equipped. We can do this in different ways, but as I mentioned in the beginning, it's better to avoid brittle data structures. For example, you could simply add the bonus value to a stat (say, attack power) when the item is equipped, and subtract it when dequipped. This is brittle because any tiny mistake will permanently change the player's stats!

A more reliable approach is to calculate on-the-fly the player's stats when they are needed, based on the original stat and any bonuses. This way there's no room for inconsistencies -- the stat is truly based on whatever bonuses apply at the moment.

But how can we change a stored variable to a dynamic value? Won't this mean we have to change all of the code that uses those stats? Not really, because of a neat Python feature! You can define a read-only property that is calculated on-the-fly very easily:

```python
    @property
    def power(self):
        return self.base_power + bonus
```

The bonus will be defined later. So now accessing _player.power_ will call this function instead of getting the value of a _power_ variable. We still need a variable to hold the player's power not counting any bonuses, though, and that's called _base_power_. This means that, in the Fighter class's `__init__` function, we don't initialize _power_ directly, but rather _base_power_:

```python
        self.base_power = power
```

More generally, you can get the value of power normally, but you only change it through _base_power_. So, you must also make this change in _check_level_up_.

All that's left is to calculate the bonus! An Equipment component will remember what's its power bonus, by passing it as a new argument at initialization. I will also go ahead and define the bonuses for all the other stats:

```python
    def __init__(self, slot, power_bonus=0, defense_bonus=0, max_hp_bonus=0):
        self.power_bonus = power_bonus
        self.defense_bonus = defense_bonus
        self.max_hp_bonus = max_hp_bonus
```

The power property can now just iterate through all equipped items, and sum up their bonuses to get the needed total:

```python
        bonus = sum(equipment.power_bonus for equipment in get_all_equipped(self.owner))
```

Finally, we need a helper function that returns the list of equipped items. For the player, we just go through the inventory. For monsters, we just return an empty list since they don't really have any. Feel free to change this if you want to let monsters equip items as well!

```python
def get_all_equipped(obj):  #returns a list of equipped items
    if obj == player:
        equipped_list = []
        for item in inventory:
            if item.equipment and item.equipment.is_equipped:
                equipped_list.append(item.equipment)
        return equipped_list
    else:
        return []  #other objects have no equipment
```

That's it! Attack power is now a dynamic property. That wasn't so hard! Remember that you can change the bonus calculation easily, so if there are other modifiers, permanent spells and other conditions, it's only a small change away.

For the sake of completeness, here are the properties for the other stats:

```python
    @property
    def defense(self):  #return actual defense, by summing up the bonuses from all equipped items
        bonus = sum(equipment.defense_bonus for equipment in get_all_equipped(self.owner))
        return self.base_defense + bonus

    @property
    def max_hp(self):  #return actual max_hp, by summing up the bonuses from all equipped items
        bonus = sum(equipment.max_hp_bonus for equipment in get_all_equipped(self.owner))
        return self.base_max_hp + bonus
```

Don't forget to make the appropriate changes in _check_level_up_ as well. Now we can define some items with hefty bonuses! In _place_objects_, I changed the sword to have _power_bonus=3_, and added a shield for good measure:

```python
            elif choice == 'shield':
                #create a shield
                equipment_component = Equipment(slot='left hand', defense_bonus=1)
                item = Object(x, y, '[', 'shield', libtcod.darker_orange, equipment=equipment_component)
```

You can get really creative with equipment, of course. I'll just modify the chances to make them appear at level 4 and level 8, respectively:

```python
    item_chances['sword'] =     from_dungeon_level([[5, 4]])
    item_chances['shield'] =    from_dungeon_level([[15, 8]])
```

Now, since we don't want the player to enter the dungeon unprepared, you can give him or her some starting equipment at the end of _new_game_:

```python
    #initial equipment: a dagger
    equipment_component = Equipment(slot='right hand', power_bonus=2)
    obj = Object(0, 0, '-', 'dagger', libtcod.sky, equipment=equipment_component)
    inventory.append(obj)
    equipment_component.equip()
    obj.always_visible = True
```

Not bad! I also decreased the player's starting power to 2; we don't want to be too generous. It's a dungeon of doom after all!

I showed you read-only properties, which are a breeze to use. If you're wondering about writable properties, check out the [Python docs](http://docs.python.org/2/library/functions.html#property) on the subject.

We managed to create a neat bonus system, and it's generic enough that you can add new stats and ways to change them very easily. There's also equipment and slots that you can choose at will. Now you can create all sorts of useful plunder for the player to discover!

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_13_code.md).
