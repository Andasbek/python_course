# Сабақ 65. Практикалық тапсырмалар: requests кітапханасы

> Алдымен виртуалды орта жасап, `pip install requests` орындаңыз. Барлық сұранысқа `timeout` беріңіз!

## Базалық деңгей

### 1-тапсырма. Жауапты зерттеу
`https://httpbin.org/get` мекенжайына GET сұранысын жіберіп, мына ақпаратты шығарыңыз: күй коды, сипаттамасы (`reason`), `Content-Type`, жауап уақыты (миллисекундпен), дене ұзындығы.

```text
Күй: 200 OK
Түрі: application/json
Уақыт: 412 мс
Дене: 305 таңба
```

### 2-тапсырма. Пайдаланушы карточкасы
Пайдаланушыдан 1–10 аралығындағы ID сұрап, `https://jsonplaceholder.typicode.com/users/<id>` деректерін алыңыз. Аты, email, қаласы және компания атауын әдемі карточка түрінде шығарыңыз.

```text
ID (1-10): 3
┌──────────────────────────────┐
  Аты:      Clementine Bauch
  Email:    Nathan@yesenia.net
  Қала:     McKenziehaven
  Компания: Romaguera-Jacobson
└──────────────────────────────┘
```

### 3-тапсырма. Тапсырмалар тізімі
`https://jsonplaceholder.typicode.com/todos` бетінен `params={"userId": 1}` арқылы бірінші пайдаланушының тапсырмаларын алыңыз. Орындалғандарын ✓, орындалмағандарын ✗ белгісімен шығарып, соңында статистика беріңіз.

```text
✓ delectus aut autem
✗ quis ut nam facilis et officia qui
...
Орындалды: 11 / 20 (55%)
```

### 4-тапсырма. Қауіпсіз сұраныс
Теориядағы `safe_get_json()` функциясын жазып, оны 4 жағдайда тексеріңіз:
- `https://jsonplaceholder.typicode.com/posts/1` — сәтті;
- `https://jsonplaceholder.typicode.com/posts/0` — 404;
- `https://httpbin.org/delay/5` (`timeout=2`) — уақыт бітті;
- `https://joq-sait-12345.kz` — қосылу қатесі.

### 5-тапсырма. Сурет жүктеу
`https://picsum.photos/400/300` мекенжайынан кездейсоқ сурет жүктеп, `photo_1.jpg`, `photo_2.jpg`, `photo_3.jpg` атауларымен сақтаңыз (циклде, арасында `time.sleep(1)`). Әр файлдың өлшемін КБ-пен шығарыңыз.

## Кеңейтілген деңгей

### 6-тапсырма. Ауа райы — алғашқы қадам
Open-Meteo API-ынан (`https://api.open-meteo.com/v1/forecast`, параметрлер: `latitude`, `longitude`, `current_weather=true`) Алматы (43.25, 76.95), Астана (51.17, 71.45), Шымкент (42.32, 69.59) қалаларының қазіргі температурасы мен жел жылдамдығын алып, кесте түрінде шығарыңыз.

```text
Қала        Темп., °C   Жел, км/сағ
Алматы          -2.4          5.1
Астана         -14.8         18.0
Шымкент          3.1          7.6
```

### 7-тапсырма. Сайттардың «жүрек соғысы»
`sites.txt` файлындағы URL тізімін оқып, әрқайсысына сұраныс жіберіп, нәтижені `status.csv` файлына жазыңыз: URL, күй коды, уақыт (мс), күні мен уақыты. Қолжетімсіз сайт үшін код — `ERROR`.

### 8-тапсырма. Ең ұзын пост
`https://jsonplaceholder.typicode.com/posts` бетінен 100 постты алып, денесі ең ұзын постты және әр пайдаланушының (`userId`) пост санын шығарыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Прогресс-жолағы бар жүктеуші
`stream=True` және `iter_content` арқылы файлды жүктеп, `Content-Length` тақырыбы бойынша жүктелу пайызын бір жолда жаңартып отыратын функция жазыңыз (`print(..., end="\r")`).

```text
Жүктелуде: [##########----------] 50%
```

### 10-тапсырма. Қайталап көру (retry)
`get_with_retry(url, attempts=3)` функциясы сұраныс сәтсіз болса (қосылу қатесі немесе 5xx коды), 1, 2, 4 секунд күтіп (экспоненциалды кідіріс), қайта жіберсін. Барлық әрекет сәтсіз болса — `None`. `https://httpbin.org/status/503` арқылы тексеріңіз.

## Үй тапсырмасы

1. `requirements.txt` файлы бар жоба папкасын GitHub-қа жүктеңіз (`.venv` — `.gitignore`-да!).
2. `https://jsonplaceholder.typicode.com/albums` және `/photos` деректерінен бірінші альбомның алғашқы 5 фотосуретін (`thumbnailUrl`) жүктеңіз.
3. Ойланыңыз: сіз білетін қай қосымшалар сыртқы сервистен деректер алады? 3 мысал жазыңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import requests

r = requests.get("https://httpbin.org/get", timeout=10)
print(f"Күй: {r.status_code} {r.reason}")
print("Түрі:", r.headers.get("Content-Type"))
print(f"Уақыт: {r.elapsed.total_seconds() * 1000:.0f} мс")
print(f"Дене: {len(r.text)} таңба")
```

**2-тапсырма**
```python
import requests

