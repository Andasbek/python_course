# Урок 96. Практические задания: часть работы с данными

> Основное время — слой данных вашего проекта. В зависимости от используемой в проекте технологии выполните вариант **А** (SQLite) или **Б** (JSON) задания 1; проекты с API выполняют также задание 3.

## Базовый уровень

### Задание 1. Модуль данных (обязательно)
**А. SQLite.** Напишите в `database.py` класс `Database`:
- конструктор: подключение, `PRAGMA foreign_keys = ON`, `executescript(SCHEMA)`, при необходимости данные по умолчанию;
- для каждой основной таблицы как минимум: `add_...`, `list_...` (или `get_...`), `delete_...`; при необходимости `update_...`;
- только параметры `?`, транзакции `with self.conn:`;
- метод `close()`.

**Б. JSON.** Напишите в `storage.py` функции `load_json` / `save_json` (с обработкой случаев «файла нет» и «файл повреждён») и действия, нужные вашему проекту (добавление рекорда, сохранение настроек, загрузка вопросов и т.п.).

В конце файла блок `if __name__ == "__main__":` должен проверить все методы с БД `:memory:` (или с временным файлом).

### Задание 2. Связывание логики (обязательно)
Подключите классы логики из уроков 94–95 к слою данных: логика получает объект хранилища через конструктор и вызывает его методы. Внутри логики не должно быть `sqlite3`, `open` или `requests`.

### Задание 3. Модуль API (для проектов с API)
Вынесите работу с API в отдельный модуль (`weather.py`, `currency.py` и т.п.):
- `timeout=5`, `raise_for_status()`, перехват `requests.RequestException`;
- кэш или офлайн-режим;
- функция возвращает **данные**, а не готовый текст для пользователя.

### Задание 4. Тест повторного запуска
Выполните 5 шагов из части 8 теории и запишите результат в файл `docs/test_log.md`:

| № | Шаг | Ожидалось | Фактически | Пройден? |
|---|---|---|---|---|
| 1 | Добавить 3 записи и закрыть программу | — | — | — |
| 2 | Открыть снова | видны 3 записи | ... | да/нет |

## Продвинутый уровень

### Задание 5. Найти SQL-инъекцию
Какая опасность в коде ниже? Исправьте его и проверьте на имени с апострофом (`Д'Артаньян`).

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT, grade INTEGER)")


def add_student(name, grade):
    conn.execute(f"INSERT INTO student (name, grade) VALUES ('{name}', {grade})")
    conn.commit()


def find(name):
    return conn.execute(f"SELECT * FROM student WHERE name = '{name}'").fetchall()


add_student("Айгерим", 8)
print(find("Айгерим"))
```

### Задание 6. Резервная копия (backup)
Добавьте в класс `Database` метод `backup(folder)`: он создаёт копию БД с именем `folder/qalta_ГГГГ-ММ-ДД_ЧЧММ.db` и, если старых копий больше 5, удаляет самые старые. *Подсказка:* метод `sqlite3.Connection.backup()` безопасно копирует даже открытую БД.

### Задание 7. Импорт CSV
Напишите функцию `import_csv(path)`: она читает операции из CSV и добавляет их в БД. Ошибочные строки (сумма не число, неизвестная категория) пропускает и в конце выводит отчёт.

Пример вывода:
```text
Импортировано строк: 18, пропущено: 2 (строка 3: сумма 'сто'; строка 11: неизвестная категория 'Спорт')
```

## ★ Олимпиадная / творческая задача

### Задание 8. Обновление версии схемы (миграция)
Допустим, после выпуска проекта в таблице `operation` понадобилось новое поле `place TEXT`. Старые БД пользователей не должны пропасть. Напишите функцию `migrate(conn)`, которая хранит версию схемы через `PRAGMA user_version` и при открытии старой БД автоматически выполняет `ALTER TABLE ... ADD COLUMN`.

### Задание 9. Срок годности кэша
Улучшите функцию `get_kzt_rate()`: если курс из кэша старше 24 часов, функция должна третьим значением возвращать признак «устарел», а также возвращать время, чтобы интерфейс мог показать «Курс на 26.04.27 14:05».

## Домашнее задание
1. Завершите слой данных и проведите тест повторного запуска. `push`.
2. Проверьте, что в `.gitignore` есть файл БД и файлы кэша.
3. Откройте прототип из урока 92 и запишите списком, какая кнопка какой метод логики/хранилища вызывает — это план следующего урока.

## Критерии оценивания
| Критерий | Доля | Что проверяется |
|---|---|---|
| Данные правильно сохраняются и загружаются | 40% | тест повторного запуска пройден, CRUD работает |
| Надёжность и безопасность | 25% | параметризованные запросы, транзакции, обработаны ошибки файлов/API |
| Структура кода | 20% | слой данных отделён, в логике нет SQL/`open`, docstring |
| Самостоятельная работа, коммиты | 15% | осмысленные коммиты, `push` |

## Для педагога: решения
<details><summary>Показать решения</summary>

**Задания 1–4** проверяются индивидуально. Лист проверки: (1) `grep -n "execute(f" *.py` — должно быть пусто; (2) в файле логики нет `import sqlite3`; (3) если удалить файл БД и запустить, программа без ошибок создаёт новый; (4) при отключении интернета API-проект не падает.

**Задание 5.** Опасность: SQL-инъекция; кроме того, имя `Д'Артаньян` вызывает синтаксическую ошибку.
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT, grade INTEGER)")


