# Сабақ 96. Жобаны әзірлеу: деректермен жұмыс бөлігін жүзеге асыру.

## Тақырыптар

1. Деректер қабаты: неге бөлек модуль?
2. Жоба мысалы: `database.py`
3. SQL-инъекция: неге тек `?` қолданамыз
4. Логиканы деректер қабатымен байланыстыру
5. JSON-ға сенімді сақтау (ойын, викторина, баптаулар)
6. CSV экспорт (Excel және Pandas үшін)
7. API-дан деректер: timeout, қате, кэш
8. Тексеру: деректер шынымен сақтала ма?
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Деректер қабаты: неге бөлек модуль?

Көпқабатты архитектурада бағдарлама үш қабаттан тұрады:

```text
┌────────────────────────────┐
│  Интерфейс (gui.py)        │   пайдаланушымен сөйлеседі
├────────────────────────────┤
│  Логика (logic.py)         │   есептейді, ережелерді тексереді
├────────────────────────────┤
│  Деректер (database.py,    │   сақтайды және жүктейді:
│  storage.py, currency.py)  │   SQLite, JSON, CSV, API
└────────────────────────────┘
```

**Ереже:** SQL сұраныстары, `open()` және `requests.get()` **тек** деректер қабатында болады. Интерфейс SQL жазбайды; логика файл ашпайды. Сонда SQLite-ты басқа ДҚ-ға немесе JSON-ды SQLite-қа ауыстырсақ, тек бір модуль өзгереді.

Бұл тәсіл **«Қойма» үлгісі** (Repository pattern) деп аталады: қойма класы «операцияны сақта», «операцияларды бер» деген әдістер ұсынады, ал ішінде қалай сақталатынын жасырады (инкапсуляция).

## 2. Жоба мысалы: `database.py`

```python
"""database.py — «Қалта ақшасы» деректер қоры."""
import sqlite3

SCHEMA = """
CREATE TABLE IF NOT EXISTS category (
    id    INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL UNIQUE,
    kind  TEXT NOT NULL CHECK (kind IN ('кіріс', 'шығыс'))
);
CREATE TABLE IF NOT EXISTS operation (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    category_id INTEGER NOT NULL REFERENCES category(id),
    amount      INTEGER NOT NULL CHECK (amount > 0),
    op_date     TEXT NOT NULL,
    note        TEXT DEFAULT ''
);
"""

DEFAULT_CATEGORIES = [
    ("Ата-ана", "кіріс"), ("Сыйлық", "кіріс"),
    ("Тамақ", "шығыс"), ("Көлік", "шығыс"), ("Ойын-сауық", "шығыс"),
]


class Database:
    """SQLite-пен жұмыс істейтін қойма класы."""

    def __init__(self, path="qalta.db"):
        self.conn = sqlite3.connect(path)
        self.conn.row_factory = sqlite3.Row      # бағанға атымен қол жеткізу
        self.conn.execute("PRAGMA foreign_keys = ON")
        self.conn.executescript(SCHEMA)
        self._fill_defaults()

    def _fill_defaults(self):
        """Бірінші іске қосылғанда әдепкі санаттарды қосады."""
        count = self.conn.execute("SELECT COUNT(*) FROM category").fetchone()[0]
        if count == 0:
            with self.conn:
                self.conn.executemany(
                    "INSERT INTO category (title, kind) VALUES (?, ?)",
                    DEFAULT_CATEGORIES)

    # ---------- Create ----------
    def add_operation(self, category_id, amount, op_date, note=""):
        """Операцияны қосып, оның id-ін қайтарады."""
        with self.conn:      # сәтті болса commit, қате болса rollback
            cur = self.conn.execute(
                "INSERT INTO operation (category_id, amount, op_date, note) "
                "VALUES (?, ?, ?, ?)",
                (category_id, amount, op_date, note))
        return cur.lastrowid

    # ---------- Read ----------
    def categories(self, kind=None):
        sql = "SELECT id, title, kind FROM category"
        params = ()
        if kind:
            sql += " WHERE kind = ?"
            params = (kind,)
        return [dict(row) for row in self.conn.execute(sql + " ORDER BY id", params)]

    def operations(self, month=None):
        """Операциялар (санат атауымен). month: 'ЖЖЖЖ-АА' немесе None."""
        sql = """SELECT o.id, o.amount, o.op_date, o.note,
                        c.title AS category, c.kind
                 FROM operation AS o JOIN category AS c ON c.id = o.category_id"""
        params = ()
        if month:
            sql += " WHERE o.op_date LIKE ?"
            params = (month + "-%",)
        sql += " ORDER BY o.op_date DESC, o.id DESC"
        return [dict(row) for row in self.conn.execute(sql, params)]

    # ---------- Update ----------
    def update_operation(self, op_id, amount, note):
        with self.conn:
            cur = self.conn.execute(
                "UPDATE operation SET amount = ?, note = ? WHERE id = ?",
                (amount, note, op_id))
        return cur.rowcount == 1

    # ---------- Delete ----------
    def delete_operation(self, op_id):
        with self.conn:
            cur = self.conn.execute("DELETE FROM operation WHERE id = ?", (op_id,))
        return cur.rowcount == 1

    def close(self):
        self.conn.close()


if __name__ == "__main__":
    db = Database(":memory:")
    food = db.categories("шығыс")[0]["id"]
    new_id = db.add_operation(food, 800, "2027-04-27", "Түскі ас")
    db.add_operation(food, 500, "2027-04-28")
    db.update_operation(new_id, 900, "Түскі ас + шай")
    for op in db.operations("2027-04"):
        print(op["op_date"], op["category"], op["amount"], op["note"])
    print("Жойылды ма:", db.delete_operation(new_id), db.delete_operation(999))
    try:
        db.add_operation(food, -100, "2027-04-28")
    except sqlite3.IntegrityError as err:
        print("ДҚ қабылдамады:", err)
    db.close()
```

