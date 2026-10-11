# Сабақ 67. API-дан алынған JSON жауабын өңдеу және пайдаланушыға көрсету.

## Тақырыптар

1. JSON → Python сәйкестігі
2. Құрылымды зерттеу: «жол картасы»
3. Қауіпсіз қол жеткізу
4. Параллель массивтер және `zip()`
5. Деректерді түрлендіру: сүзу, сұрыптау, агрегаттау
6. Күн мен уақытты өңдеу
7. Пайдаланушыға әдемі көрсету
8. Өңдеу «конвейері»
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. JSON → Python сәйкестігі

| JSON | Python | Мысал |
|---|---|---|
| object `{}` | `dict` | `{"name": "Алматы"}` |
| array `[]` | `list` | `[1, 2, 3]` |
| string | `str` | `"Қар"` |
| number | `int` / `float` | `-3.5` |
| `true` / `false` | `True` / `False` | |
| `null` | `None` | |

`r.json()` бұл түрлендіруді автоматты жасайды. Әрі қарай — тек сөздіктер мен тізімдермен жұмыс, оны жақсы білеміз!

## 2. Құрылымды зерттеу: «жол картасы»

API жауабы ұзын әрі күрделі болуы мүмкін. Алдымен оның **құрылымын** түсіну керек. Мысал үшін Open-Meteo жауабының қысқартылған нұсқасы (интернет болмаса да жұмыс істейтін болу үшін кодқа салдық):

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

# 1-қадам: жоғарғы деңгейдегі кілттер
print(list(data.keys()))
# 2-қадам: әдемі басып шығару
print(json.dumps(data["current"], ensure_ascii=False, indent=2))
```

Нәтижесі:
```text
['latitude', 'longitude', 'timezone', 'current_units', 'current', 'daily']
{
  "time": "2027-02-11T14:00",
  "temperature_2m": -1.8,
  "wind_speed_10m": 6.4,
  "weather_code": 3
}
```

**Жол картасы** — қажетті мәнге жету жолы:

```text
data ─┬─ "current" ─┬─ "temperature_2m" → -1.8
      │             └─ "weather_code"   → 3
      └─ "daily" ───┬─ "time"               → ["2027-02-11", ...]   (тізім!)
                    └─ "temperature_2m_max" → [0.4, -1.2, 2.7]
```

```python
print(data["current"]["temperature_2m"])        # сөздік → сөздік → мән
print(data["daily"]["time"][0])                 # сөздік → сөздік → тізім → элемент
print(data["daily"]["temperature_2m_max"][-1])  # соңғы күннің максимумы
```

Нәтижесі:
```text
-1.8
2027-02-11
2.7
```

**Кеңес:** браузерде (Firefox, Chrome) JSON жауапты ашсаңыз, ол ағаш түрінде әдемі көрсетіледі — құрылымды зерттеуге өте ыңғайлы.

## 3. Қауіпсіз қол жеткізу

API жауабында кейбір өрістер **болмауы** немесе `null` болуы мүмкін. Тікелей `data["key"]` — `KeyError` қаупі.

```python
city = {"name": "Түркістан", "latitude": 43.3, "longitude": 68.25, "population": None}

print(city.get("country", "белгісіз"))          # кілт жоқ → әдепкі мән
pop = city.get("population")
print(f"{pop:,}" if pop is not None else "Халық саны көрсетілмеген")

# Кірістірілген сөздіктер үшін — тізбекті get
resp = {"results": [{"name": "Түркістан"}]}
first = (resp.get("results") or [{}])[0]
print(first.get("name", "—"))
```

Нәтижесі:
```text
белгісіз
Халық саны көрсетілмеген
Түркістан
```

Терең құрылымдар үшін көмекші функция:

```python
def dig(obj, *path, default=None):
    """dig(data, "daily", "time", 0) — жол бойынша қауіпсіз жүру."""
    for key in path:
        try:
            obj = obj[key]
        except (KeyError, IndexError, TypeError):
            return default
    return obj

sample = {"daily": {"time": ["2027-02-11"]}}
print(dig(sample, "daily", "time", 0))
print(dig(sample, "daily", "rain", 0, default="жоқ"))
```

Нәтижесі:
```text
2027-02-11
жоқ
```

## 4. Параллель массивтер және `zip()`

Open-Meteo күндік деректерді «бағандар» түрінде береді: әр шама — жеке тізім, ал бірдей индекстегі элементтер бір күнге тиесілі. Оларды «жолдарға» біріктіру үшін `zip()`:

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

Нәтижесі:
```text
{'date': '2027-02-12', 'max': -1.2, 'min': -8.3, 'code': 73}
```

Енді `days` — бізге ыңғайлы «жазбалар тізімі» (SQLite-тағы жолдар сияқты).

## 5. Деректерді түрлендіру: сүзу, сұрыптау, агрегаттау

```python
days = [
    {"date": "2027-02-11", "max": 0.4, "min": -6.1, "code": 3},
    {"date": "2027-02-12", "max": -1.2, "min": -8.3, "code": 73},
    {"date": "2027-02-13", "max": 2.7, "min": -4.0, "code": 0},
]

