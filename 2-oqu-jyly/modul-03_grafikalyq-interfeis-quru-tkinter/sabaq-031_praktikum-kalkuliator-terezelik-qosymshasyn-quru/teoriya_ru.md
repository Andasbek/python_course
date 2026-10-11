# Урок 31. Практикум: создание оконного приложения «Калькулятор».

## Темы

1. Начинаем с планирования приложения
2. Структура кода: почему класс?
3. Создание кнопок с помощью цикла
4. Одна функция — много кнопок: lambda
5. Вычисление выражения: eval() и его опасность
6. Обработка ошибок
7. Полный пример: приложение «Калькулятор»
8. Тестирование
9. Частые ошибки
10. Основные понятия

## 1. Начинаем с планирования приложения

Профессиональный программист не пишет код сразу. Сначала он определяет **требования**.

**Требования к приложению «Калькулятор» (техническое задание):**

| № | Требование | Обязательно? |
|---|---|---|
| 1 | На дисплее отображаются введённое выражение и результат | Да |
| 2 | Кнопки цифр 0–9, точка `.` | Да |
| 3 | Операции: `+`, `-`, `*`, `/` | Да |
| 4 | `=` — вычислить результат, `C` — очистить, `⌫` — удалить последний символ | Да |
| 5 | При делении на ноль программа не падает, а выводит «Ошибка» | Да |
| 6 | Ввод возможен и с клавиатуры | Дополнительно |
| 7 | Кнопки `%`, `x²`, `√`, история вычислений | Дополнительно |

**Макет интерфейса:**

```text
+-------------------------------+
|                     125+37    |   <- дисплей (row=0, columnspan=4)
+-------+-------+-------+-------+
|   C   |   ⌫   |   %   |   /   |   row=1
+-------+-------+-------+-------+
|   7   |   8   |   9   |   *   |   row=2
+-------+-------+-------+-------+
|   4   |   5   |   6   |   -   |   row=3
+-------+-------+-------+-------+
|   1   |   2   |   3   |   +   |   row=4
+-------+-------+-------+-------+
|       0       |   .   |   =   |   row=5 («0» занимает два столбца)
+---------------+-------+-------+
```

Макет сразу показывает нам строки (`row`) и столбцы (`column`) сетки `grid`.

## 2. Структура кода: почему класс?

Класс объединяет данные и действия в одном месте. В калькуляторе:
- **данные (атрибуты):** `self.expression` — вводимое выражение, `self.display_var` — текст на дисплее;
- **действия (методы):** `create_widgets()`, `press(symbol)`, `calculate()`, `clear()`, `backspace()`.

```text
Calculator
 ├── __init__(root)         настройка окна
 ├── create_widgets()       создание дисплея и кнопок
 ├── press(symbol)          добавление символа к выражению
 ├── calculate()            вычисление при нажатии «=»
 ├── clear() / backspace()  очистка
 └── on_key(event)          событие клавиатуры
```

## 3. Создание кнопок с помощью цикла

Писать 20 кнопок по одной — долго и чревато ошибками. Вместо этого создадим **список** кнопок и построим их в цикле:

```python
import tkinter as tk

root = tk.Tk()
root.title("Сетка кнопок")

buttons = [
    "7", "8", "9", "/",
    "4", "5", "6", "*",
    "1", "2", "3", "-",
    "0", ".", "=", "+",
]

for index, text in enumerate(buttons):
    row = index // 4 + 1      # номер строки
    col = index % 4           # номер столбца
    btn = tk.Button(root, text=text, width=5, height=2, font=("Arial", 14))
    btn.grid(row=row, column=col, padx=2, pady=2)

root.mainloop()
```

`index // 4` и `index % 4` — с помощью целочисленного деления и остатка одномерный список «нарезается» на сетку из 4 столбцов.

## 4. Одна функция — много кнопок: lambda

Чтобы не писать отдельную функцию для каждой кнопки, создадим одну общую функцию `press(symbol)`. Но в параметр `command` нельзя напрямую передать функцию с аргументом, поэтому используем `lambda`:

```python
import tkinter as tk

def press(symbol):
    print("Нажато:", symbol)

root = tk.Tk()
for i, text in enumerate(["1", "2", "3"]):
    tk.Button(root, text=text, width=5,
              command=lambda s=text: press(s)).grid(row=0, column=i)
root.mainloop()
```

Результат (в консоли при поочерёдном нажатии кнопок):
```text
Нажато: 1
Нажато: 2
Нажато: 3
```

**Важно:** `s=text` в записи `lambda s=text: press(s)` — значение по умолчанию. Оно «запоминает» `text` на каждой итерации. Если написать `lambda: press(text)`, все кнопки будут передавать последнее значение («3») (см. раздел «Частые ошибки» ниже).

## 5. Вычисление выражения: eval() и его опасность

В Python `eval("2+3*4")` вычисляет выражение из строки и возвращает `14`. Для калькулятора это очень удобно. Но `eval` выполняет **любой** код Python, поэтому передавать ему введённый пользователем текст без проверки опасно.

