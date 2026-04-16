
## Abstract Classes

>In Java, an abstract class is a class that cannot be instantiated and is designed to be extended by other classes. It is used to achieve partial abstraction, where some methods are implemented while others are left for subclasses to define

### 1. Declaration and Basics

Abstract classes are declared using the `abstract` keyword. They serve as base classes that cannot be instantiated directly but provide a blueprint for subclasses.

```java
abstract class Animal {
    // Class body
}
```

Key characteristics:
- Cannot be instantiated directly
- Used as a base class for other classes
- Can provide both abstract and concrete implementations

### 2. Variables in Abstract Classes

Abstract classes can contain multiple types of variables with no restrictions on access modifiers or variable types.

**Allowed variable types:**
- Static variables
- Instance (non-static) variables
- Final variables
- Private, protected, or public variables

```java
abstract class Animal {
    static int count = 0;           // static variable
    int age;                        // instance variable
    private String name;            // private variable
    final String type = "Pet";      // final variable
    protected double weight;        // protected variable
}
```

### 3. Methods in Abstract Classes

Abstract classes can contain regular (concrete) methods with any combination of modifiers.

**Allowed method types:**
- Static methods
- Non-static (instance) methods
- Final methods
- Private methods

```java
abstract class Animal {
    static void staticMethod() {
        System.out.println("Static method");
    }

    void normalMethod() {
        System.out.println("Instance method");
    }

    final void finalMethod() {
        System.out.println("Final method - cannot be overridden");
    }

    private void privateMethod() {
        System.out.println("Private method");
    }
}
```

### 4. Abstract Methods

Abstract methods define contracts that subclasses must fulfill. They have no implementation in the abstract class.

**Definition:**
- Declared with the `abstract` keyword
- Have no body (no implementation)
- Must be overridden by subclasses

```java
abstract class Animal {
    abstract void makeSound();  // No body, no semicolon
}
```

**Restrictions on abstract methods:**

Abstract methods cannot be marked as `private`, `final`, or `static`. Here is why:

- Cannot be `private`: Subclasses must override abstract methods to provide implementation. A private method is not visible to subclasses, making overriding impossible.
- Cannot be `final`: The `final` keyword prevents overriding. Since abstract methods must be overridden, using `final` creates a contradiction.
- Cannot be `static`: Static methods are not inherited in the traditional sense; they are hidden. Abstract methods are meant to be overridden, which is a runtime mechanism not applicable to static methods.

**Allowed access modifiers:**
- `public`
- `protected`
- (default/package-private)

```java
abstract class Animal {
    public abstract void eat();      // public - accessible everywhere
    protected abstract void sleep(); // protected - accessible in subclasses
    abstract void move();            // default - accessible in same package
    
    // private abstract void hide();  // ERROR: Cannot be private
    // final abstract void freeze();  // ERROR: Cannot be final
    // static abstract void jump();   // ERROR: Cannot be static
}
```

### 5. Constructors in Abstract Classes

Abstract classes can have constructors. These constructors are used to initialize instance variables and are called when a subclass object is created.

**Rules for constructors:**
- Abstract classes can have constructors
- Constructors are used to initialize variables
- Constructor is called when a concrete subclass is instantiated
- Subclass must call parent constructor using `super()`

```java
abstract class Animal {
    String name;

    Animal(String name) {
        this.name = name;
        System.out.println("Animal constructor called");
    }
}

class Dog extends Animal {
    Dog(String name) {
        super(name);  // Call parent constructor
        System.out.println("Dog constructor called");
    }
}

public class Main {
    public static void main(String[] args) {
        Dog d = new Dog("Buddy");
    }
}
```

Output:
```
Animal constructor called
Dog constructor called
```

### 6. Inheritance Rules

Abstract classes support single inheritance but can implement multiple interfaces.

**Inheritance constraints:**
- A class can extend only one abstract class (single inheritance)
- A class can implement multiple interfaces

```java
abstract class Animal {
    abstract void eat();
}

interface Runnable {
    void run();
}

interface Swimmer {
    void swim();
}

class Dog extends Animal implements Runnable, Swimmer {
    @Override
    void eat() {
        System.out.println("Dog is eating");
    }

    @Override
    public void run() {
        System.out.println("Dog is running");
    }

    @Override
    public void swim() {
        System.out.println("Dog is swimming");
    }
}
```

### 7. Subclass Rules

Any class that extends an abstract class must follow specific rules regarding abstract methods.

**Subclass requirements:**
- Must implement all abstract methods from the abstract class, OR
- Must itself be declared as abstract

```java
abstract class Animal {
    abstract void eat();
    abstract void sleep();
}

// Option 1: Implement all abstract methods
class Dog extends Animal {
    @Override
    void eat() {
        System.out.println("Dog eats");
    }

    @Override
    void sleep() {
        System.out.println("Dog sleeps");
    }
}

// Option 2: Remain abstract without implementing all methods
abstract class Mammal extends Animal {
    // eat() and sleep() not implemented - still abstract
    // Must be implemented by concrete subclasses of Mammal
}
```

### 8. Summary of Rules

| Element | Allowed | Not Allowed |
|---------|---------|------------|
| Variables | static, instance, final, any access modifier | None |
| Concrete methods | static, instance, final, private, any access modifier | None |
| Abstract methods | public, protected, default | private, final, static |
| Constructors | Yes (for initialization) | No parameters restriction |
| Instantiation | No (cannot create abstract class objects) | N/A |
| Inheritance | Single inheritance from one abstract class | Multiple inheritance from classes |
| Interface implementation | Multiple interfaces | N/A |



###  Example:

```java
abstract class Animal {
	int typeOfAnimal = "Animal"; // instance variable with direct initialization
    String name;  // any access modfier -  (final also)

    // static & Non-Static blocks for var initilization.
    // static & Non-Static methods. - (private,final)

    // Constructor in abstract class
    Animal(String name) {
        this.name = name;
        System.out.println("Animal constructor called");
    }
}

class Dog extends Animal {
    Dog(String name) {
        super(name);
        System.out.println("Dog constructor called");
    }
}

public class Main {
    public static void main(String[] args) {
        Dog d = new Dog("Buddy");
    }
}
```

###  Output:

```
Animal constructor called
Dog constructor called
```

So, the constructor of the abstract class runs when the child class is created.