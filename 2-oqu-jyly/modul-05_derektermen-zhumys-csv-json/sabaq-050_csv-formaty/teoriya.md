# Сабақ 50. CSV форматы. csv модулі: файлды оқу және жазу.

## Тақырыптар

1. CSV дегеніміз не?
2. `csv.reader` — кестені оқу
3. `csv.writer` — кестені жазу
4. `csv.DictReader` — бағана атауы бойынша оқу
5. `csv.DictWriter` — сөздіктерді жазу
6. Excel-мен үйлесімділік
7. Жиі кездесетін қателер
8. Негізгі ұғымдар

## 1. CSV дегеніміз не?

**CSV (Comma-Separated Values)** — «үтірмен бөлінген мәндер». Бұл кестені қарапайым мәтіндік файлда сақтаудың ең кең тараған тәсілі.

- Әр **жол** — кестенің бір жазбасы (row).
- Жол ішіндегі мәндер **бөлгішпен** (delimiter) бөлінеді — әдетте үтір `,`.
- Бірінші жол көбіне **тақырып** (header) — бағаналар атаулары.

`students.csv`:

```text
name,grade,city,score
Айгерім,9,Алматы,95
Бауыржан,8,Астана,78
Дәулет,10,Шымкент,88
Сымбат,9,Алматы,100
```

Excel-де ашсаң — бұл кесте:

| name | grade | city | score |
|---|---|---|---|
| Айгерім | 9 | Алматы | 95 |
| Бауыржан | 8 | Астана | 78 |
| Дәулет | 10 | Шымкент | 88 |
| Сымбат | 9 | Алматы | 100 |

**Қайда қолданылады:** Excel/Google Sheets экспорты, kundelik.kz және басқа жүйелерден журналды жүктеу, ашық деректер порталдары (data.egov.kz), ғылыми өлшеулер, банк үзінділері.

### CSV ережелері (RFC 4180)

1. Мән ішінде бөлгіш (үтір) болса — мән **қос тырнақшаға** алынады: `"Алматы, Медеу ауданы"`.
2. Мән ішінде қос тырнақша болса — ол **екі рет** жазылады: `"«Жұлдыз» ""Star"" клубы"`.
3. Мән ішінде жол ауысу болса — мән тырнақшада тұрады.

```text
name,address,club
Айгерім,"Алматы, Медеу ауданы","Шахмат ""Ақ патша"""
```

Мұндай жолды `line.split(",")` дұрыс бөле алмайды — `"Алматы` және `Медеу ауданы"` деп екіге бөліп жібереді. Сондықтан **әрқашан `csv` модулін** қолданамыз — ол барлық ережені біледі.

## 2. `csv.reader` — кестені оқу

```python
import csv

# Алдымен үлгі файл жасаймыз
with open("students.csv", "w", encoding="utf-8", newline="") as f:
    f.write("name,grade,city,score\n")
    f.write("Айгерім,9,Алматы,95\n")
    f.write("Бауыржан,8,Астана,78\n")
    f.write("Дәулет,10,Шымкент,88\n")
    f.write("Сымбат,9,Алматы,100\n")

with open("students.csv", encoding="utf-8", newline="") as f:
    reader = csv.reader(f)
    header = next(reader)          # бірінші жол — тақырып
    print("Бағаналар:", header)
    for row in reader:
        print(row)
```

Нәтижесі:

```text
Бағаналар: ['name', 'grade', 'city', 'score']
['Айгерім', '9', 'Алматы', '95']
['Бауыржан', '8', 'Астана', '78']
['Дәулет', '10', 'Шымкент', '88']
['Сымбат', '9', 'Алматы', '100']
```

Маңызды:
- `reader` — **итератор**: әр қадамда бір жолды **тізім** ретінде береді;
- барлық мәндер — **жолдар** (`str`)! `'95'` — сан емес. Есептеу үшін `int(row[3])`;
- `next(reader)` тақырыпты бөлек алып, қалған циклге тек деректер қалады.

### `newline=""` неге керек?

`csv` модулі жол соңын өзі басқарады. Егер `newline=""` көрсетпесең, Windows-та жазылған файлда жолдар арасында **бос жолдар** пайда болады, ал оқығанда тырнақшадағы көпжолды мәндер бұзылуы мүмкін. Ереже: **CSV файлды әрқашан `newline=""` арқылы ашамыз.**

## 3. `csv.writer` — кестені жазу

```python
import csv

products = [
    ["Тауар", "Бағасы (₸)", "Саны"],
    ["Нан", 250, 2],
    ["Сүт", 520, 1],
    ["Алма, қызыл", 890, 3],     # мәнде үтір бар!
]

with open("shop.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.writer(f)
    writer.writerows(products)        # бірден көп жол
    writer.writerow(["Барлығы", "", 6])  # бір жол

with open("shop.csv", encoding="utf-8") as f:
    print(f.read())
```

Нәтижесі:

```text
Тауар,Бағасы (₸),Саны
Нан,250,2
Сүт,520,1
"Алма, қызыл",890,3
Барлығы,,6
```

