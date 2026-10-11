# Сабақ 16. Практикалық тапсырмалар: қалам параметрлері және бояу

> Цикл әлі өтілмеген — қайталанатын командаларды жеке жолдармен жазыңыз. Бояу үшін фигура тұйық болуы керек.

## Базалық деңгей

### 1-тапсырма. «Кемпірқосақ сызығы»
Қалыңдығы 10 болатын, әр 60 пиксельде түсі ауысатын түзу сызық салыңыз: қызыл, қызғылт сары, сары, жасыл, көк, күлгін.

### 2-тапсырма. «Үзік сызық»
Жол ортасындағы белгі сияқты ақ фонда емес, сұр фонда (`bgcolor("gray")`) ақ түсті үзік сызық салыңыз: 5 сызық (әрқайсысы 40), арасы 20 пиксель.

### 3-тапсырма. «Екі шаршы»
Бір-бірінен бөлек, жанында тұрған екі шаршы салыңыз: біріншісі қызыл (қабырғасы 80), екіншісі көк (қабырғасы 80). Олардың арасы — 40 пиксель, жалғастырушы сызық болмасын.

### 4-тапсырма. «Боялған шаршы»
Жиегі қара, қалыңдығы 3, іші сары болатын шаршы (қабырғасы 120) салыңыз. Тасбақаны соңында жасырыңыз.

### 5-тапсырма. «Бағдаршам»
Қара тіктөртбұрыштың (60 × 180) ішінде үш түрлі түсті шеңбер (қызыл, сары, жасыл) салыңыз. Кеңес: `circle(25)` және `penup`/`pendown`.

## Кеңейтілген деңгей

### 6-тапсырма. «Түсті таңда»
Пайдаланушыдан бояу түсін ағылшынша сұраңыз (мысалы, `green`) және сол түспен боялған үшбұрыш салыңыз.

Мысал енгізу:
```text
Түсті енгізіңіз (ағылшынша): purple
```

### 7-тапсырма. «Қазақстан туы» (жеңілдетілген)
Көгілдір (`"#00AFCA"`) тіктөртбұрыш (300 × 150) салып, оның ортасына алтын-сары (`"#FEC50C"`) шеңбер (күн) салыңыз.

### 8-тапсырма. «Нысана»
Ортақ центрі бар үш шеңбер-нысана салыңыз: үлкені қызыл (радиусы 90), ортасы ақ (60), кішісі қызыл (30). Кеңес: үлкен шеңберден бастаңыз, келесі шеңбер үшін тасбақаны жоғары 30 пиксель көтеріңіз (қалам көтеріліп тұрғанда).

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. «Қардан адам»
Үш ақ шеңберден (төменнен жоғары қарай кішірейеді) қардан адам салыңыз, фонды көк етіңіз, көзін `dot()` арқылы, мұрнын қызғылт сары үшбұрышпен салыңыз.

### 10-тапсырма. «Шахмат тақтасының бөлігі»
2 × 2 шахмат тақтасы: екі қара және екі ақ шаршы (қабырғасы 60), барлығы қара жиекпен.

## Үй тапсырмасы

1. Өз фамилияңыздың бірінші әрпін түсті, қалыңдығы 8 қаламмен салыңыз.
2. «Үй» суретіне түс беріңіз: қабырғасы — қоңыр, шатыры — қызыл, терезесі — сары.
3. Ойланыңыз: `begin_fill()` командасын фигураны салып болғаннан кейін жазсақ не болады? Тексеріп, нәтижесін жазыңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.**

```python
import turtle

t = turtle.Turtle()
t.pensize(10)
t.penup()
t.back(180)
t.pendown()
t.pencolor("red")
t.forward(60)
t.pencolor("orange")
t.forward(60)
t.pencolor("yellow")
t.forward(60)
t.pencolor("green")
t.forward(60)
t.pencolor("blue")
t.forward(60)
t.pencolor("purple")
t.forward(60)
turtle.done()
```

**2-тапсырма.**

```python
import turtle

turtle.bgcolor("gray")
t = turtle.Turtle()
t.pencolor("white")
t.pensize(5)
t.forward(40)
t.penup()
t.forward(20)
t.pendown()
t.forward(40)
t.penup()
t.forward(20)
t.pendown()
t.forward(40)
t.penup()
t.forward(20)
t.pendown()
t.forward(40)
t.penup()
t.forward(20)
t.pendown()
t.forward(40)
turtle.done()
```

**3-тапсырма.**

```python
import turtle

t = turtle.Turtle()
t.pencolor("red")
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.penup()
t.forward(120)    # 80 қабырға + 40 аралық
t.pendown()
t.pencolor("blue")
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
turtle.done()
```

**4-тапсырма.**

```python
import turtle

t = turtle.Turtle()
t.pensize(3)
t.color("black", "yellow")
t.begin_fill()
t.forward(120)
t.left(90)
t.forward(120)
t.left(90)
t.forward(120)
t.left(90)
t.forward(120)
t.left(90)
t.end_fill()
t.hideturtle()
turtle.done()
```

