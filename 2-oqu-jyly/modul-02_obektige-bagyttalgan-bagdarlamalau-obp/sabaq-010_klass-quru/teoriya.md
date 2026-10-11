# Сабақ 10. Класс құру. __init__ конструкторы. Объект атрибуттары.

## Тақырыптар

1. `__init__` конструкторы
2. Конструктордың параметрлері
3. Объект атрибуттары мен класс атрибуттары
4. Атрибуттармен жұмыс
5. Объектілер — бөлек, бірақ сілтемелерге абай бол
6. Объектінің ішіндегі объект (композиция — алғашқы түсінік)
7. Класты жобалаудан кодқа дейін
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. `__init__` конструкторы

**Конструктор** (инициализатор) — объект жасалған сәтте **автоматты түрде** шақырылатын арнайы әдіс. Python-да оның аты — `__init__` (екі жағында екі астын сызу: «дандер init», double underscore).

Аналогия: перзентханада нәресте туғанда оған бірден аты, туған күні, салмағы жазылған білезік тағылады. `__init__` — сол «білезік тағу» рәсімі.

```python
class Cat:
    """Мысықты сипаттайтын класс."""

    def __init__(self, name, color, age):
        self.name = name
        self.color = color
        self.age = age


murzik = Cat("Мұрзик", "сұр", 3)
barsik = Cat("Барсик", "қара", 5)

print(murzik.name, murzik.color, murzik.age)
print(barsik.name, barsik.color, barsik.age)
```
Нәтижесі:
```text
Мұрзик сұр 3
Барсик қара 5
```

### Не болады? Қадамдап
`murzik = Cat("Мұрзик", "сұр", 3)` жолы орындалғанда:
1. Python жаңа, бос `Cat` объектісін жасайды.
2. `__init__` әдісін шақырады: `self` — осы жаңа объект, `name = "Мұрзик"`, `color = "сұр"`, `age = 3`.
3. `self.name = name` — **объектінің** `name` атрибутына параметрдің мәнін жазады.
4. Дайын объект `murzik` айнымалысына меншіктеледі.

```text
Cat("Мұрзик", "сұр", 3)
        │
        ▼
 ┌─────────────────┐     __init__(self, name, color, age)
 │ жаңа бос объект │ ──► self.name  = "Мұрзик"
 └─────────────────┘     self.color = "сұр"
        │                self.age   = 3
        ▼
 murzik ──► ┌──────────────────────┐
            │ name  = "Мұрзик"     │
            │ color = "сұр"        │
            │ age   = 3            │
            └──────────────────────┘
```

### `self` деген не? (қысқаша)
`self` — «осы объектінің өзі» дегенді білдіреді. Конструктор барлық мысыққа ортақ, ал `self` арқылы ол **қай мысыққа** атрибут жазып жатқанын біледі: `murzik` жасалғанда `self` — `murzik`, `barsik` жасалғанда — `barsik`.

- `self` — әдістің **бірінші** параметрі, оны шақырғанда **бермейміз**: `Cat("Мұрзик", "сұр", 3)` — үш аргумент, ал `__init__`-те төрт параметр.
- `self.name` — объектінің атрибуты (объект өмір сүргенше сақталады).
- `name` (self-сіз) — жай ғана параметр, жергілікті айнымалы (`__init__` біткенде жойылады).

`self` әдістерде дәл осылай жұмыс істейді: әр әдістің бірінші параметрі — әдіс шақырылған объект.

## 2. Конструктордың параметрлері

### Әдепкі мәндер
Функциялардағыдай, параметрлерге әдепкі мән беруге болады:

```python
class Student:
    """Үйірме оқушысы."""

    def __init__(self, name, grade, city="Алматы"):
        self.name = name
        self.grade = grade
        self.city = city


aliya = Student("Әлия", 8)
bekzat = Student("Бекзат", 9, "Қонаев")
dana = Student(name="Дана", grade=7)      # атаулы аргументтер

for s in [aliya, bekzat, dana]:
    print(f"{s.name}, {s.grade}-сынып, {s.city}")
```
Нәтижесі:
```text
Әлия, 8-сынып, Алматы
Бекзат, 9-сынып, Қонаев
Дана, 7-сынып, Алматы
```

