# Урок 62. Практические задания: модули, import, math

## Базовый уровень

**Задание 1. Три способа импорта.** Вычисли квадратный корень из чисел 144, 225 и 2 тремя разными способами: `import math`, `from math import sqrt`, `import math as m`. Результаты должны совпасть.

```text
12.0 15.0 1.4142135623730951
```

**Задание 2. Пицца.** В чём больше «пиццы»: в одной пицце диаметром 30 см или в двух пиццах диаметром 20 см? Вычисли площади и сравни (`math.pi`).

```text
Большая пицца: 706.86 см²
Две маленькие пиццы: 628.32 см²
Большая пицца выгоднее!
```

**Задание 3. Умное округление.**
а) На экскурсию едут 130 учеников школы, в одном автобусе 45 мест. Сколько нужно автобусов?
б) В одну коробку помещается 12 яиц. Сколько **полных** коробок получится из 100 яиц?
в) Нужно покрасить 23.5 м² площади, одной банки краски хватает на 5 м². Сколько банок нужно купить?
Выбери `ceil` или `floor` и обоснуй свой выбор в комментарии.

**Задание 4. Факториал и НОД.** Пользователь вводит два натуральных числа. Программа выводит их НОД, НОК и факториал первого числа.

```text
a = 12
b = 18
НОД(12, 18) = 6
НОК(12, 18) = 36
12! = 479001600
```

**Задание 5. Пифагор.** Вводятся катеты прямоугольного треугольника, найди гипотенузу двумя способами: `math.sqrt(a**2 + b**2)` и `math.hypot(a, b)`.

## Продвинутый уровень

**Задание 6. Квадратное уравнение.** Вводятся коэффициенты уравнения `ax² + bx + c = 0`. Найди корни через дискриминант (`math.sqrt`). Если D < 0 – «Действительных корней нет», если a = 0 – «Это не квадратное уравнение». Обработай ввод не-числа с помощью `try...except`.

```text
a = 1
b = -5
c = 6
x1 = 3.0, x2 = 2.0
```

**Задание 7. Высота по тени.** Солнечные лучи падают на землю под углом α градусов. Длина тени дерева – L метров. Высота дерева `h = L * tan(α)`. Вводятся α и L, выведи высоту с 2 знаками после запятой.

```text
Угол (градусы): 45
Тень (м): 8
Высота дерева: 8.00 м
```

**Задание 8. Мой модуль.** Создай модуль `school_math.py` и напиши в нём не менее 4 функций: `is_prime(n)` (простое ли число?), `bmi(weight, height)` (ИМТ), `percent(part, total)`, `average(numbers)`. В другом файле `main.py` импортируй модуль и проверь каждую функцию.

## ★ Олимпиадная / творческая задача

**Задание 9. Охота на π.** Ряд Лейбница: π/4 = 1 − 1/3 + 1/5 − 1/7 + … Вычисли приближённое значение π в цикле. Сколько членов ряда нужно, чтобы отличие от `math.pi` было меньше 0.0001?

**Задание 10. Turtle + math.** Объедини модули `turtle` и `math` и нарисуй график функции `y = sin(x)`: меняй x от −360° до 360° и соединяй точки с помощью `goto(x, 100 * sin(radians(x)))`. Нарисуй и оси координат.

## Домашнее задание

1. «Геометрический калькулятор»: программа с меню, вычисляющая площадь круга, прямоугольника, треугольника (Герон), трапеции. Вынеси функции в отдельный модуль `geometry.py`.
2. С помощью `help(math)` или сайта docs.python.org найди 2 функции модуля `math`, которые мы не проходили, и напиши пример для каждой.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.**

```python
import math
print(math.sqrt(144), math.sqrt(225), math.sqrt(2))

from math import sqrt
print(sqrt(144), sqrt(225), sqrt(2))

import math as m
print(m.sqrt(144), m.sqrt(225), m.sqrt(2))
```

