# Сабақ 83. Практикалық тапсырмалар: «Қате аңшысы» — debugging және logging

Әр тапсырмада: (1) қатені **қайталаңыз**, (2) **болжам** жазыңыз, (3) **тексеріңіз** (print / debugger / logging), (4) **түзетіңіз**. Түзетілген жолдың үстіне `# ТҮЗЕТУ: ...` деп не өзгергенін жазыңыз.

## Базалық деңгей

**1-тапсырма. Traceback-ті оқы.** Бағдарлама мына хабарды шығарды:

```text
Traceback (most recent call last):
  File "dukenn.py", line 18, in <module>
    esep = tusim_esepte(sebet)
  File "dukenn.py", line 11, in tusim_esepte
    zhalpy += tauar_bagasy(atau) * sany
  File "dukenn.py", line 5, in tauar_bagasy
    return bagalar[atau]
KeyError: 'алма'
```

Жауап беріңіз: а) қатенің түрі мен себебі қандай? ә) қате нақты қай функцияда, қай жолда болды? б) функциялардың шақырылу реті қандай? в) қатені түзетудің екі тәсілін ұсыныңыз.

**2-тапсырма. Синтаксистік қателер.** Төмендегі кодта 5 синтаксистік қате бар. Оларды тауып, түзетілген кодты жазыңыз (алдымен кодты іске қосып, Python не дейтінін оқыңыз).

```text
def synyp_ortasha(bagalar)
    if len(bagalar) == 0:
        return 0
    zhalpy = 0
    for b in bagalar:
        zhalpy += b
    return zhalpy / len(bagalar

bagalar = [5, 4, 5, 3]
print("Орташа баға: , synyp_ortasha(bagalar))
if synyp_ortasha(bagalar) = 5:
    print("Үздік!")
  print("Бітті")
```

**3-тапсырма. Логикалық қателер.** Бағдарлама қатесіз жұмыс істейді, бірақ нәтижесі дұрыс емес. Функция ең жоғары және ең төменгі температураны, сондай-ақ аязды (0 °C-тан төмен) күндер санын табуы керек. Күтілетін жауаптар:
```text
Макс: -2, Мин: -18, Аязды күндер: 7
Макс: 4, Мин: -3, Аязды күндер: 2
```
3 қатені табыңыз (кеңес: VS Code-та `for` ішіне тоқтау нүктесін қойып, айнымалыларды қадағалаңыз).

```python
def temp_esep(temp):
    maks = 0
    mini = 0
    ayazdy = 0
    for i in range(1, len(temp)):
        t = temp[i]
        if t > maks:
            maks = t
        if t < mini:
            mini = t
        if t <= 0:
            ayazdy += 1
    print(f"Макс: {maks}, Мин: {mini}, Аязды күндер: {ayazdy}")


temp_esep([-18, -12, -4, -15, -2, -9, -7])
temp_esep([4, -3, 0, 2, -1])
```

**4-тапсырма. Тип қатесі.** Себеттің жалпы сомасын есептейтін бағдарлама `TypeError` береді, ал «тез түзетуден» кейін қате сома немесе жаңа қате шығаруы мүмкін. `print(f"{x!r}")` көмегімен себебін тауып, түзетіңіз. Күтілетін жауап: `Жалпы: 2150 теңге`.

```python
sebet = [
    {"atau": "Нан", "baga": "250", "sany": 2},
    {"atau": "Сүт", "baga": "480", "sany": 1},
    {"atau": "Алма", "baga": "1170", "sany": "1"},
]

zhalpy = 0
for tauar in sebet:
    zhalpy += tauar["baga"] * tauar["sany"]
print(f"Жалпы: {zhalpy} теңге")
```

## Кеңейтілген деңгей

