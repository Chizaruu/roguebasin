# Implicit Facing

---

## Introduction

---

Several games have used systems which represent the player's facing, and many more have rejected it. The standard way of handling facing gives the player an annoying extra element to manage, and so has been considered unworkable. This article proposes an alternate use for facing, which does not burden the player with additional details. It is best implemented as a replacement for the "defense penalty for multiple attackers" rule.  

## Rules

---

The player's facing is determined by his previous move, and by the position of monsters around him, as follows (rules applied in order):  

* if the last move was to fire an arrow, zap a wand, throw a projectile, etc., then the player is facing in the direction he fired/zapped/threw in.
* Else if there is exactly one visible monster next to the player, the player faces towards it. Justification: Regardless of the direction of movement, the player is aware of and defending against attacks.
* Else if the last attack was to move or attack, facing is in the direction given by the move/attack.
* Else facing is the same as it was last move.

Facing does NOT affect vision, movement speed, or any non-combat actions. The current facing should not be featured prominently on the HUD, or anywhere else that will make it obvious that a facing system is in use.  

## Melee

---

On a given turn, the tiles around the player are divided into frontal, flank, and rear tiles (see Fig. 1). If an attacking melee monster is in a flank or rear tile, the player does not receive defensive benefits from a shield. (Variation: one flank receives shield benefits, depending on which hand holds the shield.) Furthermore, if the attacker is in a flank tile it receives a small (system-dependent) to-hit bonus, while if it is in a rear tile it receives a substantial bonus.  

```text
Facing @         Facing NE
321  1: Front    211
3@1  2: Flank    3@1
321  3: Rear     332

Fig. 1 - Melee
```

## Missile

---

A ranged attacker receives the benefit of a flank or ranged attack depending on which quadrant (see Fig. 2) it is in.  

```text
Facing E   Facing NE
 \222/      22|11
 3\2/1      22|11  1: Front
 33@11      --@--  2: Flank
 3/2\1      33|22  3: Rear
 /222\      33|22

Fig. 2 - Missile
```

Quadrant borders are never in region 2. Assuming a bresenham line draw from attacker to player represents the path of the projectile, this is approximately equivalent to representing the projectile as a melee attack when it is one step away from the player.  

## A More Formal Definition

---

So long as we restrict ranged attacks to firing on straight or diagonal paths, we can represent facing as an integer in the range of 0 to 7, going clockwise at 45-degree increments; to determine what region an attacker is in, we find the facing which would be towards it, subtract the player's facing modulo 8, and take the absolute value; if the result is <= 1, it is a frontal attack; else if <=2, flank attack; else <=4, rear.  

If we do not make this restriction, then we must allow both for the player to face in arbitrary angles, and accurately represent the angle of origin for ranged attacks. The angle from the player to an attacker is atan2(monst.y- plr.y, monst.x-plr.x) radians. As before, we take attack abs(angle - player facing % (2*pi)). (There is some complication this time with using modulus, since C does not support use of the modulus operator on floats.) This is frontal if result <= pi/4, flank if <= 3*pi/4, else <= pi, rear. As a workaround for the lack of modulus on floats, you can use the C function below instead.  

```text
float mod(float a, float b) {
    if(a>0) return floor(a/b) * b;
    else    return -floor(-a/b) * b;
}
```

## More on Missiles

---

One of the goals of implicit facing is to not allow the player to control his facing directly, and to make it so that he doesn't need to. A problem case is when the player moves, with no monsters adjacent, but with missile attackers around. If the character is aware of their presence, it makes sense for him to consciously face towards them, but this is not what actually happens with the rules given. The situation is even worse when there are *multiple* missile attackers around. Certainly, it is tempting to ignore the case and expect that the player be aware of the facing rules, but this is not the best solution.  

One possibility is to divide up the angles around the player into regions, and face towards the region which contains the most known in-range missile attackers. The regions should be overlapping, and should not be too large (for accuracy) or small (for speed); 60 degree regions with 20 degree overlap should work nicely.  

Another possibility is to simply face towards the nearest monster. Or, facing could be left undefined until one of the monsters attacks, and set to be towards that monster. Unfortunately, this means that the specific order of missile attacks becomes relevant, which is not particularly desirable.
