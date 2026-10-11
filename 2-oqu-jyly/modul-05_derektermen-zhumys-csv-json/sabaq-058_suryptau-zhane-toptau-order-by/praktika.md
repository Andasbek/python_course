# Сабақ 58. Практикалық тапсырмалар: ORDER BY, GROUP BY, агрегат функциялары

**Дайындық.** «Мектеп асханасы» сатылымдарының қорын жаса:

```python
import sqlite3

SALES = [
    ("2027-01-18", "Самса", "тағам", 2, 350, "7А"),
    ("2027-01-18", "Шай", "сусын", 3, 100, "7А"),
    ("2027-01-18", "Бәліш", "тағам", 1, 300, "9Ә"),
    ("2027-01-18", "Шырын", "сусын", 2, 220, "9Ә"),
    ("2027-01-19", "Самса", "тағам", 4, 350, "10А"),
    ("2027-01-19", "Тоқаш", "тағам", 3, 150, "5Б"),
    ("2027-01-19", "Шай", "сусын", 5, 100, "5Б"),
    ("2027-01-19", "Алма", "жеміс", 2, 120, "7А"),
    ("2027-01-20", "Самса", "тағам", 3, 350, "9Ә"),
    ("2027-01-20", "Шырын", "сусын", 4, 220, "10А"),
    ("2027-01-20", "Өрік", "жеміс", 2, 180, "5Б"),
    ("2027-01-20", "Тоқаш", "тағам", 6, 150, "7А"),
    ("2027-01-21", "Бәліш", "тағам", 2, 300, "10А"),
    ("2027-01-21", "Алма", "жеміс", 5, 120, "9Ә"),
    ("2027-01-21", "Шай", "сусын", 2, 100, "10А"),
]

conn = sqlite3.connect("canteen.db")
conn.executescript("""
    DROP TABLE IF EXISTS sales;
    CREATE TABLE sales (
        id INTEGER PRIMARY KEY,
        sale_date TEXT NOT NULL,
        product TEXT NOT NULL,
        category TEXT NOT NULL,
        qty INTEGER NOT NULL CHECK (qty > 0),
        price INTEGER NOT NULL,
        class TEXT
    );
""")
conn.executemany(
    "INSERT INTO sales (sale_date, product, category, qty, price, class) VALUES (?, ?, ?, ?, ?, ?)", SALES
)
conn.commit()
conn.close()
print("canteen.db дайын: 15 сатылым")
```

Кеңес: бір сатылымның сомасы = `qty * price`. SQL-да оны тікелей есептеуге болады: `SUM(qty * price)`.

## Базалық деңгей

### 1-тапсырма. Сұрыптау

1. Барлық сатылымдарды сомасы (`qty * price AS total`) бойынша кему ретімен шығар.
2. Күні бойынша өсу, күні бірдей болса — өнім атауы бойынша сұрыпта.
3. Ең қымбат 3 сатылымды (`LIMIT`) шығар.

```text
Шығару (3-сұраныс):
2027-01-19 Самса ×4 = 1400 ₸
2027-01-20 Самса ×3 = 1050 ₸
2027-01-20 Тоқаш ×6 = 900 ₸
```

### 2-тапсырма. Жалпы статистика

Бір сұраныспен шығар: сатылымдар саны, сатылған тауарлардың жалпы саны, жалпы түсім, бір сатылымның орташа сомасы (1 таңбаға дейін), ең арзан және ең қымбат тауар бағасы, әртүрлі өнімдер саны.

```text
Шығару:
Сатылымдар: 15
Сатылған дана: 46
Түсім: 8920 ₸
Орташа чек: 594.7 ₸
Баға: 100..350 ₸
Өнім түрлері: 7
```

### 3-тапсырма. Санаттар бойынша

`GROUP BY category` арқылы әр санаттың сатылған данасын және түсімін шығар, түсім бойынша кему ретімен.

```text
Шығару:
тағам   — 21 дана, 5400 ₸
сусын   — 16 дана, 2320 ₸
жеміс   — 9 дана, 1200 ₸
```

### 4-тапсырма. Күндер бойынша

