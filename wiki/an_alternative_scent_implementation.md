# An Alternative Scent Implementation

---

## An Alternative Method of Implementing Scent

---

Here's an alternative method of handling scent and tracking. This should be more efficient than the per-tile scent attribute method.

We start with a Scent class, which holds the scent of a character at a map location. I’m using Java-like pseudocode for this article.

```java
public class Scent {
    int x;
    int y;
    float intensity;
    int radius;
    Mobile character;
}
```

Each time a character moves, it adds a Scent object to the map at its current location. The scent has an initial radius of 1 and an initial intensity of however badly the character smells. At each game turn the map object processes its array of Scent objects and calls the Age() method of each one:

```java
public bool Age() {
    radius = radius + 1;
    intensity = intensity * 0.8;
    return (intensity>=1.0);
}
```

Age() increases the radius of the scent and decreases the intensity by 20%. If Age() returns False then the map destroys the object, as it has faded away (i.e. intensity has become less than 1).

This gives us each Mobile leaving behind it a trail of scent objects which expand and fade away over time, like a boat leaving a wake behind it. Now lets look at how we can work with this.

## AI

---

Assume we have a RatAI behaviour which moves randomly around the map. The Rat moves to a random point near to its location, pauses for a few turns, then moves again. At each pause, the Rat takes a sniff:

```java
protected void Sniff() {
    foreach (Scent scent in this.getMap().ScentArray()) {
        if (distanceBetween(self.x,self.y, scent.x,scent.y) <=scent.radius && scent.intensity >= self.attribute(“senseSmell”)) {
            self.AI = new FollowScentAI(scent.character, self.AI);
            break;
        }
    }
}
```

This method scans the maps scent objects one by one. If it finds one within the Rats maximum smell radius whose intensity is greater than the rats sense of smell, set up an AI class which will start following the scent trail. This new behaviour tracks the character originally smelled, by "sniffing" for scent objects matching the character and moving to the one with the strongest intensity. The AI starts a fight (or whatever) if it catches up with the target. If a suitable scent object isn’t found, or if the target isn’t reached within a certain number of turns, the Rat loses interest and returns to it’s previous AI.

## Refinements

---

We can add some refinements to this system:

- Some characters, such as an orc, might have a good enough sense of smell to identify the type of character who left it - if it’s a human he’ll set off in pursuit, it it’s a deer he might ignore it (unless he’s hungry).
- To make following a scent a little more difficult, a random element could reduce how often a character leaves a scent object behind - the intensity could also be randomly modified.
- If a character doesn’t move for a number of turns, the scent object left at that location could be increased each turn (up to a maximum of say 150% of the default, otherwise we’ll stink up the whole map), or perhaps increase the radius without decreasing the intensity.
- If a character moved through some kind of watery location like a stream, no scent object is left (we could also reduce the characters smell intensity for a while).
- At the moment the AI follows the first scent object it finds. For more complex characters, such as our friend the orc, the AI could obtain a list of scent objects and follow the most interesting - human rather than dwarf. In this case “interesting” might be a percentage scale which the character uses to judge likely victory in a fight.
- If the player is allowed to play as a character with a good sense of smell, we could draw scent trails on the map - a circle of the correct radius which gets more transparent as the intensity decreases. An “Identify” command at the center of the circle might reveal the race of character which left the scent.

Some tuning of scent intensity would be needed, as we don’t want rats to be able to smell humans from say a mile away. We could also slow down the expansion and reduction of intensity to leave a narrower scent trail for a longer time.

## Other Uses

---

This system could be used for other environmental effects too, such as sound (a Sound class would reduce in intensity very quickly, after say two turns at most), magical effects or lighting.

–Knightlie 19:00, 18 June 2012 (CEST)
