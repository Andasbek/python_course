# Сабақ 96. Практикалық тапсырмалар: деректермен жұмыс бөлігі

> Негізгі уақыт — өз жобаңыздың деректер қабаты. Жобаңызда қай технология қолданылатынына қарай 1-тапсырманың **А** (SQLite) немесе **Ә** (JSON) нұсқасын орындаңыз; API бар жобалар 3-тапсырманы да орындайды.

## Базалық деңгей

### 1-тапсырма. Деректер модулі (міндетті)
**А. SQLite.** `database.py` ішінде `Database` класын жазыңыз:
- конструктор: қосылым, `PRAGMA foreign_keys = ON`, `executescript(SCHEMA)`, қажет болса әдепкі деректер;
- әр негізгі кесте үшін кемінде: `add_...`, `list_...` (немесе `get_...`), `delete_...`; қажет болса `update_...`;
- тек `?` параметрлері, `with self.conn:` транзакциялары;
- `close()` әдісі.

**Ә. JSON.** `storage.py` ішінде `load_json` / `save_json` функцияларын (файл жоқ, бүлінген жағдайларымен) және жобаңызға қажет әрекеттерді жазыңыз (рекорд қосу, баптауларды сақтау, сұрақтар жүктеу т.б.).

Файл соңында `if __name__ == "__main__":` блогы `:memory:` ДҚ-мен (немесе уақытша файлмен) барлық әдісті тексерсін.

### 2-тапсырма. Логиканы байланыстыру (міндетті)
94–95 сабақтардағы логика кластарын деректер қабатына қосыңыз: логика қойма объектісін конструктор арқылы алады және оның әдістерін шақырады. Логика ішінде `sqlite3`, `open` немесе `requests` болмауы керек.

### 3-тапсырма. API модулі (API бар жобаларға)
API-мен жұмысты бөлек модульге шығарыңыз (`weather.py`, `currency.py` т.б.):
- `timeout=5`, `raise_for_status()`, `requests.RequestException`-ды ұстау;
- кэш немесе офлайн режим;
- функция пайдаланушыға дайын мәтін емес, **деректерді** қайтарады.

### 4-тапсырма. Қайта іске қосу тесті
Теориядағы 8-бөлімнің 5 қадамын орындап, нәтижесін `docs/test_log.md` файлына жазыңыз:

| № | Қадам | Күтілген | Нақты | Өтті ме |
|---|---|---|---|---|
| 1 | 3 жазба қосып, бағдарламаны жабу | — | — | — |
| 2 | Қайта ашу | 3 жазба көрінеді | ... | иә/жоқ |

## Кеңейтілген деңгей

### 5-тапсырма. SQL-инъекцияны табу
Төмендегі кодта қандай қауіп бар? Оны түзетіңіз және тырнақшасы бар атпен (`Қожа'Ахмет`) тексеріңіз.

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT, grade INTEGER)")


def add_student(name, grade):
    conn.execute(f"INSERT INTO student (name, grade) VALUES ('{name}', {grade})")
    conn.commit()


def find(name):
    return conn.execute(f"SELECT * FROM student WHERE name = '{name}'").fetchall()


add_student("Әйгерім", 8)
print(find("Әйгерім"))
```

### 6-тапсырма. Сақтық көшірме (backup)
`Database` класына `backup(folder)` әдісін қосыңыз: ДҚ-ның көшірмесін `folder/qalta_ЖЖЖЖ-АА-КК_ССММ.db` атымен жасайды және 5-тен көп ескі көшірме болса, ең ескілерін өшіреді. *Кеңес:* `sqlite3.Connection.backup()` әдісі ашық ДҚ-ны да қауіпсіз көшіреді.

### 7-тапсырма. CSV импорт
`import_csv(path)` функциясын жазыңыз: CSV-ден операцияларды оқып, ДҚ-ға қосады. Қате жолдарды (сома сан емес, белгісіз санат) өткізіп жіберіп, соңында есеп шығарады.

Шығару мысалы:
```text
Импортталды: 18 жол, өткізілді: 2 (3-жол: сома 'жүз'; 11-жол: белгісіз санат 'Спорт')
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Схеманың нұсқасын жаңарту (миграция)
Жоба шыққаннан кейін `operation` кестесіне жаңа `place TEXT` өрісі керек болды делік. Пайдаланушылардың ескі ДҚ-лары жоғалмауы керек. `PRAGMA user_version` арқылы схема нұсқасын сақтап, ескі ДҚ ашылғанда `ALTER TABLE ... ADD COLUMN` автоматты орындалатын `migrate(conn)` функциясын жазыңыз.

