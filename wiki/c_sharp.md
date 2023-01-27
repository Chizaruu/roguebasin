# C Sharp

---

## Background

---

C# is an object-oriented programming language developed by Microsoft as part of their .NET initiative. It was designed to provide a balance of [C++](c++.md) with rapid development, [Visual Basic](visual_basic.md), Delphi, and [Java](java.md).

## Language Characteristics

---

C# is, in some sense, the programming language which most directly reflects the underlying .NET Framework on which all .NET programs run, and it depends strongly on this framework; there is no such thing as an unmanaged C# program. Its primitive datatypes are objects of the corresponding .NET types, it is garbage-collected, and many of its abstractions, such as its classes, interfaces, delegates, exceptions, and so on, expose explicit features of the .NET runtime.

Compared to C and C++, the language is restricted or enhanced in a number of ways, including but not limited to the following:

- C# was originally Windows-only and required the user to have [.NET framework](dotnet_framework.md) installed. However, the C# language is an open ECMA standard and can be reimplemented by anyone who chooses to; the [Mono Framework](http://www.mono-project.com/Main_Page) is a very full-featured system available for Windows, OS X and Linux, even able to run most .NET-compiled applications so long as these applications do not use Windows API calls directly. With the release of the .NET 6.0 runtime, C# became fully cross-platform.
- The Managed DirectX classes were designed for use in C# and make using DX easy.
- Raw pointers and unchecked arithmetic can only be used in a special unsafe mode. Most object access is done through safe references, which cannot be made invalid, and most arithmetic is checked for overflow.
- Objects cannot be explicitly freed, but instead are garbage collected when no more references to them exist.
- As in Java, only single inheritance is available, but a class can implement any number of abstract interfaces. This functions mainly to simplify the runtime's implementation.
- C# is more typesafe than C++. The only implicit conversions by default are safe conversions, such as widening of integers and conversion from a derived type to a base type. There are no implicit conversions between booleans and integers, between enumeration members and integers, no void pointers (although references to Object are similar), and any user-defined implicit conversion must be explicitly marked as such, unlike C++'s copy constructors.
- Syntax for array declaration is different ("int[] a = new int[5]" instead of "int a[5]").
- C# has no templates, but C# 2.0 has generics, and these support some features not supported by C++ templates such as type constraints on generic parameters. On the other hand, expressions cannot be used as generic parameters as in C++ templates.
- Full reflection is available.

Although C# is often considered similar to Java, there are also a number of notable differences with this language as well, including the following:

- Java does not have operator overloading.
- Java does not have an unsafe mode permitting native pointer manipulation and unchecked arithmetic.
- Java has checked exceptions, while C# exceptions are unchecked, as in C++.
- C# has a goto control flow construct not found in Java.
- Java uses Javadoc-syntax comments to automatically generate documentation from source files. C# uses XML-based comments for this purpose.
- C# supports checked arithmetic.
- C# supports indexers.
- C# greatly simplifies event-driven programming through language constructs like events and delegates.
- C# supports structures in addition to classes. Structures, known in the .NET Framework as value types, are comparable to C structures, in that they need not be heap-allocated and can limit the number of dereferences needed to access data; see value type.
- C# has a unified object model for value-types and objects (There is no difference between "int" and "System.Int32").

## Portability

---

C# is a ECMA and ISO standardized language. There are currently a number of C# implementations. More information can be found [here](http://en.wikipedia.org/wiki/C_Sharp#Implementations).

## C# Roguelikes

---

C# has only been used for a few roguelikes so far:

- Witherwyn
- URRogue
- Sane Roguelike
- Magecrawl
- Cryptband
- Legend of Siegfried
- Forays into Norrendrin
- Ali the Android
- Rogue Frontier

The site Evil Science contains a [small section](http://www.evilscience.co.uk/?cat=5) containing C# code samples for basic Roguelike concepts.

## C# Roguelike Library

---

SunshineConsole is a simple, portable, beginner-friendly library for ASCII display (using OpenGL) and input. (Other things like Field-of-View are beyond its scope.) It's built on top of OpenTK.

The Doryen Library (libtcod) is a library that provides a full console emulator, field of view, configuration file parsing, random number generation, and other things useful for a Roguelike, and has bindings for C#. It works under .NET and Mono. However, a download is only provided for a slightly older version of libtcod (1.5.1b) at the moment, and the documentation does not have complete examples for C#. (Also it may not work on OSX with Mono?)

Sharplike is an open source roguelike library written in C# for the .NET Framework and Mono Framework.

SadConsole is .NET library that can run on MonoGame or SFML. It provides an tile-rendering engine suitable for roguelike, text adventure, or console games. It also provides a text-based GUI.

RogueSharp is a free library written in C# which provides many utility functions to help speed up Roguelike development by dealing with map generation, field-of-view calculations, path finding, random number generation, goal maps and more. It doesn't handle display or IO.

RLNET is a open-source C# library for ascii and tile graphics and input.

RenderLike is an open-source C# library for emulating a terminal via OpenGL using MonoGame.

InfiniMap is a chunked-map library designed for large streaming worlds, supporting 2D and 3D environments.

GoRogue is a .NET Standard library written in C#, which provides numerous data structures an algorithms for common functionality, including field of view, sense mapping, map generation, random number generation, dice notation parsing, and more. It does not handle display.

### Deprecated

The RogueSDL library is designed to work with C#, via .NET and Mono, to provide developers with an easy-to-use, quickly implemented graphics and input system for a Roguelike. RogueSDL has been deprecated in favor of libtcod-net.
