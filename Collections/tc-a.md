# Java Collections Time Complexities (Interview Notes)


## 1. ArrayList

### Internal Structure

```text
Dynamic Array
```

| Operation       | Best | Average | Worst | Explanation               |
| --------------- | ---- | ------- | ----- | ------------------------- |
| get(index)      | O(1) | O(1)    | O(1)  | Direct array access       |
| add(end)        | O(1) | O(1)    | O(n)  | Resize and copy may occur |
| add(index)      | O(1) | O(n)    | O(n)  | Elements need shifting    |
| remove(end)     | O(1) | O(1)    | O(1)  | Remove last element       |
| remove(index)   | O(n) | O(n)    | O(n)  | Shift remaining elements  |
| contains()      | O(1) | O(n)    | O(n)  | Linear search             |
| iterator.next() | O(1) | O(1)    | O(1)  | Move to next index        |

### Best Use Case

* Frequent reads
* Random access by index

---

## 2. LinkedList

### Internal Structure

```text
Doubly Linked List
```

| Operation       | Best | Average | Worst | Explanation         |
| --------------- | ---- | ------- | ----- | ------------------- |
| addFirst()      | O(1) | O(1)    | O(1)  | Update head pointer |
| addLast()       | O(1) | O(1)    | O(1)  | Update tail pointer |
| removeFirst()   | O(1) | O(1)    | O(1)  | Remove head node    |
| removeLast()    | O(1) | O(1)    | O(1)  | Remove tail node    |
| get(index)      | O(1) | O(n)    | O(n)  | Traverse list       |
| add(index)      | O(1) | O(n)    | O(n)  | Find position first |
| remove(index)   | O(1) | O(n)    | O(n)  | Locate node first   |
| contains()      | O(1) | O(n)    | O(n)  | Sequential search   |
| iterator.next() | O(1) | O(1)    | O(1)  | Move to next node   |

### Best Use Case

* Frequent insertions/deletions

---

## 3. HashMap

### Internal Structure

```text
Hash Table
+
Linked List / Red-Black Tree
```

| Operation       | Best | Average | Worst    | Explanation                |
| --------------- | ---- | ------- | -------- | -------------------------- |
| put()           | O(1) | O(1)    | O(log n) | Treeified bucket lookup    |
| get()           | O(1) | O(1)    | O(log n) | Hash collision tree search |
| remove()        | O(1) | O(1)    | O(log n) | Locate node in tree        |
| containsKey()   | O(1) | O(1)    | O(log n) | Same as get()              |
| containsValue() | O(1) | O(n)    | O(n)     | Scan all entries           |
| iteration       | O(n) | O(n)    | O(n)     | Visit all entries          |

### Best Use Case

* Fast key-value lookup

---

## 4. HashSet

### Internal Structure

```text
HashMap
```

| Operation  | Best | Average | Worst    | Explanation        |
| ---------- | ---- | ------- | -------- | ------------------ |
| add()      | O(1) | O(1)    | O(log n) | Hash bucket lookup |
| remove()   | O(1) | O(1)    | O(log n) | Remove from bucket |
| contains() | O(1) | O(1)    | O(log n) | Hash lookup        |
| iteration  | O(n) | O(n)    | O(n)     | Visit all elements |

### Best Use Case

* Fast uniqueness checks

---

## 5. LinkedHashMap

### Internal Structure

```text
HashMap
+
Doubly Linked List
```

| Operation       | Best | Average | Worst    | Explanation                  |
| --------------- | ---- | ------- | -------- | ---------------------------- |
| put()           | O(1) | O(1)    | O(log n) | Hash lookup + maintain order |
| get()           | O(1) | O(1)    | O(log n) | Hash bucket lookup           |
| remove()        | O(1) | O(1)    | O(log n) | Remove from map and list     |
| containsKey()   | O(1) | O(1)    | O(log n) | Same as HashMap              |
| containsValue() | O(1) | O(n)    | O(n)     | Full traversal               |
| iteration       | O(n) | O(n)    | O(n)     | Traverse linked list         |

### Best Use Case

* Preserve insertion order

---

## 6. LinkedHashSet

### Internal Structure

```text
LinkedHashMap
↓
HashMap + Doubly Linked List
```

| Operation  | Best | Average | Worst    | Explanation              |
| ---------- | ---- | ------- | -------- | ------------------------ |
| add()      | O(1) | O(1)    | O(log n) | Hash lookup              |
| remove()   | O(1) | O(1)    | O(log n) | Remove from backing map  |
| contains() | O(1) | O(1)    | O(log n) | Hash lookup              |
| iteration  | O(n) | O(n)    | O(n)     | Traverse insertion order |

### Best Use Case

* Unique elements + insertion order

---

## 7. TreeMap

### Internal Structure

```text
Red-Black Tree
```

