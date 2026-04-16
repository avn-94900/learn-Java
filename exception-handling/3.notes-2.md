# Java Exception Handling - Complete Notes

## OutOfMemoryError vs StackOverflowError

| Feature         | OutOfMemoryError                          | StackOverflowError                    |
| --------------- | ----------------------------------------- | ------------------------------------- |
| Type            | Error (not Exception)                     | Error (not Exception)                 |
| Cause           | Heap memory is full (JVM runs out of heap memory) | Call stack limit exceeded             |
| When it happens | Too many or large objects in memory       | Too many nested method calls          |
| Common reasons  | Memory leaks, large arrays, heavy objects | Infinite recursion, deep calls        |
| Example         | `new String[Integer.MAX_VALUE]`           | Method calls itself without base case |
| Recovery        | Hard to recover, usually program stops    | Hard to recover, fix code logic       |
| Nature          | Memory issue                              | Programming logic issue               |

## Exception Chaining

**Definition:** Technique of catching one exception and throwing another while preserving the original exception information.

**Purpose:**
- Maintain exception history
- Provide better context
- Hide implementation details while preserving debugging info

```java
try {
    // some operation
} catch (IOException e) {
    throw new ServiceException("Service failed", e); // 'e' is the cause
}

// Accessing chained exceptions
catch (ServiceException e) {
    Throwable cause = e.getCause(); // Gets original IOException
}
```

**Key methods:**
- `initCause(Throwable cause)`
- `getCause()`

## ClassNotFoundException vs NoClassDefFoundError

| Feature         | ClassNotFoundException                        | NoClassDefFoundError                            |
| --------------- | --------------------------------------------- | ----------------------------------------------- |
| Type            | Checked Exception                             | Error                                           |
| When it happens | While loading class at runtime manually       | Class missing at runtime (after compilation)    |
| Cause           | Class not found in classpath                  | Class was present earlier but now missing       |
| Common scenario | Using `Class.forName()`                       | Dependency removed or not deployed properly     |
| Handling        | Must handle using try-catch                   | Not usually handled                             |
| Recovery        | Possible (fix classpath or handle gracefully) | Hard, usually requires fixing deployment        |
| Nature          | Runtime loading issue                         | Deployment / classpath issue                    |
| Example         | `Class.forName("MissingClass")`             | Class used but .class file not found at runtime |

```java
// ClassNotFoundException
try {
    Class.forName("NonExistentClass");
} catch (ClassNotFoundException e) {
    // Handle gracefully
}

// NoClassDefFoundError - usually indicates deployment/classpath issues
```

## Try Block Without Catch/Finally

**Question:** Can we use only try blocks without catch and finally?

**Answer:** **NO** - This is a compilation error.

**Valid combinations:**
- `try-catch`
- `try-finally`
- `try-catch-finally`
- `try-with-resources`

```java
// INVALID - Compilation error
try {
    // code
}

// VALID options
try {
    // code
} catch (Exception e) {
    // handle
}

try {
    // code
} finally {
    // cleanup
}

try (FileReader fr = new FileReader("file.txt")) {
    // code
}
```

## Empty Catch Block

**Question:** Can we have an empty catch block?

**Answer:** **YES** - Syntactically valid but **NOT RECOMMENDED**.

```java
try {
    riskyOperation();
} catch (Exception e) {
    // Empty - compiles but poor practice
}
```

**Problems with empty catch:**
- Silent failures
- Difficult debugging
- Masked errors

## Finally Block Execution

**Question:** Does finally block always execute?

**Answer:** **Almost always**, with exceptions.

### Finally always executes when:
- Normal execution
- Exception thrown and caught
- Exception thrown and not caught
- Return statement in try/catch

### Finally does not execute when:
- `System.exit()` is called
- JVM crashes
- There is an infinite loop in try/catch
- Thread is interrupted or killed

```java
try {
    System.exit(0); // Finally will NOT execute
    return "try";
} catch (Exception e) {
    System.exit(0); // Finally will NOT execute
    return "catch";
} finally {
    System.out.println("This won't print if System.exit() called");
}
```

## System.exit(0) Impact

**Effect:** Immediately terminates the JVM, bypassing finally blocks.

```java
try {
    System.out.println("Try block");
    System.exit(0);
} finally {
    System.out.println("Finally - WILL NOT EXECUTE");
}
```

**Note:** Only `System.exit()` prevents finally execution in normal circumstances.

## Checked Exceptions from Static Block

**Question:** Can we throw checked exceptions from static blocks?

**Answer:** **NO** - Static blocks cannot throw checked exceptions.

```java
// INVALID - Compilation error
static {
    throw new IOException();
}

// VALID - Runtime exceptions allowed
static {
    throw new RuntimeException();
}

// WORKAROUND - Wrap in runtime exception
static {
    try {
        // risky operation
    } catch (IOException e) {
        throw new ExceptionInInitializerError(e);
    }
}
```

**Reason:** Static blocks have no method signature to declare a `throws` clause.

## Exception from Main Method

**What happens:** Uncaught exceptions in `main` terminate the program.

```java
public static void main(String[] args) throws IOException {
    throw new IOException("Main method exception");
}
```

**Process:**
1. Exception propagates up the call stack
2. If uncaught, JVM's default handler takes over
3. Stack trace prints to `System.err`
4. Program terminates with non-zero exit code
<!-- 
## JVM Exception Handling

**When an exception occurs, JVM:**
1. Creates exception object
2. Looks for matching catch block in current method
3. If not found, propagates to calling method
4. Continues up the call stack
5. If it reaches `main` uncaught:
   - Prints stack trace
   - Terminates program

**Default exception handler:**
- Prints exception class name
- Prints exception message
- Prints stack trace
- Terminates thread/program

## Stack Trace

**Definition:** Detailed report of method calls leading to an exception.

**Components:**
- Exception class and message
- Method names and line numbers
- File names
- Call sequence (most recent first)

```text
Exception in thread "main" java.lang.NullPointerException: Cannot invoke method
    at com.example.MyClass.method3(MyClass.java:15)
    at com.example.MyClass.method2(MyClass.java:10)
    at com.example.MyClass.method1(MyClass.java:5)
    at com.example.MyClass.main(MyClass.java:3)
``` -->
