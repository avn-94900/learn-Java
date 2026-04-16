
# Pattern Matching in Java

## Overview

| Aspect | Details |
|--------|---------|
| **Definition** | Simplifies testing and extracting values from complex data structures |
| **Introduced** | Java 16 (Preview), Java 17 (Enhanced), Java 21 (Finalized) |
| **Keywords** | `instanceof`, `switch` |
| **Benefit** | Reduces boilerplate code and improves readability |

---

## Pattern Types

| Pattern Type | Purpose | Example |
|--------------|---------|---------|
| **Type Pattern** | Type checking and automatic casting | `if (obj instanceof String s)` |
| **Record Pattern** | Destructure record components | `case Point(var x, var y)` |
| **Guard Pattern** | Add conditional logic with `when` | `case Integer i when i > 0` |
| **Null Pattern** | Explicit null handling | `case null ->` |

---

## Basic Examples

### Type Patterns
```java
// Before (Java 15 and earlier)
if (obj instanceof String) {
    String str = (String) obj;
}

// After (Java 16+)
if (obj instanceof String str) {
    System.out.println(str.length());
}
```

### Switch Pattern Matching
- Enhanced `switch` statements support multiple patterns:
```java
String result = switch (obj) {
    case String s -> "String: " + s;
    case Integer i -> "Integer: " + i;
    case null -> "Null";
    default -> "Unknown";
};
```
```java
public String describeShape(Object obj) {
    return switch (obj) {
        case Circle c -> "Circle with radius " + c.radius();
        case Rectangle r -> "Rectangle " + r.width() + "x" + r.height();
        case String s when s.length() > 5 -> "Long string: " + s;
        case String s -> "Short string: " + s;
        case null -> "No shape provided";
        default -> "Unknown shape";
    };
}
```

### Record Patterns
- Record patterns destructure record components inline:
```java
public record Point(int x, int y) {}

// Simple destructuring
if (point instanceof Point(var x, var y)) {
    System.out.println(x + ", " + y);
}

// Nested destructuring
public record Circle(Point center, int radius) {}
if (circle instanceof Circle(Point(var x, var y), var r)) {
    System.out.println("Radius: " + r);
}
```

### Guard Patterns
- Guards add conditional logic to patterns:
```java
return switch (obj) {
    case Integer i when i > 0 -> "Positive";
    case Integer i when i < 0 -> "Negative";
    case Integer i -> "Zero";
    default -> "Not integer";
};
```

---

## Key Rules

- **Compile-Time Safety**: Patterns checked at compile time
- **Exhaustiveness**: `switch` must handle all cases or include `default`
- **Variable Scope**: Pattern variables scoped to their case block
- **Pattern Order**: Place specific patterns before general ones
