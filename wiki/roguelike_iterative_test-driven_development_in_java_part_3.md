# Roguelike Iterative Test-Driven Development in Java, Part 3

In this part we finally get to write some code! This part corresponds to [HWR15](how_to_write_a_roguelike_in_15_steps.md) Part 3. In a traditional waterfall development pattern this is the easiest part to implement because it's the most basic. It's also the time during which you have the most opportunity to make a design mistake that will cause you grief later on.

## Requirements

1. Screen Output
1. Keyboard Input
1. Main Menu
1. Game Mode
   1. Message Display
   2. Test Character Display
   3. Movement Keys

Personally I like to start with a main menu. It seems like when I design a game starting with the game mode it's harder to add a menu later.

## Design

The main menu and game modes are part of our view layer, or the user-facing interface. Our view layer has no functional requirements, only aesthetic requirements: does it look good, is it intuitive, is all pertinent information displayed, etc. Aesthetic requirements are tested in the acceptance testing phase by the customer who, in this case, will be me. We'll use an abstract base class to provide common functionality.

Screen output has already been abstracted for us by [libjcsi](libjcsi.md). Unfortunately so has keyboard input, and it's part of the same abstraction layer. We need to provide an abstraction of user input that is not directly tied to the keyboard so we can support key bindings and macros later on. We also need to expose the raw input for special situations, like naming a character or selecting items by letter. We won't be doing unit tests that are driven by input (because we're not unit testing the game modes) so we won't need a testing layer for this.

Surprisingly enough this leaves us with nothing to unit test.

### Classes

- **com.qbradq.durok.lib.ConsoleSystem** - Class wrapping our screen output and keyboard input services and includes our input abstraction
- **com.qbradq.durok.game.GameMode** - Interface for anything that enters an input consumption loop
  - **com.qbradq.durok.game.MainMenuMode** - The main menu
  - **com.qbradq.durok.game.MainGameMode** - The main game interface

### Screen Mockups

Main Menu

```text
+------------------------------------------------------------------------------+
|                                                                              |
|                               The Halls of Durok                             |
|                                                                              |
|                         by Norman B. Lancaster (QBRADQ)                      |
|                                                                              |
|                   n) Start a New Game                                        |
|                   r) Resume your Last Game                                   |
|                   q) Quit                                                    |
|                                                                              |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
| Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro Intro      |
|                                                                              |
+------------------------------------------------------------------------------+
```

Main Game UI

```text
+------------------------------------------------------------------------------+
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|################################# |
|Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Lo|
|Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Lo|
|Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Lo|
|Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Lo|
|Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Lo|
|Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Lo|
|Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log Log L |
+------------------------------------------------------------------------------+
```

## Tests

None. This felt very awkward to me, but after some thought I realized that even though we don't have a unit test yet we are still driving the development cycle by considering our tests first.

We will still need to do acceptance testing at the end of the development cycle.

## Implementation

I started by creating a Google Code project page for the project. Then I started Eclipse, installed the Google plugins, imported the project page and created a new Java project. I then brought in my dependencies and fiddled with things until the libjcsi examples worked.

## See Also

[Roguelike Iterative Test-Driven Development in Java](roguelike_iterative_test-driven_development_in_java.md)
