# Урок 65. Практические задания: библиотека requests

> Сначала создайте виртуальное окружение и выполните `pip install requests`. Задавайте `timeout` для всех запросов!

## Базовый уровень

### Задание 1. Исследование ответа
Отправьте GET-запрос по адресу `https://httpbin.org/get` и выведите следующую информацию: код состояния, его описание (`reason`), `Content-Type`, время ответа (в миллисекундах), длину тела.

```text
Состояние: 200 OK
Тип: application/json
Время: 412 мс
Тело: 305 символов
```

### Задание 2. Карточка пользователя
Запросите у пользователя ID от 1 до 10 и получите данные `https://jsonplaceholder.typicode.com/users/<id>`. Выведите имя, email, город и название компании в виде красивой карточки.

```text
ID (1-10): 3
┌──────────────────────────────┐
  Имя:      Clementine Bauch
  Email:    Nathan@yesenia.net
  Город:    McKenziehaven
  Компания: Romaguera-Jacobson
└──────────────────────────────┘
```

### Задание 3. Список задач
Получите задачи первого пользователя со страницы `https://jsonplaceholder.typicode.com/todos` через `params={"userId": 1}`. Выведите выполненные со знаком ✓, невыполненные — со знаком ✗, а в конце дайте статистику.

```text
✓ delectus aut autem
✗ quis ut nam facilis et officia qui
...
Выполнено: 11 / 20 (55%)
```

### Задание 4. Безопасный запрос
Напишите функцию `safe_get_json()` из теории и проверьте её в 4 ситуациях:
- `https://jsonplaceholder.typicode.com/posts/1` — успешно;
- `https://jsonplaceholder.typicode.com/posts/0` — 404;
- `https://httpbin.org/delay/5` (`timeout=2`) — время истекло;
- `https://joq-sait-12345.kz` — ошибка подключения.

### Задание 5. Скачивание картинок
Скачайте случайные картинки с адреса `https://picsum.photos/400/300` и сохраните их под именами `photo_1.jpg`, `photo_2.jpg`, `photo_3.jpg` (в цикле, с `time.sleep(1)` между запросами). Выведите размер каждого файла в КБ.

## Продвинутый уровень

### Задание 6. Погода — первый шаг
Получите из Open-Meteo API (`https://api.open-meteo.com/v1/forecast`, параметры: `latitude`, `longitude`, `current_weather=true`) текущую температуру и скорость ветра в городах Алматы (43.25, 76.95), Астана (51.17, 71.45), Шымкент (42.32, 69.59) и выведите в виде таблицы.

```text
Город       Темп., °C   Ветер, км/ч
Алматы          -2.4          5.1
Астана         -14.8         18.0
Шымкент          3.1          7.6
```

### Задание 7. «Пульс» сайтов
Прочитайте список URL из файла `sites.txt`, отправьте запрос на каждый и запишите результат в файл `status.csv`: URL, код состояния, время (мс), дата и время проверки. Для недоступного сайта код — `ERROR`.

### Задание 8. Самый длинный пост
Получите 100 постов со страницы `https://jsonplaceholder.typicode.com/posts` и выведите пост с самым длинным телом и количество постов каждого пользователя (`userId`).

## ★ Олимпиадная / творческая задача

### Задание 9. Загрузчик с индикатором прогресса
Напишите функцию, которая скачивает файл через `stream=True` и `iter_content` и обновляет в одной строке процент загрузки по заголовку `Content-Length` (`print(..., end="\r")`).

```text
Загрузка: [##########----------] 50%
```

### Задание 10. Повторная попытка (retry)
Функция `get_with_retry(url, attempts=3)` при неудачном запросе (ошибка подключения или код 5xx) должна ждать 1, 2, 4 секунды (экспоненциальная задержка) и отправлять запрос снова. Если все попытки неудачны — `None`. Проверьте через `https://httpbin.org/status/503`.

## Домашнее задание

1. Загрузите на GitHub папку проекта с файлом `requirements.txt` (`.venv` — в `.gitignore`!).
2. Используя данные `https://jsonplaceholder.typicode.com/albums` и `/photos`, скачайте первые 5 фотографий (`thumbnailUrl`) первого альбома.
3. Подумайте: какие известные вам приложения получают данные от внешних сервисов? Напишите 3 примера.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import requests

