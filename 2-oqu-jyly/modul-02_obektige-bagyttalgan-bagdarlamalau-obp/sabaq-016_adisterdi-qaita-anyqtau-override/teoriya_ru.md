# Урок 16. Переопределение методов (override). Функция super().

## Темы

1. Что такое переопределение метода (override)?
2. Где Python ищет метод? (MRO)
3. Полная замена и расширение метода
4. Функция super()
5. super().__init__() — самый частый случай
6. super(), когда метод возвращает результат
7. super() при многоуровневом наследовании
8. Переопределение и инкапсуляция, property
9. Частые ошибки
10. Основные понятия

## 1. Что такое переопределение метода (override)?

**Переопределение метода (override)** — повторное написание в дочернем классе метода, который уже есть в родительском классе, **с тем же самым именем**. Тогда для объекта дочернего класса работает новая версия.

Жизненная аналогия: отец владеет «методом» «готовить еду» — он готовит плов. Сын тоже унаследовал метод «готовить еду», но он готовит бешбармак. Название действия одинаковое — результат разный.

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        print(f"{self.name} издал какой-то звук")


class Dog(Animal):
    def speak(self):          # speak из родительского класса переопределён
        print(f"{self.name}: Гав-гав!")


class Cat(Animal):
    def speak(self):
        print(f"{self.name}: Мяу!")


class Fish(Animal):
    pass                      # speak не переопределён


animals = [Dog("Бөрібасар"), Cat("Мурзик"), Fish("Немо")]
for a in animals:
    a.speak()
```

Результат:
```text
Бөрібасар: Гав-гав!
Мурзик: Мяу!
Немо издал какой-то звук
```

В классе `Fish` нет `speak`, поэтому Python взял его из родительского класса (`Animal`).

## 2. Где Python ищет метод? (MRO)

При вызове `obj.method()` Python ищет метод в следующем порядке:

```text
1) собственный класс объекта  →  2) родительский класс  →  3) родитель родительского класса ... → object
```

Выполняется первый найденный метод. Этот порядок называется **MRO (Method Resolution Order — порядок разрешения методов)**. Его можно посмотреть:

```python
class Animal:
    pass

class Dog(Animal):
    pass

print(Dog.__mro__)
```

Результат:
```text
(<class '__main__.Dog'>, <class '__main__.Animal'>, <class 'object'>)
```

Схема:

```text
        object
          ▲
       Animal        speak() — общая версия
          ▲
         Dog         speak() — переопределённая версия  ← ищется в первую очередь
```

## 3. Полная замена и расширение метода

Есть два способа переопределения:

| Способ | Что происходит | Когда применяем |
|---|---|---|
| Полная замена | Метод родительского класса вообще не выполняется | Когда действие принципиально другое |
| Расширение | Выполняется метод родительского класса + добавляется новое действие | Когда работа родительского класса нужна, но её недостаточно |

Для расширения нужно вызвать метод родительского класса изнутри дочернего класса. Это делает функция `super()`.

## 4. Функция super()

`super()` — специальная функция, которая даёт ссылку на родительский класс (точнее, на следующий класс по MRO). Через неё можно вызвать метод родительского класса:

```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def info(self):
        print(f"Сотрудник: {self.name}, зарплата: {self.salary} тг")


class Teacher(Employee):
    def __init__(self, name, salary, subject):
        super().__init__(name, salary)   # заполняет атрибуты родительского класса
        self.subject = subject            # новый атрибут

    def info(self):
        super().info()                    # метод info родительского класса
        print(f"  Предмет: {self.subject}")  # расширение


t = Teacher("Әсел Нұрланқызы", 350000, "Информатика")
t.info()
```

Результат:
```text
Сотрудник: Әсел Нұрланқызы, зарплата: 350000 тг
  Предмет: Информатика
```

### Почему `super()`, а не `Employee.__init__(self, ...)`?

Старый способ тоже работает:

```python
class Employee:
    def __init__(self, name):
        self.name = name

class Teacher(Employee):
    def __init__(self, name, subject):
        Employee.__init__(self, name)   # работает, но не рекомендуется
        self.subject = subject

print(Teacher("Ерлан", "Физика").name)
```

Результат:
```text
Ерлан
```

Но `super()` лучше, потому что:
- не нужно повторно писать имя родительского класса (если переименуем класс, код исправлять не придётся);
- параметр `self` не передаём вручную;
- он правильно работает при множественном наследовании.

## 5. super().__init__() — самый частый случай

Если хотим добавить в дочерний класс новый атрибут, пишем собственный `__init__`. Но `__init__` дочернего класса **скрывает** `__init__` родительского класса! Поэтому атрибуты родительского класса могут не создаться:

```python
class Vehicle:
    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed

    def move(self):
        print(f"{self.brand} едет со скоростью {self.speed} км/ч")


