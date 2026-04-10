
## Polymorphism

1. Ability of an object to take many forms
2. Achieved through inheritance and interfaces
3. Types:
   - Compile-time (method overloading)
   - Runtime (method overriding)
4. Method calls are resolved at runtime based on the object's actual type
5. Objects behave differently based on their actual runtime type
6. Allows reference variables of a superclass to refer to subclass objects
7. Enables the use of superclass type to refer to subclass object
8. Enables code reuse and flexibility
9. Polymorphic references can only access methods defined in the reference type
10. Type casting required to access subclass-specific methods

## Method Overloading

> **Method Overloading** is resolved at **compile-time**.                                                         
> It refers to the ability to define multiple methods with the same name but different parameter types or counts in the same class.

1. Multiple methods with the same name but different parameters in the same class
2. Differences can be:
   - Number of parameters
   - Data type of parameters
   - Order of parameters
3. Cannot overload methods that differ only by return type. Return type alone is not sufficient for overloading and Return type is optional.
4. Determined at compile time (static binding)
5. Access modifiers can be different for overloaded methods. (Access modifiers alone is not sufficient)
6. Exception declarations can be different (Exception declarations alone is not sufficient)
7. Can involve static and non-static methods
8. Constructors can be overloaded
9. Method resolution follows most specific parameter matching

## Method Overriding

> **Method Overriding** is resolved at **runtime**.                                                         
> It allows a subclass to provide a specific implementation of a method already defined in its superclass.

1. Redefining a superclass method in a subclass with the same signature
2. Method signature must be identical (name, parameters, return type)
3. Return type can be a subtype of the original return type (covariant return)
4. Access modifier cannot be more restrictive than the overridden method
5. Cannot throw broader exceptions than the overridden method
6. `@Override` annotation is recommended but optional
7. `final`, `static`, `private` methods cannot be overridden
   - `static` methods cannot be overridden (although they can be hidden)
   - `private` methods cannot be overridden
8. Determined at runtime (dynamic binding)
9. Can use `super` keyword to call the superclass version of the method

 **Example for Covariant Return type:**

```java
class Animal {}

class Dog extends Animal {}

class Parent {
    Animal getAnimal() {
        return new Animal();
    }
}

class Child extends Parent {
    Dog getAnimal() {
        return new Dog(); // Valid: Dog is a subclass of Animal
    }
}
```

In the above code:

* `Parent.getAnimal()` returns `Animal`
* `Child.getAnimal()` returns `Dog`, which is a subclass of `Animal`
* This is valid because of covariant return type