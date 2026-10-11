# Урок 23. Основные виджеты: Label, Button, Entry.

## Темы

1. Общие свойства виджетов
2. Label — надпись
3. Button — кнопка
4. Entry — однострочное поле ввода
5. Работа с числами: проверка данных
6. Управляющие переменные: `StringVar`, `IntVar`
7. Всё вместе: форма «Вход»
8. Частые ошибки
9. Основные понятия

## 1. Общие свойства виджетов

При создании любого виджета первый аргумент — **родительский виджет** (master), затем именованные аргументы (опции):

```text
виджет = tk.ИмяВиджета(родительский_виджет, опция1=значение1, опция2=значение2, ...)
```

| Опция | Смысл | Пример |
|---|---|---|
| `text` | Текст | `text="Привет"` |
| `font` | Шрифт: (название, размер, стиль) | `font=("Arial", 14, "bold")` |
| `fg` | Цвет текста (foreground) | `fg="white"` |
| `bg` | Цвет фона (background) | `bg="#2e86de"` |
| `width`, `height` | Размер (**в текстовых единицах** — количество символов/строк) | `width=20` |
| `padx`, `pady` | Внутренний отступ | `padx=10` |
| `relief` | Стиль рамки: `flat`, `raised`, `sunken`, `groove`, `ridge`, `solid` | `relief="groove"` |
| `bd` / `borderwidth` | Толщина рамки | `bd=2` |
| `cursor` | Курсор мыши | `cursor="hand2"` |

Изменение свойства: `widget.config(опция=значение)`, чтение: `widget.cget("опция")` или `widget["опция"]`.

## 2. Label — надпись

```python
import tkinter as tk

root = tk.Tk()
root.title("Примеры Label")

tk.Label(root, text="Простая надпись").pack()
tk.Label(root, text="Большая жирная надпись", font=("Arial", 20, "bold")).pack()
tk.Label(root, text="Цветная", fg="white", bg="#e74c3c", padx=10, pady=5).pack(pady=5)
tk.Label(root, text="Надпись с рамкой", relief="ridge", bd=3, width=25).pack(pady=5)
tk.Label(root, text="Длинный текст автоматически переносится на новую строку, "
                    "потому что задано свойство wraplength.",
         wraplength=200, justify="left").pack(pady=5)
tk.Label(root, text="Выровнено влево", width=30, anchor="w", bg="lightyellow").pack()

root.mainloop()
```

| Опция | Смысл |
|---|---|
| `wraplength` | Текст переносится на новую строку, когда превышает эту ширину (пиксели) |
| `justify` | Выравнивание многострочного текста: `left`, `center`, `right` |
| `anchor` | Положение текста внутри виджета: `w` (запад/слева), `e` (справа), `n`, `s`, `center`, `nw`... |

### Изменение текста Label

```python
import tkinter as tk
import time


def show_time():
    clock.config(text=time.strftime("%H:%M:%S"))


root = tk.Tk()
root.title("Часы")
clock = tk.Label(root, text="--:--:--", font=("Consolas", 30))
clock.pack(padx=20, pady=10)
tk.Button(root, text="Показать время", command=show_time).pack(pady=5)
root.mainloop()
```

## 3. Button — кнопка

```python
import tkinter as tk

clicks = 0


def on_click():
    global clicks
    clicks += 1
    info.config(text=f"Нажато: {clicks} раз")
    if clicks >= 5:
        btn.config(state="disabled", text="Лимит исчерпан")


root = tk.Tk()
root.title("Button")
info = tk.Label(root, text="Нажато: 0 раз", font=("Arial", 14))
info.pack(pady=10)
btn = tk.Button(root, text="Нажми меня!", command=on_click,
                bg="#27ae60", fg="white", activebackground="#2ecc71",
                font=("Arial", 12), width=15, cursor="hand2")
btn.pack(pady=10)
root.mainloop()
```

