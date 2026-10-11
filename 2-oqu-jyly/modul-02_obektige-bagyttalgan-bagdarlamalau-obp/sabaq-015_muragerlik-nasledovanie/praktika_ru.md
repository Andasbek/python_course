# Урок 15. Практические задания: наследование, родительский и дочерний класс

Примечание: в сегодняшних заданиях дочерние классы **не пишут собственный `__init__`** и **не переопределяют** методы родительского класса — это тема следующего урока (override, `super()`). Потомки только добавляют новые методы и атрибуты класса.

## Базовый уровень

### Задание 1. Транспортные средства
Родительский класс `Vehicle(brand, speed)`: атрибуты `brand`, `speed`, `distance = 0`; методы `drive(hours)` — увеличивает `distance` на `speed * hours`, `info()`.

Потомки:
- `Car` — атрибут класса `wheels = 4`, метод `honk()` → «Би-бип!»;
- `Bus` — `wheels = 6`, метод `announce(stop)` → «Следующая остановка: ...»;
- `Bicycle` — `wheels = 2`, метод `ring()` → «Дзынь-дзынь!».

```text
Toyota: 90 км/ч, проехал 180 км
Toyota: Би-бип!
Yutong: Следующая остановка: метро Абай
Stels: 2 колеса, Дзынь-дзынь!
```

### Задание 2. Исследуй иерархию
Для классов из задания 1:
- выведи `Car.__mro__`;
- с помощью `isinstance`: является ли велосипед `Vehicle`? `Car`? `object`?
- `issubclass(Bus, Vehicle)`, `issubclass(Vehicle, Bus)`;
- почему `Bicycle("Stels", 20).honk()` выдаёт ошибку?

### Задание 3. Люди в школе
Родительский класс `Person(name, birth_year)`: `age()`, `introduce()`. Потомки:
- `Student` — `study(subject)`, `ask_question()`;
- `Teacher` — `teach(subject, grade)`, `check_homework(student)` → «Гүлнар Серікқызы проверил(а) домашнее задание ученика Әлия.»;
- `Director` — `make_announcement(text)`.

Собери всех людей в один список, пусть каждый представится, затем с помощью `isinstance` вызови `teach` только для учителей.

### Задание 4. is-a или has-a?
Для каждой пары напиши «наследование» или «композиция» и обоснуй:
1. `Smartphone` – `Camera`
2. `Smartphone` – `Device`
3. `Square` – `Rectangle`
4. `Library` – `Book`
5. `Dog` – `Animal`
6. `Car` – `Wheel`
7. `AdminUser` – `User`
8. `Team` – `Player`

## Продвинутый уровень

### Задание 5. Игровые персонажи
Родительский класс `Hero(name)`: `hp = 100`, `level = 1`, `take_damage(n)`, `heal(n)`, `is_alive()`, `info()`. Потомки (у каждого **свой** метод и атрибут класса):
- `Warrior`: `damage = 20`; `strike(target)` — наносит цели урон `damage`;
- `Mage`: `damage = 30`; `fireball(target)` — наносит урон цели, но сам теряет 5 HP;
- `Healer`: `heal_power = 25`; `heal_ally(ally)` — лечит союзника.

Напиши простую битву, в которой две команды (по 3 персонажа) действуют по очереди: каждый персонаж действует в зависимости от своего типа (определи через `isinstance`). Битва идёт, пока не погибнут все участники одной из команд.

### Задание 6. Банковские счета
Родительский класс `BankAccount(owner, balance=0)`: инкапсулированный `_balance`, `balance` (read-only property), `deposit`, `withdraw`. Потомки:
- `SavingsAccount` — `rate = 0.12` (годовых), метод `add_interest(months)` — сложные проценты;
- `KidsAccount` — `daily_limit = 5000`, метод `spend(amount)` — отказывает, если сумма превышает лимит (считает расходы за день), `new_day()` — обнуляет счётчик.

Как ты используешь атрибут `_balance` родительского класса в потомках? Почему не `__balance`?

### Задание 7. Рефакторинг кода
В трёх классах ниже есть повторяющийся код. Выдели общий родительский класс (`Shape`); в каждой фигуре должны остаться только присущие ей методы. Вывод программы не должен измениться.

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
        return f"{self.name} катится"


