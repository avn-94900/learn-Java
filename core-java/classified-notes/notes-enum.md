# Java Enums 

## 1. Basics and Definition

* Enum is a **special type of class** used to represent a fixed set of constants
* Declared using the `enum` keyword
* Cannot be instantiated using `new`
* Implicitly extends `java.lang.Enum`
* Used when values are **fixed and known at compile time**

```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY
}
```

---

## 2. Enum Constants

* All constants are implicitly:

  * `public`
  * `static`
  * `final`

```java
enum Day {
    MONDAY, TUESDAY;
}
```

Key Points:

* Constants are **singleton instances**
* Cannot create new constants at runtime
* Fixed at compile time

---

## 3. Variables and Fields in Enums

* Enums can have:

  * Instance variables
  * Static variables
  * Final variables
  * Private variables

```java
enum Status {
    SUCCESS, FAILURE;

    int code;              // instance variable
    static int count = 0;  // static variable
}
```

---

## 4. Methods in Enums

### Allowed Methods:

* Instance methods
* Static methods
* Private methods
* Abstract methods (with rules)

```java
enum Test {
    A; // At least one constant is required (unless you use ; for empty enum)

    void instanceMethod() {}

    static void staticMethod() {}

    private void helper() {}
}
```

---

## 5. Constructors in Enums

### Rules:

* Constructors are **always private** (implicitly or explicitly)
* Cannot be public or protected
* Used to initialize fields

```java
enum Day {
    MONDAY("Start"), TUESDAY("Work");

    String type;

    private Day(String type) {
        this.type = type;
    }
}
```

Reason:

* Prevents creation of new instances outside enum

---

## 6. Abstract Methods and Constant-Specific Behavior

* Enums can declare abstract methods
* **All constants must implement them**

```java
enum Operation {

    ADD {
        @Override
        public int apply(int a, int b) {
            return a + b;
        }
    },

    SUBTRACT {
        @Override
        public int apply(int a, int b) {
            return a - b;
        }
    },

    MULTIPLY {
        @Override
        public int apply(int a, int b) {
            return a * b;
        }
    },

    DIVIDE {
        @Override
        public int apply(int a, int b) {
            if (b == 0) {
                throw new ArithmeticException("Cannot divide by zero");
            }
            return a / b;
        }
    };

    // Abstract method
    public abstract int apply(int a, int b);
}

public class Main {
    public static void main(String[] args) {

        int a = 10;
        int b = 5;

        // Using enum constants
        System.out.println("ADD: " + Operation.ADD.apply(a, b));
        System.out.println("SUBTRACT: " + Operation.SUBTRACT.apply(a, b));
        System.out.println("MULTIPLY: " + Operation.MULTIPLY.apply(a, b));
        System.out.println("DIVIDE: " + Operation.DIVIDE.apply(a, b));

        // Loop through all operations
        System.out.println("\nUsing loop:");
        for (Operation op : Operation.values()) {
            System.out.println(op + " => " + op.apply(a, b));
        }
    }
}
```

Key Rule:

* If one constant implements, **all must implement**

---

## 7. Inheritance and Implementation Rules

* Enums:

  * Cannot extend any class
  * Can implement interfaces

Reason:

* Already extends `java.lang.Enum`

```java
interface Test {}

enum Sample implements Test {
    A, B;
}
```

---

## 8. Built-in Methods

### Common Methods:

* `values()` → returns all constants
* `valueOf(String)` → converts string to enum
* `ordinal()` → index (starts from 0)
* `name()` → exact constant name

```java
Day d = Day.valueOf("MONDAY");
System.out.println(d.ordinal()); // 0
```

---

## 9. Method Overriding Rules

### Cannot override methods of Enum class

* Because `java.lang.Enum` is final

### Can override methods from Object:

* `toString()`
* `hashCode()`
* `equals()`

```java
enum Color {
    RED;

    @Override
    public String toString() {
        return "Custom RED";
    }
}
```

---

