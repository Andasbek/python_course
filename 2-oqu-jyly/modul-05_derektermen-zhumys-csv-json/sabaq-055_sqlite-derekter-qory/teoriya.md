# Сабақ 55. SQLite деректер қоры. sqlite3 модулі. Қосылу және кесте құру.

## Тақырыптар

1. SQLite туралы
2. `sqlite3` модулімен жұмыстың негізгі қадамдары
3. `commit()` және транзакциялар
4. `CREATE TABLE` — кесте құру
5. Деректер қорының құрылымын тексеру
6. ДҚ-мен жұмысты функцияға орау
7. Жиі кездесетін қателер
8. Негізгі ұғымдар

## 1. SQLite туралы

**SQLite** — әлемдегі ең кең тараған деректер қорын басқару жүйесі. Ол әрбір смартфонда, браузерде (Chrome, Firefox), Telegram, Skype, тіпті ұшақтардың бортындағы жүйелерде қолданылады.

| Ерекшелігі | Мағынасы |
|---|---|
| **Серверсіз** | Бөлек сервер орнату керек емес: бағдарлама ДҚ файлымен тікелей жұмыс істейді |
| **Бір файл** | Бүкіл деректер қоры — бір `.db` (немесе `.sqlite`) файлы; оны көшіріп, жіберуге болады |
| **Python-ға кірістірілген** | `import sqlite3` — ештеңе орнатпаймыз |
| **SQL стандарты** | SQL-дің негізгі командаларын қолдайды |
| **Шектеуі** | Мыңдаған адам бір уақытта жазатын үлкен веб-сайттарға арналмаған |

**Аналогия:** PostgreSQL — үлкен банктің қоймасы (күзетшісі, бөлек ғимараты бар), ал SQLite — сенің жеке сейфің: кішкентай, бірақ мықты және әрқашан қасыңда.

## 2. `sqlite3` модулімен жұмыстың негізгі қадамдары

```text
1. connect()   — ДҚ-ға қосылу (файл жоқ болса, жасалады)  → Connection
2. cursor()    — курсор алу (SQL командаларын орындайтын «қалам») → Cursor
3. execute()   — SQL командасын орындау
4. commit()    — өзгерістерді файлға сақтау
5. close()     — байланысты жабу
```

```python
import sqlite3

# 1. Қосылу: club.db файлы жоқ болса — жаңадан құрылады
conn = sqlite3.connect("club.db")

# 2. Курсор
cur = conn.cursor()

# 3. SQL командасын орындау
cur.execute("""
    CREATE TABLE IF NOT EXISTS students (
        id INTEGER PRIMARY KEY,
        first_name TEXT NOT NULL,
        last_name TEXT NOT NULL,
        birth_date TEXT
    )
""")

# 4. Өзгерістерді сақтау
conn.commit()

# 5. Жабу
conn.close()
print("Деректер қоры және students кестесі құрылды")
```

Нәтижесі:

```text
Деректер қоры және students кестесі құрылды
```

Бағдарлама папкасында `club.db` файлы пайда болады. Оны Блокнотпен ашсаң — «бұдыр-бұдыр» символдар көресің (бұл екілік файл). Оның алғашқы байттары: `SQLite format 3`.

### Объектілер

| Объект | Не істейді | Аналогия |
|---|---|---|
| `Connection` (`conn`) | ДҚ-мен байланыс; транзакцияларды басқарады (`commit`, `rollback`, `close`) | телефон қоңырауы |
| `Cursor` (`cur`) | SQL командаларын орындайды, нәтижелерін береді (`execute`, `fetchall`) | қоңырау кезіндегі сөйлесу |

`conn.execute(...)` деп курсорсыз да жазуға болады — ол курсорды өзі жасап, қайтарады.

### `:memory:` — жадтағы деректер қоры

```python
import sqlite3

conn = sqlite3.connect(":memory:")   # файлсыз, тек жедел жадта
conn.execute("CREATE TABLE test (id INTEGER PRIMARY KEY, name TEXT)")
print("Жадтағы ДҚ дайын. SQLite нұсқасы:", sqlite3.sqlite_version)
conn.close()                         # жабылғанда бәрі жоғалады
```

Нәтижесі (нұсқа компьютерге байланысты):

```text
Жадтағы ДҚ дайын. SQLite нұсқасы: 3.45.1
```

`:memory:` тәжірибе жасауға және тестілеуге өте ыңғайлы.

## 3. `commit()` және транзакциялар

**Транзакция** — «бәрі немесе ештеңе» қағидасымен орындалатын әрекеттер тобы. `sqlite3` деректерді өзгертетін командалардан (INSERT, UPDATE, DELETE) кейін транзакцияны өзі ашады, бірақ **өзі жаппайды**. `commit()` шақырылмаса, бағдарлама жабылғанда өзгерістер **жоғалады**.

