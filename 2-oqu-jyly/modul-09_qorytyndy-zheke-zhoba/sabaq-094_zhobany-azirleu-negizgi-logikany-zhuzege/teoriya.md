# Сабақ 94. Жобаны әзірлеу: негізгі логиканы жүзеге асыру (1-кезең).

## Тақырыптар

1. Негізгі логика дегеніміз не?
2. Қайдан бастаймыз? Әрекеттер реті
3. Жоба мысалы: `models.py`
4. Жоба мысалы: `logic.py` — 1-кезең
5. Басқа жоба түрлеріне арналған логика үлгілері
6. Код жұмыс істемесе, шешімді қалай іздейміз
7. Жиі кездесетін қателер
8. Негізгі ұғымдар

## 1. Негізгі логика дегеніміз не?

**Негізгі логика (бизнес-логика, business logic)** — бағдарламаның «миы»: есептеулер, ережелер, шешімдер. Ол экранның қандай екенін, деректер қай файлда сақталатынын **білмейді**.

| Жоба | Негізгі логика | Логика емес (басқа қабат) |
|---|---|---|
| Қалта ақшасы | баланс есептеу, санат бойынша қосынды, лимитті тексеру | Tkinter терезесі, SQLite сұранысы |
| Викторина | жауапты тексеру, ұпай есептеу, сұрақтарды араластыру | сұрақтарды экранға шығару, JSON оқу |
| Ойын (Pygame) | ойыншының өмірі, ұпай, деңгейге өту ережесі | спрайттарды салу, дыбыс |
| Кітапхана | кітапты беру/қайтару ережесі, мерзімі өткенін анықтау | терезелер, ДҚ |

**Аналогия:** логика — асханадағы аспазшы, интерфейс — даяшы, деректер қоры — қойма. Аспазшы тағамды қай үстелге апаратынын білмейді — ол тек дәмді тағам жасайды. Даяшыны ауыстырсақ та (Tkinter → веб-сайт), аспазшы өзгермейді.

Неліктен логиканы бөлек жазамыз?
1. **Тестілеу оңай** — терезе ашпай-ақ функцияны тексереміз (`unittest` модулі).
2. **Кезеңмен әзірлеу** — интерфейс әлі жоқ, бірақ логика қазір жұмыс істейді.
3. **Қайта қолдану** — кейін дәл осы логикаға веб-интерфейс (мысалы, Flask) қосуға болады.

## 2. Қайдан бастаймыз? Әрекеттер реті

1. Kanban тақтасынан **бір** тапсырманы аламыз.
2. Деректер моделінен кластарды жазамыз — `models.py`.
3. Ең маңызды бір әрекетті (мысалы, «операция қосу») `logic.py`-де жазамыз.
4. Файлдың соңында `if __name__ == "__main__":` блогында **қолмен тексереміз**.
5. Жұмыс істесе → коммит. Келесі әрекетке өтеміз.

```text
     ┌──────────────┐
     │ Тапсырма алу │
     └──────┬───────┘
            v
   ┌─────────────────┐     жоқ    ┌─────────┐
   │ 5–15 жол код    │ ─────────> │ Жөндеу  │
   │ жазу, іске қосу │ <───────── │ (debug) │
   └──────┬──────────┘            └─────────┘
          │ жұмыс істейді
          v
   ┌─────────────┐
   │ git commit  │ ──> келесі кішкене қадам
   └─────────────┘
```

> **Ереже:** 15 минуттан артық іске қоспай код жазбаңыз. 100 жолды бірден жазып, кейін 10 қатені іздегеннен гөрі, 10 жолдан кейін 1 қатені іздеу әлдеқайда оңай.

## 3. Жоба мысалы: `models.py`

Деректер моделіндегі `category` және `operation` кестелерінен кластар жасаймыз. Python-ның `dataclasses` модулі `__init__`, `__repr__` және `__eq__` әдістерін автоматты түрде жазып береді — бұл магиялық әдістерді қолмен жазуды үнемдейді.

