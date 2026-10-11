# Сабақ 29. Canvas виджеті. Графика салу және қарапайым анимация.

## Тақырыптар

1. Canvas дегеніміз не?
2. Негізгі фигуралар
3. Кенеп объектілерін басқару
4. Анимация
5. Интерактивті графика
6. Жиі кездесетін қателер
7. Негізгі ұғымдар

## 1. Canvas дегеніміз не?

**Canvas (кенеп, холст)** — сурет салуға арналған тіктөртбұрышты аймақ. Онда фигуралар, мәтін, суреттер салуға және оларды жылжытуға болады. Canvas — диаграммалар, ойындар, графиктер, сурет редакторлары үшін негіз.

```python
import tkinter as tk

root = tk.Tk()
root.title("Canvas")
canvas = tk.Canvas(root, width=400, height=300, bg="white")
canvas.pack(padx=10, pady=10)
canvas.create_line(0, 0, 400, 300)                 # сызық: (x1, y1) → (x2, y2)
canvas.create_rectangle(50, 50, 150, 120, fill="skyblue")
root.mainloop()
```

### Координаттар жүйесі

Canvas-тың өз координаттар жүйесі бар: (0, 0) — кенептің **сол жақ жоғарғы** бұрышы, X оңға, Y **төмен** өседі.

```text
(0,0) ─────────────────────► X (400)
  │    (50,50)
  │      ┌──────────┐
  │      │          │
  │      └──────────┘ (150,120)
  ▼
  Y (300)
```

Тіктөртбұрыш пен сопақ **шектеуші тіктөртбұрыштың** екі қарама-қарсы бұрышымен беріледі: (x1, y1) — сол жақ жоғарғы, (x2, y2) — оң жақ төменгі.

## 2. Негізгі фигуралар

| Әдіс | Мысал | Сипаттама |
|---|---|---|
| `create_line(x1, y1, x2, y2, ...)` | `create_line(10, 10, 200, 10, 200, 100)` | Сызық немесе сынық сызық (бірнеше нүкте) |
| `create_rectangle(x1, y1, x2, y2)` | `create_rectangle(10, 10, 110, 60)` | Тіктөртбұрыш |
| `create_oval(x1, y1, x2, y2)` | `create_oval(10, 10, 110, 110)` | Сопақ; шаршыға сыйса — шеңбер |
| `create_polygon(x1, y1, x2, y2, x3, y3, ...)` | `create_polygon(50, 10, 90, 90, 10, 90)` | Көпбұрыш (үшбұрыш, жұлдыз...) |
| `create_arc(x1, y1, x2, y2, start=, extent=)` | `create_arc(10, 10, 110, 110, start=0, extent=90)` | Доға/сектор (бұрыштар градуспен) |
| `create_text(x, y, text=...)` | `create_text(200, 20, text="Сәлем")` | Мәтін (x, y — мәтіннің ортасы) |
| `create_image(x, y, image=...)` | `create_image(0, 0, image=img, anchor="nw")` | Сурет (`PhotoImage`) |

### Негізгі опциялар

| Опция | Мағынасы | Мысал |
|---|---|---|
| `fill` | Ішкі түс (сызық/мәтін үшін — түсі) | `fill="red"` |
| `outline` | Жиек түсі (`""` — жиексіз) | `outline="black"` |
| `width` | Сызық/жиек қалыңдығы | `width=3` |
| `dash` | Үзік сызық | `dash=(5, 3)` |
| `arrow` | Сызықтағы бағыттауыш: `"first"`, `"last"`, `"both"` | `arrow="last"` |
| `font` | Мәтін шрифті | `font=("Arial", 16, "bold")` |
| `style` | Доға түрі: `"pieslice"`, `"arc"`, `"chord"` | `style="arc"` |
| `smooth` | Сызық/көпбұрышты тегістеу | `smooth=True` |

### Мысал: «Үй мен күн»

