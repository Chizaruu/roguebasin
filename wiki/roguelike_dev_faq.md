# Roguelike Dev FAQ

---

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

---

### What is Rogue?

[Rogue](rogue.md) is a text-based game made in the 1970s that started it all. You explored a dungeon, gathering items, fighting monsters and getting stronger. There was initially no point to this all, but eventually they made it so the main quest was to find the Amulet of Yendor.

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

This is a topic that has caused vehement debates in the [rec.games.roguelike.development](rgrd.md) newsgroup. I will just explain it as simply as possible.

Let's see about the features of rogue:

- Single player
- [Text based](ascii.md)
- [Randomly](random.md) generated [dungeon](dungeon.md) levels
- Turn based (i.e. nothing happens until you press a key that does something)
- The emphasis is on good gameplay rather than good graphics
- [Death is permanent](permadeath.md). No loading saved games, no coming back to life. Once you die, you can only start from the beginning with a new character.

Firstly, you have to realise that games are not as easy to classify into genres as books or films are. There are a lot of games that don't belong into any one genre. Purists may argue that any game that is graphical and real-time (as opposed to turn-based) cannot be a roguelike, but not everyone agrees.

There is some overlap between roguelikes and role-playing games, adventure games, and so on.

Briefly then, "roguelike" is more of a feeling you get in a game rather than a set of criteria that have to be followed. Just to make things more confusing, here are some more things that can be found in modern roguelikes:

- "Dungeons and dragons" style of skills
- The maker is usually 1 person. The more popular games have "dev teams" which work on them later
- [Magic](magic.md) systems usually implemented
- Usually set in [fantasy](fantasy.md) middle ages (exceptions exist; sci-fi roguelikes do exist, see [Theme](themes.md)

### What are the major roguelikes today?

#### [ADOM](adom.md)

Website: <http://www.adom.de/>  
ADOM is Thomas Biskup's one-man effort. Inspired by the D&D universe, ADOM is a fantasy romp with a quaintly humorous mood. It has a handmade overworld map called the Drakalor Chain. It's player's job to track down the source of Chaos overtaking the land. The closer the player gets to the source, the more tainted with Chaos he becomes.

#### [Moria](moria.md)

"Mines of Moria" or just "Moria" is around. It is loosely set in Tolkien's mines of Moria in "Lord of the Rings". It is not being developed any more; only "maintained".

#### [Angband](angband.md)

Website: <http://rephial.org/>  
"Angband", is a newer roguelike based on Moria but even more loosely based on "Lord of the Rings". There are probably more variants to this one than to any other roguelike. The variants often used entirely different monsters and goals; not all of them involve Tolkien's works.

#### [Nethack](nethack.md)

Website: <http://www.nethack.org/>  
NetHack is the modern roguelike which is by far the most similar to the original Rogue. NetHack offers a varied and humorous playing experience; a favourite saying amongst players is that the DevTeam "thinks of everything". The focus of the game is relatively narrow - there's no town or wilderness, just a single dungeon to get down to the bottom of and retrieve the Amulet of Yendor from - however, individual levels are more varied than is typical of many roguelikes. The game is relatively short; it's possible to win in 8 hours of play if you put your mind to it - partly because the dungeon levels are static during the game, so unlike Angband for example, you're forced to move on to unexplored levels deeper in the dungeon.

#### [Dungeon Crawl](linleys_dungeon_crawl.md)

Website: <http://www.dungeoncrawl.org/>  
Dungeon Crawl Stone Soup: <http://crawl.develz.org/>  
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

[rec.games.roguelike.development](rgrd.md)
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

- Do some research before you ask a question. If the answer is available via a search engine, why ask here? If the research does not turn up a satisfactory answer, then by all means ask, and you will be able to ask a better, more focused question as a result of your earlier work. Demonstrate that you have some initiative, and you will get a much more helpful response. As well as searching the web in general, search r.g.r.d.

- Don't ask what the best programming language for a roguelike is, or if a particular language is suitable for roguelike programming. If you want to know why, search r.g.r.d via Google Groups for "best programming language" and you will find many flamewars sparked by that question and its variants. The topic has been done to death, and many developers are tired of it.

## GENERAL DEVELOPMENT QUESTIONS

---

### How and by whom are roguelikes generally made?

By a selection of people from all over the world, with too much free time on their hands. People looking to learn a programming language, people who play games for a while and end up wanting to make it their way, people who want to make a game by themselves and have no artistic talent (for those using ASCII that is).

Usually by people learning computer programming; originally it was students of computer science or similar courses at college or university. Of course, anyone who wants to can make them.

### Which programming language are roguelikes generally made in?

Traditionally, C, because of its portability and because other programming languages weren't available at the time. Nowadays, a variety: [Java](java.md), [C++](cpp.md), [Pascal](pascal.md), scripting languages etc. The best language for your roguelike is the one you know well.

### Do you have to know programming to make your own roguelike?

Yes. There is no other way. Read books. Read articles and tutorials on the Internet. Look at lots of source code. Most of all, get yourself a compiler and/or set up an environment for your language (most are free) and practice. A lot.

From here on onwards, this FAQ will assume the reader of this FAQ is familiar and fairly proficient with computer programming and related concepts.

### How do you make a variant to a roguelike?

Firstly, a variant is a roguelike based on the source code of another roguelike, with some changes.

Making a variant isn't easy for several reasons. Most roguelike sources that are out there are VERY badly written-- lots of poor programming techniques, hacks (more than 1600 in Angband), lack of comments, difficult to understand variable and function names, and so on. However, making a variant is much easier than starting from the beginning by yourself.

Firstly, make sure that it is allowed for the particular roguelike you are interested in, and that you aren't breaking any law by making this variant. You will most likely need to change the name of the game, to comment every source file you change with something like "Changed by **_ on _**", and log the changes you make in the history file. Employing version control (like Git) will save you the pain of differentiating between existing bugs and bugs introduced by your changes.

Secondly, play the game A LOT. Decide what exactly you would like to change. Make a list of things to change. Then obtain a copy of the source code, and see which compilers will compile it. Get one of those. Compile the game without changing anything. If it doesn't compile, get it to compile before changing anything.

Then go through the source files. Find the section you want to change. Make small changes first, then recompile and see what happens. Then make progressively bigger changes until you have what you wanted.

Variants usually never reach the fame and glory of the original game, but they are a good way to learn about how roguelikes work, and making them is usually easier and faster than starting from the beginning.

## DESIGN ISSUES

---

### Do you have to design a roguelike before you program it?

Typically, roguelikes are written without much design or planning. Once the programming is complete, you decide what you can do with your program (e.g. what items/monsters/spells you can support) and go ahead and make those. Some people believe this is still the way to do things.

Others believe differently. If you ever do a course in computer programming at school, college or university, the first thing they teach you is how to make an algorithm, how to plan your program, and how to design before you start programming. For some kinds of commercial games, the designers make a 300 page design brief before the artists or the programmers even start work! Games that rely intensively on story usually have a complete play script written before the design brief. Of course, most roguelikes are free, and nowhere near this amount of work is invested in them, so this amount of design is ridiculous. But note that most roguelikes in development have a design document on their web pages.

I believe that planning is essential to any program, particularly a big one like a roguelike. Before you start programming (at least, programming anything big or particular), make a list of things you want in the game. What kinds of items do you want? Spells? Monsters? How will your game world look? You don't have to plan the particulars like the colour of your orcs or the exact number of your spells; just look at general categories (e.g. "some of my monsters will use spells on the player", "there will be plants growing in the wilderness that you can eat" and so on), and then decide how you are going to implement those things.

Often when you start to make a roguelike game, you won't even know what the design issues are. You can spend a lot of sweat trying to figure out in advance how you're going to use each data structure and selecting data structures that efficiently support that use, but then, once you're actually in the middle of it, you'll find things you haven't allowed for, and which you cannot accommodate within your current design. So yes, do spend time planning your program. But be aware that actually coding it and playing it is your only real way of becoming intimately familiar with the design space and the problems your design needs to solve. Once you have this familiarity, you can do a much better job of planning.

### Why do many roguelikes later undergo a major rewrite?

- Poor design. If you planned enough from the beginning, you would cater for everything, or at least most of the things, that you want to implement. Then you might only need a small rewrite.
- Change of maintainer. Most popular roguelikes go through long chains of maintainers and dev teams. As soon as one guy gets the source code, the first thing he does is rewrite it so that it fits his ideas.
- Poor programming techniques. I would say most of the rewrites are there to fix bugs. Some programming languages are better structured and better suited for use for long programs like roguelikes. It has been shown that when your code exceeds 100000 lines, the cost of maintaining it and debugging it exceeds the cost of programming new things. Object Oriented programming, functions and splitting up into files help to reduce this problem.
- The developer has gained knowledge about designing roguelikes from the initial effort and has undertaken a rewrite in order to put it to use.
- When the developer decides to extend the game in an unanticipated way it often requires fundamental redesign. For example, an extension like having spells take effect after some delay may require a complete redesign and rewrite of the scheduling system.

### What needs to be planned in advance?

- The [story](story.md), [theme](themes.md) and setting. These determine practically everything.
- The world. How it will look. How big it will be.
- The [terrain](terrain.md) (e.g. town, wilderness, dungeons, special places...)
- The Scheduling system (eg, how turns and speed work, whether and how actions can be interrupted by other actions, etc).
- The [items](items.md). Major categories (e.g. weapons, armour, potions, scrolls, food...). Leave detail (e.g. how much the minor healing potion heals you by) for later. Think about how the items will be used and by whom. Plan the basic rules (e.g. you can only wield 2 weapons if you are a fighter, you need space for arrows if you equip a bow, how many rings can someone wear, etc).
- The [magic](magic.md) system. Spell types. Who uses spells and when. How you learn spells. What they cost. Which classes can use them. How to implement permanent spells (e.g. enchantments that last, like recharging a staff).
- Shopping. If your game will have shops, how will they work? Will the Player be able to rob them? If so how does that work?
- Interaction with other creatures (maybe negotiating your way out of battles?).
- Saving and restoring games Level loading system.
- Win Conditions How to finish the game and what happens when you do.

### What do people like about roguelikes?

Replayability - If the game is [random](random.md) enough, it is always fun to play and replay, because every time it is like a different game.

Freedom - There is a lot of it. You can kill just about anything. There are a lot of actions you can take. Many strategies.

Complexity - There are a lot of items, skills, classes, areas, spells.

Difficulty - While this can be off-putting, it gives you a tremendous sense of achievement to finish a roguelike or even to get far. You always have to be careful, because it is easy to die.

How do you make people like your roguelike? Make it easy to get into and play; no reading through 50 page files to learn how to play. Keep the controls simple. The gameplay must be good. This generally distinguishes roguelikes from other games - the emphasis is on the gameplay. Once your character dies, that's it - no coming back to life (or maybe you are reincarnated back without items and with reduced skills). This makes the game exciting when you are in danger. Keep it balanced. If it gets too difficult, people are discouraged. If it is too easy, there isn't much fun in it. Make a big world, with plenty of different items and monsters so it is interesting to explore. Make an interesting story. And, most of all, don't make it like some or other roguelike that already exists. Use fresh, new ideas. Make it original and different.

### How do you finish making your roguelike?

Basically, have a plan. Decide in which order to program it, and stick to the plan as much as possible. Keep it fun for yourself. If you get bored, work on another aspect. If you are bored on programming, work on the story, or on the graphics (if you use them). Take a break. It should be fun. If it isn't, ask yourself why, and do something about it. If you are stuck, get other people to help you.

## OUTPUT AND REPRESENTATION

---

### Do roguelikes have to be done in [ASCII](ascii.md) text?

It depends on preference. [Utumno](utumno.md) is an [Angband](angband.md) variant (according to the Angband variants FAQ, and I doubt the writers of it don't know what a roguelike is), and when people want to argue that things with graphics can't be roguelike, I always quote it as an example of a roguelike with graphics. Now compare Utumno and [Diablo](diablo.md) or Diablo 2. You will notice they are not that different (graphically).

If roguelikes don't need to be in ASCII, why are most of them?

- Text is very portable.
- It's ~~ridiculous~~ traditional. Most people programming roguelikes come from a tradition of playing other roguelikes, and they are done the same way.
- It's fast. A museum style computer can probably show text at the same speed as the latest one.
- It's quick and easy to make. You don't have to have lots of artist friends to draw for you. You don't have to spend hours modeling.
- Letters are very readable, even when they are small - they were designed for just that. They are more readable than (most) graphical tiles, especially isometric ones. Real 3D models successfully compete with letters, but only if the dungeon view can be freely rotated, tilted and zoomed.

### How do you add graphics?

There are several different graphical systems you might consider.

The simplest and most often used method is with square tiles. Basically, instead of filling rows and columns of the screen with text, they are filled with small bitmap pictures. No animation is used, i.e. when you hit a monster, you don't see a swinging sword. You can make an animation system, but it will get difficult. To properly animate a character, you need pictures of every single action from at least 4 different position (facing front, back, right and left) and the sizes of your picture / animation files get big very quickly. To produce smooth animations, you need even more pictures... It is obvious why Angband and NetHack use only front facing pictures with no animation.

The other method uses isometric tiles. These are like square tiles rotated 45 degrees, like they are seen from above and to the side. Just look at Utumno or Diablo to see what I mean. They are harder to program and to make, but they look better. Here you run into an even bigger problem if you want to implement animation - you really need 8 different direction (up, up-right, right, down-right, down, down-left, left and up-left) to face in, and you need every frame of every action in each of those directions. Nevertheless, Utumno, Falcon's eye and Diablo all use this.

Then there is 3D graphics...

### How do you make a 3D roguelike?

There is only one game so far that uses this: [Egoboo](egoboo.md) (<http://egoboo.sourceforge.net>). Even [Diablo](diablo.md) and Diablo 2, although they look 3D, are really only 2D. All the monsters and characters for Diablo 2 were modeled in a 3D package, and snapshots were taken in different positions and at different times to create the animations. So good luck making one of the world's only 3D roguelikes.

But here are some ideas. A good cross-platform 3D library is [OpenGL](opengl.md). You generally program it in C or C++. It is easy to learn, but requires hardware 3D acceleration for good performance. Good free modeling / animation software is difficult to come by. Try Blender and Anim8or.

In a way, designing monsters / characters in 3D is easier than in 2D, because you don't need to draw actions from all directions; only from one, then you can rotate them by any angle you like. Also, you don't need to draw every frame of an animation, just the key frames, and your software calculates the rest.

However, the mathematics of 3D is difficult, and includes linear algebra, vectors, matrices, quarternions, geometry and trigonometry.

### Which libraries let you position the cursor and change text colours?

Most programming languages come with a library of their own. In C and C++, you can use conio.h. This is DOS specific, however, and not portable to other systems.

The [curses libraries](curses_library.md) are a better choice. [Ncurses](ncurses.md) is the standard on [Linux](linux.md), [Mac OS X](mac_os_x.md) and most [UNIX](unix.md)/UNIX-like systems. On [Windows](windows.md) [PDCurses](pdcurses.md) works better than ncurses.

The article [Output libraries](output_libraries.md) discusses this in more detail.

One solution for Java is a [Java Curses Implementation](java_curses_implementation.md).

## STORY AND SETTING

---

### Which stories are possible for a roguelike?

[Moria](moria.md) and [Angband](angband.md) are (loosely) based on J.R.R. Tolkien's "Lord of the rings", but they implement very little story. NetHack has hardly any story at all, mostly just "Your god has sent you on a quest to fetch the Amulet of Yendor", with a little elaboration in the Guidebook.

In general, role playing games are the ones where story is important. For a roguelike, it is nice to have a story, and a good one will keep your player interested and provide more to the game, but it isn't the game's biggest component.

Some unexplored ideas that would be good for a roguelike might be:

- You are trapped in an out-of-control computer, and it's making deadly monsters. The goal is to stay alive and find a way out.

- You are in a post-apocalyptic future (no/limited guns). There is a war between humans and robots. You are on the losing side...

### What [themes](themes.md) and settings are possible for a roguelike?

Ones that involve killing a great number of enemies.

Most roguelikes are set in the middle ages. There are ones in a science fiction setting in the future, but they aren't very popular.

Relatively unexplored themes are:

- The Wild West
- The Far East
- The distant past (stone ages)
- Alternative histories (e.g. Atlantis)
- The modern era
- Science fiction settings

### What is / how do I make a good atmosphere?

Atmosphere, (noun): the psychological environment, the feeling and tone created by something. The atmosphere in roguelikes varies. In Angband it is often dark and desperate, because you're kilometers under the earth with a failing source of light, near death and with no way to escape your foes. In [NetHack](nethack.md), it is quite challenging but somewhat humorous. In [Diablo](diablo.md), it can be very dark and ominous - your cause is basically hopeless.

Another way to create atmosphere is music, although it can be difficult to port. Example roguelikes that use music are [DoomRL](doomrl.md) and [DrashRL](drashrl.md). If the roguelike has a graphical output, the images can be used to create atmosphere. The story also goes a long way towards creating an atmosphere.

Often a change in game mechanics can change the atmosphere substantially. If the character heals swiftly during play, and the game feels like a silly romp no matter what else you've tried, cutting the rate of healing to a tenth or less of what it has been can suddenly make the mood deadly serious and addictively engaging.

### What are quests and how are they different from other forms of plot?

A [quest](quests.md) is a set of actions you have to do in order for something to happen. For instance, once you've completed the quest "kill Morgoth", you've finished Angband. Quests have some sort of reward, or story element with them. Most roguelike games are based on one primary quest which must be completed to win. Although the value of secondary quests is debated, many have additional quests during the game, which players undertake for score, equipment, or sheer style.

### What are the standard types of quests?

- Assassination quests:
  - "Kill X"
  - "Capture X alive"
- Searching quests:
  - "Find X"
  - "Get to X"
  - "Gather X of gold"
- Competition quests:
  - Do any of the above quests, but before your adversary (e.g. get to a town before your adversary)
- Story quests:
  - "Talk to X"
- Any of the above, but they do something special (e.g. there is a story associated with finding a special item)

### How do I make good quests?

Make them relevant and provide a good reason to do them. Integrate them with the story. Give good rewards. Avoid repetition.

Example: Avoid quests like "kill 5 orcs", because they have no reason or emotional connection. It would be better to say the orcs have been killing members of the player's village and the remaining people are pleading for help. Then the player is given a logical reason to kill the orcs and will be more likely to want to complete the quest.

To add to replayability, elements of the quests could be randomised.

## [DUNGEON](dungeon.md) MECHANICS

---

### How are dungeons represented?

This is a question worthy of a long article or maybe even a small book. Nevertheless, you can get the basics from this FAQ.

This FAQ only discusses traditional systems, i.e. simple 2D tile-based dungeons. If you're making something different (e.g. a 3D roguelike) you can still use this, though.

Firstly, dungeons are made of rows and columns of tiles. If you plan to have variable sized dungeons, use dynamic arrays instead of the usual static kind.

The tiles store:

- The type of tile (e.g. wall, grass, floor, river)
- The properties of the tile (Can you walk through it? Is it illuminated? Has it been explored by the player?)
- A list of items on the tile
- The monster on the tile
- Any other data (e.g. a trap, a hole in the floor, stairs)

You should keep your tiles as small as possible. Angband has around 16 bytes per tile. Don't go much further than that; you'll see why in a moment.

### How does this work?

The type of tile is a small variable (typically a single byte) that determines what the tile is. The type determines what is drawn - if you are on a river, you draw a blue letter or graphic. A fast way to do this is to store an array of the letter / colours / graphics of all the types, and just substitute the type into this array to determine what to draw.

The properties should be set per-bit; use bit fields or bit flags. How are properties used? Initially set the "explored" flag of all tiles to false, then when the tile is explored, set it to true. Only explored tiles are drawn. If the tile can be walked through (i.e. it is not a wall or something), set the "passable" flag to true. When the player tries to walk somewhere, check the tile in that direction. If it is not "passable", don't allow the player to move there. This allows you to make illusionary walls if you want - i.e. walls which aren't really there and you can walk through them.

For the list of items, use a linked list. This allows many items to exist on the same tile. Store a pointer to the beginning of the list.

For the monsters, it depends. If you have more than one monster per tile, you also need a linked list. Otherwise, just keep a pointer to the monster, or some way to quickly find the monster occupying the tile. This is vital in combat.

There's no reason, in fact, why you can't use the same linked list to store items and monsters in. It will help you keep your tiles small, and many things that can happen in the dungeon affect both monsters and items anyway so it can be handy to find them on the same list.

What other data is needed? I would use another byte to determine any special dungeon features the tile carries, such as traps, stairs, glyphs, chests and so on. Store all of these in an array, and substitute this value into the array to find what, if anything, is at the tile. Alternatively you could just store these things in the tile's linked list too.

If your system uses 11 bytes, then to make a dungeon that is 100 by 100 tiles, you would need 100*100*11 ~= 100K of memory. This increase is quadratic as width and height increase, so be careful that you don't run out of memory. If you need to squeeze your tile size further, and you're using a programming language that supports them, consider using bit fields. They let you use less than 1 byte of memory for a variable.

### How are dungeons generated?

In most games, levels or stages are designed and hard coded in advance. In a roguelike, you want as much randomness and variety as possible (you may also want to make walkthroughs impossible to create), so you need to generate the dungeon randomly during gameplay.

There are many algorithms that will generate a dungeon. What they do is to fill a dungeon with rooms passages, stairs, traps, doors, items, and monsters, often in that order. Which comes first can be an interesting decision. If you know where the stairs from the level above came down, or where the stairs to the level below came up from, then you may want to start with the stairs and build the map around them. If you generate rooms suited to the monsters that inhabit them, then you may want to start with generating monsters so you know what kind of lairs you need to place on the map. If you regard the dungeon as a preexisting place built by someone else, that the monsters moved into later, you may want to generate the map first and place monsters and treasure in them randomly.

This all sounds very simple, but if you've tried to program it you will know it is not.

The first thing you have to decide if you are going to recreate each level every time you visit it, or if you are going to store a level once it is generated and use it afterwards until the end of the game. The latter system is more realistic, but the former ensures you never run out of variety. Angband uses the former system. NetHack uses the latter. The latter system will be described in question 6.7.

How do you fill a dungeon with rooms and passages? There are all kinds of algorithms to do it. You can just start with solid rock, then randomly "dig out" rectangles until you think there are enough. Of course, the results will be disappointing. You not only need to generate the dungeon, but you also need to make it look good. The algorithm I just described will make one huge hole in the middle of the map with rough edges, and some rooms which cannot be reached. Also, there is no limit to the size of the room, and no overlapping with other rooms is checked.

The improved "digging rectangle" algorithm would then look like this:

1. Fill the dungeon with impassable walls.
2. Pick a random number for the number of rooms.
3. Select a random location in the dungeon (x, y).
4. Randomly select the room length (RoomLength).
5. Randomly select the room height (RoomHeight).
6. If the area of this room (calculated by RoomLength\*RoomHeight) is greater than the maximum area for a room, go back to step 4.
7. If the room cannot fit in the dungeon, or overlaps with other rooms, go back to step 3.
8. Fill the rectangle given by (x, y) and (x + RoomLength, y + RoomHeight) with empty space.
9. Go back to step 3 until all the rooms are created.
10. Randomly select walls in 2 randomly selected rooms or passages.
11. A pathfinding algorithm traces a path (passage) from one wall to the other. If no path exists, go back to step 10.
12. Repeat from step 10 until every room is reachable.

This still has several errors. For instance, you might come into a situation where no further rooms can be added to your dungeon, and your dungeon generation procedure would go into an infinite loop. But the biggest problem is the speed - it can be very slow. While the dungeons it makes look good, they aren't very realistic - it isn't how anybody digs a dungeon. The pathfinding algorithm used to trace passages is slow and difficult to program. The biggest imperfection is that you don't know whether a room is reachable. There are ways (discussed later) to tell whether a room is reachable from elsewhere in the dungeon, but some algorithms always make rooms that are linked to the rest of the dungeon anyway, and this simplifies the whole thing.

How do they do it? Before a room is created, an already-existing room is randomly selected. A passage is drawn from that room to the new room, and then the new room is added at the end of that passage. Mike Anderson's "Dungeon building algorithm" at the Roguelike News sites describes it quite well, the following is just a summary.

1. Fill the dungeon with solid walls.
2. Make a room in the dungeon.
3. Randomly select a room (or passage) wall in the dungeon.
4. Decide on whether to build a passage or a room.
5. Check there is enough space to make the passage or room. If rooms are in the way, reduce the passage/room length so that it joins the room(s). If the passage/room runs out the dungeon, go back to step 3.
6. Draw the passage/room (by filling it with empty space).
7. Go back to step 3 until enough rooms/passages are drawn.

Passages that zigzag or twist can simply be handled by selecting a wall at end of the passage when you reach the end of step 6, and going to step 5 and building a passage from that wall.

Good looking dungeons can also be made with a fractal algorithm. The fractal algorithm will produce any kind of pattern - for landscapes, forests, rivers, islands, coastlines or whatever else you plan to use. I don't recommend using fractals for dungeon generation, as they can make rooms that are unreachable from somewhere in the dungeon. But have a look at question 7.5 if you are interested.

### How do you make dungeons with a theme?

TBC

### How do you make sure all your rooms and passages are reachable?

Some algorithms always generate rooms and passages that are reachable from every other room and passage. If yours does not, you need a way to check this specifically. This is one.

The flood-fill algorithm is probably the best way to do it. It involves recursively calling a procedure that fills all empty tiles until it encounters a boundary (like a wall). When the flood-fill is complete, just check all the rooms for having a tile which is filled. Those that do not are unreachable from the tile where you started the flood-fill (as well as from the rooms which are filled).

An example ([C](c.md)) is:

```c
void FloodFill(int x, int y)
{
  if ((Dungeon[x][y].Content == FLOOR) && (Dungeon[x][y].Flags != 1))
    Dungeon[x][y].Flags = 1;
  else
    return;
  FloodFill(x+1, y);
  FloodFill(x-1, y);
  FloodFill(x, y+1);
  FloodFill(x, y-1);
}

bool AreAllRoomsFilled(void)
{
  for (int Count1 = 0; Count1 < DungeonWidth; Count1++)
    for (int Count2 = 0; Count2 < DungeonHeight; Count2++)
      if ((Dungeon[Count1][Count2].Content == FLOOR) && (Dungeon[Count1][Count2].Flags != 1))
        return false;
  return true;
}
```

In some cases you might _want_ a secret room or passage which is cut off from the rest of the dungeon, and to which you can only get through a special spell or complicated digging. If so, make provisions for it.

### How do you make a town?

The simplest way to make a town is to create an empty dungeon level and fill it with buildings. A building can just be a rectangle of undiggable wall - so that the player can't accidentally mess up your town level - along with a shop doorway on one side of the rectangle. To keep the level looking the same if you don't use persistent levels, save a random seed of the town, and use this seed to regenerate the town level every time you return to it.

Depending on how you want to interact with the stores, stepping on a shop doorway should either allow the player to enter the shop, or bring up a menu allowing the player to interact with the shop. Having the shop contents inside the building makes for a more 'realistic' interface, but it forces the player to sift through the shop contents to find what they want, as well as possible for them to steal items from the shop by picking something up and leaving with it. Having a shop menu allows the player to buy and sell items more quickly, which is important for games which feature more shopping, such as Angband.

### How do you make wilderness?

There are a wide variety of different wilderness generation algorithms. However, the simplest is to create wilderness levels is to have a variety of different terrain types, such as trees, water and sand, and use these in combination to create a wilderness level.

- Pick one terrain type that the player can move through and fill a dungeon level completely with this terrain type.
- Then pick an impassable terrain type, and draw this around the edge of the dungeon level. You may want this terrain type to have rough edges. In this case, pick a y for the top left hand corner. Fill the first column from the edge to the y with the impassable terrain. Then move east in one grid increments and randomly increase or decrease y by one at each step, filling in the columns as you do. Limit y between 1 and some maximum value that ensures that your map is not completely filled with impassable terrain. As you reach the top right hand corner, you'll do the same, but working the rows going south, then columns going west from the bottom right corner, then rows going north from the bottom left corner.
- Pick two to three more terrain types, and drunken walk these around the map, to create natural looking terrain. It is not important at this stage that you worry about connectivity.
- Finally, to ensure connectivity, place the dungeon entrance, the player's starting point and other interesting areas on the map, and connect these using the tunnelling algorithm you used for building the dungeon. You'll need to change terrain differently than you would for the tunnelling algorithm through the dungeon: for instance, tunnelling through ice might result in snow. This can still result in disconnected areas on the map, but all the interesting points should be connected. Getting into these disconnected areas should just serve as a reminder that the player shouldn't risk getting lost in the wilderness.

If you want multiple wilderness areas, allow the player to travel from one of these wilderness areas to another. This could be done by having a travel command which presents a choice of adjacent wilderness locations to travel to, when you are near the edge of the map, or by having stairs which connect to different adjacent wilderness locations depending on which edge of the map they are on.

### How do you make persistent dungeons / worlds?

A persistent dungeon, as mentioned earlier, is a dungeon that doesn't change. In Angband, every time you visit level 1 it will look completely different. In NetHack, the levels are stored, so level 1 always looks the same.

Obviously you have to save the levels to disk to ensure they don't change. But is it realistic for levels never to change at all? Sure, the walls should keep the same shape and the dungeon features should be in the same place, but should an item be where you left it months ago? Should all monsters "freeze up" and stay in exactly the same place, ready to do exactly what they were doing when you last visited that level? Should spells that affect the dungeon, e.g. "destroy trap", still be in effect when you re-enter a level? You need to think carefully about what should be stored, and what should not.

To save a dungeon, all you do is create a file and then write to it, in order, everything that needs to be written to file. To load it, just read everything back from the file in the same order that it was written.

Of course, it isn't as straightforward as that. When you don't know how many structures (e.g. items, monsters) will be written to the file, you should count them, then write the count before you write those structures. That way, when you read from the file, you just read the count, and so you know exactly how many structures you need to read from the file.

Pointers are another problem. When you read a structure containing pointers from file, you have to reassign new memory addresses to those pointers. In most computers, a program can be loaded into memory starting at any address, and so the memory addresses contained by your pointers will be wrong if your program is loaded at some other address. In fact, try to avoid writing any pointers to file - they waste disk space, and do nothing useful. Instead, write a data structure to file field by field instead of just writing it as a whole.

You can use serialization to store it in a file. Look at (<http://en.wikipedia.org/wiki/Serialization>) for more information.

### How do you store a really BIG world?

You might be encouraged to make a big world and make it "persistent" (i.e. the levels don't change every time you visit them), but this takes up a lot of disk space. For instance, if each tile in your game is 20 bytes, and your dungeon is 256 by 256 tiles, and you have 100 dungeons, they will take up about 131 megabytes of disk space, items and monsters and other things excluded.

So how do you reduce the disk space taken up? Well, you can either compress your level files, or you can change the way levels are stored and generated.

The simplest way to apply compression to your world involves dividing the world into "active" and "dormant" parts. The conventional approach is to let the "active" part be the dungeon level that the player is currently on, and consider all other dungeon levels "dormant" (meaning e.g. that no monsters actually move on those levels, though movement might be simulated when the player reenters them later).

The easiest way to do the actual technical compression of a string of bytes (representing, e.g., a dungeon level) into another, hopefully shorter, string of bytes is to use an external library. [zlib](http://zlib.net/) is by far the most common choice, being very portable, and open source with a [liberal license](http://zlib.net/zlib_license.html) that allows use even in commercial closed-source projects. zlib is well established and rather efficient in terms of speed and compression ratio, and you're very unlikely to beat it with a self-implemented general-purpose compression algorithm.

If you do not wish to depend on an external library, there's a bit more work to do, but it is possible to implement a simple compression algorithm "by hand". There are many compression methods, but I will discuss one that reduces your file size considerably while at the same time not taking too long to do. It's called RLE (Run Length Encoding), and it is used in many file types. What it basically is, is replacing a repeating sequence of data, with a count and one sample of that data. For instance, the string "AAAAA" would be stored as "5A".

So to RLE compress a structure, count the number of repetitions and store it with whatever is repeated. Repeat until you've stored the entire structure. To decompress an RLE compressed file, simply read in the count, and set that many data structures to whatever comes after the count. Repeat until you've loaded the entire structure.

This method works best on:

1. Large quantities
2. Similar data

In other words, small quantities of data with lots of variety will not work well, and may even get bigger with RLE compression ("ABC" would be stored as "1A1B1C", which is double the size!).

Since most things in your roguelike should be stored as structures or classes, it might seem like you should compress / store structures as a whole. But there are usually a lot more differences between structures as a whole than there are between the fields those structures contain. In this case, it is better to RLE compress and store the fields separately. On the other hand, in some cases it is better to compress whole structures. You have to try it out and see what works better.

If there is a lot of similar data, with very few exceptions, leave out those exceptions and store them later with the location they should be at. For instance, if the string was "-_-------", you could just store "9- 2_", which means the string contains 9 minus signs, and the sign at the second position is a star. If you decide to use this, find some way to separate the exceptions from the rest of the data. In this case I used a space. You could just assume that the exceptions will be read in when the string has been filled completely. Remember to count the exceptions and write the count to the file before them so that you know how many to expect.

One last tip. You can use a bit-packed array, with 1's where there are unusual values, followed by those values, in order. If you want, you can compress those values or even the bit-packed array, but remember to decompress them somewhere before you try to decipher them.

The best data type to store the count is a 1 byte variable, but that can only store a maximum value of 255. You can handle that case by just starting a new count at zero.

While this method works well, there is an even better way to store levels, which takes up far less space. Firstly, how do you generate a level? You randomly select a number of things, like room and passage location and size, directions your passages run in, locations of your items and features and so on. But the numbers you should use are not actually random. Your game should include a [random number generator](random_number_generator.md) with a state. If you save the state, then when you load that state back into the random number generator, it will produce the same sequence of "random" numbers all over again. So if you use a sequence of random numbers to generate your level, then all you need to save is the state of the random number generator. When you need to reload the level, you just reload the same state into the generator and make the level the same way you made it the first time.

Now this really helps you reduce the size of your save file. The state of your random number generator can be stored in only a few bytes. If you decide to use this method, remember not to use random numbers seed for your monsters and items (i.e. reset the random number generator after the dungeon layout is generated), because you don't want to have the exact same monsters in exact same places every time you walk into the level, and also to have the exactly same items in the exact same places.

Why would you NOT want to use this method? Well, while every random number seed has a unique sequence of numbers associated with it, the reverse is not true. This method therefore assumes that the dungeon layout hasn't changed, i.e. that you haven't dug holes in the walls, that you haven't destroyed any doors or changed any features. If you have, the changes will be lost. You can, of course, save the changes to file with the random number seed, even compress the changes, but if a lot of changes are made, the file size can exceed that of a normally stored file.

There is a way to fix that too, of course. You can decide only to store a certain number of changes, and whatever changes occur after those, undo the changes that occur furthest back in time. For instance, if you store 10 changes, when you make the 11th change, the first change is undone (lost). One problem is that this can be exploited. For instance, if you dig though 10 walls, and you dig 1 wall further, the first wall will reappear and trap you. You can probably use this to trap monsters or do other things. This can be fixed by storing a lot of changes (like 100 or more) so the player will probably never encounter this situation. Even if you store 1000 changes, and each requires 20 bytes, you will use up about 20 kilobytes at most, which is still far smaller than 1.3 megabytes that you would have without any compression.

### How Should I Make Random Numbers?

As mentioned above, the random number generator your program uses will have a state, and when a state gets reloaded into the generator, it will produce the same sequence of numbers again. Such numbers, which are all predetermined as soon as the state is loaded into the generator, are not really random. They are called "Pseudorandom" numbers, and the routine you get them from is called a Pseudorandom Number Generator.

In C, if you include stdlib.h in your program, you get the srand() procedure, which takes as its state or "seed" a number between 0 and RAND_MAX, and the rand() procedure which returns a random number in the same range. Do not use it.

You should definitely use a random number generator that's better defined and known than srand() and rand(). Because the C standard does not define exactly how these functions work, there are variations from one system to the next and even from one compiler version to the next. The value of RAND_MAX can be different, and the pseudorandom sequences generated will definitely be different. When this happens your players will load a savefile on a new build or on a different machine, and find the "persistent" levels unrecognizable.

There are also well-known buggy implementations of rand and srand where the number sequence repeats on short periods, and the number sequence within particular bitfields of the result repeat in even shorter periods. All told, there are many good reasons to make the random number generator part of your program instead of relying on a system library.

Fortunately, there are also a lot of good third-party libraries that you can use which have consistent behavior on all platforms and operating systems they support. The Mersenne Twister is popular and fast. If you want to code your own, I recommend a lagged-fibonacci generator as described in Knuth volume 2. It's easy to code and runs fast.

In Java, creating random is much easier as they're are several implementations for this in the Java Standard-Library API. For simple random numbers the java.lang.Math class has the static random() method which returns a random double value between 0 and 1. It can be used to generate some simple random numbers by multiplying the returned value with the upper-bound.

For "seed-based" and complex random numbers (as described above), there is the java.util.Random class which can be used to get accurate random numbers to some degree. It can be instantiated with a seed, for reproducible effects. If instantiated with no seed, it will use a random seed. You can also instantiate it to use the system-time (`System.currentTimeMillis()`) as the seed but I would advise against this, as the default constructor of the Random class ensures the seed to be completely different from any other invocation of the constructor (See the source-code of the java.util.Random class's constructor). The random number generator of the Random class is very good (and fast) so you don't need to opt for native or third-partly alternatives like Mersenne-Twister. A value between 0 and a constant can be easily retrieved using: `Math.round(nextDouble() * upperBound)`. For a value between a lower-bound and an upper-bound use: `nextDouble() * (high - low + 1)) + low` .

As of Java-7u60, Instances of java.util.Random are threadsafe. "However, the concurrent use of the same java.util.Random instance across threads may encounter contention and consequent poor performance. Consider instead using ThreadLocalRandom in multithreaded designs". (-from Jdk7u60 Javadoc)

### How do you deal with stuff happening far from the player?

#### Monsters

Monsters on the same level as the player should be "processed" (i.e. decide whether to move, attack, pick up items or whatever) each turn. You simply keep a list of all the monsters on the level, and every turn you go through the list and decide what should be done for each monster.

Monsters on different levels usually do nothing. When the level is loaded, you can decide to process the monsters say 50 times (without them being able to sense the player, or they will kill him!) just to make it realistic. This way, monsters will not end up in the same place and involved in the same actions they were in when the player last visited that level. You could also keep the levels above and below the level the player is on (as well as any other levels that can be reached through say teleport traps) in memory, and process the monsters on those levels. This makes it more realistic: the longer the player is not on a level, the greater the amount of changes made to that level and the more the monsters change / disperse.

The exact monster processing will be discussed later.

### What is LoS, and why and how do you do it?

LoS ([Line of Sight](line_of_sight.md)) algorithms are ways to determine whether a particular place is visible to the player / monster. These are related to FoV ([Field of Vision](field_of_vision.md)) algorithms, which are used to determine every place which is visible to a player / monster. The former may be a subroutine of the latter.

The most common use of these algorithms is to determine what the player or monsters can currently see. The second most common use of these algorithms is to determine what is lit given a light source at a particular location. In most roguelikes, the dungeon starts in complete darkness. When you walk around, you reveal some of it. But you only see monsters or terrain that are lit and within your FoV.

Another common use is in ranged combat. Can the player shoot at the monster? Are there any obstacles in the middle that the player might hit? Does the monster have any cover from nearby walls? Usually, attackers can only shoot enemies which they have a LoS to. Obstacles and cover are between the attacker and defender. If you want to print a message along the lines of 'You miss the Orc and hit the Goblin by mistake', then you need to make sure that the player has a LoS to the goblin as well.

The final purpose that LoS and FoV algorithms are frequently used for is to determine who is affected by an area effect ability. An explosion may harm all nearby creatures, but you want the possibility of an enemy being sheltered from the blast by an intervening wall. Similarly, a gaze of petrification may affect many enemies at once, but should certainly not pass through walls.

The methods for determining LoS and FoV range from simple to extremely subtle. Simple techniques involve simply allowing sight of everything nearby or everything in the current room. [Ray casting](ray_casting.md) algorithms are a bit more complex. They use a simple LoS algorithm, such as [Breshenham's Line Algorithm](breshenhams_line_algorithm.md), and then use it to calculate FoV by checking LoS on nearby squares.

The more subtle algorithms include [Shadow casting](shadow_casting.md), which simulates a light from a point source and [Permissive Field of View](permissive_field_of_view.md), which simulates a light from a volume source to determine FoV. These more complicated methods are difficult to correctly implement. But both have freely available libraries which can be incorporated into your roguelike.

A detailed evaluation of popular LOS algorithms (along with a new, better algorithm) can be found at Adam Milazzo's page here: <http://www.adammil.net/blog/v125_roguelike_vision_algorithms.html>.

### How do you store the list of all the items in the game?

You should know what kinds of items you are going to have in the game. You should also have your magic system well planned, and any other systems involving items (battles, shopping, stealing) also well planned.

Decide on the exact categories of items, and the similarities and differences between them. Decide what each item category needs to contain. For instance, while most items should be able to carry enchantments, foods and potions should not (unless, of course, a potion works the same way as a spell does). Your weapons and armor should store info relevant to the battle system (like attack power, damage type, defensive rating and so on), but scrolls should not. I think a good way to represent all the different item categories is to use object oriented programming, because that allows inheritance, polymorphism and so on, which is very useful.

Now one way to store the item list is to just keep a huge list of all the items in the game, in some kind of array, and to represent an item, you just store a number, which is the position of that item in this array. While this system works well for some kinds of items where there is no difference between two items of the same kind, the problem arises when you want to store something individual about an item. For instance, you might want items to carry an enchantment that makes them do extra damage, or that heals you faster. You might want a damage system, where as you use your items, they take damage, and when that damage reaches zero, they fall apart and become unusable. You might want to allow the player to name objects as in Nethack or attach macros to them, as in Angband. In such cases, the items have to carry individual enchantments damage ratings, and other information. So you can't just store such items in the array. You would have to store all the settings for each item with the item.

The best way to do it, then, is to split up your items into 2 categories. In the first category are items which carry no individual properties. These are simply represented by a number which is substituted into the item array to find out about them. In the second category are items which have some individual properties. They are represented by both a number which get substituted into the item array, and by the individual settings they carry.

Since you will most likely end up with different sizes for different item types, you can't use 1 massive array for all the items. You will most likely have to use an array for each item category. You store these arrays in a file, and read them in from the file when the game is started.

### How do you store the items in the player's inventory?

The previous question discusses how to generally store the items. One more thing to remember is that if the player / monster is carrying more than one item that is the same (e.g. 5 arrows), you should allow the items to stack. So you need one more structure per item, and that is the number (for the arrows, it would be 5). If you want to check total inventory weight, remember to multiply each item's weight by its number to get the correct weight, otherwise you will just get the weight of 1 item.

But now how do you store all those items in the player's inventory? You can use:

1. A static array
2. A dynamic array
3. A linked list

A static array is simply array with a fixed number of elements. It is easy to read and write to a file, because it is one continuous structure and you always know how long it is. You only need to still store the number of items in the array so that you know where the stop reading the items back from file. It is also very easy to sort. The disadvantage is that memory is always wasted. If the array has 50 elements, and you only carry 5 items, 45 elements worth of memory is sitting there unused. Also, you can't carry more than 50 items, so you constantly have to check this when buying / picking up / doing anything that adds items to the inventory.

A dynamic array is an array that can be of any size, because it can be created and destroyed at any point in your program (using "new" and "delete" in C++, and "malloc()" and "free()" in C). When you create it, you specify the size. Unfortunately, you can't change the size of the array after it is created. So when you decide to add or remove the item from the inventory, you need to create another array with the right number of elements, and then copy everything from the old array, and delete the old array. This is slow. If you use C, you can use realloc(), which does the same thing. Sorting a dynamic array and writing it to file is just as simple as sorting a static one, though.

A linked list is a more advanced programming structure. It consists of a bunch of "nodes" created dynamically (i.e. whenever you like, not all at a specific time like with arrays), and each one containing a pointer to the next one in the list (and sometimes the previous) and containing data (like your item info). The cool thing about linked lists is that there can be as many as you like, they will never waste any memory, and adding and removing items is easy and quick. They can only be accessed sequentially, i.e. you have to go through them in order from beginning to end. However, sorting linked lists, although harder to program, is even faster than sorting arrays, because you don't need to swap the actual data, only the pointers' memory addresses. Writing this to file is slightly harder, as you have to do it item by item, you can't just write the whole list at once, and you don't know how many items you've got (unless you keep count). Linked lists can be used for a whole lot of other things too. This is the recommended structure for those who know about it and how to use it. If you don't, it's quite difficult, learn pointers and dynamic memory allocation first.

### How do you make randomly generated items?

- Decide what percentage of items in the game should be random
- Decide what categories of items should be randomly generated
- Decide what things should be random in each category
- Decide what the range for those random values should be
- At the start of a game, generate the random items and put them in the big item array

### Which kinds of monsters should I use in my roguelike?

It depends on the setting and story. Most roguelikes use monsters taken out of Tolkien's work: orcs, trolls, wargs, hobbits, dwarves, elves, dragons and so on. Traditional creatures taken out of various mythologies and religions are also used: nagas, medusas, gorgons, angels, demons and so on. Some are taken out of "Dungeons and dragons", such as the infamous kobold. Plenty of animals are used: from the normal cave-dwelling creatures like bats and spiders, to the ridiculous lice and ants.

Make up your own if these are not enough.

### How do you create a monster AI?

AI (artificial intelligence) is not so easy to create. There is several different techniques you might want to use.

State machines are probably the easiest way to do AI. Firstly, get a piece of paper. Then write a set of states (e.g. attacking, walking somewhere, running away, stealing), circle those states, and draw arrows (with directions) from each state to every state you can get to from that state. These arrows are called transitions. Label each transition with the circumstances under which it occurs. An example:

```text
     +-----------------+    +---------------+
     | player runs away|    | healthy again |
     |                 v    v               |
+-- ATTACK        APPROACH PLAYER       RUN AWAY
|    ^                 |                    ^
|    |  close enough   |                    |
|    +-----------------+   likely to die    |
+-------------------------------------------+
```

Of course, it would be a lot more complicated. There is far more decisions to be made, and there is more actions (one for each item / spell). Now how do you implement this? Instead of using the usual massive nested "if" or "switch" statement, you make a 2 dimensional array, with the number of states being its width and height. This is the state transition table.

```text
            ATTACK        APPROACH              RUN
ATTACK                    Player runs away      Likely to die
APPROACH    Close enough
RUN                       Healthy again
```

You keep a variable with each monster that tells you which state it is in. This tells you about the row of the state transition table. You then go through that row, and run tests to see if the circumstances in it are fulfilled. If they are, pick the one with the highest priority, and switch to that state. For instance, you are in the "ATTACK" state. Look in the left-most column for "ATTACK" (it's at the top). Right, now look along the row. Under the first column ("ATTACK") there is a blank space, so there is nothing to check there. Under the "APPROACH" column there is "Player runs away". Say that is true - the player is leaving. Under the "RUN" column there is "Likely to die". Say that is also true - the monster is badly hurt. The monster's life takes priority over the player's pursuit, so the state machine switches to the "RUN" state (you could of course make the monster's life less important :-). Behavior is then based on the states. This is also where big "if" statements can come in. If you want to avoid them, use function pointers as well in your state transition table, and make a function for each action / state.

State machines are very good, and unlike a quick AI you make using hacks, they stand up well to difficult situations and don't require a lot of processing or calculation. They are used in many programming situations, so it is worthwhile to learn them.

Other techniques, like neural networks are described in question 8.x.

### How do you create a good monster AI?

The previous question describes how you make an AI. This one will discuss how to improve it and make it interesting.

Firstly, plan. Do you want some monsters to attack each other? Do you want to make some co-operate with the player? Do you want group tactics? Various strategies?

### How do they find / follow the player?

Let's see. The player is always moving. The monster is always moving. The other monsters are always moving. Doors are opening and closing, new obstacles are coming around. This seemingly simple problem of "now the monster follows the player" is actually a real challenge.

Different roguelikes have tried different things. In Angband, the monsters sleep (do nothing) when they are created. When the player comes around making noise, they wake up. This way, far away monsters don't constantly have to look for the player. Also, monsters only move to attack you if they see you (I think), and if you temporarily disappear, they go to where they last saw you.

How do they move closer to you if they know where you are (i.e. can see you)? If their X value on the map is smaller than yours, they take a step that increases their X value; if their X value is bigger than yours, they take a step that decreases it, and if it is the same, they don't take a step which changes the X value. The same thing happens with the Y value. The combination of X and Y changes makes the monsters walk the best possible way (i.e. diagonally or in a straight line, depending on the situation). This, of course, doesn't help when it comes to obstacles. How do you get around obstacles? Several ways will be discussed here.

I am going to assume the monsters want to find you, and you are far away. A good way to mark a path toward the player is to use the player's Line of sight code to mark squares the player has seen. Each time a square is found to be visible to the player, mark the square with a "scent value" X, where X is 32 times the current turn number, minus the distance from which the player saw the square (this presumes that 32 is larger than your maximum sight radius. If that's not true, then use your maximum sight radius instead).

Now, any time a monster is on a square that the player has seen, it can just check the scent values on the surrounding squares. If it keeps moving into the adjacent square with the highest chase value, it will effectively find its way through squares the player has seen to the player. When the player's in view, it will act like it's tracking him by sight. You probably want to limit this method depending on the monster's abilities. Scent tracking monsters could be limited to squares seen from a very short distance of one or two squares, while stupid monsters could be limited to not noticing any scent values that are from more than 50 turns ago and sighted monsters don't get to follow the "scent" values until they have actually seen the player at least once. The great benefit of this method is that it's very simple and very fast and allows you to simulate several different related kinds of monster tracking. It will make the monsters baffled anytime the player teleports or (if the monster can't open doors) when the player closes doors. But, that's sort of what teleporting and closing doors are for, so you may decide that's all right.

Tracking by sound also works. For each action that makes a sound, start with a number representing how loud the sound was and mark the square the player's on with that noise level. Then move outward from the player marking each new radius with a noise level one less than the one before, until you get to zero.

Monsters who remember the sound then go in the direction of the tiles with the highest amount of sound. Maybe every time they hear sound, they start listening better for new sounds, and becoming more aware of smells? What is nice about sound and smell is how you magic and items which change them bring in all kinds of new strategies (e.g. an invisibility spell isn't enough any more, because some monsters can sniff you out and attack anyway).

You may want to do pathfinding with a general pathfinding algorithm like A\* or Dijkstra. These will find the shortest path (if any) between two points on the map. You might want to use these for some special monsters that know the location of the player (through magic or something). The problem with general pathfinding algorithms is that they are slow, and may be difficult to learn and program (unless you know a lot of Graph Theory). Also, since the player is moving, and monsters are moving, and new obstacles are coming around, you would have to trace a path every few turns to make sure it is still possible (no new obstacles). This is very computationally expensive, so like I said, it isn't good to use for every single monster, only for some. It can also be used for moving the player; in some games (a.k.a. Diablo/2), you click with the mouse where you want the player to go, and he walks there using the shortest path and avoiding obstacles and everything else in the way.

A quick algorithm and explanation of pathfinding (since I couldn't find any and had to learn the hard way): This is something like Djkstra's algorithm. What happens is there are 2 lists: OPEN and CLOSED. The OPEN list stores a list of tiles which are possible candidates for a shortest path, and the CLOSED list stores the tiles we've been through, so they aren't unnecessarily repeated. The "movement cost" is the number of steps the monster has to take to get to that tile, walking on the path associated with that tile. New tiles are only added to the OPEN list if they don't exist, and tiles only replace other tiles if they have shorter paths. Anyway, the algorithm is:

1. Find the destination tile (where the player is).
2. Put the starting tile (where the monster is) on the OPEN list. It's starting cost is zero.
3. While the OPEN list is not empty, and a path isn't found:
   1. Get the tile from the OPEN list with the lowest movement cost. Let's call it the CURRENT tile.
   2. If this is the destination tile, the path has been found. Exit the loop now.
   3. Find the tiles to which you can immediately walk to from this tile. These would the tiles around this tile, which don't contain obstacles. Call these tiles "successors".
   4. For each successor:
      1. Set the successor's parent to the CURRENT tile.
      2. Set the successor's movement cost to the parent's movement cost, plus 1 (for diagonal movements, add more if it takes longer to go diagonally in your game).
      3. If the successor doesn't exist on either the OPEN list or the CLOSED list, add it to the OPEN list. Otherwise, if the successor's movement cost is lower than the movement cost of the same tile on one of the lists, delete the occurrences of the successor from the lists add the successor to the OPEN list Otherwise, if the successor's movement cost is higher than that of the same tile on one of the lists, get rid of the successor
   5. Delete the CURRENT tile from the OPEN list, and put it on the CLOSED list.
4. If the while loop has been ended because the OPEN list is empty, there is no path.
5. If this is not the case, the last tile pulled from the OPEN list, and its parents, describe the shortest path (in reverse order - i.e. from the player to the monster - you should read the list of tiles back to front).

### How do you add variety to your monsters?

A big problem with monsters in roguelikes is that they are just too predictable. You know one hit with a sword will kill every mouse, one arrow will kill every yeek, and it takes a lot from both weapons and spells to kill a dragon. Even though there are often lots of monsters, once you've seen each one, that's it, you've seen it all.

A number of different techniques therefore exist to make monsters more unique and add more variety to them. Making monsters carry items is one of them. So is a good monster AI. There is several more. Here I will discuss genetics and neural networks.

In real life, animals and people carry genes, which add a lot of differences between them. Some are faster, some are smarter, some are healthier and so on. What happens over a period of time is that the fittest survive and the rest die out. So when your player walks into the dungeon and starts killing monsters, he will find they are not all the same, and they get stronger over time, because as the weaker die, the stronger live on to reproduce and have stronger children. How do you make monster genetics? Decide what the "genes" should be. Maybe extra health points. Resistances to various elements and spells. Stronger attack and defence. Intelligence and eventually the ability to use magic? Then decide what each gene does. For instance, each health gene affects health points by 5%. For each good health gene, the health points go up 5%, and with a bad health gene, they go down 5%. So if you gave an orc 4 health genes, and they all turned out "good", the orc would have 120% of its normal health points. If they all turned out "bad", he would only have 80%.

Now, using genetic algorithms, when you create a monster, randomly select 2 parents. Somehow combine the parent's genes (usually using random selection and averaging). If there isn't enough parents, randomly create genes. How does this help? Well, the orcs with 80% hit points will get killed more by the player, and so fewer will survive to have sickly children. The children will thus become healthier, because only healthy parents have survived to reproduce. As they grow healthier, they are more of challenge for the player.

Neural nets are a way for a computer to learn. They are a difficult and complicated topic, which is why they are rarely used in any games, but simple ones should be manageable with a little research. What basically happens is that a neural net takes a number of inputs, and produces a number of outputs. Unlike a program which you make, however, a neural net learns from experience and corrects its mistakes, so a good one should make a really challenging opponent.

The inputs to a monster neural net could be:

- Current state of health (hit point percentage)
- Current opponent's state of health (probably also a percentage)
- Distance from opponent
- Damage done by previous attack

The outputs:

- Movement towards the opponent
- Movement away from the opponent (running away :-)
- Some or other attack
- A spell

You train the net by comparing the damage done to the opponent, with the damage received from the opponent. With a little practise, the neural net would learn to run when it is near dead, to go closer to the opponent when it is far, to use attacks which do the most damage and so on. One thing to remember, is that if you plan to have neural nets for each creature, don't destroy the net when the creature dies. Then all the learning is lost. Rather use a net for all kinds of that creature (e.g. a neural net for all orcs).

### How do you make your battle system?

The heart of any roguelike is the battle system. What makes most roguelikes fun, is killing thousands of monsters. The better the battles, the better the game. So a good battle system is not only nice to have, it is essential.

Planning a battle system, however, is no easy job. Every single spell, every usable item, every monster type, and many other things, can completely change the way battle works. For instance, say you have this spell called "haste" that makes the player take one extra action per turn. That's easy: one "if" statement checks if haste is active, and if so, the player takes another action. But now say you decide to change the haste spell so monsters can use it too. Now things can really complicated when you have multiple monsters and the player with "haste". Your entire battle system might have to change. So the point is, you need to start with a good design that's capable of modeling the kinds of things that you're going to make important in combat. If you have something like "haste", it means speeds can vary by creature, so you need to start with a system where speeds can vary by monster. Then the complexity of the "if" statements checking each turn for another action never comes up because the player and monsters are simply taking turns at different rates.

Basically, combat works like this. Everybody gets a certain number of turns, they do what they think will inflict the most damage on their opponent while minimising their own damage taken. Damage is inflicted with weapons, and it is reduced (or blocked entirely) with armor.

The point of combat is very simple. Kill as many opponents as possible while staying alive yourself. When do you die? Every roguelike (and most other games) I've seen use a "hit point" (or "health point") rating, abbreviated HP. You have a certain amount of HP, and a maximum. The maximum grows when you reach the next level, and the current HP grows when you rest/drink healing potions/use healing spells and so on. When your current HP is maxed out, you are completely healthy. When it drops to 0, you are dead. Damage reduces it by the amount of damage taken, and healing increases it by the amount of health points gained. How do you calculate this? Instead of the slow and problematic way of checking overflows like roguelikes usually do it, do this: When a hit occurs: If the damage is greater than or equal to the current HP, the HP drops to zero and you die. Otherwise subtract the damage from the current HP. When you are healed: If the health gained is greater than the difference between your maximum and your current HP, your current HP takes on the value of the maximum HP. Otherwise, you add the health gained to the current HP.

When you process monsters, the monster AI decides what action to take.

### How do you represent, store and process monsters?

This is a good question, but I don't think very many articles have been written on this.

Firstly, to represent the monster, you need to take everything into consideration (which is why this is the last article about monsters). Does each monster have a name, or do only some? How do you deal with different AI's? How do you store and use spells? How does your monster carry items? And can all monsters do all of these things? If not, how do you know and what do you do about it? Planning (at least each kind of monster), object oriented programming and good structures should do the job for a large variety system such as this. For the items section in this FAQ, a system was discussed where items were split into 2 categories: properties unique to each item, and properties which are the same throughout the same item (e.g. two ordinary swords will do the same damage, but one might have a spell acting on it that the other does not). This system works well for monsters too - all orcs have so many maximum hit points, but each one can have different current hit points (because they have been hurt). So splitting up monsters this way can be a good idea.

Most monsters are stored in some kind of data (or text) file. They are read in when you play the game. Inside the game, monsters will change often - the player will kill them, they might multiply, more will be created, reading a scroll of monster summoning will call up more and so on. So you must make considerations for this from the start. A linked list is very good for storing the monsters inside the game; arrays are going to run into size problems very quickly. If you are programming using object oriented programming, linked lists can store any object derived from a base class, and the right virtual method will still be activated for each monster. Adding new monsters to a linked list is a piece of cake, removing dead ones is just as easy. So linked lists are the storage system of choice.

How do you process monsters? Just pass through the linked list and run some or other function or method for each monster in it. Alternatively, since each map tile points to a monster (if any is standing on it), go through each map tile and process the monster on it.

One problem that could occur is that you get dangling pointers: each spell/person/monster/item attacking/working on/flying towards the current monster points to the monster. When the monster dies, you delete the monster from the list, and those pointers point to who knows where. Unpredictable things could happen. Use "reference counting" (see www.memorymanagement.org). Basically, each monster keeps a count of its "users", initially set to zero. When something new points to the monster, increment the monster's user count by 1. Each turn, those items/spells/whatevers pointing to the monster must check if the monster is dead (marked somehow on the monster without removing it from memory), and if so, stop pointing to the monster, and decrease the monster's user count by 1. Only when the monster's user count is zero can it be safely deleted from the list and removed from memory.

Another problem you might run into is how to deal with monsters which have different speeds. A monster that takes 3 actions per turn can either make all 3 when it is processed, or you can use the more realistic (and difficult) system where those turns are spread out among everyone else's turns. For this, you might want to keep a separate list for monsters of different speeds, and pass through the linked lists with different speeds more than once, taking only one action each time. Then there are the "haste" and "slow" types of spells that change the number of actions you normally take...

## SPELLS

---

### How do you represent a [magic](magic.md) system?

You plan it very well in advance first. Don't make magic a black box device - explain it somehow, and integrate it into your story. Decide on the magic system, categories of spells, all of that.

### What is a good magic system to use?

Your own. I would not copy from "Dungeons and dragons", other roguelikes, other games, New Age books, Internet websites on magic and paganism, and whatever other sources you might think are good. Maybe get ideas from them, but don't copy spell names or statistics.

Traditionally, most roguelikes work magic similarly to "Dungeons and Dragons", with a few exceptions. Each spell has a level. You have to be on the same or higher level (experience wise) to learn it, and you have to be a class that can learn spells, and have the appropriate spell book. You have a certain amount of magic points. They increase when you level up. Each spell uses some magic points, and when you run out, you can't cast spells. Magic points are restored by resting and with some potions.

### How do you enchant objects or make "enchanted" objects?

With great difficulty.

### How do you make random spells?

Similarly to random items.

## Can you make any money from your roguelike?

---

Of course you can. Look at "Dungeon Hack" or "Diablo" for example. Real-time combat and nice looking graphics are heavily recommend if you want to reach out for a broader (paying) audience, though. But all the major roguelikes are free of charge, and all except Adom also permit free distribution and modification of the source code - something that is probably necessary unless you plan to write your game by yourself.

## Contributors

---

- David Damarell (NetHack description, question about money from roguelikes)
- Bridget (list of newsgroups)
- Jens Baader (list of roguelikes and newsgroups)
- Philip Swartzleonard (who makes roguelikes)
- [Kornel Kisielewicz](kornel_kisielewicz.md) (wikified)

If your name isn't here, and you see your text somewhere on this, please raise an issue, with what you wrote, so I can add you in.
