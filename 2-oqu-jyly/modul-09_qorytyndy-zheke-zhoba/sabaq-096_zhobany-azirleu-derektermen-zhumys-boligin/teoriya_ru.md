# Урок 96. Разработка проекта: реализация части работы с данными.

## Темы

1. Слой данных: почему отдельный модуль?
2. Пример проекта: `database.py`
3. SQL-инъекция: почему используем только `?`
4. Связывание логики со слоем данных
5. Надёжное сохранение в JSON (игра, викторина, настройки)
6. Экспорт в CSV (для Excel и Pandas)
7. Данные из API: timeout, ошибка, кэш
8. Проверка: действительно ли данные сохраняются?
9. Частые ошибки
10. Основные понятия

## 1. Слой данных: почему отдельный модуль?

В многослойной архитектуре программа состоит из трёх слоёв:

```text
┌────────────────────────────┐
│  Интерфейс (gui.py)        │   общается с пользователем
├────────────────────────────┤
│  Логика (logic.py)         │   вычисляет, проверяет правила
├────────────────────────────┤
│  Данные (database.py,      │   сохраняет и загружает:
│  storage.py, currency.py)  │   SQLite, JSON, CSV, API
└────────────────────────────┘
```

**Правило:** SQL-запросы, `open()` и `requests.get()` находятся **только** в слое данных. Интерфейс не пишет SQL; логика не открывает файлы. Тогда, если заменить SQLite другой БД или JSON на SQLite, изменится только один модуль.

Этот подход называется **шаблоном «Хранилище»** (Repository pattern): класс-хранилище предоставляет методы «сохрани операцию», «дай операции», а то, как именно данные хранятся внутри, скрывает (инкапсуляция).

## 2. Пример проекта: `database.py`

```python
"""database.py — база данных «Карманных денег»."""
import sqlite3

SCHEMA = """
CREATE TABLE IF NOT EXISTS category (
    id    INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL UNIQUE,
    kind  TEXT NOT NULL CHECK (kind IN ('доход', 'расход'))
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
    ("Родители", "доход"), ("Подарок", "доход"),
    ("Еда", "расход"), ("Транспорт", "расход"), ("Развлечения", "расход"),
]


class Database:
    """Класс-хранилище для работы с SQLite."""

    def __init__(self, path="qalta.db"):
        self.conn = sqlite3.connect(path)
        self.conn.row_factory = sqlite3.Row      # доступ к столбцу по имени
        self.conn.execute("PRAGMA foreign_keys = ON")
        self.conn.executescript(SCHEMA)
        self._fill_defaults()

    def _fill_defaults(self):
        """При первом запуске добавляет категории по умолчанию."""
        count = self.conn.execute("SELECT COUNT(*) FROM category").fetchone()[0]
        if count == 0:
            with self.conn:
                self.conn.executemany(
                    "INSERT INTO category (title, kind) VALUES (?, ?)",
                    DEFAULT_CATEGORIES)

    # ---------- Create ----------
    def add_operation(self, category_id, amount, op_date, note=""):
        """Добавляет операцию и возвращает её id."""
        with self.conn:      # при успехе commit, при ошибке rollback
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
        """Операции (с названием категории). month: 'ГГГГ-ММ' или None."""
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
    food = db.categories("расход")[0]["id"]
    new_id = db.add_operation(food, 800, "2027-04-27", "Обед")
    db.add_operation(food, 500, "2027-04-28")
    db.update_operation(new_id, 900, "Обед + чай")
    for op in db.operations("2027-04"):
        print(op["op_date"], op["category"], op["amount"], op["note"])
    print("Удалено?", db.delete_operation(new_id), db.delete_operation(999))
    try:
        db.add_operation(food, -100, "2027-04-28")
    except sqlite3.IntegrityError as err:
        print("БД не приняла:", err)
    db.close()
```

Результат:
```text
2027-04-28 Еда 500 
2027-04-27 Еда 900 Обед + чай
Удалено? True False
БД не приняла: CHECK constraint failed: amount > 0
```

Важные моменты:
- **`with self.conn:`** — транзакция: если блок завершился успешно, автоматически выполняется `commit`, при ошибке — `rollback` (не остаётся наполовину записанных данных).
- **`sqlite3.Row`** — можно получить значение по имени: `row["amount"]`; `dict(row)` — превращение в обычный словарь (удобно передавать в интерфейс).
- **`cur.lastrowid`** — `id` новой записи; **`cur.rowcount`** — число изменённых строк (найдена запись или нет).
- Ограничение `CHECK` — последний рубеж защиты; основная проверка — в логике и интерфейсе.

