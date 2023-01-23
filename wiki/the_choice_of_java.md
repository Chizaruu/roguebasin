# The Choice of Java

---

**"The Java Roguelike Development Guide"**
_Issue One - The Choice Of Java
andrew.d.joiner@googlemail.com_

---

It is fair to say that the vast majority of roguelike games are implemented in the C/C++ programming language. C/C++ are very common languages that most programmers know how to use, it has a variety of curses (or curses-like) libraries to choose from, and a fairly extensive list of functionality and is very portable across platforms.

It is not common, however, to find many roguelike games which have been programmed in the Java programming language. The big problem with Java, of cause, is its modern GUI sensibilities. There is no official curses support in Java, and the event model is strictly limited to non-blocking events. However, if these deficiencies can be removed, then Java becomes a viable roguelike programming language, and in fact, becomes one of the better prospects for long-term roguelike development. See [Java Curses Implementation](java_curses_implementation.md) for more information on solving this Java problem.

But be warned that although the Java-Curses solution offers a good way to bring Java closer to the console and adds the flexibility of the [curses](curses_library.md) library, it is a native solution. In other words it is platform-dependent and will break the platform-independence that Java is offers. You'll have to create separate builds for different platforms not to mention that your program will have to depend on a native library; and that's why it would be a good idea to look for other pure Java options. And there are many; In the recent years many Java-based roguelike libraries have been developed. Good examples include [Blacken](blacken.md) and [SquidLib](squidlib.md).

You can create a Swing (or JavaFX) GUI application yourself which emulates the way a curses-like application behaves ([libjcsi](libjcsi.md) does exactly that but it has become out-of-date because of very little to no source updates over the past few years). This is perhaps a better idea once you get your GUI to behave as a regular roguelike should.

So the question is, why Java? One reason is the excellent object-orientated paradigm that Java takes right to its core. OO-Programming is essential for creating larger scale programs, because it is more intuitive and easier to maintain. Things that were are a nightmare to represent in C, such as an efficient Item data structure, are a breeze in Java.

The Java API is also excellent. Many foundation classes are provided, which allow the programmer to get on with implementation, rather than keep reinventing the wheel. Linked Lists, Vectors, ArrayLists, Hashes, Maps, Trees, Random and SecureRandom Numbers, Strings, etc. While all of these things can be implemented in the C languages, in Java they are already implemented, and very easy to use. There is a Collection-API for stuff like Lists and Maps, Generics for added type-safety, a vast I/O api makes pipelining and streams much easier, a package system for organizing code, JFC (Java-Foundation-Classes) and Swing, the (relatively new) JavaFX api to create rich user-interfaces. While a versatile Object-Oriented system allows for OO programming without compromising performance, a strong, static type system which prevents many unintended operations.

Some of the OOP features include:

- The Class system of java is flexible and extensible, as a result, Inner-Classes, Nested-Classes, Local and Anonymous classes are available. Abstract classes are like interfaces with the exception that they can be inherited from. Enumerations are based on a class system, and can act as a class.
- Implementable interfaces instead of virtual-inheritance, and the avoidance of multiple inheritance. Interfaces can be used as a type, thus preventing the need for using container classes and it substitutes the need for multiple-inheritance.
- Lambda-Expressions and the Stream-API which adds support for functional-programming to Java. These two are perhaps the biggest features which were added to the Java Programming Language in Java-SE 8.

See <https://docs.oracle.com/javase/tutorial/java/javaOO> for more information.

The following code shows how easy it is to use a Linked List ADT in java;

```java
import java.util.LinkedList;
public class ListExample extends LinkedList {
    // constructor()
    public ListExample() {
        super(); // call the constructor of the super-class
        // create a new Object reference
        Object o = new Object();
        // add to the list
        this.addFirst(o);
        // remove from list
        this.removeFirst(o);
    }
}
```

This is just a crude example of how easy Linked Lists are to use in Java. This ability is all based on the fact that every Java class is a sub-class of Object. Object is the root Java class, and because Java has very strong OO features, all classes inherit from Object.

The first-line shows the `import` statement which imports a class from a package for use in the current package. A package is a kind of namespace in which classes are contained. Packages are virtual to Java but share a one-to-one mapping to the file system, i.e., if a class FooBar00 is present in the package foo.bar.baz then the source file FooBar00.java must be contained in the path `./foo/bar/baz`.

The line `public class...` declares a class called ListExample. The `public` keyword indicates that the class has public-level access, i.e., all classes (even in other packages) can access it. The next line is a constructor which is a special kind of method which is invoked when an object is instantiated.

The line `Object o = new Object();` creates an instance of the class Object and assigns its reference to the variable 'o'. There are no pointers in Java; Objects stored on the heap are assigned references which are like pointers but more restricted in the sense that they cannot be made to point at any arbitrary location.

ArrayLists are arbitrary-length arrays, just like Vectors but without the synchronizing overhead. The following code shows how to use an ArrayList in java:

```java
import java.util.ArrayList;

public class ArrayListExample {
    public static void main(String[] args) {
        // instantiate an array-list with Integer (its the wrapper class for int-datatype)
        ArrayList<Integer> arrayList = new ArrayList<Integer>();

        // add some data
        arrayList.add(10);
        arrayList.add(20);
        arrayList.add(30);

        // remove the element at index-0
        arrayList.remove(0);

        // clear the arrayList
        arrayList.clear();
    }
}
```

Java is a recommended programming language to learn, especially for roguelike programming, since it is so straight-forward to use. The algorithmic syntax of Java is almost identical to C, which makes it very easy to learn Java if you already understand C. The basic Java algorithmic constructs are the same as C;

- if, else if
- for
- do, do while, while, break
- switch, case, break

For example, a for statement in Java

```text
String string = "0123456789";
for (int y = 0; y < 10; y++) {
    System.out.print(string.charAt(y));
}
```

The syntax of the for statement should look familiar to a C programmer, except that the loop-variable can be declared inside the loop instead of outside it. In Java-6 there is support for a kind of foreach construct similar to the for-loop but more simpler and readable. The same example given above can be more efficiently implemented using a foreach loop like this:

```text
String s = "0123456789";
for (char c : s.toCharArray()) {
    System.out.println(c);
}
```

The toCharArray() is a method (like a function in C) which converts the String into a char-array (a char[]). In this foreach construct the loop traverses over the char-array and assigns the current element to the variable c, the println() method of the System.out instance prints the variable to the Standard-Output.

For more information on Java programming, there are many excellent tutorials available online:

- The best would probably be the official one from Oracle: <http://docs.oracle.com/javase/tutorial/>
- There's also an introductory tutorial on MIT Open-Course (It uses Think-Java which is available as a Open-Book online): <http://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-092-introduction-to-programming-in-java-january-iap-2010>
- Thinking in Java is a free e-book by Bruce Eckel. It goes into the depths of the Java Programming Language, its got a high amount of detail but it makes sure that you learn every bit about Java by the time you finish this book, that's why its not recommended as your first read. Link: <http://www.saeedsh.com/resources/Thinking%20in%20Java%204th%20Ed.pdf>
