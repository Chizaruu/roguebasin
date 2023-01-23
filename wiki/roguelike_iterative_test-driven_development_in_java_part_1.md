# Roguelike Iterative Test-Driven Development in Java, Part 1

---

Initiation, corresponding to [HWR15](how_to_write_a_roguelike_in_15_steps.md) step 1.

## Goals

---

1. Define the goal of the project
2. Define the scope of the project
3. Define how to measure success
4. Define how to measure failure

## Define Project Goals

---

My goal is to create a cross-platform text-based video game with an original (albeit contrived) fantasy setting. Game play should be paced slowly, giving time and reason for thought. Each play scenario should be meaningful to the overall game session. The game as a whole should be simple to understand yet challenging to complete, and should offer replay-ability. The game must be cross-platform, portable and self-contained. The game should be easily modifiable through external configuration and definition files.

### Hard Requirements

1. Text and keyboard only (because it's my choice :P )
   1. Must be playable over Telnet / SSH
   2. Must be playable on any system with an ANSI-compatible terminal emulator
   3. Must be playable on any system regardless of terminal emulator
2. Must support saving and loading of games
3. Must support recording and playback of games and partial games
4. Game and all supporting or produced files must be cross-platform

## Define Project Scope

---

The scope of this project will include creation of the game and it's configuration and extension capabilities and all hard requirements. Scope may be later extended to include optional requirements.

## Define Measure of Success

---

To be considered a success the project must adhere to the hard requirements and stay within the defined goals and scope of the project. The measure of success will be based solely on how much the author enjoys the game.

## Define Measure of Failure

---

The project will be considered a failure if it does not meet any hard requirement or is not enjoyable to the author as a game.

## See Also

---

[Roguelike Iterative Test-Driven Development in Java](roguelike_iterative_test-driven_development_in_java.md)
