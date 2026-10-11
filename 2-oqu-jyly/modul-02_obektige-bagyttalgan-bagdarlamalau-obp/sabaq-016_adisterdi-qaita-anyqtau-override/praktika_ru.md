# Урок 16. Практические задания: override и super()

## Базовый уровень

### Задание 1. Звук транспорта
Создайте класс `Transport`: `__init__(self, name)` и метод `sound()`, выводящий «<name> движется». Создайте от него дочерние классы `Car`, `Train`, `Bicycle`. В классах `Car` и `Train` переопределите метод `sound()`, `Bicycle` не изменяйте.

Пример вывода:
```text
Toyota: Би-биип!
Тальго: Ту-ту-у!
Stels движется
```

### Задание 2. Учитель и ученик
В классе `Person(name, age)` метод `info()` должен выводить «Имя: ..., возраст: ...». Добавьте в класс `Pupil(Person)` атрибут `grade` (класс) (через `super().__init__`), расширьте метод `info()` через `super().info()`, добавив строку «Класс: ...».

Пример вывода:
```text
Имя: Дана, возраст: 14
Класс: 8
```

### Задание 3. Найди ошибку
Запустите код ниже, объясните ошибку и исправьте её:
```python
class Phone:
    def __init__(self, model):
        self.model = model

class Smartphone(Phone):
    def __init__(self, model, os_name):
        self.os_name = os_name

s = Smartphone("Samsung A54", "Android")
print(s.os_name)
# print(s.model)  # уберите знак # перед этой строкой и попробуйте запустить
```

### Задание 4. Цена со скидкой
В классе `Ticket(route, price)` метод `get_price()` должен возвращать цену. В классе `StudentTicket(Ticket)` метод `get_price()` переопределяется и даёт скидку 50% от результата `super().get_price()`. `PensionTicket` — скидка 30%.

Пример: `Ticket("Алматы–Астана", 18000)` → 18000, `StudentTicket(...)` → 9000, `PensionTicket(...)` → 12600.

## Продвинутый уровень

### Задание 5. Игровые персонажи
В классе `Hero(name, hp, power)` метод `attack(other)`: уменьшает `other.hp` на `power` и выводит «X нанёс Y урон N». Дочерние классы:
- `Warrior` — `attack` расширяется: после атаки родительского класса с вероятностью 20% (или на каждой третьей атаке) атакует повторно;
- `Mage` — дополнительный атрибут `mana`; при вызове `attack`, если `mana >= 10`, урон `power * 2`, мана уменьшается на 10, иначе обычная атака (`super().attack`).

Проведите «бой» двух персонажей в 3 раунда.

### Задание 6. Трёхуровневое наследование
Постройте цепочку `Shape → Rectangle → Square`. В каждом классе метод `describe()` должен выводить свою строку и вызывать `super().describe()`. Результат `Square(5).describe()`:
```text
Квадрат: сторона 5
Прямоугольник: 5 x 5, площадь 25
Геометрическая фигура
```

## ★ Олимпиадная / творческая задача

### Задание 7. Логирующий список
Напишите класс `LoggedList`, наследующий встроенный класс Python `list`. Переопределите метод `append`: при каждом добавлении элемента он выводит «Добавлено: x» и вызывает `super().append(x)`. Также создайте атрибут `added_count`, хранящий количество всех добавленных элементов (для этого в `__init__` нужно вызвать `super().__init__()`).

## Домашнее задание

1. Придумайте по теме своего годового проекта родительский класс и не менее 2 дочерних классов. В каждом дочернем классе переопределите хотя бы один метод, в одном из них расширьте его через `super()`.
2. Сохраните код в файл `obp/override_hw.py` и загрузите на GitHub командой `git commit -m "примеры override и super"`.
3. Подумайте: где ещё полезна идея «одно имя метода — разные действия»? (следующий урок — полиморфизм).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
class Transport:
    def __init__(self, name):
        self.name = name

    def sound(self):
        print(f"{self.name} движется")


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

**Задание 2**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def info(self):
        print(f"Имя: {self.name}, возраст: {self.age}")


class Pupil(Person):
    def __init__(self, name, age, grade):
        super().__init__(name, age)
        self.grade = grade

    def info(self):
        super().info()
        print(f"Класс: {self.grade}")


Pupil("Дана", 14, 8).info()
```

**Задание 3.** `Smartphone.__init__` не вызывает конструктор родительского класса, поэтому атрибут `model` не создаётся → `AttributeError`. Исправление:
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

**Задание 4**
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

**Задание 5**
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
        print(f"{self.name} нанёс {other.name} урон {damage} (осталось: {other.hp})")


class Warrior(Hero):
    def __init__(self, name, hp, power):
        super().__init__(name, hp, power)
        self.hits = 0

    def attack(self, other, damage=None):
        super().attack(other, damage)
        self.hits += 1
        if self.hits % 3 == 0:
            print(f"{self.name} нанёс дополнительный удар!")
            super().attack(other, damage)


class Mage(Hero):
    def __init__(self, name, hp, power, mana):
        super().__init__(name, hp, power)
        self.mana = mana

    def attack(self, other, damage=None):
        if self.mana >= 10:
            self.mana -= 10
            print(f"{self.name} применил магию! (мана: {self.mana})")
            super().attack(other, self.power * 2)
        else:
            super().attack(other)


w = Warrior("Батыр", 100, 12)
m = Mage("Маг", 80, 10, 20)
for r in range(1, 4):
    print(f"--- Раунд {r} ---")
    w.attack(m)
    m.attack(w)
```

**Задание 6**
```python
class Shape:
    def describe(self):
        print("Геометрическая фигура")


class Rectangle(Shape):
    def __init__(self, a, b):
        self.a = a
        self.b = b

    def describe(self):
        print(f"Прямоугольник: {self.a} x {self.b}, площадь {self.a * self.b}")
        super().describe()


class Square(Rectangle):
    def __init__(self, a):
        super().__init__(a, a)

    def describe(self):
        print(f"Квадрат: сторона {self.a}")
        super().describe()


Square(5).describe()
```

**Задание 7**
```python
class LoggedList(list):
    def __init__(self, *args):
        super().__init__(*args)
        self.added_count = 0

    def append(self, x):
        print(f"Добавлено: {x}")
        self.added_count += 1
        super().append(x)


nums = LoggedList([1, 2])
nums.append(3)
nums.append(4)
print(nums, nums.added_count)
```
Результат:
```text
Добавлено: 3
Добавлено: 4
[1, 2, 3, 4] 2
```

</details>
