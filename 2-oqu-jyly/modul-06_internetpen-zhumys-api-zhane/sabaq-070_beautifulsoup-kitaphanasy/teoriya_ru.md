# Урок 70. Библиотека BeautifulSoup. Получение данных с веб-страницы.

## Темы

1. Что такое веб-скрапинг?
2. Установка и первый «суп»
3. `find()` и `find_all()`
4. Получение текста и атрибутов
5. CSS-селекторы: `select()` и `select_one()`
6. Навигация по дереву
7. Разбор таблиц
8. Реальный сайт: `books.toscrape.com`
9. Сохранение результата
10. Частые ошибки
11. Основные понятия

## 1. Что такое веб-скрапинг?

**Веб-скрапинг (web scraping)** — автоматический сбор данных из HTML-кода веб-страниц с помощью программы.

| | API | Веб-скрапинг |
|---|---|---|
| Формат данных | Структурированный JSON | HTML (страница для человека) |
| Стабильность | Высокая (задокументировано) | Низкая (изменится дизайн сайта — код сломается) |
| Разрешение | Дано официально | Зависит от правил сайта |
| Когда | Если API есть — **всегда в первую очередь** | Если API нет |

**Правило:** если есть API — используем API. Скрапинг — второй путь.

Этапы скрапинга:
```text
1. requests.get(url)  →  2. BeautifulSoup(html)  →  3. find/select  →  4. get_text / ["href"]  →  5. сохранение (CSV/JSON/SQLite)
   загрузка страницы       превращение в дерево     поиск элементов        получение данных           
```

## 2. Установка и первый «суп»

```bash
pip install beautifulsoup4
```

Название библиотеки — `beautifulsoup4`, а импорт — `bs4`. Название взято из стихотворения «Прекрасный суп» из книги «Алиса в Стране чудес».

Сначала тренируемся без интернета, на HTML-тексте:

```python
from bs4 import BeautifulSoup

html = """
<html><head><title>Книжный магазин</title></head>
<body>
  <h1 id="top">Книги</h1>
  <div class="book">
    <h3 class="title"><a href="/books/1">Путь Абая</a></h3>
    <span class="author">Мухтар Ауэзов</span>
    <span class="price">4500 ₸</span>
  </div>
  <div class="book sale">
    <h3 class="title"><a href="/books/2">Кочевники</a></h3>
    <span class="author">Ильяс Есенберлин</span>
    <span class="price">6000 ₸</span>
  </div>
  <div class="book">
    <h3 class="title"><a href="/books/3">Кровь и пот</a></h3>
    <span class="author">Абдижамил Нурпеисов</span>
    <span class="price">5200 ₸</span>
  </div>
</body></html>
"""

soup = BeautifulSoup(html, "html.parser")
print(soup.title)              # первый элемент <title>
print(soup.title.string)       # его текст
print(soup.h1.get_text())      # текст первого <h1>
```

Результат:
```text
<title>Книжный магазин</title>
Книжный магазин
Книги
```

`"html.parser"` — встроенный в Python анализатор (parser), не требует дополнительной установки. Есть и более быстрый анализатор `lxml` (`pip install lxml`).

## 3. `find()` и `find_all()`

| Метод | Возвращает | Если не найдено |
|---|---|---|
| `find(...)` | Первый подходящий элемент (`Tag`) | `None` |
| `find_all(...)` | Список всех подходящих элементов | `[]` |

```python
from bs4 import BeautifulSoup

html = """<div class="book"><h3 class="title"><a href="/books/1">Путь Абая</a></h3>
<span class="author">Мухтар Ауэзов</span><span class="price">4500 ₸</span></div>
<div class="book sale"><h3 class="title"><a href="/books/2">Кочевники</a></h3>
<span class="author">Ильяс Есенберлин</span><span class="price">6000 ₸</span></div>"""
soup = BeautifulSoup(html, "html.parser")

first_price = soup.find("span", class_="price")       # class — ключевое слово Python, поэтому class_
print(first_price.get_text())

books = soup.find_all("div", class_="book")
print("Количество книг:", len(books))

sale = soup.find("div", class_="sale")                 # находит по одному классу, даже если их несколько
print("Со скидкой:", sale.find("a").get_text())

print(soup.find("table"))                              # нет элемента → None
```

Результат:
```text
4500 ₸
Количество книг: 2
Со скидкой: Кочевники
None
```

Способы поиска:

```python
# soup.find_all("a")                          — по тегу
# soup.find_all(["h1", "h2"])                 — несколько тегов
# soup.find(id="top")                         — по id
# soup.find_all("span", class_="price")       — тег + класс
# soup.find_all("a", href=True)               — ссылки с атрибутом href
# soup.find_all("div", attrs={"data-id": "5"})— любой атрибут
# soup.find_all("li", limit=3)                — только первые 3
```

**Важно:** методы `find()` / `find_all()` можно вызывать у любого элемента — тогда поиск идёт только **внутри** этого элемента. Это основной приём скрапинга: сначала находим «карточки», затем берём данные изнутри каждой карточки.

