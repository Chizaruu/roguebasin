# A priority queue based turn scheduling system

---

This is an implementation in Python of a turn scheduling system that uses a priority queue. Events include monster and player actions, and therefore is able to represent the relative speed of different creatures.

## The theory

---

When creatures take turns in a roguelike, sometimes it is desireable to have faster creatures to receive more turns relative to other creatures. For example, the player may take two turns for every one turn the giant slug makes, while the player is outrun by the bat who takes three turns for every turn the player makes.

To achieve this effect, a creatures speed is thought of giving it a delay between its actions. When a creature take a turn, there is some delay until it may act again. Faster creatures may have turns between this delay. The turn delay is shorter for faster creatures. Referring to the last example, the player would have twice the speed as the giant slug, and therefore receives half the delay between his or her turns.

These delays are handled by using a priority queue. When the game starts, all creatures on a level (including the player) are inserted into the priority queue with a priority equal to a certain delay. Faster creatures have a lower delay, and can be calculated by dividing a base delay value by the creature's speed (watch out for divide-by-zero errors).

To execute a turn, a creature is pulled off the queue, along with the priority it was given. To represent the passage of time, the active creature's last priority is subtracted from all priority keys in the queue. The creature is then allowed to act, after which it is placed back into the queue with its own delay.

## The priority queue

---

Of course, first we need our priority queue. This uses a sorted array, such that things with the same priority are ordered by their insertion order. PriorityQueue also features the method adjustPriorities which adds a certain value to all priorities in the queue.

```python
class PriorityQueue:
    def __init__(self):
        self.__queue = []

    def __len__(self):
        return len(self.__queue)

    def enqueue(self, value, priority = 0.0):
        """
            Append a new element to the queue
            according to its priority.
        """

        tuple = [priority, value]

        # Insert the tuple in sorted position in the queue.  If a
        # tuple with equal priority is encountered, the new tuple is
        # inserted after it.

        finalPos = 0
        high = len(self)
        while finalPos < high:
            middle = (finalPos + high) // 2
            if tuple[0] < self.__queue[middle][0]:
                high = middle
            else:
                finalPos = middle + 1

        self.__queue.insert(finalPos, tuple)

    def adjustPriorities(self, add):
        """
            Increases all priorities.
        """

        for v in self.__queue:
            v[0] += add

    def dequeue(self):
        """
            Pop the value with the lowest priority.
        """

        return self.__queue.pop(0)[1]

    def dequeueWithKey(self):
        """
            Pop the (priority, value) tuple with the lowest priority.
        """

        return self.__queue.pop(0)

    def erase(self, value):
        """
            Removes an element from the queue by value.
        """

        self.__erase(value, lambda a, b: a == b)

    def erase_ref(self, value):
        """
            Removes an element from the queue by reference.
        """

        self.__erase(value, lambda a, b: a is b)

    def __erase(self, value, test):
        """
            All tupples t are removed from the queue
            if test(t[1], value) evaluates to True.
        """

        i = 0
        while i < len(self.__queue):
            if test(self.__queue[i][1], value):
                del self.__queue[i]
            else:
                i += 1
```

## The time schedule

---

The time schedule manages the priority queue .

```python
class TimeSchedule(object):
    """
        Represents a series of events that occur over time.
    """

    def __init__(self):
        self.__scheduledEvents = PriorityQueue()

    def scheduleEvent(self, event, delay = 0.0):
        """
            Schedules an event to occur after a certain delay.
        """

        if event is not None:
            self.__scheduledEvents.enqueue(event, delay)

    def nextEvent(self):
        """
            Dequeues and returns the next event to occur.
        """

        time, event = self.__scheduledEvents.dequeueWithKey()
        self.__scheduledEvents.adjustPriorities(-time)

        return event

    def cancelEvent(self, event):
        """
            Cancels a pending event.
        """

        self.__scheduledEvents.erase_ref(event)
```

## Testing

---

This is some demonstration code showing how the time scheduler may be used to control the turn order of creatures in a game. Here, the delay time for a Thing is a base delay time divided by the thing's speed. Remember, to make sure the turn order is consistent from the very beginning, the Things are first inserted into the time scheduler with a delay equal to their normal action delay.

```python
class Thing(object):
    """
        Just something to test.
        Assumes that the maximum speed of a thing is 10.
    """

    BASE_TIME = 10.0

    def __init__(self, id, speed):
        self.id = id
        self.speed = speed

    def __str__(self):
        return self.id

    def actionDelay(self):
        return Thing.BASE_TIME / self.speed

#---------------------------------------------------------------------

TURN_ROUNDS = 3

if __name__ == '__main__':
    things = [Thing('a', 1), Thing('b', 2), Thing('c', 1)]
    q = TimeSchedule()

    turns = 0
    turnsTaken = {}
    for t in things:
        q.scheduleEvent(t, t.actionDelay())
        turns += t.speed
        turnsTaken[t] = 0

    turns *= TURN_ROUNDS

    while turns > 0:
        thing = q.nextEvent()

        turnsTaken[thing] += 1
        print thing
        turns -= 1

        q.scheduleEvent(thing, thing.actionDelay())

    for thing, numTurns in turnsTaken.iteritems():
        assert numTurns == (thing.speed * TURN_ROUNDS)

    print
    for id, numTurns in turnsTaken.iteritems():
        print id, numTurns
```

Output:

```text
b
a
c
b
b
a
c
b
b
a
c
b

a 3
b 6
c 3
```

### Observations

- The number of turns a creature receives in a repeated sequence of turns is equal to its speed.
- Doubling the speed of all creatures maintains the same turn pattern (though one may notice the turn pattern will double in length)

## Other notes

---

- Different creature actions may result in different delays, causing creatures to wait for shorter or longer periods. For example, attacking may take twice as long as walking. To implement this, insert creatures back into the turn scheduler with different delays depending on what action they took. Mind you, don't go overboard with this or the player won't be able to tell what's going on.
- The turn scheduler does not assume that it is storing creaturs. It may store any kind of events scheduled to occur in the game, and may be modified to handle regeneration, hunger, spell effects, and one-time-only events like time bombs.
- The regularity of the turn pattern may allow players to employ so-called "pillar dancing" and "hack and back" maneuvers. It is up to the developer whether this is a bug or a feature (if you think it's a feature, consider making an AI that can also pillar dance and hack-and-back).
