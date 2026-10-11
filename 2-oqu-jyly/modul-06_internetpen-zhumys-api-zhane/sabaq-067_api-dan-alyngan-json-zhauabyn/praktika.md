# Сабақ 67. Практикалық тапсырмалар: API-дан алынған JSON-ды өңдеу

> Әр тапсырманы алдымен **сақталған JSON үлгісімен** (интернетсіз), содан кейін нақты API-мен орындаңыз. Кодты `fetch()`, `parse()`, `show()` функцияларына бөліңіз.

## Базалық деңгей

### 1-тапсырма. Жол картасы
Төмендегі JSON үшін дәптерге жол картасын сызып, Python-да мына мәндерді алыңыз: қала атауы, елі, 2-ші станцияның атауы, барлық станциялар саны, соңғы өлшеудің уақыты.

```python
weather_station = {
    "city": {"name": "Алматы", "country": {"code": "KZ", "name": "Қазақстан"}},
    "stations": [
        {"id": 1, "name": "Медеу", "measurements": [{"time": "08:00", "t": -7.5}, {"time": "14:00", "t": -2.1}]},
        {"id": 2, "name": "Орталық", "measurements": [{"time": "08:00", "t": -4.0}, {"time": "14:00", "t": 1.3}]},
        {"id": 3, "name": "Әуежай", "measurements": [{"time": "08:00", "t": -5.2}]},
    ],
}
```

```text
Қала: Алматы (Қазақстан)
2-станция: Орталық
Станциялар саны: 3
Соңғы өлшеу (Әуежай): 08:00
```

### 2-тапсырма. Ең жылы станция
1-тапсырмадағы деректер бойынша әр станцияның орташа температурасын есептеп, ең жылы станцияны табыңыз.

```text
Медеу: -4.8 °C
Орталық: -1.4 °C
Әуежай: -5.2 °C
Ең жылы: Орталық
```

### 3-тапсырма. Сағаттық болжам
Open-Meteo-дан `hourly=temperature_2m`, `forecast_days=1`, `timezone=auto` параметрлерімен Алматының сағаттық температурасын алыңыз. `zip()` арқылы «уақыт — температура» жұптарын жасап, тек әр 3 сағатты көрсетіңіз және күннің ең жылы/ең суық сағатын табыңыз.

```text
00:00  -6.3 °C
03:00  -7.1 °C
...
21:00  -3.9 °C
Ең жылы: 14:00 (+0.8 °C)
Ең суық: 06:00 (-7.6 °C)
```

### 4-тапсырма. Валюта топ-5
`https://open.er-api.com/v6/latest/KZT` жауабынан 1 теңгеге ең «қымбат» 5 валютаны (яғни 1 бірлігі ең көп теңге тұратын) тауып, теңгедегі бағасымен кесте түрінде шығарыңыз. Жаңарту уақытын `time_last_update_unix` бойынша `ДД.АА.ГГГГ СС:ММ` форматында көрсетіңіз.

### 5-тапсырма. Пайдаланушылар каталогы
`https://jsonplaceholder.typicode.com/users` жауабынан туралған кесте жасаңыз: ID, аты, қаласы, email домені (`@` белгісінен кейінгі бөлігі). Кестені аты бойынша алфавит ретімен сұрыптаңыз.

```text
ID  Аты                       Қала             Домен
 5  Chelsey Dietrich          Roscoeview       annie.ca
10  Clementina DuBuque        Lebsackbury      rosamond.me
...
```

## Кеңейтілген деңгей

### 6-тапсырма. Қазақша апталық болжам
7 күндік болжамды (`daily=temperature_2m_max,temperature_2m_min,precipitation_sum,weather_code`) алып, әр күнді «11 ақпан, бейсенбі» форматында шығарыңыз. Соңында қорытынды: апта бойы орташа максимум, жауын-шашынды күндер саны, ең жылы күн. Мәні `None` болса, оны есептеуге қоспаңыз.

### 7-тапсырма. Tkinter-де болжам
6-тапсырманың нәтижесін Tkinter терезесінде `ttk.Treeview` арқылы көрсетіңіз. Жоғарыда — қала атауы мен қазіргі температура (үлкен қаріппен), төменде — «Дереккөз: Open-Meteo.com» жазуы.

### 8-тапсырма. JSON → CSV
3-тапсырмадағы сағаттық деректерді `hourly.csv` файлына (`time;temperature`) сақтаңыз. Бұл файл VII модульде Pandas-пен талдауға жарайды!

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Әмбебап JSON зерттеушісі
`explore(obj, prefix="")` рекурсивті функциясы кез келген JSON құрылымының барлық «жолдарын» және мән типтерін шығарсын. Ұзын тізімдердің тек бірінші элементін көрсетіп, ұзындығын жазсын.

```text
latitude: float
current.time: str
current.temperature_2m: float
daily.time: list[3] → str
daily.temperature_2m_max: list[3] → float
```

### 10-тапсырма. Мәтіндік температура графигі
Сағаттық температураны терминалда тік «графикпен» көрсетіңіз: әр сағат — бір жол, нөлден солға (теріс) немесе оңға (оң) қарай `█` жолағы.

```text
00:00  -6.3 ██████│
06:00  -7.6 ███████│
12:00  +0.5       │
15:00  +0.8       │█
```

## Үй тапсырмасы

