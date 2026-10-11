# Урок 51. Практические задания: сериализация и десериализация JSON

Во всех заданиях: `import json`, файлы с `encoding="utf-8"`, при записи `ensure_ascii=False, indent=2`.

## Базовый уровень

### Задание 1. Мой профиль

Создай словарь о себе: имя, возраст, город, посещаешь ли кружок (`True`/`False`), список хобби, словарь оценок по любимым предметам. Сохрани его в файл `profile.json`, затем прочитай из файла и выведи в таком виде:

```text
Вывод:
Имя: Айдана (14 лет, Алматы)
Хобби: шахматы, домбра, Python
Наивысшая оценка: информатика — 5
```

### Задание 2. Исправь JSON

В тексте ниже 4 ошибки. Найди ошибки и проверь исправленный текст с помощью `json.loads`.

```text
{
  'city': "Тараз",
  "founded": 2000 год,
  "is_capital": False,
  "districts": ["Жамбыл", "Аулиеата",],
}
```

### Задание 3. Библиотека: добавление книги

В файле `library.json` хранится список книг (если файла нет — начинать с пустого списка). Программа запрашивает у пользователя название книги, автора и год, добавляет книгу в список и перезаписывает файл. В конце выводит все книги, отсортированные по году.

```text
Ввод:
Название: Путь Абая
Автор: Мухтар Ауэзов
Год: 1942
Вывод:
Книга добавлена. Всего книг в библиотеке: 3:
1942 — «Путь Абая» (Мухтар Ауэзов)
1957 — «Меня зовут Кожа» (Бердибек Сокпакбаев)
1976 — «Кочевники» (Ильяс Есенберлин)
```

### Задание 4. Чтение данных о погоде

Обработай эту JSON-строку (`json.loads`), похожую на ответ API:

```text
{"city": "Астана", "unit": "°C",
 "days": [
   {"date": "2027-01-04", "min": -24, "max": -15, "desc": "мороз"},
   {"date": "2027-01-05", "min": -20, "max": -12, "desc": "снег"},
   {"date": "2027-01-06", "min": -27, "max": -18, "desc": "ясно"}
 ]}
```

Выведи каждый день, найди самый холодный день и среднюю максимальную температуру.

```text
Вывод:
Астана:
2027-01-04: -24..-15 °C, мороз
2027-01-05: -20..-12 °C, снег
2027-01-06: -27..-18 °C, ясно
Самый холодный день: 2027-01-06 (-27 °C)
Средний max: -15.0 °C
```

## Продвинутый уровень

### Задание 5. Сохранение игры (save game)

Напиши для нашей игры на Pygame функции, сохраняющие состояние игры:
- `save_game(state, path="save.json")` — сохраняет состояние;
- `load_game(path="save.json")` — читает состояние; если файла нет или он повреждён, возвращает состояние новой игры.

Пример состояния: `{"player": {"name": "Алия", "x": 120, "y": 300, "lives": 2}, "level": 3, "score": 1450, "inventory": ["ключ", "щит"]}`.

### Задание 6. Конвертер CSV → JSON

Прочитай файл `journal.csv` из урока 50 и преобразуй его в `journal.json` такой структуры (сгруппировано по классам, оценки — числа):

```text
{
  "9А": [
    {"name": "Айгерим Нурланкызы", "marks": {"math": 5, "informatics": 5, "physics": 4}},
    ...
  ],
  "9Б": [...]
}
```

### Задание 7. Сохранение объектов класса

Напиши класс `Contact` (имя, телефон, множество тегов — `set`). С помощью методов `to_dict()`/`from_dict()` сохрани список контактов в `contacts.json` и прочитай обратно. Подумай, как записать множество в JSON.

## ★ Олимпиадная / творческая задача

### Задание 8. Поиск всех путей в JSON

Напиши рекурсивную функцию, которая выводит «путь» ко **всем** значениям в любой вложенной структуре JSON:

```text
Вход: {"team": "Қыран", "members": [{"name": "Алия"}, {"name": "Нурлан"}], "game": {"levels": [1, 2]}}
Вывод:
team = Қыран
members[0].name = Алия
members[1].name = Нурлан
game.levels[0] = 1
game.levels[1] = 2
```

### Задание 9. Викторина из JSON

Напиши программу-викторину, вопросы которой хранятся в файле `quiz.json` (вопрос, варианты ответа, индекс правильного ответа, пояснение). Результат (имя, очки, дата) должен добавляться в файл `results.json`. Вопросы составь сам по истории Казахстана или по Python (не менее 5).

## Домашнее задание

1. Заверши задание 9 или создай свой каталог «любимых фильмов/книг» в JSON-файле (не менее 5 записей, в каждой должны быть список и вложенный словарь).
2. Открой в браузере адрес `https://open.er-api.com/v6/latest/USD` и посмотри на структуру JSON: какие там ключи? По какому «пути» можно добраться до курса тенге? (С API будем работать на уроке 66.)

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**

