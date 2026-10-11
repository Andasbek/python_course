# Урок 59. Практические задания: несколько таблиц и JOIN

**Подготовка.** Создай и заполни базу данных «Школьная библиотека» (по схеме с урока 54):

```python
import sqlite3

conn = sqlite3.connect("library59.db")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
    DROP TABLE IF EXISTS loans;
    DROP TABLE IF EXISTS readers;
    DROP TABLE IF EXISTS books;
    DROP TABLE IF EXISTS authors;

    CREATE TABLE authors (id INTEGER PRIMARY KEY, name TEXT NOT NULL UNIQUE, born INTEGER);
    CREATE TABLE books (
        id INTEGER PRIMARY KEY,
        title TEXT NOT NULL,
        year INTEGER,
        author_id INTEGER REFERENCES authors(id)
    );
    CREATE TABLE readers (id INTEGER PRIMARY KEY, name TEXT NOT NULL, class TEXT);
    CREATE TABLE loans (
        id INTEGER PRIMARY KEY,
        book_id INTEGER NOT NULL REFERENCES books(id),
        reader_id INTEGER NOT NULL REFERENCES readers(id),
        issued TEXT NOT NULL,
        returned TEXT
    );

    INSERT INTO authors VALUES
        (1, 'Мухтар Ауэзов', 1897), (2, 'Ильяс Есенберлин', 1915),
        (3, 'Бердибек Сокпакбаев', 1924), (4, 'Магжан Жумабаев', 1893),
        (5, 'Оралхан Бокей', 1943);
    INSERT INTO books VALUES
        (1, 'Путь Абая', 1942, 1), (2, 'Серый Лютый', 1929, 1),
        (3, 'Кочевники', 1976, 2), (4, 'Меня зовут Кожа', 1957, 3),
        (5, 'Путешествие в детство', 1960, 3), (6, 'Грех Шолпан', 1923, 4),
        (7, 'Основы Python', 2024, NULL);
    INSERT INTO readers VALUES
        (1, 'Айгерим', '9А'), (2, 'Бауыржан', '9А'), (3, 'Даулет', '9Б'),
        (4, 'Сымбат', '9Б'), (5, 'Ерлан', '10А');
    INSERT INTO loans (book_id, reader_id, issued, returned) VALUES
        (1, 1, '2027-01-05', '2027-01-15'),
        (4, 1, '2027-01-16', NULL),
        (3, 2, '2027-01-10', NULL),
        (4, 3, '2026-12-20', '2027-01-08'),
        (2, 3, '2027-01-12', '2027-01-20'),
        (5, 4, '2027-01-18', NULL),
        (1, 4, '2026-12-01', '2026-12-25');
""")
conn.commit()
conn.close()
print("library59.db готова")
```

## Базовый уровень

### Задание 1. Книги и авторы (INNER JOIN)

Выведи название, год каждой книги и имя её автора. Используй псевдонимы таблиц.

```text
Вывод:
Путь Абая (1942) — Мухтар Ауэзов
Серый Лютый (1929) — Мухтар Ауэзов
Кочевники (1976) — Ильяс Есенберлин
...
```

Сколько книг вывелось? Почему не вывелась книга «Основы Python»?

### Задание 2. Все книги (LEFT JOIN)

Повтори задание 1 с помощью `LEFT JOIN`. Для книги без автора напиши «автор неизвестен» (`COALESCE`).

### Задание 3. Кто что читает?

Список ещё не возвращённых (`returned IS NULL`) книг: имя читателя, класс, название книги, дата выдачи. Объедини три таблицы.

```text
Вывод:
Айгерим (9А) — «Меня зовут Кожа», 2027-01-16
Бауыржан (9А) — «Кочевники», 2027-01-10
Сымбат (9Б) — «Путешествие в детство», 2027-01-18
```

### Задание 4. История читателя

Пользователь вводит имя читателя — программа выводит все взятые им книги по дате и отмечает, возвращена книга или нет.

```text
Ввод:
Читатель: Даулет
Вывод:
2026-12-20 «Меня зовут Кожа» — возвращена 2027-01-08
2027-01-12 «Серый Лютый» — возвращена 2027-01-20
```

## Продвинутый уровень

