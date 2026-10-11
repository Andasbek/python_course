# Сабақ 62. Практикалық тапсырмалар: «Оқушылар базасы», 1-бөлім

> Бұл — практикум: бүгін қосымшаның қаңқасын құрамыз (қосу + көрсету + валидация). Келесі сабақта (63) жобаны аяқтап, қорғаймыз. Жұмысты жеке немесе жұппен орындауға болады.

## Базалық деңгей (міндетті қадамдар)

### 1-қадам. Жоба құрылымы және Git
1. `student_base` папкасын жасап, VS Code-та ашыңыз.
2. `git init` орындаңыз, `.gitignore` файлын жасаңыз:
```text
students.db
*.db
__pycache__/
backups/
```
3. Бос `db.py` және `app.py` файлдарын жасаңыз.

### 2-қадам. Деректер қабаты — `db.py`
Теориядағы `StudentDB` класын жазыңыз (`add`, `get_all`, `count`, `close`). Файлды жеке іске қосып, `":memory:"` қорында 2–3 оқушы қосылып, басылып шығатынын тексеріңіз. Коммит: `git commit -m "db.py: StudentDB класы"`.

### 3-қадам. Терезе және форма
`app.py` ішінде `StudentApp` класын жасаңыз: терезе тақырыбы «Оқушылар базасы», өлшемі 760×480. `ttk.LabelFrame` ішіне `grid` арқылы бес өріс (Аты-жөні, Сынып, Қала, Телефон, Балл) және екі батырма («Қосу», «Тазалау») орналастырыңыз.

### 4-қадам. Кесте
Форманың астына `ttk.Treeview` (бағандар: ID, Аты-жөні, Сынып, Қала, Телефон, Балл) және тік айналдыру жолағын (`ttk.Scrollbar`) қосыңыз. Терезе ашылғанда `refresh()` әдісі қордағы барлық оқушыны кестеге шығарсын. Төменде статус жолы: «Барлығы: N оқушы».

### 5-қадам. Қосу және валидация
«Қосу» батырмасы: өрістерді оқиды → `validate()` → қате болса `messagebox.showerror`, болмаса `db.add()` → `refresh()` → өрістерді тазалайды. «Тазалау» батырмасы тек өрістерді тазалайды. 5 оқушы қосып тексеріңіз, бағдарламаны жауып, қайта ашыңыз — деректер сақталуы керек.

Тексеру сценарийлері:
```text
Аты бос, Қосу басылды        → «Аты-жөні бос болмауы керек»
Сынып = 12                   → «Сынып 1-ден 11-ге дейінгі бүтін сан болуы керек»
Балл = 88,5                  → қабылданады, кестеде 88.5
Балл = жүз                   → «Балл сан болуы керек»
```

## Кеңейтілген деңгей

### 6-тапсырма. Телефон нөмірін тексеру
Телефон бос болуы мүмкін, бірақ толтырылса, `+7` және 10 цифр (барлығы 12 таңба) форматында болсын. `validate()` функциясына тексеру қосыңыз.

### 7-тапсырма. Enter пернесі
Кез келген өрісте `Enter` басқанда «Қосу» орындалсын (`self.root.bind("<Return>", ...)`). Қосудан кейін курсор автоматты түрде «Аты-жөні» өрісіне қайтсын (`focus_set()`).

### 8-тапсырма. Сыныпты Combobox арқылы таңдау
Сынып өрісін `ttk.Combobox(values=list(range(1, 12)), state="readonly")` етіп ауыстырыңыз — қате енгізу мүмкіндігі азаяды.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Бағандар бойынша сұрыптау
Кесте тақырыбын (heading) басқанда сол баған бойынша сұрыпталсын, қайта басқанда — кері ретпен. Кеңес: `tree.heading(col, command=lambda c=col: self.sort_by(c))`.

### 10-тапсырма. Түсті жолдар
Балы 90-нан жоғары оқушылардың жолы жасыл, 50-ден төмендері қызғылт түспен белгіленсін (`tree.tag_configure("top", background="#d4f7d4")`, `insert(..., tags=("top",))`).

## Үй тапсырмасы

1. Бүгінгі 1–5-қадамдарды толық аяқтап, GitHub-қа жүктеңіз (кемінде 3 коммит).
2. Келесі сабаққа дайындық: `tree.selection()`, `tree.item()`, `messagebox.askyesno()` туралы оқыңыз.
3. ТТ-дағы 4–8-талаптарды орындау жоспарын жазыңыз (қандай әдістер қажет болады).

## Бағалау критерийлері

| Критерий | Ұпай | % |
|---|---|---|
| Бағдарлама дұрыс жұмыс істейді: қосу, көрсету, деректер сақталады (1–5-қадамдар) | 8 | 40% |
| Шешімнің ұтымдылығы: қабаттарға бөлу, валидация, `refresh()` әдісі | 5 | 25% |
| Кодтың оқылымдылығы: атаулар, docstring, түсініктемелер, PEP 8 | 4 | 20% |
| Өз бетінше және уақытында орындау, Git коммиттері | 3 | 15% |
| **Барлығы** | **20** | **100%** |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**`db.py`** — теориядағы `StudentDB` класы (3-бөлім), өзгеріссіз.

