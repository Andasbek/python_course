# Сабақ 56. SQL сұраныстары: INSERT және SELECT.

## Тақырыптар

1. Дайындық: жұмыс қоры
2. `INSERT INTO` — жазба қосу
3. SQL-инъекция — ең қауіпті қате
4. Шектеулер бұзылғанда: `IntegrityError`
5. `SELECT` — деректерді оқу
6. Нәтижені алу тәсілдері
7. Байланысты кестелерге қосу
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Дайындық: жұмыс қоры

Мысалдарды қайталау үшін әр мысал басында шағын қор құрамыз:

```python
import sqlite3

conn = sqlite3.connect("school.db")
conn.executescript("""
    DROP TABLE IF EXISTS students;
    CREATE TABLE students (
        id INTEGER PRIMARY KEY,
        first_name TEXT NOT NULL,
        last_name TEXT NOT NULL,
        class TEXT,
        city TEXT DEFAULT 'Алматы',
        score INTEGER CHECK (score BETWEEN 0 AND 100)
    );
""")
conn.close()
print("school.db дайын")
```

Нәтижесі:

```text
school.db дайын
```

## 2. `INSERT INTO` — жазба қосу

Синтаксис:

```sql
INSERT INTO кесте (өріс1, өріс2, ...) VALUES (мән1, мән2, ...);
```

```sql
INSERT INTO students (first_name, last_name, class, score)
VALUES ('Айгерім', 'Нұрланқызы', '9А', 95);
```

- Өрістер тізімі мен мәндер тізімі **ретімен** сәйкес келуі керек.
- Тізімде жоқ өрістер `DEFAULT` мәнін (немесе `NULL`) алады: жоғарыдағы мысалда `city = 'Алматы'`.
- `id` көрсетілмесе, SQLite келесі нөмірді өзі береді.
- SQL-дағы мәтін — **жалқы тырнақшада**: `'Айгерім'`.

### Python-да: тек параметрлер арқылы!

Мәндерді SQL жолына **ешқашан** f-жолмен немесе `+` арқылы қоспаймыз. Оның орнына **орынбасар** (placeholder) `?` қоямыз, ал мәндерді бөлек кортеж ретінде береміз:

```python
import sqlite3

conn = sqlite3.connect("school.db")
cur = conn.cursor()

cur.execute(
    "INSERT INTO students (first_name, last_name, class, score) VALUES (?, ?, ?, ?)",
    ("Айгерім", "Нұрланқызы", "9А", 95),
)
print("Жаңа жазбаның id-і:", cur.lastrowid)

first, last = "Бауыржан", "Сейітов"
cur.execute(
    "INSERT INTO students (first_name, last_name, class, city, score) VALUES (?, ?, ?, ?, ?)",
    (first, last, "9А", "Қонаев", 78),
)
print("Жаңа жазбаның id-і:", cur.lastrowid)

conn.commit()      # міндетті!
conn.close()
```

Нәтижесі:

```text
Жаңа жазбаның id-і: 1
Жаңа жазбаның id-і: 2
```

`cur.lastrowid` — соңғы қосылған жазбаның `id`-і. Ол байланысты кестелерге жазба қосқанда керек (мысалы, жаңа оқушыны қосып, бірден оны үйірмеге жазу).

> **Бір элементті кортеж:** `(95,)` — үтірмен! `(95)` — жай сан, кортеж емес.

### Атаулы параметрлер `:name`

Өрістер көп болса, сөздік ыңғайлы:

```python
import sqlite3

conn = sqlite3.connect("school.db")
student = {"first": "Дәулет", "last": "Оспанов", "cls": "9Ә", "score": 88}
conn.execute(
    "INSERT INTO students (first_name, last_name, class, score) VALUES (:first, :last, :cls, :score)",
    student,
)
conn.commit()
conn.close()
print("Дәулет қосылды")
```

Нәтижесі:

```text
Дәулет қосылды
```

