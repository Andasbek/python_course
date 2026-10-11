# Урок 106. Практические задания: диагностика и станции консультаций

## Базовый уровень

### Задание 1. Диагностический тест (15 мин, без компьютера)
Запишите ответ на каждый вопрос. Затем проверьте себя по ключу, который даст педагог, и отметьте темы с ошибками — это ваш маршрут по станциям.

**А. Ссылки**
1. Что будет выведено?
```python
x = [1, 2]
y = x
y += [3]
print(x)
```
2. В чём опасность записи `def f(items=[]):`?

**Б. `self`, атрибуты**
3. Что будет выведено?
```python
class Box:
    things = []

    def put(self, t):
        self.things.append(t)


a, b = Box(), Box()
a.put("мяч")
print(b.things)
```
4. Как записать вызов `obj.method(5)` через класс?

**В. lambda в цикле**
5. Что будет выведено?
```python
fs = [lambda: k * 10 for k in range(1, 4)]
print([f() for f in fs])
```
6. Как исправить этот код, чтобы выводилось `[10, 20, 30]`?

**Г. Цикл событий**
7. Что произойдёт с окном, если при нажатии кнопки выполняется `time.sleep(3)`?
8. Какой метод в Tkinter используется, чтобы вызывать функцию каждые 500 мс?

**Д. JOIN**
9. У 2 из 5 учеников нет кружка (`club_id` = NULL). Сколько строк даст `student JOIN club`? А `student LEFT JOIN club`?
10. Как посчитать количество учеников в каждом кружке, включая пустые кружки?

**Е. Вложенный JSON**
11. `data = {"items": [{"name": "A", "tags": ["x", "y"]}]}`. Как получить значение `"y"`?
12. Как прочитать значение без `KeyError`, если ключа может не быть?

**Ж. Выборка в Pandas**
13. Выберите строки, где столбец `temp` больше 0 **и** `city` равен «Астана».
14. В чём разница между `df.iloc[0]` и `df.loc[0]`?

**З. Конфликт Git**
15. Какой вариант находится между `<<<<<<< HEAD` и `=======`?
16. Какие две команды выполняются после исправления конфликта?

### Задание 2. Карточки станций
По каждой теме вашего маршрута: прочитайте объяснение в теории → запустите пример (также в Python Tutor) → выполните задание станции ниже.

**Станция А.** Функция принимает список учеников и возвращает **новый** список, в котором к имени каждого ученика добавлено «(проверено)», но исходный список не изменяется. Проверка: после вызова функции выведите исходный список.

**Станция Б.** В классе `Team` у каждой команды должен быть свой список игроков, а `league = "Школьная лига"` — общий для всех команд. Создайте две команды, добавьте игрока в одну из них — покажите, что во второй он не появился.

**Станция В.** В Tkinter с помощью цикла создайте пять кнопок от 1 до 5; при нажатии каждой кнопки в метке появляется «Вы выбрали N».

**Станция Г.** Сделайте в Tkinter таймер обратного отсчёта от 10 до 0 (кнопка «Старт»); окно не должно зависать во время отсчёта; при 0 выводится «Время вышло!».

**Станция Д.** Создайте таблицы `author(id, name)` и `book(id, title, author_id)`, добавьте также автора без книг. Выведите количество книг каждого автора (включая 0).

**Станция Е.** Из ответа API ниже выведите парами дату и максимальную температуру каждого дня, затем найдите самый холодный день.
```text
{"daily": {"time": ["2027-05-20", "2027-05-21", "2027-05-22"],
           "temperature_2m_max": [24.1, 19.7, 22.3]}}
```

**Станция Ж.** В таблице `df` есть столбцы `name`, `grade` (класс), `score`. Выведите списком имена учеников 8-го класса, набравших больше 80 баллов, а все баллы меньше 0 замените на `None`.

**Станция З.** В учебном репозитории, подготовленном педагогом (`conflict-practice`), слейте ветки `main` и `feature` (`git merge feature`), разрешите конфликт и закоммитьте результат.

## Продвинутый уровень

### Задание 3. Стать наставником
Станьте наставником на станции по теме, за которую на диагностике получили 2/2. Правила: вы не берёте клавиатуру; не говорите ответ — задаёте наводящие вопросы («Чему равно `k` в этой строке?», «Что показывает `type()`?»). В конце урока напишите 2 предложения: какой ваш вопрос помог больше всего?

