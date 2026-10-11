# Урок 33. Практические задания: защита GUI-проекта и Самостоятельная работа №2

## Базовый уровень: подготовка репозитория к защите (1-я часть урока)

### Задание 1. Проверь репозиторий (чек-лист)
Открой репозиторий своего проекта «Калькулятор» или «To-Do» и отметь каждый пункт:

| № | Проверка | ✔ |
|---|---|---|
| 1 | Основной `.py`-файл есть и запускается | ☐ |
| 2 | Есть `README.md`: название, описание, возможности, запуск | ☐ |
| 3 | В README виден скриншот | ☐ |
| 4 | Есть `.gitignore`, `__pycache__/`, `venv/` в репозитории отсутствуют | ☐ |
| 5 | Есть не менее 4 осмысленных коммитов | ☐ |
| 6 | Код оформлен в виде класса, у методов есть docstring | ☐ |
| 7 | При неверном вводе программа не падает | ☐ |

### Задание 2. Проверка из чистой папки
Клонируй репозиторий в другую папку и запусти оттуда:
```bash
cd ..
git clone https://github.com/username/todo-app.git test-clone
cd test-clone
python todo_app.py
```

### Задание 3. Подготовь выступление
Напиши краткий план по структуре из 5 частей из теории (по 1–2 предложения на каждую часть).

### Регламент защиты
1. Очерёдность определяет учитель; ученик показывает проект через проектор.
2. Выступление — 3–4 мин, вопросы — 1 мин (учитель и 1–2 ученика).
3. Слушатели заполняют лист взаимооценивания («сильная сторона — предложение — общая оценка»).
4. Учитель заполняет таблицу оценивания модульного проекта (ниже).

**Лист взаимооценивания:**

| Автор проекта | Работает? (0–3) | Интерфейс удобен? (0–3) | Выступление понятно? (0–3) | Сильная сторона | Предложение |
|---|---|---|---|---|---|
| | | | | | |

## Самостоятельная работа №2 (2-я часть урока, 40 минут)

> Выполняй работу в папке `ozindik-zhumys-2`, имя файла `variant1.py` или `variant2.py`. В конце сделай push в репозиторий.

### Вариант 1. «Калькулятор индекса массы тела (ИМТ)»

ИМТ = масса (кг) / рост (м)². Например, 50 кг, 160 см → 50 / 1.6² = 19.5.

**Задание 1 (интерфейс, 3 балла).** Окно с заголовком «Калькулятор ИМТ». С помощью `grid`: «Масса (кг):» + `Entry`, «Рост (см):» + `Entry`, кнопка «Рассчитать», `Label` для результата.

**Задание 2 (логика, 4 балла).** При нажатии «Рассчитать» ИМТ вычисляется с точностью до одного знака после запятой и выводится вместе с категорией:
- меньше 18.5 — «Недостаточная масса»;
- 18.5–24.9 — «Нормальная масса»;
- 25 и выше — «Избыточная масса».

Пример: `50`, `160` → `ИМТ: 19.5 — Нормальная масса`.

**Задание 3 (обработка ошибок, 2 балла).** Если введено не число или ноль/отрицательное число — сообщение «Введите положительное число» через `messagebox.showerror`.

**Задание 4 (расширение, 1 балл).** Вычисление по клавише `Enter` и кнопка «Очистить».

### Вариант 2. «Конвертер температуры»

°F = °C × 9/5 + 32; °C = (°F − 32) × 5/9.

**Задание 1 (интерфейс, 3 балла).** Окно с заголовком «Конвертер температуры»: «Температура:» + `Entry`, два `Radiobutton` («°C → °F», «°F → °C»), кнопка «Конвертировать», `Label` для результата.

**Задание 2 (логика, 4 балла).** Вычислить по выбранному направлению и вывести с одним знаком после запятой. Дополнительно: описание по значению в °C — ниже 0 «Мороз», 0–20 «Прохладно», выше 20 «Тепло».

Пример: `25`, «°C → °F» → `77.0 °F — Тепло`; `14`, «°F → °C» → `-10.0 °C — Мороз`.

