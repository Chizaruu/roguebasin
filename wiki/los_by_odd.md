# LOS by Odd

---

```c
#include <iostream.h>
#include <stdio.h>
#include <conio.h>
#include <math.h>
#include <windows.h>

/* by odd lobster@takas.lt
** map: 0-passable terrain(los can see), 1-block, 9-player start position(actualy this one isnt used), 5 - los can see, 3-player position
** ;)
*/
int myMap[20][20]=  {1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,1,
                     1,0,0,0,1,1,0,0,0,0,0,0,0,0,1,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,9,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,1,1,0,1,1,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
                     1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1};

void drawline_mod(int map[20][20], int x, int y, int x2, int y2) {
    int dx = abs(x - x2);
    int dy = abs(y - y2);
    double s = double(.99/(dx>dy?dx:dy));
    double t = 0.0;
    while(t < 1.0) {
        dx = int((1.0 - t)*x + t*x2);
        dy = int((1.0 - t)*y + t*y2);
        if (map[dy][dx] != 1) {
            map[dy][dx] = 5;
        } else {
            return;
        }
        t += s;
    }
}

void los(int map[20][20], int range, int plx, int ply) {
    int x, y;
    for (double f = 0; f < 3.14*2; f += 0.05) {
        x = int(range*cos(f)) + plx;
        y = int(range*sin(f)) + ply;
        drawline_mod(map,plx,ply,x,y);
    }
}

void draw_map(int map[20][20]) {
    for (int y=0; y<20; y++) {
        for (int x=0; x<20; x++) {
            if (map[y][x] == 0) {
                printf(" ");
            } else if (map[y][x] == 1) {
                printf("X");
            } else if (map[y][x] == 5) {
                printf(".");
            } else if (map[y][x] == 3) {
                printf("@");
            }
        }
        printf("\n");
    }
}

void main() {
    LARGE_INTEGER i1, i2, f;
    QueryPerformanceFrequency(&f);
    QueryPerformanceCounter(&i1); // start test
    los(myMap,99,8,6); // <- call los
    QueryPerformanceCounter(&i2); // end test
    int iii = 1000000*(i2.QuadPart - i1.QuadPart)/f.QuadPart;
    printf("Code executed in: %i/%i ms/ns\n", iii/1000, iii);
    myMap[6][8] = 3; // put player
    draw_map(myMap); // draw map ;)
    getch();
}


By Odd [[lobster@takas.lt]]
```
