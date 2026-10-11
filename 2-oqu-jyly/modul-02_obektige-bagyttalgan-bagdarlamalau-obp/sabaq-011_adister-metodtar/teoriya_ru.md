# Урок 11. Методы. Роль параметра self.

## Темы

1. Что такое метод?
2. Роль параметра `self`
3. Виды методов (по назначению)
4. Вызов другого метода внутри метода
5. Методы и состояние объекта: пример «лампа»
6. Цепочки методов (chaining)
7. Сравнение функции и метода
8. Частые ошибки
9. Основные понятия

## 1. Что такое метод?

**Метод** (method) — функция, объявленная внутри класса и работающая с атрибутами объекта. Если атрибуты описывают, **что объект знает**, то методы описывают, **что он умеет делать**.

```python
class Rectangle:
    """Прямоугольник."""

    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        """Возвращает площадь."""
        return self.width * self.height

    def perimeter(self):
        """Возвращает периметр."""
        return 2 * (self.width + self.height)


r = Rectangle(3, 4)
print(r.area(), r.perimeter())
r.width = 10
print(r.area(), r.perimeter())
```
Результат:
```text
12 14
40 28
```
Теперь площадь при каждом вызове вычисляется по **текущей** ширине.

Структура метода:
```text
class Rectangle:
    def area(self):          ← def + имя + первый параметр self
        """docstring"""
        return self.width * self.height   ← доступ к атрибутам через self

r.area()                     ← объект.метод() — со скобками!
```

## 2. Роль параметра `self`

### Проблема: с каким объектом работает метод?
Метод пишется в классе **один раз**, а объектов могут быть тысячи. Когда метод `area()` говорит «моя ширина» — ширину **какого** прямоугольника брать?

Ответ — `self`. При вызове метода Python автоматически передаёт **объект, стоящий перед точкой**, в качестве первого аргумента:

```text
r1.area()   ──Python──►   Rectangle.area(r1)    →   self = r1
r2.area()   ──Python──►   Rectangle.area(r2)    →   self = r2
```

Докажем:
```python
r1 = Rectangle(2, 5)
r2 = Rectangle(7, 7)
print(r1.area(), Rectangle.area(r1))
print(r2.area(), Rectangle.area(r2))
```
Результат:
```text
10 10
49 49
```
Обе записи — одинаковы. Но `r1.area()` короче и понятнее, поэтому всегда пишем так.

### Аналогии
- **Пульт и телевизор.** Кнопка «прибавить звук» работает одинаково для всех телевизоров. Но когда ты направляешь пульт на **свой** телевизор, громче становится только он. `self` — «телевизор, на который направлен пульт».
- **Слово «я».** В классе каждый ученик говорит «меня зовут ...». Слово «я» общее для всех, но в устах каждого — это сам этот человек. `self` — это «я» объекта.

### Правила о `self`
1. `self` — **первый** параметр каждого метода (даже если других параметров нет).
2. При вызове аргумент для `self` **не передаём** — Python передаёт его сам.
3. Внутри метода к атрибутам и другим методам объекта обращаемся **только** через `self.`.
4. `self` — не ключевое слово, а соглашение (конвенция). Можно назвать иначе, но **никогда** так не делай — все программисты на Python пишут `self`.

```python
class Counter:
    """Счётчик."""

    def __init__(self):
        self.value = 0

    def click(self):
        """Увеличивает значение на 1."""
        self.value += 1
        print(f"id={id(self)}, значение={self.value}")


c1 = Counter()
c2 = Counter()
c1.click()
c1.click()
c2.click()
print(id(c1) == id(c2))
```
Результат (числа id будут другими):
```text
id=140234, значение=1
id=140234, значение=2
id=140987, значение=1
False
```
id у `self` совпадает с id вызвавшего объекта: при `c1.click()` `self` — это `c1`, при `c2.click()` — `c2`.

## 3. Виды методов (по назначению)

| Вид | Что делает | Пример |
|---|---|---|
| Метод-запрос (query) | вычисляет по атрибутам и **возвращает** значение, состояние не меняет | `area()`, `average()`, `is_empty()` |
| Метод-команда (command) | **изменяет состояние** объекта | `deposit()`, `move()`, `add_mark()` |
| Метод вывода | выводит информацию на экран | `show()`, `print_info()` |

