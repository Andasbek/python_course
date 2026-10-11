# Сабақ 11. Практикалық тапсырмалар: әдістер және self

## Базалық деңгей

### 1-тапсырма. `Circle` класы
Атрибуты: `radius`. Әдістері: `area()`, `circumference()` (ұзындығы), `scale(k)` — радиусты `k` есе үлкейтеді. `math.pi` қолдан, нәтижелерді 2 таңбаға дейін дөңгелекте.

```text
r = 3: аудан 28.27, ұзындық 18.85
2 есе үлкейтілгеннен кейін: аудан 113.1, ұзындық 37.7
```

### 2-тапсырма. `Student` класы
Атрибуттары: `name`, `marks` (бос тізім). Әдістері:
- `add_mark(mark)` — тек 1–5 аралығындағы бағаны қосады, әйтпесе ескертеді;
- `average()` — орташа баға (бағасы жоқ болса — 0);
- `best_mark()` — ең жоғары баға (бағасы жоқ болса — `None`);
- `info()` — `"Әлия: бағалары [5, 4, 5], орташа 4.67"` түріндегі жолды қайтарады.

### 3-тапсырма. `Thermostat` (термостат)
Атрибуты: `temperature` (бастапқы 20). Әдістері: `warmer(step=1)`, `cooler(step=1)` — температураны 10–30 аралығынан шығармайды; `status()` — «суық» (< 18), «қалыпты» (18–24), «ыстық» (> 24).

```text
20 °C — қалыпты
warmer(7) → 27 °C — ыстық
warmer(10) → 30 °C — ыстық (шекке жетті)
cooler(25) → 10 °C — суық
```

### 4-тапсырма. `self`-ті зертте
Төмендегі кодты іске қос және әр жолдың нәтижесін түсіндір. Неге соңғы жол қате береді?

```python
class Greeter:
    def __init__(self, name):
        self.name = name

    def hello(self):
        print("Сәлем, мен", self.name)


a = Greeter("Айдар")
b = Greeter("Мадина")
a.hello()
Greeter.hello(b)
b.hello()
Greeter.hello()
```

## Кеңейтілген деңгей

### 5-тапсырма. `GameHero` — ойын кейіпкері
Атрибуттары: `name`, `hp` (100), `power` (әдепкі 10), `level` (1), `xp` (0). Әдістері:
- `is_alive()` — `hp > 0`;
- `take_damage(amount)` — `hp`-ны азайтады (0-ден төмен түспейді);
- `heal(amount)` — `hp`-ны арттырады (100-ден аспайды);
- `attack(other)` — басқа кейіпкерге `power` көлемінде зақым келтіреді; егер ол өлсе, өзіне 50 `xp` қосады;
- `gain_xp(amount)` — әр 100 `xp` жиналғанда `level` +1, `power` +5 (бір мезгілде бірнеше деңгей өсуі мүмкін).

Екі кейіпкер арасында кезекпен шабуыл жасайтын «шайқас» цикл жаз: біреуі өлгенше.

```text
Батыр Найза кейіпкеріне шабуыл жасады: Найза — 90 HP
Найза Батыр кейіпкеріне шабуыл жасады: Батыр — 92 HP
...
Жеңімпаз: Батыр (деңгей 1, XP 50)
```

### 6-тапсырма. `ShoppingCart` — сатып алу себеті
Атрибуты: `items` — сөздік `{тауар: (бағасы, саны)}`. Әдістері: `add(name, price, qty=1)` (бар болса — санын арттыру), `remove(name)`, `total()`, `apply_promo(code)` — `"STUDENT10"` коды 10% жеңілдік береді (бір рет қана), `receipt()` — әдемі чек жолын қайтарады.

```text
Дәптер       x5    750 ₸
Қалам        x3    270 ₸
Сөмке        x1  12000 ₸
-------------------------
Жеңілдік (10%): -1302 ₸
Барлығы:       11718 ₸
```

### 7-тапсырма. Тізбектелетін `TextEditor`
Атрибуты: `text` (бос жол). Әдістері `self` қайтарсын: `write(s)`, `upper()`, `replace(old, new)`, `strip()`; және `show()`. Мысалы:
```python
TextEditor().write("  сәлем, әлем  ").strip().replace("әлем", "python").upper().show()
```
```text
СӘЛЕМ, PYTHON
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. `Fraction` — жай бөлшектер
`Fraction(numerator, denominator)` класы: конструктор бөлшекті **қысқартады** (ЕҮОБ — `math.gcd`) және таңбаны алымға шығарады; бөлімі 0 болса — ескерту. Әдістер: `add(other)`, `sub(other)`, `mul(other)`, `div(other)` — **жаңа** `Fraction` объектісін қайтарады; `to_float()`; `show()` → `"3/4"`.

```text
1/2 + 1/3 = 5/6
3/4 * 2/9 = 1/6
6/-8 → -3/4
```

### 9-тапсырма. `Elevator` — лифт симуляциясы
`Elevator(floors)`: атрибуттары `current_floor` (1), `requests` (кезек тізімі), `direction`. Әдістер: `call(floor)` — шақыру (қайталанбасын, қабат шегінде), `step()` — бір қабатқа жылжу немесе тоқтау, `run()` — барлық шақыруды орындағанша `step()` шақырады және әр қадамды шығарады. Алгоритм: ағымдағы бағытта ең жақын шақыруға бар, бағыттағы шақырулар бітсе — бағытты ауыстыр.

## Үй тапсырмасы

1. 1-тапсырмаға ұқсас `Rectangle` және `Triangle` (үш қабырғасы бойынша; Герон формуласы; `is_valid()` әдісі — үшбұрыш теңсіздігі) кластарын жаз.
2. Өз жобаңның кластарына (9–10-сабақтағы қаңқа) кемінде 2 әдістен қос, GitHub-қа push жаса.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import math


class Circle:
    """Шеңбер."""

    def __init__(self, radius):
        self.radius = radius

    def area(self):
        """Ауданын қайтарады."""
        return round(math.pi * self.radius ** 2, 2)

    def circumference(self):
        """Шеңбер ұзындығын қайтарады."""
        return round(2 * math.pi * self.radius, 2)

    def scale(self, k):
        """Радиусты k есе үлкейтеді."""
        self.radius *= k


c = Circle(3)
print(f"r = 3: аудан {c.area()}, ұзындық {c.circumference()}")
c.scale(2)
print(f"2 есе үлкейтілгеннен кейін: аудан {c.area()}, ұзындық {c.circumference()}")
```

