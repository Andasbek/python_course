# Урок 57. Практические задания: UPDATE, DELETE, WHERE

**Подготовка.** Все задания выполняются с базой данных «Школьный магазин». Сначала запусти эту программу и создай `shop.db` (её можно запустить заново в любой момент, чтобы вернуть базу в исходное состояние):

```python
import sqlite3

PRODUCTS = [
    ("Тетрадь 48 л.", "канцелярия", 250, 120),
    ("Тетрадь 12 л.", "канцелярия", 90, 300),
    ("Ручка синяя", "канцелярия", 120, 200),
    ("Карандаш", "канцелярия", 80, 0),
    ("Линейка 30 см", "канцелярия", 180, 45),
    ("Сок 0.2 л", "еда", 220, 60),
    ("Самса", "еда", 350, 25),
    ("Балиш", "еда", 300, 0),
    ("Вода 0.5 л", "еда", 150, 80),
    ("Флешка 16 ГБ", "электроника", 2900, 7),
    ("Наушники", "электроника", 4500, None),
]

conn = sqlite3.connect("shop.db")
conn.executescript("""
    DROP TABLE IF EXISTS products;
    CREATE TABLE products (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL UNIQUE,
        category TEXT NOT NULL,
        price INTEGER CHECK (price > 0),
        stock INTEGER
    );
""")
conn.executemany("INSERT INTO products (name, category, price, stock) VALUES (?, ?, ?, ?)", PRODUCTS)
conn.commit()
conn.close()
print("shop.db готова: 11 товаров")
```

## Базовый уровень

### Задание 1. Упражнения с WHERE

Напиши каждый запрос и выведи результат:
1. Товары дешевле 200 ₸.
2. Товары категории «канцелярия» с ценой от 100 до 200 (`BETWEEN`).
3. Товары категории «еда» или «электроника» (`IN`).
4. Товары, название которых начинается со слова «Тетрадь» (`LIKE`).
5. Товары, которых нет на складе (`stock = 0`), и товары с неизвестным количеством (`IS NULL`).
6. Товары, в названии которых есть буква «л», но **не** из категории «еда».

```text
Вывод (запрос 1):
Тетрадь 12 л. — 90 ₸
Ручка синяя — 120 ₸
Карандаш — 80 ₸
Линейка 30 см — 180 ₸
Вода 0.5 л — 150 ₸
```

### Задание 2. Изменение цены

Пользователь вводит `id` товара и новую цену. Программа сначала показывает старую цену товара, затем выполняет `UPDATE`. Если `rowcount = 0` — «Такого товара нет».

```text
Ввод:
id товара: 7
Новая цена: 380
Вывод:
Самса: 350 ₸ → 380 ₸. Сохранено.
```

### Задание 3. Покупка

Пользователь вводит `id` товара и количество. Если на складе достаточно — `stock` уменьшается и выводится «Продано, осталось: N». Если недостаточно — «На складе только N шт.». (Подсказка: `UPDATE products SET stock = stock - ? WHERE id = ? AND stock >= ?` — можно проверить и уменьшить одним запросом; `rowcount` сообщит результат.)

### Задание 4. День скидок

Сделай скидку 10% на все товары категории «канцелярия» (цену округли до целых тенге). Выведи, у скольких товаров изменилась цена.

```text
Вывод:
Скидка: 5 товаров
Тетрадь 48 л.: 225 ₸
...
```

### Задание 5. Удаление старых товаров

Удали товары, которых нет на складе (`stock = 0`). Перед удалением покажи их список и спроси «Подтверждаете удаление? (да/нет)». Если ответ «нет» — ничего не удаляется.

## Продвинутый уровень

### Задание 6. Функция безопасного обновления

Напиши функцию `safe_update(conn, sql, params, expected_max)`: она выполняет команду; если `rowcount` больше `expected_max` — делает `rollback()` и возвращает `False`; иначе — `commit()` и `True`. Например, если запрос, предназначенный для изменения одного товара, по неосторожности изменит 11 записей, он должен быть отменён. В тесте специально попробуй отправить `UPDATE` без `WHERE`.

