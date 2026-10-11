# Урок 21. Практические задания: GUI и знакомство с Tkinter

## Базовый уровень

### Задание 1. CLI или GUI?
Заполните таблицу (в тетради): приложение → вид интерфейса → способ ввода.
`Калькулятор (Windows)`, `git`, `pip install`, `Telegram`, `Minecraft`, `IDLE Shell`, `Kaspi.kz`, `python -m venv venv`, `Блокнот`, `eGov.kz`.

### Задание 2. Первое окно
Откройте окно с заголовком «Кружок Python» и выведите в нём две надписи (`Label`): своё имя и «Алматы, 2026». Каждой надписи задайте `font=("Arial", 16)`.

### Задание 3. Кнопка — print
Создайте окно с кнопкой «Написать в консоль». При каждом нажатии в консоль должно выводиться «Кнопка нажата!». Нажмите кнопку 5 раз и понаблюдайте за консолью.

### Задание 4. Счётчик
В окне должны быть надпись «0» и кнопка «+1». При каждом нажатии число увеличивается на 1 (`label.config(text=...)`). Подсказка: храните счётчик в глобальной переменной или в списке.

## Продвинутый уровень

### Задание 5. Несколько кнопок
В окне должны быть кнопки «Қазақша», «Русский», «English» и одна надпись. В зависимости от нажатой кнопки надпись меняется на «Сәлеметсіз бе!», «Здравствуйте!», «Hello!».

### Задание 6. Из консоли в GUI
Возьмите простую консольную программу, написанную в 1-й год (например, расчёт BMI или перевод тиынов в тенге). Вынесите вычисления в функцию и сделайте GUI-версию, как в примере с температурой из теории (Entry + Button + Label).

## ★ Олимпиадная / творческая задача

### Задание 7. «Волшебный шар»
Окно с надписью «Задумайте вопрос и нажмите кнопку» и кнопкой «Получить ответ». При нажатии выводится случайный ответ («Да», «Нет», «Конечно!», «Спроси позже», «Сомневаюсь») (`random.choice`). Дополнительно: меняйте цвет ответа (`fg="green"` — для положительного, `fg="red"` — для отрицательного).

## Домашнее задание

1. Выполните на домашнем компьютере команду `python -m tkinter`.
2. Добавьте к счётчику из задания 4 кнопки «−1» и «Сбросить».
3. Загрузите код на GitHub под именем `gui/lesson21_counter.py`.
4. Подумайте: как можно изменить размер, цвет, положение окна?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.** GUI: Калькулятор, Telegram, Minecraft, Kaspi.kz (мобильный/веб GUI), Блокнот, eGov.kz (веб). CLI: git, pip install, python -m venv venv. IDLE Shell — интерактивная консоль внутри оконной программы (смешанный тип).

**Задание 2**
```python
import tkinter as tk

root = tk.Tk()
root.title("Кружок Python")
tk.Label(root, text="Ару Серікқызы", font=("Arial", 16)).pack(padx=20, pady=5)
tk.Label(root, text="Алматы, 2026", font=("Arial", 16)).pack(padx=20, pady=5)
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk


def on_click():
    print("Кнопка нажата!")


root = tk.Tk()
root.title("Консоль")
tk.Button(root, text="Написать в консоль", command=on_click).pack(padx=30, pady=20)
root.mainloop()
```

**Задание 4** (вместе с домашним заданием)
```python
import tkinter as tk

count = 0


def change(delta):
    global count
    count += delta
    label.config(text=str(count))


def reset():
    global count
    count = 0
    label.config(text="0")


root = tk.Tk()
root.title("Счётчик")
label = tk.Label(root, text="0", font=("Arial", 32))
label.pack(padx=40, pady=10)
tk.Button(root, text="+1", command=lambda: change(1)).pack(fill="x")
tk.Button(root, text="−1", command=lambda: change(-1)).pack(fill="x")
tk.Button(root, text="Сбросить", command=reset).pack(fill="x")
root.mainloop()
```
Примечание: `lambda` — способ передать в `command` функцию с аргументом (подробно объясняется на 25-м уроке).

**Задание 5**
```python
import tkinter as tk


def kz():
    label.config(text="Сәлеметсіз бе!")


def ru():
    label.config(text="Здравствуйте!")


def en():
    label.config(text="Hello!")


root = tk.Tk()
root.title("Языки")
label = tk.Label(root, text="Выберите язык", font=("Arial", 18))
label.pack(pady=10)
tk.Button(root, text="Қазақша", command=kz).pack(side="left", padx=5, pady=5)
tk.Button(root, text="Русский", command=ru).pack(side="left", padx=5, pady=5)
tk.Button(root, text="English", command=en).pack(side="left", padx=5, pady=5)
root.mainloop()
```

**Задание 6** (пример с BMI)
```python
import tkinter as tk


def bmi(weight, height_cm):
    h = height_cm / 100
    return round(weight / (h * h), 1)


def calculate():
    w = float(weight_entry.get())
    h = float(height_entry.get())
    result.config(text=f"BMI = {bmi(w, h)}")


root = tk.Tk()
root.title("BMI")
tk.Label(root, text="Вес (кг):").pack()
weight_entry = tk.Entry(root)
weight_entry.pack()
tk.Label(root, text="Рост (см):").pack()
height_entry = tk.Entry(root)
height_entry.pack()
tk.Button(root, text="Вычислить", command=calculate).pack(pady=5)
result = tk.Label(root, text="")
result.pack()
root.mainloop()
```

**Задание 7**
```python
import random
import tkinter as tk

POSITIVE = ["Да", "Конечно!"]
NEGATIVE = ["Нет", "Сомневаюсь"]
NEUTRAL = ["Спроси позже"]


def answer():
    text = random.choice(POSITIVE + NEGATIVE + NEUTRAL)
    if text in POSITIVE:
        color = "green"
    elif text in NEGATIVE:
        color = "red"
    else:
        color = "gray"
    result.config(text=text, fg=color)


root = tk.Tk()
root.title("Волшебный шар")
tk.Label(root, text="Задумайте вопрос и нажмите кнопку").pack(padx=20, pady=10)
tk.Button(root, text="Получить ответ", command=answer).pack()
result = tk.Label(root, text="?", font=("Arial", 28, "bold"))
result.pack(pady=15)
root.mainloop()
```

</details>
