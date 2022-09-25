# Monster attacks

---

Original by [The Sheep](../../../developer/the_sheep.md)  

It is important for a roguelike to have monsters that are distinct -- they should vary more than health and name. Giving monsters (and the player) diverse attacks is one way to add variety to your roguelike.  

Because of the loose organization of this list and its inclusion of more general "things monsters could do" that are not attacks, such as flying, R. Dan Henry created a more systematic list of [monster attacks in a structured list](../../monster_attacks_in_a_structured_list.md).  

The following is a list of monster attacks and effects.  

## Methods

---

* Hit -- "Bump into the monster and hit them with your weapon".
* Shoot -- Standard ranged attack. The player selects a target or shoots in a given direction.
* Beam or Ray -- Shoot a beam that affects all in its path. Some beams/rays may be reflected.
* Area attacks -- Attacks that affect a certain area. Examples include:
  * Fireballs and other basic spells cause damage over an area.
  * Noxious (or hallucinogenic, blinding) gas that dissipates over time.
  * Earthquake. May stun, damage, or cause debris to fall.
* Disease -- Spreads its effects when the monsters/player are near, or when something diseased is touched, worn, or eaten.
* Play music -- Use an instrument to play music that has some effect on all that hear it.  

Bodily weapons such as:  

* Claws, hooves, horns, tails.
* Quills and spikes (in defense, or shooting them in some way).
* Breath attacks (breathe out poison or a beam of flame).
* Gaze attacks (typically paralyzation or stoning, as with Basilisks or Medusa).  

## Effects

---

Any given effect may be permanent or temporary. There may be an onset period ("you feel your limbs stiffening") where you may act to prevent that effect. You may guard yourself in various ways against certain effects (cold resistance, unchanging).  

### Basic

* Wound -- Create a wound with long-term effect, i.e. bleeding.
* Poison, Sickness -- A stat of the player may decrease (perhaps temporarily).
* Armour-penetrating -- Like a normal attack, but bypasses (non-magical?) armour.
* Stun -- Makes the pc unable to fight for the next turn.
* Paralyze -- The pc can't do anything for several turns.
* Sleep -- Like Paralyze, but it ends when pc is attacked.
* Fear -- The pc becomes afraid and is forced to run.
* Confuse -- The pc moves around randomly, as if drunk.
* Possess -- Possess the player for N turns. The player may automatically attack the enemies of the possessing monster.
* Stumble -- Makes the pc unable to walk for the next turn.
* Slow -- The player moves slower.
* Grab -- The player can't move away from the monster.
* Silence -- The pc can't cast any spoken spells.
* Blind -- The pc becomes blind.
* Amnesia -- Causes amnesia. Parts of the map are erased, spells may be forgotten, items are un-identified.
* Hallucinate -- Monsters appear like other monsters, items are not what they seem.
  * Paint the screen upside down.
* Seduce -- Makes the pc take off his armor.
* Charm -- Makes the pc unable to attack the monster.  

### Special

More severe, or harder to implement effects include:  

* Steal -- The monsters takes an item from pc's inventory.
  * Pickpocket -- The monsters takes some gold from pc's inventory.
  * Steal spell -- Gives the monster a spell known by the player, the player may forget that spell.
* Polymorph -- Turns the player into another creature.
  * Turn the pc into a chicken (or something else) -- he keeps his HP, but can't attack.
  * Mutate -- The pc grows an additional organ that changes his abilities.
* Engulf -- PC is engulfed or swallowed, so it can only fight one creature and cannot move.
  * Digest -- Swallow, and the player may die after several turns of digestion.
* Time warp:
  * Time moves back at the point when the player entered the level.
  * Undoes the target's last N moves.
