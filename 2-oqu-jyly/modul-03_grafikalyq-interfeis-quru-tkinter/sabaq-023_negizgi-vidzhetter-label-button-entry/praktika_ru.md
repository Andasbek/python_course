# Урок 23. Практические задания: Label, Button, Entry

## Базовый уровень

### Задание 1. Приветствие
При вводе имени в поле Entry и нажатии «Поздороваться» в Label должно выводиться «Привет, <имя>! Добро пожаловать в кружок Python!». Если поле пустое — красным цветом «Напишите своё имя».

### Задание 2. Обмен тенге на доллары
Entry — сумма в тенге, Button — «Обменять», Label — результат. Курс задаётся в коде константой (например, `RATE = 520` тг/$). Результат с двумя знаками после запятой: «10000 тг = 19.23 $». Если введено не число — сообщение об ошибке.

### Задание 3. Квадрат и куб
В Entry вводится целое число. Две кнопки: «Квадрат» и «Куб». Результат выводится в одном Label.

Пример: `7` → «7² = 49» или «7³ = 343».

### Задание 4. Пароль
Поле «Пароль» (`show="*"`) и кнопка «Показать/Скрыть». При нажатии кнопки символы в поле должны становиться видимыми/скрытыми (`entry.config(show="")`).

## Продвинутый уровень

### Задание 5. Надёжность пароля
Кнопка, проверяющая введённый пароль. Правила: длина ≥ 8, хотя бы одна цифра, хотя бы одна заглавная буква, хотя бы один специальный символ (`!@#$%`). Результат: «Слабый» (красный), «Средний» (оранжевый), «Надёжный» (зелёный) — в зависимости от количества выполненных правил (0–2, 3, 4).

### Задание 6. Игра «Угадай число»
Программа загадывает случайное число от 1 до 100. Пользователь пишет число в Entry и нажимает «Проверить». Ответ: «Больше», «Меньше» или «Угадали! За N попыток». После угадывания кнопка «Проверить» становится `disabled`, а кнопка «Новая игра» начинает игру заново.

### Задание 7. Живой счётчик через StringVar
При каждом вводе текста в Entry ниже автоматически обновляется «Символов: N, слов: M». Подсказка: `var.trace_add("write", func)` — при каждом изменении переменной вызывается `func(*args)`.

## ★ Олимпиадная / творческая задача

### Задание 8. Проверка ИИН
ИИН гражданина Казахстана состоит из 12 цифр: первые 6 цифр — дата рождения (ГГММДД), 7-я цифра обозначает век и пол (3 — XX в. мужчина, 4 — XX в. женщина, 5 — XXI в. мужчина, 6 — XXI в. женщина). Напишите GUI, определяющий по введённому в Entry ИИН дату рождения (ДД.ММ.ГГГГ) и пол. Если введено не 12 цифр — ошибка. (Проверка контрольной цифры — дополнительный балл.) Примечание: проверяйте только на выдуманных ИИН!

## Домашнее задание

1. Завершите задание 6 и загрузите на GitHub под именем `gui/lesson23_guess.py`.
2. Придумайте окно «Анкета»: поля имя, фамилия, возраст, город и кнопка «Отправить» — все данные выводятся одним текстом в Label ниже.
3. Подумайте: виджеты, выстроенные друг под другом, неудобны. Как разместить их в виде таблицы?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import tkinter as tk


def greet():
    name = entry.get().strip()
    if name:
        result.config(text=f"Привет, {name}! Добро пожаловать в кружок Python!", fg="black")
    else:
        result.config(text="Напишите своё имя", fg="red")


root = tk.Tk()
root.title("Приветствие")
entry = tk.Entry(root, width=30)
entry.pack(padx=20, pady=10)
tk.Button(root, text="Поздороваться", command=greet).pack()
result = tk.Label(root, text="")
result.pack(pady=10)
root.mainloop()
```

**Задание 2**
```python
import tkinter as tk

RATE = 520


def convert():
    try:
        kzt = float(entry.get())
    except ValueError:
        result.config(text="Сумма должна быть числом", fg="red")
        return
    result.config(text=f"{kzt:g} тг = {kzt / RATE:.2f} $", fg="black")


root = tk.Tk()
root.title("Обмен")
tk.Label(root, text=f"Курс: 1 $ = {RATE} тг").pack(pady=5)
entry = tk.Entry(root, justify="center")
entry.pack(padx=20)
tk.Button(root, text="Обменять", command=convert).pack(pady=5)
result = tk.Label(root, text="", font=("Arial", 14))
result.pack(pady=5)
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk


def read_int():
    try:
        return int(entry.get())
    except ValueError:
        result.config(text="Введите целое число")
        return None


def square():
    n = read_int()
    if n is not None:
        result.config(text=f"{n}² = {n ** 2}")


def cube():
    n = read_int()
    if n is not None:
        result.config(text=f"{n}³ = {n ** 3}")


