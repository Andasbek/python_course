# Урок 54. Практические задания: проектирование базы данных

На этом уроке основные инструменты — ручка, бумага и мышление. Схемы можно рисовать на бумаге, на доске или на сайтах dbdiagram.io / drawdb.app. Задания на Python предназначены для проверки проекта.

## Базовый уровень

### Задание 1. Определи вид связи

Для каждой пары определи вид связи (1:1, 1:N, M:N) и напиши, в какой таблице будет внешний ключ (или нужна ли промежуточная таблица):

| № | Пара сущностей |
|---|---|
| 1 | Область — Город |
| 2 | Гражданин — Удостоверение личности |
| 3 | Ученик — Предмет |
| 4 | Автор — Книга |
| 5 | Игрок — Результат игры (сессия) |
| 6 | Заказ — Товар (магазин Kaspi) |
| 7 | Футбольная команда — Игрок (в одном сезоне) |
| 8 | Школа — Директор |

### Задание 2. Найди сущности и атрибуты

Прочитай текст и запиши сущности (таблицы), атрибуты (поля) каждой из них и связи:

> «Школьная библиотека ведёт учёт книг. У каждой книги есть название, автор, год издания и несколько экземпляров; у каждого экземпляра есть инвентарный номер. Читатели — ученики школы: ФИО, класс. Библиотекарь отмечает, какой экземпляр, какому ученику и в какой день выдан и когда он возвращён.»

### Задание 3. Нормализуй таблицу

Разбей таблицу ниже до 3НФ. На каждом шаге напиши, какая проблема решена.

| order_no | date | customer | customer_phone | products | total |
|---|---|---|---|---|---|
| 101 | 2027-01-10 | Асем Т. | +77011112233 | Тетрадь ×10, Ручка ×5 | 2100 |
| 102 | 2027-01-11 | Ерлан К. | +77025556677 | Ручка ×2 | 240 |
| 103 | 2027-01-11 | Асем Т. | +77011112233 | Линейка ×1 | 180 |

Подсказка: должны получиться таблицы `customers`, `products`, `orders`, `order_items`. Подумай, нужно ли хранить поле `total`.

### Задание 4. Прочитай ER-диаграмму

```text
+------------+        +------------+        +-------------+
|  regions   |1      N|   cities   |1      N|  schools    |
+------------+--------+------------+--------+-------------+
| PK id      |        | PK id      |        | PK id       |
|    name    |        |    name    |        |    number   |
+------------+        | FK region_id|       |    name     |
                      +------------+        | FK city_id  |
                                            +-------------+
```

Вопросы:
1. Сколько таблиц и сколько связей?
2. Какие таблицы нужно использовать, чтобы найти «все школы Алматинской области»?
3. Может ли один город принадлежать двум областям? Почему?
4. Как бы ты изменил схему, чтобы добавить сельские школы (не в городе)?

## Продвинутый уровень

### Задание 5. «Центр кружков» (в группах)

Расширь предметную область из теории:
- у каждого кружка есть **расписание** (день недели, время начала, кабинет) — один кружок несколько раз в неделю;
- отмечается **посещаемость** занятий учениками (дата, пришёл/не пришёл);
- в конце кружка ученик получает **сертификат** (номер, дата).

Нарисуй полную ER-диаграмму (не менее 6 таблиц), отметь тип каждого поля, PK, FK, ограничения `UNIQUE`, `NOT NULL`. Покажи по схеме, как найти ответы на такие вопросы заказчика:
1. Какие кружки проходят в понедельник в кабинете 204?
2. Сколько раз Айгерим пропустила кружок Python в январе?
3. Сколько учеников в кружках, которые ведёт Ермекова Г.С.?

### Задание 6. Проверка схемы на Python

Запиши свой проект (задание 5) в виде словаря, как в теории, и создай программу, которая проверяет:
- в каждой таблице есть `pk`, и он входит в `fields`;
- каждый FK ссылается на существующую таблицу;
- найти и вывести промежуточные таблицы (количество FK ≥ 2);
- определить **порядок создания** таблиц: таблица, на которую ссылаются, должна стоять раньше ссылающейся.

## ★ Олимпиадная / творческая задача

### Задание 7. Автоматическое определение порядка создания (топологическая сортировка)

