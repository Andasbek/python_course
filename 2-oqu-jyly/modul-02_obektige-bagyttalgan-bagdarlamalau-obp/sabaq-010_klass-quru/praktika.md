# Сабақ 10. Практикалық тапсырмалар: класс, __init__, атрибуттар

## Базалық деңгей

### 1-тапсырма. `Student` класы
Атрибуттары: `name`, `grade` (сынып), `city` (әдепкі — `"Алматы"`), `marks` (бос тізім). Үш оқушы жаса (біреуі — басқа қаладан), біреуіне 3 баға қос. Барлық оқушының мәліметін цикл арқылы шығар.

```text
Әлия, 8-сынып, Алматы, бағалары: [5, 4, 5]
Бекзат, 9-сынып, Талдықорған, бағалары: []
Дана, 7-сынып, Алматы, бағалары: []
```

### 2-тапсырма. `City` класы
Атрибуттары: `name`, `population` (адам), `area` (км²), `density` — **конструкторда есептелетін** халық тығыздығы (адам/км², бүтінге дөңгелектенген). Алматы (2 230 000 адам, 683 км²) және Астана (1 420 000 адам, 797 км²) объектілерін жасап, қайсысының тығыздығы жоғары екенін шығар.

```text
Алматы: 3265 адам/км²
Астана: 1782 адам/км²
Тығыздығы жоғары: Алматы
```

### 3-тапсырма. `Product` класы және тексеру
Атрибуттары: `name`, `price`, `quantity` (әдепкі 0). Конструктор:
- баға теріс болса, «Баға теріс болмайды» деп ескертіп, 0 қояды;
- атауды бос орынсыз және бірінші әрпі бас әріппен сақтайды.

```python
p = Product("  ноутбук ", -350000, 3)
print(p.name, p.price, p.quantity)
```
```text
Баға теріс болмайды
Ноутбук 0 3
```

### 4-тапсырма. Объектілер санауышы
`Ticket` (кино билеті) класын жаз: класс атрибуты `sold = 0`; объект атрибуттары `film`, `row`, `seat`, `price` (әдепкі 2000) және `number` — конструкторда автоматты берілетін нөмір (`Ticket.sold` арқылы). 4 билет сатып, әрқайсысының нөмірін және жалпы сатылған билет санын шығар.

```text
№1: «Көшпенділер», 5-қатар, 7-орын, 2000 ₸
№2: ...
Сатылды: 4 билет
```

## Кеңейтілген деңгей

### 5-тапсырма. Қатені тап
Төмендегі кодта 4 қате бар. Оларды тауып, түзет және әрқайсысын түсіндір.

```python
class Pet:
    toys = []

    def __int__(self, name, kind):
        self.name = name
        kind = kind


cat = Pet("Мұрзик", "мысық")
dog = Pet("Ақтөс", "ит")
cat.toys.append("доп")
print(dog.toys, cat.kind)
```

### 6-тапсырма. Композиция: `Address` және `School`
`Address` класы: `city`, `street`, `building`. `School` класы: `name`, `address` (`Address` объектісі), `students_count`, `founded` (құрылған жылы), `age` — конструкторда есептелетін мектеп жасы (2026 жылға). Екі мектеп жасап, толық мекенжайымен шығар.

```text
№35 мектеп-гимназия: Алматы, Абай даңғылы, 52; 1240 оқушы, 61 жыл
```

### 7-тапсырма. Сөздіктен объектіге
Оқушылар туралы деректер сөздіктер тізімі ретінде берілген. Әр сөздіктен `Student` объектісін жасап, объектілер тізімін құр, кейін 8-сыныптан жоғары оқушыларды шығар. (Кеңес: `Student(**data)` — сөздікті атаулы аргументтерге «ашу».)

```python
data = [
    {"name": "Әлия", "grade": 8},
    {"name": "Бекзат", "grade": 10, "city": "Шымкент"},
    {"name": "Дана", "grade": 9},
]
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. «Уақыт» класы
`Time` класын жаз: `Time(hours, minutes, seconds)`. Конструктор кез келген мәндерді **қалыпқа келтіреді**: `Time(1, 75, 130)` → 2 сағ 17 мин 10 с; `Time(25, 0, 0)` → 1 сағ 0 мин 0 с (тәулік 24 сағат); теріс секундтар да дұрыс өңделсін (`Time(0, 0, -10)` → 23:59:50). Сондай-ақ `total_seconds` атрибутын есепте.

```text
Time(1, 75, 130) → 02:17:10, total_seconds = 8230
Time(0, 0, -10)  → 23:59:50
```

### 9-тапсырма. Жоба кластарының «қаңқасы»
Өткен сабақта жобаң үшін салған UML диаграммалары бойынша барлық кластарды `__init__` конструкторларымен жаз (әзірге әдістерсіз). Әр класқа docstring. Әр кластан кемінде бір объект жасап, `vars()` арқылы шығар. GitHub-қа push жаса.

## Үй тапсырмасы

1. `Movie` класын жаз: `title`, `year`, `genre`, `rating` (0–10, шектен шықса — шекке келтіру), `viewers = 0`. 5 фильм объектісін жасап, рейтингі 7-ден жоғарыларын шығар.
2. Келесі сабаққа: өз жоба кластарыңның әрқайсысына қандай **әрекеттер** (әдістер) керек екенін тізімде.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
class Student:
    """Үйірме оқушысы."""

    def __init__(self, name, grade, city="Алматы"):
        self.name = name
        self.grade = grade
        self.city = city
        self.marks = []


students = [Student("Әлия", 8), Student("Бекзат", 9, "Талдықорған"), Student("Дана", 7)]
students[0].marks.extend([5, 4, 5])
for s in students:
    print(f"{s.name}, {s.grade}-сынып, {s.city}, бағалары: {s.marks}")
```