Для безопасности применим две меры:
1. проверим, что в выражении есть только разрешённые символы (`0123456789+-*/.()% `);
2. передадим `eval` пустое окружение: `eval(expr, {"__builtins__": {}}, {})`.

```python
ALLOWED = set("0123456789+-*/.() ")

def safe_eval(expr):
    """Вычисляет только арифметическое выражение."""
    if not expr or not set(expr) <= ALLOWED:
        raise ValueError("Недопустимый символ")
    return eval(expr, {"__builtins__": {}}, {})

print(safe_eval("2+3*4"))
print(safe_eval("(10-4)/4"))
try:
    print(safe_eval("10/0"))
except ZeroDivisionError:
    print("Делить на ноль нельзя!")
```

Результат:
```text
14
1.5
Делить на ноль нельзя!
```

**Красивый вывод результата.** Результат `6/2` получается `3.0`. Если число целое, убираем `.0`, а длинную дробь округляем:

```python
def format_number(value):
    if isinstance(value, float) and value.is_integer():
        return str(int(value))
    if isinstance(value, float):
        return str(round(value, 10))
    return str(value)

print(format_number(3.0), format_number(0.1 + 0.2), format_number(7))
```

Результат:
```text
3 0.3 7
```

## 6. Обработка ошибок

| Ситуация | Пример | Какая ошибка | Что показываем |
|---|---|---|---|
| Деление на ноль | `5/0` | `ZeroDivisionError` | «Делить на ноль нельзя» |
| Незаконченное выражение | `5+` | `SyntaxError` | «Ошибка в выражении» |
| Пустое выражение | `` | наш собственный `ValueError` | ничего не меняется |
| Посторонний символ | `abc` | наш собственный `ValueError` | «Ошибка в выражении» |

Блоки `except` пишем с конкретными типами ошибок — это делает программу понятной.

## 7. Полный пример: приложение «Калькулятор»

```python
import tkinter as tk

ALLOWED = set("0123456789+-*/.() ")


class Calculator:
    """Простой калькулятор на основе Tkinter."""

    BUTTONS = [
        ("C", 1, 0, 1), ("⌫", 1, 1, 1), ("%", 1, 2, 1), ("/", 1, 3, 1),
        ("7", 2, 0, 1), ("8", 2, 1, 1), ("9", 2, 2, 1), ("*", 2, 3, 1),
        ("4", 3, 0, 1), ("5", 3, 1, 1), ("6", 3, 2, 1), ("-", 3, 3, 1),
        ("1", 4, 0, 1), ("2", 4, 1, 1), ("3", 4, 2, 1), ("+", 4, 3, 1),
        ("0", 5, 0, 2), (".", 5, 2, 1), ("=", 5, 3, 1),
    ]

    def __init__(self, root):
        self.root = root
        self.root.title("Калькулятор")
        self.root.resizable(False, False)
        self.expression = ""
        self.display_var = tk.StringVar(value="0")
        self.create_widgets()
        self.root.bind("<Key>", self.on_key)

    def create_widgets(self):
        display = tk.Entry(self.root, textvariable=self.display_var,
                           font=("Arial", 24), justify="right",
                           bd=6, relief="ridge", state="readonly")
        display.grid(row=0, column=0, columnspan=4, sticky="nsew",
                     padx=5, pady=5)
        for text, row, col, span in self.BUTTONS:
            color = "#f0f0f0"
            if text in "+-*/%":
                color = "#ffd27f"
            elif text == "=":
                color = "#7fd17f"
            elif text in ("C", "⌫"):
                color = "#f28b82"
            btn = tk.Button(self.root, text=text, font=("Arial", 16),
                            bg=color, width=4, height=2,
                            command=lambda t=text: self.press(t))
            btn.grid(row=row, column=col, columnspan=span,
                     sticky="nsew", padx=2, pady=2)

    def update_display(self):
        self.display_var.set(self.expression or "0")

    def press(self, symbol):
        if symbol == "C":
            self.clear()
        elif symbol == "⌫":
            self.backspace()
        elif symbol == "=":
            self.calculate()
        elif symbol == "%":
            self.percent()
        else:
            self.expression += symbol
            self.update_display()

    def clear(self):
        self.expression = ""
        self.update_display()

    def backspace(self):
        self.expression = self.expression[:-1]
        self.update_display()

    def percent(self):
        try:
            value = self.safe_eval(self.expression) / 100
            self.expression = self.format_number(value)
        except (ValueError, SyntaxError, ZeroDivisionError):
            self.expression = ""
        self.update_display()

    @staticmethod
    def safe_eval(expr):
        if not expr or not set(expr) <= ALLOWED:
            raise ValueError("Ошибка в выражении")
        return eval(expr, {"__builtins__": {}}, {})

    @staticmethod
    def format_number(value):
        if isinstance(value, float) and value.is_integer():
            return str(int(value))
        if isinstance(value, float):
            return str(round(value, 10))
        return str(value)

    def calculate(self):
        try:
            result = self.safe_eval(self.expression)
            self.expression = self.format_number(result)
            self.update_display()
        except ZeroDivisionError:
            self.expression = ""
            self.display_var.set("Делить на ноль нельзя")
        except (SyntaxError, ValueError):
            self.expression = ""
            self.display_var.set("Ошибка в выражении")

    def on_key(self, event):
        if event.char in "0123456789+-*/.()":
            self.press(event.char)
        elif event.keysym in ("Return", "KP_Enter"):
            self.calculate()
        elif event.keysym == "BackSpace":
            self.backspace()
        elif event.keysym == "Escape":
            self.clear()


if __name__ == "__main__":
    root = tk.Tk()
    app = Calculator(root)
    root.mainloop()
```