### Задание 4. Свой пример-ловушка
По теме своего маршрута **сами** придумайте код-«ловушку» (5–8 строк): на первый взгляд правильный, но дающий неожиданный результат. Дайте его решить однокласснику.

## ★ Олимпиадная / творческая задача

### Задание 5. Глубокая копия
Составьте пример, показывающий разницу между `copy.copy` и `copy.deepcopy` (списки внутри списков), а затем напишите свою рекурсивную функцию, которая без модуля `copy` делает глубокую копию вложенных списков и словарей.

### Задание 6. Поисковик путей в JSON
Напишите функцию, которая для любой вложенной структуры JSON выводит полный путь ко всем «листовым» значениям: `data["list"][1]["main"]["temp"] = 23.1`. Это будет удобный инструмент для изучения ответов API.

## Домашнее задание
1. Ответьте на вопросы повторной проверки по теме вашего маршрута (их даст педагог).
2. Проверьте, нет ли в вашем итоговом проекте ошибок по этим темам (например, изменяемый параметр по умолчанию, `lambda` в цикле).
3. К следующему уроку: составьте список того, что не доделано в вашем проекте (Issues).

## Для педагога: решения
<details><summary>Показать решения</summary>

**Ключ к диагностике.**
1. `[1, 2, 3]` — `+=` изменяет список на месте, `y` и `x` — один объект.
2. Список создаётся один раз при определении функции и является общим для всех вызовов.
3. `['мяч']` — `things` является атрибутом класса, общим для всех объектов.
4. `Class.method(obj, 5)`.
5. `[30, 30, 30]`.
6. `[lambda k=k: k * 10 for k in range(1, 4)]`.
7. Окно «зависнет» на 3 секунды (не отвечает, не перерисовывается).
8. `root.after(500, func)` (с повторным вызовом внутри функции).
9. `JOIN` — 3 строки; `LEFT JOIN` — 5 строк.
10. `SELECT c.title, COUNT(s.id) FROM club c LEFT JOIN student s ON s.club_id = c.id GROUP BY c.id`.
11. `data["items"][0]["tags"][1]`.
12. `d.get("ключ", значение_по_умолчанию)`.
13. `df[(df["temp"] > 0) & (df["city"] == "Астана")]`.
14. `iloc` — по позиции (0-я строка), `loc` — по метке индекса (строка с меткой 0); после сортировки/фильтрации они могут различаться.
15. Вариант текущей ветки (наш).
16. `git add <файл>` и `git commit`.

Оценивание: 0/2 или 1/2 по теме — тема добавляется в маршрут; 2/2 — ученику предлагается стать наставником.

**Станция А.**
```python
def mark_checked(names):
    """Возвращает новый список; исходный список не изменяется."""
    return [name + " (проверено)" for name in names]


students = ["Аян", "Бибігүл"]
checked = mark_checked(students)
print(students)
print(checked)
```
Результат:
```text
['Аян', 'Бибігүл']
['Аян (проверено)', 'Бибігүл (проверено)']
```

**Станция Б.**
```python
class Team:
    league = "Школьная лига"

    def __init__(self, name):
        self.name = name
        self.players = []


a, b = Team("Барыс"), Team("Қыран")
a.players.append("Ерлан")
print(a.players, b.players, a.league == b.league)
```
Результат:
```text
['Ерлан'] [] True
```

**Станция В.**
```python
import tkinter as tk

root = tk.Tk()
root.title("Выбор")
label = tk.Label(root, text="Выберите число", font=("Arial", 14))
label.pack(pady=8)
frame = tk.Frame(root)
frame.pack(padx=10, pady=10)
for n in range(1, 6):
    tk.Button(frame, text=str(n), width=4,
              command=lambda n=n: label.config(text=f"Вы выбрали {n}")
              ).pack(side="left", padx=3)
root.mainloop()
```

**Станция Г.**
```python
import tkinter as tk

root = tk.Tk()
root.title("Обратный отсчёт")
label = tk.Label(root, text="10", font=("Arial", 32))
label.pack(padx=40, pady=10)


def countdown(n):
    if n < 0:
        label.config(text="Время вышло!")
        start_button.config(state="normal")
        return
    label.config(text=str(n))
    root.after(1000, countdown, n - 1)     # after передаёт дополнительные аргументы функции


def start():
    start_button.config(state="disabled")  # чтобы повторным нажатием не запустить два таймера
    countdown(10)


start_button = tk.Button(root, text="Старт", command=start)
start_button.pack(pady=10)
root.mainloop()
```

