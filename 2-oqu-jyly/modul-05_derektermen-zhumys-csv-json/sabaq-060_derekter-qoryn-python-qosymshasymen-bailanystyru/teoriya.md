# Сабақ 60. Деректер қорын Python қосымшасымен байланыстыру. CRUD амалдары.

## Тақырыптар

1. CRUD дегеніміз не?
2. Қосымшаның қабаттары
3. Репозиторий класы
4. Валидация және қателерді өңдеу
5. Консольдық CRUD қосымшасы
6. Сол репозиторий — Tkinter терезесінде
7. Репозиторийді тестілеу
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. CRUD дегеніміз не?

**CRUD** — деректермен жұмыстың төрт негізгі амалының қысқартылған атауы. Кез келген деректер қосымшасы (kundelik.kz, Kaspi, Instagram, телефон кітапшасы) осы төрт амалға негізделген.

| Амал | Мағынасы | SQL | Мысал (Instagram) |
|---|---|---|---|
| **C**reate | құру, қосу | `INSERT` | жаңа пост жариялау |
| **R**ead | оқу, іздеу | `SELECT` | лентаны қарау |
| **U**pdate | өзгерту | `UPDATE` | пост сипаттамасын түзету |
| **D**elete | жою | `DELETE` | постты өшіру |

## 2. Қосымшаның қабаттары

Егер SQL сұраныстары батырма өңдегіштерінің, `input()`-тардың арасына шашылып жатса — кодты оқу, түзету, тестілеу қиын. Сондықтан қосымшаны **қабаттарға** бөлеміз:

```text
┌────────────────────────────────────────┐
│  Интерфейс (UI)                         │  консоль мәзірі / Tkinter терезесі
│  input(), print(), Button, Entry        │  ← пайдаланушымен сөйлеседі, SQL білмейді
├────────────────────────────────────────┤
│  Логика (тексеру, ережелер)             │  валидация: аты бос емес, балл 0..100
├────────────────────────────────────────┤
│  Деректерге қол жеткізу (репозиторий)   │  StudentRepository: add, get, update, delete
│  sqlite3, SQL сұраныстары               │  ← тек осы жерде SQL бар
├────────────────────────────────────────┤
│  Деректер қоры (school.db)              │
└────────────────────────────────────────┘
```

**Аналогия:** мейрамхана. Даяшы (интерфейс) тапсырысты қабылдайды, бірақ тамақ пісірмейді. Аспаз (логика + репозиторий) асхана ішінде жұмыс істейді. Қойма (деректер қоры) — өнімдер сақталатын жер. Келуші қоймаға кірмейді — бәрі даяшы арқылы.

Пайдасы:
- интерфейсті ауыстыру оңай: консольдан Tkinter-ге көшсек, репозиторий **өзгермейді**;
- SQL бір жерде — қатені табу және түзету жеңіл;
- репозиторийді интерфейссіз **тестілеуге** болады.

## 3. Репозиторий класы

**Репозиторий** — бір кестемен (немесе мәнмен) жұмыс істейтін барлық SQL сұраныстарын бір жерге жинайтын класс.