### 9-тапсырма. Кэштің жарамдылығы
`get_kzt_rate()` функциясын жақсартыңыз: кэштен алынған бағам 24 сағаттан ескі болса, функция үшінші мән ретінде «ескірген» белгісін қайтарсын, ал интерфейс «Бағам 26.04.27 14:05 бойынша» деп көрсете алатындай уақытты да қайтарсын.

## Үй тапсырмасы
1. Деректер қабатын аяқтап, қайта іске қосу тестінен өткізіңіз. `push`.
2. `.gitignore`-да ДҚ файлы мен кэш файлдары бар екенін тексеріңіз.
3. 92-сабақтағы прототипті ашып, қай батырма қай логика/қойма әдісін шақыратынын тізім түрінде жазыңыз — бұл келесі сабақтың жоспары.

## Бағалау критерийлері
| Критерий | Үлес | Не тексеріледі |
|---|---|---|
| Деректер дұрыс сақталып, жүктеледі | 40% | қайта іске қосу тесті өтті, CRUD жұмыс істейді |
| Сенімділік пен қауіпсіздік | 25% | параметрленген сұраныстар, транзакция, файл/API қателері өңделген |
| Код құрылымы | 20% | деректер қабаты бөлек, логикада SQL/`open` жоқ, docstring |
| Өз бетінше жұмыс, коммиттер | 15% | мағыналы коммиттер, `push` |

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**1–4-тапсырмалар** жеке тексеріледі. Тексеру парағы: (1) `grep -n "execute(f" *.py` — бос болуы керек; (2) логика файлында `import sqlite3` жоқ; (3) ДҚ файлын өшіріп іске қосқанда бағдарлама қатесіз жаңасын құрады; (4) интернетті өшіргенде API-жоба құламайды.

**5-тапсырма.** Қауіп: SQL-инъекция; сонымен қатар `Қожа'Ахмет` есімі синтаксистік қате береді.
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT, grade INTEGER)")


def add_student(name, grade):
    with conn:
        conn.execute("INSERT INTO student (name, grade) VALUES (?, ?)", (name, grade))


def find(name):
    return conn.execute("SELECT * FROM student WHERE name = ?", (name,)).fetchall()


add_student("Әйгерім", 8)
add_student("Қожа'Ахмет", 9)
print(find("Әйгерім"))
print(find("Қожа'Ахмет"))
print(find("x' OR '1'='1"))
```
Нәтижесі:
```text
[(1, 'Әйгерім', 8)]
[(2, "Қожа'Ахмет", 9)]
[]
```

**6-тапсырма.**
```python
import datetime
import sqlite3
from pathlib import Path


class Database:
    def __init__(self, path):
        self.conn = sqlite3.connect(path)

    def backup(self, folder, keep=5):
        """ДҚ көшірмесін жасайды, ең жаңа `keep` көшірмені қалдырады."""
        folder = Path(folder)
        folder.mkdir(parents=True, exist_ok=True)
        stamp = datetime.datetime.now().strftime("%Y-%m-%d_%H%M%S")
        target = folder / f"qalta_{stamp}.db"
        dest = sqlite3.connect(target)
        with dest:
            self.conn.backup(dest)
        dest.close()
        copies = sorted(folder.glob("qalta_*.db"))   # атауындағы уақыт бойынша
        for old in copies[:-keep]:
            old.unlink()
        return target


