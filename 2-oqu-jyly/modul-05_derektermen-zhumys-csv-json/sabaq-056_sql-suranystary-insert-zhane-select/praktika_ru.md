# Урок 56. Практические задания: INSERT и SELECT

Во всех заданиях значения передаются в SQL **только через `?`**. После изменения данных не забывай `commit()`.

## Базовый уровень

### Задание 1. Каталог книг

Создай в базе данных `books.db` таблицу `books`: `id` (PK), `title` (NOT NULL), `author` (NOT NULL), `year` (INTEGER), `pages` (INTEGER), `genre` (TEXT). Добавь с помощью `executemany` не менее 6 книг (из казахской литературы), затем:
1. выведи все книги;
2. выведи только название и автора;
3. выведи список неповторяющихся жанров;
4. выведи книги, изданные после 1950 года.

```text
Вывод (фрагмент):
Всего книг: 6
«Путь Абая» — Мухтар Ауэзов
...
Жанры: роман, повесть, философия
После 1950: Кочевники (1976), Меня зовут Кожа (1957), ...
```

### Задание 2. Форма регистрации (консоль)

Программа в цикле запрашивает имя, класс и возраст ученика и добавляет его в таблицу `members` базы `club.db` (пустое имя — остановка). После каждого добавления выводит `lastrowid`: «Ты зарегистрирован под № <id>!». В конце выводит общее количество участников.

```text
Ввод:
Имя: Алихан
Класс: 7Б
Возраст: 13
Вывод:
Ты зарегистрирован под № 1!
Имя:
Всего зарегистрировано: 1
```

### Задание 3. Поиск по id

Найди участника в таблице `members` по `id`, введённому пользователем, с помощью `fetchone()`. Используя `sqlite3.Row`, выведи поля по именам. Если не найден — «Такого номера нет».

### Задание 4. Проверка ограничений

Создай таблицу `products`: `name TEXT NOT NULL UNIQUE`, `price REAL CHECK (price > 0)`, `qty INTEGER DEFAULT 0`. Попробуй добавить такие записи, перехвати каждую ошибку через `try/except sqlite3.IntegrityError` и выведи понятное сообщение:

```text
("Тетрадь", 150, 20)  → добавлено
("Ручка", -50, 10)    → цена отрицательная
("Тетрадь", 160, 5)   → название повторяется
(None, 100, 1)        → название пустое
("Линейка", 180)      → qty не указано (по умолчанию 0) → добавлено
```

## Продвинутый уровень

### Задание 5. Импорт CSV → SQLite

Прочитай файл `journal.csv` с урока 50 и импортируй все его записи в таблицу `grades` базы `journal.db` с помощью `executemany` (`csv.DictReader` + именованные параметры `:name`). После импорта выведи через SELECT: количество записей, тех, кто получил «5» по всем предметам, и список класса 9Б.

### Задание 6. Демонстрация SQL-инъекции

Напиши две функции проверки логина и пароля: `login_unsafe` (f-строка) и `login_safe` (параметры). Сделай три теста: верный пароль, неверный пароль, инъекция `' OR '1'='1`. Выведи результаты в виде таблицы.

```text
Тест                 | unsafe | safe
верный пароль        | вошёл  | вошёл
неверный пароль      | нет    | нет
инъекция             | вошёл  | нет
```

### Задание 7. Рекорды игры

Напиши функции для базы `game.db` (`players`, `scores`) с урока 55:
- `get_or_create_player(nickname)` — если игрок есть, возвращает его `id`, если нет — добавляет нового и возвращает его `id`;
- `save_score(nickname, points, level)` — сохраняет результат;
- `player_scores(nickname)` — возвращает все результаты игрока.

## ★ Олимпиадная / творческая задача

### Задание 8. Словарь-переводчик

Пусть в базе `dictionary.db` есть таблица `words(id, kk, en, ru, topic)`. Программа:
- добавляет не менее 20 слов (темы: «школа», «природа», «IT») с помощью `executemany`;
- если введено казахское слово — выводит английский и русский перевод;
- если введена тема — выводит все слова этой темы;
- проводит небольшой тест по 5 случайным словам (`ORDER BY RANDOM() LIMIT 5`).

### Задание 9. Измерение производительности

Добавь 10 000 записей тремя способами и измерь время: (а) `commit` после каждого `execute`; (б) `execute` в цикле, в конце один `commit`; (в) `executemany` + один `commit`. Объясни результат (стоимость транзакции).

## Домашнее задание

1. Заверши задание 8 или создай в SQLite свой «Каталог фильмов/игр»: не менее 10 записей, 5 разных SELECT-запросов.
2. В DB Browser for SQLite во вкладке «Execute SQL» вручную выполни команды `INSERT` и `SELECT` для своей таблицы.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**

