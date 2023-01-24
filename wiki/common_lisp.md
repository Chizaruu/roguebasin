# Common Lisp

---

**Common Lisp** is a multi-paradigm programming language, which was used for programming roguelikes several times.

## Known Common Lisp roguelikes

---

- [LAngband](langband.md)
- [zombies 1drl](http://groups.google.com/group/rec.games.roguelike.development/browse_thread/thread/5597ab6ddea1209a/407d6656e732b44d)
- [Blast Tactics](blast_tactics.md)
- [Void Mission](http://dto.github.io/notebook/void.html)
- [Sanctuary](http://dto.github.io/notebook/sanctuary.html)
- [Invader Tactics](invader_tactics.md)
- Laser Spigot
- [The Rougelike](the_roguelike.md)
- [The Sewer Massacre](the_sewer_massacre.md)
- [Urban Warfare: The Escape](urban_warfare_the_escape.md)
- [Menace of the Mines](http://sourceforge.net/projects/motm/)
- [detome](https://github.com/nowl/detome)
- [Lingwall](lingwall.md)
- [City of the Damned](city_of_the_damned.md)

## The advantages

---

The ANSI standard of Common Lisp was written about 20 years ago. Let's look at the advantages that Common Lisp had and still has over most other programming languages.

- Common Lisp is a high-level language. There is built-in support for various data structures: linked lists, arrays, hash tables, and so on, and the standard library contains a lot of useful functions to operate on them. Common Lisp is dynamically typed and garbage collected.
- Unlike the recent trend of "scripting languages", Common Lisp code is compiled, and may achieve performance comparable to C and friends.
- Common Lisp is multi-paradigm: its procedural, object-oriented, functional, meta, reflective and generic. That means it allows many features not found in other languages, such as first-class functions. Unfortunately, that makes it harder to learn than most languages. It has the most powerful object system known to mankind, CLOS, which supports multimethods, multiple inheritance, method combinations and is fully dynamic to boot.
- Lisp programs are usually developed interactively. Instead of the usual edit-compile-run cycle, Common Lisp developer incrementally compiles, recompiles and tests individual functions to save his time. Lisp's famous REPL prompt allows to execute any Lisp code in the middle of writing your program. Using such tools as Emacs-based SLIME allows for even more progressive techniques.
- The problem of repetitive code and "patterns" is solved by Lisp macros, which are much, much more powerful than their C counterparts.

As you can see, Common Lisp was way ahead of it's time in 80s, and it's still ahead now. Maybe you could use this alien technology to get ahead of other roguelike developers?

## Choosing an implementation

---

There are a lot of Common Lisp implementations, and they are mostly quite good. [SBCL](http://sbcl.sourceforge.net/) is an industrial-strength free implementation, and generally considered the best free Lisp for stuff like web-applications or where speed does matter. However, as a roguelike developer you'll want to choose [CLISP](http://clisp.cons.org/). It is somewhat slower than SBCL, but two things set it apart: it runs quite well on Windows, and the executables it produces are not very big (<10 mb, compressed to ~3mb), unlike mammoth-sized SBCL images. Anyway, if you'll stick to ANSI standard and portable libraries, there's little difference between various Lisps.

## Learning the language

---

[Practical Common Lisp](http://www.gigamonkeys.com/book/) is usually considered the best introductory book about CL. What's good about this book is how it really opens up the whole lot of possibilities, without the fawning advocacy that you'll find in some books on Ruby and similar trendy languages. Get a copy of [SLIME](http://common-lisp.net/project/slime/) (Superior Lisp Interaction Mode for Emacs), its a lisp-mode for the Emacs text-editor which'll make things easier. It may take some time to get comfortable to Emacs but it is surely a powerful tool for Common-Lisp developers.

Also check comp.lang.lisp Usenet newsgroup, which is frequented by the most skilled users of the language.

## Can I use [Curses](curses_library.md) on Lisp?

---

Yes, and you can use other C libraries (such as SDL and OpenGL) too. You can even use them more effectively, since you'll still have the advantages of interactive development (as with any Lisp program). This is done using Foreign Function Interface, such as [CFFI](http://common-lisp.net/project/cffi).

You'll have to write a library wrapper that maps the functions of C library to Lisp functions, or use someone else's work. The wrappers for SDL and OpenGL are easily found through Google. The wrapper for PDCurses (and other curses variations) was written by [Timofei Shatrov](timofei_shatrov.md) (get curses.lisp from the sources of his latest game). However it takes some work to set it up.

By now, I'll assume you're using [SLIME](http://common-lisp.net/project/slime/) with CLISP. The basic setup won't work here, because curses and Emacs don't play well together. Here's a workaround: start CLISP separately and type:

```text
(load "/path/to/asdf.lisp")
(load "/path/to/slime/swank.asd")
(asdf:oos 'asdf:load-op :swank)
(ext:saveinitmem "clispswank" :quiet t :norc t :executable t
- - - -   :init-function #'swank:create-server)
```

Now, you'll have executable file called clispswank somewhere. Run it, and in Emacs type M-x slime-connect and press Enter until it connects. Now, you're in a good shape to start using curses. Load/compile CFFI, trivial-gray-streams and curses.lisp (you can automate this process by writing an appropriate asd file). Then you can start playing with it:

```text
(in-package :curses)
(connect-console)
(erase)
(attrset :cpurple)
(mvaddstr 2 2 "Hello World!")
(refresh)
```

Look how the text appears in the other window. You can start from here and incrementally develop the entire roguelike!

### State of Lisp ncurses support in 2020

As of April 2020, known Lisp bindings for ncurses are [croatoan](https://www.cliki.net/croatoan), [cl-charms](https://www.cliki.net/cl-charms) and [cl-ncurses](https://www.cliki.net/cl-ncurses). croatoan and cl-charms are based on CFFI and actively developed, cl-ncurses was based on the less portable UFFI and has not been touched for some ten years now.

All three of them are available in the popular Common Lisp library manager [Quicklisp](https://www.quicklisp.org/beta/).

By using Quicklisp, the libraries can be installed as easily as calling

```text
(ql:quickload "croatoan")
```

Then, provided that ncurses is installed, the library can be used as easily as:

```text
(in-package :croatoan)

(defun test1 ()
  (with-screen (scr :input-echoing nil :input-blocking t :enable-colors t)
- (clear scr)
- (move scr 2 0)
- (format scr "Type chars. Type q to quit.~%~%")
- (refresh scr)
- (setf (color-pair scr) '(:yellow :red)
- -   (attributes scr) '(:bold))
- (event-case (scr event)
-   (#\q (return-from event-case))
-   (otherwise (princ event scr)
- - - -  (refresh scr)))))
```

Some examples can be seen here:

- [Menus and extended colors](https://imgur.com/iywc1iG)
- [Dialogs and forms](https://imgur.com/COlPTYG)
- [Video of the evolution game](https://www.youtube.com/watch?v=H-vb0tqkP-E) from the game book [Land Of Lisp](http://landoflisp.com/).

McParen (talk) 13:15, 23 June 2018 (CEST)
