# Go

---

> Company - Google
>
> [Influences](influences.md) - C, Pascal, Python, Smalltalk
>
> Updated - 2021-02-16 (1.16)
>
> Status - Stable
>
> [Licensing](licensing.md) - Open Source (BSD-style)
>
> [Platforms](platforms.md) - Linux, OS X, Windows, FreeBSD, Plan 9, Solaris
>
> Official site of Go

Go (or _golang_) is a general-purpose, statically typed compiled language, with garbage-collection and explicit support for concurrent programming, that has been created at Google by Rob Pike, Ken Thompson, and Robert Griesemer since 2007. It was born from the developers' frustration with developing concurrent server applications with C++.

First announced at [Google Tech Talks](https://www.youtube.com/watch?v=rKnDgT73v8s) (YouTube), in October 2009, version 1.0.0 was released in March 2012, and they currently follow a 6 month release cycle, with new major versions being released on February 1 and August 1, so the language should be a stable target for future applications.

## Roguelike Development

Go has a number of features that can be desirable to the roguelike developer:

- Easy to learn - a simple language with a small number of keywords.
- Garbage Collection.
- Fast - faster than Java in most use cases [(citation)](https://benchmarksgame-team.pages.debian.net/benchmarksgame/fastest/go.html), considerably faster than Python.
- Super fast compile speeds - often just a few seconds, which compared to compiling C++ code, is several orders of magnitude faster.
- Compiles to a native OS binary, allowing you to keep you source code private.

The language has a reasonably simple duck-typing-based interface system, which can lead to very clean reusable code.

The concurrent features such as channels and goroutines are powerful, but often are not much needed in main roguelike logic, which is generally very sequential. That said, several areas could gain some benefits, such as game initialisation, dungeon generation, user interface, and other features that can run asynchronously with the game logic.

One of the more recent additions to the language that may bring great benefits to roguelike developers is the ability to cross compile for different OS. This means that a game being developed on a macOS system, can compile for Windows and Linux without the need to boot into those operating systems. In the future it will also be possible to use Go to target Android devices.

There is also good FFI support for linking to external C libraries like SDL and Curses. Calling C code from Go is reasonably straightforward.

For Go developers who wish to use Curses, there is the [goncurses](https://github.com/rthornton128/goncurses) library, however a good alternative is the excellent [Termbox-Go](https://github.com/nsf/termbox-go), which provides a cross platform lightweight ncurses-like API written in pure Go.

[BearLibTerminal](http://foo.wyrd.name/en:bearlibterminal/) is compact, display-centered API for roguelike developers. It supports Go bindings officially.

If you don't want to rely on a binding or stay in the terminal you can try out [ramen](ramen.md). It's a simple console emulator written go and inspired by libtcod.

You can also try the [gruid](gruid.md) framework that offers terminal, SDL2 and browser drivers. It provides many additional features, such as pathfinding, FOV and map generation.

## Related Links

- [A Tour of Go at golang.org](https://tour.golang.org/)
- [How to Write Go Code at golang.org](https://golang.org/doc/code.html)
- [Effective Go at golang.org](https://golang.org/doc/effective_go.html)
