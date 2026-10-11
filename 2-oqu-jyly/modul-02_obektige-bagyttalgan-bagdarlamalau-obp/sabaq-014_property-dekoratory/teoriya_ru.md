# Урок 14. Декоратор property. Методы-геттеры и сеттеры.

## Темы

1. Первое знакомство с декоратором
2. `@property` — геттер
3. `@<имя>.setter` — сеттер
4. Реакция на ошибку: `raise` и `try/except`
5. Свойство только для чтения (read-only)
6. Вычисляемое свойство (computed property)
7. `@<имя>.deleter` (кратко)
8. Функция `property()` — альтернативная запись
9. Что и когда использовать?
10. Частые ошибки
11. Основные понятия

## 1. Первое знакомство с декоратором

**Декоратор** — функция, которая «оборачивает» другую функцию и добавляет ей новую возможность. Записывается над определением функции в виде `@имя`.

Аналогия: упаковка подарка в бумагу. Сам подарок не меняется, но «выглядит» и вручается по-другому.

```python
def shout(func):
    """Декоратор, возвращающий результат функции заглавными буквами и с восклицательным знаком."""
    def wrapper(name):
        return func(name).upper() + "!"
    return wrapper


@shout
def greet(name):
    return f"Привет, {name}"


print(greet("Ерлан"))
```
Результат:
```text
ПРИВЕТ, ЕРЛАН!
```
Запись `@shout` равносильна `greet = shout(greet)`. Для управления доступом к атрибутам используется **готовый** декоратор Python `@property`. `@staticmethod` — тоже готовый декоратор.

## 2. `@property` — геттер

`@property` позволяет читать метод **как атрибут**: вызывается без скобок.

```python
class Student:
    """Ученик."""

    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def age(self):
        """Возраст ученика."""
        return self._age


s = Student("Алия", 14)
print(s.age)        # без скобок! внутри вызван метод age()
# s.age = 15        # AttributeError: property 'age' of 'Student' object has no setter
```
Результат:
```text
14
```

Примечание: внутренние данные property обычно хранятся с **одним подчёркиванием** (`self._age`) — чтобы имя не конфликтовало с именем свойства (`age`). Можно использовать и `__age`, но в сообществе Python со свойствами чаще используют `_age`.

## 3. `@<имя>.setter` — сеттер

Метод, вызываемый при **присваивании** значения (`s.age = 15`):

```python
class Student:
    """Ученик (с проверкой возраста)."""
    MIN_AGE = 6
    MAX_AGE = 18

    def __init__(self, name, age):
        self._name = name
        self.age = age            # и в конструкторе через сеттер — проверка работает!

    @property
    def age(self):
        """Возраст ученика (6–18)."""
        return self._age

    @age.setter
    def age(self, value):
        if not isinstance(value, int):
            raise TypeError("Возраст должен быть целым числом")
        if not self.MIN_AGE <= value <= self.MAX_AGE:
            raise ValueError(f"Возраст должен быть в диапазоне {self.MIN_AGE}–{self.MAX_AGE}: {value}")
        self._age = value


s = Student("Алия", 14)
s.age += 1                 # чтение (геттер) + запись (сеттер)
print(s.age)
s.age = 40                 # ValueError
```
Результат:
```text
15
Traceback (most recent call last):
  ...
ValueError: Возраст должен быть в диапазоне 6–18: 40
```

Структура:
```text
@property                 ← геттер: obj.age
def age(self): ...

@age.setter               ← сеттер: obj.age = значение   (имя декоратора = имя свойства!)
def age(self, value): ...
```

Что произошло:
- Для **внешнего кода** `age` — обычный атрибут: `s.age`, `s.age = 15`, `s.age += 1`.
- **На самом деле** при каждом чтении/записи вызываются наши методы и выполняется проверка.

## 4. Реакция на ошибку: `raise` и `try/except`

Сеттер-метод может при неверном значении делать `print` и возвращать `False`. Но в property присваивание (`s.age = 40`) ничего не возвращает — поэтому профессиональный подход: **выбросить ошибку (исключение, exception)**.

| Исключение | Когда применяем |
|---|---|
| `ValueError` | тип правильный, но значение недопустимо (возраст = 40, оценка = 7) |
| `TypeError` | неверный тип (возраст = "четырнадцать") |

