# Сабақ 63. Практикум: «Оқушылар базасы» қосымшасын аяқтау және қорғау. Өзіндік жұмыс №4.

## Тақырыптар

1. Таңдалған жолмен жұмыс
2. `StudentDB` класын кеңейту
3. Қауіпті әрекетті растау
4. Экспорт және backup батырмалары
5. Бағдарламаны тестілеу чек-парағы
6. Жобаны рәсімдеу: құрылым және README
7. Қысқаша анықтамалық: CSV, JSON, SQLite
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Таңдалған жолмен жұмыс

Өзгерту мен өшіру үшін алдымен пайдаланушы кестеде қай оқушыны таңдағанын білу керек:

```python
def get_selected_id(tree):
    """Таңдалған оқушының ID-ін немесе None қайтарады."""
    selected = tree.selection()          # таңдалған жолдардың iid кортежі
    if not selected:
        return None
    values = tree.item(selected[0], "values")
    return int(values[0])                # 0-баған — ID
```

Таңдау оқиғасын тыңдау — жолды басқанда формаға деректер жүктеледі:

```python
# self.tree.bind("<<TreeviewSelect>>", self.on_select)
#
# def on_select(self, event):
#     sel = self.tree.selection()
#     if not sel:
#         return
#     values = self.tree.item(sel[0], "values")
#     self.clear_form()
#     for key, value in zip(("name", "grade", "city", "phone", "score"), values[1:]):
#         self.entries[key].insert(0, value)
```

## 2. `StudentDB` класын кеңейту

```python
import sqlite3


class StudentDB:
    def __init__(self, path="students.db"):
        self.conn = sqlite3.connect(path)
        self.conn.execute("""CREATE TABLE IF NOT EXISTS students (
            id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT NOT NULL,
            grade INTEGER NOT NULL, city TEXT, phone TEXT, score REAL)""")

    def add(self, name, grade, city, phone, score):
        with self.conn:
            return self.conn.execute(
                "INSERT INTO students (name, grade, city, phone, score) VALUES (?, ?, ?, ?, ?)",
                (name, grade, city, phone, score)).lastrowid

    def update(self, sid, name, grade, city, phone, score):
        with self.conn:
            cur = self.conn.execute(
                "UPDATE students SET name=?, grade=?, city=?, phone=?, score=? WHERE id=?",
                (name, grade, city, phone, score, sid))
        return cur.rowcount            # өзгерген жол саны

    def delete(self, sid):
        with self.conn:
            return self.conn.execute("DELETE FROM students WHERE id=?", (sid,)).rowcount

    def search(self, text):
        pattern = f"%{text}%"
        return self.conn.execute(
            "SELECT id, name, grade, city, phone, score FROM students "
            "WHERE name LIKE ? OR city LIKE ? ORDER BY name",
            (pattern, pattern)).fetchall()

    def stats(self):
        return self.conn.execute(
            "SELECT COUNT(*), ROUND(AVG(score), 1), MAX(score) FROM students").fetchone()


db = StudentDB(":memory:")
a = db.add("Айгерім", 9, "Алматы", "", 92.5)
db.add("Нұрлан", 10, "Астана", "", 78)
db.update(a, "Айгерім Сәрсенова", 9, "Алматы", "", 94)
print(db.search("Алм"))
print(db.search("алм"))
print(db.stats())
print("Өшірілді:", db.delete(a))
```

Нәтижесі:
```text
[(1, 'Айгерім Сәрсенова', 9, 'Алматы', '', 94.0)]
[]
(2, 86.0, 94.0)
Өшірілді: 1
```

