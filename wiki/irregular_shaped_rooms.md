# Irregular Shaped Rooms

---

Rectangular or any type of regularly shaped room in roguelike games has always bothered me, so today, instead of working I came up with a simple way to generate irregular polygons, or more "natural" looking caves. A few examples are shown below.

![rooms2](./assets/images/articles/irregular_shaped_rooms/rooms2.jpg)

## How it works

---

The basic concept is simple enough:

1. Enclose an area in 4 rectangles (left picture),
2. Randomly choose at least 1 point in each of the rectangles (second from left),
   1. Order the points in Rectangle A by their X value, from low to high, add them to a list.
   2. Order the points in Rectangle B by their Y value, from low to high, add them to a list.
   3. Order the points in Rectangle C by their X value, for high to low, add them to a list.
   4. Order the points in Rectangle D by their Y value, from high to low, add them to a list.
3. Starting with the first point in the main list use [Bresenham's Line algorithm](breshenhams_line_algorithm.md) to connect it to the next point, and so on, until you reach the end of the list. Connect the last point to the first point of the list, to completely enclosed the shape (third from left picture). The way the points have been arranged means they are connected in a clockwise direction (right picture).

![rooms1](./assets/images/articles/irregular_shaped_rooms/rooms1.jpg)

To fill in the room, sort all grids that were 'drawn' by the line algorithm from lowest to highest x value for each y value. Then scan from left to right, flipping from outside to inside (or vice versa) whenever you hit a new edge. You'll need to record which edge corresponds to which set of points as you generate the lines above, to avoid two consecutive grids which belong to the same edge incorrectly flipping the outside/inside bit.

Alternately you can use floating point math to determine the polygon interior. The C code below (from <http://www.ecse.rpi.edu/Homepages/wrf/Research/Short_Notes/pnpoly.html#The%20C%20Code>) avoids the need for [Bresenham's Line algorithm](breshenhams_line_algorithm.md):

```c
/*
 * Copyright (c) 1970-2003, Wm. Randolph Franklin
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files
 * (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge,
 * publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do
 * so, subject to the following conditions:
 * 1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimers.
 * 2. Redistributions in binary form must reproduce the above copyright notice in the documentation and/or other materials provided
 * with the distribution.
 * 3. The name of W. Randolph Franklin may not be used to endorse or promote products derived from this Software without specific
 * prior written permission.
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
 * MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE
 * FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
 * CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
 */

int pnpoly(int nvert, float *vertx, float *verty, float testx, float testy)
{
  int i, j, c = 0;
  for (i = 0, j = nvert-1; i < nvert; j = i++) {
    if ( ((verty[i]>testy) != (verty[j]>testy)) &&
       (testx < (vertx[j]-vertx[i]) * (testy-verty[i]) / (verty[j]-verty[i]) + vertx[i]) )
       c = !c;
  }
  return c;
}
```

## Notes

---

Step 2, really is the most important part, so here it is again.

```text
Moving from left to right, the points in Rectangle A are joined, then moving from top to bottom the points in Rectangle B are joined, then moving from right to left Rectangle C's points are joined and finally, from bottom to top Rectangle D's points are joined. Basically, we move around the rectangles clockwise joining the points as we go. This is the most important bit to get right, else the polygon produced is not enclosed.
```

Messing around with the settings can produce some surprising and varied shapes. Have fun!
