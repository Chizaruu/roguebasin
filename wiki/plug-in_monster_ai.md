# Plug-In Monster AI

---

Lets face it, monster AI in most games leaves a lot to be desired..... the beasties are either too stupid, too predictable or just so uninteresting that the game becomes an endless, repetitive hack and slash. I don't object to mindless violence, of course. But it would be nice to think that some the talented coders out there could come up with something a little more impressive and atmospheric.

What I'm going to suggest here is a system of 'plug-in' AI that might just make doing some interesting monsters a little bit easier.

I'm going to write the examples in Java. This is partly because it's my new language of the moment. But mainly it's because having a good object-oriented model with garbage collection makes life much more pleasant. Java also lends itself to making quite elegant solutions to problems of this type.

What's good about this model? Here are the nice points.....

- Allows for an infinite variety of friendly and unfriendly behaviour
- Easily extensible
- Relatively little extraneous coding, so you can focus on the AI itself
- Once created, monsters can just take care of themselves
- No huge switch statements (Hooray!!)

Fantastic, you say, but what is the downside? Well, it is quite a difficult concept at first, and it needs quite a lot of care in the implementation. But that, of course, is where the real fun starts. :)

## The AI Model

---

The basic idea is this: Each mobile (creature) in the game is represented by an object that maintains a reference to a separate AI object.

The basic stripped down class definitions are:

```java
// this is the definition for our basic mobile class

class Mobile extends .... implements ..... {

  // this is the key line.... a reference to the current AI object
  public AI ai;

  public void setAI(AI newAI) {
    ai = newAI
    // previous AI object will be automatically garbage collected

    // and some other stuff as required
  }

  // All other fields and methods
  // e.g. stats, inventory
}
```

```java
// And this is the generic AI object from which all other AI objects
// will inherit

class AI extends Object implements ...... {

  public void action(Mobile m) {

    //This is where the intelligence goes

  }

  // other AI functions go here, e.g psychology effects etc.
}
```

Whenever it is the particular mobile's turn to move, you just make the call

```java
  monster.ai.action(monster);
```

This instructs the AI object to work it's magic, and make the monster perform whatever behaviour it is currently following.

Well, that's the basic theory, now we move on to:

## Real-World Example

---

So far so good..... but what's the point? Well, what we want to achieve is a way of quickly and simply specifying a creature's behaviour. More importantly for realism and the sake of interesting AI, it allows us to change the behaviour of creatures dynamically as circumstances dictate.

We can do this by creating subclasses of the AI object, inheriting all the generic behaviour and adding whatever specific behaviour we desire. The most obvious AI to implement is that of the standard monster, who justs moves towards the Hero and attacks automatically.

```java
class AttackPlayerAI extends AI {
  // Just override the action method
  public void action (Mobile m) {
    //call standard monster movement function if player is visible
    if m.canSee(Hero)
    {
      m.moveTowards(Hero.x,Hero.y);
    } else {
      //Just lurk here and do nothing
    }
  }
}
```

If we now want to create a kobold at location (10,10) with this simple behaviour, we just write:

```java
  Mobile m = new Kobold(10,10);
  m.setAI(new AttackPlayerAI());
```

Et voila.... one particularly unfriendly Kobold has just been summoned into existence.

As a second example, this class makes a creature that walks between two points, e.g. a guard patrolling a side of a castle.

```java
class GuardAI extends AI {
  // these hold the co-ordinates of target squares (tx1,ty1) and (tx2,ty2)
  int tx1;
  int ty1;
  int tx2;
  int ty2:
  // direction==1 means move towards target 1 else move towards target 2
  int direction

  // Constructor for AI to walk between (x1,y1) and (x2,y2)
  public GuardAI (int x1, iny y1, int x2, int y2) {
    tx1=x1;
    ty1=y1;
    tx2=x2;
    ty2=y2;
    direction=2; //start walking from 1st to 2nd target
  }

  // Now override the action method
  public void action (Mobile m) {
    //work out current destination
    int tx = (direction==1) ? tx1 : tx2;
    int ty = (direction==1) ? ty1 : ty2;

    //call standard monster movement function
    m.moveTowards(tx,ty)

    //change direction if target reached
    if ((m.x==tx)&&(m.y==ty)) {
      direction = (direction==1) ? 2 : 1;
    }
  }
}
```

