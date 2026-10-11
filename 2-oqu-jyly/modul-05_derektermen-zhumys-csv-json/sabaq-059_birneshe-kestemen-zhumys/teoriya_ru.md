# Урок 59. Работа с несколькими таблицами. Связи и оператор JOIN.

## Темы

1. Построение сложной модели данных
2. Идея `JOIN`
3. `INNER JOIN`
4. `LEFT JOIN` — все строки левой таблицы
5. Объединение трёх таблиц (связь M:N)
6. `JOIN` + `GROUP BY` — отчёты
7. Внутренняя логика JOIN (сравнение с Python)
8. Частые ошибки
9. Основные понятия

## 1. Построение сложной модели данных

Полностью реализуем базу данных «Центр кружков»:

```text
teachers 1 ──── N clubs 1 ──── N enrollments N ──── 1 students
```

```python
import sqlite3

conn = sqlite3.connect("center59.db")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    DROP TABLE IF EXISTS enrollments;
    DROP TABLE IF EXISTS clubs;
    DROP TABLE IF EXISTS students;
    DROP TABLE IF EXISTS teachers;

    CREATE TABLE teachers (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        phone TEXT
    );
    CREATE TABLE clubs (
        id INTEGER PRIMARY KEY,
        title TEXT NOT NULL UNIQUE,
        price INTEGER DEFAULT 0,
        teacher_id INTEGER REFERENCES teachers(id)
    );
    CREATE TABLE students (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        class TEXT
    );
    CREATE TABLE enrollments (
        id INTEGER PRIMARY KEY,
        student_id INTEGER NOT NULL REFERENCES students(id) ON DELETE CASCADE,
        club_id INTEGER NOT NULL REFERENCES clubs(id),
        enrolled TEXT,
        UNIQUE (student_id, club_id)
    );
""")

conn.executemany("INSERT INTO teachers (id, name, phone) VALUES (?, ?, ?)", [
    (1, "Ермекова Г.С.", "+77011111111"),
    (2, "Жакыпов Т.Н.", "+77022222222"),
    (3, "Омарова Д.А.", "+77033333333"),     # пока без кружка
])
conn.executemany("INSERT INTO clubs (id, title, price, teacher_id) VALUES (?, ?, ?, ?)", [
    (1, "Python", 0, 1),
    (2, "Веб-дизайн", 0, 1),
    (3, "Робототехника", 5000, 2),
    (4, "3D-моделирование", 3000, None),       # руководитель ещё не назначен
])
conn.executemany("INSERT INTO students (id, name, class) VALUES (?, ?, ?)", [
    (1, "Айгерим", "9А"), (2, "Бауыржан", "9А"), (3, "Даулет", "9Б"),
    (4, "Сымбат", "9Б"), (5, "Ерлан", "10А"),   # Ерлан пока никуда не записан
])
conn.executemany("INSERT INTO enrollments (student_id, club_id, enrolled) VALUES (?, ?, ?)", [
    (1, 1, "2026-09-05"), (1, 3, "2026-09-12"), (2, 1, "2026-09-05"),
    (3, 1, "2026-09-07"), (3, 2, "2026-10-01"), (4, 3, "2026-09-15"),
])
conn.commit()
conn.close()
print("center59.db: 4 таблицы заполнены")
```

Результат:

```text
center59.db: 4 таблицы заполнены
```

Мы специально оставили записи «без пары»: руководитель без кружка (Омарова), кружок без руководителя (3D-моделирование), ученик, никуда не записанный (Ерлан), — они покажут разницу между `INNER` и `LEFT JOIN`.

## 2. Идея `JOIN`

В таблице `clubs` есть только числа `teacher_id`:

| id | title | teacher_id |
|---|---|---|
| 1 | Python | 1 |
| 3 | Робототехника | 2 |

`JOIN` — это объединение строк двух таблиц в одну результирующую таблицу путём **составления пар по условию**:

```text
clubs                                     teachers
id | title            | teacher_id        id | name
 1 | Python           |    1   ───ON───►   1 | Ермекова Г.С.
 2 | Веб-дизайн       |    1   ───ON───►   1 | Ермекова Г.С.
 3 | Робототехника    |    2   ───ON───►   2 | Жакыпов Т.Н.
 4 | 3D-моделирование |  NULL   (без пары)  3 | Омарова Д.А. (без пары)

Результат (INNER JOIN):
title            | name
Python           | Ермекова Г.С.
Веб-дизайн       | Ермекова Г.С.
Робототехника    | Жакыпов Т.Н.
```

