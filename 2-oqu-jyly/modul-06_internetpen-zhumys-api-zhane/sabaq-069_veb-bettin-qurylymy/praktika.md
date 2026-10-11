# Сабақ 69. Практикалық тапсырмалар: Веб-беттің құрылымы, HTML

## Базалық деңгей

### 1-тапсырма. «Мен туралы» беті
VS Code-та `about.html` файлын жасаңыз. Бетте болуы тиіс: `<title>`, `<h1>` (атыңыз), өзіңіз туралы абзац, сүйікті 3 пәннің `<ul>` тізімі, бір сурет (`<img>` — `alt` атрибутымен), сүйікті сайтқа сілтеме (`target="_blank"`). Браузерде ашып тексеріңіз.

### 2-тапсырма. Кесте
`schedule.html` бетінде үйірме кестесін `<table>` арқылы жасаңыз: бағандар — «Күні», «Уақыты», «Тақырып»; кемінде 4 жол. Тақырып жолы `<thead>` ішінде, қалғаны `<tbody>` ішінде болсын.

### 3-тапсырма. DOM-ағашты сызу
Төмендегі HTML-дің DOM-ағашын дәптерге сызыңыз және сұрақтарға жауап беріңіз:

```html
<body>
  <div id="shop">
    <h2>Кітаптар</h2>
    <div class="book">
      <h3 class="title">Абай жолы</h3>
      <span class="author">М. Әуезов</span>
      <span class="price">4500 ₸</span>
    </div>
    <div class="book sale">
      <h3 class="title">Көшпенділер</h3>
      <span class="author">І. Есенберлин</span>
      <span class="price">6000 ₸</span>
    </div>
  </div>
</body>
```

а) `h3.title` элементтерінің ата-анасы қандай элемент?
ә) Бірінші `span.author` үшін бауырлары қандай?
б) Мына селекторлар қанша элементті табады: `.book`, `.sale`, `#shop h3`, `div.book > span`, `.book.sale .price`?

### 4-тапсырма. DevTools зерттеуі
`https://books.toscrape.com` сайтын ашыңыз (бұл — скрапингті үйренуге арналған арнайы оқу сайты). DevTools көмегімен жауап беріңіз:
1. Бір кітап карточкасы қандай тегте және қандай класта?
2. Кітап атауы қай тегте және қай атрибутта толық жазылған?
3. Баға қандай класта?
4. Рейтинг (жұлдыздар) қалай белгіленген?
5. Elements-тегі `Ctrl+F` арқылы `article.product_pod` селекторы бетте неше элемент табады?

### 5-тапсырма. Python-да HTML есеп
Оқушылар тізімінен (аты, сынып, балл) HTML кесте жасайтын бағдарлама жазыңыз. Балы 90-нан жоғары оқушылардың жолы `class="top"` атрибутымен белгіленсін. Аттарды `html.escape()` арқылы өңдеңіз. Файлды `webbrowser` арқылы ашыңыз.

## Кеңейтілген деңгей

### 6-тапсырма. Стиль қосу
5-тапсырмадағы бетке `<head>` ішінде `<style>` блогын қосыңыз: кесте шекаралары, `.top` жолдары жасыл фонды, тақырып жолы қалың. CSS-селекторлардың HTML-мен байланысын байқаңыз.

### 7-тапсырма. SQLite → HTML
61-сабақтағы `school.db` деректер қорынан оқушыларды оқып, HTML-есеп жасаңыз: тақырып, жасалған күні, кесте, соңында «Барлығы: N оқушы, орташа балл: X».

### 8-тапсырма. Тегтерді санау
`html.parser.HTMLParser` класынан мұраланып, берілген HTML мәтініндегі әр тегтің неше рет кездесетінін санайтын бағдарлама жазыңыз (`handle_starttag` әдісі). 3-тапсырмадағы HTML-де тексеріңіз.

