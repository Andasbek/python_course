# Сабақ 97. Практикалық тапсырмалар: интерфейсті жүзеге асыру және біріктіру

> Бүгінгі мақсат — **MVP**: бағдарлама терезеден (немесе ойын экранынан) толық басқарылады, деректер сақталады. Қосымша безендіру 100-сабақта болады — бүгін тек жұмыс істейтін нұсқа.

## Базалық деңгей

### 1-тапсырма. Прототипті тірілту (міндетті)
92-сабақтағы `prototype.py`-ді `gui.py`-ге айналдырыңыз:
- интерфейс класы (`App(tk.Tk)` немесе `Game`) логика объектісін конструктор арқылы алады;
- жалған деректер (`FAKE_...`) толық алынып тасталды — деректер логикадан келеді;
- барлық «Прототип» хабарламалары нағыз әрекетпен ауыстырылды (немесе 100-сабаққа issue ретінде қалдырылды).

### 2-тапсырма. `refresh()` және қателерді өңдеу (міндетті)
- Деректерді өзгертетін әр әрекеттен кейін бір `refresh()` әдісі бүкіл экранды жаңартады.
- Әр енгізу өрісі тексеріледі; қате болса — қазақша түсінікті хабарлама, бағдарлама құламайды.
- Жою/тазалау әрекеттері растауды сұрайды.

### 3-тапсырма. `main.py` (міндетті)
`main.py` тек үш нәрсе жасайды: деректер қабатын құрады, логиканы құрады, интерфейсті іске қосады. Терезе жабылғанда ДҚ қосылымы жабылады.

### 4-тапсырма. Интеграциялық тексеру
Теориядағы 8-бөлімнің кестесі бойынша жобаңыздың кемінде 5 сценарийін өтіп, нәтижесін `docs/test_log.md`-ге қосыңыз. Барлық Must-сценарий өтсе: `git tag mvp` және `git push --tags`.

## Кеңейтілген деңгей

### 5-тапсырма. Қатені табыңыз
Төмендегі кодта 4 интеграциялық қате бар. Оларды тауып, түзетіңіз.

```python
import tkinter as tk
from tkinter import messagebox


class Counter:
    def __init__(self):
        self.value = 0

    def add(self, n):
        if n <= 0:
            raise ValueError("Сан оң болуы керек")
        self.value += n


class App(tk.Tk):
    def __init__(self, counter):
        super().__init__()
        self.counter = counter
        self.entry = tk.Entry(self)
        self.entry.pack()
        tk.Button(self, text="Қосу", command=self.on_add()).pack()
        self.label = tk.Label(self, text="0")
        self.label.pack()

    def on_add(self):
        self.counter.add(int(self.entry.get()))
        self.counter.value += 0


if __name__ == "__main__":
    App(Counter).mainloop()
```

### 6-тапсырма. Іздеу өрісі
Кестенің үстіне іздеу өрісін қосыңыз: пайдаланушы мәтін теруге сәйкес кестеде тек сәйкес келетін жолдар қалады (түсініктеме немесе санат бойынша, әріп регистріне қарамай). *Кеңес:* `entry.bind("<KeyRelease>", ...)` және 95-сабақтағы `filter_ops`.

### 7-тапсырма. Ай бойынша сүзгі
`Combobox`-қа барлық бар айлардың тізімін (`2027-03`, `2027-04`, ...) шығарып, таңдалған ай бойынша баланс, кесте және диаграмма жаңаратындай етіңіз. Айлар тізімін ДҚ-дан `SELECT DISTINCT substr(op_date, 1, 7)` арқылы алыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Пернетақта жылдам пернелері
`Ctrl+S` — сақтау, `Delete` — таңдалғанды жою, `Ctrl+F` — іздеу өрісіне өту, `Esc` — өрістерді тазалау. Жылдам пернелердің тізімін «Анықтама» мәзірінде (`Menu`) көрсетіңіз.

### 9-тапсырма. Кестені сұрыптау
`Treeview` бағанының тақырыбын басқанда кесте сол баған бойынша сұрыпталсын, қайта басқанда — кері ретпен. «Сома» бағаны сан ретінде (`+3000`, `−800`) дұрыс сұрыпталуы керек.

## Үй тапсырмасы
1. MVP-ді аяқтап, `mvp` тегін GitHub-қа жүктеңіз.
2. README-дегі «Іске қосу» бөлімін тексеріңіз: жоба таза папкаға клондалып, `pip install -r requirements.txt` және `python main.py` арқылы іске қосыла ма?
3. Келесі сабақ — code review. Өз кодыңызды бір рет оқып шығып, өзіңізге ұнамайтын 3 жерді жазып келіңіз.

## Бағалау критерийлері
| Критерий | Үлес | Не тексеріледі |
|---|---|---|
| MVP жұмыс істейді | 40% | барлық Must-сценарий терезеден өтеді, деректер сақталады |
| Интеграцияның сапасы | 25% | қабаттар араласпаған, `refresh()`, қателер өңделген |
| Код құрылымы | 20% | интерфейс класы, түсінікті әдістер, `main.py` қысқа |
| Өз бетінше жұмыс, коммиттер | 15% | `mvp` тегі, тексеру журналы толтырылған |

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**1–4-тапсырмалар** жеке тексеріледі: оқушыдан 5 сценарийді көзінше көрсетуді сұраңыз; бағдарламаны жауып-ашып, деректердің сақталғанын тексеріңіз; бос/қате енгізу жасап көріңіз.

