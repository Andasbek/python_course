# Сабақ 23. Практикалық тапсырмалар: Label, Button, Entry

## Базалық деңгей

### 1-тапсырма. Сәлемдесу
Entry өрісіне аты енгізіліп, «Сәлемдесу» басылғанда Label-де «Сәлем, <аты>! Python үйірмесіне қош келдіңіз!» шықсын. Өріс бос болса — қызыл түспен «Атыңызды жазыңыз».

### 2-тапсырма. Теңгені долларға айырбастау
Entry — теңге сомасы, Button — «Айырбастау», Label — нәтиже. Бағам кодта тұрақты ретінде берілсін (мысалы, `RATE = 520` тг/$). Нәтиже екі ондық белгімен: «10000 тг = 19.23 $». Сан емес енгізілсе — қате туралы хабарлама.

### 3-тапсырма. Квадрат пен куб
Entry-ге бүтін сан енгізіледі. Екі батырма: «Квадраты» және «Кубы». Нәтиже бір Label-де шығады.

Мысал: `7` → «7² = 49» немесе «7³ = 343».

### 4-тапсырма. Құпия сөз
«Құпия сөз» өрісі (`show="*"`) және «Көрсету/Жасыру» батырмасы. Батырма басылғанда өрістегі таңбалар көрінсін/жасырылсын (`entry.config(show="")`).

## Кеңейтілген деңгей

### 5-тапсырма. Құпия сөздің сенімділігі
Енгізілген құпия сөзді тексеретін батырма. Ережелер: ұзындығы ≥ 8, кемінде бір цифр, кемінде бір бас әріп, кемінде бір арнайы таңба (`!@#$%`). Нәтиже: «Әлсіз» (қызыл), «Орташа» (қызғылт сары), «Күшті» (жасыл) — орындалған ережелер санына қарай (0–2, 3, 4).

### 6-тапсырма. «Тапқыр» ойыны
Бағдарлама 1-ден 100-ге дейінгі кездейсоқ сан ойлайды. Пайдаланушы Entry-ге сан жазып, «Тексеру» басады. Жауап: «Көбірек», «Азырақ» немесе «Таптыңыз! N әрекетпен». Тапқанда «Тексеру» батырмасы `disabled` болсын, ал «Жаңа ойын» батырмасы ойынды қайта бастасын.

### 7-тапсырма. StringVar арқылы тірі санауыш
Entry-ге мәтін жазылған сайын төменде «Таңбалар: N, сөздер: M» автоматты жаңарып тұрсын. Кеңес: `var.trace_add("write", func)` — айнымалы өзгерген сайын `func(*args)` шақырылады.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. ЖСН (ИИН) тексерушісі
Қазақстан азаматының ЖСН-і 12 цифрдан тұрады: алғашқы 6 цифр — туған күні (ЖЖААКК), 7-цифр — ғасыр мен жынысты білдіреді (3 — ХХ ғ. ер, 4 — ХХ ғ. әйел, 5 — ХХІ ғ. ер, 6 — ХХІ ғ. әйел). Entry-ге енгізілген ЖСН бойынша туған күнін (КК.АА.ЖЖЖЖ) және жынысын анықтайтын GUI жазыңыз. Енгізу 12 цифр болмаса — қате. (Бақылау цифрын тексеру — қосымша ұпай.) Ескерту: тек ойдан шығарылған ЖСН-дермен тексеріңіз!

## Үй тапсырмасы

1. 6-тапсырманы аяқтаңыз және GitHub-қа `gui/lesson23_guess.py` деп жүктеңіз.
2. Өз ойыңыздан «Анкета» терезесін жасаңыз: аты, тегі, жасы, қаласы өрістері және «Жіберу» батырмасы — барлық деректер төмендегі Label-де бір мәтін болып шықсын.
3. Ойланыңыз: виджеттер бір-бірінің астына тізілгені ыңғайсыз. Оларды кесте түрінде қалай орналастыруға болады?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import tkinter as tk


def greet():
    name = entry.get().strip()
    if name:
        result.config(text=f"Сәлем, {name}! Python үйірмесіне қош келдіңіз!", fg="black")
    else:
        result.config(text="Атыңызды жазыңыз", fg="red")


root = tk.Tk()
root.title("Сәлемдесу")
entry = tk.Entry(root, width=30)
entry.pack(padx=20, pady=10)
tk.Button(root, text="Сәлемдесу", command=greet).pack()
result = tk.Label(root, text="")
result.pack(pady=10)
root.mainloop()
```

**2-тапсырма**
```python
import tkinter as tk

RATE = 520


def convert():
    try:
        kzt = float(entry.get())
    except ValueError:
        result.config(text="Сома сан болуы керек", fg="red")
        return
    result.config(text=f"{kzt:g} тг = {kzt / RATE:.2f} $", fg="black")


root = tk.Tk()
root.title("Айырбастау")
tk.Label(root, text=f"Бағам: 1 $ = {RATE} тг").pack(pady=5)
entry = tk.Entry(root, justify="center")
entry.pack(padx=20)
tk.Button(root, text="Айырбастау", command=convert).pack(pady=5)
result = tk.Label(root, text="", font=("Arial", 14))
result.pack(pady=5)
root.mainloop()
```

**3-тапсырма**
```python
import tkinter as tk


def read_int():
    try:
        return int(entry.get())
    except ValueError:
        result.config(text="Бүтін сан енгізіңіз")
        return None