**5-тапсырма. Логтау қосу.** Төмендегі бағдарлама оқушылар баллдарының жолдарын өңдейді. Оған `logging` қосыңыз:
- `INFO` — өңдеу басталды/аяқталды, неше жол сәтті өңделді;
- `WARNING` — бұзылған жол (баллы сан емес немесе 0..100 аралығынан тыс) — жол нөмірімен;
- `ERROR` — жол форматы мүлде қате (үтір жоқ);
- логтар `ballar.log` файлына UTF-8 кодтауымен, уақытымен жазылсын. Бағдарлама қате жолда **тоқтамай**, келесі жолға өтсін.

```python
zholdar = [
    "Әлия,87",
    "Нұрлан,92",
    "Дана,тоқсан",
    "Ерлан 75",
    "Аружан,105",
    "Санжар,68",
]

ballar = {}
for zhol in zholdar:
    aty, ball = zhol.split(",")
    ballar[aty] = int(ball)
print(ballar)
```

**6-тапсырма. Екіге бөлу әдісі.** Функция 1-ден `n`-ге дейінгі жай сандарды тізім ретінде қайтаруы керек, бірақ жауабында қате бар: `jai_sandar(20)` → күтілетін `[2, 3, 5, 7, 11, 13, 17, 19]`. Қатені `print` немесе debugger арқылы тауып, түзетіңіз. Қай сан артық шығады және неге?

```python
def jai_ma(san):
    if san < 2:
        return False
    for d in range(2, int(san ** 0.5)):
        if san % d == 0:
            return False
    return True


def jai_sandar(n):
    return [s for s in range(1, n + 1) if jai_ma(s)]


print(jai_sandar(20))
```

**7-тапсырма. `assert` арқылы болжамдар.** 6-тапсырмадағы түзетілген `jai_ma` функциясына `assert`-тер жазыңыз: `jai_ma(2)`, `jai_ma(9)`, `jai_ma(25)`, `jai_ma(29)`, `jai_ma(1)` үшін күтілетін мәндер. Барлық `assert` өтсе — «Барлық тексеру өтті» деп шығарсын. (Бұл — келесі сабақтағы тесттердің алғашқы қадамы.)

## ★ Олимпиадалық / шығармашылық тапсырма

**8-тапсырма. Мәңгілік цикл.** Екілік іздеу (бинарный поиск) функциясы кейбір мәндерде **тоқтамайды**. Debugger арқылы `sol`, `on`, `orta` айнымалыларын бақылап, себебін табыңыз және түзетіңіз. Тексеру: `[1, 3, 5, 7, 9, 11]` ішінен 1, 7, 11 және 4 (жоқ) мәндерін іздеу.

```python
def ekilik_izdeu(tizim, izdeu):
    sol = 0
    on = len(tizim) - 1
    while sol <= on:
        orta = (sol + on) // 2
        if tizim[orta] == izdeu:
            return orta
        elif tizim[orta] < izdeu:
            sol = orta
        else:
            on = orta - 1
    return -1
```

**9-тапсырма. Лог талдағыш.** 5-тапсырмада жасалған `ballar.log` (немесе кез келген лог файл) бойынша әр деңгейдің (`INFO`, `WARNING`, `ERROR`) неше рет кездескенін санайтын және барлық `ERROR` жолдарын бөлек шығаратын `log_taldau.py` бағдарламасын жазыңыз. ★★ Нәтижені Matplotlib бағаналы диаграммасымен көрсетіңіз.

## Үй тапсырмасы

1. Өз талдау жобаңызға (81-сабақ) `logging` қосыңыз: деректі оқу, тазалау қадамдары (неше жол жойылды) `INFO`/`WARNING` деңгейімен `analysis.log` файлына жазылсын. `analysis.log`-ты `.gitignore`-ға қосыңыз.
2. Осы аптада кездескен бір қатеңізді «Қате күнделігіне» жазыңыз: белгісі → болжам → қалай таптым → қалай түзеттім.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.** а) `KeyError: 'алма'` — `bagalar` сөздігінде `'алма'` кілті жоқ (мүмкін, сөздікте «Алма» бас әріппен жазылған немесе мүлде жоқ). ә) `tauar_bagasy` функциясында, 5-жолда. б) `<module>` (18-жол) → `tusim_esepte` (11-жол) → `tauar_bagasy` (5-жол). в) 1) кілтті біркелкі ету: `bagalar[atau.capitalize()]`; 2) `bagalar.get(atau)` + жоқ болса логқа ескерту жазу; 3) `try/except KeyError`.

