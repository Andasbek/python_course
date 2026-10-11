# Урок 71. Практические задания: повторение сложных тем

## Диагностический тест (12 мин)

Запиши результат кода, **не запуская** его. Затем обменяйся тетрадью с соседом и проверь по ответам педагога. Темы вопросов, на которые ты ответил неверно, – твой сегодняшний «пробел».

| № | Код | Тема |
|---|---|---|
| 1 | `print(len(range(3, 15, 4)))` | Циклы, `range` |
| 2 | `a = [4, 8, 15, 16, 23, 42]` → `print(a[2:5], a[-2])` | Срез |
| 3 | `s = "Python"` → `print(s[::-1][:3])` | Строки |
| 4 | `c = 0` → `for i in range(3):` → `for j in range(i):` → `c += 1` → `print(c)` | Вложенные циклы |
| 5 | `d = {"a": [1]}` → `d["a"].append(2)` → `print(len(d), d["a"])` | Список внутри словаря |
| 6 | `def f(x):` → `x = x * 2` → `n = 5; f(n); print(n)` | Область видимости |
| 7 | `def g(n): return 1 if n == 0 else n + g(n - 1)` → `print(g(4))` | Рекурсия |
| 8 | `a = [3, 1, 2]` → `b = a` → `b.sort()` → `print(a)` | Списки, ссылка |
| 9 | `try:` `print(int("3.5"))` `except ValueError:` `print("ошибка")` | `try...except` |
| 10 | `print({1, 2, 3} & {2, 3, 4}, {1, 2} | {5})` | Множества |

## Базовый уровень (карточка А)

**А1. Трассировка.** Заполни таблицу и предскажи результат, затем запусти и проверь:

```python
x = 1
total = 0
while x < 20:
    total += x
    x = x * 3
print(x, total)
```

```text
| Шаг | x | total | x < 20? |
|-----|---|-------|---------|
| 0   | 1 | 0     | да      |
| ... |   |       |         |
```

**А2. Прямоугольник.** Вводятся `w` и `h`. С помощью вложенного цикла выведи прямоугольник из символов `#`, а внутри заполни пробелами (только рамка).

```text
w = 5, h = 3
#####
#   #
#####
```

**А3. Срезы.** `months = ["янв", "фев", "мар", "апр", "май", "июн", "июл", "авг", "сен", "окт", "ноя", "дек"]`. С помощью срезов выведи: весенние месяцы, летние месяцы, каждый третий месяц, последние 4 месяца в обратном порядке.

**А4. Функция возвращает.** Функция `count_vowels(text)` **возвращает** количество гласных букв русского языка (`аеёиоуыэюя`). Примени функцию к двум предложениям и выведи, в каком из них больше гласных.

## Продвинутый уровень (карточка Б)

**Б1. Расписание уроков.** Словарь `{день: [список предметов]}`. Напиши функции: `add_lesson(schedule, day, subject)` (если дня нет – создаёт и добавляет), `busiest_day(schedule)` (день с наибольшим числом уроков), `find_subject(schedule, subject)` (список дней, когда есть предмет).

**Б2. Рекурсия.** Напиши рекурсивные функции: `reverse_text(s)` – переворачивает строку; `count_down_even(n)` – выводит чётные числа от n до 0; `fib(n)` – n-е число Фибоначчи.

**Б3. Таблица из файла.** В файле `marks.txt` в каждой строке записаны оценки одного ученика через пробел (`5 4 5 3`). Прочитай файл в 2D-список, выведи среднее каждого ученика и каждого столбца (четверти). Повреждённые строки пропускай.

## ★ Олимпиадная / творческая задача (карточка В)

**В1. Ханойские башни.** Рекурсивная функция `hanoi(n, from_rod, to_rod, via_rod)` должна выводить шаги перекладывания n дисков и возвращать количество шагов. Для n = 3 должно быть 7 шагов.

**В2. Магический квадрат.** Вводится таблица 3×3 (2D-список). Проверь, является ли она «магической»: суммы всех строк, столбцов и обеих диагоналей равны.

## Лист индивидуальной консультации (заполняют педагог и ученик вместе)

```text
Ученик: ____________________   Дата: 19.05.2027
Средний результат самостоятельных работ (№1–№6): ____%   Итоговый проект: ____%
Диагностический тест: ___/10   Пробелы: _____________________________
Вопрос ученика: _____________________________________________________
Летний индивидуальный план:
  1) Тема: __________  Задание: ______________________________
  2) Тема: __________  Задание: ______________________________
  3) Направление интереса (2-й год): игры / GUI / данные / веб / API
```

## Домашнее задание

1. По каждой теме, в которой ты ошибся на диагностике, выполни 1 задание (из карточки А или Б).
2. Перепиши в тетрадь летний план из листа индивидуальной консультации.
3. К следующему уроку: выбери самую понравившуюся программу из написанных за год (представим её на итоговом уроке).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Ответы диагностического теста:**

