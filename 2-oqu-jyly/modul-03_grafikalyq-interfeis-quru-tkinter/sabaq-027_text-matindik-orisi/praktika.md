# Сабақ 27. Практикалық тапсырмалар: Text және Scrollbar

## Базалық деңгей

### 1-тапсырма. Күнделік
Айналдыру жолағы бар Text өрісі (`wrap="word"`) және «Күнді қосу» батырмасы: курсор тұрған жерге бүгінгі күн мен уақыт қойылады («11.10.2026 15:30 — »).

### 2-тапсырма. Сөз санауыш
Text өрісі және төменде тірі статистика: таңбалар (бос орынсыз да), сөздер, сөйлемдер (`.`, `!`, `?` саны), жолдар. Статистика әр перне жіберілгенде (`<KeyRelease>`) жаңарсын.

### 3-тапсырма. Регистр түрлендіргіш
Text өрісі және «БАС ӘРІП», «кіші әріп», «Әр Сөз Бас Әріппен» (`title()`) батырмалары. Түрлендірілген мәтін сол өріске қайта жазылсын.

### 4-тапсырма. Тізімге айналдыру
Сол жақта Text (әр жолда бір оқушының аты), оң жақта айналдыру жолағы бар Listbox. «Тізімге көшіру» батырмасы бос емес жолдарды **алфавит бойынша сұрыптап** Listbox-қа шығарсын.

## Кеңейтілген деңгей

### 5-тапсырма. Іздеу және ауыстыру
Text өрісі, «Табу» және «Ауыстыру» өрістері, «Барлығын ауыстыру» батырмасы. Ауыстырылған сөздер жасыл фонмен бөлектелсін, төменде «N ауыстыру жасалды».

### 6-тапсырма. Чат-бот журналы
Тек оқуға арналған Text (журнал) + Entry. Пайдаланушы хабарламасы көк түспен (тег `user`), боттың жауабы жасыл түспен (тег `bot`) шығады. Бот кілт сөздерге жауап береді (`"сәлем"`, `"python"`, `"уақыт"`, `"сау бол"`), басқа жағдайда «Түсінбедім, басқаша сұраңызшы». Әр жаңа хабарламада журнал соңына айналады.

### 7-тапсырма. Жол нөмірлері
Text өрісінің сол жағында жол нөмірлерін көрсететін тар Text (`state="disabled"`) болсын. Мәтін өзгергенде нөмірлер жаңарсын. (Қосымша: екі өріс бір Scrollbar-мен бірге айналсын.)

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Python синтаксисін бөлектеу
Text өрісіне Python коды жазылғанда кілт сөздер (`keyword.kwlist`) — қызғылт сары, жолдар (`"..."`) — жасыл, түсініктемелер (`# ...`) — сұр түспен бөлектелсін. Кеңес: `re.finditer` арқылы әр жолды талдап, `"{жол}.{бас}"` индекстерін есептеңіз; бөлектеу `<KeyRelease>` оқиғасында жаңарады.

## Үй тапсырмасы

1. 2-тапсырма мен 5-тапсырманы біріктіріп, «Мәтін құралдары» қосымшасын жасаңыз.
2. Абайдың кез келген қара сөзін Text-ке енгізіп, ең жиі кездесетін 5 сөзді (3 әріптен ұзын) Listbox-та жиілігімен шығарыңыз (`collections.Counter`).
3. GitHub-қа `gui/lesson27_text.py` деп жүктеңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import datetime
import tkinter as tk
from tkinter import scrolledtext


def add_date():
    stamp = datetime.datetime.now().strftime("%d.%m.%Y %H:%M — ")
    diary.insert("insert", stamp)
    diary.focus()


root = tk.Tk()
root.title("Күнделік")
diary = scrolledtext.ScrolledText(root, width=50, height=15, wrap="word")
diary.pack(padx=10, pady=5, fill="both", expand=True)
tk.Button(root, text="Күнді қосу", command=add_date).pack(pady=5)
root.mainloop()
```

**2-тапсырма**
```python
import tkinter as tk


def update(event=None):
    s = text.get("1.0", "end-1c")
    no_spaces = len(s.replace(" ", "").replace("\n", ""))
    sentences = sum(s.count(ch) for ch in ".!?")
    lines = s.count("\n") + 1 if s else 0
    stats.config(text=f"Таңба: {len(s)} (бос орынсыз: {no_spaces}) | "
                      f"Сөз: {len(s.split())} | Сөйлем: {sentences} | Жол: {lines}")


