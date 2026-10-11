# Сабақ 14. property декораторы. Геттер мен сеттер әдістері.

## Тақырыптар

1. Декоратор туралы алғашқы түсінік
2. `@property` — геттер
3. `@<атау>.setter` — сеттер
4. Қатеге жауап беру: `raise` және `try/except`
5. Тек оқуға арналған (read-only) қасиет
6. Есептелетін қасиет (computed property)
7. `@<атау>.deleter` (қысқаша)
8. `property()` функциясы — баламалы жазу
9. Қашан не қолданамыз?
10. Жиі кездесетін қателер
11. Негізгі ұғымдар

## 1. Декоратор туралы алғашқы түсінік

**Декоратор** — басқа функцияны «орап», оған жаңа мүмкіндік қосатын функция. Ол функция анықтамасының үстіне `@атауы` түрінде жазылады.

Аналогия: сыйлықты қағазға орау. Сыйлықтың өзі өзгермейді, бірақ ол басқаша «көрінеді» және басқаша беріледі.

```python
def shout(func):
    """Функция нәтижесін бас әріппен және леппен қайтаратын декоратор."""
    def wrapper(name):
        return func(name).upper() + "!"
    return wrapper


@shout
def greet(name):
    return f"Сәлем, {name}"


print(greet("Ерлан"))
```
Нәтижесі:
```text
СӘЛЕМ, ЕРЛАН!
```
`@shout` жазбасы `greet = shout(greet)` дегенмен бірдей. Атрибуттарға қатынауды басқару үшін Python-ның **дайын** `@property` декораторы қолданылады. `@staticmethod` та — дайын декоратор.

## 2. `@property` — геттер

`@property` әдісті **атрибут сияқты** оқуға мүмкіндік береді: жақшасыз шақырылады.

```python
class Student:
    """Оқушы."""

    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def age(self):
        """Оқушының жасы."""
        return self._age


s = Student("Әлия", 14)
print(s.age)        # жақшасыз! ішінде age() әдісі шақырылды
# s.age = 15        # AttributeError: property 'age' of 'Student' object has no setter
```
Нәтижесі:
```text
14
```

Ескерту: property-нің ішкі деректері әдетте **бір астын сызумен** (`self._age`) сақталады — атауы property атауымен (`age`) соқтығыспауы үшін. `__age` те қолдануға болады, бірақ Python қауымдастығында property-мен `_age` жиі қолданылады.

## 3. `@<атау>.setter` — сеттер

Мәнді **меншіктеу** кезінде (`s.age = 15`) шақырылатын әдіс:

```python
class Student:
    """Оқушы (жасы тексерілетін)."""
    MIN_AGE = 6
    MAX_AGE = 18

    def __init__(self, name, age):
        self._name = name
        self.age = age            # конструкторда да сеттер арқылы — тексеріс жұмыс істейді!

    @property
    def age(self):
        """Оқушының жасы (6–18)."""
        return self._age

    @age.setter
    def age(self, value):
        if not isinstance(value, int):
            raise TypeError("Жас бүтін сан болуы керек")
        if not self.MIN_AGE <= value <= self.MAX_AGE:
            raise ValueError(f"Жас {self.MIN_AGE}–{self.MAX_AGE} аралығында болуы керек: {value}")
        self._age = value


s = Student("Әлия", 14)
s.age += 1                 # оқу (геттер) + жазу (сеттер)
print(s.age)
s.age = 40                 # ValueError
```
Нәтижесі:
```text
15
Traceback (most recent call last):
  ...
ValueError: Жас 6–18 аралығында болуы керек: 40
```

Құрылымы:
```text
@property                 ← геттер: obj.age
def age(self): ...

@age.setter               ← сеттер: obj.age = мән   (декоратор атауы = property атауы!)
def age(self, value): ...
```

Не болды:
- **Сыртқы код** үшін `age` — кәдімгі атрибут: `s.age`, `s.age = 15`, `s.age += 1`.
- **Іс жүзінде** әр оқу/жазу кезінде біздің әдістеріміз шақырылады және тексеріс орындалады.

## 4. Қатеге жауап беру: `raise` және `try/except`

Әдіс түріндегі сеттер қате мәнде `print` жасап, `False` қайтаруы мүмкін. Бірақ property-де меншіктеу (`s.age = 40`) ештеңе қайтармайды — сондықтан кәсіби тәсіл: **қате (ерекшелік, исключение, exception) лақтыру**.

| Ерекшелік | Қашан қолданамыз |
|---|---|
| `ValueError` | тип дұрыс, бірақ мәні жарамсыз (жас = 40, баға = 7) |
| `TypeError` | тип дұрыс емес (жас = "он төрт") |

