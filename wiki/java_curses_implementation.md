# Java Curses Implementation

---

"Java Curses Implementation"
andrew.d.joiner@googlemail.com

---

When designing and implementing a roguelike game, the most important design choice is the actual programming language used. There are many factors which will affect the choice of language, but usually it boils down to which language has a good enough curses library, and can handle blocked keyboard input. This programming requirement naturally leads to using the C-style programming languages, as there are a variety of curses libraries to choose from, such as [Ncurses](ncurses.md), [PDCurses](pdcurses.md) or even the Windows-32 Console API.

This choice of using a C style language is usually the beginning of the end for most roguelike projects. The C languages tend to become a nightmare to work with once the project expands beyond more than a few source files, as many veteran roguelike programmers will tell you.

Perhaps the easiest programming language to work with is Java, and certainly for roguelike development, there are many things Java can do so effortlessly that C programmers struggle for days figuring out! The Java API is outstanding, and allows a developer to get on with the implementation without having to keep reinventing the wheel, e.g., implementing linked lists for item piles, etc...

The big Java drawback (at least for Roguelike development) is its modern roots. It is really designed for GUI style applications, and nice event handing input that is common in GUI windowed programs. There is essentially no console mode support for Java. What is needed is curses functionality, but usable by Java. Enter the JNI!...

It was foreseen by Sun Microsystems when developing Java, that there would be times when non-standard functionality would be required by the developer. Certain things cannot be implemented in Java because of its cross-platform ideologies. One of these things is a curses implementation. With a bit of effort however, Java can have a very simple, functional curses implementation. The remainder of this article will show you how to implement a curses library in Java.

What you will need:

- Java JDK installed
- The PDCurses library (specifically the files "curses.h", "libcurses.a")
- MingW C/C++ compiler
- C/C++ IDE, such as Devcpp (optional)

At the bare minimum, a curses implementation should provide the following basic functionality;

- Setting the position of the cursor
- Printing a character to the screen
- Obtaining keyboard input from the user

Other functionality such as colours and clearing the screen will be covered here as well.

We begin by creating the Java JNI wrapper class for our curses implementation. JNI allows Java to access non-Java libraries at runtime, for example, a DLL library file compiled in C. The functionality contained in the non-Java library can be used by a native Java program. Be warned however, the use of the JNI is at the cost of platform independence.

---

**JniCurses.java (example implementation)**

```java
package jnicurses;
public class JniCurses {

     // constants (geometry)
     public static final int CONSOLE_X = 80;
     public static final int CONSOLE_Y = 50;

     // constants (colors)
     public static final int BLACK = 1;
     public static final int DARK_GRAY = 2;
     public static final int LIGHT_GRAY = 3;
     public static final int WHITE = 4;
     public static final int DARK_RED = 5;
     public static final int LIGHT_RED = 6;
     public static final int DARK_GREEN = 7;
     public static final int LIGHT_GREEN = 8;
     public static final int DARK_BLUE = 9;
     public static final int LIGHT_BLUE = 10;
     public static final int DARK_CYAN = 11;
     public static final int LIGHT_CYAN = 12;
     public static final int DARK_MAGENTA = 13;
     public static final int LIGHT_MAGENTA = 14;
     public static final int DARK_YELLOW = 15;
     public static final int LIGHT_YELLOW = 16;

     // dll library
     static { System.loadLibrary("JniCurses"); }

     // JNI native methods
     public native void init();
     public native void cls();
     public native void setCursor(boolean flag);
     public native void moveCursor(int x, int y);
     public native void setColor(int color);
     public native void print(char ch);
     public native int getch();

     // constructor()
     public JniCurses() {
         init();
     }
}
```

Most of this code should be fairly straight-forward. We are creating essentially prototype methods in this class, which will form our link to the C curses implementation. The JniCurses class is non-static, and therefore can be inherited by more sophisticated classes for performing higher level functionality, such as printing a String to the console. This is not covered in this article though.

Once the class is compiled, we need to use the javah.exe program supplied with the JDK on our JniCurses.java file. This program will create the C style header file we need for the next part of the implementation. Assuming all the correct paths are set, and you are using Windows, the following command will create the header:

```text
javah -jni -o JniCurses.h jnicurses.JniCurses
```

The header file which will be created should not be directly edited. If you alter the contents of the Java class, then run the javah command again to create a new, updated header. Next, we have to create the C source code for the library

---

**JniCurses.c (example implementation)**

