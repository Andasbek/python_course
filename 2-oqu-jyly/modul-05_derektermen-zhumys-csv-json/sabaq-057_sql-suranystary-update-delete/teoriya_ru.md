# Урок 57. SQL-запросы: UPDATE, DELETE. Условие WHERE.

## Темы

1. Рабочая база
2. Условие `WHERE` подробно
3. `UPDATE` — изменение записей
4. `DELETE` — удаление записей
5. Правила безопасного изменения
6. Удаление из связанных таблиц
7. Частые ошибки
8. Основные понятия

## 1. Рабочая база

```python
import sqlite3

STUDENTS = [
    ("Айгерим", "Нурланкызы", "9А", "Алматы", 95, "+77011112233"),
    ("Бауыржан", "Сеитов", "9А", "Конаев", 78, None),
    ("Даулет", "Оспанов", "9Б", "Алматы", 88, "+77025556677"),
    ("Сымбат", "Алиева", "9Б", "Алматы", 100, "+77071234567"),
    ("Ерлан", "Жакыпов", "9А", "Талгар", 64, None),
    ("Мадина", "Касымова", "9Б", "Алматы", 91, "+77479876543"),
    ("Темирлан", "Ахметов", "10А", "Конаев", 73, "+77051112244"),
    ("Аружан", "Серикова", "10А", "Алматы", 85, None),
]

conn = sqlite3.connect("school57.db")
conn.executescript("""
    DROP TABLE IF EXISTS students;
    CREATE TABLE students (
        id INTEGER PRIMARY KEY,
        first_name TEXT NOT NULL,
        last_name TEXT NOT NULL,
        class TEXT,
        city TEXT,
        score INTEGER CHECK (score BETWEEN 0 AND 100),
        phone TEXT
    );
""")
conn.executemany(
    "INSERT INTO students (first_name, last_name, class, city, score, phone) VALUES (?, ?, ?, ?, ?, ?)",
    STUDENTS,
)
conn.commit()
conn.close()
print("school57.db: 8 учеников")
```

Результат:

```text
school57.db: 8 учеников
```

## 2. Условие `WHERE` подробно

`WHERE` в командах `SELECT`, `UPDATE` и `DELETE` определяет, **с какими записями** мы работаем. Оно похоже на `if` в Python: условие проверяется для каждой записи.

### Операторы сравнения

| Оператор | Значение | Пример |
|---|---|---|
| `=` | равно | `class = '9А'` |
| `<>` или `!=` | не равно | `city <> 'Алматы'` |
| `<`, `>`, `<=`, `>=` | меньше, больше | `score >= 90` |

### Логические операторы: `AND`, `OR`, `NOT`

```python
import sqlite3

conn = sqlite3.connect("school57.db")


def show(title, sql, params=()):
    names = [r[0] for r in conn.execute(sql, params)]
    print(f"{title}: {', '.join(names) if names else '—'}")


show("9А и больше 80", "SELECT first_name FROM students WHERE class = ? AND score > ?", ("9А", 80))
show("Талгар или Конаев", "SELECT first_name FROM students WHERE city = ? OR city = ?", ("Талгар", "Конаев"))
show("Не из Алматы", "SELECT first_name FROM students WHERE NOT city = ?", ("Алматы",))
show("9Б, (Алматы или 95+)",
     "SELECT first_name FROM students WHERE class = ? AND (city = ? OR score >= ?)", ("9Б", "Алматы", 95))
conn.close()
```

Результат:

```text
9А и больше 80: Айгерим
Талгар или Конаев: Бауыржан, Ерлан, Темирлан
Не из Алматы: Бауыржан, Ерлан, Темирлан
9Б, (Алматы или 95+): Даулет, Сымбат, Мадина
```

`AND` выполняется раньше `OR` (как в Python). Если сомневаешься — ставь **скобки**.

### `BETWEEN` — диапазон

```sql
SELECT first_name, score FROM students WHERE score BETWEEN 80 AND 95;   -- 80 и 95 тоже входят
```

### `IN` — одно из значений списка

```sql
SELECT first_name FROM students WHERE city IN ('Талгар', 'Конаев');
-- то же самое, что city = 'Талгар' OR city = 'Конаев'
```

### `LIKE` — поиск по шаблону

| Символ шаблона | Значение | Пример | Подходит |
|---|---|---|---|
| `%` | любое количество символов (и 0 тоже) | `'А%'` | Айгерим, Аружан |
| `_` | ровно один символ | `'_р%'` | Ерлан, Аружан |
| | | `'%ов'` | Сеитов, Оспанов, Жакыпов, Ахметов |
| | | `'%+7701%'` | в телефоне есть +7701 |