Қатені **ұстау** — `try/except`:
```python
try:
    s.age = 40
except ValueError as error:
    print("Қате:", error)

try:
    s.age = "он төрт"
except (ValueError, TypeError) as error:
    print("Қате:", error)
print("Бағдарлама жалғасып жатыр, жасы:", s.age)
```
Нәтижесі:
```text
Қате: Жас 6–18 аралығында болуы керек: 40
Қате: Жас бүтін сан болуы керек
Бағдарлама жалғасып жатыр, жасы: 15
```
Қате болғанда сеттер `self._age`-ге жетпейді, сондықтан объект **дұрыс күйінде** қалады.

Пайдаланушы енгізуімен бірге:
```python
while True:
    try:
        s.age = int(input("Жасыңды енгіз: "))
        break
    except ValueError as error:
        print("Қайта енгіз:", error)
```
(`int("abc")` де `ValueError` береді — бір `except` екеуін де ұстайды.)

## 5. Тек оқуға арналған (read-only) қасиет

Сеттері жоқ property — тек оқуға болады. Банк шотының балансы — классикалық мысал:

```python
class BankAccount:
    """Банк шоты: баланс тек оқуға арналған."""

    def __init__(self, owner):
        self.owner = owner
        self._balance = 0

    @property
    def balance(self):
        """Ағымдағы баланс (тек оқу)."""
        return self._balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Сома оң болуы керек")
        self._balance += amount

    def withdraw(self, amount):
        if amount > self._balance:
            raise ValueError("Қаражат жеткіліксіз")
        self._balance -= amount


acc = BankAccount("Дана")
acc.deposit(10_000)
acc.withdraw(2_500)
print(acc.balance)
try:
    acc.balance = 1_000_000
except AttributeError as error:
    print("Болмайды:", error)
```
Нәтижесі:
```text
7500
Болмайды: property 'balance' of 'BankAccount' object has no setter
```
(Python 3.10-да хабарлама қысқарақ: `can't set attribute 'balance'`.)

## 6. Есептелетін қасиет (computed property)

`__init__` ішінде есептелген `area` атрибуты қабырғалар өзгергенде ескіріп қалады; `area()` әдісі мәселені шешеді, бірақ жақша талап етеді. Property — ең табиғи шешім: **ауданы — тіктөртбұрыштың қасиеті**, бірақ ол әр жолы есептеледі.

```python
class Rectangle:
    """Тіктөртбұрыш."""

    def __init__(self, width, height):
        self.width = width
        self.height = height

    @property
    def width(self):
        return self._width

    @width.setter
    def width(self, value):
        if value <= 0:
            raise ValueError("Ені оң болуы керек")
        self._width = value

    @property
    def height(self):
        return self._height

    @height.setter
    def height(self, value):
        if value <= 0:
            raise ValueError("Биіктігі оң болуы керек")
        self._height = value

    @property
    def area(self):
        """Ауданы — әр жолы ағымдағы өлшемдерден есептеледі."""
        return self._width * self._height

    @property
    def is_square(self):
        return self._width == self._height


r = Rectangle(3, 4)
print(r.area, r.is_square)
r.width = 4
print(r.area, r.is_square)
```
Нәтижесі:
```text
12 False
16 True
```

Есептелетін қасиет **сеттер арқылы** да мағыналы болуы мүмкін — мысалы, температура:
```python
class Temperature:
    """Температура: ішінде Цельсиймен сақталады."""

    def __init__(self, celsius=0.0):
        self.celsius = celsius

    @property
    def celsius(self):
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Абсолюттік нөлден төмен болмайды")
        self._celsius = value

    @property
    def fahrenheit(self):
        return self._celsius * 9 / 5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        self.celsius = (value - 32) * 5 / 9   # тексеріс celsius сеттерінде


t = Temperature(25)
print(t.fahrenheit)
t.fahrenheit = 212
print(t.celsius)
```
Нәтижесі:
```text
77.0
100.0
```
Деректер **бір жерде** (`_celsius`) сақталады, ал екі қасиет — оның әртүрлі «көріністері».

## 7. `@<атау>.deleter` (қысқаша)

`del obj.атау` кезінде шақырылатын әдіс — сирек қолданылады, мысалы, кэшті тазалау немесе «жою»-ға тыйым салу үшін:

```python
class Profile:
    """Пайдаланушы профилі."""

    def __init__(self, nickname):
        self._nickname = nickname

    @property
    def nickname(self):
        return self._nickname

    @nickname.deleter
    def nickname(self):
        print("Лақап атты жоюға болмайды — 'қонақ' деп қойылды")
        self._nickname = "қонақ"


p = Profile("pythonist")
del p.nickname
print(p.nickname)
```
Нәтижесі:
```text
Лақап атты жоюға болмайды — 'қонақ' деп қойылды
қонақ
```

## 8. `property()` функциясы — баламалы жазу

Декораторсыз да жазуға болады (ескі кодтарда кездеседі):