## 3. SQL-инъекция: почему используем только `?`

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE users (login TEXT, password TEXT);
INSERT INTO users VALUES ('admin', 'qwerty123');
""")

login = "кто угодно' OR '1'='1"     # вредоносный ввод
# НЕПРАВИЛЬНО: вставлять текст в SQL через f-строку
bad_sql = f"SELECT * FROM users WHERE login = '{login}'"
print("f-строка:", conn.execute(bad_sql).fetchall())
# ПРАВИЛЬНО: передавать как параметр
good = conn.execute("SELECT * FROM users WHERE login = ?", (login,)).fetchall()
print("параметр:", good)
```

Результат:
```text
f-строка: [('admin', 'qwerty123')]
параметр: []
```

Через f-строку пользователь изменил смысл SQL и получил все данные. При использовании `?` ввод рассматривается только как **значение**. Это базовое правило информационной безопасности при работе с любой SQL-базой.

## 4. Связывание логики со слоем данных

Логика не знает внутреннего устройства БД, она лишь вызывает методы хранилища. Хранилище **передаём в логику как параметр** — тогда в тесте вместо настоящего файла можно передать БД `:memory:`.

```python
class Wallet:
    """Основная логика: получает данные из хранилища."""

    def __init__(self, db):
        self.db = db

    def add_expense(self, category_id, amount, op_date, note=""):
        if amount <= 0:
            raise ValueError("Сумма должна быть положительной")
        return self.db.add_operation(category_id, amount, op_date, note)

    def balance(self, month=None):
        total = 0
        for op in self.db.operations(month):
            total += op["amount"] if op["kind"] == "доход" else -op["amount"]
        return total


class FakeDB:
    """Фиктивное хранилище для теста (без БД)."""

    def __init__(self):
        self.rows = []

    def add_operation(self, category_id, amount, op_date, note=""):
        kind = "доход" if category_id == 1 else "расход"
        self.rows.append({"amount": amount, "kind": kind, "op_date": op_date})
        return len(self.rows)

    def operations(self, month=None):
        return [r for r in self.rows if month is None or r["op_date"].startswith(month)]


w = Wallet(FakeDB())
w.db.add_operation(1, 3000, "2027-04-01")
w.add_expense(3, 800, "2027-04-02")
print("Баланс:", w.balance("2027-04"))
```

Результат:
```text
Баланс: 2200
```

`Wallet` одинаково работает и с настоящей `Database`, и с `FakeDB` — это практическая польза **полиморфизма** («утиной типизации»: важны методы объекта, а не его класс).

## 5. Надёжное сохранение в JSON (игра, викторина, настройки)

При работе с файлом учитываем три случая: файла **нет** (первый запуск), файл **повреждён** (изменён вручную), **программа прервалась** во время записи.

```python
import json
import os

DEFAULTS = {"player": "Игрок", "volume": 0.7, "records": []}


def load_json(path, defaults):
    """Читает файл; если его нет или он повреждён — значения по умолчанию."""
    try:
        with open(path, encoding="utf-8") as f:
            data = json.load(f)
    except FileNotFoundError:
        return dict(defaults)
    except json.JSONDecodeError:
        os.replace(path, path + ".broken")     # сохраняем повреждённый файл
        return dict(defaults)
    return {**defaults, **data}                # работает, даже если добавятся новые ключи


def save_json(path, data):
    """Сначала пишет во временный файл, затем заменяет (атомарная запись)."""
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

Результат:
```text
{'name': 'Ерасыл', 'score': 520}
```

`os.replace` заменяет файл одним действием: даже если программа закроется посреди записи, старый файл останется целым.

## 6. Экспорт в CSV (для Excel и Pandas)

```python
import csv
import io

rows = [
    {"op_date": "2027-04-27", "category": "Еда", "amount": 900, "note": "Обед"},
    {"op_date": "2027-04-28", "category": "Транспорт", "amount": 200, "note": ""},
]


def export_csv(rows, file):
    """Записывает операции в CSV. file — объект открытого файла."""
    writer = csv.DictWriter(file, fieldnames=["op_date", "category", "amount", "note"],
                            delimiter=";")
    writer.writeheader()
    writer.writerows(rows)


buffer = io.StringIO()      # в настоящем проекте: open("export.csv", "w", newline="", encoding="utf-8-sig")
export_csv(rows, buffer)
print(buffer.getvalue())
```

Результат:
```text
op_date;category;amount;note
2027-04-27;Еда;900;Обед
2027-04-28;Транспорт;200;
```

`encoding="utf-8-sig"` и разделитель `;` — чтобы кириллица (в том числе казахские буквы) правильно открывалась в Excel.

## 7. Данные из API: timeout, ошибка, кэш

API — внешний сервис: он может отвечать медленно, выйти из строя, может не быть интернета. Поэтому:
1. `timeout` обязателен — чтобы программа не «зависла»;
2. перехватываем ошибку и не роняем программу;
3. последний успешный ответ храним в **кэше** — в офлайн-режиме показываем его.

```python
"""currency.py — курс валют (открытый API без ключа)."""
import json
import time

import requests

URL = "https://open.er-api.com/v6/latest/USD"
CACHE_FILE = "rates_cache.json"
CACHE_TTL = 6 * 60 * 60          # 6 часов (в секундах)


def _read_cache():
    try:
        with open(CACHE_FILE, encoding="utf-8") as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return None


def get_kzt_rate():
    """Курс 1 доллара в тенге и источник: ('api'|'cache'|None)."""
    cache = _read_cache()
    if cache and time.time() - cache["time"] < CACHE_TTL:
        return cache["rate"], "cache"
    try:
        response = requests.get(URL, timeout=5)
        response.raise_for_status()
        rate = response.json()["rates"]["KZT"]
    except (requests.RequestException, KeyError, ValueError):
        if cache:                         # кэш есть, хоть и устаревший
            return cache["rate"], "cache"
        return None, None                 # данных нет совсем
    with open(CACHE_FILE, "w", encoding="utf-8") as f:
        json.dump({"rate": rate, "time": time.time()}, f)
    return rate, "api"


if __name__ == "__main__":
    rate, source = get_kzt_rate()
    if rate is None:
        print("Курс недоступен (нет интернета). Суммы показываются только в тенге.")
    else:
        print(f"1 USD = {rate:.2f} KZT (источник: {source})")
```

Результат (пример, курс меняется):
```text
1 USD = 512.34 KZT (источник: api)
```

> Добавьте `rates_cache.json` в `.gitignore` — это временный файл. Если выберете сервис, требующий API-ключ, храните ключ в `.env` и читайте его через `os.environ` или библиотеку `python-dotenv`.

## 8. Проверка: действительно ли данные сохраняются?

Самый важный тест части данных — **тест повторного запуска**:
1. Запустить программу, добавить 2–3 записи.
2. Полностью закрыть программу.
3. Открыть снова — записи на месте?
4. Визуально проверить данные в DB Browser for SQLite (или открыв JSON-файл).
5. Особый случай: удалить файл БД/JSON и запустить программу — создаёт ли она новый без ошибок?

## Частые ошибки

**1. Забыть `commit`.** Пока программа работает, данные видны, но при повторном открытии их нет. Правильно: блок `with conn:` или `conn.commit()`.

**2. Составлять SQL через f-строку** — SQL-инъекция и ошибка на тексте с апострофом (`Д'Артаньян`). Правильно: параметры `?`.

**3. Открывать новое подключение в каждой функции и не закрывать его.**
```python
import sqlite3

def count_ops():
    conn = sqlite3.connect("qalta.db")      # при каждом вызове новое подключение, не закрывается
    return conn.execute("SELECT COUNT(*) FROM operation").fetchone()[0]
```
Объяснение: множество подключений может заблокировать файл (`database is locked`). Правильно: один объект `Database`, одно подключение, `close()` при закрытии программы.

**4. Относительный путь.** `sqlite3.connect("qalta.db")` — файл создаётся в папке, **из которой запущена программа**, а не рядом с `main.py`. Надёжный вариант:
```python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent
DB_PATH = BASE_DIR / "qalta.db"
print(DB_PATH.name)
```

**5. Вызов API без `timeout`** — при медленном интернете окно переходит в состояние «не отвечает».

**6. SQL в интерфейсе.** `conn.execute(...)` внутри `gui.py` — слои перемешались. Правильно: вызывать `db.add_operation(...)`.

## Основные понятия
| Понятие | Объяснение |
|---|---|
| Слой данных | модули, отвечающие за сохранение и загрузку |
| Шаблон «Хранилище» (Repository) | сокрытие доступа к данным за методами одного класса |
| Транзакция | группа операций, выполняемых полностью или не выполняемых вовсе |
| `sqlite3.Row` | тип строки, позволяющий обращаться к столбцу по имени |
| SQL-инъекция | атака, изменяющая смысл SQL-запроса через введённый текст |
| Параметризованный запрос | запрос, в котором значения передаются отдельно через `?` |
| Атомарная запись | замена файла одним действием через временный файл |
| Кэш | сохранённый последний результат, чтобы не запрашивать повторно |
| `timeout` | максимальное время ожидания запроса |

## Примечание

- Контекстный менеджер `with conn:` в `sqlite3` управляет только транзакцией (commit/rollback), но **не закрывает** соединение — `close()` нужно вызвать отдельно (или использовать `contextlib.closing`).
- С Python 3.12 у `sqlite3.connect()` есть параметр `autocommit`; по умолчанию сохраняется старое поведение (неявное открытие транзакций перед `INSERT`/`UPDATE`/`DELETE`).
- Объект соединения `sqlite3` по умолчанию нельзя использовать из другого потока (`check_same_thread=True`) — учитывайте это, если выносите запросы в `threading`.
- `requests` не входит в стандартную библиотеку: добавьте его в `requirements.txt`; без параметра `timeout` запрос может ждать бесконечно.
- Для путей к файлам используйте `pathlib.Path` — он одинаково работает в Windows, macOS и Linux.
- Документация: https://docs.python.org/3/library/sqlite3.html и https://docs.python.org/3/library/json.html