root = tk.Tk()
root.title("Санауыш")
text = tk.Text(root, width=60, height=12, wrap="word")
text.pack(padx=10, pady=5)
text.bind("<KeyRelease>", update)
stats = tk.Label(root, text="")
stats.pack(pady=5)
update()
root.mainloop()
```

**3-тапсырма**
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
tk.Button(bar, text="БАС ӘРІП", command=lambda: transform(str.upper)).pack(side="left", padx=3)
tk.Button(bar, text="кіші әріп", command=lambda: transform(str.lower)).pack(side="left", padx=3)
tk.Button(bar, text="Әр Сөз Бас Әріппен",
          command=lambda: transform(str.title)).pack(side="left", padx=3)
root.mainloop()
```

**4-тапсырма**
```python
import tkinter as tk


def copy_to_list():
    names = [line.strip() for line in text.get("1.0", "end-1c").splitlines() if line.strip()]
    lb.delete(0, tk.END)
    for name in sorted(names):
        lb.insert(tk.END, name)


root = tk.Tk()
root.title("Тізім")
text = tk.Text(root, width=25, height=12)
text.grid(row=0, column=0, padx=5, pady=5)
tk.Button(root, text="Тізімге\nкөшіру →", command=copy_to_list).grid(row=0, column=1)
right = tk.Frame(root)
right.grid(row=0, column=2, padx=5, pady=5)
sb = tk.Scrollbar(right)
sb.pack(side="right", fill="y")
lb = tk.Listbox(right, height=12, yscrollcommand=sb.set)
lb.pack(side="left")
sb.config(command=lb.yview)
root.mainloop()
```

**5-тапсырма**
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
    info.config(text=f"{count} ауыстыру жасалды")


root = tk.Tk()
root.title("Ауыстыру")
top = tk.Frame(root)
top.pack(pady=5)
tk.Label(top, text="Табу:").grid(row=0, column=0)
find_e = tk.Entry(top)
find_e.grid(row=0, column=1, padx=3)
tk.Label(top, text="Ауыстыру:").grid(row=0, column=2)
repl_e = tk.Entry(top)
repl_e.grid(row=0, column=3, padx=3)
tk.Button(top, text="Барлығын ауыстыру", command=replace_all).grid(row=0, column=4, padx=3)
text = tk.Text(root, width=70, height=12, wrap="word")
text.pack(padx=10, pady=5)
text.tag_config("done", background="#b8e994")
info = tk.Label(root, text="")
info.pack()
root.mainloop()
```

**6-тапсырма**
```python
import time
import tkinter as tk
from tkinter import scrolledtext

ANSWERS = {
    "сәлем": "Сәлем! Қалайсың?",
    "python": "Python — керемет тіл! Біз оны үйірмеде оқимыз.",
    "уақыт": lambda: "Қазір " + time.strftime("%H:%M"),
    "сау бол": "Сау бол! Тағы кел.",
}


def bot_reply(msg):
    low = msg.lower()
    for key, answer in ANSWERS.items():
        if key in low:
            return answer() if callable(answer) else answer
    return "Түсінбедім, басқаша сұраңызшы"


def write(line, tag):
    log.config(state="normal")
    log.insert(tk.END, line + "\n", tag)
    log.see(tk.END)
    log.config(state="disabled")


def send(event=None):
    msg = entry.get().strip()
    if not msg:
        return
    write("Сіз: " + msg, "user")
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
tk.Button(root, text="Жіберу", command=send).pack(side="left")
root.mainloop()
```

**7-тапсырма**
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
root.title("Жол нөмірлері")
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

**8-тапсырма**
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
root.title("Код редакторы")
text = tk.Text(root, width=70, height=20, font=("Consolas", 12), undo=True)
text.pack(padx=5, pady=5)
text.tag_config("kw", foreground="#e17055")
text.tag_config("str", foreground="#00b894")
text.tag_config("comment", foreground="#95a5a6")
text.tag_raise("comment")
text.bind("<KeyRelease>", highlight)
text.insert("1.0", 'def hello(name):\n    # сәлемдесу\n    return "Сәлем, " + name\n')
highlight()
root.mainloop()
```

</details>
