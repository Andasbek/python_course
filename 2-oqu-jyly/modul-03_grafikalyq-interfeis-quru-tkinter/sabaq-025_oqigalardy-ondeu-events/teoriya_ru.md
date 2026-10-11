# Урок 25. Обработка событий (events). Привязка функции к нажатию кнопки.

## Темы

1. Событие, обработчик, цикл событий
2. `command` — нажатие кнопки
3. Функция с аргументом: `lambda`
4. `bind` — привязка любого события
5. Объект `event`
6. `after()` — события времени (таймер)
7. События в стиле ООП
8. Частые ошибки
9. Основные понятия

## 1. Событие, обработчик, цикл событий

- **Событие (event)** — действие, произошедшее в программе: щелчок мыши, нажатие клавиши, перемещение курсора, изменение размера окна, таймер.
- **Обработчик события (event handler / callback)** — функция, вызываемая при возникновении события.
- **Цикл событий (event loop)** — `mainloop()`: по очереди берёт события и вызывает соответствующие обработчики.

```text
Пользователь ──► [Очередь событий] ──► mainloop() ──► функция-обработчик ──► интерфейс обновляется
   щелчок          <Button-1>                          on_click()
```

В Tkinter есть два основных способа привязать обработчик:

| Способ | Где | Что передаётся функции |
|---|---|---|
| `command=...` | `Button`, `Checkbutton`, `Radiobutton`, `Scale`, меню | Ничего (вызывается без аргументов) |
| `widget.bind("<событие>", func)` | Любой виджет | Объект `event` |

## 2. `command` — нажатие кнопки

```python
import tkinter as tk


def on_click():
    label.config(text="Кнопка нажата!")


root = tk.Tk()
root.title("command")
label = tk.Label(root, text="Жду...", font=("Arial", 14))
label.pack(padx=20, pady=10)
tk.Button(root, text="Нажми", command=on_click).pack(pady=10)
root.mainloop()
```

`command` вызывает функцию **без аргументов**. Но часто функции нужно передать значение (например, какая цифра нажата).

## 3. Функция с аргументом: `lambda`

**`lambda`** — однострочная анонимная функция:

```python
square = lambda x: x * x
print(square(5))

add = lambda a, b: a + b
print(add(2, 3))
```

Результат:
```text
25
5
```

В Tkinter `lambda` «оборачивает» функцию с аргументом, создавая функцию без аргументов:

```python
import tkinter as tk


def set_text(text, color):
    label.config(text=text, fg=color)


root = tk.Tk()
root.title("lambda")
label = tk.Label(root, text="Светофор", font=("Arial", 18))
label.pack(pady=10, padx=40)
tk.Button(root, text="Красный", command=lambda: set_text("СТОЙ", "red")).pack(fill="x")
tk.Button(root, text="Жёлтый", command=lambda: set_text("ПРИГОТОВЬСЯ", "orange")).pack(fill="x")
tk.Button(root, text="Зелёный", command=lambda: set_text("ИДИ", "green")).pack(fill="x")
root.mainloop()
```

`lambda: set_text("СТОЙ", "red")` означает «при нажатии вызови `set_text` с этими аргументами».

### Ловушка lambda в цикле

```python
funcs = []
for i in range(3):
    funcs.append(lambda: print("Кнопка", i))

for f in funcs:
    f()
```

Результат:
```text
Кнопка 2
Кнопка 2
Кнопка 2
```

Почему? `i` внутри `lambda` берёт значение **в момент вызова**, а после окончания цикла `i = 2`. Решение — «зафиксировать» значение как параметр по умолчанию:

```python
funcs = []
for i in range(3):
    funcs.append(lambda n=i: print("Кнопка", n))

for f in funcs:
    f()
```

Результат:
```text
Кнопка 0
Кнопка 1
Кнопка 2
```

В Tkinter:

```python
import tkinter as tk


def press(digit):
    display.config(text=display.cget("text") + digit)


root = tk.Tk()
root.title("Цифры")
display = tk.Label(root, text="", font=("Consolas", 20), width=12, anchor="e",
                   relief="sunken")
display.grid(row=0, column=0, columnspan=5, pady=5)
for i in range(10):
    tk.Button(root, text=str(i), width=3,
              command=lambda d=str(i): press(d)).grid(row=1 + i // 5, column=i % 5)
root.mainloop()
```

### Альтернатива: `functools.partial`

```python
from functools import partial


def greet(name, greeting):
    print(f"{greeting}, {name}!")


hello_aru = partial(greet, "Ару", "Привет")   # функция с заранее зафиксированными аргументами
hello_aru()
```