* Basilisk stare, petrification -- Turns an item/player into stone.
* Condemn -- Puts a timer on the PC. When it expires the PC dies. This timer could either be dispelled or canceled when the monster dies.
* Dispel -- Removes any magical effects from PC.
* Impregnate/Parasite -- Plants an alien embryo or parasite which severely harms or kills the host as it gestates/when it hatches.
* Steal Soul -- Steal the PC's soul, making the gods ignore praying.
* Sacrifice -- If PC stands on an altar, sacrifice. Like in ADOM.
* Annoy -- The creature yells random insults, taunts, etc.
* Suicide, kamikaze -- Kills the monster. Bee stings, exploding monsters, spore-releasing monsters.
* Cooperate -- An attack that needs several monster to act at the same time.
* Throw buddy -- Monster takes a smaller monster and throws it at target. Both projectile and target takes damage.
* Track -- Reveals player location to other monsters.  

### Elemental

* Electric:
  * Metal armor is ignored.
  * Amplified by water.
* Fire:
  * The player, or the player's clothes are set on fire.
  * Items are damaged or destroyed, typically scrolls/books/potions.
  * Corpses/raw meat can be cooked.
* Cold/Ice:
  * The target is frozen in place.
  * Potions may shatter.  

### Attribute-based

* Stat-swapping -- Str/Dex/Int scores are swapped. Health and Mana may be swapped.
* Drain, reduce -- Lowers maximum health or mana, stats, experience, or level.
  * Poison and sickness may lower strength, endurance.
  * Vampiric or wraith attacks may lower level.
  * Leech -- May give these stats to the monster.
* Increase hunger -- Makes the player become hungry, or digest their food faster.
* Prevent regeneration -- Health, or mana, or endurance do not naturally heal or regenerate.
* Disenchant -- Decreases magical bonuses, erases magical scrolls.
  * Items such as potions may be diluted by water attacks.
* Encumber -- Make the player able to carry less.
* Percentile damage -- Half or quarter of health is lost.
  * Health may be reduced to a very low number.  

### Items

* Curse -- Equipment becomes sticky-cursed (undroppable, cannot be un-wielded, etc.).
* Polymorph item -- Turns an item into gold.
* Disarm -- Makes pc drop his weapon.
* Scatter equipment -- Makes PC's equipment (some if not all) to be thrown around.
* Give item -- Makes PC to accept an item (e.g. loadstone, timed explosive or something else not so useful).
* Corrode -- Attack can damage metal armor.
* Acid attack -- Attack can damage any armor.
* Rot -- damages wooden, cloth, or other organic items
* Burn -- destroys scrolls, damages flammable equipment, evaporates potions
* Disenchant -- reduces enchantment of magical equipment  

### Terrain

* Trap -- Creates a trap.
* Darkness -- Makes some of the map dark.
* Light -- Makes some of the map brighter.
* Alter ground -- Turns grass to rock, or poisoned river to clean river, etc.  

### Movement/position

* Move
  * The pc is moved by the force of attack.
  * Wind or a tornado moves/throws the player in some direction.
* Teleport
  * The pc is teleported into random place.
  * Monster teleports away from the pc, or to a square next to the pc.
  * The pc is teleported to a square beside the monster.
  * The pc is teleported to another level.
* Switch places -- Switches positions of pc and monster.
* Whirlwind -- Pick up items from the floor and move them around randomly, possibly hitting creatures in the way.
* Phase out -- Target disappears completely for a short time.  

## Monster abilities

* Mimic -- Makes the monster look/behave/have stats like the pc.
* Hide -- Become difficult to see unless really close (chameleons?).
* Summon -- Summons monsters.
  * Raise dead -- Creates zombies from monster corpses.
* Clone -- Creates a copy of the monster.
  * Divide -- Split into two (distinct from summon/clone in that each half has half the power of the original).
  * Merge -- Form a single stronger monster out of two smaller ones.
* Cry for help -- Makes all the monsters around come at the player.
* Hunt -- The monster finds and attacks some other monster, perhaps healing by killing it.
* Animate -- Raise weapons, armour, rocks, etc. to fight the target. Possibly their weapon, possibly whatever was lying around.
* Fly/levitate -- Avoid terrain, or get out of reach of PC.
