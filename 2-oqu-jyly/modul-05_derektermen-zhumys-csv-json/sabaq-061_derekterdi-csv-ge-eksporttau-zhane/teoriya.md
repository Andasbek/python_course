# Сабақ 61. Деректерді CSV-ге экспорттау және сақтық көшірме (backup) жасау.

## Тақырыптар

1. Неге CSV-ге экспорттаймыз?
2. SQLite → CSV: негізгі үлгі
3. CSV → SQLite: кері импорт
4. Сақтық көшірме (backup) деген не?
5. SQLite-та көшірме жасаудың үш тәсілі
6. Көшірмелердің ротациясы
7. Қалпына келтіруді тексеру
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Неге CSV-ге экспорттаймыз?

Деректер қоры — бағдарлама үшін ыңғайлы, бірақ адамдардың көбі `.db` файлын аша алмайды. CSV (Comma-Separated Values) — кез келген кестелік бағдарлама оқитын мәтіндік формат.

| Мақсат | Мысал |
|---|---|
| Есеп беру | Оқушылар тізімін Excel-де ашып, басып шығару |
| Басқа жүйеге тасымалдау | Деректерді Google Sheets-ке, Pandas-қа (VII модуль) беру |
| Қарапайым архив | Мәтіндік файл ондаған жылдан кейін де оқылады |
| Деректерді талдау | CSV → Pandas → Matplotlib графиктері |

## 2. SQLite → CSV: негізгі үлгі

Алдымен оқу деректер қорын жасайық:

```python
import sqlite3

conn = sqlite3.connect("school.db")
cur = conn.cursor()
cur.execute("""
    CREATE TABLE IF NOT EXISTS students (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        grade INTEGER,
        city TEXT,
        score REAL
    )
""")
cur.execute("DELETE FROM students")  # оқу мысалы үшін кестені тазалаймыз
data = [
    ("Айгерім Сәрсенова", 9, "Алматы", 92.5),
    ("Нұрлан Жақсылықов", 10, "Астана", 78.0),
    ("Дана Омарова", 9, "Шымкент", 88.0),
    ("Ержан Бекұлы", 11, "Алматы", 95.5),
]
cur.executemany("INSERT INTO students (name, grade, city, score) VALUES (?, ?, ?, ?)", data)
conn.commit()
conn.close()
print("Деректер қоры дайын")
```

Енді экспорттайық:

```python
import csv
import sqlite3

def export_to_csv(db_path, table, csv_path):
    """Кестенің барлық жолын CSV файлға жазады, жазылған жол санын қайтарады."""
    with sqlite3.connect(db_path) as conn:
        cur = conn.execute(f"SELECT * FROM {table}")
        headers = [col[0] for col in cur.description]  # баған атаулары
        rows = cur.fetchall()
    with open(csv_path, "w", newline="", encoding="utf-8-sig") as f:
        writer = csv.writer(f, delimiter=";")
        writer.writerow(headers)
        writer.writerows(rows)
    return len(rows)

count = export_to_csv("school.db", "students", "students.csv")
print(f"{count} жол экспортталды")
```

Нәтижесі:
```text
4 жол экспортталды
```

`students.csv` мазмұны:
```text
id;name;grade;city;score
1;Айгерім Сәрсенова;9;Алматы;92.5
2;Нұрлан Жақсылықов;10;Астана;78.0
3;Дана Омарова;9;Шымкент;88.0
4;Ержан Бекұлы;11;Алматы;95.5
```

### Маңызды егжей-тегжейлер

