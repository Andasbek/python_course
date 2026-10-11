# Урок 72. Практические задания: приложение «Погода» (API + Tkinter), Самостоятельная работа №5

## Базовый уровень (шаги проекта)

### Шаг 1. Структура проекта
Создайте папку `weather_app`, venv, файлы `requirements.txt` (`requests`) и `.gitignore` (`.venv/`, `__pycache__/`, `settings.json`). `git init`, первый коммит.

### Шаг 2. `weather_api.py`
Напишите исключение `CityNotFound`, функции `find_city(name)`, `get_forecast(lat, lon, days=5)` и `describe(code)`. Результат `get_forecast` — удобный словарь:
```text
{"current": {"temp": -1.8, "feels": -5.9, "humidity": 71, "wind": 6.4, "code": 3, "time": "2027-02-23T14:00"},
 "daily": [{"date": "2027-02-23", "min": -7.0, "max": 0.4, "code": 3}, ...]}
```
Проверьте модуль, запустив его отдельно (`if __name__ == "__main__":`).

### Шаг 3. Каркас GUI
`app.py`: окно 520×520, строка поиска (`Entry` + кнопка «Поиск» + `Enter`), блок текущей погоды (название города, крупная температура, описание, дополнительные сведения), `ttk.Treeview` на 5 дней, внизу — источник и время обновления.

### Шаг 4. Подключение поиска
«Поиск» → проверка пустого поля → `find_city` → `get_forecast` → обновление экрана. Даты в формате «23 февраля, вт» (урок 67). Ошибки:
- `CityNotFound` → «Город не найден»;
- `requests.exceptions.ConnectionError` → «Невозможно подключиться к интернету»;
- `requests.exceptions.Timeout` → «Сервер не ответил»;
- другие `RequestException` → «Ошибка сервера».

### Шаг 5. Настройки и тестирование
Сохраняйте последний успешно найденный город в `settings.json`, при запуске загружайте его автоматически. Выполните чек-лист из 8 сценариев из теории. Коммит: «Погода v1.0».

## Продвинутый уровень

### Задание 6. Окно не должно зависать
Вынесите запрос в фоновый поток через `threading.Thread`, результат показывайте через `root.after(0, ...)`. Во время запроса кнопка отключена (`state="disabled"`), в строке статуса — «Загрузка...».

### Задание 7. Избранные города
`ttk.Combobox` или ряд кнопок: «Алматы», «Астана», «Шымкент» + города, которые пользователь добавляет кнопкой «★ Добавить в избранное». Список сохраняется в `settings.json`.

### Задание 8. Цвет и значок
Цвет фона должен меняться в зависимости от температуры: < −10 — голубоватый, −10…+10 — серый, > +10 — желтоватый. Эмодзи, соответствующий описанию, показывается крупным шрифтом.

## ★ Олимпиадная / творческая задача

### Задание 9. Почасовой график
С помощью `tk.Canvas` постройте график температуры на ближайшие 24 часа (ломаная линия, линия нуля, отметка каждые 3 часа). Данные — `hourly=temperature_2m`.

### Задание 10. Валюта + погода = «Путешественник»
Добавьте в приложение вторую вкладку (`ttk.Notebook`): «Валюта» — выбор страны/валюты, показ курса в тенге и конвертер (`open.er-api.com`).

## Критерии оценивания (практикум)

| Критерий | Баллы | % |
|---|---|---|
| Программа работает правильно: требования 1–6 ТЗ | 8 | 40% |
| Рациональность решения: разделение на слои, обработка ошибок, один запрос, настройки | 5 | 25% |
| Читаемость кода: имена, docstring, PEP 8 | 4 | 20% |
| Самостоятельно, вовремя; коммиты в Git | 3 | 15% |
| **Итого** | **20** | **100%** |

---

## Самостоятельная работа №5 (25 минут)

> Если интернета нет, педагог выдаёт файлы-образцы `forecast.json`, `rates.json`, `page.html`; тогда вместо `requests.get(...).json()` используйте `json.load(open(...))`.

### Вариант 1