### Задание 7. Фильтр поиска

Пользователь вводит несколько фильтров (любой можно оставить пустым): категория, минимальная цена, максимальная цена, часть названия. Программа составляет условие `WHERE` только из заполненных фильтров **с параметрами** и выводит результат.

```text
Ввод:
Категория: канцелярия
Мин. цена:
Макс. цена: 150
В названии:
Вывод:
SQL: SELECT name, price FROM products WHERE category = ? AND price <= ?
Параметры: ['канцелярия', 150]
Тетрадь 12 л. — 90 ₸
Ручка синяя — 120 ₸
Карандаш — 80 ₸
```

### Задание 8. Мягкое удаление

Добавь в таблицу `products` поле `active INTEGER DEFAULT 1`. Пусть действие «Удалить» делает товар `active = 0`, все списки показывают только активные товары, а действие «Восстановить» возвращает товар.

## ★ Олимпиадная / творческая задача

### Задание 9. Журнал изменений (audit log)

Напиши функцию `change_price(product_id, new_price)`, которая записывает каждое изменение цены в таблицу `price_log(id, product_id, old_price, new_price, changed_at)`. Оба действия (UPDATE + INSERT) должны выполняться **в одной транзакции**: если одно из них не удалось — отменяются оба. В конце выведи историю цен товара.

Дополнительно (собственное средство SQL): сделай этот же журнал с помощью **триггера** SQLite (`CREATE TRIGGER ... AFTER UPDATE OF price ON products`) — тогда журнал будет заполняться вообще без кода на Python.

## Домашнее задание

1. Добавь в свой каталог (фильмы/игры/книги) с урока 56 возможность изменения и удаления: найти запись по id, изменить любое её поле, удалить с подтверждением.
2. Подумай: в приложении «База учеников» нужно действительно удалять ученика или правильнее мягкое удаление? Напиши причину.

## Для педагога: решения

<details><summary>Показать решения</summary>

Считается, что все решения запускаются после программы из раздела «Подготовка».

**Задание 1**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
queries = [
    ("1) дешевле 200", "SELECT name, price FROM products WHERE price < ?", (200,)),
    ("2) канцелярия 100..200", "SELECT name, price FROM products WHERE category = ? AND price BETWEEN ? AND ?",
     ("канцелярия", 100, 200)),
    ("3) еда/электроника", "SELECT name, price FROM products WHERE category IN (?, ?)", ("еда", "электроника")),
    ("4) Тетрадь...", "SELECT name, price FROM products WHERE name LIKE ?", ("Тетрадь%",)),
    ("5) нет/неизвестно", "SELECT name, stock FROM products WHERE stock = 0 OR stock IS NULL", ()),
    ("6) «л», не еда", "SELECT name, category FROM products WHERE name LIKE ? AND category <> ?",
     ("%л%", "еда")),
]
for title, sql, params in queries:
    rows = conn.execute(sql, params).fetchall()
    print(title + ":", "; ".join(f"{a} — {b}" for a, b in rows))
conn.close()
```

Результат:

```text
1) дешевле 200: Тетрадь 12 л. — 90; Ручка синяя — 120; Карандаш — 80; Линейка 30 см — 180; Вода 0.5 л — 150
2) канцелярия 100..200: Ручка синяя — 120; Линейка 30 см — 180
3) еда/электроника: Сок 0.2 л — 220; Самса — 350; Балиш — 300; Вода 0.5 л — 150; Флешка 16 ГБ — 2900; Наушники — 4500
4) Тетрадь...: Тетрадь 48 л. — 250; Тетрадь 12 л. — 90
5) нет/неизвестно: Карандаш — 0; Балиш — 0; Наушники — None
6) «л», не еда: Тетрадь 48 л. — канцелярия; Тетрадь 12 л. — канцелярия; Флешка 16 ГБ — электроника
```

**Задание 2**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
product_id = int(input("id товара: "))
new_price = int(input("Новая цена: "))

row = conn.execute("SELECT name, price FROM products WHERE id = ?", (product_id,)).fetchone()
if row is None:
    print("Такого товара нет")
else:
    conn.execute("UPDATE products SET price = ? WHERE id = ?", (new_price, product_id))
    conn.commit()
    print(f"{row[0]}: {row[1]} ₸ → {new_price} ₸. Сохранено.")
conn.close()
```