Әр күннің түсімін шығар және ең табысты күнді тап. Нәтижені «мәтіндік диаграмма» түрінде көрсет (әр 200 ₸ — бір `█`).

```text
Шығару:
2027-01-18  1740 ₸  ████████
2027-01-19  2590 ₸  ████████████
...
Ең табысты күн: 2027-01-20
```

## Кеңейтілген деңгей

### 5-тапсырма. HAVING

1. Жалпы 5 данадан көп сатылған өнімдер.
2. Асханаға 2000 ₸-ден көп ақша жұмсаған сыныптар.
3. Кемінде 2 түрлі күнде сатылған өнімдер (`COUNT(DISTINCT sale_date)`).

### 6-тапсырма. Беттерге бөлу

`get_page(page, size=5)` функциясын жаз: `ORDER BY sale_date, id` бойынша сұрыпталған сатылымдардың көрсетілген бетін қайтарады. Жалпы беттер санын да есепте (`COUNT(*)` арқылы). Барлық беттерді кезекпен шығар.

### 7-тапсырма. Сыныптар рейтингі

Әр сынып үшін: сатып алу саны, жалпы сомасы, ең көп алған өнімі. Ең көп алған өнімді табу үшін бір сұраныс жеткіліксіз болуы мүмкін — Python-мен біріктір.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Қазақ әліпбиі бойынша сұрыптау

SQLite қазақ әріптерін әліпби ретімен сұрыптамайды. Барлық өнім атауларын SQL арқылы ал (`SELECT DISTINCT product`), сосын Python-да қазақ әліпбиі бойынша сұрыпта:

```text
АӘБВГҒДЕЁЖЗИЙКҚЛМНҢОӨПРСТУҰҮФХҺЦЧШЩЪЫІЬЭЮЯ
```

Нәтижені SQLite-тың `ORDER BY product` нәтижесімен салыстыр.

Қосымша: `conn.create_collation("kazakh", func)` арқылы SQLite-қа өз салыстыру ережеңді тіркеп, `ORDER BY product COLLATE kazakh` жазып көр.

### 9-тапсырма. Апта күндері

SQLite-тың `strftime('%w', sale_date)` функциясы апта күнінің нөмірін береді (0 — жексенбі). Апта күндері бойынша түсімді топтап, қазақша атауларымен шығар (дүйсенбі, сейсенбі, ...).

## Үй тапсырмасы

1. 56–57-сабақтардағы өз каталогыңа кемінде 3 «аналитикалық» сұраныс қос: топ-5, топтау + агрегат, `HAVING`.
2. 50-сабақтағы `journal.csv` деректерін SQLite-қа импорттап (56-сабақ, 5-тапсырма), сыныптар бойынша әр пәннің орташасын **бір SQL сұранысымен** есепте. Python нұсқасымен салыстыр.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
print("1) Сомасы бойынша:")
for d, p, q, total in conn.execute(
    "SELECT sale_date, product, qty, qty * price AS total FROM sales ORDER BY total DESC"
):
    print(f"   {d} {p} ×{q} = {total} ₸")

print("2) Күні, сосын атауы:")
for d, p in conn.execute("SELECT sale_date, product FROM sales ORDER BY sale_date, product"):
    print(f"   {d} {p}")

print("3) Үздік 3:")
for d, p, q, total in conn.execute(
    "SELECT sale_date, product, qty, qty * price AS total FROM sales ORDER BY total DESC LIMIT 3"
):
    print(f"   {d} {p} ×{q} = {total} ₸")
conn.close()
```

3-бөліктің нәтижесі:

```text
3) Үздік 3:
   2027-01-19 Самса ×4 = 1400 ₸
   2027-01-20 Самса ×3 = 1050 ₸
   2027-01-20 Тоқаш ×6 = 900 ₸
```

**2-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
row = conn.execute("""
    SELECT COUNT(*), SUM(qty), SUM(qty * price), ROUND(AVG(qty * price), 1),
           MIN(price), MAX(price), COUNT(DISTINCT product)
    FROM sales
""").fetchone()
print("Сатылымдар:", row[0])
print("Сатылған дана:", row[1])
print("Түсім:", row[2], "₸")
print("Орташа чек:", row[3], "₸")
print(f"Баға: {row[4]}..{row[5]} ₸")
print("Өнім түрлері:", row[6])
conn.close()
```

