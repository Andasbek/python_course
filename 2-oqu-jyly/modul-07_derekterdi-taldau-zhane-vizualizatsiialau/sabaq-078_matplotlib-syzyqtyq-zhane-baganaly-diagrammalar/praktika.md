# Сабақ 78. Практикалық тапсырмалар: сызықтық және бағаналы диаграммалар

Барлық тапсырманы бөлек файлда орындаңыз (`t1.py`, `t2.py`, ...). Әр графиктің тақырыбы мен ось белгілері болуы міндетті. Әр графикті `png` файлға да сақтаңыз — олар 81-сабақтағы есепке керек болады.

## Базалық деңгей

**1-тапсырма. Қадам санауыш.** Ерлан апта бойы смартфонындағы қадам санын жазып отырды:

| Дс | Сс | Ср | Бс | Жм | Сб | Жс |
|---|---|---|---|---|---|---|
| 6200 | 8100 | 5400 | 9800 | 7300 | 12500 | 3900 |

Дөңгелек маркерлі сызықтық диаграмма салыңыз. Дәрігерлер ұсынатын 10 000 қадам деңгейін `plt.axhline(10000, color="red", linestyle="--")` арқылы белгілеңіз. Ерлан неше күн нормаға жетті? (кодпен есептеңіз)

**2-тапсырма. Сауалнама.** Сыныпта «Қай платформада көбірек уақыт өткізесің?» деген сауалнама жүрді: YouTube — 11, TikTok — 9, Instagram — 6, Telegram — 4, Ойындар — 8. Бағаналы диаграмма салып, әр бағананың үстіне мәнін жазыңыз (`bar_label`).

**3-тапсырма. Валюта бағамы.** Бір айдағы USD бағамы (теңге) апта сайын:
`[502, 505, 498, 510, 515, 512, 520, 518]`. Апта нөмірлері 1–8. Сызықтық диаграмма салыңыз; ең жоғары нүктені `plt.annotate` немесе `plt.text` көмегімен белгілеңіз (мысалы: `plt.text(7, 520, "Максимум")`).

**4-тапсырма. Көлденең рейтинг.** Қазақстанның ең биік тауларының бірнешеуі (шамамен, м): Хан Тәңірі — 6995, Талғар — 4979, Белуха — 4506, Бесбақан — 4380, Мұзтау (Сайрам) — 4238. Көлденең бағаналы диаграмманы ең биігі жоғарыда тұратындай етіп салыңыз.

## Кеңейтілген деңгей

**5-тапсырма. Екі қаланы салыстыру.** Теориядағы Алматы мен Астана температураларына Шымкентті қосыңыз:
`shymkent = [-1.0, 1.0, 7.5, 14.5, 20.0, 25.5, 28.0, 26.5, 20.5, 13.0, 5.5, 0.5]`.
Үш сызық, әрқайсысының түсі мен маркері әртүрлі, аңыз, тор (grid). Кодпен есептеп шығарыңыз: Алматы мен Астананың температура айырмасы ең үлкен ай қайсы?

**6-тапсырма. Түсім диаграммалары (`kitap_dukeni.csv`).**
а) Филиалдар бойынша түсімнің бағаналы диаграммасы (кему ретімен);
ә) Апта күндері бойынша түсімнің сызықтық диаграммасы (күндер реті Дс→Жс сақталсын: `groupby("kun", sort=False)`);
б) Екі графикті `filial.png`, `kunder.png` ретінде сақтаңыз.

**7-тапсырма. Топтастырылған бағаналар.** `pivot_table` арқылы филиал × санат түсім кестесін алып, `kesteh.plot(kind="bar")` арқылы бір жолмен топтастырылған бағаналы диаграмма салыңыз. Содан кейін дәл сондай графикті таза Matplotlib-пен (`plt.bar` + ығысу) салыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

