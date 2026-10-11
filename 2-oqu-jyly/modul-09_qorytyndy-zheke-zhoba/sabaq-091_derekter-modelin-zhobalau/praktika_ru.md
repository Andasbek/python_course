# Урок 91. Практические задания: проектирование модели данных

## Базовый уровень

### Задание 1. Поиск сущностей
Прочитайте фрагмент ТЗ и запишите в виде таблицы **сущности**, их **атрибуты** и **связи**.

> Приложение «Школьная библиотека». Библиотекарь регистрирует **книги**: название, автор, год издания, количество экземпляров. У каждой книги может быть один или несколько **авторов**. **Ученик** (ФИО, класс) **берёт** и **возвращает** книгу; записываются дата выдачи и дата возврата.

Образец результата:

| Сущность | Атрибуты | Связи |
|---|---|---|
| ... | ... | ... |

*Подсказка:* «берёт/возвращает» может быть отдельной сущностью (`loan` — запись о выдаче), так как у неё есть собственные атрибуты (даты).

### Задание 2. Определение типа связи
Для каждой пары запишите тип связи (1:1, 1:N, N:M) и укажите, где будет внешний ключ:
1. Класс — Ученик
2. Ученик — Предмет
3. Страна — Столица
4. Игрок — Результат игры (рекорд)
5. Фильм — Актёр
6. Викторина — Вопрос

### Задание 3. Исправление «плохой» таблицы
Какие ошибки в следующей таблице? Приведите её к 3НФ и запишите список новых таблиц.

| order_id | customer | phone | products | total | city | city_region |
|---|---|---|---|---|---|---|
| 1 | Дана | 87011112233 | Тетрадь, Ручка | 850 | Алматы | Алматинская область... |
| 2 | Дана | 87011112233 | Ручка | 150 | Алматы | ... |

### Задание 4. Написание CREATE TABLE
Напишите программу на Python, которая создаёт таблицы SQLite для связи «Викторина — Вопрос» из задания 2. В вопросе должны быть: текст, 4 варианта ответа, номер правильного ответа (1–4). Используйте ограничение `CHECK`. Программа должна вывести список таблиц.

Пример вывода:
```text
Таблицы: ['quiz', 'question']
```

## Продвинутый уровень

### Задание 5. Связь N:M + запрос
Создайте таблицы `student`, `club`, `enrollment` и заполните их 3 учениками и 3 кружками. Выполните следующие запросы:
- ученики, которые ходят в кружок «Робототехника»;
- сколько кружков посещает каждый ученик (`GROUP BY`);
- проверьте, что произойдёт при попытке дважды записать одного ученика в один кружок (`IntegrityError`).

### Задание 6. Проверка внешнего ключа
Напишите программу, которая при включённом `PRAGMA foreign_keys = ON` пытается добавить операцию в несуществующую категорию. Перехватите ошибку с помощью `try/except sqlite3.IntegrityError` и выведите сообщение на русском языке.

Пример вывода:
```text
Ошибка: такой категории нет (FOREIGN KEY constraint failed)
```

### Задание 7. Модель данных вашего проекта (обязательно)
Для своего итогового проекта:
1. Выпишите из ТЗ список сущностей (не менее 2–3 сущностей).
2. Нарисуйте ER-диаграмму на бумаге или в draw.io, сохраните изображение как `docs/er.png`.
3. Заполните словарь данных для каждой таблицы (шаблон ниже).
4. Напишите схему в `schema.sql` или `database.py` и создайте пустую базу данных.
5. Напишите SQL-запросы к 3 основным вопросам из ТЗ и проверьте, что модели достаточно.

Шаблон словаря данных:

| Поле | Тип | Ограничение | Описание |
|---|---|---|---|
| id | INTEGER | PK | ... |

> Если ваш проект — игра или API-приложение и не использует SQLite, опишите по этому шаблону структуру JSON (ключи, типы, пример).

## ★ Олимпиадная / творческая задача

