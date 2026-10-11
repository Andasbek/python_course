# Урок 67. Практические задания: обработка JSON, полученного от API

> Каждое задание выполняйте сначала на **сохранённом образце JSON** (без интернета), затем с реальным API. Разделяйте код на функции `fetch()`, `parse()`, `show()`.

## Базовый уровень

### Задание 1. Карта пути
Нарисуйте в тетради карту пути для JSON ниже и получите в Python следующие значения: название города, страну, название 2-й станции, количество всех станций, время последнего измерения.

```python
weather_station = {
    "city": {"name": "Алматы", "country": {"code": "KZ", "name": "Казахстан"}},
    "stations": [
        {"id": 1, "name": "Медеу", "measurements": [{"time": "08:00", "t": -7.5}, {"time": "14:00", "t": -2.1}]},
        {"id": 2, "name": "Центральная", "measurements": [{"time": "08:00", "t": -4.0}, {"time": "14:00", "t": 1.3}]},
        {"id": 3, "name": "Аэропорт", "measurements": [{"time": "08:00", "t": -5.2}]},
    ],
}
```

```text
Город: Алматы (Казахстан)
Станция 2: Центральная
Количество станций: 3
Последнее измерение (Аэропорт): 08:00
```

### Задание 2. Самая тёплая станция
По данным задания 1 вычислите среднюю температуру каждой станции и найдите самую тёплую.

```text
Медеу: -4.8 °C
Центральная: -1.4 °C
Аэропорт: -5.2 °C
Самая тёплая: Центральная
```

### Задание 3. Почасовой прогноз
Получите из Open-Meteo почасовую температуру в Алматы с параметрами `hourly=temperature_2m`, `forecast_days=1`, `timezone=auto`. С помощью `zip()` составьте пары «время — температура», показывайте только каждый 3-й час и найдите самый тёплый/самый холодный час дня.

```text
00:00  -6.3 °C
03:00  -7.1 °C
...
21:00  -3.9 °C
Самый тёплый: 14:00 (+0.8 °C)
Самый холодный: 06:00 (-7.6 °C)
```

### Задание 4. Топ-5 валют
По ответу `https://open.er-api.com/v6/latest/KZT` найдите 5 самых «дорогих» валют (то есть тех, 1 единица которых стоит больше всего тенге) и выведите их в виде таблицы с ценой в тенге. Время обновления покажите по `time_last_update_unix` в формате `ДД.ММ.ГГГГ ЧЧ:ММ`.

### Задание 5. Каталог пользователей
По ответу `https://jsonplaceholder.typicode.com/users` составьте выровненную таблицу: ID, имя, город, домен email (часть после знака `@`). Отсортируйте таблицу по имени в алфавитном порядке.

```text
ID  Имя                       Город            Домен
 5  Chelsey Dietrich          Roscoeview       annie.ca
10  Clementina DuBuque        Lebsackbury      rosamond.me
...
```

## Продвинутый уровень

### Задание 6. Недельный прогноз на русском
Получите прогноз на 7 дней (`daily=temperature_2m_max,temperature_2m_min,precipitation_sum,weather_code`) и выведите каждый день в формате «11 февраля, четверг». В конце — итог: средний максимум за неделю, количество дней с осадками, самый тёплый день. Если значение `None`, не включайте его в вычисления.

### Задание 7. Прогноз в Tkinter
Покажите результат задания 6 в окне Tkinter через `ttk.Treeview`. Сверху — название города и текущая температура (крупным шрифтом), снизу — надпись «Источник: Open-Meteo.com».

### Задание 8. JSON → CSV
Сохраните почасовые данные из задания 3 в файл `hourly.csv` (`time;temperature`). Этот файл пригодится для анализа в Pandas в VII модуле!

## ★ Олимпиадная / творческая задача

### Задание 9. Универсальный исследователь JSON
Рекурсивная функция `explore(obj, prefix="")` должна выводить все «пути» и типы значений любой структуры JSON. Для длинных списков показывать только первый элемент и указывать длину.

```text
latitude: float
current.time: str
current.temperature_2m: float
daily.time: list[3] → str
daily.temperature_2m_max: list[3] → float
```

### Задание 10. Текстовый график температуры
Покажите почасовую температуру в терминале вертикальным «графиком»: каждый час — одна строка, полоска `█` влево от нуля (отрицательная) или вправо (положительная).

```text
00:00  -6.3 ██████│
06:00  -7.6 ███████│
12:00  +0.5       │
15:00  +0.8       │█
```