```python
import json

profile = {
    "name": "Айдана",
    "age": 14,
    "city": "Алматы",
    "is_member": True,
    "hobbies": ["шахматы", "домбра", "Python"],
    "marks": {"математика": 4, "информатика": 5, "физика": 4},
}
with open("profile.json", "w", encoding="utf-8") as f:
    json.dump(profile, f, ensure_ascii=False, indent=2)

with open("profile.json", encoding="utf-8") as f:
    p = json.load(f)

best = max(p["marks"], key=p["marks"].get)
print(f"Имя: {p['name']} ({p['age']} лет, {p['city']})")
print("Хобби:", ", ".join(p["hobbies"]))
print(f"Наивысшая оценка: {best} — {p['marks'][best]}")
```

Результат:

```text
Имя: Айдана (14 лет, Алматы)
Хобби: шахматы, домбра, Python
Наивысшая оценка: информатика — 5
```

**Задание 2.** Ошибки: (1) `'city'` — одинарные кавычки; (2) `2000 год` — не число (либо `2000`, либо `"2000 год"`); (3) `False` → `false`; (4) запятые в конце массива и объекта.

```python
import json

fixed = """
{
  "city": "Тараз",
  "founded": 2000,
  "is_capital": false,
  "districts": ["Жамбыл", "Аулиеата"]
}
"""
print(json.loads(fixed))
```

Результат:

```text
{'city': 'Тараз', 'founded': 2000, 'is_capital': False, 'districts': ['Жамбыл', 'Аулиеата']}
```

(Примечание: история Тараза насчитывает более 2000 лет — в 2002 году отмечалось его 2000-летие; пример предназначен только для изучения синтаксиса.)

**Задание 3**

```python
import json
import os

FILE = "library.json"

books = []
if os.path.exists(FILE):
    with open(FILE, encoding="utf-8") as f:
        books = json.load(f)

title = input("Название: ").strip()
author = input("Автор: ").strip()
year = int(input("Год: "))
books.append({"title": title, "author": author, "year": year})

with open(FILE, "w", encoding="utf-8") as f:
    json.dump(books, f, ensure_ascii=False, indent=2)

print(f"Книга добавлена. Всего книг в библиотеке: {len(books)}:")
for b in sorted(books, key=lambda b: b["year"]):
    print(f"{b['year']} — «{b['title']}» ({b['author']})")
```

**Задание 4**

```python
import json

text = """{"city": "Астана", "unit": "°C",
 "days": [
   {"date": "2027-01-04", "min": -24, "max": -15, "desc": "мороз"},
   {"date": "2027-01-05", "min": -20, "max": -12, "desc": "снег"},
   {"date": "2027-01-06", "min": -27, "max": -18, "desc": "ясно"}
 ]}"""
data = json.loads(text)
unit = data["unit"]

print(data["city"] + ":")
for d in data["days"]:
    print(f"{d['date']}: {d['min']}..{d['max']} {unit}, {d['desc']}")

coldest = min(data["days"], key=lambda d: d["min"])
avg_max = sum(d["max"] for d in data["days"]) / len(data["days"])
print(f"Самый холодный день: {coldest['date']} ({coldest['min']} {unit})")
print(f"Средний max: {avg_max:.1f} {unit}")
```

Результат:

```text
Астана:
2027-01-04: -24..-15 °C, мороз
2027-01-05: -20..-12 °C, снег
2027-01-06: -27..-18 °C, ясно
Самый холодный день: 2027-01-06 (-27 °C)
Средний max: -15.0 °C
```

**Задание 5**

```python
import json

NEW_GAME = {
    "player": {"name": "Игрок", "x": 100, "y": 300, "lives": 3},
    "level": 1,
    "score": 0,
    "inventory": [],
}


def save_game(state, path="save.json"):
    """Сохраняет состояние игры в JSON-файл."""
    with open(path, "w", encoding="utf-8") as f:
        json.dump(state, f, ensure_ascii=False, indent=2)


def load_game(path="save.json"):
    """Читает сохранённое состояние; если невозможно — новая игра."""
    try:
        with open(path, encoding="utf-8") as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return json.loads(json.dumps(NEW_GAME))  # глубокая копия


state = load_game()
print("Загружено: уровень", state["level"], "| очков:", state["score"])
state.update({"level": 3, "score": 1450, "inventory": ["ключ", "щит"]})
state["player"].update({"name": "Алия", "x": 120, "lives": 2})
save_game(state)
print("Загружено заново:", load_game()["inventory"])
```

Результат (при первом запуске):

```text
Загружено: уровень 1 | очков: 0
Загружено заново: ['ключ', 'щит']
```

