# Сабақ 28. Мәзір (Menu) және диалог терезелері: messagebox, filedialog.

## Тақырыптар

1. Мәзір жолағы (Menu)
2. messagebox — хабарлама терезелері
3. simpledialog — мән сұрау
4. filedialog — файлды таңдау терезелері
5. Барлығы бірге: «Блокнот» (ОБП стилінде)
6. Жиі кездесетін қателер
7. Негізгі ұғымдар

## 1. Мәзір жолағы (Menu)

Мәзір — бірнеше деңгейден тұрады:

```text
Мәзір жолағы (menubar):   Файл    Түзету    Анықтама
                           │
        Ішкі мәзір:        ├─ Жаңа        Ctrl+N
                           ├─ Ашу...      Ctrl+O
                           ├─ Сақтау      Ctrl+S
                           ├────────────  (бөлгіш)
                           └─ Шығу
```

```python
import tkinter as tk


def new_file():
    print("Жаңа файл")


def about():
    print("Бағдарлама туралы")


root = tk.Tk()
root.title("Мәзір")
root.geometry("400x250")

menubar = tk.Menu(root)                       # 1. мәзір жолағы

file_menu = tk.Menu(menubar, tearoff=0)       # 2. ішкі мәзір
file_menu.add_command(label="Жаңа", command=new_file)
file_menu.add_command(label="Ашу...")
file_menu.add_separator()                     # бөлгіш сызық
file_menu.add_command(label="Шығу", command=root.destroy)
menubar.add_cascade(label="Файл", menu=file_menu)   # 3. жолаққа іліп қою

help_menu = tk.Menu(menubar, tearoff=0)
help_menu.add_command(label="Бағдарлама туралы", command=about)
menubar.add_cascade(label="Анықтама", menu=help_menu)

root.config(menu=menubar)                     # 4. терезеге орнату
root.mainloop()
```

`tearoff=0` — мәзірдің басындағы үзік сызықты (мәзірді бөлек терезеге «жұлып алу» мүмкіндігін) өшіреді.

### Menu әдістері

| Әдіс | Мағынасы |
|---|---|
| `add_command(label=, command=, accelerator=)` | Кәдімгі пункт |
| `add_cascade(label=, menu=)` | Ішкі мәзірді қосу |
| `add_separator()` | Бөлгіш сызық |
| `add_checkbutton(label=, variable=, command=)` | Жалаушалы пункт (✓) |
| `add_radiobutton(label=, variable=, value=, command=)` | Ауыстырғышты пункт (•) |
| `entryconfig(index, state="disabled")` | Пунктті белсенді емес ету |

### Ыстық пернелер (accelerator)

`accelerator` тек **мәтінді** көрсетеді («Ctrl+S»), ал перне жұмыс істеуі үшін `bind` керек:

```python
import tkinter as tk


def save(event=None):
    status.config(text="Сақталды!")


root = tk.Tk()
root.title("Ыстық пернелер")
menubar = tk.Menu(root)
file_menu = tk.Menu(menubar, tearoff=0)
file_menu.add_command(label="Сақтау", accelerator="Ctrl+S", command=save)
menubar.add_cascade(label="Файл", menu=file_menu)
root.config(menu=menubar)
root.bind("<Control-s>", save)

status = tk.Label(root, text="Ctrl+S басып көріңіз", width=30)
status.pack(pady=40)
root.mainloop()
```

### Жалаушалы және ауыстырғышты пункттер, көп деңгейлі мәзір

```python
import tkinter as tk


def apply_view():
    label.config(font=("Arial", size.get()))
    if show_status.get():
        status.pack(side="bottom", fill="x")
    else:
        status.pack_forget()                 # виджетті уақытша жасыру


root = tk.Tk()
root.title("Көрініс")
root.geometry("400x250")
size = tk.IntVar(value=14)
show_status = tk.BooleanVar(value=True)

menubar = tk.Menu(root)
view_menu = tk.Menu(menubar, tearoff=0)
view_menu.add_checkbutton(label="Күй жолағы", variable=show_status, command=apply_view)

font_menu = tk.Menu(view_menu, tearoff=0)    # ішкі мәзірдің ішкі мәзірі
for s in (10, 14, 20, 28):
    font_menu.add_radiobutton(label=f"{s} pt", variable=size, value=s, command=apply_view)
view_menu.add_cascade(label="Шрифт өлшемі", menu=font_menu)
menubar.add_cascade(label="Көрініс", menu=view_menu)
root.config(menu=menubar)

label = tk.Label(root, text="Мәтін үлгісі", font=("Arial", 14))
label.pack(expand=True)
status = tk.Label(root, text="Күй жолағы", bg="#dfe6e9", anchor="w")
status.pack(side="bottom", fill="x")
root.mainloop()
```