1. **(3 балла)** Ответьте на вопросы: а) Каковы схема, домен, путь, параметры URL `https://api.open-meteo.com/v1/forecast?latitude=43.25&longitude=76.95`? б) Разница между кодами 404 и 500? в) Разница между GET и POST?
2. **(4 балла)** Напишите программу, которая с помощью `requests` получает и выводит текущую температуру и скорость ветра в Астане (51.17, 71.45) (`timeout`, `raise_for_status`, `try/except` обязательны).
3. **(4 балла)** По JSON прогноза на 3 дня (`daily.time`, `daily.temperature_2m_max`, `daily.temperature_2m_min`) вычислите среднюю температуру каждого дня и найдите самый тёплый день.
4. **(4 балла)** Выведите из данного HTML с помощью BeautifulSoup все заголовки новостей и ссылки:
```html
<div class="news"><h2><a href="/n/1">Итоги олимпиады</a></h2><span class="date">20.02.2027</span></div>
<div class="news"><h2><a href="/n/2">Открылся новый кружок</a></h2><span class="date">21.02.2027</span></div>
<div class="ad"><h2><a href="/ad">Реклама</a></h2></div>
```
   (рекламный блок выводиться не должен; превратите ссылки в полные URL с основой `https://school.kz`).
5. **(3 балла)** Напишите фрагмент кода, читающий API-ключ из файла `.env`, и укажите, какие 3 строки нужно добавить в `.gitignore`.
6. **(2 балла)** В robots.txt записано `Disallow: /private/` и `Crawl-delay: 4`. Может ли скрапер загрузить страницу `/private/list`? Сколько секунд нужно ждать между запросами?

### Вариант 2

1. **(3 балла)** Ответьте на вопросы: а) Зачем нужен DNS? б) Разница между кодами 401 и 403? в) Из каких частей состоит HTTP-запрос?
2. **(4 балла)** Напишите программу, которая через `https://open.er-api.com/v6/latest/USD` вычисляет и выводит, сколько тенге стоят 1 USD, 1 EUR, 1 RUB (`timeout`, обработка ошибок обязательны).
3. **(4 балла)** По JSON почасового прогноза (`hourly.time`, `hourly.temperature_2m`) найдите количество часов с температурой выше 0 °C и самый холодный час (значения `None` пропускайте).
4. **(4 балла)** Соберите с помощью BeautifulSoup из данной HTML-таблицы города и температуры в словарь и выведите самый холодный город:
```html
<table id="weather">
<tr><th>Город</th><th>Темп.</th></tr>
<tr><td>Астана</td><td>-14</td></tr>
<tr><td>Алматы</td><td>-2</td></tr>
<tr><td>Актау</td><td>4</td></tr>
</table>
```
5. **(3 балла)** API-ключ попал на GitHub. Запишите порядок действий в 4 шага.
6. **(2 балла)** Назовите 4 признака вежливого скрапера и напишите одну строку кода, отправляющую запрос с заголовком `User-Agent`.

### Критерии оценивания самостоятельной работы

| Критерий | % |
|---|---|
| Программа/ответ верны и соответствуют условию | 40% |
| Эффективность решения (`params`, `timeout`, обработка ошибок, `zip`) | 25% |
| Читаемость кода | 20% |
| Самостоятельность и своевременность выполнения | 15% |

Перевод баллов в оценку (20 баллов): 17–20 — высокий уровень, 13–16 — средний, 10–12 — удовлетворительно, ≤9 — пересдача.

## Домашнее задание

1. Напишите для проекта README.md (скриншот, запуск, возможности, «Источник: Open-Meteo.com») и загрузите на GitHub.
2. Подготовка к VII модулю: выполните `pip install numpy pandas matplotlib`.
3. Подумайте: если собирать данные о погоде в течение месяца, какие выводы можно было бы из них сделать?

## Для педагога: решения

<details><summary>Показать решения</summary>

