# FOV using recursive shadowcasting - improved

---

Code and improvement for recursive shadowcasting - Henri Hakl

## Introduction

---

This text expands the article by Björn Bergström called "FOV using recursive shadowcasting". The article in question is excellent and describes one of the best visibility determination algorithms for roguelike environments.

The reader is encouraged to read Björn's [article on LOS](fov_using_recursive_shadowcasting.md), as this text assumes that the reader knows the method described. This text offers a subtle improvement to the algorithm as well as pseudocode. The improvement is primarily one that allows a more elegant representation, it does not influence the efficiency of the algorithm.

To outline, Björn describes an improvement to Gordon Lipford's shadowcasting article [Computing LOS for Large Areas](computing_los_for_large_areas.md) by making use of recursive computation. Shadowcasting divides the Field of View (FOV) into eight sections and traces the rows or columns in the sections according to simple laws that ensure that a minimum of cells need to be investigated: Cells determined to be in shadow are skipped and cells are generally only visited once (with possible exception of cells on the boundaries of sections).

Björn proceeds to explain finer mechanics in considerable detail and with well drawn ASCII art. He omits to offer code in the article, but on-site resources offer an implementation that should suffice for many.

## Improvement

---

Each recursion in the algorithm described by Björn loops through rows/columns until a break condition is met (row/column ends with obstruction). This works because any regions closer to the starting point are already finished, and areas further away are (if appropriate) handled by recursive calls to the shadowcasting code.

For the sake of elegance this breaking condition can be handled differently: Instead of looping if the break-condition isn't met, it facilitates code readability to simply create a recursive call to the shadowcaster. The change does not influence things on an efficiency level, however it does clean up the code somewhat.

Partial pseudocode, describes one octant:

```text
 Scan(depth, startslope, endslope)

   init y
   init x

   while current_slope has not reached endslope do
     if (x,y) within visual range then
       if (x,y) blocked and prior not blocked then
         Scan(depth + 1, startslope, new_endslope)
       if (x,y) not blocked and prior blocked then
         new_startslope
       set (x,y) visible
     progress (x,y)

   regress (x,y)

   if depth < visual range and (x,y) not blocked
     Scan(depth + 1, startslope, endslope)
 end
```

## Improved Java Implementation

---

This Shadowcasting algorithm is from [SquidLib](squidlib.md) by [Eben Howard](eben_howard.md).

It features a shortened form of the standard recursive algorithm with easy to read notation.

The Direction.DIAGONALS that is referred to is a list of x and y offsets, which is all combinations of plus and minus 1.

The RadiusStrategy is an strategy pattern class that allows alternate radius calculations to be used. This allows for circular, square, diamond, or any custom formula for radius.

The class member variables should be straightforward, they're the this.foo items at the beginning.

