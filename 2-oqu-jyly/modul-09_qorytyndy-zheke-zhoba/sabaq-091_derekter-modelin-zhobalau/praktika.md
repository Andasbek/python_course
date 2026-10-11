# Сабақ 91. Практикалық тапсырмалар: деректер моделін жобалау

## Базалық деңгей

### 1-тапсырма. Мәндерді табу
ТТ үзіндісін оқып, **мәндерді**, олардың **атрибуттарын** және **байланыстарды** кесте түрінде жазыңыз.

> «Мектеп кітапханасы» қосымшасы. Кітапханашы **кітаптарды** тіркейді: атауы, авторы, шыққан жылы, данасы. Әр кітаптың бір немесе бірнеше **авторы** болуы мүмкін. **Оқушы** (аты-жөні, сыныбы) кітапты **алады** және **қайтарады**; алған күні мен қайтарған күні жазылады.

Нәтиже үлгісі:

| Мән | Атрибуттар | Байланыстар |
|---|---|---|
| ... | ... | ... |

*Кеңес:* «алады/қайтарады» — бұл жеке мән (`loan` — беру жазбасы) болуы мүмкін, өйткені оның өз атрибуттары (күндері) бар.

### 2-тапсырма. Байланыс түрін анықтау
Әр жұп үшін байланыс түрін (1:1, 1:N, N:M) жазып, сыртқы кілт қай жерде болатынын көрсетіңіз:
1. Сынып — Оқушы
2. Оқушы — Пән
3. Ел — Астана (елорда)
4. Ойыншы — Ойын нәтижесі (рекорд)
5. Фильм — Актер
6. Викторина — Сұрақ

### 3-тапсырма. «Жаман» кестені түзету
Келесі кестеде қандай қателер бар? Оны 3НФ-қа келтіріп, жаңа кестелер тізімін жазыңыз.

| order_id | customer | phone | products | total | city | city_region |
|---|---|---|---|---|---|---|
| 1 | Дана | 87011112233 | Дәптер, Қалам | 850 | Алматы | Алматы облысы... |
| 2 | Дана | 87011112233 | Қалам | 150 | Алматы | ... |

### 4-тапсырма. CREATE TABLE жазу
2-тапсырмадағы «Викторина — Сұрақ» байланысы үшін SQLite кестелерін құратын Python бағдарламасын жазыңыз. Сұрақта: мәтін, 4 жауап нұсқасы, дұрыс жауап нөмірі (1–4) болсын. `CHECK` шектеуін қолданыңыз. Бағдарлама кестелер тізімін шығарсын.

Шығару мысалы:
```text
Кестелер: ['quiz', 'question']
```

## Кеңейтілген деңгей

### 5-тапсырма. N:M байланыс + сұраныс
`student`, `club`, `enrollment` кестелерін құрып, 3 оқушы мен 3 үйірмені толтырыңыз. Келесі сұрауларды орындаңыз:
- «Робототехника» үйірмесіне баратын оқушылар;
- әр оқушы неше үйірмеге баратыны (`GROUP BY`);
- бір оқушыны бір үйірмеге екі рет жазуға тырысқанда не болатынын тексеріңіз (`IntegrityError`).

### 6-тапсырма. Сыртқы кілтті тексеру
`PRAGMA foreign_keys = ON` қосылған кезде бар емес санатқа операция қосуға тырысатын бағдарлама жазыңыз. Қатені `try/except sqlite3.IntegrityError` арқылы ұстап, қазақша хабарлама шығарыңыз.

Шығару мысалы:
```text
Қате: мұндай санат жоқ (FOREIGN KEY constraint failed)
```

### 7-тапсырма. Өз жобаңыздың деректер моделі (міндетті)
Өз қорытынды жобаңыз үшін:
1. ТТ-дан мәндер тізімін жазыңыз (кемінде 2–3 мән).
2. ER-сызбаны қағазға немесе draw.io-да салыңыз, суретін `docs/er.png` ретінде сақтаңыз.
3. Әр кесте үшін деректер сөздігін толтырыңыз (төмендегі шаблон).
4. `schema.sql` немесе `database.py` ішінде схеманы жазып, бос деректер қорын құрыңыз.
5. ТТ-дағы 3 негізгі сұраққа SQL сұранысын жазып, модельдің жеткілікті екенін тексеріңіз.

Деректер сөздігінің шаблоны:

| Өріс | Түрі | Шектеу | Сипаттама |
|---|---|---|---|
| id | INTEGER | PK | ... |

