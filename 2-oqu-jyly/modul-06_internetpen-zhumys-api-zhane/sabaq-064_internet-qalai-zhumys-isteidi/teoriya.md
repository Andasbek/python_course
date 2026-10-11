# Сабақ 64. Интернет қалай жұмыс істейді. HTTP протоколы, сұраныс пен жауап.

## Тақырыптар

1. Клиент–сервер моделі
2. IP-мекенжай, DNS және порт
3. Браузерге мекенжай жазғанда не болады?
4. URL құрылымы
5. HTTP протоколы
6. HTTP және HTTPS
7. Python-дағы алғашқы HTTP сұраныс
8. Браузердің DevTools құралы
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Клиент–сервер моделі

**Аналогия: мейрамхана.** Сіз (клиент) даяшыға (желі) тапсырыс (сұраныс) бересіз, ас үй (сервер) тамақ (жауап) дайындап жібереді.

```text
 ┌──────────┐   1. Сұраныс (request)    ┌──────────┐
 │  Клиент  │ ─────────────────────────▶ │  Сервер  │
 │(браузер, │                            │ (сайт,   │
 │ Python)  │ ◀───────────────────────── │  API)    │
 └──────────┘   2. Жауап (response)      └──────────┘
```

- **Клиент** — сұраныс жіберетін бағдарлама: браузер, мобильді қосымша, біздің Python скрипт.
- **Сервер** — үнемі қосулы тұратын, сұраныстарды күтетін және жауап беретін компьютер/бағдарлама.
- Әрекеттесуді әрқашан **клиент бастайды**.

## 2. IP-мекенжай, DNS және порт

**IP-мекенжай** — желідегі құрылғының «пошталық мекенжайы». IPv4 мысалы: `142.250.74.46` (4 сан, әрқайсысы 0–255). IPv6 ұзынырақ: `2a00:1450:4001:82a::200e`.

**DNS (Domain Name System)** — интернеттің «телефон кітапшасы». Адамға `google.kz` есте сақтау оңай, ал компьютерге IP керек. DNS доменді IP-ге айналдырады.

```bash
nslookup google.kz
ping -c 3 google.kz      # Windows-та: ping google.kz
```

Python-да да DNS сұранысын жасауға болады:

```python
import socket

for domain in ["google.kz", "wikipedia.org"]:
    try:
        print(domain, "→", socket.gethostbyname(domain))
    except socket.gaierror:
        print(domain, "→ табылмады (интернет жоқ па?)")
```

Нәтижесі (IP уақыт өте өзгеруі мүмкін):
```text
google.kz → 142.250.74.67
wikipedia.org → 185.15.59.224
```

**Порт** — бір компьютердегі нақты қызметтің «пәтер нөмірі». IP — үй мекенжайы, порт — пәтер.

| Порт | Қызмет |
|---|---|
| 80 | HTTP |
| 443 | HTTPS |
| 22 | SSH |
| 5432 | PostgreSQL |

## 3. Браузерге мекенжай жазғанда не болады?

1. Браузер URL-ді талдайды: `https://kundelik.kz/login`.
2. **DNS** арқылы `kundelik.kz` → IP-мекенжай анықталады.
3. Сервермен **TCP қосылымы** орнатылады (443-порт).
4. **TLS** «қол алысуы» — шифрланған арна жасалады (HTTPS).
5. Браузер **HTTP сұранысын** жібереді: `GET /login`.
6. Сервер **HTTP жауабын** қайтарады: күй коды 200 және HTML.
7. Браузер HTML-ді оқып, CSS, сурет, JS үшін **қосымша сұраныстар** жібереді.
8. Бет экранға салынады (рендеринг).

Бір бетті ашу үшін ондаған, кейде жүздеген сұраныс жіберіледі!

## 4. URL құрылымы

```text
https://api.open-meteo.com:443/v1/forecast?latitude=43.25&longitude=76.95#top
└─┬─┘   └───────┬────────┘└┬┘└────┬─────┘ └────────────┬──────────────┘└┬┘
схема        домен       порт   жол (path)       параметрлер (query)   фрагмент
```

Python-да `urllib.parse` модулімен талдаймыз:

```python
from urllib.parse import urlparse, parse_qs

url = "https://api.open-meteo.com/v1/forecast?latitude=43.25&longitude=76.95&current_weather=true"
parts = urlparse(url)
print("Схема:", parts.scheme)
print("Домен:", parts.netloc)
print("Жол:", parts.path)
print("Параметрлер:", parse_qs(parts.query))
```

