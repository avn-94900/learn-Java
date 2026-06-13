## String vs StringBuilder vs StringBuffer (Java)

| Feature       | String                    | StringBuilder                        | StringBuffer                        |
| ------------- | ------------------------- | ------------------------------------ | ----------------------------------- |
| Mutable?      | ❌ No (Immutable)          | ✅ Yes                                | ✅ Yes                               |
| Thread Safe?  | ✅ Yes (because immutable) | ❌ No                                 | ✅ Yes (synchronized)                |
| Performance   | Slow for repeated changes | Fastest                              | Slower than StringBuilder           |
| Memory Usage  | More objects created      | Less                                 | Less                                |
| Introduced In | Java 1.0                  | Java 5                               | Java 1.0                            |
| Use Case      | Fixed text                | Single-threaded string modifications | Multi-threaded string modifications |

---

## 1. String

A `String` object **cannot be changed after creation**.

```java
String s = "Hello";
s = s + " World";
```

What happens internally:

```java
String s = "Hello";      // Object 1
s = s + " World";        // Object 2 created
```

Old object remains in memory until garbage collection.

### Memory Diagram

```text
s --> "Hello"

s = s + " World"

s --> "Hello World"
       ↑ New Object
```

### Problem

Inside loops, many objects get created.

```java
String s = "";

for(int i=0;i<1000;i++){
    s = s + i;
}
```

This is inefficient.

---

## 2. StringBuilder

`StringBuilder` is **mutable**.

It modifies the same object instead of creating new ones.

```java
StringBuilder sb = new StringBuilder("Hello");

sb.append(" World");

System.out.println(sb);
```

Output:

```text
Hello World
```

### Memory Diagram

```text
sb --> "Hello"

append(" World")

sb --> "Hello World"
```

Same object modified.

### Example

```java
StringBuilder sb = new StringBuilder();

for(int i=0;i<1000;i++){
    sb.append(i);
}
```

Efficient because only one object is used.

---

## 3. StringBuffer

`StringBuffer` works almost exactly like `StringBuilder`.

Difference:

```java
public synchronized StringBuffer append(String str)
```

Methods are synchronized.

This makes it thread-safe.

### Example

```java
StringBuffer sb = new StringBuffer();

sb.append("Java");
sb.append(" Backend");
```

Output:

```text
Java Backend
```

---

## Why StringBuffer is Slower?

Every operation needs a lock.

```java
sb.append("A");
```

Before appending:

1. Acquire lock
2. Perform operation
3. Release lock

Extra work = slower.

---

## Interview Question

### Which is Faster?

```text
StringBuilder > StringBuffer > String
```

For repeated modifications.

---

## When Should You Use What?

### Use String

When value will not change.

```java
String country = "India";
```

---

### Use StringBuilder

When frequently modifying strings in a single thread.

```java
StringBuilder sb = new StringBuilder();

sb.append("SELECT ");
sb.append("* ");
sb.append("FROM employee");
```

Best choice in most applications.

---

### Use StringBuffer

When multiple threads are modifying the same object.

```java
StringBuffer sb = new StringBuffer();
```

Rarely needed in modern code because other concurrency techniques are usually preferred.

---

## Common Interview Question

### Why is String Immutable?

Benefits:

1. Security
2. Thread Safety
3. String Pool optimization
4. HashMap key reliability

Example:

```java
String s = "Java";
```

If strings were mutable:

```java
s = "Python";
```

Hash codes and pooled strings could break.

---

## Quick Revision

```text
String
------
Immutable
Thread-safe
Creates new object on modification

StringBuilder
-------------
Mutable
Not thread-safe
Fastest
Best for most string operations

StringBuffer
------------
Mutable
Thread-safe
Slower than StringBuilder
Use when multiple threads share it
```

### Easy Interview Answer

> **String** is immutable. Every modification creates a new object.
> **StringBuilder** is mutable and not thread-safe, making it the fastest option for string manipulation.
> **StringBuffer** is mutable and thread-safe because its methods are synchronized, but it is slower than StringBuilder.