**Аналогия:** Word құжатында жаздың, бірақ «Сақтау» баспадың — компьютер өшсе, жұмыс жоғалады. `commit()` — «Сақтау» батырмасы, `rollback()` — «Сақтамай жабу».

**Банктік мысал:** Әсемнің шотынан 5000 ₸ алып, Ерланның шотына салу — екі әрекет. Егер біріншісі орындалып, екіншісінде қате шықса, ақша «жоғалады». Транзакция екеуін бірге орындайды немесе екеуін де болдырмайды.

### `with` арқылы автоматты commit

```python
import sqlite3

conn = sqlite3.connect("club.db")
with conn:   # блок сәтті аяқталса — commit, қате болса — rollback
    conn.execute("CREATE TABLE IF NOT EXISTS rooms (id INTEGER PRIMARY KEY, number TEXT)")
conn.close()  # назар аудар: with байланысты ЖАБПАЙДЫ, тек транзакцияны басқарады
print("rooms кестесі дайын")
```

Нәтижесі:

```text
rooms кестесі дайын
```

> **Маңызды:** `with conn:` файлмен жұмыстағы `with open(...)` сияқты емес — ол байланысты жаппайды, тек commit/rollback жасайды. Байланысты жабу үшін `conn.close()` немесе `contextlib.closing` қолданамыз.

## 4. `CREATE TABLE` — кесте құру

Жалпы синтаксис:

```sql
CREATE TABLE [IF NOT EXISTS] кесте_атауы (
    өріс1 ТИП [шектеулер],
    өріс2 ТИП [шектеулер],
    ...
    [кесте деңгейіндегі шектеулер]
);
```

### Мысал: «Үйірмелер орталығы» деректер қоры

```python
import sqlite3

SCHEMA = """
CREATE TABLE IF NOT EXISTS teachers (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    last_name TEXT NOT NULL,
    first_name TEXT NOT NULL,
    phone TEXT UNIQUE,
    email TEXT
);

CREATE TABLE IF NOT EXISTS clubs (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL UNIQUE,
    min_age INTEGER DEFAULT 10 CHECK (min_age BETWEEN 6 AND 18),
    price REAL DEFAULT 0,
    teacher_id INTEGER,
    FOREIGN KEY (teacher_id) REFERENCES teachers(id)
);

CREATE TABLE IF NOT EXISTS students (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    last_name TEXT NOT NULL,
    first_name TEXT NOT NULL,
    birth_date TEXT,
    parent_phone TEXT
);

CREATE TABLE IF NOT EXISTS enrollments (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    student_id INTEGER NOT NULL REFERENCES students(id),
    club_id INTEGER NOT NULL REFERENCES clubs(id),
    enrolled TEXT DEFAULT CURRENT_DATE,
    UNIQUE (student_id, club_id)
);
"""

conn = sqlite3.connect("center.db")
conn.execute("PRAGMA foreign_keys = ON")   # FK тексерісін қосу
conn.executescript(SCHEMA)                 # бірнеше команданы бірден орындау
conn.commit()
conn.close()
print("center.db: 4 кесте құрылды")
```

Нәтижесі:

```text
center.db: 4 кесте құрылды
```

### Шектеулер мен кілт сөздер

| Жазылуы | Мағынасы |
|---|---|
| `INTEGER PRIMARY KEY` | бастапқы кілт; мән берілмесе, SQLite келесі нөмірді өзі береді |
| `AUTOINCREMENT` | өшірілген жазбалардың нөмірлерін қайта қолданбау (міндетті емес) |
| `NOT NULL` | бос болмауы керек |
| `UNIQUE` | қайталанбауы керек |
| `DEFAULT мән` | мән берілмесе — әдепкі (`CURRENT_DATE` — бүгінгі күн) |
| `CHECK (шарт)` | мән шартты қанағаттандыруы керек |
| `REFERENCES кесте(өріс)` | сыртқы кілт (өріс деңгейінде) |
| `FOREIGN KEY (өріс) REFERENCES ...` | сыртқы кілт (кесте деңгейінде) |
| `UNIQUE (а, б)` | өрістер **жұбы** қайталанбауы керек |

### `PRAGMA foreign_keys = ON`