### Задание 5. Записи без пары

1. Читатели, не взявшие в библиотеке ни одной книги.
2. Книги, которые никто не брал.
3. Авторы, книг которых нет в библиотеке.

### Задание 6. Статистика (JOIN + GROUP BY)

1. Сколько раз выдавалась каждая книга (включая выданные 0 раз), по убыванию.
2. Сколько книг взял каждый читатель и сколько книг у него на руках сейчас.
3. Сколько раз читали книги каждого автора (рейтинг авторов).
4. Количество прочитанных книг по классам.

```text
Вывод (запрос 3):
Мухтар Ауэзов — 3
Бердибек Сокпакбаев — 3
Ильяс Есенберлин — 1
Магжан Жумабаев — 0
Оралхан Бокей — 0
```

### Задание 7. Просроченные книги

Книга выдаётся на 14 дней. Считая «сегодня» = `2027-01-25`, выведи просроченные книги (не возвращённые и прошло больше 14 дней): читатель, книга, на сколько дней опоздание. Подсказка: в SQLite `julianday('2027-01-25') - julianday(issued)` — разница в днях.

## ★ Олимпиадная / творческая задача

### Задание 8. Несколько авторов (M:N)

В реальной жизни у книги может быть несколько авторов. Измени модель: вместо `books.author_id` создай промежуточную таблицу `book_authors(book_id, author_id)`. Перенеси данные (`INSERT INTO book_authors SELECT id, author_id FROM books WHERE author_id IS NOT NULL`), добавь книге «Основы Python» двух авторов (новых), затем выведи каждую книгу со всеми её авторами в одной строке (`GROUP_CONCAT`).

```text
Вывод:
Основы Python — Акылбек Сагатов, Жанар Алиева
Путь Абая — Мухтар Ауэзов
...
```

### Задание 9. «Тебе также может понравиться»

Простейшая версия рекомендательной системы: найди, кто **ещё** читал книги, прочитанные данным читателем, и порекомендуй книги, которые читали эти читатели, но данный читатель ещё не читал. (Подсказка: соединение таблицы `loans` с самой собой — self-join: `FROM loans a JOIN loans b ON a.book_id = b.book_id`.)

## Домашнее задание

1. Создай и заполни свою базу данных, спроектированную на уроке 54 (или таблицы «Центра кружков»), минимум из 3 таблиц. Напиши 5 разных запросов с `JOIN`: INNER, LEFT, три таблицы, «без пары», JOIN + GROUP BY.
2. Подумай: какие запросы JOIN понадобятся в приложении «База учеников» (уроки 62–63)?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–2**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
inner = conn.execute("""
    SELECT b.title, b.year, a.name
    FROM books b JOIN authors a ON b.author_id = a.id
""").fetchall()
for title, year, author in inner:
    print(f"{title} ({year}) — {author}")
print("INNER JOIN:", len(inner), "книг\n")

for title, author in conn.execute("""
    SELECT b.title, COALESCE(a.name, 'автор неизвестен')
    FROM books b LEFT JOIN authors a ON b.author_id = a.id
"""):
    print(f"{title} — {author}")
conn.close()
```

Результат:

```text
Путь Абая (1942) — Мухтар Ауэзов
Серый Лютый (1929) — Мухтар Ауэзов
Кочевники (1976) — Ильяс Есенберлин
Меня зовут Кожа (1957) — Бердибек Сокпакбаев
Путешествие в детство (1960) — Бердибек Сокпакбаев
Грех Шолпан (1923) — Магжан Жумабаев
INNER JOIN: 6 книг

Путь Абая — Мухтар Ауэзов
Серый Лютый — Мухтар Ауэзов
Кочевники — Ильяс Есенберлин
Меня зовут Кожа — Бердибек Сокпакбаев
Путешествие в детство — Бердибек Сокпакбаев
Грех Шолпан — Магжан Жумабаев
Основы Python — автор неизвестен
```

«Основы Python» не вывелась в INNER JOIN, потому что у неё `author_id = NULL` — пары нет.

**Задание 3**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
for name, cls, title, issued in conn.execute("""
    SELECT r.name, r.class, b.title, l.issued
    FROM loans l
    JOIN readers r ON l.reader_id = r.id
    JOIN books b ON l.book_id = b.id
    WHERE l.returned IS NULL
    ORDER BY r.name
"""):
    print(f"{name} ({cls}) — «{title}», {issued}")
conn.close()
```

