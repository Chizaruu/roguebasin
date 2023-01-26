# Language in Roguelikes

---

When I started playing Omega, I was just twelve years old, I couldn't understand many things in it as I'm Finnish and school English didn't quite match the language in Omega. It was way too slangish and used terms that weren't proper for school courses ;) So I picked all stuff up and my English grade went up like a rocket. Or could it have been that frantic watching of TV ?

Now I feel at home in Omega as I know the language. It even seems a bit too easy ;) But the most irritating thing in games today is that the same phrases come up again and again. You hit it! You hit it! Kobold is dead! You critically hit orc butcher! Orc butcher is dead! Something like that. Most of these sentences are really simple and stick to the same format. Subject does verb to object.

It doesn't need to be this way. As lazy person as I am, I haven't bothered to read too many sources for roguelike games. But what I've seen has showed things like _print("You hit");print(mons_article); print(mons_name_short);_ Not too hard, easy enough to not be bothered. Still that's what gamer gets all the time. It's of course nice that experienced players get info just by examining structure of sentences or something as easy, it just requires a bit of playing ;)

With a proper interface, a game can be much more colorful. Here is a C++ show (of interface I think to be the best).

```c++
if(this!=player) msgbar << subjadj << this->damage_report();
msgbar << subj << this->simpleflags() | 0x20*map->isseen(this->x,this->y);
msgbar << verbadj << "critically" <<
verb << "hits";
msgbar << objadj << mons->damage_report();
msgbar << obj << mons->simpleflags() | 0x20*map->isseen(mons->x,mons->y);
```

when damaging a monster.

```c++
if(msgbar.isstop) return;
msgbar << cont << obj2subj;
msgbar << passive << but;
msgbar << verb << "protected";
msgbar << obj << "armor";
msgbar << stop;
```

The player would see a message like

- You critically hit badly wounded orc but he was protected by his armor
- You hit orc
- You simply blast orc but he was protected by his armor
- You strike it (blind)

And dozen of other choices.

This could be done by saving words to data structures with info about it's meaning. When that sentence is asked, it would be built by an other function. That function could also drop words depending on verbose level, it could search for synonyms and even translate simple sentences quite easily.

Why would anyone do it in such a hard way ? You could just make a list of sentences and use them by index (if done cleverly this is a nice way). Or words too. Because this more modular, which helps it to be maintained and programmers don't need to think about the language. It is left to translators and those with better language skills. And because it is just so damn fun to write such a tricky interface.

Of course this could be used in a dozen other games, especially sports games, but in roguelike games we have a pressing need for this. One should naturally have a mode of :

```c++
msgbar << sentence << "You hear a voice : Thank you for helping me. ";
msgbar << "Killing The Big Bad Monster was beyond my abilities but";
msgbar << "I can die in peace, come to me for reward." << stop;
```

Such sentences could be indexed and translated later. But they are quite easy to recognize and translate by a function, if needed.

Another thing could be languages in game. Like dwarf or elf speech.

```c++
msgbar << says_in_language << "Village elder says"
msgbar << "You are welcome to our village" << stop;
```

could be showed as

```text
75% lang skill Village elder says :"You ar velkom to our villch"
50% lang skill Village elder says :"yu ar vlekm vil"
25% lang skill Village elder mumbles :"yu r vlkm vil"
```

and so on. In most languages, IMO, it's easy to spot things that go amiss. Like those 'e' letters, they really can be hard to spot as are word 'to' also. That's in English, in Finnish it might be the thing that it's easy to get opposites like "pankkiin" and "pankista" are in and out (of bank). And that words tend to be long and they are many times added to each other. The late word can be quite hard to spot, I'd guess.

Now for more important matters ;) As of the 1st of March 2000 (today) I make public my intentions to make a roguelike game (...silence..) and I've already written some code (very little) but I really would like to get in touch with some people that could help, either coding (I don't have too much time) or just suggesting things. I'll call my game Unjust World Of Adventures. But the game isn't all of it: I'll try to create the engine and basic world code to be used by anyone willing to make a roguelike game.

I develop using gcc(egcs) in a GNU/Linux system. All code is in C++ but it should work in any modern compiler environment (except curses and graphics).

Those interested should contact me, I'll send code (tar.bz2 or zip) in email (about 50Kbytes at the moment ;) or upload it someplace.

Copyright (C) 2000 Esa Ilari Vuokko. All rights reserved.

As far as law permits author (Esa Ilari Vuokko) can't be held responsible of any use of this text. Anyone may publish unmodified text in electronic media without specific permission. Though I really appreciate notes.
