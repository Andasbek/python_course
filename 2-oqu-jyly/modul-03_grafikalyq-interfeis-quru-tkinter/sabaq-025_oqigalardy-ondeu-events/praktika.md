# Сабақ 25. Практикалық тапсырмалар: оқиғаларды өңдеу

## Базалық деңгей

### 1-тапсырма. Смайлик батырмалары
Үш батырма: «😀», «😐», «😢». Басылған батырманың смайлигі үлкен Label-де шықсын және тақырыпқа «Көңіл-күй: жақсы/қалыпты/нашар» жазылсын. Бір ғана `set_mood(emoji, text)` функциясы және `lambda` қолданыңыз.

### 2-тапсырма. Тінтуір координаттары
400×300 Label аймағында курсор жылжыған сайын (`<Motion>`) төменде «x = ..., y = ...» жаңарып тұрсын. Сол батырманы басқанда аймақ түсі кездейсоқ өзгерсін.

### 3-тапсырма. Enter арқылы есептеу
Entry-ге сан жазылып, **Enter** немесе «Есептеу» батырмасы басылғанда санның факториалы шықсын (`math.factorial`). Бір ғана функция (`event=None`).

### 4-тапсырма. Секундомер
«Бастау», «Тоқтату», «Нөлдеу» батырмалары бар секундомер (`after(100, ...)`, ондық секундпен: `12.3 с`).

## Кеңейтілген деңгей

### 5-тапсырма. Перне тренажері
Терезеде кездейсоқ әріп шығады (`random.choice("ASDFJKL")`). Пайдаланушы сол пернені басса — ұпай +1 және жаңа әріп; қате болса — ұпай −1 және экран бір сәтке қызарады (`after(200, ...)` арқылы қайта ақ түске). 30 секундтан кейін ойын аяқталып, нәтиже шығады.

### 6-тапсырма. Батырма «қашады»
`place` арқылы орналасқан «Мені бас!» батырмасы. Курсор батырмаға кірген сәтте (`<Enter>`) батырма терезенің кездейсоқ жеріне ауысады. Пайдаланушы бәрібір басып үлгерсе — «Жеңдіңіз!» (Кеңес: `Tab` және `space` арқылы басуға болады.)

### 7-тапсырма. Ыстық пернелер
Мәтіндік өріс (Entry) және жазу. Пернелер тіркесімі:
- `Ctrl+U` — мәтінді бас әріпке;
- `Ctrl+L` — кіші әріпке;
- `Ctrl+D` — өрісті тазарту;
- `Esc` — бағдарламадан шығу.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. «Реакция» ойыны
Терезе қызыл түсте «Күтіңіз...» дейді. 2–5 секундтан кейін (кездейсоқ) жасыл болып «БАС!» жазады. Пайдаланушы басқанша өткен уақыт (мс) есептеледі (`time.perf_counter()`). Ерте басса — «Асығыс!». 5 әрекеттің ең жақсы және орташа нәтижесі көрсетілсін. Ойынды `tk.Tk`-тан мұра алатын класс түрінде жазыңыз.

## Үй тапсырмасы

1. 4-тапсырмадағы секундомерге «Айналым» (lap) батырмасын қосыңыз: басқан сайын ағымдағы уақыт төмендегі Label-ге жаңа жол болып қосылсын.
2. 24-сабақтағы телефон/калькулятор пернетақтасын пернетақтадан да басқаруға болатындай етіңіз (`root.bind("<Key>", ...)`).
3. GitHub-қа `gui/lesson25_events.py` деп жүктеңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import tkinter as tk


def set_mood(emoji, text):
    face.config(text=emoji)
    root.title(f"Көңіл-күй: {text}")


root = tk.Tk()
root.title("Көңіл-күй")
face = tk.Label(root, text="?", font=("Arial", 60))
face.pack(padx=40, pady=10)
for emoji, text in [("😀", "жақсы"), ("😐", "қалыпты"), ("😢", "нашар")]:
    tk.Button(root, text=emoji, font=("Arial", 20),
              command=lambda e=emoji, t=text: set_mood(e, t)).pack(side="left", padx=10, pady=10)
root.mainloop()
```

**2-тапсырма**
```python
import random
import tkinter as tk


