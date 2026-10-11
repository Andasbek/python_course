# Сабақ 63. Практикалық тапсырмалар: «Оқушылар базасы» — аяқтау, қорғау, Өзіндік жұмыс №4

## Базалық деңгей (жобаны аяқтау қадамдары)

### 1-қадам. `db.py` кеңейту
`StudentDB` класына `update`, `delete`, `search` (регистрге сезімсіз, `PYLOWER` арқылы), `stats`, `export_csv(path)`, `backup(folder="backups")` әдістерін қосыңыз. Әр әдісті `if __name__ == "__main__":` бөлімінде `":memory:"` қорында тексеріңіз.

### 2-қадам. Таңдау және өзгерту
- Кестеде жолды басқанда (`<<TreeviewSelect>>`) оқушы деректері формаға жүктелсін.
- «Сақтау» батырмасы таңдалған оқушыны жаңартсын (валидациямен!).
- Ештеңе таңдалмаса: «Алдымен оқушыны таңдаңыз».

### 3-қадам. Өшіру және іздеу
- «Өшіру» батырмасы — `askyesno` растауымен.
- Кестенің үстінде «Іздеу:» өрісі. Әр перне басылған сайын (`<KeyRelease>`) кесте сүзілсін; өріс бос болса — барлық оқушы.

### 4-қадам. Экспорт, backup, статистика
- «CSV экспорт» — `filedialog.asksaveasfilename` арқылы.
- «Backup» — `backups/students_ГГГГ-АА-КК_СС-ММ-СС.db`.
- Статус жолы: «Барлығы: 12 оқушы · Орташа балл: 84.3 · Ең жоғары: 98».

### 5-қадам. Тестілеу және қорғауға дайындық
Теориядағы тестілеу чек-парағының 10 сценарийін орындап, ✓ қойыңыз. Қорғауға 5–6 демо-оқушы енгізіңіз. Соңғы коммит: `git commit -m "Оқушылар базасы v1.0"`, GitHub-қа `git push`.

## Кеңейтілген деңгей

### 6-тапсырма. CSV-ден импорт
«Импорт» батырмасы: `askopenfilename` арқылы CSV таңдап, дұрыс жолдарды қорға қосу, қате жолдар санын хабарлау (61-сабақ, 6-тапсырма).

### 7-тапсырма. Сынып бойынша сүзгі
Іздеу өрісінің жанына `Combobox` («Барлығы», 1…11) қосыңыз; іздеу мәтіні мен сынып сүзгісі бірге жұмыс істесін.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Бағалар кестесі (JOIN)
`subjects` (пәндер) және `marks` (student_id, subject_id, mark) кестелерін қосыңыз. Оқушыны екі рет басқанда (`<Double-1>`) жаңа терезе (`tk.Toplevel`) ашылып, оның пәндер бойынша бағалары `JOIN` арқылы көрсетілсін.

### 9-тапсырма. «Болдырмау» (Undo)
Соңғы өшірілген оқушыны есте сақтап, «Болдырмау» батырмасы (немесе `Ctrl+Z`) оны қайта қоссын.

## Жобаны қорғау регламенті

| Кезең | Уақыт | Не көрсетіледі |
|---|---|---|
| Кіріспе | 20 сек | Атауы, кімге арналған |
| Демонстрация | 90 сек | Қосу → іздеу → өзгерту → өшіру → экспорт/backup |
| Код | 40 сек | `db.py` / `app.py` құрылымы, бір қызық шешім |
| Қиындықтар | 20 сек | Не қиын болды, қалай шешілді |
| Сұрақтар | 1 мин | Педагог пен сыныптастардың сұрақтары |

## Бағалау критерийлері (практикум-жоба)

| Критерий | Ұпай | % |
|---|---|---|
| Бағдарлама дұрыс жұмыс істейді, ТТ-ның 1–7 талабы орындалған | 8 | 40% |
| Шешімнің ұтымдылығы: қабаттарға бөлу, валидация, растау, қателерді өңдеу | 5 | 25% |
| Кодтың оқылымдылығы: атаулар, docstring, PEP 8, Git коммиттері | 4 | 20% |
| Өз бетінше орындау, мерзімінде тапсыру, қорғау сапасы | 3 | 15% |
| **Барлығы** | **20** | **100%** |

