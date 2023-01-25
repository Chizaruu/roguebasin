# Simple maze

---

## Maze Generator in C++

---

Simple maze generator written in 10 minutes :) The source code is public domain.

```c++
// Simple Maze Generator in C++ by Jakub Debski '2006

#include <time.h>
#include <vector>
#include <list>
using namespace std;

int main()
{
 srand(time(0));

 const int maze_size_x = 80;
 const int maze_size_y = 25;
 vector<vector<bool>> maze;
 list<pair<int, int>> drillers;

 maze.resize(maze_size_y);
 for (size_t y = 0; y < maze_size_y; y++)
  maze[y].resize(maze_size_x);

 for (size_t x = 0; x < maze_size_x; x++)
  for (size_t y = 0; y < maze_size_y; y++)
   maze[y][x] = false;

 drillers.push_back(make_pair(maze_size_x / 2, maze_size_y / 2));
 while (drillers.size() > 0)
 {
  list<pair<int, int>>::iterator m, _m, temp;
  m = drillers.begin();
  _m = drillers.end();
  while (m != _m)
  {
   bool remove_driller = false;
   switch (rand() % 4)
   {
   case 0:
    (*m).second -= 2;
    if ((*m).second < 0 || maze[(*m).second][(*m).first])
    {
     remove_driller = true;
     break;
    }
    maze[(*m).second + 1][(*m).first] = true;
    break;
   case 1:
    (*m).second += 2;
    if ((*m).second >= maze_size_y || maze[(*m).second][(*m).first])
    {
     remove_driller = true;
     break;
    }
    maze[(*m).second - 1][(*m).first] = true;
    break;
   case 2:
    (*m).first -= 2;
    if ((*m).first < 0 || maze[(*m).second][(*m).first])
    {
     remove_driller = true;
     break;
    }
    maze[(*m).second][(*m).first + 1] = true;
    break;
   case 3:
    (*m).first += 2;
    if ((*m).first >= maze_size_x || maze[(*m).second][(*m).first])
    {
     remove_driller = true;
     break;
    }
    maze[(*m).second][(*m).first - 1] = true;
    break;
   }
   if (remove_driller)
    m = drillers.erase(m);
   else
   {
    drillers.push_back(make_pair((*m).first, (*m).second));
    // uncomment the line below to make the maze easier
    // if (rand()%2)
    drillers.push_back(make_pair((*m).first, (*m).second));

    maze[(*m).second][(*m).first] = true;
    ++m;
   }
  }
 }

 // Done
 for (size_t y = 0; y < maze_size_y; y++)
  for (size_t x = 0; x < maze_size_x; x++)
  {
   if (maze[y][x] == true)
    printf(".");
   else
    printf("#");
  }

 return 0;
}
```

### Example

