# Урок 24. Размещение элементов: менеджеры pack, grid, place.

## Темы

1. Что такое менеджер размещения?
2. Менеджер pack
3. Frame — группировка виджетов
4. Менеджер grid
5. Менеджер place
6. Сравнение менеджеров и главное правило
7. Советы по планированию интерфейса
8. Частые ошибки
9. Основные понятия

## 1. Что такое менеджер размещения?

Создать виджет недостаточно — его нужно **поместить** в окно. Это делает **менеджер размещения (менеджер геометрии, geometry manager)**. В Tkinter их три:

| Менеджер | Принцип | Аналогия | Когда применяем |
|---|---|---|---|
| `pack` | По очереди «укладывает» виджеты к сторонам | Укладка вещей в чемодан | Простое размещение в одном направлении |
| `grid` | Таблица: строки и столбцы | Таблица Excel, шахматная доска | Формы, калькулятор, таблицы |
| `place` | Точные координаты | Прибить картину гвоздём к стене | Особые случаи, наложение сверху |

## 2. Менеджер pack

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

`pack` выделяет из свободного пространства «полосу» в указанную сторону и помещает туда виджет. Следующий виджет размещается в **оставшемся** пространстве — поэтому **порядок важен**.

### Опции pack

| Опция | Значения | Смысл |
|---|---|---|
| `side` | `"top"` (по умолчанию), `"bottom"`, `"left"`, `"right"` | К какой стороне прижимается |
| `fill` | `"x"`, `"y"`, `"both"`, `"none"` | Заполнение выделенного пространства по ширине/высоте |
| `expand` | `True` / `False` | Занимать дополнительное место при увеличении окна |
| `padx`, `pady` | число или `(слева, справа)` | Внешний отступ |
| `ipadx`, `ipady` | число | Внутренний отступ («раздувает» виджет) |
| `anchor` | `"n"`, `"s"`, `"e"`, `"w"`, `"center"`, `"ne"`... | Положение внутри выделенного пространства |

```python
import tkinter as tk

root = tk.Tk()
root.title("pack: fill и expand")
root.geometry("300x250")

tk.Label(root, text="Заголовок (fill=x)", bg="#4a69bd", fg="white").pack(fill="x")
tk.Label(root, text="Контент (fill=both, expand=True)", bg="#dfe6e9").pack(
    fill="both", expand=True, padx=10, pady=10)
tk.Label(root, text="Нижняя строка (fill=x)", bg="#4a69bd", fg="white").pack(
    side="bottom", fill="x")

root.mainloop()
```

Попробуйте изменить размер окна мышью: средняя часть увеличивается и уменьшается, а верхняя/нижняя полосы растягиваются только по ширине.

## 3. Frame — группировка виджетов

**Frame (рамка)** — «коробка», в которую помещают другие виджеты. Сложный интерфейс всегда делится на несколько Frame.

```python
import tkinter as tk

root = tk.Tk()
root.title("Frame")

top = tk.Frame(root, bg="#dff9fb", padx=10, pady=10)
top.pack(fill="x")
tk.Label(top, text="Поиск:", bg="#dff9fb").pack(side="left")
tk.Entry(top).pack(side="left", fill="x", expand=True, padx=5)
tk.Button(top, text="Найти").pack(side="left")

bottom = tk.Frame(root, padx=10, pady=10)
bottom.pack(fill="x")
tk.Button(bottom, text="Отмена").pack(side="right")
tk.Button(bottom, text="ОК").pack(side="right", padx=5)

root.mainloop()
```

Внутри каждого Frame своё размещение. Представьте это как «матрёшку».

## 4. Менеджер grid

`grid` делит окно на **строки (row)** и **столбцы (column)**. Нумерация начинается с 0.

```text
          column 0        column 1
row 0   [ Имя:      ]  [ Entry          ]
row 1   [ Фамилия:  ]  [ Entry          ]
row 2   [ Возраст:  ]  [ Entry          ]
row 3   [  Зарегистрироваться (columnspan=2) ]
```

