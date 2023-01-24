# Two-Key Targeting

---

## Introduction

---

The use of projectile combat in a roguelike game requires setting a target before firing a weapon or casting a spell and this process usually requires several key stokes to accomplish. For a turn-based game, this usually isn't a problem since the game opponents only react when the player character completes an action. However, it can be a problem for real-time games, where the game opponents move and react independently of the player actions. In the case of a real-time game, targeting needs to be quick and efficient.

## Implementation

---

Two-key targeting, as the name implies, uses one key to target and one key to fire at a target. What is being fired, a projectile weapon or spell for example, is assumed to be ready to fire. The implementation is actually quite simple.

A single key is assigned as the 'target' key. When pressed, all targets within range of the player character are assigned an id, usually a letter or number. This targeting id overlays the entities normal display icon while the targeting mechanic is active. Once the target ids have been assigned, pressing the target id fires the active weapon at the target.

Exiting target mode can be accomplished explicitly by pressing the assigned target key or automatically under certain conditions. Some of the conditions may be:

- All targets have been eliminated.
- The weapon is out of ammo or charges.
- Targets have moved out of range.
- The player has moved the character out of weapon range or line of sight of targets.

There may be other game specific conditions as well. Automatically exiting target mode streamlines game play and prevents player confusion, especially in a real time game.

## Limitations

---

As with any method, there are limitations that need to be considered before implementing two-key targeting. Some of the limitations and possible solutions are listed below. There are probably other, and better solutions to these problems than those listed here.

### Loss of Monster Identification

"and we drink" In an ascii-based game, replacing the monster letter for a target id may be problematic since the monster letter information may be needed by the player for tactical decisions.

_Possible solution:_ The target id may be placed beside the monster letter in a linked fashion so that the player has both the monster letter and target id available. When many targets are crowded together though, this may not be a workable alternative.

_Possible solution:_ Somewhere on the screen, a list is generated and displayed that shows both the monster letter and target id while targeting is active. This would require the player to keep track of two areas of information, but if the list is in close proximity to the area of action, this may be a workable alternative.

_Possible solution:_ Using blinking text, cycling back and forth between the target id and the monster letter rapidly. This may be difficult to read or annoying.

Note that for a graphical game, where monsters are represented as graphical icons, the target id could be printed in a smaller font in the corner of the icon, maintaining monster identification information and target information.

### Spell Casting

For conventional weapons, it is normally assumed that a player character can only fire weapons that have already been equipped. However, in the case of spell casting, the player may need to select a spell from a list of spells that are not normally equipped items.

_Possible solution:_ A simple approach in this case would be to require that the spells be queued before use. The player could arrange the spells in the order of use, the first spell in the list the first employed, the second spell in the list the second employed, etc.

### Limited Target IDs

Since there are a limited number of keys on the keyboard that can be selected with a single key press, the number of target letters is also limited.

_Possible solution:_ If the number of targets available exceed the number of target ids available, only assign target ids to targets based on some sort of priority mechanic. The ids do not necessarily have to be letters, but could be any of the available keyboard keys such as letters, numbers and symbols. Once the assigned targets have been eliminated, the player would need to target again to acquire the remaining targets. By including the shift key, a three-key process, the number of available target ids would double.

## Summary

---

Two-key targeting offers a way to streamline game play, but would not be appropriate for every game. In the situation where speed and efficiency are important, the two-key method, or a variation, could greatly enhance the game experience for the player.

## Games That Use Two-Key Targeting

---

[Escape From Lab 42](escape_from_lab_42.md) uses two-key targeting with auto termination of targeting.
