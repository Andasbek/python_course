# Сабақ 16. Практикалық тапсырмалар: override және super()

## Базалық деңгей

### 1-тапсырма. Көлік дыбысы
`Transport` класын құрыңыз: `__init__(self, name)` және `sound()` әдісі «<name> қозғалып барады» деп шығарсын. Одан `Car`, `Train`, `Bicycle` ұрпақ кластарын жасаңыз. `Car` мен `Train` класында `sound()` әдісін қайта анықтаңыз, `Bicycle`-ді өзгертпеңіз.

Мысал шығару:
```text
Toyota: Би-биип!
Тальго: Ту-ту-у!
Stels қозғалып барады
```

### 2-тапсырма. Мұғалім мен оқушы
`Person(name, age)` класында `info()` әдісі «Аты: ..., жасы: ...» шығарсын. `Pupil(Person)` класына `grade` (сынып) атрибутын қосыңыз (`super().__init__` арқылы), `info()` әдісін `super().info()` арқылы кеңейтіп, «Сыныбы: ...» жолын қосыңыз.

Мысал шығару:
```text
Аты: Дана, жасы: 14
Сыныбы: 8
```

### 3-тапсырма. Қатені тап
Төмендегі кодты іске қосыңыз, қатені түсіндіріп, түзетіңіз:
```python
class Phone:
    def __init__(self, model):
        self.model = model

class Smartphone(Phone):
    def __init__(self, model, os_name):
        self.os_name = os_name

s = Smartphone("Samsung A54", "Android")
print(s.os_name)
# print(s.model)  # осы жолдың алдындағы # белгісін алып, іске қосып көріңіз
```

### 4-тапсырма. Жеңілдікпен баға
`Ticket(route, price)` класында `get_price()` бағаны қайтарсын. `StudentTicket(Ticket)` класында `get_price()` қайта анықталып, `super().get_price()` нәтижесінен 50% жеңілдік берсін. `PensionTicket` — 30% жеңілдік.

Мысал: `Ticket("Алматы–Астана", 18000)` → 18000, `StudentTicket(...)` → 9000, `PensionTicket(...)` → 12600.

## Кеңейтілген деңгей

### 5-тапсырма. Ойын кейіпкерлері
`Hero(name, hp, power)` класында `attack(other)` әдісі: `other.hp`-ден `power` азайтып, «X Y-ке N зиян келтірді» шығарсын. Ұрпақ кластар:
- `Warrior` — `attack` кеңейтіледі: ата-класс шабуылынан кейін 20% ықтималдықпен (немесе әр үшінші шабуылда) қайта шабуыл жасайды;
- `Mage` — қосымша `mana` атрибуты; `attack` шақырылса, `mana >= 10` болғанда `power * 2` зиян, мана 10-ға азаяды, әйтпесе қарапайым шабуыл (`super().attack`).

Екі кейіпкерді 3 раунд «шайқастырыңыз».

### 6-тапсырма. Үш деңгейлі мұрагерлік
`Shape → Rectangle → Square` тізбегін құрыңыз. Әр класта `describe()` әдісі өз жолын шығарып, `super().describe()` шақырсын. `Square(5).describe()` нәтижесі:
```text
Шаршы: қабырғасы 5
Тіктөртбұрыш: 5 x 5, ауданы 25
Геометриялық фигура
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 7-тапсырма. Логтайтын тізім
Python-ның кіріктірілген `list` класынан мұра алатын `LoggedList` класын жазыңыз. `append` әдісін қайта анықтаңыз: элемент қосылған сайын «Қосылды: x» деп шығарып, `super().append(x)` шақырсын. Сондай-ақ барлық қосылған элементтер санын сақтайтын `added_count` атрибутын жасаңыз (бұл үшін `__init__`-те `super().__init__()` шақыру керек).

## Үй тапсырмасы

1. Жыл бойғы жобаңыздың тақырыбына сай ата-класс пен кемінде 2 ұрпақ класс ойлап табыңыз. Әр ұрпақта кемінде бір әдісті қайта анықтаңыз, бірінде `super()` арқылы кеңейтіңіз.
2. Кодты `obp/override_hw.py` файлына сақтап, GitHub-қа `git commit -m "override және super мысалдары"` командасымен жүктеңіз.
3. Ойланыңыз: «Бір әдіс атауы — әртүрлі әрекет» идеясы тағы қай жерде пайдалы? (келесі сабақ — полиморфизм).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
class Transport:
    def __init__(self, name):
        self.name = name

    def sound(self):
        print(f"{self.name} қозғалып барады")


class Car(Transport):
    def sound(self):
        print(f"{self.name}: Би-биип!")


class Train(Transport):
    def sound(self):
        print(f"{self.name}: Ту-ту-у!")


class Bicycle(Transport):
    pass


for t in [Car("Toyota"), Train("Тальго"), Bicycle("Stels")]:
    t.sound()
```

