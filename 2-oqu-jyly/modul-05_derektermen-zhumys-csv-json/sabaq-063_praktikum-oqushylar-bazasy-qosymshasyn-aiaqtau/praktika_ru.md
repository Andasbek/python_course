# Урок 63. Практические задания: «База учеников» — завершение, защита, Самостоятельная работа №4

## Базовый уровень (шаги завершения проекта)

### Шаг 1. Расширение `db.py`
Добавьте в класс `StudentDB` методы `update`, `delete`, `search` (нечувствительный к регистру, через `PYLOWER`), `stats`, `export_csv(path)`, `backup(folder="backups")`. Проверьте каждый метод в разделе `if __name__ == "__main__":` на базе `":memory:"`.

### Шаг 2. Выбор и изменение
- При щелчке по строке таблицы (`<<TreeviewSelect>>`) данные ученика должны загружаться в форму.
- Кнопка «Сохранить» должна обновлять выбранного ученика (с валидацией!).
- Если ничего не выбрано: «Сначала выберите ученика».

### Шаг 3. Удаление и поиск
- Кнопка «Удалить» — с подтверждением `askyesno`.
- Над таблицей — поле «Поиск:». При каждом нажатии клавиши (`<KeyRelease>`) таблица должна фильтроваться; если поле пустое — показываются все ученики.

### Шаг 4. Экспорт, backup, статистика
- «Экспорт CSV» — через `filedialog.asksaveasfilename`.
- «Backup» — `backups/students_ГГГГ-ММ-ДД_ЧЧ-ММ-СС.db`.
- Строка статуса: «Всего учеников: 12 · Средний балл: 84.3 · Максимальный: 98».

### Шаг 5. Тестирование и подготовка к защите
Выполните 10 сценариев чек-листа тестирования из теории и поставьте ✓. Для защиты внесите 5–6 демо-учеников. Последний коммит: `git commit -m "База учеников v1.0"`, затем `git push` на GitHub.

## Продвинутый уровень

### Задание 6. Импорт из CSV
Кнопка «Импорт»: выбрать CSV через `askopenfilename`, добавить правильные строки в базу, сообщить количество ошибочных строк (урок 61, задание 6).

### Задание 7. Фильтр по классу
Рядом с полем поиска добавьте `Combobox` («Все», 1…11); текст поиска и фильтр по классу должны работать вместе.

## ★ Олимпиадная / творческая задача

### Задание 8. Таблица оценок (JOIN)
Добавьте таблицы `subjects` (предметы) и `marks` (student_id, subject_id, mark). При двойном щелчке по ученику (`<Double-1>`) должно открываться новое окно (`tk.Toplevel`), в котором через `JOIN` показываются его оценки по предметам.

### Задание 9. «Отмена» (Undo)
Запоминайте последнего удалённого ученика; кнопка «Отменить» (или `Ctrl+Z`) должна добавлять его обратно.

## Регламент защиты проекта

| Этап | Время | Что показывается |
|---|---|---|
| Введение | 20 сек | Название, для кого предназначен |
| Демонстрация | 90 сек | Добавление → поиск → изменение → удаление → экспорт/backup |
| Код | 40 сек | Структура `db.py` / `app.py`, одно интересное решение |
| Трудности | 20 сек | Что было трудно, как решено |
| Вопросы | 1 мин | Вопросы педагога и одноклассников |

## Критерии оценивания (практикум-проект)

| Критерий | Баллы | % |
|---|---|---|
| Программа работает правильно, требования 1–7 ТЗ выполнены | 8 | 40% |
| Рациональность решения: разделение на слои, валидация, подтверждение, обработка ошибок | 5 | 25% |
| Читаемость кода: имена, docstring, PEP 8, коммиты в Git | 4 | 20% |
| Самостоятельность, сдача в срок, качество защиты | 3 | 15% |
| **Итого** | **20** | **100%** |

---

## Самостоятельная работа №4 (25 минут)

Во всех заданиях используется база данных `shop.db` (в зависимости от варианта таблица немного отличается). Ответы запишите в файл `.py` и сдайте.

### Вариант 1

