# Template Dungeon themeing/generation

---

Inspired by the Brogue cellophane idea/stitching template idea.  

## Basic idea and explanation

---

The basic idea is that you will be given a sample/entire room such as  

```text
#######
#.....#
#.....*
#.....#
#.....#
#....>#
#######
```

The room is 7x7 there are various symbols in the room.  

From here we would slice too room into various chunks of YxY (could do different dimensions but why not is discussed later) that exhaust all possible placement of the YxY square  

So this 7x7 room sliced into 5x5 (for simplicity sake in showing this by hand) items would be.  

1  

```text
#####
#....
#....
#....
#....
```

2

```text
#####
.....
.....
.....
.....
```

3

```text
#####
....#
....*
....#
....#
```

4

```text
#....
#....
#....
#....
#...>
```

5

```text
.....
.....
.....
.....
....>
```

6

```text
....#
....#
....#
....#
...>#
```

7

```text
#....
#....
#....
#....
#####
```

8

```text
.....
.....
.....
....>
#####
```

9

```text
....#
....*
....#
...>#
#####
```

Store a tokenized version of the slices as a key in a hash pointing to a list of top left (or your corner of choice) points as multiple slices may be the same token (3x3 slices of the given room will produce a lot of ......... tokens).  

Now reference templates  

A reference template is an update to a room based on existing geometry. IE a 5x5 template to create a "pillar" in the corner room would be as follows.  

reference  

```text
??#??
?...?
#...?
?...?
?????
```

result  

```text
??#??
?...?
#.#.?
?...?
?????
```

The ? marks do not care what the tile in that relative location is but other items must match. The result will blit in all items except for ? marks. to best fill the items this object will be rotated 3 times so that all cases will be caught without having to make 4 variations on this design.  

Create a token of all references and using regex or your choice of string comparing get a list of the tokens created by the room as per one of the points in the list.  

From here you can randomly choose an item to apply the result to or possibly apply the result to all items in the room (which might be silly but work for your purpose). In this example the following would be selected from the room.  

1  

```text
#####
#....
#....
#....
#....
```

7  

```text
#....
#....
#....
#....
#####
```

So the room could end with the result of  

```text
#######
#.....#
#.#...*
#.....#
#.....#
#....>#
#######
```

or  

```text
#######
#.....#
#.....*
#.....#
#.#...#
#....>#
#######
```

if we only pick one  

If all are done (without caring about overlap)  

```text
#######
#.....#
#.#...*
#.....#
#.#...#
#....>#
#######
```

This idea could be used for putting in items, monsters or other such  

So what about odd sized rooms?  

a 2*3 reference would look like this  

```text
?##
?>#
?.#
```

Due to how things are read it doesn't matter what the ??? is. This said it will not read past the edge of the room to allow the template to fit.  

it requires a bit more work with odder items such as 2*4 but this could be automated.  

```text
??##
??>#
??.#
??.#
```

```text
?##?
?>#?
?.#?
?.#?
```

```text
?##?
?#>?
?#.?
?#.?
```

```text
##??
>#??
.#??
.#??
```

A bit of mirroring could save time, just check to see if the tokenized version exists.  

A bonus is that entire dungeons can be made if a background "Unfilled" symbol is used.  

Start with grid of unfilled  

```text
XXXXXXXXX
XXXXXXXXX
XXXXXXXXX
XXXXXXXXX
XXXXXXXXX
XXXXXXXXX
XXXXXXXXX
XXXXXXXXX
XXXXXXXXX
XXXXXXXXX
```

create a starting template and replace string to represent starting room.  

```text
XXXXX
XXXXX
XXXXX
XXXXX
XXXXX
```

```text
?????
?#+#?
?+.+?
?#+#?
?????
```

blit it to a proper location (realistically anywhere on the grid will happen)  

```text
XXXXXXXXXX
XXXXXXXXXX
XXXXX#+#Xx
XXXXX+.+XX
XXXXX#+#XX
XXXXXXXXXX
XXXXXXXXXX
XXXXXXXXXX
XXXXXXXXXX
XXXXXXXXXX
XXXXXXXXXX
```

Create new rooms with a template searching for doors (or empty floor squares to create expansive rooms)  

