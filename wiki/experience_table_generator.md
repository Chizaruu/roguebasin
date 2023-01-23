# Experience table generator

---

```c
/*
  This is a utility for generating experience point tables.

  Each line printed will display a level number, the number of points
  required to reach that level and finally the gap to the next level.

  Compile with something like: gcc -lm xptable.c -o xptable

  ulf.astrom@gmail.com / happyponyland.net, 2014-11-20
*/

#include <math.h>
#include <stdio.h>

int main()
{
  long total = 0;
  int level;

  // You probably want to change these!

  // Points required to go from level 1 -> 2
  long tnl = 100;

  // How quickly the curve flattens out
  double factor = 0.95;

  // The number of levels to display
  int levels = 20;

  printf("%-10s|  %-13s|  %-13s\n", "Level", "Exp acquired", "Exp until next level");
  printf("-------------------------------------------\n");
  for (level = 1; level <= levels; level++)
  {
    printf("Level %2d  |  %-12ld |  %-12ld |\n", level, total, tnl);
    total += tnl;
    tnl = tnl * (1 + pow(factor, level));
  }

  return 0;
}
```
