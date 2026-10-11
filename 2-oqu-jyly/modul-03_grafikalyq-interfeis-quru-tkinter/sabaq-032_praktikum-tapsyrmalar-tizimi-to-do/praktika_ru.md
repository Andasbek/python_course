# Урок 32. Практические задания: приложение «To-Do List»

> Цель — создать список задач, сохраняющий данные в файле. После каждого шага запускай и проверяй программу, делай `git commit`. Папка проекта: `todo-app`.

## Базовый уровень

### Шаг 1. Интерфейс
Создай окно (420×420, заголовок «Список задач»):
- сверху — поле ввода (`Entry`) и кнопка «Добавить»;
- в середине — `Listbox` и справа `Scrollbar`;
- внизу — кнопки «Выполнено», «Удалить» и счётчик `Label`.

**Подсказка:** каждую строку размещай в отдельном `Frame` с помощью `pack`.

### Шаг 2. Добавление задачи
Введённый текст должен добавляться в список `self.tasks` в виде `{"text": ..., "done": False}` и отображаться в Listbox как `☐ текст`. Поле ввода должно очищаться. Клавиша `Enter` тоже должна работать.
Пустой текст не добавляется → предупреждение «Текст задачи не должен быть пустым».

### Шаг 3. Отметка и удаление
- «Выполнено» — переключает состояние выбранной задачи (`☐` ↔ `✔`), выполненная отображается серым цветом;
- «Удалить» — после вопроса-подтверждения удаляет выбранную задачу;
- если ничего не выбрано — выводится предупреждение, программа не падает.

### Шаг 4. Счётчик
В нижней строке после каждого изменения должна обновляться статистика вида `Выполнено: 2 / 5`.

### Шаг 5. Сохранение в файл и загрузка
- При запуске программы задачи загружаются из файла `tasks.txt` (если файла нет — пустой список);
- при закрытии окна (`WM_DELETE_WINDOW`) задачи сохраняются в файл.

Пример содержимого файла:
```text
0|Выучить 20 слов по английскому языку
1|Загрузить калькулятор на GitHub
```

## Продвинутый уровень

### Задание 6. Редактирование
Кнопка «Изменить»: изменение текста выбранной задачи с помощью `simpledialog.askstring`.

### Задание 7. Приоритет
Добавь рядом с полем ввода `Combobox`: «Высокий», «Средний», «Низкий». В списке цвет зависит от приоритета: высокий — красный, средний — оранжевый, низкий — зелёный. Формат файла: `0|Высокий|Текст`.

### Задание 8. Сортировка и поиск
- Кнопка «Сортировать»: сначала невыполненные, затем выполненные (внутри каждой группы — по алфавиту);
- поле поиска: показываются только задачи, содержащие введённое слово.

## ★ Олимпиадная / творческая задача

**А. Срок (дедлайн).** Добавь к каждой задаче срок (`ДД.ММ.ГГГГ`). С помощью модуля `datetime` просроченные задачи показывай красным, сегодняшние — жирным шрифтом. При вводе неверной даты — предупреждение.

**Б. Несколько списков.** Переключение списков «Школа», «Дом», «Кружок» через меню (`Menu`); каждый список хранится в отдельном файле.

## Домашнее задание

1. Доделай приложение и загрузи в репозиторий `todo-app`. Добавь файл `tasks.txt` в `.gitignore` (личные данные не должны попадать в репозиторий).
2. `README.md`: описание, список возможностей, скриншот, инструкция по запуску.
3. Подготовься к представлению проекта за 3 минуты на следующем уроке (регламент защиты — в 33-м уроке).

## Критерии оценивания

| Критерий | Описание | Баллы (%) |
|---|---|---|
| Правильная работа и соответствие требованиям | Шаги 1–5 выполнены, все пункты чек-листа тестирования проходят | 40 |
| Рациональность решения | Данные в списке, Listbox — только представление; ошибки (пустой выбор, нет файла) обработаны | 25 |
| Читаемость кода | Структура класса, имена методов, docstring, PEP 8 | 20 |
| Самостоятельность и своевременность | К концу урока — работающая версия, история коммитов | 15 |
| **Итого** | | **100** |

## Для педагога: решения

<details><summary>Показать решения</summary>

**Шаги 1–6.** Полное решение — в классе `TodoApp` в теории (раздел 6).

**Задание 7. Приоритет.** Изменения, вносимые в `TodoApp`:

```python
import tkinter as tk
from tkinter import ttk, messagebox

COLORS = {"Высокий": "red", "Средний": "darkorange", "Низкий": "green"}


class PriorityTodo:
    """Список задач с приоритетом (сокращённая версия)."""

    def __init__(self, root):
        self.root = root
        root.title("Задачи с приоритетом")
        self.tasks = []
        top = tk.Frame(root)
        top.pack(fill="x", padx=10, pady=10)
        self.entry = tk.Entry(top)
        self.entry.pack(side="left", fill="x", expand=True)
        self.priority = ttk.Combobox(top, values=list(COLORS), width=8,
                                     state="readonly")
        self.priority.current(1)
        self.priority.pack(side="left", padx=5)
        tk.Button(top, text="Добавить", command=self.add_task).pack(side="left")
        self.listbox = tk.Listbox(root, font=("Arial", 12))
        self.listbox.pack(fill="both", expand=True, padx=10, pady=5)

    def add_task(self):
        text = self.entry.get().strip()
        if not text:
            messagebox.showwarning("Предупреждение", "Текст пуст")
            return
        self.tasks.append({"text": text, "done": False,
                           "priority": self.priority.get()})
        self.entry.delete(0, tk.END)
        self.refresh_list()

    def refresh_list(self):
        self.listbox.delete(0, tk.END)
        for i, task in enumerate(self.tasks):
            mark = "✔" if task["done"] else "☐"
            self.listbox.insert(tk.END, f"{mark} [{task['priority']}] {task['text']}")
            self.listbox.itemconfig(i, fg=COLORS[task["priority"]])

    def save_line(self, task):
        flag = "1" if task["done"] else "0"
        return f"{flag}|{task['priority']}|{task['text']}\n"

    @staticmethod
    def parse_line(line):
        flag, priority, text = line.rstrip("\n").split("|", 2)
        return {"text": text, "done": flag == "1", "priority": priority}


if __name__ == "__main__":
    root = tk.Tk()
    PriorityTodo(root)
    root.mainloop()
```

**Задание 8. Сортировка и поиск.** Методы, добавляемые в класс `TodoApp`:

```python
def sort_tasks(self):
    self.tasks.sort(key=lambda t: (t["done"], t["text"].lower()))
    self.refresh_list()


def filter_tasks(self, word):
    """Показывает только задачи, содержащие слово поиска."""
    self.listbox.delete(0, "end")
    word = word.lower()
    for task in self.tasks:
        if word in task["text"].lower():
            mark = "✔" if task["done"] else "☐"
            self.listbox.insert("end", f"{mark} {task['text']}")
```

Привязка поля поиска: `search_entry.bind("<KeyRelease>", lambda e: self.filter_tasks(search_entry.get()))`.
Примечание: в режиме фильтрации индексы Listbox не совпадают с индексами `self.tasks`, поэтому для «Удалить»/«Отметить» при фильтре нужно хранить список реальных индексов показанных задач (`self.visible`) — хороший вопрос для разбора с сильными учениками.

Ключ `(t["done"], t["text"].lower())`: `False < True`, поэтому невыполненные выводятся первыми.

**★ А. Срок.**

```python
from datetime import datetime, date


def parse_deadline(text):
    """'25.12.2026' → date; при ошибке None."""
    try:
        return datetime.strptime(text, "%d.%m.%Y").date()
    except ValueError:
        return None


def deadline_color(deadline, done):
    if done or deadline is None:
        return "gray" if done else "black"
    today = date.today()
    if deadline < today:
        return "red"
    if deadline == today:
        return "blue"
    return "black"


print(parse_deadline("25.12.2026"))
print(parse_deadline("31.02.2026"))
print(deadline_color(date(2020, 1, 1), False))
```

Результат:
```text
2026-12-25
None
red
```

**★ Б. Несколько списков.** Имя файла хранится в переменной (`self.filename = "mektep.txt"`), в каждом пункте меню: сохранить текущий список → сменить `self.filename` → `self.tasks = self.load_tasks()` → `refresh_list()`. Создание меню:

```python
import tkinter as tk


def build_menu(app, root):
    menubar = tk.Menu(root)
    lists_menu = tk.Menu(menubar, tearoff=0)
    for name, filename in [("Школа", "mektep.txt"), ("Дом", "ui.txt"),
                           ("Кружок", "uiirme.txt")]:
        lists_menu.add_command(label=name,
                               command=lambda f=filename: app.switch_list(f))
    menubar.add_cascade(label="Списки", menu=lists_menu)
    root.config(menu=menubar)
```

</details>
