# Сабақ 27. Text мәтіндік өрісі. Айналдыру жолағы (Scrollbar).

## Тақырыптар

1. Text виджеті
2. Text индекстері
3. Мәтінді оқу, қосу, өшіру
4. Тегтер — мәтінді пішімдеу
5. Scrollbar — айналдыру жолағы
6. Дайын шешім: `scrolledtext`
7. Жиі кездесетін қателер
8. Негізгі ұғымдар

## 1. Text виджеті

`Entry` — тек **бір жол**. Көп жолды мәтін (хат, шығарма, код, чат) үшін **`Text`** виджеті қолданылады.

| | `Entry` | `Text` |
|---|---|---|
| Жолдар | Бір | Шексіз |
| Индекс | Сан: `0`, `tk.END` | `"жол.таңба"`: `"1.0"`, `tk.END` |
| `textvariable` | Бар | Жоқ |
| Пішімдеу (түстер, шрифттер) | Бүкіл өріске | Мәтіннің жеке бөліктеріне (тегтер) |
| Суреттер, виджеттер кірістіру | Жоқ | Бар |

```python
import tkinter as tk

root = tk.Tk()
root.title("Text")
text = tk.Text(root, width=40, height=10, wrap="word", font=("Arial", 12))
text.pack(padx=10, pady=10)
text.insert("1.0", "Адам баласына адам баласының бәрі — дос.\n")
text.insert(tk.END, "Абай Құнанбайұлы")
root.mainloop()
```

| Опция | Мағынасы |
|---|---|
| `width`, `height` | Өлшемі (таңбалар және жолдар саны) |
| `wrap` | Жолды тасымалдау: `"none"` (тасымалдамау), `"char"` (таңба бойынша), `"word"` (сөз бойынша) |
| `font`, `fg`, `bg` | Шрифт және түстер |
| `padx`, `pady` | Ішкі шегіністер |
| `state` | `"normal"` — өңдеуге болады, `"disabled"` — тек оқу |
| `undo` | `True` болса, `Ctrl+Z` арқылы болдырмау жұмыс істейді |
| `insertbackground` | Курсор түсі (қараңғы фонда пайдалы) |

## 2. Text индекстері

`Text` ішіндегі әр орын `"жол.таңба"` түрінде беріледі:
- **жолдар 1-ден** басталады,
- **таңбалар 0-ден** басталады.

```text
       таңба: 0 1 2 3 4 5
жол 1:        С ә л е м !
жол 2:        Ә л е м
```

`"1.0"` — 1-жолдың басы, `"1.3"` — «е» әрпінің алдындағы орын, `"2.0"` — 2-жолдың басы.

| Индекс | Мағынасы |
|---|---|
| `"1.0"` | Мәтіннің ең басы |
| `tk.END` (`"end"`) | Мәтіннің соңы (Tkinter мұнда әрқашан қосымша `\n` қосады) |
| `"insert"` (`tk.INSERT`) | Курсордың тұрған орны |
| `"2.end"` | 2-жолдың соңы |
| `"insert linestart"` | Курсор тұрған жолдың басы |
| `"1.0 + 5 chars"` | Басынан 5 таңба кейін |
| `"end-1c"` | Соңғы (қосымша `\n`-сіз) нақты соң |
| `"sel.first"`, `"sel.last"` | Белгіленген мәтіннің басы мен соңы |

## 3. Мәтінді оқу, қосу, өшіру

```python
import tkinter as tk


def show_stats():
    content = text.get("1.0", "end-1c")      # бүкіл мәтін, соңғы \n-сіз
    lines = content.count("\n") + 1 if content else 0
    words = len(content.split())
    stats.config(text=f"Таңбалар: {len(content)} | Сөздер: {words} | Жолдар: {lines}")


def clear():
    text.delete("1.0", tk.END)
    show_stats()


def insert_date():
    import datetime
    text.insert("insert", datetime.date.today().strftime("%d.%m.%Y"))


root = tk.Tk()
root.title("Мәтін статистикасы")
text = tk.Text(root, width=50, height=10, wrap="word", undo=True)
text.pack(padx=10, pady=5)
bar = tk.Frame(root)
bar.pack()
tk.Button(bar, text="Санау", command=show_stats).pack(side="left", padx=3)
tk.Button(bar, text="Күнді қою", command=insert_date).pack(side="left", padx=3)
tk.Button(bar, text="Тазарту", command=clear).pack(side="left", padx=3)
stats = tk.Label(root, text="")
stats.pack(pady=5)
text.bind("<KeyRelease>", lambda e: show_stats())   # әр перне жіберілгенде санау
root.mainloop()
```

| Әдіс | Мысал | Мағынасы |
|---|---|---|
| `insert(index, text)` | `text.insert(tk.END, "сәлем\n")` | Мәтін қосу |
| `get(start, end)` | `text.get("1.0", "end-1c")` | Мәтінді алу |
| `delete(start, end)` | `text.delete("1.0", tk.END)` | Өшіру |
| `see(index)` | `text.see(tk.END)` | Сол орынға айналдыру |
| `index(index)` | `text.index("insert")` → `"3.5"` | Индексті «жол.таңба» түрінде алу |
| `search(pattern, start, stopindex)` | `text.search("Абай", "1.0", tk.END)` | Мәтіннен іздеу |

