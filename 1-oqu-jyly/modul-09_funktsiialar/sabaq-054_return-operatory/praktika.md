# Сабақ 54. Практикалық тапсырмалар: return операторы

Әр тапсырмада функция нәтижені **қайтаруы** (`return`) керек, ал экранға шығару — негізгі бағдарламада.

## Базалық деңгей

### 1-тапсырма. Шеңбер
`circle_area(r)` және `circle_length(r)` функциялары шеңбердің ауданы мен ұзындығын қайтарсын (π = 3.14159). Радиусы 5 болатын шеңбер үшін екеуін де 2 ондық белгімен шығарыңыз.

```text
Аудан: 78.54
Ұзындық: 31.42
```

### 2-тапсырма. Максимум
`max_of_three(a, b, c)` функциясы үш санның ең үлкенін қайтарсын (`max()` қолданбай, `if` арқылы). Оны пайдаланып, төрт санның ең үлкенін табыңыз: `max_of_three(max_of_three(a, b, c), d, d)`.

### 3-тапсырма. Жұп па?
`is_even(n)` функциясын жазыңыз. Пайдаланушы енгізген 5 санның ішінен жұп сандардың санын осы функция арқылы табыңыз.

### 4-тапсырма. ДСИ (дене салмағының индексі)
`bmi(weight, height)` функциясы ДСИ = салмақ / бой² мәнін қайтарсын (бой метрмен). `bmi_category(value)` функциясы санатын қайтарсын: 18.5-тен төмен — «салмақ аз», 18.5–24.9 — «қалыпты», 25 және жоғары — «салмақ артық».

Ескерту: балаларға арналған нақты нормалар жасқа байланысты, бұл тек бағдарламалау жаттығуы.

### 5-тапсырма. Цифрлар қосындысы
`digit_sum(n)` функциясы бүтін санның цифрларының қосындысын қайтарсын. `digit_sum(2027)` → `11`.

## Кеңейтілген деңгей

### 6-тапсырма. Жай сандар
`is_prime(n)` функциясын жазыңыз. Оны пайдаланып:
- 1-ден 100-ге дейінгі жай сандар санын;
- пайдаланушы енгізген саннан кейінгі ең жақын жай санды табыңыз.

### 7-тапсырма. Уақыт
`to_seconds(h, m, s)` функциясы уақытты секундқа айналдырсын, `from_seconds(total)` кері түрлендіріп `(h, m, s)` кортежін қайтарсын. Екі сабақ аралығын (мысалы, `09:15:00` және `10:45:30`) есептеңіз.

### 8-тапсырма. Бағаны әріппен
`letter_grade(score)` функциясы 100 ұпайлық бағаны әріпке айналдырсын: A (90+), B (75–89), C (60–74), D (50–59), F (50-ден төмен). `average(scores)` функциясы тізімнің орташасын қайтарсын. Бағдарлама сыныптың орташа ұпайы мен әріптік бағасын шығарсын.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. ЕҮОБ және ЕКОЕ
`gcd(a, b)` функциясы Евклид алгоритмімен ең үлкен ортақ бөлгішті, `lcm(a, b)` функциясы `gcd` арқылы ең кіші ортақ еселікті қайтарсын. Бөлшекті қысқартатын `reduce_fraction(num, den)` функциясын жазыңыз: `reduce_fraction(12, 18)` → `(2, 3)`.

Евклид алгоритмі: `b ≠ 0` болғанша `a, b = b, a % b`; соңында `a` — ЕҮОБ.

### 10-тапсырма. Бақытты билет
Автобус билетінің нөмірі 6 цифрдан тұрады. Алғашқы үш цифрдың қосындысы соңғы үшеуінің қосындысына тең болса — билет «бақытты». `is_lucky(ticket)` функциясын жазып, `000000`-ден `999999`-ға дейін неше бақытты билет бар екенін есептеңіз.

## Үй тапсырмасы

1. `km_to_miles(km)` және `miles_to_km(miles)` функцияларын жазып, Алматы–Астана (≈ 1215 км) арақашықтығын мильге айналдырыңыз.
2. `count_vowels(text)` функциясы мәтіндегі дауысты дыбыстар санын қайтарсын. Оны 3 түрлі мақалға қолданыңыз.
3. 45-сабақтағы кез келген 2 тапсырманы `return` қолданатын функцияға айналдырыңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
PI = 3.14159


def circle_area(r):
    return PI * r ** 2


def circle_length(r):
    return 2 * PI * r


print(f"Аудан: {circle_area(5):.2f}")
print(f"Ұзындық: {circle_length(5):.2f}")
```

**2-тапсырма**
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

**3-тапсырма**
```python
def is_even(n):
    return n % 2 == 0


count = 0
for i in range(5):
    if is_even(int(input("Сан: "))):
        count += 1
print("Жұп сандар:", count)
```

**4-тапсырма**
```python
def bmi(weight, height):
    return weight / height ** 2


def bmi_category(value):
    if value < 18.5:
        return "салмақ аз"
    elif value < 25:
        return "қалыпты"
    return "салмақ артық"


b = bmi(50, 1.6)
print(f"ДСИ: {b:.1f} — {bmi_category(b)}")
```

**5-тапсырма**
```python
def digit_sum(n):
    total = 0
    for ch in str(abs(n)):
        total += int(ch)
    return total


print(digit_sum(2027))
```

**6-тапсырма**
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
print("1–100 аралығындағы жай сандар:", count)

n = int(input("Сан: ")) + 1
while not is_prime(n):
    n += 1
print("Келесі жай сан:", n)
```
1–100 аралығында 25 жай сан бар.

**7-тапсырма**
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
print(f"Айырма: {h} сағ {m} мин {s} сек")
```
Нәтижесі:
```text
Айырма: 1 сағ 30 мин 30 сек
```

**8-тапсырма**
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
print(f"Орташа: {avg:.1f}, баға: {letter_grade(avg)}")
```
Нәтижесі:
```text
Орташа: 76.2, баға: B
```

**9-тапсырма**
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
Нәтижесі:
```text
6 36
(2, 3)
```

**10-тапсырма**
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
Нәтижесі:
```text
55252
```
(Есептеу бірнеше секунд алуы мүмкін.)
</details>
