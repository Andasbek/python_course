# Урок 64. Практические задания: интернет и HTTP

## Базовый уровень

### Задание 1. Исследователь DNS
С помощью `nslookup` в терминале найдите IP-адреса 3 сайтов (`kaspi.kz`, `egov.kz`, `github.com`). Затем напишите программу, которая делает то же самое в Python через `socket.gethostbyname()`. Если домен не найден, программа не должна падать, а должна выводить сообщение.

```text
Домен: egov.kz
egov.kz → 195.12.113.8
Домен: joq-sait-12345.kz
Домен не найден
```

### Задание 2. Анализатор URL
Пользователь вводит URL, программа выводит его части; параметры показывает отдельными строками.

```text
URL: https://www.olx.kz/elektronika/?search=ноутбук&page=3
Схема: https
Домен: www.olx.kz
Путь: /elektronika/
Параметры:
  search = ноутбук
  page = 3
Безопасный (HTTPS): да
```

### Задание 3. Толкователь кодов состояния
Функция `explain(code)` принимает код состояния и возвращает его группу и пояснение на русском языке. Храните в словаре не менее 10 кодов; для кода, которого нет в словаре, показывайте только группу.

```text
Код: 404
404 — Ошибка клиента: страница не найдена
Код: 418
418 — Ошибка клиента (описание неизвестно)
```

### Задание 4. Исследование DevTools (в тетради)
Откройте сайт `https://open-meteo.com`, F12 → Network. Заполните таблицу (не менее 5 запросов):

| Название | Метод | Код состояния | Content-Type | Размер |
|---|---|---|---|---|

Вопросы: сколько запросов было отправлено? Какой файл самый большой? Встретился ли код 304, что он означает?

### Задание 5. Конструктор URL
Введите координаты города (широта, долгота) и с помощью `urlencode` постройте URL запроса к Open-Meteo. Откройте полученный URL в браузере и посмотрите JSON-ответ.

```text
Широта: 51.17
Долгота: 71.45
https://api.open-meteo.com/v1/forecast?latitude=51.17&longitude=71.45&current_weather=true
```

## Продвинутый уровень

### Задание 6. Проверка сайтов
Проверьте список из 4 сайтов через `urllib.request.urlopen` и выведите в виде таблицы код состояния, заголовок `Content-Type` и время ответа (`time.perf_counter()`) каждого из них. При ошибке — «недоступен».

```text
Сайт                    Код  Время   Тип
https://example.com     200  0.41 с  text/html
https://httpbin.org/status/404  404  0.87 с  —
```

Подсказка: при кодах 4xx/5xx `urlopen` выбрасывает `HTTPError`, у которого есть атрибут `.code`.

### Задание 7. Построение запроса «вручную»
Функция `build_request(method, host, path, headers)` должна собирать из списка строк текст HTTP-запроса (начальная строка, заголовки, пустая строка) и возвращать его. Выведите результат на экран и сравните с образцом из теории.

## ★ Олимпиадная / творческая задача

### Задание 8. HTTP через сокет
С помощью модуля `socket` подключитесь к порту 80 сервера `example.com` и вручную отправьте запрос HTTP/1.1 `GET /` (с заголовками `Host` и `Connection: close`). Выведите первую строку ответа (строку состояния) и все заголовки. Это поможет понять, что библиотека `requests` делает «внутри».

### Задание 9. Инфографика «Путешествие запроса»
Создайте схему (на бумаге или в draw.io), изображающую 8 шагов от ввода URL в браузере до появления страницы. К каждому шагу приведите пример (реальный IP, реальный код состояния).

## Домашнее задание

1. Выучите таблицу кодов состояния из теории (2xx, 3xx, 4xx, 5xx — по 2 кода из каждой группы).
2. Дома просмотрите через DevTools запросы Fetch/XHR своего любимого сайта, найдите один, возвращающий JSON, и запишите его URL.
3. Выполните в терминале `pip install requests` (к следующему уроку).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import socket

for _ in range(2):
    domain = input("Домен: ").strip()
    try:
        print(domain, "→", socket.gethostbyname(domain))
    except socket.gaierror:
        print("Домен не найден")
```

**Задание 2**
```python
from urllib.parse import urlparse, parse_qs

url = input("URL: ")
p = urlparse(url)
print("Схема:", p.scheme)
print("Домен:", p.netloc)
print("Путь:", p.path)
params = parse_qs(p.query)
if params:
    print("Параметры:")
    for key, values in params.items():
        print(f"  {key} = {', '.join(values)}")
print("Безопасный (HTTPS):", "да" if p.scheme == "https" else "нет")
```

**Задание 3**
```python
CODES = {
    200: "успешно выполнено", 201: "создан новый ресурс", 204: "нет содержимого",
    301: "перемещён навсегда", 302: "перемещён временно", 304: "не изменён (бери из кэша)",
    400: "неверный запрос", 401: "требуется авторизация", 403: "доступ запрещён",
    404: "страница не найдена", 429: "слишком много запросов", 500: "внутренняя ошибка сервера",
    502: "ошибочный шлюз", 503: "сервис временно недоступен",
}
GROUPS = {1: "Информационный", 2: "Успех", 3: "Перенаправление", 4: "Ошибка клиента", 5: "Ошибка сервера"}

def explain(code):
    group = GROUPS.get(code // 100, "Неизвестная группа")
    if code in CODES:
        return f"{code} — {group}: {CODES[code]}"
    return f"{code} — {group} (описание неизвестно)"

print(explain(404))
print(explain(418))
```

**Задание 5**
```python
from urllib.parse import urlencode

lat = float(input("Широта: "))
lon = float(input("Долгота: "))
params = {"latitude": lat, "longitude": lon, "current_weather": "true"}
print("https://api.open-meteo.com/v1/forecast?" + urlencode(params))
```

**Задание 6**
```python
import time
from urllib.request import urlopen
from urllib.error import HTTPError, URLError

sites = ["https://example.com", "https://httpbin.org/status/404",
         "https://www.python.org", "https://joq-sait-12345.kz"]
print(f"{'Сайт':35} {'Код':>4}  {'Время':>7}  Тип")
for url in sites:
    start = time.perf_counter()
    try:
        with urlopen(url, timeout=10) as r:
            code, ctype = r.status, r.headers.get("Content-Type", "—")
    except HTTPError as e:
        code, ctype = e.code, "—"
    except URLError:
        print(f"{url:35} недоступен")
        continue
    t = time.perf_counter() - start
    print(f"{url:35} {code:>4}  {t:5.2f} с  {ctype}")
```

**Задание 7**
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

**Задание 8**
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
print("Строка состояния:", lines[0])
for h in lines[1:]:
    print("  ", h)
print("Размер тела:", len(body), "байт")
```

**Задания 4, 9** — творческие; оценивание: полнота таблицы, правильное объяснение кода 304 как «браузер использовал версию из кэша».

</details>
