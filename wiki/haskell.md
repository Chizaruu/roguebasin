# Haskell

---

## Background

---

Haskell began as a research language but has evolved into a platform suitable for general-purpose development. Haskell programs are concise and expressive, minimizing the "semantic gap" between the programmer's intentions and the text of the source code. The most recent revision of the Haskell standard is Haskell 2010.

Haskell is purely functional, meaning that functions, in general, map inputs to outputs, but can not perform side-effects, such as writing to disk. This limitation is comparable to the decision to remove 'GOTO' from most modern programming languages -- it greatly eases the task of proving the correctness of functions.

Haskell libraries provide IO and mutable state using an abstraction called a Monad. A monad is a mathematical concept that captures our intuitive notion of imperative programming.

Haskell is non-strict (often called "lazy"), meaning that it does not initialize data structures in memory until they are specifically needed. For example, it would be possible to perform find-and-replace on a 20GB file represented by a linked list -- only a small working segment of the list would actually exist in RAM at any given time.

The GHC compiler features a rigorous optimization pass so that idiomatic Haskell runs at speeds comparable to C/C++.

Haskell has an advanced type system, featuring abstract data types, parametric polymorphism, type classes (type-indexed functions), associated types (type-indexed types), and existential quantification. Haskell uses type inference to determine the types of values that are not explicitly indicated by the programmer.

## Language Characteristics

---

- Purely functional
- Non-strict
- Static typing
- Closures
- Strong semantics for concurrent and parallel programming
- Platform independence
- Automatic garbage collection
- Foreign Function Interface to interoperate with C/C++

## Haskell Roguelikes

---

- [Roguestar](roguestar.md)
- [LambdaHack](lambdahack.md)
- [Allure of the Stars](allure_of_the_stars.md)
- [Mage](http://hackage.haskell.org/package/mage)
- [Space Privateers](space_privateers.md)

## Related Links

---

- [Web site](http://haskell.org/)
- [Glasgow Haskell Compiler](http://www.haskell.org/ghc/)
- [Haskell Platform](http://hackage.haskell.org/platform/)
- [Computer Language Benchmarks Game](http://shootout.alioth.debian.org/u64/benchmark.php?test=all&lang=ghc)
