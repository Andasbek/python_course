# Сабақ 72. Практикалық тапсырмалар: «Ауа райы» қосымшасы (API + Tkinter), Өзіндік жұмыс №5

## Базалық деңгей (жоба қадамдары)

### 1-қадам. Жоба құрылымы
`weather_app` папкасын, venv-ті, `requirements.txt` (`requests`) және `.gitignore` (`.venv/`, `__pycache__/`, `settings.json`) файлдарын жасаңыз. `git init`, алғашқы коммит.

### 2-қадам. `weather_api.py`
`CityNotFound` ерекшелігін, `find_city(name)`, `get_forecast(lat, lon, days=5)` және `describe(code)` функцияларын жазыңыз. `get_forecast` нәтижесі — ыңғайлы сөздік:
```text
{"current": {"temp": -1.8, "feels": -5.9, "humidity": 71, "wind": 6.4, "code": 3, "time": "2027-02-23T14:00"},
 "daily": [{"date": "2027-02-23", "min": -7.0, "max": 0.4, "code": 3}, ...]}
```
Модульді жеке іске қосып (`if __name__ == "__main__":`) тексеріңіз.

### 3-қадам. GUI қаңқасы
`app.py`: терезе 520×520, іздеу жолағы (`Entry` + «Іздеу» батырмасы + `Enter`), қазіргі ауа райы блогы (қала атауы, үлкен температура, сипаттама, қосымша мәліметтер), 5 күндік `ttk.Treeview`, төменде дереккөз пен жаңарту уақыты.

### 4-қадам. Іздеуді байланыстыру
«Іздеу» → бос өріс тексеру → `find_city` → `get_forecast` → экранды жаңарту. Күндерді «23 ақпан, сс» форматында (67-сабақ). Қателер:
- `CityNotFound` → «Қала табылмады»;
- `requests.exceptions.ConnectionError` → «Интернетке қосылу мүмкін емес»;
- `requests.exceptions.Timeout` → «Сервер жауап бермеді»;
- басқа `RequestException` → «Сервер қатесі».

### 5-қадам. Баптаулар және тестілеу
Соңғы сәтті ізделген қаланы `settings.json`-ға сақтаңыз, іске қосылғанда автоматты жүктеңіз. Теориядағы 8 сценарийлік чек-парақты орындаңыз. Коммит: «Ауа райы v1.0».

## Кеңейтілген деңгей

### 6-тапсырма. Терезе қатпасын
Сұранысты `threading.Thread` арқылы фондық ағынға шығарыңыз, нәтижені `root.after(0, ...)` арқылы көрсетіңіз. Сұраныс кезінде батырма өшірулі (`state="disabled"`), статус жолында «Жүктелуде...».

### 7-тапсырма. Таңдаулы қалалар
`ttk.Combobox` немесе батырмалар қатары: «Алматы», «Астана», «Шымкент» + пайдаланушы «★ Таңдаулыға қосу» батырмасымен қосатын қалалар. Тізім `settings.json`-да сақталады.

### 8-тапсырма. Түс пен белгіше
Температураға қарай фон түсі өзгерсін: < −10 — көкшіл, −10…+10 — сұр, > +10 — сарғыш. Сипаттамаға сәйкес эмодзи үлкен қаріппен көрсетілсін.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Сағаттық график
`tk.Canvas` арқылы келесі 24 сағаттың температура графигін (сынық сызық, нөл сызығы, әр 3 сағатта белгі) салыңыз. Деректер — `hourly=temperature_2m`.

### 10-тапсырма. Валюта + ауа райы = «Саяхатшы»
Қосымшаға екінші қойынды (`ttk.Notebook`) қосыңыз: «Валюта» — ел/валюта таңдап, теңгемен бағамды және айырбастағышты көрсетеді (`open.er-api.com`).

## Бағалау критерийлері (практикум)

| Критерий | Ұпай | % |
|---|---|---|
| Бағдарлама дұрыс жұмыс істейді: ТТ-ның 1–6 талабы | 8 | 40% |
| Шешімнің ұтымдылығы: қабаттарға бөлу, қателерді өңдеу, бір сұраныс, баптаулар | 5 | 25% |
| Кодтың оқылымдылығы: атаулар, docstring, PEP 8 | 4 | 20% |
| Өз бетінше, уақытында; Git коммиттері | 3 | 15% |
| **Барлығы** | **20** | **100%** |

---

## Өзіндік жұмыс №5 (25 минут)

> Интернет болмаса, педагог `forecast.json`, `rates.json`, `page.html` үлгі файлдарын береді; онда `requests.get(...).json()` орнына `json.load(open(...))` қолданыңыз.

### 1-нұсқа

