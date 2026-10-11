# Сабақ 26. Виджеттер: Checkbutton, Radiobutton, Listbox, Combobox.

## Тақырыптар

1. Таңдау виджеттерінің шолуы
2. Checkbutton — жалауша
3. Radiobutton — ауыстырғыш
4. Listbox — тізім
5. ttk.Combobox — ашылмалы тізім
6. Барлығы бірге: «Үйірмеге тіркелу» формасы
7. Қай виджетті таңдау керек?
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Таңдау виджеттерінің шолуы

| Виджет | Сыртқы түрі | Таңдау түрі | Мысал |
|---|---|---|---|
| `Checkbutton` | ☑ жалауша | Бірнеше тәуелсіз «иә/жоқ» | «Ірімшік қосу», «Келісемін» |
| `Radiobutton` | ◉ дөңгелек | Топтан **біреуін** | Жынысы, төлем тәсілі |
| `Listbox` | Тізім қорабы | Бір немесе бірнеше элемент | Пәндер тізімі, ойнату тізімі |
| `ttk.Combobox` | Ашылмалы тізім ▼ | Біреуін (немесе өз мәнін жазу) | Қала, сынып, ай |

## 2. Checkbutton — жалауша

Әр `Checkbutton`-ге **өз** айнымалысы байланыстырылады:

```python
import tkinter as tk


def show():
    items = []
    if cheese.get():
        items.append("ірімшік")
    if mushrooms.get():
        items.append("саңырауқұлақ")
    if olives.get():
        items.append("зәйтүн")
    result.config(text="Қосымшалар: " + (", ".join(items) if items else "жоқ"))


root = tk.Tk()
root.title("Пицца")
cheese = tk.BooleanVar(value=True)      # әдепкі бойынша белгіленген
mushrooms = tk.BooleanVar()
olives = tk.BooleanVar()

tk.Checkbutton(root, text="Ірімшік", variable=cheese, command=show).pack(anchor="w")
tk.Checkbutton(root, text="Саңырауқұлақ", variable=mushrooms, command=show).pack(anchor="w")
tk.Checkbutton(root, text="Зәйтүн", variable=olives, command=show).pack(anchor="w")
result = tk.Label(root, text="")
result.pack(pady=10, padx=20)
show()
root.mainloop()
```

| Опция | Мағынасы |
|---|---|
| `variable` | Байланысқан айнымалы (`BooleanVar`, `IntVar`, `StringVar`) |
| `onvalue`, `offvalue` | Белгіленгенде/белгіленбегенде айнымалыға жазылатын мән (әдепкі 1 және 0) |
| `command` | Күйі өзгерген сайын шақырылатын функция |

`onvalue`/`offvalue` мысалы: `tk.Checkbutton(root, text="Жаңалықтарға жазылу", variable=sub, onvalue="иә", offvalue="жоқ")`, мұнда `sub = tk.StringVar(value="жоқ")`.

### «Келісемін» — батырманы белсендіру

```python
import tkinter as tk


def toggle():
    btn.config(state="normal" if agree.get() else "disabled")


root = tk.Tk()
root.title("Ережелер")
agree = tk.BooleanVar()
tk.Checkbutton(root, text="Үйірме ережелерімен таныстым", variable=agree,
               command=toggle).pack(padx=20, pady=10)
btn = tk.Button(root, text="Тіркелу", state="disabled")
btn.pack(pady=5)
root.mainloop()
```

## 3. Radiobutton — ауыстырғыш

Бір топтағы барлық `Radiobutton` **бір ортақ айнымалыға** байланады, әрқайсысының өз `value`-і бар. Біреуін таңдағанда басқалары автоматты түрде «өшеді».

```python
import tkinter as tk


def show():
    result.config(text=f"Таңдалды: {payment.get()}")


root = tk.Tk()
root.title("Төлем тәсілі")
payment = tk.StringVar(value="Kaspi QR")

for option in ["Kaspi QR", "Банк картасы", "Қолма-қол"]:
    tk.Radiobutton(root, text=option, variable=payment, value=option,
                   command=show).pack(anchor="w", padx=20)

result = tk.Label(root, text="Таңдалды: Kaspi QR", fg="blue")
result.pack(pady=10)
root.mainloop()
```