```java
/**
* Calculates the Field Of View for the provided map from the given x, y
* coordinates. Returns a lightmap for a result where the values represent a
* percentage of fully lit.
*
* A value equal to or below 0 means that cell is not in the
* field of view, whereas a value equal to or above 1 means that cell is
* in the field of view.
*
* @param resistanceMap the grid of cells to calculate on where 0 is transparent and 1 is opaque
* @param startx the horizontal component of the starting location
* @param starty the vertical component of the starting location
* @param radius the maximum distance to draw the FOV
* @param radiusStrategy provides a means to calculate the radius as desired
* @return the computed light grid
*/
public float[][] calculateFOV(float[][] resistanceMap, int startx, int starty, float radius, RadiusStrategy rStrat) {
    this.startx = startx;
    this.starty = starty;
    this.radius = radius;
    this.rStrat = rStrat;
    this.resistanceMap = resistanceMap;

    width = resistanceMap.length;
    height = resistanceMap[0].length;
    lightMap = new float[width][height];

    lightMap[startx][starty] = force;//light the starting cell
    for (Direction d : Direction.DIAGONALS) {
        castLight(1, 1.0f, 0.0f, 0, d.deltaX, d.deltaY, 0);
        castLight(1, 1.0f, 0.0f, d.deltaX, 0, 0, d.deltaY);
    }

    return lightMap;
}

private void castLight(int row, float start, float end, int xx, int xy, int yx, int yy) {
    float newStart = 0.0f;
    if (start < end) {
        return;
    }
    boolean blocked = false;
    for (int distance = row; distance <= radius && !blocked; distance++) {
        int deltaY = -distance;
        for (int deltaX = -distance; deltaX <= 0; deltaX++) {
            int currentX = startx + deltaX * xx + deltaY * xy;
            int currentY = starty + deltaX * yx + deltaY * yy;
            float leftSlope = (deltaX - 0.5f) / (deltaY + 0.5f);
            float rightSlope = (deltaX + 0.5f) / (deltaY - 0.5f);

            if (!(currentX >= 0 && currentY >= 0 && currentX < this.width && currentY < this.height) || start < rightSlope) {
                continue;
            } else if (end > leftSlope) {
                break;
            }

            //check if it's within the lightable area and light if needed
            if (rStrat.radius(deltaX, deltaY) <= radius) {
                float bright = (float) (1 - (rStrat.radius(deltaX, deltaY) / radius));
                lightMap[currentX][currentY] = bright;
            }

            if (blocked) { //previous cell was a blocking one
                if (resistanceMap[currentX][currentY] >= 1) {//hit a wall
                    newStart = rightSlope;
                    continue;
                } else {
                    blocked = false;
                    start = newStart;
                }
            } else {
                if (resistanceMap[currentX][currentY] >= 1 && distance < radius) {//hit a wall within sight line
                    blocked = true;
                    castLight(distance + 1, start, leftSlope, xx, xy, yx, yy);
                    newStart = rightSlope;
                }
            }
        }
    }
}
```

## Pascal Implementation

---

The following code excerpt comes from my own implementation in [Turbo Pascal](pascal.md):

- mv -> global describing view distance
- mw -> mv \* mv

Octants are divided as follows:

```text
    \ 1 | 2 /
   8         3
   --       --
   7         4
    / 6 | 5 \
```

