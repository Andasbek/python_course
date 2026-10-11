# Сабақ 24. Практикалық тапсырмалар: pack, grid, place

## Базалық деңгей

### 1-тапсырма. Үш жолақ (pack)
Терезеде (400×300) жоғарыда көк тақырып жолағы («Менің қосымшам»), төменде сұр күй жолағы («Дайын»), ортада қалған кеңістікті толық алатын ақ аймақ болсын. Терезені үлкейткенде орта бөлік созылуы керек.

### 2-тапсырма. Батырмалар қатары (pack + Frame)
Терезенің төменгі жағында оң жаққа тураланған «Сақтау», «Болдырмау», «Анықтама» батырмалары тұрсын (Frame ішінде `side="right"`).

### 3-тапсырма. Байланыс формасы (grid)
Кесте түріндегі форма: «Аты», «Email», «Телефон», «Қала» белгілері (сол баған, оңға тураланған) және Entry өрістері (оң баған, ені бойынша созылған). Ең төменгі жолда екі бағанды алатын «Жіберу» батырмасы. Басқанда барлық деректер консольге шықсын.

### 4-тапсырма. Шахмат тақтасы (grid)
8×8 тақта: ақ және қара түсті `Label`-дер (`width=4, height=2`). Ұяшық түсі: `(row + col) % 2 == 0` болса ақ, әйтпесе қоңыр (`"#8b4513"`).

## Кеңейтілген деңгей

### 5-тапсырма. Телефон пернетақтасы (grid)
1–9, `*`, `0`, `#` батырмалары (4 жол × 3 баған) және жоғарыда нөмір көрсететін Entry (3 бағанды алады). Батырма басылғанда сәйкес таңба Entry-ге қосылсын; «⌫» батырмасы соңғы таңбаны өшірсін.

### 6-тапсырма. place: «Сурет үстіндегі жазу»
600×400 терезеде фон ретінде үлкен түсті `Label` (`relwidth=1, relheight=1`), оның ортасында (`relx=0.5, rely=0.4`) ойын атауы, астында «Бастау» батырмасы, ал оң жақ төменгі бұрышта нұсқа нөмірі «v1.0» тұрсын. Терезе өлшемін өзгерткенде элементтер өз орнын (пропорционалды) сақтауы керек.

### 7-тапсырма. Аралас интерфейс
Эскиз бойынша интерфейс жасаңыз:
```text
┌──────────────────────────────────────┐
│        Оқушы карточкасы (pack)       │
├──────────────────────────────────────┤
│ Аты:    [__________]                 │
│ Сынып:  [__________]   (grid, Frame) │
│ Пән:    [__________]                 │
├──────────────────────────────────────┤
│                   [Тазарту] [Сақтау] │  (pack, Frame)
└──────────────────────────────────────┘
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Сабақ кестесі
Апта күндері (Дс–Жм) бағандар, сабақ нөмірлері (1–6) жолдар болатын сабақ кестесін `grid` арқылы жасаңыз. Деректер сөздікте сақталсын (`{"Дс": ["Математика", ...], ...}`). Тақырып жолы мен бірінші баған басқа түспен ерекшеленсін; `columnconfigure(..., weight=1)` арқылы кесте терезеге қарай созылсын. Бос сабақтар «—» болып шықсын.

## Үй тапсырмасы

1. 7-тапсырманы аяқтаңыз; терезені үлкейткенде өрістер созылатындай етіңіз.
2. Өз телефоныңыздағы кез келген қосымшаның (мысалы, калькулятор, сағат) бір экранының эскизін салып, оны Tkinter-де қайталаңыз.
3. Кодты GitHub-қа `gui/lesson24_layout.py` деп жүктеңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Үш жолақ")
root.geometry("400x300")
tk.Label(root, text="Менің қосымшам", bg="#1e3799", fg="white",
         font=("Arial", 14), pady=8).pack(side="top", fill="x")
tk.Label(root, text="Дайын", bg="#d1d8e0", anchor="w", padx=5).pack(side="bottom", fill="x")
tk.Label(root, bg="white").pack(fill="both", expand=True)
root.mainloop()
```

**2-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Батырмалар")
root.geometry("400x200")
bar = tk.Frame(root, pady=8, padx=8)
bar.pack(side="bottom", fill="x")
for text in ["Анықтама", "Болдырмау", "Сақтау"]:
    tk.Button(bar, text=text, width=10).pack(side="right", padx=3)
root.mainloop()
```

**3-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Байланыс")
root.columnconfigure(1, weight=1)
labels = ["Аты", "Email", "Телефон", "Қала"]
entries = {}
for i, name in enumerate(labels):
    tk.Label(root, text=name + ":").grid(row=i, column=0, sticky="e", padx=5, pady=4)
    e = tk.Entry(root)
    e.grid(row=i, column=1, sticky="ew", padx=5, pady=4)
    entries[name] = e


def send():
    for name, e in entries.items():
        print(f"{name}: {e.get()}")


tk.Button(root, text="Жіберу", command=send).grid(
    row=len(labels), column=0, columnspan=2, sticky="ew", padx=5, pady=8)
root.mainloop()
```

