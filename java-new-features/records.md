
## Records in Java

### What are Records?

| Aspect | Details |
|--------|---------|
| **Definition** | Immutable data carriers that reduce boilerplate code |
| **Introduced** | Java 14 (Preview), Java 16 (Final) |
| **Keyword** | `record` |
| **Purpose** | Simplify creation of classes that hold data |

---

### Key Features
- Automatically generates `equals()`, `hashCode()`, `toString()`
- All fields are `private final` by default
- Compact constructor support for validation
- No inheritance (records implicitly extend `Record`)
- Cannot declare instance fields except components

### Field Declaration
- Declared in parentheses after record name
- Automatically generates getters (no `get` prefix)
- All fields are immutable after construction

### Useful Methods
- `equals()` - compares all fields
- `hashCode()` - based on all field values
- `toString()` - shows record name and field values
- `getters` - automatically generated for each field

## Basic Syntax

```java
public record Person(String name, int age) {
    // implementation
}

public record Point(double x, double y) {
    // compact constructor
    public Point {
        if (x < 0 || y < 0) {
            throw new IllegalArgumentException("Coordinates must be non-negative");
        }
    }
}

public record Employee(String id, String name, double salary) {

    public Employee(String id, String name, double salary) {

        if (id == null || id.isBlank())
            throw new IllegalArgumentException("Id cannot be empty");

        if (salary < 0)
            throw new IllegalArgumentException("Salary cannot be negative");

        this.id = id;
        this.name = name;
        this.salary = salary;
    }
}
```

---

## Rules for Records

Records **must** follow constraints:

| Constraint | Details |
|-----------|---------|
| Immutability | All fields are `final` |
| No Instance Fields | Cannot add extra instance fields |
| No Inheritance | Cannot extend another class |
| Sealed by Default | Acts as sealed for design safety |
| Auto-generated Methods | `equals()`, `hashCode()`, `toString()` |

---

## Records vs Classes: Boilerplate Comparison

### Traditional Class
```java
public class PersonClass {
    private final String name;
    private final int age;

    public PersonClass(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String name() {
        return name;
    }

    public int age() {
        return age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        PersonClass that = (PersonClass) o;
        return age == that.age && Objects.equals(name, that.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    @Override
    public String toString() {
        return "PersonClass{" + "name='" + name + '\'' + ", age=" + age + '}';
    }
}
```

### Record (Same Functionality)
```java
public record Person(String name, int age) {}
```

---

## Field Access

Record fields are accessed **by their component name** (not via getter methods with `get` prefix):

```java
Person person = new Person("Alice", 30);
System.out.println(person.name());  // "Alice"
System.out.println(person.age());   // 30
// NOT person.getName() or person.getAge()
```

---

## Static and Instance Members

### Static Variables and Methods ✅
```java
public record Circle(double radius) {
    static final double PI = Math.PI;
    
    static Circle unitCircle() {
        return new Circle(1.0);
    }
}
```

### Instance Variables ❌
```java
public record Circle(double radius) {
    // INVALID: Cannot declare additional instance variables
    // private double area;  // Compilation error
}
```
**Why?** Records are implicitly `final` classes designed for immutability. Adding instance variables violates the immutable data carrier contract.

### Instance Methods ✅
```java
public record Circle(double radius) {
    public double area() {
        return Math.PI * radius * radius;
    }
    
    public double circumference() {
        return 2 * Math.PI * radius;
    }
}
```
---

## Default Field Modifiers

All record components are implicitly:
- `private`
- `final`
- Immutable after construction

```java
public record Point(double x, double y) {
    // x and y are: private final double
}
```

---

## Key Rules Summary

| Rule | Details |
|------|---------|
| **Immutability** | All fields are `private final` |
| **Zero-arg Constructor** | Not generated; must be explicit |
| **Non-canonical Constructors** | Must delegate to canonical constructor |
| **Instance Variables** | Cannot be added (records are `final`) |
| **Static Members** | Allowed (static fields, static methods) |
| **Field Access** | Via component name, not getter methods |
| **Compact Constructor** | Validates without explicit assignments |


## Zero-Argument Constructor

Records **do NOT** have a zero-argument constructor by default. You must explicitly declare one if needed, and it must delegate to the canonical constructor:

```java
public record Person(String name, int age) {
    // Custom zero-arg constructor (must delegate)
    public Person() {
        this("Unknown", 0);
    }
}
```

---

## Canonical Constructor

The **canonical constructor** is the constructor whose parameters match the record's components in order:

```java
public record Person(String name, int age) {
    // Canonical constructor (implicitly declared, can be made explicit)
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

---

## Compact Canonical Constructor

A **compact constructor** omits the parameters and automatic field assignments. It receives validation logic only:

```java
public record Person(String name, int age) {
    // Compact canonical constructor
    public Person {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
        // Fields are automatically assigned after validation
    }
}
```

---

## Non-Canonical Constructors

Non-canonical constructors **must delegate** to the canonical constructor:

```java
public record Employee(String id, String name, double salary) {
    // Non-canonical constructor (must delegate)
    public Employee(String name, double salary) {
        this("EMP-" + System.nanoTime(), name, salary);
    }
}
```

---



## Records with Custom Behavior

### Record with Methods
```java
public record Circle(double radius) {
    public double area() {
        return Math.PI * radius * radius;
    }
    
    public double circumference() {
        return 2 * Math.PI * radius;
    }
}
```

### Record with Compact Constructor
```java
public record Temperature(double celsius) {
    public Temperature {
        if (celsius < -273.15) {
            throw new IllegalArgumentException("Invalid temperature");
        }
    }
}
```

---

## Reflection Methods

```java
Person.class.isRecord();                   // true
Person.class.getRecordComponents();        // [name, age]
```

---

## Records vs Classes

| Feature | Record | Class |
|---------|--------|-------|
| Boilerplate | Minimal | Extensive |
| Mutability | Immutable | Mutable |
| Inheritance | None | Supported |
| Use Case | Data holders | General purpose |