**2-тапсырма** — 5 қате: `def` соңында `:` жоқ; `len(bagalar` жақшасы жабылмаған; `"Орташа баға: ` тырнақшасы жабылмаған; `if ... = 5` орнына `==`; `print("Бітті")` шегінісі қате.
```python
def synyp_ortasha(bagalar):
    if len(bagalar) == 0:
        return 0
    zhalpy = 0
    for b in bagalar:
        zhalpy += b
    return zhalpy / len(bagalar)


bagalar = [5, 4, 5, 3]
print("Орташа баға:", synyp_ortasha(bagalar))
if synyp_ortasha(bagalar) == 5:
    print("Үздік!")
print("Бітті")
```

**3-тапсырма** — 3 қате: (1) `range(1, ...)` бірінші элементті өткізіп жібереді (бірінші жинақта −18, екіншіде 4 жоғалады); (2) `maks = 0`, `mini = 0` бастапқы мәндері — барлық температура теріс болса, макс қате 0 шығады (тізімнің бірінші элементінен бастау керек); (3) `t <= 0` — 0 °C аяз емес, `t < 0` болуы керек.
Қате код шығаратыны: `Макс: 0, Мин: -15, Аязды күндер: 6` және `Макс: 2, Мин: -3, Аязды күндер: 3`.
```python
def temp_esep(temp):
    maks = temp[0]
    mini = temp[0]
    ayazdy = 0
    for t in temp:
        if t > maks:
            maks = t
        if t < mini:
            mini = t
        if t < 0:
            ayazdy += 1
    print(f"Макс: {maks}, Мин: {mini}, Аязды күндер: {ayazdy}")


temp_esep([-18, -12, -4, -15, -2, -9, -7])
temp_esep([4, -3, 0, 2, -1])
```
Педагогке: бір ғана деректер жинағымен тексерсе, кейбір қателер бір-бірін «теңестіріп» жасырынып қалуы мүмкін — сондықтан екі түрлі жинақ берілген. Бұл келесі сабақтағы «шекаралық жағдайларды тестілеу» идеясына көпір.

**4-тапсырма.** `baga` — мәтін (`'250'`), сондықтан `"250" * 2` → `'250250'` (жолды қайталау!), ал `0 + '250250'` → `TypeError`. Оқушы тек `zhalpy = ""` деп «түзетсе», сома `'250250480...'` сияқты мағынасыз жол болады; ал тек `baga`-ны санға айналдырса, `1170 * "1"` → `'1'`-ді 1170 рет қайталап, қайтадан `TypeError`. Дұрыс түзету — екі өрісті де санға түрлендіру:
```python
sebet = [
    {"atau": "Нан", "baga": "250", "sany": 2},
    {"atau": "Сүт", "baga": "480", "sany": 1},
    {"atau": "Алма", "baga": "1170", "sany": "1"},
]

zhalpy = 0
for tauar in sebet:
    print(f"{tauar['baga']!r} {tauar['sany']!r}")     # түзету кезінде
    zhalpy += int(tauar["baga"]) * int(tauar["sany"])
print(f"Жалпы: {zhalpy} теңге")
```
Жауап: 250·2 + 480 + 1170 = 2150.