**`weather_api.py`**
```python
"""Работа с Open-Meteo API: геокодирование и прогноз."""
import requests

GEO_URL = "https://geocoding-api.open-meteo.com/v1/search"
FORECAST_URL = "https://api.open-meteo.com/v1/forecast"
TIMEOUT = 10

WEATHER_CODES = {
    0: "Ясно ☀️", 1: "Преимущественно ясно 🌤", 2: "Переменная облачность ⛅", 3: "Облачно ☁️",
    45: "Туман 🌫", 48: "Изморозь, туман 🌫", 51: "Слабая морось", 53: "Морось",
    55: "Сильная морось", 61: "Слабый дождь 🌦", 63: "Дождь 🌧", 65: "Ливень 🌧",
    71: "Слабый снег 🌨", 73: "Снег 🌨", 75: "Сильный снег ❄️", 80: "Небольшие осадки",
    81: "Осадки", 82: "Сильные осадки", 95: "Гроза ⛈",
}


class CityNotFound(Exception):
    """Выбрасывается, если город не найден."""


def describe(code):
    return WEATHER_CODES.get(code, "Неизвестно")


def find_city(name):
    r = requests.get(GEO_URL, params={"name": name, "count": 1, "language": "ru"}, timeout=TIMEOUT)
    r.raise_for_status()
    results = r.json().get("results")
    if not results:
        raise CityNotFound(f"Город «{name}» не найден")
    c = results[0]
    return {"name": c["name"], "country": c.get("country", ""),
            "lat": c["latitude"], "lon": c["longitude"]}


def get_forecast(lat, lon, days=5):
    params = {
        "latitude": lat, "longitude": lon, "timezone": "auto", "forecast_days": days,
        "current": "temperature_2m,apparent_temperature,relative_humidity_2m,wind_speed_10m,weather_code",
        "daily": "temperature_2m_max,temperature_2m_min,weather_code",
    }
    r = requests.get(FORECAST_URL, params=params, timeout=TIMEOUT)
    r.raise_for_status()
    data = r.json()
    c, d = data["current"], data["daily"]
    current = {"temp": c["temperature_2m"], "feels": c["apparent_temperature"],
               "humidity": c["relative_humidity_2m"], "wind": c["wind_speed_10m"],
               "code": c["weather_code"], "time": c["time"]}
    daily = [{"date": t, "min": mn, "max": mx, "code": code}
             for t, mn, mx, code in zip(d["time"], d["temperature_2m_min"],
                                        d["temperature_2m_max"], d["weather_code"])]
    return {"current": current, "daily": daily}


if __name__ == "__main__":
    city = find_city("Алматы")
    print(city)
    print(get_forecast(city["lat"], city["lon"])["current"])
```

**`app.py`** (базовый + задание 6: фоновый поток)
```python
import json
import threading
import tkinter as tk
from datetime import date, datetime
from pathlib import Path
from tkinter import ttk, messagebox

import requests

from weather_api import CityNotFound, describe, find_city, get_forecast

SETTINGS = Path("settings.json")
MONTHS = ["января", "февраля", "марта", "апреля", "мая", "июня",
          "июля", "августа", "сентября", "октября", "ноября", "декабря"]
WEEKDAYS = ["пн", "вт", "ср", "чт", "пт", "сб", "вс"]


def kz_date(iso):
    d = date.fromisoformat(iso)
    return f"{d.day} {MONTHS[d.month - 1]}, {WEEKDAYS[d.weekday()]}"


def load_settings():
    try:
        return json.loads(SETTINGS.read_text(encoding="utf-8"))
    except (FileNotFoundError, json.JSONDecodeError):
        return {"last_city": "Алматы"}


def save_settings(data):
    SETTINGS.write_text(json.dumps(data, ensure_ascii=False, indent=2), encoding="utf-8")


class WeatherApp:
    def __init__(self, root):
        self.root = root
        root.title("Погода")
        root.geometry("520x520")
        self.settings = load_settings()

        top = ttk.Frame(root)
        top.pack(fill="x", padx=10, pady=10)
        ttk.Label(top, text="Город:").pack(side="left")
        self.city_entry = ttk.Entry(top, width=30)
        self.city_entry.pack(side="left", padx=5)
        self.search_btn = ttk.Button(top, text="Поиск", command=self.search)
        self.search_btn.pack(side="left")
        root.bind("<Return>", lambda e: self.search())

        self.city_label = tk.Label(root, font=("Arial", 16, "bold"))
        self.city_label.pack()
        self.temp_label = tk.Label(root, font=("Arial", 40))
        self.temp_label.pack()
        self.desc_label = tk.Label(root, font=("Arial", 14))
        self.desc_label.pack()
        self.details_label = tk.Label(root, font=("Arial", 11))
        self.details_label.pack(pady=5)

        cols = ("date", "min", "max", "desc")
        self.tree = ttk.Treeview(root, columns=cols, show="headings", height=5)
        for col, title, w in zip(cols, ("Дата", "Мин", "Макс", "Погода"), (150, 60, 60, 190)):
            self.tree.heading(col, text=title)
            self.tree.column(col, width=w, anchor="center")
        self.tree.pack(padx=10, pady=10)

        self.status = tk.Label(root, text="Источник: Open-Meteo.com", fg="gray", anchor="w")
        self.status.pack(fill="x", side="bottom", padx=10, pady=5)

        self.city_entry.insert(0, self.settings.get("last_city", "Алматы"))
        self.search()

    def search(self):
        name = self.city_entry.get().strip()
        if not name:
            messagebox.showwarning("Предупреждение", "Введите название города")
            return
        self.search_btn.config(state="disabled")
        self.status.config(text="Загрузка...")
        threading.Thread(target=self.worker, args=(name,), daemon=True).start()

    def worker(self, name):
        """Фоновый поток: только получает данные, виджеты не трогает."""
        try:
            city = find_city(name)
            forecast = get_forecast(city["lat"], city["lon"])
            self.root.after(0, self.show, city, forecast)
        except CityNotFound as e:
            self.root.after(0, self.show_error, str(e))
        except requests.exceptions.ConnectionError:
            self.root.after(0, self.show_error, "Невозможно подключиться к интернету")
        except requests.exceptions.Timeout:
            self.root.after(0, self.show_error, "Сервер не ответил")
        except requests.exceptions.RequestException:
            self.root.after(0, self.show_error, "Ошибка сервера, попробуйте позже")

    def show(self, city, forecast):
        cur = forecast["current"]
        self.city_label.config(text=f"{city['name']}, {city['country']}")
        self.temp_label.config(text=f"{cur['temp']:+.0f} °C")
        self.desc_label.config(text=describe(cur["code"]))
        self.details_label.config(text=f"Ощущается: {cur['feels']:+.0f} °C · "
                                       f"Влажность: {cur['humidity']}% · Ветер: {cur['wind']:.0f} км/ч")
        self.tree.delete(*self.tree.get_children())
        for day in forecast["daily"]:
            self.tree.insert("", "end", values=(kz_date(day["date"]), f"{day['min']:+.0f}°",
                                                f"{day['max']:+.0f}°", describe(day["code"])))
        self.status.config(text=f"Источник: Open-Meteo.com · Обновлено {datetime.now():%H:%M}")
        self.search_btn.config(state="normal")
        self.settings["last_city"] = city["name"]
        save_settings(self.settings)

    def show_error(self, text):
        self.search_btn.config(state="normal")
        self.status.config(text="Источник: Open-Meteo.com")
        messagebox.showerror("Ошибка", text)


if __name__ == "__main__":
    root = tk.Tk()
    WeatherApp(root)
    root.mainloop()
```

