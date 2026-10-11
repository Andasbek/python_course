# Сабақ 55. Практикалық тапсырмалар: SQLite-қа қосылу және кесте құру

Әр тапсырмадан кейін нәтижені DB Browser for SQLite-та (немесе VS Code SQLite Viewer-де) ашып тексер.

## Базалық деңгей

### 1-тапсырма. Алғашқы деректер қоры

`my_first.db` деректер қорын құрып, онда `friends` кестесін жаса:
- `id` — бүтін сан, бастапқы кілт;
- `name` — мәтін, бос болмайды;
- `city` — мәтін, әдепкі мәні `'Алматы'`;
- `birthday` — мәтін (ISO күні).

Бағдарламаны екі рет іске қос — екінші рет қате шықпауы керек (`IF NOT EXISTS`). Соңында «Кесте дайын» деп шығар.

### 2-тапсырма. Кестелер тізімі

Кез келген `.db` файлының атын сұрап, ондағы барлық кестелерді және әр кестенің өрістер санын шығаратын бағдарлама жаз. Файл жоқ болса — «Файл табылмады» (байқа: `connect` жоқ файлды өзі жасап жібереді, сондықтан алдын ала тексеру керек!).

```text
Енгізу:
Файл: center.db
Шығару:
teachers — 5 өріс
clubs — 5 өріс
students — 5 өріс
enrollments — 4 өріс
```

### 3-тапсырма. Ойын рекордтарының қоры

Pygame ойынымыз үшін `game.db` құр. Кестелер:
- `players`: `id` (PK), `nickname` (NOT NULL, UNIQUE), `created` (DEFAULT CURRENT_TIMESTAMP);
- `scores`: `id` (PK), `player_id` (FK → players), `points` (INTEGER, `CHECK (points >= 0)`), `level` (INTEGER DEFAULT 1), `played_at` (TEXT).

Кестелерді `executescript` арқылы бір рет құр, `PRAGMA foreign_keys = ON` қос.

### 4-тапсырма. Қателерді түзет

Төмендегі кодта 5 қате бар. Тауып, түзет:

```text
import sqlite3
conn = sqlite3.connect("shop.db")
conn.execute("""
    CREATE TABLE products (
        id INTEGER PRIMARY KEY
        name TEXT NOT NULL,
        price REAL CHECK (price > 0),
        category TEXT,
    )
""")
conn.execute("CREATE TABLE orders (id INTEGER PRIMARY KEY); CREATE TABLE items (id INTEGER PRIMARY KEY);")
conn.close
```

Кеңес: үтірлер, бір execute — бір команда, `close` шақырылуы, екінші іске қосуда не болады?

## Кеңейтілген деңгей

### 5-тапсырма. Мектеп кітапханасы

54-сабақтағы кітапхана схемасы бойынша `library.db` құр: `books`, `copies`, `readers`, `loans`. Барлық PK, FK, `NOT NULL`, `UNIQUE` (инвентарлық нөмір), `CHECK` (жыл 1800-ден үлкен) шектеулерін қолдан. Кестелерді дұрыс ретпен құр. Соңында `PRAGMA foreign_key_list` арқылы барлық байланыстарды шығар:

```text
copies.book_id → books.id
loans.reader_id → readers.id
loans.copy_id → copies.id
```

### 6-тапсырма. `db.py` модулі

Кез келген жобада қолдануға болатын `db.py` модулін жаз:
- `get_connection(path)` — қосылып, FK тексерісін қосады;
- `init_db(path, schema_sql)` — кестелерді құрады;
- `list_tables(path)` — кестелер тізімін қайтарады;
- `describe(path, table)` — өрістер туралы `[(аты, типі, pk), ...]` тізімін қайтарады.
Модульді `if __name__ == "__main__":` блогында тексер.

### 7-тапсырма. Кестені өзгерту

`friends` кестесіне (1-тапсырма) `ALTER TABLE` арқылы `phone TEXT` өрісін қос. Бағдарлама өріс бұрыннан бар болса, оны қайта қоспасын (`PRAGMA table_info` арқылы тексер).

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. JSON-нан деректер қорын жасау