Нәтижесі:
```text
Схема: https
Домен: api.open-meteo.com
Жол: /v1/forecast
Параметрлер: {'latitude': ['43.25'], 'longitude': ['76.95'], 'current_weather': ['true']}
```

Кері амал — сөздіктен параметр жолын құру. Кириллица мен бос орындар автоматты түрде **кодталады** (URL encoding):

```python
from urllib.parse import urlencode

params = {"q": "Алматы ауа райы", "page": 2}
print("https://example.com/search?" + urlencode(params))
```

Нәтижесі:
```text
https://example.com/search?q=%D0%90%D0%BB%D0%BC%D0%B0%D1%82%D1%8B+%D0%B0%D1%83%D0%B0+%D1%80%D0%B0%D0%B9%D1%8B&page=2
```

## 5. HTTP протоколы

**HTTP (HyperText Transfer Protocol)** — клиент мен сервер арасындағы мәтіндік «келісім-шарт», сұраныс пен жауап қандай форматта болатынын анықтайды.

### HTTP сұранысы (request)

```text
GET /v1/forecast?latitude=43.25&longitude=76.95 HTTP/1.1      ← бастапқы жол: әдіс, жол, нұсқа
Host: api.open-meteo.com                                     ← тақырыптар (headers)
User-Agent: Mozilla/5.0
Accept: application/json
                                                             ← бос жол
(дене — GET-те әдетте бос)                                    ← дене (body)
```

### HTTP әдістері

| Әдіс | Мағынасы | CRUD сәйкестігі | Мысал |
|---|---|---|---|
| `GET` | Деректерді алу | Read | Бетті ашу, ауа райын сұрау |
| `POST` | Жаңа деректер жіберу | Create | Тіркелу формасы, пост жариялау |
| `PUT` / `PATCH` | Өзгерту (толық / ішінара) | Update | Профильді өңдеу |
| `DELETE` | Өшіру | Delete | Хабарламаны өшіру |

Бұл — таныс CRUD амалдары: SQL-дағы `SELECT`, `INSERT`, `UPDATE`, `DELETE` командаларының «желілік» баламасы.

### HTTP жауабы (response)

```text
HTTP/1.1 200 OK                                  ← күй жолы: нұсқа, код, сипаттама
Content-Type: application/json; charset=utf-8    ← тақырыптар
Content-Length: 312
Date: Thu, 04 Feb 2027 09:15:00 GMT

{"latitude":43.25,"current_weather":{"temperature":-3.2, ...}}   ← дене
```

### Күй кодтары (status codes)

| Топ | Мағынасы | Жиі кездесетін кодтар |
|---|---|---|
| 1xx | Ақпараттық | 101 |
| **2xx** | **Сәтті** | **200 OK**, 201 Created, 204 No Content |
| 3xx | Қайта бағыттау | 301 Moved Permanently, 302 Found, 304 Not Modified |
| **4xx** | **Клиент қатесі** | 400 Bad Request, 401 Unauthorized, 403 Forbidden, **404 Not Found**, 429 Too Many Requests |
| **5xx** | **Сервер қатесі** | **500 Internal Server Error**, 502 Bad Gateway, 503 Service Unavailable |

**Есте сақтау тәсілі:** 4xx — «сен қателестің» (клиент), 5xx — «мен қателестім» (сервер).

### Маңызды тақырыптар (headers)

| Тақырып | Қайда | Мағынасы |
|---|---|---|
| `Host` | сұраныс | Қай сайтқа |
| `User-Agent` | сұраныс | Клиент кім (браузер, Python) |
| `Accept` | сұраныс | Қандай формат күтеміз |
| `Content-Type` | екеуінде де | Дененің форматы: `text/html`, `application/json` |
| `Set-Cookie` / `Cookie` | жауап / сұраныс | Сайт клиентті «есте сақтауы» |
| `Authorization` | сұраныс | Кілт немесе токен |

## 6. HTTP және HTTPS

**HTTP** — деректер ашық мәтінмен жіберіледі: Wi-Fi желісіндегі зиянкес құпия сөзіңізді оқи алады.
**HTTPS** = HTTP + TLS шифрлау. Браузердегі 🔒 белгісі осыны білдіреді.

**Ереже:** құпия сөзді, карта нөмірін тек HTTPS сайтқа енгізіңіз. Біздің бағдарламаларда да әрқашан `https://` қолданамыз.

