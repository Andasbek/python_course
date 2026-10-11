# Урок 30. Стили и цвета. Модуль ttk. Основы дизайна интерфейса.

## Темы

1. Почему важен дизайн?
2. 5 основных принципов дизайна интерфейса
3. Цвета
4. Шрифты
5. Модуль ttk: тематические виджеты
6. Темы (themes)
7. ttk.Style — собственные стили
8. Полезные виджеты ttk
9. Образец: стильное окно «Профиль»
10. Частые ошибки
11. Основные понятия

## 1. Почему важен дизайн?

Пользователь оценивает программу по её **интерфейсу**. Даже самый сильный алгоритм теряет ценность в непонятном, «лоскутном» окне. Хороший интерфейс:
- **понятный** — им можно пользоваться без инструкции;
- **удобный** — цель достигается за минимум действий;
- **красивый** — приятен глазу, выглядит профессионально;
- **доступный** — им могут пользоваться и люди со слабым зрением.

## 2. 5 основных принципов дизайна интерфейса

| Принцип | Суть | Реализация в Tkinter |
|---|---|---|
| **Контраст** | Текст должен чётко отличаться от фона; важный элемент должен выделяться | Светлый фон + тёмный текст; главная кнопка — яркого цвета |
| **Выравнивание** | Элементы стоят по одной линии | `grid` + `sticky`, `anchor="w"` |
| **Единообразие** | Одинаковые элементы выглядят одинаково | Один стиль, один отступ (8/16 px), один шрифт |
| **Близость** | Связанные элементы рядом, остальные — дальше | Группировка `Frame`/`LabelFrame`, разница в `pady` |
| **Свободное пространство** | Элементам нужно место, чтобы «дышать» | `padx`, `pady`, `ipadx` |

### Плохой и хороший пример

```text
ПЛОХО                                  ХОРОШО
┌──────────────────────────┐           ┌──────────────────────────┐
│Имя:[_______]             │           │  Регистрация             │
│   Фамилия:[__________]   │           │                          │
│Email:[___]  [ОТПРАВИТЬ!!!]           │  Имя     [_____________] │
│ [Очистить]               │           │  Фамилия [_____________] │
│ (зелёный текст на красном)│          │  Email   [_____________] │
└──────────────────────────┘           │                          │
                                       │   [Очистить] [Отправить] │
                                       └──────────────────────────┘
```

## 3. Цвета

### Правило 60–30–10

- **60%** — основной цвет (фон): нейтральный, светлый или тёмный;
- **30%** — дополнительный цвет: панели, заголовки;
- **10%** — акцентный цвет: главная кнопка, важные отметки.

### Образцы палитр

| Роль | Светлая тема | Тёмная тема |
|---|---|---|
| Фон | `#F5F6FA` | `#1E1E2E` |
| Панель | `#FFFFFF` | `#2A2A3C` |
| Текст | `#2D3436` | `#F5F5F5` |
| Приглушённый текст | `#636E72` | `#A6ADC8` |
| Акцент | `#0984E3` | `#89B4FA` |
| Успех | `#00B894` | `#A6E3A1` |
| Ошибка | `#D63031` | `#F38BA8` |

Советы:
- не используйте больше 3–4 цветов;
- не злоупотребляйте чистыми красным/зелёным/синим (`#FF0000`) — они утомляют глаза;
- храните цвета в одном месте как **константы** (словарь или класс) — тогда тему легко сменить.

### Доступность

- Контраст текста и фона должен быть высоким (светло-жёлтый текст на белом фоне — плохо).
- Не передавайте информацию **только цветом**: пишите и слово «Ошибка» (есть люди, не различающие цвета).
- Размер шрифта — не меньше 10 pt.

## 4. Шрифты

- В одном приложении достаточно 1–2 семейств шрифтов.
- Иерархия: заголовок (18–24 pt, жирный), подзаголовок (13–15 pt), основной текст (10–12 pt).
- Выбирайте шрифт с поддержкой кириллицы и казахских букв (ә, ғ, қ, ң, ө, ұ, ү, һ, і): `Arial`, `Segoe UI`, `Calibri`, `Verdana`, `DejaVu Sans`.
- Для кода/чисел — моноширинный шрифт: `Consolas`, `Courier New`.

```python
import tkinter as tk
from tkinter import font

root = tk.Tk()
families = sorted(font.families())
print("Количество шрифтов:", len(families))
print("Есть ли Arial?", "Arial" in families)
root.destroy()
```

## 5. Модуль ttk: тематические виджеты

