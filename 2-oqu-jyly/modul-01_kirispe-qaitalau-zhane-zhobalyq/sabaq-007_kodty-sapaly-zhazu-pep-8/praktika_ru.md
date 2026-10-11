# Урок 7. Практические задания: PEP 8, имена, комментарии, docstring

## Базовый уровень

### Задание 1. «Детектив стиля»
В коде ниже не менее **10** нарушений PEP 8. Запиши каждое в тетрадь с номером строки (что не так и как должно быть), затем исправь код.

```python
import random,math
def AreaCircle( R ):
  pi=3.14159
  return pi*R**2
def get(x,y = 2) :
    return x+y
MaxValue=100
l=[1,2,3]
if len(l)==0 : print("пусто")
print(AreaCircle(2),get(3))
```

### Задание 2. Правильно дай имена
Заполни таблицу: придумай имя по PEP 8 для описания слева.

| Описание | Вид | Твоё имя |
|---|---|---|
| Год рождения ученика | переменная | |
| Максимальное число учеников в классе (не меняется) | константа | |
| Найти самую высокую оценку в списке | функция | |
| Признак того, что игрок жив | логическая переменная | |
| Банковский счёт | класс | |
| Файл для работы с оценками | модуль | |
| Названия всех городов | список | |

### Задание 3. Напиши docstring
Добавь docstring к функциям ниже (одной — однострочный, другой — многострочный с разделами Args/Returns). Затем проверь через `help()`.

```python
def bmi(weight, height):
    return round(weight / height ** 2, 1)


def ticket_price(age, is_student=False):
    if age < 7:
        return 0
    price = 250
    if is_student:
        price = price // 2
    return price
```

```text
>>> help(bmi)
bmi(weight, height)
    Возвращает индекс массы тела (ИМТ).
```

### Задание 4. Рассортируй комментарии
Какие из комментариев ниже полезны, а какие бесполезны? Удали бесполезные и, если нужно, улучши имя переменной.

```python
x = 0  # переменная x равна 0
speed = 5  # пикселей/кадр; при 60 FPS игрок пересекает экран за ~3 секунды
for i in range(10):  # цикл 10 раз
    x += speed  # прибавляем к x speed
# При переходе на новый уровень скорость растёт на 20%, но не должна превышать 12
speed = min(speed * 1.2, 12)
```

## Продвинутый уровень

### Задание 5. Полный рефакторинг
Выполни рефакторинг программы «Магазин» ниже:
- отформатируй по PEP 8;
- дай осмысленные имена;
- раздели минимум на 3 функции, к каждой напиши docstring;
- вынеси константы;
- добавь `main()` и `if __name__ == "__main__":`.

**Поведение программы не должно измениться** (на одинаковый ввод — одинаковый вывод).

```python
d={"хлеб":180,"молоко":520,"яйцо":75}
s=0
while True:
 a=input("Товар (пусто — завершить): ")
 if a=="":break
 if a not in d:print("нет");continue
 b=int(input("количество: "))
 s=s+d[a]*b
if s>5000:s=s*0.95
print("Итого:",round(s,2),"₸")
```

### Задание 6. Использование Ruff (или flake8)
1. Установи в виртуальном окружении `pip install ruff`.
2. Сохрани **исходный** код задания 1 в файл `bad_style.py` и выполни `ruff check bad_style.py`. Какие коды ошибок появились (`E…`, `F…`)?
3. Выполни `ruff format bad_style.py` и посмотри на результат. Какие нарушения форматировщик **не смог исправить** (например, имена)? Почему?
4. Включи в VS Code настройку «Format On Save».

### Задание 7. Документация модуля
Добавь в файл `grades_utils.py` из 3-го урока (или в свой модуль) docstring модуля, docstring к каждой функции и аннотации типов. В другом файле выполни `import grades_utils` → `help(grades_utils)` и проверь, что выводится полная «справка» модуля. Отправь изменения на GitHub: `git commit -m "Добавлены docstring и аннотации типов"`.

## ★ Олимпиадная / творческая задача

### Задание 8. Свой «мини-линтер»
Напиши программу `mini_lint.py`: она построчно читает заданный файл `.py` и находит такие нарушения:
- строка длиннее 79 символов;
- отступ не кратен 4;
- в конце строки лишний пробел;
- есть символ Tab;
- имя после `def` не в `snake_case` (есть заглавная буква);
- в следующей строке после объявления функции нет docstring.

```text
bad_style.py:2: имя функции не в snake_case: AreaCircle
bad_style.py:3: отступ не кратен 4 (2)
bad_style.py:3: нет docstring: AreaCircle
...
Всего: 7 предупреждений
```

### Задание 9. «Генератор документации»
С помощью модуля `inspect` напиши программу, которая выводит имена, параметры и docstring всех функций модуля в виде таблицы Markdown (автоматическое создание документации проекта). Функции без docstring помечай «⚠ не задокументировано».

## Домашнее задание

