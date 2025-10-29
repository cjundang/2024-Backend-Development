# ใบงานที่ 2 : 
## 🎯 เป้าหมายการเรียนรู้ (Learning Outcomes)

หลังจบกิจกรรมชุดนี้ นักศึกษาควรสามารถ

1. อธิบายโครงสร้าง backend ตามแนวคิด Client–Server, REST, Layered, MVC
2. สร้าง RESTful API ด้วย **FastAPI** ได้
3. แยกโครงสร้าง Controller, Service, Repository แบบ Layered Architecture
4. ใช้หลัก **Dependency Injection (DI)** และ **Pydantic Model** เพื่อจัดการข้อมูล
5. ทดสอบ API ด้วย **curl / Postman / Swagger UI**

---

## 🧭 แผนการเรียนรู้เชิงปฏิบัติ (Lab Series)

---

### 🔹 **ใบงานที่ 1 — เข้าใจ Client–Server และ REST**

**วัตถุประสงค์:**
เข้าใจหลัก Client–Server และ REST ผ่านการทดลองเรียก API

**กิจกรรม:**

1. ใช้ Postman หรือ `curl` ทดลองเรียก public API เช่น

   ```
   GET https://jsonplaceholder.typicode.com/users/1
   ```
2. วิเคราะห์ Request–Response

   * Method
   * URL
   * Header
   * Body
   * Status Code

**แบบฝึกหัด:**

* วาดแผนภาพการสื่อสาร Client–Server
* อธิบายว่า HTTP เป็นโปรโตคอลแบบ *Stateless* อย่างไร

---

### 🔹 **ใบงานที่ 2 — สร้าง REST API แรกด้วย FastAPI**

**วัตถุประสงค์:**
เข้าใจโครงสร้างโปรเจกต์ FastAPI และ REST endpoint

**เตรียมเครื่องมือ:**

```bash
pip install fastapi uvicorn
```

**ตัวอย่างโค้ด:**

```python
# main.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/hello")
def say_hello():
    return {"message": "Hello, Backend!"}
```

**รันเซิร์ฟเวอร์:**

```bash
uvicorn main:app --reload
```

เปิด browser ไปที่ `http://127.0.0.1:8000/docs`

**กิจกรรม:**

* ทดลองเรียก `/hello` ผ่าน Swagger UI
* สังเกต Request–Response

**คำถามท้ายใบงาน:**

1. FastAPI ทำงานในบทบาทใดของ Client–Server?
2. Request/Response ใน REST API คืออะไร?

---

### 🔹 **ใบงานที่ 3 — เพิ่ม Layered Architecture (Controller → Service → Repository)**

**วัตถุประสงค์:**
แยกชั้นการทำงานและใช้ Dependency Injection

**โครงสร้างไฟล์:**

```
project/
 ├── main.py
 ├── services/
 │   └── student_service.py
 └── repositories/
     └── student_repo.py
```

**student_repo.py**

```python
students = [{"id": 1, "name": "Ananya", "major": "IT"}]

def get_students():
    return students
```

**student_service.py**

```python
from repositories.student_repo import get_students

def list_students():
    return get_students()
```

**main.py**

```python
from fastapi import FastAPI
from services.student_service import list_students

app = FastAPI()

@app.get("/students")
def get_students():
    return list_students()
```

**แบบฝึกหัด:**

* อธิบายว่าไฟล์ใดคือ Controller / Service / Repository
* เขียน diagram แสดง Layered Architecture

---

### 🔹 **ใบงานที่ 4 — ออกแบบ RESTful API สำหรับ Resource จริง**

**วัตถุประสงค์:**
ฝึกออกแบบและสร้าง RESTful Endpoint แบบเต็มรูปแบบ

**ตัวอย่าง Model:**

```python
from pydantic import BaseModel

class Student(BaseModel):
    id: int
    name: str
    major: str
```

**main.py**

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Student(BaseModel):
    id: int
    name: str
    major: str

students = []

@app.get("/api/students")
def get_all():
    return students

@app.post("/api/students")
def add_student(s: Student):
    students.append(s)
    return s