Note the template cares about a door surrounded by walls, followed by a chunk of 6 "unfilled" tiles. The unfilled tiles are the footprint of the room that will be blitted in. The only real thing that the template should care about is the door and/or wall that it is expanding upon and the room footprint that it is filling in. This allows it to snugly fit against other rooms or expand out into the unfilled area.  

```text
?#+#?
?XXX?
?XXX?
?????
?????
```

```text
?#+#?
?+.+?
?#+#?
?????
?????
```

Let it run for how many rooms you want or until you can't place anything more  

run for 4 new rooms.  

```text
XXXXXXXXXX
XXXXXXXXXX
XXX#+#+#XX
XXX+.+.+XX
XXX#+#+#XX
XXX+.+.+XX
XXX#+#+#XX
XXXXX+.+XX
XXXXX#+#XX
XXXXXXXXXX
XXXXXXXXXX
```

Clean up doorways to nothingness with this template  

```text
?X?
?+?
???
```

```text
???
?#?
???
```

Results in this fully connected super tiny dungeon.  

```text
XXXXXXXXX
XXXXXXXXX
XXX#####X
XXX#.+.#X
XXX#+#+#X
XXX#.+.#X
XXX###+#X
XXXXX#.#X
XXXXX###X
XXXXXXXXX
```

## 2.7 python code methods

---

2.7 Totally Unoptimized python code  

```py
def match_string(a,b):
    """
    a,b two strings

    a is the test string where there will be characters that allow skiping
    or charcter sets such as any floor/any wall tile (not implimented)

    b is the tokenized slice of an area
    """
    for i,j in zip(a,b):
        #? is our current wildcard where we don't care and move on
        if i != "?" and i!=j:
            #we encountered a character that does not obey the template so
            #returning false
            return False
    return True

def tokenize_grid_slice(grid,x,y,width,height,slice_width,slice_height):
    """
    grid, 1d List with ascii characters per cell

    x y, int locaiton as if grid was a 2 d grid

    width height, width and height of the grid, currently height is superfluous

    slice_width slice_height, the current chunk to tokenize

    """
    out = []
    for ay in xrange(slice_height):
        for ax in xrange(slice_width):
            #translate an x y into an idex for a 1d list
            at = (x+ax)+(y+ay)*width
            out.append(grid[at])
    return "".join(out)

def grid_slice(grid_in,width,height,slice_width,slice_height,
               min_x=None,min_y=None,max_x=None,max_y=None,
               x_offset=1,y_offset=1):
    """
    grid_in, 1d List with ascii characters per cell

    width height, width and height of the grid, currently height is superfluous

    slice_width slice_height, the current chunk to tokenize

    min_x min_y, optional floor for for starting points on the grid

    max_x max_y, optional ceiling for the ending points on grid, note that this
                 actualy is subracted from the grid width - the slice_width as
                 the slice is slice_width from current x point and slice_height
                 is from the current y point

    x_offset y_offest, skips for x and y for instance having an x_offset of 2
                       will mean items divisible by x (-min_x) will be evaluate
                       as the left side of the slice.
    
    """
    #slice and tokenize entire grid
    out_dict = {}
    if min_x is None:
        min_x = 0
    if min_y is None:
        min_y = 0
        
    #+1 is for instance slicing at a 5x5 square by 5x5, a zero would produce
    #no results as xrange(0) so adding one is only solution
    if max_x is None:
        max_x = width-slice_width+1
    if max_y is None:
        max_y = height-slice_height+1

    
    for y in xrange(min_y,max_y,y_offset):
        for x in xrange(min_x,max_x,x_offset):
            #get token
            got = tokenize_grid_slice(grid_in,x,y,width,height,
                                      slice_width,slice_height)
            #if token exists add the current x,y to that list
            if got in out_dict:
                out_dict[got].append((x,y))
            #otherwise add token to the dictionary with a list of the current
            #coord tuple
            else:
                out_dict[got] = [(x,y)]
    return out_dict


def rotate_string(string_in,width):
    """
    string_in, the string to be rotated

    width , if the string were converted to a grid it would be this wide

    return the string as if it had been rotated around the center on a 2d grid
    """
    #rotate the string by 90 and return that string
    #derived by wrighting down the rotation and extrapulating
    #I can not give a math awnser for how this works because I am terrible
    #at math
    #could probably be better written as a default list of string_in size of
    #nones and then project the characters to their end resting place but
    #once again I'm terrible at math.
    out = []
    for main in xrange(width):
        string_slice = string_in[main*width:main*width+width]
        for at,sub in enumerate(string_slice):
            out.insert(at*main+at,sub)
    return "".join(out)

def create_template(template_string,replace_string,width):
    """
    template_string , the base template that will check on the grid
    
    replace_string , the replacement when the template_string matches
    
    width , if the string were convereted to a grid ti would be this wide

    Returns a list of tuples the tuple being (template, result) where both have
            been rotated 360 degrees 90 degrees at a time
    """
    #for this width and height has to be the same so only width is passed in
    #returns a list of template and the replace string
    template_out = []
    #normal
    template_out.append((template_string,replace_string))
    #string for transformation
    current_template = template_string
    current_replace = replace_string
    for i in xrange(3):
        #rotate by 90 each time
        current_template = rotate_string(current_template,width)
        current_replace = rotate_string(current_replace,width)
        template_out.append((current_template,current_replace))
    return template_out

def apply_replace_to_grid(x,y,grid,grid_width,grid_height,
                          replace,replace_width):
    """
    x y , top left coordinates of where the string is going to repalce
    
    grid , 1d List with ascii characters per cell

    grid_width grid_height , width and height of the grid, currently height is
                             superfluous

    replace , the string to replace with ? are skipped

    replace_width , the width of the replace string as if it were a 2d grid

    blit into grid replacement string while obeying 2d formating
    """
    start_at = x+y*grid_width
    for at,i in enumerate(replace):
        if i != "?":
            ax = at%replace_width+x
            ay = at/replace_width+y
            grid_at = ax+ay*grid_width
            grid[grid_at] = i

def get_valid_spot(grid_dict,re_list):
    """
    grid_dict , tokenization dict of the grid with locations to the the
                top left x,y

    re_list , list of tokens to check against

    returns matching tokens    
    """
    out = []
    for i in re_list:
        for key in grid_dict:
            if match_string(i,key):
                out.append(key)
    return out


def print_grid(grid,width):
    """
    grid , the grid
    width , width of the grid

    prints to screen the grid
    """
    for i in xrange(width):
        print "".join(grid[i*width:i*width+width])
```