Сан мәндерімен (мысалы, сынып):

```python
import tkinter as tk

root = tk.Tk()
root.title("Деңгей")
level = tk.IntVar(value=1)
levels = {1: "Жеңіл", 2: "Орташа", 3: "Қиын"}
for value, text in levels.items():
    tk.Radiobutton(root, text=text, variable=level, value=value).pack(side="left", padx=5)
tk.Button(root, text="Бастау",
          command=lambda: print("Деңгей:", levels[level.get()])).pack(side="left", padx=10)
root.mainloop()
```

**Есте сақтаңыз:** Checkbutton — әрқайсысына жеке айнымалы; Radiobutton — бүкіл топқа бір айнымалы.

## 4. Listbox — тізім

```python
import tkinter as tk


def add():
    text = entry.get().strip()
    if text:
        listbox.insert(tk.END, text)       # соңына қосу
        entry.delete(0, tk.END)


def remove():
    selected = listbox.curselection()     # таңдалған индекстер кортежі
    if selected:
        listbox.delete(selected[0])


def on_select(event):
    selected = listbox.curselection()
    if selected:
        info.config(text=f"Таңдалды: {listbox.get(selected[0])}")


root = tk.Tk()
root.title("Пәндер")
listbox = tk.Listbox(root, height=6, width=25)
for subject in ["Математика", "Физика", "Информатика"]:
    listbox.insert(tk.END, subject)
listbox.pack(padx=10, pady=5)
listbox.bind("<<ListboxSelect>>", on_select)

entry = tk.Entry(root, width=25)
entry.pack(pady=2)
tk.Button(root, text="Қосу", command=add).pack(side="left", padx=10, pady=5)
tk.Button(root, text="Өшіру", command=remove).pack(side="right", padx=10, pady=5)
info = tk.Label(root, text="")
info.pack(side="bottom", pady=5)
root.mainloop()
```

### Listbox әдістері мен опциялары

| Әдіс / опция | Мағынасы |
|---|---|
| `insert(index, *items)` | Элемент(тер) қосу: `insert(tk.END, "А", "Б")` |
| `delete(first, last=None)` | Өшіру; `delete(0, tk.END)` — бәрін |
| `get(index)` / `get(0, tk.END)` | Бір элемент / барлық элементтер (кортеж) |
| `curselection()` | Таңдалған элементтердің **индекстері** (кортеж; ештеңе таңдалмаса — бос) |
| `size()` | Элементтер саны |
| `selection_set(i)` / `selection_clear(0, tk.END)` | Кодтан таңдау / таңдауды алу |
| `selectmode` | `"browse"` (біреу, әдепкі), `"single"`, `"multiple"` (басып белгілеу), `"extended"` (Ctrl/Shift) |
| `height`, `width` | Көрінетін жолдар саны, ені |
| `<<ListboxSelect>>` | Таңдау өзгергендегі виртуалды оқиға |

Бірнеше таңдау:

```python
import tkinter as tk


def show():
    chosen = [lb.get(i) for i in lb.curselection()]
    print("Таңдалған үйірмелер:", chosen)


root = tk.Tk()
root.title("Үйірмелер")
lb = tk.Listbox(root, selectmode="multiple", height=5)
lb.insert(tk.END, "Python", "Робототехника", "3D-модельдеу", "Шахмат", "Домбыра")
lb.pack(padx=10, pady=5)
tk.Button(root, text="Көрсету", command=show).pack(pady=5)
root.mainloop()
```

## 5. ttk.Combobox — ашылмалы тізім

`Combobox` ерекше модульде — `tkinter.ttk` (Themed Tk — заманауи көріністегі тақырыптық виджеттер).

```python
import tkinter as tk
from tkinter import ttk

CITIES = ["Алматы", "Астана", "Шымкент", "Қарағанды", "Ақтөбе", "Атырау"]


def on_select(event):
    info.config(text=f"Сіз {city_var.get()} қаласын таңдадыңыз")


root = tk.Tk()
root.title("Қала")
city_var = tk.StringVar()
combo = ttk.Combobox(root, textvariable=city_var, values=CITIES,
                     state="readonly", width=20)
combo.current(0)                         # бірінші элементті таңдау
combo.pack(padx=20, pady=10)
combo.bind("<<ComboboxSelected>>", on_select)
info = tk.Label(root, text="")
info.pack(pady=5)
root.mainloop()
```

