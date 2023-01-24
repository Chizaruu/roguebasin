# Ray-Tracing Field-Of-View Demo

---

Ray-Ttracing Field-Of-View Demo - Greg McIntyre [greg@puyo.cjb.net].txt

NB. You can download a Linux source distro of this article here.

## The Algorithm

---

This is a ray-tracing algorithm which uses a bit of precalculation to speed things up, and a hack to make things look (nearly) right.

Each turn, rays are traced from the player in every direction to the end of his perception. These rays mark everything as 'seen' until they hit something opaque.

I believe the algorithm has efficiency O(n^2) (n scaling linearly with the radius of the FOV). But then, I was never terribly good at figuring out orders of efficiency. ;)

## The Hack

---

The hack tests the tile adjacent to the current tile at each step along the ray, to alleviate what I call the 'low angle wall case'.

This is where rays move nearly horizontal to a wall, catch on the wall's tiles and prematurely stop. This leaves gaps in the FOV along the wall and looks wrong.

```text
@xxx
xxxx
########!???########

! = displayed
? = mistake; not displayed!
```

Without the hack, it looks something like this:

```text
@
########## ### ### ### # # #
```

The hack checks the tile away from the wall. The 'adjacent' tile, for want of a better name. It also checks the 'adjacent' tile from the previous tile in the ray. If both are clear, then we've encountered that nasty 'low angle wall case', and we know to keep tracing.

```text
aaa
@xxxaaaa
xxxxaaaa
########!!!!a#######

! = displayed
```

With the hack in place, it looks like this:

```text
@
#############################################
```

There are still some little jitters around corners and between walls sometimes. This seems fairly unavoidable at such a low resolution, however I am certain some of them can be ironed out. Anyone want to fix this up for me? :)

## Compilation

---

Make sure you have the [Curses](curses_library.md), [Ncurses](ncurses.md) or [PDCurses](pdcurses.md) library. I used ncurses and I haven't tested this with other libraries, but it's very simple and should, in theory, work.

Compile with:

gcc -o fov fov.c -Wall -lncurses

where ncurses can be replaced with curses or pdcurses or whatever.

## Usage

---

Turn numlock on and use the keypad to move. Press 'q'/'Q'/Esc to quit.

