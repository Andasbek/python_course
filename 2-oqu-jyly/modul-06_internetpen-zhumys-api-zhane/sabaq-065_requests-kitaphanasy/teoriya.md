# Сабақ 65. requests кітапханасы. GET сұранысын жіберу және жауапты алу.

## Тақырыптар

1. Неге `requests`?
2. Орнату және виртуалды орта
3. Алғашқы GET сұраныс
4. `Response` объектісі
5. Параметрлер: `params`
6. Тақырыптар: `headers`
7. `timeout` — міндетті аргумент
8. Қателерді өңдеу
9. Файлдарды жүктеу
10. POST туралы бір ауыз
11. Жиі кездесетін қателер
12. Негізгі ұғымдар

## 1. Неге `requests`?

Стандартты `urllib` жұмыс істейді, бірақ көп кодты қажет етеді. `requests` — Python-дағы ең танымал кітапханалардың бірі (аптасына миллиондаған жүктеу), оның ұраны: **«HTTP for Humans»** — «Адамдарға арналған HTTP».

| Міндет | `urllib` | `requests` |
|---|---|---|
| Параметрлерді қосу | `urlencode` + жолдарды біріктіру | `params={...}` |
| JSON алу | `json.loads(resp.read().decode())` | `resp.json()` |
| 404 кезінде | Ерекшелік лақтырады | `status_code` = 404, өзіміз шешеміз |

## 2. Орнату және виртуалды орта

`requests` — сыртқы кітапхана, оны орнату керек. Ол үшін виртуалды орта (venv) және `pip` пакет менеджері қолданылады:

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS / Linux:
source .venv/bin/activate
pip install requests
pip freeze > requirements.txt
```

**Виртуалды орта (venv)** — жобаның «жеке сөмкесі»: әр жобаның өз кітапханалары болады, бір-біріне кедергі жасамайды. `requirements.txt` басқа адамға жобаны `pip install -r requirements.txt` арқылы бірден іске қосуға мүмкіндік береді. `.venv` папкасы `.gitignore`-ға қосылады.

Тексеру:
```python
import requests
print(requests.__version__)
```

## 3. Алғашқы GET сұраныс

```python
import requests

response = requests.get("https://httpbin.org/get", timeout=10)
print("Күй коды:", response.status_code)
print("Сәтті ме:", response.ok)
print("Content-Type:", response.headers["Content-Type"])
print(response.text[:120])
```

Нәтижесі (шамамен):
```text
Күй коды: 200
Сәтті ме: True
Content-Type: application/json
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "gzip, deflate",
    "Host": "httpbin.org",
```

`httpbin.org` — оқу үшін арнайы жасалған сервис: ол сіздің сұранысыңызды «айнадай» қайтарады.

## 4. `Response` объектісі

`requests.get()` нәтижесі — `Response` объектісі. Оның негізгі атрибуттары:

| Атрибут / әдіс | Түрі | Мағынасы |
|---|---|---|
| `status_code` | `int` | Күй коды: 200, 404... |
| `ok` | `bool` | Код 400-ден кіші болса `True` |
| `reason` | `str` | Код сипаттамасы: `"OK"`, `"Not Found"` |
| `text` | `str` | Дене мәтін түрінде (HTML, JSON-мәтін) |
| `content` | `bytes` | Дене байттар түрінде (сурет, PDF) |
| `json()` | `dict`/`list` | JSON денені Python объектісіне айналдырады |
| `headers` | сөздік тәрізді | Жауап тақырыптары (регистрге сезімсіз) |
| `url` | `str` | Соңғы URL (параметрлермен, қайта бағыттаудан кейін) |
| `encoding` | `str` | Мәтін кодировкасы |
| `elapsed` | `timedelta` | Жауап уақыты |
| `raise_for_status()` | әдіс | 4xx/5xx болса `HTTPError` лақтырады |

### `json()` — ең жиі қолданылатын әдіс

```python
import requests

