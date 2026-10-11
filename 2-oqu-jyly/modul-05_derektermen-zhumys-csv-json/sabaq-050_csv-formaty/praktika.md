# Сабақ 50. Практикалық тапсырмалар: CSV файлдарымен жұмыс

Барлық тапсырмада `import csv`, файлдарды `encoding="utf-8", newline=""` арқылы ашу міндетті.

**Дайындық.** `journal.csv` файлын жасаңдар (VS Code-та немесе Excel-де «CSV UTF-8» ретінде сақтап):

```text
name,class,math,informatics,physics
Айгерім Нұрланқызы,9А,5,5,4
Бауыржан Сейітов,9А,3,4,4
Дәулет Оспанов,9Ә,4,5,5
Сымбат Әлиева,9Ә,5,5,5
Ерлан Жақыпов,9А,3,3,4
Мадина Қасымова,9Ә,4,4,3
```

## Базалық деңгей

### 1-тапсырма. Журналды кесте түрінде шығару

`csv.reader` арқылы `journal.csv`-ны оқып, бағаналарды тураланған кесте түрінде шығар.

```text
Шығару:
name                 class  math  informatics  physics
Айгерім Нұрланқызы   9А     5     5            4
Бауыржан Сейітов     9А     3     4            4
...
```

### 2-тапсырма. Орташа балл

`csv.DictReader` арқылы әр оқушының үш пән бойынша орташа баллын есептеп шығар. Соңында сыныптың (барлық оқушылардың) жалпы орташасын шығар.

```text
Шығару:
Айгерім Нұрланқызы: 4.67
Бауыржан Сейітов: 3.67
Дәулет Оспанов: 4.67
Сымбат Әлиева: 5.00
Ерлан Жақыпов: 3.33
Мадина Қасымова: 3.67
Жалпы орташа: 4.17
```

### 3-тапсырма. Үздіктер тізімін жазу

Барлық пәннен «5» алған оқушыларды (үздіктер) және кемінде бір «3» алғандарды бөлек `excellent.csv` және `need_help.csv` файлдарына `csv.writer` арқылы жаз (тақырып жолымен бірге).

```text
excellent.csv:
name,class
Сымбат Әлиева,9Ә

need_help.csv:
name,class,subjects
Бауыржан Сейітов,9А,math
Ерлан Жақыпов,9А,"math, informatics"
Мадина Қасымова,9Ә,physics
```

Байқа: «math, informatics» мәнін `csv.writer` өзі тырнақшаға алады.

### 4-тапсырма. Сатып алу тізімі

Пайдаланушы тауар атауын, бағасын және санын енгізеді (бос атау — тоқтау). Бағдарлама оларды `shopping.csv` файлына `DictWriter` арқылы жазып, соңында жалпы соманы шығарады.

```text
Енгізу:
Тауар: Дәптер
Бағасы: 150
Саны: 10
Тауар: Қалам
Бағасы: 120
Саны: 5
Тауар:
Шығару:
Барлығы: 2100 ₸. Тізім shopping.csv файлына сақталды
```

## Кеңейтілген деңгей

### 5-тапсырма. Сыныптар бойынша есеп

`journal.csv` бойынша әр сыныптың (9А, 9Ә) әр пәннен орташа баллын есептеп, `report.csv` файлына жаз. Excel-де дұрыс ашылуы үшін `utf-8-sig` және `;` бөлгішін қолдан, орташаны бір таңбалы бөлшекпен жаз.

```text
report.csv:
class;math;informatics;physics
9А;3.7;4.0;4.0
9Ә;4.3;4.7;4.3
```

### 6-тапсырма. Іздеу

Пайдаланушы аты-жөнінің бір бөлігін енгізеді (мысалы, «әли»). Бағдарлама регистрге қарамай сәйкес келетін барлық оқушының деректерін шығарады. Ешкім табылмаса — «Табылмады».

### 7-тапсырма. CSV түрлендіргіш

Кез келген CSV файлдың бөлгішін өзгертетін функция жаз: `convert(src, dst, old=",", new=";")`. Тырнақшадағы үтірлер бұзылмауы тиіс. Тексер: 3-тапсырмадағы `need_help.csv` файлын түрлендір.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Ашық деректерді талдау

data.egov.kz немесе stat.gov.kz порталдарынан (немесе педагог берген файлдан) Қазақстан облыстары бойынша кез келген CSV деректерін (халық саны, мектептер саны т.б.) жүктеп ал. `csv` модулімен:
1. бағаналар атауын және жазбалар санын шығар;
2. ең үлкен және ең кіші мәнді облысты тап;
3. мәндері бойынша сұрыпталған жаңа CSV файлын жаса.

