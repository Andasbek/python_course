# Урок 56. SQL-запросы: INSERT и SELECT.

## Темы

1. Подготовка: рабочая база
2. `INSERT INTO` — добавление записи
3. SQL-инъекция — самая опасная ошибка
4. При нарушении ограничений: `IntegrityError`
5. `SELECT` — чтение данных
6. Способы получения результата
7. Добавление в связанные таблицы
8. Частые ошибки
9. Основные понятия

## 1. Подготовка: рабочая база

Чтобы повторить примеры, в начале создаём небольшую базу:

```python
import sqlite3

conn = sqlite3.connect("school.db")
conn.executescript("""
    DROP TABLE IF EXISTS students;
    CREATE TABLE students (
        id INTEGER PRIMARY KEY,
        first_name TEXT NOT NULL,
        last_name TEXT NOT NULL,
        class TEXT,
        city TEXT DEFAULT 'Алматы',
        score INTEGER CHECK (score BETWEEN 0 AND 100)
    );
""")
conn.close()
print("school.db готова")
```

Результат:

```text
school.db готова
```

## 2. `INSERT INTO` — добавление записи

Синтаксис:

```sql
INSERT INTO таблица (поле1, поле2, ...) VALUES (значение1, значение2, ...);
```

```sql
INSERT INTO students (first_name, last_name, class, score)
VALUES ('Айгерим', 'Нурланкызы', '9А', 95);
```

- Список полей и список значений должны совпадать **по порядку**.
- Поля, которых нет в списке, получают значение `DEFAULT` (или `NULL`): в примере выше `city = 'Алматы'`.
- Если `id` не указан, SQLite сам присваивает следующий номер.
- Текст в SQL — **в одинарных кавычках**: `'Айгерим'`.

### В Python: только через параметры!

Значения **никогда** не добавляем в строку SQL через f-строку или `+`. Вместо этого ставим **заполнитель** (placeholder) `?`, а значения передаём отдельным кортежем:

```python
import sqlite3

conn = sqlite3.connect("school.db")
cur = conn.cursor()

cur.execute(
    "INSERT INTO students (first_name, last_name, class, score) VALUES (?, ?, ?, ?)",
    ("Айгерим", "Нурланкызы", "9А", 95),
)
print("id новой записи:", cur.lastrowid)

first, last = "Бауыржан", "Сеитов"
cur.execute(
    "INSERT INTO students (first_name, last_name, class, city, score) VALUES (?, ?, ?, ?, ?)",
    (first, last, "9А", "Конаев", 78),
)
print("id новой записи:", cur.lastrowid)

conn.commit()      # обязательно!
conn.close()
```

Результат:

```text
id новой записи: 1
id новой записи: 2
```

`cur.lastrowid` — `id` последней добавленной записи. Он нужен при добавлении записей в связанные таблицы (например, добавить нового ученика и сразу записать его в кружок).

> **Кортеж из одного элемента:** `(95,)` — с запятой! `(95)` — просто число, а не кортеж.

### Именованные параметры `:name`

Если полей много, удобен словарь:

```python
import sqlite3

conn = sqlite3.connect("school.db")
student = {"first": "Даулет", "last": "Оспанов", "cls": "9Б", "score": 88}
conn.execute(
    "INSERT INTO students (first_name, last_name, class, score) VALUES (:first, :last, :cls, :score)",
    student,
)
conn.commit()
conn.close()
print("Даулет добавлен")
```

Результат:

```text
Даулет добавлен
```

### `executemany` — добавление многих записей сразу

```python
import sqlite3

students = [
    ("Сымбат", "Алиева", "9Б", "Алматы", 100),
    ("Ерлан", "Жакыпов", "9А", "Талгар", 64),
    ("Мадина", "Касымова", "9Б", "Алматы", 91),
    ("Темирлан", "Ахметов", "10А", "Конаев", 73),
]

conn = sqlite3.connect("school.db")
conn.executemany(
    "INSERT INTO students (first_name, last_name, class, city, score) VALUES (?, ?, ?, ?, ?)",
    students,
)
conn.commit()
total = conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]
conn.close()
print("Всего учеников в таблице:", total)
```

Результат:

```text
Всего учеников в таблице: 7
```

`executemany` повторяет команду для каждого кортежа из списка — это быстрее, чем вызывать `execute` в цикле.