Результат:
```text
Привет, Ару!
```

В Tkinter: `tk.Button(root, text="1", command=partial(press, "1"))`. В цикле тоже работает правильно.

## 4. `bind` — привязка любого события

```text
виджет.bind("<последовательность_события>", функция_обработчик)
```

Функция-обработчик **обязательно принимает один параметр** — объект `event`.

```python
import tkinter as tk


def on_left_click(event):
    info.config(text=f"Левая кнопка: x={event.x}, y={event.y}")


def on_right_click(event):
    info.config(text="Нажата правая кнопка")


def on_double(event):
    info.config(text="Двойной щелчок!")


root = tk.Tk()
root.title("События мыши")
area = tk.Label(root, text="Щёлкните по этой области", bg="#dfe6e9", width=40, height=10)
area.pack(padx=10, pady=10)
info = tk.Label(root, text="")
info.pack(pady=5)

area.bind("<Button-1>", on_left_click)
area.bind("<Button-3>", on_right_click)     # в macOS правая кнопка иногда <Button-2>
area.bind("<Double-Button-1>", on_double)
area.bind("<Enter>", lambda e: area.config(bg="#ffeaa7"))
area.bind("<Leave>", lambda e: area.config(bg="#dfe6e9"))
root.mainloop()
```

### Основные последовательности событий

| Последовательность | Событие |
|---|---|
| `<Button-1>` / `<Button-2>` / `<Button-3>` | Левая / средняя / правая кнопка мыши |
| `<Double-Button-1>` | Двойной щелчок |
| `<ButtonRelease-1>` | Отпускание кнопки |
| `<B1-Motion>` | Перемещение с зажатой левой кнопкой (перетаскивание) |
| `<Motion>` | Перемещение курсора |
| `<Enter>` / `<Leave>` | Курсор вошёл в виджет / вышел из него |
| `<MouseWheel>` | Колёсико мыши (Windows/macOS) |
| `<Key>` / `<KeyPress>` | Любая клавиша |
| `<Return>` | Клавиша Enter |
| `<Escape>`, `<space>`, `<BackSpace>`, `<Tab>` | Соответствующие клавиши |
| `<Up>`, `<Down>`, `<Left>`, `<Right>` | Клавиши-стрелки |
| `<a>`, `<A>` | Конкретная буквенная клавиша |
| `<Control-s>`, `<Alt-x>`, `<Shift-Up>` | Сочетания клавиш |
| `<FocusIn>` / `<FocusOut>` | Виджет получил / потерял фокус |
| `<Configure>` | Изменился размер или положение виджета |

## 5. Объект `event`

| Атрибут | Смысл |
|---|---|
| `event.x`, `event.y` | Координаты курсора внутри виджета |
| `event.x_root`, `event.y_root` | Координаты курсора на экране |
| `event.widget` | Виджет, в котором произошло событие |
| `event.keysym` | Название клавиши: `"Return"`, `"a"`, `"Up"`, `"Escape"` |
| `event.char` | Нажатый символ (`"a"`, `"5"`); для специальных клавиш пусто |
| `event.num` | Номер кнопки мыши |
| `event.width`, `event.height` | Новый размер при `<Configure>` |
| `event.delta` | Направление колёсика при `<MouseWheel>` |

### События клавиатуры

```python
import tkinter as tk


def on_key(event):
    info.config(text=f"keysym: {event.keysym!r}, char: {event.char!r}")


def on_escape(event):
    root.destroy()


root = tk.Tk()
root.title("Клавиатура")
root.geometry("350x120")
info = tk.Label(root, text="Нажмите любую клавишу (Esc — выход)", font=("Arial", 12))
info.pack(expand=True)
root.bind("<Key>", on_key)          # root.bind — на всё окно
root.bind("<Escape>", on_escape)
root.mainloop()
```

### Отправка формы по Enter

```python
import tkinter as tk


def search(event=None):          # event=None: подходит и для command, и для bind
    text = entry.get().strip()
    result.config(text=f"Поиск: {text}" if text else "Пустой запрос")


root = tk.Tk()
root.title("Поиск")
entry = tk.Entry(root, width=30)
entry.pack(side="left", padx=5, pady=10)
entry.focus()
tk.Button(root, text="Искать", command=search).pack(side="left", padx=5)
result = tk.Label(root, text="")
result.pack(side="left", padx=5)
entry.bind("<Return>", search)
root.mainloop()
```

Приём `event=None` очень полезен: одну функцию можно вызывать и через `command` (без аргументов), и через `bind` (с `event`).

