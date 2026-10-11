# Урок 28. Меню (Menu) и диалоговые окна: messagebox, filedialog.

## Темы

1. Строка меню (Menu)
2. messagebox — окна сообщений
3. simpledialog — запрос значения
4. filedialog — окна выбора файла
5. Всё вместе: «Блокнот» (в стиле ООП)
6. Частые ошибки
7. Основные понятия

## 1. Строка меню (Menu)

Меню состоит из нескольких уровней:

```text
Строка меню (menubar):    Файл    Правка    Справка
                           │
        Вложенное меню:    ├─ Новый       Ctrl+N
                           ├─ Открыть...  Ctrl+O
                           ├─ Сохранить   Ctrl+S
                           ├────────────  (разделитель)
                           └─ Выход
```

```python
import tkinter as tk


def new_file():
    print("Новый файл")


def about():
    print("О программе")


root = tk.Tk()
root.title("Меню")
root.geometry("400x250")

menubar = tk.Menu(root)                       # 1. строка меню

file_menu = tk.Menu(menubar, tearoff=0)       # 2. вложенное меню
file_menu.add_command(label="Новый", command=new_file)
file_menu.add_command(label="Открыть...")
file_menu.add_separator()                     # линия-разделитель
file_menu.add_command(label="Выход", command=root.destroy)
menubar.add_cascade(label="Файл", menu=file_menu)   # 3. прикрепить к строке

help_menu = tk.Menu(menubar, tearoff=0)
help_menu.add_command(label="О программе", command=about)
menubar.add_cascade(label="Справка", menu=help_menu)

root.config(menu=menubar)                     # 4. установить в окно
root.mainloop()
```

`tearoff=0` — убирает пунктирную линию в начале меню (возможность «оторвать» меню в отдельное окно).

### Методы Menu

| Метод | Смысл |
|---|---|
| `add_command(label=, command=, accelerator=)` | Обычный пункт |
| `add_cascade(label=, menu=)` | Добавить вложенное меню |
| `add_separator()` | Линия-разделитель |
| `add_checkbutton(label=, variable=, command=)` | Пункт-флажок (✓) |
| `add_radiobutton(label=, variable=, value=, command=)` | Пункт-переключатель (•) |
| `entryconfig(index, state="disabled")` | Сделать пункт неактивным |

### Горячие клавиши (accelerator)

`accelerator` только **показывает текст** («Ctrl+S»), а чтобы клавиша работала, нужен `bind`:

```python
import tkinter as tk


def save(event=None):
    status.config(text="Сохранено!")


root = tk.Tk()
root.title("Горячие клавиши")
menubar = tk.Menu(root)
file_menu = tk.Menu(menubar, tearoff=0)
file_menu.add_command(label="Сохранить", accelerator="Ctrl+S", command=save)
menubar.add_cascade(label="Файл", menu=file_menu)
root.config(menu=menubar)
root.bind("<Control-s>", save)

status = tk.Label(root, text="Попробуйте нажать Ctrl+S", width=30)
status.pack(pady=40)
root.mainloop()
```

### Пункты-флажки и переключатели, многоуровневое меню

```python
import tkinter as tk


def apply_view():
    label.config(font=("Arial", size.get()))
    if show_status.get():
        status.pack(side="bottom", fill="x")
    else:
        status.pack_forget()                 # временно скрыть виджет


root = tk.Tk()
root.title("Вид")
root.geometry("400x250")
size = tk.IntVar(value=14)
show_status = tk.BooleanVar(value=True)

menubar = tk.Menu(root)
view_menu = tk.Menu(menubar, tearoff=0)
view_menu.add_checkbutton(label="Строка состояния", variable=show_status, command=apply_view)

font_menu = tk.Menu(view_menu, tearoff=0)    # вложенное меню внутри вложенного
for s in (10, 14, 20, 28):
    font_menu.add_radiobutton(label=f"{s} pt", variable=size, value=s, command=apply_view)
view_menu.add_cascade(label="Размер шрифта", menu=font_menu)
menubar.add_cascade(label="Вид", menu=view_menu)
root.config(menu=menubar)

label = tk.Label(root, text="Образец текста", font=("Arial", 14))
label.pack(expand=True)
status = tk.Label(root, text="Строка состояния", bg="#dfe6e9", anchor="w")
status.pack(side="bottom", fill="x")
root.mainloop()
```

