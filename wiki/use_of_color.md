# Use of color

---

The most common display used in roguelike are monochrome icons with parameterizable foreground and background colors. This makes the choice of color important in making the visual look of the game usable and attractive. Roguelikes intended to be played on a console use a 16-color palette with four bits for setting the red, green, blue and "bright color" channels (the [IBM CGA palette](http://en.wikipedia.org/wiki/List_of_8-bit_computer_hardware_palettes#CGA)). Modern games generally use a graphical display, such as the [Doryen library](doryen_library.md) that allows the use of full 24-bit color for the tiles. Full-color graphics present more design options than the 16 color terminal games.

## Bright colors get noticed first

---

You probably want the player noticing the most important gameplay elements first when they glance at the screen. High-contrast elements, such as a white block on a black background or a bright yellow letter on a dark floor tile, draw the attention right away. A dark gray '#' next to a dark brown '\*' won't distinguish itself that much.

Think of what the important elements currently on screen are. Probably walls that stop you from moving, dangerous enemies and interesting item drops. Color these in a way that make them stand out from the rest of the graphics. Regular floor, blood stains and junk items aren't interesting, and probably shouldn't be colored in a way that stands out.

## Color-blind players

---

A sizable number of people can't tell red and green apart. They tend to get annoyed at games where important information is conveyed by whether on-screen smudges are red or green.

## Miscellaneous

---

Proper writeup by someone who actually knows about this stuff would be welcome.

Blue is cold and red is hot, gradients might go from blue to red in addition to dark to light. Can something be done with [complementary colors](http://en.wikipedia.org/wiki/Complementary_colors)? Atmospheric scattering makes very distant real-world objects more blue, this can be used in graphics.

## External links

---

- [Article on different types of color contrast](http://blog.designedlykristi.com/?p=531)
- [How to Choose Colours Procedurally](http://devmag.org.za/2012/07/29/how-to-choose-colours-procedurally-algorithms/)
- [Color blindness tools](http://www.colblindor.com/color-blindness-tools/)
- [Color Scheme Designer webapp](http://colorschemedesigner.com/)
- [Color in games: An in-depth look](http://www.gamasutra.com/blogs/HermanTulleken/20150729/249761/Color_in_Games.php)
- [Visual Hierarchy for Game Developers](http://purplepwny.com/blog/visual_hierarchy_for_game_developers_a_practical_guide_to_making_important_stuff_seem_important.html)
