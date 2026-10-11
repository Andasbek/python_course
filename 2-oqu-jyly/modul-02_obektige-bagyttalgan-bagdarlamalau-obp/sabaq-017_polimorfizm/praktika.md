# Сабақ 17. Практикалық тапсырмалар: полиморфизм және абстракция

## Базалық деңгей

### 1-тапсырма. Музыкалық аспаптар
`Dombyra`, `Piano`, `Drum` кластарын құрыңыз, әрқайсысында `play()` әдісі өз дыбысын қайтарсын («Домбыра: дың-дың», т.б.). Үш аспаптың тізімін жасап, бір цикл арқылы оркестрді «ойнатыңыз».

Мысал шығару:
```text
Домбыра: дың-дың
Фортепиано: до-ре-ми
Барабан: дүң-дүң
```

### 2-тапсырма. Жалақы есептеу
`Employee(name, base)` ата-класында `salary()` әдісі `base` қайтарсын. Ұрпақтар:
- `Manager` — `base + 50000` (бонус);
- `Intern` — `base` жартысы;
- `Developer` — `base * 1.5`.

Барлық қызметкерлердің тізімін жасап, әрқайсысының жалақысын және жалпы жалақы қорын шығарыңыз. Циклде `if` / `type()` қолданбаңыз!

### 3-тапсырма. Полиморфизмді табыңыз
Төмендегі кодтың нәтижесін алдын ала дәптерге жазыңыз, содан кейін тексеріңіз:
```python
items = ["Python", [10, 20, 30], (1, 2), {"x": 1}]
for it in items:
    print(type(it).__name__, len(it))
print(3 * "ab", 3 * [0])
```

### 4-тапсырма. Абстрактілі «Жануар»
`abc` модулін пайдаланып `Animal(ABC)` абстрактілі класын жасаңыз: абстрактілі әдістер `speak()` және `move()`. `Horse` және `Eagle` кластарында оларды іске асырыңыз. `Animal()` объектісін құруға тырысып, қатені `try/except` арқылы ұстаңыз.

## Кеңейтілген деңгей

### 5-тапсырма. Хабарлама жіберу жүйесі
Абстрактілі `Notifier(ABC)` класы: абстрактілі `send(text)` әдісі және кәдімгі `log(text)` әдісі (`"[LOG] ..."` шығарады). Нақты кластар: `SMSNotifier(phone)`, `EmailNotifier(email)`, `TelegramNotifier(username)`. Әрқайсысы өз форматында хабарлайды. `broadcast(notifiers, text)` функциясы тізімдегі барлық тәсілмен хабар жіберсін.

Мысал шығару:
```text
SMS +77011234567: Ертең сабақ 15:00-де
Email aru@mail.kz: Ертең сабақ 15:00-де
Telegram @erlan_kz: Ертең сабақ 15:00-де
```

### 6-тапсырма. Duck typing: файл тәрізді объектілер
`ConsoleWriter` (жазуды экранға шығарады) және `MemoryWriter` (жазуды ішкі тізімге сақтайды) кластарын жасаңыз. Екеуінде де `write(text)` әдісі болсын. `report(writer)` функциясы кез келген «жазушыға» 3 жол жазсын. Ортақ ата-класс қолданбаңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 7-тапсырма. Фигуралар реттеушісі
Абстрактілі `Shape` класы (`area`, `perimeter`) және кемінде 4 нақты фигура (`Circle`, `Rectangle`, `Square`, `Triangle` — үшбұрыш үш қабырғасымен, ауданы Герон формуласымен) жасаңыз. Пайдаланушы фигураларды енгізеді (мысалы `circle 3`, `rect 4 5`, `tri 3 4 5`, `stop`). Бағдарлама:
1. фигураларды ауданы бойынша кему ретімен сұрыптап шығарсын (`sorted(..., key=lambda s: s.area(), reverse=True)`);
2. ең үлкен периметрлі фигураны көрсетсін.

## Үй тапсырмасы

1. «ОБП-ның 4 принципі» тақырыбына өз мысалдарыңызбен кесте жасаңыз (әр принципке Python коды + өмірлік аналогия).
2. 5-тапсырманы аяқтап, GitHub репозиторийіңізге `polymorphism.py` деп жүктеңіз.
3. Ойланыңыз: `print(obj)` жазғанда объект неге `<__main__.Dog object at 0x...>` болып шығады? Мұны қалай өзгертуге болады?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
class Dombyra:
    def play(self):
        return "Домбыра: дың-дың"

class Piano:
    def play(self):
        return "Фортепиано: до-ре-ми"

class Drum:
    def play(self):
        return "Барабан: дүң-дүң"

for instrument in [Dombyra(), Piano(), Drum()]:
    print(instrument.play())
```

**2-тапсырма**
```python
class Employee:
    def __init__(self, name, base):
        self.name = name
        self.base = base

    def salary(self):
        return self.base

