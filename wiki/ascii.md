# ASCII

---

**ASCII** is a type of display used in Roguelike games.

The typical ASCII display looks like the following:

```text
 ------                             -  Wall
 |....|      ############           #  Unlit hallway
 |....|      #          #           .  Lit area
 |.$..+########         #           $  Some quantity of gold
 |....|       #      ---+---        +  A door
 ------       #      |.....|        |  Wall
              #      |.!...|        !  A magic potion
              #      |.....|
              #      |..@..|        @  The adventurer
   ----       #      |.....|
   |..|       #######+..D..|        D  A red dragon
   |<.+###    #      |.....|        <  Stairs to a higher level
   ----  #    #      |.?...|        ?  A magic scroll
         ######      -------
```

This ASCII screenshot was taken from [Wikipedia's Roguelike page](http://en.wikipedia.org/wiki/Roguelike). Wikipedia is licensed under the [GFDL](http://www.gnu.org/licenses/fdl.html).

## History

---

ASCII (American Standard Code for Information Interchange) is a code used to represent romanic characters using 7 bits. It has been widely used in computers and the digital field in general since its creation. It's also known as ISO 646.

Most 8 bit codes (like the ISO 8859-1, ISO 8859-2, etc.) use it as a base for their lower-code characters, and extend it with their specific characters. Even [Unicode](unicode.md) uses it as a base.

On old terminals, the underscore "\_" is displayed as a left arrow, the caret "^" is displayed as an up arrow and the vertical bar "|" has a hole in the middle.

The ASCII standard was published by the United States of America Standards Institute (USASI) in 1968.

Nowadays other standards like [Unicode](unicode.md) are growing in use because of their multilingual capabilities (using 2 or more bytes)

## Implementation

---

There are two ways of implementing an ASCII display in a roguelike:

1. Use the ASCII console provided by the operating system. Since you will usually want your game to work in all major operating systems, you should use a portable screen-handling library. Most text mode roguelike games use the library called [Curses](curses_library.md); in fact, Ken Arnold who wrote Curses has also worked on [Rogue](rogue.md). Curses has been ported to most operating systems. [FreePascal](free_pascal.md) also provides its own ways to use the console.

2. Simulate the ASCII display using a graphical interface. Again, you will probably want to use a portable graphics library for this task, like [SDL](sdl.md).

Since both solutions have their advantages, the best solution usually is to write a wrapper around display routines, and make the rest of the game communicate only with the wrapper, not with the screen library itself. Then you can have several implementations of the wrapper using different techniques.

## Advantages of the system console

- The system console has some built-in features. The [Windows](windows.md) system console works in text mode (on full screen) or in windowed mode (allowing changing fonts, colors, and copying a fragment of text, i.e. making txt screenshots). In [Linux](linux.md), console games can be played in text mode, and also there are many terminal emulators in X11, which also allow changing fonts, having background pictures etc. If you implement your own console, all this functionality is lost, unless you implement it too.

- It is possible to play console games remotely through telnet/ssh.

- There exists some software that works with the system console, and would not work with simulated console. This software includes: [Necklace of the Eye](noteye.md) (a graphical frontend), ASCII movie recorders, screen readers (for blind people), and tools to read the game's screen and enter input automatically (based on a script or something). All of these could be useful for roguelike players.

- Some people cannot or don't want to run in a graphical mode.

- Some roguelike players are used to the system console and like playing games which use it, but refuse to play games which use simulated ASCII displays, because they don't have the same feel (some other people refuse to play ASCII games at all).

## Advantages of simulating the ASCII display

- You don't limit yourself to 96 characters of 7-bit ASCII, or 256 characters of some 8-bit extension (most terminals, excluding the popular Unix terminals such as rxvt-unicode, cannot display [Unicode](unicode.md)). You can use a much greater number of symbols while still maintaining a feel similar to ASCII (e.g. Greek letters for centaurs, Chinese for Chinese dragons, Thai signs for nagas, runes for trolls, Cyrillic for rusalkas, Hebrew for angels, Arabic for djinni, knots for quetzalcoatls, chess symbols for chess figures etc.).

- Most terminals provide 8/16 colors (with the exception of some, which support over 88 colors). Most displays used today can show 24-bit "truecolor", which means that 16,777,216 colors can be used in a simulated ASCII display.

- You can use some "special" effects, like having characters of different size, animations etc.

- The colors used by the Windows console by default are bad (but they can be changed).

- Some implementations of Curses under Windows are slow (although you can always try to use a different Curses implementation).

- There is no portable way to use the system console in [Java](java.md). JCurses exists and has been successfully used in some Java roguelikes, but using it means losing some portability.
