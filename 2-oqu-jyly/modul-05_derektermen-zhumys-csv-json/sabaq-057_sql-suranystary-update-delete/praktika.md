# Сабақ 57. Практикалық тапсырмалар: UPDATE, DELETE, WHERE

**Дайындық.** Барлық тапсырма «Мектеп дүкені» деректер қорымен орындалады. Алдымен мына бағдарламаны іске қосып, `shop.db` жаса (оны кез келген уақытта қайта іске қосып, қорды бастапқы күйге келтіруге болады):

```python
import sqlite3

PRODUCTS = [
    ("Дәптер 48 п.", "кеңсе", 250, 120),
    ("Дәптер 12 п.", "кеңсе", 90, 300),
    ("Қалам көк", "кеңсе", 120, 200),
    ("Қарындаш", "кеңсе", 80, 0),
    ("Сызғыш 30 см", "кеңсе", 180, 45),
    ("Шырын 0.2 л", "тамақ", 220, 60),
    ("Самса", "тамақ", 350, 25),
    ("Бәліш", "тамақ", 300, 0),
    ("Су 0.5 л", "тамақ", 150, 80),
    ("Флешка 16 ГБ", "электроника", 2900, 7),
    ("Құлаққап", "электроника", 4500, None),
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
print("shop.db дайын: 11 тауар")
```

## Базалық деңгей

### 1-тапсырма. WHERE жаттығулары

Әр сұранысты жазып, нәтижесін шығар:
1. Бағасы 200 ₸-ден арзан тауарлар.
2. «кеңсе» санатындағы, бағасы 100 мен 200 аралығындағы тауарлар (`BETWEEN`).
3. «тамақ» немесе «электроника» санатындағы тауарлар (`IN`).
4. Атауы «Дәптер» сөзінен басталатын тауарлар (`LIKE`).
5. Қоймада жоқ тауарлар (`stock = 0`) және саны белгісіз тауарлар (`IS NULL`).
6. Атауында «л» әрпі бар, бірақ «тамақ» санатынан **емес** тауарлар.

```text
Шығару (1-сұраныс):
Дәптер 12 п. — 90 ₸
Қалам көк — 120 ₸
Қарындаш — 80 ₸
Сызғыш 30 см — 180 ₸
Су 0.5 л — 150 ₸
```

### 2-тапсырма. Бағаны өзгерту

Пайдаланушы тауардың `id`-ін және жаңа бағасын енгізеді. Бағдарлама алдымен тауардың ескі бағасын көрсетеді, сосын `UPDATE` жасайды. `rowcount = 0` болса — «Мұндай тауар жоқ».

```text
Енгізу:
Тауар id: 7
Жаңа баға: 380
Шығару:
Самса: 350 ₸ → 380 ₸. Сақталды.
```

### 3-тапсырма. Сатып алу

Пайдаланушы тауар `id`-і мен санын енгізеді. Қоймада жеткілікті болса — `stock` азаяды және «Сатылды, қалды: N». Жеткіліксіз болса — «Қоймада тек N дана бар». (Кеңес: `UPDATE products SET stock = stock - ? WHERE id = ? AND stock >= ?` — бір сұраныспен тексеріп, азайтуға болады; `rowcount` нәтижені айтады.)

### 4-тапсырма. Жеңілдік күні

«кеңсе» санатындағы барлық тауарға 10% жеңілдік жаса (бағаны бүтін теңгеге дейін дөңгелекте). Неше тауардың бағасы өзгергенін шығар.

```text
Шығару:
Жеңілдік: 5 тауар
Дәптер 48 п.: 225 ₸
...
```

### 5-тапсырма. Ескі тауарларды жою

Қоймада жоқ (`stock = 0`) тауарларды жой. Жоймас бұрын олардың тізімін көрсетіп, «Жоюды растайсыз ба? (иә/жоқ)» деп сұра. «жоқ» десе — ештеңе жойылмайды.

## Кеңейтілген деңгей

### 6-тапсырма. Қауіпсіз жаңарту функциясы

`safe_update(conn, sql, params, expected_max)` функциясын жаз: команданы орындайды; `rowcount` `expected_max`-тан асса — `rollback()` жасап, `False` қайтарады; әйтпесе `commit()` және `True`. Мысалы, бір тауарды өзгертуге арналған сұраныс абайсызда 11 жазбаны өзгертсе, ол болдырылмауы керек. Тестте әдейі `WHERE`-сіз `UPDATE` жіберіп көр.

### 7-тапсырма. Іздеу сүзгісі

Пайдаланушы бірнеше сүзгіні енгізеді (кез келгенін бос қалдыруға болады): санат, ең аз баға, ең көп баға, атауының бөлігі. Бағдарлама тек толтырылған сүзгілерден `WHERE` шартын **параметрлермен** құрастырып, нәтижені шығарады.

