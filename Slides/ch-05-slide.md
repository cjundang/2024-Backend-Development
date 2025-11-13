---
marp: true
theme: default
size: 16:9
paginate: true
title: JavaScript
description: Connecting Spring Boot to RDBMS (MySQL) using JPA
backgroundImage: url('wallpaper.001.jpeg')
backgroundSize: cover
footer: "IIT67-272 | Chapter 2: Fundamentals of Backend Architecture"

style: |
  section {
    color: blue; /* สีตัวอักษรทั้งหมด */
  }
  h1 {
    color: black;
    font-size: 56px !important;
    line-height: 1.1;
    margin: 0.2em 0 0.4em;
  }
  h2 {
    color: black;
    font-size: 56px !important;
    line-height: 1.1;
    margin: 0.2em 0 0.4em;
  }
  h3 {
    color: black;
  }
---

# Spring Boot + Database Integration
### Lecture Duration: 90 Minutes

Topic:
- Spring Data JPA  
- Entity Mapping  
- Repository Interface  
- H2/MySQL Connection  
- CRUD Demo with MySQL

---

# 1. Relational Databases & JPA  

---

## RDBMS and Persistence

- RDBMS (Relational Database Management System)  
  - Examples: MySQL, PostgreSQL, MariaDB, H2  
  - Stores data in **tables** (rows, columns)  
  - Supports **SQL** for queries  

- Spring Boot applications must persist data:
  - Students
  - Courses
  - Enrollments  
  rather than keeping them only in memory.

---

## Object–Relational Mapping (ORM)

- Problem:  
  - Java works with **objects**  
  - Database works with **tables/rows**  
- Solution:  
  - ORM maps Java classes ↔ database tables

- JPA (Java Persistence API):  
  - Specification (the rules / contract)
  - Spring Data JPA: implementation to make it easier

---

## JPA in the Architecture

```text
           Controller
                ↓
             Service
                ↓
      Spring Data JPA (Repository)
                ↓
           MySQL / H2 DB
````

Observation: The application does not write raw SQL manually for basic CRUD.
The Repository layer speaks to the database through JPA.

---

# 2. Entity Mapping in Spring Boot

---

## @Entity Class

We model a database table using a Java class.

```java
import jakarta.persistence.*;

@Entity
@Table(name = "students")
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;  // primary key

    private String name;
    private String email;
    private String major;

    // getters and setters ...
}
```

Key concepts:

* `@Entity`: marks the class as a JPA-managed table
* `@Table`: optional custom table name
* `@Id`: primary key column
* `@GeneratedValue`: auto-increment strategy

---

## Column Mapping (Optional Control)

```java
@Column(name = "student_email", unique = true, length = 100)
private String email;
```

Why useful:

* Aligns Java fields with DB column definitions
* Enforces constraints (unique, length)

Design principle: The entity represents “data state” in the system.

---

## Life Cycle of an Entity

1. Create new Student object
2. Save via Repository
3. JPA translates to `INSERT`
4. Fetch later via Repository
5. JPA translates to `SELECT`

In other words, we operate on objects; JPA handles SQL.

---

# 3. Repository Layer & CRUD

(20 min)

---

## Repository Pattern

```java
import org.springframework.data.jpa.repository.JpaRepository;
import com.example.demo.Student;

public interface StudentRepository extends JpaRepository<Student, Long> {
    // Optional: custom queries, e.g. findByEmail
    Student findByEmail(String email);
}
```

* `JpaRepository<Student, Long>`

  * `Student` = entity type
  * `Long` = primary key type
* Provides built-in CRUD:

  * `findAll()`
  * `findById(id)`
  * `save(entity)`
  * `deleteById(id)`

---

## Service Layer Using Repository

```java
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class StudentService {
    private final StudentRepository repo;

    public StudentService(StudentRepository repo) {
        this.repo = repo;
    }

    public List<Student> getAllStudents() {
        return repo.findAll();
    }

    public Student getStudentById(Long id) {
        return repo.findById(id).orElse(null);
    }

    public Student createOrUpdate(Student s) {
        return repo.save(s);
    }

    public void deleteStudent(Long id) {
        repo.deleteById(id);
    }
}
```

Reason for this layer:

* Centralizes business logic and validation
* Keeps Controller thin and testable

---

## REST Controller Exposing CRUD Endpoints

```java
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/students")
@CrossOrigin("*")
public class StudentController {

