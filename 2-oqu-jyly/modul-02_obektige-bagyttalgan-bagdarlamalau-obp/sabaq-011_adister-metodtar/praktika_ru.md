# Урок 11. Практические задания: методы и self

## Базовый уровень

### Задание 1. Класс `Circle`
Атрибут: `radius`. Методы: `area()`, `circumference()` (длина окружности), `scale(k)` — увеличивает радиус в `k` раз. Используй `math.pi`, результаты округляй до 2 знаков.

```text
r = 3: площадь 28.27, длина 18.85
После увеличения в 2 раза: площадь 113.1, длина 37.7
```

### Задание 2. Класс `Student`
Атрибуты: `name`, `marks` (пустой список). Методы:
- `add_mark(mark)` — добавляет только оценку от 1 до 5, иначе предупреждает;
- `average()` — средний балл (если оценок нет — 0);
- `best_mark()` — самая высокая оценка (если оценок нет — `None`);
- `info()` — возвращает строку вида `"Алия: оценки [5, 4, 5], средний 4.67"`.

### Задание 3. `Thermostat` (термостат)
Атрибут: `temperature` (начальная 20). Методы: `warmer(step=1)`, `cooler(step=1)` — не выводят температуру за пределы 10–30; `status()` — «холодно» (< 18), «нормально» (18–24), «жарко» (> 24).

```text
20 °C — нормально
warmer(7) → 27 °C — жарко
warmer(10) → 30 °C — жарко (достигнут предел)
cooler(25) → 10 °C — холодно
```

### Задание 4. Исследуй `self`
Запусти код ниже и объясни результат каждой строки. Почему последняя строка выдаёт ошибку?

```python
class Greeter:
    def __init__(self, name):
        self.name = name

    def hello(self):
        print("Привет, я", self.name)


a = Greeter("Айдар")
b = Greeter("Мадина")
a.hello()
Greeter.hello(b)
b.hello()
Greeter.hello()
```

## Продвинутый уровень

### Задание 5. `GameHero` — игровой персонаж
Атрибуты: `name`, `hp` (100), `power` (по умолчанию 10), `level` (1), `xp` (0). Методы:
- `is_alive()` — `hp > 0`;
- `take_damage(amount)` — уменьшает `hp` (не ниже 0);
- `heal(amount)` — увеличивает `hp` (не выше 100);
- `attack(other)` — наносит другому персонажу урон величиной `power`; если тот погибает, добавляет себе 50 `xp`;
- `gain_xp(amount)` — за каждые накопленные 100 `xp` `level` +1, `power` +5 (уровень может вырасти сразу на несколько).

Напиши цикл «битвы», в котором два персонажа атакуют по очереди, пока один не погибнет.

```text
Батыр атакует Найза: у Найза 90 HP
Найза атакует Батыр: у Батыр 92 HP
...
Победитель: Батыр (уровень 1, XP 50)
```

### Задание 6. `ShoppingCart` — корзина покупок
Атрибут: `items` — словарь `{товар: (цена, количество)}`. Методы: `add(name, price, qty=1)` (если товар уже есть — увеличить количество), `remove(name)`, `total()`, `apply_promo(code)` — код `"STUDENT10"` даёт скидку 10% (только один раз), `receipt()` — возвращает красиво оформленный чек.

```text
Тетрадь      x5     750 ₸
Ручка        x3     270 ₸
Рюкзак       x1   12000 ₸
-------------------------
Скидка (10%): -1302 ₸
Итого:          11718 ₸
```

### Задание 7. `TextEditor` с цепочками
Атрибут: `text` (пустая строка). Методы `write(s)`, `upper()`, `replace(old, new)`, `strip()` должны возвращать `self`; также `show()`. Например:
```python
TextEditor().write("  привет, мир  ").strip().replace("мир", "python").upper().show()
```
```text
ПРИВЕТ, PYTHON
```

## ★ Олимпиадная / творческая задача

### Задание 8. `Fraction` — обыкновенные дроби
Класс `Fraction(numerator, denominator)`: конструктор **сокращает** дробь (НОД — `math.gcd`) и переносит знак в числитель; если знаменатель 0 — предупреждение. Методы: `add(other)`, `sub(other)`, `mul(other)`, `div(other)` — возвращают **новый** объект `Fraction`; `to_float()`; `show()` → `"3/4"`.

```text
1/2 + 1/3 = 5/6
3/4 * 2/9 = 1/6
6/-8 → -3/4
```

