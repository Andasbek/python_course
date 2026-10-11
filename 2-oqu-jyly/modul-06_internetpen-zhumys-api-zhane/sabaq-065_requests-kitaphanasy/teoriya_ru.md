# Урок 65. Библиотека requests. Отправка GET-запроса и получение ответа.

## Темы

1. Почему `requests`?
2. Установка и виртуальное окружение
3. Первый GET-запрос
4. Объект `Response`
5. Параметры: `params`
6. Заголовки: `headers`
7. `timeout` — обязательный аргумент
8. Обработка ошибок
9. Скачивание файлов
10. Пару слов о POST
11. Частые ошибки
12. Основные понятия

## 1. Почему `requests`?

Стандартный `urllib` работает, но требует много кода. `requests` — одна из самых популярных библиотек Python (миллионы загрузок в неделю), её девиз: **«HTTP for Humans»** — «HTTP для людей».

| Задача | `urllib` | `requests` |
|---|---|---|
| Добавить параметры | `urlencode` + склеивание строк | `params={...}` |
| Получить JSON | `json.loads(resp.read().decode())` | `resp.json()` |
| При 404 | Выбрасывает исключение | `status_code` = 404, решаем сами |

## 2. Установка и виртуальное окружение

`requests` — внешняя библиотека, её нужно установить. Для этого используются виртуальное окружение (venv) и менеджер пакетов `pip`:

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS / Linux:
source .venv/bin/activate
pip install requests
pip freeze > requirements.txt
```

**Виртуальное окружение (venv)** — «личный рюкзак» проекта: у каждого проекта свои библиотеки, они не мешают друг другу. `requirements.txt` позволяет другому человеку сразу запустить проект через `pip install -r requirements.txt`. Папка `.venv` добавляется в `.gitignore`.

Проверка:
```python
import requests
print(requests.__version__)
```

## 3. Первый GET-запрос

```python
import requests

response = requests.get("https://httpbin.org/get", timeout=10)
print("Код состояния:", response.status_code)
print("Успешно ли:", response.ok)
print("Content-Type:", response.headers["Content-Type"])
print(response.text[:120])
```

Результат (примерно):
```text
Код состояния: 200
Успешно ли: True
Content-Type: application/json
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Accept-Encoding": "gzip, deflate",
    "Host": "httpbin.org",
```

`httpbin.org` — сервис, специально созданный для обучения: он «зеркально» возвращает ваш запрос.

## 4. Объект `Response`

Результат `requests.get()` — объект `Response`. Его основные атрибуты:

| Атрибут / метод | Тип | Значение |
|---|---|---|
| `status_code` | `int` | Код состояния: 200, 404... |
| `ok` | `bool` | `True`, если код меньше 400 |
| `reason` | `str` | Описание кода: `"OK"`, `"Not Found"` |
| `text` | `str` | Тело в виде текста (HTML, JSON-текст) |
| `content` | `bytes` | Тело в виде байтов (картинка, PDF) |
| `json()` | `dict`/`list` | Превращает JSON-тело в объект Python |
| `headers` | словареподобный | Заголовки ответа (нечувствительны к регистру) |
| `url` | `str` | Итоговый URL (с параметрами, после перенаправлений) |
| `encoding` | `str` | Кодировка текста |
| `elapsed` | `timedelta` | Время ответа |
| `raise_for_status()` | метод | Выбрасывает `HTTPError` при 4xx/5xx |

### `json()` — самый используемый метод

```python
import requests

r = requests.get("https://jsonplaceholder.typicode.com/users/1", timeout=10)
user = r.json()              # dict
print(type(user))
print(user["name"])
print(user["address"]["city"])
print("Время ответа:", r.elapsed.total_seconds(), "с")
```

Результат:
```text
<class 'dict'>
Leanne Graham
Gwenborough
Время ответа: 0.183 с
```

`jsonplaceholder` — сервис фиктивных (тестовых) данных для обучения: пользователи, посты, задачи.

## 5. Параметры: `params`

URL вручную не собираем — `requests` сам его закодирует:

```python
import requests

params = {"latitude": 43.25, "longitude": 76.95, "current_weather": "true"}
r = requests.get("https://api.open-meteo.com/v1/forecast", params=params, timeout=10)
print(r.url)
data = r.json()
print("Температура в Алматы:", data["current_weather"]["temperature"], "°C")
```

Результат (температура каждый раз разная):
```text
https://api.open-meteo.com/v1/forecast?latitude=43.25&longitude=76.95&current_weather=true
Температура в Алматы: -2.4 °C
```

Если передать список — параметр повторится: `params={"id": [1, 2]}` → `?id=1&id=2`.

## 6. Заголовки: `headers`

Некоторые сайты проверяют, от кого пришёл запрос (`User-Agent`). «Представить» свою программу — вежливая практика:

```python
import requests

headers = {"User-Agent": "PythonClub-Almaty/1.0 (educational)", "Accept": "application/json"}
r = requests.get("https://httpbin.org/headers", headers=headers, timeout=10)
print(r.json()["headers"]["User-Agent"])
```

Результат:
```text
PythonClub-Almaty/1.0 (educational)
```

## 7. `timeout` — обязательный аргумент

Если `timeout` не задан, а сервер не отвечает, программа будет ждать **бесконечно**. Правило: **у каждого запроса — `timeout`**.

```python
import requests

try:
    r = requests.get("https://httpbin.org/delay/5", timeout=2)   # сервер задерживает ответ на 5 с
