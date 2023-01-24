# Platforms

---

A platform is an operating system family or device line upon which a Roguelike may be designed to be played on.

Common platforms are:

- [Windows](windows.md)
- [Linux](linux.md) - Going to support lesser [unices](unix.md) also if [Open Source](open_source.md).
- [Mac OS](mac_os.md)
- [Mac OS X](mac_os_x.md)
- [DOS](dos.md) - More portable than you think, thanks to [DosBOX](http://dosbox.org/), which runs on anything with support for [SDL](sdl.md). However, DOS can be painful to develop for (djgpp compiler, no scripting, no nothing, poor implementation of random()).
- [Browser](browser.md) - Roguelikes running in a browser through either [JavaScript](javascript.md), [Flash](actionscript.md) or other runtimes.

Roguelikes are usually made to be quite portable. Either if they use [Curses](curses_library.md) ([Ncurses](ncurses.md) on all unices, [PDCurses](pdcurses.md) on Windows) or tiles ([SDL](http://libsdl.org/) is used, which is avaliable on almost every platform) or even 3D ([OpenGL](http://opengl.org/) is available pretty much everywhere).

There's a new trend to develop for [Java](java.md) virtual machine. Using the JVM, your game will run on most platforms, but be warned: the experience isn't always first-class, and sometimes a user will need to install the JVM in order to play. Sometimes, specific Java libraries for drawing or sound are used, some of which require additional native libs, thus effectively defeating the cross-platform features of the JVM.

## See also

---

[Portability issues](portability_issues.md)
