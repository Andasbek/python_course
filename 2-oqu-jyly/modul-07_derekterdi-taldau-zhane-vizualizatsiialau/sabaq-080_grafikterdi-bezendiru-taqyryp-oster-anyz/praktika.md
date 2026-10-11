# Сабақ 80. Практикалық тапсырмалар: графиктерді безендіру

Барлық тапсырманы **объектілі стильде** (`fig, ax = plt.subplots()`) орындаңыз. Әр графикті теориядағы «Кәсіби график» чек-парағымен тексеріңіз.

## Базалық деңгей

**1-тапсырма. Нашар графикті құтқар.** Төмендегі код «сәтсіз» график салады. Оны кемінде 6 жақсартумен қайта жазыңыз: тақырып (хабары бар), ось атаулары бірлігімен, Y осі 0-ден, мәндер бағаналардың үстінде, артық жақтаулар жоқ, акцент түс (ең көп оқушы саны бар үйірме).

```python
import matplotlib.pyplot as plt

uyirmeler = ["Python", "Робототехника", "Шахмат", "Домбыра", "Бейнелеу өнері", "Футбол"]
sany = [26, 22, 14, 11, 9, 24]
plt.bar(uyirmeler, sany, color=["red", "green", "blue", "yellow", "purple", "cyan"])
plt.ylim(8, 27)
plt.show()
```

**2-тапсырма. Аңыз және сызық стильдері.** Үш оқушының тоқсандық бағасы (5 балдық жүйеде, орташа):
- Әлия: `[4.2, 4.5, 4.7, 4.9]`
- Нұрлан: `[3.8, 3.9, 4.4, 4.6]`
- Дана: `[4.6, 4.4, 4.3, 4.5]`

X осінде «I тоқсан ... IV тоқсан». Үш сызық: әртүрлі түс, маркер және сызық түрі (ақ-қара принтерде де ажыратылсын). Аңыз графиктің ішінде, жақтаусыз (`frameon=False`). Y осі 3.5-тен 5.0-ге дейін (сызықтық графикте 0-ден бастау міндетті емес — неге екенін түсініктемеге жазыңыз).

**3-тапсырма. Түсініктеме.** USD бағамы `[502, 505, 498, 510, 515, 512, 520, 518]` (апталар 1–8) графигінде ең төменгі және ең жоғарғы нүктеге жебелі түсініктеме қосыңыз (`ax.annotate`). Түсініктеме мәтіндері кодтан есептелсін (мысалы `f"Мин: {min(usd)} ₸"`).

**4-тапсырма. Стильдерді салыстыру.** Бір графикті `plt.style.use(...)` арқылы үш түрлі стильде (`"default"`, `"ggplot"`, `"bmh"`) сақтаңыз: `style_default.png`, `style_ggplot.png`, `style_bmh.png`. Қайсысы ұнады және неге?

## Кеңейтілген деңгей

**5-тапсырма. Палитра таңдау.** Қазақстанның 6 өңірінің шартты халық тығыздығы (адам/км²): Алматы облысы — 9.2, Түркістан облысы — 17.5, Қостанай облысы — 3.0, Маңғыстау облысы — 4.6, Ұлытау облысы — 0.9, Жамбыл облысы — 8.1. Көлденең бағаналы диаграмма салыңыз: түсі тығыздыққа қарай **реттік** палитрадан (`Blues` немесе `viridis`) алынсын, `colorbar` қосылсын. Неге мұнда санаттық палитра жарамайды?

**6-тапсырма. Бір стильдегі есеп.** `kitap_dukeni.csv` бойынша 2×2 фигура (`plt.subplots(2, 2, figsize=(12, 8))`):
а) филиалдар түсімі (бағана), ә) күндер бойынша түсім (сызық), б) санаттар үлесі (дөңгелек), в) баға × дана (шашырау).
Талаптар: `fig.suptitle`, әр Axes-тің өз тақырыбы, барлық графикте бір санатқа бір түс (`tuster = {"Кітап": ..., "Кеңсе": ..., "Ойыншық": ...}`), `tight_layout`, `savefig(dpi=200)`.

**7-тапсырма. Өз функцияң.** `beautify(ax, title, xlabel, ylabel)` функциясын жазыңыз: ол берілген Axes-ке тақырыпты (сол жақ, қалың), ось атауларын, жұмсақ торды қосып, жоғарғы/оң жақтауларды алып тастасын. Оны 2–3 түрлі графикте қолданыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

**8-тапсырма. Өз стиль файлың.** Matplotlib стиль файлын (`.mplstyle`) жасаңыз — мысалы, `uyirme.mplstyle`:
```text
figure.figsize: 9, 5
axes.spines.top: False
axes.spines.right: False
axes.grid: True
grid.alpha: 0.3
axes.titlesize: 14
axes.titleweight: bold
axes.prop_cycle: cycler('color', ['264653', '2A9D8F', 'E9C46A', 'F4A261', 'E76F51'])
```
және оны `plt.style.use("uyirme.mplstyle")` арқылы қолданыңыз. Бір графикті стильсіз және стильмен салыстырыңыз.

