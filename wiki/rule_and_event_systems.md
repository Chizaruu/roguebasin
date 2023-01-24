# Rule and Event Systems

---

Rule systems and event systems describe how a world model evolves in time. They intend to make it easier to describe complex interactions between entities in the world model, compared to writing the behavior out in engine code. Roughly speaking, rule and event systems are to time what [entity component systems](entity_component_system.md) are to space.

A rule system has rules in the form of "IF condition THEN effect", which are constantly being checked when the game is running, with the effect triggering if the condition is met. The key difference here is that the rule writer does not explicitly attach the rules to the control flow of the game engine code, the engine iterates through the rules automatically and triggers the ones where the condition is fulfilled. The problems include rules coming into conflict, increased difficulty in understanding control flow and possible performance problems caused by large rule sets. There is research on this technique using the term "forward chaining inference".

An event system is more about the effect parts of the rules. It reifies events that cause changes in the game world into objects, which can then be put into a delay queue, logged for debugging, logged as transactions that can be rolled back, intercepted and modified and downright ignored. Event objects are also handy for a publish/subscribe game AI architecture, where the in-game AI is passed certain types of events and reacts to them. Using events involves a more complicated control flow and overall program architecture, but they might be useful for making complex interactions that happen over spans of in-game time easier to describe.

Event systems are not quite the same thing as [event-driven programming](http://en.wikipedia.org/wiki/Event-driven_programming). The latter is about using events from physical input devices, like key presses and mouse clicks, to interrupt the program, while event systems are about representing virtual in-game events, like a game character firing an arrow, as objects, instead of hardcoding them in the engine code. Certain languages and platforms, however, such as [ActionScript](actionscript.md), treat input-based events much in the same way as user-defined events.

## References

---

- Andrew Plotkin: [Rule-Based Programming in Interactive Fiction](http://eblong.com/zarf/essays/rule-based-if/) Introduction to rule-based systems in games.

- Julian Mensch: [Incursion: Return of the Forsaken – A Case Study in Roguelike Game Development](<http://www.incursion-roguelike.org/TechPaper%20(Web%20Version).htm>) ([archived version](<https://web.archive.org/web/20150214122242/http://www.incursion-roguelike.org/TechPaper%20(Web%20Version).htm>)) Tech paper describing an event-based system.

- Nathan Combs, Jean-Louis Ardoint (2005): [Declarative versus Imperative Paradigms in Games AI](https://pdfs.semanticscholar.org/b531/0f768ed94a7fac605c169869954aff40d705.pdf) ([citeseer](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.142.1820))
