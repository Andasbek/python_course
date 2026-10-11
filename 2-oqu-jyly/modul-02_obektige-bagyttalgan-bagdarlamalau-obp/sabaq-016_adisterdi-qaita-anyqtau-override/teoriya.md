# Сабақ 16. Әдістерді қайта анықтау (override). super() функциясы.

## Тақырыптар

1. Әдісті қайта анықтау (override) дегеніміз не?
2. Python әдісті қайдан іздейді? (MRO)
3. Әдісті толық ауыстыру мен кеңейту
4. super() функциясы
5. super().__init__() — ең жиі қолданылатын жағдай
6. super() әдістің нәтижесін қайтарғанда
7. Көп деңгейлі мұрагерлікте super()
8. Қайта анықтау және инкапсуляция, property
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Әдісті қайта анықтау (override) дегеніміз не?

**Әдісті қайта анықтау (переопределение, override)** — ұрпақ класта ата-класта бар әдісті **дәл сол атпен** қайта жазу. Сонда ұрпақ кластың объектісі үшін жаңа нұсқа жұмыс істейді.

Өмірлік аналогия: әкесі «тамақ пісіру» әдісін біледі — палау пісіреді. Ұлы да «тамақ пісіру» әдісін мұраға алды, бірақ ол бешбармақ пісіреді. Әрекеттің аты бірдей — нәтижесі басқа.

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        print(f"{self.name} бір дыбыс шығарды")


class Dog(Animal):
    def speak(self):          # ата-кластағы speak қайта анықталды
        print(f"{self.name}: Ау-ау!")


class Cat(Animal):
    def speak(self):
        print(f"{self.name}: Мяу!")


class Fish(Animal):
    pass                      # speak қайта анықталмаған


animals = [Dog("Бөрібасар"), Cat("Мысық"), Fish("Немо")]
for a in animals:
    a.speak()
```

Нәтижесі:
```text
Бөрібасар: Ау-ау!
Мысық: Мяу!
Немо бір дыбыс шығарды
```

`Fish` класында `speak` жоқ, сондықтан Python оны ата-кластан (`Animal`) алды.

## 2. Python әдісті қайдан іздейді? (MRO)

`obj.method()` шақырылғанда Python әдісті мына ретпен іздейді:

```text
1) объектінің өз класы  →  2) ата-класы  →  3) ата-кластың ата-класы ... → object
```

Бірінші табылған әдіс орындалады. Бұл рет **MRO (Method Resolution Order — әдістерді іздеу реті)** деп аталады. Оны көруге болады:

```python
class Animal:
    pass

class Dog(Animal):
    pass

print(Dog.__mro__)
```

Нәтижесі:
```text
(<class '__main__.Dog'>, <class '__main__.Animal'>, <class 'object'>)
```

Сызба:

```text
        object
          ▲
       Animal        speak() — жалпы нұсқа
          ▲
         Dog         speak() — қайта анықталған нұсқа  ← алдымен осы ізделеді
```

## 3. Әдісті толық ауыстыру мен кеңейту

Қайта анықтаудың екі жолы бар:

| Тәсіл | Не болады | Қашан қолданамыз |
|---|---|---|
| Толық ауыстыру | Ата-класс әдісі мүлдем орындалмайды | Әрекет түбегейлі басқаша болғанда |
| Кеңейту | Ата-класс әдісі орындалады + жаңа әрекет қосылады | Ата-кластың жұмысы керек, бірақ жеткіліксіз болғанда |

Кеңейту үшін ата-кластың әдісін ұрпақ класс ішінен шақыру керек. Мұны `super()` функциясы жасайды.

## 4. super() функциясы

`super()` — ата-класқа (дәлірек айтқанда MRO бойынша келесі класқа) сілтеме беретін арнайы функция. Ол арқылы ата-кластың әдісін шақыруға болады:

```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def info(self):
        print(f"Қызметкер: {self.name}, жалақы: {self.salary} тг")


