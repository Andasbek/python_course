# Сабақ 14. Практикалық тапсырмалар: property, геттер және сеттер

## Базалық деңгей

### 1-тапсырма. `get/set`-тен `property`-ге
Өткен сабақтағы `Student` класын (`get_age()`, `set_age()`, `get_name()`) `@property` арқылы қайта жаз:
- `name` — тек оқуға арналған;
- `age` — 6–18 аралығы, қате болса `ValueError`, бүтін сан болмаса `TypeError`;
- конструктор сеттер арқылы тексерсін.

Тексеру коды:
```python
s = Student("Әлия", 14)
s.age += 1
print(s.name, s.age)
for bad in (40, "он", 5):
    try:
        s.age = bad
    except (ValueError, TypeError) as error:
        print("Қате:", error)
```
```text
Әлия 15
Қате: Жас 6–18 аралығында болуы керек: 40
Қате: Жас бүтін сан болуы керек
Қате: Жас 6–18 аралығында болуы керек: 5
```

### 2-тапсырма. `Circle` — есептелетін қасиеттер
`Circle(radius)`: `radius` — property (оң сан), `diameter` — property **сеттерімен** (диаметрді өзгерту радиусты өзгертеді), `area` және `circumference` — тек оқу.

```text
c = Circle(5) → d = 10, S = 78.54
c.diameter = 4 → r = 2.0, S = 12.57
```

### 3-тапсырма. `Product` — баға мен жеңілдік
`Product(name, price, discount=0)`:
- `price` — теріс емес;
- `discount` — 0–90 (%) аралығы;
- `final_price` — тек оқу: жеңілдікпен баға (бүтінге дөңгелектенген);
- `name` — сеттерде бос орындар алынып, бос жол болса `ValueError`.

```text
Кроссовка: 45000 ₸, жеңілдік 20% → 36000 ₸
```

### 4-тапсырма. Read-only қасиеттер
`Order` (тапсырыс) класы: `id` (класс санауышы арқылы автоматты, тек оқу), `created` (`datetime.now()`, тек оқу), `items` (тізім **көшірмесін** қайтаратын property), `add_item(name, price)`, `total` (тек оқу). `order.id = 5` және `order.total = 0` әрекеттерінің `AttributeError` беретінін `try/except` арқылы көрсет.

## Кеңейтілген деңгей

### 5-тапсырма. `Temperature` — үш шкала
`Temperature` класы ішінде тек Кельвинмен сақтайды (`_kelvin ≥ 0`), бірақ `celsius`, `fahrenheit`, `kelvin` үш қасиеттің бәріне **геттер және сеттер** бар. Кез келген шкаламен орнатылған мән басқа екеуінде дұрыс көрінуі керек.

```text
t = Temperature(); t.celsius = 36.6
t.fahrenheit → 97.88, t.kelvin → 309.75
t.fahrenheit = -500 → ValueError: Абсолюттік нөлден төмен
```

### 6-тапсырма. `Password` — күрделілікті тексеру
`Account(login, password)`: `password` қасиетінің **геттері жоқ** (оқуға болмайды — геттер `AttributeError` лақтырсын), сеттері талаптарды тексереді: кемінде 8 таңба, кемінде бір цифр, бір бас әріп, бір кіші әріп; сақталатыны — тек хэш. `check(password)` әдісі. `strength` — тек оқу қасиеті: «әлсіз/орташа/күшті» (ұзындық пен таңба түрлеріне қарай).

### 7-тапсырма. `Clock` — сағат
`Clock(hours, minutes)`: `hours` (0–23), `minutes` (0–59) — property; `minutes` сеттері 59-дан асса, артығын сағатқа ауыстырады (`minutes = 135` → +2 сағ 15 мин); `time_str` — `"09:05"` форматы (тек оқу); `add_minutes(n)` әдісі.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Кэштелген қасиет
`Polygon` класы төбелер тізімін (`points`, `(x, y)` кортеждері) сақтайды. `area` (Гаусс/«бау» формуласы) және `perimeter` — есептелетін property, бірақ олар **кэштеледі**: бір рет есептеліп сақталады, ал `points` сеттері немесе `add_point()` шақырылғанда кэш тазаланады. Кэш жұмыс істейтінін есептеу санауышымен дәлелде. (Кеңес: `self._cache = {}`; қосымша — `functools.cached_property` туралы оқып, айырмашылығын түсіндір.)

```text
Квадрат (0,0),(4,0),(4,4),(0,4): аудан 16.0, периметр 16.0
area 3 рет оқылды, есептеу: 1 рет
```

### 9-тапсырма. Өз декораторың
`timer` декораторын жаз: ол функцияның орындалу уақытын (`time.perf_counter`) шығарады. Оны 1-ден `n`-ге дейінгі жай сандарды табатын екі функцияға (қарапайым және Эратосфен елегі) қолданып, `n = 50 000` үшін жылдамдығын салыстыр.

