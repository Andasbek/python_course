# Сабақ 59. Практикалық тапсырмалар: бірнеше кесте және JOIN

**Дайындық.** «Мектеп кітапханасы» деректер қорын құрып, толтыр (54-сабақтағы схема бойынша):

```python
import sqlite3

conn = sqlite3.connect("library59.db")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    DROP TABLE IF EXISTS loans;
    DROP TABLE IF EXISTS readers;
    DROP TABLE IF EXISTS books;
    DROP TABLE IF EXISTS authors;

    CREATE TABLE authors (id INTEGER PRIMARY KEY, name TEXT NOT NULL UNIQUE, born INTEGER);
    CREATE TABLE books (
        id INTEGER PRIMARY KEY,
        title TEXT NOT NULL,
        year INTEGER,
        author_id INTEGER REFERENCES authors(id)
    );
    CREATE TABLE readers (id INTEGER PRIMARY KEY, name TEXT NOT NULL, class TEXT);
    CREATE TABLE loans (
        id INTEGER PRIMARY KEY,
        book_id INTEGER NOT NULL REFERENCES books(id),
        reader_id INTEGER NOT NULL REFERENCES readers(id),
        issued TEXT NOT NULL,
        returned TEXT
    );

    INSERT INTO authors VALUES
        (1, 'Мұхтар Әуезов', 1897), (2, 'Ілияс Есенберлин', 1915),
        (3, 'Бердібек Соқпақбаев', 1924), (4, 'Мағжан Жұмабаев', 1893),
        (5, 'Оралхан Бөкей', 1943);
    INSERT INTO books VALUES
        (1, 'Абай жолы', 1942, 1), (2, 'Көксерек', 1929, 1),
        (3, 'Көшпенділер', 1976, 2), (4, 'Менің атым Қожа', 1957, 3),
        (5, 'Балалық шаққа саяхат', 1960, 3), (6, 'Шолпанның күнәсі', 1923, 4),
        (7, 'Python негіздері', 2024, NULL);
    INSERT INTO readers VALUES
        (1, 'Айгерім', '9А'), (2, 'Бауыржан', '9А'), (3, 'Дәулет', '9Ә'),
        (4, 'Сымбат', '9Ә'), (5, 'Ерлан', '10А');
    INSERT INTO loans (book_id, reader_id, issued, returned) VALUES
        (1, 1, '2027-01-05', '2027-01-15'),
        (4, 1, '2027-01-16', NULL),
        (3, 2, '2027-01-10', NULL),
        (4, 3, '2026-12-20', '2027-01-08'),
        (2, 3, '2027-01-12', '2027-01-20'),
        (5, 4, '2027-01-18', NULL),
        (1, 4, '2026-12-01', '2026-12-25');
""")
conn.commit()
conn.close()
print("library59.db дайын")
```

## Базалық деңгей

### 1-тапсырма. Кітаптар және авторлар (INNER JOIN)

Әр кітаптың атауын, жылын және авторының атын шығар. Кесте бүркеншік аттарын қолдан.

```text
Шығару:
Абай жолы (1942) — Мұхтар Әуезов
Көксерек (1929) — Мұхтар Әуезов
Көшпенділер (1976) — Ілияс Есенберлин
...
```

Неше кітап шықты? «Python негіздері» неге шықпады?

### 2-тапсырма. Барлық кітаптар (LEFT JOIN)

1-тапсырманы `LEFT JOIN` арқылы қайтала. Авторы жоқ кітапқа «автор белгісіз» деп жаз (`COALESCE`).

### 3-тапсырма. Кім не оқып жатыр?

Әлі қайтарылмаған (`returned IS NULL`) кітаптар тізімі: оқырманның аты, сыныбы, кітап атауы, алған күні. Үш кестені біріктір.

```text
Шығару:
Айгерім (9А) — «Менің атым Қожа», 2027-01-16
Бауыржан (9А) — «Көшпенділер», 2027-01-10
Сымбат (9Ә) — «Балалық шаққа саяхат», 2027-01-18
```

### 4-тапсырма. Оқырманның тарихы

Пайдаланушы оқырманның атын енгізеді — бағдарлама оның алған барлық кітаптарын күні бойынша шығарады, қайтарылғанын/қайтарылмағанын белгілейді.

```text
Енгізу:
Оқырман: Дәулет
Шығару:
2026-12-20 «Менің атым Қожа» — қайтарылды 2027-01-08
2027-01-12 «Көксерек» — қайтарылды 2027-01-20
```