### `executemany` — көп жазбаны бірден қосу

```python
import sqlite3

students = [
    ("Сымбат", "Әлиева", "9Ә", "Алматы", 100),
    ("Ерлан", "Жақыпов", "9А", "Талғар", 64),
    ("Мадина", "Қасымова", "9Ә", "Алматы", 91),
    ("Темірлан", "Ахметов", "10А", "Қонаев", 73),
]

conn = sqlite3.connect("school.db")
conn.executemany(
    "INSERT INTO students (first_name, last_name, class, city, score) VALUES (?, ?, ?, ?, ?)",
    students,
)
conn.commit()
total = conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]
conn.close()
print("Кестеде барлығы", total, "оқушы")
```

Нәтижесі:

```text
Кестеде барлығы 7 оқушы
```

`executemany` тізімдегі әр кортеж үшін команданы қайталайды — бұл циклде `execute` шақырудан жылдамырақ.

## 3. SQL-инъекция — ең қауіпті қате

Неге f-жол қолдануға болмайды? Пайдаланушы енгізген мәтін **SQL кодының бөлігіне** айналып кетуі мүмкін:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE users (login TEXT, password TEXT)")
conn.execute("INSERT INTO users VALUES ('admin', 'Qwerty2027')")

login = "admin"
password = "' OR '1'='1"          # бұзақы енгізген «пароль»

# ҚАУІПТІ: мәтінді SQL-ға тікелей қосу
sql = f"SELECT * FROM users WHERE login = '{login}' AND password = '{password}'"
print(sql)
print("f-жол:", "Кіру рұқсат етілді!" if conn.execute(sql).fetchone() else "Кіру жоқ")

# ҚАУІПСІЗ: параметрлер
row = conn.execute("SELECT * FROM users WHERE login = ? AND password = ?", (login, password)).fetchone()
print("Параметр:", "Кіру рұқсат етілді!" if row else "Кіру жоқ")
conn.close()
```

Нәтижесі:

```text
SELECT * FROM users WHERE login = 'admin' AND password = '' OR '1'='1'
f-жол: Кіру рұқсат етілді!
Параметр: Кіру жоқ
```

Бұзақы парольді білмей-ақ жүйеге кірді! `'1'='1'` әрқашан ақиқат. Ал параметрлер тәсілінде ДҚБЖ енгізілген мәтінді **тек мән** ретінде қабылдайды, ешқашан код ретінде емес.

> **Алтын ереже:** SQL сұранысына пайдаланушыдан келген кез келген мән тек `?` немесе `:name` арқылы беріледі.

(Ескерту: шынайы жүйелерде парольдер ашық түрде сақталмайды, олардың хэші сақталады — бұл мысал тек инъекцияны көрсету үшін.)

## 4. Шектеулер бұзылғанда: `IntegrityError`

```python
import sqlite3

conn = sqlite3.connect("school.db")
bad_rows = [
    ("Арман", "Бекұлы", "9А", "Алматы", 150),   # CHECK: score 0..100
    (None, "Белгісіз", "9Ә", "Алматы", 80),     # NOT NULL
]
for row in bad_rows:
    try:
        conn.execute(
            "INSERT INTO students (first_name, last_name, class, city, score) VALUES (?, ?, ?, ?, ?)",
            row,
        )
    except sqlite3.IntegrityError as e:
        print("Қосылмады:", e)
conn.close()
```

Нәтижесі:

```text
Қосылмады: CHECK constraint failed: score BETWEEN 0 AND 100
Қосылмады: NOT NULL constraint failed: students.first_name
```

Деректер қоры қате деректерді **өзі** тоқтатты — CSV/JSON-да мұндай қорғаныс жоқ еді. `UNIQUE` бұзылса — `UNIQUE constraint failed`, сыртқы кілт бұзылса — `FOREIGN KEY constraint failed`.

## 5. `SELECT` — деректерді оқу

Негізгі синтаксис:

```sql
SELECT өрістер FROM кесте [WHERE шарт] [LIMIT n];
```

### Барлық өрістер: `*`

```python
import sqlite3

