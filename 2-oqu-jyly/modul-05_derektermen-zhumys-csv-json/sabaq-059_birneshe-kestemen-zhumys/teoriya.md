# Сабақ 59. Бірнеше кестемен жұмыс. Байланыстар және JOIN операторы.

## Тақырыптар

1. Күрделі деректер моделін құру
2. `JOIN` идеясы
3. `INNER JOIN`
4. `LEFT JOIN` — сол жақ кестенің бәрі
5. Үш кестені біріктіру (M:N байланысы)
6. `JOIN` + `GROUP BY` — есептер
7. JOIN-ның ішкі логикасы (Python-мен салыстыру)
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Күрделі деректер моделін құру

«Үйірмелер орталығы» деректер қорын толық жүзеге асырамыз:

```text
teachers 1 ──── N clubs 1 ──── N enrollments N ──── 1 students
```

```python
import sqlite3

conn = sqlite3.connect("center59.db")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    DROP TABLE IF EXISTS enrollments;
    DROP TABLE IF EXISTS clubs;
    DROP TABLE IF EXISTS students;
    DROP TABLE IF EXISTS teachers;

    CREATE TABLE teachers (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        phone TEXT
    );
    CREATE TABLE clubs (
        id INTEGER PRIMARY KEY,
        title TEXT NOT NULL UNIQUE,
        price INTEGER DEFAULT 0,
        teacher_id INTEGER REFERENCES teachers(id)
    );
    CREATE TABLE students (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        class TEXT
    );
    CREATE TABLE enrollments (
        id INTEGER PRIMARY KEY,
        student_id INTEGER NOT NULL REFERENCES students(id) ON DELETE CASCADE,
        club_id INTEGER NOT NULL REFERENCES clubs(id),
        enrolled TEXT,
        UNIQUE (student_id, club_id)
    );
""")

conn.executemany("INSERT INTO teachers (id, name, phone) VALUES (?, ?, ?)", [
    (1, "Ермекова Г.С.", "+77011111111"),
    (2, "Жақыпов Т.Н.", "+77022222222"),
    (3, "Омарова Д.А.", "+77033333333"),     # әзірге үйірмесі жоқ
])
conn.executemany("INSERT INTO clubs (id, title, price, teacher_id) VALUES (?, ?, ?, ?)", [
    (1, "Python", 0, 1),
    (2, "Веб-дизайн", 0, 1),
    (3, "Робототехника", 5000, 2),
    (4, "3D-модельдеу", 3000, None),           # жетекшісі әлі бекітілмеген
])
conn.executemany("INSERT INTO students (id, name, class) VALUES (?, ?, ?)", [
    (1, "Айгерім", "9А"), (2, "Бауыржан", "9А"), (3, "Дәулет", "9Ә"),
    (4, "Сымбат", "9Ә"), (5, "Ерлан", "10А"),   # Ерлан әлі ешқайда жазылмаған
])
conn.executemany("INSERT INTO enrollments (student_id, club_id, enrolled) VALUES (?, ?, ?)", [
    (1, 1, "2026-09-05"), (1, 3, "2026-09-12"), (2, 1, "2026-09-05"),
    (3, 1, "2026-09-07"), (3, 2, "2026-10-01"), (4, 3, "2026-09-15"),
])
conn.commit()
conn.close()
print("center59.db: 4 кесте толтырылды")
```

Нәтижесі:

```text
center59.db: 4 кесте толтырылды
```

Әдейі «жұбы жоқ» жазбалар қалдырдық: үйірмесі жоқ жетекші (Омарова), жетекшісіз үйірме (3D-модельдеу), ешқайда жазылмаған оқушы (Ерлан) — олар `INNER` мен `LEFT JOIN` айырмашылығын көрсетеді.

## 2. `JOIN` идеясы

`clubs` кестесінде тек `teacher_id` сандары бар:

| id | title | teacher_id |
|---|---|---|
| 1 | Python | 1 |
| 3 | Робототехника | 2 |