---

## Өзіндік жұмыс №4 (25 минут)

Барлық тапсырмада `shop.db` деректер қоры қолданылады (нұсқаға қарай кесте сәл өзгеше). Жауаптарды `.py` файлына жазып тапсырыңыз.

### 1-нұсқа

1. **(3 ұпай)** `books` кестесін құратын SQL жазыңыз: `id` (бастапқы кілт, автоөсу), `title` (міндетті мәтін), `author` (мәтін), `year` (бүтін сан), `price` (нақты сан).
2. **(3 ұпай)** Python-да `executemany` арқылы 4 кітап қосыңыз (кемінде 2 қазақ авторы — мысалы, Абай, М. Әуезов).
3. **(4 ұпай)** SQL сұраныстарын жазыңыз:
   а) 2000 жылдан кейін шыққан кітаптар, бағасы бойынша кемуі ретімен;
   ә) әр автордың кітап саны және орташа бағасы (`GROUP BY`).
4. **(3 ұпай)** Барлық кітаптың бағасын 10%-ға арттыратын `UPDATE` және бағасы 1000 теңгеден арзан кітаптарды өшіретін `DELETE` сұранысын жазыңыз.
5. **(4 ұпай)** `books` кестесін `books.csv` файлына экспорттайтын Python функциясын жазыңыз (тақырып жолымен, `utf-8-sig`).
6. **(3 ұпай)** Кітаптар тізімін `books.json` файлына сөздіктер тізімі түрінде (`ensure_ascii=False`, `indent=2`) сақтаңыз.

### 2-нұсқа

1. **(3 ұпай)** `products` кестесін құратын SQL жазыңыз: `id` (бастапқы кілт, автоөсу), `name` (міндетті мәтін), `category` (мәтін), `qty` (бүтін сан, әдепкі 0), `price` (нақты сан).
2. **(3 ұпай)** Python-да `executemany` арқылы 5 тауар қосыңыз (кемінде 2 санат: «Кеңсе», «Тағам»).
3. **(4 ұпай)** SQL сұраныстарын жазыңыз:
   а) бағасы 500-ден 2000 теңгеге дейінгі тауарлар, атауы бойынша алфавит ретімен;
   ә) әр санаттағы тауарлардың жалпы құны (`SUM(qty * price)`).
4. **(3 ұпай)** «Тағам» санатындағы тауарлардың санын 5-ке арттыратын `UPDATE` және саны 0-ге тең тауарларды өшіретін `DELETE` сұранысын жазыңыз.
5. **(4 ұпай)** `products.csv` файлынан (бөлгіш `;`) тауарларды оқып, кестеге қосатын Python функциясын жазыңыз.
6. **(3 ұпай)** Деректер қорының `Connection.backup()` арқылы уақыт белгісі бар көшірмесін жасайтын функция жазыңыз.

### Өзіндік жұмысты бағалау критерийлері

| Критерий | % |
|---|---|
| Бағдарлама/сұраныс дұрыс жұмыс істейді және шартқа сай | 40% |
| Шешімнің тиімділігі (параметрленген сұраныс, `with`, `executemany`) | 25% |
| Кодтың оқылымдылығы (атаулар, түсініктемелер) | 20% |
| Өз бетінше және уақытында орындау | 15% |

Ұпайды бағаға аудару (20 ұпай): 17–20 — жоғары деңгей (85–100%), 13–16 — орта деңгей, 10–12 — қанағаттанарлық, 9 және одан төмен — қайта тапсыру.

## Үй тапсырмасы

