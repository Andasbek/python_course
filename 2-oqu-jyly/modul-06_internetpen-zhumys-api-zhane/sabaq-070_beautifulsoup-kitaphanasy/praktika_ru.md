# Урок 70. Практические задания: BeautifulSoup, получение данных с веб-страницы

> `pip install beautifulsoup4`. Задания 1–3 — без интернета (с HTML-текстом), начиная с 4-го — учебный сайт `https://books.toscrape.com` (специально разрешён для тренировки скрапинга). В каждом запросе — `timeout`, между страницами — `time.sleep(1)`!

## Базовый уровень

### Задание 1. Школьные новости
Получите из HTML ниже заголовок, дату, ссылку (полный URL, основа `https://school.kz`) и теги каждой новости и выведите в виде списка словарей. Рекламный блок (`class="ad"`) не должен попасть в результат.

```python
NEWS_HTML = """
<div id="news">
  <article class="item">
    <h2><a href="/news/101">Областная олимпиада по информатике</a></h2>
    <span class="date">20.02.2027</span>
    <ul class="tags"><li>олимпиада</li><li>информатика</li></ul>
  </article>
  <article class="item ad">
    <h2><a href="https://ads.kz">Реклама: курсы</a></h2>
  </article>
  <article class="item">
    <h2><a href="/news/102">Новый проект в кружке Python</a></h2>
    <span class="date">22.02.2027</span>
    <ul class="tags"><li>кружок</li></ul>
  </article>
</div>
"""
```

```text
{'title': 'Областная олимпиада по информатике', 'date': '20.02.2027', 'url': 'https://school.kz/news/101', 'tags': ['олимпиада', 'информатика']}
{'title': 'Новый проект в кружке Python', 'date': '22.02.2027', 'url': 'https://school.kz/news/102', 'tags': ['кружок']}
```

### Задание 2. Чтение таблицы
Соберите расписание кружков из HTML-таблицы в список и выведите только занятия, в названии которых есть слово «Python».

```python
TABLE_HTML = """
<table class="schedule">
  <thead><tr><th>День</th><th>Время</th><th>Кружок</th><th>Кабинет</th></tr></thead>
  <tbody>
    <tr><td>Понедельник</td><td>15:00</td><td>Программирование на Python</td><td>204</td></tr>
    <tr><td>Вторник</td><td>16:00</td><td>Робототехника</td><td>101</td></tr>
    <tr><td>Четверг</td><td>15:00</td><td>Программирование на Python</td><td>204</td></tr>
    <tr><td>Пятница</td><td>14:00</td><td>3D-моделирование</td><td>305</td></tr>
  </tbody>
</table>
"""
```

```text
Понедельник 15:00 — Программирование на Python (каб. 204)
Четверг 15:00 — Программирование на Python (каб. 204)
```

### Задание 3. `find` ↔ `select`
Напишите задание 1 двумя способами: только через `find`/`find_all` и только через `select`/`select_one`. Какой короче? Какой понятнее?

### Задание 4. Каталог книг
Соберите с главной страницы `https://books.toscrape.com/` для 20 книг: название (полное — атрибут `title`), цену (число), рейтинг (1–5) и «есть ли на складе» (`p.instock.availability`). Результат:
```text
Найдено книг: 20
Средняя цена: £..
Самая дорогая: <название книги> (£..)
Книг с 5★: ..
```
(Конкретные числа вычисляет ваша программа — проверьте, сравнив с данными на сайте.)

### Задание 5. Сохранение в CSV
Сохраните результат задания 4 в файл `books.csv` (`csv.DictWriter`, `utf-8-sig`) и проверьте, открыв в Excel.

## Продвинутый уровень

### Задание 6. Пагинация
Соберите все книги с первых 5 страниц (`catalogue/page-1.html` … `page-5.html`) — переходите на следующую страницу по ссылке `li.next a`, используйте `urljoin`, между страницами пауза 1 секунда. Результат: 100 книг. Выведите количество книг по рейтингу.

### Задание 7. Страница книги
Перейдите на отдельную страницу книги (`url` из задания 4) и получите из таблицы «Product Information» (`table.table-striped`) UPC, цену, количество на складе, а также описание книги (`<p>` после элемента `#product_description`). Подсказка: `find_next_sibling("p")`.

### Задание 8. Категории
Получите названия и ссылки всех категорий в левом меню (`div.side_categories ul li ul li a`) и выведите названия в алфавитном порядке. Сколько всего категорий?

## ★ Олимпиадная / творческая задача

### Задание 9. Самая дешёвая «пятёрка»
Обойдите все 50 страниц (с паузами!) и найдите среди книг с рейтингом 5★ 10 самых дешёвых. Запишите результат в базу SQLite (таблица `books`) — примените знания из V модуля. При повторном запуске программы, если база существует, данные читаются из базы без повторных запросов к сайту.

