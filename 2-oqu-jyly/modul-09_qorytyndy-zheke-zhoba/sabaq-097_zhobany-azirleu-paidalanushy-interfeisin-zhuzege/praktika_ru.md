# Урок 97. Практические задания: реализация интерфейса и объединение

> Сегодняшняя цель — **MVP**: программа полностью управляется из окна (или игрового экрана), данные сохраняются. Дополнительное оформление будет на уроке 100 — сегодня только работающая версия.

## Базовый уровень

### Задание 1. Оживить прототип (обязательно)
Превратите `prototype.py` из урока 92 в `gui.py`:
- класс интерфейса (`App(tk.Tk)` или `Game`) получает объект логики через конструктор;
- фиктивные данные (`FAKE_...`) полностью удалены — данные приходят из логики;
- все сообщения «Прототип» заменены настоящими действиями (или оставлены как issue на урок 100).

### Задание 2. `refresh()` и обработка ошибок (обязательно)
- После каждого действия, изменяющего данные, один метод `refresh()` обновляет весь экран.
- Каждое поле ввода проверяется; при ошибке — понятное сообщение на русском, программа не падает.
- Действия удаления/очистки запрашивают подтверждение.

### Задание 3. `main.py` (обязательно)
`main.py` делает только три вещи: создаёт слой данных, создаёт логику, запускает интерфейс. При закрытии окна закрывается подключение к БД.

### Задание 4. Интеграционная проверка
По таблице из части 8 теории пройдите не менее 5 сценариев своего проекта и добавьте результат в `docs/test_log.md`. Если все сценарии Must пройдены: `git tag mvp` и `git push --tags`.

## Продвинутый уровень

### Задание 5. Найдите ошибку
В коде ниже 4 интеграционные ошибки. Найдите и исправьте их.

```python
import tkinter as tk
from tkinter import messagebox


class Counter:
    def __init__(self):
        self.value = 0

    def add(self, n):
        if n <= 0:
            raise ValueError("Число должно быть положительным")
        self.value += n


class App(tk.Tk):
    def __init__(self, counter):
        super().__init__()
        self.counter = counter
        self.entry = tk.Entry(self)
        self.entry.pack()
        tk.Button(self, text="Добавить", command=self.on_add()).pack()
        self.label = tk.Label(self, text="0")
        self.label.pack()

    def on_add(self):
        self.counter.add(int(self.entry.get()))
        self.counter.value += 0


if __name__ == "__main__":
    App(Counter).mainloop()
```

### Задание 6. Поле поиска
Добавьте над таблицей поле поиска: по мере ввода текста пользователем в таблице остаются только подходящие строки (по комментарию или категории, без учёта регистра). *Подсказка:* `entry.bind("<KeyRelease>", ...)` и `filter_ops` из урока 95.

### Задание 7. Фильтр по месяцу
Выведите в `Combobox` список всех имеющихся месяцев (`2027-03`, `2027-04`, ...) и сделайте так, чтобы баланс, таблица и диаграмма обновлялись по выбранному месяцу. Список месяцев получите из БД через `SELECT DISTINCT substr(op_date, 1, 7)`.

## ★ Олимпиадная / творческая задача

### Задание 8. Горячие клавиши
`Ctrl+S` — сохранить, `Delete` — удалить выбранное, `Ctrl+F` — перейти в поле поиска, `Esc` — очистить поля. Покажите список горячих клавиш в меню «Справка» (`Menu`).

### Задание 9. Сортировка таблицы
При нажатии на заголовок столбца `Treeview` таблица должна сортироваться по этому столбцу, при повторном нажатии — в обратном порядке. Столбец «Сумма» должен правильно сортироваться как числа (`+3000`, `−800`).

## Домашнее задание
1. Завершите MVP и загрузите тег `mvp` на GitHub.
2. Проверьте раздел «Запуск» в README: запускается ли проект после клонирования в чистую папку через `pip install -r requirements.txt` и `python main.py`?
3. Следующий урок — code review. Прочитайте свой код один раз и запишите 3 места, которые вам самим не нравятся.

## Критерии оценивания
| Критерий | Доля | Что проверяется |
|---|---|---|
| MVP работает | 40% | все сценарии Must проходятся из окна, данные сохраняются |
| Качество интеграции | 25% | слои не перемешаны, `refresh()`, ошибки обработаны |
| Структура кода | 20% | класс интерфейса, понятные методы, `main.py` короткий |
| Самостоятельная работа, коммиты | 15% | тег `mvp`, журнал проверки заполнен |

## Для педагога: решения
<details><summary>Показать решения</summary>

**Задания 1–4** проверяются индивидуально: попросите ученика показать 5 сценариев при вас; закройте и откройте программу, проверьте, что данные сохранились; попробуйте пустой/неверный ввод.