```text
Енгізу:
Санат: кеңсе
Мин. баға:
Макс. баға: 150
Атауында:
Шығару:
SQL: SELECT name, price FROM products WHERE category = ? AND price <= ?
Параметрлер: ['кеңсе', 150]
Дәптер 12 п. — 90 ₸
Қалам көк — 120 ₸
Қарындаш — 80 ₸
```

### 8-тапсырма. Жұмсақ жою

`products` кестесіне `active INTEGER DEFAULT 1` өрісін қос. «Жою» әрекеті тауарды `active = 0` етсін, барлық тізімдер тек белсенді тауарларды көрсетсін, ал «Қалпына келтіру» әрекеті тауарды қайтарсын.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Өзгерістер журналы (audit log)

Әр баға өзгерісін `price_log(id, product_id, old_price, new_price, changed_at)` кестесіне жазатын `change_price(product_id, new_price)` функциясын жаз. Екі әрекет (UPDATE + INSERT) **бір транзакцияда** орындалсын: біреуі сәтсіз болса — екеуі де болдырылмасын. Соңында тауардың баға тарихын шығар.

Қосымша (SQL-дің өз құралы): дәл осы журналды SQLite **триггері** (`CREATE TRIGGER ... AFTER UPDATE OF price ON products`) арқылы жаса — сонда журнал Python кодынсыз-ақ толтырылады.

## Үй тапсырмасы

1. 56-сабақтағы өз каталогыңа (фильмдер/ойындар/кітаптар) өзгерту және жою мүмкіндігін қос: id бойынша жазбаны табу, кез келген өрісін өзгерту, растаумен жою.
2. Ойлан: «Оқушылар базасы» қосымшасында оқушыны шынымен жою керек пе, әлде жұмсақ жою дұрыс па? Себебін жаз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

Барлық шешімдер «Дайындық» бағдарламасынан кейін іске қосылады деп есептеледі.

**1-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
queries = [
    ("1) 200-ден арзан", "SELECT name, price FROM products WHERE price < ?", (200,)),
    ("2) кеңсе 100..200", "SELECT name, price FROM products WHERE category = ? AND price BETWEEN ? AND ?",
     ("кеңсе", 100, 200)),
    ("3) тамақ/электроника", "SELECT name, price FROM products WHERE category IN (?, ?)", ("тамақ", "электроника")),
    ("4) Дәптер...", "SELECT name, price FROM products WHERE name LIKE ?", ("Дәптер%",)),
    ("5) жоқ/белгісіз", "SELECT name, stock FROM products WHERE stock = 0 OR stock IS NULL", ()),
    ("6) «л», тамақ емес", "SELECT name, category FROM products WHERE name LIKE ? AND category <> ?",
     ("%л%", "тамақ")),
]
for title, sql, params in queries:
    rows = conn.execute(sql, params).fetchall()
    print(title + ":", "; ".join(f"{a} — {b}" for a, b in rows))
conn.close()
```

Нәтижесі:

```text
1) 200-ден арзан: Дәптер 12 п. — 90; Қалам көк — 120; Қарындаш — 80; Сызғыш 30 см — 180; Су 0.5 л — 150
2) кеңсе 100..200: Қалам көк — 120; Сызғыш 30 см — 180
3) тамақ/электроника: Шырын 0.2 л — 220; Самса — 350; Бәліш — 300; Су 0.5 л — 150; Флешка 16 ГБ — 2900; Құлаққап — 4500
4) Дәптер...: Дәптер 48 п. — 250; Дәптер 12 п. — 90
5) жоқ/белгісіз: Қарындаш — 0; Бәліш — 0; Құлаққап — None
6) «л», тамақ емес: Қалам көк — кеңсе; Флешка 16 ГБ — электроника; Құлаққап — электроника
```

**2-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
product_id = int(input("Тауар id: "))
new_price = int(input("Жаңа баға: "))

row = conn.execute("SELECT name, price FROM products WHERE id = ?", (product_id,)).fetchone()
if row is None:
    print("Мұндай тауар жоқ")
else:
    conn.execute("UPDATE products SET price = ? WHERE id = ?", (new_price, product_id))
    conn.commit()
    print(f"{row[0]}: {row[1]} ₸ → {new_price} ₸. Сақталды.")
conn.close()
```

**3-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
product_id = int(input("Тауар id: "))
qty = int(input("Саны: "))

cur = conn.execute(
    "UPDATE products SET stock = stock - ? WHERE id = ? AND stock >= ?",
    (qty, product_id, qty),
)
if cur.rowcount == 1:
    conn.commit()
    left = conn.execute("SELECT stock FROM products WHERE id = ?", (product_id,)).fetchone()[0]
    print("Сатылды, қалды:", left)
else:
    row = conn.execute("SELECT stock FROM products WHERE id = ?", (product_id,)).fetchone()
    print("Мұндай тауар жоқ" if row is None else f"Қоймада тек {row[0] or 0} дана бар")