`tkinter.ttk` (Themed Tk) — набор современно выглядящих виджетов. Они похожи на стиль операционной системы и оформляются централизованно с помощью **стилей**.

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("tk и ttk")
tk.Label(root, text="tk.Button:").grid(row=0, column=0, padx=10, pady=5)
tk.Button(root, text="Старый стиль").grid(row=0, column=1, padx=10)
tk.Label(root, text="ttk.Button:").grid(row=1, column=0, padx=10, pady=5)
ttk.Button(root, text="Новый стиль").grid(row=1, column=1, padx=10)
root.mainloop()
```

### Различие tk и ttk

| | Виджеты `tk` | Виджеты `ttk` |
|---|---|---|
| Внешний вид | Устаревший, одинаковый во всех ОС | Современный, адаптирован к ОС |
| Настройка цвета | `bg`, `fg`, `font` для каждого виджета | Через `Style`, сразу для всех виджетов |
| Темы | Нет | `clam`, `alt`, `default`, `classic`, в Windows `vista`, в macOS `aqua` |
| Особые виджеты | `Text`, `Canvas`, `Listbox`, `Menu` (в ttk нет) | `Combobox`, `Notebook`, `Progressbar`, `Treeview`, `Separator` |

Виджеты, которые есть в `ttk`: `Button`, `Label`, `Entry`, `Checkbutton`, `Radiobutton`, `Scale`, `Scrollbar`, `Frame`, `LabelFrame`, `Combobox`, `Notebook`, `Progressbar`, `Separator`, `Treeview`, `Spinbox`.

**Важно:** у виджетов ttk нет опций `bg`, `fg` (или они ограничены) — цвета задаём через `Style`.

## 6. Темы (themes)

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
style = ttk.Style()
print("Доступные темы:", style.theme_names())
print("Текущая тема:", style.theme_use())
style.theme_use("clam")          # сменить тему
root.destroy()
```

Смена темы «на лету»:

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Темы")
style = ttk.Style()
theme = tk.StringVar(value=style.theme_use())

ttk.Label(root, text="Выберите тему:").pack(padx=10, pady=5)
box = ttk.Combobox(root, textvariable=theme, values=style.theme_names(), state="readonly")
box.pack(padx=10)
box.bind("<<ComboboxSelected>>", lambda e: style.theme_use(theme.get()))
ttk.Button(root, text="Кнопка").pack(pady=5)
ttk.Checkbutton(root, text="Флажок").pack()
ttk.Entry(root).pack(pady=5)
ttk.Progressbar(root, value=60, length=200).pack(pady=5)
root.mainloop()
```

## 7. ttk.Style — собственные стили

```text
style.configure("ИмяСтиля", опция=значение, ...)
```

Имя стиля заканчивается на `.TButton`, `.TLabel` и т.д. Стили по умолчанию: `TButton`, `TLabel`, `TEntry`, `TFrame`, `TCheckbutton`...

```python
import tkinter as tk
from tkinter import ttk

ACCENT = "#0984e3"
BG = "#f5f6fa"

root = tk.Tk()
root.title("Стили")
root.configure(bg=BG)
style = ttk.Style()
style.theme_use("clam")

# общее для всех виджетов ttk
style.configure(".", font=("Segoe UI", 11), background=BG)
# все TLabel
style.configure("TLabel", foreground="#2d3436")
# наш стиль: надпись-заголовок
style.configure("Title.TLabel", font=("Segoe UI", 18, "bold"))
# наш стиль: акцентная кнопка
style.configure("Accent.TButton", background=ACCENT, foreground="white",
                padding=(16, 6), borderwidth=0)
# цвета, меняющиеся в зависимости от состояния
style.map("Accent.TButton",
          background=[("pressed", "#0652a3"), ("active", "#3498db"), ("disabled", "#b2bec3")])

ttk.Label(root, text="Регистрация", style="Title.TLabel").pack(padx=20, pady=(15, 5))
ttk.Label(root, text="Введите свои данные").pack()
ttk.Entry(root, width=30).pack(padx=20, pady=10)
ttk.Button(root, text="Отправить", style="Accent.TButton").pack(pady=(0, 15))
root.mainloop()
```

| Метод | Смысл |
|---|---|
| `Style()` | Менеджер стилей |
| `theme_names()` / `theme_use(name)` | Список тем / установка темы |
| `configure(style, **опции)` | Установка опций стиля |
| `map(style, опция=[(состояние, значение), ...])` | Значения, зависящие от состояния (`active`, `pressed`, `disabled`, `focus`) |
| `widget.configure(style="...")` | Назначение стиля виджету |

`"."` — корень всех стилей: заданные ему опции действуют на все виджеты ttk.

## 8. Полезные виджеты ttk

### Notebook — вкладки

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Настройки")
nb = ttk.Notebook(root)
nb.pack(fill="both", expand=True, padx=10, pady=10)

general = ttk.Frame(nb, padding=10)
sound = ttk.Frame(nb, padding=10)
nb.add(general, text="Общие")
nb.add(sound, text="Звук")

ttk.Label(general, text="Язык:").grid(row=0, column=0, sticky="w")
ttk.Combobox(general, values=["Қазақша", "Русский", "English"],
             state="readonly").grid(row=0, column=1, padx=5)
ttk.Checkbutton(sound, text="Включить звук").pack(anchor="w")
ttk.Scale(sound, from_=0, to=100, length=200).pack(pady=10)
root.mainloop()
```

### Progressbar — индикатор выполнения

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Загрузка")
bar = ttk.Progressbar(root, length=300, maximum=100, mode="determinate")
bar.pack(padx=20, pady=15)
label = ttk.Label(root, text="0%")
label.pack()