### `IS NULL` — проверка пустого значения

`NULL` — это «значения нет / неизвестно». Проверять его через `= NULL` **нельзя** (результат всегда «неизвестно»). Только `IS NULL` или `IS NOT NULL`.

```python
import sqlite3

conn = sqlite3.connect("school57.db")
q = {
    "BETWEEN 80 AND 95": ("SELECT first_name FROM students WHERE score BETWEEN ? AND ?", (80, 95)),
    "IN (Талгар, Конаев)": ("SELECT first_name FROM students WHERE city IN (?, ?)", ("Талгар", "Конаев")),
    "LIKE 'А%'": ("SELECT first_name FROM students WHERE first_name LIKE ?", ("А%",)),
    "LIKE '%ов'": ("SELECT last_name FROM students WHERE last_name LIKE ?", ("%ов",)),
    "phone IS NULL": ("SELECT first_name FROM students WHERE phone IS NULL", ()),
    "phone = NULL (ошибка!)": ("SELECT first_name FROM students WHERE phone = NULL", ()),
}
for title, (sql, params) in q.items():
    print(f"{title:<23}→ {[r[0] for r in conn.execute(sql, params)]}")
conn.close()
```

Результат:

```text
BETWEEN 80 AND 95      → ['Айгерим', 'Даулет', 'Мадина', 'Аружан']
IN (Талгар, Конаев)    → ['Бауыржан', 'Ерлан', 'Темирлан']
LIKE 'А%'              → ['Айгерим', 'Аружан']
LIKE '%ов'             → ['Сеитов', 'Оспанов', 'Жакыпов', 'Ахметов']
phone IS NULL          → ['Бауыржан', 'Ерлан', 'Аружан']
phone = NULL (ошибка!) → []
```

> **Кириллица и `LIKE`:** в SQLite `LIKE` для латинских букв нечувствителен к регистру (`'a%'` = `'A%'`), но **для кириллицы чувствителен**: `'а%'` не найдёт «Айгерим». Решение: выполнять поиск в Python через `str.lower()` или правильно писать первую букву слова, введённого пользователем.

### `LIKE` с текстом, введённым пользователем

Символы `%` добавляем в **параметр**, а не в строку SQL:

```python
import sqlite3

conn = sqlite3.connect("school57.db")
part = "ан"                                   # ввёл пользователь
rows = conn.execute("SELECT first_name FROM students WHERE first_name LIKE ?", (f"%{part}%",)).fetchall()
print(f"Имена, содержащие «{part}»:", [r[0] for r in rows])
conn.close()
```

Результат:

```text
Имена, содержащие «ан»: ['Бауыржан', 'Ерлан', 'Темирлан', 'Аружан']
```

## 3. `UPDATE` — изменение записей

Синтаксис:

```sql
UPDATE таблица
SET поле1 = значение1, поле2 = значение2
WHERE условие;
```

```python
import sqlite3

conn = sqlite3.connect("school57.db")

# 1) Изменение одной записи по id
cur = conn.execute("UPDATE students SET class = ? WHERE id = ?", ("9Б", 2))
print("Бауыржан переведён, изменено записей:", cur.rowcount)

# 2) Несколько полей сразу
cur = conn.execute("UPDATE students SET city = ?, phone = ? WHERE id = ?", ("Алматы", "+77770001122", 5))
print("Адрес и телефон Ерлана:", cur.rowcount)

# 3) Вычисление: классу 10А +5 бонусных баллов (но не больше 100)
cur = conn.execute("UPDATE students SET score = MIN(score + 5, 100) WHERE class = ?", ("10А",))
print("Бонус получили в 10А:", cur.rowcount, "ученика")

conn.commit()
for row in conn.execute("SELECT first_name, class, city, score FROM students WHERE id IN (2, 5, 7, 8)"):
    print(row)
conn.close()
```

Результат:

```text
Бауыржан переведён, изменено записей: 1
Адрес и телефон Ерлана: 1
Бонус получили в 10А: 2 ученика
('Бауыржан', '9Б', 'Конаев', 78)
('Ерлан', '9А', 'Алматы', 64)
('Темирлан', '10А', 'Конаев', 78)
('Аружан', '10А', 'Алматы', 90)
```

- `cur.rowcount` показывает, **на сколько записей** повлияла команда. Если `0` — не найдено записей, удовлетворяющих условию (например, неверный `id`).
- `SET score = score + 5` — новое значение вычисляется из старого.
- `MIN(a, b)` — в SQLite меньшее из двух значений.

