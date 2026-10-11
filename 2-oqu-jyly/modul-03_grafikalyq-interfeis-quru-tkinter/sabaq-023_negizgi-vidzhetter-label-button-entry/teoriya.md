# Сабақ 23. Негізгі виджеттер: Label, Button, Entry.

## Тақырыптар

1. Виджеттердің ортақ қасиеттері
2. Label — жазу
3. Button — батырма
4. Entry — бір жолдық енгізу өрісі
5. Сандармен жұмыс: деректерді тексеру
6. Басқару айнымалылары: `StringVar`, `IntVar`
7. Барлығы бірге: «Кіру» формасы
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Виджеттердің ортақ қасиеттері

Барлық виджет құрылғанда бірінші аргумент — **ата-виджет** (master), содан кейін кілттік аргументтер (опциялар):

```text
виджет = tk.ВиджетАты(ата_виджет, опция1=мән1, опция2=мән2, ...)
```

| Опция | Мағынасы | Мысал |
|---|---|---|
| `text` | Мәтін | `text="Сәлем"` |
| `font` | Шрифт: (атауы, өлшемі, стилі) | `font=("Arial", 14, "bold")` |
| `fg` | Мәтін түсі (foreground) | `fg="white"` |
| `bg` | Фон түсі (background) | `bg="#2e86de"` |
| `width`, `height` | Өлшемі (**мәтіндік бірлікпен** — таңбалар/жолдар саны) | `width=20` |
| `padx`, `pady` | Ішкі шегініс | `padx=10` |
| `relief` | Жиек стилі: `flat`, `raised`, `sunken`, `groove`, `ridge`, `solid` | `relief="groove"` |
| `bd` / `borderwidth` | Жиек қалыңдығы | `bd=2` |
| `cursor` | Тінтуір курсоры | `cursor="hand2"` |

Қасиетті өзгерту: `widget.config(опция=мән)`, оқу: `widget.cget("опция")` немесе `widget["опция"]`.

## 2. Label — жазу

```python
import tkinter as tk

root = tk.Tk()
root.title("Label мысалдары")

tk.Label(root, text="Қарапайым жазу").pack()
tk.Label(root, text="Үлкен қалың жазу", font=("Arial", 20, "bold")).pack()
tk.Label(root, text="Түрлі түсті", fg="white", bg="#e74c3c", padx=10, pady=5).pack(pady=5)
tk.Label(root, text="Жиекті жазу", relief="ridge", bd=3, width=25).pack(pady=5)
tk.Label(root, text="Ұзын мәтін автоматты түрде жаңа жолға ауысады, "
                    "себебі wraplength қасиеті берілген.",
         wraplength=200, justify="left").pack(pady=5)
tk.Label(root, text="Солға тураланған", width=30, anchor="w", bg="lightyellow").pack()

root.mainloop()
```

| Опция | Мағынасы |
|---|---|
| `wraplength` | Мәтін осы ені (пиксель) асқанда жаңа жолға көшеді |
| `justify` | Көп жолды мәтінді туралау: `left`, `center`, `right` |
| `anchor` | Мәтіннің виджет ішіндегі орны: `w` (батыс/сол), `e` (оң), `n`, `s`, `center`, `nw`... |

### Label мәтінін өзгерту

```python
import tkinter as tk
import time


def show_time():
    clock.config(text=time.strftime("%H:%M:%S"))


root = tk.Tk()
root.title("Сағат")
clock = tk.Label(root, text="--:--:--", font=("Consolas", 30))
clock.pack(padx=20, pady=10)
tk.Button(root, text="Уақытты көрсету", command=show_time).pack(pady=5)
root.mainloop()
```

## 3. Button — батырма

```python
import tkinter as tk

clicks = 0


def on_click():
    global clicks
    clicks += 1
    info.config(text=f"Басылды: {clicks} рет")
    if clicks >= 5:
        btn.config(state="disabled", text="Шек бітті")


root = tk.Tk()
root.title("Button")
info = tk.Label(root, text="Басылды: 0 рет", font=("Arial", 14))
info.pack(pady=10)
btn = tk.Button(root, text="Бас мені!", command=on_click,
                bg="#27ae60", fg="white", activebackground="#2ecc71",
                font=("Arial", 12), width=15, cursor="hand2")
btn.pack(pady=10)
root.mainloop()
```

| Опция | Мағынасы |
|---|---|
| `command` | Басқанда шақырылатын функция (жақшасыз) |
| `state` | `"normal"` — белсенді, `"disabled"` — белсенді емес (сұр) |
| `activebackground`, `activeforeground` | Басып тұрғандағы түстер |
| `width` | Ені (таңбамен) |

