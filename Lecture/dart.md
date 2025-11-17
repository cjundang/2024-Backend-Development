# บทที่ 1: ภาษา Java และการเขียนโปรแกรมเชิงวัตถุ (OOP)

## 1.1 บทนำ

Java เป็นหนึ่งในภาษาการเขียนโปรแกรมที่ได้รับความนิยมสูงสุด ใช้งานตั้งแต่เว็บเซิร์ฟเวอร์ แอป Android หุ่นยนต์ ไปจนถึงระบบธนาคาร จุดแข็งสำคัญคือโครงสร้างแบบ **Object-Oriented Programming (OOP)**

OOP คือแนวทางออกแบบโปรแกรมโดยจำลองโลกความจริง—วัตถุ การกระทำ และความสัมพันธ์—ให้อยู่ในโค้ด บทนี้ทบทวนแนวคิด OOP หลัก ๆ ผ่านตัวอย่างใน Java เปรียบเทียบกับภาษาอื่น และแนวทางจัดการข้อผิดพลาดอย่างปลอดภัย

> **หมายเหตุสำหรับตัวอย่างโค้ด:** ในบทแปลนี้ ส่วน “โปรแกรม/โค้ด” ถูกแทนที่ด้วย **ภาษา Dart** เพื่อให้เห็นภาพเทียบเคียงแนวคิด OOP ได้ชัดเจน

---

## 1.2 OOP คืออะไร?

**Object-Oriented Programming** ช่วยให้เรามองซอฟต์แวร์เป็น “โลกของวัตถุ (objects)” ที่มีข้อมูล (attributes/fields) และพฤติกรรม (methods)

### แนวคิดแกนหลักของ OOP

| แนวคิด            | คำอธิบาย                                   | ตัวอย่าง (เชิงไวยากรณ์)          |
| :---------------- | :----------------------------------------- | :------------------------------- |
| **Class**         | แม่แบบสำหรับสร้างวัตถุ                     | `class Student { ... }`          |
| **Object**        | อินสแตนซ์จริงที่สร้างจากคลาส               | `var s = Student();`             |
| **Encapsulation** | ซ่อนรายละเอียดภายใน ควบคุมการเข้าถึงข้อมูล | ใช้ `_private` และ getter/setter |
| **Inheritance**   | นำโค้ดไปใช้ซ้ำ/ขยายความสามารถ              | `class Dog extends Animal`       |
| **Polymorphism**  | อินเทอร์เฟซเดียว พฤติกรรมหลายรูปแบบ        | overriding เมธอด                 |

---

## 1.3 ทบทวนโครงสร้างโปรแกรม (เทียบเคียงด้วย Dart)

ทุกโปรแกรมต้องมีจุดเริ่มต้นการทำงาน ใน Dart คือฟังก์ชัน `main()`

```dart
void main() {
  print('Hello, Dart!');
}
```

### การรัน

1. บันทึกเป็น `hello.dart`
2. รัน: `dart run hello.dart`

---

## 1.4 คลาสและอ็อบเจ็กต์

**คลาส** กำหนดสิ่งที่วัตถุ “รู้” (ตัวแปร) และ “ทำ” (เมธอด) ส่วน **อ็อบเจ็กต์** คืออินสแตนซ์ที่สร้างจากคลาสนั้น

```dart
class Student {
  final String name;
  int score;

  Student({required this.name, required this.score});

  void showInfo() {
    print('$name ได้ $score คะแนน');
  }
}

void main() {
  final s = Student(name: 'Alice', score: 95);
  s.showInfo();
}
```

**ผลลัพธ์**

```
Alice ได้ 95 คะแนน
```

ที่นี่:

* `Student` คือคลาส
* `s` คืออ็อบเจ็กต์ (อินสแตนซ์) ของคลาสนั้น

---

## 1.5 การห่อหุ้ม (Encapsulation) — ปกป้องข้อมูล

