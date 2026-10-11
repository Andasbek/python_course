# Урок 26. Виджеты: Checkbutton, Radiobutton, Listbox, Combobox.

## Темы

1. Обзор виджетов выбора
2. Checkbutton — флажок
3. Radiobutton — переключатель
4. Listbox — список
5. ttk.Combobox — выпадающий список
6. Всё вместе: форма «Запись в кружок»
7. Какой виджет выбрать?
8. Частые ошибки
9. Основные понятия

## 1. Обзор виджетов выбора

| Виджет | Внешний вид | Вид выбора | Пример |
|---|---|---|---|
| `Checkbutton` | ☑ флажок | Несколько независимых «да/нет» | «Добавить сыр», «Согласен» |
| `Radiobutton` | ◉ кружок | **Один** из группы | Пол, способ оплаты |
| `Listbox` | Окно списка | Один или несколько элементов | Список предметов, плейлист |
| `ttk.Combobox` | Выпадающий список ▼ | Один (или ввести своё значение) | Город, класс, месяц |

## 2. Checkbutton — флажок

К каждому `Checkbutton` привязывается **своя** переменная:

```python
import tkinter as tk


def show():
    items = []
    if cheese.get():
        items.append("сыр")
    if mushrooms.get():
        items.append("грибы")
    if olives.get():
        items.append("оливки")
    result.config(text="Добавки: " + (", ".join(items) if items else "нет"))


root = tk.Tk()
root.title("Пицца")
cheese = tk.BooleanVar(value=True)      # отмечен по умолчанию
mushrooms = tk.BooleanVar()
olives = tk.BooleanVar()

tk.Checkbutton(root, text="Сыр", variable=cheese, command=show).pack(anchor="w")
tk.Checkbutton(root, text="Грибы", variable=mushrooms, command=show).pack(anchor="w")
tk.Checkbutton(root, text="Оливки", variable=olives, command=show).pack(anchor="w")
result = tk.Label(root, text="")
result.pack(pady=10, padx=20)
show()
root.mainloop()
```

| Опция | Смысл |
|---|---|
| `variable` | Связанная переменная (`BooleanVar`, `IntVar`, `StringVar`) |
| `onvalue`, `offvalue` | Значение, записываемое в переменную, когда флажок отмечен/не отмечен (по умолчанию 1 и 0) |
| `command` | Функция, вызываемая при каждом изменении состояния |

Пример `onvalue`/`offvalue`: `tk.Checkbutton(root, text="Подписаться на новости", variable=sub, onvalue="да", offvalue="нет")`, где `sub = tk.StringVar(value="нет")`.

### «Согласен» — активация кнопки

```python
import tkinter as tk


def toggle():
    btn.config(state="normal" if agree.get() else "disabled")


root = tk.Tk()
root.title("Правила")
agree = tk.BooleanVar()
tk.Checkbutton(root, text="Я ознакомился с правилами кружка", variable=agree,
               command=toggle).pack(padx=20, pady=10)
btn = tk.Button(root, text="Зарегистрироваться", state="disabled")
btn.pack(pady=5)
root.mainloop()
```

## 3. Radiobutton — переключатель

Все `Radiobutton` одной группы привязываются к **одной общей переменной**, у каждого — своё `value`. При выборе одного остальные автоматически «выключаются».

```python
import tkinter as tk


def show():
    result.config(text=f"Выбрано: {payment.get()}")


root = tk.Tk()
root.title("Способ оплаты")
payment = tk.StringVar(value="Kaspi QR")

for option in ["Kaspi QR", "Банковская карта", "Наличные"]:
    tk.Radiobutton(root, text=option, variable=payment, value=option,
                   command=show).pack(anchor="w", padx=20)

result = tk.Label(root, text="Выбрано: Kaspi QR", fg="blue")
result.pack(pady=10)
root.mainloop()
```

С числовыми значениями (например, уровень):

```python
import tkinter as tk

root = tk.Tk()
root.title("Уровень")
level = tk.IntVar(value=1)
levels = {1: "Лёгкий", 2: "Средний", 3: "Сложный"}
for value, text in levels.items():
    tk.Radiobutton(root, text=text, variable=level, value=value).pack(side="left", padx=5)
tk.Button(root, text="Начать",
          command=lambda: print("Уровень:", levels[level.get()])).pack(side="left", padx=10)
root.mainloop()
```

**Запомните:** Checkbutton — у каждого своя переменная; Radiobutton — одна переменная на всю группу.

## 4. Listbox — список