### Задание 9. `Elevator` — симуляция лифта
`Elevator(floors)`: атрибуты `current_floor` (1), `requests` (список-очередь), `direction`. Методы: `call(floor)` — вызов (без повторов, в пределах этажей), `step()` — переезд на один этаж или остановка, `run()` — вызывает `step()`, пока не выполнены все вызовы, и выводит каждый шаг. Алгоритм: двигайся к ближайшему вызову в текущем направлении; если вызовы по направлению закончились — меняй направление.

## Домашнее задание

1. По аналогии с заданием 1 напиши классы `Rectangle` и `Triangle` (по трём сторонам; формула Герона; метод `is_valid()` — неравенство треугольника).
2. Добавь хотя бы по 2 метода к классам своего проекта (каркас с 9–10-го уроков), сделай push на GitHub.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import math


class Circle:
    """Окружность."""

    def __init__(self, radius):
        self.radius = radius

    def area(self):
        """Возвращает площадь."""
        return round(math.pi * self.radius ** 2, 2)

    def circumference(self):
        """Возвращает длину окружности."""
        return round(2 * math.pi * self.radius, 2)

    def scale(self, k):
        """Увеличивает радиус в k раз."""
        self.radius *= k


c = Circle(3)
print(f"r = 3: площадь {c.area()}, длина {c.circumference()}")
c.scale(2)
print(f"После увеличения в 2 раза: площадь {c.area()}, длина {c.circumference()}")
```

**Задание 2**
```python
class Student:
    """Учащийся кружка."""

    def __init__(self, name):
        self.name = name
        self.marks = []

    def add_mark(self, mark):
        """Добавляет оценку от 1 до 5."""
        if 1 <= mark <= 5:
            self.marks.append(mark)
        else:
            print(f"Неверная оценка: {mark}")

    def average(self):
        """Возвращает средний балл."""
        return sum(self.marks) / len(self.marks) if self.marks else 0

    def best_mark(self):
        """Возвращает самую высокую оценку."""
        return max(self.marks) if self.marks else None

    def info(self):
        """Возвращает строку об ученике."""
        return f"{self.name}: оценки {self.marks}, средний {self.average():.2f}"


aliya = Student("Алия")
for m in (5, 4, 5):
    aliya.add_mark(m)
print(aliya.info(), aliya.best_mark())
```

**Задание 3**
```python
class Thermostat:
    """Комнатный термостат."""
    MIN_TEMP = 10
    MAX_TEMP = 30

    def __init__(self, temperature=20):
        self.temperature = temperature

    def _clamp(self):
        self.temperature = max(self.MIN_TEMP, min(self.MAX_TEMP, self.temperature))

    def warmer(self, step=1):
        self.temperature += step
        self._clamp()

    def cooler(self, step=1):
        self.temperature -= step
        self._clamp()

    def status(self):
        if self.temperature < 18:
            word = "холодно"
        elif self.temperature <= 24:
            word = "нормально"
        else:
            word = "жарко"
        return f"{self.temperature} °C — {word}"


t = Thermostat()
print(t.status())
t.warmer(7)
print(t.status())
t.cooler(25)
print(t.status())
```

**Задание 4** — `a.hello()` → «Привет, я Айдар»; `Greeter.hello(b)` → «Привет, я Мадина» (self передали вручную); `b.hello()` → то же; `Greeter.hello()` → `TypeError: Greeter.hello() missing 1 required positional argument: 'self'` — при вызове через класс объект не передан.

**Задание 5**
```python
class GameHero:
    """Игровой персонаж."""
    MAX_HP = 100

    def __init__(self, name, power=10):
        self.name = name
        self.hp = self.MAX_HP
        self.power = power
        self.level = 1
        self.xp = 0

    def is_alive(self):
        return self.hp > 0

    def take_damage(self, amount):
        self.hp = max(0, self.hp - amount)

    def heal(self, amount):
        self.hp = min(self.MAX_HP, self.hp + amount)

    def gain_xp(self, amount):
        old_level = self.level
        self.xp += amount
        self.level = 1 + self.xp // 100
        self.power += 5 * (self.level - old_level)

    def attack(self, other):
        other.take_damage(self.power)
        print(f"{self.name} атакует {other.name}: "
              f"у {other.name} {other.hp} HP")
        if not other.is_alive():
            self.gain_xp(50)


batyr = GameHero("Батыр", 10)
naiza = GameHero("Найза", 8)
attacker, defender = batyr, naiza
while batyr.is_alive() and naiza.is_alive():
    attacker.attack(defender)
    attacker, defender = defender, attacker

