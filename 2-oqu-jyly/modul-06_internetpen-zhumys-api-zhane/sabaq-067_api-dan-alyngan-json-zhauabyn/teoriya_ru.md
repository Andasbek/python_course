# Урок 67. Обработка JSON-ответа, полученного от API, и отображение пользователю.

## Темы

1. Соответствие JSON → Python
2. Исследование структуры: «карта пути»
3. Безопасный доступ
4. Параллельные массивы и `zip()`
5. Преобразование данных: фильтрация, сортировка, агрегирование
6. Обработка даты и времени
7. Красивый показ пользователю
8. «Конвейер» обработки
9. Частые ошибки
10. Основные понятия

## 1. Соответствие JSON → Python

| JSON | Python | Пример |
|---|---|---|
| object `{}` | `dict` | `{"name": "Алматы"}` |
| array `[]` | `list` | `[1, 2, 3]` |
| string | `str` | `"Снег"` |
| number | `int` / `float` | `-3.5` |
| `true` / `false` | `True` / `False` | |
| `null` | `None` | |

`r.json()` выполняет это преобразование автоматически. Дальше — только работа со словарями и списками, которую мы хорошо знаем!

## 2. Исследование структуры: «карта пути»

Ответ API может быть длинным и сложным. Сначала нужно понять его **структуру**. Для примера — сокращённая версия ответа Open-Meteo (вставили в код, чтобы работало и без интернета):

```python
import json

response_text = """
{
  "latitude": 43.25, "longitude": 76.95, "timezone": "Asia/Almaty",
  "current_units": {"temperature_2m": "°C", "wind_speed_10m": "km/h"},
  "current": {"time": "2027-02-11T14:00", "temperature_2m": -1.8,
              "wind_speed_10m": 6.4, "weather_code": 3},
  "daily": {
    "time": ["2027-02-11", "2027-02-12", "2027-02-13"],
    "temperature_2m_max": [0.4, -1.2, 2.7],
    "temperature_2m_min": [-6.1, -8.3, -4.0],
    "weather_code": [3, 73, 0]
  }
}
"""
data = json.loads(response_text)

# Шаг 1: ключи верхнего уровня
print(list(data.keys()))
# Шаг 2: красивый вывод
print(json.dumps(data["current"], ensure_ascii=False, indent=2))
```

Результат:
```text
['latitude', 'longitude', 'timezone', 'current_units', 'current', 'daily']
{
  "time": "2027-02-11T14:00",
  "temperature_2m": -1.8,
  "wind_speed_10m": 6.4,
  "weather_code": 3
}
```

**Карта пути** — путь к нужному значению:

```text
data ─┬─ "current" ─┬─ "temperature_2m" → -1.8
      │             └─ "weather_code"   → 3
      └─ "daily" ───┬─ "time"               → ["2027-02-11", ...]   (список!)
                    └─ "temperature_2m_max" → [0.4, -1.2, 2.7]
```

```python
print(data["current"]["temperature_2m"])        # словарь → словарь → значение
print(data["daily"]["time"][0])                 # словарь → словарь → список → элемент
print(data["daily"]["temperature_2m_max"][-1])  # максимум последнего дня
```

Результат:
```text
-1.8
2027-02-11
2.7
```

**Подсказка:** если открыть JSON-ответ в браузере (Firefox, Chrome), он красиво отображается в виде дерева — очень удобно для изучения структуры.

## 3. Безопасный доступ

В ответе API некоторых полей может **не быть** или они могут быть `null`. Прямой `data["key"]` — риск `KeyError`.

```python
city = {"name": "Туркестан", "latitude": 43.3, "longitude": 68.25, "population": None}

print(city.get("country", "неизвестно"))        # ключа нет → значение по умолчанию
pop = city.get("population")
print(f"{pop:,}" if pop is not None else "Численность населения не указана")

# Для вложенных словарей — цепочка get
resp = {"results": [{"name": "Туркестан"}]}
first = (resp.get("results") or [{}])[0]
print(first.get("name", "—"))
```

Результат:
```text
неизвестно
Численность населения не указана
Туркестан
```

Вспомогательная функция для глубоких структур:

```python
def dig(obj, *path, default=None):
    """dig(data, "daily", "time", 0) — безопасный проход по пути."""
    for key in path:
        try:
            obj = obj[key]
        except (KeyError, IndexError, TypeError):
            return default
    return obj

sample = {"daily": {"time": ["2027-02-11"]}}
print(dig(sample, "daily", "time", 0))
print(dig(sample, "daily", "rain", 0, default="нет"))
```

Результат:
```text
2027-02-11
нет
```

