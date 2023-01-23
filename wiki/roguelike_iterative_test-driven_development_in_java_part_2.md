# Roguelike Iterative Test-Driven Development in Java, Part 2

---

Architecture is the key to the success of a software engineering project. At least that's what the software architects say. In our case we don't have to worry about integration with existing systems, adherence to industry standards or legal compliance. In the business world that's a rare luxury. In the Roguelike world it's the status quo.

This roughly corresponds to [HWR15](how_to_write_a_roguelike_in_15_steps.md) Step 2.

## Goals

---

1. Define the technology needs of the project
2. Make build versus acquire decisions for those needs
3. Select acquisitions
4. Define work packages for builds

## Define Technology Needs

---

1. Implementation language that is cross-platform
2. Development environment that is cross-platform
3. Unit testing and reporting
4. Flexible, abstract and extensible console input and output
5. Cross-platform and easily human-writable file format
6. Game Engine

## Build Versus Acquire

---

I use the word "acquire" because I have zero budget for this project. This restricts our acquisition choices to free-as-in-bear products. A requirement of the article series is that the code be available on Google Code via SVN, so a free distribution license is also required.

### Implementation Language

Build an implementation language? If that's your thing it's a wonderfully fun exercise. I've written a few compilers in my day, and I'm in no mood to do it again. This is an easy acquire decision.

### Development Environment

There are plenty of great open-source IDE's available for just about every language in existence, so this is an easy acquire decision as well.

### Unit Testing

Depending on the selection of implementation language this could go either way. I'd prefer to acquire, obviously.

### Console Input and Output

Again this can go either way depending on the implementation language. It's also territory I've covered before and would prefer not to again.

### Human Writable File Format

In the past I have worked with just about every file format that claims to be "easily human-writable". Some of them simply aren't (I'm looking at you, XML). The rest may be convenient to write, but are never a perfect fit for the task at hand unless that task is trivial (such as a key-value store). In my last several projects I've written reams of code to make existing file formats fit the task. Since I have no compliance or integration requirements this time around I'll build my own.

### Game Engine

There are some very good game engines out there. There's even one in our problem domain (the [T-Engine](t-engine.md))! However the whole point of this exercise is to engineer our own, so we'll build.

## Select Acquisitions

---

Sometimes in designing a solution you want to acquire an existing product to fulfill a common technology need and find that a quality product is not available. It is in these times of strife that you must quit your day job, create your own product to fulfill that need and become the next penniless freelancer. Yet I digress.

### Implementation Language

This one is pretty easy. It's in the name of the article. I'll choose Java for the language. Not because it's my favorite language, but because I need more experience with it. I'll be working in Java for the next three years easily, and will likely be responsible for maintaining a substantial Java code base after that, so I'd better sure up my skills in my free time if I'm to be productive at work.

### Development Environment

At work I use Eclipse, therefore I need more experience with Eclipse, therefore I will use Eclipse. Easy enough.

### Unit Testing

Work with, need experience with, will use: JUnit 4.

### Console Input and Output

I wanted to use [libjcsi](libjcsi.md), a library written by a community member and very successful Roguelike developer [Slash](slash.md). Unfortunately libjcsi does not support background colors. Although the use of background color is not going to be terribly common I do like to use it to handle targeting, multiple items on the ground and other things.

I evaluated the other available Java Curses and console libraries without much luck. It's not just the background color issue, it's also the dependence on native code libraries for every platform. It makes packaging and porting harder.

So I'll develop my own Java console interface to fulfill the requirements of this project.

### Human Writable File Format

The file format parser(s) should be internal to the main game engine but localized into a self-contained classes. Parsers will be developed as-needed to fulfill the requirements of the project. No special effort should be made to reduce the number of file formats, as the intention is to tailor the file format to the task at hand.

#### Hard Requirements

1. All file formats must be plain-text
2. All file formats must tolerate UNIX or DOS-style line endings

Game Engine

The game engine will be built to fulfill the hard requirements of the project and the project goals.

## See Also

---

[Roguelike Iterative Test-Driven Development in Java](roguelike_iterative_test-driven_development_in_java.md)
