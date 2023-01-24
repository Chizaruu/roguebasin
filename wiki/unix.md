# Unix

---

**Unix** is a family of operating systems conforming to the Single Unix Specification, ultimately based on the original AT&T Unix. Unix-like operating systems, such as various [BSD](bsd.md) derivates and [Linux](linux.md) distributions, follow most of the specification. UNIX is a [trademark of The Open Group](http://www.unix.org/trademark.html); most Unix systems are [not UNIX](http://www.netbsd.org/Misc/call-it-a-duck.html).

Unix is the origin of the [C](c.md) programming language, the [curses library](curses_library.md) and the roguelike genre of games. The original [_Rogue_](rogue.md) rose to fame as a binary distributed with the [BSD](bsd.md) variant of Unix.

## Porting a roguelike to Unix

---

If your roguelike game is a C program, then to port it to Unix, you must provide the source code to the public. This is necessary to allow everyone to recompile the roguelike game for their Unix system and their processor architecture.

Today, Unix and Unix-like operating systems, for instance, [FreeBSD](http://www.freebsd.org/), [NetBSD](http://www.netbsd.org/), [OpenBSD](http://www.openbsd.org/), [Minix](http://www.minix3.org/), [OpenSolaris](http://www.opensolaris.org/) or [GNU/Linux](http://www.gnu.org/), are freely available. Many players and developers use them.

Some roguelike developers only distribute a Linux/i386 binary. “Linux/i386” refers to the Intel x86 or IA-32 architecture found in common personal computers. However, some players have Linux on other types of computers, such as [PowerPC](http://www.penguinppc.org/). Some players have Unix flavors other than Linux, such as the \*BSD systems. **There is no single Unix binary format**. A Linux/powerpc computer cannot a play a Linux/i386 game. An OpenBSD/powerpc computer cannot play a Linux/powerpc game.

There are inconvenient workarounds, such as emulation, but confining players to Linux/i386 will lock out many potential Unix players.

## Source-compatility

---

In the 1980s, the many BSD and SYSV variants introduced incompatibilities to Unix. Today, Single Unix Specification has brought more uniformity to things. Most C functions that a roguelike programmer needs should be everywhere.

For example, C programmers on both Unix and not-Unix platforms may use the rand function to generate random numbers, and srand and time to seed the [random number generator](random_number_generator.md); you would #include <stdlib.h> and #include <time.h>. Because rand is a bad generator on many systems, you may prefer to use the random and initstate generator on Unix. (Integration and use of the [Mersenne twister](mersenne_twister.md) may the key to giving reliable random numbers to a roguelike game on any operating system.)

Avoid non-portable Unix functions, such as random_r or arc4random, mempcpy or strlcpy, unless you are prepared to write configure tests for these functions and to provide substitutes should they be missing. The [GNU C Library Manual](http://www.gnu.org/software/libc/manual/) (“info libc” on a GNU system) describes quite well which functions are portable. If you are reading the BSD or Solaris manual pages, then functions attributed to ANSI C, XPG4, POSIX, or the Single Unix Specification are portable. Some functions from 4.4BSD, including snprintf, are now portable.

The worst portability problems will happen if you need to create a pseudo-terminal (as sshd and xterm do), create a networking interface, or support old Unix systems. A roguelike game will rarely come close to doing anything of that sort. In general, **a roguelike developer can program for one flavor of Unix and not worry about the others.**

If you need to draw ASCII graphics on the terminal, then use the [curses library](curses_library.md)! Programming with curses is very consistent across platforms; the main difference is that Linux and Darwin users will link with -lncurses while most other Unix users will link with -lcurses.

## Diversity of interfaces

---

Unix has a diverse set of libraries and environments to program in. [_Angband_](angband.md), [_NetHack_](nethack.md), and their variants take the most advantage of this, providing multiple frontends or window ports.

The graphical environment on most Unix systems is the [X Window System, Version 11](http://www.x.org/). The main-x11.c frontend for Angband draws the game using X11, enabling new features like tiles and multiple windows. The x11 window port of NetHack provides similar features, but requires the Xaw widget set bundled with XFree86 or Xorg.

[Qt](qt.md) and [SDL](sdl.md) interfaces to both Angband and NetHack also exist. Both Qt an SDL are portable across X11, Mac OS, and Microsoft Windows! Qt and SDL also add features that plain ancient X11 lacks.
