# Урок 29. Виджет Canvas. Рисование графики и простая анимация.

## Темы

1. Что такое Canvas?
2. Основные фигуры
3. Управление объектами холста
4. Анимация
5. Интерактивная графика
6. Частые ошибки
7. Основные понятия

## 1. Что такое Canvas?

**Canvas (холст)** — прямоугольная область для рисования. На ней можно рисовать фигуры, текст, изображения и перемещать их. Canvas — основа для диаграмм, игр, графиков, графических редакторов.

```python
import tkinter as tk

root = tk.Tk()
root.title("Canvas")
canvas = tk.Canvas(root, width=400, height=300, bg="white")
canvas.pack(padx=10, pady=10)
canvas.create_line(0, 0, 400, 300)                 # линия: (x1, y1) → (x2, y2)
canvas.create_rectangle(50, 50, 150, 120, fill="skyblue")
root.mainloop()
```

### Система координат

У Canvas своя система координат: (0, 0) — **левый верхний** угол холста, X растёт вправо, Y — **вниз**.

```text
(0,0) ─────────────────────► X (400)
  │    (50,50)
  │      ┌──────────┐
  │      │          │
  │      └──────────┘ (150,120)
  ▼
  Y (300)
```

Прямоугольник и овал задаются двумя противоположными углами **ограничивающего прямоугольника**: (x1, y1) — левый верхний, (x2, y2) — правый нижний.

## 2. Основные фигуры

| Метод | Пример | Описание |
|---|---|---|
| `create_line(x1, y1, x2, y2, ...)` | `create_line(10, 10, 200, 10, 200, 100)` | Линия или ломаная (несколько точек) |
| `create_rectangle(x1, y1, x2, y2)` | `create_rectangle(10, 10, 110, 60)` | Прямоугольник |
| `create_oval(x1, y1, x2, y2)` | `create_oval(10, 10, 110, 110)` | Овал; если вписан в квадрат — круг |
| `create_polygon(x1, y1, x2, y2, x3, y3, ...)` | `create_polygon(50, 10, 90, 90, 10, 90)` | Многоугольник (треугольник, звезда...) |
| `create_arc(x1, y1, x2, y2, start=, extent=)` | `create_arc(10, 10, 110, 110, start=0, extent=90)` | Дуга/сектор (углы в градусах) |
| `create_text(x, y, text=...)` | `create_text(200, 20, text="Привет")` | Текст (x, y — центр текста) |
| `create_image(x, y, image=...)` | `create_image(0, 0, image=img, anchor="nw")` | Изображение (`PhotoImage`) |

### Основные опции

| Опция | Смысл | Пример |
|---|---|---|
| `fill` | Цвет заливки (для линии/текста — цвет) | `fill="red"` |
| `outline` | Цвет контура (`""` — без контура) | `outline="black"` |
| `width` | Толщина линии/контура | `width=3` |
| `dash` | Пунктирная линия | `dash=(5, 3)` |
| `arrow` | Стрелка на линии: `"first"`, `"last"`, `"both"` | `arrow="last"` |
| `font` | Шрифт текста | `font=("Arial", 16, "bold")` |
| `style` | Вид дуги: `"pieslice"`, `"arc"`, `"chord"` | `style="arc"` |
| `smooth` | Сглаживание линии/многоугольника | `smooth=True` |

### Пример: «Дом и солнце»