### Задание 8. Автоматическое описание схемы
Напишите скрипт `describe_db.py`, который открывает любой файл `.db` и выводит все его таблицы и поля в виде словаря данных (таблица markdown). Используйте `PRAGMA table_info(таблица)` и `PRAGMA foreign_key_list(таблица)`.

Пример вывода:
```text
### operation
| Поле | Тип | NOT NULL | PK | FK |
|---|---|---|---|---|
| id | INTEGER | 0 | 1 |  |
| category_id | INTEGER | 1 | 0 | category.id |
```

### Задание 9. Рекурсивная связь
Проект «Семейное древо»: отец и мать каждого человека тоже находятся в той же таблице `person`. Спроектируйте таблицу и напишите SQL-запрос, который находит деда (отца отца) заданного человека.

## Домашнее задание
1. Завершите ER-диаграмму и словарь данных своего проекта и запишите их в файл `docs/data_model.md`.
2. Подготовьте 3 SQL-запроса (или файл с примером JSON) для проверки схемы.
3. К следующему уроку: продумайте, каким будет главный экран вашего проекта, и нарисуйте эскиз от руки на бумаге.

## Критерии оценивания
Модель данных входит в часть «техническое исполнение» итогового проекта. Работа на уроке оценивается по критериям практической работы:

| Критерий | Доля | Что проверяется |
|---|---|---|
| Правильность модели и соответствие ТЗ | 40% | есть все сущности, связи правильные, модель отвечает на вопросы ТЗ |
| Рациональность решения | 25% | 3НФ, нет повторов, правильные типы данных и ограничения |
| Читаемость документации | 20% | словарь данных, ER-диаграмма понятна, названия понятные |
| Самостоятельность и своевременность выполнения | 15% | схема создана до конца урока |

## Для педагога: решения
<details><summary>Показать решения</summary>

**Задание 1.**

| Сущность | Атрибуты | Связи |
|---|---|---|
| book | id, title, year, copies | N:M author (промежуточная `book_author`) |
| author | id, full_name | N:M book |
| student | id, first_name, last_name, grade | 1:N loan |
| loan | id, book_id, student_id, taken_date, returned_date (может быть NULL) | N:1 book, N:1 student |

**Задание 2.** 1) 1:N, FK `student.class_id`; 2) N:M, промежуточная `student_subject`; 3) 1:1, FK `country.capital_id UNIQUE` или в одной таблице; 4) 1:N, FK `score.player_id`; 5) N:M, промежуточная `film_actor`; 6) 1:N, FK `question.quiz_id`.

**Задание 3.** Ошибки: `products` неатомарно (нарушена 1НФ); `customer, phone` повторяются; `city_region` зависит от города (нарушена 3НФ); `total` — вычисляемое значение. Таблицы: `customer(id, name, phone)`, `city(id, name, region)`, `product(id, title, price)`, `orders(id, customer_id, city_id, order_date)`, `order_item(order_id, product_id, quantity, PK(order_id, product_id))`. Общая сумма вычисляется через `SUM(price*quantity)`.

**Задание 4.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
CREATE TABLE quiz (
    id    INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL UNIQUE
);
CREATE TABLE question (
    id       INTEGER PRIMARY KEY AUTOINCREMENT,
    quiz_id  INTEGER NOT NULL REFERENCES quiz(id) ON DELETE CASCADE,
    text     TEXT NOT NULL,
    option1  TEXT NOT NULL,
    option2  TEXT NOT NULL,
    option3  TEXT NOT NULL,
    option4  TEXT NOT NULL,
    correct  INTEGER NOT NULL CHECK (correct BETWEEN 1 AND 4)
);
""")
names = conn.execute(
    "SELECT name FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%'"
).fetchall()
print("Таблицы:", [n[0] for n in names])
```
(Вынести варианты в отдельную таблицу `option(question_id, text, is_correct)` тоже правильно — тогда количество вариантов может быть любым.)

**Задание 5.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT NOT NULL);
CREATE TABLE club (id INTEGER PRIMARY KEY, title TEXT NOT NULL UNIQUE);
CREATE TABLE enrollment (
    student_id INTEGER REFERENCES student(id),
    club_id    INTEGER REFERENCES club(id),
    PRIMARY KEY (student_id, club_id)
);
INSERT INTO student VALUES (1, 'Әлихан'), (2, 'Мадина'), (3, 'Тимур');
INSERT INTO club VALUES (1, 'Python'), (2, 'Робототехника'), (3, 'Шахматы');
INSERT INTO enrollment VALUES (1, 1), (1, 2), (2, 2), (3, 1), (3, 3), (2, 1);
""")

