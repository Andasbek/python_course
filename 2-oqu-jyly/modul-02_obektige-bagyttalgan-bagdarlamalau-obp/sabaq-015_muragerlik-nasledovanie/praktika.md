# Сабақ 15. Практикалық тапсырмалар: мұрагерлік, ата-класс және ұрпақ класс

Ескерту: бүгінгі тапсырмаларда ұрпақ кластар **өз `__init__`-ін жазбайды** және ата-класс әдістерін **қайта анықтамайды** — бұл келесі сабақтың тақырыбы (override, `super()`). Ұрпақтар тек жаңа әдістер мен класс атрибуттарын қосады.

## Базалық деңгей

### 1-тапсырма. Көлік құралдары
Ата-класс `Vehicle(brand, speed)`: атрибуттары `brand`, `speed`, `distance = 0`; әдістері `drive(hours)` — `distance`-ті `speed * hours`-қа арттырады, `info()`.

Ұрпақтар:
- `Car` — класс атрибуты `wheels = 4`, әдісі `honk()` → «Би-бип!»;
- `Bus` — `wheels = 6`, әдісі `announce(stop)` → «Келесі аялдама: ...»;
- `Bicycle` — `wheels = 2`, әдісі `ring()` → «Дың-дың!».

```text
Toyota: 90 км/сағ, жүрді 180 км
Toyota: Би-бип!
Yutong: Келесі аялдама: Абай метросы
Stels: 2 дөңгелек, Дың-дың!
```

### 2-тапсырма. Иерархияны зертте
1-тапсырманың кластары үшін:
- `Car.__mro__` шығар;
- `isinstance` арқылы: велосипед `Vehicle` ме? `Car` ме? `object` пе?
- `issubclass(Bus, Vehicle)`, `issubclass(Vehicle, Bus)`;
- `Bicycle("Stels", 20).honk()` неге қате береді?

### 3-тапсырма. Мектептегі адамдар
`Person(name, birth_year)` ата-класы: `age()`, `introduce()`. Ұрпақтар:
- `Student` — `study(subject)`, `ask_question()`;
- `Teacher` — `teach(subject, grade)`, `check_homework(student)` → «Гүлнар Серікқызы Әлияның үй тапсырмасын тексерді»;
- `Director` — `make_announcement(text)`.

Барлық адамды бір тізімге жинап, әрқайсысы өзін таныстырсын, содан кейін `isinstance` арқылы тек мұғалімдерге `teach` шақыр.

### 4-тапсырма. is-a немесе has-a?
Әр жұп үшін «мұрагерлік» немесе «композиция» деп жаз және негізде:
1. `Smartphone` – `Camera`
2. `Smartphone` – `Device`
3. `Square` – `Rectangle`
4. `Library` – `Book`
5. `Dog` – `Animal`
6. `Car` – `Wheel`
7. `AdminUser` – `User`
8. `Team` – `Player`

## Кеңейтілген деңгей

### 5-тапсырма. Ойын кейіпкерлері
Ата-класс `Hero(name)`: `hp = 100`, `level = 1`, `take_damage(n)`, `heal(n)`, `is_alive()`, `info()`. Ұрпақтар (әрқайсысында **өз** әдісі және класс атрибуты):
- `Warrior`: `damage = 20`; `strike(target)` — нысанаға `damage` зақым;
- `Mage`: `damage = 30`; `fireball(target)` — нысанаға зақым, бірақ өзі 5 HP жоғалтады;
- `Healer`: `heal_power = 25`; `heal_ally(ally)` — одақтасты емдейді.

Екі команда (әрқайсысында 3 кейіпкер) кезекпен әрекет ететін қарапайым шайқас жаз: әр кейіпкер өз түріне қарай әрекет етеді (`isinstance` арқылы анықта). Шайқас бір команданың барлық мүшесі өлгенше жүреді.

### 6-тапсырма. Банк шоттары
Ата-класс `BankAccount(owner, balance=0)`: инкапсуляцияланған `_balance`, `balance` (read-only property), `deposit`, `withdraw`. Ұрпақтар:
- `SavingsAccount` — `rate = 0.12` (жылдық), әдісі `add_interest(months)` — күрделі пайыз;
- `KidsAccount` — `daily_limit = 5000`, әдісі `spend(amount)` — лимиттен көп болса, бас тартады (бір күндегі шығынды санайды), `new_day()` — санауышты нөлдейді.

Ата-кластағы `_balance` атрибутын ұрпақтарда қалай қолданасың? Неге `__balance` емес?

