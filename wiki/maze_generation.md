# Maze Generation

---

I, personally, like roguelikes that have levels entirely occupied by mazes.

## Example

---

```text
 #####################################################################
 ###############################.#.###################################
 ###############################.#.###################################
 #############################.....###################################
 #############################.#.#####################################
 #########################.#.......###################################
 #########################.#.#.#.#.###################################
 ###################.......#.#.#.#...###...#...#######################
 ###################.#####.#####.#.#.###.###.#########################
 #############.........#...#...#...#...#...#.#######...###############
 #################.###.#.#.#.#.#.#.###.###.#.#########.###############
 #############.....#...#...#...........#.#...###.#.....###############
 ###################.#.#.###.#.###.###.#.###.###.#.###################
 #################...#.#.....#.......#.......#...#.###################
 #################.#.#.#.###.#.#.###.#.#.#.#.#.###.###################
 #################.#.#...#...#.....#...#...........###################
 #################.###.#.###.#.#######.###.###########################
 #################...#...#...#...........#.#.....#####################
 #################.#####.#######.#.#######.#.#########################
 #############.....#...#.#.......#...#.......#.#.#####################
 #############.###.#.###.#.###.#.###.###.#.#.#.#.#####################
 #############.###.....#...........#.#...#.#...#.#####################
 #############.###.#.#.###.#.###.#.###.#.#.#.###.#####################
 #############...#...#.......#...#.....#.........#####################
 #############.###############.#.###########.###.#####################
 ###########.#.........#######.#.#########...#.#.#####################
 ###########.#######.#########.###########.###.#.#####################
 ###########.....###.#.#####...###########.....#...###################
 #############.#.###.#.###############################################
 #############.........###############################################
 #################.###################################################
 #############.....###################################################
 #############.#######################################################
 #############.#######################################################
 #############.#######################################################
 #############.#######################################################
 #############.#######################################################
 #############.#######################################################
 #####################################################################
```

## Description

---

Here's a simple way to generate mazes:

1. Start from a cell and mark it 'visited'
2. Pick a direction.
3. If the nearest cell in that direction is 'unvisited' or you want to make a loop here, move to it.
4. Mark it 'visited'
5. Move until there is no space left.
6. Pick a random 'visited' cell.
7. Go to step 2 until you're satisfied with the maze.

Code

