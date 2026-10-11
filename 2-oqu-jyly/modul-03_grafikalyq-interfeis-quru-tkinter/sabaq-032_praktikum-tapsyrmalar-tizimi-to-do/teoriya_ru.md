# Урок 32. Практикум: создание приложения «Список задач (To-Do List)».

## Темы

1. Требования и макет
2. Разделение на слои: данные, логика, интерфейс
3. Сохранение данных в файле
4. Сохранение при закрытии окна
5. Работа с Listbox
6. Полный пример: приложение «To-Do List»
7. Чек-лист тестирования
8. Частые ошибки
9. Основные понятия

## 1. Требования и макет

| № | Требование | Тип |
|---|---|---|
| 1 | Добавление новой задачи через поле ввода (кнопка или Enter) | Обязательно |
| 2 | Удаление выбранной задачи | Обязательно |
| 3 | Отметка задачи как «выполненной» (знак ✔) | Обязательно |
| 4 | Пустая задача не добавляется, выводится предупреждение | Обязательно |
| 5 | При закрытии программы задачи сохраняются в файл, при открытии — загружаются | Обязательно |
| 6 | Редактирование задачи (изменение текста) | Дополнительно |
| 7 | Очистка выполненных задач, счётчик («выполнено 3 / 5») | Дополнительно |

```text
+--------------------------------------------+
| [ Введите новую задачу...       ] [Добавить]|
+--------------------------------------------+
| ☐ Домашняя работа по математике        ▲   |
| ✔ Сделать калькулятор на Python         █   |
| ☐ Сходить в библиотеку                  ▼   |
+--------------------------------------------+
| [Выполнено] [Изменить] [Удалить] [Очистить] |
| Выполнено: 1 / 3                            |
+--------------------------------------------+
```

## 2. Разделение на слои: данные, логика, интерфейс

В большом приложении удобно делить код на три части:

| Слой | Что делает | В нашем проекте |
|---|---|---|
| Данные (модель) | Хранит информацию | `self.tasks` — список словарей |
| Логика | Изменяет данные | `add_task()`, `delete_task()`, `toggle_done()` |
| Интерфейс (представление) | Показывает данные на экране | `refresh_list()` — заново заполняет Listbox |

**Главное правило:** Listbox — только «витрина». Настоящие данные хранятся в списке `self.tasks`. После любого изменения мы меняем список и вызываем `refresh_list()`. Тогда экран и данные всегда совпадают.

Одну задачу храним в виде словаря:

```python
tasks = [
    {"text": "Домашняя работа по математике", "done": False},
    {"text": "Сделать калькулятор на Python", "done": True},
]

for i, task in enumerate(tasks, start=1):
    mark = "✔" if task["done"] else "☐"
    print(f"{i}. {mark} {task['text']}")
```

Результат:
```text
1. ☐ Домашняя работа по математике
2. ✔ Сделать калькулятор на Python
```

## 3. Сохранение данных в файле

Данные храним в простом текстовом файле. Каждую задачу запишем в одну строку: сначала состояние (`1` — выполнено, `0` — нет), затем знак `|` и текст.

```text
0|Домашняя работа по математике
1|Сделать калькулятор на Python
0|Сходить в библиотеку
```

**Функция сохранения:**

```python
def save_tasks(tasks, filename="tasks.txt"):
    with open(filename, "w", encoding="utf-8") as f:
        for task in tasks:
            flag = "1" if task["done"] else "0"
            f.write(f"{flag}|{task['text']}\n")
```

**Функция загрузки** (если файла ещё нет, возвращаем пустой список):

```python
def load_tasks(filename="tasks.txt"):
    tasks = []
    try:
        with open(filename, "r", encoding="utf-8") as f:
            for line in f:
                line = line.rstrip("\n")
                if "|" not in line:
                    continue
                flag, text = line.split("|", 1)
                tasks.append({"text": text, "done": flag == "1"})
    except FileNotFoundError:
        pass
    return tasks
```

`split("|", 1)` — делит только по первому `|`, поэтому даже если в тексте задачи есть `|`, она не испортится. `encoding="utf-8"` **обязателен**, чтобы казахские буквы (ә, ғ, қ, ң, ө, ұ, ү, һ, і) и кириллица сохранялись правильно.

> Для более сложных данных удобнее форматы JSON (модуль `json`) и SQLite (модуль `sqlite3`). Текстовый формат — самый простой вариант.

## 4. Сохранение при закрытии окна

Пользователь может забыть нажать кнопку «Сохранить». Поэтому перехватываем кнопку «×» окна:

