# Roguelike engine

---

A [roguelike](what_a_roguelike_is.md) engine is not a playable game but rather an underlying structure that can power many different games. Roguelike engines exist to allow people to easily write new games without the hassle of coding things like [Line of Sight](line_of_sight.md) code, object handling, [RNG](random_number_generator.md), etcetera. The idea of a roguelike engine is a good one: roguelikes are too often [forked](<http://www.wikipedia.org/wiki/Fork_(software_development)>). If an engine is used, this can decrease the likelihood of forking. [Interactive fiction](http://www.wikipedia.org/wiki/Interactive_fiction) games, which are a non-roguelike type of adventure games, nowadays always use engines, the most common of which are [Inform](http://www.wikipedia.org/wiki/Inform) and the [Text Adventure Development System](http://www.wikipedia.org/wiki/TADS).

The most popular roguelike engine is [T-Engine](t-engine.md), the engine powering [ToME](tome.md), made by [DarkGod](darkgod.md) but which has other modules available.

Some other engines include (most abandoned for a decade):

- [libtcod](libtcod.md), which eschews engine development for a pure library approach and has seen widescale uptake among 7DRL participants.
- [Carceri](carceri.md) (abandoned)
- The [Ng Java Roguelike Engine](ng_java_roguelike_engine.md) (abandoned)
- [H-World](h-world.md) a pure game engine by Hansjoerg Malthener (abandoned)
- [Tenney Level Generator .Net](tenney_level_generator_.net.md) a new early stage library that is being written in C# for use in a Roguelike game. The author has decided to adapt the library to allow for more flexibility by other developers. (abandoned)
- The [Neon](neon.md) roguelike engine. (abandoned)
- [pYendor](pyendor.md) (abandoned)

While T-World is the most successful RL engine to date, it builds on the whole [Angband variant](list_of_angband_variants.md) explosion, which shows that a functional game with clean code can serve the same basic function as an engine. T-World developed as an engine only after ToME was established as a popular game. Various attempts at a roguelike engine have come and gone without any success, because there was no game to develop an audience that was thinking "Wouldn't it be cool if...". The comparison to IF games is somewhat misleading: IF games generally are not competing to find more interesting game mechanics, whereas roguelike developers are generally wanting to tinker with new magic systems, new combat systems, new this and new that, so that it is much harder for an engine to either not end up frustrating developers or requiring such complexity in designing a game with it to not represent an advantage over not using the engine in the first place. For these reasons, roguelike specific libraries may be a more promising route to making new game development easier. In any case, any engine developer should create at least one good game with his own engine, because if you can't show success using your engine, why should anyone else expect they can?
