# Урок 54. Практические задания: оператор return

В каждом задании функция должна **возвращать** результат (`return`), а вывод на экран — в основной программе.

## Базовый уровень

### Задание 1. Окружность
Функции `circle_area(r)` и `circle_length(r)` должны возвращать площадь круга и длину окружности (π = 3.14159). Для окружности радиусом 5 выведите обе величины с 2 знаками после запятой.

```text
Площадь: 78.54
Длина: 31.42
```

### Задание 2. Максимум
Функция `max_of_three(a, b, c)` должна возвращать наибольшее из трёх чисел (без `max()`, с помощью `if`). Используя её, найдите наибольшее из четырёх чисел: `max_of_three(max_of_three(a, b, c), d, d)`.

### Задание 3. Чётное ли?
Напишите функцию `is_even(n)`. С её помощью найдите количество чётных среди 5 чисел, введённых пользователем.

### Задание 4. ИМТ (индекс массы тела)
Функция `bmi(weight, height)` должна возвращать значение ИМТ = масса / рост² (рост в метрах). Функция `bmi_category(value)` должна возвращать категорию: ниже 18.5 — «недостаточный вес», 18.5–24.9 — «норма», 25 и выше — «избыточный вес».

Примечание: реальные нормы для детей зависят от возраста, это только упражнение по программированию.

### Задание 5. Сумма цифр
Функция `digit_sum(n)` должна возвращать сумму цифр целого числа. `digit_sum(2027)` → `11`.

## Продвинутый уровень

### Задание 6. Простые числа
Напишите функцию `is_prime(n)`. С её помощью найдите:
- количество простых чисел от 1 до 100;
- ближайшее простое число, следующее за числом, введённым пользователем.

### Задание 7. Время
Функция `to_seconds(h, m, s)` должна переводить время в секунды, а `from_seconds(total)` — выполнять обратное преобразование и возвращать кортеж `(h, m, s)`. Вычислите промежуток между двумя уроками (например, `09:15:00` и `10:45:30`).

### Задание 8. Оценка буквой
Функция `letter_grade(score)` должна переводить оценку по 100-балльной шкале в букву: A (90+), B (75–89), C (60–74), D (50–59), F (ниже 50). Функция `average(scores)` должна возвращать среднее значение списка. Программа должна вывести средний балл класса и его буквенную оценку.

## ★ Олимпиадная / творческая задача

### Задание 9. НОД и НОК
Функция `gcd(a, b)` должна возвращать наибольший общий делитель по алгоритму Евклида, функция `lcm(a, b)` — наименьшее общее кратное через `gcd`. Напишите функцию `reduce_fraction(num, den)`, сокращающую дробь: `reduce_fraction(12, 18)` → `(2, 3)`.

Алгоритм Евклида: пока `b ≠ 0`, выполнять `a, b = b, a % b`; в конце `a` — НОД.

### Задание 10. Счастливый билет
Номер автобусного билета состоит из 6 цифр. Если сумма первых трёх цифр равна сумме последних трёх — билет «счастливый». Напишите функцию `is_lucky(ticket)` и подсчитайте, сколько счастливых билетов от `000000` до `999999`.

## Домашнее задание

1. Напишите функции `km_to_miles(km)` и `miles_to_km(miles)` и переведите расстояние Алматы–Астана (≈ 1215 км) в мили.
2. Функция `count_vowels(text)` должна возвращать количество гласных в тексте. Примените её к 3 разным пословицам.
3. Превратите любые 2 задания из 45-го урока в функции, использующие `return`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
PI = 3.14159


def circle_area(r):
    return PI * r ** 2


def circle_length(r):
    return 2 * PI * r


print(f"Площадь: {circle_area(5):.2f}")
print(f"Длина: {circle_length(5):.2f}")
```

**Задание 2**
```python
def max_of_three(a, b, c):
    biggest = a
    if b > biggest:
        biggest = b
    if c > biggest:
        biggest = c
    return biggest


print(max_of_three(4, 9, 2))
print(max_of_three(max_of_three(4, 9, 2), 15, 15))
```

**Задание 3**
```python
def is_even(n):
    return n % 2 == 0


count = 0
for i in range(5):
    if is_even(int(input("Число: "))):
        count += 1
print("Чётных чисел:", count)
```

**Задание 4**
```python
def bmi(weight, height):
    return weight / height ** 2


def bmi_category(value):
    if value < 18.5:
        return "недостаточный вес"
    elif value < 25:
        return "норма"
    return "избыточный вес"


b = bmi(50, 1.6)
print(f"ИМТ: {b:.1f} — {bmi_category(b)}")
```

**Задание 5**
```python
def digit_sum(n):
    total = 0
    for ch in str(abs(n)):
        total += int(ch)
    return total


print(digit_sum(2027))
```

**Задание 6**
```python
def is_prime(n):
    if n < 2:
        return False
    for d in range(2, int(n ** 0.5) + 1):
        if n % d == 0:
            return False
    return True


count = 0
for x in range(1, 101):
    if is_prime(x):
        count += 1
print("Простых чисел от 1 до 100:", count)

n = int(input("Число: ")) + 1
while not is_prime(n):
    n += 1
print("Следующее простое число:", n)
```
В промежутке от 1 до 100 — 25 простых чисел.

**Задание 7**
```python
def to_seconds(h, m, s):
    return h * 3600 + m * 60 + s


def from_seconds(total):
    h = total // 3600
    m = total % 3600 // 60
    s = total % 60
    return h, m, s


start = to_seconds(9, 15, 0)
end = to_seconds(10, 45, 30)
h, m, s = from_seconds(end - start)
print(f"Разница: {h} ч {m} мин {s} с")
```
Результат:
```text
Разница: 1 ч 30 мин 30 с
```

**Задание 8**
```python
def letter_grade(score):
    if score >= 90:
        return "A"
    if score >= 75:
        return "B"
    if score >= 60:
        return "C"
    if score >= 50:
        return "D"
    return "F"


def average(scores):
    return sum(scores) / len(scores)


class_scores = [88, 92, 67, 75, 54, 81]
avg = average(class_scores)
print(f"Средний балл: {avg:.1f}, оценка: {letter_grade(avg)}")
```
Результат:
```text
Средний балл: 76.2, оценка: B
```

**Задание 9**
```python
def gcd(a, b):
    while b != 0:
        a, b = b, a % b
    return a


def lcm(a, b):
    return a * b // gcd(a, b)


def reduce_fraction(num, den):
    g = gcd(num, den)
    return num // g, den // g


print(gcd(12, 18), lcm(12, 18))
print(reduce_fraction(12, 18))
```
Результат:
```text
6 36
(2, 3)
```

**Задание 10**
```python
def digit_sum(s):
    total = 0
    for ch in s:
        total += int(ch)
    return total


def is_lucky(ticket):
    return digit_sum(ticket[:3]) == digit_sum(ticket[3:])


count = 0
for number in range(1000000):
    if is_lucky(f"{number:06d}"):
        count += 1
print(count)
```
Результат:
```text
55252
```
(Вычисление может занять несколько секунд.)
</details>
