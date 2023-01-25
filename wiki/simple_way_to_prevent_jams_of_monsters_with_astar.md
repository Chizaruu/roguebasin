# Simple way to prevent jams of monsters with A\*

---

## Introduction

---

This was originally posted on the r.g.r.d. newsgroup, and sparked quite the discussion. The explanation of the method is simple and can be read on the first post, copied here in its entirety.

## The problem

---

The problem is that monsters blocking the best path make other monsters go around the longest way. Making A\* think the monsters are "passable" results in piles of monsters around entrances, when ideally some of them should go around to avoid the "jam". Most solutions are a bit convoluted; at least the ones I know about. IIRC one of the arguments for the scent-based approach is that it avoids this; but A\* is more desirable for other reasons.

## The solution

---

**Monsters have a simple counter for how long they've been stopped in the same tile ("idle time"). This should be 0 when moving. When pathfinding, if a walkable tile has a monster on it, its cost is proportional to the monster's idle time.**

That's it! Emergent behavior is that some monsters flowing naturally through the quickest path don't make others go around the longest way; the cost is 0. But as soon as a jam is formed, the idle monsters will make it an undesirable path, and others will try to go for alternatives.

Discussion: ["Getting monsters with A\* pathfinding to behave!", on r.g.r.d., first post by Joao Henriques (Jotaf)](http://groups.google.com/group/rec.games.roguelike.development/browse_thread/thread/30eee2a1991f2086)
