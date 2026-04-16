

## What is an object?
- A real-world entity with properties and behavior.
- Example: a bank account has data like `accountNumber`, `accountHolderName`, `pin`.
- It also has actions such as `withdraw()`, `deposit()`, and `balanceEnquiry()`.

## What this example shows
- `SuperClass` and `SubClass` demonstrate inheritance.
- Static variables and static methods are class members.
- Instance variables and instance methods belong to objects.
- Static members are hidden by reference type, not overridden.
- Instance methods are polymorphic and can be overridden.
- Upcasting: assigning a subclass object to a superclass reference.
- Downcasting: converting a superclass reference back into a subclass reference.

## Java code example
```java
class SuperClass {
    static int staticVar = 10;
    int instanceVar = 20;

    static void staticMethod() {
        System.out.println("Static method in SuperClass");
    }

    void instanceMethod() {
        System.out.println("Instance method in SuperClass");
    }
}

class SubClass extends SuperClass {
    static int staticVar = 30;
    int instanceVar = 40;

    static void staticMethod() {
        System.out.println("Static method in SubClass");
    }

    @Override
    void instanceMethod() {
        System.out.println("Instance method in SubClass");
    }
}

public class Main {
    public static void main(String[] args) {
        SuperClass superClassRef = new SubClass();

        System.out.println("Static Variable: " + superClassRef.staticVar);
        System.out.println("Instance Variable: " + superClassRef.instanceVar);
        superClassRef.staticMethod();
        superClassRef.instanceMethod();

        System.out.println("-------------------------------------------------------------------------------");

        SubClass subClassRef = (SubClass) superClassRef;

        System.out.println("Static Variable: " + subClassRef.staticVar);
        System.out.println("Instance Variable: " + subClassRef.instanceVar);
        subClassRef.staticMethod();
        subClassRef.instanceMethod();

        System.out.println("-------------------------------------------------------------------------------");

        SuperClass superClassRef1 = subClassRef;
        System.out.println("Static Variable (SuperClass): " + superClassRef1.staticVar);
        System.out.println("Instance Variable (SuperClass): " + superClassRef1.instanceVar);
        superClassRef1.staticMethod();
        superClassRef1.instanceMethod();
    }
}
```

## Explanation
- `superClassRef.staticVar` prints `10` because static fields are resolved using the reference type, not the object type.
- `superClassRef.instanceVar` prints `20` because instance variables are not polymorphic.
- `superClassRef.staticMethod()` calls `SuperClass.staticMethod()` for the same reason.
- `superClassRef.instanceMethod()` prints `Instance method in SubClass` because instance methods use runtime polymorphism and `SubClass` overrides it.
- After casting to `SubClass`, `subClassRef.staticVar` prints `30` and `subClassRef.instanceVar` prints `40`.
- Even when the object is referenced as `SuperClass`, overridden instance methods still call the subclass implementation.

## Sample output
```text
Static Variable: 10
Instance Variable: 20
Static method in SuperClass
Instance method in SubClass
-------------------------------------------------------------------------------
Static Variable: 30
Instance Variable: 40
Static method in SubClass
Instance method in SubClass
-------------------------------------------------------------------------------
Static Variable (SuperClass): 10
Instance Variable (SuperClass): 20
Static method in SuperClass
Instance method in SubClass
```

## Summary
- Use `static` for class-level data and behavior.
- Use instance members for object-specific state and polymorphism.
- `extends` creates an inheritance relationship.
- `@Override` helps show when a subclass changes instance method behavior.
