
## Interfaces

### 1. Declaration and Basics

Interfaces are declared using the `interface` keyword. They serve as contracts that define what methods implementing classes must provide.

```java
interface Animal {
    // Interface body
}
```

Key characteristics:
- Cannot be instantiated directly
- Used to define contracts for implementing classes
- Can provide both abstract and concrete methods (since Java 8)
- Supports multiple inheritance through interface extension

### 2. Variables in Interfaces

Interface variables are implicitly `public`, `static`, and `final` (constants). They cannot be changed once declared.

**Variable characteristics:**
- All variables are implicitly `public static final`
- Must be initialized at declaration
- Act as constants
- No instance variables allowed
- No private or protected variables

```java
interface Animal {
    int AGE = 10;                    // implicitly public static final
    String TYPE = "Pet";             // implicitly public static final
    double WEIGHT = 50.5;            // implicitly public static final
    
    // ❌ These are implied:
    // public static final int AGE = 10;
    // public static final String TYPE = "Pet";
}
```

### 3. Methods in Interfaces

Interface methods can be abstract, default, or static (since Java 8), and private (since Java 9).

**Allowed method types:**
- Abstract methods (no body)
- Default methods (with implementation)
- Static methods
- Private methods (Java 9+)

```java
interface Animal {
    // Abstract method (implicitly public abstract)
    void eat();
    
    // Default method (Java 8+)
    default void sleep() {
        System.out.println("Sleeping");
    }
    
    // Static method (Java 8+)
    static void staticInfo() {
        System.out.println("Static method");
    }
    
    // Private method (Java 9+)
    private void privateHelper() {
        System.out.println("Private helper");
    }
}
```

### 4. Abstract Methods

Abstract methods define contracts that implementing classes must fulfill. They have no implementation in the interface.

**Definition:**
- Declared without the `abstract` keyword (implicit)
- Have no body (no implementation)
- Must be overridden by implementing classes
- Implicitly `public`

```java
interface Animal {
    void eat();           // implicitly public abstract
    void sleep();         // implicitly public abstract
    void move();          // implicitly public abstract
    
    // ❌ These would create compile errors:
    // private void hide();      // ERROR: Cannot be private
    // static void jump();       // ERROR: Cannot be static
}
```

**Restrictions on abstract methods:**

Abstract methods in interfaces cannot be `private`, `static`, or `final`:

- Cannot be `private`: Implementing classes must override these methods, and private methods are not visible to subclasses.
- Cannot be `static`: Abstract methods are meant to be overridden, which requires instance method behavior.
- Cannot be `final`: The `final` keyword prevents overriding, contradicting the purpose of abstract methods.

### 5. Default Methods (Java 8+)

Default methods provide implementations within the interface, allowing adding new methods without breaking existing implementations.

**Characteristics:**
- Defined with the `default` keyword
- Have a body with implementation
- Can be overridden by implementing classes
- Cannot be `private`, `final`, `static`, or `abstract`
- Implicitly `public`

```java
interface Animal {
    default void sleep() {
        System.out.println("Default sleep implementation");
    }
    
    default void groom() {
        System.out.println("Default grooming");
    }
    
    // ❌ These would create compile errors:
    // final default void freeze() {}        // ERROR: Cannot be final
    // static default void jump() {}         // ERROR: Cannot be static
    // abstract default void move() {}       // ERROR: Cannot be abstract
}
```

**Handling multiple default methods:**

When implementing multiple interfaces with the same default method, the implementing class must override it to resolve the conflict:

```java
interface A {
    default void greet() {
        System.out.println("Hello from A");
    }
}

interface B {
    default void greet() {
        System.out.println("Hello from B");
    }
}

class MyClass implements A, B {
    @Override
    public void greet() {
        // Must override to resolve conflict
        System.out.println("Hello from MyClass");
    }
}
```

### 6. Static Methods (Java 8+)

Static methods in interfaces provide utility functions and cannot be overridden.