**Задание 3 (обработка ошибок, 2 балла).** Если введено не число — «Введите число» через `messagebox.showerror`. Значение ниже −273.15 °C (абсолютный ноль) — «Такой температуры не бывает».

**Задание 4 (расширение, 1 балл).** Вычисление по клавише `Enter` и кнопка «Очистить».

## Продвинутый уровень

Для учеников, которые рано закончили основной вариант:
- **А.** Показывай историю вычислений в `Listbox` (последние 5 результатов).
- **Б.** Перепиши приложение в виде класса (если оно написано функциями), добавь docstring.

## ★ Олимпиадная / творческая задача

Объедини два варианта в одно приложение: с помощью `ttk.Notebook` (вкладки) создай «Помощник по здоровью и погоде» с вкладками «ИМТ» и «Температура».

## Домашнее задание

1. Доработай свой GUI-проект по предложениям, полученным на защите, и сделай push.
2. Найди 1–2 примера игр на библиотеке Pygame (на сайте pygame.org) и запиши, к какому жанру они относятся.
3. По возможности установи `pip install pygame` на домашний компьютер.

## Критерии оценивания

**Самостоятельная работа №2 (10 баллов = 100%)**

| Критерий | Описание | % |
|---|---|---|
| Правильная работа программы и соответствие требованиям | Задания 1–2 выполнены, примеры верны | 40 |
| Эффективность алгоритма, рациональность решения | Ошибки обработаны (задание 3), нет лишних повторов | 25 |
| Читаемость кода | Имена, комментарии, структура (функции/класс) | 20 |
| Самостоятельность и своевременная сдача | Push сделан в течение 40 мин | 15 |

Перевод баллов в проценты: 9–10 баллов — высокий уровень (85–100%), 6–8 — средний (60–84%), 4–5 — начальный (40–59%).

**Оценивание модульного GUI-проекта (защита)**

| Критерий | % | Баллы |
|---|---|---|
| Выполнение технических требований (охват тем модуля: виджеты, размещение, события, диалоги) | 30 | |
| Стабильная и безошибочная работа программы | 25 | |
| Структура и качество кода (PEP 8, разбиение на классы, docstring) | 20 | |
| Удобство для пользователя и оформление | 15 | |
| Правильная загрузка на GitHub и README | 10 | |
| **Итого** | **100** | |

## Для педагога: решения

<details><summary>Показать решения</summary>

**Вариант 1. Калькулятор ИМТ (полное решение с расширением):**

```python
import tkinter as tk
from tkinter import messagebox


class BmiApp:
    """Приложение для расчёта индекса массы тела."""

    def __init__(self, root):
        self.root = root
        root.title("Калькулятор ИМТ")
        tk.Label(root, text="Масса (кг):").grid(row=0, column=0, sticky="e", padx=5, pady=5)
        self.weight = tk.Entry(root)
        self.weight.grid(row=0, column=1, padx=5)
        tk.Label(root, text="Рост (см):").grid(row=1, column=0, sticky="e", padx=5, pady=5)
        self.height = tk.Entry(root)
        self.height.grid(row=1, column=1, padx=5)
        tk.Button(root, text="Рассчитать", command=self.calculate).grid(row=2, column=0, pady=5)
        tk.Button(root, text="Очистить", command=self.clear).grid(row=2, column=1)
        self.result = tk.Label(root, text="", font=("Arial", 12, "bold"))
        self.result.grid(row=3, column=0, columnspan=2, pady=10)
        root.bind("<Return>", lambda event: self.calculate())

    @staticmethod
    def category(bmi):
        if bmi < 18.5:
            return "Недостаточная масса"
        if bmi < 25:
            return "Нормальная масса"
        return "Избыточная масса"

    def calculate(self):
        try:
            weight = float(self.weight.get())
            height = float(self.height.get()) / 100
            if weight <= 0 or height <= 0:
                raise ValueError
        except ValueError:
            messagebox.showerror("Ошибка", "Введите положительное число")
            return
        bmi = weight / height ** 2
        self.result.config(text=f"ИМТ: {bmi:.1f} — {self.category(bmi)}")

    def clear(self):
        self.weight.delete(0, tk.END)
        self.height.delete(0, tk.END)
        self.result.config(text="")


if __name__ == "__main__":
    root = tk.Tk()
    BmiApp(root)
    root.mainloop()
```

