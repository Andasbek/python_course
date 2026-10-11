# Сабақ 26. Практикалық тапсырмалар: Checkbutton, Radiobutton, Listbox, Combobox

## Базалық деңгей

### 1-тапсырма. Кофехана мәзірі (Checkbutton)
Сусынға қосымшалар: «Сүт (+200 тг)», «Сироп (+250 тг)», «Қосымша эспрессо (+300 тг)». Негізгі баға — 900 тг. Әр жалауша өзгерген сайын жалпы баға жаңарып тұрсын.

### 2-тапсырма. Тест сұрағы (Radiobutton)
Сұрақ: «Қазақстанның астанасы қай қала?» — 4 жауап нұсқасы. «Тексеру» батырмасы басылғанда «Дұрыс!» (жасыл) немесе «Қате, дұрыс жауап — Астана» (қызыл).

### 3-тапсырма. Сатып алу тізімі (Listbox)
Entry + «Қосу» батырмасы + Listbox + «Өшіру» батырмасы + «Тазарту» (бәрін өшіру). Төменде «Барлығы: N тауар» жазуы.

### 4-тапсырма. Туған күн (Combobox)
Үш Combobox: күн (1–31), ай (қаңтар...желтоқсан), жыл (2008–2016). «Жасын есептеу» батырмасы бүгінгі күнге дейінгі толық жасты шығарсын (`datetime.date.today()`).

## Кеңейтілген деңгей

### 5-тапсырма. Валюта айырбастау (Combobox + Radiobutton)
Combobox: валюта (USD, EUR, RUB, CNY) — бағамдар сөздікте (`{"USD": 520, "EUR": 565, "RUB": 5.6, "CNY": 72}`). Radiobutton: бағыты «теңге → валюта» немесе «валюта → теңге». Entry — сома. Нәтиже валюта не бағыт өзгерген сайын да автоматты қайта есептелсін.

### 6-тапсырма. Екі тізім арасында тасымалдау (Listbox)
Сол жақта «Барлық пәндер» Listbox-ы, оң жақта «Менің таңдауым» (екеуі де `selectmode="extended"`). Ортасында «→» және «←» батырмалары таңдалған элементтерді бір тізімнен екіншісіне көшірсін. Оң жақтағы тізім 5-тен аспасын (асса — ескерту).

### 7-тапсырма. Іздеу сүзгісі
Entry-ге мәтін жазылған сайын (`StringVar.trace_add`) Listbox-та тек сол мәтінді қамтитын қалалар қалсын (Қазақстанның кемінде 15 қаласы).

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. «Пицца құрастырушы» (ОБП)
`PizzaApp(tk.Tk)` класы:
- Radiobutton — өлшемі (25 см — 2500 тг, 30 см — 3200 тг, 35 см — 3900 тг);
- Combobox — қамыр түрі (жұқа, қалың, ірімшік жиекті +500 тг);
- Checkbutton — 6 қосымша (әрқайсысы өз бағасымен, сөздіктен циклмен жасалады);
- «Себетке қосу» — тапсырыс сипаттамасы мен бағасы Listbox-қа қосылады;
- себеттің жалпы сомасы; таңдалған тапсырысты себеттен өшіру.

## Үй тапсырмасы

1. 4-тапсырманы аяқтаңыз: таңдалған күн мүмкін болмаса (31 ақпан), «Мұндай күн жоқ» деген хабар шықсын (`try/except ValueError`).
2. Өз жобаңыз үшін «Баптаулар» терезесін жасаңыз: тақырып (Radiobutton: жарық/қараңғы), тіл (Combobox), дыбыс (Checkbutton).
3. GitHub-қа `gui/lesson26_choice.py` деп жүктеңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import tkinter as tk

BASE = 900
EXTRAS = {"Сүт": 200, "Сироп": 250, "Қосымша эспрессо": 300}


def update():
    total = BASE + sum(price for name, price in EXTRAS.items() if vars_[name].get())
    total_lbl.config(text=f"Барлығы: {total} тг")


root = tk.Tk()
root.title("Кофехана")
tk.Label(root, text=f"Латте — {BASE} тг", font=("Arial", 14)).pack(pady=5)
vars_ = {}
for name, price in EXTRAS.items():
    v = tk.BooleanVar()
    tk.Checkbutton(root, text=f"{name} (+{price} тг)", variable=v,
                   command=update).pack(anchor="w", padx=20)
    vars_[name] = v
total_lbl = tk.Label(root, text=f"Барлығы: {BASE} тг", font=("Arial", 14, "bold"))
total_lbl.pack(pady=10)
root.mainloop()
```

**2-тапсырма**
```python
import tkinter as tk


def check():
    if answer.get() == "Астана":
        result.config(text="Дұрыс!", fg="green")
    else:
        result.config(text="Қате, дұрыс жауап — Астана", fg="red")


root = tk.Tk()
root.title("Тест")
tk.Label(root, text="Қазақстанның астанасы қай қала?", font=("Arial", 13)).pack(pady=5, padx=20)
answer = tk.StringVar(value="")
for city in ["Алматы", "Астана", "Шымкент", "Түркістан"]:
    tk.Radiobutton(root, text=city, variable=answer, value=city).pack(anchor="w", padx=40)
