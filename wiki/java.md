# Java

---

## Background

---

Java is a reflective, object-oriented programming language initially developed by James Gosling and colleagues at Sun Microsystems in 1991, as part of the Green Project. It was initially called Oak, and was intended to replace [C++](c++.md), although its feature set resembles more that of [Objective-C](objective-c.md). Sun Microsystems is currently owned by the Oracle Corporation, which maintains and regularly updates the Java platform.

Java should not be confused with [Javascript](javascript.md), a scripting language with which it shares only the name and a similar [C](c.md)-like syntax. The Java Runtime Environment (JRE) does contain a JavaScript interpreter, however, based on Mozilla Rhino.

Specifications of the Java language, the JVM (Java Virtual Machine) and the Java API are community-maintained through the Sun-managed Java Community Process.

After first being made public in 1994, it achieved prominence following the announcement at 1995's SunWorld that Netscape would be including support for it in their next version of the Navigator browser.

## Language Characteristics

---

- Object orientation
- Platform independence
- Automatic garbage collection

See [The Choice of Java](the_choice_of_java.md), which contains a more detailed evaluation of the language.

## Roguelike Development

---

Pros Java has excellent features for roguelike development:

- Object Oriented
- Platform Independent
- Garbage Collected (i.e. no need to manage memory by hand)
- Ability to run on a web page as an applet.
- Generally easier to learn and use than other high-level languages like C and C++.
- Serialization to files makes it easier to implement and maintain game save and load.
- For developers of tiled roguelikes, the AWT and Swing libraries provide an excellent alternative to the 3rd party (or native platform) graphics libraries used in C and C++ since the developer can ensure that these libraries will be present in every Java implementation.

Cons However, it still has some disadvantages:

- No native console support. Developers of [ASCII](ascii.md) roguelikes must decide whether to
  - Use the JNI for traditional curses implmentation ([Java Curses Implementation](java_curses_implementation.md))
    - Existing libraries([JCurses](https://sourceforge.net/projects/javacurses/), [Charva](http://www.pitman.co.za/projects/charva/index.html) or [libjcsi](libjcsi.md))
    - Develop a new library (e.g. wrapping the [Curses](curses_library.md) C library and native methods)
  - Emulate console output in platform independent AWT or Swing components (see [Blacken](blacken.md), [SquidLib](squidlib.md), and [Zircon](zircon.md)).
- Longer initial program startup time. Java has no issues with speed once running [[1]](http://www.idiom.com/~zilla/Computer/javaCbenchmark.html), especially for RL games.
- Dependency on a Java Runtime Environment (JRE)
  - Ahead Of Time (AOT) compiling to a specific OS is possible, e.g. using the [GNU Compiler for Java](http://gcc.gnu.org/java/) (GCJ). A Windows implementation is available with MinGW [[2]](http://www.thisiscool.com/gcc_mingw.htm).

## Java Roguelikes

---

The features of Java have led many roguelikes to adopt this language:

- [Attack The Geth](attack_the_geth.md)
- [Beggar](beggar.md)
- [CastlevaniaRL](castlevaniarl.md)
- [Caverns of Underkeep](caverns_of_underkeep.md)
- [Crossfire](crossfire.md)
- [CryptRL](cryptrl.md)
- [Dimension Dungeon](dimension_dungeon.md)
- [Dweller](dweller.md) (using Java Micro Edition, J2ME)
- [Earl Spork](earl_spock.md)(using [Libjcsi](libjcsi.md))
- [EmoSquid](emosquid.md)
- [Excitable Digger](excitable_digger.md) (using [Libjcsi](libjcsi.md))
- [Expedition](expedition.md)
- [Fortress of the Goblin King](fortress_of_the_goblin_king.md)
- [Fruits of the Forest](fruits_of_the_forest.md)
- [Guardian Angel](guardian_angel.md)
- [HexRogue](hexrogue.md)
- [Hokuto no Rogue](hokuto_no_rogue.md)
- [JADE](jade.md)
- [Javelin](javelin.md)
- [Kalyp](kalyp.md)
- [Kamyran's Eye](kamyrans_eye.md) (for mobile phones)
- [Legerdemain](legerdemain.md)
- [MegamanRL](megamanrl.md)
- [MetroidRL](metroidrl.md)
- [MIDP RL](midp_rl.md)
- [Mt. Drash: the Roguelike](mt_drash_the_roguelike.md)
- [Neon](neon.md)
- [Netwhack](netwhack.md)
- [Pixel Dungeon](pixel_dungeon.md) (for Android)
- [Plutonium roguelike](plutonium_roguelike.md)
- [Quarker](quarker.md)
- [QuickHack](quickhack.md)
- [Roguelike Runner](roguelike_runner.md)
- [Sigmore Mines](sigmore_mines.md)
- [Sigmore Mines 2](sigmore_mines_2.md)
- [Tower](tower.md)
- [Tower of Druaga RL](tower_of_druaga_rl.md)
- [Tyrant](tyrant.md)
- [Valley of Ge-Hinnom](valley_of_ge-hinnom.md)
- [Wayfarer](wayfarer.md) (Written in [Processing](http://www.processing.org/))
- [ZeldaRL](zeldarl.md)
- [a small freeware roguelike applet created by Will Thimbleby](http://will.thimbleby.net/roguelike/)

## Related links

---

- [The Choice of Java](the_choice_of_java.md)
- ["Java programming language" (Wikipedia article)](http://en.wikipedia.org/wiki/Java_programming_language)
- [List of Java Development Environments](http://gpwiki.org/index.php/Java:Development_Environments) on the [Game Programming Wiki](http://gpwiki.org/)
