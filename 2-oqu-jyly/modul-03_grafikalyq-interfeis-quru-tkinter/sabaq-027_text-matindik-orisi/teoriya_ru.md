# Урок 27. Текстовое поле Text. Полоса прокрутки (Scrollbar).

## Темы

1. Виджет Text
2. Индексы Text
3. Чтение, добавление, удаление текста
4. Теги — форматирование текста
5. Scrollbar — полоса прокрутки
6. Готовое решение: `scrolledtext`
7. Частые ошибки
8. Основные понятия

## 1. Виджет Text

`Entry` — только **одна строка**. Для многострочного текста (письмо, сочинение, код, чат) используется виджет **`Text`**.

| | `Entry` | `Text` |
|---|---|---|
| Строки | Одна | Неограниченно |
| Индекс | Число: `0`, `tk.END` | `"строка.символ"`: `"1.0"`, `tk.END` |
| `textvariable` | Есть | Нет |
| Форматирование (цвета, шрифты) | Для всего поля | Для отдельных частей текста (теги) |
| Вставка изображений, виджетов | Нет | Есть |

```python
import tkinter as tk

root = tk.Tk()
root.title("Text")
text = tk.Text(root, width=40, height=10, wrap="word", font=("Arial", 12))
text.pack(padx=10, pady=10)
text.insert("1.0", "Адам баласына адам баласының бәрі — дос.\n")
text.insert(tk.END, "Абай Құнанбайұлы")
root.mainloop()
```

(Изречение Абая: «Всякий человек человеку — друг».)

| Опция | Смысл |
|---|---|
| `width`, `height` | Размер (количество символов и строк) |
| `wrap` | Перенос строк: `"none"` (без переноса), `"char"` (по символам), `"word"` (по словам) |
| `font`, `fg`, `bg` | Шрифт и цвета |
| `padx`, `pady` | Внутренние отступы |
| `state` | `"normal"` — можно редактировать, `"disabled"` — только чтение |
| `undo` | При `True` работает отмена через `Ctrl+Z` |
| `insertbackground` | Цвет курсора (полезно на тёмном фоне) |

## 2. Индексы Text

Каждая позиция внутри `Text` задаётся в виде `"строка.символ"`:
- **строки** начинаются **с 1**,
- **символы** — **с 0**.

```text
         символ: 0 1 2 3 4 5
строка 1:        П р и в е т
строка 2:        М и р
```

`"1.0"` — начало 1-й строки, `"1.3"` — позиция перед буквой «в», `"2.0"` — начало 2-й строки.

| Индекс | Смысл |
|---|---|
| `"1.0"` | Самое начало текста |
| `tk.END` (`"end"`) | Конец текста (Tkinter всегда добавляет здесь лишний `\n`) |
| `"insert"` (`tk.INSERT`) | Позиция курсора |
| `"2.end"` | Конец 2-й строки |
| `"insert linestart"` | Начало строки, где стоит курсор |
| `"1.0 + 5 chars"` | 5 символов от начала |
| `"end-1c"` | Реальный конец (без последнего лишнего `\n`) |
| `"sel.first"`, `"sel.last"` | Начало и конец выделенного текста |

## 3. Чтение, добавление, удаление текста

```python
import tkinter as tk


def show_stats():
    content = text.get("1.0", "end-1c")      # весь текст, без последнего \n
    lines = content.count("\n") + 1 if content else 0
    words = len(content.split())
    stats.config(text=f"Символы: {len(content)} | Слова: {words} | Строки: {lines}")


def clear():
    text.delete("1.0", tk.END)
    show_stats()


def insert_date():
    import datetime
    text.insert("insert", datetime.date.today().strftime("%d.%m.%Y"))


root = tk.Tk()
root.title("Статистика текста")
text = tk.Text(root, width=50, height=10, wrap="word", undo=True)
text.pack(padx=10, pady=5)
bar = tk.Frame(root)
bar.pack()
tk.Button(bar, text="Посчитать", command=show_stats).pack(side="left", padx=3)
tk.Button(bar, text="Вставить дату", command=insert_date).pack(side="left", padx=3)
tk.Button(bar, text="Очистить", command=clear).pack(side="left", padx=3)
stats = tk.Label(root, text="")
stats.pack(pady=5)
text.bind("<KeyRelease>", lambda e: show_stats())   # подсчёт при каждом отпускании клавиши
root.mainloop()
```

