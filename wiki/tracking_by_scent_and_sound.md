# Tracking by Scent and Sound

---

## Why

---

I'm sick of stupid monsters that can't properly navigate the dungeon. I have yet to see a roguelike game that exhibits intelligent and realistic tracking of the player by monsters. The dominant algorithm seems to be

```text
10 IF X < PX THEN X = X + 1
20 IF X > PX THEN X = X - 1
30 IF Y < PY THEN Y = Y + 1
40 IF Y > PY THEN Y = Y - 1
```

I didn't want my monsters to end up that way, so I started looking around the web for pathfinding algorithms. I didn't like what I found. The main problem I had was that they all assumed that the destination point is known absolutely. I don't want monsters that see through walls.

So I started thinking. I came up with something. Now, normally, the "brilliant" ideas I come up with at 4 AM don't work very well after a few hours of sleep, but this one still made sense.

And since I'm sure my roguelike is never going to see the light of day, I decided to try to force my ideas on other potential coders.

## Scent

---

It's a simple idea: you have a "scent" value for each tile on your map. These values all start out at zero.

Each game tick, the players scent value is added to the tile he's standing on. Then, each empty tile on the map is set to the average of itself and its surrounding empty tiles, minus a decay percentage.

That's it. The scent will permeate the dungeon in a relatively realistic manner.

How to use it? Well, I used it like this: if the scent level of the square a monster is on gets high enough (according to the monsters ability to smell), it becomes alerted to the presence of the player.

When an alert monster gets to move, it steps onto the adjacent tile that has the highest scent value, unless it can see the player, in which case it just takes a step directly towards the player.

This technique requires a lot of fine tuning: how often the scent is updated, how strong the players scent is, and how quickly it decays.

It also creates the potential for plenty of amusing items. A "cursed ring of stench", for example. Or deodorant.

## Justification

---

This algorithm is very processor intensive (the poor performance is, by the way, the main reason I decided not to model the flow of "scent" particles more realistically).

On my Pentium 133, iterating the algorithm 10 times each turn for an 80x50 map is noticably slow, even after some reasonable optimization.

It took me a while for me to justify this to myself. Now I don't know if you need a similar justification, but here's what I came up with.

For years, the CPU bottleneck in PC games has been the graphics. With the proliferation of 3d acceleration, it isn't so much anymore, but most games still spend most of the processor time on the graphics.

With a roguelike game, the graphics are simple. The biggest consumer of CPU time is gone. Now this can mean one of two things: it can mean that the game is written to run on slower computers (which understandably the case with most roguelikes, since they were written ON slower computers), or that the CPU time is being spent elsewhere.

It took me a while to wrap my head around the concept of a bottleneck in a game not being the graphics, but I managed to do it.

This is also why I insist on using C for my roguelikes -- I demand speed that can't be provided by sissified languages like ... well, let's not name names.

Now I'm rambling. Let's move on.

## Sound

---

This is theoretical. I haven't actually coded it, so I haven't worked out all of the details.

Sound is instantaneous. When a sound is generated, the area in which the sound can be heard should be determined, and the creatures within that area should be alerted (depending on hearing ability, etc).

Modelling the paths a sound follows can be as complicated as you want; feel free to write a raytracer. I chose this relatively simple method, somewhat similar to the one I used to model the permeation of scent:

Each tile has a corresponding "sound" value. Clear the array before each use.

Set the value of the tile in which the sound was generated to the volume (V) of the sound.

For each tile of the current V, set all surrounding empty tiles that don't already have a value to V-1. Decrement V, repeat until V is 0.

A sound alerts a monster if, after this process, the tile its standing on has a sound value greater than its "pay-attention" threshold.

If the sound alerts a monster (according to the monsters ability to hear), you then find the highest sound value that is in the monster's line of sight (for line of sight calculations, I recommend Bresenham) using the searching technique I described above. This is the square that the sound came from, as far as the monster knows, so it should begin moving to that square.

When it gets there (or before, if it trusts scent more than sound), it would switch to scent as a method of tracking.

When should noise be generated? Most of the things the player does should make noise. When a monster notices the player, it should probably call for help (this can lead to a chain reaction... though the monsters further down the chain won't have a good idea of where you are).

Noises should have descriptions attached in case the player hears them (and can't see them). If a monster makes a loud noise or fights or sets off a trap within earshot, the player should know.

## A squared plus B squared

---

Don't like the shapes these algorithms make? They look like squares, not circles, which would be natural, right? (please don't write to me and say they look like diamonds)

Well, consider the following formula:

```text
max(a, b)=c
```

That's the pythagorean theorem for roguelikes. Ever noticed how, in roguelikes, taking a step diagonally takes the same amount of time as taking a step horizontally? Walking from one corner of an 80x25 map to the opposite corner takes 80 steps.

(Incidentally, in Rogue it would take 105 steps, the formula for that game being a+b=c).

There are a lot of implications to this, but I'm not the one to work them out, or propose a "solution", if indeed a "solution" is what's required. It's just something to think about. And it's great for confusing people who point out flaws in my algorithms.

## Code

---

As a "bonus", here's some C code to demonstrate the smell (written on the spot and not thoroughly checked for bugs, so it might not work as is):

```c
void addscent(map *m, int x, int y, int scent)
{ m->place[x][y].scent+=scent;
}

void blurscent(map *m)
{ static int buffer[80][25];
  int i, j;

  for(i=0; i<80; i++)
    for(j=0; j<25; j++)
      buffer[i][j]=m->place[i][j].scent;

  for(i=1; i<79; i++)
    for(j=1; j<24; j++)
      if(walkable(m, i, j))
      { m->place[i][j].scent=
          (walkable(m, i, j)*buffer[i][j]+
           walkable(m, i+1, j)*buffer[i+1][j]+
           walkable(m, i-1, j)*buffer[i-1][j]+
           walkable(m, i, j+1)*buffer[i][j+1]+
           walkable(m, i, j-1)*buffer[i][j-1])/
          (walkable(m, i+1, j)+walkable(m, i-1, j)+
           walkable(m, i, j+1)+walkable(m, i, j-1)+1)*255/256;
      }
}

void followscent(map *m, monster *mn)
{ int i, j, x=mn->x, y=mn->y;

  for(i=-1; i<=1; i++)
    for(j=-1; j<=1; j++)
      if(walkable(m, mn->x+i, mn->y+j) && m->place[mn->x+i][mn->y+j].scent > m->place[x][y].scent)
        x=mn->x+i, y=mn->y+j;

  mn->x=x, mn->y=y;
}
```

## Closing

---

In closing, bye. Write me!

-- Jim Crawford