**Аналогия:** есть два списка: «Кружки и номер руководителя» и «Номер — имя руководителя». Ты находишь каждый номер из первого списка во втором списке и пишешь рядом имя. `JOIN` делает именно это.

## 3. `INNER JOIN`

```sql
SELECT поля
FROM таблица1
INNER JOIN таблица2 ON таблица1.поле = таблица2.поле;
```

- `ON` — условие, как составлять пары строк (обычно `FK = PK`);
- `INNER JOIN` (или просто `JOIN`) возвращает **только строки, для которых нашлась пара**;
- если есть поля с одинаковыми именами (в обеих таблицах есть `id`, `name`), их записывают **с именем таблицы**: `clubs.title`, `teachers.name`.

```python
import sqlite3

conn = sqlite3.connect("center59.db")
rows = conn.execute("""
    SELECT clubs.title, teachers.name
    FROM clubs
    INNER JOIN teachers ON clubs.teacher_id = teachers.id
""").fetchall()
for title, teacher in rows:
    print(f"{title:<14} — {teacher}")
conn.close()
```

Результат:

```text
Python         — Ермекова Г.С.
Веб-дизайн     — Ермекова Г.С.
Робототехника  — Жакыпов Т.Н.
```

«3D-моделирование» (нет руководителя) и Омарова (нет кружка) **не попали** в результат — `INNER JOIN` показывает только пары.

### Псевдонимы таблиц (alias)

Сокращаем длинные имена: `FROM clubs AS c` или просто `FROM clubs c`.

```python
import sqlite3

conn = sqlite3.connect("center59.db")
rows = conn.execute("""
    SELECT c.title, t.name, t.phone
    FROM clubs c
    JOIN teachers t ON c.teacher_id = t.id
    WHERE c.price = 0
    ORDER BY c.title
""").fetchall()
print("Бесплатные кружки:")
for row in rows:
    print(" ", row)
conn.close()
```

Результат:

```text
Бесплатные кружки:
  ('Python', 'Ермекова Г.С.', '+77011111111')
  ('Веб-дизайн', 'Ермекова Г.С.', '+77011111111')
```

## 4. `LEFT JOIN` — все строки левой таблицы

`LEFT JOIN` возвращает **все** строки **левой** таблицы (стоящей после `FROM`). Если в правой таблице пары не нашлось — её поля будут `NULL`.

```python
import sqlite3

conn = sqlite3.connect("center59.db")
print("INNER JOIN:")
for row in conn.execute("SELECT c.title, t.name FROM clubs c JOIN teachers t ON c.teacher_id = t.id"):
    print(" ", row)
print("LEFT JOIN:")
for row in conn.execute("SELECT c.title, t.name FROM clubs c LEFT JOIN teachers t ON c.teacher_id = t.id"):
    print(" ", row)
conn.close()
```

Результат:

```text
INNER JOIN:
  ('Python', 'Ермекова Г.С.')
  ('Веб-дизайн', 'Ермекова Г.С.')
  ('Робототехника', 'Жакыпов Т.Н.')
LEFT JOIN:
  ('Python', 'Ермекова Г.С.')
  ('Веб-дизайн', 'Ермекова Г.С.')
  ('Робототехника', 'Жакыпов Т.Н.')
  ('3D-моделирование', None)
```

```text
   INNER JOIN               LEFT JOIN
  ┌─────┬─────┐           ┌─────┬─────┐
  │  A  │█████│  B        │█████│█████│  B
  │     │█████│           │█████│█████│
  └─────┴─────┘           └─────┴─────┘
  только общая часть      всё A + общая часть
```

| | `INNER JOIN` | `LEFT JOIN` |
|---|---|---|
| В результате | только строки, у которых есть пара | **все** строки левой таблицы |
| Если пары нет | строка не выводится | поля правой таблицы — `NULL` |
| Когда | «Кружки и их руководители» | «Все кружки, а если есть руководитель — его имя» |

Если поменять порядок таблиц — получим ответ на другой вопрос:

```sql
-- Все руководители и их кружки (руководитель без кружка тоже выводится)
SELECT t.name, c.title FROM teachers t LEFT JOIN clubs c ON c.teacher_id = t.id;
```

В SQLite 3.39+ есть также `RIGHT JOIN` и `FULL OUTER JOIN`, но на практике обычно достаточно `LEFT JOIN` и смены порядка таблиц.

### Поиск записей «без пары»: `LEFT JOIN ... WHERE ... IS NULL`

