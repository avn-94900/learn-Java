# Java 21: Sequenced Collection Interfaces

## Overview

Java 21 introduced three new interfaces to the Collection hierarchy:

- `SequencedCollection`
- `SequencedSet`
- `SequencedMap`

These interfaces sit between existing interfaces in the hierarchy:

- `List` now extends `SequencedCollection`
- `Deque` now extends `SequencedCollection`
- `LinkedHashSet` now implements `SequencedSet` (which extends `Set` and `SequencedCollection`)
- `SortedSet` now extends `SequencedSet`
- `LinkedHashMap` now implements `SequencedMap` (which extends `Map`)
- `SortedMap` now extends `SequencedMap`

## Problem Statement

Collections with predictable order (insertion or sorted) had inconsistent APIs for common operations:

- Accessing first and last elements
- Adding or removing at first and last positions
- Obtaining a reversed view

### Examples of Inconsistency

- `Deque`: `getFirst()`, `addFirst()`, `removeFirst()`
- `List`: `list.get(0)`, `add(0, ...)`, no built-in reverse
- `SortedSet`: `first()`, `last()`, `descendingIterator()`
- `LinkedHashSet`: no direct methods, manual iteration required

### Solution

The Sequenced interfaces provide uniform methods across all collection types:

- `getFirst()`, `getLast()`
- `addFirst()`, `addLast()`
- `removeFirst()`, `removeLast()`
- `reversed()`

## Implementation Criteria

A collection must meet all three criteria to implement Sequenced interfaces:

1. **Predictable iteration order**
   - Insertion order (List, LinkedHashSet, LinkedHashMap, Deque)
   - Or sorted order (SortedSet, SortedMap)

2. **Support for accessing and manipulating first and last elements**
   - Must support add, remove, or get operations on first and last

3. **Support for reversible view**
   - Must provide a reversed view of the collection (not a copy)

## Collection Analysis

| Collection | Predictable Order | First/Last Support | Reversible | Included |
|------------|-------------------|--------------------|-----------|-----------| 
| List | Yes (insertion) | Yes | Yes | SequencedCollection |
| Deque | Yes (insertion) | Yes | Yes | SequencedCollection |
| Queue | Yes (insertion) | No | No | No |
| PriorityQueue | No (heap-based) | No | No | No |
| HashSet | No | No | No | No |
| LinkedHashSet | Yes (insertion) | Yes | Yes | SequencedSet |
| SortedSet | Yes (sorted) | Yes | Yes | SequencedSet |
| HashMap | No | No | No | No |
| LinkedHashMap | Yes (insertion) | Yes | Yes | SequencedMap |
| SortedMap | Yes (sorted) | Yes | Yes | SequencedMap |

## Design Rationale

### SequencedSet
- Extends `SequencedCollection` while maintaining the no-duplicates constraint

### SequencedMap
- Provides specialized methods for key-value pairs:
  - `firstEntry()`, `lastEntry()`
  - `pollFirstEntry()`, `pollLastEntry()`
  - `putFirst()`, `putLast()`

This design preserves set and map semantics while sharing first/last and reversed functionality across collection types.

## Usage Example

```java
List<String> list = new ArrayList<>(List.of("B", "C", "D"));
list.addFirst("A");      // ["A", "B", "C", "D"]
list.addLast("Z");       // ["A", "B", "C", "D", "Z"]
list.removeFirst();      // ["B", "C", "D", "Z"]
list.removeLast();       // ["B", "C", "D"]
List<String> reversed = list.reversed(); // ["D", "C", "B"]
```

The same methods apply to `Deque`, `LinkedHashSet`, and `LinkedHashMap`.

**Note:** For `SortedSet` and `SortedMap`, `addFirst()` and `addLast()` throw `UnsupportedOperationException`, but `getFirst()`, `getLast()`, and `reversed()` are supported.

## Interface Summary

| Interface | Allows Duplicates | Predictable Order | Primary Methods |
|-----------|-------------------|-------------------|-----------------|
| SequencedCollection | Yes | insertion/sorted | getFirst, getLast, addFirst, addLast, removeFirst, removeLast, reversed |
| SequencedSet | No | insertion/sorted | getFirst, getLast, removeFirst, removeLast, reversed |
| SequencedMap | N/A | insertion/sorted | firstEntry, lastEntry, putFirst, putLast, pollFirstEntry, pollLastEntry, reversed |

## Key Benefits

- Unified API for first/last element access and reversed views
- Consistency across List, Deque, LinkedHashSet, SortedSet, LinkedHashMap, and SortedMap
- Improved code readability and maintainability

## Quick Reference

When determining if a collection should implement Sequenced interfaces, verify:

- Predictable iteration order exists
- First and last element access and manipulation is supported
- Reversible views are possible
- Check if duplicates are allowed (determines Set vs Collection)