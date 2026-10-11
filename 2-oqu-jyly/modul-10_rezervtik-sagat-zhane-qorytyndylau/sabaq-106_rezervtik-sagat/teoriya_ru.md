# Урок 106. Резервный час. Индивидуальные консультации по сложным темам.

## Темы

1. Объекты, ссылки и изменяемые значения
2. `self` и атрибуты класса/объекта
3. `lambda` в цикле (кнопки Tkinter)
4. Цикл событий: почему окно «зависает»?
5. Виды `JOIN`
6. Поиск пути во вложенном JSON (ответы API)
7. Pandas: выборка строк и столбцов
8. Git: конфликт слияния (merge conflict)
9. Частые ошибки
10. Основные понятия

## 1. Объекты, ссылки и изменяемые значения

**Главная идея:** переменная — это не сам объект, а **наклеенная на него метка (ссылка)**. `b = a` не создаёт копию, а наклеивает вторую метку на тот же объект.

```python
a = [1, 2, 3]
b = a              # вторая метка на тот же список
b.append(4)
print(a)           # a тоже изменился!
c = a.copy()       # новый список (копия)
c.append(5)
print(a, c)
print(a is b, a is c)
```

Результат:
```text
[1, 2, 3, 4]
[1, 2, 3, 4] [1, 2, 3, 4, 5]
True False
```

**Типичная ошибка — изменяемый параметр по умолчанию:**
```python
def add_score(score, scores=[]):       # список создаётся ОДИН РАЗ, при определении функции
    scores.append(score)
    return scores


print(add_score(10))
print(add_score(20))                   # неожиданный результат!
```
Результат:
```text
[10]
[10, 20]
```
Правильно:
```python
def add_score(score, scores=None):
    if scores is None:
        scores = []
    scores.append(score)
    return scores


print(add_score(10), add_score(20))
```
Результат:
```text
[10] [20]
```

> Запустите этот код в Python Tutor и посмотрите на стрелки (ссылки) своими глазами — это самый быстрый способ понять тему.

## 2. `self` и атрибуты класса/объекта

**Аналогия:** класс — форма для выпекания торта; объект — конкретный торт, испечённый в этой форме. `self` — «именно этот торт». При вызове метода Python автоматически передаёт в `self` сам объект: `cake.decorate("цветок")` ≡ `Cake.decorate(cake, "цветок")`.

```python
class Student:
    school = "Школа №25"             # атрибут класса: общий для ВСЕХ объектов

    def __init__(self, name):
        self.name = name             # атрибут объекта: свой у каждого объекта
        self.grades = []             # СВОЙ список у каждого ученика

    def add_grade(self, grade):
        self.grades.append(grade)


a = Student("Аян")
b = Student("Бибігүл")
a.add_grade(5)
print(a.grades, b.grades)
Student.school = "Школа №120"
print(a.school, b.school)
```

Результат:
```text
[5] []
Школа №120 Школа №120
```

Правило: изменяемые данные (списки, словари) — **только внутри `__init__` через `self.`**.

## 3. `lambda` в цикле (кнопки Tkinter)

```python
buttons = []
for i in range(3):
    buttons.append(lambda: print("Кнопка", i))   # запоминается не значение i, а ПЕРЕМЕННАЯ i
for b in buttons:
    b()
```

Результат:
```text
Кнопка 2
Кнопка 2
Кнопка 2
```

`lambda` ищет значение `i` в **момент** вызова — а цикл уже закончился, `i = 2`. Решение — «зафиксировать» значение через параметр по умолчанию:

```python
buttons = [lambda n=i: print("Кнопка", n) for i in range(3)]
for b in buttons:
    b()
```

Результат:
```text
Кнопка 0
Кнопка 1
Кнопка 2
```

В Tkinter: `tk.Button(root, text=name, command=lambda n=name: show(n))`.

## 4. Цикл событий: почему окно «зависает»?

Tkinter (`mainloop`) и Pygame (`while running`) — оба являются **циклом**: «ждать событие → обработать → отрисовать экран → снова». Если функция-обработчик работает долго (`time.sleep(5)`, бесконечный `while`, медленный API), цикл останавливается — окно не отвечает.