1. **(3 балла)** Напишите SQL, создающий таблицу `books`: `id` (первичный ключ, автоинкремент), `title` (обязательный текст), `author` (текст), `year` (целое число), `price` (вещественное число).
2. **(3 балла)** Добавьте в Python через `executemany` 4 книги (не менее 2 казахских авторов — например, Абай, М. Ауэзов).
3. **(4 балла)** Напишите SQL-запросы:
   а) книги, изданные после 2000 года, в порядке убывания цены;
   б) количество книг и средняя цена каждого автора (`GROUP BY`).
4. **(3 балла)** Напишите запрос `UPDATE`, увеличивающий цену всех книг на 10%, и запрос `DELETE`, удаляющий книги дешевле 1000 тенге.
5. **(4 балла)** Напишите функцию Python, экспортирующую таблицу `books` в файл `books.csv` (со строкой заголовков, `utf-8-sig`).
6. **(3 балла)** Сохраните список книг в файл `books.json` в виде списка словарей (`ensure_ascii=False`, `indent=2`).

### Вариант 2

1. **(3 балла)** Напишите SQL, создающий таблицу `products`: `id` (первичный ключ, автоинкремент), `name` (обязательный текст), `category` (текст), `qty` (целое число, по умолчанию 0), `price` (вещественное число).
2. **(3 балла)** Добавьте в Python через `executemany` 5 товаров (не менее 2 категорий: «Канцелярия», «Продукты»).
3. **(4 балла)** Напишите SQL-запросы:
   а) товары с ценой от 500 до 2000 тенге, в алфавитном порядке названий;
   б) общая стоимость товаров в каждой категории (`SUM(qty * price)`).
4. **(3 балла)** Напишите запрос `UPDATE`, увеличивающий количество товаров категории «Продукты» на 5, и запрос `DELETE`, удаляющий товары с количеством, равным 0.
5. **(4 балла)** Напишите функцию Python, которая читает товары из файла `products.csv` (разделитель `;`) и добавляет их в таблицу.
6. **(3 балла)** Напишите функцию, создающую копию базы данных с отметкой времени через `Connection.backup()`.

### Критерии оценивания самостоятельной работы

| Критерий | % |
|---|---|
| Программа/запрос работает правильно и соответствует условию | 40% |
| Эффективность решения (параметризованный запрос, `with`, `executemany`) | 25% |
| Читаемость кода (имена, комментарии) | 20% |
| Самостоятельность и своевременность выполнения | 15% |

Перевод баллов в оценку (20 баллов): 17–20 — высокий уровень (85–100%), 13–16 — средний уровень, 10–12 — удовлетворительно, 9 и ниже — пересдача.

## Домашнее задание

