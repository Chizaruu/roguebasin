# LUA 2d Line of sight

---

Hi, I adapted this function to suit my roguelike from:

<http://www.gamedev.net/reference/articles/article1275.asp> code by Tom Ootjers

It returns true if x1,y1 can "see" x2,y2 without "blockage" in the "line of sight" and "false" if otherwise, so in your LUA code it can be used like:

if funcs.lineofsight(x1,y1) then (draw the creature or shoot the arrow or whatever) end along with your other checks for the action.

I made it go "one round further" when LOS was blocked, so my engine would draw the trees and monsters even though they blocked los.

The many comments are his, also. Credit goes to him basically.

```lua
function funcs.lineofsight(x1,y1,x2,y2)

local math = math

local deltax = math.abs(x2 - x1) -- // The difference between the x's

local deltay = math.abs(y2 - y1) -- // The difference between the y's

local x = x1; -- // Start x off at the first pixel

local y = y1; -- // Start y off at the first pixel

local xinc1,xinc2,yinc1,yinc2

if (x2 >= x1) then -- // The x-values are increasing

xinc1 = 1;

xinc2 = 1;

else -- // The x-values are decreasing

xinc1 = -1;

xinc2 = -1

end


if (y2 >= y1) then -- // The y-values are increasing

yinc1 = 1;

yinc2 = 1;

else -- // The y-values are decreasing

yinc1 = -1;

yinc2 = -1;

end


if (deltax >= deltay) then -- // There is at least one x-value for every y-value

xinc1 = 0; -- // Don't change the x when numerator >= denominator

yinc2 = 0; -- // Don't change the y for every iteration

den = deltax;

num = deltax / 2;

numadd = deltay;

numpixels = deltax; -- // There are more x-values than y-values

else -- // There is at least one y-value for every x-value

xinc2 = 0; -- // Don't change the x for every iteration

yinc1 = 0; -- // Don't change the y when numerator >= denominator

den = deltay;

num = deltay / 2;

numadd = deltax;

numpixels = deltay; -- // There are more y-values than x-values

end

local onemoreround = 0

local roundnum = 0

while (true) do

roundnum = roundnum + 1 --forces two distance sight

           --  // Draw the current pixel

if onemoreround == 1 and roundnum > 2 then return false end --this stuff is so it penetrates one round and doesnt stop on the first round

if (roundnum ~= 1) and funcs.checkforlosblock(x,y) then onemoreround = 1 end --was return false

if x == x2 and y == y2 then return true end



num = num + numadd; --// Increase the numerator by the top of the fraction

if (num >= den) then -- // Check if numerator >= denominator

num = num - den; -- // Calculate the new numerator value

x = x +xinc1; -- // Change the x as appropriate

y = y+ yinc1; -- // Change the y as appropriate

end

x = x+xinc2; -- // Change the x as appropriate

y = y+yinc2; -- // Change the y as appropriate

end

end
```

---

```lua
function funcs.checkforlosblock(x,y) --enter your own checks here to see if the square is has any objects that would block LOS like trees, etc. Those objects should just have "block_los" = true. Return true if square is blocked, false if its clear.
```

---

By Rustle
