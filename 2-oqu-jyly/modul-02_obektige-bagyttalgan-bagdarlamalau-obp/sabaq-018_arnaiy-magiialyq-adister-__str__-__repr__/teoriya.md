# Сабақ 18. Арнайы (магиялық) әдістер: __str__, __repr__, __len__, __eq__.

## Тақырыптар

1. Магиялық әдістер дегеніміз не?
2. `__str__` — адамға арналған мәтін
3. `__repr__` — бағдарламашыға арналған мәтін
4. `__len__` — объектінің «ұзындығы»
5. `__eq__` — теңдікті салыстыру
6. Қосымша: басқа пайдалы магиялық әдістер (шолу)
7. Жиі кездесетін қателер
8. Негізгі ұғымдар

## 1. Магиялық әдістер дегеніміз не?

**Арнайы (магиялық) әдістер (магические методы, magic / dunder methods)** — атауы екі астын сызумен басталып, екі астын сызумен аяқталатын әдістер: `__init__`, `__str__`, `__len__`... «Dunder» = **d**ouble **under**score.

Ерекшелігі: оларды біз әдетте **өзіміз шақырмаймыз** — Python оларды белгілі бір амал орындалғанда **автоматты түрде** шақырады.

| Біз жазамыз | Python шын мәнінде шақырады |
|---|---|
| `Book("Абай жолы")` | `Book.__init__(obj, "Абай жолы")` |
| `print(b)` немесе `str(b)` | `b.__str__()` |
| `repr(b)` | `b.__repr__()` |
| `len(b)` | `b.__len__()` |
| `a == b` | `a.__eq__(b)` |
| `a < b` | `a.__lt__(b)` |
| `a + b` | `a.__add__(b)` |
| `x in b` | `b.__contains__(x)` |

Яғни сіз `__init__`-пен бұрыннан таныссыз — ол да магиялық әдіс!

Python-ның өз типтері де осылай жұмыс істейді:

```python
print("сәлем".__len__())       # len("сәлем") сияқты
print((5).__add__(3))          # 5 + 3 сияқты
print([1, 2].__eq__([1, 2]))   # [1, 2] == [1, 2] сияқты
```

Нәтижесі:
```text
5
8
True
```

## 2. `__str__` — адамға арналған мәтін

`__str__` объект `print()` арқылы шығарылғанда немесе `str()` функциясына берілгенде шақырылады. Ол **жолды қайтаруы тиіс** (`return`, `print` емес!).

```python
class Book:
    def __init__(self, title, author, pages):
        self.title = title
        self.author = author
        self.pages = pages

    def __str__(self):
        return f"«{self.title}» — {self.author} ({self.pages} бет)"


b = Book("Абай жолы", "М. Әуезов", 1200)
print(b)
text = "Кітап: " + str(b)
print(text)
print(f"Мен оқып жатқан кітап: {b}")
```

Нәтижесі:
```text
«Абай жолы» — М. Әуезов (1200 бет)
Кітап: «Абай жолы» — М. Әуезов (1200 бет)
Мен оқып жатқан кітап: «Абай жолы» — М. Әуезов (1200 бет)
```

## 3. `__repr__` — бағдарламашыға арналған мәтін

`__repr__` объектінің «ресми», нақты сипаттамасын береді. Ол шақырылады:
- `repr(obj)` функциясында;
- интерактивті консольде (`>>> b`) жай объектіні жазғанда;
- объект **тізім, сөздік** т.б. ішінде басылғанда;
- `__str__` жоқ болса, `print()` да `__repr__`-ді қолданады.

Жақсы `__repr__` — объектіні қайта құруға болатын код түрінде жазылады.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point({self.x}, {self.y})"

    def __str__(self):
        return f"({self.x}; {self.y}) нүктесі"