**Задание 3**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
product_id = int(input("id товара: "))
qty = int(input("Количество: "))

cur = conn.execute(
    "UPDATE products SET stock = stock - ? WHERE id = ? AND stock >= ?",
    (qty, product_id, qty),
)
if cur.rowcount == 1:
    conn.commit()
    left = conn.execute("SELECT stock FROM products WHERE id = ?", (product_id,)).fetchone()[0]
    print("Продано, осталось:", left)
else:
    row = conn.execute("SELECT stock FROM products WHERE id = ?", (product_id,)).fetchone()
    print("Такого товара нет" if row is None else f"На складе только {row[0] or 0} шт.")
conn.close()
```

Важно проверять и уменьшать в одном запросе: если два кассира одновременно продают последний товар, они оба не увидят «есть», и остаток на складе не уйдёт в минус.

**Задание 4**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
cur = conn.execute("UPDATE products SET price = CAST(ROUND(price * 0.9) AS INTEGER) WHERE category = ?", ("канцелярия",))
conn.commit()
print("Скидка:", cur.rowcount, "товаров")
for name, price in conn.execute("SELECT name, price FROM products WHERE category = ?", ("канцелярия",)):
    print(f"{name}: {price} ₸")
conn.close()
```

Результат (с исходной базой):

```text
Скидка: 5 товаров
Тетрадь 48 л.: 225 ₸
Тетрадь 12 л.: 81 ₸
Ручка синяя: 108 ₸
Карандаш: 72 ₸
Линейка 30 см: 162 ₸
```

**Задание 5**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
rows = conn.execute("SELECT id, name FROM products WHERE stock = 0").fetchall()
if not rows:
    print("Нет товаров для удаления")
else:
    print("Будут удалены:", ", ".join(name for _, name in rows))
    if input("Подтверждаете удаление? (да/нет): ").strip().lower() == "да":
        cur = conn.execute("DELETE FROM products WHERE stock = 0")
        conn.commit()
        print("Удалено:", cur.rowcount)
    else:
        print("Отменено")
conn.close()
```

**Задание 6**

```python
import sqlite3


def safe_update(conn, sql, params, expected_max=1):
    """Если затронуто больше записей, чем ожидалось, — rollback."""
    cur = conn.execute(sql, params)
    if cur.rowcount > expected_max:
        conn.rollback()
        print(f"Опасно! Изменилось бы {cur.rowcount} записей (ожидалось ≤ {expected_max}). Отменено.")
        return False
    conn.commit()
    print(f"Сохранено: {cur.rowcount} запись")
    return True


conn = sqlite3.connect("shop.db")
safe_update(conn, "UPDATE products SET price = ? WHERE id = ?", (260, 1))
safe_update(conn, "UPDATE products SET price = ?", (1,))            # забыли WHERE!
print("Товаров с ценой 1 ₸:", conn.execute("SELECT COUNT(*) FROM products WHERE price = 1").fetchone()[0])
conn.close()
```

Результат:

```text
Сохранено: 1 запись
Опасно! Изменилось бы 11 записей (ожидалось ≤ 1). Отменено.
Товаров с ценой 1 ₸: 0
```

**Задание 7**

```python
import sqlite3