```python
import tkinter as tk

root = tk.Tk()
root.title("Ауыл")
c = tk.Canvas(root, width=500, height=350, bg="#74b9ff")
c.pack()

# жер
c.create_rectangle(0, 260, 500, 350, fill="#55efc4", outline="")
# күн сәулелері, содан кейін күннің өзі (кейін салынған объект үстінде тұрады)
for x2, y2 in [(380, 65), (435, 10), (490, 65), (435, 120)]:
    c.create_line(435, 65, x2, y2, fill="#fdcb6e", width=3)
c.create_oval(400, 30, 470, 100, fill="#fdcb6e", outline="#e17055", width=2)
# үй қабырғасы
c.create_rectangle(120, 160, 280, 280, fill="#fab1a0", outline="black", width=2)
# шатыр
c.create_polygon(100, 160, 200, 80, 300, 160, fill="#d63031", outline="black", width=2)
# терезе
c.create_rectangle(145, 190, 195, 235, fill="#ffeaa7", outline="black")
c.create_line(170, 190, 170, 235)
c.create_line(145, 212, 195, 212)
# есік
c.create_rectangle(215, 200, 255, 280, fill="#a0522d", outline="black")
c.create_oval(245, 238, 251, 244, fill="gold")
# жол мен жазу
c.create_line(235, 280, 300, 350, fill="#636e72", width=6, smooth=True)
c.create_text(250, 330, text="Менің ауылым", font=("Arial", 18, "bold"), fill="#2d3436")

root.mainloop()
```

### Доғалар: диаграмма мысалы

```python
import tkinter as tk

DATA = {"Python": 45, "Scratch": 25, "C++": 15, "JavaScript": 15}   # пайыз
COLORS = ["#0984e3", "#fdcb6e", "#d63031", "#00b894"]

root = tk.Tk()
root.title("Дөңгелек диаграмма")
c = tk.Canvas(root, width=420, height=260, bg="white")
c.pack()
start = 0
for i, (name, percent) in enumerate(DATA.items()):
    extent = percent * 360 / 100
    c.create_arc(20, 20, 240, 240, start=start, extent=extent, fill=COLORS[i], outline="white")
    c.create_rectangle(270, 40 + i * 30, 290, 60 + i * 30, fill=COLORS[i], outline="")
    c.create_text(300, 50 + i * 30, text=f"{name} — {percent}%", anchor="w")
    start += extent
root.mainloop()
```

`anchor="w"` — мәтін (x, y) нүктесінен **оңға** қарай басталады (әдепкіде ортасы — `"center"`).

## 3. Кенеп объектілерін басқару

Әр `create_...` әдісі объектінің **идентификаторын** (бүтін сан) қайтарады. Ол арқылы объектіні кейін өзгертеміз:

```python
import tkinter as tk

root = tk.Tk()
c = tk.Canvas(root, width=300, height=200, bg="white")
c.pack()
ball = c.create_oval(10, 10, 50, 50, fill="red")
print("Доп идентификаторы:", ball)
print("Координаттары:", c.coords(ball))
c.move(ball, 100, 50)                 # dx=100, dy=50 жылжыту
print("Жаңа координаттар:", c.coords(ball))
c.itemconfig(ball, fill="green")      # түсін өзгерту
root.destroy()
```

Нәтижесі:
```text
Доп идентификаторы: 1
Координаттары: [10.0, 10.0, 50.0, 50.0]
Жаңа координаттар: [110.0, 60.0, 150.0, 100.0]
```

| Әдіс | Мағынасы |
|---|---|
| `move(id, dx, dy)` | Объектіні dx, dy-ке жылжыту |
| `coords(id)` / `coords(id, x1, y1, ...)` | Координаттарды алу / жаңа координаттар беру |
| `itemconfig(id, **опциялар)` | Түсін, мәтінін т.б. өзгерту |
| `delete(id)` / `delete("all")` | Объектіні / бәрін өшіру |
| `bbox(id)` | Объектіні қоршайтын тіктөртбұрыш (x1, y1, x2, y2) |
| `find_overlapping(x1, y1, x2, y2)` | Аймақпен қиылысатын объектілер (соқтығысу үшін) |
| `tag_raise(id)` / `tag_lower(id)` | Объектіні жоғары/төмен қабатқа шығару |