r = requests.get("https://jsonplaceholder.typicode.com/users/1", timeout=10)
user = r.json()              # dict
print(type(user))
print(user["name"])
print(user["address"]["city"])
print("Жауап уақыты:", r.elapsed.total_seconds(), "с")
```

Нәтижесі:
```text
<class 'dict'>
Leanne Graham
Gwenborough
Жауап уақыты: 0.183 с
```

`jsonplaceholder` — оқуға арналған жалған (тест) деректер сервисі: пайдаланушылар, посттар, тапсырмалар.

## 5. Параметрлер: `params`

URL-ді қолмен құрамаймыз — `requests` өзі кодтайды:

```python
import requests

params = {"latitude": 43.25, "longitude": 76.95, "current_weather": "true"}
r = requests.get("https://api.open-meteo.com/v1/forecast", params=params, timeout=10)
print(r.url)
data = r.json()
print("Алматыдағы температура:", data["current_weather"]["temperature"], "°C")
```

Нәтижесі (температура әр сәтте әртүрлі):
```text
https://api.open-meteo.com/v1/forecast?latitude=43.25&longitude=76.95&current_weather=true
Алматыдағы температура: -2.4 °C
```

Тізім берсек — параметр қайталанады: `params={"id": [1, 2]}` → `?id=1&id=2`.

## 6. Тақырыптар: `headers`

Кейбір сайттар сұраныстың кімнен келгенін (`User-Agent`) тексереді. Өз бағдарламаңызды «таныстыру» — әдепті тәжірибе:

```python
import requests

headers = {"User-Agent": "PythonClub-Almaty/1.0 (oqu maqsatynda)", "Accept": "application/json"}
r = requests.get("https://httpbin.org/headers", headers=headers, timeout=10)
print(r.json()["headers"]["User-Agent"])
```

Нәтижесі:
```text
PythonClub-Almaty/1.0 (oqu maqsatynda)
```

## 7. `timeout` — міндетті аргумент

`timeout` берілмесе, сервер жауап бермеген жағдайда бағдарлама **шексіз** күтеді. Ереже: **әр сұранысқа `timeout`**.

```python
import requests

try:
    r = requests.get("https://httpbin.org/delay/5", timeout=2)   # сервер 5 с кідіреді
except requests.exceptions.Timeout:
    print("Сервер 2 секунд ішінде жауап бермеді")
```

Нәтижесі:
```text
Сервер 2 секунд ішінде жауап бермеді
```

## 8. Қателерді өңдеу

Желі — сенімсіз орта: интернет үзілуі, сервер құлауы, URL қате болуы мүмкін.

| Ерекшелік | Қашан туындайды |
|---|---|
| `requests.exceptions.ConnectionError` | Интернет жоқ, домен табылмады |
| `requests.exceptions.Timeout` | Белгіленген уақытта жауап жоқ |
| `requests.exceptions.HTTPError` | `raise_for_status()` 4xx/5xx тапты |
| `requests.exceptions.RequestException` | Жоғарыдағылардың барлығының «атасы» |
| `requests.exceptions.JSONDecodeError` | Жауап JSON емес, бірақ `json()` шақырылды |

Әмбебап «қауіпсіз» функция:

```python
import requests

def safe_get_json(url, params=None):
    """JSON деректерді немесе қате болса None қайтарады."""
    try:
        r = requests.get(url, params=params, timeout=10)
        r.raise_for_status()
        return r.json()
    except requests.exceptions.Timeout:
        print("Қате: сервер уақытында жауап бермеді")
    except requests.exceptions.ConnectionError:
        print("Қате: интернетке қосылу мүмкін емес")
    except requests.exceptions.HTTPError as e:
        print("HTTP қатесі:", e.response.status_code)
    except requests.exceptions.JSONDecodeError:
        print("Қате: жауап JSON форматында емес")
    return None

print(safe_get_json("https://jsonplaceholder.typicode.com/todos/1"))
print(safe_get_json("https://jsonplaceholder.typicode.com/todos/99999"))
```

Нәтижесі:
```text
{'userId': 1, 'id': 1, 'title': 'delectus aut autem', 'completed': False}
HTTP қатесі: 404
None
```

**Ескерту:** `except` блоктарының реті маңызды — нақтырақ ерекшеліктер жоғарыда болуы керек.

## 9. Файлдарды жүктеу

Сурет, PDF сияқты екілік файлдар үшін `text` емес, `content` (байттар) қолданамыз және файлды `"wb"` режимінде ашамыз:

```python
import requests