```python
import tkinter as tk

root = tk.Tk()
root.title("Аул")
c = tk.Canvas(root, width=500, height=350, bg="#74b9ff")
c.pack()

# земля
c.create_rectangle(0, 260, 500, 350, fill="#55efc4", outline="")
# лучи солнца, затем само солнце (объект, нарисованный позже, находится сверху)
for x2, y2 in [(380, 65), (435, 10), (490, 65), (435, 120)]:
    c.create_line(435, 65, x2, y2, fill="#fdcb6e", width=3)
c.create_oval(400, 30, 470, 100, fill="#fdcb6e", outline="#e17055", width=2)
# стена дома
c.create_rectangle(120, 160, 280, 280, fill="#fab1a0", outline="black", width=2)
# крыша
c.create_polygon(100, 160, 200, 80, 300, 160, fill="#d63031", outline="black", width=2)
# окно
c.create_rectangle(145, 190, 195, 235, fill="#ffeaa7", outline="black")
c.create_line(170, 190, 170, 235)
c.create_line(145, 212, 195, 212)
# дверь
c.create_rectangle(215, 200, 255, 280, fill="#a0522d", outline="black")
c.create_oval(245, 238, 251, 244, fill="gold")
# дорога и надпись
c.create_line(235, 280, 300, 350, fill="#636e72", width=6, smooth=True)
c.create_text(250, 330, text="Мой аул", font=("Arial", 18, "bold"), fill="#2d3436")

root.mainloop()
```

### Дуги: пример диаграммы

```python
import tkinter as tk

DATA = {"Python": 45, "Scratch": 25, "C++": 15, "JavaScript": 15}   # проценты
COLORS = ["#0984e3", "#fdcb6e", "#d63031", "#00b894"]

root = tk.Tk()
root.title("Круговая диаграмма")
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

`anchor="w"` — текст начинается от точки (x, y) **вправо** (по умолчанию центр — `"center"`).

## 3. Управление объектами холста

Каждый метод `create_...` возвращает **идентификатор** объекта (целое число). Через него объект можно потом изменять:

```python
import tkinter as tk

root = tk.Tk()
c = tk.Canvas(root, width=300, height=200, bg="white")
c.pack()
ball = c.create_oval(10, 10, 50, 50, fill="red")
print("Идентификатор мяча:", ball)
print("Координаты:", c.coords(ball))
c.move(ball, 100, 50)                 # сдвинуть на dx=100, dy=50
print("Новые координаты:", c.coords(ball))
c.itemconfig(ball, fill="green")      # изменить цвет
root.destroy()
```

Результат:
```text
Идентификатор мяча: 1
Координаты: [10.0, 10.0, 50.0, 50.0]
Новые координаты: [110.0, 60.0, 150.0, 100.0]
```

| Метод | Смысл |
|---|---|
| `move(id, dx, dy)` | Сдвинуть объект на dx, dy |
| `coords(id)` / `coords(id, x1, y1, ...)` | Получить координаты / задать новые |
| `itemconfig(id, **опции)` | Изменить цвет, текст и т.д. |
| `delete(id)` / `delete("all")` | Удалить объект / всё |
| `bbox(id)` | Ограничивающий прямоугольник объекта (x1, y1, x2, y2) |
| `find_overlapping(x1, y1, x2, y2)` | Объекты, пересекающиеся с областью (для столкновений) |
| `tag_raise(id)` / `tag_lower(id)` | Поднять объект на верхний / опустить на нижний слой |

### Теги — группа объектов

Нескольким объектам можно дать общий тег и управлять ими вместе:

```python
import tkinter as tk

root = tk.Tk()
root.title("Теги")
c = tk.Canvas(root, width=400, height=200, bg="white")
c.pack()
# три объекта с тегом «car» — одна машина
c.create_rectangle(20, 100, 120, 140, fill="#e84393", tags="car")
c.create_oval(30, 130, 55, 155, fill="black", tags="car")
c.create_oval(85, 130, 110, 155, fill="black", tags="car")
tk.Button(root, text="Вперёд →", command=lambda: c.move("car", 20, 0)).pack(pady=5)
root.mainloop()
```

## 4. Анимация

Анимация — перемещение объекта понемногу и частая перерисовка экрана. В Tkinter это делает `after()`:

```text
step():  1) сдвинь объект  →  2) проверь границу  →  3) after(20, step)
```

Задержка 20 мс → примерно 50 кадров в секунду (FPS).

```python
import tkinter as tk

W, H, R = 500, 300, 20

root = tk.Tk()
root.title("Прыгающий мяч")
c = tk.Canvas(root, width=W, height=H, bg="#2d3436")
c.pack()
ball = c.create_oval(50, 50, 50 + 2 * R, 50 + 2 * R, fill="#fdcb6e", outline="")
dx, dy = 4, 3