Нәтижесі:

```text
Сатылымдар: 15
Сатылған дана: 46
Түсім: 8920 ₸
Орташа чек: 594.7 ₸
Баға: 100..350 ₸
Өнім түрлері: 7
```

**3-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
for cat, qty, income in conn.execute("""
    SELECT category, SUM(qty), SUM(qty * price) AS income
    FROM sales
    GROUP BY category
    ORDER BY income DESC
"""):
    print(f"{cat:<7} — {qty} дана, {income} ₸")
conn.close()
```

Нәтижесі:

```text
тағам   — 21 дана, 5400 ₸
сусын   — 16 дана, 2320 ₸
жеміс   — 9 дана, 1200 ₸
```

**4-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
days = conn.execute(
    "SELECT sale_date, SUM(qty * price) FROM sales GROUP BY sale_date ORDER BY sale_date"
).fetchall()
for day, income in days:
    print(f"{day}  {income:>4} ₸  {'█' * (income // 200)}")
best = max(days, key=lambda d: d[1])
print("Ең табысты күн:", best[0])

# SQL-дың өзімен:
print(conn.execute(
    "SELECT sale_date FROM sales GROUP BY sale_date ORDER BY SUM(qty * price) DESC LIMIT 1"
).fetchone()[0])
conn.close()
```

Нәтижесі:

```text
2027-01-18  1740 ₸  ████████
2027-01-19  2590 ₸  ████████████
2027-01-20  3190 ₸  ███████████████
2027-01-21  1400 ₸  ███████
Ең табысты күн: 2027-01-20
2027-01-20
```

**5-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
print("1)", conn.execute(
    "SELECT product, SUM(qty) FROM sales GROUP BY product HAVING SUM(qty) > 5 ORDER BY SUM(qty) DESC"
).fetchall())
print("2)", conn.execute(
    "SELECT class, SUM(qty * price) FROM sales GROUP BY class HAVING SUM(qty * price) > 2000"
).fetchall())
print("3)", conn.execute(
    "SELECT product, COUNT(DISTINCT sale_date) FROM sales GROUP BY product HAVING COUNT(DISTINCT sale_date) >= 2"
).fetchall())
conn.close()
```

Нәтижесі:

```text
1) [('Шай', 10), ('Тоқаш', 9), ('Самса', 9), ('Алма', 7), ('Шырын', 6)]
2) [('10А', 3080), ('7А', 2140), ('9Ә', 2390)]
3) [('Алма', 2), ('Бәліш', 2), ('Самса', 3), ('Тоқаш', 2), ('Шай', 3), ('Шырын', 2)]
```

(Тең мәндердің (Тоқаш пен Самса — 9) реті кепілдендірілмейді; тұрақты рет керек болса: `ORDER BY SUM(qty) DESC, product`.)

**6-тапсырма**

```python
import math
import sqlite3

conn = sqlite3.connect("canteen.db")


def get_page(page, size=5):
    offset = (page - 1) * size
    return conn.execute(
        "SELECT id, sale_date, product FROM sales ORDER BY sale_date, id LIMIT ? OFFSET ?",
        (size, offset),
    ).fetchall()


total = conn.execute("SELECT COUNT(*) FROM sales").fetchone()[0]
pages = math.ceil(total / 5)
for p in range(1, pages + 1):
    print(f"{p}/{pages}-бет:", [f"{r[0]}:{r[2]}" for r in get_page(p)])
