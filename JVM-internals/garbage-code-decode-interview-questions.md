# Garbage Collection (GC) in Java — Interview Notes


## 1. What is Garbage Collection?

Automatic memory management process that:
- Looks at **heap memory**
- Identifies and removes **unreferenced objects**
- Reclaims memory automatically — no manual allocation/deallocation needed

**Key Terms**
- **Referenced Object** — still has a reference pointing to it (in use)
- **Unreferenced Object** — no references → eligible for GC

---

## 2. Heap vs Stack Memory

| Memory | Stores | Example |
|--------|--------|---------|
| **Heap** | All objects, managed by GC, shared across threads | `new Customer()` object |
| **Stack** | Local variables, primitives, object references | `obj` reference variable |

```java
Customer obj = new Customer();
// obj (reference) → Stack
// Customer object → Heap
```

---

## 3. Making an Object Eligible for GC

An object becomes eligible when **no reference points to it**.

**1) Set reference to null**
```java
Customer obj = new Customer();
obj = null;
```

**2) Reassign reference**
```java
Customer obj = new Customer();
obj = new Customer(); // old object eligible
```

**3) Island of Isolation** — objects reference each other but no external reference exists
```java
class A { B b; }
class B { A a; }
// If both lose external references → eligible for GC
```

---

## 4. GC Phases

**Phase 1 — Mark**
- Referenced objects → kept
- Unreferenced objects → marked for deletion

**Phase 2 — Delete**
- Normal Deletion — removes unused objects
- Deletion + Compaction — moves remaining objects to start of heap, creates contiguous memory blocks → faster allocation, less fragmentation

---

## 5. Generational GC (HotSpot JVM)

**Why?** Most objects die young (*Infant Mortality / Die Young Strategy*) → GC focuses on young objects → better performance.

**Heap Structure**
```
Heap
 ├── Young Generation
 │     ├── Eden
 │     ├── Survivor S0
 │     └── Survivor S1
 ├── Old Generation (Tenured)
 └── Metaspace (Java 8+)  ← replaced PermGen
```

---

## 6. GC Process Flow

1. New objects created in **Eden**
2. Eden fills → **Minor GC** triggered → unreferenced removed, survivors → S0
3. Eden fills again → Minor GC → Eden + S0 survivors → S1, age increments
4. Repeat: objects cycle `S0 → S1 → S0`, age increases each cycle
5. Age reaches threshold (e.g. **8**) → object **promoted** to Old Generation

---

## 7. Types of GC

| Type | Where | Trigger | Speed |
|------|-------|---------|-------|
| **Minor GC** | Young Generation | Eden fills | Fast, frequent |
| **Major GC (Full GC)** | Old Generation | Old Gen fills | Slow |

---

## 8. finalize() Method

- Defined in `java.lang.Object`, called before GC destroys an object
- Used for cleanup / releasing external resources

```java
@Override
protected void finalize() {
    System.out.println("Cleanup done");
}
```

⚠ **Deprecated** — do not rely on it. Use `try-with-resources` / `AutoCloseable` instead.

---

## 9. GC Thread & Daemon Threads

- The **GC runs as a background / daemon thread**
- A daemon thread runs behind the application, started by the JVM, and **stops automatically** when all non-daemon threads finish
- Examples: GC thread, finalizer thread

---

## 10. Memory Leaks

A memory leak occurs when GC **cannot reclaim objects** that are still referenced but no longer needed → can cause `OutOfMemoryError`.

**Symptoms**
- Performance degrades over time
- Memory usage keeps increasing

**Causes**

| Cause | Description |
|-------|-------------|
| Unwanted object references | Objects no longer needed but still referenced |
| Long-lived static objects | Live for entire application lifespan |
| Native resource leaks | Uncleaned JNI (C/C++) resources |

**Prevention**
- Avoid unnecessary object creation
- Use `StringBuilder` instead of String concatenation
- Always close `ResultSet`, `Statement`, `Connection` — use **try-with-resources**
- Avoid long-lived static references; set to `null` when done
- Time out idle sessions
- Do not call `System.gc()`

**Detection Tools**

| Tool | Purpose |
|------|---------|
| VisualVM / JConsole | Monitor heap usage |
| YourKit / Java Flight Recorder | Memory profiling, object retention graphs |
| Eclipse MAT | Heap dump analysis |

---

## 11. PermGen → Metaspace

| Feature | PermGen | Metaspace |
|---------|---------|-----------|
| Location | Heap | Native Memory |
| Size | Fixed | Dynamic |
| Java Version | Java ≤ 7 | Java 8+ |

---

## 12. Quick Revision Summary

- Objects → **Heap** | References → **Stack**
- Unreferenced objects → eligible for GC
- GC Phases: Mark → Delete → Compaction
- Heap: Young (Eden + S0 + S1) → Old → Metaspace
- Eden full → Minor GC | Old Gen full → Major GC
- Survive multiple cycles → **Promotion** to Old Gen
- Most objects die young → **Generational GC**
- GC = daemon thread
- `finalize()` → deprecated → use `try-with-resources`
- PermGen → replaced by **Metaspace** (Java 8+)

---

## 13. Common Interview Questions

1. What is Garbage Collection?
2. How does GC work internally?
3. Difference between Minor GC and Major GC?
4. What is Generational GC?
5. Explain Eden, Survivor, Old Gen
6. What is Promotion? What is Compaction?
7. What makes an object eligible for GC?
8. Why is `finalize()` deprecated?
9. What replaced PermGen?
10. Is GC a foreground or background thread?
11. How do you manage memory leaks?

---

## 14. Next Topics (Mid–Senior Interviews)

- GC Algorithms: Serial, Parallel, CMS, G1, ZGC, Shenandoah
- JVM GC tuning flags
- Memory leak debugging tools