# Сүзу: аязды күндер (максимум 0-ден төмен)
frosty = [d["date"] for d in days if d["max"] < 0]
# Сұрыптау: ең жылы күн бірінші
warm_first = sorted(days, key=lambda d: d["max"], reverse=True)
# Агрегаттау: орташа, ең төмен
avg_max = sum(d["max"] for d in days) / len(days)
coldest = min(days, key=lambda d: d["min"])

print("Аязды күндер:", frosty)
print("Ең жылы күн:", warm_first[0]["date"])
print(f"Орташа максимум: {avg_max:.1f} °C")
print("Ең суық түн:", coldest["date"], coldest["min"])
```

Нәтижесі:
```text
Аязды күндер: ['2027-02-12']
Ең жылы күн: 2027-02-13
Орташа максимум: 0.6 °C
Ең суық түн: 2027-02-12 -8.3
```

## 6. Күн мен уақытты өңдеу

API-лар уақытты **ISO 8601** форматында береді: `2027-02-11T14:00`. Оны `datetime` объектісіне айналдырып, қазақша форматтаймыз:

```python
from datetime import datetime

MONTHS = ["қаңтар", "ақпан", "наурыз", "сәуір", "мамыр", "маусым",
          "шілде", "тамыз", "қыркүйек", "қазан", "қараша", "желтоқсан"]
WEEKDAYS = ["дүйсенбі", "сейсенбі", "сәрсенбі", "бейсенбі", "жұма", "сенбі", "жексенбі"]

dt = datetime.fromisoformat("2027-02-11T14:00")
print(f"{dt.day} {MONTHS[dt.month - 1]}, {WEEKDAYS[dt.weekday()]}, {dt:%H:%M}")
```

Нәтижесі:
```text
11 ақпан, бейсенбі, 14:00
```

Валюта API-ы уақытты Unix форматында да береді (`time_last_update_unix`: 1970 жылдан бергі секунд саны):

```python
from datetime import datetime
print(datetime.fromtimestamp(1802217600).strftime("%d.%m.%Y %H:%M"))
```

## 7. Пайдаланушыға әдемі көрсету

### Консольдегі кесте

f-жолдардағы туралау: `<` — солға, `>` — оңға, `^` — ортаға, сан — ені.

```python
days = [("11.02", "бейсенбі", 0.4, -6.1, "Бұлтты"),
        ("12.02", "жұма", -1.2, -8.3, "Қар"),
        ("13.02", "сенбі", 2.7, -4.0, "Ашық")]

print(f"{'Күні':<6} {'Апта күні':<10} {'Макс':>6} {'Мин':>6}  Ауа райы")
print("-" * 44)
for date, wd, tmax, tmin, desc in days:
    print(f"{date:<6} {wd:<10} {tmax:>+6.1f} {tmin:>+6.1f}  {desc}")
```

Нәтижесі:
```text
Күні   Апта күні    Макс    Мин  Ауа райы
--------------------------------------------
11.02  бейсенбі     +0.4   -6.1  Бұлтты
12.02  жұма         -1.2   -8.3  Қар
13.02  сенбі        +2.7   -4.0  Ашық
```

### Мәтіндік «график»

```python
temps = {"11.02": 0.4, "12.02": -1.2, "13.02": 2.7, "14.02": 5.0}
for day, t in temps.items():
    bar = "█" * int(abs(t) * 2)
    print(f"{day} {t:+5.1f} {bar}")
```

### Tkinter терезесінде

```python
import tkinter as tk
from tkinter import ttk

days = [("11.02", 0.4, -6.1, "Бұлтты ☁️"), ("12.02", -1.2, -8.3, "Қар 🌨"),
        ("13.02", 2.7, -4.0, "Ашық ☀️")]

root = tk.Tk()
root.title("Алматы: 3 күндік болжам")
tk.Label(root, text="Алматы", font=("Arial", 20, "bold")).pack(pady=(10, 0))
tk.Label(root, text="Қазір: −1.8 °C, бұлтты", font=("Arial", 12)).pack()