def build_query(category="", min_price="", max_price="", text=""):
    conditions, params = [], []
    if category:
        conditions.append("category = ?")
        params.append(category)
    if min_price:
        conditions.append("price >= ?")
        params.append(int(min_price))
    if max_price:
        conditions.append("price <= ?")
        params.append(int(max_price))
    if text:
        conditions.append("name LIKE ?")
        params.append(f"%{text}%")
    sql = "SELECT name, price FROM products"
    if conditions:
        sql += " WHERE " + " AND ".join(conditions)
    return sql, params


sql, params = build_query(category="канцелярия", max_price="150")  # в реальной программе: через input()
print("SQL:", sql)
print("Параметры:", params)
conn = sqlite3.connect("shop.db")
for name, price in conn.execute(sql, params):
    print(f"{name} — {price} ₸")
conn.close()
```

Результат (с исходной базой):

```text
SQL: SELECT name, price FROM products WHERE category = ? AND price <= ?
Параметры: ['канцелярия', 150]
Тетрадь 12 л. — 90 ₸
Ручка синяя — 120 ₸
Карандаш — 80 ₸
```

Важно: в строку SQL добавляются только **написанные нами** фрагменты условий, а значения пользователя — только через `params`.

**Задание 8**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
columns = [r[1] for r in conn.execute("PRAGMA table_info(products)")]
if "active" not in columns:
    conn.execute("ALTER TABLE products ADD COLUMN active INTEGER DEFAULT 1")


def soft_delete(product_id):
    conn.execute("UPDATE products SET active = 0 WHERE id = ?", (product_id,))
    conn.commit()


def restore(product_id):
    conn.execute("UPDATE products SET active = 1 WHERE id = ?", (product_id,))
    conn.commit()


def active_count():
    return conn.execute("SELECT COUNT(*) FROM products WHERE active = 1").fetchone()[0]


print("Активных:", active_count())
soft_delete(4)
print("После «удаления» карандаша:", active_count())
restore(4)
print("После восстановления:", active_count())
conn.close()
```

Результат:

```text
Активных: 11
После «удаления» карандаша: 10
После восстановления: 11
```

**Задание 9**

```python
import sqlite3
from datetime import datetime

conn = sqlite3.connect("shop.db")
conn.execute("PRAGMA foreign_keys = ON")
conn.execute("""
    CREATE TABLE IF NOT EXISTS price_log (
        id INTEGER PRIMARY KEY,
        product_id INTEGER NOT NULL REFERENCES products(id),
        old_price INTEGER,
        new_price INTEGER,
        changed_at TEXT
    )
""")


def change_price(product_id, new_price):
    row = conn.execute("SELECT price FROM products WHERE id = ?", (product_id,)).fetchone()
    if row is None:
        print("Товара нет")
        return
    try:
        with conn:   # оба действия в одной транзакции
            conn.execute("UPDATE products SET price = ? WHERE id = ?", (new_price, product_id))
            conn.execute(
                "INSERT INTO price_log (product_id, old_price, new_price, changed_at) VALUES (?, ?, ?, ?)",
                (product_id, row[0], new_price, datetime.now().isoformat(timespec="seconds")),
            )
    except sqlite3.IntegrityError as e:
        print("Отменено:", e)


change_price(7, 380)
change_price(7, 400)
change_price(7, -5)      # CHECK (price > 0) → оба действия отменяются
for old, new in conn.execute("SELECT old_price, new_price FROM price_log WHERE product_id = ?", (7,)):
    print(f"{old} → {new}")
conn.close()
```

Результат (с исходной базой):

```text
Отменено: CHECK constraint failed: price > 0
350 → 380
380 → 400
```

Вариант с триггером:

```sql
CREATE TRIGGER IF NOT EXISTS log_price
AFTER UPDATE OF price ON products
WHEN OLD.price <> NEW.price
BEGIN
    INSERT INTO price_log (product_id, old_price, new_price, changed_at)
    VALUES (OLD.id, OLD.price, NEW.price, datetime('now'));
END;
```

</details>
