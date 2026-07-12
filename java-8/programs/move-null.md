In Java 8, you can use `Comparator.nullsFirst()` and `Comparator.nullsLast()` while sorting.

### 1. Move all `null` values to the **last**

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {

        List<Integer> list = Arrays.asList(5, null, 2, 8, null, 1, 4);

        list.stream()
            .sorted(Comparator.nullsLast(Integer::compareTo))
            .forEach(System.out::println);
    }
}
```
```java
import java.util.*;
import java.util.stream.Stream;

public class Main {

    public static void main(String[] args) {

        List<Integer> list = Arrays.asList(5, null, 2, 8, null, 1, 4);

        Stream<Integer> result = Stream.concat(

                list.stream()
                        .filter(Objects::isNull),

                list.stream()
                        .filter(Objects::nonNull)
                        .sorted()

        );

        result.forEach(System.out::println);
    }
}
```
**Output:**

```
1
2
4
5
8
null
null
```

---

### 2. Move all `null` values to the **first**

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {

        List<Integer> list = Arrays.asList(5, null, 2, 8, null, 1, 4);

        list.stream()
            .sorted(Comparator.nullsFirst(Integer::compareTo))
            .forEach(System.out::println);
    }
}
```

**Output:**

```
null
null
1
2
4
5
8
```

---

## For String List

### Nulls Last

```java
List<String> names = Arrays.asList("Ram", null, "Anil", "John", null);

names.stream()
     .sorted(Comparator.nullsLast(String::compareTo))
     .forEach(System.out::println);
```

---

### Nulls First

```java
List<String> names = Arrays.asList("Ram", null, "Anil", "John", null);

names.stream()
     .sorted(Comparator.nullsFirst(String::compareTo))
     .forEach(System.out::println);
```

---

## For Employee Objects

Suppose:

```java
class Employee {
    String name;
    Integer salary;

    Employee(String name, Integer salary) {
        this.name = name;
        this.salary = salary;
    }

    public Integer getSalary() {
        return salary;
    }

    @Override
    public String toString() {
        return name + " : " + salary;
    }
}
```

### Sort by salary with `null` salaries last

```java
employees.stream()
         .sorted(Comparator.comparing(
                 Employee::getSalary,
                 Comparator.nullsLast(Integer::compareTo)))
         .forEach(System.out::println);
```

---

### Sort by salary with `null` salaries first

```java
employees.stream()
         .sorted(Comparator.comparing(
                 Employee::getSalary,
                 Comparator.nullsFirst(Integer::compareTo)))
         .forEach(System.out::println);
```

---

## Interview Point

`Comparator.nullsFirst()` and `Comparator.nullsLast()` are wrapper comparators.

**Syntax:**

```java
Comparator.nullsFirst(Comparator.naturalOrder())

Comparator.nullsLast(Comparator.naturalOrder())
```

or

```java
Comparator.nullsFirst(Integer::compareTo)

Comparator.nullsLast(String::compareTo)
```

These methods prevent `NullPointerException` during sorting and control whether `null` elements appear at the beginning or the end of the sorted collection.


<br/><br/><br/><br/><br/>

Below is a complete Java 8 example covering all common interview cases.