Now to create a guard that patrols between (10,10) and (20,5), just use:

```java
  g = new Guard(10,10);
  g.setAI ( new WanderAI(10,10,20,5) );
```

If the player attacks the guard, however, then we want to change the guard into a hostile creature, represented by a different AI. This is done simply as follows:

```java
  g.setAI ( new AttackPlayerAI() );
```

where AttackPlayerAI is just a standard AI descendant that makes the creature charge towards the player in a berserk rage......

## Extensibility

---

Of course, inheritance is a very useful concept in programming, and never more so than here. Withg only minor modifications, we can make a HostileGuard who patrols like a normal guard but attacks the player as soon as he/she is visible:

```java
class HostileGuardAI extends GuardAI {

  // override action
  public void action(Mobile m) {

    if (m.canSee(Hero)) {

      // Charge! Install new AI into the mobile.
      m.setAI (new AttackPlayerAI());
      Hero.message("The " + m.name() + " yells with rage!");

    } else {

      // call parent method to continue patrol....
      super.action(m);

    }
  }
}
```

The really neat thing about this system is that it is almost infinitely extensible. You can make a new AI object for almost any type of monster imaginable. Just a few ideas for AI objects:

### CowardlyMageAI ()

Stays still and casts spells at the player UNLESS there are no cretures between it and the angry hero, in which case it makes for the nearest escape route.

### SleepAI (int time, AI wakeup)

Sleeps for time periods, then gets up uses the new AI object given. Brilliant for a mesmerize spell, for example, that makes the creature friendly upon waking.

### LurkAI (int dist, AI whatnext)

Waits on the same spot, until the player comes within a specified distance. Then does something completely different, e.g. run away, attack, or offer to trade.....

### FleeAI ()

Run away! This AI object makes sure that the creature stays as far away from the player as possible. You could give the creature a valuable object, and make it play a fun game of hide+seek all round the dungeon.

### SequenceAI (AI firstAI, int time, AI secondAI)

Use firstAI for the specified number of time periods, then swotch to secondAI. Nested SequenceAI objects could be used to create complex behaviour.

The list of possibilities is endless.......

Having thought about it for a while, I think that psychological effects such as certain spells should probably be passed to the AI object, which can deal with them as approprite. For example, UndeadAI might filter out any panic or confusion effects. If necessary, the AI could react by installing a new AI object. For example an initially hostile goblin who is hit by a panic spell could be given a FleeAI object.

It is probably useful to create a default AI for each type of creature, so that you don't need to explicitly create a new AI object whenever you create a mobile. Behaviour could also be allowed to return to this default AI when psychological effects wear off.

## Extensions

There's lots of things that could be added to this AI model. I'd like to describe just one of them very briefly that I think could be a useful way of defining behaviour for roguelike creatures.

Since each mobile contains only a _reference_ to it's AI object, the thought naturally occurs that several mobiles could share the same AI object.

If this was a simple AI with no internal data, e.g. AttackPlayerAI, this would make no real difference, apart from saving the overhead of constructing a seperate AI object for each hostile monster.

But you could create a special set of "Group" AI objects that would allow a set of monsters to act as a pact, with a built-in flocking instinct and some idea of co-operation in battles. Basically, you would construct a single AI object and assign it to all the monsters in the group, who would then take on the relevant shared behaviour.

e.g.

```java
  AI ai = new HositlePackAI ();
  Mobile m1 = new Orc(10,10);
  Mobile m2 = new Orc(10,10);
  Mobile m3 = new Goblin(10,10);
  Mobile m4 = new Goblin(10,10);
  Mobile m5 = new Troll(10,10);

  m1.setAI(ai);
  m2.setAI(ai);
  m3.setAI(ai);
  m4.setAI(ai);
  m5.setAI(ai);
```

This kind of system would be brilliant for outdoor skirmishes. You could monsters capable of laying an ambush. You could have them charging and retreating as a single unit. I leave it to your imagination to dream up all of the evil ways you could get fighters to flock in defence of a vulnerable mage,

## Conclusion

Well, I hope this gives a little bit of inspiration to everyone out there who is trying to craft the ultimate creature from the pit. I hope that I've shown that although there's a lot of different things that you need to consider when building an AI system, the problem really can be manageable and flexible if you find the right structure.

Happy Coding!

Mike Anderson

mike@mikera.net

5th March 1999
