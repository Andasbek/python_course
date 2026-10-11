# Урок 29. Практические задания: Canvas, графика и анимация

## Базовый уровень

### Задание 1. Флаг Казахстана
На холсте 600×300 нарисуйте голубой (`#00AFCA`) фон, в центре — жёлтое солнце (круг), под ним вместо парящего беркута — жёлтую дугу (`create_arc`, `style="arc"`), слева — жёлтую вертикальную полосу (вместо национального орнамента — ряд прямоугольников).

### Задание 2. Светофор
Внутри чёрного прямоугольника три круга (красный, жёлтый, зелёный — изначально серые). При каждом нажатии кнопки «Следующий» по очереди горит только один (`itemconfig`).

### Задание 3. Координатная сетка
На холсте 400×400 нарисуйте серыми пунктирными линиями сетку через каждые 50 пикселей (`dash=(2, 2)`) и подпишите числа рядом с осями (`create_text`). В месте щелчка мыши должна появиться маленькая красная точка и её координаты.

### Задание 4. Столбчатая диаграмма
Нарисуйте столбчатую диаграмму по данным словаря: `{"Пн": 5, "Вт": 8, "Ср": 3, "Чт": 7, "Пт": 6}` — количество решённых задач в каждый день недели. Над каждым столбцом — число, под ним — название дня. Высота столбца пропорциональна значению (наибольший — 200 пикселей).

## Продвинутый уровень

### Задание 5. Бегущая строка (анимация)
Текст «Добро пожаловать в кружок Python!» должен двигаться справа налево по холсту; когда он полностью уйдёт, он снова появляется справа (проверка через `bbox`).

### Задание 6. Часы
Нарисуйте аналоговые часы с круглым циферблатом (12 делений), часовой, минутной и секундной стрелками. Стрелки обновляются каждую секунду в соответствии с реальным временем (`time.localtime()`, `math.sin/cos`, `coords`).
Подсказка: для угла `θ` (по часовой стрелке, начиная с 12) конец стрелки: `x = cx + L·sin θ`, `y = cy − L·cos θ`.

### Задание 7. Программа для рисования
Усовершенствуйте пример рисования из теории:
- толщина кисти — `Scale` или `Combobox` (1–20);
- режим «Ластик» (рисование цветом фона);
- режимы «Прямоугольник» и «Круг»: фигура рисуется при нажатии, перетаскивании и отпускании мыши (`<ButtonPress-1>`, `<B1-Motion>`, `<ButtonRelease-1>`, предпросмотр через `coords` при перетаскивании).

## ★ Олимпиадная / творческая задача

### Задание 8. Игра «Мяч и ракетка»
Внизу ракетка (управляется `←`/`→` или мышью), прыгающий мяч. Если мяч касается ракетки — отскакивает и очки +1; если падает на землю — текст «Игра окончена! Очки: N» и перезапуск через `Space`. Каждые 5 очков скорость мяча увеличивается. Напишите игру с помощью классов `Ball`, `Paddle`, `Game`.

## Домашнее задание

1. Нарисуйте на Canvas свой рисунок (пейзаж, юрта, космос, робот) — не менее 15 объектов, 3 разных вида фигур, текст.
2. Добавьте в рисунок хотя бы одну анимацию (облако плывёт, звёзды мерцают и т.п.).
3. Загрузите на GitHub под именем `gui/lesson29_canvas.py`, добавьте скриншот в README.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import math
import tkinter as tk

BLUE, GOLD = "#00AFCA", "#FEC50C"
root = tk.Tk()
root.title("Флаг")
c = tk.Canvas(root, width=600, height=300, bg=BLUE, highlightthickness=0)
c.pack()
c.create_oval(260, 70, 360, 170, fill=GOLD, outline="")
for i in range(16):                                   # лучи солнца (упрощённо)
    a = math.radians(i * 22.5)
    c.create_line(310 + 55 * math.cos(a), 120 + 55 * math.sin(a),
                  310 + 70 * math.cos(a), 120 + 70 * math.sin(a), fill=GOLD, width=3)
c.create_arc(200, 150, 420, 260, start=20, extent=140, style="arc", outline=GOLD, width=6)
for y in range(10, 300, 30):                         # полоса орнамента
    c.create_rectangle(20, y, 40, y + 20, fill=GOLD, outline="")
root.mainloop()
```

**Задание 2**
```python
import tkinter as tk

COLORS = ["red", "yellow", "green"]
state = {"current": -1}

root = tk.Tk()
root.title("Светофор")
c = tk.Canvas(root, width=120, height=320, bg="white")
c.pack()
c.create_rectangle(20, 10, 100, 310, fill="black")
lamps = [c.create_oval(30, 20 + i * 100, 90, 80 + i * 100, fill="gray") for i in range(3)]


