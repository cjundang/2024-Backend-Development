# 🧾 Worksheet 4: Develop a RESTful CRUD API for Students Using Spring Boot and Web Frontend

**Course:** IIT67-272 Fundamentals of Backend Architecture
**Topic:** RESTful API CRUD Development with Spring Boot
**Duration:** 3 Hours
**Tools:** Java 17+, Spring Boot 3, Maven, H2 Database / MySQL, Postman, HTML + Fetch API

---

## 🎯 Learning Objectives

By the end of this lab, students will be able to:

1. Create a **Spring Boot** project using **Spring Initializr**.
2. Implement a **RESTful CRUD API** for Student data.
3. Use **Controller – Service – Repository** layered architecture.
4. Connect and persist data using **Spring Data JPA**.
5. Build a simple **frontend webpage** (HTML + JavaScript) to interact with the API.

---

## 🧠 Background Theory

* **Spring Boot** simplifies Java backend development by auto-configuring web and database components.
* The **RESTful API** exposes resources via endpoints using HTTP methods (`GET`, `POST`, `PUT`, `DELETE`).
* **Spring Data JPA** handles database CRUD operations automatically.
* A **Frontend Web Page** can call these APIs using the **Fetch API** for testing.

---

## 🧩 Task Description

Create a **Student Management API** that provides full CRUD operations and a simple frontend to test it.

### Functional Requirements

| Operation      | HTTP Method | Endpoint             | Description        |
| -------------- | ----------- | -------------------- | ------------------ |
| Create Student | POST        | `/api/students`      | Add new student    |
| Read All       | GET         | `/api/students`      | List all students  |
| Read One       | GET         | `/api/students/{id}` | Get student by ID  |
| Update         | PUT         | `/api/students/{id}` | Edit existing data |
| Delete         | DELETE      | `/api/students/{id}` | Remove student     |

---

## 🧱 Project Structure (Suggested)

```
student-api/
 ├── src/
 │   ├── main/java/com/example/studentapi/
 │   │      ├── controller/
 │   │      ├── service/
 │   │      ├── repository/
 │   │      ├── model/
 │   │      └── StudentApiApplication.java
 │   └── main/resources/
 │          ├── static/index.html
 │          └── application.properties
 └── pom.xml
```

---

## 💻 Step-by-Step Instructions

### Step 1 – Create Project

Use **Spring Initializr**:

* Project: Maven Project
* Language: Java
* Spring Boot: 3.x
* Dependencies:

  * Spring Web
  * Spring Data JPA
  * H2 Database (or MySQL Driver)

---

### Step 2 – Model Layer

```java
// model/Student.java
package com.example.studentapi.model;

import jakarta.persistence.*;
import lombok.Data;

@Data
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    private String major;
}
```

---

### Step 3 – Repository Layer

```java
// repository/StudentRepository.java
package com.example.studentapi.repository;
import com.example.studentapi.model.Student;
import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository extends JpaRepository<Student, Long> {}
```

---

### Step 4 – Service Layer

```java
// service/StudentService.java
package com.example.studentapi.service;

import com.example.studentapi.model.Student;
import com.example.studentapi.repository.StudentRepository;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class StudentService {
    private final StudentRepository repo;
    public StudentService(StudentRepository repo) { this.repo = repo; }

    public List<Student> getAll() { return repo.findAll(); }
    public Student getById(Long id) { return repo.findById(id).orElse(null); }
    public Student save(Student s) { return repo.save(s); }
    public void delete(Long id) { repo.deleteById(id); }
}
```

---

### Step 5 – Controller Layer

```java
// controller/StudentController.java
package com.example.studentapi.controller;

import com.example.studentapi.model.Student;
import com.example.studentapi.service.StudentService;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/students")
@CrossOrigin("*") // Allow frontend access
public class StudentController {
    private final StudentService service;
    public StudentController(StudentService service) { this.service = service; }

    @GetMapping
    public List<Student> getAll() { return service.getAll(); }

    @GetMapping("/{id}")
    public Student getById(@PathVariable Long id) { return service.getById(id); }

    @PostMapping
    public Student create(@RequestBody Student s) { return service.save(s); }

    @PutMapping("/{id}")
    public Student update(@PathVariable Long id, @RequestBody Student s) {
        s.setId(id);
        return service.save(s);
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) { service.delete(id); }
}
```