Хороший стиль: метод должен **либо** возвращать значение, **либо** менять состояние — лучше их не смешивать (но возврат `True/False` — «успешно ли» — частое исключение).

### Методы с параметрами
```python
class BankAccount:
    """Банковский счёт."""

    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance
        self.history = []

    def deposit(self, amount):
        """Пополняет счёт."""
        if amount <= 0:
            print("Сумма должна быть положительной")
            return
        self.balance += amount
        self.history.append(f"+{amount}")

    def withdraw(self, amount):
        """Снимает деньги со счёта. Возвращает True при успехе."""
        if amount > self.balance:
            print(f"{self.owner}: недостаточно средств (баланс {self.balance} ₸)")
            return False
        self.balance -= amount
        self.history.append(f"-{amount}")
        return True

    def show(self):
        """Выводит информацию о счёте."""
        print(f"{self.owner}: {self.balance} ₸, операции: {self.history}")


dana = BankAccount("Дана", 5000)
dana.deposit(15000)
dana.withdraw(3000)
dana.withdraw(50000)
dana.show()
```
Результат:
```text
Дана: недостаточно средств (баланс 17000 ₸)
Дана: 17000 ₸, операции: ['+15000', '-3000']
```

Обрати внимание: если изменить баланс напрямую `dana.balance -= 50000`, проверки не будет — баланс станет отрицательным. Изменение через метод `withdraw()` — **безопасно**. Это основа инкапсуляции.

### Метод, принимающий объект как параметр
Метод может работать и с **другим** объектом:

```python
class BankAccount:
    """Банковский счёт (с возможностью перевода)."""

    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance

    def transfer(self, other, amount):
        """Переводит деньги на другой счёт (other)."""
        if amount > self.balance:
            print("Перевод невозможен")
            return False
        self.balance -= amount
        other.balance += amount
        print(f"{self.owner} → {other.owner}: {amount} ₸")
        return True


aliya = BankAccount("Алия", 10000)
erlan = BankAccount("Ерлан", 2000)
aliya.transfer(erlan, 4000)
print(aliya.balance, erlan.balance)
```
Результат:
```text
Алия → Ерлан: 4000 ₸
6000 6000
```
Здесь `self` — отправитель денег, `other` — получатель.

## 4. Вызов другого метода внутри метода

Методы могут вызывать друг друга через `self.` — разбиваем большое действие на маленькие части:

```python
class Student:
    """Учащийся кружка."""

    def __init__(self, name):
        self.name = name
        self.marks = []

    def add_mark(self, mark):
        """Добавляет оценку (только от 1 до 5)."""
        if 1 <= mark <= 5:
            self.marks.append(mark)
        else:
            print(f"Неверная оценка: {mark}")

    def average(self):
        """Возвращает средний балл."""
        if not self.marks:
            return 0
        return sum(self.marks) / len(self.marks)

    def is_excellent(self):
        """True, если средний балл не ниже 4.5."""
        return self.average() >= 4.5

    def report(self):
        """Возвращает строку-отчёт об ученике."""
        status = "отличник" if self.is_excellent() else "хорошо учится"
        return f"{self.name}: {self.average():.2f} ({status})"


aliya = Student("Алия")
for mark in [5, 4, 5, 7, 5]:
    aliya.add_mark(mark)
print(aliya.report())
```
Результат:
```text
Неверная оценка: 7
Алия: 4.75 (отличник)
```
`report()` → `is_excellent()` → `average()` — каждый метод выполняет одну задачу (принцип «одна функция — одна задача»).

## 5. Методы и состояние объекта: пример «лампа»

```python
class Lamp:
    """Лампа с регулируемой яркостью."""

    def __init__(self, room):
        self.room = room
        self.is_on = False
        self.brightness = 0

    def turn_on(self):
        """Включает лампу (на 50% яркости)."""
        self.is_on = True
        self.brightness = 50

    def turn_off(self):
        """Выключает лампу."""
        self.is_on = False
        self.brightness = 0

    def set_brightness(self, level):
        """Устанавливает яркость в диапазоне 0–100."""
        if not self.is_on:
            print(f"{self.room}: сначала включи лампу!")
            return
        self.brightness = max(0, min(100, level))

    def status(self):
        """Возвращает состояние в виде текста."""
        if self.is_on:
            return f"{self.room}: включена, {self.brightness}%"
        return f"{self.room}: выключена"


kitchen = Lamp("Кухня")
kitchen.set_brightness(80)
kitchen.turn_on()
kitchen.set_brightness(150)
print(kitchen.status())
kitchen.turn_off()
print(kitchen.status())
```
Результат:
```text
Кухня: сначала включи лампу!
Кухня: включена, 100%
Кухня: выключена
```
Методы соблюдают **правила** объекта: яркость выключенной лампы менять нельзя, яркость не превышает 100.

