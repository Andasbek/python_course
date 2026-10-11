# Сабақ 57. SQL сұраныстары: UPDATE, DELETE. WHERE шарты.

## Тақырыптар

1. Жұмыс қоры
2. `WHERE` шарты толық
3. `UPDATE` — жазбаларды өзгерту
4. `DELETE` — жазбаларды жою
5. Қауіпсіз өзгерту ережелері
6. Байланысты кестелерден жою
7. Жиі кездесетін қателер
8. Негізгі ұғымдар

## 1. Жұмыс қоры

```python
import sqlite3

STUDENTS = [
    ("Айгерім", "Нұрланқызы", "9А", "Алматы", 95, "+77011112233"),
    ("Бауыржан", "Сейітов", "9А", "Қонаев", 78, None),
    ("Дәулет", "Оспанов", "9Ә", "Алматы", 88, "+77025556677"),
    ("Сымбат", "Әлиева", "9Ә", "Алматы", 100, "+77071234567"),
    ("Ерлан", "Жақыпов", "9А", "Талғар", 64, None),
    ("Мадина", "Қасымова", "9Ә", "Алматы", 91, "+77479876543"),
    ("Темірлан", "Ахметов", "10А", "Қонаев", 73, "+77051112244"),
    ("Аружан", "Серікова", "10А", "Алматы", 85, None),
]

conn = sqlite3.connect("school57.db")
conn.executescript("""
    DROP TABLE IF EXISTS students;
    CREATE TABLE students (
        id INTEGER PRIMARY KEY,
        first_name TEXT NOT NULL,
        last_name TEXT NOT NULL,
        class TEXT,
        city TEXT,
        score INTEGER CHECK (score BETWEEN 0 AND 100),
        phone TEXT
    );
""")
conn.executemany(
    "INSERT INTO students (first_name, last_name, class, city, score, phone) VALUES (?, ?, ?, ?, ?, ?)",
    STUDENTS,
)
conn.commit()
conn.close()
print("school57.db: 8 оқушы")
```

Нәтижесі:

```text
school57.db: 8 оқушы
```

## 2. `WHERE` шарты толық

`WHERE` — `SELECT`, `UPDATE` және `DELETE` командаларында **қай жазбалармен** жұмыс істейтінімізді анықтайды. Ол Python-дағы `if` сияқты: әр жазба үшін шарт тексеріледі.

### Салыстыру операторлары

| Оператор | Мағынасы | Мысал |
|---|---|---|
| `=` | тең | `class = '9А'` |
| `<>` немесе `!=` | тең емес | `city <> 'Алматы'` |
| `<`, `>`, `<=`, `>=` | кіші, үлкен | `score >= 90` |

### Логикалық операторлар: `AND`, `OR`, `NOT`

```python
import sqlite3

conn = sqlite3.connect("school57.db")


def show(title, sql, params=()):
    names = [r[0] for r in conn.execute(sql, params)]
    print(f"{title}: {', '.join(names) if names else '—'}")


show("9А және 80-нен жоғары", "SELECT first_name FROM students WHERE class = ? AND score > ?", ("9А", 80))
show("Талғар немесе Қонаев", "SELECT first_name FROM students WHERE city = ? OR city = ?", ("Талғар", "Қонаев"))
show("Алматыдан емес", "SELECT first_name FROM students WHERE NOT city = ?", ("Алматы",))
show("9Ә, (Алматы немесе 95+)",
     "SELECT first_name FROM students WHERE class = ? AND (city = ? OR score >= ?)", ("9Ә", "Алматы", 95))
conn.close()
```

Нәтижесі:

```text
9А және 80-нен жоғары: Айгерім
Талғар немесе Қонаев: Бауыржан, Ерлан, Темірлан
Алматыдан емес: Бауыржан, Ерлан, Темірлан
9Ә, (Алматы немесе 95+): Дәулет, Сымбат, Мадина
```

`AND` `OR`-дан бұрын орындалады (Python-дағыдай). Күмән болса — **жақша** қой.

