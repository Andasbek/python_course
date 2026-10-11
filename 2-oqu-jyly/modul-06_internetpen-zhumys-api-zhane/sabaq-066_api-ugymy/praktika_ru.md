# Урок 66. Практические задания: понятие API, погода и курс валют

> Используемые открытые API (ключ не нужен):
> - погода: `https://api.open-meteo.com/v1/forecast`
> - геокодирование: `https://geocoding-api.open-meteo.com/v1/search`
> - валюты: `https://open.er-api.com/v6/latest/<валюта>`

## Базовый уровень

### Задание 1. Изучение документации (в тетради + в браузере)
Откройте страницу `https://open-meteo.com/en/docs` и ответьте:
1. Какие base URL и эндпоинт?
2. Какие параметры обязательны?
3. В чём разница между `temperature_2m` и `apparent_temperature`?
4. С помощью конструктора составьте URL максимальной/минимальной температуры на 3 дня для города Туркестан и откройте его в браузере. Запишите основные ключи JSON.

### Задание 2. Погода в моём городе
По координатам своего города выведите текущую температуру, влажность, скорость ветра и описание погоды (через словарь кодов WMO).

```text
Алматы, 14:15
Температура: -1.8 °C
Влажность: 71 %
Ветер: 6.4 km/h
Облачно ☁️
```

### Задание 3. Любой город
Пользователь вводит название города → API геокодирования находит координаты → API погоды даёт температуру. Если город не найден — сообщение.

```text
Город: Актау
Актау (Казахстан): 43.65, 51.17
Сейчас: +4.2 °C, Переменная облачность ⛅
```
```text
Город: Абвгдеж
Город «Абвгдеж» не найден
```

### Задание 4. Курс валют
Получите через `https://open.er-api.com/v6/latest/KZT` курсы относительно тенге и рассчитайте, сколько тенге стоят 1 USD, 1 EUR, 1 RUB, 1 CNY (подсказка: 1 USD = 1 / rates["USD"] тенге).

```text
Курс обновлён: Tue, 09 Feb 2027 00:02:31 +0000
1 USD = 512.40 ₸
1 EUR = 556.96 ₸
1 RUB = 5.62 ₸
1 CNY = 71.36 ₸
```

### Задание 5. Конвертер
Пользователь вводит сумму, код исходной и целевой валюты. Программа вычисляет результат. Если код валюты неверный — сообщение.

```text
Сумма: 250
Из какой валюты: EUR
В какую валюту: KZT
250.00 EUR = 139 239.13 KZT
```

## Продвинутый уровень

### Задание 6. Прогноз на 3 дня
С параметрами `daily=temperature_2m_max,temperature_2m_min,weather_code`, `timezone=auto`, `forecast_days=3` выведите прогноз для города. Показывайте дни с названиями дней недели на русском языке (`datetime.date.fromisoformat(...).weekday()`).

```text
Вторник    09.02:  -6°…  0°  Облачно ☁️
Среда      10.02:  -8°… -2°  Снег 🌨
Четверг    11.02: -10°… -3°  Ясно ☀️
```

### Задание 7. Рейтинг городов
Получите текущую температуру в 6 городах Казахстана (Алматы, Астана, Шымкент, Актобе, Караганда, Усть-Каменогорск) и выведите их, отсортировав от самого тёплого к самому холодному. Open-Meteo принимает в одном запросе несколько координат: `latitude=43.25,51.17,...` — используйте эту возможность и отправьте 1 запрос вместо 6 (ответ — список).

### Задание 8. Кэш курсов
Сохраняйте курсы вместе со временем в файл `rates_cache.json`. При запуске программы: если кэш моложе 12 часов — читать из файла, если старше — заново получать из API. Выводите на экран источник данных: «из кэша» или «из API».

## ★ Олимпиадная / творческая задача

### Задание 9. Советчик «Что надеть?»
Создайте программу, которая по данным о погоде (температура, `apparent_temperature`, осадки, ветер) даёт ученику совет по одежде: «Надень куртку, шапку и перчатки. Зонт не нужен». Правила придумайте сами (не менее 6 правил).

### Задание 10. Бюджет путешествия
Пользователь вводит страну (её валюту: `USD`, `TRY`, `AED`, `GEL` и т.д.) и дневные расходы в этой валюте. Программа рассчитывает стоимость 7-дневной поездки в тенге и сравнивает с пределом «бюджет: 500 000 ₸». Выведите сравнительную таблицу по нескольким странам.

