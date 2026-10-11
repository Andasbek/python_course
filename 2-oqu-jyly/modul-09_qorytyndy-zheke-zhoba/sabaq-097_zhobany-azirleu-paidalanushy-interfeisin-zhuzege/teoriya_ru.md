# Урок 97. Разработка проекта: реализация пользовательского интерфейса.

## Темы

1. Что такое интеграция?
2. Полная цепочка одного события
3. Интерфейс логики
4. Пример проекта: `gui.py`
5. `main.py` — собираем всё вместе
6. Долгие действия: чтобы окно не «зависало»
7. Объединение в проекте на Pygame
8. Интеграционная проверка и MVP
9. Частые ошибки
10. Основные понятия

## 1. Что такое интеграция?

**Интеграция (integration)** — объединение отдельно написанных частей в одну работающую программу. Даже если каждая часть работает по отдельности, при соединении могут появиться новые ошибки: функция ожидает значение другого типа, названия не совпадают, данные не обновляются.

**Аналогия:** колесо, рама и цепь велосипеда могут быть по отдельности отличными, но при сборке размеры болтов должны совпадать. Наши «болты» — **параметры и возвращаемые значения** функций (их интерфейсы).

Порядок объединения:
```text
main.py
  │ создаёт
  ├──> Database("qalta.db")            (слой данных)
  ├──> Wallet(db)                      (логика)
  └──> App(wallet) ──> mainloop()      (интерфейс)
```

Каждый слой знает только **нижележащий** слой: `App` → `Wallet` → `Database`. Не наоборот.

## 2. Полная цепочка одного события

Что происходит, когда пользователь нажимает кнопку «Сохранить»:

```text
[Кнопка нажата]
      │
      v
App.on_save()           1. берёт текст из полей
      │                 2. преобразует текст в число (ошибка -> сообщение)
      v
Wallet.add_expense()    3. проверяет правила (ошибка -> ValueError)
      │
      v
Database.add_operation() 4. записывает в БД
      │
      v
App.refresh()           5. обновляет баланс, таблицу, диаграмму
      │
      v
[Пользователь увидел результат: «Сохранено»]
```

**Правило:** при каждом изменении данных обновляем **весь экран из одного места** — методом `refresh()`. Иначе в одном месте баланс обновится, а в таблице останутся старые данные.

## 3. Интерфейс логики

Методы логики, которые вызывает интерфейс (кратко):

```python
class Wallet:
    """Список методов, используемых интерфейсом (кратко)."""

    def __init__(self, db):
        self.db = db

    def categories(self, kind):
        return self.db.categories(kind)

    def add_operation(self, category_id, amount, op_date, note=""):
        if amount <= 0:
            raise ValueError("Сумма должна быть положительной")
        return self.db.add_operation(category_id, amount, op_date, note)

    def delete_operation(self, op_id):
        return self.db.delete_operation(op_id)

    def operations(self, month=None):
        return self.db.operations(month)

    def balance(self, month=None):
        return sum(op["amount"] if op["kind"] == "доход" else -op["amount"]
                   for op in self.db.operations(month))

    def expenses_by_category(self, month=None):
        totals = {}
        for op in self.db.operations(month):
            if op["kind"] == "расход":
                totals[op["category"]] = totals.get(op["category"], 0) + op["amount"]
        return totals
```

## 4. Пример проекта: `gui.py`

Интерфейс пишем в виде **класса** (ООП): виджеты — атрибуты, обработчики событий — методы. Это избавляет от глобальных переменных.