class Square:
    def __init__(self, name, color):
        self.name = name
        self.color = color

    def describe(self):
        return f"{self.color} {self.name}"

    def paint(self, color):
        self.color = color

    def stack(self):
        return f"{self.name}: поставлены друг на друга"


class Triangle:
    def __init__(self, name, color):
        self.name = name
        self.color = color

    def describe(self):
        return f"{self.color} {self.name}"

    def paint(self, color):
        self.color = color

    def point(self):
        return f"{self.name} указывает вверх"


c = Circle("круг", "красный")
c.paint("синий")
print(c.describe(), "|", c.roll())
print(Square("квадрат", "зелёный").stack())
print(Triangle("треугольник", "жёлтый").describe())
```

## ★ Олимпиадная / творческая задача

### Задание 8. Построение дерева иерархии
Напиши функцию `print_tree(cls, indent=0)`, которая выводит **всех потомков** любого класса в виде дерева (изучи метод `cls.__subclasses__()`). Примени её к встроенному классу `Exception` и выведи первые два уровня иерархии исключений в Python. Также напиши функцию `ancestors(cls)`, которая для любого класса выводит цепочку его родительских классов до `object` с помощью `__bases__` (не используя MRO).

```text
Animal
    Mammal
        Cat
        Dog
    Bird
```

### Задание 9. «Живой мир» — симулятор эволюции
Родительский класс `Creature(name, x)`: `energy`, `move()`, `is_alive()`. Потомки: `Plant` (не двигается, `grow()` — накапливает энергию), `Herbivore` (`eat(plant)`), `Predator` (`hunt(herbivore)`). Сделай симуляцию из 10 шагов в одномерном мире (позиции 0–20): на каждом шаге каждое существо действует в соответствии со своим типом, существа с энергией 0 погибают. После каждого шага выводи мир строкой: `P` — растение, `H` — травоядное, `X` — хищник, `.` — пусто.

## Домашнее задание

1. Найди в своём проекте место, где можно применить наследование (например: `Enemy` → `Zombie`, `Ghost`; `Task` → `HomeworkTask`, `ExamTask`). Нарисуй UML-диаграмму и напиши классы; сделай push.
2. Понадобился ли классу `Student` из задания 3 атрибут `marks`? Подумай, как бы ты добавил его в конструкторе — это вопрос следующего урока (`super()`).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
class Vehicle:
    """Транспортное средство."""
    wheels = 0

    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed
        self.distance = 0

    def drive(self, hours):
        self.distance += self.speed * hours

    def info(self):
        return f"{self.brand}: {self.speed} км/ч, проехал {self.distance} км"


class Car(Vehicle):
    wheels = 4

    def honk(self):
        return f"{self.brand}: Би-бип!"


class Bus(Vehicle):
    wheels = 6

    def announce(self, stop):
        return f"{self.brand}: Следующая остановка: {stop}"


class Bicycle(Vehicle):
    wheels = 2

    def ring(self):
        return f"{self.brand}: {self.wheels} колеса, Дзынь-дзынь!"


car = Car("Toyota", 90)
car.drive(2)
print(car.info())
print(car.honk())
print(Bus("Yutong", 50).announce("метро Абай"))
print(Bicycle("Stels", 20).ring())
```

**Задание 2**
```python
print([c.__name__ for c in Car.__mro__])        # ['Car', 'Vehicle', 'object']
bike = Bicycle("Stels", 20)
print(isinstance(bike, Vehicle), isinstance(bike, Car), isinstance(bike, object))  # True False True
print(issubclass(Bus, Vehicle), issubclass(Vehicle, Bus))  # True False
```
`honk()` есть только в классе `Car`; `Bicycle` — не потомок `Car`, поэтому `AttributeError`.

**Задание 3**
```python
class Person:
    """Человек в школе."""

    def __init__(self, name, birth_year):
        self.name = name
        self.birth_year = birth_year

    def age(self, current_year=2026):
        return current_year - self.birth_year

    def introduce(self):
        return f"Я — {self.name}, мой возраст — {self.age()}."


class Student(Person):
    def study(self, subject):
        return f"{self.name} изучает предмет «{subject}»."

    def ask_question(self):
        return f"{self.name}: Можно задать вопрос?"


class Teacher(Person):
    def teach(self, subject, grade):
        return f"{self.name} ведёт урок по предмету «{subject}» в {grade}-м классе."

    def check_homework(self, student):
        return f"{self.name} проверил(а) домашнее задание ученика {student.name}."


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
print(people[2].make_announcement("Завтра — конкурс научных проектов!"))
```