r = requests.get("https://httpbin.org/get", timeout=10)
print(f"Состояние: {r.status_code} {r.reason}")
print("Тип:", r.headers.get("Content-Type"))
print(f"Время: {r.elapsed.total_seconds() * 1000:.0f} мс")
print(f"Тело: {len(r.text)} символов")
```

**Задание 2**
```python
import requests

uid = int(input("ID (1-10): "))
r = requests.get(f"https://jsonplaceholder.typicode.com/users/{uid}", timeout=10)
if r.ok:
    u = r.json()
    print("┌" + "─" * 30 + "┐")
    print(f"  Имя:      {u['name']}")
    print(f"  Email:    {u['email']}")
    print(f"  Город:    {u['address']['city']}")
    print(f"  Компания: {u['company']['name']}")
    print("└" + "─" * 30 + "┘")
else:
    print("Пользователь не найден, код:", r.status_code)
```

**Задание 3**
```python
import requests

r = requests.get("https://jsonplaceholder.typicode.com/todos", params={"userId": 1}, timeout=10)
todos = r.json()
done = 0
for t in todos:
    mark = "✓" if t["completed"] else "✗"
    done += t["completed"]
    print(mark, t["title"])
print(f"Выполнено: {done} / {len(todos)} ({done / len(todos):.0%})")
```

**Задание 4**
```python
import requests

def safe_get_json(url, timeout=10):
    try:
        r = requests.get(url, timeout=timeout)
        r.raise_for_status()
        return r.json()
    except requests.exceptions.Timeout:
        print("Ошибка: время истекло")
    except requests.exceptions.ConnectionError:
        print("Ошибка: подключение невозможно")
    except requests.exceptions.HTTPError as e:
        print("Ошибка HTTP:", e.response.status_code)
    except requests.exceptions.JSONDecodeError:
        print("Ошибка: не JSON")
    return None

print(safe_get_json("https://jsonplaceholder.typicode.com/posts/1"))
safe_get_json("https://jsonplaceholder.typicode.com/posts/0")
safe_get_json("https://httpbin.org/delay/5", timeout=2)
safe_get_json("https://joq-sait-12345.kz")
```

**Задание 5**
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

**Задание 6**
```python
import requests

cities = {"Алматы": (43.25, 76.95), "Астана": (51.17, 71.45), "Шымкент": (42.32, 69.59)}
print(f"{'Город':10} {'Темп., °C':>10} {'Ветер, км/ч':>12}")
for name, (lat, lon) in cities.items():
    r = requests.get("https://api.open-meteo.com/v1/forecast",
                     params={"latitude": lat, "longitude": lon, "current_weather": "true"},
                     timeout=10)
    cw = r.json()["current_weather"]
    print(f"{name:10} {cw['temperature']:>10} {cw['windspeed']:>12}")
```

**Задание 7**
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
print("status.csv готов")
```

**Задание 8**
```python
from collections import Counter
import requests

posts = requests.get("https://jsonplaceholder.typicode.com/posts", timeout=10).json()
longest = max(posts, key=lambda p: len(p["body"]))
print("Самый длинный пост:", longest["id"], longest["title"], len(longest["body"]), "символов")
for uid, n in sorted(Counter(p["userId"] for p in posts).items()):
    print(f"Пользователь {uid}: постов — {n}")
```

**Задание 9**
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
                    print(f"Загрузка: [{'#' * part}{'-' * (20 - part)}] {done * 100 // total}%", end="\r")
    print("\nГотово:", path)

download_with_progress("https://www.python.org/static/img/python-logo.png", "logo.png")
```

**Задание 10**
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
            print(f"Попытка {i}: ошибка сервера {r.status_code}")
        except requests.exceptions.ConnectionError:
            print(f"Попытка {i}: ошибка подключения")
        if i < attempts:
            time.sleep(delay)
            delay *= 2
    return None

print(get_with_retry("https://httpbin.org/status/503"))
```

</details>