### Параметрден алынбайтын атрибуттар
Барлық атрибут параметрден келуі міндетті емес. Кейбіреулері бастапқы мәнмен басталады:

```python
class Student:
    """Үйірме оқушысы."""

    def __init__(self, name, grade):
        self.name = name
        self.grade = grade
        self.marks = []          # бағалар — бос тізімнен басталады
        self.is_active = True    # жаңа оқушы белсенді
        self.points = 0


aliya = Student("Әлия", 8)
aliya.marks.append(5)
aliya.marks.append(4)
aliya.points += 10
print(aliya.name, aliya.marks, aliya.points, aliya.is_active)
```
Нәтижесі:
```text
Әлия [5, 4] 10 True
```

Маңызды: `self.marks = []` конструктор **ішінде** — сондықтан әр оқушының **өз** тізімі болады.

### Есептелетін атрибуттар
Конструктор ішінде басқа атрибуттардан мән есептеуге болады:

```python
class Rectangle:
    """Тіктөртбұрыш."""

    def __init__(self, width, height):
        self.width = width
        self.height = height
        self.area = width * height
        self.perimeter = 2 * (width + height)
        self.is_square = width == height


r = Rectangle(4, 6)
print(r.area, r.perimeter, r.is_square)
```
Нәтижесі:
```text
24 20 False
```
Ескерту: егер кейін `r.width = 10` деп өзгертсек, `r.area` автоматты түрде **жаңармайды** (ол бір рет есептелген). Бұл мәселе ауданды әдіс арқылы есептеу немесе `property` декораторы арқылы шешіледі.

### Бастапқы мәндерді тексеру
Конструктор объектінің «дұрыс» туылуын қамтамасыз ете алады:

```python
class BankAccount:
    """Банк шоты."""

    def __init__(self, owner, balance=0):
        if balance < 0:
            print("Бастапқы баланс теріс болмайды, 0 қойылды")
            balance = 0
        self.owner = owner.strip().title()
        self.balance = balance


acc = BankAccount("  дана серікқызы ", -500)
print(acc.owner, acc.balance)
```
Нәтижесі:
```text
Бастапқы баланс теріс болмайды, 0 қойылды
Дана Серікқызы 0
```
(Кәсіби бағдарламаларда мұндай жағдайда хабар шығарудың орнына қате «лақтырылады»: `raise ValueError("Баланс теріс болмауы керек")` — сонда қате объект мүлдем жасалмайды.)

## 3. Объект атрибуттары мен класс атрибуттары

| | Объект атрибуты | Класс атрибуты |
|---|---|---|
| Қай жерде жарияланады | `__init__` ішінде, `self.x = ...` | класс денесінде, әдістерден тыс |
| Кімге тиесілі | тек осы объектіге | барлық объектіге ортақ |
| Мысал | `self.name`, `self.age` | `species = "үй мысығы"`, `count = 0` |
| Қатынау | `murzik.name` | `Cat.species` немесе `murzik.species` |

```python
class Student:
    """Үйірме оқушысы."""
    club = "Python үйірмесі"     # класс атрибуты — бәріне ортақ
    count = 0                    # жасалған оқушылар саны

    def __init__(self, name, grade):
        self.name = name         # объект атрибуттары
        self.grade = grade
        Student.count += 1       # класс атрибутын өзгерту — класс атауы арқылы!
        self.student_id = Student.count


a = Student("Әлия", 8)
b = Student("Бекзат", 9)
print(a.club, b.club)
print(a.student_id, b.student_id, Student.count)
```
Нәтижесі:
```text
Python үйірмесі Python үйірмесі
1 2 2
```

Python атрибутты қалай іздейді: алдымен **объектінің** өз атрибуттарынан, табылмаса — **кластан**. Сондықтан `a.club` жұмыс істейді, бірақ `a.club = "..."` жазсаң, `a`-ға жеке атрибут қосылады, класс атрибуты өзгермейді.

