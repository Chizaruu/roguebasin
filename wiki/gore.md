# Gore

---

## Horror in Roguelike Games, Part 1 : Gore

---

Gore as a [theme](themes.md) for a roguelike game is not impossible. Although the best effect would be achieved using graphics (for most of the elements of gore are purely visual) the "feeling" can be also achieved by the proper use of standard [ASCII](ascii.md).

Oh, check out an RPG system called Violence. That's pure gore...

## Introduction

---

So you want to make a game with elements of gore, huh? Well, go to your local Video Renting place and rent a bunch of "horror" movies. Watch them all night, and I grant you, you will be able to think gore in the morning. What gore is, I already described in the previous article. Today I'll talk about some details of implementation. But still one has to remember that gore is mainly a visual theme, so if you use plain [ASCII](ascii.md), implementation might be tricky.

## Setting

---

The gore theme is not suitable for all settings of roguelikes. It goes the best with modern or near-future games with supernatural beings. Of course, you can do a medieval gore game, but it would be trickier. You can also do a science-fiction gore game, where instead of zombies and living-dead you have aliens that spill thier entrails all around. The alien theme is quite good actually, if you approach it with Geigeresque weirdness -- concentrate on biological things then: on eggs, on alien-mothers, and the like. Use Aliens II as a reference (The Alien series isn't pure gore though).

Forget about skill systems, forget about realistic combat. It's only the bloodshed and carnage that counts. Choose a setting that allows you maximum combat with small risk. The character is supposed to face tons of enemies, and emerge victorious out of a pool of blood/[acid](acid.md) and a pile of bones/organs/brains/whatever... (Or die and lose, of course. One classic addition is to have your nigh-invulnerable hero not only fighting the monsters, but trying to protect weaker innocents as well. Your odds of survival could be very good, but the game can still challenge you to keep that little girl and the basket of kittens alive.)

Systems with guns work especially well, because of the carnage you can do with modern weaponry. Let the player carry around as much weaponry as he likes.

The descriptions in combat should be bloody and full of disgusting details -- you should be able to sever the enemies' limbs, cut off or cleave heads, cut them in half, make them explode and the like. You don't need a detailed limb system though, the descriptions will be enough. Remember about splattered brains, hearts, heads when creating descriptions and adding visual elements.

## Enemies

---

You have to decide on a proper enemy for your player. The more the merrier. Decide on some cannon fodder enemies -- let it be zombies, skeletons, or other living dead in a modern or ancient setting, biologically-reanimated dead people and animals in a near-future setting, or aliens in a science-fiction setting. The rule is that there should be plenty of them, and they should be relatively easy to kill. The player should be attacked by hordes, and I mean HORDES of them.

You also need bosses -- the archenemies you encounter from time to time. They need to be tough. Also it's nice to surround them with cannon fodder or, even better, let them spawn cannon fodder. In a medieval setting it might be a daemon. In a modern setting, a master-brain... or a living evil Heart of the Dungeon. In an alien setting, an alien-queen. It's always nice to make the enemies as weird and disgusting as possible.

Enemies need to have names (or descriptions) that make them weird and disgusting... Like the Living Brain-Heart of Darkness, a Brain-Eater, Skull Daemon, Animated Rotting Corpse, Living Flesh Wall... or whatever. Hellish creature names also work well. Futuristic possibilities: Berserk Cleaning Droid, Belly-Ripper or other variant of Alien's "chest-burster", Cancerous Mutant, Cloned Serial Killer, Parasitic Slime.

In a modern game with guns, it's nice to make the player paranoid about the creatures getting too close -- make hand-to-hand combat deadly for the player. Put up a warning message when the character is starting to get "low on ammunition" (which could be something like 50 or 100 rounds in some games), to encourage the player to become paranoid about ammunition at that point.

A good idea for gore enemies are floating/crawling/walking body parts like flying brains, four-legged hearts, crawling hands and the like.

Add enemies that can be only destroyed in certain ways, such as only with a direct hit to the heart, or can only be damaged by holy water or fire. For example, you can add zombies that stand back up after a while, unless killed by fire or unless you cut off their heads. Add semi-immortal bosses, like a creature you cannot kill unless you destroy its heart which is on a pedestal somewhere in the old mansion. Finding the vulnerability in the first place can be a minigame, although if you make it too big a part of the game, you lean away from gore and into mystery.

In futuristic settings, a great idea is to connect flesh to machinery, creating half-living monsters. I loved Doom for that. Just remind yourself of the Cyberdaemons...

## Blood and Body Parts

---

Include lots of these. Make normal enemies leave at least a red dot on the floor when they die -- archenemies should splatter and leave lots of their interior around. You can even make a modern game, where the player has no guns, and must use fallen zombie body parts as weapons. Leave body parts on the floor as items.

Blood is also important. Track the amount of blood on the floor and serve the player with a description from time to time: "You are knee deep in blood.", "Your movement is slowed, the blood is very thick.", make him swim in blood. The more the better ;). Crunching bone and bits of flesh sticking to things also make for repeatable and common gore elements.