except requests.exceptions.Timeout:
    print("Сервер не ответил в течение 2 секунд")
```

Результат:
```text
Сервер не ответил в течение 2 секунд
```

## 8. Обработка ошибок

Сеть — ненадёжная среда: может пропасть интернет, упасть сервер, оказаться неверным URL.

| Исключение | Когда возникает |
|---|---|
| `requests.exceptions.ConnectionError` | Нет интернета, домен не найден |
| `requests.exceptions.Timeout` | Нет ответа за отведённое время |
| `requests.exceptions.HTTPError` | `raise_for_status()` обнаружил 4xx/5xx |
| `requests.exceptions.RequestException` | «Предок» всех перечисленных выше |
| `requests.exceptions.JSONDecodeError` | Ответ не JSON, но вызван `json()` |

Универсальная «безопасная» функция:

```python
import requests

def safe_get_json(url, params=None):
    """Возвращает JSON-данные или None при ошибке."""
    try:
        r = requests.get(url, params=params, timeout=10)
        r.raise_for_status()
        return r.json()
    except requests.exceptions.Timeout:
        print("Ошибка: сервер не ответил вовремя")
    except requests.exceptions.ConnectionError:
        print("Ошибка: невозможно подключиться к интернету")
    except requests.exceptions.HTTPError as e:
        print("Ошибка HTTP:", e.response.status_code)
    except requests.exceptions.JSONDecodeError:
        print("Ошибка: ответ не в формате JSON")
    return None

print(safe_get_json("https://jsonplaceholder.typicode.com/todos/1"))
print(safe_get_json("https://jsonplaceholder.typicode.com/todos/99999"))
```

Результат:
```text
{'userId': 1, 'id': 1, 'title': 'delectus aut autem', 'completed': False}
Ошибка HTTP: 404
None
```

**Примечание:** порядок блоков `except` важен — более конкретные исключения должны стоять выше.

## 9. Скачивание файлов

Для двоичных файлов (картинки, PDF) используем не `text`, а `content` (байты) и открываем файл в режиме `"wb"`:

```python
import requests

url = "https://www.python.org/static/img/python-logo.png"
r = requests.get(url, timeout=15)
if r.ok:
    with open("python_logo.png", "wb") as f:
        f.write(r.content)
    print(f"Сохранено: {len(r.content)} байт")
```

Результат:
```text
Сохранено: 15770 байт
```

Большие файлы (видео, архивы) скачиваем частями, чтобы не переполнять память:

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

## 10. Пару слов о POST

Для отправки данных используется `requests.post()` (например, отправка формы):

```python
import requests

r = requests.post("https://httpbin.org/post", json={"name": "Айгерім", "grade": 9}, timeout=10)
print(r.status_code, r.json()["json"])
```

Результат:
```text
200 {'grade': 9, 'name': 'Айгерім'}
```

Главный инструмент нашего модуля — GET, а POST используем в проектах только при необходимости.

## Частые ошибки

**1. Назвать файл `requests.py`** — Python импортирует ваш файл вместо библиотеки: `AttributeError: module 'requests' has no attribute 'get'`. Переименуйте файл и удалите папку `__pycache__`.

**2. `ModuleNotFoundError: No module named 'requests'`** — библиотека не установлена или выбран другой Python/venv. В VS Code выберите правильный интерпретатор в правом нижнем углу.

**3. `json()` без скобок:**
```python
import requests
r = requests.get("https://jsonplaceholder.typicode.com/todos/1", timeout=10)
# Неправильно: r.json["title"]  → TypeError: 'method' object is not subscriptable
print(r.json()["title"])   # Правильно
```

**4. Вызов `json()` без проверки кода состояния** — на странице 404 может прийти HTML, возникнет `JSONDecodeError`. Сначала `r.raise_for_status()` или `if r.ok:`.

**5. Слишком частая отправка запросов в цикле** — сервер вернёт `429 Too Many Requests` или заблокирует IP. Ставьте между запросами `time.sleep(1)`.

**6. Сохранение картинки через `r.text`** — файл будет повреждён. Для двоичных данных — `r.content` и `"wb"`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `requests` | Внешняя библиотека для HTTP-запросов |
| `pip` | Инструмент установки библиотек Python |
| Виртуальное окружение (venv) | Отдельная среда библиотек проекта |
| `requirements.txt` | Список библиотек, необходимых проекту |
| `Response` | Объект, хранящий ответ сервера |
| `params` | Передача параметров URL в виде словаря |
| `timeout` | Максимальное время ожидания ответа |
| `raise_for_status()` | Метод, выбрасывающий исключение при коде ошибки |
| `content` / `text` | Тело ответа: байты / текст |

## Примечание

- В `requests` по умолчанию **нет** таймаута — без `timeout=` программа может ждать ответа бесконечно.
- `r.raise_for_status()` выбрасывает `HTTPError` только для кодов 4xx/5xx; перенаправления 3xx `requests` выполняет автоматически (история — в `r.history`).
- `r.text` декодирует тело по `r.encoding`; если кириллица отображается неправильно, задайте `r.encoding = "utf-8"` или используйте `r.content.decode("utf-8")`.
- Для нескольких запросов к одному сайту используйте `requests.Session()` — она переиспользует соединение и общие заголовки.
- Фиксируйте зависимости: `pip freeze > requirements.txt`, установка — `pip install -r requirements.txt`. Документация: https://requests.readthedocs.io.
