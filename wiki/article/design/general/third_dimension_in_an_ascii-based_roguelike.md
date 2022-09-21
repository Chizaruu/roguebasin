# Third dimension in an ASCII-based roguelike

---

by [Kornel Kisielewicz](../../../developer/kornel_kisielewicz.md)  

Sounds crazy, eh? Well our world is apparently three dimensional. So why should a roguelike map be only 2D? Of course this article considers only the serious reality-based roguelike games - Crawl is 2D and it is fine.  

So what do I mean "ASCII-3D-roguelike"? No, I don't want to add 3D-models and do a QuakeTT style game. What I'm thinking about is a 3D map, that also has a "z" component. Why the hell, you might think?  

## Advantages

---

1. the possibility of really flying enemies (you can be overflown) without hacks.
2. a truly 3D system of dungeons, where you can fall from a gallery, or climb up a ladder, or swim underwater... possibilities are endless.
3. underwater action - cities, caves
4. Thief-style game, where you sneak on top of the town's roofs, trying to sneak past the townguards. Moreover, a assassin on the roof... at last jumping and climbing comes in handy.
5. a real ability to fly
6. complicated interconnected Castle buildings, where you can actually walk around the roofs. A lot inspiration for political/assassin games.
7. a truly mountainous landscape, hiding behind hills, charging downhill
8. patrolling the city walls at night, trying to sight an enemy
9. a real siege scenario
10. and more...  

Ok, so 3D is a NiceThing(tm). So why don't we plug it in?  

## Problems and Solutions

---

1. a extended map object (space consumption, more coordinates)
2. monster AI that takes into account the third dimension
3. a new raytracer or light engine for three dimensions
4. a way to represent elevation on the map
5. and more...  

Point number 1 is quite easy -- space isn't so much restricted (if you do level compression), the additional coordinate can be implemented easily (if you're doing it from the beginning, that is). Calculating distance may be done easy with the formula:  

```text
  3D Distance = Max(D,H) + Min(D,H)/2
    D = 2D Distance (x,y)
    H = Difference in height  
```

Point 2 would still be possible, although you would have to remember to alter the spotting chance depending on the difference in elevation. Flying creatures are a little more tricky though. It is wise to change the cost of moving up and moving down for flying creatures and for walking ones as well (it's easier to run downhill then uphill).  

It's point number 3 that will cause trouble, light has to be treated as a sphere and not a circle, and standing on the middle of a roof, you won't see what is just by the building's wall.  

## Display

---

But the real killer is point 4. It still isn't a problem if you use graphics or more than 16 colors. But if you're trying to make a hardcore 16 color ASCII-based game -- then you're in trouble.  

A way to do it is just to show the current elevation -- but then many possibilities are closed (you can't see from a roof where the guards on the bottom are). So we need some kind of representation for the lower levels (we ought to have a representation for higher levels, but as far as I can think of it, it's pure science-fiction...). Another idea, taken from the UFO game is to have a key, to cycle through the levels -- seems good, but it's quite inconvenient to do that every time when you want to see what's happening below.  

Of course you can treat higher elevation as separate levels, but that seriously impairs on their interaction capabilities.  

IMHO the best way to resolve this problem is to make all the squares below the player Dark Gray... but then, how will we see the light on them? I need more shades of Gray! There is a problem still with creatures flying high -- maybe a shadow on the ground (that is a gray tile?).  

On the other hand, this could be a very good use case for 2.5D display system such as goggles that simulate 3D objects or even upcoming 3D display systems that no longer have a need for goggles. ("I, for one, welcome our 2.5D roguelikes" ;) )  

Another idea is to change the orientation of the player's view. Traditionally the view is from top down and this was good. If the view were to be shifted from north to south or east to west, this would help with 3D intensive actions like stairs, falling, climbing. Leaving the view of choice to the user seems the simplest and most direction method.  

How about the concept of displaying an angled slice of the world. In a 2D world that slice was implicit, but in a 3D world that needs to be displayed onto two dimensions that slice can come from any angle. There are three easy slices, each aligned with two of the three axes. But wrap your head around making a 45 degree slice through a 3D world and displaying that on a flat screen. How would you show that to the user, and would the controls change? Meh, I'm day dreaming.  

## Things to remember

---

1. gravity -- items dropped in air will fall, so will corpses of killed flying creatures. So will the player if his "Fly" spell ends...
2. targeting -- here keys like PgUp PgDown for switching between levels would come in handy.
3. hurling grenades over walls -- just think about it...
4. explosions (e.g.fireball) -- do they affect creatures over you? The answer is yes.
5. jumping -- how to implement it?
6. roofs -- there is a difference whether a tile is just empty, or non-existent -- that is a floor on level two separates the levels, an air tile doesn't
7. and more...  

## Implicit Jumping

---

One method of implementing the jump in a roguelike game is to repeat what has been done with previous titles. In NetHack, the jump command was entered, the target location selected, and if it was range, the player is moved to that location. The points in-between were assumed to be "in the air". This is the past.  

The implementation I propose in the implicit jump. While a player is directly above a solid object he is "standing". When a player is not directly above a solid object he is "falling". Standing characters have freedom to move in any direction while falling characters generally prefer to move in the downward direction. If a standing character in a featureless plain moves to the north, he arrives. However if a standing character moves UP and to the north, he arrives there, finds himself no longer standing on anything, and begins to fall. Were he to attempt to move up and to the north again, his actions would be impeded and he would find himself on the ground.  

This would call for additional directions to be implemented. Long gone would be the 4 cardinal directions we hold so dear. The 8 directions (and #5, implicitly standing still) would further be expanding into 27 directions. The lower directions could, perhaps, be ignored if descending stairs is never an issue. Furthermore, unless some concept of ladder or flying is implemented, moving directly down seems trivial, and is aided by our good friend Mr. Gravity.  

## 3D Roguelikes

---

* [CastlevaniaRL](../../../game/castevaniarl.md) and [MetroidRL](../../../game/metroidrl.md) <http://www.santiagoz.com/web>
* [Dwarf Fortress](../../../game/slaves_to_armok_II_dwarf_fortress.md)
* Wish, available at the [Roguelike Graveyard](http://www.graveyard.uni.cc/).
* DungeonClimb, available at [1](http://sourceforge.net/projects/dungeonclimb/)
* [Ascii Wilderness](../../../game/ascii_wilderness.md) written entirely in LUA 5 with 3d ascii landscape.
* [X@COM](../../../game/xcom.md), featuring side-view for checking LOS/LOF in z-dimension
* [City of the Damned](../../../game/city_of_the_damned.md)