## Домашнее задание

1. Найдите в интернете ещё один открытый API без ключа (подсказка: `https://github.com/public-apis/public-apis`, столбец «Auth: No»). Запишите его эндпоинт, параметры и отправьте один запрос из Python.
2. Разбейте задание 3 на функции: `find_city()`, `get_weather()`, `show()`.
3. Подумайте: какие данные в JSON от API оказались нам «лишними»?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Общий модуль — `weather_api.py`** (используется в заданиях 2, 3, 6, 9)
```python
import requests

FORECAST_URL = "https://api.open-meteo.com/v1/forecast"
GEO_URL = "https://geocoding-api.open-meteo.com/v1/search"

WEATHER_CODES = {
    0: "Ясно ☀️", 1: "Преимущественно ясно 🌤", 2: "Переменная облачность ⛅", 3: "Облачно ☁️",
    45: "Туман 🌫", 48: "Изморозь, туман 🌫", 51: "Слабая морось", 53: "Морось",
    55: "Сильная морось", 61: "Слабый дождь 🌦", 63: "Дождь 🌧", 65: "Ливень 🌧",
    71: "Слабый снег 🌨", 73: "Снег 🌨", 75: "Сильный снег ❄️", 80: "Небольшие осадки",
    81: "Осадки", 82: "Сильные осадки", 95: "Гроза ⛈",
}


def describe(code):
    return WEATHER_CODES.get(code, f"Неизвестно ({code})")


def find_city(name):
    r = requests.get(GEO_URL, params={"name": name, "count": 1, "language": "ru"}, timeout=10)
    r.raise_for_status()
    results = r.json().get("results")
    if not results:
        return None
    c = results[0]
    return {"name": c["name"], "country": c.get("country", ""),
            "lat": c["latitude"], "lon": c["longitude"]}


def current_weather(lat, lon):
    params = {"latitude": lat, "longitude": lon, "timezone": "auto",
              "current": "temperature_2m,relative_humidity_2m,wind_speed_10m,weather_code"}
    r = requests.get(FORECAST_URL, params=params, timeout=10)
    r.raise_for_status()
    data = r.json()
    return data["current"], data["current_units"]
```

**Задание 2**
```python
from weather_api import current_weather, describe

cur, units = current_weather(43.25, 76.95)
print("Алматы,", cur["time"][-5:])
print("Температура:", cur["temperature_2m"], units["temperature_2m"])
print("Влажность:", cur["relative_humidity_2m"], units["relative_humidity_2m"])
print("Ветер:", cur["wind_speed_10m"], units["wind_speed_10m"])
print(describe(cur["weather_code"]))
```

**Задание 3**
```python
import requests
from weather_api import find_city, current_weather, describe

name = input("Город: ").strip()
try:
    city = find_city(name)
    if city is None:
        print(f"Город «{name}» не найден")
    else:
        print(f"{city['name']} ({city['country']}): {city['lat']:.2f}, {city['lon']:.2f}")
        cur, _ = current_weather(city["lat"], city["lon"])
        print(f"Сейчас: {cur['temperature_2m']:+.1f} °C, {describe(cur['weather_code'])}")
except requests.exceptions.RequestException as e:
    print("Ошибка сети:", e)
```

**Задание 4**
```python
import requests

data = requests.get("https://open.er-api.com/v6/latest/KZT", timeout=10).json()
if data["result"] == "success":
    print("Курс обновлён:", data["time_last_update_utc"])
    for code in ("USD", "EUR", "RUB", "CNY"):
        print(f"1 {code} = {1 / data['rates'][code]:.2f} ₸")
```

**Задание 5**
```python
import requests

rates = requests.get("https://open.er-api.com/v6/latest/USD", timeout=10).json()["rates"]
amount = float(input("Сумма: ").replace(",", "."))
src = input("Из какой валюты: ").strip().upper()
dst = input("В какую валюту: ").strip().upper()
if src not in rates or dst not in rates:
    print("Неизвестный код валюты")
else:
    result = amount / rates[src] * rates[dst]
    print(f"{amount:.2f} {src} = {result:,.2f} {dst}".replace(",", " "))
```

