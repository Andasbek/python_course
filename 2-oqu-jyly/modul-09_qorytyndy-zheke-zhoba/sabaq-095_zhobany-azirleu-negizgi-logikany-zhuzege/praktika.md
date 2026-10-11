# Сабақ 95. Практикалық тапсырмалар: негізгі логика (2-кезең)

> Негізгі уақыт — өз жобаңыздың күрделі логикасы (1–3 тапсырмалар). 4–7 жаттығулар — бүгінгі тәсілдерді (сүзу, сұрыптау, топтау, күй машинасы, рефакторинг) машықтануға арналған; өз тапсырмасын аяқтаған немесе кептеліп қалып, ойын «жаңартқысы» келетін оқушыларға.

## Базалық деңгей

### 1-тапсырма. Функциялар ағашы (міндетті)
Жобаңыздың ең күрделі мүмкіндігін бір сөйлеммен жазыңыз (мысалы: «Викторина соңында тақырыптар бойынша нәтиже мен рекордтар кестесін көрсету»). Оны теориядағы 1-бөлімдегідей функциялар ағашына бөліңіз: әр функцияның атауы, не қабылдайтыны, не қайтаратыны.

### 2-тапсырма. Псевдокод → код (міндетті)
Ағаштағы ең күрделі функцияның псевдокодын жазыңыз (`docs/` папкасына немесе docstring ішіне), содан кейін оны кодқа айналдырыңыз. Әр функциядан кейін — іске қосу және коммит.

### 3-тапсырма. Шекаралық мәндер (міндетті)
Бүгін жазылған әр функцияға кемінде 3 `assert`-тексеріс жазыңыз: қалыпты жағдай, бос деректер, тағы бір шекаралық мән (нөл, бір элемент, айдың соңғы күні т.б.). Барлық тексеріс өтетінін көрсетіңіз: `Барлық тексеріс өтті`.

### 4-тапсырма (жаттығу). Сүзу және сұрыптау: «Сынып журналы»
Деректер:
```text
students = [
    {"name": "Аружан", "grade": "8А", "score": 87},
    {"name": "Бекзат", "grade": "8Ә", "score": 92},
    {"name": "Гүлім", "grade": "8А", "score": 74},
    {"name": "Дамир", "grade": "8Ә", "score": 92},
    {"name": "Ерке", "grade": "8А", "score": 58},
]
```
Жазыңыз:
1. `filter_students(students, grade=None, min_score=0)` — берілген шарттар бойынша сүзу.
2. Ұпай бойынша кему ретімен, тең болса — аты бойынша сұрыптау.
3. Әр сыныптың орташа ұпайы (`defaultdict` немесе сөздік).

Шығару мысалы:
```text
8А, 70+: ['Аружан', 'Гүлім']
Рейтинг: Бекзат 92, Дамир 92, Аружан 87, Гүлім 74, Ерке 58
Орташа: {'8А': 73.0, '8Ә': 92.0}
```

## Кеңейтілген деңгей

### 5-тапсырма (жаттығу). Викторинаның күй машинасы
Викторина күйлері: `start` → `question` → `answer_shown` → (`question` немесе `result`) → `start`. Оқиғалар: `begin`, `answer`, `next`, `finish`, `restart`. Ауысулар кестесін құрып, `QuizFlow` класын жазыңыз; соңғы сұрақтан кейін `next` емес, тек `finish` рұқсат етілсін.

### 6-тапсырма (жаттығу). Рефакторинг
Төмендегі код жұмыс істейді, бірақ «иісі» бар. Мінез-құлқын өзгертпей рефакторинг жасаңыз: сиқырлы сандарды, қайталауды, ұзын шарттарды жойыңыз. Рефакторингке дейін және кейін бірдей нәтиже беретінін `assert` арқылы дәлелдеңіз.

```python
def price(age, day, is_student):
    if day == "сенбі" or day == "жексенбі":
        if age < 7:
            p = 0
        elif is_student:
            p = 2500 - 2500 * 0.3
        else:
            p = 2500
    else:
        if age < 7:
            p = 0
        elif is_student:
            p = 1800 - 1800 * 0.3
        else:
            p = 1800
    return p
```

### 7-тапсырма. Күндермен жұмыс: «Үй тапсырмасының мерзімі»
`deadline_status(deadline, today)` функциясы: мерзім өтіп кетсе — «N күн кешіктірілді», бүгін болса — «Бүгін тапсыру керек!», ертең болса — «Ертең», әйтпесе — «N күн қалды». Демалыс күндерін есептемейтін нұсқасын да жасаңыз: `workdays_left(deadline, today)`.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Ең ұзақ «үнемді» серия
Күнделікті шығыстар тізімі мен күндік лимит берілген. Шығысы лимиттен аспаған ең ұзақ **қатарынан келген күндер** сериясын және оның басталу/аяқталу күнін табыңыз. Шығысы жоқ күн (тізімде жоқ) — үнемді күн болып саналады.