**5-тапсырма**
```python
import logging

logging.basicConfig(
    filename="ballar.log",
    encoding="utf-8",
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(message)s",
)

zholdar = [
    "Әлия,87",
    "Нұрлан,92",
    "Дана,тоқсан",
    "Ерлан 75",
    "Аружан,105",
    "Санжар,68",
]

logging.info("Өңдеу басталды: %d жол", len(zholdar))
ballar = {}
for nomir, zhol in enumerate(zholdar, start=1):
    if "," not in zhol:
        logging.error("%d-жол: формат қате (үтір жоқ): %r", nomir, zhol)
        continue
    aty, ball_str = zhol.split(",", 1)
    try:
        ball = int(ball_str)
    except ValueError:
        logging.warning("%d-жол: балл сан емес: %r", nomir, ball_str)
        continue
    if not 0 <= ball <= 100:
        logging.warning("%d-жол: балл аралықтан тыс: %d", nomir, ball)
        continue
    ballar[aty] = ball

logging.info("Өңдеу аяқталды: %d жол сәтті", len(ballar))
print(ballar)
```
Нәтиже: `{'Әлия': 87, 'Нұрлан': 92, 'Санжар': 68}`; логта 2 WARNING, 1 ERROR, 2 INFO.

**6-тапсырма.** `range(2, int(san ** 0.5))` — жоғарғы шекара кірмейді, сондықтан `san = 4` үшін `range(2, 2)` бос → 4 «жай сан» болып шығады; 9, 25 т.б. да солай. Нәтижеде 4 және 9 артық. Түзету — `+ 1`:
```python
def jai_ma(san):
    if san < 2:
        return False
    for d in range(2, int(san ** 0.5) + 1):
        if san % d == 0:
            return False
    return True


def jai_sandar(n):
    return [s for s in range(1, n + 1) if jai_ma(s)]


print(jai_sandar(20))
```

**7-тапсырма**
```python
def jai_ma(san):
    if san < 2:
        return False
    for d in range(2, int(san ** 0.5) + 1):
        if san % d == 0:
            return False
    return True


assert jai_ma(2) is True, "2 — жай сан"
assert jai_ma(9) is False, "9 = 3·3"
assert jai_ma(25) is False, "25 = 5·5"
assert jai_ma(29) is True, "29 — жай сан"
assert jai_ma(1) is False, "1 жай сан емес"
print("Барлық тексеру өтті")
```

**8-тапсырма.** `sol = orta` — `sol` пен `on` көрші болғанда (`sol=4, on=5`), `orta = 4`, ал `sol` қайтадан 4 болады → мәңгілік цикл (мысалы, 11-ді немесе 4-ті іздегенде). Түзету: `sol = orta + 1`.
```python
def ekilik_izdeu(tizim, izdeu):
    sol = 0
    on = len(tizim) - 1
    while sol <= on:
        orta = (sol + on) // 2
        if tizim[orta] == izdeu:
            return orta
        elif tizim[orta] < izdeu:
            sol = orta + 1
        else:
            on = orta - 1
    return -1


t = [1, 3, 5, 7, 9, 11]
print(ekilik_izdeu(t, 1), ekilik_izdeu(t, 7), ekilik_izdeu(t, 11), ekilik_izdeu(t, 4))
```
Нәтиже: `0 3 5 -1`.

**9-тапсырма**
```python
from collections import Counter

sanau = Counter()
qateler = []
try:
    with open("ballar.log", encoding="utf-8") as f:
        for zhol in f:
            for dengei in ("DEBUG", "INFO", "WARNING", "ERROR", "CRITICAL"):
                if f"[{dengei}]" in zhol:
                    sanau[dengei] += 1
                    if dengei == "ERROR":
                        qateler.append(zhol.strip())
except FileNotFoundError:
    print("Лог файл табылмады — алдымен 5-тапсырманы іске қосыңыз")

for dengei, san in sanau.most_common():
    print(f"{dengei:<9} {san}")
print("ERROR жолдары:")
for q in qateler:
    print(" ", q)
```

</details>
