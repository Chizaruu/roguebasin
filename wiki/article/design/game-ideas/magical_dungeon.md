# Magical Dungeon

---

(by Patashu, compiled by [Kornel Kisielewicz](../../../developer/kornel_kisielewicz.md)) copy edited by [R. Dan Henry](../../../developer/r_dan_henry.md)  

Okay, there's this dungeon, as per the usual...  

...However, excess magical residue has distorted it dimensionally. Half of the staircases, instead of going through a level of the dungeon, take you through another area altogether (a forest, grassland, lake, shore, cave system, city...) where the next stairs down reside.  

It seems like a neat idea to me, and a plot shouldn't be too hard to weave around it (wizard guy is studying the dungeon to be able to harness the dimensional distortion and control spacetime at will...or the artifact at the bottom floor is causing the distortion and if not destroyed/relocated will cause it to spread everywhere...).  

I don't have any programming skills, though, so I was at least wondering what everyone thought of the idea?  

---

**Mechanoid**  

It would be better if the dungeon occasionally looped in the X and/or Y axis, thus if you ran in a straight line you would end up in the same spot you started; and no mapping.  

But it is a good idea.  

**AsterAzul**  

I've actually thought through this one before. I've been kicking the can over a little sandbox world design with very tiny worlds that are based on geometric shapes, where the dungeon is an exploration of the worlds' insides. So you would descend the various levels into the earth and pass through the different planes of element that way, like in Native American myth. I had a brainspasm over the thought of implementing a sphere in ASCII, though. Man has a hard enough time trying to draw a danged map of the world in real life.  

Cylindrical has the advantage of simplicity, though. Going off the edge sticks you to the side of a circle.  

Although you could represent the insides of a sphere in 2D, by scaling the levels up and down the sphere. I suppose you could use different shades to represent the sphere's elevation, with a constantly circular apparent top. I'm not sure if that would be more amusing or more a pain-in-the-butt, though.  

**Simon Richard Clarkstone**  

If you want to be really weird, don't just stick one or both opposite edges together! How about a Mobius strip, where one edge joins onto the opposite edge backwards? If you scroll the map rather than moving the player-symbol, this could create some severe puzzlement. If you join the other two edges in the "normal" way, you get a [Klein bottle](../../../game/kleinrl.md). Or you could have a non-flat dungeon, in which walking forward might cause you to change direction; for example, walking off the north edge might make you come out on the west, rotated 90 degrees.  

The ultimate in confusion is a perversion of Chris Morris's eMaze though: the dungeon is made of small sections, which join at the edges with arbitrary flips or rotations. LoS could be interesting, indeed you would have to do many things by walking from one section to the next. A true programming challenge. You could try the world-representation out of [The Continuous World of Dungeon Siege](http://www.floatingorigin.com/mirror/continuous-world.htm). Their system was invented for slightly different reasons than confusing the player, but it still made it impossible to draw overall maps.  

Depicting known places will be an extreme pain. The best you can do is to flood outwards from the player, writing to the screen as you go, and whichever square writes to a particular part of the screen first gets it. You will need a notation for discontinuities on-screen, and maybe some means by which the player can draw his own map (e.g. numbered rooms) or remember an approximate view of the dungeon (e.g. miniature view).