```python
import sqlite3


class StudentRepository:
    """students кестесімен CRUD амалдарын орындайтын класс."""

    def __init__(self, db_path="students.db"):
        self.conn = sqlite3.connect(db_path)
        self.conn.row_factory = sqlite3.Row
        self.conn.execute("PRAGMA foreign_keys = ON")
        self._create_table()

    def _create_table(self):
        self.conn.execute("""
            CREATE TABLE IF NOT EXISTS students (
                id INTEGER PRIMARY KEY,
                name TEXT NOT NULL,
                class TEXT NOT NULL,
                score INTEGER CHECK (score BETWEEN 0 AND 100),
                email TEXT UNIQUE
            )
        """)
        self.conn.commit()

    # ---------- C: Create ----------
    def add(self, name, cls, score=None, email=None):
        """Жаңа оқушы қосады, оның id-ін қайтарады."""
        cur = self.conn.execute(
            "INSERT INTO students (name, class, score, email) VALUES (?, ?, ?, ?)",
            (name, cls, score, email),
        )
        self.conn.commit()
        return cur.lastrowid

    # ---------- R: Read ----------
    def get(self, student_id):
        """id бойынша бір оқушыны (сөздік) немесе None қайтарады."""
        row = self.conn.execute("SELECT * FROM students WHERE id = ?", (student_id,)).fetchone()
        return dict(row) if row else None

    def list_all(self, order_by="name"):
        """Барлық оқушылар тізімі. Сұрыптау өрісі тек рұқсат етілгендерден."""
        if order_by not in ("id", "name", "class", "score"):
            order_by = "name"
        rows = self.conn.execute(f"SELECT * FROM students ORDER BY {order_by}")
        return [dict(r) for r in rows]

    def search(self, text):
        """Аты бойынша іздеу (бөлігі бойынша)."""
        rows = self.conn.execute("SELECT * FROM students WHERE name LIKE ? ORDER BY name", (f"%{text}%",))
        return [dict(r) for r in rows]

    # ---------- U: Update ----------
    def update(self, student_id, **fields):
        """Көрсетілген өрістерді өзгертеді. True — өзгерді, False — оқушы жоқ."""
        allowed = {"name", "class", "score", "email"}
        fields = {k: v for k, v in fields.items() if k in allowed}
        if not fields:
            return False
        assignments = ", ".join(f"{k} = ?" for k in fields)
        cur = self.conn.execute(
            f"UPDATE students SET {assignments} WHERE id = ?",
            (*fields.values(), student_id),
        )
        self.conn.commit()
        return cur.rowcount == 1

    # ---------- D: Delete ----------
    def delete(self, student_id):
        """Оқушыны жояды. True — жойылды, False — табылмады."""
        cur = self.conn.execute("DELETE FROM students WHERE id = ?", (student_id,))
        self.conn.commit()
        return cur.rowcount == 1

    def count(self):
        return self.conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]

    def close(self):
        self.conn.close()


repo = StudentRepository(":memory:")
a = repo.add("Айгерім", "9А", 95, "aigerim@mail.kz")
b = repo.add("Бауыржан", "9А", 78)
c = repo.add("Дәулет", "9Ә", 88)
print("Қосылды:", a, b, c, "| барлығы:", repo.count())
print("Оқу:", repo.get(a))
print("Өзгерту:", repo.update(b, score=82, **{"class": "9Ә"}))
print("Жаңа мәндер:", repo.get(b)["class"], repo.get(b)["score"])
print("Іздеу «ау»:", [s["name"] for s in repo.search("ау")])
print("Жою:", repo.delete(c), "| қайта жою:", repo.delete(c))
print("Балл бойынша:", [(s["name"], s["score"]) for s in repo.list_all("score")])
repo.close()
```

Нәтижесі:

```text
Қосылды: 1 2 3 | барлығы: 3
Оқу: {'id': 1, 'name': 'Айгерім', 'class': '9А', 'score': 95, 'email': 'aigerim@mail.kz'}
Өзгерту: True
Жаңа мәндер: 9Ә 82
Іздеу «ау»: ['Бауыржан']
Жою: True | қайта жою: False
Балл бойынша: [('Бауыржан', 82), ('Айгерім', 95)]
```

Назар аудар:
- `class` — Python-да кілт сөз, сондықтан аргумент ретінде `**{"class": "9Ә"}` түрінде бердік (немесе әдісте `cls` деп атауға болады);
- `ORDER BY` және `SET` бөліктерінде өріс атаулары f-жол арқылы қойылды, бірақ **тек рұқсат етілген тізімнен** (`allowed`) — пайдаланушы мәтіні SQL-ға ешқашан тікелей түспейді;
- мәндер әрқашан `?` арқылы.

## 4. Валидация және қателерді өңдеу

ДҚ шектеулері (`NOT NULL`, `CHECK`, `UNIQUE`) — соңғы қорғаныс шебі. Бірақ пайдаланушыға `CHECK constraint failed: score BETWEEN 0 AND 100` деп көрсету — жаман. Сондықтан деректерді **алдын ала** тексеріп, түсінікті хабарлама береміз.

