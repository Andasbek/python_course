# Урок 69. Практические задания: структура веб-страницы, HTML

## Базовый уровень

### Задание 1. Страница «Обо мне»
Создайте в VS Code файл `about.html`. На странице должны быть: `<title>`, `<h1>` (ваше имя), абзац о себе, список `<ul>` из 3 любимых предметов, одно изображение (`<img>` — с атрибутом `alt`), ссылка на любимый сайт (`target="_blank"`). Откройте в браузере и проверьте.

### Задание 2. Таблица
На странице `schedule.html` создайте расписание кружка с помощью `<table>`: столбцы — «День», «Время», «Тема»; не менее 4 строк. Строка заголовков должна быть внутри `<thead>`, остальные — внутри `<tbody>`.

### Задание 3. Рисуем DOM-дерево
Нарисуйте в тетради DOM-дерево HTML ниже и ответьте на вопросы:

```html
<body>
  <div id="shop">
    <h2>Книги</h2>
    <div class="book">
      <h3 class="title">Путь Абая</h3>
      <span class="author">М. Ауэзов</span>
      <span class="price">4500 ₸</span>
    </div>
    <div class="book sale">
      <h3 class="title">Кочевники</h3>
      <span class="author">И. Есенберлин</span>
      <span class="price">6000 ₸</span>
    </div>
  </div>
</body>
```

а) Какой элемент является родителем элементов `h3.title`?
б) Какие соседи у первого `span.author`?
в) Сколько элементов найдут селекторы: `.book`, `.sale`, `#shop h3`, `div.book > span`, `.book.sale .price`?

### Задание 4. Исследование DevTools
Откройте сайт `https://books.toscrape.com` (это специальный учебный сайт для изучения скрапинга). С помощью DevTools ответьте:
1. В каком теге и с каким классом находится карточка одной книги?
2. В каком теге и в каком атрибуте полностью записано название книги?
3. В каком классе цена?
4. Как обозначен рейтинг (звёзды)?
5. Сколько элементов на странице находит селектор `article.product_pod` через `Ctrl+F` в Elements?

### Задание 5. HTML-отчёт в Python
Напишите программу, создающую HTML-таблицу из списка учеников (имя, класс, балл). Строки учеников с баллом выше 90 должны помечаться атрибутом `class="top"`. Обрабатывайте имена через `html.escape()`. Откройте файл через `webbrowser`.

## Продвинутый уровень

### Задание 6. Добавление стиля
Добавьте на страницу из задания 5 блок `<style>` внутри `<head>`: границы таблицы, зелёный фон у строк `.top`, жирная строка заголовков. Обратите внимание на связь CSS-селекторов с HTML.

### Задание 7. SQLite → HTML
Прочитайте учеников из базы данных `school.db` урока 61 и создайте HTML-отчёт: заголовок, дата создания, таблица, в конце «Всего учеников: N, средний балл: X».

### Задание 8. Подсчёт тегов
Унаследуйтесь от класса `html.parser.HTMLParser` и напишите программу, считающую, сколько раз встречается каждый тег в заданном HTML-тексте (метод `handle_starttag`). Проверьте на HTML из задания 3.

```text
span: 4
div: 3
h3: 2
body: 1
h2: 1
```

## ★ Олимпиадная / творческая задача

### Задание 9. Сборщик ссылок
С помощью `HTMLParser` соберите все ссылки `<a href>` любой страницы (например, `https://example.com` или `https://books.toscrape.com`, загруженной через `urllib.request`) и превратите их в полные URL с помощью `urljoin`. Посчитайте отдельно внутренние (того же домена) и внешние ссылки.

### Задание 10. Сайт-портфолио
Напишите на Python генератор `portfolio.html`: по проектам из файла `projects.json` (название, описание, ссылка на GitHub, технологии) он создаёт для каждого проекта карточку `<div class="project">`. Эту страницу потом можно опубликовать на GitHub Pages.

## Домашнее задание

