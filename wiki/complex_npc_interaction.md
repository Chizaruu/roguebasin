# Complex NPC Interaction

---

Complex NPC Interaction by [Gamer_2k4](gamer_2k4.md)

In a standard roguelike, NPC interaction involves mainly buying and killing. This is acceptable for basic dungeonhacks. However, roguelikes could be so much more than just hack-and-slash. Imagine a world where NPCs negotiate and interact with each other. I have put some thought into a potential system.

Each monster has a set of attributes that determine their character. Morals is their general willingness to do lawful actions. Skill is a composite of their capabilities, such as diplomacy, woodcutting, bartering, or whatever else is generally considered useful. Strength is the representation of their combat abilities. Reputation is a measure of their reliability to do what they say (increased by completing quests, for example). Charisma is how likeable the character appears. All of these would have a value from 0 to 100. Monsters also have another attribute, Renown, that determines the percent of these attributes that are immediately visible to other monsters. For example, if you had Strength of 60 but only a Renown of 25, then other monsters would think you had a Strength of 15 (60 x .25 = 15).

Every monster also has three stats that represent their appraisal of other monsters. Favor is how much the other monster is liked. It increases based on the similarity of the two monsters' attributes. Respect is how much the monster recognizes the other monster's abilities. It is proportional to the level of the other monster's attributes. Honor is the amount that one monster feels they should help another. This is based mostly on race and profession. For example, paladins might feel high honor for everyone else; barbarians might have none. Dwarves would have high honor for other dwarves, but none for orcs.

In a nutshell, Favor is the amount one monster wants to help another. Respect is the amount of benefit one monster gains from helping another. Honor is the amount a monster feels obligated to help another. These stats range from -100 to 100. The average of the stats determines a monster's actions toward another. Negative numbers represent the chance a monster has of spontaneously hurting the other. Positive numbers represent the chance a monster has of spontaneously helping the other. Zero means the monster will neither attempt to help nor hurt the other.

Monsters would probably also have a familiarity stat that increases with each interaction. This would just be a bonus to the the other monster's Renown attribute, but it would represent personal knowledge of each other. Anyway, assuming no familiarity and no renown (as the case would be with citizens interacting with each other), monsters would leave each other alone (aligned neutrally). Monsters found in dungeons would probably have a low honor level of the player character, so they would attack him. However, once the player earned enough experience, he might gain the respect of weaker creatures who would then leave him alone.

That's the core of my AI idea. Obviously there would be different actions the monster could take based on their likelihood of hurting or helping another, including healing, attacking, stealing from, giving items, offering mercenary services, and so on.