Схема JSON файлында сипатталған. Оны оқып, `CREATE TABLE` SQL командаларын **автоматты түрде** құрастырып орындайтын бағдарлама жаз:

```text
{
  "tables": {
    "classes":  {"id": "INTEGER PRIMARY KEY", "name": "TEXT NOT NULL UNIQUE"},
    "students": {"id": "INTEGER PRIMARY KEY", "name": "TEXT NOT NULL",
                 "class_id": "INTEGER REFERENCES classes(id)"}
  }
}
```

Бағдарлама құрастырылған SQL мәтінін де шығарсын.

### 9-тапсырма. Деректер қорының «паспорты»

Кез келген `.db` файлы үшін Markdown форматында құжат (`schema.md`) жасайтын бағдарлама жаз: әр кесте — тақырып, оның өрістері — кесте (атауы, типі, NOT NULL, әдепкі мәні, PK), сыртқы кілттер тізімі. Мұндай құжатты жобаның README-іне қосуға болады.

## Үй тапсырмасы

1. Үйде DB Browser for SQLite орнатып, сабақта жасаған `center.db` және `game.db` файлдарын ашып, құрылымын зертте. «Execute SQL» қойындысында `CREATE TABLE` командасын қолмен орындап көр.
2. Өз жобаңның (54-сабақтағы 8-тапсырма немесе 9-тапсырма) деректер қорын `init_db()` функциясымен құр.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("my_first.db")
conn.execute("""
    CREATE TABLE IF NOT EXISTS friends (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        city TEXT DEFAULT 'Алматы',
        birthday TEXT
    )
""")
conn.commit()
conn.close()
print("Кесте дайын")
```

**2-тапсырма**

```python
import os
import sqlite3

path = input("Файл: ").strip()
if not os.path.exists(path):
    print("Файл табылмады")
else:
    conn = sqlite3.connect(path)
    names = conn.execute(
        "SELECT name FROM sqlite_master WHERE type = 'table' AND name NOT LIKE 'sqlite_%'"
    ).fetchall()
    for (name,) in names:
        columns = conn.execute(f"PRAGMA table_info({name})").fetchall()
        print(f"{name} — {len(columns)} өріс")
    conn.close()
```

Ескерту: кесте атауын f-жол арқылы қоюға болады, себебі ол ДҚ-ның өзінен алынды (пайдаланушыдан емес). Пайдаланушы енгізген **мәндерді** SQL-ға f-жолмен қоюға болмайтынын келесі сабақта (SQL-инъекция) талқылаймыз.

**3-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("game.db")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    CREATE TABLE IF NOT EXISTS players (
        id INTEGER PRIMARY KEY,
        nickname TEXT NOT NULL UNIQUE,
        created TEXT DEFAULT CURRENT_TIMESTAMP
    );
    CREATE TABLE IF NOT EXISTS scores (
        id INTEGER PRIMARY KEY,
        player_id INTEGER NOT NULL REFERENCES players(id),
        points INTEGER NOT NULL CHECK (points >= 0),
        level INTEGER DEFAULT 1,
        played_at TEXT
    );
""")
conn.commit()
tables = conn.execute("SELECT name FROM sqlite_master WHERE type = 'table'").fetchall()
print("game.db кестелері:", [t[0] for t in tables])
conn.close()
```

Нәтижесі:

```text
game.db кестелері: ['players', 'scores']
```

**4-тапсырма.** Қателер: (1) `id INTEGER PRIMARY KEY` кейін үтір жоқ; (2) `category TEXT,` — соңғы өрістен кейін артық үтір; (3) бір `execute`-те екі команда → `executescript`; (4) `conn.close` — жақшасыз, функция шақырылмайды; (5) `IF NOT EXISTS` жоқ — екінші іске қосуда `table products already exists`. (Қосымша: `commit` — CREATE үшін `sqlite3` автоматты сақтайды, бірақ әдет ретінде жазған дұрыс.)