## 4. Параллельные массивы и `zip()`

Open-Meteo отдаёт данные по дням в виде «столбцов»: каждая величина — отдельный список, а элементы с одинаковым индексом относятся к одному дню. Чтобы объединить их в «строки», используем `zip()`:

```python
daily = {
    "time": ["2027-02-11", "2027-02-12", "2027-02-13"],
    "temperature_2m_max": [0.4, -1.2, 2.7],
    "temperature_2m_min": [-6.1, -8.3, -4.0],
    "weather_code": [3, 73, 0],
}

days = []
for t, tmax, tmin, code in zip(daily["time"], daily["temperature_2m_max"],
                               daily["temperature_2m_min"], daily["weather_code"]):
    days.append({"date": t, "max": tmax, "min": tmin, "code": code})

print(days[1])
```

Результат:
```text
{'date': '2027-02-12', 'max': -1.2, 'min': -8.3, 'code': 73}
```

Теперь `days` — удобный нам «список записей» (как строки в SQLite).

## 5. Преобразование данных: фильтрация, сортировка, агрегирование

```python
days = [
    {"date": "2027-02-11", "max": 0.4, "min": -6.1, "code": 3},
    {"date": "2027-02-12", "max": -1.2, "min": -8.3, "code": 73},
    {"date": "2027-02-13", "max": 2.7, "min": -4.0, "code": 0},
]

# Фильтрация: морозные дни (максимум ниже 0)
frosty = [d["date"] for d in days if d["max"] < 0]
# Сортировка: самый тёплый день первым
warm_first = sorted(days, key=lambda d: d["max"], reverse=True)
# Агрегирование: среднее, минимум
avg_max = sum(d["max"] for d in days) / len(days)
coldest = min(days, key=lambda d: d["min"])

print("Морозные дни:", frosty)
print("Самый тёплый день:", warm_first[0]["date"])
print(f"Средний максимум: {avg_max:.1f} °C")
print("Самая холодная ночь:", coldest["date"], coldest["min"])
```

Результат:
```text
Морозные дни: ['2027-02-12']
Самый тёплый день: 2027-02-13
Средний максимум: 0.6 °C
Самая холодная ночь: 2027-02-12 -8.3
```

## 6. Обработка даты и времени

API передают время в формате **ISO 8601**: `2027-02-11T14:00`. Превращаем его в объект `datetime` и форматируем по-русски:

```python
from datetime import datetime

MONTHS = ["января", "февраля", "марта", "апреля", "мая", "июня",
          "июля", "августа", "сентября", "октября", "ноября", "декабря"]
WEEKDAYS = ["понедельник", "вторник", "среда", "четверг", "пятница", "суббота", "воскресенье"]

dt = datetime.fromisoformat("2027-02-11T14:00")
print(f"{dt.day} {MONTHS[dt.month - 1]}, {WEEKDAYS[dt.weekday()]}, {dt:%H:%M}")
```

Результат:
```text
11 февраля, четверг, 14:00
```

API валют передаёт время и в формате Unix (`time_last_update_unix`: число секунд с 1970 года):

```python
from datetime import datetime
print(datetime.fromtimestamp(1802217600).strftime("%d.%m.%Y %H:%M"))
```

## 7. Красивый показ пользователю

### Таблица в консоли

Выравнивание в f-строках: `<` — влево, `>` — вправо, `^` — по центру, число — ширина.

```python
days = [("11.02", "четверг", 0.4, -6.1, "Облачно"),
        ("12.02", "пятница", -1.2, -8.3, "Снег"),
        ("13.02", "суббота", 2.7, -4.0, "Ясно")]

print(f"{'Дата':<6} {'День нед.':<10} {'Макс':>6} {'Мин':>6}  Погода")
print("-" * 44)
for date, wd, tmax, tmin, desc in days:
    print(f"{date:<6} {wd:<10} {tmax:>+6.1f} {tmin:>+6.1f}  {desc}")
```

Результат:
```text
Дата   День нед.    Макс    Мин  Погода
--------------------------------------------
11.02  четверг      +0.4   -6.1  Облачно
12.02  пятница      -1.2   -8.3  Снег
13.02  суббота      +2.7   -4.0  Ясно
```

### Текстовый «график»

```python
temps = {"11.02": 0.4, "12.02": -1.2, "13.02": 2.7, "14.02": 5.0}
for day, t in temps.items():
    bar = "█" * int(abs(t) * 2)
    print(f"{day} {t:+5.1f} {bar}")
```

### В окне Tkinter

