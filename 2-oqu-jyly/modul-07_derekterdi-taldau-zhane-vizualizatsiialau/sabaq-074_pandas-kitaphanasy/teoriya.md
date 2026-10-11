# Сабақ 74. Pandas кітапханасы. Series және DataFrame құрылымдары.

## Тақырыптар

1. Pandas деген не?
2. Series
3. DataFrame
4. Бағандармен жұмыс
5. Жолдарға қол жеткізу: `loc` және `iloc`
6. Сұрыптау және агрегаттар
7. Сақтау
8. Pandas-сыз және Pandas-пен
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Pandas деген не?

**Pandas** — кестелік деректерді талдауға арналған кітапхана. Атауы *panel data* (эконометрика термині) сөзінен шыққан. Оны «Python ішіндегі Excel» деп елестетуге болады, бірақ ол әлдеқайда қуатты: миллиондаған жолды өңдейді, CSV, Excel, JSON, SQL-мен жұмыс істейді.

```bash
pip install pandas
```

```python
import pandas as pd          # жалпы қабылданған атау — pd
import numpy as np

print(pd.__version__)
```

Pandas-тың екі негізгі құрылымы:

| Құрылым | Өлшемі | Аналогия |
|---|---|---|
| `Series` | 1 өлшемді | Кестенің бір бағаны (аты бар) |
| `DataFrame` | 2 өлшемді | Бүкіл кесте (Excel парағы, SQL кестесі) |

```text
          Series                 DataFrame
       index  value         index  name     grade  score
         0    92.5            0    Айгерім    9    92.5
         1    78.0            1    Нұрлан    10    78.0
         2    88.0            2    Дана       9    88.0
                                    └─ әр баған — Series
```

## 2. Series

**Series** — индекстелген бір өлшемді массив: мәндер (NumPy массиві) + **индекс** (белгілер).

### Құру

```python
import pandas as pd

s1 = pd.Series([92.5, 78.0, 88.0])                         # индекс автоматты: 0, 1, 2
s2 = pd.Series([92.5, 78.0, 88.0], index=["Айгерім", "Нұрлан", "Дана"], name="Балл")
s3 = pd.Series({"Алматы": 2200, "Астана": 1500, "Шымкент": 1250})   # сөздіктен: кілттер → индекс

print(s1)
print(s2)
print(s3.index.tolist(), s3.values)
```

Нәтижесі:
```text
0    92.5
1    78.0
2    88.0
dtype: float64
Айгерім    92.5
Нұрлан     78.0
Дана       88.0
Name: Балл, dtype: float64
['Алматы', 'Астана', 'Шымкент'] [2200 1500 1250]
```

### Элементтерге қол жеткізу

```python
import pandas as pd

pop = pd.Series({"Алматы": 2200, "Астана": 1500, "Шымкент": 1250, "Ақтөбе": 580})

print(pop["Астана"])          # белгі (label) бойынша
print(pop.iloc[0])            # орны (position) бойынша
print(pop[["Алматы", "Ақтөбе"]])
print(pop[pop > 1000])        # маска — NumPy-дегідей
```

Нәтижесі:
```text
1500
2200
Алматы    2200
Ақтөбе     580
dtype: int64
Алматы     2200
Астана     1500
Шымкент    1250
dtype: int64
```

### Амалдар мен әдістер

```python
import pandas as pd

temps = pd.Series([-5.2, -3.1, 0.5, 2.8, 1.1, -1.4, -6.0],
                  index=["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"])

print((temps * 9 / 5 + 32).round(1).tolist())   # векторлық амал
print("Орташа:", round(temps.mean(), 2))
print("Ең жылы күн:", temps.idxmax())          # индекс белгісін қайтарады!
print(temps.sort_values().head(3))             # ең суық 3 күн
```

Нәтижесі:
```text
[22.6, 26.4, 32.9, 37.0, 34.0, 29.5, 21.2]
Орташа: -1.61
Ең жылы күн: Бс
Жс   -6.0
Дс   -5.2
Сс   -3.1
dtype: float64
```

`idxmax()` — NumPy-дегі `argmax()`-тың «адамға түсінікті» нұсқасы: нөмір емес, **атауды** қайтарады.