### Тегтер — объектілер тобы

Бірнеше объектіге ортақ тег беріп, оларды бірге басқаруға болады:

```python
import tkinter as tk

root = tk.Tk()
root.title("Тегтер")
c = tk.Canvas(root, width=400, height=200, bg="white")
c.pack()
# «car» тегі бар үш объект — бір көлік
c.create_rectangle(20, 100, 120, 140, fill="#e84393", tags="car")
c.create_oval(30, 130, 55, 155, fill="black", tags="car")
c.create_oval(85, 130, 110, 155, fill="black", tags="car")
tk.Button(root, text="Алға →", command=lambda: c.move("car", 20, 0)).pack(pady=5)
root.mainloop()
```

## 4. Анимация

Анимация — объектіні аз-аздан жылжытып, экранды жиі қайта сызу. Tkinter-де мұны `after()` жасайды:

```text
step():  1) объектіні жылжыт  →  2) шекараны тексер  →  3) after(20, step)
```

20 мс кідіріс → секундына ~50 кадр (FPS).

```python
import tkinter as tk

W, H, R = 500, 300, 20

root = tk.Tk()
root.title("Секіретін доп")
c = tk.Canvas(root, width=W, height=H, bg="#2d3436")
c.pack()
ball = c.create_oval(50, 50, 50 + 2 * R, 50 + 2 * R, fill="#fdcb6e", outline="")
dx, dy = 4, 3


def step():
    global dx, dy
    c.move(ball, dx, dy)
    x1, y1, x2, y2 = c.coords(ball)
    if x1 <= 0 or x2 >= W:      # сол/оң қабырғаға соқтығысу
        dx = -dx
    if y1 <= 0 or y2 >= H:      # жоғарғы/төменгі қабырға
        dy = -dy
    root.after(20, step)


step()
root.mainloop()
```

### ОБП стиліндегі анимация: бірнеше доп

```python
import random
import tkinter as tk

W, H = 500, 300


class Ball:
    def __init__(self, canvas):
        self.canvas = canvas
        r = random.randint(10, 25)
        x, y = random.randint(r, W - r), random.randint(r, H - r)
        color = random.choice(["#d63031", "#0984e3", "#00b894", "#fdcb6e", "#6c5ce7"])
        self.id = canvas.create_oval(x - r, y - r, x + r, y + r, fill=color, outline="")
        self.dx = random.choice([-4, -3, 3, 4])
        self.dy = random.choice([-4, -3, 3, 4])

    def move(self):
        self.canvas.move(self.id, self.dx, self.dy)
        x1, y1, x2, y2 = self.canvas.coords(self.id)
        if x1 <= 0 or x2 >= W:
            self.dx = -self.dx
        if y1 <= 0 or y2 >= H:
            self.dy = -self.dy


root = tk.Tk()
root.title("Доптар")
canvas = tk.Canvas(root, width=W, height=H, bg="white")
canvas.pack()
balls = [Ball(canvas) for _ in range(8)]


def animate():
    for b in balls:
        b.move()                 # полиморфизм мен ОБП тағы да көмекке келді
    root.after(20, animate)


canvas.bind("<Button-1>", lambda e: balls.append(Ball(canvas)))
animate()
root.mainloop()
```

## 5. Интерактивті графика

### Тінтуірмен сурет салу

```python
import tkinter as tk

root = tk.Tk()
root.title("Сурет салу")
c = tk.Canvas(root, width=500, height=350, bg="white", cursor="pencil")
c.pack()
last = {"x": None, "y": None}
color = tk.StringVar(value="black")


def start(event):
    last["x"], last["y"] = event.x, event.y


def draw(event):
    c.create_line(last["x"], last["y"], event.x, event.y,
                  fill=color.get(), width=3, capstyle="round", smooth=True)
    last["x"], last["y"] = event.x, event.y


c.bind("<Button-1>", start)
c.bind("<B1-Motion>", draw)
bar = tk.Frame(root)
bar.pack(pady=5)
for col in ["black", "red", "green", "blue", "orange"]:
    tk.Radiobutton(bar, bg=col, variable=color, value=col, indicatoron=False,
                   width=3, selectcolor=col).pack(side="left", padx=2)
tk.Button(bar, text="Тазарту", command=lambda: c.delete("all")).pack(side="left", padx=10)
root.mainloop()
```

