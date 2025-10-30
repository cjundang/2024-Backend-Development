---
marp: true
theme: default
class: lead
paginate: true
---

# บทที่ 7  
## File Handling & Upload/Download  
### in Spring Boot

---

## หัวข้อที่จะเรียนรู้
- แนวคิดการจัดการไฟล์ใน Backend  
- การอัปโหลดไฟล์ (Multipart Upload)  
- การจัดเก็บไฟล์ (Local vs Cloud)  
- การดาวน์โหลดไฟล์  
- Security Considerations

---

## แนวคิดการจัดการไฟล์

- Backend ต้องรองรับการจัดเก็บไฟล์ เช่น:  
  - รูปโปรไฟล์ผู้ใช้  
  - เอกสาร PDF  
  - ไฟล์แนบ (Attachment)  

- รูปแบบการจัดเก็บ:  
  - Local Disk  
  - Database (แนะนำเก็บ Metadata + Path ไม่เก็บไฟล์จริง)  
  - Cloud Storage (AWS S3, GCP Storage)

---

## Multipart File Upload

- ใช้ **HTTP POST + Content-Type: multipart/form-data**  
- Spring Boot รองรับผ่าน `MultipartFile`  

ตัวอย่าง:  
```java
@PostMapping("/upload")
public String uploadFile(@RequestParam("file") MultipartFile file) {
    return "Uploaded: " + file.getOriginalFilename();
}
````

---

## การบันทึกไฟล์ใน Local Storage

```java
String path = "uploads/" + file.getOriginalFilename();
file.transferTo(new File(path));
```

* สร้างโฟลเดอร์ `uploads/`
* เก็บ path ลง DB แทนไฟล์จริง
* ข้อควรระวัง: File overwrite, Validation

---

## การดาวน์โหลดไฟล์

```java
@GetMapping("/download/{filename}")
public ResponseEntity<Resource> download(@PathVariable String filename) {
    Path path = Paths.get("uploads/").resolve(filename);
    Resource resource = new FileSystemResource(path);
    return ResponseEntity.ok()
        .header(HttpHeaders.CONTENT_DISPOSITION, 
                "attachment; filename=\"" + filename + "\"")
        .body(resource);
}
```

---

## Metadata & Database

* เก็บข้อมูลเพิ่มเติม:

  * File name
  * File type (MIME)
  * File size
  * Upload time
  * Owner (userId)

* Database ใช้เก็บ metadata, ส่วนไฟล์จริงเก็บใน disk/cloud

---

## Security Considerations

* ตรวจสอบ:

  * ขนาดไฟล์ (size limit)
  * นามสกุลไฟล์ (extension whitelist)
  * MIME type
* ป้องกันการโจมตี:

  * Directory traversal (`../../etc/passwd`)
  * Malware upload

---

## Lab  

1. เขียน API อัปโหลดไฟล์ → เก็บใน `uploads/`
2. เก็บ metadata ลง DB (`filename`, `size`, `type`)
3. เขียน API ดาวน์โหลดไฟล์
4. ทดสอบด้วย Postman

---

## Assignment

* พัฒนา API `/users/{id}/avatar`

  * **POST** → อัปโหลดรูปโปรไฟล์
  * **GET** → ดาวน์โหลดรูปโปรไฟล์

* ข้อกำหนด:

  * รองรับเฉพาะ `.jpg` / `.png`
  * ขนาดไม่เกิน 2MB
  * เก็บ metadata ลง DB

* ส่งโค้ด + Screenshot การทดสอบผ่าน Postman

