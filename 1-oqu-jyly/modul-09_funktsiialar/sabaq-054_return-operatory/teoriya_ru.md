# Урок 54. Оператор return. Возврат значения из функции.

## Темы

1. Оператор return
2. print и return — главное различие
3. Способы использования возвращённого значения
4. return завершает функцию
5. Функции, возвращающие True / False
6. Возврат нескольких значений
7. None — «ничего»
8. Частые ошибки
9. Основные понятия

## 1. Оператор return

```
def функция(параметры):
    ...
    return значение
```

`return`:
1. **возвращает** значение в место **вызова** функции;
2. **сразу завершает** работу функции.

```python
def area(a, b):
    return a * b


room1 = area(3, 4)
room2 = area(5, 2)
print("Первая комната:", room1)
print("Общая площадь:", room1 + room2)
print("Кухня большая?", area(4, 4) > 15)
```

Результат:
```text
Первая комната: 12
Общая площадь: 22
Кухня большая? True
```

Аналогия — **банкомат**: вы вставляете карту, вводите сумму (аргументы), банкомат что-то вычисляет внутри и **выдаёт деньги вам в руки** (return). Что делать с этими деньгами, решаете вы сами. А `print` — это как если бы банкомат только **показал** на экране «20 000 тенге»: денег в руках нет!

## 2. print и return — главное различие

| | `print()` | `return` |
|---|---|---|
| Что делает | выводит значение **на экран** | возвращает значение **программе** |
| Можно ли использовать результат | нет | да: в переменной, выражении, условии |
| Завершает ли функцию | нет | да |
| Для кого | для человека | для программы |

```python
def square_print(n):
    print(n * n)


def square_return(n):
    return n * n


a = square_print(5)      # на экран выводится 25, a = None
b = square_return(5)     # на экран ничего не выводится, b = 25
print(a, b)
print(square_return(3) + square_return(4))
```

Результат:
```text
25
None 25
25
```

**Правило:** если функция что-то **вычисляет**, результат лучше возвращать через `return`. А выводить его на экран — решение вызывающей программы. Такую функцию можно повторно использовать где угодно.

## 3. Способы использования возвращённого значения

```python
def celsius_to_fahrenheit(c):
    return c * 9 / 5 + 32


# 1) сохранить в переменную
t = celsius_to_fahrenheit(25)
print(t)

# 2) в выражении
print(celsius_to_fahrenheit(0) - 32)

# 3) в условии
if celsius_to_fahrenheit(-40) == -40:
    print("При -40 градусах шкалы совпадают!")

# 4) в f-строке
print(f"В Алматы +30°C = {celsius_to_fahrenheit(30)}°F")

# 5) как аргумент другой функции
print(round(celsius_to_fahrenheit(36.6), 1))
```

Результат:
```text
77.0
0.0
При -40 градусах шкалы совпадают!
В Алматы +30°C = 86.0°F
97.9
```

### Цепочка функций

Результат одной функции передаётся в другую:

```python
def price_with_discount(price, percent):
    return price - price * percent / 100


def price_with_tax(price, tax=12):
    return price + price * tax / 100


final = price_with_tax(price_with_discount(10000, 20))
print(final)
```

Результат:
```text
8960.0
```

## 4. return завершает функцию

Строки после `return` не выполняются:

```python
def test():
    print("до return")
    return 1
    print("Эта строка никогда не выполнится")


print(test())
```

Результат:
```text
до return
1
```

### Несколько return — с ветвлением

```python
def sign(n):
    if n > 0:
        return "положительное"
    elif n < 0:
        return "отрицательное"
    return "ноль"


print(sign(5), sign(-3), sign(0))
```

Результат:
```text
положительное отрицательное ноль
```

### Досрочный возврат из цикла

```python
def find_index(items, target):
    for i in range(len(items)):
        if items[i] == target:
            return i          # найдено — сразу выходим
    return -1                 # цикл завершён, не найдено


cities = ["Алматы", "Астана", "Шымкент"]
print(find_index(cities, "Астана"))
print(find_index(cities, "Тараз"))
```

Результат:
```text
1
-1
```

Это работает как метод `str.find()`: если не найдено — -1.

## 5. Функции, возвращающие True / False

Их имена обычно начинаются с `is_`, `has_`, `can_`. Их используем прямо в `if`:

```python
def is_even(n):
    return n % 2 == 0


def is_palindrome(text):
    clean = text.lower().replace(" ", "")
    return clean == clean[::-1]


def is_leap_year(year):
    return (year % 4 == 0 and year % 100 != 0) or year % 400 == 0


print(is_even(10), is_even(7))
if is_palindrome("Казак"):
    print("Палиндром!")
for y in [2024, 2027, 2000, 1900]:
    print(y, is_leap_year(y))
```

Результат:
```text
True False
Палиндром!
2024 True
2027 False
2000 True
1900 False
```

`return n % 2 == 0` — результат сравнения сам по себе `True` или `False`, поэтому нет необходимости писать длинно `if ... : return True else: return False`.

### Проверка простого числа

```python
def is_prime(n):
    if n < 2:
        return False
    for d in range(2, int(n ** 0.5) + 1):
        if n % d == 0:
            return False
    return True


primes = []
for x in range(1, 30):
    if is_prime(x):
        primes.append(x)
print(primes)
```

Результат:
```text
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
```

## 6. Возврат нескольких значений

В Python можно вернуть через запятую несколько значений — они передаются в виде **кортежа** и сразу «распаковываются» в несколько переменных:

```python
def min_max_avg(numbers):
    return min(numbers), max(numbers), sum(numbers) / len(numbers)


grades = [5, 3, 4, 5, 4]
low, high, avg = min_max_avg(grades)
print(f"Наименьшая: {low}, наибольшая: {high}, средняя: {avg}")
print(min_max_avg([1, 2, 3]))
```

Результат:
```text
Наименьшая: 3, наибольшая: 5, средняя: 4.2
(1, 3, 2.0)
```

```python
def split_time(total_minutes):
    return total_minutes // 60, total_minutes % 60


h, m = split_time(135)
print(f"{h} ч {m} мин")
```

Результат:
```text
2 ч 15 мин
```

## 7. None — «ничего»

Функция без `return` (или с пустым `return`) возвращает `None`. `None` — специальное значение «нет значения», его тип — `NoneType`.

```python
def nothing():
    pass


result = nothing()
print(result, type(result))
```

Результат:
```text
None <class 'NoneType'>
```

Функции, не возвращающие значения, тоже нужны — например, вывод меню, рисование. Но **вычисляющие** функции всегда должны использовать `return`.

## Частые ошибки

**1. print вместо return**

```python
def add(a, b):
    print(a + b)          # неверная идея: результат не возвращается

def add_ok(a, b):
    return a + b

total = add_ok(2, 3) * 10
print(total)              # 50
```

**2. Возвращённое значение никуда не сохраняют**

```python
def double(x):
    return x * 2

double(5)                 # результат потерян
y = double(5)
print(y)                  # 10
```

**3. Слишком ранний return внутри цикла**

```python
def total_sum(numbers):
    s = 0
    for n in numbers:
        s += n
        # return s        # ошибка: выйдет уже после первого элемента
    return s              # правильно: после цикла

print(total_sum([1, 2, 3]))   # 6
```

**4. Не во всех ветках есть return**

```python
def grade(score):
    if score >= 50:
        return "сдал"
    # при score < 50 вернётся None!
    return "не сдал"

print(grade(30))
```

## Основные понятия

| Понятие | Пояснение |
|---|---|
| `return` | оператор, возвращающий значение из функции и завершающий её |
| Возвращаемое значение | результат, передаваемый в место вызова функции |
| `None` | «нет значения»; результат функции без return |
| Предикат | функция, возвращающая `True`/`False` (`is_even`) |
| Досрочный возврат | немедленный выход из функции при выполнении условия |
| Возврат нескольких значений | `return a, b` — возвращает кортеж |

## Примечание

- В интерактивном режиме (`>>>`) возвращённое значение функции выводится на экран автоматически, а в файле `.py` — нет. Поэтому в файле для просмотра результата нужен `print()`.
- PEP 8 рекомендует единообразные `return`: если одна ветка возвращает значение, остальные тоже должны явно возвращать значение (при необходимости `return None`).
- `return a, b` возвращает кортеж; при распаковке число переменных должно совпадать с числом элементов, иначе — `ValueError`.
- Дробные числа лучше не сравнивать через `==` (`0.1 + 0.2 == 0.3` → `False`); для этого есть `math.isclose()`.
- Тип возвращаемого значения можно указать аннотацией: `def area(a: float, b: float) -> float:` — это только подсказка, Python её не проверяет.
- Документация: https://docs.python.org/3/reference/simple_stmts.html#the-return-statement