**2-тапсырма**
```python
class Student:
    """Үйірме оқушысы."""

    def __init__(self, name):
        self.name = name
        self.marks = []

    def add_mark(self, mark):
        """1–5 аралығындағы бағаны қосады."""
        if 1 <= mark <= 5:
            self.marks.append(mark)
        else:
            print(f"Қате баға: {mark}")

    def average(self):
        """Орташа бағаны қайтарады."""
        return sum(self.marks) / len(self.marks) if self.marks else 0

    def best_mark(self):
        """Ең жоғары бағаны қайтарады."""
        return max(self.marks) if self.marks else None

    def info(self):
        """Оқушы туралы жол қайтарады."""
        return f"{self.name}: бағалары {self.marks}, орташа {self.average():.2f}"


aliya = Student("Әлия")
for m in (5, 4, 5):
    aliya.add_mark(m)
print(aliya.info(), aliya.best_mark())
```

**3-тапсырма**
```python
class Thermostat:
    """Бөлме термостаты."""
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
            word = "суық"
        elif self.temperature <= 24:
            word = "қалыпты"
        else:
            word = "ыстық"
        return f"{self.temperature} °C — {word}"


t = Thermostat()
print(t.status())
t.warmer(7)
print(t.status())
t.cooler(25)
print(t.status())
```

**4-тапсырма** — `a.hello()` → «Сәлем, мен Айдар»; `Greeter.hello(b)` → «Сәлем, мен Мадина» (self-ті қолмен бердік); `b.hello()` → сол; `Greeter.hello()` → `TypeError: Greeter.hello() missing 1 required positional argument: 'self'` — класс арқылы шақырғанда объект берілмеді.

**5-тапсырма**
```python
class GameHero:
    """Ойын кейіпкері."""
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
        print(f"{self.name} {other.name} кейіпкеріне шабуыл жасады: "
              f"{other.name} — {other.hp} HP")
        if not other.is_alive():
            self.gain_xp(50)


batyr = GameHero("Батыр", 10)
naiza = GameHero("Найза", 8)
attacker, defender = batyr, naiza
while batyr.is_alive() and naiza.is_alive():
    attacker.attack(defender)
    attacker, defender = defender, attacker

winner = batyr if batyr.is_alive() else naiza
print(f"Жеңімпаз: {winner.name} (деңгей {winner.level}, XP {winner.xp})")
```

**6-тапсырма**
```python
class ShoppingCart:
    """Сатып алу себеті."""
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
            print("Промокод бұрын қолданылған")
            return False
        if code not in self.PROMO_CODES:
            print("Мұндай промокод жоқ")
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
            lines.append(f"Жеңілдік ({self.discount:.0%}): -{cut} ₸")
        lines.append(f"Барлығы: {self.total():>12} ₸")
        return "\n".join(lines)


cart = ShoppingCart()
cart.add("Дәптер", 150, 5)
cart.add("Қалам", 90, 3)
cart.add("Сөмке", 12000)
cart.apply_promo("STUDENT10")
print(cart.receipt())
```

**7-тапсырма**
```python
class TextEditor:
    """Тізбектелетін әдістері бар мәтін редакторы."""

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


TextEditor().write("  сәлем, әлем  ").strip().replace("әлем", "python").upper().show()
```

**8-тапсырма**
```python
import math


class Fraction:
    """Жай бөлшек."""

    def __init__(self, numerator, denominator=1):
        if denominator == 0:
            print("Бөлімі нөл болмайды! 1 қойылды.")
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

**9-тапсырма**
```python
class Elevator:
    """Лифт симуляциясы."""

    def __init__(self, floors):
        self.floors = floors
        self.current_floor = 1
        self.requests = []
        self.direction = 1  # 1 — жоғары, -1 — төмен

    def call(self, floor):
        if 1 <= floor <= self.floors and floor not in self.requests:
            self.requests.append(floor)

    def _has_requests_ahead(self):
        return any((f - self.current_floor) * self.direction > 0 for f in self.requests)

    def step(self):
        if self.current_floor in self.requests:
            self.requests.remove(self.current_floor)
            print(f"{self.current_floor}-қабат: есік ашылды")
            return
        if not self._has_requests_ahead():
            self.direction *= -1
        self.current_floor += self.direction
        print(f"→ {self.current_floor}-қабат")

    def run(self):
        while self.requests:
            self.step()


lift = Elevator(9)
for floor in (5, 3, 8, 2):
    lift.call(floor)
lift.run()
```

</details>
