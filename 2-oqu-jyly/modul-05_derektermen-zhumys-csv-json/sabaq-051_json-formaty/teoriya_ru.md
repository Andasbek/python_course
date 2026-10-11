# Урок 51. Формат JSON. Модуль json: сериализация и десериализация.

## Темы

1. Что такое JSON?
2. Сериализация и десериализация
3. JSON-файлы: `dump` и `load`
4. Работа с вложенными структурами
5. Обработка ошибок
6. Типы, которые не помещаются в JSON
7. Сериализация объектов (экземпляров классов)
8. Сравнение JSON, CSV и pickle
9. Частые ошибки
10. Основные понятия

## 1. Что такое JSON?

**JSON (JavaScript Object Notation)** — формат хранения и обмена данными в текстовом виде. Он пришёл из языка JavaScript, но сейчас используется во всех языках. Подавляющее большинство веб-API отдают данные в формате JSON.

```json
{
  "name": "Айдана",
  "age": 14,
  "city": "Алматы",
  "is_member": true,
  "clubs": ["Python", "Робототехника"],
  "scores": {"python": 98, "robotics": 91},
  "mentor": null
}
```

Очень похоже на словарь Python! Но есть несколько отличий.

### Правила JSON

1. **Объект** `{...}` — пары «ключ: значение»; ключ — **только строка**, **только в двойных кавычках**.
2. **Массив** `[...]` — список значений.
3. Строки — **только в двойных кавычках** `"..."` (одинарные `'...'` — ошибка).
4. Логические значения — `true` / `false` (со строчной буквы), пустое значение — `null`.
5. После последнего элемента ставить запятую **нельзя**: `[1, 2, 3,]` — ошибка.
6. Писать комментарии (`#`, `//`) **нельзя**.

### Соответствие типов

| JSON | Python |
|---|---|
| object `{}` | `dict` |
| array `[]` | `list` (кортеж `tuple` тоже превращается в массив) |
| string `"..."` | `str` |
| number (целое) | `int` |
| number (дробное) | `float` |
| `true` / `false` | `True` / `False` |
| `null` | `None` |

Типов, которых **нет** в JSON: `set`, `datetime`, `bytes`, объект твоего собственного класса. Чтобы их сохранить, сначала нужно превратить их в типы JSON (см. ниже).

## 2. Сериализация и десериализация

- **Сериализация (serialization)** — превращение объекта Python в текст JSON (или в файл).
- **Десериализация (deserialization)** — восстановление объекта Python из текста JSON.

**Аналогия:** чтобы отправить мебель (словарь) по почте, её разбирают и складывают в коробку (сериализация). Получатель открывает коробку и снова собирает мебель (десериализация). Инструкция внутри коробки — формат JSON.

```text
Объект Python  --dumps / dump-->  Текст JSON / файл
Объект Python  <--loads / load--  Текст JSON / файл
```

| Функция | Что делает | Как запомнить |
|---|---|---|
| `json.dumps(obj)` | объект → **строка** | **s** = string |
| `json.loads(text)` | **строка** → объект | **s** = string |
| `json.dump(obj, f)` | объект → **файл** | |
| `json.load(f)` | **файл** → объект | |

### `dumps` и `loads` — работа со строкой

```python
import json

student = {
    "name": "Айдана",
    "age": 14,
    "is_member": True,
    "clubs": ["Python", "Робототехника"],
    "mentor": None,
}

text = json.dumps(student)
print(type(text))
print(text)

back = json.loads(text)
print(type(back))
print(back["clubs"][0])
```

Результат:

```text
<class 'str'>
{"name": "\u0410\u0439\u0434\u0430\u043d\u0430", "age": 14, "is_member": true, "clubs": ["Python", "\u0420\u043e\u0431\u043e\u0442\u043e\u0442\u0435\u0445\u043d\u0438\u043a\u0430"], "mentor": null}
<class 'dict'>
Python
```

Обрати внимание: `True` → `true`, `None` → `null`. А кириллица записана в «экранированном» виде `\u0410...` — это не ошибка, но читать такое неудобно.

### `ensure_ascii=False` и `indent`

```python
import json

student = {"name": "Айдана", "clubs": ["Python", "Домбра"], "scores": {"python": 98}}
print(json.dumps(student, ensure_ascii=False, indent=2))
```

Результат:

```text
{
  "name": "Айдана",
  "clubs": [
    "Python",
    "Домбра"
  ],
  "scores": {
    "python": 98
  }
}
```

| Параметр | Значение |
|---|---|
| `ensure_ascii=False` | записывать русские/казахские буквы как есть |
| `indent=2` (или 4) | «красивая» запись с отступами (удобно читать человеку) |
| `sort_keys=True` | сортировка ключей по алфавиту |
| `separators=(",", ":")` | самая компактная запись без пробелов (для передачи по сети) |

## 3. JSON-файлы: `dump` и `load`