```python
"""gui.py — оконный интерфейс «Карманных денег»."""
import datetime
import tkinter as tk
from tkinter import ttk, messagebox

from matplotlib.figure import Figure
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg


class App(tk.Tk):
    """Главное окно. Получает логику (wallet) извне."""

    def __init__(self, wallet):
        super().__init__()
        self.wallet = wallet
        self.title("Карманные деньги")
        self.geometry("820x480")
        self.minsize(700, 420)
        self._build_top()
        self._build_form()
        self._build_table()
        self._build_chart()
        self.refresh()

    # ---------- Создание виджетов ----------
    def _build_top(self):
        top = ttk.Frame(self, padding=10)
        top.grid(row=0, column=0, columnspan=2, sticky="ew")
        self.balance_label = ttk.Label(top, font=("Arial", 18, "bold"))
        self.balance_label.pack(side="left")

    def _build_form(self):
        form = ttk.LabelFrame(self, text="Новая операция", padding=10)
        form.grid(row=1, column=0, sticky="nw", padx=10)

        self.kind_var = tk.StringVar(value="расход")
        for i, kind in enumerate(("расход", "доход")):
            ttk.Radiobutton(form, text=kind.capitalize(), value=kind,
                            variable=self.kind_var,
                            command=self._update_categories).grid(row=0, column=i, sticky="w")

        ttk.Label(form, text="Сумма (тг):").grid(row=1, column=0, sticky="w", pady=4)
        self.amount_entry = ttk.Entry(form, width=18)
        self.amount_entry.grid(row=1, column=1, pady=4)

        ttk.Label(form, text="Категория:").grid(row=2, column=0, sticky="w", pady=4)
        self.category_box = ttk.Combobox(form, state="readonly", width=16)
        self.category_box.grid(row=2, column=1, pady=4)

        ttk.Label(form, text="Комментарий:").grid(row=3, column=0, sticky="w", pady=4)
        self.note_entry = ttk.Entry(form, width=18)
        self.note_entry.grid(row=3, column=1, pady=4)

        ttk.Button(form, text="Сохранить", command=self.on_save).grid(
            row=4, column=1, sticky="e", pady=8)
        ttk.Button(form, text="Удалить выбранное", command=self.on_delete).grid(
            row=5, column=0, columnspan=2, sticky="ew")
        self.status = ttk.Label(form, foreground="#2e7d32")
        self.status.grid(row=6, column=0, columnspan=2, sticky="w", pady=6)

        self.amount_entry.bind("<Return>", lambda event: self.on_save())
        self._update_categories()

    def _build_table(self):
        self.table = ttk.Treeview(self, columns=("date", "cat", "sum", "note"),
                                  show="headings", height=10)
        for col, title, width in (("date", "Дата", 90), ("cat", "Категория", 110),
                                  ("sum", "Сумма", 80), ("note", "Комментарий", 150)):
            self.table.heading(col, text=title)
            self.table.column(col, width=width)
        self.table.grid(row=2, column=0, sticky="nsew", padx=10, pady=10)
        self.rowconfigure(2, weight=1)
        self.columnconfigure(1, weight=1)

    def _build_chart(self):
        self.figure = Figure(figsize=(3.6, 3.2), dpi=90)
        self.axes = self.figure.add_subplot()
        self.canvas = FigureCanvasTkAgg(self.figure, master=self)
        self.canvas.get_tk_widget().grid(row=1, column=1, rowspan=2,
                                         sticky="nsew", padx=10, pady=10)

    # ---------- Обработка событий ----------
    def _update_categories(self):
        self._categories = self.wallet.categories(self.kind_var.get())
        self.category_box["values"] = [c["title"] for c in self._categories]
        self.category_box.current(0)

    def on_save(self):
        text = self.amount_entry.get().strip().replace(" ", "")
        if not text.isdigit():
            messagebox.showwarning("Ошибка", "Введите сумму целым числом, например: 800")
            self.amount_entry.focus_set()
            return
        category = self._categories[self.category_box.current()]
        try:
            self.wallet.add_operation(category["id"], int(text),
                                      datetime.date.today().isoformat(),
                                      self.note_entry.get().strip())
        except ValueError as err:          # нарушено правило логики
            messagebox.showwarning("Ошибка", str(err))
            return
        except Exception as err:           # непредвиденная ошибка: программа не должна упасть
            messagebox.showerror("Ошибка", f"Не удалось сохранить:\n{err}")
            return
        self.amount_entry.delete(0, "end")
        self.note_entry.delete(0, "end")
        self.status.config(text=f"Сохранено: {text} тг, {category['title']}")
        self.refresh()

    def on_delete(self):
        selected = self.table.selection()
        if not selected:
            messagebox.showinfo("Удаление", "Сначала выберите операцию в таблице.")
            return
        if messagebox.askyesno("Подтверждение", "Удалить выбранную операцию?"):
            self.wallet.delete_operation(int(selected[0]))   # iid = id операции
            self.status.config(text="Удалено")
            self.refresh()

    # ---------- Обновление экрана ----------
    def refresh(self):
        """Обновляет всё отображение при каждом изменении данных."""
        month = datetime.date.today().strftime("%Y-%m")
        balance = self.wallet.balance()
        self.balance_label.config(text=f"Баланс: {balance:,} тг".replace(",", " "))

        self.table.delete(*self.table.get_children())
        for op in self.wallet.operations():
            sign = "+" if op["kind"] == "доход" else "−"
            self.table.insert("", "end", iid=str(op["id"]), values=(
                op["op_date"], op["category"], f"{sign}{op['amount']}", op["note"]))

        totals = self.wallet.expenses_by_category(month)
        self.axes.clear()
        if totals:
            self.axes.pie(list(totals.values()), labels=list(totals.keys()),
                          autopct="%1.0f%%")
            self.axes.set_title("Расходы этого месяца")
        else:
            self.axes.text(0.5, 0.5, "В этом месяце расходов нет", ha="center", va="center")
            self.axes.axis("off")
        self.canvas.draw()
```

