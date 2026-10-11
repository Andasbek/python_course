# Сабақ 70. BeautifulSoup кітапханасы. Веб-беттен деректерді алу.

## Тақырыптар

1. Веб-скрапинг деген не?
2. Орнату және алғашқы «сорпа»
3. `find()` және `find_all()`
4. Мәтін мен атрибуттарды алу
5. CSS-селекторлар: `select()` және `select_one()`
6. Ағашта навигация
7. Кестелерді талдау
8. Нақты сайт: `books.toscrape.com`
9. Нәтижені сақтау
10. Жиі кездесетін қателер
11. Негізгі ұғымдар

## 1. Веб-скрапинг деген не?

**Веб-скрапинг (web scraping)** — веб-беттердің HTML кодынан деректерді бағдарлама арқылы автоматты түрде жинау.

| | API | Веб-скрапинг |
|---|---|---|
| Деректер форматы | Құрылымды JSON | HTML (адамға арналған бет) |
| Тұрақтылық | Жоғары (құжатталған) | Төмен (сайт дизайны өзгерсе — код бұзылады) |
| Рұқсат | Ресми түрде берілген | Сайт ережелеріне байланысты |
| Қашан | API бар болса — **әрқашан бірінші** | API жоқ болса |

**Ереже:** API бар болса — API қолданамыз. Скрапинг — екінші жол.

Скрапинг кезеңдері:
```text
1. requests.get(url)  →  2. BeautifulSoup(html)  →  3. find/select  →  4. get_text / ["href"]  →  5. сақтау (CSV/JSON/SQLite)
   бетті жүктеу            ағашқа айналдыру          элементтерді табу      деректерді алу           
```

## 2. Орнату және алғашқы «сорпа»

```bash
pip install beautifulsoup4
```

Кітапхана атауы — `beautifulsoup4`, ал импорт — `bs4`. Атауы «Алиса ғажайыптар елінде» кітабындағы «Әдемі сорпа» өлеңінен алынған.

Алдымен интернетсіз, HTML мәтінмен жаттығамыз:

```python
from bs4 import BeautifulSoup

html = """
<html><head><title>Кітап дүкені</title></head>
<body>
  <h1 id="top">Кітаптар</h1>
  <div class="book">
    <h3 class="title"><a href="/books/1">Абай жолы</a></h3>
    <span class="author">Мұхтар Әуезов</span>
    <span class="price">4500 ₸</span>
  </div>
  <div class="book sale">
    <h3 class="title"><a href="/books/2">Көшпенділер</a></h3>
    <span class="author">Ілияс Есенберлин</span>
    <span class="price">6000 ₸</span>
  </div>
  <div class="book">
    <h3 class="title"><a href="/books/3">Қан мен тер</a></h3>
    <span class="author">Әбдіжәміл Нұрпейісов</span>
    <span class="price">5200 ₸</span>
  </div>
</body></html>
"""

soup = BeautifulSoup(html, "html.parser")
print(soup.title)              # бірінші <title> элементі
print(soup.title.string)       # оның мәтіні
print(soup.h1.get_text())      # бірінші <h1> мәтіні
```

Нәтижесі:
```text
<title>Кітап дүкені</title>
Кітап дүкені
Кітаптар
```

`"html.parser"` — Python-ға кірістірілген талдағыш (parser), қосымша орнатуды қажет етпейді. Жылдамырақ `lxml` талдағышы да бар (`pip install lxml`).

## 3. `find()` және `find_all()`

| Әдіс | Қайтарады | Табылмаса |
|---|---|---|
| `find(...)` | Бірінші сәйкес элемент (`Tag`) | `None` |
| `find_all(...)` | Барлық сәйкес элементтер тізімі | `[]` |

```python
from bs4 import BeautifulSoup

html = """<div class="book"><h3 class="title"><a href="/books/1">Абай жолы</a></h3>
<span class="author">Мұхтар Әуезов</span><span class="price">4500 ₸</span></div>
<div class="book sale"><h3 class="title"><a href="/books/2">Көшпенділер</a></h3>
<span class="author">Ілияс Есенберлин</span><span class="price">6000 ₸</span></div>"""
soup = BeautifulSoup(html, "html.parser")

first_price = soup.find("span", class_="price")       # class — Python-да кілт сөз, сондықтан class_
print(first_price.get_text())

books = soup.find_all("div", class_="book")
print("Кітап саны:", len(books))

sale = soup.find("div", class_="sale")                 # бірнеше класс болса да біреуі бойынша табады
print("Жеңілдіктегі:", sale.find("a").get_text())

print(soup.find("table"))                              # жоқ элемент → None
```

