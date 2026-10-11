# Урок 60. Практические задания: CRUD-приложение

Главный проект сегодняшнего урока — приложение **«Телефонная книга»**. Оно делится на слои:

```text
contacts/
├── repo.py          # ContactRepository — весь SQL здесь
├── validators.py    # функции проверки
├── app_console.py   # консольное меню
└── contacts.db      # создаётся автоматически (добавь в .gitignore!)
```

Таблица: `contacts(id INTEGER PRIMARY KEY, name TEXT NOT NULL, phone TEXT NOT NULL UNIQUE, city TEXT, birthday TEXT, favorite INTEGER DEFAULT 0)`.

## Базовый уровень

### Задание 1. Репозиторий: Create и Read

Напиши в `repo.py` класс `ContactRepository`:
- `__init__(self, path)` — подключение, `row_factory = sqlite3.Row`, создание таблицы;
- `add(name, phone, city=None, birthday=None)` → новый `id`;
- `get(contact_id)` → словарь или `None`;
- `list_all()` → список словарей всех контактов (отсортированных по имени);
- `close()`.

Проверка:

```text
repo = ContactRepository(":memory:")
repo.add("Мама", "+77011112233", "Алматы")
repo.add("Ерлан", "+77475556677", "Шымкент")
print(repo.list_all())
→ [{'id': 2, 'name': 'Ерлан', ...}, {'id': 1, 'name': 'Мама', ...}]
```

### Задание 2. Update и Delete

Добавь в класс:
- `update(contact_id, **fields)` — изменяет только разрешённые поля (`name`, `phone`, `city`, `birthday`, `favorite`), возвращает `True/False`;
- `delete(contact_id)` → `True/False`;
- `toggle_favorite(contact_id)` — переключает поле `favorite` 0 ↔ 1 (один SQL: `SET favorite = 1 - favorite`).

### Задание 3. Валидация

В `validators.py` — класс `ValidationError` и функция `validate_contact(name, phone, birthday)`:
- имя не пустое, 2–40 символов;
- телефон: после удаления пробелов и дефисов — `+7` и 10 цифр (`+77011112233`); принимай также формат `8 701 111 22 33` и приводи его к виду `+7...`;
- дата рождения пустая или в формате `ГГГГ-ММ-ДД` (проверяй через `datetime.date.fromisoformat`).

```text
validate_contact("Алия", "8 (701) 111-22-33", "2011-05-17")
→ ('Алия', '+77011112233', '2011-05-17')
validate_contact("А", "123", "")
→ ValidationError: Имя должно содержать 2–40 символов
```

### Задание 4. Консольное меню

`app_console.py`: меню (1 — все, 2 — добавить, 3 — поиск, 4 — изменить, 5 — удалить, 6 — добавить в избранное/убрать, 0 — выход). В этом файле не должно быть ни одного SQL-запроса! Если телефон повторяется (`IntegrityError`) — выведи «Этот номер уже сохранён».

## Продвинутый уровень

### Задание 5. Поиск и фильтры

Добавь в репозиторий:
- `search(text)` — по имени **или** телефону (по части);
- `by_city(city)` — по городу;
- `favorites()` — только избранные;
- `stats()` — количество контактов по городам (`GROUP BY`).

### Задание 6. Дни рождения

Метод `upcoming_birthdays(days=30, today=None)` должен возвращать контакты, у которых день рождения в ближайшие `days` дней (отсортированные по дате). Подсказка: возьми месяц и день рождения (`birthday[5:]`) и сравни в Python с датой в этом или следующем году.

```text
today = 2027-01-26
→ [('Алия', '2027-02-03', через 8 дней), ('Ерлан', '2027-02-20', через 25 дней)]
```

### Задание 7. Тесты

Напиши в файле `test_repo.py` не менее 5 тестов (`assert`) с базой `:memory:`: добавление, чтение по id, несуществующий id, изменение, удаление, повторный телефон вызывает `IntegrityError`.

