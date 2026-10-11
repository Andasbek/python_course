# Урок 28. Практические задания: Menu, messagebox, filedialog

## Базовый уровень

### Задание 1. Окно с меню
Создайте окно с меню «Файл» (Новый, Выход), «Цвет» (Белый, Жёлтый, Голубой — меняют фон окна), «Справка» (О программе — `showinfo`).

### Задание 2. Выход с подтверждением
Кнопка «✕» окна и пункт «Файл → Выход» должны спрашивать «Выйти из программы?» (`askyesno`). Окно закрывается только при ответе «Да».

### Задание 3. Три вида сообщений
В Entry вводится оценка (1–5). Кнопка «Проверить»: не число — `showerror`; 1–2 — `showwarning` («Нужно лучше готовиться»); 3–5 — `showinfo` («Молодец!»); вне диапазона — `showerror`.

### Задание 4. Открытие файла
Кнопка «Открыть» предлагает выбрать файл `.txt` и выводит его содержимое в Text с полосой прокрутки. В заголовке окна должно отображаться только имя файла (`os.path.basename`).

## Продвинутый уровень

### Задание 5. Горячие клавиши и контекстное меню
Добавьте к заданию 4 меню: «Файл → Открыть (Ctrl+O), Сохранить (Ctrl+S), Выход (Ctrl+Q)». Добавьте к полю Text меню правой кнопки: «Копировать», «Вставить», «Вырезать», «Выделить всё».

### Задание 6. Настройки вида
Меню «Вид»:
- «Тёмная тема» — `add_checkbutton` (меняет фон Text и цвет текста);
- «Размер шрифта» — вложенное меню, `add_radiobutton`: 10, 12, 14, 18;
- «Перенос по словам» — `add_checkbutton` (`wrap="word"` / `"none"`).

### Задание 7. Список класса: сохранение и загрузка
Список учеников в Listbox (добавление/удаление через Entry и кнопки) сохраните кнопкой «Сохранить» в файл `.txt` (по одному ученику в строке), а кнопкой «Загрузить» — загрузите из файла обратно. Перед очисткой списка запрашивайте подтверждение через `askokcancel`.

## ★ Олимпиадная / творческая задача

### Задание 8. Полноценный «Блокнот»
На основе класса `Notepad` из теории добавьте:
1. «Правка → Найти...» (`simpledialog.askstring`) — найденные слова выделяются, их количество — в `showinfo`;
2. нижнюю строку состояния: «Строка: 3, Столбец: 15 | Слова: 120» (обновляется при перемещении курсора);
3. вложенное меню «Файл → Недавние файлы» (последние 5 открытых файлов);
4. «Выбор цвета» (`tkinter.colorchooser.askcolor`) — изменение цвета текста.

## Домашнее задание

1. Объединив задания 5–6, завершите своё приложение «Блокнот»; добавьте скриншот в README.
2. Загрузите на GitHub под именем `gui/notepad/notepad.py`.
3. Подумайте: какой виджет нужен, чтобы рисовать в программе линии, круги и перемещать их?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–2**
```python
import tkinter as tk
from tkinter import messagebox


def set_bg(color):
    root.config(bg=color)


def new():
    set_bg("white")


def about():
    messagebox.showinfo("О программе", "Пример меню, версия 1.0")


def exit_app():
    if messagebox.askyesno("Выход", "Выйти из программы?"):
        root.destroy()


root = tk.Tk()
root.title("Меню")
root.geometry("400x250")
menubar = tk.Menu(root)

file_menu = tk.Menu(menubar, tearoff=0)
file_menu.add_command(label="Новый", command=new)
file_menu.add_separator()
file_menu.add_command(label="Выход", command=exit_app)
menubar.add_cascade(label="Файл", menu=file_menu)

color_menu = tk.Menu(menubar, tearoff=0)
for name, color in [("Белый", "white"), ("Жёлтый", "lightyellow"), ("Голубой", "lightblue")]:
    color_menu.add_command(label=name, command=lambda c=color: set_bg(c))
menubar.add_cascade(label="Цвет", menu=color_menu)

help_menu = tk.Menu(menubar, tearoff=0)
help_menu.add_command(label="О программе", command=about)
menubar.add_cascade(label="Справка", menu=help_menu)

root.config(menu=menubar)
root.protocol("WM_DELETE_WINDOW", exit_app)
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk
from tkinter import messagebox


def check():
    try:
        g = int(entry.get())
    except ValueError:
        messagebox.showerror("Ошибка", "Введите оценку числом")
        return
    if g in (1, 2):
        messagebox.showwarning("Предупреждение", "Нужно лучше готовиться")
    elif g in (3, 4, 5):
        messagebox.showinfo("Результат", "Молодец!")
    else:
        messagebox.showerror("Ошибка", "Оценка должна быть от 1 до 5")


root = tk.Tk()
root.title("Оценка")
entry = tk.Entry(root, justify="center")
entry.pack(padx=20, pady=10)
tk.Button(root, text="Проверить", command=check).pack(pady=5)
root.mainloop()
```