| № | Ответ | Пояснение |
|---|---|---|
| 1 | `3` | 3, 7, 11 |
| 2 | `[15, 16, 23] 23` | индексы 2, 3, 4; `a[-2]` – второй с конца |
| 3 | `noh` | `"nohtyP"[:3]` |
| 4 | `3` | i=0: 0 раз, i=1: 1 раз, i=2: 2 раза |
| 5 | `1 [1, 2]` | ключ один, к списку добавлен элемент |
| 6 | `5` | `x` – локальная, `n` не меняется |
| 7 | `11` | 4 + 3 + 2 + 1 + 1 |
| 8 | `[1, 2, 3]` | `b` и `a` – один и тот же список |
| 9 | `ошибка` | `int("3.5")` – `ValueError` |
| 10 | `{2, 3} {1, 2, 5}` | пересечение и объединение |

**А1.** Трассировка: (x, total) = (1, 0) → (3, 1) → (9, 4) → (27, 13); 27 < 20 ложно. Результат: `27 13`.

```python
x = 1
total = 0
while x < 20:
    total += x
    x = x * 3
print(x, total)
```

**А2.**

```python
w = int(input("w = "))
h = int(input("h = "))
for r in range(h):
    line = ""
    for c in range(w):
        if r == 0 or r == h - 1 or c == 0 or c == w - 1:
            line += "#"
        else:
            line += " "
    print(line)
```

**А3.**

```python
months = ["янв", "фев", "мар", "апр", "май", "июн", "июл", "авг", "сен", "окт", "ноя", "дек"]
print("Весна:", months[2:5])
print("Лето:", months[5:8])
print("Каждый третий:", months[::3])
print("Последние 4 (обратно):", months[:-5:-1])
```

Результат:
```text
Весна: ['мар', 'апр', 'май']
Лето: ['июн', 'июл', 'авг']
Каждый третий: ['янв', 'апр', 'июл', 'окт']
Последние 4 (обратно): ['дек', 'ноя', 'окт', 'сен']
```

**А4.**

```python
def count_vowels(text):
    vowels = "аеёиоуыэюя"
    count = 0
    for ch in text.lower():
        if ch in vowels:
            count += 1
    return count


s1 = "Знание – неисчерпаемый клад"
s2 = "Учиться – что иглой колодец копать"
v1 = count_vowels(s1)
v2 = count_vowels(s2)
print(v1, v2)
if v1 > v2:
    print("В первом предложении больше гласных")
elif v2 > v1:
    print("Во втором предложении больше гласных")
else:
    print("Поровну")
```

**Б1.**

```python
def add_lesson(schedule, day, subject):
    if day not in schedule:
        schedule[day] = []
    schedule[day].append(subject)


def busiest_day(schedule):
    best = None
    for day, lessons in schedule.items():
        if best is None or len(lessons) > len(schedule[best]):
            best = day
    return best


def find_subject(schedule, subject):
    return [day for day, lessons in schedule.items() if subject in lessons]


schedule = {}
add_lesson(schedule, "Понедельник", "Математика")
add_lesson(schedule, "Понедельник", "Python")
add_lesson(schedule, "Среда", "Python")
add_lesson(schedule, "Среда", "Английский")
add_lesson(schedule, "Среда", "История")
print(busiest_day(schedule))
print(find_subject(schedule, "Python"))
```

Результат:
```text
Среда
['Понедельник', 'Среда']
```

**Б2.**

```python
def reverse_text(s):
    if len(s) <= 1:
        return s
    return reverse_text(s[1:]) + s[0]


def count_down_even(n):
    if n < 0:
        return
    if n % 2 == 0:
        print(n, end=" ")
    count_down_even(n - 1)


def fib(n):
    if n <= 2:
        return 1
    return fib(n - 1) + fib(n - 2)


print(reverse_text("Алматы"))
count_down_even(10)
print()
print(fib(10))
```

Результат:
```text
ытамлА
10 8 6 4 2 0 
55
```

**Б3.**

```python
table = []
try:
    with open("marks.txt", encoding="utf-8") as f:
        for line in f:
            try:
                row = [int(x) for x in line.split()]
            except ValueError:
                continue
            if row:
                table.append(row)
except FileNotFoundError:
    print("marks.txt не найден")

for i in range(len(table)):
    print(f"Ученик {i + 1}: {sum(table[i]) / len(table[i]):.2f}")
if table:
    cols = min(len(row) for row in table)
    for c in range(cols):
        column = [row[c] for row in table]
        print(f"Четверть {c + 1}: {sum(column) / len(column):.2f}")
```

**В1.**

```python
def hanoi(n, from_rod, to_rod, via_rod):
    if n == 0:
        return 0
    steps = hanoi(n - 1, from_rod, via_rod, to_rod)
    print(f"Диск {n}: {from_rod} -> {to_rod}")
    steps += 1
    steps += hanoi(n - 1, via_rod, to_rod, from_rod)
    return steps


print("Всего:", hanoi(3, "A", "C", "B"), "шагов")
```

**В2.**

```python
square = [
    [2, 7, 6],
    [9, 5, 1],
    [4, 3, 8],
]
n = len(square)
target = sum(square[0])
sums = []
for r in range(n):
    sums.append(sum(square[r]))
for c in range(n):
    sums.append(sum([square[r][c] for r in range(n)]))
sums.append(sum([square[i][i] for i in range(n)]))
sums.append(sum([square[i][n - 1 - i] for i in range(n)]))
is_magic = True
for s in sums:
    if s != target:
        is_magic = False
print("Магический квадрат!" if is_magic else "Не магический")
```

</details>