1. **(3 ұпай)** Сұрақтарға жауап беріңіз: а) `https://api.open-meteo.com/v1/forecast?latitude=43.25&longitude=76.95` URL-інің схемасы, домені, жолы, параметрлері қандай? ә) 404 және 500 кодтарының айырмашылығы? б) GET мен POST айырмашылығы?
2. **(4 ұпай)** `requests` арқылы Астананың (51.17, 71.45) қазіргі температурасы мен жел жылдамдығын алып шығаратын бағдарлама жазыңыз (`timeout`, `raise_for_status`, `try/except` міндетті).
3. **(4 ұпай)** 3 күндік болжам JSON-ынан (`daily.time`, `daily.temperature_2m_max`, `daily.temperature_2m_min`) әр күннің орташа температурасын есептеп, ең жылы күнді табыңыз.
4. **(4 ұпай)** Берілген HTML-ден BeautifulSoup арқылы барлық жаңалық тақырыптары мен сілтемелерін шығарыңыз:
```html
<div class="news"><h2><a href="/n/1">Олимпиада нәтижелері</a></h2><span class="date">20.02.2027</span></div>
<div class="news"><h2><a href="/n/2">Жаңа үйірме ашылды</a></h2><span class="date">21.02.2027</span></div>
<div class="ad"><h2><a href="/ad">Жарнама</a></h2></div>
```
   (жарнама блогы шықпауы керек; сілтемелерді `https://school.kz` негізімен толық URL-ге айналдырыңыз).
5. **(3 ұпай)** API кілтін `.env` файлынан оқитын код үзіндісін жазыңыз және `.gitignore`-ға қандай 3 жол қосу керектігін көрсетіңіз.
6. **(2 ұпай)** robots.txt-те `Disallow: /private/` және `Crawl-delay: 4` жазылған. Скрапер `/private/list` бетін жүктей ала ма? Сұраныстар арасында неше секунд күту керек?

### 2-нұсқа

1. **(3 ұпай)** Сұрақтарға жауап беріңіз: а) DNS не үшін керек? ә) 401 және 403 кодтарының айырмашылығы? б) HTTP сұранысы қандай бөліктерден тұрады?
2. **(4 ұпай)** `https://open.er-api.com/v6/latest/USD` арқылы 1 USD, 1 EUR, 1 RUB неше теңге екенін есептеп шығаратын бағдарлама жазыңыз (`timeout`, қателерді өңдеу міндетті).
3. **(4 ұпай)** Сағаттық болжам JSON-ынан (`hourly.time`, `hourly.temperature_2m`) температурасы 0 °C-тан жоғары сағаттар санын және ең суық сағатты табыңыз (`None` мәндерін өткізіп жіберіңіз).
4. **(4 ұпай)** Берілген HTML кестесінен BeautifulSoup арқылы қалалар мен температураларды сөздікке жинап, ең суық қаланы шығарыңыз:
```html
<table id="weather">
<tr><th>Қала</th><th>Темп.</th></tr>
<tr><td>Астана</td><td>-14</td></tr>
<tr><td>Алматы</td><td>-2</td></tr>
<tr><td>Ақтау</td><td>4</td></tr>
</table>
```
5. **(3 ұпай)** API кілті GitHub-қа жүктеліп кетті. Әрекет ету ретін 4 қадаммен жазыңыз.
6. **(2 ұпай)** Әдепті скрапердің 4 белгісін атаңыз және `User-Agent` тақырыбын қосып сұраныс жіберетін бір жол код жазыңыз.

### Өзіндік жұмысты бағалау критерийлері

| Критерий | % |
|---|---|
| Бағдарлама/жауап дұрыс және шартқа сай | 40% |
| Шешімнің тиімділігі (`params`, `timeout`, қателерді өңдеу, `zip`) | 25% |
| Кодтың оқылымдылығы | 20% |
| Өз бетінше және уақытында орындау | 15% |

Ұпайды бағаға аудару (20 ұпай): 17–20 — жоғары деңгей, 13–16 — орта, 10–12 — қанағаттанарлық, ≤9 — қайта тапсыру.

## Үй тапсырмасы