1. Жобаның README.md файлын жазыңыз: сипаттама, скриншот, іске қосу жолы, мүмкіндіктер тізімі.
2. Қорғауда алған «тілекті» жүзеге асырыңыз және коммит жасаңыз.
3. Келесі модульге дайындық: браузерде кез келген сайтты ашып, F12 → Network қойындысын қараңыз. Қандай сұраныстар жіберілетінін жазып келіңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**Жобаның соңғы нұсқасы — `db.py`**
```python
import csv
import sqlite3
from datetime import datetime
from pathlib import Path

FIELDS = "id, name, grade, city, phone, score"


class StudentDB:
    """Оқушылар деректер қоры."""

    def __init__(self, path="students.db"):
        self.path = path
        self.conn = sqlite3.connect(path)
        self.conn.create_function("PYLOWER", 1, lambda s: s.lower() if s else s)
        with self.conn:
            self.conn.execute("""CREATE TABLE IF NOT EXISTS students (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                grade INTEGER NOT NULL CHECK (grade BETWEEN 1 AND 11),
                city TEXT, phone TEXT,
                score REAL CHECK (score BETWEEN 0 AND 100))""")

    def add(self, name, grade, city, phone, score):
        with self.conn:
            return self.conn.execute(
                "INSERT INTO students (name, grade, city, phone, score) VALUES (?, ?, ?, ?, ?)",
                (name, grade, city, phone, score)).lastrowid

    def update(self, sid, name, grade, city, phone, score):
        with self.conn:
            return self.conn.execute(
                "UPDATE students SET name=?, grade=?, city=?, phone=?, score=? WHERE id=?",
                (name, grade, city, phone, score, sid)).rowcount

    def delete(self, sid):
        with self.conn:
            return self.conn.execute("DELETE FROM students WHERE id=?", (sid,)).rowcount

    def get_all(self):
        return self.conn.execute(f"SELECT {FIELDS} FROM students ORDER BY name").fetchall()

    def search(self, text):
        p = f"%{text.lower()}%"
        return self.conn.execute(
            f"SELECT {FIELDS} FROM students "
            "WHERE PYLOWER(name) LIKE ? OR PYLOWER(city) LIKE ? ORDER BY name", (p, p)).fetchall()

    def stats(self):
        return self.conn.execute(
            "SELECT COUNT(*), ROUND(AVG(score), 1), MAX(score) FROM students").fetchone()

    def export_csv(self, path):
        cur = self.conn.execute(f"SELECT {FIELDS} FROM students ORDER BY name")
        rows = cur.fetchall()
        with open(path, "w", newline="", encoding="utf-8-sig") as f:
            w = csv.writer(f, delimiter=";")
            w.writerow([c[0] for c in cur.description])
            w.writerows(rows)
        return len(rows)

    def backup(self, folder="backups"):
        Path(folder).mkdir(exist_ok=True)
        target = Path(folder) / f"students_{datetime.now():%Y-%m-%d_%H-%M-%S}.db"
        dst = sqlite3.connect(target)
        with dst:
            self.conn.backup(dst)
        dst.close()
        return target

    def close(self):
        self.conn.close()


if __name__ == "__main__":
    db = StudentDB(":memory:")
    a = db.add("Айгерім Сәрсенова", 9, "Алматы", "", 92.5)
    db.add("Нұрлан Жақсылықов", 10, "Астана", "", 78)
    print(db.search("алм"))
    print(db.stats())
```

