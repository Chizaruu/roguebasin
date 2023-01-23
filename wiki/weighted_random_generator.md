# Weighted random generator

---

by Wire_Hall_Medic

## Introduction

---

With their significant level of randomly-generated content, it is very helpful for a Roguelike to have a single method of managing it all. When each option is equally likely, this is simple enough. But when different results have different probabilities, something a little more complex is called for.

## Synopsis

---

A weighted random generator works by accepting a list of possible results, each with a weight, a minimum level, and a maximum level. Then, it copies elements of the list of possibles who's level requirements are met by the passed level. Every time it copies one of these elements over, it adds the weight of the element to a total.

With the list complete, the generator picks a number between 0 and 1, and multiplies it by the total weight (we'll call this "the roll").

The generator then begins running down the second list. At each entry, if the roll is less than the current element's weight, the generator returns the element. Otherwise, the generator subtracts the current element's weight, and goes to the next entry.

Essentially, this mimics the effect of constructing a large table, as in table-top RPGs, and then rolling on it; this, however, is much cheaper computationally.

Note that this generator only selects the item; it does not actually create it. That is left to an object factory, so that the generator can be used for more than one type of creation.

## Weight

---

A key concept to this algorithm is that of weight. Weight represents how often the element will be selected, relative to other weights. So if every item has the same weight (regardless of whether that's 1 or 1,000,000), they will have an equal chance of being generated.

This allows the programmer to easily fine-tune frequency, and drop in new items without re-coding a whole chart. Also, it allows easy generation ratios. For example, suppose your dungeon has thirty levels, and you weight healing potions like so:

```text
  SMALL_HEALING_POTION  (minLevel = 0,  maxLevel = 10, weight = 8);
  MEDIUM_HEALING_POTION (minLevel = 0,  maxLevel = 20, weight = 4);
  LARGE_HEALING_POTION  (minLevel = 11, maxLevel = 30, weight = 2);
  HUGE_HEALING_POTION   (minLevel = 21, maxLevel = 40, weight = 1);
```

No matter which level the hero is on, there are two possible healing potions, with the larger having half the chance of occurrence as the smaller.

## Inheritance

---

In programming the generator, it is highly recommended to use inheritance (if your language of choice supports multiple inheritance), or something that fakes it (such as Java's interfaces) on constants (enumerators are ideal). The reason for this is that you don't have to create any objects for the generator, nor adapt anything to fit. This generator is designed to select an entry from a list, which then would be handed to the proper object factory to actually create the selection. Keeping the two (selection and creation) separate allows you to use the generator for more than just item generation, or monster selection, or map type selection, or what ever the generator is tied to.

## Programming Example

---

Here is the author's implementation, in Java.

First, the generator interface, which is its own class:

```java
public interface RandomlyGenerated
{
   public int getMinLevel();
   public int getMaxLevel();
   public int getWeight();

}
```

Next, the actual generator:

```java
public class RandomGenerator
{
   public static RandomlyGenerated generate(RandomlyGenerated[] list, int level)
   {
     // declare variables
      int maxRoll = 0;
      int roll;
      List<RandomlyGenerated> table = new ArrayList<RandomlyGenerated>();

      // populate table and determine max roll
      for(RandomlyGenerated item : list)
      {
         if(item.getMinLevel() <= level &&
            item.getMaxLevel() >= level)
         {
            table.add(item);
            maxRoll += item.getWeight();
         }
      }

      // early exit if no possible entries
      if(table.size() == 0)
         return null;

      roll = (int)(Math.random() * maxRoll);

      for(RandomlyGenerated item : table)
      {
         // return element if roll < weight
         if(roll < item.getWeight())
            return item;

         // otherwise, subtract weight before moving on
         roll -= item.getWeight();
      }

      return null;
   }
}
```

Some example objects to feed the generator:

```java
  public enum ItemType implements RandomlyGenerated
  {
     GOLD              (100),
     POTION            (25),
     EQUIPPABLE        (20),
     BOOST             (10);

     private int weight;

     public int getMinLevel(){return 0;}
     public int getMaxLevel(){return 1000;}
     public int getWeight(){return weight;}

     private ItemType(int w)
     {
        weight = w;
     }
  }


  public enum PotionType implements RandomlyGenerated
  {
     LIGHT_HEALING     (0,  20,  100),
     MEDIUM_HEALING    (10, 30,  50),
     STRONG_HEALING    (20, 100, 25),
     LIGHT_MANA        (0,  20,  100),
     MEDIUM_MANA       (10, 30,  50),
     STRONG_MANA       (20, 100, 25);


     private int minLevel;
     private int maxLevel;
     private int weight;

     public int getMinLevel(){return minLevel;}
     public int getMaxLevel(){return maxLevel;}
     public int getWeight(){return weight;}

     private PotionType(int minL, int maxL, int w)
     {
        minLevel = minL;
        maxLevel = maxL;
        weight = w;
     }
  }

  // etc for equippables and boosts
```

Note that, as all items types are available at all levels, the aren't given individual level ranges, instead returning a range the player will never be outside of.

Putting it all together:

```java
public static LootObject generateLoot(int level)
{
  switch((ItemType)RandomGenerator.generate(ItemType.values(), level))
  {
   case GOLD : return GoldGenerator.generate(level);

    case POTION : return PotionGenerator.generate(
     RandomGenerator.generate(
          PotionType.values(), level),
     level);

   case EQUIPPABLE : return EquippableGenerator.generate(
     RandomGenerator.generate(
          EquippableType.values(), level),
     level);

   case BOOST : return BoostGenerator.generate(
     RandomGenerator.generate(
          BoostType.values(), level),
     level);
  }
  return null;
}
```

## Credit

---

The author first saw this method in use in the game [DoomRL](doomrl.md), explained [here](http://doom.chaosforge.org/wiki/Monster_Generation).
