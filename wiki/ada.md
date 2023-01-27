# Ada

---

Ada is a compiled, structured, statically typed, imperative, and object-oriented high-level programming language, extended from Pascal and other languages.

## Language origins

---

Ada began its history as a military programming language designed mostly for embedded and real-time systems but over time it evolved into more general purpose language. Its name is after Augusta Ada Lovelace - one of the first known computer programmers. Also, the first standard of language was named in honor of Lady Ada - MIL-STD-1815 (1815 is the year of birth of Ada Lovelace) released in 1980. All next versions are named Ada[year of release]. The newest version of the language standard is Ada2012. Today Ada is used not only in military projects, but also very often in commercial solutions where high safety and security is crucial (like avionics or medical). And can be a quite good language to write roguelikes too.
Main characteristics

- Expansible type system - Ada allows to create own types of data which can be or completely a new type or subtype of existing type. For example, Ada provides an Integer type (same as int in C) which has two subtypes: Natural - from 0 to last Integer and Positive - from 1 to last Integer.
- Very strong static typing - Ada doesn't allow to make any operation on different types of variables without implicit casting/conversion. For example, it is impossible to add Natural value to Positive value without casting one of them to the second.
- Focused on reading a code than writing - Ada prefers to use English words (like begin, end, or, and then) than mathematical symbols.
- Build in support for interfacing with code written in C, C++, Fortran and COBOL - it is very easy to use a code written in these languages in an Ada code.
- A lot of compile and runtime checks - Ada compilers do many tests during compilation and adds others tests to programs to eliminate many bugs (like buffer overflow, off-by-one or range violations)

## Ada from roguelike development point of view

---

The biggest problem with Ada as a language for writing roguelike is lack of libraries which can boost the creation process. Fortunately, due to the very good interfacing level with C or C++ it is easy to use libraries like libtcod or SDL in an Ada code. Also there are available bindings for many libraries (for example ncurses) which can be easily used in creating an Ada roguelike.

The biggest advantage is that Ada allows to create a high-level code, on level comparable with Python or Java which make creating a roguelike a lot easier than with lower level programming languages. Ada offers, for example, dynamic containers (like Python list or tuples), high level text operation (like Java strings) or generics (like templates in C++). Also, because Ada is a lot faster than Java or Python, there should be less problems with performance and computer resources usage (like memory or CPU). Theoretically, an experienced programmer can make an Ada code as fast as C (by disabling runtime checks) or even with smaller memory usage than C (by packing variables).

## Ada roguelikes

---

- [Steam Sky](steam_sky.md)

## Related links

---

- [Wikipedia article about Ada](<https://en.wikipedia.org/wiki/Ada_(programming_language)>)
- [A curated list of awesome resources related to the Ada and SPARK programming language](https://github.com/ohenley/awesome-ada)