## 4. Атрибуттармен жұмыс

### Оқу және өзгерту
```python
class Phone:
    """Смартфон."""

    def __init__(self, brand, model, battery=100):
        self.brand = brand
        self.model = model
        self.battery = battery


my_phone = Phone("Samsung", "Galaxy A54")
print(my_phone.battery)
my_phone.battery -= 35          # өзгерту
my_phone.model = "Galaxy A55"   # ауыстыру
print(my_phone.model, my_phone.battery)
```
Нәтижесі:
```text
100
Galaxy A55 65
```

### Объектінің ішіне қарау: `vars()` және `__dict__`
```python
print(vars(my_phone))
print(my_phone.__dict__)
```
Нәтижесі:
```text
{'brand': 'Samsung', 'model': 'Galaxy A55', 'battery': 65}
{'brand': 'Samsung', 'model': 'Galaxy A55', 'battery': 65}
```
Объект атрибуттары ішінде — сөздік! ОБП мен сөздіктер осылай байланысты. VS Code отладчигінде де объектінің атрибуттары дәл осылай көрінеді.

### Атрибуттармен динамикалық жұмыс
```python
print(hasattr(my_phone, "color"))        # атрибут бар ма?
print(getattr(my_phone, "color", "белгісіз"))
setattr(my_phone, "color", "қара")
print(my_phone.color)
```
Нәтижесі:
```text
False
белгісіз
қара
```
Ереже: барлық атрибутты **`__init__` ішінде** жариялаған дұрыс — сонда класқа қарап, объектіде не бар екенін бірден көреміз. `setattr` пен класс сыртынан жаңа атрибут қосу — тек ерекше жағдайларда.

## 5. Объектілер — бөлек, бірақ сілтемелерге абай бол

```python
class Cat:
    """Мысық."""

    def __init__(self, name):
        self.name = name


a = Cat("Мұрзик")
b = a                 # жаңа объект ЕМЕС — сол объектіге екінші атау
b.name = "Барсик"
print(a.name)         # Барсик!

c = Cat("Мұрзик")
d = Cat("Мұрзик")
print(c is d, c.name == d.name)
```
Нәтижесі:
```text
Барсик
False True
```
- `b = a` — бір объектіге екі «жапсырма» (тізімдердегідей).
- Бірдей атрибуттары бар екі объект — бәрібір **екі бөлек** объект (`is` → `False`).

## 6. Объектінің ішіндегі объект (композиция — алғашқы түсінік)

Атрибуттың мәні кез келген тип бола алады — тізім, сөздік, тіпті **басқа кластың объектісі**:

```python
class Engine:
    """Қозғалтқыш."""

    def __init__(self, power, fuel):
        self.power = power      # ат күші
        self.fuel = fuel


class Car:
    """Автомобиль."""

    def __init__(self, brand, engine):
        self.brand = brand
        self.engine = engine    # атрибут — Engine объектісі


car = Car("Toyota", Engine(181, "бензин"))
print(car.brand, car.engine.power, car.engine.fuel)
```
Нәтижесі:
```text
Toyota 181 бензин
```
«Автомобильде қозғалтқыш **бар**» — бұл **композиция**. Объектілер жиынтығы да осылай құрылады: «кітапханада кітаптар бар» — атрибут ретінде `Book` объектілерінің тізімі.

## 7. Класты жобалаудан кодқа дейін

UML:
```text
┌────────────────────────────┐
│           Book             │
├────────────────────────────┤
│ title: str                 │
│ author: str                │
│ year: int                  │
│ pages: int                 │
│ is_available: bool = True  │
└────────────────────────────┘
```
Код:
```python
class Book:
    """Кітапхана кітабы."""

    def __init__(self, title, author, year, pages):
        self.title = title
        self.author = author
        self.year = year
        self.pages = pages
        self.is_available = True


books = [
    Book("Абай жолы", "Мұхтар Әуезов", 1942, 480),
    Book("Көшпенділер", "Ілияс Есенберлин", 1976, 960),
]
for book in books:
    status = "бар" if book.is_available else "берілген"
    print(f"«{book.title}» — {book.author}, {book.year} ж., {book.pages} бет ({status})")
```
Нәтижесі:
```text
«Абай жолы» — Мұхтар Әуезов, 1942 ж., 480 бет (бар)
«Көшпенділер» — Ілияс Есенберлин, 1976 ж., 960 бет (бар)
```

