# Сабақ 56. Практикалық тапсырмалар: INSERT және SELECT

Барлық тапсырмада SQL-ға мәндер **тек `?` арқылы** беріледі. Деректерді өзгерткеннен кейін `commit()` ұмытпа.

## Базалық деңгей

### 1-тапсырма. Кітаптар каталогы

`books.db` деректер қорында `books` кестесін құр: `id` (PK), `title` (NOT NULL), `author` (NOT NULL), `year` (INTEGER), `pages` (INTEGER), `genre` (TEXT). `executemany` арқылы кемінде 6 кітап қос (қазақ әдебиетінен), сосын:
1. барлық кітаптарды шығар;
2. тек атауы мен авторын шығар;
3. қайталанбайтын жанрлар тізімін шығар;
4. 1950 жылдан кейін шыққан кітаптарды шығар.

```text
Шығару (үзінді):
Барлығы 6 кітап
«Абай жолы» — Мұхтар Әуезов
...
Жанрлар: роман, повесть, философия
1950-ден кейін: Көшпенділер (1976), Менің атым Қожа (1957), ...
```

### 2-тапсырма. Тіркелу формасы (консоль)

Бағдарлама цикл ішінде оқушының атын, сыныбын және жасын сұрап, `club.db` қорындағы `members` кестесіне қосады (бос ат — тоқтау). Әр қосылғаннан кейін «№ <id> болып тіркелдің!» деп `lastrowid` шығарады. Соңында барлық мүшелер санын шығарады.

```text
Енгізу:
Аты: Әлихан
Сыныбы: 7Б
Жасы: 13
Шығару:
№ 1 болып тіркелдің!
Аты:
Барлығы тіркелген: 1
```

### 3-тапсырма. id бойынша іздеу

`members` кестесінен пайдаланушы енгізген `id` бойынша мүшені `fetchone()` арқылы тап. `sqlite3.Row` қолданып, өрістерді атауымен шығар. Табылмаса — «Мұндай нөмір жоқ».

### 4-тапсырма. Шектеулерді тексеру

`products` кестесін құр: `name TEXT NOT NULL UNIQUE`, `price REAL CHECK (price > 0)`, `qty INTEGER DEFAULT 0`. Мына жазбаларды қосып көр және әр қатені `try/except sqlite3.IntegrityError` арқылы ұстап, түсінікті хабарлама шығар:

```text
("Дәптер", 150, 20)   → қосылды
("Қалам", -50, 10)    → баға теріс
("Дәптер", 160, 5)    → атау қайталанды
(None, 100, 1)        → атау бос
("Сызғыш", 180)       → qty көрсетілмеген (әдепкі 0) → қосылды
```

## Кеңейтілген деңгей

### 5-тапсырма. CSV → SQLite импорт

50-сабақтағы `journal.csv` файлын оқып, оның барлық жазбаларын `journal.db` қорындағы `grades` кестесіне `executemany` арқылы импортта (`csv.DictReader` + атаулы параметрлер `:name`). Импорттан кейін: жазбалар санын, барлық пәннен «5» алғандарды және 9Ә сыныбының тізімін SELECT арқылы шығар.

### 6-тапсырма. SQL-инъекцияны көрсету

Логин мен пароль тексеретін екі функция жаз: `login_unsafe` (f-жол) және `login_safe` (параметрлер). Үш тест жаса: дұрыс пароль, қате пароль, инъекция `' OR '1'='1`. Нәтижелерді кесте түрінде шығар.

```text
Тест                 | unsafe | safe
дұрыс пароль         | кірді  | кірді
қате пароль          | жоқ    | жоқ
инъекция             | кірді  | жоқ
```

### 7-тапсырма. Ойын рекордтары

55-сабақтағы `game.db` (`players`, `scores`) қорына функциялар жаз:
- `get_or_create_player(nickname)` — ойыншы бар болса оның `id`-ін, жоқ болса жаңасын қосып `id`-ін қайтарады;
- `save_score(nickname, points, level)` — нәтижені сақтайды;
- `player_scores(nickname)` — ойыншының барлық нәтижелерін қайтарады.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Сөздік-аудармашы

`dictionary.db` қорында `words(id, kk, en, ru, topic)` кестесі болсын. Бағдарлама:
- кемінде 20 сөзді (тақырыптары: «мектеп», «табиғат», «IT») `executemany` арқылы қосады;
- қазақша сөз енгізілсе — ағылшын және орыс аудармасын шығарады;
- тақырып енгізілсе — сол тақырыптың барлық сөздерін шығарады;
- кездейсоқ 5 сөзбен шағын тест өткізеді (`ORDER BY RANDOM() LIMIT 5`).

### 9-тапсырма. Өнімділікті өлшеу

