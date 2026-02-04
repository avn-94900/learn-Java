# Java Modern Features - Top Interview Questions & Answers

## Table of Contents
1. [Java Sealed Classes Q&A](#java-sealed-classes-qa)
2. [Java Pattern Matching Q&A](#java-pattern-matching-qa)
3. [Java Records Q&A](#java-records-qa)
4. [Java Virtual Threads Q&A](#java-virtual-threads-qa)
5. [Quick Reference Summary](#quick-reference-summary)

---

## Java Sealed Classes Q&A

### Q1: What are Java sealed classes and why were they introduced?
**Answer:** Sealed classes are a Java 15 feature that allows you to control which classes can extend your class. They restrict inheritance to only specific, permitted subclasses. This helps create more secure and maintainable code by preventing unexpected extensions and making the class hierarchy explicit.

### Q2: How do you declare a sealed class?
**Answer:** 
```java
public sealed class Shape permits Circle, Rectangle, Triangle {
    // class body
}
```
Use the `sealed` keyword followed by a `permits` clause that lists the allowed subclasses.

### Q3: What are the requirements for permitted subclasses?
**Answer:** Permitted subclasses must:
- Be accessible at compile time
- Directly extend the sealed class
- Use one of three modifiers: `final`, `sealed`, or `non-sealed`
- Be in the same module or package as the sealed class

### Q4: Can interfaces be sealed?
**Answer:** Yes, interfaces can be sealed using the same syntax:
```java
public sealed interface Drawable permits Circle, Rectangle {
    void draw();
}
```

### Q5: What's the difference between `final`, `sealed`, and `non-sealed` subclasses?
**Answer:**
- **final**: Cannot be extended further
- **sealed**: Can be extended, but only by its permitted subclasses
- **non-sealed**: Can be extended by any class (removes the sealing restriction)

---

## Java Pattern Matching Q&A

### Q6: What is pattern matching in Java?
**Answer:** Pattern matching is a Java 16+ feature that allows you to test a value against a pattern and extract data from it in a single operation. It makes conditional logic more concise and type-safe.

### Q7: How does pattern matching improve upon traditional instanceof checks?
**Answer:** Traditional way:
```java
if (obj instanceof String) {
    String str = (String) obj;
    // use str
}
```
Pattern matching way:
```java
if (obj instanceof String str) {
    // use str directly
}
```
It eliminates the need for explicit casting and reduces boilerplate code.

### Q8: Can you use pattern matching in switch expressions?
**Answer:** Yes, Java 17+ supports pattern matching in switch:
```java
public String processObject(Object obj) {
    return switch (obj) {
        case String s -> "String: " + s;
        case Integer i -> "Integer: " + i;
        case null -> "Null value";
        default -> "Unknown type";
    };
}
```

### Q9: What are guard conditions in pattern matching?
**Answer:** Guard conditions allow you to add additional checks to patterns:
```java
switch (obj) {
    case String s && s.length() > 5 -> "Long string";
    case String s -> "Short string";
    default -> "Not a string";
}
```

### Q10: What types of patterns are supported in Java?
**Answer:**
- **Type patterns**: `obj instanceof String s`
- **Constant patterns**: `case 1`, `case "hello"`
- **Guarded patterns**: `case String s && s.length() > 0`
- **Record patterns**: `case Point(int x, int y)` (Preview feature)

---

## Java Records Q&A

### Q11: What are Java records and when should you use them?
**Answer:** Records are a special kind of class introduced in Java 14 for holding immutable data. Use them when you need simple data carriers without behavior. They automatically generate constructor, getters, `equals()`, `hashCode()`, and `toString()`.

### Q12: How do you declare a record?
**Answer:**
```java
public record Person(String name, int age, String email) {}
```
This automatically creates a class with private final fields, constructor, and accessor methods.

### Q13: What methods does a record automatically provide?
**Answer:**
- Constructor with all components
- Accessor methods (not getters): `name()`, `age()`, `email()`
- `equals()` method based on all components
- `hashCode()` method based on all components
- `toString()` method showing all components

### Q14: Can records have custom constructors and methods?
**Answer:** Yes, records can have:
```java
public record Person(String name, int age) {
    // Compact constructor for validation
    public Person {
        if (age < 0) throw new IllegalArgumentException("Age cannot be negative");
    }
    
    // Custom methods
    public boolean isAdult() {
        return age >= 18;
    }
    
    // Static methods
    public static Person of(String name, int age) {
        return new Person(name, age);
    }
}
```

### Q15: What are the limitations of records?
**Answer:** Records cannot:
- Extend other classes (but can implement interfaces)
- Be abstract
- Declare instance fields beyond the record components
- Be non-static nested classes
- Have mutable fields (all fields are final)

### Q16: How do you check if a class is a record at runtime?
**Answer:**
```java
Class<?> clazz = Person.class;
boolean isRecord = clazz.isRecord();
RecordComponent[] components = clazz.getRecordComponents();
```

---

## Java Virtual Threads Q&A

### Q17: What are virtual threads and how do they differ from platform threads?
**Answer:** Virtual threads are lightweight threads managed by the JVM, not the OS. Key differences:

| Aspect | Platform Threads | Virtual Threads |
|--------|-----------------|-----------------|
| OS binding | 1:1 with OS threads | Many:1 with OS threads |
| Memory | ~2MB stack | Few KB |
| Creation cost | Expensive | Very cheap |
| Scalability | Thousands | Millions |

### Q18: How do you create virtual threads?
**Answer:**
```java
// Method 1: Thread.ofVirtual()
Thread vThread = Thread.ofVirtual().start(() -> {
    System.out.println("Running in virtual thread");
});

// Method 2: Using ExecutorService
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> {
        // task code
    });
}

// Method 3: Thread.startVirtualThread()
Thread.startVirtualThread(() -> {
    // task code
});
```

### Q19: When should you use virtual threads?
**Answer:** Use virtual threads for:
- I/O intensive applications
- High-concurrency scenarios (web servers, microservices)
- Applications with many blocking operations
- When you need to handle thousands/millions of concurrent tasks

Avoid for:
- CPU-intensive tasks
- Applications already using async programming models

### Q20: What is the carrier thread concept?
**Answer:** Carrier threads are the actual OS threads that run virtual threads. When a virtual thread blocks (e.g., I/O operation), it's unmounted from its carrier thread, which can then run other virtual threads. When the blocking operation completes, the virtual thread is mounted back onto an available carrier thread.

### Q21: Are there any pitfalls with virtual threads?
**Answer:** Yes, be aware of:
- **Thread-local variables**: Can cause memory leaks if not cleaned up
- **Synchronized blocks**: Can pin virtual threads to carrier threads
- **JNI calls**: Also pin virtual threads
- **CPU-bound tasks**: No benefit, might be slower due to scheduling overhead

---

## Quick Reference Summary

### Sealed Classes Checklist
- ✅ Use `sealed` keyword + `permits` clause
- ✅ Subclasses must be `final`, `sealed`, or `non-sealed`
- ✅ Same package/module requirement
- ✅ Works with classes and interfaces

### Pattern Matching Checklist
- ✅ Available from Java 16+
- ✅ Works with `instanceof` and `switch`
- ✅ Eliminates casting boilerplate
- ✅ Supports guard conditions

### Records Checklist
- ✅ Use for immutable data carriers
- ✅ Auto-generates equals/hashCode/toString
- ✅ Can implement interfaces
- ❌ Cannot extend classes
- ❌ Cannot have mutable fields

### Virtual Threads Checklist
- ✅ Great for I/O intensive tasks
- ✅ Can create millions of them
- ✅ Use Executors.newVirtualThreadPerTaskExecutor()
- ⚠️ Avoid for CPU-intensive work
- ⚠️ Watch out for pinning with synchronized blocks

### Key Interview Points
1. **When introduced**: Sealed classes (Java 15), Pattern matching (Java 16), Records (Java 14), Virtual threads (Java 19+)
2. **Main benefits**: Code safety, reduced boilerplate, better concurrency
3. **Limitations**: Each feature has specific constraints you should know
4. **Real-world usage**: Be prepared to discuss when to use each feature