
# Java Sealed Classes - Complete Guide

## What are Sealed Classes?

| Aspect | Details |
|--------|---------|
| **Definition** | Classes that control which classes can extend them |
| **Introduced** | Java 15 (Preview), Java 17 (Final) |
| **Keyword** | `sealed` |
| **Purpose** | Restrict inheritance hierarchy for better design control |

---

### Key Features
- Use `sealed` keyword before class name
- Must include `permits` clause listing allowed subclasses
- Only permitted classes can extend the sealed class

### Constraints for Permitted Subclasses
- Must be accessible at compile time
- Must directly extend the sealed class
- Must use one of: `final`, `sealed`, or `non-sealed` modifier
- Must be in same module/package as sealed class

### Sealed Interfaces
- Yes, interfaces can be sealed too
- Use same `sealed` keyword and `permits` clause
- Control which classes/interfaces can implement/extend

### Useful Methods
- `isSealed()` - returns true if class is sealed
- `permittedSubclasses()` - returns array of permitted subclasses

## Basic Syntax

```java
public sealed class Shape permits Circle, Rectangle {
    // implementation
}

public final class Circle extends Shape {
    // no further extension
}

public non-sealed class Rectangle extends Shape {
    // can be extended by any class
}

public sealed class Oval extends Shape permits FilledOval {
    // implementation
}

public final class FilledOval extends Oval {
    // no further extension
}
```

---

## Rules for Permitted Subclasses

Permitted classes **must** have ONE modifier:

| Modifier | Meaning | Can be Extended? |
|----------|---------|------------------|
| `final` | No further extension | ❌ No |
| `sealed` | Controls further extension | ✅ Yes (via permits) |
| `non-sealed` | Open to all extensions | ✅ Yes (by anyone) |

---

## Sealed Classes vs Interfaces

### Sealed Abstract Class
```java
public sealed abstract class Animal permits Dog, Cat {
    abstract void sound();
}

public final class Dog extends Animal {
    @Override
    void sound() { System.out.println("Woof"); }
}
// also cat impl
```

### Sealed Interface
```java
// VehicleDemo.java
// Compile: javac VehicleDemo.java
// Run: java VehicleDemo

// --------- Base sealed interface ---------
public sealed interface Vehicle permits Car, Bike, ElectricVehicle {
    void drive();
}

// --------- Normal final implementations ---------
final class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Car driving with petrol engine");
    }
}

final class Bike implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Bike driving on two wheels");
    }
}

// --------- Sealed child interface ---------
// Notice: it must also declare permits
sealed interface ElectricVehicle extends Vehicle permits Tesla {
}

// --------- Concrete EV ---------
final class Tesla implements ElectricVehicle {
    @Override
    public void drive() {
        System.out.println("Tesla driving silently ⚡");
    }
}

// --------- Main class ---------
class VehicleDemo {
    public static void main(String[] args) {

        Vehicle v1 = new Car();
        Vehicle v2 = new Bike();
        Vehicle v3 = new Tesla();

        v1.drive();
        v2.drive();
        v3.drive();
    }
}
```

---

## Reflection Methods

```java
Shape.class.isSealed();                    // true
Shape.class.permittedSubclasses();         // [Circle, Rectangle]
```

---

## Top Interview Questions

1. **Why use sealed classes?** → Control design, prevent misuse, improve security
2. **Can sealed classes be abstract?** → Yes
3. **Must all permitted subclasses be final?** → No, can be `sealed` or `non-sealed`
4. **Can interfaces be sealed?** → Yes, same rules apply
5. **Use case?** → Domain modeling, preventing unauthorized extensions