10 000 жазбаны үш тәсілмен қосып, уақытын өлше: (а) әр `execute`-тен кейін `commit`; (б) циклде `execute`, соңында бір `commit`; (в) `executemany` + бір `commit`. Нәтижені түсіндір (транзакция құны).

## Үй тапсырмасы

1. 8-тапсырманы аяқта немесе өз «Фильмдер/Ойындар каталогыңды» SQLite-та жаса: кемінде 10 жазба, 5 түрлі SELECT сұранысы.
2. DB Browser for SQLite-та «Execute SQL» қойындысында өз кестеңе `INSERT` және `SELECT` командаларын қолмен орындап көр.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**

```python
import sqlite3

books = [
    ("Абай жолы", "Мұхтар Әуезов", 1942, 512, "роман"),
    ("Көшпенділер", "Ілияс Есенберлин", 1976, 640, "роман"),
    ("Менің атым Қожа", "Бердібек Соқпақбаев", 1957, 176, "повесть"),
    ("Ақбілек", "Жүсіпбек Аймауытов", 1928, 288, "роман"),
    ("Қара сөздер", "Абай Құнанбайұлы", 1890, 128, "философия"),
    ("Қан мен тер", "Әбдіжәміл Нұрпейісов", 1970, 704, "роман"),
]

conn = sqlite3.connect("books.db")
conn.executescript("""
    DROP TABLE IF EXISTS books;
    CREATE TABLE books (
        id INTEGER PRIMARY KEY,
        title TEXT NOT NULL,
        author TEXT NOT NULL,
        year INTEGER,
        pages INTEGER,
        genre TEXT
    );
""")
conn.executemany("INSERT INTO books (title, author, year, pages, genre) VALUES (?, ?, ?, ?, ?)", books)
conn.commit()

rows = conn.execute("SELECT * FROM books").fetchall()
print("Барлығы", len(rows), "кітап")
for title, author in conn.execute("SELECT title, author FROM books"):
    print(f"«{title}» — {author}")
genres = [g[0] for g in conn.execute("SELECT DISTINCT genre FROM books")]
print("Жанрлар:", ", ".join(genres))
recent = conn.execute("SELECT title, year FROM books WHERE year > ?", (1950,)).fetchall()
print("1950-ден кейін:", ", ".join(f"{t} ({y})" for t, y in recent))
conn.close()
```

Нәтижесі:

```text
Барлығы 6 кітап
«Абай жолы» — Мұхтар Әуезов
«Көшпенділер» — Ілияс Есенберлин
«Менің атым Қожа» — Бердібек Соқпақбаев
«Ақбілек» — Жүсіпбек Аймауытов
«Қара сөздер» — Абай Құнанбайұлы
«Қан мен тер» — Әбдіжәміл Нұрпейісов
Жанрлар: роман, повесть, философия
1950-ден кейін: Көшпенділер (1976), Менің атым Қожа (1957), Қан мен тер (1970)
```

**2-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("club.db")
conn.execute("""
    CREATE TABLE IF NOT EXISTS members (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        class TEXT,
        age INTEGER
    )
""")
while True:
    name = input("Аты: ").strip()
    if not name:
        break
    cls = input("Сыныбы: ").strip()
    age = int(input("Жасы: "))
    cur = conn.execute("INSERT INTO members (name, class, age) VALUES (?, ?, ?)", (name, cls, age))
    conn.commit()
    print(f"№ {cur.lastrowid} болып тіркелдің!")

print("Барлығы тіркелген:", conn.execute("SELECT COUNT(*) FROM members").fetchone()[0])
conn.close()
```

**3-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("club.db")
conn.row_factory = sqlite3.Row
member_id = int(input("Нөмір: "))
row = conn.execute("SELECT * FROM members WHERE id = ?", (member_id,)).fetchone()
if row is None:
    print("Мұндай нөмір жоқ")
else:
    print(f"Аты: {row['name']}, сыныбы: {row['class']}, жасы: {row['age']}")
conn.close()
```

**4-тапсырма**

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("""
    CREATE TABLE products (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL UNIQUE,
        price REAL CHECK (price > 0),
        qty INTEGER DEFAULT 0
    )
""")

tests = [("Дәптер", 150, 20), ("Қалам", -50, 10), ("Дәптер", 160, 5), (None, 100, 1), ("Сызғыш", 180)]
messages = {"CHECK": "баға теріс", "UNIQUE": "атау қайталанды", "NOT NULL": "атау бос"}

for item in tests:
    columns = "name, price, qty" if len(item) == 3 else "name, price"
    marks = ", ".join("?" * len(item))
    try:
        conn.execute(f"INSERT INTO products ({columns}) VALUES ({marks})", item)
        print(item, "→ қосылды")
    except sqlite3.IntegrityError as e:
        reason = next((m for key, m in messages.items() if str(e).startswith(key)), str(e))
        print(item, "→", reason)
