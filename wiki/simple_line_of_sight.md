# Simple Line of Sight

---

**Line of Sight - Steve Register [arns@arns.freeservers.com].txt**

This is an article that some of you may find of interest. At the time that I started this club I was looking for some simple line_of_sight code. After much searching I found some source code that was similar to what I was looking for. On the x2ftp site I found the graphical gems collection which contained a c program called Digital Line Drawing by Paul Heckbert. The program was described as "digline: draw digital line from (x1,y1) to (x2,y2), calling a user-supplied procedure at each pixel. Does no clipping. Uses Bresenham's algorithm." If you are interested in seeing the original code I can send it to you or you can find it at the x2ftp site.

The original LOS code that was in my game only checked to see if the player was in the same sector as the monster. If it was then the monster could see the player. I wanted my LOS code to be of the type that knowing the monsters x,y coords and the players x,y coords you would draw a line between the two checking to see if the monsters sight was blocked by any objects. This is what I have now. As of now it doesn't matter how far the player is, if the monsters sight is not blocked it can see him. Later I will change it to limit the distance that a monster can see, but I also want it so that some monsters can see further that others.

The code that I will be explaining will be the modified code that I am using in my program. I started out by trying to use the code with as little changes as possible. Running my program after doing this caused it to hang big time. After a lot of troubleshooting and changing I finaly got it to work (sort of.) I found out that if the player was in a certain position in reference to the monster that the code would not work. I found out that if the player's y coord was equal to or less than the monster's it work ok. If the player's x was less than the monster's x and the player's y was greater than monster's y then the monster would not see the player, but the program didn't hang up. If the player's x and y were greater than the monster's x and y the program would hang.

After much hair pulling and uttering many colorful programing phrases :+) I finally found the problem. The original code used a macro called SGN to return the sign of the delta x and y. It was defined as the following:

```text
#define SGN(a) (((a)<0) ? -1 : 0)
```

Looking at the description of the macro "take binary sign of a, either -1, or 1 if >= 0". I'm still learning but that looked like it would only return either -1 or 0 and not -1 or 1 like the description says.

After changing the macro to this:

```text
#define SGN(a) (((a)<0) ? -1 : 1)
```

everything seemed to start working.

The following is the code that I use in my program. The remarks explain the code as best as I can. If I have something wrong in my explanation if someone would let me know I will change it. I hope this helps someone looking to do something similar.

```c
/* Line of sight code         *
 * this is a Boolean function *
 * that returns FALSE if the  *
 * monster cannot see the     *
 * player and TRUE if it can  *
 *                            *
 * It has the monsters x and y*
 * coords as parameters       */
BOOL los(coord monster_x, coord monster_y)
{
    int t, x, y, abs_delta_x, abs_delta_y, sign_x, sign_y, delta_x, delta_y;

   /* Delta x is the players x minus the monsters x    *
    * d is my dungeon structure and px is the players  *
    * x position. monster_x is the monsters x position passed *
    * to the function.                                 */
   delta_x = d.px - monster_x;

   /* delta_y is the same as delta_x using the y coordinates */
   delta_y = d.py - monster_y;

   /* abs_delta_x & abs_delta_y: these are the absolute values of delta_x & delta_y */
   abs_delta_x = abs(delta_x);
   abs_delta_y = abs(delta_y);

   /* sign_x & sign_y: these are the signs of delta_x & delta_y */
   sign_x = SGN(delta_x);
   sign_y = SGN(delta_y);

   /* x & y: these are the monster's x & y coords */
   x = monster_x;
   y = monster_y;

   /* The following if statement checks to see if the line *
    * is x dominate or y dominate and loops accordingly    */
   if(abs_delta_x > abs_delta_y)
   {
      /* X dominate loop */
      /* t = twice the absolute of y minus the absolute of x*/
      t = abs_delta_y * 2 - abs_delta_x;
      do
      {
         if(t >= 0)
         {
            /* if t is greater than or equal to zero then *
             * add the sign of delta_y to y                    *
             * subtract twice the absolute of delta_x from t   */
            y += sign_y;
            t -= abs_delta_x*2;
         }

         /* add the sign of delta_x to x      *
          * add twice the adsolute of delta_y to t  */
         x += sign_x;
         t += abs_delta_y * 2;

         /* check to see if we are at the player's position */
         if x == d.px && y == d.py)
         {
            /* return that the monster can see the player */
            return TRUE;
         }
      /* keep looping until the monster's sight is blocked *
       * by an object at the updated x,y coord             */
      }
      while(sight_blocked(x,y) == FALSE);

      /* NOTE: sight_blocked is a function that returns true      *
       * if an object at the x,y coord. would block the monster's *
       * sight                                                    */

      /* the loop was exited because the monster's sight was blocked *
       * return FALSE: the monster cannot see the player             */
      return FALSE;
   }
   else
   {
      /* Y dominate loop, this loop is basically the same as the x loop */
      t = abs_delta_x * 2 - abs_delta_y;
      do
      {
         if(t >= 0)
         {
            x += sign_x;
            t -= abs_delta_y * 2;
         }
         y += sign_y;
         t += abs_delta_x * 2;
         if(x == d.px && y == d.py)
         {
            return TRUE;
         }
      }
      while(sight_blocked(x,y) == FALSE);
      return FALSE;
   }
}
```

Well that's it. Not much to it once I figured out what was going on. I have looked at a lot of line_of_sight code but I was unable to figure out what was going on. This is very simple but it gets the job done. Later I plan to make changes but you have to start somewhere.

I hope this will help someone just starting out like me to understand some of the mysteries of coding a Roguelike game.

Keep Coding

_**Steve Register**_

Note: The above code doesn't deal with the case where the monster and player are the same location. The easy fix is to detect that and exit with TRUE.

```c
delta_y */
  abs_delta_x = abs(delta_x);
  abs_delta_y = abs(delta_y);

  /* sign_x
```