**Задание 5.** Ошибки: (1) `command=self.on_add()` — со скобками, функция вызывается сразу; (2) `App(Counter)` — передан класс, а не объект; (3) `int(...)` и `ValueError` не обработаны; (4) при изменении значения надпись (`label`) не обновляется; кроме того, `self.label` ещё не существует в момент создания кнопки (вместе с ошибкой 1 это даёт `AttributeError`).
```python
import tkinter as tk
from tkinter import messagebox


class Counter:
    def __init__(self):
        self.value = 0

    def add(self, n):
        if n <= 0:
            raise ValueError("Число должно быть положительным")
        self.value += n


class App(tk.Tk):
    def __init__(self, counter):
        super().__init__()
        self.counter = counter
        self.entry = tk.Entry(self)
        self.entry.pack()
        tk.Button(self, text="Добавить", command=self.on_add).pack()
        self.label = tk.Label(self, text="0")
        self.label.pack()

    def on_add(self):
        try:
            self.counter.add(int(self.entry.get()))
        except ValueError:
            messagebox.showwarning("Ошибка", "Введите целое положительное число")
            return
        self.refresh()

    def refresh(self):
        self.label.config(text=str(self.counter.value))


if __name__ == "__main__":
    App(Counter()).mainloop()
```

**Задание 6** (методы, добавляемые в класс `App`):
```python
import tkinter as tk
from tkinter import ttk


class SearchMixin:
    """Методы, добавляющие в App возможность поиска (считаем, что есть self.table, self.wallet)."""

    def _build_search(self, row):
        self.search_var = tk.StringVar()
        entry = ttk.Entry(self, textvariable=self.search_var)
        entry.grid(row=row, column=0, sticky="ew", padx=10)
        entry.bind("<KeyRelease>", lambda event: self.refresh_table())

    def refresh_table(self):
        text = self.search_var.get().strip().lower()
        self.table.delete(*self.table.get_children())
        for op in self.wallet.operations():
            haystack = f"{op['category']} {op['note']}".lower()
            if text and text not in haystack:
                continue
            self.table.insert("", "end", iid=str(op["id"]),
                              values=(op["op_date"], op["category"], op["amount"], op["note"]))
```
Использование: `class App(SearchMixin, tk.Tk)` и вместо заполнения таблицы внутри `refresh()` — `self.refresh_table()`.

**Задание 7.**
```python
import sqlite3


def months(conn):
    """Все месяцы в БД, самый новый первым."""
    rows = conn.execute(
        "SELECT DISTINCT substr(op_date, 1, 7) AS m FROM operation ORDER BY m DESC")
    return [r[0] for r in rows]


conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE operation (id INTEGER PRIMARY KEY, op_date TEXT);
INSERT INTO operation (op_date) VALUES ('2027-03-30'), ('2027-04-01'), ('2027-04-20');
""")
print(months(conn))
```
Результат:
```text
['2027-04', '2027-03']
```
В интерфейсе: `self.month_box["values"] = months(...)`, `self.month_box.bind("<<ComboboxSelected>>", lambda e: self.refresh())`, а внутри `refresh()` значение `month = self.month_box.get()` передаётся методам логики.

**Задание 8** (внутри `App.__init__`):
```python
def bind_shortcuts(app):
    """Привязывает горячие клавиши. app — объект App."""
    app.bind("<Control-s>", lambda event: app.on_save())
    app.bind("<Delete>", lambda event: app.on_delete())
    app.bind("<Control-f>", lambda event: app.search_entry.focus_set())
    app.bind("<Escape>", lambda event: (app.amount_entry.delete(0, "end"),
                                        app.note_entry.delete(0, "end")))
```
`Menu` → «Справка» → `messagebox.showinfo("Горячие клавиши", "Ctrl+S — сохранить\nDelete — удалить\n...")`.

**Задание 9.**
```python
def sort_column(tree, col, reverse=False):
    """Сортирует столбец Treeview; числа сравнивает как числа."""
    def key(value):
        cleaned = value.replace("−", "-").replace("+", "").replace(" ", "")
        try:
            return (0, int(cleaned))
        except ValueError:
            return (1, value.lower())

    items = [(tree.set(iid, col), iid) for iid in tree.get_children("")]
    items.sort(key=lambda item: key(item[0]), reverse=reverse)
    for index, (_, iid) in enumerate(items):
        tree.move(iid, "", index)
    tree.heading(col, command=lambda: sort_column(tree, col, not reverse))
```
Использование: для каждого столбца `tree.heading(col, text=..., command=lambda c=col: sort_column(tree, c))`. Обратите внимание: поскольку в таблице используется «−» (знак минуса, U+2212), его нужно заменить на обычный «-» — иначе `int()` выдаст ошибку.
</details>
