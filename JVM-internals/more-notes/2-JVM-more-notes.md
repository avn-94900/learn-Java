

<br/><br/><br/>

## 🏗️ JVM Memory Spaces Recap

### **Metaspace vs PermGen**

* **PermGen (Pre-Java 8)**

  * Stored class metadata, methods, and interned Strings.
  * Fixed size, part of **JVM heap**.
  * Could cause **OutOfMemoryError: PermGen space** if too many classes loaded.
  * Difficult to tune in dynamic class-loading environments (e.g., app servers, frameworks).

* **Metaspace (Java 8 onwards)**

  * Replaced PermGen.
  * Allocated in **native (OS) memory**, not heap.
  * Grows dynamically, limited by **system memory** unless capped with `-XX:MaxMetaspaceSize`.
  * Problems:

    * If too many classes are loaded (e.g., frameworks with proxies, dynamic code generation), can still cause **server crash** due to native memory exhaustion.
    * Needs monitoring.

---

## 🗑️ Garbage Collection (GC) Algorithms

### **1. Serial GC**

* Single-threaded collection.
* Best for small apps or client machines.
* Causes **stop-the-world pauses**.

### **2. Parallel GC (a.k.a. Throughput Collector)**

* Multi-threaded, uses multiple CPU cores.
* Suitable for large heaps, batch jobs.
* Still has stop-the-world pauses.
* Variants:

  * **Parallel New GC** – parallel for young gen.
  * **Parallel Old GC** – also parallel for old gen.

### **3. CMS (Concurrent Mark-Sweep)**

* Reduces pause time by doing GC concurrently with application threads.
* Old generation collected without long pauses.
* Drawbacks:

  * High CPU usage.
  * Fragmentation issues (can still cause Full GC).
* Deprecated since JDK 9.

### **4. G1 GC (Garbage First)**

* Default GC in Java 9+.
* Divides heap into equal-sized **regions** (no strict Young/Old boundaries).
* Prioritizes collecting regions with the most garbage.
* Predictable pause times (`-XX:MaxGCPauseMillis`).
* Great for **large heaps & server apps**.

---

## ⚠️ Problems with GC

* **Efficiency challenges**: identifying live vs. garbage objects correctly.
* **Stop-the-world pauses**: application halts during GC.
* **OOM Errors**: if heap or Metaspace not tuned correctly.
* **Fragmentation** (esp. CMS): memory scattered, can’t allocate large objects.

---

## ⚙️ JVM GC Tuning & Best Practices

1. **Monitor GC logs**

   * Use `-Xlog:gc*` (Java 9+) or `-verbose:gc` (pre-Java 9).
   * Track frequency & duration of collections.

2. **Choose right GC**

   * Small app → Serial GC.
   * Multi-threaded/batch jobs → Parallel GC.
   * Low latency services → G1 GC (or ZGC/Shenandoah in latest JDKs).

3. **Heap Tuning**

   * `-Xms` and `-Xmx` to set min/max heap.
   * Avoid frequent heap resizing.

4. **Metaspace Tuning**

   * Use `-XX:MaxMetaspaceSize` to cap memory use.
   * Monitor classloading with `jcmd VM.classloaders`.

5. **Application-level Best Practices**

   * Avoid classloader leaks (e.g., redeploy in app servers).
   * Release references to unused objects.
   * Use object pooling cautiously (may cause memory retention).
   * Prefer immutable objects (shorter lifespan).

---

✅ So in short:

* **PermGen → Metaspace** migration was done to fix fixed-size issues.
* **GC choices** depend on app type (small app, throughput-heavy, low-latency).
* **Problems**: OOM, fragmentation, pauses.
* **Best practices**: monitor, tune heap/metaspace, choose right GC, avoid memory leaks.

---

