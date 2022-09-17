# Open Source

---

## Definition

---

The various Free Software/Open Source licenses are covered in brief under [Licensing](../licensing.md).

* Source code available, but distribution of modifications is restricted (this does not qualify under the free software definition). While people have access to the source code (so they can do ports, preserve the work, etc), they do not have permission to produce derivative works (make variants, sell for profit, etc). While this may seem to be a safe middle of the road choice, in practice you should not expect your wishes to be followed. If your game is popular enough to spawn variants, people will write them despite your wishes. It is thus suggested you save yourself the heartache and either resign yourself to "Hack With Space Orcs" being written or keep it closed source.
* True Free Software/Open Source license: Source code available, can make derivatives, but must release new source (copyleft). This is the GNU GPL definition of Free Software. When applied to a finished game, this restricts third parties ability to repackage and resell your game without providing the source for the modifications. Any repackaging they do must be made available for you to reintegrate into the main baseline, preserving your ability to have the definitive version.
* Source code available, can make derivatives, little other restrictions (non-copyleft free software/open source). This is the BSD/MIT interpretation of Open Source. Note it allows a third party to release a closed source version of your game, hiding from you and others any changes they performed. This is a useful option if you produce an engine where you see the majority of the work being done by the secondary developer. It then lets the person who does the most work decide on licensing.

## Pros

---

* Attracting Contributors. Publishing your source code under and appropriate liscense is your best chance to attract other developers to your project (other than actually paying them). You might attract people with skills and abilities that you lack.
* [Portability Issues](../implementation/portability_issues.md). You do not need to own all the platforms that you want to port to. People on those platforms can do their own ports and give you the needed changes.
* Preservation. By releasing the source code, you insulate yourself against catastrophic data failure. The GLQuake movement famously moved to GPL licenses after one of the key programs was lost in a hard drive crash. To maximize this effect, it is recommended you include the source code in the standard download so as to ensure all players end up with a copy by default.
* Survivability. If your roguelike only exists as a binary, it will become harder and harder for future computers to run it. Source code helps ensure native versions are available.
* Bug Fixes. Ideally, when users encounter crashing bugs, they can give you the bug fix rather than just reporting the problem.
* New Features. Eager users may develop patches that add cool new features that you can then add to your game.
* Variants. People may branch off producing entire new roguelikes with your system used as an engine, enriching the roguelike scene.
* Can use GPL code. If you are releasing under GPL, you can use GPL code in your program. This isn't so much a "Pro" as an forced prerequisite of using the code, however.
* Community Approval. There is a strong pro-open source component to the roguelike community that will favour any roguelike with sources and disfavour ones that lack sources.

## Cons

---

* The Pros Don't Apply. Many of the Pros only apply to already successful projects. Variants, New Features, Bug Fixes, will only be submitted if you have an active userbase large enough to contain technically qualified people.
* Rejecting Changes is Hard. You have your own vision for the game. It is hard enough dealing with requests to change the game in ways you didn't envision, without having to deal with requests containing the correct source code to make the changes.
* Licensing is Confusing. What are the implications of the different licenses? What licenses are implied with submitted patches? What if I want to release a commercial version one day? These all have answers, but researching them is time not spent writing your roguelike.
* Closed Source is Successful. Contrary to the Community Approval note, [ADOM](../../game/adom.md) proves available sources isn't a prerequisite for success.
* Want Data Secret. Providing the source means providing your build process, which opens up all the secrets of your game to source divers. While, arguably, the secrets were already available to competent disassemblers, they are a lot fewer than source divers. Some hold [ADOM](../../game/adom.md) as an example of this working. However, all the important secrets were eventually routed out and posted. To the spoiled player (who cares not *how* the secrets were obtained, as they just read the spoiler file) the result is just a delay in the availability of spoilers.
* Releasing Source is Hard. Releasing your roguelike is difficult enough without also having to release the source code. One will often want to do *some* cleanup. One may also want to document the build process so it is at all useful to outsiders. Any tools used for building need to be released or documented as well.