### Тек оқуға арналған Text (журнал, чат)

`state="disabled"` болса, мәтінді тіпті код та өзгерте алмайды. Сондықтан: уақытша `normal` → қосу → қайта `disabled`.

```python
import tkinter as tk
import time


def log(message):
    log_text.config(state="normal")
    log_text.insert(tk.END, f"[{time.strftime('%H:%M:%S')}] {message}\n")
    log_text.see(tk.END)                      # соңғы жолға айналдыру
    log_text.config(state="disabled")


def send(event=None):
    msg = entry.get().strip()
    if msg:
        log(f"Сіз: {msg}")
        entry.delete(0, tk.END)


root = tk.Tk()
root.title("Чат")
log_text = tk.Text(root, width=45, height=12, state="disabled", bg="#f5f6fa")
log_text.pack(padx=10, pady=5)
entry = tk.Entry(root, width=35)
entry.pack(side="left", padx=10, pady=5)
entry.bind("<Return>", send)
tk.Button(root, text="Жіберу", command=send).pack(side="left")
log("Чатқа қош келдіңіз!")
root.mainloop()
```

## 4. Тегтер — мәтінді пішімдеу

**Тег (tag)** — мәтіннің бір бөлігіне берілген атау; тегке стиль (түс, шрифт, фон) беріледі.

```python
import tkinter as tk

root = tk.Tk()
root.title("Тегтер")
text = tk.Text(root, width=50, height=8, wrap="word", font=("Arial", 12))
text.pack(padx=10, pady=10)

text.tag_config("title", font=("Arial", 16, "bold"), foreground="#2d3436", justify="center")
text.tag_config("quote", font=("Arial", 12, "italic"), foreground="#0984e3")
text.tag_config("mark", background="yellow")

text.insert(tk.END, "Он төртінші сөз\n", "title")      # мәтінмен бірге тег
text.insert(tk.END, "«Жүрек — теңіз, ақыл — кеме»\n", "quote")
text.insert(tk.END, "Бұл сөздерді бірнеше рет оқып шығыңыз.")
text.tag_add("mark", "3.4", "3.12")                    # «сөздерді» сөзіне тег қосу
root.mainloop()
```

| Әдіс | Мағынасы |
|---|---|
| `tag_config(name, **опциялар)` | Тег стилін анықтау: `foreground`, `background`, `font`, `underline`, `justify` |
| `insert(index, text, tag)` | Мәтінді бірден тегпен қосу |
| `tag_add(name, start, end)` | Бар мәтінге тег қою |
| `tag_remove(name, start, end)` | Тегті алу |

### Іздеу және бөлектеу

```python
import tkinter as tk

SAMPLE = ("Python — бағдарламалау тілі. Python оқуға жеңіл. "
          "Python тілінде ойындар мен сайттар жасауға болады.")


def highlight():
    text.tag_remove("found", "1.0", tk.END)
    word = entry.get()
    if not word:
        return
    count = 0
    start = "1.0"
    while True:
        pos = text.search(word, start, stopindex=tk.END, nocase=True)
        if not pos:
            break
        end = f"{pos}+{len(word)}c"
        text.tag_add("found", pos, end)
        start = end
        count += 1
    info.config(text=f"Табылды: {count}")


root = tk.Tk()
root.title("Іздеу")
entry = tk.Entry(root)
entry.pack(pady=5)
tk.Button(root, text="Бөлектеу", command=highlight).pack()
text = tk.Text(root, width=50, height=6, wrap="word")
text.pack(padx=10, pady=5)
text.insert("1.0", SAMPLE)
text.tag_config("found", background="#ffeaa7", foreground="#d63031")
info = tk.Label(root, text="")
info.pack()
root.mainloop()
```

## 5. Scrollbar — айналдыру жолағы

Мәтін өріске сыймаса, айналдыру жолағы керек. Scrollbar мен Text **екі жақты** байланысады:

```text
   Text  ──yscrollcommand=scroll.set──►  Scrollbar   (мәтін жылжыса, жүгірткі жылжиды)
   Text  ◄──command=text.yview─────────  Scrollbar   (жүгірткіні тартса, мәтін жылжиды)
```

```python
import tkinter as tk

root = tk.Tk()
root.title("Scrollbar")

frame = tk.Frame(root)
frame.pack(fill="both", expand=True, padx=10, pady=10)

scroll = tk.Scrollbar(frame)
scroll.pack(side="right", fill="y")

text = tk.Text(frame, width=40, height=10, wrap="word", yscrollcommand=scroll.set)
text.pack(side="left", fill="both", expand=True)
scroll.config(command=text.yview)

for i in range(1, 51):
    text.insert(tk.END, f"{i}-жол: Python үйренеміз!\n")

root.mainloop()
```

