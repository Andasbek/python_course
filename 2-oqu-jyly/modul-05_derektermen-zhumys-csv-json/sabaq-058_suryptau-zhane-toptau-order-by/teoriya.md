# Сабақ 58. Сұрыптау және топтау: ORDER BY, GROUP BY, агрегат функциялары.

## Тақырыптар

1. Жұмыс қоры: олимпиада нәтижелері
2. `ORDER BY` — сұрыптау
3. Агрегат функциялары
4. `GROUP BY` — топтау
5. `HAVING` — топтарды сүзу
6. `SELECT` сөйлемінің толық құрылымы
7. SQL пен Python-ды салыстыру
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Жұмыс қоры: олимпиада нәтижелері

```python
import sqlite3

RESULTS = [
    ("Айгерім", "9А", "Алматы", "информатика", 95),
    ("Айгерім", "9А", "Алматы", "математика", 82),
    ("Бауыржан", "9А", "Қонаев", "информатика", 78),
    ("Дәулет", "9Ә", "Алматы", "физика", 88),
    ("Дәулет", "9Ә", "Алматы", "информатика", 91),
    ("Сымбат", "9Ә", "Алматы", "математика", 100),
    ("Ерлан", "9А", "Талғар", "физика", 64),
    ("Мадина", "9Ә", "Алматы", "информатика", 91),
    ("Темірлан", "10А", "Қонаев", "математика", 73),
    ("Аружан", "10А", "Алматы", "физика", None),     # нәтижесі әлі жоқ
]

conn = sqlite3.connect("olympiad.db")
conn.executescript("""
    DROP TABLE IF EXISTS results;
    CREATE TABLE results (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        class TEXT,
        city TEXT,
        subject TEXT,
        score INTEGER
    );
""")
conn.executemany("INSERT INTO results (name, class, city, subject, score) VALUES (?, ?, ?, ?, ?)", RESULTS)
conn.commit()
conn.close()
print("olympiad.db: 10 нәтиже")
```

Нәтижесі:

```text
olympiad.db: 10 нәтиже
```

## 2. `ORDER BY` — сұрыптау

Әдепкіде `SELECT` нәтижесінің реті **кепілдендірілмеген** (көбіне қосылған реті, бірақ оған сенуге болмайды). Нақты рет керек болса — `ORDER BY`.

```sql
SELECT ... FROM ... [WHERE ...] ORDER BY өріс1 [ASC|DESC], өріс2 [ASC|DESC];
```

- `ASC` — өсу реті (әдепкі): 1, 2, 3 / А, Ә, Б;
- `DESC` — кему реті: 100, 95, 91 ...

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")

print("Балл бойынша (кему):")
for name, subject, score in conn.execute(
    "SELECT name, subject, score FROM results WHERE score IS NOT NULL ORDER BY score DESC"
):
    print(f"  {score:>3}  {name} ({subject})")

print("Пән бойынша, ішінде балл кемуі:")
for subject, name, score in conn.execute(
    "SELECT subject, name, score FROM results WHERE score IS NOT NULL ORDER BY subject, score DESC"
):
    print(f"  {subject:<12} {name:<9} {score}")
conn.close()
```

Нәтижесі:

```text
Балл бойынша (кему):
  100  Сымбат (математика)
   95  Айгерім (информатика)
   91  Дәулет (информатика)
   91  Мадина (информатика)
   88  Дәулет (физика)
   82  Айгерім (математика)
   78  Бауыржан (информатика)
   73  Темірлан (математика)
   64  Ерлан (физика)
Пән бойынша, ішінде балл кемуі:
  информатика  Айгерім   95
  информатика  Дәулет    91
  информатика  Мадина    91
  информатика  Бауыржан  78
  математика   Сымбат    100
  математика   Айгерім   82
  математика   Темірлан  73
  физика       Дәулет    88
  физика       Ерлан     64
```

**Бірнеше өріс бойынша сұрыптау:** алдымен бірінші өріс бойынша, бірінші өрістің мәндері тең болса — екінші өріс бойынша. Бұл телефон кітапшасындағыдай: алдымен тегі, тегі бірдей болса — аты.

> **Ескерту:** SQLite әдепкіде мәтінді байттар реті бойынша салыстырады. Сондықтан қазақ әріптері (Ә, Ғ, Қ, Ң, Ө, Ұ, Ү, Һ, І) әліпбидегі орнында емес, кириллицаның стандартты әріптерінен **кейін** тұруы мүмкін. Дәл әліпби реті керек болса — Python-да арнайы кілтпен сұрыптаймыз.

### `LIMIT` және `OFFSET` — «топ-N» және беттер

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")
top3 = conn.execute("SELECT name, score FROM results ORDER BY score DESC LIMIT 3").fetchall()
print("Үздік үштік:", top3)

page_size = 4
for page in (1, 2, 3):
    rows = conn.execute(
        "SELECT name FROM results ORDER BY id LIMIT ? OFFSET ?",
        (page_size, (page - 1) * page_size),
    ).fetchall()
    print(f"{page}-бет:", [r[0] for r in rows])
conn.close()
```

