# Сабақ 64. Практикалық тапсырмалар: Интернет және HTTP

## Базалық деңгей

### 1-тапсырма. DNS зерттеушісі
Терминалда `nslookup` арқылы 3 сайттың (`kaspi.kz`, `egov.kz`, `github.com`) IP-мекенжайын табыңыз. Содан кейін осыны Python-да `socket.gethostbyname()` арқылы орындайтын бағдарлама жазыңыз. Домен табылмаса, бағдарлама құламай, хабар шығарсын.

```text
Домен: egov.kz
egov.kz → 195.12.113.8
Домен: joq-sait-12345.kz
Домен табылмады
```

### 2-тапсырма. URL талдаушы
Пайдаланушы URL енгізеді, бағдарлама оның бөліктерін шығарады; параметрлерді жеке жолдармен көрсетеді.

```text
URL: https://www.olx.kz/elektronika/?search=ноутбук&page=3
Схема: https
Домен: www.olx.kz
Жол: /elektronika/
Параметрлер:
  search = ноутбук
  page = 3
Қауіпсіз (HTTPS): иә
```

### 3-тапсырма. Күй кодын түсіндіргіш
`explain(code)` функциясы күй кодын қабылдап, тобын және қазақша түсіндірмесін қайтарсын. Кемінде 10 кодты сөздікте сақтаңыз; сөздікте жоқ код үшін тек тобын көрсетсін.

```text
Код: 404
404 — Клиент қатесі: бет табылмады
Код: 418
418 — Клиент қатесі (сипаттама белгісіз)
```

### 4-тапсырма. DevTools зерттеуі (дәптерде)
`https://open-meteo.com` сайтын ашып, F12 → Network. Кестені толтырыңыз (кемінде 5 сұраныс):

| Атауы | Әдіс | Күй коды | Content-Type | Өлшемі |
|---|---|---|---|---|

Сұрақтар: қанша сұраныс жіберілді? Ең үлкен файл қайсы? 304 коды кездесті ме, ол нені білдіреді?

### 5-тапсырма. URL құрастырушы
Қала координаттарын (ендік, бойлық) енгізіп, `urlencode` арқылы Open-Meteo сұраныс URL-ін құрастырыңыз. Нәтижедегі URL-ді браузерде ашып, JSON жауапты көріңіз.

```text
Ендік: 51.17
Бойлық: 71.45
https://api.open-meteo.com/v1/forecast?latitude=51.17&longitude=71.45&current_weather=true
```

## Кеңейтілген деңгей

### 6-тапсырма. Сайттарды тексеру
`urllib.request.urlopen` арқылы 4 сайттың тізімін тексеріп, әрқайсысының күй кодын, `Content-Type` тақырыбын және жауап уақытын (`time.perf_counter()`) кесте түрінде шығарыңыз. Қате болса — «қолжетімсіз».

```text
Сайт                    Код  Уақыт   Түрі
https://example.com     200  0.41 с  text/html
https://httpbin.org/status/404  404  0.87 с  —
```

Кеңес: 4xx/5xx кодтарында `urlopen` `HTTPError` лақтырады, оның `.code` атрибуты бар.

### 7-тапсырма. Сұранысты «қолмен» құру
`build_request(method, host, path, headers)` функциясы HTTP сұранысының мәтінін (бастапқы жол, тақырыптар, бос жол) жолдар тізімінен құрастырып қайтарсын. Нәтижені экранға шығарып, теориядағы үлгімен салыстырыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Сокет арқылы HTTP
`socket` модулімен `example.com` серверінің 80-портына қосылып, HTTP/1.1 `GET /` сұранысын қолмен жіберіңіз (`Host` және `Connection: close` тақырыптарымен). Жауаптың бірінші жолын (күй жолы) және барлық тақырыптарын шығарыңыз. Бұл — `requests` кітапханасы «ішінде» не істейтінін түсінуге көмектеседі.

### 9-тапсырма. «Сұраныс саяхаты» инфографикасы
Браузерге URL жазғаннан бет көрінгенге дейінгі 8 қадамды суреттейтін схема (қағазда немесе draw.io-да) жасаңыз. Әр қадамға мысал келтіріңіз (нақты IP, нақты күй коды).

## Үй тапсырмасы