### Контекстік мәзір (оң батырма)

```python
import tkinter as tk


def show_menu(event):
    context.tk_popup(event.x_root, event.y_root)    # курсор тұрған жерде ашу


root = tk.Tk()
root.title("Контекстік мәзір")
text = tk.Text(root, width=40, height=10)
text.pack(padx=10, pady=10)

context = tk.Menu(root, tearoff=0)
context.add_command(label="Қиып алу", command=lambda: text.event_generate("<<Cut>>"))
context.add_command(label="Көшіру", command=lambda: text.event_generate("<<Copy>>"))
context.add_command(label="Қою", command=lambda: text.event_generate("<<Paste>>"))
context.add_separator()
context.add_command(label="Барлығын тазарту", command=lambda: text.delete("1.0", tk.END))

text.bind("<Button-3>", show_menu)       # macOS-та: "<Button-2>"
root.mainloop()
```

`event_generate("<<Copy>>")` — Text-тің кірістірілген көшіру/қою әрекеттерін шақырады.

## 2. messagebox — хабарлама терезелері

```python
from tkinter import messagebox
```

| Функция | Белгіше | Батырмалар | Қайтаратыны |
|---|---|---|---|
| `showinfo(title, message)` | ℹ | OK | `"ok"` |
| `showwarning(title, message)` | ⚠ | OK | `"ok"` |
| `showerror(title, message)` | ✖ | OK | `"ok"` |
| `askyesno(title, message)` | ? | Иә / Жоқ | `True` / `False` |
| `askokcancel(title, message)` | ? | OK / Болдырмау | `True` / `False` |
| `askyesnocancel(title, message)` | ? | Иә / Жоқ / Болдырмау | `True` / `False` / `None` |
| `askretrycancel(title, message)` | ⚠ | Қайталау / Болдырмау | `True` / `False` |
| `askquestion(title, message)` | ? | Иә / Жоқ | `"yes"` / `"no"` |

(Батырмалар жазуы операциялық жүйенің тіліне байланысты.)

```python
import tkinter as tk
from tkinter import messagebox


def check_age():
    try:
        age = int(entry.get())
    except ValueError:
        messagebox.showerror("Қате", "Жасыңызды сан түрінде енгізіңіз!")
        return
    if age < 10 or age > 17:
        messagebox.showwarning("Назар аударыңыз", "Үйірмеге 10–17 жастағы оқушылар қабылданады.")
    else:
        messagebox.showinfo("Сәтті", "Сіз үйірмеге тіркеле аласыз!")


def on_close():
    if messagebox.askyesno("Шығу", "Бағдарламадан шынымен шығасыз ба?"):
        root.destroy()


root = tk.Tk()
root.title("messagebox")
tk.Label(root, text="Жасыңыз:").pack(pady=(10, 0))
entry = tk.Entry(root, justify="center")
entry.pack(padx=20, pady=5)
tk.Button(root, text="Тексеру", command=check_age).pack(pady=5)
root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

**Маңызды:** диалог терезесі ашық тұрғанда бағдарлама сол жолда **тоқтап**, пайдаланушының жауабын күтеді (модальді терезе).

## 3. simpledialog — мән сұрау

```python
import tkinter as tk
from tkinter import simpledialog


def ask():
    name = simpledialog.askstring("Танысу", "Атыңыз кім?", parent=root)
    if name is None:                 # «Болдырмау» басылды
        return
    age = simpledialog.askinteger("Танысу", "Жасыңыз?", minvalue=5, maxvalue=100, parent=root)
    label.config(text=f"{name}, {age} жас")