**Перехват** ошибки — `try/except`:
```python
try:
    s.age = 40
except ValueError as error:
    print("Ошибка:", error)

try:
    s.age = "четырнадцать"
except (ValueError, TypeError) as error:
    print("Ошибка:", error)
print("Программа продолжает работу, возраст:", s.age)
```
Результат:
```text
Ошибка: Возраст должен быть в диапазоне 6–18: 40
Ошибка: Возраст должен быть целым числом
Программа продолжает работу, возраст: 15
```
При ошибке сеттер не доходит до `self._age`, поэтому объект остаётся в **корректном состоянии**.

Вместе с вводом пользователя:
```python
while True:
    try:
        s.age = int(input("Введи свой возраст: "))
        break
    except ValueError as error:
        print("Введи заново:", error)
```
(`int("abc")` тоже вызывает `ValueError` — один `except` перехватывает оба случая.)

## 5. Свойство только для чтения (read-only)

Property без сеттера можно только читать. Баланс банковского счёта — классический пример:

```python
class BankAccount:
    """Банковский счёт: баланс только для чтения."""

    def __init__(self, owner):
        self.owner = owner
        self._balance = 0

    @property
    def balance(self):
        """Текущий баланс (только чтение)."""
        return self._balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Сумма должна быть положительной")
        self._balance += amount

    def withdraw(self, amount):
        if amount > self._balance:
            raise ValueError("Недостаточно средств")
        self._balance -= amount


acc = BankAccount("Дана")
acc.deposit(10_000)
acc.withdraw(2_500)
print(acc.balance)
try:
    acc.balance = 1_000_000
except AttributeError as error:
    print("Нельзя:", error)
```
Результат:
```text
7500
Нельзя: property 'balance' of 'BankAccount' object has no setter
```
(В Python 3.10 сообщение короче: `can't set attribute 'balance'`.)

## 6. Вычисляемое свойство (computed property)

Атрибут `area`, вычисленный в `__init__`, устаревает при изменении сторон; метод `area()` решает проблему, но требует скобок. Property — самое естественное решение: **площадь — это свойство прямоугольника**, но она вычисляется каждый раз заново.

```python
class Rectangle:
    """Прямоугольник."""

    def __init__(self, width, height):
        self.width = width
        self.height = height

    @property
    def width(self):
        return self._width

    @width.setter
    def width(self, value):
        if value <= 0:
            raise ValueError("Ширина должна быть положительной")
        self._width = value

    @property
    def height(self):
        return self._height

    @height.setter
    def height(self, value):
        if value <= 0:
            raise ValueError("Высота должна быть положительной")
        self._height = value

    @property
    def area(self):
        """Площадь — каждый раз вычисляется по текущим размерам."""
        return self._width * self._height

    @property
    def is_square(self):
        return self._width == self._height


r = Rectangle(3, 4)
print(r.area, r.is_square)
r.width = 4
print(r.area, r.is_square)
```
Результат:
```text
12 False
16 True
```

Вычисляемое свойство может иметь смысл и **с сеттером** — например, температура:
```python
class Temperature:
    """Температура: внутри хранится в градусах Цельсия."""

    def __init__(self, celsius=0.0):
        self.celsius = celsius

    @property
    def celsius(self):
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Не может быть ниже абсолютного нуля")
        self._celsius = value

    @property
    def fahrenheit(self):
        return self._celsius * 9 / 5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        self.celsius = (value - 32) * 5 / 9   # проверка — в сеттере celsius


t = Temperature(25)
print(t.fahrenheit)
t.fahrenheit = 212
print(t.celsius)
```
Результат:
```text
77.0
100.0
```
Данные хранятся **в одном месте** (`_celsius`), а два свойства — разные «представления» этих данных.

## 7. `@<имя>.deleter` (кратко)

Метод, вызываемый при `del obj.имя`, — применяется редко, например для очистки кэша или запрета «удаления»:

```python
class Profile:
    """Профиль пользователя."""

    def __init__(self, nickname):
        self._nickname = nickname

    @property
    def nickname(self):
        return self._nickname

    @nickname.deleter
    def nickname(self):
        print("Никнейм удалить нельзя — установлен 'гость'")
        self._nickname = "гость"


p = Profile("pythonist")
del p.nickname
print(p.nickname)
```
Результат:
```text
Никнейм удалить нельзя — установлен 'гость'
гость
```

## 8. Функция `property()` — альтернативная запись

Можно обойтись и без декоратора (встречается в старом коде):

