# Урок 60. Связь базы данных с приложением на Python. Операции CRUD.

## Темы

1. Что такое CRUD?
2. Слои приложения
3. Класс-репозиторий
4. Валидация и обработка ошибок
5. Консольное CRUD-приложение
6. Тот же репозиторий — в окне Tkinter
7. Тестирование репозитория
8. Частые ошибки
9. Основные понятия

## 1. Что такое CRUD?

**CRUD** — сокращённое название четырёх основных операций с данными. Любое приложение для работы с данными (kundelik.kz, Kaspi, Instagram, телефонная книга) основано на этих четырёх операциях.

| Операция | Значение | SQL | Пример (Instagram) |
|---|---|---|---|
| **C**reate | создание, добавление | `INSERT` | опубликовать новый пост |
| **R**ead | чтение, поиск | `SELECT` | просмотр ленты |
| **U**pdate | изменение | `UPDATE` | исправить описание поста |
| **D**elete | удаление | `DELETE` | удалить пост |

## 2. Слои приложения

Если SQL-запросы разбросаны между обработчиками кнопок и вызовами `input()`, код трудно читать, исправлять и тестировать. Поэтому приложение делят на **слои**:

```text
┌────────────────────────────────────────┐
│  Интерфейс (UI)                         │  консольное меню / окно Tkinter
│  input(), print(), Button, Entry        │  ← общается с пользователем, не знает SQL
├────────────────────────────────────────┤
│  Логика (проверки, правила)             │  валидация: имя не пустое, балл 0..100
├────────────────────────────────────────┤
│  Доступ к данным (репозиторий)          │  StudentRepository: add, get, update, delete
│  sqlite3, SQL-запросы                   │  ← SQL есть только здесь
├────────────────────────────────────────┤
│  База данных (school.db)                │
└────────────────────────────────────────┘
```

**Аналогия:** ресторан. Официант (интерфейс) принимает заказ, но не готовит еду. Повар (логика + репозиторий) работает на кухне. Склад (база данных) — место, где хранятся продукты. Посетитель не заходит на склад — всё через официанта.

Польза:
- интерфейс легко заменить: если перейти с консоли на Tkinter, репозиторий **не меняется**;
- SQL в одном месте — легко найти и исправить ошибку;
- репозиторий можно **тестировать** без интерфейса.

## 3. Класс-репозиторий

**Репозиторий** — класс, собирающий в одном месте все SQL-запросы для работы с одной таблицей (или сущностью).

