# Сабақ 64. Практикалық тапсырмалар: time және datetime

## Базалық деңгей

**1-тапсырма. Бүгін қандай күн?** Бүгінгі күнді үш түрде шығар: `2027-04-26`, `26.04.2027` және қазақша `2027 жылғы 26 сәуір, дүйсенбі`.

**2-тапсырма. Кері санақ.** Пайдаланушы секунд санын енгізеді. Бағдарлама әр секунд сайын қалған уақытты `ММ:СС` пішімінде шығарады, соңында «Уақыт бітті!» деп жазады.

```text
Неше секунд? 75
01:15
01:14
...
00:01
Уақыт бітті!
```

**3-тапсырма. Мерекеге дейін.** Келесі мерекелерге неше күн қалғанын есепте (бүгінгі күннен бастап): Наурыз (22 наурыз), Тәуелсіздік күні (16 желтоқсан), Жаңа жыл (1 қаңтар), өз туған күнің. Мереке биыл өтіп кетсе – келесі жылғы күнін ал.

**4-тапсырма. Апта күнін тап.** Пайдаланушы `КК.АА.ЖЖЖЖ` пішімінде күн енгізеді, бағдарлама сол күн аптаның қай күні екенін қазақша шығарады. Қате пішімді `try...except` арқылы өңде.

```text
Күн: 16.12.1991
Бұл – Дүйсенбі
```

**5-тапсырма. Баяу мәтін.** `type_text(text, delay)` функциясын жаз: ол мәтінді әріппен-әріп, әр таңбадан кейін `delay` секунд кідіріп шығарады (`print(ch, end="", flush=True)`). Ертегінің алғашқы сөйлемін «жазу машинкасы» эффектісімен шығар.

## Кеңейтілген деңгей

**6-тапсырма. Жас калькуляторы+.** Туған күн енгізіледі. Шығар: толық жас; өмір сүрген күндер, сағаттар, минуттар саны; келесі туған күнге неше күн қалғаны; 10 000-шы күнің қай күні болатыны.

**7-тапсырма. Жылдамдық тесті.** Бағдарлама кездейсоқ 5 сөзді (тізімнен) шығарып, оларды бір жолға тез теруді сұрайды. Теру уақытын өлшеп, қателер санын және минутына неше таңба (жылдамдық) екенін есепте.

```text
Тер: алма жаңбыр компьютер тау өзен
> алма жаңбыр компьютер тау өзен
Уақыт: 9.42 с, қате: 0, жылдамдық: 191 таңба/мин
```

**8-тапсырма. Сабақ кестесі.** Сабақ 14:00-де басталады, әр сабақ 45 минут, үзіліс 10 минут. 4 сабақтың басталу және аяқталу уақытын `timedelta` арқылы есептеп шығар.

```text
1-сабақ: 14:00 – 14:45
2-сабақ: 14:55 – 15:40
3-сабақ: 15:50 – 16:35
4-сабақ: 16:45 – 17:30
```

## ★ Олимпиадалық / шығармашылық тапсырма

**9-тапсырма. Күнтізбе.** Пайдаланушы жыл мен айды енгізеді. Бағдарлама сол айдың күнтізбесін апта күндерімен кесте түрінде шығарады (айдың бірінші күні аптаның қай күні екенін `weekday()` арқылы анықта, айдағы күндер санын – келесі айдың 1-күнінен 1 күн алып тап).

```text
     Сәуір 2027
Дс Сс Ср Бс Жм Сб Жс
          1  2  3  4
 5  6  7  8  9 10 11
12 13 14 15 16 17 18
19 20 21 22 23 24 25
26 27 28 29 30
```

**10-тапсырма. Әдет трекері.** Әр іске қосқанда бағдарлама бүгінгі күнді `habits.txt` файлына жазады (бір күнге бір рет қана). Содан кейін «қатарынан неше күн» (streak) әдетті орындағаныңды есептейді.

## Үй тапсырмасы

1. «Оқу жылының соңына дейін» бағдарламасы: 25 мамырға дейін неше күн, неше апта және неше Python сабағы (дүйсенбі мен сәрсенбі) қалғанын есептейтін бағдарлама жаз.
2. Өзіңнің туған күніңнің қай апта күніне түскенін тауып, ата-анаңнан сол күн туралы бір қызық оқиға сұра.
3. Қорытынды жобаға дайындық: келесі сабаққа өз жобаңның 2–3 идеясын ойлап кел (ойын, тренажер, күнделік, викторина т.б.).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.**

```python
from datetime import date

months = ["қаңтар", "ақпан", "наурыз", "сәуір", "мамыр", "маусым",
          "шілде", "тамыз", "қыркүйек", "қазан", "қараша", "желтоқсан"]
week = ["дүйсенбі", "сейсенбі", "сәрсенбі", "бейсенбі", "жұма", "сенбі", "жексенбі"]
d = date.today()
print(d)
print(d.strftime("%d.%m.%Y"))
print(f"{d.year} жылғы {d.day} {months[d.month - 1]}, {week[d.weekday()]}")
```

**2-тапсырма.**

