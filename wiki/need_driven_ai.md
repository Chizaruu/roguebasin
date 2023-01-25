# Need driven AI

---

## Introduction

---

This article is based on a system proposed by Hansjoerg Malthaner [hansjoerg.malthaner@danet.de] which in turn based some of it on Joseph Swing's excellent article "A better monster AI". The system described here is used for my own RL Dungeondweller.

There are many ways to create an interesting AI. One way would be to hardcode each creatures behaviour. If enough work is put into such a hardcoded system the results can be quite impressing, but as said, it needs a lot of work.

A better (or at least faster) way of creating an interesting AI would be to take a look at behaviourism. Behaviourism states that a creature reacts to external stimuli. This is not entirely true since humans have a mind and a concept of Self, but the general idea behind behaviourism is sufficient for the AI of a roguelike.

Imagine what would happen if you teleported a Kobold into a big dungeon. Lets say that the Kobold starts in an empty corridor. The first thing the Kobold does is to look around and ensure that there are no enemies in sight. If he is alone he decides that he feels safer if he wields a sword and equips some armour. At the moment this is his most urgent need. The Kobold reacts to the situation and puts on his leather armour and wields his shortsword. If the Kobold still doesn't see anyone he begins to interest himself for his environment. His most urgent need would then be to move. The Kobold continues to move through the corridor until he reaches a door. If the Kobold still doesn't see anyone his most urgent need would be to open the door. The Kobold reacts to this new situation and opens the door. He then looks around again and sees that the door led into a big room. Near the door lies a pile of shining gold coins and at the other end of a room a big snake looks back at the Kobold! The Kobold evaluates this new environment, and decides that he loves the gold more than he feares and hates the snake. The Kobold reacts to the situation and starts to move closer to the pile of gold. As the Kobold gets closer his fear and hate of the snake grows, as does his love for the gold. At some point the Kobolds fear is greater than the Kobolds love. The Kobolds most urgent need will become to move away from the snake. The Kobold reacts to this and starts to retreat from the snake. All of a sudden a door opens to the left of the Kobold and another Kobold appears. Our Kobold evaluates this new scenario, and immediately recognise the other Kobold as a friend. The Kobold loves :-) other Kobolds, and this new Kobold is armed to the teeth with shining armour and pointy weapons. Once again his love for the gold and for his new found friend is greater than the fear of the snake. The Kobold reacts, moves towards the gold and his friend and picks it up. ...

The story above seems fairly realistic and it is not that difficult to achieve a behaviour similar to the behaviour of the Kobold. It all boils down to a three step process:

1. Observe and evaluate the environment
1. Find the most urgent need, based on the environment and the self
1. Perform the need

First the AI need to look around and evaluate it's environment, just like the Kobold did. The AI assigns values for Fear, Love and Hate to all creatures and objects within sight. The next step is to find the most urgent need at that time. The AI base this descision on the Fear, Love and Hate for perceived objects and creatures and on its own status (the Kobold puts on equipment). When the most urgent need is found it is performed, and the cycle starts over again.

Each of the above steps are explained in detail below:

## Observe and evaluate the environment

---

Before the AI decides what to do it needs to evaluate its environment. The AI need to look around and memorize all objects and creatures that it can perceive. This can be done using normal LOS, or any other suitable way. Each object or creature (from now on entity) perceived is assigned a Fear/Love value (1.2) and a Hate value (1.3). The most Feared, Loved and Hated entity is memorized (1.4) and the Fear/Love and Hate center is calculated (1.5). These values will be used later on to determine where to move or whom to attack.

How to achieve all this? Well, it is fairly simple. To help us with our calculations we need a couple of help methods:

### Help methods

These methods are made to make life easier for us during the calculation of the Fear/Love and Hate values. The methods could have more uses in your game, so they are worth implementing. Each entity needs a three methods called; physicalCondition(), perceivedStrength() and perceivedDanger().

#### physicalCondition()

this method returns the entity's current hitpoints divided by its maximum hitpoints. This gives us a value from 0 and upwards:

```text
0 - Dead
< - Damaged
1 - Not damaged
> - Boosted
```

#### 1.1.2 perceivedStrength()

This method should return the entity's perceived strength, ie how strong it looks if we were to see it. This value should be based on the entity's physique, not powerful equipment. I base the value on the entity's strength multiplied by it's physicalCondition(). You may wish to include more factors when calculating the perceived strength, but this works fine.