## Үй тапсырмасы

1. Өз жобаңның кластарында кемінде бір тексерілетін property және бір есептелетін property жаса; push жаса.
2. 12-сабақтағы `Classroom` контейнеріне `average` және `size` есептелетін қасиеттерін қос (әдістердің орнына).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
class Student:
    """Оқушы."""
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
            raise TypeError("Жас бүтін сан болуы керек")
        if not self.MIN_AGE <= value <= self.MAX_AGE:
            raise ValueError(f"Жас {self.MIN_AGE}–{self.MAX_AGE} аралығында болуы керек: {value}")
        self._age = value


s = Student("Әлия", 14)
s.age += 1
print(s.name, s.age)
for bad in (40, "он", 5):
    try:
        s.age = bad
    except (ValueError, TypeError) as error:
        print("Қате:", error)
```

**2-тапсырма**
```python
import math


class Circle:
    """Шеңбер."""

    def __init__(self, radius):
        self.radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value <= 0:
            raise ValueError("Радиус оң болуы керек")
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

**3-тапсырма**
```python
class Product:
    """Жеңілдігі бар тауар."""

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
            raise ValueError("Атауы бос болмайды")
        self._name = value

    @property
    def price(self):
        return self._price

    @price.setter
    def price(self, value):
        if value < 0:
            raise ValueError("Баға теріс болмайды")
        self._price = value

    @property
    def discount(self):
        return self._discount

    @discount.setter
    def discount(self, value):
        if not 0 <= value <= 90:
            raise ValueError("Жеңілдік 0–90% аралығында")
        self._discount = value

    @property
    def final_price(self):
        return round(self._price * (100 - self._discount) / 100)


p = Product(" Кроссовка ", 45000, 20)
print(f"{p.name}: {p.price} ₸, жеңілдік {p.discount}% → {p.final_price} ₸")
```

**4-тапсырма**
```python
from datetime import datetime


class Order:
    """Тапсырыс."""
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
            raise ValueError("Баға теріс болмайды")
        self._items.append((name, price))


order = Order()
order.add_item("Пицца", 3500)
order.add_item("Шырын", 800)
print(order.id, order.created.strftime("%d.%m.%Y %H:%M"), order.items, order.total)
for attr, value in (("id", 5), ("total", 0)):
    try:
        setattr(order, attr, value)
    except AttributeError as error:
        print("Болмайды:", error)
```

**5-тапсырма**
```python
class Temperature:
    """Температура (ішінде Кельвинмен)."""

    def __init__(self, kelvin=273.15):
        self.kelvin = kelvin

    @property
    def kelvin(self):
        return self._kelvin

    @kelvin.setter
    def kelvin(self, value):
        if value < 0:
            raise ValueError("Абсолюттік нөлден төмен")
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

**6-тапсырма**
```python
import hashlib


class Account:
    """Аккаунт: пароль тек жазылады, оқылмайды."""

    def __init__(self, login, password):
        self.login = login
        self.password = password

    @staticmethod
    def _hash(text):
        return hashlib.sha256(text.encode("utf-8")).hexdigest()

    @property
    def password(self):
        raise AttributeError("Парольді оқуға болмайды")

    @password.setter
    def password(self, value):
        if len(value) < 8:
            raise ValueError("Кемінде 8 таңба")
        if not any(ch.isdigit() for ch in value):
            raise ValueError("Кемінде бір цифр")
        if not any(ch.isupper() for ch in value):
            raise ValueError("Кемінде бір бас әріп")
        if not any(ch.islower() for ch in value):
            raise ValueError("Кемінде бір кіші әріп")
        self._password_hash = self._hash(value)
        kinds = sum([any(c.isdigit() for c in value), any(c.isupper() for c in value),
                     any(c.islower() for c in value), any(not c.isalnum() for c in value)])
        score = kinds + (len(value) >= 12)
        self._strength = "күшті" if score >= 5 else "орташа" if score >= 4 else "әлсіз"

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
    print("Әлсіз пароль:", error)
```

**7-тапсырма**
```python
class Clock:
    """Тәуліктік сағат."""

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

**8-тапсырма**
```python
import math


class Polygon:
    """Көпбұрыш (аудан мен периметр кэштеледі)."""

    def __init__(self, points):
        self.points = points
        self.computations = 0

    @property
    def points(self):
        return list(self._points)

    @points.setter
    def points(self, value):
        if len(value) < 3:
            raise ValueError("Кемінде 3 төбе керек")
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
print(f"аудан {square.area}, периметр {square.perimeter}")
print("Есептеу саны:", square.computations)
```
`functools.cached_property` мәнді бір рет есептеп, объектінің `__dict__`-іне жазады, бірақ оны автоматты түрде тазаламайды — тазалау үшін `del obj.атауы` керек.

**9-тапсырма**
```python
import time


def timer(func):
    """Функцияның орындалу уақытын шығаратын декоратор."""
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