**Characteristics:**
- Defined with the `static` keyword
- Have a body with implementation
- Cannot be overridden by implementing classes
- Implicitly `public`
- Called using `InterfaceName.methodName()`
- Not inherited by implementing classes

```java
interface Animal {
    static void printInfo() {
        System.out.println("This is an Animal interface");
    }
    
    static int getDefaultAge() {
        return 10;
    }
}

public class Main {
    public static void main(String[] args) {
        Animal.printInfo();              // Calls static method
        int age = Animal.getDefaultAge(); // Returns 10
    }
}
```

### 7. Private Methods (Java 9+)

Private methods help organize code within interfaces and support code reuse for default and static methods.

**Characteristics:**
- Defined with the `private` keyword
- Have a body with implementation
- Can be static or instance methods
- Cannot be accessed by implementing classes
- Used for helper functionality
- Not inherited by implementing classes

```java
interface Animal {
    default void eat() {
        prepareFood();
        System.out.println("Eating");
    }
    
    private void prepareFood() {
        System.out.println("Preparing food...");
    }
    
    static void staticHelper() {
        helperMethod();
    }
    
    private static void helperMethod() {
        System.out.println("Static helper");
    }
}

class Dog implements Animal {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.eat();  // Output: Preparing food... Eating
        Animal.staticHelper();  // Output: Static helper
        
        // ❌ These would cause compile errors:
        // dog.prepareFood();      // ERROR: Cannot access private method
        // Animal.helperMethod();  // ERROR: Cannot access private method
    }
}
```

### 8. Implementation Rules

Any class implementing an interface must follow specific rules regarding abstract methods.

**Implementation requirements:**
- Must implement all abstract methods from the interface, OR
- Must itself be declared as abstract

```java
interface Animal {
    void eat();
    void sleep();
    
    default void groom() {
        System.out.println("Grooming");
    }
}

// Option 1: Implement all abstract methods
class Dog implements Animal {
    @Override
    public void eat() {
        System.out.println("Dog eats");
    }
    
    @Override
    public void sleep() {
        System.out.println("Dog sleeps");
    }
    
    // groom() inherited from interface
}

// Option 2: Remain abstract without implementing all methods
abstract class Mammal implements Animal {
    // eat() and sleep() not implemented
    // Must be implemented by concrete subclasses of Mammal
}
```

### 9. Inheritance Rules

Interfaces support multiple inheritance and can extend other interfaces.

**Inheritance constraints:**
- A class can implement multiple interfaces
- An interface can extend multiple interfaces
- All concrete methods must be implemented by the implementing class
- Default method conflicts must be resolved

```java
interface Eater {
    void eat();
}

interface Walker {
    void walk();
}

interface Runner {
    void run();
}

class Dog implements Eater, Walker, Runner {
    @Override
    public void eat() {
        System.out.println("Dog eats");
    }
    
    @Override
    public void walk() {
        System.out.println("Dog walks");
    }
    
    @Override
    public void run() {
        System.out.println("Dog runs");
    }
}
```

### 10. Summary of Rules

| Element | Allowed | Not Allowed |
|---------|---------|------------|
| Variables | public static final (constants only) | instance, private, protected, final without static |
| Abstract methods | public, default access | private, final, static |
| Default methods | public, instance-level | private, final, static, abstract |
| Static methods | public, utility functions | private, final, abstract |
| Private methods | instance/static helpers | abstract |
| Constructors | No | N/A |
| Instantiation | No (cannot create interface objects) | N/A |
| Inheritance | Multiple interfaces | Single class inheritance |
| Implementation | Multiple interfaces per class | Limited to one abstract class |

###  Example:

```java
interface Animal {
    // Variables (implicitly public static final)
    int AGE = 10;
    String TYPE = "Pet";
    
    // Abstract method
    void eat();
    
    // Default method (Java 8+)
    default void sleep() {
        System.out.println("Default sleep");
    }
    
    // Static method (Java 8+)
    static void printInfo() {
        System.out.println("Animal interface");
    }
    
    // Private method (Java 9+)
    private void helper() {
        System.out.println("Helper method");
    }
}

class Dog implements Animal {
    @Override
    public void eat() {
        System.out.println("Dog eats");
    }
    
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.eat();           // Dog eats
        dog.sleep();         // Default sleep
        Animal.printInfo();  // Animal interface
        System.out.println("Age: " + Animal.AGE);  // Age: 10
    }
}
```

### Output:

```
Dog eats
Default sleep
Animal interface
Age: 10
```
<br/>

### code example for default methods in interface

```java
// ❌ Invalid default method declarations — commented to avoid compile errors
interface InvalidDefaults {

    // ❌ Cannot use final with default methods — they are meant to be overridden
    // final default void method1();        

    // ❌ Cannot use static with default methods — static and default don't go together
    // static default void method2();       

    // ❌ Cannot use abstract with default methods — default has a body, abstract doesn't
    // abstract default void method3();     

    // ❌ Cannot use synchronized — not allowed in interface methods
    // synchronized default void method4(); 
}
```

<br/>

```java
// ✅ Multiple interfaces with same default method — must resolve conflict
interface A {
    default void greet() {
        System.out.println("Hello from A");
    }
}

interface B {
    default void greet() {
        System.out.println("Hello from B");
    }
}

class MyClass implements A, B {
    // Must override to resolve conflict between A and B
    @Override
    public void greet() {
        System.out.println("Hello from MyClass");
    }
}
```


<br/>

```java
// ✅ Overriding default method in a sub-interface
interface Parent {
    default void show() {
        System.out.println("Parent show");
    }
}

interface Child extends Parent {
    @Override
    default void show() {
        System.out.println("Child show");
    }
}

// ✅ Main class to run all examples
public class DefaultMethodExamples {
    public static void main(String[] args) {
        System.out.println("=== Conflict Resolution ===");
        MyClass obj = new MyClass();
        obj.greet(); // Output: Hello from MyClass

        System.out.println("\n=== Sub-interface override ===");
        Child child = new Child() {}; // anonymous class implementation
        child.show(); // Output: Child show
    }
}
```

<br/><br/><br/>

## Interface vs Abstract Class

Understanding the differences between interfaces and abstract classes is crucial for proper design decisions in Java.

### Comparison Table:

| Feature | Interface | Abstract Class |
|---------|-----------|----------------|
| Variables | Always `public static final` | Can be `private`, `public`, `static`, `protected`, `final` |
| Variable initialization | Only declaration, no initialization | Can initialize variables |
| Blocks/Initializers | Cannot write blocks (instance/static) | Can include both static and instance initializers |
| Constructors | Cannot write constructors | Can write constructors |
| Object creation | Cannot create objects, only reference variables | Cannot instantiate directly, but can be extended |
| State representation | Doesn't represent object state | May represent object state |
| Methods | Abstract, default, static, private (Java 9+) | Abstract and concrete methods |
| Default methods | Supported | Not applicable (uses regular methods) |
| Multiple inheritance | Supported | Not supported |
| Object class methods | Cannot directly override | Can override |

<br/>


## Java: Abstraction, Interfaces, Inheritance & Diamond Problem

## 1. How are Abstraction and Loose Coupling Achieved?

### Abstraction
**Definition**: Hiding implementation details while showing only essential features to the user.

**Achieved through**:
- **Abstract Classes**: Classes that cannot be instantiated and may contain abstract methods
- **Interfaces**: Contracts that define what a class must do, not how it does it
- **Access Modifiers**: Control visibility of class members

**Example**:
```java
// Abstract class providing abstraction
abstract class Vehicle {
    abstract void start();  // What to do (not how)
    
    public void stop() {    // Common implementation
        System.out.println("Vehicle stopped");
    }
}

class Car extends Vehicle {
    void start() {          // How to do it
        System.out.println("Car engine started");
    }
}
```

### Loose Coupling
**Definition**: Reducing dependencies between classes/modules so changes in one don't heavily impact others.