1. 66-сабақтағы үй тапсырмасында тапқан ашық API жауабына 9-тапсырмадағы `explore()` функциясын қолданып, жол картасын жазыңыз.
2. Сол API деректерін `fetch → parse → show` үлгісімен әдемі кесте етіп шығарыңыз.
3. Келесі сабаққа: GitHub-тағы бір жобаның `.gitignore` файлын ашып, ішінде не жазылғанын қарап келіңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–2-тапсырмалар**
```python
weather_station = {
    "city": {"name": "Алматы", "country": {"code": "KZ", "name": "Қазақстан"}},
    "stations": [
        {"id": 1, "name": "Медеу", "measurements": [{"time": "08:00", "t": -7.5}, {"time": "14:00", "t": -2.1}]},
        {"id": 2, "name": "Орталық", "measurements": [{"time": "08:00", "t": -4.0}, {"time": "14:00", "t": 1.3}]},
        {"id": 3, "name": "Әуежай", "measurements": [{"time": "08:00", "t": -5.2}]},
    ],
}
ws = weather_station
print(f"Қала: {ws['city']['name']} ({ws['city']['country']['name']})")
print("2-станция:", ws["stations"][1]["name"])
print("Станциялар саны:", len(ws["stations"]))
last = ws["stations"][-1]
print(f"Соңғы өлшеу ({last['name']}): {last['measurements'][-1]['time']}")

averages = {}
for st in ws["stations"]:
    temps = [m["t"] for m in st["measurements"]]
    averages[st["name"]] = sum(temps) / len(temps)
    print(f"{st['name']}: {averages[st['name']]:.1f} °C")
print("Ең жылы:", max(averages, key=averages.get))
```

**3-тапсырма**
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
    print(f"Ең жылы: {warm[0]} ({warm[1]:+.1f} °C)")
    print(f"Ең суық: {cold[0]} ({cold[1]:+.1f} °C)")

show(parse(fetch()))
```

**4-тапсырма**
```python
from datetime import datetime
import requests

data = requests.get("https://open.er-api.com/v6/latest/KZT", timeout=10).json()
in_kzt = {code: 1 / rate for code, rate in data["rates"].items() if rate}
top = sorted(in_kzt.items(), key=lambda kv: kv[1], reverse=True)[:5]
updated = datetime.fromtimestamp(data["time_last_update_unix"])
print("Жаңартылған:", updated.strftime("%d.%m.%Y %H:%M"))
print(f"{'Валюта':<8}{'Теңгемен':>12}")
for code, price in top:
    print(f"{code:<8}{price:>12,.2f}")
```

**5-тапсырма**
```python
import requests

users = requests.get("https://jsonplaceholder.typicode.com/users", timeout=10).json()
print(f"{'ID':>2}  {'Аты':<25} {'Қала':<16} Домен")
for u in sorted(users, key=lambda u: u["name"]):
    domain = u["email"].split("@")[1]
    print(f"{u['id']:>2}  {u['name']:<25} {u['address']['city']:<16} {domain}")
```

**6-тапсырма**
```python
from datetime import date
import requests

MONTHS = ["қаңтар", "ақпан", "наурыз", "сәуір", "мамыр", "маусым",
          "шілде", "тамыз", "қыркүйек", "қазан", "қараша", "желтоқсан"]
WEEKDAYS = ["дүйсенбі", "сейсенбі", "сәрсенбі", "бейсенбі", "жұма", "сенбі", "жексенбі"]

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
    print(f"{kz_date(d['date']):<24} {d['min']:+5.1f}…{d['max']:+5.1f} °C  жауын: {d['rain']} мм")
maxes = [d["max"] for d in days if d["max"] is not None]
rainy = sum(1 for d in days if (d["rain"] or 0) > 0)
warmest = max((d for d in days if d["max"] is not None), key=lambda d: d["max"])
print(f"Орташа максимум: {sum(maxes) / len(maxes):.1f} °C")
print("Жауын-шашынды күндер:", rainy)
print("Ең жылы күн:", kz_date(warmest["date"]))
```

**7-тапсырма** (6-тапсырманың `fetch_week`, `parse_week`, `kz_date` функцияларын импорттаймыз деп есептейміз, файл атауы `week.py`)
```python
import tkinter as tk
from tkinter import ttk
# from week import fetch_week, parse_week, kz_date

def show_window(city, days):
    root = tk.Tk()
    root.title(f"{city}: апталық болжам")
    tk.Label(root, text=city, font=("Arial", 22, "bold")).pack(pady=(10, 0))
    if days:
        tk.Label(root, text=f"Бүгін: {days[0]['min']:+.0f}…{days[0]['max']:+.0f} °C",
                 font=("Arial", 14)).pack()
    cols = ("date", "min", "max", "rain")
    tree = ttk.Treeview(root, columns=cols, show="headings", height=7)
    for c, title, w in zip(cols, ("Күні", "Мин", "Макс", "Жауын, мм"), (200, 70, 70, 90)):
        tree.heading(c, text=title)
        tree.column(c, width=w, anchor="center")
    for d in days:
        tree.insert("", "end", values=(kz_date(d["date"]), f"{d['min']:+.1f}", f"{d['max']:+.1f}", d["rain"]))
    tree.pack(padx=10, pady=10)
    tk.Label(root, text="Дереккөз: Open-Meteo.com", fg="gray").pack(pady=(0, 8))
    root.mainloop()

# show_window("Алматы", parse_week(fetch_week()))
```

**8-тапсырма**
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
print("hourly.csv сақталды")
```

**9-тапсырма**
```python
def explore(obj, prefix=""):
    if isinstance(obj, dict):
        for key, value in obj.items():
            explore(value, f"{prefix}.{key}" if prefix else key)
    elif isinstance(obj, list):
        inner = type(obj[0]).__name__ if obj else "бос"
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

**10-тапсырма**
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