## Жиі кездесетін қателер

**1. `__init__` атауын қате жазу** (`__int__`, `_init_`, `__init`). Python оны конструктор деп танымайды:
```text
TypeError: Cat() takes no arguments
```
Дұрысы: екі жағынан **екі** астын сызу — `__init__`.

**2. `self`-ті ұмыту.**
```python
class Cat:
    def __init__(self, name):
        name = name          # атрибут жасалмады! жай жергілікті айнымалы


class CatFixed:
    def __init__(self, name):
        self.name = name     # дұрыс
```
`Cat("Мұрзик").name` → `AttributeError: 'Cat' object has no attribute 'name'`.

**3. `__init__` анықтамасында `self` параметрін жазбау.**
```text
def __init__(name):  →  TypeError: Cat.__init__() takes 1 positional argument but 2 were given
```

**4. Аргументтер санының сәйкес келмеуі.**
```text
Cat("Мұрзик")  →  TypeError: Cat.__init__() missing 2 required positional arguments: 'color' and 'age'
```

**5. `__init__`-тен мән қайтару.** Конструктор `return` арқылы мән қайтармауы керек (`None`-нан басқа) — `TypeError: __init__() should return None`.

**6. Өзгермелі мәнді класс атрибуты ретінде жариялау.**
```python
class Student:
    marks = []               # ҚАТЕ: барлық оқушыға ОРТАҚ тізім!

    def __init__(self, name):
        self.name = name


a = Student("Әлия")
b = Student("Бекзат")
a.marks.append(5)
print(b.marks)               # [5] — Бекзатқа да «баға қойылды»
```
Дұрысы: `self.marks = []` — `__init__` ішінде.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Конструктор (`__init__`) | Объект жасалғанда автоматты шақырылатын әдіс |
| `self` | Әдістің ішінде «осы объектінің өзі» |
| Объект атрибуты | `self.x` — тек осы объектіге тиесілі мән |
| Класс атрибуты | Класс денесінде жарияланған, барлық объектіге ортақ мән |
| Инициализация | Объектінің бастапқы күйін орнату |
| Әдепкі мән | Аргумент берілмегенде қолданылатын мән |
| `vars()` / `__dict__` | Объект атрибуттарының сөздігі |
| Композиция | Объектінің атрибуты ретінде басқа объектінің болуы |

## Ескерту

- `__init__` — объектіні жасамайды, тек оны баптайды (инициализациялайды). Объектіні `__new__` әдісі жасайды; оны өзгерту өте сирек қажет.
- Атрибуттарды тек деректерді сақтауға арналған кластар үшін `dataclasses` модулі (Python 3.7+) ыңғайлы: `@dataclass` декораторы `__init__`, `__repr__` және `__eq__` әдістерін автоматты түрде жасайды. Құжаттама: https://docs.python.org/3/library/dataclasses.html.
- Әдепкі мән ретінде өзгермелі объектіні қолданба: `def __init__(self, marks=[])` — тізім барлық объектіге ортақ болып қалады. Дұрыс нұсқа: `marks=None`, ал ішінде `self.marks = marks if marks is not None else []`.
- `print(obj)` әдепкі бойынша `<__main__.Cat object at 0x...>` шығарады. Түсінікті мәтін үшін класқа `__str__` немесе `__repr__` әдісі қосылады.
- PEP 8 бойынша барлық атрибуттарды `__init__` ішінде анықтау ұсынылады — Pylance және басқа талдағыштар `__init__`-тен тыс жасалған атрибуттар туралы ескертуі мүмкін.
