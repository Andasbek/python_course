# Сабақ 70. Практикалық тапсырмалар: өтпей қалған тақырыптарды өтеу (модульдер бойынша жиынтық)

> Педагог өтпей қалған тақырыптарға сәйкес бөлімдерді таңдайды. Барлық тақырып өтілген болса – әр оқушы өзінің әлсіз модулінен 3–4 тапсырма таңдайды.

## Жылдам диагностика (сабақ басында, 7 мин)

Әр сұраққа бір жауап таңда:
1. `print(7 // 2, 7 % 2)` нәтижесі: а) `3.5 1` ә) `3 1` б) `3 0`
2. `range(2, 8, 2)` қандай сандарды береді? а) 2,4,6,8 ә) 2,4,6 б) 2,3,...,7
3. `"Алматы"[-1]` нәтижесі: а) `А` ә) `ы` б) қате
4. `d = {"a": 1}; d.get("b", 0)` нәтижесі: а) `None` ә) `0` б) `KeyError`
5. Функцияда `return` жоқ болса, ол не қайтарады? а) `0` ә) `None` б) қате

## Базалық деңгей

### І–ІІ модульдер: айнымалылар мен типтер

**1.** Пайдаланушы секундтар санын енгізеді. Оны сағат, минут, секундқа айналдыр.

```text
Секунд: 3725
1 сағ 2 мин 5 с
```

**2.** «Дүкен чегі»: тауар атауы, бағасы, саны енгізіледі. Чекті f-жолдармен әдемі шығар, 12% ҚҚС-ны бөлек көрсет (баға ҚҚС-ты қамтиды деп есепте: ҚҚС = сома × 12 / 112).

### ІІІ модуль: turtle

**3.** `turtle` арқылы үйдің суретін сал: шаршы қабырға (бояумен), үшбұрыш шатыр, терезе.

### IV модуль: тармақталу

**4.** Жыл енгізіледі. Кібісе жыл ма? (4-ке бөлінсе және 100-ге бөлінбесе, немесе 400-ге бөлінсе).

```text
Жыл: 2028
2028 – кібісе жыл
```

### V модуль: циклдер

**5.** 1-ден 100-ге дейінгі сандардың ішінен 7-ге бөлінетіндерін немесе құрамында 7 цифры барларын шығар («Жеті» ойыны).

**6.** Пайдаланушы 0 енгізгенше сандар енгізеді. Оң сандардың қосындысын және теріс сандардың санын шығар.

### VI–VII модульдер: тізімдер мен жолдар

**7.** Тізім: `temps = [-5, 2, 7, -1, 0, 12, 4]`. Оң температуралар тізімін (тізім генераторымен), ең жылы және ең суық күннің нөмірін шығар.

**8.** Сөйлем енгізіледі. Ең ұзын сөзді, сөздер санын және әр сөздің бас әрпінен құралған қысқартуды (аббревиатура) шығар.

```text
Сөйлем: оқушылардың инновациялық шығармашылық орталығы
Ең ұзын сөз: шығармашылық
Сөздер саны: 4
Аббревиатура: ОИШО
```

### VIII модуль: сөздіктер мен жиындар

**9.** Мәтіндегі әр сөздің неше рет кездесетінін сөздік арқылы сана және ең жиі кездесетін сөзді шығар.

**10.** Екі сыныптың үйірмелер тізімі жиын ретінде берілген. Екі сыныпта да бар үйірмелерді, тек бірінші сыныпта бар үйірмелерді және барлық үйірмелерді шығар.

### IX модуль: функциялар

**11.** `is_palindrome(word)` функциясы: сөз палиндром болса `True` қайтарады (регистр мен бос орындарды елемейді). Тексер: «Қазақ», «Python», «А роза упала на лапу Азора».

**12.** `power(base, exp=2)` функциясы: дәрежені цикл арқылы есептейді (`**` қолданбай). Рекурсивті нұсқасын да жаз.

### Х–ХІ модульдер: қателер, файлдар, модульдер

**13.** `numbers.txt` файлына 1-ден 20-ға дейінгі кездейсоқ 10 сан жаз (`random`). Содан кейін файлды оқып, сандардың қосындысын және ең үлкенін шығар. Файл жоқ болса – хабарлама.

