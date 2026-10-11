# Сабақ 64. time және datetime модульдері. Уақытпен жұмыс.

## Тақырыптар

1. time модулі
2. datetime модулі
3. timedelta – уақыт аралығы
4. Күнді пішімдеу: strftime()
5. Жолдан күнді оқу: strptime()
6. Уақыт пен файлды біріктіру: журнал
7. time пен datetime: қайсысын қашан қолданамыз?
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. time модулі

`time` модулі – уақытпен «төмен деңгейде» жұмыс: секундтарды санау, кідіріс жасау.

### Уақыт белгісі (timestamp)

Компьютер уақытты **1970 жылғы 1 қаңтардан** (UTC) бері өткен секундтар саны ретінде есептейді. Бұл сәт «Unix дәуірі» (epoch) деп аталады.

```python
import time

now = time.time()
print("1970 жылдан бері өткен секунд:", now)
print("Адам оқитын түрде:", time.ctime())
```

Нәтижесі (мысал):
```text
1970 жылдан бері өткен секунд: 1808713800.123456
Адам оқитын түрде: Mon Apr 26 14:30:00 2027
```

### sleep() – кідіріс

`time.sleep(секунд)` бағдарламаны көрсетілген уақытқа «ұйықтатады»:

```python
import time

print("Зымыран ұшуға дайындалуда...")
for i in range(5, 0, -1):
    print(i)
    time.sleep(1)
print("Ұшу!")
```

Нәтижесі (әр сан 1 секунд аралықпен шығады):
```text
Зымыран ұшуға дайындалуда...
5
4
3
2
1
Ұшу!
```

`sleep(0.5)` – жарты секунд. Бұл анимация, «баяу басылған мәтін» эффектісі, ойындағы кідірістер үшін қолданылады.

### Секундомер: орындалу уақытын өлшеу

```python
import time

start = time.time()
total = 0
for i in range(1_000_000):
    total += i
finish = time.time()
print("Қосынды:", total)
print(f"Уақыт: {finish - start:.3f} секунд")
```

Нәтижесі (компьютерге байланысты):
```text
Қосынды: 499999500000
Уақыт: 0.084 секунд
```

(`1_000_000` – оқуға ыңғайлы болу үшін сан ішіне астын сызу қойылған, Python оны 1000000 деп оқиды. Дәлірек өлшеу үшін `time.perf_counter()` бар.)

### Реакция ойыны

```python
import random
import time

print("Дайын бол! 'ЕНДІ!' деген жазу шыққанда Enter бас.")
time.sleep(random.uniform(2, 5))
start = time.time()
input("ЕНДІ!")
reaction = time.time() - start
print(f"Сенің реакцияң: {reaction:.3f} секунд")
if reaction < 0.3:
    print("Керемет! Нағыз ниндзя!")
```

### localtime() – уақыттың бөліктері

```python
import time

t = time.localtime()
print("Жыл:", t.tm_year)
print("Ай:", t.tm_mon)
print("Күн:", t.tm_mday)
print(f"Уақыт: {t.tm_hour:02d}:{t.tm_min:02d}")
```

## 2. datetime модулі

`datetime` – күн мен уақытпен ыңғайлы жұмыс: күнтізбе, айырма, пішімдеу. Модульде бірнеше **класс** (объект түрі) бар:

| Класс | Нені сақтайды | Мысал |
|---|---|---|
| `date` | Күн: жыл, ай, күн | `2027-04-26` |
| `time` | Уақыт: сағат, минут, секунд | `14:30:00` |
| `datetime` | Күн + уақыт | `2027-04-26 14:30:00` |
| `timedelta` | Уақыт аралығы (айырма) | `29 days` |

```python
from datetime import date, datetime, timedelta
```

> Модуль мен класс атаулары бірдей – `datetime`. Шатаспау үшін жоғарыдағыдай `from datetime import ...` деп жазған ыңғайлы.

### Бүгінгі күн және қазіргі уақыт

```python
from datetime import date, datetime

today = date.today()
now = datetime.now()
print("Бүгін:", today)
print("Қазір:", now)
print("Жыл:", today.year, "Ай:", today.month, "Күн:", today.day)
print("Сағат:", now.hour, "Минут:", now.minute)
```

Нәтижесі (мысал):
```text
Бүгін: 2027-04-26
Қазір: 2027-04-26 14:30:15.482913
Жыл: 2027 Ай: 4 Күн: 26
Сағат: 14 Минут: 30
```

### Нақты күн құру

```python
from datetime import date, datetime

independence = date(1991, 12, 16)
lesson = datetime(2027, 4, 26, 14, 30)
print(independence)
print(lesson)
```

Нәтижесі:
```text
1991-12-16
2027-04-26 14:30:00
```

Жоқ күнді құрсаң – `ValueError`: `date(2027, 2, 30)` → `day is out of range for month`.

### Апта күні

