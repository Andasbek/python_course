# Сабақ 30. Практикалық тапсырмалар: стильдер, ttk және дизайн

## Базалық деңгей

### 1-тапсырма. «Дизайн-сыншы»
Төмендегі «нашар» интерфейсті іске қосып, дизайн қағидаларының қайсысы бұзылғанын дәптерге жазыңыз (кемінде 5 кемшілік):
```python
import tkinter as tk

root = tk.Tk()
root.title("НАШАР ФОРМА")
root.config(bg="red")
tk.Label(root, text="аты", bg="yellow", fg="white", font=("Comic Sans MS", 8)).pack(anchor="w")
tk.Entry(root, width=10).pack(anchor="e")
tk.Label(root, text="ТЕГІ!!!", bg="green", fg="lime", font=("Impact", 22)).pack()
tk.Entry(root, width=40).pack(anchor="w", pady=30)
tk.Button(root, text="жіберу", bg="blue", fg="red").pack(side="left")
tk.Button(root, text="ТАЗАРТУ", bg="purple", fg="yellow", font=("Arial", 18)).pack(side="right")
root.mainloop()
```

### 2-тапсырма. Қайта жобалау
1-тапсырмадағы форманы `ttk` виджеттері, `grid` және бір палитра арқылы қайта жасаңыз: тақырып, туралаған өрістер, оң жақта «Тазарту» (қарапайым) және «Жіберу» (акцент) батырмалары.

### 3-тапсырма. Тақырып таңдағыш
Combobox арқылы `ttk` тақырыптарын ауыстыратын терезе жасаңыз. Терезеде кемінде 6 түрлі ttk виджеті болсын (Button, Entry, Checkbutton, Radiobutton, Combobox, Progressbar, Scale).

### 4-тапсырма. Жүктелу экраны
`ttk.Progressbar` 0-ден 100-ге дейін толатын «Жүктелуде...» терезесі. Толған соң жазу «Дайын!» болып, «Бастау» батырмасы белсенді болсын (`state=["!disabled"]`).

## Кеңейтілген деңгей

### 5-тапсырма. Баптаулар терезесі (Notebook)
Үш қойындысы бар `ttk.Notebook`:
- «Жалпы» — аты (Entry), тілі (Combobox);
- «Көрініс» — тақырып (Radiobutton: жарық/қараңғы), шрифт өлшемі (`ttk.Spinbox` 10–20);
- «Хабарландыру» — `LabelFrame` ішінде 3 Checkbutton.
Төменде `Separator` және «Сақтау» (акцент) батырмасы — басқанда барлық баптаулар сөздік түрінде консольге шықсын.

### 6-тапсырма. Жарық және қараңғы тақырып
`PALETTES = {"light": {...}, "dark": {...}}` сөздігін жасап, `ttk.Style` арқылы екі тақырыпты қолданатын функция жазыңыз. Батырма басылғанда бүкіл интерфейс (фон, мәтін, Entry, батырмалар) тақырыбын ауыстырсын.

## ★ Олимпиадалық / шығармашылық тапсырма

### 7-тапсырма. Өз «дизайн-жүйеңіз»
`theme.py` модулін жазыңыз: палитра, шрифттер, шегіністер тұрақтылары және `apply_theme(root, mode)` функциясы (`Title.TLabel`, `Muted.TLabel`, `Accent.TButton`, `Danger.TButton`, `Card.TFrame` стильдерін тіркейді). Осы модульді импорттап, 26–28 сабақтардағы өз қосымшаңыздың бірін (мысалы, тіркелу формасы немесе Блокнот) қайта безендіріңіз. «Бұрын / кейін» скриншоттарын салыстырыңыз.

## Үй тапсырмасы

1. Өз «Калькулятор» қосымшаңыздың интерфейс эскизін салыңыз (түстер палитрасымен) — келесі сабақта практикум.
2. coolors.co немесе colorhunt.co сайтынан 4 түстен тұратын палитра таңдап, оны 7-тапсырмадағы `theme.py`-ға енгізіңіз.
3. GitHub-қа `gui/lesson30_style/` папкасы ретінде жүктеңіз.

## Бағалау критерийлері

Өзара бағалау чек-парағы (2-тапсырма немесе 5-тапсырма бойынша, 10 ұпай):

