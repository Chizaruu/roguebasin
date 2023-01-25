# Pathfinding

---

```c
/*
The most generic algorithm is something like:

    1. Start with empty priority queue P
    2. Start with empty set of previously visited nodes V
    3. Add start node s to P with cost f(s)
    4. If P is empty, then stop, no solution.
    5. Remove node x with lowest f(x) from P.
    6. If x is goal, stop with success.
    7. For each nx in successors of x:
        a) calculate f(nx) (= f(x) + delta)
        b) if nx not visited yet OR
              nx is in the priority queue P but with higher f(nx) OR
              nx has been visited but with higher f(nx)
           then place/update nx to P with new cost and
                update V to include (nx, f(nx), x)
    8. Go to 4

Then if
  f(x) equals to cost from start node, it is djikstra
  f(x) equals to cost from start node + estimate to goal, the algorithm is A*.
*/

/* pathfind.c - simple path finder - public domain */
#include    <stdio.h>
#include    <stdlib.h>
#include    <time.h>
#include    <string.h>

#define MAP_WIDTH 70
#define MAP_HEIGHT 25
#define MAP_SIZE MAP_WIDTH*MAP_HEIGHT

#define MAP_BLOCKERS 500
#define MAP_BLOCKERS_POS_TRIES 100
#define MAP_PATHS 10000
#define MAP_PATHS_POS_TRIES 100

//#define ASTAR
//#define MAP_VARIYING_COST

/* Map - 1 means blocked, 0 means passable */
static int map[ MAP_SIZE ];

/* States of path elements */
enum path_element_state
    {
    path_element_empty,
    path_element_open,
    path_element_closed
    };

/* Structure for path elements */
struct path_element
    {
    int x_pos_;
    int y_pos_;
    int cost_;
    int estimate_;
    enum path_element_state state_;
    struct path_element* parent_;
    };

/* Nodes - one per cell in map */
static struct path_element path_nodes[ MAP_SIZE ];

/* Open nodes */
static struct path_element* path_open[ MAP_SIZE ];

/* Top index */
static int path_open_top;

/* Calculates distance between two points */
int path_distance( int x0, int y0, int x1, int y1 );
/* Checks if position is blocked */
int path_blocked( int x, int y );
/* Initialises path structures */
void path_init( void );
/* Maps coordinate to one dimensional array */
int path_map( int x, int y );
/* Returns pointer to path element, or NULL if position is invalid */
struct path_element* path_element_map( int x, int y );
/* Calculates cost from element to goal. */
int path_cost( struct path_element* element, int goalx, int goaly );
/* Pushes element to open stack */
void path_push_open( struct path_element* element, int goalx, int goaly );
/* Pops element from open stack */
struct path_element* path_pop_open( void );
/* Removes element from stack */
void path_remove( struct path_element* element );
/* Checks if there are elements in open stack */
int path_open_not_empty( void );
/* Pushes position to open stack when needed */
void path_check( struct path_element* parent,
    int posx, int posy, int goalx, int goaly );
/* Finds path from start towards goal */
int path_find( int startx, int starty, int goalx, int goaly );
/* Gets next step towards goal. */
int path_get_next( int* nextx, int* nexty );
/* Updates cost to pos which is adjacent to parent */
void path_update_cost( struct path_element* parent, struct path_element*
pos );

int main()
    {
    int i, j, k;
    int seed = time( NULL );
    memset( map, 0, MAP_SIZE*sizeof( int ) );

    /* Put some dirt to the map */
    for ( i = 0; i < MAP_WIDTH; i++ )
        {
        map[ path_map( i, 0 ) ] = 1;
        map[ path_map( i, MAP_HEIGHT - 1 ) ] = 1;
        }
    for ( j = 0; j < MAP_HEIGHT; j++ )
        {
        map[ path_map( 0, j ) ] = 1;
        map[ path_map( MAP_WIDTH - 1, j ) ] = 1;
        }
    for ( i = 0; i < MAP_BLOCKERS; i++ )
        {
        for ( j = 0; j < MAP_BLOCKERS_POS_TRIES; j++ )
            {
            int x = rand() % MAP_WIDTH;
            int y = rand() % MAP_HEIGHT;

            if ( !path_blocked( x, y ) )
                {
                map[ path_map( x, y ) ] = 1;
                break;
                }
            }
        }

    srand( seed );

    /* Show map */
    printf( "Map with seed = %x:\n", seed );
    k = 0;
    for ( j = 0; j < MAP_HEIGHT; j++ )
        {
        if ( j )
            {
            printf( "\n" );
            }

        for ( i = 0; i < MAP_WIDTH; i++ )
            {
            if ( !map[ k ] )
                {
                printf( "." );
                }
            else
                {
                printf( "#" );
                }

            k++;
            }
        }

    printf( "\n" );

    /* Generate some paths */
    for ( k = 0; k < MAP_PATHS; k++ )
        {
        int start_x;
        int start_y;
        int goal_x;
        int goal_y;
        int path;
        int steps;

        printf( "Path %d:\n", k + 1 );

        for ( i = 0; i < MAP_PATHS_POS_TRIES; i++ )
            {
            start_x = rand() % MAP_WIDTH;
            start_y = rand() % MAP_HEIGHT;

            if ( !path_blocked( start_x, start_y ) )
                {
                break;
                }
            }

        for ( j = 0; j < MAP_PATHS_POS_TRIES; j++ )
            {
            goal_x = rand() % MAP_WIDTH;
            goal_y = rand() % MAP_HEIGHT;

            if ( !path_blocked( goal_x, goal_y ) )
                {
                break;
                }
            }
        if ( ( i == MAP_PATHS_POS_TRIES ) ||
             ( j == MAP_PATHS_POS_TRIES ) )
            {
            printf( "\tFailed to generate start & goal\n" );
            continue;
            }

        /* Goal & start location are Ok, generate path */
        printf( "\tStart = (%d, %d), goal = (%d, %d)\n",
            start_x, start_y, goal_x, goal_y );

        path = path_find( start_x, start_y, goal_x, goal_y );
        if ( path )
            {
            /* Print out steps to goal. */
            steps = 0;
            while ( path )
                {
                int prev_x = start_x;
                int prev_y = start_y;

                path = path_get_next( &start_x, &start_y );

                if ( !steps )
                    {
                    printf( "\t(%2d, %2d) -> (%2d, %2d)",
                        prev_x, prev_y, start_x, start_y );
                    }
                else
                    {
                    if ( ( steps + 1 ) & 7 )
                        {
                        printf( " -> (%2d, %2d)", start_x, start_y );
                        }
                    else
                        {
                        printf( " ->\n\t(%2d, %2d)", start_x, start_y );
                        }
                    }

                steps++;
                }

            if ( ( start_x == goal_x ) &&
                 ( start_y == goal_y ) )
                {
                printf( "\n\tFound target!\n" );
                }
            }
        else
            {
            printf( "\tCould not find path\n" );
            }
        }

    return EXIT_SUCCESS;
    }

/* Calculate distance between two points */
int path_distance( int x0, int y0, int x1, int y1 )
    {
    int xd = ( x0 - x1 );
    int yd = ( y0 - y1 );

    if ( xd < 0 )
        {
        xd = -xd;
        }
    if ( yd < 0 )
        {
        yd = -yd;
        }

    return xd + yd;
    }

/* Checks if position is blocked */
int path_blocked( int x, int y )
    {
    return map[ path_map( x, y ) ];
    }

/* Initialises path structures. */
void path_init( void )
    {
    int i, j;
    int p;

    /* Everything done here is not absolutely necessary. */
    memset( path_nodes, 0, MAP_SIZE * sizeof( struct path_element ) );
    memset( path_open, 0, MAP_SIZE * sizeof( struct path_element* ) );
    path_open_top = 0;

    p = 0;
    for ( j = 0; j < MAP_HEIGHT; j++ )
        {
        for ( i = 0; i < MAP_WIDTH; i++ )
            {
            path_nodes[ p ].x_pos_ = i;
            path_nodes[ p ].y_pos_ = j;
            p++;
            }
        }
    }

/* Maps coordinates to one dimensional array indices. */
int path_map( int x, int y )
    {
    return y * MAP_WIDTH + x;
    }

/* Returns pointer to path element structure or NULL if invalid position. */
struct path_element* path_element_map( int x, int y )
    {
    struct path_element* result = NULL;

    if ( ( x >= 0 && x < MAP_WIDTH ) &&
         ( y >= 0 && y < MAP_HEIGHT ) )
        {
        result = &path_nodes[ path_map( x, y ) ];
        }

    return result;
    }

/* Returns cost from position defined by element to goal.*/
int path_cost( struct path_element* element, int goalx, int goaly )
    {
#ifdef ASTAR
    if ( !element->estimate_ )
        {
        element->estimate_ =
            path_distance( element->x_pos_, element->y_pos_, goalx, goaly );
        }
#endif

    return element->cost_ + element->estimate_;
    }

/* Pushes pointer to one element to the open stack */
void path_push_open( struct path_element* element, int goalx, int goaly )
    {
    int i;

    path_open[ path_open_top ] = element;
    path_open_top++;

    /* Keep elements in ascending order by distance to goal
       - we want to find one of the shortest paths */
    for ( i = path_open_top - 1; i >= 1; i-- )
        {
        struct path_element* current = path_open[ i ];
        struct path_element* next = path_open[ i - 1 ];

#if 0
        if ( path_distance( next->x_pos_, next->y_pos_, goalx, goaly ) <
             path_distance( current->x_pos_, current->y_pos_, goalx,
goaly ) )
#else
        int next_total = path_cost( next, goalx, goaly );
        int current_total = path_cost( current, goalx, goaly );
        if ( next_total < current_total )
            {
#endif
            /* Swap pointers */
            path_open[ i ] = next;
            path_open[ i - 1 ] = current;
            }
        }
    }

/* Pops one element from the open stack */
struct path_element* path_pop_open( void )
    {
    path_open_top--;
    struct path_element* result = path_open[ path_open_top ];
    path_open[ path_open_top ] = NULL;
    return result;
    }

/* Removes element from stack */
void path_remove( struct path_element* element )
    {
    int i;
    for ( i = 0; i < path_open_top; i++ )
        {
        if ( element == path_open[ i ] )
            {
            break;
            }
        }

    if ( i != path_open_top )
        {
        /* Element was found. */
        path_open[ i ] = NULL;
        for ( ; i < ( path_open_top - 1 ); i++ )
            {
            path_open[ i ] = path_open[ i + 1 ];
            }

        path_open_top--;
        }
    }

/* Checks that open stack is not empty. */
int path_open_not_empty( void )
    {
    return path_open_top;
    }

/* Pushes to open stack unless already open, closed or impassable */
void path_check(
        struct path_element* parent,
        int posx, int posy, int goalx, int goaly )
    {
    struct path_element* pos = path_element_map( posx, posy );
    if ( pos )
        {
        /* We can ignore blocked positions (consider that cost is
infinite).*/
        if ( !path_blocked( posx, posy ) )
            {
            /* If not processed yet, add to open set */
            if ( pos->state_ == path_element_empty )
                {
                pos->state_ = path_element_open;
                pos->parent_ = parent;
                path_update_cost( parent, pos );

                path_push_open( pos, goalx, goaly );
                }
            else
                {
                /* Now element is either in open or closed set. */
                const int orig_cost = path_cost( pos, goalx, goaly );
                struct path_element tmp = *pos;
                path_update_cost( parent, &tmp );

                if ( orig_cost > path_cost( &tmp, goalx, goaly ) )
                    {
                    /* New path is better than old. */
                    int was_open = ( pos->state_ == path_element_open );

                    *pos = tmp;
                    pos->parent_ = parent;
                    pos->state_ = path_element_open;

                    if ( was_open )
                        {
                        path_remove( pos );
                        }

                    path_push_open( pos, goalx, goaly );
                    }
                }
            }
        }
    }

/* Updates startx and starty one step towards (goalx, goaly). */
int path_find( int startx, int starty, int goalx, int goaly )
    {
    int found = 0;

    /* Parent points towards start of search. So if we start from goal,
       then those parent pointers are automatically ok.

       Of course, the following assumption is made (reflexivity):
         if there is path from A to B, then there is path from B to A.
       (this holds in this application)
     */
    const int real_goal_x = startx;
    const int real_goal_y = starty;
    const int real_start_x = goalx;
    const int real_start_y = goaly;

    path_init();

    struct path_element* pos = path_element_map( real_start_x,
real_start_y );
    struct path_element* start = path_element_map( real_goal_x,
real_goal_y );
    if ( pos && start ) /* Both should be acceptable */
        {
        /* Push first element to stack. */
        pos->state_ = path_element_open;
        path_push_open( pos, real_goal_x, real_goal_y );

        while ( path_open_not_empty() )
            {
            struct path_element* current = path_pop_open();

            /* Did we reach target? */
            if ( ( current->x_pos_ == real_goal_x ) &&
                 ( current->y_pos_ == real_goal_y ) )
                {
                found = 1;
                break;
                }

            /* Current is closed. */
            current->state_ = path_element_closed;

            /* Generate positions reachable from current position. */
            path_check( current,
                current->x_pos_ + 1, current->y_pos_,
                real_goal_x, real_goal_y );
            path_check( current,
                current->x_pos_ - 1, current->y_pos_,
                real_goal_x, real_goal_y );
            path_check( current,
                current->x_pos_, current->y_pos_ + 1,
                real_goal_x, real_goal_y );
            path_check( current,
                current->x_pos_, current->y_pos_ - 1,
                real_goal_x, real_goal_y );
            /* So if you want to allow diagonal movement, add four
additional
               path_check calls. */
            }
        }

    return found;
    }

/* Gets next step towards goal - path_find must have been called beforehand.
   Results are erased after next path_find call.
 */
int path_get_next( int* nextx, int* nexty )
    {
    int result = 0;
    struct path_element* pos = path_element_map( *nextx, *nexty );
    if ( pos->parent_ )
        {
        *nextx = pos->parent_->x_pos_;
        *nexty = pos->parent_->y_pos_;
        result = ( pos->parent_->parent_ != NULL );
        }

    return result;
    }

const static int cost_table_a[3][3] =
    { { 1, 1, 1 },
      { 3, 1, 3 },
      { 1, 1, 1 } };

const static int cost_table_b[3][3] =
    { { 1, 3, 1 },
      { 1, 1, 1 },
      { 1, 3, 1 } };

/* Updates cost to pos which is adjacent to parent. */
void path_update_cost( struct path_element* parent, struct path_element*
pos )
    {
#ifndef MAP_VARIYING_COST
    pos->cost_ = parent->cost_ + 1;
#else
    int dx = pos->x_pos_ - parent->x_pos_ + 1; /* dx = 0, 1, 2 */
    int dy = pos->y_pos_ - parent->y_pos_ + 1; /* dy = 0, 1, 2 */

    if ( pos->x_pos_ & 1 )
        {
        pos->cost_ = parent->cost_ + cost_table_a[dx][dy];
        }
    else
        {
        pos->cost_ = parent->cost_ + cost_table_b[dx][dy];
        }
#endif // MAP_VARIYING_COST
    }

/* End of file */


// Pekka Nurminen
```