`weekday()` – дүйсенбі = 0, …, жексенбі = 6. Қазақша атауларды тізімнен аламыз:

```python
from datetime import date

week = ["Дүйсенбі", "Сейсенбі", "Сәрсенбі", "Бейсенбі", "Жұма", "Сенбі", "Жексенбі"]
d = date(1991, 12, 16)
print("Тәуелсіздік күні аптаның", week[d.weekday()], "күні болған")
```

Нәтижесі:
```text
Тәуелсіздік күні аптаның Дүйсенбі күні болған
```

## 3. timedelta – уақыт аралығы

**Екі күннің айырмасы** – `timedelta` объектісі:

```python
from datetime import date

start = date(2027, 4, 26)
summer = date(2027, 5, 25)
left = summer - start
print("Айырма:", left)
print("Демалысқа", left.days, "күн қалды")
```

Нәтижесі:
```text
Айырма: 29 days, 0:00:00
Демалысқа 29 күн қалды
```

**Күнге уақыт қосу:**

```python
from datetime import date, datetime, timedelta

d = date(2027, 4, 26)
print("100 күннен кейін:", d + timedelta(days=100))
print("Бір апта бұрын:", d - timedelta(weeks=1))

lesson = datetime(2027, 4, 26, 14, 30)
end = lesson + timedelta(hours=1, minutes=40)
print("Сабақ аяқталады:", end.strftime("%H:%M"))
```

Нәтижесі:
```text
100 күннен кейін: 2027-08-04
Бір апта бұрын: 2027-04-19
Сабақ аяқталады: 16:10
```

`timedelta` параметрлері: `weeks`, `days`, `hours`, `minutes`, `seconds`. `.days` – тұтас күндер, `.total_seconds()` – барлық секунд.

**Күндерді салыстыру:** `<`, `>`, `==` жұмыс істейді:

```python
from datetime import date

deadline = date(2027, 5, 10)
if date(2027, 4, 26) < deadline:
    print("Жобаны тапсыруға әлі уақыт бар")
```

## 4. Күнді пішімдеу: strftime()

`strftime(пішім)` – күнді қалаған түрдегі **жолға** айналдырады (string format time).

| Код | Мағынасы | Мысал |
|---|---|---|
| `%d` | Күн (01–31) | `26` |
| `%m` | Ай нөмірі (01–12) | `04` |
| `%Y` | Жыл (4 цифр) | `2027` |
| `%y` | Жыл (2 цифр) | `27` |
| `%H` | Сағат (00–23) | `14` |
| `%M` | Минут | `30` |
| `%S` | Секунд | `05` |
| `%A` / `%B` | Апта күні / ай атауы (ағылшынша) | `Monday` / `April` |

```python
from datetime import datetime

dt = datetime(2027, 4, 26, 14, 30, 5)
print(dt.strftime("%d.%m.%Y"))
print(dt.strftime("%H:%M:%S"))
print(dt.strftime("Күні: %d.%m.%y, уақыты: %H:%M"))
```

Нәтижесі:
```text
26.04.2027
14:30:05
Күні: 26.04.27, уақыты: 14:30
```

Ай атауын қазақша шығару үшін өз тізімімізді қолданамыз:

```python
from datetime import date

months = ["қаңтар", "ақпан", "наурыз", "сәуір", "мамыр", "маусым",
          "шілде", "тамыз", "қыркүйек", "қазан", "қараша", "желтоқсан"]
d = date(2027, 4, 26)
print(f"{d.year} жылғы {d.day} {months[d.month - 1]}")
```

Нәтижесі:
```text
2027 жылғы 26 сәуір
```

## 5. Жолдан күнді оқу: strptime()

Пайдаланушы күнді мәтін ретінде енгізеді – оны `datetime.strptime(жол, пішім)` арқылы күнге айналдырамыз (string parse time):

```python
from datetime import datetime

text = "16.12.2027"
d = datetime.strptime(text, "%d.%m.%Y").date()
print(d, type(d))
```

Нәтижесі:
```text
2027-12-16 <class 'datetime.date'>
```

Пішімге сәйкес келмесе – `ValueError`. Сондықтан `try...except` қолданамыз:

### Мысал: жас калькуляторы

```python
from datetime import date, datetime

text = input("Туған күніңді енгіз (КК.АА.ЖЖЖЖ): ")
try:
    birthday = datetime.strptime(text, "%d.%m.%Y").date()
except ValueError:
    print("Күн пішімі қате. Мысалы: 15.07.2013")
else:
    today = date.today()
    days_lived = (today - birthday).days
    age = today.year - birthday.year
    if (today.month, today.day) < (birthday.month, birthday.day):
        age -= 1       # биыл туған күн әлі болған жоқ
    print("Сенің жасың:", age)
    print("Сен өмір сүрген күндер:", days_lived)
```