def square():
    n = read_int()
    if n is not None:
        result.config(text=f"{n}² = {n ** 2}")


def cube():
    n = read_int()
    if n is not None:
        result.config(text=f"{n}³ = {n ** 3}")


root = tk.Tk()
root.title("Дәреже")
entry = tk.Entry(root, justify="center")
entry.pack(pady=10, padx=20)
tk.Button(root, text="Квадраты", command=square).pack(side="left", padx=10, pady=5)
tk.Button(root, text="Кубы", command=cube).pack(side="left", padx=10, pady=5)
result = tk.Label(root, text="", font=("Arial", 16))
result.pack(side="left", padx=10)
root.mainloop()
```

**4-тапсырма**
```python
import tkinter as tk


def toggle():
    if entry.cget("show") == "*":
        entry.config(show="")
        btn.config(text="Жасыру")
    else:
        entry.config(show="*")
        btn.config(text="Көрсету")


root = tk.Tk()
root.title("Құпия сөз")
entry = tk.Entry(root, show="*", width=25)
entry.pack(side="left", padx=10, pady=20)
btn = tk.Button(root, text="Көрсету", command=toggle, width=8)
btn.pack(side="left", padx=10)
root.mainloop()
```

**5-тапсырма**
```python
import tkinter as tk


def check():
    p = entry.get()
    rules = [
        len(p) >= 8,
        any(ch.isdigit() for ch in p),
        any(ch.isupper() for ch in p),
        any(ch in "!@#$%" for ch in p),
    ]
    score = sum(rules)
    if score == 4:
        result.config(text="Күшті", fg="green")
    elif score == 3:
        result.config(text="Орташа", fg="orange")
    else:
        result.config(text="Әлсіз", fg="red")


root = tk.Tk()
root.title("Сенімділік")
entry = tk.Entry(root, show="*", width=25)
entry.pack(padx=20, pady=10)
tk.Button(root, text="Тексеру", command=check).pack()
result = tk.Label(root, text="", font=("Arial", 16, "bold"))
result.pack(pady=10)
root.mainloop()
```

**6-тапсырма**
```python
import random
import tkinter as tk

state = {"secret": random.randint(1, 100), "tries": 0}


def check():
    try:
        n = int(entry.get())
    except ValueError:
        info.config(text="Сан енгізіңіз!")
        return
    state["tries"] += 1
    if n < state["secret"]:
        info.config(text="Көбірек")
    elif n > state["secret"]:
        info.config(text="Азырақ")
    else:
        info.config(text=f"Таптыңыз! {state['tries']} әрекетпен")
        check_btn.config(state="disabled")
    entry.delete(0, tk.END)


def new_game():
    state["secret"] = random.randint(1, 100)
    state["tries"] = 0
    info.config(text="1-ден 100-ге дейінгі санды тап")
    check_btn.config(state="normal")


root = tk.Tk()
root.title("Тапқыр")
info = tk.Label(root, text="1-ден 100-ге дейінгі санды тап", font=("Arial", 13))
info.pack(pady=10, padx=20)
entry = tk.Entry(root, justify="center")
entry.pack()
check_btn = tk.Button(root, text="Тексеру", command=check)
check_btn.pack(pady=5)
tk.Button(root, text="Жаңа ойын", command=new_game).pack(pady=5)
root.mainloop()
```

**7-тапсырма**
```python
import tkinter as tk


def update(*args):
    text = var.get()
    stats.config(text=f"Таңбалар: {len(text)}, сөздер: {len(text.split())}")


root = tk.Tk()
root.title("Санауыш")
var = tk.StringVar()
var.trace_add("write", update)
tk.Entry(root, textvariable=var, width=40).pack(padx=10, pady=10)
stats = tk.Label(root, text="Таңбалар: 0, сөздер: 0")
stats.pack(pady=5)
root.mainloop()
```

**8-тапсырма**
```python
import tkinter as tk

CENTURY = {"1": (1800, "ер"), "2": (1800, "әйел"), "3": (1900, "ер"),
           "4": (1900, "әйел"), "5": (2000, "ер"), "6": (2000, "әйел")}


def control_digit(iin):
    d = [int(c) for c in iin]
    w1 = list(range(1, 12))
    s = sum(a * b for a, b in zip(d[:11], w1)) % 11
    if s == 10:
        w2 = [3, 4, 5, 6, 7, 8, 9, 10, 11, 1, 2]
        s = sum(a * b for a, b in zip(d[:11], w2)) % 11
    return s


def check():
    iin = entry.get().strip()
    if len(iin) != 12 or not iin.isdigit():
        result.config(text="ЖСН 12 цифрдан тұруы керек", fg="red")
        return
    if iin[6] not in CENTURY:
        result.config(text="7-цифр қате", fg="red")
        return
    base, gender = CENTURY[iin[6]]
    year = base + int(iin[0:2])
    month, day = iin[2:4], iin[4:6]
    ok = "дұрыс" if control_digit(iin) == int(iin[11]) else "сәйкес емес"
    result.config(text=f"Туған күні: {day}.{month}.{year}\nЖынысы: {gender}\n"
                       f"Бақылау цифры: {ok}", fg="black")


root = tk.Tk()
root.title("ЖСН тексеру")
entry = tk.Entry(root, width=20, justify="center", font=("Consolas", 14))
entry.pack(padx=20, pady=10)
tk.Button(root, text="Тексеру", command=check).pack()
result = tk.Label(root, text="", justify="left")
result.pack(pady=10)
root.mainloop()
```

</details>