```java
# include "stdio.h"
# include "JniCurses.h"
# include <curses.h>

// init()
JNIEXPORT void JNICALL Java_jnicurses_JniCurses_init(JNIEnv \* env, jobject obj) {
    // initialize standard curses features
    initscr();
    cbreak();
    noecho();
    keypad(stdscr, TRUE);
    curs_set(0);
    // set pre-definied colors
    start_color();
    init_pair(curses_JniCurses_BLACK, COLOR_BLACK, COLOR_BLACK);
    init_pair(curses_JniCurses_LIGHT_GRAY, COLOR_WHITE, COLOR_BLACK);
    init_pair(curses_JniCurses_DARK_RED, COLOR_RED, COLOR_BLACK);
    init_pair(curses_JniCurses_DARK_GREEN, COLOR_GREEN, COLOR_BLACK);
    init_pair(curses_JniCurses_DARK_BLUE, COLOR_BLUE, COLOR_BLACK);
    init_pair(curses_JniCurses_DARK_CYAN, COLOR_CYAN, COLOR_BLACK);
    init_pair(curses_JniCurses_DARK_MAGENTA, COLOR_MAGENTA, COLOR_BLACK);
    init_pair(curses_JniCurses_DARK_YELLOW, COLOR_YELLOW, COLOR_BLACK);
}

// cls()
JNIEXPORT void JNICALL Java_jnicurses_JniCurses_cls(JNIEnv \* env, jobject obj) {
   int x, y;
       for (y = 0; y < 50; y++) {
           for (x = 0; x < 80; x++) {
            move(y, x);
            echochar(' ');
           }
   }
}

// setCursor()
JNIEXPORT void JNICALL Java_jnicurses_JniCurses_setCursor(JNIEnv \* env, jobject obj, jboolean flag) {
    if (flag) { curs_set(1); } else { curs_set(0); }
}

// moveCursor()
JNIEXPORT void JNICALL Java_jnicurses_JniCurses_moveCursor(JNIEnv \* env, jobject obj, jint x, jint y) {
    move(y, x);
    refresh();
}

// setColor()
JNIEXPORT void JNICALL Java_jnicurses_JniCurses_setColor(JNIEnv \* env, jobject obj, jint color) {
    wattroff(stdscr, A_BOLD);
    if (color == curses_JniCurses_LIGHT_RED) {
    wattron(stdscr, COLOR_PAIR(curses_JniCurses_DARK_RED) | A_BOLD);
    } else if (color == curses_JniCurses_LIGHT_GREEN) {
    wattron(stdscr, COLOR_PAIR(curses_JniCurses_DARK_GREEN) | A_BOLD);
    } else if (color == curses_JniCurses_LIGHT_BLUE) {
    wattron(stdscr, COLOR_PAIR(curses_JniCurses_DARK_BLUE) | A_BOLD);
    } else if (color == curses_JniCurses_LIGHT_CYAN) {
    wattron(stdscr, COLOR_PAIR(curses_JniCurses_DARK_CYAN) | A_BOLD);
    } else if (color == curses_JniCurses_LIGHT_MAGENTA) {
    wattron(stdscr, COLOR_PAIR(curses_JniCurses_DARK_MAGENTA) | A_BOLD);
    } else if (color == curses_JniCurses_LIGHT_YELLOW) {
    wattron(stdscr, COLOR_PAIR(curses_JniCurses_DARK_YELLOW) | A_BOLD);
    } else if (color == curses_JniCurses_DARK_GRAY) {
    wattron(stdscr, COLOR_PAIR(curses_JniCurses_BLACK) | A_BOLD);
    } else if (color == curses_JniCurses_WHITE) {
    wattron(stdscr, COLOR_PAIR(curses_JniCurses_LIGHT_GRAY) | A_BOLD);
    } else {
    wattron(stdscr, COLOR_PAIR(color) | A_NORMAL);
    }
}

// print()
JNIEXPORT void JNICALL Java_jnicurses_JniCurses_print(JNIEnv \* env, jobject obj, jchar ch) {
    addch(ch);
    refresh();
}

// getch()
JNIEXPORT jint JNICALL Java_jnicurses_JniCurses_getch(JNIEnv \* env, jobject obj) {
    flushinp();
    int ch = getch();
    return ch;
}
```

The final stage is to compile the library, and test it out in Java. Assuming all paths have been set correctly, and the three files "JniCurses.c", "JniCurses.h" and "libcurses.a" are all in the same folder, the following command will build the DLL

```text
gcc -lcurses -mno-cygwin -I$jdk/include -I$jdk/include/win32 -Wl,--add-stdcall-alias -shared -o JniCurses.dll JniCurses.c libcurses.a
```

If all has gone successfully, a new DLL called "JniCurses.dll" should have been created, and the Java PDCurses implementation is complete. Remember that the JniCurses.dll file should be in the root folder of any Java project you create, unless you specifically set the classpath variable. With this implementation, you can extend the functionality of JniCurses quite easily, for example:

---

**Gui.java (example implementation)**

```java
package jnicurses;
public class Gui extends JniCurses {

    // constructor()
    public Gui() {
        super();
    }

    // method: printString()
    public void printString(int x, int y, int color, String string) { /*...*/ }
}
```

Using the actual library is straight-forward. Simply create a new JniCurses object, or extend the JniCurses class;

```java
JniCurses curses = new JniCurses();
curses.cls();
curses.setColor(LIGHT_GREEN);
curses.moveCursor(2, 4);
curses.print('@');
int key = curses.getch();
// etc...
```

For more information on JNI, including detailed tutorials, visit: <http://www.inonit.com/cygwin/jni/helloWorld/>
