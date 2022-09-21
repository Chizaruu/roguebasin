# Time Systems

---

Time systems are controllers built into a roguelike engine which handle the order of interaction of the actors inside the world. There are many ways to handle the interactions and they are usually unique to each project; However, certain similarities allow to propose a categorization on the following:  

## Player-centric

---

Have the main game loop handle UI and call move_monsters() (or generally any other function that advances the whole world a turn) in the code for some of the player actions (the ones that consume a turn). Generally inflexible approach, although might seem appealing for animation-heavy and "modern" game.  

## Simple turns

---

You've got your main loop that iterates over all the "actors" (things that can act, be it the player character, the monster or self-closing door), asks them what they want to do and performs it if it's possible. The actors might still be able to do some things by themselves, without consuming a turn.  

This approach is better, but it's a bit awkward to write the actor's code, because every turn the function is called anew -- you've got to record all state information in the actor's data, and probably do a switch statement at the beginning of the actor's code... Note that "slow" monster may wait once per several turns, and "fast" monsters can perform some actions without returning, but it's a little messy.  

If you want an action like eating to take several turns, you just mark in the actor's data that it's eating, check for interrupts, update the counters and return.  

## Queued turns

---

In this approach you've got a kind of priority queue, or other similar thing, that holds the actors. You remove actors from the queue, call their functions, perform their actions and then put them back into the a sorted position into the queue. The position depends on how much time the action took -- you must keep track of it.  

Interrupts can be handled in two ways -- you can use small steps for long actions, exactly like in the previous approach, or you can use more accurate system:  

Have the action separated into the preparation and the effect parts. When you add do queue an actor that decided to do a preparation action, add it with proper delay, but add it also to a special "watchers" lists. Every time any actor does something, all the actors in the "watchers" list are informed about it and have to decide whether continue the preparation (then nothing happens) or interrupt it (then they are moved to the beginning of the queue), then the "preparation" is cancelled and they are free to decide upon their own action. When you reach in the queue an actor that was "preparing" and didn't cancel it, it can do the "effect" part of it's action, actually performing it.  

## Energy systems

---

The game time is separated into "ticks" -- they are like turns in the "simple turns" approach. A main loop iterates over all the actors every tick, increasing their "energy" counters. Every action has an energy cost. When an actor declares an action, it's first checked whether it has enough energy for it -- if yes, the energy is deducted and the action is performed instantly. If no, the actor waits until it accumulates enough. If he decides to interrupt the wait, he can use up the accumulated energy right away.  

Alternatively, when the actor wishes to perform an action, given that the actor has greater than 0 energy, it performs it and has the action's cost deducted from its energy amount; depending on the current game speed (potentially customizable), a certain amount of energy is restored to every actor every tick. Games such as [Dwarf Fortress](../../../game/slaves_to_armok_II_dwarf_fortress.md) use this approach.  

## See also

---

* [An elegant time-management system for roguelikes](../../implementation/time-management/an_elegant_time-management_system_for_roguelikes.md)
* [A priority queue based turn scheduling system](../../implementation/time-management/a_priority_queue_based_turn_scheduling_system.md)
* [A simple turn scheduling system -- Python implementation](../../implementation/time-management/a_simple_turn_scheduling_system_--_python_implementation.md)
* [@Play: Modeling Motion on a Dungeon Grid](http://www.gamesetwatch.com/2008/08/column_play_modeling_motion_on.php)
