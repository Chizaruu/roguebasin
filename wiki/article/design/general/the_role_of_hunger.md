# The Role of Hunger

---

```text
"Do you have hunger in your roguelike?"

"Not yet."

"Excellent!  I hate that!"
```

The dislike of starving to death in roguelikes seems, in my experience, to dwarf the dislike of permanent death. And yet roguelike authors continue to rush to implement starvation. The following is a collection of thoughts on hunger, largely derived from discussions I have read roguelikes I have played. I look forward to any expansion people can make.  

## Benefits of Hunger

---

### Hunger as a Resource

Hunger provides another dimension of resource management. Like Hitpoints, Magic points, and Gold, Satiation is another resource that must be managed. Since many roguelikes are largely about balancing resources, removing hunger from the equation would make it easier for the player to min/max, and thus less challenging, and less fun.  

**Examples**  

* A ring of sustenance uses up a ring slot which the player could use for another purpose.
* Casting spells consumes hunger, thus having a long term (hunger) and short term (magic point) cost.
* Food rations take space/weight in inventory, leading to more careful packing and/or stashing.  

### Hunger as a Time Limit

The inexorable tick of the Hungry time bomb provides a sort of urgency to the adventure. You must delve deeper searching for food rations. You can't just lock yourself in a room for 2000 turns to heal/study/etc.  

Many aspects of Roguelikes encourage safe and careful play. Hunger is often listed as a drive for action rather than inaction.  

**Examples**  

* Cutting trees in [ADOM](../../../game/adom.md) would be a nearly free action if not for going from "Full" to "Hungry" in a single user-turn.
* In the early [NetHack](../../../game/nethack.md) game, I personally tend to dive quickly if I'm running low on food in hopes of getting a yummy corpse. (Note: This is not directly encouraged as some note that one can always use a hunger prayer instead.)
* [Cogmind](../../../game/cogmind.md), though it does not use hunger, uses the lack of healing to similar extent. The player may only heal after they reach a new level, limiting the time they have to explore before suffering the consequences of attrition.  

### Hunger as Realism

We have to eat in real life, right? Of course, we have to breathe in real life, and few would suggest a "breathe" command be added to roguelikes. (It would make an interesting death message though - "forgot to breathe") Hunger could always be fully abstracted. It could be assumed the adventurer is foraging and eating, much as it is assumed the adventurer is sleeping regularly in most roguelikes.

I don't consider this much of a benefit, and merely leave it here for completeness.  

## Uses of Eating

---

What use is eating? Why have eating at all in the game? Another way of looking at this, is why would a player want to eat in your roguelike?  

### Prevent Death

This is the most commonly implemented use. And, in my opinion, is the source of most of the aggravation with Hunger in Roguelikes. No food for long enough implies death.  

This use strengthens hunger as a resource, as any resource which causes death on expiry (such as hitpoints) tends to be more carefully managed. However, Magic Points are another carefully managed resource, and usually hitting 0 MP doesn't directly result in death. Thus, to gain hunger's advantages as a resource, one must merely ensure being Hungry causes sufficient negative properties that one will be encouraged to manage it.  

B2 is more susceptible to the removal of the death penalty. Hunger-as-timelimit is well displayed by Ultima III. In Ultima III, your party members each had a Food counter. Each action decremented that counter. If it hit 0, the character would take damage until death. You had a very good idea about what your time-to-live was by the counter. On the other hand, if Hunger has only non-fatal effects, say preventing magic regeneration, there is no sense of time-to-live. One could let one's hunger bottom out, spend an arbitrary amount of time goofing around, and then have a couple cram rations to resatiate oneself. (Removing the floor on hunger may avoid this, but seems like an artificial fix.)  

### Allow Healing

Often eating is implemented as a form of instant-healing. A sandwich may give +5HP & +5MP. One's hunger level is more of a satiation level, and it is ironically better to be hungry than full as one can then take advantage of food. This, however, turns food into YAWTH (Yet another way to heal), as there are plenty of insta-heal methods in the Roguelike genre already.  

This system does not provide hunger as a resource nor as a time limit.  

### Allow Regeneration

The natural regeneration of hit points and magic points can be tied to the hunger level. This keeps food as a method-of-healing, but it doesn't change an apple into a different looking potion of healing.  

This system only weakly drives hunger as a resource and time limit. Regeneration is nice (one gets hp/mp while wandering around, rather than having to consume turns and items), but can be ignored by uninjured players and high level players. Thus, one may find people only eating before major fights and falling to "Starving!" whilst moving stuff between their caches.  

### Allow Special Actions

Actions, such as SpellCasting, or going Berserk, may be limited to those who are not hungry. This has a strong resource component. The strength of this feature as a time limit depends on how pervasive the requirement is. (If I can research spells while "Starving!", the hunger system does not contribute to the balancing of the time taking to research spells.)  