```python
import sqlite3

books = [
    ("Путь Абая", "Мухтар Ауэзов", 1942, 512, "роман"),
    ("Кочевники", "Ильяс Есенберлин", 1976, 640, "роман"),
    ("Меня зовут Кожа", "Бердибек Сокпакбаев", 1957, 176, "повесть"),
    ("Акбилек", "Жусипбек Аймауытов", 1928, 288, "роман"),
    ("Слова назидания", "Абай Кунанбайулы", 1890, 128, "философия"),
    ("Кровь и пот", "Абдижамил Нурпеисов", 1970, 704, "роман"),
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
print("Всего книг:", len(rows))
for title, author in conn.execute("SELECT title, author FROM books"):
    print(f"«{title}» — {author}")
genres = [g[0] for g in conn.execute("SELECT DISTINCT genre FROM books")]
print("Жанры:", ", ".join(genres))
recent = conn.execute("SELECT title, year FROM books WHERE year > ?", (1950,)).fetchall()
print("После 1950:", ", ".join(f"{t} ({y})" for t, y in recent))
conn.close()
```

Результат:

```text
Всего книг: 6
«Путь Абая» — Мухтар Ауэзов
«Кочевники» — Ильяс Есенберлин
«Меня зовут Кожа» — Бердибек Сокпакбаев
«Акбилек» — Жусипбек Аймауытов
«Слова назидания» — Абай Кунанбайулы
«Кровь и пот» — Абдижамил Нурпеисов
Жанры: роман, повесть, философия
После 1950: Кочевники (1976), Меня зовут Кожа (1957), Кровь и пот (1970)
```

**Задание 2**

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
    name = input("Имя: ").strip()
    if not name:
        break
    cls = input("Класс: ").strip()
    age = int(input("Возраст: "))
    cur = conn.execute("INSERT INTO members (name, class, age) VALUES (?, ?, ?)", (name, cls, age))
    conn.commit()
    print(f"Ты зарегистрирован под № {cur.lastrowid}!")

print("Всего зарегистрировано:", conn.execute("SELECT COUNT(*) FROM members").fetchone()[0])
conn.close()
```

**Задание 3**

```python
import sqlite3

conn = sqlite3.connect("club.db")
conn.row_factory = sqlite3.Row
member_id = int(input("Номер: "))
row = conn.execute("SELECT * FROM members WHERE id = ?", (member_id,)).fetchone()
if row is None:
    print("Такого номера нет")
else:
    print(f"Имя: {row['name']}, класс: {row['class']}, возраст: {row['age']}")
conn.close()
```

**Задание 4**

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

tests = [("Тетрадь", 150, 20), ("Ручка", -50, 10), ("Тетрадь", 160, 5), (None, 100, 1), ("Линейка", 180)]
messages = {"CHECK": "цена отрицательная", "UNIQUE": "название повторяется", "NOT NULL": "название пустое"}

for item in tests:
    columns = "name, price, qty" if len(item) == 3 else "name, price"
    marks = ", ".join("?" * len(item))
    try:
        conn.execute(f"INSERT INTO products ({columns}) VALUES ({marks})", item)
        print(item, "→ добавлено")
    except sqlite3.IntegrityError as e:
        reason = next((m for key, m in messages.items() if str(e).startswith(key)), str(e))
        print(item, "→", reason)
conn.commit()
print(conn.execute("SELECT name, price, qty FROM products").fetchall())
conn.close()
```

Результат:

```text
('Тетрадь', 150, 20) → добавлено
('Ручка', -50, 10) → цена отрицательная
('Тетрадь', 160, 5) → название повторяется
(None, 100, 1) → название пустое
('Линейка', 180) → добавлено
[('Тетрадь', 150.0, 20), ('Линейка', 180.0, 0)]
```

Примечание: здесь f-строка составляет только **имена полей и количество `?`** — значения всё равно передаются через параметры, поэтому это безопасно.

**Задание 5**

```python
import csv
import sqlite3

rows = [
    ["name", "class", "math", "informatics", "physics"],
    ["Айгерим Нурланкызы", "9А", 5, 5, 4],
    ["Бауыржан Сеитов", "9А", 3, 4, 4],
    ["Даулет Оспанов", "9Б", 4, 5, 5],
    ["Сымбат Алиева", "9Б", 5, 5, 5],
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

print("Записей:", conn.execute("SELECT COUNT(*) FROM grades").fetchone()[0])
best = conn.execute("SELECT name FROM grades WHERE math = 5 AND informatics = 5 AND physics = 5").fetchall()
print("Отличники:", [b[0] for b in best])
print("9Б:", [r[0] for r in conn.execute("SELECT name FROM grades WHERE class = ?", ("9Б",))])
conn.close()
```

