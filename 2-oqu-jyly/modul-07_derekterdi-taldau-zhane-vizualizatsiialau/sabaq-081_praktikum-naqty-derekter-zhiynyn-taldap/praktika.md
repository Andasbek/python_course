# Сабақ 81. Практикалық тапсырмалар: аналитикалық есеп құру (практикум)

## Практикумның тапсырмасы

Бір деректер жиынын таңдап, **толық талдау циклін** орындаңыз және нәтижесін GitHub-та аналитикалық есеп (README.md) түрінде ресімдеңіз. Жұмыс жеке орындалады (жұппен — педагогтің рұқсатымен). Келесі сабақта (82) жобаны 3–4 минутта қорғайсыз.

**Міндетті талаптар (минимум):**
1. Кемінде **3 зерттеу сұрағы**.
2. Деректі **тазалау** (кемінде 3 түрлі әрекет) және тазалау есебі.
3. Кемінде **2 `groupby`/`agg`/`pivot_table`** талдауы.
4. Кемінде **3 график**, кемінде **2 түрлі** диаграмма типі, барлығы «кәсіби график» чек-парағы бойынша безендірілген.
5. Код функцияларға бөлінген, docstring және түсініктемелер бар (PEP 8).
6. README.md — үлгі құрылым бойынша, сұрақтарға нақты жауаптар (сандармен).

## Деректер жиынын таңдау

### А нұсқасы. «Оқушының өмір салты» (базалық)

Теориядағы үлгі жобаның деректері. Үлгіні қайталамай, **өз сұрақтарыңызды** қойыңыз (мысалы: ұл мен қыздың айырмашылығы, сыныптар бойынша телефон уақыты, ұйқысы 7 сағаттан аз оқушылар үлесі).

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2027)
n = 150
synyp = rng.integers(7, 12, n)
jynys = rng.choice(["Ұл", "Қыз"], n)
uiqy = np.round(rng.normal(7.5, 1.0, n), 1)
telefon = np.round(rng.uniform(1, 7, n), 1)
sport = rng.choice(["Иә", "Жоқ"], n, p=[0.45, 0.55])
ball = np.round(60 + 4 * (uiqy - 7) - 3 * (telefon - 4)
                + np.where(sport == "Иә", 3, 0) + rng.normal(10, 7, n)).clip(20, 100)

df = pd.DataFrame({"synyp": synyp, "jynys": jynys, "uiqy": uiqy,
                   "telefon": telefon, "sport": sport, "ball": ball})
# әдейі "ластау"
df.loc[[5, 40, 77], "uiqy"] = np.nan
df.loc[[12, 90], "jynys"] = ["қыз ", "ұл"]
df.loc[33, "ball"] = 450
df.loc[61, "telefon"] = -2
df = pd.concat([df, df.iloc[[3, 50, 100]]], ignore_index=True)
df.to_csv("oqushy_omiri.csv", index=False)
print("oqushy_omiri.csv жасалды:", len(df), "жазба")
```

### Ә нұсқасы. «Менің қаламның ауа райы» (кеңейтілген, API + талдау)

Open-Meteo архивінен (тегін, кілтсіз) өз қалаңыздың бір жылдық күнделікті ауа райын алыңыз. Ұсынылатын сұрақтар: ең ыстық/суық ай; жауын-шашынды күндер саны айлар бойынша; күндізгі-түнгі температура айырмасы; екі қаланы салыстыру.

```python
import requests
import pandas as pd

url = "https://archive-api.open-meteo.com/v1/archive"
params = {
    "latitude": 43.24,            # Алматы
    "longitude": 76.89,
    "start_date": "2025-01-01",
    "end_date": "2025-12-31",
    "daily": "temperature_2m_max,temperature_2m_min,precipitation_sum",
    "timezone": "Asia/Almaty",
}
response = requests.get(url, params=params, timeout=30)
response.raise_for_status()
daily = response.json()["daily"]

