# Урок 10. Практические задания: класс, __init__, атрибуты

## Базовый уровень

### Задание 1. Класс `Student`
Атрибуты: `name`, `grade` (класс), `city` (по умолчанию — `"Алматы"`), `marks` (пустой список). Создай трёх учеников (один — из другого города), одному добавь 3 оценки. Выведи сведения обо всех учениках в цикле.

```text
Алия, 8 класс, Алматы, оценки: [5, 4, 5]
Бекзат, 9 класс, Талдыкорган, оценки: []
Дана, 7 класс, Алматы, оценки: []
```

### Задание 2. Класс `City`
Атрибуты: `name`, `population` (человек), `area` (км²), `density` — плотность населения, **вычисляемая в конструкторе** (чел./км², округлённая до целого). Создай объекты Алматы (2 230 000 человек, 683 км²) и Астана (1 420 000 человек, 797 км²) и выведи, у какого города плотность выше.

```text
Алматы: 3265 чел./км²
Астана: 1782 чел./км²
Плотность выше: Алматы
```

### Задание 3. Класс `Product` и проверка
Атрибуты: `name`, `price`, `quantity` (по умолчанию 0). Конструктор:
- если цена отрицательная, предупреждает «Цена не может быть отрицательной» и устанавливает 0;
- сохраняет название без лишних пробелов и с заглавной первой буквой.

```python
p = Product("  ноутбук ", -350000, 3)
print(p.name, p.price, p.quantity)
```
```text
Цена не может быть отрицательной
Ноутбук 0 3
```

### Задание 4. Счётчик объектов
Напиши класс `Ticket` (билет в кино): атрибут класса `sold = 0`; атрибуты объекта `film`, `row`, `seat`, `price` (по умолчанию 2000) и `number` — номер, автоматически присваиваемый в конструкторе (через `Ticket.sold`). Продай 4 билета, выведи номер каждого и общее количество проданных билетов.

```text
№1: «Кочевник», ряд 5, место 7, 2000 ₸
№2: ...
Продано: 4 билета
```

## Продвинутый уровень

### Задание 5. Найди ошибки
В коде ниже 4 ошибки. Найди их, исправь и объясни каждую.

```python
class Pet:
    toys = []

    def __int__(self, name, kind):
        self.name = name
        kind = kind


cat = Pet("Мурзик", "кошка")
dog = Pet("Актос", "собака")
cat.toys.append("мяч")
print(dog.toys, cat.kind)
```

### Задание 6. Композиция: `Address` и `School`
Класс `Address`: `city`, `street`, `building`. Класс `School`: `name`, `address` (объект `Address`), `students_count`, `founded` (год основания), `age` — возраст школы, вычисляемый в конструкторе (на 2026 год). Создай две школы и выведи их с полным адресом.

```text
Школа-гимназия №35: Алматы, проспект Абая, 52; 1240 учеников, возраст: 61
```

### Задание 7. Из словаря в объект
Данные об учениках заданы в виде списка словарей. Создай из каждого словаря объект `Student`, составь список объектов, затем выведи учеников старше 8-го класса. (Подсказка: `Student(**data)` — «распаковка» словаря в именованные аргументы.)

```python
data = [
    {"name": "Алия", "grade": 8},
    {"name": "Бекзат", "grade": 10, "city": "Шымкент"},
    {"name": "Дана", "grade": 9},
]
```

## ★ Олимпиадная / творческая задача

### Задание 8. Класс «Время»
Напиши класс `Time`: `Time(hours, minutes, seconds)`. Конструктор **нормализует** любые значения: `Time(1, 75, 130)` → 2 ч 17 мин 10 с; `Time(25, 0, 0)` → 1 ч 0 мин 0 с (в сутках 24 часа); отрицательные секунды тоже должны обрабатываться правильно (`Time(0, 0, -10)` → 23:59:50). Также вычисли атрибут `total_seconds`.

```text
Time(1, 75, 130) → 02:17:10, total_seconds = 8230
Time(0, 0, -10)  → 23:59:50
```

### Задание 9. «Каркас» классов проекта
По UML-диаграммам, нарисованным на прошлом уроке для твоего проекта, напиши все классы с конструкторами `__init__` (пока без методов). К каждому классу — docstring. Создай хотя бы по одному объекту каждого класса и выведи через `vars()`. Сделай push на GitHub.

## Домашнее задание

1. Напиши класс `Movie`: `title`, `year`, `genre`, `rating` (0–10, при выходе за пределы — приведение к границе), `viewers = 0`. Создай 5 объектов-фильмов и выведи те, у которых рейтинг выше 7.
2. К следующему уроку: составь список **действий** (методов), которые нужны каждому из классов твоего проекта.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
class Student:
    """Учащийся кружка."""

    def __init__(self, name, grade, city="Алматы"):
        self.name = name
        self.grade = grade
        self.city = city
        self.marks = []


students = [Student("Алия", 8), Student("Бекзат", 9, "Талдыкорган"), Student("Дана", 7)]
students[0].marks.extend([5, 4, 5])
for s in students:
    print(f"{s.name}, {s.grade} класс, {s.city}, оценки: {s.marks}")
