# Урок 61. Экспорт данных в CSV и создание резервной копии (backup).

## Темы

1. Зачем экспортировать в CSV?
2. SQLite → CSV: основной шаблон
3. CSV → SQLite: обратный импорт
4. Что такое резервная копия (backup)?
5. Три способа создания копии в SQLite
6. Ротация копий
7. Проверка восстановления
8. Частые ошибки
9. Основные понятия

## 1. Зачем экспортировать в CSV?

База данных удобна для программы, но большинство людей не умеют открывать файл `.db`. CSV (Comma-Separated Values) — текстовый формат, который читает любая табличная программа.

| Цель | Пример |
|---|---|
| Отчётность | Открыть список учеников в Excel и распечатать |
| Перенос в другую систему | Передача данных в Google Sheets, в Pandas (VII модуль) |
| Простой архив | Текстовый файл можно будет прочитать и через десятки лет |
| Анализ данных | CSV → Pandas → графики Matplotlib |

## 2. SQLite → CSV: основной шаблон

Сначала создадим учебную базу данных:

```python
import sqlite3

conn = sqlite3.connect("school.db")
cur = conn.cursor()
cur.execute("""
    CREATE TABLE IF NOT EXISTS students (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        grade INTEGER,
        city TEXT,
        score REAL
    )
""")
cur.execute("DELETE FROM students")  # очищаем таблицу для учебного примера
data = [
    ("Айгерім Сәрсенова", 9, "Алматы", 92.5),
    ("Нұрлан Жақсылықов", 10, "Астана", 78.0),
    ("Дана Омарова", 9, "Шымкент", 88.0),
    ("Ержан Бекұлы", 11, "Алматы", 95.5),
]
cur.executemany("INSERT INTO students (name, grade, city, score) VALUES (?, ?, ?, ?)", data)
conn.commit()
conn.close()
print("База данных готова")
```

Теперь выполним экспорт:

```python
import csv
import sqlite3

def export_to_csv(db_path, table, csv_path):
    """Записывает все строки таблицы в CSV-файл, возвращает число записанных строк."""
    with sqlite3.connect(db_path) as conn:
        cur = conn.execute(f"SELECT * FROM {table}")
        headers = [col[0] for col in cur.description]  # названия столбцов
        rows = cur.fetchall()
    with open(csv_path, "w", newline="", encoding="utf-8-sig") as f:
        writer = csv.writer(f, delimiter=";")
        writer.writerow(headers)
        writer.writerows(rows)
    return len(rows)

count = export_to_csv("school.db", "students", "students.csv")
print(f"Экспортировано строк: {count}")
```

Результат:
```text
Экспортировано строк: 4
```

Содержимое `students.csv`:
```text
id;name;grade;city;score
1;Айгерім Сәрсенова;9;Алматы;92.5
2;Нұрлан Жақсылықов;10;Астана;78.0
3;Дана Омарова;9;Шымкент;88.0
4;Ержан Бекұлы;11;Алматы;95.5
```

### Важные подробности

- **`cur.description`** — список кортежей с информацией о столбцах последнего `SELECT`. Нулевой элемент каждого кортежа — название столбца. Поэтому код продолжит работать, даже если структура таблицы изменится.
- **`newline=""`** — обязателен, чтобы в Windows между строками не появлялись пустые строки.
- **`encoding="utf-8-sig"`** — добавляет в начало файла метку BOM (Byte Order Mark). Увидев эту метку, Excel правильно отображает казахские буквы (ә, ғ, қ, ң, ө, ұ, ү, һ, і). При обычной `utf-8` Excel иногда выводит «кракозябры».
- **`delimiter=";"`** — русско-/казахоязычный Excel чаще всего ожидает в качестве разделителя не запятую, а точку с запятой (поскольку в дробных числах используется запятая). Для Pandas или Google Sheets лучше стандартная `","`.

### Экспорт с фильтрацией

Можно экспортировать не всю таблицу, а только нужные строки:

```python
import csv
import sqlite3

def export_query(db_path, query, params, csv_path):
    with sqlite3.connect(db_path) as conn:
        cur = conn.execute(query, params)
        headers = [c[0] for c in cur.description]
        rows = cur.fetchall()
    with open(csv_path, "w", newline="", encoding="utf-8-sig") as f:
        writer = csv.writer(f)
        writer.writerow(headers)
        writer.writerows(rows)
    return len(rows)

n = export_query(
    "school.db",
    "SELECT name AS 'ФИО', score AS 'Балл' FROM students WHERE city = ? ORDER BY score DESC",
    ("Алматы",),
    "almaty.csv",
)
print("Ученики из Алматы:", n)
```