**Индекс бойынша туралау** — Pandas-тың ерекше қасиеті: екі Series қосылғанда мәндер орны бойынша емес, **индекс белгілері** бойынша сәйкестендіріледі.

```python
import pandas as pd

jan = pd.Series({"Алматы": 10, "Астана": 7})
feb = pd.Series({"Астана": 5, "Алматы": 12, "Шымкент": 4})
print(jan + feb)
```

Нәтижесі:
```text
Алматы     22.0
Астана     12.0
Шымкент     NaN
dtype: float64
```

**`NaN`** (Not a Number) — «мән жоқ» белгісі: Шымкенттің қаңтар деректері болмағандықтан қосынды белгісіз. Бос мәндер `isna()`, `fillna()`, `dropna()` әдістерімен өңделеді.

### `value_counts()` — жиілікті санау

```python
import pandas as pd

cities = pd.Series(["Алматы", "Астана", "Алматы", "Шымкент", "Алматы", "Астана"])
print(cities.value_counts())
print(cities.nunique(), "түрлі қала")
```

Нәтижесі:
```text
Алматы     3
Астана     2
Шымкент    1
Name: count, dtype: int64
3 түрлі қала
```

## 3. DataFrame

**DataFrame** — аталған бағандары бар екі өлшемді кесте. Әр баған — `Series`, барлық бағандардың индексі ортақ.

### Құрудың 3 тәсілі

```python
import pandas as pd

# 1. Бағандар сөздігінен (кілт — баған аты, мән — тізім)
df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия"],
    "grade": [9, 10, 9, 11, 10],
    "city": ["Алматы", "Астана", "Шымкент", "Алматы", "Алматы"],
    "math": [92, 78, 88, 95, 70],
    "inf": [98, 85, 90, 91, 88],
})
print(df)

# 2. Жазбалар (сөздіктер) тізімінен — API/скрапинг нәтижесі дәл осындай!
books = [{"title": "Абай жолы", "price": 4500}, {"title": "Көшпенділер", "price": 6000}]
print(pd.DataFrame(books))

# 3. NumPy массивінен
import numpy as np
print(pd.DataFrame(np.arange(6).reshape(2, 3), columns=["a", "b", "c"]))
```

Нәтижесі:
```text
      name  grade     city  math  inf
0  Айгерім      9   Алматы    92   98
1   Нұрлан     10   Астана    78   85
2     Дана      9  Шымкент    88   90
3    Ержан     11   Алматы    95   91
4     Әлия     10   Алматы    70   88
         title  price
0    Абай жолы   4500
1  Көшпенділер   6000
   a  b  c
0  0  1  2
1  3  4  5
```

SQLite-тан да тікелей оқуға болады: `pd.read_sql("SELECT * FROM students", conn)` — V модульдегі деректер қорлары енді талдауға дайын!

### Кестені зерттеу

```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия"],
    "grade": [9, 10, 9, 11, 10],
    "city": ["Алматы", "Астана", "Шымкент", "Алматы", "Алматы"],
    "math": [92, 78, 88, 95, 70],
    "inf": [98, 85, 90, 91, 88],
})

print(df.shape)               # (жолдар, бағандар)
print(df.columns.tolist())    # баған атаулары
print(df.dtypes)              # әр бағанның типі
print(df.head(2))             # алғашқы 2 жол (әдепкі — 5)
print(df.tail(1))             # соңғы жол
df.info()                     # жалпы ақпарат: типтер, бос емес мәндер саны
print(df.describe().round(1)) # сандық бағандар статистикасы
```

Нәтижесі (үзінді):
```text
(5, 5)
['name', 'grade', 'city', 'math', 'inf']
name     object
grade     int64
city     object
math      int64
inf       int64
dtype: object
...
       grade  math   inf
count    5.0   5.0   5.0
mean     9.8  84.6  90.4
std      0.8  10.4   4.8
min      9.0  70.0  85.0
25%      9.0  78.0  88.0
50%     10.0  88.0  90.0
75%     10.0  92.0  91.0
max     11.0  95.0  98.0
```

| Әдіс / атрибут | Не көрсетеді |
|---|---|
| `shape` | Жолдар мен бағандар саны |
| `columns`, `index` | Баған атаулары, жол белгілері |
| `dtypes` | Бағандар типтері (`object` — мәтін; Pandas 3.x нұсқасында мәтін бағандары `str` деп көрсетіледі) |
| `head(n)`, `tail(n)` | Басы / соңы |
| `info()` | Типтер, бос емес мәндер, жад |
| `describe()` | count, mean, std, min, квартильдер, max |