df = pd.DataFrame(daily)
df["time"] = pd.to_datetime(df["time"])
df.to_csv("almaty_2025.csv", index=False)
print(df.head())
print("Жазбалар:", len(df))
```

Кеңес: ай бойынша топтау — `df.groupby(df["time"].dt.month)`.

### Б нұсқасы. «Кітап дүкендері» (базалық)

77-сабақтағы `kitap_dukeni.csv`. Деректі өзіңіз кеңейтіңіз: екінші апта қосыңыз (`pd.concat`), «Онлайн» филиалын енгізіңіз, әдейі 3–4 қате қосып, оны тазалаңыз.

### В нұсқасы. Өзіндік деректер жиыны (★ шығармашылық)

data.egov.kz, stat.gov.kz, Kaggle немесе сыныптағы өз сауалнамаңыз. Кемінде 50 жол және 4 баған. Педагогпен алдын ала келісіңіз. Жеке деректерді (аты-жөні, телефон) алып тастаңыз!

## Жұмыс қадамдары

**1-қадам. Жобаны дайындау (5 мин).**
```bash
mkdir taldau-zhobasy
cd taldau-zhobasy
mkdir data images
git init
```
`data/raw.csv` ішіне деректі салыңыз.

**2-қадам. Сұрақтар (5 мин).** README.md файлын ашып, 1-бөлімге 3–5 зерттеу сұрағын жазыңыз. Педагогке көрсетіп, бекітіңіз.

**3-қадам. Зерттеу және тазалау (20 мин).** `analysis.py`-да `load_data()` және `clean_data()` функциялары. Тазалау есебін (неше бос мән, дубликат, қате табылды және не істелді) экранға шығарып, README-нің 3-бөліміне көшіріңіз. `data/clean.csv` сақтаңыз.

**4-қадам. Талдау (20 мин).** `analyze()` функциясы: әр сұраққа бір кесте немесе сан. Нәтижелерді README-ге жазыңыз.

**5-қадам. Визуализация (20 мин).** `make_charts()` функциясы: әр сұраққа бір график, `images/` папкасына `dpi=150` сақтаңыз. Чек-парақпен тексеріңіз.

**6-қадам. Қорытынды және README (10 мин).** 3–5 нақты қорытынды (сандармен), 1–2 ұсыныс, шектеулер, іске қосу нұсқаулығы.

**7-қадам. GitHub (5 мин).**
```bash
git add .
git commit -m "Деректерді талдау жобасы: тазалау, талдау, графиктер, есеп"
git branch -M main
git remote add origin https://github.com/<логин>/taldau-zhobasy.git
git push -u origin main
```

## README.md шаблоны

```text
# Оқушылардың өмір салты және үлгерімі

## 1. Мақсаты және зерттеу сұрақтары
1. Телефонда өткізетін уақыт баллға қалай байланысты?
2. ...

## 2. Деректер
- Көзі: синтетикалық сауалнама (oqushy_omiri.csv)
- Көлемі: 153 жол × 6 баған
- Бағандар: synyp — сынып, jynys — жынысы, uiqy — ұйқы (сағ), ...

## 3. Деректерді тазалау
| Мәселе | Саны | Шешім |
|---|---|---|
| Дубликаттар | 3 | жойылды |
| Ұйқы бос | 3 | медианамен толтырылды |
| ... | | |
Нәтижесі: 148 жазба қалды.

## 4. Талдау нәтижелері
### 4.1. Телефон және балл
![Телефон және балл](images/telefon_ball.png)
Күніне 4 сағаттан көп телефонда отыратындардың орташа баллы — 70,9,
2 сағатқа дейін отыратындарда — 81,4 (корреляция r = −0,49).

## 5. Қорытынды және ұсыныстар
## 6. Шектеулер
## 7. Іске қосу
pip install pandas matplotlib
python analysis.py
```

## Өзін-өзі тексеру чек-парағы (тапсырмас бұрын)

- [ ] `python analysis.py` қатесіз жұмыс істейді, жаңа ортада да (`requirements` көрсетілген)
- [ ] `data/raw.csv` өзгертілмеген, `data/clean.csv` бар
- [ ] Кемінде 3 сұрақ және әрқайсысына жауап бар
- [ ] Кемінде 3 график, 2 түрлі тип; тақырып, ось бірліктері, аңыз
- [ ] Қорытындыда сандар бар
- [ ] Код функцияларға бөлінген, docstring бар
- [ ] GitHub-та README дұрыс көрінеді (суреттер ашылады)

## Үй тапсырмасы

1. Жобаны аяқтаңыз (егер сабақта үлгермесеңіз) және GitHub сілтемесін педагогке жіберіңіз.
2. Қорғауға 3–4 минуттық баяндама дайындаңыз (құрылымы — 82-сабақтың теориясында): сұрақтар → дерек → 2–3 негізгі график → қорытынды.
3. 76–80-сабақтарды қайталаңыз — келесі сабақта өзіндік жұмыс №6.

## Бағалау критерийлері

| Критерий | Үлесі | Толық ұпай үшін |
|---|---|---|
| Бағдарламаның дұрыс жұмыс істеуі және тапсырма талабына сай келуі | 40% | Код қатесіз; барлық 6 міндетті талап орындалған; сұрақтарға жауап бар |
| Алгоритмнің тиімділігі, шешімнің ұтымдылығы | 25% | Тазалау шешімдері негізделген; groupby/agg орынды; диаграмма түрлері сұраққа сай |
| Кодтың оқылымдылығы: атау беру, түсініктеме, құрылым | 20% | Функциялар, docstring, PEP 8, түсінікті атаулар; README құрылымды |
| Жұмысты өз бетінше орындауы және мерзімінде тапсыруы | 15% | Сабақ соңында/келесі сабаққа дейін GitHub-та |

Меңгеру деңгейлері: 85–100% — жоғары, 60–84% — орта, 40–59% — бастапқы.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**А нұсқасы** — толық үлгі шешім теорияның 4-бөлімінде. Қосымша сұрақтарға үлгі:
```python
import pandas as pd

