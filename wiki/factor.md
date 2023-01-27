# Factor

---

**Factor** is a concatenative programming language by Slava Pestov. It is inspired by Forth, but includes garbage collection, dynamic typing, a modern object system and combinator words for expressing abstractions.

Factor code is compiled to x86 machine code by the Factor runtime. It supports Windows, Linux and Mac OS X platforms. The language features extensive metaprogramming capabilities, and large amounts of the language, including the object system and the native code generation, are implemented as libraries a programmer can manipulate if necessary.

The standard library includes a graphics/GUI toolkit which uses OpenGL. FFI to C is straightforward, and can be used to set up support for curses or libtcod. The library that comes with the language is comprehensive, and includes such gamedev-relevant features as vector and matrix arithmetic and A\*-search.

The language has software-based cooperative threads, but no actual multiprocess support as of 2010-11.

Redistributable Factor applications are made using Factor's deploy tool. This copies the standard Factor runtime executable and generates a custom Factor image which contains the application code into a standalone package.

## Related Links

- [Factor programming language official site](http://factorcode.org/)
- [Factor page on Wikipedia](<http://en.wikipedia.org/wiki/Factor_(programming_language)>)
- [Why I like Factor, a blog post](http://jedahu.blogspot.com/2010/08/why-i-like-factor.html)