```text
######.........#.#.......#.....#...#.....#.........#.#...#.#.......#.......#####
######.#.###.#.#.#.#######.#.#####.#.###.#.#.#####.#.###.#.#.#######.###########
.....#.#.#...#.#.........#.#.#.....#.###...#.#.....#.......#.......#.....#######
.#.#.###.#.#####.#.#.#######.#.#.#.#.#####################.#.###.#######.#######
.#.#.#...#...#...#.#...#.#.#.#.#.#.#.........#.....#...........#.#.............#
####.#.#######.#######.#.#.#.#.#.#######.#.#.#.#.#.#.#######.#######.#######.#.#
##...#...#####.#.#####.#...#...#.#...#.#.#.#...#.#...###...#.#.......#.......#.#
####.###.#####.#.#####.#.###.#.###.###.#.#.###.#########.#####.###.#############
.........###.#.....###.#...#.#.#.#.......#.#...#...#.#.#.#.......#.###...#.....#
########.###.#.#.#.###.#.#.###.#.#.#####.#.###.#.###.#.#.#.#.###.#####.###.#.###
.......#.....#.#.#.#...#.#...........#...#.#.....#.......#.#...#.#.........#...#
####.#.#.#####.#.###.#######.#######.###.#.#.###.#.#####.#.#####.#.#.#####.#####
...#.#.#.......#...#.....#.....#.#...#####.#...#.......#.#...#.....#...#.#.....#
##.###.#.#.#.###.#####.#.#####.#.#.#######.#####.#####.#.###.#.###.#####.#####.#
##...#...#.#.#.......#.#.....#...#.......#...#...#.....#.....#...#...........#.#
####.#.###########.###.#.###.#####.#######.###.###########.###.###.#####.###.###
####...###.....#...#.#.#...#...........###...#.###...#.###.###...#.....#.#.....#
####.#####.#######.#.###.###.#.#####.#.#############.#.###.#####.#####.#.###.###
####.........#.#...#.....#.#.#.#.....#.###.#...#.............#...#####.#...#...#
######.#.#####.#.###.#.#.#.###.#######.###.#.#####.###.#################.#######
######.#...#.#.....#.#.#.#...#...###...###.....#...#.......#########...#.......#
######.###.#.#####.#####.#.###.#.###.#.###.#.###.###.#####.#########.#########.#
######.#.#.#...###...#...#.#.#.#...#.#.....#.#.#...#.###...#########...#.......#
########.#.###.#######.#.#.#.###.#######.###.#.#####.###############.#.###.#####
########...###.........#.#.......#######.#.....#####.......#########.#.........#
```

## Maze Generator in Perl

---

This is a recursive backtracker maze generator in [Perl](perl.md):

```perl
#!/usr/bin/env perl

use strict;
use warnings;

no warnings 'recursion';
no warnings 'uninitialized';

use List::Util qw/shuffle/;

### Constants

my ( $WIDTH, $HEIGHT ) = ( 50, 30 );

my %DIRECTION = (
    N => { dy => -1, opposite => 'S' },
    S => { dy => 1,  opposite => 'N' },
    E => { dx => 1,  opposite => 'W' },
    W => { dx => -1, opposite => 'E' },
);

### Code

my $map = carve( [], $WIDTH / 2, $HEIGHT / 2, 'E' );
print output($map);

sub carve {
    my ( $map, $x0, $y0, $direction ) = @_;

    my $x1 = $x0 + $DIRECTION{$direction}{dx};
    my $y1 = $y0 + $DIRECTION{$direction}{dy};

    return if $x1 == 0 or $x1 == $WIDTH or $y1 == 0 or $y1 == $HEIGHT;
    return if defined $map->[$x1][$y1];

    $map->[$x0][$y0]{$direction} = 1;
    $map->[$x1][$y1]{ $DIRECTION{$direction}{opposite} } = 1;

    carve( $map, $x1, $y1, $_ ) for shuffle keys %DIRECTION;

    return $map;
}

sub output {
    my ($map) = @_;

    my $output = '';
    for my $y ( 0 .. $HEIGHT ) {
        for my $x ( 0 .. $WIDTH ) {
            if ( defined $map->[$x][$y] ) {
                $output .= $map->[$x][$y]{S} ? ' ' : '_';
                $output .= $map->[$x][$y]{E} ? ' ' : '!';
            }
            else {
                $output .= '##';
            }
        }
        $output .= "\n";
    }
    $output =~ s/_ /__/gs;

    return $output;
}
```

### Example