Результат:
```text
Ученики из Алматы: 2
```

Псевдоним `AS` задаёт заголовку CSV название столбца на русском языке.

### DictWriter и sqlite3.Row

```python
import csv
import sqlite3

with sqlite3.connect("school.db") as conn:
    conn.row_factory = sqlite3.Row          # делает строки похожими на словари
    rows = conn.execute("SELECT name, grade FROM students").fetchall()

with open("names.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "grade"])
    writer.writeheader()
    for r in rows:
        writer.writerow(dict(r))
print("Готово")
```

## 3. CSV → SQLite: обратный импорт

«Близнец» экспорта — импорт. Например, новых учеников ввели в Excel и передали в виде CSV:

```python
import csv
import sqlite3

def import_from_csv(db_path, csv_path):
    added = 0
    with open(csv_path, encoding="utf-8-sig", newline="") as f:
        reader = csv.DictReader(f, delimiter=";")
        rows = [(r["name"], int(r["grade"]), r["city"], float(r["score"])) for r in reader]
    with sqlite3.connect(db_path) as conn:
        conn.executemany(
            "INSERT INTO students (name, grade, city, score) VALUES (?, ?, ?, ?)", rows
        )
        added = len(rows)
    return added
```

Примечание: при импорте нужна **проверка данных** (валидация) — пустые строки, текст вместо числа и т.п. Чтобы ошибочная строка не обрушила весь импорт, используем `try/except ValueError`.

## 4. Что такое резервная копия (backup)?

**Резервная копия** — копия данных, создаваемая для восстановления в случае их потери, повреждения или ошибочного изменения.

Причины потери данных: поломка диска/флешки, ошибочная команда (`DELETE` без WHERE), вирус-шифровальщик (ransomware), кража ноутбука, ошибка в программе.

### Правило 3-2-1

| Число | Значение |
|---|---|
| **3** | не менее 3 экземпляров данных (оригинал + 2 копии) |
| **2** | не менее чем на 2 разных носителях (диск ноутбука + флешка) |
| **1** | 1 копия в другом месте (облако: Google Drive, GitHub и т.п.) |

## 5. Три способа создания копии в SQLite

### Способ 1. Копирование файла (`shutil.copy2`)

База данных SQLite — это просто один файл, поэтому его можно скопировать:

```python
import shutil
from datetime import datetime
from pathlib import Path

def simple_backup(db_path, backup_dir="backups"):
    Path(backup_dir).mkdir(exist_ok=True)
    stamp = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
    target = Path(backup_dir) / f"{Path(db_path).stem}_{stamp}.db"
    shutil.copy2(db_path, target)  # copy2 сохраняет и время изменения
    return target

print("Копия:", simple_backup("school.db"))
```

Результат (время каждый раз другое):
```text
Копия: backups/school_2027-01-28_15-42-07.db
```

**Недостаток:** если именно в этот момент другая программа записывает данные в базу, копия может оказаться повреждённой. Поэтому перед копированием нужно закрыть все подключения.

### Способ 2. `Connection.backup()` — самый надёжный

```python
import sqlite3

def safe_backup(db_path, target_path):
    src = sqlite3.connect(db_path)
    dst = sqlite3.connect(target_path)
    with dst:
        src.backup(dst)   # копирует правильно, даже если база в работе
    dst.close()
    src.close()

safe_backup("school.db", "school_backup.db")
print("Безопасная копия создана")
```

Этот метод использует собственный «онлайн backup API» SQLite: даже при открытом подключении копия получается целостной (консистентной).

### Способ 3. SQL-дамп (`iterdump`)

```python
import sqlite3

with sqlite3.connect("school.db") as conn:
    with open("school_dump.sql", "w", encoding="utf-8") as f:
        for line in conn.iterdump():
            f.write(line + "\n")
print("SQL-дамп сохранён")
```

Дамп — текстовый файл, состоящий из команд `CREATE TABLE` и `INSERT`. Преимущество: его можно читать, класть в Git, переносить в другую СУБД. Восстановление:

```python
import sqlite3

with open("school_dump.sql", encoding="utf-8") as f:
    script = f.read()
with sqlite3.connect("restored.db") as conn:
    conn.executescript(script)
print("База восстановлена из дампа")
```

### Сравнение

