# Programming Roguelike Magic

---

## A typical first-time approach

---

Magic is an important part of most roguelikes, in one way or another. Partly due to adding some glitz and atmosphere to the game, and partly due to the added tactical options and diversity. It was also the one that I had most difficulty in programming when I tried to make my first roguelike.

One method of creating spells is to hard-code all the spells into separate functions, and select the correct function from a gigantic switch statement. My first try was something along these lines:

```c
switch (spell_num)
{
  case SPELL_FIREBALL:
    cast_fireball(y, x);
  break;
  case SPELL_MAGICMISSILE:
    cast_magicmissile(y, x);
  break;
  case SPELL_HEALSELF:
    cast_healself();
  break;
}
```

But once I started adding more spells, a lot of code needed to be unnecessarily duplicated. For example, a magic missile spell and a icebolt spell do not really differ very much, and neither do a fireball and a firebolt.

By analyzing the different spells that you want to deal with in your game and deciding upon some properties that a lot of spells will share the work can be greatly simplified.

## Properties of a spell

---

The basic properties of spells that I decided upon were the effect of the spell (heal, teleport, fire, cold), the area that it acts on (unit, beam, ball, level) and the possible targets of the spell (the square the player is on, any square adjacent to the player, any square on the level).

```c
typedef struct
{
  int (*ef)(int, int);
  int area;
  int target;
}
spell_s;
```

Wait, what is the first variable in the struct ? It's a function pointer, a very powerful feature of C. Just like regular pointers point to the memory location of a variable, function pointers point to the adress of a function. Any function that matches the prototype of the pointer (return int, accept 2 ints as arguments in this case) can be assigned to the pointer, and called from it. Well that's neat, but how does this help us make a better magic system ?

The logical way to use the properties that we have is to go through them one by one. First look at the target variable and prompt the user for the coordinates of the target. If the target is any adjacent square, we can just prompt for a direction and receive the x and y coordinates. For a target of the square the player is on we need to do even less.

Next, look at the area variable and find out all the squares that will be affected by the spell. For a ball spell a ball of a certain radius around the target coordinates, for a level spell all the squares on the level.

Finally, look at the effect variable and apply the effect to all the squares affected. And naturally to any players, monsters or items that are residing in the particular squares too.

## An example

---

The last two stages are easier to do parallelly, by finding one square to be affected, and applying the spell to it. Here is a small example of the concept:

First, lets initialize a couple of spells:

```c
int spell_init(void)
{
  /* a heal-player spell */
  /* The function pointer can be assigned to using the name of the
     function without the parenthesis at the end*/
  spells[0].ef = spell_effect_heal; 	       /* Correct way */
  /* spells[0].ef = spell_effect_heal(); */     /* Wrong way */
  spells[0].area = AREA_SQUARE;
  spells[0].target = TARGET_SELF;

  /* a fireball spell */
  spells[1].ef = spell_effect_fire;
  spells[1].area = AREA_BIGSQUARE;   /* Well, it's almost a ball ;) */
  spells[1].target = TARGET_VISIBLE;

  return 0;
}
```

And of course we'll need the functions that are being pointed to:

```c
int spell_effect_heal(int y, int x)
{
  monster_s* monster;

  /* Heal the player first */
  if (player_at(y, x) )
    player.hp += 10;

  /* If there is a monster in the square, heal it too */
  monster = monster_at(y, x);
  if (monster != NULL)
    monster->hp += 10;

  return 0;
}

int spell_effect_fire(int y, int x)
{
  monster_s* monster;

  /* Do some visual effects to the square, to make our fireball look
     more spectacular */
  draw_character(y, x, '*', RED);

  if (player_at(y, x) && player.fireresistance == 0)
    player.hp -= 10;

  monster = monster_at(y, x);
  if (monster != NULL)
    monster->hp -= 10;

  return 0;
}
```

And of course a function for actually casting the spells:

```c
int spell_do(spell_s* sp)
{
  int y, x, y2, x2;

  /* Acquire a target for the spell */

  switch (sp->target)
  {
    case TARGET_VISIBLE:
      get_visible_target_coordinates(&y, &x);
    break;
    case TARGET_ADJACENT:
      get_adjacent_target_coordinates(&y, &x);
    break;
    case TARGET_SELF:
      y = player.y;  /* Y-coordinate of the player */
      x = player.x;  /* X-coordinate of the player */
    break;
  }

  /* Apply the spell to an area */

  switch (sp->area)
  {
    /* Just one square */
    case AREA_SQUARE:
      sp->ef(y, x);
    break;
    /* Lots of squares */
    case AREA_BIGSQUARE:
      for (y2 = -3; y2 <= 3; y2++)
        for (x2 = -3; x2 <= 3; x2++)
          sp->ef(y+y2, x+x2);
      break;
  }
  return 0;
}
```

Some nice things came out of this approach. Adding new spells that share properties with the old ones is now very easy. In my own game there are no static spells, the player can mix the different properties freely to create custom-made spells for any situation. Using function pointers also helps to avoid cut-and-paste programming.

## Projects to continue with

---

Beyond adding weird areas of effect or new types of damage there are a few things that you can work with to make your roguelike meet the standards set by earlier games.

Obviously some improvements really need to be made into this system. Some method of telling the spell_effect functions a power level for the spell. Another parameter that should be added is some concept of range. It is constraining to have all ball-spells have a radius of three squares, or beam-spells to reach 5 squares all the time.

The current method of supplying coordinates of the squares to be affected is too constraining. For example you would often want to apply spells into objects in your inventory or equipment slots. One method is adding a new targeting method (TARGET*INVENTORY) returning an item from the inventory and a new area of effect (AREA_INVENTORY) that calls the spell_effect*\* function with some extra parameters. Another approach is making a separate hierarchy for spells that affect inventory items, one for those affecting objects on the main map, and new hierarchies for other needs that arise.

Once you have spells it should be easy to add magical items like potions, scrolls and wands, as well as adding spell activations to weapons and armor.

And the biggest challenge that you will face is in all propability designing a balanced, interesting and maybe even remotely original set of spells.

If you disagree vehemently with something I said in this article, please let me know at jsnell@lyseo.edu.ouka.fi. If you think that the article was the most brilliant piece of writing ever, you really need to let me know. I could use the encouragement :-)

_Written by Juho Snellman._