```python
import sqlite3

conn = sqlite3.connect("shop.db")
conn.execute("""
    CREATE TABLE IF NOT EXISTS products (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        price REAL CHECK (price > 0),
        category TEXT
    )
""")
conn.executescript("""
    CREATE TABLE IF NOT EXISTS orders (id INTEGER PRIMARY KEY);
    CREATE TABLE IF NOT EXISTS items (id INTEGER PRIMARY KEY);
""")
conn.commit()
conn.close()
print("shop.db дайын")
```

**5-тапсырма**

```python
import sqlite3

SCHEMA = """
CREATE TABLE IF NOT EXISTS books (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    author TEXT NOT NULL,
    year INTEGER CHECK (year > 1800)
);
CREATE TABLE IF NOT EXISTS copies (
    id INTEGER PRIMARY KEY,
    inv_no TEXT NOT NULL UNIQUE,
    book_id INTEGER NOT NULL REFERENCES books(id)
);
CREATE TABLE IF NOT EXISTS readers (
    id INTEGER PRIMARY KEY,
    last_name TEXT NOT NULL,
    first_name TEXT NOT NULL,
    class TEXT
);
CREATE TABLE IF NOT EXISTS loans (
    id INTEGER PRIMARY KEY,
    copy_id INTEGER NOT NULL REFERENCES copies(id),
    reader_id INTEGER NOT NULL REFERENCES readers(id),
    issued TEXT NOT NULL DEFAULT CURRENT_DATE,
    returned TEXT
);
"""

conn = sqlite3.connect("library.db")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript(SCHEMA)
conn.commit()

for (table,) in conn.execute("SELECT name FROM sqlite_master WHERE type = 'table'").fetchall():
    for fk in conn.execute(f"PRAGMA foreign_key_list({table})"):
        print(f"{table}.{fk[3]} → {fk[2]}.{fk[4]}")
conn.close()
```

Нәтижесі:

```text
copies.book_id → books.id
loans.reader_id → readers.id
loans.copy_id → copies.id
```

**6-тапсырма**

```python
# db.py
import sqlite3


def get_connection(path):
    """Қосылады және сыртқы кілттерді тексеруді қосады."""
    conn = sqlite3.connect(path)
    conn.execute("PRAGMA foreign_keys = ON")
    return conn


def init_db(path, schema_sql):
    """schema_sql ішіндегі CREATE TABLE командаларын орындайды."""
    conn = get_connection(path)
    with conn:
        conn.executescript(schema_sql)
    conn.close()


def list_tables(path):
    conn = get_connection(path)
    rows = conn.execute(
        "SELECT name FROM sqlite_master WHERE type = 'table' AND name NOT LIKE 'sqlite_%' ORDER BY name"
    ).fetchall()
    conn.close()
    return [r[0] for r in rows]


def describe(path, table):
    conn = get_connection(path)
    info = [(r[1], r[2], bool(r[5])) for r in conn.execute(f"PRAGMA table_info({table})")]
    conn.close()
    return info


if __name__ == "__main__":
    init_db("test.db", "CREATE TABLE IF NOT EXISTS pets (id INTEGER PRIMARY KEY, name TEXT, kind TEXT);")
    print(list_tables("test.db"))
    print(describe("test.db", "pets"))
```

Нәтижесі:

```text
['pets']
[('id', 'INTEGER', True), ('name', 'TEXT', False), ('kind', 'TEXT', False)]
```

**7-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("my_first.db")
conn.execute("CREATE TABLE IF NOT EXISTS friends (id INTEGER PRIMARY KEY, name TEXT NOT NULL)")
columns = [row[1] for row in conn.execute("PRAGMA table_info(friends)")]
if "phone" in columns:
    print("phone өрісі бұрыннан бар")
else:
    conn.execute("ALTER TABLE friends ADD COLUMN phone TEXT")
    conn.commit()
    print("phone өрісі қосылды")
