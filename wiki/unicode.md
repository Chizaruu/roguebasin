# Unicode

---

**Unicode** is a character encoding standard, similar in spirit to [ASCII](ascii.md) but encoding vastly more characters. Whereas there are only 94 printable ASCII characters, there are literally hundreds of thousands of printable Unicode characters.

Roguelike games are historically console-based games that use characters to represent monsters, treasures, and maps. While some developers now prefer to use graphical [tiles](user_interface_features.md) instead, Unicode allows roguelike games that use characters to represent things a much greater variety of visual representations.

Unicode is derived from a host of earlier character encodings, each of which added some characters to the ASCII set, but most of which added no more than 128 additional characters by using the high bit (8th bit). While ASCII with its 94 printable characters was a reasonable way to represent English text, these high-bit encodings, or code pages, each of which was ASCII plus some selected characters, were a reasonably complete way to encode one, two, or a few additional languages each. But it wouldn't work if you took the high-bit encoding that handled, say, Arabic characters and tried to use it to represent German text, which requires Roman characters with umlauts.

The trouble with these extensions was that there were so many to choose from. While characters 0-127 (which were encoded by ASCII) were always the same from computer to computer, characters 128-255 (which were encoded by whatever high-bit encoding was in use locally) were not. Assumptions about code pages being the same led to strange gibberish being displayed rather than the correct characters.

Unicode is an attempt to resolve this once and for all by using a larger character set of 1,114,112 (2^20 + 2^16) code points, allowing it to map all known human glyphs into one consistent character set. Nearly 10% of those code points are already assigned. Any Unicode character can be represented with 21 bits, although depending on what 'encoding' your local system uses the actual number of bits devoted to representing a particular character may be more or less. The first 127 code points, like the first 127 code points of all the ISO standards, are the same as in ASCII.

The majority of the most useful characters are encoded in the first 65536 locations of Unicode, which is called the Basic Multilingual Plane. Since this is the number of different bit patterns that can be represented with two bytes, some programming languages (such as Java) use two bytes as their basic character type.

## Unicode in Roguelikes

---

Many roguelikes, including [NetHack](nethack.md), have historically included options to take advantage of high-bit encodings to get extra symbols, allowing them to draw walls with lines rather than # marks. But if a different code page than the expected one happens to be loaded, the results can be humourous since the code points that the program expects to contain lines may contain, say, Arabic or Cyrillic characters instead. This can be seen when NetHack's IBMgraphics display is played with DOS code page 850.

Modern terminal programs, however, mostly use Unicode characters now rather than the various earlier code pages, so using Unicode characters allows roguelikes to access these special glyphs in a platform independent manner. It also allows for the use of glyphs from typically eastern languages (which may be ideal for those who can read those glyphs, as entire words may be represented as a single character).

Unfortunately, Unicode, by its "embrace every known glyph" mentality, can't guarantee that the font that you are using actually has the glyph defined.

It is surprisingly difficult to get a C program to use Unicode characters effectively in combination with a curses display, mostly due to inadequate documentation of the requirements. These requirements are listed at the page on [Ncursesw](ncursesw.md).

## Roguelikes using Unicode

---

Roguelikes that use Unicode are:

- [ChessRogue](chessrogue.md)
- [Legerdemain](legerdemain.md)
- [Dungeon Crawl Stone Soup](dungeon_crawl_stone_soup.md)
- [Neon](neon.md)

External links

---

- [Useful Unicode characters for Roguelikes](https://github.com/globalcitizen/zomia/blob/master/USEFUL-UNICODE.md) (c/Zomia .. not hosted here as Wikimedia breaks many Unicode characters .. contributions welcome)
- [Everything you wanted to know about Unicode but were afraid to ask](http://en.wikipedia.org/wiki/Unicode)
- [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](http://www.joelonsoftware.com/articles/Unicode.html)