## ★ Олимпиадная / творческая задача

### Задание 8. Интерфейс Tkinter

Не меняя тот же `ContactRepository`, напиши интерфейс на Tkinter: таблица `Treeview`, форма добавления/изменения, поле поиска (фильтрация по мере ввода — событие `<KeyRelease>`), кнопка «Удалить» с подтверждением, отображение избранных значком ★. Это будет подготовкой к практикуму на уроках 62–63.

### Задание 9. Универсальный репозиторий

Напиши базовый класс `Repository(conn, table, fields)`, работающий с любой таблицей (`add(**data)`, `get(id)`, `list_all()`, `update(id, **data)`, `delete(id)`). Пусть `ContactRepository` и `StudentRepository` наследуют его (модуль II — наследование). Обеспечь безопасность имён таблиц и полей.

## Домашнее задание

1. Заверши «Телефонную книгу» и загрузи на GitHub (README: возможности, запуск, скриншот; `contacts.db` — в `.gitignore`).
2. Подумай и запиши: какие методы репозитория нужны для приложения «База учеников»? (На уроке 62 — практикум Tkinter + SQLite.)

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–2, 5: `repo.py`**

```python
import sqlite3


class ContactRepository:
    """Операции CRUD с таблицей contacts."""

    FIELDS = {"name", "phone", "city", "birthday", "favorite"}

    def __init__(self, path="contacts.db"):
        self.conn = sqlite3.connect(path)
        self.conn.row_factory = sqlite3.Row
        self.conn.execute("""
            CREATE TABLE IF NOT EXISTS contacts (
                id INTEGER PRIMARY KEY,
                name TEXT NOT NULL,
                phone TEXT NOT NULL UNIQUE,
                city TEXT,
                birthday TEXT,
                favorite INTEGER DEFAULT 0
            )
        """)
        self.conn.commit()

    def _rows(self, sql, params=()):
        return [dict(r) for r in self.conn.execute(sql, params)]

    # Create
    def add(self, name, phone, city=None, birthday=None):
        cur = self.conn.execute(
            "INSERT INTO contacts (name, phone, city, birthday) VALUES (?, ?, ?, ?)",
            (name, phone, city, birthday),
        )
        self.conn.commit()
        return cur.lastrowid

    # Read
    def get(self, contact_id):
        row = self.conn.execute("SELECT * FROM contacts WHERE id = ?", (contact_id,)).fetchone()
        return dict(row) if row else None

    def list_all(self):
        return self._rows("SELECT * FROM contacts ORDER BY name")

    def search(self, text):
        pattern = f"%{text}%"
        return self._rows("SELECT * FROM contacts WHERE name LIKE ? OR phone LIKE ? ORDER BY name",
                          (pattern, pattern))

    def by_city(self, city):
        return self._rows("SELECT * FROM contacts WHERE city = ? ORDER BY name", (city,))

    def favorites(self):
        return self._rows("SELECT * FROM contacts WHERE favorite = 1 ORDER BY name")

    def stats(self):
        return [tuple(r) for r in self.conn.execute(
            "SELECT COALESCE(city, 'неизвестно'), COUNT(*) FROM contacts GROUP BY city ORDER BY COUNT(*) DESC")]

    # Update
    def update(self, contact_id, **fields):
        fields = {k: v for k, v in fields.items() if k in self.FIELDS}
        if not fields:
            return False
        sql = "UPDATE contacts SET " + ", ".join(f"{k} = ?" for k in fields) + " WHERE id = ?"
        cur = self.conn.execute(sql, (*fields.values(), contact_id))
        self.conn.commit()
        return cur.rowcount == 1

    def toggle_favorite(self, contact_id):
        cur = self.conn.execute("UPDATE contacts SET favorite = 1 - favorite WHERE id = ?", (contact_id,))
        self.conn.commit()
        return cur.rowcount == 1

    # Delete
    def delete(self, contact_id):
        cur = self.conn.execute("DELETE FROM contacts WHERE id = ?", (contact_id,))
        self.conn.commit()
        return cur.rowcount == 1

    def close(self):
        self.conn.close()


if __name__ == "__main__":
    repo = ContactRepository(":memory:")
    repo.add("Мама", "+77011112233", "Алматы")
    e = repo.add("Ерлан", "+77475556677", "Шымкент")
    repo.add("Дана", "+77027778899", "Алматы")
    repo.toggle_favorite(e)
    print([c["name"] for c in repo.list_all()])
    print("Поиск «555»:", [c["name"] for c in repo.search("555")])
    print("Избранные:", [c["name"] for c in repo.favorites()])
    print("Города:", repo.stats())
    print("Изменение:", repo.update(e, city="Туркестан"), repo.get(e)["city"])
    print("Удаление:", repo.delete(e), repo.get(e))
    repo.close()
```