rows = conn.execute("""
    SELECT s.name FROM student s
    JOIN enrollment e ON e.student_id = s.id
    JOIN club c ON c.id = e.club_id
    WHERE c.title = 'Робототехника'
""").fetchall()
print("Робототехника:", [r[0] for r in rows])

for name, cnt in conn.execute("""
    SELECT s.name, COUNT(e.club_id) FROM student s
    LEFT JOIN enrollment e ON e.student_id = s.id
    GROUP BY s.id ORDER BY s.name
"""):
    print(f"{name}: кружков — {cnt}")

try:
    conn.execute("INSERT INTO enrollment VALUES (1, 1)")
except sqlite3.IntegrityError as err:
    print("Повторная запись невозможна:", err)
```
Результат:
```text
Робототехника: ['Әлихан', 'Мадина']
Мадина: кружков — 2
Тимур: кружков — 2
Әлихан: кружков — 2
Повторная запись невозможна: UNIQUE constraint failed: enrollment.student_id, enrollment.club_id
```
(«Әлихан» стоит в конце, потому что SQLite сравнивает буквы по коду Юникода: казахская «Ә» идёт после основных букв кириллицы — хороший вопрос для обсуждения.)

**Задание 6.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
CREATE TABLE category (id INTEGER PRIMARY KEY, title TEXT);
CREATE TABLE operation (id INTEGER PRIMARY KEY,
                        category_id INTEGER NOT NULL REFERENCES category(id),
                        amount INTEGER NOT NULL);
INSERT INTO category VALUES (1, 'Еда');
""")
try:
    conn.execute("INSERT INTO operation (category_id, amount) VALUES (5, 1000)")
except sqlite3.IntegrityError as err:
    print(f"Ошибка: такой категории нет ({err})")
```

**Задание 7.** Проверяется индивидуально: побеседуйте с каждым учеником 2–3 минуты и спросите: «Где в модели хранится вот эта возможность из ТЗ?»

**Задание 8.**
```python
import sqlite3
import sys


def describe(path):
    """Описывает все таблицы базы данных в формате markdown."""
    conn = sqlite3.connect(path)
    tables = conn.execute(
        "SELECT name FROM sqlite_master WHERE type='table' "
        "AND name NOT LIKE 'sqlite_%' ORDER BY name"
    ).fetchall()
    for (table,) in tables:
        fks = {}
        for fk in conn.execute(f"PRAGMA foreign_key_list({table})"):
            # fk: (id, seq, table, from, to, on_update, on_delete, match)
            fks[fk[3]] = f"{fk[2]}.{fk[4]}"
        print(f"### {table}")
        print("| Поле | Тип | NOT NULL | PK | FK |")
        print("|---|---|---|---|---|")
        for col in conn.execute(f"PRAGMA table_info({table})"):
            # col: (cid, name, type, notnull, default, pk)
            print(f"| {col[1]} | {col[2]} | {col[3]} | {col[5]} | {fks.get(col[1], '')} |")
        print()
    conn.close()


if __name__ == "__main__":
    describe(sys.argv[1] if len(sys.argv) > 1 else "qalta.db")
```

**Задание 9.**
```sql
CREATE TABLE person (
    id        INTEGER PRIMARY KEY,
    name      TEXT NOT NULL,
    father_id INTEGER REFERENCES person(id),
    mother_id INTEGER REFERENCES person(id)
);
-- дед (отец отца) человека с id = 5
SELECT grand.name
FROM person AS p
JOIN person AS f     ON f.id = p.father_id
JOIN person AS grand ON grand.id = f.father_id
WHERE p.id = 5;
```
</details>
