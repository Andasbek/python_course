# Сабақ 60. Практикалық тапсырмалар: CRUD қосымшасы

Бүгінгі негізгі жоба — **«Телефон кітапшасы»** қосымшасы. Ол қабаттарға бөлінеді:

```text
contacts/
├── repo.py          # ContactRepository — барлық SQL осында
├── validators.py    # тексеру функциялары
├── app_console.py   # консольдық мәзір
└── contacts.db      # автоматты құрылады (.gitignore-ге қос!)
```

Кесте: `contacts(id INTEGER PRIMARY KEY, name TEXT NOT NULL, phone TEXT NOT NULL UNIQUE, city TEXT, birthday TEXT, favorite INTEGER DEFAULT 0)`.

## Базалық деңгей

### 1-тапсырма. Репозиторий: Create және Read

`repo.py` ішінде `ContactRepository` класын жаз:
- `__init__(self, path)` — қосылу, `row_factory = sqlite3.Row`, кестені құру;
- `add(name, phone, city=None, birthday=None)` → жаңа `id`;
- `get(contact_id)` → сөздік немесе `None`;
- `list_all()` → барлық контактілер (аты бойынша сұрыпталған) сөздіктер тізімі;
- `close()`.

Тексеру:

```text
repo = ContactRepository(":memory:")
repo.add("Анам", "+77011112233", "Алматы")
repo.add("Ерлан", "+77475556677", "Шымкент")
print(repo.list_all())
→ [{'id': 1, 'name': 'Анам', ...}, {'id': 2, 'name': 'Ерлан', ...}]
```

### 2-тапсырма. Update және Delete

Класқа қос:
- `update(contact_id, **fields)` — тек рұқсат етілген өрістерді (`name`, `phone`, `city`, `birthday`, `favorite`) өзгертеді, `True/False` қайтарады;
- `delete(contact_id)` → `True/False`;
- `toggle_favorite(contact_id)` — `favorite` өрісін 0 ↔ 1 ауыстырады (бір SQL: `SET favorite = 1 - favorite`).

### 3-тапсырма. Валидация

`validators.py` ішінде `ValidationError` класы және `validate_contact(name, phone, birthday)` функциясы:
- аты бос емес, 2–40 таңба;
- телефон: бос орындар мен сызықшаларды алып тастағанда `+7` және 10 цифр (`+77011112233`); `8 701 111 22 33` форматын да қабылдап, `+7...` түріне келтір;
- туған күні бос немесе `ЖЖЖЖ-АА-КК` форматында (`datetime.date.fromisoformat` арқылы тексер).

```text
validate_contact("Әлия", "8 (701) 111-22-33", "2011-05-17")
→ ('Әлия', '+77011112233', '2011-05-17')
validate_contact("Ә", "123", "")
→ ValidationError: Аты 2–40 таңба болуы керек
```

### 4-тапсырма. Консольдық мәзір

`app_console.py`: мәзір (1 — барлығы, 2 — қосу, 3 — іздеу, 4 — өзгерту, 5 — жою, 6 — таңдаулыға қосу/алу, 0 — шығу). Бұл файлда бірде-бір SQL сұранысы болмауы керек! Телефон қайталанса (`IntegrityError`) — «Бұл нөмір бұрын сақталған» деп шығар.

## Кеңейтілген деңгей

### 5-тапсырма. Іздеу және сүзгілер

Репозиторийге қос:
- `search(text)` — аты **немесе** телефоны бойынша (бөлігі);
- `by_city(city)` — қала бойынша;
- `favorites()` — тек таңдаулылар;
- `stats()` — қалалар бойынша контактілер саны (`GROUP BY`).

### 6-тапсырма. Туған күндер

`upcoming_birthdays(days=30, today=None)` әдісі — алдағы `days` күн ішінде туған күні болатын контактілерді (күні бойынша сұрыптап) қайтарсын. Кеңес: туған күннің айы мен күнін (`birthday[5:]`) алып, Python-да биылғы немесе келесі жылдағы күнмен салыстыр.

```text
today = 2027-01-26
→ [('Әлия', '2027-02-03', 8 күннен кейін), ('Ерлан', '2027-02-20', 25 күннен кейін)]
```

### 7-тапсырма. Тесттер

`test_repo.py` файлында `:memory:` қорымен кемінде 5 тест жаз (`assert`): қосу, id бойынша оқу, жоқ id, өзгерту, жою, қайталанған телефон `IntegrityError` береді.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Tkinter интерфейсі

Сол `ContactRepository`-ді өзгертпей, Tkinter интерфейсін жаз: `Treeview` кестесі, қосу/өзгерту формасы, іздеу өрісі (теру барысында сүзгілеу — `<KeyRelease>` оқиғасы), «Жою» батырмасы растаумен, таңдаулыларды ★ белгісімен көрсету. Бұл 62–63-сабақтардағы практикумға дайындық болады.

### 9-тапсырма. Әмбебап репозиторий

