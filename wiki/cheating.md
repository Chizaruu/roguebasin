# Cheating

---

An exploit is an action taken by the [player](player.md) to avoid negative effects or to milk positive effects by taking advantage of quirks, oversights, or outright [bugs](bug.md). Some exploits are clearly cheating, while others cannot fairly be called anything worse than "cheesy" or "abusive". Anything the creator of the game has deliberately introduced into or allowed to stay in the game is part of the rules as intended and does not constitute cheating, no matter how ill-considered that rule might be. Some players, however, pride themselves on not using any tactics they regard as exploiting misfeatures of the game.

## Level scumming

---

**Level scumming** is an exploit which occurs in games featuring [non-persistent dungeons](dungeon_persistence.md). The exploiting player climbs and descends [stairs](stairs.md) repeatedly (generating a new [level](level.md) each time) until they come upon conditions which are satisfying. Such actions can slow the game down considerably if the levels are generated on the fly.

This exploit is even more abusive in a game that features [level feelings](level_feelings.md).

### Countermeasures

- When using non-persistent dungeons, remove the staircase from which the player just arrived. This technique is used in [Rogue](rogue.md).
- Use only one dungeon level.

## Pillar dancing

**Pillar dancing** is the act of moving around a piece of unwalkable [terrain](terrain.md), preferably a 1x1 'pillar' of [rock](rock.md), such that an opponent or group of opponents is forced to chase you round and round, so that they (hopefully) never get a chance to actually attack.

Since in many [roguelikes](what_a_roguelike_is.md) resources like [hit points](hit_points.md) and [mana](mana.md) recharge slowly over time, the idea is to delay an attack until the character has hit points to survive at least one attack and/or enough mana to cast one spell. (If the opponent is still alive at this point, one generally returns to pillar dancing.)

Pillar dancing is only an issue with turn-based roguelikes.

### Countermeasures

- Have the monsters move at different speeds from the player. Fast monsters are invulnerable to pillar dancing.
- Introduce a stamina system that requires the player to rest after a certain amount of activity.
- Make all patches of unreachable terrain large.
- Change your regeneration system so that things only regenerate when they're not doing anything.

## Stair dancing

**Stair dancing** is similar to [pillar dancing](pillar_dancing.md), in that the player uses the stairs to evade monsters in the dungeon. In games with [persistent](dungeon_persistence.md) levels, the [PC](player_character.md) can be fighting an enemy, become badly wounded, then run up the nearest stairs. Since in most roguelikes, the [PC](player_character.md) is the only entity that can use the stairs, the enemy cannot follow. The [PC](player_character.md) then can heal and return to battle the injured enemy. This process can be repeated until the enemy is dead. The effect on game balance is that a monster that would normally be very dangerous for the player, is not very dangerous at all when encountered near stairs. In some games, such as [Dungeon Crawl](linleys_dungeon_crawl.md), monsters will follow the player up stairs, preventing stair dancing. In a game with an effective [clock](clock.md), having to retreat to heal up is its own punishment, as either time or some other resource must be spent in order to again face the opponent.

### Countermeasures

- Have monsters engaging in combat with the player follow him/her up and down stairs.
- Use only one dungeon level.

## Save scumming

**Save scumming** is an exploit which occurs in games that let the user save the game for later play. If a player can retrieve the saved file through some means, then he can use it as a form of checkpoint to return to after dying, thus evading permadeath. The ethics of save scumming and the methods to prevent them are a controversial topic in the roguelike community. The term scumming is derived from the process of repeated removing the "scum," or surface debris, on a liquid in order to obtain a purified substance.

### Counter-measures

- Do not let the player have control of the saved games (have the player connect to a games server, for example).

### Ethics

Unless you require the player to play on a server you control, it is pretty much impossible to prevent save file abuse, so it is widely considered a waste of time to spend much effort on technological solutions. There is, however, a less coercive alternative: persuasion. A good, clear explanation of why you actually die when killed in the game ought to be in the basic game manual. Instead of trying to prevent a replacement savefile from being used, why not just detect it and issue a warning that doing so for reasons other than a game crash/power failure is cheating? If someone wants to cheat, you aren't going to stop them, but you can try to make them actually think about and decide if they really want to.

Someone who chooses to cheat at a single-player game is doing no harm, except perhaps to themselves, so as long as you don't post a victory post with a character who used save file abuse or add it to a shared high score list, nobody much cares, except for the sake of the cheater. Generally speaking, lessons stick better when they have consequences. You become a better RL player by making mistakes, dying, and not repeating the mistake. Cheating your way around the consequences slows learning. On the other hand, in addition to playing technique, there is a learning curve for what items and monsters you encounter in a game and some have said that save file abusing their way to a first "victory" in order to preview the late game was helpful in eventually achieving a real win.

## Farming

Farming is the repetitive killing of monsters which present little or no challenge to the player character for the purpose of earning [experience](experience.md) and winning [treasure](treasure.md) (at no risk).

Farming is widely considered to be abusive play as it upsets the game balance if players can get [items](items.md) and experience for free.

Games with [non-persistent](dungeon_persistence.md) levels or games that feature self-reproducing monsters are most susceptible to farming. Perhaps the quintessential example is worm-farming in [Angband](angband.md). Angband features a category of monsters known as "worm masses" which will divide to create more worms. A high level character surrounded by worms can level up ad infinitum by placing a small heavy object on one of the arrow keys of his keyboard, causing the [PC](player_character.md) to attack the masses continually, and come back several hours later to find his character leveled up. Similar issues appear in [NetHack](nethack.md), the most well-known being the pudding.

### Countermeasures

- Grant little or no experience when the level difference between the PC and a monster is larger than some threshold.
- Grant experience only for the first x monsters of one type that a player kills.
- Detect farming behavior directly and punish the player through divine retribution.
- Disallow infinitely reproducing monsters.
- Set a limit for the number of "generations" a reproducing monster can divide.
- Don't use a conventional [level](level.md)-based system.
- Don't allow experience or treasure drops from monsters created by monster-breeding, cloning of monsters by the player, or summoning spells/traps.