```python
import sqlite3


class ValidationError(Exception):
    """Енгізілген деректер қате болғанда шығатын қате."""


def validate_student(name, cls, score):
    """Деректерді тексеріп, тазаланған мәндерді қайтарады."""
    name = name.strip()
    cls = cls.strip().upper()
    if not name:
        raise ValidationError("Аты бос болмауы керек")
    if len(name) > 50:
        raise ValidationError("Аты тым ұзын (50 таңбадан аспасын)")
    if not cls:
        raise ValidationError("Сыныбын көрсетіңіз")
    if score == "":
        return name, cls, None
    if not score.isdigit() or not 0 <= int(score) <= 100:
        raise ValidationError("Балл 0-ден 100-ге дейінгі бүтін сан болуы керек")
    return name, cls, int(score)


tests = [("  Айгерім ", "9а", "95"), ("", "9А", "80"), ("Ерлан", "10А", "150"), ("Сымбат", "9Ә", "")]
for raw in tests:
    try:
        print("OK:", validate_student(*raw))
    except ValidationError as e:
        print("Қате:", e)

# ДҚ қателерін «аудару»
conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE s (email TEXT UNIQUE)")
conn.execute("INSERT INTO s VALUES ('a@mail.kz')")
try:
    conn.execute("INSERT INTO s VALUES ('a@mail.kz')")
except sqlite3.IntegrityError as e:
    message = "Бұл email бұрын тіркелген" if "UNIQUE" in str(e) else "Деректер қорының қатесі"
    print("Пайдаланушыға:", message)
conn.close()
```

Нәтижесі:

```text
OK: ('Айгерім', '9А', 95)
Қате: Аты бос болмауы керек
Қате: Балл 0-ден 100-ге дейінгі бүтін сан болуы керек
OK: ('Сымбат', '9Ә', None)
Пайдаланушыға: Бұл email бұрын тіркелген
```

`ValidationError` — өз қате класымыз (`Exception`-нан мұрагерлік — II модуль). Ол интерфейске «бұл пайдаланушының қатесі, оған көрсет» деген белгі береді.

## 5. Консольдық CRUD қосымшасы

Репозиторийді консольдық мәзірге қосамыз. Бұл файлда **бірде-бір SQL жолы жоқ** екеніне назар аудар:

```python
# app_console.py — интерфейс қабаты.
# repo.py файлында 3-бөлімдегі StudentRepository және 4-бөлімдегі
# ValidationError, validate_student жазылған деп есептейміз.
from repo import StudentRepository, ValidationError, validate_student

MENU = """
=== Оқушылар базасы ===
1. Барлық оқушылар
2. Оқушы қосу
3. Іздеу
4. Өзгерту
5. Жою
0. Шығу"""


def print_table(students):
    if not students:
        print("Тізім бос")
        return
    print(f"{'id':<4}{'Аты':<15}{'Сыныбы':<8}{'Балл':<5}")
    for s in students:
        score = "-" if s["score"] is None else s["score"]
        print(f"{s['id']:<4}{s['name']:<15}{s['class']:<8}{score:<5}")


def ask_id(prompt="Оқушы id: "):
    text = input(prompt).strip()
    return int(text) if text.isdigit() else None


def main(repo):
    while True:
        print(MENU)
        choice = input("Таңдау: ").strip()
        if choice == "0":
            break
        elif choice == "1":
            print_table(repo.list_all())
        elif choice == "2":
            try:
                name, cls, score = validate_student(input("Аты: "), input("Сыныбы: "), input("Балл: "))
                new_id = repo.add(name, cls, score)
                print(f"Қосылды, id = {new_id}")
            except ValidationError as e:
                print("Қате:", e)
        elif choice == "3":
            print_table(repo.search(input("Атының бөлігі: ")))
        elif choice == "4":
            student_id = ask_id()
            student = repo.get(student_id) if student_id else None
            if student is None:
                print("Мұндай оқушы жоқ")
                continue
            print("Бос қалдырсаң — өзгермейді")
            name = input(f"Аты [{student['name']}]: ") or student["name"]
            cls = input(f"Сыныбы [{student['class']}]: ") or student["class"]
            score = input(f"Балл [{student['score']}]: ") or str(student["score"] or "")
            try:
                name, cls, score = validate_student(name, cls, score)
                repo.update(student_id, name=name, score=score, **{"class": cls})
                print("Сақталды")
            except ValidationError as e:
                print("Қате:", e)
        elif choice == "5":
            student_id = ask_id()
            student = repo.get(student_id) if student_id else None
            if student and input(f"{student['name']} жойылсын ба? (иә/жоқ): ") == "иә":
                repo.delete(student_id)
                print("Жойылды")
            elif student is None:
                print("Мұндай оқушы жоқ")
        else:
            print("Мәзірден таңда")

if __name__ == "__main__":
    repository = StudentRepository("students.db")
    main(repository)
    repository.close()
```

Жұмыс мысалы:

```text
=== Оқушылар базасы ===
...
Таңдау: 2
Аты: Мадина
Сыныбы: 9ә
Балл: 91
Қосылды, id = 4
Таңдау: 1
id  Аты            Сыныбы  Балл
1   Айгерім        9А      95
4   Мадина         9Ә      91
```

## 6. Сол репозиторий — Tkinter терезесінде

Интерфейсті ауыстырамыз, ал репозиторий сол күйінде қалады. `ttk.Treeview` — кесте түріндегі тізім виджеті:

```python
import sqlite3
import tkinter as tk
from tkinter import messagebox, ttk


class MiniRepo:
    """Мысал үшін шағын репозиторий (нақты жобада — StudentRepository)."""

    def __init__(self):
        self.conn = sqlite3.connect("students_gui.db")
        self.conn.execute("CREATE TABLE IF NOT EXISTS students "
                          "(id INTEGER PRIMARY KEY, name TEXT NOT NULL, class TEXT NOT NULL)")

    def add(self, name, cls):
        self.conn.execute("INSERT INTO students (name, class) VALUES (?, ?)", (name, cls))
        self.conn.commit()

    def list_all(self):
        return self.conn.execute("SELECT id, name, class FROM students ORDER BY name").fetchall()

    def delete(self, student_id):
        self.conn.execute("DELETE FROM students WHERE id = ?", (student_id,))
        self.conn.commit()


repo = MiniRepo()
root = tk.Tk()
root.title("Оқушылар базасы")

form = tk.Frame(root)
form.pack(padx=10, pady=10, fill="x")
tk.Label(form, text="Аты:").grid(row=0, column=0)
name_entry = tk.Entry(form)
name_entry.grid(row=0, column=1)
tk.Label(form, text="Сыныбы:").grid(row=0, column=2)
class_entry = tk.Entry(form, width=6)
class_entry.grid(row=0, column=3)

tree = ttk.Treeview(root, columns=("id", "name", "class"), show="headings", height=8)
for col, title in (("id", "id"), ("name", "Аты"), ("class", "Сыныбы")):
    tree.heading(col, text=title)
tree.pack(padx=10, fill="both", expand=True)


def refresh():
    tree.delete(*tree.get_children())
    for row in repo.list_all():
        tree.insert("", "end", values=row)


def on_add():
    name, cls = name_entry.get().strip(), class_entry.get().strip()
    if not name or not cls:
        messagebox.showwarning("Қате", "Аты мен сыныбын толтырыңыз")
        return
    repo.add(name, cls)
    name_entry.delete(0, "end")
    refresh()


def on_delete():
    selected = tree.selection()
    if selected and messagebox.askyesno("Растау", "Таңдалған оқушы жойылсын ба?"):
        student_id = tree.item(selected[0])["values"][0]
        repo.delete(student_id)
        refresh()


tk.Button(form, text="Қосу", command=on_add).grid(row=0, column=4, padx=5)
tk.Button(root, text="Таңдалғанды жою", command=on_delete).pack(pady=10)
refresh()
root.mainloop()
```

Мұнда да: батырма → интерфейс функциясы → репозиторий әдісі → SQL. Интерфейс функциялары (`on_add`, `on_delete`) SQL-ды білмейді.

## 7. Репозиторийді тестілеу

Қабаттарға бөлудің тағы бір пайдасы — репозиторийді **автоматты** тексеруге болады. `:memory:` деректер қоры әр тест үшін таза қор береді:

```python
import sqlite3


class TaskRepository:
    """To-Do тапсырмалары үшін шағын репозиторий."""

    def __init__(self, path=":memory:"):
        self.conn = sqlite3.connect(path)
        self.conn.execute("CREATE TABLE IF NOT EXISTS tasks "
                          "(id INTEGER PRIMARY KEY, title TEXT NOT NULL, done INTEGER DEFAULT 0)")

    def add(self, title):
        cur = self.conn.execute("INSERT INTO tasks (title) VALUES (?)", (title,))
        self.conn.commit()
        return cur.lastrowid

    def mark_done(self, task_id):
        cur = self.conn.execute("UPDATE tasks SET done = 1 WHERE id = ?", (task_id,))
        self.conn.commit()
        return cur.rowcount == 1

    def pending(self):
        return [r[0] for r in self.conn.execute("SELECT title FROM tasks WHERE done = 0 ORDER BY id")]


def test_add_and_pending():
    repo = TaskRepository()
    repo.add("Үй тапсырмасы")
    repo.add("Жоба README")
    assert repo.pending() == ["Үй тапсырмасы", "Жоба README"]


def test_mark_done():
    repo = TaskRepository()
    task_id = repo.add("Git push")
    assert repo.mark_done(task_id) is True
    assert repo.pending() == []
    assert repo.mark_done(999) is False


for test in (test_add_and_pending, test_mark_done):
    test()
    print(f"{test.__name__}: өтті")
```