#### 1.1.3 perceivedDanger()

This method should return the entity's perceived danger, ie how dangerous it looks. This value should be based on the entity's equipment. If the entity has sharp weapons and heavy armour the returned value should be high.

Ok, now that we have some methods to work with, we can assign Fear/Love and Hate values to the perceived entities.

### The Fear/Love value

The Fear/love value, assigned to each perceived entity, is based on factors such as the perceived entity's strength, danger and distance from the AI. If the entity is a friend it gets a positive value (=love) and if it is an enemy it gets a negative value (=fear). The closer to the Ai, the higher the value.

The AI rates a friend/enemy differently if the AI belongs to an instinct driven creature (tiger, snake etc), a creature with a mind (human, orc etc) or a mindless creature (undead).

#### Mindless Fear/Love

A mindless creature doesn't have any feelings at all and rates friends and enemies as Fear/Love = 0

#### Instinctdriven Fear/Love

A creature driven by instincts doesn't have the intelligence to measure a friends equipment. It measures a friend by how much stronger he/she is than itself:

```text
(1000 * (friend->perceivedStrength() / own->perceivedStrength())) / distance
```

Enemies are measured in the same manner (except for the '-'):

```text
(-1000 * (enemy->perceivedStrength() / own->perceivedStrength())) / distance
```

#### Minddriven Fear/Love

A creature with intelligence measures a friend both by its strength and by its equipment. If the friend is stronger and/or better equipped it is loved more:

```text
(500 * (friend->perceivedStrength() / own->perceivedStrength()) +
500 * (friend->perceivedDanger() / own->perceivedDanger())) / distance
```

And enemies:

```text
(-500 * (friend->perceivedStrength() / own->perceivedStrength()) +
-500 * (friend->perceivedDanger() / own->perceivedDanger())) / distance
```

You may wish to change the 500 \* strength and 500 \* danger to other values. Some minddriven creatures may value equipment more than strength and vice versa. Remember that the sum should be 1000. There is no particular reason for choosing 1000, you could go with even higher numbers to achieve higher variation, but I think 1000 is enough.

Remember that Fear/Love also applies to objects. If the perceived entity is an object you may wish to assign a value to it. Treasures and similar items would become Loved, at least by most minddriven creatures. Similarly some kinds of objects might be Feared (unholy symbols etc)

### The Hate value

The Hate value is based on the AI's feeling for other entities along with the distance from the AI. The more it hates something the higher the value. As for the Fear/Love value you can differentiate between instinct, mind and mindless AI. You can also add extra rules to make your AI hate some creatures more than others. For simplicity I just set the rule that if the AI isn't of the same race as the perceived entity the Hate value is set to 1000 / distance, otherwise 0.

### Memorize most Feared, Loved and Hated

You need to keep track of the most Feared, Loved and Hated entity that the AI can perceive. This is just a matter of saving the currently most Feared, Loved and Hated creatures Fear/Love or Hate value along with a pointer or similar. This is kind of implementation specific, so just solve it in a way suitable for your game. It should present no problem.

### Calculating the Fear/Love and Hate center

This is where it gets really interesting. Here you need to calculate where the Fear/Love and Hate center are positioned among all of the perceived entities. I'll try to illustrate what we wish to achieve:

```text
 .........  In this first example the AI (A) can perceive only one entity (1)
 .1.......
 .........  The center of Fear/Love and Hate are then positioned on the same
 .........  cell as the entity
 .........
 ....A....
 .........
```

```text
 .........  In this example the AI (A) can perceive two entities (1) and (2)
 .1..x..2.  The AI has assigned exactly the same Fear/Love and Hate values to
 .........  both entities
 .........
 .........  The center of Fear/Love and Hate are then positioned at a cell on
 ....A....  equal distance from both (1) and (2). In this case cell (x)
 .........
```

```text
 .........  In this example the AI (A) can perceive two entities (1) and (2)
 .1f.h..2.  The AI has assigned a higher Fear/Love value to (1), but the same
 .........  Hate values to both (1) and (2)
 .........
 .........  The center of Fear/Love (f) is then positioned closer to (1) than
 ....A....  than (2). The center of Hate (h) is still placed at equal distance
 .........  from (1) and (2)
```

