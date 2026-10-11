# Урок 55. Практические задания: область видимости

## Базовый уровень

### Задание 1. Что выведет? (1)
Не запуская программу, запишите её результат в тетрадь. Затем проверьте в Thonny.
```python
a = 5


def f():
    a = 10
    print("Внутри f:", a)


f()
print("Снаружи:", a)
```

### Задание 2. Что выведет? (2)
```python
x = 1


def change(x):
    x = x + 100
    print("В функции:", x)
    return x


y = change(x)
print(x, y)
```

### Задание 3. Что выведет? (3)
```python
name = "Айгерим"


def hello():
    print("Привет,", name)


hello()
name = "Бекзат"
hello()
```

### Задание 4. Исправь ошибку
Программа должна увеличивать игровые очки, но выдаёт ошибку. Исправьте двумя способами: а) с помощью `global`; б) с помощью параметра и `return`. Какой лучше? Почему?
```python
score = 0


def add_points(points):
    score = score + points


add_points(10)
add_points(5)
print(score)
```

### Задание 5. Константы
Создайте глобальные константы: `SCHOOL = "Школа №120"`, `CITY = "Алматы"`, `YEAR = 2027`. Функция `make_certificate(name, place)`, используя эти константы, должна **возвращать** (в виде строки) текст грамоты олимпиады. Выведите грамоты для 3 учеников.

## Продвинутый уровень

### Задание 6. Банковский счёт
Смоделируйте банковский счёт без глобальных переменных: функции `deposit(balance, amount)` и `withdraw(balance, amount)` должны возвращать новый баланс (если денег не хватает — вывести предупреждение и вернуть прежний баланс). В основной программе выполняйте операции через меню.

### Задание 7. Счётчик вызовов
Функция `greet(name)` должна считать, сколько раз её вызвали. Сделайте двумя способами:
- а) глобальная переменная `calls` и `global`;
- б) глобальный словарь `stats = {"calls": 0}` (почему в этом случае `global` не нужен?).

### Задание 8. Порядок поиска
Какое `value` и где используется в следующем коде? Рядом с каждым `print` напишите «локальная», «глобальная» или «ошибка», затем проверьте.
```python
value = "G"


def outer_print():
    print(value)


def inner_change():
    value = "L"
    print(value)


def tricky():
    print(value)


outer_print()
inner_change()
tricky()
print(value)
```

## ★ Олимпиадная / творческая задача

### Задание 9. Игра «Собери числа»
Игрок играет 5 раундов. В каждом раунде программа запрашивает число от 1 до 10; если число чётное — +2 очка, если нечётное — +1 очко, если 7 — очки обнуляются. **Не используя `global`**, передавайте состояние игры (очки, раунд) через параметры и `return`: `play_round(score, number)` → новые очки.

### Задание 10. Исследование
Вызовите встроенные функции `globals()` и `locals()` внутри функции и вне её и исследуйте, что они возвращают (выводите на экран только `locals()` внутри функции). Опишите увиденное в 3–4 предложениях.

## Домашнее задание

1. Придумайте собственную аналогию, похожую на «школу и класс», и напишите небольшой текст, объясняющий область видимости.
2. Найдите в одной из своих программ с прошлых уроков функцию, использующую `global` (или зависящую от глобальной переменной), и переделайте её на параметр + `return`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```text
Внутри f: 10
Снаружи: 5
```

**Задание 2**
```text
В функции: 101
1 101
```

**Задание 3** — функция читает значение глобальной переменной **на момент вызова**:
```text
Привет, Айгерим
Привет, Бекзат
```

**Задание 4**
```python
# а) с помощью global
score = 0


def add_points(points):
    global score
    score = score + points


add_points(10)
add_points(5)
print(score)
```

```python
# б) параметр + return (лучше: функция независима, её легко тестировать)
def add_points(score, points):
    return score + points


score = 0
score = add_points(score, 10)
score = add_points(score, 5)
print(score)
```
Результат:
```text
15
```

**Задание 5**
```python
SCHOOL = "Школа №120"
CITY = "Алматы"
YEAR = 2027


def make_certificate(name, place):
    return (f"ГРАМОТА\n{name}, ученик(ца) учреждения «{SCHOOL}»,\n"
            f"занял(а) {place} место в городской олимпиаде по Python, г. {CITY}. {YEAR} г.")


for student, place in [("Нурай", 1), ("Алихан", 2), ("Тимур", 3)]:
    print(make_certificate(student, place))
    print()
```

**Задание 6**
```python
def deposit(balance, amount):
    return balance + amount


def withdraw(balance, amount):
    if amount > balance:
        print("Недостаточно средств!")
        return balance
    return balance - amount


balance = 0
while True:
    choice = input("1-внести, 2-снять, 3-баланс, 0-выход: ")
    if choice == "1":
        balance = deposit(balance, int(input("Сумма: ")))
    elif choice == "2":
        balance = withdraw(balance, int(input("Сумма: ")))
    elif choice == "3":
        print("Баланс:", balance, "тг")
    elif choice == "0":
        break
```

**Задание 7**
```python
# а)
calls = 0


def greet(name):
    global calls
    calls += 1
    print("Привет,", name)


greet("Дана")
greet("Ерлан")
print("Вызовов:", calls)
```

```python
# б) меняем содержимое словаря, переменную stats не переприсваиваем — global не нужен
stats = {"calls": 0}


def greet(name):
    stats["calls"] += 1
    print("Привет,", name)


greet("Дана")
greet("Ерлан")
print("Вызовов:", stats["calls"])
```

**Задание 8**
```text
G   ← outer_print: глобальная
L   ← inner_change: локальная
G   ← tricky: глобальная
G   ← снаружи: глобальная (inner_change её не изменила)
```

**Задание 9**
```python
def play_round(score, number):
    if number == 7:
        return 0
    if number % 2 == 0:
        return score + 2
    return score + 1


score = 0
for round_no in range(1, 6):
    n = int(input(f"Раунд {round_no}, число (1-10): "))
    score = play_round(score, n)
    print("Очки:", score)
print("Итоговые очки:", score)
```

**Задание 10**
```python
x = 5


def demo(a):
    b = a * 2
    print(locals())


demo(3)
print("x" in globals())
```
Результат:
```text
{'a': 3, 'b': 6}
True
```
`locals()` — словарь локальных переменных, `globals()` — словарь глобальных имён (в него входят и имена функций).
</details>
