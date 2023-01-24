# Info Files

---

by James Burton [burton@guide.cz].txt

One of the major ways Angband stands out among Roguelike games is the sheer number of its variants. Want to play in Zelazny's world instead of Tolkien? Play Zangband. Want Lovecraft? Play CthAngband. And one thing that makes these variants possible is that Angband loads nearly all of its monster and item information out of easy-to-edit info files (I'm defining an "info" file here as a text file used to configure the actual game-play aspects of the game, not the user interface). This means I can make my own Angband variant without knowing a line of code! Now don't get me wrong, these other variants nearly always have their own code modifications as well, but toying around with info files is the beginning that can draw a person into starting a variant.

## Text files

---

You should consider very strongly loading your information from text files instead of storing it in the code itself. For one, it is far easier to design a simple text parser and then enter the data as text then to fill in loads of arrays with code. For another, it lets you fine-tune your game without constantly recompiling. Now you might worry about players being able to read all the vulnerabilities of the monsters and the powers of items, but this is very easy to hide—simply embed your info files in the game (most programming languages allow this), or if your programming language doesn't allow it, invert your text file (turn the 0's into 1's and vice-versa, use the "not" operation on each byte in the file) before giving out your compiled version. And if you have second thoughts because you want the whole game to be in one single application file, don't even worry: just about every OS has a way to package your text data into a "resource" in your application file (Windows and Mac certainly allow this). For example, in an arcade game I made called Witches (<http://www.strangegames.com>), all the maps were done as text files which were loaded separately from the program, for easy editing. When the time came to compile and distribute the game, I moved the files into the resource fork of the application and changed a single #define in the code—no hassle at all.

### File format

Now, the important part: the format of your file. You have any range of formats available, but it helps to look first at your priorities:

1. It should be human-editable. This means it should be a text file, with easily comprehensible keywords and structure, with whitespace and user comments.
1. It should be easy to scan by a parser (I'm using the word "parser" in the loose sense, meaning "programming code to read and interpret text"). This means it is pretty tightly structured. It also means there are is no actual programming in the info file—we're just storing data, not code! So it is reasonable to have a ATTACKS_ELVES flag in the file, it is not reasonable to allow constructs like "IF PLAYER=ELF ATTACK PLAYER" because that's a pain to write a parser for. The parser should be able to read the whole file in a single quick scan.
1. It should be relatively compact. Note that this priority is in opposition to the first two (for example, it would be far more compact to disallow comments, have referential constructs with data inheritance to prevent redundant data, etc., but the first violates rule 1 and the second violates rule 2). When in doubt, the first two are more important. This is text, after all, and won't take too much space no matter how space-inefficient your format: for example, the standard Angband monsters file (which is reasonably compact, but not too much) has over 400 creatures in it, and still takes less than 40k!

Here is a simple example of a format. In our game, let's say that the two things that are file-configurable are items and monsters. You could actually configure many more things than this, for instance:

- artifacts (i.e. unique items)
- item "flavors" (i.e. of sharpness, of fire resistance—often called "ego items" because of AD & D)
- terrain features (forests, walls, etc.)
- treasures
- rooms & vaults
- shops & shopkeepers
- what spells exist in the game
- random naming of creatures & items
- what races & classes the player can be

We will say that the basic units of our format are Whitespace (spaces, tabs, and returns), Comments and Entries. A Comment we will define as any line that begins with a # symbol (you can do C-style /\* \*/ comments if you like, but # parses really easily). The parser will read the entries one at a time, in any order. I find this is easier than making order important, for two reasons: a) because then the user can just add entries as s/he thinks of them, and b) you can separate the part of the parser that separates the whitespace and comments from the part that actually constructs entries.

We will next say that all entries have the following format:

```text
ENTRY_TYPE <Entry name> (
INFORMATION_LINE
INFORMATION_LINE
INFORMATION_LINE
...
)
```

ENTRY_TYPE is the specific type of entry this is. Since our example only has items and monsters, this will be either ITEM or MONSTER. You might have ITEM, MONSTER, SPELL, SHOP, VAULT, etc. The entry name is obvious: the base name of the entry. So

```text
MONSTER <Grue> (
...
)
```

is an evil Grue monster. You might wonder why I use <angle brackets> instead of "quotes". This is so I can use quotes inside the name (I could have defined an escape sequence, but I'm keeping the parser simple, right?), so I can have a monster like <Morgoth "Sex-Kitten" Bauglir> without confusing the parser.

Now the information lines inside the entry are clearly going to need to tell us what we need to know about the monster. I am going to say that each information line starts with the name of a certain attribute. You will have many of these, with different attributes for different creatures, such as WEIGHT and VALUE for an item or HITPOINTS and ATTACK for a monster. This attribute can be followed by a comma-separated list of integers and angle-bracketed strings, which can be of any length (including length 0). What each of these elements means depends on the attribute type. Attribute WEIGHT might require just a number, signifying the weight in poinds. Attribute ATTACK might take the kind of attack (a string), followed by the accuracy and damage of the monster. Note that we can have multiple attributes of the same kind in one entry, though this may not do anything in some cases (i.e. WEIGHT). Here are two examples:

```text
# This is easy to read, isn't it?  A ration of food is worth 200 points
# of food, weighs 2 lbs or
# kg or whatever, and is worth 5 gold or dollars or whatever.
#
ITEM <Ration of Food> (
NUTRITION 200
WEIGHT 2
VALUE 5
)

# I doubt you even need a comment to understand this one
#
MONSTER <Mauve Dragon> (
PLURAL <Mauve Dragons>
DESCR <A bluish-purplish dragon, an affront to both goodness >
DESRC <and standards of taste.>
DUNGEONLEVEL 30
EXPVALUE 15000
ATTACK <claws>, 30, 15
ATTACK <claws>, 30, 15
ATTACK <bite>, 15, 25
BREATH <irritation>, 400
)
```

Now there isn't space here to write a scanner for this file, but you should be able to see how easy it is. The scanner just runs until it reaches an entry, then gets the first word. Once it has the type, it builds either a monster or an item of the given name, then fills it with the information in the lines. Note that this is especially easy in an object-oriented language like C++ or Java—once you know you're making a type of monster you just create a new MonsterType() object, then as you scan the information lines one at a time call a addInfo() method in the object over and over, and let the object build itself from the information. So both the parser and the file writer have an easy time of it, and even though this is not a compressed format at all the Mauve Dragon above still only took 369 bytes! For more compression, the easy way is to shrink the keyword size—this is easier anyway, since it's more convenient and less error-prone to write DL instead of DUNGEONLEVEL and XP instead of EXPVALUE. On the other hand, if you go down to just one letter like Angband then the files are not quite as easy to read.

I should mention in passing two suggested developments to information files that pop up every so often in newsgroups. The first is XML. If you don't know already, XML is a really cool markup language (it looks superficially like HTML with all the <angle brackets>) which is being hailed as the new platform-independent way to send data of any form. You could definitely do your info files as XML. I recommend against this because it is probably a little more intense than you want or need. It is a) quite tricky to write by hand, violating our first priority; b) pretty intense to write a correct parser for (though far easier than parsing a programming language), violating the second priority; and c) not very compact, violating priority three.

## Scripting

---

The other development is the use of scripting languages to control behavior. The ideal is that what you would write in C or C++ is the "engine" of the game—the graphics and the interface and so on—but instead of implementing special behaviors in code, you would do it in a scripting language. So for example, using the data scheme above, if I wanted to make a special "singing sword" that made a little poem each time it slayed an opponent, I could do two things. The first (the bad or "NetHack" way) would be to check for the item in code; as in "IF weapon = singing sword THEN DoSing()". This is bad because your code gets rapidly cluttered up and unreadable. The second (the Angband way) would be to make a new attribute or flag, like SINGING, and put that in your object description. But you're still doing custom code.

The scripting language alternative would be best of all: what would happen is there would be a "hook" in your object that executes code. These hooks occur after certain events; for example weapons might have an ON_SLAY hook that does a certain action if the user slays a creature with that weapon (people familiar with JavaScript will see a similarity to events like "onClick" in HTML that hook into JS—it's the same idea). The way I would recommend this is to have a separate file with all your scripting functions. As a simple example, you might have a file called "scripts.js" with JavaScript routines for all your hooks. So you might have a function like:

```text
function Sing() {
  var r = game.RandomInt(0,2);
  if(r = 0) game.GiveMessage("Your sword sings of joy!");
  if(r = 1) game.GiveMessage("Your sword sings of triumph!");
  if(r = 2) game.GiveMessage("Your sword sings of victory!");
  PlaySound("annoying_song.wav");
}
```

And in your item entry, you would then only need an information line like this:

```text
ON_SLAY < Sing(); >
```

The dream behind scripting Roguelikes is that variants would then be able to customize completely or nearly-completely without having to recompile the code! Normally Python is suggested as the ideal language for this, because it has a lot of nice flexibilities that way. No one is suggesting making a special Roguelike custom scripting language, as it's not worth the effort—there are already lots of good scripting languages out there that have a good base of people who can program in it and are flexible enough to adapt easily to a Roguelike game; some even have code libraries available for making the parsing easier.

But to go back to reality, if you are starting a Roguelike, you'll have plenty of things to worry about without adding a scripting language processor. It's a huge task even if someone has built the processor for you (think of adding all the hooks, having a decent object model for the code to actually do things, etc.). For the same investment of effort, you could go a long way towards making your info files more configurable and general. Why not have an info file that confers every text message in the game, so it can be ported to other languages easily? (Or have the language pushed back a few centuries back for a Shakespearian feel?) One that gives your player configurable quests? If every important aspect of your game can be externally defined, then it's far easier to recruit friends and loved ones to build your game with you—and that's pretty cool, isn't it?

## Implementations

---

The following roguelikes make use of info files in some form:

[Angband](angband.md)
[POWDER](powder.md)