def load(step=0):
    if step <= 100:
        bar["value"] = step
        label.config(text=f"{step}%")
        root.after(30, load, step + 1)    # передача аргумента в after
    else:
        label.config(text="Готово!")


ttk.Button(root, text="Начать", command=load).pack(pady=10)
root.mainloop()
```

`mode="indeterminate"` + `bar.start()` — «бегущая» полоса для процесса неизвестной длительности.

### LabelFrame и Separator

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Группировка")
group = ttk.LabelFrame(root, text="Уведомления", padding=10)
group.pack(fill="x", padx=10, pady=10)
for name in ["Email", "SMS", "Push"]:
    ttk.Checkbutton(group, text=name).pack(anchor="w")
ttk.Separator(root, orient="horizontal").pack(fill="x", padx=10, pady=5)
ttk.Button(root, text="Сохранить").pack(pady=10)
root.mainloop()
```

## 9. Образец: стильное окно «Профиль»

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
        ttk.Label(card, text="Личный профиль", style="Title.TLabel").grid(
            row=0, column=0, columnspan=2, sticky="w")
        ttk.Label(card, text="Данные используются только внутри кружка",
                  style="Muted.TLabel").grid(row=1, column=0, columnspan=2, sticky="w",
                                             pady=(0, 16))
        for i, name in enumerate(["Имя", "Фамилия", "Класс"], start=2):
            ttk.Label(card, text=name, style="Card.TLabel").grid(row=i, column=0, sticky="w",
                                                                 pady=6, padx=(0, 16))
            ttk.Entry(card, width=28).grid(row=i, column=1, sticky="ew", pady=6)
        buttons = ttk.Frame(card, style="Card.TFrame")
        buttons.grid(row=5, column=0, columnspan=2, sticky="e", pady=(16, 0))
        ttk.Button(buttons, text="Отмена").pack(side="left", padx=(0, 8))
        ttk.Button(buttons, text="Сохранить", style="Accent.TButton").pack(side="left")


if __name__ == "__main__":
    ProfileApp().mainloop()
```

Здесь применены все принципы: одна палитра, один шрифт, одинаковые отступы (8/16/24), выравнивание, акцент только на главной кнопке, группировка («карточка»).

## Частые ошибки

**1. Передача `bg`/`fg` виджету ttk**

```python
import tkinter as tk
from tkinter import ttk
root = tk.Tk()
# ttk.Label(root, text="Привет", bg="red")   -> TclError: unknown option "-bg"
style = ttk.Style()
style.configure("Red.TLabel", background="red")
ttk.Label(root, text="Привет", style="Red.TLabel").pack()
root.destroy()
```

**2. Забыли окончание `.TButton` в имени стиля** — `style.configure("Accent", ...)` не действует на виджет. Правильно: `"Accent.TButton"`.

**3. Цвет кнопки не меняется** — некоторые темы (`vista`, `aqua`) не позволяют настраивать фон кнопки. Используйте `style.theme_use("clam")`.

**4. Слишком много цветов и шрифтов** — «радужный» интерфейс. Придерживайтесь правила 60–30–10 и 1–2 шрифтов.

**5. Разнобой в отступах** — в одном месте `pady=3`, в другом `pady=17`. Выберите одну шкалу: 4, 8, 16, 24.

**6. Цвета, «разбросанные» по всему коду** — если `"#0984e3"` написан в десяти местах, менять его трудно. Используйте константы/словарь палитры.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| UI / UX | Пользовательский интерфейс / пользовательский опыт (удобство использования) |
| Контраст, выравнивание, единообразие, близость, свободное пространство | 5 основных принципов дизайна |
| Палитра | Набор цветов, используемых в приложении |
| Правило 60–30–10 | Доли основного, дополнительного и акцентного цветов |
| `ttk` | Тематические (современные) виджеты Tkinter |
| Тема (theme) | Общий внешний вид виджетов ttk |
| `ttk.Style` | Управление стилями: `configure`, `map`, `theme_use` |
| `Notebook`, `Progressbar`, `Separator`, `LabelFrame` | Полезные виджеты ttk |

## Примечание

- Набор тем зависит от ОС: `vista`, `winnative`, `xpnative` есть только в Windows, `aqua` — только в macOS; `clam`, `alt`, `default`, `classic` доступны везде.
- Объект `ttk.Style` общий для всего приложения: изменение стиля сразу применяется ко всем виджетам, которые его используют.
- Чтобы узнать, какие опции поддерживает стиль, используйте `style.layout("TButton")`, `style.element_options("Button.label")` и `style.lookup("TButton", "background")`.
- Готовые современные темы устанавливаются через pip: `sv-ttk` (Sun Valley), `ttkbootstrap`; библиотека CustomTkinter предлагает собственные виджеты.
- Для читаемости рекомендации WCAG требуют контраст текста и фона не ниже 4.5:1 для обычного текста.
- Документация: https://docs.python.org/3/library/tkinter.ttk.html
