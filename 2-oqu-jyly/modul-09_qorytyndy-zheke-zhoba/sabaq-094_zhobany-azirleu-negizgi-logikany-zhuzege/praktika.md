# Сабақ 94. Практикалық тапсырмалар: негізгі логика (1-кезең)

> Бүгінгі сабақтың негізгі уақыты — **өз жобаңызбен жұмыс**. Төмендегі базалық тапсырмалар — сол жұмыстың қадамдары. Жаттығу тапсырмалары (4, 6, 8) — логика жазуды үлгі жобада машықтанғысы келетін немесе өз тапсырмасын ерте бітірген оқушыларға.

## Базалық деңгей

### 1-тапсырма. `models.py` (міндетті)
Деректер моделіңіздегі (91-сабақ) әр мән үшін класс жазыңыз:
- атрибуттары кесте өрістеріне сәйкес келеді;
- `__repr__` бар (немесе `@dataclass`);
- кемінде бір класта `__init__`/`__post_init__` ішінде валидация (`ValueError`).

Файл соңындағы `if __name__ == "__main__":` блогында 2–3 объект құрып, оларды экранға шығарыңыз.

### 2-тапсырма. Негізгі әрекеттер (міндетті)
Kanban тақтаңыздағы 94-сабақтың тапсырмалары бойынша `logic.py` (немесе `game/logic.py`, `quiz/engine.py`) файлында **ең маңызды 3 әрекетті** жазыңыз. Талаптар:
- әдістер мән қайтарады, ішінде `print`/`input` жоқ;
- әр әдіске бір жолдық docstring;
- консольдік тексеру блогы нәтижені көрсетеді.

### 3-тапсырма. Коммиттер және Kanban
Сабақ барысында кемінде **3 коммит** жасаңыз (әр жұмыс істейтін қадамнан кейін), issue нөмірін көрсетіңіз. Сабақ соңында `git push` және Kanban тақтасын жаңартыңыз.

### 4-тапсырма (жаттығу). «Мектеп кітапханасы» логикасы
`Book(title, author, copies)` және `Library` класын жазыңыз:
- `add_book(book)`;
- `give(title)` — данасы болса, `copies` 1-ге азаяды және `True`; болмаса `False`;
- `give_back(title)` — данасы 1-ге көбейеді; мұндай кітап жоқ болса `KeyError`;
- `available()` — данасы > 0 кітаптар атауларының тізімі.

Мысал:
```text
>>> lib.give("Абай жолы")
True
>>> lib.available()
['Абай жолы', 'Көшпенділер']
```

## Кеңейтілген деңгей

### 5-тапсырма. Ерекше жағдайлар тізімі
Өз логикаңыз үшін «не бұзылуы мүмкін?» деген сұраққа жауап беріп, кемінде 5 ерекше жағдайды жазыңыз (бос тізім, нөлге бөлу, теріс сан, қайталанатын атау, т.б.). Әрқайсысын кодта өңдеңіз немесе 95-сабаққа issue ретінде тіркеңіз.

### 6-тапсырма (жаттығу). Айлық лимит
`Wallet` класына (теориядағы үлгі) мына әдістерді қосыңыз:
- `set_limit(category_title, amount)` — санатқа айлық лимит;
- `check_limits(year, month)` — лимиттен асқан санаттардың сөздігін қайтарады: `{'Тамақ': 300}` (қаншаға асқаны).

### 7-тапсырма. Логиканы бөлу
Төмендегі «бәрі бірге» кодтан логиканы `Shop` класына бөліп шығарыңыз; енгізу/шығару тек `main()` функциясында қалсын.