url = "https://www.python.org/static/img/python-logo.png"
r = requests.get(url, timeout=15)
if r.ok:
    with open("python_logo.png", "wb") as f:
        f.write(r.content)
    print(f"Сақталды: {len(r.content)} байт")
```

Нәтижесі:
```text
Сақталды: 15770 байт
```

Үлкен файлдар үшін (бейне, архив) жадты толтырмау мақсатында бөліктеп жүктейміз:

```python
import requests

def download(url, path):
    with requests.get(url, stream=True, timeout=30) as r:
        r.raise_for_status()
        with open(path, "wb") as f:
            for chunk in r.iter_content(chunk_size=8192):
                f.write(chunk)
    return path
```

## 10. POST туралы бір ауыз

Деректерді жіберу үшін `requests.post()` қолданылады (мысалы, форма жіберу):

```python
import requests

r = requests.post("https://httpbin.org/post", json={"name": "Айгерім", "grade": 9}, timeout=10)
print(r.status_code, r.json()["json"])
```

Нәтижесі:
```text
200 {'grade': 9, 'name': 'Айгерім'}
```

Біздің модульдегі басты құрал — GET, POST-ты жобаларда қажет болғанда ғана қолданамыз.

## Жиі кездесетін қателер

**1. Файлды `requests.py` деп атау** — Python кітапхананың орнына сіздің файлыңызды импорттайды: `AttributeError: module 'requests' has no attribute 'get'`. Файл атын өзгертіп, `__pycache__` папкасын өшіріңіз.

**2. `ModuleNotFoundError: No module named 'requests'`** — кітапхана орнатылмаған немесе басқа Python/venv таңдалған. VS Code-та төменгі оң жақтан дұрыс интерпретаторды таңдаңыз.

**3. `json()` жақшасыз:**
```python
import requests
r = requests.get("https://jsonplaceholder.typicode.com/todos/1", timeout=10)
# Қате: r.json["title"]  → TypeError: 'method' object is not subscriptable
print(r.json()["title"])   # Дұрыс
```

**4. Күй кодын тексермей `json()` шақыру** — 404 бетінде HTML келуі мүмкін, `JSONDecodeError` шығады. Алдымен `r.raise_for_status()` немесе `if r.ok:`.

**5. Сұранысты циклде өте жиі жіберу** — сервер `429 Too Many Requests` қайтарады немесе IP-ді бұғаттайды. Сұраныстар арасына `time.sleep(1)` қойыңыз.

**6. Суретті `r.text` арқылы сақтау** — файл бүлінеді. Екілік деректер үшін `r.content` және `"wb"`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `requests` | HTTP сұраныстарына арналған сыртқы кітапхана |
| `pip` | Python кітапханаларын орнату құралы |
| Виртуалды орта (venv) | Жобаның жеке кітапханалар ортасы |
| `requirements.txt` | Жобаға қажет кітапханалар тізімі |
| `Response` | Сервер жауабын сақтайтын объект |
| `params` | URL параметрлерін сөздік ретінде беру |
| `timeout` | Жауапты күтудің ең ұзақ уақыты |
| `raise_for_status()` | Қате кодында ерекшелік лақтыратын әдіс |
| `content` / `text` | Жауап денесі: байттар / мәтін |

## Ескерту

- `requests`-те әдепкі бойынша таймаут **жоқ** — `timeout=` берілмесе, бағдарлама жауапты шексіз күтуі мүмкін.
- `r.raise_for_status()` тек 4xx/5xx кодтарында `HTTPError` шығарады; 3xx бағыттауларды `requests` автоматты орындайды (тарихы — `r.history`-де).
- `r.text` денені `r.encoding` бойынша декодтайды; кириллица бұзылып шықса, `r.encoding = "utf-8"` орнатыңыз немесе `r.content.decode("utf-8")` қолданыңыз.
- Бір сайтқа бірнеше сұраныс жіберілсе, `requests.Session()` қолданыңыз — ол қосылымды және ортақ тақырыптарды қайта пайдаланады.
- Тәуелділіктерді бекітіңіз: `pip freeze > requirements.txt`, орнату — `pip install -r requirements.txt`. Құжаттама: https://requests.readthedocs.io.