class Teacher(Employee):
    def __init__(self, name, salary, subject):
        super().__init__(name, salary)   # ата-класс атрибуттарын толтырады
        self.subject = subject            # жаңа атрибут

    def info(self):
        super().info()                    # ата-кластың info әдісі
        print(f"  Пәні: {self.subject}")  # кеңейту


t = Teacher("Әсел Нұрланқызы", 350000, "Информатика")
t.info()
```

Нәтижесі:
```text
Қызметкер: Әсел Нұрланқызы, жалақы: 350000 тг
  Пәні: Информатика
```

### Неге `Employee.__init__(self, ...)` емес, `super()`?

Ескі тәсіл де жұмыс істейді:

```python
class Employee:
    def __init__(self, name):
        self.name = name

class Teacher(Employee):
    def __init__(self, name, subject):
        Employee.__init__(self, name)   # жұмыс істейді, бірақ ұсынылмайды
        self.subject = subject

print(Teacher("Ерлан", "Физика").name)
```

Нәтижесі:
```text
Ерлан
```

Бірақ `super()` жақсырақ, себебі:
- ата-класс атын қайталап жазудың қажеті жоқ (класс атын өзгертсек, кодты түзетпейміз);
- `self` параметрін қолмен бермейміз;
- көптік мұрагерлікте дұрыс жұмыс істейді.

## 5. super().__init__() — ең жиі қолданылатын жағдай

Ұрпақ класқа жаңа атрибут қосқымыз келсе, өз `__init__`-ін жазамыз. Бірақ ұрпақ класс `__init__`-і ата-кластың `__init__`-ін **жасырып** тастайды! Сондықтан ата-кластың атрибуттары құрылмай қалуы мүмкін:

```python
class Vehicle:
    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed

    def move(self):
        print(f"{self.brand} {self.speed} км/сағ жылдамдықпен жүріп келеді")


class ElectricCar(Vehicle):
    def __init__(self, brand, speed, battery):
        super().__init__(brand, speed)
        self.battery = battery

    def move(self):
        if self.battery > 0:
            super().move()
            self.battery -= 10
            print(f"  Батарея заряды: {self.battery}%")
        else:
            print(f"{self.brand}: батарея отырып қалды, зарядтау керек!")


car = ElectricCar("Tesla", 120, 20)
car.move()
car.move()
car.move()
```

Нәтижесі:
```text
Tesla 120 км/сағ жылдамдықпен жүріп келеді
  Батарея заряды: 10%
Tesla 120 км/сағ жылдамдықпен жүріп келеді
  Батарея заряды: 0%
Tesla: батарея отырып қалды, зарядтау керек!
```

Мұнда `move` әдісі **шартты түрде** кеңейтілген: батарея бар болса — ата-класс әрекеті, жоқ болса — мүлдем басқа әрекет.

## 6. super() әдістің нәтижесін қайтарғанда

Ата-класс әдісі мән қайтарса, оны `super()` арқылы алып, өзгертуге болады:

```python
class Product:
    def __init__(self, title, price):
        self.title = title
        self.price = price

    def final_price(self):
        return self.price


class DiscountProduct(Product):
    def __init__(self, title, price, discount):
        super().__init__(title, price)
        self.discount = discount   # пайызбен

    def final_price(self):
        base = super().final_price()
        return base - base * self.discount // 100


items = [Product("Дәптер", 400), DiscountProduct("Рюкзак", 12000, 25)]
for it in items:
    print(f"{it.title}: {it.final_price()} тг")
```

Нәтижесі:
```text
Дәптер: 400 тг
Рюкзак: 9000 тг
```

## 7. Көп деңгейлі мұрагерлікте super()

`super()` тізбек бойынша жоғары көтеріледі:

```python
class A:
    def hello(self):
        print("A сәлем береді")

class B(A):
    def hello(self):
        print("B сәлем береді")
        super().hello()

class C(B):
    def hello(self):
        print("C сәлем береді")
        super().hello()