```python
import tkinter as tk


def register():
    data = [e.get() for e in entries]
    result.config(text=" | ".join(data))


root = tk.Tk()
root.title("Форма регистрации")

fields = ["Имя:", "Фамилия:", "Возраст:"]
entries = []
for i, text in enumerate(fields):
    tk.Label(root, text=text).grid(row=i, column=0, sticky="e", padx=5, pady=5)
    e = tk.Entry(root, width=25)
    e.grid(row=i, column=1, padx=5, pady=5)
    entries.append(e)

tk.Button(root, text="Зарегистрироваться", command=register).grid(
    row=3, column=0, columnspan=2, sticky="ew", padx=5, pady=10)
result = tk.Label(root, text="", fg="blue")
result.grid(row=4, column=0, columnspan=2)

root.mainloop()
```

### Опции grid

| Опция | Смысл |
|---|---|
| `row`, `column` | Номер строки и столбца |
| `rowspan`, `columnspan` | Занять несколько строк/столбцов (объединение ячеек) |
| `sticky` | К какой стороне ячейки прижаться: `"n"`, `"s"`, `"e"`, `"w"` и их сочетания: `"ew"` — заполнить по ширине, `"nsew"` — заполнить полностью |
| `padx`, `pady`, `ipadx`, `ipady` | Отступы (как в pack) |

### Как сделать столбцы «растягиваемыми»: weight

```python
import tkinter as tk

root = tk.Tk()
root.title("weight")
root.geometry("400x200")
root.columnconfigure(0, weight=1)   # столбец 0 получает 1 долю пространства
root.columnconfigure(1, weight=3)   # столбец 1 — 3 доли
root.rowconfigure(0, weight=1)

tk.Label(root, text="Меню", bg="#ffeaa7").grid(row=0, column=0, sticky="nsew")
tk.Label(root, text="Основная часть", bg="#81ecec").grid(row=0, column=1, sticky="nsew")

root.mainloop()
```

Если увеличить окно, столбец 1 будет расширяться в 3 раза быстрее столбца 0.

### Пример: клавиатура калькулятора

```python
import tkinter as tk

root = tk.Tk()
root.title("Клавиатура")
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

`divmod(i, 4)` даёт сразу целое частное и остаток: `divmod(6, 4)` → `(1, 2)` — строка 1, столбец 2.

## 5. Менеджер place

`place` ставит виджет в **точные координаты**.

```python
import tkinter as tk

root = tk.Tk()
root.title("place")
root.geometry("300x200")

tk.Label(root, text="(10, 10)", bg="#fab1a0").place(x=10, y=10)
tk.Label(root, text="По центру", bg="#74b9ff").place(relx=0.5, rely=0.5, anchor="center")
tk.Button(root, text="Правый нижний угол").place(relx=1.0, rely=1.0, anchor="se",
                                                 x=-5, y=-5)
tk.Label(root, text="Половина ширины", bg="#55efc4").place(relx=0, rely=0.25, relwidth=0.5)

root.mainloop()
```

| Опция | Смысл |
|---|---|
| `x`, `y` | Абсолютные координаты (пиксели) |
| `relx`, `rely` | Относительное положение: 0.0 — слева/сверху, 1.0 — справа/снизу |
| `anchor` | Какая точка виджета совпадает с координатой |
| `width`, `height` | Абсолютный размер |
| `relwidth`, `relheight` | Размер относительно родительского виджета (0.5 — половина) |

Недостаток `place`: при изменении размера окна виджеты с абсолютными координатами «не сдвигаются», а при удлинении текста могут перекрывать друг друга. Поэтому основной интерфейс строим через `pack`/`grid`, а `place` используем в особых случаях (например, надпись поверх изображения).

## 6. Сравнение менеджеров и главное правило

| | `pack` | `grid` | `place` |
|---|---|---|---|
| Простота | Очень просто | Средне | Просто, но много ручных расчётов |
| При изменении размера | Хорошо адаптируется | Хорошо (`weight`) | Плохо (в абсолютных) |
| Для форм | Плохо | Отлично | Плохо |
| Для рядов/полос | Отлично | Хорошо | Средне |

**Главное правило:** внутри одного родительского виджета (окна или одного Frame) **нельзя смешивать** `pack` и `grid`! В Python 3.10+ это вызывает `TclError`. Если нужны разные менеджеры — используйте разные Frame:

```python
import tkinter as tk