- **`cur.description`** — соңғы `SELECT` бағандары туралы кортеждер тізімі. Әр кортеждің 0-элементі — баған атауы. Сондықтан кесте құрылымы өзгерсе де код жұмыс істей береді.
- **`newline=""`** — Windows-та жолдар арасында бос жол пайда болмауы үшін міндетті.
- **`encoding="utf-8-sig"`** — файл басына BOM (Byte Order Mark) белгісін қосады. Excel осы белгіні көріп, қазақ әріптерін (ә, ғ, қ, ң, ө, ұ, ү, һ, і) дұрыс көрсетеді. Қарапайым `utf-8` болса, Excel кейде «кракозябра» шығарады.
- **`delimiter=";"`** — орыс/қазақ тілді Excel көбіне үтір емес, нүктелі үтірді бөлгіш деп күтеді (себебі бөлшек сандарда үтір қолданылады). Pandas немесе Google Sheets үшін әдепкі `","` жақсы.

### Сүзілген экспорт

Барлық кестені емес, тек қажет жолдарды экспорттауға болады:

```python
import csv
import sqlite3

def export_query(db_path, query, params, csv_path):
    with sqlite3.connect(db_path) as conn:
        cur = conn.execute(query, params)
        headers = [c[0] for c in cur.description]
        rows = cur.fetchall()
    with open(csv_path, "w", newline="", encoding="utf-8-sig") as f:
        writer = csv.writer(f)
        writer.writerow(headers)
        writer.writerows(rows)
    return len(rows)

n = export_query(
    "school.db",
    "SELECT name AS 'Аты-жөні', score AS 'Балл' FROM students WHERE city = ? ORDER BY score DESC",
    ("Алматы",),
    "almaty.csv",
)
print("Алматы оқушылары:", n)
```

Нәтижесі:
```text
Алматы оқушылары: 2
```

`AS` бүркеншік атауы CSV тақырыбына қазақша баған атауын береді.

### DictWriter және sqlite3.Row

```python
import csv
import sqlite3

with sqlite3.connect("school.db") as conn:
    conn.row_factory = sqlite3.Row          # жолдарды сөздік тәрізді етеді
    rows = conn.execute("SELECT name, grade FROM students").fetchall()

with open("names.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "grade"])
    writer.writeheader()
    for r in rows:
        writer.writerow(dict(r))
print("Дайын")
```

## 3. CSV → SQLite: кері импорт

Экспорттың «егізі» — импорт. Мысалы, жаңа оқушылар Excel-де енгізіліп, CSV файл ретінде берілді:

```python
import csv
import sqlite3

def import_from_csv(db_path, csv_path):
    added = 0
    with open(csv_path, encoding="utf-8-sig", newline="") as f:
        reader = csv.DictReader(f, delimiter=";")
        rows = [(r["name"], int(r["grade"]), r["city"], float(r["score"])) for r in reader]
    with sqlite3.connect(db_path) as conn:
        conn.executemany(
            "INSERT INTO students (name, grade, city, score) VALUES (?, ?, ?, ?)", rows
        )
        added = len(rows)
    return added
```

Ескерту: импорт кезінде **деректерді тексеру** (валидация) керек — бос жол, сан орнына мәтін т.б. Қате жол бүкіл импортты құлатпауы үшін `try/except ValueError` қолданамыз.

## 4. Сақтық көшірме (backup) деген не?

**Сақтық көшірме** — деректердің жоғалуы, бүлінуі немесе қате өзгертілуі жағдайында қалпына келтіру үшін жасалатын көшірмесі.

Деректер жоғалуының себептері: диск/флешка бұзылуы, қате команда (`DELETE` WHERE-сіз), вирус-шифрлаушы (ransomware), ноутбук ұрлануы, бағдарламадағы қате.

### 3-2-1 ережесі

| Сан | Мағынасы |
|---|---|
| **3** | деректердің кемінде 3 данасы (түпнұсқа + 2 көшірме) |
| **2** | кемінде 2 түрлі тасығышта (ноутбук диск + флешка) |
| **1** | 1 көшірме басқа жерде (бұлт: Google Drive, GitHub т.б.) |

## 5. SQLite-та көшірме жасаудың үш тәсілі

### 1-тәсіл. Файлды көшіру (`shutil.copy2`)