```python
items = {}
while True:
    cmd = input("Команда (add/total/exit): ")
    if cmd == "add":
        name = input("Тауар: ")
        price = int(input("Бағасы: "))
        items[name] = items.get(name, 0) + price
        print("Қосылды")
    elif cmd == "total":
        print("Жалпы:", sum(items.values()))
    elif cmd == "exit":
        break
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Болдырмау (Undo)
`Wallet` класына `undo()` әдісін қосыңыз: соңғы әрекетті (қосу немесе жою) болдырмайды. Бірнеше рет қатарынан шақыруға болады. *Кеңес:* әрекеттер тарихын стек (тізім, `append`/`pop`) ретінде сақтаңыз.

### 9-тапсырма. Ойын деңгейлерінің генераторы
Ойын жобасы үшін `make_level(n)` функциясын жазыңыз: n-деңгейде кедергілер саны `3 + 2*n`, жылдамдығы `1.0 + 0.25*n` (5,0-ден аспайды), кедергілердің x-координаталары 0–800 аралығында кездейсоқ, бірақ бір-бірінен кемінде 60 пиксель қашық. `random.seed(n)` қолданып, бір деңгей әрқашан бірдей болсын.

## Үй тапсырмасы
1. Бүгін басталған тапсырмаларды аяқтаңыз, `push` жасаңыз.
2. 95-сабақтың тапсырмаларын Kanban-да қарап, ең күрделі алгоритмді қағазға блок-сызба/псевдокод түрінде жазыңыз.
3. Өз кодыңыздан бір функцияны таңдап, оны құрбыңызға (немесе үйде біреуге) «үйрек әдісімен» түсіндіріңіз.

## Бағалау критерийлері
Жоба сабақтарында (94–97) әр сабақтың соңында **прогресс картасы** толтырылады:

| Критерий | Үлес | Не тексеріледі |
|---|---|---|
| Жоспардағы тапсырмалардың орындалуы | 40% | бүгінгі issue-лер жабылды / жұмыс істейтін код бар |
| Шешімнің ұтымдылығы | 25% | логика интерфейстен бөлек, валидация бар |
| Код сапасы | 20% | PEP 8, түсінікті атаулар, docstring |
| Өз бетінше жұмыс, тұрақты коммиттер | 15% | кемінде 3 мағыналы коммит, `push` жасалды |

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**1–3, 5-тапсырмалар** жеке тексеріледі. Әр оқушыға 3 сұрақ: «Бүгін не жасадың? Қай жерде кептелдің? Келесі қадамың қандай?» — жауаптарды прогресс картасына жазыңыз.

**4-тапсырма.**
```python
class Book:
    def __init__(self, title, author, copies=1):
        self.title = title
        self.author = author
        self.copies = copies

    def __repr__(self):
        return f"Book({self.title!r}, {self.copies} дана)"


class Library:
    def __init__(self):
        self._books = {}

    def add_book(self, book):
        """Кітапты қосады (бар болса, данасын көбейтеді)."""
        if book.title in self._books:
            self._books[book.title].copies += book.copies
        else:
            self._books[book.title] = book

    def give(self, title):
        """Кітапты береді. Сәтті болса True."""
        book = self._books.get(title)
        if book is None or book.copies == 0:
            return False
        book.copies -= 1
        return True

    def give_back(self, title):
        """Кітапты қайтарады."""
        if title not in self._books:
            raise KeyError(f"Кітапханада '{title}' жоқ")
        self._books[title].copies += 1

    def available(self):
        """Қолда бар кітаптар атаулары."""
        return [t for t, b in self._books.items() if b.copies > 0]


lib = Library()
lib.add_book(Book("Абай жолы", "М. Әуезов", 2))
lib.add_book(Book("Көшпенділер", "І. Есенберлин", 1))
print(lib.give("Абай жолы"))
print(lib.available())
print(lib.give("Көшпенділер"), lib.give("Көшпенділер"))
print(lib.available())
```
Нәтижесі:
```text
True
['Абай жолы', 'Көшпенділер']
True False
['Абай жолы']
```

**6-тапсырма.**
```python
import datetime


class Category:
    def __init__(self, title, kind):
        self.title = title
        self.kind = kind


class Operation:
    def __init__(self, amount, category, op_date):
        self.amount = amount
        self.category = category
        self.op_date = op_date