1. Теориядағы күй кодтары кестесін жаттаңыз (2xx, 3xx, 4xx, 5xx — әрқайсысынан 2 код).
2. Үйде DevTools арқылы сүйікті сайтыңыздың Fetch/XHR сұраныстарын қарап, JSON қайтаратын біреуін тауып, URL-ін жазып келіңіз.
3. Терминалда `pip install requests` орындаңыз (келесі сабаққа).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import socket

for _ in range(2):
    domain = input("Домен: ").strip()
    try:
        print(domain, "→", socket.gethostbyname(domain))
    except socket.gaierror:
        print("Домен табылмады")
```

**2-тапсырма**
```python
from urllib.parse import urlparse, parse_qs

url = input("URL: ")
p = urlparse(url)
print("Схема:", p.scheme)
print("Домен:", p.netloc)
print("Жол:", p.path)
params = parse_qs(p.query)
if params:
    print("Параметрлер:")
    for key, values in params.items():
        print(f"  {key} = {', '.join(values)}")
print("Қауіпсіз (HTTPS):", "иә" if p.scheme == "https" else "жоқ")
```

**3-тапсырма**
```python
CODES = {
    200: "сәтті орындалды", 201: "жаңа ресурс құрылды", 204: "мазмұн жоқ",
    301: "біржола көшірілді", 302: "уақытша көшірілді", 304: "өзгермеген (кэштен ал)",
    400: "қате сұраныс", 401: "авторизация қажет", 403: "рұқсат жоқ",
    404: "бет табылмады", 429: "сұраныс тым көп", 500: "сервердің ішкі қатесі",
    502: "қате шлюз", 503: "қызмет уақытша қолжетімсіз",
}
GROUPS = {1: "Ақпараттық", 2: "Сәтті", 3: "Қайта бағыттау", 4: "Клиент қатесі", 5: "Сервер қатесі"}

def explain(code):
    group = GROUPS.get(code // 100, "Белгісіз топ")
    if code in CODES:
        return f"{code} — {group}: {CODES[code]}"
    return f"{code} — {group} (сипаттама белгісіз)"

print(explain(404))
print(explain(418))
```

**5-тапсырма**
```python
from urllib.parse import urlencode

lat = float(input("Ендік: "))
lon = float(input("Бойлық: "))
params = {"latitude": lat, "longitude": lon, "current_weather": "true"}
print("https://api.open-meteo.com/v1/forecast?" + urlencode(params))
```

**6-тапсырма**
```python
import time
from urllib.request import urlopen
from urllib.error import HTTPError, URLError

sites = ["https://example.com", "https://httpbin.org/status/404",
         "https://www.python.org", "https://joq-sait-12345.kz"]
print(f"{'Сайт':35} {'Код':>4}  {'Уақыт':>7}  Түрі")
for url in sites:
    start = time.perf_counter()
    try:
        with urlopen(url, timeout=10) as r:
            code, ctype = r.status, r.headers.get("Content-Type", "—")
    except HTTPError as e:
        code, ctype = e.code, "—"
    except URLError:
        print(f"{url:35} қолжетімсіз")
        continue
    t = time.perf_counter() - start
    print(f"{url:35} {code:>4}  {t:5.2f} с  {ctype}")
```

**7-тапсырма**
```python
def build_request(method, host, path, headers):
    lines = [f"{method} {path} HTTP/1.1", f"Host: {host}"]
    for key, value in headers.items():
        lines.append(f"{key}: {value}")
    lines.append("")
    return "\r\n".join(lines) + "\r\n"

print(build_request("GET", "api.open-meteo.com", "/v1/forecast?latitude=43.25&longitude=76.95",
                    {"User-Agent": "MyPython/1.0", "Accept": "application/json"}))
```

**8-тапсырма**
```python
import socket

request = "GET / HTTP/1.1\r\nHost: example.com\r\nConnection: close\r\n\r\n"
with socket.create_connection(("example.com", 80), timeout=10) as s:
    s.sendall(request.encode("ascii"))
    data = b""
    while chunk := s.recv(4096):
        data += chunk

head, _, body = data.partition(b"\r\n\r\n")
lines = head.decode("iso-8859-1").split("\r\n")
print("Күй жолы:", lines[0])
for h in lines[1:]:
    print("  ", h)
print("Дене өлшемі:", len(body), "байт")
```

**4, 9-тапсырмалар** — шығармашылық; бағалау: кестенің толықтығы, 304 коды «браузер кэштегі нұсқаны қолданды» деп дұрыс түсіндірілуі.

</details>