```text
limit = 1000
spent = {"2027-04-01": 800, "2027-04-02": 1500, "2027-04-03": 300,
         "2027-04-05": 900, "2027-04-06": 1200}
период: 2027-04-01 — 2027-04-07
Нәтиже: 3 күн (2027-04-03 — 2027-04-05)
```

### 9-тапсырма. Орындар тең болғанда
Ойын рекордтары үшін «олимпиадалық» орын беру функциясын жазыңыз: ұпайлары тең ойыншылар бір орынды бөліседі, келесі орын өткізіледі (100, 90, 90, 80 → 1, 2, 2, 4).

## Үй тапсырмасы
1. Логиканың барлық функцияларын аяқтап, `assert`-тексерістерден өткізіңіз. `push`, Kanban жаңарту.
2. 96-сабаққа: деректер қорымен жұмысты қайталаңыз (55–60 сабақтар: `sqlite3`, CRUD, `JOIN`) немесе JSON (51-сабақ) — жобаңызда не қолданылатынына қарай.
3. Логикаңыздағы кез келген қайталанатын кодты тауып, рефакторинг жасаңыз.

## Бағалау критерийлері
Прогресс картасы (94-сабақтағыдай):

| Критерий | Үлес | Не тексеріледі |
|---|---|---|
| Жоспардағы тапсырмалардың орындалуы | 40% | күрделі логика жұмыс істейді, 95-сабақтың issue-лері жабылды |
| Алгоритмнің ұтымдылығы | 25% | функцияларға бөлінген, шекаралық мәндер өңделген |
| Код сапасы | 20% | псевдокод/docstring, сиқырлы сандар жоқ, DRY |
| Өз бетінше жұмыс, коммиттер | 15% | әр функциядан кейін коммит, `push` жасалды |

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**1–3-тапсырмалар** жеке тексеріледі. Ағашта бір функция «бәрін істейтін» болса (мысалы, `run_quiz()` 80 жол), оқушымен бірге оны 3–4 функцияға бөліңіз.

**4-тапсырма.**
```python
from collections import defaultdict

students = [
    {"name": "Аружан", "grade": "8А", "score": 87},
    {"name": "Бекзат", "grade": "8Ә", "score": 92},
    {"name": "Гүлім", "grade": "8А", "score": 74},
    {"name": "Дамир", "grade": "8Ә", "score": 92},
    {"name": "Ерке", "grade": "8А", "score": 58},
]


def filter_students(items, grade=None, min_score=0):
    return [s for s in items
            if (grade is None or s["grade"] == grade) and s["score"] >= min_score]


def ranking(items):
    return sorted(items, key=lambda s: (-s["score"], s["name"]))


def average_by_grade(items):
    sums, counts = defaultdict(int), defaultdict(int)
    for s in items:
        sums[s["grade"]] += s["score"]
        counts[s["grade"]] += 1
    return {g: round(sums[g] / counts[g], 1) for g in sorted(sums)}


print("8А, 70+:", [s["name"] for s in filter_students(students, "8А", 70)])
print("Рейтинг:", ", ".join(f"{s['name']} {s['score']}" for s in ranking(students)))
print("Орташа:", average_by_grade(students))
assert filter_students([], "8А") == []
assert ranking([]) == []
```
Нәтижесі:
```text
8А, 70+: ['Аружан', 'Гүлім']
Рейтинг: Бекзат 92, Дамир 92, Аружан 87, Гүлім 74, Ерке 58
Орташа: {'8А': 73.0, '8Ә': 92.0}
```

**5-тапсырма.**
```python
TRANSITIONS = {
    ("start", "begin"): "question",
    ("question", "answer"): "answer_shown",
    ("answer_shown", "next"): "question",
    ("answer_shown", "finish"): "result",
    ("result", "restart"): "start",
}


class QuizFlow:
    def __init__(self, total_questions):
        self.total = total_questions
        self.state = "start"
        self.index = 0

    def send(self, event):
        if (self.state, event) not in TRANSITIONS:
            raise ValueError(f"'{self.state}' күйінде '{event}' мүмкін емес")
        is_last = self.index == self.total - 1
        if self.state == "answer_shown":
            if event == "next" and is_last:
                raise ValueError("Бұл соңғы сұрақ: тек 'finish'")
            if event == "finish" and not is_last:
                raise ValueError("Сұрақтар әлі бітпеді")
            if event == "next":
                self.index += 1
        if event == "restart":
            self.index = 0
        self.state = TRANSITIONS[(self.state, event)]
        return self.state


flow = QuizFlow(2)
for e in ["begin", "answer", "next", "answer", "finish", "restart"]:
    print(e, "->", flow.send(e))
```
Нәтижесі:
```text
begin -> question
answer -> answer_shown
next -> question
answer -> answer_shown
finish -> result
restart -> start
```