Кез келген кестемен жұмыс істейтін `Repository(conn, table, fields)` базалық класын жаз (`add(**data)`, `get(id)`, `list_all()`, `update(id, **data)`, `delete(id)`). `ContactRepository` пен `StudentRepository` оны мұра етсін (II модуль — мұрагерлік). Кесте мен өріс атауларының қауіпсіздігін қамтамасыз ет.

## Үй тапсырмасы

1. «Телефон кітапшасын» аяқтап, GitHub-қа жүкте (README: мүмкіндіктер, іске қосу, скриншот; `contacts.db` — `.gitignore`-де).
2. Ойлан және жаз: «Оқушылар базасы» қосымшасы үшін қандай репозиторий әдістері керек? (62-сабақта Tkinter + SQLite практикумы.)

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–2, 5-тапсырмалар: `repo.py`**

```python
import sqlite3


class ContactRepository:
    """contacts кестесімен CRUD амалдары."""

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
            "SELECT COALESCE(city, 'белгісіз'), COUNT(*) FROM contacts GROUP BY city ORDER BY COUNT(*) DESC")]

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
    repo.add("Анам", "+77011112233", "Алматы")
    e = repo.add("Ерлан", "+77475556677", "Шымкент")
    repo.add("Дана", "+77027778899", "Алматы")
    repo.toggle_favorite(e)
    print([c["name"] for c in repo.list_all()])
    print("Іздеу «555»:", [c["name"] for c in repo.search("555")])
    print("Таңдаулылар:", [c["name"] for c in repo.favorites()])
    print("Қалалар:", repo.stats())
    print("Өзгерту:", repo.update(e, city="Түркістан"), repo.get(e)["city"])
    print("Жою:", repo.delete(e), repo.get(e))
    repo.close()
```

Нәтижесі:

```text
['Анам', 'Дана', 'Ерлан']
Іздеу «555»: ['Ерлан']
Таңдаулылар: ['Ерлан']
Қалалар: [('Алматы', 2), ('Шымкент', 1)]
Өзгерту: True Түркістан
Жою: True None
```

**3-тапсырма: `validators.py`**

```python
import datetime
import re


class ValidationError(Exception):
    """Пайдаланушы енгізген деректердің қатесі."""


def normalize_phone(phone):
    digits = re.sub(r"\D", "", phone)          # тек цифрлар қалады
    if len(digits) == 11 and digits[0] in "78":
        return "+7" + digits[1:]
    raise ValidationError("Телефон +7XXXXXXXXXX немесе 8XXXXXXXXXX форматында болуы керек")


def validate_contact(name, phone, birthday=""):
    name = name.strip()
    if not 2 <= len(name) <= 40:
        raise ValidationError("Аты 2–40 таңба болуы керек")
    phone = normalize_phone(phone)
    birthday = birthday.strip()
    if birthday:
        try:
            date = datetime.date.fromisoformat(birthday)
        except ValueError:
            raise ValidationError("Туған күні ЖЖЖЖ-АА-КК форматында болуы керек") from None
        if date > datetime.date.today():
            raise ValidationError("Туған күні болашақта бола алмайды")
    return name, phone, birthday or None


print(validate_contact("Әлия", "8 (701) 111-22-33", "2011-05-17"))
for bad in [("Ә", "123", ""), ("Ерлан", "123", ""), ("Дана", "+7 702 777 88 99", "17.05.2011")]:
    try:
        validate_contact(*bad)
    except ValidationError as e:
        print("ValidationError:", e)
```

Нәтижесі:

```text
('Әлия', '+77011112233', '2011-05-17')
ValidationError: Аты 2–40 таңба болуы керек
ValidationError: Телефон +7XXXXXXXXXX немесе 8XXXXXXXXXX форматында болуы керек
ValidationError: Туған күні ЖЖЖЖ-АА-КК форматында болуы керек
```

(`re` — тұрақты өрнектер модулі; `\D` — «цифр емес» таңба. Оқушылар оны білмесе: `"".join(ch for ch in phone if ch.isdigit())`.)

**4-тапсырма: `app_console.py`**

```python
import sqlite3

from repo import ContactRepository
from validators import ValidationError, validate_contact

MENU = "\n1-Барлығы 2-Қосу 3-Іздеу 4-Өзгерту 5-Жою 6-Таңдаулы 0-Шығу"


def show(contacts):
    if not contacts:
        print("Тізім бос")
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
                    input("Аты: "), input("Телефон: "), input("Туған күні (ЖЖЖЖ-АА-КК, бос болуы мүмкін): "))
                city = input("Қаласы: ").strip() or None
                print("Сақталды, id =", repo.add(name, phone, city, birthday))
            elif choice == "3":
                show(repo.search(input("Іздеу: ").strip()))
            elif choice == "4":
                cid = ask_int("id: ")
                old = repo.get(cid) if cid else None
                if old is None:
                    print("Мұндай контакт жоқ")
                    continue
                name, phone, birthday = validate_contact(
                    input(f"Аты [{old['name']}]: ") or old["name"],
                    input(f"Телефон [{old['phone']}]: ") or old["phone"],
                    input(f"Туған күні [{old['birthday'] or ''}]: ") or (old["birthday"] or ""))
                repo.update(cid, name=name, phone=phone, birthday=birthday)
                print("Өзгертілді")
            elif choice == "5":
                cid = ask_int("id: ")
                old = repo.get(cid) if cid else None
                if old and input(f"{old['name']} жойылсын ба? (иә/жоқ): ").strip() == "иә":
                    repo.delete(cid)
                    print("Жойылды")
                elif old is None:
                    print("Мұндай контакт жоқ")
            elif choice == "6":
                cid = ask_int("id: ")
                print("Дайын" if cid and repo.toggle_favorite(cid) else "Мұндай контакт жоқ")
        except ValidationError as e:
            print("Қате:", e)
        except sqlite3.IntegrityError:
            print("Бұл нөмір бұрын сақталған")
    repo.close()


if __name__ == "__main__":
    main()
```

