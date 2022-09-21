# Alternatives to Permadeath

---

Original compilation by [Kornel Kisielewicz](../../../developer/kornel_kisielewicz.md) of the thread on [rgrd](../../development/communities/rgrd.md)  

## Original post by [SZDev](../../../developer/szdev.md)

---

Hello All  

I am thinking on removing permadeath from CvRL and replacing with something 'softer'...  

So I thought about adding 'save points', scattered though the world randomly, so you can save there and your character would be restored there upon death.  

This would however ruin the challenge unless some penalties are decided, I have though on these:  

* Saving requires gold, sacrificing an artifact or another valuable
* Saving can only be done x times
* Saving costs player levels or skills
* Reviving strips the player off his gold, and artifact or another valuables
* Reviving costs player levels or skills
* Save places are located far away from critical places, so you cant just save before a boss battle or something. kind of far located checkpoints.

Any thoughts on this?  

Thanks!  

## [Crypt](../../../developer/crypt.md)

---

Prey has an interesting response to this question. The character never really dies. When the character dies he's thrown in afterworld and the player must fight to regain health (and mana) in a limited time. He's progressively sucked back to the physical world. Then he only have the health and mana he regains in the afterworld. Simple but efficient.  

## Kari "Grandi" Rantanen

---

In my opinion, removing the permadeath isn't a good idea. It's more thrilling as there's no way back after death.  

## Keith H Duggar

---