```text
span: 4
div: 3
h3: 2
body: 1
h2: 1
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Сілтемелер жинағыш
`HTMLParser` арқылы кез келген беттің (мысалы `https://example.com` немесе `https://books.toscrape.com`, `urllib.request` арқылы жүктелген) барлық `<a href>` сілтемелерін жинап, `urljoin` көмегімен толық URL-ге айналдырыңыз. Ішкі (сол домендегі) және сыртқы сілтемелерді бөлек санаңыз.

### 10-тапсырма. Портфолио сайты
Python-да `portfolio.html` генераторын жазыңыз: `projects.json` файлындағы жобалар (атауы, сипаттамасы, GitHub сілтемесі, технологиялар) бойынша әр жобаға `<div class="project">` карточкасын жасайды. Бұл бетті кейін GitHub Pages-те жариялауға болады.

## Үй тапсырмасы

1. Сүйікті сайтыңыздың (жаңалықтар, кітап дүкені т.б.) бір бетін DevTools арқылы зерттеп, «деректер картасын» жазыңыз: қандай деректер (атау, баға, күн) қай тегте/класта тұр.
2. `pip install beautifulsoup4` орындаңыз.
3. 1-тапсырмадағы бетті жетілдіріп, `<header>`, `<main>`, `<footer>` тегтерін қолданыңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма — үлгі**
```html
<!DOCTYPE html>
<html lang="kk">
<head>
    <meta charset="UTF-8">
    <title>Мен туралы</title>
</head>
<body>
    <h1>Айгерім Сәрсенова</h1>
    <p>Мен 9-сыныпта оқимын, Python үйірмесіне қатысамын.</p>
    <ul>
        <li>Информатика</li>
        <li>Математика</li>
        <li>Физика</li>
    </ul>
    <img src="photo.jpg" alt="Менің суретім" width="200">
    <p><a href="https://www.python.org" target="_blank">Python ресми сайты</a></p>
</body>
</html>
```

**2-тапсырма — үлгі**
```html
<table border="1">
    <thead>
        <tr><th>Күні</th><th>Уақыты</th><th>Тақырып</th></tr>
    </thead>
    <tbody>
        <tr><td>Дүйсенбі</td><td>15:00</td><td>HTML негіздері</td></tr>
        <tr><td>Сейсенбі</td><td>15:00</td><td>BeautifulSoup</td></tr>
        <tr><td>Бейсенбі</td><td>15:00</td><td>Скрапинг этикасы</td></tr>
        <tr><td>Дүйсенбі</td><td>15:00</td><td>Ауа райы қосымшасы</td></tr>
    </tbody>
</table>
```

**3-тапсырма — жауаптар**
а) `div.book` (әрқайсысы өз карточкасының ішінде).
ә) `h3.title` және `span.price`.
б) `.book` — 2; `.sale` — 1; `#shop h3` — 2 (`h2` емес, тек `h3`); `div.book > span` — 4; `.book.sale .price` — 1.

**4-тапсырма — жауаптар**
1. `<article class="product_pod">`. 2. `<h3><a title="Толық атауы">қысқартылған…</a></h3>` — толық атауы `title` атрибутында. 3. `<p class="price_color">`. 4. `<p class="star-rating Three">` — рейтинг класс атауында. 5. 20 элемент.

**5–6-тапсырмалар**
```python
import html
import webbrowser
from pathlib import Path

students = [("Айгерім", 9, 92.5), ("Нұрлан", 10, 78.0), ("Дана <3", 9, 88.0), ("Ержан", 11, 95.5)]

rows = []
for name, grade, score in students:
    cls = ' class="top"' if score > 90 else ""
    rows.append(f"<tr{cls}><td>{html.escape(name)}</td><td>{grade}</td><td>{score}</td></tr>")

page = f"""<!DOCTYPE html>
<html lang="kk">
<head>
<meta charset="UTF-8">
<title>Оқушылар есебі</title>
<style>
  table {{ border-collapse: collapse; }}
  td, th {{ border: 1px solid #999; padding: 4px 10px; }}
  th {{ font-weight: bold; background: #eee; }}
  .top {{ background: #d4f7d4; }}
</style>
</head>
<body>
<h1>Оқушылар есебі</h1>
<table>
<tr><th>Аты</th><th>Сынып</th><th>Балл</th></tr>
{chr(10).join(rows)}
</table>
</body>
</html>"""

path = Path("report.html")
path.write_text(page, encoding="utf-8")
print("Сақталды:", path.resolve())
# webbrowser.open(path.resolve().as_uri())
```
Ескерту: f-жол ішіндегі CSS бұйра жақшалары `{{ }}` деп екі еселенеді.

