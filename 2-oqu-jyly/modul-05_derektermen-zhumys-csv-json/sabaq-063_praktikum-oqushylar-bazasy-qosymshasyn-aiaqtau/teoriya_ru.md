# Урок 63. Практикум: завершение и защита приложения «База учеников». Самостоятельная работа №4.

## Темы

1. Работа с выбранной строкой
2. Расширение класса `StudentDB`
3. Подтверждение опасного действия
4. Кнопки экспорта и backup
5. Чек-лист тестирования программы
6. Оформление проекта: структура и README
7. Краткая шпаргалка: CSV, JSON, SQLite
8. Частые ошибки
9. Основные понятия

## 1. Работа с выбранной строкой

Для изменения и удаления сначала нужно узнать, какого ученика пользователь выбрал в таблице:

```python
def get_selected_id(tree):
    """Возвращает ID выбранного ученика или None."""
    selected = tree.selection()          # кортеж iid выбранных строк
    if not selected:
        return None
    values = tree.item(selected[0], "values")
    return int(values[0])                # столбец 0 — ID
```

Отслеживание события выбора — при щелчке по строке данные загружаются в форму:

```python
# self.tree.bind("<<TreeviewSelect>>", self.on_select)
#
# def on_select(self, event):
#     sel = self.tree.selection()
#     if not sel:
#         return
#     values = self.tree.item(sel[0], "values")
#     self.clear_form()
#     for key, value in zip(("name", "grade", "city", "phone", "score"), values[1:]):
#         self.entries[key].insert(0, value)
```

## 2. Расширение класса `StudentDB`

```python
import sqlite3


class StudentDB:
    def __init__(self, path="students.db"):
        self.conn = sqlite3.connect(path)
        self.conn.execute("""CREATE TABLE IF NOT EXISTS students (
            id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT NOT NULL,
            grade INTEGER NOT NULL, city TEXT, phone TEXT, score REAL)""")

    def add(self, name, grade, city, phone, score):
        with self.conn:
            return self.conn.execute(
                "INSERT INTO students (name, grade, city, phone, score) VALUES (?, ?, ?, ?, ?)",
                (name, grade, city, phone, score)).lastrowid

    def update(self, sid, name, grade, city, phone, score):
        with self.conn:
            cur = self.conn.execute(
                "UPDATE students SET name=?, grade=?, city=?, phone=?, score=? WHERE id=?",
                (name, grade, city, phone, score, sid))
        return cur.rowcount            # число изменённых строк

    def delete(self, sid):
        with self.conn:
            return self.conn.execute("DELETE FROM students WHERE id=?", (sid,)).rowcount

    def search(self, text):
        pattern = f"%{text}%"
        return self.conn.execute(
            "SELECT id, name, grade, city, phone, score FROM students "
            "WHERE name LIKE ? OR city LIKE ? ORDER BY name",
            (pattern, pattern)).fetchall()

    def stats(self):
        return self.conn.execute(
            "SELECT COUNT(*), ROUND(AVG(score), 1), MAX(score) FROM students").fetchone()


db = StudentDB(":memory:")
a = db.add("Айгерім", 9, "Алматы", "", 92.5)
db.add("Нұрлан", 10, "Астана", "", 78)
db.update(a, "Айгерім Сәрсенова", 9, "Алматы", "", 94)
print(db.search("Алм"))
print(db.search("алм"))
print(db.stats())
print("Удалено:", db.delete(a))
```

Результат:
```text
[(1, 'Айгерім Сәрсенова', 9, 'Алматы', '', 94.0)]
[]
(2, 86.0, 94.0)
Удалено: 1
```