**5-тапсырма.** Тіктөртбұрыштың сол-төменгі бұрышы (0; 0). Шеңбер `circle(25)` тасбақадан жоғары салынады, сондықтан тасбақаны шеңбердің төменгі нүктесіне қоямыз.

```python
import turtle

t = turtle.Turtle()
t.speed(0)
# корпус
t.color("black", "black")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(180)
t.left(90)
t.forward(60)
t.left(90)
t.forward(180)
t.left(90)
t.end_fill()
# жасыл (төменгі) шам: центрі (30; 30)
t.penup()
t.forward(30)
t.left(90)
t.forward(5)
t.right(90)
t.pendown()
t.color("green", "green")
t.begin_fill()
t.circle(25)
t.end_fill()
# сары шам: 60 жоғары
t.penup()
t.left(90)
t.forward(60)
t.right(90)
t.pendown()
t.color("yellow", "yellow")
t.begin_fill()
t.circle(25)
t.end_fill()
# қызыл шам
t.penup()
t.left(90)
t.forward(60)
t.right(90)
t.pendown()
t.color("red", "red")
t.begin_fill()
t.circle(25)
t.end_fill()
t.hideturtle()
turtle.done()
```

**6-тапсырма.**

```python
import turtle

tus = input("Түсті енгізіңіз (ағылшынша): ")
t = turtle.Turtle()
t.fillcolor(tus)
t.begin_fill()
t.forward(150)
t.left(120)
t.forward(150)
t.left(120)
t.forward(150)
t.left(120)
t.end_fill()
turtle.done()
```

**7-тапсырма.**

```python
import turtle

t = turtle.Turtle()
t.speed(0)
t.penup()
t.back(150)
t.right(90)
t.forward(75)
t.left(90)
t.pendown()
t.color("#00AFCA", "#00AFCA")
t.begin_fill()
t.forward(300)
t.left(90)
t.forward(150)
t.left(90)
t.forward(300)
t.left(90)
t.forward(150)
t.left(90)
t.end_fill()
# күн: центрі туының ортасы (0; 0), радиусы 35
t.penup()
t.forward(150)
t.left(90)
t.forward(40)
t.right(90)
t.pendown()
t.color("#FEC50C", "#FEC50C")
t.begin_fill()
t.circle(35)
t.end_fill()
t.hideturtle()
turtle.done()
```

**8-тапсырма.** Центр (0; 0) нүктесінде болу үшін радиусы r шеңберді (0; −r) нүктесінен бастаймыз.

```python
import turtle

t = turtle.Turtle()
t.speed(0)
t.penup()
t.right(90)
t.forward(90)
t.left(90)
t.pendown()
t.color("red", "red")
t.begin_fill()
t.circle(90)
t.end_fill()
t.penup()
t.left(90)
t.forward(30)
t.right(90)
t.pendown()
t.color("white", "white")
t.begin_fill()
t.circle(60)
t.end_fill()
t.penup()
t.left(90)
t.forward(30)
t.right(90)
t.pendown()
t.color("red", "red")
t.begin_fill()
t.circle(30)
t.end_fill()
t.hideturtle()
turtle.done()
```

**9-тапсырма** (бір мүмкін шешім).

```python
import turtle

turtle.bgcolor("skyblue")
t = turtle.Turtle()
t.speed(0)
t.color("white", "white")
# төменгі шар (радиусы 70)
t.penup()
t.right(90)
t.forward(200)
t.left(90)
t.pendown()
t.begin_fill()
t.circle(70)
t.end_fill()
# ортаңғы шар (радиусы 50)
t.penup()
t.left(90)
t.forward(140)
t.right(90)
t.pendown()
t.begin_fill()
t.circle(50)
t.end_fill()
# басы (радиусы 35)
t.penup()
t.left(90)
t.forward(100)
t.right(90)
t.pendown()
t.begin_fill()
t.circle(35)
t.end_fill()
# көздері
t.penup()
t.left(90)
t.forward(45)
t.left(90)
t.forward(12)
t.dot(8, "black")
t.back(24)
t.dot(8, "black")
# мұрны
t.forward(12)
t.left(90)
t.forward(15)
t.left(90)
t.pendown()
t.color("orange", "orange")
t.begin_fill()
t.forward(25)
t.left(160)
t.forward(25)
t.end_fill()
t.hideturtle()
turtle.done()
```

**10-тапсырма.**

```python
import turtle

t = turtle.Turtle()
t.speed(0)
# 1-шаршы (қара)
t.color("black", "black")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.end_fill()
# 2-шаршы (ақ) — оң жақта
t.forward(60)
t.color("black", "white")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.end_fill()
# 3-шаршы (қара) — оң жақ жоғарғы
t.left(90)
t.forward(60)
t.right(90)
t.color("black", "black")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.end_fill()
# 4-шаршы (ақ) — сол жақ жоғарғы
t.back(60)
t.color("black", "white")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.end_fill()
t.hideturtle()
turtle.done()
```

**Үй тапсырмасы, 3-сұрақ.** `begin_fill()` фигурадан кейін жазылса, бояу басталмаған болады да фигура боялмайды (бояу тек `begin_fill` пен `end_fill` арасындағы жолға қолданылады).

</details>