### Задание 10. Универсальный скрапер таблиц
Функция `scrape_table(url, selector)` должна превращать указанную HTML-таблицу любой страницы (с заголовками) в список словарей и сохранять в CSV. Проверьте на таблице страницы `https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html`.

## Домашнее задание

1. Соберите все книги категории «Travel» сайта `books.toscrape.com` и сохраните в CSV.
2. К следующему уроку: откройте файлы `https://books.toscrape.com/robots.txt`, `https://www.wikipedia.org/robots.txt` и посмотрите, что в них написано.
3. Подумайте: можно ли скрапить любой сайт? Какие могут быть ограничения?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1 (стиль `find`) и задание 3**
```python
from urllib.parse import urljoin
from bs4 import BeautifulSoup

NEWS_HTML = """
<div id="news">
  <article class="item">
    <h2><a href="/news/101">Областная олимпиада по информатике</a></h2>
    <span class="date">20.02.2027</span>
    <ul class="tags"><li>олимпиада</li><li>информатика</li></ul>
  </article>
  <article class="item ad">
    <h2><a href="https://ads.kz">Реклама: курсы</a></h2>
  </article>
  <article class="item">
    <h2><a href="/news/102">Новый проект в кружке Python</a></h2>
    <span class="date">22.02.2027</span>
    <ul class="tags"><li>кружок</li></ul>
  </article>
</div>
"""
BASE = "https://school.kz"
soup = BeautifulSoup(NEWS_HTML, "html.parser")

# стиль find / find_all
news = []
for art in soup.find("div", id="news").find_all("article", class_="item"):
    if "ad" in art["class"]:
        continue
    link = art.find("h2").find("a")
    news.append({
        "title": link.get_text(strip=True),
        "date": art.find("span", class_="date").get_text(strip=True),
        "url": urljoin(BASE, link["href"]),
        "tags": [li.get_text(strip=True) for li in art.find("ul", class_="tags").find_all("li")],
    })
for n in news:
    print(n)

# стиль select
news2 = [{
    "title": a.select_one("h2 a").get_text(strip=True),
    "date": a.select_one(".date").get_text(strip=True),
    "url": urljoin(BASE, a.select_one("h2 a")["href"]),
    "tags": [li.get_text(strip=True) for li in a.select(".tags li")],
} for a in soup.select("#news article.item:not(.ad)")]
print(news == news2)
```

**Задание 2**
```python
from bs4 import BeautifulSoup

TABLE_HTML = """
<table class="schedule">
  <thead><tr><th>День</th><th>Время</th><th>Кружок</th><th>Кабинет</th></tr></thead>
  <tbody>
    <tr><td>Понедельник</td><td>15:00</td><td>Программирование на Python</td><td>204</td></tr>
    <tr><td>Вторник</td><td>16:00</td><td>Робототехника</td><td>101</td></tr>
    <tr><td>Четверг</td><td>15:00</td><td>Программирование на Python</td><td>204</td></tr>
    <tr><td>Пятница</td><td>14:00</td><td>3D-моделирование</td><td>305</td></tr>
  </tbody>
</table>
"""
soup = BeautifulSoup(TABLE_HTML, "html.parser")
rows = [[td.get_text(strip=True) for td in tr.find_all("td")]
        for tr in soup.select("table.schedule tbody tr")]
for day, time_, club, room in rows:
    if "Python" in club:
        print(f"{day} {time_} — {club} (каб. {room})")
```

**Задания 4–5**
```python
import csv
from urllib.parse import urljoin

import requests
from bs4 import BeautifulSoup

URL = "https://books.toscrape.com/"
RATING = {"One": 1, "Two": 2, "Three": 3, "Four": 4, "Five": 5}

r = requests.get(URL, headers={"User-Agent": "PythonClub-Almaty/1.0"}, timeout=10)
r.raise_for_status()
r.encoding = "utf-8"
soup = BeautifulSoup(r.text, "html.parser")

books = []
for card in soup.select("article.product_pod"):
    link = card.select_one("h3 a")
    books.append({
        "title": link["title"],
        "price": float(card.select_one(".price_color").get_text(strip=True).lstrip("£")),
        "rating": RATING[card.select_one("p.star-rating")["class"][1]],
        "in_stock": "In stock" in card.select_one("p.instock.availability").get_text(),
        "url": urljoin(URL, link["href"]),
    })

print("Найдено книг:", len(books))
print(f"Средняя цена: £{sum(b['price'] for b in books) / len(books):.2f}")
top = max(books, key=lambda b: b["price"])
print(f"Самая дорогая: {top['title']} (£{top['price']:.2f})")
print("Книг с 5★:", sum(b["rating"] == 5 for b in books))

with open("books.csv", "w", newline="", encoding="utf-8-sig") as f:
    writer = csv.DictWriter(f, fieldnames=list(books[0].keys()))
    writer.writeheader()
    writer.writerows(books)
```

