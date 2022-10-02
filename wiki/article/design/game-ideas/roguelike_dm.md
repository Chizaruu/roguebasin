# Roguelike DM

---

(by [Timothy Pruett](../../../developer/timothy_pruett.md), compiled by [Kornel Kisielewicz](../../../developer/kornel_kisielewicz.md))  

No, the DM does not stand for Dungeon Master in this case. What I'm talking about, is Deathmatch. Like pretty much every RL developer does at some point in time, I again started thinking about multiplayer RLs. And like most developers, I again quickly reached the conclusion that, in order to make it multiplayer, you would either have to sacrifice some of the elements that define the genre, or sacrifice playability, due to the obnoxiousness of online turn-based gameplay.  

Then, I thought there might be a way. The obnoxiousness of having to wait for another player to move is still there, but it's not so bad: Deathmatches. They're (typically) short, and require a great deal of thought and planning, like a chess game, so the pain of waiting for your foe to move isn't so painful. Like any compromise in a system, however, the idea of RL Deathmatches is not without its potential pitfalls.  

Firstly, different players may have different ideas of what an appropriate playing speed might be. And while the short DM battle may alleviate this pain, someone who makes a move once every couple seconds will get awfully frustrated with someone who moves once every minute. Easy solution to this problem, though. Allow the host to set the timeout length at the start of the game, so that everyone knows what they're getting into ahead of time, and they can choose an appropriately paced game.  

Secondly, of course, you run into the problem of gross imbalances in the gameplay, allowing unfair advantages, and encouraging everyone to play one or two particular classes. If, for example, ADoM had a DM mode, nobody would ever bother playing, say, a Weaponsmith, because a Wizard of equal level would kick their ass every time, regardless of equipment. This is also *somewhat* easily remedied. By using a point-based system, you can encourage experimentation and a variety of interesting character builds.  

By point-based system, I mean that every single thing that makes that character what he is, is assigned a point value. Level, class, race, stats, equipment, skills, abilities, etc, all have point values. Obviously, you then have the trouble of having to balance the point values, but this is a *far* easier task than balancing the actual game.  

For example, suppose that a level 5 Fighter has a point value of 6000, a level 10 Fighter has a value of 10000, and a level 20 Fighter has a value of 15000. In this case, I'm assuming that the Fighter's power gradually levels out, like it does in most games. Strong at low levels, but not as much so in the late game. A level 5 Wizard, on the other hand, might have a value of 1000, a level 10 Wizard valued at 7000, and a level 20 Wizard valued at 30000, since, in most RLs, wizards become more powerful as the game goes on, but are frail at the beginning.  

Please note that the point values could be *very* precisely balanced with a bit of work. You could program some reasonably intelligent bots, and, using empirical research, gather enough data on everything to determine precisely how powerful it really is.  

I think this has great potential. Every player would have a chance, since they would only play against people of roughly equal point values (unless they intentionally wanted an extra challenge). Now, the Weaponsmith might have a chance on the Wizard, since the Weaponsmith is able to play at a higher level, with higher stats, and better equipment.  

An interesting possibility arises from this system. Now, players could just DM with their single player characters, and be reasonably assured of a fair, balanced fight. However, due to the point based system, it would also be possible for player's to build their characters before the battle, with a predetermined number of points to spend. That would, in my opinion, be very fun.  

Now, another potential problem (and a nasty one too) is, of course, cheating. Cheating in a single player game is easy if all the data is client side, so a player could cheat his way into some godly character, and unfairly beat a lot of people who built their characters the fair way. Luckily, because of the point values, the unfairness wouldn't be too bad (a cheat character with ungodly stats and equipment would have an equally ungodly point value), but, if one player had to earn their character, they won't have all the options available that the cheater had, and the cheater could easily create a better character.  

One solution would be to force people to play the single player game through a central server, thus making cheating much more difficult. However, this is an extremely unappealing option, and should be avoided. Nobody should have to connect to the internet to play a single player game.  

A better solution is present already: allow players to build their PCs before battle, with a predetermined number of points to spend. This works, but, I'd still rather see players have the right to choose whether to play a DM with single player characters or built characters.  

Unfortunately, I can't think of any feasible way to completely prevent cheating on single player games. But, if the DM mode is always played through a central server, than you can log all accusations of cheating, as well as IP addresses and usernames. If conclusive evidence of cheating does show up, problem solved: ban them. If they are cheating but you can't prove it, well, once word gets out that KeWlDoOd69 cheats, nobody will want to play with him anyway.  

Well, I rambled on far longer than I intended. I do, however, think that this is a good idea (even if not very original), and I know that I myself would have a blast playing such a  

When I wrote that, I had, in my mind, envisioned it as being 1v1 deathmatches. Now that I think about it though, there's no reason why there couldn't be additional modes, like massive Free For Alls, Team Deathmatch, Capture the Flag (or Orb, or Dragon Egg, or whatever), and even Coop levels.  

The Coop levels are just single dungeon floors, designed to be *very* hard (obviously scaled in difficulty depending on the combined point value of the players, number of players, etc.). The only way to win would be through teamwork and cooperation. "Every man for himself" attitudes get everyone killed.  

Anyway, there are a variety of similar modes possible that would be great fun.  

Ideas and suggestions are, as always, welcome.  

---

**Christopher Brandt**  

I like the idea very much and I'm looking forward to include it in my own RL (called DungeonHunter), though there are some minor problems changing my code to multiplayer. Anyway, it'd be lots of fun. What do you think, should there also be permadeath in DM (the name would indicate that)? If yes, there should be a strict anti-cheating system, if not, there could be some kind of Tournament or ranking list for single chars (and accounts?).  

The coop-idea is cool, but the arena has great potential too: I'm thinking of some events triggering monsters into the arena, arena shops, fans, traps and much more. Also there could be team battles (and tag-team battles) and even clan-fights. In any case, something like that would not only be a great chance to compare characters, but would also be a place where players can exchange there thoughts about the game and roguelikes in general in some kind of chat, but I might be going to far with this.  

**zicher**  

I like the shot clock (or chess clock if you prefer) idea. If a player misses their time interval, the player can default to a rest/regenerate/defend behavior. I'd also allow a spectator mode for dead players that still want to follow the adventure in the case of team games or coop dungeon party. Perhaps there is a fixed delay time before the player can re-spawn, if allowed.  

The point based system is a rock solid idea. If you're conerned about single player cheats, you could add a character pointer checker on the server side that calculates the point total of the character instead of trusting the point total given by the player. That might be a good idea anyway if you want the 'server' to assign a penalty spell casters, etc.  

**Michal Brzozowski**  

I was thinking about a good timing system for a game for two players, where most of the time the players don't see each other. With this assumption, you can give them seperate timing and synchronize it only when they are in contact. It's sort of a discrete relativity theory in roguelikes :).  

When I'm about to make a move, the game calculates that the minimal number of moves from each side to make contact is large enough to let me do that move without waiting for the other player. Of course there can be no teleportation or level teleportation on multilevel games. In one level games, the field of view has to be quite small too, to take any advantage.  

When the players can see each other, the game falls back to your favorite simple timing routine.  

Imagine what happens when you let two players into a big dungeon from two opposite sides and they get too close to each other, and one of them was playing much more slowly.  

```text
"Waiting for the other player to do 12312 moves."
"12311"
"12310"
"12309"
....
```
