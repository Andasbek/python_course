# Сабақ 66. Практикалық тапсырмалар: API ұғымы, ауа райы және валюта бағамы

> Қолданылатын ашық API-лар (кілт қажет емес):
> - ауа райы: `https://api.open-meteo.com/v1/forecast`
> - геокодтау: `https://geocoding-api.open-meteo.com/v1/search`
> - валюта: `https://open.er-api.com/v6/latest/<валюта>`

## Базалық деңгей

### 1-тапсырма. Құжаттаманы зерттеу (дәптерде + браузерде)
`https://open-meteo.com/en/docs` бетін ашып, жауап беріңіз:
1. Base URL және эндпоинт қандай?
2. Қандай параметрлер міндетті?
3. `temperature_2m` пен `apparent_temperature` айырмашылығы неде?
4. Конструктор арқылы Түркістан қаласы үшін 3 күндік максималды/минималды температура URL-ін құрып, браузерде ашыңыз. JSON-ның негізгі кілттерін жазыңыз.

### 2-тапсырма. Менің қаламның ауа райы
Өз қалаңыздың координаталарымен қазіргі температураны, ылғалдылықты, жел жылдамдығын және ауа райы сипаттамасын (WMO кодтар сөздігі арқылы) шығарыңыз.

```text
Алматы, 14:15
Температура: -1.8 °C
Ылғалдылық: 71 %
Жел: 6.4 km/h
Бұлтты ☁️
```

### 3-тапсырма. Кез келген қала
Пайдаланушы қала атауын енгізеді → геокодтау API координаталарды табады → ауа райы API температураны береді. Қала табылмаса — хабар.

```text
Қала: Ақтау
Ақтау (Казахстан): 43.65, 51.17
Қазір: +4.2 °C, Ішінара бұлтты ⛅
```
```text
Қала: Абвгдеж
«Абвгдеж» қаласы табылмады
```

### 4-тапсырма. Валюта бағамы
`https://open.er-api.com/v6/latest/KZT` арқылы теңге негізіндегі бағамдарды алып, 1 USD, 1 EUR, 1 RUB, 1 CNY неше теңге тұратынын есептеңіз (кеңес: 1 USD = 1 / rates["USD"] теңге).

```text
Бағам жаңартылған: Tue, 09 Feb 2027 00:02:31 +0000
1 USD = 512.40 ₸
1 EUR = 556.96 ₸
1 RUB = 5.62 ₸
1 CNY = 71.36 ₸
```

### 5-тапсырма. Айырбастағыш
Пайдаланушы сома, бастапқы және мақсатты валюта кодын енгізеді. Бағдарлама нәтижені есептейді. Валюта коды қате болса — хабар.

```text
Сома: 250
Қай валютадан: EUR
Қай валютаға: KZT
250.00 EUR = 139 239.13 KZT
```

## Кеңейтілген деңгей

### 6-тапсырма. 3 күндік болжам
`daily=temperature_2m_max,temperature_2m_min,weather_code`, `timezone=auto`, `forecast_days=3` параметрлерімен қала болжамын шығарыңыз. Күндерді қазақша апта күндерімен көрсетіңіз (`datetime.date.fromisoformat(...).weekday()`).

```text
Сейсенбі   09.02:  -6°…  0°  Бұлтты ☁️
Сәрсенбі   10.02:  -8°… -2°  Қар 🌨
Бейсенбі   11.02: -10°… -3°  Ашық аспан ☀️
```

### 7-тапсырма. Қалалар рейтингі
Қазақстанның 6 қаласы (Алматы, Астана, Шымкент, Ақтөбе, Қарағанды, Өскемен) бойынша қазіргі температураны алып, ең жылыдан ең суыққа қарай сұрыптап шығарыңыз. Open-Meteo бір сұраныста бірнеше координата қабылдайды: `latitude=43.25,51.17,...` — осы мүмкіндікті қолданып, 6 сұраныс орнына 1 сұраныс жіберіңіз (жауап — тізім).

