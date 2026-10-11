# Урок 55. База данных SQLite. Модуль sqlite3. Подключение и создание таблицы.

## Темы

1. О SQLite
2. Основные шаги работы с модулем `sqlite3`
3. `commit()` и транзакции
4. `CREATE TABLE` — создание таблицы
5. Проверка структуры базы данных
6. Оборачиваем работу с БД в функцию
7. Частые ошибки
8. Основные понятия

## 1. О SQLite

**SQLite** — самая распространённая система управления базами данных в мире. Она используется в каждом смартфоне, в браузерах (Chrome, Firefox), в Telegram, Skype и даже в бортовых системах самолётов.

| Особенность | Значение |
|---|---|
| **Без сервера** | Не нужно устанавливать отдельный сервер: программа работает с файлом БД напрямую |
| **Один файл** | Вся база данных — один файл `.db` (или `.sqlite`); его можно копировать и пересылать |
| **Встроена в Python** | `import sqlite3` — ничего не устанавливаем |
| **Стандарт SQL** | Поддерживает основные команды SQL |
| **Ограничение** | Не предназначена для больших веб-сайтов, куда одновременно пишут тысячи людей |

**Аналогия:** PostgreSQL — хранилище большого банка (с охраной, отдельным зданием), а SQLite — твой личный сейф: маленький, но надёжный и всегда рядом.

## 2. Основные шаги работы с модулем `sqlite3`

```text
1. connect()   — подключение к БД (если файла нет, он создаётся)  → Connection
2. cursor()    — получение курсора («ручки», выполняющей команды SQL) → Cursor
3. execute()   — выполнение команды SQL
4. commit()    — сохранение изменений в файл
5. close()     — закрытие соединения
```

```python
import sqlite3

# 1. Подключение: если файла club.db нет — он создаётся заново
conn = sqlite3.connect("club.db")

# 2. Курсор
cur = conn.cursor()

# 3. Выполнение команды SQL
cur.execute("""
    CREATE TABLE IF NOT EXISTS students (
        id INTEGER PRIMARY KEY,
        first_name TEXT NOT NULL,
        last_name TEXT NOT NULL,
        birth_date TEXT
    )
""")

# 4. Сохранение изменений
conn.commit()

# 5. Закрытие
conn.close()
print("База данных и таблица students созданы")
```

Результат:

```text
База данных и таблица students созданы
```

В папке программы появится файл `club.db`. Если открыть его в Блокноте — увидишь «кракозябры» (это двоичный файл). Его первые байты: `SQLite format 3`.

### Объекты

| Объект | Что делает | Аналогия |
|---|---|---|
| `Connection` (`conn`) | Соединение с БД; управляет транзакциями (`commit`, `rollback`, `close`) | телефонный звонок |
| `Cursor` (`cur`) | Выполняет команды SQL, отдаёт результаты (`execute`, `fetchall`) | разговор во время звонка |

Можно писать и без курсора: `conn.execute(...)` — он сам создаёт курсор и возвращает его.

### `:memory:` — база данных в памяти

```python
import sqlite3

conn = sqlite3.connect(":memory:")   # без файла, только в оперативной памяти
conn.execute("CREATE TABLE test (id INTEGER PRIMARY KEY, name TEXT)")
print("БД в памяти готова. Версия SQLite:", sqlite3.sqlite_version)
conn.close()                         # при закрытии всё пропадает
```

Результат (версия зависит от компьютера):

```text
БД в памяти готова. Версия SQLite: 3.45.1
```

`:memory:` очень удобна для экспериментов и тестирования.

## 3. `commit()` и транзакции

**Транзакция** — группа действий, выполняемых по принципу «всё или ничего». `sqlite3` сам открывает транзакцию после команд, изменяющих данные (INSERT, UPDATE, DELETE), но **сам её не закрывает**. Если не вызвать `commit()`, при закрытии программы изменения **пропадут**.

**Аналогия:** ты написал документ в Word, но не нажал «Сохранить» — если компьютер выключится, работа пропадёт. `commit()` — кнопка «Сохранить», `rollback()` — «Закрыть без сохранения».

**Банковский пример:** снять 5000 ₸ со счёта Асем и положить на счёт Ерлана — это два действия. Если первое выполнилось, а во втором произошла ошибка, деньги «пропадут». Транзакция выполняет оба действия вместе или отменяет оба.

### Автоматический commit через `with`

```python
import sqlite3

conn = sqlite3.connect("club.db")
with conn:   # если блок завершился успешно — commit, при ошибке — rollback
    conn.execute("CREATE TABLE IF NOT EXISTS rooms (id INTEGER PRIMARY KEY, number TEXT)")
conn.close()  # обрати внимание: with НЕ закрывает соединение, а только управляет транзакцией
print("Таблица rooms готова")
```

Результат:

```text
Таблица rooms готова
```