root = tk.Tk()
root.title("simpledialog")
label = tk.Label(root, text="", font=("Arial", 14))
label.pack(padx=40, pady=10)
tk.Button(root, text="Танысу", command=ask).pack(pady=5)
root.mainloop()
```

Бар функциялар: `askstring`, `askinteger`, `askfloat`. «Болдырмау» басылса — `None`.

## 4. filedialog — файлды таңдау терезелері

```python
from tkinter import filedialog
```

| Функция | Мағынасы | Қайтаратыны |
|---|---|---|
| `askopenfilename(...)` | Ашу үшін файл таңдау | Толық жол (жол) немесе `""` |
| `askopenfilenames(...)` | Бірнеше файл таңдау | Жолдар кортежі |
| `asksaveasfilename(...)` | Сақтау үшін атау/орын таңдау | Толық жол немесе `""` |
| `askdirectory(...)` | Папка таңдау | Папка жолы немесе `""` |

Негізгі параметрлер:
- `title` — терезе тақырыбы;
- `filetypes` — сүзгі: `[("Мәтіндік файлдар", "*.txt"), ("Барлық файлдар", "*.*")]`;
- `defaultextension` — кеңейтімі жазылмаса қосылатын: `".txt"`;
- `initialdir` — бастапқы папка; `initialfile` — бастапқы атау.

**Маңызды:** `filedialog` файлды **ашпайды** және **сақтамайды** — ол тек **жолды** қайтарады. Файлмен жұмысты біз өзіміз `open()` арқылы жасаймыз.

```python
import tkinter as tk
from tkinter import filedialog, messagebox

FILETYPES = [("Мәтіндік файлдар", "*.txt"), ("Барлық файлдар", "*.*")]


def open_file():
    path = filedialog.askopenfilename(title="Файлды ашу", filetypes=FILETYPES)
    if not path:                     # пайдаланушы «Болдырмау» басты
        return
    try:
        with open(path, encoding="utf-8") as f:
            content = f.read()
    except (OSError, UnicodeDecodeError) as e:
        messagebox.showerror("Қате", f"Файлды оқу мүмкін болмады:\n{e}")
        return
    text.delete("1.0", tk.END)
    text.insert("1.0", content)
    root.title(f"Блокнот — {path}")


def save_file():
    path = filedialog.asksaveasfilename(title="Сақтау", defaultextension=".txt",
                                        filetypes=FILETYPES)
    if not path:
        return
    with open(path, "w", encoding="utf-8") as f:
        f.write(text.get("1.0", "end-1c"))
    messagebox.showinfo("Сақталды", f"Файл сақталды:\n{path}")


root = tk.Tk()
root.title("Блокнот")
text = tk.Text(root, width=60, height=15, wrap="word")
text.pack(padx=5, pady=5, fill="both", expand=True)
bar = tk.Frame(root)
bar.pack()
tk.Button(bar, text="Ашу", command=open_file).pack(side="left", padx=5, pady=5)
tk.Button(bar, text="Сақтау", command=save_file).pack(side="left", padx=5, pady=5)
root.mainloop()
```

## 5. Барлығы бірге: «Блокнот» (ОБП стилінде)

```python
import tkinter as tk
from tkinter import filedialog, messagebox
from tkinter.scrolledtext import ScrolledText

FILETYPES = [("Мәтіндік файлдар", "*.txt"), ("Python файлдары", "*.py"),
             ("Барлық файлдар", "*.*")]


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
        file_menu.add_command(label="Жаңа", accelerator="Ctrl+N", command=self.new_file)
        file_menu.add_command(label="Ашу...", accelerator="Ctrl+O", command=self.open_file)
        file_menu.add_command(label="Сақтау", accelerator="Ctrl+S", command=self.save_file)
        file_menu.add_command(label="Басқаша сақтау...", command=self.save_as)
        file_menu.add_separator()
        file_menu.add_command(label="Шығу", command=self.exit_app)
        menubar.add_cascade(label="Файл", menu=file_menu)

        edit_menu = tk.Menu(menubar, tearoff=0)
        edit_menu.add_command(label="Болдырмау", accelerator="Ctrl+Z",
                              command=lambda: self.text.event_generate("<<Undo>>"))
        edit_menu.add_separator()
        edit_menu.add_command(label="Барлығын белгілеу", accelerator="Ctrl+A",
                              command=lambda: self.text.tag_add("sel", "1.0", "end"))
        menubar.add_cascade(label="Түзету", menu=edit_menu)

        help_menu = tk.Menu(menubar, tearoff=0)
        help_menu.add_command(label="Бағдарлама туралы", command=self.about)
        menubar.add_cascade(label="Анықтама", menu=help_menu)
        self.config(menu=menubar)

    def bind_keys(self):
        self.bind("<Control-n>", lambda e: self.new_file())
        self.bind("<Control-o>", lambda e: self.open_file())
        self.bind("<Control-s>", lambda e: self.save_file())

    def update_title(self):
        name = self.path if self.path else "Атаусыз"
        self.title(f"Блокнот — {name}")

    # ---------- әрекеттер ----------
    def confirm_discard(self):
        """Сақталмаған өзгерістер болса, пайдаланушыдан сұрайды."""
        if not self.text.edit_modified():
            return True
        answer = messagebox.askyesnocancel("Сақтау", "Өзгерістерді сақтайсыз ба?")
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
            messagebox.showerror("Қате", str(e))
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
        messagebox.showinfo("Бағдарлама туралы",
                            "Блокнот 1.0\n«Python класс Программирования» үйірмесі")

    def exit_app(self):
        if self.confirm_discard():
            self.destroy()


