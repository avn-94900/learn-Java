

## 🏢 Comprehensive Employee Examples

This section demonstrates various stream operations applied specifically to a list of `Employee` objects, covering common data manipulation tasks.


- [Iterating Over Employees](#iterating-over-employees)
- [Collecting & Filtering Employee Data](#collecting--filtering-employee-data)
    - [Collect only the names of all employees into a new List.](#collect-only-the-names-of-all-employees-into-a-new-list)
    - [Filter employees older than 30.](#filter-employees-older-than-30)
    - [Find unique and duplicate employee names.](#find-unique-and-duplicate-employee-names)
    - [Store unique employee objs.](#store-unique-employee-objs)
    - [Store unique employee objs on the basis of -(key,value).](#store-unique-employee-objs-on-the-basis-of--keyvalue)
- [Grouping Employees](#grouping-employees)
    - [Group employees by their Department.](#group-employees-by-their-department)
    - [Group employees by Active Status (Active vs. Inactive).](#group-employees-by-active-status-active-vs-inactive)
    - [Count employees in each Department.](#count-employees-in-each-department)
- [Sorting Employees](#sorting-employees)
    - [Sort employees by Name in ascending order.](#sort-employees-by-name-in-ascending-order)
    - [Sort employees by Name in descending order.](#sort-employees-by-name-in-descending-order)
    - [Multi-level sorting: Sort by City Descending, then by Name Ascending.](#multi-level-sorting-sort-by-city-descending-then-by-name-ascending)
    - [Multi-level sorting: Sort by City Descending, then by Name Descending.](#multi-level-sorting-sort-by-city-descending-then-by-name-descending)
    - [Select the top 3 Employees with the highest Salaries.](#select-the-top-3-employees-with-the-highest-salaries)
    - [Select Employees starting from the 4th highest Salary onwards.](#select-employees-starting-from-the-4th-highest-salary-onwards)
    - [Select the 2nd & 3rd youngest Employees.](#select-the-2nd--3rd-youngest-employees)
- [Employee Statistics](#-employee-statistics)
    - [Find the Employee with the Maximum Salary.](#find-the-employee-with-the-maximum-salary)
    - [Find the Employee with the Minimum Salary.](#find-the-employee-with-the-minimum-salary)
    - [Calculate Summary Statistics for Employee Ages (Count, Sum, Min, Average, Max).](#calculate-summary-statistics-for-employee-ages-count-sum-min-average-max)
    - [Find the Highest Salary in each Department.](#find-the-highest-salary-in-each-department)
- [Stream Re-use Caveat](#-stream-re-use-caveat)
    - [Streams cannot be reused after a terminal operation is called.](#streams-cannot-be-reused-after-a-terminal-operation-is-called-attempting-to-do-so-results-in-an-illegalstateexception)
    - [To perform multiple operations, either recreate the stream or use a Supplier.](#to-perform-multiple-operations-either-recreate-the-stream-or-use-a-supplier)
- [Conclusion](#-conclusion)



---
### Additional Resources

If you found this guide helpful, you might also be interested in my other Spring Framework resources:
- [Core Java & Java-8 Interview Questions](https://github.com/anilvn/Java-Interview-Questions/tree/main)
- [Spring Boot Interview Questions](https://github.com/anilvn/spring-boot-interview-questions/tree/main)
- [Microservices with Spring Cloud Tutorials](https://javatechonline.com/microservices-tutorial/)

Feel free to star and fork these repositories if you find them useful!

---

### Employee Class Definition

* _First, let's define the `Employee` class used in these examples._
    ```java
    import java.util.Objects;

    class Employee {
        private String name;
        private int age;
        private String gender;
        private double salary;
        private String city;
        private String deptName;
        private boolean activeEmp;
       
        // Constructors , Getters, Setters , toStrring(), hashCode()

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Employee employee = (Employee) o;
            return age == employee.age && Double.compare(employee.salary, salary) == 0 &&
                   activeEmp == employee.activeEmp && Objects.equals(name, employee.name) &&
                   Objects.equals(gender, employee.gender) && Objects.equals(city, employee.city) &&
                   Objects.equals(deptName, employee.deptName);
        }

    }
    ```

### Sample Employee Data

* _We'll use this list for the examples below._
    ```java
    import java.util.Arrays;
    import java.util.List;
    import java.util.ArrayList; // Needed for some examples later

    List<Employee> employeeList = Arrays.asList(
        new Employee("Alice", 30, "Female", 52000, "New York", "IT", true),
        new Employee("Bob", 25, "Male", 60000, "San Francisco", "Finance", false),
        new Employee("Charlie", 35, "Male", 55000, "New York", "Marketing", true),
        new Employee("David", 40, "Male", 70000, "San Francisco", "IT", true),
        new Employee("Emma", 28, "Female", 75000, "Los Angeles", "HR", false),
        new Employee("Frank", 35, "Male", 58000, "New York", "IT", true), // Duplicate name, same age as Charlie but diff salary
        new Employee("Grace", 28, "Female", 62000, "New York", "IT", true) // Same age as Emma
    );
    ```

### Iterating Over Employees

* _Iterating using an enhanced for-loop (traditional)._
    ```java
    System.out.println("--- Iterating with Enhanced For-Loop ---");
    for (Employee emp : employeeList) {
        System.out.println(emp);
        // Access specific fields: System.out.println(emp.getName());
    }
    ```

* _Iterating using `forEach` with a Lambda Expression._
    ```java
    System.out.println("\n--- Iterating with forEach + Lambda ---");
    employeeList.forEach(emp -> {
        System.out.println("Name: " + emp.getName() + ", Age: " + emp.getAge());
    });
    ```

* _Iterating using `forEach` with a Method Reference (prints the `toString()` representation)._
    ```java
    System.out.println("\n--- Iterating with forEach + Method Reference ---");
    employeeList.forEach(System.out::println);
    // employeeList.stream().forEach(System.out::println);
    ```

* _Iterating using a Stream and `forEach` (equivalent to list's `forEach`)._
    ```java
    System.out.println("\n--- Iterating with Stream + forEach ---");
    employeeList.stream().forEach(emp -> {
        System.out.println("Dept: " + emp.getDeptName() + ", Salary: " + emp.getSalary());
    });
    ```

---

### Collecting & Filtering Employee Data

* _Collect only the names of all employees into a new List._
    ```java
    import java.util.stream.Collectors;

    System.out.println("\n--- Collecting Employee Names ---");
    List<String> employeeNames = employeeList.stream()
                                      .map(Employee::getName) // Method reference for getter
                                   // .map(emp -> emp.getName()) // Equivalent lambda
                        //  .map(emp -> emp.getFirstName()+" "emp.getLastName()) 
                                      .collect(Collectors.toList());
                                   // .filter(x -> x!=null)
                                  //  .filter(x -> x.startWith('a'))
                                 //   .filter(name -> name != null && name.length() > 3)
                                //    .filter(name -> name != null && name.toLowerCase().startsWith("r"))
    System.out.println(employeeNames);


    Set<String> uniqueEmployeesNames = employeeList.stream()
                                                .map(Employee::getName)
                                                .collect(Collectors.toSet());

    Set<String> usingDistinct = employeeList.stream()
                    .map(Employee::getName)
                    .distinct()
                    .collect(Collectors.toSet());
    ```
    > _Output: `[Alice, Bob, Charlie, David, Emma, Frank, Grace]`_

* _Filter employees older than 30._
    ```java
    System.out.println("\n--- Filtering Employees Older Than 30 ---");
    // works good with the primitive type --  emp.getAge(),if it is Integer -- NullPointerException
    List<Employee> olderEmployees = employeeList.stream()
                                         .filter(emp -> emp.getAge() > 30)
                                         .collect(Collectors.toList());      
    List<Employee> olderEmployees = employeeList.stream()
                                         .filter(emp-> emp.getAge()!=null && emp -> emp.getAge() > 30)
                                         .collect(Collectors.toList());                             
    List<Employee> olderEmployees = employeeList.stream()
                                        .filter(emp -> {
                                            return emp.getAge() != null && emp.getAge() > 30;
                                        }).collect(Collectors.toList()); 

    olderEmployees.forEach(System.out::println);
    ```
    > _Output: Includes Charlie, David, Frank_

* _Find unique and duplicate employee names._
    ```java
    import java.util.Set;
    import java.util.HashSet;

    System.out.println("\n--- Finding Unique and Duplicate Names ---");
    Set<String> uniqueNames = new HashSet<>();
    Set<String> duplicateNames = employeeList.stream()
                                            .map(Employee::getName)
                                            .filter(name -> !uniqueNames.add(name)) // .add returns false if element already exists
                                            .collect(Collectors.toSet());

    System.out.println("All Unique Names Encountered: " + uniqueNames);
    System.out.println("Duplicate Names Found: " + duplicateNames);
    ```
    > _Output:_
    > _All Unique Names Encountered: `[Alice, Bob, Charlie, David, Emma, Frank, Grace]` (order may vary)_
    > _Duplicate Names Found: `[]` (In this specific dataset, names are unique)_
    > _Note: If 'Frank' was named 'Charlie', Duplicates would be `[Charlie]`._

    <br/>
* _Store unique employee objs._
    ```java
     // approach - 1
    Set<Long> seen = new HashSet<>();
    Set<Employee> uniqueEmployees = employeeList.stream()
                        .filter(e -> seen.add(e.getId()))  // returns false for duplicates
                        .collect(Collectors.toSet());
                        
        // approach - 2
        Set<Employee> uniqueEmployees =
                employeeList.stream()
                        .collect(Collectors.toMap(
                                Employee::getId,      // key mapper
                                // e -> e.getId()

                                Function.identity(),  // value mapper
                                // Alternative:
                                // e -> e

                                (e1, e2) -> e1        // keep first duplicate
                                /*
                                Alternative merge behaviors:

                                Keep LAST duplicate:
                                (e1, e2) -> e2

                                Custom merge logic:
                                (e1, e2) -> e1.getName().length()
                                               > e2.getName().length()
                                               ? e1 : e2
                                */
                        ))
                        .values()
                        .stream()
                        .collect(Collectors.toSet());

        System.out.println(uniqueEmployees);


        /*
        ===========================================================
        OVERLOADED toMap() VERSION
        ===========================================================

        toMap(keyMapper,
              valueMapper,
              mergeFunction,
              mapSupplier)

        Useful when order matters.
        */

        Set<Employee> orderedUniqueEmployees =
                new LinkedHashSet<>(

                        employeeList.stream()
                                .collect(Collectors.toMap(
                                        Employee::getId,
                                        Function.identity(),
                                        (e1, e2) -> e1,
                                        LinkedHashMap::new
                                        // Alternative:
                                        // HashMap::new
                                        // TreeMap::new
                                ))
                                .values()

                );

        System.out.println(orderedUniqueEmployees);


        /*
        ===========================================================
        ALTERNATIVE APPROACH (If equals/hashCode implemented)
        ===========================================================

        Requires:
        equals() and hashCode() based on id.
        */

        Set<Employee> usingDistinct =
                employeeList.stream()
                        .distinct()
                        .collect(Collectors.toSet());

        /*
        Works only if:

        @Override
        public boolean equals(Object o) {
            return this.id == ((Employee)o).id;
        }

        @Override
        public int hashCode() {
            return Objects.hash(id);
        }
        */


        /*
        ===========================================================
        ANOTHER ALTERNATIVE — groupingBy()
        ===========================================================

        Groups by id → take first employee.
        */

        Set<Employee> usingGrouping =
                employeeList.stream()
                        .collect(Collectors.groupingBy(
                                Employee::getId
                        ))
                        .values()
                        .stream()
                        .map(list -> list.get(0))
                        // Alternative:
                        // list -> list.get(list.size()-1)  // keep last
                        .collect(Collectors.toSet());



        /*
        ===========================================================
        SHORT NOTES
        ===========================================================

        Function.identity()
            → same as (e -> e)

        (e1, e2) -> e1
            → keep first duplicate

        (e1, e2) -> e2
            → keep last duplicate

        LinkedHashMap::new
            → preserves insertion order

        distinct()
            → works only with equals/hashCode

        ===========================================================
        */

    ```

* _Store unique employee objs on the basis of -(key,value)._
    ```java
     // ✅ 1. Store first employee per department
        // If a department repeats, the first employee is retained, others are ignored
        Map<String, Employee> firstEmployeeByDept = employeeList.stream()
            .collect(Collectors.toMap(
                Employee::getDeptName,             // Key: department name
                e -> e,                            // Value: Employee object
                (existing, replacement) -> existing // Merge function: keep first (ignore replacement)
            ));

        System.out.println("=== First Employee per Department ===");
        firstEmployeeByDept.forEach((dept, emp) ->
            System.out.println("Dept: " + dept + ", Employee: " + emp)
        );

        // ✅ 2. Store latest employee per department
        // If a department repeats, the latest employee replaces the previous one
        Map<String, Employee> latestEmployeeByDept = employeeList.stream()
            .collect(Collectors.toMap(
                Employee::getDeptName,                // Key: department name
                e -> e,                               // Value: Employee object
                (existing, replacement) -> replacement // Merge function: keep latest (override existing)
            ));

        System.out.println("\n=== Latest Employee per Department ===");
        latestEmployeeByDept.forEach((dept, emp) ->
            System.out.println("Dept: " + dept + ", Employee: " + emp)
        );
    });
    ```

### 🧑‍🤝‍🧑 Grouping Employees

* _Group employees by their Department._
    ```java
    import java.util.Map;

    System.out.println("\n--- Grouping Employees by Department ---");
    Map<String, List<Employee>> employeesByDept = employeeList.stream()
        .collect(Collectors.groupingBy(Employee::getDeptName));

    employeesByDept.forEach((dept, emps) -> {
        System.out.println("Department: " + dept);
        emps.forEach(emp -> System.out.println("  " + emp.getName()));
    });
    ```

* _Group employees by Active Status (Active vs. Inactive)._
    ```java
    System.out.println("\n--- Grouping Employees by Active Status ---");
    Map<Boolean, List<Employee>> employeesByActiveStatus = employeeList.stream()
        .collect(Collectors.groupingBy(Employee::isActiveEmp));

    employeesByActiveStatus.forEach((isActive, emps) -> {
        System.out.println("Active: " + isActive);
        emps.forEach(emp -> System.out.println("  " + emp.getName()));
    });
    ```


* _Count employees in each Department._
    ```java
    System.out.println("\n--- Counting Employees per Department ---");
    Map<String, Long> countByDept = employeeList.stream()
        .collect(Collectors.groupingBy(Employee::getDeptName, // Group by department
                                       Collectors.counting())); // Count elements in each group
    countByDept.forEach((dept, count) -> System.out.println(dept + ": " + count));
    ```
    > _Output:_
    > _HR: 1_
    > _Finance: 1_
    > _Marketing: 1_
    > _IT: 4_
    

* _Total salaries grouped by department._
    ```java
        // 1. Total salary of all employees
        double totalSalary = empList.stream()
            .map(Employee::getSalary)
            .reduce(0.0, Double::sum);  //  .reduce(0.0, (a, b) -> a + b);
        System.out.println("Total Salary of All Employees: " + totalSalary);

        double totalSalary = empList.stream()
                                .mapToDouble(Employee::getSalary)
                                .sum();

        // 2. Total salaries grouped by department
        Map<String, Double> totalSalariesByDept = empList.stream()
            .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.reducing(0.0, Employee::getSalary, Double::sum)
            ));

        System.out.println("Total Salaries by Department:");
        totalSalariesByDept.forEach((dept, total) ->
            System.out.println(dept + " → " + total)
        );
    ```
    ```
    Total Salary of All Employees: 237000.0
    Total Salaries by Department:
    HR → 105000.0
    IT → 122000.0
    Finance → 70000.0
    ```
---

### 📊 Sorting Employees


* _Sort employees by Name in ascending order._
    ```java
     import java.util.*;
     import java.util.stream.Collectors;
     
     /*
      * ============================================================
      *                EMPLOYEE SORTING - COMPLETE NOTES
      * ============================================================
      *
      * Key Concepts:
      *
      * 1. Comparable (compareTo)
      *    - Defines NATURAL ORDERING
      *    - Required for:
      *          Collections.sort(list)
      *          stream().sorted()
      *
      * 2. Comparator
      *    - Defines CUSTOM ORDERING
      *    - NOT required to implement Comparable
      *
      * ------------------------------------------------------------
      * RULE:
      * Comparable  -> Default sorting (inside class)
      * Comparator  -> Custom sorting (outside class)
      * ------------------------------------------------------------
      */
     
     class Employee implements Comparable<Employee> {
     
         private String name;
         private double salary;
     
         // Constructor
         // Getters
     
         /*
          * NATURAL ORDERING
          *
          * Constraint:
          * - REQUIRED for 
          *    - Collections.sort() 
          *    - stream().sorted()
          *
          * Here: Sorting by name (ascending)
          */
         @Override
         public int compareTo(Employee other) {
             return this.name.compareTo(other.name);
         }
     
         @Override
         public String toString() {
             return name + " - " + salary;
         }
     }
     
     public class EmployeeSortingNotes {
     
         public static void main(String[] args) {
         
             List<Employee> employees = Arrays.asList(
                     new Employee("Anil", 50000),
                     new Employee("Ravi", 70000),
                     new Employee("Kiran", 60000),
                     new Employee("Anil", 55000)
             );
     
             /*
              * ============================================================
              * Approach 1: Collections.sort()
              * ============================================================
              *
              * Uses:
              * - compareTo() method (natural ordering)
              *
              * Constraint:
              * - Employee MUST implement Comparable
              */
             Collections.sort(employees);
             // Collections.sort(employees, Collections.reverseOrder());
             System.out.println("Sorted using Collections.sort(): " + employees);
     
     
             /*
              * ============================================================
              * Approach 2: Stream sorted() (Natural Ordering)
              * ============================================================
              *
              * Uses:
              * - compareTo()
              *
              * Constraint:
              * - Employee MUST implement Comparable
              */
             List<Employee> sortedNatural = employees.stream()
                     .sorted()
                     .collect(Collectors.toList());
     
             System.out.println("Sorted using stream().sorted(): " + sortedNatural);
     
     
             /*
              * ============================================================
              * Approach 3: Anonymous Comparator (Salary ASC)
              * ============================================================
              *
              * Uses:
              * - Custom Comparator
              *
              * Constraint:
              * - NO need for Comparable
              */
     
                     // String (name ASC)
                     // return e1.getName().compareTo(e2.getName());
     
                     // String (name DESC)
                     // return e2.getName().compareTo(e1.getName());
             List<Employee> sortedBySalaryAnon = employees.stream()
                     .sorted(new Comparator<Employee>() {
                         @Override
                         public int compare(Employee e1, Employee e2) {
                             return Double.compare(e1.getSalary(), e2.getSalary());
                             // return Long.compare(e1.getId(), e2.getId());
                         }
                     })
                     .collect(Collectors.toList());
     
             System.out.println("Sorted by salary (Anonymous): " + sortedBySalaryAnon);
     
     
     
             /*
              * ============================================================
              * Approach 4: Lambda Expression (Name DESC)
              * ============================================================
              *
              * Constraint:
              * - NO need for Comparable
              */
             List<Employee> sortedNameDesc = employees.stream()
                     .sorted((e1, e2) -> e2.getName().compareTo(e1.getName()))
                     .collect(Collectors.toList());
     
             System.out.println("Sorted by name DESC: " + sortedNameDesc);
     
     
             /*
              * ============================================================
              * Approach 5: Lambda + Double.compare (BEST PRACTICE)
              * ============================================================
              *
              * Why Double.compare?
              * - Avoids floating-point precision issues
              * - Avoids overflow
              *
              * Returns:
              *  -1, 0, 1 (safe comparison)
              */
             List<Employee> sortedBySalary = employees.stream()
                     .sorted((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()))
                     .collect(Collectors.toList());
     
             System.out.println("Sorted by salary (Lambda): " + sortedBySalary);
     
     
             /*
              * ============================================================
              * Approach 6: Comparator.comparing() (MODERN & CLEAN)
              * ============================================================
              *
              * Recommended in interviews
              *
              * Constraint:
              * - NO need for Comparable
              */
             List<Employee> sortedModern = employees.stream()
                     .sorted(Comparator.comparing(Employee::getSalary))
                     .collect(Collectors.toList());
     
             System.out.println("Sorted using Comparator.comparing(): " + sortedModern);
     
     
             /*
              * DESCENDING ORDER
              */
             List<Employee> sortedDesc = employees.stream()
                     .sorted(Comparator.comparing(Employee::getSalary).reversed())
                     .collect(Collectors.toList());
     
             System.out.println("Sorted salary DESC: " + sortedDesc);
     
     
             /*
              * ============================================================
              * BONUS: MULTI-LEVEL SORTING
              * ============================================================
              *
              * First by salary, then by name
              */
             List<Employee> multiSort = employees.stream()
                     .sorted(Comparator.comparing(Employee::getSalary)
                             .thenComparing(Employee::getName))
                     .collect(Collectors.toList());

            /*
            - comparingDouble() → works with primitive double
            - Avoids boxing overhead
            - Slightly more performant
            */

            List<Employee> multiSort = employees.stream()
                    .sorted(Comparator.comparingDouble(Employee::getSalary)
                            .thenComparing(Employee::getName))
                    .collect(Collectors.toList());
     
             System.out.println("Multi-level sorting: " + multiSort);

            List<Employee> sortedEmployees = employees.stream()
                    .sorted(Comparator.comparing(Employee::getCity).reversed() // City descending
                            .thenComparing(Employee::getName).reversed()) // Then name descending
                    .collect(Collectors.toList());
         }
     }
     
     /*
      * ============================================================
      * FINAL SUMMARY
      * ============================================================
      *
      * Comparable:
      *   - Inside class
      *   - Default sorting
      *   - Used by Collections.sort() & sorted()
      *
      * Comparator:
      *   - Outside class
      *   - Custom sorting
      *   - More flexible
      *
      * BEST PRACTICE:
      *   Use Comparator.comparing()
      *
      * INTERVIEW LINE:
      *   "Comparable defines natural ordering,
      *    Comparator defines custom ordering."
      *
      * ============================================================
      */
    ```

    ```
    Comparator.comparing(Employee::getName).reversed()
    Comparator.comparingInt(Employee::getId).reversed()
    Comparator.comparingDouble(Employee::getSalary).reversed()
    Comparator.comparingLong(Employee::getMobile).reversed()
    Comparator.comparing(Employee::getJoiningDate).reversed()           -  LocalDate
    ```
<!-- * _Sort employees by Name in descending order._
    ```java
        // Approach 1: Using Collections.sort() with reverse order
        Collections.sort(employees, Collections.reverseOrder());

        // Approach 2: Using Collections.reverseOrder() with method reference
        List<Employee> sorted1 = employees.stream()
                .sorted(Comparator.comparing(Employee::getName).reversed())
                .collect(Collectors.toList());
        
        // Approach 3: Using anonymous comparator for descending order
        List<Employee> sorted2 = employees.stream()
                .sorted(new Comparator<Employee>() {
                    @Override
                    public int compare(Employee e1, Employee e2) {
                        return e2.getName().compareTo(e1.getName()); // Reverse order
                    }
                })
                .collect(Collectors.toList());

        // Approach 4: Using lambda expression
        List<Employee> sorted3 = employees.stream()
                .sorted((e1, e2) -> e2.getName().compareTo(e1.getName()))
                .collect(Collectors.toList());
    ```

* _Multi-level sorting: Sort by City Descending, then by Name Ascending.._
    ```java
        List<Employee> sortedEmployees = employees.stream()
                .sorted(Comparator.comparing(Employee::getCity).reversed() // City descending
                        .thenComparing(Employee::getName)) // Then name ascending
                .collect(Collectors.toList());

    ```

* _Multi-level sorting: Sort by City Descending, then by Name Descending.._
    ```java
        List<Employee> sortedEmployees = employees.stream()
                .sorted(Comparator.comparing(Employee::getCity).reversed() // City descending
                        .thenComparing(Employee::getName).reversed()) // Then name descending
                .collect(Collectors.toList());

    ``` -->

* _Traditional approach for implementing the comparator interface method._
    ```java
    class SalaryComparator implements Comparator<Employee> {
        @Override
        public int compare(Employee e1, Employee e2) {
            return Double.compare(e1.getSalary(), e2.getSalary());
        }
    }

    Collections.sort(employees, new SalaryComparator());  // Sorts in ascending order of salary

    ```
    
* _Select the top 3 Employees with the highest Salaries._
    ```java
    System.out.println("\n--- Top 3 Highest Paid Employees ---");
    List<Employee> top3Salaries = employeeList.stream()
                .sorted(Comparator.comparingDouble(Employee::getSalary).reversed())
                .limit(3)
                .collect(Collectors.toList());
    top3Salaries.forEach(System.out::println);
    ```

* _Select Employees starting from the 4th highest Salary onwards._
    ```java
    System.out.println("\n--- Employees After Top 3 Paid ---");
    List<Employee> afterTop3Salaries = employeeList.stream()
                .sorted(Comparator.comparingDouble(Employee::getSalary).reversed())
                .skip(3) // Skip the first 3
                .collect(Collectors.toList());
    afterTop3Salaries.forEach(System.out::println);
    ```

* _Select the 2nd & 3rd youngest Employees._
    ```java
    System.out.println("\n--- 2nd and 3rd Youngest Employees ---");
    List<Employee> secondAndThirdYoungest = employeeList.stream()
                .sorted(Comparator.comparingInt(Employee::getAge))
                .skip(1) // Skip the youngest
                .limit(2) // Take the next two
                .collect(Collectors.toList());
    secondAndThirdYoungest.forEach(System.out::println);
    ```
    > _Output: Includes Emma (28) and Grace (28)_

---

### 📈 Employee Statistics

* _Find the Employee with the Maximum Salary._
    ```java
    /*
     * ============================================================
     *        EMPLOYEE STATISTICS (MAX / MIN SALARY) - NOTES
     * ============================================================
     *
     * GOAL:
     *  - Find Employee with MAX salary
     *  - Find Employee with MIN salary
     *  - Find only salary (Double)
     *
     * IMPORTANT:
     *  - max() / min() → Direct stream methods (BEST)
     *  - Collectors.maxBy() / minBy() → Alternative (less preferred)
     *
     * RETURN TYPE:
     *  - Always Optional<T> (to avoid null issues)
     * ============================================================
     */
    
            /*
             * ============================================================
             *  1. MAX SALARY EMPLOYEE (BEST APPROACH)
             * ============================================================
             *
             * - Uses stream().max()
             * - Efficient & readable
             */
            Optional<Employee> highestPaid = employeeList.stream()
                    .max(Comparator.comparingDouble(Employee::getSalary));
    
            highestPaid.ifPresent(System.out::println);
            // Output: Employee{id=2, name='Jane', salary=85000.0}
    
    
            /*
             * ============================================================
             *  2. MAX SALARY EMPLOYEE (Collectors - ALTERNATIVE)
             * ============================================================
             *
             * - Same result, but slightly verbose
             */
            Optional<Employee> highestPaidAlt = employeeList.stream()
                    .collect(Collectors.maxBy(
                            Comparator.comparingDouble(Employee::getSalary)));
    
            highestPaidAlt.ifPresent(System.out::println);
    
    
            /*
             * ============================================================
             *  3. MAX SALARY VALUE ONLY (Double)
             * ============================================================
             *
             * - First map → extract salary
             * - Then apply max()
             */
            Optional<Double> maxSalary = employeeList.stream()
                    .map(Employee::getSalary)
                    .max(Double::compare);
    
            maxSalary.ifPresent(System.out::println);
            // Output: 85000.0
    
    
            /*
             * ============================================================
             *  4. MIN SALARY EMPLOYEE
             * ============================================================
             *
             * - Uses stream().min()
             */
            Optional<Employee> lowestPaid = employeeList.stream()
                    .min(Comparator.comparingDouble(Employee::getSalary));
    
            lowestPaid.ifPresent(System.out::println);
            // Output: Employee with lowest salary
    
    
            /*
             * ============================================================
             *  5. MIN SALARY (Collectors - ALTERNATIVE)
             * ============================================================
             */
            Optional<Employee> lowestPaidAlt = employeeList.stream()
                    .collect(Collectors.minBy(
                            Comparator.comparingDouble(Employee::getSalary)));
    
            lowestPaidAlt.ifPresent(System.out::println);
    
    
            /*
             * ============================================================
             *  IMPORTANT NOTES
             * ============================================================
             *
             * 1. Optional is used because:
             *    - Stream may be empty
             *
             * 2. Prefer:
             *      max() / min()
             *    over:
             *      Collectors.maxBy() / minBy()
             *
             * 3. comparingDouble():
             *    - Avoids boxing (double → Double)
             *    - Better performance
             *
             * 4. To get value directly (unsafe if empty):
             */
            // Employee emp = highestPaid.get(); // ⚠️ may throw exception
    
    
            /*
             * ============================================================
             *  BONUS VARIATIONS
             * ============================================================
             */
    
            // Get only employee name with max salary
            Optional<String> name = employeeList.stream()
                    .max(Comparator.comparingDouble(Employee::getSalary))
                    .map(e -> e.name);
    
            name.ifPresent(System.out::println);
    
    
            // Default value if empty
            Employee defaultEmp = employeeList.stream()
                    .max(Comparator.comparingDouble(Employee::getSalary))
                    .orElse(new Employee(0, "Default", 0));
    
            System.out.println(defaultEmp);
        }
    }
    
    /*
     * ============================================================
     * FINAL QUICK SUMMARY
     * ============================================================
     *
     * Find MAX object:
     *   stream().max(Comparator)
     *
     * Find MIN object:
     *   stream().min(Comparator)
     *
     * Extract field:
     *   map() → then max()/min()
     *
     * BEST PRACTICE:
     *   comparingDouble() for double
     *
     * ============================================================
     */
    ```

* _Calculate Summary Statistics for Employee Ages (Count, Sum, Min, Average, Max)._
    ```java
    import java.util.IntSummaryStatistics;

    System.out.println("\n--- Summary Statistics for Employee Ages ---");
    IntSummaryStatistics ageStats = employeeList.stream()
                        .mapToInt(Employee::getAge) // Convert to IntStream
                    //  .mapToDouble(Employee::getSalary) // Convert to DoubleStream - for the salaryStats
                        .summaryStatistics(); // Calculate stats

    System.out.println("Min: " + ageStats.getMin());
    System.out.println("Max: " + ageStats.getMax());
    System.out.println("Sum: " + ageStats.getSum());
    System.out.println("Average: " + ageStats.getAverage());
    System.out.println("Count: " + ageStats.getCount());
    ```
    > _Output:_
    > _Min: 25_
    > _Max: 40_
    > _Sum: 221_
    > _Average: 31.57..._
    > _Count: 7_

* _Find the Highest Salary in each Department._
    ```java
    System.out.println("\n--- Highest Salary per Department ---");
    
    Map<String, Optional<Employee>> highestSalaryByDept =
        employeeList.stream()
            .collect(Collectors.groupingBy(
                Employee::getDeptName,
                Collectors.maxBy(
                    Comparator.comparingDouble(Employee::getSalary)
                )
            ));
    
    highestSalaryByDept.forEach((dept, empOpt) -> {
        empOpt.ifPresent(emp ->
            System.out.println(
                "Dept: " + dept +
                ", Highest Paid: " +
                emp.getName() +
                " (" + emp.getSalary() + ")"
            )
        );
    });
    ```

---

### ⚠️ Stream Re-use Caveat

* _Streams cannot be reused after a terminal operation is called. Attempting to do so results in an `IllegalStateException`._
    ```java
    Stream<String> namesStream = employeeList.stream().map(Employee::getName);
    System.out.println("\n--- Stream Re-use Attempt ---");
    System.out.println("First consumption (forEach):");
    namesStream.forEach(System.out::println); // Consumes the stream

    try {
        // Attempting to reuse the *same* stream instance
        long count = namesStream.count(); // This will fail
        System.out.println("Count (should not be reached): " + count);
    } catch (IllegalStateException e) {
        System.out.println("Error trying to reuse stream: " + e.getMessage());
    }
    ```
    > _Output: Will print names, then throw `IllegalStateException: stream has already been operated upon or closed`_

* _To perform multiple operations, either recreate the stream or use a `Supplier`._
    ```java
    import java.util.function.Supplier;

    // Option 1: Recreate the stream
    System.out.println("\n--- Recreating Stream ---");
    employeeList.stream().map(Employee::getName).forEach(System.out::println);
    long countRecreated = employeeList.stream().map(Employee::getName).count();
    System.out.println("Count (recreated): " + countRecreated);

    // Option 2: Use a Supplier
    System.out.println("\n--- Using Supplier for Stream ---");
    Supplier<Stream<Employee>> employeeStreamSupplier = () -> employeeList.stream();

    System.out.println("First consumption via supplier:");
    employeeStreamSupplier.get().map(Employee::getName).forEach(System.out::println); // Gets a new stream

    System.out.println("Second consumption via supplier:");
    long countSupplier = employeeStreamSupplier.get().count(); // Gets another new stream
    System.out.println("Count (supplier): " + countSupplier);
    ```

---

## ✅ Conclusion

> Java Streams offer a flexible and expressive paradigm for data processing. Mastering the distinction between **lazy intermediate operations** and **eager terminal operations**, along with understanding how to chain them effectively, unlocks the power of the Stream API for writing clean, declarative, and efficient Java code. 

> Common problems, especially when working with collections of objects like `Employee`, can often be solved elegantly using combinations of stream operations like `filter`, `map`, `reduce`, `collect` (especially `groupingBy`), `sorted`, `distinct`, and summary statistics methods. Remember the single-consumption nature of streams and plan accordingly for multiple operations.
