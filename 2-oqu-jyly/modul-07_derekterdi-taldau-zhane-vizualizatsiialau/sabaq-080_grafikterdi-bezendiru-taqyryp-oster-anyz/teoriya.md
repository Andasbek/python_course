# Сабақ 80. Графиктерді безендіру: тақырып, осьтер, аңыз, түс палитрасы.

## Тақырыптар

1. Екі стиль: `plt.` және `fig, ax`
2. Тақырып және осьтер
3. Тор, аңыз, түсініктеме
4. Түстер және палитралар
5. Стильдер және жалпы баптаулар
6. Толық кәсіби мысал: «бұрын» және «кейін»
7. «Кәсіби график» чек-парағы
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Екі стиль: `plt.` және `fig, ax`

`plt.title()`, `plt.xlabel()` жазбасы — бұл **pyplot (күй) стилі**. Кәсіби кодта көбіне **объектілі стиль** қолданылады: фигура мен осьтерді айнымалыға сақтап, солардың әдістерін шақырамыз (бұлар әдістері бар кәдімгі Python объектілері).

```python
import matplotlib.pyplot as plt

fig, ax = plt.subplots(figsize=(8, 5))     # 8×5 дюйм өлшемді фигура
ax.plot([1, 2, 3, 4], [10, 20, 15, 25])
ax.set_title("Объектілі стиль")
ax.set_xlabel("X")
ax.set_ylabel("Y")
plt.show()
```

| pyplot стилі | Объектілі стиль |
|---|---|
| `plt.title(...)` | `ax.set_title(...)` |
| `plt.xlabel(...)` / `plt.ylabel(...)` | `ax.set_xlabel(...)` / `ax.set_ylabel(...)` |
| `plt.xlim(...)` / `plt.ylim(...)` | `ax.set_xlim(...)` / `ax.set_ylim(...)` |
| `plt.xticks(...)` | `ax.set_xticks(...)`, `ax.set_xticklabels(...)` |
| `plt.legend()` | `ax.legend()` |
| `plt.grid()` | `ax.grid()` |

**Неге объектілі стиль жақсы?** Бір фигурада бірнеше график (`plt.subplots(2, 2)`) болғанда әрқайсысын нақты басқарамыз; код түсінікті әрі сенімді.

## 2. Тақырып және осьтер

### Тақырып

Жақсы тақырып **не көрсетілгенін** және мүмкін болса **негізгі қорытындыны** айтады:

- Нашар: «График 1», «Деректер».
- Жақсы: «Алматы филиалы апталық түсімнің жартысын береді».

```python
ax.set_title("Филиалдардың апталық түсімі", fontsize=14, fontweight="bold", loc="left")
fig.suptitle("Кітап дүкендері желісі, наурыз 2027")    # бүкіл фигураның тақырыбы
```

### Ось атаулары — әрдайым **бірлігімен**

```python
ax.set_xlabel("Апта күні")
ax.set_ylabel("Түсім, мың теңге")      # бірлік: теңге? мың теңге? °C? см?
```

### Ось шектері және бөліктері (ticks)

```python
ax.set_ylim(0, 120)                     # Y 0-ден 120-ға дейін
ax.set_xticks([0, 1, 2, 3])             # бөліктер орны
ax.set_xticklabels(["Қаң", "Ақп", "Нау", "Сәу"], rotation=45)   # бөлік жазулары, 45° бұрылған
ax.tick_params(axis="both", labelsize=10)
```

Ұзын атаулар бір-біріне жабысып қалса — `rotation=45` немесе көлденең бағана (`barh`).

### Үлкен сандарды оқылатын ету

`1e6` сияқты жазуды адам түсінбейді. Мәнді алдын ала бөлу ең оңай жол:

```python
import matplotlib.pyplot as plt

filial = ["Алматы", "Астана", "Шымкент"]
tusim = [116200, 80100, 36800]
tusim_myn = [t / 1000 for t in tusim]        # мың теңгеге

fig, ax = plt.subplots()
ax.bar(filial, tusim_myn)
ax.set_ylabel("Түсім, мың теңге")
plt.show()
```

Немесе форматтаушымен: `ax.yaxis.set_major_formatter("{x:,.0f} ₸")`.