Тарихи себептермен SQLite-та сыртқы кілттер тексерісі **әдепкіде өшірулі**. Әр қосылғаннан кейін оны қосу керек:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
print(conn.execute("PRAGMA foreign_keys").fetchone())   # (1,) — қосулы
conn.close()
```

Нәтижесі:

```text
(1,)
```

### `IF NOT EXISTS` және `DROP TABLE`

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE notes (id INTEGER PRIMARY KEY, text TEXT)")
try:
    conn.execute("CREATE TABLE notes (id INTEGER PRIMARY KEY, text TEXT)")
except sqlite3.OperationalError as e:
    print("Қате:", e)

conn.execute("CREATE TABLE IF NOT EXISTS notes (id INTEGER PRIMARY KEY, text TEXT)")
print("IF NOT EXISTS — қате жоқ")

conn.execute("DROP TABLE IF EXISTS notes")   # кестені ішіндегі деректерімен бірге жою!
print("notes кестесі жойылды")
conn.close()
```

Нәтижесі:

```text
Қате: table notes already exists
IF NOT EXISTS — қате жоқ
notes кестесі жойылды
```

> `DROP TABLE` — кестені **барлық деректерімен** жояды, оны «Болдырмау» мүмкін емес. Абай бол!

Кестеге кейін өріс қосу: `ALTER TABLE students ADD COLUMN email TEXT;`

## 5. Деректер қорының құрылымын тексеру

### Python арқылы

Әр SQLite қорында `sqlite_master` деген жүйелік кесте бар — онда барлық кестелердің тізімі мен SQL сипаттамасы сақталады. Нәтижені алу үшін `fetchall()` (барлық жолдар) немесе `fetchone()` (бір жол) қолданамыз.

```python
import sqlite3

conn = sqlite3.connect("center.db")

tables = conn.execute(
    "SELECT name FROM sqlite_master WHERE type = 'table' AND name NOT LIKE 'sqlite_%'"
).fetchall()
print("Кестелер:", [t[0] for t in tables])

print("\nclubs кестесінің құрылымы:")
for cid, name, col_type, notnull, default, pk in conn.execute("PRAGMA table_info(clubs)"):
    flags = []
    if pk:
        flags.append("PK")
    if notnull:
        flags.append("NOT NULL")
    if default is not None:
        flags.append(f"DEFAULT {default}")
    print(f"  {name:<11} {col_type:<8} {' '.join(flags)}")

print("\nclubs сыртқы кілттері:")
for row in conn.execute("PRAGMA foreign_key_list(clubs)"):
    print(f"  {row[3]} → {row[2]}.{row[4]}")
conn.close()
```

Нәтижесі:

```text
Кестелер: ['teachers', 'clubs', 'students', 'enrollments']

clubs кестесінің құрылымы:
  id          INTEGER  PK
  title       TEXT     NOT NULL
  min_age     INTEGER  DEFAULT 10
  price       REAL     DEFAULT 0
  teacher_id  INTEGER  

clubs сыртқы кілттері:
  teacher_id → teachers.id
```

(`sqlite_sequence` жүйелік кестесін `AUTOINCREMENT` өзі жасайды — сондықтан оны `NOT LIKE 'sqlite_%'` арқылы алып тастадық.)

### DB Browser for SQLite арқылы

1. **sqlitebrowser.org** сайтынан бағдарламаны жүктеп орнатамыз (тегін).
2. **Open Database** → `center.db` файлын таңдаймыз.
3. **Database Structure** қойындысы — кестелер, өрістер, типтер.
4. **Browse Data** — кестедегі деректер (әзірге бос).
5. **Execute SQL** — SQL командаларын қолмен орындап көруге болады.

> DB Browser-де өзгеріс жасасаң, **Write Changes** басуды ұмытпа (бұл — `commit`). Python бағдарламасы мен DB Browser бір файлды бір уақытта өзгертсе, `database is locked` қатесі шығуы мүмкін — DB Browser-дегі өзгерістерді сақтап немесе жауып қой.

## 6. ДҚ-мен жұмысты функцияға орау

Жобада ДҚ-ны құратын кодты бөлек функцияға (немесе модульге) шығарамыз:

```python
import sqlite3
from pathlib import Path

DB_PATH = Path(__file__).with_name("library.db")


def get_connection():
    """ДҚ-ға қосылады және сыртқы кілттер тексерісін қосады."""
    conn = sqlite3.connect(DB_PATH)
    conn.execute("PRAGMA foreign_keys = ON")
    return conn


def init_db():
    """Кестелер жоқ болса, оларды құрады. Бағдарлама басында бір рет шақырылады."""
    with get_connection() as conn:
        conn.executescript("""
            CREATE TABLE IF NOT EXISTS authors (
                id INTEGER PRIMARY KEY,
                name TEXT NOT NULL UNIQUE
            );
            CREATE TABLE IF NOT EXISTS books (
                id INTEGER PRIMARY KEY,
                title TEXT NOT NULL,
                year INTEGER CHECK (year > 0),
                author_id INTEGER REFERENCES authors(id)
            );
        """)
    conn.close()


if __name__ == "__main__":
    init_db()
    print("library.db дайын:", DB_PATH.exists())
```