| Критерий | Ұпай |
|---|---|
| Элементтер туралған (бір сызықта) | 2 |
| Біркелкі шегіністер және бос кеңістік | 2 |
| Палитра 3–4 түстен аспайды, контраст жеткілікті | 2 |
| Шрифт иерархиясы (тақырып / мәтін) | 2 |
| Негізгі әрекет (акцент батырма) айқын ерекшеленеді | 2 |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.** Кемшіліктер: контраст жоқ (сары фонда ақ мәтін, жасыл фонда ашық жасыл); туралау жоқ (өрістер әр жақта); біркелкілік жоқ (әртүрлі шрифт, өлшем, регистр); жақындық бұзылған (белгі мен өріс алыс, `pady=30`); тым көп түс (5+ ашық түс); батырмалардың маңыздылығы түсініксіз; кіші шрифт (8 pt); «!!!» және бас әріптер «айқайлайды».

**2-тапсырма**
```python
import tkinter as tk
from tkinter import ttk

BG, ACCENT = "#f5f6fa", "#0984e3"

root = tk.Tk()
root.title("Тіркелу")
root.configure(bg=BG)
s = ttk.Style()
s.theme_use("clam")
s.configure(".", background=BG, font=("Segoe UI", 11))
s.configure("Title.TLabel", font=("Segoe UI", 16, "bold"))
s.configure("Accent.TButton", background=ACCENT, foreground="white")
s.map("Accent.TButton", background=[("active", "#3498db")])

frame = ttk.Frame(root, padding=20)
frame.pack()
frame.columnconfigure(1, weight=1)
ttk.Label(frame, text="Тіркелу", style="Title.TLabel").grid(row=0, column=0, columnspan=2,
                                                            sticky="w", pady=(0, 12))
entries = []
for i, name in enumerate(["Аты", "Тегі"], start=1):
    ttk.Label(frame, text=name).grid(row=i, column=0, sticky="w", pady=6, padx=(0, 12))
    e = ttk.Entry(frame, width=28)
    e.grid(row=i, column=1, sticky="ew", pady=6)
    entries.append(e)
btns = ttk.Frame(frame)
btns.grid(row=3, column=0, columnspan=2, sticky="e", pady=(12, 0))
ttk.Button(btns, text="Тазарту",
           command=lambda: [e.delete(0, tk.END) for e in entries]).pack(side="left", padx=(0, 8))
ttk.Button(btns, text="Жіберу", style="Accent.TButton").pack(side="left")
root.mainloop()
```

**3-тапсырма**
```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Тақырыптар")
style = ttk.Style()
theme = tk.StringVar(value=style.theme_use())
frame = ttk.Frame(root, padding=15)
frame.pack(fill="both", expand=True)
box = ttk.Combobox(frame, textvariable=theme, values=style.theme_names(), state="readonly")
box.pack(fill="x")
box.bind("<<ComboboxSelected>>", lambda e: style.theme_use(theme.get()))
ttk.Separator(frame).pack(fill="x", pady=10)
ttk.Button(frame, text="Батырма").pack(anchor="w")
ttk.Entry(frame).pack(anchor="w", pady=5)
ttk.Checkbutton(frame, text="Жалауша").pack(anchor="w")
choice = tk.StringVar(value="a")
ttk.Radiobutton(frame, text="Нұсқа А", variable=choice, value="a").pack(anchor="w")
ttk.Radiobutton(frame, text="Нұсқа Б", variable=choice, value="b").pack(anchor="w")
ttk.Scale(frame, from_=0, to=100).pack(fill="x", pady=5)
ttk.Progressbar(frame, value=40).pack(fill="x")
root.mainloop()
```

**4-тапсырма**
```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Жүктелу")
frame = ttk.Frame(root, padding=20)
frame.pack()
label = ttk.Label(frame, text="Жүктелуде...")
label.pack()
bar = ttk.Progressbar(frame, length=280, maximum=100)
bar.pack(pady=10)
start_btn = ttk.Button(frame, text="Бастау", state="disabled")
start_btn.pack()


def step(value=0):
    bar["value"] = value
    if value < 100:
        root.after(40, step, value + 2)
    else:
        label.config(text="Дайын!")
        start_btn.state(["!disabled"])


step()
root.mainloop()
```

