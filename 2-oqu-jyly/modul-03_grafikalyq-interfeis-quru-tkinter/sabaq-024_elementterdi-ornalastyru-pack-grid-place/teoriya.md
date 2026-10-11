# Сабақ 24. Элементтерді орналастыру: pack, grid, place менеджерлері.

## Тақырыптар

1. Орналастыру менеджері дегеніміз не?
2. pack менеджері
3. Frame — виджеттерді топтастыру
4. grid менеджері
5. place менеджері
6. Менеджерлерді салыстыру және басты ереже
7. Интерфейсті жоспарлау кеңестері
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Орналастыру менеджері дегеніміз не?

Виджетті құру жеткіліксіз — оны терезеге **қою** керек. Мұны **орналастыру менеджері (менеджер геометрии, geometry manager)** атқарады. Tkinter-де үшеу:

| Менеджер | Принципі | Аналогия | Қашан қолданамыз |
|---|---|---|---|
| `pack` | Виджеттерді жағына қарай кезекпен «жинайды» | Чемоданға зат салу | Қарапайым, бір бағыттағы орналасу |
| `grid` | Кесте: жол мен баған | Excel кестесі, шахмат тақтасы | Формалар, калькулятор, кестелер |
| `place` | Дәл координат | Суретті қабырғаға шегемен қағу | Ерекше жағдайлар, үстінен қою |

## 2. pack менеджері

```python
import tkinter as tk

root = tk.Tk()
root.title("pack: side")
root.geometry("300x200")

tk.Label(root, text="TOP", bg="#f8c291").pack(side="top")
tk.Label(root, text="BOTTOM", bg="#82ccdd").pack(side="bottom")
tk.Label(root, text="LEFT", bg="#b8e994").pack(side="left")
tk.Label(root, text="RIGHT", bg="#f6b93b").pack(side="right")

root.mainloop()
```

`pack` бос кеңістіктен көрсетілген жаққа қарай «жолақ» бөліп, виджетті сонда қояды. Келесі виджет **қалған** кеңістікке орналасады — сондықтан **реті маңызды**.

### pack опциялары

| Опция | Мәндері | Мағынасы |
|---|---|---|
| `side` | `"top"` (әдепкі), `"bottom"`, `"left"`, `"right"` | Қай жаққа жабысады |
| `fill` | `"x"`, `"y"`, `"both"`, `"none"` | Бөлінген кеңістікті ені/биіктігі бойынша толтыру |
| `expand` | `True` / `False` | Терезе үлкейгенде қосымша орынды алу |
| `padx`, `pady` | сан немесе `(сол, оң)` | Сыртқы шегініс |
| `ipadx`, `ipady` | сан | Ішкі шегініс (виджетті «үрлейді») |
| `anchor` | `"n"`, `"s"`, `"e"`, `"w"`, `"center"`, `"ne"`... | Бөлінген кеңістіктің ішіндегі орны |

```python
import tkinter as tk

root = tk.Tk()
root.title("pack: fill және expand")
root.geometry("300x250")

tk.Label(root, text="Тақырып (fill=x)", bg="#4a69bd", fg="white").pack(fill="x")
tk.Label(root, text="Контент (fill=both, expand=True)", bg="#dfe6e9").pack(
    fill="both", expand=True, padx=10, pady=10)
tk.Label(root, text="Төменгі жол (fill=x)", bg="#4a69bd", fg="white").pack(
    side="bottom", fill="x")

root.mainloop()
```

Терезенің өлшемін тінтуірмен өзгертіп көріңіз: ортаңғы бөлік үлкейіп-кішірейеді, ал жоғарғы/төменгі жолақтар тек ені бойынша созылады.

## 3. Frame — виджеттерді топтастыру

**Frame (рамка)** — басқа виджеттерді ішіне салатын «қорап». Күрделі интерфейс әрқашан бірнеше Frame-ге бөлінеді.

```python
import tkinter as tk

root = tk.Tk()
root.title("Frame")

top = tk.Frame(root, bg="#dff9fb", padx=10, pady=10)
top.pack(fill="x")
tk.Label(top, text="Іздеу:", bg="#dff9fb").pack(side="left")
tk.Entry(top).pack(side="left", fill="x", expand=True, padx=5)
tk.Button(top, text="Табу").pack(side="left")

bottom = tk.Frame(root, padx=10, pady=10)
bottom.pack(fill="x")
tk.Button(bottom, text="Болдырмау").pack(side="right")
tk.Button(bottom, text="ОК").pack(side="right", padx=5)

root.mainloop()
```

Әр Frame-дің ішінде өз орналасуы бар. Мұны «матрёшка» сияқты елестетіңіз.

## 4. grid менеджері

`grid` терезені **жолдар (row)** мен **бағандарға (column)** бөледі. Нөмірлеу 0-ден басталады.