```text
$ ./perl-generator.pl
######################################################################################################
##  ______   !  ________ !  __ !    __ !  __ ! !    __ !    ___!    _!  __ !  ____   !   !    __ ! !##
##______ ! !___!  ______ !___!___! !__ ! ! ! !  _! !  _! !_______!_____! ! !____ ! !___! !_! !  ___!##
##    _! ! !______ !   !________ !__ !_! ! ! !__ ! !  _! !__  ______ !  _!__  _! ! !_________!_!   !##
## ! !  _! !   !___! !__ !  _!  _!   !  _! !__ !_! ! !  _____!  __ ! !  __ ! !  _! !  ______ !  _! !##
## !_____!__ ! !  ___!  _!   !  ___!_!__  ___!__ ! !_!__ !   ! !   ! !___! ! !__ ! !__ !__ !_____! !##
##_!  ____ ! ! !__ ! !__ ! ! ! !  ____ ! !  _____! !  ___! ! ! ! ! !______ !  _! !_____!   ! !  ___!##
##  _!__ ! !_!  _!____ ! ! ! ! !__ ! !___!______ !__ !  ___! !_! !____ ! ! !_____!  __ ! !___!____ !##
##  __ !______ !  _____! !_!_____!______ !__  _!__ ! ! !   ! !  _!   !__ ! !__  ___!  _!__ !  __ ! !##
##___!__ !  ___! ! !  ___!  ________  _!__ ! !   ! ! ! ! ! ! !  ___!__ ! ! !  _!   !____ ! ! !____ !##
##   !  _!____ !__ !__ !  _!______ !_______! ! !__ !_! !_! ! !__ ! !  _!_! ! !  _!____ !__ ! !   ! !##
## ! ! !   !  ___!__ ! !____ !   !__ !  __   ! !___!  _!  _! ! !__ ! !   ! ! !__ ! !  ___! !___! ! !##
## ! ! ! ! !  _!  ___!  __ !___! !  _! !  _! !____ ! !____ ! !_____!___!_____!  _! !____ ! ! !  _! !##
## !___!_!__ !  _!   !__ ! !__ ! !_____! ! ! !   ! ! !  ___!____   !  __ !__ ! !    __ !___! ! !__ !##
## !  ____ !_!__ !_!  _! ! !   !______ ! !___! ! !_____!  ____ ! !___!  _!  _! !_!   !__  ___!__ !_!##
## ! !   !____ !__ ! !  _!___!_____!  _! !  ___! !__ !  _!  _! !_____! ! !   !__ !_! ! ! !  __ !__ !##
## !___! !   !_____! !____ !  _______! !  _!__ ! !  _!__ !__ !_________!  _!__ ! !  _!__ !__ !____ !##
## !  ___! !  _!  __  __ !___!   !  __ ! !    _! ! !  ___!   !   !____ ! !__ !___! !   !__ !____ ! !##
## !__ ! ! ! !  _! !__ !_____! !_____!_____! !   !___!    _!___! !  ___!________  _! !__ !___!  _! !##
##  _! !  _!___!____ !____ !  ___!  __ !  ___! !__ !  _!__   !  _!______________ !  _! !_______! ! !##
##  ___! !__  ____ !  __ ! ! !  ___!  _!__ ! !__ !_! !   !_! ! !     !    ____ !_! !  __ !  __  _! !##
## !   !__ ! !   ! ! ! ! !___! !  _!____ !__ ! ! !  _! ! !  _! ! ! ! ! ! !   !_____! ! ! ! !  _!  _!##
## !_!____ ! ! !___!  _!__   ! !    ___! ! !__ ! ! !  _!___!  _! ! !___!_! ! !__  _!__ ! ! !__ !__ !##
##______ !___!____ ! !    _!_! ! !_______!____ ! ! !____ ! ! !___!____   ! !__ ! !  ___! ! ! !__ ! !##
##  ___!____ !____ !_! ! !  ___!____________ ! !___!  ___!  _!   !   ! !___!  _!  _!  __ !____ !___!##
## !   !  ___!   !  ___!_! !  ________ !  ___!_!  ___!__ ! !  _!___! !_!  ___!___!  _!  _____! !__ !##
##  _!___!  ___! !_!  _____! !__  _!  _! !  __  _!     ! ! !__ !   ! !  _!__  _____!  ___!   !__ ! !##
##_________!  ___!  _!    _!__ !__ !__ ! !___! !  _! !___!_____! !_!___!  ___!__  _____!  _!__ !__ !##
##  ______ ! !____ ! ! !__ !  _!   !  _!_______! ! !__ !  __  _!  ______ !  __ !_!   !  _!   !  _! !##
##_______!_________!_____!_______!_!_________________!_____!___________!_____!_____!_______!_!_____!##
######################################################################################################
```

