# Roguelike Iterative Test-Driven Development in Java, Part 2a

During the build versus acquire decisions I wanted to use an existing Java console library, but found the existing options lacking. So now I've got to build one, and that maps neatly to the later part of Step 2 in [HWR15](how_to_write_a_roguelike_in_15_steps.md).

## Requirements

The venerable Curses is much more than a console interface, it's a text-based GUI toolkit. That's a lot more functionality than I actually need for a Roguelike. With that in mind I am going to model the library, which I am calling [libjcio], after conio.h and adding foreground and background color support.

Functional Requirements:

1. Platform Support Targets
   1. ANSI-compatible terminals (with xterm/eterm extensions)
   2. Microsoft Windows command prompt
   3. Java Swing
2. Required Functionality
   1. Clear the screen
   1. State-based foreground and background colors
   1. Full 16-color support
   1. Locate cursor
   1. Put strings or single characters
3. Optional Functionality
   1. Set console title
4. Domain-specific functionality
   1. Consumer-provided input filtering

## Design

A single service-providing class will be exposed to the consumer to provide a complete abstraction of the console environment. Interfaces will be provided for mix-in objects used to hook key events.

### Classes

- com.qbradq.libjcio - Library package
  - ConsoleInputOutput - Console service provider and abstraction layer
  - ConsoleProviderInterface - Interface for platform-specific console provider
  - ANSIPlatformProvider - Console provider for ANSI-compatible terminals
  - WindowsPlatformProvider - Console provider for the Windows command prompt
  - SwingPlatformProvider - Console provider for the Swing toolkit
  - InputEventFilterInterface - Interface for consumer-provided input event filter
  - InputEvent - Class describing an input event

### Test Cases

- com.qbradq.libjcio.test - Unit test package
  - AllPlatformProviders - Tests all platform providers that can run on the current platform covering every functional requirement
  - InputEventFiltering - Tests input event filtering
  - TestInputEventFilter - Test input event filter for the above test class

## Implementation

## See Also

[Roguelike Iterative Test-Driven Development in Java](roguelike_iterative_test-driven_development_in_java.md)
