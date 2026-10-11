# Урок 30. Практические задания: стили, ttk и дизайн

## Базовый уровень

### Задание 1. «Дизайн-критик»
Запустите «плохой» интерфейс ниже и запишите в тетрадь, какие принципы дизайна нарушены (не менее 5 недостатков):
```python
import tkinter as tk

root = tk.Tk()
root.title("ПЛОХАЯ ФОРМА")
root.config(bg="red")
tk.Label(root, text="имя", bg="yellow", fg="white", font=("Comic Sans MS", 8)).pack(anchor="w")
tk.Entry(root, width=10).pack(anchor="e")
tk.Label(root, text="ФАМИЛИЯ!!!", bg="green", fg="lime", font=("Impact", 22)).pack()
tk.Entry(root, width=40).pack(anchor="w", pady=30)
tk.Button(root, text="отправить", bg="blue", fg="red").pack(side="left")
tk.Button(root, text="ОЧИСТИТЬ", bg="purple", fg="yellow", font=("Arial", 18)).pack(side="right")
root.mainloop()
```

### Задание 2. Перепроектирование
Переделайте форму из задания 1 с помощью виджетов `ttk`, `grid` и одной палитры: заголовок, выровненные поля, справа кнопки «Очистить» (обычная) и «Отправить» (акцентная).

### Задание 3. Выбор темы
Создайте окно, которое переключает темы `ttk` через Combobox. В окне должно быть не менее 6 разных виджетов ttk (Button, Entry, Checkbutton, Radiobutton, Combobox, Progressbar, Scale).

### Задание 4. Экран загрузки
Окно «Загрузка...», в котором `ttk.Progressbar` заполняется от 0 до 100. После заполнения надпись меняется на «Готово!», а кнопка «Начать» становится активной (`state=["!disabled"]`).

## Продвинутый уровень

### Задание 5. Окно настроек (Notebook)
`ttk.Notebook` с тремя вкладками:
- «Общие» — имя (Entry), язык (Combobox);
- «Вид» — тема (Radiobutton: светлая/тёмная), размер шрифта (`ttk.Spinbox` 10–20);
- «Уведомления» — 3 Checkbutton внутри `LabelFrame`.
Внизу — `Separator` и (акцентная) кнопка «Сохранить»; при нажатии все настройки выводятся в консоль в виде словаря.

### Задание 6. Светлая и тёмная тема
Создайте словарь `PALETTES = {"light": {...}, "dark": {...}}` и напишите функцию, применяющую обе темы через `ttk.Style`. При нажатии кнопки весь интерфейс (фон, текст, Entry, кнопки) должен менять тему.

## ★ Олимпиадная / творческая задача

### Задание 7. Своя «дизайн-система»
Напишите модуль `theme.py`: константы палитры, шрифтов, отступов и функцию `apply_theme(root, mode)` (регистрирует стили `Title.TLabel`, `Muted.TLabel`, `Accent.TButton`, `Danger.TButton`, `Card.TFrame`). Импортируйте этот модуль и переоформите одно из своих приложений с уроков 26–28 (например, форму регистрации или Блокнот). Сравните скриншоты «до / после».

## Домашнее задание

1. Нарисуйте эскиз интерфейса своего приложения «Калькулятор» (с цветовой палитрой) — на следующем уроке практикум.
2. Подберите на сайте coolors.co или colorhunt.co палитру из 4 цветов и внесите её в `theme.py` из задания 7.
3. Загрузите на GitHub в виде папки `gui/lesson30_style/`.

## Критерии оценивания

Чек-лист взаимооценивания (по заданию 2 или заданию 5, 10 баллов):

| Критерий | Баллы |
|---|---|
| Элементы выровнены (по одной линии) | 2 |
| Единообразные отступы и свободное пространство | 2 |
| Палитра не более 3–4 цветов, контраст достаточный | 2 |
| Иерархия шрифтов (заголовок / текст) | 2 |
| Главное действие (акцентная кнопка) явно выделено | 2 |

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.** Недостатки: нет контраста (белый текст на жёлтом фоне, светло-зелёный на зелёном); нет выравнивания (поля в разных местах); нет единообразия (разные шрифты, размеры, регистр); нарушена близость (метка и поле далеко, `pady=30`); слишком много цветов (5+ ярких цветов); непонятна важность кнопок; мелкий шрифт (8 pt); «!!!» и заглавные буквы «кричат».