root = tk.Tk()
root.title("Смешанный интерфейс")

header = tk.Label(root, text="Личные данные", font=("Arial", 14, "bold"))
header.pack(pady=5)                      # внутри root — только pack

form = tk.Frame(root)                    # form помещается в root через pack
form.pack(padx=10, pady=5)
tk.Label(form, text="Email:").grid(row=0, column=0, sticky="e")   # внутри form — только grid
tk.Entry(form).grid(row=0, column=1)
tk.Label(form, text="Телефон:").grid(row=1, column=0, sticky="e")
tk.Entry(form).grid(row=1, column=1)

tk.Button(root, text="Сохранить").pack(pady=10)
root.mainloop()
```

## 7. Советы по планированию интерфейса

1. Сначала нарисуйте **эскиз** на бумаге: какие части (заголовок, форма, панель кнопок).
2. Каждая часть — отдельный `Frame`.
3. Для форм и таблиц — `grid`, для полос и рядов — `pack`.
4. Держите отступы (`padx`, `pady`) одинаковыми (например, везде 5 или 10) — интерфейс будет выглядеть аккуратно.
5. Проверьте, увеличивая и уменьшая окно.

## Частые ошибки

**1. Смешивание `pack` и `grid` в одном контейнере**

```python
import tkinter as tk
root = tk.Tk()
tk.Label(root, text="A").pack()
# tk.Label(root, text="B").grid(row=0, column=0)
# -> TclError: cannot use geometry manager grid inside . which already has slaves managed by pack
root.destroy()
```

**2. Не написали `sticky` и удивляетесь, что Entry или кнопка не растягиваются** — в `grid` виджет стоит в центре ячейки; чтобы растянуть, нужен `sticky="ew"`.

**3. Не задан `weight`** — при увеличении окна сетка остаётся на месте. Добавьте `columnconfigure(i, weight=1)`.

**4. Есть `expand=True`, но нет `fill`** — виджету выделяется место, но сам виджет не растягивается.

**5. Неверно указан родительский виджет** — создать `tk.Entry(root)` и пытаться поместить его в grid внутри Frame. В каком Frame стоит виджет, тот Frame и должен быть первым аргументом.

**6. Поочерёдное применение двух менеджеров к одному виджету** — последний «заменяет» предыдущий; это приводит к путанице.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Менеджер размещения | Механизм, определяющий положение и размер виджета в окне |
| `pack` | Поочерёдная укладка виджетов к сторонам |
| `grid` | Таблица из строк и столбцов |
| `place` | Абсолютные или относительные координаты |
| `Frame` | Контейнер для группировки виджетов |
| `sticky` | Сторона ячейки grid, к которой прижимается виджет |
| `weight` | Доля распределения свободного пространства между строками/столбцами |
| `fill`, `expand` | Растягивание и занятие дополнительного места в pack |

## Примечание

- Методы `pack()`, `grid()` и `place()` возвращают `None`, поэтому создание и размещение виджета, который понадобится позже, пишут в две строки.
- По умолчанию `Frame` подстраивается под размер содержимого и игнорирует свои `width`/`height`; чтобы сохранить заданный размер, вызывают `frame.pack_propagate(False)` или `frame.grid_propagate(False)`.
- Пустая строка или столбец `grid` без виджетов имеет нулевой размер; минимальную ширину/высоту задаёт `columnconfigure(i, minsize=...)` / `rowconfigure(i, minsize=...)`.
- Скрыть виджет можно через `pack_forget()`, `grid_forget()` или `grid_remove()`; последний запоминает опции, и повторный `grid()` вернёт виджет на прежнее место.
- Отступы можно задавать разными с двух сторон: `padx=(10, 0)` — 10 пикселей слева, 0 справа.
- Подробное руководство по размещению: https://tkdocs.com/tutorial/grid.html