`JOIN` — екі кестенің жолдарын **шарт бойынша жұптастырып**, бір нәтиже кестесіне біріктіру:

```text
clubs                                   teachers
id | title          | teacher_id        id | name
 1 | Python         |    1   ───ON───►   1 | Ермекова Г.С.
 2 | Веб-дизайн     |    1   ───ON───►   1 | Ермекова Г.С.
 3 | Робототехника  |    2   ───ON───►   2 | Жақыпов Т.Н.
 4 | 3D-модельдеу   |  NULL   (жұбы жоқ)  3 | Омарова Д.А. (жұбы жоқ)

Нәтиже (INNER JOIN):
title          | name
Python         | Ермекова Г.С.
Веб-дизайн     | Ермекова Г.С.
Робототехника  | Жақыпов Т.Н.
```

**Аналогия:** екі тізім бар: «Үйірмелер және жетекші нөмірі» және «Нөмір — жетекші аты». Сен бірінші тізімдегі әр нөмірді екінші тізімнен тауып, атын жанына жазасың. `JOIN` дәл осыны істейді.

## 3. `INNER JOIN`

```sql
SELECT өрістер
FROM кесте1
INNER JOIN кесте2 ON кесте1.өріс = кесте2.өріс;
```

- `ON` — жолдарды қалай жұптастыру керектігінің шарты (әдетте `FK = PK`);
- `INNER JOIN` (немесе жай `JOIN`) — **тек жұбы табылған** жолдарды қайтарады;
- бірдей атаулы өрістер болса (екі кестеде де `id`, `name`), олар **кесте атауымен** жазылады: `clubs.title`, `teachers.name`.

```python
import sqlite3

conn = sqlite3.connect("center59.db")
rows = conn.execute("""
    SELECT clubs.title, teachers.name
    FROM clubs
    INNER JOIN teachers ON clubs.teacher_id = teachers.id
""").fetchall()
for title, teacher in rows:
    print(f"{title:<14} — {teacher}")
conn.close()
```

Нәтижесі:

```text
Python         — Ермекова Г.С.
Веб-дизайн     — Ермекова Г.С.
Робототехника  — Жақыпов Т.Н.
```

«3D-модельдеу» (жетекшісі жоқ) және Омарова (үйірмесі жоқ) нәтижеге **кірмеді** — `INNER JOIN` тек жұптарды көрсетеді.

### Кесте бүркеншік аттары (alias)

Ұзын атауларды қысқартамыз: `FROM clubs AS c` немесе жай `FROM clubs c`.

```python
import sqlite3

conn = sqlite3.connect("center59.db")
rows = conn.execute("""
    SELECT c.title, t.name, t.phone
    FROM clubs c
    JOIN teachers t ON c.teacher_id = t.id
    WHERE c.price = 0
    ORDER BY c.title
""").fetchall()
print("Тегін үйірмелер:")
for row in rows:
    print(" ", row)
conn.close()
```

Нәтижесі:

```text
Тегін үйірмелер:
  ('Python', 'Ермекова Г.С.', '+77011111111')
  ('Веб-дизайн', 'Ермекова Г.С.', '+77011111111')
```

## 4. `LEFT JOIN` — сол жақ кестенің бәрі

`LEFT JOIN` **сол жақ** кестенің (`FROM`-дан кейінгі) **барлық** жолдарын қайтарады. Оң жақ кестеде жұбы табылмаса — оның өрістері `NULL` болады.

```python
import sqlite3

conn = sqlite3.connect("center59.db")
print("INNER JOIN:")
for row in conn.execute("SELECT c.title, t.name FROM clubs c JOIN teachers t ON c.teacher_id = t.id"):
    print(" ", row)
print("LEFT JOIN:")
for row in conn.execute("SELECT c.title, t.name FROM clubs c LEFT JOIN teachers t ON c.teacher_id = t.id"):
    print(" ", row)
conn.close()
```

Нәтижесі:

```text
INNER JOIN:
  ('Python', 'Ермекова Г.С.')
  ('Веб-дизайн', 'Ермекова Г.С.')
  ('Робототехника', 'Жақыпов Т.Н.')
LEFT JOIN:
  ('Python', 'Ермекова Г.С.')
  ('Веб-дизайн', 'Ермекова Г.С.')
  ('Робототехника', 'Жақыпов Т.Н.')
  ('3D-модельдеу', None)
```

```text
   INNER JOIN               LEFT JOIN
  ┌─────┬─────┐           ┌─────┬─────┐
  │  A  │█████│  B        │█████│█████│  B
  │     │█████│           │█████│█████│
  └─────┴─────┘           └─────┴─────┘
  тек ортақ бөлік         А-ның бәрі + ортақ бөлік
```

| | `INNER JOIN` | `LEFT JOIN` |
|---|---|---|
| Нәтижеде | тек жұбы бар жолдар | сол жақ кестенің **барлық** жолдары |
| Жұбы жоқ болса | жол шықпайды | оң жақ өрістері `NULL` |
| Қашан | «Үйірмелер және олардың жетекшілері» | «Барлық үйірме, жетекшісі болса — аты» |

Кесте ретін ауыстырсақ — басқа сұрақтың жауабы:

```sql
-- Барлық жетекші және олардың үйірмелері (үйірмесі жоқ жетекші де шығады)
SELECT t.name, c.title FROM teachers t LEFT JOIN clubs c ON c.teacher_id = t.id;
```

SQLite 3.39+ нұсқасында `RIGHT JOIN` және `FULL OUTER JOIN` те бар, бірақ іс жүзінде көбіне `LEFT JOIN` пен кесте ретін ауыстыру жеткілікті.

### «Жұбы жоқ» жазбаларды табу: `LEFT JOIN ... WHERE ... IS NULL`

```python
import sqlite3

conn = sqlite3.connect("center59.db")
idle_teachers = conn.execute("""
    SELECT t.name FROM teachers t
    LEFT JOIN clubs c ON c.teacher_id = t.id
    WHERE c.id IS NULL
""").fetchall()
print("Үйірмесі жоқ жетекшілер:", [r[0] for r in idle_teachers])

lonely = conn.execute("""
    SELECT s.name FROM students s
    LEFT JOIN enrollments e ON e.student_id = s.id
    WHERE e.id IS NULL
""").fetchall()
print("Ешқайда жазылмаған оқушылар:", [r[0] for r in lonely])
conn.close()
```

Нәтижесі:

```text
Үйірмесі жоқ жетекшілер: ['Омарова Д.А.']
Ешқайда жазылмаған оқушылар: ['Ерлан']
```

Бұл — өте жиі қолданылатын тәсіл: «тапсырыс бермеген клиенттер», «бірде-бір кітап алмаған оқырмандар», «сабаққа келмегендер».

## 5. Үш кестені біріктіру (M:N байланысы)

«Кім қай үйірмеге жазылған?» сұрағына жауап беру үшін аралық кесте арқылы **үш** кестені біріктіреміз:

```text
students ──(s.id = e.student_id)── enrollments ──(e.club_id = c.id)── clubs
```

```python
import sqlite3

conn = sqlite3.connect("center59.db")
rows = conn.execute("""
    SELECT s.name, s.class, c.title, e.enrolled
    FROM enrollments e
    JOIN students s ON e.student_id = s.id
    JOIN clubs c ON e.club_id = c.id
    ORDER BY s.name, c.title
""").fetchall()
for name, cls, title, date in rows:
    print(f"{name:<9} {cls:<4} {title:<14} {date}")
conn.close()
```

Нәтижесі:

```text
Айгерім   9А   Python         2026-09-05
Айгерім   9А   Робототехника  2026-09-12
Бауыржан  9А   Python         2026-09-05
Дәулет    9Ә   Python         2026-09-07
Дәулет    9Ә   Веб-дизайн     2026-10-01
Сымбат    9Ә   Робототехника  2026-09-15
```