**8-тапсырма. Бір терезеде екі график.** `plt.subplot(1, 2, 1)` және `plt.subplot(1, 2, 2)` арқылы бір фигураның сол жағына қадамдар сызығын, оң жағына күндер бойынша «норма орындалды/орындалмады» бағаналарын (жасыл/қызыл түспен) салыңыз. Кеңес: түстер тізімін шартпен жасаңыз: `["green" if q >= 10000 else "red" for q in qadam]`.

**9-тапсырма. Жинақталған өсу.** 1-тапсырмадағы қадамдардың **жинақталған қосындысын** (күн сайын жалпы қанша жүрді) есептеп (кеңес: `itertools.accumulate` немесе Pandas `cumsum()`), бір графикте екі Y осімен көрсетіңіз: сол жақта — күнделікті бағаналар, оң жақта — жинақталған сызық (`ax2 = ax.twinx()`).

## Үй тапсырмасы

1. 76–77-сабақтардағы өз деректеріңіз бойынша кемінде бір сызықтық және бір бағаналы диаграмма салыңыз.
2. Әр графиктің астына (README немесе түсініктемеге) «Графиктен не көрінеді?» деген сұраққа 1–2 сөйлеммен жауап жазыңыз.
3. Суреттерді `data-analysis/images/` папкасына сақтап, GitHub-қа жүктеңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import matplotlib.pyplot as plt

kunder = ["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"]
qadam = [6200, 8100, 5400, 9800, 7300, 12500, 3900]

plt.plot(kunder, qadam, marker="o", color="purple")
plt.axhline(10000, color="red", linestyle="--", label="Норма: 10 000")
plt.title("Ерланның апталық қадам саны")
plt.xlabel("Апта күні")
plt.ylabel("Қадам")
plt.legend()
plt.savefig("qadam.png", dpi=150, bbox_inches="tight")
plt.show()

norma = len([q for q in qadam if q >= 10000])
print("Нормаға жеткен күндер:", norma)
```
Жауап: 1 күн (Сб).

**2-тапсырма**
```python
import matplotlib.pyplot as plt

platformalar = ["YouTube", "TikTok", "Instagram", "Telegram", "Ойындар"]
sany = [11, 9, 6, 4, 8]

b = plt.bar(platformalar, sany, color="coral")
plt.bar_label(b)
plt.title("Оқушылар қай платформада көп уақыт өткізеді")
plt.ylabel("Оқушы саны")
plt.savefig("platforma.png", dpi=150, bbox_inches="tight")
plt.show()
```

**3-тапсырма**
```python
import matplotlib.pyplot as plt

aptalar = list(range(1, 9))
usd = [502, 505, 498, 510, 515, 512, 520, 518]

plt.plot(aptalar, usd, marker="o")
max_i = usd.index(max(usd))
plt.annotate("Максимум", xy=(aptalar[max_i], usd[max_i]),
             xytext=(aptalar[max_i] - 2, usd[max_i] + 2),
             arrowprops={"arrowstyle": "->"})
plt.title("USD бағамы, теңге")
plt.xlabel("Апта")
plt.ylabel("Теңге")
plt.show()
```

**4-тапсырма**
```python
import matplotlib.pyplot as plt

taular = ["Хан Тәңірі", "Талғар", "Белуха", "Бесбақан", "Мұзтау (Сайрам)"]
biiktik = [6995, 4979, 4506, 4380, 4238]

plt.barh(taular, biiktik, color="sienna")
plt.gca().invert_yaxis()
plt.xlabel("Биіктігі, м")
plt.title("Қазақстанның биік таулары (шамамен)")
plt.show()
```

**5-тапсырма**
```python
import matplotlib.pyplot as plt

ailar = ["Қаң", "Ақп", "Нау", "Сәу", "Мам", "Мау",
         "Шіл", "Там", "Қыр", "Қаз", "Қар", "Жел"]