```c
#include <curses.h>

/* DEFINES -------------------------------------------------------- */

#define MAP_WIDTH 80
#define MAP_HEIGHT 50
#define PERCEPTION_RADIUS 80

#define PLAYER '@'
#define GROUND '.'
#define WALL '#'

#define REMEMBERED_COLOUR 1
#define PLAYER_COLOUR 2
#define SEEN_COLOUR 3

#define PLAYER_START_X 20
#define PLAYER_START_Y 5

#define ERROR_NOCOLOUR 1

#ifndef bool
#define bool char
#endif

#ifndef true
#define true -1
#endif

#ifndef false
#define false 0
#endif

/* TYPE DEFS ------------------------------------------------------ */

typedef struct {
    int x, y;
} COORD;

typedef struct {
    char ch; /* this tile's ASCII character */
    bool seen; /* this tile can be seen by the player */
    bool remembered; /* this tile has been seen by the player */
} TILE;


/* GLOBAL DATA ---------------------------------------------------- */

/* map */
TILE map[MAP_WIDTH][MAP_HEIGHT];

/* fov lookup table */
COORD octant[PERCEPTION_RADIUS][PERCEPTION_RADIUS];

/* player coodinates */
unsigned char px = PLAYER_START_X, py = PLAYER_START_Y;


/* FUNCTION PROTOTYPES -------------------------------------------- */

int init_curses(void);
void exit_curses(void);

void create_map(void);
void create_octant(void);
void do_line(int x1, int y1, int x2, int y2);

void draw_map(void);
void do_fov(void);

void move_player(int dx, int dy);

TILE * tile(int x, int y);
bool valid_tile(int x, int y);


/* FUNCTION DEFS -------------------------------------------------- */

int main(void)
{
    int k; /* keystoke */
    int ret; /* return code */

    if ((ret = init_curses()) != 0) {
        return ret;
    }

    create_map();
    create_octant();

    /* main loop */
    for (;;) {

        /* draw map */
        draw_map();

        k = getch();

        /* analyse key press */
        switch (k) {
            case ('8'): move_player( 0, -1); break;
            case ('2'): move_player( 0, 1); break;
            case ('4'): move_player(-1, 0); break;
            case ('6'): move_player( 1, 0); break;
            case ('7'): move_player(-1, -1); break;
            case ('3'): move_player( 1, 1); break;
            case ('9'): move_player( 1, -1); break;
            case ('1'): move_player(-1, 1); break;

            case ('q'):
            case ('Q'):
            case (27):
                goto end;
        };

    }

    end:
    exit_curses();
    return 0;
}

/* initialise the curses library, for drawing colourful ASCII text */
int init_curses(void)
{
    /* init curses */
    initscr();
    noecho();
    curs_set(0);
    if (has_colors()) {
        start_color();
    } else {
        printf("Your system does not support colour. This demo requires"
               "colours!");
        return ERROR_NOCOLOUR;
    }

    /* set up some colours */
    init_pair(0, COLOR_BLACK, COLOR_BLACK);
    init_pair(REMEMBERED_COLOUR, COLOR_WHITE, COLOR_BLACK);
    init_pair(PLAYER_COLOUR, COLOR_GREEN, COLOR_BLACK);
    init_pair(SEEN_COLOUR, COLOR_BLUE, COLOR_BLACK);

    return 0;
}


/* close curses and return to normal text screen mode */
void exit_curses(void)
{
    endwin();
}


/* create an arbitrary map for the player to walk around */
void create_map(void)
{
    int i, j; /* coordinate counters */

    /* initialise the map to ground (GROUND) */
    for (j = 0; j != MAP_HEIGHT; ++j) {
        for (i = 0; i != MAP_WIDTH; ++i) {
            map[i][j].ch = GROUND;
            map[i][j].seen = false;
            map[i][j].remembered = false;
        }
    }

    /* draw some fairly arbitrary walls */
    for (i = 10; i != 20; ++i)
        map[i][10].ch = WALL;
    for (i = 10; i != 40; ++i)
        map[i][20].ch = WALL;

    for (j = 10; j != 21; ++j)
        map[10][j].ch = WALL;
    for (i = 10; i != 21; ++i)
        map[40][i].ch = WALL;

    for (i = 10; i != 21; ++i)
        map[11][i].ch = WALL;
    for (i = 10; i != 21; ++i)
        map[12][i].ch = WALL;

    for (i = 20; i != 25; ++i)
        map[30][i].ch = WALL;

    for (j = 15; j != 70; ++j)
        map[j][30].ch = WALL;
}


/* pre-calculate a single octant */
void create_octant(void)
{
    /*
    @
    |\
    ||\
    ||\\
    */
    int i;
    for (i = 0; i != PERCEPTION_RADIUS; ++i) {
        do_line(0, 0, i, PERCEPTION_RADIUS);
    }
}


/* (x,y) is inside the map boundary */
bool valid_tile(int x, int y)
{
    return (((unsigned)x < MAP_WIDTH) && ((unsigned)y < MAP_HEIGHT));
}


/* the tile at (x,y) */
TILE *tile(int x, int y)
{
    if (valid_tile(x, y))
        return &map[x][y];
    else
        return NULL;
}


/* move the player by an offset (dx,dy) */
void move_player(int dx, int dy)
{
    if (valid_tile(px + dx, py + dy)) {
        if (map[px + dx][py + dy].ch != WALL) {
            px += dx;
            py += dy;
        }
    }
}


/* calculate which tiles can be seen by the player */
void do_fov(void)
{
    int xoff, yoff; /* x offset, y offset */
    TILE *t = NULL; /* current tile */
    TILE *adj_t = NULL; /* adjacent tile (see explanation at top) */
    TILE *last_t = NULL; /* last tile */
    TILE *last_adj_t = NULL; /* last adjacent tile */
    int tx, ty; /* current tile x, current tile y */
    int i, j; /* counters */
    bool wall; /* the current tile is a wall */
    bool adj_clear; /* the adjacent tile is clear (not a wall) */
    bool last_adj_clear; /* the adjacent tile from the last
    iteration is clear (not a wall) */

    /* un-see everything */
    for (j = 0; j != MAP_HEIGHT; ++j) {
        for (i = 0; i != MAP_WIDTH; ++i) {
            if (tile(i, j)-&gtseen) {
                tile(i, j)-&gtremembered = true;
            }
            tile(i, j)-&gtseen = false;
        }
    }

    /* damn ugly octant definition - the heart of the algorithm */

#define DO_OCTANT(x, y, sx, sy, dx, dy)

    for (i = 0; i != PERCEPTION_RADIUS; ++i) {

        last_t = NULL;
        last_adj_t = NULL;

        for (j = 0; j != PERCEPTION_RADIUS; ++j) {

            /* get the current tile offset from the player */
            xoff = octant[i][j].x;
            yoff = octant[i][j].y;

            /* get the current tile */
            tx = px sx xoff;
            ty = py sy yoff;
            t = tile(tx, ty);

            /* if we've gone off into null territory, stop ray */
            if (t == NULL) break;

            /* get the adjacent tile */ \
            adj_t = tile(tx - (sx dx), ty - (sy dy));

            if (last_t) {
                if (last_t-&gtch != WALL) {
                    /* if we're not behind a wall, we can see what's here */
                    t-&gtseen = true;
                } else /* last char was a wall */ {

                    /* if horizontal/vertical case, stop ray */
                    if (x##off == 0) break;

                    /* if we reach here, we've got a potential wall case */
                    wall = t-&gtch == WALL;
                    adj_clear = adj_t && (adj_t-&gtch != WALL);
                    last_adj_clear = last_adj_t && (last_adj_t-&gtch != WALL);

                    /* if we find wall next to clear stuff, continue */
                    /* along the wall */
                    if ((wall && adj_clear && last_adj_clear)) {
                        t-&gtseen = true;
                    } else {
                        /* stop ray */
                        break;
                    }
                }
            }
            last_t = t;
            last_adj_t = adj_t;
        }
    }

    /* do *that* 8 times, once for each octant */
    DO_OCTANT(x, y, +, +, 1, 0);
    DO_OCTANT(x, y, +, -, 1, 0);
    DO_OCTANT(x, y, -, +, 1, 0);
    DO_OCTANT(x, y, -, -, 1, 0);
    DO_OCTANT(y, x, +, +, 0, 1);
    DO_OCTANT(y, x, +, -, 0, 1);
    DO_OCTANT(y, x, -, +, 0, 1);
    DO_OCTANT(y, x, -, -, 0, 1);
}


/* draw the map */
void draw_map(void)
{
    int i, j;

    /* calculate FOV */
    do_fov();

    /* draw all the tiles */
    for (j = 0; j != MAP_HEIGHT; ++j) {
        for (i = 0; i != MAP_WIDTH; ++i) {
            if (map[i][j].seen) {
                mvaddch(j, i, map[i][j].ch | COLOR_PAIR(SEEN_COLOUR));
            } else if (map[i][j].remembered) {
                mvaddch(j, i, map[i][j].ch | COLOR_PAIR(REMEMBERED_COLOUR));
            }
        }
    }

    /* draw the player */
    mvaddch(py, px, PLAYER | COLOR_PAIR(PLAYER_COLOUR));
    refresh();
}


/* walk along line from (x1, y1) to (x2, y2), setting values in octant
*/
void do_line(int x1, int y1, int x2, int y2)
{
    int dx = x2 - x1;
    int dy = y2 - y1;
    int i1, i2;
    int x, y;
    int dd;
    int i = x2;
    int j = 0;

    #define DO_LINE(pri_sign, pri_c, pri_cond, sec_sign, sec_c, sec_cond)
    {
        if (d##pri_c == 0)
        {
            octant[i][j].x = x1;

            octant[i][j].y = y1;

            ++j;

            return;
        }

        i1 = 2 * d##sec_c;

        dd = i1 - (sec_sign (pri_sign d##pri_c));

        i2 = dd - (sec_sign (pri_sign d##pri_c));

        x = x1;

        y = y1;

        while (pri_c pri_cond pri_c##2)
        {
            octant[i][j].x = x;

            octant[i][j].y = y;

            ++j;

            if (dd sec_cond pri_sign##1)
            {
                sec_c sec_sign##= 1;

                dd += i2;
            }

            else

            dd += i1;

            pri_c pri_sign##= 1;
        }
    }

    if (dx >= 0)
    {
        if (dy >= 0)
        {
            if (dx >= dy)
            {
                /* (x1 <= x2) && (y1 <= y2) && (dx >= dy) */
                DO_LINE(+, x, <=, +, y, >=);
            }
            else
            {
                /* (x1 <= x2) && (y1 <= y2) && (dx < dy) */
                DO_LINE(+, y, <=, +, x, >=);
            }
        }
        else
        {
            if (dx >= -dy)
            {
                /* (x1 <= x2) && (y1 > y2) && (dx >= dy) */
                DO_LINE(+, x, <=, -, y, <=);
            }
            else
            {
                /* (x1 <= x2) && (y1 > y2) && (dx < dy) */
                DO_LINE(-, y, >=, +, x, >=);
            }
        }
    }
    else
    {
        if (dy >= 0)
        {
            if (-dx >= dy)
            {
                /* (x1 > x2) && (y1 <= y2) && (dx >= dy) */
                DO_LINE(-, x, >=, +, y, >=);
            }
            else
            {
                /* (x1 > x2) && (y1 <= y2) && (dx < dy) */
                DO_LINE(+, y, <=, -, x, <=);
            }
        }
        else
        {
            if (-dx >= -dy)
            {
                /* (x1 > x2) && (y1 > y2) && (dx >= dy) */
                DO_LINE(-, x, >=, -, y, <=);
            }
            else
            {
                /* (x1 > x2) && (y1 > y2) && (dx < dy) */
                DO_LINE(-, y, >=, -, x, <=);
            }
        }
    }
}
```
