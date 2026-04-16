# Java Interview Questions Repository

This document contains a curated list of Java interview questions covering various topics from basic concepts to advanced features.

## Table of Contents

| #   | Topic | Question |
| --- | ----- | -------- |
|     | **Java Basics** | |
| 1   | Java Basics | [Why is Java not 100% object-oriented?](#why-is-java-not-100-object-oriented) |
| 2   | Java Basics | [What are the features of Java?](#what-are-the-features-of-java) |
| 3   | Java Basics | [What do you mean by platform independence?](#what-do-you-mean-by-platform-independence) |
| 4   | Java Basics | [What is the difference between JDK, JRE, and JVM?](#what-is-the-difference-between-jdk-jre-and-jvm) |
| 5   | Java Basics | [What are the jobs of JVM?](#what-are-the-jobs-of-jvm) |
| 6   | Java Basics | [What is the difference between a compiler, an interpreter, and bytecode?](#what-is-the-difference-between-a-compiler-an-interpreter-and-bytecode) |
| 7   | Java Basics | [What is a package in Java?](#what-is-a-package-in-java) |
| 8   | Java Basics | [Can I import the same package/class twice? Will the JVM load the package twice at runtime?](#can-i-import-the-same-packageclass-twice-will-the-jvm-load-the-package-twice-at-runtime) |
| 9   | Java Basics | [What are the different data types in Java?](#what-are-the-different-data-types-in-java) |
| 10  | Java Basics | [What is the difference between declaring, defining, and initializing a value?](#what-is-the-difference-between-declaring-defining-and-initializing-a-value) |
| 11  | Java Basics | [How many different ways are there to take input in Java?](#how-many-different-ways-are-there-to-take-input-in-java) |
| 12  | Java Basics | [Using String... args vs. String[] args in the main method.](#using-string-args-vs-string-args-in-the-main-method) |
|     | **Unicode and Identifiers** | |
| 13  | Unicode & Identifiers | [What is Unicode, and which Unicode system does Java use?](#what-is-unicode-and-which-unicode-system-does-java-use) |
| 14  | Unicode & Identifiers | [Which non-Unicode characters can be used as the first character of an identifier?](#which-non-unicode-characters-can-be-used-as-the-first-character-of-an-identifier) |
| 15  | Unicode & Identifiers | [Does 'main' a keyword in Java? What are other identifiers?](#does-main-a-keyword-in-java-what-are-other-identifiers) |
|     | **Primitive Types and Wrapper Classes** | |
| 16  | Primitives & Wrappers | [What are primitive types and their wrapper classes in Java?](#what-are-primitive-types-and-their-wrapper-classes-in-java) |
| 17  | Primitives & Wrappers | [What methods are inside primitive and wrapper classes?](#what-methods-are-inside-primitive-and-wrapper-classes) |
| 18  | Primitives & Wrappers | [What is autoboxing and unboxing?](#what-is-autoboxing-and-unboxing) |
|     | **Casting and Cloning** | |
| 19  | Casting & Cloning | [What is casting (implicit and explicit)?](#what-is-casting-implicit-and-explicit) |
| 20  | Casting & Cloning | [Implicit casting / upcasting vs Explicit casting / downcasting.](#implicit-casting--upcasting-vs-explicit-casting--downcasting) |
| 21  | Casting & Cloning | [Different types of cloning available in Java? Difference between deep cloning and shallow cloning?](#different-types-of-cloning-available-in-java-difference-between-deep-cloning-and-shallow-cloning) |
|     | **Strings** | |
| 22  | Strings | [What are string literals?](#what-are-string-literals) |
| 23  | Strings | [Why is String not mutable, and how to develop a mutable String in Java?](#why-is-string-not-mutable-and-how-to-develop-a-mutable-string-in-java) |
| 24  | Strings | [What is the difference between String, StringBuilder, and StringBuffer?](#what-is-the-difference-between-string-stringbuilder-and-stringbuffer) |
| 25  | Strings | [What is the String constant pool?](#what-is-the-string-constant-pool) |
| 26  | Strings | [String s_1 = "anil"; vs. String s_2 = new String("anil");](#string-s_1--anil-vs-string-s_2--new-stringanil) |
| 27  | Strings | [How do you convert a String to an int (parse)?](#how-do-you-convert-a-string-to-an-int-parse) |
|     | **Operators and Comparisons** | |
| 28  | Operators & Comparisons | [What is the difference between == and .equals?](#what-is-the-difference-between--and-equals) |
|     | **Static and Singleton** | |
| 29  | Static & Singleton | [What is static in Java?](#what-is-static-in-java) |
| 30  | Static & Singleton | [Why is the main method static in Java?](#why-is-the-main-method-static-in-java) |
| 31  | Static & Singleton | [Can we run main() without the static keyword?](#can-we-run-main-without-the-static-keyword) |
| 32  | Static & Singleton | [Can we run a Java class without a main method?](#can-we-run-a-java-class-without-a-main-method) |
| 33  | Static & Singleton | [Can you declare the main method as final or private?](#can-you-declare-the-main-method-as-final-or-private) |
| 34  | Static & Singleton | [What if we write static public void instead of public static void?](#what-if-we-write-static-public-void-instead-of-public-static-void) |
| 35  | Static & Singleton | [Can a static reference a non-static variable? What happens if it does?](#can-a-static-reference-a-non-static-variable-what-happens-if-it-does) |
| 36  | Static & Singleton | [Can we override static variables?](#can-we-override-static-variables) |
| 37  | Static & Singleton | [Static binding vs Dynamic binding.](#static-binding-vs-dynamic-binding) |
| 38  | Static & Singleton | [Static loading and initialization phase.](#static-loading-and-initialization-phase) |
| 39  | Static & Singleton | [Non-static loading and initialization phase.](#non-static-loading-and-initialization-phase) |
| 40  | Static & Singleton | [What is a singleton class in Java?](#what-is-a-singleton-class-in-java) |
|     | **Final Keyword** | |
| 41  | Final Keyword | [What is the final keyword, and where can we use it?](#what-is-the-final-keyword-and-where-can-we-use-it) |
| 42  | Final Keyword | [Can you declare a class as final?](#can-you-declare-a-class-as-final) |
| 43  | Final Keyword | [What is the difference between the final method and an abstract method?](#what-is-the-difference-between-the-final-method-and-an-abstract-method) |
| 44  | Final Keyword | [Can we declare an interface as final?](#can-we-declare-an-interface-as-final) |
|     | **Constructors** | |
| 45  | Constructors | [What is a constructor? Give an example.](#what-is-a-constructor-give-an-example) |
| 46  | Constructors | [What is the main purpose of a constructor?](#what-is-the-main-purpose-of-a-constructor) |
| 47  | Constructors | [When is a constructor invoked?](#when-is-a-constructor-invoked) |
| 48  | Constructors | [What is a default constructor?](#what-is-a-default-constructor) |
| 49  | Constructors | [Can a constructor return any value?](#can-a-constructor-return-any-value) |
| 50  | Constructors | [What is constructor overloading? (multiple constructors)](#what-is-constructor-overloading-multiple-constructors) |
| 51  | Constructors | [What is the difference between a constructor and a method?](#what-is-the-difference-between-a-constructor-and-a-method) |
| 52  | Constructors | [What is constructor chaining?](#what-is-constructor-chaining) |
| 53  | Constructors | [What is a copy constructor?](#what-is-a-copy-constructor) |
| 54  | Constructors | [What is a destructor?](#what-is-a-destructor) |
| 55  | Constructors | [Can we declare a constructor as private or final?](#can-we-declare-a-constructor-as-private-or-final) |
| 56  | Constructors | [What are the this and super keywords? How are they used in constructors?](#what-are-the-this-and-super-keywords-how-are-they-used-in-constructors) |
| 57  | Constructors | [Can we call a subclass constructor from a superclass constructor?](#can-we-call-a-subclass-constructor-from-a-superclass-constructor) |
| 58  | Constructors | [Is it possible to inherit a constructor?](#is-it-possible-to-inherit-a-constructor) |
| 59  | Constructors | [Is it possible to invoke a constructor of a class more than once for an object?](#is-it-possible-to-invoke-a-constructor-of-a-class-more-than-once-for-an-object) |
| 60  | Constructors | [Invoking a constructor.](#invoking-a-constructor) |
|     | **Access Modifiers** | |
| 61  | Access Modifiers | [What are visibility modifiers and access modifiers? What are the scopes associated with them?](#what-are-visibility-modifiers-and-access-modifiers-what-are-the-scopes-associated-with-them) |
| 62  | Access Modifiers | [What is the access modifier scope order (lower to higher)? What is default? Diff bw protected & default?](#what-is-the-access-modifier-scope-order-lower-to-higher-what-is-default-diff-bw-protected--default) |
| 63  | Access Modifiers | [Can a constructor be declared as private? Why is it needed?](#can-a-constructor-be-declared-as-private-why-is-it-needed) |
| 64  | Access Modifiers | [Can a protected method be accessed outside the package? If yes, how?](#can-a-protected-method-be-accessed-outside-the-package-if-yes-how) |
| 65  | Access Modifiers | [Can a class be declared as private or protected? Why or why not?](#can-a-class-be-declared-as-private-or-protected-why-or-why-not) |
| 66  | Access Modifiers | [How can we restrict subclassing in Java?](#how-can-we-restrict-subclassing-in-java) |
| 67  | Access Modifiers | [Can an abstract method be private? Why or why not?](#can-an-abstract-method-be-private-why-or-why-not) |
|     | **Overloading and Overriding** | |
| 68  | Overloading & Overriding | [What is the difference between method overloading and overriding?](#what-is-the-difference-between-method-overloading-and-overriding) |
| 69  | Overloading & Overriding | [What are the rules associated with overloading and overriding?](#what-are-the-rules-associated-with-overloading-and-overriding) |
| 70  | Overloading & Overriding | [Can we change the scope of the overridden method in the subclass?](#can-we-change-the-scope-of-the-overridden-method-in-the-subclass) |
| 71  | Overloading & Overriding | [What is the covariant return type?](#what-is-the-covariant-return-type) |
| 72  | Overloading & Overriding | [Can we override private methods? \| static methods](#can-we-override-private-methods--static-methods) |
|     | **OOP Concepts** | |
| 73  | OOP | [What is encapsulation, and how is it implemented? What are its benefits?](#what-is-encapsulation-and-how-is-it-implemented-what-are-its-benefits) |
| 74  | OOP | [What is polymorphism? (types)](#what-is-polymorphism-types) |
| 75  | OOP | [Steps to create an immutable class.](#steps-to-create-an-immutable-class) |
|     | **Abstract Classes** | |
| 76  | Abstract Classes | [Can an abstract class have a constructor?](#can-an-abstract-class-have-a-constructor) |
| 77  | Abstract Classes | [How do you create an abstract class?](#how-do-you-create-an-abstract-class) |
| 78  | Abstract Classes | [Is at least one abstract method mandatory in an abstract class? (Concrete methods)](#is-at-least-one-abstract-method-mandatory-in-an-abstract-class-concrete-methods) |
| 79  | Abstract Classes | [Can we create an object of an abstract class using the new keyword?](#can-we-create-an-object-of-an-abstract-class-using-the-new-keyword) |
| 80  | Abstract Classes | [Can final methods be present in an abstract class?](#can-final-methods-be-present-in-an-abstract-class) |
| 81  | Abstract Classes | [Can we define an abstract class as final?](#can-we-define-an-abstract-class-as-final) |
| 82  | Abstract Classes | [Can an abstract class have a main method in Java?](#can-an-abstract-class-have-a-main-method-in-java) |
|     | **Inheritance and Interfaces** | |
| 83  | Inheritance & Interfaces | [What is inheritance, and what are the types of inheritance?](#what-is-inheritance-and-what-are-the-types-of-inheritance) |
| 84  | Inheritance & Interfaces | [What class do all classes inherit from in Java?](#what-class-do-all-classes-inherit-from-in-java) |
| 85  | Inheritance & Interfaces | [Why is multiple inheritance not possible in Java? (ambiguity problem)](#why-is-multiple-inheritance-not-possible-in-java-ambiguity-problem) |
| 86  | Inheritance & Interfaces | [What is the use of interfaces?](#what-is-the-use-of-interfaces) |
| 87  | Inheritance & Interfaces | [How are abstraction and loose coupling achieved?](#how-are-abstraction-and-loose-coupling-achieved) |
| 88  | Inheritance & Interfaces | [Can an interface implement another interface in Java?](#can-an-interface-implement-another-interface-in-java) |
| 89  | Inheritance & Interfaces | [What is the diamond problem in Java? How does Java's approach with interfaces solve it?](#what-is-the-diamond-problem-in-java-how-does-javas-approach-with-interfaces-solve-it) |
| 90  | Inheritance & Interfaces | [What are marker interfaces? Can you provide examples?](#what-are-marker-interfaces-can-you-provide-examples) |
| 91  | Inheritance & Interfaces | [Can interfaces have static initializer blocks or constructors?](#can-interfaces-have-static-initializer-blocks-or-constructors) |
| 92  | Inheritance & Interfaces | [Can an interface have a static variable?](#can-an-interface-have-a-static-variable) |
| 93  | Inheritance & Interfaces | [Can an interface have static nested interfaces?](#can-an-interface-have-static-nested-interfaces) |
| 94  | Inheritance & Interfaces | [What is the difference between Comparable and Comparator interfaces in Java?](#what-is-the-difference-between-comparable-and-comparator-interfaces-in-java) |
| 95  | Inheritance & Interfaces | [How can you achieve callback functionality using interfaces in Java?](#how-can-you-achieve-callback-functionality-using-interfaces-in-java) |
| 96  | Inheritance & Interfaces | [What is a functional interface?](#what-is-a-functional-interface) |
| 97  | Inheritance & Interfaces | [What are default and static methods? What is their use?](#what-are-default-and-static-methods-what-is-their-use) |
| 98  | Inheritance & Interfaces | [Can a default method in an interface be overridden in an implementing class?](#can-a-default-method-in-an-interface-be-overridden-in-an-implementing-class) |
| 99  | Inheritance & Interfaces | [Can an interface have fields?](#can-an-interface-have-fields) |
|     | **Enum** | |
| 100 | Enum | [What is an enum? How do you declare it in Java? What are the advantages and limitations?](#what-is-an-enum-how-do-you-declare-it-in-java-what-are-the-advantages-and-limitations) |
| 101 | Enum | [Can enums have methods in Java? \| constructor](#can-enums-have-methods-in-java--constructor) |
| 102 | Enum | [Can we create an enum object using new?](#can-we-create-an-enum-object-using-new) |
| 103 | Enum | [How do you access enum constants and methods in Java?](#how-do-you-access-enum-constants-and-methods-in-java) |
| 104 | Enum | [Can enums implement interfaces in Java?](#can-enums-implement-interfaces-in-java) |
| 105 | Enum | [Can enums have abstract methods in Java?](#can-enums-have-abstract-methods-in-java) |
| 106 | Enum | [Can enums override methods from the java.lang.Enum class in Java?](#can-enums-override-methods-from-the-javalangenum-class-in-java) |
| 107 | Enum | [How can you serialize and deserialize enums in Java?](#how-can-you-serialize-and-deserialize-enums-in-java) |
|     | **Collections and Data Structures** | |
| 108 | Collections | [Given an Employee class: Store in a map ensuring no duplicates.](#given-an-employee-class-store-in-a-map-ensuring-no-duplicates) |
|     | **Memory Management and Garbage Collection** | |
| 109 | Memory & GC | [What are the memory areas available in Java?](#what-are-the-memory-areas-available-in-java) |
| 110 | Memory & GC | [What is garbage collection in Java? What methods are related to GC?](#what-is-garbage-collection-in-java-what-methods-are-related-to-gc) |
| 111 | Memory & GC | [What are the memory types, and what are the GC methods?](#what-are-the-memory-types-and-what-are-the-gc-methods-revisit-gc-section) |
| 112 | Memory & GC | [What is Garbage Collection, and why is it needed in Java?](#what-is-garbage-collection-and-why-is-it-needed-in-java) |
| 113 | Memory & GC | [How can we manually request Garbage Collection, and is it recommended?](#how-can-we-manually-request-garbage-collection-and-is-it-recommended) |
| 114 | Memory & GC | [How does finalize() work, and is it guaranteed to execute? When does GC call finalize()?](#how-does-finalize-work-and-is-it-guaranteed-to-execute-when-does-gc-call-finalize) |
| 115 | Memory & GC | [What are the different Garbage Collectors available in Java? Different ways to call GC?](#what-are-the-different-garbage-collectors-available-in-java-different-ways-to-call-gc) |
| 116 | Memory & GC | [What are Strong, Soft, Weak, and Phantom References in Java?](#what-are-strong-soft-weak-and-phantom-references-in-java) |
| 117 | Memory & GC | [How to make an object eligible for Garbage Collection?](#how-to-make-an-object-eligible-for-garbage-collection) |
| 118 | Memory & GC | [Differentiate between Heap vs Stack Memory in Java? Different parts of the heap?](#differentiate-between-heap-vs-stack-memory-in-java-different-parts-of-the-heap) |
| 119 | Memory & GC | [Which part of the memory is involved in Garbage Collection? Stack or Heap?](#which-part-of-the-memory-is-involved-in-garbage-collection-stack-or-heap) |
| 120 | Memory & GC | [How do you identify minor and major garbage collection in Java?](#how-do-you-identify-minor-and-major-garbage-collection-in-java) |
| 121 | Memory & GC | [What is the algorithm for garbage collection in Java?](#what-is-the-algorithm-for-garbage-collection-in-java) |
|     | **Multithreading and Concurrency** | |
| 122 | Multithreading | [Process vs Thread.](#process-vs-thread) |
| 123 | Multithreading | [What is latching with respect to multithreading?](#what-is-latching-with-respect-to-multithreading) |
| 124 | Multithreading | [What is synchronization in Java?](#what-is-synchronization-in-java) |
| 125 | Multithreading | [Synchronization definition and purpose.](#synchronization-definition-and-purpose) |
| 126 | Multithreading | [What is a critical section?](#what-is-a-critical-section) |
| 127 | Multithreading | [How to achieve thread safety apart from using synchronization? (Semaphore & Mutex)](#how-to-achieve-thread-safety-apart-from-using-synchronization-semaphore--mutex) |
| 128 | Multithreading | [What is a deadlock situation in a Java program?](#what-is-a-deadlock-situation-in-a-java-program) |
| 129 | Multithreading | [Race Condition in threads.](#race-condition-in-threads) |
| 130 | Multithreading | [Why are wait(), notify(), and notifyAll() in Object class and not in Thread class?](#why-are-wait-notify-and-notifyall-in-object-class-and-not-in-thread-class) |
| 131 | Multithreading | [Explain ExecutorService framework. submit vs execute?](#explain-executorservice-framework-submit-vs-execute) |
| 132 | Multithreading | [What is the need for the Executor framework?](#what-is-the-need-for-the-executor-framework) |
| 133 | Multithreading | [What is CompletableFuture? How is it different from Future?](#what-is-completablefuture-how-is-it-different-from-future) |
| 134 | Multithreading | [How to create a thread pool?](#how-to-create-a-thread-pool) |
| 135 | Multithreading | [Use of Thread dump.](#use-of-thread-dump) |
| 136 | Multithreading | [How to analyze thread dump.](#how-to-analyze-thread-dump) |
| 137 | Multithreading | [Callable and Future in ExecutorServices.](#callable-and-future-in-executorservices) |
|     | **Exception Handling** | |
| 138 | Exception Handling | [What is exception propagation in Java?](#what-is-exception-propagation-in-java) |
| 139 | Exception Handling | [What is BufferUnderflowException?](#what-is-bufferunderflowexception) |
| 140 | Exception Handling | [Explain try-with-resources. Why don't we need a finally block here?](#explain-try-with-resources-why-dont-we-need-a-finally-block-here) |
| 141 | Exception Handling | [Checked Exception vs Unchecked Exception.](#checked-exception-vs-unchecked-exception) |
|     | **Java 8 and Functional Programming** | |
| 142 | Java 8 & Functional | [What are Java 8 features?](#what-are-java-8-features) |
| 143 | Java 8 & Functional | [Explain the internal functioning of streams.](#explain-the-internal-functioning-of-streams) |
| 144 | Java 8 & Functional | [How do you implement a functional interface?](#how-do-you-implement-a-functional-interface) |
| 145 | Java 8 & Functional | [Random generation in streams.](#random-generation-in-streams) |
|     | **Design Patterns** | |
| 146 | Design Patterns | [What are factory methods in Java?](#what-are-factory-methods-in-java) |
| 147 | Design Patterns | [Spring design patterns.](#spring-design-patterns) |
|     | **Miscellaneous** | |
| 148 | Miscellaneous | [What is the transient keyword?](#transient-keyword) |
| 149 | Miscellaneous | [What is PostConstruct?](#what-is-postconstruct) |
|     | **JVM Internals** | |
| 150 | JVM Internals | [(Refer to external resources/notes for JVM Internals)](#refer-to-external-resourcesnotes-for-jvm-internals) |
|     | **Generics** | |
| 151 | Generics | [(Refer to external resources/notes for Generics)](#refer-to-external-resourcesnotes-for-generics) |
|     | **Reflection API** | |
| 152 | Reflection API | [(Refer to external resources/notes for Reflection API)](#refer-to-external-resourcesnotes-for-reflection-api) |
|     | **Spring Framework** | |
| 153 | Spring | [Bean life cycle in Spring.](#bean-life-cycle-in-spring) |
| 154 | Spring | [@Qualifier and @Required.](#qualifier-and-required) |
| 155 | Spring | [What are actuators, and which have you used?](#what-are-actuators-and-which-have-you-used) |
| 156 | Spring | [How to handle global exceptions in Spring Boot.](#how-to-handle-global-exceptions-in-spring-boot) |
| 157 | Spring | [How to access other .properties files in Spring Boot.](#how-to-access-other-properties-files-in-spring-boot) |
| 158 | Spring | [How to rename application.properties and use a custom properties file.](#how-to-rename-applicationproperties-and-use-a-custom-properties-file) |
| 159 | Spring | [What is advice in AOP?](#what-is-advice-in-aop) |
| 160 | Spring | [ApplicationContext vs WebApplicationContext.](#applicationcontext-vs-webapplicationcontext) |
| 161 | Spring | [Scopes in Spring.](#scopes-in-spring) |
| 162 | Spring | [Spring Data JPA flow.](#spring-data-jpa-flow) |
| 163 | Spring | [How to create a composite key in Spring Data JPA.](#how-to-create-a-composite-key-in-spring-data-jpa) |
| 164 | Spring | [What are pointcuts in AOP?](#what-are-pointcuts-in-aop) |