| Метод | Пример | Смысл |
|---|---|---|
| `insert(index, text)` | `text.insert(tk.END, "привет\n")` | Добавить текст |
| `get(start, end)` | `text.get("1.0", "end-1c")` | Получить текст |
| `delete(start, end)` | `text.delete("1.0", tk.END)` | Удалить |
| `see(index)` | `text.see(tk.END)` | Прокрутить к этой позиции |
| `index(index)` | `text.index("insert")` → `"3.5"` | Получить индекс в виде «строка.символ» |
| `search(pattern, start, stopindex)` | `text.search("Абай", "1.0", tk.END)` | Поиск в тексте |

### Text только для чтения (журнал, чат)

При `state="disabled"` текст не может изменить даже код. Поэтому: временно `normal` → добавить → снова `disabled`.

```python
import tkinter as tk
import time


def log(message):
    log_text.config(state="normal")
    log_text.insert(tk.END, f"[{time.strftime('%H:%M:%S')}] {message}\n")
    log_text.see(tk.END)                      # прокрутить к последней строке
    log_text.config(state="disabled")


def send(event=None):
    msg = entry.get().strip()
    if msg:
        log(f"Вы: {msg}")
        entry.delete(0, tk.END)


root = tk.Tk()
root.title("Чат")
log_text = tk.Text(root, width=45, height=12, state="disabled", bg="#f5f6fa")
log_text.pack(padx=10, pady=5)
entry = tk.Entry(root, width=35)
entry.pack(side="left", padx=10, pady=5)
entry.bind("<Return>", send)
tk.Button(root, text="Отправить", command=send).pack(side="left")
log("Добро пожаловать в чат!")
root.mainloop()
```

## 4. Теги — форматирование текста

**Тег (tag)** — имя, присвоенное части текста; тегу задаётся стиль (цвет, шрифт, фон).

```python
import tkinter as tk

root = tk.Tk()
root.title("Теги")
text = tk.Text(root, width=50, height=8, wrap="word", font=("Arial", 12))
text.pack(padx=10, pady=10)

text.tag_config("title", font=("Arial", 16, "bold"), foreground="#2d3436", justify="center")
text.tag_config("quote", font=("Arial", 12, "italic"), foreground="#0984e3")
text.tag_config("mark", background="yellow")

text.insert(tk.END, "Слово четырнадцатое\n", "title")      # текст вместе с тегом
text.insert(tk.END, "«Сердце — море, разум — корабль»\n", "quote")
text.insert(tk.END, "Эти слова прочитайте несколько раз.")
text.tag_add("mark", "3.4", "3.9")                         # добавить тег к слову «слова»
root.mainloop()
```

| Метод | Смысл |
|---|---|
| `tag_config(name, **опции)` | Определение стиля тега: `foreground`, `background`, `font`, `underline`, `justify` |
| `insert(index, text, tag)` | Добавить текст сразу с тегом |
| `tag_add(name, start, end)` | Поставить тег на существующий текст |
| `tag_remove(name, start, end)` | Снять тег |

### Поиск и выделение

```python
import tkinter as tk

SAMPLE = ("Python — язык программирования. Python легко изучать. "
          "На языке Python можно создавать игры и сайты.")


def highlight():
    text.tag_remove("found", "1.0", tk.END)
    word = entry.get()
    if not word:
        return
    count = 0
    start = "1.0"
    while True:
        pos = text.search(word, start, stopindex=tk.END, nocase=True)
        if not pos:
            break
        end = f"{pos}+{len(word)}c"
        text.tag_add("found", pos, end)
        start = end
        count += 1
    info.config(text=f"Найдено: {count}")


root = tk.Tk()
root.title("Поиск")
entry = tk.Entry(root)
entry.pack(pady=5)
tk.Button(root, text="Выделить", command=highlight).pack()
text = tk.Text(root, width=50, height=6, wrap="word")
text.pack(padx=10, pady=5)
text.insert("1.0", SAMPLE)
text.tag_config("found", background="#ffeaa7", foreground="#d63031")
info = tk.Label(root, text="")
info.pack()
root.mainloop()
```

## 5. Scrollbar — полоса прокрутки

Если текст не помещается в поле, нужна полоса прокрутки. Scrollbar и Text связываются **в обе стороны**:

```text
   Text  ──yscrollcommand=scroll.set──►  Scrollbar   (текст сдвинулся — ползунок сдвигается)
   Text  ◄──command=text.yview─────────  Scrollbar   (потянули ползунок — текст сдвигается)
```

```python
import tkinter as tk

root = tk.Tk()
root.title("Scrollbar")

frame = tk.Frame(root)
frame.pack(fill="both", expand=True, padx=10, pady=10)

scroll = tk.Scrollbar(frame)
scroll.pack(side="right", fill="y")

text = tk.Text(frame, width=40, height=10, wrap="word", yscrollcommand=scroll.set)
text.pack(side="left", fill="both", expand=True)
scroll.config(command=text.yview)

for i in range(1, 51):
    text.insert(tk.END, f"Строка {i}: изучаем Python!\n")

root.mainloop()
```

