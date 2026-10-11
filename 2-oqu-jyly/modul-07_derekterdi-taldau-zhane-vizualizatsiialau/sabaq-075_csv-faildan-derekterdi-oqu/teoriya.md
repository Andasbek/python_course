# Сабақ 75. CSV файлдан деректерді оқу. Деректерді қарау, таңдау және сүзу.

## Тақырыптар

1. Деректер жиыны (dataset)
2. `pd.read_csv()` — CSV оқу
3. Деректерді зерттеу алгоритмі
4. Таңдау (selection)
5. Сүзу (filtering)
6. Жиі кездесетін қателер
7. Негізгі ұғымдар

## 1. Деректер жиыны (dataset)

Бүгін «Облыстық информатика олимпиадасының нәтижелері» деп аталатын оқу деректер жиынымен жұмыс істейміз. Файлды мына код жасайды (оны бір рет іске қосыңыз):

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2027)
n = 60
girls = ["Айгерім", "Дана", "Әлия", "Аружан", "Мадина", "Жансая"]
boys = ["Нұрлан", "Ержан", "Темірлан", "Санжар", "Алихан", "Бекзат"]
cities = ["Алматы", "Астана", "Шымкент", "Қарағанды", "Ақтөбе"]
gender = rng.choice(["қ", "ұ"], n)
names = [f"{rng.choice(girls if g == 'қ' else boys)} {chr(65 + i % 26)}." for i, g in enumerate(gender)]
df = pd.DataFrame({
    "id": range(1, n + 1),
    "name": names,
    "gender": gender,
    "grade": rng.integers(7, 12, n),
    "city": rng.choice(cities, n, p=[0.35, 0.25, 0.2, 0.1, 0.1]),
    "task1": rng.integers(0, 101, n),
    "task2": rng.integers(0, 101, n),
    "task3": rng.integers(0, 101, n),
    "date": rng.choice(["2027-02-10", "2027-02-11"], n),
})
df["total"] = df[["task1", "task2", "task3"]].sum(axis=1)
df.to_csv("olympiad.csv", index=False, encoding="utf-8")
print("olympiad.csv жасалды:", df.shape)
```

Нәтижесі:
```text
olympiad.csv жасалды: (60, 10)
```

Файлдың басы (мәтіндік редакторда):
```text
id,name,gender,grade,city,task1,task2,task3,date,total
1,Алихан A.,ұ,10,Шымкент,83,95,82,2027-02-11,260
2,Мадина B.,қ,7,Шымкент,79,97,100,2027-02-10,276
3,Айгерім C.,қ,8,Алматы,25,14,2,2027-02-10,41
...
```

(Мәндер кездейсоқ генерацияланған, бірақ `seed=2027` болғандықтан бәрінде бірдей.)

## 2. `pd.read_csv()` — CSV оқу

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
print(df.shape)
print(df.head(3))
```

Бір жол — және бүкіл файл (60 жол, 10 баған) кестеге айналды! Тақырыптар баған атауларына, сандар сан типтеріне автоматты айналады.

### Маңызды параметрлер

| Параметр | Не үшін | Мысал |
|---|---|---|
| `sep` | Бөлгіш (әдепкі `,`) | `sep=";"` — Excel үшін экспортталған файл |
| `encoding` | Кодировка | `"utf-8"`, `"utf-8-sig"`, `"cp1251"` (ескі Windows файлдары) |
| `usecols` | Тек қажетті бағандар | `usecols=["name", "total"]` |
| `index_col` | Индекс болатын баған | `index_col="id"` |
| `parse_dates` | Күн бағандарын `datetime`-ға айналдыру | `parse_dates=["date"]` |
| `nrows` | Тек алғашқы n жол (үлкен файлды тексеру) | `nrows=100` |
| `header`, `names` | Тақырып жоқ болса, атауларды беру | `header=None, names=["a", "b"]` |
| `decimal` | Бөлшек бөлгіш | `decimal=","` → «3,5» |
| `dtype` | Типті нақты көрсету | `dtype={"phone": str}` — алдыңғы нөлдер жоғалмауы үшін |
| `na_values` | Қай мәндер «бос» деп саналады | `na_values=["—", "жоқ"]` |

```python
import pandas as pd

df = pd.read_csv("olympiad.csv", index_col="id", parse_dates=["date"],
                 usecols=["id", "name", "city", "total", "date"])
print(df.dtypes)
print(df.head(3))
```

Нәтижесі:
```text
name            object
city            object
date    datetime64[ns]
total            int64
dtype: object
          name     city       date  total
id
1    Алихан A.  Шымкент 2027-02-11    260
2    Мадина B.  Шымкент 2027-02-10    276
3   Айгерім C.   Алматы 2027-02-10     41
```

Интернеттен тікелей оқуға да болады: `pd.read_csv("https://.../data.csv")`. Ал `;` бөлгішті, BOM-ы бар файл: `pd.read_csv("students.csv", sep=";", encoding="utf-8-sig")`.