Пояснения:
- `class App(tk.Tk)` — наследование: само окно является объектом нашего класса.
- Методы `_build_...` (имя начинается с `_`) — внутренние методы; они делят окно на части.
- `iid` каждой строки таблицы — это `id` операции в БД. Поэтому удалить выбранную строку легко.
- `FigureCanvasTkAgg` превращает рисунок Matplotlib в виджет Tkinter (диаграмма отображается прямо внутри окна).
- Двухуровневая обработка ошибок: `ValueError` — понятное пользователю правило; `Exception` — непредвиденная ошибка, но программа не закрывается.

## 5. `main.py` — собираем всё вместе

```python
"""main.py — точка запуска «Карманных денег»."""
from pathlib import Path

from qalta.database import Database
from qalta.logic import Wallet
from qalta.gui import App

DB_PATH = Path(__file__).resolve().parent / "qalta.db"


def main():
    db = Database(DB_PATH)
    wallet = Wallet(db)
    app = App(wallet)
    app.protocol("WM_DELETE_WINDOW", lambda: (db.close(), app.destroy()))
    app.mainloop()


if __name__ == "__main__":
    main()
```

`protocol("WM_DELETE_WINDOW", ...)` — при нажатии кнопки окна «×» правильно закрывает подключение к БД.

## 6. Долгие действия: чтобы окно не «зависало»

Запрос к API может занять 1–5 секунд. В это время `mainloop` останавливается, и окно «не отвечает». Простое решение — запустить запрос после открытия программы через `after()` и, пока ждём результат, показывать «Загрузка...». Более полное решение — отдельный поток (`threading`):

```python
import threading
import tkinter as tk


def slow_request():
    """Вместо запроса к API: ждём 2 секунды."""
    import time
    time.sleep(2)
    return 512.3


class RateLabel(tk.Label):
    def __init__(self, master):
        super().__init__(master, text="Курс: загрузка...")
        threading.Thread(target=self._load, daemon=True).start()

    def _load(self):
        rate = slow_request()                      # в отдельном потоке
        self.after(0, lambda: self.config(text=f"1 USD = {rate} тг"))  # экран меняет только главный поток


if __name__ == "__main__":
    root = tk.Tk()
    RateLabel(root).pack(padx=20, pady=20)
    root.mainloop()
```

> **Важно:** виджеты Tkinter можно изменять только из главного потока — поэтому результат передаём через `after(0, ...)`.

## 7. Объединение в проекте на Pygame

В игре интерфейс — это игровой цикл и сцены; логика — `Player`, `GameState`; данные — JSON рекордов.

```python
import pygame


class Player:
    def __init__(self):
        self.lives, self.score = 3, 0


class Game:
    """Игровой цикл: событие -> логика -> отрисовка."""

    def __init__(self):
        pygame.init()
        self.screen = pygame.display.set_mode((640, 400))
        pygame.display.set_caption("Игра проекта")
        self.font = pygame.font.SysFont("arial", 24)
        self.clock = pygame.time.Clock()
        self.player = Player()
        self.state = "menu"

    def handle_events(self):
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                return False
            if event.type == pygame.KEYDOWN:
                if self.state == "menu" and event.key == pygame.K_RETURN:
                    self.state = "game"
                elif self.state == "game" and event.key == pygame.K_SPACE:
                    self.player.score += 10            # логика
        return True

    def draw(self):
        self.screen.fill((20, 24, 40))
        if self.state == "menu":
            text = "ENTER - начать"
        else:
            text = f"Очки: {self.player.score}   Жизни: {self.player.lives}"
        self.screen.blit(self.font.render(text, True, (255, 255, 255)), (20, 20))
        pygame.display.flip()

    def run(self):
        running = True
        while running:
            running = self.handle_events()
            self.draw()
            self.clock.tick(60)
        pygame.quit()


if __name__ == "__main__":
    Game().run()
```