1. Напишите файл README.md проекта: описание, скриншот, способ запуска, список возможностей.
2. Реализуйте «пожелание», полученное на защите, и сделайте коммит.
3. Подготовка к следующему модулю: откройте в браузере любой сайт, нажмите F12 → вкладка Network. Запишите, какие запросы отправляются.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Финальная версия проекта — `db.py`**
```python
import csv
import sqlite3
from datetime import datetime
from pathlib import Path

FIELDS = "id, name, grade, city, phone, score"


class StudentDB:
    """База данных учеников."""

    def __init__(self, path="students.db"):
        self.path = path
        self.conn = sqlite3.connect(path)
        self.conn.create_function("PYLOWER", 1, lambda s: s.lower() if s else s)
        with self.conn:
            self.conn.execute("""CREATE TABLE IF NOT EXISTS students (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                grade INTEGER NOT NULL CHECK (grade BETWEEN 1 AND 11),
                city TEXT, phone TEXT,
                score REAL CHECK (score BETWEEN 0 AND 100))""")

    def add(self, name, grade, city, phone, score):
        with self.conn:
            return self.conn.execute(
                "INSERT INTO students (name, grade, city, phone, score) VALUES (?, ?, ?, ?, ?)",
                (name, grade, city, phone, score)).lastrowid

    def update(self, sid, name, grade, city, phone, score):
        with self.conn:
            return self.conn.execute(
                "UPDATE students SET name=?, grade=?, city=?, phone=?, score=? WHERE id=?",
                (name, grade, city, phone, score, sid)).rowcount

    def delete(self, sid):
        with self.conn:
            return self.conn.execute("DELETE FROM students WHERE id=?", (sid,)).rowcount

    def get_all(self):
        return self.conn.execute(f"SELECT {FIELDS} FROM students ORDER BY name").fetchall()

    def search(self, text):
        p = f"%{text.lower()}%"
        return self.conn.execute(
            f"SELECT {FIELDS} FROM students "
            "WHERE PYLOWER(name) LIKE ? OR PYLOWER(city) LIKE ? ORDER BY name", (p, p)).fetchall()

    def stats(self):
        return self.conn.execute(
            "SELECT COUNT(*), ROUND(AVG(score), 1), MAX(score) FROM students").fetchone()

    def export_csv(self, path):
        cur = self.conn.execute(f"SELECT {FIELDS} FROM students ORDER BY name")
        rows = cur.fetchall()
        with open(path, "w", newline="", encoding="utf-8-sig") as f:
            w = csv.writer(f, delimiter=";")
            w.writerow([c[0] for c in cur.description])
            w.writerows(rows)
        return len(rows)

    def backup(self, folder="backups"):
        Path(folder).mkdir(exist_ok=True)
        target = Path(folder) / f"students_{datetime.now():%Y-%m-%d_%H-%M-%S}.db"
        dst = sqlite3.connect(target)
        with dst:
            self.conn.backup(dst)
        dst.close()
        return target

    def close(self):
        self.conn.close()


if __name__ == "__main__":
    db = StudentDB(":memory:")
    a = db.add("Айгерім Сәрсенова", 9, "Алматы", "", 92.5)
    db.add("Нұрлан Жақсылықов", 10, "Астана", "", 78)
    print(db.search("алм"))
    print(db.stats())
```