def on_move(event):
    coords.config(text=f"x = {event.x}, y = {event.y}")


def on_click(event):
    area.config(bg="#%06x" % random.randint(0, 0xFFFFFF))


root = tk.Tk()
root.title("Координаттар")
area = tk.Frame(root, width=400, height=300, bg="white")
area.pack()
coords = tk.Label(root, text="x = 0, y = 0")
coords.pack(pady=5)
area.bind("<Motion>", on_move)
area.bind("<Button-1>", on_click)
root.mainloop()
```
(Frame өлшемі пиксельмен беріледі, сондықтан мұнда Label-ден гөрі ыңғайлы.)

**3-тапсырма**
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
        result.config(text="Теріс емес бүтін сан енгізіңіз")


root = tk.Tk()
root.title("Факториал")
entry = tk.Entry(root, justify="center")
entry.pack(padx=20, pady=10)
entry.focus()
entry.bind("<Return>", calc)
tk.Button(root, text="Есептеу", command=calc).pack()
result = tk.Label(root, text="", wraplength=300)
result.pack(pady=10)
root.mainloop()
```

**4-тапсырма** (үй тапсырмасындағы «Айналым» қосылған)
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
for text, cmd in [("Бастау", start), ("Тоқтату", stop), ("Нөлдеу", reset), ("Айналым", lap)]:
    tk.Button(bar, text=text, command=cmd, width=8).pack(side="left", padx=2)
laps = tk.Label(root, text="", justify="left")
laps.pack(pady=5)
root.mainloop()
```

**5-тапсырма**
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
    score_lbl.config(text=f"Ұпай: {state['score']}")


def tick():
    state["time"] -= 1
    time_lbl.config(text=f"Уақыт: {state['time']}")
    if state["time"] > 0:
        root.after(1000, tick)
    else:
        state["over"] = True
        letter_lbl.config(text="Бітті!")


root = tk.Tk()
root.title("Перне тренажері")
root.config(bg="white")
letter_lbl = tk.Label(root, text=state["letter"], font=("Arial", 72), bg="white")
letter_lbl.pack(padx=60, pady=10)
score_lbl = tk.Label(root, text="Ұпай: 0", bg="white")
score_lbl.pack()
time_lbl = tk.Label(root, text="Уақыт: 30", bg="white")
time_lbl.pack(pady=5)
root.bind("<Key>", on_key)
root.after(1000, tick)
root.mainloop()
```

**6-тапсырма**
```python
import random
import tkinter as tk


def run_away(event):
    btn.place(x=random.randint(0, 300), y=random.randint(0, 250))


def win():
    btn.config(text="Жеңдіңіз!", state="disabled")


root = tk.Tk()
root.title("Қашқын батырма")
root.geometry("400x300")
btn = tk.Button(root, text="Мені бас!", command=win)
btn.place(x=150, y=130)
btn.bind("<Enter>", run_away)
root.mainloop()
```

**7-тапсырма**
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
root.title("Ыстық пернелер")
entry = tk.Entry(root, width=40)
entry.pack(padx=10, pady=10)
entry.focus()
tk.Label(root, text="Ctrl+U — БАС, Ctrl+L — кіші, Ctrl+D — тазарту, Esc — шығу").pack(pady=5)
root.bind("<Control-u>", upper)
root.bind("<Control-l>", lower)
root.bind("<Control-d>", clear)
root.bind("<Escape>", lambda e: root.destroy())
root.mainloop()
```

**8-тапсырма**
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
        self.label.config(text="Күтіңіз...", bg="#d63031")
        self.job = self.after(random.randint(2000, 5000), self.go)

    def go(self):
        self.ready = True
        self.start_time = time.perf_counter()
        self.label.config(text="БАС!", bg="#00b894")

    def on_click(self, event):
        if len(self.results) >= self.ROUNDS:
            return
        if not self.ready:
            if self.job:
                self.after_cancel(self.job)
            self.label.config(text="Асығыс! Қайтадан...")
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
            self.label.config(text=f"Ең жақсы: {min(self.results)} мс\nОрташа: {avg} мс",
                              bg="#6c5ce7")


if __name__ == "__main__":
    ReactionGame().mainloop()
```

</details>