```python
import sqlite3


class StudentRepository:
    """Класс, выполняющий операции CRUD с таблицей students."""

    def __init__(self, db_path="students.db"):
        self.conn = sqlite3.connect(db_path)
        self.conn.row_factory = sqlite3.Row
        self.conn.execute("PRAGMA foreign_keys = ON")
        self._create_table()

    def _create_table(self):
        self.conn.execute("""
            CREATE TABLE IF NOT EXISTS students (
                id INTEGER PRIMARY KEY,
                name TEXT NOT NULL,
                class TEXT NOT NULL,
                score INTEGER CHECK (score BETWEEN 0 AND 100),
                email TEXT UNIQUE
            )
        """)
        self.conn.commit()

    # ---------- C: Create ----------
    def add(self, name, cls, score=None, email=None):
        """Добавляет нового ученика, возвращает его id."""
        cur = self.conn.execute(
            "INSERT INTO students (name, class, score, email) VALUES (?, ?, ?, ?)",
            (name, cls, score, email),
        )
        self.conn.commit()
        return cur.lastrowid

    # ---------- R: Read ----------
    def get(self, student_id):
        """Возвращает одного ученика по id (словарь) или None."""
        row = self.conn.execute("SELECT * FROM students WHERE id = ?", (student_id,)).fetchone()
        return dict(row) if row else None

    def list_all(self, order_by="name"):
        """Список всех учеников. Поле сортировки — только из разрешённых."""
        if order_by not in ("id", "name", "class", "score"):
            order_by = "name"
        rows = self.conn.execute(f"SELECT * FROM students ORDER BY {order_by}")
        return [dict(r) for r in rows]

    def search(self, text):
        """Поиск по имени (по части имени)."""
        rows = self.conn.execute("SELECT * FROM students WHERE name LIKE ? ORDER BY name", (f"%{text}%",))
        return [dict(r) for r in rows]

    # ---------- U: Update ----------
    def update(self, student_id, **fields):
        """Изменяет указанные поля. True — изменено, False — ученика нет."""
        allowed = {"name", "class", "score", "email"}
        fields = {k: v for k, v in fields.items() if k in allowed}
        if not fields:
            return False
        assignments = ", ".join(f"{k} = ?" for k in fields)
        cur = self.conn.execute(
            f"UPDATE students SET {assignments} WHERE id = ?",
            (*fields.values(), student_id),
        )
        self.conn.commit()
        return cur.rowcount == 1

    # ---------- D: Delete ----------
    def delete(self, student_id):
        """Удаляет ученика. True — удалён, False — не найден."""
        cur = self.conn.execute("DELETE FROM students WHERE id = ?", (student_id,))
        self.conn.commit()
        return cur.rowcount == 1

    def count(self):
        return self.conn.execute("SELECT COUNT(*) FROM students").fetchone()[0]

    def close(self):
        self.conn.close()


repo = StudentRepository(":memory:")
a = repo.add("Айгерим", "9А", 95, "aigerim@mail.kz")
b = repo.add("Бауыржан", "9А", 78)
c = repo.add("Даулет", "9Б", 88)
print("Добавлены:", a, b, c, "| всего:", repo.count())
print("Чтение:", repo.get(a))
print("Изменение:", repo.update(b, score=82, **{"class": "9Б"}))
print("Новые значения:", repo.get(b)["class"], repo.get(b)["score"])
print("Поиск «ау»:", [s["name"] for s in repo.search("ау")])
print("Удаление:", repo.delete(c), "| повторное удаление:", repo.delete(c))
print("По баллу:", [(s["name"], s["score"]) for s in repo.list_all("score")])
repo.close()
```

Результат:

```text
Добавлены: 1 2 3 | всего: 3
Чтение: {'id': 1, 'name': 'Айгерим', 'class': '9А', 'score': 95, 'email': 'aigerim@mail.kz'}
Изменение: True
Новые значения: 9Б 82
Поиск «ау»: ['Бауыржан', 'Даулет']
Удаление: True | повторное удаление: False
По баллу: [('Бауыржан', 82), ('Айгерим', 95)]
```

Обрати внимание:
- `class` в Python — ключевое слово, поэтому мы передали его как аргумент в виде `**{"class": "9Б"}` (или можно назвать его в методе `cls`);
- в частях `ORDER BY` и `SET` имена полей подставлены через f-строку, но **только из списка разрешённых** (`allowed`) — текст пользователя никогда не попадает в SQL напрямую;
- значения — всегда через `?`.

## 4. Валидация и обработка ошибок

Ограничения БД (`NOT NULL`, `CHECK`, `UNIQUE`) — последний рубеж защиты. Но показывать пользователю `CHECK constraint failed: score BETWEEN 0 AND 100` — плохо. Поэтому мы проверяем данные **заранее** и выдаём понятное сообщение.