**Achieved through**:
- **Interfaces**: Program to interfaces, not implementations
- **Dependency Injection**: Provide dependencies from outside
- **Abstract Classes**: Use abstract types instead of concrete classes

**Example**:
```java
// Loose coupling with interfaces
interface PaymentProcessor {
    void processPayment(double amount);
}

class CreditCardProcessor implements PaymentProcessor {
    public void processPayment(double amount) {
        // Credit card logic
    }
}

class PayPalProcessor implements PaymentProcessor {
    public void processPayment(double amount) {
        // PayPal logic
    }
}

class OrderService {
    private PaymentProcessor processor;
    
    // Loosely coupled - can work with any payment processor
    public OrderService(PaymentProcessor processor) {
        this.processor = processor;
    }
    
    public void processOrder(double amount) {
        processor.processPayment(amount);
    }
}
```

## 2. What is the Use of Interfaces?

### Primary Uses:

1. **Achieve Multiple Inheritance**: Java classes can implement multiple interfaces
2. **Contract Definition**: Define what methods a class must implement
3. **Abstraction**: Hide implementation details
4. **Loose Coupling**: Reduce dependencies between classes
5. **Polymorphism**: Treat different objects uniformly
6. **API Design**: Define consistent APIs across different implementations

### Practical Examples:

**Example 1: Multiple Inheritance of Type**
**Example 2: Polymorphism**


## 3. What is Inheritance and Types of Inheritance?

### Inheritance
**Definition**: Mechanism where a new class (child/subclass) inherits properties and methods from an existing class (parent/superclass).

**Benefits**:
- Code reusability
- Method overriding
- Polymorphism
- Hierarchical classification

### Types of Inheritance:

#### 1. Single Inheritance
One class inherits from one parent class.
```java
class Animal {
    void eat() { System.out.println("Animal eats"); }
}

class Dog extends Animal {
    void bark() { System.out.println("Dog barks"); }
}
```

#### 2. Multilevel Inheritance
Chain of inheritance (grandparent → parent → child).
```java
class Animal {
    void breathe() { System.out.println("Breathing"); }
}

class Mammal extends Animal {
    void warmBlooded() { System.out.println("Warm blooded"); }
}

class Dog extends Mammal {
    void bark() { System.out.println("Barking"); }
}
```

#### 3. Hierarchical Inheritance
Multiple classes inherit from one parent class.
```java
class Animal {
    void move() { System.out.println("Animal moves"); }
}

class Dog extends Animal {
    void bark() { System.out.println("Dog barks"); }
}

class Cat extends Animal {
    void meow() { System.out.println("Cat meows"); }
}
```

#### 4. Multiple Inheritance (NOT supported in Java for classes)
One class inheriting from multiple parent classes.
```java
// NOT POSSIBLE in Java with classes
// class Child extends Parent1, Parent2 { } // COMPILATION ERROR

// But possible with interfaces
interface Interface1 { void method1(); }
interface Interface2 { void method2(); }
class Child implements Interface1, Interface2 {
    public void method1() { }
    public void method2() { }
}
```

#### 5. Hybrid Inheritance (NOT directly supported)
Combination of multiple inheritance types.

## 4. Why is Multiple Inheritance Not Possible in Java?

### The Ambiguity Problem

**Reasons**:
1. **Diamond Problem**: Ambiguity when same method exists in multiple parent classes
2. **Complexity**: Makes the language complex and error-prone
3. **Compilation Issues**: Compiler cannot decide which method to inherit

**Example of the Problem**:
```java
// This is NOT valid Java code - just for illustration
class A {
    void display() { System.out.println("A"); }
}

class B extends A {
    void display() { System.out.println("B"); }
}

class C extends A {
    void display() { System.out.println("C"); }
}

// If this were allowed (IT'S NOT):
class D extends B, C {  // COMPILATION ERROR
    // Which display() method should D inherit?
    // B's display() or C's display()?
    // This creates ambiguity!
}
```