### More examples

The following patch will do a backtrack, generating a dead end every 50 steps:

```diff
@@ -20,9 +20,22 @@ my %DIRECTION = (
 my $map = carve( [], $WIDTH / 2, $HEIGHT / 2, 'E' );
 print output($map);

+my ( $steps, $backtrack ) = ( 0, 0 );
+
 sub carve {
     my ( $map, $x0, $y0, $direction ) = @_;

+    $steps++;
+
+    if ( $steps > 1 and $steps % 50 == 0 ) {
+        $backtrack = 20;
+    }
+
+    if ( $backtrack > 0 ) {
+        $backtrack--;
+        return;
+    }
+
     my $x1 = $x0 + $DIRECTION{$direction}{dx};
     my $y1 = $y0 + $DIRECTION{$direction}{dy};
```

The result is a more complex maze, with a cave-like appearance:

```text
$ ./perl-generator.pl
######################################################################################################
########     !  _!______  _!    __ !   ! !    __________ !##########   !    ____ !  __ ! !############
########_! !___!   !#### !  _!_!  __ ! !  _!__ !  ___!  _!  ___! !  _! ! !_!   !___!##___!############
##   !##  ____ ! ! !   !___!__  _!  _! !_!    _! !#### !## !  ___!  _!____ ! !__ !####################
## !__ !____ !  _! ! !____   !__ !__ ! !  _! !  _!####_!  _!____ ! !   !__ !_!## !####################
## !       ! ! !___!  ___! !_____!  _!___!##  _!  ____  _!    ___!_! !__ !  _!  ____ !################
## ! ! ! !___! !__  _!  ___!######___!    _! !## !__ ! !  _! !   !## ! ! ! !  _!  __ !################
## ! ! !____ !__ ! !_______!######__ ! !____ !_______! ! !  _!_!__ !___!_____!  _!##__ !##############
## ! !__ ! !__ !  __ !############## !_!   !________ !  _! !   !  _!   !##   !______ ! !##############
##_!   !__ ! !_! !  _!######____ !## !  _!__ !  _____! !__  _!__ !  _! !  _! !  _! ! !__   !##########
##  _! ! !_____!_!__ !########  _!  _!_!  _!__ ! !______ ! ! !  _!_!  _! !     !  _!_!  _! !##########
##   !____ !##_______!######  _!  _!  ___!  _!  _!####  _! !______  _! !_! ! !_!____ ! !  ___!########
## ! !##  _!##########  _!   !____ !__ !__________  ___!____ ! ! ! !  ____ ! !    _! ! !__ !  _!   !##
## ! !##__ !####  _____!  _!____  _!   !  __ !____ !  __ ! !___! ! !____ ! !___!____   !___!_____! !##
## !____ !_!#### !__ !   !__ !  _!  _! !__ !  __ !___!_____! ! !  _!__ ! !_! !  __ ! !__ !  ______ !##
## !  __ !   ! !__ !   !__ !_____!__ !__ ! !__ ! !____ !  __ ! ! !  __ !_____! !_____! !___! !   ! !##
##_! ! !__ ! !__ ! ! !___!____ !  ____ !___!  _!____ ! !__ ! !___! !___!##   !  ____ ! !   !___!__ !##
## ! ! !  _!_____! !__ !  __ !___!#### !__ ! ! !    _!_____!____  _!  _!   !_!__ !   ! ! ! !  _____!##
##  ___! !  _!__  _!  ___!___!########____ !___! !   !  ____ !  _!   !  _!_!   !_! ! ! ! !_!__ !   !##
##_!  ___! !##    ___!   !##############  _!  ___! !___!   !_! !  _!_! !   ! !__ ! !___!__ !  _! !_!##
##  _!  ___!  _!_______! !##############_!## !####_______!  ____ !  ____ ! ! ! !_!______ !_______! !##
##  ___!   !__ ! !   !  _!##################_!####____  _!__ !___! !   ! ! !__  ______ ! !  __ ! ! !##
## !  ___!  _!___!_! !   !   !######################## !   !  __ !__ ! ! !___!______ ! ! !__ !___! !##
## ! ! ! !  ____   !  _!___! !######################## ! ! !__ !__ !_! !__  ____ !  _! !  ______ ! !##
## !   ! !____ ! !__  _!__ ! !########################___! !  _! ! !  _! !__ !##___!  ___!    _! ! !##
## ! ! ! !#### !__ !   !  _!___!######################  __ !__ !__  _!____ ! !####  _!  ___!  _____!##
## ! ! ! !####_!## !_! ! !############################__ !   !  _!____ !## ! !##  _!## ! !  ___! !####
## ! !___!##_______!  _! !############################  _! !______ !## !##___!## ! !___! ! !   !__ !##
##  _!##   ! !  ___!__ !__ !########################## ! ! !   !  _!## !######___!____  _!__ !__ ! !##
##_______!___!_____________!##########################___!_!_!___!####_____!##########_______!##___!##
######################################################################################################
```

