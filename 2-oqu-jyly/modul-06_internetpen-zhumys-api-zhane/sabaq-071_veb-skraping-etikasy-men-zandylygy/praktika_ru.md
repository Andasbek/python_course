# Урок 71. Практические задания: этика скрапинга, robots.txt, персональные данные

## Базовый уровень

### Задание 1. Чтение robots.txt (в браузере + в тетради)
Откройте файлы robots.txt следующих сайтов и заполните таблицу:
`https://www.wikipedia.org/robots.txt`, `https://github.com/robots.txt`, `https://books.toscrape.com/robots.txt`, `https://www.google.com/robots.txt`.

| Сайт | 2 раздела, запрещённых для `User-agent: *` | Есть ли `Crawl-delay` | Есть ли `Sitemap` |
|---|---|---|---|

Подумайте: что означает сайт без файла robots.txt (404)?

### Задание 2. Анализатор robots.txt
Загрузите правила ниже через `RobotFileParser.parse()` и выведите для каждого URL из списка, разрешён ли он для `StudentBot`. Также покажите значение `crawl_delay`.

```python
RULES = """
User-agent: *
Allow: /library/public
Disallow: /library/
Disallow: /students/
Disallow: /*.pdf
Crawl-delay: 3

User-agent: StudentBot
Allow: /news/
Disallow: /library/
Crawl-delay: 2
"""
URLS = ["/", "/news/2027/olympiad", "/library/books", "/library/public/list",
        "/students/9a", "/docs/plan.pdf"]
```

```text
/                       → разрешено
/news/2027/olympiad     → разрешено
/library/books          → запрещено
...
Crawl-delay: 2 с
```

Примечание: у `StudentBot` есть собственный блок — блок `*` он не использует! Объясните результат с этой точки зрения.

### Задание 3. Подсчёт пауз
Скрапер должен загрузить 120 страниц. `Crawl-delay: 5`, каждый запрос в среднем 0.4 с. Сколько минут продлится вся работа? Напишите программу для расчёта, выведите результат в минутах и секундах. А если без пауз — сколько? Какая разница для сервера?

### Задание 4. Маскирование персональных данных
Функция `mask_personal(text)` должна маскировать в тексте адреса email (`ai***@mail.kz`), телефоны (`+7 701 *** ** 67`) и 12-значные номера ИИН (`0503******12`).

```text
Вход:   Ученик: Дана, ИИН 050312650412, тел 87071234567, dana.o@gmail.com
Выход:  Ученик: Дана, ИИН 0503******12, тел 8 707 *** ** 67, da***@gmail.com
```

### Задание 5. Ситуации (кейсы) — групповое обсуждение
Для каждой ситуации ответьте: «Этично ли? Законно ли? Как поступить правильно?»
1. У сайта погоды есть API, но Асет каждую секунду скрапит HTML-страницу.
2. Мадина раз в день собирает цены 50 товаров Kaspi и строит на своём ноутбуке график изменения цен. Правила сайта она не проверяла.
3. Ерлан собрал из школьного чата имена и телефоны всех учеников и опубликовал на сайте «Наш класс».
4. Айбек взял из Wikipedia таблицу городов Казахстана и использовал в своём проекте, указав источник.
5. Бот собирает из VK/Instagram профили «16-летних алматинцев» и продаёт их рекламной компании.

## Продвинутый уровень

### Задание 6. Вежливый скрапер
С помощью класса `PoliteScraper` из теории соберите названия книг с первых 3 страниц сайта `books.toscrape.com`. Выводите время каждого запроса (`time.strftime("%H:%M:%S")`) и докажите, что пауза соблюдается.

```text
12:00:01 → catalogue/page-1.html (книг: 20)
12:00:02 → catalogue/page-2.html (книг: 20)
12:00:03 → catalogue/page-3.html (книг: 20)
Всего книг: 60
```

### Задание 7. Дисковый кэш
Добавьте в класс `PoliteScraper` дисковый кэш: страницы сохраняются в папке `cache/` (имя файла — хэш `hashlib.md5` от URL). Если страница есть в кэше — запрос в сеть не отправляется. Покажите, что при втором запуске программа работает мгновенно.

### Задание 8. Retry-After
Если сервер возвращает код 429 или 503 с заголовком `Retry-After: 10`, скрапер должен подождать указанное число секунд (но не более 60 с) и повторить запрос один раз. Проверьте через `https://httpbin.org/status/429`.

## ★ Олимпиадная / творческая задача

### Задание 9. Собственный анализатор robots.txt
Напишите простой анализатор без `RobotFileParser`: читает блоки `User-agent`, правила `Allow`/`Disallow`; для URL выбирает **самое длинное** подходящее правило (способ Google); для робота с собственным блоком применяет только этот блок. Сравните с результатом `RobotFileParser` на данных задания 2.

### Задание 10. Плакат кодекса этики
Создайте инфографику «Кодекс ответственного скрапера» (7 правил, с примерами). Лучшая работа будет повешена в кабинете.

## Домашнее задание

1. Найдите страницу «Условия использования» 2 сайтов, которыми часто пользуетесь, и кратко законспектируйте, что там сказано об автоматическом сборе данных.
2. Перепишите код своего скрапера из урока 70, сделав его «вежливым» (robots.txt, User-Agent, паузы).
3. К следующему уроку: повторите код погоды из уроков 66–67 и структуру Tkinter из урока 62 — будет практикум.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1** — реальные файлы могут меняться; оценивание: таблица заполнена, есть понимание, что отсутствие robots.txt означает «ограничения не объявлены, но ToS нужно проверить».

