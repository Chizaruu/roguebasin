# Simple Rogue levels

---

There are lots of different level generating algorithms out there to choose from. Most of them create some very cool and advanced designs. But sometimes you want to make something really simple, go back to the roots, replicate the levels that the original Rogue had. The obvious thing is to look at the source code and figure out how they did it, then reimplement it. I found that task to be a little hard though, so I came up with a really simple algorithm that produces very similar levels (although they are different in a couple of crucial details). I thought that this might be useful for others, so here it goes.

First, let's see what we are aiming at. The original Rogue levels look something like this:

```text
                 ######
#########     ::::+....#
#.......#     :   #....#          ######
#.......+::::::   #....+::::::    #....#
#.......#         ##+###     :::::+....#
##+######           :             ######
  :::::             :
   ###+#####        :::
   #.......#      ####+##
   #.......#  ::::+.....#
   #.......+:::   #.....+:::::::::::
   ##+######      #.....#          :
     :            #######       ::::
     :                          :
   ##+##                     ###+###
   #...#                     #.....#
   #...#                     #.....#
   #...#                     #.....#
   #####                     #######
```

The characters I used are a little diffrent than the original Rogue used, and the whole thing is much smaller, but you get the idea: you have nine areas that can contain a room (but they might also only have a corridor bend in them, or nothing at all). Those rooms are all connected with corridors, with a small probability of having a loop. If you look closely at the corridors, you can see that they always connect neighbouring areas, and that they have at most two bends (the corridor going from the middle room to the bottom right room with 3 bends in it is actually 2 corridors).

We will actually make something much simpler:

```text
                  ......
.........         ......
.........         ......          ......
........................................
.........         ......          ......
.........           .             ......
 .                  . ..................
 . .........        . .
 . .........      .......
 ........................     ......
   .........      ..................
   .........      .......     ......
     .            .......     ......
     .                .         .
   .....              .      .......
   .....           ......    .......
   .....           ......    .......
   .....           ......    .......
   .....                     .......
```

It's not exactly the same thing, but I think it works as a level as well. Of course you can easily add things like walls, doors, corridor tiles, etc. We are just not going to bother with that now.

So, we start with defining our areas. Rogue had them predefined, and we will start with that too. Later on you can try to randomly pick one of several predefined layouts, or even randomly generate the layout -- lots of room for creativity here. We will encode the areas as four numbers: the position of the top left corner, width and height. We will need nine non-overlaping areas that fill our screen completely.

Side note: We are going to be using that representation of a rectangle as four numbers a lot. It's probably a good idea to have a couple of helper functions for it. We will definitely be selecting a random spot inside a rectangle, so at least have a function for that -- it takes the rectangle as the input and gives you a random position within it as output.

Now we have to iterate through the list of areas and decide where to put a room in each of it. For simplicity we will put a room in every one of them for now. Later we can add a little more variety and randomly skip areas or make them just a join of corridors instead of a room. (Rogue even had maze rooms on the later levels!) For every area randomly pick a point inside it, far enough from the bottom and right edges of the area, and then pick a random width and height (make sure to get your ranges right so that the room fits in the area). Save that as another list of areas.

Once you have a list of rooms, you can iterate over it and draw them on your map. We do it in a separate loop though, because we will want to do some other things in between later on. But for now, drawing the rooms lets you see if you got everything right and it's nice to actually see some result of your work so far.

Now it's time to add corridors. We will draw the corridors before the rooms, so that we don't have problems with a corridor hitting a room.

In Rogue, corridors always look like this:

```text
 A...........
            .
            .
            .
            ............B
```

Or like this:

```text
 A
 .
 ........................
                        .
                        B
```

So you have the end points and a middle point, and you either go vertically, then horizontally, then vertically again, or you first go horizontally, etc.

That is a little too complicated for the beginning, so let's make something simple instead:

```text
 A.......................
                        .
                        .
                        .
                        B
```

We can get an alternate look if we swap the endpoints:

```text
 B
 .
 .
 .
 .......................A
```

So we first make a function that draws such a corridor between two points. Simply two loops, one drawing the horizontal and one drawing the vertical part. We can later replace it with a more advanced corridor function, or even randomly select one of several functions. Again lots of place for creativity here.

So now we need to connect our rooms. We want all the rooms to be connected, so we will do just that: iterate over all the rooms on your list, and draw a corridor from a random point in your room to a random point in the previous room (except for the first room, obviously). Note, that the order is important here, we will get to that later.

You should get something like this:

```text
                  ......
........................
.........         ......          ......
.........         ......................
.........         ......          ......
......... ..............................
          .                       ......
   .........
   .........      .......
   ......................     ......
   .........      ..................
   .................................
     .            .......     ......
     .
   .....                     .......
   .....           ......    .......
   .................................
   .....           ......    .......
   .....                     .......
```

You can immediately tell that there are two things wrong with this level. First, the corridors touch themselves and the edges of rooms, running parallel to them. Second, then connections between rooms are very predictable. We will start with the second one first.

To make the connections between rooms less predictable, just shuffle the room list before making the corridors. That's it. It even takes care of selecting one of the two corridor variants we have, by randomizing the order of the endpoints. This is actually the main trick here.

Now let's handle the problem of corridors running into itself and the rooms. We are going to do two things, both a little hacky. You can come up with better solutions for your own level generator. The first hack is to avoid two corridors going in parallel next to each other or next to an edge of a room: we will jest select only even numbers for the positions and sizes of rooms and corridors. Voila, done :) Yeah, I know, it doesn't sound very good, but it works perfectly and doesn't really show. The second problem is corridor crossings, and we are only going to solve that half-way by turning them into T-connections: just stop drawing a corridor as soon as you hit an existing corridor. This is the reason why you always connected a room to the previous one on the list, not to the next one -- you always end up with a fully connected level that way.

Well, and that's it. One hint for the end: keep the room list. It will come in handly when you are placing items and monsters, and later you can use it for field of vision during the game.