```python
import tkinter as tk

def on_close():
    print("Данные сохранены")
    root.destroy()

root = tk.Tk()
root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

`protocol("WM_DELETE_WINDOW", func)` — перенаправляет действие закрытия окна в нашу функцию. В конце функции обязательно вызвать `root.destroy()`, иначе окно не закроется.

## 5. Работа с Listbox

| Метод | Что делает |
|---|---|
| `lb.insert(tk.END, "текст")` | Добавляет строку в конец |
| `lb.delete(0, tk.END)` | Удаляет все строки |
| `lb.curselection()` | Кортеж индексов выбранных строк, например `(2,)` или `()` |
| `lb.itemconfig(i, fg="gray")` | Меняет цвет i-й строки |
| `lb.bind("<Double-Button-1>", f)` | Привязывает функцию к двойному щелчку |

Пример получения выбранного индекса:

```python
def get_selected_index(listbox):
    selection = listbox.curselection()
    if not selection:
        return None
    return selection[0]
```

## 6. Полный пример: приложение «To-Do List»

```python
import tkinter as tk
from tkinter import messagebox, simpledialog

FILENAME = "tasks.txt"


class TodoApp:
    """Список задач: добавление, отметка, редактирование, удаление, сохранение в файл."""

    def __init__(self, root):
        self.root = root
        self.root.title("Список задач")
        self.root.geometry("420x420")
        self.tasks = self.load_tasks()
        self.create_widgets()
        self.refresh_list()
        self.root.protocol("WM_DELETE_WINDOW", self.on_close)

    # ---------- Интерфейс ----------
    def create_widgets(self):
        top = tk.Frame(self.root)
        top.pack(fill="x", padx=10, pady=10)
        self.entry = tk.Entry(top, font=("Arial", 12))
        self.entry.pack(side="left", fill="x", expand=True)
        self.entry.bind("<Return>", lambda event: self.add_task())
        tk.Button(top, text="Добавить", command=self.add_task).pack(side="left", padx=5)

        middle = tk.Frame(self.root)
        middle.pack(fill="both", expand=True, padx=10)
        scrollbar = tk.Scrollbar(middle)
        scrollbar.pack(side="right", fill="y")
        self.listbox = tk.Listbox(middle, font=("Arial", 12),
                                  yscrollcommand=scrollbar.set)
        self.listbox.pack(side="left", fill="both", expand=True)
        scrollbar.config(command=self.listbox.yview)
        self.listbox.bind("<Double-Button-1>", lambda event: self.toggle_done())

        bottom = tk.Frame(self.root)
        bottom.pack(fill="x", padx=10, pady=5)
        tk.Button(bottom, text="Выполнено", command=self.toggle_done).pack(side="left")
        tk.Button(bottom, text="Изменить", command=self.edit_task).pack(side="left", padx=5)
        tk.Button(bottom, text="Удалить", command=self.delete_task).pack(side="left")
        tk.Button(bottom, text="Очистить выполненные",
                  command=self.clear_done).pack(side="left", padx=5)

        self.status = tk.Label(self.root, text="", anchor="w")
        self.status.pack(fill="x", padx=10, pady=(0, 10))

    def refresh_list(self):
        self.listbox.delete(0, tk.END)
        for i, task in enumerate(self.tasks):
            mark = "✔" if task["done"] else "☐"
            self.listbox.insert(tk.END, f"{mark} {task['text']}")
            if task["done"]:
                self.listbox.itemconfig(i, fg="gray")
        done = sum(1 for t in self.tasks if t["done"])
        self.status.config(text=f"Выполнено: {done} / {len(self.tasks)}")

    def selected_index(self):
        selection = self.listbox.curselection()
        if not selection:
            messagebox.showwarning("Предупреждение", "Сначала выберите задачу")
            return None
        return selection[0]

    # ---------- Логика ----------
    def add_task(self):
        text = self.entry.get().strip()
        if not text:
            messagebox.showwarning("Предупреждение", "Текст задачи не должен быть пустым")
            return
        self.tasks.append({"text": text, "done": False})
        self.entry.delete(0, tk.END)
        self.refresh_list()

    def toggle_done(self):
        index = self.selected_index()
        if index is None:
            return
        self.tasks[index]["done"] = not self.tasks[index]["done"]
        self.refresh_list()

    def edit_task(self):
        index = self.selected_index()
        if index is None:
            return
        new_text = simpledialog.askstring(
            "Редактирование", "Новый текст:", initialvalue=self.tasks[index]["text"])
        if new_text and new_text.strip():
            self.tasks[index]["text"] = new_text.strip()
            self.refresh_list()

    def delete_task(self):
        index = self.selected_index()
        if index is None:
            return
        if messagebox.askyesno("Подтверждение", "Удалить задачу?"):
            self.tasks.pop(index)
            self.refresh_list()

    def clear_done(self):
        self.tasks = [t for t in self.tasks if not t["done"]]
        self.refresh_list()

    # ---------- Данные ----------
    @staticmethod
    def load_tasks():
        tasks = []
        try:
            with open(FILENAME, "r", encoding="utf-8") as f:
                for line in f:
                    line = line.rstrip("\n")
                    if "|" in line:
                        flag, text = line.split("|", 1)
                        tasks.append({"text": text, "done": flag == "1"})
        except FileNotFoundError:
            pass
        return tasks

    def save_tasks(self):
        with open(FILENAME, "w", encoding="utf-8") as f:
            for task in self.tasks:
                flag = "1" if task["done"] else "0"
                f.write(f"{flag}|{task['text']}\n")

    def on_close(self):
        self.save_tasks()
        self.root.destroy()