> Жобаңыз ойын немесе API-қосымша болса және SQLite қолданбаса — JSON құрылымын (кілттер, түрлері, мысал) осы шаблон бойынша сипаттаңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Схеманы автоматты сипаттау
Кез келген `.db` файлын ашып, ондағы барлық кесте мен өрістерді деректер сөздігі түрінде (markdown кесте) шығаратын `describe_db.py` скриптін жазыңыз. `PRAGMA table_info(кесте)` және `PRAGMA foreign_key_list(кесте)` қолданыңыз.

Шығару мысалы:
```text
### operation
| Өріс | Түрі | NOT NULL | PK | FK |
|---|---|---|---|---|
| id | INTEGER | 0 | 1 |  |
| category_id | INTEGER | 1 | 0 | category.id |
```

### 9-тапсырма. Рекурсивті байланыс
«Отбасы ағашы» жобасы: әр адамның әкесі мен анасы да сол `person` кестесінде. Кестені жобалап, берілген адамның атасын (әкесінің әкесін) табатын SQL сұранысын жазыңыз.

## Үй тапсырмасы
1. Өз жобаңыздың ER-сызбасы мен деректер сөздігін аяқтап, `docs/data_model.md` файлына жазыңыз.
2. Схеманы тексеретін 3 SQL сұранысын (немесе JSON мысал файлын) дайындаңыз.
3. Келесі сабаққа: жобаңыздың басты экраны қандай болатынын ойлап, қағазға қолмен эскиз салып келіңіз.

## Бағалау критерийлері
Деректер моделі қорытынды жобаның «техникалық орындалу» бөлігіне кіреді. Сабақтағы жұмыс практикалық жұмыс критерийлері бойынша бағаланады:

| Критерий | Үлес | Не тексеріледі |
|---|---|---|
| Модельдің дұрыстығы және ТТ-ға сәйкестігі | 40% | барлық мән бар, байланыстар дұрыс, ТТ сұрақтарына жауап береді |
| Шешімнің ұтымдылығы | 25% | 3НФ, қайталау жоқ, дұрыс деректер түрлері мен шектеулер |
| Құжаттаудың оқылымдылығы | 20% | деректер сөздігі, ER-сызба анық, атаулар түсінікті |
| Өз бетінше және уақытында орындау | 15% | сабақ соңына дейін схема құрылды |

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.**

| Мән | Атрибуттар | Байланыстар |
|---|---|---|
| book | id, title, year, copies | N:M author (аралық `book_author`) |
| author | id, full_name | N:M book |
| student | id, first_name, last_name, grade | 1:N loan |
| loan | id, book_id, student_id, taken_date, returned_date (NULL болуы мүмкін) | N:1 book, N:1 student |

**2-тапсырма.** 1) 1:N, FK `student.class_id`; 2) N:M, аралық `student_subject`; 3) 1:1, FK `country.capital_id UNIQUE` немесе бір кестеде; 4) 1:N, FK `score.player_id`; 5) N:M, аралық `film_actor`; 6) 1:N, FK `question.quiz_id`.

**3-тапсырма.** Қателер: `products` атомар емес (1НФ бұзылған); `customer, phone` қайталанады; `city_region` қалаға тәуелді (3НФ бұзылған); `total` есептелетін мән. Кестелер: `customer(id, name, phone)`, `city(id, name, region)`, `product(id, title, price)`, `orders(id, customer_id, city_id, order_date)`, `order_item(order_id, product_id, quantity, PK(order_id, product_id))`. Жалпы сома `SUM(price*quantity)` арқылы есептеледі.

**4-тапсырма.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
CREATE TABLE quiz (
    id    INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL UNIQUE
);
CREATE TABLE question (
    id       INTEGER PRIMARY KEY AUTOINCREMENT,
    quiz_id  INTEGER NOT NULL REFERENCES quiz(id) ON DELETE CASCADE,
    text     TEXT NOT NULL,
    option1  TEXT NOT NULL,
    option2  TEXT NOT NULL,
    option3  TEXT NOT NULL,
    option4  TEXT NOT NULL,
    correct  INTEGER NOT NULL CHECK (correct BETWEEN 1 AND 4)
);
""")
names = conn.execute(
    "SELECT name FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%'"
).fetchall()
print("Кестелер:", [n[0] for n in names])
```
(Нұсқаларды жеке `option(question_id, text, is_correct)` кестесіне шығару да дұрыс — онда нұсқа саны еркін болады.)

**5-тапсырма.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT NOT NULL);
CREATE TABLE club (id INTEGER PRIMARY KEY, title TEXT NOT NULL UNIQUE);
CREATE TABLE enrollment (
    student_id INTEGER REFERENCES student(id),
    club_id    INTEGER REFERENCES club(id),
    PRIMARY KEY (student_id, club_id)
);
INSERT INTO student VALUES (1, 'Әлихан'), (2, 'Мадина'), (3, 'Тимур');
INSERT INTO club VALUES (1, 'Python'), (2, 'Робототехника'), (3, 'Шахмат');
INSERT INTO enrollment VALUES (1, 1), (1, 2), (2, 2), (3, 1), (3, 3), (2, 1);
""")

