# Сабақ 28. Практикалық тапсырмалар: Menu, messagebox, filedialog

## Базалық деңгей

### 1-тапсырма. Мәзірі бар терезе
«Файл» (Жаңа, Шығу), «Түс» (Ақ, Сары, Көгілдір — терезе фонын өзгертеді), «Анықтама» (Бағдарлама туралы — `showinfo`) мәзірлері бар терезе жасаңыз.

### 2-тапсырма. Растаумен шығу
Терезенің «✕» батырмасы мен «Файл → Шығу» пункті «Бағдарламадан шығасыз ба?» (`askyesno`) деп сұрасын. «Иә» болса ғана жабылсын.

### 3-тапсырма. Үш түрлі хабарлама
Entry-ге баға енгізіледі (1–5). «Тексеру» батырмасы: сан емес — `showerror`; 1–2 — `showwarning` («Көбірек дайындалу керек»); 3–5 — `showinfo` («Жарайсыз!»); диапазоннан тыс — `showerror`.

### 4-тапсырма. Файлды ашу
«Ашу» батырмасы `.txt` файлын таңдатып, оның мазмұнын айналдыру жолағы бар Text-ке шығарсын. Терезе тақырыбында файлдың тек атауы көрінсін (`os.path.basename`).

## Кеңейтілген деңгей

### 5-тапсырма. Ыстық пернелер және контекстік мәзір
4-тапсырмаға мәзір қосыңыз: «Файл → Ашу (Ctrl+O), Сақтау (Ctrl+S), Шығу (Ctrl+Q)». Text өрісіне оң батырма мәзірін қосыңыз: «Көшіру», «Қою», «Қиып алу», «Барлығын белгілеу».

### 6-тапсырма. Көрініс баптаулары
«Көрініс» мәзірі:
- «Қараңғы тақырып» — `add_checkbutton` (Text фоны мен мәтін түсін ауыстырады);
- «Шрифт өлшемі» — ішкі мәзір, `add_radiobutton`: 10, 12, 14, 18;
- «Сөз бойынша тасымалдау» — `add_checkbutton` (`wrap="word"` / `"none"`).

### 7-тапсырма. Сыныптың тізімі: сақтау және жүктеу
Listbox-тағы оқушылар тізімін (қосу/өшіру Entry және батырмалармен) «Сақтау» арқылы `.txt` файлына (әр жолға бір оқушы), «Жүктеу» арқылы файлдан қайта жүктеңіз. Тізімді тазартар алдында `askokcancel` арқылы растау сұралсын.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Толық «Блокнот»
Теориядағы `Notepad` класын негізге алып, қосыңыз:
1. «Түзету → Табу...» (`simpledialog.askstring`) — табылған сөздер бөлектеледі, саны `showinfo`-да;
2. төменгі күй жолағы: «Жол: 3, Баған: 15 | Сөздер: 120» (курсор жылжығанда жаңарады);
3. «Файл → Соңғы файлдар» ішкі мәзірі (соңғы 5 ашылған файл);
4. «Түс таңдау» (`tkinter.colorchooser.askcolor`) — мәтін түсін өзгерту.

## Үй тапсырмасы

1. 5–6-тапсырмаларды біріктіріп, өзіңіздің «Блокнот» қосымшаңызды аяқтаңыз; README-ге скриншот қосыңыз.
2. GitHub-қа `gui/notepad/notepad.py` деп жүктеңіз.
3. Ойланыңыз: бағдарламада сызықтар, шеңберлер салу және оларды жылжыту үшін қандай виджет керек?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–2-тапсырмалар**
```python
import tkinter as tk
from tkinter import messagebox


def set_bg(color):
    root.config(bg=color)


def new():
    set_bg("white")


def about():
    messagebox.showinfo("Бағдарлама туралы", "Мәзір мысалы, 1.0 нұсқа")


def exit_app():
    if messagebox.askyesno("Шығу", "Бағдарламадан шығасыз ба?"):
        root.destroy()


root = tk.Tk()
root.title("Мәзір")
root.geometry("400x250")
menubar = tk.Menu(root)

file_menu = tk.Menu(menubar, tearoff=0)
file_menu.add_command(label="Жаңа", command=new)
file_menu.add_separator()
file_menu.add_command(label="Шығу", command=exit_app)
menubar.add_cascade(label="Файл", menu=file_menu)

color_menu = tk.Menu(menubar, tearoff=0)
for name, color in [("Ақ", "white"), ("Сары", "lightyellow"), ("Көгілдір", "lightblue")]:
    color_menu.add_command(label=name, command=lambda c=color: set_bg(c))
menubar.add_cascade(label="Түс", menu=color_menu)

help_menu = tk.Menu(menubar, tearoff=0)
help_menu.add_command(label="Бағдарлама туралы", command=about)
menubar.add_cascade(label="Анықтама", menu=help_menu)

root.config(menu=menubar)
root.protocol("WM_DELETE_WINDOW", exit_app)
root.mainloop()
```

**3-тапсырма**
```python
import tkinter as tk
from tkinter import messagebox


def check():
    try:
        g = int(entry.get())
    except ValueError:
        messagebox.showerror("Қате", "Бағаны сан түрінде енгізіңіз")
        return
    if g in (1, 2):
        messagebox.showwarning("Ескерту", "Көбірек дайындалу керек")
    elif g in (3, 4, 5):
        messagebox.showinfo("Нәтиже", "Жарайсыз!")
    else:
        messagebox.showerror("Қате", "Баға 1-ден 5-ке дейін болуы керек")


root = tk.Tk()
root.title("Баға")
entry = tk.Entry(root, justify="center")
entry.pack(padx=20, pady=10)
tk.Button(root, text="Тексеру", command=check).pack(pady=5)
root.mainloop()
```