```c
/* This program is free software. It comes without any warranty, to
 * the extent permitted by applicable law. You can redistribute it
 * and/or modify it under the terms of the Do What The Fuck You Want
 * To Public License, Version 2, as published by Sam Hocevar. See
 * http://sam.zoy.org/wtfpl/COPYING for more details. */

// NOTE: Since I like the c99 style, I've written this piece of code in it
// so compile with -std=c99!

#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// a magic number!
#define magic 666

// the map's width and height
#define max_x 70
#define max_y 40

// distance from a cell to another (in map blocks)
#define cellrad 2

// max map cells
const int maxc_x = max_x / cellrad - 1;
const int maxc_y = max_y / cellrad - 1;

// the map and cell arrays
int map[max_x][max_y];
int cell[max_x / cellrad - 1][max_y / cellrad - 1];

// just some counters
int visited_cells = 0, total_cells = 0;

// this function returns 1 if (x,y) is in the range 2...max_x/y-2, to prevent some nasty bugs
int inrange(int x, int y)
{
 if (x > 2 && y > 2 && x < max_x - 2 && y < max_y - 2)
  return 1;
 return 0;
}

// this function links two cells together
void clink(int x1, int y1, int x2, int y2)
{
 int cx = x1, cy = y1;
 while (cx != x2)
 {
  if (x1 > x2)
  {
   cx--;
  }
  else
  {
   cx++;
  }
  if (inrange(cx, cy) == 1)
   map[cx][cy] = 0;
 }
 while (cy != y2)
 {
  if (y1 > y2)
  {
   cy--;
  }
  else
  {
   cy++;
  }
  if (inrange(cx, cy) == 1)
   map[cx][cy] = 0;
 }
}

// this function counts the visited cells and the total cells
void count_cells(void)
{
 visited_cells = 0;
 total_cells = 0;
 for (int ix = 0; ix < maxc_x; ix++)
 {
  for (int iy = 0; iy < maxc_y; iy++)
  {
   total_cells++;
   if (cell[ix][iy] == 1)
    visited_cells++;
  }
 }
}

// this is the main maze-generating function
void mkmaze(void)
{
 // some ints for random x, random y and random direction
 int rx = 0, ry = 0, rd = 0;
 // c is used to count tries -- if there are too many tries, the main loop
 // jumps to the end of the function
 int c = 0;

 // we fill the map with 'walls'
 for (int ix = 0; ix < max_x; ix++)
 {
  for (int iy = 0; iy < max_y; iy++)
  {
   map[ix][iy] = 1;
  }
 }

 // and the cell array with 'unvisited' cells
 for (int ix = 0; ix < maxc_x; ix++)
 {
  for (int iy = 0; iy < maxc_y; iy++)
  {
   cell[ix][iy] = 0;
  }
 }

 // we choose a random (x,y) pair, but here we use the center of the screen instead
 rx = maxc_x / 2;
 ry = maxc_y / 2;

 // we mark that cell 'visited'
 cell[rx][ry] = 1;

 // we count the cells
 count_cells();

 // and start the main loop!
 while (visited_cells < total_cells)
 {

  // c is increased, and we check whether it is too big
  c++;
  if (c > magic)
   goto hell;

  // we choose a random direction
  rd = rand() % 4;

  // and then dig!
  if (rd == 0)
  { // up
   if (inrange(rx * cellrad, ry * cellrad - 1) == 1 && (cell[rx][ry - 1] == 0 || rand() % 7 == 6))
   {
    clink(rx * cellrad, ry * cellrad, rx * cellrad, (ry - 1) * cellrad);
    ry--;
   }
   else
   {
    while (1)
    {
     rx = rand() % maxc_x;
     ry = rand() % maxc_y;
     if (cell[rx][ry] == 1)
      break;
    }
   }
  }
  else if (rd == 1)
  { // down
   if (inrange(rx * cellrad, ry * cellrad + 1) == 1 && (cell[rx][ry + 1] == 0 || rand() % 7 == 6))
   {
    clink(rx * cellrad, ry * cellrad, rx * cellrad, (ry + 1) * cellrad);
    ry++;
   }
   else
   {
    while (1)
    {
     rx = rand() % maxc_x;
     ry = rand() % maxc_y;
     if (cell[rx][ry] == 1)
      break;
    }
   }
  }
  else if (rd == 2)
  { // left
   if (inrange(rx * cellrad - 1, ry * cellrad) == 1 && (cell[rx - 1][ry] == 0 || rand() % 7 == 6))
   {
    clink((rx - 1) * cellrad, ry * cellrad, rx * cellrad, ry * cellrad);
    rx--;
   }
   else
   {
    while (1)
    {
     rx = rand() % maxc_x;
     ry = rand() % maxc_y;
     if (cell[rx][ry] == 1)
      break;
    }
   }
  }
  else if (rd == 3)
  { // right
   if (inrange(rx * 2 + 1, ry * 2) == 1 && (cell[rx + 1][ry] == 0 || rand() % 7 == 6))
   {
    clink(rx * cellrad, ry * cellrad, (rx + 1) * cellrad, ry * cellrad);
    rx++;
   }
   else
   {
    while (1)
    {
     rx = rand() % maxc_x;
     ry = rand() % maxc_y;
     if (cell[rx][ry] == 1)
      break;
    }
   }
  }

  /* NOTE:    the above code checks whether the to-be-dug cell is free.
   *     If it isn't, and rand()%7==6, it links it anyway. This is used
   *     to give the maze loops, change 7 and 6 as you want to get different
   *     results */

  // now we mark the reached cell 'visited'
  cell[rx][ry] = 1;

  // and we put a 'floor' tile on the map, where the maze cell should be
  map[rx * cellrad][ry * cellrad] = 0;

  // now we count the cells, so we can know how many cells we've visited.
  count_cells();
 }

// hell is a bad place. but we use it to put 'floor' tiles where we have a 'visited' cell
hell:
 for (int ix = 0; ix < maxc_x; ix++)
 {
  for (int iy = 0; iy < maxc_y; iy++)
  {
   if (cell[ix][iy] == 1)
    map[ix * cellrad][iy * cellrad] = 0;
  }
 }
}

// now this is a simple function that shows us what we just dug.
// it uses ANSI codes, so it may or may not work on your system
void showmap(void)
{
 printf("\033[2J\033[1;1f");
 for (int iy = 0; iy < max_y; iy++)
 {
  for (int ix = 0; ix < max_x; ix++)
  {
   fprintf(stdout, "\033[%i;%if", iy, ix);
   switch (map[ix][iy])
   {
   case 1:
    printf("#");
    break;
   case 0:
    printf(".");
    break;
   default:
    printf("*");
    break;
   }
   printf("\n");
  }
 }
 printf("Done.\n");
}

// and now the main function...
int main(void)
{
 // we get a random seed
 srand(time(0));

 // we dig the maze
 mkmaze();

 // we show the map
 showmap();

 // and say 'goodbye'
 return 0;
}
```

