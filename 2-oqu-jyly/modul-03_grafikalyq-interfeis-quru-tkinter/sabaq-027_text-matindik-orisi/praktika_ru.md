# Урок 27. Практические задания: Text и Scrollbar

## Базовый уровень

### Задание 1. Дневник
Поле Text с полосой прокрутки (`wrap="word"`) и кнопка «Добавить дату»: в место курсора вставляются сегодняшние дата и время («11.10.2026 15:30 — »).

### Задание 2. Счётчик слов
Поле Text и под ним живая статистика: символы (и без пробелов), слова, предложения (количество `.`, `!`, `?`), строки. Статистика обновляется при каждом отпускании клавиши (`<KeyRelease>`).

### Задание 3. Преобразователь регистра
Поле Text и кнопки «ЗАГЛАВНЫЕ», «строчные», «Каждое Слово С Заглавной» (`title()`). Преобразованный текст записывается обратно в то же поле.

### Задание 4. Превращение в список
Слева Text (в каждой строке имя одного ученика), справа Listbox с полосой прокрутки. Кнопка «Скопировать в список» выводит непустые строки в Listbox, **отсортировав по алфавиту**.

## Продвинутый уровень

### Задание 5. Найти и заменить
Поле Text, поля «Найти» и «Заменить», кнопка «Заменить все». Заменённые слова выделяются зелёным фоном, внизу — «Выполнено замен: N».

### Задание 6. Журнал чат-бота
Text только для чтения (журнал) + Entry. Сообщение пользователя выводится синим цветом (тег `user`), ответ бота — зелёным (тег `bot`). Бот отвечает на ключевые слова (`"привет"`, `"python"`, `"время"`, `"пока"`), в остальных случаях — «Не понял, спросите, пожалуйста, иначе». При каждом новом сообщении журнал прокручивается в конец.

### Задание 7. Номера строк
Слева от поля Text должно быть узкое поле Text (`state="disabled"`), показывающее номера строк. При изменении текста номера обновляются. (Дополнительно: оба поля прокручиваются вместе одним Scrollbar.)

## ★ Олимпиадная / творческая задача

### Задание 8. Подсветка синтаксиса Python
При вводе Python-кода в поле Text ключевые слова (`keyword.kwlist`) подсвечиваются оранжевым, строки (`"..."`) — зелёным, комментарии (`# ...`) — серым. Подсказка: разбирайте каждую строку через `re.finditer` и вычисляйте индексы `"{строка}.{начало}"`; подсветка обновляется по событию `<KeyRelease>`.

## Домашнее задание

1. Объедините задания 2 и 5 в приложение «Инструменты для текста».
2. Вставьте в Text любое из «Слов назидания» Абая и выведите в Listbox 5 самых частых слов (длиннее 3 букв) с их частотой (`collections.Counter`).
3. Загрузите на GitHub под именем `gui/lesson27_text.py`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import datetime
import tkinter as tk
from tkinter import scrolledtext


def add_date():
    stamp = datetime.datetime.now().strftime("%d.%m.%Y %H:%M — ")
    diary.insert("insert", stamp)
    diary.focus()


root = tk.Tk()
root.title("Дневник")
diary = scrolledtext.ScrolledText(root, width=50, height=15, wrap="word")
diary.pack(padx=10, pady=5, fill="both", expand=True)
tk.Button(root, text="Добавить дату", command=add_date).pack(pady=5)
root.mainloop()
```

**Задание 2**
```python
import tkinter as tk


def update(event=None):
    s = text.get("1.0", "end-1c")
    no_spaces = len(s.replace(" ", "").replace("\n", ""))
    sentences = sum(s.count(ch) for ch in ".!?")
    lines = s.count("\n") + 1 if s else 0
    stats.config(text=f"Символы: {len(s)} (без пробелов: {no_spaces}) | "
                      f"Слова: {len(s.split())} | Предложения: {sentences} | Строки: {lines}")


root = tk.Tk()
root.title("Счётчик")
text = tk.Text(root, width=60, height=12, wrap="word")
text.pack(padx=10, pady=5)
text.bind("<KeyRelease>", update)
stats = tk.Label(root, text="")
stats.pack(pady=5)
update()
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk


def transform(func):
    content = text.get("1.0", "end-1c")
    text.delete("1.0", tk.END)
    text.insert("1.0", func(content))


root = tk.Tk()
root.title("Регистр")
text = tk.Text(root, width=50, height=8, wrap="word")
text.pack(padx=10, pady=5)
bar = tk.Frame(root)
bar.pack(pady=5)
tk.Button(bar, text="ЗАГЛАВНЫЕ", command=lambda: transform(str.upper)).pack(side="left", padx=3)
tk.Button(bar, text="строчные", command=lambda: transform(str.lower)).pack(side="left", padx=3)
tk.Button(bar, text="Каждое Слово С Заглавной",
          command=lambda: transform(str.title)).pack(side="left", padx=3)
root.mainloop()
```

**Задание 4**
```python
import tkinter as tk


def copy_to_list():
    names = [line.strip() for line in text.get("1.0", "end-1c").splitlines() if line.strip()]
    lb.delete(0, tk.END)
    for name in sorted(names):
        lb.insert(tk.END, name)


root = tk.Tk()
root.title("Список")
text = tk.Text(root, width=25, height=12)
text.grid(row=0, column=0, padx=5, pady=5)
tk.Button(root, text="Скопировать\nв список →", command=copy_to_list).grid(row=0, column=1)
right = tk.Frame(root)
right.grid(row=0, column=2, padx=5, pady=5)
sb = tk.Scrollbar(right)
sb.pack(side="right", fill="y")
lb = tk.Listbox(right, height=12, yscrollcommand=sb.set)
lb.pack(side="left")
sb.config(command=lb.yview)
root.mainloop()
```

**Задание 5**
```python
import tkinter as tk