conn.close()
```

Нәтижесі:

```text
1/3-бет: ['1:Самса', '2:Шай', '3:Бәліш', '4:Шырын', '5:Самса']
2/3-бет: ['6:Тоқаш', '7:Шай', '8:Алма', '9:Самса', '10:Шырын']
3/3-бет: ['11:Өрік', '12:Тоқаш', '13:Бәліш', '14:Алма', '15:Шай']
```

**7-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
summary = conn.execute("""
    SELECT class, COUNT(*), SUM(qty * price) AS spent
    FROM sales GROUP BY class ORDER BY spent DESC
""").fetchall()

for cls, purchases, spent in summary:
    favourite = conn.execute("""
        SELECT product, SUM(qty) AS n FROM sales
        WHERE class = ? GROUP BY product ORDER BY n DESC, product LIMIT 1
    """, (cls,)).fetchone()
    print(f"{cls:<4} сатып алу: {purchases}, сома: {spent} ₸, сүйікті өнімі: {favourite[0]} ({favourite[1]})")
conn.close()
```

Нәтижесі:

```text
10А  сатып алу: 4, сома: 3080 ₸, сүйікті өнімі: Самса (4)
9Ә   сатып алу: 4, сома: 2390 ₸, сүйікті өнімі: Алма (5)
7А   сатып алу: 4, сома: 2140 ₸, сүйікті өнімі: Тоқаш (6)
5Б   сатып алу: 3, сома: 1310 ₸, сүйікті өнімі: Шай (5)
```

**8-тапсырма**

```python
import sqlite3

ALPHABET = "АӘБВГҒДЕЁЖЗИЙКҚЛМНҢОӨПРСТУҰҮФХҺЦЧШЩЪЫІЬЭЮЯ"
ORDER = {ch: i for i, ch in enumerate(ALPHABET)}


def kazakh_key(word):
    """Сөзді әліпбидегі әріп нөмірлерінің тізіміне айналдырады."""
    return [ORDER.get(ch, 100 + ord(ch)) for ch in word.upper()]


def kazakh_collation(a, b):
    ka, kb = kazakh_key(a), kazakh_key(b)
    return (ka > kb) - (ka < kb)      # -1, 0 немесе 1


conn = sqlite3.connect("canteen.db")
products = [r[0] for r in conn.execute("SELECT DISTINCT product FROM sales")]
print("SQLite ORDER BY:", [r[0] for r in conn.execute("SELECT DISTINCT product FROM sales ORDER BY product")])
print("Қазақ әліпбиі:  ", sorted(products, key=kazakh_key))

conn.create_collation("kazakh", kazakh_collation)
print("COLLATE kazakh: ", [r[0] for r in conn.execute(
    "SELECT DISTINCT product FROM sales ORDER BY product COLLATE kazakh")])
conn.close()
```

Нәтижесі:

```text
SQLite ORDER BY: ['Алма', 'Бәліш', 'Самса', 'Тоқаш', 'Шай', 'Шырын', 'Өрік']
Қазақ әліпбиі:   ['Алма', 'Бәліш', 'Өрік', 'Самса', 'Тоқаш', 'Шай', 'Шырын']
COLLATE kazakh:  ['Алма', 'Бәліш', 'Өрік', 'Самса', 'Тоқаш', 'Шай', 'Шырын']
```

Түсіндірме: SQLite «Өрік»-ті соңына қойды, себебі «Ө» әрпінің коды (U+04E8) барлық орыс әріптерінің кодынан үлкен, ал қазақ әліпбиінде «Ө» — «О» мен «П» арасында. Сол сияқты «Құрт» пен «Лимонад» сөздерін SQLite «Лимонад, Құрт» ретімен қояды (Қ = U+049A > Л = U+041B), ал әліпби бойынша «Құрт» бірінші.

**9-тапсырма**

```python
import sqlite3

DAYS = ["жексенбі", "дүйсенбі", "сейсенбі", "сәрсенбі", "бейсенбі", "жұма", "сенбі"]

conn = sqlite3.connect("canteen.db")
for day_no, income in conn.execute("""
    SELECT CAST(strftime('%w', sale_date) AS INTEGER) AS wd, SUM(qty * price)
    FROM sales GROUP BY wd ORDER BY wd
"""):
    print(f"{DAYS[day_no]:<9} {income} ₸")
conn.close()
```

Нәтижесі:

```text
дүйсенбі  1740 ₸
сейсенбі  2590 ₸
сәрсенбі  3190 ₸
бейсенбі  1400 ₸
```

</details>