**Станция Д.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE author (id INTEGER PRIMARY KEY, name TEXT);
CREATE TABLE book (id INTEGER PRIMARY KEY, title TEXT, author_id INTEGER REFERENCES author(id));
INSERT INTO author VALUES (1, 'М. Ауэзов'), (2, 'Г. Мусрепов'), (3, 'Новый автор');
INSERT INTO book (title, author_id) VALUES
    ('Путь Абая', 1), ('Лихая година', 1), ('Улпан', 2);
""")
for name, count in conn.execute("""
        SELECT a.name, COUNT(b.id) FROM author a
        LEFT JOIN book b ON b.author_id = a.id
        GROUP BY a.id ORDER BY a.id"""):
    print(f"{name}: {count}")
```
Результат:
```text
М. Ауэзов: 2
Г. Мусрепов: 1
Новый автор: 0
```

**Станция Е.**
```python
import json

text = """{"daily": {"time": ["2027-05-20", "2027-05-21", "2027-05-22"],
           "temperature_2m_max": [24.1, 19.7, 22.3]}}"""
daily = json.loads(text)["daily"]
pairs = list(zip(daily["time"], daily["temperature_2m_max"]))
for day, t in pairs:
    print(day, t)
coldest = min(pairs, key=lambda p: p[1])
print("Самый холодный день:", coldest[0])
```
Результат:
```text
2027-05-20 24.1
2027-05-21 19.7
2027-05-22 22.3
Самый холодный день: 2027-05-21
```

**Станция Ж.**
```python
import pandas as pd

df = pd.DataFrame({"name": ["Аян", "Бибігүл", "Дәулет", "Есет"],
                   "grade": [8, 8, 9, 8],
                   "score": [85, 78, 92, -1]})
best = df.loc[(df["grade"] == 8) & (df["score"] > 80), "name"].tolist()
print(best)
df.loc[df["score"] < 0, "score"] = None
print(df["score"].tolist())
```
Результат:
```text
['Аян']
[85.0, 78.0, 92.0, nan]
```
(При записи `None` столбец переходит в тип `float`, а пустое значение отображается как `nan` — полезно для обсуждения.)

**Станция З.** Подготовка учебного репозитория (для педагога):
```bash
git init conflict-practice
cd conflict-practice
echo "GAME_SPEED = 5" > settings.py
git add . && git commit -m "Начальная настройка"
git switch -c feature
echo "GAME_SPEED = 8" > settings.py
git commit -am "Скорость 8"
git switch main
echo "GAME_SPEED = 6" > settings.py
git commit -am "Скорость 6"
git merge feature        # -> CONFLICT (content): Merge conflict in settings.py
```
(Примечание: команда `git switch main` работает, когда ветка по умолчанию называется `main`; в старых версиях Git она может называться `master` — проверьте через `git branch`.)

**Задание 5.**
```python
import copy

board = [[0, 0], [0, 0]]
shallow = copy.copy(board)
deep = copy.deepcopy(board)
board[0][0] = 1
print(shallow[0][0], deep[0][0])     # 1 0 — поверхностная копия разделяет внутренние списки


def deep_copy(obj):
    """Рекурсивно копирует списки и словари."""
    if isinstance(obj, list):
        return [deep_copy(item) for item in obj]
    if isinstance(obj, dict):
        return {key: deep_copy(value) for key, value in obj.items()}
    return obj                         # число, строка и т.п. — неизменяемые, копировать не нужно


data = {"scores": [[1, 2], [3]], "name": "Аян"}
clone = deep_copy(data)
data["scores"][0].append(99)
print(clone["scores"])
```
Результат:
```text
1 0
[[1, 2], [3]]
```

**Задание 6.**
```python
def leaf_paths(obj, path="data"):
    """Выдаёт пути ко всем листовым значениям вложенной структуры."""
    if isinstance(obj, dict):
        for key, value in obj.items():
            yield from leaf_paths(value, f'{path}["{key}"]')
    elif isinstance(obj, list):
        for i, value in enumerate(obj):
            yield from leaf_paths(value, f"{path}[{i}]")
    else:
        yield path, obj


answer = {"list": [{"main": {"temp": 21.5}}, {"main": {"temp": 23.1}}], "ok": True}
for p, value in leaf_paths(answer):
    print(p, "=", value)
```
Результат:
```text
data["list"][0]["main"]["temp"] = 21.5
data["list"][1]["main"]["temp"] = 23.1
data["ok"] = True
```
</details>