```text
          column 0        column 1
row 0   [ Аты:      ]  [ Entry          ]
row 1   [ Тегі:     ]  [ Entry          ]
row 2   [ Жасы:     ]  [ Entry          ]
row 3   [        Тіркелу (columnspan=2)  ]
```

```python
import tkinter as tk


def register():
    data = [e.get() for e in entries]
    result.config(text=" | ".join(data))


root = tk.Tk()
root.title("Тіркелу формасы")

fields = ["Аты:", "Тегі:", "Жасы:"]
entries = []
for i, text in enumerate(fields):
    tk.Label(root, text=text).grid(row=i, column=0, sticky="e", padx=5, pady=5)
    e = tk.Entry(root, width=25)
    e.grid(row=i, column=1, padx=5, pady=5)
    entries.append(e)

tk.Button(root, text="Тіркелу", command=register).grid(
    row=3, column=0, columnspan=2, sticky="ew", padx=5, pady=10)
result = tk.Label(root, text="", fg="blue")
result.grid(row=4, column=0, columnspan=2)

root.mainloop()
```

### grid опциялары

| Опция | Мағынасы |
|---|---|
| `row`, `column` | Жол және баған нөмірі |
| `rowspan`, `columnspan` | Бірнеше жол/бағанды алу (ұяшықтарды біріктіру) |
| `sticky` | Ұяшықтың қай жағына жабысу: `"n"`, `"s"`, `"e"`, `"w"` және олардың тіркесі: `"ew"` — енін толтыру, `"nsew"` — толық толтыру |
| `padx`, `pady`, `ipadx`, `ipady` | Шегіністер (pack-тағыдай) |

### Бағандарды «созылмалы» ету: weight

```python
import tkinter as tk

root = tk.Tk()
root.title("weight")
root.geometry("400x200")
root.columnconfigure(0, weight=1)   # 0-баған кеңістіктің 1 үлесін алады
root.columnconfigure(1, weight=3)   # 1-баған — 3 үлесін
root.rowconfigure(0, weight=1)

tk.Label(root, text="Мәзір", bg="#ffeaa7").grid(row=0, column=0, sticky="nsew")
tk.Label(root, text="Негізгі бөлім", bg="#81ecec").grid(row=0, column=1, sticky="nsew")

root.mainloop()
```

Терезені үлкейтсеңіз, 1-баған 0-бағаннан 3 есе жылдам кеңейеді.

### Мысал: калькулятор пернетақтасы

```python
import tkinter as tk

root = tk.Tk()
root.title("Пернетақта")
buttons = [
    "7", "8", "9", "/",
    "4", "5", "6", "*",
    "1", "2", "3", "-",
    "0", ".", "=", "+",
]
for i, text in enumerate(buttons):
    row, col = divmod(i, 4)
    tk.Button(root, text=text, width=5, height=2,
              font=("Arial", 14)).grid(row=row, column=col, padx=2, pady=2)
root.mainloop()
```

`divmod(i, 4)` бүтін бөлінді мен қалдықты бірге береді: `divmod(6, 4)` → `(1, 2)` — 1-жол, 2-баған.

## 5. place менеджері

`place` виджетті **нақты координатқа** қояды.

```python
import tkinter as tk

root = tk.Tk()
root.title("place")
root.geometry("300x200")

tk.Label(root, text="(10, 10)", bg="#fab1a0").place(x=10, y=10)
tk.Label(root, text="Ортада", bg="#74b9ff").place(relx=0.5, rely=0.5, anchor="center")
tk.Button(root, text="Оң жақ төменгі бұрыш").place(relx=1.0, rely=1.0, anchor="se",
                                                   x=-5, y=-5)
tk.Label(root, text="Жартылай ен", bg="#55efc4").place(relx=0, rely=0.25, relwidth=0.5)

root.mainloop()
```

| Опция | Мағынасы |
|---|---|
| `x`, `y` | Абсолютті координаттар (пиксель) |
| `relx`, `rely` | Салыстырмалы орын: 0.0 — сол/жоғары, 1.0 — оң/төмен |
| `anchor` | Виджеттің қай нүктесі координатқа сәйкес келеді |
| `width`, `height` | Абсолютті өлшем |
| `relwidth`, `relheight` | Ата-виджетке қатысты өлшем (0.5 — жартысы) |

`place` кемшілігі: терезе өлшемі өзгергенде абсолютті координаттағы виджеттер «жылжымайды», мәтін ұзарса бір-бірін жабуы мүмкін. Сондықтан негізгі интерфейсті `pack`/`grid` арқылы, ал `place`-ті ерекше жағдайларда (мысалы, сурет үстіне жазу) қолданамыз.

## 6. Менеджерлерді салыстыру және басты ереже

| | `pack` | `grid` | `place` |
|---|---|---|---|
| Қарапайымдылығы | Өте оңай | Орташа | Оңай, бірақ қолмен есептеу көп |
| Өлшем өзгергенде | Жақсы бейімделеді | Жақсы (`weight`) | Нашар (абсолюттіде) |
| Формалар үшін | Нашар | Өте жақсы | Нашар |
| Тізбек/жолақ үшін | Өте жақсы | Жақсы | Орташа |