## 7. Python-дағы алғашқы HTTP сұраныс

Python-да HTTP сұранысын стандартты `urllib.request` модулімен жіберуге болады (ыңғайлырақ сыртқы баламасы — `requests` кітапханасы). Мысалы, жауаптың күй кодын және тақырыптарын көрейік:

```python
from urllib.request import urlopen
from urllib.error import URLError

try:
    with urlopen("https://example.com", timeout=10) as resp:
        print("Күй коды:", resp.status)
        print("Content-Type:", resp.headers["Content-Type"])
        html = resp.read().decode("utf-8")
        print("HTML ұзындығы:", len(html), "таңба")
        print(html[:60])
except URLError as e:
    print("Қосылу қатесі:", e.reason)
```

Нәтижесі:
```text
Күй коды: 200
Content-Type: text/html
HTML ұзындығы: 1256 таңба
<!doctype html>
<html>
<head>
    <title>Example Domain</title>
```

## 8. Браузердің DevTools құралы

1. Кез келген сайтты ашып, **F12** басыңыз → **Network** (Желі) қойындысы.
2. Бетті жаңартыңыз (F5).
3. Әр жол — бір HTTP сұранысы. Бағандар: **Name**, **Status**, **Type**, **Size**, **Time**.
4. Сұранысты басып, **Headers** (тақырыптар) және **Response** (жауап денесі) бөлімдерін қараңыз.
5. Сүзгіде **Fetch/XHR** таңдасаңыз — сайттың API-ға жіберетін сұраныстары ғана қалады. Олардың көбі JSON қайтарады!

## Жиі кездесетін қателер

**1. URL-де схеманы ұмыту:**
```python
from urllib.parse import urlparse
print(urlparse("example.com/page").netloc)          # бос жол! домен path-қа түсіп кетті
print(urlparse("https://example.com/page").netloc)  # example.com
```

**2. Кириллицаны URL-ге кодтамай қосу** — сервер түсінбеуі мүмкін. `urlencode()` немесе `quote()` қолданыңыз.

**3. «404 — интернет істемейді» деп ойлау.** 404 — сервер жауап берді, яғни интернет бар, тек бет табылмады. Интернет жоқ болса — күй коды мүлдем болмайды, `URLError` / қосылу қатесі шығады.

**4. 401 мен 403-ті шатастыру:** 401 — «сіз кім екеніңізді дәлелдемедіңіз» (кілт жоқ), 403 — «сізді танимын, бірақ рұқсат жоқ».

**5. `timeout` бермеу** — сервер жауап бермесе, бағдарлама шексіз күтіп «қатып» қалады.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Клиент / сервер | Сұраныс жіберуші / жауап беруші бағдарлама |
| IP-мекенжай | Желідегі құрылғының сандық мекенжайы |
| DNS | Домен атауын IP-ге айналдыратын жүйе |
| Порт | Компьютердегі қызметтің нөмірі (80, 443) |
| URL | Ресурстың толық мекенжайы |
| HTTP / HTTPS | Гипермәтінді жіберу протоколы / оның шифрланған нұсқасы |
| Сұраныс (request) | Әдіс + жол + тақырыптар + дене |
| Жауап (response) | Күй коды + тақырыптар + дене |
| Күй коды | Сұраныс нәтижесін білдіретін үш таңбалы сан |
| Header (тақырып) | «Кілт: мән» түріндегі қосымша ақпарат |

## Ескерту

- `urllib.request.urlopen()` 4xx/5xx кодтары үшін `HTTPError`, ал желі/DNS ақауында `URLError` шығарады — екеуі де `urllib.error` модулінде (https://docs.python.org/3/library/urllib.request.html).
- `http.HTTPStatus` барлық күй кодтарын атауларымен сақтайды: `HTTPStatus.NOT_FOUND == 404`, `HTTPStatus(404).phrase` → `'Not Found'`.
- URL-ді бөлшектеу — `urllib.parse.urlparse()`, параметрлерді дұрыс жинау (бос орын мен кириллицаны кодтаумен) — `urllib.parse.urlencode()`.
- HTTP тақырыптарының атаулары регистрге тәуелсіз: `Content-Type` пен `content-type` — бір тақырып.
- `socket.gethostbyname("python.org")` атау бойынша IP-мекенжайды береді — бұл DNS сұранысының өзі.