SQLite деректер қоры — жай ғана бір файл, сондықтан оны көшіруге болады:

```python
import shutil
from datetime import datetime
from pathlib import Path

def simple_backup(db_path, backup_dir="backups"):
    Path(backup_dir).mkdir(exist_ok=True)
    stamp = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
    target = Path(backup_dir) / f"{Path(db_path).stem}_{stamp}.db"
    shutil.copy2(db_path, target)  # copy2 өзгерту уақытын да сақтайды
    return target

print("Көшірме:", simple_backup("school.db"))
```

Нәтижесі (уақыт әр ретте басқа):
```text
Көшірме: backups/school_2027-01-28_15-42-07.db
```

**Кемшілігі:** егер дәл осы сәтте басқа бағдарлама қорға жазып жатса, көшірме бүлінуі мүмкін. Сондықтан көшірер алдында барлық қосылымды жабу керек.

### 2-тәсіл. `Connection.backup()` — ең сенімді

```python
import sqlite3

def safe_backup(db_path, target_path):
    src = sqlite3.connect(db_path)
    dst = sqlite3.connect(target_path)
    with dst:
        src.backup(dst)   # қор жұмыс істеп тұрса да дұрыс көшіреді
    dst.close()
    src.close()

safe_backup("school.db", "school_backup.db")
print("Қауіпсіз көшірме жасалды")
```

Бұл әдіс SQLite-тың өзінің «онлайн backup API»-ін пайдаланады: қосылым ашық болса да, көшірме тұтас (консистентті) болады.

### 3-тәсіл. SQL-дамп (`iterdump`)

```python
import sqlite3

with sqlite3.connect("school.db") as conn:
    with open("school_dump.sql", "w", encoding="utf-8") as f:
        for line in conn.iterdump():
            f.write(line + "\n")
print("SQL-дамп сақталды")
```

Дамп — `CREATE TABLE` және `INSERT` командаларынан тұратын мәтіндік файл. Артықшылығы: оны оқуға, Git-ке салуға, басқа ДҚБЖ-ға ауыстыруға болады. Қалпына келтіру:

```python
import sqlite3

with open("school_dump.sql", encoding="utf-8") as f:
    script = f.read()
with sqlite3.connect("restored.db") as conn:
    conn.executescript(script)
print("Қор дамптан қалпына келтірілді")
```

### Салыстыру

| Тәсіл | Артықшылығы | Кемшілігі |
|---|---|---|
| `shutil.copy2` | Өте қарапайым | Жазу кезінде бүлінуі мүмкін |
| `conn.backup()` | Сенімді, жылдам | Нәтижесі екілік файл |
| `iterdump()` | Адам оқиды, Git-ке ыңғайлы | Үлкен қорда баяу, көлемі үлкен |
| CSV экспорт | Excel-де ашылады | Тек деректер, құрылым (типтер, кілттер) сақталмайды |

## 6. Көшірмелердің ротациясы

Әр іске қосқанда көшірме жасасақ, папка тез толады. **Ротация** — тек соңғы N көшірмені қалдырып, ескілерін өшіру.

```python
from pathlib import Path

def rotate_backups(backup_dir="backups", keep=5):
    files = sorted(Path(backup_dir).glob("*.db"))   # атауда күн бар — алфавит бойынша сұрыпталады
    old = files[:-keep] if len(files) > keep else []
    for f in old:
        f.unlink()
    return len(old)

print("Өшірілді:", rotate_backups(keep=3))
```

Атаудағы күн форматы `ГГГГ-АА-КК_СС-ММ-СС` болғандықтан, мәтін ретімен сұрыптау уақыт ретімен сәйкес келеді — бұл ыңғайлы трюк.

## 7. Қалпына келтіруді тексеру

«Тексерілмеген көшірме — көшірме емес». Көшірме ашылатынын және жол саны дұрыс екенін тексерейік:

```python
import sqlite3

def verify_backup(path, table="students"):
    try:
        with sqlite3.connect(path) as conn:
            ok = conn.execute("PRAGMA integrity_check").fetchone()[0]
            count = conn.execute(f"SELECT COUNT(*) FROM {table}").fetchone()[0]
        return ok == "ok", count
    except sqlite3.DatabaseError:
        return False, 0

print(verify_backup("school_backup.db"))
```

Нәтижесі:
```text
(True, 4)
```

## Жиі кездесетін қателер

**1. `newline=""` ұмытылған** — Windows-та CSV-де әр жолдан кейін бос жол шығады.
```python
# Қате:
f = open("out.csv", "w", encoding="utf-8")
# Дұрыс:
f = open("out.csv", "w", newline="", encoding="utf-8")
f.close()
```

**2. Excel қазақ әріптерін бұзады** — `utf-8` орнына `utf-8-sig` қолданыңыз.

**3. Тақырып жолы жоқ** — `writer.writerow(headers)` жазылмаса, файлды ашқан адам бағандардың мағынасын білмейді.

**4. Көшірмені сол файлдың үстіне жазу:**
```python
import shutil
# Қате: әр көшірме алдыңғысын өшіреді, қате деректер үстінен «жақсы» көшірме жоғалады
# shutil.copy("school.db", "backup.db")
# Дұрыс: атауға уақыт белгісін қосу
from datetime import datetime
name = f"backup_{datetime.now():%Y%m%d_%H%M%S}.db"
print(name)
```

**5. Кесте атауын пайдаланушыдан алып f-жолға қою** — SQL-инъекция қаупі. Кесте атауын рұқсат етілген тізіммен тексеріңіз: `if table not in ("students", "grades"): raise ValueError`.

**6. Көшірмені сол дискте ғана сақтау** — диск бұзылса, түпнұсқа да, көшірме де жоғалады (3-2-1 ережесін есте сақтаңыз).

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Экспорт | Деректерді бағдарламадан сыртқы форматқа (CSV, JSON) шығару |
| Импорт | Сыртқы файлдан деректерді бағдарламаға енгізу |
| `cursor.description` | Соңғы сұраныс бағандарының сипаттамасы |
| BOM / `utf-8-sig` | Excel кодировканы тануы үшін файл басындағы белгі |
| Сақтық көшірме (backup) | Қалпына келтіруге арналған деректер көшірмесі |
| SQL-дамп | Қорды қайта құратын SQL командалары бар мәтіндік файл |
| Ротация | Тек соңғы N көшірмені сақтап, ескісін өшіру |
| 3-2-1 ережесі | 3 дана, 2 тасығыш, 1 сыртқы орын |
| `PRAGMA integrity_check` | SQLite қорының тұтастығын тексеретін команда |

## Ескерту

- `csv` модулімен жұмыс істегенде файлды `newline=""` параметрімен ашыңыз — әйтпесе Windows-та жолдар арасында бос жолдар пайда болады (https://docs.python.org/3/library/csv.html).
- Орыс/қазақ локаліндегі Excel үшін `;` бөлгіші мен `utf-8-sig` кодировкасы ыңғайлы; бағдарламалар арасында алмасу үшін (Pandas, Google Sheets) — стандартты `,` және `utf-8`.
- `Connection.backup()` әдісі Python 3.7-ден бастап бар; ол файлды жай көшіруден айырмашылығы — қорға жазу жүріп жатса да дұрыс көшірме жасайды.
- `.db` файлының жанындағы `-wal` және `-journal` файлдары — қордың бөлігі; файлды қолмен көшіргенде оларды елемеуге болмайды (сондықтан `backup()` сенімдірек).
- Уақыт белгісі бар файл атауын `datetime.now().strftime("%Y-%m-%d_%H-%M-%S")` түрінде жасаңыз: Windows-та файл атауында `:` таңбасына тыйым салынған, ал ЖЖЖЖ-АА-КК пішімі дұрыс сұрыпталады.
