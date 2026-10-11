# Сабақ 63. Практикалық тапсырмалар: random модулі

## Базалық деңгей

**1-тапсырма. Екі кубик.** Екі кубикті лақтырып, әрқайсысының мәнін және қосындысын шығар. Екеуі бірдей түссе – «Дубль!» деп жаз.

```text
1-кубик: 4
2-кубик: 4
Қосынды: 8
Дубль!
```

**2-тапсырма. Кезекші таңдау.** Сыныптағы 8 оқушының тізімін құр. Бағдарлама:
а) бүгінгі бір кезекшіні (`choice`);
ә) аптаның 5 күніне әртүрлі 5 кезекшіні (`sample`) таңдап, «Дүйсенбі – Дана» түрінде шығарсын.

**3-тапсырма. Ауа райы генераторы.** Аптаның 7 күніне кездейсоқ температура (`uniform(-10, 10)`, бір ондық белгімен) және кездейсоқ ауа райы түрін (`["Күн ашық", "Бұлтты", "Жаңбыр", "Қар"]`) шығар. Соңында апталық орташа температураны есепте.

**4-тапсырма. Тиын тәжірибесі.** Тиынды 100 рет лақтыр. «Елтаңба» мен «Сан» неше рет түскенін және пайызын шығар. 1000 және 10000 рет лақтырып көр – пайыздар 50%-ға жақындай ма?

**5-тапсырма. Кездейсоқ мысал.** Бағдарлама 10-нан 99-ға дейінгі екі кездейсоқ санды және кездейсоқ амалды (`+` немесе `-`) таңдап, оқушыдан жауап сұрайды. Жауап дұрыс па, жоқ па – хабарла.

## Кеңейтілген деңгей

**6-тапсырма. «Тас, қайшы, қағаз».** Компьютермен 5 раундтық ойын. Ойыншы `тас`, `қайшы` немесе `қағаз` енгізеді, компьютер кездейсоқ таңдайды. Әр раундтың жеңімпазын және соңғы есепті шығар. Қате енгізуді (мысалы, «таз») өңде.

```text
1-раунд. Таңдауың: тас
Компьютер: қайшы
Сен ұттың!
...
Қорытынды есеп: Сен 3 – 1 Компьютер (1 тең)
```

**7-тапсырма. Құпиясөз генераторы.** Пайдаланушы құпиясөз ұзындығын (8–32) енгізеді. Құпиясөзде кемінде бір кіші әріп, бір бас әріп, бір цифр және бір арнайы таңба **міндетті түрде** болуы керек. Кеңес: алдымен әр топтан бір таңба ал, қалғанын жалпы жиыннан толтыр, соңында `shuffle` арқылы араластыр.

**8-тапсырма. Викторина.** Сөздік: `{сұрақ: жауап}` (кемінде 8 сұрақ, мысалы Қазақстан туралы). Бағдарлама кездейсоқ 5 сұрақты (қайталанбайтын) таңдап, оқушыдан сұрайды және ұпай есептейді.

## ★ Олимпиадалық / шығармашылық тапсырма

**9-тапсырма. π-ді кездейсоқтықпен табу (Монте-Карло).** Қабырғасы 1-ге тең квадратқа кездейсоқ N нүкте лақтыр (`x = random.random()`, `y = random.random()`). Шеңбер ширегінің ішіне түскен нүктелер (`x² + y² ≤ 1`) саны M болса, π ≈ 4·M/N. N = 1000, 100000, 1000000 үшін нәтижені `math.pi`-мен салыстыр.

**10-тапсырма. Turtle – кездейсоқ жұлдызды аспан.** `turtle` арқылы қара фонға кездейсоқ орындарда, кездейсоқ өлшемде және кездейсоқ түсте 50 «жұлдыз» (нүкте немесе кішкентай жұлдыз фигурасы) сал. `t.dot(size, color)` әдісін қолдануға болады.

## Үй тапсырмасы

1. «Болжам беретін сиқырлы шар»: пайдаланушы «стоп» жазғанша сұрақ қоя береді, бағдарлама 10 жауаптың бірін кездейсоқ береді. Бос сұраққа «Сұрақ қой!» деп жауап берсін.
2. 6-тапсырмадағы ойынға рекордты (ең көп жеңіс) файлда сақтау мүмкіндігін қос (60-сабақтағы тәсілмен).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.**

```python
import random

d1 = random.randint(1, 6)
d2 = random.randint(1, 6)
print("1-кубик:", d1)
print("2-кубик:", d2)
print("Қосынды:", d1 + d2)
if d1 == d2:
    print("Дубль!")
```

**2-тапсырма.**

```python
import random

students = ["Айгерім", "Нұрлан", "Дана", "Ерасыл", "Мадина", "Арман", "Әлия", "Санжар"]
print("Бүгінгі кезекші:", random.choice(students))
days = ["Дүйсенбі", "Сейсенбі", "Сәрсенбі", "Бейсенбі", "Жұма"]
duty = random.sample(students, 5)
for i in range(5):
    print(days[i], "–", duty[i])
```

