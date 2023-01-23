# Items in Java

---

**"The Java Roguelike Development Guide"**
_Issue Two - Items in Java
andrew.d.joiner@googlemail.com_

---

One of the core features of any roguelike game are Items. Things you can pickup, use, drink, equip, drop, etc... In Java, using the OO inheritance model, we can create elegant, efficient and very easy to use Item classes. Start by deciding the types of Item that will be available in the game. More can be added easily later on. For this example, the following Item types will be used:

- Food
- Potion
- Weapon
- Armour
- Junk

Each of these items will have shared properties, and unique properties. The key is to determine which properties are common for all items, and which are unique to just a single item. To keep this simple, we will take the following properties to be common for every item type;

- Name (String)
- Colour (int)
- Symbol (char)
- Weight (int)
- Value (int)

The root class for all of these items will be called **Item**, and contains the common properties for every item type, as listed above.

```java
public class Item {

    // class data
    public String name;
    public int color;
    public char symbol;
    public int weight;
    public int value;

    // constructor()
    public Item() {
    }
}
```

Using inheritance, we can create sub-classes of Item, for each of our item types;

```java
public class Food extends Item {
// methods for Food class
}
public class Potion extends Item {
// methods for Potion class
}
public class Weapon extends Item {
// methods for Weapon class
}
public class Armour extends Item {
// methods for Armour class
}
public class Junk extends Item {
// methods for Junk class
}
```

Each of these classes contain their own data, relevant for just that item type. We can improve the model further by realising that Food and Potion are similar item types. Similar in the sense that same actions can be performed on similar items, for example, instances of both Food and Potion can be consumed by the Player while all instances of the class Item can be applied. The similarity of these classes can be expressed using common `interface`s. An interface is a special class which can contain only constants and method signatures (although default and static methods are allowed). When a class `implements` an interface, it's like a binding contract, the class has to define all the methods the interface declares. A class can implement an arbitrary number of interfaces while it can inherit only from one.

So we will create an interface `Consumable` to realise the common features of both Food and Potion classes. This interface only declares a single method `consume()` which returns nothing and takes no arguments (you can change this depending on your needs).

```java
public interface Consumable {
    public void consume();
}
```

Now we will make the classes we declared earlier implement this interface. Note that when we implement an interface we will have to provide a body for each method the interface declares. So each class which implements this will have to provide the `consume()` method.

```java
public class Food extends Item implements Consumable {
    public void consume() {
    // code;
    }
    // additional methods...
}
public class Potion extends Item implements Consumable {
    public void consume() {
    // code;
    }
    // additional methods...
}
```

An improvement to the Item class at this point can be made. A series of macro methods to determine what Item type we are dealing with;

```java
// item methods
public boolean isConsumable() { return this instanceof Consumable; }
public boolean isFood() { return this instanceof Food; }
public boolean isPotion() { return this instanceof Potion; }
public boolean isWeapon() { return this instanceof Weapon; }
public boolean isArmour() { return this instanceof Armour; }
public boolean isJunk() { return this instanceof Junk; }
```

Finally, copy-constructors can be used to make deep-level copies of any Item. This is achieved by making a second constructor method in each class, and implementing the copy functionality. Here it is demonstrated for the Item class;

```java
public Item(Item i) {
    this.name = i.name;
    this.color = i.color;
    this.symbol = i.symbol;
    this.weight = i.weight;
    this.value = i.value
}
```

The same example for a sub-class of Item;

```java
public Armour(Armour a) {
    super((Item) a);
    // set specific armour data here
}
```

Hopefully this article will give you some ideas when implementing you're roguelike Items in Java. The beauty of this approach, is that a new item type can easily be added without much effort. Simple extend the Item class, implement the new method, then add its tester macro method to the Item class.