### Контекстное меню (правая кнопка)

```python
import tkinter as tk


def show_menu(event):
    context.tk_popup(event.x_root, event.y_root)    # открыть там, где курсор


root = tk.Tk()
root.title("Контекстное меню")
text = tk.Text(root, width=40, height=10)
text.pack(padx=10, pady=10)

context = tk.Menu(root, tearoff=0)
context.add_command(label="Вырезать", command=lambda: text.event_generate("<<Cut>>"))
context.add_command(label="Копировать", command=lambda: text.event_generate("<<Copy>>"))
context.add_command(label="Вставить", command=lambda: text.event_generate("<<Paste>>"))
context.add_separator()
context.add_command(label="Очистить всё", command=lambda: text.delete("1.0", tk.END))

text.bind("<Button-3>", show_menu)       # в macOS: "<Button-2>"
root.mainloop()
```

`event_generate("<<Copy>>")` — вызывает встроенные действия копирования/вставки Text.

## 2. messagebox — окна сообщений

```python
from tkinter import messagebox
```

| Функция | Значок | Кнопки | Возвращает |
|---|---|---|---|
| `showinfo(title, message)` | ℹ | OK | `"ok"` |
| `showwarning(title, message)` | ⚠ | OK | `"ok"` |
| `showerror(title, message)` | ✖ | OK | `"ok"` |
| `askyesno(title, message)` | ? | Да / Нет | `True` / `False` |
| `askokcancel(title, message)` | ? | OK / Отмена | `True` / `False` |
| `askyesnocancel(title, message)` | ? | Да / Нет / Отмена | `True` / `False` / `None` |
| `askretrycancel(title, message)` | ⚠ | Повтор / Отмена | `True` / `False` |
| `askquestion(title, message)` | ? | Да / Нет | `"yes"` / `"no"` |

(Надписи на кнопках зависят от языка операционной системы.)

```python
import tkinter as tk
from tkinter import messagebox


def check_age():
    try:
        age = int(entry.get())
    except ValueError:
        messagebox.showerror("Ошибка", "Введите возраст числом!")
        return
    if age < 10 or age > 17:
        messagebox.showwarning("Внимание", "В кружок принимаются ученики 10–17 лет.")
    else:
        messagebox.showinfo("Успешно", "Вы можете записаться в кружок!")


def on_close():
    if messagebox.askyesno("Выход", "Вы действительно хотите выйти из программы?"):
        root.destroy()


root = tk.Tk()
root.title("messagebox")
tk.Label(root, text="Ваш возраст:").pack(pady=(10, 0))
entry = tk.Entry(root, justify="center")
entry.pack(padx=20, pady=5)
tk.Button(root, text="Проверить", command=check_age).pack(pady=5)
root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

**Важно:** пока диалоговое окно открыто, программа **останавливается** на этой строке и ждёт ответа пользователя (модальное окно).

## 3. simpledialog — запрос значения

```python
import tkinter as tk
from tkinter import simpledialog


def ask():
    name = simpledialog.askstring("Знакомство", "Как вас зовут?", parent=root)
    if name is None:                 # нажата «Отмена»
        return
    age = simpledialog.askinteger("Знакомство", "Ваш возраст?", minvalue=5, maxvalue=100, parent=root)
    label.config(text=f"{name}, {age} лет")


root = tk.Tk()
root.title("simpledialog")
label = tk.Label(root, text="", font=("Arial", 14))
label.pack(padx=40, pady=10)
tk.Button(root, text="Познакомиться", command=ask).pack(pady=5)
root.mainloop()
```

Доступные функции: `askstring`, `askinteger`, `askfloat`. Если нажата «Отмена» — `None`.

## 4. filedialog — окна выбора файла

```python
from tkinter import filedialog
```

| Функция | Смысл | Возвращает |
|---|---|---|
| `askopenfilename(...)` | Выбрать файл для открытия | Полный путь (строка) или `""` |
| `askopenfilenames(...)` | Выбрать несколько файлов | Кортеж путей |
| `asksaveasfilename(...)` | Выбрать имя/место для сохранения | Полный путь или `""` |
| `askdirectory(...)` | Выбрать папку | Путь к папке или `""` |

Основные параметры:
- `title` — заголовок окна;
- `filetypes` — фильтр: `[("Текстовые файлы", "*.txt"), ("Все файлы", "*.*")]`;
- `defaultextension` — расширение, добавляемое, если оно не указано: `".txt"`;
- `initialdir` — начальная папка; `initialfile` — начальное имя.

**Важно:** `filedialog` **не открывает** и **не сохраняет** файл — он только возвращает **путь**. С файлом мы работаем сами через `open()`.

```python
import tkinter as tk
from tkinter import filedialog, messagebox

