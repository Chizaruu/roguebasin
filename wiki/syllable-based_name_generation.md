# Syllable-based name generation

---

Syllable-based name generation is perhaps the simplest random name generation to implement. In its most basic form, the method looks up two random elements: one from a list of "first syllables", and a second one from a list of "second syllables". The two elements are then joined to form a single string. For instance, a simple pair of lists like this:

```text
Steer / pike
Burb / root
Slartibart / fast
Snarley / tot
Prune / squallor
Puss-in- / boots
Suck / fizzle
Gormen / ghast
Rose / bud
Goldi / locks
```

would yield names such as "Snarleyboots" and "Pruneroot", which could be deemed acceptible, but also slightly disjointed names, like "Slartibarttot".

The key to a successful syllable-based name generator lies in making a balanced pair of lists, where the different elements have a common feel. Typically, a set of syllable lists will have a common theme (eg. "Germanic female", "Arabic male", "Pub names").

For a more varied engine, you can of course use three or more lists. Three lists of ten syllables each already opens up for 1000 names. You can also include empty syllables (which in the example above would allow names like "Prune" and "Tot") or placeholder "syllables" that are instantly replaced by a two-syllable set (yielding names like "Slartibartsquallorfizzle"). Name generation rules can have a grade of randomness as well. Imagine you have three sets of syllables. You are not limited to using one of each. It might be a good idea to place a random number of middle syllables between the starting and ending ones. You might also want to include some post-processing rules, eg. disallowing triple consonants, or making sure that a first syllable ending with a vowel will not be followed by a second syllable beginning with a vowel.

The big pro of syllable-based name generation is that it's extremely easy to implement, and you can retain good control of what kinds of names are produced. A major downside is that almost every set of lists will occationally spit out a really goofy name.
