# Сабақ 74. Практикалық тапсырмалар: Pandas — Series және DataFrame

> Барлық тапсырмада `import pandas as pd`. Циклсіз, Pandas әдістерімен шешуге тырысыңыз.

## Базалық деңгей

### 1-тапсырма. Қалалар Series-і
Қазақстанның 6 қаласының халқын (мың адам) сөздіктен `Series` етіп құрыңыз: Алматы 2200, Астана 1500, Шымкент 1250, Қарағанды 500, Ақтөбе 580, Тараз 370.
а) ең көп және ең аз халқы бар қала (`idxmax`, `idxmin`);
ә) 1 миллионнан асатын қалалар;
б) халықты өсу ретімен сұрыптаңыз;
в) барлық қалалардың жалпы халқы және әр қаланың үлесі (%), 1 ондық таңбамен.

```text
Ең көп: Алматы, ең аз: Тараз
Миллионер қалалар: ['Алматы', 'Астана', 'Шымкент']
...
Алматы     34.4
Астана     23.4
...
```

### 2-тапсырма. Апталық қадамдар
Бір аптадағы қадам саны: Дс 8200, Сс 10500, Ср 6400, Бс 12000, Жм 9100, Сб 15300, Жс 4800.
а) орташа, медиана, ең белсенді күн;
ә) 10 000 қадам нормасын орындаған күндер саны;
б) әр күннің нормадан айырмасы (`steps - 10000`);
в) демалыс күндерінің (Сб, Жс) қосындысы.

### 3-тапсырма. Сынып кестесі
Сөздіктен 6 оқушының DataFrame-ін құрыңыз (бағандар: `name`, `gender` (ұ/қ), `grade`, `math`, `kazakh`, `english`). Содан кейін:
а) `shape`, `dtypes`, `head(3)`, `describe()` шығарыңыз;
ә) `total` (үш пән қосындысы) және `avg` (орташа, 1 ондық) бағандарын қосыңыз;
б) `avg` бойынша кему ретімен сұрыптап, тек `name` және `avg` бағандарын көрсетіңіз;
в) ағылшын тілінің ең жоғары бағасын алған оқушының атын шығарыңыз.

### 4-тапсырма. API нәтижесінен DataFrame
Төмендегі «API жауабынан» (жазбалар тізімі) DataFrame жасаңыз, бағаны теңгеге айналдырыңыз (1 £ = 650 ₸), `price_kzt` бағанын қосып, ең қымбат 3 кітапты көрсетіңіз және кестені `books.csv`-ге сақтаңыз.

```python
books = [
    {"title": "A Light in the Attic", "price": 51.77, "rating": 3},
    {"title": "Tipping the Velvet", "price": 53.74, "rating": 1},
    {"title": "Soumission", "price": 50.10, "rating": 1},
    {"title": "Sharp Objects", "price": 47.82, "rating": 4},
    {"title": "Sapiens", "price": 54.23, "rating": 5},
]
```

### 5-тапсырма. `loc` / `iloc` жаттығуы
3-тапсырмадағы кестені `set_index("name")` арқылы индекстеңіз. Сосын:
а) бір оқушының барлық бағалары (`loc`);
ә) екі оқушының математикадан бағалары;
б) кестенің 2–4 жолдары және алғашқы 2 бағаны (`iloc`);
в) бір оқушының қазақ тілі бағасын өзгертіп, нәтижені көрсетіңіз.

## Кеңейтілген деңгей

### 6-тапсырма. SQLite → Pandas
61-сабақтағы `school.db` қорынан `pd.read_sql("SELECT * FROM students", conn)` арқылы DataFrame алыңыз. Қалалар бойынша оқушы санын (`value_counts`), ең жоғары балды оқушыны, орташа балды шығарыңыз.

### 7-тапсырма. Series арифметикасы
Екі дүкеннің тауар бағалары Series түрінде берілген (тауарлар жиыны толық сәйкес келмейді). Әр тауар бойынша қай дүкен арзан екенін, бағалар айырмасын табыңыз; тек бір дүкенде бар тауарларды (`NaN`) бөлек көрсетіңіз (кеңес: `isna()`).

