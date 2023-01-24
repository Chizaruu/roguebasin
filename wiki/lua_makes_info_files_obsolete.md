# Lua makes info files obsolete

---

In Lua, any .lua code file can be executed by your software with a call to dofile("filename")

Hence, there's no reason to write

1:Tree,

2:green,#

3:block_los,block_pathfind

and load that into your data structure with code that parses it and knows what to do, in it's own sort of "code". That's way to complex and with LUA there is no need for that.

when you want to extend a LUA game with a mod, it's as simple as "dofile"ing a file that contains the following code: (data.prototype_objects is where you store your basic item information. Simple.)

---

data.prototype_objects.tree = { --table constructor

name = "tree",

symbol = "#",

color = "green",

block_lineofsight=true,

block_pathfind = true,

} --finished the table

---

and the game engine already knows what to do with it and there's no need to "interperet" the data a second time by parsing a text file in a custom code system.

--This is only one example form your data can take...

But as you can see, the code is highly readable and easy enough to mod if you have other example code to work from. There's no reason to make a complicated "info file" system if your objects can be contained in .lua files that are easily edited with a text editor and the code is fairly obvious and readable and only requires a minimum familiarity with LUA programming.

-Russell Ackerman