Даны таблицы и их FK-зависимости. Напиши функцию, находящую правильный порядок создания таблиц. Если зависимости образуют «круг» (А → Б → А), выведи «Циклическая зависимость!».

```text
deps = {"enrollments": ["students", "clubs"], "clubs": ["teachers", "rooms"],
        "students": [], "teachers": [], "rooms": []}
Вывод:
Порядок создания: students, teachers, rooms, clubs, enrollments
```

### Задание 8. Твой проект

Если твоему годовому проекту (идеи, обсуждавшиеся на уроке 8) нужна база данных, нарисуй её полную ER-диаграмму. Добавь изображение диаграммы в README проекта.

## Домашнее задание

1. Заверши схему библиотеки из задания 2: ER-диаграмма + SQL-описание `CREATE TABLE` каждой таблицы (по образцу из теории).
2. Подумай: какие таблицы нужны для приложения «База учеников» (уроки 62–63)? Предложи 3–4 таблицы.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**

| № | Пара | Вид | Реализация |
|---|---|---|---|
| 1 | Область — Город | 1:N | `cities.region_id` |
| 2 | Гражданин — Удостоверение личности | 1:1 (одновременно одно действующее удостоверение) | `id_cards.citizen_id UNIQUE` |
| 3 | Ученик — Предмет | M:N | промежуточная `student_subjects` (или `grades`) |
| 4 | Автор — Книга | M:N (у книги может быть несколько авторов) | промежуточная `book_authors` |
| 5 | Игрок — Сессия | 1:N | `sessions.player_id` |
| 6 | Заказ — Товар | M:N | промежуточная `order_items` (количество, цена) |
| 7 | Команда — Игрок (один сезон) | 1:N | `players.team_id` (если сезонов несколько — M:N, промежуточная `contracts`) |
| 8 | Школа — Директор | 1:1 | `schools.director_id UNIQUE` |

**Задание 2.** Сущности: `books` (id, title, author, year), `copies` (id, inv_no UNIQUE, book_id FK), `readers` (id, last_name, first_name, class), `loans` (id, copy_id FK, reader_id FK, issued_date, returned_date NULL). Связи: books 1:N copies; copies 1:N loans; readers 1:N loans. (M:N между читателем и экземпляром — через `loans`.) Дополнительно: если авторов несколько — `authors` + `book_authors`.

**Задание 3.**
- 1НФ: разбиваем список в ячейке `products` на строки: (order_no, product, qty).
- 2НФ: `date`, `customer` зависят только от `order_no` → `orders(order_no, date, customer...)` и `order_items(order_no, product, qty)`.
- 3НФ: `customer_phone` зависит не от заказа, а от клиента → `customers(id, name, phone)`, `orders.customer_id`. Цена товара → `products(id, name, price)`.
- Результат: `customers(id, name, phone)`, `products(id, name, price)`, `orders(id, date, customer_id)`, `order_items(id, order_id, product_id, qty, price)`.
- `total` — вычисляемое значение, его лучше не хранить (или хранить только историческую цену как `order_items.price`: цена товара может потом измениться).

**Задание 4.** 1) 3 таблицы, 2 связи (1:N). 2) `regions` → `cities` → `schools` (`region.name = 'Алматинская область'`, через cities к schools). 3) Нет: в таблице `cities` только одно поле `region_id`. 4) Создать общую таблицу `settlements` (населённые пункты) с полем `type` (город/село) или добавить в `schools` поле `village_id`.

**Задание 5 (образец ответа).**