## 3. Тор, аңыз, түсініктеме

### Тор (grid)

Тор мәндерді «оқуға» көмектеседі, бірақ деректен көзге түспеуі керек:

```python
ax.grid(axis="y", color="lightgray", linestyle="--", linewidth=0.7)
ax.set_axisbelow(True)         # тор бағаналардың артында тұрсын
```

### Аңыз (legend)

```python
ax.plot(x, almaty, label="Алматы")
ax.plot(x, astana, label="Астана")
ax.legend(loc="upper left", title="Қала", frameon=False, fontsize=10)
```

`loc` мәндері: `"best"` (әдепкі, өзі таңдайды), `"upper right"`, `"upper left"`, `"lower right"`, `"lower left"`, `"center"`... Графиктен **тыс** орналастыру: `ax.legend(bbox_to_anchor=(1.02, 1), loc="upper left")`.

**Кеңес:** бір ғана сызық болса — аңыз керек емес, тақырып жеткілікті. Сызық аз болса, атауды аңызсыз, сызықтың соңына тікелей жазу (`ax.text`) — одан да түсінікті.

### Түсініктеме (annotation)

Ең маңызды нүктеге көрерменнің назарын аударамыз:

```python
ax.annotate("Рекорд: 63,8 мың ₸",
            xy=(5, 63.8),                    # көрсетілетін нүкте
            xytext=(2.5, 58),                # мәтін орны
            arrowprops={"arrowstyle": "->", "color": "black"})
```

## 4. Түстер және палитралар

### Түсті беру тәсілдері

| Тәсіл | Мысал |
|---|---|
| Атауы | `"red"`, `"steelblue"`, `"darkorange"`, `"seagreen"` |
| Қысқа код | `"r"`, `"g"`, `"b"`, `"k"` (қара) |
| HEX коды | `"#1f77b4"`, `"#E63946"` |
| Tableau палитрасы | `"tab:blue"`, `"tab:orange"`, `"tab:green"` ... (10 түс) |
| Сұр реңк | `"0.7"` (0 — қара, 1 — ақ) |
| RGB кортежі | `(0.2, 0.4, 0.6)` — әр мән 0..1 |

### Палитра түрлері

```text
Санаттық (qualitative)  ■ ■ ■ ■ ■   — бір-біріне ұқсамайтын түстер: қалалар, санаттар      → tab10, Set2
Реттік (sequential)     □ ▫ ▪ ■ █   — ақшылдан қоюға: «аз → көп» (халық тығыздығы)        → viridis, Blues
Екіжақты (diverging)    ■ ▪ □ ▪ ■   — ортасы бейтарап: «теріс ← 0 → оң» (температура ауытқуы) → coolwarm, RdBu
```

Түс картасынан түстер тізімін алу:

```python
import matplotlib.pyplot as plt

cmap = plt.get_cmap("viridis")
tuster = [cmap(i / 4) for i in range(5)]      # 5 біркелкі түс
fig, ax = plt.subplots()
ax.bar(["A", "B", "C", "D", "E"], [5, 8, 12, 15, 20], color=tuster)
plt.show()
```

### Түс таңдау ережелері

1. **Мағынасы бар түс:** жақсы — жасыл, нашар — қызыл; суық — көк, ыстық — қызыл.
2. **Аз түс — көп мағына.** Барлық бағана бір түсті (сұр), ал назар аударатын біреуі — ашық түсті (**акцент**).
3. **Түс соқырлығын** (дальтонизм — ер адамдардың шамамен 8%-ы) ескеру: қызыл + жасылды қатар қоймау; `viridis`, `tab10`, `cividis` палитралары қауіпсіз.
4. Ақ-қара басып шығарғанда да ажыратылуы үшін сызықтарға әртүрлі `linestyle`/`marker` беру.
5. Бір есептегі барлық графикте бір санатқа **бір түс** (Алматы әрдайым көк).

### Акцент мысалы