## 3. SQL-инъекция — самая опасная ошибка

Почему нельзя использовать f-строку? Текст, введённый пользователем, может стать **частью SQL-кода**:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE users (login TEXT, password TEXT)")
conn.execute("INSERT INTO users VALUES ('admin', 'Qwerty2027')")

login = "admin"
password = "' OR '1'='1"          # «пароль», введённый злоумышленником

# ОПАСНО: прямое добавление текста в SQL
sql = f"SELECT * FROM users WHERE login = '{login}' AND password = '{password}'"
print(sql)
print("f-строка:", "Вход разрешён!" if conn.execute(sql).fetchone() else "Вход запрещён")

# БЕЗОПАСНО: параметры
row = conn.execute("SELECT * FROM users WHERE login = ? AND password = ?", (login, password)).fetchone()
print("Параметр:", "Вход разрешён!" if row else "Вход запрещён")
conn.close()
```

Результат:

```text
SELECT * FROM users WHERE login = 'admin' AND password = '' OR '1'='1'
f-строка: Вход разрешён!
Параметр: Вход запрещён
```

Злоумышленник вошёл в систему, не зная пароля! `'1'='1'` всегда истинно. А при использовании параметров СУБД воспринимает введённый текст **только как значение**, но никогда как код.

> **Золотое правило:** любое значение, пришедшее от пользователя, передаётся в SQL-запрос только через `?` или `:name`.

(Примечание: в реальных системах пароли не хранятся в открытом виде, хранится их хеш — этот пример нужен только для демонстрации инъекции.)

## 4. При нарушении ограничений: `IntegrityError`

```python
import sqlite3

conn = sqlite3.connect("school.db")
bad_rows = [
    ("Арман", "Бекулы", "9А", "Алматы", 150),   # CHECK: score 0..100
    (None, "Неизвестный", "9Б", "Алматы", 80),     # NOT NULL
]
for row in bad_rows:
    try:
        conn.execute(
            "INSERT INTO students (first_name, last_name, class, city, score) VALUES (?, ?, ?, ?, ?)",
            row,
        )
    except sqlite3.IntegrityError as e:
        print("Не добавлено:", e)
conn.close()
```

Результат:

```text
Не добавлено: CHECK constraint failed: score BETWEEN 0 AND 100
Не добавлено: NOT NULL constraint failed: students.first_name
```

База данных **сама** остановила неправильные данные — в CSV/JSON такой защиты не было. При нарушении `UNIQUE` — `UNIQUE constraint failed`, при нарушении внешнего ключа — `FOREIGN KEY constraint failed`.

## 5. `SELECT` — чтение данных

Базовый синтаксис:

```sql
SELECT поля FROM таблица [WHERE условие] [LIMIT n];
```

### Все поля: `*`

```python
import sqlite3

conn = sqlite3.connect("school.db")
cur = conn.execute("SELECT * FROM students")
rows = cur.fetchall()
print("Количество записей:", len(rows))
for row in rows:
    print(row)
conn.close()
```

Результат:

```text
Количество записей: 7
(1, 'Айгерим', 'Нурланкызы', '9А', 'Алматы', 95)
(2, 'Бауыржан', 'Сеитов', '9А', 'Конаев', 78)
(3, 'Даулет', 'Оспанов', '9Б', 'Алматы', 88)
(4, 'Сымбат', 'Алиева', '9Б', 'Алматы', 100)
(5, 'Ерлан', 'Жакыпов', '9А', 'Талгар', 64)
(6, 'Мадина', 'Касымова', '9Б', 'Алматы', 91)
(7, 'Темирлан', 'Ахметов', '10А', 'Конаев', 73)
```

Каждая запись приходит в Python в виде **кортежа**, порядок полей — как в таблице.

### Выбранные поля и `AS`

```python
import sqlite3

conn = sqlite3.connect("school.db")
for first, last, score in conn.execute("SELECT first_name, last_name, score FROM students LIMIT 3"):
    print(f"{last} {first}: {score}")

print(conn.execute("SELECT first_name || ' ' || last_name AS full_name FROM students LIMIT 1").fetchone())
conn.close()
```

Результат:

```text
Нурланкызы Айгерим: 95
Сеитов Бауыржан: 78
Оспанов Даулет: 88
('Айгерим Нурланкызы',)
```

- `LIMIT 3` — только первые 3 записи;
- `||` — объединение строк в SQL;
- `AS full_name` — новое имя (псевдоним, alias) для столбца результата;
- курсор можно перебирать прямо циклом `for`.

### `DISTINCT` — неповторяющиеся значения

```python
import sqlite3