`describe()` — жаңа деректер жиынымен танысудың ең жылдам тәсілі: бір қарағанда орташа, шашырау, шеткі мәндер көрінеді.

## 4. Бағандармен жұмыс

```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия"],
    "grade": [9, 10, 9, 11, 10],
    "math": [92, 78, 88, 95, 70],
    "inf": [98, 85, 90, 91, 88],
})

print(df["math"].mean())                 # бір баған → Series
print(df[["name", "inf"]])               # бірнеше баған → DataFrame (екі жақша!)

df["avg"] = (df["math"] + df["inf"]) / 2            # жаңа есептелген баған
df["passed"] = df["avg"] >= 85                       # логикалық баған
df = df.rename(columns={"inf": "informatics"})       # атын өзгерту
df = df.drop(columns=["grade"])                      # бағанды өшіру
print(df)
```

Нәтижесі:
```text
84.6
      name  inf
0  Айгерім   98
1   Нұрлан   85
2     Дана   90
3    Ержан   91
4     Әлия   88
      name  math  informatics   avg  passed
0  Айгерім    92           98  95.0    True
1   Нұрлан    78           85  81.5   False
2     Дана    88           90  89.0    True
3    Ержан    95           91  93.0    True
4     Әлия    70           88  79.0   False
```

## 5. Жолдарға қол жеткізу: `loc` және `iloc`

| | `loc` | `iloc` |
|---|---|---|
| Неге сүйенеді | **Белгі** (индекс аты, баған аты) | **Орын** (нөмір) |
| Мысал | `df.loc[2, "math"]` | `df.iloc[2, 3]` |
| Кесінді | Соңғысы **қосылады** | Соңғысы қосылмайды (Python сияқты) |

```python
import pandas as pd

df = pd.DataFrame({"math": [92, 78, 88], "inf": [98, 85, 90]},
                  index=["Айгерім", "Нұрлан", "Дана"])

print(df.loc["Нұрлан"])                  # бір жол → Series
print(df.loc["Дана", "inf"])             # бір ұяшық
print(df.iloc[0])                        # бірінші жол
print(df.iloc[:2, 1].tolist())           # алғашқы 2 жолдың 2-бағаны
df.loc["Дана", "math"] = 90              # мәнді өзгерту
print(df)
```

Нәтижесі:
```text
math    78
inf     85
Name: Нұрлан, dtype: int64
90
math    92
inf     98
Name: Айгерім, dtype: int64
[98, 85]
         math  inf
Айгерім    92   98
Нұрлан     78   85
Дана       90   90
```

Кез келген бағанды индекс етуге болады: `df.set_index("name")`; кері — `df.reset_index()`.

Шарт бойынша сүзу:
```python
import pandas as pd
df = pd.DataFrame({"name": ["Айгерім", "Нұрлан", "Дана"], "math": [92, 78, 88]})
print(df[df["math"] > 80])
```

## 6. Сұрыптау және агрегаттар

```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия"],
    "city": ["Алматы", "Астана", "Шымкент", "Алматы", "Алматы"],
    "math": [92, 78, 88, 95, 70],
    "inf": [98, 85, 90, 91, 88],
})

print(df.sort_values("math", ascending=False).head(3)[["name", "math"]])
print(df.sort_values(["city", "inf"], ascending=[True, False])[["city", "name", "inf"]])
print(df[["math", "inf"]].mean())            # әр бағанның ортасы (axis=0)
print(df[["math", "inf"]].mean(axis=1).tolist())  # әр оқушының ортасы
print(df["city"].value_counts())
```

Нәтижесі (үзінді):
```text
      name  math
3    Ержан    95
0  Айгерім    92
2     Дана    88
...
math    84.6
inf     90.4
dtype: float64
[95.0, 81.5, 89.0, 93.0, 79.0]
```

## 7. Сақтау