def next_light():
    state["current"] = (state["current"] + 1) % 3
    for i, lamp in enumerate(lamps):
        c.itemconfig(lamp, fill=COLORS[i] if i == state["current"] else "gray")


tk.Button(root, text="Следующий", command=next_light).pack(pady=5)
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk

SIZE, STEP = 400, 50
root = tk.Tk()
root.title("Сетка")
c = tk.Canvas(root, width=SIZE, height=SIZE, bg="white")
c.pack()
for v in range(0, SIZE + 1, STEP):
    c.create_line(v, 0, v, SIZE, fill="gray", dash=(2, 2))
    c.create_line(0, v, SIZE, v, fill="gray", dash=(2, 2))
    c.create_text(v + 2, 2, text=str(v), anchor="nw", font=("Arial", 7))
    c.create_text(2, v + 2, text=str(v), anchor="nw", font=("Arial", 7))


def on_click(event):
    c.create_oval(event.x - 3, event.y - 3, event.x + 3, event.y + 3, fill="red", outline="")
    c.create_text(event.x + 6, event.y, text=f"({event.x}, {event.y})", anchor="w", fill="red")


c.bind("<Button-1>", on_click)
root.mainloop()
```

**Задание 4**
```python
import tkinter as tk

DATA = {"Пн": 5, "Вт": 8, "Ср": 3, "Чт": 7, "Пт": 6}
W, H, BASE, MAX_H, BAR = 400, 300, 260, 200, 40

root = tk.Tk()
root.title("Диаграмма")
c = tk.Canvas(root, width=W, height=H, bg="white")
c.pack()
top = max(DATA.values())
c.create_line(20, BASE, W - 20, BASE, width=2)
for i, (day, value) in enumerate(DATA.items()):
    x1 = 40 + i * 70
    h = value / top * MAX_H
    c.create_rectangle(x1, BASE - h, x1 + BAR, BASE, fill="#0984e3", outline="")
    c.create_text(x1 + BAR / 2, BASE - h - 10, text=str(value))
    c.create_text(x1 + BAR / 2, BASE + 15, text=day)
root.mainloop()
```

**Задание 5**
```python
import tkinter as tk

W = 500
root = tk.Tk()
root.title("Бегущая строка")
c = tk.Canvas(root, width=W, height=80, bg="black")
c.pack()
msg = c.create_text(W, 40, text="Добро пожаловать в кружок Python!", fill="#00ff00",
                    font=("Arial", 20, "bold"), anchor="w")


def scroll():
    c.move(msg, -3, 0)
    x1, y1, x2, y2 = c.bbox(msg)
    if x2 < 0:
        c.coords(msg, W, 40)
    root.after(20, scroll)


scroll()
root.mainloop()
```

**Задание 6**
```python
import math
import time
import tkinter as tk

CX, CY, R = 150, 150, 120
root = tk.Tk()
root.title("Часы")
c = tk.Canvas(root, width=300, height=300, bg="white")
c.pack()
c.create_oval(CX - R, CY - R, CX + R, CY + R, width=3)
for h in range(12):
    a = math.radians(h * 30)
    c.create_line(CX + (R - 12) * math.sin(a), CY - (R - 12) * math.cos(a),
                  CX + R * math.sin(a), CY - R * math.cos(a), width=3)
    c.create_text(CX + (R - 28) * math.sin(a), CY - (R - 28) * math.cos(a),
                  text=str(h if h else 12), font=("Arial", 12))
hands = {
    "h": c.create_line(CX, CY, CX, CY, width=6, fill="black"),
    "m": c.create_line(CX, CY, CX, CY, width=4, fill="#2d3436"),
    "s": c.create_line(CX, CY, CX, CY, width=1, fill="red"),
}


def set_hand(hand, angle_deg, length):
    a = math.radians(angle_deg)
    c.coords(hands[hand], CX, CY, CX + length * math.sin(a), CY - length * math.cos(a))


def tick():
    t = time.localtime()
    set_hand("h", (t.tm_hour % 12) * 30 + t.tm_min * 0.5, R * 0.5)
    set_hand("m", t.tm_min * 6 + t.tm_sec * 0.1, R * 0.75)
    set_hand("s", t.tm_sec * 6, R * 0.9)
    root.after(1000, tick)


tick()
root.mainloop()
```

**Задание 7**
```python
import tkinter as tk
from tkinter import ttk

BG = "white"