```python
import matplotlib.pyplot as plt

kunder = ["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"]
tusim = [20.9, 18.2, 24.1, 22.4, 32.0, 63.8, 51.7]     # мың теңге
tuster = ["tab:orange" if t == max(tusim) else "lightgray" for t in tusim]

fig, ax = plt.subplots(figsize=(8, 4.5))
baganalar = ax.bar(kunder, tusim, color=tuster)
ax.bar_label(baganalar, fmt="%.1f")
ax.set_title("Сенбі — аптаның ең табысты күні", loc="left", fontsize=14, fontweight="bold")
ax.set_ylabel("Түсім, мың теңге")
ax.spines[["top", "right"]].set_visible(False)       # артық жақтауларды алу
ax.grid(axis="y", linestyle="--", alpha=0.4)
ax.set_axisbelow(True)
plt.tight_layout()
plt.show()
```

Нәтижесі: алты сұр бағана және бір қызғылт сары бағана (Сб). Тақырып графиктің негізгі хабарын айтып тұр.

## 5. Стильдер және жалпы баптаулар

Дайын стильдер бір жолмен бүкіл графиктің түрін өзгертеді:

```python
import matplotlib.pyplot as plt

print(plt.style.available[:6])       # қолжетімді стильдер тізімінің басы
plt.style.use("ggplot")              # немесе "seaborn-v0_8", "bmh", "fivethirtyeight", "default"
```

Жаһандық баптаулар (`rcParams`) — бағдарламаның басында бір рет:

```python
import matplotlib.pyplot as plt

plt.rcParams["figure.figsize"] = (8, 5)
plt.rcParams["font.size"] = 11
plt.rcParams["axes.spines.top"] = False
plt.rcParams["axes.spines.right"] = False
```

## 6. Толық кәсіби мысал: «бұрын» және «кейін»

**Бұрын:**

```python
import matplotlib.pyplot as plt

ailar = ["Қаң", "Ақп", "Нау", "Сәу", "Мам", "Мау", "Шіл", "Там", "Қыр", "Қаз", "Қар", "Жел"]
almaty = [-4.7, -3.0, 3.9, 11.6, 16.8, 21.8, 24.4, 23.4, 18.0, 10.7, 2.7, -2.9]
astana = [-14.2, -13.4, -6.0, 5.5, 13.6, 19.1, 20.9, 18.8, 12.5, 4.4, -5.0, -11.5]

plt.plot(ailar, almaty)
plt.plot(ailar, astana)
plt.show()
```

**Кейін:**

```python
import matplotlib.pyplot as plt

ailar = ["Қаң", "Ақп", "Нау", "Сәу", "Мам", "Мау", "Шіл", "Там", "Қыр", "Қаз", "Қар", "Жел"]
almaty = [-4.7, -3.0, 3.9, 11.6, 16.8, 21.8, 24.4, 23.4, 18.0, 10.7, 2.7, -2.9]
astana = [-14.2, -13.4, -6.0, 5.5, 13.6, 19.1, 20.9, 18.8, 12.5, 4.4, -5.0, -11.5]

fig, ax = plt.subplots(figsize=(9, 5))
ax.plot(ailar, almaty, color="#E76F51", marker="o", linewidth=2, label="Алматы")
ax.plot(ailar, astana, color="#264653", marker="s", linewidth=2, linestyle="--", label="Астана")
ax.axhline(0, color="gray", linewidth=0.8)
ax.fill_between(ailar, almaty, astana, color="gray", alpha=0.1)   # айырманы бояу

ax.set_title("Астана қыста Алматыдан 10 °C-қа дейін суық", loc="left",
             fontsize=14, fontweight="bold")
ax.set_xlabel("Ай")
ax.set_ylabel("Орташа температура, °C")
ax.set_ylim(-20, 30)
ax.grid(axis="y", linestyle=":", alpha=0.5)
ax.spines[["top", "right"]].set_visible(False)
ax.legend(loc="upper left", frameon=False)
ax.annotate("Ақпан: айырма 10,4 °C", xy=(1, -13.4), xytext=(2.5, -18),
            arrowprops={"arrowstyle": "->"})
fig.text(0.01, 0.01, "Дерек көзі: климаттық орташа мәндер (шамамен)", fontsize=8, color="gray")
plt.tight_layout()
plt.savefig("temperatura.png", dpi=200, bbox_inches="tight")
plt.show()
```

## 7. «Кәсіби график» чек-парағы

