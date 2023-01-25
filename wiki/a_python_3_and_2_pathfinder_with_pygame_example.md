# A Python 3 and 2 Pathfinder with Pygame Example

---

**by James Spencer**

Originally my Roguelike had a pathfinder that predated me even starting to write a Roguelike. It suffered badly from being both my first serious pathfinder and some of my earliest Python code. Layer on a raft of incremental additions, and it got complicated and weird, and well, it had to go. I intend to use this pathfinder as a base for any personal Python project that requires pathfinder. I designed this pathfinder to take advantage of the similarities between A-Star and Dijkstra pathfinding, while leveraging the high level nature of Python to make the actual search functions as simple and pseudocode like as possible. This implementation is designed to work on grid based worlds (with the concept of distance as opposed to steps), though extending it to work with hex based worlds would be trivial. It is intended to be minimal and comprehensible, and doesn't include any provisions for variable movement costs though they would be easy to add.

Secondly, let me make it clear that this is not intended as a tutorial on A-Star pathfinding or Dijkstra pathfinding, it is intended as a pure python pathfinder that is: 1) drop in ready, 2) easily modifiable, and 3) fast enough. Additionally, it is licensed under CC0, so use it for whatever you want.

If you are interested in tutorials on pathfinding I would suggest:

- The Wikipedia pages on "A\* search algorithm" (<https://en.wikipedia.org/wiki/A*_search_algorithm>) and "Dijkstra's algorithm" (<https://en.wikipedia.org/wiki/Dijkstra>'s_algorithm)
- The Red Blob Games pages on pathfinding (<http://www.redblobgames.com/pathfinding/a-star/introduction.html> and <http://www.redblobgames.com/pathfinding/a-star/implementation.html>)
- "A\* Pathfinding for Beginners" by Patrick Lester (<http://www.policyalmanac.org/games/aStarTutorial.htm>)
- "Best practices for A\* on grids" by riscy (<https://github.com/riscy/a_star_on_grids>)

## The Pathfinder(Area()) class has four public functions

1. find_point(self, x1, y1, x2, y2, use_diagonals=True, best_path=True, abort=False)
1. is_point_findable(self, x1, y1, x2, y2, use_diagonals=True, abort=False)
1. find_tile(self, x1, y1, tile, use_diagonals=True, best_path=True, abort=False)
1. nearest(self, x1, y1, tile, use_diagonals=True, abort=False)

All of these functions are documented in the code. In short, 'find_point()' will return deque() with a path from x1, y1 to x2, y2 (or None if no path is found), 'is_point_findable()' will return a Boolean if a point can be found, 'find_tile()' will return deque() with a path from x1, y1 to the nearest tile (or tile in an iterable of tiles) as specified by 'tile' (or None if no path is found), and finally 'nearest()' will look for a tile (or tile in an iterable of tiles) and return a Tuple of '(x, y, tile name)' (or None if no tile is found).

Both 'find_point()' and 'is_point_findable()' use an A-Star search, while 'find_tile()' and 'nearest()' use a Dijkstra search. As was mentioned this implementation is designed for grid based worlds, and is distance aware, meaning that it will generate paths based on the shortest distance as opposed to the fewest steps. While largely moot for cardinal only movement, this does have implications when diagonal movement is allowed (use_diagonals == True).

## Pathfinder Implementation Notes

- The actual pathfinder only requires Python, and the example requires Python and Pygame. Pygame should be installable via 'pip'.
- The pathfinder expects an 'Area()' class where: 'area.terrain' is a list of lists of terrain names (like a 2D array), 'area.width' is the width of a rectangular area, and 'area.height' is the height of a rectangular area.
- The pathfinder expects a 'config.OBSTRUCTION_CHARACTERS' set / tuple / list, where the members are tiles that can't be pathed through. This could also be dictionary where the keys are the tiles that can't be pathed through, and the values can be whatever you want (with good ideas being the character representation of the tile or a Pygame surface with the tile's graphic).
- The pathfinder is designed to be flexible. Thus setting 'self.\_directions' makes it easy to choose between searching cardinal directions versus searching cardinal and diagonal directions, setting 'self.\_heuristic' makes it easy to do a Dijkstra search ('self.\_heuristic == None') or to assign an appropriate heuristic for and A-Star search, and setting 'self.\_is_goal' lets you discriminate goals that are points, tiles, or a tile in an iterable of tiles.
- The pathfinder internally has 'self.\_closed_set_parent_map' and 'self.\_closed_set_coords', and 'self.\_open_set' and 'self.\_open_set_coords'. This is Python specific. Testing for inclusion in a set in Python is fast necessitating the use of 'self.\_closed_set_coords' and 'self.\_open_set_coords'. Meanwhile Python's heapq wants hashable objects to sort necessitating 'self.\_open_set'.
- 'self.\_closed_set_parent_map' is a list of lists that is set to 'None' unless a tile has a parent that leads to the origin of a search, then it has an (x, y) Tuple of the parent's coordinates. This is basically a Dijkstra map, and is used to help efficiently retrace the path.
- The ordering of the sub-lists in 'self.\_open_set' should break ties in a desirable manner for A-Star searches.
- Setting 'self.\_print_path_info = True' will print info about a found path from '\_retrace_path' to the console.
- If you want to implement variable movement costs is should be trivial if you modify '\_look_for_open' at about Line 240. A 1.0 based movement system, where 1.0 is the the fastest possible movement for any given tile, and where slower terrains have a higher modifier (1.1, 1.3, etc.), should be relatively straightforward to implement.
- if '.\_unobstruct_goals == True' then a goal (point, tile, iterable of tiles) will be found even if it is an obstruction. This is set to 'False' for 'find_point' and 'is_point_findable' and 'True' for 'find_tile' and 'nearest' under the assumption that if you're specifically looking for an obstruction you probably want to find it.

## Example Implementation Notes

- Since the example doesn't bundle a font it looks for: "dejavusansmono", "liberationmono", "andalemono", "lucidamono", "notomono", and finally the first font with 'mono' in the name. If the display is hideous a list of fonts with mono in the name is printed to the console. Add a reasonable choice from that list to 'font_names' (Line 691).
- The example times 5 functions by default, and colours the paths / found tiles. '|R Path' is the red path, '|G Path' the green path, '|B Path' is the blue path, and '|F Path' is the fuchsia (which by default is looking for the nearest 'open secret door' and not a path). The relevant code starts on Line 788 and should be easy to modify.

## Image of the Pygame Example

![Pygame Example](./assets/images/articles/a_python_3_and_2_pathfinder_with_pygame_example/Pathfinding_Test.png)

## Thoughts on Performance

There is 1 unavoidable fact about this pathfinder: it's written in Python and not C. While it aims to be efficient Python, it will be considerably slower than any reasonable C implementation. With that being said, I don't consider it too slow for a traditional roguelike or most other turn based games, and it does bring to the table all of the advantages Python has to offer.

There are steps developers can take to mitigate any excessive slowness in this implementation. In rough order of preference (with 1-6 being reasonable, and 7-10 being more heavy handed, IMHO):

1. Avoid recalculating paths. Both 'find_point' and 'find_tile' return a deque of (x, y) Tuples. Don't recalculate every step, just validate the next step (or maybe a few steps ahead for smarter creatures). Python's deque has a fast '.popleft()' that should come in handy for path consumers.
1. Do you need to use diagonal movement? If not you'll get a > 40% speedup with 'use_diagonals=False'. Do you need the best path? If not you get a < 10% speedup with 'best_path=False'. If 'best_path == False' paths will tend to veer slightly toward the goal. This may actually be desirable for more organic looking paths.
1. Do you still need diagonal movement and the best path when both the player and a creature can't see each other?
1. Can more of your creatures be at rest until the player comes into view?
1. Can you limit searches to a certain "as the crow flies" distance?
1. It would be easy to make a function that produces Dijkstra maps of a given area to ANY given point. You could use these Dijkstra maps as pre-calculated paths fixed to waypoints.
1. Can you make maps that are less twisty, maze like, and / or with fewer culs-de-sac?
1. 'abort=' can be set to stop a long search based on the size of the '.\_closed_set_coords'.
1. Even without multiprocessing it would be relatively easy to write '\_find_path_bipolar' and a 'find_point_bipolar' that searches from the start to the goal and the goal to the start. Such a search would terminate when the goal is found, or when they share an entry in the closed set. Given how an A-Star frontier expands, even without multiprocessing, it should help reduce then number of bad paths explored in maps that are twisty, maze like, and / or have culs-de-sac.
1. You could leverage Python's 'multiprocessing' module and have one or more processes dedicated to calculating paths while you're game's main process continues on with other work until paths are calculated. Use this to pre-calculate paths where possible.

## Pastebin Link to the Code

Note: The Pastebin link makes it easy to download the code, and has rather better code highlighting.

<http://pastebin.com/jHeKc6j0>

## The Full Code Follows Below

```python
#! /usr/bin/env python3
# coding: utf-8

# pathfinder.py, a python pathfinder and demo by
# James Spencer <jamessp [at] gmail.com>.

# To the extent possible under law, the person who associated CC0 with
# pathfinder.py has waived all copyright and related or neighboring rights
# to pathfinder.py.

# You should have received a copy of the CC0 legalcode along with this
# work. If not, see <http://creativecommons.org/publicdomain/zero/1.0/>.

from __future__ import print_function
from __future__ import division
from __future__ import unicode_literals
from collections import deque
import heapq
from sys import version_info


if version_info[0] < 3:
        range = xrange


class Config(object):
    '''This class is a minimal subset of <config.py> from my project, and much
    of the data herein was parsed from config files, hence the irregular
    naming. Feel free top plop the dicts into a <config.py> of your own and
    import it.
    '''

    def __init__(self):
        self.TERRAIN_CHARACTERS = {'open secret door'   : '~',
                                   'closed secret door' : '§',
                                   'flagstone'          : '.',
                                   'stone brick'        : '#',
                                   'closed door'        : '+',
                                   'open door'          : '-',
                                   'solid stone'        : '&'}
        self.TERRAIN_COLORS = {'closed door'        : 'aqua',
                               'flagstone'          : 'silver',
                               'open secret door'   : 'aqua',
                               'open door'          : 'aqua',
                               'solid stone'        : 'black',
                               'stone brick'        : 'white',
                               'closed secret door' : 'aqua'}
        # NOTE: This could easily be a dict where the keys are the obstructions
        # and the values are the tile characters, pygame surfaces, etc.
        self.OBSTRUCTION_CHARACTERS = {'closed secret door', 'stone brick',
                                       'closed door', 'solid stone'}
        # 16 of the DB32 colors, as they are easier on the eyes than VGA16.
        self.COLORNAMES = {'white':   (255, 255, 255),
                           'yellow':  (251, 242, 54),
                           'fuchsia': (215, 123, 186),
                           'red':     (172, 50, 50),
                           'silver':  (155, 173, 183),
                           'gray':    (105, 106, 106),
                           'olive':   (143, 151, 74),
                           'purple':  (118, 66, 138),
                           'maroon':  (102, 57, 49),
                           'aqua':    (96, 205, 228),
                           'lime':    (153, 229, 80),
                           'teal':    (48, 96, 130),
                           'green':   (75, 105, 47),
                           'blue':    (91, 110, 225),
                           'navy':    (63, 63, 116),
                           'black':   (0, 0, 0)}


# To 'fake' my projects <config.py>
config = Config()


class Area(object):
        '''The relevant to pathfinding bits of my project's Area() class. See
        the example at the end to see how this is used.
        '''

        def __init__(self):
            self.terrain = None
            self.width = None
            self.height = None


class Pathfinder(object):
    '''Find a path form x1, y1 to a point or tile(s).

    area -- An instance of the Area() class. See Area() at the top, and the
    pygame example at the end of the file for a minimal implementation.
    c_dist -- Integer or Float, the distance of a step in a cardinal
    direction.
    d_dist -- Integer or Float, the distance of a step in a diagonal
    direction.
    obstruction_characters -- An iterable of characters that obstruct movement.
    '''

    def __init__(self, area):
        self.area = area    # An instance of the Area() class.
        self.c_dist = 70    # Could be 70, 1.0,                10, 100, 1000.
        self.d_dist = 99    # Could be 99, 1.4142135623730951, 14, 141, 1414.
        self.obstruction_characters = config.OBSTRUCTION_CHARACTERS
        self._unobstruct_goals = None    # Find a goal that is an obstruction.
        self._cardinals = [( 0, -1, self.c_dist), ( 1,  0, self.c_dist),
                           ( 0,  1, self.c_dist), (-1,  0, self.c_dist)]
        self._diagonals = [(-1, -1, self.d_dist), ( 1, -1, self.d_dist),
                           ( 1,  1, self.d_dist), (-1,  1, self.d_dist)]
        self._directions = None          # Cardinals, or cardinals + diagonals.
        self._heuristic = None           # The A-Star heuristic
        self._x2, self._y2 = None, None  # Used if the goal is a point.
        self._tile, self._tiles = None, None         # goal is a tile, tiles.
        self._closed_set_coords = set()  # Just the coords to speed up checks.
        # List of lists of parent coordinates to help retrace the path.
        # NOTE: This is a literal Dijkstra map. See ._purge_private() for info.
        self.__parent_map_row = [None] * self.area.width
        self._closed_set_parent_map = []
        self._open_set = []             # Tiles to be evaluated.
        self._open_set_coords = set()   # Just the coords to speed up checks.
        self._is_goal = None            # Is this tile the goal?
        self._print_path_info = False   # Print info from retrace path.

    def _is_goal_point(self, current_tile):
        '''Is this the goal point?

        current_tile -- List in [current + estimated distance, estimated
        distance, distance so far, (current x, current y), (parent x,
        parent y)] format.

        Return: Boolean. (True if the goal is found.)
        '''

        return current_tile[3] == (self._x2, self._y2)

    def _is_goal_tile(self, current_tile):
        '''Is this the goal tile?

        current_tile -- List in [current + estimated distance, estimated
        distance, distance so far, (current x, current y), (parent x,
        parent y)] format.


        Return: Boolean. (True if the goal is found.)
        '''

        cur_x1, cur_y1 = current_tile[3]

        return self.area.terrain[cur_y1][cur_x1] == self._tile

    def _is_goal_iterable(self, current_tile):
        '''Is this the goal as found in the iterable?

        current_tile -- List in [current + estimated distance, estimated
        distance, distance so far, (current x, current y), (parent x,
        parent y)] format.


        Return: Boolean. (True if the goal is found.)
        '''

        cur_x1, cur_y1 = current_tile[3]

        return self.area.terrain[cur_y1][cur_x1] in self._tiles

    def _cardinal_heuristic(self, x1, y1, x2, y2):
        '''Return the Manhattan distance.

        x1, y1, x2, y2 -- Integers. Start and end coordinates.

        Return: Integer or Float. (The distance estimate.)
        '''

        d_x, d_y = abs(x1 - x2), abs(y1 - y2)

        return (d_x + d_y) * self.c_dist

    def _diagonal_heuristic(self, x1, y1, x2, y2):
        '''Return a distance estimate for grids that allow diagonal movement.

        This is the 'octile heuristic' as defined on:
        https://github.com/riscy/a_star_on_grids#on-an-8-connected-grid

        x1, y1, x2, y2 -- Integers. Start and end coordinates.

        1: /r/rogulikedev's RIngan suggested:
        Chebyshev distance:
        max(d_x, d_y) * self.c_dist

        While simple, fast, and producing an admissible heuristic, the more
        diagonal the path the more an estimate would be low.

        2: /r/rogulikedev's chrisrayner suggested:
        https://github.com/riscy/a_star_on_grids#on-an-8-connected-grid

        C, D = self.c_dist, self.d_dist
        E = 2 * C - D
        (E * abs(d_x - d_y) + D * (d_x + d_y)) / 2

        or

        B = self.d_dist - self.c_dist
        C * d_x + B * d_y if d_x > d_y else
        C * d_y + B * d_x

        for a more accurate estimate. I settled on the latter.

        Return: Integer or Float. (The distance estimate.)
        '''

        d_x, d_y = abs(x1 - x2), abs(y1 - y2)

        # NOTE: Doing this with a max() and min() was slower on Python 3
        if d_x > d_y:
            return self.c_dist * d_x + (self.d_dist - self.c_dist) * d_y
        else:
            return self.c_dist * d_y + (self.d_dist - self.c_dist) * d_x

    def _purge_private(self):
        '''Purge Pathfinder()'s private values, usually before finding a new
        path.

        NOTE: self._heuristic = None preforms a Dijkstra search, set it to a
        heuristic to use an A-Star search.
        NOTE 2: self._closed_set_parent_map is a List of Lists initialized to
        None. Tiles in ._closed_set_coords put their parent coordinates at
        self._closed_set_parent_map[y][x] in (parent x, parent y) format.
        '''

        self._x2, self._y2 = None, None
        self._tile, self._tiles = None, None
        self._heuristic = None
        self._directions = None
        self._open_set_coords = set()
        self._open_set = []
        self._closed_set_coords = set()
        self._closed_set_parent_map = [self.__parent_map_row[:] for row in
                                       range(self.area.height)]
        self._is_goal = None
        self._unobstruct_goals = None

    def _look_for_open(self, current_tile, best_path):
        '''Add the eligible neighbours to open_set adjusting other tiles as
        needed.

        current_tile -- List in [current + estimated distance, estimated
        distance, distance so far, (current x, current y), (parent x,
        parent y)] format.
        best_path -- Boolean. 'True' to look for the best path. This is slower
        as it involves modifying already processed tiles and possibly breaking
        the heap invariant.
        '''

        x, y = current_tile[3]
        current_dist = current_tile[2]

        for direction in self._directions:
            # NOTE: Implementing a '1' based movement cost system should be
            # trivial in the following code.
            x_mod, y_mod, step_dist = direction
            new_x, new_y = x+x_mod, y+y_mod

            # If it's not in bounds...
            if 0 > new_x == self.area.width or 0 > new_y == self.area.height:

                continue
            else:
                the_tile = self.area.terrain[new_y][new_x]

            # Or if it is in the closed_set...
            if (new_x, new_y) in self._closed_set_coords:

                continue

            # If not unobstructing goals and it hits an obstruction...
            elif not self._unobstruct_goals and the_tile in\
                self.obstruction_characters:

                continue

            # When looking for a goal find it even if it's an obstruction when
            # unobstructing goals...
            elif self._unobstruct_goals and the_tile in\
                self.obstruction_characters:

                if self._x2 and self._y2 and (
                     new_x, new_y) != (self._x2, self._y2):

                    continue
                elif self._tile and self.area.terrain[
                     new_y][new_x] != self._tile:

                    continue
                elif self._tiles and self.area.terrain[
                     new_y][new_x] not in self._tiles:

                    continue

            # Update the distance travelled
            dist = current_dist + step_dist
            # Generate a heuristic distance for a goal that's a point.
            # NOTE: if self._heuristic == None then do a Dijkstra search
            # where the heuristic distance is just the distance traveled so
            # far, and the distance estimate is None.
            heuristic_estimate = None
            heuristic_distance = dist
            if self._heuristic:
                heuristic_estimate = self._heuristic(new_x, new_y,
                                                     self._x2, self._y2)
                heuristic_distance += heuristic_estimate

            # Not in the open_set so add it.
            if (new_x, new_y) not in self._open_set_coords:
                self._open_set_coords.add((new_x, new_y))
                heapq.heappush(self._open_set,
                               [heuristic_distance,     # Heuristic distance
                                heuristic_estimate,     # Estimated distance
                                dist,                   # Distance traveled
                                (new_x, new_y),         # (x, y)
                                (x, y)])                # (parent_x, parent_y)

                continue
            # In the open_set and we want the best path.
            elif best_path and (new_x, new_y) in self._open_set_coords:
                for tile in self._open_set:
                    if (new_x, new_y) == tile[3]:
                        # In the open_set and better.
                        if tile[2] > dist:
                            tile[0] = heuristic_distance
                            tile[1] = heuristic_estimate
                            tile[2] = dist
                            tile[4] = (x, y)
                            # Turns out naively re-heapifying is faster.
                            heapq.heapify(self._open_set)

                        break

    def _retrace_path(self, current_tile):
        '''Retrace a path to the start.

        current_tile -- List in [current + estimated distance, estimated
        distance, distance so far, (current x, current y), (parent x,
        parent y)] format.

        Retrace a path to (x1, y1). A path includes the (x, y) of the goal /
        target, but not that of the starting tile.

        NOTE: This will retrace the path of any tile back to the starting
        point, and may be useful for a number of purposes like building
        Dijkstra maps for multiple consumers.

        NOTE 2: Given python's recursion limit making this recursive is an iffy
        proposition.

        Return: deque(). (A deque of (x, y) Tuples representing the path.)
        '''

        parent = current_tile[4]
        the_path = deque()
        if parent:
            # Add the endpoint.
            the_path.appendleft(current_tile[3])

            while parent:
                # Add the parent until we get to to the starting x, y
                x, y = parent
                if self._closed_set_parent_map[y][x]:
                    the_path.appendleft(parent)
                parent = self._closed_set_parent_map[y][x]

        if self._print_path_info:
            print("\n\n==========")
            print("\nCurrent:")
            print(current_tile)
            # print("\nOpen Set Coordinates:")
            # print(self._open_set_coords)
            print("\nOpen Set Length:")
            print(len(self._open_set_coords))
            # print("\nClosed Set Coordinates:")
            # print(self._closed_set_coords)
            print("\nClosed Set Length:")
            print(len(self._closed_set_coords))
            print("\nPath:")
            print(the_path)
            print("\nTile Steps:")
            print(len(the_path))

        return the_path

    def _find_path(self, best_path, abort, goal_only):
        '''Find a path.

        best_path -- Boolean. 'True' to look for the best path. This is slower
        as it involves modifying already processed tiles and possibly breaking
        the heap invariant.
        abort -- False, or Integer. If the len(self._closed_set_coords) > abort
        stop searching. This should stop any 'too slow' away searches.
        goal_only -- Boolean. If True it will only return the (x, y, tile name)
        of the goal, and not the path. Faster than retracing the path.

        Return: deque, list, or None. (A deque of (x, y) Tuples, or a Tuple of
        (x, y, tile name) if goal only == true, or None if no path is found.)
        '''

        while self._open_set:
            current_tile = heapq.heappop(self._open_set)
            self._open_set_coords.remove(current_tile[3])
            x, y = current_tile[3]

            # Yay, we found the goal!
            if self._is_goal(current_tile) and not goal_only:
                return self._retrace_path(current_tile)
            elif self._is_goal(current_tile) and goal_only:
                return (x, y, self.area.terrain[y][x])

            # No goal, let's update the self._closed_set* and look for more
            # tiles...
            self._closed_set_coords.add(current_tile[3])
            # Abort search. Remember False == 0.
            if len(self._closed_set_coords) > abort > 0:
                return None
            self._closed_set_parent_map[y][x] = current_tile[4]
            self._look_for_open(current_tile, best_path)

        # Ooops, couldn't find a path!
        return None

    def find_point(self, x1, y1, x2, y2, use_diagonals=True, best_path=True,
                   abort=False):
        '''Look for a specified point.

        x1, y1, x2, y2 -- Integers. The start and end point.
        use_diagonals -- Boolean. Path including diagonal directions. This is
        slower as it has to check twice the tiles.
        best_path -- Boolean. 'True' to look for the best path. This is slower
        as it involves modifying already processed tiles and possibly breaking
        the heap invariant. If set to 'False' paths are often somewhat more
        organic, and can somewhat approximate a 'greedy best first' search.
        abort -- False, or Integer. If the 'len(self._closed_set_coords) >
        abort' stop searching. This should stop any 'too slow' searches.

        NOTE: This performs an A-Star search as it sets self._heuristic.

        Return: deque or None. (A deque of (x, y) Tuples, or None if no path
        is found.)
        '''

        self._purge_private()
        self._x2 = x2
        self._y2 = y2
        self._unobstruct_goals = False

        if use_diagonals:
            self._heuristic = self._diagonal_heuristic
            self._directions = set(self._cardinals + self._diagonals)
        else:
            self._heuristic = self._cardinal_heuristic
            self._directions = set(self._cardinals)

        self._is_goal = self._is_goal_point
        self._open_set_coords.add((x1, y1))
        heuristic_estimate = self._heuristic(x1, y1, x2, y2)

        heapq.heappush(self._open_set,
                       [0 + heuristic_estimate,         # A-Star
                        heuristic_estimate,             # Distance estimate
                        0,                              # Distance traveled
                        (x1, y1),                       # (x, y)
                        None])                          # (parent_x, parent_y)

        return self._find_path(best_path, abort, False)

    def is_point_findable(self, x1, y1, x2, y2, use_diagonals=True,
                          abort=False):
        '''Can the pathfider find a given point?

        NOTE: DO NOT USE THIS TO DETERMINE IF YOU SHOULD USE .find_point(), as
        you will be doing a search to do a search. In that case just use
        .find_point(). If you merely need to see if a tile is open please check
        the Area().terrain data structure. If you need LOS cast a ray, or use
        your FOV implementation. This is primarily useful for a 'blink' /
        'teleport' that requires a valid path, but may not be directly seen.

        x1, y1, x2, y2 -- Integers. The start and end point.
        use_diagonals -- Boolean. Path including diagonal directions. This is
        slower as it has to check twice the tiles.
        abort -- False, or Integer. If the 'len(self._closed_set_coords) >
        abort' stop searching. This should stop any 'too slow' searches.

        NOTE 2: This performs an A-Star search as it sets self._heuristic.

        Return: Boolean. (True if the point is findable)
        '''

        self._purge_private()
        self._x2 = x2
        self._y2 = y2
        self._unobstruct_goals = False

        if use_diagonals:
            self._heuristic = self._diagonal_heuristic
            self._directions = set(self._cardinals + self._diagonals)
        else:
            self._heuristic = self._cardinal_heuristic
            self._directions = set(self._cardinals)

        self._is_goal = self._is_goal_point
        self._open_set_coords.add((x1, y1))
        heuristic_estimate = self._heuristic(x1, y1, x2, y2)

        heapq.heappush(self._open_set,
                       [0 + heuristic_estimate,         # A-Star
                        heuristic_estimate,             # Distance estimate
                        0,                              # Distance traveled
                        (x1, y1),                       # (x, y)
                        None])                          # (parent_x, parent_y)

        found = self._find_path(False, abort, True)
        if found:
            return True
        else:
            return False

    def find_tile(self, x1, y1, tile, use_diagonals=True, best_path=True,
                  abort=False):
        '''Look for a specified tile, or tile in an iterable of tiles.

        x1, y1 -- Integers. The start point.
        tile -- String or Iterable. The tile, or an iterable of tiles, being
        sought.
        use_diagonals -- Boolean. Path including diagonal directions. This is
        slower as it has to check twice the tiles.
        best_path -- Boolean. 'True' to look for the best path. This is slower
        as it involves modifying already processed tiles and possibly breaking
        the heap invariant. If set to 'False' paths are often somewhat more
        organic, and can somewhat approximate a 'greedy best first' search.
        abort -- False, or Integer. If the 'len(self._closed_set_coords) >
        abort' stop searching. This should stop any 'too slow' searches.

        NOTE: This performs an Dijkstra search as it doesn't set
        self._heuristic.

        Return: deque or None. (A deque of (x, y) Tuples, or None if no path
        is found.)
        '''

        self._purge_private()

        if type(tile) == str:
            self._tile = tile
            self._is_goal = self._is_goal_tile
        else:
            self._tiles = tile
            self._is_goal = self._is_goal_iterable

        self._unobstruct_goals = True

        if use_diagonals:
            self._directions = set(self._cardinals + self._diagonals)
        else:
            self._directions = set(self._cardinals)

        self._open_set_coords.add((x1, y1))

        heapq.heappush(self._open_set,
                       [0,                              # Dijkstra
                        None,                           # Distance estimate
                        0,                              # Distance traveled
                        (x1, y1),                       # (x, y)
                        None])                          # (parent_x, parent_y)

        return self._find_path(best_path, abort, False)

    def nearest(self, x1, y1, tile, use_diagonals=True, abort=False):
        '''Look for a specified tile, or tile in an iterable of tiles, and
        return the location and name of that tile.

        x1, y1 -- Integers. The start point.
        tile -- String or Iterable. The tile, or an iterable of tiles, being
        sought.
        use_diagonals -- Boolean. Path including diagonal directions. This is
        slower as it has to check twice the tiles.
        abort -- False, or Integer. If the len(self._closed_set_coords) >
        abort stop searching. This should stop any 'too slow' away searches.

        NOTE: This performs an Dijkstra search as it doesn't set
        self._heuristic.

        Return: Tuple or None. (A Tuple of (x, y, tile name), or None.)
        '''

        self._purge_private()

        if type(tile) == str:
            self._tile = tile
            self._is_goal = self._is_goal_tile
        else:
            self._tiles = tile
            self._is_goal = self._is_goal_iterable

        self._unobstruct_goals = True

        if use_diagonals:
            self._directions = set(self._cardinals + self._diagonals)
        else:
            self._directions = set(self._cardinals)

        self._open_set_coords.add((x1, y1))

        heapq.heappush(self._open_set,
                       [0,                              # Dijkstra
                        None,                           # Distance estimate
                        0,                              # Distance traveled
                        (x1, y1),                       # (x, y)
                        None])                          # (parent_x, parent_y)

        return self._find_path(False, abort, True)


if __name__ == '__main__':
    '''Test the Pathfinder Class.'''

    dun = ["#########################################################&#######",
           "#.......#...#.........#...........#...............#.....#&#.-...#",
           "#######.~...#........#..........#...................#...#&#.#...#",
           "&&&&&&#.#...........#...........#~###################...###.#####",
           "#######.#...#####.##............#...................#.....§.....#",
           "#.......#...#&&#....##..........#...................#.....#####-#",
           "#####+###...####....##.......#####################..#.....#.....#",
           "#..##........................#&&#................#..#.....#.....#",
           "#...##...........#########...####.............#..#..#.....#.....#",
           "#....##..................#......#..###############..#.....#.....#",
           "#.....##.................#.#....#..#...#...#...#....#.....#.....#",
           "#......##.......#######..#.#....#....#...#...#...#..#.....#.##..#",
           "#...............#&&&&&#..#.#....#####################.....#.##..#",
           "#........#####..#&&&&&#..#.#.............#................##.#..#",
           "#........#&&&#..#######....#.............#................#.##..#",
           "#........#####.............#####.....#...#...#....###...####.#..#",
           "#..............##########.............#..#..#.....#&#...#&#.##..#",
           "#..............#........#..............#...#......###...####.#..#",
           "#.##...###..####........#..#####........#.#...............#..#..#",
           "#..............-........§..#........############..........####..#",
           "####.#.........#........#..#........#....#.....#.########.......#",
           "#.....#........##########..#.............#.......#&&#..###-####+#",
           "#.....##...................#.............#.......#####.#&#......#",
           "#.......#..................#.............#.......~.....#&#......#",
           "########################################################&########"]

    import pygame
    from pygame.locals import *
    import time
    from fnmatch import filter
    from sys import exit

    # Translate the character based map into Area().terrain tile names.
    # Tile names are used to avoid character clashes in more complex maps.
    rev = {}
    tmp_terrain = []

    for tile in config.TERRAIN_CHARACTERS:
        tmp = config.TERRAIN_CHARACTERS[tile]
        rev[tmp] = tile

    for y in range(len(dun)):
        tmp_terrain.append([])
        for x in range(len(dun[y])):
            tmp_terrain[y].append(rev[dun[y][x]])

    test_area = Area()
    test_area.terrain = tmp_terrain
    test_area.width = len(tmp_terrain[0])
    test_area.height = len(tmp_terrain)

    # An instance of the pathfinder.
    pathfinder = Pathfinder(test_area)

    # Initialize Pygame.
    pygame.display.init()
    pygame.font.init()

    clock = pygame.time.Clock()

    pygame.display.set_caption("Pathfinding Test")

    chosen_font = None
    installed_fonts = pygame.font.get_fonts()

    # Pick the first font from font_names, or the first font with *mono* in
    # the name.
    print("\nFONTS WITH MONO IN THE NAME:\n"
          "============================")
    print(filter(installed_fonts, "*mono*"))
    first_mono = filter(installed_fonts, "*mono*")[0]

    font_names = ["dejavusansmono",
                  "liberationmono",
                  "andalemono",
                  "lucidamono",
                  "notomono",
                  first_mono]

    chosen_font = pygame.font.match_font(
        [font_name for font_name in font_names if font_name in installed_fonts]
        [0])

    print("\nFONT:\n"
          "=====")
    print("Using font: " + chosen_font + '\n')

    font_size = 20
    font = pygame.font.Font(chosen_font, font_size)
    font_w, font_h = font.size(" ")

    font_size2 = 14
    font2 = pygame.font.Font(chosen_font, font_size2)

    # The goal and player x and y.
    gx, gy = 8, 8
    px, py = 10, 10

    default_fps = 60

    R_color = 'red'
    G_color = 'lime'
    B_color = 'blue'
    F_color = 'fuchsia'

    pygame.key.set_repeat(250, 1000 // default_fps)

    win = pygame.display.set_mode((test_area.width * font_w,
                                   (test_area.height + 1) * font_h))

    win.fill(config.COLORNAMES['black'])
    txt1 = font.render("WSAD to move '$', and UP, DOWN, LEFT, " +
                       "RIGHT, to move '@'.", True,
                       config.COLORNAMES['white'])
    txt2 = font.render("Press an any key to begin...", True,
                       config.COLORNAMES['white'])
    win.blit(txt1, (0, font_h * 5))
    win.blit(txt2, (0, font_h * 7))

    pygame.display.flip()

    pad_h = test_area.height - 3
    pad_w = test_area.width - 3

    wait = True
    while wait:
        for event in pygame.event.get():
            clock.tick(default_fps)
            if event.type == KEYDOWN:
                    wait = False

    win.fill(config.COLORNAMES['black'])

    # The main loop.
    while True:
        # Set the FPS
        clock.tick(default_fps)

        for event in pygame.event.get():
            if (event.type == QUIT or event.type == KEYDOWN and
                    event.key == K_ESCAPE):
                pygame.quit()
                exit()

            if event.type == KEYDOWN:
                if event.key == K_UP:
                    if py > 1:
                        py -= 1
                elif event.key == K_DOWN:
                    if py <= pad_h:
                        py += 1
                elif event.key == K_LEFT:
                    if px > 1:
                        px -= 1
                elif event.key == K_RIGHT:
                    if px <= pad_w:
                        px += 1
                elif event.unicode == 'w':
                    if gy > 1:
                        gy -= 1
                elif event.unicode == 's':
                    if gy <= pad_h:
                        gy += 1
                elif event.unicode == 'a':
                    if gx > 1:
                        gx -= 1
                elif event.unicode == 'd':
                    if gx <= pad_w:
                        gx += 1

                # Calculate and (crudely) time the paths.
                t1 = time.time()
                # Find the Goal '$'
                r1 = pathfinder.is_point_findable(px, py, gx, gy,
                                                  use_diagonals=True,
                                                  abort=False)
                t2 = time.time()
                # Red Path
                r2 = pathfinder.find_point(px, py, gx, gy,
                                           best_path=True,
                                           use_diagonals=True,
                                           abort=False)
                t3 = time.time()
                # Green Path
                r3 = pathfinder.find_tile(px, py, 'open door',
                                          best_path=True,
                                          use_diagonals=True,
                                          abort=False)
                t4 = time.time()
                # Blue Path
                r4 = pathfinder.find_tile(px, py, ['closed door',
                                                   'closed secret door'],
                                          best_path=True,
                                          use_diagonals=True,
                                          abort=False)
                t5 = time.time()
                # Fuchsia Path
                r5 = pathfinder.nearest(px, py, 'open secret door',
                                        use_diagonals=True,
                                        abort=False)
                t6 = time.time()

                win.fill(config.COLORNAMES['black'])

                # Display the area on the given window.
                for x1 in range(test_area.width):
                    for y1 in range(test_area.height):

                        char = None

                        if r2 and (x1, y1) in r2:
                            color = R_color
                        elif r3 and (x1, y1) in r3:
                            color = G_color
                        elif r4 and (x1, y1) in r4:
                            color = B_color
                        elif r5 and (x1, y1) ==\
                            (r5[0], r5[1]):
                            color = F_color
                        else:
                            color = config.TERRAIN_COLORS[
                                        test_area.terrain[y1][x1]]

                        char = config.TERRAIN_CHARACTERS[
                            test_area.terrain[y1][x1]]

                        if x1 == px and y1 == py:
                            color = 'yellow'
                            char = '@'

                        elif x1 == gx and y1 == gy:
                            color = 'teal'
                            char = '$'

                        if char:
                            char_surf = font.render(char, True,
                                                    config.COLORNAMES[color])
                            win.blit(char_surf, (x1 * font_w, y1 * font_h))

                goal_found = 'False'
                if r1:
                    goal_found = str(round(t2 - t1, 4))

                txt = ('|R Path in: ' +
                       str(round(t3 - t2, 4)) +
                       ' |G Path in: ' +
                       str(round(t4 - t3, 4)) +
                       ' |B Path in: ' +
                       str(round(t5 - t4, 4)) +
                       ' |F Path in: ' +
                       str(round(t6 - t5, 4)) +
                       ' |$ Found in: ' +
                       goal_found + ' |')

                txt3 = font2.render(txt, True, config.COLORNAMES['white'])
                win.blit(txt3, (0, font_h * test_area.height))

                pygame.display.flip()
```

Even if your language of choice isn't Python, I hope you find this pathfinder helpful to your endeavours. Cheers!
