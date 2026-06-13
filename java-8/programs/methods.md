```java
/*
=========================================================
JAVA STREAM COLLECTORS - UTILITY METHODS CHEAT SHEET
=========================================================

Collectors is a utility class that provides factory methods
for creating Collector objects used in Stream.collect().

Package:
import java.util.stream.Collectors;

=========================================================
1. COLLECTION CREATION
=========================================================
*/

// Collect stream elements into a List
Collectors.toList();

// Collect stream elements into a Set
Collectors.toSet();

// Collect stream elements into a specific Collection type
Collectors.toCollection(...);

// Collect elements into an unmodifiable List
Collectors.toUnmodifiableList();

// Collect elements into an unmodifiable Set
Collectors.toUnmodifiableSet();


/*
=========================================================
2. MAP CREATION
=========================================================
*/

// Convert stream elements into a Map
Collectors.toMap(...);

// Convert stream elements into a concurrent Map
Collectors.toConcurrentMap(...);

// Group elements into a Map
Collectors.groupingBy(...);

// Group elements into a concurrent Map
Collectors.groupingByConcurrent(...);

// Partition elements into true/false groups
Collectors.partitioningBy(...);


/*
=========================================================
3. STRING OPERATIONS
=========================================================
*/

// Concatenate elements into a String
Collectors.joining();

// Concatenate with delimiter
Collectors.joining(...);

// Concatenate with delimiter, prefix, suffix
Collectors.joining(...);


/*
=========================================================
4. COUNTING
=========================================================
*/

// Count number of elements
Collectors.counting();


/*
=========================================================
5. SUMMING
=========================================================
*/

// Sum int values
Collectors.summingInt(...);

// Sum long values
Collectors.summingLong(...);

// Sum double values
Collectors.summingDouble(...);


/*
=========================================================
6. AVERAGING
=========================================================
*/

// Average int values
Collectors.averagingInt(...);

// Average long values
Collectors.averagingLong(...);

// Average double values
Collectors.averagingDouble(...);


/*
=========================================================
7. STATISTICS
=========================================================
*/

// Int statistics
Collectors.summarizingInt(...);

// Long statistics
Collectors.summarizingLong(...);

// Double statistics
Collectors.summarizingDouble(...);


/*
=========================================================
8. MINIMUM / MAXIMUM
=========================================================
*/

// Find minimum element
Collectors.minBy(...);

// Find maximum element
Collectors.maxBy(...);


/*
=========================================================
9. REDUCTION
=========================================================
*/

// Generic reduction
Collectors.reducing(...);

// Reduction with identity
Collectors.reducing(...);

// Reduction with mapper and identity
Collectors.reducing(...);


/*
=========================================================
10. MAPPING OPERATIONS
=========================================================
*/

// Transform elements before downstream collector
Collectors.mapping(...);

// FlatMap elements before downstream collector
Collectors.flatMapping(...);

// Filter elements before downstream collector
Collectors.filtering(...);


/*
=========================================================
11. COLLECT AND THEN
=========================================================
*/

// Apply finisher function after collection
Collectors.collectingAndThen(...);


/*
=========================================================
12. TEEING (JAVA 12+)
=========================================================
*/

// Run two collectors simultaneously and merge result
Collectors.teeing(...);


/*
=========================================================
13. DOWNSTREAM GROUPING HELPERS
=========================================================
*/

// Group then count
Collectors.counting();

// Group then sum
Collectors.summingInt(...);
Collectors.summingLong(...);
Collectors.summingDouble(...);

// Group then average
Collectors.averagingInt(...);
Collectors.averagingLong(...);
Collectors.averagingDouble(...);

// Group then statistics
Collectors.summarizingInt(...);
Collectors.summarizingLong(...);
Collectors.summarizingDouble(...);

// Group then min
Collectors.minBy(...);

// Group then max
Collectors.maxBy(...);

// Group then map transformation
Collectors.mapping(...);

// Group then filter
Collectors.filtering(...);

// Group then flatMap
Collectors.flatMapping(...);


/*
=========================================================
14. CONCURRENT COLLECTORS
=========================================================
*/

// Concurrent Map creation
Collectors.toConcurrentMap(...);

// Concurrent grouping
Collectors.groupingByConcurrent(...);


/*
=========================================================
15. UNMODIFIABLE COLLECTIONS (JAVA 10+)
=========================================================
*/

// Unmodifiable List
Collectors.toUnmodifiableList();

// Unmodifiable Set
Collectors.toUnmodifiableSet();

// Unmodifiable Map
Collectors.toUnmodifiableMap(...);


/*
=========================================================
MOST IMPORTANT INTERVIEW METHODS
=========================================================

Collectors.toList()
Collectors.toSet()
Collectors.toMap()

Collectors.groupingBy()
Collectors.partitioningBy()

Collectors.joining()

Collectors.counting()

Collectors.summingInt()
Collectors.averagingInt()
Collectors.summarizingInt()

Collectors.maxBy()
Collectors.minBy()

Collectors.mapping()
Collectors.filtering()

Collectors.collectingAndThen()

=========================================================
METHODS RETURNING OPTIONAL
=========================================================

Collectors.maxBy(...)
Collectors.minBy(...)

=========================================================
METHODS COMMONLY USED WITH groupingBy()
=========================================================

counting()
mapping()
filtering()
flatMapping()

summingInt()
summingLong()
summingDouble()

averagingInt()
averagingLong()
averagingDouble()

summarizingInt()
summarizingLong()
summarizingDouble()

minBy()
maxBy()

collectingAndThen()

=========================================================
END OF COLLECTORS CHEAT SHEET
=========================================================
*/
```
