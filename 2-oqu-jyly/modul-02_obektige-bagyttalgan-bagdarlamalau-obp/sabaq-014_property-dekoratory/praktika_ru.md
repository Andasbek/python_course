# Урок 14. Практические задания: property, геттеры и сеттеры

## Базовый уровень

### Задание 1. От `get/set` к `property`
Перепиши класс `Student` с прошлого урока (`get_age()`, `set_age()`, `get_name()`) с помощью `@property`:
- `name` — только для чтения;
- `age` — диапазон 6–18, при ошибке `ValueError`, если не целое число — `TypeError`;
- конструктор должен проверять значение через сеттер.

Код для проверки:
```python
s = Student("Алия", 14)
s.age += 1
print(s.name, s.age)
for bad in (40, "десять", 5):
    try:
        s.age = bad
    except (ValueError, TypeError) as error:
        print("Ошибка:", error)
```
```text
Алия 15
Ошибка: Возраст должен быть в диапазоне 6–18: 40
Ошибка: Возраст должен быть целым числом
Ошибка: Возраст должен быть в диапазоне 6–18: 5
```

### Задание 2. `Circle` — вычисляемые свойства
`Circle(radius)`: `radius` — property (положительное число), `diameter` — property **с сеттером** (изменение диаметра меняет радиус), `area` и `circumference` — только для чтения.

```text
c = Circle(5) → d = 10, S = 78.54
c.diameter = 4 → r = 2.0, S = 12.57
```

### Задание 3. `Product` — цена и скидка
`Product(name, price, discount=0)`:
- `price` — неотрицательная;
- `discount` — в диапазоне 0–90 (%);
- `final_price` — только для чтения: цена со скидкой (округлённая до целого);
- `name` — в сеттере удаляются лишние пробелы, если строка пустая — `ValueError`.

```text
Кроссовки: 45000 ₸, скидка 20% → 36000 ₸
```

### Задание 4. Свойства только для чтения
Класс `Order` (заказ): `id` (автоматически через счётчик класса, только чтение), `created` (`datetime.now()`, только чтение), `items` (property, возвращающее **копию** списка), `add_item(name, price)`, `total` (только чтение). Покажи с помощью `try/except`, что действия `order.id = 5` и `order.total = 0` вызывают `AttributeError`.

## Продвинутый уровень

### Задание 5. `Temperature` — три шкалы
Класс `Temperature` хранит внутри только кельвины (`_kelvin ≥ 0`), но у всех трёх свойств `celsius`, `fahrenheit`, `kelvin` есть **геттер и сеттер**. Значение, установленное в любой шкале, должно правильно отображаться в двух других.

```text
t = Temperature(); t.celsius = 36.6
t.fahrenheit → 97.88, t.kelvin → 309.75
t.fahrenheit = -500 → ValueError: Ниже абсолютного нуля
```

### Задание 6. `Password` — проверка сложности
`Account(login, password)`: у свойства `password` **нет геттера** (читать нельзя — геттер должен выбрасывать `AttributeError`), сеттер проверяет требования: минимум 8 символов, хотя бы одна цифра, одна заглавная буква, одна строчная буква; хранится только хеш. Метод `check(password)`. `strength` — свойство только для чтения: «слабый/средний/сильный» (в зависимости от длины и видов символов).

### Задание 7. `Clock` — часы
`Clock(hours, minutes)`: `hours` (0–23), `minutes` (0–59) — property; если в сеттер `minutes` передано больше 59, излишек переносится в часы (`minutes = 135` → +2 ч 15 мин); `time_str` — формат `"09:05"` (только чтение); метод `add_minutes(n)`.

## ★ Олимпиадная / творческая задача

### Задание 8. Кэшируемое свойство
Класс `Polygon` хранит список вершин (`points`, кортежи `(x, y)`). `area` (формула Гаусса / «шнурования») и `perimeter` — вычисляемые property, но они **кэшируются**: вычисляются один раз и сохраняются, а при вызове сеттера `points` или `add_point()` кэш очищается. Докажи работу кэша счётчиком вычислений. (Подсказка: `self._cache = {}`; дополнительно — прочитай про `functools.cached_property` и объясни разницу.)

```text
Квадрат (0,0),(4,0),(4,4),(0,4): площадь 16.0, периметр 16.0
area прочитана 3 раза, вычислений: 1
```

### Задание 9. Свой декоратор
Напиши декоратор `timer`: он выводит время выполнения функции (`time.perf_counter`). Примени его к двум функциям, находящим простые числа от 1 до `n` (простой перебор и решето Эратосфена), и сравни их скорость при `n = 50 000`.