> **Важно:** `with conn:` — не то же самое, что `with open(...)` при работе с файлами: он не закрывает соединение, а только делает commit/rollback. Для закрытия соединения используем `conn.close()` или `contextlib.closing`.

## 4. `CREATE TABLE` — создание таблицы

Общий синтаксис:

```sql
CREATE TABLE [IF NOT EXISTS] имя_таблицы (
    поле1 ТИП [ограничения],
    поле2 ТИП [ограничения],
    ...
    [ограничения уровня таблицы]
);
```

### Пример: база данных «Центр кружков»

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
conn.execute("PRAGMA foreign_keys = ON")   # включение проверки FK
conn.executescript(SCHEMA)                 # выполнение сразу нескольких команд
conn.commit()
conn.close()
print("center.db: создано 4 таблицы")
```

Результат:

```text
center.db: создано 4 таблицы
```

### Ограничения и ключевые слова

| Запись | Значение |
|---|---|
| `INTEGER PRIMARY KEY` | первичный ключ; если значение не задано, SQLite сам присваивает следующий номер |
| `AUTOINCREMENT` | не использовать повторно номера удалённых записей (необязательно) |
| `NOT NULL` | не должно быть пустым |
| `UNIQUE` | не должно повторяться |
| `DEFAULT значение` | если значение не задано — значение по умолчанию (`CURRENT_DATE` — сегодняшняя дата) |
| `CHECK (условие)` | значение должно удовлетворять условию |
| `REFERENCES таблица(поле)` | внешний ключ (на уровне поля) |
| `FOREIGN KEY (поле) REFERENCES ...` | внешний ключ (на уровне таблицы) |
| `UNIQUE (а, б)` | не должна повторяться **пара** полей |

### `PRAGMA foreign_keys = ON`

По историческим причинам в SQLite проверка внешних ключей **по умолчанию выключена**. Её нужно включать после каждого подключения:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
print(conn.execute("PRAGMA foreign_keys").fetchone())   # (1,) — включено
conn.close()
```

Результат:

```text
(1,)
```

### `IF NOT EXISTS` и `DROP TABLE`

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE notes (id INTEGER PRIMARY KEY, text TEXT)")
try:
    conn.execute("CREATE TABLE notes (id INTEGER PRIMARY KEY, text TEXT)")
except sqlite3.OperationalError as e:
    print("Ошибка:", e)

conn.execute("CREATE TABLE IF NOT EXISTS notes (id INTEGER PRIMARY KEY, text TEXT)")
print("IF NOT EXISTS — ошибки нет")

conn.execute("DROP TABLE IF EXISTS notes")   # удаление таблицы вместе с данными!
print("Таблица notes удалена")
conn.close()
```

Результат:

```text
Ошибка: table notes already exists
IF NOT EXISTS — ошибки нет
Таблица notes удалена
```

> `DROP TABLE` удаляет таблицу **со всеми данными**, «Отменить» это невозможно. Будь осторожен!

Добавить поле в таблицу позже: `ALTER TABLE students ADD COLUMN email TEXT;`

## 5. Проверка структуры базы данных

### С помощью Python

В каждой базе SQLite есть системная таблица `sqlite_master` — в ней хранятся список всех таблиц и их SQL-описание. Чтобы получить результат, используем `fetchall()` (все строки) или `fetchone()` (одна строка).

```python
import sqlite3

conn = sqlite3.connect("center.db")

tables = conn.execute(
    "SELECT name FROM sqlite_master WHERE type = 'table' AND name NOT LIKE 'sqlite_%'"
).fetchall()
print("Таблицы:", [t[0] for t in tables])

print("\nСтруктура таблицы clubs:")
for cid, name, col_type, notnull, default, pk in conn.execute("PRAGMA table_info(clubs)"):
    flags = []
    if pk:
        flags.append("PK")
    if notnull:
        flags.append("NOT NULL")
    if default is not None:
        flags.append(f"DEFAULT {default}")
    print(f"  {name:<11} {col_type:<8} {' '.join(flags)}")

print("\nВнешние ключи clubs:")
for row in conn.execute("PRAGMA foreign_key_list(clubs)"):
    print(f"  {row[3]} → {row[2]}.{row[4]}")
conn.close()
```

Результат:

```text
Таблицы: ['teachers', 'clubs', 'students', 'enrollments']

Структура таблицы clubs:
  id          INTEGER  PK
  title       TEXT     NOT NULL
  min_age     INTEGER  DEFAULT 10
  price       REAL     DEFAULT 0
  teacher_id  INTEGER  

Внешние ключи clubs:
  teacher_id → teachers.id