```python
import pandas as pd

shop_a = pd.Series({"нан": 230, "сүт": 520, "жұмыртқа": 950, "ірімшік": 2800})
shop_b = pd.Series({"сүт": 480, "нан": 250, "жұмыртқа": 990, "айран": 450})
```

### 8-тапсырма. Ауа райы DataFrame-і
67-сабақтағы Open-Meteo `daily` сөздігінен (бағандар — тізімдер) бірден `pd.DataFrame(data["daily"])` құрыңыз. `time` бағанын `pd.to_datetime` арқылы күнге айналдырып, индекс етіңіз; `range` (макс − мин) бағанын қосыңыз; ең үлкен тәуліктік ауытқу болған күнді табыңыз. (Интернет болмаса, үлгі деректерді қолданыңыз.)

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Мини-«Excel»
Консольдік бағдарлама: пайдаланушы бағандар атауын және жолдарды енгізеді, бағдарлама DataFrame құрады және команда күтеді: `show`, `sort <баған>`, `mean <баған>`, `add <жаңа> = <баған1> + <баған2>`, `save <файл>`, `exit`.

### 10-тапсырма. Жеке бюджет
Бір айдағы шығындарыңызды (күні, санаты, сомасы) кемінде 15 жазба ретінде DataFrame-ге енгізіңіз. Санаттар бойынша жалпы шығын (`groupby` — келесі сабақтардың алдын ала танысуы), ең қымбат 3 сатып алу, күндік орташа шығынды есептеңіз.

## Үй тапсырмасы

1. Өз сыныбыңыз (немесе ойдан шығарылған) туралы кемінде 10 жол, 5 бағаннан тұратын DataFrame құрып, `class.csv` етіп сақтаңыз — келесі сабақта қолданамыз.
2. `describe()` нәтижесіндегі 25%, 50%, 75% мәндері нені білдіретінін түсіндіріп жазыңыз.
3. Интернеттен ашық деректер жиынын (CSV) табыңыз: мысалы, `data.egov.kz` порталынан немесе Kaggle-ден. Сілтемесін сақтаңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import pandas as pd

pop = pd.Series({"Алматы": 2200, "Астана": 1500, "Шымкент": 1250,
                 "Қарағанды": 500, "Ақтөбе": 580, "Тараз": 370})
print("Ең көп:", pop.idxmax(), ", ең аз:", pop.idxmin())
print("Миллионер қалалар:", pop[pop > 1000].index.tolist())
print(pop.sort_values())
print("Жалпы:", pop.sum())
print((pop / pop.sum() * 100).round(1))
```

**2-тапсырма**
```python
import pandas as pd

steps = pd.Series([8200, 10500, 6400, 12000, 9100, 15300, 4800],
                  index=["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"])
print("Орташа:", round(steps.mean()), "Медиана:", steps.median(), "Ең белсенді:", steps.idxmax())
print("Норма орындалды:", (steps >= 10000).sum(), "күн")
print(steps - 10000)
print("Демалыс күндері:", steps[["Сб", "Жс"]].sum())
```

**3-тапсырма**
```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия", "Темірлан"],
    "gender": ["қ", "ұ", "қ", "ұ", "қ", "ұ"],
    "grade": [9, 9, 9, 9, 9, 9],
    "math": [92, 78, 88, 95, 70, 84],
    "kazakh": [95, 82, 91, 76, 89, 80],
    "english": [88, 90, 79, 85, 93, 72],
})
print(df.shape)
print(df.dtypes)
print(df.head(3))
print(df.describe())
df["total"] = df[["math", "kazakh", "english"]].sum(axis=1)
df["avg"] = (df["total"] / 3).round(1)
print(df.sort_values("avg", ascending=False)[["name", "avg"]])
print("Ағылшын тілінің үздігі:", df.loc[df["english"].idxmax(), "name"])
```

**4-тапсырма**
```python
import pandas as pd