**6-тапсырма (репозиторийге қосылатын әдіс, жеке функция түрінде көрсетілген)**

```python
import datetime


def upcoming_birthdays(contacts, days=30, today=None):
    """[(аты, келесі туған күні, неше күннен кейін), ...] — жақыны бірінші."""
    today = today or datetime.date.today()
    result = []
    for c in contacts:
        if not c.get("birthday"):
            continue
        born = datetime.date.fromisoformat(c["birthday"])
        try:
            next_bd = born.replace(year=today.year)
        except ValueError:                       # 29 ақпан
            next_bd = datetime.date(today.year, 3, 1)
        if next_bd < today:
            next_bd = next_bd.replace(year=today.year + 1)
        delta = (next_bd - today).days
        if delta <= days:
            result.append((c["name"], next_bd.isoformat(), delta))
    return sorted(result, key=lambda item: item[2])


contacts = [
    {"name": "Әлия", "birthday": "2011-02-03"},
    {"name": "Ерлан", "birthday": "2010-02-20"},
    {"name": "Дана", "birthday": "2012-07-01"},
    {"name": "Анам", "birthday": None},
]
for name, date, delta in upcoming_birthdays(contacts, 30, datetime.date(2027, 1, 26)):
    print(f"{name}: {date}, {delta} күннен кейін")
```

Нәтижесі:

```text
Әлия: 2027-02-03, 8 күннен кейін
Ерлан: 2027-02-20, 25 күннен кейін
```

Репозиторийде: `return upcoming_birthdays(self.list_all(), days, today)`.

**7-тапсырма: `test_repo.py` (repo.py қасында)**

```python
import sqlite3

from repo import ContactRepository


def make_repo():
    return ContactRepository(":memory:")


def test_add_and_get():
    repo = make_repo()
    cid = repo.add("Анам", "+77011112233", "Алматы")
    assert repo.get(cid)["name"] == "Анам"


def test_get_missing():
    assert make_repo().get(999) is None


def test_update():
    repo = make_repo()
    cid = repo.add("Ерлан", "+77475556677")
    assert repo.update(cid, city="Шымкент") is True
    assert repo.get(cid)["city"] == "Шымкент"
    assert repo.update(cid, hacker="x") is False      # рұқсат етілмеген өріс


def test_delete():
    repo = make_repo()
    cid = repo.add("Дана", "+77027778899")
    assert repo.delete(cid) is True
    assert repo.delete(cid) is False


def test_unique_phone():
    repo = make_repo()
    repo.add("Анам", "+77011112233")
    try:
        repo.add("Әкем", "+77011112233")
    except sqlite3.IntegrityError:
        return
    raise AssertionError("Қайталанған телефон қабылданып кетті")


if __name__ == "__main__":
    for test in (test_add_and_get, test_get_missing, test_update, test_delete, test_unique_phone):
        test()
        print(test.__name__, "— өтті")
```

**8-тапсырма** — шығармашылық; теориядағы 6-бөлімнің Tkinter мысалын негізге алуға болады. Іздеу үшін: `search_entry.bind("<KeyRelease>", lambda e: fill(repo.search(search_entry.get())))`. Таңдаулылар: `values=(("★" if c["favorite"] else ""), c["name"], ...)`.

**9-тапсырма**

```python
import sqlite3


class Repository:
    """Кез келген кестеге арналған базалық CRUD класы."""

    table = ""
    fields = ()

    def __init__(self, conn):
        if not self.table.isidentifier() or not all(f.isidentifier() for f in self.fields):
            raise ValueError("Кесте/өріс атаулары тек әріп, цифр және _ болуы керек")
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
contacts.add(name="Анам", phone="+77011112233", city="Алматы")
sid = students.add(name="Айгерім", score=95, **{"class": "9А"}, hacker="DROP TABLE")
students.update(sid, score=97)
print(contacts.list_all())
print(students.get(sid))
conn.close()
```

Нәтижесі:

```text
[{'id': 1, 'name': 'Анам', 'phone': '+77011112233', 'city': 'Алматы'}]
{'id': 1, 'name': 'Айгерім', 'class': '9А', 'score': 97}
```

Қауіпсіздік: кесте мен өріс атаулары класта бекітілген (пайдаланушыдан емес), `_clean` белгісіз кілттерді (`hacker`) алып тастайды, мәндер — тек `?` арқылы.

</details>