1. Жобаға README.md жазыңыз (скриншот, іске қосу, мүмкіндіктер, «Дереккөз: Open-Meteo.com»), GitHub-қа жүктеңіз.
2. VII модульге дайындық: `pip install numpy pandas matplotlib` орындаңыз.
3. Ойланыңыз: ауа райы деректерін бір ай бойы жинасаңыз, олардан қандай қорытынды жасауға болар еді?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**`weather_api.py`**
```python
"""Open-Meteo API-мен жұмыс: геокодтау және болжам."""
import requests

GEO_URL = "https://geocoding-api.open-meteo.com/v1/search"
FORECAST_URL = "https://api.open-meteo.com/v1/forecast"
TIMEOUT = 10

WEATHER_CODES = {
    0: "Ашық аспан ☀️", 1: "Негізінен ашық 🌤", 2: "Ішінара бұлтты ⛅", 3: "Бұлтты ☁️",
    45: "Тұман 🌫", 48: "Қырау тұманы 🌫", 51: "Әлсіз сіркіреме", 53: "Сіркіреме",
    55: "Күшті сіркіреме", 61: "Әлсіз жаңбыр 🌦", 63: "Жаңбыр 🌧", 65: "Нөсер 🌧",
    71: "Әлсіз қар 🌨", 73: "Қар 🌨", 75: "Қалың қар ❄️", 80: "Аздаған жауын",
    81: "Жауын", 82: "Қатты жауын", 95: "Найзағай ⛈",
}


class CityNotFound(Exception):
    """Қала табылмағанда лақтырылады."""


def describe(code):
    return WEATHER_CODES.get(code, "Белгісіз")


def find_city(name):
    r = requests.get(GEO_URL, params={"name": name, "count": 1, "language": "ru"}, timeout=TIMEOUT)
    r.raise_for_status()
    results = r.json().get("results")
    if not results:
        raise CityNotFound(f"«{name}» қаласы табылмады")
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

**`app.py`** (базалық + 6-тапсырма: фондық ағын)
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
MONTHS = ["қаңтар", "ақпан", "наурыз", "сәуір", "мамыр", "маусым",
          "шілде", "тамыз", "қыркүйек", "қазан", "қараша", "желтоқсан"]
WEEKDAYS = ["дс", "сс", "ср", "бс", "жм", "сб", "жс"]


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
        root.title("Ауа райы")
        root.geometry("520x520")
        self.settings = load_settings()

        top = ttk.Frame(root)
        top.pack(fill="x", padx=10, pady=10)
        ttk.Label(top, text="Қала:").pack(side="left")
        self.city_entry = ttk.Entry(top, width=30)
        self.city_entry.pack(side="left", padx=5)
        self.search_btn = ttk.Button(top, text="Іздеу", command=self.search)
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
        for col, title, w in zip(cols, ("Күні", "Мин", "Макс", "Ауа райы"), (150, 60, 60, 190)):
            self.tree.heading(col, text=title)
            self.tree.column(col, width=w, anchor="center")
        self.tree.pack(padx=10, pady=10)

        self.status = tk.Label(root, text="Дереккөз: Open-Meteo.com", fg="gray", anchor="w")
        self.status.pack(fill="x", side="bottom", padx=10, pady=5)

        self.city_entry.insert(0, self.settings.get("last_city", "Алматы"))
        self.search()

    def search(self):
        name = self.city_entry.get().strip()
        if not name:
            messagebox.showwarning("Ескерту", "Қала атауын енгізіңіз")
            return
        self.search_btn.config(state="disabled")
        self.status.config(text="Жүктелуде...")
        threading.Thread(target=self.worker, args=(name,), daemon=True).start()

    def worker(self, name):
        """Фондық ағын: тек деректер алады, виджеттерге тиіспейді."""
        try:
            city = find_city(name)
            forecast = get_forecast(city["lat"], city["lon"])
            self.root.after(0, self.show, city, forecast)
        except CityNotFound as e:
            self.root.after(0, self.show_error, str(e))
        except requests.exceptions.ConnectionError:
            self.root.after(0, self.show_error, "Интернетке қосылу мүмкін емес")
        except requests.exceptions.Timeout:
            self.root.after(0, self.show_error, "Сервер жауап бермеді")
        except requests.exceptions.RequestException:
            self.root.after(0, self.show_error, "Сервер қатесі, кейінірек көріңіз")

    def show(self, city, forecast):
        cur = forecast["current"]
        self.city_label.config(text=f"{city['name']}, {city['country']}")
        self.temp_label.config(text=f"{cur['temp']:+.0f} °C")
        self.desc_label.config(text=describe(cur["code"]))
        self.details_label.config(text=f"Сезіледі: {cur['feels']:+.0f} °C · "
                                       f"Ылғал: {cur['humidity']}% · Жел: {cur['wind']:.0f} км/сағ")
        self.tree.delete(*self.tree.get_children())
        for day in forecast["daily"]:
            self.tree.insert("", "end", values=(kz_date(day["date"]), f"{day['min']:+.0f}°",
                                                f"{day['max']:+.0f}°", describe(day["code"])))
        self.status.config(text=f"Дереккөз: Open-Meteo.com · Жаңартылды {datetime.now():%H:%M}")
        self.search_btn.config(state="normal")
        self.settings["last_city"] = city["name"]
        save_settings(self.settings)

    def show_error(self, text):
        self.search_btn.config(state="normal")
        self.status.config(text="Дереккөз: Open-Meteo.com")
        messagebox.showerror("Қате", text)


if __name__ == "__main__":
    root = tk.Tk()
    WeatherApp(root)
    root.mainloop()
```