Результат:

```text
['Дана', 'Ерлан', 'Мама']
Поиск «555»: ['Ерлан']
Избранные: ['Ерлан']
Города: [('Алматы', 2), ('Шымкент', 1)]
Изменение: True Туркестан
Удаление: True None
```

**Задание 3: `validators.py`**

```python
import datetime
import re


class ValidationError(Exception):
    """Ошибка в данных, введённых пользователем."""


def normalize_phone(phone):
    digits = re.sub(r"\D", "", phone)          # остаются только цифры
    if len(digits) == 11 and digits[0] in "78":
        return "+7" + digits[1:]
    raise ValidationError("Телефон должен быть в формате +7XXXXXXXXXX или 8XXXXXXXXXX")


def validate_contact(name, phone, birthday=""):
    name = name.strip()
    if not 2 <= len(name) <= 40:
        raise ValidationError("Имя должно содержать 2–40 символов")
    phone = normalize_phone(phone)
    birthday = birthday.strip()
    if birthday:
        try:
            date = datetime.date.fromisoformat(birthday)
        except ValueError:
            raise ValidationError("Дата рождения должна быть в формате ГГГГ-ММ-ДД") from None
        if date > datetime.date.today():
            raise ValidationError("Дата рождения не может быть в будущем")
    return name, phone, birthday or None


print(validate_contact("Алия", "8 (701) 111-22-33", "2011-05-17"))
for bad in [("А", "123", ""), ("Ерлан", "123", ""), ("Дана", "+7 702 777 88 99", "17.05.2011")]:
    try:
        validate_contact(*bad)
    except ValidationError as e:
        print("ValidationError:", e)
```

Результат:

```text
('Алия', '+77011112233', '2011-05-17')
ValidationError: Имя должно содержать 2–40 символов
ValidationError: Телефон должен быть в формате +7XXXXXXXXXX или 8XXXXXXXXXX
ValidationError: Дата рождения должна быть в формате ГГГГ-ММ-ДД
```

(`re` — модуль регулярных выражений; `\D` — символ «не цифра». Если ученики его не знают: `"".join(ch for ch in phone if ch.isdigit())`.)

**Задание 4: `app_console.py`**