| Способ | Преимущество | Недостаток |
|---|---|---|
| `shutil.copy2` | Очень простой | Может повредиться во время записи |
| `conn.backup()` | Надёжный, быстрый | Результат — двоичный файл |
| `iterdump()` | Читается человеком, удобен для Git | Медленный и объёмный для большой базы |
| Экспорт в CSV | Открывается в Excel | Только данные, структура (типы, ключи) не сохраняется |

## 6. Ротация копий

Если создавать копию при каждом запуске, папка быстро переполнится. **Ротация** — сохранение только последних N копий и удаление старых.

```python
from pathlib import Path

def rotate_backups(backup_dir="backups", keep=5):
    files = sorted(Path(backup_dir).glob("*.db"))   # в имени есть дата — сортируется по алфавиту
    old = files[:-keep] if len(files) > keep else []
    for f in old:
        f.unlink()
    return len(old)

print("Удалено:", rotate_backups(keep=3))
```

Поскольку дата в имени записана в формате `ГГГГ-ММ-ДД_ЧЧ-ММ-СС`, сортировка по тексту совпадает с сортировкой по времени — это удобный трюк.

## 7. Проверка восстановления

«Непроверенная копия — не копия». Проверим, что копия открывается и количество строк правильное:

```python
import sqlite3

def verify_backup(path, table="students"):
    try:
        with sqlite3.connect(path) as conn:
            ok = conn.execute("PRAGMA integrity_check").fetchone()[0]
            count = conn.execute(f"SELECT COUNT(*) FROM {table}").fetchone()[0]
        return ok == "ok", count
    except sqlite3.DatabaseError:
        return False, 0

print(verify_backup("school_backup.db"))
```

Результат:
```text
(True, 4)
```

## Частые ошибки

**1. Забыт `newline=""`** — в Windows в CSV после каждой строки появляется пустая строка.
```python
# Неправильно:
f = open("out.csv", "w", encoding="utf-8")
# Правильно:
f = open("out.csv", "w", newline="", encoding="utf-8")
f.close()
```

**2. Excel портит казахские буквы** — используйте `utf-8-sig` вместо `utf-8`.

**3. Нет строки заголовков** — если не записать `writer.writerow(headers)`, человек, открывший файл, не поймёт смысла столбцов.

**4. Запись копии поверх того же файла:**
```python
import shutil
# Неправильно: каждая копия стирает предыдущую, и «хорошая» копия теряется под ошибочными данными
# shutil.copy("school.db", "backup.db")
# Правильно: добавить в имя отметку времени
from datetime import datetime
name = f"backup_{datetime.now():%Y%m%d_%H%M%S}.db"
print(name)
```

**5. Получение имени таблицы от пользователя и подстановка в f-строку** — риск SQL-инъекции. Проверяйте имя таблицы по списку разрешённых: `if table not in ("students", "grades"): raise ValueError`.

**6. Хранение копии только на том же диске** — если диск выйдет из строя, пропадут и оригинал, и копия (помните правило 3-2-1).

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Экспорт | Вывод данных из программы во внешний формат (CSV, JSON) |
| Импорт | Ввод данных из внешнего файла в программу |
| `cursor.description` | Описание столбцов последнего запроса |
| BOM / `utf-8-sig` | Метка в начале файла, чтобы Excel распознал кодировку |
| Резервная копия (backup) | Копия данных, предназначенная для восстановления |
| SQL-дамп | Текстовый файл с SQL-командами, воссоздающими базу |
| Ротация | Хранение только последних N копий и удаление старых |
| Правило 3-2-1 | 3 экземпляра, 2 носителя, 1 внешнее место |
| `PRAGMA integrity_check` | Команда проверки целостности базы SQLite |

## Примечание

- При работе с модулем `csv` открывайте файл с параметром `newline=""` — иначе в Windows между строками появятся пустые строки (https://docs.python.org/3/library/csv.html).
- Для Excel в русской/казахской локали удобны разделитель `;` и кодировка `utf-8-sig`; для обмена между программами (Pandas, Google Sheets) — стандартные `,` и `utf-8`.
- Метод `Connection.backup()` есть начиная с Python 3.7; в отличие от простого копирования файла, он делает корректную копию даже во время записи в базу.
- Файлы `-wal` и `-journal` рядом с `.db` — часть базы; при ручном копировании файла их нельзя игнорировать (поэтому `backup()` надёжнее).
- Имя файла с отметкой времени формируйте как `datetime.now().strftime("%Y-%m-%d_%H-%M-%S")`: в Windows символ `:` в именах файлов запрещён, а формат ГГГГ-ММ-ДД правильно сортируется.