if __name__ == "__main__":
    root = tk.Tk()
    TodoApp(root)
    root.mainloop()
```

**Обратите внимание:**
- все изменения вносятся в `self.tasks`, после чего `refresh_list()` обновляет экран;
- `simpledialog.askstring()` — готовое диалоговое окно для ввода текста («родственник» `messagebox`);
- `[t for t in self.tasks if not t["done"]]` — фильтрация с помощью генератора списка (list comprehension).

## 7. Чек-лист тестирования

| № | Действие | Результат |
|---|---|---|
| 1 | «Добавить» при пустом поле | Окно предупреждения, список не меняется |
| 2 | Ввести «Почитать книгу» и нажать Enter | В списке «☐ Почитать книгу» |
| 3 | Двойной щелчок по задаче | «✔», серый цвет, счётчик меняется |
| 4 | «Удалить», ничего не выбрав | «Сначала выберите задачу» |
| 5 | Закрыть окно и открыть снова | Все задачи и их состояния сохранены |
| 6 | Задача с казахскими буквами: «Әжеме қоңырау шалу» (Позвонить бабушке) | Правильно и в файле, и на экране |

## Частые ошибки

**1. Получение индекса, когда ничего не выбрано**
```python
def delete_task():
    index = listbox.curselection()[0]   # IndexError, если ничего не выбрано
```
Правильно: сначала проверить `if not listbox.curselection(): return`.

**2. Хранение данных только в Listbox.** «Считывать» текст обратно из Listbox и отрезать знак ✔ — источник ошибок. Данные — в списке, Listbox — только представление.

**3. Не указана кодировка**
```python
f = open("tasks.txt", "w")   # в Windows казахские буквы могут испортиться
```
Правильно: `open("tasks.txt", "w", encoding="utf-8")`.

**4. Нет `destroy()` внутри `on_close`** — окно не закрывается, и пользователь думает, что «программа зависла».

**5. Падение, когда файла нет** — при первом запуске `tasks.txt` отсутствует. Если не обработать `FileNotFoundError`, программа не откроется.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Сохранение данных (персистентность) | Сохранность данных и после закрытия программы |
| Модель / представление | Структура данных / её отображение на экране |
| `curselection()` | Индексы выбранных строк в Listbox |
| `protocol("WM_DELETE_WINDOW")` | Перехват события закрытия окна |
| `simpledialog.askstring` | Стандартный диалог, запрашивающий текст |
| `encoding="utf-8"` | Кодировка, правильно сохраняющая национальные буквы |
| Генератор списка | Краткая запись вида `[x for x in ... if ...]` |

## Примечание

- Всегда открывайте файл с параметром `encoding="utf-8"`: кодировка по умолчанию зависит от операционной системы, и на Windows без него казахские буквы могут испортиться.
- Знак `|` может встретиться и в тексте задачи. Поэтому разбивайте строку только по первому `|`: `line.split("|", 1)`.
- Если задан `root.protocol("WM_DELETE_WINDOW", on_close)`, окно само не закрывается — внутри `on_close` обязательно вызовите `root.destroy()`.
- `Listbox.curselection()` возвращает кортеж (tuple); если ничего не выбрано — пустой кортеж `()`. Проверяйте его перед получением индекса.
- Чтобы файл не повредился при сохранении, можно сначала записать данные во временный файл, а затем заменить им основной через `os.replace(tmp, path)` — замена выполняется атомарно.
- Документация функции `open()`: https://docs.python.org/3/library/functions.html#open