root = tk.Tk()
root.title("Степень")
entry = tk.Entry(root, justify="center")
entry.pack(pady=10, padx=20)
tk.Button(root, text="Квадрат", command=square).pack(side="left", padx=10, pady=5)
tk.Button(root, text="Куб", command=cube).pack(side="left", padx=10, pady=5)
result = tk.Label(root, text="", font=("Arial", 16))
result.pack(side="left", padx=10)
root.mainloop()
```

**Задание 4**
```python
import tkinter as tk


def toggle():
    if entry.cget("show") == "*":
        entry.config(show="")
        btn.config(text="Скрыть")
    else:
        entry.config(show="*")
        btn.config(text="Показать")


root = tk.Tk()
root.title("Пароль")
entry = tk.Entry(root, show="*", width=25)
entry.pack(side="left", padx=10, pady=20)
btn = tk.Button(root, text="Показать", command=toggle, width=8)
btn.pack(side="left", padx=10)
root.mainloop()
```

**Задание 5**
```python
import tkinter as tk


def check():
    p = entry.get()
    rules = [
        len(p) >= 8,
        any(ch.isdigit() for ch in p),
        any(ch.isupper() for ch in p),
        any(ch in "!@#$%" for ch in p),
    ]
    score = sum(rules)
    if score == 4:
        result.config(text="Надёжный", fg="green")
    elif score == 3:
        result.config(text="Средний", fg="orange")
    else:
        result.config(text="Слабый", fg="red")


root = tk.Tk()
root.title("Надёжность")
entry = tk.Entry(root, show="*", width=25)
entry.pack(padx=20, pady=10)
tk.Button(root, text="Проверить", command=check).pack()
result = tk.Label(root, text="", font=("Arial", 16, "bold"))
result.pack(pady=10)
root.mainloop()
```

**Задание 6**
```python
import random
import tkinter as tk

state = {"secret": random.randint(1, 100), "tries": 0}


def check():
    try:
        n = int(entry.get())
    except ValueError:
        info.config(text="Введите число!")
        return
    state["tries"] += 1
    if n < state["secret"]:
        info.config(text="Больше")
    elif n > state["secret"]:
        info.config(text="Меньше")
    else:
        info.config(text=f"Угадали! За {state['tries']} попыток")
        check_btn.config(state="disabled")
    entry.delete(0, tk.END)


def new_game():
    state["secret"] = random.randint(1, 100)
    state["tries"] = 0
    info.config(text="Угадай число от 1 до 100")
    check_btn.config(state="normal")


root = tk.Tk()
root.title("Угадай число")
info = tk.Label(root, text="Угадай число от 1 до 100", font=("Arial", 13))
info.pack(pady=10, padx=20)
entry = tk.Entry(root, justify="center")
entry.pack()
check_btn = tk.Button(root, text="Проверить", command=check)
check_btn.pack(pady=5)
tk.Button(root, text="Новая игра", command=new_game).pack(pady=5)
root.mainloop()
```

**Задание 7**
```python
import tkinter as tk


def update(*args):
    text = var.get()
    stats.config(text=f"Символов: {len(text)}, слов: {len(text.split())}")


root = tk.Tk()
root.title("Счётчик")
var = tk.StringVar()
var.trace_add("write", update)
tk.Entry(root, textvariable=var, width=40).pack(padx=10, pady=10)
stats = tk.Label(root, text="Символов: 0, слов: 0")
stats.pack(pady=5)
root.mainloop()
```

**Задание 8**
```python
import tkinter as tk

CENTURY = {"1": (1800, "мужской"), "2": (1800, "женский"), "3": (1900, "мужской"),
           "4": (1900, "женский"), "5": (2000, "мужской"), "6": (2000, "женский")}


def control_digit(iin):
    d = [int(c) for c in iin]
    w1 = list(range(1, 12))
    s = sum(a * b for a, b in zip(d[:11], w1)) % 11
    if s == 10:
        w2 = [3, 4, 5, 6, 7, 8, 9, 10, 11, 1, 2]
        s = sum(a * b for a, b in zip(d[:11], w2)) % 11
    return s


def check():
    iin = entry.get().strip()
    if len(iin) != 12 or not iin.isdigit():
        result.config(text="ИИН должен состоять из 12 цифр", fg="red")
        return
    if iin[6] not in CENTURY:
        result.config(text="Неверная 7-я цифра", fg="red")
        return
    base, gender = CENTURY[iin[6]]
    year = base + int(iin[0:2])
    month, day = iin[2:4], iin[4:6]
    ok = "верна" if control_digit(iin) == int(iin[11]) else "не совпадает"
    result.config(text=f"Дата рождения: {day}.{month}.{year}\nПол: {gender}\n"
                       f"Контрольная цифра: {ok}", fg="black")


root = tk.Tk()
root.title("Проверка ИИН")
entry = tk.Entry(root, width=20, justify="center", font=("Consolas", 14))
entry.pack(padx=20, pady=10)
tk.Button(root, text="Проверить", command=check).pack()
result = tk.Label(root, text="", justify="left")
result.pack(pady=10)
root.mainloop()
```

</details>