สาระสำคัญคือ **ซ่อนรายละเอียด** การเก็บ/เปลี่ยนข้อมูล และควบคุมการเข้าถึงด้วย **getter/setter**

```dart
class Student {
  int _score = 0; // private ด้วยขีดล่างนำหน้า

  set score(int s) {
    if (s >= 0 && s <= 100) {
      _score = s;
    } else {
      print('คะแนนไม่ถูกต้อง!');
    }
  }

  int get score => _score;
}
```

**เหตุผลที่สำคัญ:**

* ป้องกันข้อมูลผิดรูปแบบ
* ยืดหยุ่นต่อการเปลี่ยนแปลงภายใน
* ส่งเสริมโค้ดที่ปลอดภัยและเป็นระเบียบ

---

## 1.6 การสืบทอด (Inheritance) — ใช้ซ้ำและขยายคลาส

สับคลาสสามารถ **นำโค้ดของซูเปอร์คลาสไปใช้** และ “ขยาย/เปลี่ยน” พฤติกรรมได้

```dart
class Animal {
  void speak() {
    print('Some sound...');
  }
}

class Dog extends Animal {
  @override
  void speak() {
    print('Woof!');
  }
}
```

### ตัวอย่างการรัน

```dart
void main() {
  final d = Dog();
  d.speak();
}
```

**ผลลัพธ์**

```
Woof!
```

อุปมา: สุนัขเป็น “ชนิดหนึ่งของ” สัตว์ สับคลาสจึงสืบคุณสมบัติของซูเปอร์คลาส

---

## 1.7 พหุสัณฐาน (Polymorphism) — หนึ่งหน้าตา หลายพฤติกรรม

ตัวแปรชนิดซูเปอร์คลาสอ้างถึงอ็อบเจ็กต์ของสับคลาสใดก็ได้ และเรียกใช้เมธอดที่ถูก override ตามชนิดจริงขณะรัน

```dart
void main() {
  Animal a = Dog();
  a.speak(); // เรียกเวอร์ชันของ Dog
}
```

ช่วยให้โค้ดยืดหยุ่น—เพิ่มชนิดใหม่ได้ โดยไม่ต้องแก้แกนหลัก

---

## 1.8 อินเทอร์เฟซ/สัญญา — กำหนด “ต้องมีเมธอดใดบ้าง”

ใน Dart ใช้ **abstract class** หรือใช้อินเทอร์เฟซโดยนัยผ่าน `implements` เพื่อบังคับให้คลาสต้องมีเมธอดตามสัญญา

```dart
abstract class Playable {
  void play();
}

class Dog implements Playable {
  @override
  void play() {
    print('สุนัขเล่นรับลูกบอล!');
  }
}
```

คลาสใด `implements Playable` ต้องมีเมธอด `play()` เสมอ เหมาะสำหรับเชื่อมคลาสที่ไม่เกี่ยวข้องกันให้มีพฤติกรรมร่วม

---

## 1.9 แพ็กเกจ/โมดูล — จัดระเบียบโค้ด

เมื่อโปรเจ็กต์เติบโต ควรจัดกลุ่มคลาสเป็นโครงสร้างโฟลเดอร์/แพ็กเกจ

โครงแบบตัวอย่าง (Dart package):

```
lib/
 └── animals/
      ├── animal.dart
      └── dog.dart
```

ภายใน `dog.dart`:

```dart
import 'animal.dart';

class Dog extends Animal {
  @override
  void speak() => print('Woof!');
}
```

แล้วไฟล์อื่น ๆ:

```dart
import 'package:my_app/animals/dog.dart';
```

🗂 โครงสร้างที่ดี = โค้ดอ่านง่ายและขยายได้

---

## 1.10 การจัดการข้อยกเว้น (Exception Handling) — รองรับความผิดพลาดอย่างสุภาพ

