# Localization Project

---

This page aims to collect translations of common roguelike messages to other languages. Using this information author can (crudely) enable localization support for his/her game. It will not be perfect but probably sufficient for a good start.  

## Translation notes

---

**French**: Change "le" in the French messages below to "la" when referring to a grammatically feminine monster. French grammatical gender when referring to nonhumans varies by species rather than by individual gender. Either article shortens to l' when used before a monster whose species name starts with a vowel. Note that if you're building more complex messages using adjectives, the adjectives will also have to agree in the masculine or feminine form with the modified noun.  

Also, if you have to guess the grammatical gender of a species, and you guess feminine when the species name ends in -a or -e and masculine otherwise, you'll be right most of the time (but not always, alas) in French.  

**Polish**: Nouns are modified by appropriate declension rules. Thus a "standard" message would be most likely wrong for randomly chosen game name.  

**Spanish**: Verb conjugation, division into masculine and feminine sexes. Nouns have only one form for singular and one for plural, but there are articles for each person and number, both definite and indefinite:  

| Sg. M. | Sg. F. | Pl. M. | Pl. F. |
--------|--------|--------|--------
un |una |unos |unas  
el |la |los |las  

**German**: Declension rules modify articles and verbs to determine case and many other things in a sentence. While the German is more complex than English in this regard, translating simple sentences is relatively straight-forward, with a little effort. I will give some of the rules here, and if you get these, then you should be able to translate simple sentences on your own. Granted, you could always use an online translator, but if you know the rules yourself, you can double check the results.  

**Articles** (German has four forms of 'the')
Nominative case | Accusative case | Dative case | Genitive case
--------|--------|--------|--------
der (masculine) |den |dem |des
die (feminine) |die |der |der
das (neuter) |das |dem |des
die (plural) |die |den |der

Every German word has a 'the-word' (one of four, including plural, indicated above under Nominative case) attached to it. The is important because whenever you have a noun (car, for example) there is always a 'the-word' (article) and the article is always spoken (unlike in English). There is little rhyme or reason which article goes with which noun and Germans just simply memorize them.

A quick word about cases. I listed the four cases (Nominative, Accusative, Dative, and Genitive) above. Nominative is used for the subject (**I** gave, for example I is the subject of the sentence). Accusative is used for the direct object (I gave the **book**, book being the direct object). Dative is used for a number of things (might be useful to look up Dative case in wikipedia for more information on its uses) but, for now, it simply indicates the indirect object (I gave the book to the **editor**, editor being the indirect object). Most simple sentences you construct will have one or all three of these cases in them. I will leave Genitive case for your own further study.  

So, hopefully you understand articles. The next thing to learn is conjugation. Conjugation is simply learning how to modify verbs (verbs = action words) based on the preceding participant (I, you, he, she, it, we, you guys, they). Given a participant, you add the ending onto the verb. The endings are show below:  