```python
import sqlite3

from repo import ContactRepository
from validators import ValidationError, validate_contact

MENU = "\n1-Все 2-Добавить 3-Поиск 4-Изменить 5-Удалить 6-Избранное 0-Выход"


def show(contacts):
    if not contacts:
        print("Список пуст")
    for c in contacts:
        star = "★" if c["favorite"] else " "
        print(f"{star} {c['id']:<3} {c['name']:<15} {c['phone']:<13} {c['city'] or ''}")


def ask_int(prompt):
    text = input(prompt).strip()
    return int(text) if text.isdigit() else None


def main():
    repo = ContactRepository("contacts.db")
    while True:
        print(MENU)
        choice = input("> ").strip()
        try:
            if choice == "0":
                break
            elif choice == "1":
                show(repo.list_all())
            elif choice == "2":
                name, phone, birthday = validate_contact(
                    input("Имя: "), input("Телефон: "), input("Дата рождения (ГГГГ-ММ-ДД, можно пусто): "))
                city = input("Город: ").strip() or None
                print("Сохранено, id =", repo.add(name, phone, city, birthday))
            elif choice == "3":
                show(repo.search(input("Поиск: ").strip()))
            elif choice == "4":
                cid = ask_int("id: ")
                old = repo.get(cid) if cid else None
                if old is None:
                    print("Такого контакта нет")
                    continue
                name, phone, birthday = validate_contact(
                    input(f"Имя [{old['name']}]: ") or old["name"],
                    input(f"Телефон [{old['phone']}]: ") or old["phone"],
                    input(f"Дата рождения [{old['birthday'] or ''}]: ") or (old["birthday"] or ""))
                repo.update(cid, name=name, phone=phone, birthday=birthday)
                print("Изменено")
            elif choice == "5":
                cid = ask_int("id: ")
                old = repo.get(cid) if cid else None
                if old and input(f"Удалить {old['name']}? (да/нет): ").strip() == "да":
                    repo.delete(cid)
                    print("Удалено")
                elif old is None:
                    print("Такого контакта нет")
            elif choice == "6":
                cid = ask_int("id: ")
                print("Готово" if cid and repo.toggle_favorite(cid) else "Такого контакта нет")
        except ValidationError as e:
            print("Ошибка:", e)
        except sqlite3.IntegrityError:
            print("Этот номер уже сохранён")
    repo.close()


if __name__ == "__main__":
    main()
```

**Задание 6 (метод для репозитория, показан в виде отдельной функции)**

```python
import datetime


def upcoming_birthdays(contacts, days=30, today=None):
    """[(имя, следующий день рождения, через сколько дней), ...] — ближайший первым."""
    today = today or datetime.date.today()
    result = []
    for c in contacts:
        if not c.get("birthday"):
            continue
        born = datetime.date.fromisoformat(c["birthday"])
        try:
            next_bd = born.replace(year=today.year)
        except ValueError:                       # 29 февраля
            next_bd = datetime.date(today.year, 3, 1)
        if next_bd < today:
            next_bd = next_bd.replace(year=today.year + 1)
        delta = (next_bd - today).days
        if delta <= days:
            result.append((c["name"], next_bd.isoformat(), delta))
    return sorted(result, key=lambda item: item[2])


contacts = [
    {"name": "Алия", "birthday": "2011-02-03"},
    {"name": "Ерлан", "birthday": "2010-02-20"},
    {"name": "Дана", "birthday": "2012-07-01"},
    {"name": "Мама", "birthday": None},
]
for name, date, delta in upcoming_birthdays(contacts, 30, datetime.date(2027, 1, 26)):
    print(f"{name}: {date}, через {delta} дн.")
```

Результат:

```text
Алия: 2027-02-03, через 8 дн.
Ерлан: 2027-02-20, через 25 дн.
```

В репозитории: `return upcoming_birthdays(self.list_all(), days, today)`.

**Задание 7: `test_repo.py` (рядом с repo.py)**