**Java's Solution**: Use interfaces for multiple inheritance of type, not implementation.

## 5. What is the Diamond Problem and How Does Java Solve It?

### Diamond Problem
**Definition**: Occurs when a class inherits from two classes that have a common base class, creating ambiguity about which version of inherited methods to use.

**Visual Representation**:
```
     A
   /   \
  B     C
   \   /
     D
```

### The Problem Scenario:
```java
// Hypothetical scenario (NOT valid Java)
class A {
    void method() { System.out.println("A"); }
}

class B extends A {
    void method() { System.out.println("B"); }
}

class C extends A {
    void method() { System.out.println("C"); }
}

// IF multiple inheritance were allowed:
class D extends B, C {  // NOT ALLOWED
    // Which method() should be inherited?
    // Ambiguity: B's method() or C's method()?
}
```

### Java's Solutions:

#### 1. No Multiple Inheritance for Classes
Java simply doesn't allow multiple class inheritance.

#### 2. Multiple Inheritance with Interfaces

When diamond problem occurs with interfaces:
 - Interface Default Methods (Java 8+)

```java
interface A {
    default void method() { System.out.println("A"); }
}

interface B extends A {
    default void method() { System.out.println("B"); }
}

interface C extends A {
    default void method() { System.out.println("C"); }
}

class D implements B, C {
    // Must resolve the conflict explicitly
    public void method() {
        B.super.method(); // Explicitly call B's method
        // or C.super.method(); // or C's method
        // or provide own implementation
    }
}
```
### Key Points:
- **Classes**: No multiple inheritance allowed - prevents diamond problem
- **Interfaces**: Multiple inheritance allowed, but conflicts must be resolved explicitly
- **Compiler Enforcement**: Java compiler forces explicit resolution of ambiguous situations
- **Clean Design**: Encourages better design patterns and loose coupling


## no error in this program

```java

interface EarthLike {
    void Rotation();  // Abstract method (no default keyword)
}

interface MarsLike {
    void Rotation();  // Same abstract method signature
}

class SatelliteBody implements EarthLike, MarsLike {
    @Override
    public void Rotation() {
        System.out.println("Resolved ambiguity: Satellite rotates uniquely.");
    }
}

public class Main {
    public static void main(String[] args) {
        SatelliteBody satellite = new SatelliteBody();
        satellite.Rotation();  // Output: Resolved ambiguity: Satellite rotates uniquely.
    }
}

```
## code will shows error here

```java
class A {
    void method() { System.out.println("A"); }
}

abstract class B extends A {
    abstract void method2();
}

abstract class C extends A {
    abstract void method2();
}

class D extends B, C {  // ❌ NOT ALLOWED
    // Which method() should be inherited?
}


```


## valid code only
```java
abstract class B {
    void common() {
        System.out.println("B");
    }
}

interface C {
    void extra();
}

class D extends B implements C {
    public void extra() {
        System.out.println("C's method");
    }
}

```

```java
interface MyInterface {
    static void staticMethod() {
        System.out.println("Static method in interface");
    }
}

class MyClass implements MyInterface {
    // No access to staticMethod() directly
}

public class Test {
    public static void main(String[] args) {
        // MyClass.staticMethod();  ❌ Compile-time error
        // new MyClass().staticMethod(); ❌ Compile-time error

        MyInterface.staticMethod(); // ✅ Correct usage
    }
}

```

```java
// Parent class with a static method
class Parent {
    static void greet() { // Static method in Parent
        System.out.println("Hello from Parent");
    }
}

// Child class that hides the static method of Parent
class Child extends Parent {
    static void greet() { // Static method in Child (hides Parent's method, doesn't override it)
        System.out.println("Hello from Child");
    }
}

public class Test {
    public static void main(String[] args) {
        Parent p = new Child(); // Reference is of type Parent, object is of type Child
        p.greet(); // Calls Parent.greet() because static methods are resolved at compile-time by reference type
                  // This is method hiding, not overriding. So output will be: Hello from Parent
    }
}

```
