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

## DESIGN ISSUES

### Do you have to design a roguelike before you program it?

Typically, roguelikes are written without much design or planning. Once the programming is complete, you decide what you can do with your program (e.g. what items/monsters/spells you can support) and go ahead and make those. Some people believe this is still the way to do things.

Others believe differently. If you ever do a course in computer programming at school, college or university, the first thing they teach you is how to make an algorithm, how to plan your program, and how to design before you start programming. For some kinds of commercial games, the designers make a 300 page design brief before the artists or the programmers even start work! Games that rely intensively on story usually have a complete play script written before the design brief. Of course, most roguelikes are free, and nowhere near this amount of work is invested in them, so this amount of design is ridiculous. But note that most roguelikes in development have a design document on their web pages.

I believe that planning is essential to any program, particularly a big one like a roguelike. Before you start programming (at least, programming anything big or particular), make a list of things you want in the game. What kinds of items do you want? Spells? Monsters? How will your game world look? You don't have to plan the particulars like the colour of your orcs or the exact number of your spells; just look at general categories (e.g. "some of my monsters will use spells on the player", "there will be plants growing in the wilderness that you can eat" and so on), and then decide how you are going to implement those things.

Often when you start to make a roguelike game, you won't even know what the design issues are. You can spend a lot of sweat trying to figure out in advance how you're going to use each data structure and selecting data structures that efficiently support that use, but then, once you're actually in the middle of it, you'll find things you haven't allowed for, and which you cannot accommodate within your current design. So yes, do spend time planning your program. But be aware that actually coding it and playing it is your only real way of becoming intimately familiar with the design space and the problems your design needs to solve. Once you have this familiarity, you can do a much better job of planning.

### Why do many roguelikes later undergo a major rewrite?

* Poor design. If you planned enough from the beginning, you would cater for everything, or at least most of the things, that you want to implement. Then you might only need a small rewrite.
* Change of maintainer. Most popular roguelikes go through long chains of maintainers and dev teams. As soon as one guy gets the source code, the first thing he does is rewrite it so that it fits his ideas.
* Poor programming techniques. I would say most of the rewrites are there to fix bugs. Some programming languages are better structured and better suited for use for long programs like roguelikes. It has been shown that when your code exceeds 100000 lines, the cost of maintaining it and debugging it exceeds the cost of programming new things. Object Oriented programming, functions and splitting up into files help to reduce this problem.
* The developer has gained knowledge about designing roguelikes from the initial effort and has undertaken a rewrite in order to put it to use.
* When the developer decides to extend the game in an unanticipated way it often requires fundamental redesign. For example, an extension like having spells take effect after some delay may require a complete redesign and rewrite of the scheduling system.

### What needs to be planned in advance?

* The [story](../story.md), [theme](../theme.md) and setting. These determine practically everything.
* The world. How it will look. How big it will be.
* The [terrain](../terrain.md) (e.g. town, wilderness, dungeons, special places...)
* The Scheduling system (eg, how turns and speed work, whether and how actions can be interrupted by other actions, etc).
* The [items](../items.md). Major categories (e.g. weapons, armour, potions, scrolls, food...). Leave detail (e.g. how much the minor healing potion heals you by) for later. Think about how the items will be used and by whom. Plan the basic rules (e.g. you can only wield 2 weapons if you are a fighter, you need space for arrows if you equip a bow, how many rings can someone wear, etc).
* The [magic](../magic.md) system. Spell types. Who uses spells and when. How you learn spells. What they cost. Which classes can use them. How to implement permanent spells (e.g. enchantments that last, like recharging a staff).
* Shopping. If your game will have shops, how will they work? Will the Player be able to rob them? If so how does that work?
* Interaction with other creatures (maybe negotiating your way out of battles?).
* Saving and restoring games Level loading system.
* Win Conditions How to finish the game and what happens when you do.

### What do people like about roguelikes?

Replayability - If the game is [random](../random.md) enough, it is always fun to play and replay, because every time it is like a different game.

Freedom - There is a lot of it. You can kill just about anything. There are a lot of actions you can take. Many strategies.

Complexity - There are a lot of items, skills, classes, areas, spells.

Difficulty - While this can be off-putting, it gives you a tremendous sense of achievement to finish a roguelike or even to get far. You always have to be careful, because it is easy to die.

How do you make people like your roguelike? Make it easy to get into and play; no reading through 50 page files to learn how to play. Keep the controls simple. The gameplay must be good. This generally distinguishes roguelikes from other games - the emphasis is on the gameplay. Once your character dies, that's it - no coming back to life (or maybe you are reincarnated back without items and with reduced skills). This makes the game exciting when you are in danger. Keep it balanced. If it gets too difficult, people are discouraged. If it is too easy, there isn't much fun in it. Make a big world, with plenty of different items and monsters so it is interesting to explore. Make an interesting story. And, most of all, don't make it like some or other roguelike that already exists. Use fresh, new ideas. Make it original and different.

### How do you finish making your roguelike?

Basically, have a plan. Decide in which order to program it, and stick to the plan as much as possible. Keep it fun for yourself. If you get bored, work on another aspect. If you are bored on programming, work on the story, or on the graphics (if you use them). Take a break. It should be fun. If it isn't, ask yourself why, and do something about it. If you are stuck, get other people to help you.