**Задание 2**
```python
from urllib.robotparser import RobotFileParser

RULES = """
User-agent: *
Allow: /library/public
Disallow: /library/
Disallow: /students/
Disallow: /*.pdf
Crawl-delay: 3

User-agent: StudentBot
Allow: /news/
Disallow: /library/
Crawl-delay: 2
"""
URLS = ["/", "/news/2027/olympiad", "/library/books", "/library/public/list",
        "/students/9a", "/docs/plan.pdf"]

rp = RobotFileParser()
rp.parse(RULES.splitlines())
for path in URLS:
    ok = rp.can_fetch("StudentBot", "https://school.kz" + path)
    print(f"{path:22} → {'разрешено' if ok else 'запрещено'}")
print("Crawl-delay:", rp.crawl_delay("StudentBot"), "с")
```
Пояснение: для `StudentBot` применяется только его собственный блок, поэтому `/students/9a` и `/docs/plan.pdf` — разрешено, `/library/public/list` — запрещено (в блоке StudentBot нет `Allow: /library/public`). Кроме того, стандартный `robotparser` не поддерживает маску `*` (`/*.pdf`).

**Задание 3**
```python
pages, delay, request_time = 120, 5, 0.4
total = pages * request_time + (pages - 1) * delay
no_delay = pages * request_time
print(f"С паузами: {int(total // 60)} мин {total % 60:.0f} с")
print(f"Без пауз: {int(no_delay // 60)} мин {no_delay % 60:.0f} с")
print(f"Нагрузка на сервер ≈ в {total / no_delay:.0f} раз меньше")
```

**Задание 4**
```python
import re

EMAIL = re.compile(r"\b([\w.]{2})[\w.]*@([\w-]+\.[a-z]{2,})\b")
PHONE = re.compile(r"(\+7|8)[\s-]?(\d{3})[\s-]?\d{3}[\s-]?\d{2}[\s-]?(\d{2})\b")
IIN = re.compile(r"\b(\d{4})\d{6}(\d{2})\b")

def mask_personal(text):
    text = EMAIL.sub(r"\1***@\2", text)
    text = IIN.sub(r"\1******\2", text)         # ИИН — раньше телефона
    text = PHONE.sub(r"\1 \2 *** ** \3", text)
    return text

print(mask_personal("Ученик: Дана, ИИН 050312650412, тел 87071234567, dana.o@gmail.com"))
```

**Задание 5 — ожидаемые ответы**
1. Неэтично: есть API, нагружает сервер → использовать API, уменьшить частоту.
2. Частота вежливая, но ToS и robots.txt не проверены → сначала проверить, только для личного анализа.
3. Незаконно: распространение персональных данных без согласия → прекратить публикацию, удалить данные.
4. Правильно: контент с открытой лицензией, источник указан, у Wikipedia есть и API.
5. Незаконно и неэтично: сбор и продажа персональных данных несовершеннолетних.

**Задания 6–7** (считаем, что `PoliteScraper` из теории находится в файле `polite.py`)
```python
import hashlib
import time
from pathlib import Path
from bs4 import BeautifulSoup
from polite import PoliteScraper   # класс из теории в файле polite.py

class CachedScraper(PoliteScraper):
    def __init__(self, *args, cache_dir="cache", **kwargs):
        super().__init__(*args, **kwargs)
        self.cache_dir = Path(cache_dir)
        self.cache_dir.mkdir(exist_ok=True)

    def get(self, url):
        file = self.cache_dir / (hashlib.md5(url.encode()).hexdigest() + ".html")
        if file.exists():
            return file.read_text(encoding="utf-8")
        html = super().get(url)
        if html is not None:
            file.write_text(html, encoding="utf-8")
        return html

scraper = CachedScraper("https://books.toscrape.com")
total = 0
for n in range(1, 4):
    url = f"https://books.toscrape.com/catalogue/page-{n}.html"
    html = scraper.get(url)
    titles = [a["title"] for a in BeautifulSoup(html, "html.parser").select("article.product_pod h3 a")]
    total += len(titles)
    print(f"{time.strftime('%H:%M:%S')} → catalogue/page-{n}.html (книг: {len(titles)})")
print("Всего книг:", total)
```

**Задание 8**
```python
import time
import requests

def get_respectful(url, session=None):
    s = session or requests.Session()
    for attempt in range(2):
        r = s.get(url, timeout=10)
        if r.status_code not in (429, 503):
            return r
        wait = min(int(r.headers.get("Retry-After", "5")), 60)
        print(f"Код {r.status_code}: жду {wait} с")
        time.sleep(wait)
    return None

print(get_respectful("https://httpbin.org/status/429"))
```

**Задание 9**
```python
def parse_robots(text):
    groups, agents, current = {}, [], None
    for raw in text.splitlines():
        line = raw.split("#")[0].strip()
        if ":" not in line:
            continue
        key, value = (p.strip() for p in line.split(":", 1))
        key = key.lower()
        if key == "user-agent":
            if current is not None:          # началась новая группа
                agents, current = [], None
            agents.append(value.lower())
            groups.setdefault(value.lower(), [])
        elif key in ("allow", "disallow"):
            current = True
            for a in agents:
                groups[a].append((key, value))
    return groups

def allowed(groups, agent, path):
    rules = groups.get(agent.lower(), groups.get("*", []))
    best = ("allow", "")                      # по умолчанию: разрешено
    for kind, rule in rules:
        if rule and path.startswith(rule) and len(rule) >= len(best[1]):
            best = (kind, rule)
    return best[0] == "allow"

g = parse_robots("User-agent: *\nDisallow: /library/\nAllow: /library/public\n")
for p in ["/", "/library/x", "/library/public/list"]:
    print(p, allowed(g, "AnyBot", p))
```

</details>