if __name__ == "__main__":
    db = Database(":memory:")
    db.conn.execute("CREATE TABLE t (x INTEGER)")
    print("Көшірме:", db.backup("backups").name)
```
(Тапсырмадағы `ССММ` форматына секунд қосылды — бір минутта екі көшірме жасалса, атаулар қайталанбайды.)

**7-тапсырма.**
```python
import csv
import io

CATEGORIES = {"Тамақ": 3, "Көлік": 4, "Ойын-сауық": 5}


def import_csv(file, add_operation):
    """CSV (op_date;category;amount;note) -> add_operation(...). Есеп қайтарады."""
    ok, skipped = 0, []
    reader = csv.DictReader(file, delimiter=";")
    for line_no, row in enumerate(reader, start=2):     # 1-жол — тақырып
        try:
            amount = int(row["amount"])
            if amount <= 0:
                raise ValueError
        except (ValueError, TypeError):
            skipped.append(f"{line_no}-жол: сома '{row['amount']}'")
            continue
        cat_id = CATEGORIES.get(row["category"].strip())
        if cat_id is None:
            skipped.append(f"{line_no}-жол: белгісіз санат '{row['category']}'")
            continue
        add_operation(cat_id, amount, row["op_date"], row.get("note", ""))
        ok += 1
    return f"Импортталды: {ok} жол, өткізілді: {len(skipped)} ({'; '.join(skipped)})"


data = io.StringIO("op_date;category;amount;note\n"
                   "2027-04-01;Тамақ;800;ас\n"
                   "2027-04-02;Тамақ;жүз;\n"
                   "2027-04-03;Спорт;500;\n")
saved = []
print(import_csv(data, lambda *args: saved.append(args)))
print(saved)
```
Нәтижесі:
```text
Импортталды: 1 жол, өткізілді: 2 (3-жол: сома 'жүз'; 4-жол: белгісіз санат 'Спорт')
[(3, 800, '2027-04-01', 'ас')]
```

**8-тапсырма.**
```python
import sqlite3

MIGRATIONS = {
    1: "CREATE TABLE operation (id INTEGER PRIMARY KEY, amount INTEGER, op_date TEXT)",
    2: "ALTER TABLE operation ADD COLUMN place TEXT DEFAULT ''",
}


def migrate(conn):
    """Схеманы соңғы нұсқаға дейін жаңартады."""
    version = conn.execute("PRAGMA user_version").fetchone()[0]
    for v in sorted(MIGRATIONS):
        if v > version:
            with conn:
                conn.execute(MIGRATIONS[v])
                conn.execute(f"PRAGMA user_version = {v}")   # PRAGMA параметр қабылдамайды; v — біздің санымыз
            print(f"Схема {v}-нұсқаға жаңартылды")


conn = sqlite3.connect(":memory:")
migrate(conn)
migrate(conn)          # екінші рет ештеңе істемейді
cols = [c[1] for c in conn.execute("PRAGMA table_info(operation)")]
print(cols)
```
Нәтижесі:
```text
Схема 1-нұсқаға жаңартылды
Схема 2-нұсқаға жаңартылды
['id', 'amount', 'op_date', 'place']
```

**9-тапсырма.**
```python
import datetime
import json
import time

MAX_AGE = 24 * 60 * 60


def rate_from_cache(cache_text, now=None):
    """(бағам, уақыт_мәтіні, ескірген_бе) қайтарады."""
    now = now or time.time()
    cache = json.loads(cache_text)
    stamp = datetime.datetime.fromtimestamp(cache["time"]).strftime("%d.%m.%y %H:%M")
    return cache["rate"], stamp, now - cache["time"] > MAX_AGE


saved = json.dumps({"rate": 512.3, "time": time.time() - 2 * MAX_AGE})
rate, stamp, stale = rate_from_cache(saved)
print(rate, stale)
```
Нәтижесі (уақыт мәтіні іске қосылған күнге байланысты):
```text
512.3 True
```
Толық шешімде `get_kzt_rate()` осы үш мәнді қайтарады, ал API сәтті болғанда `stale=False`.
</details>
