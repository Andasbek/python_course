# Сабақ 11. Әдістер (методтар). self параметрінің рөлі.

## Тақырыптар

1. Әдіс деген не?
2. `self` параметрінің рөлі
3. Әдістердің түрлері (атқаратын қызметі бойынша)
4. Әдістің ішінен басқа әдісті шақыру
5. Әдістер және объектінің күйі: «шам» мысалы
6. Әдістерді тізбектеу (chaining)
7. Функция мен әдісті салыстыру
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Әдіс деген не?

**Әдіс** (метод, method) — класс ішінде жарияланған және объектінің атрибуттарымен жұмыс істейтін функция. Атрибуттар объектінің **не білетінін** сипаттаса, әдістер оның **не істей алатынын** сипаттайды.

```python
class Rectangle:
    """Тіктөртбұрыш."""

    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        """Ауданын қайтарады."""
        return self.width * self.height

    def perimeter(self):
        """Периметрін қайтарады."""
        return 2 * (self.width + self.height)


r = Rectangle(3, 4)
print(r.area(), r.perimeter())
r.width = 10
print(r.area(), r.perimeter())
```
Нәтижесі:
```text
12 14
40 28
```
Енді аудан әр шақыруда **ағымдағы** енінен есептеледі.

Әдістің құрылымы:
```text
class Rectangle:
    def area(self):          ← def + атауы + бірінші параметр self
        """docstring"""
        return self.width * self.height   ← self арқылы атрибуттарға қатынау

r.area()                     ← объект.әдіс() — жақшамен!
```

## 2. `self` параметрінің рөлі

### Мәселе: әдіс қай объектімен жұмыс істейді?
Әдіс класта **бір рет** жазылады, ал объектілер мыңдаған болуы мүмкін. `area()` әдісі «менің енім» дегенде — **қай** тіктөртбұрыштың енін алу керек?

Жауабы — `self`. Әдісті шақырғанда Python **нүктенің алдындағы объектіні** автоматты түрде бірінші аргумент ретінде береді:

```text
r1.area()   ──Python──►   Rectangle.area(r1)    →   self = r1
r2.area()   ──Python──►   Rectangle.area(r2)    →   self = r2
```

Дәлелдейік:
```python
r1 = Rectangle(2, 5)
r2 = Rectangle(7, 7)
print(r1.area(), Rectangle.area(r1))
print(r2.area(), Rectangle.area(r2))
```
Нәтижесі:
```text
10 10
49 49
```
Екі жазба — бірдей. Бірақ `r1.area()` — қысқа әрі түсінікті, сондықтан әрқашан осылай жазамыз.

### Аналогиялар
- **Пульт және теледидар.** Барлық теледидардың «дыбысты көтеру» батырмасы бірдей жұмыс істейді. Бірақ сен батырманы **өз** теледидарыңа бағыттағанда, тек сол теледидардың дыбысы көтеріледі. `self` — «пульт бағытталған теледидар».
- **«Мен» сөзі.** Сыныпта әр оқушы «менің атым ...» дейді. «Мен» сөзі бәріне ортақ, бірақ әр адамның аузында — сол адамның өзі. `self` — объектінің «мен»-і.

### `self` туралы ережелер
1. `self` — әр әдістің **бірінші** параметрі (тіпті басқа параметр болмаса да).
2. Шақырғанда `self`-ке аргумент **бермейміз** — Python өзі береді.
3. Әдіс ішінде объектінің атрибуттары мен басқа әдістеріне **тек** `self.` арқылы қатынаймыз.
4. `self` — кілт сөз емес, келісім (конвенция). Басқаша атауға болады, бірақ **ешқашан** олай істеме — барлық Python бағдарламашылары `self` деп жазады.

```python
class Counter:
    """Санауыш."""

    def __init__(self):
        self.value = 0

    def click(self):
        """Мәнді 1-ге арттырады."""
        self.value += 1
        print(f"id={id(self)}, мән={self.value}")


c1 = Counter()
c2 = Counter()
c1.click()
c1.click()
c2.click()
print(id(c1) == id(c2))
```
Нәтижесі (id сандары әртүрлі болады):
```text
id=140234, мән=1
id=140234, мән=2
id=140987, мән=1
False
```
`self`-тің id-сі шақырған объектінің id-сімен бірдей: `c1.click()` кезінде `self` — `c1`, `c2.click()` кезінде — `c2`.

## 3. Әдістердің түрлері (атқаратын қызметі бойынша)

| Түрі | Не істейді | Мысал |
|---|---|---|
| Сұрау әдісі (query) | атрибуттардан мән есептеп **қайтарады**, күйді өзгертпейді | `area()`, `average()`, `is_empty()` |
| Өзгерту әдісі (command) | объектінің **күйін өзгертеді** | `deposit()`, `move()`, `add_mark()` |
| Көрсету әдісі | ақпаратты экранға шығарады | `show()`, `print_info()` |