1. Исследуйте через DevTools одну страницу своего любимого сайта (новости, книжный магазин и т.п.) и составьте «карту данных»: какие данные (название, цена, дата) в каком теге/классе находятся.
2. Выполните `pip install beautifulsoup4`.
3. Доработайте страницу из задания 1, используя теги `<header>`, `<main>`, `<footer>`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1 — образец**
```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Обо мне</title>
</head>
<body>
    <h1>Айгерім Сәрсенова</h1>
    <p>Я учусь в 9-м классе, посещаю кружок Python.</p>
    <ul>
        <li>Информатика</li>
        <li>Математика</li>
        <li>Физика</li>
    </ul>
    <img src="photo.jpg" alt="Моя фотография" width="200">
    <p><a href="https://www.python.org" target="_blank">Официальный сайт Python</a></p>
</body>
</html>
```

**Задание 2 — образец**
```html
<table border="1">
    <thead>
        <tr><th>День</th><th>Время</th><th>Тема</th></tr>
    </thead>
    <tbody>
        <tr><td>Понедельник</td><td>15:00</td><td>Основы HTML</td></tr>
        <tr><td>Вторник</td><td>15:00</td><td>BeautifulSoup</td></tr>
        <tr><td>Четверг</td><td>15:00</td><td>Этика скрапинга</td></tr>
        <tr><td>Понедельник</td><td>15:00</td><td>Приложение «Погода»</td></tr>
    </tbody>
</table>
```

**Задание 3 — ответы**
а) `div.book` (каждый — внутри своей карточки).
б) `h3.title` и `span.price`.
в) `.book` — 2; `.sale` — 1; `#shop h3` — 2 (не `h2`, только `h3`); `div.book > span` — 4; `.book.sale .price` — 1.

**Задание 4 — ответы**
1. `<article class="product_pod">`. 2. `<h3><a title="Полное название">сокращённое…</a></h3>` — полное название в атрибуте `title`. 3. `<p class="price_color">`. 4. `<p class="star-rating Three">` — рейтинг в имени класса. 5. 20 элементов.

**Задания 5–6**
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
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Отчёт по ученикам</title>
<style>
  table {{ border-collapse: collapse; }}
  td, th {{ border: 1px solid #999; padding: 4px 10px; }}
  th {{ font-weight: bold; background: #eee; }}
  .top {{ background: #d4f7d4; }}
</style>
</head>
<body>
<h1>Отчёт по ученикам</h1>
<table>
<tr><th>Имя</th><th>Класс</th><th>Балл</th></tr>
{chr(10).join(rows)}
</table>
</body>
</html>"""

path = Path("report.html")
path.write_text(page, encoding="utf-8")
print("Сохранено:", path.resolve())
# webbrowser.open(path.resolve().as_uri())
```
Примечание: фигурные скобки CSS внутри f-строки удваиваются: `{{ }}`.

**Задание 7**
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
<html lang="ru"><head><meta charset="UTF-8"><title>Отчёт</title></head>
<body>
<h1>Отчёт по ученикам</h1>
<p>Дата создания: {datetime.now():%d.%m.%Y %H:%M}</p>
<table border="1">
<tr><th>ФИО</th><th>Класс</th><th>Город</th><th>Балл</th></tr>
{body}
</table>
<p>Всего учеников: {len(rows)}, средний балл: {avg:.1f}</p>
</body></html>"""
Path("school_report.html").write_text(page, encoding="utf-8")
print("Отчёт готов")
```

**Задание 8**
```python
from collections import Counter
from html.parser import HTMLParser


class TagCounter(HTMLParser):
    def __init__(self):
        super().__init__()
        self.counter = Counter()

    def handle_starttag(self, tag, attrs):
        self.counter[tag] += 1


doc = """<body><div id="shop"><h2>Книги</h2>
<div class="book"><h3 class="title">Путь Абая</h3><span class="author">М. Ауэзов</span>
<span class="price">4500 ₸</span></div>
<div class="book sale"><h3 class="title">Кочевники</h3><span class="author">И. Есенберлин</span>
<span class="price">6000 ₸</span></div></div></body>"""

parser = TagCounter()
parser.feed(doc)
for tag, n in parser.counter.most_common():
    print(f"{tag}: {n}")
```

**Задание 9**
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
print("Всего ссылок:", len(collector.links))
print("Внутренних:", len(internal), "Внешних:", len(collector.links) - len(internal))
```

**Задание 10**
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
page = ("<!DOCTYPE html><html lang='ru'><head><meta charset='UTF-8'><title>Портфолио</title></head>"
        "<body><h1>Мои проекты</h1>" + "\n".join(cards) + "</body></html>")
Path("portfolio.html").write_text(page, encoding="utf-8")
print("portfolio.html готов")
```

</details>