FILETYPES = [("Текстовые файлы", "*.txt"), ("Все файлы", "*.*")]


def open_file():
    path = filedialog.askopenfilename(title="Открыть файл", filetypes=FILETYPES)
    if not path:                     # пользователь нажал «Отмена»
        return
    try:
        with open(path, encoding="utf-8") as f:
            content = f.read()
    except (OSError, UnicodeDecodeError) as e:
        messagebox.showerror("Ошибка", f"Не удалось прочитать файл:\n{e}")
        return
    text.delete("1.0", tk.END)
    text.insert("1.0", content)
    root.title(f"Блокнот — {path}")


def save_file():
    path = filedialog.asksaveasfilename(title="Сохранить", defaultextension=".txt",
                                        filetypes=FILETYPES)
    if not path:
        return
    with open(path, "w", encoding="utf-8") as f:
        f.write(text.get("1.0", "end-1c"))
    messagebox.showinfo("Сохранено", f"Файл сохранён:\n{path}")


root = tk.Tk()
root.title("Блокнот")
text = tk.Text(root, width=60, height=15, wrap="word")
text.pack(padx=5, pady=5, fill="both", expand=True)
bar = tk.Frame(root)
bar.pack()
tk.Button(bar, text="Открыть", command=open_file).pack(side="left", padx=5, pady=5)
tk.Button(bar, text="Сохранить", command=save_file).pack(side="left", padx=5, pady=5)
root.mainloop()
```

## 5. Всё вместе: «Блокнот» (в стиле ООП)

```python
import tkinter as tk
from tkinter import filedialog, messagebox
from tkinter.scrolledtext import ScrolledText

FILETYPES = [("Текстовые файлы", "*.txt"), ("Файлы Python", "*.py"),
             ("Все файлы", "*.*")]


class Notepad(tk.Tk):
    def __init__(self):
        super().__init__()
        self.path = None
        self.geometry("700x450")
        self.text = ScrolledText(self, wrap="word", undo=True, font=("Consolas", 12))
        self.text.pack(fill="both", expand=True)
        self.text.edit_modified(False)
        self.create_menu()
        self.bind_keys()
        self.protocol("WM_DELETE_WINDOW", self.exit_app)
        self.update_title()

    # ---------- интерфейс ----------
    def create_menu(self):
        menubar = tk.Menu(self)
        file_menu = tk.Menu(menubar, tearoff=0)
        file_menu.add_command(label="Новый", accelerator="Ctrl+N", command=self.new_file)
        file_menu.add_command(label="Открыть...", accelerator="Ctrl+O", command=self.open_file)
        file_menu.add_command(label="Сохранить", accelerator="Ctrl+S", command=self.save_file)
        file_menu.add_command(label="Сохранить как...", command=self.save_as)
        file_menu.add_separator()
        file_menu.add_command(label="Выход", command=self.exit_app)
        menubar.add_cascade(label="Файл", menu=file_menu)

        edit_menu = tk.Menu(menubar, tearoff=0)
        edit_menu.add_command(label="Отменить", accelerator="Ctrl+Z",
                              command=lambda: self.text.event_generate("<<Undo>>"))
        edit_menu.add_separator()
        edit_menu.add_command(label="Выделить всё", accelerator="Ctrl+A",
                              command=lambda: self.text.tag_add("sel", "1.0", "end"))
        menubar.add_cascade(label="Правка", menu=edit_menu)

        help_menu = tk.Menu(menubar, tearoff=0)
        help_menu.add_command(label="О программе", command=self.about)
        menubar.add_cascade(label="Справка", menu=help_menu)
        self.config(menu=menubar)

    def bind_keys(self):
        self.bind("<Control-n>", lambda e: self.new_file())
        self.bind("<Control-o>", lambda e: self.open_file())
        self.bind("<Control-s>", lambda e: self.save_file())

    def update_title(self):
        name = self.path if self.path else "Без имени"
        self.title(f"Блокнот — {name}")

    # ---------- действия ----------
    def confirm_discard(self):
        """Если есть несохранённые изменения, спрашивает пользователя."""
        if not self.text.edit_modified():
            return True
        answer = messagebox.askyesnocancel("Сохранение", "Сохранить изменения?")
        if answer is None:
            return False
        if answer:
            return self.save_file()
        return True

    def new_file(self):
        if self.confirm_discard():
            self.text.delete("1.0", tk.END)
            self.path = None
            self.text.edit_modified(False)
            self.update_title()

    def open_file(self):
        if not self.confirm_discard():
            return
        path = filedialog.askopenfilename(filetypes=FILETYPES)
        if not path:
            return
        try:
            with open(path, encoding="utf-8") as f:
                content = f.read()
        except (OSError, UnicodeDecodeError) as e:
            messagebox.showerror("Ошибка", str(e))
            return
        self.text.delete("1.0", tk.END)
        self.text.insert("1.0", content)
        self.path = path
        self.text.edit_modified(False)
        self.update_title()

    def save_file(self):
        if self.path is None:
            return self.save_as()
        with open(self.path, "w", encoding="utf-8") as f:
            f.write(self.text.get("1.0", "end-1c"))
        self.text.edit_modified(False)
        return True

    def save_as(self):
        path = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=FILETYPES)
        if not path:
            return False
        self.path = path
        self.update_title()
        return self.save_file()

    def about(self):
        messagebox.showinfo("О программе",
                            "Блокнот 1.0\nКружок «Python класс Программирования»")

    def exit_app(self):
        if self.confirm_discard():
            self.destroy()