**14.** Пайдаланушы туған жылын енгізеді. `datetime` арқылы ағымдағы жылды алып, жасын есепте. Сан емес енгізу – `try...except`.

## Кеңейтілген деңгей

**15.** «Бағалар журналы» (VI, VIII, IX, X модульдер): `{оқушы: [бағалар]}` сөздігін құр, функциялар жаз – орташаны есептеу, үздікті табу, журналды файлға жазу.

**16.** «Мәтін статистикасы» (VII, VIII, X): файлдағы мәтіннің әріптерін, сөздерін, сөйлемдерін (`.`, `!`, `?` бойынша) сана; ең жиі 3 әріпті шығар.

## ★ Олимпиадалық / шығармашылық тапсырма

**17.** «Сандық спираль»: n енгізіледі, n×n кестені 1-ден n²-ге дейінгі сандармен спираль бойынша толтырып шығар (кірістірілген тізім және циклдер).

```text
n = 4
 1  2  3  4
12 13 14  5
11 16 15  6
10  9  8  7
```

## Үй тапсырмасы

1. Өзің нашар меңгерген модульдің шпаргалкасын (теория файлы) қайта оқып, сол модульден 2 тапсырманы орында.
2. Келесі сабаққа жыл бойы ең түсініксіз болған тақырып бойынша 2–3 сұрақ дайындап кел – жеке кеңесте талқылаймыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**Диагностика:** 1 – ә; 2 – ә; 3 – ә; 4 – ә; 5 – ә.

**1.**

```python
seconds = int(input("Секунд: "))
hours = seconds // 3600
minutes = seconds % 3600 // 60
secs = seconds % 60
print(f"{hours} сағ {minutes} мин {secs} с")
```

**2.**

```python
name = input("Тауар: ")
price = float(input("Бағасы: "))
count = int(input("Саны: "))
total = price * count
vat = total * 12 / 112
print("=" * 30)
print(f"{name:<15}{count:>3} x {price:.2f}")
print(f"{'Барлығы:':<15}{total:>12.2f}")
print(f"{'оның ішінде ҚҚС:':<15}{vat:>12.2f}")
print("=" * 30)
```

**3.**

```python
import turtle

t = turtle.Turtle()
t.fillcolor("wheat")
t.begin_fill()
for _ in range(4):
    t.forward(150)
    t.left(90)
t.end_fill()
# шатыр
t.penup(); t.goto(0, 150); t.pendown()
t.fillcolor("brown")
t.begin_fill()
t.goto(75, 230)
t.goto(150, 150)
t.goto(0, 150)
t.end_fill()
# терезе
t.penup(); t.goto(50, 60); t.pendown()
t.fillcolor("lightblue")
t.begin_fill()
for _ in range(4):
    t.forward(50)
    t.left(90)
t.end_fill()
turtle.done()
```

**4.**

```python
year = int(input("Жыл: "))
if (year % 4 == 0 and year % 100 != 0) or year % 400 == 0:
    print(year, "– кібісе жыл")
else:
    print(year, "– кібісе жыл емес")
```

**5.**

```python
for n in range(1, 101):
    if n % 7 == 0 or "7" in str(n):
        print(n, end=" ")
print()
```

**6.**

```python
positive_sum = 0
negative_count = 0
while True:
    n = int(input("Сан (0 – тоқтату): "))
    if n == 0:
        break
    if n > 0:
        positive_sum += n
    else:
        negative_count += 1
print("Оң сандар қосындысы:", positive_sum)
print("Теріс сандар саны:", negative_count)
```

**7.**

```python
temps = [-5, 2, 7, -1, 0, 12, 4]
print("Оң:", [t for t in temps if t > 0])
print("Ең жылы күн:", temps.index(max(temps)) + 1)
print("Ең суық күн:", temps.index(min(temps)) + 1)
```

**8.**

```python
sentence = input("Сөйлем: ")
words = sentence.split()
longest = ""
for w in words:
    if len(w) > len(longest):
        longest = w
abbr = "".join([w[0].upper() for w in words])
print("Ең ұзын сөз:", longest)
print("Сөздер саны:", len(words))
print("Аббревиатура:", abbr)
```

**9.**