**6-тапсырма.**
```python
WEEKEND = {"сенбі", "жексенбі"}
WEEKEND_PRICE = 2500
WEEKDAY_PRICE = 1800
STUDENT_DISCOUNT = 0.3
FREE_AGE = 7


def price(age, day, is_student):
    """Билет бағасы: жасы, күні және студенттік жеңілдікке қарай."""
    if age < FREE_AGE:
        return 0
    base = WEEKEND_PRICE if day in WEEKEND else WEEKDAY_PRICE
    return base * (1 - STUDENT_DISCOUNT) if is_student else base


def old_price(age, day, is_student):
    if day == "сенбі" or day == "жексенбі":
        if age < 7:
            p = 0
        elif is_student:
            p = 2500 - 2500 * 0.3
        else:
            p = 2500
    else:
        if age < 7:
            p = 0
        elif is_student:
            p = 1800 - 1800 * 0.3
        else:
            p = 1800
    return p


for age in (5, 7, 15):
    for day in ("дүйсенбі", "сенбі", "жексенбі"):
        for student in (True, False):
            assert abs(price(age, day, student) - old_price(age, day, student)) < 1e-9
print("Рефакторинг мінез-құлықты өзгертпеді")
```
Нәтижесі:
```text
Рефакторинг мінез-құлықты өзгертпеді
```
(`2500 - 2500 * 0.3` пен `2500 * (1 - 0.3)` бөлшек есептеуде сәл өзгеше болуы мүмкін, сондықтан `==` емес, `abs(...) < 1e-9` қолданылды; ақшаны бүтін санмен сақтау туралы 91-сабақтағы кеңесті еске салыңыз.)

**7-тапсырма.**
```python
import datetime


def deadline_status(deadline, today):
    days = (deadline - today).days
    if days < 0:
        return f"{-days} күн кешіктірілді"
    if days == 0:
        return "Бүгін тапсыру керек!"
    if days == 1:
        return "Ертең"
    return f"{days} күн қалды"


def workdays_left(deadline, today):
    """today-ден кейінгі deadline-ге дейінгі (қоса алғанда) жұмыс күндері."""
    count, day = 0, today
    while day < deadline:
        day += datetime.timedelta(days=1)
        if day.weekday() < 5:          # 0–4: дүйсенбі–жұма
            count += 1
    return count


t = datetime.date(2027, 4, 23)          # жұма
print(deadline_status(datetime.date(2027, 4, 20), t))
print(deadline_status(t, t))
print(deadline_status(datetime.date(2027, 4, 30), t))
print(workdays_left(datetime.date(2027, 4, 30), t))
```
Нәтижесі:
```text
3 күн кешіктірілді
Бүгін тапсыру керек!
7 күн қалды
5
```

**8-тапсырма.**
```python
import datetime


def longest_saving_streak(spent, limit, start, end):
    """Шығыс <= limit болған ең ұзақ қатарынан келген күндер."""
    best_len, best_start, best_end = 0, None, None
    cur_len, cur_start = 0, None
    day = start
    while day <= end:
        if spent.get(day.isoformat(), 0) <= limit:
            if cur_len == 0:
                cur_start = day
            cur_len += 1
            if cur_len > best_len:
                best_len, best_start, best_end = cur_len, cur_start, day
        else:
            cur_len = 0
        day += datetime.timedelta(days=1)
    return best_len, best_start, best_end


spent = {"2027-04-01": 800, "2027-04-02": 1500, "2027-04-03": 300,
         "2027-04-05": 900, "2027-04-06": 1200}
n, a, b = longest_saving_streak(spent, 1000, datetime.date(2027, 4, 1), datetime.date(2027, 4, 7))
print(f"{n} күн ({a} — {b})")
```
Нәтижесі:
```text
3 күн (2027-04-03 — 2027-04-05)
```

**9-тапсырма.**
```python
def olympic_places(scores):
    """Тең ұпайлар бір орынды бөліседі: [100, 90, 90, 80] -> [1, 2, 2, 4]."""
    ordered = sorted(scores, reverse=True)
    first_place = {}
    for i, s in enumerate(ordered, start=1):
        first_place.setdefault(s, i)          # әр ұпайдың бірінші кездескен орны
    return [first_place[s] for s in scores]


print(olympic_places([100, 90, 90, 80]))
print(olympic_places([50, 70, 50]))
```
Нәтижесі:
```text
[1, 2, 2, 4]
[2, 1, 2]
```
</details>