```python
"""models.py — жобаның негізгі деректер кластары."""
from dataclasses import dataclass
import datetime

KINDS = ("кіріс", "шығыс")


@dataclass
class Category:
    """Операция санаты."""
    id: int
    title: str
    kind: str          # "кіріс" немесе "шығыс"


@dataclass
class Operation:
    """Бір ақшалай операция (кіріс немесе шығыс)."""
    amount: int
    category: Category
    op_date: datetime.date
    note: str = ""
    id: int | None = None   # ДҚ-ға сақталғанда беріледі

    def __post_init__(self):
        # __init__-тен кейін автоматты түрде шақырылады: деректерді тексереміз
        if not isinstance(self.amount, int) or self.amount <= 0:
            raise ValueError("Сома оң бүтін сан болуы керек")
        if self.category.kind not in KINDS:
            raise ValueError(f"Белгісіз санат түрі: {self.category.kind}")

    @property
    def signed_amount(self):
        """Шығыс теріс таңбамен, кіріс оң таңбамен."""
        return self.amount if self.category.kind == "кіріс" else -self.amount


if __name__ == "__main__":
    food = Category(1, "Тамақ", "шығыс")
    op = Operation(800, food, datetime.date(2027, 4, 15), "Түскі ас")
    print(op.category.title, op.signed_amount)
    try:
        Operation(-5, food, datetime.date.today())
    except ValueError as err:
        print("Қате:", err)
```

Нәтижесі:
```text
Тамақ -800
Қате: Сома оң бүтін сан болуы керек
```

`dataclass` міндетті емес: `__init__`-ті қолмен жазсаңыз да болады. Бастысы — **деректер бір жерде, бір форматта** болуы.

## 4. Жоба мысалы: `logic.py` — 1-кезең

Бірінші кезеңде ең қажетті әрекеттерді жазамыз: қосу, баланс, санат бойынша қосынды. Деректер әзірге **жадта** (тізімде) сақталады — ДҚ келесі кезеңде қосылады.

```python
"""logic.py — «Қалта ақшасы» негізгі логикасы (1-кезең)."""
from dataclasses import dataclass
import datetime


@dataclass
class Category:
    id: int
    title: str
    kind: str


@dataclass
class Operation:
    amount: int
    category: Category
    op_date: datetime.date
    note: str = ""

    @property
    def signed_amount(self):
        return self.amount if self.category.kind == "кіріс" else -self.amount


class Wallet:
    """Пайдаланушының әмияны: операциялар тізімі және есептеулер."""

    def __init__(self, owner):
        self.owner = owner
        self._operations = []

    def add(self, operation):
        """Операцияны қосады."""
        self._operations.append(operation)

    @property
    def operations(self):
        """Операциялардың көшірмесі (сырттан тізімді бұзбау үшін)."""
        return list(self._operations)

    def balance(self):
        """Ағымдағы баланс: кірістер минус шығыстар."""
        return sum(op.signed_amount for op in self._operations)

    def total_by_category(self, kind="шығыс"):
        """Санаттар бойынша қосынды: {'Тамақ': 2300, ...}"""
        totals = {}
        for op in self._operations:
            if op.category.kind == kind:
                title = op.category.title
                totals[title] = totals.get(title, 0) + op.amount
        return totals


if __name__ == "__main__":
    salary = Category(1, "Ата-ана", "кіріс")
    food = Category(2, "Тамақ", "шығыс")
    bus = Category(3, "Көлік", "шығыс")

    w = Wallet("Дәулет")
    day = datetime.date(2027, 4, 14)
    w.add(Operation(3000, salary, day))
    w.add(Operation(800, food, day, "Түскі ас"))
    w.add(Operation(200, bus, day))
    w.add(Operation(500, food, day + datetime.timedelta(days=1)))

    print("Баланс:", w.balance(), "тг")
    print("Шығыстар:", w.total_by_category())
```

