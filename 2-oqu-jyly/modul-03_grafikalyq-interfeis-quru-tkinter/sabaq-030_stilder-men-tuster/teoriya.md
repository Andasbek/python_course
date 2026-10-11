# Сабақ 30. Стильдер мен түстер. ttk модулі. Интерфейс дизайнының негіздері.

## Тақырыптар

1. Неге дизайн маңызды?
2. Интерфейс дизайнының 5 негізгі қағидасы
3. Түстер
4. Шрифттер
5. ttk модулі: тақырыпталған виджеттер
6. Тақырыптар (themes)
7. ttk.Style — өз стильдеріңіз
8. Пайдалы ttk виджеттері
9. Үлгі: стильді «Профиль» терезесі
10. Жиі кездесетін қателер
11. Негізгі ұғымдар

## 1. Неге дизайн маңызды?

Пайдаланушы бағдарламаны оның **интерфейсі** арқылы бағалайды. Ең мықты алгоритм де түсініксіз, «жамаулы» терезеде құнын жоғалтады. Жақсы интерфейс:
- **түсінікті** — нұсқаусыз қолдануға болады;
- **ыңғайлы** — аз әрекетпен мақсатқа жетеді;
- **әдемі** — көзге жағымды, кәсіби көрінеді;
- **қолжетімді** — көру қабілеті нашар адамдар да қолдана алады.

## 2. Интерфейс дизайнының 5 негізгі қағидасы

| Қағида | Мәні | Tkinter-дегі іске асыру |
|---|---|---|
| **Контраст** | Мәтін фоннан анық ажыратылуы керек; маңызды элемент ерекшеленуі керек | Ашық фон + қараңғы мәтін; негізгі батырма — ашық түсті |
| **Туралау** | Элементтер бір сызық бойымен тұрады | `grid` + `sticky`, `anchor="w"` |
| **Біркелкілік** | Бірдей элементтер бірдей көрінеді | Бір стиль, бір шегініс (8/16 px), бір шрифт |
| **Жақындық** | Байланысты элементтер жақын, басқалары алыс | `Frame`/`LabelFrame` топтау, `pady` айырмашылығы |
| **Бос кеңістік** | Элементтерге «тыныс алу» орны керек | `padx`, `pady`, `ipadx` |

### Нашар және жақсы мысал

```text
НАШАР                                  ЖАҚСЫ
┌──────────────────────────┐           ┌──────────────────────────┐
│Аты:[_______]             │           │  Тіркелу                 │
│   Тегі:[____________]    │           │                          │
│Email:[___]  [ЖІБЕРУ!!!]  │           │  Аты     [_____________] │
│ [Тазарту]                │           │  Тегі    [_____________] │
│ (қызыл фонда жасыл мәтін)│           │  Email   [_____________] │
└──────────────────────────┘           │                          │
                                       │       [Тазарту] [Жіберу] │
                                       └──────────────────────────┘
```

## 3. Түстер

### 60–30–10 ережесі

- **60%** — негізгі (фон) түс: бейтарап, ашық немесе қараңғы;
- **30%** — қосымша түс: панельдер, тақырыптар;
- **10%** — акцент түс: негізгі батырма, маңызды белгілер.

### Үлгі палитралар

| Рөлі | Жарық тақырып | Қараңғы тақырып |
|---|---|---|
| Фон | `#F5F6FA` | `#1E1E2E` |
| Панель | `#FFFFFF` | `#2A2A3C` |
| Мәтін | `#2D3436` | `#F5F5F5` |
| Әлсіз мәтін | `#636E72` | `#A6ADC8` |
| Акцент | `#0984E3` | `#89B4FA` |
| Сәтті | `#00B894` | `#A6E3A1` |
| Қате | `#D63031` | `#F38BA8` |

Кеңестер:
- 3–4 түстен артық қолданбаңыз;
- таза қызыл/жасыл/көкті (`#FF0000`) көп қолданбаңыз — көзді шаршатады;
- түстерді **тұрақтылар** ретінде бір жерде сақтаңыз (сөздік немесе класс) — сонда тақырыпты оңай ауыстырасыз.

### Қолжетімділік

- Мәтін мен фонның контрасты жоғары болсын (ақ фонда ашық сары мәтін — нашар).
- Ақпаратты **тек түспен** бермеңіз: «Қате» сөзін де жазыңыз (түсті ажырата алмайтын адамдар бар).
- Шрифт өлшемі 10 pt-тен кіші болмасын.

## 4. Шрифттер

