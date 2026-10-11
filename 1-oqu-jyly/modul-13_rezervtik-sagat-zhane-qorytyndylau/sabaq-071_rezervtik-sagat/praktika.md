# Сабақ 71. Практикалық тапсырмалар: күрделі тақырыптарды қайталау

## Диагностикалық тест (12 мин)

Кодты **іске қоспай** нәтижесін жаз. Содан кейін көршіңмен дәптер алмасып, педагогтің жауаптары бойынша тексер. Қате жауап берген сұрақтардың тақырыбы – сенің бүгінгі «олқылығың».

| № | Код | Тақырып |
|---|---|---|
| 1 | `print(len(range(3, 15, 4)))` | Циклдер, `range` |
| 2 | `a = [4, 8, 15, 16, 23, 42]` → `print(a[2:5], a[-2])` | Кесінді |
| 3 | `s = "Python"` → `print(s[::-1][:3])` | Жолдар |
| 4 | `c = 0` → `for i in range(3):` → `for j in range(i):` → `c += 1` → `print(c)` | Кірістірілген циклдер |
| 5 | `d = {"a": [1]}` → `d["a"].append(2)` → `print(len(d), d["a"])` | Сөздік ішіндегі тізім |
| 6 | `def f(x):` → `x = x * 2` → `n = 5; f(n); print(n)` | Көріну аймағы |
| 7 | `def g(n): return 1 if n == 0 else n + g(n - 1)` → `print(g(4))` | Рекурсия |
| 8 | `a = [3, 1, 2]` → `b = a` → `b.sort()` → `print(a)` | Тізімдер, сілтеме |
| 9 | `try:` `print(int("3.5"))` `except ValueError:` `print("қате")` | `try...except` |
| 10 | `print({1, 2, 3} & {2, 3, 4}, {1, 2} | {5})` | Жиындар |

## Базалық деңгей (А карточкасы)

**А1. Трассировка.** Кестені толтыр және нәтижені болжа, содан кейін іске қосып тексер:

```python
x = 1
total = 0
while x < 20:
    total += x
    x = x * 3
print(x, total)
```

```text
| Қадам | x | total | x < 20? |
|-------|---|-------|---------|
| 0     | 1 | 0     | иә      |
| ...   |   |       |         |
```

**А2. Тіктөртбұрыш.** `w` және `h` енгізіледі. Кірістірілген цикл арқылы `#` белгілерінен тіктөртбұрыш шығар, ал ішін бос орынмен толтыр (тек жиегі).

```text
w = 5, h = 3
#####
#   #
#####
```

**А3. Кесінділер.** `months = ["қаң", "ақп", "нау", "сәу", "мам", "мау", "шіл", "там", "қыр", "қаз", "қар", "жел"]`. Кесінділер арқылы шығар: көктем айлары, жаз айлары, әр үшінші ай, кері ретпен соңғы 4 ай.

**А4. Функция қайтарады.** `count_vowels(text)` функциясы қазақ тіліндегі дауысты дыбыстар санын **қайтарады** (`аәеиоөұүыіуэюя`). Функция нәтижесін екі сөйлемге қолданып, қайсысында дауыстылар көп екенін шығар.

## Кеңейтілген деңгей (Ә карточкасы)

**Ә1. Сабақ кестесі.** Сөздік `{күн: [пәндер тізімі]}`. Функциялар жаз: `add_lesson(schedule, day, subject)` (күн жоқ болса құрып қосады), `busiest_day(schedule)` (ең көп сабақ бар күн), `find_subject(schedule, subject)` (пән бар күндердің тізімі).

**Ә2. Рекурсия.** Рекурсивті функциялар жаз: `reverse_text(s)` – жолды аударады; `count_down_even(n)` – n-нен 0-ге дейінгі жұп сандарды шығарады; `fib(n)` – n-ші Фибоначчи саны.

**Ә3. Файлдан кесте.** `marks.txt` файлында әр жолда бір оқушының бағалары бос орын арқылы жазылған (`5 4 5 3`). Файлды 2D тізімге оқы, әр оқушының және әр бағанның (тоқсанның) орташасын шығар. Бүлінген жолдарды өткізіп жібер.

## ★ Олимпиадалық / шығармашылық тапсырма (Б карточкасы)

**Б1. Ханой мұнаралары.** `hanoi(n, from_rod, to_rod, via_rod)` рекурсивті функциясы n дискіні көшіру қадамдарын шығарсын және қадамдар санын қайтарсын. n = 3 үшін 7 қадам болуы керек.

**Б2. Сиқырлы шаршы.** 3×3 кесте (2D тізім) енгізіледі. Оның «сиқырлы» екенін тексер: барлық жолдардың, бағандардың және екі диагональдың қосындылары тең.

## Жеке кеңес парағы (педагог пен оқушы бірге толтырады)

