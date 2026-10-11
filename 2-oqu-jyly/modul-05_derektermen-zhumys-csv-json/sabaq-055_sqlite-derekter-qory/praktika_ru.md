# Урок 55. Практические задания: подключение к SQLite и создание таблиц

После каждого задания открой результат в DB Browser for SQLite (или в VS Code SQLite Viewer) и проверь.

## Базовый уровень

### Задание 1. Первая база данных

Создай базу данных `my_first.db` и в ней таблицу `friends`:
- `id` — целое число, первичный ключ;
- `name` — текст, не может быть пустым;
- `city` — текст, значение по умолчанию `'Алматы'`;
- `birthday` — текст (дата ISO).

Запусти программу дважды — во второй раз ошибки быть не должно (`IF NOT EXISTS`). В конце выведи «Таблица готова».

### Задание 2. Список таблиц

Напиши программу, которая запрашивает имя любого `.db`-файла и выводит все таблицы в нём и количество полей каждой таблицы. Если файла нет — «Файл не найден» (обрати внимание: `connect` сам создаёт несуществующий файл, поэтому нужно проверить заранее!).

```text
Ввод:
Файл: center.db
Вывод:
teachers — полей: 5
clubs — полей: 5
students — полей: 5
enrollments — полей: 4
```

### Задание 3. База рекордов игры

Создай `game.db` для нашей игры на Pygame. Таблицы:
- `players`: `id` (PK), `nickname` (NOT NULL, UNIQUE), `created` (DEFAULT CURRENT_TIMESTAMP);
- `scores`: `id` (PK), `player_id` (FK → players), `points` (INTEGER, `CHECK (points >= 0)`), `level` (INTEGER DEFAULT 1), `played_at` (TEXT).

Создай таблицы одним вызовом `executescript`, включи `PRAGMA foreign_keys = ON`.

### Задание 4. Исправь ошибки

В коде ниже 5 ошибок. Найди и исправь их:

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

Подсказка: запятые, один execute — одна команда, вызов `close`, что будет при втором запуске?

## Продвинутый уровень

### Задание 5. Школьная библиотека

Создай `library.db` по схеме библиотеки с урока 54: `books`, `copies`, `readers`, `loans`. Используй все ограничения PK, FK, `NOT NULL`, `UNIQUE` (инвентарный номер), `CHECK` (год больше 1800). Создай таблицы в правильном порядке. В конце выведи все связи с помощью `PRAGMA foreign_key_list`:

```text
copies.book_id → books.id
loans.reader_id → readers.id
loans.copy_id → copies.id
```

### Задание 6. Модуль `db.py`

Напиши модуль `db.py`, который можно использовать в любом проекте:
- `get_connection(path)` — подключается и включает проверку FK;
- `init_db(path, schema_sql)` — создаёт таблицы;
- `list_tables(path)` — возвращает список таблиц;
- `describe(path, table)` — возвращает список полей `[(имя, тип, pk), ...]`.
Проверь модуль в блоке `if __name__ == "__main__":`.

### Задание 7. Изменение таблицы

Добавь в таблицу `friends` (задание 1) поле `phone TEXT` с помощью `ALTER TABLE`. Если поле уже есть, программа не должна добавлять его повторно (проверь через `PRAGMA table_info`).

## ★ Олимпиадная / творческая задача

### Задание 8. Создание базы данных из JSON

Схема описана в JSON-файле. Напиши программу, которая читает её, **автоматически** составляет SQL-команды `CREATE TABLE` и выполняет их:

```text
{
  "tables": {
    "classes":  {"id": "INTEGER PRIMARY KEY", "name": "TEXT NOT NULL UNIQUE"},
    "students": {"id": "INTEGER PRIMARY KEY", "name": "TEXT NOT NULL",
                 "class_id": "INTEGER REFERENCES classes(id)"}
  }
}
```

Программа должна выводить и составленный текст SQL.

### Задание 9. «Паспорт» базы данных

Напиши программу, которая для любого `.db`-файла создаёт документ в формате Markdown (`schema.md`): каждая таблица — заголовок, её поля — таблица (имя, тип, NOT NULL, значение по умолчанию, PK), список внешних ключей. Такой документ можно добавить в README проекта.

## Домашнее задание

1. Установи дома DB Browser for SQLite, открой созданные на уроке файлы `center.db` и `game.db` и изучи их структуру. Попробуй вручную выполнить команду `CREATE TABLE` во вкладке «Execute SQL».
2. Создай базу данных своего проекта (задание 8 или задание 9 урока 54) с помощью функции `init_db()`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**

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
print("Таблица готова")
```

**Задание 2**

```python
import os
import sqlite3

path = input("Файл: ").strip()
if not os.path.exists(path):
    print("Файл не найден")