## Large example

---

The following map was created with the following templates  

First room was created with following template placed on a 15x15 square of x tiles  

```text
XXXXXXXXXXXXXXX
X######+######X
X#...........#X
X#...........#X
X#...........#X
X#...........#X
X#...........#X
X+...........+X
X#...........#X
X#...........#X
X#...........#X
X#...........#X
X#...........#X
X######+######X
XXXXXXXXXXXXXXX
```

Next 20 iterations of rooms were attempted to be placed.  

The template string was  

```text
?????##+##?????
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
?XXXXXXXXXXXXX?
???????????????
```

The replacement string was the following. Only 1 was choosen to create rooms of the same exact size on the same grid patern  

```text
???????????????
?######+######?
?#...........#?
?#...........#?
?#...........#?
?#...........#?
?#...........#?
?+...........+?
?#...........#?
?#...........#?
?#...........#?
?#...........#?
?#...........#?
?######+######?
???????????????
```

Corner themes had the following template  

```text
???????
?######
?#.....
?#.....
?#.....
?#.....
?#.....
```

The following strings were used for replacement  

```text
???????
???????
??.....
??.##..
??.##..
??.....
??.....
```

```text
???????
???????
??####.
??####.
??####.
??####.
??.....
```

```text
???????
???????
??.....
??..##.
??.###.
??.###.
??.....
```

```text
???????
???????
??.....
??.___.
??.___.
??.....
???????
```

And some middle pillars/pit/other such things  

The template for this  

```text
???????
?.....?
?.....?
?.....?
?.....?
?.....?
???????
```

and the replacement string  

```text
???????
?.....?
?.###.?
?.###.?
?.###.?
?.....?
???????
```

```text
???????
?.....?
?.___.?
?.___.?
?.___.?
?.....?
???????
```

Resulting map after empty doors were removed or turned to floor tiles. Not the most exciting result but this is still experimental. More runs of the corner and pillar/pit in middle of room would make this more interesting.  

## Example output  

---