See also: [Dynamically Sized Maze](dynamically_sized_maze.md)

## Maze Generator in Javascript

---

The following code is a port from the original [Perl](perl.md) implementation (above):

```javascript
#!/usr/bin/env node

(function () {
  var mazeGenerator = {
    map: [],
    WIDTH: 50,
    HEIGHT: 30,

    DIRECTIONS: {
      N: { dy: -1, opposite: "S" },
      S: { dy: 1, opposite: "N" },
      E: { dx: 1, opposite: "W" },
      W: { dx: -1, opposite: "E" },
    },

    prefill: function () {
      for (var x = 0; x < this.WIDTH; x++) {
        this.map[x] = [];
        for (var y = 0; y < this.HEIGHT; y++) {
          this.map[x][y] = {};
        }
      }
    },

    shuffle: function (o) {
      for (
        var j, x, i = o.length;
        i;
        j = Math.floor(Math.random() * i), x = o[--i], o[i] = o[j], o[j] = x
      );
      return o;
    },

    carve: function (x0, y0, direction) {
      //console.log('[%d, %d, "%s"]', x0, y0, direction);

      var x1 = x0 + (this.DIRECTIONS[direction].dx || 0),
        y1 = y0 + (this.DIRECTIONS[direction].dy || 0);

      if (x1 == 0 || x1 == this.WIDTH || y1 == 0 || y1 == this.HEIGHT) {
        return;
      }

      if (this.map[x1][y1].seen) {
        return;
      }

      this.map[x0][y0][direction] = true;
      this.map[x1][y1][this.DIRECTIONS[direction].opposite] = true;
      this.map[x1][y1].seen = true;

      var directions = this.shuffle(["N", "S", "E", "W"]);
      for (var i = 0; i < directions.length; i++) {
        this.carve(x1, y1, directions[i]);
      }
    },

    output: function () {
      var output = "";
      for (var y = 0; y < this.HEIGHT; y++) {
        for (var x = 0; x < this.WIDTH; x++) {
          output += this.map[x][y].S ? " " : "_";
          output += this.map[x][y].E ? " " : "!";
        }
        output += "\n";
      }
      output = output.replace(/_ /g, "__");
      console.log(output);
    },
  };

  mazeGenerator.prefill();
  mazeGenerator.carve(mazeGenerator.WIDTH / 2, mazeGenerator.HEIGHT / 2, "N");
  mazeGenerator.output();
})();
```

