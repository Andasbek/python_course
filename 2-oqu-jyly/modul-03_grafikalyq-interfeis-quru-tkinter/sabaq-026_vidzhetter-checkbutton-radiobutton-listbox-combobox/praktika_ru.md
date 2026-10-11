# Урок 26. Практические задания: Checkbutton, Radiobutton, Listbox, Combobox

## Базовый уровень

### Задание 1. Меню кофейни (Checkbutton)
Добавки к напитку: «Молоко (+200 тг)», «Сироп (+250 тг)», «Дополнительный эспрессо (+300 тг)». Базовая цена — 900 тг. При каждом изменении флажка общая цена должна обновляться.

### Задание 2. Тестовый вопрос (Radiobutton)
Вопрос: «Какой город — столица Казахстана?» — 4 варианта ответа. При нажатии кнопки «Проверить» — «Верно!» (зелёным) или «Неверно, правильный ответ — Астана» (красным).

### Задание 3. Список покупок (Listbox)
Entry + кнопка «Добавить» + Listbox + кнопка «Удалить» + «Очистить» (удалить всё). Внизу надпись «Всего: N товаров».

### Задание 4. День рождения (Combobox)
Три Combobox: день (1–31), месяц (январь...декабрь), год (2008–2016). Кнопка «Вычислить возраст» выводит полный возраст на сегодняшний день (`datetime.date.today()`).

## Продвинутый уровень

### Задание 5. Обмен валюты (Combobox + Radiobutton)
Combobox: валюта (USD, EUR, RUB, CNY) — курсы в словаре (`{"USD": 520, "EUR": 565, "RUB": 5.6, "CNY": 72}`). Radiobutton: направление «тенге → валюта» или «валюта → тенге». Entry — сумма. Результат должен автоматически пересчитываться и при изменении валюты или направления.

### Задание 6. Перенос между двумя списками (Listbox)
Слева Listbox «Все предметы», справа «Мой выбор» (оба `selectmode="extended"`). Посередине кнопки «→» и «←» переносят выбранные элементы из одного списка в другой. В правом списке не больше 5 элементов (если больше — предупреждение).

### Задание 7. Фильтр поиска
При каждом вводе текста в Entry (`StringVar.trace_add`) в Listbox остаются только города, содержащие этот текст (не менее 15 городов Казахстана).

## ★ Олимпиадная / творческая задача

### Задание 8. «Конструктор пиццы» (ООП)
Класс `PizzaApp(tk.Tk)`:
- Radiobutton — размер (25 см — 2500 тг, 30 см — 3200 тг, 35 см — 3900 тг);
- Combobox — вид теста (тонкое, толстое, с сырным бортиком +500 тг);
- Checkbutton — 6 добавок (каждая со своей ценой, создаются циклом из словаря);
- «Добавить в корзину» — описание и цена заказа добавляются в Listbox;
- общая сумма корзины; удаление выбранного заказа из корзины.

## Домашнее задание

1. Завершите задание 4: если выбранная дата невозможна (31 февраля), выводится сообщение «Такой даты нет» (`try/except ValueError`).
2. Создайте для своего проекта окно «Настройки»: тема (Radiobutton: светлая/тёмная), язык (Combobox), звук (Checkbutton).
3. Загрузите на GitHub под именем `gui/lesson26_choice.py`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import tkinter as tk

BASE = 900
EXTRAS = {"Молоко": 200, "Сироп": 250, "Дополнительный эспрессо": 300}


def update():
    total = BASE + sum(price for name, price in EXTRAS.items() if vars_[name].get())
    total_lbl.config(text=f"Итого: {total} тг")


root = tk.Tk()
root.title("Кофейня")
tk.Label(root, text=f"Латте — {BASE} тг", font=("Arial", 14)).pack(pady=5)
vars_ = {}
for name, price in EXTRAS.items():
    v = tk.BooleanVar()
    tk.Checkbutton(root, text=f"{name} (+{price} тг)", variable=v,
                   command=update).pack(anchor="w", padx=20)
    vars_[name] = v
total_lbl = tk.Label(root, text=f"Итого: {BASE} тг", font=("Arial", 14, "bold"))
total_lbl.pack(pady=10)
root.mainloop()
```

**Задание 2**
```python
import tkinter as tk


def check():
    if answer.get() == "Астана":
        result.config(text="Верно!", fg="green")
    else:
        result.config(text="Неверно, правильный ответ — Астана", fg="red")


