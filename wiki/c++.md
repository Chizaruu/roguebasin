# C++

---

**C++** (pronounced "see plus plus") is a general-purpose intermediate-level computer programming language. It is a statically typed free-form multi-paradigm language supporting procedural programming, data abstraction, object-oriented programming, and generic programming. Strictly speaking, C++ is not a superset of [C](c.md), but for all practical purposes one can consider it thus.

During the 1990s, C++ became one of the most popular commercial programming languages, and it is still widely used today. Recently, however, more modern high-level scripting languages such as [Python](python.md) and [Ruby](ruby.md) are enjoying increasing popularity.

## Differences in C++ from C

---

Features introduced in C++ include declarations as statements, function-like casts, `new/delete`, `bool`, reference types, `const`, `inline` functions, default arguments, function overloading, namespaces, classes (including all class-related features such as inheritance, member functions, virtual functions, abstract classes, and constructors), operator overloading, templates, the `::` operator, exception handling, and run-time type identification.

C++ also performs more type checking than C in several cases.

Comments starting with two slashes ("//") were originally part of [C](c.md)'s predecessor, BCPL, and were reintroduced in C++.

Several features of C++ were later adopted by C, including `const`, `inline`, declarations in `for` loops, and C++-style comments (using the `//` symbol). However, [C99](c.md#C99) also introduced features that do not exist in C++, such as variadic macros and better handling of arrays as parameters.

## Design of C++

In _The Design and Evolution of C++_ ISBN 0-201-54330-3, Bjarne Stroustrup describes some rules that he uses for the design of C++. Knowing the rules helps to understand why C++ is the way it is. The following is a summary of the rules. Much more detail can be found in _The Design and Evolution of C++_.

- C++ is designed to be a statically typed, general-purpose language that is as efficient and portable as C
- C++ is designed to directly and comprehensively support multiple programming styles (procedural programming, data abstraction, object-oriented programming, and generic programming)
- C++ is designed to give the programmer choice, even if this makes it possible for the programmer to choose incorrectly
- C++ is designed to be as compatible with C as possible, therefore providing a smooth transition from C
- C++ avoids features that are platform specific or not general purpose
- C++ does not incur overhead for features that are not used
- C++ is designed to function without a sophisticated programming environment

Please refer to the indepth book on C++ Internals by Stanley B. Lippman (he worked on implementing/maintaining _C-front_ the original C++ implementation at Bell Labs). "_Inside the C++ Object Model"_ documents how the C++ compiler converts your program statements into an in-memory layout.

## Roguelike Specific Details

C++ is a great language to write a rogue-like with. It has all the freedom of [C](c.md), with some additional language features which can make a programmer's life easier (such as templates and classes).

## C++ Roguelikes

- stable 3059
- defunct Abura Tan
- defunct The Abyss of Nah-Karr
- αlpha Adeo
- 7DRL Backwards Gravity
- 7DRL City of the Condemned
- αlpha Cogmind
- αlpha CyberRogue
- stable Decker
- stable Deliantra
- βeta DemiseRL
- βeta Doryen arena
- αlpha Downfall
- stable Drakefire Chasm
- stable Dragonslayer
- βeta Dungeon Crawl Stone Soup
- 7DRL DungeonMinder
- 7DRL Fatherhood
- 7DRL The Favored
- stable Frozen Depths
- αlpha Geminum Orbis
- stable Get Out!!
- stable Hunger Games
- βeta Incursion
- βeta Infra Arcana
- βeta Into the Dungeon ++
- stable IVAN
- 7DRL Jacob's Matrix
- αlpha Kaduria
- βeta Labyrinth of Reptoran
- βeta Lair of the Demon Ape
- defunct The Legend of Saladir
- βeta Letrain
- 7DRL Letter Hunt
- stable Linley's Dungeon Crawl
- 7DRL Magic Monsters
- αlpha Middlecrest
- 7DRL Mouse Crawl: Blackheather
- βeta Nazghul
- αlpha Papaki
- αlpha Peleron's Brilliant Rebirth
- stable POWDER
- βeta PrinceVSZombies
- stable Rings of Valor
- βeta Rogue Effect
- 7DRL Rogue Mercenaries
- αlpha Roguelike gcs
- defunct QuickQuest
- 7DRL Save Scummer
- stable SCOURGE
- βeta SewerJacks
- stable Shadow of the Wyrm
- βeta Slaves to Armok II: Dwarf Fortress
- βeta The Wastes
- αlpha Tale of Arcana
- stable The Ground Gives Way
- 7DRL True God
- αlpha Underbooks
- 7DRL Warlock of Firetop Mountain
- βeta Warlock's Mountain
- defunct The Woods of Torbin
- αlpha X@COM
- βeta Xenocide
- 7DRL You Only Live Once
- αlpha Zaiband
