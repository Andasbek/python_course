# Урок 17. Практические задания: полиморфизм и абстракция

## Базовый уровень

### Задание 1. Музыкальные инструменты
Создайте классы `Dombyra`, `Piano`, `Drum`, в каждом метод `play()` должен возвращать свой звук («Домбра: дың-дың» и т.д.). Составьте список из трёх инструментов и одним циклом «сыграйте» оркестром.

Пример вывода:
```text
Домбра: дың-дың
Фортепиано: до-ре-ми
Барабан: бум-бум
```

### Задание 2. Расчёт зарплаты
В родительском классе `Employee(name, base)` метод `salary()` возвращает `base`. Дочерние классы:
- `Manager` — `base + 50000` (бонус);
- `Intern` — половина `base`;
- `Developer` — `base * 1.5`.

Составьте список всех сотрудников, выведите зарплату каждого и общий фонд оплаты труда. Не используйте в цикле `if` / `type()`!

### Задание 3. Найдите полиморфизм
Сначала запишите в тетрадь результат кода ниже, затем проверьте:
```python
items = ["Python", [10, 20, 30], (1, 2), {"x": 1}]
for it in items:
    print(type(it).__name__, len(it))
print(3 * "ab", 3 * [0])
```

### Задание 4. Абстрактное «Животное»
С помощью модуля `abc` создайте абстрактный класс `Animal(ABC)`: абстрактные методы `speak()` и `move()`. Реализуйте их в классах `Horse` и `Eagle`. Попробуйте создать объект `Animal()` и перехватите ошибку с помощью `try/except`.

## Продвинутый уровень

### Задание 5. Система отправки уведомлений
Абстрактный класс `Notifier(ABC)`: абстрактный метод `send(text)` и обычный метод `log(text)` (выводит `"[LOG] ..."`). Конкретные классы: `SMSNotifier(phone)`, `EmailNotifier(email)`, `TelegramNotifier(username)`. Каждый отправляет сообщение в своём формате. Функция `broadcast(notifiers, text)` должна отправить сообщение всеми способами из списка.

Пример вывода:
```text
SMS +77011234567: Завтра урок в 15:00
Email aru@mail.kz: Завтра урок в 15:00
Telegram @erlan_kz: Завтра урок в 15:00
```

### Задание 6. Duck typing: файлоподобные объекты
Создайте классы `ConsoleWriter` (выводит запись на экран) и `MemoryWriter` (сохраняет запись во внутренний список). У обоих должен быть метод `write(text)`. Функция `report(writer)` пусть записывает 3 строки в любой «писатель». Общий родительский класс не используйте.

## ★ Олимпиадная / творческая задача

### Задание 7. Упорядочиватель фигур
Создайте абстрактный класс `Shape` (`area`, `perimeter`) и не менее 4 конкретных фигур (`Circle`, `Rectangle`, `Square`, `Triangle` — треугольник по трём сторонам, площадь по формуле Герона). Пользователь вводит фигуры (например, `circle 3`, `rect 4 5`, `tri 3 4 5`, `stop`). Программа должна:
1. вывести фигуры, отсортированные по убыванию площади (`sorted(..., key=lambda s: s.area(), reverse=True)`);
2. показать фигуру с наибольшим периметром.

## Домашнее задание

1. Составьте таблицу на тему «4 принципа ООП» со своими примерами (для каждого принципа — код на Python + жизненная аналогия).
2. Завершите задание 5 и загрузите в свой репозиторий GitHub под именем `polymorphism.py`.
3. Подумайте: почему при `print(obj)` объект выводится как `<__main__.Dog object at 0x...>`? Как это можно изменить?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
class Dombyra:
    def play(self):
        return "Домбра: дың-дың"

class Piano:
    def play(self):
        return "Фортепиано: до-ре-ми"

class Drum:
    def play(self):
        return "Барабан: бум-бум"

for instrument in [Dombyra(), Piano(), Drum()]:
    print(instrument.play())
```

**Задание 2**
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
print("Фонд оплаты труда:", fund, "тг")
```

**Задание 3.** Ответ:
```text
str 6
list 3
tuple 2
dict 1
ababab [0, 0, 0]
```

**Задание 4**
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
        print("Лошадь: И-го-го!")

    def move(self):
        print("Лошадь скачет галопом")

class Eagle(Animal):
    def speak(self):
        print("Беркут: Кий-кий!")

    def move(self):
        print("Беркут парит в небе")

for a in [Horse(), Eagle()]:
    a.speak()
    a.move()

try:
    Animal()
except TypeError as e:
    print("Ошибка:", e)
```

**Задание 5**
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
    notifiers[0].log(f"Отправлено по {len(notifiers)} каналам")

broadcast([SMSNotifier("+77011234567"), EmailNotifier("aru@mail.kz"),
           TelegramNotifier("erlan_kz")], "Завтра урок в 15:00")
```

**Задание 6**
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
    writer.write("Отчёт начат")
    writer.write("Количество учеников: 15")
    writer.write("Отчёт завершён")

report(ConsoleWriter())
mem = MemoryWriter()
report(mem)
print("В памяти:", mem.lines)
```

**Задание 7**
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
        self.name = f"Круг(r={r})"
    def area(self):
        return math.pi * self.r ** 2
    def perimeter(self):
        return 2 * math.pi * self.r

class Rectangle(Shape):
    def __init__(self, a, b):
        self.a, self.b = a, b
        self.name = f"Прямоугольник({a}x{b})"
    def area(self):
        return self.a * self.b
    def perimeter(self):
        return 2 * (self.a + self.b)

class Square(Rectangle):
    def __init__(self, a):
        super().__init__(a, a)
        self.name = f"Квадрат({a})"

class Triangle(Shape):
    def __init__(self, a, b, c):
        self.a, self.b, self.c = a, b, c
        self.name = f"Треугольник({a},{b},{c})"
    def area(self):
        p = self.perimeter() / 2
        return math.sqrt(p * (p - self.a) * (p - self.b) * (p - self.c))
    def perimeter(self):
        return self.a + self.b + self.c

shapes = []
while True:
    parts = input("Фигура (stop — остановить): ").split()
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
    print("Наибольший периметр:", big.name)
```
(Здесь `if`, проверяющий вид фигуры, есть только при **создании** объекта — обработка полностью полиморфна.)

</details>