uid = int(input("ID (1-10): "))
r = requests.get(f"https://jsonplaceholder.typicode.com/users/{uid}", timeout=10)
if r.ok:
    u = r.json()
    print("┌" + "─" * 30 + "┐")
    print(f"  Аты:      {u['name']}")
    print(f"  Email:    {u['email']}")
    print(f"  Қала:     {u['address']['city']}")
    print(f"  Компания: {u['company']['name']}")
    print("└" + "─" * 30 + "┘")
else:
    print("Пайдаланушы табылмады, код:", r.status_code)
```

**3-тапсырма**
```python
import requests

r = requests.get("https://jsonplaceholder.typicode.com/todos", params={"userId": 1}, timeout=10)
todos = r.json()
done = 0
for t in todos:
    mark = "✓" if t["completed"] else "✗"
    done += t["completed"]
    print(mark, t["title"])
print(f"Орындалды: {done} / {len(todos)} ({done / len(todos):.0%})")
```

**4-тапсырма**
```python
import requests

def safe_get_json(url, timeout=10):
    try:
        r = requests.get(url, timeout=timeout)
        r.raise_for_status()
        return r.json()
    except requests.exceptions.Timeout:
        print("Қате: уақыт бітті")
    except requests.exceptions.ConnectionError:
        print("Қате: қосылу мүмкін емес")
    except requests.exceptions.HTTPError as e:
        print("HTTP қатесі:", e.response.status_code)
    except requests.exceptions.JSONDecodeError:
        print("Қате: JSON емес")
    return None

print(safe_get_json("https://jsonplaceholder.typicode.com/posts/1"))
safe_get_json("https://jsonplaceholder.typicode.com/posts/0")
safe_get_json("https://httpbin.org/delay/5", timeout=2)
safe_get_json("https://joq-sait-12345.kz")
```

**5-тапсырма**
```python
import time
import requests

for i in range(1, 4):
    r = requests.get("https://picsum.photos/400/300", timeout=15)
    if r.ok:
        name = f"photo_{i}.jpg"
        with open(name, "wb") as f:
            f.write(r.content)
        print(f"{name}: {len(r.content) / 1024:.1f} КБ")
    time.sleep(1)
```

**6-тапсырма**
```python
import requests

cities = {"Алматы": (43.25, 76.95), "Астана": (51.17, 71.45), "Шымкент": (42.32, 69.59)}
print(f"{'Қала':10} {'Темп., °C':>10} {'Жел, км/сағ':>12}")
for name, (lat, lon) in cities.items():
    r = requests.get("https://api.open-meteo.com/v1/forecast",
                     params={"latitude": lat, "longitude": lon, "current_weather": "true"},
                     timeout=10)
    cw = r.json()["current_weather"]
    print(f"{name:10} {cw['temperature']:>10} {cw['windspeed']:>12}")
```

**7-тапсырма**
```python
import csv
from datetime import datetime
import requests

with open("sites.txt", encoding="utf-8") as f:
    urls = [line.strip() for line in f if line.strip()]

with open("status.csv", "w", newline="", encoding="utf-8-sig") as f:
    w = csv.writer(f)
    w.writerow(["url", "code", "ms", "checked_at"])
    for url in urls:
        now = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        try:
            r = requests.get(url, timeout=10)
            w.writerow([url, r.status_code, round(r.elapsed.total_seconds() * 1000), now])
        except requests.exceptions.RequestException:
            w.writerow([url, "ERROR", "", now])
print("status.csv дайын")
```

**8-тапсырма**
```python
from collections import Counter
import requests

posts = requests.get("https://jsonplaceholder.typicode.com/posts", timeout=10).json()
longest = max(posts, key=lambda p: len(p["body"]))
print("Ең ұзын пост:", longest["id"], longest["title"], len(longest["body"]), "таңба")
for uid, n in sorted(Counter(p["userId"] for p in posts).items()):
    print(f"Пайдаланушы {uid}: {n} пост")
```

**9-тапсырма**
```python
import requests

def download_with_progress(url, path):
    with requests.get(url, stream=True, timeout=30) as r:
        r.raise_for_status()
        total = int(r.headers.get("Content-Length", 0))
        done = 0
        with open(path, "wb") as f:
            for chunk in r.iter_content(chunk_size=8192):
                f.write(chunk)
                done += len(chunk)
                if total:
                    part = done * 20 // total
                    print(f"Жүктелуде: [{'#' * part}{'-' * (20 - part)}] {done * 100 // total}%", end="\r")
    print("\nДайын:", path)

download_with_progress("https://www.python.org/static/img/python-logo.png", "logo.png")
```

**10-тапсырма**
```python
import time
import requests

def get_with_retry(url, attempts=3):
    delay = 1
    for i in range(1, attempts + 1):
        try:
            r = requests.get(url, timeout=10)
            if r.status_code < 500:
                return r
            print(f"{i}-әрекет: сервер қатесі {r.status_code}")
        except requests.exceptions.ConnectionError:
            print(f"{i}-әрекет: қосылу қатесі")
        if i < attempts:
            time.sleep(delay)
            delay *= 2
    return None

print(get_with_retry("https://httpbin.org/status/503"))
```

</details>