```java
import java.util.*;
import java.util.stream.Collectors;

class Employee {

    private Integer id;
    private String name;
    private Integer salary;

    public Employee(Integer id, String name, Integer salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }

    public Integer getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public Integer getSalary() {
        return salary;
    }

    @Override
    public String toString() {
        return "Employee{id=" + id +
                ", name='" + name + '\'' +
                ", salary=" + salary +
                '}';
    }
}

public class Main {

    public static void main(String[] args) {

        List<Employee> employees = Arrays.asList(

                new Employee(1, "Anil", 50000),
                new Employee(2, "John", null),   // salary is null
                null,                            // Employee object itself is null
                new Employee(3, "David", 35000),
                new Employee(4, "Ram", null),    // salary is null
                new Employee(5, "Steve", 80000),
                null                             // Employee object itself is null
        );

        // -------------------------------------------------------
        // Example 1 : Employee object NULL -> Last
        // -------------------------------------------------------

        System.out.println("Employee object nulls LAST");

        employees.stream()
                .sorted(Comparator.nullsLast(
                        Comparator.comparing(Employee::getId)))
                .forEach(System.out::println);


        // -------------------------------------------------------
        // Example 2 : Employee object NULL -> First
        // -------------------------------------------------------

        System.out.println("\nEmployee object nulls FIRST");

        employees.stream()
                .sorted(Comparator.nullsFirst(
                        Comparator.comparing(Employee::getId)))
                .forEach(System.out::println);


        // -------------------------------------------------------
        // Example 3 : Salary NULL -> Last
        // -------------------------------------------------------

        System.out.println("\nSalary nulls LAST");

        employees.stream()
                .filter(Objects::nonNull)   // remove null employee objects
                .sorted(
                        Comparator.comparing(
                                Employee::getSalary,
                                Comparator.nullsLast(Integer::compareTo)
                        )
                )
                .forEach(System.out::println);


        // -------------------------------------------------------
        // Example 4 : Salary NULL -> First
        // -------------------------------------------------------

        System.out.println("\nSalary nulls FIRST");

        employees.stream()
                .filter(Objects::nonNull)
                .sorted(
                        Comparator.comparing(
                                Employee::getSalary,
                                Comparator.nullsFirst(Integer::compareTo)
                        )
                )
                .forEach(System.out::println);


        // -------------------------------------------------------
        // Example 5 : Salary DESC + NULL Last
        // -------------------------------------------------------

        System.out.println("\nSalary DESC + null LAST");

        employees.stream()
                .filter(Objects::nonNull)
                .sorted(
                        Comparator.comparing(
                                Employee::getSalary,
                                Comparator.nullsLast(Comparator.reverseOrder())
                        )
                )
                .forEach(System.out::println);


        // -------------------------------------------------------
        // Example 6 : Sort by Name
        // -------------------------------------------------------

        System.out.println("\nSort by Name");

        employees.stream()
                .filter(Objects::nonNull)
                .sorted(Comparator.comparing(Employee::getName))
                .forEach(System.out::println);

    }
}
```

---

# Difference between the two `nullsLast()` usages

## Case 1: Employee object itself is `null`

```java
employees.stream()
         .sorted(
             Comparator.nullsLast(
                 Comparator.comparing(Employee::getId)
             )
         )
```

Input:

```java
[
Employee(1),
null,
Employee(2),
null,
Employee(3)
]
```

Output:

```java
Employee(1)
Employee(2)
Employee(3)
null
null
```

Here, `Comparator.nullsLast()` is comparing **Employee objects**.

---

## Case 2: Employee exists, but salary is `null`

```java
employees.stream()
         .filter(Objects::nonNull)
         .sorted(
             Comparator.comparing(
                 Employee::getSalary,
                 Comparator.nullsLast(Integer::compareTo)
             )
         )
```

Input:

```java
Employee(1,50000)
Employee(2,null)
Employee(3,30000)
Employee(4,null)
```

Output:

```java
Employee(3,30000)
Employee(1,50000)
Employee(2,null)
Employee(4,null)
```

Here, the **Employee object is not null**, only the **salary field** is null.

---

# Interview Trick Question

Sometimes an interviewer combines both cases:

* Employee object can be `null`
* Employee salary can also be `null`

Then use **both** `nullsLast()` methods together.

```java
employees.stream()
        .sorted(
            Comparator.nullsLast(
                Comparator.comparing(
                    Employee::getSalary,
                    Comparator.nullsLast(Integer::compareTo)
                )
            )
        )
        .forEach(System.out::println);
```

This handles:

* ✅ `Employee` object is `null` → moved to the end.
* ✅ `salary` is `null` → moved after employees with non-null salaries.
* ✅ No `NullPointerException`.

This nested approach is a common Java 8 interview question because it tests whether you understand the difference between a **null object** and a **null field inside an object**.