**3-тапсырма.**

```python
import random

days = ["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"]
kinds = ["Күн ашық", "Бұлтты", "Жаңбыр", "Қар"]
temps = []
for day in days:
    t = round(random.uniform(-10, 10), 1)
    temps.append(t)
    print(f"{day}: {t:>5} °C, {random.choice(kinds)}")
print(f"Орташа: {sum(temps) / len(temps):.1f} °C")
```

**4-тапсырма.**

```python
import random

for n in [100, 1000, 10000]:
    heads = 0
    for _ in range(n):
        if random.randint(0, 1) == 0:
            heads += 1
    tails = n - heads
    print(f"N={n}: Елтаңба {heads} ({heads / n * 100:.1f}%), Сан {tails} ({tails / n * 100:.1f}%)")
```

**5-тапсырма.**

```python
import random

a = random.randint(10, 99)
b = random.randint(10, 99)
op = random.choice(["+", "-"])
correct = a + b if op == "+" else a - b
try:
    answer = int(input(f"{a} {op} {b} = "))
    if answer == correct:
        print("Дұрыс!")
    else:
        print("Қате. Дұрыс жауап:", correct)
except ValueError:
    print("Жауап сан болуы керек. Дұрыс жауап:", correct)
```

**6-тапсырма.**

```python
import random

options = ["тас", "қайшы", "қағаз"]
beats = {"тас": "қайшы", "қайшы": "қағаз", "қағаз": "тас"}
player_score = 0
computer_score = 0
draws = 0
round_no = 1
while round_no <= 5:
    player = input(f"{round_no}-раунд. Таңдауың: ").strip().lower()
    if player not in options:
        print("Тек: тас, қайшы немесе қағаз!")
        continue
    computer = random.choice(options)
    print("Компьютер:", computer)
    if player == computer:
        print("Тең!")
        draws += 1
    elif beats[player] == computer:
        print("Сен ұттың!")
        player_score += 1
    else:
        print("Компьютер ұтты!")
        computer_score += 1
    round_no += 1
print(f"Қорытынды есеп: Сен {player_score} – {computer_score} Компьютер ({draws} тең)")
```

**7-тапсырма.**

```python
import random

lower = "abcdefghijklmnopqrstuvwxyz"
upper = lower.upper()
digits = "0123456789"
symbols = "!@#$%&*?"

while True:
    try:
        length = int(input("Ұзындығы (8-32): "))
        if 8 <= length <= 32:
            break
    except ValueError:
        pass
    print("8 бен 32 аралығындағы сан енгіз.")

chars = [random.choice(lower), random.choice(upper),
         random.choice(digits), random.choice(symbols)]
all_chars = lower + upper + digits + symbols
for _ in range(length - 4):
    chars.append(random.choice(all_chars))
random.shuffle(chars)
print("Құпиясөз:", "".join(chars))
```

**8-тапсырма.**

```python
import random

quiz = {
    "Қазақстанның астанасы?": "астана",
    "Ең биік шың – Хан ... ?": "тәңірі",
    "Қазақстан тәуелсіздігін қай жылы алды?": "1991",
    "Ең үлкен көл (Қазақстан ішінде)?": "балқаш",
    "Алматыдағы әйгілі мұз айдыны?": "медеу",
    "Қазақстанның ұлттық валютасы?": "теңге",
    "«Абай жолы» романының авторы?": "мұхтар әуезов",
    "Байқоңыр – бұл ...?": "ғарыш айлағы",
}
questions = random.sample(list(quiz.keys()), 5)
score = 0
for q in questions:
    answer = input(q + " ").strip().lower()
    if answer == quiz[q]:
        print("Дұрыс!")
        score += 1
    else:
        print("Қате. Жауап:", quiz[q])
print(f"Ұпай: {score}/5")
```

**9-тапсырма.**

```python
import math
import random

for n in [1000, 100000, 1000000]:
    inside = 0
    for _ in range(n):
        x = random.random()
        y = random.random()
        if x * x + y * y <= 1:
            inside += 1
    estimate = 4 * inside / n
    print(f"N={n}: π ≈ {estimate:.5f}, қате = {abs(estimate - math.pi):.5f}")
```

Ескерту: N = 1 000 000 болғанда цикл бірнеше секунд жұмыс істейді – бұл қалыпты.

**10-тапсырма.**

```python
import random
import turtle

screen = turtle.Screen()
screen.bgcolor("black")
t = turtle.Turtle()
t.hideturtle()
t.speed(0)
t.penup()
colors = ["white", "yellow", "lightblue", "pink", "gold"]
for _ in range(50):
    t.goto(random.randint(-300, 300), random.randint(-250, 250))
    t.dot(random.randint(2, 8), random.choice(colors))
turtle.done()
```

</details>