**Задание 8** (добавить в метод `show`)
```python
def bg_for(temp):
    if temp < -10:
        return "#dbe9ff"
    if temp <= 10:
        return "#eeeeee"
    return "#ffe8c2"

# color = bg_for(cur["temp"])
# for w in (self.root, self.city_label, self.temp_label, self.desc_label, self.details_label):
#     w.config(bg=color)
print(bg_for(-15), bg_for(3), bg_for(22))
```

**Задание 9** (функция построения графика)
```python
import tkinter as tk

def draw_chart(canvas, temps, width=480, height=160, pad=20):
    canvas.delete("all")
    lo, hi = min(temps + [0]), max(temps + [0])
    span = (hi - lo) or 1
    step = (width - 2 * pad) / (len(temps) - 1)
    y = lambda t: height - pad - (t - lo) / span * (height - 2 * pad)
    canvas.create_line(pad, y(0), width - pad, y(0), fill="gray", dash=(3, 3))
    points = []
    for i, t in enumerate(temps):
        points += [pad + i * step, y(t)]
        if i % 3 == 0:
            canvas.create_text(pad + i * step, height - 8, text=f"{i:02d}", font=("Arial", 7))
    canvas.create_line(*points, fill="red", width=2, smooth=True)

root = tk.Tk()
c = tk.Canvas(root, width=480, height=160, bg="white")
c.pack()
draw_chart(c, [-6, -7, -7, -8, -7, -5, -3, -1, 0, 1, 1, 0, -1, -2, -3, -4, -4, -5, -5, -6, -6, -6, -7, -7])
root.mainloop()
```

**Самостоятельная работа №5, вариант 1**