## Домашнее задание

1. Создай в классах своего проекта хотя бы одно проверяемое property и одно вычисляемое property; сделай push.
2. Добавь в контейнер `Classroom` из 12-го урока вычисляемые свойства `average` и `size` (вместо методов).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
class Student:
    """Ученик."""
    MIN_AGE = 6
    MAX_AGE = 18

    def __init__(self, name, age):
        self._name = name
        self.age = age

    @property
    def name(self):
        return self._name

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if not isinstance(value, int):
            raise TypeError("Возраст должен быть целым числом")
        if not self.MIN_AGE <= value <= self.MAX_AGE:
            raise ValueError(f"Возраст должен быть в диапазоне {self.MIN_AGE}–{self.MAX_AGE}: {value}")
        self._age = value


s = Student("Алия", 14)
s.age += 1
print(s.name, s.age)
for bad in (40, "десять", 5):
    try:
        s.age = bad
    except (ValueError, TypeError) as error:
        print("Ошибка:", error)
```

**Задание 2**
```python
import math


class Circle:
    """Окружность."""

    def __init__(self, radius):
        self.radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value <= 0:
            raise ValueError("Радиус должен быть положительным")
        self._radius = value

    @property
    def diameter(self):
        return self._radius * 2

    @diameter.setter
    def diameter(self, value):
        self.radius = value / 2

    @property
    def area(self):
        return math.pi * self._radius ** 2

    @property
    def circumference(self):
        return 2 * math.pi * self._radius


c = Circle(5)
print(f"d = {c.diameter}, S = {c.area:.2f}")
c.diameter = 4
print(f"r = {c.radius}, S = {c.area:.2f}")
```

**Задание 3**
```python
class Product:
    """Товар со скидкой."""

    def __init__(self, name, price, discount=0):
        self.name = name
        self.price = price
        self.discount = discount

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        value = value.strip()
        if not value:
            raise ValueError("Название не может быть пустым")
        self._name = value

    @property
    def price(self):
        return self._price

    @price.setter
    def price(self, value):
        if value < 0:
            raise ValueError("Цена не может быть отрицательной")
        self._price = value

    @property
    def discount(self):
        return self._discount

    @discount.setter
    def discount(self, value):
        if not 0 <= value <= 90:
            raise ValueError("Скидка в диапазоне 0–90%")
        self._discount = value

    @property
    def final_price(self):
        return round(self._price * (100 - self._discount) / 100)


p = Product(" Кроссовки ", 45000, 20)
print(f"{p.name}: {p.price} ₸, скидка {p.discount}% → {p.final_price} ₸")
```

**Задание 4**
```python
from datetime import datetime


class Order:
    """Заказ."""
    _counter = 0

    def __init__(self):
        Order._counter += 1
        self._id = Order._counter
        self._created = datetime.now()
        self._items = []

    @property
    def id(self):
        return self._id

    @property
    def created(self):
        return self._created

    @property
    def items(self):
        return list(self._items)

    @property
    def total(self):
        return sum(price for _, price in self._items)

    def add_item(self, name, price):
        if price < 0:
            raise ValueError("Цена не может быть отрицательной")
        self._items.append((name, price))


order = Order()
order.add_item("Пицца", 3500)
order.add_item("Сок", 800)
print(order.id, order.created.strftime("%d.%m.%Y %H:%M"), order.items, order.total)
for attr, value in (("id", 5), ("total", 0)):
    try:
        setattr(order, attr, value)
    except AttributeError as error:
        print("Нельзя:", error)
```

**Задание 5**
```python
class Temperature:
    """Температура (внутри — в кельвинах)."""

    def __init__(self, kelvin=273.15):
        self.kelvin = kelvin

    @property
    def kelvin(self):
        return self._kelvin

    @kelvin.setter
    def kelvin(self, value):
        if value < 0:
            raise ValueError("Ниже абсолютного нуля")
        self._kelvin = value

    @property
    def celsius(self):
        return self._kelvin - 273.15

    @celsius.setter
    def celsius(self, value):
        self.kelvin = value + 273.15

    @property
    def fahrenheit(self):
        return self.celsius * 9 / 5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        self.celsius = (value - 32) * 5 / 9


t = Temperature()
t.celsius = 36.6
print(round(t.fahrenheit, 2), round(t.kelvin, 2))
try:
    t.fahrenheit = -500
except ValueError as error:
    print("ValueError:", error)
```

**Задание 6**
```python
import hashlib