### Төрт кесте: оқушы → үйірме → жетекші

```python
import sqlite3

conn = sqlite3.connect("center59.db")
student = "Айгерім"
rows = conn.execute("""
    SELECT c.title, COALESCE(t.name, 'бекітілмеген') AS teacher
    FROM students s
    JOIN enrollments e ON e.student_id = s.id
    JOIN clubs c ON c.id = e.club_id
    LEFT JOIN teachers t ON t.id = c.teacher_id
    WHERE s.name = ?
""", (student,)).fetchall()
print(f"{student} үйірмелері:", rows)
conn.close()
```

Нәтижесі:

```text
Айгерім үйірмелері: [('Python', 'Ермекова Г.С.'), ('Робототехника', 'Жақыпов Т.Н.')]
```

`COALESCE(a, b)` — `a` `NULL` болса, `b` мәнін қайтарады. Жетекшісіз үйірме үшін «бекітілмеген» жазуы шығады.

## 6. `JOIN` + `GROUP BY` — есептер

```python
import sqlite3

conn = sqlite3.connect("center59.db")

print("Әр үйірмедегі оқушылар саны:")
for title, count in conn.execute("""
    SELECT c.title, COUNT(e.id) AS cnt
    FROM clubs c
    LEFT JOIN enrollments e ON e.club_id = c.id
    GROUP BY c.id
    ORDER BY cnt DESC, c.title
"""):
    print(f"  {title:<14} {count}")

print("Жетекшілердің жүктемесі:")
for name, clubs, students in conn.execute("""
    SELECT t.name, COUNT(DISTINCT c.id), COUNT(e.id)
    FROM teachers t
    LEFT JOIN clubs c ON c.teacher_id = t.id
    LEFT JOIN enrollments e ON e.club_id = c.id
    GROUP BY t.id
"""):
    print(f"  {name:<14} үйірме: {clubs}, оқушы: {students}")
conn.close()
```

Нәтижесі:

```text
Әр үйірмедегі оқушылар саны:
  Python         3
  Робототехника  2
  Веб-дизайн     1
  3D-модельдеу   0
Жетекшілердің жүктемесі:
  Ермекова Г.С.  үйірме: 2, оқушы: 4
  Жақыпов Т.Н.   үйірме: 1, оқушы: 2
  Омарова Д.А.   үйірме: 0, оқушы: 0
```

`COUNT(e.id)` — `NULL`-ды санамайды, сондықтан бос үйірмеге 0 шығады. Ал `COUNT(*)` бос үйірмеге де 1 берер еді (LEFT JOIN жасаған `NULL` жолын санап) — бұл жиі қате.

## 7. JOIN-ның ішкі логикасы (Python-мен салыстыру)

`INNER JOIN` іс жүзінде «кірістірілген цикл» сияқты:

```python
clubs = [(1, "Python", 1), (2, "Веб-дизайн", 1), (3, "Робототехника", 2), (4, "3D-модельдеу", None)]
teachers = [(1, "Ермекова Г.С."), (2, "Жақыпов Т.Н."), (3, "Омарова Д.А.")]

# INNER JOIN clubs, teachers ON clubs.teacher_id = teachers.id
inner = [(c[1], t[1]) for c in clubs for t in teachers if c[2] == t[0]]
print(inner)

# LEFT JOIN: жұбы болмаса — None
left = []
for c in clubs:
    matches = [t[1] for t in teachers if c[2] == t[0]]
    left += [(c[1], m) for m in matches] or [(c[1], None)]
print(left)
```

Нәтижесі:

```text
[('Python', 'Ермекова Г.С.'), ('Веб-дизайн', 'Ермекова Г.С.'), ('Робототехника', 'Жақыпов Т.Н.')]
[('Python', 'Ермекова Г.С.'), ('Веб-дизайн', 'Ермекова Г.С.'), ('Робототехника', 'Жақыпов Т.Н.'), ('3D-модельдеу', None)]
```