```

(Системную таблицу `sqlite_sequence` создаёт сам `AUTOINCREMENT` — поэтому мы исключили её через `NOT LIKE 'sqlite_%'`.)

### С помощью DB Browser for SQLite

1. Скачиваем и устанавливаем программу с сайта **sqlitebrowser.org** (бесплатно).
2. **Open Database** → выбираем файл `center.db`.
3. Вкладка **Database Structure** — таблицы, поля, типы.
4. **Browse Data** — данные в таблице (пока пусто).
5. **Execute SQL** — можно вручную выполнять команды SQL.

> Если вносишь изменения в DB Browser, не забудь нажать **Write Changes** (это и есть `commit`). Если программа на Python и DB Browser одновременно изменяют один файл, может возникнуть ошибка `database is locked` — сохрани изменения в DB Browser или закрой его.

## 6. Оборачиваем работу с БД в функцию

В проекте код, создающий БД, выносим в отдельную функцию (или модуль):

```python
import sqlite3
from pathlib import Path

DB_PATH = Path(__file__).with_name("library.db")


def get_connection():
    """Подключается к БД и включает проверку внешних ключей."""
    conn = sqlite3.connect(DB_PATH)
    conn.execute("PRAGMA foreign_keys = ON")
    return conn


def init_db():
    """Создаёт таблицы, если их нет. Вызывается один раз в начале программы."""
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
    print("library.db готова:", DB_PATH.exists())
```

Результат:

```text
library.db готова: True
```

Функцию `init_db()` можно вызывать несколько раз — благодаря `IF NOT EXISTS` существующие таблицы не пострадают.

## Частые ошибки

**1. Забыт `commit()`** → данные не сохраняются (особенно при INSERT/UPDATE). Правильно: `conn.commit()` или `with conn:`.

**2. Несколько команд в одном `execute()`.**

```python
import sqlite3

conn = sqlite3.connect(":memory:")
try:
    conn.execute("CREATE TABLE a (id INTEGER); CREATE TABLE b (id INTEGER);")
except sqlite3.ProgrammingError as e:
    print("Ошибка:", e)
conn.executescript("CREATE TABLE a (id INTEGER); CREATE TABLE b (id INTEGER);")
print("executescript — правильно")
conn.close()
```

Результат:

```text
Ошибка: You can only execute one statement at a time.
executescript — правильно
```

**3. Синтаксическая ошибка в SQL** → `sqlite3.OperationalError: near "...": syntax error`. Частые причины: лишняя запятая после последнего поля, не закрыта скобка, имя поля совпадает с ключевым словом SQL (`order`, `group`) — нужно выбрать другое имя.

```sql
-- Ошибка: запятая после последнего поля
CREATE TABLE t (id INTEGER, name TEXT,);
-- Правильно
CREATE TABLE t (id INTEGER, name TEXT);
```

**4. Файл создан в другой папке.** `sqlite3.connect("club.db")` создаёт файл в **текущей рабочей папке**. Правильно: `Path(__file__).with_name("club.db")`.

**5. Внешние ключи не проверяются.** Забыт `PRAGMA foreign_keys = ON`. Правильно: включать после каждого подключения (в функции `get_connection`).

**6. `database is locked`** — БД удерживается другой программой (DB Browser) или соединение не закрыто. Правильно: `close()`, в DB Browser — Write/Close.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| SQLite | Однофайловая СУБД без сервера |
| `sqlite3` | Стандартный модуль Python для работы с SQLite |
| `Connection` | Объект соединения с БД (возвращает `connect()`) |
| `Cursor` | Объект, выполняющий команды SQL |
| `execute()` / `executescript()` | выполнение одной / нескольких команд SQL |
| `commit()` / `rollback()` | сохранение / отмена транзакции |
| Транзакция | Группа действий, выполняемых по принципу «всё или ничего» |
| `CREATE TABLE` | команда создания таблицы |
| `IF NOT EXISTS` | не выдавать ошибку, если таблица существует |
| `DROP TABLE` | удаление таблицы вместе с данными |
| `PRAGMA` | специальные команды настройки/информации SQLite |
| `sqlite_master` | системная таблица со списком всех таблиц |

## Примечание

- Начиная с Python 3.12 у `sqlite3.connect()` есть параметр `autocommit`; рекомендуемое значение `autocommit=False` даёт поведение по стандарту PEP 249 (транзакция всегда открыта, нужен явный `commit()`). По умолчанию пока сохраняется старый режим (`LEGACY_TRANSACTION_CONTROL`).
- `executescript()` перед выполнением сам делает `COMMIT` незавершённой транзакции, а параметры `?` в нём не поддерживаются — используйте его только для фиксированных скриптов (создание схемы).
- `PRAGMA foreign_keys = ON` не действует внутри открытой транзакции, поэтому его выполняют сразу после `connect()`.
- `INTEGER PRIMARY KEY` — псевдоним встроенного `rowid`; `AUTOINCREMENT` нужен редко и немного замедляет вставку, так как ведёт таблицу `sqlite_sequence`.
- Соединение нельзя по умолчанию использовать из другого потока (`check_same_thread=True`); для многопоточных программ каждый поток открывает своё соединение.
- Документация: https://docs.python.org/3/library/sqlite3.html и https://www.sqlite.org/lang_createtable.html