## 6. Цепочки методов (chaining)

Если метод-команда возвращает `self`, методы можно вызывать цепочкой в одной строке:

```python
class Robot:
    """Робот, перемещающийся по сетке."""

    def __init__(self):
        self.x = 0
        self.y = 0

    def up(self, steps=1):
        self.y += steps
        return self

    def right(self, steps=1):
        self.x += steps
        return self

    def position(self):
        return (self.x, self.y)


bot = Robot()
print(bot.up(3).right(2).up().position())
```
Результат:
```text
(2, 4)
```

## 7. Сравнение функции и метода

| | Функция | Метод |
|---|---|---|
| Где объявляется | в модуле (вне класса) | внутри класса |
| Первый параметр | любой | `self` |
| Вызов | `area(r)` | `r.area()` |
| Доступ к данным | через параметры | к атрибутам через `self` |
| Примеры | `len(x)`, `print()`, `max()` | `s.upper()`, `lst.append()`, `d.get()` |

## Частые ошибки

**1. Забыли параметр `self` в методе.**
```python
class Dog:
    def bark():                 # нет self
        print("Гав-гав!")


rex = Dog()
# rex.bark()   # TypeError: Dog.bark() takes 0 positional arguments but 1 was given
```
Python передаёт `rex` как аргумент, но метод его не принимает. Правильно: `def bark(self):`.

**2. Обращение к атрибуту без `self`.**
```python
class Circle:
    def __init__(self, r):
        self.r = r

    def area(self):
        return 3.14 * r ** 2    # NameError: name 'r' is not defined
```
Правильно: `self.r`.

**3. Вызов метода без скобок.**
```python
r = Rectangle(3, 4)
print(r.area)     # <bound method Rectangle.area of ...> — не вызван!
print(r.area())   # 12
```

**4. Вызов другого метода внутри метода без `self`.**
Если написать `average()` — будет `NameError`. Правильно: `self.average()`.

**5. Использование результата метода, который ничего не возвращает.**
```python
acc = BankAccount("Дана", 100)
result = acc.show()   # show() делает print, но return нет
print(result)         # None
```

**6. Одинаковое имя у метода и атрибута.**
`self.area = 12` и `def area(self)` — атрибут «перекрывает» метод: `r.area()` → `TypeError: 'int' object is not callable`.

## Основные понятия

| Понятие | Пояснение |
|---|---|
| Метод | Функция внутри класса, описывающая действие объекта |
| `self` | Ссылка на объект, вызвавший метод |
| Поведение (behavior) | Набор методов объекта |
| Метод-запрос | Метод, возвращающий значение и не меняющий состояние |
| Метод-команда | Метод, изменяющий состояние объекта |
| Связанный метод (bound method) | Метод, «привязанный» к конкретному объекту: `r.area` |
| Цепочка (chaining) | Вызов методов в одной строке за счёт возврата `self` |

## Примечание

- `r.area` — объект «связанный метод» (bound method): его можно сохранить в переменную и вызвать позже: `f = r.area; print(f())`.
- Бывают методы, вызываемые не через `self`, а через класс: `@classmethod` (первый параметр — `cls`, сам класс) и `@staticmethod` (нет ни `self`, ни `cls`). Их применяют для действий на уровне класса и вспомогательных функций.
- «Специальные» (dunder) методы — `__str__`, `__len__`, `__eq__` и др. — учат объекты работать со встроенными операциями `print()`, `len()`, `==`. Полный список: https://docs.python.org/3/reference/datamodel.html#special-method-names.
- По PEP 8 имена методов пишутся в `snake_case`, как у функций, а методы внутри класса разделяются одной пустой строкой.
- Не смешивай в методе `print` и `return`: метод, возвращающий значение, можно повторно использовать в графическом интерфейсе и в тестах, а метод с `print` — только в консоли.
