# Урок 95. Практические задания: основная логика (этап 2)

> Основное время — сложная логика вашего проекта (задания 1–3). Упражнения 4–7 предназначены для отработки сегодняшних приёмов (фильтрация, сортировка, группировка, конечный автомат, рефакторинг); для учеников, которые закончили свою задачу или застряли и хотят «переключиться».

## Базовый уровень

### Задание 1. Дерево функций (обязательно)
Запишите одним предложением самую сложную возможность своего проекта (например: «Показать в конце викторины результат по темам и таблицу рекордов»). Разбейте её на дерево функций, как в части 1 теории: название каждой функции, что она принимает и что возвращает.

### Задание 2. Псевдокод → код (обязательно)
Напишите псевдокод самой сложной функции дерева (в папке `docs/` или внутри docstring), затем превратите его в код. После каждой функции — запуск и коммит.

### Задание 3. Граничные значения (обязательно)
Для каждой написанной сегодня функции напишите не менее 3 проверок `assert`: обычный случай, пустые данные, ещё одно граничное значение (ноль, один элемент, последний день месяца и т.п.). Покажите, что все проверки проходят: `Все проверки пройдены`.

### Задание 4 (тренировочное). Фильтрация и сортировка: «Классный журнал»
Данные:
```text
students = [
    {"name": "Аружан", "grade": "8А", "score": 87},
    {"name": "Бекзат", "grade": "8Б", "score": 92},
    {"name": "Гулим", "grade": "8А", "score": 74},
    {"name": "Дамир", "grade": "8Б", "score": 92},
    {"name": "Ерке", "grade": "8А", "score": 58},
]
```
Напишите:
1. `filter_students(students, grade=None, min_score=0)` — фильтрация по заданным условиям.
2. Сортировку по баллам по убыванию, при равенстве — по имени.
3. Средний балл каждого класса (`defaultdict` или словарь).

Пример вывода:
```text
8А, 70+: ['Аружан', 'Гулим']
Рейтинг: Бекзат 92, Дамир 92, Аружан 87, Гулим 74, Ерке 58
Среднее: {'8А': 73.0, '8Б': 92.0}
```

## Продвинутый уровень

### Задание 5 (тренировочное). Конечный автомат викторины
Состояния викторины: `start` → `question` → `answer_shown` → (`question` или `result`) → `start`. События: `begin`, `answer`, `next`, `finish`, `restart`. Составьте таблицу переходов и напишите класс `QuizFlow`; после последнего вопроса должен быть разрешён только `finish`, а не `next`.

### Задание 6 (тренировочное). Рефакторинг
Код ниже работает, но «попахивает». Выполните рефакторинг, не меняя поведения: уберите магические числа, повторы, длинные условия. Докажите с помощью `assert`, что до и после рефакторинга результат одинаковый.

```python
def price(age, day, is_student):
    if day == "суббота" or day == "воскресенье":
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

### Задание 7. Работа с датами: «Срок сдачи домашнего задания»
Функция `deadline_status(deadline, today)`: если срок прошёл — «Просрочено на N дн.», если сегодня — «Сдать нужно сегодня!», если завтра — «Завтра», иначе — «Осталось N дн.». Сделайте также вариант, не учитывающий выходные: `workdays_left(deadline, today)`.

## ★ Олимпиадная / творческая задача

### Задание 8. Самая длинная «экономная» серия
Дан список ежедневных расходов и дневной лимит. Найдите самую длинную серию **идущих подряд дней**, в которые расходы не превышали лимит, и даты её начала/окончания. День без расходов (нет в списке) считается экономным.

```text
limit = 1000
spent = {"2027-04-01": 800, "2027-04-02": 1500, "2027-04-03": 300,
         "2027-04-05": 900, "2027-04-06": 1200}
период: 2027-04-01 — 2027-04-07
Результат: 3 дн. (2027-04-03 — 2027-04-05)
```

### Задание 9. При равенстве мест
Напишите функцию «олимпийского» распределения мест для рекордов игры: игроки с равными очками делят одно место, следующее место пропускается (100, 90, 90, 80 → 1, 2, 2, 4).

## Домашнее задание
1. Завершите все функции логики и прогоните их через проверки `assert`. `push`, обновление Kanban.
2. К уроку 96: повторите работу с базой данных (уроки 55–60: `sqlite3`, CRUD, `JOIN`) или JSON (урок 51) — в зависимости от того, что используется в вашем проекте.
3. Найдите в своей логике любой повторяющийся код и выполните рефакторинг.

## Критерии оценивания
Карта прогресса (как на уроке 94):

| Критерий | Доля | Что проверяется |
|---|---|---|
| Выполнение запланированных задач | 40% | сложная логика работает, issues урока 95 закрыты |
| Рациональность алгоритма | 25% | разбит на функции, граничные значения обработаны |
| Качество кода | 20% | псевдокод/docstring, нет магических чисел, DRY |
| Самостоятельная работа, коммиты | 15% | коммит после каждой функции, сделан `push` |

## Для педагога: решения
<details><summary>Показать решения</summary>

**Задания 1–3** проверяются индивидуально. Если в дереве одна функция «делает всё» (например, `run_quiz()` на 80 строк), разбейте её вместе с учеником на 3–4 функции.

**Задание 4.**
```python
from collections import defaultdict