Нәтижесі:

```text
library.db дайын: True
```

`init_db()` функциясын бірнеше рет шақыруға болады — `IF NOT EXISTS` арқасында бар кестелер бұзылмайды.

## Жиі кездесетін қателер

**1. `commit()` ұмытылды** → деректер сақталмайды (әсіресе INSERT/UPDATE кезінде). Дұрыс: `conn.commit()` немесе `with conn:`.

**2. Бір `execute()` ішінде бірнеше команда.**

```python
import sqlite3

conn = sqlite3.connect(":memory:")
try:
    conn.execute("CREATE TABLE a (id INTEGER); CREATE TABLE b (id INTEGER);")
except sqlite3.ProgrammingError as e:
    print("Қате:", e)
conn.executescript("CREATE TABLE a (id INTEGER); CREATE TABLE b (id INTEGER);")
print("executescript — дұрыс")
conn.close()
```

Нәтижесі:

```text
Қате: You can only execute one statement at a time.
executescript — дұрыс
```

**3. SQL-дағы синтаксистік қате** → `sqlite3.OperationalError: near "...": syntax error`. Жиі себептері: соңғы өрістен кейінгі артық үтір, жақша жабылмаған, өріс атауы SQL кілт сөзімен сәйкес (`order`, `group`) — басқа атау таңдау керек.

```sql
-- Қате: соңғы өрістен кейін үтір
CREATE TABLE t (id INTEGER, name TEXT,);
-- Дұрыс
CREATE TABLE t (id INTEGER, name TEXT);
```

**4. Файл басқа папкада құрылды.** `sqlite3.connect("club.db")` файлды **ағымдағы жұмыс папкасында** құрады. Дұрыс: `Path(__file__).with_name("club.db")`.

**5. Сыртқы кілттер тексерілмейді.** `PRAGMA foreign_keys = ON` ұмытылды. Дұрыс: әр қосылудан кейін қосу (`get_connection` функциясында).

**6. `database is locked`** — ДҚ-ны басқа бағдарлама (DB Browser) ұстап тұр немесе байланыс жабылмаған. Дұрыс: `close()`, DB Browser-де Write/Close.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| SQLite | Серверсіз, бір файлдық ДҚБЖ |
| `sqlite3` | Python-ның SQLite-пен жұмыс істейтін стандартты модулі |
| `Connection` | ДҚ-мен байланыс объектісі (`connect()` қайтарады) |
| `Cursor` | SQL командаларын орындайтын объект |
| `execute()` / `executescript()` | бір / бірнеше SQL командасын орындау |
| `commit()` / `rollback()` | транзакцияны сақтау / болдырмау |
| Транзакция | «Бәрі немесе ештеңе» қағидасымен орындалатын әрекеттер тобы |
| `CREATE TABLE` | кесте құру командасы |
| `IF NOT EXISTS` | кесте бар болса, қате шығармау |
| `DROP TABLE` | кестені деректерімен бірге жою |
| `PRAGMA` | SQLite-тың арнайы баптау/ақпарат командалары |
| `sqlite_master` | барлық кестелердің тізімі сақталатын жүйелік кесте |

## Ескерту

- Python 3.12-ден бастап `sqlite3.connect()`-те `autocommit` параметрі бар; ұсынылатын `autocommit=False` мәні PEP 249 стандарты бойынша әрекет береді (транзакция әрқашан ашық, анық `commit()` керек). Әзірге әдепкі бойынша ескі режим (`LEGACY_TRANSACTION_CONTROL`) сақталады.
- `executescript()` орындау алдында аяқталмаған транзакцияға өзі `COMMIT` жасайды, ал онда `?` параметрлеріне қолдау жоқ — оны тек тұрақты скрипттер үшін (схема құру) қолданыңыз.
- `PRAGMA foreign_keys = ON` ашық транзакция ішінде әрекет етпейді, сондықтан оны `connect()`-тен кейін бірден орындайды.
- `INTEGER PRIMARY KEY` — кірістірілген `rowid`-тің бүркеншік аты; `AUTOINCREMENT` сирек керек және `sqlite_sequence` кестесін жүргізетіндіктен қосуды сәл баяулатады.
- Әдепкі бойынша қосылымды басқа ағыннан қолдануға болмайды (`check_same_thread=True`); көп ағынды бағдарламаларда әр ағын өз қосылымын ашады.
- Құжаттама: https://docs.python.org/3/library/sqlite3.html және https://www.sqlite.org/lang_createtable.html