### 7-тапсырма. Кодты рефакторинг
Төмендегі үш класта қайталанатын код бар. Ортақ ата-класты (`Shape`) бөліп шығар; әр пішінде тек өзіне тән әдістер қалсын. Бағдарламаның шығысы өзгермеуі керек.

```python
class Circle:
    def __init__(self, name, color):
        self.name = name
        self.color = color

    def describe(self):
        return f"{self.color} {self.name}"

    def paint(self, color):
        self.color = color

    def roll(self):
        return f"{self.name} домалап барады"


class Square:
    def __init__(self, name, color):
        self.name = name
        self.color = color

    def describe(self):
        return f"{self.color} {self.name}"

    def paint(self, color):
        self.color = color

    def stack(self):
        return f"{self.name} бір-бірінің үстіне қойылды"


class Triangle:
    def __init__(self, name, color):
        self.name = name
        self.color = color

    def describe(self):
        return f"{self.color} {self.name}"

    def paint(self, color):
        self.color = color

    def point(self):
        return f"{self.name} жоғарыны нұсқайды"


c = Circle("шеңбер", "қызыл")
c.paint("көк")
print(c.describe(), "|", c.roll())
print(Square("шаршы", "жасыл").stack())
print(Triangle("үшбұрыш", "сары").describe())
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Иерархия ағашын салу
Кез келген класс үшін оның **барлық ұрпақтарын** ағаш түрінде шығаратын `print_tree(cls, indent=0)` функциясын жаз (`cls.__subclasses__()` әдісін зертте). Кірістірілген `Exception` класына қолданып, Python-дағы ерекшеліктер иерархиясының алғашқы екі деңгейін шығар. Сондай-ақ кез келген класс үшін оның ата-кластар тізбегін `object`-ке дейін шығаратын `ancestors(cls)` функциясын `__bases__` арқылы (MRO-ны қолданбай) жаз.

```text
Animal
    Mammal
        Cat
        Dog
    Bird
```

### 9-тапсырма. «Тірі әлем» — эволюция симуляторы
`Creature(name, x)` ата-класы: `energy`, `move()`, `is_alive()`. Ұрпақтар: `Plant` (қозғалмайды, `grow()` — энергия жинайды), `Herbivore` (`eat(plant)`), `Predator` (`hunt(herbivore)`). 1D әлемде (0–20 позиция) 10 қадамдық симуляция жаса: әр қадамда әр тіршілік иесі өз түріне сай әрекет етеді, энергиясы 0-ге жеткендер өледі. Әр қадамнан кейін әлемді жолмен көрсет: `P` — өсімдік, `H` — шөпқоректі, `X` — жыртқыш, `.` — бос.

## Үй тапсырмасы

1. Өз жобаңда мұрагерлік қолдануға болатын жерді тап (мысалы: `Enemy` → `Zombie`, `Ghost`; `Task` → `HomeworkTask`, `ExamTask`). UML диаграммасын сал және кластарды жаз; push жаса.
2. 3-тапсырмадағы `Student` класына `marks` атрибуты керек болды ма? Оны конструкторда қалай қосар едің — келесі сабақтың сұрағы туралы ойлан (`super()`).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
class Vehicle:
    """Көлік құралы."""
    wheels = 0

    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed
        self.distance = 0

    def drive(self, hours):
        self.distance += self.speed * hours

    def info(self):
        return f"{self.brand}: {self.speed} км/сағ, жүрді {self.distance} км"


class Car(Vehicle):
    wheels = 4

    def honk(self):
        return f"{self.brand}: Би-бип!"


class Bus(Vehicle):
    wheels = 6

    def announce(self, stop):
        return f"{self.brand}: Келесі аялдама: {stop}"


class Bicycle(Vehicle):
    wheels = 2

    def ring(self):
        return f"{self.brand}: {self.wheels} дөңгелек, Дың-дың!"


car = Car("Toyota", 90)
car.drive(2)
print(car.info())
print(car.honk())
print(Bus("Yutong", 50).announce("Абай метросы"))
print(Bicycle("Stels", 20).ring())
```

**2-тапсырма**
```python
print([c.__name__ for c in Car.__mro__])        # ['Car', 'Vehicle', 'object']
bike = Bicycle("Stels", 20)
print(isinstance(bike, Vehicle), isinstance(bike, Car), isinstance(bike, object))  # True False True
print(issubclass(Bus, Vehicle), issubclass(Vehicle, Bus))  # True False
```
`honk()` тек `Car` класында бар; `Bicycle` — `Car`-дың ұрпағы емес, сондықтан `AttributeError`.