เมื่อเกิดเหตุผิดปกติ เช่น แบ่งด้วยศูนย์ ไฟล์หาย เป็นต้น แทนที่จะปล่อยให้โปรแกรมล่ม ควร **จับและจัดการ** อย่างเหมาะสม

### ตัวอย่าง (Dart)

```dart
void main() {
  try {
    final result = 10 ~/ 0; // แบ่งจำนวนเต็ม ทำให้เกิดข้อยกเว้น
    print(result);
  } on IntegerDivisionByZeroException {
    print('ห้ามหารด้วยศูนย์!');
  } catch (e) {
    print('ข้อผิดพลาด: $e');
  } finally {
    print('โปรแกรมจบการทำงาน');
  }
}
```

**ผลลัพธ์**

```
ห้ามหารด้วยศูนย์!
โปรแกรมจบการทำงาน
```

> หมายเหตุ: ใน Java มีทั้ง *checked* และ *unchecked exceptions* ส่วน Dart ไม่มี checked exceptions—นักพัฒนาจึงรับผิดชอบการจัดการตามความเหมาะสม

---

## 1.11 โปรแกรมตัวอย่างแบบครบถ้วน

รวมแนวคิด OOP: สืบทอด บังคับสัญญา (implements) และการห่อหุ้ม

```dart
abstract class Playable {
  void play();
}

class Animal {
  void speak() {
    print('...');
  }
}

class Dog extends Animal implements Playable {
  final String name;
  Dog(this.name);

  @override
  void speak() => print('$name พูดว่า Woof!');
  @override
  void play() => print('$name เล่นรับลูกบอล!');
}
```

```dart
void main() {
  try {
    final d = Dog('Bobby');
    d.speak();
    d.play();
  } catch (e) {
    print('Error: $e');
  }
}
```

**ผลลัพธ์**

```
Bobby พูดว่า Woof!
Bobby เล่นรับลูกบอล!
```

---

## 1.12 มินิโปรเจ็กต์: ระบบลงทะเบียนเรียน 🏫

**โจทย์:** ออกแบบโมเดล OOP สำหรับระบบลงทะเบียนรายวิชาอย่างง่าย

**ข้อกำหนด:**

* คลาส: `Student`, `Course`, `Registration`
* ใช้ **encapsulation** กับข้อมูลนักศึกษา
* ใช้ **inheritance** หากเหมาะสม (เช่น `OnlineCourse extends Course`)
* **โยนข้อยกเว้น** หากรายวิชาเต็ม (เช่น `throw Exception('Course is full');` ใน Dart)

**เป้าหมาย:** ประยุกต์หลักการ OOP ให้ครบในบริบทที่มีความหมาย

---

## 1.13 สรุป OOP

| แนวคิด                 | หน้าที่/คุณค่า                       |
| :--------------------- | :----------------------------------- |
| **Class & Object**     | นิยามและสร้างตัวตนในโค้ด             |
| **Encapsulation**      | ปกป้องรายละเอียดภายใน                |
| **Inheritance**        | ใช้ซ้ำและขยายพฤติกรรม                |
| **Polymorphism**       | เรียกอินเทอร์เฟซเดียว ได้หลายผลลัพธ์ |
| **Interface/Contract** | กำหนดพฤติกรรมร่วมที่ “ต้องมี”        |
| **Exception Handling** | รองรับความผิดพลาดอย่างเป็นระบบ       |

---

## 1.14 คำถามทบทวน

1. ความแตกต่างระหว่าง **คลาส** กับ **อ็อบเจ็กต์** คืออะไร?
2. เหตุใด **encapsulation** จึงสำคัญ?
3. คีย์เวิร์ดใดใน Java ที่อนุญาตให้คลาสหนึ่งสืบทอดอีกคลาสหนึ่ง?
4. **polymorphism** ทำให้โค้ดยืดหยุ่นได้อย่างไร?
5. ควรใช้อินเทอร์เฟซแทนการสืบทอดในกรณีใด?
6. จะเกิดอะไรขึ้นเมื่อโปรแกรม “หารด้วยศูนย์” (ทั้งใน Java และ Dart)?