## 4. Получение текста и атрибутов

```python
from bs4 import BeautifulSoup

soup = BeautifulSoup('<a href="/books/1" title="Полное название"> \n Путь Абая \n</a>', "html.parser")
link = soup.find("a")

print(repr(link.get_text()))            # с пробелами
print(link.get_text(strip=True))        # без пробелов
print(link["href"])                     # атрибут (если нет — KeyError)
print(link.get("title"))                # атрибут (если нет — None)
print(link.get("target", "нет"))
print(link.attrs)                       # словарь всех атрибутов
```

Результат:
```text
' \n Путь Абая \n'
Путь Абая
/books/1
Полное название
нет
{'href': '/books/1', 'title': 'Полное название'}
```

**Атрибут `class` возвращается списком**, потому что у элемента может быть несколько классов: `tag["class"]` → `['book', 'sale']`.

### Шаблон: сбор данных из карточек

```python
from bs4 import BeautifulSoup

html = """<div class="book"><h3 class="title"><a href="/books/1">Путь Абая</a></h3>
<span class="author">Мухтар Ауэзов</span><span class="price">4500 ₸</span></div>
<div class="book sale"><h3 class="title"><a href="/books/2">Кочевники</a></h3>
<span class="author">Ильяс Есенберлин</span><span class="price">6000 ₸</span></div>"""
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

Результат:
```text
{'title': 'Путь Абая', 'author': 'Мухтар Ауэзов', 'price': 4500, 'url': '/books/1', 'sale': False}
{'title': 'Кочевники', 'author': 'Ильяс Есенберлин', 'price': 6000, 'url': '/books/2', 'sale': True}
```

Результат скрапинга — знакомый нам **список словарей**. Его можно сохранить в CSV, JSON или SQLite (V модуль).

## 5. CSS-селекторы: `select()` и `select_one()`

CSS-селекторы можно использовать напрямую — часто это короче:

| Стиль `find` | Стиль `select` |
|---|---|
| `soup.find_all("div", class_="book")` | `soup.select("div.book")` |
| `soup.find(id="top")` | `soup.select_one("#top")` |
| `card.find("h3").find("a")` | `card.select_one("h3 a")` |
| — | `soup.select("div.book > span.price")` |
| — | `soup.select("table tr td:nth-of-type(2)")` |

```python
from bs4 import BeautifulSoup

html = """<div class="book"><h3><a href="/b/1">Путь Абая</a></h3><span class="price">4500 ₸</span></div>
<div class="book sale"><h3><a href="/b/2">Кочевники</a></h3><span class="price">6000 ₸</span></div>"""
soup = BeautifulSoup(html, "html.parser")

for a in soup.select("div.book h3 a"):
    print(a.get_text(), "→", a["href"])
print("Цена со скидкой:", soup.select_one(".book.sale .price").get_text())
```

Результат:
```text
Путь Абая → /b/1
Кочевники → /b/2
Цена со скидкой: 6000 ₸
```

**Подсказка:** в DevTools правой кнопкой по элементу → Copy → Copy selector — но такой селектор может быть длинным и «хрупким» (`body > div:nth-child(3) > ...`). Лучше самому написать короткий селектор на основе класса.

## 6. Навигация по дереву

```python
from bs4 import BeautifulSoup

soup = BeautifulSoup("<ul><li>Python</li><li>SQL</li><li>Git</li></ul>", "html.parser")
second = soup.find_all("li")[1]

print(second.parent.name)                         # родитель
print(second.find_previous_sibling("li").text)    # предыдущий сосед
print(second.find_next_sibling("li").text)        # следующий сосед
print([li.text for li in soup.ul.find_all("li")]) # дочерние элементы
```

Результат:
```text
ul
Python
Git
['Python', 'SQL', 'Git']
```

## 7. Разбор таблиц

```python
from bs4 import BeautifulSoup

html = """<table id="cities">
<tr><th>Город</th><th>Область</th><th>Население, тыс.</th></tr>
<tr><td>Алматы</td><td>—</td><td>2 200</td></tr>
<tr><td>Караганда</td><td>Карагандинская</td><td>500</td></tr>
<tr><td>Актобе</td><td>Актюбинская</td><td>580</td></tr>
</table>"""
soup = BeautifulSoup(html, "html.parser")
table = soup.find("table", id="cities")

headers = [th.get_text(strip=True) for th in table.find_all("th")]
rows = []
for tr in table.find_all("tr")[1:]:                # пропускаем строку заголовков
    cells = [td.get_text(strip=True) for td in tr.find_all("td")]
    rows.append(dict(zip(headers, cells)))

for r in sorted(rows, key=lambda r: int(r["Население, тыс."].replace(" ", "")), reverse=True):
    print(r["Город"], r["Население, тыс."])
```

Результат:
```text
Алматы 2 200
Актобе 580
Караганда 500
```

## 8. Реальный сайт: `books.toscrape.com`

```python
import requests
from bs4 import BeautifulSoup
from urllib.parse import urljoin

