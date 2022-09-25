# Interesting Critical Hits

---

## Introduction

---

Presently, critical hits in roguelikes all seem to have the same effect: increasing damage by some multiple. However, during a conversation on [#rgrd](irc://irc.quakenet.org/#rgrd), [kaw](../../../developer/kaw.md), [DGrey](../../../developer/dgrey.md), and [Gamer_2k4](../../../developer/gamer_2k4.md) came up with several additional ideas for critical hits. After all, a critical hit is just an attack that gives you some sort of unexpected advantage. Some [monster attack effects](monster_attacks.md#effects) may be suitable as additional consequence.

## Critical Attack Effects

---

A good hit might deal the same amount of damage, but also cause some sort of status effect, such as:

* Stunning
* Bleeding
* Paralysis
* Slow Movement (target was hit in the leg)
* Blindness (target was hit in eye/eyes)

Critical hits could also have instant effects:

* Disarm the target
* Damage/Destroy the target's weapon/armor
* Knock the target back a tile
* Swap positions with the target (for unarmed "throws")
* Prevent the target from attacking next round
* Giving the attacker another action (the attack was "free") or an initiative/speed bonus on the next turn
* Give the attacker an attack or defense bonus against the target for the next turn (The bonus should only last one turn, to prevent insane stacking issues)
* Allow for a second critical roll (a critical on a critical hit) which may inflict a second effect in addition to the effect from the first roll or make the first effect more powerful. (Must be carefully balanced though, could easily be too powerful. The number of criticals allowed per attack should be limited, probably at two.)