---

## 1.15 การสะท้อนคิด

> “OOP ไม่ได้สอนเพียงการเขียนโค้ด แต่สอนให้คิดเป็น **ระบบ** — วัตถุที่ทำงานร่วมกัน”

เมื่อเชี่ยวชาญ OOP ใน Java ได้ ก็ถ่ายทอดทักษะไปยังภาษาใหม่ ๆ เช่น Python, C#, Swift—รวมถึง **Dart**—ได้อย่างคล่องตัว

---

## ✅ ประเด็นสำคัญ

* Java ใช้ **OOP** เป็นรากฐาน
* ความเข้าใจ OOP ช่วยให้การออกแบบโปรแกรมขนาดใหญ่เป็นระบบ
* คิดเสมอว่า

  > *ปัญหานี้มี “วัตถุ” อะไรบ้าง?*
  > *พวกมันทำอะไร?*
  > *ปฏิสัมพันธ์กันอย่างไร?*

---

## 🧩 แบบฝึกหัดเพิ่มเติม (ไม่บังคับ)

เขียนโปรแกรมระบบ **ห้องสมุด (Library System)** ขนาดเล็ก:

* สร้างคลาส `Book`, `Member`, `Library`
* ใช้ Encapsulation เพื่อปกป้องรายละเอียดของหนังสือ
* ใช้ Inheritance สำหรับ `EBook` กับ `PrintedBook`
* สร้างสัญญา `Borrowable`
* จัดการข้อยกเว้นเมื่อสมาชิกยืมเกิน 3 เล่ม

---

## 📖 อ่านเพิ่มเติม

* *Head First Java* — Kathy Sierra & Bert Bates
* *Effective Java* — Joshua Bloch
* *Java Programming for Beginners* (Oracle Academy)


---

# บทที่ 2: พื้นฐานการพัฒนาแอปพลิเคชันด้วย Flutter

## 2.1 บทนำ

Flutter เป็นเฟรมเวิร์กสำหรับการพัฒนาแอปพลิเคชันแบบ **cross-platform** ที่สามารถสร้างผลงานได้ทั้ง Android, iOS, Web และ Desktop จากโค้ดฐานเดียว โดยอาศัยภาษา **Dart** เป็นแกนหลักด้านตรรกะและโมเดลข้อมูล คุณลักษณะสำคัญที่ทำให้ Flutter ได้รับความนิยมอย่างมากในงานพัฒนาเชิงวิชาการและเชิงอุตสาหกรรมคือความสามารถด้านการเรนเดอร์ UI แบบกำหนดเอง ความถูกต้องตามหลัก reactive programming และประสิทธิภาพในการรันระดับ native

การศึกษาบทนี้จะทำให้เข้าใจกลไกหลักของ Flutter ตั้งแต่โครงสร้างโปรเจ็กต์ วิดเจ็ต การจัดการสถานะ ไปจนถึงแนวคิดสำคัญที่รองรับการออกแบบซอฟต์แวร์เชิงวัตถุ

---

## 2.2 สถาปัตยกรรมของ Flutter

Flutter ไม่อาศัยระบบ UI ดั้งเดิมของแพลตฟอร์ม แต่ใช้ **Skia Rendering Engine** เรนเดอร์องค์ประกอบทั้งหมดด้วยตนเอง ส่งผลให้สามารถควบคุมภาพได้ละเอียด และให้ผลลัพธ์คงที่ระหว่างแพลตฟอร์มต่าง ๆ

องค์ประกอบหลักประกอบด้วย:

1. **Framework Layer (Dart)**
   ประกอบด้วย widget system, rendering layer, animation, gestures