**5-тапсырма**
```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Баптаулар")
ttk.Style().theme_use("clam")
nb = ttk.Notebook(root)
nb.pack(fill="both", expand=True, padx=10, pady=10)

general = ttk.Frame(nb, padding=12)
view = ttk.Frame(nb, padding=12)
notify = ttk.Frame(nb, padding=12)
nb.add(general, text="Жалпы")
nb.add(view, text="Көрініс")
nb.add(notify, text="Хабарландыру")

name = tk.StringVar()
lang = tk.StringVar(value="Қазақша")
ttk.Label(general, text="Аты:").grid(row=0, column=0, sticky="w", pady=4)
ttk.Entry(general, textvariable=name).grid(row=0, column=1, pady=4, padx=6)
ttk.Label(general, text="Тілі:").grid(row=1, column=0, sticky="w", pady=4)
ttk.Combobox(general, textvariable=lang, values=["Қазақша", "Русский", "English"],
             state="readonly").grid(row=1, column=1, pady=4, padx=6)

theme = tk.StringVar(value="light")
size = tk.IntVar(value=12)
ttk.Radiobutton(view, text="Жарық", variable=theme, value="light").pack(anchor="w")
ttk.Radiobutton(view, text="Қараңғы", variable=theme, value="dark").pack(anchor="w")
ttk.Label(view, text="Шрифт өлшемі:").pack(anchor="w", pady=(8, 0))
ttk.Spinbox(view, from_=10, to=20, textvariable=size, width=5).pack(anchor="w")

group = ttk.LabelFrame(notify, text="Арналар", padding=8)
group.pack(fill="x")
channels = {}
for ch in ["Email", "SMS", "Push"]:
    v = tk.BooleanVar()
    ttk.Checkbutton(group, text=ch, variable=v).pack(anchor="w")
    channels[ch] = v


def save():
    settings = {
        "name": name.get(), "lang": lang.get(), "theme": theme.get(),
        "font_size": size.get(),
        "channels": [ch for ch, v in channels.items() if v.get()],
    }
    print(settings)


ttk.Separator(root).pack(fill="x", padx=10)
ttk.Button(root, text="Сақтау", command=save).pack(anchor="e", padx=10, pady=10)
root.mainloop()
```

**6-тапсырма**
```python
import tkinter as tk
from tkinter import ttk

PALETTES = {
    "light": {"bg": "#f5f6fa", "fg": "#2d3436", "field": "#ffffff", "accent": "#0984e3"},
    "dark": {"bg": "#1e1e2e", "fg": "#f5f5f5", "field": "#2a2a3c", "accent": "#89b4fa"},
}
state = {"mode": "light"}


def apply(mode):
    p = PALETTES[mode]
    root.configure(bg=p["bg"])
    style.configure(".", background=p["bg"], foreground=p["fg"])
    style.configure("TEntry", fieldbackground=p["field"], foreground=p["fg"])
    style.configure("Accent.TButton", background=p["accent"], foreground=p["bg"])


def toggle():
    state["mode"] = "dark" if state["mode"] == "light" else "light"
    apply(state["mode"])


root = tk.Tk()
root.title("Тақырып")
style = ttk.Style()
style.theme_use("clam")
frame = ttk.Frame(root, padding=20)
frame.pack()
ttk.Label(frame, text="Жарық пен қараңғы", font=("Segoe UI", 16, "bold")).pack(pady=5)
ttk.Entry(frame, width=30).pack(pady=5)
ttk.Button(frame, text="Тақырыпты ауыстыру", style="Accent.TButton",
           command=toggle).pack(pady=5)
apply("light")
root.mainloop()
```

**7-тапсырма** (`theme.py` модулі)
```python
"""Үйірме қосымшаларына ортақ дизайн-жүйе."""
from tkinter import ttk

PALETTES = {
    "light": {"bg": "#f5f6fa", "panel": "#ffffff", "text": "#2d3436",
              "muted": "#636e72", "accent": "#0984e3", "danger": "#d63031"},
    "dark": {"bg": "#1e1e2e", "panel": "#2a2a3c", "text": "#f5f5f5",
             "muted": "#a6adc8", "accent": "#89b4fa", "danger": "#f38ba8"},
}
FONT = "Segoe UI"
SPACE = {"s": 4, "m": 8, "l": 16, "xl": 24}


def apply_theme(root, mode="light"):
    p = PALETTES[mode]
    root.configure(bg=p["bg"])
    s = ttk.Style(root)
    s.theme_use("clam")
    s.configure(".", font=(FONT, 11), background=p["bg"], foreground=p["text"])
    s.configure("Card.TFrame", background=p["panel"])
    s.configure("Title.TLabel", font=(FONT, 18, "bold"))
    s.configure("Muted.TLabel", foreground=p["muted"], font=(FONT, 9))
    s.configure("Accent.TButton", background=p["accent"], foreground="white",
                padding=(SPACE["l"], SPACE["s"]))
    s.map("Accent.TButton", background=[("active", p["text"])])
    s.configure("Danger.TButton", background=p["danger"], foreground="white",
                padding=(SPACE["l"], SPACE["s"]))
    return p


if __name__ == "__main__":
    import tkinter as tk
    root = tk.Tk()
    apply_theme(root, "dark")
    ttk.Label(root, text="Дизайн-жүйе", style="Title.TLabel").pack(padx=24, pady=8)
    ttk.Button(root, text="Негізгі", style="Accent.TButton").pack(pady=4)
    ttk.Button(root, text="Өшіру", style="Danger.TButton").pack(pady=(4, 24))
    root.mainloop()
```

</details>