**3-тапсырма**
```python
class Person:
    """Мектептегі адам."""

    def __init__(self, name, birth_year):
        self.name = name
        self.birth_year = birth_year

    def age(self, current_year=2026):
        return current_year - self.birth_year

    def introduce(self):
        return f"Мен — {self.name}, {self.age()} жастамын."


class Student(Person):
    def study(self, subject):
        return f"{self.name} {subject} пәнін оқып отыр."

    def ask_question(self):
        return f"{self.name}: Мұғалім, сұрақ қоюға бола ма?"


class Teacher(Person):
    def teach(self, subject, grade):
        return f"{self.name} {grade}-сыныпқа {subject} пәнінен сабақ береді."

    def check_homework(self, student):
        return f"{self.name} {student.name} оқушының үй тапсырмасын тексерді."


class Director(Person):
    def make_announcement(self, text):
        return f"Директор {self.name}: {text}"


aliya = Student("Әлия", 2012)
people = [aliya, Teacher("Гүлнар Серікқызы", 1985), Director("Мұрат Қанатұлы", 1975)]
for p in people:
    print(p.introduce())
for p in people:
    if isinstance(p, Teacher):
        print(p.teach("информатика", 8))
        print(p.check_homework(aliya))
print(people[2].make_announcement("Ертең — ғылыми жобалар байқауы!"))
```

**4-тапсырма**: 1 — композиция (смартфонда камера бар); 2 — мұрагерлік (смартфон — құрылғы); 3 — мұрагерлік (шаршы — тіктөртбұрыштың дербес түрі; ескерту: егер шаршының ені мен биіктігін бөлек өзгертуге болса, бұл қиындық тудырады — талқылауға тұрарлық); 4 — композиция; 5 — мұрагерлік; 6 — композиция; 7 — мұрагерлік; 8 — композиция.

**5-тапсырма**
```python
import random


class Hero:
    """Ойын кейіпкері."""
    MAX_HP = 100

    def __init__(self, name):
        self.name = name
        self.hp = self.MAX_HP
        self.level = 1

    def take_damage(self, n):
        self.hp = max(0, self.hp - n)

    def heal(self, n):
        if self.is_alive():
            self.hp = min(self.MAX_HP, self.hp + n)

    def is_alive(self):
        return self.hp > 0

    def info(self):
        return f"{self.name} ({type(self).__name__}): {self.hp} HP"


class Warrior(Hero):
    damage = 20

    def strike(self, target):
        target.take_damage(self.damage)
        return f"{self.name} {target.name} кейіпкерін шапты (-{self.damage})"


class Mage(Hero):
    damage = 30

    def fireball(self, target):
        target.take_damage(self.damage)
        self.take_damage(5)
        return f"{self.name} {target.name} кейіпкеріне от шарын лақтырды (-{self.damage})"


class Healer(Hero):
    heal_power = 25

    def heal_ally(self, ally):
        ally.heal(self.heal_power)
        return f"{self.name} {ally.name} кейіпкерін емдеді (+{self.heal_power})"


def alive(team):
    return [h for h in team if h.is_alive()]


def act(hero, allies, enemies):
    """Кейіпкер өз түріне сай әрекет етеді."""
    if isinstance(hero, Healer):
        weakest = min(alive(allies), key=lambda h: h.hp)
        return hero.heal_ally(weakest)
    target = random.choice(alive(enemies))
    if isinstance(hero, Warrior):
        return hero.strike(target)
    if isinstance(hero, Mage):
        return hero.fireball(target)
    return f"{hero.name} күтіп тұр"


team_a = [Warrior("Батыр"), Mage("Мерлин"), Healer("Айгүл")]
team_b = [Warrior("Қарақұс"), Mage("Сұңқар"), Healer("Ақбөпе")]
round_no = 0
while alive(team_a) and alive(team_b) and round_no < 50:
    round_no += 1
    for allies, enemies in ((team_a, team_b), (team_b, team_a)):
        for hero in alive(allies):
            if alive(enemies):
                print(act(hero, allies, enemies))
print("Жеңді:", "А командасы" if alive(team_a) else "Б командасы")
```