ДҚБЖ мұны индекстер арқылы әлдеқайда жылдам орындайды.

## Жиі кездесетін қателер

**1. `ON` шарты жоқ немесе қате** → әр жол екінші кестенің **әр** жолымен жұптасады (декарттық көбейтінді, `CROSS JOIN`): 4 үйірме × 3 жетекші = 12 мағынасыз жол.

```python
import sqlite3

conn = sqlite3.connect("center59.db")
print("ON-сыз:", conn.execute("SELECT COUNT(*) FROM clubs, teachers").fetchone()[0], "жол")
print("ON-мен:", conn.execute(
    "SELECT COUNT(*) FROM clubs JOIN teachers ON clubs.teacher_id = teachers.id").fetchone()[0], "жол")
conn.close()
```

Нәтижесі:

```text
ON-сыз: 12 жол
ON-мен: 3 жол
```

**2. Бірмәнсіз өріс атауы.** `SELECT name FROM students JOIN teachers ...` — екі кестеде де `name` бар → `ambiguous column name: name`. Дұрыс: `s.name`, `t.name`.

**3. `LEFT JOIN`-нан кейін `WHERE`-де оң жақ кесте шарты.** `LEFT JOIN teachers t ... WHERE t.name LIKE 'Е%'` — `NULL` жолдары сүзіліп, нәтиже `INNER JOIN`-ға айналады. Оң жақ кестенің шартын `ON` ішіне жаз: `LEFT JOIN teachers t ON t.id = c.teacher_id AND t.name LIKE 'Е%'`.

**4. `LEFT JOIN` + `COUNT(*)`** → бос топқа 1 шығады. Дұрыс: `COUNT(оң_жақ.id)`.

**5. Шарттағы өрістерді шатастыру.** `ON c.id = t.id` — үйірме нөмірі мен жетекші нөмірін салыстыру мағынасыз. Әрқашан **FK = PK**: `ON c.teacher_id = t.id`.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| `JOIN` | Екі кестенің жолдарын шарт бойынша біріктіру |
| `ON` | Жолдарды жұптастыру шарты (әдетте FK = PK) |
| `INNER JOIN` | Тек жұбы табылған жолдар |
| `LEFT JOIN` | Сол жақ кестенің барлық жолдары, жұбы жоқ болса — `NULL` |
| Бүркеншік ат (alias) | Кестенің қысқа атауы: `FROM clubs c` |
| Декарттық көбейтінді | `ON`-сыз біріктіру: әр жол әр жолмен |
| `COALESCE(a, b)` | `a` бос болса, `b`-ны қайтару |
| Аралық кесте арқылы біріктіру | M:N байланысын екі `JOIN` арқылы оқу |

## Ескерту

- `FROM a, b WHERE a.x = b.y` жазуы — `INNER JOIN`-ге балама ескі біріктіру синтаксисі; анық `JOIN ... ON` оңай оқылады және шартты кездейсоқ ұмытуға жол бермейді.
- `LEFT JOIN` кезінде оң жақ кестеге қойылатын шартты `WHERE`-ге емес, `ON`-ға жазыңыз: `WHERE c.price > 0` `NULL` бар жолдарды алып тастап, `LEFT JOIN`-ді іс жүзінде `INNER JOIN`-ге айналдырады.
- Екі кестеде аттас өрістер болса (`id`, `name`), нәтижеде оларға бүркеншік ат беріңіз (`t.name AS teacher`) — әйтпесе `sqlite3.Row` арқылы атауымен алғанда тек біреуі қайтарылады.
- `JOIN ... USING (club_id)` — өрістер аттас болғанда `ON a.club_id = b.club_id`-нің қысқа түрі.
- Жылдам біріктіру үшін сыртқы кілттерге индекс жасаңыз: `CREATE INDEX idx_clubs_teacher ON clubs(teacher_id);` — SQLite оларды автоматты түрде жасамайды.
- Құжаттама: https://www.sqlite.org/lang_select.html#joinclause