**`app.py` — 3–5-қадамдар және 6–7-тапсырмалар**
```python
import tkinter as tk
from tkinter import ttk, messagebox

from db import StudentDB

COLUMNS = ("id", "name", "grade", "city", "phone", "score")
HEADERS = ("ID", "Аты-жөні", "Сынып", "Қала", "Телефон", "Балл")
WIDTHS = (40, 220, 60, 120, 130, 60)


def validate(name, grade, phone, score):
    """Қате мәтінін немесе None қайтарады."""
    if not name.strip():
        return "Аты-жөні бос болмауы керек"
    if not grade.isdigit() or not 1 <= int(grade) <= 11:
        return "Сынып 1-ден 11-ге дейінгі бүтін сан болуы керек"
    phone = phone.strip()
    if phone and not (phone.startswith("+7") and len(phone) == 12 and phone[1:].isdigit()):
        return "Телефон +7XXXXXXXXXX форматында болуы керек"
    try:
        s = float(score.replace(",", "."))
    except ValueError:
        return "Балл сан болуы керек"
    if not 0 <= s <= 100:
        return "Балл 0–100 аралығында болуы керек"
    return None


class StudentApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Оқушылар базасы")
        self.root.geometry("760x480")
        self.db = StudentDB()
        self.entries = {}
        self.build_form()
        self.build_table()
        self.status = tk.Label(root, anchor="w", relief="sunken")
        self.status.pack(fill="x", side="bottom")
        self.root.bind("<Return>", lambda e: self.add_student())
        self.root.protocol("WM_DELETE_WINDOW", self.on_close)
        self.refresh()

    def build_form(self):
        form = ttk.LabelFrame(self.root, text="Оқушы деректері")
        form.pack(fill="x", padx=10, pady=5)
        fields = [("name", "Аты-жөні:"), ("grade", "Сынып:"), ("city", "Қала:"),
                  ("phone", "Телефон:"), ("score", "Балл:")]
        for i, (key, label) in enumerate(fields):
            row, col = divmod(i, 2)
            ttk.Label(form, text=label).grid(row=row, column=col * 2, sticky="e", padx=5, pady=3)
            entry = ttk.Entry(form, width=28)
            entry.grid(row=row, column=col * 2 + 1, sticky="w", padx=5, pady=3)
            self.entries[key] = entry
        buttons = ttk.Frame(form)
        buttons.grid(row=3, column=0, columnspan=4, pady=5)
        ttk.Button(buttons, text="Қосу", command=self.add_student).pack(side="left", padx=5)
        ttk.Button(buttons, text="Тазалау", command=self.clear_form).pack(side="left", padx=5)

    def build_table(self):
        frame = ttk.Frame(self.root)
        frame.pack(fill="both", expand=True, padx=10, pady=5)
        self.tree = ttk.Treeview(frame, columns=COLUMNS, show="headings")
        for col, head, w in zip(COLUMNS, HEADERS, WIDTHS):
            self.tree.heading(col, text=head)
            self.tree.column(col, width=w, anchor="center" if w <= 60 else "w")
        scroll = ttk.Scrollbar(frame, orient="vertical", command=self.tree.yview)
        self.tree.configure(yscrollcommand=scroll.set)
        self.tree.pack(side="left", fill="both", expand=True)
        scroll.pack(side="right", fill="y")

    def get_form(self):
        return {k: e.get().strip() for k, e in self.entries.items()}

    def clear_form(self):
        for e in self.entries.values():
            e.delete(0, tk.END)
        self.entries["name"].focus_set()

    def refresh(self):
        self.tree.delete(*self.tree.get_children())
        for row in self.db.get_all():
            self.tree.insert("", "end", values=row)
        self.status.config(text=f"Барлығы: {self.db.count()} оқушы")

    def add_student(self):
        d = self.get_form()
        error = validate(d["name"], d["grade"], d["phone"], d["score"])
        if error:
            messagebox.showerror("Қате", error)
            return
        self.db.add(d["name"], int(d["grade"]), d["city"], d["phone"],
                    float(d["score"].replace(",", ".")))
        self.refresh()
        self.clear_form()

    def on_close(self):
        self.db.close()
        self.root.destroy()


if __name__ == "__main__":
    root = tk.Tk()
    StudentApp(root)
    root.mainloop()
```

**8-тапсырма** (`build_form` ішінде сынып өрісін ауыстыру)
```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
grade_box = ttk.Combobox(root, values=list(range(1, 12)), state="readonly", width=5)
grade_box.set("9")
grade_box.pack()
# self.entries["grade"] = grade_box  — .get() әдісі Entry сияқты жұмыс істейді
root.mainloop()
```

**9-тапсырма** (`StudentApp` класына қосылатын әдіс)
```python
def sort_by(self, col):
    rows = [(self.tree.set(iid, col), iid) for iid in self.tree.get_children()]
    reverse = getattr(self, "_sort_reverse", False)
    try:
        rows.sort(key=lambda t: float(t[0]), reverse=reverse)
    except ValueError:
        rows.sort(key=lambda t: t[0].lower(), reverse=reverse)
    for index, (_, iid) in enumerate(rows):
        self.tree.move(iid, "", index)
    self._sort_reverse = not reverse

# build_table ішінде:
# self.tree.heading(col, text=head, command=lambda c=col: self.sort_by(c))
```

**10-тапсырма** (`refresh` әдісінің нұсқасы)
```python
def refresh(self):
    self.tree.tag_configure("top", background="#d4f7d4")
    self.tree.tag_configure("low", background="#ffd6d6")
    self.tree.delete(*self.tree.get_children())
    for row in self.db.get_all():
        score = row[5] or 0
        tag = "top" if score >= 90 else "low" if score < 50 else ""
        self.tree.insert("", "end", values=row, tags=(tag,))
```

</details>