1. Выполни полный рефакторинг проекта калькулятора из портфолио по PEP 8, добавь docstring и сделай push на GitHub.
2. «Мои правила написания кода» — 7 правил (своими словами, с примерами) — запиши в конец README или в отдельный файл `STYLE.md`.
3. Следующий урок — диагностическая работа: повтори темы 1-го года и инструменты этого модуля (venv, Git, PEP 8). Подумай над идеей проекта, который хочешь делать в течение года.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1** — нарушения: (1) два импорта в одной строке; (2) `math` не используется; (3) нет 2 пустых строк перед функциями; (4) `AreaCircle` — PascalCase, для функции нужен `snake_case`; (5) лишние пробелы внутри скобок `( R )`; (6) имя параметра `R` с заглавной буквы; (7) отступ 2 пробела; (8) `pi=3.14159` — нет пробелов вокруг `=`, к тому же правильнее использовать `math.pi`; (9) в значении по умолчанию `y = 2` не должно быть пробелов; (10) пробел перед `:` — `) :`; (11) `get` — бессмысленное имя; (12) `MaxValue` — константа должна быть `MAX_VALUE`; (13) `l` — путающее имя; (14) `if ... : print(...)` в одной строке; (15) вместо `len(l)==0` — `not numbers`; (16) нет пробела после запятой; (17) нет docstring.
```python
import math

MAX_VALUE = 100


def circle_area(radius):
    """Возвращает площадь круга."""
    return math.pi * radius ** 2


def add_numbers(x, y=2):
    """Возвращает сумму двух чисел."""
    return x + y


numbers = [1, 2, 3]
if not numbers:
    print("пусто")
print(circle_area(2), add_numbers(3))
```

**Задание 2 (образец)**: `birth_year`, `MAX_CLASS_SIZE`, `find_max_grade()`, `is_alive`, `BankAccount`, `grades_utils.py`, `city_names`.

**Задание 3**
```python
def bmi(weight, height):
    """Возвращает индекс массы тела (ИМТ)."""
    return round(weight / height ** 2, 1)


def ticket_price(age, is_student=False):
    """Вычисляет стоимость автобусного билета.

    Args:
        age: возраст пассажира.
        is_student: True, если школьник или студент (скидка 50%).

    Returns:
        Цена билета в тенге (до 7 лет — бесплатно).
    """
    if age < 7:
        return 0
    price = 250
    if is_student:
        price = price // 2
    return price


help(bmi)
help(ticket_price)
```

**Задание 4** — полезные: комментарий в строке `speed` (единицы и смысл) и комментарий о переходе на уровень (причина и предел). Остальные повторяют код.
```python
position_x = 0
speed = 5  # пикселей/кадр; при 60 FPS игрок пересекает экран за ~3 секунды
for _ in range(10):
    position_x += speed
# При переходе на новый уровень скорость растёт на 20%, но не должна превышать 12
speed = min(speed * 1.2, 12)
```

**Задание 5**
```python
"""Программа «Магазин»: вычисляет сумму корзины."""

PRICES = {"хлеб": 180, "молоко": 520, "яйцо": 75}
DISCOUNT_THRESHOLD = 5000
DISCOUNT_RATE = 0.05


def read_cart(prices):
    """Считывает товары от пользователя и возвращает общую сумму корзины."""
    total = 0
    while True:
        product = input("Товар (пусто — завершить): ")
        if product == "":
            break
        if product not in prices:
            print("нет")
            continue
        count = int(input("количество: "))
        total += prices[product] * count
    return total


def apply_discount(total):
    """Применяет скидку, если сумма превышает порог."""
    if total > DISCOUNT_THRESHOLD:
        return total * (1 - DISCOUNT_RATE)
    return total


def main():
    total = apply_discount(read_cart(PRICES))
    print("Итого:", round(total, 2), "₸")


if __name__ == "__main__":
    main()
```

**Задание 6** — `ruff check` с правилами по умолчанию показывает коды вроде `E401` (несколько импортов в одной строке), `F401` (неиспользуемый импорт), `E701` (несколько операторов в одной строке), `E741` (имя `l`); `ruff format` исправляет пробелы и отступы, но не меняет имена — смысл имени знает только человек, а его изменение может сломать другие части программы.

**Задание 8**
```python
import re
import sys

MAX_LINE = 79
SNAKE_CASE = re.compile(r"^[a-z_][a-z0-9_]*$")


def check_file(path):
    """Проверяет файл и возвращает список предупреждений."""
    warnings = []
    with open(path, encoding="utf-8") as file:
        lines = file.read().splitlines()

    for number, line in enumerate(lines, start=1):
        if len(line) > MAX_LINE:
            warnings.append((number, f"слишком длинная строка ({len(line)} символов)"))
        if "\t" in line:
            warnings.append((number, "есть символ Tab"))
        if line != line.rstrip():
            warnings.append((number, "лишний пробел в конце строки"))
        indent = len(line) - len(line.lstrip(" "))
        if line.strip() and indent % 4 != 0:
            warnings.append((number, f"отступ не кратен 4 ({indent})"))

        match = re.match(r"\s*def\s+(\w+)", line)
        if match:
            name = match.group(1)
            if not SNAKE_CASE.match(name):
                warnings.append((number, f"имя функции не в snake_case: {name}"))
            next_line = lines[number].strip() if number < len(lines) else ""
            if not next_line.startswith(('"""', "'''")):
                warnings.append((number, f"нет docstring: {name}"))
    return warnings


def main():
    path = sys.argv[1] if len(sys.argv) > 1 else input("Файл: ")
    warnings = check_file(path)
    for number, text in warnings:
        print(f"{path}:{number}: {text}")
    print("Всего:", len(warnings), "предупреждений")


if __name__ == "__main__":
    main()
```

**Задание 9**
```python
import inspect

import grades_utils


def document_module(module):
    """Возвращает таблицу функций модуля в формате Markdown."""
    rows = ["| Функция | Параметры | Описание |", "|---|---|---|"]
    for name, func in inspect.getmembers(module, inspect.isfunction):
        signature = inspect.signature(func)
        doc = inspect.getdoc(func)
        summary = doc.splitlines()[0] if doc else "⚠ не задокументировано"
        rows.append(f"| `{name}` | `{signature}` | {summary} |")
    return "\n".join(rows)


print(document_module(grades_utils))
```

</details>
