# Translation

---

This page is intended to contain a summary of information on how to translate [NetHack](../../../game/nethack.md) or other [roguelike](../fundamentals/what_a_roguelike_is.md) games into other languages.  

**This is not a complete list. For more information on roguelikes in your language, do a Google search.**  

## Roguelikes in Non-English Languages

---

### Czech

* [CalcRogue](../../../game/calcrogue.md) works in English and Czech. CalcRogue is a Rogue clone for Windows, Linux, PalmPilots, and certain QWERTY-keyboard TI calculators.

### French

* [KRogue](http://ksi.ii.uj.edu.pl/krogue/), the KDE-improved version of [Rogue](../../../game/rogue.md), works in French.
* [CryptRL](../../../game/cryptrl.md) works in English and French.

### German

* [NetzHack](http://www.netzhack.de/) (note the 'z') is a German translation of NetHack. Runs on Linux, Win32, *BSD, OS X.
* [NetHack-De](http://nethack-de.sourceforge.net/) is an unfinished translation of NetHack into German for Linux and Win32.
* [Lost Labyrinth](../../../game/lost_labyrinth.md) works in English and German.

### Japanese

* There are Japanese translations of [NetHack](http://www.jnethack.org/) and Slash'EM available.

* Angband 2.8.3 and several variants have been translated into

Japanese. More information is available at the following web pages:  

* Angband 2.8.3: <http://plaza16.mbn.or.jp/~irisroom/jangband/jangband.html>
* Oangband 0.4.1: <http://www.kmc.kyoto-u.ac.jp/~habu/oangband/>
* PernAngband: <http://www.hcn.zaq.ne.jp/kusunose/pernangband/index.html>
* Zangband 2.4.0: <http://www.geocities.co.jp/SiliconValley-SanJose/9606/zg/index.html>

* [Tower of Doom](../../../game/tower_of_doom.md) is available in Japanese: <http://panyara.hp.infoseek.co.jp/tod.html>

### Polish

* RUSH - <http://www.rush.republika.pl/>
* Podziemia - <http://www.podziemia.ugu.pl/>
* Drogue - <http://bzss.org/drcz/drogue33/drogue33.htm>
* Tasak - <http://www.rzuf.kom.pl/>

All entirely and only in Polish. Try a Google search or ask in the Polish [newsgroup](http://groups.google.com/group/pl.rec.gry.komputerowe.roguelike/topics) pl.rec.gry.komputerowe.roguelike for more information.  

### Russian

* Russian version of ADOM exists (in fact, there are several of them), although they are not grammatically correct.
* [Menskband](../../../game/menskband.md) is a roguelike game similar to Angband.
* Baryonyx
* There was an attempt to make a translation of Angband into Russian which met with some success. Search at <http://www.googlegroups.com> for more details.

### Spanish

* [The Dawn of the Dead III](../../../game/dawn_of_the_dead.md) is exclusively in Spanish.
* There is a [Spanish translation](http://spanish-nethack.sourceforge.net/) of NetHack available.
* [Como el Rogue](http://comoelrogue.blogspot.com/) is a Spanish roguelike in development

The translations of NetHack and SlashEm do not use [GNU gettext](http://www.wikipedia.org/wiki/gettext), the standard system for translating all applications, because of the fancy linguistic operations that NetHack and SlashEm do.  

### Translations not released

Many other people have started (and often abandoned) work on other translations; search the [rec.games.roguelike.* newsgroups](http://groups.google.ca/groups?q=group%3Arec.games.roguelike.*+internationalization%7Clocalization%7Cl10n%7Ci18n%7Ctranslation%7Cgettext) for more info. If you find information on abandoned roguelikes, please post the original translator's contact information here if possible.  

There is an [Ubuntu roguelike translation project](https://launchpad.net/roguelike/) although it has just been started and is not yet active.  

## Translation issues

---

Issues in translating roguelikes include:  

* Issues that affect all software translators:
  * Inflection: conjugating verbs; declining nouns, pronouns and adjectives
  * Number and gender agreement between nouns and adjectives, verbs and subjects
  * Natural gender vs. grammatical gender
  * Number: singular/plural/dual

* Issues specific to roguelikes:
  * Parsing user's text input at the wishing prompt
  * Lack of internationalized program structure

NetHack constructs sentences using the superficial morphology of English, rarely using any grammatical information such as number, gender, subject or object case, etc. Making the code conjugate verbs and decline nouns and adjectives in another language is therefore part of the work involved in translating the program. These issues are different in each language, and the NetHack source code does not anticipate them.  

The [NetHack Wiki](http://nethackwiki.com/wiki/Localization) has some information on localization strategies.  

See also [Localization Project](localization_project.md).  

For more info, see [the appropriate threads in the rec.games.roguelike.* newsgroups](http://groups.google.ca/groups?q=group%3Arec.games.roguelike.*+internationalization%7Clocalization%7Cl10n%7Ci18n%7Ctranslation%7Cgettext).