```python
text = "біз python үйренеміз python қызық біз бағдарламашымыз"
counts = {}
for word in text.lower().split():
    counts[word] = counts.get(word, 0) + 1
best = ""
for word, cnt in counts.items():
    if best == "" or cnt > counts[best]:
        best = word
print(counts)
print("Ең жиі:", best, counts[best])
```

**10.**

```python
class_a = {"Python", "шахмат", "робототехника", "домбыра"}
class_b = {"Python", "футбол", "домбыра", "сурет"}
print("Екеуінде де:", class_a & class_b)
print("Тек 1-сыныпта:", class_a - class_b)
print("Барлығы:", class_a | class_b)
```

**11.**

```python
def is_palindrome(word):
    clean = word.lower().replace(" ", "")
    return clean == clean[::-1]


print(is_palindrome("Қазақ"), is_palindrome("Python"),
      is_palindrome("А роза упала на лапу Азора"))
```

Нәтижесі:
```text
True False True
```

**12.**

```python
def power(base, exp=2):
    result = 1
    for _ in range(exp):
        result *= base
    return result


def power_rec(base, exp=2):
    if exp == 0:
        return 1
    return base * power_rec(base, exp - 1)


print(power(3), power(2, 10), power_rec(2, 10))
```

**13.**

```python
import random

with open("numbers.txt", "w", encoding="utf-8") as f:
    for _ in range(10):
        f.write(str(random.randint(1, 20)) + "\n")

try:
    with open("numbers.txt", encoding="utf-8") as f:
        numbers = [int(line) for line in f if line.strip()]
    print("Сандар:", numbers)
    print("Қосынды:", sum(numbers), "Ең үлкені:", max(numbers))
except FileNotFoundError:
    print("Файл табылмады")
```

**14.**

```python
from datetime import date

try:
    year = int(input("Туған жылың: "))
    print("Жасың (биылғы туған күніңнен кейін):", date.today().year - year)
except ValueError:
    print("Жылды цифрмен жаз!")
```

**15.**

```python
journal = {"Айгерім": [5, 5, 4], "Нұрлан": [4, 3, 4], "Дана": [5, 4, 5]}


def average(grades):
    return sum(grades) / len(grades)


def best_student(journal):
    best = ""
    for name in journal:
        if best == "" or average(journal[name]) > average(journal[best]):
            best = name
    return best


def save_journal(journal, file_name):
    with open(file_name, "w", encoding="utf-8") as f:
        for name, grades in journal.items():
            f.write(f"{name};{' '.join(map(str, grades))};{average(grades):.2f}\n")


print("Үздік:", best_student(journal))
save_journal(journal, "journal.txt")
```

**16.**

```python
try:
    with open("text.txt", encoding="utf-8") as f:
        text = f.read()
except FileNotFoundError:
    text = "Python – керемет тіл! Біз оны үйренеміз. Сен де үйрен?"

letters = [ch for ch in text.lower() if ch.isalpha()]
words = text.split()
sentences = 0
for ch in text:
    if ch in ".!?":
        sentences += 1
counts = {}
for ch in letters:
    counts[ch] = counts.get(ch, 0) + 1
top = sorted(counts.items(), key=lambda pair: pair[1], reverse=True)[:3]
print("Әріптер:", len(letters), "Сөздер:", len(words), "Сөйлемдер:", sentences)
print("Ең жиі 3 әріп:", top)
```

(`lambda` – күшті оқушыларға арналған қосымша түсіндірме: «бір жолдық атаусыз функция»; оның орнына `def get_count(pair): return pair[1]` жазуға болады.)

**17.**

```python
n = int(input("n = "))
grid = [[0] * n for _ in range(n)]
top, bottom, left, right = 0, n - 1, 0, n - 1
num = 1
while num <= n * n:
    for c in range(left, right + 1):
        grid[top][c] = num
        num += 1
    top += 1
    for r in range(top, bottom + 1):
        grid[r][right] = num
        num += 1
    right -= 1
    for c in range(right, left - 1, -1):
        grid[bottom][c] = num
        num += 1
    bottom -= 1
    for r in range(bottom, top - 1, -1):
        grid[r][left] = num
        num += 1
    left += 1
for row in grid:
    print(" ".join(f"{x:2}" for x in row))
```

</details>
