# Thoughts on Combat Models

---

As stated on other pages, combat is an integral part of most Roguelikes (and in fact, most RPGs). Here are some thoughts on possible models for combat. (This may need some cleaning up by someone that's better at it than me.)

## In general

---

All combat systems boil down to just two basic questions:

- What chance do I have to hit the enemy?
- How much damage do I do?

Many systems will also deal with a third, less basic, question:

- What else happens to the enemy?

## In specific

For the purposes of discussion, let's assume that the entire combat system consists of three numbers: ATK, DEF and HP. Each combatant has different values for each of these. With such simplicity, you might think that there aren't many different ways the above questions (ignoring the third for now) can be answered. Below is a (non-exhaustive) list of possibilities. In all of the examples, rnd() returns a [random number](random_number_generator.md) between 0 and 1. (For example, rnd()+rnd() would be a number between 0 and 2, which is most likely near 1; while 2\*rnd() would re a number evenly distributed between 0 and 2.) I'll use C-style syntax to represent the examples.

Feel free to expand this list and discuss the pros and cons for each (which can be listed below). Some of the ideas came from [this discussion](http://gmc.yoyogames.com/index.php?showtopic=293496).

### Determining whether you hit

#### Possibility #1

```text
You always hit.
```

Variant: hitting is random, but your chance to hit is constant.

Corollary: the damage calculation will be your main focus when balancing.

#### Possibility #2

```text
Hit if rnd() < ATK / (some maximum ATK).
```

Corollary: DEF should figure heavily in the damage calculation if you want to keep ATK and DEF balanced. There are diminishing returns on increasing ATK, since the difference between 90% and 95% is fairly trivial. This possibility is somewhat outside of the scope of this exercise, because it assumes an additional stat: maxATK, which would likely be global to the game.

#### Possibility #3

```text
Hit if ATK > DEF.
```

Variant: randomize ATK, DEF, or both. (i.e., hit if rnd()*ATK > rnd()*DEF)

Corollary: In the non-randomized version, your players will be unable to affect some opponents, which implies that ATK is more important than DEF for dungeon-diving. (You never want to have an invincible opponent, after all.)

#### Possibility #4

```text
Hit if rnd() < ATK / (ATK + DEF).
```

(Equivalently, hit if rnd() > DEF / (ATK + DEF).)

Corollary: Like #2 above, there are diminishing returns for increased ATK, but like #3 above, players will always want a good ATK.

### Determining Damage

Not all damage methods are compatible with all attack methods; use common sense.

#### Possiblity #1

```text
Constant damage: 1HP.
```

If you use some other damage number, then your effective HP is equal to your real HP divided by the damage. (i.e., how many hits you can take.)

#### Possibility #2

```text
Damage = ATK
```

Variant: damage = rnd()\*ATK

#### Possibility #3

```text
Damage = ATK - DEF
```

Variant: damage = rnd() \* (ATK - DEF)

#### Possibility #4

```text
Damage is proportional to the degree of success of the attack.
```

This will have different definitions depending on the method used to attack. In attack possibility #3, this is equivalent to damage possibility #3. For attack possibility #4, the "degree of success" will always be less than 1, so you will need to scale this number somehow (possibly with HP, as in possibility #6 below).

#### Possibility #5

```text
Damage = ATK/DEF.
```

Corollary: For best results, your ATK and DEF should be balanced; a 3A/3D player will beat either a 5A/1D or a 1A/5D defender.

#### Possiblity #6

```text
Damage = Attacker.HP
```

Corollary: As any of the above, but scale damage by HP; a damaged (or weak) attacker does less damage.

#### Possibility #7

```text
Kill the defender.
```

Variant: if your degree of success is sufficiently larger than the defender's HP, kill them (say, ATK - DEF > HP). This variant screams out for some degree of randomization, or else two roughly-equally-matched opponents will never hurt each other.

Corollary: This approach assumes some kind of stealthy/distance-managing game, or it is combined with one of the randomized or ATK > DEF methods for hitting.

### A few thoughts on combining attack & defense methods

A particular statistic is more important the more it shows up in your combined attack & damage equations. If your chance to hit depends only on ATK, and your damage depends on both ATK and DEF, then ATK is much more important.

## Question 3

---

The question of secondary effects can be dealt with is several ways as well.

- Each attack could be limited to one effect or could have the possibility of multiple effects
- Resistance can be handled in several ways
- The list of effects itself can be big or small

## Other thoughts

---

By limiting the system to just three variables (plus some random numbers) it makes things very simple, both to design and to balance. Such simplicity doesn't have to be a major limitation though. For example, imagine you used the list of D&D attributes (STR, DEX, CON, INT, WIS, CHA). You can reduce these to ATK, DEF and HP and with more variables, you can mix it up a little more. A fighter-type with a light melee weapon might use something like HP = CON + DEX/2; ATK = STR + DEX/2 + WIS/2; DEF = DEX + CON/2 + WIS/2;. Other classes and types of equipment would use different equations.

Still, three basic variables can be limiting. By adding just one more, say ACC or DEX, you can make the hit chance more meaningful and the equipment more balanced. With only three variables, weapons would simply increase ATK and armor would increase DEF. With something like DEX, heavier weapons and armor could decrease DEX in addition to increasing ATK and DEF, which would make the player decide between hit chance/dodge rate and damage/defence.

Also, by starting simple and adding complexity slowly, it can (sometimes) be easier to balance the system (assuming that adding complexity doesn't break the system).
