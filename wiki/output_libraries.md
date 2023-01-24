# Output libraries

---

It can be difficult to decide which output library to use when creating a Roguelike. There are many different ones available. Libraries differ in portability, ease of use, speed, and other factors. This article compares different options.

## Curses

---

[Curses](curses_library.md) is a library for I/O to a terminal. It's quite portable, and applications will often compile on different operating systems with little or no change. It is probably your best choice if you don't need more than 8 or 16 colors.

Advantages:

- Portable
- Relatively easy to use

Disadvantages:

- Limited by the player's and developer's terminal. Most terminals only allow up to 8 foreground colors and 8 background colors. (On these terminals, it's possible to get 8 more foreground colors by using the bold attribute.)
- May require special code for different terminals, for example code to support 256-color, 88-color and terminals that can change their colors at runtime.

## SDL

---

[SDL](sdl.md) is a cross-platform multimedia library. It has many features not found in other libraries on this page, and is portable (though it can be hard to install on Windows machines). It has bindings for several languages, including [Python](python.md).

Advantages:

- Many features
- Portable

Disadvantages:

- Somewhat difficult to use

## libtcod

---

[Libtcod](libtcod.md) allows 16 million colors, and larger consoles than available otherwise. It's portable, and includes a collection of FOV algorithims, noise generators for Perlin, simplex and wavelet noise, heightmap generators, random name generators, and other roguelike goodies. Unfortunately it takes some time to get used to.

Advantages:

- Portable
- 16 million colors
- Supports Unicode
- Many capabilities besides a command-line interface, like mouse support and a configuration file parser, and advanced features such as perlin noise

Disadvantages:

- Somewhat difficult to use
- The default font is hard to read, especially on large screens.

## RLLib

---

RLLib was developed by [Nathan Stoddard](nathan_stoddard.md). It isn't being developed anymore, because the author is now using [Python](python.md), but the newest version is [available for download](http://nathanstoddard.com/rllib/rllib.shtml), and it might be useful for some games. Note that it is different than Roguelikelib, which happens to have a similar name.

Advantages:

- 16 million colors
- Mouse support, an [RNG](random_number_generator.md), config file support
- Doesn't rely on any additional libraries
- Allows access to individual pixels, and drawing characters at any position on the screen
- A simple message printing system
- Relatively easy to use
- Hexagon support
- Simple graphics support

Disadvantages:

- Not easily portable to platforms besides [Windows](windows.md) and the X Window System.

## conio.h

---

This only works with [DOS](dos.md) and [Windows](windows.md) and hence should not be used for any game required to be portable.

Advantages:

- Easy to use

Disadvantages:

- Unportable

## Windows API Calls

---

It's possible to call the Windows API functions directly. As you can tell from the name this only works with Windows and is not portable to other platforms.

Advantages:

- Fast

Disadvantages:

- Very hard to use
- Unportable

## LÖVE2D

---

[LÖVE2D](https://love2d.org/) is a [Lua](lua.md) graphics framework.

Advantages:

- Fast
- Portable (Mac, Windows, Linux, Android, iOS...)
- Strong support for touch devices and other modern features
- Well documented

Disadvantages:

- Lua only
- Historically rapidly evolving, a lot of code you find is not up to date
- A fair portion of development is commercial, so better code can be hard to find