Жақсы стиль: әдіс **не** мән қайтарсын, **не** күйді өзгертсін — екеуін араластырмаған дұрыс (бірақ «сәтті болды ма» деген `True/False` қайтару — жиі кездесетін ерекшелік).

### Параметрлері бар әдістер
```python
class BankAccount:
    """Банк шоты."""

    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance
        self.history = []

    def deposit(self, amount):
        """Шотты толтырады."""
        if amount <= 0:
            print("Сома оң болуы керек")
            return
        self.balance += amount
        self.history.append(f"+{amount}")

    def withdraw(self, amount):
        """Шоттан ақша шешеді. Сәтті болса True қайтарады."""
        if amount > self.balance:
            print(f"{self.owner}: қаражат жеткіліксіз (балансы {self.balance} ₸)")
            return False
        self.balance -= amount
        self.history.append(f"-{amount}")
        return True

    def show(self):
        """Шот туралы ақпаратты шығарады."""
        print(f"{self.owner}: {self.balance} ₸, операциялар: {self.history}")


dana = BankAccount("Дана", 5000)
dana.deposit(15000)
dana.withdraw(3000)
dana.withdraw(50000)
dana.show()
```
Нәтижесі:
```text
Дана: қаражат жеткіліксіз (балансы 17000 ₸)
Дана: 17000 ₸, операциялар: ['+15000', '-3000']
```

Назар аудар: шоттың балансын тікелей `dana.balance -= 50000` деп өзгертсек, тексеріс болмайды — баланс теріс болып кетеді. `withdraw()` әдісі арқылы өзгерту — **қауіпсіз**. Бұл — инкапсуляцияның негізі.

### Объектіні параметр ретінде қабылдайтын әдіс
Әдіс **басқа** объектімен де жұмыс істей алады:

```python
class BankAccount:
    """Банк шоты (аударым мүмкіндігімен)."""

    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance

    def transfer(self, other, amount):
        """Басқа шотқа (other) ақша аударады."""
        if amount > self.balance:
            print("Аударым мүмкін емес")
            return False
        self.balance -= amount
        other.balance += amount
        print(f"{self.owner} → {other.owner}: {amount} ₸")
        return True


aliya = BankAccount("Әлия", 10000)
erlan = BankAccount("Ерлан", 2000)
aliya.transfer(erlan, 4000)
print(aliya.balance, erlan.balance)
```
Нәтижесі:
```text
Әлия → Ерлан: 4000 ₸
6000 6000
```
Мұнда `self` — ақша жіберуші, `other` — алушы.

## 4. Әдістің ішінен басқа әдісті шақыру

Әдістер бір-бірін `self.` арқылы шақыра алады — үлкен әрекетті кішкентай бөліктерге бөлеміз:

```python
class Student:
    """Үйірме оқушысы."""

    def __init__(self, name):
        self.name = name
        self.marks = []

    def add_mark(self, mark):
        """Баға қосады (1–5 аралығында ғана)."""
        if 1 <= mark <= 5:
            self.marks.append(mark)
        else:
            print(f"Қате баға: {mark}")

    def average(self):
        """Орташа бағаны қайтарады."""
        if not self.marks:
            return 0
        return sum(self.marks) / len(self.marks)

    def is_excellent(self):
        """Орташасы 4.5-тен жоғары болса True."""
        return self.average() >= 4.5

    def report(self):
        """Оқушы туралы есеп жолын қайтарады."""
        status = "үздік" if self.is_excellent() else "жақсы оқиды"
        return f"{self.name}: {self.average():.2f} ({status})"


aliya = Student("Әлия")
for mark in [5, 4, 5, 7, 5]:
    aliya.add_mark(mark)
print(aliya.report())
```
Нәтижесі:
```text
Қате баға: 7
Әлия: 4.75 (үздік)
```
`report()` → `is_excellent()` → `average()` — әр әдіс бір міндетті атқарады («бір функция — бір міндет» қағидасы).

## 5. Әдістер және объектінің күйі: «шам» мысалы

```python
class Lamp:
    """Жарықтығы реттелетін шам."""

    def __init__(self, room):
        self.room = room
        self.is_on = False
        self.brightness = 0

    def turn_on(self):
        """Шамды қосады (50% жарықпен)."""
        self.is_on = True
        self.brightness = 50

    def turn_off(self):
        """Шамды өшіреді."""
        self.is_on = False
        self.brightness = 0

    def set_brightness(self, level):
        """Жарықтықты 0–100 аралығында орнатады."""
        if not self.is_on:
            print(f"{self.room}: алдымен шамды қос!")
            return
        self.brightness = max(0, min(100, level))

    def status(self):
        """Күйін мәтінмен қайтарады."""
        if self.is_on:
            return f"{self.room}: қосулы, {self.brightness}%"
        return f"{self.room}: өшірулі"


kitchen = Lamp("Ас үй")
kitchen.set_brightness(80)
kitchen.turn_on()
kitchen.set_brightness(150)
print(kitchen.status())
kitchen.turn_off()
print(kitchen.status())
```
Нәтижесі:
```text
Ас үй: алдымен шамды қос!
Ас үй: қосулы, 100%
Ас үй: өшірулі
```
Әдістер объектінің **ережелерін** сақтайды: өшірулі шамның жарықтығын өзгертуге болмайды, жарықтық 100-ден аспайды.