**Ереже:** Scrollbar-ды алдымен `side="right", fill="y"` деп орналастырыңыз, сосын Text — сонда терезе кішірейгенде жолақ жоғалмайды.

### Көлденең айналдыру

`wrap="none"` болса ұзын жолдар үшін көлденең жолақ қажет:

```python
import tkinter as tk

root = tk.Tk()
root.title("Екі жолақ")
text = tk.Text(root, width=30, height=8, wrap="none")
ys = tk.Scrollbar(root, orient="vertical", command=text.yview)
xs = tk.Scrollbar(root, orient="horizontal", command=text.xview)
text.config(yscrollcommand=ys.set, xscrollcommand=xs.set)

text.grid(row=0, column=0, sticky="nsew")
ys.grid(row=0, column=1, sticky="ns")
xs.grid(row=1, column=0, sticky="ew")
root.rowconfigure(0, weight=1)
root.columnconfigure(0, weight=1)

for i in range(30):
    text.insert(tk.END, f"{i:02}: " + "өте ұзын жол " * 8 + "\n")
root.mainloop()
```

### Listbox-қа Scrollbar

```python
import tkinter as tk

root = tk.Tk()
root.title("Қалалар")
scroll = tk.Scrollbar(root)
scroll.pack(side="right", fill="y")
lb = tk.Listbox(root, height=6, yscrollcommand=scroll.set)
lb.pack(side="left", fill="both", expand=True)
scroll.config(command=lb.yview)
for i in range(1, 101):
    lb.insert(tk.END, f"{i}-оқушы")
root.mainloop()
```

## 6. Дайын шешім: `scrolledtext`

`tkinter.scrolledtext.ScrolledText` — ішінде тік Scrollbar-ы бар дайын Text:

```python
import tkinter as tk
from tkinter import scrolledtext

root = tk.Tk()
root.title("ScrolledText")
st = scrolledtext.ScrolledText(root, width=50, height=10, wrap="word", font=("Consolas", 11))
st.pack(padx=10, pady=10, fill="both", expand=True)
st.insert(tk.END, "Мұнда ұзын мәтін жазыңыз...\n" * 30)
root.mainloop()
```

Әдістері — `Text`-тікімен бірдей.

## Жиі кездесетін қателер

**1. Индексті сан түрінде беру**

```python
import tkinter as tk
root = tk.Tk()
t = tk.Text(root)
# t.get(0, tk.END)   -> TclError: bad text index "0"
print(repr(t.get("1.0", "end-1c")))   # дұрыс: "1.0"
root.destroy()
```

**2. Жолдарды 0-ден санау** — Text-те жолдар **1-ден**, таңбалар 0-ден басталады.

**3. `get("1.0", tk.END)` соңындағы артық `\n`** — салыстыру/сақтау кезінде `"end-1c"` немесе `.strip()` қолданыңыз.

**4. `state="disabled"` өріске `insert` жасау** — ештеңе қосылмайды (қате де шықпайды). Алдымен `config(state="normal")`.

**5. Scrollbar-ды бір жақты байланыстыру** — тек `command=text.yview` берілсе, жүгірткі мәтінмен бірге қозғалмайды; `yscrollcommand=scroll.set` міндетті.

**6. Text үшін `textvariable` іздеу** — Text-те ол жоқ; мәтінді `get`/`insert` арқылы басқарамыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| `Text` | Көп жолды, пішімделетін мәтін өрісі |
| Индекс `"жол.таңба"` | Text-тегі орын (жол 1-ден, таңба 0-ден) |
| `tk.END`, `"end-1c"` | Мәтін соңы; соңғы `\n`-сіз соң |
| `"insert"` | Курсордың ағымдағы орны |
| `wrap` | Жолдарды тасымалдау тәртібі |
| Тег (tag) | Мәтін бөлігіне берілген стиль атауы |
| `Scrollbar` | Айналдыру жолағы |
| `yscrollcommand` / `yview` | Text пен Scrollbar-ды екі жақты байланыстыру |
| `ScrolledText` | Айналдыру жолағы кірістірілген дайын Text |

## Ескерту

- `undo=True` болғанда `edit_undo()` және `edit_redo()` әдістері қолжетімді; `edit_reset()` болдырмау тарихын тазалайды (жаңа файл ашқаннан кейін пайдалы).
- `text.search(..., regexp=True)` Tcl тұрақты өрнектері бойынша іздейді — олардың синтаксисі `re` модуліне ұқсас, бірақ толық сәйкес келмейді.
- Үлкен мәтінді циклде жолма-жол емес, бір `insert` шақыруымен қосу жылдамырақ.
- Индекстердің пайдалы модификаторлары: `"insert lineend"`, `"1.0 wordend"`, `"end-1c"`; `text.index(...)` оларды `"жол.таңба"` түріне айналдырады.
- `ScrolledText` — айналдыру жолағымен бірге `Frame` ішіне салынған `Text`; `pack`/`grid` әдістері бүкіл құрылымға қолданылады.
- Құжаттама: https://docs.python.org/3/library/tkinter.scrolledtext.html