| Опция | Смысл |
|---|---|
| `command` | Функция, вызываемая при нажатии (без скобок) |
| `state` | `"normal"` — активна, `"disabled"` — неактивна (серая) |
| `activebackground`, `activeforeground` | Цвета в момент нажатия |
| `width` | Ширина (в символах) |

Примечание: в macOS цвет фона `Button` (`bg`) может не отображаться из-за системного стиля — это нормально; для единообразного вида на всех системах используют `ttk.Button` со стилями `ttk.Style`.

## 4. Entry — однострочное поле ввода

```python
import tkinter as tk


def greet():
    name = entry.get().strip()          # получить текст из поля
    if name:
        result.config(text=f"Привет, {name}!", fg="green")
    else:
        result.config(text="Введите своё имя!", fg="red")


def clear():
    entry.delete(0, tk.END)             # очистить поле
    result.config(text="")
    entry.focus()                       # поставить курсор в поле


root = tk.Tk()
root.title("Entry")
tk.Label(root, text="Ваше имя:").pack(pady=(10, 0))
entry = tk.Entry(root, width=25, font=("Arial", 12))
entry.pack(padx=20, pady=5)
entry.insert(0, "Ару")                  # начальный текст
entry.focus()
tk.Button(root, text="Поздороваться", command=greet).pack(pady=5)
tk.Button(root, text="Очистить", command=clear).pack(pady=5)
result = tk.Label(root, text="", font=("Arial", 14))
result.pack(pady=5)
root.mainloop()
```

### Основные методы Entry

| Метод | Смысл |
|---|---|
| `get()` | Возвращает текст поля в виде **строки** |
| `insert(index, text)` | Вставляет текст в позицию `index` (`0` — в начало, `tk.END` — в конец) |
| `delete(first, last)` | Удаляет текст; `delete(0, tk.END)` — полная очистка |
| `focus()` | Ставит курсор в поле |

### Опции Entry

| Опция | Смысл |
|---|---|
| `show="*"` | Поле пароля: введённые символы отображаются как `*` |
| `width` | Ширина (в символах) |
| `state="readonly"` | Только для чтения |
| `justify="center"` | Выравнивание текста |

## 5. Работа с числами: проверка данных

`get()` всегда возвращает **строку**! Нужно преобразовать её в число и перехватить неверный ввод:

```python
import tkinter as tk


def add():
    try:
        a = float(entry_a.get())
        b = float(entry_b.get())
    except ValueError:
        result.config(text="Ошибка: вводите только числа!", fg="red")
        return
    result.config(text=f"Сумма: {a + b:g}", fg="black")


root = tk.Tk()
root.title("Сложение")
entry_a = tk.Entry(root, width=10, justify="center")
entry_a.pack(side="left", padx=5, pady=10)
tk.Label(root, text="+").pack(side="left")
entry_b = tk.Entry(root, width=10, justify="center")
entry_b.pack(side="left", padx=5)
tk.Button(root, text="=", command=add).pack(side="left", padx=5)
result = tk.Label(root, text="", width=25)
result.pack(side="left", padx=5)
root.mainloop()
```

Формат `:g` убирает лишние нули: `5.0` → `5`, `2.5` → `2.5`.

## 6. Управляющие переменные: `StringVar`, `IntVar`

В Tkinter есть специальные переменные, **автоматически связанные** с виджетом:

| Класс | Хранимый тип |
|---|---|
| `tk.StringVar()` | строка |
| `tk.IntVar()` | целое число |
| `tk.DoubleVar()` | дробное число |
| `tk.BooleanVar()` | `True`/`False` |

Методы: `.get()` — получить значение, `.set(значение)` — установить значение. С виджетом связываем через `textvariable=`:

```python
import tkinter as tk


def to_upper():
    text_var.set(text_var.get().upper())


root = tk.Tk()
root.title("StringVar")
text_var = tk.StringVar(value="алматы")

tk.Entry(root, textvariable=text_var, width=25).pack(padx=20, pady=5)
tk.Label(root, textvariable=text_var, font=("Arial", 16), fg="blue").pack(pady=5)
tk.Button(root, text="ЗАГЛАВНЫМИ", command=to_upper).pack(pady=5)
root.mainloop()
```