conn = sqlite3.connect("school.db")
cities = conn.execute("SELECT DISTINCT city FROM students").fetchall()
print("Города:", [c[0] for c in cities])
conn.close()
```

Результат:

```text
Города: ['Алматы', 'Конаев', 'Талгар']
```

### Простое условие `WHERE`

`WHERE` выбирает только записи, удовлетворяющие условию. Условия можно объединять операторами `AND`, `OR`, `NOT`, а также использовать `LIKE`, `BETWEEN`, `IN`.

```python
import sqlite3

conn = sqlite3.connect("school.db")

cls = "9Б"
rows = conn.execute("SELECT first_name, score FROM students WHERE class = ?", (cls,)).fetchall()
print(f"Класс {cls}:", rows)

rows = conn.execute("SELECT first_name, score FROM students WHERE score >= ?", (90,)).fetchall()
print("90 и выше:", rows)
conn.close()
```

Результат:

```text
Класс 9Б: [('Даулет', 88), ('Сымбат', 100), ('Мадина', 91)]
90 и выше: [('Айгерим', 95), ('Сымбат', 100), ('Мадина', 91)]
```

Операторы сравнения: `=`, `<>` (или `!=`), `<`, `>`, `<=`, `>=`. Обрати внимание: в SQL равенство — один знак `=`.

### Вычисления в SQL

```python
import sqlite3

conn = sqlite3.connect("school.db")
count = conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]
row = conn.execute("SELECT MAX(score), MIN(score), AVG(score) FROM students").fetchone()
print("Всего:", count)
print(f"Наибольший: {row[0]}, наименьший: {row[1]}, средний: {row[2]:.1f}")
conn.close()
```

Результат:

```text
Всего: 7
Наибольший: 100, наименьший: 64, средний: 84.1
```

## 6. Способы получения результата

| Способ | Результат | Когда |
|---|---|---|
| `cur.fetchone()` | одна запись (кортеж) или `None` | если ожидается одна запись (поиск по id, COUNT) |
| `cur.fetchall()` | список всех записей | если результат небольшой |
| `cur.fetchmany(n)` | следующие `n` записей | обработка большого результата частями |
| `for row in cur:` | записи по одной | большой результат, экономия памяти |

```python
import sqlite3

conn = sqlite3.connect("school.db")
student_id = 42
row = conn.execute("SELECT first_name FROM students WHERE id = ?", (student_id,)).fetchone()
if row is None:
    print(f"Ученик с id={student_id} не найден")
else:
    print("Найден:", row[0])

cur = conn.execute("SELECT first_name FROM students")
print("Первые два:", cur.fetchmany(2))
print("Следующие два:", cur.fetchmany(2))
conn.close()
```

Результат:

```text
Ученик с id=42 не найден
Первые два: [('Айгерим',), ('Бауыржан',)]
Следующие два: [('Даулет',), ('Сымбат',)]
```

### `sqlite3.Row` — доступ по имени поля

`row[3]` — непонятно, какое это поле. С настройкой `row_factory = sqlite3.Row` читаем запись по имени, как словарь:

```python
import sqlite3

conn = sqlite3.connect("school.db")
conn.row_factory = sqlite3.Row
row = conn.execute("SELECT * FROM students WHERE id = ?", (4,)).fetchone()
print(row["first_name"], row["class"], row["score"])
print("Поля:", row.keys())
print(dict(row))
conn.close()
```

Результат:

```text
Сымбат 9Б 100
Поля: ['id', 'first_name', 'last_name', 'class', 'city', 'score']
{'id': 4, 'first_name': 'Сымбат', 'last_name': 'Алиева', 'class': '9Б', 'city': 'Алматы', 'score': 100}
```

## 7. Добавление в связанные таблицы

Добавить ученика и сразу записать его в кружок — пример использования `lastrowid` и транзакции:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    CREATE TABLE clubs (id INTEGER PRIMARY KEY, title TEXT NOT NULL UNIQUE);
    CREATE TABLE kids (id INTEGER PRIMARY KEY, name TEXT NOT NULL);
    CREATE TABLE enrollments (
        kid_id INTEGER NOT NULL REFERENCES kids(id),
        club_id INTEGER NOT NULL REFERENCES clubs(id),
        UNIQUE (kid_id, club_id)
    );
    INSERT INTO clubs (title) VALUES ('Python'), ('Робототехника');
""")

with conn:   # два INSERT сохраняются вместе или вместе отменяются
    cur = conn.execute("INSERT INTO kids (name) VALUES (?)", ("Алихан",))
    kid_id = cur.lastrowid
    conn.execute("INSERT INTO enrollments (kid_id, club_id) VALUES (?, ?)", (kid_id, 1))

try:
    with conn:
        conn.execute("INSERT INTO enrollments (kid_id, club_id) VALUES (?, ?)", (kid_id, 99))
except sqlite3.IntegrityError as e:
    print("Ошибка:", e)

print(conn.execute("SELECT * FROM enrollments").fetchall())
conn.close()
```

