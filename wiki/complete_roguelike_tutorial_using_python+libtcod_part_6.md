# Complete Roguelike Tutorial, using python+libtcod, part 6

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

## Going Berserk

---

### The components

---

Finally, it's time to bash some orc helmets into tiny metal pancakes! Combat is a big turning point in the development of any game: it separates the game from the tech demo. Although dungeon exploration is interesting, the simplest fight can be far more fun, and you may even find yourself playing your game more than you code it!

We have a big design decision ahead of us. Until now, the Object class was enough to hold the properties of both the player and the enemies. But as we develop further, many properties will make sense for one kind of Object, but not for another. How do we solve this?

Well, we could just pretend that this is not a problem, and use the **data-driven** approach, which is used by many of the older roguelikes. In this approach, all objects have the same set of properties, and only the values of these properties are modified from object to object. There isn't any feature variation between different objects. For example, there might be a property that determines whether a monster has a petrification attack, another for stealing items or gold, other properties for magical capabilities...

As can be seen, it becomes quite tedious to implement a new property for all items every time you want to add a new feature to only one. The only way around this is to limit the number of properties, which in turn limits the number of special features that can be added. This is, of course, not good for us.

The other popular alternative is inheritance. You define a hierarchy of parent (a.k.a. base) and child (a.k.a. derived) classes. Child/derived classes (like Item or Monster), in addition to their own properties, receive the properties from their parent classes (such as Object). This reduces redundancy, and there's a seemingly clean separation between different classes.

However, the separation is not exactly clean, since the properties of parent classes are 'pasted' on the same space as the child's properties; their properties can conflict if they share names. And there's also the temptation to define deep hierarchies of classes. As you develop further, your hierarchy will grow to extreme lengths (such as Object > Item > Equipment > Weapon > Melee weapon > Blunt weapon > Mace > Legendary Mace of Deep Hierarchies). Each level can add just a tiny bit of functionality over the last one.

The fact that a Mace can't be both a Weapon and a Magic Item due to the rigid hierarchy is a bummer. Shuffling classes and code around to achieve these simple tasks is common with inheritance. We want to be able to mix and match freely! Hence, we have inheritance's older, but often forgotten, cousin: **composition**. It has none of the disadvantages listed above.

It's dead simple: there's the Object class, and some component classes. A component class defines extra properties and methods for an Object that needs them. Then you just slap an instance of the component class as a _property_ of the Object; it now "owns" the component. It doesn't even require special functions or code! Let's see how it works.

Our first component will be the Fighter. Any object that can fight or be attacked must have it. It holds hit points, maximum hit points (for healing), defense and attack power.

```python
class Fighter:
    #combat-related properties and methods (monster, player, NPC).
    def __init__(self, hp, defense, power):
        self.max_hp = hp
        self.hp = hp
        self.defense = defense
        self.power = power
```

It'll later be augmented with methods to attack and take damage.

Then there's the BasicMonster component, which holds basic AI routines. You can create other AI components (say, for ranged combat) and use them for some monsters. We'll define a _take_turn method_; as long as a component defines this method, it's a valid alternative to BasicMonster. For now it just prints a debug message:

```python
class BasicMonster:
    #AI for a basic monster.
    def take_turn(self):
        print 'The ' + self.owner.name + ' growls!'
```

Ignore the reference to _self.owner_ -- it's just the Object instance that owns this component, and is initialized elsewhere. We'll get to that in a moment. So how do we associate components with an Object? It's simple: create a Fighter instance, and/or a BasicMonster instance, and pass them as parameters when initializing the Object:

```python
    def __init__(self, x, y, char, name, color, blocks=False, fighter=None, ai=None):
```

Notice that all components are optional; they can be _None_ if you don't want them. Then they're stored as properties of the object, for example with _self.fighter = fighter_. Also, since a component will often want to deal with its owner Object, it has to "know" who it is (for example, to get its position, or its name -- as you noticed earlier, _BasicMonster's take_turn_ method needs to know the object's name to display a proper message). So, in addition to holding the component, the Object will set the component's owner property to itself. The if lines just make sure this happens only if the component is actually defined.

```python
        self.fighter = fighter
        if self.fighter:  #let the fighter component know who owns it
            self.fighter.owner = self

        self.ai = ai
        if self.ai:  #let the AI component know who owns it
            self.ai.owner = self
```

This may look a bit weird, but now we can follow these properties around to go from a component (_self_), to its owner object (_self.owner_), to a different one of its components (_self.owner.ai_), allowing us to do all sorts of funky stuff! Most other systems don't have this kind of flexibility for free. This is actually the most complicated code that composition needs; the rest will be pure game logic!

OK, now it's time to decide on some stats for the monsters and the player! First up, the player. Just create a Fighter component with the stats you choose, and set it as the _fighter_ parameter when creating the player object. Place it above the main loop:

```python
#create object representing the player
fighter_component = Fighter(hp=30, defense=2, power=5)
player = Object(0, 0, '@', 'player', libtcod.white, blocks=True, fighter=fighter_component)
```

Here, I decided to use [keyword arguments](http://docs.python.org/tutorial/controlflow.html#keyword-arguments) to make it clear what the different stats are (_Fighter(30, 2, 5)_ is hard to interpret). They're not necessary for the first few arguments of _Object_ since you can easily deduce what they mean (name, color, etc). This is common sense for most people, but I'll say it anyway: always try to strike a good balance between short and readable code; in places where you can't, pepper it with lots explanatory comments. It will make your code much easier to maintain!

Now, the monsters are defined in _place_objects_. Trolls will be obviously stronger than orcs. Monsters have two components: Fighter and BasicMonster.

```python
                #create an orc
                fighter_component = Fighter(hp=10, defense=0, power=3)
                ai_component = BasicMonster()

                monster = Object(x, y, 'o', 'orc', libtcod.desaturated_green,
                    blocks=True, fighter=fighter_component, ai=ai_component)
            else:
                #create a troll
                fighter_component = Fighter(hp=16, defense=1, power=4)
                ai_component = BasicMonster()

                monster = Object(x, y, 'T', 'troll', libtcod.darker_green,
                    blocks=True, fighter=fighter_component, ai=ai_component)
```

Keyword arguments come to the rescue again, since in the future most objects will have only a handful of all possible components. This way you can set only the ones you want, even if they're out-of-order!

### AI

---

It's time to make our monsters move and kick about! It's not really "artificial intelligence", as these guys will be pretty thick. The rule for them is: if you see the player, chase him. Actually, we'll assume that the monster can see the player if its within the player's FOV.

We'll create a chasing method (_move_towards_) in the Object class, which can be used to simplify all your AI functions. It has a bit of vector mathematics, but if you're not into that you can use it without understanding how it works. Basically, we get a vector from the object to the target, then we normalize it so it has the same _direction_ but has a length of exactly 1 tile, and then we round it so the resulting vector is integer (instead of fractional as usual - so dx and dy can only take the values 0, -1 or +1). The object then moves by this amount. Of course, you don't have to understand the math thoroughly in order to use it!

```python
    def move_towards(self, target_x, target_y):
        #vector from this object to the target, and distance
        dx = target_x - self.x
        dy = target_y - self.y
        distance = math.sqrt(dx ** 2 + dy ** 2)

        #normalize it to length 1 (preserving direction), then round it and
        #convert to integer so the movement is restricted to the map grid
        dx = int(round(dx / distance))
        dy = int(round(dy / distance))
        self.move(dx, dy)
```

Another useful Object method returns the distance between two objects, using the common distance formula. You need _import math_ at the top of the file in order to use the square root function.

```python
    def distance_to(self, other):
        #return the distance to another object
        dx = other.x - self.x
        dy = other.y - self.y
        return math.sqrt(dx ** 2 + dy ** 2)
```

As mentioned earlier, the behavior is simply "if you see the player, chase him". Here's the full code for the BasicMonster class that does it. The monster is only active if its within the player's FOV.

```python
class BasicMonster:
    #AI for a basic monster.
    def take_turn(self):
        #a basic monster takes its turn. If you can see it, it can see you
        monster = self.owner
        if libtcod.map_is_in_fov(fov_map, monster.x, monster.y):

            #move towards player if far away
            if monster.distance_to(player) >= 2:
                monster.move_towards(player.x, player.y)

            #close enough, attack! (if the player is still alive.)
            elif player.fighter.hp > 0:
                print 'The attack of the ' + monster.name + ' bounces off your shiny metal armor!'
```

That's not terribly smart, but it gets the job done! You can, of course, improve it a lot; for now we'll just leave it like this and continue working on combat. The last thing is to call _take_turn_ for any intelligent monsters from the main loop:

```python
    if game_state == 'playing' and player_action != 'didnt-take-turn':
        for object in objects:
            if object.ai:
                object.ai.take_turn()
```

Ready to test! The annoying little buggers will now chase you and try to hit you.

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_6_code.md).

### Sword-fighting

---

The quest for some epic medieval combat is coming to an end! We will now write the actual functions to attack and take damage, and replace those silly placeholders with the meaty stuff.

The "meaty stuff" is deliberately simple. This is so you can easily change it with your own damage system, whatever it may be. The _Fighter_ class will have a method to take damage:

```python
    def take_damage(self, damage):
        #apply damage if possible
        if damage > 0:
            self.hp -= damage
```

In the next section we'll modify it to also handle deaths. Then there's the method to attack another object:

```python
    def attack(self, target):
        #a simple formula for attack damage
        damage = self.power - target.fighter.defense

        if damage > 0:
            #make the target take some damage
            print self.owner.name.capitalize() + ' attacks ' + target.name + ' for ' + str(damage) + ' hit points.'
            target.fighter.take_damage(damage)
        else:
            print self.owner.name.capitalize() + ' attacks ' + target.name + ' but it has no effect!'
```

It calls the previous method in order to handle taking damage. We separated "attacks" and "damage" because you might want an event, like poison or a trap, to directly damage an object by some amount, without going through the attack damage formula.

Now to give them some use. In the _BasicMonster's take_turn_ method, replace the dummy _print_ line for the monster's attack with:

```python
                monster.fighter.attack(player)
```

And the dummy _print_ line for the player's attack, in _player_move_or_attack_, with:

```python
        player.fighter.attack(target)
```

That's it, the player and the monsters can beat each other silly, but no-one will die. We'll take this opportunity to print the player's HP so you can see it plummeting to negative values as the monsters attack you. This is how you make a simple GUI! At the end of the _render_all_ function:

```python
    #show the player's stats
    libtcod.console_set_default_foreground(con, libtcod.white)
    libtcod.console_print_ex(con, 1, SCREEN_HEIGHT - 2, libtcod.BKGND_NONE, libtcod.LEFT,
        'HP: ' + str(player.fighter.hp) + '/' + str(player.fighter.max_hp))
```

### Untimely deaths

---

Of course, nobody can lose HP indefinitely. We'll now code the inevitable demise of both the monsters and the player! This is handled by the Fighter class. Since different objects have different behaviors when killed, the Fighter class must know what function to call when the object dies. This is so that monsters leave corpses behind, the player loses the game, the end-level boss reveals the stairs to the next level, etc. This _death_function_ is passed as a parameter when creating a Fighter instance.

```python
    def __init__(self, hp, defense, power, death_function=None):
        self.death_function = death_function
```

It is then called by the _take_damage_ method, in the event that the HP reaches 0:

```python
            #check for death. if there's a death function, call it
            if self.hp <= 0:
                function = self.death_function
                if function is not None:
                    function(self.owner)
```

Now we'll define some fun death functions! They just change the object so it looks like a corpse, as well as printing some messages. The player's death also changes the game state, so he can't move or attack any more.

```python
def player_death(player):
    #the game ended!
    global game_state
    print 'You died!'
    game_state = 'dead'

    #for added effect, transform the player into a corpse!
    player.char = '%'
    player.color = libtcod.dark_red

def monster_death(monster):
    #transform it into a nasty corpse! it doesn't block, can't be
    #attacked and doesn't move
    print monster.name.capitalize() + ' is dead!'
    monster.char = '%'
    monster.color = libtcod.dark_red
    monster.blocks = False
    monster.fighter = None
    monster.ai = None
    monster.name = 'remains of ' + monster.name
```

Notice that the monster's components were disabled, so it doesn't run any AI functions and can no longer be attacked.

To assign these behaviours to the player and monsters, pass the extra parameter _death_function=monster_death_ when creating the monsters' Fighter component, in _place_objects_; and also when creating the player's Fighter component before the main loop (_death_function=player_death_).

We'll add a few details to polish it up. For the impatient, however: it's ready to play now! You may notice some glitches though. In _player_move_or_attack_, we only want the player to attack objects that have a Fighter component. So change the _if object.x == x_ ... line to:

```python
        if object.fighter and object.x == x and object.y == y:
```

There's also currently the issue that, when the player walks over a corpse, sometimes it's drawn over the player! There's no guarantee that the player is the object that is drawn last. So we need to draw all other objects first, and only then the player. Just change the rendering loop in _render_all_ to:

```python
    #draw all objects in the list, except the player. we want it to
    #always appear over all other objects! so it's drawn later.
    for object in objects:
        if object != player:
            object.draw()
    player.draw()
```

The same thing also happens with monsters -- a monster corpse being drawn over another monster. To fix it, create a method in the _Object_ class that moves it to the start of the list, so it's drawn first:

```python
    def send_to_back(self):
        #make this object be drawn first, so all others appear above it if they're in the same tile.
        global objects
        objects.remove(self)
        objects.insert(0, self)
```

And call it somewhere in the _monster_death_ function:

```python
    monster.send_to_back()
```

It's finally ready to play, and it actually feels like a game! It was a long journey since we first printed the @ character, but we've got random dungeons, FOV, exploration, enemies, AI, and a true combat system. You can now beat those pesky monsters into a pulp and walk over them! (Ugh!) See if you can finish off all of them before they do the same to you.

You may have noticed that this is fine and dandy for a turn-based game, but in a real-time game the monsters are just too fast! If that happens to be the case with your game, [check out this Extra](complete_roguelike_tutorial_using_python+libtcod_extras.md#Real-time_combat) on real-time combat.

The whole code is available [here](complete_roguelike_tutorial_using_python+libtcod_part_6_code.md#Untimely_deaths).

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_7.md).
