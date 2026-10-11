# Сабақ 61. Практикалық тапсырмалар: CSV экспорт және сақтық көшірме

> Барлық тапсырмада теориядағы `school.db` (кесте `students`: id, name, grade, city, score) қолданылады. Алдымен теориядағы «Деректер қоры дайын» кодын іске қосыңыз.

## Базалық деңгей

### 1-тапсырма. Толық экспорт
`students` кестесін `students_export.csv` файлына экспорттаңыз. Бөлгіш — `;`, кодировка — `utf-8-sig`, бірінші жол — баған атаулары. Файлды Excel/Calc-та ашып, қазақ әріптерінің дұрыс көрінетінін тексеріңіз.

Шығыс мысалы:
```text
4 жол students_export.csv файлына жазылды
```

### 2-тапсырма. Сыныптар бойынша экспорт
Пайдаланушыдан сынып нөмірін сұрап (`input`), тек сол сынып оқушыларын `grade_<N>.csv` файлына жазыңыз. Оқушы табылмаса, файл жасамай, хабар шығарыңыз.

```text
Сынып нөмірі: 9
2 оқушы grade_9.csv файлына сақталды
```
```text
Сынып нөмірі: 7
7-сыныпта оқушы жоқ
```

### 3-тапсырма. Уақыт белгісі бар көшірме
`make_backup(db_path)` функциясын жазыңыз: `backups/` папкасын жасап (жоқ болса), `school_ГГГГ-АА-КК_СС-ММ-СС.db` атаулы көшірме жасайды және жолын қайтарады. `Connection.backup()` тәсілін қолданыңыз.

### 4-тапсырма. Көшірмені тексеру
`check_backup(path)` функциясы көшірмені ашып, `PRAGMA integrity_check` нәтижесін және `students` кестесіндегі жол санын басып шығарсын.

```text
backups/school_2027-01-28_15-42-07.db: тұтастығы — ok, жазба саны — 4
```

### 5-тапсырма. SQL-дамп
Қорды `school_dump.sql` файлына сақтаңыз. Содан кейін дамптан `restored.db` қорын құрып, ондағы жазба санын шығарыңыз.

## Кеңейтілген деңгей

### 6-тапсырма. Валидациясы бар импорт
`new_students.csv` файлын өзіңіз жасаңыз (кемінде 5 жол, оның 2-еуі қате: баға орнында мәтін, аты бос). Импорт функциясы дұрыс жолдарды қосып, қателерін өткізіп жіберсін және есеп берсін:

```text
Қосылды: 3
Өткізілді: 2 (жолдар: 3, 5)
```

### 7-тапсырма. Ротация
`make_backup` функциясын 6 рет шақырып (арасына `time.sleep(1)`), содан кейін `rotate_backups(keep=3)` көмегімен тек соңғы 3 көшірмені қалдырыңыз. Қалған файлдар тізімін экранға шығарыңыз.

### 8-тапсырма. Барлық кестені экспорттау
Қорда бірнеше кесте болуы мүмкін. `sqlite_master` кестесінен барлық кесте атауларын алып (`SELECT name FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%'`), әр кестені `export/<кесте>.csv` файлына жазыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. «Уақыт машинасы»
Консольдік мәзір жасаңыз:
```text
1 — Көшірме жасау
2 — Көшірмелер тізімі (күні, өлшемі КБ, жазба саны)
3 — Таңдалған көшірмеден қалпына келтіру
0 — Шығу
```
Қалпына келтірер алдында ағымдағы қордың өзінің де көшірмесін автоматты түрде жасаңыз («қауіпсіздік торы»).

### 10-тапсырма. ZIP-архив
Қорды, оның CSV экспортын және SQL-дампын бір `zipfile.ZipFile` архивіне (`backup_ГГГГ-АА-КК.zip`) салатын функция жазыңыз. Архив өлшемін және түпнұсқа файлдар өлшемін салыстырыңыз.

## Үй тапсырмасы

1. Өзіңіздің 60-сабақтағы CRUD қосымшаңызға «Экспорт CSV» және «Backup» пункттерін қосыңыз.
2. Өз компьютеріңіздегі маңызды файлдар (жобалар, фото) үшін 3-2-1 ережесі бойынша жоспар жазыңыз: не, қайда, қаншалықты жиі.
3. Келесі сабаққа дайындық: Tkinter-дегі `Entry`, `Button`, `ttk.Treeview` виджеттерін қайталаңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import csv
import sqlite3

with sqlite3.connect("school.db") as conn:
    cur = conn.execute("SELECT * FROM students")
    headers = [c[0] for c in cur.description]
    rows = cur.fetchall()

with open("students_export.csv", "w", newline="", encoding="utf-8-sig") as f:
    w = csv.writer(f, delimiter=";")
    w.writerow(headers)
    w.writerows(rows)
print(f"{len(rows)} жол students_export.csv файлына жазылды")
```

**2-тапсырма**
```python
import csv
import sqlite3

grade = int(input("Сынып нөмірі: "))
with sqlite3.connect("school.db") as conn:
    cur = conn.execute("SELECT name, city, score FROM students WHERE grade = ?", (grade,))
    headers = [c[0] for c in cur.description]
    rows = cur.fetchall()

if not rows:
    print(f"{grade}-сыныпта оқушы жоқ")
else:
    path = f"grade_{grade}.csv"
    with open(path, "w", newline="", encoding="utf-8-sig") as f:
        w = csv.writer(f, delimiter=";")
        w.writerow(headers)
        w.writerows(rows)
    print(f"{len(rows)} оқушы {path} файлына сақталды")