Проверка: 50 кг, 160 см → `ИМТ: 19.5 — Нормальная масса`; 45 кг, 170 см → `15.6 — Недостаточная масса`; 90 кг, 175 см → `29.4 — Избыточная масса`.

**Вариант 2. Конвертер температуры:**

```python
import tkinter as tk
from tkinter import messagebox


class TempApp:
    """Конвертер между Цельсием и Фаренгейтом."""

    def __init__(self, root):
        self.root = root
        root.title("Конвертер температуры")
        tk.Label(root, text="Температура:").grid(row=0, column=0, padx=5, pady=5)
        self.entry = tk.Entry(root)
        self.entry.grid(row=0, column=1, padx=5)
        self.mode = tk.StringVar(value="c2f")
        tk.Radiobutton(root, text="°C → °F", variable=self.mode,
                       value="c2f").grid(row=1, column=0)
        tk.Radiobutton(root, text="°F → °C", variable=self.mode,
                       value="f2c").grid(row=1, column=1)
        tk.Button(root, text="Конвертировать", command=self.convert).grid(row=2, column=0, pady=5)
        tk.Button(root, text="Очистить", command=self.clear).grid(row=2, column=1)
        self.result = tk.Label(root, text="", font=("Arial", 12, "bold"))
        self.result.grid(row=3, column=0, columnspan=2, pady=10)
        root.bind("<Return>", lambda event: self.convert())

    @staticmethod
    def describe(celsius):
        if celsius < 0:
            return "Мороз"
        if celsius <= 20:
            return "Прохладно"
        return "Тепло"

    def convert(self):
        try:
            value = float(self.entry.get())
        except ValueError:
            messagebox.showerror("Ошибка", "Введите число")
            return
        if self.mode.get() == "c2f":
            celsius = value
            text = f"{value * 9 / 5 + 32:.1f} °F"
        else:
            celsius = (value - 32) * 5 / 9
            text = f"{celsius:.1f} °C"
        if celsius < -273.15:
            messagebox.showerror("Ошибка", "Такой температуры не бывает")
            return
        self.result.config(text=f"{text} — {self.describe(celsius)}")

    def clear(self):
        self.entry.delete(0, tk.END)
        self.result.config(text="")


if __name__ == "__main__":
    root = tk.Tk()
    TempApp(root)
    root.mainloop()
```

Проверка: 25 °C → `77.0 °F — Тепло`; 14 °F → `-10.0 °C — Мороз`; 0 °C → `32.0 °F — Прохладно`.

**Продвинутый А. История (последние 5 результатов)** — фрагмент, добавляемый к любому варианту:

```python
import tkinter as tk

root = tk.Tk()
history = tk.Listbox(root, height=5, width=35)
history.grid(row=4, column=0, columnspan=2, padx=5, pady=5)


def add_to_history(text):
    history.insert(0, text)          # новое сверху
    if history.size() > 5:
        history.delete(5, tk.END)    # удалить всё сверх 5
```

**★ Объединённое приложение (ttk.Notebook):**

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Помощник по здоровью и погоде")
notebook = ttk.Notebook(root)
bmi_tab = tk.Frame(notebook)
temp_tab = tk.Frame(notebook)
notebook.add(bmi_tab, text="ИМТ")
notebook.add(temp_tab, text="Температура")
notebook.pack(fill="both", expand=True)
# BmiApp(bmi_tab) и TempApp(temp_tab) — классы, приведённые выше.
# Чтобы строка root.title(...) в них вызывалась только для окна,
# достаточно добавить проверку isinstance(root, tk.Tk).
root.mainloop()
```

</details>
