# Simple and accurate LOS function for BlitzMax

---

By Gbox

Setup: This fuction assumes you have Type (Or struct) For each map cell And your map is an array of this Type.

Example:

```text
Type tMapcell
    Field InLos '// This flag is set true if the player can see the cell.
    Field HasSeen '// This Flag is when the player has already seen this cell before.
    Field BlockVision '// This flag is set for things like walls and doors, otherwise its false.
End Type

Global Map:tMapCell[MAPWIDTH,MAPHEIGHT] '// the map
```

For this code example we'll assume a Type called tMonster. Monsters in the dungeon are of this type and so is the player.

Example:

```text
Type tMonster
    Field X
    Field Y
    Field Vision '// this is how many tiles can be seen in any direction from center
End Type
```

This Method is Not the fastest, but it's easy To implement And work very well. I dont detect any kind of performance decrease when using this function. It works by drawing a filled circle 360 deg around the player And marking map cells that are in LOS. There are more than 360 Steps in the circle because just using 360 would Create gaps in the vision at a distance. the fractional amount gives better resolution. [0.1 causes LOS To sometimes seep though doors, >= 0.2 creates gaps. 0.18 seems the sweet spot. The Code is in BLitzMax (a derivitive of Basic made For games. [www.blitzbasic.com]) but should be easy To translate.

The code:

```text
Function UpdateLOS( m:tMonster )
    If m = Null Then RuntimeError("Null in LOScheck!")
    Local XX:Int, YY:Int
    '//
    '// Loop through the entire map resetting the LOS flag
    '//
    For xx = 0 To MAPWIDTH - 1
        For yy = 0 To MAPHEIGHT - 1
            If Map[xx, yy].InLOS = True Then
                Map[xx, yy].HasSeen = True '// Mark the cell as previously seen [drawn in dark colors]
                Map[xx, yy].InLOS = False
            EndIf
        Next
    Next

    For Local angle:Float = 1 To 360 Step 0.18
        Local dist:Int = 0
        Local x:Float = Float(m.X) + 0.5 '// Add 0.5 to simulate looking from the center of the tile.
        Local y:Float = Float(m.y) + 0.5
        Local xmove:Float = Cos(angle)
        Local ymove:Float = Sin(angle)

        Repeat
            x = x + xmove
            y = y + ymove
            dist = dist + 1
            If dist >= m.Vision Then Exit
            If X >= MAPWIDTH Then Exit
            If y >= MAPHEIGHT Then Exit
            If x < 0 Then Exit
            If y < 0 Then Exit
            Map[x, y].InLOS = True
            If Map[x, y].BlockVision Then Exit
        Forever
    Next
End Function
```