class Paint(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Рисование")
        self.mode = tk.StringVar(value="pen")
        self.color = "black"
        self.size = tk.IntVar(value=3)
        self.start = None
        self.preview = None
        bar = tk.Frame(self)
        bar.pack(fill="x")
        for text, value in [("Карандаш", "pen"), ("Ластик", "eraser"),
                            ("Прямоугольник", "rect"), ("Круг", "oval")]:
            tk.Radiobutton(bar, text=text, variable=self.mode, value=value).pack(side="left")
        ttk.Combobox(bar, textvariable=self.size, values=list(range(1, 21)),
                     width=4, state="readonly").pack(side="left", padx=5)
        for col in ["black", "red", "green", "blue"]:
            tk.Button(bar, bg=col, width=2,
                      command=lambda c=col: setattr(self, "color", c)).pack(side="left")
        tk.Button(bar, text="Очистить", command=lambda: self.canvas.delete("all")).pack(side="right")
        self.canvas = tk.Canvas(self, width=600, height=400, bg=BG)
        self.canvas.pack()
        self.canvas.bind("<ButtonPress-1>", self.on_press)
        self.canvas.bind("<B1-Motion>", self.on_drag)
        self.canvas.bind("<ButtonRelease-1>", self.on_release)

    def on_press(self, e):
        self.start = (e.x, e.y)
        mode = self.mode.get()
        if mode == "rect":
            self.preview = self.canvas.create_rectangle(e.x, e.y, e.x, e.y,
                                                        outline=self.color, width=self.size.get())
        elif mode == "oval":
            self.preview = self.canvas.create_oval(e.x, e.y, e.x, e.y,
                                                   outline=self.color, width=self.size.get())

    def on_drag(self, e):
        mode = self.mode.get()
        if mode in ("pen", "eraser"):
            color = BG if mode == "eraser" else self.color
            width = self.size.get() * (3 if mode == "eraser" else 1)
            self.canvas.create_line(*self.start, e.x, e.y, fill=color, width=width,
                                    capstyle="round")
            self.start = (e.x, e.y)
        elif self.preview:
            self.canvas.coords(self.preview, *self.start, e.x, e.y)

    def on_release(self, e):
        self.preview = None


if __name__ == "__main__":
    Paint().mainloop()
```

**Задание 8**
```python
import tkinter as tk

W, H = 500, 400


class Paddle:
    def __init__(self, canvas):
        self.canvas = canvas
        self.id = canvas.create_rectangle(200, H - 30, 300, H - 15, fill="#0984e3")
        self.dx = 0

    def move(self):
        x1, _, x2, _ = self.canvas.coords(self.id)
        if (self.dx < 0 and x1 > 0) or (self.dx > 0 and x2 < W):
            self.canvas.move(self.id, self.dx, 0)


class Ball:
    def __init__(self, canvas):
        self.canvas = canvas
        self.id = canvas.create_oval(240, 100, 260, 120, fill="#d63031")
        self.dx, self.dy = 3, 3

    def move(self):
        self.canvas.move(self.id, self.dx, self.dy)
        x1, y1, x2, y2 = self.canvas.coords(self.id)
        if x1 <= 0 or x2 >= W:
            self.dx = -self.dx
        if y1 <= 0:
            self.dy = -self.dy
        return y2 >= H                     # упал на землю?

    def speed_up(self):
        self.dx += 1 if self.dx > 0 else -1
        self.dy += 1 if self.dy > 0 else -1


class Game:
    def __init__(self, root):
        self.root = root
        self.canvas = tk.Canvas(root, width=W, height=H, bg="#2d3436")
        self.canvas.pack()
        root.bind("<KeyPress-Left>", lambda e: self.set_dx(-6))
        root.bind("<KeyPress-Right>", lambda e: self.set_dx(6))
        root.bind("<KeyRelease-Left>", lambda e: self.set_dx(0))
        root.bind("<KeyRelease-Right>", lambda e: self.set_dx(0))
        root.bind("<space>", lambda e: self.restart())
        self.running = False
        self.restart()

    def set_dx(self, dx):
        self.paddle.dx = dx

    def restart(self):
        if self.running:
            return
        self.canvas.delete("all")
        self.paddle = Paddle(self.canvas)
        self.ball = Ball(self.canvas)
        self.score = 0
        self.score_text = self.canvas.create_text(10, 10, anchor="nw", fill="white",
                                                  text="Очки: 0", font=("Arial", 14))
        self.running = True
        self.loop()

    def check_hit(self):
        bx1, by1, bx2, by2 = self.canvas.coords(self.ball.id)
        if self.paddle.id in self.canvas.find_overlapping(bx1, by1, bx2, by2) and self.ball.dy > 0:
            self.ball.dy = -self.ball.dy
            self.score += 1
            self.canvas.itemconfig(self.score_text, text=f"Очки: {self.score}")
            if self.score % 5 == 0:
                self.ball.speed_up()

    def loop(self):
        self.paddle.move()
        fell = self.ball.move()
        self.check_hit()
        if fell:
            self.running = False
            self.canvas.create_text(W / 2, H / 2, fill="white", font=("Arial", 22, "bold"),
                                    text=f"Игра окончена! Очки: {self.score}\nSpace — начать заново",
                                    justify="center")
            return
        self.root.after(20, self.loop)


if __name__ == "__main__":
    root = tk.Tk()
    root.title("Мяч и ракетка")
    Game(root)
    root.mainloop()
```

</details>