Структура такая же, как в Tkinter: **событие → вызов логики → перерисовка экрана**.

## 8. Интеграционная проверка и MVP

После объединения вручную проходим от начала до конца **каждый основной сценарий** из ТЗ:

| № | Сценарий | Шаги | Ожидаемое поведение | ✓ |
|---|---|---|---|---|
| 1 | Добавить расход | 800, «Еда», Сохранить | строка в таблице, баланс −800, диаграмма обновилась | |
| 2 | Неверная сумма | «абв», Сохранить | предупреждение, ничего не сохранено | |
| 3 | Удаление | выбрать строку, Удалить, Да | строки нет, баланс восстановился | |
| 4 | Повторное открытие | закрыть → открыть | все данные на месте | |
| 5 | Пустая БД | удалить файл БД и открыть | пустая таблица, текст «расходов нет» | |

Если все сценарии Must пройдены — **MVP готов**. Отмечаем это в Git:
```bash
git add .
git commit -m "MVP: интерфейс подключён к логике и БД"
git tag mvp
git push --tags
```

## Частые ошибки

**1. `command=self.on_save()` — со скобками.** Функция вызывается один раз при создании окна, а при нажатии кнопки — ничего. Правильно: `command=self.on_save` (без скобок) или `lambda: ...`.

**2. Изменить данные и не обновить экран.** В БД запись есть, а в таблице нет. Правильно: после каждого изменения — `self.refresh()`.

**3. Дублирование логики в интерфейсе.** Повторный расчёт баланса внутри `gui.py`. Правильно: вызывать `self.wallet.balance()` — вычисление в одном месте.

**4. Заполнять `Treeview`, не очистив его** — при каждом обновлении строки удваиваются. Правильно: `self.table.delete(*self.table.get_children())`.

**5. Ошибка импорта.** `ModuleNotFoundError: No module named 'qalta'` — `main.py` запущен вне папки проекта или нет `qalta/__init__.py`. Правильно: `python main.py` из корня проекта.

**6. Действие на пустой таблице/без выбора** — `selection()[0]` → `IndexError`. Правильно: сначала проверка `if not selected:`.

## Основные понятия
| Понятие | Объяснение |
|---|---|
| Интеграция | объединение отдельных модулей в одну работающую систему |
| Обработчик события | метод, реагирующий на событие: кнопку, клавишу и т.п. |
| `refresh()` | метод, обновляющий всё отображение при изменении данных |
| `FigureCanvasTkAgg` | класс, размещающий рисунок Matplotlib в Tkinter |
| Поток (thread) | часть программы, выполняемая параллельно |
| Интеграционная проверка | прохождение сценариев через всю систему от начала до конца |
| Метка MVP (`git tag`) | название важной точки в репозитории |

## Примечание

- В `matplotlib` внутри Tkinter используйте `matplotlib.figure.Figure`, а не `pyplot`: окна `plt.figure()` живут в глобальном состоянии `pyplot` и не освобождают память при закрытии вашего окна.
- Если окно Matplotlib открывается отдельно от Tkinter, проверьте бэкенд: с Tkinter используется `FigureCanvasTkAgg` из `matplotlib.backends.backend_tkagg`.
- `ttk.Treeview` хранит значения строк как строки: `table.item(iid, "values")` вернёт `('2027-04-27', 'Еда', '-900', '')` — числа нужно преобразовывать обратно через `int()`.
- `widget.after(ms, func)` — безопасный способ запланировать действие в главном потоке Tkinter; `time.sleep()` в обработчике замораживает всё окно.
- Тяжёлые импорты (`matplotlib`) замедляют запуск на 1–2 секунды — это нормально; при желании импортируйте их внутри функции, которая строит диаграмму.
- Документация: https://docs.python.org/3/library/tkinter.html и https://matplotlib.org/stable/gallery/user_interfaces/embedding_in_tk_sgskip.html