```

**3–4-тапсырмалар**
```python
import sqlite3
from datetime import datetime
from pathlib import Path

def make_backup(db_path, backup_dir="backups"):
    Path(backup_dir).mkdir(exist_ok=True)
    stamp = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
    target = Path(backup_dir) / f"{Path(db_path).stem}_{stamp}.db"
    src = sqlite3.connect(db_path)
    dst = sqlite3.connect(target)
    with dst:
        src.backup(dst)
    dst.close()
    src.close()
    return target

def check_backup(path):
    with sqlite3.connect(path) as conn:
        ok = conn.execute("PRAGMA integrity_check").fetchone()[0]
        n = conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]
    print(f"{path}: тұтастығы — {ok}, жазба саны — {n}")

b = make_backup("school.db")
check_backup(b)
```

**5-тапсырма**
```python
import sqlite3
from pathlib import Path

with sqlite3.connect("school.db") as conn, open("school_dump.sql", "w", encoding="utf-8") as f:
    for line in conn.iterdump():
        f.write(line + "\n")

Path("restored.db").unlink(missing_ok=True)   # қайта іске қосқанда кесте қайталанбауы үшін
with sqlite3.connect("restored.db") as conn:
    conn.executescript(Path("school_dump.sql").read_text(encoding="utf-8"))
    n = conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]
print("Қалпына келтірілген жазбалар:", n)
```

**6-тапсырма** (`new_students.csv` үлгісі: `name;grade;city;score`)
```python
import csv
import sqlite3

good, skipped = [], []
with open("new_students.csv", encoding="utf-8-sig", newline="") as f:
    reader = csv.DictReader(f, delimiter=";")
    for line_no, r in enumerate(reader, start=2):   # 1-жол — тақырып
        try:
            name = r["name"].strip()
            if not name:
                raise ValueError("аты бос")
            good.append((name, int(r["grade"]), r["city"].strip(), float(r["score"])))
        except (ValueError, KeyError, AttributeError):
            skipped.append(line_no)

with sqlite3.connect("school.db") as conn:
    conn.executemany("INSERT INTO students (name, grade, city, score) VALUES (?, ?, ?, ?)", good)

print("Қосылды:", len(good))
print(f"Өткізілді: {len(skipped)} (жолдар: {', '.join(map(str, skipped))})")
```

**7-тапсырма** (`make_backup` 3-тапсырмадан)
```python
import time
from pathlib import Path

def rotate_backups(backup_dir="backups", keep=3):
    files = sorted(Path(backup_dir).glob("*.db"))
    for f in files[:-keep]:
        f.unlink()

# for _ in range(6):
#     make_backup("school.db")
#     time.sleep(1)
rotate_backups(keep=3)
for f in sorted(Path("backups").glob("*.db")):
    print(f.name)
```

**8-тапсырма**
```python
import csv
import sqlite3
from pathlib import Path

Path("export").mkdir(exist_ok=True)
with sqlite3.connect("school.db") as conn:
    tables = [r[0] for r in conn.execute(
        "SELECT name FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%'")]
    for t in tables:
        cur = conn.execute(f'SELECT * FROM "{t}"')
        with open(f"export/{t}.csv", "w", newline="", encoding="utf-8-sig") as f:
            w = csv.writer(f, delimiter=";")
            w.writerow([c[0] for c in cur.description])
            w.writerows(cur)
        print("Экспортталды:", t)
```

**9-тапсырма** (қысқартылған үлгі)
```python
import sqlite3
from datetime import datetime
from pathlib import Path

DB = "school.db"
BK = Path("backups")

def backup(src_path, dst_path):
    src, dst = sqlite3.connect(src_path), sqlite3.connect(dst_path)
    with dst:
        src.backup(dst)
    src.close()
    dst.close()

def make_backup():
    BK.mkdir(exist_ok=True)
    target = BK / f"school_{datetime.now():%Y-%m-%d_%H-%M-%S}.db"
    backup(DB, target)
    return target

def list_backups():
    files = sorted(BK.glob("*.db"))
    for i, f in enumerate(files, 1):
        with sqlite3.connect(f) as c:
            n = c.execute("SELECT COUNT(*) FROM students").fetchone()[0]
        print(f"{i}. {f.name}  {f.stat().st_size / 1024:.1f} КБ  {n} жазба")
    return files

while True:
    choice = input("1-көшірме, 2-тізім, 3-қалпына келтіру, 0-шығу: ")
    if choice == "1":
        print("Жасалды:", make_backup())
    elif choice == "2":
        list_backups()
    elif choice == "3":
        files = list_backups()
        k = int(input("Нөмірі: "))
        safety = make_backup()
        print("Қауіпсіздік көшірмесі:", safety)
        backup(files[k - 1], DB)
        print("Қалпына келтірілді")
    elif choice == "0":
        break
```

**10-тапсырма**
```python
import zipfile
from datetime import date
from pathlib import Path

files = ["school.db", "students_export.csv", "school_dump.sql"]
name = f"backup_{date.today():%Y-%m-%d}.zip"
with zipfile.ZipFile(name, "w", compression=zipfile.ZIP_DEFLATED) as z:
    for f in files:
        z.write(f)
total = sum(Path(f).stat().st_size for f in files)
print(f"Түпнұсқалар: {total} байт, архив: {Path(name).stat().st_size} байт")
```

</details>