root = tk.Tk()
root.title("Тест")
tk.Label(root, text="Какой город — столица Казахстана?", font=("Arial", 13)).pack(pady=5, padx=20)
answer = tk.StringVar(value="")
for city in ["Алматы", "Астана", "Шымкент", "Туркестан"]:
    tk.Radiobutton(root, text=city, variable=answer, value=city).pack(anchor="w", padx=40)
tk.Button(root, text="Проверить", command=check).pack(pady=5)
result = tk.Label(root, text="")
result.pack(pady=5)
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk


def refresh():
    count.config(text=f"Всего: {lb.size()} товаров")


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
root.title("Список покупок")
entry = tk.Entry(root, width=30)
entry.pack(padx=10, pady=5)
entry.bind("<Return>", add)
tk.Button(root, text="Добавить", command=add).pack()
lb = tk.Listbox(root, width=30, height=8)
lb.pack(padx=10, pady=5)
bar = tk.Frame(root)
bar.pack()
tk.Button(bar, text="Удалить", command=remove).pack(side="left", padx=5)
tk.Button(bar, text="Очистить", command=clear).pack(side="left", padx=5)
count = tk.Label(root, text="Всего: 0 товаров")
count.pack(pady=5)
root.mainloop()
```

**Задание 4** (с домашним заданием)
```python
import datetime
import tkinter as tk
from tkinter import ttk

MONTHS = ["январь", "февраль", "март", "апрель", "май", "июнь",
          "июль", "август", "сентябрь", "октябрь", "ноябрь", "декабрь"]


def calc_age():
    try:
        birth = datetime.date(int(year.get()), month.current() + 1, int(day.get()))
    except ValueError:
        result.config(text="Такой даты нет", fg="red")
        return
    today = datetime.date.today()
    age = today.year - birth.year - ((today.month, today.day) < (birth.month, birth.day))
    result.config(text=f"Ваш возраст: {age}", fg="black")


root = tk.Tk()
root.title("Возраст")
day = ttk.Combobox(root, values=[str(d) for d in range(1, 32)], width=4, state="readonly")
day.current(0)
month = ttk.Combobox(root, values=MONTHS, width=10, state="readonly")
month.current(0)
year = ttk.Combobox(root, values=[str(y) for y in range(2008, 2017)], width=6, state="readonly")
year.current(0)
for w in (day, month, year):
    w.pack(side="left", padx=3, pady=10)
tk.Button(root, text="Вычислить возраст", command=calc_age).pack(side="left", padx=5)
result = tk.Label(root, text="")
result.pack(side="left", padx=5)
root.mainloop()
```

**Задание 5**
```python
import tkinter as tk
from tkinter import ttk

RATES = {"USD": 520, "EUR": 565, "RUB": 5.6, "CNY": 72}


def recalc(*args):
    try:
        amount = float(amount_var.get())
    except ValueError:
        result.config(text="Введите сумму")
        return
    cur = currency.get()
    rate = RATES[cur]
    if direction.get() == "to_cur":
        result.config(text=f"{amount:g} KZT = {amount / rate:.2f} {cur}")
    else:
        result.config(text=f"{amount:g} {cur} = {amount * rate:.2f} KZT")


root = tk.Tk()
root.title("Обмен")
amount_var = tk.StringVar(value="10000")
amount_var.trace_add("write", recalc)
tk.Entry(root, textvariable=amount_var, justify="center").pack(padx=10, pady=5)
currency = ttk.Combobox(root, values=list(RATES), state="readonly")
currency.current(0)
currency.pack(pady=5)
currency.bind("<<ComboboxSelected>>", recalc)
direction = tk.StringVar(value="to_cur")
tk.Radiobutton(root, text="тенге → валюта", variable=direction, value="to_cur",
               command=recalc).pack(anchor="w", padx=10)
tk.Radiobutton(root, text="валюта → тенге", variable=direction, value="to_kzt",
               command=recalc).pack(anchor="w", padx=10)
result = tk.Label(root, text="", font=("Arial", 13))
result.pack(pady=10)
recalc()
root.mainloop()
```

**Задание 6**
```python
import tkinter as tk

SUBJECTS = ["Алгебра", "Геометрия", "Физика", "Химия", "Биология",
            "Информатика", "История", "География", "Английский язык"]
LIMIT = 5


def move(src, dst, check_limit=False):
    selected = list(src.curselection())
    items = [src.get(i) for i in selected]
    if check_limit and dst.size() + len(items) > LIMIT:
        info.config(text=f"Нельзя выбрать больше {LIMIT} предметов!", fg="red")
        return
    for i in reversed(selected):
        src.delete(i)
    for item in items:
        dst.insert(tk.END, item)
    info.config(text=f"Выбрано: {right.size()}/{LIMIT}", fg="black")