```python
import time

seconds = int(input("Неше секунд? "))
for left in range(seconds, 0, -1):
    print(f"{left // 60:02d}:{left % 60:02d}")
    time.sleep(1)
print("Уақыт бітті!")
```

**3-тапсырма.**

```python
from datetime import date

def days_until(month, day):
    today = date.today()
    target = date(today.year, month, day)
    if target < today:
        target = date(today.year + 1, month, day)
    return (target - today).days

print("Наурызға дейін:", days_until(3, 22), "күн")
print("Тәуелсіздік күніне дейін:", days_until(12, 16), "күн")
print("Жаңа жылға дейін:", days_until(1, 1), "күн")
print("Туған күніме дейін:", days_until(7, 15), "күн")
```

**4-тапсырма.**

```python
from datetime import datetime

week = ["Дүйсенбі", "Сейсенбі", "Сәрсенбі", "Бейсенбі", "Жұма", "Сенбі", "Жексенбі"]
text = input("Күн: ")
try:
    d = datetime.strptime(text, "%d.%m.%Y")
    print("Бұл –", week[d.weekday()])
except ValueError:
    print("Пішім қате немесе мұндай күн жоқ. Мысалы: 16.12.1991")
```

**5-тапсырма.**

```python
import time

def type_text(text, delay=0.05):
    for ch in text:
        print(ch, end="", flush=True)
        time.sleep(delay)
    print()

type_text("Ерте, ерте, ертеде, ешкі жүні бөртеде...", 0.08)
```

**6-тапсырма.**

```python
from datetime import date, datetime, timedelta

text = input("Туған күнің (КК.АА.ЖЖЖЖ): ")
try:
    birthday = datetime.strptime(text, "%d.%m.%Y").date()
except ValueError:
    print("Қате пішім")
else:
    today = date.today()
    age = today.year - birthday.year
    if (today.month, today.day) < (birthday.month, birthday.day):
        age -= 1
    days = (today - birthday).days
    print("Жасың:", age)
    print("Күндер:", days, "| Сағаттар:", days * 24, "| Минуттар:", days * 24 * 60)
    next_bd = date(today.year, birthday.month, birthday.day)
    if next_bd < today:
        next_bd = date(today.year + 1, birthday.month, birthday.day)
    print("Келесі туған күнге:", (next_bd - today).days, "күн")
    print("10 000-шы күнің:", (birthday + timedelta(days=10000)).strftime("%d.%m.%Y"))
```

Ескерту: 29 ақпанда туғандар үшін `date(year, 2, 29)` кібісе емес жылы `ValueError` береді – күшті оқушыларға қосымша тапсырма ретінде ұсынуға болады.

**7-тапсырма.**

```python
import random
import time

words = ["алма", "жаңбыр", "компьютер", "тау", "өзен", "кітап", "Python", "мектеп", "дала", "жұлдыз"]
target = " ".join(random.sample(words, 5))
print("Тер:", target)
start = time.time()
typed = input("> ")
elapsed = time.time() - start

errors = 0
for i in range(max(len(target), len(typed))):
    if i >= len(target) or i >= len(typed) or target[i] != typed[i]:
        errors += 1
speed = len(typed) / elapsed * 60
print(f"Уақыт: {elapsed:.2f} с, қате: {errors}, жылдамдық: {speed:.0f} таңба/мин")
```

**8-тапсырма.**

```python
from datetime import datetime, timedelta

start = datetime(2027, 4, 26, 14, 0)
lesson = timedelta(minutes=45)
pause = timedelta(minutes=10)
for i in range(1, 5):
    end = start + lesson
    print(f"{i}-сабақ: {start.strftime('%H:%M')} – {end.strftime('%H:%M')}")
    start = end + pause
```

**9-тапсырма.**

```python
from datetime import date, timedelta

months = ["Қаңтар", "Ақпан", "Наурыз", "Сәуір", "Мамыр", "Маусым",
          "Шілде", "Тамыз", "Қыркүйек", "Қазан", "Қараша", "Желтоқсан"]
year = int(input("Жыл: "))
month = int(input("Ай: "))

first = date(year, month, 1)
if month == 12:
    next_first = date(year + 1, 1, 1)
else:
    next_first = date(year, month + 1, 1)
days_in_month = (next_first - timedelta(days=1)).day

print(f"{months[month - 1]} {year}".center(20))
print("Дс Сс Ср Бс Жм Сб Жс")
line = "   " * first.weekday()
for day in range(1, days_in_month + 1):
    line += f"{day:2} "
    if (first.weekday() + day) % 7 == 0:
        print(line.rstrip())
        line = ""
if line:
    print(line.rstrip())
```

**10-тапсырма.**

```python
from datetime import date, timedelta

FILE_NAME = "habits.txt"
today = date.today()
days = set()
try:
    with open(FILE_NAME, encoding="utf-8") as f:
        for line in f:
            if line.strip():
                days.add(line.strip())
except FileNotFoundError:
    pass

if today.isoformat() not in days:
    with open(FILE_NAME, "a", encoding="utf-8") as f:
        f.write(today.isoformat() + "\n")
    days.add(today.isoformat())

streak = 0
d = today
while d.isoformat() in days:
    streak += 1
    d -= timedelta(days=1)
print("Қатарынан күндер:", streak)
```

</details>