**Задание 2.**

```python
import math

big = math.pi * (30 / 2) ** 2
small_two = 2 * math.pi * (20 / 2) ** 2
print(f"Большая пицца: {big:.2f} см²")
print(f"Две маленькие пиццы: {small_two:.2f} см²")
if big > small_two:
    print("Большая пицца выгоднее!")
else:
    print("Две маленькие пиццы выгоднее!")
```

**Задание 3.**

```python
import math

print("Автобусы:", math.ceil(130 / 45))     # 3 – никто не должен остаться
print("Полные коробки:", math.floor(100 / 12))  # 8 – только полные коробки
print("Банки краски:", math.ceil(23.5 / 5))  # 5 – краски должно хватить
```

**Задание 4.**

```python
import math

a = int(input("a = "))
b = int(input("b = "))
print(f"НОД({a}, {b}) = {math.gcd(a, b)}")
print(f"НОК({a}, {b}) = {math.lcm(a, b)}")
print(f"{a}! = {math.factorial(a)}")
```

В Python 3.8 и более старых версиях `lcm` нет: `a * b // math.gcd(a, b)`.

**Задание 5.**

```python
import math

a = float(input("Первый катет: "))
b = float(input("Второй катет: "))
print("Через sqrt:", math.sqrt(a ** 2 + b ** 2))
print("Через hypot:", math.hypot(a, b))
```

**Задание 6.**

```python
import math

try:
    a = float(input("a = "))
    b = float(input("b = "))
    c = float(input("c = "))
except ValueError:
    print("Коэффициенты должны быть числами.")
else:
    if a == 0:
        print("Это не квадратное уравнение")
    else:
        d = b ** 2 - 4 * a * c
        if d < 0:
            print("Действительных корней нет")
        elif d == 0:
            print("x =", -b / (2 * a))
        else:
            x1 = (-b + math.sqrt(d)) / (2 * a)
            x2 = (-b - math.sqrt(d)) / (2 * a)
            print(f"x1 = {x1}, x2 = {x2}")
```

**Задание 7.**

```python
import math

alpha = float(input("Угол (градусы): "))
shadow = float(input("Тень (м): "))
h = shadow * math.tan(math.radians(alpha))
print(f"Высота дерева: {h:.2f} м")
```

**Задание 8.** `school_math.py`:

```python
import math

def is_prime(n):
    if n < 2:
        return False
    for d in range(2, math.isqrt(n) + 1):
        if n % d == 0:
            return False
    return True

def bmi(weight, height):
    return weight / height ** 2

def percent(part, total):
    return part / total * 100

def average(numbers):
    return sum(numbers) / len(numbers)
```

`main.py`:

```python
import school_math

print(school_math.is_prime(17), school_math.is_prime(21))
print(round(school_math.bmi(45, 1.55), 1))
print(school_math.percent(18, 25), "%")
print(school_math.average([5, 4, 5, 3]))
```

Результат:
```text
True False
18.7
72.0 %
4.25
```

**Задание 9.**

```python
import math

total = 0
n = 0
sign = 1
while abs(4 * total - math.pi) >= 0.0001:
    total += sign / (2 * n + 1)
    sign = -sign
    n += 1
print("Количество членов:", n)
print("Приближённое значение:", 4 * total)
```

Результат:
```text
Количество членов: 10000
Приближённое значение: 3.1414926535900345
```

**Задание 10.**

```python
import math
import turtle

t = turtle.Turtle()
t.speed(0)
# оси
t.penup(); t.goto(-380, 0); t.pendown(); t.goto(380, 0)
t.penup(); t.goto(0, -150); t.pendown(); t.goto(0, 150)
# график
t.pencolor("blue")
t.pensize(2)
t.penup()
for x in range(-360, 361, 5):
    y = 100 * math.sin(math.radians(x))
    t.goto(x, y)
    t.pendown()
turtle.done()
```

</details>