students = [
    {"name": "Аружан", "grade": "8А", "score": 87},
    {"name": "Бекзат", "grade": "8Б", "score": 92},
    {"name": "Гулим", "grade": "8А", "score": 74},
    {"name": "Дамир", "grade": "8Б", "score": 92},
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
print("Среднее:", average_by_grade(students))
assert filter_students([], "8А") == []
assert ranking([]) == []
```
Результат:
```text
8А, 70+: ['Аружан', 'Гулим']
Рейтинг: Бекзат 92, Дамир 92, Аружан 87, Гулим 74, Ерке 58
Среднее: {'8А': 73.0, '8Б': 92.0}
```

**Задание 5.**
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
            raise ValueError(f"В состоянии '{self.state}' событие '{event}' невозможно")
        is_last = self.index == self.total - 1
        if self.state == "answer_shown":
            if event == "next" and is_last:
                raise ValueError("Это последний вопрос: только 'finish'")
            if event == "finish" and not is_last:
                raise ValueError("Вопросы ещё не закончились")
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
Результат:
```text
begin -> question
answer -> answer_shown
next -> question
answer -> answer_shown
finish -> result
restart -> start
```

**Задание 6.**
```python
WEEKEND = {"суббота", "воскресенье"}
WEEKEND_PRICE = 2500
WEEKDAY_PRICE = 1800
STUDENT_DISCOUNT = 0.3
FREE_AGE = 7


def price(age, day, is_student):
    """Цена билета в зависимости от возраста, дня и студенческой скидки."""
    if age < FREE_AGE:
        return 0
    base = WEEKEND_PRICE if day in WEEKEND else WEEKDAY_PRICE
    return base * (1 - STUDENT_DISCOUNT) if is_student else base


def old_price(age, day, is_student):
    if day == "суббота" or day == "воскресенье":
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
    for day in ("понедельник", "суббота", "воскресенье"):
        for student in (True, False):
            assert abs(price(age, day, student) - old_price(age, day, student)) < 1e-9
print("Рефакторинг не изменил поведение")
```
Результат:
```text
Рефакторинг не изменил поведение
```
(`2500 - 2500 * 0.3` и `2500 * (1 - 0.3)` в дробных вычислениях могут немного отличаться, поэтому использовано не `==`, а `abs(...) < 1e-9`; напомните совет из урока 91 о хранении денег целыми числами.)

**Задание 7.**
```python
import datetime


def deadline_status(deadline, today):
    days = (deadline - today).days
    if days < 0:
        return f"Просрочено на {-days} дн."
    if days == 0:
        return "Сдать нужно сегодня!"
    if days == 1:
        return "Завтра"
    return f"Осталось {days} дн."


def workdays_left(deadline, today):
    """Рабочие дни после today до deadline (включительно)."""
    count, day = 0, today
    while day < deadline:
        day += datetime.timedelta(days=1)
        if day.weekday() < 5:          # 0–4: понедельник–пятница
            count += 1
    return count


t = datetime.date(2027, 4, 23)          # пятница
print(deadline_status(datetime.date(2027, 4, 20), t))
print(deadline_status(t, t))
print(deadline_status(datetime.date(2027, 4, 30), t))
print(workdays_left(datetime.date(2027, 4, 30), t))
```
Результат:
```text
Просрочено на 3 дн.
Сдать нужно сегодня!
Осталось 7 дн.
5
```

**Задание 8.**
```python
import datetime


def longest_saving_streak(spent, limit, start, end):
    """Самая длинная серия идущих подряд дней, когда расход <= limit."""
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
print(f"{n} дн. ({a} — {b})")
```
Результат:
```text
3 дн. (2027-04-03 — 2027-04-05)
```

**Задание 9.**
```python
def olympic_places(scores):
    """Равные очки делят одно место: [100, 90, 90, 80] -> [1, 2, 2, 4]."""
    ordered = sorted(scores, reverse=True)
    first_place = {}
    for i, s in enumerate(ordered, start=1):
        first_place.setdefault(s, i)          # место первого появления каждого результата
    return [first_place[s] for s in scores]


print(olympic_places([100, 90, 90, 80]))
print(olympic_places([50, 70, 50]))
```
Результат:
```text
[1, 2, 2, 4]
[2, 1, 2]
```
</details>