conn = sqlite3.connect("school.db")
cur = conn.execute("SELECT * FROM students")
rows = cur.fetchall()
print("Жазбалар саны:", len(rows))
for row in rows:
    print(row)
conn.close()
```

Нәтижесі:

```text
Жазбалар саны: 7
(1, 'Айгерім', 'Нұрланқызы', '9А', 'Алматы', 95)
(2, 'Бауыржан', 'Сейітов', '9А', 'Қонаев', 78)
(3, 'Дәулет', 'Оспанов', '9Ә', 'Алматы', 88)
(4, 'Сымбат', 'Әлиева', '9Ә', 'Алматы', 100)
(5, 'Ерлан', 'Жақыпов', '9А', 'Талғар', 64)
(6, 'Мадина', 'Қасымова', '9Ә', 'Алматы', 91)
(7, 'Темірлан', 'Ахметов', '10А', 'Қонаев', 73)
```

Әр жазба Python-да **кортеж** болып келеді, өрістердің реті — кестедегідей.

### Таңдалған өрістер және `AS`

```python
import sqlite3

conn = sqlite3.connect("school.db")
for first, last, score in conn.execute("SELECT first_name, last_name, score FROM students LIMIT 3"):
    print(f"{last} {first}: {score}")

print(conn.execute("SELECT first_name || ' ' || last_name AS full_name FROM students LIMIT 1").fetchone())
conn.close()
```

Нәтижесі:

```text
Нұрланқызы Айгерім: 95
Сейітов Бауыржан: 78
Оспанов Дәулет: 88
('Айгерім Нұрланқызы',)
```

- `LIMIT 3` — тек алғашқы 3 жазба;
- `||` — SQL-да жолдарды біріктіру;
- `AS full_name` — нәтиже бағанына жаңа атау (бүркеншік ат, alias) беру;
- курсорды тікелей `for` циклімен қарап шығуға болады.

### `DISTINCT` — қайталанбайтын мәндер

```python
import sqlite3

conn = sqlite3.connect("school.db")
cities = conn.execute("SELECT DISTINCT city FROM students").fetchall()
print("Қалалар:", [c[0] for c in cities])
conn.close()
```

Нәтижесі:

```text
Қалалар: ['Алматы', 'Қонаев', 'Талғар']
```

### Қарапайым `WHERE` шарты

`WHERE` — тек шартқа сай жазбаларды таңдайды. Шарттарды `AND`, `OR`, `NOT` операторларымен біріктіруге, сондай-ақ `LIKE`, `BETWEEN`, `IN` қолдануға болады.

```python
import sqlite3

conn = sqlite3.connect("school.db")

cls = "9Ә"
rows = conn.execute("SELECT first_name, score FROM students WHERE class = ?", (cls,)).fetchall()
print(f"{cls} сыныбы:", rows)

rows = conn.execute("SELECT first_name, score FROM students WHERE score >= ?", (90,)).fetchall()
print("90 және одан жоғары:", rows)
conn.close()
```

Нәтижесі:

```text
9Ә сыныбы: [('Дәулет', 88), ('Сымбат', 100), ('Мадина', 91)]
90 және одан жоғары: [('Айгерім', 95), ('Сымбат', 100), ('Мадина', 91)]
```

Салыстыру операторлары: `=`, `<>` (немесе `!=`), `<`, `>`, `<=`, `>=`. Назар аудар: SQL-да теңдік — бір `=` таңбасы.

### SQL-да есептеу

```python
import sqlite3