**Задания 4–5**
```python
import os
import tkinter as tk
from tkinter import filedialog, messagebox
from tkinter.scrolledtext import ScrolledText

TYPES = [("Текстовые файлы", "*.txt"), ("Все файлы", "*.*")]


def open_file(event=None):
    path = filedialog.askopenfilename(filetypes=TYPES)
    if not path:
        return
    try:
        with open(path, encoding="utf-8") as f:
            content = f.read()
    except (OSError, UnicodeDecodeError) as e:
        messagebox.showerror("Ошибка", str(e))
        return
    text.delete("1.0", tk.END)
    text.insert("1.0", content)
    root.title(os.path.basename(path))


def save_file(event=None):
    path = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=TYPES)
    if path:
        with open(path, "w", encoding="utf-8") as f:
            f.write(text.get("1.0", "end-1c"))
        root.title(os.path.basename(path))


def select_all():
    text.tag_add("sel", "1.0", "end")


def popup(event):
    context.tk_popup(event.x_root, event.y_root)


root = tk.Tk()
root.title("Просмотрщик")
text = ScrolledText(root, width=70, height=20, wrap="word")
text.pack(fill="both", expand=True)

menubar = tk.Menu(root)
fm = tk.Menu(menubar, tearoff=0)
fm.add_command(label="Открыть", accelerator="Ctrl+O", command=open_file)
fm.add_command(label="Сохранить", accelerator="Ctrl+S", command=save_file)
fm.add_separator()
fm.add_command(label="Выход", accelerator="Ctrl+Q", command=root.destroy)
menubar.add_cascade(label="Файл", menu=fm)
root.config(menu=menubar)
root.bind("<Control-o>", open_file)
root.bind("<Control-s>", save_file)
root.bind("<Control-q>", lambda e: root.destroy())

context = tk.Menu(root, tearoff=0)
for label, ev in [("Копировать", "<<Copy>>"), ("Вставить", "<<Paste>>"), ("Вырезать", "<<Cut>>")]:
    context.add_command(label=label, command=lambda e=ev: text.event_generate(e))
context.add_separator()
context.add_command(label="Выделить всё", command=select_all)
text.bind("<Button-3>", popup)
root.mainloop()
```

**Задание 6**
```python
import tkinter as tk
from tkinter.scrolledtext import ScrolledText


def apply():
    if dark.get():
        text.config(bg="#1e1e2e", fg="#f5f5f5", insertbackground="white")
    else:
        text.config(bg="white", fg="black", insertbackground="black")
    text.config(font=("Consolas", size.get()), wrap="word" if wrap.get() else "none")


root = tk.Tk()
root.title("Вид")
text = ScrolledText(root, width=60, height=15)
text.pack(fill="both", expand=True)
dark = tk.BooleanVar(value=False)
size = tk.IntVar(value=12)
wrap = tk.BooleanVar(value=True)

menubar = tk.Menu(root)
view = tk.Menu(menubar, tearoff=0)
view.add_checkbutton(label="Тёмная тема", variable=dark, command=apply)
sizes = tk.Menu(view, tearoff=0)
for s in (10, 12, 14, 18):
    sizes.add_radiobutton(label=str(s), variable=size, value=s, command=apply)
view.add_cascade(label="Размер шрифта", menu=sizes)
view.add_checkbutton(label="Перенос по словам", variable=wrap, command=apply)
menubar.add_cascade(label="Вид", menu=view)
root.config(menu=menubar)
apply()
root.mainloop()
```