conn.close()
```

Бір сұраныста тексеріп-азайту маңызды: екі кассир бір уақытта соңғы тауарды сатса, екеуі де «бар» деп көріп, қойма теріс санға түспейді.

**4-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
cur = conn.execute("UPDATE products SET price = CAST(ROUND(price * 0.9) AS INTEGER) WHERE category = ?", ("кеңсе",))
conn.commit()
print("Жеңілдік:", cur.rowcount, "тауар")
for name, price in conn.execute("SELECT name, price FROM products WHERE category = ?", ("кеңсе",)):
    print(f"{name}: {price} ₸")
conn.close()
```

Нәтижесі (бастапқы қормен):

```text
Жеңілдік: 5 тауар
Дәптер 48 п.: 225 ₸
Дәптер 12 п.: 81 ₸
Қалам көк: 108 ₸
Қарындаш: 72 ₸
Сызғыш 30 см: 162 ₸
```

**5-тапсырма**

```python
import sqlite3

conn = sqlite3.connect("shop.db")
rows = conn.execute("SELECT id, name FROM products WHERE stock = 0").fetchall()
if not rows:
    print("Жоятын тауар жоқ")
else:
    print("Жойылатындар:", ", ".join(name for _, name in rows))
    if input("Жоюды растайсыз ба? (иә/жоқ): ").strip().lower() == "иә":
        cur = conn.execute("DELETE FROM products WHERE stock = 0")
        conn.commit()
        print("Жойылды:", cur.rowcount)
    else:
        print("Болдырылмады")
conn.close()
```

**6-тапсырма**

```python
import sqlite3


def safe_update(conn, sql, params, expected_max=1):
    """Әсер еткен жазбалар саны күтілгеннен көп болса — rollback."""
    cur = conn.execute(sql, params)
    if cur.rowcount > expected_max:
        conn.rollback()
        print(f"Қауіп! {cur.rowcount} жазба өзгерер еді (күтілгені ≤ {expected_max}). Болдырылмады.")
        return False
    conn.commit()
    print(f"Сақталды: {cur.rowcount} жазба")
    return True


conn = sqlite3.connect("shop.db")
safe_update(conn, "UPDATE products SET price = ? WHERE id = ?", (260, 1))
safe_update(conn, "UPDATE products SET price = ?", (1,))            # WHERE ұмытылды!
print(conn.execute("SELECT COUNT(*) FROM products WHERE price = 1").fetchone()[0], "тауардың бағасы 1 ₸")
conn.close()
```

Нәтижесі:

```text
Сақталды: 1 жазба
Қауіп! 11 жазба өзгерер еді (күтілгені ≤ 1). Болдырылмады.
0 тауардың бағасы 1 ₸
```

**7-тапсырма**

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


sql, params = build_query(category="кеңсе", max_price="150")  # нақты бағдарламада: input() арқылы
print("SQL:", sql)
print("Параметрлер:", params)
conn = sqlite3.connect("shop.db")
for name, price in conn.execute(sql, params):
    print(f"{name} — {price} ₸")
conn.close()
```

Нәтижесі (бастапқы қормен):

```text
SQL: SELECT name, price FROM products WHERE category = ? AND price <= ?
Параметрлер: ['кеңсе', 150]
Дәптер 12 п. — 90 ₸
Қалам көк — 120 ₸
Қарындаш — 80 ₸
```

Маңызды: SQL жолына тек **біз жазған** шарт үзінділері қосылады, пайдаланушы мәндері — тек `params` арқылы.

**8-тапсырма**

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


print("Белсенді:", active_count())
soft_delete(4)
print("Қарындаш «жойылғаннан» кейін:", active_count())
restore(4)
print("Қалпына келтірілгеннен кейін:", active_count())
conn.close()
```

Нәтижесі:

```text
Белсенді: 11
Қарындаш «жойылғаннан» кейін: 10
Қалпына келтірілгеннен кейін: 11
```

**9-тапсырма**

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
        print("Тауар жоқ")
        return
    try:
        with conn:   # екі әрекет бір транзакцияда
            conn.execute("UPDATE products SET price = ? WHERE id = ?", (new_price, product_id))
            conn.execute(
                "INSERT INTO price_log (product_id, old_price, new_price, changed_at) VALUES (?, ?, ?, ?)",
                (product_id, row[0], new_price, datetime.now().isoformat(timespec="seconds")),
            )
    except sqlite3.IntegrityError as e:
        print("Болдырылмады:", e)


change_price(7, 380)
change_price(7, 400)
change_price(7, -5)      # CHECK (price > 0) → екі әрекет те болдырылмайды
for old, new in conn.execute("SELECT old_price, new_price FROM price_log WHERE product_id = ?", (7,)):
    print(f"{old} → {new}")
conn.close()
```

Нәтижесі (бастапқы қормен):

```text
Болдырылмады: CHECK constraint failed: price > 0
350 → 380
380 → 400
```

Триггер нұсқасы:

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