```python
import tkinter as tk


def add():
    text = entry.get().strip()
    if text:
        listbox.insert(tk.END, text)       # добавить в конец
        entry.delete(0, tk.END)


def remove():
    selected = listbox.curselection()     # кортеж выбранных индексов
    if selected:
        listbox.delete(selected[0])


def on_select(event):
    selected = listbox.curselection()
    if selected:
        info.config(text=f"Выбрано: {listbox.get(selected[0])}")


root = tk.Tk()
root.title("Предметы")
listbox = tk.Listbox(root, height=6, width=25)
for subject in ["Математика", "Физика", "Информатика"]:
    listbox.insert(tk.END, subject)
listbox.pack(padx=10, pady=5)
listbox.bind("<<ListboxSelect>>", on_select)

entry = tk.Entry(root, width=25)
entry.pack(pady=2)
tk.Button(root, text="Добавить", command=add).pack(side="left", padx=10, pady=5)
tk.Button(root, text="Удалить", command=remove).pack(side="right", padx=10, pady=5)
info = tk.Label(root, text="")
info.pack(side="bottom", pady=5)
root.mainloop()
```

### Методы и опции Listbox

| Метод / опция | Смысл |
|---|---|
| `insert(index, *items)` | Добавить элемент(ы): `insert(tk.END, "А", "Б")` |
| `delete(first, last=None)` | Удалить; `delete(0, tk.END)` — все |
| `get(index)` / `get(0, tk.END)` | Один элемент / все элементы (кортеж) |
| `curselection()` | **Индексы** выбранных элементов (кортеж; если ничего не выбрано — пустой) |
| `size()` | Количество элементов |
| `selection_set(i)` / `selection_clear(0, tk.END)` | Выбрать из кода / снять выбор |
| `selectmode` | `"browse"` (один, по умолчанию), `"single"`, `"multiple"` (отметка щелчком), `"extended"` (Ctrl/Shift) |
| `height`, `width` | Количество видимых строк, ширина |
| `<<ListboxSelect>>` | Виртуальное событие при изменении выбора |

Множественный выбор:

```python
import tkinter as tk


def show():
    chosen = [lb.get(i) for i in lb.curselection()]
    print("Выбранные кружки:", chosen)


root = tk.Tk()
root.title("Кружки")
lb = tk.Listbox(root, selectmode="multiple", height=5)
lb.insert(tk.END, "Python", "Робототехника", "3D-моделирование", "Шахматы", "Домбра")
lb.pack(padx=10, pady=5)
tk.Button(root, text="Показать", command=show).pack(pady=5)
root.mainloop()
```

## 5. ttk.Combobox — выпадающий список

`Combobox` находится в отдельном модуле — `tkinter.ttk` (Themed Tk — тематические виджеты с современным видом).

```python
import tkinter as tk
from tkinter import ttk

CITIES = ["Алматы", "Астана", "Шымкент", "Караганда", "Актобе", "Атырау"]


def on_select(event):
    info.config(text=f"Вы выбрали город {city_var.get()}")


root = tk.Tk()
root.title("Город")
city_var = tk.StringVar()
combo = ttk.Combobox(root, textvariable=city_var, values=CITIES,
                     state="readonly", width=20)
combo.current(0)                         # выбрать первый элемент
combo.pack(padx=20, pady=10)
combo.bind("<<ComboboxSelected>>", on_select)
info = tk.Label(root, text="")
info.pack(pady=5)
root.mainloop()
```

| Метод / опция | Смысл |
|---|---|
| `values` | Элементы списка |
| `state="readonly"` | Только выбор из списка (нельзя вводить свой текст) |
| `current(i)` | Выбрать `i`-й элемент; `current()` — индекс выбранного |
| `get()` / `set(text)` | Получить / установить значение |
| `<<ComboboxSelected>>` | Событие выбора из списка |
| `combo["values"] = [...]` | Изменить список позже |

### Зависимые Combobox

```python
import tkinter as tk
from tkinter import ttk

REGIONS = {
    "Алматинская область": ["Конаев", "Есик", "Талгар"],
    "Туркестанская область": ["Туркестан", "Кентау", "Сарыагаш"],
    "Мангистауская область": ["Актау", "Жанаозен", "Форт-Шевченко"],
}


def on_region(event):
    towns = REGIONS[region.get()]
    town["values"] = towns
    town.current(0)


root = tk.Tk()
root.title("Адрес")
region = ttk.Combobox(root, values=list(REGIONS), state="readonly")
region.grid(row=0, column=1, padx=5, pady=5)
tk.Label(root, text="Область:").grid(row=0, column=0, sticky="e")
town = ttk.Combobox(root, state="readonly")
town.grid(row=1, column=1, padx=5, pady=5)
tk.Label(root, text="Город:").grid(row=1, column=0, sticky="e")
region.bind("<<ComboboxSelected>>", on_region)
root.mainloop()
```

## 6. Всё вместе: форма «Запись в кружок»

