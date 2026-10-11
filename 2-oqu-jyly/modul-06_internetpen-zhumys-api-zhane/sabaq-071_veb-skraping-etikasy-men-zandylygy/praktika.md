# Сабақ 71. Практикалық тапсырмалар: Скрапинг этикасы, robots.txt, жеке деректер

## Базалық деңгей

### 1-тапсырма. robots.txt оқу (браузерде + дәптерде)
Мына сайттардың robots.txt файлдарын ашып, кестені толтырыңыз:
`https://www.wikipedia.org/robots.txt`, `https://github.com/robots.txt`, `https://books.toscrape.com/robots.txt`, `https://www.google.com/robots.txt`.

| Сайт | `User-agent: *` үшін тыйым салынған 2 бөлім | `Crawl-delay` бар ма | `Sitemap` бар ма |
|---|---|---|---|

Ойланыңыз: robots.txt файлы жоқ сайт (404) нені білдіреді?

### 2-тапсырма. robots.txt талдаушы
Төмендегі ережелерді `RobotFileParser.parse()` арқылы жүктеп, URL тізімінің әрқайсысы `StudentBot` үшін рұқсат етілген бе, жоқ па — шығарыңыз. Сондай-ақ `crawl_delay` мәнін көрсетіңіз.

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
/                       → рұқсат
/news/2027/olympiad     → рұқсат
/library/books          → тыйым
...
Crawl-delay: 2 с
```

Ескерту: `StudentBot` үшін өз блогы бар — ол `*` блогын қолданбайды! Нәтижені осы тұрғыдан түсіндіріңіз.

### 3-тапсырма. Кідірісті санау
Скрапер 120 бетті жүктеуі керек. `Crawl-delay: 5`, әр сұраныс орта есеппен 0.4 с. Бүкіл жұмыс неше минутқа созылады? Бағдарлама жазып есептеңіз, нәтижені минут пен секундпен шығарыңыз. Кідіріс болмаса — қанша? Серверге қандай айырмашылық?

### 4-тапсырма. Жеке деректерді маскалау
`mask_personal(text)` функциясы мәтіндегі email-дерді (`ai***@mail.kz`), телефондарды (`+7 701 *** ** 67`) және 12 таңбалы ЖСН (ИИН) нөмірлерін (`0503******12`) маскаласын.

```text
Кіріс:  Оқушы: Дана, ЖСН 050312650412, тел 87071234567, dana.o@gmail.com
Шығыс:  Оқушы: Дана, ЖСН 0503******12, тел 8 707 *** ** 67, da***@gmail.com
```

### 5-тапсырма. Жағдаяттар (кейстер) — топтық талқылау
Әр жағдай үшін «Этикалық па? Заңды ма? Қалай дұрыс істеу керек?» деп жауап беріңіз:
1. Ауа райы сайтының API-ы бар, бірақ Әсет HTML бетті секунд сайын скрапинг жасайды.
2. Мадина Kaspi тауарларының бағасын күніне бір рет, 50 тауар бойынша жинап, өз ноутбугында бағаның өзгеру графигін жасайды. Сайт ережелерін тексермеген.
3. Ерлан мектеп чатынан барлық оқушының аты мен телефонын жинап, «Біздің сынып» сайтына жариялады.
4. Айбек Wikipedia-дан Қазақстан қалалары кестесін алып, жобасында дереккөзін көрсетіп қолданды.
5. Бот VK/Instagram-нан «16 жастағы алматылықтардың» профильдерін жинап, жарнама компаниясына сатады.

## Кеңейтілген деңгей

### 6-тапсырма. Әдепті скрапер
Теориядағы `PoliteScraper` класын пайдаланып, `books.toscrape.com` сайтының алғашқы 3 бетінен кітап атауларын жинаңыз. Әр сұраныстың уақытын (`time.strftime("%H:%M:%S")`) шығарып, кідірістің сақталғанын дәлелдеңіз.

```text
12:00:01 → catalogue/page-1.html (20 кітап)
12:00:02 → catalogue/page-2.html (20 кітап)
12:00:03 → catalogue/page-3.html (20 кітап)
Барлығы: 60 кітап
```

### 7-тапсырма. Дискілік кэш
`PoliteScraper` класына дискілік кэш қосыңыз: беттер `cache/` папкасында сақталады (файл атауы — URL-дің `hashlib.md5` хэші). Бет кэште болса — желіге сұраныс жіберілмейді. Екінші іске қосуда бағдарлама бірден жұмыс істейтінін көрсетіңіз.

### 8-тапсырма. Retry-After
Сервер 429 немесе 503 кодын `Retry-After: 10` тақырыбымен қайтарса, скрапер көрсетілген секундты күтіп (бірақ 60 с-тан артық емес), бір рет қайталасын. `https://httpbin.org/status/429` арқылы тексеріңіз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Өз robots.txt талдағышыңыз
`RobotFileParser`-сіз қарапайым талдағыш жазыңыз: `User-agent` блоктарын, `Allow`/`Disallow` ережелерін оқиды; URL үшін **ең ұзын** сәйкес ережені таңдайды (Google тәсілі); өз блогы бар роботқа тек сол блокты қолданады. 2-тапсырма деректерімен `RobotFileParser` нәтижесімен салыстырыңыз.

