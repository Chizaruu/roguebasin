# Quick and dirty FOV/LOS

---

This is a FOV algorithm (read: babies first Line of sight) i used in a C# roguelike i made for the learning experience, it is probably not as efficient as it could be.

## Pseudo-code

---

```text
  For each block (assuming your roguelike map works in blocks, 99.9% of RL's do)
  {
     if the block is within the players view radius
     {
        draw a virtual line from the player to the block
        for each block on the line
        {
           set it as visible initially
           if it is a wall/door/object that blocks vision, or any previous blocks have been blocking objects
           {
              the block = non-visible
           }
        }
     }
  }
```

[C# code](http://pastebin.com/wUharWgs) Note: This was programmed using XNA, the data types "vector" and "point" can be easily recreated in normal c# with the use of data structures, also, the function HasLOS(x, y) can be used outside FOV, e.g. testing whether a monster can see the player, because it just checks the give x & y coordinates against the player's

## Other methods

---

This method worked fine in my RL, however, it may be more efficient (read: probably) to draw a line to each block that lays on the line of circumference around the player (the radius being view distance), and going through each block on the line and checking for blocking objects.