**9-тапсырма. «Инфографика».** Бір ғана фигурада: үлкен тақырып, бір негізгі сан (`fig.text` арқылы ірі шрифтпен, мысалы «233 100 ₸»), қасында шағын бағаналы диаграмма және дерек көзі. Нәтиже әлеуметтік желіге жариялауға жарайтындай болсын.

## Үй тапсырмасы

1. 78–79-сабақтарда салған өз графиктеріңіздің ең сәтсізін таңдап, чек-парақ бойынша жақсартыңыз. «Бұрын» және «кейін» суреттерін сақтаңыз.
2. Келесі сабақ — **практикум**: өз деректер жиыныңызды таңдап, ойланып келіңіз (тізім келесі сабақтың `praktika.md`-сінде). Деректі алдын ала тауып, CSV түрінде дайындауға болады.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import matplotlib.pyplot as plt

uyirmeler = ["Python", "Робототехника", "Шахмат", "Домбыра", "Бейнелеу өнері", "Футбол"]
sany = [26, 22, 14, 11, 9, 24]

# сұрыптау: ең көбі жоғарыда
juptar = sorted(zip(sany, uyirmeler))
sany_s = [j[0] for j in juptar]
atau_s = [j[1] for j in juptar]
tuster = ["#2A9D8F" if s == max(sany) else "lightgray" for s in sany_s]

fig, ax = plt.subplots(figsize=(8, 4.5))
b = ax.barh(atau_s, sany_s, color=tuster)
ax.bar_label(b, padding=3)
ax.set_title("Python — ең танымал үйірме", loc="left", fontsize=14, fontweight="bold")
ax.set_xlabel("Оқушы саны, адам")
ax.set_xlim(0, 30)
ax.spines[["top", "right"]].set_visible(False)
plt.tight_layout()
plt.show()
```
Жақсартулар: көлденең бағана (ұзын атаулар), сұрыпталған, ось 0-ден, мәндер жазылған, акцент, хабары бар тақырып, бірлігі бар ось, артық жақтау жоқ.

**2-тапсырма**
```python
import matplotlib.pyplot as plt

toqsan = ["I тоқсан", "II тоқсан", "III тоқсан", "IV тоқсан"]
fig, ax = plt.subplots(figsize=(8, 5))
ax.plot(toqsan, [4.2, 4.5, 4.7, 4.9], color="tab:blue", marker="o", linestyle="-", label="Әлия")
ax.plot(toqsan, [3.8, 3.9, 4.4, 4.6], color="tab:orange", marker="s", linestyle="--", label="Нұрлан")
ax.plot(toqsan, [4.6, 4.4, 4.3, 4.5], color="tab:green", marker="^", linestyle=":", label="Дана")
ax.set_ylim(3.5, 5.0)
ax.set_title("Нұрлан ең үлкен өсім көрсетті", loc="left", fontweight="bold")
ax.set_ylabel("Орташа баға (5 балдық)")
ax.legend(frameon=False, loc="lower right")
ax.grid(axis="y", alpha=0.3)
plt.show()
# Сызықтық графикте біз өзгерісті (тренд) көрсетеміз, ұзындықты емес,
# сондықтан Y осін деректер аймағына жақындатуға болады.
```

**3-тапсырма**
```python
import matplotlib.pyplot as plt

aptalar = list(range(1, 9))
usd = [502, 505, 498, 510, 515, 512, 520, 518]
i_min = usd.index(min(usd))
i_max = usd.index(max(usd))

fig, ax = plt.subplots()
ax.plot(aptalar, usd, marker="o", color="tab:green")
ax.annotate(f"Мин: {min(usd)} ₸", xy=(aptalar[i_min], usd[i_min]),
            xytext=(aptalar[i_min] + 0.5, usd[i_min] - 4), arrowprops={"arrowstyle": "->"})
ax.annotate(f"Макс: {max(usd)} ₸", xy=(aptalar[i_max], usd[i_max]),
            xytext=(aptalar[i_max] - 2.5, usd[i_max] + 2), arrowprops={"arrowstyle": "->"})
ax.set_ylim(490, 528)
ax.set_title("USD бағамы, 8 апта")
ax.set_xlabel("Апта")
ax.set_ylabel("Теңге")
plt.show()
```

**4-тапсырма**
```python
import matplotlib.pyplot as plt

for stil in ["default", "ggplot", "bmh"]:
    plt.style.use(stil)
    fig, ax = plt.subplots()
    ax.plot([1, 2, 3, 4, 5], [3, 5, 4, 7, 6], marker="o")
    ax.set_title(f"Стиль: {stil}")
    fig.savefig(f"style_{stil}.png", dpi=120)
    plt.close(fig)
plt.style.use("default")
```

**5-тапсырма**
```python
import matplotlib.pyplot as plt
from matplotlib.colors import Normalize
from matplotlib.cm import ScalarMappable