tk.Button(root, text="Тексеру", command=check).pack(pady=5)
result = tk.Label(root, text="")
result.pack(pady=5)
root.mainloop()
```

**3-тапсырма**
```python
import tkinter as tk


def refresh():
    count.config(text=f"Барлығы: {lb.size()} тауар")


def add(event=None):
    text = entry.get().strip()
    if text:
        lb.insert(tk.END, text)
        entry.delete(0, tk.END)
        refresh()


def remove():
    for i in reversed(lb.curselection()):
        lb.delete(i)
    refresh()


def clear():
    lb.delete(0, tk.END)
    refresh()


root = tk.Tk()
root.title("Сатып алу тізімі")
entry = tk.Entry(root, width=30)
entry.pack(padx=10, pady=5)
entry.bind("<Return>", add)
tk.Button(root, text="Қосу", command=add).pack()
lb = tk.Listbox(root, width=30, height=8)
lb.pack(padx=10, pady=5)
bar = tk.Frame(root)
bar.pack()
tk.Button(bar, text="Өшіру", command=remove).pack(side="left", padx=5)
tk.Button(bar, text="Тазарту", command=clear).pack(side="left", padx=5)
count = tk.Label(root, text="Барлығы: 0 тауар")
count.pack(pady=5)
root.mainloop()
```

**4-тапсырма** (үй тапсырмасымен)
```python
import datetime
import tkinter as tk
from tkinter import ttk

MONTHS = ["қаңтар", "ақпан", "наурыз", "сәуір", "мамыр", "маусым",
          "шілде", "тамыз", "қыркүйек", "қазан", "қараша", "желтоқсан"]


def calc_age():
    try:
        birth = datetime.date(int(year.get()), month.current() + 1, int(day.get()))
    except ValueError:
        result.config(text="Мұндай күн жоқ", fg="red")
        return
    today = datetime.date.today()
    age = today.year - birth.year - ((today.month, today.day) < (birth.month, birth.day))
    result.config(text=f"Сіздің жасыңыз: {age}", fg="black")


root = tk.Tk()
root.title("Жас")
day = ttk.Combobox(root, values=[str(d) for d in range(1, 32)], width=4, state="readonly")
day.current(0)
month = ttk.Combobox(root, values=MONTHS, width=10, state="readonly")
month.current(0)
year = ttk.Combobox(root, values=[str(y) for y in range(2008, 2017)], width=6, state="readonly")
year.current(0)
for w in (day, month, year):
    w.pack(side="left", padx=3, pady=10)
tk.Button(root, text="Жасын есептеу", command=calc_age).pack(side="left", padx=5)
result = tk.Label(root, text="")
result.pack(side="left", padx=5)
root.mainloop()
```

**5-тапсырма**
```python
import tkinter as tk
from tkinter import ttk

RATES = {"USD": 520, "EUR": 565, "RUB": 5.6, "CNY": 72}


def recalc(*args):
    try:
        amount = float(amount_var.get())
    except ValueError:
        result.config(text="Сома енгізіңіз")
        return
    cur = currency.get()
    rate = RATES[cur]
    if direction.get() == "to_cur":
        result.config(text=f"{amount:g} KZT = {amount / rate:.2f} {cur}")
    else:
        result.config(text=f"{amount:g} {cur} = {amount * rate:.2f} KZT")


root = tk.Tk()
root.title("Айырбастау")
amount_var = tk.StringVar(value="10000")
amount_var.trace_add("write", recalc)
tk.Entry(root, textvariable=amount_var, justify="center").pack(padx=10, pady=5)
currency = ttk.Combobox(root, values=list(RATES), state="readonly")
currency.current(0)
currency.pack(pady=5)
currency.bind("<<ComboboxSelected>>", recalc)
direction = tk.StringVar(value="to_cur")
tk.Radiobutton(root, text="теңге → валюта", variable=direction, value="to_cur",
               command=recalc).pack(anchor="w", padx=10)
tk.Radiobutton(root, text="валюта → теңге", variable=direction, value="to_kzt",
               command=recalc).pack(anchor="w", padx=10)
result = tk.Label(root, text="", font=("Arial", 13))
result.pack(pady=10)
recalc()
root.mainloop()
```

**6-тапсырма**
```python
import tkinter as tk

SUBJECTS = ["Алгебра", "Геометрия", "Физика", "Химия", "Биология",
            "Информатика", "Тарих", "География", "Ағылшын тілі"]
LIMIT = 5


def move(src, dst, check_limit=False):
    selected = list(src.curselection())
    items = [src.get(i) for i in selected]
    if check_limit and dst.size() + len(items) > LIMIT:
        info.config(text=f"{LIMIT} пәннен артық таңдауға болмайды!", fg="red")
        return
    for i in reversed(selected):
        src.delete(i)
    for item in items:
        dst.insert(tk.END, item)
    info.config(text=f"Таңдалды: {right.size()}/{LIMIT}", fg="black")