class ElectricCar(Vehicle):
    def __init__(self, brand, speed, battery):
        super().__init__(brand, speed)
        self.battery = battery

    def move(self):
        if self.battery > 0:
            super().move()
            self.battery -= 10
            print(f"  Заряд батареи: {self.battery}%")
        else:
            print(f"{self.brand}: батарея разрядилась, нужна зарядка!")


car = ElectricCar("Tesla", 120, 20)
car.move()
car.move()
car.move()
```

Результат:
```text
Tesla едет со скоростью 120 км/ч
  Заряд батареи: 10%
Tesla едет со скоростью 120 км/ч
  Заряд батареи: 0%
Tesla: батарея разрядилась, нужна зарядка!
```

Здесь метод `move` расширен **по условию**: если батарея заряжена — выполняется действие родительского класса, если нет — совсем другое действие.

## 6. super(), когда метод возвращает результат

Если метод родительского класса возвращает значение, его можно получить через `super()` и изменить:

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
        self.discount = discount   # в процентах

    def final_price(self):
        base = super().final_price()
        return base - base * self.discount // 100


items = [Product("Тетрадь", 400), DiscountProduct("Рюкзак", 12000, 25)]
for it in items:
    print(f"{it.title}: {it.final_price()} тг")
```

Результат:
```text
Тетрадь: 400 тг
Рюкзак: 9000 тг
```

## 7. super() при многоуровневом наследовании

`super()` поднимается вверх по цепочке:

```python
class A:
    def hello(self):
        print("A здоровается")

class B(A):
    def hello(self):
        print("B здоровается")
        super().hello()

class C(B):
    def hello(self):
        print("C здоровается")
        super().hello()

C().hello()
```

Результат:
```text
C здоровается
B здоровается
A здоровается
```

## 8. Переопределение и инкапсуляция, property

В дочернем классе трудно напрямую обратиться к закрытым атрибутам родительского класса (вроде `__balance`) из-за искажения имён (name mangling). Поэтому методы родительского класса правильнее вызывать через `super()`:

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
    LIMIT = 5000   # ограничение на одну операцию

    def withdraw(self, amount):
        if amount > self.LIMIT:
            print(f"С ученической карты нельзя снять больше {self.LIMIT} тг за один раз")
            return False
        return super().withdraw(amount)


acc = StudentAccount("Айгерім", 20000)
print(acc.withdraw(7000))
print(acc.withdraw(3000))
print("Остаток:", acc.balance)
```

Результат:
```text
С ученической карты нельзя снять больше 5000 тг за один раз
False
True
Остаток: 17000
```

## Частые ошибки

**1. Забыли вызвать `super().__init__()`**

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        self.breed = breed          # name не создан!

d = Dog("Тайган", "Тазы")
# print(d.name)  -> AttributeError: 'Dog' object has no attribute 'name'
```

Исправленный вариант:

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

**2. Передача `self` в `super()`**

```python
# Ошибка: super().__init__(self, name)  -> TypeError: лишний аргумент
```

При вызове через `super()` `self` передаётся автоматически: `super().__init__(name)`.

**3. Забыли скобки: `super.__init__(name)`**

`super` — функция, её обязательно нужно вызвать: `super()`.

**4. Ошибка в имени метода**

Если в дочернем классе написать `speek`, это будет **новый** метод, а `speak` не переопределится. Пишите имя точно так же, как в родительском классе, вплоть до буквы.

**5. Несовпадение количества параметров**

Если метод родительского класса — `area(self)`, а в дочернем написать `area(self, x)`, то при вызове `obj.area()` в общем коде возникнет ошибка. Переопределённый метод должен вызываться так же, как исходный.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Переопределение (override) | Повторное написание метода родительского класса в дочернем классе под тем же именем |
| Расширение | Вызов метода родительского класса и добавление к нему нового действия |
| `super()` | Функция, открывающая доступ к методам родительского класса (следующего класса по MRO) |
| `super().__init__()` | Вызов конструктора родительского класса |
| MRO | Порядок классов, в котором Python ищет метод (`Класс.__mro__`) |
| `object` | Общий родительский класс всех классов в Python |

## Примечание

- В Python 3 `super()` пишется без аргументов; старая форма `super(Class, self)` тоже работает, но нужна только вне обычного метода класса.
- `super()` обращается не просто к «родителю», а к **следующему классу в MRO**. При множественном наследовании это может быть «соседний» класс, поэтому в таких иерархиях каждый класс должен вызывать `super()`.
- Если дочерний класс не определяет `__init__`, автоматически используется `__init__` родителя — писать конструктор только ради `super().__init__(...)` не нужно.
- С Python 3.12 есть декоратор `@typing.override` для явной пометки переопределённых методов: его проверяют анализаторы (mypy, pyright), на выполнение программы он не влияет.
- Документация: https://docs.python.org/3/library/functions.html#super
