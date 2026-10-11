# Сабақ 33. Практикалық тапсырмалар: turtle және цикл

Барлық тапсырмаларда `import turtle` және соңында `turtle.done()` болуы керек. Күрделі суреттерде `t.speed(0)` қолдан.

## Базалық деңгей

### 1-тапсырма. Көпбұрыштар жинағы
Цикл арқылы бір терезеде: қызыл үшбұрыш, жасыл шаршы, көк алтыбұрыш сал. Әр фигураның арасында тасбақаны `penup()` / `goto()` / `pendown()` арқылы жылжыт.

### 2-тапсырма. Әмбебап көпбұрыш
Пайдаланушы қабырға санын `n`, ұзындығын `a` және түсін (`red`, `blue`, т.б.) енгізеді. Бағдарлама сол көпбұрышты салып, бояйды.

### 3-тапсырма. Алтын жұлдыз
Қызыл фонда (`bgcolor`) сары, боялған бес бұрышты жұлдыз сал.

### 4-тапсырма. Шаршы спираль
Қабырғасы 5-тен басталып, әр қадам сайын 5-ке артатын 40 қадамдық шаршы спираль сал.

## Кеңейтілген деңгей

### 5-тапсырма. Розетка
Алтыбұрышты 18 рет айналдырып, розетка сал. Әр айналым сайын түс ауысып отырсын (3 түс, `i % 3` арқылы).

### 6-тапсырма. Ұлғаятын шаршылар
Ортасы бір нүктеде орналасқан, өлшемі 20, 40, 60, ..., 200 болатын 10 шаршы сал (концентрлі шаршылар).

**Кеңес:** әр шаршыны салмас бұрын тасбақаны `goto(-size / 2, size / 2)` нүктесіне апар.

### 7-тапсырма. Баспалдақ
Тасбақа 8 сатылы баспалдақ салсын (әр саты: жоғары 20, оңға 30).

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Қазақ ою-өрнегіне ұқсас өрнек
Кірістірілген циклдер арқылы симметриялы өрнек ойлап тап (мысалы, «қошқар мүйіз» мотивін шеңбер доғаларымен `circle(r, extent)` арқылы бейнеле). Өрнек 4 немесе 8 рет қайталанып, симметриялы болсын.

### 9-тапсырма. Түнгі аспан
Қара фонда кездейсоқ емес, бірақ әртүрлі орындарда орналасқан 20 жұлдыз сал. Жұлдыздың координатасын цикл айнымалысынан есепте, мысалы: `x = (i * 97) % 600 - 300`, `y = (i * 53) % 400 - 200`; өлшемі `10 + i % 4 * 5`.

## Үй тапсырмасы

1. 3-тен 8 қабырғаға дейінгі көпбұрыштарды бір қатарға (сол жақтан оңға қарай) сал.
2. Өз есіміңнің бас әрпінің айналасына розетка сал (әріпті `t.write("А", font=("Arial", 40, "bold"))` арқылы жазуға болады).
3. 2–3 параметрін өзгерте отырып спираль өрнектерімен тәжірибе жаса; ең ұнаған 2 нұсқаның кодын және «бұл бұрыш неге осындай өрнек береді?» деген сұраққа өз жауабыңды дайында.

## Бағалау критерийлері (шығармашылық жұмыс — 8/9-тапсырма)

| Критерий | Ұпай |
|---|---|
| Цикл (және кірістірілген цикл) дұрыс қолданылған | 3 |
| Бұрыштар мен өлшемдер дұрыс есептелген (фигура тұйықталады) | 2 |
| Түстер, бояу, жылдамдық орынды қолданылған | 2 |
| Өрнек симметриялы, ұқыпты | 2 |
| Идеяның өзіндігі | 1 |
| **Барлығы** | **10** |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import turtle

t = turtle.Turtle()
t.penup()
t.goto(-250, 0)
t.pendown()
t.color("red")
for i in range(3):
    t.forward(100)
    t.left(120)

t.penup()
t.goto(-50, 0)
t.pendown()
t.color("green")
for i in range(4):
    t.forward(100)
    t.left(90)

t.penup()
t.goto(150, 0)
t.pendown()
t.color("blue")
for i in range(6):
    t.forward(60)
    t.left(60)
turtle.done()
```

**2-тапсырма**
```python
import turtle

n = int(input("Қабырға саны: "))
a = int(input("Ұзындығы: "))
col = input("Түсі (ағылшынша): ")
t = turtle.Turtle()
t.color(col)
t.begin_fill()
for i in range(n):
    t.forward(a)
    t.right(360 / n)
t.end_fill()
turtle.done()
```

**3-тапсырма**
```python
import turtle

turtle.bgcolor("red")
t = turtle.Turtle()
t.color("yellow")
t.penup()
t.goto(-100, 30)
t.pendown()
t.begin_fill()
for i in range(5):
    t.forward(200)
    t.right(144)
t.end_fill()
t.hideturtle()
turtle.done()
```

**4-тапсырма**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
for i in range(1, 41):
    t.forward(i * 5)
    t.right(90)
turtle.done()
```

**5-тапсырма**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
for k in range(18):
    if k % 3 == 0:
        t.color("red")
    elif k % 3 == 1:
        t.color("green")
    else:
        t.color("blue")
    for i in range(6):
        t.forward(80)
        t.right(60)
    t.right(20)
t.hideturtle()
turtle.done()
```

**6-тапсырма**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
for size in range(20, 201, 20):
    t.penup()
    t.goto(-size / 2, size / 2)
    t.pendown()
    for i in range(4):
        t.forward(size)
        t.right(90)
turtle.done()
```

**7-тапсырма**
```python
import turtle

t = turtle.Turtle()
t.penup()
t.goto(-150, -150)
t.pendown()
for step in range(8):
    t.left(90)
    t.forward(20)
    t.right(90)
    t.forward(30)
turtle.done()
```

**8-тапсырма (үлгі)**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
t.pensize(3)
t.color("darkred")
for k in range(8):
    t.forward(80)
    t.circle(20, 270)      # мүйіздің бұйрасы
    t.penup()
    t.goto(0, 0)
    t.pendown()
    t.setheading(k * 45 + 45)
t.hideturtle()
turtle.done()
```

**9-тапсырма**
```python
import turtle

turtle.bgcolor("black")
turtle.tracer(0)
t = turtle.Turtle()
t.hideturtle()
t.color("white")
for i in range(20):
    x = (i * 97) % 600 - 300
    y = (i * 53) % 400 - 200
    size = 10 + i % 4 * 5
    t.penup()
    t.goto(x, y)
    t.pendown()
    t.begin_fill()
    for j in range(5):
        t.forward(size)
        t.right(144)
    t.end_fill()
turtle.update()
turtle.done()
```

**Үй тапсырмасы (1)**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
x = -330
for n in range(3, 9):
    t.penup()
    t.goto(x, 0)
    t.pendown()
    for i in range(n):
        t.forward(40)
        t.left(360 / n)
    x += 110
turtle.done()
```
</details>