C().hello()
```

Нәтижесі:
```text
C сәлем береді
B сәлем береді
A сәлем береді
```

## 8. Қайта анықтау және инкапсуляция, property

Ұрпақ класта ата-кластың жабық атрибуттарына (`__balance` сияқты) тікелей қол жеткізу қиын (атауды бұрмалау, name mangling). Сондықтан ата-класс әдістерін `super()` арқылы шақырған дұрыс:

```python
class Account:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    def withdraw(self, amount):
        if amount <= self._balance:
            self._balance -= amount
            return True
        return False


class StudentAccount(Account):
    LIMIT = 5000   # бір реттік шектеу

    def withdraw(self, amount):
        if amount > self.LIMIT:
            print(f"Оқушы картасынан бір ретте {self.LIMIT} тг-ден артық алуға болмайды")
            return False
        return super().withdraw(amount)


acc = StudentAccount("Айгерім", 20000)
print(acc.withdraw(7000))
print(acc.withdraw(3000))
print("Қалдық:", acc.balance)
```

Нәтижесі:
```text
Оқушы картасынан бір ретте 5000 тг-ден артық алуға болмайды
False
True
Қалдық: 17000
```

## Жиі кездесетін қателер

**1. `super().__init__()` шақыруды ұмыту**

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        self.breed = breed          # name құрылмай қалды!

d = Dog("Тайган", "Тазы")
# print(d.name)  -> AttributeError: 'Dog' object has no attribute 'name'
```

Түзетілгені:

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)
        self.breed = breed

d = Dog("Тайган", "Тазы")
print(d.name, d.breed)
```

**2. `super()` ішіне `self` беру**

```python
# Қате: super().__init__(self, name)  -> TypeError: артық аргумент
```

`super()` арқылы шақырғанда `self` автоматты түрде беріледі: `super().__init__(name)`.

**3. Жақшаны ұмыту: `super.__init__(name)`**

`super` — функция, оны міндетті түрде шақыру керек: `super()`.

**4. Әдіс атын қате жазу**

Ұрпақ класта `speek` деп жазсаңыз, ол **жаңа** әдіс болады, ал `speak` қайта анықталмайды. Ата-кластағы атпен әріпке дейін бірдей жазыңыз.

**5. Параметрлер санын сәйкестендірмеу**

Ата-класс әдісі `area(self)` болса, ұрпақта `area(self, x)` деп жазсақ, ортақ кодта `obj.area()` шақырылғанда қате шығады. Қайта анықталған әдістің шақырылу тәсілі бірдей болғаны дұрыс.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| Қайта анықтау (override) | Ұрпақ класта ата-кластағы әдісті сол атпен қайта жазу |
| Кеңейту | Ата-класс әдісін шақырып, оған жаңа әрекет қосу |
| `super()` | Ата-класс (MRO бойынша келесі класс) әдістеріне жол ашатын функция |
| `super().__init__()` | Ата-класс конструкторын шақыру |
| MRO | Python әдісті іздейтін кластар реті (`Класс.__mro__`) |
| `object` | Python-дағы барлық кластардың ортақ ата-класы |

## Ескерту

- Python 3-те `super()` аргументсіз жазылады; ескі `super(Class, self)` түрі де жұмыс істейді, бірақ ол тек кластың кәдімгі әдісінен тыс жерде керек.
- `super()` жай ғана «ата-класқа» емес, **MRO бойынша келесі класқа** жүгінеді. Көптік мұрагерлікте бұл «көрші» класс болуы мүмкін, сондықтан мұндай иерархияда әр класс `super()` шақыруы керек.
- Ұрпақ класс `__init__` анықтамаса, ата-кластың `__init__`-і автоматты түрде қолданылады — тек `super().__init__(...)` үшін конструктор жазудың қажеті жоқ.
- Python 3.12-ден бастап қайта анықталған әдістерді анық белгілеуге арналған `@typing.override` декораторы бар: оны анализаторлар (mypy, pyright) тексереді, бағдарламаның орындалуына әсер етпейді.
- Құжаттама: https://docs.python.org/3/library/functions.html#super