```python
import sqlite3

conn = sqlite3.connect("center59.db")
idle_teachers = conn.execute("""
    SELECT t.name FROM teachers t
    LEFT JOIN clubs c ON c.teacher_id = t.id
    WHERE c.id IS NULL
""").fetchall()
print("Руководители без кружков:", [r[0] for r in idle_teachers])

lonely = conn.execute("""
    SELECT s.name FROM students s
    LEFT JOIN enrollments e ON e.student_id = s.id
    WHERE e.id IS NULL
""").fetchall()
print("Ученики, никуда не записанные:", [r[0] for r in lonely])
conn.close()
```

Результат:

```text
Руководители без кружков: ['Омарова Д.А.']
Ученики, никуда не записанные: ['Ерлан']
```

Это очень часто используемый приём: «клиенты, не сделавшие заказ», «читатели, не взявшие ни одной книги», «не пришедшие на урок».

## 5. Объединение трёх таблиц (связь M:N)

Чтобы ответить на вопрос «Кто в какой кружок записан?», объединяем **три** таблицы через промежуточную:

```text
students ──(s.id = e.student_id)── enrollments ──(e.club_id = c.id)── clubs
```

```python
import sqlite3

conn = sqlite3.connect("center59.db")
rows = conn.execute("""
    SELECT s.name, s.class, c.title, e.enrolled
    FROM enrollments e
    JOIN students s ON e.student_id = s.id
    JOIN clubs c ON e.club_id = c.id
    ORDER BY s.name, c.title
""").fetchall()
for name, cls, title, date in rows:
    print(f"{name:<9} {cls:<4} {title:<14} {date}")
conn.close()
```

Результат:

```text
Айгерим   9А   Python         2026-09-05
Айгерим   9А   Робототехника  2026-09-12
Бауыржан  9А   Python         2026-09-05
Даулет    9Б   Python         2026-09-07
Даулет    9Б   Веб-дизайн     2026-10-01
Сымбат    9Б   Робототехника  2026-09-15
```

### Четыре таблицы: ученик → кружок → руководитель

```python
import sqlite3

conn = sqlite3.connect("center59.db")
student = "Айгерим"
rows = conn.execute("""
    SELECT c.title, COALESCE(t.name, 'не назначен') AS teacher
    FROM students s
    JOIN enrollments e ON e.student_id = s.id
    JOIN clubs c ON c.id = e.club_id
    LEFT JOIN teachers t ON t.id = c.teacher_id
    WHERE s.name = ?
""", (student,)).fetchall()
print(f"Кружки ученика {student}:", rows)
conn.close()
```

Результат:

```text
Кружки ученика Айгерим: [('Python', 'Ермекова Г.С.'), ('Робототехника', 'Жакыпов Т.Н.')]
```

`COALESCE(a, b)` — если `a` равно `NULL`, возвращает значение `b`. Для кружка без руководителя будет выведена надпись «не назначен».

## 6. `JOIN` + `GROUP BY` — отчёты

```python
import sqlite3

conn = sqlite3.connect("center59.db")

print("Количество учеников в каждом кружке:")
for title, count in conn.execute("""
    SELECT c.title, COUNT(e.id) AS cnt
    FROM clubs c
    LEFT JOIN enrollments e ON e.club_id = c.id
    GROUP BY c.id
    ORDER BY cnt DESC, c.title
"""):
    print(f"  {title:<16} {count}")

print("Нагрузка руководителей:")
for name, clubs, students in conn.execute("""
    SELECT t.name, COUNT(DISTINCT c.id), COUNT(e.id)
    FROM teachers t
    LEFT JOIN clubs c ON c.teacher_id = t.id
    LEFT JOIN enrollments e ON e.club_id = c.id
    GROUP BY t.id
"""):
    print(f"  {name:<14} кружков: {clubs}, учеников: {students}")
conn.close()
```

Результат:

```text
Количество учеников в каждом кружке:
  Python           3
  Робототехника    2
  Веб-дизайн       1
  3D-моделирование 0
Нагрузка руководителей:
  Ермекова Г.С.  кружков: 2, учеников: 4
  Жакыпов Т.Н.   кружков: 1, учеников: 2
  Омарова Д.А.   кружков: 0, учеников: 0
```

`COUNT(e.id)` не считает `NULL`, поэтому для пустого кружка выводится 0. А `COUNT(*)` дал бы пустому кружку 1 (посчитав строку с `NULL`, созданную LEFT JOIN), — это частая ошибка.

## 7. Внутренняя логика JOIN (сравнение с Python)

`INNER JOIN` на деле похож на «вложенный цикл»:

```python
clubs = [(1, "Python", 1), (2, "Веб-дизайн", 1), (3, "Робототехника", 2), (4, "3D-моделирование", None)]
teachers = [(1, "Ермекова Г.С."), (2, "Жакыпов Т.Н."), (3, "Омарова Д.А.")]

# INNER JOIN clubs, teachers ON clubs.teacher_id = teachers.id
inner = [(c[1], t[1]) for c in clubs for t in teachers if c[2] == t[0]]
print(inner)

# LEFT JOIN: если пары нет — None
left = []
for c in clubs:
    matches = [t[1] for t in teachers if c[2] == t[0]]
    left += [(c[1], m) for m in matches] or [(c[1], None)]
print(left)
```

Результат:

```text
[('Python', 'Ермекова Г.С.'), ('Веб-дизайн', 'Ермекова Г.С.'), ('Робототехника', 'Жакыпов Т.Н.')]
[('Python', 'Ермекова Г.С.'), ('Веб-дизайн', 'Ермекова Г.С.'), ('Робототехника', 'Жакыпов Т.Н.'), ('3D-моделирование', None)]
```

СУБД выполняет это гораздо быстрее с помощью индексов.

## Частые ошибки

**1. Условия `ON` нет или оно неверное** → каждая строка соединяется с **каждой** строкой второй таблицы (декартово произведение, `CROSS JOIN`): 4 кружка × 3 руководителя = 12 бессмысленных строк.

```python
import sqlite3

conn = sqlite3.connect("center59.db")
print("Без ON:", conn.execute("SELECT COUNT(*) FROM clubs, teachers").fetchone()[0], "строк")
print("С ON:", conn.execute(
    "SELECT COUNT(*) FROM clubs JOIN teachers ON clubs.teacher_id = teachers.id").fetchone()[0], "строки")
conn.close()
```

Результат:

```text
Без ON: 12 строк
С ON: 3 строки
```

**2. Неоднозначное имя поля.** `SELECT name FROM students JOIN teachers ...` — `name` есть в обеих таблицах → `ambiguous column name: name`. Правильно: `s.name`, `t.name`.

**3. Условие на правую таблицу в `WHERE` после `LEFT JOIN`.** `LEFT JOIN teachers t ... WHERE t.name LIKE 'Е%'` — строки с `NULL` отфильтровываются, и результат превращается в `INNER JOIN`. Условие правой таблицы пиши внутри `ON`: `LEFT JOIN teachers t ON t.id = c.teacher_id AND t.name LIKE 'Е%'`.

**4. `LEFT JOIN` + `COUNT(*)`** → для пустой группы выводится 1. Правильно: `COUNT(правая.id)`.

**5. Путаница полей в условии.** `ON c.id = t.id` — сравнивать номер кружка с номером руководителя бессмысленно. Всегда **FK = PK**: `ON c.teacher_id = t.id`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `JOIN` | Объединение строк двух таблиц по условию |
| `ON` | Условие составления пар строк (обычно FK = PK) |
| `INNER JOIN` | Только строки, для которых нашлась пара |
| `LEFT JOIN` | Все строки левой таблицы; если пары нет — `NULL` |
| Псевдоним (alias) | Короткое имя таблицы: `FROM clubs c` |
| Декартово произведение | Объединение без `ON`: каждая строка с каждой |
| `COALESCE(a, b)` | Возвращает `b`, если `a` пусто |
| Объединение через промежуточную таблицу | Чтение связи M:N с помощью двух `JOIN` |

## Примечание

- Запись `FROM a, b WHERE a.x = b.y` — старый синтаксис соединения, эквивалентный `INNER JOIN`; явный `JOIN ... ON` читается лучше и не даёт случайно забыть условие.
- Условие на правую таблицу при `LEFT JOIN` пишите в `ON`, а не в `WHERE`: `WHERE c.price > 0` отбросит строки с `NULL` и превратит `LEFT JOIN` фактически в `INNER JOIN`.
- Если в двух таблицах есть поля с одинаковыми именами (`id`, `name`), в результате дайте им псевдонимы (`t.name AS teacher`) — иначе при доступе по имени через `sqlite3.Row` вернётся только одно из них.
- `JOIN ... USING (club_id)` — короткая форма `ON a.club_id = b.club_id`, если поля называются одинаково.
- Для быстрых соединений создавайте индексы на внешних ключах: `CREATE INDEX idx_clubs_teacher ON clubs(teacher_id);` — SQLite не создаёт их автоматически.
- Документация: https://www.sqlite.org/lang_select.html#joinclause