При каждом вводе в поле текст `Label` меняется **сразу** — потому что оба связаны с одной переменной. (Примечание: `StringVar` создаётся только после создания `Tk()`.)

## 7. Всё вместе: форма «Вход»

```python
import tkinter as tk

USERS = {"aru": "1234", "erlan": "qwerty"}   # пример только в учебных целях!


def login():
    user = login_var.get().strip().lower()
    password = pass_entry.get()
    if USERS.get(user) == password:
        status.config(text=f"Добро пожаловать, {user}!", fg="green")
        login_btn.config(state="disabled")
    else:
        status.config(text="Неверный логин или пароль", fg="red")
        pass_entry.delete(0, tk.END)


root = tk.Tk()
root.title("Вход")
root.geometry("280x200")
root.resizable(False, False)

login_var = tk.StringVar()
tk.Label(root, text="Логин:").pack(pady=(15, 0))
tk.Entry(root, textvariable=login_var).pack()
tk.Label(root, text="Пароль:").pack(pady=(10, 0))
pass_entry = tk.Entry(root, show="*")
pass_entry.pack()
login_btn = tk.Button(root, text="Войти", width=12, command=login)
login_btn.pack(pady=10)
status = tk.Label(root, text="")
status.pack()
root.mainloop()
```

Замечание о безопасности: в реальных приложениях пароли не хранятся в коде в открытом виде — они хэшируются и хранятся в базе данных.

## Частые ошибки

**1. Сохранение результата `pack()` в переменную**

```python
import tkinter as tk
root = tk.Tk()
entry = tk.Entry(root).pack()     # ОШИБКА: pack() возвращает None!
# entry.get()  -> AttributeError: 'NoneType' object has no attribute 'get'
root.destroy()
```

Правильно — разделить на две строки:

```python
import tkinter as tk
root = tk.Tk()
entry = tk.Entry(root)
entry.pack()
root.destroy()
```

**2. Не преобразовали результат `get()` в число** — `entry.get() + 5` → `TypeError`.

**3. Писать `delete(0)`** — удалится только первый символ. Полная очистка: `delete(0, tk.END)`.

**4. Вызов функции до создания кнопки / ошибка в имени поля внутри функции** — `NameError`. Создавайте переменные виджетов до вызова функции (до `mainloop`).

**5. Не проверять пустое поле** — `float("")` → `ValueError`. Используйте `try/except` или `if not text:`.

**6. Создание `StringVar` до `Tk()`** → `RuntimeError: Too early to create variable`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `Label` | Виджет, показывающий текст (или изображение) |
| `Button` | Кнопка, вызывающая функцию `command` при нажатии |
| `Entry` | Однострочное поле ввода текста |
| `get / insert / delete` | Методы чтения, вставки, удаления текста Entry |
| `tk.END` | Константа, обозначающая конец текста |
| `StringVar`, `IntVar` | Управляющие переменные, связываемые с виджетом |
| `textvariable` | Опция связывания виджета с управляющей переменной |
| `state` | Состояние виджета: `normal`, `disabled`, `readonly` |

## Примечание

- `IntVar.get()` и `DoubleVar.get()` выбрасывают `TclError`, если в связанном поле записан нечисловой текст — оборачивайте вызов в `try/except`.
- Шрифт удобнее задавать кортежем: `("Segoe UI", 12, "bold")`. В строковой форме имя с пробелом нужно брать в фигурные скобки: `"{Segoe UI} 12 bold"`.
- Для проверки ввода «на лету» у `Entry` есть опции `validate` и `validatecommand` (например, разрешить только цифры).
- `global` в обработчиках допустим в маленьких скриптах; в больших программах состояние лучше хранить в атрибутах класса.
- Пароли никогда не хранят открытым текстом: для хэширования используют `hashlib` (например, `hashlib.pbkdf2_hmac`), для случайных токенов — модуль `secrets`.
- Документация: https://docs.python.org/3/library/tkinter.html