```text
 .........  As you add more entities the Fear/Love and Hate centers are moved
 .1.....2.  around more and more.
 .........
 ...h.....
 .........
 ....A....
 ..f......
 .........
 .3.......
```

At a first glance this may seem quite complex, but it is fairly simple to calculate. The first thing you need to keep track of is the total Fear/Love and Hate values for the entities that you have assigned Fear/Love and Hate values to. The next thing you need is a x,y coordinate for the Fear/Love and Hate center.

When the AI begins to evaluate its environment the total Fear/Love and Hate are zero and the Fear/Love center and Hate center has not been assigned any values.

After assigning a Fear/Love and Hate value to the first of the perceived entities the center of Fear/Love and Hate are positioned at the same coordinates as the entity, just like in the first figure above.

Subsequent position calculations are done like this:

1. Find the smallest of absolute value of total Fear/Love and absolute value of

```text
perceived entity's Fear/Love. ie:

minFL = __min(abs(totalFearLove),abs(entityFearLove))
```

2. Find the largest, ie:

```text
   maxFL = __max(abs(totalFearLove),abs(entityFearLove))
```

3. Subtract the x coordinate of the Fear/Love center from the perceived entity's x coordinate, and multiply the result by (minFL / maxFl). Add the resulting value to the Fear/Love center's x coordinate. ie:

```text
   fearLoveX += ((entityX - fearLoveX) * (minFL / maxFL))
```

4. Do the same for y. ie:

```text
   fearLoveY += ((entityY - fearLoveY) * (minFL / maxFL))
```

5. Repeat the same procedure for Hate center

```text
   minH = __min(totalHate,entityHate)    absolute values not nescessary since
   maxH = __max(totalHate,entityHate)    Hate value always is positive
   hateX += ((entityX - hateX) * (minH / maxH))
   hateY += ((entityY - hateY) * (minH / maxH))
```

After you have repeated all of the above for all of the AI's perceived entities you know which entity the AI Feares, Loves and Hates the most. You know of the total Fear/Love and Hate values for all of the perceived entities, and you know where the center of Fear/Love and Hate are positioned.

When all of this is done we have evaluated the environment, and have all the knowledge we need to decide for an appropriate course of action.

## Find the most urgent need (contesting actions)

---

Each AI driven creature needs to be assigned a set of actions that it can perform. The actions vary depending on the type of creature. A humanoid should be able to perform actions such as OPEN, PICKUP, DROP, EQUIP, MOVE and ATTACK while a snake is a bit more limited; MOVE and ATTACK.

Each action is represented by two methods called rate() and execute(). The methods take a reference to the creature in question as parameter.

Each time the AI need to decide which action to perform, the rate() method is called on each of the available actions. The rate() method decides how urgent it is to perform the action. A high value gives the action high priority and a low value gives the action low priority. The action that has the highest rating is the one being performed, i.e calling the execute() method.

This is simple enough, but how to rate each action? This is the tricky part, and it needs a bit of balancing before it works. I used fixed values for the priorites:

```text
PRIORITY_NONE    0
PRIORITY_LOW     50
PRIORITY_NORMAL  100
PRIORITY_HIGH    200
PRIORITY_HIGHEST 800
```

Let's have a look at some of the actions one by one:

### OPEN.rate(creature\*)

The method examines the mapcells surrounding the creature. If a closed door is found the method returns a priority of NORMAL, otherwise 0. The closed door is remembered so that the execute method knows what to open.
PICKLOCK.rate(creature\*)

The method examines the mapcells surrounding the creature. If a locked door is found the method returns a priority of NORMAL, otherwise 0. The locked door is remembered so that the execute method knows what to lockpick.

### DROP.rate(creature\*)

In the system that I use for Dungeondweller, each creature has a method called physcialSpeed(). The method returns a value from 0 and upwards, 0 meaning immobilized, less than 1 is slowed, 1 normal speed and greater than 1 meaning fast. The method takes into account the creatures strength, the amount of inventory carried and the creatures physical condition.

If the creature is slowed down (physicalSpeed() < 1) the rate() method decides for an object to drop, and returns a priority of NORMAL/physicalSpeed(), otherwise rate() returns 0. This gives a higher value the more encumbered and wounded the creature is. If extremely hurt the creature will throw away inventory to be able to flee faster.

