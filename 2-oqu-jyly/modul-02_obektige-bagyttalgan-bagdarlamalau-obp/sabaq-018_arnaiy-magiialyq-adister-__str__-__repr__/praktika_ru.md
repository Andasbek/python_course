# Урок 18. Практические задания: магические методы

## Базовый уровень

### Задание 1. Город
Создайте класс `City(name, population, region)`. `__str__` — «Алматы (Алматинская область): 2 200 000 жителей», `__repr__` — `City('Алматы', 2200000, 'Алматинская область')`. Составьте список из трёх городов и сравните результаты `print(city)` и `print(cities)`.

### Задание 2. Класс
В классе `Classroom(name)` должны быть список `students` и метод `add(name)`. `__len__` возвращает количество учеников, `__str__` — «Класс 8 «Ә»: учеников — 3».

Пример:
```text
>>> c = Classroom("8 «Ә»")
>>> c.add("Айжан"); c.add("Бекзат"); c.add("Нұрлан")
>>> len(c)
3
```

### Задание 3. Одинаковые товары
В классе `Product(code, name, price)` метод `__eq__` должен сравнивать товары только по `code` (штрихкоду). Проверьте:
```text
Product("4870001", "Молоко", 520) == Product("4870001", "Молоко 2.5%", 540)  -> True
Product("4870001", "Молоко", 520) == Product("4870002", "Кефир", 520)        -> False
Product("4870001", "Молоко", 520) == "Молоко"                                -> False (без ошибки!)
```

### Задание 4. Исправь ошибку
```python
class Timer:
    def __init__(self, seconds):
        self.seconds = seconds

    def __str__(self):
        m = self.seconds // 60
        s = self.seconds % 60
        print(f"{m:02}:{s:02}")

t = Timer(125)
# print(t)   # уберите знак # и посмотрите на ошибку
```
Исправьте так, чтобы `print(t)` → выводил `02:05`.

## Продвинутый уровень

### Задание 5. Вектор
Создайте класс `Vector(x, y)`: `__repr__`, `__eq__`, `__add__` (сложение векторов), `__mul__` (умножение на число), `__len__` нет — вместо него метод `length()` (модуль, `math.hypot`). Проверьте:
```text
Vector(1, 2) + Vector(3, 4)  -> Vector(4, 6)
Vector(1, 2) * 3             -> Vector(3, 6)
Vector(3, 4).length()        -> 5.0
```
Подумайте: почему `__len__` здесь не подходит? (Он возвращает только целое число.)

### Задание 6. Корзина
Класс `Cart`: метод `add(product, qty)`, `__len__` — общее количество **единиц** товара, `__contains__` — есть ли товар в корзине (`"Хлеб" in cart`), `__str__` — в виде чека (каждая строка: название × количество = сумма, в конце «Итого: ... тг»). Используйте класс `Product` из задания 3.

## ★ Олимпиадная / творческая задача

### Задание 7. Промежуток времени
Создайте класс `Duration(hours, minutes)`. Если минут больше 60, они должны автоматически переводиться в часы (нормализация в конструкторе). Магические методы: `__str__` («2 ч 15 мин»), `__repr__`, `__eq__`, `__lt__`, `__add__`. Пользователь вводит время, потраченное на домашнее задание за каждый день недели (`1 40`, `2 5`, ...). Программа должна вывести общее время, самый долгий день (`max`) и отсортированный список (`sorted`).

## Домашнее задание

1. Добавьте методы `__str__` и `__repr__` в классы, написанные на уроках 15–17 (в классы годового проекта).
2. Добавьте в один класс `__eq__` и `__lt__` и отсортируйте список объектов с помощью `sorted()`.
3. Подготовка к следующему практикуму: нарисуйте в тетради схему, какие классы могут быть в системе «Библиотека».

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
class City:
    def __init__(self, name, population, region):
        self.name = name
        self.population = population
        self.region = region

    def __str__(self):
        pop = f"{self.population:,}".replace(",", " ")
        return f"{self.name} ({self.region}): {pop} жителей"

    def __repr__(self):
        return f"City({self.name!r}, {self.population}, {self.region!r})"