Нәтижесі (бүгін 26.04.2027 болса):
```text
Туған күніңді енгіз (КК.АА.ЖЖЖЖ): 15.07.2013
Сенің жасың: 13
Сен өмір сүрген күндер: 5033
```

(Кортеждерді салыстыру `(4, 26) < (7, 15)` алдымен айды, тең болса күнді салыстырады: кортеждер элемент бойынша салыстырылады.)

## 6. Уақыт пен файлды біріктіру: журнал

Файлға қосып жазуды (`a` режимі) уақыт белгісімен біріктірейік – кез келген нақты бағдарламада осындай **лог** бар:

```python
from datetime import datetime

action = input("Не істедің? ")
stamp = datetime.now().strftime("%d.%m.%Y %H:%M")
with open("log.txt", "a", encoding="utf-8") as f:
    f.write(f"[{stamp}] {action}\n")
print("Журналға жазылды.")
```

`log.txt` (мысал):
```text
[26.04.2027 14:35] Python сабағына келдім
[26.04.2027 15:10] datetime модулін үйрендім
```

## 7. time пен datetime: қайсысын қашан қолданамыз?

| Міндет | Модуль |
|---|---|
| Кідіріс жасау | `time.sleep()` |
| Кодтың жылдамдығын өлшеу | `time.time()` / `time.perf_counter()` |
| Бүгінгі күн, апта күні | `datetime.date.today()` |
| Күндер айырмасы, күнге қосу | `datetime` + `timedelta` |
| Күнді әдемі шығару / мәтіннен оқу | `strftime()` / `strptime()` |

## Жиі кездесетін қателер

**1. `datetime` атауын шатастыру.**

```python
import datetime
# datetime.now()  -> AttributeError: module 'datetime' has no attribute 'now'
print(datetime.datetime.now())   # модуль.класс.әдіс
```

Немесе: `from datetime import datetime` → `datetime.now()`.

**2. Пайдаланушы енгізген жолды күнмен салыстыру.** `"16.12.2027" < date.today()` → `TypeError`. Алдымен `strptime` арқылы түрлендір.

**3. Пішімдегі әріп регистрі.** `%m` – ай, `%M` – минут! `%Y` – 4 таңбалы жыл, `%y` – 2 таңбалы.

**4. `timedelta`-дан айларды сұрау.** `timedelta(months=1)` → `TypeError`: айлар ұзындығы әртүрлі болғандықтан, `timedelta`-да `months` жоқ. `days=30` қолдан немесе айды қолмен өзгерт.

**5. `sleep`-ті миллисекунд деп ойлау.** `time.sleep(500)` – 500 миллисекунд емес, 500 **секунд** (8 минуттан астам)! Жарты секунд – `time.sleep(0.5)`.

**6. Тек жылдардың айырмасымен жасты есептеу.** Туған күн биыл әлі болмаса, жас бір жылға артық шығады – айы мен күнін де ескер.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Уақыт белгісі (timestamp) | 1970.01.01-ден бері өткен секунд саны |
| `time.sleep(s)` | Бағдарламаны s секундқа кідірту |
| `time.time()` | Ағымдағы уақыт белгісі |
| `date` | Күнді (жыл, ай, күн) сақтайтын класс |
| `datetime` | Күн мен уақытты сақтайтын класс |
| `timedelta` | Уақыт аралығы |
| `weekday()` | Апта күнінің нөмірі (0 – дүйсенбі) |
| `strftime()` | Күнді жолға пішімдеу |
| `strptime()` | Жолды күнге түрлендіру |

## Ескерту

- `datetime.now()` және `date.today()` уақыт белдеуі жоқ **аңғал** (naive) уақытты қайтарады. Белдеулермен жұмыс үшін `datetime.now(timezone.utc)` немесе `zoneinfo` модулін (Python 3.9+) қолдан: `datetime.now(ZoneInfo("Asia/Almaty"))`.
- `datetime.utcnow()` Python 3.12-ден бастап ескірген деп белгіленді – орнына `datetime.now(timezone.utc)` жаз.
- Код жылдамдығын өлшеу үшін `time.perf_counter()` (ең дәл санауыш, сағатты ауыстыруға тәуелсіз) немесе `timeit` модулі жақсырақ; `time.time()` жүйелік сағат синхрондалғанда «секіруі» мүмкін.
- `%A`/`%B` кодтарындағы ай мен күн атаулары жүйе локаліне байланысты; жоғарыдағы мысалдардағыдай өз атаулар тізімін сақтау сенімдірек.
- ISO 8601 форматын (`2027-04-26T14:30:00`) `isoformat()` және `datetime.fromisoformat()` әдістерімен оқу-жазу ыңғайлы – бұл файлдарда күнді сақтаудың стандартты тәсілі.
- Құжаттама: [datetime](https://docs.python.org/3/library/datetime.html), [time](https://docs.python.org/3/library/time.html), [strftime кодтары](https://docs.python.org/3/library/datetime.html#format-codes).