Нәтижесі:
```text
2027-04-28 Тамақ 500 
2027-04-27 Тамақ 900 Түскі ас + шай
Жойылды ма: True False
ДҚ қабылдамады: CHECK constraint failed: amount > 0
```

Маңызды тұстар:
- **`with self.conn:`** — транзакция: блок сәтті аяқталса автоматты `commit`, қате шықса `rollback` (жартылай жазылған деректер қалмайды).
- **`sqlite3.Row`** — `row["amount"]` деп атымен алуға болады; `dict(row)` — қарапайым сөздікке айналдыру (интерфейске беру ыңғайлы).
- **`cur.lastrowid`** — жаңа жазбаның `id`-і; **`cur.rowcount`** — өзгерген жолдар саны (жазба табылды ма, жоқ па).
- `CHECK` шектеуі — қорғаудың соңғы шебі; негізгі тексеру логика мен интерфейсте.

## 3. SQL-инъекция: неге тек `?` қолданамыз

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE users (login TEXT, password TEXT);
INSERT INTO users VALUES ('admin', 'qwerty123');
""")

login = "кез келген' OR '1'='1"     # зиянды енгізу
# ҚАТЕ: мәтінді SQL-ге f-жол арқылы қосу
bad_sql = f"SELECT * FROM users WHERE login = '{login}'"
print("f-жол:", conn.execute(bad_sql).fetchall())
# ДҰРЫС: параметр ретінде беру
good = conn.execute("SELECT * FROM users WHERE login = ?", (login,)).fetchall()
print("параметр:", good)
```

Нәтижесі:
```text
f-жол: [('admin', 'qwerty123')]
параметр: []
```

f-жол арқылы пайдаланушы SQL-дің мағынасын өзгертіп, барлық деректі алды. `?` қолданғанда енгізу **мән** ретінде ғана қарастырылады. Бұл — кез келген SQL дерекқорымен жұмыс істегендегі ақпараттық қауіпсіздіктің негізгі ережесі.

## 4. Логиканы деректер қабатымен байланыстыру

Логика ДҚ-ның ішкі құрылымын білмейді, ол тек қойма әдістерін шақырады. Логикаға қойманы **параметр ретінде береміз** — сонда тестте нағыз файлдың орнына `:memory:` ДҚ-ны беруге болады.

```python
class Wallet:
    """Негізгі логика: деректерді қоймадан алады."""

    def __init__(self, db):
        self.db = db

    def add_expense(self, category_id, amount, op_date, note=""):
        if amount <= 0:
            raise ValueError("Сома оң болуы керек")
        return self.db.add_operation(category_id, amount, op_date, note)

    def balance(self, month=None):
        total = 0
        for op in self.db.operations(month):
            total += op["amount"] if op["kind"] == "кіріс" else -op["amount"]
        return total


class FakeDB:
    """Тестке арналған жалған қойма (ДҚ-сыз)."""

    def __init__(self):
        self.rows = []

    def add_operation(self, category_id, amount, op_date, note=""):
        kind = "кіріс" if category_id == 1 else "шығыс"
        self.rows.append({"amount": amount, "kind": kind, "op_date": op_date})
        return len(self.rows)

    def operations(self, month=None):
        return [r for r in self.rows if month is None or r["op_date"].startswith(month)]


w = Wallet(FakeDB())
w.db.add_operation(1, 3000, "2027-04-01")
w.add_expense(3, 800, "2027-04-02")
print("Баланс:", w.balance("2027-04"))
```

Нәтижесі:
```text
Баланс: 2200
```

`Wallet` нағыз `Database`-пен де, `FakeDB`-мен де бірдей жұмыс істейді — бұл **полиморфизмнің** («үйрек типтеу»: объектінің класы емес, әдістері маңызды) практикалық пайдасы.

## 5. JSON-ға сенімді сақтау (ойын, викторина, баптаулар)

Файлмен жұмыста үш жағдайды ескереміз: файл **жоқ** (бірінші іске қосу), файл **бүлінген** (қолмен өзгертілген), жазу кезінде **бағдарлама үзілді**.

```python
import json
import os

DEFAULTS = {"player": "Ойыншы", "volume": 0.7, "records": []}


def load_json(path, defaults):
    """Файлды оқиды; жоқ немесе бүлінген болса — әдепкі мәндер."""
    try:
        with open(path, encoding="utf-8") as f:
            data = json.load(f)
    except FileNotFoundError:
        return dict(defaults)
    except json.JSONDecodeError:
        os.replace(path, path + ".broken")     # бүлінген файлды сақтап қоямыз
        return dict(defaults)
    return {**defaults, **data}                # жаңа кілттер қосылса да жұмыс істейді


def save_json(path, data):
    """Алдымен уақытша файлға жазып, сосын ауыстырады (атомар жазу)."""
    tmp = path + ".tmp"
    with open(tmp, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)
    os.replace(tmp, path)


def add_record(data, name, score, top=5):
    data["records"].append({"name": name, "score": score})
    data["records"].sort(key=lambda r: r["score"], reverse=True)
    del data["records"][top:]


path = "demo_save.json"
data = load_json(path, DEFAULTS)
for name, score in [("Аружан", 340), ("Ерасыл", 520), ("Аружан", 410)]:
    add_record(data, name, score)
save_json(path, data)
print(load_json(path, DEFAULTS)["records"][0])
os.remove(path)
```

Нәтижесі:
```text
{'name': 'Ерасыл', 'score': 520}
```

`os.replace` — файлды бір әрекетпен ауыстырады: жазу ортасында бағдарлама жабылса да, ескі файл бүтін қалады.

## 6. CSV экспорт (Excel және Pandas үшін)

```python
import csv
import io

rows = [
    {"op_date": "2027-04-27", "category": "Тамақ", "amount": 900, "note": "Түскі ас"},
    {"op_date": "2027-04-28", "category": "Көлік", "amount": 200, "note": ""},
]


def export_csv(rows, file):
    """Операцияларды CSV-ге жазады. file — ашық файл объектісі."""
    writer = csv.DictWriter(file, fieldnames=["op_date", "category", "amount", "note"],
                            delimiter=";")
    writer.writeheader()
    writer.writerows(rows)


buffer = io.StringIO()      # нағыз жобада: open("export.csv", "w", newline="", encoding="utf-8-sig")
export_csv(rows, buffer)
print(buffer.getvalue())
```

Нәтижесі:
```text
op_date;category;amount;note
2027-04-27;Тамақ;900;Түскі ас
2027-04-28;Көлік;200;
```

`encoding="utf-8-sig"` және `;` бөлгіші — қазақ әріптері Excel-де дұрыс ашылуы үшін.

## 7. API-дан деректер: timeout, қате, кэш

API — сыртқы қызмет: ол баяу жауап беруі, істен шығуы, интернет болмауы мүмкін. Сондықтан:
1. `timeout` міндетті — бағдарлама «қатып» қалмасын;
2. қатені ұстап, бағдарламаны құлатпаймыз;
3. соңғы сәтті жауапты **кэште** сақтаймыз — офлайн режимде соны көрсетеміз.

```python
"""currency.py — валюта бағамы (кілтсіз ашық API)."""
import json
import time

import requests

URL = "https://open.er-api.com/v6/latest/USD"
CACHE_FILE = "rates_cache.json"
CACHE_TTL = 6 * 60 * 60          # 6 сағат (секундпен)


def _read_cache():
    try:
        with open(CACHE_FILE, encoding="utf-8") as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return None


def get_kzt_rate():
    """1 доллардың теңгедегі бағамы және дереккөзі: ('api'|'cache'|None)."""
    cache = _read_cache()
    if cache and time.time() - cache["time"] < CACHE_TTL:
        return cache["rate"], "cache"
    try:
        response = requests.get(URL, timeout=5)
        response.raise_for_status()
        rate = response.json()["rates"]["KZT"]
    except (requests.RequestException, KeyError, ValueError):
        if cache:                         # ескі болса да кэш бар
            return cache["rate"], "cache"
        return None, None                 # мүлде дерек жоқ
    with open(CACHE_FILE, "w", encoding="utf-8") as f:
        json.dump({"rate": rate, "time": time.time()}, f)
    return rate, "api"


if __name__ == "__main__":
    rate, source = get_kzt_rate()
    if rate is None:
        print("Бағам қолжетімсіз (интернет жоқ). Сомалар тек теңгемен көрсетіледі.")
    else:
        print(f"1 USD = {rate:.2f} KZT (дереккөз: {source})")
```

Нәтижесі (мысал, бағам өзгеріп отырады):
```text
1 USD = 512.34 KZT (дереккөз: api)
```

> `rates_cache.json`-ды `.gitignore`-ға қосыңыз — бұл уақытша файл. API кілті қажет болатын қызметті таңдасаңыз, кілтті `.env`-те сақтап, оны `os.environ` немесе `python-dotenv` кітапханасы арқылы оқыңыз.

## 8. Тексеру: деректер шынымен сақтала ма?

Деректер бөлігінің ең маңызды тесті — **қайта іске қосу тесті**:
1. Бағдарламаны іске қосып, 2–3 жазба қосу.
2. Бағдарламаны толық жабу.
3. Қайта ашу — жазбалар орнында ма?
4. DB Browser for SQLite-та (немесе JSON файлын ашып) деректерді көзбен тексеру.
5. Ерекше жағдай: ДҚ/JSON файлын өшіріп, бағдарламаны іске қосу — қатесіз жаңасын құра ма?

## Жиі кездесетін қателер

**1. `commit` ұмыту.** Бағдарлама жұмыс істеген кезде деректер көрінеді, бірақ қайта ашқанда жоқ. Дұрысы: `with conn:` блогы немесе `conn.commit()`.

**2. f-жолмен SQL құру** — SQL-инъекция және тырнақшасы бар мәтінде (`Қожа'Ахмет`) қате. Дұрысы: `?` параметрлері.

**3. Әр функцияда жаңа қосылым ашып, жаппау.**
```python
import sqlite3

def count_ops():
    conn = sqlite3.connect("qalta.db")      # әр шақыруда жаңа қосылым, жабылмайды
    return conn.execute("SELECT COUNT(*) FROM operation").fetchone()[0]
```
Түсіндірме: көп қосылым файлды құлыптап қоюы мүмкін (`database is locked`). Дұрысы: бір `Database` объектісі, бір қосылым, бағдарлама жабылғанда `close()`.

**4. Салыстырмалы жол.** `sqlite3.connect("qalta.db")` — файл бағдарлама **іске қосылған папкада** құрылады, `main.py` жанында емес. Сенімді нұсқа:
```python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent
DB_PATH = BASE_DIR / "qalta.db"
print(DB_PATH.name)
```

**5. API-ды `timeout`-сыз шақыру** — интернет баяу болса, терезе «жауап бермейді» күйіне түседі.

**6. Интерфейсте SQL жазу.** `gui.py` ішіндегі `conn.execute(...)` — қабаттар араласты. Дұрысы: `db.add_operation(...)` шақыру.

## Негізгі ұғымдар
| Ұғым | Түсіндірме |
|---|---|
| Деректер қабаты | сақтау мен жүктеуге жауапты модульдер |
| Қойма үлгісі (Repository) | деректерге қол жеткізуді бір класс әдістерінің артына жасыру |
| Транзакция | толық орындалатын немесе мүлде орындалмайтын амалдар тобы |
| `sqlite3.Row` | бағанға атымен қол жеткізуге мүмкіндік беретін жол түрі |
| SQL-инъекция | енгізілген мәтін арқылы SQL сұранысының мағынасын өзгерту шабуылы |
| Параметрленген сұраныс | мәндер `?` арқылы бөлек берілетін сұраныс |
| Атомар жазу | файлды уақытша файл арқылы бір әрекетпен ауыстыру |
| Кэш | қайта сұрамау үшін сақталған соңғы нәтиже |
| `timeout` | сұраныстың ең ұзақ күту уақыты |

## Ескерту

- `sqlite3`-тегі `with conn:` контекстік менеджері тек транзакцияны басқарады (commit/rollback), бірақ қосылымды **жаппайды** — `close()`-ты бөлек шақыру керек (немесе `contextlib.closing` қолданыңыз).
- Python 3.12-ден бастап `sqlite3.connect()`-те `autocommit` параметрі бар; әдепкі бойынша ескі мінез-құлық сақталады (`INSERT`/`UPDATE`/`DELETE` алдында транзакция жасырын ашылады).
- `sqlite3` қосылым объектісін әдепкі бойынша басқа ағыннан қолдануға болмайды (`check_same_thread=True`) — сұраныстарды `threading`-ке шығарсаңыз, мұны ескеріңіз.
- `requests` стандартты кітапханаға кірмейді: оны `requirements.txt`-ке қосыңыз; `timeout` параметрінсіз сұраныс шексіз күтуі мүмкін.
- Файл жолдары үшін `pathlib.Path` қолданыңыз — ол Windows, macOS және Linux-та бірдей жұмыс істейді.
- Құжаттама: https://docs.python.org/3/library/sqlite3.html және https://docs.python.org/3/library/json.html
