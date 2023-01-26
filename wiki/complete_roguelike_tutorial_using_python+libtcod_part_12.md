# Complete Roguelike Tutorial, using python+libtcod, part 12

---

This is part of a series of tutorials; the main page can be found here.

The tutorial uses libtcod version 1.6.0 and above.

## Monster and item progression

---

### Random choices

---

Now that the player character can become stronger, the challenges that await should become harder too! It sure would be nice if the type and quantity of monsters would vary with the dungeon level, as well as the items. This discovery is a great way to entice the player to go forward, wondering if around the next corner is something he or she has never seen before!

Before going further, we'll take a short stop to look at the algorithm to choose randomly between some options, such as different items and monsters. In _place_objects_, for the items, we have:

```python
dice = libtcod.random_get_int(0, 0, 100)
if dice < 70:
    #create a healing potion (70% chance)
    ...
elif dice < 70+10:
    #create a lightning bolt scroll (10% chance)
    ...
elif dice < 70+10+10:
    #create a fireball scroll (10% chance)
    ...
else:
    #create a confuse scroll (10% chance)
    ...
```

This will be hard to maintain if you have many items; a small mistake can mess up the chances. It would be better to have a generic function that accepts a list of chances and returns a random choice.

Alright, let's look at that code again! There's a clear pattern, and it has an intuitive explanation. Imagine if you draw a line and split it in 10 equal parts. Take the first 7 parts and call them "healing potion"; the next part call it "lightning bolt scroll"; the next "fireball scroll" and the final part "confuse scroll". If you choose a point at random in this line, there's a 70% chance (or 7 in 10) you get a healing potion, and so on, with all the appropriate chances.

So, for each option, we have to sum its chance and the chances of the previous options, because that gives us the point in the line where the corresponding parts end. Then we check if the random number (point) happens to be inside that zone. That's all there is to it.

For our generic function, we can loop through the choices, and keep track of the point where we are on the line by using a running sum (ie., summing the chances of the choices as we go along). We must also keep track of the index of the current choice. When we find the correct choice, we return its index.

```python
def random_choice_index(chances):  #choose one option from list of chances, returning its index
    #the dice will land on some number between 1 and the sum of the chances
    dice = libtcod.random_get_int(0, 1, sum(chances))
    
    #go through all chances, keeping the sum so far
    running_sum = 0
    choice = 0
    for w in chances:
        running_sum += w
        
        #see if the dice landed in the part that corresponds to this choice
        if dice <= running_sum:
            return choice
        choice += 1
```

Notice that you don't really have to specify the chances in percentage; you can as easily have 70%, 4 in 10, or 1 in 3. If the sum of the chances is 100 then they're percentages; otherwise they're in some other unit.