if __name__ == "__main__":
    Notepad().mainloop()
```

`text.edit_modified()` — Text-тегі мәтін соңғы рет «сақталды» деп белгіленгеннен кейін өзгерді ме, соны көрсетеді.

## Жиі кездесетін қателер

**1. `root.config(menu=menubar)` ұмыту** — мәзір көрінбейді.

**2. `add_cascade`-ті ұмыту** — ішкі мәзір құрылды, бірақ жолаққа ілінбеді.

**3. `accelerator` жазып, `bind` жасамау** — мәзірде «Ctrl+S» көрінеді, бірақ перне жұмыс істемейді.

**4. `bind` арқылы шақырылатын функцияда `event` параметрінің болмауы** — `lambda e: self.save_file()` немесе `def save(event=None)`.

**5. Диалогтың бос нәтижесін тексермеу**

```python
from tkinter import filedialog
# path = filedialog.askopenfilename()
path = ""                                 # пайдаланушы «Болдырмау» басқан жағдай
if not path:
    print("Файл таңдалмады")
else:
    with open(path, encoding="utf-8") as f:
        print(f.read())
```

`open("")` → `FileNotFoundError`. Әрқашан `if not path: return`.

**6. `encoding="utf-8"` көрсетпеу** — қазақ әріптері «кракозябр» болып кетуі мүмкін (әсіресе Windows-та).

**7. `askquestion` нәтижесін `True`-мен салыстыру** — ол `"yes"`/`"no"` жолын қайтарады. `askyesno` — `True`/`False`.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| `Menu` | Мәзір жолағы немесе ішкі мәзір |
| `add_cascade` | Ішкі мәзірді жоғарғы деңгейге іліп қою |
| `accelerator` | Мәзір пунктінде көрсетілетін ыстық перне мәтіні |
| Контекстік мәзір | Оң батырмамен ашылатын мәзір (`tk_popup`) |
| Модальді терезе | Жабылмайынша бағдарламаның жалғасуын тоқтататын диалог |
| `messagebox` | Ақпарат, ескерту, қате, сұрақ терезелері |
| `filedialog` | Файл/папка таңдау терезелері (тек жолды қайтарады) |
| `simpledialog` | Жол/сан сұрайтын қарапайым диалогтар |

## Ескерту

- macOS-та мәзір жолағы терезеде емес, экранның жоғарғы панелінде көрсетіледі; онда `Ctrl` орнына `Command` қолданылады (`<Command-s>`).
- `bind("<Control-s>")` регистрге сезімтал: Caps Lock қосулы болса, `<Control-S>` келеді, сондықтан екі нұсқаны да байланыстырған пайдалы.
- Диалогтарға `parent=root` (немесе керекті терезені) берген дұрыс, сонда олар экранның кез келген жерінде емес, өз терезесінің үстінде шығады.
- `filedialog.askopenfile()` (`name`-сіз) жолды емес, ашық файл объектісін қайтарады — оны жабу керек; әдетте `askopenfilename()` + `with open(...)` ыңғайлырақ.
- Жолдармен жұмыс үшін `pathlib` ыңғайлы: `Path(path).name` — терезе тақырыбына арналған файл аты, `Path(path).suffix` — кеңейтімі.
- Құжаттама: https://docs.python.org/3/library/tkinter.messagebox.html және https://docs.python.org/3/library/dialog.html