### `BETWEEN` — аралық

```sql
SELECT first_name, score FROM students WHERE score BETWEEN 80 AND 95;   -- 80 және 95 те кіреді
```

### `IN` — тізімдегі мәндердің бірі

```sql
SELECT first_name FROM students WHERE city IN ('Талғар', 'Қонаев');
-- city = 'Талғар' OR city = 'Қонаев' дегенмен бірдей
```

### `LIKE` — үлгі бойынша іздеу

| Үлгі таңбасы | Мағынасы | Мысал | Сәйкес келеді |
|---|---|---|---|
| `%` | кез келген таңбалар саны (0 да) | `'А%'` | Айгерім, Аружан |
| `_` | дәл бір таңба | `'_р%'` | Ерлан, Аружан |
| | | `'%ов'` | Сейітов, Оспанов, Жақыпов, Ахметов |
| | | `'%+7701%'` | телефонында +7701 бар |

### `IS NULL` — бос мәнді тексеру

`NULL` — «мән жоқ / белгісіз». Оны `= NULL` арқылы тексеруге **болмайды** (нәтиже әрқашан «белгісіз»). Тек `IS NULL` немесе `IS NOT NULL`.

```python
import sqlite3

conn = sqlite3.connect("school57.db")
q = {
    "BETWEEN 80 AND 95": ("SELECT first_name FROM students WHERE score BETWEEN ? AND ?", (80, 95)),
    "IN (Талғар, Қонаев)": ("SELECT first_name FROM students WHERE city IN (?, ?)", ("Талғар", "Қонаев")),
    "LIKE 'А%'": ("SELECT first_name FROM students WHERE first_name LIKE ?", ("А%",)),
    "LIKE '%ов'": ("SELECT last_name FROM students WHERE last_name LIKE ?", ("%ов",)),
    "phone IS NULL": ("SELECT first_name FROM students WHERE phone IS NULL", ()),
    "phone = NULL (қате!)": ("SELECT first_name FROM students WHERE phone = NULL", ()),
}
for title, (sql, params) in q.items():
    print(f"{title:<22}→ {[r[0] for r in conn.execute(sql, params)]}")
conn.close()
```

Нәтижесі:

```text
BETWEEN 80 AND 95     → ['Айгерім', 'Дәулет', 'Мадина', 'Аружан']
IN (Талғар, Қонаев)   → ['Бауыржан', 'Ерлан', 'Темірлан']
LIKE 'А%'             → ['Айгерім', 'Аружан']
LIKE '%ов'            → ['Сейітов', 'Оспанов', 'Жақыпов', 'Ахметов']
phone IS NULL         → ['Бауыржан', 'Ерлан', 'Аружан']
phone = NULL (қате!)  → []
```

> **Кириллица және `LIKE`:** SQLite-та `LIKE` латын әріптері үшін регистрге сезімсіз (`'a%'` = `'A%'`), бірақ **кириллица үшін сезімтал**: `'а%'` «Айгерім»-ді таппайды. Шешім: іздеуді Python-да `str.lower()` арқылы жасау немесе пайдаланушы енгізген сөздің бас әрпін дұрыс жазу.

### Пайдаланушы енгізген мәтінмен `LIKE`

`%` таңбаларын **параметрге** қосамыз, SQL жолына емес:

```python
import sqlite3

conn = sqlite3.connect("school57.db")
part = "ан"                                   # пайдаланушы енгізді
rows = conn.execute("SELECT first_name FROM students WHERE first_name LIKE ?", (f"%{part}%",)).fetchall()
print(f"«{part}» бар аттар:", [r[0] for r in rows])
conn.close()
```

Нәтижесі:

```text
«ан» бар аттар: ['Бауыржан', 'Ерлан', 'Темірлан', 'Аружан']
```

## 3. `UPDATE` — жазбаларды өзгерту

Синтаксис:

```sql
UPDATE кесте
SET өріс1 = мән1, өріс2 = мән2
WHERE шарт;
```