I used the [built-in function sum()](http://docs.python.org/library/functions.html#sum), which takes a list of numbers and returns their sum. It's nicely suited for numeric algorithms like this.

Alright, that was a bit abstract. But now we have a handy function that can be useful in many situations! Figuring out algorithms like this can be the hardest part of programming, but don't let that discourage you. Trying out some concrete situations with pen & paper, such as the line diagram we discussed earlier, can be a great help. Or you can just look for some code on the net if you feel impatient!

Now that it's done, using this function is easy. We can set up lists with the chance of each item and monster:

```python
monster_chances = [80, 20]
item_chances = [70, 10, 10, 10]
```

We can then call random_choice_index and create the item with the chosen index:

```python
            choice = random_choice_index(item_chances)
            if choice == 0:
                #create a healing potion
```

And so on with indexes from 0 to 3. For monsters it's exactly the same but using _monster_chances_ instead of _item_chances_.

This is much cleaner, but we can do even better. At a glance you don't know what chance corresponds to each item or monster. We could replace the lists with dictionaries (dicts), so that we can use strings instead of indexes:

```python
monster_chances = {'orc': 80, 'troll': 20}
item_chances = {'heal': 70, 'lightning': 10, 'fireball': 10, 'confuse': 10}
```

Looks good! Now to create an alternative of _random_choice_index_ that works with strings instead of indexes. You can see a dictionary as just 2 lists: _the keys_, in this case [_'heal', 'lightning', 'fireball', 'confuse'_], and the values, in this case _[70, 10, 10, 10]_. We can break down a dict into the 2 lists using the following functions, which are part of the standard library:

```python
def random_choice(chances_dict):
    #choose one option from dictionary of chances, returning its key
    chances = chances_dict.values()
    strings = chances_dict.keys()
```

We can now pass the chances list to _random_choice_index_, which does all the work, and then convert the index to a string again:

```python
    return strings[random_choice_index(chances)]
```

There it is! This function is much handier than the last. The code for choosing items becomes:

```python
            choice = random_choice(item_chances)
            if choice == 'heal':
                #create a healing potion
```

And likewise for all the other items and monsters. This will be much easier to maintain as you add more items and monsters to your game!

### Monster and item progression

---

The only thing left is varying the contents of the dungeon (number of monsters and items, and their chances) according to the dungeon level. Instead of having fixed values, they could change with some formula, like the one we used to calculate how much xp is needed to level up. You're welcome to do this if you prefer; however in this section we will go down a slightly different path!

I'd like to be able to say that the maximum number of items per room starts as 1 at level 1, and changes to 2 at level 4. Generally, I'd like to have a table of transition points. Each entry in the table says what the value changes to, and at what level. This should be easier to tune, since you can change the value of one level without affecting the values of the others!

We can make a table as a list of pairs _[value, level]_. For the example above we'd have _[[1, 1], [2, 4]]_, meaning a value of 1 at level 1 and a value of 2 at level 4. The code for looking up the table is pretty short:

```python
def from_dungeon_level(table):
    #returns a value that depends on level. the table specifies what value occurs after each level, default is 0.
    for (value, level) in reversed(table):
        if dungeon_level >= level:
            return value
    return 0
```

Notice two things. First, we unpack the current pair into the variables (_value, level_) directly in the _for_, which is a nice shortcut. Second, we use the built-in function reversed to go through the list backwards (the Python standard library comes to the rescue again!). Because if you try looping in the regular order, you'll see it always returns on the first element! You could figure this out by trying a few values and seeing what happens. As I said before, I like pen & paper for these situations. This function assumes the table is sorted by level in ascending order, which is reasonable, but you could also use the sort function to enforce it.

Ok, now we have all the tools we need to get back to our game! I decided on the number of monsters and items, and their chances. This took quite a bit of playtesting, but I'm sure it could be better! At the top of _place_objects_:

```python
    #maximum number of monsters per room
    max_monsters = from_dungeon_level([[2, 1], [3, 4], [5, 6]])

    #chance of each monster
    monster_chances = {}
    monster_chances['orc'] = 80  #orc always shows up, even if all other monsters have 0 chance
    monster_chances['troll'] = from_dungeon_level([[15, 3], [30, 5], [60, 7]])

    #maximum number of items per room
    max_items = from_dungeon_level([[1, 1], [2, 4]])
    
    #chance of each item (by default they have a chance of 0 at level 1, which then goes up)
    item_chances = {}
    item_chances['heal'] = 35  #healing potion always shows up, even if all other items have 0 chance
    item_chances['lightning'] = from_dungeon_level([[25, 4]])
    item_chances['fireball'] =  from_dungeon_level([[25, 6]])
    item_chances['confuse'] =   from_dungeon_level([[10, 2]])
```

You can get rid of the constants _MAX_ROOM_MONSTERS_ and _MAX_ROOM_ITEMS_, and use the new variables _max_monsters_ and _max_items_ instead. As you can see, the chances now vary with the level. I also decided to change some other values to make the game more balanced: _HEAL_AMOUNT = 40_, _LIGHTNING_DAMAGE = 40_, and _FIREBALL_DAMAGE = 25_. For the player, I set _hp=100_, _defense=1_, _power=4_; for the orcs, _hp=20_, _defense=0_, _power=4_; and for the trolls, _hp=30, defense=2, power=8_.

Wow, that was a ton of work! But I hope you agree that it paid off. Try playing it for a bit; you'll see it's actually challenging, and you need to use every strategy at your disposal to survive! (I'm planning for the end to be around level 10 or so.) I think it's fun already, and it only has 2 monsters and 4 items. With this system in place, you should have no trouble adding as many items, monsters and other features as you want!

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_12_code.md).

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_13.md).