```python
import json

library = {
    "school": "Школа-лицей № 25",
    "books": [
        {"title": "Путь Абая", "author": "Мухтар Ауэзов", "year": 1942, "available": True},
        {"title": "Кочевники", "author": "Ильяс Есенберлин", "year": 1976, "available": False},
        {"title": "Меня зовут Кожа", "author": "Бердибек Сокпакбаев", "year": 1957, "available": True},
    ],
}

# Сериализация: запись в файл
with open("library.json", "w", encoding="utf-8") as f:
    json.dump(library, f, ensure_ascii=False, indent=2)

# Десериализация: чтение из файла
with open("library.json", encoding="utf-8") as f:
    data = json.load(f)

print(data["school"])
for book in data["books"]:
    status = "в наличии" if book["available"] else "выдана"
    print(f"- «{book['title']}» ({book['author']}, {book['year']}) — {status}")
```

Результат:

```text
Школа-лицей № 25
- «Путь Абая» (Мухтар Ауэзов, 1942) — в наличии
- «Кочевники» (Ильяс Есенберлин, 1976) — выдана
- «Меня зовут Кожа» (Бердибек Сокпакбаев, 1957) — в наличии
```

Если открыть файл `library.json` в VS Code, это будет удобный для чтения текст с отступами.

## 4. Работа с вложенными структурами

К данным в JSON добираемся «по пути»: `data["books"][0]["title"]` — «ключ title нулевого элемента списка books».

```python
import json

text = """
{
  "team": "Қыран",
  "members": [
    {"name": "Алия", "role": "тимлид", "commits": 9},
    {"name": "Нурлан", "role": "код", "commits": 14},
    {"name": "Дана", "role": "графика", "commits": 6}
  ],
  "game": {"title": "Қыран", "version": "1.0", "levels": [1, 2, 3]}
}
"""
data = json.loads(text)

print("Игра:", data["game"]["title"], "v" + data["game"]["version"])
print("Количество уровней:", len(data["game"]["levels"]))
total = sum(m["commits"] for m in data["members"])
top = max(data["members"], key=lambda m: m["commits"])
print("Всего коммитов:", total)
print("Самый активный:", top["name"])

# Изменение и добавление нового элемента
data["members"].append({"name": "Ерасыл", "role": "звук", "commits": 5})
data["game"]["version"] = "1.1"
print(json.dumps(data["game"], ensure_ascii=False))
```

Результат:

```text
Игра: Қыран v1.0
Количество уровней: 3
Всего коммитов: 29
Самый активный: Нурлан
{"title": "Қыран", "version": "1.1", "levels": [1, 2, 3]}
```

Если ключа может не быть, используем `data.get("key", значение_по_умолчанию)` — `KeyError` не будет.

## 5. Обработка ошибок

При чтении неправильного текста JSON возникает `json.JSONDecodeError`:

```python
import json

bad_texts = [
    "{'name': 'Алия'}",          # одинарные кавычки
    '{"scores": [1, 2, 3,]}',    # запятая в конце
    '{"ok": True}',              # должно быть не True, а true
    '{"ok": true}',              # правильно
]

for text in bad_texts:
    try:
        print("Правильно:", json.loads(text))
    except json.JSONDecodeError as e:
        print(f"Ошибка: {e.msg} (позиция {e.pos})")
```

Результат:

```text
Ошибка: Expecting property name enclosed in double quotes (позиция 1)
Ошибка: Illegal trailing comma before end of array (позиция 19)
Ошибка: Expecting value (позиция 7)
Правильно: {'ok': True}
```

(В Python 3.12 и более старых версиях сообщение во второй строке выводится как `Expecting value` — смысл ошибки тот же.)

Надёжная функция чтения файла:

```python
import json


def load_json(path, default=None):
    """Читает JSON-файл; если файла нет или он повреждён, возвращает default."""
    try:
        with open(path, encoding="utf-8") as f:
            return json.load(f)
    except FileNotFoundError:
        return default
    except json.JSONDecodeError:
        print(f"Предупреждение: файл {path} повреждён")
        return default


print(load_json("jok.json", default={"records": []}))
```

Результат:

```text
{'records': []}
```

## 6. Типы, которые не помещаются в JSON

```python
import json
from datetime import date

data = {"tags": {"python", "json"}, "today": date(2027, 1, 4)}
try:
    json.dumps(data)
except TypeError as e:
    print("Ошибка:", e)

# Решение: сначала превратить в типы JSON
safe = {"tags": sorted(data["tags"]), "today": data["today"].isoformat()}
print(json.dumps(safe))
```

Результат:

```text
Ошибка: Object of type set is not JSON serializable
{"tags": ["json", "python"], "today": "2027-01-04"}
```

Ещё одна особенность: ключи объекта JSON — **только строки**. Ключи `int` словаря Python превращаются в строки: `{1: "a"}` → `{"1": "a"}`, и при обратном чтении остаются `"1"`.