**2-тапсырма**
```python
class City:
    """Қала."""

    def __init__(self, name, population, area):
        self.name = name
        self.population = population
        self.area = area
        self.density = round(population / area)


almaty = City("Алматы", 2_230_000, 683)
astana = City("Астана", 1_420_000, 797)
for city in (almaty, astana):
    print(f"{city.name}: {city.density} адам/км²")
denser = almaty if almaty.density > astana.density else astana
print("Тығыздығы жоғары:", denser.name)
```

**3-тапсырма**
```python
class Product:
    """Дүкен тауары."""

    def __init__(self, name, price, quantity=0):
        if price < 0:
            print("Баға теріс болмайды")
            price = 0
        self.name = name.strip().capitalize()
        self.price = price
        self.quantity = quantity


p = Product("  ноутбук ", -350000, 3)
print(p.name, p.price, p.quantity)
```

**4-тапсырма**
```python
class Ticket:
    """Кино билеті."""
    sold = 0

    def __init__(self, film, row, seat, price=2000):
        Ticket.sold += 1
        self.number = Ticket.sold
        self.film = film
        self.row = row
        self.seat = seat
        self.price = price


tickets = [
    Ticket("Көшпенділер", 5, 7),
    Ticket("Көшпенділер", 5, 8),
    Ticket("Томирис", 3, 1, 2500),
    Ticket("Томирис", 3, 2, 2500),
]
for t in tickets:
    print(f"№{t.number}: «{t.film}», {t.row}-қатар, {t.seat}-орын, {t.price} ₸")
print("Сатылды:", Ticket.sold, "билет")
```

**5-тапсырма** — қателер: (1) `__int__` → `__init__`; (2) `kind = kind` → `self.kind = kind`; (3) `toys = []` класс атрибуты — барлық үй жануарына ортақ, `self.toys = []` конструкторда болуы керек; (4) соның салдарынан `dog.toys` → `['доп']` және `cat.kind` → `AttributeError`.
```python
class Pet:
    """Үй жануары."""

    def __init__(self, name, kind):
        self.name = name
        self.kind = kind
        self.toys = []


cat = Pet("Мұрзик", "мысық")
dog = Pet("Ақтөс", "ит")
cat.toys.append("доп")
print(dog.toys, cat.kind)  # [] мысық
```

**6-тапсырма**
```python
CURRENT_YEAR = 2026


class Address:
    """Мекенжай."""

    def __init__(self, city, street, building):
        self.city = city
        self.street = street
        self.building = building


class School:
    """Мектеп."""

    def __init__(self, name, address, students_count, founded):
        self.name = name
        self.address = address
        self.students_count = students_count
        self.founded = founded
        self.age = CURRENT_YEAR - founded


schools = [
    School("№35 мектеп-гимназия", Address("Алматы", "Абай даңғылы", 52), 1240, 1965),
    School("№12 лицей", Address("Астана", "Республика даңғылы", 8), 980, 1998),
]
for s in schools:
    a = s.address
    print(f"{s.name}: {a.city}, {a.street}, {a.building}; "
          f"{s.students_count} оқушы, {s.age} жыл")
```

**7-тапсырма**
```python
class Student:
    """Үйірме оқушысы."""

    def __init__(self, name, grade, city="Алматы"):
        self.name = name
        self.grade = grade
        self.city = city


data = [
    {"name": "Әлия", "grade": 8},
    {"name": "Бекзат", "grade": 10, "city": "Шымкент"},
    {"name": "Дана", "grade": 9},
]
students = [Student(**item) for item in data]
for s in students:
    if s.grade > 8:
        print(s.name, s.grade, s.city)
```

**8-тапсырма**
```python
SECONDS_IN_DAY = 24 * 60 * 60


class Time:
    """Тәулік ішіндегі уақыт (қалыпқа келтірілген)."""

    def __init__(self, hours=0, minutes=0, seconds=0):
        total = (hours * 3600 + minutes * 60 + seconds) % SECONDS_IN_DAY
        self.total_seconds = total
        self.hours = total // 3600
        self.minutes = total % 3600 // 60
        self.seconds = total % 60


for t in (Time(1, 75, 130), Time(25, 0, 0), Time(0, 0, -10)):
    print(f"{t.hours:02}:{t.minutes:02}:{t.seconds:02}, total_seconds = {t.total_seconds}")
```

Үй тапсырмасы 1:
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
    Movie("Көшпенділер", 2005, "тарихи", 6.8),
    Movie("Томирис", 2019, "тарихи", 7.2),
    Movie("Қыз Жібек", 1970, "драма", 8.4),
    Movie("Бауыржан Момышұлы", 2013, "соғыс", 7.5),
    Movie("Тест", 2024, "комедия", 12),
]
for m in movies:
    if m.rating > 7:
        print(m.title, m.rating)
```

</details>