```text
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXX#############################################################XXXXXXXXXXX
XXXXXXXX#...........#...........#...........#...........#...........#XXXXXXXXXXX
XXXXXXXX#...........#..##.......#...........#...........#...........#XXXXXXXXXXX
XXXXXXXX#...........#.###.......#...........#...........#...........#XXXXXXXXXXX
XXXXXXXX#...........#.###.......#...........#......###..#...###.....#XXXXXXXXXXX
XXXXXXXX#...........#...........#...........#......###..#...###.....#XXXXXXXXXXX
XXXXXXXX#..........................................###......###.....#XXXXXXXXXXX
XXXXXXXX#...........#...........#...........#...........#...........#XXXXXXXXXXX
XXXXXXXX#.......#####.__........#...........#....###....#.###.......#XXXXXXXXXXX
XXXXXXXX#.......#####.__........#...........#....###.##.#.###.......#XXXXXXXXXXX
XXXXXXXX#.......#####.__........#...........#....###.##.#..##.......#XXXXXXXXXXX
XXXXXXXX#.......#####...........#...........#...........#...........#XXXXXXXXXXX
XXXXXXXX##################.###################################.######XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#####.......#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#.##.###....#XXXXXXXXXXXXXXXXXXXXXXX#####....##.#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#.##.###....#XXXXXXXXXXXXXXXXXXXXXXX#####....##.#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#....###....#XXXXXXXXXXXXXXXXXXXXXXX#####.......#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#.__........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#.__........#XXXXXXXXXXXXXXXXXXXXXXX#.##........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#.__........#XXXXXXXXXXXXXXXXXXXXXXX#.##........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXX##################.######XXXXXXXXXXX##################.######XXXXXXXXXXX
XXXXXXXX#####...#####...........#XXXXXXXXXXX#...........#...........#XXXXXXXXXXX
XXXXXXXX#####...#####.___.......#XXXXXXXXXXX#..##.......#...........#XXXXXXXXXXX
XXXXXXXX#####...#####.___.......#XXXXXXXXXXX#.###.......#...........#XXXXXXXXXXX
XXXXXXXX#####...#####...........#XXXXXXXXXXX#.###.......#...###.....#XXXXXXXXXXX
XXXXXXXX#...........#...........#XXXXXXXXXXX#...........#...###.....#XXXXXXXXXXX
XXXXXXXX#.......................#XXXXXXXXXXX#...............###.....#XXXXXXXXXXX
XXXXXXXX#...........#...........#XXXXXXXXXXX#...........#...........#XXXXXXXXXXX
XXXXXXXX#...........#.###.......#XXXXXXXXXXX#...........#.###.......#XXXXXXXXXXX
XXXXXXXX#...........#.###.......#XXXXXXXXXXX#...........#.###.......#XXXXXXXXXXX
XXXXXXXX#...........#..##.......#XXXXXXXXXXX#...........#..##.......#XXXXXXXXXXX
XXXXXXXX#...........#...........#XXXXXXXXXXX#...........#...........#XXXXXXXXXXX
XXXXXXXX##################.######XXXXXXXXXXX##################.######XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#..##.......#XXXXXXXXXXXXXXXXXXXXXXX#..##.......#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#.###.......#XXXXXXXXXXXXXXXXXXXXXXX#.###.......#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#.###.......#XXXXXXXXXXXXXXXXXXXXXXX#.###.......#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#.__........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#.__........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#.__........#XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXXXXXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXX##################.##################XXXXXXXXXXX######.######XXXXXXXXXXX
XXXXXXXX#...........#####.......#####.......#XXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXX#...........#####.......#####.......#XXXXXXXXXXX#.##........#XXXXXXXXXXX
XXXXXXXX#...........#####.......#####.......#XXXXXXXXXXX#.##........#XXXXXXXXXXX
XXXXXXXX#...........#####.......#####.......#XXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXX#...........#...........#...........#XXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXX#...................................#XXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXX#...........#...........#...........#XXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXX#...........#...........#...........#XXXXXXXXXXX#.......###.#XXXXXXXXXXX
XXXXXXXX#...........#...........#.......___.#XXXXXXXXXXX#.......###.#XXXXXXXXXXX
XXXXXXXX#...........#...........#.......___.#XXXXXXXXXXX#.......##..#XXXXXXXXXXX
XXXXXXXX#...........#...........#...........#XXXXXXXXXXX#...........#XXXXXXXXXXX
XXXXXXXX#####################################XXXXXXXXXXX#############XXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```