As a twist for futuristic games, you can make some monsters (e.g. aliens) spill green [acid](acid.md) instead of blood. Make the player have problems with that acid -- it causes damage (if you don't have good boots), damages or destroys armor when you kill an alien hand-to-hand, and creates pits in the ground when acid melts through a metal floor. If you are in a space ship, acid might eat through the hull, creating a hull breech, with all the fun that implies.

## Weapons

---

This depends on the setting, but is really important in the gore genre.

Medieval or Quasi-Medieval -- Use swords, axes, halberds, the bigger the better. Also crossbows and bows allow ranged weapons, as do thrown weapons. If you go the spiritual way, add blessed weapons, holy symbols and holy water. You can also use torches (a great idea), and "burning oil" (it isn't historically accurate for Europe, but who the hell cares? The Muslim world did have fire weapons at a medieval stage due to good chemists and available petrochemicals). A lot more can be introduced with [magic](magic.md). Create special "holy" [artifacts](artifact.md) with gore names (like "Skullcrusher", "Brainsmasher" and the like), add some disgusting effects to them when used (like an acid-spitting crossbow, a chainsword-like blade [or more period-appropriately, some "ripper" blade with lots of teeth], vorpal blades (snicker-snack!) if you want to be both cliched and insert humor here). Another way to add atmosphere with weapons is to add a good history to their description, explaining, for example, "The Virgin's Revenge was blessed with demon-slaying power when Saint Bernice, crawling on broken legs, took this blade from a fallen knight and drove it into the demon that had ravished and crippled her. Now it burns the flesh of any unholy thing it touches." A whole new world opens with the introduction of [spells](spell.md)... remember to add infernal spells with gory effects like "Boiling the Blood", "Body Implosion", "Sign of Blood"... Also things like "Sanctuary" come in handy. Some spell should go well with the blood ideas -- the enemies should die in an effective display of bloody fireworks. Fire spells are also important. Combining these ideas, a spell that set spilled blood on fire could be very handy. A spell that makes flesh "run like wax" would be appropriately gross and could have nifty side-effects.

Modern -- You can either go for high-tech, low-tech or both. High-tech would be handguns, rifles, and (yes!) shotguns! You may also add automatic rifles, and bazookas (just imagine the tons of enemies being splattered around). All kinds of explosives are also welcome -- explosions produce a lot of gore, and grenades are really good for blowing things up. For me the double-barreled shotgun is the thing... (ever played Doom II?). Low-tech modern weapons would include tools (shovels, hatchets, jackhammers, drilling devices, staple guns, hot glue guns, soldering irons, power screwdrivers and the like -- they are very gore producing), especially chainsaws (oh, yes) or any power saw with any safety features disabled. When implementing power tools remember that such things are loud, and they produce really disgusting effects. Don't make the chainsaw just another weapon. Remind the player from time to time that he's using a chainsaw "The chainsaw vibrates in your hands... Vroom!!!". Also add some descriptions of the feeling the player has for his weapon ("Vroom!! The chainsaw demands blood!"). Add fuel -- it makes the player value his weapon. If you want something less used than the chainsaw, provide power lawn mowers and lawn trimmers. You can also go fully low-tech -- pipes, baseball bats, planks with nails and the like.

Don't forget that machinery can explode (let cars blow up like it was a movie), catch fire, spew sparks, emit steam, etc.

Science-Fiction -- Use futuristic versions of weapons mentioned above and add some new interesting devices like vibro-axes, living bullets, acid sprayers and flamers. Yes, the last one is really important (also in modern tech). Burning is the second-best thing after blowing up (remember Aliens II?). Also add some gore-related cyber-tech like claws mounted in the hand, or a built-in chainsaw. The bigger and more Frankenstein-like the better.

## Scenery

---

Scenery also depends on the setting. In modern and older settings, you would probably use old mansions, deserted cities (no one left living :), dungeons, caves, sewers, old factories, monasteries, and castles. Fighting in a large mall is also a classic for modern settings. If you use a lot of magic, then the action later in the game can travel beyond the natural world to places like Hell, the Inferno, Pandemonium, the Abyss, and the like. Make those places really hellish, though. There shouldn't just be more of the same or even just tougher monsters. Make the environment itself dangerous and/or creepy. Don't wait for killing to produce blood -- include a river of the stuff. The floor of one place could be the faces of the damned and when you move, sometimes you get messages ("'Ouch!'" "'Watch where you're stepping!'" "'It wasn't my fault! Save me!'" "As you step here, you hear a crunch of a breaking nose.")

In a more advanced world you can use high-tech factories (which produce living-dead, for example), and living half-machine complexes. Like modern factories, these can have molten metal, crushing machinery, industrial lasers, robotic arms, heavy machinery, but can also have anti-gravity devices, holograms, teleporters, and such.

In science fiction a nice way to progress is to first guide the player through massacred human complexes, then bring him on alien bases and ships. Aliens can have bio-technology, so doors, or even whole spaceships may be against you (computerized equipment can also bear a grudge in science fiction). A nice boss for a science fiction alien game is to destroy the literal Heart of the alien spaceship. Biotech ships should be drippy, with walls that ooze organic fluids when you use heavier weapons and messages should include noises and smells ("The engine pod gurgles like a hungry stomach.")

## Humor

---

Yes, there may be place for humor in the gore genre. The first type is the humor of weaponry -- those chainsaws may produce weird messages, and the named weapons may have really funny gore-invoking names. Not to mention the character may need to use some pretty goofy items as weapons -- a hot iron in a laundry room battle, exploding an outhouse (natural gases) to burn that zombie, etc. Blood, blood everywhere. The second type is dark sarcasm. Ironic messages, or out-of-place remarks by [NPCs](npc.md) or "player thoughts". 'Well, he was dead anyway'. 'Oh, a normal humanoid! Two hands, two legs, a head and a torso. Bang! Two hands, two legs, a head... no torso. Damn' those modern shotguns!' The sort of thing made popular by James Bond, but now required for every action movie, in other words.

## Plot

---

You need a supernatural (or exotically scientific) and weird/disgusting story as a background for your game. You also need a known (or unknown?) archenemy that is disgusting, huge, and menacing. It's nice to add stories about an end of the world if the evil isn't stopped. A prophetic character, either supernatural or scientific would be good to deliver the doom-laden warning.

For medieval themes, the main plot elements would be undead or infernal creatures. You may have the mission to kill the Devil (or more plausibly, defeat and banish him), or the Plague Lord. It may be also smaller in scale -- kill the Apostle of Disease or the Plague Bearer. It is also possible to send aliens into a medieval setting and produce a clash with cultural expectations. The player would be able to discover the truth about the "demons" with his modern perspective -- although this may bring in more complexity than you want and does mean you'll need some non-gore elements where clues are found. It is also possible to do a purely historical (although cinematic) setting with nasty disease, human cruelty, and superstitious ignorance driving things. Humans killing humans (and beasts) produces plenty of gore and can be quite horrific. In fact, if done well, this has the danger of being horrific enough to lose you some players.

For modern times you just need an evil spirit/being that infested the local sewers/old mansion/factory. An insane computer, mad scientist, or exotic radiation/micro-organism also make excellent plot devices. Or you could take the "attacking animals" route and have rats, birds, house cats, cattle, or some other animals going berserk for unknown reasons and have to either kill them all or kill lots of them and also figure out what to do to put a stop to their strange behavior.

In science fiction it would be either an alien base/ship, or an invasion of evil. A lab accident could have loosed a virus that makes victims try to bite others (spreading the disease) before it kills them over a period of weeks, during which they exhibit ugly symptoms you can describe to the player. Unethical government experiments, especially ones that have gotten out of hand, are also a handy excuse for any sort of mayhem, plus they allow a source of dark humor.

Cut your plot idea into episodes, and make the episodes visible to the player. Give him a feeling of accomplishment. But remember to keep things open for a sequel. It isn't really horror unless the closing shot suggests that some remnant of the threat survives.

One of the examples that I mostly like is Doom.

Episode I: Knee Deep in Dead -- you land with your co-soldiers on Phobos, one of the moons of Mars. There have been strange experiments going on about interdimensional portals. You are assigned to guard the ship. After landing, after a while the radio goes silent. You take your trusted gun and enter the base alone...

Episode II: The Shores of Hell -- after clearing the base you get transported by the portal to the lost Deimos Base. There you meet the Cyberdemon!

Episode III: Inferno! -- after you kill the bad guy on Deimos it turns out that the base is floating over Hell itself! You take on further carnage and finally stand against the lord of the hellish invasion himself -- the Arch-Vile brain-on-spider-like Spider Mastermind.

## Conclusion

---

I think that a gore-themed roguelike game isn't especially hard to achieve, and despite of the lack in story, it may be really fun to play. I still enjoy playing Doom from time to time... I'm looking forward to see a Gore RL.

As always I'm open to comments, suggestions, ideas, and death threats.

## Links

---

- [Kornel Kisielewicz](kornel_kisielewicz.md)
- [DoomRL](doomrl.md)
- [Berserk!](berserk.md)
- [Horror in Roguelike Games](horror_in_roguelike_games.md)
