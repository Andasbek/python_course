# Урок 61. Практические задания: экспорт в CSV и резервное копирование

> Во всех заданиях используется `school.db` из теории (таблица `students`: id, name, grade, city, score). Сначала запустите из теории код «База данных готова».

## Базовый уровень

### Задание 1. Полный экспорт
Экспортируйте таблицу `students` в файл `students_export.csv`. Разделитель — `;`, кодировка — `utf-8-sig`, первая строка — названия столбцов. Откройте файл в Excel/Calc и убедитесь, что казахские буквы отображаются правильно.

Пример вывода:
```text
В файл students_export.csv записано строк: 4
```

### Задание 2. Экспорт по классам
Запросите у пользователя номер класса (`input`) и запишите только учеников этого класса в файл `grade_<N>.csv`. Если ученики не найдены, не создавайте файл, а выведите сообщение.

```text
Номер класса: 9
Учеников сохранено в grade_9.csv: 2
```
```text
Номер класса: 7
В 7-м классе нет учеников
```

### Задание 3. Копия с отметкой времени
Напишите функцию `make_backup(db_path)`: она создаёт папку `backups/` (если её нет), делает копию с именем `school_ГГГГ-ММ-ДД_ЧЧ-ММ-СС.db` и возвращает её путь. Используйте способ `Connection.backup()`.

### Задание 4. Проверка копии
Функция `check_backup(path)` должна открыть копию и вывести результат `PRAGMA integrity_check` и количество строк в таблице `students`.

```text
backups/school_2027-01-28_15-42-07.db: целостность — ok, число записей — 4
```

### Задание 5. SQL-дамп
Сохраните базу в файл `school_dump.sql`. Затем создайте из дампа базу `restored.db` и выведите количество записей в ней.

## Продвинутый уровень

### Задание 6. Импорт с валидацией
Создайте сами файл `new_students.csv` (не менее 5 строк, 2 из них ошибочные: текст вместо класса, пустое имя). Функция импорта должна добавить правильные строки, пропустить ошибочные и выдать отчёт:

```text
Добавлено: 3
Пропущено: 2 (строки: 3, 5)
```

### Задание 7. Ротация
Вызовите функцию `make_backup` 6 раз (с `time.sleep(1)` между вызовами), затем с помощью `rotate_backups(keep=3)` оставьте только последние 3 копии. Выведите на экран список оставшихся файлов.

### Задание 8. Экспорт всех таблиц
В базе может быть несколько таблиц. Получите названия всех таблиц из `sqlite_master` (`SELECT name FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%'`) и запишите каждую таблицу в файл `export/<таблица>.csv`.

## ★ Олимпиадная / творческая задача

### Задание 9. «Машина времени»
Создайте консольное меню:
```text
1 — Создать копию
2 — Список копий (дата, размер в КБ, число записей)
3 — Восстановить из выбранной копии
0 — Выход
```
Перед восстановлением автоматически создавайте копию и самой текущей базы («страховочная сетка»).

### Задание 10. ZIP-архив
Напишите функцию, которая помещает базу, её CSV-экспорт и SQL-дамп в один архив `zipfile.ZipFile` (`backup_ГГГГ-ММ-ДД.zip`). Сравните размер архива и размер исходных файлов.

## Домашнее задание

1. Добавьте в своё CRUD-приложение из урока 60 пункты «Экспорт CSV» и «Backup».
2. Составьте план по правилу 3-2-1 для важных файлов на своём компьютере (проекты, фото): что, где, как часто.
3. Подготовка к следующему уроку: повторите виджеты Tkinter `Entry`, `Button`, `ttk.Treeview`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import csv
import sqlite3

with sqlite3.connect("school.db") as conn:
    cur = conn.execute("SELECT * FROM students")
    headers = [c[0] for c in cur.description]
    rows = cur.fetchall()

with open("students_export.csv", "w", newline="", encoding="utf-8-sig") as f:
    w = csv.writer(f, delimiter=";")
    w.writerow(headers)
    w.writerows(rows)
print(f"В файл students_export.csv записано строк: {len(rows)}")
```

**Задание 2**
```python
import csv
import sqlite3

grade = int(input("Номер класса: "))
with sqlite3.connect("school.db") as conn:
    cur = conn.execute("SELECT name, city, score FROM students WHERE grade = ?", (grade,))
    headers = [c[0] for c in cur.description]
    rows = cur.fetchall()

if not rows:
    print(f"В {grade}-м классе нет учеников")
else:
    path = f"grade_{grade}.csv"
    with open(path, "w", newline="", encoding="utf-8-sig") as f:
        w = csv.writer(f, delimiter=";")
        w.writerow(headers)
        w.writerows(rows)
    print(f"Учеников сохранено в {path}: {len(rows)}")
```

**Задания 3–4**
```python
import sqlite3
from datetime import datetime
from pathlib import Path

