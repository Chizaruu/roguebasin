# Roguelike Dev FAQ

```text
                     #                 #              ##           #
###         ##      ##    ##   ##    ###    ##    ##   #         ###
 ####     ## ##     #      #   ##   ##      ##     #   #  ###   ##
 #  ###  ##    ##  ## ##   #   ## ###       #      #   ####   ###
 # ##    ##   ##  ## #### ##   ## ######   ##      #   ##     #######
 ###      ## ##   ##   ##  ##  ##   ##     ##      #   ####    ###
 #  ##     ###      ####    #####     ##   ###     ##  ## ###    ##
##  ##      ##       ###      ##       #   ######  ##  ##  ##     ##
                                                                   ##
     ##          ###        ###        ###           ##             #
     ####       ##   ##    ##           ######      ###    ###
     ## ##     ##     ##   #            #    ##    ## #   ## ###
     ##  ##  #######  ##  ##            ###       ##  #  ##    #
     ##   ## ###       # ##             #####    #### #  ##   ##
     ## ###     ##     ###              ##      ##   ##   ## ##
     ####        ###   ###              ###    ##     ##   #####
     ##                                               ##       ###
     #                    Roguelike Dev FAQ                     ##
    #                         v. 0.0.3
```

By Damjan Jovanovic and many contributors on Roguebasin

## General

### What is Rogue?

[Rogue](../rogue.md) is a text-based game made in the 1970s that started it all. You explored a dungeon, gathering items, fighting monsters and getting stronger. There was initially no point to this all, but eventually they made it so the main quest was to find the Amulet of Yendor.

What separated Rogue from most similar text-based games of its time was how it handled the output. Most adventure games described the environment (e.g. "You are in a small room, with a passage out behind you"); Rogue "drew" it using text. For instance, if you were standing in the room described above, this is how it would look (the "@" being you):

```text
-----
|...|
|.@.+#####
|...|
|...|
-----
```

Back at the time, PCs were virtually unheard of, and most computers were just dumb terminals connected to a mainframe, with no graphics. Drawing with text was a big novelty. Rogue was incredibly addictive in its time. It was eventually distributed with UNIX.

### What is a Roguelike?

This is a topic that has caused vehement debates in the [rec.games.roguelike.development](../rgrd.md) newsgroup. I will just explain it as simply as possible.

Let's see about the features of rogue:

* Single player
* [Text based](../ascii.md)
* [Randomly](../random.md) generated [dungeon](../dungeon.md) levels
* Turn based (i.e. nothing happens until you press a key that does something)
* The emphasis is on good gameplay rather than good graphics
* [Death is permanent](../permadeath.md). No loading saved games, no coming back to life. Once you die, you can only start from the beginning with a new character.

Firstly, you have to realise that games are not as easy to classify into genres as books or films are. There are a lot of games that don't belong into any one genre. Purists may argue that any game that is graphical and real-time (as opposed to turn-based) cannot be a roguelike, but not everyone agrees.

There is some overlap between roguelikes and role-playing games, adventure games, and so on.

Briefly then, "roguelike" is more of a feeling you get in a game rather than a set of criteria that have to be followed. Just to make things more confusing, here are some more things that can be found in modern roguelikes:

* "Dungeons and dragons" style of skills
* The maker is usually 1 person. The more popular games have "dev teams" which work on them later
* [Magic](../magic.md) systems usually implemented
* Usually set in [fantasy](../fantasy.md) middle ages (exceptions exist; sci-fi roguelikes do exist, see [Theme](../theme.md))

### What are the major roguelikes today?

#### [ADOM](major-roguelikes/adom.md)

Website: [http://www.adom.de/](http://www.adom.de/)
ADOM is Thomas Biskup's one-man effort. Inspired by the D&D universe, ADOM is a fantasy romp with a quaintly humorous mood. It has a handmade overworld map called the Drakalor Chain. It's player's job to track down the source of Chaos overtaking the land. The closer the player gets to the source, the more tainted with Chaos he becomes.

#### [Moria](major-roguelikes/moria.md)

"Mines of Moria" or just "Moria" is around. It is loosely set in Tolkien's mines of Moria in "Lord of the Rings". It is not being developed any more; only "maintained".

#### [Angband](major-roguelikes/angband.md)

Website: [http://rephial.org/](http://rephial.org/)
"Angband", is a newer roguelike based on Moria but even more loosely based on "Lord of the Rings". There are probably more variants to this one than to any other roguelike. The variants often used entirely different monsters and goals; not all of them involve Tolkien's works.

#### [Nethack](major-roguelikes/nethack.md)

Website: [http://www.nethack.org/](http://www.nethack.org/)
NetHack is the modern roguelike which is by far the most similar to the original Rogue. NetHack offers a varied and humorous playing experience; a favourite saying amongst players is that the DevTeam "thinks of everything". The focus of the game is relatively narrow - there's no town or wilderness, just a single dungeon to get down to the bottom of and retrieve the Amulet of Yendor from - however, individual levels are more varied than is typical of many roguelikes. The game is relatively short; it's possible to win in 8 hours of play if you put your mind to it - partly because the dungeon levels are static during the game, so unlike Angband for example, you're forced to move on to unexplored levels deeper in the dungeon.

#### [Dungeon Crawl](major-roguelikes/dungeon_crawl.md)

Website: [http://www.dungeoncrawl.org/](http://www.dungeoncrawl.org/)
Dungeon Crawl Stone Soup: [http://crawl.develz.org/](http://crawl.develz.org/)
Dungeon Crawl is a roguelike with a strong emphasis on the "rogue" part of the genre. It's a game of stealth and cunning, with a strong emphasis on traps and stealth. It's also a game of exploration, with a huge number of different monsters and items to discover. The game is set in a fantasy world, with a strong emphasis on the "dungeon" part of the genre. The game is a single-player game, with no multiplayer or online play.