`csv.writer` «Алма, қызыл» мәнін **автоматты** тырнақшаға алды, ал сандарды өзі жолға айналдырды.

| Әдіс | Не істейді |
|---|---|
| `writer.writerow(list)` | бір жолды жазады |
| `writer.writerows(list_of_lists)` | бірнеше жолды жазады |

## 4. `csv.DictReader` — бағана атауы бойынша оқу

`row[3]` — қай бағана екені түсініксіз. `DictReader` әр жолды **сөздік** ретінде береді, кілттері — тақырып жолынан:

```python
import csv

with open("students.csv", encoding="utf-8", newline="") as f:
    reader = csv.DictReader(f)
    print("Бағаналар:", reader.fieldnames)
    for row in reader:
        print(f"{row['name']} ({row['city']}) — {row['score']} балл")
```

Нәтижесі:

```text
Бағаналар: ['name', 'grade', 'city', 'score']
Айгерім (Алматы) — 95 балл
Бауыржан (Астана) — 78 балл
Дәулет (Шымкент) — 88 балл
Сымбат (Алматы) — 100 балл
```

### Деректерді өңдеу: сүзу, сұрыптау, есептеу

```python
import csv

with open("students.csv", encoding="utf-8", newline="") as f:
    students = list(csv.DictReader(f))

# Түрлендіру: жолдарды санға
for s in students:
    s["score"] = int(s["score"])
    s["grade"] = int(s["grade"])

almaty = [s["name"] for s in students if s["city"] == "Алматы"]
average = sum(s["score"] for s in students) / len(students)
ranking = sorted(students, key=lambda s: s["score"], reverse=True)

print("Алматыдан:", ", ".join(almaty))
print(f"Орташа балл: {average:.2f}")
print("Рейтинг:")
for place, s in enumerate(ranking, start=1):
    print(f"  {place}. {s['name']} — {s['score']}")
```

Нәтижесі:

```text
Алматыдан: Айгерім, Сымбат
Орташа балл: 90.25
Рейтинг:
  1. Сымбат — 100
  2. Айгерім — 95
  3. Дәулет — 88
  4. Бауыржан — 78
```

### Топтау: қала бойынша статистика

```python
import csv

with open("students.csv", encoding="utf-8", newline="") as f:
    rows = list(csv.DictReader(f))

by_city = {}
for row in rows:
    by_city.setdefault(row["city"], []).append(int(row["score"]))

for city, scores in sorted(by_city.items()):
    print(f"{city}: {len(scores)} оқушы, орташа {sum(scores) / len(scores):.1f}")
```

Нәтижесі:

```text
Алматы: 2 оқушы, орташа 97.5
Астана: 1 оқушы, орташа 78.0
Шымкент: 1 оқушы, орташа 88.0
```

## 5. `csv.DictWriter` — сөздіктерді жазу

```python
import csv

results = [
    {"name": "Айгерім", "subject": "Информатика", "score": 95},
    {"name": "Дәулет", "subject": "Математика", "score": 88},
]

with open("olympiad.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "subject", "score"])
    writer.writeheader()          # тақырып жолы
    writer.writerows(results)
    writer.writerow({"name": "Сымбат", "subject": "Физика", "score": 100})

with open("olympiad.csv", encoding="utf-8") as f:
    print(f.read())
```

Нәтижесі:

```text
name,subject,score
Айгерім,Информатика,95
Дәулет,Математика,88
Сымбат,Физика,100
```

`fieldnames` бағаналар ретін анықтайды. Сөздікте `fieldnames`-те жоқ кілт болса — `ValueError`; керісінше, сөздікте кейбір кілт жоқ болса — ұяшық бос қалады.

### Файлға жазба қосу

```python
import csv
import os

FILE = "visits.csv"
FIELDS = ["date", "name", "minutes"]


def add_visit(date, name, minutes):
    """Үйірмеге келу жазбасын қосады; файл жаңа болса, тақырып жазады."""
    is_new = not os.path.exists(FILE)
    with open(FILE, "a", encoding="utf-8", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=FIELDS)
        if is_new:
            writer.writeheader()
        writer.writerow({"date": date, "name": name, "minutes": minutes})


add_visit("2026-12-31", "Айгерім", 90)
add_visit("2026-12-31", "Дәулет", 75)
with open(FILE, encoding="utf-8") as f:
    print(f.read())
```

Нәтижесі (бірінші іске қосуда):

```text
date,name,minutes
2026-12-31,Айгерім,90
2026-12-31,Дәулет,75
```

## 6. Excel-мен үйлесімділік

Қазақстанда Excel көбіне орыс/қазақ тілді баптаулармен тұрады. Онда:
- бөлгіш ретінде **нүктелі үтір** `;` күтіледі (себебі үтір — бөлшек сандардың бөлгіші: `3,14`);
- UTF-8 файлдағы кириллицаны дұрыс тану үшін файл басында **BOM** белгісі керек — оны `encoding="utf-8-sig"` береді.

