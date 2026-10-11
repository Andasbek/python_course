# Урок 31. Практические задания: оконное приложение «Калькулятор»

> Цель практикума — к концу урока получить работающий калькулятор, готовый к загрузке на GitHub. Выполняй работу поэтапно, после каждого этапа запускай и проверяй программу и делай `git commit`.

## Базовый уровень

### Шаг 1. Папка проекта и окно
1. Создай папку `calculator` и выполни в ней `git init`.
2. В файле `calculator.py` создай окно размером 300×400 с заголовком «Калькулятор» и неизменяемым размером.
3. В верхней части окна размести дисплей (`Entry`) с выравниванием по правому краю и шрифтом размера 24. Изначально на дисплее должен стоять `0`.

```bash
mkdir calculator
cd calculator
git init
git add calculator.py
git commit -m "Окно и дисплей калькулятора"
```

### Шаг 2. Сетка кнопок
По макету из теории построй сетку из 4 столбцов: `C ⌫ % /`, `7 8 9 *`, `4 5 6 -`, `1 2 3 +`, `0 . =`. Кнопка «0» должна занимать два столбца (`columnspan=2`).
**Подсказка:** опиши кнопки списком кортежей и создай их в цикле.

### Шаг 3. Ввод цифр и операций
Напиши функцию `press(symbol)`: при нажатии кнопки символ добавляется в конец выражения и отображается на дисплее.

Пример: нажаты `1`, `2`, `+`, `5` → на дисплее `12+5`.

### Шаг 4. Вычисление, очистка, удаление
- `=` — вычисляет выражение и выводит результат (`12+5` → `17`);
- `C` — возвращает дисплей к `0`;
- `⌫` — удаляет последний символ (`125` → `12`).

### Шаг 5. Обработка ошибок
- `8/0` → на дисплее «Делить на ноль нельзя»;
- `8*` → «Ошибка в выражении»;
- `6/3` → `2` (а не `2.0`).

## Продвинутый уровень

### Задание 6. Управление с клавиатуры
Должны работать клавиши цифр и `+ - * / .`, `Enter` (вычислить), `BackSpace` (удалить), `Escape` (очистить).

### Задание 7. Дополнительные кнопки
Добавь кнопки `x²` (квадрат) и `√` (корень). При извлечении корня из отрицательного числа должна выводиться «Ошибка».
Пример: `9` → `√` → `3`; `12` → `x²` → `144`.

### Задание 8. История вычислений
Добавь справа (или снизу) окна `Listbox` и сохраняй каждое вычисление в виде `12+5 = 17`. При двойном щелчке по строке истории результат должен снова появиться на дисплее.

## ★ Олимпиадная / творческая задача

**А. Калькулятор без eval.** Не используя `eval`, напиши логику «карманного калькулятора», который хранит два числа и одну операцию отдельно: вводится число → нажимается операция → второе число → `=`. `2 + 3 =` → `5`, затем `* 4 =` → `20`.

**Б. Приложение-конвертер валют.** Добавь в калькулятор режим «₸ ↔ $»: направление выбирается в `Combobox`, курс (например, 1 $ = 480 ₸) хранится в отдельном поле. API подключим позже (в VI модуле).

## Домашнее задание

1. Доделай калькулятор и загрузи его на GitHub в репозиторий `calculator`.
2. Напиши для репозитория `README.md`: описание проекта, возможности, команда запуска, скриншот интерфейса.
3. Выполни 9 проверок из таблицы тестирования (в теории) на своём калькуляторе и добавь результаты в `README.md` разделом «Тестирование».

## Критерии оценивания

| Критерий | Описание | Баллы (%) |
|---|---|---|
| Правильная работа программы и соответствие требованиям | Шаги 1–5 выполнены полностью, все тесты проходят | 40 |
| Эффективность алгоритма, рациональность решения | Кнопки созданы циклом/списком, одна общая функция `press()`, ошибки обработаны | 25 |
| Читаемость кода | Понятные имена, структура класса, docstring, комментарии | 20 |
| Самостоятельность и своевременность выполнения | К концу урока — работающая версия + история коммитов | 15 |
| **Итого** | | **100** |

Дополнительные возможности (6–8, ★) помогают достичь высокого уровня (85–100%).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Шаги 1–6.** Полное решение приведено в теории в разделе «Полный пример: приложение «Калькулятор»» (класс `Calculator`). Для размера окна можно добавить `root.geometry("300x400")`; чтобы столбцы `grid` растягивались равномерно:

```python
import tkinter as tk

root = tk.Tk()
root.geometry("300x400")
for col in range(4):
    root.columnconfigure(col, weight=1)
for row in range(6):
    root.rowconfigure(row, weight=1)
```

**Задание 7. x² и √ (методы, добавляемые в класс Calculator):**