Ескерту: macOS-та `Button` фон түсі (`bg`) жүйелік стильге байланысты көрінбеуі мүмкін — бұл қалыпты жағдай; барлық жүйеде бірдей көрініс үшін `ttk.Style` стильдері бар `ttk.Button` қолданылады.

## 4. Entry — бір жолдық енгізу өрісі

```python
import tkinter as tk


def greet():
    name = entry.get().strip()          # өрістегі мәтінді алу
    if name:
        result.config(text=f"Сәлем, {name}!", fg="green")
    else:
        result.config(text="Атыңызды енгізіңіз!", fg="red")


def clear():
    entry.delete(0, tk.END)             # өрісті тазарту
    result.config(text="")
    entry.focus()                       # курсорды өріске қою


root = tk.Tk()
root.title("Entry")
tk.Label(root, text="Атыңыз:").pack(pady=(10, 0))
entry = tk.Entry(root, width=25, font=("Arial", 12))
entry.pack(padx=20, pady=5)
entry.insert(0, "Ару")                  # бастапқы мәтін
entry.focus()
tk.Button(root, text="Сәлемдесу", command=greet).pack(pady=5)
tk.Button(root, text="Тазарту", command=clear).pack(pady=5)
result = tk.Label(root, text="", font=("Arial", 14))
result.pack(pady=5)
root.mainloop()
```

### Entry негізгі әдістері

| Әдіс | Мағынасы |
|---|---|
| `get()` | Өрістегі мәтінді **жол** түрінде қайтарады |
| `insert(index, text)` | `index` орнына мәтін қояды (`0` — басына, `tk.END` — соңына) |
| `delete(first, last)` | Мәтінді өшіреді; `delete(0, tk.END)` — толық тазарту |
| `focus()` | Курсорды өріске қояды |

### Entry опциялары

| Опция | Мағынасы |
|---|---|
| `show="*"` | Құпия сөз өрісі: енгізілген таңбалар `*` болып көрінеді |
| `width` | Ені (таңбамен) |
| `state="readonly"` | Тек оқу үшін |
| `justify="center"` | Мәтінді туралау |

## 5. Сандармен жұмыс: деректерді тексеру

`get()` әрқашан **жол** қайтарады! Санға айналдыру керек және қате енгізуді ұстау қажет:

```python
import tkinter as tk


def add():
    try:
        a = float(entry_a.get())
        b = float(entry_b.get())
    except ValueError:
        result.config(text="Қате: тек сан енгізіңіз!", fg="red")
        return
    result.config(text=f"Қосындысы: {a + b:g}", fg="black")


root = tk.Tk()
root.title("Қосу")
entry_a = tk.Entry(root, width=10, justify="center")
entry_a.pack(side="left", padx=5, pady=10)
tk.Label(root, text="+").pack(side="left")
entry_b = tk.Entry(root, width=10, justify="center")
entry_b.pack(side="left", padx=5)
tk.Button(root, text="=", command=add).pack(side="left", padx=5)
result = tk.Label(root, text="", width=25)
result.pack(side="left", padx=5)
root.mainloop()
```

`:g` пішімі артық нөлдерді алып тастайды: `5.0` → `5`, `2.5` → `2.5`.

## 6. Басқару айнымалылары: `StringVar`, `IntVar`

Tkinter-де виджетпен **автоматты байланысатын** арнайы айнымалылар бар:

| Класс | Сақтайтын типі |
|---|---|
| `tk.StringVar()` | жол |
| `tk.IntVar()` | бүтін сан |
| `tk.DoubleVar()` | бөлшек сан |
| `tk.BooleanVar()` | `True`/`False` |

Әдістері: `.get()` — мәнін алу, `.set(мән)` — мәнін орнату. Виджетке `textvariable=` арқылы байланыстырамыз:

```python
import tkinter as tk


def to_upper():
    text_var.set(text_var.get().upper())


root = tk.Tk()
root.title("StringVar")
text_var = tk.StringVar(value="алматы")

tk.Entry(root, textvariable=text_var, width=25).pack(padx=20, pady=5)
tk.Label(root, textvariable=text_var, font=("Arial", 16), fg="blue").pack(pady=5)
tk.Button(root, text="БАС ӘРІППЕН", command=to_upper).pack(pady=5)
root.mainloop()
```

