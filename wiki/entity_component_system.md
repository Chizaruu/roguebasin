# Entity Component System

---

An entity component system is a way to implement your game objects so that you can build their functionality through composition instead of object-oriented inheritance. The prevailing wisdom in current game development seems to be that complex games should have one of those instead of an inheritance-based object system that is likely to lead to unmaintainable blobs of a class. A roguelike is probably a complex enough game that you should use an entity component system in one.

The basic idea is that game objects at their core are just unique identifiers (UIDs), plain strings or integers, instead of complex objects. The complex data and functionality is moved into multiple components, which are stored in their own containers and associated with the object UIDs.

This makes it easy to have data-driven object construction and allows very diverse combinations of component loadouts in objects that still coexist in the same game world as the same fundamental type of program object.

## References

---

- Good overview and links on Stack Overflow: <http://stackoverflow.com/questions/1901251/component-based-game-engine-design>

- [A Handful of Components](http://web.archive.org/web/20140719094839/http://www.altdev.co/2011/10/08/a-handful-of-components/), descriptions of some useful components by Michael A. Carr-Robb-John

- [Entity Systems Project](http://entity-systems.wikidot.com/), an entity component system wiki

- [Entity Systems: what makes good Components? good Entities?](http://t-machine.org/index.php/2012/03/16/entity-systems-what-makes-good-components-good-entities/), a post describing an architecture that has both entity components and global system objects for the game state

- [Case Study: Bomberman Mechanics in an Entity-Component-System](http://www.gamedev.net/page/resources/_/technical/game-programming/case-study-bomberman-mechanics-in-an-entity-component-system-r3159), a post on actually implementing a small ECS.

- [Game Programming Patterns: Component](http://gameprogrammingpatterns.com/component.html), a straightforward explanation of component based architecture, along with example implementation.

- [Brian Bucklew: Data-Driven Engines of Qud and Sproggiwood](https://www.youtube.com/watch?v=U03XXzcThGU), IRDC 2015 talk about a data- and component-driven roguelike architecture.

- [Catherine West: RustConf 2018 Closing Keynote: Using Rust for Game Development](https://kyren.github.io/2018/09/14/rustconf-talk.html) ([video](https://www.youtube.com/watch?v=aKLntZcp27M), [slides](https://kyren.github.io/rustconf_2018_slides/index.html)), works through various alternatives for game engine structure in Rust and ends up at an ECS architecture as the best option for a medium to large game in Rust.