- Бір қосымшада 1–2 шрифт отбасы жеткілікті.
- Иерархия: тақырып (18–24 pt, қалың), ішкі тақырып (13–15 pt), негізгі мәтін (10–12 pt).
- Кириллица мен қазақ әріптерін (ә, ғ, қ, ң, ө, ұ, ү, һ, і) қолдайтын шрифт таңдаңыз: `Arial`, `Segoe UI`, `Calibri`, `Verdana`, `DejaVu Sans`.
- Код/сандар үшін — тең енді шрифт: `Consolas`, `Courier New`.

```python
import tkinter as tk
from tkinter import font

root = tk.Tk()
families = sorted(font.families())
print("Шрифттер саны:", len(families))
print("Arial бар ма?", "Arial" in families)
root.destroy()
```

## 5. ttk модулі: тақырыпталған виджеттер

`tkinter.ttk` (Themed Tk) — заманауи көрінетін виджеттер жиынтығы. Олар операциялық жүйенің стиліне ұқсайды және **стильдер** арқылы орталықтан безендіріледі.

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("tk және ttk")
tk.Label(root, text="tk.Button:").grid(row=0, column=0, padx=10, pady=5)
tk.Button(root, text="Ескі стиль").grid(row=0, column=1, padx=10)
tk.Label(root, text="ttk.Button:").grid(row=1, column=0, padx=10, pady=5)
ttk.Button(root, text="Жаңа стиль").grid(row=1, column=1, padx=10)
root.mainloop()
```

### tk және ttk айырмашылығы

| | `tk` виджеттері | `ttk` виджеттері |
|---|---|---|
| Сыртқы түрі | Ескілеу, барлық ОЖ-да бірдей | Заманауи, ОЖ-ға бейімделген |
| Түсті баптау | Әр виджетке `bg`, `fg`, `font` | `Style` арқылы, барлық виджетке бірден |
| Тақырыптар | Жоқ | `clam`, `alt`, `default`, `classic`, Windows-та `vista`, macOS-та `aqua` |
| Ерекше виджеттер | `Text`, `Canvas`, `Listbox`, `Menu` (ttk-да жоқ) | `Combobox`, `Notebook`, `Progressbar`, `Treeview`, `Separator` |

`ttk`-да бар виджеттер: `Button`, `Label`, `Entry`, `Checkbutton`, `Radiobutton`, `Scale`, `Scrollbar`, `Frame`, `LabelFrame`, `Combobox`, `Notebook`, `Progressbar`, `Separator`, `Treeview`, `Spinbox`.

**Маңызды:** ttk виджеттерінде `bg`, `fg` опциялары жоқ (немесе шектеулі) — түстерді `Style` арқылы береміз.

## 6. Тақырыптар (themes)

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
style = ttk.Style()
print("Қолжетімді тақырыптар:", style.theme_names())
print("Ағымдағы тақырып:", style.theme_use())
style.theme_use("clam")          # тақырыпты ауыстыру
root.destroy()
```

Тақырыпты тірі ауыстыру:

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Тақырыптар")
style = ttk.Style()
theme = tk.StringVar(value=style.theme_use())

ttk.Label(root, text="Тақырыпты таңдаңыз:").pack(padx=10, pady=5)
box = ttk.Combobox(root, textvariable=theme, values=style.theme_names(), state="readonly")
box.pack(padx=10)
box.bind("<<ComboboxSelected>>", lambda e: style.theme_use(theme.get()))
ttk.Button(root, text="Батырма").pack(pady=5)
ttk.Checkbutton(root, text="Жалауша").pack()
ttk.Entry(root).pack(pady=5)
ttk.Progressbar(root, value=60, length=200).pack(pady=5)
root.mainloop()
```

## 7. ttk.Style — өз стильдеріңіз

```text
style.configure("СтильАты", опция=мән, ...)
```

Стиль атауы `.TButton`, `.TLabel` т.б. соңымен аяқталады. Әдепкі стильдер: `TButton`, `TLabel`, `TEntry`, `TFrame`, `TCheckbutton`...

```python
import tkinter as tk
from tkinter import ttk

ACCENT = "#0984e3"
BG = "#f5f6fa"

root = tk.Tk()
root.title("Стильдер")
root.configure(bg=BG)
style = ttk.Style()
style.theme_use("clam")

# барлық ttk виджеттерге ортақ
style.configure(".", font=("Segoe UI", 11), background=BG)
# барлық TLabel
style.configure("TLabel", foreground="#2d3436")
# өз стиліміз: тақырып жазуы
style.configure("Title.TLabel", font=("Segoe UI", 18, "bold"))
# өз стиліміз: акцент батырма
style.configure("Accent.TButton", background=ACCENT, foreground="white",
                padding=(16, 6), borderwidth=0)