**Примечание:** в SQLite `LIKE` нечувствителен к регистру для латинских букв, но для кириллицы — **чувствителен**. Поэтому поиск «Алм» нашёл «Алматы», а «алм» вернул пустой список. Пользователь вполне может написать строчными буквами. Решение: сравнение через `lower()` в Python или регистрация собственной функции:

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.create_function("PYLOWER", 1, lambda s: s.lower() if s else s)
conn.execute("CREATE TABLE t (name TEXT)")
conn.execute("INSERT INTO t VALUES ('Алматы')")
print(conn.execute("SELECT name FROM t WHERE PYLOWER(name) LIKE ?", ("%алм%",)).fetchall())
```

Результат:
```text
[('Алматы',)]
```

Поэтому в приложении в методе `search` пишем `WHERE PYLOWER(name) LIKE ? OR PYLOWER(city) LIKE ?` и передаём `pattern = f"%{text.lower()}%"`.

## 3. Подтверждение опасного действия

Удаление — необратимое действие. Обязательно спрашиваем пользователя:

```python
# from tkinter import messagebox
# if messagebox.askyesno("Подтверждение", f"Удалить ученика «{name}»?"):
#     self.db.delete(sid)
#     self.refresh()
```

**Правило UX:** перед необратимым действием — подтверждение; после действия — сообщение о результате (в строке статуса).

## 4. Кнопки экспорта и backup

`filedialog` позволяет пользователю выбрать, куда сохранить файл:

```python
# from tkinter import filedialog
# path = filedialog.asksaveasfilename(defaultextension=".csv",
#                                     filetypes=[("CSV-файлы", "*.csv")],
#                                     initialfile="students.csv")
# if path:   # если пользователь не нажал «Отмена»
#     n = self.db.export_csv(path)
```

В класс `StudentDB` добавляются методы `export_csv(path)` (экспорт в CSV) и `backup(folder)` (резервная копия через `Connection.backup()`).

## 5. Чек-лист тестирования программы

Ручное тестирование — проверка программы по конкретным сценариям. Для каждого сценария заранее записывается правильное поведение:

| № | Сценарий | Правильное поведение |
|---|---|---|
| 1 | Правильно заполнить все поля и нажать «Добавить» | В таблице новая строка, статус обновился |
| 2 | Пустое имя | Сообщение об ошибке, база не изменилась |
| 3 | Класс = 0 или «десять» | Сообщение об ошибке |
| 4 | «Удалить», ничего не выбрав | «Сначала выберите ученика» |
| 5 | Выбрать, «Удалить» → «Нет» | Ничего не удалилось |
| 6 | Выбрать, изменить, «Сохранить» | Изменилось в таблице и сохранилось после повторного открытия |
| 7 | Поиск «алм» | Только ученики из Алматы |
| 8 | Очистить поле поиска | Снова видны все ученики |
| 9 | «Экспорт CSV» | Файл правильно открылся в Excel с казахскими буквами |
| 10 | «Backup» | В `backups/` появился файл с временем в имени |

## 6. Оформление проекта: структура и README

Готовый проект должен легко запускаться на другом компьютере. Типичная структура:

```
student_base/
├── app.py            # интерфейс (Tkinter)
├── db.py             # слой данных (класс StudentDB)
├── requirements.txt  # внешние зависимости (если есть)
├── README.md         # описание проекта
├── .gitignore        # students.db, backups/, __pycache__/
└── backups/          # резервные копии (не попадают в Git)
```

Минимальный `README.md` содержит:

1. **Название и назначение** — одно-два предложения о том, что делает программа.
2. **Возможности** — список функций (добавление, поиск, изменение, удаление, экспорт, backup).
3. **Запуск** — версия Python и команда: `python app.py`.
4. **Структура** — за что отвечает каждый файл.
5. **Скриншот** интерфейса (по желанию).

Точка входа оформляется через проверку `__name__` — тогда модуль можно импортировать, не открывая окно:

```python
import tkinter as tk


def main():
    root = tk.Tk()
    root.title("База учеников")
    root.mainloop()


if __name__ == "__main__":
    main()
```

## 7. Краткая шпаргалка: CSV, JSON, SQLite

```python
import csv
import json
import sqlite3