2. **Engine Layer (C++ / Skia)**
   รับผิดชอบการวาดภาพ การรันโค้ด Dart ผ่าน Dart VM

3. **Embedder Layer**
   ช่วยให้ Flutter ทำงานบน Android, iOS, Linux, Windows, macOS รวมถึง Web

---

## 2.3 โครงสร้างโปรเจ็กต์

หลังสร้างโปรเจ็กต์ด้วยคำสั่ง:

```
flutter create my_app
```

จะได้โครงสร้างหลักดังนี้:

```
lib/
  main.dart
android/
ios/
web/
pubspec.yaml
```

คำอธิบายสำคัญ:

* `lib/main.dart` — ไฟล์เริ่มต้นของแอป
* `pubspec.yaml` — ระบุ dependencies, assets, fonts
* โฟลเดอร์ platform-specific — ใช้เมื่อปรับแต่ง native

---

## 2.4 วิดเจ็ต (Widget) เป็นหน่วยพื้นฐานของ UI

ใน Flutter **ทุกองค์ประกอบคือวิดเจ็ต** ไม่ว่าจะเป็นปุ่ม ข้อความ รูปภาพ หรือโครงร่างการจัดวาง

### ประเภทวิดเจ็ตหลัก

| ประเภท              | ความหมาย                                                             |
| ------------------- | -------------------------------------------------------------------- |
| **StatelessWidget** | วิดเจ็ตที่ไม่เปลี่ยนสถานะ                                            |
| **StatefulWidget**  | วิดเจ็ตที่มีสถานะภายใน สามารถเปลี่ยนแปลงได้ตาม action หรือ event     |
| **InheritedWidget** | วิดเจ็ตสำหรับแพร่กระจายข้อมูลลงสู่ลำดับชั้น (ใช้ใน state management) |

---

## 2.5 StatelessWidget — ไม่มีสถานะ

```dart
class HelloWidget extends StatelessWidget {
  final String name;

  const HelloWidget({required this.name});

  @override
  Widget build(BuildContext context) {
    return Text("Hello, $name");
  }
}
```

ใช้ในกรณีที่ข้อมูลไม่เปลี่ยนตลอดอายุของวิดเจ็ต

---

## 2.6 StatefulWidget — มีสถานะที่เปลี่ยนแปลงได้

```dart
class CounterWidget extends StatefulWidget {
  const CounterWidget({super.key});

  @override
  State<CounterWidget> createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int counter = 0;

  void increment() {
    setState(() {
      counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('นับ: $counter'),
        ElevatedButton(onPressed: increment, child: Text('เพิ่ม')),
      ],
    );
  }
}
```

**คำอธิบาย:**
เมื่อผู้ใช้กดปุ่ม `setState()` จะเรียกการ re-render UI ใหม่

---

## 2.7 ระบบ Layout และการจัดวางองค์ประกอบ

Flutter ใช้หลักการจัดวางแบบเชิงลำดับชั้น (tree structure) ผ่านวิดเจ็ตประเภท container ต่าง ๆ

### ตัวอย่างวิดเจ็ตจัดวาง

* `Row()` — จัดเรียงตามแนวนอน
* `Column()` — จัดเรียงตามแนวตั้ง
* `Stack()` — ซ้อนทับกัน
* `Container()` — ใช้กำหนด padding, margin, decoration

ตัวอย่าง:

```dart
Widget build(BuildContext context) {
  return Container(
    padding: EdgeInsets.all(16),
    child: Row(
      mainAxisAlignment: MainAxisAlignment.spaceBetween,
      children: [
        Text('ซ้าย'),
        Text('ขวา'),
      ],
    ),
  );
}
```

---

## 2.8 Navigation และหน้าจอหลายหน้า

Flutter ใช้กลไก **Navigator** แบบ stack เพื่อควบคุมการเปลี่ยนหน้า

### การไปหน้าใหม่

```dart
Navigator.push(
  context,
  MaterialPageRoute(builder: (_) => SecondPage()),
);
```