1. а) схема `https`, домен `api.open-meteo.com`, путь `/v1/forecast`, параметры `latitude=43.25`, `longitude=76.95`; б) 404 — ошибка клиента (ресурса нет), 500 — внутренняя ошибка сервера; в) GET — получение данных (параметры в URL), POST — отправка данных (внутри тела).
```python
# 2
import requests

try:
    r = requests.get("https://api.open-meteo.com/v1/forecast",
                     params={"latitude": 51.17, "longitude": 71.45,
                             "current": "temperature_2m,wind_speed_10m"}, timeout=10)
    r.raise_for_status()
    c = r.json()["current"]
    print(f"Астана: {c['temperature_2m']} °C, ветер {c['wind_speed_10m']} км/ч")
except requests.exceptions.RequestException as e:
    print("Ошибка:", e)
```
```python
# 3
data = {"daily": {"time": ["2027-02-23", "2027-02-24", "2027-02-25"],
                  "temperature_2m_max": [0.4, -1.2, 2.7], "temperature_2m_min": [-6.1, -8.3, -4.0]}}
d = data["daily"]
avgs = {t: (mx + mn) / 2 for t, mx, mn in zip(d["time"], d["temperature_2m_max"], d["temperature_2m_min"])}
for t, a in avgs.items():
    print(t, f"{a:.1f}")
print("Самый тёплый день:", max(avgs, key=avgs.get))
```
```python
# 4
from urllib.parse import urljoin
from bs4 import BeautifulSoup

html = """<div class="news"><h2><a href="/n/1">Итоги олимпиады</a></h2><span class="date">20.02.2027</span></div>
<div class="news"><h2><a href="/n/2">Открылся новый кружок</a></h2><span class="date">21.02.2027</span></div>
<div class="ad"><h2><a href="/ad">Реклама</a></h2></div>"""
soup = BeautifulSoup(html, "html.parser")
for a in soup.select("div.news h2 a"):
    print(a.get_text(strip=True), "→", urljoin("https://school.kz", a["href"]))
```
```python
# 5
import os
from dotenv import load_dotenv

load_dotenv()
API_KEY = os.getenv("WEATHER_API_KEY")
# .gitignore: .env  /  .venv/  /  __pycache__/
```
6. Нет, раздел `/private/` запрещён; между запросами — не менее 4 секунд.

**Самостоятельная работа №5, вариант 2**

1. а) DNS превращает доменное имя в IP-адрес; б) 401 — нет авторизации (неизвестно, кто вы), 403 — вас узнали, но доступа нет; в) начальная строка (метод, путь, версия), заголовки, пустая строка, тело.
```python
# 2
import requests

try:
    r = requests.get("https://open.er-api.com/v6/latest/USD", timeout=10)
    r.raise_for_status()
    rates = r.json()["rates"]
    for code in ("USD", "EUR", "RUB"):
        print(f"1 {code} = {rates['KZT'] / rates[code]:.2f} ₸")
except requests.exceptions.RequestException as e:
    print("Ошибка:", e)
```
```python
# 3
hourly = {"time": ["00:00", "01:00", "02:00", "03:00"], "temperature_2m": [-1.5, None, 0.5, 1.2]}
pairs = [(t, v) for t, v in zip(hourly["time"], hourly["temperature_2m"]) if v is not None]
warm = sum(1 for _, v in pairs if v > 0)
coldest = min(pairs, key=lambda p: p[1])
print("Часов выше 0 °C:", warm)
print("Самый холодный час:", coldest[0], coldest[1])
```
```python
# 4
from bs4 import BeautifulSoup

html = """<table id="weather"><tr><th>Город</th><th>Темп.</th></tr>
<tr><td>Астана</td><td>-14</td></tr><tr><td>Алматы</td><td>-2</td></tr><tr><td>Актау</td><td>4</td></tr></table>"""
soup = BeautifulSoup(html, "html.parser")
temps = {}
for tr in soup.find("table", id="weather").find_all("tr")[1:]:
    city, t = [td.get_text(strip=True) for td in tr.find_all("td")]
    temps[city] = int(t)
print(temps)
print("Самый холодный:", min(temps, key=temps.get))
```
5. 1) Немедленно удалить/заменить ключ в сервисе; 2) записать новый ключ в `.env`, добавить `.env` в `.gitignore`, `git rm --cached .env`; 3) проверить историю использования в сервисе; 4) при необходимости очистить историю репозитория или сделать его закрытым.
6. Соблюдает robots.txt, делает паузы между запросами, представляется через `User-Agent`, использует кэш / не собирает персональные данные.
```python
import requests
r = requests.get("https://books.toscrape.com", headers={"User-Agent": "PythonClubBot/1.0"}, timeout=10)
```

</details>