### Пернетақтамен басқару

```python
import tkinter as tk

STEP = 10
root = tk.Tk()
root.title("Басқару")
c = tk.Canvas(root, width=400, height=300, bg="#dfe6e9")
c.pack()
player = c.create_rectangle(180, 130, 220, 170, fill="#0984e3")
MOVES = {"Left": (-STEP, 0), "Right": (STEP, 0), "Up": (0, -STEP), "Down": (0, STEP)}


def on_key(event):
    if event.keysym in MOVES:
        dx, dy = MOVES[event.keysym]
        c.move(player, dx, dy)


root.bind("<Key>", on_key)
root.mainloop()
```

Бұл — ойынның қарапайым негізі. Толыққанды ойындар үшін Pygame кітапханасы бар (`pip install pygame`) — онда өз ойын циклі, спрайттар, дыбыс және соқтығысуларды өңдеу бар.

## Жиі кездесетін қателер

**1. Шеңберді центр мен радиус арқылы салуға тырысу** — `create_oval(cx, cy, r)` → қате. Дұрысы: `create_oval(cx - r, cy - r, cx + r, cy + r)`.

**2. Y осін математикадағыдай жоғары деп ойлау** — Y үлкейсе, объект **төмен** түседі.

**3. Анимацияда `while True` + `time.sleep()` қолдану**

```python
# ҚАТЕ: терезе қатып қалады
# while True:
#     canvas.move(ball, 5, 0)
#     time.sleep(0.02)
```

Дұрысы — `after()` арқылы өз-өзін шақыратын функция.

**4. `create_...` нәтижесін сақтамау** — кейін объектіні жылжыту мүмкін емес. `ball = c.create_oval(...)`.

**5. `move` пен `coords`-ты шатастыру** — `move` **ығысуды** қосады, `coords` **жаңа абсолютті** координаттарды береді.

**6. Мәтіннің орнын қате түсіну** — `create_text(x, y)` мәтіннің **ортасын** (x, y)-ке қояды; сол жақтан бастау үшін `anchor="w"` немесе `"nw"`.

**7. `PhotoImage`-ті функция ішінде құру** — функция біткенде сурет жойылып, кенепте көрінбейді. Сілтемені сақтаңыз: `self.img = tk.PhotoImage(...)`.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| Canvas | Сурет салуға арналған виджет |
| Шектеуші тіктөртбұрыш | Сопақ/тіктөртбұрышты анықтайтын (x1, y1, x2, y2) |
| Идентификатор (id) | `create_...` қайтаратын объект нөмірі |
| Тег | Кенеп объектілеріне берілетін ортақ атау |
| `move`, `coords`, `itemconfig` | Объектіні жылжыту, орнын беру, қасиетін өзгерту |
| Анимация | Объектіні кадр сайын аз-аздан жылжыту |
| FPS | Секундтағы кадр саны (`after(20)` ≈ 50 FPS) |

## Ескерту

- Canvas координаттары бөлшек сан ретінде сақталады: фигура бүтін координаттармен құрылса да, `coords()` `float` қайтарады.
- Кенептің әр объектісі жад пен сызу уақытын алады; ұзақ анимацияларда керексіз объектілерді `delete` арқылы жойыңыз, әйтпесе бағдарлама біртіндеп баяулайды.
- `after(20, ...)` нақты аралыққа кепілдік бермейді; бірқалыпты жылдамдық үшін қозғалысты өткен уақыт бойынша есептейді (`time.perf_counter()`).
- Арнайы тегтер: `"all"` — барлық объектілер, `"current"` — тінтуір курсорының астындағы объект.
- `canvas.postscript(file="pic.ps")` суретті PostScript форматында сақтайды; PNG-ге сақтау үшін әдетте Pillow кітапханасы қолданылады.
- Canvas бойынша құжаттама: https://tkdocs.com/tutorial/canvas.html