**Басты ереже:** бір ата-виджеттің (терезенің немесе бір Frame-нің) ішінде `pack` пен `grid`-ті **араластыруға болмайды**! Python 3.10+-та бұл `TclError` береді. Әртүрлі менеджерлер керек болса — әртүрлі Frame-дерді қолданыңыз:

```python
import tkinter as tk

root = tk.Tk()
root.title("Аралас интерфейс")

header = tk.Label(root, text="Жеке деректер", font=("Arial", 14, "bold"))
header.pack(pady=5)                      # root ішінде — тек pack

form = tk.Frame(root)                    # form root-қа pack-пен қойылады
form.pack(padx=10, pady=5)
tk.Label(form, text="Email:").grid(row=0, column=0, sticky="e")   # form ішінде — тек grid
tk.Entry(form).grid(row=0, column=1)
tk.Label(form, text="Телефон:").grid(row=1, column=0, sticky="e")
tk.Entry(form).grid(row=1, column=1)

tk.Button(root, text="Сақтау").pack(pady=10)
root.mainloop()
```

## 7. Интерфейсті жоспарлау кеңестері

1. Алдымен қағазға **эскиз** сызыңыз: қандай бөліктер (тақырып, форма, батырмалар жолағы).
2. Әр бөлікті — бөлек `Frame`.
3. Формалар мен кестелерге — `grid`, жолақтар мен тізбектерге — `pack`.
4. Шегіністерді (`padx`, `pady`) бірдей ұстаңыз (мысалы, барлық жерде 5 немесе 10) — интерфейс ұқыпты көрінеді.
5. Терезені үлкейтіп-кішірейтіп тексеріңіз.

## Жиі кездесетін қателер

**1. Бір контейнерде `pack` пен `grid` араластыру**

```python
import tkinter as tk
root = tk.Tk()
tk.Label(root, text="A").pack()
# tk.Label(root, text="B").grid(row=0, column=0)
# -> TclError: cannot use geometry manager grid inside . which already has slaves managed by pack
root.destroy()
```

**2. `sticky` жазбай, Entry немесе батырманы созылмайды деп ойлау** — `grid`-те виджет ұяшықтың ортасында тұрады; созу үшін `sticky="ew"`.

**3. `weight` берілмеген** — терезені үлкейткенде grid орнында қалады. `columnconfigure(i, weight=1)` қосыңыз.

**4. `expand=True` бар, бірақ `fill` жоқ** — виджетке орын бөлінеді, бірақ виджеттің өзі созылмайды.

**5. Виджет ата-виджетін қате көрсету** — `tk.Entry(root)` деп құрып, оны Frame ішіндегі grid-ке қоюға тырысу. Виджет қай Frame-де тұрса, сол Frame бірінші аргумент болуы керек.

**6. Бір виджетке екі менеджерді кезекпен қолдану** — соңғысы алдыңғысын «ауыстырады»; бұл шатасуға әкеледі.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| Орналастыру менеджері | Виджеттің терезедегі орны мен өлшемін анықтайтын механизм |
| `pack` | Виджеттерді жақтарға кезекпен жинау |
| `grid` | Жолдар мен бағандардан тұратын кесте |
| `place` | Абсолютті немесе салыстырмалы координаттар |
| `Frame` | Виджеттерді топтастыратын контейнер |
| `sticky` | grid ұяшығында виджеттің жабысатын жағы |
| `weight` | Бос кеңістікті жол/бағандар арасында бөлу үлесі |
| `fill`, `expand` | pack-та созылу және қосымша орынды алу |

## Ескерту

- `pack()`, `grid()` және `place()` әдістері `None` қайтарады, сондықтан кейін керек болатын виджетті құру мен орналастыру екі жолға бөлініп жазылады.
- Әдепкі бойынша `Frame` ішіндегісінің өлшеміне бейімделіп, өз `width`/`height` мәндерін елемейді; берілген өлшемді сақтау үшін `frame.pack_propagate(False)` немесе `frame.grid_propagate(False)` шақырылады.
- Виджеті жоқ бос `grid` жолының немесе бағанының өлшемі нөлге тең; ең аз енін/биіктігін `columnconfigure(i, minsize=...)` / `rowconfigure(i, minsize=...)` береді.
- Виджетті `pack_forget()`, `grid_forget()` немесе `grid_remove()` арқылы жасыруға болады; соңғысы опцияларды есте сақтайды, қайта `grid()` шақырса, виджет бұрынғы орнына оралады.
- Шегіністерді екі жақтан әртүрлі беруге болады: `padx=(10, 0)` — сол жақтан 10 пиксель, оң жақтан 0.
- Орналастыру бойынша толық нұсқаулық: https://tkdocs.com/tutorial/grid.html
