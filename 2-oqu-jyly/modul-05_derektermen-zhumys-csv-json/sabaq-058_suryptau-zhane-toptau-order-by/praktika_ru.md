# Урок 58. Практические задания: ORDER BY, GROUP BY, агрегатные функции

**Подготовка.** Создай базу продаж «Школьной столовой»:

```python
import sqlite3

SALES = [
    ("2027-01-18", "Самса", "еда", 2, 350, "7А"),
    ("2027-01-18", "Чай", "напиток", 3, 100, "7А"),
    ("2027-01-18", "Балиш", "еда", 1, 300, "9Б"),
    ("2027-01-18", "Сок", "напиток", 2, 220, "9Б"),
    ("2027-01-19", "Самса", "еда", 4, 350, "10А"),
    ("2027-01-19", "Булочка", "еда", 3, 150, "5Б"),
    ("2027-01-19", "Чай", "напиток", 5, 100, "5Б"),
    ("2027-01-19", "Яблоко", "фрукт", 2, 120, "7А"),
    ("2027-01-20", "Самса", "еда", 3, 350, "9Б"),
    ("2027-01-20", "Сок", "напиток", 4, 220, "10А"),
    ("2027-01-20", "Өрік", "фрукт", 2, 180, "5Б"),
    ("2027-01-20", "Булочка", "еда", 6, 150, "7А"),
    ("2027-01-21", "Балиш", "еда", 2, 300, "10А"),
    ("2027-01-21", "Яблоко", "фрукт", 5, 120, "9Б"),
    ("2027-01-21", "Чай", "напиток", 2, 100, "10А"),
]

conn = sqlite3.connect("canteen.db")
conn.executescript("""
    DROP TABLE IF EXISTS sales;
    CREATE TABLE sales (
        id INTEGER PRIMARY KEY,
        sale_date TEXT NOT NULL,
        product TEXT NOT NULL,
        category TEXT NOT NULL,
        qty INTEGER NOT NULL CHECK (qty > 0),
        price INTEGER NOT NULL,
        class TEXT
    );
""")
conn.executemany(
    "INSERT INTO sales (sale_date, product, category, qty, price, class) VALUES (?, ?, ?, ?, ?, ?)", SALES
)
conn.commit()
conn.close()
print("canteen.db готова: 15 продаж")
```

(«Өрік» — казахское название урюка/абрикоса; это слово специально оставлено по-казахски для задания 8.)

Подсказка: сумма одной продажи = `qty * price`. В SQL её можно вычислить напрямую: `SUM(qty * price)`.

## Базовый уровень

### Задание 1. Сортировка

1. Выведи все продажи по убыванию суммы (`qty * price AS total`).
2. Отсортируй по дате по возрастанию, а при одинаковой дате — по названию продукта.
3. Выведи 3 самые дорогие продажи (`LIMIT`).

```text
Вывод (запрос 3):
2027-01-19 Самса ×4 = 1400 ₸
2027-01-20 Самса ×3 = 1050 ₸
2027-01-20 Булочка ×6 = 900 ₸
```

### Задание 2. Общая статистика

Выведи одним запросом: количество продаж, общее количество проданных товаров, общую выручку, среднюю сумму одной продажи (до 1 знака), цену самого дешёвого и самого дорогого товара, количество различных продуктов.

```text
Вывод:
Продаж: 15
Продано штук: 46
Выручка: 8920 ₸
Средний чек: 594.7 ₸
Цена: 100..350 ₸
Видов продуктов: 7
```

### Задание 3. По категориям

С помощью `GROUP BY category` выведи количество проданных штук и выручку для каждой категории, по убыванию выручки.

```text
Вывод:
еда     — 21 шт., 5400 ₸
напиток — 16 шт., 2320 ₸
фрукт   — 9 шт., 1200 ₸
```

### Задание 4. По дням

Выведи выручку за каждый день и найди самый прибыльный день. Покажи результат в виде «текстовой диаграммы» (каждые 200 ₸ — один `█`).

```text
Вывод:
2027-01-18  1740 ₸  ████████
2027-01-19  2590 ₸  ████████████
...
Самый прибыльный день: 2027-01-20
```

## Продвинутый уровень

### Задание 5. HAVING

1. Продукты, которых всего продано больше 5 штук.
2. Классы, потратившие в столовой больше 2000 ₸.
3. Продукты, которые продавались как минимум в 2 разных дня (`COUNT(DISTINCT sale_date)`).

### Задание 6. Разбиение на страницы

Напиши функцию `get_page(page, size=5)`: она возвращает указанную страницу продаж, отсортированных по `ORDER BY sale_date, id`. Вычисли также общее количество страниц (через `COUNT(*)`). Выведи все страницы по очереди.

### Задание 7. Рейтинг классов

Для каждого класса: количество покупок, общая сумма, самый покупаемый продукт. Чтобы найти самый покупаемый продукт, одного запроса может не хватить — объедини с Python.

## ★ Олимпиадная / творческая задача

### Задание 8. Сортировка по казахскому алфавиту

SQLite не сортирует казахские буквы в алфавитном порядке. Получи все названия продуктов через SQL (`SELECT DISTINCT product`), затем отсортируй их в Python по казахскому алфавиту:

```text
АӘБВГҒДЕЁЖЗИЙКҚЛМНҢОӨПРСТУҰҮФХҺЦЧШЩЪЫІЬЭЮЯ
```

Сравни результат с результатом `ORDER BY product` в SQLite.

Дополнительно: зарегистрируй в SQLite своё правило сравнения через `conn.create_collation("kazakh", func)` и попробуй написать `ORDER BY product COLLATE kazakh`.

### Задание 9. Дни недели

Функция SQLite `strftime('%w', sale_date)` возвращает номер дня недели (0 — воскресенье). Сгруппируй выручку по дням недели и выведи с их русскими названиями (понедельник, вторник, ...).

## Домашнее задание

1. Добавь в свой каталог с уроков 56–57 не менее 3 «аналитических» запросов: топ-5, группировка + агрегат, `HAVING`.
2. Импортируй данные `journal.csv` с урока 50 в SQLite (урок 56, задание 5) и вычисли среднее по каждому предмету для каждого класса **одним SQL-запросом**. Сравни с вариантом на Python.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
print("1) По сумме:")
for d, p, q, total in conn.execute(
    "SELECT sale_date, product, qty, qty * price AS total FROM sales ORDER BY total DESC"
):
    print(f"   {d} {p} ×{q} = {total} ₸")

print("2) По дате, затем по названию:")
for d, p in conn.execute("SELECT sale_date, product FROM sales ORDER BY sale_date, product"):
    print(f"   {d} {p}")

print("3) Лучшие 3:")
for d, p, q, total in conn.execute(
    "SELECT sale_date, product, qty, qty * price AS total FROM sales ORDER BY total DESC LIMIT 3"
):
    print(f"   {d} {p} ×{q} = {total} ₸")
conn.close()
```

Результат части 3:

```text
3) Лучшие 3:
   2027-01-19 Самса ×4 = 1400 ₸
   2027-01-20 Самса ×3 = 1050 ₸
   2027-01-20 Булочка ×6 = 900 ₸
```

**Задание 2**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
row = conn.execute("""
    SELECT COUNT(*), SUM(qty), SUM(qty * price), ROUND(AVG(qty * price), 1),
           MIN(price), MAX(price), COUNT(DISTINCT product)
    FROM sales
""").fetchone()
print("Продаж:", row[0])
print("Продано штук:", row[1])
print("Выручка:", row[2], "₸")
print("Средний чек:", row[3], "₸")
print(f"Цена: {row[4]}..{row[5]} ₸")
print("Видов продуктов:", row[6])
conn.close()
```

Результат:

```text
Продаж: 15
Продано штук: 46
Выручка: 8920 ₸
Средний чек: 594.7 ₸
Цена: 100..350 ₸
Видов продуктов: 7
```

**Задание 3**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
for cat, qty, income in conn.execute("""
    SELECT category, SUM(qty), SUM(qty * price) AS income
    FROM sales
    GROUP BY category
    ORDER BY income DESC
"""):
    print(f"{cat:<7} — {qty} шт., {income} ₸")
conn.close()
```

Результат:

```text
еда     — 21 шт., 5400 ₸
напиток — 16 шт., 2320 ₸
фрукт   — 9 шт., 1200 ₸
```

**Задание 4**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
days = conn.execute(
    "SELECT sale_date, SUM(qty * price) FROM sales GROUP BY sale_date ORDER BY sale_date"
).fetchall()
for day, income in days:
    print(f"{day}  {income:>4} ₸  {'█' * (income // 200)}")
best = max(days, key=lambda d: d[1])
print("Самый прибыльный день:", best[0])

# Средствами самого SQL:
print(conn.execute(
    "SELECT sale_date FROM sales GROUP BY sale_date ORDER BY SUM(qty * price) DESC LIMIT 1"
).fetchone()[0])
conn.close()
```

Результат:

```text
2027-01-18  1740 ₸  ████████
2027-01-19  2590 ₸  ████████████
2027-01-20  3190 ₸  ███████████████
2027-01-21  1400 ₸  ███████
Самый прибыльный день: 2027-01-20
2027-01-20
```

**Задание 5**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
print("1)", conn.execute(
    "SELECT product, SUM(qty) FROM sales GROUP BY product HAVING SUM(qty) > 5 ORDER BY SUM(qty) DESC"
).fetchall())
print("2)", conn.execute(
    "SELECT class, SUM(qty * price) FROM sales GROUP BY class HAVING SUM(qty * price) > 2000"
).fetchall())
print("3)", conn.execute(
    "SELECT product, COUNT(DISTINCT sale_date) FROM sales GROUP BY product HAVING COUNT(DISTINCT sale_date) >= 2"
).fetchall())
conn.close()
```

Результат:

```text
1) [('Чай', 10), ('Самса', 9), ('Булочка', 9), ('Яблоко', 7), ('Сок', 6)]
2) [('10А', 3080), ('7А', 2140), ('9Б', 2390)]
3) [('Балиш', 2), ('Булочка', 2), ('Самса', 3), ('Сок', 2), ('Чай', 3), ('Яблоко', 2)]
```

(Порядок равных значений (Самса и Булочка — 9) не гарантирован; если нужен стабильный порядок: `ORDER BY SUM(qty) DESC, product`.)

**Задание 6**

```python
import math
import sqlite3