**Verb endings**
Participant | Ending
--------|--------
ich (I) |e
du (you) |st
er (he) |t
sie (she) |t
es (it) |t
wir (we) |en
ihr (y'all/you guys) |t
sie (they) |en
Sie (formal you, single or plural) |en  

One last thing to learn before we can put all this together. Whenever you look up a word in a German dictionary, you are given the full word (infinitive). Hidden inside the infinitive is a stem (this is not a technical term), and we need to know how to get the stem so we can construct sentences. If you look up the word 'to say' in a German dictionary, you find 'sagen'. German words, many times (every time? I can't recall) have an 'en' ending on the verb. To find the stem, we remove the 'en' ending. So, the stem of 'sagen' is 'sag'. Now, we simply put the ending on the stem. If I want to say 'I say' we construct it like this (participant stem+ending) to get ('Ich sag+**e** ' or 'Ich sag**e** '). To say 'you say' we get 'du sag**st** '. Do you see how the participant modifies the verb, now?  

Now that's all we need. Let's start constructing some sentences!  

So, we want to say 'I hit the goblin'. The subject is I (German: ich). Hit is 'schlagen' and goblin is 'der Kobold' in German (remember the article? Just remember to always include the article for nouns). So, we have the subject and that's the beginning part of the sentence 'Ich'. Now, we add the verb and it's ending (based on the participant) 'Ich schlage'. Lastly, the goblin (der Kobold) is the direct object of the sentence. So, we change the 'der' to the accusative form (which is 'den', based on the first table above) and we add that to the sentence to get 'Ich schlage den Kobold'. You just constructed your first sentence!  

Also, a last word. All nouns in German are capitalized. Ich (I) is only capitalized at the beginning of the sentence (unlike I in English). Remember, when you check your translation with a translator, you will find exceptions. Don't worry about them too much since they are probably aspects of the German language that are remote or old (for example, 'you hit' doesn't translate to 'du schlagst' but 'du schlägst'). As long as the basic form is right, then don't worry about it. It's good.  

## Messages

---

* En: "Welcome to Neohack."
* Fr: "Bienvenue sur Neohack."
* Pl: "Witaj w Neohack."
* De: "Willkommen zu NeoHack."
* Es: "Bienvenido a Neohack."(M), "Bienvenida a Neohack."(F), "Bienvenid@ a Neohack."(M+F)

<br/>

* En: "You hit the goblin."
* Fr: "Vous avez frappé le gobelin."
* Pl: "Trafiasz goblina." [goblin: Acc.]
* De: "Du schlägst den Goblin."
* Es: "Golpeas al duende."

<br/>

* En: "You killed the goblin."
* Fr: "Vous avez tué le gobelin."
* Pl: "Zabiłeś goblina." [goblin: Acc.]
* De: "Du hast den Goblin getötet."
* Es: "Has matado al duende."

<br/>

* En: "You cut the goblin's head off!"
* Fr: "Vous avez décapité le gobelin!"
* Pl: "Odcinasz goblinowi głowę!" [goblin: Dat., głowa: Acc.]
* De: "Du köpfst den Goblin!"
* Es: "Decapitas al duende."

<br/>

* En: "The goblin is slightly damaged."
* Fr: "Le gobelin est légèrement blessé."
* Pl: "Goblin jest lekko uszkodzony." [goblin: Nom.]
* De: "Der Goblin ist leicht verwundet."
* Es: "El duende está ligeramente dañado."

<br/>

* En: "The goblin is badly damaged."
* Fr: "Le gobelin est gravement blessé."
* Pl: "Goblin jest ciężko uszkodzony." [goblin: Nom.]
* De: "Der Goblin ist schwer verwundet."
* Es: "El duende está gravemente dañado."

<br/>

* En: "The goblin is wounded."
* Fr: "Le gobelin est blessé."
* Pl: "Goblin jest ranny." [goblin: Nom.]
* De: "Der Goblin ist verwundet."
* Es: "El duende está herido."

<br/>

* En: "The goblin hits you."
* Fr: "Le gobelin vous frappe."
* Pl: "Goblin trafia cię." [goblin: Nom.]
* De: "Der Goblin schlägt dich."
* Es: "El duende te golpea."

## Table of monster names and grammatical genders

---

En: | Fr: | Pl: | De: | Es:
---|---|----|----|----
beholder |spectateur(M) |obserwator(M) |der Beobachter |el espectador
cyclops |cyclope(M) |cyklop(M) |der Zyklop |el cíclope
demon |demon(M) |demon(M) |der Dämon |el demonio
devil |diable(M) |diabeł(M) |der Teufel |el diablo
djinni |djinn(M) |dżinn(M) |der Dschinn |el genio
dopplegänger |double(M) |sobowtór(M) |der Doppelgänger |el dopplegänger
dragon |dragon(M) |smok(M) |der Drache |el dragón
efreet |éfrit(M) |ifryt(M) |der Ifrit |el ifrit
elemental |esprit élémentaire(M) |żywiołak(M) |das Elementar |el elemental
evil eye |oeil maléfique(M) |złe oko(N) [a bit ridiculous...] |? |el ojo maléfico
familiar |familier(M) |chowaniec(M) |der Vertraute |el familiar
gargoyle |gargouille(F) |gargulec(M), rzygacz(M) |die Gargoyle |la gárgola
gelatinous cube |cube gélatineux(M) |galaretowaty sześcian(M) |der Gelatinewürfel |el cubo gelatinoso
ghost |fantôme |duch(M) |der Geist |el fantasma
ghoul |goule(F) |ghul(M) |der Ghul |el ghul
giant |géant(M) |olbrzym(M), gigant(M) |der Gigant, der Riese |el gigante
goblin |gobelin(M)|goblin(M) |der Goblin |el duende
golem |golem(M) |golem(M) |der Golem |el golem
gorgon |gorgone(F)|gorgona(F) |die Gorgo |la gorgona
griffin or gryphon |griffon(M) |gryf(M) |der Greif |el grifo
hippogriff |hippogriffe(M) |hipogryf(M) |der Hippogryph |el hipogrifo
homunculus |homoncule(M) |homunkulus(M) |der Homunculus |el homúnculo
imp |lutin(M) |chochlik(M) |das Teufelchen |el diablillo
kobold |kobold(M) |kobold(M) |der Kobold |el kobold
lich |liche(F) |lisz(M) |der Lich |el liche
mummy |momie(F) |mumia(F) |die Mumie |la momia
ogre |ogre(M) |ogr(M) |der Oger |el ogro
orc |orque(M) |ork(M) |der Ork |el orco
quasit |? |? |der Quasit |?
shapeshifter |métamorphe(M) |zmiennokształtny(M) |der Formwandler |el cambiante
skeleton |squelette(M) | szkielet(M) |das Skelett |el esqueleto
spectre |spectre(M) |zjawa(F), widmo(N) |das Gespenst |el espectro
troll |troll(M) |trol(M) |der Troll |el trol
unicorn |licorne(F) |jednorożec(M) |das Einhorn |el unicornio
vampire |vampire(M) |wampir(M) |der Vampir |el vampiro
vortex |vortex(M) |wir(M) |der Vortex |el vórtice
wight |âme(F) |? |der Wicht |el alma(F) en pena *
wyvern |vouivre(F) |wiwerna(F) |der Lindwurm |el guiverno

* [masculine article, but "alma" is actually feminine]  

## Table of race names and grammatical genders

---

En: |Fr: |Pl: |De: |Es:
----|----|----|----|----
dwarf |nain(M), naine(F) |krasnolud(M), krzat(M) |der Zwerg |el enano
elf |elfe(M) |elf(M) |der Elf |el elfo
gnome |gnome(M) |gnom(M) |der Kobold, der Gnom |el gnomo
half elf |demi-elfe(M) |półelf(M) |der Halbelf |el medio eelfo
halfling |halfelin(M) |niziołek(M) |der Halbling |el mediano
human |humain(M) |człowiek(M) |der Mensch |el hombre
tiefling |tieffelin(M) |diabelstwo(M) |der Tiefling |?

## Table of profession names and grammatical genders

---

En: |Fr: |Pl: |De: |Es:
----|----|----|----|----
archer |archer(M) |łucznik(M) |der Bogenschütze |el arquero
assassin |assassin(M) |zabójca(M), skrytobójca(M) |der Assassine |el asesino
barbarian |barbare(M) |barbarzyńca(M) |der Barbar |el bárbaro
cleric |clerc(M) |kleryk(M) |der Kleriker |el clérigo
fighter |guerrier(M) |wojownik(M) |der Kämpfer, der Krieger |el guerrero
healer |guérisseur(M) |uzdrowiciel(M) |der Heiler |el curador
monk |moine(M) |mnich(M) |der Mönch |el monje
necromancer |nécromancien(M) |nekromanta(M), nigromanta(M) |der Totenbeschwörer, der Nekromant |el nigromante
priest |prêtre(M) |kapłan(M) |der Priester |el sacerdote
thief |voleur(M) |złodziej(M) |der Dieb |el ladrón
warlock |sorcier(M) |czarnoksiężnik(M) |der Hexenmeister |el hechicero
wizard |magicien(M) |czarodziej(M) |der Zauberer |el mago