**Жобаның соңғы нұсқасы — `app.py`**
```python
import tkinter as tk
from tkinter import ttk, messagebox, filedialog

from db import StudentDB

COLUMNS = ("id", "name", "grade", "city", "phone", "score")
HEADERS = ("ID", "Аты-жөні", "Сынып", "Қала", "Телефон", "Балл")
WIDTHS = (40, 220, 60, 120, 130, 60)
FORM_KEYS = ("name", "grade", "city", "phone", "score")


def validate(name, grade, phone, score):
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
        root.title("Оқушылар базасы")
        root.geometry("820x540")
        self.db = StudentDB()
        self.entries = {}
        self.build_form()
        self.build_search()
        self.build_table()
        self.status = tk.Label(root, anchor="w", relief="sunken")
        self.status.pack(fill="x", side="bottom")
        root.protocol("WM_DELETE_WINDOW", self.on_close)
        self.refresh()

    # ---------- интерфейс ----------
    def build_form(self):
        form = ttk.LabelFrame(self.root, text="Оқушы деректері")
        form.pack(fill="x", padx=10, pady=5)
        labels = ("Аты-жөні:", "Сынып:", "Қала:", "Телефон:", "Балл:")
        for i, (key, label) in enumerate(zip(FORM_KEYS, labels)):
            r, c = divmod(i, 2)
            ttk.Label(form, text=label).grid(row=r, column=c * 2, sticky="e", padx=5, pady=3)
            e = ttk.Entry(form, width=28)
            e.grid(row=r, column=c * 2 + 1, sticky="w", padx=5, pady=3)
            self.entries[key] = e
        bar = ttk.Frame(form)
        bar.grid(row=3, column=0, columnspan=4, pady=5)
        for text, cmd in (("Қосу", self.add_student), ("Сақтау", self.update_student),
                          ("Өшіру", self.delete_student), ("Тазалау", self.clear_form),
                          ("CSV экспорт", self.export_csv), ("Backup", self.make_backup)):
            ttk.Button(bar, text=text, command=cmd).pack(side="left", padx=3)

    def build_search(self):
        frame = ttk.Frame(self.root)
        frame.pack(fill="x", padx=10)
        ttk.Label(frame, text="Іздеу:").pack(side="left")
        self.search_entry = ttk.Entry(frame, width=30)
        self.search_entry.pack(side="left", padx=5)
        self.search_entry.bind("<KeyRelease>", lambda e: self.refresh())

    def build_table(self):
        frame = ttk.Frame(self.root)
        frame.pack(fill="both", expand=True, padx=10, pady=5)
        self.tree = ttk.Treeview(frame, columns=COLUMNS, show="headings")
        for col, head, w in zip(COLUMNS, HEADERS, WIDTHS):
            self.tree.heading(col, text=head)
            self.tree.column(col, width=w, anchor="center" if w <= 60 else "w")
        sb = ttk.Scrollbar(frame, orient="vertical", command=self.tree.yview)
        self.tree.configure(yscrollcommand=sb.set)
        self.tree.pack(side="left", fill="both", expand=True)
        sb.pack(side="right", fill="y")
        self.tree.bind("<<TreeviewSelect>>", self.on_select)

    # ---------- көмекші ----------
    def get_form(self):
        return {k: e.get().strip() for k, e in self.entries.items()}

    def clear_form(self):
        for e in self.entries.values():
            e.delete(0, tk.END)
        self.entries["name"].focus_set()

    def selected_id(self):
        sel = self.tree.selection()
        if not sel:
            messagebox.showwarning("Ескерту", "Алдымен оқушыны таңдаңыз")
            return None
        return int(self.tree.item(sel[0], "values")[0])

    def refresh(self):
        text = self.search_entry.get().strip()
        rows = self.db.search(text) if text else self.db.get_all()
        self.tree.delete(*self.tree.get_children())
        for row in rows:
            self.tree.insert("", "end", values=row)
        count, avg, best = self.db.stats()
        self.status.config(text=f"Барлығы: {count} оқушы · Орташа балл: {avg or 0} · Ең жоғары: {best or 0}")

    def checked_data(self):
        d = self.get_form()
        err = validate(d["name"], d["grade"], d["phone"], d["score"])
        if err:
            messagebox.showerror("Қате", err)
            return None
        return (d["name"], int(d["grade"]), d["city"], d["phone"],
                float(d["score"].replace(",", ".")))

    # ---------- оқиғалар ----------
    def on_select(self, event):
        sel = self.tree.selection()
        if not sel:
            return
        values = self.tree.item(sel[0], "values")
        for key, value in zip(FORM_KEYS, values[1:]):
            self.entries[key].delete(0, tk.END)
            self.entries[key].insert(0, value)

    def add_student(self):
        data = self.checked_data()
        if data:
            self.db.add(*data)
            self.clear_form()
            self.refresh()

    def update_student(self):
        sid = self.selected_id()
        data = self.checked_data() if sid else None
        if data:
            self.db.update(sid, *data)
            self.refresh()

    def delete_student(self):
        sid = self.selected_id()
        if sid and messagebox.askyesno("Растау", "Таңдалған оқушыны өшіру керек пе?"):
            self.db.delete(sid)
            self.clear_form()
            self.refresh()

    def export_csv(self):
        path = filedialog.asksaveasfilename(defaultextension=".csv",
                                            filetypes=[("CSV файлдар", "*.csv")],
                                            initialfile="students.csv")
        if path:
            n = self.db.export_csv(path)
            messagebox.showinfo("Экспорт", f"{n} жазба сақталды:\n{path}")

    def make_backup(self):
        target = self.db.backup()
        messagebox.showinfo("Backup", f"Көшірме жасалды:\n{target}")

    def on_close(self):
        self.db.close()
        self.root.destroy()


if __name__ == "__main__":
    root = tk.Tk()
    StudentApp(root)
    root.mainloop()
```

