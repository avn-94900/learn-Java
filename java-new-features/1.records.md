







# Java Records

## What are Records?

Records are immutable data carriers introduced to reduce boilerplate. They were added as a preview in Java 14 and finalized in Java 16.

**Keyword:** `record`  
**Purpose:** Simplify classes whose sole job is to hold data.

---

## The Boilerplate Problem

A traditional class holding two fields requires a lot of code:

```java
final class PersonClass {
    private final String name;
    private final int age;

    public PersonClass(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String name() { return name; }
    public int age()     { return age; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        PersonClass that = (PersonClass) o;
        return age == that.age && Objects.equals(name, that.name);
    }

    @Override
    public int hashCode() { return Objects.hash(name, age); }

    @Override
    public String toString() {
        return "PersonClass{name='" + name + "', age=" + age + '}';
    }
}
```

A record replaces all of that with one line:

```java
public record Person(String name, int age) {}
```

The compiler automatically generates `equals()`, `hashCode()`, `toString()`, and accessor methods.

---

## Field Access

Record accessors use the component name directly — no `get` prefix:

```java
Person person = new Person("Alice", 30);
person.name();  // "Alice"
person.age();   // 30
// NOT person.getName() or person.getAge()
```

---
## Key Rules Summary

| Rule                           | What It Means                                | Important Notes                                    |
| ------------------------------ | -------------------------------------------- | -------------------------------------------------- |
| **Immutability**               | All record fields are **private final**      | Values cannot change after object creation         |
| **Record Components**          | Fields are defined only in record header     | Example: `record Employee(String id, String name)` |
| **No Extra Instance Fields**   | Cannot declare additional instance variables | Only components become instance fields             |
| **Final Class**                | Records are implicitly **final**             | Cannot extend a record                             |
| **Inheritance**                | Cannot extend another class                  | But can implement interfaces                       |
| **Constructors**               | Canonical and compact constructors allowed   | Used for validation and initialization             |
| **Zero-arg Constructor**       | Not generated automatically                  | Must define manually if needed                     |
| **Non-canonical Constructors** | Must call canonical constructor              | Using `this(...)`                                  |
| **Compact Constructor**        | Allows validation without assignments        | Java auto-assigns fields                           |
| **Static Members**             | Static fields and methods allowed            | Instance fields not allowed beyond components      |
| **Field Access**               | Access using component methods               | `emp.id()` not `getId()`                           |
| **Auto-generated Methods**     | Compiler generates common methods            | `equals()`, `hashCode()`, `toString()`             |
| **Accessor Methods**           | Getter-like methods auto-created             | Same name as component                             |
| **toString Format**            | Includes class name and field values         | Useful for debugging                               |


---

## Constructors

### Canonical Constructor

The canonical constructor matches the record's components in order. It is implicitly declared, but you can make it explicit to add validation with manual assignment:

```java
public record Employee(String id, String name, double salary) {

    public Employee(String id, String name, double salary) {
        if (id == null || id.isBlank())
            throw new IllegalArgumentException("Id cannot be empty");
        if (salary < 0)
            throw new IllegalArgumentException("Salary cannot be negative");

        // Explicit assignment is required in the canonical constructor
        this.id     = id;
        this.name   = name;
        this.salary = salary;
    }
}
```

### Compact Constructor

A compact constructor omits the parameter list and the field assignments — the compiler inserts assignments automatically after the body runs. Use it for validation:

```java
public record Employee(String id, String name, double salary) {

    public Employee {
        if (id == null || id.isBlank())
            throw new IllegalArgumentException("Id cannot be empty");
        if (salary < 0)
            throw new IllegalArgumentException("Salary cannot be negative");
        // No this.id = id; needed — compiler handles it
    }
}
```

### Non-Canonical Constructor

Any constructor that does not match the canonical signature must delegate to it via `this(...)`:

```java
public record Employee(String id, String name, double salary) {

    // Auto-generates an id, only requires name and salary
    public Employee(String name, double salary) {
        this("EMP-" + System.nanoTime(), name, salary);
    }
}
```

### Zero-Argument Constructor

Records do not generate a zero-arg constructor automatically. If you need one, declare it and delegate:

```java
public record Person(String name, int age) {
    public Person() {
        this("Unknown", 0);
    }
}
```

---

## Static and Instance Members

### Static fields and methods — allowed

```java
public record Circle(double radius) {
    static final double PI = Math.PI;

    static Circle unitCircle() {
        return new Circle(1.0);
    }
}
```

### Instance methods — allowed

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

### Extra instance fields — not allowed

```java
public record Employee(String id, String name, double salary) {
    private int age;  // Compilation error
}
```

Records are designed as immutable data carriers. Additional instance fields would violate that contract.

---

## Inheritance

Records implicitly extend `java.lang.Record` and are `final`, so they cannot extend another class or be subclassed:

```java
public record Employee(String id) extends Person { } // Compilation error
```

Records can, however, implement interfaces.

---

## Reflection

```java
Person.class.isRecord();            // true
Person.class.getRecordComponents(); // [name, age]
```

---

## Records vs Classes

| Feature | Record | Class |
|---------|--------|-------|
| Boilerplate | Minimal | Extensive |
| Mutability | Immutable | Mutable |
| Inheritance | Not supported | Supported |
| Use case | Data holders | General purpose |