root = tk.Tk()
root.title("Предметы по выбору")
left = tk.Listbox(root, selectmode="extended", height=10, exportselection=False)
left.insert(tk.END, *SUBJECTS)
left.grid(row=0, column=0, rowspan=2, padx=5, pady=5)
right = tk.Listbox(root, selectmode="extended", height=10, exportselection=False)
right.grid(row=0, column=2, rowspan=2, padx=5, pady=5)
tk.Button(root, text="→", width=4,
          command=lambda: move(left, right, True)).grid(row=0, column=1, sticky="s", pady=3)
tk.Button(root, text="←", width=4,
          command=lambda: move(right, left)).grid(row=1, column=1, sticky="n", pady=3)
info = tk.Label(root, text=f"Выбрано: 0/{LIMIT}")
info.grid(row=2, column=0, columnspan=3, pady=5)
root.mainloop()
```

**Задание 7**
```python
import tkinter as tk

CITIES = ["Алматы", "Астана", "Шымкент", "Караганда", "Актобе", "Тараз", "Павлодар",
          "Усть-Каменогорск", "Семей", "Атырау", "Костанай", "Кызылорда", "Уральск",
          "Петропавловск", "Актау", "Туркестан", "Кокшетау", "Талдыкорган", "Жезказган",
          "Экибастуз"]


def refilter(*args):
    text = query.get().strip().lower()
    lb.delete(0, tk.END)
    for city in CITIES:
        if text in city.lower():
            lb.insert(tk.END, city)


root = tk.Tk()
root.title("Города")
query = tk.StringVar()
query.trace_add("write", refilter)
tk.Entry(root, textvariable=query, width=25).pack(padx=10, pady=5)
lb = tk.Listbox(root, width=25, height=12)
lb.pack(padx=10, pady=5)
refilter()
root.mainloop()
```

**Задание 8**
```python
import tkinter as tk
from tkinter import ttk

SIZES = {"25 см": 2500, "30 см": 3200, "35 см": 3900}
DOUGH = {"Тонкое": 0, "Толстое": 0, "С сырным бортиком": 500}
TOPPINGS = {"Сыр": 300, "Колбаса": 400, "Курица": 450,
            "Грибы": 250, "Помидоры": 200, "Оливки": 250}


class PizzaApp(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Конструктор пиццы")
        self.prices = []
        self.size = tk.StringVar(value="30 см")
        self.topping_vars = {}
        self.build()

    def build(self):
        size_frame = tk.LabelFrame(self, text="Размер", padx=5, pady=5)
        size_frame.grid(row=0, column=0, sticky="nsew", padx=5, pady=5)
        for name, price in SIZES.items():
            tk.Radiobutton(size_frame, text=f"{name} — {price} тг",
                           variable=self.size, value=name).pack(anchor="w")

        dough_frame = tk.LabelFrame(self, text="Тесто", padx=5, pady=5)
        dough_frame.grid(row=1, column=0, sticky="nsew", padx=5, pady=5)
        self.dough = ttk.Combobox(dough_frame, values=list(DOUGH), state="readonly")
        self.dough.current(0)
        self.dough.pack()

        top_frame = tk.LabelFrame(self, text="Добавки", padx=5, pady=5)
        top_frame.grid(row=0, column=1, rowspan=2, sticky="nsew", padx=5, pady=5)
        for name, price in TOPPINGS.items():
            var = tk.BooleanVar()
            tk.Checkbutton(top_frame, text=f"{name} (+{price})", variable=var).pack(anchor="w")
            self.topping_vars[name] = var

        tk.Button(self, text="Добавить в корзину", command=self.add_to_cart).grid(
            row=2, column=0, columnspan=2, pady=5)
        self.cart = tk.Listbox(self, width=60, height=6)
        self.cart.grid(row=3, column=0, columnspan=2, padx=5)
        tk.Button(self, text="Удалить", command=self.remove).grid(row=4, column=0, pady=5)
        self.total_lbl = tk.Label(self, text="Итого: 0 тг", font=("Arial", 12, "bold"))
        self.total_lbl.grid(row=4, column=1)

    def add_to_cart(self):
        tops = [n for n, v in self.topping_vars.items() if v.get()]
        price = SIZES[self.size.get()] + DOUGH[self.dough.get()]
        price += sum(TOPPINGS[t] for t in tops)
        desc = f"{self.size.get()}, {self.dough.get()}, {', '.join(tops) or 'без добавок'}"
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
        self.total_lbl.config(text=f"Итого: {sum(self.prices)} тг")


if __name__ == "__main__":
    PizzaApp().mainloop()
```

</details>