```python
import sqlite3

from repo import ContactRepository


def make_repo():
    return ContactRepository(":memory:")


def test_add_and_get():
    repo = make_repo()
    cid = repo.add("Мама", "+77011112233", "Алматы")
    assert repo.get(cid)["name"] == "Мама"


def test_get_missing():
    assert make_repo().get(999) is None


def test_update():
    repo = make_repo()
    cid = repo.add("Ерлан", "+77475556677")
    assert repo.update(cid, city="Шымкент") is True
    assert repo.get(cid)["city"] == "Шымкент"
    assert repo.update(cid, hacker="x") is False      # неразрешённое поле


def test_delete():
    repo = make_repo()
    cid = repo.add("Дана", "+77027778899")
    assert repo.delete(cid) is True
    assert repo.delete(cid) is False


def test_unique_phone():
    repo = make_repo()
    repo.add("Мама", "+77011112233")
    try:
        repo.add("Папа", "+77011112233")
    except sqlite3.IntegrityError:
        return
    raise AssertionError("Повторный телефон был принят")


if __name__ == "__main__":
    for test in (test_add_and_get, test_get_missing, test_update, test_delete, test_unique_phone):
        test()
        print(test.__name__, "— пройден")
```

**Задание 8** — творческое; за основу можно взять пример Tkinter из раздела 6 теории. Для поиска: `search_entry.bind("<KeyRelease>", lambda e: fill(repo.search(search_entry.get())))`. Избранные: `values=(("★" if c["favorite"] else ""), c["name"], ...)`.

**Задание 9**

```python
import sqlite3


class Repository:
    """Базовый CRUD-класс для любой таблицы."""

    table = ""
    fields = ()

    def __init__(self, conn):
        if not self.table.isidentifier() or not all(f.isidentifier() for f in self.fields):
            raise ValueError("Имена таблиц/полей должны состоять только из букв, цифр и _")
        self.conn = conn
        self.conn.row_factory = sqlite3.Row

    def _clean(self, data):
        return {k: v for k, v in data.items() if k in self.fields}

    def add(self, **data):
        data = self._clean(data)
        cols = ", ".join(data)
        marks = ", ".join("?" for _ in data)
        cur = self.conn.execute(f"INSERT INTO {self.table} ({cols}) VALUES ({marks})", tuple(data.values()))
        self.conn.commit()
        return cur.lastrowid

    def get(self, item_id):
        row = self.conn.execute(f"SELECT * FROM {self.table} WHERE id = ?", (item_id,)).fetchone()
        return dict(row) if row else None

    def list_all(self):
        return [dict(r) for r in self.conn.execute(f"SELECT * FROM {self.table} ORDER BY id")]

    def update(self, item_id, **data):
        data = self._clean(data)
        if not data:
            return False
        sets = ", ".join(f"{k} = ?" for k in data)
        cur = self.conn.execute(f"UPDATE {self.table} SET {sets} WHERE id = ?", (*data.values(), item_id))
        self.conn.commit()
        return cur.rowcount == 1

    def delete(self, item_id):
        cur = self.conn.execute(f"DELETE FROM {self.table} WHERE id = ?", (item_id,))
        self.conn.commit()
        return cur.rowcount == 1


class ContactRepo(Repository):
    table = "contacts"
    fields = ("name", "phone", "city")


class StudentRepo(Repository):
    table = "students"
    fields = ("name", "class", "score")


conn = sqlite3.connect(":memory:")
conn.executescript("""
    CREATE TABLE contacts (id INTEGER PRIMARY KEY, name TEXT, phone TEXT, city TEXT);
    CREATE TABLE students (id INTEGER PRIMARY KEY, name TEXT, class TEXT, score INTEGER);
""")
contacts, students = ContactRepo(conn), StudentRepo(conn)
contacts.add(name="Мама", phone="+77011112233", city="Алматы")
sid = students.add(name="Айгерим", score=95, **{"class": "9А"}, hacker="DROP TABLE")
students.update(sid, score=97)
print(contacts.list_all())
print(students.get(sid))
conn.close()
```

Результат:

```text
[{'id': 1, 'name': 'Мама', 'phone': '+77011112233', 'city': 'Алматы'}]
{'id': 1, 'name': 'Айгерим', 'class': '9А', 'score': 97}
```

Безопасность: имена таблиц и полей зафиксированы в классе (а не получены от пользователя), `_clean` отбрасывает неизвестные ключи (`hacker`), значения — только через `?`.

</details>