**Задание 7**
```python
import tkinter as tk
from tkinter import filedialog, messagebox

TYPES = [("Текстовые файлы", "*.txt")]


def add(event=None):
    name = entry.get().strip()
    if name:
        lb.insert(tk.END, name)
        entry.delete(0, tk.END)


def remove():
    sel = lb.curselection()
    if sel:
        lb.delete(sel[0])


def save():
    path = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=TYPES)
    if path:
        with open(path, "w", encoding="utf-8") as f:
            f.write("\n".join(lb.get(0, tk.END)))
        messagebox.showinfo("Сохранено", f"Сохранено учеников: {lb.size()}")


def load():
    if lb.size() and not messagebox.askokcancel("Загрузка", "Текущий список будет удалён. Продолжить?"):
        return
    path = filedialog.askopenfilename(filetypes=TYPES)
    if not path:
        return
    with open(path, encoding="utf-8") as f:
        names = [line.strip() for line in f if line.strip()]
    lb.delete(0, tk.END)
    lb.insert(tk.END, *names)


root = tk.Tk()
root.title("Список класса")
entry = tk.Entry(root, width=30)
entry.pack(padx=10, pady=5)
entry.bind("<Return>", add)
lb = tk.Listbox(root, width=30, height=10)
lb.pack(padx=10)
bar = tk.Frame(root)
bar.pack(pady=5)
for text, cmd in [("Добавить", add), ("Удалить", remove), ("Сохранить", save), ("Загрузить", load)]:
    tk.Button(bar, text=text, command=cmd).pack(side="left", padx=2)
root.mainloop()
```

**Задание 8** (методы, добавляемые к `Notepad` из теории; в `create_menu` добавляются соответствующие пункты, в `__init__` — строка состояния и `self.recent = []`)
```python
import tkinter as tk
from tkinter import simpledialog, messagebox, colorchooser


class NotepadExtras:
    """Дополнительные методы-примесь (mixin) для класса Notepad."""

    def find(self):
        word = simpledialog.askstring("Найти", "Искомое слово:", parent=self)
        if not word:
            return
        self.text.tag_remove("found", "1.0", tk.END)
        self.text.tag_config("found", background="yellow")
        start, count = "1.0", 0
        while True:
            pos = self.text.search(word, start, stopindex=tk.END, nocase=True)
            if not pos:
                break
            end = f"{pos}+{len(word)}c"
            self.text.tag_add("found", pos, end)
            start, count = end, count + 1
        messagebox.showinfo("Найти", f"Найдено: {count}")

    def update_status(self, event=None):
        line, col = self.text.index("insert").split(".")
        words = len(self.text.get("1.0", "end-1c").split())
        self.status.config(text=f"Строка: {line}, Столбец: {int(col) + 1} | Слова: {words}")

    def add_recent(self, path):
        if path in self.recent:
            self.recent.remove(path)
        self.recent.insert(0, path)
        self.recent = self.recent[:5]
        self.recent_menu.delete(0, tk.END)
        for p in self.recent:
            self.recent_menu.add_command(label=p, command=lambda p=p: self.open_path(p))

    def choose_color(self):
        color = colorchooser.askcolor(title="Цвет текста")[1]
        if color:
            self.text.config(fg=color)
```
Примечание: наследуемся как `class Notepad(NotepadExtras, tk.Tk)` и в `__init__` добавляем `self.status = tk.Label(self, anchor="w")`, `self.text.bind("<KeyRelease>", self.update_status)`, `self.text.bind("<ButtonRelease-1>", self.update_status)`; логика `open_file` выносится в метод `open_path(path)`, в конце которого вызывается `self.add_recent(path)`.

</details>