## 6. Әдістерді тізбектеу (chaining)

Егер өзгерту әдісі `self`-ті қайтарса, әдістерді бір жолда тізбектеуге болады:

```python
class Robot:
    """Тор бойымен жүретін робот."""

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
Нәтижесі:
```text
(2, 4)
```

## 7. Функция мен әдісті салыстыру

| | Функция | Әдіс |
|---|---|---|
| Қай жерде жарияланады | модульде (класс сыртында) | класс ішінде |
| Бірінші параметр | кез келген | `self` |
| Шақыру | `area(r)` | `r.area()` |
| Деректерге қатынау | параметр арқылы | `self` арқылы атрибуттарға |
| Мысалдар | `len(x)`, `print()`, `max()` | `s.upper()`, `lst.append()`, `d.get()` |

## Жиі кездесетін қателер

**1. Әдісте `self` параметрін ұмыту.**
```python
class Dog:
    def bark():                 # self жоқ
        print("Ау-ау!")


rex = Dog()
# rex.bark()   # TypeError: Dog.bark() takes 0 positional arguments but 1 was given
```
Python `rex`-ті аргумент ретінде береді, бірақ әдіс оны қабылдамайды. Дұрысы: `def bark(self):`.

**2. Атрибутқа `self`-сіз қатынау.**
```python
class Circle:
    def __init__(self, r):
        self.r = r

    def area(self):
        return 3.14 * r ** 2    # NameError: name 'r' is not defined
```
Дұрысы: `self.r`.

**3. Әдісті жақшасыз шақыру.**
```python
r = Rectangle(3, 4)
print(r.area)     # <bound method Rectangle.area of ...> — шақырылмады!
print(r.area())   # 12
```

**4. Әдіс ішінде басқа әдісті `self`-сіз шақыру.**
`average()` деп жазсаң — `NameError`. Дұрысы: `self.average()`.

**5. Мән қайтармайтын әдістің нәтижесін пайдалану.**
```python
acc = BankAccount("Дана", 100)
result = acc.show()   # show() print жасайды, бірақ return жоқ
print(result)         # None
```

**6. Әдістің атауын атрибуттың атауымен бірдей беру.**
`self.area = 12` және `def area(self)` — атрибут әдісті «жауып» тастайды: `r.area()` → `TypeError: 'int' object is not callable`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Әдіс (метод) | Класс ішіндегі, объектінің әрекетін сипаттайтын функция |
| `self` | Әдісті шақырған объектіге сілтеме |
| Мінез-құлық (behavior) | Объектінің әдістер жиыны |
| Сұрау әдісі | Мән қайтаратын, күйді өзгертпейтін әдіс |
| Өзгерту әдісі | Объектінің күйін өзгертетін әдіс |
| Байланысқан әдіс (bound method) | Нақты объектіге «бекітілген» әдіс: `r.area` |
| Тізбектеу (chaining) | `self` қайтару арқылы әдістерді бір жолда шақыру |

## Ескерту

- `r.area` — «байланысқан әдіс» (bound method) объектісі: оны айнымалыға сақтап, кейін шақыруға болады: `f = r.area; print(f())`.
- `self` арқылы емес, класс арқылы шақырылатын әдістер де бар: `@classmethod` (бірінші параметрі — `cls`, класстың өзі) және `@staticmethod` (`self` те, `cls` те жоқ). Олар класс деңгейіндегі амалдар мен көмекші функциялар үшін қолданылады.
- `__str__`, `__len__`, `__eq__` сияқты «арнайы» (dunder) әдістер объектілерді `print()`, `len()`, `==` сияқты кірістірілген амалдармен жұмыс істеуге үйретеді. Тізімі: https://docs.python.org/3/reference/datamodel.html#special-method-names.
- PEP 8 бойынша әдіс атаулары функциялар сияқты `snake_case` түрінде жазылады, ал класс ішіндегі әдістер бір бос жолмен бөлінеді.
- Әдіс ішінде `print` пен `return`-ды араластырма: мәнді қайтаратын әдісті графикалық интерфейсте де, тестте де қайта қолдануға болады, ал `print` жасайтын әдісті — тек консольде.
