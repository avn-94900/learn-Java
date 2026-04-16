
# Java Generics Notes

## 1. Basics and Definition of Generics

Generics in Java enable writing type-safe, reusable code by allowing classes, interfaces, and methods to operate on objects of various types while providing compile-time type checking. Introduced in Java 5, generics prevent runtime errors like `ClassCastException` by enforcing type constraints.

**Key Benefit**: Code reusability without sacrificing type safety.

## 2. Type Parameters (T, E, K, V) and Naming Conventions

Type parameters are placeholders for actual types specified when using generic constructs. Common conventions:
- `T`: Type (general purpose)
- `E`: Element (used in collections like List<E>)
- `K`: Key (used in maps like Map<K, V>)
- `V`: Value (used in maps)

These are single uppercase letters. You can use multiple parameters, e.g., `Map<K, V>`.

## 3. Generic Classes

A generic class has a type parameter in its declaration, allowing it to work with different types.

```java
public class Box<T> {
    private T data;

    public void setData(T data) {
        this.data = data;
    }

    public T getData() {
        return data;
    }
}

// Usage
Box<Integer> intBox = new Box<>();
intBox.setData(42);
System.out.println(intBox.getData());  // 42

Box<String> strBox = new Box<>();
strBox.setData("Hello");
System.out.println(strBox.getData());  // Hello
```

## 4. Generic Methods

Generic methods allow type parameters at the method level, independent of the class.

```java
public class Utility {
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.println(element);
        }
    }
}

// Usage
Integer[] intArray = {1, 2, 3};
Utility.printArray(intArray);

String[] strArray = {"A", "B", "C"};
Utility.printArray(strArray);
```

## 5. Generic Interfaces

Interfaces can be generic, defining contracts for different types.

```java
@FunctionalInterface
interface Processor<T> {
    T process(T input);
}

// Usage
Processor<Integer> intProcessor = x -> x * 2;
System.out.println(intProcessor.process(5));  // 10

Processor<String> strProcessor = s -> s.toUpperCase();
System.out.println(strProcessor.process("hello"));  // HELLO
```

## 6. Bounded Types (extends Keyword)

Bounded types restrict type parameters to subclasses of a specific class or implementors of an interface.

```java
public class Container<T extends Number> {
    private T value;

    public Container(T value) {
        this.value = value;
    }

    public double getDoubleValue() {
        return value.doubleValue();
    }
}

// Usage
Container<Integer> intContainer = new Container<>(10);
System.out.println(intContainer.getDoubleValue());  // 10.0

// Container<String> strContainer = new Container<>("test");  // Compile error
```

## 7. Wildcards (?, ? extends, ? super)

Wildcards provide flexibility in generic types:
- `?`: Unbounded wildcard (any type)
- `? extends T`: Upper bounded (T or subclasses)
- `? super T`: Lower bounded (T or superclasses)

```java
import java.util.List;

public class WildcardExample {
    // Accepts any list
    public static void printList(List<?> list) {
        for (Object item : list) {
            System.out.println(item);
        }
    }

    // Accepts lists of Number or subclasses
    public static double sum(List<? extends Number> list) {
        double total = 0;
        for (Number num : list) {
            total += num.doubleValue();
        }
        return total;
    }

    // Accepts lists of Integer or superclasses
    public static void addNumbers(List<? super Integer> list) {
        list.add(1);
        list.add(2);
    }
}
```

## 8. Type Safety and Compile-Time Checking

Generics enforce type safety by checking types at compile time, preventing invalid operations. For example, a `List<String>` cannot hold integers, catching errors early.

**Tricky Point**: Raw types (e.g., `List` without `<String>`) bypass generics, leading to unchecked warnings and potential runtime errors.

## 9. Type Erasure

Type erasure removes generic type information at runtime, converting generics to raw types. This ensures backward compatibility but means runtime type checks (e.g., `instanceof`) don't work with generics.

**Why it happens**: To maintain compatibility with pre-Java 5 code.

**Example**:
```java
List<String> list = new ArrayList<>();
// At runtime, it's treated as List<Object>
```

## 10. Restrictions of Generics

- **No Primitives**: Cannot use primitives like `int`; use wrappers like `Integer`.
- **No Instantiation**: Cannot create `new T()`; use reflection or pass factories.
- **No Static Members**: Type parameters cannot be used in static contexts.
- **No Arrays**: Cannot create `T[]` directly; use `Array.newInstance()` or collections.
- **No Exceptions**: Cannot throw or catch generic types.

**Explanation**: Restrictions stem from type erasure, which removes type info at runtime, making operations like instantiation impossible without knowing the concrete type.

## 11. Inheritance with Generics

Generic types follow inheritance rules, but with caveats:
- `List<Integer>` is not a subtype of `List<Number>`.
- Use wildcards for covariance/contravariance.

```java
List<? extends Number> numbers = new ArrayList<Integer>();  // OK
// List<Number> numbers = new ArrayList<Integer>();  // Compile error
```

## 12. Advantages and Limitations

**Advantages**:
- Type safety and compile-time error detection.
- Code reusability and cleaner APIs.
- Elimination of casting.

**Limitations**:
- Runtime overhead due to boxing/unboxing for primitives.
- Complexity with wildcards and bounds.
- Type erasure limits runtime reflection.
- Cannot use with static members or certain operations.

[Back to Top](#table-of-contents)

### Example: Simple Generic Functional Interface

```java
@FunctionalInterface
interface I<T> {
    T m1(T x);
}

public class Main {
    public static void main(String[] args) {
        // Using the generic interface with Integer type
        I<Integer> obj1 = (x) -> {
            System.out.println("Lambda-Integer: " + x);
            return x;
        };
        System.out.println(obj1.m1(999));  
        /*
           Output:
           Lambda-Integer: 999
           999
        */

        // Using the same interface with String type
        I<String> obj2 = (String x) -> {
            System.out.println("Lambda-String: " + x);
            return "Hello " + x;
        };
        System.out.println(obj2.m1("world"));  
        /*
           Output:
           Lambda-String: world
           Hello world
        */
    }
}
```

### Example: Generic Class with Type Parameter

```java
public class Box<T> {
    private T data;

    public void setData(T data) {
        this.data = data;
    }

    public T getData() {
        return data;
    }
}

public class Main {
    public static void main(String[] args) {
        // Using the generic Box with Integer type
        Box<Integer> obj = new Box<>();
        obj.setData(19);
        System.out.println(obj.getData());  // Output: 19
        
        // Using the same Box class with String type
        Box<String> strBox = new Box<>();
        strBox.setData("Hello Generic");
        System.out.println(strBox.getData());  // Output: Hello Generic
    }
}
```


[Back to Top](#table-of-contents)