**Разбор кода:**
- `BUTTONS` — список кортежей: (текст, строка, столбец, сколько столбцов занимает). Чтобы изменить интерфейс, достаточно поправить только эту таблицу.
- `@staticmethod` — вспомогательные методы, которым не нужен `self` (из модуля ООП).
- `state="readonly"` — пользователь не может писать на дисплее напрямую, только через кнопки/клавиши.
- `on_key` — `event.char` даёт нажатый символ, `event.keysym` — название клавиши.
- `if __name__ == "__main__":` — если файл импортируется как модуль, окно не открывается само.

## 8. Тестирование

Чтобы назвать приложение готовым, проверяем его систематически:

| № | Действие | Результат |
|---|---|---|
| 1 | `2 + 3 * 4 =` | `14` |
| 2 | `7 / 2 =` | `3.5` |
| 3 | `6 / 2 =` | `3` (а не `3.0`) |
| 4 | `5 / 0 =` | «Делить на ноль нельзя» |
| 5 | `5 + =` | «Ошибка в выражении» |
| 6 | `0.1 + 0.2 =` | `0.3` |
| 7 | `1 2 3 ⌫` | `12` |
| 8 | `C` | `0` |
| 9 | С клавиатуры `9*9` + Enter | `81` |

## Частые ошибки

**1. Переменная цикла «замыкается» внутри lambda**
```python
import tkinter as tk
root = tk.Tk()
for i, text in enumerate(["1", "2", "3"]):
    tk.Button(root, text=text, command=lambda: print(text)).grid(row=0, column=i)
```
Любая кнопка выводит `3`, потому что `lambda` ищет значение `text` в момент нажатия, а к этому времени цикл уже завершён.
Правильно:
```python
import tkinter as tk
root = tk.Tk()
for i, text in enumerate(["1", "2", "3"]):
    tk.Button(root, text=text, command=lambda t=text: print(t)).grid(row=0, column=i)
```

**2. Передача функции в `command` со скобками**
```python
import tkinter as tk
def calculate():
    print("Вычисление")
root = tk.Tk()
tk.Button(root, text="=", command=calculate())   # ошибка: функция выполняется сразу
```
Правильно: `command=calculate` (без скобок).

**3. Отсутствие обработки ошибок** — `eval("5/0")` не «вешает» окно, но в консоли появляется `Traceback`, и результат не отображается. Всегда используйте `try...except`.

**4. Смешивание `grid` и `pack` в одном контейнере** — Tkinter выдаёт ошибку или зависает. В одном окне (или внутри одного `Frame`) используйте только один менеджер.

**5. Забыли преобразовать результат в строку** — если написать `self.expression = result` (число), следующая операция `+=` вызовет `TypeError`. Всегда преобразуйте результат в строку через `str()`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Техническое задание (ТЗ) | Список требований к программе |
| Макет (прототип) | Предварительный эскиз интерфейса |
| `lambda` | Однострочная анонимная функция |
| `eval()` | Функция, вычисляющая выражение Python из строки (использовать осторожно) |
| `columnspan` | Занятие виджетом нескольких столбцов в `grid` |
| `sticky` | Направление растяжения виджета внутри ячейки (`n`, `s`, `e`, `w`) |
| `event.char` / `event.keysym` | Символ / название нажатой клавиши |
| Таблица тестирования | Список действий и ожидаемых результатов |

## Примечание

- `eval()` выполняет любой код Python из строки. В калькуляторе применяйте его только к заранее проверенной строке: например, проверьте, что в ней только разрешённые символы: `set(expr) <= set("0123456789.+-*/() ")`.
- Оператор `/` всегда возвращает `float`: `6 / 2` → `3.0`. Чтобы убрать лишнее `.0`, можно использовать метод `result.is_integer()`.
- Дробные числа хранятся в двоичной системе неточно: `0.1 + 0.2` → `0.30000000000000004`. При выводе используйте `round(result, 10)`, а для точных вычислений (деньги) есть модуль `decimal.Decimal`.
- По PEP 8 имена классов пишутся в стиле `CapWords` (`Calculator`), а методы и переменные — в стиле `snake_case` (`create_widgets`).
- Официальная документация Tkinter: https://docs.python.org/3/library/tkinter.html