## Кеңейтілген деңгей

### 5-тапсырма. Жұбы жоқ жазбалар

1. Кітапханадан бірде-бір кітап алмаған оқырмандар.
2. Ешкім алмаған кітаптар.
3. Кітабы кітапханада жоқ авторлар.

### 6-тапсырма. Статистика (JOIN + GROUP BY)

1. Әр кітаптың неше рет берілгені (0 рет берілгендерімен қоса), кему ретімен.
2. Әр оқырманның алған кітап саны және қазір қолындағы кітап саны.
3. Әр автордың кітаптары неше рет оқылды (авторлар рейтингі).
4. Сыныптар бойынша оқылған кітаптар саны.

```text
Шығару (3-сұраныс):
Мұхтар Әуезов — 3
Бердібек Соқпақбаев — 3
Ілияс Есенберлин — 1
Мағжан Жұмабаев — 0
Оралхан Бөкей — 0
```

### 7-тапсырма. Мерзімі өткен кітаптар

Кітап 14 күнге беріледі. «Бүгін» = `2027-01-25` деп алып, мерзімі өткен (қайтарылмаған және 14 күннен көп уақыт өткен) кітаптарды шығар: оқырман, кітап, неше күн кешіккені. Кеңес: SQLite-та `julianday('2027-01-25') - julianday(issued)` — күндер айырмасы.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Бірнеше автор (M:N)

Шын өмірде кітаптың бірнеше авторы болуы мүмкін. Модельді өзгерт: `books.author_id` орнына `book_authors(book_id, author_id)` аралық кестесін жаса. Деректерді көшір (`INSERT INTO book_authors SELECT id, author_id FROM books WHERE author_id IS NOT NULL`), «Python негіздері» кітабына екі автор қос (жаңа авторлар), сосын әр кітапты барлық авторларымен бір жолда шығар (`GROUP_CONCAT`).

```text
Шығару:
Python негіздері — Ақылбек Сағатов, Жанар Әлиева
Абай жолы — Мұхтар Әуезов
...
```

### 9-тапсырма. «Сені де қызықтыруы мүмкін»

Ұсыныс жүйесінің қарапайым нұсқасы: берілген оқырман оқыған кітаптарды **басқа** кім оқығанын тауып, сол оқырмандар оқыған, бірақ берілген оқырман әлі оқымаған кітаптарды ұсын. (Кеңес: `loans` кестесін өзімен біріктіру — self-join: `FROM loans a JOIN loans b ON a.book_id = b.book_id`.)

## Үй тапсырмасы

1. 54-сабақта жобалаған өз деректер қорыңды (немесе «Үйірмелер орталығы» кестесін) кемінде 3 кестемен құрып, толтыр. 5 түрлі `JOIN` сұранысын жаз: INNER, LEFT, үш кесте, «жұбы жоқ», JOIN + GROUP BY.
2. Ойлан: «Оқушылар базасы» қосымшасында (62–63-сабақтар) қандай JOIN сұраныстары керек болады?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–2-тапсырмалар**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
inner = conn.execute("""
    SELECT b.title, b.year, a.name
    FROM books b JOIN authors a ON b.author_id = a.id
""").fetchall()
for title, year, author in inner:
    print(f"{title} ({year}) — {author}")
print("INNER JOIN:", len(inner), "кітап\n")

for title, author in conn.execute("""
    SELECT b.title, COALESCE(a.name, 'автор белгісіз')
    FROM books b LEFT JOIN authors a ON b.author_id = a.id
"""):
    print(f"{title} — {author}")
conn.close()
```

Нәтижесі:

```text
Абай жолы (1942) — Мұхтар Әуезов
Көксерек (1929) — Мұхтар Әуезов
Көшпенділер (1976) — Ілияс Есенберлин
Менің атым Қожа (1957) — Бердібек Соқпақбаев
Балалық шаққа саяхат (1960) — Бердібек Соқпақбаев
Шолпанның күнәсі (1923) — Мағжан Жұмабаев
INNER JOIN: 6 кітап

Абай жолы — Мұхтар Әуезов
Көксерек — Мұхтар Әуезов
Көшпенділер — Ілияс Есенберлин
Менің атым Қожа — Бердібек Соқпақбаев
Балалық шаққа саяхат — Бердібек Соқпақбаев
Шолпанның күнәсі — Мағжан Жұмабаев
Python негіздері — автор белгісіз
```

«Python негіздері» INNER JOIN-да шықпады, себебі оның `author_id = NULL` — жұбы жоқ.

**3-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
for name, cls, title, issued in conn.execute("""
    SELECT r.name, r.class, b.title, l.issued
    FROM loans l
    JOIN readers r ON l.reader_id = r.id
    JOIN books b ON l.book_id = b.id
    WHERE l.returned IS NULL
    ORDER BY r.name
"""):
    print(f"{name} ({cls}) — «{title}», {issued}")
conn.close()
```