### 8-тапсырма. Бағам кэші
Бағамдарды `rates_cache.json` файлына уақытымен бірге сақтаңыз. Бағдарлама іске қосылғанда кэш 12 сағаттан жас болса — файлдан оқысын, ескі болса — API-дан қайта алсын. Экранға дереккөзін шығарыңыз: «кэштен» немесе «API-дан».

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. «Не кием?» кеңесшісі
Ауа райы деректері (температура, `apparent_temperature`, жауын-шашын, жел) бойынша оқушыға киім кеңесін беретін бағдарлама жасаңыз: «Күртеше, бөрік және қолғап ки. Қолшатыр қажет емес». Ережелерді өзіңіз ойлап табыңыз (кемінде 6 ереже).

### 10-тапсырма. Саяхат бюджеті
Пайдаланушы елді (`USD`, `TRY`, `AED`, `GEL` т.б. валютасын) және күндік шығынды сол валютамен енгізеді. Бағдарлама 7 күндік саяхаттың теңгедегі бағасын есептейді және «бюджет: 500 000 ₸» шегімен салыстырады. Бірнеше ел бойынша салыстырмалы кесте шығарыңыз.

## Үй тапсырмасы

1. Интернеттен тағы бір кілтсіз ашық API табыңыз (кеңес: `https://github.com/public-apis/public-apis`, «Auth: No» бағаны). Оның эндпоинтін, параметрлерін жазып, Python-да бір сұраныс жіберіңіз.
2. 3-тапсырманы функцияларға бөліңіз: `find_city()`, `get_weather()`, `show()`.
3. Ойланыңыз: API-дан келген JSON-да қандай деректер бізге «артық» болды?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**Ортақ модуль — `weather_api.py`** (2, 3, 6, 9-тапсырмалар осыны қолданады)
```python
import requests

FORECAST_URL = "https://api.open-meteo.com/v1/forecast"
GEO_URL = "https://geocoding-api.open-meteo.com/v1/search"

WEATHER_CODES = {
    0: "Ашық аспан ☀️", 1: "Негізінен ашық 🌤", 2: "Ішінара бұлтты ⛅", 3: "Бұлтты ☁️",
    45: "Тұман 🌫", 48: "Қырау тұманы 🌫", 51: "Әлсіз сіркіреме", 53: "Сіркіреме",
    55: "Күшті сіркіреме", 61: "Әлсіз жаңбыр 🌦", 63: "Жаңбыр 🌧", 65: "Нөсер 🌧",
    71: "Әлсіз қар 🌨", 73: "Қар 🌨", 75: "Қалың қар ❄️", 80: "Аздаған жауын",
    81: "Жауын", 82: "Қатты жауын", 95: "Найзағай ⛈",
}


def describe(code):
    return WEATHER_CODES.get(code, f"Белгісіз ({code})")


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

**2-тапсырма**
```python
from weather_api import current_weather, describe

cur, units = current_weather(43.25, 76.95)
print("Алматы,", cur["time"][-5:])
print("Температура:", cur["temperature_2m"], units["temperature_2m"])
print("Ылғалдылық:", cur["relative_humidity_2m"], units["relative_humidity_2m"])
print("Жел:", cur["wind_speed_10m"], units["wind_speed_10m"])
print(describe(cur["weather_code"]))
```

**3-тапсырма**
```python
import requests
from weather_api import find_city, current_weather, describe

name = input("Қала: ").strip()
try:
    city = find_city(name)
    if city is None:
        print(f"«{name}» қаласы табылмады")
    else:
        print(f"{city['name']} ({city['country']}): {city['lat']:.2f}, {city['lon']:.2f}")
        cur, _ = current_weather(city["lat"], city["lon"])
        print(f"Қазір: {cur['temperature_2m']:+.1f} °C, {describe(cur['weather_code'])}")
except requests.exceptions.RequestException as e:
    print("Желі қатесі:", e)
```

**4-тапсырма**
```python
import requests

data = requests.get("https://open.er-api.com/v6/latest/KZT", timeout=10).json()
if data["result"] == "success":
    print("Бағам жаңартылған:", data["time_last_update_utc"])
    for code in ("USD", "EUR", "RUB", "CNY"):
        print(f"1 {code} = {1 / data['rates'][code]:.2f} ₸")
```

**5-тапсырма**
```python
import requests

rates = requests.get("https://open.er-api.com/v6/latest/USD", timeout=10).json()["rates"]
amount = float(input("Сома: ").replace(",", "."))
src = input("Қай валютадан: ").strip().upper()
dst = input("Қай валютаға: ").strip().upper()
if src not in rates or dst not in rates:
    print("Белгісіз валюта коды")