`json.loads(json.dumps(...))` — глубокая копия (или `copy.deepcopy`), чтобы случайно не изменить словарь `NEW_GAME`.

**Задание 6**

```python
import csv
import json

SUBJECTS = ["math", "informatics", "physics"]
rows = [
    ["name", "class", "math", "informatics", "physics"],
    ["Айгерим Нурланкызы", "9А", 5, 5, 4],
    ["Даулет Оспанов", "9Б", 4, 5, 5],
]
with open("journal.csv", "w", encoding="utf-8", newline="") as f:
    csv.writer(f).writerows(rows)  # небольшой файл для проверки

result = {}
with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        student = {"name": row["name"], "marks": {s: int(row[s]) for s in SUBJECTS}}
        result.setdefault(row["class"], []).append(student)

with open("journal.json", "w", encoding="utf-8") as f:
    json.dump(result, f, ensure_ascii=False, indent=2)
print(json.dumps(result, ensure_ascii=False))
```

Результат:

```text
{"9А": [{"name": "Айгерим Нурланкызы", "marks": {"math": 5, "informatics": 5, "physics": 4}}], "9Б": [{"name": "Даулет Оспанов", "marks": {"math": 4, "informatics": 5, "physics": 5}}]}
```

**Задание 7**

```python
import json


class Contact:
    """Контакт телефонной книжки."""

    def __init__(self, name, phone, tags=None):
        self.name = name
        self.phone = phone
        self.tags = set(tags or [])

    def to_dict(self):
        # set не помещается в JSON → превращаем в отсортированный список
        return {"name": self.name, "phone": self.phone, "tags": sorted(self.tags)}

    @classmethod
    def from_dict(cls, data):
        return cls(data["name"], data["phone"], data.get("tags", []))

    def __str__(self):
        return f"{self.name}: {self.phone} [{', '.join(sorted(self.tags))}]"


contacts = [
    Contact("Мама", "+7 701 111 22 33", {"семья"}),
    Contact("Ерлан", "+7 747 555 66 77", {"класс", "футбол"}),
]
with open("contacts.json", "w", encoding="utf-8") as f:
    json.dump([c.to_dict() for c in contacts], f, ensure_ascii=False, indent=2)

with open("contacts.json", encoding="utf-8") as f:
    for c in (Contact.from_dict(d) for d in json.load(f)):
        print(c)
```

Результат:

```text
Мама: +7 701 111 22 33 [семья]
Ерлан: +7 747 555 66 77 [класс, футбол]
```

**Задание 8**

```python
def walk(value, path=""):
    """Выводит все «листья» вложенной структуры вместе с путём к ним."""
    if isinstance(value, dict):
        for key, item in value.items():
            walk(item, f"{path}.{key}" if path else key)
    elif isinstance(value, list):
        for i, item in enumerate(value):
            walk(item, f"{path}[{i}]")
    else:
        print(f"{path} = {value}")


data = {"team": "Қыран", "members": [{"name": "Алия"}, {"name": "Нурлан"}], "game": {"levels": [1, 2]}}
walk(data)
```

Результат:

```text
team = Қыран
members[0].name = Алия
members[1].name = Нурлан
game.levels[0] = 1
game.levels[1] = 2
```

**Задание 9 (образец)**

```python
import json
import os
from datetime import date

QUIZ = [
    {"q": "В каком году было образовано Казахское ханство?", "options": ["1465", "1731", "1991"], "answer": 0,
     "why": "В 1465 году его основали султаны Керей и Жанибек."},
    {"q": "Метод, добавляющий элемент в список в Python?", "options": ["add", "append", "push"], "answer": 1,
     "why": "list.append(x) — добавляет в конец списка."},
]

if not os.path.exists("quiz.json"):
    with open("quiz.json", "w", encoding="utf-8") as f:
        json.dump(QUIZ, f, ensure_ascii=False, indent=2)

with open("quiz.json", encoding="utf-8") as f:
    questions = json.load(f)

name = input("Твоё имя: ")
score = 0
for item in questions:
    print("\n" + item["q"])
    for i, option in enumerate(item["options"], start=1):
        print(f"  {i}) {option}")
    choice = int(input("Номер ответа: ")) - 1
    if choice == item["answer"]:
        score += 1
        print("Правильно!")
    else:
        print("Неверно.", item["why"])

results = []
if os.path.exists("results.json"):
    with open("results.json", encoding="utf-8") as f:
        results = json.load(f)
results.append({"name": name, "score": score, "total": len(questions), "date": date.today().isoformat()})
with open("results.json", "w", encoding="utf-8") as f:
    json.dump(results, f, ensure_ascii=False, indent=2)
print(f"\n{name}, твой результат: {score}/{len(questions)}")
```

</details>