### Example

```text
$ ./javascript-generator.js
_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!_!
_!  ______ !  ________  ________ !  ______ !    __ !  ________  __ !____  ___!    _!   !  ____ !   !
_!______ ! ! !   !   !_!   !__  _! !__ !  _! ! ! ! !____  _!  _! !____ !_______! !  _! !____ !___! !
_! !  ___! ! ! !___!_____!__ !___!____ !_____! ! !__ ! ! !  _! !  __ !________ !  _! ! !  ___! !  _!
_! !____ ! !____  ________ !__ !  __ !__ !   ! ! !  _!  _!__ !____ !__ !  __ ! !____ !___!__   !__ !
_! !   ! !__ ! ! !  ____ !___! ! ! !_____! !_! !____ !_____!  __ !___! !__ !___!   !________ !___! !
_!  _! !  _! ! ! !____ !____ !__________ !__ !__ !________ !_!  _!  _____!____ ! !___!   !  _!  ___!
_! ! !___!  _!_____!  _!__ !______ !   !__ !  _!__ !     ! !  _!  _!  ____ !  _!____  _!_____!____ !
_! !__ !  _!  ____ !____ !  __   ! ! ! ! ! !__ !  _!_! !_! ! !  _!  _!  ___! !  __ ! !  ______ !   !
_!__ ! !_____!__ !    _! !_!   !_!___! !____ ! !____ !_______!  ___! !_______!__ ! !_!____ ! !___! !
_! !____ !____   ! !_!  _!   !_!  __ !   !__ !   !  _!  ____ ! !  ______ !  __ ! !_________! !   ! !
_! !   !_______! !_____!__ ! !  _! ! ! !__ !___! !_____!__ ! !___! !   ! ! ! ! !   !  __  __ ! !___!
_!  _!______   !__ !____  _!_! !   ! ! ! !____ ! !  _______!__ !_____! ! ! !  _!_! !__ ! !___! !  _!
_!____ !  ___!__ !__ !   !  ___! ! ! !__ !  ___!___!  ______ !______ !_____! !  ___! ! !____ ! !   !
_! !  _!__ !   ! ! !___! !__ !__ !_! !___!__  __ !  _!    _! !  __ !  __ !_____! !   ! !   ! ! !_! !
_!  _____! !_! !  _____! ! !__ !  ________ !___!  __ ! ! !  _!   ! !___! !  __ !___! !___! !___!  _!
_! !  __ !__ ! ! !   !  _!   ! !___!  __ !_______!  _!_! ! ! ! ! !_______! ! !__ !   !  ___! !  __ !
_!_!__ !____ !___! !___!   !_!__ !  _! !______ !____ !   ! !___! !   !   !____ ! ! !_! !__ !  _!   !
_!  ___!__ ! !   !____ !_!__   !___!  ___!   ! !   ! ! !_!____ ! ! !___!  ___! ! ! !  _!  _! ! ! !_!
_! !____  _! ! !  _!  _!  ___!____ ! !   ! !___! ! ! ! !  _____! ! !  _!__ !  _! !  _! !   ! ! !__ !
_!  __ !  ___! !__ ! !________ ! ! !___! ! !  ___! !__ !______ ! ! !__ !  _! ! ! ! ! !  _!___!  _! !
_! ! ! !_____! ! ! ! !  __ !  _! !__ !  _! ! !  _!__ !__ !  ___!  _!   !_______! ! ! ! ! !     !   !
_! ! !______ !__ ! !___! ! ! !  _____!   !___!____ !__ !_! !   ! !   ! !  _!  ___! !  _!__ ! ! ! ! !
_!______ ! !_____!____  _!___! !  ___! !__  ______ !  _!  _! !___! ! !___!  _!  _! !____ ! ! ! ! !_!
_!____ ! !  _!   !   !____  _! ! !  ___!  _!____ ! !_____!______ ! !____ ! !__ !  _!____ !_! ! !__ !
_!   ! ! !_____!___!__   !_____!___!  ___!    ___! !    ____ !  _!_!  __ !__ !________ !__  _!_!  _!
_! !___! !  __ !  _____!__ !  _______! !  _! !  ___! !__ ! ! ! !   !__ ! ! !__ !__  _! !  _!   !__ !
_! !   ! ! !__ !__ !   !  _!____ !   !  _! ! !____ ! !  _!  _!___!  ___!_____! !  _!  _!_!  _!____ !
_!  _!___!__ !__ !___! ! !__   ! ! ! !__ ! ! !  ___!_! ! !______ !_!  _________!   !____ !____ ! ! !
_!___________!_________!_____!___!_!_______!___________!_____________!___________!_____________!___!
```

