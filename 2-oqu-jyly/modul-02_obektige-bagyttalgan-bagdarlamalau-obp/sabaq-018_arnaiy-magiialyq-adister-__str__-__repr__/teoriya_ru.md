# Урок 18. Специальные (магические) методы: __str__, __repr__, __len__, __eq__.

## Темы

1. Что такое магические методы?
2. `__str__` — текст для человека
3. `__repr__` — текст для программиста
4. `__len__` — «длина» объекта
5. `__eq__` — сравнение на равенство
6. Дополнительно: другие полезные магические методы (обзор)
7. Частые ошибки
8. Основные понятия

## 1. Что такое магические методы?

**Специальные (магические) методы (magic / dunder methods)** — методы, имя которых начинается и заканчивается двумя подчёркиваниями: `__init__`, `__str__`, `__len__`... «Dunder» = **d**ouble **under**score.

Особенность: обычно мы **не вызываем их сами** — Python вызывает их **автоматически** при выполнении определённой операции.

| Мы пишем | Python на самом деле вызывает |
|---|---|
| `Book("Путь Абая")` | `Book.__init__(obj, "Путь Абая")` |
| `print(b)` или `str(b)` | `b.__str__()` |
| `repr(b)` | `b.__repr__()` |
| `len(b)` | `b.__len__()` |
| `a == b` | `a.__eq__(b)` |
| `a < b` | `a.__lt__(b)` |
| `a + b` | `a.__add__(b)` |
| `x in b` | `b.__contains__(x)` |

То есть с `__init__` вы уже давно знакомы — это тоже магический метод!

Собственные типы Python работают так же:

```python
print("привет".__len__())      # как len("привет")
print((5).__add__(3))          # как 5 + 3
print([1, 2].__eq__([1, 2]))   # как [1, 2] == [1, 2]
```

Результат:
```text
6
8
True
```

## 2. `__str__` — текст для человека

`__str__` вызывается, когда объект выводится через `print()` или передаётся в функцию `str()`. Он **должен возвращать строку** (`return`, а не `print`!).

```python
class Book:
    def __init__(self, title, author, pages):
        self.title = title
        self.author = author
        self.pages = pages

    def __str__(self):
        return f"«{self.title}» — {self.author} ({self.pages} стр.)"


b = Book("Путь Абая", "М. Ауэзов", 1200)
print(b)
text = "Книга: " + str(b)
print(text)
print(f"Книга, которую я читаю: {b}")
```

Результат:
```text
«Путь Абая» — М. Ауэзов (1200 стр.)
Книга: «Путь Абая» — М. Ауэзов (1200 стр.)
Книга, которую я читаю: «Путь Абая» — М. Ауэзов (1200 стр.)
```

## 3. `__repr__` — текст для программиста

`__repr__` даёт «официальное», точное описание объекта. Он вызывается:
- в функции `repr(obj)`;
- когда в интерактивной консоли просто пишем объект (`>>> b`);
- когда объект печатается внутри **списка, словаря** и т.п.;
- если нет `__str__`, `print()` тоже использует `__repr__`.

Хороший `__repr__` записывается в виде кода, по которому можно заново создать объект.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point({self.x}, {self.y})"

    def __str__(self):
        return f"точка ({self.x}; {self.y})"


p = Point(3, 4)
print(p)              # __str__
print(repr(p))        # __repr__
points = [Point(0, 0), Point(1, 2)]
print(points)         # внутри списка — __repr__
```

Результат:
```text
точка (3; 4)
Point(3, 4)
[Point(0, 0), Point(1, 2)]
```

### Сравнение `__str__` и `__repr__`

| | `__str__` | `__repr__` |
|---|---|---|
| Для кого | Для пользователя | Для программиста (для отладки) |
| Когда вызывается | `print()`, `str()`, f-строка | `repr()`, консоль, внутри списка |
| Цель | Понятно, красиво | Точно, однозначно |
| Если нет | Используется `__repr__` | `<__main__.X object at 0x...>` |

Подсказка: пишите хотя бы `__repr__` — тогда и `print` будет понятным.

Встроенный пример:

```python
s = "Привет"
print(str(s))
print(repr(s))
```

Результат:
```text
Привет
'Привет'
```

## 4. `__len__` — «длина» объекта

При вызове `len(obj)` выполняется `obj.__len__()`. Он должен возвращать **целое неотрицательное число**.

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
        return f"Плейлист «{self.name}»: песен — {len(self)}"


pl = Playlist("В дороге")
pl.add("Қара жорға")
pl.add("Мен қазақпын")
pl.add("Атамекен")
print(len(pl))
print(pl)
if pl:          # не пуст ли? (__len__ > 0)
    print("Плейлист не пуст")
```

Результат:
```text
3
Плейлист «В дороге»: песен — 3
Плейлист не пуст
```