root = tk.Tk()
root.title("Таңдау пәндері")
left = tk.Listbox(root, selectmode="extended", height=10, exportselection=False)
left.insert(tk.END, *SUBJECTS)
left.grid(row=0, column=0, rowspan=2, padx=5, pady=5)
right = tk.Listbox(root, selectmode="extended", height=10, exportselection=False)
right.grid(row=0, column=2, rowspan=2, padx=5, pady=5)
tk.Button(root, text="→", width=4,
          command=lambda: move(left, right, True)).grid(row=0, column=1, sticky="s", pady=3)
tk.Button(root, text="←", width=4,
          command=lambda: move(right, left)).grid(row=1, column=1, sticky="n", pady=3)
info = tk.Label(root, text=f"Таңдалды: 0/{LIMIT}")
info.grid(row=2, column=0, columnspan=3, pady=5)
root.mainloop()
```

**7-тапсырма**
```python
import tkinter as tk

CITIES = ["Алматы", "Астана", "Шымкент", "Қарағанды", "Ақтөбе", "Тараз", "Павлодар",
          "Өскемен", "Семей", "Атырау", "Қостанай", "Қызылорда", "Орал", "Петропавл",
          "Ақтау", "Түркістан", "Көкшетау", "Талдықорған", "Жезқазған", "Екібастұз"]


def refilter(*args):
    text = query.get().strip().lower()
    lb.delete(0, tk.END)
    for city in CITIES:
        if text in city.lower():
            lb.insert(tk.END, city)


root = tk.Tk()
root.title("Қалалар")
query = tk.StringVar()
query.trace_add("write", refilter)
tk.Entry(root, textvariable=query, width=25).pack(padx=10, pady=5)
lb = tk.Listbox(root, width=25, height=12)
lb.pack(padx=10, pady=5)
refilter()
root.mainloop()
```

**8-тапсырма**
```python
import tkinter as tk
from tkinter import ttk

SIZES = {"25 см": 2500, "30 см": 3200, "35 см": 3900}
DOUGH = {"Жұқа": 0, "Қалың": 0, "Ірімшік жиекті": 500}
TOPPINGS = {"Ірімшік": 300, "Шұжық": 400, "Тауық еті": 450,
            "Саңырауқұлақ": 250, "Қызанақ": 200, "Зәйтүн": 250}


class PizzaApp(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Пицца құрастырушы")
        self.prices = []
        self.size = tk.StringVar(value="30 см")
        self.topping_vars = {}
        self.build()

    def build(self):
        size_frame = tk.LabelFrame(self, text="Өлшемі", padx=5, pady=5)
        size_frame.grid(row=0, column=0, sticky="nsew", padx=5, pady=5)
        for name, price in SIZES.items():
            tk.Radiobutton(size_frame, text=f"{name} — {price} тг",
                           variable=self.size, value=name).pack(anchor="w")

        dough_frame = tk.LabelFrame(self, text="Қамыр", padx=5, pady=5)
        dough_frame.grid(row=1, column=0, sticky="nsew", padx=5, pady=5)
        self.dough = ttk.Combobox(dough_frame, values=list(DOUGH), state="readonly")
        self.dough.current(0)
        self.dough.pack()

        top_frame = tk.LabelFrame(self, text="Қосымшалар", padx=5, pady=5)
        top_frame.grid(row=0, column=1, rowspan=2, sticky="nsew", padx=5, pady=5)
        for name, price in TOPPINGS.items():
            var = tk.BooleanVar()
            tk.Checkbutton(top_frame, text=f"{name} (+{price})", variable=var).pack(anchor="w")
            self.topping_vars[name] = var

        tk.Button(self, text="Себетке қосу", command=self.add_to_cart).grid(
            row=2, column=0, columnspan=2, pady=5)
        self.cart = tk.Listbox(self, width=60, height=6)
        self.cart.grid(row=3, column=0, columnspan=2, padx=5)
        tk.Button(self, text="Өшіру", command=self.remove).grid(row=4, column=0, pady=5)
        self.total_lbl = tk.Label(self, text="Барлығы: 0 тг", font=("Arial", 12, "bold"))
        self.total_lbl.grid(row=4, column=1)

    def add_to_cart(self):
        tops = [n for n, v in self.topping_vars.items() if v.get()]
        price = SIZES[self.size.get()] + DOUGH[self.dough.get()]
        price += sum(TOPPINGS[t] for t in tops)
        desc = f"{self.size.get()}, {self.dough.get()}, {', '.join(tops) or 'қосымшасыз'}"
        self.cart.insert(tk.END, f"{desc} — {price} тг")
        self.prices.append(price)
        self.update_total()

    def remove(self):
        sel = self.cart.curselection()
        if sel:
            self.cart.delete(sel[0])
            self.prices.pop(sel[0])
            self.update_total()

    def update_total(self):
        self.total_lbl.config(text=f"Барлығы: {sum(self.prices)} тг")


if __name__ == "__main__":
    PizzaApp().mainloop()
```

</details>