conn = sqlite3.connect("school.db")
count = conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]
row = conn.execute("SELECT MAX(score), MIN(score), AVG(score) FROM students").fetchone()
print("Барлығы:", count)
print(f"Ең жоғары: {row[0]}, ең төмен: {row[1]}, орташа: {row[2]:.1f}")
conn.close()
```

Нәтижесі:

```text
Барлығы: 7
Ең жоғары: 100, ең төмен: 64, орташа: 84.1
```

## 6. Нәтижені алу тәсілдері

| Тәсіл | Нәтижесі | Қашан |
|---|---|---|
| `cur.fetchone()` | бір жазба (кортеж) немесе `None` | бір жазба күтілсе (id бойынша іздеу, COUNT) |
| `cur.fetchall()` | барлық жазбалар тізімі | нәтиже шағын болса |
| `cur.fetchmany(n)` | келесі `n` жазба | үлкен нәтижені бөліп өңдеу |
| `for row in cur:` | жазбаларды бір-бірлеп | үлкен нәтиже, жадты үнемдеу |

```python
import sqlite3

conn = sqlite3.connect("school.db")
student_id = 42
row = conn.execute("SELECT first_name FROM students WHERE id = ?", (student_id,)).fetchone()
if row is None:
    print(f"id={student_id} оқушы табылмады")
else:
    print("Табылды:", row[0])

cur = conn.execute("SELECT first_name FROM students")
print("Алғашқы екеуі:", cur.fetchmany(2))
print("Келесі екеуі:", cur.fetchmany(2))
conn.close()
```

Нәтижесі:

```text
id=42 оқушы табылмады
Алғашқы екеуі: [('Айгерім',), ('Бауыржан',)]
Келесі екеуі: [('Дәулет',), ('Сымбат',)]
```

### `sqlite3.Row` — өріс атауы бойынша алу

`row[3]` — қай өріс екені түсініксіз. `row_factory = sqlite3.Row` баптауымен жазбаны сөздік сияқты атау бойынша оқимыз:

```python
import sqlite3

conn = sqlite3.connect("school.db")
conn.row_factory = sqlite3.Row
row = conn.execute("SELECT * FROM students WHERE id = ?", (4,)).fetchone()
print(row["first_name"], row["class"], row["score"])
print("Өрістер:", row.keys())
print(dict(row))
conn.close()
```

Нәтижесі:

```text
Сымбат 9Ә 100
Өрістер: ['id', 'first_name', 'last_name', 'class', 'city', 'score']
{'id': 4, 'first_name': 'Сымбат', 'last_name': 'Әлиева', 'class': '9Ә', 'city': 'Алматы', 'score': 100}
```

## 7. Байланысты кестелерге қосу

Оқушыны қосып, оны бірден үйірмеге жазу — `lastrowid` пен транзакцияның мысалы:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    CREATE TABLE clubs (id INTEGER PRIMARY KEY, title TEXT NOT NULL UNIQUE);
    CREATE TABLE kids (id INTEGER PRIMARY KEY, name TEXT NOT NULL);
    CREATE TABLE enrollments (
        kid_id INTEGER NOT NULL REFERENCES kids(id),
        club_id INTEGER NOT NULL REFERENCES clubs(id),
        UNIQUE (kid_id, club_id)
    );
    INSERT INTO clubs (title) VALUES ('Python'), ('Робототехника');
""")

with conn:   # екі INSERT бірге сақталады немесе бірге болдырылмайды
    cur = conn.execute("INSERT INTO kids (name) VALUES (?)", ("Әлихан",))
    kid_id = cur.lastrowid
    conn.execute("INSERT INTO enrollments (kid_id, club_id) VALUES (?, ?)", (kid_id, 1))

try:
    with conn:
        conn.execute("INSERT INTO enrollments (kid_id, club_id) VALUES (?, ?)", (kid_id, 99))
except sqlite3.IntegrityError as e:
    print("Қате:", e)

print(conn.execute("SELECT * FROM enrollments").fetchall())
conn.close()
```

Нәтижесі:

```text
Қате: FOREIGN KEY constraint failed
[(1, 1)]
```

