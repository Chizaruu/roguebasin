# Python

---

## What is Python?

---

From the official [Python FAQ](http://www.python.org/doc/faq/general/#what-is-python):

"Python is an interpreted, interactive, object-oriented programming language. It incorporates modules, exceptions, dynamic typing, very high level dynamic data types, and classes. Python combines remarkable power with very clear syntax. It has interfaces to many system calls and libraries, as well as to various window systems, and is extensible in [C](c.md) or [C++](c++.md). It is also usable as an extension language for applications that need a programmable interface. Finally, Python is portable: it runs on many Unix variants, on the Mac, and on PCs under MS-DOS, Windows, Windows NT, and OS/2."

A full set of features can be found [here](http://www.python.org/about/).

## Roguelike Specific Details

---

Python's expressive code, dynamic typing, flexible data types, and powerful OO and exception handling facilities make it well-suited to quick, productive RL development. However, like all interpreted languages, Python has a reputation for large memory overheads and being slower in some applications when compared to compiled languages such as C++. These problems, however, can be overcome with the extension of third-party Python modules and proper profiling and programming techniques. Another problem is that Python often requires the user to download an interpreter separately. A way to solve that is to include the interpreter in the game download.

Python's rapid speed of development and human-readable code makes it an excellent choice for programmers wanting to develop a Roguelike in a limited amount of time, making the language ideal for 7DRL Challenges.

## Python-Specific Articles on RogueBasin

---

General:

- [Complete Roguelike Tutorial, using python+libtcod](complete_roguelike_tutorial_using_python+libtcod.md)

Field of View and Line of Sight:

- [Python shadowcasting implementation](python_shadowcasting_implementation.md) - Python code for field-of-view calculation using Bjorn Bergstrom's excellent [recursive shadowcasting algorithm](fov_using_recursive_shadowcasting.md).
- [Permissive Field of View in Python](permissive_field_of_view_in_python.md) - An implementation of the [precise Permissive Field of View](precise_permissive_field_of_view.md) algorithm in Python.
- [Raycasting_in_python](raycasting_in_python.md) - A python implementation of brute force raycasting.
- [Bresenham's_Line_Algorithm#Python](breshenhams_line_algorithm.md#Python) - Bresenham's Line Algorithm for line of sight.

Pathfinding

- [A Python 3 and 2 Pathfinder with Pygame Example](a_python_3_and_2_pathfinder_with_pygame_example.md) - A Python 3 and 2 pathfinder that uses A\* and Dijkstra pathfinding with a Pygame example.

Map Generation:

- [Dungeon builder written in Python](dungeon_builder_written_in_python.md) - A dungeon builder.
- [A Simple Dungeon Generator for Python 2 or 3](a_simple_dungeon_generator_for_python_2_or_3.md) - A simple and adaptable python 2 or 3 dungeon generator.
- [Python_Curses_Example_of_Dungeon-Building_Algorithm](python_curses_example_of_dungeon-building_algorithm.md) - An example of a dungeon building algorithm.
- [An_Implementation_of_City_Generation_by_Leaf_Venation](an_implementation_of_city_generation_by_leaf_venation.md) - A city generator in Tk.

Scheduling:

- [A simple turn scheduling system -- Python implementation](a_simple_turn_scheduling_system_--_python_implementation.md) - A simple time system for Roguelikes.
- [A_priority_queue_based_turn_scheduling_system](a_priority_queue_based_turn_scheduling_system.md) - A turn scheduling system that uses a priority queue.

Name Generation:

- [Markov_chains_name_generator_in_Python](markov_chains_name_generator_in_python.md) - Example code used to generate random names.
- [Names_from_a_high_order_Markov_Process_and_a_simplified_Katz_back-off_scheme](names_from_a_high_order_markov_process_and_a_simplified_katz_back-off_scheme.md) - High quality Markov process random name generation.

## Related links

---

- [Official Python website](http://python.org/)
- [Official Python FAQs](http://www.python.org/doc/faq/)
- [libtcod](libtcod.md) is a free, fast, portable and uncomplicated API for roguelike developers providing an advanced true color console emulator, input, and lots of other utilities frequently used in roguelikes.
- [BearLibTerminal](http://foo.wyrd.name/en:bearlibterminal/) is another API for roguelike developers. Unlike libtcod, it provides and API for output only. It is therefore more compact, and is much simpler. Sports binding for C/C++, C#, Ruby, Lua, Python and Pascal.
- [py2exe](http://www.py2exe.org/) is a package which converts Python scripts into executable Windows programs, able to run without requiring a Python installation. Since most Windows users will not have Python installed, py2exe is a good way to distribute your Python game to that audience.
- [PyInstaller](https://github.com/pyinstaller/pyinstaller/wiki) is a program that converts (packages) Python programs into stand-alone executables, under Windows, Linux, and Mac OS X.
- [Pygame](http://www.pygame.org/news.html) ([Pygame](pygame.md)) is an SDL wrapper for Python that has been used for many graphical and non-graphical Roguelikes.

## Roguelike games in Python

---

[Artisan](artisan.md)
[Armoured Commander](armoured_commander.md)
[CrashRun](crashrun.md)
[CyberRogue](cyberrogue.md)
[Cypress Tree Manor](cypress_tree_manor.md)
[Fist of the Rogue Warrior](fist_of_the_rogue_warrior.md)
[Herculeum](herculeum.md)
[Ighalsk](ighalsk.md)
[I, Monster](i,_monster.md)
[Lands of Elderlore](lands_of_elderlore.md)
[Mines of Elderlore](mines_of_elderlore.md)
[Netpack](netpack.md)
[Nightmare Tyrant](nightmare_tyrant.md)
[Nyctos](nyctos.md)
[Pyro](pyro.md)
[rng clrc](rng_clrc.md)
[Roguelike me](roguelike_me.md)
[Shuruppak](shuruppak.md)
[SummonerRL](summonerrl.md)
[Swamp Monster](swamp_monster.md)
[The Temple of Torment](the_temple_of_torment.md)
[Torchlit](torchlit.md)
[TrapRL](traprl.md)
[Ultima Ratio Regum](ultima_ratio_regum.md)
[Wa](wa.md)
[Whispers in the Void](whispers_in_the_void.md)
[Z-Day](z-day.md)

## References

---

1. [Python downloads](https://www.python.org/downloads/), official downloads page with release information.
2. [History and License](https://docs.python.org/3/license.html), detailed license info from the Python documentation.