```python
import sqlite3


class ValidationError(Exception):
    """Ошибка, возникающая при неверных введённых данных."""


def validate_student(name, cls, score):
    """Проверяет данные и возвращает очищенные значения."""
    name = name.strip()
    cls = cls.strip().upper()
    if not name:
        raise ValidationError("Имя не должно быть пустым")
    if len(name) > 50:
        raise ValidationError("Имя слишком длинное (не более 50 символов)")
    if not cls:
        raise ValidationError("Укажите класс")
    if score == "":
        return name, cls, None
    if not score.isdigit() or not 0 <= int(score) <= 100:
        raise ValidationError("Балл должен быть целым числом от 0 до 100")
    return name, cls, int(score)


tests = [("  Айгерим ", "9а", "95"), ("", "9А", "80"), ("Ерлан", "10А", "150"), ("Сымбат", "9Б", "")]
for raw in tests:
    try:
        print("OK:", validate_student(*raw))
    except ValidationError as e:
        print("Ошибка:", e)

# «Перевод» ошибок БД
conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE s (email TEXT UNIQUE)")
conn.execute("INSERT INTO s VALUES ('a@mail.kz')")
try:
    conn.execute("INSERT INTO s VALUES ('a@mail.kz')")
except sqlite3.IntegrityError as e:
    message = "Этот email уже зарегистрирован" if "UNIQUE" in str(e) else "Ошибка базы данных"
    print("Пользователю:", message)
conn.close()
```

Результат:

```text
OK: ('Айгерим', '9А', 95)
Ошибка: Имя не должно быть пустым
Ошибка: Балл должен быть целым числом от 0 до 100
OK: ('Сымбат', '9Б', None)
Пользователю: Этот email уже зарегистрирован
```

`ValidationError` — наш собственный класс ошибки (наследуется от `Exception` — модуль II). Он сигнализирует интерфейсу: «это ошибка пользователя, покажи её ему».

## 5. Консольное CRUD-приложение

Подключаем репозиторий к консольному меню. Обрати внимание, что в этом файле **нет ни одной строки SQL**:

```python
# app_console.py — слой интерфейса.
# Считаем, что в файле repo.py записаны StudentRepository из раздела 3
# и ValidationError, validate_student из раздела 4.
from repo import StudentRepository, ValidationError, validate_student

MENU = """
=== База учеников ===
1. Все ученики
2. Добавить ученика
3. Поиск
4. Изменить
5. Удалить
0. Выход"""


def print_table(students):
    if not students:
        print("Список пуст")
        return
    print(f"{'id':<4}{'Имя':<15}{'Класс':<8}{'Балл':<5}")
    for s in students:
        score = "-" if s["score"] is None else s["score"]
        print(f"{s['id']:<4}{s['name']:<15}{s['class']:<8}{score:<5}")


def ask_id(prompt="id ученика: "):
    text = input(prompt).strip()
    return int(text) if text.isdigit() else None


def main(repo):
    while True:
        print(MENU)
        choice = input("Выбор: ").strip()
        if choice == "0":
            break
        elif choice == "1":
            print_table(repo.list_all())
        elif choice == "2":
            try:
                name, cls, score = validate_student(input("Имя: "), input("Класс: "), input("Балл: "))
                new_id = repo.add(name, cls, score)
                print(f"Добавлен, id = {new_id}")
            except ValidationError as e:
                print("Ошибка:", e)
        elif choice == "3":
            print_table(repo.search(input("Часть имени: ")))
        elif choice == "4":
            student_id = ask_id()
            student = repo.get(student_id) if student_id else None
            if student is None:
                print("Такого ученика нет")
                continue
            print("Если оставить пустым — не изменится")
            name = input(f"Имя [{student['name']}]: ") or student["name"]
            cls = input(f"Класс [{student['class']}]: ") or student["class"]
            score = input(f"Балл [{student['score']}]: ") or str(student["score"] or "")
            try:
                name, cls, score = validate_student(name, cls, score)
                repo.update(student_id, name=name, score=score, **{"class": cls})
                print("Сохранено")
            except ValidationError as e:
                print("Ошибка:", e)
        elif choice == "5":
            student_id = ask_id()
            student = repo.get(student_id) if student_id else None
            if student and input(f"Удалить {student['name']}? (да/нет): ") == "да":
                repo.delete(student_id)
                print("Удалено")
            elif student is None:
                print("Такого ученика нет")
        else:
            print("Выбери пункт меню")

if __name__ == "__main__":
    repository = StudentRepository("students.db")
    main(repository)
    repository.close()
```