```python
import tkinter as tk

root = tk.Tk()
label = tk.Label(root, text="0", font=("Arial", 24))
label.pack(padx=40, pady=20)
seconds = 0


def tick():
    """Каждую секунду заново планирует сама себя — цикл не останавливается."""
    global seconds
    seconds += 1
    label.config(text=str(seconds))
    root.after(1000, tick)          # ОШИБКОЙ было бы: while True: time.sleep(1)


root.after(1000, tick)
root.mainloop()
```

Правило: в Tkinter повторяющееся действие — `after()`, долгое действие — отдельный поток (`threading`); в Pygame — проверка времени внутри цикла через `pygame.time.get_ticks()`, а не `sleep`.

## 5. Виды `JOIN`

```text
student                    club
id | name    | club_id     id | title
1  | Аян     | 1           1  | Python
2  | Бибігүл | 2           2  | Шахматы
3  | Дәулет  | NULL        3  | Робототехника

INNER JOIN — только те, у кого есть пара:  Аян–Python, Бибігүл–Шахматы
LEFT JOIN (student слева) —   + Дәулет–NULL (выводится и ученик без кружка)
LEFT JOIN (club слева) —      + NULL–Робототехника (кружок без учеников)
```

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE club (id INTEGER PRIMARY KEY, title TEXT);
CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT, club_id INTEGER);
INSERT INTO club VALUES (1, 'Python'), (2, 'Шахматы'), (3, 'Робототехника');
INSERT INTO student VALUES (1, 'Аян', 1), (2, 'Бибігүл', 2), (3, 'Дәулет', NULL);
""")
print(conn.execute("""SELECT c.title, COUNT(s.id) FROM club c
                      LEFT JOIN student s ON s.club_id = c.id
                      GROUP BY c.id""").fetchall())
print(conn.execute("""SELECT c.title, COUNT(s.id) FROM club c
                      JOIN student s ON s.club_id = c.id
                      GROUP BY c.id""").fetchall())
```

Результат:
```text
[('Python', 1), ('Шахматы', 1), ('Робототехника', 0)]
[('Python', 1), ('Шахматы', 1)]
```

Правило: вопрос «**все** кружки, даже пустые» → `LEFT JOIN` и `COUNT(s.id)` (а не `COUNT(*)` — он считает и пустую строку).

## 6. Поиск пути во вложенном JSON (ответы API)

Ответ API — это «матрёшка» из словарей и списков. Чтобы добраться до нужного значения, записываем путь шаг за шагом **снаружи внутрь**:

```python
answer = {
    "city": {"name": "Астана", "coord": {"lat": 51.17, "lon": 71.45}},
    "list": [
        {"dt_txt": "2027-05-20 12:00", "main": {"temp": 21.5}, "weather": [{"description": "ясно"}]},
        {"dt_txt": "2027-05-20 15:00", "main": {"temp": 23.1}, "weather": [{"description": "облачно"}]},
    ],
}
# answer -> "list" (список) -> [1] (второй элемент) -> "weather" (список) -> [0] -> "description"
print(answer["list"][1]["weather"][0]["description"])
print(answer["city"]["coord"]["lat"])
temps = [item["main"]["temp"] for item in answer["list"]]
print(max(temps))
print(answer.get("country", "неизвестно"))         # если ключа нет — без ошибки
```

Результат:
```text
облачно
51.17
23.1
неизвестно
```

Метод: красиво выведите ответ через `print(json.dumps(answer, ensure_ascii=False, indent=2))` и на каждом уровне проверяйте `type(...)`: если `dict` — `["ключ"]`, если `list` — `[индекс]` или цикл.

## 7. Pandas: выборка строк и столбцов

| Что нужно | Код |
|---|---|
| Один столбец (Series) | `df["temp"]` |
| Несколько столбцов | `df[["city", "temp"]]` |
| Строки по условию | `df[df["temp"] > 0]` |
| Два условия | `df[(df["temp"] > 0) & (df["city"] == "Алматы")]` — скобки обязательны, `&`, а не `and` |
| По метке | `df.loc[2, "temp"]`, `df.loc[df["temp"] > 0, "city"]` |
| По позиции | `df.iloc[0]`, `df.iloc[:3, 1]` |
| Изменение значения | `df.loc[df["temp"] < -40, "temp"] = None` |

```python
import pandas as pd

df = pd.DataFrame({"city": ["Алматы", "Астана", "Актау", "Алматы"],
                   "temp": [12.5, -3.0, 15.2, 9.8]})
warm_almaty = df[(df["temp"] > 10) & (df["city"] == "Алматы")]
print(warm_almaty)
print(df.loc[df["temp"] < 0, "city"].tolist())
```

Результат:
```text
     city  temp
0  Алматы  12.5
['Астана']
```

Типичная ошибка: `df[df["temp"] > 0 and df["city"] == "Алматы"]` → `ValueError: The truth value of a Series is ambiguous`. Причина: `and` не может превратить весь столбец в одно `True/False`; для поэлементного сравнения каждой строки нужен `&`.

## 8. Git: конфликт слияния (merge conflict)

Если два человека (или две ветки) по-разному изменили **одну и ту же строку** файла, Git не знает, какой вариант взять, и ставит в файле пометки:

```text
<<<<<<< HEAD
GAME_SPEED = 5
=======
GAME_SPEED = 8
>>>>>>> feature/levels
```

Шаги разрешения:
1. `git status` — в каких файлах конфликт (`both modified`).
2. Откройте файл и **оставьте правильный вариант** (или объедините оба); удалите пометки `<<<<<<<`, `=======`, `>>>>>>>`.
3. Запустите программу и проверьте.
4. `git add <файл>` и `git commit`.

```bash
git status
# после исправления файла:
git add game/settings.py
git commit -m "Слияние: оставлена скорость игры 8"
```

Как избежать конфликтов: `git pull` в начале работы; частые небольшие коммиты; договориться в команде, чтобы один файл не меняли одновременно два человека.

## Частые ошибки

**1. `b = a` вместо копии.** Изменение `b` меняет и `a`. Правильно: `a.copy()` или `list(a)`; для вложенных структур — `copy.deepcopy(a)`.

**2. `COUNT(*)` вместе с `LEFT JOIN`.** Для группы без пары `COUNT(*)` вернёт 1 (считается строка с `NULL`). Правильно: `COUNT(s.id)` — считает только непустые значения.

**3. `and`/`or` вместо `&`/`|` в Pandas.** Вызывает `ValueError: The truth value of a Series is ambiguous`. Правильно: `&`, `|`, `~` и скобки вокруг каждого условия.

**4. Сделать коммит с пометками конфликта.** В файле остались `<<<<<<<` и `>>>>>>>` — программа не запускается (`SyntaxError`). Правильно: перед `git add` найти пометки поиском и запустить программу.

## Основные понятия
| Понятие | Пояснение |
|---|---|
| Ссылка | связь между переменной и объектом («метка») |
| Изменяемый объект | объект, который можно изменять на месте (список, словарь, множество) |
| Атрибут класса | атрибут, общий для всех объектов |
| Замыкание (closure) | «запоминание» функцией внешней переменной |
| Цикл событий | основной цикл, который ожидает события, обрабатывает их и отрисовывает экран |
| `LEFT JOIN` | соединение, сохраняющее все строки левой таблицы |
| Логическая маска | столбец `True/False` в Pandas для выборки строк |
| Конфликт слияния | ситуация, когда Git не может автоматически объединить две версии одной строки |

## Примечание

- `a.copy()` и `list(a)` создают **поверхностную** копию: вложенные списки остаются общими. Для полной копии используйте `copy.deepcopy()`.
- Оператор `is` сравнивает идентичность объектов, `==` — значения; `x is None` — рекомендуемая PEP 8 проверка на `None`.
- Вместо приёма `lambda n=i:` можно использовать `functools.partial(show, i)` — он тоже фиксирует значение аргумента в момент создания.
- Pandas поддерживает `df.query("temp > 10 and city == 'Алматы'")` — внутри строки запроса `and`/`or` разрешены.
- Python Tutor (https://pythontutor.com) наглядно показывает ссылки и объекты в памяти; модель данных Python описана в https://docs.python.org/3/reference/datamodel.html
