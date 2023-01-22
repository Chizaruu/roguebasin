# C

---

## Background

---

The C programming language is a standardized imperative computer programming language developed in the early 1970s by Ken Thompson and Dennis Ritchie for use on the UNIX operating system. It has since spread to many other operating systems, and is one of the most widely used programming languages.  

C is prized for its efficiency, and is the most popular programming language for writing system software, though it is also used for writing applications. It is also commonly used in computer science education, despite not being designed for novices.  

## Language Characteristics

---

C is a relatively minimalist programming language that operates close to the hardware, and is closer to assembly language than to most modern high-level programming languages. Although, compared to assembly language, C can accurately be described as a "high-level" programming language, since C's features and syntax abstracts away much of the work that would need to be done if writing the same program in assembly.  

C was created with one important goal in mind: to make it easier to write large programs with fewer errors in the procedural programming paradigm, but without putting a burden on the writer of the C compiler, who is encumbered by complex language features. To this end, C has the following important features:  

* A simple core language, with important functionality such as math functions or file handling provided by sets of library routines instead
* Focus on the procedural programming paradigm, with facilities for programming in a structured style
* A simple type system which prevents many operations that are not meaningful
* Use of a preprocessor language, the C preprocessor, for tasks such as defining macros and including multiple source files
* Low-level unchecked access to computer memory via the use of pointers
* A minimalistic set of keywords
* Parameters that are always passed to functions by value, never by reference
* Function pointers, which allow for a rudimentary form of Closure and Polymorphism
* Lexical variable scoping
* Records, or user-defined aggregate datatypes (structs) which allow related data to be combined and manipulated as a whole
* Portability, in the strict sense that standards-compliant and portably written C programs can be compiled for a very wide variety of computer platforms and operating systems with little or no change to its source code  

Some features that C lacks that are found in other languages include:  

* Type safety
* Garbage collection
* Classes or objects
* An advanced type system
* Closures
* Generic programming
* Operator overloading
* Native support for multithreading and networking (C11, while not yet widely supported by compilers, does have support for multithreading)  

## Roguelike Issues

---

A lot of RLs actually use "C++/--", which is basic C with some of the more useful [C++](c++.md) features. This list applies to those using strict ANSI C.  

Pros:  

* C compilers exist for almost every platform, and it is possible to write highly portable C code that runs almost everywhere.
* There are lots of open-source C RLs, so there are many examples.
* Because of its low-level nature, C has little built-in overhead which means it is a good choice if you program for platforms with limited resources.
* Interfaces exist for many graphics libraries.
* Lots of people have made successful RLs in C, so you can too!  

Cons:  

* C is designed for the structured, procedural programming paradigm. It is not a good choice if you want to use object-oriented programming techniques, although function pointers and void* pointers make it possible to construct your own object-oriented system.
* C is a bare-bones, low-level language with no built-in support for things like garbage collection, generics, XML handling, or thread synchronization. 3rd-party libraries exist for all of these features, though.  

## C Roguelikes

---

Being the default language for roguelike development in the early days of the genre, many of the "traditional" major roguelikes are written in C:  

* [Rogue](rogue.md) (1980)
* [Super-Rogue](super-rogue.md) (1983)
* [Hack](hack.md) (1984)
* [Larn](larn.md) (1986)
* [Rogue Clone](rogue_clone.md) (1986)
* [NetHack](nethack.md) (1987)
* [Umoria](umoria.md) (1988)
* [Omega](omega.md) (1980s)
* [Angband](angband.md) (1990)
* [Ularn](ularn.md) (1992)
* [ADOM](adom.md) (1994)
* [QHack](qhack.md) (1997)

These days it's much more common for a roguelike to be written in [Python](python.md) or [C++](c++.md), however some 21st century roguelikes are still written in C:

* [Martin's Dungeon Bash](martins_dungeon_bash.md) (2005)
* [Scrap](scrap.md) (2005)
* [2DRL in 2K](2drl_in_2k.md) (2006)
* [A Roguelike in less than 512 Bytes](http://locklessinc.com/articles/512byte_roguelike/) (2006)
* [Alan's Psychedelic Journey](alans_psychedelic_journey.md) (2008)
* [CryptRover](cryptrover.md) (2008)
* [Chickhack](chickhack.md) (2009)
* [NLarn](nlarn.md) (2009)
* [Tomb of Rawdin](tomb_of_rawdin.md) (2009)
* [Warp Rogue](warp_rogue.md) (2009)
* [Last-of-candle](last-of-candle.md) (2010)
* [Free-Like](free-like.md) (2011)
* [Rook](rook.md) (2011)
* [Regicide](regicide.md) (2011)
* [RevivedHack](revivedhack.md) (2011)
* [Ascension of the Drillworms](ascension_of_the_drillworms.md) (2012)
* [Cave Chop](cave_chop.md) (2012)
* [Sil](sil.md) (2012)
* [CoreRL](corerl.md) (2012)

Many traditional C roguelikes tended to use the [Curses](curses_library.md) library, however there are C bindings for [libtcod](libtcod.md). Another modern choice may be [SDL](sdl.md) perhaps using [SDL_ttf](sdl_ttf.md) to render text.  