Пример работы:

```text
=== База учеников ===
...
Выбор: 2
Имя: Мадина
Класс: 9б
Балл: 91
Добавлен, id = 4
Выбор: 1
id  Имя            Класс   Балл
1   Айгерим        9А      95
4   Мадина         9Б      91
```

## 6. Тот же репозиторий — в окне Tkinter

Меняем интерфейс, а репозиторий остаётся прежним. `ttk.Treeview` — виджет списка в виде таблицы:

```python
import sqlite3
import tkinter as tk
from tkinter import messagebox, ttk


class MiniRepo:
    """Небольшой репозиторий для примера (в реальном проекте — StudentRepository)."""

    def __init__(self):
        self.conn = sqlite3.connect("students_gui.db")
        self.conn.execute("CREATE TABLE IF NOT EXISTS students "
                          "(id INTEGER PRIMARY KEY, name TEXT NOT NULL, class TEXT NOT NULL)")

    def add(self, name, cls):
        self.conn.execute("INSERT INTO students (name, class) VALUES (?, ?)", (name, cls))
        self.conn.commit()

    def list_all(self):
        return self.conn.execute("SELECT id, name, class FROM students ORDER BY name").fetchall()

    def delete(self, student_id):
        self.conn.execute("DELETE FROM students WHERE id = ?", (student_id,))
        self.conn.commit()


repo = MiniRepo()
root = tk.Tk()
root.title("База учеников")

form = tk.Frame(root)
form.pack(padx=10, pady=10, fill="x")
tk.Label(form, text="Имя:").grid(row=0, column=0)
name_entry = tk.Entry(form)
name_entry.grid(row=0, column=1)
tk.Label(form, text="Класс:").grid(row=0, column=2)
class_entry = tk.Entry(form, width=6)
class_entry.grid(row=0, column=3)

tree = ttk.Treeview(root, columns=("id", "name", "class"), show="headings", height=8)
for col, title in (("id", "id"), ("name", "Имя"), ("class", "Класс")):
    tree.heading(col, text=title)
tree.pack(padx=10, fill="both", expand=True)


def refresh():
    tree.delete(*tree.get_children())
    for row in repo.list_all():
        tree.insert("", "end", values=row)


def on_add():
    name, cls = name_entry.get().strip(), class_entry.get().strip()
    if not name or not cls:
        messagebox.showwarning("Ошибка", "Заполните имя и класс")
        return
    repo.add(name, cls)
    name_entry.delete(0, "end")
    refresh()


def on_delete():
    selected = tree.selection()
    if selected and messagebox.askyesno("Подтверждение", "Удалить выбранного ученика?"):
        student_id = tree.item(selected[0])["values"][0]
        repo.delete(student_id)
        refresh()


tk.Button(form, text="Добавить", command=on_add).grid(row=0, column=4, padx=5)
tk.Button(root, text="Удалить выбранного", command=on_delete).pack(pady=10)
refresh()
root.mainloop()
```

Здесь тоже: кнопка → функция интерфейса → метод репозитория → SQL. Функции интерфейса (`on_add`, `on_delete`) не знают SQL.

## 7. Тестирование репозитория

Ещё одна польза разделения на слои — репозиторий можно проверять **автоматически**. База данных `:memory:` даёт чистую базу для каждого теста:

```python
import sqlite3


class TaskRepository:
    """Небольшой репозиторий для задач To-Do."""

    def __init__(self, path=":memory:"):
        self.conn = sqlite3.connect(path)
        self.conn.execute("CREATE TABLE IF NOT EXISTS tasks "
                          "(id INTEGER PRIMARY KEY, title TEXT NOT NULL, done INTEGER DEFAULT 0)")

    def add(self, title):
        cur = self.conn.execute("INSERT INTO tasks (title) VALUES (?)", (title,))
        self.conn.commit()
        return cur.lastrowid

    def mark_done(self, task_id):
        cur = self.conn.execute("UPDATE tasks SET done = 1 WHERE id = ?", (task_id,))
        self.conn.commit()
        return cur.rowcount == 1

    def pending(self):
        return [r[0] for r in self.conn.execute("SELECT title FROM tasks WHERE done = 0 ORDER BY id")]


def test_add_and_pending():
    repo = TaskRepository()
    repo.add("Домашнее задание")
    repo.add("README проекта")
    assert repo.pending() == ["Домашнее задание", "README проекта"]


def test_mark_done():
    repo = TaskRepository()
    task_id = repo.add("Git push")
    assert repo.mark_done(task_id) is True
    assert repo.pending() == []
    assert repo.mark_done(999) is False


for test in (test_add_and_pending, test_mark_done):
    test()
    print(f"{test.__name__}: пройден")
```

Результат:

```text
test_add_and_pending: пройден
test_mark_done: пройден
```

`assert условие` — если условие ложно, возбуждает `AssertionError`. В профессиональных проектах такие тесты запускает инструмент `pytest`.

## Частые ошибки

**1. SQL разбросан по коду интерфейса.** `conn.execute("INSERT ...")` в обработчике кнопки — потом трудно исправлять. Правильно: весь SQL — в репозитории.

**2. Открывать новое соединение в каждой операции и не закрывать его.** Десятки открытых соединений → `database is locked`. Правильно: одно соединение (в объекте репозитория), в конце программы — `close()`.

**3. `commit()` только в конце программы.** Если программа упадёт — все изменения потеряются. Правильно: `commit()` после каждой изменяющей операции.

**4. Подстановка имени поля от пользователя прямо в SQL.**

```python
# ОПАСНО: order_by пришёл от пользователя
# conn.execute(f"SELECT * FROM students ORDER BY {order_by}")
# ПРАВИЛЬНО: проверка по списку разрешённых
order_by = "score; DROP TABLE students"
if order_by not in ("id", "name", "class", "score"):
    order_by = "name"
print("Используемая сортировка:", order_by)
```

Результат:

```text
Используемая сортировка: name
```

**5. Запись результата `input()` в БД без проверки.** `int("десять")` → `ValueError`, и программа падает. Правильно: функция валидации.

**6. Путаница `None` и пустой строки.** Если балл не введён, в БД нужно записывать не `""`, а `None` (`NULL`) — иначе `AVG` и `CHECK` будут работать неправильно.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| CRUD | Create, Read, Update, Delete — 4 основные операции с данными |
| Слой (layer) | Часть приложения, отвечающая за одну задачу: интерфейс, логика, данные |
| Репозиторий | Класс, собирающий все SQL-запросы для работы с одной таблицей |
| Валидация | Проверка правильности введённых данных |
| Собственный класс ошибки | Класс-наследник `Exception` (`ValidationError`) |
| `ttk.Treeview` | Виджет таблицы/списка в Tkinter |
| `assert` | Оператор, проверяющий условие (в тестировании) |
| `:memory:` | Временная база данных, удобная для тестов |

## Примечание

- Соединение с БД лучше открывать один раз на всё время работы приложения (в репозитории) и закрывать при выходе, а не подключаться заново в каждом методе.
- `with conn:` фиксирует или откатывает транзакцию, но **не закрывает** соединение; для закрытия используйте `conn.close()` или `contextlib.closing(conn)`.
- `conn.row_factory = sqlite3.Row` позволяет обращаться к полям по имени (`row["title"]`) и легко превращать строку в словарь: `dict(row)`.
- Для тестов используйте базу `":memory:"` — она создаётся заново для каждого теста и не портит рабочие данные; модуль `unittest` или библиотека `pytest` запускают такие тесты автоматически.
- `assert` в рабочем коде не годится для валидации: при запуске `python -O` все `assert` отключаются. Для проверки данных пользователя выбрасывайте `ValueError`.
- Документация: https://docs.python.org/3/library/sqlite3.html и https://docs.python.org/3/library/tkinter.ttk.html#treeview
