# Урок 25. Практические задания: обработка событий

## Базовый уровень

### Задание 1. Кнопки-смайлики
Три кнопки: «😀», «😐», «😢». Смайлик нажатой кнопки выводится в большом Label, а в заголовок записывается «Настроение: хорошее/обычное/плохое». Используйте одну функцию `set_mood(emoji, text)` и `lambda`.

### Задание 2. Координаты мыши
В области Label 400×300 при каждом перемещении курсора (`<Motion>`) внизу должно обновляться «x = ..., y = ...». При нажатии левой кнопки цвет области меняется случайным образом.

### Задание 3. Вычисление по Enter
При вводе числа в Entry и нажатии **Enter** или кнопки «Вычислить» выводится факториал числа (`math.factorial`). Только одна функция (`event=None`).

### Задание 4. Секундомер
Секундомер с кнопками «Старт», «Стоп», «Сброс» (`after(100, ...)`, с десятыми долями секунды: `12.3 с`).

## Продвинутый уровень

### Задание 5. Клавиатурный тренажёр
В окне появляется случайная буква (`random.choice("ASDFJKL")`). Если пользователь нажал эту клавишу — очки +1 и новая буква; если ошибся — очки −1, и экран на мгновение краснеет (через `after(200, ...)` снова белый). Через 30 секунд игра заканчивается и выводится результат.

### Задание 6. Кнопка «убегает»
Кнопка «Нажми меня!», размещённая с помощью `place`. В момент, когда курсор входит на кнопку (`<Enter>`), кнопка перемещается в случайное место окна. Если пользователь всё же успел нажать — «Вы победили!» (Подсказка: нажать можно с помощью `Tab` и `space`.)

### Задание 7. Горячие клавиши
Текстовое поле (Entry) и надпись. Сочетания клавиш:
- `Ctrl+U` — текст заглавными буквами;
- `Ctrl+L` — строчными буквами;
- `Ctrl+D` — очистить поле;
- `Esc` — выход из программы.

## ★ Олимпиадная / творческая задача

### Задание 8. Игра «Реакция»
Окно красного цвета говорит «Ждите...». Через 2–5 секунд (случайно) становится зелёным и пишет «ЖМИ!». Вычисляется время (мс) до нажатия пользователем (`time.perf_counter()`). Если нажал рано — «Поторопились!». Показываются лучший и средний результаты из 5 попыток. Напишите игру в виде класса, наследуемого от `tk.Tk`.

## Домашнее задание

1. Добавьте к секундомеру из задания 4 кнопку «Круг» (lap): при каждом нажатии текущее время добавляется новой строкой в Label ниже.
2. Сделайте так, чтобы клавиатурой телефона/калькулятора из урока 24 можно было управлять и с клавиатуры (`root.bind("<Key>", ...)`).
3. Загрузите на GitHub под именем `gui/lesson25_events.py`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import tkinter as tk


def set_mood(emoji, text):
    face.config(text=emoji)
    root.title(f"Настроение: {text}")


root = tk.Tk()
root.title("Настроение")
face = tk.Label(root, text="?", font=("Arial", 60))
face.pack(padx=40, pady=10)
for emoji, text in [("😀", "хорошее"), ("😐", "обычное"), ("😢", "плохое")]:
    tk.Button(root, text=emoji, font=("Arial", 20),
              command=lambda e=emoji, t=text: set_mood(e, t)).pack(side="left", padx=10, pady=10)
root.mainloop()
```

**Задание 2**
```python
import random
import tkinter as tk


def on_move(event):
    coords.config(text=f"x = {event.x}, y = {event.y}")


def on_click(event):
    area.config(bg="#%06x" % random.randint(0, 0xFFFFFF))


root = tk.Tk()
root.title("Координаты")
area = tk.Frame(root, width=400, height=300, bg="white")
area.pack()
coords = tk.Label(root, text="x = 0, y = 0")
coords.pack(pady=5)
area.bind("<Motion>", on_move)
area.bind("<Button-1>", on_click)
root.mainloop()
```
(Размер Frame задаётся в пикселях, поэтому здесь он удобнее, чем Label.)

**Задание 3**
```python
import math
import tkinter as tk


def calc(event=None):
    try:
        n = int(entry.get())
        if n < 0:
            raise ValueError
        result.config(text=f"{n}! = {math.factorial(n)}")
    except ValueError:
        result.config(text="Введите неотрицательное целое число")


root = tk.Tk()
root.title("Факториал")
entry = tk.Entry(root, justify="center")
entry.pack(padx=20, pady=10)
entry.focus()
entry.bind("<Return>", calc)
tk.Button(root, text="Вычислить", command=calc).pack()
result = tk.Label(root, text="", wraplength=300)
result.pack(pady=10)
root.mainloop()
```

**Задание 4** (с добавленной кнопкой «Круг» из домашнего задания)
```python
import tkinter as tk

state = {"tenths": 0, "running": False, "job": None}


def update():
    if state["running"]:
        state["tenths"] += 1
        label.config(text=f"{state['tenths'] / 10:.1f} с")
        state["job"] = root.after(100, update)


def start():
    if not state["running"]:
        state["running"] = True
        state["job"] = root.after(100, update)


def stop():
    state["running"] = False
    if state["job"]:
        root.after_cancel(state["job"])
        state["job"] = None