rows = [{"name": "Айгерим", "score": 95}, {"name": "Нурлан", "score": 88}]

# CSV: запись и чтение
with open("data.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "score"])
    writer.writeheader()
    writer.writerows(rows)
with open("data.csv", encoding="utf-8") as f:
    data = list(csv.DictReader(f))   # значения — строки (str)!

# JSON: запись и чтение
with open("data.json", "w", encoding="utf-8") as f:
    json.dump(rows, f, ensure_ascii=False, indent=2)
with open("data.json", encoding="utf-8") as f:
    rows = json.load(f)

# SQLite: CRUD с параметрами
con = sqlite3.connect(":memory:")
con.execute("CREATE TABLE students (id INTEGER PRIMARY KEY, name TEXT, score INTEGER)")
con.executemany("INSERT INTO students (name, score) VALUES (?, ?)",
                [(r["name"], r["score"]) for r in rows])
con.execute("UPDATE students SET score = ? WHERE id = ?", (97, 1))
con.execute("DELETE FROM students WHERE score < ?", (50,))
for row in con.execute("SELECT name, score FROM students ORDER BY score DESC"):
    print(row)
con.commit()
con.close()
```

| Команда SQL | Назначение |
|---|---|
| `CREATE TABLE` | Создание таблицы |
| `INSERT` / `SELECT` / `UPDATE` / `DELETE` | Операции CRUD |
| `ORDER BY` | Сортировка |
| `GROUP BY` + `COUNT`/`AVG` | Группировка и агрегаты |
| `JOIN ... ON` | Объединение таблиц по ключу |

## Частые ошибки

**1. Выбор не проверен:**
```python
# Неправильно: если ничего не выбрано — IndexError
# values = self.tree.item(self.tree.selection()[0], "values")
# Правильно:
# sel = self.tree.selection()
# if not sel:
#     messagebox.showwarning("Предупреждение", "Сначала выберите ученика")
#     return
```

**2. Значения `tree.item(...)["values"]` могут быть текстом или числом.** Всегда преобразуйте ID через `int()`.

**3. При изменении забыт `WHERE id=?`** — все ученики получат одинаковое имя. Это самая опасная ошибка! Поэтому перед тестированием сделайте backup.

**4. При нажатии «Отмена» в `filedialog` возвращается пустая строка `""`** — чтобы не возникла ошибка `open("")`, нужна проверка `if path:`.

**5. После поиска таблица остаётся «пустой»** — если поле поиска пустое, должен вызываться `get_all()`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `tree.selection()` | Выбранные строки в Treeview |
| `<<TreeviewSelect>>` | Виртуальное событие, возникающее при выборе строки |
| `rowcount` | Число строк, изменённых последним `UPDATE`/`DELETE` |
| `create_function` | Регистрация функции Python в SQLite |
| `askyesno` | Окно подтверждения «Да/Нет» |
| `filedialog` | Диалоговое окно открытия/сохранения файла |
| Чек-лист тестирования | Список проверяемых сценариев |
| UX | Пользовательский опыт — удобство использования |

## Примечание

- `tree.selection()` возвращает кортеж идентификаторов строк (iid), а не значения; значения получают через `tree.item(iid, "values")`.
- `messagebox.askyesno()` возвращает `True`/`False`; другие варианты — `askokcancel` и `askyesnocancel` (последний может вернуть и `None`) (https://docs.python.org/3/library/tkinter.messagebox.html).
- `filedialog.asksaveasfilename()` только возвращает путь — сам файл не создаёт; при нажатии «Отмена» возвращается пустая строка.
- После `UPDATE`/`DELETE` атрибут `cursor.rowcount` показывает, сколько строк реально изменилось, — удобно проверять, существовала ли запись с данным `id`.
- Событие `<<TreeviewSelect>>` возникает и при программном изменении выделения (`selection_set`) — учитывайте это, чтобы не попасть в бесконечный цикл.
