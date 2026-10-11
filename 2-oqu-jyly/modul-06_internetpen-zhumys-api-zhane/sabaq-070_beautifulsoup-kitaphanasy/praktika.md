# Сабақ 70. Практикалық тапсырмалар: BeautifulSoup, веб-беттен деректер алу

> `pip install beautifulsoup4`. 1–3-тапсырмалар интернетсіз (HTML мәтінмен), 4-тен бастап — оқу сайты `https://books.toscrape.com` (скрапингті жаттықтыруға арнайы рұқсат етілген). Әр сұраныста `timeout`, беттер арасында `time.sleep(1)`!

## Базалық деңгей

### 1-тапсырма. Мектеп жаңалықтары
Төмендегі HTML-ден әр жаңалықтың тақырыбын, күнін, сілтемесін (толық URL, негізі `https://school.kz`) және тегтерін алып, сөздіктер тізімі ретінде шығарыңыз. Жарнама блогы (`class="ad"`) нәтижеге кірмеуі керек.

```python
NEWS_HTML = """
<div id="news">
  <article class="item">
    <h2><a href="/news/101">Информатикадан облыстық олимпиада</a></h2>
    <span class="date">20.02.2027</span>
    <ul class="tags"><li>олимпиада</li><li>информатика</li></ul>
  </article>
  <article class="item ad">
    <h2><a href="https://ads.kz">Жарнама: курстар</a></h2>
  </article>
  <article class="item">
    <h2><a href="/news/102">Python үйірмесінде жаңа жоба</a></h2>
    <span class="date">22.02.2027</span>
    <ul class="tags"><li>үйірме</li></ul>
  </article>
</div>
"""
```

```text
{'title': 'Информатикадан облыстық олимпиада', 'date': '20.02.2027', 'url': 'https://school.kz/news/101', 'tags': ['олимпиада', 'информатика']}
{'title': 'Python үйірмесінде жаңа жоба', 'date': '22.02.2027', 'url': 'https://school.kz/news/102', 'tags': ['үйірме']}
```

### 2-тапсырма. Кестені оқу
HTML кестесінен үйірме кестесін тізімге жинап, «Python» сөзі бар сабақтарды ғана шығарыңыз.

```python
TABLE_HTML = """
<table class="schedule">
  <thead><tr><th>Күні</th><th>Уақыты</th><th>Үйірме</th><th>Кабинет</th></tr></thead>
  <tbody>
    <tr><td>Дүйсенбі</td><td>15:00</td><td>Python бағдарламалау</td><td>204</td></tr>
    <tr><td>Сейсенбі</td><td>16:00</td><td>Робототехника</td><td>101</td></tr>
    <tr><td>Бейсенбі</td><td>15:00</td><td>Python бағдарламалау</td><td>204</td></tr>
    <tr><td>Жұма</td><td>14:00</td><td>3D модельдеу</td><td>305</td></tr>
  </tbody>
</table>
"""
```

```text
Дүйсенбі 15:00 — Python бағдарламалау (204-каб.)
Бейсенбі 15:00 — Python бағдарламалау (204-каб.)
```

### 3-тапсырма. `find` ↔ `select`
1-тапсырманы екі тәсілмен жазыңыз: тек `find`/`find_all` арқылы және тек `select`/`select_one` арқылы. Қайсысы қысқа? Қайсысы түсінікті?

### 4-тапсырма. Кітаптар каталогы
`https://books.toscrape.com/` басты бетінен 20 кітаптың атауын (толық — `title` атрибуты), бағасын (сан), рейтингін (1–5) және «қоймада бар ма» (`p.instock.availability`) деректерін жинаңыз. Нәтиже:
```text
Табылды: 20 кітап
Орташа баға: £..
Ең қымбат: <кітап атауы> (£..)
5★ кітаптар: ..
```
(Нақты сандарды бағдарламаңыз есептейді — сайттағы деректермен салыстырып тексеріңіз.)

### 5-тапсырма. CSV-ге сақтау
4-тапсырманың нәтижесін `books.csv` файлына (`csv.DictWriter`, `utf-8-sig`) сақтаңыз және Excel-де ашып тексеріңіз.

## Кеңейтілген деңгей

### 6-тапсырма. Пагинация
Алғашқы 5 беттен (`catalogue/page-1.html` … `page-5.html`) барлық кітапты жинаңыз — `li.next a` сілтемесі арқылы келесі бетке өтіңіз, `urljoin` қолданыңыз, беттер арасында 1 секунд кідіріс. Нәтиже: 100 кітап. Рейтинг бойынша кітап санын шығарыңыз.

### 7-тапсырма. Кітап беті
Бір кітаптың жеке бетіне өтіп (4-тапсырмадағы `url`), «Product Information» кестесінен (`table.table-striped`) UPC, бағасы, қоймадағы саны деректерін және кітап сипаттамасын (`#product_description` элементінен кейінгі `<p>`) алыңыз. Кеңес: `find_next_sibling("p")`.

### 8-тапсырма. Категориялар
Сол жақ мәзірдегі (`div.side_categories ul li ul li a`) барлық категория атаулары мен сілтемелерін алып, атауларын алфавит бойынша шығарыңыз. Қанша категория бар?

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Ең арзан «бестік»
Барлық 50 бетті аралап (кідіріспен!), 5★ рейтингі бар кітаптардың ішінен ең арзан 10-ын табыңыз. Нәтижені SQLite қорына (`books` кестесі) жазыңыз — V модульдегі білімді қолданыңыз. Бағдарлама қайта іске қосылғанда қор бар болса, сайтқа қайта сұраныс жібермей, қордан оқысын.