def step():
    global dx, dy
    c.move(ball, dx, dy)
    x1, y1, x2, y2 = c.coords(ball)
    if x1 <= 0 or x2 >= W:      # столкновение с левой/правой стеной
        dx = -dx
    if y1 <= 0 or y2 >= H:      # верхняя/нижняя стена
        dy = -dy
    root.after(20, step)


step()
root.mainloop()
```

### Анимация в стиле ООП: несколько мячей

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
root.title("Мячи")
canvas = tk.Canvas(root, width=W, height=H, bg="white")
canvas.pack()
balls = [Ball(canvas) for _ in range(8)]


def animate():
    for b in balls:
        b.move()                 # полиморфизм и ООП снова пришли на помощь
    root.after(20, animate)


canvas.bind("<Button-1>", lambda e: balls.append(Ball(canvas)))
animate()
root.mainloop()
```

## 5. Интерактивная графика

### Рисование мышью

```python
import tkinter as tk

root = tk.Tk()
root.title("Рисование")
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
tk.Button(bar, text="Очистить", command=lambda: c.delete("all")).pack(side="left", padx=10)
root.mainloop()
```

### Управление с клавиатуры

```python
import tkinter as tk

STEP = 10
root = tk.Tk()
root.title("Управление")
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

Это простейшая основа игры. Для полноценных игр существует библиотека Pygame (`pip install pygame`) — в ней есть собственный игровой цикл, спрайты, звук и обработка столкновений.

## Частые ошибки

**1. Попытка нарисовать круг через центр и радиус** — `create_oval(cx, cy, r)` → ошибка. Правильно: `create_oval(cx - r, cy - r, cx + r, cy + r)`.

**2. Считать, что ось Y направлена вверх, как в математике** — при увеличении Y объект опускается **вниз**.

**3. Использование `while True` + `time.sleep()` в анимации**

```python
# ОШИБКА: окно зависнет
# while True:
#     canvas.move(ball, 5, 0)
#     time.sleep(0.02)
```

Правильно — функция, вызывающая саму себя через `after()`.

**4. Не сохранили результат `create_...`** — потом объект не сдвинуть. `ball = c.create_oval(...)`.

**5. Путаница `move` и `coords`** — `move` прибавляет **смещение**, `coords` задаёт **новые абсолютные** координаты.

**6. Неверное понимание положения текста** — `create_text(x, y)` ставит **центр** текста в (x, y); чтобы начать слева, нужен `anchor="w"` или `"nw"`.

**7. Создание `PhotoImage` внутри функции** — после завершения функции изображение удаляется и не видно на холсте. Сохраняйте ссылку: `self.img = tk.PhotoImage(...)`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Canvas | Виджет для рисования |
| Ограничивающий прямоугольник | (x1, y1, x2, y2), определяющий овал/прямоугольник |
| Идентификатор (id) | Номер объекта, возвращаемый `create_...` |
| Тег | Общее имя, присваиваемое объектам холста |
| `move`, `coords`, `itemconfig` | Перемещение объекта, задание положения, изменение свойств |
| Анимация | Перемещение объекта понемногу в каждом кадре |
| FPS | Количество кадров в секунду (`after(20)` ≈ 50 FPS) |

## Примечание

- Координаты Canvas хранятся как дробные числа: `coords()` возвращает `float`, даже если фигура создана с целыми координатами.
- Каждый объект холста занимает память и время отрисовки; в долгих анимациях удаляйте ненужные объекты через `delete`, иначе программа постепенно замедляется.
- `after(20, ...)` не гарантирует точный интервал; для равномерной скорости движение рассчитывают по прошедшему времени (`time.perf_counter()`).
- Специальные теги: `"all"` — все объекты, `"current"` — объект под курсором мыши.
- `canvas.postscript(file="pic.ps")` сохраняет рисунок в формате PostScript; для сохранения в PNG обычно используют библиотеку Pillow.
- Документация по Canvas: https://tkdocs.com/tutorial/canvas.html