| Әдіс / опция | Мағынасы |
|---|---|
| `values` | Тізім элементтері |
| `state="readonly"` | Тек тізімнен таңдау (өз мәтінін жазуға болмайды) |
| `current(i)` | `i`-ші элементті таңдау; `current()` — таңдалған индекс |
| `get()` / `set(text)` | Мәнін алу / орнату |
| `<<ComboboxSelected>>` | Тізімнен таңдалғандағы оқиға |
| `combo["values"] = [...]` | Тізімді кейін өзгерту |

### Тәуелді Combobox-тар

```python
import tkinter as tk
from tkinter import ttk

REGIONS = {
    "Алматы облысы": ["Қонаев", "Есік", "Талғар"],
    "Түркістан облысы": ["Түркістан", "Кентау", "Сарыағаш"],
    "Маңғыстау облысы": ["Ақтау", "Жаңаөзен", "Форт-Шевченко"],
}


def on_region(event):
    towns = REGIONS[region.get()]
    town["values"] = towns
    town.current(0)


root = tk.Tk()
root.title("Мекенжай")
region = ttk.Combobox(root, values=list(REGIONS), state="readonly")
region.grid(row=0, column=1, padx=5, pady=5)
tk.Label(root, text="Облыс:").grid(row=0, column=0, sticky="e")
town = ttk.Combobox(root, state="readonly")
town.grid(row=1, column=1, padx=5, pady=5)
tk.Label(root, text="Қала:").grid(row=1, column=0, sticky="e")
region.bind("<<ComboboxSelected>>", on_region)
root.mainloop()
```

## 6. Барлығы бірге: «Үйірмеге тіркелу» формасы

```python
import tkinter as tk
from tkinter import ttk


def submit():
    langs = [name for name, var in lang_vars.items() if var.get()]
    lines = [
        f"Аты: {name_entry.get()}",
        f"Сынып: {grade.get()}",
        f"Жынысы: {gender.get()}",
        f"Білетін тілдері: {', '.join(langs) if langs else '—'}",
        f"Уақыт: {time_box.get(time_box.curselection()[0]) if time_box.curselection() else '—'}",
    ]
    result.config(text="\n".join(lines))


root = tk.Tk()
root.title("Үйірмеге тіркелу")

tk.Label(root, text="Аты:").grid(row=0, column=0, sticky="e", padx=5, pady=3)
name_entry = tk.Entry(root)
name_entry.grid(row=0, column=1, sticky="w", padx=5)

tk.Label(root, text="Сынып:").grid(row=1, column=0, sticky="e", padx=5, pady=3)
grade = ttk.Combobox(root, values=[str(g) for g in range(5, 12)], state="readonly", width=5)
grade.current(2)
grade.grid(row=1, column=1, sticky="w", padx=5)

tk.Label(root, text="Жынысы:").grid(row=2, column=0, sticky="e", padx=5, pady=3)
gender = tk.StringVar(value="ұл")
gframe = tk.Frame(root)
gframe.grid(row=2, column=1, sticky="w")
tk.Radiobutton(gframe, text="ұл", variable=gender, value="ұл").pack(side="left")
tk.Radiobutton(gframe, text="қыз", variable=gender, value="қыз").pack(side="left")

tk.Label(root, text="Тілдер:").grid(row=3, column=0, sticky="ne", padx=5, pady=3)
lframe = tk.Frame(root)
lframe.grid(row=3, column=1, sticky="w")
lang_vars = {}
for lang in ["Python", "Scratch", "C++"]:
    var = tk.BooleanVar()
    tk.Checkbutton(lframe, text=lang, variable=var).pack(anchor="w")
    lang_vars[lang] = var

tk.Label(root, text="Уақыт:").grid(row=4, column=0, sticky="ne", padx=5, pady=3)
time_box = tk.Listbox(root, height=3, exportselection=False)
time_box.insert(tk.END, "Дс/Ср 15:00", "Сс/Бс 16:00", "Сб 10:00")
time_box.grid(row=4, column=1, sticky="w", padx=5)

tk.Button(root, text="Жіберу", command=submit).grid(row=5, column=0, columnspan=2, pady=8)
result = tk.Label(root, text="", justify="left", fg="blue")
result.grid(row=6, column=0, columnspan=2, padx=10, pady=5)
root.mainloop()
```

