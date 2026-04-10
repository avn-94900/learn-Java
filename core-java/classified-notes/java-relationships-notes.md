

## Table of Contents

| #   | Question                                                                 |
|-----|--------------------------------------------------------------------------|
| 1   | [What is Association in OOP?](#what-is-association-in-oop)              |
| 2   | [What is Aggregation in OOP?](#what-is-aggregation-in-oop)              |
| 3   | [What is Composition in OOP?](#what-is-composition-in-oop)              |
| 4   | [Differences between Association, Aggregation, and Composition](#differences-between-association-aggregation-and-composition) |
| 5   | [Differences between is-a & has-a relationship](#)                                          |


---


## 1. Association

Represents a "uses-a" or "has-a" relationship.
Objects can be related without ownership.
May be unidirectional or bidirectional.
Objects can exist independently.
UML Notation: Plain line

### Example: One-to-Many Association (Doctor ↔ Patients)

```java
class Patient {
    private String name;

    Patient(String name) {
        this.name = name;
    }

    String getName() {
        return name;
    }
}

class Doctor {
    private String name;

    Doctor(String name) {
        this.name = name;
    }

    void treat(Patient patient) {
        System.out.println(name + " is treating patient " + patient.getName());
    }
}

public class AssociationDemo {
    public static void main(String[] args) {
        Doctor doctor = new Doctor("Dr. Sharma");
        Patient p1 = new Patient("John");
        Patient p2 = new Patient("Sara");

        // One-to-Many: A doctor can treat many patients
        doctor.treat(p1);
        doctor.treat(p2);
    }
}
```

Additional Examples:

* One-to-One: A person has one passport.
* Many-to-One: Many students belong to one school.
* Many-to-Many: Students enroll in many courses and vice versa.

---

## 2. Aggregation

Represents a "has-a" relationship.
Weak association – the child can exist independently.
Implemented via object reference.
Represented by a hollow diamond in UML diagrams.

### Example: Aggregation (Team has Players)

```java
import java.util.List;
import java.util.ArrayList;

class Player {
    private String name;

    Player(String name) {
        this.name = name;
    }

    String getName() {
        return name;
    }
}

class Team {
    private String teamName;
    private List<Player> players;

    Team(String teamName) {
        this.teamName = teamName;
        this.players = new ArrayList<>();
    }

    void addPlayer(Player player) {
        players.add(player); // Aggregation: Player is referenced but not owned
    }

    void showTeam() {
        System.out.println("Team: " + teamName);
        for (Player p : players) {
            System.out.println("- " + p.getName());
        }
    }
}

public class AggregationDemo {
    public static void main(String[] args) {
        Player p1 = new Player("Alice");
        Player p2 = new Player("Bob");

        Team team = new Team("Warriors");
        team.addPlayer(p1);
        team.addPlayer(p2);

        team.showTeam();

        // Players continue to exist even if the Team is disbanded
    }
}
```

---

## 3. Composition

Composition is a strong form of aggregation, where one class owns another class and is responsible for its lifecycle.
Represents a "part-of" or strong has-a relationship.
Strong association – the child cannot exist without the parent.
Represented by a solid diamond in UML diagrams.

### Example: Composition (Car has Engine)

```java
class Engine {
    private String type;

    Engine(String type) {
        this.type = type;
    }

    void start() {
        System.out.println("Engine of type " + type + " started.");
    }
}

class Car {
    private Engine engine;

    Car() {
        engine = new Engine("V8"); // Composition: Engine created and owned by Car
    }

    void drive() {
        engine.start();
        System.out.println("Car is driving.");
    }
}

public class CompositionDemo {
    public static void main(String[] args) {
        Car car = new Car();
        car.drive();

        // If the Car is destroyed, the Engine is also destroyed
    }
}
```

---

## Differences between Association, Aggregation, and Composition

| Feature      | Association          | Aggregation                   | Composition                      |
| ------------ | -------------------- | ----------------------------- | -------------------------------- |
| Type         | General relationship | Weak "has-a"                  | Strong "has-a"                   |
| Dependency   | No dependency        | Child can exist independently | Child cannot exist independently |
| Lifecycle    | Independent          | Independent                   | Dependent                        |
| UML Notation | Plain Line           | Hollow diamond                | Solid diamond                    |
| Example      | Student ↔ School     | Department → Professor        | House → Room                     |

---
## Comparison between is-a & has-a Relationship

| Feature            | is-a Relationship         | has-a Relationship             |
| ------------------ | ------------------------- | ------------------------------ |
| Concept            | Inheritance               | Composition/Aggregation        |
| Implementation     | `extends` / `implements`  | Class references another class |
| Relationship       | Child is a type of Parent | One class contains another     |
| Example            | Dog **is an** Animal      | Car **has a** Engine           |
| Design Flexibility | Less flexible             | More flexible                  |

---





<br/>
