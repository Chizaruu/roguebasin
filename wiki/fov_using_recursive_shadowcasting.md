# FOV using recursive shadowcasting

---

FOV using recursive shadowcasting - Björn Bergström [bjorn.bergstrom@roguelikedevelopment.org]

## Introduction

---

A working field-of-view (or commonly known as line of sight) algorithm is one of the essential parts in any roguelike. A FOV algorithm is used to calculate which mapcells, within a given radius, that can be seen, or in the case of a lightsource, which mapcells that are lit.

The simplest approach is to trace lines from the center out to all of the mapcells at the edge of the radius and stopping when a mapcell is blocking line of sight. The problem with this approach is that many mapcells are visited several times, most often near the starting point and more seldom at the edges. There are a few things that can improve the performance of this simple approach. The most obvious is improvement can be made when a blocking mapcell is hit. Using a simple calculation all rays that cross the blocking mapcell can be skipped, hence improving performance. All of this is covered in detail in other LOS articles. [Line of Sight - Tobias Downer](line_of_sight_-_tobias_downer.md) is a good starting point.

Even if the "rayskipping" described above is implemented many mapcells will be visited more than once, thus wasting CPU time. To overcome this a totally different approach must be used. This approach is called shadowcasting.

## Shadowcasting

---

Shadowcasting divides the FOV calculations into eight octants and visits the mapcells in a totally different way than normal raycasting, described above. Instead of tracing lines from the center out to the edges, shadowcasting visits mapcells row by row or column by column, starting with the nearest row or column and working it's way outward.

```text
  ------>  6 row 6 last
   ----->  5 .
    ---->  4 .
     --->  3 .
      -->  2 row 2 second
       ->  1 row 1 is scanned first
        @  @ this is the starting point

 Fig.2a Shadowcasting order of scanning
```

When a scan comes across a cell that blocks LOS it calculates which other cells in rows/columns farther away that isn't visible because of the blocker. Those cells are "in shadow", hence the term shadowcasting.

```text
  -...---  - = visible cells
   -..---  # = blocking cell
    -#---  . = cells in blocker's shadow
     ----
      ---
       --
        @

 Fig.2b Cells in shadow of blocker
```

Normal shadowcasting is described in detail in the article [Computing LOS for Large Areas](computing_los_for_large_areas.md) by Gordon Lipford. Gordon's algorithm uses a temporary array and is rather complex. Using recursion one can achieve a clean and pretty fast algorithm that only visits non blocking and blocking cells, leaving out the cells in shadow. The algorithm is especially fast in confined areas such as corridors and small rooms.

## Definitions

---

In order to understand recursive shadowcasting one need to understand what the slope and inverse slope of a line means. The slope is calculated using this simple formula:

```text
slope = (x1 - x2) / (y1 - y2)
```

If we draw a line between [6,6] and [5,3] the slope would be:

```text
slope = (6 - 5) / (6 - 3) = 1 / 3 = 0.33333
```

If we were to walk along this line we would find that for each step that we decreased y, x would be decreased 0.3333.

The inverse slope is simply 1 / slope.

## Recursive Shadowcasting

---

Recursive shadowcasting divides the field of view into eight octants with shared edges. The field of view will look like this when the octants are marked:

```text
             Shared
             edge by
  Shared     1 & 2      Shared
  edge by\      |      /edge by
  1 & 8   \     |     / 2 & 3
           \1111|2222/
           8\111|222/3
           88\11|22/33
           888\1|2/333
  Shared   8888\|/3333  Shared
  edge by-------@-------edge by
  7 & 8    7777/|\4444  3 & 4
           777/6|5\444
           77/66|55\44
           7/666|555\4
           /6666|5555\
  Shared  /     |     \ Shared
  edge by/      |      \edge by
  6 & 7      Shared     4 & 5
             edge by
             5 & 6

 Fig.4a Area of coverage by each octant
```

As with normal shadowcasting, this recursive shadowcasting algorithm scans an octant row by row or column by column, depending on the octant. In each octant the rows/columns closest to the starting point are scanned first.

