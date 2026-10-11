# Урок 24. Практические задания: pack, grid, place

## Базовый уровень

### Задание 1. Три полосы (pack)
В окне (400×300) вверху должна быть синяя полоса заголовка («Моё приложение»), внизу — серая строка состояния («Готово»), в середине — белая область, занимающая всё оставшееся пространство. При увеличении окна средняя часть должна растягиваться.

### Задание 2. Ряд кнопок (pack + Frame)
Внизу окна должны стоять выровненные вправо кнопки «Сохранить», «Отмена», «Справка» (внутри Frame, `side="right"`).

### Задание 3. Форма обратной связи (grid)
Форма в виде таблицы: метки «Имя», «Email», «Телефон», «Город» (левый столбец, выравнивание вправо) и поля Entry (правый столбец, растянуты по ширине). В самой нижней строке — кнопка «Отправить», занимающая два столбца. При нажатии все данные выводятся в консоль.

### Задание 4. Шахматная доска (grid)
Доска 8×8: `Label` белого и тёмного цвета (`width=4, height=2`). Цвет клетки: если `(row + col) % 2 == 0` — белый, иначе коричневый (`"#8b4513"`).

## Продвинутый уровень

### Задание 5. Телефонная клавиатура (grid)
Кнопки 1–9, `*`, `0`, `#` (4 строки × 3 столбца) и вверху Entry, показывающий номер (занимает 3 столбца). При нажатии кнопки соответствующий символ добавляется в Entry; кнопка «⌫» удаляет последний символ.

### Задание 6. place: «Надпись поверх картинки»
В окне 600×400 в качестве фона — большой цветной `Label` (`relwidth=1, relheight=1`), в его центре (`relx=0.5, rely=0.4`) — название игры, под ним — кнопка «Начать», а в правом нижнем углу — номер версии «v1.0». При изменении размера окна элементы должны сохранять своё (пропорциональное) положение.

### Задание 7. Смешанный интерфейс
Создайте интерфейс по эскизу:
```text
┌──────────────────────────────────────┐
│       Карточка ученика (pack)        │
├──────────────────────────────────────┤
│ Имя:     [__________]                │
│ Класс:   [__________]   (grid, Frame)│
│ Предмет: [__________]                │
├──────────────────────────────────────┤
│              [Очистить] [Сохранить]  │  (pack, Frame)
└──────────────────────────────────────┘
```

## ★ Олимпиадная / творческая задача

### Задание 8. Расписание уроков
С помощью `grid` создайте расписание уроков, где столбцы — дни недели (Пн–Пт), строки — номера уроков (1–6). Данные хранятся в словаре (`{"Пн": ["Математика", ...], ...}`). Строка заголовка и первый столбец выделены другим цветом; с помощью `columnconfigure(..., weight=1)` таблица растягивается вместе с окном. Пустые уроки выводятся как «—».

## Домашнее задание

1. Завершите задание 7; сделайте так, чтобы при увеличении окна поля растягивались.
2. Нарисуйте эскиз одного экрана любого приложения на своём телефоне (например, калькулятор, часы) и повторите его в Tkinter.
3. Загрузите код на GitHub под именем `gui/lesson24_layout.py`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import tkinter as tk

root = tk.Tk()
root.title("Три полосы")
root.geometry("400x300")
tk.Label(root, text="Моё приложение", bg="#1e3799", fg="white",
         font=("Arial", 14), pady=8).pack(side="top", fill="x")
tk.Label(root, text="Готово", bg="#d1d8e0", anchor="w", padx=5).pack(side="bottom", fill="x")
tk.Label(root, bg="white").pack(fill="both", expand=True)
root.mainloop()
```

**Задание 2**
```python
import tkinter as tk

root = tk.Tk()
root.title("Кнопки")
root.geometry("400x200")
bar = tk.Frame(root, pady=8, padx=8)
bar.pack(side="bottom", fill="x")
for text in ["Справка", "Отмена", "Сохранить"]:
    tk.Button(bar, text=text, width=10).pack(side="right", padx=3)
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk

root = tk.Tk()
root.title("Обратная связь")
root.columnconfigure(1, weight=1)
labels = ["Имя", "Email", "Телефон", "Город"]
entries = {}
for i, name in enumerate(labels):
    tk.Label(root, text=name + ":").grid(row=i, column=0, sticky="e", padx=5, pady=4)
    e = tk.Entry(root)
    e.grid(row=i, column=1, sticky="ew", padx=5, pady=4)
    entries[name] = e


def send():
    for name, e in entries.items():
        print(f"{name}: {e.get()}")


tk.Button(root, text="Отправить", command=send).grid(
    row=len(labels), column=0, columnspan=2, sticky="ew", padx=5, pady=8)
root.mainloop()
```

**Задание 4**
```python
import tkinter as tk

root = tk.Tk()
root.title("Шахматы")
for row in range(8):
    for col in range(8):
        color = "white" if (row + col) % 2 == 0 else "#8b4513"
        tk.Label(root, bg=color, width=4, height=2).grid(row=row, column=col)
root.mainloop()
```

**Задание 5**
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

**Задание 6**
```python
import tkinter as tk

root = tk.Tk()
root.title("Игра")
root.geometry("600x400")
tk.Label(root, bg="#2d3436").place(relx=0, rely=0, relwidth=1, relheight=1)
tk.Label(root, text="КОСМИЧЕСКОЕ ПУТЕШЕСТВИЕ", fg="#ffeaa7", bg="#2d3436",
         font=("Arial", 32, "bold")).place(relx=0.5, rely=0.4, anchor="center")
tk.Button(root, text="Начать", font=("Arial", 14), width=12).place(
    relx=0.5, rely=0.6, anchor="center")
tk.Label(root, text="v1.0", fg="gray", bg="#2d3436").place(
    relx=1.0, rely=1.0, anchor="se", x=-5, y=-5)
root.mainloop()
```

**Задание 7**
```python
import tkinter as tk

root = tk.Tk()
root.title("Карточка ученика")
tk.Label(root, text="Карточка ученика", font=("Arial", 16, "bold")).pack(pady=8)

form = tk.Frame(root, padx=10)
form.pack(fill="x")
form.columnconfigure(1, weight=1)
entries = []
for i, name in enumerate(["Имя:", "Класс:", "Предмет:"]):
    tk.Label(form, text=name).grid(row=i, column=0, sticky="w", pady=3)
    e = tk.Entry(form)
    e.grid(row=i, column=1, sticky="ew", pady=3, padx=(5, 0))
    entries.append(e)


def clear():
    for e in entries:
        e.delete(0, tk.END)


def save():
    print("Сохранено:", [e.get() for e in entries])


bar = tk.Frame(root, pady=8, padx=10)
bar.pack(fill="x")
tk.Button(bar, text="Сохранить", command=save).pack(side="right")
tk.Button(bar, text="Очистить", command=clear).pack(side="right", padx=5)
root.mainloop()
```

**Задание 8**
```python
import tkinter as tk

DAYS = ["Пн", "Вт", "Ср", "Чт", "Пт"]
SCHEDULE = {
    "Пн": ["Математика", "Казахский язык", "Физика", "Английский", "Информатика"],
    "Вт": ["Химия", "Математика", "История", "Физкультура"],
    "Ср": ["Биология", "Геометрия", "Литература", "Русский язык", "Музыка", "Классный час"],
    "Чт": ["Физика", "Алгебра", "География", "Английский"],
    "Пт": ["Информатика", "Казахская литература", "Математика", "Труд", "История"],
}
HEAD_BG = "#0984e3"

root = tk.Tk()
root.title("Расписание уроков")
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