conn.commit()
print(conn.execute("SELECT name, price, qty FROM products").fetchall())
conn.close()
```

Нәтижесі:

```text
('Дәптер', 150, 20) → қосылды
('Қалам', -50, 10) → баға теріс
('Дәптер', 160, 5) → атау қайталанды
(None, 100, 1) → атау бос
('Сызғыш', 180) → қосылды
[('Дәптер', 150.0, 20), ('Сызғыш', 180.0, 0)]
```

Ескерту: мұнда f-жол тек **өріс атаулары мен `?` санын** құрастырады — мәндер бәрібір параметр арқылы беріледі, сондықтан қауіпсіз.

**5-тапсырма**

```python
import csv
import sqlite3

rows = [
    ["name", "class", "math", "informatics", "physics"],
    ["Айгерім Нұрланқызы", "9А", 5, 5, 4],
    ["Бауыржан Сейітов", "9А", 3, 4, 4],
    ["Дәулет Оспанов", "9Ә", 4, 5, 5],
    ["Сымбат Әлиева", "9Ә", 5, 5, 5],
]
with open("journal.csv", "w", encoding="utf-8", newline="") as f:
    csv.writer(f).writerows(rows)

conn = sqlite3.connect("journal.db")
conn.executescript("""
    DROP TABLE IF EXISTS grades;
    CREATE TABLE grades (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        class TEXT,
        math INTEGER, informatics INTEGER, physics INTEGER
    );
""")
with open("journal.csv", encoding="utf-8", newline="") as f:
    conn.executemany(
        "INSERT INTO grades (name, class, math, informatics, physics) "
        "VALUES (:name, :class, :math, :informatics, :physics)",
        csv.DictReader(f),
    )
conn.commit()

print("Жазбалар:", conn.execute("SELECT COUNT(*) FROM grades").fetchone()[0])
best = conn.execute("SELECT name FROM grades WHERE math = 5 AND informatics = 5 AND physics = 5").fetchall()
print("Үздіктер:", [b[0] for b in best])
print("9Ә:", [r[0] for r in conn.execute("SELECT name FROM grades WHERE class = ?", ("9Ә",))])
conn.close()
```

Нәтижесі:

```text
Жазбалар: 4
Үздіктер: ['Сымбат Әлиева']
9Ә: ['Дәулет Оспанов', 'Сымбат Әлиева']
```

Ескерту: CSV-ден келген мәндер — жолдар (`'5'`), бірақ `INTEGER` өрісіне жазғанда SQLite оларды санға айналдырады (типтік жақындық, type affinity). `AND` операторы келесі сабақта толық түсіндіріледі.

**6-тапсырма**

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE users (login TEXT, password TEXT)")
conn.execute("INSERT INTO users VALUES ('aida', 'Tulpar2027')")


def login_unsafe(login, password):
    sql = f"SELECT 1 FROM users WHERE login = '{login}' AND password = '{password}'"
    return conn.execute(sql).fetchone() is not None


def login_safe(login, password):
    sql = "SELECT 1 FROM users WHERE login = ? AND password = ?"
    return conn.execute(sql, (login, password)).fetchone() is not None


tests = [("дұрыс пароль", "Tulpar2027"), ("қате пароль", "12345"), ("инъекция", "' OR '1'='1")]
print(f"{'Тест':<20} | unsafe | safe")
for title, pwd in tests:
    u = "кірді" if login_unsafe("aida", pwd) else "жоқ"
    s = "кірді" if login_safe("aida", pwd) else "жоқ"
    print(f"{title:<20} | {u:<6} | {s}")
conn.close()
```

Нәтижесі:

```text
Тест                 | unsafe | safe
дұрыс пароль         | кірді  | кірді
қате пароль          | жоқ    | жоқ
инъекция             | кірді  | жоқ
```

**7-тапсырма**

```python
import sqlite3
from datetime import datetime

conn = sqlite3.connect(":memory:")   # нақты жобада: "game.db"
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    CREATE TABLE IF NOT EXISTS players (id INTEGER PRIMARY KEY, nickname TEXT NOT NULL UNIQUE);
    CREATE TABLE IF NOT EXISTS scores (
        id INTEGER PRIMARY KEY,
        player_id INTEGER NOT NULL REFERENCES players(id),
        points INTEGER NOT NULL CHECK (points >= 0),
        level INTEGER DEFAULT 1,
        played_at TEXT
    );
""")


def get_or_create_player(nickname):
    row = conn.execute("SELECT id FROM players WHERE nickname = ?", (nickname,)).fetchone()
    if row:
        return row[0]
    cur = conn.execute("INSERT INTO players (nickname) VALUES (?)", (nickname,))
    conn.commit()
    return cur.lastrowid


def save_score(nickname, points, level):
    player_id = get_or_create_player(nickname)
    now = datetime.now().isoformat(timespec="seconds")
    conn.execute("INSERT INTO scores (player_id, points, level, played_at) VALUES (?, ?, ?, ?)",
                 (player_id, points, level, now))
    conn.commit()


def player_scores(nickname):
    player_id = get_or_create_player(nickname)
    return conn.execute("SELECT points, level FROM scores WHERE player_id = ?", (player_id,)).fetchall()


save_score("Qyran", 340, 1)
save_score("Qyran", 520, 2)
save_score("Tulpar", 410, 2)
print("Qyran:", player_scores("Qyran"))
print("Ойыншылар:", conn.execute("SELECT * FROM players").fetchall())
conn.close()
```