`exportselection=False` — басқа виджетте мәтін белгілегенде Listbox таңдауы «жоғалып кетпеуі» үшін.

## 7. Қай виджетті таңдау керек?

| Жағдаят | Виджет |
|---|---|
| 2–5 нұсқаның біреуі, бәрі көрініп тұруы керек | `Radiobutton` |
| Көп нұсқаның біреуі (қала, ел, ай) | `Combobox` |
| Әр опция тәуелсіз «иә/жоқ» | `Checkbutton` |
| Ұзын тізім, қосу/өшіру, бірнеше таңдау | `Listbox` |

## Жиі кездесетін қателер

**1. Radiobutton-дарға әртүрлі айнымалы беру** — бәрін бірдей таңдауға болып қалады. Бір топқа — бір айнымалы.

**2. Checkbutton-дарға бір айнымалы беру** — біреуін басқанда бәрі бірге белгіленеді. Әрқайсысына жеке айнымалы.

**3. Айнымалыны функция ішінде құру** — функция біткенде айнымалы жойылады да, виджет күйі «ұмытылады». Айнымалыларды `mainloop`-қа дейін, глобал деңгейде немесе `self.` атрибуты ретінде құрыңыз.

**4. `curselection()` бос кортежін тексермеу**

```python
import tkinter as tk
root = tk.Tk()
lb = tk.Listbox(root)
lb.insert(tk.END, "A")
sel = lb.curselection()
# lb.get(sel[0])   -> IndexError: tuple index out of range (ештеңе таңдалмаған)
if sel:
    print(lb.get(sel[0]))
else:
    print("Ештеңе таңдалмаған")
root.destroy()
```

**5. `Combobox`-ты `tk.Combobox` деп жазу** — ол `ttk` модулінде: `from tkinter import ttk` → `ttk.Combobox`.

**6. `Radiobutton`-ның `value`-ін бермеу** — барлық батырмалардың мәні бірдей болып, дұрыс жұмыс істемейді.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| `Checkbutton` | Тәуелсіз «иә/жоқ» жалаушасы |
| `Radiobutton` | Топтан біреуін таңдайтын ауыстырғыш |
| `Listbox` | Элементтер тізімі; бір немесе бірнеше таңдау |
| `ttk.Combobox` | Ашылмалы тізім |
| `variable`, `value` | Таңдау виджетін айнымалымен байланыстыру опциялары |
| `curselection()` | Listbox-та таңдалған индекстер |
| `<<ListboxSelect>>`, `<<ComboboxSelected>>` | Таңдаудың виртуалды оқиғалары |
| `selectmode` | Listbox таңдау режимі |

## Ескерту

- Басқарушы айнымалы (`BooleanVar`, `StringVar`…) виджетпен бірдей уақыт өмір сүруі керек: оны қоқыс жинағыш жойса (мысалы, ол функцияның жергілікті айнымалысы болса), жалауша немесе ауыстырғыш жұмысын тоқтатады.
- `state="readonly"` берілмеген `Combobox` өз мәтініңізді енгізуге мүмкіндік береді, сондықтан `get()` тізімде жоқ мәнді қайтаруы мүмкін.
- `<<ComboboxSelected>>` оқиғасы тек тінтуірмен/пернетақтамен таңдағанда пайда болады; бағдарламалық `set()` және `current()` оны шақырмайды.
- Айнымалының кез келген өзгерісіне жауап беру үшін `var.trace_add("write", callback)` қолданыңыз (Python 3.6+); ескі `trace()` әдісі ескірген.
- `Listbox` тек жолдарды сақтайды: `insert` кезінде сандар мәтінге айналады, оқығанда оларды қайта түрлендіру керек.
- Құжаттама: https://docs.python.org/3/library/tkinter.ttk.html#combobox