**Задание 2**
```python
import tkinter as tk
from tkinter import ttk

BG, ACCENT = "#f5f6fa", "#0984e3"

root = tk.Tk()
root.title("Регистрация")
root.configure(bg=BG)
s = ttk.Style()
s.theme_use("clam")
s.configure(".", background=BG, font=("Segoe UI", 11))
s.configure("Title.TLabel", font=("Segoe UI", 16, "bold"))
s.configure("Accent.TButton", background=ACCENT, foreground="white")
s.map("Accent.TButton", background=[("active", "#3498db")])

frame = ttk.Frame(root, padding=20)
frame.pack()
frame.columnconfigure(1, weight=1)
ttk.Label(frame, text="Регистрация", style="Title.TLabel").grid(row=0, column=0, columnspan=2,
                                                                sticky="w", pady=(0, 12))
entries = []
for i, name in enumerate(["Имя", "Фамилия"], start=1):
    ttk.Label(frame, text=name).grid(row=i, column=0, sticky="w", pady=6, padx=(0, 12))
    e = ttk.Entry(frame, width=28)
    e.grid(row=i, column=1, sticky="ew", pady=6)
    entries.append(e)
btns = ttk.Frame(frame)
btns.grid(row=3, column=0, columnspan=2, sticky="e", pady=(12, 0))
ttk.Button(btns, text="Очистить",
           command=lambda: [e.delete(0, tk.END) for e in entries]).pack(side="left", padx=(0, 8))
ttk.Button(btns, text="Отправить", style="Accent.TButton").pack(side="left")
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Темы")
style = ttk.Style()
theme = tk.StringVar(value=style.theme_use())
frame = ttk.Frame(root, padding=15)
frame.pack(fill="both", expand=True)
box = ttk.Combobox(frame, textvariable=theme, values=style.theme_names(), state="readonly")
box.pack(fill="x")
box.bind("<<ComboboxSelected>>", lambda e: style.theme_use(theme.get()))
ttk.Separator(frame).pack(fill="x", pady=10)
ttk.Button(frame, text="Кнопка").pack(anchor="w")
ttk.Entry(frame).pack(anchor="w", pady=5)
ttk.Checkbutton(frame, text="Флажок").pack(anchor="w")
choice = tk.StringVar(value="a")
ttk.Radiobutton(frame, text="Вариант А", variable=choice, value="a").pack(anchor="w")
ttk.Radiobutton(frame, text="Вариант Б", variable=choice, value="b").pack(anchor="w")
ttk.Scale(frame, from_=0, to=100).pack(fill="x", pady=5)
ttk.Progressbar(frame, value=40).pack(fill="x")
root.mainloop()
```

**Задание 4**
```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Загрузка")
frame = ttk.Frame(root, padding=20)
frame.pack()
label = ttk.Label(frame, text="Загрузка...")
label.pack()
bar = ttk.Progressbar(frame, length=280, maximum=100)
bar.pack(pady=10)
start_btn = ttk.Button(frame, text="Начать", state="disabled")
start_btn.pack()


def step(value=0):
    bar["value"] = value
    if value < 100:
        root.after(40, step, value + 2)
    else:
        label.config(text="Готово!")
        start_btn.state(["!disabled"])


step()
root.mainloop()
```

**Задание 5**
```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Настройки")
ttk.Style().theme_use("clam")
nb = ttk.Notebook(root)
nb.pack(fill="both", expand=True, padx=10, pady=10)

general = ttk.Frame(nb, padding=12)
view = ttk.Frame(nb, padding=12)
notify = ttk.Frame(nb, padding=12)
nb.add(general, text="Общие")
nb.add(view, text="Вид")
nb.add(notify, text="Уведомления")

name = tk.StringVar()
lang = tk.StringVar(value="Русский")
ttk.Label(general, text="Имя:").grid(row=0, column=0, sticky="w", pady=4)
ttk.Entry(general, textvariable=name).grid(row=0, column=1, pady=4, padx=6)
ttk.Label(general, text="Язык:").grid(row=1, column=0, sticky="w", pady=4)
ttk.Combobox(general, textvariable=lang, values=["Қазақша", "Русский", "English"],
             state="readonly").grid(row=1, column=1, pady=4, padx=6)

theme = tk.StringVar(value="light")
size = tk.IntVar(value=12)
ttk.Radiobutton(view, text="Светлая", variable=theme, value="light").pack(anchor="w")
ttk.Radiobutton(view, text="Тёмная", variable=theme, value="dark").pack(anchor="w")
ttk.Label(view, text="Размер шрифта:").pack(anchor="w", pady=(8, 0))
ttk.Spinbox(view, from_=10, to=20, textvariable=size, width=5).pack(anchor="w")

group = ttk.LabelFrame(notify, text="Каналы", padding=8)
group.pack(fill="x")
channels = {}
for ch in ["Email", "SMS", "Push"]:
    v = tk.BooleanVar()
    ttk.Checkbutton(group, text=ch, variable=v).pack(anchor="w")
    channels[ch] = v


def save():
    settings = {
        "name": name.get(), "lang": lang.get(), "theme": theme.get(),
        "font_size": size.get(),
        "channels": [ch for ch, v in channels.items() if v.get()],
    }
    print(settings)


ttk.Separator(root).pack(fill="x", padx=10)
ttk.Button(root, text="Сохранить", command=save).pack(anchor="e", padx=10, pady=10)
root.mainloop()
```