| Operation       | Best     | Average  | Worst    | Explanation               |
| --------------- | -------- | -------- | -------- | ------------------------- |
| put()           | O(log n) | O(log n) | O(log n) | Tree insertion            |
| get()           | O(log n) | O(log n) | O(log n) | Tree traversal            |
| remove()        | O(log n) | O(log n) | O(log n) | Tree deletion             |
| containsKey()   | O(log n) | O(log n) | O(log n) | Tree search               |
| containsValue() | O(n)     | O(n)     | O(n)     | Scan all nodes            |
| firstKey()      | O(log n) | O(log n) | O(log n) | Traverse leftmost branch  |
| lastKey()       | O(log n) | O(log n) | O(log n) | Traverse rightmost branch |
| iteration       | O(n)     | O(n)     | O(n)     | In-order traversal        |

### Best Use Case

* Sorted keys

---

## 8. TreeSet

### Internal Structure

```text
TreeMap
↓
Red-Black Tree
```

| Operation  | Best     | Average  | Worst    | Explanation      |
| ---------- | -------- | -------- | -------- | ---------------- |
| add()      | O(log n) | O(log n) | O(log n) | Tree insertion   |
| remove()   | O(log n) | O(log n) | O(log n) | Tree deletion    |
| contains() | O(log n) | O(log n) | O(log n) | Tree search      |
| first()    | O(log n) | O(log n) | O(log n) | Leftmost node    |
| last()     | O(log n) | O(log n) | O(log n) | Rightmost node   |
| iteration  | O(n)     | O(n)     | O(n)     | Sorted traversal |

### Best Use Case

* Sorted unique elements

---

## 9. PriorityQueue

### Internal Structure

```text
Binary Heap
```

| Operation       | Best     | Average  | Worst    | Explanation         |
| --------------- | -------- | -------- | -------- | ------------------- |
| peek()          | O(1)     | O(1)     | O(1)     | Root element access |
| add()           | O(log n) | O(log n) | O(log n) | Heapify up          |
| poll()          | O(log n) | O(log n) | O(log n) | Heapify down        |
| remove(element) | O(1)     | O(n)     | O(n)     | Search required     |
| contains()      | O(1)     | O(n)     | O(n)     | Linear search       |
| iteration       | O(n)     | O(n)     | O(n)     | Visit heap elements |

### Best Use Case

* Top-K problems
* Scheduling systems

---

## 10. ConcurrentHashMap (Java 8+)

### Internal Structure

```text
Hash Table
+
CAS
+
Fine-Grained Locking
+
Red-Black Tree Buckets
```

| Operation       | Best | Average | Worst    | Explanation              |
| --------------- | ---- | ------- | -------- | ------------------------ |
| get()           | O(1) | O(1)    | O(log n) | Treeified bucket search  |
| put()           | O(1) | O(1)    | O(log n) | Concurrent bucket update |
| remove()        | O(1) | O(1)    | O(log n) | Bucket lookup            |
| containsKey()   | O(1) | O(1)    | O(log n) | Same as get()            |
| containsValue() | O(1) | O(n)    | O(n)     | Scan entire map          |
| iteration       | O(n) | O(n)    | O(n)     | Visit all entries        |

### Best Use Case

* High-concurrency applications

---

## Ultimate Interview Memory Table

| Collection        | Internal DS           | Lookup/Search | Insert   | Delete   | Ordered   |
| ----------------- | --------------------- | ------------- | -------- | -------- | --------- |
| ArrayList         | Dynamic Array         | O(n)          | O(1)*    | O(n)     | Yes       |
| LinkedList        | Doubly Linked List    | O(n)          | O(1)     | O(1)     | Yes       |
| HashMap           | Hash Table            | O(1)          | O(1)     | O(1)     | No        |
| HashSet           | HashMap               | O(1)          | O(1)     | O(1)     | No        |
| LinkedHashMap     | Hash + DLL            | O(1)          | O(1)     | O(1)     | Insertion |
| LinkedHashSet     | Hash + DLL            | O(1)          | O(1)     | O(1)     | Insertion |
| TreeMap           | Red-Black Tree        | O(log n)      | O(log n) | O(log n) | Sorted    |
| TreeSet           | Red-Black Tree        | O(log n)      | O(log n) | O(log n) | Sorted    |
| PriorityQueue     | Binary Heap           | O(n)          | O(log n) | O(log n) | Priority  |
| ConcurrentHashMap | Concurrent Hash Table | O(1)          | O(1)     | O(1)     | No        |

**Memory Rule:**

* Hash-based → Average **O(1)**
* Tree-based → **O(log n)**
* ArrayList → Fast Read, Slow Middle Insert/Delete
* LinkedList → Slow Search, Fast Insert/Delete
* Heap (PriorityQueue) → Fast Peek, Logarithmic Add/Remove