Результат:

```text
Ошибка: FOREIGN KEY constraint failed
[(1, 1)]
```

В одной команде `INSERT` можно добавить несколько записей: `VALUES ('Python'), ('Робототехника')`.

## Частые ошибки

**1. Нет `commit()`** → при закрытии программы добавленные записи пропадают. Самая частая ошибка!

**2. Количество параметров не совпадает.**

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE t (a TEXT, b TEXT)")
try:
    conn.execute("INSERT INTO t (a, b) VALUES (?, ?)", ("только одно",))
except sqlite3.ProgrammingError as e:
    print("Ошибка:", e)
conn.close()
```

Результат:

```text
Ошибка: Incorrect number of bindings supplied. The current statement uses 2, and there are 1 supplied.
```

**3. Передача одного значения без кортежа.** `execute("... WHERE id = ?", (5))` → ошибка; правильно: `(5,)`. Со строкой: `("9А")` — это строка, а не кортеж, поэтому Python воспринимает её как последовательность из 2 символов.

**4. Составление SQL через f-строку** → SQL-инъекция. Правильно: `?`.

**5. `fetchone()[0]`, когда результата нет** → `TypeError: 'NoneType' object is not subscriptable`. Правильно: сначала `if row is None`.

**6. Двойные кавычки в SQL.** `WHERE city = "Алматы"` — SQLite иногда воспринимает это как имя поля. Правильно: текст в SQL — в одинарных кавычках `'Алматы'`, а из Python — через `?`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `INSERT INTO ... VALUES` | Добавление новой записи в таблицу |
| Заполнитель (placeholder) | `?` или `:name` в SQL — значение передаётся позже |
| `executemany` | Выполнение одной команды с многими наборами данных |
| `lastrowid` | id последней добавленной записи |
| SQL-инъекция | Атака, изменяющая SQL-код через вводимый текст |
| `IntegrityError` | Ошибка при нарушении ограничения (NOT NULL, UNIQUE, CHECK, FK) |
| `SELECT ... FROM` | Чтение данных из таблицы |
| `*` | Все поля |
| `AS` | Присвоение псевдонима столбцу результата |
| `DISTINCT` | Удаление повторяющихся строк |
| `LIMIT` | Ограничение количества результатов |
| `fetchone` / `fetchall` / `fetchmany` | Получение результата в виде одной / всех / n записей |
| `sqlite3.Row` | Тип, позволяющий читать запись по имени поля |

## Примечание

- Плейсхолдеры `?` подставляют только **значения**; имена таблиц, полей и ключевые слова (`ORDER BY name`) через параметры передать нельзя — их выбирают из заранее известного списка.
- Даже для одного параметра нужен кортеж: `cur.execute("... WHERE id = ?", (5,))` — запятая обязательна; `(5)` — это просто число, а строка `"5"` будет воспринята как последовательность символов.
- После `INSERT` номер новой записи доступен в `cur.lastrowid`, а количество затронутых строк — в `cur.rowcount`. Начиная с SQLite 3.35 можно писать `INSERT ... RETURNING id`.
- `executemany()` значительно быстрее цикла с `execute()` и выполняется в одной транзакции; для сотен тысяч строк передавайте генератор, а не готовый список.
- `SELECT` без `ORDER BY` не гарантирует порядок строк — если порядок важен, указывайте его явно.
- Документация: https://docs.python.org/3/library/sqlite3.html#sqlite3-placeholders и https://www.sqlite.org/lang_select.html