### `UPDATE` без `WHERE` — катастрофа!

```sql
UPDATE students SET score = 0;   -- у ВСЕХ учеников балл стал 0!
```

Если `WHERE` не указано, команда применяется ко **всем** записям таблицы. SQL не спрашивает «Ты уверен?».

## 4. `DELETE` — удаление записей

Синтаксис:

```sql
DELETE FROM таблица WHERE условие;
```

```python
import sqlite3

conn = sqlite3.connect("school57.db")

cur = conn.execute("DELETE FROM students WHERE id = ?", (8,))
print("Удалено:", cur.rowcount)

cur = conn.execute("DELETE FROM students WHERE score < ? AND phone IS NULL", (70,))
print("Удалено по условию:", cur.rowcount)

cur = conn.execute("DELETE FROM students WHERE id = ?", (999,))
print("id=999:", "не найден" if cur.rowcount == 0 else "удалён")

conn.commit()
print("Осталось:", conn.execute("SELECT COUNT(*) FROM students").fetchone()[0])
conn.close()
```

Результат:

```text
Удалено: 1
Удалено по условию: 0
id=999: не найден
Осталось: 7
```

(Ерлан раньше получил телефон, поэтому второму условию никто не удовлетворил.)

`DELETE FROM students;` — без `WHERE` — удаляет **все записи таблицы** (сама таблица остаётся). `DROP TABLE` — удаляет таблицу полностью.

| Команда | Результат |
|---|---|
| `DELETE FROM t WHERE id = 5` | удаляется одна запись |
| `DELETE FROM t` | удаляются все записи, таблица остаётся пустой |
| `DROP TABLE t` | таблица удаляется вместе со структурой |

## 5. Правила безопасного изменения

1. **Сначала `SELECT`, потом `UPDATE`/`DELETE`.** Выполни `SELECT` с тем же условием `WHERE` и посмотри, какие записи изменятся.
2. **По возможности изменяй по `id`** — он уникален.
3. **Проверяй `rowcount`** — совпадает ли он с ожидаемым числом?
4. **Транзакция:** если результат не тот, что ожидался, — `rollback()`.
5. **Спрашивай подтверждение у пользователя:** «Будет удалено 3 записи. Продолжить? (да/нет)».
6. **Резервная копия** (backup) — перед важными изменениями (копия файла `.db` или метод `conn.backup()`).

```python
import sqlite3

conn = sqlite3.connect("school57.db")
city = "Конаев"

# 1. Сначала смотрим, что изменится
victims = conn.execute("SELECT id, first_name FROM students WHERE city = ?", (city,)).fetchall()
print("Будут удалены:", victims)

# 2. Удаление, но пока без commit
cur = conn.execute("DELETE FROM students WHERE city = ?", (city,))

# 3. Проверка: совпадает ли количество?
if cur.rowcount != len(victims) or cur.rowcount > 2:
    conn.rollback()        # отмена — ничего не изменится
    print("Слишком много записей! Отменено (rollback)")
else:
    conn.commit()
    print("Удалено:", cur.rowcount)

print("Осталось:", conn.execute("SELECT COUNT(*) FROM students").fetchone()[0])
conn.close()
```

Результат:

```text
Будут удалены: [(2, 'Бауыржан'), (7, 'Темирлан')]
Удалено: 2
Осталось: 5
```

## 6. Удаление из связанных таблиц

Если удалить ученика, записанного в кружок, то записи в таблице `enrollments`, ссылающиеся на него, останутся «сиротами». SQLite (если `PRAGMA foreign_keys = ON`) запрещает это или выполняет выбранное нами действие:

| Правило | Значение |
|---|---|
| (по умолчанию) / `ON DELETE RESTRICT` | если есть ссылающиеся записи — удалять **нельзя** |
| `ON DELETE CASCADE` | вместе с родительской записью ссылающиеся записи тоже **удаляются автоматически** |
| `ON DELETE SET NULL` | поле FK в ссылающихся записях становится `NULL` |

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    CREATE TABLE kids (id INTEGER PRIMARY KEY, name TEXT);
    CREATE TABLE clubs (id INTEGER PRIMARY KEY, title TEXT);
    CREATE TABLE enrollments (
        kid_id INTEGER REFERENCES kids(id) ON DELETE CASCADE,
        club_id INTEGER REFERENCES clubs(id)
    );
    INSERT INTO kids VALUES (1, 'Алихан'), (2, 'Томирис');
    INSERT INTO clubs VALUES (1, 'Python'), (2, 'Шахматы');
    INSERT INTO enrollments VALUES (1, 1), (1, 2), (2, 1);