Нәтижесі:
```text
Баланс: 1500 тг
Шығыстар: {'Тамақ': 1300, 'Көлік': 200}
```

Назар аударыңыз:
- `Wallet` ішінде бірде-бір `print` немесе `input` жоқ — ол тек мән **қайтарады**. Шығару — интерфейстің міндеті.
- `operations` қасиеті тізімнің көшірмесін береді — инкапсуляция.
- Кодты қысқарту үшін мұнда `Category` мен `Operation` қайта жазылды; нағыз жобада `from qalta.models import Category, Operation` деп импорттаймыз.

## 5. Басқа жоба түрлеріне арналған логика үлгілері

### Викторина
```python
import random


class Question:
    def __init__(self, text, options, correct_index):
        self.text = text
        self.options = options
        self.correct_index = correct_index

    def check(self, answer_index):
        return answer_index == self.correct_index


class QuizGame:
    def __init__(self, questions, shuffle=True):
        self.questions = list(questions)
        if shuffle:
            random.shuffle(self.questions)
        self.current = 0
        self.score = 0

    def current_question(self):
        return self.questions[self.current] if not self.is_over() else None

    def answer(self, index):
        """Жауапты тексеріп, келесі сұраққа өтеді. Дұрыс болса True."""
        ok = self.questions[self.current].check(index)
        if ok:
            self.score += 1
        self.current += 1
        return ok

    def is_over(self):
        return self.current >= len(self.questions)


q = [Question("Қазақстанның астанасы?", ["Алматы", "Астана", "Шымкент"], 1),
     Question("2 + 2 * 2 = ?", ["8", "6", "4"], 1)]
game = QuizGame(q, shuffle=False)
game.answer(1)
game.answer(0)
print(f"Нәтиже: {game.score}/{len(q)}, аяқталды: {game.is_over()}")
```
Нәтижесі:
```text
Нәтиже: 1/2, аяқталды: True
```

### Ойын (Pygame-сіз логика)
```python
class Player:
    """Ойыншының күйі: экранға қалай салынатынын білмейді."""

    def __init__(self, lives=3):
        self.lives = lives
        self.score = 0
        self.level = 1

    def hit(self):
        self.lives = max(0, self.lives - 1)

    def collect_coin(self, value=10):
        self.score += value
        if self.score >= self.level * 100:   # әр 100 ұпай — жаңа деңгей
            self.level += 1

    @property
    def is_alive(self):
        return self.lives > 0


p = Player()
for _ in range(12):
    p.collect_coin()
p.hit()
print(p.score, p.level, p.lives, p.is_alive)
```
Нәтижесі:
```text
120 2 2 True
```

Pygame циклі кейін тек `player.hit()` немесе `player.collect_coin()` шақырады.

## 6. Код жұмыс істемесе, шешімді қалай іздейміз

**«15 минут ережесі»:** 15 минут өзіңіз ізденіңіз, одан кейін көмек сұраңыз.

| Қадам | Не істейміз |
|---|---|
| 1. Қатені оқу | Traceback-тың **соңғы жолы** — қатенің түрі мен себебі; жоғарыдағы жол нөмірі — орны |
| 2. `print` / дебаггер | айнымалылардың мәнін шығарып қараңыз; `breakpoint()` бағдарламаны `pdb` дебаггерінде тоқтатады |
| 3. «Үйрек әдісі» | кодты жол-жолымен дауыстап түсіндіріңіз (ойыншық үйрекке, әріптесіңізге) — жиі қатені өзіңіз табасыз |
| 4. Құжаттама | docs.python.org, кітапхананың ресми құжаты |
| 5. Іздеу | қате мәтінін ағылшынша іздеу (Stack Overflow) |
| 6. Дұрыс сұрақ қою | әріптеске немесе форумда: *не істегім келеді, не жасадым, не күттім, не болды* + кодтың ең аз мысалы |