Нәтижесі:

```text
test_add_and_pending: өтті
test_mark_done: өтті
```

`assert шарт` — шарт жалған болса, `AssertionError` шығарады. Кәсіби жобаларда мұндай тесттерді `pytest` құралы іске қосады.

## Жиі кездесетін қателер

**1. SQL интерфейс кодының ішінде шашылған.** Батырма өңдегішінде `conn.execute("INSERT ...")` — кейін түзету қиын. Дұрыс: барлық SQL — репозиторийде.

**2. Әр амалда жаңа байланыс ашып, жаппау.** Ондаған ашық байланыс → `database is locked`. Дұрыс: бір байланыс (репозиторий объектісінде), бағдарлама соңында `close()`.

**3. `commit()` тек бағдарлама соңында.** Бағдарлама құласа — барлық өзгерістер жоғалады. Дұрыс: әр өзгертетін амалдан кейін `commit()`.

**4. Өріс атауын пайдаланушыдан тікелей SQL-ға қою.**

```python
# ҚАУІПТІ: order_by пайдаланушыдан келді
# conn.execute(f"SELECT * FROM students ORDER BY {order_by}")
# ДҰРЫС: рұқсат етілген тізіммен тексеру
order_by = "score; DROP TABLE students"
if order_by not in ("id", "name", "class", "score"):
    order_by = "name"
print("Қолданылатын сұрыптау:", order_by)
```

Нәтижесі:

```text
Қолданылатын сұрыптау: name
```

**5. `input()` нәтижесін тексермей ДҚ-ға жазу.** `int("он")` → `ValueError` және бағдарлама құлайды. Дұрыс: валидация функциясы.

**6. `None` пен бос жолды шатастыру.** Балл енгізілмесе, ДҚ-ға `""` емес, `None` (`NULL`) жазу керек — әйтпесе `AVG` пен `CHECK` дұрыс жұмыс істемейді.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| CRUD | Create, Read, Update, Delete — деректермен жұмыстың 4 негізгі амалы |
| Қабат (layer) | Қосымшаның бір міндетке жауапты бөлігі: интерфейс, логика, деректер |
| Репозиторий | Бір кестемен жұмыстың барлық SQL сұраныстарын жинайтын класс |
| Валидация | Енгізілген деректердің дұрыстығын тексеру |
| Өз қате класы | `Exception`-нан мұрагер класс (`ValidationError`) |
| `ttk.Treeview` | Tkinter-дегі кесте/тізім виджеті |
| `assert` | Шартты тексеретін оператор (тестілеуде) |
| `:memory:` | Тесттерге ыңғайлы уақытша деректер қоры |

## Ескерту

- ДҚ-мен қосылымды әр әдісте қайта қосылмай, қосымшаның бүкіл жұмыс уақытына бір рет ашқан (репозиторийде) және шығу кезінде жапқан дұрыс.
- `with conn:` транзакцияны бекітеді немесе кері қайтарады, бірақ қосылымды **жаппайды**; жабу үшін `conn.close()` немесе `contextlib.closing(conn)` қолданыңыз.
- `conn.row_factory = sqlite3.Row` өрістерге атауы бойынша жүгінуге (`row["title"]`) және жолды сөздікке оңай айналдыруға мүмкіндік береді: `dict(row)`.
- Тесттер үшін `":memory:"` қорын қолданыңыз — ол әр тест үшін қайта құрылады және жұмыс деректерін бүлдірмейді; `unittest` модулі немесе `pytest` кітапханасы мұндай тесттерді автоматты түрде іске қосады.
- Жұмыс кодында `assert` валидацияға жарамайды: `python -O` арқылы іске қосқанда барлық `assert` өшіріледі. Пайдаланушы деректерін тексеру үшін `ValueError` лақтырыңыз.
- Құжаттама: https://docs.python.org/3/library/sqlite3.html және https://docs.python.org/3/library/tkinter.ttk.html#treeview
