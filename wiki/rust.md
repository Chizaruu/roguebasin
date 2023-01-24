# Rust

---

Rust is a systems programming language from Mozilla Research. It is a viable replacement for C++ and is used by Mozilla to write a next-generation web browser engine. Unlike most high-level programming languages that use garbage collection, Rust uses the scope-delimited memory management idiom of C++ with stronger enforcement at the language level. It uses an abstraction idiom similar to Haskell's type classes instead of OO, and supports reasonably extensive generic programming with type parameters that can be constrained to implemented traits. It has a macro system.

The language and type system have been designed to make it impossible to create memory safety violations outside regions explicitly marked as unsafe, while maintaining C++-style explicit memory handling. The cost for this is a somewhat complex core language with various smart pointer types and the occasional need for explicit lifetime annotations.

Writing FFI bindings to C code is easy. The [Cargo](https://crates.io/) tool handles builds and dependencies to third-party packages. There is built-in unit test and benchmark support.

## Rust Libraries

- [tcod-rs](https://github.com/tomassedovic/tcod-rs): libtcod 1.5.1 bindings for Rust, last git commit: August 29, 2018
- [rltk-rs](https://github.com/thebracket/rltk_rs): A libtcod-like library for Rust, last git commit: September 11, 2019

## Rust Tutorials

- [[3]](https://tomassedovic.github.io/roguelike-tutorial/) A tutorial based on the [Python libtcod tutorial](complete_roguelike_tutorial_using_python+libtcod.md), ported for Rust's [tcod-rs](https://github.com/tomassedovic/tcod-rs).
- [[4]](http://bfnightly.bracketproductions.com/rustbook/) A tutorial based on the [tcod+python tutorial](http://rogueliketutorials.com/tutorials/tcod/), using rltk_rs

## Rust Roguelikes

- [phage](https://github.com/rsaarelm/phage), 7DRL
- [rhex](https://github.com/dpc/rhex)
- [Roguelike Tutorial](https://github.com/tomassedovic/roguelike-tutorial), A port of the Complete Roguelike Tutorial, using python+libtcod.
- [rg](https://github.com/serprex/rg), still in very early stages

## References

- [Are we game yet?](http://arewegameyet.com/), collection of game development resources for Rust
- [A game developer's wishlist for Rust features from 2015](https://users.rust-lang.org/t/my-gamedever-wishlist-for-rust/2859)
- [Reddit thread about Chucklefish developing a commercial game in Rust](https://www.reddit.com/r/rust/comments/78bowa/hey_this_is_kyren_from_chucklefish_we_make_and/)
- [Catherine West: RustConf 2018 Closing Keynote: Using Rust for Game Development](https://kyren.github.io/2018/09/14/rustconf-talk.html) ([video](https://www.youtube.com/watch?v=aKLntZcp27M), [slides](https://kyren.github.io/rustconf_2018_slides/index.html)), works through various alternatives for game engine structure in Rust and ends up at an ECS architecture as the best option for a medium to large game in Rust.