Результат:

```text
Айгерим (9А) — «Меня зовут Кожа», 2027-01-16
Бауыржан (9А) — «Кочевники», 2027-01-10
Сымбат (9Б) — «Путешествие в детство», 2027-01-18
```

**Задание 4**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
reader = input("Читатель: ").strip()
rows = conn.execute("""
    SELECT l.issued, b.title, l.returned
    FROM loans l
    JOIN readers r ON l.reader_id = r.id
    JOIN books b ON l.book_id = b.id
    WHERE r.name = ?
    ORDER BY l.issued
""", (reader,)).fetchall()
if not rows:
    print("Этот читатель не брал книг или такого читателя нет")
for issued, title, returned in rows:
    status = f"возвращена {returned}" if returned else "ещё читает"
    print(f"{issued} «{title}» — {status}")
conn.close()
```

**Задание 5**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
q1 = conn.execute("""
    SELECT r.name FROM readers r LEFT JOIN loans l ON l.reader_id = r.id WHERE l.id IS NULL
""").fetchall()
q2 = conn.execute("""
    SELECT b.title FROM books b LEFT JOIN loans l ON l.book_id = b.id WHERE l.id IS NULL
""").fetchall()
q3 = conn.execute("""
    SELECT a.name FROM authors a LEFT JOIN books b ON b.author_id = a.id WHERE b.id IS NULL
""").fetchall()
print("Не брали книг:", [r[0] for r in q1])
print("Книги, которые никто не брал:", [r[0] for r in q2])
print("Авторы без книг:", [r[0] for r in q3])
conn.close()
```

Результат:

```text
Не брали книг: ['Ерлан']
Книги, которые никто не брал: ['Грех Шолпан', 'Основы Python']
Авторы без книг: ['Оралхан Бокей']
```

**Задание 6**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
print("1) Книги:")
for title, n in conn.execute("""
    SELECT b.title, COUNT(l.id) AS n FROM books b
    LEFT JOIN loans l ON l.book_id = b.id
    GROUP BY b.id ORDER BY n DESC, b.id
"""):
    print(f"   {title} — {n}")

print("2) Читатели:")
for name, total, now in conn.execute("""
    SELECT r.name, COUNT(l.id), SUM(CASE WHEN l.id IS NOT NULL AND l.returned IS NULL THEN 1 ELSE 0 END)
    FROM readers r LEFT JOIN loans l ON l.reader_id = r.id
    GROUP BY r.id
"""):
    print(f"   {name}: всего {total}, на руках {now}")

print("3) Авторы:")
for name, n in conn.execute("""
    SELECT a.name, COUNT(l.id) AS n FROM authors a
    LEFT JOIN books b ON b.author_id = a.id
    LEFT JOIN loans l ON l.book_id = b.id
    GROUP BY a.id ORDER BY n DESC, a.id
"""):
    print(f"   {name} — {n}")

print("4) Классы:")
for cls, n in conn.execute("""
    SELECT r.class, COUNT(l.id) FROM readers r
    LEFT JOIN loans l ON l.reader_id = r.id
    GROUP BY r.class ORDER BY r.class
"""):
    print(f"   {cls} — {n}")
conn.close()
```

Результат:

```text
1) Книги:
   Путь Абая — 2
   Меня зовут Кожа — 2
   Серый Лютый — 1
   Кочевники — 1
   Путешествие в детство — 1
   Грех Шолпан — 0
   Основы Python — 0
2) Читатели:
   Айгерим: всего 2, на руках 1
   Бауыржан: всего 1, на руках 1
   Даулет: всего 2, на руках 0
   Сымбат: всего 2, на руках 1
   Ерлан: всего 0, на руках 0
3) Авторы:
   Мухтар Ауэзов — 3
   Бердибек Сокпакбаев — 3
   Ильяс Есенберлин — 1
   Магжан Жумабаев — 0
   Оралхан Бокей — 0
4) Классы:
   10А — 0
   9А — 3
   9Б — 4