tree = ttk.Treeview(root, columns=("d", "max", "min", "w"), show="headings", height=3)
for col, title in zip(("d", "max", "min", "w"), ("Күні", "Макс", "Мин", "Ауа райы")):
    tree.heading(col, text=title)
    tree.column(col, width=90, anchor="center")
for d in days:
    tree.insert("", "end", values=(d[0], f"{d[1]:+.1f}°", f"{d[2]:+.1f}°", d[3]))
tree.pack(padx=10, pady=10)
root.mainloop()
```

Осы тәсілмен толық «Ауа райы» қосымшасы құрылады: деректер қабаты JSON-ды өңдейді, интерфейс тек дайын нәтижені көрсетеді.

## 8. Өңдеу «конвейері»

Сапалы кодта API-мен жұмыс үш бөлек функцияға бөлінеді:

```text
fetch()  ──►  parse()  ──►  show()
желіден       JSON-нан       экранға
алу           қажеттіні      шығару
              таңдау
```

```python
def parse_daily(data):
    """API жауабынан күндер тізімін қайтарады."""
    d = data.get("daily", {})
    keys = ("time", "temperature_2m_max", "temperature_2m_min", "weather_code")
    return [dict(zip(("date", "max", "min", "code"), row)) for row in zip(*(d.get(k, []) for k in keys))]

sample = {"daily": {"time": ["2027-02-11"], "temperature_2m_max": [0.4],
                    "temperature_2m_min": [-6.1], "weather_code": [3]}}
print(parse_daily(sample))
```

Нәтижесі:
```text
[{'date': '2027-02-11', 'max': 0.4, 'min': -6.1, 'code': 3}]
```

**Пайдасы:** `parse()` функциясын интернетсіз, сақталған JSON үлгісімен тексеруге болады; интерфейсті ауыстырсақ (консоль → Tkinter), тек `show()` өзгереді.

## Жиі кездесетін қателер

**1. Тізімді сөздік деп қарау:**
```python
resp = {"results": [{"name": "Алматы"}]}
# Қате: resp["results"]["name"]  → TypeError: list indices must be integers
print(resp["results"][0]["name"])   # Дұрыс: алдымен индекс
```

**2. `KeyError` — өріс жоқ.** Міндетті емес өрістерге `.get()` қолданыңыз.

**3. `None`-мен арифметика:** кейбір сағаттарда мән `null` болуы мүмкін → `TypeError`. Есептеуден бұрын сүзіңіз: `[t for t in temps if t is not None]`.

**4. Күнді жол ретінде салыстыру** — `"2027-02-11" < "2027-02-9"` дұрыс жұмыс істемеуі мүмкін. `datetime.fromisoformat()` қолданыңыз.

**5. JSON-ды `print(data)` арқылы бір жолмен оқуға тырысу** — түсініксіз. `json.dumps(data, indent=2, ensure_ascii=False)` қолданыңыз.

**6. Әр көрсету үшін қайта сұраныс жіберу** — деректерді бір рет алып, айнымалыда сақтаңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Кірістірілген құрылым | Сөздік/тізім ішіндегі сөздік/тізім |
| Жол картасы | Қажетті мәнге жететін кілттер мен индекстер тізбегі |
| Параллель массивтер | Бірдей индекстегі элементтері бір жазбаға тиесілі тізімдер |
| `zip()` | Бірнеше тізімді элементтер бойынша біріктіру |
| Парсинг (parse) | Шикі деректерден қажетті ақпаратты бөліп алу |
| ISO 8601 | Күн/уақыттың халықаралық форматы: `ГГГГ-АА-ККTСС:ММ` |
| Unix уақыты | 1970-01-01-ден бергі секундтар саны |
| `json.dumps(indent=2)` | JSON-ды оқылатын түрде шығару |

## Ескерту

- `json.loads()` JSON-дағы `null`-ды `None`-ға, `true/false`-ты `True/False`-қа айналдырады; JSON объектісінің кілттері сан сияқты көрінсе де әрқашан жол (str) болады.
- `dict.get(key, default)` default-ты тек кілт жоқ болса қайтарады; кілт бар, бірақ мәні `None` болса, `None` қайтады — мұндайда `d.get("x") or default` қолданыңыз.
- `zip()` ең қысқа тізім біткенде қатесіз тоқтайды; Python 3.10+ нұсқасында `zip(a, b, strict=True)` ұзындықтар әртүрлі болса `ValueError` шығарады.
- Python 3.11-ге дейін `datetime.fromisoformat()` `Z` жұрнағын және ISO 8601-дің кейбір пішімдерін түсінбейтін (https://docs.python.org/3/library/datetime.html).
- Құрылымды қарау үшін: `json.dumps(data, indent=2, ensure_ascii=False)` немесе `pprint.pprint(data, depth=2)`.