**Финальная версия проекта — `app.py`**
```python
import tkinter as tk
from tkinter import ttk, messagebox, filedialog

from db import StudentDB

COLUMNS = ("id", "name", "grade", "city", "phone", "score")
HEADERS = ("ID", "ФИО", "Класс", "Город", "Телефон", "Балл")
WIDTHS = (40, 220, 60, 120, 130, 60)
FORM_KEYS = ("name", "grade", "city", "phone", "score")


def validate(name, grade, phone, score):
    if not name.strip():
        return "ФИО не должно быть пустым"
    if not grade.isdigit() or not 1 <= int(grade) <= 11:
        return "Класс должен быть целым числом от 1 до 11"
    phone = phone.strip()
    if phone and not (phone.startswith("+7") and len(phone) == 12 and phone[1:].isdigit()):
        return "Телефон должен быть в формате +7XXXXXXXXXX"
    try:
        s = float(score.replace(",", "."))
    except ValueError:
        return "Балл должен быть числом"
    if not 0 <= s <= 100:
        return "Балл должен быть в диапазоне 0–100"
    return None


class StudentApp:
    def __init__(self, root):
        self.root = root
        root.title("База учеников")
        root.geometry("820x540")
        self.db = StudentDB()
        self.entries = {}
        self.build_form()
        self.build_search()
        self.build_table()
        self.status = tk.Label(root, anchor="w", relief="sunken")
        self.status.pack(fill="x", side="bottom")
        root.protocol("WM_DELETE_WINDOW", self.on_close)
        self.refresh()

    # ---------- интерфейс ----------
    def build_form(self):
        form = ttk.LabelFrame(self.root, text="Данные ученика")
        form.pack(fill="x", padx=10, pady=5)
        labels = ("ФИО:", "Класс:", "Город:", "Телефон:", "Балл:")
        for i, (key, label) in enumerate(zip(FORM_KEYS, labels)):
            r, c = divmod(i, 2)
            ttk.Label(form, text=label).grid(row=r, column=c * 2, sticky="e", padx=5, pady=3)
            e = ttk.Entry(form, width=28)
            e.grid(row=r, column=c * 2 + 1, sticky="w", padx=5, pady=3)
            self.entries[key] = e
        bar = ttk.Frame(form)
        bar.grid(row=3, column=0, columnspan=4, pady=5)
        for text, cmd in (("Добавить", self.add_student), ("Сохранить", self.update_student),
                          ("Удалить", self.delete_student), ("Очистить", self.clear_form),
                          ("Экспорт CSV", self.export_csv), ("Backup", self.make_backup)):
            ttk.Button(bar, text=text, command=cmd).pack(side="left", padx=3)

    def build_search(self):
        frame = ttk.Frame(self.root)
        frame.pack(fill="x", padx=10)
        ttk.Label(frame, text="Поиск:").pack(side="left")
        self.search_entry = ttk.Entry(frame, width=30)
        self.search_entry.pack(side="left", padx=5)
        self.search_entry.bind("<KeyRelease>", lambda e: self.refresh())

    def build_table(self):
        frame = ttk.Frame(self.root)
        frame.pack(fill="both", expand=True, padx=10, pady=5)
        self.tree = ttk.Treeview(frame, columns=COLUMNS, show="headings")
        for col, head, w in zip(COLUMNS, HEADERS, WIDTHS):
            self.tree.heading(col, text=head)
            self.tree.column(col, width=w, anchor="center" if w <= 60 else "w")
        sb = ttk.Scrollbar(frame, orient="vertical", command=self.tree.yview)
        self.tree.configure(yscrollcommand=sb.set)
        self.tree.pack(side="left", fill="both", expand=True)
        sb.pack(side="right", fill="y")
        self.tree.bind("<<TreeviewSelect>>", self.on_select)

    # ---------- вспомогательные ----------
    def get_form(self):
        return {k: e.get().strip() for k, e in self.entries.items()}

    def clear_form(self):
        for e in self.entries.values():
            e.delete(0, tk.END)
        self.entries["name"].focus_set()

    def selected_id(self):
        sel = self.tree.selection()
        if not sel:
            messagebox.showwarning("Предупреждение", "Сначала выберите ученика")
            return None
        return int(self.tree.item(sel[0], "values")[0])

    def refresh(self):
        text = self.search_entry.get().strip()
        rows = self.db.search(text) if text else self.db.get_all()
        self.tree.delete(*self.tree.get_children())
        for row in rows:
            self.tree.insert("", "end", values=row)
        count, avg, best = self.db.stats()
        self.status.config(text=f"Всего учеников: {count} · Средний балл: {avg or 0} · Максимальный: {best or 0}")

    def checked_data(self):
        d = self.get_form()
        err = validate(d["name"], d["grade"], d["phone"], d["score"])
        if err:
            messagebox.showerror("Ошибка", err)
            return None
        return (d["name"], int(d["grade"]), d["city"], d["phone"],
                float(d["score"].replace(",", ".")))

    # ---------- события ----------
    def on_select(self, event):
        sel = self.tree.selection()
        if not sel:
            return
        values = self.tree.item(sel[0], "values")
        for key, value in zip(FORM_KEYS, values[1:]):
            self.entries[key].delete(0, tk.END)
            self.entries[key].insert(0, value)

    def add_student(self):
        data = self.checked_data()
        if data:
            self.db.add(*data)
            self.clear_form()
            self.refresh()

    def update_student(self):
        sid = self.selected_id()
        data = self.checked_data() if sid else None
        if data:
            self.db.update(sid, *data)
            self.refresh()

    def delete_student(self):
        sid = self.selected_id()
        if sid and messagebox.askyesno("Подтверждение", "Удалить выбранного ученика?"):
            self.db.delete(sid)
            self.clear_form()
            self.refresh()

    def export_csv(self):
        path = filedialog.asksaveasfilename(defaultextension=".csv",
                                            filetypes=[("CSV-файлы", "*.csv")],
                                            initialfile="students.csv")
        if path:
            n = self.db.export_csv(path)
            messagebox.showinfo("Экспорт", f"Сохранено записей: {n}\n{path}")

    def make_backup(self):
        target = self.db.backup()
        messagebox.showinfo("Backup", f"Копия создана:\n{target}")

    def on_close(self):
        self.db.close()
        self.root.destroy()


if __name__ == "__main__":
    root = tk.Tk()
    StudentApp(root)
    root.mainloop()
```