### การกลับหน้าเดิม

```dart
Navigator.pop(context);
```

---

## 2.9 การจัดการสถานะ (State Management)

ในระบบซอฟต์แวร์ขนาดใหญ่จำเป็นต้องจัดการสถานะให้เป็นระบบ ตัวเลือกที่นิยม ได้แก่:

1. **setState() — local state**
   เหมาะกับ UI ที่มีขอบเขตเล็ก

2. **Provider** — pattern ที่เหมาะสำหรับแบ่งข้อมูลให้หลาย widget

3. **Riverpod** — ยืดหยุ่นกว่า provider, ใช้ได้แม้ไม่ผูกกับ widget tree

4. **Bloc/Cubit — Architecture แบบ reactive**
   เน้น separation of concerns ระหว่าง business logic และ UI

### ตัวอย่าง Provider

```dart
class CounterModel with ChangeNotifier {
  int value = 0;

  void increment() {
    value++;
    notifyListeners();
  }
}
```

---

## 2.10 การทำงานกับ API (Networking)

ใช้แพ็กเกจ `http` สำหรับรับ/ส่งข้อมูล

```dart
import 'package:http/http.dart' as http;

Future<void> fetchData() async {
  final response = await http.get(Uri.parse('https://api.example.com/data'));

  if (response.statusCode == 200) {
    print(response.body);
  } else {
    print('เกิดข้อผิดพลาด');
  }
}
```

---

## 2.11 ตัวอย่างโครงสร้างแอป Flutter อย่างง่าย

```dart
void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: const HomePage(),
    );
  }
}

class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('หน้าหลัก')),
      body: Center(
        child: ElevatedButton(
          child: Text('ไปหน้าที่สอง'),
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(builder: (_) => const SecondPage()),
            );
          },
        ),
      ),
    );
  }
}

class SecondPage extends StatelessWidget {
  const SecondPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('หน้า 2')),
      body: Center(child: Text('นี่คือหน้าที่สอง')),
    );
  }
}
```

---

## 2.12 Flutter กับหลัก OOP

Flutter อาศัย Dart ซึ่งรองรับ OOP อย่างเต็มรูปแบบ ดังนั้นจึงสามารถออกแบบ UI และ Business Logic ด้วยโครงสร้างแบบโมดูลาร์และ reusable component โดยเน้น:

* **การสืบทอด (extends)** — สร้าง widget ใหม่จาก base class
* **การใช้ mixin** — เติมความสามารถเพิ่มเติมโดยไม่ต้องสืบทอดหลายชั้น
* **Composition** — รวมวิดเจ็ตย่อยเพื่อประกอบเป็น UI ที่ซับซ้อน

---

## 2.13 สรุปบทที่ 2

| หัวข้อ      | เนื้อหา                              |
| ----------- | ------------------------------------ |
| สถาปัตยกรรม | Flutter ใช้ Skia เรนเดอร์ UI ทั้งหมด |
| วิดเจ็ต     | ทุกอย่างเป็น widget                  |
| Layout      | ใช้ Row, Column, Stack, Container    |
| State       | setState, Provider, Riverpod, Bloc   |
| Navigation  | stack-based, push/pop                |
| API         | ทำงานผ่าน http หรือ dio              |

---

## 2.14 คำถามทบทวน

1. Flutter มีสถาปัตยกรรมกี่ชั้น และแต่ละชั้นทำหน้าที่อย่างไร?
2. ความแตกต่างระหว่าง StatelessWidget และ StatefulWidget คืออะไร?
3. เหตุใดการจัดการสถานะระดับแอปจึงซับซ้อนกว่าการใช้ setState()?
4. Provider ช่วยแก้ปัญหาใดในโครงสร้าง widget tree?
5. Navigator ใน Flutter ทำงานใกล้เคียงกับโครงสร้างข้อมูลใด?

