# D

---

## Introduction

---

D is static, compiled language influenced by [C++](c++.md). It was created by Walter Bright, author of the first C++ compiler to compile directly to machine code without using C as an intermediate language.

D has an imperative core, but is a multi-paradigm language that includes support for object-orientated, functional, and generic programming.

## Advantages

---

- Compiles natively with performance comparable to C++ while still being garbage collected by default.
- Interfaces well with [C](c.md) libraries, without writing boilerplate or using a foreign function interface and can import headers unmodified - see <https://github.com/atilaneves/dpp>. Although C++ interfacing is more limited, it is available (see D Programming Language Specification: Chapter 33).
- Has a modern module system, no need to write header files or deal with a preprocessor.
- Syntax is familiar to [C](c.md), [C++](c++.md), [C#](c_sharp.md), [Java](java.md) etc programmers.
- Supports the functional programming paradigm better than other C-like languages, with features such as closures, delegates, transitive immutability, higher order functions, anonymous functions, and the ability to write compiler enforced pure functions.
- Supports the template metaprogramming thing, which C++ supports without really meaning to, in a way that's actually sane to use.
- DMD builds code incredibly fast, making compile-edit-run cycles comparable to dynamic languages. Build automation tools like make, etc. can be used in the same way as they are used in C/C++ projects.
- Knowledgeable, helpful community (including the D.learn forum for asking questions).
- Unicode native - call the ☃.melt() function, or instantiate a 💡!T; the basic string type is UTF-8.
- A package registry (<http://code.dlang.org/>)

## Disadvantages

---

- There aren't as many libraries available if you want pure D implementations.
- The language is mostly stable, but still has breaking changes on rare occasions (although these are preceded by deprecation warnings)
- Documentation sometimes abstruse or lacking examples; paucity of beginner learning resources.

## Compilers/Tooling

---

- DMD (reference compiler; bleeding edge feature support) - <http://dlang.org/download.html>
- GDC (GCC backend; strong optimization; targets the most platforms, but generally a bit behind) - <http://gdcproject.org/>
- LDC (LLVM backend; also strong optimizations, targets almost as many platforms as GDC but generally implements a bit newer version of the language) - <http://wiki.dlang.org/LDC>
- Dub (Package manager and build tool) - <http://code.dlang.org/download>
- IDEs and IDE plugins - <http://wiki.dlang.org/IDEs>
- Editor support - <http://wiki.dlang.org/Editors>
- Other stuff (debugging, profiling, fixup, etc.) - <http://wiki.dlang.org/Development_tools>

## Roguelike Libraries

---

- Bindings for [libtcod](libtcod.md) - <https://github.com/jaydg/libtcod-d>
- Interface to [Ncurses](ncurses.md) - <https://github.com/D-Programming-Deimos/ncurses>
- Bindings to [BearLibTerminal](bearlibterminal.md) - <https://github.com/Elronnd/bearlibterminal-dlang>
- Adam Ruppe's arsd collection (simpledisplay, terminal, eventloop, database, script, etc.) - <https://github.com/adamdruppe/arsd>
- Terminal ANSI colour lib - <http://code.dlang.org/packages/rainbow>
- Parser for Tiled maps - <http://code.dlang.org/packages/dtiled>
- ArmageddonEngine, D library for games, network applications etc. (lots of utility code) - <https://github.com/CyberShadow/ae>
- scone - cross-platform terminal application library - <http://code.dlang.org/packages/scone>
- [Necklace of the Eye](noteye.md) includes sample in D.

## D Roguelikes

---

- 7DRL [Infection](infection.md)
- αlpha [Arcan Myth RL](arcan_myth_rl.md)
- αlpha [Tetraworld](tetraworld.md)

## Links

---

- [Official Site](http://www.dlang.org/)
- [IRC channel](irc://irc.freenode.net/d)
- [wiki](http://wiki.dlang.org/)
- [DUB registry](http://code.dlang.org/)