In octant 1 and 6 the scans are performed row by row, going from the leftmost cell to the rightmost cell in each row.

In octant 2 and 5 the scans are performed row by row, going from the rightmost cell to the leftmost cell in each row.

In octant 3 and 8 the scans are performed column by column, going from the topmost cell to the bottom most cell in each column.

In octant 4 and 7 the scans are performed column by column, going from the bottom most cell to the topmost cell in each column.

When a blocking cell is found a new scan is recursivly started one row/column further away, covering the area up until the first cell in shadow of the blocker. The rest of the initial row/column is scanned and subsequent blocking cells directly adjacent to the initial blocker is skipped. If a new section of non-blocking cells, followed by a blocker, is found the procedure is repeated.

I will try to illustrate the procedure described above using some simple ascii drawings. The area that we wish to calculate a field of view for looks like this:

```text
 ................. 16  @ = starting cell
  ......###....... 15  # = blocking cell
   .....###....... 14  . = non-blocking cell
    ....###..#..## 13
     ...##........ 12
      ............ 11
       ........... 10
        .......... 9
         ......... 8
          ........ 7
           ....... 6
            ...... 5
             ..... 4
              .... 3
               ... 2
                .. 1
                 @

  Fig.4b Field of view
```

Rows 1 through 11 are all scanned without any problems from left to right. When the rightmost cell is reached a new scan is started one row further away, just as described before.

If we were to draw a line from the center of the starting cell to the center of the leftmost cell in any of these rows we would find that the slope is 1. We call this the scan's start slope. If we would do the same for the rightmost cell the slope would be 0. This slope is ofcourse called the end slope.

When we reach the 12th row things are becoming a bit more interesting. The recursion is started when we get to row 12 and hit the blocking cells.

```text
 ................. 16  # = blocking cell
  ......###....... 15  . = non-blocking cell
   .....###....... 14
    ....###..#..## 13
     ...x#........ 12  x = first blocking cell

 Fig.4c The first blocking cell
```

When the first blocking cell is hit (x) a new scan is started on row 13. The start slope is ofcourse the same as for all of the previous rows (ie. 1), but the end slope is different. The end slope is calculated using a line from the starting point to a point that 'brushes' by to the left of the blocking cell. If we zoom in it looks something like this:

```text
 +---+xxxxx#####  x = first blocking cell
 |   |xxxxx#####  a = point that 'brushes' by to
 |   |xxxxx#####      the left of the blocking cell
 |   |xxxxx#####
 +---axxxxx#####
 +---++---++---+
 |   ||   ||   |
 |   ||   ||   |
 |   ||   ||   |
 +---++---++---+

 Fig.4d Zoom in on the first blocking cell
```

Thus, the end slope is obtained using a line from the center starting cell to the point marked 'a' in the figure. This gives an end slope of about 0.82.

Ok, so now we have two scans; the original that continues to scan row 12 until the rightmost cell is reached and a new scan that scans row 13 from the leftmost cell (start slope 1) to the cell at row 13 that intersects the line with a slope of 0.82 (end slope):

```text
 2222............. 16  # = blocking cell
  2222..###....... 15  . = non-blocking cell
   222..###....... 14
    222.###..#..## 13  1 = original scan
     111##11111111 12  2 = new scan

 Fig.4e Current scans
```

Ok, lets return to the original scan on row 12. The scan had just come across the first blocking cell and recursivly started a new scan one row further away with a new end slope. The original scan now checks the next cell and finds that this one is also a blocking cell. Since the previous cell was a blocking cell too, we have come across a section of blocker and just continue scanning until we reach a non blocking cell:

```text
 ................. 16  # = blocking cell
  ......###....... 15  . = non-blocking cell
   .....###....... 14
    ....###..#..## 13
     ...##o....... 12  o = first non-blocking cell after a section of blockers

 Fig.4f First non-blocking cell after a blocker
```

When the first non-blocking cell is found after a section of blockers we need to calculate a new start slope for the scan. This is done using a line from the center of the starting cell to a point that 'brushes' by to the right of the blocking cell. If we zoom in it looks something like this:

```text
 ##########aoooo  o = first non-blocking cell
 ##########o   o  a = point that 'brushes' by to the
 ##########o   o      right of the blocking cell
 ##########o   o
 ##########ooooo
 +---++---++---+
 |   ||   ||   |
 |   ||   ||   |
 |   ||   ||   |
 +---++---++---+

 Fig.4g Zoom in on the first non-blocking cell
```

Thus, the new start slope of the original scan is obtained using a line from the center of the starting cell to the point marked 'a' in the figure. This gives a start slope of 0.6.

Ok, once a scan has reached it's last cell the scan is finished and a new scan is started one row further away if, and only if, the last cell was a non-blocking cell. In the case of our original scan the last cell was a non-blocking cell so a new scan is started one row further away with the new start slope of 0.6 (instead of the old 1).

When the original scan starts on row 13 a blocking cell is immediately found:

```text
 ................. 16  # = blocking cell
  ......###....... 15  . = non-blocking cell
   .....###....... 14
    ....##x..#..## 13  x = blocking cell in original scan

 Fig.4h Starting with a blocking cell
```

When this happens we continue scanning until a non-blocking cell is found. In this case the next cell is a non-blocking cell and we calculate a new start slope in the same manner as on row 12 when we passed the section of blockers. After this is done we continue to scan from left to right until we reach the last cell or until we hit a blocking cell. In our example a blocking cell is found before we reach the last cell:

```text
 ................. 16  # = blocking cell
  ......###....... 15  . = non-blocking cell
   .....###....... 14
    ....##...x..## 13  x = blocking cell in original scan

 Fig.4i Another blocking cell
```

A new scan is now recursively started in the same way as on row 12. The scan has the same start slope as the original scan (0.6) and an end slope of a line that 'brushes' by to the left of the blocking cell (marked x in fig.4i). Now we have three active scans:

```text
 2222......33..... 16
  2222..##333..... 15
   222..##333..... 14
    222.###11#11## 13

 Fig.4j Active scans
```

The same procedure is repeated once more when we move out of the blocking cell, find two new non-blocking cells and the run into yet another blocking cell:

```text
 2222......33444.. 16
  2222..##333.44.. 15
   222..##333.44.. 14
    222.##111#11## 13

 Fig.4k Active scans
```

When the original scan ends at the rightmost cell in row 13 we end with a blocking instead of a non-blocking, as we did in row 12. Since the original scan ended with a blocking cell a new scan is NOT started one row further away. We now have scan 2, 3 and 4 to do the job of scanning the rest of the field of view. These scans follow the same procedures and rules as the original scan.

When the scans are done we get this field of view:

```text
 ....ssssss.....ss 16  @ = starting cell
  ....ssss#..s..ss 15  # = blocking cell
   ...ssss#..s..ss 14  . = non-blocking cell
    ...ss##..#..## 13  s = shadowed cells
     ...##........ 12
      ............ 11
       ........... 10
        .......... 9
         ......... 8
          ........ 7
           ....... 6
            ...... 5
             ..... 4
              .... 3
               ... 2
                .. 1
                 @
```

This procedure is repeated on the other octants, thus producing a complete field of view. An implementation along with source and DOS executable can be found here.

Copyright 2001 Björn Bergström bjorn.bergstrom@roguelikedevelopment.org

## Implementations

---

A Java implementation of the improved version: [Improved Shadowcasting in Java](improved_shadowcasting_in_java.md).

An [implementation](python_shadowcasting_implementation.md) of this algorithm in Python.

[A more Pythonic adaptation of the Python implementation above](https://raw.githubusercontent.com/irskep/clubsandwich/afc79ed/clubsandwich/line_of_sight.py).

Another [implementation](ruby_shadowcasting_implementation.md) in Ruby.

[Another one](c++_shadowcasting_implementation.md) in C++.

[ActionScript 3 implementation](http://nemonon.de/blog/?p=10)

[C# Implementation](http://www.evilscience.co.uk/?p=225)

Heavily-commented [C# implementation](http://fadden.com/tech/ShadowCast.cs.txt).