Нәтижесі:

```text
Үздік үштік: [('Сымбат', 100), ('Айгерім', 95), ('Дәулет', 91)]
1-бет: ['Айгерім', 'Айгерім', 'Бауыржан', 'Дәулет']
2-бет: ['Дәулет', 'Сымбат', 'Ерлан', 'Мадина']
3-бет: ['Темірлан', 'Аружан']
```

`OFFSET n` — алғашқы `n` жазбаны өткізіп жібер. Сайттардағы «1, 2, 3 ... келесі бет» батырмалары осылай жұмыс істейді.

`ORDER BY score DESC`-те `NULL` мәндері SQLite-та **ең соңында** тұрады (кему ретінде), өсу ретінде — ең басында.

## 3. Агрегат функциялары

**Агрегат функциясы** — көп жазбаның мәндерінен **бір** қорытынды мән есептейтін функция.

| Функция | Не есептейді | `NULL`-ға қатысты |
|---|---|---|
| `COUNT(*)` | жазбалар саны | барлық жазбаны санайды |
| `COUNT(өріс)` | өрісі бос емес жазбалар саны | `NULL`-ды санамайды |
| `COUNT(DISTINCT өріс)` | әртүрлі мәндер саны | |
| `SUM(өріс)` | қосындысы | `NULL`-ды елемейді |
| `AVG(өріс)` | арифметикалық ортасы | `NULL`-ды елемейді |
| `MIN(өріс)` / `MAX(өріс)` | ең кіші / ең үлкен | `NULL`-ды елемейді |

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")
row = conn.execute("""
    SELECT COUNT(*), COUNT(score), COUNT(DISTINCT name),
           SUM(score), ROUND(AVG(score), 1), MIN(score), MAX(score)
    FROM results
""").fetchone()
labels = ["Барлық жазба", "Нәтижесі бар", "Әртүрлі оқушы", "Қосынды", "Орташа", "Ең төмен", "Ең жоғары"]
for label, value in zip(labels, row):
    print(f"{label:<14}: {value}")
conn.close()
```

Нәтижесі:

```text
Барлық жазба  : 10
Нәтижесі бар  : 9
Әртүрлі оқушы : 8
Қосынды       : 762
Орташа        : 84.7
Ең төмен      : 64
Ең жоғары     : 100
```

Байқа: `COUNT(*)` = 10, ал `COUNT(score)` = 9 — Аружанның нәтижесі `NULL`. `AVG` те 9 мәнге бөлді (762 / 9 = 84.67).

`ROUND(мән, n)` — `n` таңбаға дейін дөңгелектеу.

Агрегатты `WHERE`-пен бірге:

```sql
SELECT COUNT(*), MAX(score) FROM results WHERE subject = 'информатика';   -- 4 және 95
```

## 4. `GROUP BY` — топтау

`GROUP BY` жазбаларды өрістің бірдей мәндері бойынша **топтарға** бөледі, ал агрегат функциялары **әр топ үшін бөлек** есептеледі.

**Аналогия:** барлық дәптерді сыныптар бойынша үйіп қояды (топтау), сосын әр үйдегі дәптерлерді санайды (COUNT) немесе орташа бағаны шығарады (AVG).

```text
results                         GROUP BY subject              Нәтиже
информатика  95  ─┐
информатика  78   ├─► информатика: 95,78,91,91 ─► AVG=88.8, COUNT=4
информатика  91   │
информатика  91  ─┘
математика   82  ─┐
математика  100   ├─► математика: 82,100,73    ─► AVG=85.0, COUNT=3
математика   73  ─┘
физика       88  ─┐
физика       64   ├─► физика: 88,64,NULL       ─► AVG=76.0, COUNT(score)=2
физика     NULL  ─┘
```

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")
print("Пәндер бойынша:")
for subject, count, avg, best in conn.execute("""
    SELECT subject, COUNT(score), ROUND(AVG(score), 1), MAX(score)
    FROM results
    GROUP BY subject
    ORDER BY AVG(score) DESC
"""):
    print(f"  {subject:<12} қатысушы: {count}, орташа: {avg}, үздік: {best}")

print("Сыныптар бойынша:")
for cls, avg in conn.execute(
    "SELECT class, ROUND(AVG(score), 1) AS avg_score FROM results GROUP BY class ORDER BY avg_score DESC"
):
    print(f"  {cls}: {avg}")
conn.close()
```