**Жасанды интеллект көмекшілері туралы:** оларды түсіндіру мен қатені іздеу үшін қолдануға болады, бірақ автор өз жобасының әр жолын түсінуі керек. Түсінбеген кодты қоймаңыз; көмек алсаңыз, README-де көрсетіңіз.

## Жиі кездесетін қателер

**1. Логиканың ішінде `print`/`input`.**
```python
class WalletBad:
    def __init__(self):
        self.ops = []

    def add(self):
        amount = int(input("Сома: "))   # қате: логика пайдаланушымен сөйлеспейді
        self.ops.append(amount)
```
Түсіндірме: мұны Tkinter-ге қосу және тестілеу мүмкін емес. Дұрысы — мәнді параметр ретінде қабылдау:
```python
class WalletGood:
    def __init__(self):
        self.ops = []

    def add(self, amount):
        self.ops.append(amount)
```

**2. Қатені «жұтып қою».**
```python
def parse_amount(text):
    try:
        return int(text)
    except ValueError:
        return 0       # қате: «0 теңге» деп сақталып кетеді, пайдаланушы білмейді
```
Дұрысы: қатені жоғары жіберу немесе түсінікті хабармен көтеру:
```python
def parse_amount(text):
    try:
        value = int(text)
    except ValueError:
        raise ValueError("Сома сан болуы керек") from None
    if value <= 0:
        raise ValueError("Сома оң болуы керек")
    return value
```

**3. Ортақ (класс деңгейіндегі) тізім.**
```python
class Wallet:
    operations = []        # қате: барлық әмиянға ортақ бір тізім!

a, b = Wallet(), Wallet()
a.operations.append(100)
print(b.operations)        # [100] — b-ге де қосылып кетті
```
Дұрысы: тізімді `__init__` ішінде `self.operations = []` деп құру.

**4. Бәрін бірден жазу, бір рет те іске қоспау.** Кішкене қадам → іске қосу.

**5. Коммитсіз екі сағат жұмыс.** Компьютер қатып қалса — бәрі жоғалады. Әр жұмыс істейтін қадамнан кейін коммит.

## Негізгі ұғымдар
| Ұғым | Түсіндірме |
|---|---|
| Негізгі (бизнес) логика | есептеулер мен ережелер; интерфейс пен сақтау тәсіліне тәуелсіз |
| Инкременттік әзірлеу | бағдарламаны кішкене, тексерілген қадамдармен өсіру |
| Валидация | кіріс деректерінің дұрыстығын тексеру |
| `dataclass` | `__init__`, `__repr__`, `__eq__`-ті автоматты жасайтын декоратор |
| `__post_init__` | dataclass объектісі құрылғаннан кейін шақырылатын әдіс |
| Үйрек әдісі (rubber duck) | кодты дауыстап түсіндіру арқылы қатені табу |

## Ескерту

- Аннотациядағы `int | None` жазбасы Python 3.10-нан бастап жұмыс істейді; ескі нұсқаларда `typing` модулінен `Optional[int]` жазылады.
- `dataclass`-та өзгермелі әдепкі мәндер `field(default_factory=list)` арқылы беріледі: `items: list = []` жолы `ValueError` береді.
- `@dataclass(frozen=True)` объектіні өзгермейтін (және хештелетін) етеді — құрылғаннан кейін өзгермеуге тиіс санат сияқты мәндерге ыңғайлы.
- Ақшаға бөлшек мән керек болса, `float` емес, `decimal.Decimal` қолданыңыз.
- Бір астын сызу (`self._operations`) — PEP 8 бойынша «ішкі» атрибут туралы келісім ғана; Python оған қол жеткізуге тыйым салмайды.
- Құжаттама: https://docs.python.org/3/library/dataclasses.html