### Gain Intrinsics

You are what you eat. This benefit for eating is similar to allowing healing, except usually is not just a clone of another existent process. It has serious balance issues, however. Being able to gain an intrinsic means not having to use an item slot to fulfill that intrinsic. One must be careful not to nerf rings of fire resistance (as I personally think they have been in [NetHack](../../../game/nethack.md)). There are multiple solutions to this:  

* Balance with it in mind.
* Have different levels of resistance. Thus, maximum resistance requires the item + the eaten intrinsic.
* Only grant temporary resistances through eating.  

### Add Atmosphere

Being able to bake a cake adds to the sense of the completeness of the world. One should be cautioned that the powergamers will immediately clamour for it to be "useful."  

## Implementation Details

---

What are things to keep in mind when implementing hunger?  

### Lower Limit

Even when using hunger as a resource, this is not easily answered. Ultima III, for example, does bottom out at 0 food, so as soon as you buy more food you stop starving. However, they could have tracked arbitrarily large negative food.  

By not letting hunger bottom out, one can strengthen hunger as a time limit. While one may research that spell while "Starving!", one can't then just have a slice of pizza and be back at 100%, you have to eat yourself out of a very deep hole. I think such a system would have serious representational problems.  

### Upper Limit

For either healing or for intrinsics, I think this is mandatory. For the other cases, it is entirely optional. Note that removing a maximum cap removes the inventory management aspect of food. The other portions of food as a resource remain, of course.  

Note that if one can eat corpses, having no limit lets you stock up from a room of kills. Most extant roguelikes require you to kill-when-hungry.  

A variant of this used by [Crawl](../../../game/linleys_dungeon_crawl.md) is to have some food stuffs that you can only eat when hungry. Most characters refuse to eat raw meat unless they are already hungry, even though they'd be quite willing to eat an apple.  

### Change Over Time

Hunger can increase in several different ways. One or more of:  

* **Time.** Every game tick, you grow hungrier.
* **Turns.** Every time you do an action, you hunger (possibly relating to the strenuousness of the action).
* **Regeneration.** When you regenerate due to U3, you hunger.
* **Special Abilities.** When you use special abilities due you hunger.
* **Intrinsics.** Possession of certain items/intrinsics/whatever which are too strong can be balanced with hunger. (e.g. ring of conflict in [NetHack](../../../game/nethack.md), being invisible in [ADOM](../../../game/adom.md), etc.)  

### Eating as an Action

In aforementioned Ultima III, one never has to "eat" something - it is done automatically. Eating can quickly become a micro management issue. However, if one wants intrinsics, the game had better have explicit eating.  

### Interface

This comes down to a question of granularity:  

* **Numeric Value:** This strongly reinforces hunger's use as a time limit. One sees the ticking of the clock in front of one's face.
* **Hunger Bar:** Simpler to reference, feels less fiction-breaking than the numeric value, but still allows one to watch the slow ticking.
* **Hunger Status Line:** "Hungry" type status line entries. Hides the time limit aspect of hunger. Often results in newbies being surprised and starving.
* **Invisible:** Best for atmospheric systems or those in which hunger is only used to heal. Definitely not suggested for hunger as a means to prevent death. This avoids spamming the user with something they may not care about. It will downplay the role of food in your game. Ultima Online, for example, used this system.  

## Hunger Hatred

Why do people hate hunger?  

### Micromanagement

If no non-newbies should ever starve to death, then the hunger system is just requiring the player to periodically perform extra keypresses. If the player doesn't find these key presses interesting, it seems like meaningless micromanagement.  

Some roguelikes (for example [NetHack](../../../game/nethack.md)) have magical items that eliminate or greatly reduce food consumption. Such an item allows the benefits of a hunger system, and the realism in the early game, but helps to ensure that micromanagement is reduced in the mid-to-end game, when the player would be most irritated by starving to death. Similarly, other roguelikes like [POWDER](../../../game/powder.md) can have a skill which provides immunity to the negative effects of hunger - allowing the player to let their hunger timer to bottom out without adverse effect.  

### Time Limit

This is less often expressed, but I think lies at the heart. People feel the constant tick of time and know there is only a finite number of turns before they must get the next food ration. This may add excitement to some players, but others are turned off. Roguelikes are turn based, after all, so often played by people who want to think about their moves. Knowing a wasted diagonal movement may cause starvation will worry many players needlessly.  

### Interruption of Story

Worrying about food whilst engaged in an awesome task seems petty. This is tied to realism, of course, and both are as relevant to the whole discussion.  

This isn't to say that a heroic tale doesn't have food or hunger. Such tales, however, tend to concentrate on epic hunger. The mundane day-to-day eating is passed over to save words for more exciting things. Hunger in roguelikes tends to combine the life-or-death of "epic hunger" with the mundanity of everyday food shopping.