```python
class Box:
    """Ящик."""

    def __init__(self, weight):
        self._weight = weight

    def get_weight(self):
        return self._weight

    def set_weight(self, value):
        if value < 0:
            raise ValueError("Вес не может быть отрицательным")
        self._weight = value

    weight = property(get_weight, set_weight)


b = Box(5)
b.weight = 12
print(b.weight)
```
Результат:
```text
12
```

## 9. Что и когда использовать?

| Ситуация | Выбор |
|---|---|
| Простые данные без правил (название книги) | открытый атрибут `self.title` |
| Значение, требующее проверки (возраст, оценка) | `@property` + `@setter` |
| Значение, которое нельзя менять снаружи (баланс, ID) | `@property` (без сеттера) |
| Значение, быстро вычисляемое из других атрибутов (площадь, средний балл) | вычисляемое `@property` |
| Долгое вычисление, с параметрами или «действие» (ходить, продать) | метод `obj.move(dx)` |

**Подход Python:** сначала пиши простой открытый атрибут. Если позже понадобится проверка — преврати его в property, и внешний код (`obj.age`) **не изменится**. Писать заранее `get_x()/set_x()` к каждому атрибуту, как в Java, в Python не принято.

Правило: property должно быть **быстрым** и **без побочных эффектов** — когда программист пишет `obj.area`, не должен записываться файл или уходить запрос в интернет.

## Частые ошибки

**1. Запись в атрибут с тем же именем, что и свойство, внутри сеттера — бесконечная рекурсия.**
```python
class Student:
    @property
    def age(self):
        return self.age          # вызывает сам себя!

    @age.setter
    def age(self, value):
        self.age = value         # сеттер вызывает сам себя!
```
`RecursionError: maximum recursion depth exceeded`. Решение: внутренний атрибут под другим именем — `self._age`.

**2. Неверное имя декоратора сеттера.**
Если вместо `@age.setter` написать `@setter` или `@property.setter` → `NameError`/`AttributeError`. Имя метода-сеттера тоже должно **совпадать** с именем свойства (`def age(self, value)`).

**3. Вызов свойства со скобками, как метода.**
```text
r.area()  →  TypeError: 'int' object is not callable
```
Property — без скобок: `r.area`.

**4. Обход сеттера в конструкторе.** Если написать `self._age = age`, в конструкторе проверки не будет: `Student("Алия", 100)` будет создан. Правильно: `self.age = age`.

**5. `print` + `return False` в сеттере.** Результат присваивания никто не проверяет — неверное значение «молча» не принимается, и пользователь этого не замечает. Профессиональный подход — `raise ValueError(...)`.

**6. Написать только геттер и «забыть» сеттер.** Если атрибут нужно изменять — `AttributeError: ... has no setter`. Property без сеттера — только когда read-only сделано **намеренно**.

## Основные понятия

| Понятие | Пояснение |
|---|---|
| Декоратор | Средство, «оборачивающее» функцию и добавляющее ей возможности, `@имя` |
| `@property` | Декоратор, позволяющий читать метод как атрибут (геттер) |
| `@x.setter` | Сеттер, вызываемый при `obj.x = значение` |
| `@x.deleter` | Метод, вызываемый при `del obj.x` |
| Свойство (property) | Снаружи — атрибут, внутри — методы |
| Read-only свойство | Свойство без сеттера, только для чтения |
| Вычисляемое свойство | Не хранится, вычисляется из других данных |
| Исключение (exception) | Сигнал об ошибке: `ValueError`, `TypeError` |
| `raise` | Выбросить исключение |
| `try / except` | Перехватить и обработать исключение |

## Примечание

- При написании собственного декоратора оборачивай внутреннюю функцию через `@functools.wraps(func)` — тогда сохранятся имя и docstring исходной функции.
- Для свойства, которое долго вычисляется, но не меняется, есть `functools.cached_property` (Python 3.8+): значение вычисляется при первом обращении и затем запоминается.
- Property определяется на уровне класса: создание `self.x = property(...)` внутри `__init__` работать не будет.
- Текст ошибки при присваивании свойству без сеттера зависит от версии: в Python 3.11+ — `property 'balance' of 'BankAccount' object has no setter`, в более старых — `can't set attribute`.
- Официальная документация: https://docs.python.org/3/library/functions.html#property и руководство по дескрипторам https://docs.python.org/3/howto/descriptor.html.