Результат:

```text
Записей: 4
Отличники: ['Сымбат Алиева']
9Б: ['Даулет Оспанов', 'Сымбат Алиева']
```

Примечание: значения из CSV — строки (`'5'`), но при записи в поле `INTEGER` SQLite превращает их в числа (приведение типов, type affinity). Оператор `AND` подробно объясняется на следующем уроке.

**Задание 6**

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


tests = [("верный пароль", "Tulpar2027"), ("неверный пароль", "12345"), ("инъекция", "' OR '1'='1")]
print(f"{'Тест':<20} | unsafe | safe")
for title, pwd in tests:
    u = "вошёл" if login_unsafe("aida", pwd) else "нет"
    s = "вошёл" if login_safe("aida", pwd) else "нет"
    print(f"{title:<20} | {u:<6} | {s}")
conn.close()
```

Результат:

```text
Тест                 | unsafe | safe
верный пароль        | вошёл  | вошёл
неверный пароль      | нет    | нет
инъекция             | вошёл  | нет
```

**Задание 7**

```python
import sqlite3
from datetime import datetime

conn = sqlite3.connect(":memory:")   # в реальном проекте: "game.db"
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
print("Игроки:", conn.execute("SELECT * FROM players").fetchall())
conn.close()
```

Результат:

```text
Qyran: [(340, 1), (520, 2)]
Игроки: [(1, 'Qyran'), (2, 'Tulpar')]
```

**Задание 8 (основная часть)**

```python
import sqlite3

WORDS = [
    ("мектеп", "school", "школа", "школа"), ("мұғалім", "teacher", "учитель", "школа"),
    ("дәптер", "notebook", "тетрадь", "школа"), ("сабақ", "lesson", "урок", "школа"),
    ("кітап", "book", "книга", "школа"), ("тау", "mountain", "гора", "природа"),
    ("өзен", "river", "река", "природа"), ("орман", "forest", "лес", "природа"),
    ("дала", "steppe", "степь", "природа"), ("көл", "lake", "озеро", "природа"),
    ("бағдарлама", "program", "программа", "IT"), ("компьютер", "computer", "компьютер", "IT"),
    ("пернетақта", "keyboard", "клавиатура", "IT"), ("тінтуір", "mouse", "мышь", "IT"),
    ("желі", "network", "сеть", "IT"), ("файл", "file", "файл", "IT"),
    ("деректер қоры", "database", "база данных", "IT"), ("кесте", "table", "таблица", "IT"),
    ("жаңбыр", "rain", "дождь", "природа"), ("қоңырау", "bell", "звонок", "школа"),
]

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE words (id INTEGER PRIMARY KEY, kk TEXT UNIQUE, en TEXT, ru TEXT, topic TEXT)")
conn.executemany("INSERT INTO words (kk, en, ru, topic) VALUES (?, ?, ?, ?)", WORDS)
conn.commit()

word = "орман"   # в реальной программе: input()
row = conn.execute("SELECT en, ru FROM words WHERE kk = ?", (word,)).fetchone()
print(f"{word}: {row[0]} / {row[1]}" if row else "Слово не найдено")

topic = "IT"
print(f"{topic}:", ", ".join(r[0] for r in conn.execute("SELECT kk FROM words WHERE topic = ?", (topic,))))

quiz = conn.execute("SELECT kk, en FROM words ORDER BY RANDOM() LIMIT 5").fetchall()
print("Слов в тесте:", len(quiz))
conn.close()
```

Результат:

```text
орман: forest / лес
IT: бағдарлама, компьютер, пернетақта, тінтуір, желі, файл, деректер қоры, кесте
Слов в тесте: 5
```

**Задание 9**

```python
import os
import sqlite3
import time

data = [(f"Ученик {i}", i % 100) for i in range(10_000)]


def fresh(path="speed.db"):
    if os.path.exists(path):
        os.remove(path)
    conn = sqlite3.connect(path)
    conn.execute("CREATE TABLE t (name TEXT, score INTEGER)")
    return conn


conn = fresh()
start = time.perf_counter()
for row in data[:1000]:                       # только 1000 — очень медленно!
    conn.execute("INSERT INTO t VALUES (?, ?)", row)
    conn.commit()
a = (time.perf_counter() - start) * 10        # в пересчёте на 10 000
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

print(f"(а) commit после каждой строки: ~{a:.2f} с")
print(f"(б) один commit:                 {b:.3f} с")
print(f"(в) executemany:                 {c:.3f} с")
```

Объяснение: каждый `commit` требует физической записи данных на диск (самая дорогая операция). Поэтому добавлять много записей **в одной транзакции** в десятки-сотни раз быстрее; `executemany` к тому же уменьшает затраты на цикл на уровне Python.

</details>