""")

conn.execute("DELETE FROM kids WHERE id = ?", (1,))          # CASCADE
print("Записи Алихана тоже удалены:", conn.execute("SELECT * FROM enrollments").fetchall())

try:
    conn.execute("DELETE FROM clubs WHERE id = ?", (1,))     # по умолчанию: нельзя
except sqlite3.IntegrityError as e:
    print("Кружок Python не удалён:", e)
conn.close()
```

Результат:

```text
Записи Алихана тоже удалены: [(2, 1)]
Кружок Python не удалён: FOREIGN KEY constraint failed
```

### «Мягкое удаление» (soft delete)

Во многих системах данные на самом деле не удаляются — их только помечают как «неактивные». Например, даже если ученик ушёл из кружка, его прежние оценки и сертификаты должны сохраниться.

```sql
ALTER TABLE students ADD COLUMN active INTEGER DEFAULT 1;
UPDATE students SET active = 0 WHERE id = 5;          -- «удаление»
SELECT first_name FROM students WHERE active = 1;      -- только активные
UPDATE students SET active = 1 WHERE id = 5;          -- восстановить легко!
```

## Частые ошибки

**1. `UPDATE`/`DELETE` без `WHERE`** → изменяется вся таблица. Всегда проверяй, есть ли `WHERE`!

**2. `= NULL`** → никогда не бывает истинным. Правильно: `IS NULL`.

**3. `AND`/`OR` без скобок.**

```sql
-- Ошибка: «алматинцы из 9Б» + «95+ из ВСЕХ классов»
SELECT * FROM students WHERE class = '9Б' AND city = 'Алматы' OR score >= 95;
-- Правильно: в классе 9Б (алматинцы или 95+)
SELECT * FROM students WHERE class = '9Б' AND (city = 'Алматы' OR score >= 95);
```

**4. Забыт `commit()`** → UPDATE/DELETE отменяются при закрытии программы (иногда это полезно, но чаще — ошибка).

**5. `%` для `LIKE` в строке SQL, а значение через f-строку:** `f"... LIKE '%{text}%'"` → SQL-инъекция. Правильно: `LIKE ?` и `(f"%{text}%",)`.

**6. `UPDATE ... SET a = 1 AND b = 2`** — ошибка! Не `AND`, а **запятая**: `SET a = 1, b = 2`. (Иначе SQLite запишет в `a` результат логического выражения `1 AND b = 2`.)

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `WHERE` | Условие, фильтрующее записи |
| `AND`, `OR`, `NOT` | Логические операторы, объединяющие условия |
| `BETWEEN a AND b` | Значение в диапазоне от a до b (оба включены) |
| `IN (...)` | Значение — одно из значений списка |
| `LIKE` | Сравнение по шаблону (`%` — любые символы, `_` — один символ) |
| `IS NULL` | Проверка, что значение пустое |
| `UPDATE ... SET` | Изменение значений в записях |
| `DELETE FROM` | Удаление записей |
| `rowcount` | Количество записей, затронутых командой |
| `rollback()` | Отмена незавершённой транзакции |
| `ON DELETE CASCADE` | Удаление зависимых записей вместе с родительской |
| Мягкое удаление | Пометка записи как неактивной без её удаления |

## Примечание

- `cur.rowcount` после `UPDATE`/`DELETE` показывает число реально затронутых строк — удобно проверять, нашлась ли запись (`0` — ничего не изменено).
- Сравнение `= NULL` всегда даёт «неизвестно», поэтому `WHERE phone = NULL` не найдёт ни одной строки; правильно `WHERE phone IS NULL`.
- `LIKE` в SQLite нечувствителен к регистру только для латиницы: `'алматы' LIKE 'Алматы'` даёт ложь. Для кириллицы приводят строки к одному регистру в Python или хранят отдельное поле для поиска.
- В пользовательском тексте для `LIKE` символы `%` и `_` нужно экранировать (`ESCAPE '\'`), иначе они станут шаблоном.
- `ON DELETE CASCADE` работает только при `PRAGMA foreign_keys = ON`; без неё зависимые записи останутся «сиротами».
- Копию базы «на лету» делает `src.backup(dst)` (`sqlite3.Connection.backup`). Документация: https://docs.python.org/3/library/sqlite3.html и https://www.sqlite.org/lang_update.html
