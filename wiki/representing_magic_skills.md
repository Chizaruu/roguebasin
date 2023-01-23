# Representing Magic Skills

---

This article describes the basics of representing and using magic spells in roguelikes and many other games. The general techniques are very similar to those used in my article on object representation.

The basic concept is to create a data-driven definition of a spell, including the effects the spell has. This can be done with a simple set of data structure and a hard-coded table to start. More complete versions will want to use an external data-file to allow more rapid iteration of spells.

For starts, we need a class to hold the information for a specific spell. We will call this the SpellEffect. Spell effects can be defined with a few simple properties. One of these is an enumeration to define the specific effect in place, which would be used in code to select which set of game logic to apply when the spell effect is used. Different spell types will need additional parameters. We can for many simpler magic systems simply keep this as an array of integers, the specific interpretation of which is up to the spell effect.

First, we create an enumeration of the different spell effects the game has support for.

```c
enum ESpellEffectType {
    kEffectNone,
    kEffectHeal,
    kEffectDamage,
    kEffectTickle,
    kEffectCreateObject,
};
```

Now we can create a simple definition for a spell effect. All we need is the type of effect and the parameter array. We'll create a macro for the number of items to ensure we have as few magic numbers (no pun intended) as possible.

```c
#define NUM_SPELL_EFFECT_PARAMS 5
struct SpellEffect {
    enum ESpellEffectType type;
    int params[NUM_SPELL_EFFECT_PARAMS];
};
```

At this point we can define an entire spell. A spell can have multiple effects, so we need an array of SpellEffect values in our spell. We'd also like some additional data about the spell, such as its name, its cost in mana points, and its level. This metadata will vary based on how exactly the magic system in a particular game is meant to function from a gameplay perspective. Not all games use levels or mana points, for instance.

```c
#define NUM_SPELL_EFFECTS 5
struct Spell {
    char* name;
    int mpCost;
    int level;
    struct SpellEffect effects[NUM_SPELL_EFFECTS];
};
```

Casting the spell is just as simple. In order to cast a spell, the system needs to know which spell is being cast, who is casting the spell, the target (if any), and so on.

```c
void CastSpell ( const struct Spell* spell, struct Actor* source, struct Actor* target );
```

An implementation could be as simple as a switch statement on the effec type.

```c
void CastSpell ( const struct Spell* spell, struct Actor* source, struct Actor* target ) {
    int effectIndex;
    PrintLog( "%s cast %s on %s", source->name, spell->name, target->name );
    for ( effectIndex = 0 ; effectIndex < NUM_SPELL_EFFECTS &&
            spell->effects[ effectIndex ].type != kEffectNone; ++ effectIndex ) {
        const struct SpellEffect* effect = &spell->effects[ effectIndex ];
        switch ( effect->type ) {
            case kEffectHeal:
                Heal( target, effect->params[0] + source->level * effect->params[1] );
                break;
            case kEffectDamage:
                Damage( target, effect->params[0] + source->level * effect->params[1] );
                break;
            case kEffectCreateObject:
                CreateObjectAt( effect->params[0], target->position );
                break;
        }
    }
}
```

Notice how the params of each effect are used. For both heal and damage, the parameters are defined as a constant value plus a scalar applied to the caster's level. The other values in the params array are unused. For the create object effect, the first param is used as an object type (likely taken from another enumeration) and the other values are unused. These parameters can be used however is most appropriate for the desired effect. Also notice how kEffectNone is used as a sentinel, which must be 0 to make easy initialization in a C table work.

For instance, the heal effect could instead be defined as:

```c
Heal( target, random_dice( effect->params[0] , effect->params[1] ) + effects->params[2] );
```

That definition would mean that the first three parameters are used to define an expression similar to the common XdY+Z pattern, meaning to roll X number of Y-sided dice and add Z to the result.

An example of definining a couple of really simple spells with this system in C without a data file would be:

```c
const struct Spell spells[] = {
    {
        "Cure Light Wounds", 10, 1, {
            { kEffectHeal, { 10, 1, } },
        }
    },
    {
        "Cure Moderate Wounds", 30, 2, {
            { kEffectHeal, { 15, 4, } },
        }
    },
    {
        "Summon Duck", 15, 5, {
            { kEffectCreateObject, { kObjectDuckActor } },
        }
    },
};
```

Note the aggregate initialize syntax of C. Only the parameters in use need to be specified. Since the definition of the heal effect only uses the first two parameters, that's all we fill in. The kEffectNone spell type is used as a sentinel value so our loop above knows when it's done with all effects in a spell.

It may be necessarily to add some additional fields to SpellEffect, such as whether the effect affects the caster/source or the target, timing information, and so on.

Any questions, comments, threats, etc., e-mail me at s_middleditch@wargaming.net

Sean Middleditch Wargaming Seattle