**2-тапсырма**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def info(self):
        print(f"Аты: {self.name}, жасы: {self.age}")


class Pupil(Person):
    def __init__(self, name, age, grade):
        super().__init__(name, age)
        self.grade = grade

    def info(self):
        super().info()
        print(f"Сыныбы: {self.grade}")


Pupil("Дана", 14, 8).info()
```

**3-тапсырма.** `Smartphone.__init__` ата-класс конструкторын шақырмайды, сондықтан `model` атрибуты құрылмайды → `AttributeError`. Түзетуі:
```python
class Phone:
    def __init__(self, model):
        self.model = model

class Smartphone(Phone):
    def __init__(self, model, os_name):
        super().__init__(model)
        self.os_name = os_name

s = Smartphone("Samsung A54", "Android")
print(s.model, s.os_name)
```

**4-тапсырма**
```python
class Ticket:
    def __init__(self, route, price):
        self.route = route
        self.price = price

    def get_price(self):
        return self.price


class StudentTicket(Ticket):
    def get_price(self):
        return super().get_price() * 50 // 100


class PensionTicket(Ticket):
    def get_price(self):
        return super().get_price() * 70 // 100


for t in [Ticket("Алматы–Астана", 18000),
          StudentTicket("Алматы–Астана", 18000),
          PensionTicket("Алматы–Астана", 18000)]:
    print(type(t).__name__, t.get_price())
```

**5-тапсырма**
```python
class Hero:
    def __init__(self, name, hp, power):
        self.name = name
        self.hp = hp
        self.power = power

    def attack(self, other, damage=None):
        if damage is None:
            damage = self.power
        other.hp -= damage
        print(f"{self.name} {other.name}-ке {damage} зиян келтірді (қалды: {other.hp})")


class Warrior(Hero):
    def __init__(self, name, hp, power):
        super().__init__(name, hp, power)
        self.hits = 0

    def attack(self, other, damage=None):
        super().attack(other, damage)
        self.hits += 1
        if self.hits % 3 == 0:
            print(f"{self.name} қосымша соққы жасады!")
            super().attack(other, damage)


class Mage(Hero):
    def __init__(self, name, hp, power, mana):
        super().__init__(name, hp, power)
        self.mana = mana

    def attack(self, other, damage=None):
        if self.mana >= 10:
            self.mana -= 10
            print(f"{self.name} сиқыр қолданды! (мана: {self.mana})")
            super().attack(other, self.power * 2)
        else:
            super().attack(other)


w = Warrior("Батыр", 100, 12)
m = Mage("Сиқыршы", 80, 10, 20)
for r in range(1, 4):
    print(f"--- {r}-раунд ---")
    w.attack(m)
    m.attack(w)
```

**6-тапсырма**
```python
class Shape:
    def describe(self):
        print("Геометриялық фигура")


class Rectangle(Shape):
    def __init__(self, a, b):
        self.a = a
        self.b = b

    def describe(self):
        print(f"Тіктөртбұрыш: {self.a} x {self.b}, ауданы {self.a * self.b}")
        super().describe()


class Square(Rectangle):
    def __init__(self, a):
        super().__init__(a, a)

    def describe(self):
        print(f"Шаршы: қабырғасы {self.a}")
        super().describe()


Square(5).describe()
```

**7-тапсырма**
```python
class LoggedList(list):
    def __init__(self, *args):
        super().__init__(*args)
        self.added_count = 0

    def append(self, x):
        print(f"Қосылды: {x}")
        self.added_count += 1
        super().append(x)


nums = LoggedList([1, 2])
nums.append(3)
nums.append(4)
print(nums, nums.added_count)
```
Нәтижесі:
```text
Қосылды: 3
Қосылды: 4
[1, 2, 3, 4] 2
```

</details>
