# Complete Roguelike Tutorial, using python+libtcod

---

This tutorial is for libtcod 1.6.0 and above.

## Short introduction

---

### Welcome

Welcome to this tutorial! As you probably guessed, the goal is to have a one-stop-shop for all the info you need on how to build a good Roguelike from scratch. We hope you find it useful! But first, some quick Q&A.

### Why Python?

Most people familiar with this language will tell you it's fun! Python aims to be simple but powerful, and very accessible to beginners. This tutorial would probably be much harder without it. We insist that you install/use Python 2.7 and go through at least the first parts of the [Python Tutorial](http://docs.python.org/tutorial/). This tutorial will be much easier if you've experimented with the language first. Remember that the [Python Library Reference](http://docs.python.org/library/index.html) is your friend -- the standard library has everything you might need and when programming you should be ready to search it for help on any unknown function you might encounter.

> This tutorial is for Python 2 only, and it is strongly recommended you use the latest Python 2.7 release.
>
> If you choose to use earlier versions of Python 2, you may encounter problems you need to overcome.
> If you choose to use Python 3, be aware this tutorial is not compatible with it and you are on your own. (See "other languages" below.)

### Why libtcod?

---

If you haven't seen it in action yet, check out the [features](https://bitbucket.org/libtcod/libtcod/wiki/Features) and [some projects](http://roguecentral.org/doryen/projects-2/) where it was used successfully. It's extremely easy to use and has tons of useful functions specific to RLs.

If you're using Windows, download either the Win32 or x64 build from [bitbucket](https://bitbucket.org/libtcod/libtcod/downloads). Various samples are prebuilt and included, and can be used to both experiment with the various features and see what is possible.

For other platforms, you're going to have to compile them yourself. Maybe someone who owns a MacOS machine might compile builds for the libtcod project?

### Other languages

---

There are no known versions of this tutorial for other programming languages than Python, for libtcod 1.6.0. However, you can hop back to [the tutorial for 1.5.1](http://www.roguebasin.com/index.php?title=Complete_Roguelike_Tutorial,_using_python%2Blibtcod&oldid=43255) and find some option there to work from.

[Here](http://rogueliketutorials.com/) you'll find completed ports, one for Python 3 and libtcod (revising this tutorial "with good coding practices kept in mind from the beginning") and another for Python 3 and TDL, created by [/u/TStand90](https://www.reddit.com/user/TStand90) for r/roguelikedev [Tutorial Tuesday 2017](https://www.reddit.com/r/roguelikedev/wiki/python_tutorial_series).

A separate work-in-progress port of this tutorial for Python 3 and tdl (a pythonic cffi port of libtcod) can be found [here](http://www.roguebasin.com/index.php?title=Roguelike_Tutorial,_using_python3%2Btdl).

## Start the tutorial

---

Follow the first link to get started!

- [Part 1: Graphics](complete_roguelike_tutorial_using_python+libtcod_part_1.md)

  Start your game right away by setting up the screen, printing the stereotypical @ character and moving it around with the arrow keys.

- [Part 2: The object and the map](complete_roguelike_tutorial_using_python+libtcod_part_2.md)

  This introduces two new concepts: the generic object system that will be the basis for the whole game, and a general map object that you'll use to hold your dungeon.

- [Part 3: The dungeon](complete_roguelike_tutorial_using_python+libtcod_part_3.md)

  Learn how to code up a neat little dungeon generator.

- [Part 4: Field-of-view and exploration](complete_roguelike_tutorial_using_python+libtcod_part_4.md)

  Display the player's field-of-view (FOV) and explore the dungeon gradually (also known as fog-of-war).

- [Part 5: Preparing for combat](complete_roguelike_tutorial_using_python+libtcod_part_5.md)

  Place some orcs and trolls around the dungeon (they won't stay there for long!). Also, deal with blocking objects and game states, which are important before coding the next part.

- [Part 6: Going Berserk!](complete_roguelike_tutorial_using_python+libtcod_part_6.md)

  Stalking monsters, fights, splatter -- need we say more?

- [Part 7: The GUI](complete_roguelike_tutorial_using_python+libtcod_part_7.md)

  A juicy Graphical User Interface with status bars and a colored message log for maximum eye-candy. Also, the infamous "look" command, with a twist: you can use the mouse.

- [Part 8: Items and Inventory](complete_roguelike_tutorial_using_python+libtcod_part_8.md)

  The player gets to collect ("borrow") items from the dungeon and use them, with a neat inventory screen. More items added in the next part.

- [Part 9: Spells and ranged combat](complete_roguelike_tutorial_using_python+libtcod_part_9.md)

  The player's strategic choices increase exponentially as we add a few magic scrolls to the mix. Covers damage and mind spells, as well as ranged combat.

- [Part 10: Main menu and saving](complete_roguelike_tutorial_using_python+libtcod_part_10.md)

  A main menu complete with a background image and the ability to save and load the game.

- [Part 11: Dungeon levels and character progression](complete_roguelike_tutorial_using_python+libtcod_part_11.md)

  Let the player venture deeper into the dungeon and grow stronger, including experience gain, levels and raising stats!

- [Part 12: Monster and item progression](complete_roguelike_tutorial_using_python+libtcod_part_12.md)

  Deeper dungeon levels become increasingly more difficult! Here we create tools for dealing with chances and making them vary with level.

- [Part 13: Adventure gear](complete_roguelike_tutorial_using_python+libtcod_part_13.md)

  Swords, shields and other equipment can now help the player by granting hefty bonuses. The bonus system can also be used for all kinds of magics and buffs!

## Extras

---

Some stuff that is entirely optional and didn't make it in; check this out if you finished the tutorial and are looking for some modifications and improvements to your game -- some are easy, others are more advanced.

- [A neat Python shortcut for Notepad++](complete_roguelike_tutorial_using_python+libtcod_extras.md#A_neat_Python_shortcut_for_Notepad++)

  For Notepad++ users, how to set up a shortcut to help you debugging.

- [Old-school wall and floor tiles](complete_roguelike_tutorial_using_python+libtcod_extras.md#Old-school_wall_and_floor_tiles)

  Using characters in tiles, without getting weird graphical glitches. This is actually very simple.

- [Real-time combat](complete_roguelike_tutorial_using_python+libtcod_extras.md#Real-time_combat)

  A speed system to change the tutorial's turn-based combat to real-time!

- [Mouse-driven menus](complete_roguelike_tutorial_using_python+libtcod_extras.md#Mouse-driven_menus)

  Add basic mouse support to your menus!

- [Scrolling maps](complete_roguelike_tutorial_using_python+libtcod_extras.md#Scrolling_maps)

  Placeholder page for the scrolling map code. Tutorial text will be written soon.

- [Creating a Binary](complete_roguelike_tutorial_using_python+libtcod_extras.md#Creating_a_Binary)

  Package and deliver your game the nice way!

- [A\* Pathfinding](complete_roguelike_tutorial_using_python+libtcod_extras.md#A*_Pathfinding)

  A good pathfinding system

- [Using Graphical Tiles](complete_roguelike_tutorial_using_python+libtcod_extras.md#Using_Graphical_Tiles)

  An alternative to solid colors or ASCII graphics

- [BSP Dungeon Generator](complete_roguelike_tutorial_using_python+libtcod_extras.md#BSP_Dungeon_Generator)

  Binary Space Partitioning Dungeon Generator

## Credits

---

Code and tutorial written by João F. Henriques (a.k.a. Jotaf). Thanks go out to George Oliver for helping with the layout, sections rearrangement, and syntax highlighting; Teddy Leach for his text reviews; and all the folks in the libtcod forums for their valuable feedback!

The most active place to discuss this tutorial, or libtcod in general, is the [roguelikedev subreddit](https://www.reddit.com/r/roguelikedev/). Post if you're stuck, to show your own project, or just to say hi. It's always cool to get some feedback on the tutorial, and hear about other roguelikes in development. Also, past discussions can either be found in the old [libtcod/Python forum](http://roguecentral.org/doryen/forum/index.php?board=20.0) or the old [forum on this tutorial](http://roguecentral.org/doryen/forum/index.php?topic=328.0).