## Домашнее задание

1. Примените функцию `explore()` из задания 9 к ответу открытого API, найденного в домашнем задании урока 66, и запишите карту пути.
2. Выведите данные этого API в виде красивой таблицы по шаблону `fetch → parse → show`.
3. К следующему уроку: откройте файл `.gitignore` какого-нибудь проекта на GitHub и посмотрите, что в нём написано.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–2**
```python
weather_station = {
    "city": {"name": "Алматы", "country": {"code": "KZ", "name": "Казахстан"}},
    "stations": [
        {"id": 1, "name": "Медеу", "measurements": [{"time": "08:00", "t": -7.5}, {"time": "14:00", "t": -2.1}]},
        {"id": 2, "name": "Центральная", "measurements": [{"time": "08:00", "t": -4.0}, {"time": "14:00", "t": 1.3}]},
        {"id": 3, "name": "Аэропорт", "measurements": [{"time": "08:00", "t": -5.2}]},
    ],
}
ws = weather_station
print(f"Город: {ws['city']['name']} ({ws['city']['country']['name']})")
print("Станция 2:", ws["stations"][1]["name"])
print("Количество станций:", len(ws["stations"]))
last = ws["stations"][-1]
print(f"Последнее измерение ({last['name']}): {last['measurements'][-1]['time']}")

averages = {}
for st in ws["stations"]:
    temps = [m["t"] for m in st["measurements"]]
    averages[st["name"]] = sum(temps) / len(temps)
    print(f"{st['name']}: {averages[st['name']]:.1f} °C")
print("Самая тёплая:", max(averages, key=averages.get))
```

**Задание 3**
```python
import requests

def fetch():
    params = {"latitude": 43.25, "longitude": 76.95, "hourly": "temperature_2m",
              "forecast_days": 1, "timezone": "auto"}
    r = requests.get("https://api.open-meteo.com/v1/forecast", params=params, timeout=10)
    r.raise_for_status()
    return r.json()

def parse(data):
    h = data["hourly"]
    return [(t[-5:], temp) for t, temp in zip(h["time"], h["temperature_2m"]) if temp is not None]

def show(pairs):
    for hour, temp in pairs[::3]:
        print(f"{hour}  {temp:+.1f} °C")
    warm = max(pairs, key=lambda p: p[1])
    cold = min(pairs, key=lambda p: p[1])
    print(f"Самый тёплый: {warm[0]} ({warm[1]:+.1f} °C)")
    print(f"Самый холодный: {cold[0]} ({cold[1]:+.1f} °C)")

show(parse(fetch()))
```

**Задание 4**
```python
from datetime import datetime
import requests

data = requests.get("https://open.er-api.com/v6/latest/KZT", timeout=10).json()
in_kzt = {code: 1 / rate for code, rate in data["rates"].items() if rate}
top = sorted(in_kzt.items(), key=lambda kv: kv[1], reverse=True)[:5]
updated = datetime.fromtimestamp(data["time_last_update_unix"])
print("Обновлено:", updated.strftime("%d.%m.%Y %H:%M"))
print(f"{'Валюта':<8}{'В тенге':>12}")
for code, price in top:
    print(f"{code:<8}{price:>12,.2f}")
```

**Задание 5**
```python
import requests

users = requests.get("https://jsonplaceholder.typicode.com/users", timeout=10).json()
print(f"{'ID':>2}  {'Имя':<25} {'Город':<16} Домен")
for u in sorted(users, key=lambda u: u["name"]):
    domain = u["email"].split("@")[1]
    print(f"{u['id']:>2}  {u['name']:<25} {u['address']['city']:<16} {domain}")
```

