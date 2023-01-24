# Restrictive Precise Angle Shadowcasting

---

Introduction

---

Recently, a study has taken place with the aim of comparing the existent and commonly used field of view (FOV) algorithms. It revealed that none of them could be called "the best" and all had some flaws. The tested algorithms were raycasting, recursive shadowcasting, modeling LOS rays (in the test it was called "diamond raycasting"), digital field of view and a few variants of the permissive field of view. In some cases, the main issue was the speed, in others, problems related to the algorithm not penetrating diagonal lines of obstacles or an unacceptable grade of asymmetry. Sometimes the glitch was the shape of the field of view, which was visually unattractive, bad for gameplay or simply unnatural.

One day at work I was reading through Dirk Kok's article on shadowcasting and it gave me some basic insight on how the whole mechanism works. However, I immediately saw a major flaw in it: the slope calculation was very inaccurate. And I knew just how to fix it.

## Overview

---

Restrictive Precise Angle Shadowcasting, because that's how this shadowcasting implementation is called, is an improvement over the standard shadowcasting. Instead of calculating one approximated slope for each cell, it uses three extremely exact slopes, or angles, to determine a cell's visibility. The final result is a very restrictive algorithm that appears to fulfill the basic requirements enumerated on RGRD: it has decent speed, it doesn't produce artifacts, it has good looking shadows, even behind 1x1 pillars and regardless of the PC's position relative to the pillar, it lets you see through diagonal lines of obstacles, the angles it produces have a natural feel...

## Mechanics, part 1: the angles

---

Let's delve deeper into how this beast works. As said before, each processed cell has 3 exact angles (from now on, I'll be using the word "angle" instead of "slope") assigned to it. Please have a look at the following image:

![the angles](./assets/images/articles/restrictive_precise_angle_shadowcasting/i1QnTsK.png)

The source tile is the one marked "@", while the #'s are obstacles. The example angles are those of the tile [@.x-1,@.y-3]. The green line represents the starting angle, the blue one is the centre angle and the purple one is the end angle. Note also that the lines that connect the "@" with the processed tile's corners and baseline aren't the lines of sight that are calculated. They have been represented so only to visualise which parts of the processed cell are taken into account when determining their visibility.

The angles are calculated this way: the algorithm always knows how many cells the currently processed line has. The entire line's range of angles is always 0.0f to 1.0f. Therefore, a single cell's angles can be calculated by dividing the entire line's angle range (1.0f) by the number of cells in this line. For instance, in the image above, line number 3 is calculated (@.y-3). It has 4 cells (y displacement plus one cell), so the angle range of one cell in this line is 1.0f/4 = 0.25f. Using this value, it is easy to calculate the three slopes of the cell in question: cell number\*angle range for the starting angle, starting angle + angle range for the ending cell, starting angle + angle range/2 for the centre angle.

Please note that the starting and ending angle are shared with the adjacent cells.

There's also one important thing to consider when calculating a cell's angles. The order in which the cells are processed in each line is always from the orthogonal border of the octant towards the diagonal one. In case the "@" is near the map's border, this will prevent the slopes from being calculated wrongly. The following image shows the cell calculation order for two adjacent octants:

![two adjacent octants](./assets/images/articles/restrictive_precise_angle_shadowcasting/iTkPM2c.png)

The green cells belong to the NNW octant (the one we'll be focusing on, for simplicity's sake), the red ones belong to the WNW octant. The yellowish ones are shared by both octants. The arrows show the cell calculation order in a single line (line 2 in both cases) for the NNW and WNW octants.

The line calculation order is from the "@"" outwards (in this case, the lines will start at the positions [@.x,@.y-1], [@.x,@.y-2], [@.x,@.y-3], et caetera. It is shown on the following image:

![line calculation order](./assets/images/articles/restrictive_precise_angle_shadowcasting/2gV9fnQ.png)

The first calculated line is marked with green and the following ones go along the gradient through blue to pink. The pink line will be calculated as the last one (line number 7).

## Mechanics, part 2: the obstacles

---

When a visible obstacle is found during the cell processing, the algorithm has to remember its starting and ending angles. They should be written in some sort of an array (array of structs, or perhaps two separate arrays of starting and ending angles at the same index position). This range will be considered blocked and all cells whose angles are within this range will be considered unlit starting from the next line.

At the same time, the count of visible obstacles needs to be increased by 1. This total number will be used later. The only thing one has to bear in mind is the difference between the total number of found obstacles and the total number of found obstacles up to the previous line. The latter will be used for actual visibility determination.

## Mechanics, part 3: visibility determination

---

Now that we know how to calculate each cell's angles and there's a blocked angles' array set up and possibly filled with some obstacles that have so far been encountered, the fun part begins.

The approach I have adopted is the following: a transparent cell requires its centre angle to be unblocked, along with at least one of the side angles (either starting or ending angle). The opaque tiles require any single unobstructed angle. This can be tweaked to match the user's preferences. The most permissive version would set any tile's visibility to on if there's any single unobstructed angle, but be aware that this will light tiles immediately behind a single cell obstacle (ie, behind a pillar).

When a cell is processed, it needs to check whether the angles in question are within the bounds of the blocked angle ranges by going through the blocked range array at all indexes from zero to the number of found obstacles up to the previous line. If in no case the angles are obstructed, the tile is marked as visible.

Please have a look at the image below:

![visibility determination](./assets/images/articles/restrictive_precise_angle_shadowcasting/eOU5iol.png)

Up to the line 2 the algorithm has encountered 0 obstacles, thus all cells up to line 3 are visible (marked green). However, line 3 contains two obstacles. They mark the blocked ranges as 0.0f-0.25f and 0.75f-1.0f. The angle ranges have been marked with red lines.

The following image shows the whole octant already calculated:

![visibility determination 2](./assets/images/articles/restrictive_precise_angle_shadowcasting/7xXDuy6.png)

As you can observe, line 4 has 5 cells. The first one is not visible, since it's completely within the blocked range of the first obstacle (0.0f-0.25f). It is ignored and left invisible. Even though it's an obstacle, there is no sense in adding its angles to the blocked ranges array, since it's 0.0f-0.2f, which is already within the previous blocked range of 0.0f-0.25f. The second cell is visible, since its ending angle, as well as the centre angle, are both unblocked.

## Implementations

---

There are implementations available in multiple programming languages:

- [C](c.md) ([implementation with los code](https://github.com/luctius/heresyrl/blob/master/src/fov/rpsc_fov.c))
- [C#](c_sharp.md)
- [C++](c++.md)
- [Lua](lua.md) ([rotLove is a port of rot.js](https://github.com/paulofmandown/rotLove))
- [Python](python.md) (Python 2.7 [implementation and demo](https://github.com/MoyTW/roguebasin_rpas))
- [Rust](rust.md)
  - ([Rust implementation.](https://github.com/bluss/rpasha/blob/master/src/shadowcasting.rs))
  - ([doryen-fov](https://github.com/jice-nospam/doryen-fov))
- [JavaScript](javascript.md)
  - [mrpas](https://www.npmjs.com/package/mrpas)
  - [rot.js](http://ondras.github.io/rot.js/hp/)
  - [mrpas-js](http://github.com/domasx2/mrpas-js) ([live demo](http://domasx2.github.com/mrpas-js/))
- TypeScript ([Yendor.ts](yendor.ts.md))
- Godot Engine ([GDScript implementation](https://github.com/matt-kimball/godot-mrpas))

A [Java](java.md) implementation is planned as well.