```python
import tkinter as tk
from tkinter import ttk

days = [("11.02", 0.4, -6.1, "Облачно ☁️"), ("12.02", -1.2, -8.3, "Снег 🌨"),
        ("13.02", 2.7, -4.0, "Ясно ☀️")]

root = tk.Tk()
root.title("Алматы: прогноз на 3 дня")
tk.Label(root, text="Алматы", font=("Arial", 20, "bold")).pack(pady=(10, 0))
tk.Label(root, text="Сейчас: −1.8 °C, облачно", font=("Arial", 12)).pack()

tree = ttk.Treeview(root, columns=("d", "max", "min", "w"), show="headings", height=3)
for col, title in zip(("d", "max", "min", "w"), ("Дата", "Макс", "Мин", "Погода")):
    tree.heading(col, text=title)
    tree.column(col, width=90, anchor="center")
for d in days:
    tree.insert("", "end", values=(d[0], f"{d[1]:+.1f}°", f"{d[2]:+.1f}°", d[3]))
tree.pack(padx=10, pady=10)
root.mainloop()
```

Этим же способом строится полноценное приложение «Погода»: слой данных обрабатывает JSON, интерфейс лишь показывает готовый результат.

## 8. «Конвейер» обработки

В качественном коде работа с API делится на три отдельные функции:

```text
fetch()  ──►  parse()  ──►  show()
получение     выбор нужного  вывод
из сети       из JSON        на экран
```

```python
def parse_daily(data):
    """Возвращает список дней из ответа API."""
    d = data.get("daily", {})
    keys = ("time", "temperature_2m_max", "temperature_2m_min", "weather_code")
    return [dict(zip(("date", "max", "min", "code"), row)) for row in zip(*(d.get(k, []) for k in keys))]

sample = {"daily": {"time": ["2027-02-11"], "temperature_2m_max": [0.4],
                    "temperature_2m_min": [-6.1], "weather_code": [3]}}
print(parse_daily(sample))
```

Результат:
```text
[{'date': '2027-02-11', 'max': 0.4, 'min': -6.1, 'code': 3}]
```

**Польза:** функцию `parse()` можно проверить без интернета, на сохранённом образце JSON; если сменим интерфейс (консоль → Tkinter), изменится только `show()`.

## Частые ошибки

**1. Обращение со списком как со словарём:**
```python
resp = {"results": [{"name": "Алматы"}]}
# Неправильно: resp["results"]["name"]  → TypeError: list indices must be integers
print(resp["results"][0]["name"])   # Правильно: сначала индекс
```

**2. `KeyError` — поля нет.** Для необязательных полей используйте `.get()`.

**3. Арифметика с `None`:** в некоторые часы значение может быть `null` → `TypeError`. Перед вычислениями отфильтруйте: `[t for t in temps if t is not None]`.

**4. Сравнение дат как строк** — `"2027-02-11" < "2027-02-9"` может работать неправильно. Используйте `datetime.fromisoformat()`.

**5. Попытка прочитать JSON через `print(data)` одной строкой** — непонятно. Используйте `json.dumps(data, indent=2, ensure_ascii=False)`.

**6. Повторный запрос для каждого показа** — получите данные один раз и сохраните в переменной.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Вложенная структура | Словарь/список внутри словаря/списка |
| Карта пути | Последовательность ключей и индексов, ведущая к нужному значению |
| Параллельные массивы | Списки, элементы которых с одинаковым индексом относятся к одной записи |
| `zip()` | Объединение нескольких списков поэлементно |
| Парсинг (parse) | Извлечение нужной информации из «сырых» данных |
| ISO 8601 | Международный формат даты/времени: `ГГГГ-ММ-ДДTЧЧ:ММ` |
| Время Unix | Число секунд с 1970-01-01 |
| `json.dumps(indent=2)` | Вывод JSON в читаемом виде |

## Примечание

- `json.loads()` превращает JSON `null` в `None`, `true/false` — в `True/False`; ключи JSON-объекта всегда строки (str), даже если выглядят как числа.
- `dict.get(key, default)` возвращает default только при отсутствии ключа; если ключ есть, но значение `None`, вернётся `None` — в таком случае используйте `d.get("x") or default`.
- `zip()` без ошибки останавливается на самом коротком списке; в Python 3.10+ `zip(a, b, strict=True)` выбрасывает `ValueError` при разной длине.
- До Python 3.11 `datetime.fromisoformat()` не понимал суффикс `Z` и некоторые форматы ISO 8601 (https://docs.python.org/3/library/datetime.html).
- Для просмотра структуры: `json.dumps(data, indent=2, ensure_ascii=False)` или `pprint.pprint(data, depth=2)`.