**Задание 6**
```python
from datetime import date
import requests

MONTHS = ["января", "февраля", "марта", "апреля", "мая", "июня",
          "июля", "августа", "сентября", "октября", "ноября", "декабря"]
WEEKDAYS = ["понедельник", "вторник", "среда", "четверг", "пятница", "суббота", "воскресенье"]

def fetch_week(lat=43.25, lon=76.95):
    params = {"latitude": lat, "longitude": lon, "timezone": "auto", "forecast_days": 7,
              "daily": "temperature_2m_max,temperature_2m_min,precipitation_sum,weather_code"}
    return requests.get("https://api.open-meteo.com/v1/forecast", params=params, timeout=10).json()

def parse_week(data):
    d = data["daily"]
    return [{"date": date.fromisoformat(t), "max": mx, "min": mn, "rain": pr, "code": c}
            for t, mx, mn, pr, c in zip(d["time"], d["temperature_2m_max"], d["temperature_2m_min"],
                                        d["precipitation_sum"], d["weather_code"])]

def kz_date(dt):
    return f"{dt.day} {MONTHS[dt.month - 1]}, {WEEKDAYS[dt.weekday()]}"

days = parse_week(fetch_week())
for d in days:
    print(f"{kz_date(d['date']):<24} {d['min']:+5.1f}…{d['max']:+5.1f} °C  осадки: {d['rain']} мм")
maxes = [d["max"] for d in days if d["max"] is not None]
rainy = sum(1 for d in days if (d["rain"] or 0) > 0)
warmest = max((d for d in days if d["max"] is not None), key=lambda d: d["max"])
print(f"Средний максимум: {sum(maxes) / len(maxes):.1f} °C")
print("Дней с осадками:", rainy)
print("Самый тёплый день:", kz_date(warmest["date"]))
```

**Задание 7** (считаем, что импортируем функции `fetch_week`, `parse_week`, `kz_date` из задания 6, имя файла `week.py`)
```python
import tkinter as tk
from tkinter import ttk
# from week import fetch_week, parse_week, kz_date

def show_window(city, days):
    root = tk.Tk()
    root.title(f"{city}: прогноз на неделю")
    tk.Label(root, text=city, font=("Arial", 22, "bold")).pack(pady=(10, 0))
    if days:
        tk.Label(root, text=f"Сегодня: {days[0]['min']:+.0f}…{days[0]['max']:+.0f} °C",
                 font=("Arial", 14)).pack()
    cols = ("date", "min", "max", "rain")
    tree = ttk.Treeview(root, columns=cols, show="headings", height=7)
    for c, title, w in zip(cols, ("Дата", "Мин", "Макс", "Осадки, мм"), (200, 70, 70, 90)):
        tree.heading(c, text=title)
        tree.column(c, width=w, anchor="center")
    for d in days:
        tree.insert("", "end", values=(kz_date(d["date"]), f"{d['min']:+.1f}", f"{d['max']:+.1f}", d["rain"]))
    tree.pack(padx=10, pady=10)
    tk.Label(root, text="Источник: Open-Meteo.com", fg="gray").pack(pady=(0, 8))
    root.mainloop()

# show_window("Алматы", parse_week(fetch_week()))
```

**Задание 8**
```python
import csv
import requests

params = {"latitude": 43.25, "longitude": 76.95, "hourly": "temperature_2m",
          "forecast_days": 1, "timezone": "auto"}
h = requests.get("https://api.open-meteo.com/v1/forecast", params=params, timeout=10).json()["hourly"]
with open("hourly.csv", "w", newline="", encoding="utf-8") as f:
    w = csv.writer(f, delimiter=";")
    w.writerow(["time", "temperature"])
    w.writerows(zip(h["time"], h["temperature_2m"]))
print("hourly.csv сохранён")
```

**Задание 9**
```python
def explore(obj, prefix=""):
    if isinstance(obj, dict):
        for key, value in obj.items():
            explore(value, f"{prefix}.{key}" if prefix else key)
    elif isinstance(obj, list):
        inner = type(obj[0]).__name__ if obj else "пусто"
        print(f"{prefix}: list[{len(obj)}] → {inner}")
        if obj and isinstance(obj[0], (dict, list)):
            explore(obj[0], prefix + "[0]")
    else:
        print(f"{prefix}: {type(obj).__name__}")

sample = {"latitude": 43.25,
          "current": {"time": "2027-02-11T14:00", "temperature_2m": -1.8},
          "daily": {"time": ["a", "b", "c"], "temperature_2m_max": [0.4, -1.2, 2.7]},
          "results": [{"name": "Алматы", "id": 1}]}
explore(sample)
```

**Задание 10**
```python
def text_chart(pairs, scale=1):
    width = int(max(abs(t) for _, t in pairs) * scale) + 1
    for hour, t in pairs:
        n = int(abs(t) * scale)
        left = ("█" * n).rjust(width) if t < 0 else " " * width
        right = "█" * n if t > 0 else ""
        print(f"{hour}  {t:+5.1f} {left}│{right}")

text_chart([("00:00", -6.3), ("06:00", -7.6), ("12:00", 0.5), ("15:00", 0.8), ("18:00", -2.4)])
```

</details>