books = [
    {"title": "A Light in the Attic", "price": 51.77, "rating": 3},
    {"title": "Tipping the Velvet", "price": 53.74, "rating": 1},
    {"title": "Soumission", "price": 50.10, "rating": 1},
    {"title": "Sharp Objects", "price": 47.82, "rating": 4},
    {"title": "Sapiens", "price": 54.23, "rating": 5},
]
df = pd.DataFrame(books)
df["price_kzt"] = (df["price"] * 650).round()
print(df.sort_values("price_kzt", ascending=False).head(3))
df.to_csv("books.csv", index=False, encoding="utf-8-sig")
```

**5-тапсырма**
```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия", "Темірлан"],
    "math": [92, 78, 88, 95, 70, 84],
    "kazakh": [95, 82, 91, 76, 89, 80],
    "english": [88, 90, 79, 85, 93, 72],
}).set_index("name")
print(df.loc["Дана"])
print(df.loc[["Айгерім", "Ержан"], "math"])
print(df.iloc[1:4, :2])
df.loc["Нұрлан", "kazakh"] = 87
print(df.loc["Нұрлан"])
```

**6-тапсырма**
```python
import sqlite3
import pandas as pd

with sqlite3.connect("school.db") as conn:
    df = pd.read_sql("SELECT * FROM students", conn)
print(df["city"].value_counts())
print("Үздік:", df.loc[df["score"].idxmax(), "name"])
print("Орташа балл:", round(df["score"].mean(), 1))
```

**7-тапсырма**
```python
import pandas as pd

shop_a = pd.Series({"нан": 230, "сүт": 520, "жұмыртқа": 950, "ірімшік": 2800})
shop_b = pd.Series({"сүт": 480, "нан": 250, "жұмыртқа": 990, "айран": 450})
diff = shop_a - shop_b
both = diff.dropna()
print("Айырма (A − B):")
print(both)
print("A арзан:", both[both < 0].index.tolist())
print("B арзан:", both[both > 0].index.tolist())
print("Тек бір дүкенде:", diff[diff.isna()].index.tolist())
```

**8-тапсырма**
```python
import pandas as pd

daily = {"time": ["2027-03-01", "2027-03-02", "2027-03-03"],
         "temperature_2m_max": [5.1, 8.4, 3.0], "temperature_2m_min": [-4.2, -1.0, -6.5]}
df = pd.DataFrame(daily)
df["time"] = pd.to_datetime(df["time"])
df = df.set_index("time")
df["range"] = df["temperature_2m_max"] - df["temperature_2m_min"]
print(df)
print("Ең үлкен ауытқу:", df["range"].idxmax().date(), f"({df['range'].max():.1f} °C)")
```

**9-тапсырма** (қысқа үлгі)
```python
import pandas as pd

cols = input("Бағандар (үтірмен): ").split(",")
rows = []
while (line := input("Жол (бос — аяқтау): ").strip()):
    rows.append([float(v) if v.replace(".", "", 1).lstrip("-").isdigit() else v
                 for v in line.split(",")])
df = pd.DataFrame(rows, columns=[c.strip() for c in cols])
while True:
    cmd = input("> ").split()
    if not cmd:
        continue
    if cmd[0] == "show":
        print(df)
    elif cmd[0] == "sort":
        df = df.sort_values(cmd[1])
    elif cmd[0] == "mean":
        print(df[cmd[1]].mean())
    elif cmd[0] == "add" and len(cmd) == 6:
        df[cmd[1]] = df[cmd[3]] + df[cmd[5]]
    elif cmd[0] == "save":
        df.to_csv(cmd[1], index=False)
    elif cmd[0] == "exit":
        break
```

**10-тапсырма**
```python
import pandas as pd

data = {
    "date": pd.date_range("2027-03-01", periods=15, freq="2D"),
    "category": ["тамақ", "көлік", "ойын-сауық", "тамақ", "кітап", "тамақ", "көлік", "киім",
                 "тамақ", "ойын-сауық", "көлік", "тамақ", "кітап", "тамақ", "көлік"],
    "amount": [1500, 300, 2000, 1200, 3500, 900, 300, 8000, 1100, 2500, 300, 1400, 2800, 700, 300],
}
df = pd.DataFrame(data)
print(df.groupby("category")["amount"].sum().sort_values(ascending=False))
print(df.nlargest(3, "amount"))
print("Күндік орташа:", round(df["amount"].sum() / 30), "₸")
```

</details>
