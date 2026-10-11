# Сабақ 18. Практикалық тапсырмалар: магиялық әдістер

## Базалық деңгей

### 1-тапсырма. Қала
`City(name, population, region)` класын құрыңыз. `__str__` — «Алматы (Алматы облысы): 2 200 000 тұрғын», `__repr__` — `City('Алматы', 2200000, 'Алматы облысы')`. Үш қаланың тізімін жасап, `print(city)` және `print(cities)` нәтижелерін салыстырыңыз.

### 2-тапсырма. Сынып
`Classroom(name)` класында `students` тізімі және `add(name)` әдісі болсын. `__len__` оқушылар санын қайтарсын, `__str__` — «8 «Ә» сыныбы: 3 оқушы».

Мысал:
```text
>>> c = Classroom("8 «Ә»")
>>> c.add("Айжан"); c.add("Бекзат"); c.add("Нұрлан")
>>> len(c)
3
```

### 3-тапсырма. Бірдей тауарлар
`Product(code, name, price)` класында `__eq__` тауарларды тек `code` (штрихкод) бойынша салыстырсын. Тексеріңіз:
```text
Product("4870001", "Сүт", 520) == Product("4870001", "Сүт 2.5%", 540)  -> True
Product("4870001", "Сүт", 520) == Product("4870002", "Кефир", 520)     -> False
Product("4870001", "Сүт", 520) == "Сүт"                                 -> False (қатесіз!)
```

### 4-тапсырма. Қатені түзет
```python
class Timer:
    def __init__(self, seconds):
        self.seconds = seconds

    def __str__(self):
        m = self.seconds // 60
        s = self.seconds % 60
        print(f"{m:02}:{s:02}")

t = Timer(125)
# print(t)   # # белгісін алып тастап, қатені көріңіз
```
`print(t)` → `02:05` шығатындай етіп түзетіңіз.

## Кеңейтілген деңгей

### 5-тапсырма. Вектор
`Vector(x, y)` класын жасаңыз: `__repr__`, `__eq__`, `__add__` (векторларды қосу), `__mul__` (санға көбейту), `__len__` жоқ — оның орнына `length()` әдісі (модулі, `math.hypot`). Тексеріңіз:
```text
Vector(1, 2) + Vector(3, 4)  -> Vector(4, 6)
Vector(1, 2) * 3             -> Vector(3, 6)
Vector(3, 4).length()        -> 5.0
```
Ойланыңыз: `__len__` неге бұл жерде сәйкес келмейді? (Ол тек бүтін сан қайтарады.)

### 6-тапсырма. Себет
`Cart` класы: `add(product, qty)` әдісі, `__len__` — тауар **бірліктерінің** жалпы саны, `__contains__` — тауар себетте бар ма (`"Нан" in cart`), `__str__` — чек түрінде (әр жол: атауы × саны = сомасы, соңында «Барлығы: ... тг»). 3-тапсырмадағы `Product` класын қолданыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 7-тапсырма. Уақыт аралығы
`Duration(hours, minutes)` класын жасаңыз. Минуттар 60-тан асса, автоматты түрде сағатқа ауыссын (конструкторда нормалау). Магиялық әдістер: `__str__` («2 сағ 15 мин»), `__repr__`, `__eq__`, `__lt__`, `__add__`. Пайдаланушы бір аптадағы күнделікті үй тапсырмасына кеткен уақытты енгізеді (`1 40`, `2 5`, ...). Бағдарлама жалпы уақытты, ең ұзақ күнді (`max`) және сұрыпталған тізімді (`sorted`) шығарсын.

## Үй тапсырмасы

1. 15–17 сабақтарда жазған кластарыңызға (жыл бойғы жоба кластарына) `__str__` және `__repr__` әдістерін қосыңыз.
2. Бір класқа `__eq__` және `__lt__` қосып, объектілер тізімін `sorted()` арқылы сұрыптаңыз.
3. Келесі практикумға дайындық: «Кітапхана» жүйесінде қандай кластар болуы мүмкін екенін дәптерге сызба түрінде жазып келіңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
class City:
    def __init__(self, name, population, region):
        self.name = name
        self.population = population
        self.region = region

    def __str__(self):
        pop = f"{self.population:,}".replace(",", " ")
        return f"{self.name} ({self.region}): {pop} тұрғын"

    def __repr__(self):
        return f"City({self.name!r}, {self.population}, {self.region!r})"


cities = [City("Алматы", 2200000, "Алматы облысы"),
          City("Шымкент", 1200000, "Түркістан облысы"),
          City("Ақтау", 270000, "Маңғыстау облысы")]
for c in cities:
    print(c)
print(cities)
```

**2-тапсырма**
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
        return f"{self.name} сыныбы: {len(self)} оқушы"


c = Classroom("8 «Ә»")
c.add("Айжан")
c.add("Бекзат")
c.add("Нұрлан")
print(len(c))
print(c)
```

**3-тапсырма**
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


print(Product("4870001", "Сүт", 520) == Product("4870001", "Сүт 2.5%", 540))
print(Product("4870001", "Сүт", 520) == Product("4870002", "Кефир", 520))
print(Product("4870001", "Сүт", 520) == "Сүт")
```

**4-тапсырма.** `__str__` жолды қайтаруы керек:
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

**5-тапсырма**
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

**6-тапсырма**
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
        lines.append(f"Барлығы: {total} тг")
        return "\n".join(lines)


cart = Cart()
cart.add(Product("1", "Нан", 250), 2)
cart.add(Product("2", "Сүт", 520))
cart.add(Product("1", "Нан", 250))
print(len(cart))
print("Нан" in cart, "Ет" in cart)
print(cart)
```

**7-тапсырма**
```python
class Duration:
    def __init__(self, hours=0, minutes=0):
        total = hours * 60 + minutes
        self.hours = total // 60
        self.minutes = total % 60

    def total_minutes(self):
        return self.hours * 60 + self.minutes

    def __str__(self):
        return f"{self.hours} сағ {self.minutes} мин"

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


days = ["Дүйсенбі", "Сейсенбі", "Сәрсенбі", "Бейсенбі", "Жұма"]
week = []
for d in days:
    h, m = map(int, input(f"{d} (сағат минут): ").split())
    week.append(Duration(h, m))

total = Duration()
for d in week:
    total = total + d
print("Жалпы:", total)
longest = max(week)
print("Ең ұзақ күн:", days[week.index(longest)], "-", longest)
print("Сұрыпталған:", sorted(week))
```

</details>