**Задание 6**
```python
import tkinter as tk
from tkinter import ttk

PALETTES = {
    "light": {"bg": "#f5f6fa", "fg": "#2d3436", "field": "#ffffff", "accent": "#0984e3"},
    "dark": {"bg": "#1e1e2e", "fg": "#f5f5f5", "field": "#2a2a3c", "accent": "#89b4fa"},
}
state = {"mode": "light"}


def apply(mode):
    p = PALETTES[mode]
    root.configure(bg=p["bg"])
    style.configure(".", background=p["bg"], foreground=p["fg"])
    style.configure("TEntry", fieldbackground=p["field"], foreground=p["fg"])
    style.configure("Accent.TButton", background=p["accent"], foreground=p["bg"])


def toggle():
    state["mode"] = "dark" if state["mode"] == "light" else "light"
    apply(state["mode"])


root = tk.Tk()
root.title("Тема")
style = ttk.Style()
style.theme_use("clam")
frame = ttk.Frame(root, padding=20)
frame.pack()
ttk.Label(frame, text="Свет и тьма", font=("Segoe UI", 16, "bold")).pack(pady=5)
ttk.Entry(frame, width=30).pack(pady=5)
ttk.Button(frame, text="Сменить тему", style="Accent.TButton",
           command=toggle).pack(pady=5)
apply("light")
root.mainloop()
```

**Задание 7** (модуль `theme.py`)
```python
"""Общая дизайн-система для приложений кружка."""
from tkinter import ttk

PALETTES = {
    "light": {"bg": "#f5f6fa", "panel": "#ffffff", "text": "#2d3436",
              "muted": "#636e72", "accent": "#0984e3", "danger": "#d63031"},
    "dark": {"bg": "#1e1e2e", "panel": "#2a2a3c", "text": "#f5f5f5",
             "muted": "#a6adc8", "accent": "#89b4fa", "danger": "#f38ba8"},
}
FONT = "Segoe UI"
SPACE = {"s": 4, "m": 8, "l": 16, "xl": 24}


def apply_theme(root, mode="light"):
    p = PALETTES[mode]
    root.configure(bg=p["bg"])
    s = ttk.Style(root)
    s.theme_use("clam")
    s.configure(".", font=(FONT, 11), background=p["bg"], foreground=p["text"])
    s.configure("Card.TFrame", background=p["panel"])
    s.configure("Title.TLabel", font=(FONT, 18, "bold"))
    s.configure("Muted.TLabel", foreground=p["muted"], font=(FONT, 9))
    s.configure("Accent.TButton", background=p["accent"], foreground="white",
                padding=(SPACE["l"], SPACE["s"]))
    s.map("Accent.TButton", background=[("active", p["text"])])
    s.configure("Danger.TButton", background=p["danger"], foreground="white",
                padding=(SPACE["l"], SPACE["s"]))
    return p


if __name__ == "__main__":
    import tkinter as tk
    root = tk.Tk()
    apply_theme(root, "dark")
    ttk.Label(root, text="Дизайн-система", style="Title.TLabel").pack(padx=24, pady=8)
    ttk.Button(root, text="Основная", style="Accent.TButton").pack(pady=4)
    ttk.Button(root, text="Удалить", style="Danger.TButton").pack(pady=(4, 24))
    root.mainloop()
```

</details>