Бір `INSERT` командасында бірнеше жазба қосуға болады: `VALUES ('Python'), ('Робототехника')`.

## Жиі кездесетін қателер

**1. `commit()` жоқ** → бағдарлама жабылғанда қосылған жазбалар жоғалады. Ең жиі қате!

**2. Параметрлер саны сәйкес емес.**

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE t (a TEXT, b TEXT)")
try:
    conn.execute("INSERT INTO t (a, b) VALUES (?, ?)", ("тек біреу",))
except sqlite3.ProgrammingError as e:
    print("Қате:", e)
conn.close()
```

Нәтижесі:

```text
Қате: Incorrect number of bindings supplied. The current statement uses 2, and there are 1 supplied.
```

**3. Бір мәнді кортежсіз беру.** `execute("... WHERE id = ?", (5))` → қате; дұрыс: `(5,)`. Жолмен: `("9А")` — бұл жол, кортеж емес, сондықтан Python оны 2 таңбалы тізбек деп қабылдайды.

**4. f-жолмен SQL құрастыру** → SQL-инъекция. Дұрыс: `?`.

**5. `fetchone()[0]` нәтиже жоқ болғанда** → `TypeError: 'NoneType' object is not subscriptable`. Дұрыс: алдымен `if row is None`.

**6. SQL-да қос тырнақша.** `WHERE city = "Алматы"` — SQLite кейде оны өріс атауы деп түсінеді. Дұрыс: SQL-дағы мәтін — жалқы тырнақша `'Алматы'`, ал Python-нан — `?` арқылы.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| `INSERT INTO ... VALUES` | Кестеге жаңа жазба қосу |
| Орынбасар (placeholder) | SQL-дағы `?` немесе `:name` — мән кейін беріледі |
| `executemany` | Бір команданы көп деректер жиынымен орындау |
| `lastrowid` | Соңғы қосылған жазбаның id-і |
| SQL-инъекция | Енгізілген мәтін арқылы SQL кодын өзгертіп жіберетін шабуыл |
| `IntegrityError` | Шектеу (NOT NULL, UNIQUE, CHECK, FK) бұзылғандағы қате |
| `SELECT ... FROM` | Кестеден деректерді оқу |
| `*` | Барлық өрістер |
| `AS` | Нәтиже бағанына бүркеншік ат беру |
| `DISTINCT` | Қайталанатын жолдарды алып тастау |
| `LIMIT` | Нәтиже санын шектеу |
| `fetchone` / `fetchall` / `fetchmany` | Нәтижені бір / барлық / n жазба түрінде алу |
| `sqlite3.Row` | Жазбаны өріс атауы бойынша оқуға мүмкіндік беретін тип |

## Ескерту

- `?` толтырғыштары тек **мәндерді** қояды; кесте, өріс атауларын және кілт сөздерді (`ORDER BY name`) параметр арқылы беруге болмайды — оларды алдын ала белгілі тізімнен таңдайды.
- Бір параметрдің өзіне кортеж керек: `cur.execute("... WHERE id = ?", (5,))` — үтір міндетті; `(5)` — жай сан, ал `"5"` жолы таңбалар тізбегі ретінде қабылданады.
- `INSERT`-тен кейін жаңа жазбаның нөмірі `cur.lastrowid`-те, ал әсер еткен жолдар саны `cur.rowcount`-та болады. SQLite 3.35-тен бастап `INSERT ... RETURNING id` деп жазуға болады.
- `executemany()` `execute()` циклынан әлдеқайда жылдам және бір транзакцияда орындалады; жүздеген мың жол үшін дайын тізімді емес, генераторды беріңіз.
- `ORDER BY`-сыз `SELECT` жолдардың ретіне кепілдік бермейді — рет маңызды болса, оны анық көрсетіңіз.
- Құжаттама: https://docs.python.org/3/library/sqlite3.html#sqlite3-placeholders және https://www.sqlite.org/lang_select.html
