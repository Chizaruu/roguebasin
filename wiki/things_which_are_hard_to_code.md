# Things which are hard to code

---

Compilation by [Kornel Kisielewicz](kornel_kisielewicz.md) of the thead on [rgrd](rgrd.md)

## Things which are harder to code than one might initially think

---

[Antoine](antoine.md)

- Invisibility
- Polymorph-self
- Charm monster
- Stacking objects
- Friendly NPCs in the dungeon

[The Sheep](the_sheep.md)

- Selling items
- Timed events
- Animation
- Persistent levels
- Monsters moving between levels
- Monsters with FOV
- Pets
- Random artifacts
- Random monster races
- Doors with keys
- Throwing items
- Monster inventory
- Running

[Kornel Kisielewicz](kornel_kisielewicz.md)

- Random Quests
- Random Plot
- Random Overworld
- Adding content
- Balancing
- Polishing

[Stu George](stu_george.md)

- Balance
- Sense of time
- Food
- Q&A, Testing, Documenting, Planning
- Water (infinitely divisible)
- Fire (I want to burn everything!)
- Mixing liquids
- Economy

konjin

- Stealth

Ray Dillinger

- Rope

[Michal Bielinski](michal_bielinski.md)

- Inter-monster fights

[Stoolmaker](stoolmaker.md)

[Not part of Kornel's kompilation.] I think it depends on the whole framework of code. In my world, for instance, making critters invisible is a rather simple task because of how I've implemented [Line of Sight](line_of_sight.md). Making them dead is harder :) That said: I find that balancing everything is a delicate task. Randomizing quests, equipments, races etc. is also an interesting challenge. Regarding these, however, I think its possible to find some short cuts just by writing imaginative pieces of sentences/elements and combining them in unpredictably large structures (needn't be so huge). For example: put n kinds of "wandlikes" in a list (wands, lamps, bottles (uncork to release effect) etc.), have n kinds of "range models" (cones, rays, single adjacent/far points, zones (eg. fireball), clouds which start to drift and finally dissolve...), and n kinds of effects (elemental damage, teleport etc.). Already you'll have weird objects popping up: from the presumably predictable ("copper wand: ray of ice"), by means of the useful ("painted calabash: cloud of panic") and all the way to the outright silly ("ugly conjurers hat: rain of polymorph").

[Ramiro](ramiro.md)

- Cellular Automata dungeons. (Even whit all the theory is STILL harder than you can think)
- Anti-filescumming. (In theory is impossible, but you can make things harder)
- Running (on ASCII Roguelikes, and turn based ones specially)