print([row[1] for row in conn.execute("PRAGMA table_info(friends)")])
conn.close()
```

Нәтижесі (бірінші іске қосуда, 1-тапсырмадағы файлмен):

```text
phone өрісі қосылды
['id', 'name', 'city', 'birthday', 'phone']
```

**8-тапсырма**

```python
import json
import sqlite3

text = """
{"tables": {
  "classes":  {"id": "INTEGER PRIMARY KEY", "name": "TEXT NOT NULL UNIQUE"},
  "students": {"id": "INTEGER PRIMARY KEY", "name": "TEXT NOT NULL",
               "class_id": "INTEGER REFERENCES classes(id)"}
}}
"""
schema = json.loads(text)

statements = []
for table, columns in schema["tables"].items():
    cols = ",\n    ".join(f"{name} {definition}" for name, definition in columns.items())
    statements.append(f"CREATE TABLE IF NOT EXISTS {table} (\n    {cols}\n);")
sql = "\n".join(statements)
print(sql)

conn = sqlite3.connect(":memory:")
conn.executescript(sql)
print("Құрылды:", [r[0] for r in conn.execute("SELECT name FROM sqlite_master")])
conn.close()
```

Нәтижесі:

```text
CREATE TABLE IF NOT EXISTS classes (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL UNIQUE
);
CREATE TABLE IF NOT EXISTS students (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    class_id INTEGER REFERENCES classes(id)
);
Құрылды: ['classes', 'sqlite_autoindex_classes_1', 'students']
```

Ескерту: `sqlite_autoindex_...` — `UNIQUE` шектеуі үшін SQLite өзі жасаған **индекс** (53-сабақтағы 8-тапсырманы еске түсіріңіз). Тек кестелерді алу үшін `WHERE type = 'table'` қосу керек.

**9-тапсырма**

```python
import sqlite3


def schema_markdown(path):
    conn = sqlite3.connect(path)
    lines = [f"# Деректер қоры: {path}", ""]
    tables = conn.execute(
        "SELECT name FROM sqlite_master WHERE type = 'table' AND name NOT LIKE 'sqlite_%'"
    ).fetchall()
    for (table,) in tables:
        lines += [f"## {table}", "", "| Өріс | Типі | NOT NULL | Әдепкі | PK |", "|---|---|---|---|---|"]
        for _, name, col_type, notnull, default, pk in conn.execute(f"PRAGMA table_info({table})"):
            lines.append(f"| {name} | {col_type} | {'иә' if notnull else ''} | "
                         f"{default if default is not None else ''} | {'иә' if pk else ''} |")
        fks = conn.execute(f"PRAGMA foreign_key_list({table})").fetchall()
        if fks:
            lines += ["", "Сыртқы кілттер:"]
            lines += [f"- `{fk[3]}` → `{fk[2]}.{fk[4]}`" for fk in fks]
        lines.append("")
    conn.close()
    return "\n".join(lines)


conn = sqlite3.connect("demo.db")
conn.executescript("""
    CREATE TABLE IF NOT EXISTS classes (id INTEGER PRIMARY KEY, name TEXT NOT NULL);
    CREATE TABLE IF NOT EXISTS students (id INTEGER PRIMARY KEY, name TEXT NOT NULL,
                                         class_id INTEGER REFERENCES classes(id));
""")
conn.close()

md = schema_markdown("demo.db")
with open("schema.md", "w", encoding="utf-8") as f:
    f.write(md)
print(md)
```

Нәтижесі:

```text
# Деректер қоры: demo.db

## classes

| Өріс | Типі | NOT NULL | Әдепкі | PK |
|---|---|---|---|---|
| id | INTEGER |  |  | иә |
| name | TEXT | иә |  |  |

## students

| Өріс | Типі | NOT NULL | Әдепкі | PK |
|---|---|---|---|---|
| id | INTEGER |  |  | иә |
| name | TEXT | иә |  |  |
| class_id | INTEGER |  |  |  |

Сыртқы кілттер:
- `class_id` → `classes.id`
```

</details>