```python
class Box:
    """Жәшік."""

    def __init__(self, weight):
        self._weight = weight

    def get_weight(self):
        return self._weight

    def set_weight(self, value):
        if value < 0:
            raise ValueError("Салмақ теріс болмайды")
        self._weight = value

    weight = property(get_weight, set_weight)


b = Box(5)
b.weight = 12
print(b.weight)
```
Нәтижесі:
```text
12
```

## 9. Қашан не қолданамыз?

| Жағдай | Таңдау |
|---|---|
| Ережесіз қарапайым дерек (кітап атауы) | ашық атрибут `self.title` |
| Тексеріс керек мән (жас, баға) | `@property` + `@setter` |
| Сырттан өзгертуге болмайтын мән (баланс, ID) | `@property` (сеттерсіз) |
| Басқа атрибуттардан есептелетін мән, жылдам (аудан, орташа баға) | есептелетін `@property` |
| Ұзақ есептеу, параметрлері бар немесе «әрекет» (жүру, сату) | әдіс `obj.move(dx)` |

**Python тәсілі:** алдымен қарапайым ашық атрибут жаз. Кейін тексеріс керек болса, оны property-ге айналдыр — сыртқы код (`obj.age`) **өзгермейді**. Java-дағыдай әр атрибутқа алдын ала `get_x()/set_x()` жазу Python-да қабылданбаған.

Ереже: property **жылдам** әрі **жанама әсерсіз** болуы тиіс — бағдарламашы `obj.area` жазғанда файл жазылмайды, интернетке сұраныс кетпейді.

## Жиі кездесетін қателер

**1. Сеттерде property атауымен бірдей атрибутқа жазу — шексіз рекурсия.**
```python
class Student:
    @property
    def age(self):
        return self.age          # өзін-өзі шақырады!

    @age.setter
    def age(self, value):
        self.age = value         # сеттер өзін-өзі шақырады!
```
`RecursionError: maximum recursion depth exceeded`. Шешімі: ішкі атрибут басқа атпен — `self._age`.

**2. Сеттер декораторының атауы қате.**
`@age.setter` орнына `@setter` немесе `@property.setter` жазу → `NameError`/`AttributeError`. Сеттердің әдіс атауы да property атауымен **бірдей** болуы керек (`def age(self, value)`).

**3. Property-ді әдіс сияқты жақшамен шақыру.**
```text
r.area()  →  TypeError: 'int' object is not callable
```
Property — жақшасыз: `r.area`.

**4. Конструкторда сеттерді айналып өту.** `self._age = age` деп жазсаң, конструкторда тексеріс жүрмейді: `Student("Әлия", 100)` жасалып қалады. Дұрысы: `self.age = age`.

**5. Сеттерде `print` + `return False`.** Меншіктеу нәтижесін ешкім тексермейді — қате мән «үнсіз» қабылданбайды да, пайдаланушы мұны байқамайды. Кәсіби тәсіл — `raise ValueError(...)`.

**6. Тек геттер жазып, сеттерді «ұмыту».** Егер атрибутты өзгерту керек болса — `AttributeError: ... has no setter`. Сеттерсіз property — **әдейі** read-only болғанда ғана.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Декоратор | Функцияны «орап», жаңа мүмкіндік қосатын құрал, `@атауы` |
| `@property` | Әдісті атрибут сияқты оқуға мүмкіндік беретін декоратор (геттер) |
| `@x.setter` | `obj.x = мән` кезінде шақырылатын сеттер |
| `@x.deleter` | `del obj.x` кезінде шақырылатын әдіс |
| Қасиет (property) | Сырттан атрибут, ішінен — әдістер |
| Read-only қасиет | Сеттері жоқ, тек оқылатын қасиет |
| Есептелетін қасиет | Сақталмайтын, басқа деректерден есептелетін қасиет |
| Ерекшелік (exception) | Қате туралы сигнал: `ValueError`, `TypeError` |
| `raise` | Ерекшелікті лақтыру |
| `try / except` | Ерекшелікті ұстап, өңдеу |

## Ескерту

- Өз декораторыңды жазғанда ішкі функцияны `functools.wraps` арқылы безендір: `@functools.wraps(func)` — сонда бастапқы функцияның аты мен docstring-і сақталады.
- Есептеуі ұзақ, бірақ нәтижесі өзгермейтін қасиет үшін `functools.cached_property` (Python 3.8+) бар: мән бірінші қатынағанда есептеліп, кейін сақталады.
- Property класс деңгейінде анықталады: оны `__init__` ішінде `self.x = property(...)` деп жасау жұмыс істемейді.
- Сеттерсіз property-ге меншіктегендегі қате мәтіні нұсқаға байланысты: Python 3.11+ — `property 'balance' of 'BankAccount' object has no setter`, ескі нұсқаларда — `can't set attribute`.
- Ресми құжаттама: https://docs.python.org/3/library/functions.html#property және дескрипторлар бойынша нұсқаулық https://docs.python.org/3/howto/descriptor.html.