Педагог файлы болмаса, мына деректерді қолдан (`cities.csv`, мың адам, 2024 ж. шамасы):

```text
city,region,population
Алматы,Алматы қ.,2228
Астана,Астана қ.,1423
Шымкент,Шымкент қ.,1219
Ақтөбе,Ақтөбе обл.,575
Қарағанды,Қарағанды обл.,505
Тараз,Жамбыл обл.,368
Өскемен,Шығыс Қазақстан обл.,368
Павлодар,Павлодар обл.,363
```

### 9-тапсырма. Өз CSV оқығышың

`csv` модулін қолданбай, бір CSV жолын тырнақшалар ережесін ескеріп бөлетін `parse_line(line)` функциясын жаз. Тест:

```text
parse_line('Айгерім,"Алматы, Медеу","Шахмат ""Ақ патша"""')
→ ['Айгерім', 'Алматы, Медеу', 'Шахмат "Ақ патша"']
```

## Үй тапсырмасы

1. Өз отбасыңның бір апталық шығындарын `expenses.csv` файлына жаз (`date,category,amount`, кемінде 10 жазба). Бағдарлама санаттар бойынша шығын қосындысын және ең көп ақша кеткен санатты шығарсын.
2. Файлды Excel-де (немесе Google Sheets-те) ашып, дұрыс көрінетінін тексер. Көрінбесе — себебін тауып, түзет.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**Дайындық файлын кодпен жасау (тексеру үшін)**

```python
import csv

rows = [
    ["name", "class", "math", "informatics", "physics"],
    ["Айгерім Нұрланқызы", "9А", 5, 5, 4],
    ["Бауыржан Сейітов", "9А", 3, 4, 4],
    ["Дәулет Оспанов", "9Ә", 4, 5, 5],
    ["Сымбат Әлиева", "9Ә", 5, 5, 5],
    ["Ерлан Жақыпов", "9А", 3, 3, 4],
    ["Мадина Қасымова", "9Ә", 4, 4, 3],
]
with open("journal.csv", "w", encoding="utf-8", newline="") as f:
    csv.writer(f).writerows(rows)
print("journal.csv дайын")
```

**1-тапсырма**

```python
import csv

widths = [21, 7, 6, 13, 8]
with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.reader(f):
        line = "".join(value.ljust(width) for value, width in zip(row, widths))
        print(line.rstrip())
```

**2-тапсырма**

```python
import csv

SUBJECTS = ["math", "informatics", "physics"]
all_marks = []

with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        marks = [int(row[s]) for s in SUBJECTS]
        all_marks.extend(marks)
        print(f"{row['name']}: {sum(marks) / len(marks):.2f}")

print(f"Жалпы орташа: {sum(all_marks) / len(all_marks):.2f}")
```

Нәтижесі:

```text
Айгерім Нұрланқызы: 4.67
Бауыржан Сейітов: 3.67
Дәулет Оспанов: 4.67
Сымбат Әлиева: 5.00
Ерлан Жақыпов: 3.33
Мадина Қасымова: 3.67
Жалпы орташа: 4.17
```

**3-тапсырма**

```python
import csv

SUBJECTS = ["math", "informatics", "physics"]
excellent = [["name", "class"]]
need_help = [["name", "class", "subjects"]]

with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        marks = {s: int(row[s]) for s in SUBJECTS}
        if all(m == 5 for m in marks.values()):
            excellent.append([row["name"], row["class"]])
        weak = [s for s, m in marks.items() if m == 3]
        if weak:
            need_help.append([row["name"], row["class"], ", ".join(weak)])

for filename, rows in [("excellent.csv", excellent), ("need_help.csv", need_help)]:
    with open(filename, "w", encoding="utf-8", newline="") as f:
        csv.writer(f).writerows(rows)
    print(filename, "—", len(rows) - 1, "жазба")
```

Нәтижесі:

```text
excellent.csv — 1 жазба
need_help.csv — 3 жазба
```

**4-тапсырма**

```python
import csv

items = []
while True:
    name = input("Тауар: ").strip()
    if not name:
        break
    price = int(input("Бағасы: "))
    qty = int(input("Саны: "))
    items.append({"item": name, "price": price, "qty": qty, "total": price * qty})

with open("shopping.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["item", "price", "qty", "total"])
    writer.writeheader()
    writer.writerows(items)

print(f"Барлығы: {sum(i['total'] for i in items)} ₸. Тізім shopping.csv файлына сақталды")
```