Нәтижесі:
```text
4500 ₸
Кітап саны: 2
Жеңілдіктегі: Көшпенділер
None
```

Іздеу тәсілдері:

```python
# soup.find_all("a")                          — тег бойынша
# soup.find_all(["h1", "h2"])                 — бірнеше тег
# soup.find(id="top")                         — id бойынша
# soup.find_all("span", class_="price")       — тег + класс
# soup.find_all("a", href=True)               — href атрибуты бар сілтемелер
# soup.find_all("div", attrs={"data-id": "5"})— кез келген атрибут
# soup.find_all("li", limit=3)                — тек алғашқы 3
```

**Маңызды:** `find()` / `find_all()` әдістерін кез келген элементтен шақыруға болады — сонда іздеу тек сол элементтің **ішінде** жүреді. Бұл — скрапингтің негізгі тәсілі: алдымен «карточкаларды» табамыз, сосын әр карточканың ішінен деректерді аламыз.

## 4. Мәтін мен атрибуттарды алу

```python
from bs4 import BeautifulSoup

soup = BeautifulSoup('<a href="/books/1" title="Толық атауы"> \n Абай жолы \n</a>', "html.parser")
link = soup.find("a")

print(repr(link.get_text()))            # бос орындармен
print(link.get_text(strip=True))        # бос орындарсыз
print(link["href"])                     # атрибут (жоқ болса — KeyError)
print(link.get("title"))                # атрибут (жоқ болса — None)
print(link.get("target", "жоқ"))
print(link.attrs)                       # барлық атрибуттар сөздігі
```

Нәтижесі:
```text
' \n Абай жолы \n'
Абай жолы
/books/1
Толық атауы
жоқ
{'href': '/books/1', 'title': 'Толық атауы'}
```

**`class` атрибуты тізім ретінде** қайтарылады, себебі элементте бірнеше класс болуы мүмкін: `tag["class"]` → `['book', 'sale']`.

### Үлгі: карточкалардан деректер жинау

```python
from bs4 import BeautifulSoup

html = """<div class="book"><h3 class="title"><a href="/books/1">Абай жолы</a></h3>
<span class="author">Мұхтар Әуезов</span><span class="price">4500 ₸</span></div>
<div class="book sale"><h3 class="title"><a href="/books/2">Көшпенділер</a></h3>
<span class="author">Ілияс Есенберлин</span><span class="price">6000 ₸</span></div>"""
soup = BeautifulSoup(html, "html.parser")

books = []
for card in soup.find_all("div", class_="book"):
    title_link = card.find("h3", class_="title").find("a")
    price_text = card.find("span", class_="price").get_text(strip=True)
    books.append({
        "title": title_link.get_text(strip=True),
        "author": card.find("span", class_="author").get_text(strip=True),
        "price": int(price_text.replace("₸", "").replace(" ", "")),   # «4500 ₸» → 4500
        "url": title_link["href"],
        "sale": "sale" in card["class"],
    })

for b in books:
    print(b)
```

Нәтижесі:
```text
{'title': 'Абай жолы', 'author': 'Мұхтар Әуезов', 'price': 4500, 'url': '/books/1', 'sale': False}
{'title': 'Көшпенділер', 'author': 'Ілияс Есенберлин', 'price': 6000, 'url': '/books/2', 'sale': True}
```

Скрапингтің нәтижесі — бізге таныс **сөздіктер тізімі**. Оны CSV-ге, JSON-ға немесе SQLite-қа сақтай аламыз (V модуль).

## 5. CSS-селекторлар: `select()` және `select_one()`

CSS-селекторларды тікелей қолдануға болады — көбіне бұл қысқарақ:

| `find` стилі | `select` стилі |
|---|---|
| `soup.find_all("div", class_="book")` | `soup.select("div.book")` |
| `soup.find(id="top")` | `soup.select_one("#top")` |
| `card.find("h3").find("a")` | `card.select_one("h3 a")` |
| — | `soup.select("div.book > span.price")` |
| — | `soup.select("table tr td:nth-of-type(2)")` |