```

**ทดสอบผ่าน Swagger UI หรือ Postman:**

* `GET /api/students`
* `POST /api/students` พร้อม JSON body

**คำถามท้ายใบงาน:**

* Request–Response เหมือน/ต่างจากใบงาน 2 อย่างไร?
* หลัก RESTful ใดที่คุณใช้ใน endpoint เหล่านี้?

---

### 🔹 **ใบงานที่ 5 — MVC และ Persistence (SQLite + FastAPI + SQLModel)**

**วัตถุประสงค์:**
เพิ่มชั้น Model (Database) และเชื่อมต่อกับ Repository

**ติดตั้ง:**

```bash
pip install sqlmodel
```

**ตัวอย่างโครงสร้าง:**

```
 ├── main.py
 ├── models/
 │   └── student_model.py
 ├── repositories/
 │   └── student_repo.py
 ├── services/
 │   └── student_service.py
```

**student_model.py**

```python
from sqlmodel import SQLModel, Field

class Student(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str
    major: str
```

**student_repo.py**

```python
from sqlmodel import Session, select
from models.student_model import Student

def get_all_students(session: Session):
    return session.exec(select(Student)).all()
```

**main.py**

```python
from fastapi import FastAPI, Depends
from sqlmodel import SQLModel, Session, create_engine
from models.student_model import Student
from repositories.student_repo import get_all_students

engine = create_engine("sqlite:///database.db")

app = FastAPI()

@app.on_event("startup")
def on_startup():
    SQLModel.metadata.create_all(engine)

def get_session():
    with Session(engine) as session:
        yield session

@app.get("/students")
def list_students(session: Session = Depends(get_session)):
    return get_all_students(session)
```

**ผลลัพธ์:**
นักศึกษาจะได้ระบบ RESTful API ที่มี
Controller + Service + Repository + Database

---

### 🔹 **ใบงานที่ 6 (Project Mini) — ออกแบบ Backend จริง**

**โจทย์:**
ให้นักศึกษาสร้างระบบ Backend ขนาดย่อม เช่น

* ระบบจัดการหนังสือ
* ระบบบันทึกงาน To-Do
* ระบบนักเรียน

**เงื่อนไข:**

* ใช้ FastAPI
* ใช้ RESTful endpoints
* ใช้โครงสร้างแบบ Layered
* บันทึกข้อมูลลง SQLite

**Deliverables:**

* Diagram แสดง Layered Architecture
* Screenshot จาก Swagger UI
* ไฟล์ source code

---

## 📊 เกณฑ์ประเมิน

| หมวด                        | รายละเอียด                            | สัดส่วน |
| --------------------------- | ------------------------------------- | ------- |
| ความเข้าใจแนวคิดสถาปัตยกรรม | อธิบายการทำงานได้                     | 20%     |
| ความถูกต้องของโค้ดและ API   | Endpoint ทำงานตาม REST                | 40%     |
| การออกแบบโครงสร้าง Layer    | Controller / Service / Repo แยกชัดเจน | 20%     |
| การทดสอบและบันทึกผล         | ใช้ Postman/Swagger พร้อมผลลัพธ์      | 10%     |
| การนำเสนอ/ขยายระบบ          | มีความคิดสร้างสรรค์                   | 10%     |

---

## 📚 สรุปแนวคิดสำหรับผู้สอน

| บทในตำรา             | กิจกรรมในใบงาน         | ทักษะที่พัฒนา        |
| -------------------- | ---------------------- | -------------------- |
| 2.2 Frontend–Backend | ทดลองเรียก API         | เข้าใจ Client–Server |
| 2.3 Client–Server    | curl / Postman test    | Request–Response     |
| 2.4 REST             | ออกแบบ Endpoint        | RESTful thinking     |
| 2.5 Layered          | สร้าง Service/Repo     | Modular coding       |
| 2.6 MVC              | Controller–Model       | Code organization    |
| 2.7 Framework        | FastAPI implementation | Framework practice   |

---

หากต้องการ ผมสามารถสร้าง:

* 📄 **ใบงานฉบับเต็ม (Markdown + PDF พร้อมช่องว่างตอบคำถาม)**
* 🧑‍💻 **Template โปรเจกต์ FastAPI (โครงสร้างไฟล์พร้อม starter code)**

คุณอยากให้ผมสร้างแบบไหนก่อนครับ?

> 🔹 สร้างไฟล์ **ใบงาน (Markdown พร้อม Pre/Post-Lab)**
> 🔹 หรือ **Starter Project Template (FastAPI directory พร้อมโค้ดเบื้องต้น)**