---

### Step 6 – Configuration

```properties
# application.properties
spring.datasource.url=jdbc:h2:mem:studentdb
spring.datasource.driverClassName=org.h2.Driver
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
server.port=8080
```

---

## 🌐 Step 7 – Frontend Web Page (Testing Interface)

**File:** `src/main/resources/static/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Student Management (Spring Boot)</title>
<style>
  body { font-family: Arial; margin: 2em; color: #333; }
  table { width: 100%; border-collapse: collapse; margin-top: 1em; }
  th, td { border: 1px solid #999; padding: 6px; }
  th { background: #f2f2f2; }
  input { margin: 4px; padding: 6px; }
  button { padding: 6px 10px; margin: 4px; }
</style>
</head>
<body>
<h1>🎓 Student Management</h1>

<div>
  <input id="name" placeholder="Name">
  <input id="email" placeholder="Email">
  <input id="major" placeholder="Major">
  <button onclick="addStudent()">Add Student</button>
</div>

<table id="table">
  <thead><tr><th>ID</th><th>Name</th><th>Email</th><th>Major</th><th>Actions</th></tr></thead>
  <tbody></tbody>
</table>

<script>
const API = '/api/students';

async function loadStudents() {
  const res = await fetch(API);
  const data = await res.json();
  const tbody = document.querySelector('#table tbody');
  tbody.innerHTML = '';
  data.forEach(stu => {
    tbody.innerHTML += `
      <tr>
        <td>${stu.id}</td>
        <td>${stu.name}</td>
        <td>${stu.email}</td>
        <td>${stu.major}</td>
        <td><button onclick="deleteStudent(${stu.id})">🗑 Delete</button></td>
      </tr>`;
  });
}

async function addStudent() {
  const name = nameInput.value;
  const email = emailInput.value;
  const major = majorInput.value;
  await fetch(API, {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({name, email, major})
  });
  loadStudents();
}

async function deleteStudent(id) {
  await fetch(`${API}/${id}`, { method: 'DELETE' });
  loadStudents();
}

const nameInput = document.getElementById('name');
const emailInput = document.getElementById('email');
const majorInput = document.getElementById('major');
loadStudents();
</script>
</body>
</html>
```

---

## 🧪 Testing Checklist

| Action        | Method  | Endpoint             | Expected               |
| ------------- | ------- | -------------------- | ---------------------- |
| Add Student   | POST    | `/api/students`      | New record added       |
| View All      | GET     | `/api/students`      | All records displayed  |
| Update        | PUT     | `/api/students/{id}` | Record modified        |
| Delete        | DELETE  | `/api/students/{id}` | Record removed         |
| Frontend Test | Browser | `/index.html`        | Interactive UI working |

---

## 📋 Evaluation Criteria (100 pts)

| Component                                | Points |
| ---------------------------------------- | ------ |
| Correct CRUD endpoints                   | 25     |
| Proper use of Service/Repository layers  | 20     |
| Database connectivity (H2/MySQL)         | 15     |
| Working frontend HTML + Fetch API        | 25     |
| Clean project structure & documentation  | 10     |
| Testing evidence (Postman / screenshots) | 5      |

---

## 🧭 Reflection Questions

1. How does the **Controller–Service–Repository** architecture improve maintainability?
2. What differences do you observe between **FastAPI** and **Spring Boot** implementations?
3. Why is **CORS** configuration important when connecting a web frontend to an API?

---

## 📚 References

* [Spring Boot Documentation](https://spring.io/projects/spring-boot)
* [Spring Data JPA Guide](https://spring.io/guides/gs/accessing-data-jpa/)
* [Fetch API – MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
* [RESTful API Design Best Practices](https://restfulapi.net/)