almaty = [-4.7, -3.0, 3.9, 11.6, 16.8, 21.8, 24.4, 23.4, 18.0, 10.7, 2.7, -2.9]
astana = [-14.2, -13.4, -6.0, 5.5, 13.6, 19.1, 20.9, 18.8, 12.5, 4.4, -5.0, -11.5]
shymkent = [-1.0, 1.0, 7.5, 14.5, 20.0, 25.5, 28.0, 26.5, 20.5, 13.0, 5.5, 0.5]

plt.plot(ailar, almaty, marker="o", label="Алматы")
plt.plot(ailar, astana, marker="s", label="Астана")
plt.plot(ailar, shymkent, marker="^", label="Шымкент")
plt.title("Үш қаланың орташа айлық температурасы")
plt.ylabel("°C")
plt.grid(True, alpha=0.3)
plt.legend()
plt.show()

airma = [a - b for a, b in zip(almaty, astana)]
i = airma.index(max(airma))
print("Айырма ең үлкен ай:", ailar[i], round(airma[i], 1), "°C")
```
Жауап: Ақпан (10,4 °C).

**6-тапсырма**
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("kitap_dukeni.csv")
df["tusim"] = df["sany"] * df["baga"]

filial = df.groupby("filial")["tusim"].sum().sort_values(ascending=False)
plt.bar(filial.index, filial.values, color="teal")
plt.title("Филиалдар бойынша апталық түсім")
plt.ylabel("Теңге")
plt.savefig("filial.png", dpi=150, bbox_inches="tight")
plt.show()

kunder = df.groupby("kun", sort=False)["tusim"].sum()
plt.plot(kunder.index, kunder.values, marker="o", color="darkorange")
plt.title("Апта күндері бойынша түсім")
plt.ylabel("Теңге")
plt.savefig("kunder.png", dpi=150, bbox_inches="tight")
plt.show()
```

**7-тапсырма**
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("kitap_dukeni.csv")
df["tusim"] = df["sany"] * df["baga"]
kesteh = pd.pivot_table(df, values="tusim", index="filial", columns="sanat",
                        aggfunc="sum", fill_value=0)

# 1-тәсіл: Pandas
kesteh.plot(kind="bar", title="Филиал × санат түсімі")
plt.ylabel("Теңге")
plt.show()

# 2-тәсіл: таза Matplotlib
x = list(range(len(kesteh.index)))
eni = 0.25
for j, sanat in enumerate(kesteh.columns):
    plt.bar([i + (j - 1) * eni for i in x], kesteh[sanat], width=eni, label=sanat)
plt.xticks(x, kesteh.index)
plt.ylabel("Теңге")
plt.legend()
plt.show()
```

**8-тапсырма**
```python
import matplotlib.pyplot as plt

kunder = ["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"]
qadam = [6200, 8100, 5400, 9800, 7300, 12500, 3900]
tuster = ["green" if q >= 10000 else "red" for q in qadam]

plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
plt.plot(kunder, qadam, marker="o")
plt.title("Қадамдар")

plt.subplot(1, 2, 2)
plt.bar(kunder, qadam, color=tuster)
plt.axhline(10000, color="black", linestyle="--")
plt.title("Норма орындалды ма?")

plt.tight_layout()
plt.show()
```

**9-тапсырма**
```python
import pandas as pd
import matplotlib.pyplot as plt

kunder = ["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"]
qadam = pd.Series([6200, 8100, 5400, 9800, 7300, 12500, 3900], index=kunder)
zhinaq = qadam.cumsum()

fig, ax = plt.subplots()
ax.bar(kunder, qadam, color="lightblue", label="Күнделікті")
ax.set_ylabel("Күнделікті қадам")
ax2 = ax.twinx()
ax2.plot(kunder, zhinaq, color="navy", marker="o", label="Жинақталған")
ax2.set_ylabel("Жинақталған қадам")
ax.set_title("Апталық қадамдар")
plt.show()
print("Апта бойы барлығы:", zhinaq.iloc[-1])
```
Жауап: 53 200 қадам.

</details>