p = Point(3, 4)
print(p)              # __str__
print(repr(p))        # __repr__
points = [Point(0, 0), Point(1, 2)]
print(points)         # тізім ішінде — __repr__
```

Нәтижесі:
```text
(3; 4) нүктесі
Point(3, 4)
[Point(0, 0), Point(1, 2)]
```

### `__str__` пен `__repr__` салыстыру

| | `__str__` | `__repr__` |
|---|---|---|
| Кімге арналған | Пайдаланушыға | Бағдарламашыға (жөндеу үшін) |
| Қашан шақырылады | `print()`, `str()`, f-жол | `repr()`, консоль, тізім ішінде |
| Мақсаты | Түсінікті, әдемі | Нақты, бір мәнді |
| Жоқ болса | `__repr__` қолданылады | `<__main__.X object at 0x...>` |

Кеңес: ең болмағанда `__repr__` жазыңыз — сонда `print` та түсінікті болады.

Кіріктірілген мысал:

```python
s = "Сәлем"
print(str(s))
print(repr(s))
```

Нәтижесі:
```text
Сәлем
'Сәлем'
```

## 4. `__len__` — объектінің «ұзындығы»

`len(obj)` шақырылғанда `obj.__len__()` орындалады. Ол **бүтін теріс емес санды** қайтаруы керек.

```python
class Playlist:
    def __init__(self, name):
        self.name = name
        self.songs = []

    def add(self, song):
        self.songs.append(song)

    def __len__(self):
        return len(self.songs)

    def __str__(self):
        return f"Плейлист «{self.name}»: {len(self)} ән"


pl = Playlist("Жол үстінде")
pl.add("Қара жорға")
pl.add("Мен қазақпын")
pl.add("Атамекен")
print(len(pl))
print(pl)
if pl:          # бос емес пе? (__len__ > 0)
    print("Плейлист бос емес")
```

Нәтижесі:
```text
3
Плейлист «Жол үстінде»: 3 ән
Плейлист бос емес
```

Ескерту: `__len__` бар объект `if obj:` тексерісінде `len(obj) > 0` болса ғана `True` болады.

## 5. `__eq__` — теңдікті салыстыру

Әдепкі бойынша `==` екі объектінің **бір объект** екенін (жадтағы бір орын) тексереді:

```python
class Student:
    def __init__(self, name, iin):
        self.name = name
        self.iin = iin

a = Student("Дана", "100101600123")
b = Student("Дана", "100101600123")
print(a == b)    # мәндері бірдей, бірақ...
print(a == a)
```

Нәтижесі:
```text
False
True
```

`__eq__` арқылы «теңдікті» өзіміз анықтаймыз. Мысалы, ЖСН (ИИН) бірдей оқушылар — бір адам:

```python
class Student:
    def __init__(self, name, iin):
        self.name = name
        self.iin = iin

    def __eq__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.iin == other.iin

    def __repr__(self):
        return f"Student({self.name!r}, {self.iin!r})"


a = Student("Дана", "100101600123")
b = Student("Дана Ерланқызы", "100101600123")
c = Student("Асқар", "090505500321")
print(a == b)
print(a == c)
print(a != c)            # __eq__ болса, != да автоматты жұмыс істейді
print(b in [c, a])       # in тізімде == арқылы іздейді
print(a == "Дана")
```

Нәтижесі:
```text
True
False
True
True
False
```

Түсініктемелер:
- `isinstance(other, Student)` — `other` объектісі `Student` класына жата ма, тексереді;
- `NotImplemented` — «бұл типпен салыстыруды білмеймін» деген арнайы мән; сонда Python `False` береді, бағдарлама құламайды;
- `!r` f-жолда `repr()` қолданады (тырнақшамен шығады).

## 6. Қосымша: басқа пайдалы магиялық әдістер (шолу)

```python
class Money:
    def __init__(self, amount):
        self.amount = amount

    def __add__(self, other):          # +
        return Money(self.amount + other.amount)

    def __lt__(self, other):           # <  (sorted, min, max үшін керек)
        return self.amount < other.amount

    def __eq__(self, other):           # ==
        return self.amount == other.amount

    def __repr__(self):
        return f"Money({self.amount})"

    def __str__(self):
        return f"{self.amount:,} тг".replace(",", " ")