Өріске жазған сайын `Label` мәтіні **бірден** өзгереді — себебі екеуі бір айнымалыға байланған. (Ескерту: `StringVar` тек `Tk()` құрылғаннан кейін жасалады.)

## 7. Барлығы бірге: «Кіру» формасы

```python
import tkinter as tk

USERS = {"aru": "1234", "erlan": "qwerty"}   # тек оқу мақсатындағы мысал!


def login():
    user = login_var.get().strip().lower()
    password = pass_entry.get()
    if USERS.get(user) == password:
        status.config(text=f"Қош келдіңіз, {user}!", fg="green")
        login_btn.config(state="disabled")
    else:
        status.config(text="Логин немесе құпия сөз қате", fg="red")
        pass_entry.delete(0, tk.END)


root = tk.Tk()
root.title("Кіру")
root.geometry("280x200")
root.resizable(False, False)

login_var = tk.StringVar()
tk.Label(root, text="Логин:").pack(pady=(15, 0))
tk.Entry(root, textvariable=login_var).pack()
tk.Label(root, text="Құпия сөз:").pack(pady=(10, 0))
pass_entry = tk.Entry(root, show="*")
pass_entry.pack()
login_btn = tk.Button(root, text="Кіру", width=12, command=login)
login_btn.pack(pady=10)
status = tk.Label(root, text="")
status.pack()
root.mainloop()
```

Қауіпсіздік ескертпесі: нақты қосымшаларда құпия сөздер кодта ашық сақталмайды — олар хэштеліп, дерекқорда сақталады.

## Жиі кездесетін қателер

**1. `pack()` нәтижесін айнымалыға сақтау**

```python
import tkinter as tk
root = tk.Tk()
entry = tk.Entry(root).pack()     # ҚАТЕ: pack() None қайтарады!
# entry.get()  -> AttributeError: 'NoneType' object has no attribute 'get'
root.destroy()
```

Дұрысы — екі жолға бөлу:

```python
import tkinter as tk
root = tk.Tk()
entry = tk.Entry(root)
entry.pack()
root.destroy()
```

**2. `get()` нәтижесін санға айналдырмау** — `entry.get() + 5` → `TypeError`.

**3. `delete(0)` деп жазу** — тек бірінші таңба өшеді. Толық тазалау: `delete(0, tk.END)`.

**4. Функцияны батырма құрылмай тұрып шақыру / өріс атауын функция ішінде қате жазу** — `NameError`. Виджет айнымалыларын функцияны шақырғанға дейін құрыңыз (`mainloop` алдында).

**5. Бос өрісті тексермеу** — `float("")` → `ValueError`. `try/except` немесе `if not text:` қолданыңыз.

**6. `StringVar`-ды `Tk()`-ге дейін құру** → `RuntimeError: Too early to create variable`.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| `Label` | Мәтін (немесе сурет) көрсететін виджет |
| `Button` | Басқанда `command` функциясын шақыратын батырма |
| `Entry` | Бір жолдық мәтін енгізу өрісі |
| `get / insert / delete` | Entry мәтінін оқу, қою, өшіру әдістері |
| `tk.END` | Мәтіннің соңын білдіретін тұрақты |
| `StringVar`, `IntVar` | Виджетпен байланысатын басқару айнымалылары |
| `textvariable` | Виджетті басқару айнымалысымен байланыстыру опциясы |
| `state` | Виджет күйі: `normal`, `disabled`, `readonly` |

## Ескерту

- Байланысқан өрісте сан емес мәтін жазылса, `IntVar.get()` пен `DoubleVar.get()` `TclError` лақтырады — шақыруды `try/except` ішіне орап жазыңыз.
- Қаріпті кортежбен беру ыңғайлы: `("Segoe UI", 12, "bold")`. Жол түрінде берсеңіз, бос орны бар атауды фигуралық жақшаға алу керек: `"{Segoe UI} 12 bold"`.
- Енгізуді «бірден» тексеру үшін `Entry`-де `validate` және `validatecommand` опциялары бар (мысалы, тек цифрларға рұқсат беру).
- Өңдеушілерде `global` шағын скриптерде рұқсат; үлкен бағдарламаларда күйді класс атрибуттарында сақтаған дұрыс.
- Құпиясөздерді ешқашан ашық мәтінмен сақтамайды: хэштеу үшін `hashlib` (мысалы, `hashlib.pbkdf2_hmac`), кездейсоқ токендер үшін `secrets` модулі қолданылады.
- Құжаттама: https://docs.python.org/3/library/tkinter.html