**Ескерту:** SQLite-та `LIKE` латын әріптері үшін регистрге сезімсіз, бірақ кириллица үшін — **сезімтал**. Сондықтан «Алм» іздеуі «Алматы»-ны тапты, ал «алм» — бос тізім қайтарды. Пайдаланушы кіші әріппен жазуы әбден мүмкін. Шешімі: Python-да `lower()` арқылы салыстыру немесе өз функциясын тіркеу:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.create_function("PYLOWER", 1, lambda s: s.lower() if s else s)
conn.execute("CREATE TABLE t (name TEXT)")
conn.execute("INSERT INTO t VALUES ('Алматы')")
print(conn.execute("SELECT name FROM t WHERE PYLOWER(name) LIKE ?", ("%алм%",)).fetchall())
```

Нәтижесі:
```text
[('Алматы',)]
```

Сондықтан қосымшада `search` әдісінде `WHERE PYLOWER(name) LIKE ? OR PYLOWER(city) LIKE ?` жазып, `pattern = f"%{text.lower()}%"` береміз.

## 3. Қауіпті әрекетті растау

Өшіру — қайтымсыз әрекет. Пайдаланушыдан міндетті түрде сұраймыз:

```python
# from tkinter import messagebox
# if messagebox.askyesno("Растау", f"«{name}» оқушысын өшіру керек пе?"):
#     self.db.delete(sid)
#     self.refresh()
```

**UX ережесі:** қайтымсыз әрекет алдында — растау; әрекеттен кейін — нәтиже туралы хабар (статус жолында).

## 4. Экспорт және backup батырмалары

`filedialog` пайдаланушыға файлды қайда сақтайтынын таңдауға мүмкіндік береді:

```python
# from tkinter import filedialog
# path = filedialog.asksaveasfilename(defaultextension=".csv",
#                                     filetypes=[("CSV файлдар", "*.csv")],
#                                     initialfile="students.csv")
# if path:   # пайдаланушы «Болдырмау» баспаса
#     n = self.db.export_csv(path)
```

`StudentDB` класына `export_csv(path)` (CSV-ге экспорт) және `backup(folder)` (`Connection.backup()` арқылы сақтық көшірме) әдістері қосылады.

## 5. Бағдарламаны тестілеу чек-парағы

Қолмен тестілеу — бағдарламаны нақты сценарийлер бойынша тексеру. Әр сценарий үшін дұрыс нәтиже алдын ала жазылады:

| № | Сценарий | Дұрыс нәтиже |
|---|---|---|
| 1 | Барлық өрісті дұрыс толтырып «Қосу» | Кестеде жаңа жол, статус жаңарды |
| 2 | Аты бос | Қате хабары, қор өзгермеді |
| 3 | Сынып = 0 немесе «он» | Қате хабары |
| 4 | Ештеңе таңдамай «Өшіру» | «Алдымен оқушыны таңдаңыз» |
| 5 | Таңдап «Өшіру» → «Жоқ» | Ештеңе өшпеді |
| 6 | Таңдап, өзгертіп «Сақтау» | Кестеде өзгерді, қайта ашқанда да сақталды |
| 7 | Іздеу «алм» | Тек Алматы оқушылары |
| 8 | Іздеу өрісін тазалау | Барлық оқушы қайта көрінді |
| 9 | «CSV экспорт» | Файл Excel-де қазақша дұрыс ашылды |
| 10 | «Backup» | `backups/` ішінде уақыты бар файл пайда болды |

## 6. Жобаны рәсімдеу: құрылым және README

Дайын жоба басқа компьютерде оңай іске қосылуы керек. Типтік құрылым:

```
student_base/
├── app.py            # интерфейс (Tkinter)
├── db.py             # деректер қабаты (StudentDB класы)
├── requirements.txt  # сыртқы тәуелділіктер (болса)
├── README.md         # жоба сипаттамасы
├── .gitignore        # students.db, backups/, __pycache__/
└── backups/          # сақтық көшірмелер (Git-ке салынбайды)
```

Ең қарапайым `README.md` мыналарды қамтиды:

1. **Атауы мен мақсаты** — бағдарлама не істейтіні туралы бір-екі сөйлем.
2. **Мүмкіндіктері** — функциялар тізімі (қосу, іздеу, өзгерту, өшіру, экспорт, backup).
3. **Іске қосу** — Python нұсқасы және команда: `python app.py`.
4. **Құрылымы** — әр файл не үшін жауап береді.
5. Интерфейстің **скриншоты** (қаласаңыз).

Кіру нүктесі `__name__` тексерісі арқылы жазылады — сонда модульді терезе ашпай-ақ импорттауға болады:

```python
import tkinter as tk


def main():
    root = tk.Tk()
    root.title("Оқушылар базасы")
    root.mainloop()


if __name__ == "__main__":
    main()
```

## 7. Қысқаша анықтамалық: CSV, JSON, SQLite

```python
import csv
import json
import sqlite3

rows = [{"name": "Айгерім", "score": 95}, {"name": "Нұрлан", "score": 88}]