## 3. Деректерді зерттеу алгоритмі

Жаңа деректер жиынын алғанда, бірден «талдауға» кіріспейміз. Алдымен **таныстық** (EDA — Exploratory Data Analysis, зерттеушілік талдау):

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

print("1. Өлшемі:", df.shape)
print("2. Бағандар:", df.columns.tolist())
print("3. Басы мен соңы:")
print(df.head(3))
print(df.tail(2))
print("4. Кездейсоқ жолдар:")
print(df.sample(3, random_state=1))
print("5. Типтер және бос мәндер:")
df.info()
print("6. Сандық статистика:")
print(df[["task1", "task2", "task3", "total"]].describe().round(1))
print("7. Категориялар:")
print(df["city"].value_counts())
print("Сыныптар:", sorted(df["grade"].unique().tolist()), "| қала саны:", df["city"].nunique())
```

| Қадам | Әдіс | Сұрақ |
|---|---|---|
| 1 | `shape` | Қанша жол, қанша баған? |
| 2 | `columns` | Қандай деректер бар? |
| 3 | `head`, `tail` | Деректер қалай көрінеді? Соңында «қоқыс» жоқ па? |
| 4 | `sample` | Ортасында не бар? |
| 5 | `info` | Типтер дұрыс па? Бос мәндер бар ма? |
| 6 | `describe` | Мәндер ауқымы қисынды ма (мысалы, балл > 100 жоқ па)? |
| 7 | `value_counts`, `unique` | Санаттар қандай, жиілігі қанша? |

## 4. Таңдау (selection)

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

names = df["name"]                              # бір баған — Series
short = df[["name", "city", "total"]]           # бірнеше баған — DataFrame
first10 = df.iloc[:10]                          # алғашқы 10 жол
block = df.iloc[5:8, [1, 4, 9]]                 # 5–7 жолдар, 1, 4, 9-бағандар
by_label = df.loc[df.index[:3], "task1":"task3"]   # баған кесіндісі атау бойынша (соңғысы қосылады)
print(block)
print(by_label)
```

Ең жоғары / төмен нәтижелер — сұрыптамай-ақ:

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
print(df.nlargest(5, "total")[["name", "city", "total"]])     # үздік 5
print(df.nsmallest(3, "task2")[["name", "task2"]])
```

## 5. Сүзу (filtering)

Сүзу — NumPy-дегідей **логикалық маска** арқылы.

### Бір шарт

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

mask = df["city"] == "Алматы"
print(mask.head(3).tolist())        # True/False тізбегі
almaty = df[mask]
print("Алматы қатысушылары:", len(almaty))
print("200-ден жоғары:", len(df[df["total"] > 200]))
```

### Бірнеше шарт: `&`, `|`, `~`

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

# 9-сынып ҚЫЗДАРЫ (екі шарт та орындалуы керек) — әр шарт жақшада!
girls9 = df[(df["grade"] == 9) & (df["gender"] == "қ")]
# Астана НЕМЕСЕ Шымкент
south_capital = df[(df["city"] == "Астана") | (df["city"] == "Шымкент")]
# Алматыдан ЕМЕС
not_almaty = df[~(df["city"] == "Алматы")]
print(len(girls9), len(south_capital), len(not_almaty))
```

| Оператор | Мағынасы | Python баламасы (жай мәндер үшін) |
|---|---|---|
| `&` | және | `and` |
| `\|` | немесе | `or` |
| `~` | емес | `not` |

### Ыңғайлы әдістер

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

big_cities = df[df["city"].isin(["Алматы", "Астана", "Шымкент"])]   # тізімнің бірі
middle = df[df["total"].between(120, 180)]                          # аралық (шеттерімен)
aig = df[df["name"].str.contains("Айгерім")]                        # мәтін ішінде
starts_a = df[df["name"].str.startswith("А")]                       # мәтін басы
full_task1 = df[df["task1"] == df["task1"].max()]                   # ең жоғары мәнге тең
print(len(big_cities), len(middle), len(aig), len(starts_a), len(full_task1))
```

### `query()` — SQL-ға ұқсас жазу

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
min_total = 150
result = df.query("grade >= 10 and city == 'Алматы' and total > @min_total")   # @ — Python айнымалысы
print(result[["name", "grade", "total"]])
```

`query` ұзын шарттарды оқуға жеңіл етеді. SQL-мен салыстырыңыз:

| SQL | Pandas |
|---|---|
| `SELECT name, total FROM t` | `df[["name", "total"]]` |
| `WHERE city = 'Алматы'` | `df[df["city"] == "Алматы"]` |
| `WHERE grade IN (9, 10)` | `df[df["grade"].isin([9, 10])]` |
| `WHERE total BETWEEN 100 AND 200` | `df[df["total"].between(100, 200)]` |
| `WHERE name LIKE '%ан%'` | `df[df["name"].str.contains("ан")]` |
| `ORDER BY total DESC LIMIT 5` | `df.sort_values("total", ascending=False).head(5)` |
| `SELECT COUNT(*)` | `len(df)` |

### Сүзу + таңдау + сұрыптау + сақтау — бір тізбекте

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
winners = (
    df[(df["grade"] >= 10) & (df["total"] >= 180)]       # сүзу
    .sort_values("total", ascending=False)               # сұрыптау
    [["name", "grade", "city", "total"]]                 # бағандарды таңдау
)
winners.to_csv("winners.csv", index=False, encoding="utf-8-sig")
print(f"{len(winners)} жеңімпаз winners.csv файлына сақталды")
print(winners.head())
```