### 10-тапсырма. Этика кодексінің плакаты
«Жауапты скрапер кодексі» инфографикасын жасаңыз (7 ереже, мысалдармен). Үздік жұмыс кабинетке ілінеді.

## Үй тапсырмасы

1. Өзіңіз жиі қолданатын 2 сайттың «Пайдалану шарттары» бетін тауып, автоматты деректер жинау туралы не жазылғанын қысқаша конспектілеңіз.
2. 70-сабақтағы скрапер кодыңызды «әдепті» етіп қайта жазыңыз (robots.txt, User-Agent, кідіріс).
3. Келесі сабаққа: 66–67-сабақтардағы ауа райы кодын, 62-сабақтағы Tkinter құрылымын қайталаңыз — практикум болады.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма** — нақты файлдар өзгеруі мүмкін; бағалау: кесте толтырылған, robots.txt жоқ болса «шектеу жарияланбаған, бірақ ToS-ты тексеру керек» деген түсінік.

**2-тапсырма**
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
    print(f"{path:22} → {'рұқсат' if ok else 'тыйым'}")
print("Crawl-delay:", rp.crawl_delay("StudentBot"), "с")
```
Түсіндірме: `StudentBot` үшін тек өз блогы қолданылады, сондықтан `/students/9a` және `/docs/plan.pdf` — рұқсат, `/library/public/list` — тыйым (StudentBot блогында `Allow: /library/public` жоқ). Сонымен қатар стандартты `robotparser` `*` маскасын (`/*.pdf`) қолдамайды.

**3-тапсырма**
```python
pages, delay, request_time = 120, 5, 0.4
total = pages * request_time + (pages - 1) * delay
no_delay = pages * request_time
print(f"Кідіріспен: {int(total // 60)} мин {total % 60:.0f} с")
print(f"Кідіріссіз: {int(no_delay // 60)} мин {no_delay % 60:.0f} с")
print(f"Серверге жүктеме ≈ {total / no_delay:.0f} есе аз")
```

**4-тапсырма**
```python
import re

EMAIL = re.compile(r"\b([\w.]{2})[\w.]*@([\w-]+\.[a-z]{2,})\b")
PHONE = re.compile(r"(\+7|8)[\s-]?(\d{3})[\s-]?\d{3}[\s-]?\d{2}[\s-]?(\d{2})\b")
IIN = re.compile(r"\b(\d{4})\d{6}(\d{2})\b")

def mask_personal(text):
    text = EMAIL.sub(r"\1***@\2", text)
    text = IIN.sub(r"\1******\2", text)         # ЖСН-ді телефоннан бұрын
    text = PHONE.sub(r"\1 \2 *** ** \3", text)
    return text

print(mask_personal("Оқушы: Дана, ЖСН 050312650412, тел 87071234567, dana.o@gmail.com"))
```

**5-тапсырма — күтілетін жауаптар**
1. Этикаға қайшы: API бар, серверді жүктейді → API қолдану, жиілікті азайту.
2. Жиілігі әдепті, бірақ ToS пен robots.txt тексерілмеген → алдымен тексеру, тек жеке талдау үшін.
3. Заңсыз: дербес деректерді келісімсіз тарату → жариялауды тоқтату, деректерді өшіру.
4. Дұрыс: ашық лицензиялы мазмұн, дереккөз көрсетілген, Wikipedia-ның API-ы да бар.
5. Заңсыз әрі этикаға қайшы: кәмелетке толмағандардың дербес деректерін жинау және сату.

**6–7-тапсырмалар** (`PoliteScraper` теориядан, `polite.py` файлында деп есептейміз)
```python
import hashlib
import time
from pathlib import Path
from bs4 import BeautifulSoup
from polite import PoliteScraper   # теориядағы класс polite.py файлында

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
    print(f"{time.strftime('%H:%M:%S')} → catalogue/page-{n}.html ({len(titles)} кітап)")
print("Барлығы:", total, "кітап")
```

**8-тапсырма**
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
        print(f"Код {r.status_code}: {wait} с күтемін")
        time.sleep(wait)
    return None

print(get_respectful("https://httpbin.org/status/429"))
```

**9-тапсырма**
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
            if current is not None:          # жаңа топ басталды
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
    best = ("allow", "")                      # әдепкі: рұқсат
    for kind, rule in rules:
        if rule and path.startswith(rule) and len(rule) >= len(best[1]):
            best = (kind, rule)
    return best[0] == "allow"

g = parse_robots("User-agent: *\nDisallow: /library/\nAllow: /library/public\n")
for p in ["/", "/library/x", "/library/public/list"]:
    print(p, allowed(g, "AnyBot", p))
```

</details>