# күйлерге қарай өзгеретін түстер
style.map("Accent.TButton",
          background=[("pressed", "#0652a3"), ("active", "#3498db"), ("disabled", "#b2bec3")])

ttk.Label(root, text="Тіркелу", style="Title.TLabel").pack(padx=20, pady=(15, 5))
ttk.Label(root, text="Деректеріңізді енгізіңіз").pack()
ttk.Entry(root, width=30).pack(padx=20, pady=10)
ttk.Button(root, text="Жіберу", style="Accent.TButton").pack(pady=(0, 15))
root.mainloop()
```

| Әдіс | Мағынасы |
|---|---|
| `Style()` | Стиль басқарушысы |
| `theme_names()` / `theme_use(name)` | Тақырыптар тізімі / тақырыпты орнату |
| `configure(style, **опциялар)` | Стильдің опцияларын орнату |
| `map(style, опция=[(күй, мән), ...])` | Күйге байланысты мәндер (`active`, `pressed`, `disabled`, `focus`) |
| `widget.configure(style="...")` | Виджетке стильді беру |

`"."` — барлық стильдердің түбірі: оған берілген опциялар барлық ttk виджеттерге әсер етеді.

## 8. Пайдалы ttk виджеттері

### Notebook — қойындылар (вкладки)

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Баптаулар")
nb = ttk.Notebook(root)
nb.pack(fill="both", expand=True, padx=10, pady=10)

general = ttk.Frame(nb, padding=10)
sound = ttk.Frame(nb, padding=10)
nb.add(general, text="Жалпы")
nb.add(sound, text="Дыбыс")

ttk.Label(general, text="Тілі:").grid(row=0, column=0, sticky="w")
ttk.Combobox(general, values=["Қазақша", "Русский", "English"],
             state="readonly").grid(row=0, column=1, padx=5)
ttk.Checkbutton(sound, text="Дыбысты қосу").pack(anchor="w")
ttk.Scale(sound, from_=0, to=100, length=200).pack(pady=10)
root.mainloop()
```

### Progressbar — орындалу жолағы

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Жүктелу")
bar = ttk.Progressbar(root, length=300, maximum=100, mode="determinate")
bar.pack(padx=20, pady=15)
label = ttk.Label(root, text="0%")
label.pack()


def load(step=0):
    if step <= 100:
        bar["value"] = step
        label.config(text=f"{step}%")
        root.after(30, load, step + 1)    # after-ке аргумент беру
    else:
        label.config(text="Дайын!")


ttk.Button(root, text="Бастау", command=load).pack(pady=10)
root.mainloop()
```

`mode="indeterminate"` + `bar.start()` — ұзақтығы белгісіз процесс үшін «жүгіретін» жолақ.

### LabelFrame және Separator

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Топтау")
group = ttk.LabelFrame(root, text="Хабарландырулар", padding=10)
group.pack(fill="x", padx=10, pady=10)
for name in ["Email", "SMS", "Push"]:
    ttk.Checkbutton(group, text=name).pack(anchor="w")
ttk.Separator(root, orient="horizontal").pack(fill="x", padx=10, pady=5)
ttk.Button(root, text="Сақтау").pack(pady=10)
root.mainloop()
```

## 9. Үлгі: стильді «Профиль» терезесі

```python
import tkinter as tk
from tkinter import ttk

PALETTE = {"bg": "#f5f6fa", "panel": "#ffffff", "text": "#2d3436",
           "muted": "#636e72", "accent": "#0984e3"}


class ProfileApp(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Профиль")
        self.configure(bg=PALETTE["bg"])
        self.resizable(False, False)
        self.setup_style()
        self.build()

    def setup_style(self):
        s = ttk.Style(self)
        s.theme_use("clam")
        s.configure(".", font=("Segoe UI", 11), background=PALETTE["bg"],
                    foreground=PALETTE["text"])
        s.configure("Card.TFrame", background=PALETTE["panel"])
        s.configure("Card.TLabel", background=PALETTE["panel"])
        s.configure("Muted.TLabel", background=PALETTE["panel"], foreground=PALETTE["muted"],
                    font=("Segoe UI", 9))
        s.configure("Title.TLabel", background=PALETTE["panel"], font=("Segoe UI", 16, "bold"))
        s.configure("Accent.TButton", background=PALETTE["accent"], foreground="white",
                    padding=(14, 6))
        s.map("Accent.TButton", background=[("active", "#3498db")])

    def build(self):
        card = ttk.Frame(self, style="Card.TFrame", padding=24)
        card.pack(padx=24, pady=24)
        card.columnconfigure(1, weight=1)
        ttk.Label(card, text="Жеке профиль", style="Title.TLabel").grid(
            row=0, column=0, columnspan=2, sticky="w")
        ttk.Label(card, text="Деректер тек үйірме ішінде қолданылады",
                  style="Muted.TLabel").grid(row=1, column=0, columnspan=2, sticky="w",
                                             pady=(0, 16))
        for i, name in enumerate(["Аты", "Тегі", "Сыныбы"], start=2):
            ttk.Label(card, text=name, style="Card.TLabel").grid(row=i, column=0, sticky="w",
                                                                 pady=6, padx=(0, 16))
            ttk.Entry(card, width=28).grid(row=i, column=1, sticky="ew", pady=6)
        buttons = ttk.Frame(card, style="Card.TFrame")
        buttons.grid(row=5, column=0, columnspan=2, sticky="e", pady=(16, 0))
        ttk.Button(buttons, text="Болдырмау").pack(side="left", padx=(0, 8))
        ttk.Button(buttons, text="Сақтау", style="Accent.TButton").pack(side="left")


if __name__ == "__main__":
    ProfileApp().mainloop()
```