```python
import tkinter as tk
from tkinter import ttk


def submit():
    langs = [name for name, var in lang_vars.items() if var.get()]
    lines = [
        f"Имя: {name_entry.get()}",
        f"Класс: {grade.get()}",
        f"Пол: {gender.get()}",
        f"Знает языки: {', '.join(langs) if langs else '—'}",
        f"Время: {time_box.get(time_box.curselection()[0]) if time_box.curselection() else '—'}",
    ]
    result.config(text="\n".join(lines))


root = tk.Tk()
root.title("Запись в кружок")

tk.Label(root, text="Имя:").grid(row=0, column=0, sticky="e", padx=5, pady=3)
name_entry = tk.Entry(root)
name_entry.grid(row=0, column=1, sticky="w", padx=5)

tk.Label(root, text="Класс:").grid(row=1, column=0, sticky="e", padx=5, pady=3)
grade = ttk.Combobox(root, values=[str(g) for g in range(5, 12)], state="readonly", width=5)
grade.current(2)
grade.grid(row=1, column=1, sticky="w", padx=5)

tk.Label(root, text="Пол:").grid(row=2, column=0, sticky="e", padx=5, pady=3)
gender = tk.StringVar(value="мальчик")
gframe = tk.Frame(root)
gframe.grid(row=2, column=1, sticky="w")
tk.Radiobutton(gframe, text="мальчик", variable=gender, value="мальчик").pack(side="left")
tk.Radiobutton(gframe, text="девочка", variable=gender, value="девочка").pack(side="left")

tk.Label(root, text="Языки:").grid(row=3, column=0, sticky="ne", padx=5, pady=3)
lframe = tk.Frame(root)
lframe.grid(row=3, column=1, sticky="w")
lang_vars = {}
for lang in ["Python", "Scratch", "C++"]:
    var = tk.BooleanVar()
    tk.Checkbutton(lframe, text=lang, variable=var).pack(anchor="w")
    lang_vars[lang] = var

tk.Label(root, text="Время:").grid(row=4, column=0, sticky="ne", padx=5, pady=3)
time_box = tk.Listbox(root, height=3, exportselection=False)
time_box.insert(tk.END, "Пн/Ср 15:00", "Вт/Чт 16:00", "Сб 10:00")
time_box.grid(row=4, column=1, sticky="w", padx=5)

tk.Button(root, text="Отправить", command=submit).grid(row=5, column=0, columnspan=2, pady=8)
result = tk.Label(root, text="", justify="left", fg="blue")
result.grid(row=6, column=0, columnspan=2, padx=10, pady=5)
root.mainloop()
```

`exportselection=False` — чтобы выбор в Listbox не «пропадал» при выделении текста в другом виджете.

## 7. Какой виджет выбрать?

| Ситуация | Виджет |
|---|---|
| Один из 2–5 вариантов, все должны быть видны | `Radiobutton` |
| Один из многих вариантов (город, страна, месяц) | `Combobox` |
| Каждая опция — независимое «да/нет» | `Checkbutton` |
| Длинный список, добавление/удаление, множественный выбор | `Listbox` |

## Частые ошибки

**1. Разные переменные у Radiobutton** — можно выбрать все одновременно. Одна группа — одна переменная.

**2. Одна переменная у нескольких Checkbutton** — при нажатии одного отмечаются все. У каждого — своя переменная.

**3. Создание переменной внутри функции** — после завершения функции переменная удаляется, и состояние виджета «забывается». Создавайте переменные до `mainloop`, на глобальном уровне или как атрибут `self.`.

**4. Не проверять пустой кортеж `curselection()`**

```python
import tkinter as tk
root = tk.Tk()
lb = tk.Listbox(root)
lb.insert(tk.END, "A")
sel = lb.curselection()
# lb.get(sel[0])   -> IndexError: tuple index out of range (ничего не выбрано)
if sel:
    print(lb.get(sel[0]))
else:
    print("Ничего не выбрано")
root.destroy()
```

**5. Писать `tk.Combobox`** — он находится в модуле `ttk`: `from tkinter import ttk` → `ttk.Combobox`.

**6. Не задать `value` у `Radiobutton`** — у всех кнопок окажется одинаковое значение, и работать они будут неправильно.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `Checkbutton` | Независимый флажок «да/нет» |
| `Radiobutton` | Переключатель для выбора одного из группы |
| `Listbox` | Список элементов; одиночный или множественный выбор |
| `ttk.Combobox` | Выпадающий список |
| `variable`, `value` | Опции связи виджета выбора с переменной |
| `curselection()` | Выбранные индексы в Listbox |
| `<<ListboxSelect>>`, `<<ComboboxSelected>>` | Виртуальные события выбора |
| `selectmode` | Режим выбора Listbox |

## Примечание

- Управляющая переменная (`BooleanVar`, `StringVar`…) должна жить столько же, сколько виджет: если её удалит сборщик мусора (например, она была локальной переменной функции), флажок или переключатель перестанут работать.
- `Combobox` без `state="readonly"` позволяет вводить свой текст, поэтому `get()` может вернуть значение, которого нет в списке.
- Событие `<<ComboboxSelected>>` возникает только при выборе мышью/клавиатурой; программные `set()` и `current()` его не вызывают.
- Чтобы реагировать на любое изменение переменной, используйте `var.trace_add("write", callback)` (Python 3.6+); старый метод `trace()` устарел.
- `Listbox` хранит только строки: числа при `insert` превращаются в текст, при чтении их нужно преобразовывать обратно.
- Документация: https://docs.python.org/3/library/tkinter.ttk.html#combobox
