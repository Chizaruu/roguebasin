# JavaScript

---

JavaScript (often abbreviated to JS) is a scripting language used by most web browsers. Roguelikes can also use it as a scripting language (e.g. for scripted events or quests). This allows changing game behavior without recompiling. Some browser-based roguelikes are written in JavaScript or its Microsoft relative JScript (and JScript.NET). With the advent of Node.js, io.js and v8, JavaScript can be used outside of the browser as well.

## Development Hints

- Use [jsLint](http://www.jslint.com/) to avoid many mistakes that are normally caught by a compiler
- JavaScript frameworks abstract many of the browser inconsistencies. ([MooTools](http://mootools.net/), [JQuery](http://jquery.com/))
- Take advantage of Firebug for Firefox, the Web Inspector for Chrome and Safari and the Developer Tools in Internet Explorer (version 8 and above)
- Full IDEs are available including Microsoft Visual Studio Web Developer Express, Aptana and NetBeans.
- [Rot.js](rot.js.md) is a JavaScript roguelike library with many useful features.

## Advantages

- Scripting languages usually have a faster feedback loop between development and viewing the changes.
- Closures are a powerful language construct that can simplify application structure and enhance readability.
- Any user with a modern browser (on any platform) can run your game.
- A whole new world of easy-to-access graphical possibilities with the DOM or <canvas>.
- Working in a dynamic and garbage collected language takes a lot of the pain out of strong typing and memory management.
- Well supported serialization of data structures using [JSON](json.md).
- Emerging HTML5 standards provide support for local storage of data.
- Emerging web application stores, such as the Google Chrome Store, provide a distribution platform.

## Disadvantages

- Many errors that are caught at compile-time in a strongly typed language will be caught at run-time.
- Existing source for most roguelikes is in C or C++, and not all idioms translate into JavaScript.
- There are browser differences in JavaScript implementations and javascript speed, so true portability still takes work.
- JavaScript is much slower than C and C++ (however, not all roguelikes require blazing speed).
- Full file system access requires additional plugins.
- JavaScript, owing to its troubled past, has more than the average share of design flaws and overall quirkiness.

## HTML and CSS

The collection of web standards that is sometimes loosely referred to as HTML offers a multitude of methods to interface with the user.

### HTML, CSS and the DOM (Document Object Model)

This approach is also informally known as DHTML, for dynamic HTML. It involves using the functionality of HTML elements and their styling together with JavaScript to create a dynamically-changing web page. Traditional DHTML is only of utility to very simple games, since it is not possible to escape the limits of HTML markup, which was not designed to make program interfaces, but static documents.

### Canvas 2D context

Canvas 2D that emerged in 2004 from Apple, and is currently supported by all popular modern web browsers (with Internet Explorer being an exception up to version 9). Canvas 2D in itself is a rather simple 2D API with a set of basic functions for drawing lines, arcs, curves and polygons. The API is packaged in the form of the <canvas> element, whose context can be acquired and modified by JavaScript.

### Canvas WebGL context

The WebGL context of the <canvas> element is a later invention that appeared in 2006 at Mozilla. It is an API based on [OpenGL](opengl.md) ES 2.0, and it was designed to make hardware graphics acceleration accessible to web content. It was designed with 3D rendering in mind, and as such implements a more complex interface than Canvas 2D.

### SVG and its DOM

Animating SVG with JavaScript via its DOM is the rarest method of building an interface with the user. Although SVG was designed to create graphical presentations, the complexity of using its DOM makes it similar to DHTML in many respects.

## Roguelikes in JavaScript

- [Ananias](ananias.md)
- [Cardinal Quest](cardinal_quest.md) (original concept was JavaScript)
- [Cave of Epokothar](cave_of_epokothar.md) (HTML5 and Canvas)
- [Demon Tactic](demon_tactic.md) ([PHP](php.md) + JavaScript)
- [DungeonEpic](dungeonepic.md) A roguelike game and Roguelike builder that runs in the browser
- [FARA](fara.md)
- [Gone Rogue](gone_rogue.md)
- [gTile](gtile.md)
- [jsMoria](jsmoria.md) (JavaScript port of the roguelike classic [Moria](moria.md), uses [MooTools](http://mootools.net/))
- [js-like](js-like.md)
- [Neon](neon.md) (uses JavaScript for scripting)
- [Overworld](overworld.md)
- [PonyRL](ponyrl.md)
- [roguezombies](roguezombies.md)
- [RailRL](railrl.md)
- [Rogue Fable III](rogue_fable_iii.md)
- [Rogue Sector](rogue_sector.md) 7DRL
- [Ruins of Kal Raman](ruins_of_kal_raman.md) (HTML5 and Canvas) 7DRL
- [Saege](saege.md) (not yet released)
- [Shambletown](shambletown.md)
- [The Seven Day Quest](the_seven_day_quest.md) 7DRL
- [Succession](succession.md) 7DRL
- [Wayward](wayward.md) βeta

## JavaScript Roguelike Libraries

- [Rot.js](rot.js.md)
