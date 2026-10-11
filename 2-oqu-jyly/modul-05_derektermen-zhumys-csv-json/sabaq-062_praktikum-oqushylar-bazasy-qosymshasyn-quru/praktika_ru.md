# Урок 62. Практические задания: «База учеников», часть 1

> Это практикум: сегодня строим каркас приложения (добавление + отображение + валидация). На следующем уроке (63) завершим и защитим проект. Работу можно выполнять индивидуально или в паре.

## Базовый уровень (обязательные шаги)

### Шаг 1. Структура проекта и Git
1. Создайте папку `student_base` и откройте её в VS Code.
2. Выполните `git init`, создайте файл `.gitignore`:
```text
students.db
*.db
__pycache__/
backups/
```
3. Создайте пустые файлы `db.py` и `app.py`.

### Шаг 2. Слой данных — `db.py`
Напишите класс `StudentDB` из теории (`add`, `get_all`, `count`, `close`). Запустите файл отдельно и проверьте, что в базе `":memory:"` добавляются и выводятся 2–3 ученика. Коммит: `git commit -m "db.py: класс StudentDB"`.

### Шаг 3. Окно и форма
В `app.py` создайте класс `StudentApp`: заголовок окна «База учеников», размер 760×480. Внутри `ttk.LabelFrame` с помощью `grid` разместите пять полей (ФИО, Класс, Город, Телефон, Балл) и две кнопки («Добавить», «Очистить»).

### Шаг 4. Таблица
Под формой добавьте `ttk.Treeview` (столбцы: ID, ФИО, Класс, Город, Телефон, Балл) и вертикальную полосу прокрутки (`ttk.Scrollbar`). При открытии окна метод `refresh()` должен выводить в таблицу всех учеников из базы. Внизу — строка статуса: «Всего учеников: N».

### Шаг 5. Добавление и валидация
Кнопка «Добавить»: читает поля → `validate()` → при ошибке `messagebox.showerror`, иначе `db.add()` → `refresh()` → очищает поля. Кнопка «Очистить» только очищает поля. Добавьте 5 учеников, закройте программу и откройте снова — данные должны сохраниться.

Сценарии проверки:
```text
Имя пустое, нажата «Добавить»  → «ФИО не должно быть пустым»
Класс = 12                     → «Класс должен быть целым числом от 1 до 11»
Балл = 88,5                    → принимается, в таблице 88.5
Балл = сто                     → «Балл должен быть числом»
```

## Продвинутый уровень

### Задание 6. Проверка номера телефона
Телефон может быть пустым, но если он заполнен, то должен быть в формате `+7` и 10 цифр (всего 12 символов). Добавьте проверку в функцию `validate()`.

### Задание 7. Клавиша Enter
При нажатии `Enter` в любом поле должно выполняться «Добавить» (`self.root.bind("<Return>", ...)`). После добавления курсор должен автоматически возвращаться в поле «ФИО» (`focus_set()`).

### Задание 8. Выбор класса через Combobox
Замените поле класса на `ttk.Combobox(values=list(range(1, 12)), state="readonly")` — так уменьшится возможность ошибочного ввода.

## ★ Олимпиадная / творческая задача

### Задание 9. Сортировка по столбцам
При нажатии на заголовок таблицы (heading) данные должны сортироваться по этому столбцу, при повторном нажатии — в обратном порядке. Подсказка: `tree.heading(col, command=lambda c=col: self.sort_by(c))`.

### Задание 10. Цветные строки
Строки учеников с баллом выше 90 должны выделяться зелёным, ниже 50 — розовым цветом (`tree.tag_configure("top", background="#d4f7d4")`, `insert(..., tags=("top",))`).

## Домашнее задание

1. Полностью завершите сегодняшние шаги 1–5 и загрузите проект на GitHub (не менее 3 коммитов).
2. Подготовка к следующему уроку: прочитайте о `tree.selection()`, `tree.item()`, `messagebox.askyesno()`.
3. Составьте план выполнения требований 4–8 из ТЗ (какие методы понадобятся).

## Критерии оценивания

| Критерий | Баллы | % |
|---|---|---|
| Программа работает правильно: добавление, отображение, данные сохраняются (шаги 1–5) | 8 | 40% |
| Рациональность решения: разделение на слои, валидация, метод `refresh()` | 5 | 25% |
| Читаемость кода: имена, docstring, комментарии, PEP 8 | 4 | 20% |
| Самостоятельность и своевременность выполнения, коммиты в Git | 3 | 15% |
| **Итого** | **20** | **100%** |

## Для педагога: решения

<details><summary>Показать решения</summary>

**`db.py`** — класс `StudentDB` из теории (раздел 3) без изменений.

