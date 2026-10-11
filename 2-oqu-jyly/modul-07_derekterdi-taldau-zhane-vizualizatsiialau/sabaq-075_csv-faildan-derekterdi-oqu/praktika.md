# Сабақ 75. Практикалық тапсырмалар: CSV оқу, қарау, таңдау және сүзу

> Деректер: `olympiad.csv` (теорияның 1-бөліміндегі кодпен жасаңыз). Бағандар: `id, name, gender, grade, city, task1, task2, task3, date, total`.

## Базалық деңгей

### 1-тапсырма. Деректермен танысу (EDA)
`olympiad.csv` файлын оқып, теориядағы 7 қадамды орындаңыз. Дәптерге жауап жазыңыз:
а) қанша қатысушы, қанша баған?
ә) бос мәндер бар ма?
б) қай есеп (task1/2/3) орташа есеппен ең қиын болды?
в) қай қаладан қатысушы ең көп / ең аз?
г) қыздар мен ұлдар саны.

### 2-тапсырма. `read_csv` параметрлері
а) Тек `name`, `city`, `total` бағандарын оқыңыз (`usecols`), индекс — `id`.
ә) Тек алғашқы 10 жолды оқыңыз (`nrows`).
б) `date` бағанын күн ретінде оқып (`parse_dates`), оның типін тексеріңіз.
в) 61-сабақтағы `;` бөлгішті, `utf-8-sig` кодировкалы `students.csv` файлын дұрыс оқыңыз.

### 3-тапсырма. Таңдау
а) 15-ші жолдағы қатысушының барлық деректері (`iloc`);
ә) алғашқы 5 қатысушының аты мен жалпы балы;
б) үздік 5 және ең төмен 3 нәтиже (`nlargest`, `nsmallest`);
в) `task1`-ден 100 немесе 0 балл алғандар.

### 4-тапсырма. Сүзу
Мына сұрақтарға жауап беретін сүзгілер жазыңыз (әрқайсысының санын шығарыңыз):
а) 11-сынып оқушылары;
ә) Астанадан келген қыздар;
б) Шымкент немесе Ақтөбе қатысушылары (`isin`);
в) жалпы балы 120 мен 180 аралығындағылар (`between`);
г) Алматыдан емес, 10–11-сынып, жалпы балы 200-ден жоғары;
д) аты «Д» әрпінен басталатындар (`str.startswith`).

### 5-тапсырма. Жеңімпаздар хаттамасы
Жүлде ережесі: жалпы балл ≥ 220 — I орын, 190–219 — II орын, 160–189 — III орын. `prize` бағанын `loc` арқылы толтырыңыз, жүлдегерлерді (тек `name`, `grade`, `city`, `total`, `prize`) балл бойынша сұрыптап, `prizes.csv` файлына (`utf-8-sig`) сақтаңыз. Әр жүлде санын шығарыңыз.

## Кеңейтілген деңгей

### 6-тапсырма. `query` vs маска
4-тапсырманың г) және б) пункттерін `query()` арқылы қайта жазыңыз. Қай жазу оқуға жеңіл? Бір пунктте Python айнымалысын (`@`) пайдаланыңыз.

### 7-тапсырма. «Тұрақты» қатысушылар
Үш есептің әрқайсысынан кемінде 50 балл алғандарды табыңыз. Кеңес: `(df[["task1", "task2", "task3"]] >= 50).all(axis=1)`. Ал кем дегенде бір есептен 90+ алғандар ше (`any`)?

### 8-тапсырма. Қалалар есебі
Әр қала үшін циклмен (әзірге `groupby`-сыз): қатысушы саны, орташа жалпы балл, ең үздік қатысушы. Нәтижені DataFrame етіп жинап, орташа балл бойынша сұрыптаңыз. (77-сабақта мұны бір жолмен `groupby` арқылы жасаймыз!)

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Интерактивті сүзгі
Консольдік бағдарлама пайдаланушыдан қала (немесе «барлығы»), сынып аралығы және минималды балды сұрап, сәйкес қатысушылар кестесін шығарады және нәтижені файлға сақтауды ұсынады. Қате енгізуді өңдеңіз.

### 10-тапсырма. Нақты ашық деректер
`data.egov.kz` порталынан, Kaggle-ден немесе басқа ашық көзден кез келген CSV деректер жиынын (кемінде 100 жол) жүктеп, EDA-ның 7 қадамын орындаңыз және кемінде 3 мағыналы сүзгі сұрағын қойып, жауап беріңіз. Деректердің дереккөзі мен лицензиясын көрсетіңіз (71-сабақ!).

## Үй тапсырмасы