**7-тапсырма**
```python
import html
import sqlite3
from datetime import datetime
from pathlib import Path

with sqlite3.connect("school.db") as conn:
    rows = conn.execute("SELECT name, grade, city, score FROM students ORDER BY score DESC").fetchall()

body = "\n".join(
    "<tr>" + "".join(f"<td>{html.escape(str(v))}</td>" for v in r) + "</tr>" for r in rows)
avg = sum(r[3] for r in rows) / len(rows) if rows else 0
page = f"""<!DOCTYPE html>
<html lang="kk"><head><meta charset="UTF-8"><title>Есеп</title></head>
<body>
<h1>Оқушылар есебі</h1>
<p>Жасалған күні: {datetime.now():%d.%m.%Y %H:%M}</p>
<table border="1">
<tr><th>Аты-жөні</th><th>Сынып</th><th>Қала</th><th>Балл</th></tr>
{body}
</table>
<p>Барлығы: {len(rows)} оқушы, орташа балл: {avg:.1f}</p>
</body></html>"""
Path("school_report.html").write_text(page, encoding="utf-8")
print("Есеп дайын")
```

**8-тапсырма**
```python
from collections import Counter
from html.parser import HTMLParser


class TagCounter(HTMLParser):
    def __init__(self):
        super().__init__()
        self.counter = Counter()

    def handle_starttag(self, tag, attrs):
        self.counter[tag] += 1


doc = """<body><div id="shop"><h2>Кітаптар</h2>
<div class="book"><h3 class="title">Абай жолы</h3><span class="author">М. Әуезов</span>
<span class="price">4500 ₸</span></div>
<div class="book sale"><h3 class="title">Көшпенділер</h3><span class="author">І. Есенберлин</span>
<span class="price">6000 ₸</span></div></div></body>"""

parser = TagCounter()
parser.feed(doc)
for tag, n in parser.counter.most_common():
    print(f"{tag}: {n}")
```

**9-тапсырма**
```python
from html.parser import HTMLParser
from urllib.parse import urljoin, urlparse
from urllib.request import urlopen


class LinkCollector(HTMLParser):
    def __init__(self, base):
        super().__init__()
        self.base = base
        self.links = set()

    def handle_starttag(self, tag, attrs):
        if tag == "a":
            href = dict(attrs).get("href")
            if href and not href.startswith(("#", "mailto:", "javascript:")):
                self.links.add(urljoin(self.base, href))


url = "https://books.toscrape.com/"
with urlopen(url, timeout=10) as r:
    page = r.read().decode("utf-8")
collector = LinkCollector(url)
collector.feed(page)
domain = urlparse(url).netloc
internal = [l for l in collector.links if urlparse(l).netloc == domain]
print("Барлық сілтеме:", len(collector.links))
print("Ішкі:", len(internal), "Сыртқы:", len(collector.links) - len(internal))
```

**10-тапсырма**
```python
import html
import json
from pathlib import Path

projects = json.loads(Path("projects.json").read_text(encoding="utf-8"))
cards = []
for p in projects:
    tags = ", ".join(html.escape(t) for t in p.get("tech", []))
    cards.append(f"""<div class="project">
  <h2>{html.escape(p['name'])}</h2>
  <p>{html.escape(p['description'])}</p>
  <p><i>{tags}</i></p>
  <a href="{html.escape(p['github'])}" target="_blank">GitHub</a>
</div>""")
page = ("<!DOCTYPE html><html lang='kk'><head><meta charset='UTF-8'><title>Портфолио</title></head>"
        "<body><h1>Менің жобаларым</h1>" + "\n".join(cards) + "</body></html>")
Path("portfolio.html").write_text(page, encoding="utf-8")
print("portfolio.html дайын")
```

</details>