```python
from bs4 import BeautifulSoup

html = """<div class="book"><h3><a href="/b/1">Абай жолы</a></h3><span class="price">4500 ₸</span></div>
<div class="book sale"><h3><a href="/b/2">Көшпенділер</a></h3><span class="price">6000 ₸</span></div>"""
soup = BeautifulSoup(html, "html.parser")

for a in soup.select("div.book h3 a"):
    print(a.get_text(), "→", a["href"])
print("Жеңілдік бағасы:", soup.select_one(".book.sale .price").get_text())
```

Нәтижесі:
```text
Абай жолы → /b/1
Көшпенділер → /b/2
Жеңілдік бағасы: 6000 ₸
```

**Кеңес:** DevTools-та элементті оң батырмамен басып → Copy → Copy selector — бірақ ол ұзын әрі «нәзік» болуы мүмкін (`body > div:nth-child(3) > ...`). Өзіңіз қысқа, класқа негізделген селектор жазған дұрыс.

## 6. Ағашта навигация

```python
from bs4 import BeautifulSoup

soup = BeautifulSoup("<ul><li>Python</li><li>SQL</li><li>Git</li></ul>", "html.parser")
second = soup.find_all("li")[1]

print(second.parent.name)                         # ата-ана
print(second.find_previous_sibling("li").text)    # алдыңғы бауыр
print(second.find_next_sibling("li").text)        # келесі бауыр
print([li.text for li in soup.ul.find_all("li")]) # балалар
```

Нәтижесі:
```text
ul
Python
Git
['Python', 'SQL', 'Git']
```

## 7. Кестелерді талдау

```python
from bs4 import BeautifulSoup

html = """<table id="cities">
<tr><th>Қала</th><th>Облыс</th><th>Халқы, мың</th></tr>
<tr><td>Алматы</td><td>—</td><td>2 200</td></tr>
<tr><td>Қарағанды</td><td>Қарағанды</td><td>500</td></tr>
<tr><td>Ақтөбе</td><td>Ақтөбе</td><td>580</td></tr>
</table>"""
soup = BeautifulSoup(html, "html.parser")
table = soup.find("table", id="cities")

headers = [th.get_text(strip=True) for th in table.find_all("th")]
rows = []
for tr in table.find_all("tr")[1:]:                # тақырып жолын өткіземіз
    cells = [td.get_text(strip=True) for td in tr.find_all("td")]
    rows.append(dict(zip(headers, cells)))

for r in sorted(rows, key=lambda r: int(r["Халқы, мың"].replace(" ", "")), reverse=True):
    print(r["Қала"], r["Халқы, мың"])
```

Нәтижесі:
```text
Алматы 2 200
Ақтөбе 580
Қарағанды 500
```

## 8. Нақты сайт: `books.toscrape.com`

```python
import requests
from bs4 import BeautifulSoup
from urllib.parse import urljoin

URL = "https://books.toscrape.com/"
HEADERS = {"User-Agent": "PythonClub-Almaty/1.0 (oqu maqsatynda)"}

r = requests.get(URL, headers=HEADERS, timeout=10)
r.raise_for_status()
r.encoding = "utf-8"                              # кодировканы нақты көрсетеміз
soup = BeautifulSoup(r.text, "html.parser")

RATING = {"One": 1, "Two": 2, "Three": 3, "Four": 4, "Five": 5}
books = []
for card in soup.select("article.product_pod"):
    link = card.select_one("h3 a")
    books.append({
        "title": link["title"],                                   # толық атауы атрибутта
        "price": float(card.select_one(".price_color").get_text().replace("£", "")),
        "rating": RATING[card.select_one("p.star-rating")["class"][1]],
        "url": urljoin(URL, link["href"]),                        # салыстырмалы → толық
    })

print("Табылды:", len(books))
for b in books[:3]:
    print(f"{b['rating']}★ £{b['price']:.2f}  {b['title']}")
```

Нәтижесі:
```text
Табылды: 20
3★ £51.77  A Light in the Attic
1★ £53.74  Tipping the Velvet
1★ £50.10  Soumission
```

### Бірнеше бетті аралау (пагинация)

Бетте «next» сілтемесі болса, оған өтіп, жинауды жалғастырамыз:

```python
import time
import requests
from bs4 import BeautifulSoup
from urllib.parse import urljoin

def scrape_pages(start_url, max_pages=3):
    url, all_titles = start_url, []
    for page in range(1, max_pages + 1):
        r = requests.get(url, timeout=10)
        soup = BeautifulSoup(r.text, "html.parser")
        all_titles += [a["title"] for a in soup.select("article.product_pod h3 a")]
        print(f"{page}-бет: барлығы {len(all_titles)} кітап")
        next_link = soup.select_one("li.next a")
        if next_link is None:                     # соңғы бет
            break
        url = urljoin(url, next_link["href"])
        time.sleep(1)                             # серверге әдептілік: 1 секунд үзіліс
    return all_titles

# titles = scrape_pages("https://books.toscrape.com/catalogue/page-1.html")
```

`time.sleep(1)` — серверді шамадан тыс жүктемеу үшін.

## 9. Нәтижені сақтау

```python
import csv

books = [{"title": "Абай жолы", "price": 4500}, {"title": "Көшпенділер", "price": 6000}]
with open("books.csv", "w", newline="", encoding="utf-8-sig") as f:
    writer = csv.DictWriter(f, fieldnames=["title", "price"])
    writer.writeheader()
    writer.writerows(books)
print("books.csv сақталды")
```

## Жиі кездесетін қателер

**1. `AttributeError: 'NoneType' object has no attribute 'get_text'`** — `find()` ештеңе таппады (селектор қате немесе бұл карточкада элемент жоқ). Тексеру:
```python
from bs4 import BeautifulSoup
card = BeautifulSoup('<div class="book"><h3>Атау</h3></div>', "html.parser")
price = card.find("span", class_="price")
print(price.get_text(strip=True) if price else "баға көрсетілмеген")
```

**2. `class` орнына `class_`** жазылмаса — `SyntaxError`, себебі `class` — Python кілт сөзі.

**3. `find_all` нәтижесінен бірден `.get_text()`** — тізімнің мұндай әдісі жоқ. Циклмен немесе `[0]` арқылы.

**4. Деректер `Ctrl+U`-да жоқ** — бет JavaScript арқылы жүктеледі; `requests` оларды көрмейді. Network → Fetch/XHR-дан API іздеңіз.

**5. Кодировка қатесі («Ã©» сияқты таңбалар)** — `r.encoding = "utf-8"` немесе `BeautifulSoup(r.content, "html.parser")` (байттар беріп, кодировканы кітапханаға анықтатыңыз).

**6. Салыстырмалы сілтемені тікелей сұрау** — `requests.get("/books/1")` → `MissingSchema`. `urljoin(base, href)` қолданыңыз.

**7. Мәтінді санға айналдырмау** — `"4500 ₸" > "600 ₸"` жолдарды салыстыру қате нәтиже береді. Валюта белгісі мен бос орындарды алып, `int()`/`float()`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Веб-скрапинг | HTML беттерден деректерді автоматты жинау |
| BeautifulSoup | HTML/XML талдауға арналған кітапхана (`bs4`) |
| Талдағыш (parser) | HTML мәтінін ағашқа айналдыратын модуль: `html.parser`, `lxml` |
| `find()` / `find_all()` | Бірінші / барлық сәйкес элементті іздеу |
| `select()` / `select_one()` | CSS-селектор бойынша іздеу |
| `get_text(strip=True)` | Элементтің таза мәтіні |
| `tag["attr"]` / `tag.get("attr")` | Атрибут мәні |
| `urljoin` | Салыстырмалы URL-ді толық URL-ге айналдыру |
| Пагинация | Деректердің бірнеше бетке бөлінуі |

## Ескерту

- Элемент табылмаса, `find()` `None` қайтарады, ал `find_all()` — бос тізім; `None`-ның `.text`-іне жүгінсе, `AttributeError` шығады.
- `find_all()`-да класс атрибуты `class_` (астын сызумен) деп беріледі, себебі `class` — Python-ның резервтелген сөзі.
- `"html.parser"` талдағышы Python-ға кірістірілген; `"lxml"` жылдамырақ, бірақ `pip install lxml` керек; әртүрлі талдағыштар бұзық HTML-ді әртүрлі «түзетуі» мүмкін.
- `get_text(strip=True)` артық бос орындар мен жол ауыстыруларды алып тастайды; тег ішінде бірнеше еншілес элемент болса, `.string` `None` қайтарады.
- Салыстырмалы сілтемелерді (`href="page-2.html"`) `urllib.parse.urljoin(base_url, href)` арқылы абсолюттіге айналдырыңыз. Құжаттама: https://www.crummy.com/software/BeautifulSoup/bs4/doc/.