Нәтижесі:

```text
Айгерім (9А) — «Менің атым Қожа», 2027-01-16
Бауыржан (9А) — «Көшпенділер», 2027-01-10
Сымбат (9Ә) — «Балалық шаққа саяхат», 2027-01-18
```

**4-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
reader = input("Оқырман: ").strip()
rows = conn.execute("""
    SELECT l.issued, b.title, l.returned
    FROM loans l
    JOIN readers r ON l.reader_id = r.id
    JOIN books b ON l.book_id = b.id
    WHERE r.name = ?
    ORDER BY l.issued
""", (reader,)).fetchall()
if not rows:
    print("Бұл оқырман кітап алмаған немесе мұндай оқырман жоқ")
for issued, title, returned in rows:
    status = f"қайтарылды {returned}" if returned else "әлі оқып жүр"
    print(f"{issued} «{title}» — {status}")
conn.close()
```

**5-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
q1 = conn.execute("""
    SELECT r.name FROM readers r LEFT JOIN loans l ON l.reader_id = r.id WHERE l.id IS NULL
""").fetchall()
q2 = conn.execute("""
    SELECT b.title FROM books b LEFT JOIN loans l ON l.book_id = b.id WHERE l.id IS NULL
""").fetchall()
q3 = conn.execute("""
    SELECT a.name FROM authors a LEFT JOIN books b ON b.author_id = a.id WHERE b.id IS NULL
""").fetchall()
print("Кітап алмағандар:", [r[0] for r in q1])
print("Ешкім алмаған кітаптар:", [r[0] for r in q2])
print("Кітабы жоқ авторлар:", [r[0] for r in q3])
conn.close()
```

Нәтижесі:

```text
Кітап алмағандар: ['Ерлан']
Ешкім алмаған кітаптар: ['Шолпанның күнәсі', 'Python негіздері']
Кітабы жоқ авторлар: ['Оралхан Бөкей']
```

**6-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
print("1) Кітаптар:")
for title, n in conn.execute("""
    SELECT b.title, COUNT(l.id) AS n FROM books b
    LEFT JOIN loans l ON l.book_id = b.id
    GROUP BY b.id ORDER BY n DESC, b.id
"""):
    print(f"   {title} — {n}")

print("2) Оқырмандар:")
for name, total, now in conn.execute("""
    SELECT r.name, COUNT(l.id), SUM(CASE WHEN l.id IS NOT NULL AND l.returned IS NULL THEN 1 ELSE 0 END)
    FROM readers r LEFT JOIN loans l ON l.reader_id = r.id
    GROUP BY r.id
"""):
    print(f"   {name}: барлығы {total}, қолында {now}")

print("3) Авторлар:")
for name, n in conn.execute("""
    SELECT a.name, COUNT(l.id) AS n FROM authors a
    LEFT JOIN books b ON b.author_id = a.id
    LEFT JOIN loans l ON l.book_id = b.id
    GROUP BY a.id ORDER BY n DESC, a.id
"""):
    print(f"   {name} — {n}")

print("4) Сыныптар:")
for cls, n in conn.execute("""
    SELECT r.class, COUNT(l.id) FROM readers r
    LEFT JOIN loans l ON l.reader_id = r.id
    GROUP BY r.class ORDER BY r.class
"""):
    print(f"   {cls} — {n}")
conn.close()
```

Нәтижесі:

```text
1) Кітаптар:
   Абай жолы — 2
   Менің атым Қожа — 2
   Көксерек — 1
   Көшпенділер — 1
   Балалық шаққа саяхат — 1
   Шолпанның күнәсі — 0
   Python негіздері — 0
2) Оқырмандар:
   Айгерім: барлығы 2, қолында 1
   Бауыржан: барлығы 1, қолында 1
   Дәулет: барлығы 2, қолында 0
   Сымбат: барлығы 2, қолында 1
   Ерлан: барлығы 0, қолында 0
3) Авторлар:
   Мұхтар Әуезов — 3
   Бердібек Соқпақбаев — 3
   Ілияс Есенберлин — 1
   Мағжан Жұмабаев — 0
   Оралхан Бөкей — 0
