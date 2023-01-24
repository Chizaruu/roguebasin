# Ruby

---

## What is Ruby?

"Ruby is...a dynamic, open source programming language with a focus on simplicity and productivity. It has an elegant syntax that is natural to read and easy to write." It is a pure object orientated language (in the vein of [Smalltalk](smalltalk.md), rather than Simula), but also supports procedural and functional programming.
Roguelike Specific Details

Ruby is still coming of age in the roguelike field -- there are no roguelike libraries and only a few finished games in the language. That said, Ruby is very powerful, combining an elegant syntax with an incredibly dynamic object system. It's also incredibly fun to code with.

### Libraries

- [Gosu](http://www.libgosu.org/), a 2D game development library
- [Chingu](http://ippa.se/chingu), a game framework that extends Gosu with higher level features
- [Ncurses](ncurses.md), cross-platform, true [Curses](curses_library.md) support (built-in Curses support has no colours under Windows and is generally a mess)

### Interfacing with C

Ruby MRI, the canonical and most popular Ruby distribution, is implemented in [C](c.md) and can make use of C libraries and extensions relatively easy. This is great for leveraging libraries such as [libtcod](libtcod.md), as well as for writing performance and/or memory critical parts of your application.

- [Extending Ruby - The Pragmatic Programmer's Guide](http://www.ruby-doc.org/docs/ProgrammingRuby/html/ext_ruby.html) A guide to writing C extensions for Ruby. The overhead of this is minimal, but it does require a modicum of boilerplate.
- [RubyInline](http://www.zenspider.com/ZSS/Products/RubyInline/) Is a third party module that allows you to inline C code in pure ruby. The overhead is around 2\*.

### ncurses-ruby

It is possible to use the built-in curses on linux and other platforms, and interface with the Win32 console via the Win32 API for Windows support. However, this is a relative pain. Instead, install the non-standard ncurses-ruby package!

On Windows:

- Download ncurses-ruby1.8-0.9.1-i386-mswin32.zip from <http://ncurses-ruby.berlios.de/>
- Copy lib/ncurses.rb and ncurses.so into your code directory (don't copy the lib directory itself!)
- In your script, require 'ncurses'

That's it! You can now use ncurses (see the zdennis's ncurses examples at <http://github.com/zdennis/ncurses_examples/tree/master> or the C API, which is very similar). At some point an example roguelike using ncurses will be uploaded.

### Line of sight

[Here](breshenhams_line_algorithm.md) is a Bresenham's Line Algorithm implementation in Ruby.

### Field of View

A few [field of view](field_of_view.md) algorithms have been implemented in Ruby

- [Ruby shadowcasting implementation](ruby_shadowcasting_implementation.md)
- [Ruby precise permissive FOV implementation](ruby_precise_permissive_FOV_implementation.md)

### Bitfields

For certain intensive operations it may be prudent to use a [Bitfield](ruby_bitfield.md). One example is storing which map tiles have been visited by your player (and will be drawn).

Using this library, it is easy to add methods to your Map class for FOV calculation:

```ruby
def initialize(map)
  ....
  @visited = BitField.new(@width * @height) # visited by player (drawn in grey)
  @lit = BitField.new(@width * @height) # seen by player this round (drawn in white)
end

# Return true if the square is lit (seen by player)
def lit?(x, y)
  @lit[y * @width + x] == 1
end

# Returns true if square has been visited by player
def visited?(x, y)
  @visited[y * @width + x] == 1
end

# Set lit status for square. Also visits the square
def light(x, y)
  idx = y * @width + x
  @lit[idx] = @visited[idx] = 1
end

# Unlight everything (call after doing FOV calc + map draw)
def reset_light
  @lit.clear
end
```

### Other Resources

Over on [RubyQuiz](http://rubyquiz.com/) there are several quiz solutions which would be useful to anyone wanting to use Ruby for terminal RL games;

- [Sokaban](http://rubyquiz.com/quiz5.html). Full game with examples that use Gosu, Curses, OpenGL and plain old terminal. James's 'unix' entry could be useful for a pure ASCII RL jump off point.
- [AD&D Dice Roller](http://rubyquiz.com/quiz61.html). Dennis Ranke's full_parser_roll.rb is very interesting.
- [Dungeon Generation](http://rubyquiz.com/quiz80.html).
- [Maze Generation](http://rubyquiz.com/quiz31.html)

## External links

- [Official Ruby website](http://www.ruby-lang.org/)
- [Ruby API Documentation](http://ruby-doc.org/)
- [The Ruby Toolbox](http://ruby-toolbox.com/categories/game_libraries.html) Listing of current Ruby game libraries and their popularity.
- [RubyMotion](http://www.rubymotion.com/), develop OSX, iOS, and Android apps/games using Ruby.