```text
Оқушы: ____________________   Күні: 19.05.2027
Өзіндік жұмыстар (№1–№6) орташа нәтижесі: ____%   Қорытынды жоба: ____%
Диагностикалық тест: ___/10   Олқылықтар: _________________________
Оқушының сұрағы: ____________________________________________________
Жазғы жеке жоспар:
  1) Тақырып: __________  Тапсырма: ______________________________
  2) Тақырып: __________  Тапсырма: ______________________________
  3) Қызығушылық бағыты (2-жыл): ойындар / GUI / деректер / веб / API
```

## Үй тапсырмасы

1. Диагностикада қате жіберген әр тақырып бойынша 1 тапсырманы (А немесе Ә карточкасынан) орында.
2. Жеке кеңес парағындағы жазғы жоспарды дәптеріңе көшір.
3. Келесі сабаққа: жыл бойы жазған бағдарламаларыңның ішінен ең ұнағанын таңдап кел (қорытынды сабақта таныстырамыз).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**Диагностикалық тест жауаптары:**

| № | Жауабы | Түсіндірме |
|---|---|---|
| 1 | `3` | 3, 7, 11 |
| 2 | `[15, 16, 23] 23` | 2, 3, 4 индекстер; `a[-2]` – соңынан екінші |
| 3 | `noh` | `"nohtyP"[:3]` |
| 4 | `3` | i=0: 0 рет, i=1: 1 рет, i=2: 2 рет |
| 5 | `1 [1, 2]` | кілт біреу, тізімге қосылды |
| 6 | `5` | `x` – жергілікті, `n` өзгермейді |
| 7 | `11` | 4 + 3 + 2 + 1 + 1 |
| 8 | `[1, 2, 3]` | `b` мен `a` – бір тізім |
| 9 | `қате` | `int("3.5")` – `ValueError` |
| 10 | `{2, 3} {1, 2, 5}` | қиылысу және бірігу |

**А1.** Трассировка: (x, total) = (1, 0) → (3, 1) → (9, 4) → (27, 13); 27 < 20 жалған. Нәтижесі: `27 13`.

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
months = ["қаң", "ақп", "нау", "сәу", "мам", "мау", "шіл", "там", "қыр", "қаз", "қар", "жел"]
print("Көктем:", months[2:5])
print("Жаз:", months[5:8])
print("Әр үшінші:", months[::3])
print("Соңғы 4 (кері):", months[:-5:-1])
```

Нәтижесі:
```text
Көктем: ['нау', 'сәу', 'мам']
Жаз: ['мау', 'шіл', 'там']
Әр үшінші: ['қаң', 'сәу', 'шіл', 'қаз']
Соңғы 4 (кері): ['жел', 'қар', 'қаз', 'қыр']
```

**А4.**

```python
def count_vowels(text):
    vowels = "аәеиоөұүыіуэюя"
    count = 0
    for ch in text.lower():
        if ch in vowels:
            count += 1
    return count


s1 = "Білім – таусылмас кен"
s2 = "Оқу – инемен құдық қазғандай"
v1 = count_vowels(s1)
v2 = count_vowels(s2)
print(v1, v2)
if v1 > v2:
    print("Бірінші сөйлемде дауыстылар көп")
elif v2 > v1:
    print("Екінші сөйлемде дауыстылар көп")
else:
    print("Тең")
```

**Ә1.**

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
add_lesson(schedule, "Дүйсенбі", "Математика")
add_lesson(schedule, "Дүйсенбі", "Python")
add_lesson(schedule, "Сәрсенбі", "Python")
add_lesson(schedule, "Сәрсенбі", "Ағылшын")
add_lesson(schedule, "Сәрсенбі", "Тарих")
print(busiest_day(schedule))
print(find_subject(schedule, "Python"))
```

Нәтижесі:
```text
Сәрсенбі
['Дүйсенбі', 'Сәрсенбі']
```

**Ә2.**

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

Нәтижесі:
```text
ытамлА
10 8 6 4 2 0 
55
```

**Ә3.**

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
    print("marks.txt табылмады")

for i in range(len(table)):
    print(f"{i + 1}-оқушы: {sum(table[i]) / len(table[i]):.2f}")
if table:
    cols = min(len(row) for row in table)
    for c in range(cols):
        column = [row[c] for row in table]
        print(f"{c + 1}-тоқсан: {sum(column) / len(column):.2f}")
```

**Б1.**

```python
def hanoi(n, from_rod, to_rod, via_rod):
    if n == 0:
        return 0
    steps = hanoi(n - 1, from_rod, via_rod, to_rod)
    print(f"Дискі {n}: {from_rod} -> {to_rod}")
    steps += 1
    steps += hanoi(n - 1, via_rod, to_rod, from_rod)
    return steps


print("Барлығы:", hanoi(3, "A", "C", "B"), "қадам")
```

**Б2.**

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
print("Сиқырлы шаршы!" if is_magic else "Сиқырлы емес")
```

</details>