4) Сыныптар:
   10А — 0
   9А — 3
   9Ә — 4
```

`CASE WHEN ... THEN ... ELSE ... END` — SQL-дағы «егер»: шартқа сай жолдарды санау үшін ыңғайлы.

**7-тапсырма**

```python
import sqlite3

TODAY = "2027-01-25"
conn = sqlite3.connect("library59.db")
rows = conn.execute("""
    SELECT r.name, b.title, CAST(julianday(?) - julianday(l.issued) AS INTEGER) - 14 AS late
    FROM loans l
    JOIN readers r ON r.id = l.reader_id
    JOIN books b ON b.id = l.book_id
    WHERE l.returned IS NULL AND julianday(?) - julianday(l.issued) > 14
    ORDER BY late DESC
""", (TODAY, TODAY)).fetchall()
for name, title, late in rows:
    print(f"{name} — «{title}»: {late} күн кешікті")
conn.close()
```

Нәтижесі:

```text
Бауыржан — «Көшпенділер»: 1 күн кешікті
```

(Айгерім 9 күн, Сымбат 7 күн бұрын алған — мерзімі әлі өтпеген.)

**8-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
conn.executescript("""
    DROP TABLE IF EXISTS book_authors;
    CREATE TABLE book_authors (
        book_id INTEGER NOT NULL REFERENCES books(id),
        author_id INTEGER NOT NULL REFERENCES authors(id),
        PRIMARY KEY (book_id, author_id)
    );
    INSERT INTO book_authors SELECT id, author_id FROM books WHERE author_id IS NOT NULL;
    INSERT OR IGNORE INTO authors (id, name, born) VALUES (6, 'Ақылбек Сағатов', 1990), (7, 'Жанар Әлиева', 1992);
    INSERT OR IGNORE INTO book_authors VALUES (7, 6), (7, 7);
""")
conn.commit()
for title, authors in conn.execute("""
    SELECT b.title, GROUP_CONCAT(a.name, ', ')
    FROM books b
    JOIN book_authors ba ON ba.book_id = b.id
    JOIN authors a ON a.id = ba.author_id
    GROUP BY b.id
    ORDER BY COUNT(*) DESC, b.id
"""):
    print(f"{title} — {authors}")
conn.close()
```

Нәтижесі:

```text
Python негіздері — Ақылбек Сағатов, Жанар Әлиева
Абай жолы — Мұхтар Әуезов
Көксерек — Мұхтар Әуезов
Көшпенділер — Ілияс Есенберлин
Менің атым Қожа — Бердібек Соқпақбаев
Балалық шаққа саяхат — Бердібек Соқпақбаев
Шолпанның күнәсі — Мағжан Жұмабаев
```

Ескерту: толық көшу үшін `books.author_id` бағанын алып тастау керек (SQLite 3.35+: `ALTER TABLE books DROP COLUMN author_id`, бірақ FK бағанын алу үшін кестені қайта құру қажет болуы мүмкін). `GROUP_CONCAT` ішіндегі есімдердің реті кепілдендірілмеген.

**9-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
reader = "Айгерім"
rows = conn.execute("""
    SELECT DISTINCT b.title
    FROM readers me
    JOIN loans a ON a.reader_id = me.id                -- мен оқыған кітаптар
    JOIN loans other ON other.book_id = a.book_id       -- сол кітапты оқығандар
        AND other.reader_id <> me.id
    JOIN loans rec ON rec.reader_id = other.reader_id   -- олар оқыған басқа кітаптар
    JOIN books b ON b.id = rec.book_id
    WHERE me.name = ?
      AND rec.book_id NOT IN (SELECT book_id FROM loans WHERE reader_id = me.id)
    ORDER BY b.title
""", (reader,)).fetchall()
print(f"{reader}, сені де қызықтыруы мүмкін:", [r[0] for r in rows])
conn.close()
```

Нәтижесі:

```text
Айгерім, сені де қызықтыруы мүмкін: ['Балалық шаққа саяхат', 'Көксерек']
```

Түсіндірме: Айгерім «Абай жолы» мен «Менің атым Қожа» кітаптарын оқыды. «Абай жолы»-н Сымбат оқыған (ол «Балалық шаққа саяхат»-ты да алған), «Менің атым Қожа»-ны Дәулет оқыған (ол «Көксерек»-ті де алған). Ішкі `SELECT` (ішкі сұраныс) — Айгерім бұрын оқығандарды алып тастайды.

</details>