## 10. Usage with switch, EnumSet, EnumMap

### Switch Example:

```java
switch(day) {
    case MONDAY:
        System.out.println("Start");
}
```

### EnumSet:

* Efficient set for enums

```java
EnumSet<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);
```

### EnumMap:

* Efficient map with enum keys

```java
EnumMap<Day, String> map = new EnumMap<>(Day.class);
```

---

## 11. Serialization Behavior

* Enums are **serializable by default**
* Maintain singleton property during deserialization
* No need for special handling

---

## 12. Advantages and Limitations

### Advantages:

* Type safety
* Readable code
* Singleton-like behavior
* Can have fields and methods
* Works well with switch
* Built-in serialization

### Limitations:

* Cannot extend classes
* Fixed constants (no dynamic addition)
* Cannot be cloned
* Less flexible for dynamic data

---

## Important Rules for Revision

* Enum = special class with fixed constants
* Constants = public static final
* Constructor = always private
* Cannot extend class (already extends Enum)
* Can implement interfaces
* Can have abstract methods, but all constants must implement
* Cannot create objects using `new`
* Built-in methods are frequently asked in exams

---

<br/><br/><br/>

## Examples

### Example 1: Basic Enum Usage

```java
import java.util.EnumSet;
import java.util.EnumMap;
import java.util.Map;

public class EnumExample {

    // Define enum Day with constants
    enum Day {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
    }

    public static void main(String[] args) {
        // 1. values() - Get all enum constants
        System.out.println("All days:");
        for (Day day : Day.values()) {
            System.out.print(day + " ");
        }
        System.out.println("\n");

        // 2. valueOf() - Convert string to enum constant
        Day d = Day.valueOf("MONDAY");
        System.out.println("valueOf(\"MONDAY\") returns: " + d);  // valueOf("MONDAY") returns: MONDAY

        // 3. ordinal() - Get index of enum constant
        System.out.println("Ordinal of MONDAY: " + Day.MONDAY.ordinal()); // 0
        System.out.println("Ordinal of SUNDAY: " + Day.SUNDAY.ordinal()); // 6

        // 4. name() - Get exact name of enum constant
        System.out.println("Name of Day.FRIDAY: " + Day.FRIDAY.name());

        System.out.println();

        // Using EnumSet for weekend days
        EnumSet<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);
        System.out.println("Weekend days using EnumSet:");
        for (Day day : weekend) {
            System.out.print(day + " ");
        }
        System.out.println("\n");

        // Using EnumMap to map days to tasks
        EnumMap<Day, String> tasks = new EnumMap<>(Day.class);
        tasks.put(Day.MONDAY, "Write report");
        tasks.put(Day.FRIDAY, "Submit report");

        System.out.println("Tasks mapped to days using EnumMap:");
        for (Map.Entry<Day, String> entry : tasks.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```




### Example: Main Method in Different Structures

```java
class A {
    public static void main(String[] s) {
        System.out.println("A-main");
    }
}

abstract class B {
    public static void main(String[] s) {
        System.out.println("B-main");
    }
}

enum C {
    ;  // Empty enum body
    public static void main(String[] s) {
        System.out.println("C-main");
    }
}

interface K {
    public static void main(String[] s) {
        System.out.println("K-main");
    }
}
```

> All of these classes can be compiled and their main methods can be executed.


<br/><br/>
<br/>

## Interview questions

### Can enums override methods from `java.lang.Enum`?
**No**, enums **cannot override** methods from `java.lang.Enum` because it's a **final class**.

### Can enums override methods from `java.lang.Object`?
**Yes**, enums in Java **can override methods from `java.lang.Object`**, such as:
- `toString()`
- `equals()`
- `hashCode()`
- `clone()` (though enums can't be cloned)
- `finalize()` (deprecated)

Example:
```java
enum Color {
    RED, GREEN, BLUE;

    @Override
    public String toString() {
        return "Color: " + name();
    }
}
```

[Back to Top](#table-of-contents)