**Правило:** сначала разместите Scrollbar с `side="right", fill="y"`, затем Text — тогда при уменьшении окна полоса не исчезнет.

### Горизонтальная прокрутка

При `wrap="none"` для длинных строк нужна горизонтальная полоса:

```python
import tkinter as tk

root = tk.Tk()
root.title("Две полосы")
text = tk.Text(root, width=30, height=8, wrap="none")
ys = tk.Scrollbar(root, orient="vertical", command=text.yview)
xs = tk.Scrollbar(root, orient="horizontal", command=text.xview)
text.config(yscrollcommand=ys.set, xscrollcommand=xs.set)

text.grid(row=0, column=0, sticky="nsew")
ys.grid(row=0, column=1, sticky="ns")
xs.grid(row=1, column=0, sticky="ew")
root.rowconfigure(0, weight=1)
root.columnconfigure(0, weight=1)

for i in range(30):
    text.insert(tk.END, f"{i:02}: " + "очень длинная строка " * 8 + "\n")
root.mainloop()
```

### Scrollbar для Listbox

```python
import tkinter as tk

root = tk.Tk()
root.title("Ученики")
scroll = tk.Scrollbar(root)
scroll.pack(side="right", fill="y")
lb = tk.Listbox(root, height=6, yscrollcommand=scroll.set)
lb.pack(side="left", fill="both", expand=True)
scroll.config(command=lb.yview)
for i in range(1, 101):
    lb.insert(tk.END, f"Ученик {i}")
root.mainloop()
```

## 6. Готовое решение: `scrolledtext`

`tkinter.scrolledtext.ScrolledText` — готовый Text со встроенной вертикальной полосой прокрутки:

```python
import tkinter as tk
from tkinter import scrolledtext

root = tk.Tk()
root.title("ScrolledText")
st = scrolledtext.ScrolledText(root, width=50, height=10, wrap="word", font=("Consolas", 11))
st.pack(padx=10, pady=10, fill="both", expand=True)
st.insert(tk.END, "Напишите здесь длинный текст...\n" * 30)
root.mainloop()
```

Методы — такие же, как у `Text`.

## Частые ошибки

**1. Передача индекса в виде числа**

```python
import tkinter as tk
root = tk.Tk()
t = tk.Text(root)
# t.get(0, tk.END)   -> TclError: bad text index "0"
print(repr(t.get("1.0", "end-1c")))   # правильно: "1.0"
root.destroy()
```

**2. Счёт строк с 0** — в Text строки начинаются **с 1**, символы — с 0.

**3. Лишний `\n` в конце `get("1.0", tk.END)`** — при сравнении/сохранении используйте `"end-1c"` или `.strip()`.

**4. `insert` в поле со `state="disabled"`** — ничего не добавится (и ошибки не будет). Сначала `config(state="normal")`.

**5. Односторонняя связь Scrollbar** — если указан только `command=text.yview`, ползунок не двигается вместе с текстом; `yscrollcommand=scroll.set` обязателен.

**6. Искать `textvariable` для Text** — у Text его нет; текстом управляем через `get`/`insert`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `Text` | Многострочное форматируемое текстовое поле |
| Индекс `"строка.символ"` | Позиция в Text (строка с 1, символ с 0) |
| `tk.END`, `"end-1c"` | Конец текста; конец без последнего `\n` |
| `"insert"` | Текущая позиция курсора |
| `wrap` | Режим переноса строк |
| Тег (tag) | Имя стиля, присвоенное части текста |
| `Scrollbar` | Полоса прокрутки |
| `yscrollcommand` / `yview` | Двусторонняя связь Text и Scrollbar |
| `ScrolledText` | Готовый Text со встроенной полосой прокрутки |

## Примечание

- При `undo=True` доступны методы `edit_undo()` и `edit_redo()`; `edit_reset()` очищает историю отмен (полезно после открытия нового файла).
- `text.search(..., regexp=True)` ищет по регулярным выражениям Tcl — их синтаксис похож, но не совпадает полностью с модулем `re`.
- Большой текст быстрее вставить одним вызовом `insert`, чем построчно в цикле.
- Полезные модификаторы индексов: `"insert lineend"`, `"1.0 wordend"`, `"end-1c"`; `text.index(...)` переводит их в вид `"строка.символ"`.
- `ScrolledText` — это `Text`, вложенный во `Frame` вместе с полосой прокрутки; методы `pack`/`grid` применяются ко всей конструкции.
- Документация: https://docs.python.org/3/library/tkinter.scrolledtext.html