```python
import sqlite3

conn = sqlite3.connect("school57.db")

# 1) Бір жазбаны id бойынша өзгерту
cur = conn.execute("UPDATE students SET class = ? WHERE id = ?", ("9Ә", 2))
print("Бауыржан ауыстырылды, өзгерген жазбалар:", cur.rowcount)

# 2) Бірнеше өрісті бірден
cur = conn.execute("UPDATE students SET city = ?, phone = ? WHERE id = ?", ("Алматы", "+77770001122", 5))
print("Ерланның мекенжайы мен телефоны:", cur.rowcount)

# 3) Есептеу: 10А сыныбына +5 бонус балл (бірақ 100-ден аспасын)
cur = conn.execute("UPDATE students SET score = MIN(score + 5, 100) WHERE class = ?", ("10А",))
print("10А бонус алды:", cur.rowcount, "оқушы")

conn.commit()
for row in conn.execute("SELECT first_name, class, city, score FROM students WHERE id IN (2, 5, 7, 8)"):
    print(row)
conn.close()
```

Нәтижесі:

```text
Бауыржан ауыстырылды, өзгерген жазбалар: 1
Ерланның мекенжайы мен телефоны: 1
10А бонус алды: 2 оқушы
('Бауыржан', '9Ә', 'Қонаев', 78)
('Ерлан', '9А', 'Алматы', 64)
('Темірлан', '10А', 'Қонаев', 78)
('Аружан', '10А', 'Алматы', 90)
```

- `cur.rowcount` — команда **неше жазбаға** әсер еткенін көрсетеді. `0` болса — шартқа сай жазба табылмады (мысалы, қате `id`).
- `SET score = score + 5` — жаңа мән ескі мәннен есептеледі.
- `MIN(a, b)` — SQLite-та екі мәннің кішісі.

### `WHERE`-сіз `UPDATE` — апат!

```sql
UPDATE students SET score = 0;   -- БАРЛЫҚ оқушының баллы 0 болды!
```

`WHERE` жазылмаса, команда кестедегі **барлық** жазбаға қолданылады. SQL «Сенімдісің бе?» деп сұрамайды.

## 4. `DELETE` — жазбаларды жою

Синтаксис:

```sql
DELETE FROM кесте WHERE шарт;
```

```python
import sqlite3

conn = sqlite3.connect("school57.db")

cur = conn.execute("DELETE FROM students WHERE id = ?", (8,))
print("Жойылды:", cur.rowcount)

cur = conn.execute("DELETE FROM students WHERE score < ? AND phone IS NULL", (70,))
print("Шарт бойынша жойылды:", cur.rowcount)

cur = conn.execute("DELETE FROM students WHERE id = ?", (999,))
print("id=999:", "табылмады" if cur.rowcount == 0 else "жойылды")

conn.commit()
print("Қалды:", conn.execute("SELECT COUNT(*) FROM students").fetchone()[0])
conn.close()
```

Нәтижесі:

```text
Жойылды: 1
Шарт бойынша жойылды: 0
id=999: табылмады
Қалды: 7
```

(Ерлан бұрын телефон алған, сондықтан екінші шартқа ешкім сай келмеді.)

`DELETE FROM students;` — `WHERE`-сіз — **кестедегі барлық** жазбаны жояды (кестенің өзі қалады). `DROP TABLE` — кестені толығымен жояды.

| Команда | Нәтижесі |
|---|---|
| `DELETE FROM t WHERE id = 5` | бір жазба жойылады |
| `DELETE FROM t` | барлық жазба жойылады, кесте бос қалады |
| `DROP TABLE t` | кесте құрылымымен бірге жойылады |

## 5. Қауіпсіз өзгерту ережелері