class Manager(Employee):
    def salary(self):
        return self.base + 50000

class Intern(Employee):
    def salary(self):
        return self.base // 2

class Developer(Employee):
    def salary(self):
        return int(self.base * 1.5)

staff = [Manager("Асан", 400000), Intern("Үсен", 200000),
         Developer("Әлия", 500000), Employee("Бақыт", 250000)]
fund = 0
for e in staff:
    print(f"{e.name}: {e.salary()} тг")
    fund += e.salary()
print("Жалақы қоры:", fund, "тг")
```

**3-тапсырма.** Жауабы:
```text
str 6
list 3
tuple 2
dict 1
ababab [0, 0, 0]
```

**4-тапсырма**
```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass

    @abstractmethod
    def move(self):
        pass

class Horse(Animal):
    def speak(self):
        print("Жылқы: Иһаһа!")

    def move(self):
        print("Жылқы шауып барады")

class Eagle(Animal):
    def speak(self):
        print("Бүркіт: Кий-кий!")

    def move(self):
        print("Бүркіт қалықтап ұшып жүр")

for a in [Horse(), Eagle()]:
    a.speak()
    a.move()

try:
    Animal()
except TypeError as e:
    print("Қате:", e)
```

**5-тапсырма**
```python
from abc import ABC, abstractmethod

class Notifier(ABC):
    @abstractmethod
    def send(self, text):
        pass

    def log(self, text):
        print(f"[LOG] {text}")

class SMSNotifier(Notifier):
    def __init__(self, phone):
        self.phone = phone

    def send(self, text):
        print(f"SMS {self.phone}: {text}")

class EmailNotifier(Notifier):
    def __init__(self, email):
        self.email = email

    def send(self, text):
        print(f"Email {self.email}: {text}")

class TelegramNotifier(Notifier):
    def __init__(self, username):
        self.username = username

    def send(self, text):
        print(f"Telegram @{self.username}: {text}")

def broadcast(notifiers, text):
    for n in notifiers:
        n.send(text)
    notifiers[0].log(f"{len(notifiers)} арна арқылы жіберілді")

broadcast([SMSNotifier("+77011234567"), EmailNotifier("aru@mail.kz"),
           TelegramNotifier("erlan_kz")], "Ертең сабақ 15:00-де")
```

**6-тапсырма**
```python
class ConsoleWriter:
    def write(self, text):
        print(text)

class MemoryWriter:
    def __init__(self):
        self.lines = []

    def write(self, text):
        self.lines.append(text)

def report(writer):
    writer.write("Есеп басталды")
    writer.write("Оқушылар саны: 15")
    writer.write("Есеп аяқталды")

report(ConsoleWriter())
mem = MemoryWriter()
report(mem)
print("Жадта:", mem.lines)
```

**7-тапсырма**
```python
from abc import ABC, abstractmethod
import math

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

class Circle(Shape):
    def __init__(self, r):
        self.r = r
        self.name = f"Шеңбер(r={r})"
    def area(self):
        return math.pi * self.r ** 2
    def perimeter(self):
        return 2 * math.pi * self.r

class Rectangle(Shape):
    def __init__(self, a, b):
        self.a, self.b = a, b
        self.name = f"Тіктөртбұрыш({a}x{b})"
    def area(self):
        return self.a * self.b
    def perimeter(self):
        return 2 * (self.a + self.b)

class Square(Rectangle):
    def __init__(self, a):
        super().__init__(a, a)
        self.name = f"Шаршы({a})"

class Triangle(Shape):
    def __init__(self, a, b, c):
        self.a, self.b, self.c = a, b, c
        self.name = f"Үшбұрыш({a},{b},{c})"
    def area(self):
        p = self.perimeter() / 2
        return math.sqrt(p * (p - self.a) * (p - self.b) * (p - self.c))
    def perimeter(self):
        return self.a + self.b + self.c

shapes = []
while True:
    parts = input("Фигура (stop — тоқтау): ").split()
    if not parts or parts[0] == "stop":
        break
    kind, nums = parts[0], [float(x) for x in parts[1:]]
    if kind == "circle":
        shapes.append(Circle(*nums))
    elif kind == "rect":
        shapes.append(Rectangle(*nums))
    elif kind == "square":
        shapes.append(Square(*nums))
    elif kind == "tri":
        shapes.append(Triangle(*nums))

for s in sorted(shapes, key=lambda s: s.area(), reverse=True):
    print(f"{s.name}: S = {s.area():.2f}, P = {s.perimeter():.2f}")
if shapes:
    big = max(shapes, key=lambda s: s.perimeter())
    print("Ең үлкен периметр:", big.name)
```
(Мұнда фигура түрін тексеретін `if` тек объектіні **құру** кезінде ғана бар — өңдеу толық полиморфты.)

</details>
