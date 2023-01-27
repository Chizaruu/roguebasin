# Double layer cave generator

---

This [dungeon](dungeon.md) generator is mainly based on [Cellular Automata Method](cellular_automata_method_for_generating_random_cave-like_levels.md), so I suggest to read that article first. With my generator modification, you will generate absolutely random caves, and there will not be any unreachable disconnected space.

## Algorithm

---

In words there are only 3 (or 4, if you want less open space) steps:

- Generate one map by Cellular Automata method.
- Generate another map with that method.
- Fuse both maps: Check both map arrays. If the first map's square is "wall", and the second map's is "wall", then invert and make it a "stepable" square. Otherwise, do not invert and keep it a "wall". If the first map's square is "stepable" and the second map's square is too, then invert and make it a "wall", else keep it a "stepable square".
- (_Optional step_) Generate random walls: I.e. if there are 3 or less "walls" in a 3 square radius around another square, make that square also a "wall". Or something like that.

Also, here is dirty(we can join some loops) code in [Pascal](pascal.md) language:

```pascal
  program Map_Gen;
   uses crt;
   var x, y: array [1..116,1..50,1..5] of char;
   xx: array[1..116,1..50] of integer;
   i, j, q, qq, ii, jj, ax, ay, k:integer;
begin
   Randomize;

   for i:=1 to 50 do
       for j:=1 to 116 do begin
           If (i=1) or (i=50) or (j=1) or (j=116) then xx[j,i]:=-1
           else xx[j,i]:=0;
       end;

   for i:=1 to 50 do
       for j:=1 to 116 do begin
           If (i=1) or (i=50) or (j=1) or (j=116) then begin
               x[j,i,1]:='#';
               x[j,i,5]:='#';
               y[j,i,1]:='#';
           end
           else begin
               q:=random(100);
               if q<45 then x[j,i,1]:='#'
               else x[j,i,1]:='.';
               qq:=random(100);
               if qq<45 then y[j,i,1]:='#'
               else y[j,i,1]:='.';
           end;
       end;

   for k:=2 to 5 do begin
       TextColor(k);
       for i:=2 to 49 do begin
           for j:=2 to 115 do begin
               ax:=0;
               ay:=0;
               for ii:=(i-1) to (i+1) do begin
                   for jj:=(j-1) to (j+1) do begin
                       If (x[jj,ii,k-1]='#') then ax:=ax+1;
                       If (y[jj,ii,k-1]='#') then ay:=ay+1;
                   end;
               end;
               If ((x[j,i,k-1]='#') and (ax>3)) or (ax>4) then begin
                   x[j,i,k]:='#';
               end
               else begin
                   x[j,i,k]:='.';
               end;
               If ((y[j,i,k-1]='#') and (ay>3)) or (ay>4) then begin
                   y[j,i,k]:='#';
               end
               else begin
                   y[j,i,k-1]:='.';
               end;
           end;
       end;
   end;

   for i:=2 to 49 do begin
       for j:=2 to 115 do begin
           Case x[j,i,5] of
           '#':    if x[j,i,5]=y[j,i,5] then begin
                       x[j,i,5]:='.';
                       xx[j,i]:=0;
                   end
                   else begin
                       x[j,i,5]:='#';
                       xx[j,i]:=-1;
                   end;
           '.':    if x[j,i,5]=y[j,i,5] then begin
                       x[j,i,5]:='#';
                       xx[j,i]:=-1;
                   end
                   else begin
                       x[j,i,5]:='.';
                       xx[j,i]:=0;
                   end;
           end;
       end;
   end;

   for i:=22 to 28 do
       for j:=55 to 61 do begin
           x[j,i,5]:='.';
           xx[j,i]:=0;
       end;

   xx[58,25]:=1;

   for k:=1 to 50 do
       for i:=2 to 49 do
           for j:=2 to 115 do begin
               if xx[j,i]=k then begin
                   for ii:=(i-1) to (i+1) do begin
                       for jj:=(j-1) to (j+1) do begin
                           if (xx[jj,ii]=0) then xx[jj,ii]:=k+1;
                       end;
                   end;
               end;
           end;

   for i:=2 to 49 do
       for j:=2 to 115 do begin
           if (xx[j,i]=0) and (x[j,i,5]='.') then begin
           x[j,i,5]:='#';
           end;
       end;

   for i:=1 to 50 do
       for j:=1 to 116 do begin
           GoToXY(j,i);
           Write(x[j,i,5]);
       end;
   Readln;
end.
```