```python
import math


def square(self):
    try:
        value = self.safe_eval(self.expression)
        self.expression = self.format_number(value ** 2)
        self.update_display()
    except (ValueError, SyntaxError, ZeroDivisionError):
        self.expression = ""
        self.display_var.set("Ошибка")


def square_root(self):
    try:
        value = self.safe_eval(self.expression)
        if value < 0:
            raise ValueError("Отрицательное число")
        self.expression = self.format_number(math.sqrt(value))
        self.update_display()
    except (ValueError, SyntaxError, ZeroDivisionError):
        self.expression = ""
        self.display_var.set("Ошибка")
```

Нужно добавить в список `BUTTONS` `("x²", 6, 0, 1)`, `("√", 6, 1, 1)`, а в `press()` — ветви `elif symbol == "x²": self.square()` и `elif symbol == "√": self.square_root()`.

**Задание 8. История вычислений:**

```python
import tkinter as tk

ALLOWED = set("0123456789+-*/.() ")


class HistoryCalculator:
    """Упрощённый калькулятор с историей (через поле ввода)."""

    def __init__(self, root):
        self.root = root
        root.title("Калькулятор с историей")
        self.entry = tk.Entry(root, font=("Arial", 18), justify="right")
        self.entry.grid(row=0, column=0, padx=5, pady=5, sticky="ew")
        tk.Button(root, text="=", width=4,
                  command=self.calculate).grid(row=0, column=1)
        self.history = tk.Listbox(root, width=30, height=8)
        self.history.grid(row=1, column=0, columnspan=2, padx=5, pady=5)
        self.history.bind("<Double-Button-1>", self.restore)
        root.bind("<Return>", lambda event: self.calculate())

    def calculate(self):
        expr = self.entry.get()
        try:
            if not expr or not set(expr) <= ALLOWED:
                raise ValueError
            result = eval(expr, {"__builtins__": {}}, {})
            if isinstance(result, float) and result.is_integer():
                result = int(result)
            self.history.insert(tk.END, f"{expr} = {result}")
            self.entry.delete(0, tk.END)
            self.entry.insert(0, str(result))
        except ZeroDivisionError:
            self.entry.delete(0, tk.END)
            self.entry.insert(0, "Делить на ноль нельзя")
        except (SyntaxError, ValueError):
            self.entry.delete(0, tk.END)
            self.entry.insert(0, "Ошибка в выражении")

    def restore(self, event):
        selection = self.history.curselection()
        if selection:
            line = self.history.get(selection[0])
            result = line.split(" = ")[1]
            self.entry.delete(0, tk.END)
            self.entry.insert(0, result)


if __name__ == "__main__":
    root = tk.Tk()
    HistoryCalculator(root)
    root.mainloop()
```

**★ А. Калькулятор без eval (логика в отдельном классе, можно проверить в консоли):**

```python
class PocketCalculator:
    """Логика калькулятора, хранящего два операнда и одну операцию."""

    def __init__(self):
        self.current = "0"
        self.first = None
        self.operation = None
        self.new_number = True

    def digit(self, d):
        if self.new_number:
            self.current = d
            self.new_number = False
        else:
            self.current += d

    def set_operation(self, op):
        if self.operation and not self.new_number:
            self.equals()
        self.first = float(self.current)
        self.operation = op
        self.new_number = True

    def equals(self):
        if self.operation is None:
            return self.current
        second = float(self.current)
        if self.operation == "+":
            result = self.first + second
        elif self.operation == "-":
            result = self.first - second
        elif self.operation == "*":
            result = self.first * second
        elif second == 0:
            self.current = "Ошибка"
            self.operation = None
            self.new_number = True
            return self.current
        else:
            result = self.first / second
        if result.is_integer():
            result = int(result)
        self.current = str(result)
        self.operation = None
        self.new_number = True
        return self.current


calc = PocketCalculator()
calc.digit("2")
calc.set_operation("+")
calc.digit("3")
print(calc.equals())
calc.set_operation("*")
calc.digit("4")
print(calc.equals())
```

Результат:
```text
5
20
```
Подключение этого класса к кнопкам Tkinter: цифры → `calc.digit(t)`, операции → `calc.set_operation(t)`, `=` → `display_var.set(calc.equals())`.

**★ Б. Конвертер валют (краткий пример):**

```python
import tkinter as tk
from tkinter import ttk

RATE = 480  # курс 1 доллара в тенге (пример)


def convert():
    try:
        amount = float(amount_entry.get())
    except ValueError:
        result_label.config(text="Введите число")
        return
    if direction.get() == "₸ → $":
        result_label.config(text=f"{amount / RATE:.2f} $")
    else:
        result_label.config(text=f"{amount * RATE:.2f} ₸")


root = tk.Tk()
root.title("Конвертер валют")
amount_entry = tk.Entry(root, font=("Arial", 14))
amount_entry.grid(row=0, column=0, padx=5, pady=5)
direction = ttk.Combobox(root, values=["₸ → $", "$ → ₸"], state="readonly")
direction.current(0)
direction.grid(row=0, column=1, padx=5)
tk.Button(root, text="Конвертировать", command=convert).grid(row=1, column=0, columnspan=2)
result_label = tk.Label(root, text="", font=("Arial", 16))
result_label.grid(row=2, column=0, columnspan=2, pady=10)
root.mainloop()
```

</details>