```pascal
function GetSlopeStd(x1, y1, x2, y2 : single) : single;
begin
  GetSlopeStd := (x1 - x2) / (y1 - y2);
end;

function GetSlopeInv(x1, y1, x2, y2 : single) : single;
begin
  GetSlopeInv := (y1 - y2) / (x1 - x2);
end;

function GetVisDistance(x1, y1, x2, y2 : integer) : integer;
begin
  GetVisDistance := (x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2);
end;

procedure RecursiveVisibility(e : PCreature; oct, depth : integer; slopeA, slopeB : single);
var
  x, y : integer;
begin
  case oct of
    1 : begin
      y := e^.y - depth;                                                { initialize y }
      x := round(e^.x - slopeA * depth);                                { initialize z }
      while GetSlopeStd(x, y, e^.x, e^.y) >= slopeB do begin            { while in octant }
        if GetVisDistance(x, y, e^.x, e^.y) <= mw then begin            { if within max visual range }
          if WorldSurface[x, y].entity^.obstruct then begin             { if obstruction }
            if not WorldSurface[x - 1, y].entity^.obstruct then begin   { if no prior obstruction }
              RecursiveVisibility(e, 1, depth + 1, slopeA, GetSlopeStd(x - 0.5, y + 0.5, e^.x, e^.y));
            end;                                                        { ^create recursive scan }
          end else begin                                                { no obstruction }
            if WorldSurface[x - 1, y].entity^.obstruct then begin       { if prior obstruction }
              slopeA := GetSlopeStd(x - 0.5, y - 0.5, e^.x, e^.y);      { adjust slope for later recursion }
            end;
          end;
          WorldSurface[x, y].visibility := 3;                           { set block visible }
        end;
        inc(x);
      end;
      dec(x)
    end;
    2 : begin
      y := e^.y - depth;                                                { initialize y }
      x := round(e^.x + slopeA * depth);                                { initialize z }
      while GetSlopeStd(x, y, e^.x, e^.y) <= slopeB do begin            { while in octant }
        if GetVisDistance(x, y, e^.x, e^.y) <= mw then begin            { if within max visual range }
          if WorldSurface[x, y].entity^.obstruct then begin             { if obstruction }
            if not WorldSurface[x + 1, y].entity^.obstruct then begin   { if no prior obstruction }
              RecursiveVisibility(e, 2, depth + 1, slopeA, GetSlopeStd(x + 0.5, y + 0.5, e^.x, e^.y));
            end;                                                        { ^create recursive scan }
          end else begin                                                { no obstruction }
            if WorldSurface[x + 1, y].entity^.obstruct then begin       { if prior obstruction }
              slopeA := -GetSlopeStd(x + 0.5, y - 0.5, e^.x, e^.y);     { adjust slope for later recursion }
            end;
          end;
          WorldSurface[x, y].visibility := 3;                           { set block visible }
        end;
        dec(x);
      end;
      inc(x)
    end;
    3 : begin
      x := e^.x + depth;                                                { initialize y }
      y := round(e^.y - slopeA * depth);                                { initialize z }
      while GetSlopeInv(x, y, e^.x, e^.y) <= slopeB do begin            { while in octant }
        if GetVisDistance(x, y, e^.x, e^.y) <= mw then begin            { if within max visual range }
          if WorldSurface[x, y].entity^.obstruct then begin             { if obstruction }
            if not WorldSurface[x, y - 1].entity^.obstruct then begin   { if no prior obstruction }
              RecursiveVisibility(e, 3, depth + 1, slopeA, GetSlopeInv(x - 0.5, y - 0.5, e^.x, e^.y));
            end;                                                        { ^create recursive scan }
          end else begin                                                { no obstruction }
            if WorldSurface[x, y - 1].entity^.obstruct then begin       { if prior obstruction }
              slopeA := -GetSlopeInv(x + 0.5, y - 0.5, e^.x, e^.y);     { adjust slope for later recursion }
            end;
          end;
          WorldSurface[x, y].visibility := 3;                           { set block visible }
        end;
        inc(y);
      end;
      dec(y)
    end;
    4 : begin
      x := e^.x + depth;                                                { initialize y }
      y := round(e^.y + slopeA * depth);                                { initialize z }
      while GetSlopeInv(x, y, e^.x, e^.y) >= slopeB do begin            { while in octant }
        if GetVisDistance(x, y, e^.x, e^.y) <= mw then begin            { if within max visual range }
          if WorldSurface[x, y].entity^.obstruct then begin             { if obstruction }
            if not WorldSurface[x, y + 1].entity^.obstruct then begin   { if no prior obstruction }
              RecursiveVisibility(e, 4, depth + 1, slopeA, GetSlopeInv(x - 0.5, y + 0.5, e^.x, e^.y));
            end;                                                        { ^create recursive scan }
          end else begin                                                { no obstruction }
            if WorldSurface[x, y + 1].entity^.obstruct then begin       { if prior obstruction }
              slopeA := GetSlopeInv(x + 0.5, y + 0.5, e^.x, e^.y);      { adjust slope for later recursion }
            end;
          end;
          WorldSurface[x, y].visibility := 3;                           { set block visible }
        end;
        dec(y);
      end;
      inc(y)
    end;
    5 : begin
      y := e^.y + depth;                                                { initialize y }
      x := round(e^.x + slopeA * depth);                                { initialize z }
      while GetSlopeStd(x, y, e^.x, e^.y) >= slopeB do begin            { while in octant }
        if GetVisDistance(x, y, e^.x, e^.y) <= mw then begin            { if within max visual range }
          if WorldSurface[x, y].entity^.obstruct then begin             { if obstruction }
            if not WorldSurface[x + 1, y].entity^.obstruct then begin   { if no prior obstruction }
              RecursiveVisibility(e, 5, depth + 1, slopeA, GetSlopeStd(x + 0.5, y - 0.5, e^.x, e^.y));
            end;                                                        { ^create recursive scan }
          end else begin                                                { no obstruction }
            if WorldSurface[x + 1, y].entity^.obstruct then begin       { if prior obstruction }
              slopeA := GetSlopeStd(x + 0.5, y + 0.5, e^.x, e^.y);      { adjust slope for later recursion }
            end;
          end;
          WorldSurface[x, y].visibility := 3;                           { set block visible }
        end;
        dec(x);
      end;
      inc(x)
    end;
    6 : begin
      y := e^.y + depth;                                                { initialize y }
      x := round(e^.x - slopeA * depth);                                { initialize z }
      while GetSlopeStd(x, y, e^.x, e^.y) <= slopeB do begin            { while in octant }
        if GetVisDistance(x, y, e^.x, e^.y) <= mw then begin            { if within max visual range }
          if WorldSurface[x, y].entity^.obstruct then begin             { if obstruction }
            if not WorldSurface[x - 1, y].entity^.obstruct then begin   { if no prior obstruction }
              RecursiveVisibility(e, 6, depth + 1, slopeA, GetSlopeStd(x - 0.5, y - 0.5, e^.x, e^.y));
            end;                                                        { ^create recursive scan }
          end else begin                                                { no obstruction }
            if WorldSurface[x - 1, y].entity^.obstruct then begin       { if prior obstruction }
              slopeA := -GetSlopeStd(x - 0.5, y + 0.5, e^.x, e^.y);     { adjust slope for later recursion }
            end;
          end;
          WorldSurface[x, y].visibility := 3;                           { set block visible }
        end;
        inc(x);
      end;
      dec(x)
    end;
    7 : begin
      x := e^.x - depth;                                                { initialize y }
      y := round(e^.y + slopeA * depth);                                { initialize z }
      while GetSlopeInv(x, y, e^.x, e^.y) <= slopeB do begin            { while in octant }
        if GetVisDistance(x, y, e^.x, e^.y) <= mw then begin            { if within max visual range }
          if WorldSurface[x, y].entity^.obstruct then begin             { if obstruction }
            if not WorldSurface[x, y + 1].entity^.obstruct then begin   { if no prior obstruction }
              RecursiveVisibility(e, 7, depth + 1, slopeA, GetSlopeInv(x + 0.5, y + 0.5, e^.x, e^.y));
            end;                                                        { ^create recursive scan }
          end else begin                                                { no obstruction }
            if WorldSurface[x, y + 1].entity^.obstruct then begin       { if prior obstruction }
              slopeA := -GetSlopeInv(x - 0.5, y + 0.5, e^.x, e^.y);     { adjust slope for later recursion }
            end;
          end;
          WorldSurface[x, y].visibility := 3;                           { set block visible }
        end;
        dec(y);
      end;
      inc(y)
    end;
    8 : begin
      x := e^.x - depth;                                                { initialize y }
      y := round(e^.y - slopeA * depth);                                { initialize z }
      while GetSlopeInv(x, y, e^.x, e^.y) >= slopeB do begin            { while in octant }
        if GetVisDistance(x, y, e^.x, e^.y) <= mw then begin            { if within max visual range }
          if WorldSurface[x, y].entity^.obstruct then begin             { if obstruction }
            if not WorldSurface[x, y - 1].entity^.obstruct then begin   { if no prior obstruction }
              RecursiveVisibility(e, 8, depth + 1, slopeA, GetSlopeInv(x + 0.5, y - 0.5, e^.x, e^.y));
            end;                                                        { ^create recursive scan }
          end else begin                                                { no obstruction }
            if WorldSurface[x, y - 1].entity^.obstruct then begin       { if prior obstruction }
              slopeA := GetSlopeInv(x - 0.5, y - 0.5, e^.x, e^.y);      { adjust slope for later recursion }
            end;
          end;
          WorldSurface[x, y].visibility := 3;                           { set block visible }
        end;
        inc(y);
      end;
      dec(y)
    end;
  end;

  if (depth < mv) and not WorldSurface[x, y].entity^.obstruct then begin   { break/continue }
    RecursiveVisibility(e, oct, depth + 1, slopeA, slopeB);
  end;
end;
```

## C# Implementation

---

A C# 3.0 implementation, including source code, of the above algorithm can be found here[[1]](http://www.evilscience.co.uk/?p=225).