**4–5-тапсырмалар**
```python
import os
import tkinter as tk
from tkinter import filedialog, messagebox
from tkinter.scrolledtext import ScrolledText

TYPES = [("Мәтіндік файлдар", "*.txt"), ("Барлық файлдар", "*.*")]


def open_file(event=None):
    path = filedialog.askopenfilename(filetypes=TYPES)
    if not path:
        return
    try:
        with open(path, encoding="utf-8") as f:
            content = f.read()
    except (OSError, UnicodeDecodeError) as e:
        messagebox.showerror("Қате", str(e))
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
root.title("Көрермен")
text = ScrolledText(root, width=70, height=20, wrap="word")
text.pack(fill="both", expand=True)

menubar = tk.Menu(root)
fm = tk.Menu(menubar, tearoff=0)
fm.add_command(label="Ашу", accelerator="Ctrl+O", command=open_file)
fm.add_command(label="Сақтау", accelerator="Ctrl+S", command=save_file)
fm.add_separator()
fm.add_command(label="Шығу", accelerator="Ctrl+Q", command=root.destroy)
menubar.add_cascade(label="Файл", menu=fm)
root.config(menu=menubar)
root.bind("<Control-o>", open_file)
root.bind("<Control-s>", save_file)
root.bind("<Control-q>", lambda e: root.destroy())

context = tk.Menu(root, tearoff=0)
for label, ev in [("Көшіру", "<<Copy>>"), ("Қою", "<<Paste>>"), ("Қиып алу", "<<Cut>>")]:
    context.add_command(label=label, command=lambda e=ev: text.event_generate(e))
context.add_separator()
context.add_command(label="Барлығын белгілеу", command=select_all)
text.bind("<Button-3>", popup)
root.mainloop()
```

**6-тапсырма**
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
root.title("Көрініс")
text = ScrolledText(root, width=60, height=15)
text.pack(fill="both", expand=True)
dark = tk.BooleanVar(value=False)
size = tk.IntVar(value=12)
wrap = tk.BooleanVar(value=True)

menubar = tk.Menu(root)
view = tk.Menu(menubar, tearoff=0)
view.add_checkbutton(label="Қараңғы тақырып", variable=dark, command=apply)
sizes = tk.Menu(view, tearoff=0)
for s in (10, 12, 14, 18):
    sizes.add_radiobutton(label=str(s), variable=size, value=s, command=apply)
view.add_cascade(label="Шрифт өлшемі", menu=sizes)
view.add_checkbutton(label="Сөз бойынша тасымалдау", variable=wrap, command=apply)
menubar.add_cascade(label="Көрініс", menu=view)
root.config(menu=menubar)
apply()
root.mainloop()
```

**7-тапсырма**
```python
import tkinter as tk
from tkinter import filedialog, messagebox

TYPES = [("Мәтіндік файлдар", "*.txt")]


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
        messagebox.showinfo("Сақталды", f"{lb.size()} оқушы сақталды")


def load():
    if lb.size() and not messagebox.askokcancel("Жүктеу", "Ағымдағы тізім өшеді. Жалғастырасыз ба?"):
        return
    path = filedialog.askopenfilename(filetypes=TYPES)
    if not path:
        return
    with open(path, encoding="utf-8") as f:
        names = [line.strip() for line in f if line.strip()]
    lb.delete(0, tk.END)
    lb.insert(tk.END, *names)


root = tk.Tk()
root.title("Сынып тізімі")
entry = tk.Entry(root, width=30)
entry.pack(padx=10, pady=5)
entry.bind("<Return>", add)
lb = tk.Listbox(root, width=30, height=10)
lb.pack(padx=10)
bar = tk.Frame(root)
bar.pack(pady=5)
for text, cmd in [("Қосу", add), ("Өшіру", remove), ("Сақтау", save), ("Жүктеу", load)]:
    tk.Button(bar, text=text, command=cmd).pack(side="left", padx=2)
root.mainloop()
```

**8-тапсырма** (теориядағы `Notepad`-қа қосылатын әдістер; `create_menu` ішіне тиісті пункттер, `__init__` ішіне күй жолағы мен `self.recent = []` қосылады)
```python
import tkinter as tk
from tkinter import simpledialog, messagebox, colorchooser


class NotepadExtras:
    """Notepad класына араласатын (mixin) қосымша әдістер."""

    def find(self):
        word = simpledialog.askstring("Табу", "Ізделетін сөз:", parent=self)
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
        messagebox.showinfo("Табу", f"Табылды: {count}")

    def update_status(self, event=None):
        line, col = self.text.index("insert").split(".")
        words = len(self.text.get("1.0", "end-1c").split())
        self.status.config(text=f"Жол: {line}, Баған: {int(col) + 1} | Сөздер: {words}")

    def add_recent(self, path):
        if path in self.recent:
            self.recent.remove(path)
        self.recent.insert(0, path)
        self.recent = self.recent[:5]
        self.recent_menu.delete(0, tk.END)
        for p in self.recent:
            self.recent_menu.add_command(label=p, command=lambda p=p: self.open_path(p))

    def choose_color(self):
        color = colorchooser.askcolor(title="Мәтін түсі")[1]
        if color:
            self.text.config(fg=color)
```
Ескерту: `class Notepad(NotepadExtras, tk.Tk)` деп мұра алып, `__init__`-те `self.status = tk.Label(self, anchor="w")`, `self.text.bind("<KeyRelease>", self.update_status)`, `self.text.bind("<ButtonRelease-1>", self.update_status)` қосылады; `open_file` логикасы `open_path(path)` әдісіне бөлінеді және соңында `self.add_recent(path)` шақырылады.

</details>