```python
import csv

rows = [["Аты", "Орташа балл"], ["Әлия", "4,8"], ["Ерасыл", "4,5"]]

with open("for_excel.csv", "w", encoding="utf-8-sig", newline="") as f:
    writer = csv.writer(f, delimiter=";")
    writer.writerows(rows)

with open("for_excel.csv", encoding="utf-8-sig", newline="") as f:
    for row in csv.reader(f, delimiter=";"):
        print(row)
```

Нәтижесі:

```text
['Аты', 'Орташа балл']
['Әлия', '4,8']
['Ерасыл', '4,5']
```

| Параметр | Мағынасы | Мысал |
|---|---|---|
| `delimiter` | бағаналар бөлгіші | `","`, `";"`, `"\t"` (TSV) |
| `quotechar` | тырнақша таңбасы | `'"'` (әдепкі) |
| `quoting` | қай мәндерді тырнақшаға алу | `csv.QUOTE_MINIMAL` (әдепкі), `csv.QUOTE_ALL` |
| `encoding="utf-8-sig"` | BOM қосу/өткізу | Excel үшін |

Бөлгішті білмесең, `csv.Sniffer().sniff(sample)` оны автоматты анықтауға тырысады.

## Жиі кездесетін қателер

**1. Сандарды жол ретінде салыстыру.**

```python
scores = ["95", "100", "78"]
print(max(scores))                    # '95' — қате! Жолдар әліпби бойынша
print(max(int(s) for s in scores))    # 100 — дұрыс
```

Нәтижесі:

```text
95
100
```

**2. `newline=""` жоқ** → Windows-та жолдар арасы бос жолдармен толады. Дұрыс: `open(..., newline="")`.

**3. Тақырып жолын деректер ретінде өңдеу.** `int("score")` → `ValueError`. Дұрыс: `next(reader)` немесе `DictReader`.

**4. `split(",")` арқылы CSV оқу** → тырнақшадағы үтір бұзылады. Дұрыс: `csv.reader`.

**5. Бос жол немесе бос ұяшық.** Файл соңындағы бос жол `reader`-де `[]` береді; бос ұяшық — `''`. Тексеру:

```python
import csv
import io

data = io.StringIO("name,score\nӘлия,90\n\nЕрлан,\n")
for row in csv.DictReader(data):
    if not row["score"]:
        print(f"{row['name']}: балл жоқ, өткізіп жібердік")
        continue
    print(row["name"], int(row["score"]))
```

Нәтижесі:

```text
Әлия 90
Ерлан: балл жоқ, өткізіп жібердік
```

(`io.StringIO` — жолды «файл» сияқты оқуға мүмкіндік береді; тексеру үшін ыңғайлы. `DictReader` бос жолдарды өзі өткізіп жібереді.)

**6. Excel-де «кракозябры»** (`Ð�Ð»Ð¸Ñ`) → файл BOM-сыз UTF-8. Дұрыс: `encoding="utf-8-sig"`.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| CSV | Үтірмен (немесе басқа бөлгішпен) бөлінген мәндері бар мәтіндік кесте |
| Тақырып (header) | Бағаналар атаулары жазылған бірінші жол |
| Бөлгіш (delimiter) | Мәндерді бөлетін таңба: `,` `;` `\t` |
| Тырнақшаға алу (quoting) | Ішінде бөлгіші бар мәнді `"..."` ішіне алу |
| `csv.reader` / `csv.writer` | Жолдарды тізім ретінде оқу/жазу |
| `csv.DictReader` / `csv.DictWriter` | Жолдарды сөздік ретінде оқу/жазу |
| `fieldnames` | DictWriter-дегі бағаналар тізімі мен реті |
| BOM | Файл басындағы белгі; Excel-ге UTF-8 екенін хабарлайды |
| TSV | Табуляциямен бөлінген мәндер |

## Ескерту

- `csv.reader` типтерді түрлендірмейді: тіпті `quoting=csv.QUOTE_NONNUMERIC` болғанда да тырнақшасыз мәндер ғана санға айналады, әрі нәтиже әрқашан `float`. `ValueError`-ды өңдей отырып, `int()`/`float()`-ты анық шақыру сенімдірек.
- `DictReader` жолда «артық» ұяшықтар болса, оларды `None` кілті астына тізім етіп жинайды (`restkey` параметрімен өзгертіледі), ал жетіспейтіндеріне `None` береді (`restval` параметрі).
- `DictWriter(..., extrasaction="ignore")` `fieldnames`-те жоқ сөздік кілттерін `ValueError` шығармай, үнсіз өткізіп жібереді.
- Өте үлкен кестелер үшін `list(csv.DictReader(f))` жасамаңыз — жолдарды `for` циклінде өңдеңіз, сонда жадта тек бір жол болады.
- «Орысша» Excel-де бөлшек сандар үтір арқылы жазылады (`4,8`); `float()` алдында оны ауыстырыңыз: `float(value.replace(",", "."))`.
- Модуль құжаттамасы: https://docs.python.org/3/library/csv.html