onirler = ["Алматы обл.", "Түркістан обл.", "Қостанай обл.",
           "Маңғыстау обл.", "Ұлытау обл.", "Жамбыл обл."]
tygyzdyq = [9.2, 17.5, 3.0, 4.6, 0.9, 8.1]

norm = Normalize(vmin=0, vmax=max(tygyzdyq))
cmap = plt.get_cmap("Blues")
tuster = [cmap(norm(t)) for t in tygyzdyq]

fig, ax = plt.subplots(figsize=(8, 4.5))
ax.barh(onirler, tygyzdyq, color=tuster, edgecolor="gray")
ax.invert_yaxis()
ax.set_xlabel("Халық тығыздығы, адам/км² (шартты)")
ax.set_title("Өңірлердің халық тығыздығы", loc="left", fontweight="bold")
fig.colorbar(ScalarMappable(norm=norm, cmap=cmap), ax=ax, label="адам/км²")
plt.tight_layout()
plt.show()
# Мұнда мән "аз → көп" ретімен өзгереді, сондықтан реттік палитра ғана
# "қою = көп" деген мағынаны береді; санаттық түстер ретті көрсетпейді.
```

**6-тапсырма**
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("kitap_dukeni.csv")
df["tusim"] = df["sany"] * df["baga"]
tuster = {"Кітап": "#264653", "Кеңсе": "#E9C46A", "Ойыншық": "#E76F51"}

fig, ax = plt.subplots(2, 2, figsize=(12, 8))

filial = df.groupby("filial")["tusim"].sum().sort_values(ascending=False) / 1000
ax[0, 0].bar(filial.index, filial.values, color="#2A9D8F")
ax[0, 0].set_title("Филиалдар түсімі")
ax[0, 0].set_ylabel("мың ₸")

kun = df.groupby("kun", sort=False)["tusim"].sum() / 1000
ax[0, 1].plot(kun.index, kun.values, marker="o", color="#2A9D8F")
ax[0, 1].set_title("Күндер бойынша түсім")
ax[0, 1].set_ylabel("мың ₸")

sanat = df.groupby("sanat")["tusim"].sum()
ax[1, 0].pie(sanat.values, labels=sanat.index, autopct="%1.0f%%",
             colors=[tuster[s] for s in sanat.index], startangle=90)
ax[1, 0].set_title("Санаттар үлесі")

for s, top in df.groupby("sanat"):
    ax[1, 1].scatter(top["baga"], top["sany"], color=tuster[s], label=s, s=60)
ax[1, 1].set_title("Баға мен дана")
ax[1, 1].set_xlabel("Баға, ₸")
ax[1, 1].set_ylabel("Дана")
ax[1, 1].legend(frameon=False)

for a in [ax[0, 0], ax[0, 1], ax[1, 1]]:
    a.spines[["top", "right"]].set_visible(False)
fig.suptitle("Кітап дүкендері: апталық есеп", fontsize=16, fontweight="bold")
plt.tight_layout()
fig.savefig("esep.png", dpi=200)
plt.show()
```

**7-тапсырма**
```python
import matplotlib.pyplot as plt


def beautify(ax, title, xlabel="", ylabel=""):
    """Axes-ті бірыңғай кәсіби стильде безендіреді."""
    ax.set_title(title, loc="left", fontsize=13, fontweight="bold")
    ax.set_xlabel(xlabel)
    ax.set_ylabel(ylabel)
    ax.grid(axis="y", linestyle="--", alpha=0.4)
    ax.set_axisbelow(True)
    ax.spines[["top", "right"]].set_visible(False)


fig, ax = plt.subplots(1, 2, figsize=(10, 4))
ax[0].bar(["A", "B", "C"], [3, 7, 5])
beautify(ax[0], "Бағаналар", "Санат", "Мән")
ax[1].plot([1, 2, 3, 4], [2, 3, 5, 4], marker="o")
beautify(ax[1], "Сызық", "Уақыт", "Мән")
plt.tight_layout()
plt.show()
```

**8–9-тапсырмалар** — шығармашылық; бағалау: стиль файлы жұмыс істейді, графиктер чек-парақтың кемінде 8 пунктін қанағаттандырады. 9-тапсырма үлгісі:
```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(8, 4.5))
fig.text(0.05, 0.80, "Кітап дүкендері, апта", fontsize=16, fontweight="bold")
fig.text(0.05, 0.50, "233 100 ₸", fontsize=40, color="#E76F51", fontweight="bold")
fig.text(0.05, 0.40, "жалпы түсім", fontsize=12, color="gray")
ax = fig.add_axes([0.55, 0.15, 0.4, 0.6])
ax.bar(["Алматы", "Астана", "Шымкент"], [116.2, 80.1, 36.8], color="#2A9D8F")
ax.set_ylabel("мың ₸")
ax.spines[["top", "right"]].set_visible(False)
fig.text(0.05, 0.05, "Дерек көзі: kitap_dukeni.csv", fontsize=8, color="gray")
plt.show()
```

</details>