rows = conn.execute("""
    SELECT s.name FROM student s
    JOIN enrollment e ON e.student_id = s.id
    JOIN club c ON c.id = e.club_id
    WHERE c.title = 'Робототехника'
""").fetchall()
print("Робототехника:", [r[0] for r in rows])

for name, cnt in conn.execute("""
    SELECT s.name, COUNT(e.club_id) FROM student s
    LEFT JOIN enrollment e ON e.student_id = s.id
    GROUP BY s.id ORDER BY s.name
"""):
    print(f"{name}: {cnt} үйірме")

try:
    conn.execute("INSERT INTO enrollment VALUES (1, 1)")
except sqlite3.IntegrityError as err:
    print("Қайта жазуға болмайды:", err)
```
Нәтижесі:
```text
Робототехника: ['Әлихан', 'Мадина']
Мадина: 2 үйірме
Тимур: 2 үйірме
Әлихан: 2 үйірме
Қайта жазуға болмайды: UNIQUE constraint failed: enrollment.student_id, enrollment.club_id
```
(«Әлихан» соңында тұр, себебі SQLite әріптерді Юникод коды бойынша салыстырады: «Ә» кириллицаның негізгі әріптерінен кейін келеді — бұл жақсы талқылау сұрағы.)

**6-тапсырма.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
CREATE TABLE category (id INTEGER PRIMARY KEY, title TEXT);
CREATE TABLE operation (id INTEGER PRIMARY KEY,
                        category_id INTEGER NOT NULL REFERENCES category(id),
                        amount INTEGER NOT NULL);
INSERT INTO category VALUES (1, 'Тамақ');
""")
try:
    conn.execute("INSERT INTO operation (category_id, amount) VALUES (5, 1000)")
except sqlite3.IntegrityError as err:
    print(f"Қате: мұндай санат жоқ ({err})")
```

**7-тапсырма.** Жеке тексеріледі: әр оқушымен 2–3 минут сөйлесіп, «ТТ-дағы мына мүмкіндік модельдің қай жерінде сақталады?» деп сұраңыз.

**8-тапсырма.**
```python
import sqlite3
import sys


def describe(path):
    """Деректер қорының барлық кестесін markdown түрінде сипаттайды."""
    conn = sqlite3.connect(path)
    tables = conn.execute(
        "SELECT name FROM sqlite_master WHERE type='table' "
        "AND name NOT LIKE 'sqlite_%' ORDER BY name"
    ).fetchall()
    for (table,) in tables:
        fks = {}
        for fk in conn.execute(f"PRAGMA foreign_key_list({table})"):
            # fk: (id, seq, table, from, to, on_update, on_delete, match)
            fks[fk[3]] = f"{fk[2]}.{fk[4]}"
        print(f"### {table}")
        print("| Өріс | Түрі | NOT NULL | PK | FK |")
        print("|---|---|---|---|---|")
        for col in conn.execute(f"PRAGMA table_info({table})"):
            # col: (cid, name, type, notnull, default, pk)
            print(f"| {col[1]} | {col[2]} | {col[3]} | {col[5]} | {fks.get(col[1], '')} |")
        print()
    conn.close()


if __name__ == "__main__":
    describe(sys.argv[1] if len(sys.argv) > 1 else "qalta.db")
```

**9-тапсырма.**
```sql
CREATE TABLE person (
    id        INTEGER PRIMARY KEY,
    name      TEXT NOT NULL,
    father_id INTEGER REFERENCES person(id),
    mother_id INTEGER REFERENCES person(id)
);
-- id = 5 адамның атасы (әкесінің әкесі)
SELECT grand.name
FROM person AS p
JOIN person AS f     ON f.id = p.father_id
JOIN person AS grand ON grand.id = f.father_id
WHERE p.id = 5;
```
</details>