| № | Тексеру | Иә/Жоқ |
|---|---|---|
| 1 | Диаграмма түрі сұраққа сай | |
| 2 | Тақырып бар және хабарды айтады | |
| 3 | Осьтер аталған, **бірлігі** көрсетілген | |
| 4 | Бағаналы диаграммада Y осі 0-ден басталады | |
| 5 | Сандар оқылады (`1e6` жоқ, жазулар бір-біріне жабыспаған) | |
| 6 | Бірнеше қатар болса — аңыз бар (немесе тікелей белгі) | |
| 7 | Түстер мағыналы, 5–6-дан көп емес, түс соқырлығына қауіпсіз | |
| 8 | Артық элемент жоқ (қалың жақтау, ашық тор, 3D эффект) | |
| 9 | Маңызды нүкте түсініктемемен/акцентпен белгіленген | |
| 10 | Дерек көзі көрсетілген, сурет жоғары сапада сақталған | |

## Жиі кездесетін қателер

**1. Стильдерді араластыру.**
```python
fig, ax = plt.subplots(1, 2)
plt.title("Сол жақ")      # тек соңғы (оң) графикке түседі!
```
Дұрысы:
```python
fig, ax = plt.subplots(1, 2)
ax[0].set_title("Сол жақ")
ax[1].set_title("Оң жақ")
```

**2. `ax.title("...")` немесе `ax.xlabel("...")` деп жазу** — `TypeError` немесе `AttributeError` береді. Axes-те әдістер `set_title`, `set_xlabel`, `set_ylim` деп аталады: объектілі стильде баптау әдістерінің көбі `set_` деп басталады.

**3. Ось белгілері кесіліп қалады** — `plt.tight_layout()` немесе `savefig(..., bbox_inches="tight")`.

**4. «Кемпірқосақ»** — әр бағана әртүрлі түсте, бірақ түстің мағынасы жоқ. Санаттарды X осі ажыратып тұр — түс артық.

**5. Стиль бүкіл бағдарламаға әсер етеді.** `plt.style.use("dark_background")` кейінгі барлық графикке қолданылады; қайтару: `plt.style.use("default")`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `fig, ax = plt.subplots()` | Фигура мен осьтер объектілерін жасау |
| Ticks (бөліктер) | Осьтегі белгіленген мәндер және олардың жазулары |
| Аңыз (legend) | Түстер/сызықтар нені білдіретінін түсіндіретін блок |
| Түсініктеме (annotation) | Графиктегі нүктеге бағытталған мәтін (жебемен) |
| Палитра | Графикте қолданылатын түстер жиыны |
| Түс картасы (colormap) | Санды түске айналдыратын үздіксіз шкала (`viridis`) |
| Акцент | Негізгі элементті ерекше түспен бөлу |
| `spines` | Графиктің жақтау сызықтары |

## Ескерту

- Бір фигурада екі стильді араластырмаңыз: `plt.*` функциялары «ағымдағы» осьтермен (`plt.gca()`) жұмыс істейді, ал олар әрдайым қажетті `ax`-пен сәйкес келе бермейді.
- Matplotlib 3.6+ нұсқасында `"seaborn"` стилі `"seaborn-v0_8"` деп өзгертілген; ескі атау `OSError: 'seaborn' not found in the style library` қатесін береді.
- `ax.set_xticks()` шақырмай `ax.set_xticklabels()` жазсаңыз, `FixedFormatter should only be used together with FixedLocator` ескертуі шығады; бөліктер мен белгілерді бірге беріңіз: `ax.set_xticks(pos, labels)` (Matplotlib 3.5+).
- Кирилл және қазақ әріптері (ә, ғ, қ, ң, ө, ұ, ү, һ, і) әдепкі DejaVu Sans қаріпімен көрсетіледі; әріп орнына шаршылар шықса, қаріпті `plt.rcParams["font.family"]` арқылы беріңіз.
- Бірнеше фигура ашық болғанда `plt.savefig()`-тен гөрі `fig.savefig()` сенімдірек: дәл қажетті фигура сақталады.
- Құжаттама: [Customizing Matplotlib](https://matplotlib.org/stable/users/explain/customizing.html) және [Choosing Colormaps](https://matplotlib.org/stable/users/explain/colors/colormaps.html).