conn = sqlite3.connect("canteen.db")


def get_page(page, size=5):
    offset = (page - 1) * size
    return conn.execute(
        "SELECT id, sale_date, product FROM sales ORDER BY sale_date, id LIMIT ? OFFSET ?",
        (size, offset),
    ).fetchall()


total = conn.execute("SELECT COUNT(*) FROM sales").fetchone()[0]
pages = math.ceil(total / 5)
for p in range(1, pages + 1):
    print(f"Страница {p}/{pages}:", [f"{r[0]}:{r[2]}" for r in get_page(p)])
conn.close()
```

Результат:

```text
Страница 1/3: ['1:Самса', '2:Чай', '3:Балиш', '4:Сок', '5:Самса']
Страница 2/3: ['6:Булочка', '7:Чай', '8:Яблоко', '9:Самса', '10:Сок']
Страница 3/3: ['11:Өрік', '12:Булочка', '13:Балиш', '14:Яблоко', '15:Чай']
```

**Задание 7**

```python
import sqlite3

conn = sqlite3.connect("canteen.db")
summary = conn.execute("""
    SELECT class, COUNT(*), SUM(qty * price) AS spent
    FROM sales GROUP BY class ORDER BY spent DESC
""").fetchall()

for cls, purchases, spent in summary:
    favourite = conn.execute("""
        SELECT product, SUM(qty) AS n FROM sales
        WHERE class = ? GROUP BY product ORDER BY n DESC, product LIMIT 1
    """, (cls,)).fetchone()
    print(f"{cls:<4} покупок: {purchases}, сумма: {spent} ₸, любимый продукт: {favourite[0]} ({favourite[1]})")
conn.close()
```

Результат:

```text
10А  покупок: 4, сумма: 3080 ₸, любимый продукт: Самса (4)
9Б   покупок: 4, сумма: 2390 ₸, любимый продукт: Яблоко (5)
7А   покупок: 4, сумма: 2140 ₸, любимый продукт: Булочка (6)
5Б   покупок: 3, сумма: 1310 ₸, любимый продукт: Чай (5)
```

**Задание 8**

```python
import sqlite3

ALPHABET = "АӘБВГҒДЕЁЖЗИЙКҚЛМНҢОӨПРСТУҰҮФХҺЦЧШЩЪЫІЬЭЮЯ"
ORDER = {ch: i for i, ch in enumerate(ALPHABET)}


def kazakh_key(word):
    """Превращает слово в список номеров букв в алфавите."""
    return [ORDER.get(ch, 100 + ord(ch)) for ch in word.upper()]


def kazakh_collation(a, b):
    ka, kb = kazakh_key(a), kazakh_key(b)
    return (ka > kb) - (ka < kb)      # -1, 0 или 1


conn = sqlite3.connect("canteen.db")
products = [r[0] for r in conn.execute("SELECT DISTINCT product FROM sales")]
print("SQLite ORDER BY:  ", [r[0] for r in conn.execute("SELECT DISTINCT product FROM sales ORDER BY product")])
print("Казахский алфавит:", sorted(products, key=kazakh_key))

conn.create_collation("kazakh", kazakh_collation)
print("COLLATE kazakh:   ", [r[0] for r in conn.execute(
    "SELECT DISTINCT product FROM sales ORDER BY product COLLATE kazakh")])
conn.close()
```

Результат:

```text
SQLite ORDER BY:   ['Балиш', 'Булочка', 'Самса', 'Сок', 'Чай', 'Яблоко', 'Өрік']
Казахский алфавит: ['Балиш', 'Булочка', 'Өрік', 'Самса', 'Сок', 'Чай', 'Яблоко']
COLLATE kazakh:    ['Балиш', 'Булочка', 'Өрік', 'Самса', 'Сок', 'Чай', 'Яблоко']
```

Объяснение: SQLite поставил «Өрік» в конец, потому что код буквы «Ө» (U+04E8) больше кодов всех русских букв, а в казахском алфавите «Ө» стоит между «О» и «П». Точно так же слова «Құрт» и «Лимонад» SQLite расположит в порядке «Лимонад, Құрт» (Қ = U+049A > Л = U+041B), а по алфавиту «Құрт» идёт первым.

**Задание 9**

```python
import sqlite3

DAYS = ["воскресенье", "понедельник", "вторник", "среда", "четверг", "пятница", "суббота"]

conn = sqlite3.connect("canteen.db")
for day_no, income in conn.execute("""
    SELECT CAST(strftime('%w', sale_date) AS INTEGER) AS wd, SUM(qty * price)
    FROM sales GROUP BY wd ORDER BY wd
"""):
    print(f"{DAYS[day_no]:<11} {income} ₸")
conn.close()
```

Результат:

```text
понедельник 1740 ₸
вторник     2590 ₸
среда       3190 ₸
четверг     1400 ₸
```

</details>
