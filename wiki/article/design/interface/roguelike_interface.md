# Roguelike Interface

---

by [Gamer_2k4](../../../developer/gamer_2k4.md)  

Many roguelikes have, IMO, a clunky interface. Ideally, the screen should show all relevant information, and no unnecessary information; this information should be easily accessible. Examples of good interfaces include the two line message display in ADOM, the combat icons (*, -, /, etc.) in Gearhead, and the monster HP display in the sidebar in some Angband variants.  

When the player is just moving around, what should be displayed? Is it really important for the player to know each of his attributes at all times? Probably not. It's probably only necessary to display status conditions, like "hungry" and "bleeding", speed, maybe a list of visible items or creatures. Angband variants do the last two features very well. Also, if sounds are heard, an '!' icon should display, adjacent to the player and in the direction of the noise. Perhaps color could indicate distance or type of sound. for example, in ADOM, "you hear the wind howling" would display a dark grey '!' in the direction of the forge.  

During combat, there are several things the player wants to know. These include monster HP, player HP, player MP, player resistances, attack results, status effects, and so on. It's also a good idea to alert the player if his equipment breaks, or if he scores a critical hit, or some similar event that changes his combat ability. Ideally, the information will not need to be displayed as a message, so that the player doesn't have to check the top of the screen after each attack. An attack would display a '*' on the target, with the color indicating damage. Light grey might be a normal hit, dark grey could be a harder hit, dark red could be a critical hit, and so on. Spells and arrows should always be displayed "graphically", assuming the player would be able to see them.  

Well...what else is there? Roguelikes are about movement and combat. I think that's it for display, except for one thing. Earlier I advocated that only certain pieces of information should be shown at certain times. However, there should definitely be commands to view all the information. Obviously the player will want to know his Strength or Dexterity at some time, even if he never directly needs that information. So it's a good choice to have a character screen that gives all that information.  

Interface also refers to input, so here are a few ideas for that. There should be a generic command to interact with the environment, and another generic command to equip items. Unless you're making the next Nethack, there's no need for different commands for wearing armor, putting on jewellery, and wielding weapons. There should also be a generic command to use an item, like drinking a potion or reading a scroll. Better yet, why not have all such commands dealt with from the inventory screen? 'I' might bring up the inventory screen, which would have a list of items and a bottom row something like this:  

```text
[M] More information   [U] Use/wear   [D] Drop      Filter: []{}()/\+=!?~"
```  

You would just select the item you want with the arrow keys, and use the commands when the desired item is selected. Of course, this would get tedious searching for items, so it would be good to have a filter option like in ADOM.  

There should definitely be a macro system of some sort to handle tedious commands, or at least a way to map certain spells or attacks to the function keys. Also, why should there be a difference in combat techniques, abilities, and magic? Maybe you've learned the Cross-slash technique and the Fire Bolt spell, and you can intrinsically use Stone Skin. Why should you need different keys to access them? Maybe use 'a' for ability, then just select one.  

Basically, input should use as few commands as necessary, and the player should have to read as few messages as necessary. This keeps the display open, and the interface uncluttered in general. I think roguelikes would benefit from an effective interface; maybe we'll see some new roguelike that uses these ideas or even new ones.