Нәтижесі:

```text
Пәндер бойынша:
  информатика  қатысушы: 4, орташа: 88.8, үздік: 95
  математика   қатысушы: 3, орташа: 85.0, үздік: 100
  физика       қатысушы: 2, орташа: 76.0, үздік: 88
Сыныптар бойынша:
  9Ә: 92.5
  9А: 79.8
  10А: 73.0
```

**Маңызды ереже:** `GROUP BY` бар сұраныстағы `SELECT` тізімінде тек (1) топтау өрістері және (2) агрегат функциялары болуы керек. `SELECT subject, name, AVG(score) ... GROUP BY subject` — `name` қай оқушының аты? Топта бірнеше оқушы бар! (SQLite қате бермейді, бірақ кездейсоқ бір атты қайтарады — бұл логикалық қате.)

### Бірнеше өріс бойынша топтау

```sql
SELECT city, subject, COUNT(*) FROM results GROUP BY city, subject;
-- әр (қала, пән) жұбы — жеке топ
```

## 5. `HAVING` — топтарды сүзу

`WHERE` **жазбаларды** топтауға **дейін** сүзеді. `HAVING` **топтарды** топтаудан **кейін** сүзеді, сондықтан оның ішінде агрегат функцияларын қолдануға болады.

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")

rows = conn.execute("""
    SELECT name, COUNT(*) AS olympiads, SUM(score) AS total
    FROM results
    GROUP BY name
    HAVING COUNT(*) >= 2
    ORDER BY total DESC
""").fetchall()
print("Екі және одан көп олимпиадаға қатысқандар:", rows)

rows = conn.execute("""
    SELECT city, ROUND(AVG(score), 1)
    FROM results
    WHERE subject = ?
    GROUP BY city
    HAVING AVG(score) > ?
""", ("информатика", 80)).fetchall()
print("Информатикадан орташасы 80-нен жоғары қалалар:", rows)
conn.close()
```

Нәтижесі:

```text
Екі және одан көп олимпиадаға қатысқандар: [('Дәулет', 2, 179), ('Айгерім', 2, 177)]
Информатикадан орташасы 80-нен жоғары қалалар: [('Алматы', 92.3)]
```

| | `WHERE` | `HAVING` |
|---|---|---|
| Нені сүзеді | жеке жазбаларды | топтарды |
| Қашан орындалады | топтауға дейін | топтаудан кейін |
| Агрегат функциялары | **болмайды** | болады |
| Мысал | `WHERE subject = 'физика'` | `HAVING COUNT(*) >= 2` |

## 6. `SELECT` сөйлемінің толық құрылымы

**Жазылу реті:**

```sql
SELECT    өрістер, агрегаттар
FROM      кесте
WHERE     жазбалар шарты
GROUP BY  топтау өрістері
HAVING    топтар шарты
ORDER BY  сұрыптау
LIMIT     саны OFFSET өткізу;
```

**Орындалу реті** (ДҚБЖ іштей):

```text
1. FROM      — қай кестеден?
2. WHERE     — қай жазбалар қалады?
3. GROUP BY  — қалай топтаймыз?
4. HAVING    — қай топтар қалады?
5. SELECT    — қандай бағандарды есептейміз?
6. ORDER BY  — қалай сұрыптаймыз?
7. LIMIT     — неше жол береміз?
```

Сондықтан `WHERE` ішінде `AS` арқылы берілген атауды (`avg_score`) қолдануға болмайды — ол әлі есептелмеген. Ал `ORDER BY` ішінде — болады.

**Есте сақтау үшін:** «**F**ROM — **W**HERE — **G**ROUP — **H**AVING — **S**ELECT — **O**RDER — **L**IMIT».

## 7. SQL пен Python-ды салыстыру

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")

# Python тәсілі: барлық деректерді алып, өзіміз есептейміз
by_city = {}
for city, score in conn.execute("SELECT city, score FROM results WHERE score IS NOT NULL"):
    by_city.setdefault(city, []).append(score)
python_result = {c: round(sum(s) / len(s), 1) for c, s in by_city.items()}

# SQL тәсілі: есептеуді деректер қоры жасайды
sql_result = dict(conn.execute(
    "SELECT city, ROUND(AVG(score), 1) FROM results WHERE score IS NOT NULL GROUP BY city"
))
print(python_result)
print(sql_result)
print("Бірдей ме?", python_result == sql_result)
conn.close()
```

Нәтижесі:

```text
{'Алматы': 91.2, 'Қонаев': 75.5, 'Талғар': 64.0}
{'Алматы': 91.2, 'Талғар': 64.0, 'Қонаев': 75.5}
Бірдей ме? True
```