Нәтижесі:
```text
6 жеңімпаз winners.csv файлына сақталды
         name  grade     city  total
0   Алихан A.     10  Шымкент    260
19    Дана T.     10   Алматы    238
41  Бекзат P.     10   Ақтөбе    216
46  Жансая U.     11   Алматы    211
26  Санжар A.     10  Шымкент    187
```

Жақшадағы «тізбек» (method chaining) — Pandas-та кең тараған стиль: әр қадам жаңа жолда, оқуға оңай.

### Сүзу арқылы өзгерту

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
df["prize"] = "қатысушы"                                  # барлығына
df.loc[df["total"] >= 200, "prize"] = "I орын"            # шартқа сай жолдарға
df.loc[df["total"].between(170, 199), "prize"] = "II орын"
print(df["prize"].value_counts())
```

Нәтижесі:
```text
prize
қатысушы    40
I орын      12
II орын      8
Name: count, dtype: int64
```

**Маңызды:** өзгерту үшін **`df.loc[маска, "баған"] = мән`** жазамыз, `df[маска]["баған"] = мән` емес (ол көшірмені өзгертіп, ескерту береді).

## Жиі кездесетін қателер

**1. `FileNotFoundError`** — файл бағдарламамен бір папкада емес. `import os; print(os.getcwd())` арқылы ағымдағы папканы тексеріңіз немесе толық жол беріңіз.

**2. `UnicodeDecodeError`** — файл басқа кодировкада. `encoding="cp1251"` немесе `encoding="utf-8-sig"` байқап көріңіз.

**3. Барлық деректер бір бағанда** (`df.shape` → `(60, 1)`) — бөлгіш басқа: `sep=";"`.

**4. Шарттарды жақшасыз жазу:**
```python
import pandas as pd
df = pd.DataFrame({"grade": [9, 10], "total": [150, 210]})
# Қате: df[df["grade"] == 10 & df["total"] > 200]   → & бірінші орындалады, TypeError/қате нәтиже
print(df[(df["grade"] == 10) & (df["total"] > 200)])   # Дұрыс: әр шарт жақшада
```

**5. `and` / `or` қолдану** — `ValueError: The truth value of a Series is ambiguous`. Pandas-та `&`, `|`, `~`.

**6. `SettingWithCopyWarning`** — `df[df.x > 0]["y"] = 1` деп жазу. Дұрысы: `df.loc[df.x > 0, "y"] = 1`.

**7. Сандар мәтін болып оқылды** (`dtype: object`) — бағанда «—», «жоқ» сияқты мәндер бар. `na_values=["—"]` немесе `pd.to_numeric(df["col"], errors="coerce")`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Деректер жиыны (dataset) | Талдауға арналған деректер жинағы |
| `read_csv` | CSV файлды DataFrame-ге оқу функциясы |
| EDA | Зерттеушілік талдау: деректермен алғашқы танысу |
| `sample` | Кездейсоқ жолдар |
| Логикалық маска | `True`/`False` Series-і, жолдарды сүзу үшін |
| `isin` / `between` | Тізімге кіру / аралыққа түсу шарттары |
| `.str` | Мәтіндік бағандарға арналған әдістер жиыны |
| `query` | Шартты мәтін түрінде жазу әдісі |
| `nlargest` / `nsmallest` | Ең үлкен / кіші n жол |
| Method chaining | Әдістерді тізбектей шақыру стилі |

## Ескерту

- `read_csv` баған типтерін өзі анықтайды; оны `dtype={"phone": str}` арқылы нақты беруге болады — әйтпесе алдыңғы нөлі бар телефондар мен кодтар санға айналып, `0`-ден айырылады.
- Сүзу шарттарында `&`, `|`, `~` және әр шартты жақшаға алу қолданылады; `and`/`or` `ValueError` шығарады.
- `df.query()` сыртқы айнымалыларға `@` арқылы жүгінеді: `df.query("total > @limit")`; бос орыны бар баған атаулары кері тырнақшаға алынады.
- Сүзгі арқылы өзгертуді бір амалмен жасаңыз: `df.loc[mask, "col"] = value`; `df[mask]["col"] = value` жазбасы бастапқы кестені өзгертпейді (`SettingWithCopyWarning`).
- Бірнеше мәнге тексеру үшін `|` тізбегінен гөрі `isin([...])` ыңғайлы; `between(a, b)` әдепкі бойынша екі шекараны да қосады. Құжаттама: https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html.