winner = batyr if batyr.is_alive() else naiza
print(f"Победитель: {winner.name} (уровень {winner.level}, XP {winner.xp})")
```

**Задание 6**
```python
class ShoppingCart:
    """Корзина покупок."""
    PROMO_CODES = {"STUDENT10": 0.10}

    def __init__(self):
        self.items = {}
        self.discount = 0

    def add(self, name, price, qty=1):
        if name in self.items:
            old_price, old_qty = self.items[name]
            self.items[name] = (old_price, old_qty + qty)
        else:
            self.items[name] = (price, qty)

    def remove(self, name):
        self.items.pop(name, None)

    def subtotal(self):
        return sum(price * qty for price, qty in self.items.values())

    def apply_promo(self, code):
        if self.discount:
            print("Промокод уже применён")
            return False
        if code not in self.PROMO_CODES:
            print("Такого промокода нет")
            return False
        self.discount = self.PROMO_CODES[code]
        return True

    def total(self):
        return round(self.subtotal() * (1 - self.discount))

    def receipt(self):
        lines = [f"{name:<12} x{qty:<3}{price * qty:>6} ₸"
                 for name, (price, qty) in self.items.items()]
        lines.append("-" * 25)
        if self.discount:
            cut = self.subtotal() - self.total()
            lines.append(f"Скидка ({self.discount:.0%}): -{cut} ₸")
        lines.append(f"Итого: {self.total():>14} ₸")
        return "\n".join(lines)


cart = ShoppingCart()
cart.add("Тетрадь", 150, 5)
cart.add("Ручка", 90, 3)
cart.add("Рюкзак", 12000)
cart.apply_promo("STUDENT10")
print(cart.receipt())
```

**Задание 7**
```python
class TextEditor:
    """Текстовый редактор с методами-цепочками."""

    def __init__(self):
        self.text = ""

    def write(self, s):
        self.text += s
        return self

    def upper(self):
        self.text = self.text.upper()
        return self

    def replace(self, old, new):
        self.text = self.text.replace(old, new)
        return self

    def strip(self):
        self.text = self.text.strip()
        return self

    def show(self):
        print(self.text)
        return self


TextEditor().write("  привет, мир  ").strip().replace("мир", "python").upper().show()
```

**Задание 8**
```python
import math


class Fraction:
    """Обыкновенная дробь."""

    def __init__(self, numerator, denominator=1):
        if denominator == 0:
            print("Знаменатель не может быть нулём! Установлено 1.")
            denominator = 1
        if denominator < 0:
            numerator, denominator = -numerator, -denominator
        g = math.gcd(numerator, denominator)
        self.numerator = numerator // g
        self.denominator = denominator // g

    def add(self, other):
        return Fraction(self.numerator * other.denominator + other.numerator * self.denominator,
                        self.denominator * other.denominator)

    def sub(self, other):
        return Fraction(self.numerator * other.denominator - other.numerator * self.denominator,
                        self.denominator * other.denominator)

    def mul(self, other):
        return Fraction(self.numerator * other.numerator, self.denominator * other.denominator)

    def div(self, other):
        return Fraction(self.numerator * other.denominator, self.denominator * other.numerator)

    def to_float(self):
        return self.numerator / self.denominator

    def show(self):
        return f"{self.numerator}/{self.denominator}"


print("1/2 + 1/3 =", Fraction(1, 2).add(Fraction(1, 3)).show())
print("3/4 * 2/9 =", Fraction(3, 4).mul(Fraction(2, 9)).show())
print("6/-8 →", Fraction(6, -8).show())
```

**Задание 9**
```python
class Elevator:
    """Симуляция лифта."""

    def __init__(self, floors):
        self.floors = floors
        self.current_floor = 1
        self.requests = []
        self.direction = 1  # 1 — вверх, -1 — вниз

    def call(self, floor):
        if 1 <= floor <= self.floors and floor not in self.requests:
            self.requests.append(floor)

    def _has_requests_ahead(self):
        return any((f - self.current_floor) * self.direction > 0 for f in self.requests)

    def step(self):
        if self.current_floor in self.requests:
            self.requests.remove(self.current_floor)
            print(f"Этаж {self.current_floor}: двери открылись")
            return
        if not self._has_requests_ahead():
            self.direction *= -1
        self.current_floor += self.direction
        print(f"→ этаж {self.current_floor}")

    def run(self):
        while self.requests:
            self.step()


lift = Elevator(9)
for floor in (5, 3, 8, 2):
    lift.call(floor)
lift.run()
```

</details>