def add_student(name, grade):
    with conn:
        conn.execute("INSERT INTO student (name, grade) VALUES (?, ?)", (name, grade))


def find(name):
    return conn.execute("SELECT * FROM student WHERE name = ?", (name,)).fetchall()


add_student("Айгерим", 8)
add_student("Д'Артаньян", 9)
print(find("Айгерим"))
print(find("Д'Артаньян"))
print(find("x' OR '1'='1"))
```
Результат:
```text
[(1, 'Айгерим', 8)]
[(2, "Д'Артаньян", 9)]
[]
```

**Задание 6.**
```python
import datetime
import sqlite3
from pathlib import Path


class Database:
    def __init__(self, path):
        self.conn = sqlite3.connect(path)

    def backup(self, folder, keep=5):
        """Создаёт копию БД, оставляет `keep` самых новых копий."""
        folder = Path(folder)
        folder.mkdir(parents=True, exist_ok=True)
        stamp = datetime.datetime.now().strftime("%Y-%m-%d_%H%M%S")
        target = folder / f"qalta_{stamp}.db"
        dest = sqlite3.connect(target)
        with dest:
            self.conn.backup(dest)
        dest.close()
        copies = sorted(folder.glob("qalta_*.db"))   # по времени в названии
        for old in copies[:-keep]:
            old.unlink()
        return target


if __name__ == "__main__":
    db = Database(":memory:")
    db.conn.execute("CREATE TABLE t (x INTEGER)")
    print("Копия:", db.backup("backups").name)
```
(К формату `ЧЧММ` из задания добавлены секунды — если за одну минуту создать две копии, имена не повторятся.)

**Задание 7.**
```python
import csv
import io

CATEGORIES = {"Еда": 3, "Транспорт": 4, "Развлечения": 5}


def import_csv(file, add_operation):
    """CSV (op_date;category;amount;note) -> add_operation(...). Возвращает отчёт."""
    ok, skipped = 0, []
    reader = csv.DictReader(file, delimiter=";")
    for line_no, row in enumerate(reader, start=2):     # строка 1 — заголовок
        try:
            amount = int(row["amount"])
            if amount <= 0:
                raise ValueError
        except (ValueError, TypeError):
            skipped.append(f"строка {line_no}: сумма '{row['amount']}'")
            continue
        cat_id = CATEGORIES.get(row["category"].strip())
        if cat_id is None:
            skipped.append(f"строка {line_no}: неизвестная категория '{row['category']}'")
            continue
        add_operation(cat_id, amount, row["op_date"], row.get("note", ""))
        ok += 1
    return f"Импортировано строк: {ok}, пропущено: {len(skipped)} ({'; '.join(skipped)})"


data = io.StringIO("op_date;category;amount;note\n"
                   "2027-04-01;Еда;800;обед\n"
                   "2027-04-02;Еда;сто;\n"
                   "2027-04-03;Спорт;500;\n")
saved = []
print(import_csv(data, lambda *args: saved.append(args)))
print(saved)
```
Результат:
```text
Импортировано строк: 1, пропущено: 2 (строка 3: сумма 'сто'; строка 4: неизвестная категория 'Спорт')
[(3, 800, '2027-04-01', 'обед')]
```

**Задание 8.**
```python
import sqlite3

MIGRATIONS = {
    1: "CREATE TABLE operation (id INTEGER PRIMARY KEY, amount INTEGER, op_date TEXT)",
    2: "ALTER TABLE operation ADD COLUMN place TEXT DEFAULT ''",
}


def migrate(conn):
    """Обновляет схему до последней версии."""
    version = conn.execute("PRAGMA user_version").fetchone()[0]
    for v in sorted(MIGRATIONS):
        if v > version:
            with conn:
                conn.execute(MIGRATIONS[v])
                conn.execute(f"PRAGMA user_version = {v}")   # PRAGMA не принимает параметры; v — наше число
            print(f"Схема обновлена до версии {v}")


conn = sqlite3.connect(":memory:")
migrate(conn)
migrate(conn)          # второй раз ничего не делает
cols = [c[1] for c in conn.execute("PRAGMA table_info(operation)")]
print(cols)
```
Результат:
```text
Схема обновлена до версии 1
Схема обновлена до версии 2
['id', 'amount', 'op_date', 'place']
```

**Задание 9.**
```python
import datetime
import json
import time

MAX_AGE = 24 * 60 * 60


def rate_from_cache(cache_text, now=None):
    """Возвращает (курс, текст_времени, устарел_ли)."""
    now = now or time.time()
    cache = json.loads(cache_text)
    stamp = datetime.datetime.fromtimestamp(cache["time"]).strftime("%d.%m.%y %H:%M")
    return cache["rate"], stamp, now - cache["time"] > MAX_AGE


saved = json.dumps({"rate": 512.3, "time": time.time() - 2 * MAX_AGE})
rate, stamp, stale = rate_from_cache(saved)
print(rate, stale)
```
Результат (текст времени зависит от даты запуска):
```text
512.3 True
```
В полном решении `get_kzt_rate()` возвращает эти три значения, а при успешном обращении к API `stale=False`.
</details>