class Account:
    """Аккаунт: пароль можно только записать, но не прочитать."""

    def __init__(self, login, password):
        self.login = login
        self.password = password

    @staticmethod
    def _hash(text):
        return hashlib.sha256(text.encode("utf-8")).hexdigest()

    @property
    def password(self):
        raise AttributeError("Пароль читать нельзя")

    @password.setter
    def password(self, value):
        if len(value) < 8:
            raise ValueError("Минимум 8 символов")
        if not any(ch.isdigit() for ch in value):
            raise ValueError("Хотя бы одна цифра")
        if not any(ch.isupper() for ch in value):
            raise ValueError("Хотя бы одна заглавная буква")
        if not any(ch.islower() for ch in value):
            raise ValueError("Хотя бы одна строчная буква")
        self._password_hash = self._hash(value)
        kinds = sum([any(c.isdigit() for c in value), any(c.isupper() for c in value),
                     any(c.islower() for c in value), any(not c.isalnum() for c in value)])
        score = kinds + (len(value) >= 12)
        self._strength = "сильный" if score >= 5 else "средний" if score >= 4 else "слабый"

    @property
    def strength(self):
        return self._strength

    def check(self, password):
        return self._hash(password) == self._password_hash


acc = Account("aigerim", "Python2026")
print(acc.check("Python2026"), acc.strength)
try:
    print(acc.password)
except AttributeError as error:
    print(error)
try:
    acc.password = "qwerty"
except ValueError as error:
    print("Слабый пароль:", error)
```

**Задание 7**
```python
class Clock:
    """Часы в пределах суток."""

    def __init__(self, hours=0, minutes=0):
        self.hours = hours
        self.minutes = minutes

    @property
    def hours(self):
        return self._hours

    @hours.setter
    def hours(self, value):
        self._hours = value % 24

    @property
    def minutes(self):
        return self._minutes

    @minutes.setter
    def minutes(self, value):
        extra_hours, self._minutes = divmod(value, 60)
        self.hours = self._hours + extra_hours

    @property
    def time_str(self):
        return f"{self._hours:02}:{self._minutes:02}"

    def add_minutes(self, n):
        self.minutes = self._minutes + n


c = Clock(9, 5)
print(c.time_str)
c.minutes = 135
print(c.time_str)
c.add_minutes(1000)
print(c.time_str)
```

**Задание 8**
```python
import math


class Polygon:
    """Многоугольник (площадь и периметр кэшируются)."""

    def __init__(self, points):
        self.points = points
        self.computations = 0

    @property
    def points(self):
        return list(self._points)

    @points.setter
    def points(self, value):
        if len(value) < 3:
            raise ValueError("Нужно не менее 3 вершин")
        self._points = list(value)
        self._cache = {}

    def add_point(self, point):
        self._points.append(point)
        self._cache = {}

    @property
    def area(self):
        if "area" not in self._cache:
            self.computations += 1
            pts = self._points
            s = sum(pts[i][0] * pts[(i + 1) % len(pts)][1] - pts[(i + 1) % len(pts)][0] * pts[i][1]
                    for i in range(len(pts)))
            self._cache["area"] = abs(s) / 2
        return self._cache["area"]

    @property
    def perimeter(self):
        if "perimeter" not in self._cache:
            pts = self._points
            self._cache["perimeter"] = sum(math.dist(pts[i], pts[(i + 1) % len(pts)])
                                           for i in range(len(pts)))
        return self._cache["perimeter"]


square = Polygon([(0, 0), (4, 0), (4, 4), (0, 4)])
for _ in range(3):
    square.area
print(f"площадь {square.area}, периметр {square.perimeter}")
print("Количество вычислений:", square.computations)
```
`functools.cached_property` вычисляет значение один раз и записывает его в `__dict__` объекта, но автоматически не очищает — для очистки нужно `del obj.имя`.

**Задание 9**
```python
import time


def timer(func):
    """Декоратор, выводящий время выполнения функции."""
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        print(f"{func.__name__}: {time.perf_counter() - start:.3f} с")
        return result
    return wrapper


@timer
def primes_simple(n):
    return [x for x in range(2, n + 1) if all(x % d for d in range(2, int(x ** 0.5) + 1))]


@timer
def primes_sieve(n):
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    for i in range(2, int(n ** 0.5) + 1):
        if is_prime[i]:
            for j in range(i * i, n + 1, i):
                is_prime[j] = False
    return [i for i, flag in enumerate(is_prime) if flag]


print(len(primes_simple(50_000)), len(primes_sieve(50_000)))
```

</details>