## 6. `after()` — события времени (таймер)

`widget.after(мс, функция)` — вызывает функцию **один раз** через указанное количество миллисекунд. Если функция планирует саму себя заново — получается таймер.

```python
import tkinter as tk

seconds = 10


def tick():
    global seconds
    if seconds > 0:
        label.config(text=str(seconds))
        seconds -= 1
        root.after(1000, tick)       # вызвать снова через 1 секунду
    else:
        label.config(text="Время вышло!", fg="red")


root = tk.Tk()
root.title("Обратный отсчёт")
label = tk.Label(root, text="", font=("Arial", 40))
label.pack(padx=40, pady=20)
tick()
root.mainloop()
```

**Важно:** не используйте `time.sleep()` в GUI-программе — он останавливает `mainloop`, и окно «зависает» («Не отвечает»). Всегда используйте `after()`.

Остановка таймера: `job = root.after(1000, tick)` → `root.after_cancel(job)`.

## 7. События в стиле ООП

```python
import tkinter as tk


class ClickGame(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Кликер")
        self.score = 0
        self.label = tk.Label(self, text="Очки: 0", font=("Arial", 18))
        self.label.pack(pady=10, padx=40)
        self.button = tk.Button(self, text="Жми!", width=10, command=self.add_point)
        self.button.pack(pady=5)
        self.bind("<space>", self.add_point)
        self.bind("<Control-r>", self.reset)

    def add_point(self, event=None):
        self.score += 1
        self.label.config(text=f"Очки: {self.score}")

    def reset(self, event=None):
        self.score = 0
        self.label.config(text="Очки: 0")


if __name__ == "__main__":
    ClickGame().mainloop()
```

Методы (`self.add_point`) отлично работают как обработчики — глобальные переменные не нужны.

## Частые ошибки

**1. Забыли параметр `event` в обработчике `bind`**

```python
import tkinter as tk

def on_click():                 # ОШИБКА: нет параметра event
    print("нажато")

root = tk.Tk()
lbl = tk.Label(root, text="Нажми")
lbl.pack()
lbl.bind("<Button-1>", on_click)
# При щелчке: TypeError: on_click() takes 0 positional arguments but 1 was given
root.destroy()
```

Решение: `def on_click(event):` или `def on_click(event=None):`.

**2. `command=func(arg)`** — функция вызывается сразу. Правильно: `command=lambda: func(arg)`.

**3. `lambda` в цикле даёт всем последнее значение** — `lambda x=i: ...` или `partial`.

**4. Ошибка в названии события** — `"<button-1>"` (строчная буква), считать `"<Enter>"` клавишей Enter (это вход курсора!). Клавиша Enter — `"<Return>"`.

**5. Привязка события клавиши к виджету без фокуса** — `Label.bind("<Key>", ...)` не работает, потому что Label не получает фокус. Используйте `root.bind(...)` или `Entry.bind(...)`.

**6. Ожидание через `time.sleep()`** — окно зависает. Используйте `after()`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Событие (event) | Действие в программе: щелчок, ввод, перемещение, таймер |
| Обработчик (handler, callback) | Функция, вызываемая в ответ на событие |
| `command` | Привязка основного действия виджетов типа кнопки |
| `bind()` | Привязка любого события к функции |
| Последовательность события | Описание события вида `"<Button-1>"`, `"<Return>"` |
| Объект `event` | Информация о событии: координаты, клавиша, виджет |
| `lambda` | Однострочная анонимная функция |
| `functools.partial` | Функция с заранее зафиксированными аргументами |
| `after()` | Вызов функции по истечении времени |

## Примечание

- Если обработчик `bind` возвращает строку `"break"`, дальнейшая обработка события прекращается (например, отменяется стандартное поведение виджета — ввод символа в `Entry`).
- Повторный `bind` на то же событие **заменяет** прежний обработчик; чтобы добавить ещё один, используйте `widget.bind("<Button-1>", func, add="+")`.
- `bind_all` привязывает событие ко всему приложению, `bind_class` — ко всем виджетам одного класса.
- Колёсико мыши в Linux приходит как `<Button-4>`/`<Button-5>`, а не `<MouseWheel>`; в Windows `event.delta` кратно 120.
- Долгие вычисления внутри обработчика «замораживают» окно: разбивайте работу на части через `after()` или выносите её в отдельный поток (`threading`), обновляя виджеты только из главного потока.
- `after(ms, func, *args)` умеет передавать аргументы: `root.after(1000, show, "текст")`.
