# User interface features

---

The user interface allows the player to interact with the game.  

## Display

---

[ASCII](ascii.md) or graphical tiles are commonly used ways to represent the game world; however, you can also use any techniques from other computer games genres, or even try to develop your own.  

### Character display

---

A character display (also textual display) is an output format that relies on characters to represent information; character displays were the main output format for all software in the 80's, where using extended [ASCII](ascii.md) it was possible to create simple menus and graphic user interfaces, even using the mouse.  

The character display in roguelikes is, however, different: instead of representing words or graphical components of a graphic user interface, each character has a meaning on its own. This is one of the [basic characteristics](what_a_roguelike_is.md) of roguelikes, although it is not a deciding one as the developer may not feel comfortable with it and would instead go with a graphical display. There are some adventure and role-playing games that have a character display similar to the one roguelikes use, but it doesn't qualify them as roguelikes, as they are missing other most important features.  

A typical roguelike ASCII display is the following:  

```text
            ######       # A wall
######      #....###     . The floor
#....#   ####.@..+..     + A closed door
#.<..#####.....g.###     / An opened door
#.......#######       < Stairs to the previous level
#.?..#######             
######                   @ The hero
                         g A monster
                         ? A magical item
```  

Some ASCII characters have the same meaning in nearly every roguelike. `.` is used for floors, lit areas, or somewhere the player can walk on. In [dungeons](dungeon.md), `<` and `>` are stairs to the previous and next level. `@` usually is the hero and letters from `a` to `z` (lower case and capital letters) the monsters. `?` is common for scrolls, `!` for potions.  

### Graphical display

---

Graphical displays (a misnomer when text is one kind whereof, as opposed to sprite) manipulate pixels directly, rather than rendering them as characters of text. This approach gives much more flexibility than pure text, since shapes can be modified before drawing them on the screen. Graphical displays often use tiles (small pictures) instead of text symbols.  

If you want to use graphical tiles in your roguelike, you might find the [list of tilesets](finding_graphical_tiles.md) helpful.  

## Movement keys

---

### Numpad

The numeric pad on most [keyboards](keyboard.md):  

```text
7 8 9
 \|/
4-5-6
 /|\
1 2 3
```  

On US and UK keyboards (not sure about others), 7 doubles up as 'Home', 1 as 'End', 9 as 'Page Up' and 3 as 'Page Down', while 8, 4, 6 and 2 double up as the arrow keys Up, Left, Right and Down.  

The 5 key usually means to stay in the same place.  

But be warned that some keyboards, especially some laptop-keyboards wholly omit the numpad so be sure to always provide an alternative to numpad (such as the vi-keys given below).  

### Vi keys

Keyboard controls for moving the cursor in Vi(m) and some Roguelikes.  

```text
y k u
 \|/
h-.-l
 /|\
b j n
```  

The keys are laid out so that 'hjkl' are all on one line of the keyboard. (This may not be the case on non-us keyboards). As they are (supposed to be) on the keyboard:  

```text
y u
 h j k l
b n
```  

The '.' key is often used to stay in one place.  

## Targetting system

---

The targetting system is the part of the UI that is in charge of picking targets for the different actions, it is specially used in ranged combat with bows, crossbows, etc. And spell casting (Magic missiles or beams).  

It is a very important part of the UI, as it helps to make the combat simpler to the eyes of the player.  

Some games (such as [Nethack](nethack.md)) do not have a proper targetting system, they only allow the player to enter the direction not the target.  

```text
 You throw 2 daggers.
 
 
                   ---------
                   |····f···####
                   |)·)··@·|   #
                   |········#  0
                   -------·|#
                          # #
                          # #
                       #### ##
```  

Others like [Crawl](linleys_dungeon_crawl.md) or [Cataclysm](cataclysm.md) have a targetting system for spells, projectiles or missile weapons.  

```text
                                    Nord the Conjurer
                                    Tengu
                                    Health: 16/16     ========================
                                    Magic:  6/6       ========================
                                    AC: 10            Str: 12
         ### #####                  EV: 11            Int: 15
        ##...#...##                 SH:  0            Dex: 14
        #.........##...             XL:  2 Next: 54%  Place: Dungeon:2
        .....g.@l......             Gold: 14          Time: 880.2 (1.3) 
        ...............             h) +0 hand axe
        .(..........###             g) 7 tomahawks (return)
        #...........#           
        ##...#.....##           
         ########.##                l   giant gecko
                 >.                 g   hobgoblin
                 r..                r   rat

A rat comes into view.
Casting: Magic Dart
Confirm with . or Enter, or press ? or * to list all spells.
Aiming: Magic Dart
Press: ? - help, Shift-Dir - straight line, f - giant gecko
Aim: a giant gecko
```  

## See Also

---

* [Finding graphical tiles](finding_graphical_tiles.md)