else:
    conn = sqlite3.connect(path)
    names = conn.execute(
        "SELECT name FROM sqlite_master WHERE type = 'table' AND name NOT LIKE 'sqlite_%'"
    ).fetchall()
    for (name,) in names:
        columns = conn.execute(f"PRAGMA table_info({name})").fetchall()
        print(f"{name} — полей: {len(columns)}")
    conn.close()
```

Примечание: имя таблицы можно подставлять через f-строку, потому что оно взято из самой БД (а не от пользователя). То, что **значения**, введённые пользователем, нельзя подставлять в SQL через f-строку, обсудим на следующем уроке (SQL-инъекция).

**Задание 3**

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
print("Таблицы game.db:", [t[0] for t in tables])
conn.close()
```

Результат:

```text
Таблицы game.db: ['players', 'scores']
```

**Задание 4.** Ошибки: (1) нет запятой после `id INTEGER PRIMARY KEY`; (2) `category TEXT,` — лишняя запятая после последнего поля; (3) две команды в одном `execute` → `executescript`; (4) `conn.close` — без скобок, функция не вызывается; (5) нет `IF NOT EXISTS` — при втором запуске `table products already exists`. (Дополнительно: `commit` — для CREATE `sqlite3` сохраняет автоматически, но по привычке его лучше писать.)

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
print("shop.db готова")
```

**Задание 5**

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

Результат:

```text
copies.book_id → books.id
loans.reader_id → readers.id
loans.copy_id → copies.id
```

**Задание 6**

```python
# db.py
import sqlite3


def get_connection(path):
    """Подключается и включает проверку внешних ключей."""
    conn = sqlite3.connect(path)
    conn.execute("PRAGMA foreign_keys = ON")
    return conn


def init_db(path, schema_sql):
    """Выполняет команды CREATE TABLE из schema_sql."""
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

Результат:

```text
['pets']
[('id', 'INTEGER', True), ('name', 'TEXT', False), ('kind', 'TEXT', False)]
```

**Задание 7**

```python
import sqlite3

conn = sqlite3.connect("my_first.db")
conn.execute("CREATE TABLE IF NOT EXISTS friends (id INTEGER PRIMARY KEY, name TEXT NOT NULL)")
columns = [row[1] for row in conn.execute("PRAGMA table_info(friends)")]
if "phone" in columns:
    print("Поле phone уже существует")
else:
    conn.execute("ALTER TABLE friends ADD COLUMN phone TEXT")
    conn.commit()
    print("Поле phone добавлено")
print([row[1] for row in conn.execute("PRAGMA table_info(friends)")])
conn.close()
```

Результат (при первом запуске, с файлом из задания 1):

```text
Поле phone добавлено
['id', 'name', 'city', 'birthday', 'phone']
```

**Задание 8**

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
print("Создано:", [r[0] for r in conn.execute("SELECT name FROM sqlite_master")])
conn.close()
```

Результат:

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
Создано: ['classes', 'sqlite_autoindex_classes_1', 'students']
```

Примечание: `sqlite_autoindex_...` — **индекс**, который SQLite сам создал для ограничения `UNIQUE` (вспомните задание 8 урока 53). Чтобы получить только таблицы, нужно добавить `WHERE type = 'table'`.

**Задание 9**

```python
import sqlite3


def schema_markdown(path):
    conn = sqlite3.connect(path)
    lines = [f"# База данных: {path}", ""]
    tables = conn.execute(
        "SELECT name FROM sqlite_master WHERE type = 'table' AND name NOT LIKE 'sqlite_%'"
    ).fetchall()
    for (table,) in tables:
        lines += [f"## {table}", "", "| Поле | Тип | NOT NULL | По умолчанию | PK |", "|---|---|---|---|---|"]
        for _, name, col_type, notnull, default, pk in conn.execute(f"PRAGMA table_info({table})"):
            lines.append(f"| {name} | {col_type} | {'да' if notnull else ''} | "
                         f"{default if default is not None else ''} | {'да' if pk else ''} |")
        fks = conn.execute(f"PRAGMA foreign_key_list({table})").fetchall()
        if fks:
            lines += ["", "Внешние ключи:"]
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

Результат:

```text
# База данных: demo.db

## classes

| Поле | Тип | NOT NULL | По умолчанию | PK |
|---|---|---|---|---|
| id | INTEGER |  |  | да |
| name | TEXT | да |  |  |

## students

| Поле | Тип | NOT NULL | По умолчанию | PK |
|---|---|---|---|---|
| id | INTEGER |  |  | да |
| name | TEXT | да |  |  |
| class_id | INTEGER |  |  |  |

Внешние ключи:
- `class_id` → `classes.id`
```

</details>
