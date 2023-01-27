# Doryen library Reviews

---

Review of the [Doryen library](doryen_library.md) by PaulBlay

## Usage background

Preferred IDE : Visual C++ 2008 Express Edition

Preferred Platform : Windows XP

Other requirements sought : Unicode support / Works with SQLite

## Experience with Doryen library

libtcod (aka the Doryen library) is dependent on [SDL](sdl.md) dlls being made available to the project. The instructions are [available online](http://roguecentral.free.fr/libtcod/1.4/doc/doc.php) and are relatively clear. I had some trouble because the subsystem in linker settings must be set to "Not set" or "Console" while I had it at "Windows". No compiler warnings were prompted by including "libtcod.hpp". At first glance the functionality would appear to be quite impressive, although the implementation appears a little intimidating to someone at my (low) level of C++ 'expertise'.

## Conclusion

As soon as it gets to "TCODConsole" stuff it exits with the message "The thread 'Win32 Thread' (0x694) has exited with code 1 (0x1)". I'm going to put this on the backburner for now.

- This is in the [FAQ](http://doryen.eptalys.net/forum/index.php?topic=274.0), and it's usually because the font ("terminal.png") is not in the executable's directory. Jotaf 19:08, 21 July 2009 (UTC)