**Задание 4**: 1 — композиция (у смартфона есть камера); 2 — наследование (смартфон — устройство); 3 — наследование (квадрат — частный случай прямоугольника; примечание: если ширину и высоту квадрата можно менять по отдельности, это создаёт трудности — стоит обсудить); 4 — композиция; 5 — наследование; 6 — композиция; 7 — наследование; 8 — композиция.

**Задание 5**
```python
import random


class Hero:
    """Игровой персонаж."""
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
        return f"{self.name} ударил персонажа {target.name} (-{self.damage})"


class Mage(Hero):
    damage = 30

    def fireball(self, target):
        target.take_damage(self.damage)
        self.take_damage(5)
        return f"{self.name} бросил огненный шар в персонажа {target.name} (-{self.damage})"


class Healer(Hero):
    heal_power = 25

    def heal_ally(self, ally):
        ally.heal(self.heal_power)
        return f"{self.name} вылечил персонажа {ally.name} (+{self.heal_power})"


def alive(team):
    return [h for h in team if h.is_alive()]


def act(hero, allies, enemies):
    """Персонаж действует в соответствии со своим типом."""
    if isinstance(hero, Healer):
        weakest = min(alive(allies), key=lambda h: h.hp)
        return hero.heal_ally(weakest)
    target = random.choice(alive(enemies))
    if isinstance(hero, Warrior):
        return hero.strike(target)
    if isinstance(hero, Mage):
        return hero.fireball(target)
    return f"{hero.name} ждёт"


team_a = [Warrior("Батыр"), Mage("Мерлин"), Healer("Айгүл")]
team_b = [Warrior("Беркут"), Mage("Сокол"), Healer("Ақбөпе")]
round_no = 0
while alive(team_a) and alive(team_b) and round_no < 50:
    round_no += 1
    for allies, enemies in ((team_a, team_b), (team_b, team_a)):
        for hero in alive(allies):
            if alive(enemies):
                print(act(hero, allies, enemies))
print("Победила:", "команда А" if alive(team_a) else "команда Б")
```

**Задание 6**
```python
class BankAccount:
    """Банковский счёт."""

    def __init__(self, owner, balance=0):
        self.owner = owner
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Сумма должна быть положительной")
        self._balance += amount

    def withdraw(self, amount):
        if amount > self._balance:
            raise ValueError("Недостаточно средств")
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
            print(f"Дневной лимит: {self.daily_limit} ₸, сегодня потрачено: {spent} ₸")
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
`__balance` в родительском классе искажается до `_BankAccount__balance`, а `self.__balance` в потомке → `_SavingsAccount__balance` — это другой атрибут. Поэтому для данных, используемых потомками, — `_balance`. (Создание `_spent_today` «потом» через `getattr` — временное решение; на следующем уроке мы объявим его в конструкторе через `super().__init__()`.)

**Задание 7**
```python
class Shape:
    """Общая фигура."""

    def __init__(self, name, color):
        self.name = name
        self.color = color

    def describe(self):
        return f"{self.color} {self.name}"

    def paint(self, color):
        self.color = color


class Circle(Shape):
    def roll(self):
        return f"{self.name} катится"


class Square(Shape):
    def stack(self):
        return f"{self.name}: поставлены друг на друга"


class Triangle(Shape):
    def point(self):
        return f"{self.name} указывает вверх"


c = Circle("круг", "красный")
c.paint("синий")
print(c.describe(), "|", c.roll())
print(Square("квадрат", "зелёный").stack())
print(Triangle("треугольник", "жёлтый").describe())
```

**Задание 8**
```python
def print_tree(cls, indent=0, max_depth=None):
    """Выводит класс и его потомков в виде дерева."""
    print(" " * indent + cls.__name__)
    if max_depth is not None and indent // 4 >= max_depth:
        return
    for sub in cls.__subclasses__():
        print_tree(sub, indent + 4, max_depth)


def ancestors(cls):
    """Возвращает цепочку родительских классов до object (по первому родительскому классу)."""
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

**Задание 9**
```python
import random

WORLD_SIZE = 21


class Creature:
    """Живое существо."""
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
