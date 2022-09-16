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

### What roguelike websites / newsgroups are there?

rec.games.roguelike.angband
    Discusses Angband and its variants.

rec.games.roguelike.nethack
    Discusses NetHack and its variants.

rec.games.roguelike.adom
    Discusses ADOM.

rec.games.roguelike.misc
    Discusses all roguelikes which don't have a newsgroup of their own yet.

[rec.games.roguelike.development](../rgrd.md)
    Discusses all topics which are related to the development of roguelikes.

rec.games.roguelike.announce
    A moderated group for announcements about roguelikes.

rec.games.roguelike.moria
    Discusses Moria, the parent of Angband. This newsgroup is almost dead.

rec.games.roguelike.rogue
    Discusses Rogue, the original roguelike. This newsgroup is almost dead.

alt.games.omega
    Discusses Omega. This newsgroup is almost dead, but there's a Yahoo Group for discussing Omega's development and maintenance.

### What should I know when first posting to r.g.r.d?

Some places are more newbie-friendly than others. The developers at rec.games.roguelike.development may sometimes show little patience with questions that have been asked many times before. Here are two simple things you can do to ensure that you get off to a good start:

* Do some research before you ask a question. If the answer is available via a search engine, why ask here? If the research does not turn up a satisfactory answer, then by all means ask, and you will be able to ask a better, more focused question as a result of your earlier work. Demonstrate that you have some initiative, and you will get a much more helpful response. As well as searching the web in general, search r.g.r.d.

* Don't ask what the best programming language for a roguelike is, or if a particular language is suitable for roguelike programming. If you want to know why, search r.g.r.d via Google Groups for "best programming language" and you will find many flamewars sparked by that question and its variants. The topic has been done to death, and many developers are tired of it.

## GENERAL DEVELOPMENT QUESTIONS

### How and by whom are roguelikes generally made?

By a selection of people from all over the world, with too much free time on their hands. People looking to learn a programming language, people who play games for a while and end up wanting to make it their way, people who want to make a game by themselves and have no artistic talent (for those using ASCII that is).

Usually by people learning computer programming; originally it was students of computer science or similar courses at college or university. Of course, anyone who wants to can make them.

### Which programming language are roguelikes generally made in?

Traditionally, C, because of its portability and because other programming languages weren't available at the time. Nowadays, a variety: [Java](languages/java.md), [C++](languages/cpp.md), [Pascal](languages/pascal.md), scripting languages etc. The best language for your roguelike is the one you know well.

### Do you have to know programming to make your own roguelike?

Yes. There is no other way. Read books. Read articles and tutorials on the Internet. Look at lots of source code. Most of all, get yourself a compiler and/or set up an environment for your language (most are free) and practice. A lot.

From here on onwards, this FAQ will assume the reader of this FAQ is familiar and fairly proficient with computer programming and related concepts.

### How do you make a variant to a roguelike?

Firstly, a variant is a roguelike based on the source code of another roguelike, with some changes.

Making a variant isn't easy for several reasons. Most roguelike sources that are out there are VERY badly written-- lots of poor programming techniques, hacks (more than 1600 in Angband), lack of comments, difficult to understand variable and function names, and so on. However, making a variant is much easier than starting from the beginning by yourself.

Firstly, make sure that it is allowed for the particular roguelike you are interested in, and that you aren't breaking any law by making this variant. You will most likely need to change the name of the game, to comment every source file you change with something like "Changed by ___ on ___", and log the changes you make in the history file. Employing version control (like Git) will save you the pain of differentiating between existing bugs and bugs introduced by your changes.

Secondly, play the game A LOT. Decide what exactly you would like to change. Make a list of things to change. Then obtain a copy of the source code, and see which compilers will compile it. Get one of those. Compile the game without changing anything. If it doesn't compile, get it to compile before changing anything.

Then go through the source files. Find the section you want to change. Make small changes first, then recompile and see what happens. Then make progressively bigger changes until you have what you wanted.

Variants usually never reach the fame and glory of the original game, but they are a good way to learn about how roguelikes work, and making them is usually easier and faster than starting from the beginning.