**6-тапсырма**
```python
class BankAccount:
    """Банк шоты."""

    def __init__(self, owner, balance=0):
        self.owner = owner
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Сома оң болуы керек")
        self._balance += amount

    def withdraw(self, amount):
        if amount > self._balance:
            raise ValueError("Қаражат жеткіліксіз")
        self._balance -= amount


class SavingsAccount(BankAccount):
    rate = 0.12

    def add_interest(self, months):
        monthly = self.rate / 12
        for _ in range(months):
            self._balance = round(self._balance * (1 + monthly), 2)


class KidsAccount(BankAccount):
    daily_limit = 5000

    def spend(self, amount):
        spent = getattr(self, "_spent_today", 0)
        if spent + amount > self.daily_limit:
            print(f"Күндік лимит: {self.daily_limit} ₸, бүгін жұмсалды: {spent} ₸")
            return False
        self.withdraw(amount)
        self._spent_today = spent + amount
        return True

    def new_day(self):
        self._spent_today = 0


s = SavingsAccount("Дана", 100_000)
s.add_interest(12)
print(s.balance)
k = KidsAccount("Нұрлан", 20_000)
k.spend(3000)
k.spend(3000)
k.new_day()
k.spend(4000)
print(k.balance)
```
`__balance` ата-класта `_BankAccount__balance` болып бұрмаланады, ұрпақтағы `self.__balance` → `_SavingsAccount__balance` — басқа атрибут. Сондықтан ұрпақтар қолданатын деректерге `_balance`. (`getattr` арқылы `_spent_today`-ді «кейін» жасау — уақытша шешім; келесі сабақта оны `super().__init__()` арқылы конструкторда жариялаймыз.)

**7-тапсырма**
```python
class Shape:
    """Ортақ пішін."""

    def __init__(self, name, color):
        self.name = name
        self.color = color

    def describe(self):
        return f"{self.color} {self.name}"

    def paint(self, color):
        self.color = color


class Circle(Shape):
    def roll(self):
        return f"{self.name} домалап барады"


class Square(Shape):
    def stack(self):
        return f"{self.name} бір-бірінің үстіне қойылды"


class Triangle(Shape):
    def point(self):
        return f"{self.name} жоғарыны нұсқайды"


c = Circle("шеңбер", "қызыл")
c.paint("көк")
print(c.describe(), "|", c.roll())
print(Square("шаршы", "жасыл").stack())
print(Triangle("үшбұрыш", "сары").describe())
```

**8-тапсырма**
```python
def print_tree(cls, indent=0, max_depth=None):
    """Класс пен оның ұрпақтарын ағаш түрінде шығарады."""
    print(" " * indent + cls.__name__)
    if max_depth is not None and indent // 4 >= max_depth:
        return
    for sub in cls.__subclasses__():
        print_tree(sub, indent + 4, max_depth)


def ancestors(cls):
    """Ата-кластар тізбегін object-ке дейін қайтарады (бірінші ата-класс бойынша)."""
    chain = [cls.__name__]
    while cls.__bases__:
        cls = cls.__bases__[0]
        chain.append(cls.__name__)
    return chain


class Animal: pass
class Mammal(Animal): pass
class Bird(Animal): pass
class Cat(Mammal): pass
class Dog(Mammal): pass


print_tree(Animal)
print_tree(Exception, max_depth=1)
print(" → ".join(ancestors(Cat)))
```

**9-тапсырма**
```python
import random

WORLD_SIZE = 21


class Creature:
    """Тіршілік иесі."""
    symbol = "?"

    def __init__(self, name, x):
        self.name = name
        self.x = x
        self.energy = 10

    def is_alive(self):
        return self.energy > 0

    def move(self):
        self.x = max(0, min(WORLD_SIZE - 1, self.x + random.choice((-1, 1))))
        self.energy -= 1


class Plant(Creature):
    symbol = "P"

    def grow(self):
        self.energy += 2


class Herbivore(Creature):
    symbol = "H"

    def eat(self, plant):
        if plant.is_alive() and abs(plant.x - self.x) <= 1:
            self.energy += 4
            plant.energy -= 4


class Predator(Creature):
    symbol = "X"

    def hunt(self, prey):
        if prey.is_alive() and abs(prey.x - self.x) <= 1:
            self.energy += 6
            prey.energy = 0


def draw(world):
    cells = ["."] * WORLD_SIZE
    for c in world:
        if c.is_alive():
            cells[c.x] = c.symbol
    return "".join(cells)


world = [Plant(f"P{i}", random.randrange(WORLD_SIZE)) for i in range(5)]
world += [Herbivore(f"H{i}", random.randrange(WORLD_SIZE)) for i in range(3)]
world += [Predator("X0", random.randrange(WORLD_SIZE))]

for step in range(1, 11):
    living = [c for c in world if c.is_alive()]
    for c in living:
        if isinstance(c, Plant):
            c.grow()
            continue
        c.move()
        if isinstance(c, Herbivore):
            for p in living:
                if isinstance(p, Plant):
                    c.eat(p)
        elif isinstance(c, Predator):
            for h in living:
                if isinstance(h, Herbivore):
                    c.hunt(h)
    print(f"{step:2}: {draw(world)}")
```

</details>