Мұнда барлық қағида қолданылған: бір палитра, бір шрифт, бірдей шегіністер (8/16/24), туралау, акцент тек негізгі батырмада, топтау («карточка»).

## Жиі кездесетін қателер

**1. ttk виджетіне `bg`/`fg` беру**

```python
import tkinter as tk
from tkinter import ttk
root = tk.Tk()
# ttk.Label(root, text="Сәлем", bg="red")   -> TclError: unknown option "-bg"
style = ttk.Style()
style.configure("Red.TLabel", background="red")
ttk.Label(root, text="Сәлем", style="Red.TLabel").pack()
root.destroy()
```

**2. Стиль атауында `.TButton` соңын ұмыту** — `style.configure("Accent", ...)` виджетке әсер етпейді. Дұрысы: `"Accent.TButton"`.

**3. Батырма түсі өзгермейді** — кейбір тақырыптар (`vista`, `aqua`) батырма фонын баптауға мүмкіндік бермейді. `style.theme_use("clam")` қолданыңыз.

**4. Тым көп түс пен шрифт** — «кемпірқосақ» интерфейс. 60–30–10 ережесін және 1–2 шрифтті ұстаныңыз.

**5. Шегіністердің әртүрлілігі** — бір жерде `pady=3`, басқа жерде `pady=17`. Бір шкаланы таңдаңыз: 4, 8, 16, 24.

**6. Түстерді кодтың әр жеріне «шашу»** — `"#0984e3"` он жерде жазылса, өзгерту қиын. Тұрақтылар/палитра сөздігін қолданыңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| UI / UX | Пайдаланушы интерфейсі / пайдаланушы тәжірибесі (қолдану ыңғайлылығы) |
| Контраст, туралау, біркелкілік, жақындық, бос кеңістік | Дизайнның 5 негізгі қағидасы |
| Палитра | Қосымшада қолданылатын түстер жиыны |
| 60–30–10 ережесі | Негізгі, қосымша және акцент түстердің үлесі |
| `ttk` | Тақырыпталған (заманауи) Tkinter виджеттері |
| Тақырып (theme) | ttk виджеттерінің жалпы сыртқы түрі |
| `ttk.Style` | Стильдерді басқару: `configure`, `map`, `theme_use` |
| `Notebook`, `Progressbar`, `Separator`, `LabelFrame` | Пайдалы ttk виджеттері |

## Ескерту

- Тақырыптар жиыны ОЖ-ға байланысты: `vista`, `winnative`, `xpnative` тек Windows-та, `aqua` тек macOS-та бар; `clam`, `alt`, `default`, `classic` барлық жерде қолжетімді.
- `ttk.Style` объектісі бүкіл қосымшаға ортақ: стильді өзгерту оны қолданатын барлық виджеттерге бірден әсер етеді.
- Стиль қандай опцияларды қолдайтынын білу үшін `style.layout("TButton")`, `style.element_options("Button.label")` және `style.lookup("TButton", "background")` қолданыңыз.
- Дайын заманауи тақырыптар pip арқылы орнатылады: `sv-ttk` (Sun Valley), `ttkbootstrap`; CustomTkinter кітапханасы өз виджеттерін ұсынады.
- Оқылымдылық үшін WCAG ұсынымдары кәдімгі мәтін мен фонның контрастын кемінде 4.5:1 болуын талап етеді.
- Құжаттама: https://docs.python.org/3/library/tkinter.ttk.html
