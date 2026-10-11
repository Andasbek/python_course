# Сабақ 62. Практикалық тапсырмалар: модульдер, import, math

## Базалық деңгей

**1-тапсырма. Импорттың үш тәсілі.** 144, 225 және 2 сандарының квадрат түбірін үш түрлі тәсілмен есепте: `import math`, `from math import sqrt`, `import math as m`. Нәтижелер бірдей болуы керек.

```text
12.0 15.0 1.4142135623730951
```

**2-тапсырма. Пицца.** Диаметрі 30 см пицца мен диаметрі 20 см екі пиццаның қайсысында көбірек «пицца» бар? Аудандарын есептеп, салыстыр (`math.pi`).

```text
Үлкен пицца: 706.86 см²
Екі кіші пицца: 628.32 см²
Үлкен пицца тиімдірек!
```

**3-тапсырма. Ақылды дөңгелектеу.** 
а) Мектепке 130 оқушы экскурсияға барады, бір автобусқа 45 орын бар. Неше автобус керек?
ә) Бір қорапқа 12 жұмыртқа сыяды. 100 жұмыртқадан неше **толық** қорап шығады?
б) Бөлмені бояуға 23.5 м² аудан бар, бір банка бояу 5 м²-ге жетеді. Неше банка сатып алу керек?
`ceil` немесе `floor` таңда және таңдауыңды түсініктемеде дәлелде.

**4-тапсырма. Факториал және ЕҮОБ.** Пайдаланушы екі натурал сан енгізеді. Бағдарлама олардың ЕҮОБ-ын, ЕКОЕ-ін және бірінші санның факториалын шығарады.

```text
a = 12
b = 18
ЕҮОБ(12, 18) = 6
ЕКОЕ(12, 18) = 36
12! = 479001600
```

**5-тапсырма. Пифагор.** Тікбұрышты үшбұрыштың катеттері енгізіледі, гипотенузасын екі тәсілмен тап: `math.sqrt(a**2 + b**2)` және `math.hypot(a, b)`.

## Кеңейтілген деңгей

**6-тапсырма. Квадрат теңдеу.** `ax² + bx + c = 0` теңдеуінің коэффициенттері енгізіледі. Дискриминант арқылы түбірлерді тап (`math.sqrt`). D < 0 болса – «Нақты түбірлері жоқ», a = 0 болса – «Бұл квадрат теңдеу емес». Сан емес енгізуді `try...except` арқылы өңде.

```text
a = 1
b = -5
c = 6
x1 = 3.0, x2 = 2.0
```

**7-тапсырма. Көлеңке бойынша биіктік.** Күн сәулесі жерге α градус бұрышпен түседі. Ағаштың көлеңкесінің ұзындығы L метр. Ағаштың биіктігі `h = L * tan(α)`. α мен L енгізіліп, биіктікті 2 ондық белгімен шығар.

```text
Бұрыш (градус): 45
Көлеңке (м): 8
Ағаштың биіктігі: 8.00 м
```

**8-тапсырма. Өз модулім.** `school_math.py` модулін құр, оған кемінде 4 функция жаз: `is_prime(n)` (жай сан ба?), `bmi(weight, height)` (ДСИ), `percent(part, total)`, `average(numbers)`. Басқа `main.py` файлында модульді импорттап, әр функцияны тексер.

## ★ Олимпиадалық / шығармашылық тапсырма

**9-тапсырма. π-ді аулау.** Лейбниц қатары: π/4 = 1 − 1/3 + 1/5 − 1/7 + … Цикл арқылы π-ді жуықтап есепте. `math.pi`-ден айырмасы 0.0001-ден кіші болуы үшін қатардың неше мүшесі керек?

**10-тапсырма. Turtle + math.** `turtle` және `math` модульдерін біріктіріп, `y = sin(x)` функциясының графигін сал: x-ті −360°-тан 360°-қа дейін өзгертіп, нүктелерді `goto(x, 100 * sin(radians(x)))` арқылы қос. Координата осьтерін де сал.