def make_backup(db_path, backup_dir="backups"):
    Path(backup_dir).mkdir(exist_ok=True)
    stamp = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
    target = Path(backup_dir) / f"{Path(db_path).stem}_{stamp}.db"
    src = sqlite3.connect(db_path)
    dst = sqlite3.connect(target)
    with dst:
        src.backup(dst)
    dst.close()
    src.close()
    return target

def check_backup(path):
    with sqlite3.connect(path) as conn:
        ok = conn.execute("PRAGMA integrity_check").fetchone()[0]
        n = conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]
    print(f"{path}: целостность — {ok}, число записей — {n}")

b = make_backup("school.db")
check_backup(b)
```

**Задание 5**
```python
import sqlite3
from pathlib import Path

with sqlite3.connect("school.db") as conn, open("school_dump.sql", "w", encoding="utf-8") as f:
    for line in conn.iterdump():
        f.write(line + "\n")

Path("restored.db").unlink(missing_ok=True)   # чтобы при повторном запуске таблица не дублировалась
with sqlite3.connect("restored.db") as conn:
    conn.executescript(Path("school_dump.sql").read_text(encoding="utf-8"))
    n = conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]
print("Восстановлено записей:", n)
```

**Задание 6** (шаблон `new_students.csv`: `name;grade;city;score`)
```python
import csv
import sqlite3

good, skipped = [], []
with open("new_students.csv", encoding="utf-8-sig", newline="") as f:
    reader = csv.DictReader(f, delimiter=";")
    for line_no, r in enumerate(reader, start=2):   # строка 1 — заголовок
        try:
            name = r["name"].strip()
            if not name:
                raise ValueError("пустое имя")
            good.append((name, int(r["grade"]), r["city"].strip(), float(r["score"])))
        except (ValueError, KeyError, AttributeError):
            skipped.append(line_no)

with sqlite3.connect("school.db") as conn:
    conn.executemany("INSERT INTO students (name, grade, city, score) VALUES (?, ?, ?, ?)", good)

print("Добавлено:", len(good))
print(f"Пропущено: {len(skipped)} (строки: {', '.join(map(str, skipped))})")
```

**Задание 7** (`make_backup` из задания 3)
```python
import time
from pathlib import Path

def rotate_backups(backup_dir="backups", keep=3):
    files = sorted(Path(backup_dir).glob("*.db"))
    for f in files[:-keep]:
        f.unlink()

# for _ in range(6):
#     make_backup("school.db")
#     time.sleep(1)
rotate_backups(keep=3)
for f in sorted(Path("backups").glob("*.db")):
    print(f.name)
```

**Задание 8**
```python
import csv
import sqlite3
from pathlib import Path

Path("export").mkdir(exist_ok=True)
with sqlite3.connect("school.db") as conn:
    tables = [r[0] for r in conn.execute(
        "SELECT name FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%'")]
    for t in tables:
        cur = conn.execute(f'SELECT * FROM "{t}"')
        with open(f"export/{t}.csv", "w", newline="", encoding="utf-8-sig") as f:
            w = csv.writer(f, delimiter=";")
            w.writerow([c[0] for c in cur.description])
            w.writerows(cur)
        print("Экспортировано:", t)
```

**Задание 9** (сокращённый образец)
```python
import sqlite3
from datetime import datetime
from pathlib import Path

DB = "school.db"
BK = Path("backups")

def backup(src_path, dst_path):
    src, dst = sqlite3.connect(src_path), sqlite3.connect(dst_path)
    with dst:
        src.backup(dst)
    src.close()
    dst.close()

def make_backup():
    BK.mkdir(exist_ok=True)
    target = BK / f"school_{datetime.now():%Y-%m-%d_%H-%M-%S}.db"
    backup(DB, target)
    return target

def list_backups():
    files = sorted(BK.glob("*.db"))
    for i, f in enumerate(files, 1):
        with sqlite3.connect(f) as c:
            n = c.execute("SELECT COUNT(*) FROM students").fetchone()[0]
        print(f"{i}. {f.name}  {f.stat().st_size / 1024:.1f} КБ  записей: {n}")
    return files

while True:
    choice = input("1-копия, 2-список, 3-восстановить, 0-выход: ")
    if choice == "1":
        print("Создано:", make_backup())
    elif choice == "2":
        list_backups()
    elif choice == "3":
        files = list_backups()
        k = int(input("Номер: "))
        safety = make_backup()
        print("Страховочная копия:", safety)
        backup(files[k - 1], DB)
        print("Восстановлено")
    elif choice == "0":
        break
```

**Задание 10**
```python
import zipfile
from datetime import date
from pathlib import Path

files = ["school.db", "students_export.csv", "school_dump.sql"]
name = f"backup_{date.today():%Y-%m-%d}.zip"
with zipfile.ZipFile(name, "w", compression=zipfile.ZIP_DEFLATED) as z:
    for f in files:
        z.write(f)
total = sum(Path(f).stat().st_size for f in files)
print(f"Исходные файлы: {total} байт, архив: {Path(name).stat().st_size} байт")
```

</details>