**Задание 6**
```python
import time
from collections import Counter
from urllib.parse import urljoin

import requests
from bs4 import BeautifulSoup

RATING = {"One": 1, "Two": 2, "Three": 3, "Four": 4, "Five": 5}
url = "https://books.toscrape.com/catalogue/page-1.html"
books = []
for page in range(5):
    soup = BeautifulSoup(requests.get(url, timeout=10).text, "html.parser")
    for card in soup.select("article.product_pod"):
        books.append({"title": card.select_one("h3 a")["title"],
                      "rating": RATING[card.select_one("p.star-rating")["class"][1]]})
    nxt = soup.select_one("li.next a")
    if not nxt:
        break
    url = urljoin(url, nxt["href"])
    time.sleep(1)
print("Всего:", len(books))
for stars, n in sorted(Counter(b["rating"] for b in books).items()):
    print(f"{stars}★: {n}")
```

**Задание 7**
```python
import requests
from bs4 import BeautifulSoup

url = "https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html"
r = requests.get(url, timeout=10)
r.encoding = "utf-8"
soup = BeautifulSoup(r.text, "html.parser")
info = {tr.th.get_text(strip=True): tr.td.get_text(strip=True)
        for tr in soup.select("table.table-striped tr")}
print("UPC:", info["UPC"])
print("Цена:", info["Price (incl. tax)"])
print("На складе:", info["Availability"])
desc_header = soup.find(id="product_description")
if desc_header:
    print("Описание:", desc_header.find_next_sibling("p").get_text(strip=True)[:200], "...")
```

**Задание 8**
```python
from urllib.parse import urljoin
import requests
from bs4 import BeautifulSoup

URL = "https://books.toscrape.com/"
soup = BeautifulSoup(requests.get(URL, timeout=10).text, "html.parser")
cats = {a.get_text(strip=True): urljoin(URL, a["href"])
        for a in soup.select("div.side_categories ul li ul li a")}
for name in sorted(cats):
    print(name)
print("Количество категорий:", len(cats))
```

**Задание 9** (сокращённый образец)
```python
import sqlite3
import time
from pathlib import Path
from urllib.parse import urljoin

import requests
from bs4 import BeautifulSoup

DB = Path("books.db")

def scrape_five_star():
    url, found = "https://books.toscrape.com/catalogue/page-1.html", []
    while url:
        soup = BeautifulSoup(requests.get(url, timeout=10).text, "html.parser")
        for card in soup.select("article.product_pod"):
            if "Five" in card.select_one("p.star-rating")["class"]:
                found.append((card.select_one("h3 a")["title"],
                              float(card.select_one(".price_color").get_text().lstrip("£"))))
        nxt = soup.select_one("li.next a")
        url = urljoin(url, nxt["href"]) if nxt else None
        time.sleep(1)
    return found

if not DB.exists():
    rows = scrape_five_star()
    with sqlite3.connect(DB) as conn:
        conn.execute("CREATE TABLE books (title TEXT, price REAL)")
        conn.executemany("INSERT INTO books VALUES (?, ?)", rows)
with sqlite3.connect(DB) as conn:
    for title, price in conn.execute("SELECT title, price FROM books ORDER BY price LIMIT 10"):
        print(f"£{price:6.2f}  {title}")
```

**Задание 10**
```python
import csv
import requests
from bs4 import BeautifulSoup

def scrape_table(url, selector, out="table.csv"):
    soup = BeautifulSoup(requests.get(url, timeout=10).text, "html.parser")
    table = soup.select_one(selector)
    if table is None:
        raise ValueError("Таблица не найдена: " + selector)
    rows = table.find_all("tr")
    headers = [c.get_text(strip=True) for c in rows[0].find_all(["th", "td"])]
    data = []
    if table.find("thead") or rows[0].find("th") and not rows[0].find("td"):
        body = rows[1:]
    else:                                        # строки заголовков нет, th — название строки
        headers, body = ["key", "value"], rows
    for tr in body:
        cells = [c.get_text(strip=True) for c in tr.find_all(["th", "td"])]
        data.append(dict(zip(headers, cells)))
    with open(out, "w", newline="", encoding="utf-8-sig") as f:
        w = csv.DictWriter(f, fieldnames=headers)
        w.writeheader()
        w.writerows(data)
    return data

print(scrape_table("https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html",
                   "table.table-striped"))
```

</details>