Нәтижесі:

```text
Qyran: [(340, 1), (520, 2)]
Ойыншылар: [(1, 'Qyran'), (2, 'Tulpar')]
```

**8-тапсырма (негізгі бөлігі)**

```python
import sqlite3

WORDS = [
    ("мектеп", "school", "школа", "мектеп"), ("мұғалім", "teacher", "учитель", "мектеп"),
    ("дәптер", "notebook", "тетрадь", "мектеп"), ("сабақ", "lesson", "урок", "мектеп"),
    ("кітап", "book", "книга", "мектеп"), ("тау", "mountain", "гора", "табиғат"),
    ("өзен", "river", "река", "табиғат"), ("орман", "forest", "лес", "табиғат"),
    ("дала", "steppe", "степь", "табиғат"), ("көл", "lake", "озеро", "табиғат"),
    ("бағдарлама", "program", "программа", "IT"), ("компьютер", "computer", "компьютер", "IT"),
    ("пернетақта", "keyboard", "клавиатура", "IT"), ("тінтуір", "mouse", "мышь", "IT"),
    ("желі", "network", "сеть", "IT"), ("файл", "file", "файл", "IT"),
    ("деректер қоры", "database", "база данных", "IT"), ("кесте", "table", "таблица", "IT"),
    ("жаңбыр", "rain", "дождь", "табиғат"), ("қоңырау", "bell", "звонок", "мектеп"),
]

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE words (id INTEGER PRIMARY KEY, kk TEXT UNIQUE, en TEXT, ru TEXT, topic TEXT)")
conn.executemany("INSERT INTO words (kk, en, ru, topic) VALUES (?, ?, ?, ?)", WORDS)
conn.commit()

word = "орман"   # нақты бағдарламада: input()
row = conn.execute("SELECT en, ru FROM words WHERE kk = ?", (word,)).fetchone()
print(f"{word}: {row[0]} / {row[1]}" if row else "Сөз табылмады")

topic = "IT"
print(f"{topic}:", ", ".join(r[0] for r in conn.execute("SELECT kk FROM words WHERE topic = ?", (topic,))))

quiz = conn.execute("SELECT kk, en FROM words ORDER BY RANDOM() LIMIT 5").fetchall()
print("Тест сөздері:", len(quiz))
conn.close()
```

Нәтижесі:

```text
орман: forest / лес
IT: бағдарлама, компьютер, пернетақта, тінтуір, желі, файл, деректер қоры, кесте
Тест сөздері: 5
```

**9-тапсырма**

```python
import os
import sqlite3
import time

data = [(f"Оқушы {i}", i % 100) for i in range(10_000)]


def fresh(path="speed.db"):
    if os.path.exists(path):
        os.remove(path)
    conn = sqlite3.connect(path)
    conn.execute("CREATE TABLE t (name TEXT, score INTEGER)")
    return conn


conn = fresh()
start = time.perf_counter()
for row in data[:1000]:                       # тек 1000 — өте баяу!
    conn.execute("INSERT INTO t VALUES (?, ?)", row)
    conn.commit()
a = (time.perf_counter() - start) * 10        # 10 000-ға шаққанда
conn.close()

conn = fresh()
start = time.perf_counter()
for row in data:
    conn.execute("INSERT INTO t VALUES (?, ?)", row)
conn.commit()
b = time.perf_counter() - start
conn.close()

conn = fresh()
start = time.perf_counter()
conn.executemany("INSERT INTO t VALUES (?, ?)", data)
conn.commit()
c = time.perf_counter() - start
conn.close()

print(f"(а) әр жолдан кейін commit: ~{a:.2f} с")
print(f"(б) бір commit:              {b:.3f} с")
print(f"(в) executemany:             {c:.3f} с")
```

Түсіндірме: әр `commit` деректерді дискіге физикалық түрде жазуды талап етеді (ең қымбат операция). Сондықтан көп жазбаны **бір транзакцияда** қосу ондаған-жүздеген есе жылдам; `executemany` Python деңгейіндегі циклдің шығынын да азайтады.

</details>