def reset():
    stop()
    state["tenths"] = 0
    label.config(text="0.0 с")
    laps.config(text="")


def lap():
    laps.config(text=laps.cget("text") + f"{state['tenths'] / 10:.1f} с\n")


root = tk.Tk()
root.title("Секундомер")
label = tk.Label(root, text="0.0 с", font=("Consolas", 36))
label.pack(padx=30, pady=10)
bar = tk.Frame(root)
bar.pack()
for text, cmd in [("Старт", start), ("Стоп", stop), ("Сброс", reset), ("Круг", lap)]:
    tk.Button(bar, text=text, command=cmd, width=8).pack(side="left", padx=2)
laps = tk.Label(root, text="", justify="left")
laps.pack(pady=5)
root.mainloop()
```

**Задание 5**
```python
import random
import tkinter as tk

LETTERS = "ASDFJKL"
state = {"score": 0, "letter": random.choice(LETTERS), "time": 30, "over": False}


def new_letter():
    state["letter"] = random.choice(LETTERS)
    letter_lbl.config(text=state["letter"])


def flash_red():
    root.config(bg="red")
    root.after(200, lambda: root.config(bg="white"))


def on_key(event):
    if state["over"]:
        return
    if event.keysym.upper() == state["letter"]:
        state["score"] += 1
        new_letter()
    else:
        state["score"] -= 1
        flash_red()
    score_lbl.config(text=f"Очки: {state['score']}")


def tick():
    state["time"] -= 1
    time_lbl.config(text=f"Время: {state['time']}")
    if state["time"] > 0:
        root.after(1000, tick)
    else:
        state["over"] = True
        letter_lbl.config(text="Конец!")


root = tk.Tk()
root.title("Клавиатурный тренажёр")
root.config(bg="white")
letter_lbl = tk.Label(root, text=state["letter"], font=("Arial", 72), bg="white")
letter_lbl.pack(padx=60, pady=10)
score_lbl = tk.Label(root, text="Очки: 0", bg="white")
score_lbl.pack()
time_lbl = tk.Label(root, text="Время: 30", bg="white")
time_lbl.pack(pady=5)
root.bind("<Key>", on_key)
root.after(1000, tick)
root.mainloop()
```

**Задание 6**
```python
import random
import tkinter as tk


def run_away(event):
    btn.place(x=random.randint(0, 300), y=random.randint(0, 250))


def win():
    btn.config(text="Вы победили!", state="disabled")


root = tk.Tk()
root.title("Убегающая кнопка")
root.geometry("400x300")
btn = tk.Button(root, text="Нажми меня!", command=win)
btn.place(x=150, y=130)
btn.bind("<Enter>", run_away)
root.mainloop()
```

**Задание 7**
```python
import tkinter as tk


def upper(event=None):
    text = entry.get()
    entry.delete(0, tk.END)
    entry.insert(0, text.upper())


def lower(event=None):
    text = entry.get()
    entry.delete(0, tk.END)
    entry.insert(0, text.lower())


def clear(event=None):
    entry.delete(0, tk.END)


root = tk.Tk()
root.title("Горячие клавиши")
entry = tk.Entry(root, width=40)
entry.pack(padx=10, pady=10)
entry.focus()
tk.Label(root, text="Ctrl+U — ЗАГЛАВНЫЕ, Ctrl+L — строчные, Ctrl+D — очистить, Esc — выход").pack(pady=5)
root.bind("<Control-u>", upper)
root.bind("<Control-l>", lower)
root.bind("<Control-d>", clear)
root.bind("<Escape>", lambda e: root.destroy())
root.mainloop()
```

**Задание 8**
```python
import random
import time
import tkinter as tk


class ReactionGame(tk.Tk):
    ROUNDS = 5

    def __init__(self):
        super().__init__()
        self.title("Реакция")
        self.geometry("400x250")
        self.results = []
        self.ready = False
        self.job = None
        self.start_time = 0
        self.label = tk.Label(self, font=("Arial", 22), fg="white")
        self.label.pack(fill="both", expand=True)
        self.label.bind("<Button-1>", self.on_click)
        self.new_round()

    def new_round(self):
        self.ready = False
        self.label.config(text="Ждите...", bg="#d63031")
        self.job = self.after(random.randint(2000, 5000), self.go)

    def go(self):
        self.ready = True
        self.start_time = time.perf_counter()
        self.label.config(text="ЖМИ!", bg="#00b894")

    def on_click(self, event):
        if len(self.results) >= self.ROUNDS:
            return
        if not self.ready:
            if self.job:
                self.after_cancel(self.job)
            self.label.config(text="Поторопились! Ещё раз...")
            self.job = self.after(1000, self.new_round)
            return
        ms = int((time.perf_counter() - self.start_time) * 1000)
        self.results.append(ms)
        self.ready = False
        if len(self.results) < self.ROUNDS:
            self.label.config(text=f"{ms} мс", bg="#0984e3")
            self.job = self.after(1500, self.new_round)
        else:
            avg = sum(self.results) // len(self.results)
            self.label.config(text=f"Лучший: {min(self.results)} мс\nСредний: {avg} мс",
                              bg="#6c5ce7")


if __name__ == "__main__":
    ReactionGame().mainloop()
```

</details>
