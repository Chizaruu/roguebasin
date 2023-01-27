# Zombie survival RL

---

Zombie survival is a genre that I feel has been let down by the mainstream gaming industry; it offers vast potential but is rarely tapped for more than a few drops at a time, drops which are then overdiluted to the point of absurdity. Usually, the "survival" aspect is completely lost and it turns into little more than a gorefest. This is probably because of hardware limitation to create a real zombie-survival game on the cutting edge that mainstream games require; maybe there's not enough vocal support (hell, perhaps I'm the only one who feels this way!), but for whatever reason - I feel the roguelike approach lends itself pretty well to the prospect. While it wouldn't be a traditional roguelike - there'd be no spells, no magic equipment no Fun unidentified potions etc - there are still many elements of the typical roguelike that would give the genre the boost it requires. The "wield anything" mechanic makes for a good realism-strategy, the complexity of resource management, the tactical combat style, to mention a few, are ideal for a game about surviving a zombie-plague.

Being very unfamiliar with roguelike development, and game development in general, I feel that what this post outlines is not a project I could even hope to realize for a long, long time still, but I nevertheless feel it's not a bad idea and I figured I'd post it to this wiki because I think roguelike authors are probably the most likely to make something of it, and while it's probably not at all original and may have been done before but flown below my radar, it might perk someone's interest or tickle someone's intriguebone enough to give it the fair go the genre deserves.

## Zombie survival roguelike-ish

---

The first thing that needs to be established with a zombie game is what branch of zombies to use - there have been a lot of different types, each with their own ability, but I've personally always felt that the one "true" zombie-type is the Romero zombie, lately replaced in my mind by the Max Brooks zombie - which is very similar but wisely decides that "land of the living dead" never happened. Max Brooks' (Zombie Survival Guide, World War Z) are the result of a virus which kills its host and reanimates its corpse, guiding it to spread the infection through bodily fluids - saliva (getting bitten) or blood (from messy killings) etc - which leads the zombies to humans for reproduction; using a sort of "hunger instinct" to get its reanimated host to spread the virus.

The zombies are stupid - or rather mindless. That's an important distinction; too many zombie flicks portray zombies with intelligence and tactical decision making; this should not be so in a zombie survival game. An individual zombie should be more nuissance than danger - easily dispatched - except for one distinct aspect. The moan. The Brooks' zombie has an instinctual reaction to spot prey, which is to utter a loud, gutteral moan. This moan can carry quite a long way, and when a zombie hears this moan - they in turn utter a moan of their own and move towards the source of the original moan. This could potentially turn one stray zombie into a hungering, terrifying horde. This will serve to provide the player with both challenge (as while one zombie is not a threat, two or three might be because of their damage-potential - that is fatal if bitten) and tactical decision making. "Do I run away, or do I dispatch this zombie for experience and look for salvage in the abandoned building over there?"

Example: The player is scavenging for something in a log cabin in the woods. Zombie A spots the player and lets out a moan, then advances on the player. The player doesn't know how many more zombies are in the forest; there could be none, there could be dozens - and they could be steadilly streaming towards his position as he takes the time to kill Zombie A and continue his scavenging, but he needs a quest-item from the cabin. If he leaves now, he'll have to come back later - at a time when it might be more dangerous than it is now. Further away - Zombie B has heard Zombie A's moan, and utters his own moan, lurching towards Zombie A's position at the time of the moan. Further away still, Zombie C hears Zombie B's moan and so on.

If he continues the search for the quest item and kills Zombie A - Zombie B might have had enough time to get within sight of the player. This causes him to utter a new moan and move to attack. Zombie C hears Zombie B again and he too begins to advance towards the player's position, perhaps giving the player more than he bargained for. If the player leaves, zombie A & B might still be around when he gets back, or they might have moved on since there was no longer any prey around.

Considering the stupidity and low-threat of the single zombie (though, you'll rarely meet just one), it's obvious that the threat to the player comes either from a Horde, or - more dangerously and requiring much more tactics - other survivors. The other survivors come in a few flavours; there's the background-survivors, found in safe-zones who have little to say. They're flavour NPCs, they're there to fill streets and houses. There are Quest-Giver survivors, say they had to leave something behind when they fled their home, they want you to go there and bring it to the quest giver for a reward (XP and/or money). There are military-survivors, the "town guards" of the safe-zones and they occasionally have quests for the player as well (scout out this settlement/aid a group of soldiers in search & rescue/aid a group of soldiers in reclaiming an area) and can sometimes join a player on a quest to help them (but leaves the player's party after all quests have been resolved, or if the player strays too far from their safe-zone). Roving the wilds and urban centres, there are also bandits - lawless NPCs who have no qualms shooting you and taking everything you own just for fun, taking advantage of the anarchy of the zombie-infestation. Lastly, the most dangerous to a roving, scavenging player, is the unpredictable lunatic. A roving band of bandits can definitely spell doom for a player unprepared for them, they'll at least try to not alert the undead and are on the whole rare - lunatics are less rare, and is blind to the zombie threat when they spot the player; they rig traps, they snipe from buildings, they fire freely when a player is approaching - and in doing so alert every zombie nearby that dinner's served. Occasionally, the lunatics can be a person you've been tasked to find (say NPCs are spawned in cities for search-and-rescue quests given by citizens or the military, and some of them go insane depending on some factors) which could mean quest complications (you were sent to rescue them, but end up killing them. Grieving relatives may not be very understanding).

Zombies should have abilities like the player, but randomly shuffled to create variation between the zombies - obviously, the only useful attribute to the zombie is the strength attribute - but the others lend well to varying descriptions to the individual zombies. Zombies also don't care about muscle-limits; they will happily abuse muscles until they snap, therefor a strength of 5 in a zombie, should be equivalent to a 6-7 in a human (but should be listed as a 5. They don't -look- any stronger and in reality, they aren't stronger, than a 5-strength human. They just don't feel the pain of an abused muscle and so push what they have further).