There is a lot of room for intelligent selection of object to drop, but you could just start from the top of the creature's inventory, or choose the heaviest object in the inventory.

### PICKUP.rate(creature\*)

If the creature is standing on an object and it isn't slowed down the method returns a priority of NORMAL, otherwise 0. The object is remebered so that the execute() method knows what to pick up.

You might wish to modify the priority if the creature is a scavenger or if the object to pick up is valueable etc.

### EQUIP.rate(creature\*)

If the creature isn't equipped with a weapon and it has a weapon in its inventory the method returns a priority of HIGH, otherwise it continues to check for other things to equip (armour, rings etc). If something is found it is remembered for the execute() method and the priority is HIGH. If nothing needs to be equipped the method returns 0.

You could expand this and check for better equipment in the inventory than the already equipped equipment. Checks for broken weapons and similar things can also be done here.

### MOVE.rate(creature\*)

If and where to move the creature is decided by the environment. This is where all of our calculations in step 1 comes into use.

If the total Hate and total FearLove are zero the creature should just move around in a manner that seems intelligent. There are many different ways to move a creature on a map. This is a rather big topic and it would be more appropriate to address this in another article. You could just write something simple that made the creature follow the walls or similar. Remember the direction of movement and return with a priority of LOW

Else if the total Hate is zero, and total Fear/Love is non zero the creature should move towards the Fear/Love center. This will make the creature move towards it's friends. To prevent your creatures from forming big packs that won't move an inch since the all love eachother that much, you could also check the creatures distance to the Fear/Love center. If the creature is within a certain distance, say three cells, just move the creature using the same system as for total Hate and total Fear/Love equal to zero. Remember the direction of movement and return a priority of NORMAL

Else if the total Fear/Love is bigger than the total Hate the creature is at a scary place and should flee. The creature should move in a direction directly away from the Fear/Love center. Remember the direction of movement and return a priority of HIGHEST.

Else the creature should move to attack, and direction of movement should be towards the center of Hate. Remeber the direction of movement and return a priority of HIGH.

### ATTACK.rate(creature\*)

Last but not least we have the attack action. If the most Hated entity is within range of any of the creatures attacks the target is remembered and the method returns a priority of HIGHEST.

## Perform the need

---

All that is left now is to call the execute() method of the action that returned the highest rating. I kept the code for the execute methods very general so I could use the same execute() code when the player performs his/her actions.

## How to achieve more variation

---

At the moment I have only implemented the basic system described above, but from the discussion at [r.g.r.d](rgrd.md) Hansjoerg suggested the following tweaks:

How to get variety into the AI? Of course we can give each monster individual fears and hates. We can change the things/monsters which are feared and hated and we can change the strength of fear and hate. This will make cowards and fighters from our monsters. The love value (a monster has negative fear for friends) will make them move towards each other until their fear reduces and if they hate something nearby they will move there as a pack.

More variety can be introduced by restricting the monster's perception. Some monsters may see far, others not. Some may recognize weapons as dangerous, some not. If a monster does not recognize weapons it might not fear a heavily armed player and attack him (stupid monster). Another monster might fear the player if it preceives him healthy and well armed. It fears him less if wounded.

Such a monster will stay away from the player as long as he is well and wields his weapon. This monster will attack if the players weapon breaks or the player self is wounded (clever monster).

If a monster's fear drop faster with distance than the hate it will follow the player. The monster fears to be near the player, but still hates him from a distance, which makes it stay away a certain distance.

Monsters with ranged attacks which fear the player will stay away from him, until their fear drops, and then stay there and attack the player with their ranged attacks.

If fear is made to depend on the health of the monster, an monster could rush towards the player (no fear) and attack him. After the first strike they receive the fear goes up and the monster runs away. This would yield the above mentioned "hit-and-run" tactic.

From kamikaze to cowards quite some different monsters seem to be able to be modeled by this simple system. I'm not sure if it will really work until I made my tests. One thing is sure: monster stats must hold all the mentioned information (what is feared, what is loved, what is hated, how strong does fear drop with distance, how strong does hate drop with distance, can the monster perceive weapons and armor, how tough is the monster/how strong does it fear to be wounded)

Send any comments, questions or suggestions to: Bj?¶rn Bergstr?¶m bjorn.bergstrom@roguelikedevelopment.org