## 7. Сериализация объектов (экземпляров классов)

Записать объект класса напрямую в JSON нельзя, поэтому добавляем в класс методы `to_dict()` и `from_dict()`:

```python
import json


class Student:
    """Ученик кружка."""

    def __init__(self, name, grade, scores=None):
        self.name = name
        self.grade = grade
        self.scores = scores or {}

    def to_dict(self):
        """Превращает объект в словарь, пригодный для JSON."""
        return {"name": self.name, "grade": self.grade, "scores": self.scores}

    @classmethod
    def from_dict(cls, data):
        """Создаёт новый объект из словаря."""
        return cls(data["name"], data["grade"], data.get("scores", {}))

    def __repr__(self):
        return f"Student({self.name!r}, {self.grade})"


students = [Student("Айдана", 8, {"python": 98}), Student("Темирлан", 9)]

with open("students.json", "w", encoding="utf-8") as f:
    json.dump([s.to_dict() for s in students], f, ensure_ascii=False, indent=2)

with open("students.json", encoding="utf-8") as f:
    loaded = [Student.from_dict(d) for d in json.load(f)]

print(loaded)
print(loaded[0].scores["python"])
```

Результат:

```text
[Student('Айдана', 8), Student('Темирлан', 9)]
98
```

Этот приём часто используется для файлов настроек, сохранений игры и данных, полученных через API.

## 8. Сравнение JSON, CSV и pickle

| | CSV | JSON | pickle |
|---|---|---|---|
| Вид | текст | текст | двоичный |
| Структура | плоская таблица | вложенная (дерево) | любой объект Python |
| Типы | всё — строки | str, int, float, bool, null, list, dict | все |
| Открывается в Excel | да | нет | нет |
| Читают другие языки | да | да (универсальный) | только Python |
| Безопасность | безопасно | безопасно | чужой файл опасен |
| Применение | журнал, отчёт | настройки, API, сохранения игры | временный кэш |

## Частые ошибки

**1. Путают `dump` и `dumps`.**

```python
import json

data = {"a": 1}
with open("a.json", "w", encoding="utf-8") as f:
    # json.dumps(data, f)  # Ошибка: dumps не принимает файл (TypeError)
    json.dump(data, f)     # Правильно
```

**2. Кириллица записывается как `\u0410`.** Правильно: `ensure_ascii=False` и открытие файла с `encoding="utf-8"`.

**3. Одинарные кавычки или запятая в конце при ручном написании JSON.** Правильно: только `"..."`, без последней запятой. Для проверки — команда `python -m json.tool file.json`:

```bash
python -m json.tool library.json
```

**4. `json.load` читает пустой файл** → `JSONDecodeError: Expecting value`. Правильно: `try/except` или изначально создавать файл со значением `[]` / `{}`.

**5. Открыть в режиме `"w"` и сделать `json.load`** → файл стирается. Для чтения — `"r"`, для записи — `"w"`, одновременно этого не делаем: сначала читаем, изменяем, затем записываем заново.

**6. Хранение числовых ключей.** `{1: "один"}` сохраняется, а при чтении становится `{"1": "один"}` — `data[1]` → `KeyError`. Правильно: `data["1"]` или после чтения преобразовать ключи в `int`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| JSON | Универсальный текстовый формат для обмена данными |
| Объект / массив | `{}` (словарь) и `[]` (список) в JSON |
| Сериализация | Превращение объекта в текст/файл (`dump`, `dumps`) |
| Десериализация | Восстановление объекта из текста/файла (`load`, `loads`) |
| `ensure_ascii=False` | Сохранение национальных букв как есть |
| `indent` | Красивая запись с отступами |
| `JSONDecodeError` | Ошибка при чтении неправильного текста JSON |
| `to_dict` / `from_dict` | Превращение объекта в словарь и обратно |

## Примечание

- `json.load()` возвращает новые объекты: изменение прочитанного словаря не меняет файл — чтобы сохранить изменения, файл нужно записать заново через `json.dump()`.
- Модуль `json` по умолчанию пропускает `NaN` и `Infinity` (`float("nan")` → `NaN`), хотя стандарт JSON их не допускает; строгий режим — `json.dumps(obj, allow_nan=False)`.
- Для дробных денег используйте `json.loads(text, parse_float=decimal.Decimal)`, чтобы избежать погрешностей `float`.
- Нестандартные типы можно сериализовать через параметр `default`: `json.dumps(data, default=str)` превратит `date`, `Decimal` и т. п. в строку; для объектов `dataclass` удобно `dataclasses.asdict(obj)`.
- Чтобы не повредить файл при сбое во время записи, сначала пишут во временный файл, а затем заменяют им оригинал: `os.replace("data.tmp", "data.json")` — эта операция атомарна.
- Документация: https://docs.python.org/3/library/json.html