a = Money(1500)
b = Money(25000)
print(a + b)
print(a < b)
print(sorted([b, a, Money(700)]))
print(max(a, b))
```

Нәтижесі:
```text
26 500 тг
True
[Money(700), Money(1500), Money(25000)]
25 000 тг
```

| Әдіс | Амал |
|---|---|
| `__lt__`, `__le__`, `__gt__`, `__ge__` | `<`, `<=`, `>`, `>=` |
| `__add__`, `__sub__`, `__mul__` | `+`, `-`, `*` |
| `__contains__` | `x in obj` |
| `__getitem__` | `obj[i]` |
| `__bool__` | `if obj:` |

## Жиі кездесетін қателер

**1. `__str__` ішінде `return` орнына `print`**

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        print(f"Мысық {self.name}")   # ҚАТЕ: ештеңе қайтармайды (None)

c = Cat("Мұрын")
# print(c)  -> TypeError: __str__ returned non-string (type NoneType)
```

Дұрысы:

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f"Мысық {self.name}"

print(Cat("Мұрын"))
```

**2. `__str__` жолдан басқа тип қайтарады** — `return self.age` (int) → `TypeError`. Шешімі: `return str(self.age)` немесе f-жол.

**3. `__len__` теріс сан немесе бөлшек қайтарады** → `ValueError` / `TypeError`. Тек `int >= 0`.

**4. Атауда астын сызу саны қате**: `_str_` немесе `__str_` — Python мұны магиялық әдіс деп танымайды, ешқандай қате шықпайды, бірақ жұмыс та істемейді.

**5. `__eq__` ішінде `other` типін тексермеу**

```python
class Box:
    def __init__(self, size):
        self.size = size

    def __eq__(self, other):
        return self.size == other.size

# Box(3) == 5  -> AttributeError: 'int' object has no attribute 'size'
print(Box(3) == Box(3))
```

Шешімі — `isinstance` тексерісі және `NotImplemented` қайтару (5-бөлімді қараңыз).

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| Магиялық (dunder) әдіс | `__атау__` түріндегі, Python автоматты шақыратын әдіс |
| `__str__` | `print()`/`str()` үшін адамға түсінікті мәтін |
| `__repr__` | `repr()`, консоль, тізім үшін нақты сипаттама |
| `__len__` | `len(obj)` нәтижесі |
| `__eq__` | `obj1 == obj2` салыстыру ережесі |
| `isinstance(obj, Class)` | Объектінің класқа тиесілілігін тексеру |
| `NotImplemented` | «Бұл амалды білмеймін» деген арнайы мән |

## Ескерту

- `__eq__` анықталып, `__hash__` анықталмаса, Python объектілерді **хэштелмейтін** етеді: оларды `set`-ке салуға немесе `dict` кілті ретінде қолдануға болмайды. Шешімі — `__eq__`-мен келісілген `__hash__`, мысалы `return hash(self.iin)`.
- `NotImplemented` (`__eq__`, `__add__` т.б. әдістерден **қайтарылатын** арнайы мән) мен `NotImplementedError` (**лақтырылатын** ерекше жағдай) екеуін шатастырмаңыз.
- `functools.total_ordering` декораторы жетіспейтін салыстыруларды толықтырады: `__eq__` және `__lt__`, `__le__`, `__gt__`, `__ge__` әдістерінің біреуін анықтау жеткілікті.
- `@dataclass` (`dataclasses` модулі, Python 3.7+) өрістер тізімі бойынша `__init__`, `__repr__` және `__eq__` әдістерін автоматты түрде жасайды.
- f-жолда `{obj!r}` `repr()`-ді, `{obj!s}` `str()`-ді шақырады; жөндеу кезінде `f"{obj=}"` жазбасы ыңғайлы (Python 3.8+).
- Құжаттама: https://docs.python.org/3/reference/datamodel.html#special-method-names