### 10-тапсырма. Әмбебап кесте скрапері
`scrape_table(url, selector)` функциясы кез келген беттегі көрсетілген HTML кестені (тақырыптарымен) сөздіктер тізіміне айналдырып, CSV-ге сақтасын. `https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html` бетіндегі кестемен тексеріңіз.

## Үй тапсырмасы

1. `books.toscrape.com` сайтының «Travel» категориясындағы барлық кітапты жинап, CSV-ге сақтаңыз.
2. Келесі сабаққа: `https://books.toscrape.com/robots.txt`, `https://www.wikipedia.org/robots.txt` файлдарын ашып, оларда не жазылғанын қарап келіңіз.
3. Ойланыңыз: кез келген сайтты скрапинг жасауға бола ма? Қандай шектеулер болуы мүмкін?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма (`find` стилі) және 3-тапсырма**
```python
from urllib.parse import urljoin
from bs4 import BeautifulSoup

NEWS_HTML = """
<div id="news">
  <article class="item">
    <h2><a href="/news/101">Информатикадан облыстық олимпиада</a></h2>
    <span class="date">20.02.2027</span>
    <ul class="tags"><li>олимпиада</li><li>информатика</li></ul>
  </article>
  <article class="item ad">
    <h2><a href="https://ads.kz">Жарнама: курстар</a></h2>
  </article>
  <article class="item">
    <h2><a href="/news/102">Python үйірмесінде жаңа жоба</a></h2>
    <span class="date">22.02.2027</span>
    <ul class="tags"><li>үйірме</li></ul>
  </article>
</div>
"""
BASE = "https://school.kz"
soup = BeautifulSoup(NEWS_HTML, "html.parser")

# find / find_all стилі
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

# select стилі
news2 = [{
    "title": a.select_one("h2 a").get_text(strip=True),
    "date": a.select_one(".date").get_text(strip=True),
    "url": urljoin(BASE, a.select_one("h2 a")["href"]),
    "tags": [li.get_text(strip=True) for li in a.select(".tags li")],
} for a in soup.select("#news article.item:not(.ad)")]
print(news == news2)
```

**2-тапсырма**
```python
from bs4 import BeautifulSoup

TABLE_HTML = """
<table class="schedule">
  <thead><tr><th>Күні</th><th>Уақыты</th><th>Үйірме</th><th>Кабинет</th></tr></thead>
  <tbody>
    <tr><td>Дүйсенбі</td><td>15:00</td><td>Python бағдарламалау</td><td>204</td></tr>
    <tr><td>Сейсенбі</td><td>16:00</td><td>Робототехника</td><td>101</td></tr>
    <tr><td>Бейсенбі</td><td>15:00</td><td>Python бағдарламалау</td><td>204</td></tr>
    <tr><td>Жұма</td><td>14:00</td><td>3D модельдеу</td><td>305</td></tr>
  </tbody>
</table>
"""
soup = BeautifulSoup(TABLE_HTML, "html.parser")
rows = [[td.get_text(strip=True) for td in tr.find_all("td")]
        for tr in soup.select("table.schedule tbody tr")]
for day, time_, club, room in rows:
    if "Python" in club:
        print(f"{day} {time_} — {club} ({room}-каб.)")
```

**4–5-тапсырмалар**
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

print("Табылды:", len(books), "кітап")
print(f"Орташа баға: £{sum(b['price'] for b in books) / len(books):.2f}")
top = max(books, key=lambda b: b["price"])
print(f"Ең қымбат: {top['title']} (£{top['price']:.2f})")
print("5★ кітаптар:", sum(b["rating"] == 5 for b in books))

with open("books.csv", "w", newline="", encoding="utf-8-sig") as f:
    writer = csv.DictWriter(f, fieldnames=list(books[0].keys()))
    writer.writeheader()
    writer.writerows(books)
```

**6-тапсырма**
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
print("Барлығы:", len(books))
for stars, n in sorted(Counter(b["rating"] for b in books).items()):
    print(f"{stars}★: {n}")
```

**7-тапсырма**
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
print("Бағасы:", info["Price (incl. tax)"])
print("Қоймада:", info["Availability"])
desc_header = soup.find(id="product_description")
if desc_header:
    print("Сипаттама:", desc_header.find_next_sibling("p").get_text(strip=True)[:200], "...")
```

**8-тапсырма**
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
print("Категория саны:", len(cats))
```

**9-тапсырма** (қысқартылған үлгі)
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

**10-тапсырма**
```python
import csv
import requests
from bs4 import BeautifulSoup

def scrape_table(url, selector, out="table.csv"):
    soup = BeautifulSoup(requests.get(url, timeout=10).text, "html.parser")
    table = soup.select_one(selector)
    if table is None:
        raise ValueError("Кесте табылмады: " + selector)
    rows = table.find_all("tr")
    headers = [c.get_text(strip=True) for c in rows[0].find_all(["th", "td"])]
    data = []
    if table.find("thead") or rows[0].find("th") and not rows[0].find("td"):
        body = rows[1:]
    else:                                        # тақырып жолы жоқ, th — жол атауы
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