df = pd.read_csv("oqushy_omiri.csv").drop_duplicates()
df["jynys"] = df["jynys"].str.strip().str.capitalize()
df["uiqy"] = df["uiqy"].fillna(df["uiqy"].median())
df = df[df["ball"].between(0, 100) & (df["telefon"] >= 0)].copy()

print(df.groupby("jynys")["ball"].agg(["count", "mean"]).round(1))
print(df.groupby("synyp")["telefon"].mean().round(1))
az_uiqy = (df["uiqy"] < 7).mean() * 100
print(f"Ұйқысы 7 сағаттан аз оқушылар: {az_uiqy:.1f}%")
print(pd.pivot_table(df, values="ball", index="synyp", columns="sport", aggfunc="mean").round(1))
```
Күтілетін нәтижелер: тазалаудан кейін 148 жазба; ұлдар 75,3, қыздар 73,8 (айырма шағын); телефон–балл r ≈ −0,49; ұйқы–балл r ≈ 0,29.

**Ә нұсқасы** — талдау үлгісі:
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("almaty_2025.csv", parse_dates=["time"])
df = df.dropna()
df["ai"] = df["time"].dt.month
df["airma"] = df["temperature_2m_max"] - df["temperature_2m_min"]
df["zhauyn"] = df["precipitation_sum"] > 0.1

aylyq = df.groupby("ai").agg(
    max_orta=("temperature_2m_max", "mean"),
    min_orta=("temperature_2m_min", "mean"),
    zhauyn_kun=("zhauyn", "sum"),
    airma=("airma", "mean"),
).round(1)
print(aylyq)
print("Ең ыстық ай:", aylyq["max_orta"].idxmax())
print("Ең суық ай:", aylyq["min_orta"].idxmin())

fig, ax = plt.subplots(figsize=(9, 5))
ax.plot(aylyq.index, aylyq["max_orta"], marker="o", color="#E76F51", label="Күндізгі max")
ax.plot(aylyq.index, aylyq["min_orta"], marker="o", color="#264653", label="Түнгі min")
ax.fill_between(aylyq.index, aylyq["min_orta"], aylyq["max_orta"], alpha=0.1)
ax.set_xticks(range(1, 13))
ax.set_title("Алматы, 2025: айлық орташа температура", loc="left", fontweight="bold")
ax.set_xlabel("Ай")
ax.set_ylabel("°C")
ax.legend(frameon=False)
ax.spines[["top", "right"]].set_visible(False)
fig.savefig("images/temperatura.png", dpi=150, bbox_inches="tight")

fig, ax = plt.subplots(figsize=(9, 5))
ax.bar(aylyq.index, aylyq["zhauyn_kun"], color="#2A9D8F")
ax.set_xticks(range(1, 13))
ax.set_title("Жауын-шашынды күндер саны", loc="left", fontweight="bold")
ax.set_xlabel("Ай")
ax.set_ylabel("Күн")
fig.savefig("images/zhauyn.png", dpi=150, bbox_inches="tight")
plt.show()
```

**Б және В нұсқалары** — шешім оқушының деректеріне байланысты; бағалау кесте бойынша. Жиі ескерту беретін тұстар: (1) бастапқы файлдың үстіне жазу; (2) қорытындыда сандардың болмауы; (3) бағаналы диаграмманың Y осі 0-ден басталмауы; (4) графиктердің README-де ашылмауы (суреттің салыстырмалы жолы қате).

</details>