**5-тапсырма.** Қателер: (1) `command=self.on_add()` — жақшамен, функция бірден шақырылады; (2) `App(Counter)` — класс беріледі, объект емес; (3) `int(...)` және `ValueError` өңделмеген; (4) мән өзгергенде белгі (`label`) жаңармайды; сондай-ақ `self.label` батырма құрылған кезде әлі жоқ (1-қатемен бірге `AttributeError` береді).
```python
import tkinter as tk
from tkinter import messagebox


class Counter:
    def __init__(self):
        self.value = 0

    def add(self, n):
        if n <= 0:
            raise ValueError("Сан оң болуы керек")
        self.value += n


class App(tk.Tk):
    def __init__(self, counter):
        super().__init__()
        self.counter = counter
        self.entry = tk.Entry(self)
        self.entry.pack()
        tk.Button(self, text="Қосу", command=self.on_add).pack()
        self.label = tk.Label(self, text="0")
        self.label.pack()

    def on_add(self):
        try:
            self.counter.add(int(self.entry.get()))
        except ValueError:
            messagebox.showwarning("Қате", "Оң бүтін сан енгізіңіз")
            return
        self.refresh()

    def refresh(self):
        self.label.config(text=str(self.counter.value))


if __name__ == "__main__":
    App(Counter()).mainloop()
```

**6-тапсырма** (`App` класына қосылатын әдістер):
```python
import tkinter as tk
from tkinter import ttk


class SearchMixin:
    """App-қа іздеу мүмкіндігін қосатын әдістер (self.table, self.wallet бар деп есептейміз)."""

    def _build_search(self, row):
        self.search_var = tk.StringVar()
        entry = ttk.Entry(self, textvariable=self.search_var)
        entry.grid(row=row, column=0, sticky="ew", padx=10)
        entry.bind("<KeyRelease>", lambda event: self.refresh_table())

    def refresh_table(self):
        text = self.search_var.get().strip().lower()
        self.table.delete(*self.table.get_children())
        for op in self.wallet.operations():
            haystack = f"{op['category']} {op['note']}".lower()
            if text and text not in haystack:
                continue
            self.table.insert("", "end", iid=str(op["id"]),
                              values=(op["op_date"], op["category"], op["amount"], op["note"]))
```
Қолдану: `class App(SearchMixin, tk.Tk)` және `refresh()` ішінде кестені толтырудың орнына `self.refresh_table()`.

**7-тапсырма.**
```python
import sqlite3


def months(conn):
    """ДҚ-дағы барлық айлар, жаңасы бірінші."""
    rows = conn.execute(
        "SELECT DISTINCT substr(op_date, 1, 7) AS m FROM operation ORDER BY m DESC")
    return [r[0] for r in rows]


conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE operation (id INTEGER PRIMARY KEY, op_date TEXT);
INSERT INTO operation (op_date) VALUES ('2027-03-30'), ('2027-04-01'), ('2027-04-20');
""")
print(months(conn))
```
Нәтижесі:
```text
['2027-04', '2027-03']
```
Интерфейсте: `self.month_box["values"] = months(...)`, `self.month_box.bind("<<ComboboxSelected>>", lambda e: self.refresh())`, ал `refresh()` ішінде `month = self.month_box.get()` логика әдістеріне беріледі.

**8-тапсырма** (`App.__init__` ішіне):
```python
def bind_shortcuts(app):
    """Жылдам пернелерді байланыстырады. app — App объектісі."""
    app.bind("<Control-s>", lambda event: app.on_save())
    app.bind("<Delete>", lambda event: app.on_delete())
    app.bind("<Control-f>", lambda event: app.search_entry.focus_set())
    app.bind("<Escape>", lambda event: (app.amount_entry.delete(0, "end"),
                                        app.note_entry.delete(0, "end")))
```
`Menu` → «Анықтама» → `messagebox.showinfo("Жылдам пернелер", "Ctrl+S — сақтау\nDelete — жою\n...")`.

**9-тапсырма.**
```python
def sort_column(tree, col, reverse=False):
    """Treeview бағанын сұрыптайды; сандарды сан ретінде салыстырады."""
    def key(value):
        cleaned = value.replace("−", "-").replace("+", "").replace(" ", "")
        try:
            return (0, int(cleaned))
        except ValueError:
            return (1, value.lower())

    items = [(tree.set(iid, col), iid) for iid in tree.get_children("")]
    items.sort(key=lambda item: key(item[0]), reverse=reverse)
    for index, (_, iid) in enumerate(items):
        tree.move(iid, "", index)
    tree.heading(col, command=lambda: sort_column(tree, col, not reverse))
```
Қолдану: әр баған үшін `tree.heading(col, text=..., command=lambda c=col: sort_column(tree, c))`. Назар аударыңыз: кестеде «−» (минус белгісі, U+2212) қолданылғандықтан, оны кәдімгі «-»-ке ауыстыру керек — әйтпесе `int()` қате береді.
</details>