class Wallet:
    def __init__(self):
        self._operations = []
        self._limits = {}

    def add(self, op):
        self._operations.append(op)

    def set_limit(self, category_title, amount):
        """Санатқа айлық лимит қояды."""
        if amount <= 0:
            raise ValueError("Лимит оң сан болуы керек")
        self._limits[category_title] = amount

    def spent_in_month(self, year, month):
        totals = {}
        for op in self._operations:
            d = op.op_date
            if op.category.kind == "шығыс" and d.year == year and d.month == month:
                totals[op.category.title] = totals.get(op.category.title, 0) + op.amount
        return totals

    def check_limits(self, year, month):
        """Лимиттен асқан санаттар: {санат: асқан сома}."""
        spent = self.spent_in_month(year, month)
        return {title: spent[title] - limit
                for title, limit in self._limits.items()
                if spent.get(title, 0) > limit}


food = Category("Тамақ", "шығыс")
w = Wallet()
w.add(Operation(1500, food, datetime.date(2027, 4, 1)))
w.add(Operation(1800, food, datetime.date(2027, 4, 10)))
w.add(Operation(900, food, datetime.date(2027, 5, 1)))
w.set_limit("Тамақ", 3000)
print(w.check_limits(2027, 4))
print(w.check_limits(2027, 5))
```
Нәтижесі:
```text
{'Тамақ': 300}
{}
```

**7-тапсырма.**
```python
class Shop:
    """Себет логикасы: енгізу/шығаруды білмейді."""

    def __init__(self):
        self.items = {}

    def add(self, name, price):
        if price < 0:
            raise ValueError("Баға теріс болмауы керек")
        self.items[name] = self.items.get(name, 0) + price

    def total(self):
        return sum(self.items.values())


def main():
    shop = Shop()
    while True:
        cmd = input("Команда (add/total/exit): ")
        if cmd == "add":
            name = input("Тауар: ")
            try:
                shop.add(name, int(input("Бағасы: ")))
                print("Қосылды")
            except ValueError as err:
                print("Қате:", err)
        elif cmd == "total":
            print("Жалпы:", shop.total())
        elif cmd == "exit":
            break


if __name__ == "__main__":
    main()
```
(`int("абв")` те `ValueError` береді — оның хабарламасы ағылшынша болады; оны да қазақшаға ауыстыруды талқылауға болады.)

**8-тапсырма.**
```python
class Wallet:
    def __init__(self):
        self._operations = []
        self._history = []          # ("add", op) немесе ("remove", op, index)

    def add(self, op):
        self._operations.append(op)
        self._history.append(("add", op))

    def remove(self, index):
        op = self._operations.pop(index)
        self._history.append(("remove", op, index))

    def undo(self):
        """Соңғы әрекетті болдырмайды. Тарих бос болса False."""
        if not self._history:
            return False
        action = self._history.pop()
        if action[0] == "add":
            self._operations.remove(action[1])
        else:
            self._operations.insert(action[2], action[1])
        return True

    def __len__(self):
        return len(self._operations)


w = Wallet()
w.add(100)
w.add(200)
w.remove(0)
print(len(w))     # 1
w.undo()          # жоюды болдырмау
print(len(w))     # 2
w.undo()
w.undo()
print(len(w), w.undo())   # 0 False
```
Нәтижесі:
```text
1
2
0 False
```

**9-тапсырма.**
```python
import random


def make_level(n, width=800, gap=60):
    """n-деңгейдің кедергілері мен жылдамдығы."""
    rnd = random.Random(n)            # тек осы деңгейге арналған генератор
    count = 3 + 2 * n
    speed = min(5.0, 1.0 + 0.25 * n)
    xs = []
    attempts = 0
    while len(xs) < count and attempts < 10000:
        attempts += 1
        x = rnd.randint(0, width)
        if all(abs(x - other) >= gap for other in xs):
            xs.append(x)
    return {"level": n, "speed": speed, "obstacles": sorted(xs)}


print(make_level(1))
print(make_level(1) == make_level(1))
```
Ескерту: `width / gap` ≈ 13 кедергіден көп болса, барлығын орналастыру мүмкін емес — `attempts` шектеуі шексіз циклден қорғайды; бұл жағдайды оқушымен талқылаңыз. (`random.seed(n)` орнына `random.Random(n)` жеке генераторы қолданылды — ол ойынның басқа кездейсоқ бөліктеріне әсер етпейді.)
</details>