**4-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Шахмат")
for row in range(8):
    for col in range(8):
        color = "white" if (row + col) % 2 == 0 else "#8b4513"
        tk.Label(root, bg=color, width=4, height=2).grid(row=row, column=col)
root.mainloop()
```

**5-тапсырма**
```python
import tkinter as tk


def press(ch):
    display.insert(tk.END, ch)


def backspace():
    text = display.get()
    display.delete(0, tk.END)
    display.insert(0, text[:-1])


root = tk.Tk()
root.title("Телефон")
display = tk.Entry(root, font=("Consolas", 18), justify="center")
display.grid(row=0, column=0, columnspan=3, sticky="ew", padx=5, pady=5)
keys = ["1", "2", "3", "4", "5", "6", "7", "8", "9", "*", "0", "#"]
for i, k in enumerate(keys):
    r, c = divmod(i, 3)
    tk.Button(root, text=k, width=5, height=2, font=("Arial", 14),
              command=lambda ch=k: press(ch)).grid(row=r + 1, column=c, padx=2, pady=2)
tk.Button(root, text="⌫", command=backspace).grid(
    row=5, column=0, columnspan=3, sticky="ew", padx=2, pady=2)
root.mainloop()
```

**6-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Ойын")
root.geometry("600x400")
tk.Label(root, bg="#2d3436").place(relx=0, rely=0, relwidth=1, relheight=1)
tk.Label(root, text="ҒАРЫШ САПАРЫ", fg="#ffeaa7", bg="#2d3436",
         font=("Arial", 32, "bold")).place(relx=0.5, rely=0.4, anchor="center")
tk.Button(root, text="Бастау", font=("Arial", 14), width=12).place(
    relx=0.5, rely=0.6, anchor="center")
tk.Label(root, text="v1.0", fg="gray", bg="#2d3436").place(
    relx=1.0, rely=1.0, anchor="se", x=-5, y=-5)
root.mainloop()
```

**7-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Оқушы карточкасы")
tk.Label(root, text="Оқушы карточкасы", font=("Arial", 16, "bold")).pack(pady=8)

form = tk.Frame(root, padx=10)
form.pack(fill="x")
form.columnconfigure(1, weight=1)
entries = []
for i, name in enumerate(["Аты:", "Сынып:", "Пән:"]):
    tk.Label(form, text=name).grid(row=i, column=0, sticky="w", pady=3)
    e = tk.Entry(form)
    e.grid(row=i, column=1, sticky="ew", pady=3, padx=(5, 0))
    entries.append(e)


def clear():
    for e in entries:
        e.delete(0, tk.END)


def save():
    print("Сақталды:", [e.get() for e in entries])


bar = tk.Frame(root, pady=8, padx=10)
bar.pack(fill="x")
tk.Button(bar, text="Сақтау", command=save).pack(side="right")
tk.Button(bar, text="Тазарту", command=clear).pack(side="right", padx=5)
root.mainloop()
```

**8-тапсырма**
```python
import tkinter as tk

DAYS = ["Дс", "Сс", "Ср", "Бс", "Жм"]
SCHEDULE = {
    "Дс": ["Математика", "Қазақ тілі", "Физика", "Ағылшын", "Информатика"],
    "Сс": ["Химия", "Математика", "Тарих", "Дене шынықтыру"],
    "Ср": ["Биология", "Геометрия", "Әдебиет", "Орыс тілі", "Музыка", "Сынып сағаты"],
    "Бс": ["Физика", "Алгебра", "География", "Ағылшын"],
    "Жм": ["Информатика", "Қазақ әдебиеті", "Математика", "Көркем еңбек", "Тарих"],
}
HEAD_BG = "#0984e3"

root = tk.Tk()
root.title("Сабақ кестесі")
tk.Label(root, text="№", bg=HEAD_BG, fg="white", relief="ridge").grid(
    row=0, column=0, sticky="nsew")
for c, day in enumerate(DAYS, start=1):
    root.columnconfigure(c, weight=1)
    tk.Label(root, text=day, bg=HEAD_BG, fg="white", relief="ridge",
             font=("Arial", 11, "bold")).grid(row=0, column=c, sticky="nsew")
for r in range(1, 7):
    tk.Label(root, text=str(r), bg="#dfe6e9", relief="ridge", width=3).grid(
        row=r, column=0, sticky="nsew")
    for c, day in enumerate(DAYS, start=1):
        lessons = SCHEDULE[day]
        text = lessons[r - 1] if r <= len(lessons) else "—"
        tk.Label(root, text=text, relief="ridge", padx=6, pady=4).grid(
            row=r, column=c, sticky="nsew")
root.mainloop()
```

</details>