**5-тапсырма**

```python
import csv

SUBJECTS = ["math", "informatics", "physics"]
groups = {}

with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        groups.setdefault(row["class"], []).append(row)

with open("report.csv", "w", encoding="utf-8-sig", newline="") as f:
    writer = csv.writer(f, delimiter=";")
    writer.writerow(["class"] + SUBJECTS)
    for cls, rows in sorted(groups.items()):
        averages = [f"{sum(int(r[s]) for r in rows) / len(rows):.1f}" for s in SUBJECTS]
        writer.writerow([cls] + averages)

with open("report.csv", encoding="utf-8-sig") as f:
    print(f.read())
```

Нәтижесі:

```text
class;math;informatics;physics
9А;3.7;4.0;4.0
9Ә;4.3;4.7;4.3
```

**6-тапсырма**

```python
import csv

query = input("Іздеу: ").strip().lower()
found = False
with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        if query in row["name"].lower():
            print(", ".join(f"{k}: {v}" for k, v in row.items()))
            found = True
if not found:
    print("Табылмады")
```

**7-тапсырма**

```python
import csv


def convert(src, dst, old=",", new=";"):
    """CSV файлдың бөлгішін ауыстырады, тырнақшаларды сақтайды."""
    with open(src, encoding="utf-8", newline="") as fin, \
         open(dst, "w", encoding="utf-8", newline="") as fout:
        writer = csv.writer(fout, delimiter=new)
        for row in csv.reader(fin, delimiter=old):
            writer.writerow(row)


with open("need_help.csv", "w", encoding="utf-8", newline="") as f:
    csv.writer(f).writerows([["name", "subjects"], ["Ерлан", "math, informatics"]])
convert("need_help.csv", "need_help_semicolon.csv")
with open("need_help_semicolon.csv", encoding="utf-8") as f:
    print(f.read())
```

Нәтижесі:

```text
name;subjects
Ерлан;math, informatics
```

Ескерту: жаңа бөлгіш `;` болғандықтан, «math, informatics» мәнін енді тырнақшаға алудың қажеті жоқ — `csv.writer` мұны өзі шешеді.

**8-тапсырма (берілген cities.csv бойынша)**

```python
import csv

data = """city,region,population
Алматы,Алматы қ.,2228
Астана,Астана қ.,1423
Шымкент,Шымкент қ.,1219
Ақтөбе,Ақтөбе обл.,575
Қарағанды,Қарағанды обл.,505
Тараз,Жамбыл обл.,368
Өскемен,Шығыс Қазақстан обл.,368
Павлодар,Павлодар обл.,363
"""
with open("cities.csv", "w", encoding="utf-8", newline="") as f:
    f.write(data)

with open("cities.csv", encoding="utf-8", newline="") as f:
    reader = csv.DictReader(f)
    rows = list(reader)
    print("Бағаналар:", reader.fieldnames, "| Жазбалар:", len(rows))

for row in rows:
    row["population"] = int(row["population"])

biggest = max(rows, key=lambda r: r["population"])
smallest = min(rows, key=lambda r: r["population"])
print("Ең үлкен:", biggest["city"], biggest["population"])
print("Ең кіші:", smallest["city"], smallest["population"])

rows.sort(key=lambda r: r["population"], reverse=True)
with open("cities_sorted.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["city", "region", "population"])
    writer.writeheader()
    writer.writerows(rows)
```

Нәтижесі:

```text
Бағаналар: ['city', 'region', 'population'] | Жазбалар: 8
Ең үлкен: Алматы 2228
Ең кіші: Павлодар 363
```

**9-тапсырма**

```python
def parse_line(line, sep=","):
    """CSV жолын тырнақшалар ережесін ескеріп бөледі (күй машинасы)."""
    result, value = [], ""
    in_quotes = False
    i = 0
    while i < len(line):
        ch = line[i]
        if in_quotes:
            if ch == '"':
                if i + 1 < len(line) and line[i + 1] == '"':
                    value += '"'      # қос тырнақша → бір тырнақша
                    i += 1
                else:
                    in_quotes = False
            else:
                value += ch
        elif ch == '"':
            in_quotes = True
        elif ch == sep:
            result.append(value)
            value = ""
        else:
            value += ch
        i += 1
    result.append(value)
    return result


print(parse_line('Айгерім,"Алматы, Медеу","Шахмат ""Ақ патша"""'))
```

Нәтижесі:

```text
['Айгерім', 'Алматы, Медеу', 'Шахмат "Ақ патша"']
```

</details>