def replace_all():
    old, new = find_e.get(), repl_e.get()
    if not old:
        return
    content = text.get("1.0", "end-1c")
    count = content.count(old)
    text.delete("1.0", tk.END)
    text.insert("1.0", content.replace(old, new))
    text.tag_remove("done", "1.0", tk.END)
    if new:
        start = "1.0"
        while True:
            pos = text.search(new, start, stopindex=tk.END)
            if not pos:
                break
            end = f"{pos}+{len(new)}c"
            text.tag_add("done", pos, end)
            start = end
    info.config(text=f"Выполнено замен: {count}")


root = tk.Tk()
root.title("Замена")
top = tk.Frame(root)
top.pack(pady=5)
tk.Label(top, text="Найти:").grid(row=0, column=0)
find_e = tk.Entry(top)
find_e.grid(row=0, column=1, padx=3)
tk.Label(top, text="Заменить:").grid(row=0, column=2)
repl_e = tk.Entry(top)
repl_e.grid(row=0, column=3, padx=3)
tk.Button(top, text="Заменить все", command=replace_all).grid(row=0, column=4, padx=3)
text = tk.Text(root, width=70, height=12, wrap="word")
text.pack(padx=10, pady=5)
text.tag_config("done", background="#b8e994")
info = tk.Label(root, text="")
info.pack()
root.mainloop()
```

**Задание 6**
```python
import time
import tkinter as tk
from tkinter import scrolledtext

ANSWERS = {
    "привет": "Привет! Как дела?",
    "python": "Python — отличный язык! Мы изучаем его в кружке.",
    "время": lambda: "Сейчас " + time.strftime("%H:%M"),
    "пока": "Пока! Заходи ещё.",
}


def bot_reply(msg):
    low = msg.lower()
    for key, answer in ANSWERS.items():
        if key in low:
            return answer() if callable(answer) else answer
    return "Не понял, спросите, пожалуйста, иначе"


def write(line, tag):
    log.config(state="normal")
    log.insert(tk.END, line + "\n", tag)
    log.see(tk.END)
    log.config(state="disabled")


def send(event=None):
    msg = entry.get().strip()
    if not msg:
        return
    write("Вы: " + msg, "user")
    write("Бот: " + bot_reply(msg), "bot")
    entry.delete(0, tk.END)


root = tk.Tk()
root.title("Чат-бот")
log = scrolledtext.ScrolledText(root, width=50, height=15, state="disabled", wrap="word")
log.pack(padx=10, pady=5)
log.tag_config("user", foreground="#0984e3")
log.tag_config("bot", foreground="#00b894")
entry = tk.Entry(root, width=40)
entry.pack(side="left", padx=10, pady=5)
entry.bind("<Return>", send)
entry.focus()
tk.Button(root, text="Отправить", command=send).pack(side="left")
root.mainloop()
```

**Задание 7**
```python
import tkinter as tk


def update_numbers(event=None):
    count = int(text.index("end-1c").split(".")[0])
    numbers.config(state="normal")
    numbers.delete("1.0", tk.END)
    numbers.insert("1.0", "\n".join(str(i) for i in range(1, count + 1)))
    numbers.config(state="disabled")
    numbers.yview_moveto(text.yview()[0])


def on_scroll(*args):
    text.yview(*args)
    numbers.yview(*args)


def on_text_scroll(first, last):
    sb.set(first, last)
    numbers.yview_moveto(first)


root = tk.Tk()
root.title("Номера строк")
sb = tk.Scrollbar(root, command=on_scroll)
sb.pack(side="right", fill="y")
numbers = tk.Text(root, width=4, height=20, bg="#dfe6e9", state="disabled")
numbers.pack(side="left", fill="y")
text = tk.Text(root, width=60, height=20, wrap="none", yscrollcommand=on_text_scroll)
text.pack(side="left", fill="both", expand=True)
text.bind("<KeyRelease>", update_numbers)
update_numbers()
root.mainloop()
```

**Задание 8**
```python
import keyword
import re
import tkinter as tk

KW = re.compile(r"\b(" + "|".join(keyword.kwlist) + r")\b")
STR = re.compile(r"(\".*?\"|'.*?')")
COMMENT = re.compile(r"#.*$")


def highlight(event=None):
    for tag in ("kw", "str", "comment"):
        text.tag_remove(tag, "1.0", tk.END)
    lines = text.get("1.0", "end-1c").split("\n")
    for n, line in enumerate(lines, start=1):
        for tag, pattern in (("kw", KW), ("str", STR), ("comment", COMMENT)):
            for m in pattern.finditer(line):
                text.tag_add(tag, f"{n}.{m.start()}", f"{n}.{m.end()}")


root = tk.Tk()
root.title("Редактор кода")
text = tk.Text(root, width=70, height=20, font=("Consolas", 12), undo=True)
text.pack(padx=5, pady=5)
text.tag_config("kw", foreground="#e17055")
text.tag_config("str", foreground="#00b894")
text.tag_config("comment", foreground="#95a5a6")
text.tag_raise("comment")
text.bind("<KeyRelease>", highlight)
text.insert("1.0", 'def hello(name):\n    # приветствие\n    return "Привет, " + name\n')
highlight()
root.mainloop()
```

</details>