```

`CASE WHEN ... THEN ... ELSE ... END` — это «если» в SQL: удобно для подсчёта строк, удовлетворяющих условию.

**Задание 7**

```python
import sqlite3

TODAY = "2027-01-25"
conn = sqlite3.connect("library59.db")
rows = conn.execute("""
    SELECT r.name, b.title, CAST(julianday(?) - julianday(l.issued) AS INTEGER) - 14 AS late
    FROM loans l
    JOIN readers r ON r.id = l.reader_id
    JOIN books b ON b.id = l.book_id
    WHERE l.returned IS NULL AND julianday(?) - julianday(l.issued) > 14
    ORDER BY late DESC
""", (TODAY, TODAY)).fetchall()
for name, title, late in rows:
    print(f"{name} — «{title}»: опоздание {late} дн.")
conn.close()
```

Результат:

```text
Бауыржан — «Кочевники»: опоздание 1 дн.
```

(Айгерим взяла книгу 9 дней назад, Сымбат — 7 дней назад: срок ещё не истёк.)

**Задание 8**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
conn.executescript("""
    DROP TABLE IF EXISTS book_authors;
    CREATE TABLE book_authors (
        book_id INTEGER NOT NULL REFERENCES books(id),
        author_id INTEGER NOT NULL REFERENCES authors(id),
        PRIMARY KEY (book_id, author_id)
    );
    INSERT INTO book_authors SELECT id, author_id FROM books WHERE author_id IS NOT NULL;
    INSERT OR IGNORE INTO authors (id, name, born) VALUES (6, 'Акылбек Сагатов', 1990), (7, 'Жанар Алиева', 1992);
    INSERT OR IGNORE INTO book_authors VALUES (7, 6), (7, 7);
""")
conn.commit()
for title, authors in conn.execute("""
    SELECT b.title, GROUP_CONCAT(a.name, ', ')
    FROM books b
    JOIN book_authors ba ON ba.book_id = b.id
    JOIN authors a ON a.id = ba.author_id
    GROUP BY b.id
    ORDER BY COUNT(*) DESC, b.id
"""):
    print(f"{title} — {authors}")
conn.close()
```

Результат:

```text
Основы Python — Акылбек Сагатов, Жанар Алиева
Путь Абая — Мухтар Ауэзов
Серый Лютый — Мухтар Ауэзов
Кочевники — Ильяс Есенберлин
Меня зовут Кожа — Бердибек Сокпакбаев
Путешествие в детство — Бердибек Сокпакбаев
Грех Шолпан — Магжан Жумабаев
```

Примечание: для полного перехода нужно удалить столбец `books.author_id` (SQLite 3.35+: `ALTER TABLE books DROP COLUMN author_id`, но для удаления столбца FK может понадобиться пересоздать таблицу). Порядок имён внутри `GROUP_CONCAT` не гарантирован.

**Задание 9**

```python
import sqlite3

conn = sqlite3.connect("library59.db")
reader = "Айгерим"
rows = conn.execute("""
    SELECT DISTINCT b.title
    FROM readers me
    JOIN loans a ON a.reader_id = me.id                -- книги, которые прочитал я
    JOIN loans other ON other.book_id = a.book_id       -- те, кто читал эту же книгу
        AND other.reader_id <> me.id
    JOIN loans rec ON rec.reader_id = other.reader_id   -- другие книги, которые они читали
    JOIN books b ON b.id = rec.book_id
    WHERE me.name = ?
      AND rec.book_id NOT IN (SELECT book_id FROM loans WHERE reader_id = me.id)
    ORDER BY b.title
""", (reader,)).fetchall()
print(f"{reader}, тебе также может понравиться:", [r[0] for r in rows])
conn.close()
```

Результат:

```text
Айгерим, тебе также может понравиться: ['Путешествие в детство', 'Серый Лютый']
```

Объяснение: Айгерим прочитала книги «Путь Абая» и «Меня зовут Кожа». «Путь Абая» читала Сымбат (она брала и «Путешествие в детство»), «Меня зовут Кожа» читал Даулет (он брал и «Серый Лютый»). Внутренний `SELECT` (подзапрос) исключает книги, которые Айгерим уже читала.

</details>