URL = "https://books.toscrape.com/"
HEADERS = {"User-Agent": "PythonClub-Almaty/1.0 (educational)"}

r = requests.get(URL, headers=HEADERS, timeout=10)
r.raise_for_status()
r.encoding = "utf-8"                              # явно указываем кодировку
soup = BeautifulSoup(r.text, "html.parser")

RATING = {"One": 1, "Two": 2, "Three": 3, "Four": 4, "Five": 5}
books = []
for card in soup.select("article.product_pod"):
    link = card.select_one("h3 a")
    books.append({
        "title": link["title"],                                   # полное название — в атрибуте
        "price": float(card.select_one(".price_color").get_text().replace("£", "")),
        "rating": RATING[card.select_one("p.star-rating")["class"][1]],
        "url": urljoin(URL, link["href"]),                        # относительный → полный
    })

print("Найдено:", len(books))
for b in books[:3]:
    print(f"{b['rating']}★ £{b['price']:.2f}  {b['title']}")
```

Результат:
```text
Найдено: 20
3★ £51.77  A Light in the Attic
1★ £53.74  Tipping the Velvet
1★ £50.10  Soumission
```

### Обход нескольких страниц (пагинация)

Если на странице есть ссылка «next», переходим по ней и продолжаем сбор:

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
        print(f"Страница {page}: всего книг — {len(all_titles)}")
        next_link = soup.select_one("li.next a")
        if next_link is None:                     # последняя страница
            break
        url = urljoin(url, next_link["href"])
        time.sleep(1)                             # вежливость к серверу: пауза 1 секунда
    return all_titles

# titles = scrape_pages("https://books.toscrape.com/catalogue/page-1.html")
```

`time.sleep(1)` — чтобы не перегружать сервер.

## 9. Сохранение результата

```python
import csv

books = [{"title": "Путь Абая", "price": 4500}, {"title": "Кочевники", "price": 6000}]
with open("books.csv", "w", newline="", encoding="utf-8-sig") as f:
    writer = csv.DictWriter(f, fieldnames=["title", "price"])
    writer.writeheader()
    writer.writerows(books)
print("books.csv сохранён")
```

## Частые ошибки

**1. `AttributeError: 'NoneType' object has no attribute 'get_text'`** — `find()` ничего не нашёл (неверный селектор или в этой карточке нет элемента). Проверка:
```python
from bs4 import BeautifulSoup
card = BeautifulSoup('<div class="book"><h3>Название</h3></div>', "html.parser")
price = card.find("span", class_="price")
print(price.get_text(strip=True) if price else "цена не указана")
```

**2. Написано `class` вместо `class_`** — `SyntaxError`, потому что `class` — ключевое слово Python.

**3. Вызов `.get_text()` сразу у результата `find_all`** — у списка нет такого метода. Используйте цикл или `[0]`.

**4. Данных нет в `Ctrl+U`** — страница загружается через JavaScript; `requests` их не видит. Ищите API в Network → Fetch/XHR.

**5. Ошибка кодировки (символы вроде «Ã©»)** — `r.encoding = "utf-8"` или `BeautifulSoup(r.content, "html.parser")` (передайте байты, пусть библиотека сама определит кодировку).

**6. Прямой запрос относительной ссылки** — `requests.get("/books/1")` → `MissingSchema`. Используйте `urljoin(base, href)`.

**7. Текст не преобразован в число** — сравнение строк `"4500 ₸" > "600 ₸"` даёт неверный результат. Уберите знак валюты и пробелы, затем `int()`/`float()`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Веб-скрапинг | Автоматический сбор данных с HTML-страниц |
| BeautifulSoup | Библиотека для разбора HTML/XML (`bs4`) |
| Анализатор (parser) | Модуль, превращающий HTML-текст в дерево: `html.parser`, `lxml` |
| `find()` / `find_all()` | Поиск первого / всех подходящих элементов |
| `select()` / `select_one()` | Поиск по CSS-селектору |
| `get_text(strip=True)` | Чистый текст элемента |
| `tag["attr"]` / `tag.get("attr")` | Значение атрибута |
| `urljoin` | Превращение относительного URL в полный |
| Пагинация | Разбиение данных на несколько страниц |

## Примечание

- Если элемент не найден, `find()` возвращает `None`, а `find_all()` — пустой список; обращение к `.text` у `None` даёт `AttributeError`.
- В `find_all()` атрибут класса передаётся как `class_` (с подчёркиванием), потому что `class` — зарезервированное слово Python.
- Парсер `"html.parser"` встроен в Python; `"lxml"` быстрее, но требует `pip install lxml`; разные парсеры могут по-разному «чинить» некорректный HTML.
- `get_text(strip=True)` убирает лишние пробелы и переносы строк; `.string` возвращает `None`, если внутри тега несколько дочерних элементов.
- Относительные ссылки (`href="page-2.html"`) превращайте в абсолютные через `urllib.parse.urljoin(base_url, href)`. Документация: https://www.crummy.com/software/BeautifulSoup/bs4/doc/.