The relative suckage of cost systems depends heavily on the game design. Basically the more renewable a resource is or if it is recoverable then charging that resource on death does not suck and can provide incentive to be careful. For example, gold is often a renewable resource costing only game time and therefore I usually don't mind paying some gold as a death penalty. Conversely, if a game has ultra rare items and I lose one upon death it SUCKS and I don't find it fun. (It is ultimately my goal to have fun when I play. On the other hand, if that item reappears in a temple somewhere and I can buy it back later, then I don't mind. This can have the positive effect of encouraging one to try new items if the lost item is very expensive to recover.  

Also, I like "death plane" systems like Crypt mentioned. Especially if the death plane has interesting and beneficial quests or activities you can do there. Then, even though death may be something you really want to avoid in most circumstances, it can also be beneficial to occasionally die. "Planescape Torment" did not have a death plane but when you died you had to start back at the city morgue and occasionally you "remembered" parts of your past lives and regained powers you once had. Thus death was actually part of the advancement system in that game.  

Finally, "resurrection sickness" systems where the players stats are reduced (perhaps greatly) but recover over time also work well for me.  

## benn

---

One way to limit the usefulness of "explore new features without fear" is to make powerful, unique artifacts (if you are planning any in your game - think Phial of Galadrial in Angband) that can only be found once. If they are found while exploring and then the player restores from a save point after dying or committing suicide or going really badly and giving up then the artifact is gone forever and CANNOT be found again. If players want the artifact then they will need to play carefully to get back to the save point with the artifact which is far away from the critical place where the artifact was found in order to save the game again.  

## [Gamer_2k4](../../../developer/gamer_2k4.md)

---

Roguelikes aren't supposed to be easy. As long as your game is beatable, keep permadeath in there.  

Also, IIRC, CvRL doesn't have a dungeon structure like most roguelikes. This may make some of my suggestions irrelevant.  

Anyway, here are some of my suggestions:  

1. Allow the character to be "rescued" after he dies. The idea is that town patrols watch the first 10 levels or so of a dungeon, and if they see an adventurer left for dead, they can recover and revive him. Of course, the player loses all of their equipment, but at least he can keep playing.
2. Allow the player to buy a "fairy in a bottle" or something. When the player dies, the fairy revives him. Along the same lines, you could have the ever popular amulet of life saving. Obviously both of these would cost a *lot*.
3. In your game, you could have a checkpoint to the start of each stage. Dying would return you to the start of that stage.  

## Giorgos

---

Do you remember how in old console RPGs (and even newer ones) everyone seemed to complain about the lack of free-save and the presence of save-points, save-portals, save-runes, save-crystals, etc.?  

I think that this kind of saving is not liked by many gamers (myself included). It requires careful planning and makes the player want to routinely do the same thing - for example, if a save-point is far from a boss, this means that each time the player loads the game, they must cross the same way. It gets boring after a while. And punishing savers with gold/item sacrifices sounds too cruel :-D  

In my RL I use what I call "permasave": save is automatically done when changing locations (=autosave), death doesn't delete it, only 1 slot is allowed though. In other words, the world is disk-persistent, except the current level.  

* For example, if a save-point is far from a boss, this means that each time the player loads the game, they must cross the same way.
* Not necessarily, in a randomly-generated roguelike... Almafeta 23:25, 6 Jan 2007 (CET)  

## Corremn

---

Money value to be resurrected/restored etc might work. Losing items sucks.  

What I like - How about when you die you lose experience or a level?? Dying will be a pain in the butt so the player will do anything to avoid it. The player just has to kill a few more creatures to get back to where they were. ( Maybe reduce exp to the start of the players current level so you don't have to strip him of abilities - this will be easier to program).  

Or have the player drop every thing he/she is carrying where they died and have to fight their way back to their equipment. (kind of like diablo).  

Copx's warp rogue used a set number of fate points - when you died you lost one of them, when you had none left - game over. Players may be able to gain lives like in arcade games.  

If you add saving make sure you have an advanced mode where permadeath is still there. This gives the experienced players something to aim for. Otherwise you may find people finish the game and never play it again because it is no longer a challenge to finish. many games have a beginner difficulty where you can save any time, a normal version with limited saves and a hardcore difficulty with no saves. Roguelikes are about overcoming a great challenge to feel self rewarded, don't take this away from your players.  

## Gerry Quinn

---

How about the game decides? It saves where it wants, more or less arbitrarily, but ideally when you hit a new level. When you die, it goes back a step or two and loads a save, throws away some of your stuff (and possibly compensates partially by giving you some new stuff), and lets you carry on, re-rolling as much as it can in regions you haven't explored (so new levels get a complete re-roll).  

## Risto Saarelma

---

Console-type save points actually serve two purposes. Besides circumventing permadeath, they also serve as a suspend mechanism, as consoles don't tend to have enough storage space to suspend game state at an arbitrary point.  

Saving the game is already done well in roguelikes. You suspend the game to a file, then resume it. Only with roguelikes you can resume the game only once from that specific save.  

So a save point in a roguelike would actually be some sort of time-rewinding mechanic. Which might be made more interesting than just popping up at the save point. Few games have done this explicitly, Prince of Persia: The Sands of Time and its sequels are the only ones I can think of at the moment. A RL based on the same mechanic might be interesting. The player could manually rewind time for some duration, even after having died.  

(I thought about game logic that can be run backwards as well as forwards and the player being able to act when time is running backwards, but the inverted causality would make things quite weird. It wouldn't make sense to cast a fireball, for example, since it will explode after being cast and no such explosion occurred in the immediate future which the PC just travelled past. But maybe some sufficiently abstract system could make something out of this.)  

But the simple save-restore rewinding is just returning to an earlier game state. This brings with it all the usual problems in a roguelike, the player will now know what his unidentified items are, what lurks in the deeper levels and so forth. Free multiple loading of saves would pretty much destroy the object identification mechanic, and you might just as well tell what each object does when the player picks it up.  

The other mechanic is resurrection. The character is brought back from the dead in the world where it has died, not somewhere in its past. Many commercial games do this: Diablo, the Ultima games, Grand Theft Auto. All of these return the dead player to some town-equivalent, generally some distance away from the more dangerous areas. There is also some penalty. Money is lost, and the character might drop its equipment where it dies. Experience loss is also possible. Penalties in easily regained resources like money and experience are probably better than in equipment, which the player might have worked very hard to get.  

The interesting thing with the resurrection mechanic is that even though it does away with permadeath, you can still do irrevocable things. For example Diablo 2 does not have multiple loading of saves. So if you start messing with the item transmutation box, you might lose your artifact plate armor for good. It also leads to some stupid play, defeating the second boss with a sorceress was several iterations of "cast town portal nearby", "blast the boss so its health drops a little more" and "get killed, resurrect in town, walk through portal, quickly grab your gear from your corpse, cast town portal nearby..."  

In a game that's pretty close to interactive fiction, like the Ultima games, or in a sandbox game like Grand Theft Auto, the resurrection mechanic works well. But in a game with a more tactical focus like Diablo or Neverwinter Nights, it can dilute the game into tacticsless hacking and slashing at everything until something manages to kill you (at least it does when I'm playing). Unfortunately, most roguelikes fall to the latter category.  

Still another approach might be loosening the identification between the player and the player character. If the player character, instead of the player's avatar in the game world, is just hired help, a possessed minion or some similar pawn, the character's death would just mean going to the local tavern with a big bag of gold to hire the next henchman or drifting for a while in limbo waiting for the next unwitting human to try reciting aloud from The Big Dark Book of General Unwholesomeness.  

## zicher

---

I like the idea of temples being a sort of save point. You pay the priest some hefty fee and the next time your character dies you re-spawn at the nearest known temple. So, the player has the option to 'buy' save points or they can perform quests for certain priests and perhaps earn a free resurrect. I'd go with the Diablo model where if you die, all your gold on hand and gear stays with your dead body (for possible retrieval.) To give the players a chance after death, you could have a bank account so they can buy some gear from a merchant or perhaps a stash where you keep your backup equipment.  

You could even have temples inside of a dungeon complex if it is sufficiently large. For more variety, you could have temple aligned with good, evil, or individual gods in order to offer some variety on where you respawn. An interesting role playing element might be that your point of respawn is based on where you pray last. So, this would allow for the selecting of town temples, road side shrines, or mysterious altars as your respawn point.  

I like this method since it allows for save points that stay within the theme of the game and they are technically optional since the player choose whether to use them or not.  

## Julian Mensch

---

Hmmm. I think the core point you have to remember is that there are two kinds of roguelike players: those that like the genre for having permadeath and find it emotionally satisfying to repeat playing a game hundreds of times until they are skilled enough to win it, and those who gain more satisfaction from steady progress and gain no fulfillment from starting a game from the beginning again and again. These seem, IMO, to be fairly distinct groups.  

Trying to find the middle ground will just annoy both groups. The purists want permadeath, and the RPGers want easy, hassle-free saving. Nobody wants to trek through a repetitious sequence or scum for gold to enable saves. The purists will find they "have to" use the saving as intelligently as possible to be the smart players they strive to be, and will end up just "farming saves" and trying to minimize the amount of time between them to improve their tactics. The RPGers will just ignore your heavily restricted save system and copy the save-game files like usual.  

Your best bet, IMO, is to build for permadeath and then not try to work against the save-scummers -- then everybody gets what they want from the game.  

Ironically, in Incursion, I'm going against this rule a bit myself. The basic theory is that if you're in good standing with your deity, he can resurrect you if you die, but (A) some of the gods don't do this, while others do it only at varying levels of favor, (B) you lose an experience level as well as a point of Constitution when you get raised, and move back to the start of the dungeon (or the local temple, when I get wilderness levels done), (C) you need to attain a certain minimum character level before being raised and (D) you have a resurrection survival chance that is nearly certain the first time, and grows worse and worse with each death -- so it's only "certain" the first time.  

The reason I'm doing this is because while right now Incursion is a very short game (in terms of gameplay hours from beginning to win), it will eventually be a lot longer, with possible *months* of gameplay like an RPG -- and no one wants to play, say, Neverwinter Nights over 100 times from the start. The system above ensures that you still have the "tactical danger" permadeath usually entails, but one stupid mistake doesn't kill months of play. The minimum level ensures that you're playing with intelligent habits /before/ you get the save, and the penalties are harsh enough that you never /want/ to die, but if you do the frustration isn't inhuman. Also, the Incursion system is, in being true to it's d20 roots, a set of rules where sometimes you can have immaculate tactics and still die as the result of the computer rolling several criticals in a row -- and every combat with creatures near your CR is at least a little dangerous by design. I think limited resurrection is a better design in this regard than allowing 95% of fights to become essentially risk-free, which is true of a lot of current RLs once the character gets a kit and "finds their feet".  

The guiding design intent here is that you don't get permadead from a single mistake, but a string of mistakes indicative of an actual tactical misjudgement in the longer term will rub you out for good without any recourse, and after an abortive resurrection or two, you'll stay dead.  

Also, I'm considering letting uniques kill you for good after the first round of the fight has passed, making them more dangerous.  

The RPGers, of course, can just save-scum as usual, and the hardcore permadeath types can pledge to Asherath, the god who doesn't raise the dead.  

Feel free to steal as much of this design as you like, if it appeals to you.  

## GameGuy

---

I personally think a good samaritan system works best, have a "dead" player be taken back to a hospital/inn, with 20-50% of his money taken for treatment of his wounds. Also, maybe not fully healed or in a bad state (hungry or angry, confused, etc.).  

## vins

---

Or you can make a system where when your health goes below 0, you fall uncounsious and you have 1-10% of chance to be left for dead by the creature, and after a long time, to wake up.  

## [Scautura](../../../developer/scautura.md)

---

In a world where death is common (think cyberpunk/BladeRunner gone wrong), circumventing death also becomes common. Permadeath is still an issue: For those who cannot pay to be cloned and imprinted, or for those who are too cheap to pay for good service (not going to be walking around in a clone who's been harvested for parts, are you?), but this also adds the capability for more complications. Don't get yourself imprinted recently, and you go back to an old "memory" if you end up brain dead on the scene. Maybe your clone is kidnapped and sent to kill you by your arch enemy. Maybe you end up with "freezer burn" and have memory loss, physical issues, or even psychological issues. Buy cheap, get cheap.  

There is still the option for permadeath - if you don't pay for your life insurance, you're dead. You also won't have any of your old equipment, but because money remains digital (tied to a fingerprint or retina scan - another possibilty for failure in cloning) you keep that - minus a small fee, of course. Time to buy yourself some new gear and head back out into the nasty world again.  

Note that [Shiren the Wanderer](http://www.wikipedia.org/wiki/Shiren_the_Wanderer) works in a similar way - if you die, you're reincarnated back at the original town with no equipment or experience, but there's a warehouse you can store items in. So if you find a useful shield/sword/item of any variety, then you bring it back to the original town (note: You lose all experience but no items when you do this) and store it for future use. There are a couple of warehouses on the way through as well, and a cart driver who will take you to later towns as well (but you need to have gone sufficiently further than the town you want to go to before)  

In a way, this means you have a "shared memory" and "shared storage" with previous characters, and as far as you can tell there is no permadeath (you're still playing the "same character") but it's still permadeath to some degree - you have to start from scratch, but you can choose your starting equipment.  

OK, so Shiren is a little "rogue-lite" in places, but it's still a very enjoyable game.  

## Pokemon Mystery Dungeon

---

Player death in the Pokemon Mystery Dungeon series tries to encompass the multiplayer aspect of the main series Pokemon games. When the player is knocked out in a dungeon they have the option to return to the main hub or wait for rescue by another player. Returning to the main hub will result in a loss of money and items held by the player at the time of defeat. If the player chooses to wait for rescue an SOS mail code is generated. This code must be given to a friend who will input the code into their copy of the game and proceed through the dungeon to the level where the original player was knocked out. When the friend has rescued the downed player an A-OK mail code is generated. Once the waiting player inputs this code they may continue through the dungeon from the level they were defeated. The waiting player can cancel waiting for the A-OK code but will suffer the losses of returning to the main hub.  

The SOS mail code is an encryption of the pokemon to be rescued, the pokemon's name, the dungeon and level they were defeated, the game file ID, and the number of attempts to be rescued. The A-OK mail is generated using the SOS mail code. Therefore a defeated player requires a unique A-OK code to be revived.  

This method works well for Pokemon Mystery Dungeon since each copy of the game may only contain one save file, but it is exploitable. Online code generators can produce SOS and A-OK codes, along with other event codes used in game.  

## Other things to consider

---

It's important to remember that if you're designing a roguelike, you need to design what your players want to play and not what you want to code. While in a group dedicated to developing roguelikes (or even a group dedicated to PLAYING roguelikes) the majority of people will tell you how much they love the challenge of permadeath, but it's often a HUGE hurdle to new players. Besides, despite what people tell you it's one of the least-important aspects of design anyways - if you create a permadeath game, people will savescum, and if you create a game without permadeath, nobody is forcing people to reload their same characters after they die. Or better yet, why not have an option that lets the player turn it on and off? The real underlying problem is the design of the difficulty curve to match the save system you're using - if there's permadeath, it's easy to keep things artificially "challenging" by just increasing enemy statistics and ability levels as the player continues to grow. If, however, you keep permadeath as just an option for player preference, you can greatly increase the challenge of the game without making it feel too 'cheap' to the player, which could engender the formation of strategies and tactics to deal with threats other than engraving E and sitting around until you're healed or running away.  

As for my own game, I'm not including permadeath at all, even though it's basically a roguelike. It's meant to be a fun, light game, and I know that the audience for that type of thing doesn't want to lose hours of progress because an enemy they didn't see yet shot them in the face from a corridor somewhere. To ensure that there's still a high level of difficulty, however, I'm implementing the following save rules:  

* There are 3 save slots that can be accessed for a fee (in gold, based on player level + equipment) on the overworld. When it's used, the entire dungeon is repopulated with monsters (though item/room locations remain static) so the player can't just clear one level after another and continue to save. It's meant to save character progression, not the exact state of the game.
* There is a single quicksave slot. When used to save the game, the game exits out. When it's loaded, the quicksave file gets deleted or overwritten. I'm not going to be doing any fancy encryption magic to ensure it can only be used once or anything (savescummers will savescum, after all), but I am going to limit when you can save by only allowing a quicksave when no enemies are visible and no enemies on the current level know of the player's location. That way, the player can save and leave the game almost whenever Real Life (the other RL) calls, but can't just quick hit F5 and backup the save file whenever they see an Ultra Demon Uber Lich or some monster that they're not sure they can handle.  

## Zireael

---

(Note: not part of the [rgrd](../../development/communities/rgrd.md) discussion)  

Another way we could have an alternative to permadeath is by allowing the player to switch characters. Imagine having a second-in-command that gets promoted upon your original character's death. Alternately you might have a way for your character to create a clone or a child of itself.