```python
import pandas as pd

df = pd.DataFrame({"name": ["Айгерім", "Нұрлан"], "math": [92, 78]})
df.to_csv("students_pandas.csv", index=False, encoding="utf-8-sig")   # index=False — 0,1,2 бағанын жазбау
df.to_json("students_pandas.json", orient="records", force_ascii=False, indent=2)
print("Сақталды")
```

`df.to_excel("file.xlsx")` да бар (`openpyxl` кітапханасы қажет).

## 8. Pandas-сыз және Pandas-пен

Тапсырма: «Алматы оқушыларын балл бойынша сұрыптап, CSV-ге сақтау».

| Pandas-сыз (csv + цикл) | Pandas-пен |
|---|---|
| ~15 жол: файл ашу, `DictReader`, цикл, `int()`, `sorted(key=...)`, `DictWriter` | 1 жол |

```python
import pandas as pd

df = pd.DataFrame({"name": ["Айгерім", "Нұрлан", "Ержан"], "city": ["Алматы", "Астана", "Алматы"],
                   "score": [92.5, 78.0, 95.5]})
df[df["city"] == "Алматы"].sort_values("score", ascending=False).to_csv("almaty.csv", index=False)
print(open("almaty.csv", encoding="utf-8").read())
```

Нәтижесі:
```text
name,city,score
Ержан,Алматы,95.5
Айгерім,Алматы,92.5
```

## Жиі кездесетін қателер

**1. Бірнеше бағанды бір жақшамен таңдау:**
```python
import pandas as pd
df = pd.DataFrame({"a": [1], "b": [2], "c": [3]})
# Қате: df["a", "b"]  → KeyError
print(df[["a", "b"]])   # Дұрыс: тізім беру керек — екі жақша
```

**2. `KeyError: 'Math'`** — баған атауы регистрге сезімтал және бос орынды ескереді. `print(df.columns.tolist())` арқылы тексеріңіз.

**3. Әдіс нәтижесін сақтамау:**
```python
import pandas as pd
df = pd.DataFrame({"x": [3, 1, 2]})
df.sort_values("x")          # df өзгермейді! Жаңа кесте қайтарылып, жоғалады
df = df.sort_values("x")     # Дұрыс: нәтижені меншіктеу
print(df["x"].tolist())
```

**4. `loc` пен `iloc`-ты шатастыру** — индекс сандар болса (0, 1, 2), сұрыптаудан кейін олар орындармен сәйкес келмейді. Белгі — `loc`, орын — `iloc`.

**5. `to_csv` кезінде индексті жазып жіберу** — файлда атаусыз «Unnamed: 0» бағаны пайда болады. `index=False`.

**6. Циклмен жолдарды аралау** (`for i in range(len(df))`) — баяу әрі Pandas стиліне жат. Векторлық амалдар: `df["avg"] = (df["a"] + df["b"]) / 2`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Pandas | Кестелік деректерді талдау кітапханасы |
| `Series` | Индексі бар бір өлшемді массив (кестенің бағаны) |
| `DataFrame` | Аталған бағандары бар екі өлшемді кесте |
| Индекс | Жолдардың белгілері (сандар немесе атаулар) |
| `NaN` | Жоқ (белгісіз) мән |
| `loc` / `iloc` | Белгі / орын бойынша қол жеткізу |
| `describe()` | Сандық бағандардың сипаттамалық статистикасы |
| `value_counts()` | Мәндердің кездесу жиілігі |
| `idxmax()` | Ең үлкен мәннің индекс белгісі |

## Ескерту

- `df["col"]` Series қайтарады, ал `df[["col"]]` — бір бағанды DataFrame.
- `loc` кесіндінің оң шекарасын қосады (`df.loc[0:2]` — 3 жол), `iloc` — қоспайды (`df.iloc[0:2]` — 2 жол).
- Көп әдістер (`sort_values`, `drop`, `rename`) **жаңа** объект қайтарады; нәтижені меншіктеу керек: `df = df.sort_values("x")`.
- `df.to_csv("file.csv")` әдепкі бойынша индексті жеке баған етіп сақтайды — әдетте `index=False` қосылады.
- `df["a"][0] = 1` сияқты тізбекті меншіктеу pandas 2.x-те ескерту береді, ал Copy-on-Write режимінде (pandas 3.0-де әдепкі) мүлде істемейді — `df.loc[0, "a"] = 1` жазыңыз (https://pandas.pydata.org/docs/).