### Demo

[Click here](http://jsfiddle.net/o20073mp/) to see the code in action.

## Maze Generator in [Visual Basic](visual_basic.md) 6

```vb
''''''''''''''''''''''''''
' Perfect Maze Generator '
'          Icey          '
'        Oct 2006        '
'      Public Domain     '
''''''''''''''''''''''''''

' this code is designed to be run from a module, using sub main

' all variables are declared
Option Explicit
' 0 is the most random, randomisation gets lower after that
' less randomisation means more straight corridors
Private Const RANDOMISATION As Integer = 5
' the spaces across - this must be an odd number
Private Const MAZE_X As Integer = 53
' the spaces down - this must be an odd number
Private Const MAZE_Y As Integer = 17
' used for storing the current square and squares potentially to move to
Private Type COORDS
    X As Integer
    Y As Integer
End Type
' stores the directions that corridors go in
Dim cDir(3) As COORDS
' these can be any odd numbers
Dim blnMaze(MAZE_X, MAZE_Y) As Boolean

Private Sub GenerateMaze()
    Dim cN As COORDS, cS As COORDS
    Dim intDir As Integer, intDone As Integer
    Dim blnBlocked As Boolean
    Randomize
    Erase blnMaze
    Do
        ' this code is used to make sure the numbers are odd
        cS.X = 2 + (Int(((MAZE_X - 1) * Rnd) / 2) * 2)
        cS.Y = 2 + (Int(((MAZE_Y - 1) * Rnd) / 2) * 2)
        ' first one is free!
        If intDone = 0 Then blnMaze(cS.X, cS.Y) = True
        If blnMaze(cS.X, cS.Y) Then
            ' always randomisation directions to start
            RandomDirections
            Do
                ' only randomisation directions, based on the constant
                If Int(RANDOMISATION * Rnd) = 0 Then RandomDirections
                blnBlocked = True
                ' loop through order of directions
                For intDir = 0 To 3
                    ' work out where this direction is
                    cN.X = cS.X + (cDir(intDir).X * 2)
                    cN.Y = cS.Y + (cDir(intDir).Y * 2)
                    ' check if the tile can be used
                    If IsFree(cN) Then
                        ' create a path
                        blnMaze(cN.X, cN.Y) = True
                        ' and the square inbetween
                        blnMaze(cS.X + cDir(intDir).X, cS.Y + cDir(intDir).Y) = True
                        ' this is now the current square
                        cS.X = cN.X
                        cS.Y = cN.Y
                        blnBlocked = False
                        ' increment paths created
                        intDone = intDone + 1
                        Exit For
                    End If
                Next
            ' loop until a path was created
            Loop Until blnBlocked
        End If
    ' create enough paths to fill the whole grid
    Loop While intDone + 1 < ((MAZE_X - 1) * (MAZE_Y - 1)) / 4
End Sub

' this changes the direction to go from the current square, to the next available
Private Sub RandomDirections()
    ' clear the array
    Erase cDir
    ' four possible sets of directions
    Select Case Int(3 * Rnd)
        Case 0
            cDir(0).X = -1: cDir(1).X = 1
            cDir(2).Y = -1: cDir(3).Y = 1
        Case 1
            cDir(3).X = -1: cDir(2).X = 1
            cDir(1).Y = -1: cDir(0).Y = 1
        Case 2
            cDir(2).X = -1: cDir(3).X = 1
            cDir(0).Y = -1: cDir(1).Y = 1
        Case 3
            cDir(1).X = -1: cDir(0).X = 1
            cDir(3).Y = -1: cDir(2).Y = 1
    End Select
End Sub

' checks if a tile is free for use
Private Function IsFree(cF As COORDS) As Boolean
    ' check it's within the grid
    If cF.X < MAZE_X And cF.X > 1 And cF.Y < MAZE_Y And cF.Y > 1 Then
        ' check it hasn't been used yet
        IsFree = (blnMaze(cF.X, cF.Y) = False)
    End If
End Function

' this code should be run from a module
' go to Project > [projectname] Properties
' and then select Sub Main from the Startup Object list
Sub Main()
    ' the maze is added to this string, which is then copied to the clipboard
    Dim strOutput As String
    GenerateMaze
    Dim A As Integer, B As Integer
    ' loop through squares
    For A = 1 To MAZE_Y
        For B = 1 To MAZE_X
            ' check if a path was created here
            If blnMaze(B, A) Then
                ' empty
                strOutput = strOutput & " "
            Else
                ' wall
                strOutput = strOutput & "#"
            End If
        Next
        ' go down to the next row
        strOutput = strOutput & vbNewLine
    Next
    Clipboard.Clear
    Clipboard.SetText strOutput
    ' tell the user what has happened
    MsgBox "Maze copied to the clipboard.", vbInformation, "Maze generator"
End Sub
```

### Examples

Randomisation: 0

```text
###########################################
#     #     #           #           #     #
# ### # ##### # # ##### ### ### # ### #####
# # # # #   # # #     #     # # # #       #
# # # # # # # # ##### ##### # # # # ##### #
# #   # # # # #   # #   #   #   #       # #
# ##### # # # ### # ### ######### ##### # #
#   #   # #   # # #   # #   # #   #   # # #
### ### # # ### # ### # # # # # ##### ### #
# # #   # # #   #   # #   # #         #   #
# # # ### # # # ### # # ### ########### ###
#   #     # # #   #   # # # #     # #   # #
# ######### # ######### # # # ### # # ### #
# # #   #   # #       #   # #   # # #   # #
# # # # # ### # # ### ### # ### # # ### # #
# # # # # #   # # # # #   #   # # #   #   #
# # # # # # ##### # # # ### # ### # # #####
# # # #   # #   # # #   # # #   #   # #   #
# # # ### # # # # # # ### # ### ##### # # #
# #     # # # # #   #     #   #       # # #
# ####### # # # ######### ### ########### #
#         #   #           #               #
###########################################
```

Randomisation: 5

```text
#####################################################
#                 #   #   #                       # #
# ############### # # # # ##################### # # #
#       #   #   # # # # #                       # # #
####### # # # # # # # # ######################### # #
# #   # # #   # # # # # #                       # # #
# # # # ### ##### # # # # # ################### # # #
# # # # # #     # # # # # # #         #   #     # # #
# # # # # ### # # # # # ### ######### # # # # ### # #
# # # # # #   # #   # #   # #         # # # #   # # #
# # # # # ##### ##### # # # # ######### # # ### # # #
# # # # # #           # # # #         # # # #   # # #
# # # # # # ########### # # ######### # # # # ### # #
# # # # # #   #   #   # # # #   #   # # # # #   # # #
# # # # # # # # # # # # # # # # # # # # # # ### # # #
#   #   #   #   #   #   #   # #   #     #   #   #   #
#####################################################
```

## See also

---

- [Dynamically Sized Maze](dynamically_sized_maze.md)
- [Maze Generation](maze_generation.md)