(SQL нәтижесінде «Қонаев» «Талғар»-дан кейін тұр — `GROUP BY` топтарды байттар реті бойынша орналастырды, ал «Қ» әрпінің коды «Т»-дан үлкен. Бұл жоғарыдағы әліпби туралы ескертудің мысалы. Сөздіктер салыстырғанда рет маңызды емес, сондықтан `True`.)

Нәтиже бірдей, бірақ миллион жазбада SQL тәсілі әлдеқайда тиімді: деректер дискіден Python-ға тасымалданбайды, ДҚБЖ есептеуге оңтайландырылған.

## Жиі кездесетін қателер

**1. `WHERE` ішінде агрегат.**

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")
try:
    conn.execute("SELECT subject FROM results WHERE AVG(score) > 80 GROUP BY subject")
except sqlite3.OperationalError as e:
    print("Қате:", e)
print(conn.execute("SELECT subject FROM results GROUP BY subject HAVING AVG(score) > 80").fetchall())
conn.close()
```

Нәтижесі:

```text
Қате: misuse of aggregate: AVG()
[('информатика',), ('математика',)]
```

**2. `GROUP BY`-сыз агрегат пен қарапайым өріс.** `SELECT name, MAX(score) FROM results` — бір жол қайтарады; SQLite-та `name` максимумға сәйкес келеді (бұл SQLite-тың ерекшелігі, басқа ДҚБЖ-ларда қате). Түсінікті болу үшін: `ORDER BY score DESC LIMIT 1`.

**3. `ORDER BY`-сыз `LIMIT`.** «Үздік 3» деп `LIMIT 3` жазып, `ORDER BY` ұмытылса — кездейсоқ 3 жазба шығады.

**4. `COUNT(*)` пен `COUNT(өріс)`-ті шатастыру** — `NULL` бар өрістерде нәтиже әртүрлі.

**5. Бүтін сандарды бөлу.** SQLite-та `SUM(score) / COUNT(score)` екі бүтін санды бөлсе, нәтиже **бүтін** (бөлшегі тасталады): `762 / 9 = 84`. Дұрыс: `AVG(score)` немесе `SUM(score) * 1.0 / COUNT(score)`.

**6. `DESC` тек бір өріске әсер етеді.** `ORDER BY subject, score DESC` — `subject` өсу ретімен, тек `score` кему ретімен.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| `ORDER BY` | Нәтижені сұрыптау |
| `ASC` / `DESC` | Өсу / кему реті |
| `LIMIT` / `OFFSET` | Нәтиже санын шектеу / алғашқы жазбаларды өткізу |
| Пагинация | Үлкен нәтижені беттерге бөлу |
| Агрегат функциясы | Көп мәннен бір қорытынды есептейтін функция |
| `COUNT`, `SUM`, `AVG`, `MIN`, `MAX` | Саны, қосындысы, ортасы, ең кішісі, ең үлкені |
| `GROUP BY` | Жазбаларды бірдей мәндер бойынша топтау |
| `HAVING` | Топтарды агрегат шарты бойынша сүзу |
| `ROUND(x, n)` | Санды n таңбаға дейін дөңгелектеу |
| `COUNT(DISTINCT x)` | Әртүрлі мәндер саны |

## Ескерту

- Агрегат функциялары `NULL`-ды өткізіп жібереді: `COUNT(score)` тек толтырылған мәндерді, ал `COUNT(*)` — барлық жолды санайды; `AVG` тек бос емес мәндер санына бөледі.
- `AVG()` әрқашан бөлшек сан (`REAL`) қайтарады; дөңгелектеу үшін `ROUND(AVG(score), 1)` қолданыңыз.
- SQLite `SELECT`-те `GROUP BY`-ға кірмейтін және агрегатқа оралмаған өрістерге рұқсат береді, бірақ олардың мәні топтың кез келген жолынан алынады — PostgreSQL-де мұндай сұраныс қате береді. Тасымалданатын SQL жазыңыз.
- SQLite-та `ORDER BY ... ASC` кезінде `NULL` бірінші тұрады; 3.30 нұсқасынан бастап ретті анық беруге болады: `ORDER BY score DESC NULLS LAST`.
- Кириллицаны `ORDER BY` арқылы сұрыптау әліпби бойынша емес, таңба кодтары бойынша жүреді (қазақтың `ә`, `ң`, `ө` әріптері соңында қалады); дұрыс рет үшін Python-да сұрыптайды немесе `conn.create_collation()` арқылы өз салыстыру функциясын тіркейді.
- Құжаттама: https://www.sqlite.org/lang_aggfunc.html және https://www.sqlite.org/lang_select.html