1. **Алдымен `SELECT`, сосын `UPDATE`/`DELETE`.** Сол `WHERE` шартымен `SELECT` жасап, қай жазбалар өзгеретінін көріп ал.
2. **Мүмкіндігінше `id` бойынша** өзгерт — ол бірегей.
3. **`rowcount`-ты тексер** — күткен санмен сәйкес пе?
4. **Транзакция:** күткен нәтиже болмаса — `rollback()`.
5. **Пайдаланушыдан растау сұра:** «3 жазба жойылады. Жалғастырасыз ба? (иә/жоқ)».
6. **Сақтық көшірме** (backup) — маңызды өзгерістер алдында (`.db` файлының көшірмесі немесе `conn.backup()` әдісі).

```python
import sqlite3

conn = sqlite3.connect("school57.db")
city = "Қонаев"

# 1. Алдымен не өзгеретінін көреміз
victims = conn.execute("SELECT id, first_name FROM students WHERE city = ?", (city,)).fetchall()
print("Жойылатындар:", victims)

# 2. Жою, бірақ әзірге commit жасамаймыз
cur = conn.execute("DELETE FROM students WHERE city = ?", (city,))

# 3. Тексеру: саны сәйкес пе?
if cur.rowcount != len(victims) or cur.rowcount > 2:
    conn.rollback()        # болдырмау — ештеңе өзгермейді
    print("Тым көп жазба! Болдырылмады (rollback)")
else:
    conn.commit()
    print("Жойылды:", cur.rowcount)

print("Қалды:", conn.execute("SELECT COUNT(*) FROM students").fetchone()[0])
conn.close()
```

Нәтижесі:

```text
Жойылатындар: [(2, 'Бауыржан'), (7, 'Темірлан')]
Жойылды: 2
Қалды: 5
```

## 6. Байланысты кестелерден жою

Үйірмеге жазылған оқушыны жойсақ, `enrollments` кестесінде оған сілтейтін жазбалар «жетім» болып қалады. SQLite (`PRAGMA foreign_keys = ON` болса) мұны тоқтатады немесе біз таңдаған әрекетті орындайды:

| Ереже | Мағынасы |
|---|---|
| (әдепкі) / `ON DELETE RESTRICT` | сілтейтін жазбалар бар болса — жоюға **рұқсат жоқ** |
| `ON DELETE CASCADE` | ата-жазбамен бірге сілтейтін жазбалар да **автоматты жойылады** |
| `ON DELETE SET NULL` | сілтейтін жазбалардағы FK өрісі `NULL` болады |

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    CREATE TABLE kids (id INTEGER PRIMARY KEY, name TEXT);
    CREATE TABLE clubs (id INTEGER PRIMARY KEY, title TEXT);
    CREATE TABLE enrollments (
        kid_id INTEGER REFERENCES kids(id) ON DELETE CASCADE,
        club_id INTEGER REFERENCES clubs(id)
    );
    INSERT INTO kids VALUES (1, 'Әлихан'), (2, 'Томирис');
    INSERT INTO clubs VALUES (1, 'Python'), (2, 'Шахмат');
    INSERT INTO enrollments VALUES (1, 1), (1, 2), (2, 1);