# CSV: жазу және оқу
with open("data.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "score"])
    writer.writeheader()
    writer.writerows(rows)
with open("data.csv", encoding="utf-8") as f:
    data = list(csv.DictReader(f))   # мәндер — мәтін (str)!

# JSON: жазу және оқу
with open("data.json", "w", encoding="utf-8") as f:
    json.dump(rows, f, ensure_ascii=False, indent=2)
with open("data.json", encoding="utf-8") as f:
    rows = json.load(f)

# SQLite: параметрлі CRUD
con = sqlite3.connect(":memory:")
con.execute("CREATE TABLE students (id INTEGER PRIMARY KEY, name TEXT, score INTEGER)")
con.executemany("INSERT INTO students (name, score) VALUES (?, ?)",
                [(r["name"], r["score"]) for r in rows])
con.execute("UPDATE students SET score = ? WHERE id = ?", (97, 1))
con.execute("DELETE FROM students WHERE score < ?", (50,))
for row in con.execute("SELECT name, score FROM students ORDER BY score DESC"):
    print(row)
con.commit()
con.close()
```

| SQL командасы | Мақсаты |
|---|---|
| `CREATE TABLE` | Кесте құру |
| `INSERT` / `SELECT` / `UPDATE` / `DELETE` | CRUD амалдары |
| `ORDER BY` | Сұрыптау |
| `GROUP BY` + `COUNT`/`AVG` | Топтау және агрегаттар |
| `JOIN ... ON` | Кестелерді кілт бойынша біріктіру |

## Жиі кездесетін қателер

**1. Таңдау тексерілмеген:**
```python
# Қате: ештеңе таңдалмаса IndexError
# values = self.tree.item(self.tree.selection()[0], "values")
# Дұрыс:
# sel = self.tree.selection()
# if not sel:
#     messagebox.showwarning("Ескерту", "Алдымен оқушыны таңдаңыз")
#     return
```

**2. `tree.item(...)["values"]` мәндері — мәтін немесе сан болуы мүмкін.** ID-ді әрқашан `int()` арқылы түрлендіріңіз.

**3. Өзгертуде `WHERE id=?` ұмытылған** — барлық оқушы бірдей атқа ие болады. Бұл — ең қауіпті қате! Сондықтан тестілеу алдында backup жасаңыз.

**4. `filedialog` «Болдырмау» басылғанда бос жол `""` қайтарады** — `open("")` қатесі шықпауы үшін `if path:` тексеру керек.

**5. Іздеуден кейін кесте «бос» қалып қояды** — іздеу өрісі бос болса, `get_all()` шақырылуы тиіс.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `tree.selection()` | Treeview-тегі таңдалған жолдар |
| `<<TreeviewSelect>>` | Жол таңдалғанда туындайтын виртуалды оқиға |
| `rowcount` | Соңғы `UPDATE`/`DELETE` өзгерткен жол саны |
| `create_function` | SQLite-қа Python функциясын тіркеу |
| `askyesno` | «Иә/Жоқ» растау терезесі |
| `filedialog` | Файлды ашу/сақтау сұхбат терезесі |
| Тестілеу чек-парағы | Тексерілетін сценарийлер тізімі |
| UX | Пайдаланушы тәжірибесі — қолданудың ыңғайлылығы |

## Ескерту

- `tree.selection()` мәндерді емес, жол идентификаторларының (iid) кортежін қайтарады; мәндер `tree.item(iid, "values")` арқылы алынады.
- `messagebox.askyesno()` `True`/`False` қайтарады; басқа нұсқалары — `askokcancel` және `askyesnocancel` (ол `None` да қайтаруы мүмкін) (https://docs.python.org/3/library/tkinter.messagebox.html).
- `filedialog.asksaveasfilename()` тек жолды қайтарады — файлдың өзін жасамайды; «Болдырмау» басылса, бос жол қайтарылады.
- `UPDATE`/`DELETE`-тен кейін `cursor.rowcount` нақты қанша жол өзгергенін көрсетеді — берілген `id`-мен жазба болғанын тексеруге ыңғайлы.
- `<<TreeviewSelect>>` оқиғасы бағдарлама таңдауды өзгерткенде де (`selection_set`) туындайды — шексіз циклге түспеу үшін мұны ескеріңіз.