**Задание 9 (Undo) — идея**
```python
# внутри delete_student перед удалением:
#     self.last_deleted = self.tree.item(self.tree.selection()[0], "values")
# def undo(self):
#     if self.last_deleted:
#         _, name, grade, city, phone, score = self.last_deleted
#         self.db.add(name, int(grade), city, phone, float(score))
#         self.last_deleted = None
#         self.refresh()
# self.root.bind("<Control-z>", lambda e: self.undo())
```

**Самостоятельная работа №4, вариант 1**
```python
import csv
import json
import sqlite3

conn = sqlite3.connect("shop.db")
conn.row_factory = sqlite3.Row
# 1
conn.execute("""CREATE TABLE IF NOT EXISTS books (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL, author TEXT, year INTEGER, price REAL)""")
# 2
books = [("Путь Абая", "Мухтар Ауэзов", 1942, 4500),
         ("Слова назидания", "Абай Кунанбайулы", 2015, 2500),
         ("Кочевники", "Ильяс Есенберлин", 2005, 6000),
         ("Основы Python", "А. Сериков", 2021, 800)]
conn.executemany("INSERT INTO books (title, author, year, price) VALUES (?, ?, ?, ?)", books)
conn.commit()
# 3а
for r in conn.execute("SELECT * FROM books WHERE year > 2000 ORDER BY price DESC"):
    print(dict(r))
# 3б
for r in conn.execute("SELECT author, COUNT(*) AS cnt, AVG(price) AS avg_price FROM books GROUP BY author"):
    print(r["author"], r["cnt"], round(r["avg_price"], 1))
# 4
conn.execute("UPDATE books SET price = price * 1.1")
conn.execute("DELETE FROM books WHERE price < 1000")
conn.commit()
# 5
def export_books(path="books.csv"):
    cur = conn.execute("SELECT * FROM books")
    with open(path, "w", newline="", encoding="utf-8-sig") as f:
        w = csv.writer(f)
        w.writerow([c[0] for c in cur.description])
        w.writerows(tuple(r) for r in cur)
export_books()
# 6
data = [dict(r) for r in conn.execute("SELECT * FROM books")]
with open("books.json", "w", encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False, indent=2)
conn.close()
```

**Самостоятельная работа №4, вариант 2**
```python
import csv
import sqlite3
from datetime import datetime

conn = sqlite3.connect("shop.db")
# 1
conn.execute("""CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL, category TEXT, qty INTEGER DEFAULT 0, price REAL)""")
# 2
items = [("Тетрадь", "Канцелярия", 50, 250), ("Ручка", "Канцелярия", 100, 150),
         ("Хлеб", "Продукты", 20, 230), ("Курт", "Продукты", 0, 1500), ("Степлер", "Канцелярия", 5, 1900)]
conn.executemany("INSERT INTO products (name, category, qty, price) VALUES (?, ?, ?, ?)", items)
conn.commit()
# 3а
print(conn.execute("SELECT * FROM products WHERE price BETWEEN 500 AND 2000 ORDER BY name").fetchall())
# 3б
print(conn.execute("SELECT category, SUM(qty * price) FROM products GROUP BY category").fetchall())
# 4
conn.execute("UPDATE products SET qty = qty + 5 WHERE category = 'Продукты'")
conn.execute("DELETE FROM products WHERE qty = 0")
conn.commit()
# 5
def import_products(path="products.csv"):
    with open(path, encoding="utf-8-sig", newline="") as f:
        rows = [(r["name"], r["category"], int(r["qty"]), float(r["price"]))
                for r in csv.DictReader(f, delimiter=";")]
    with conn:
        conn.executemany("INSERT INTO products (name, category, qty, price) VALUES (?, ?, ?, ?)", rows)
    return len(rows)
# 6
def backup_db():
    name = f"shop_{datetime.now():%Y-%m-%d_%H-%M-%S}.db"
    dst = sqlite3.connect(name)
    with dst:
        conn.backup(dst)
    dst.close()
    return name
print(backup_db())
conn.close()
```

</details>