**9-тапсырма (Undo) — идея**
```python
# delete_student ішінде өшірер алдында:
#     self.last_deleted = self.tree.item(self.tree.selection()[0], "values")
# def undo(self):
#     if self.last_deleted:
#         _, name, grade, city, phone, score = self.last_deleted
#         self.db.add(name, int(grade), city, phone, float(score))
#         self.last_deleted = None
#         self.refresh()
# self.root.bind("<Control-z>", lambda e: self.undo())
```

**Өзіндік жұмыс №4, 1-нұсқа**
```python
import csv
import json
import sqlite3

conn = sqlite3.connect("shop.db")
conn.row_factory = sqlite3.Row
# 1
conn.execute("""CREATE TABLE IF NOT EXISTS books (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL, author TEXT, year INTEGER, price REAL)""")
# 2
books = [("Абай жолы", "Мұхтар Әуезов", 1942, 4500),
         ("Қара сөздер", "Абай Құнанбайұлы", 2015, 2500),
         ("Көшпенділер", "Ілияс Есенберлин", 2005, 6000),
         ("Python негіздері", "А. Сериков", 2021, 800)]
conn.executemany("INSERT INTO books (title, author, year, price) VALUES (?, ?, ?, ?)", books)
conn.commit()
# 3а
for r in conn.execute("SELECT * FROM books WHERE year > 2000 ORDER BY price DESC"):
    print(dict(r))
# 3ә
for r in conn.execute("SELECT author, COUNT(*) AS cnt, AVG(price) AS avg_price FROM books GROUP BY author"):
    print(r["author"], r["cnt"], round(r["avg_price"], 1))
# 4
conn.execute("UPDATE books SET price = price * 1.1")
conn.execute("DELETE FROM books WHERE price < 1000")
conn.commit()
# 5
def export_books(path="books.csv"):
    cur = conn.execute("SELECT * FROM books")
    with open(path, "w", newline="", encoding="utf-8-sig") as f:
        w = csv.writer(f)
        w.writerow([c[0] for c in cur.description])
        w.writerows(tuple(r) for r in cur)
export_books()
# 6
data = [dict(r) for r in conn.execute("SELECT * FROM books")]
with open("books.json", "w", encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False, indent=2)
conn.close()
```

**Өзіндік жұмыс №4, 2-нұсқа**
```python
import csv
import sqlite3
from datetime import datetime

conn = sqlite3.connect("shop.db")
# 1
conn.execute("""CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL, category TEXT, qty INTEGER DEFAULT 0, price REAL)""")
# 2
items = [("Дәптер", "Кеңсе", 50, 250), ("Қалам", "Кеңсе", 100, 150),
         ("Нан", "Тағам", 20, 230), ("Құрт", "Тағам", 0, 1500), ("Степлер", "Кеңсе", 5, 1900)]
conn.executemany("INSERT INTO products (name, category, qty, price) VALUES (?, ?, ?, ?)", items)
conn.commit()
# 3а
print(conn.execute("SELECT * FROM products WHERE price BETWEEN 500 AND 2000 ORDER BY name").fetchall())
# 3ә
print(conn.execute("SELECT category, SUM(qty * price) FROM products GROUP BY category").fetchall())
# 4
conn.execute("UPDATE products SET qty = qty + 5 WHERE category = 'Тағам'")
conn.execute("DELETE FROM products WHERE qty = 0")
conn.commit()
# 5
def import_products(path="products.csv"):
    with open(path, encoding="utf-8-sig", newline="") as f:
        rows = [(r["name"], r["category"], int(r["qty"]), float(r["price"]))
                for r in csv.DictReader(f, delimiter=";")]
    with conn:
        conn.executemany("INSERT INTO products (name, category, qty, price) VALUES (?, ?, ?, ?)", rows)
    return len(rows)
# 6
def backup_db():
    name = f"shop_{datetime.now():%Y-%m-%d_%H-%M-%S}.db"
    dst = sqlite3.connect(name)
    with dst:
        conn.backup(dst)
    dst.close()
    return name
print(backup_db())
conn.close()
```

</details>