**Задание 6**
```python
from datetime import date
import requests
from weather_api import describe, FORECAST_URL

DAYS = ["Понедельник", "Вторник", "Среда", "Четверг", "Пятница", "Суббота", "Воскресенье"]
params = {"latitude": 43.25, "longitude": 76.95, "timezone": "auto", "forecast_days": 3,
          "daily": "temperature_2m_max,temperature_2m_min,weather_code"}
d = requests.get(FORECAST_URL, params=params, timeout=10).json()["daily"]
for day, tmax, tmin, code in zip(d["time"], d["temperature_2m_max"],
                                 d["temperature_2m_min"], d["weather_code"]):
    dt = date.fromisoformat(day)
    print(f"{DAYS[dt.weekday()]:10} {dt:%d.%m}: {tmin:3.0f}°…{tmax:3.0f}°  {describe(code)}")
```

**Задание 7**
```python
import requests

cities = {"Алматы": (43.25, 76.95), "Астана": (51.17, 71.45), "Шымкент": (42.32, 69.59),
          "Актобе": (50.28, 57.17), "Караганда": (49.80, 73.10), "Усть-Каменогорск": (49.95, 82.61)}
lats = ",".join(str(v[0]) for v in cities.values())
lons = ",".join(str(v[1]) for v in cities.values())
data = requests.get("https://api.open-meteo.com/v1/forecast",
                    params={"latitude": lats, "longitude": lons, "current": "temperature_2m"},
                    timeout=10).json()          # если точек несколько — список
temps = [(name, item["current"]["temperature_2m"]) for name, item in zip(cities, data)]
for i, (name, t) in enumerate(sorted(temps, key=lambda x: x[1], reverse=True), 1):
    print(f"{i}. {name:16} {t:+.1f} °C")
```

**Задание 8**
```python
import json
import time
from pathlib import Path
import requests

CACHE = Path("rates_cache.json")
MAX_AGE = 12 * 3600

def get_rates():
    if CACHE.exists():
        cache = json.loads(CACHE.read_text(encoding="utf-8"))
        if time.time() - cache["saved_at"] < MAX_AGE:
            return cache["rates"], "из кэша"
    data = requests.get("https://open.er-api.com/v6/latest/USD", timeout=10).json()
    CACHE.write_text(json.dumps({"saved_at": time.time(), "rates": data["rates"]}), encoding="utf-8")
    return data["rates"], "из API"

rates, source = get_rates()
print(f"1 USD = {rates['KZT']:.2f} KZT ({source})")
```

**Задание 9** (образец правил)
```python
import requests

params = {"latitude": 43.25, "longitude": 76.95,
          "current": "temperature_2m,apparent_temperature,precipitation,wind_speed_10m"}
c = requests.get("https://api.open-meteo.com/v1/forecast", params=params, timeout=10).json()["current"]
feels, rain, wind = c["apparent_temperature"], c["precipitation"], c["wind_speed_10m"]
advice = []
if feels < -15:
    advice.append("Надень тёплый пуховик, шапку, перчатки и шарф")
elif feels < 0:
    advice.append("Надень зимнюю куртку и шапку")
elif feels < 10:
    advice.append("Надень лёгкую куртку или свитер")
elif feels < 20:
    advice.append("Достаточно рубашки и лёгкой кофты")
else:
    advice.append("Надень футболку, возьми с собой воду")
if rain > 0:
    advice.append("Возьми зонт")
if wind > 30:
    advice.append("Сильный ветер — пригодится капюшон")
print(f"Ощущаемая температура: {feels} °C")
print(". ".join(advice) + ".")
```

**Задание 10**
```python
import requests

rates = requests.get("https://open.er-api.com/v6/latest/KZT", timeout=10).json()["rates"]
BUDGET = 500_000
trips = {"Турция": ("TRY", 2500), "ОАЭ": ("AED", 400), "Грузия": ("GEL", 200), "США": ("USD", 150)}
print(f"{'Страна':8} {'7 дней, ₸':>12}  Бюджет")
for country, (cur, per_day) in trips.items():
    total_kzt = per_day * 7 / rates[cur]
    status = "хватает" if total_kzt <= BUDGET else "не хватает"
    print(f"{country:8} {total_kzt:>12,.0f}  {status}")
```

</details>