**8-тапсырма** (`show` әдісіне қосу)
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

**9-тапсырма** (график салу функциясы)
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

**Өзіндік жұмыс №5, 1-нұсқа**

1. а) схема `https`, домен `api.open-meteo.com`, жол `/v1/forecast`, параметрлер `latitude=43.25`, `longitude=76.95`; ә) 404 — клиент қатесі (ресурс жоқ), 500 — сервердің ішкі қатесі; б) GET — деректер алу (параметрлер URL-де), POST — деректер жіберу (дененің ішінде).
```python
# 2
import requests

try:
    r = requests.get("https://api.open-meteo.com/v1/forecast",
                     params={"latitude": 51.17, "longitude": 71.45,
                             "current": "temperature_2m,wind_speed_10m"}, timeout=10)
    r.raise_for_status()
    c = r.json()["current"]
    print(f"Астана: {c['temperature_2m']} °C, жел {c['wind_speed_10m']} км/сағ")
except requests.exceptions.RequestException as e:
    print("Қате:", e)
```
```python
# 3
data = {"daily": {"time": ["2027-02-23", "2027-02-24", "2027-02-25"],
                  "temperature_2m_max": [0.4, -1.2, 2.7], "temperature_2m_min": [-6.1, -8.3, -4.0]}}
d = data["daily"]
avgs = {t: (mx + mn) / 2 for t, mx, mn in zip(d["time"], d["temperature_2m_max"], d["temperature_2m_min"])}
for t, a in avgs.items():
    print(t, f"{a:.1f}")
print("Ең жылы күн:", max(avgs, key=avgs.get))
```
```python
# 4
from urllib.parse import urljoin
from bs4 import BeautifulSoup

html = """<div class="news"><h2><a href="/n/1">Олимпиада нәтижелері</a></h2><span class="date">20.02.2027</span></div>
<div class="news"><h2><a href="/n/2">Жаңа үйірме ашылды</a></h2><span class="date">21.02.2027</span></div>
<div class="ad"><h2><a href="/ad">Жарнама</a></h2></div>"""
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
6. Жоқ, `/private/` бөліміне тыйым салынған; сұраныстар арасында кемінде 4 секунд.

**Өзіндік жұмыс №5, 2-нұсқа**

1. а) DNS домен атауын IP-мекенжайға айналдырады; ә) 401 — авторизация жоқ (кім екеніңіз белгісіз), 403 — танылдыңыз, бірақ рұқсат жоқ; б) бастапқы жол (әдіс, жол, нұсқа), тақырыптар, бос жол, дене.
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
    print("Қате:", e)
```
```python
# 3
hourly = {"time": ["00:00", "01:00", "02:00", "03:00"], "temperature_2m": [-1.5, None, 0.5, 1.2]}
pairs = [(t, v) for t, v in zip(hourly["time"], hourly["temperature_2m"]) if v is not None]
warm = sum(1 for _, v in pairs if v > 0)
coldest = min(pairs, key=lambda p: p[1])
print("0 °C-тан жоғары сағаттар:", warm)
print("Ең суық сағат:", coldest[0], coldest[1])
```
```python
# 4
from bs4 import BeautifulSoup

html = """<table id="weather"><tr><th>Қала</th><th>Темп.</th></tr>
<tr><td>Астана</td><td>-14</td></tr><tr><td>Алматы</td><td>-2</td></tr><tr><td>Ақтау</td><td>4</td></tr></table>"""
soup = BeautifulSoup(html, "html.parser")
temps = {}
for tr in soup.find("table", id="weather").find_all("tr")[1:]:
    city, t = [td.get_text(strip=True) for td in tr.find_all("td")]
    temps[city] = int(t)
print(temps)
print("Ең суық:", min(temps, key=temps.get))
```
5. 1) Кілтті сервисте дереу жою/ауыстыру; 2) жаңа кілтті `.env`-ке жазу, `.env`-ті `.gitignore`-ға қосу, `git rm --cached .env`; 3) сервистегі пайдалану тарихын тексеру; 4) қажет болса репозиторий тарихын тазалау немесе жеке ету.
6. robots.txt-ті сақтайды, сұраныстар арасында кідіреді, өзін `User-Agent` арқылы таныстырады, кэш қолданады / жеке деректерді жинамайды.
```python
import requests
r = requests.get("https://books.toscrape.com", headers={"User-Agent": "PythonClubBot/1.0"}, timeout=10)
```

</details>