```sql
CREATE TABLE teachers (id INTEGER PRIMARY KEY, last_name TEXT NOT NULL, first_name TEXT NOT NULL, phone TEXT UNIQUE);
CREATE TABLE rooms (id INTEGER PRIMARY KEY, number TEXT NOT NULL UNIQUE, seats INTEGER);
CREATE TABLE clubs (id INTEGER PRIMARY KEY, title TEXT NOT NULL UNIQUE, teacher_id INTEGER REFERENCES teachers(id));
CREATE TABLE schedule (
    id INTEGER PRIMARY KEY,
    club_id INTEGER NOT NULL REFERENCES clubs(id),
    room_id INTEGER NOT NULL REFERENCES rooms(id),
    weekday INTEGER CHECK (weekday BETWEEN 1 AND 7),
    start_time TEXT NOT NULL
);
CREATE TABLE students (id INTEGER PRIMARY KEY, last_name TEXT NOT NULL, first_name TEXT NOT NULL, birth_date TEXT);
CREATE TABLE enrollments (
    id INTEGER PRIMARY KEY,
    student_id INTEGER NOT NULL REFERENCES students(id),
    club_id INTEGER NOT NULL REFERENCES clubs(id),
    enrolled TEXT,
    UNIQUE (student_id, club_id)
);
CREATE TABLE attendance (
    id INTEGER PRIMARY KEY,
    enrollment_id INTEGER NOT NULL REFERENCES enrollments(id),
    lesson_date TEXT NOT NULL,
    present INTEGER NOT NULL DEFAULT 1,
    UNIQUE (enrollment_id, lesson_date)
);
CREATE TABLE certificates (
    id INTEGER PRIMARY KEY,
    enrollment_id INTEGER NOT NULL UNIQUE REFERENCES enrollments(id),
    cert_no TEXT NOT NULL UNIQUE,
    issued TEXT
);
```

Путь к ответам: 1) `schedule` (weekday = 1) + `rooms` (number = '204') + `clubs`. 2) `students` → `enrollments` (кружок Python) → `attendance` (январь, present = 0) → подсчёт. 3) `teachers` → `clubs` → `enrollments` → подсчёт. `certificates` и `enrollments` — 1:1.

**Задание 6**

```python
schema = {
    "teachers": {"pk": "id", "fields": ["id", "last_name", "phone"], "fk": {}},
    "rooms": {"pk": "id", "fields": ["id", "number", "seats"], "fk": {}},
    "clubs": {"pk": "id", "fields": ["id", "title", "teacher_id"], "fk": {"teacher_id": "teachers"}},
    "schedule": {"pk": "id", "fields": ["id", "club_id", "room_id", "weekday"],
                 "fk": {"club_id": "clubs", "room_id": "rooms"}},
    "students": {"pk": "id", "fields": ["id", "last_name"], "fk": {}},
    "enrollments": {"pk": "id", "fields": ["id", "student_id", "club_id"],
                    "fk": {"student_id": "students", "club_id": "clubs"}},
}

ok = True
for table, info in schema.items():
    if info["pk"] not in info["fields"]:
        print(f"ОШИБКА: в таблице {table} нет поля PK")
        ok = False
    for field, target in info["fk"].items():
        if target not in schema:
            print(f"ОШИБКА: {table}.{field} ссылается на несуществующую таблицу ({target})")
            ok = False
print("Схема корректна" if ok else "В схеме есть ошибка")
print("Промежуточные таблицы:", [t for t, i in schema.items() if len(i["fk"]) >= 2])

created = []
while len(created) < len(schema):
    for table, info in schema.items():
        if table not in created and all(t in created for t in info["fk"].values()):
            created.append(table)
print("Порядок создания:", ", ".join(created))
```

Результат:

```text
Схема корректна
Промежуточные таблицы: ['schedule', 'enrollments']
Порядок создания: teachers, rooms, clubs, schedule, students, enrollments
```

Примечание: `schedule` формально — таблица с двумя FK (M:N между clubs и rooms), но у неё есть собственный смысл (время занятия). Обсудите с учениками: «промежуточная таблица» может превратиться в полноценную сущность со своими атрибутами.

Цикл `while` выше при циклической зависимости будет крутиться бесконечно — это решает задание 7.

**Задание 7**

```python
def creation_order(deps):
    """Сортирует таблицы по зависимостям (алгоритм Кана)."""
    order = []
    remaining = dict(deps)
    while remaining:
        ready = [t for t, d in remaining.items() if all(x in order for x in d)]
        if not ready:
            return None  # ни одну нельзя создать → циклическая зависимость
        for t in ready:
            order.append(t)
            del remaining[t]
    return order


deps = {"enrollments": ["students", "clubs"], "clubs": ["teachers", "rooms"],
        "students": [], "teachers": [], "rooms": []}
result = creation_order(deps)
print("Порядок создания:", ", ".join(result))

bad = {"a": ["b"], "b": ["a"]}
print("Порядок создания:", ", ".join(creation_order(bad)) if creation_order(bad) else "Циклическая зависимость!")
```

Результат:

```text
Порядок создания: students, teachers, rooms, clubs, enrollments
Порядок создания: Циклическая зависимость!
```

</details>
