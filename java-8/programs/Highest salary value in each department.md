This is one of the most common Java 8 interview questions.

## Employee Class

```java
import java.util.*;

class Employee {

    private int id;
    private String name;
    private String department;
    private double salary;

    public Employee(int id, String name, String department, double salary) {
        this.id = id;
        this.name = name;
        this.department = department;
        this.salary = salary;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getDepartment() {
        return department;
    }

    public double getSalary() {
        return salary;
    }

    @Override
    public String toString() {
        return id + " " + name + " " + department + " " + salary;
    }
}
```

---

## Input

```java
List<Employee> employees = Arrays.asList(
        new Employee(1, "Anil", "IT", 70000),
        new Employee(2, "John", "IT", 90000),
        new Employee(3, "Ram", "HR", 50000),
        new Employee(4, "David", "HR", 65000),
        new Employee(5, "Steve", "Finance", 80000),
        new Employee(6, "Sam", "Finance", 75000)
);
```

---

# Solution 1 (Most Asked)

```java
Map<String, Optional<Employee>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.maxBy(
                                Comparator.comparing(Employee::getSalary)
                        )
                ));

result.forEach((dept, emp) ->
        System.out.println(dept + " -> " + emp.get()));
```

### Output

```
Finance -> 5 Steve Finance 80000.0
HR -> 4 David HR 65000.0
IT -> 2 John IT 90000.0
```

---

# Solution 2 (Without Optional)

This is cleaner because the result is `Map<String, Employee>`.

```java
Map<String, Employee> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.collectingAndThen(
                                Collectors.maxBy(
                                        Comparator.comparing(Employee::getSalary)
                                ),
                                Optional::get
                        )
                ));

result.forEach((dept, emp) ->
        System.out.println(dept + " -> " + emp));
```

### Output

```
Finance -> 5 Steve Finance 80000.0
HR -> 4 David HR 65000.0
IT -> 2 John IT 90000.0
```

---

# Solution 3 (Using `toMap`)

```java
Map<String, Employee> result =
        employees.stream()
                .collect(Collectors.toMap(
                        Employee::getDepartment,
                        emp -> emp,
                        (e1, e2) -> e1.getSalary() > e2.getSalary() ? e1 : e2
                ));

result.forEach((dept, emp) ->
        System.out.println(dept + " -> " + emp));
```

---

## Interview Follow-up

### Highest salary value in each department

```java
Map<String, Double> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.collectingAndThen(
                                Collectors.maxBy(
                                        Comparator.comparing(Employee::getSalary)
                                ),
                                emp -> emp.get().getSalary()
                        )
                ));
```

Output:

```
Finance -> 80000.0
HR -> 65000.0
IT -> 90000.0
```

---

## Which solution should you use?

| Approach                             | Returns                           | Interview Rating       |
| ------------------------------------ | --------------------------------- | ---------------------- |
| `groupingBy() + maxBy()`             | `Map<String, Optional<Employee>>` | ⭐⭐⭐⭐                   |
| `groupingBy() + collectingAndThen()` | `Map<String, Employee>`           | ⭐⭐⭐⭐⭐ (Most preferred) |
| `toMap()` with merge function        | `Map<String, Employee>`           | ⭐⭐⭐⭐                   |

For interviews, the **`groupingBy()` + `collectingAndThen(maxBy(...), Optional::get)`** solution is the one most interviewers expect because it clearly shows your knowledge of collectors and avoids returning `Optional` in the final map.

<br/>

```java
// highest employee to top 3 highest-paid employees in each department,
Map<String, List<Employee>> result =
    employees.stream()
             .collect(Collectors.groupingBy(
                 Employee::getDepartment,
                 Collectors.collectingAndThen(
                     Collectors.toList(),
                     list -> list.stream()
                                 .sorted(Comparator.comparing(Employee::getSalary)
                                         .reversed())
                                 .limit(3)
                                 .collect(Collectors.toList())
                 )
             ));
```