**`app.py` — шаги 3–5 и задания 6–7**
```python
import tkinter as tk
from tkinter import ttk, messagebox

from db import StudentDB

COLUMNS = ("id", "name", "grade", "city", "phone", "score")
HEADERS = ("ID", "ФИО", "Класс", "Город", "Телефон", "Балл")
WIDTHS = (40, 220, 60, 120, 130, 60)


def validate(name, grade, phone, score):
    """Возвращает текст ошибки или None."""
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
        self.root.title("База учеников")
        self.root.geometry("760x480")
        self.db = StudentDB()
        self.entries = {}
        self.build_form()
        self.build_table()
        self.status = tk.Label(root, anchor="w", relief="sunken")
        self.status.pack(fill="x", side="bottom")
        self.root.bind("<Return>", lambda e: self.add_student())
        self.root.protocol("WM_DELETE_WINDOW", self.on_close)
        self.refresh()

    def build_form(self):
        form = ttk.LabelFrame(self.root, text="Данные ученика")
        form.pack(fill="x", padx=10, pady=5)
        fields = [("name", "ФИО:"), ("grade", "Класс:"), ("city", "Город:"),
                  ("phone", "Телефон:"), ("score", "Балл:")]
        for i, (key, label) in enumerate(fields):
            row, col = divmod(i, 2)
            ttk.Label(form, text=label).grid(row=row, column=col * 2, sticky="e", padx=5, pady=3)
            entry = ttk.Entry(form, width=28)
            entry.grid(row=row, column=col * 2 + 1, sticky="w", padx=5, pady=3)
            self.entries[key] = entry
        buttons = ttk.Frame(form)
        buttons.grid(row=3, column=0, columnspan=4, pady=5)
        ttk.Button(buttons, text="Добавить", command=self.add_student).pack(side="left", padx=5)
        ttk.Button(buttons, text="Очистить", command=self.clear_form).pack(side="left", padx=5)

    def build_table(self):
        frame = ttk.Frame(self.root)
        frame.pack(fill="both", expand=True, padx=10, pady=5)
        self.tree = ttk.Treeview(frame, columns=COLUMNS, show="headings")
        for col, head, w in zip(COLUMNS, HEADERS, WIDTHS):
            self.tree.heading(col, text=head)
            self.tree.column(col, width=w, anchor="center" if w <= 60 else "w")
        scroll = ttk.Scrollbar(frame, orient="vertical", command=self.tree.yview)
        self.tree.configure(yscrollcommand=scroll.set)
        self.tree.pack(side="left", fill="both", expand=True)
        scroll.pack(side="right", fill="y")

    def get_form(self):
        return {k: e.get().strip() for k, e in self.entries.items()}

    def clear_form(self):
        for e in self.entries.values():
            e.delete(0, tk.END)
        self.entries["name"].focus_set()

    def refresh(self):
        self.tree.delete(*self.tree.get_children())
        for row in self.db.get_all():
            self.tree.insert("", "end", values=row)
        self.status.config(text=f"Всего учеников: {self.db.count()}")

    def add_student(self):
        d = self.get_form()
        error = validate(d["name"], d["grade"], d["phone"], d["score"])
        if error:
            messagebox.showerror("Ошибка", error)
            return
        self.db.add(d["name"], int(d["grade"]), d["city"], d["phone"],
                    float(d["score"].replace(",", ".")))
        self.refresh()
        self.clear_form()

    def on_close(self):
        self.db.close()
        self.root.destroy()


if __name__ == "__main__":
    root = tk.Tk()
    StudentApp(root)
    root.mainloop()
```

**Задание 8** (замена поля класса внутри `build_form`)
```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
grade_box = ttk.Combobox(root, values=list(range(1, 12)), state="readonly", width=5)
grade_box.set("9")
grade_box.pack()
# self.entries["grade"] = grade_box  — метод .get() работает так же, как у Entry
root.mainloop()
```

**Задание 9** (метод, добавляемый в класс `StudentApp`)
```python
def sort_by(self, col):
    rows = [(self.tree.set(iid, col), iid) for iid in self.tree.get_children()]
    reverse = getattr(self, "_sort_reverse", False)
    try:
        rows.sort(key=lambda t: float(t[0]), reverse=reverse)
    except ValueError:
        rows.sort(key=lambda t: t[0].lower(), reverse=reverse)
    for index, (_, iid) in enumerate(rows):
        self.tree.move(iid, "", index)
    self._sort_reverse = not reverse

# внутри build_table:
# self.tree.heading(col, text=head, command=lambda c=col: self.sort_by(c))
```

**Задание 10** (вариант метода `refresh`)
```python
def refresh(self):
    self.tree.tag_configure("top", background="#d4f7d4")
    self.tree.tag_configure("low", background="#ffd6d6")
    self.tree.delete(*self.tree.get_children())
    for row in self.db.get_all():
        score = row[5] or 0
        tag = "top" if score >= 90 else "low" if score < 50 else ""
        self.tree.insert("", "end", values=row, tags=(tag,))
```

</details>
