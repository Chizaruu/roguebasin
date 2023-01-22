# World of Rogue

---

## Plot

---

In the original Rogue, the goal was for a 'rogue' to retrieve the Amulet of Yendor. But where did this rogue come from? And where does he need to take the amulet? Surely there's a world outside of the dungeon, and there must be some reason why this rogue made it to the dungeon and no one else did. World of Rogue will explore the background of this famous dungeon-runner.  

## Concept

---

World of Rogue is meant to be the epitome of a roguelike. Obviously this is a pretty bold statement, so there should probably be an explanation of what is meant by it and what classifies a game as a roguelike.  

* randomized gameplay for infinite replayability, including random monsters
* ASCII interface (no graphics)
* easy to learn, difficult to master gameplay  

World of Rogue will take the phrase "no two games are the same" to a whole new level. The world will be randomly generated. This includes the overworld, towns, locations of interest, and dungeons. Each town will be populated by randomly created NPCs, and each dungeon will contain truly random monsters.  

### Random Monsters

#### Enemies

At the start of the game, several hundred monsters will be randomly created. They will be given a random name, a random description, and several random abilities. The amount of abilities and overall strength of the monster will determine its "level", that is, how soon in the game the player will face it. A potential monster might look like this:  

The Daruka is a large, vicious mammal. It employs its claws to deadly effect. It prefers a cool climate.  

Large would determine its physical stats, like hitpoints. Vicious might be a modifier that makes it more likely to attack, or less likely to run when at low health. "Claws" could be a special attack. And "cool climate" indicates where it's generated or that it is weak to fire attacks.  

#### Allies

Townsfolk and other allies will be randomly generated as will. They will have a random name and several flags, such as:  

* QUEST - this monster offers various quests to the player
* MERCENARY - this monster will accompany the player, for the right price
* STORE_[type] - this monster will sell items to the player.
* RUMORS - this monster will give game hints and advice
* FACTION_[#] - this monster is in a particular group, such as Royal Knights, Thieves Guild, etc. (used in quests)  

### Combat

Combat will consist of what I call "fixed randomness". A monster has several body components, such as arms, legs, head, etc. Each component has a certain amount of hitpoints. Each attack deals a fixed amount of damage to one or more random components (fixed randomness). If that part is armored, the armor must be destroyed before damage can be taken to that part. Whenever a body component has zero hitpoints, the monster or player dies. I think this is a logical combat abstraction. You can keep fighting with a wounded arm. However, if that arm gets hacked off, you have a very low chance of survival, and will probably not fight competently afterwards. Weapons do a fixed amount of damage. Depending on the weapon, they may affect only one or several body parts, and they may do high or low damage. A whip might deal 1 damage to 2 body parts. A greataxe might deal 3 damage to only 1 body part. In addition, weapons have attack speed and hit chance. These four weapon attributes (attack speed, hit chance, hit amount, hit damage) are the only factors that diferentiate weapons. Strategy involves using the right weapon at the right time, instead of being stronger or more skilled than your opponant.  

### Character Development

Instead of having a class determine skills and combat abilities, the player will have a set of attributes that determine NPC interaction. Every character will start out as a sort of template; there will be no starting equipment, no starting skills, no stats. That's right: This game has none of the familiar Strength, Dexterity, and Constitution player stats. Because combat strength is determined by weapons, stats are completely unnecessary. However, as I mentioned before, there will be interaction attributes (think [GearHead](gearhead.md)).  

### Quests and NPC Interaction

#### Interaction Attributes

The player will have several attribute that determine how likely it is that an NPC will help him or give him a quest.  

* Morals - how likely one is to do the right/lawful thing
* Reliability - how likely one is to finish a task
* Charitibility - how likely one is to work for small rewards
* Employability - how likely one is to accept a quest  

#### Quests

Quests will also employ the idea of "fixed randomness". There are several fixed quest types, but they will vary in levels of difficulty and rewards.  

* Assassination
  * Random monster to kill
  * Random map
  * If high enough level, add bodyguards
* Exploration
  * Random map
  * If high enough level, add patrolling monsters
  * certain percent of map must be explored to complete the quest
* Take item to ally
  * Random item
  * Time limit
  * Reward based on distance, item value
* Retrieve item for ally
  * Random item
  * If high enough level, add guardian monster
  * Reward based on item value
* Rescue an ally
  * Random map
  * Random ally to rescue
  * Low level, ally is lost
  * High level, ally is imprisoned  

## Programming Structure

---

World of Rogue should be a very "light" program. Because it is completely random, it requires no special case coding and no plot lines. Data files will be minimal or non-existant, since everything is generated during runtime. It should also be fairly simple to code, consisting only of an engine and data generators.  

## Current Version

---

Presently World of Rogue is merely conceptual. However, there should be a working version out by the end of the year.  

## Contact Information

---

If you have questions, comments, advice, or ideas, feel free to [contact the developer](gamer2k4@gmail.com).  

[Gamer 2k4](gamer_2k4.md)