cities = [City("Алматы", 2200000, "Алматинская область"),
          City("Шымкент", 1200000, "Туркестанская область"),
          City("Актау", 270000, "Мангистауская область")]
for c in cities:
    print(c)
print(cities)
```

**Задание 2**
```python
class Classroom:
    def __init__(self, name):
        self.name = name
        self.students = []

    def add(self, name):
        self.students.append(name)

    def __len__(self):
        return len(self.students)

    def __str__(self):
        return f"Класс {self.name}: учеников — {len(self)}"


c = Classroom("8 «Ә»")
c.add("Айжан")
c.add("Бекзат")
c.add("Нұрлан")
print(len(c))
print(c)
```

**Задание 3**
```python
class Product:
    def __init__(self, code, name, price):
        self.code = code
        self.name = name
        self.price = price

    def __eq__(self, other):
        if not isinstance(other, Product):
            return NotImplemented
        return self.code == other.code

    def __repr__(self):
        return f"Product({self.code!r}, {self.name!r}, {self.price})"


print(Product("4870001", "Молоко", 520) == Product("4870001", "Молоко 2.5%", 540))
print(Product("4870001", "Молоко", 520) == Product("4870002", "Кефир", 520))
print(Product("4870001", "Молоко", 520) == "Молоко")
```

**Задание 4.** `__str__` должен возвращать строку:
```python
class Timer:
    def __init__(self, seconds):
        self.seconds = seconds

    def __str__(self):
        m = self.seconds // 60
        s = self.seconds % 60
        return f"{m:02}:{s:02}"

print(Timer(125))
```

**Задание 5**
```python
import math

class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

    def __eq__(self, other):
        if not isinstance(other, Vector):
            return NotImplemented
        return self.x == other.x and self.y == other.y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __mul__(self, k):
        return Vector(self.x * k, self.y * k)

    def length(self):
        return math.hypot(self.x, self.y)


print(Vector(1, 2) + Vector(3, 4))
print(Vector(1, 2) * 3)
print(Vector(3, 4).length())
print(Vector(1, 1) == Vector(1, 1))
```

**Задание 6**
```python
class Product:
    def __init__(self, code, name, price):
        self.code = code
        self.name = name
        self.price = price


class Cart:
    def __init__(self):
        self.items = {}          # name -> [product, qty]

    def add(self, product, qty=1):
        if product.name in self.items:
            self.items[product.name][1] += qty
        else:
            self.items[product.name] = [product, qty]

    def __len__(self):
        return sum(qty for _, qty in self.items.values())

    def __contains__(self, name):
        return name in self.items

    def __str__(self):
        lines = []
        total = 0
        for p, qty in self.items.values():
            s = p.price * qty
            total += s
            lines.append(f"{p.name} × {qty} = {s} тг")
        lines.append(f"Итого: {total} тг")
        return "\n".join(lines)


cart = Cart()
cart.add(Product("1", "Хлеб", 250), 2)
cart.add(Product("2", "Молоко", 520))
cart.add(Product("1", "Хлеб", 250))
print(len(cart))
print("Хлеб" in cart, "Мясо" in cart)
print(cart)
```

**Задание 7**
```python
class Duration:
    def __init__(self, hours=0, minutes=0):
        total = hours * 60 + minutes
        self.hours = total // 60
        self.minutes = total % 60

    def total_minutes(self):
        return self.hours * 60 + self.minutes

    def __str__(self):
        return f"{self.hours} ч {self.minutes} мин"

    def __repr__(self):
        return f"Duration({self.hours}, {self.minutes})"

    def __eq__(self, other):
        if not isinstance(other, Duration):
            return NotImplemented
        return self.total_minutes() == other.total_minutes()

    def __lt__(self, other):
        return self.total_minutes() < other.total_minutes()

    def __add__(self, other):
        return Duration(0, self.total_minutes() + other.total_minutes())


days = ["Понедельник", "Вторник", "Среда", "Четверг", "Пятница"]
week = []
for d in days:
    h, m = map(int, input(f"{d} (часы минуты): ").split())
    week.append(Duration(h, m))

total = Duration()
for d in week:
    total = total + d
print("Всего:", total)
longest = max(week)
print("Самый долгий день:", days[week.index(longest)], "-", longest)
print("Отсортировано:", sorted(week))
```

</details>