The player themselves - and NPCs - have a set of attributes and skills, as is usual, and experience lets a player improve his skills and attributes. Keeping things "real", improvement of a skill or attribute depends on their use and should be increased gradually as they're applied. The more a player, say, fires a gun - the better they get at handling guns. The more heavy stuff a player lugs around, the stronger they get etc. Of course, carrying lots of heavy stuff means you're slowed down and endangers you of being eaten by zombies, firing guns a lot means you need a lot of ammo... Improving skills should be a balance of risk, supply-management and gain.

### Attributes

---

The attributes should be something along the lines of:

Strength - dictates melee damage and amount of stuff the player character can carry without penalty. Overloading oneself means increased rate of food, water and energy depletion and gives the player penalties to sneaking and other agility-based rolls. Doing strength-related exercise increases this value, say carrying more weight than you're comfortable with and running around.

Agility - dictates base speed of the player character, as well sneaking and long-range accuracy. Agility exercises (running, traversing difficult terrain) increases this value. Intelligence - gives advantages to certain skill-rolls and is increased through study.

Endurance - determines how quickly the player wears down, how often they need to sleep and is increased by pushing the envelope (spending longer on the move with heavy backpack, running longer distances etc). This also controls how quickly the player gets hungry.

Willpower - the ability to keep calm under stress. As zombies approach, the player's willpower is taking greater punishment; failing a check might mean the player fires their gun before meaning to, failing critically means the player panics and tries to run away. Every time the player lets a zombie get close enough to be "too close" without panicking, willpower has a chance of increasing.

### Skills

---

The skills go something along the lines of:

Small Melee - the effective use of one handed melee weapons, increases hit-chance, especially chance of hitting critical areas.

Large Melee - the effective use of two-handed melee weapons.

Small Firearms - small firearms, like handguns, and the use thereof. Also handbows and so on.

Large Firearms - rifles, bows and crossbows etc.

Long Range targetting - increases the effective range of weapon-attacks (never beyond the weapon's effective range; the player just gets better at aiming over greater distances). When the player hits an enemy at ranges longer than this skill dictates they "should" be able to, this skill increases a bit.

Wilderness Survival - this skill increases the chances for a player in the wilderness to find things to eat, to locate sources of water and other survival skills. Based on intelligence and increased through study.

Skill usage will increase that skill, practice makes perfect.

### Items

---

Items in the game should be realistic; they should be guns, tools etc. that you would expect to find in an actual zombie-apocalypse. No fantastical weapons, it's a survival game and so inclusion of a zombie-seeking missile launcher will take the main-focus of the game out. Guns and rifles should be somewhat difficult to get ahold of, and ammunition should be a limited resource that the player needs to keep a close eye on and be disciplined with. Ammo in every drawer will break immersion and hamper the challenge, but no ammo where you'd expect to find an abundance (no ammo in the gunshop of the town that hadn't been raided, for example) will strain immersion as well. Same goes for food and water. Balance need be concidered when placing and pricing these resources.

A varying difficulty curve could be created by giving different starting-conditions for the player. Say, "easy" means the player starts out as a trained recruit in a safe-zone army, equipped and trained as a scavenger and scout. "Medium" means the player has some knowledge on firearms, hunting and so on, and have to escape their house on the outskirts of a town with limited supplies, heading towards a safe-zone, the position of which had just been relayed to them over a short-wave radio. "Hard" places the player in an apartment, where they have held the undead at bay thanks to their recently installed safety-door. They own a small firearm and rudimentary knowledge of how to use it, but having to leave because their supplies are all but gone. Scavenging the town they start in for supplies will probably be required for them to make it to the nearest safe zone - wherever that is. The starting number of attribute-points and skill-points given should perhaps not differ between difficulties, depending on how that balances out.

Since it is a survival game, the player has to manage their supplies appropriately in order to... Well, survive. They have to take into account energy, food and water. Lacking in food will give penalty to all rolls as well as increasing the rate of energy depletion; dehydration should do the same as should a lack of energy - and these three should stack, to really show the miserable state one would be in, starving, dehydrated and sleep deprived; you would not survive a zombie outbreak unless you kept yourself fed, watered and rested at all times. You need to be on top of your phsyical self or find yourself eaten; so trying to stride on through the night is a bad idea - unless you have a very good reason to do so.

### The World

---

Ideally, the world should be created procedurally and realistically - create urban centers, smaller communities surrounding them and run roads and utilities between them and with more or less isolated hunting lodges in forests etc. dotting the landscape. Safe zones should be not too close and not too far away from urban centers and be well defended from bandit raids as well as zombie attacks, and the military inside will be trying to find missing people, scavenge supplies and clear out whatever zones they can. Ideally, a "working" region should be created, infection carriers seeded and then allow a sort of natural spreading of the virus and creating of safe zones etc. take place. Say a year or so of time is simulated from outbreak to beseiged stability, and the world simulated all at once - allowing things to happen in places the player isn't at the moment. say, finding their safe zone is beseiged by the undead means the player will now have to GTFO or find himself being chomped on and so on. If the game plays real-time, and a server-application could run the world-simulation, the game might suit itself pretty well to multiplayer action. A construction-system could make this even more interesting, allowing a group of players to work together in creating and defending a fortress from the undead, or together clear out a town of zombies etc.

---

This is just a rather rough idea for a game; but with some polish and balancing, I think it could make an interesting one, at least. =P

Fallen Fox 22:38, 16 February 2012 (CET)