    private final StudentService service;
    public StudentController(StudentService service) {
        this.service = service;
    }

    @GetMapping
    public List<Student> all() {
        return service.getAllStudents();
    }

    @GetMapping("/{id}")
    public Student one(@PathVariable Long id) {
        return service.getStudentById(id);
    }

    @PostMapping
    public Student create(@RequestBody Student s) {
        return service.createOrUpdate(s);
    }

    @PutMapping("/{id}")
    public Student update(@PathVariable Long id,
                          @RequestBody Student s) {
        s.setId(id);
        return service.createOrUpdate(s);
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        service.deleteStudent(id);
    }
}
```

This controller becomes the public API of the backend.

---

# 4. Connecting Spring Boot to H2 / MySQL

(20 min)

---

## Option A: In-Memory H2 Database

`application.properties` example:

```properties
spring.datasource.url=jdbc:h2:mem:studentdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
server.port=8080
```

Notes:

* Data stored in memory (lost on restart)
* Good for development and classroom demos
* `spring.h2.console.enabled=true` gives a web UI to inspect tables

---

## Option B: MySQL Database

`application.properties` example:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/studentdb
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
server.port=8080
```

Explanation:

* `ddl-auto=update`

  * Automatically creates/updates tables based on `@Entity`
* `show-sql=true`

  * Prints SQL statements to console for learning/debugging

---

## MySQL Integration Flow

```text
Spring Boot (Controller)
        ↓
Service Layer
        ↓
StudentRepository (JPA)
        ↓
Hibernate (ORM)
        ↓
MySQL (studentdb, table students)
```

Observation: We do not manually write `INSERT INTO students ...`; Spring generates SQL.

---

## Verifying the Connection

1. Application starts with no error
2. Table `students` appears in MySQL/H2
3. Sending POST to `/api/students` inserts data
4. Sending GET to `/api/students` returns stored data

This confirms persistence layer is functioning.

---

# 5. CRUD Demo via JPA + MySQL

(20 min)

---

## Example: Create Student (POST)

**Request (JSON):**

```json
POST /api/students
{
  "name": "Alice",
  "email": "alice@university.edu",
  "major": "Computer Science"
}
```

**Response:**

```json
{
  "id": 1,
  "name": "Alice",
  "email": "alice@university.edu",
  "major": "Computer Science"
}
```

What happened:

* Controller receives JSON
* Service calls `repo.save()`
* Hibernate performs SQL `INSERT`
* MySQL stores the row and returns new `id`

---

## Example: Read All Students (GET)

```http
GET /api/students
```

Response (JSON array):

```json
[
  {
    "id": 1,
    "name": "Alice",
    "email": "alice@university.edu",
    "major": "Computer Science"
  },
  {
    "id": 2,
    "name": "Bob",
    "email": "bob@university.edu",
    "major": "Information Systems"
  }
]
```

This is how frontend pages (HTML + JS) or Postman clients consume data.

---

## Example: Update Student (PUT)

```http
PUT /api/students/1
{
  "name": "Alice Tan",
  "email": "alice.tan@university.edu",
  "major": "Computer Science"
}
```

Effect:

* If `id = 1` exists → update row
* If not found → (by default in this simple service) creates/overwrites

In production we add validation and 404 handling.

---

## Example: Delete Student (DELETE)

```http
DELETE /api/students/1
```

Expected result:

* Row with `id = 1` should be removed from database
* Subsequent `GET /api/students/1` should not find it

---

# Summary and Next Steps

---

## Key Points from Today

1. An RDBMS such as MySQL persists data in relational tables.
2. Spring Data JPA provides ORM mapping between Java objects and SQL tables.
3. `@Entity` defines the table schema in code.
4. `JpaRepository` gives CRUD without manual SQL.
5. `application.properties` controls connection to H2 or MySQL.
6. REST Controller exposes CRUD endpoints externally.

Outcome: You can now build a persistent backend service.

---

## Next Workshop

Hands-on Lab:

* Configure Spring Boot + MySQL
* Implement `Student` entity and repository
* Test endpoints in Postman
* Use a simple HTML/JavaScript frontend (Fetch API) to:

  * List all students
  * Add student
  * Delete student

Goal: Full-stack CRUD pipeline from browser → API → database.

---

# Questions?

## IIT67-272 | Backend Fundamentals

Please prepare:

* Java 17+ installed
* MySQL or Dockerized MySQL
* Postman or alternative REST client
* Browser for frontend testing

```
```