Feel free to improve it, but please mention what exactly you changed. --Deveah.

## Sceptre's Implementation

---

Really liked the look of the caves generated with this algorithm, but thought I could condense it a bit. Instead of a long series of conditional statements, I generalized the digging process by using a direction lookup Table. Pushed some global variables into macros, changed a few names, mostly trying to help readability. Also should compile on a non-c99 compiler.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define MAZE_W 60
#define MAZE_H 25

#define CELL_R 2

#define MAGIC 666

#define MAX_C_W (MAZE_W / CELL_R - 1)
#define MAX_C_H (MAZE_H / CELL_R - 1)

// This saves us from making a bunch of similar conditionals
// inside our Maze function.
int dirList[4][2] = {{0, -1}, {1, 0}, {0, 1}, {-1, 0}};

int map[MAZE_W][MAZE_H];
int cell[MAX_C_W][MAX_C_H];

void initGrid(void)
{
 int i, j;
 for (i = 0; i < MAZE_W; i++)
 {
  for (j = 0; j < MAZE_H; j++)
  {
   map[i][j] = 1;
  }
 }
 // Since we're setting to 0, let's just memset and save ourselves the nested FOR loop.
 memset(cell, 0, sizeof(cell));
}

// Returns 1 if (x, y) wont jump us off map.
int inRange(int x, int y)
{
 if (x > 2 && y > 2 && x < MAZE_W - 2 && y < MAZE_H - 2)
  return 1;
 return 0;
}

// Links our Cells
void linkCells(int x0, int y0, int x1, int y1)
{
 int cx = x0;
 int cy = y0;
 while (cx != x1)
 {
  if (x0 > x1)
   cx--;
  else
   cx++;
  if (inRange(cx, cy) == 1)
   map[cx][cy] = 0;
 }
 while (cy != y1)
 {
  if (y0 > y1)
   cy--;
  else
   cy++;
  if (inRange(cx, cy) == 1)
   map[cx][cy] = 0;
 }
}

// This function puts Floor Tiles on our map based on our larger 'cells'
void fillCells(void)
{
 int i, j;
 for (i = 0; i < MAX_C_W; i++)
 {
  for (j = 0; j < MAX_C_H; j++)
  {
   if (cell[i][j] == 1)
    map[i * CELL_R][j * CELL_R] = 0;
  }
 }
}

void makeMaze(void)
{
 int rx = 0;
 int ry = 0;
 int dx;
 int dy;
 int dir = 0;
 int count = 0;

 int totalCells = MAX_C_W * MAX_C_H;

 initGrid();

 // Replace with desired startX/startY;
 rx = MAX_C_W / 2;
 ry = MAX_C_H / 2;

 cell[rx][ry] = 1;

 int visitedCells = 1; // The Cell we just assigned to 1!

 while (visitedCells < totalCells)
 {

  count++;
  if (count < MAGIC)
  {
   fillCells();
  }

  // Use Direction Lookup table for more Generic dig function.
  dir = rand() % 4;

  dx = dirList[dir][0];
  dy = dirList[dir][1];

  if (inRange(rx * CELL_R + dx, ry * CELL_R + dy) == 1 && cell[rx + dx][ry + dy] == 0 || rand() % 7 == 6)
  {
   linkCells(rx * CELL_R, ry * CELL_R, (rx + dx) * CELL_R, (ry + dy) * CELL_R);
   rx += dx;
   ry += dy;
  }
  else
  {
   do
   {
    rx = rand() % MAX_C_W;
    ry = rand() % MAX_C_H;
   } while (cell[rx][ry] != 1);
  }

  /* NOTE: Above code checks whether to-be-dug cell is free.
   *        If it isn't, and rand()%7 == 6, it links it anyways.
   *        This is done to create loops and give a more cavelike appearance.
   */

  cell[rx][ry] = 1;

  map[rx * CELL_R][ry * CELL_R] = 0;

  visitedCells++;
 }

 fillCells();
}

void printMaze(void)
{
 int i, j;
 for (j = 0; j < MAZE_H; j++)
 {
  for (i = 0; i < MAZE_W; i++)
  {
   if (map[i][j] == 1)
    printf("#");
   else if (map[i][j] == 0)
    printf(".");
  }
  printf("\n");
 }
 printf("VOILA!");
}

int main(void)
{
 srand(time(0));
 makeMaze();
 printMaze();

 return 0;
}
```

## Related Links

---

- [Simple maze](simple_maze.md)
- [Dynamically Sized Maze](dynamically_sized_maze.md)
