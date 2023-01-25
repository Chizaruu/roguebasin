# Ratio AI

---

## Ratio-based AI for Dungeon Monsters

---

A nice roguelike should have decent AI. Obviously, there should be a fairly complex AI scheme for a town or village, with storekeepers, villagers, guards, soldiers, etc. However, for dungeons or wilderness encounters yu can use a simple AI involving rations. Each monster should have a certain proability or doing one action or another. These actions can be broken down into the following categories:

- Movement
- Escape
- Attack
- Defense

### Movement

The movement ratio determines what a monster will do when it sees an enemy. It's the probability of whether a monster will attack the enemy, move towards the nearest monster of it's type, or just move randomly. The ratio would be something like this - Attack:Pack:Random. A zombie might have a 1:0:0 ratio, meaning that it will always attack on sight. A goblin ratio could be 1:2:0; it would prefer to join other goblins rather than attack (33% chance of attacking, 67% chance or regrouping). A wild animal, on the other hand, like a jackal, might be 2:3:1 (33%:50%: 17%). It would join its kin, or attack, or maybe just move around. Of course, if a ratio condition could not be met, you would set its value to 0. A lone goblin would always attack (1:0:0). I think that pretty much covers movement.

### Escape

Escape is a lot like movement. Each monster should have a fear threshold, upon reaching it tries to escape. Suppose out goblin from the previous example has a fear threshold of 40%. Its escape AI would be triggered when its hit points dropped below 40%, or when it encounters an enemy that the goblin only has 40% of the power of (a goblin with a power of 10 sees an ogre with a power of 25 (10 / 25 = .4 = 40%)). A monster's power would probably be based on its strength, toughness, armor, and weapons. Anyway, once the the goblin's fear threshold is reached, it has three options: run away from the monster, run towards the nearest exit (a door or some stairs possibly), or run towards other goblins. Our ration looks like this - Away:Exit:Pack. For a goblin, the ratio might be 2:0:1. Goblins aren't especially smart, so they wouldn't seek out an exit. The goblin would have 33% chance of joining a friend, and a 67% chance of just running away. The same rules from the last example also apply here: as long as there are no other goblins, the Pack value would be zero and the goblin would always flee. A note about fear thresholds: The fear threshold should diminish as the creature gets closer to other creatures like it (one goblin might not take on a level 25 hero, but a group of goblins would). This works well, because as long as there are many goblins, their fear thresholds would be low. However, once the player kill enough of them, the goblins would break apart and run. Also, mindless or non-sentient creatures should have a very low fear threshold. A zombie should never run.

### Attack

Ratio-based AI also works well for attacking. A monster would have a ratio representing the probability of using a ranged weapon or a melee attack (Range:Melee). If a creature is inclined to melee, it will attack it's enemy, or if the enemy is out of range, the monster will move towards it. Otherwise, if the monster will execute a ranged attack or back away from any adjacent enemies. This works out nicely. If we have a group of orc bowmen and orc skirmishers, the skirmishers would surround and attack the enemy, while the bowmen would rain arrows on the enemy from afar. Also, there should be a ratio of magical to physical attacks (Magic:Weapon), as well as Magical to Ranged attacks. These would be checked after the Range:Melee ratio. Remember, if a condition cannot be fulfilled, its value is set to zero. For example, if a creature is out of MP, there's no chance that it will try to cast a spell. If a creature is out of arrows, it will try to melee an enemy.

### Defense

First of all, there should be an Attack:Defense ratio. This would be the chance that a monster would attack instead of making itself tougher. The defense ratio woulf be Heal:Teleport:Enhance. The first two are fairly self-explanatory. Enhance refers to increasing defense, attack, or speed. An enhancing monster would evaluate its options based on a Power:Toughness ratio and cast the appropriate spell or wear the best equipment.

### Implementation

So how do we put this into our RPG? First we want a simple function for evaluating ratios (this assumes you have a function to get random integers):

```c
int Ratio(int a, int b, int c)
{
     b += a; c += a;
     int r = Randint(1, c);
     if (r > b) return 3;
     if (r > a) return 2;
     return 1;
}
```

This returns a number that indicates which ratio value was used. Now, the order that you check the AI:

```text
canSeeEnemy
  Action
    Attack
      Melee
        Magic
        Weapon
      Ranged
        Magic
        Weapon
    Defend
      Heal
      Teleport
      Enhance
  Pack
  Random
```

And that's it for Ratio-based AI. If you have any comments or suggestions, e-mail me at gamer2k4@gmail.com.