if __name__ == "__main__":
    Notepad().mainloop()
```

`text.edit_modified()` — показывает, изменился ли текст в Text с того момента, когда он в последний раз был отмечен как «сохранённый».

## Частые ошибки

**1. Забыли `root.config(menu=menubar)`** — меню не видно.

**2. Забыли `add_cascade`** — вложенное меню создано, но не прикреплено к строке.

**3. Написали `accelerator`, но не сделали `bind`** — в меню видно «Ctrl+S», но клавиша не работает.

**4. Отсутствие параметра `event` в функции, вызываемой через `bind`** — `lambda e: self.save_file()` или `def save(event=None)`.

**5. Не проверять пустой результат диалога**

```python
from tkinter import filedialog
# path = filedialog.askopenfilename()
path = ""                                 # случай, когда пользователь нажал «Отмена»
if not path:
    print("Файл не выбран")
else:
    with open(path, encoding="utf-8") as f:
        print(f.read())
```

`open("")` → `FileNotFoundError`. Всегда `if not path: return`.

**6. Не указывать `encoding="utf-8"`** — казахские и русские буквы могут превратиться в «кракозябры» (особенно в Windows).

**7. Сравнение результата `askquestion` с `True`** — он возвращает строку `"yes"`/`"no"`. `askyesno` — `True`/`False`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `Menu` | Строка меню или вложенное меню |
| `add_cascade` | Прикрепление вложенного меню к верхнему уровню |
| `accelerator` | Текст горячей клавиши, отображаемый в пункте меню |
| Контекстное меню | Меню, открываемое правой кнопкой (`tk_popup`) |
| Модальное окно | Диалог, останавливающий выполнение программы до его закрытия |
| `messagebox` | Окна информации, предупреждения, ошибки, вопроса |
| `filedialog` | Окна выбора файла/папки (возвращают только путь) |
| `simpledialog` | Простые диалоги, запрашивающие строку/число |

## Примечание

- В macOS строка меню отображается в верхней панели экрана, а не в окне; вместо `Ctrl` там используется `Command` (`<Command-s>`).
- `bind("<Control-s>")` чувствителен к регистру: при включённом Caps Lock приходит `<Control-S>`, поэтому полезно привязать оба варианта.
- Диалогам стоит передавать `parent=root` (или нужное окно), чтобы они появлялись поверх своего окна, а не где-то на экране.
- `filedialog.askopenfile()` (без `name`) возвращает уже открытый файловый объект, а не путь — его нужно закрыть; обычно удобнее `askopenfilename()` + `with open(...)`.
- Для работы с путями удобен `pathlib`: `Path(path).name` — имя файла для заголовка окна, `Path(path).suffix` — расширение.
- Документация: https://docs.python.org/3/library/tkinter.messagebox.html и https://docs.python.org/3/library/dialog.html