```

**Задание 2**
```python
class City:
    """Город."""

    def __init__(self, name, population, area):
        self.name = name
        self.population = population
        self.area = area
        self.density = round(population / area)


almaty = City("Алматы", 2_230_000, 683)
astana = City("Астана", 1_420_000, 797)
for city in (almaty, astana):
    print(f"{city.name}: {city.density} чел./км²")
denser = almaty if almaty.density > astana.density else astana
print("Плотность выше:", denser.name)
```

**Задание 3**
```python
class Product:
    """Товар магазина."""

    def __init__(self, name, price, quantity=0):
        if price < 0:
            print("Цена не может быть отрицательной")
            price = 0
        self.name = name.strip().capitalize()
        self.price = price
        self.quantity = quantity


p = Product("  ноутбук ", -350000, 3)
print(p.name, p.price, p.quantity)
```

**Задание 4**
```python
class Ticket:
    """Билет в кино."""
    sold = 0

    def __init__(self, film, row, seat, price=2000):
        Ticket.sold += 1
        self.number = Ticket.sold
        self.film = film
        self.row = row
        self.seat = seat
        self.price = price


tickets = [
    Ticket("Кочевник", 5, 7),
    Ticket("Кочевник", 5, 8),
    Ticket("Томирис", 3, 1, 2500),
    Ticket("Томирис", 3, 2, 2500),
]
for t in tickets:
    print(f"№{t.number}: «{t.film}», ряд {t.row}, место {t.seat}, {t.price} ₸")
print("Продано:", Ticket.sold, "билета")
```

**Задание 5** — ошибки: (1) `__int__` → `__init__`; (2) `kind = kind` → `self.kind = kind`; (3) `toys = []` — атрибут класса, общий для всех питомцев, `self.toys = []` должен быть в конструкторе; (4) как следствие, `dog.toys` → `['мяч']`, а `cat.kind` → `AttributeError`.
```python
class Pet:
    """Домашний питомец."""

    def __init__(self, name, kind):
        self.name = name
        self.kind = kind
        self.toys = []


cat = Pet("Мурзик", "кошка")
dog = Pet("Актос", "собака")
cat.toys.append("мяч")
print(dog.toys, cat.kind)  # [] кошка
```

**Задание 6**
```python
CURRENT_YEAR = 2026


class Address:
    """Адрес."""

    def __init__(self, city, street, building):
        self.city = city
        self.street = street
        self.building = building


class School:
    """Школа."""

    def __init__(self, name, address, students_count, founded):
        self.name = name
        self.address = address
        self.students_count = students_count
        self.founded = founded
        self.age = CURRENT_YEAR - founded


schools = [
    School("Школа-гимназия №35", Address("Алматы", "проспект Абая", 52), 1240, 1965),
    School("Лицей №12", Address("Астана", "проспект Республики", 8), 980, 1998),
]
for s in schools:
    a = s.address
    print(f"{s.name}: {a.city}, {a.street}, {a.building}; "
          f"{s.students_count} учеников, возраст: {s.age}")
```

**Задание 7**
```python
class Student:
    """Учащийся кружка."""

    def __init__(self, name, grade, city="Алматы"):
        self.name = name
        self.grade = grade
        self.city = city


data = [
    {"name": "Алия", "grade": 8},
    {"name": "Бекзат", "grade": 10, "city": "Шымкент"},
    {"name": "Дана", "grade": 9},
]
students = [Student(**item) for item in data]
for s in students:
    if s.grade > 8:
        print(s.name, s.grade, s.city)
```

**Задание 8**
```python
SECONDS_IN_DAY = 24 * 60 * 60


class Time:
    """Время в пределах суток (нормализованное)."""

    def __init__(self, hours=0, minutes=0, seconds=0):
        total = (hours * 3600 + minutes * 60 + seconds) % SECONDS_IN_DAY
        self.total_seconds = total
        self.hours = total // 3600
        self.minutes = total % 3600 // 60
        self.seconds = total % 60


for t in (Time(1, 75, 130), Time(25, 0, 0), Time(0, 0, -10)):
    print(f"{t.hours:02}:{t.minutes:02}:{t.seconds:02}, total_seconds = {t.total_seconds}")
```

Домашнее задание 1:
```python
class Movie:
    """Фильм."""

    def __init__(self, title, year, genre, rating):
        self.title = title
        self.year = year
        self.genre = genre
        self.rating = min(max(rating, 0), 10)
        self.viewers = 0


movies = [
    Movie("Кочевник", 2005, "исторический", 6.8),
    Movie("Томирис", 2019, "исторический", 7.2),
    Movie("Кыз Жибек", 1970, "драма", 8.4),
    Movie("Бауыржан Момышулы", 2013, "военный", 7.5),
    Movie("Тест", 2024, "комедия", 12),
]
for m in movies:
    if m.rating > 7:
        print(m.title, m.rating)
```

</details>