## Үй тапсырмасы

1. «Геометрия калькуляторы»: мәзір арқылы шеңбер, тіктөртбұрыш, үшбұрыш (Герон), трапеция ауданын есептейтін бағдарлама. Функцияларды жеке `geometry.py` модуліне шығар.
2. `help(math)` немесе docs.python.org сайтынан біз өтпеген `math` модулінің 2 функциясын тауып, әрқайсысына мысал жаз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.**

```python
import math
print(math.sqrt(144), math.sqrt(225), math.sqrt(2))

from math import sqrt
print(sqrt(144), sqrt(225), sqrt(2))

import math as m
print(m.sqrt(144), m.sqrt(225), m.sqrt(2))
```

**2-тапсырма.**

```python
import math

big = math.pi * (30 / 2) ** 2
small_two = 2 * math.pi * (20 / 2) ** 2
print(f"Үлкен пицца: {big:.2f} см²")
print(f"Екі кіші пицца: {small_two:.2f} см²")
if big > small_two:
    print("Үлкен пицца тиімдірек!")
else:
    print("Екі кіші пицца тиімдірек!")
```

**3-тапсырма.**

```python
import math

print("Автобустар:", math.ceil(130 / 45))     # 3 – ешкім қалып қоймауы керек
print("Толық қораптар:", math.floor(100 / 12))  # 8 – тек толық қораптар
print("Бояу банкалары:", math.ceil(23.5 / 5))  # 5 – бояу жетуі керек
```

**4-тапсырма.**

```python
import math

a = int(input("a = "))
b = int(input("b = "))
print(f"ЕҮОБ({a}, {b}) = {math.gcd(a, b)}")
print(f"ЕКОЕ({a}, {b}) = {math.lcm(a, b)}")
print(f"{a}! = {math.factorial(a)}")
```

Python 3.8 және одан ескі нұсқаларда `lcm` жоқ: `a * b // math.gcd(a, b)`.

**5-тапсырма.**

```python
import math

a = float(input("Бірінші катет: "))
b = float(input("Екінші катет: "))
print("sqrt арқылы:", math.sqrt(a ** 2 + b ** 2))
print("hypot арқылы:", math.hypot(a, b))
```

**6-тапсырма.**

```python
import math

try:
    a = float(input("a = "))
    b = float(input("b = "))
    c = float(input("c = "))
except ValueError:
    print("Коэффициенттер сан болуы керек.")
else:
    if a == 0:
        print("Бұл квадрат теңдеу емес")
    else:
        d = b ** 2 - 4 * a * c
        if d < 0:
            print("Нақты түбірлері жоқ")
        elif d == 0:
            print("x =", -b / (2 * a))
        else:
            x1 = (-b + math.sqrt(d)) / (2 * a)
            x2 = (-b - math.sqrt(d)) / (2 * a)
            print(f"x1 = {x1}, x2 = {x2}")
```

**7-тапсырма.**

```python
import math

alpha = float(input("Бұрыш (градус): "))
shadow = float(input("Көлеңке (м): "))
h = shadow * math.tan(math.radians(alpha))
print(f"Ағаштың биіктігі: {h:.2f} м")
```

**8-тапсырма.** `school_math.py`:

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

Нәтижесі:
```text
True False
18.7
72.0 %
4.25
```

**9-тапсырма.**

```python
import math

total = 0
n = 0
sign = 1
while abs(4 * total - math.pi) >= 0.0001:
    total += sign / (2 * n + 1)
    sign = -sign
    n += 1
print("Мүшелер саны:", n)
print("Жуық мән:", 4 * total)
```

Нәтижесі:
```text
Мүшелер саны: 10000
Жуық мән: 3.1414926535900345
```

**10-тапсырма.**

```python
import math
import turtle

t = turtle.Turtle()
t.speed(0)
# осьтер
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