1. 74-сабақтағы үй тапсырмасында жасаған `class.csv` файлын оқып, 5 түрлі сүзгі сұрағын құрастырып, жауап беріңіз.
2. Pandas пен SQL сәйкестік кестесін (теориядағы) өз мысалдарыңызбен толықтырыңыз.
3. Ойланыңыз: нақты деректерде қандай «ақаулар» болуы мүмкін? (бос ұяшық, қате жазылған қала, қайталанған жол...) Кемінде 5 мысал жазыңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
print("Өлшемі:", df.shape)
print("Бос мәндер:", df.isna().sum().sum())
means = df[["task1", "task2", "task3"]].mean()
print(means.round(1))
print("Ең қиын есеп:", means.idxmin())
counts = df["city"].value_counts()
print("Ең көп:", counts.idxmax(), "| ең аз:", counts.idxmin())
print(df["gender"].value_counts())
```

**2-тапсырма**
```python
import pandas as pd

a = pd.read_csv("olympiad.csv", usecols=["id", "name", "city", "total"], index_col="id")
b = pd.read_csv("olympiad.csv", nrows=10)
c = pd.read_csv("olympiad.csv", parse_dates=["date"])
print(a.head(3))
print(b.shape)
print(c["date"].dtype)
# в) d = pd.read_csv("students.csv", sep=";", encoding="utf-8-sig")
```

**3-тапсырма**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
print(df.iloc[14])
print(df.loc[:4, ["name", "total"]])
print(df.nlargest(5, "total")[["name", "total"]])
print(df.nsmallest(3, "total")[["name", "total"]])
print(df[df["task1"].isin([0, 100])][["name", "task1"]])
```

**4-тапсырма**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
filters = {
    "а) 11-сынып": df["grade"] == 11,
    "ә) Астана қыздары": (df["city"] == "Астана") & (df["gender"] == "қ"),
    "б) Шымкент/Ақтөбе": df["city"].isin(["Шымкент", "Ақтөбе"]),
    "в) 120–180": df["total"].between(120, 180),
    "г) Алматыдан емес, 10–11, >200": (df["city"] != "Алматы") & (df["grade"] >= 10) & (df["total"] > 200),
    "д) «Д» әрпінен": df["name"].str.startswith("Д"),
}
for title, mask in filters.items():
    print(f"{title}: {mask.sum()}")
```

**5-тапсырма**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
df["prize"] = ""
df.loc[df["total"] >= 220, "prize"] = "I орын"
df.loc[df["total"].between(190, 219), "prize"] = "II орын"
df.loc[df["total"].between(160, 189), "prize"] = "III орын"
prizes = (df[df["prize"] != ""]
          .sort_values("total", ascending=False)
          [["name", "grade", "city", "total", "prize"]])
prizes.to_csv("prizes.csv", index=False, encoding="utf-8-sig")
print(prizes["prize"].value_counts())
```

**6-тапсырма**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
limit = 200
g = df.query("city != 'Алматы' and grade >= 10 and total > @limit")
b = df.query("city == 'Астана' and gender == 'қ'")
print(len(g), len(b))
```

**7-тапсырма**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
tasks = df[["task1", "task2", "task3"]]
stable = df[(tasks >= 50).all(axis=1)]
stars = df[(tasks >= 90).any(axis=1)]
print("Барлық есептен 50+:", len(stable))
print(stable[["name", "task1", "task2", "task3"]])
print("Кемінде бір есептен 90+:", len(stars))
```

**8-тапсырма**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
rows = []
for city in df["city"].unique():
    part = df[df["city"] == city]
    best = part.loc[part["total"].idxmax()]
    rows.append({"city": city, "count": len(part),
                 "avg_total": round(part["total"].mean(), 1),
                 "best": f"{best['name']} ({best['total']})"})
report = pd.DataFrame(rows).sort_values("avg_total", ascending=False)
print(report.to_string(index=False))
```

**9-тапсырма**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
city = input("Қала (немесе «барлығы»): ").strip()
try:
    g_from, g_to = map(int, input("Сынып аралығы (мысалы 9 11): ").split())
    min_total = int(input("Минималды балл: "))
except ValueError:
    raise SystemExit("Қате енгізу: сандар күтілген")
mask = df["grade"].between(g_from, g_to) & (df["total"] >= min_total)
if city.lower() != "барлығы":
    mask &= df["city"] == city
result = df[mask].sort_values("total", ascending=False)[["name", "grade", "city", "total"]]
if result.empty:
    print("Сәйкес қатысушы жоқ")
else:
    print(result.to_string(index=False))
    if input("Файлға сақтау керек пе? (иә/жоқ): ").lower() == "иә":
        result.to_csv("filter_result.csv", index=False, encoding="utf-8-sig")
        print("filter_result.csv сақталды")
```

**10-тапсырма** — шығармашылық; бағалау: дереккөз мен лицензия көрсетілген, EDA 7 қадамы орындалған, сүзгі сұрақтары мағыналы және дұрыс жазылған.

</details>
