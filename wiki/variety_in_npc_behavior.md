# Variety in NPC behavior

Ideas for various NPC behaviors, compiled from [r.g.r.d.](http://groups.google.com/group/rec.games.roguelike.development/)

## bear

Raise an alarm/Notify superior officers
entrench (create favored terrain)
set traps involving other monsters as the mechanism or bait
play powerful monsters against each other hoping to loot corpses
run'n'gun tactics
hack'n'back tactics
coerce other monsters to act via blackmail or domination spell
argue with each other
fight each other
show off in foolhardy ways
visit battle sites or barber shops looking for a bit of his hair (to bind the head on that arrow of Mike-the-Fighter slaying...)
seeking vengeance for killed family/community
Hold the @'s family or familiar hostage.
play stupid drinking games in monster taverns
brew strong drink/cultivate strong plants (and get really upset when the @ destroys all those mushrooms of hallucination, weeds of mellowness, or potions of booze...)
Make friends with an @ who gives them booze
Play BDSM games with each other
Torture as spectator sport (could also be a religious rite)
Trigger a "recall item" spell to conjure an item they own out of the @'s hands.

## Hansjörg Malthaner

- search for equipment
- go shopping
- search the dungeon
- choose best combination of equipment
- trade news to other NPC
- grow plants/animals
- do some mining
- learn new spells
- do training, i.e. combat training
- repair broken items
- mix/brew potions
- search/buy ingredients
- make music/sing a song
- cook a meal
- build a shelter (or just a tactical improvment)
- clean the dungeon
- polish the treasures
- improve items (i.e. has arrows, finds a scroll of firetongue and enchants the arrows with the scroll)
- copy/create a spellbook (master mages helper)
- make a map of the surroundings
- go hunting (animals -> food)

## konijn

Seek hero to fix unpleasant situation.
Seek hero to deliver message.
Seek hero to hand over an item.

## Benhem

"crab" movement: fast sideways, slow up/down.
â€¨"turtle" -- can retreat into a high-defense "shell" mode.
â€¨"swarmer" -- monsters follow just outside of the player's light radius â€¨until there's a small gang, then swarm in.
â€¨"slug" -- leaves a slime trail when frightened, which slows the player â€¨down.
â€¨"nest-breeder" -- Two can combine to make a nest, which acts as a â€¨Gauntlet-like monster generator until bashed.
â€¨"chess moves" -- e.g. straight/diagonal limitations or things like the â€¨knight-jump.
â€¨"territorial" -- tends to patrol its "territory" and bully monsters â€¨within it.
â€¨"charmer" -- will try to charm nearby monsters and amass an army.
sphynx -- asks a riddle, and attacks if the wrong answer is given
lurker -- like the D&D monster: hidden floor/wall/chest variant of mimic/ambusher
doppelganger -- touch attack to take on the target's basic stats
stegosaurus -- "porcupine" type attack if hit from behind (or use turn direction if implemented)
"Mario" turtle -- next attack while in "shell" mode sends it careening (into other monsters?)
wuss -- if encountered alone, flees and returns with next monster it meets
mephit -- can emerge from torches, braziers, etc. or escape back into them
water nymph -- same, but with fountains/pools
alchemist -- gathers potions and recombines them, explodes occasionally

## Gelatinous Mutant Coconut

"grid-bug crab" -- have movement costs for the monster be greater in the y direction than the x direction.
"realistic crab" -- have movement costs for the monster be greater in a direction parallel to its target than in directions perpendicular to its target. (If your roguelike implements facing and turning costs, obviously use those instead.)
"porcupine" -- can retreat into a zero-movement mode; melee attacks on the monster reactively damage the attacker while in this mode.
"Grue-like" -- this monster hates light. If the player has no light source, it closes in on the player to attack. If the player has a light source, it flees away from the player (at a faster speed) until it is outside of the light radius. A smart player might be able to trap the monster in a corner and kill it while it is helpless.
May be made more general by having the substance (blood, poison, confusion potion, etc) and condition (IS_FRIGHTENED, !IS_HUNGRY, IS_LIT, etc) given as parameters.
"nest-maker" -- like "nest-breeder", but only needs one to make a nest (a fertile ant queen, for instance).
"fusing" -- like "nest-breeder", but the resulting creature is mobile.
"evolving" -- like "nest-maker", but the resulting creature is mobile.

## Radomir Dopleralski / The Sheep

I think the most basic monster behavior that changes the rules of â€¨game when that monster appears on the level is collecting items. There â€¨are three variants: item-eater, which destroys the items that it picks up, â€¨single-item-carrier, which picks up a single item and drops it on death â€¨(or when it finds a better item), and item-collector, which collects many â€¨items and drops them all when dead. You can add variety by making the â€¨monsters only target some kinds of items (food, gold, magical items).
Examples:
â€¨ rust monster that eats all metal weapons and armor it finds
â€¨ leprechaun that picks up all gold it finds
â€¨ gelatinous cube/amoeba that swallows all items and expells them upon â€¨ death
â€¨ ants that pick up single items and carry them through all the dungeon
A variation on the topic is a monster that becomes somehow different when â€¨it finds specific item (and maybe reverts back when the item is â€¨destroyed). A goblin might turn into a goblin warrior after finding â€¨a weapon -- and it doesn't have to be simulated by it actually equipping â€¨the weapon. The item might also make the monster weaker: in Rogue orc â€¨becomes immobile after finding gold, for example.
Another common theme is special reaction to certain attacks. This gets â€¨interesting if you allow the player character to throw potions at â€¨monsters. For example, as someone mentioned on #rgrd recently, silencing â€¨should work as blinding on bats and bat-like creatures, while blinding or â€¨invisibility might not affect them at all.
Generally speaking, status-altering attacks are a source of great variety â€¨in the game, as they let monsters interact with each other through the â€¨player character. The status may include poisoning, blinding, immobilizing â€¨(lasting until the monster is defeated or as paralysis for a certain â€¨number of turns), teleporting, confusing, disarming, weakening, etc. all â€¨of which will change the relative strengths of other monsters around.

Hard-coded riddles will get old pretty quickly. You might want to generate quiz questions based on qualities randomized each game, so the answer to "Is goblin blood green or blue?" or "Is the Throne Room of the Minotaur Kings found in the Gnomish Mines or the Kobold Warrens?" would change from game to game, rewarding players for exploring and paying attention. (I think that the possible answers (maybe 2 to 4?) should be presented in the question, because then even if the player hasn't explored enough to know the answer, they might learn something new from the encounter, for example, that the Throne Room of the Minotaur Kings will be in either the Gnomish Mines or the Kobold Warrens.)
Related: "The Bridgekeeper" -- Asks a question (or three); a dangerous trap is triggered if the wrong answer is given.
"transmuter" -- collects metal items. When it collects enough, it destroys those items and creates a random new item composed of a more valuable metal; the total value of the new item should be less than the total value of all the items destroyed.
"The Armorsmith" -- collects and converts non-armor metal items into random armors made from the same metal
"The Weaponsmith" -- collects and converts non-weapon metal items into random weapons made from the same metal

If they are non-hostile, you can buy the newly forged items from them; if they are hostile, they may use the forged items against you, or give them to other hostile monsters to use against you. (Even if they are non-hostile, they might sell these items to monsters that are hostile to you, so even if you are a paladin you might decide to buy that Vile Unholy Greataxe of Paladin Butchering +9 in order to keep it out of the wrong hands.)

## Billy Bissette

There is another variation possible for some more magical/special creatures. Not just item damage/destruction, but outright item changing.
Azure Dreams had a monster that appeared on only one dungeon level â€¨that was effectively a living magical bag. It attacked like other â€¨monsters, but if you threw an item at it, that item would disappear â€¨and it would throw a random item to you in return. This behavior â€¨was meant to be abused to make building a winnable character easier. â€¨Indeed, you could go as far as to make one a pet, and thus have â€¨item transformation available on every level in trade for not being â€¨able to take a more effective attack animal in that spot.

```text
While not necessarily pure random transformation, you could have â€¨monsters that pick up one item and transform it into a different â€¨(but not necessarily inferior) item, even perhaps dropping said â€¨new item back to the ground.  Maybe a rock eater would turn metal â€¨objects into small piles of the related metal.  It eats a gold â€¨crown and leaves a few gold nugget "crumbs" on the ground (or â€¨simplify and just leave "coins".)
```

A fire elemental might burn up some objects on the ground, but â€¨maybe others could be transformed. To steal and slightly modify â€¨a couple of ideas from Tobal 2's quest mode, bad meat might be â€¨safely cooked if a flame creature briefly walks over it, but â€¨cooked meat will be burnt to a crisp and destroyed. Potions â€¨might be transformed as well. In Tobal 2's Quest mode, holding â€¨a potion inside a fire would cause its color to lighten and â€¨thus transform it into a new potion type. Expose it too long â€¨though and it would evaporate, and thus be destroyed. (Tobal â€¨randomly reassigned potion effects on every dungeon entrance and â€¨town visit, so this was sometimes quite useful and sometimes â€¨quite useless. The mechanics of the game also made it take â€¨serious effort to abuse, though it was likely expected for â€¨serious players to take as much advantage of it as they could.)

```text
A gremlin or certain demons might curse objects, while a â€¨non-violent angel might bless them.  Temporarily or permanently, â€¨depending on what it means to your game system.
```

Tinkerer/Craftsman -- collects broken objects and fixes them (might need some raw materials in addition to the object)
Another item-related behavior would be collectors with short attention spans. Such monsters would pick up objects freely, but discard them when they lose interest. Losing interest could be after performing an action, or it could be upon finding a new object.
For example, the craftsman above might lose interest in an object once he has fixed it, simply discarding it onto the ground. On the other hand, a giant crow might pick up one object only to discard it the moment that it finds a shinier object.
You could make a difficult or dangerous to kill enemy such a collector, so that if it picks up an object that the player wants, the player's best method of dealing with it would be to drop something else in its path that would command its interest.

## pdpl

Facehugger: rushes to you to infect you with something.
â€¨Screecher: mostly harmless, makes a huge noise if it's hurt, â€¨attracting more monsters. Can be deadly in the same room as several â€¨other monsters, since it'll mostly shut down your aoe (if your game â€¨implements any).
â€¨eggs: monster-on-a-timer, there might or might not be a mother nearby. â€¨unseen: your average invisible monster, turns visible when it attacks â€¨you, and stays visible for a turn after that.
â€¨guerilla fighter: ranged attacker, plays hit and run. Problematic if â€¨your PCs are mostly melee fighters.

## E. Liddell

I'm surprised that no one has mentioned the obvious "ambusher" yet (monster hides/pretends to be something else, then springs a surprise attack on the player when he steps on it/tries to manipulate it/ whatever). Potentially interesting variant: the monster pretends to be an NPC or other harmless creature.
For a more complex interaction between monsters, consider the cultivator:
â€¨a more intelligent monster that "plants" less intelligent sessile â€¨monsters either around its lair/treasure stash for protection, or in a â€¨location where it can drive or lure the player into them. A variation on â€¨that would be a monster that uses traps for similar purposes. Nothing â€¨like chasing after a monster that moves in 2-square jumps every second â€¨turn, and being dropped into a pit trap in a square that it skipped â€¨over...
Timothy Pruett

- note -- All the monster's can take into account allied strength, which is factored into their own when comparing against a percieved enemy.

Typical -- Attack percieved enemies with percieved strength lower than or equal to own. When wounded to a certain point, attempt retreat. If retreat is impossible, attempt to attack with full force (berzerk).
Scavenger -- Avoid enemies with percieved strength within certain percent of own, but follow within certain distance. Attack enemies if they are weakened. If valuables/food is found, take it. If wounded lightly, retreat.
Coward -- Run from any enemy, no matter what. Only fight back when cornered.
Patrol -- Patrol specified area, until enemy presence is detected.
When enemy is detected, attack. Other than the patroling part, the rest of this module is identical to the Typical module.
Special -- Specially scripted AI for unique enemies, bosses, and townsfolk.
Auric

Insane -- Always attack the enemy, no matter how strong it is, and fight until death. (Standard hack-n-slash game AI)
Coward 2 -- Hide from the enemy, and later attack from the enemy's blind side. (D&D thief)
Long-range -- Attempt to keep the enemy from getting close, while attacking with ranged attacks.
Trapper -- lays a trap and waits for the enemy to walk into it, only entering actual combat if the enemy isn't killed by the trap.
Experienced -- fights with good tactics: if wounded to a certain point, will use healing magic/potions on self; fights cautiously but not cowardly, if outmatched, attempts to bargain its way out; etc.

- flanking maneuvers: goblins attack you from both sides!
- ambushes: a buncha goblins jump down from a ledge, landing on your head!
- false retreats: (variation on ambush) the goblins run away! oh, wait, they just led you to another dozen goblins...
- overwhelming numbers: you are attacked by a hundred goblins! - hit-n-run tactics: the goblins hit you for X damage! the goblins run away! [later] the goblins return!
- relief combatants: you hit goblin A for X damage! goblin A retreats! you are attacked by goblin B! goblin A drinks a healing potion!

## R. Dan Henry

Daredevil -- Defends if attacked or allies attacked, but only initiates combat if forces are "close enough" to equal. Represents groups that fight to prove courage; suicidal attacks are still stupid, but an attack with a strong advantage shows no heroism, so is pointless.
Greedy -- Largely ignores target strength. Attacks if target appears wealthy (including obvious magic goodies, like flaming sword).
Fanatic -- Ignores others unless they break a religious taboo (so each Fanatic type needs AI modified for different taboos), then makes berserk attacks.

Looks like as good a place as any for my "Stupid NPC Tricks" list:

- melee
  - lethal attack
  - non-lethal attack (warning, to capture, torture)
- attack at range (offensive spells/missiles/breath weapons)
- seek favored terrain
- seek food
  - seeking prepared food
  - hunting
- seek water
- seek mate
- seek equipment (weapon, armor, wand, herbs, parts, etc.)
  - by salvage
  - by trade/purchase
  - by killing and looting
  - by sneak theft
- seek money
- seek ally, hireling, or employment
- command other NPC (or PC) or receive command
  - issue order
  - issue request
  - offer paid job
- just stand there
- perform a religious rite
- sleep
- transmit information
  - shout "Thief!" or sound other alarm
  - inform of locations of items of interest
  - spread PC reputation
  - spread lies (try to start fights)
- follow you (up close or at a distance)
- follow another NPC
- own items
- inherit items
- eat/drink
- conduct business (NPC-to-NPC trade as well as PC/NPC trade)
- digging (dig walls, dig a pit)
  - may be done to search for wealth
- run away
- hide/seek cover
- open, close, lock, unlock, break down, or jam a door
- build, bait, set or disarm traps
- pick up/drop/destroy/create/enchant/modify/repair/move items
- equip/unequip items
- cook food
- cast spells
- use psychic power
- train
  - learn spell
  - learn/increase skill
- mimic others (visually, aurally, maybe even mimic scent)
- cooperative group behaviors (tactical action)
- seek/avoid light
- use items
- commit suicide
- heal self or another
- guard a creature, a location, or an item
- tend crops (planting, watering, harvesting)
- tend animals (provide food, water, guard, herd, slaughter, sheer)
- explore location
- make a noise (song, speech, roar, belch, etc.)
- mate and/or reproduce
- summon
- die
  - commit suicide
- flying creatures can take off/land
- take prisoners/hostages
- threaten/kill hostages
- change emotions/opinions
- give/accept gifts

## Kusigrosz

I think monster-monster interaction is definitely a good idea.
It is not easy to get right though; on a large map it could leave parts of the level in a 'strange' state. OTOH, if properly balanced, this could be part of the fun - maybe even worth running the level without the PC for some turns immediately after it is created, to allow some interesting situations to develop.
The Aging Minotaur

...For instance, the game features a kind of giant moth ("night swarmer") that is never hostile to anything, instead moving completely at random and performing an attack on whatever comes in its way. Crossing a cave with these beasts is a trying task, but you can also lead hostile monsters into such a flock and retreat to a safe distance. There are also groups of adventurers, who are neutral to the pc, but will attack certain monster types. OTOH, these bands tend to clog passages the pc needs to get through, or use area attacks that can harm the player if s/he gets in the way (or even their comrades, causing in-fighting, which is amusing in itself, and tends to leave some fair equipment lying around when the dust has settled). In a game where the player can only move orthogonally, there are a few monsters that move along the diagonals. Snakes became an interesting addition: they're only allowed to move diagonally, wants to attack everything, and retreats from enemies standing orthogonally adjacent to them. This makes them difficult to hit, but usually easy to avoid. If you are being chased by some brutish melee monster, however, a snake pit can turn out to be either a useful dungeon feature or a deadly trap.
...
The idea now is to generate random cultures for two species at the beginning of the game. One is loosely based on Swift's lilliputs, and one on Breughel's dancing skeletons. The customs will be "molecular" kits that can be patched onto the data objects used to create these critters and their environs. A lot will be cosmetic, or have only minor effects. Details pertaining to archetecture and inventory lists is one example. In one game, a species might live in small, connected rooms and favor books for treasure, in another they may prefer to build houses in larger caves and keep mummies in crypts[1]. Other quirks will set or rearrange switches in the state machine of species/individuals. Here you've got local and global taboos ("don't eat in the temple", "don't harm <species X>"), paired up with reprimands (dislike, attack, give penal quest, verbally abuse, etc.) You can also get stuff that's almost purely for show (for instance a quirk to make a species musical, adding instruments to their treasure lists, and making them susceptible to passing the time by playing when they're at ease).
The possibilies are many, of course. You can have herders (keeping flocks of animals, either behind fences or as pets in the wild), hunters (wanting to kill and collect the corpse of certain species), farmers (have gardens), etc. I'm aiming for a nonsensical setting, so the plan is to combine cultural quirks pretty much at random and hope for interesting effects. The game play value I hope to gain, is to make exploration of the game world fresh from game to game. In lots of RLs, you pretty soon learn how the setting is put together, eg. that you have "kobolds" and "kobold archers". Instead, I let some of the moving parts vary from game to game. For instance, a species might always contain a subset of <archers>, but they may be rock throwers in one game and bombadiers in the next.

## Inu

Try taking some cues from games of a different genre, as some games have AI with interesting behavior.

The Descent series had several fine examples of AI, such as the Thief Bot and several sneaky-stalker bots. The Thief Bot did the usual stealing of player weapons and running (typically during fights), but out of sight it would also horde the level items in a "den" it selects somewhere in the level. The stalker bots, just stayed at a safe distance while peeking around corners making sounds alerting its presence and changing its viewing location (out of player sight). Whenever a player tried paying any attention to it, such as looking at it, it would shyly hide behind the corner again and if the player tried getting close or chase it, it would run away to stalk again. It would do this until the oppertunity arises to rush the player in a berserk swarm of missiles. I've read a player's story about him trying to do paranoid sweeps of levels in order to kill these things as they would usually be the last monsters standing, taking a rather long time to find and kill.

- "hoarder" - Grabs items off the ground and places them into a "den" and tries to avoid player contact where possible
- "thieving hoarder" - similar to the above, but also steals items and only avoids player contact when it has an item in hand (either stolen from the player or off the ground)
- "hive hoarder" - similar to hoarder but does so with a group
- "thieving hive hoarder" - similar to thieving hoarder but does so with a group
- "stalker" - Stalks the player, runs when approached.
- "fetcher" - Uses magic to fetch a player to the monsters position.
- "gravity" - Uses magic to pull the player to the monsters position. It makes the player take a step towards the monster, globally within a level, even if there is a wall in the way, causing the player to get stuck. Does so every X turns, and does so less frequently the farther the player is from the monster or when the monster is tired. May take 5 turns of gravitation with 10 turns of rest (depending on the monster's strength). "You hear a strange sucking sound off in the distance. You are pulled." (Inspired by Suika's move set in Touhou 7.5.)
- "migration" - Similar to gravity, but uses magic to direct the player's action to move towards the monsters position. The player moves according to some movement algorithm such as A\* to the monsters position and the player must manage obstacles (jumping over lava, water, traps... unless the trap will help the player escape the level... player is forced to open doors or will be forced to open them... with disastrous results). "Your feet take you somewhere special. You see a door. Your feet try taking you somewhere special. You stub your toe against the door. Your feet take you somewhere special. You are slammed into the door. The door breaks."
- "puppet master" - Uses magic within its LoS to direct the players actions and movements. Not necessarily a good/bad thing as the puppet master will just place the player into "auto" mode and will try to keep the player alive as long as possible for the sole reason that it's not often that an interesting toy comes to the dungeon. May come with a fairly numerous set of puppets that are dropped to imbue life into them and become lifeless when not in the LoS or presence of the puppet master or when knocked out (and can be picked up again if not destroyed) and will aid the player as long as there are no hostile acts towards the puppet master. (Inspired by Alice of the Touhou series, and similar archetypes... Also drones in Gearhead...)
- "aura" - creates an aura around the monster which affects all those around it
- "LoS aura" - creates an aura which affects all those the monster sees
- "reverse LoS aura" - creates an aura which affects all those that see the monster
- "level aura" - creates an aura around the monster which affects all those in the level
- "Calculator" - Casts spells without cost by using a simple rule set: Pick a stat. Pick 2, 3, 5, 7 for a multiple check. Pick a spell. Nuke everything on the level matching the rules with the chosen spell(including self, player, monsters). (Inspired by Final Fantasy Tactics class of the same name.)

## Sources

- [r.g.r.d: "Some suggestions/help for my monster AI"](http://groups.google.com/group/rec.games.roguelike.development/browse_thread/thread/325073abe1a7f54)
- [r.g.r.d: "Open call for monster behaviors"](http://groups.google.com/group/rec.games.roguelike.development/browse_thread/thread/d9ab39a252fea946)
- [r.g.r.d: "Emergent puzzles to beat the grind"](http://groups.google.com/group/rec.games.roguelike.development/browse_thread/thread/335f9f406320e91b)
- [r.g.r.d: "Brainstorming: Stupid NPC tricks"](http://groups.google.com/group/rec.games.roguelike.development/browse_thread/thread/b243cbb086cdd73a)
- [r.g.r.d: "Quick wins for enhancing monster AI"](http://groups.google.com/group/rec.games.roguelike.development/browse_thread/thread/ecaa8856e4689bf6)