else:
    result = amount / rates[src] * rates[dst]
    print(f"{amount:.2f} {src} = {result:,.2f} {dst}".replace(",", " "))
```

**6-тапсырма**
```python
from datetime import date
import requests
from weather_api import describe, FORECAST_URL

DAYS = ["Дүйсенбі", "Сейсенбі", "Сәрсенбі", "Бейсенбі", "Жұма", "Сенбі", "Жексенбі"]
params = {"latitude": 43.25, "longitude": 76.95, "timezone": "auto", "forecast_days": 3,
          "daily": "temperature_2m_max,temperature_2m_min,weather_code"}
d = requests.get(FORECAST_URL, params=params, timeout=10).json()["daily"]
for day, tmax, tmin, code in zip(d["time"], d["temperature_2m_max"],
                                 d["temperature_2m_min"], d["weather_code"]):
    dt = date.fromisoformat(day)
    print(f"{DAYS[dt.weekday()]:10} {dt:%d.%m}: {tmin:3.0f}°…{tmax:3.0f}°  {describe(code)}")
```

**7-тапсырма**
```python
import requests

cities = {"Алматы": (43.25, 76.95), "Астана": (51.17, 71.45), "Шымкент": (42.32, 69.59),
          "Ақтөбе": (50.28, 57.17), "Қарағанды": (49.80, 73.10), "Өскемен": (49.95, 82.61)}
lats = ",".join(str(v[0]) for v in cities.values())
lons = ",".join(str(v[1]) for v in cities.values())
data = requests.get("https://api.open-meteo.com/v1/forecast",
                    params={"latitude": lats, "longitude": lons, "current": "temperature_2m"},
                    timeout=10).json()          # бірнеше нүкте болса — тізім
temps = [(name, item["current"]["temperature_2m"]) for name, item in zip(cities, data)]
for i, (name, t) in enumerate(sorted(temps, key=lambda x: x[1], reverse=True), 1):
    print(f"{i}. {name:10} {t:+.1f} °C")
```

**8-тапсырма**
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
            return cache["rates"], "кэштен"
    data = requests.get("https://open.er-api.com/v6/latest/USD", timeout=10).json()
    CACHE.write_text(json.dumps({"saved_at": time.time(), "rates": data["rates"]}), encoding="utf-8")
    return data["rates"], "API-дан"

rates, source = get_rates()
print(f"1 USD = {rates['KZT']:.2f} KZT ({source})")
```

**9-тапсырма** (ережелер үлгісі)
```python
import requests

params = {"latitude": 43.25, "longitude": 76.95,
          "current": "temperature_2m,apparent_temperature,precipitation,wind_speed_10m"}
c = requests.get("https://api.open-meteo.com/v1/forecast", params=params, timeout=10).json()["current"]
feels, rain, wind = c["apparent_temperature"], c["precipitation"], c["wind_speed_10m"]
advice = []
if feels < -15:
    advice.append("Қалың пуховик, бөрік, қолғап, шарф ки")
elif feels < 0:
    advice.append("Қысқы күртеше мен бөрік ки")
elif feels < 10:
    advice.append("Жеңіл күртеше немесе жемпір ки")
elif feels < 20:
    advice.append("Жейде мен жеңіл кофта жеткілікті")
else:
    advice.append("Футболка ки, су алып жүр")
if rain > 0:
    advice.append("Қолшатыр ал")
if wind > 30:
    advice.append("Жел қатты — капюшон пайдалы")
print(f"Сезілетін температура: {feels} °C")
print(". ".join(advice) + ".")
```

**10-тапсырма**
```python
import requests

rates = requests.get("https://open.er-api.com/v6/latest/KZT", timeout=10).json()["rates"]
BUDGET = 500_000
trips = {"Түркия": ("TRY", 2500), "БАӘ": ("AED", 400), "Грузия": ("GEL", 200), "АҚШ": ("USD", 150)}
print(f"{'Ел':8} {'7 күн, ₸':>12}  Бюджет")
for country, (cur, per_day) in trips.items():
    total_kzt = per_day * 7 / rates[cur]
    status = "жетеді" if total_kzt <= BUDGET else "жетпейді"
    print(f"{country:8} {total_kzt:>12,.0f}  {status}")
```

</details>