""")

conn.execute("DELETE FROM kids WHERE id = ?", (1,))          # CASCADE
print("Әлиханның жазылулары да жойылды:", conn.execute("SELECT * FROM enrollments").fetchall())

try:
    conn.execute("DELETE FROM clubs WHERE id = ?", (1,))     # әдепкі: рұқсат жоқ
except sqlite3.IntegrityError as e:
    print("Python үйірмесі жойылмады:", e)
conn.close()
```

Нәтижесі:

```text
Әлиханның жазылулары да жойылды: [(2, 1)]
Python үйірмесі жойылмады: FOREIGN KEY constraint failed
```

### «Жұмсақ жою» (soft delete)

Көп жүйеде деректер шын мәнінде жойылмайды — тек «белсенді емес» деп белгіленеді. Мысалы, оқушы үйірмеден кетсе де, оның бұрынғы бағалары мен сертификаттары сақталуы керек.

```sql
ALTER TABLE students ADD COLUMN active INTEGER DEFAULT 1;
UPDATE students SET active = 0 WHERE id = 5;          -- «жою»
SELECT first_name FROM students WHERE active = 1;      -- тек белсенділер
UPDATE students SET active = 1 WHERE id = 5;          -- қалпына келтіру оңай!
```

## Жиі кездесетін қателер

**1. `WHERE`-сіз `UPDATE`/`DELETE`** → бүкіл кесте өзгереді. Әрқашан `WHERE` бар-жоғын тексер!

**2. `= NULL`** → ешқашан ақиқат емес. Дұрыс: `IS NULL`.

**3. `AND`/`OR` жақшасыз.**

```sql
-- Қате: «9Ә-дағы алматылықтар» + «БАРЛЫҚ сыныптағы 95+»
SELECT * FROM students WHERE class = '9Ә' AND city = 'Алматы' OR score >= 95;
-- Дұрыс: 9Ә сыныбындағы (алматылықтар немесе 95+)
SELECT * FROM students WHERE class = '9Ә' AND (city = 'Алматы' OR score >= 95);
```

**4. `commit()` ұмытылды** → UPDATE/DELETE бағдарлама жабылғанда болдырылмайды (бұл кейде пайдалы, бірақ көбіне — қате).

**5. `LIKE`-тағы `%` SQL жолында, ал мән f-жолмен:** `f"... LIKE '%{text}%'"` → SQL-инъекция. Дұрыс: `LIKE ?` және `(f"%{text}%",)`.

**6. `UPDATE ... SET a = 1 AND b = 2`** — қате! `AND` емес, **үтір**: `SET a = 1, b = 2`. (Әйтпесе SQLite `a`-ға `1 AND b = 2` логикалық өрнегінің нәтижесін жазады.)

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| `WHERE` | Жазбаларды сүзетін шарт |
| `AND`, `OR`, `NOT` | Шарттарды біріктіретін логикалық операторлар |
| `BETWEEN a AND b` | Мән a мен b аралығында (екеуі де кіреді) |
| `IN (...)` | Мән тізімдегі мәндердің бірі |
| `LIKE` | Үлгі бойынша салыстыру (`%` — кез келген, `_` — бір таңба) |
| `IS NULL` | Мән бос екенін тексеру |
| `UPDATE ... SET` | Жазбалардағы мәндерді өзгерту |
| `DELETE FROM` | Жазбаларды жою |
| `rowcount` | Команда әсер еткен жазбалар саны |
| `rollback()` | Аяқталмаған транзакцияны болдырмау |
| `ON DELETE CASCADE` | Ата-жазбамен бірге тәуелді жазбаларды жою |
| Жұмсақ жою | Жазбаны жоймай, белсенді емес деп белгілеу |

## Ескерту

- `UPDATE`/`DELETE`-тен кейін `cur.rowcount` шын мәнінде әсер еткен жолдар санын көрсетеді — жазбаның табылғанын тексеруге ыңғайлы (`0` — ештеңе өзгермеді).
- `= NULL` салыстыруы әрқашан «белгісіз» береді, сондықтан `WHERE phone = NULL` бірде-бір жолды таппайды; дұрысы `WHERE phone IS NULL`.
- SQLite-та `LIKE` регистрге тек латын әріптері үшін сезімтал емес: `'алматы' LIKE 'Алматы'` жалған береді. Кирилл үшін жолдарды Python-да бір регистрге келтіреді немесе іздеуге арналған бөлек өріс сақтайды.
- `LIKE` үшін пайдаланушы мәтініндегі `%` және `_` таңбаларын экрандау керек (`ESCAPE '\'`), әйтпесе олар үлгіге айналады.
- `ON DELETE CASCADE` тек `PRAGMA foreign_keys = ON` болғанда жұмыс істейді; онсыз тәуелді жазбалар «жетім» қалады.
- Қордың көшірмесін «жұмыс барысында» `src.backup(dst)` (`sqlite3.Connection.backup`) жасайды. Құжаттама: https://docs.python.org/3/library/sqlite3.html және https://www.sqlite.org/lang_update.html