Примечание: объект с `__len__` при проверке `if obj:` будет `True` только если `len(obj) > 0`.

## 5. `__eq__` — сравнение на равенство

По умолчанию `==` проверяет, являются ли два объекта **одним и тем же объектом** (одним местом в памяти):

```python
class Student:
    def __init__(self, name, iin):
        self.name = name
        self.iin = iin

a = Student("Дана", "100101600123")
b = Student("Дана", "100101600123")
print(a == b)    # значения одинаковые, но...
print(a == a)
```

Результат:
```text
False
True
```

С помощью `__eq__` мы сами определяем «равенство». Например, ученики с одинаковым ИИН — один и тот же человек:

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
print(a != c)            # если есть __eq__, != тоже работает автоматически
print(b in [c, a])       # in ищет в списке через ==
print(a == "Дана")
```

Результат:
```text
True
False
True
True
False
```

Пояснения:
- `isinstance(other, Student)` — проверяет, принадлежит ли объект `other` классу `Student`;
- `NotImplemented` — специальное значение «не умею сравнивать с этим типом»; тогда Python вернёт `False`, программа не упадёт;
- `!r` в f-строке использует `repr()` (выводится в кавычках).

## 6. Дополнительно: другие полезные магические методы (обзор)

```python
class Money:
    def __init__(self, amount):
        self.amount = amount

    def __add__(self, other):          # +
        return Money(self.amount + other.amount)

    def __lt__(self, other):           # <  (нужен для sorted, min, max)
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

Результат:
```text
26 500 тг
True
[Money(700), Money(1500), Money(25000)]
25 000 тг
```

| Метод | Операция |
|---|---|
| `__lt__`, `__le__`, `__gt__`, `__ge__` | `<`, `<=`, `>`, `>=` |
| `__add__`, `__sub__`, `__mul__` | `+`, `-`, `*` |
| `__contains__` | `x in obj` |
| `__getitem__` | `obj[i]` |
| `__bool__` | `if obj:` |

## Частые ошибки

**1. `print` вместо `return` внутри `__str__`**

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        print(f"Кот {self.name}")   # ОШИБКА: ничего не возвращает (None)

c = Cat("Мурзик")
# print(c)  -> TypeError: __str__ returned non-string (type NoneType)
```

Правильно:

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f"Кот {self.name}"

print(Cat("Мурзик"))
```

**2. `__str__` возвращает не строку** — `return self.age` (int) → `TypeError`. Решение: `return str(self.age)` или f-строка.

**3. `__len__` возвращает отрицательное или дробное число** → `ValueError` / `TypeError`. Только `int >= 0`.

**4. Неправильное количество подчёркиваний в имени**: `_str_` или `__str_` — Python не распознаёт это как магический метод, никакой ошибки не будет, но и работать не будет.

**5. Не проверять тип `other` внутри `__eq__`**

```python
class Box:
    def __init__(self, size):
        self.size = size

    def __eq__(self, other):
        return self.size == other.size

# Box(3) == 5  -> AttributeError: 'int' object has no attribute 'size'
print(Box(3) == Box(3))
```

Решение — проверка `isinstance` и возврат `NotImplemented` (см. раздел 5).

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Магический (dunder) метод | Метод вида `__имя__`, который Python вызывает автоматически |
| `__str__` | Понятный человеку текст для `print()`/`str()` |
| `__repr__` | Точное описание для `repr()`, консоли, списка |
| `__len__` | Результат `len(obj)` |
| `__eq__` | Правило сравнения `obj1 == obj2` |
| `isinstance(obj, Class)` | Проверка принадлежности объекта классу |
| `NotImplemented` | Специальное значение «не умею выполнять эту операцию» |

## Примечание

- Если определить `__eq__`, но не определить `__hash__`, Python сделает объекты **нехэшируемыми**: их нельзя положить в `set` или использовать как ключ `dict`. Решение — `__hash__`, согласованный с `__eq__`, например `return hash(self.iin)`.
- Не путайте `NotImplemented` (специальное значение, которое **возвращают** из `__eq__`, `__add__` и т.п.) и `NotImplementedError` (исключение, которое **выбрасывают**).
- Декоратор `functools.total_ordering` достраивает недостающие сравнения: достаточно определить `__eq__` и один из `__lt__`, `__le__`, `__gt__`, `__ge__`.
- `@dataclass` (модуль `dataclasses`, Python 3.7+) автоматически создаёт `__init__`, `__repr__` и `__eq__` по списку полей.
- В f-строке `{obj!r}` вызывает `repr()`, `{obj!s}` — `str()`; в отладке удобна запись `f"{obj=}"` (Python 3.8+).
- Документация: https://docs.python.org/3/reference/datamodel.html#special-method-names
