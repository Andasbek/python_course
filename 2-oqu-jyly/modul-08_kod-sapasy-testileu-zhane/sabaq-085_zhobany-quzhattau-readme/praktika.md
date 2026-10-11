# Сабақ 85. Практикалық тапсырмалар: жобаны құжаттау

## Базалық деңгей

**1-тапсырма. Markdown-ға жылыту.** `markdown_test.md` файлын жасап, мына мәтінді Markdown элементтерімен ресімдеңіз: 1-деңгейлі тақырып, екі 2-деңгейлі тақырып, нүктелі және нөмірленген тізім, бір қалың сөз, бір `код` сөзі, сілтеме, код блогы (`bash`), 3 жолдық кесте. VS Code-та `Ctrl+Shift+V` арқылы нәтижені тексеріңіз.

```text
Валюта түрлендіргіш
Бағдарлама теңгені доллар мен еуроға аударады. Бағам open.er-api.com сайтынан алынады.
Мүмкіндіктер: бағамды алу, соманы аудару, тарихты сақтау.
Орнату: репозиторийді клондау, venv жасау, pip install -r requirements.txt орындау.
Іске қосу командасы: python converter.py
Валюталар: USD — АҚШ доллары, EUR — еуро, RUB — ресей рублі.
```

**2-тапсырма. Нашар README-ді түзет.** Құрбыңыздың README-і төменде. Ондағы кемінде 6 кемшілікті тізіп жазыңыз, содан кейін README-ді толық қайта жазыңыз (жоба — 32-сабақтағы «To-Do List» Tkinter қосымшасы, деректер `tasks.json` файлында сақталады).

```text
# project1

бұл менің жобам. app2_final_NEW.py ашыңыз. жұмыс істемесе маған жазыңыз.
керек: python, tkinter, json, pandas
![](C:\Users\Erlan\Desktop\screen.png)
api_key = "sk-12345abcdef"
```

**3-тапсырма. Docstring жазу.** Төмендегі функцияларға толық docstring (сипаттама, `Args`, `Returns`, қажет болса `Raises`) жазыңыз. `help(funktsiya)` арқылы тексеріңіз.

```python
def bmi(salmaq, boiy):
    if boiy <= 0:
        raise ValueError("Бой оң болуы керек")
    return round(salmaq / boiy ** 2, 1)


def bmi_sanaty(index):
    if index < 18.5:
        return "Салмақ жетіспеушілігі"
    if index < 25:
        return "Қалыпты"
    if index < 30:
        return "Артық салмақ"
    return "Семіздік"


class Oqushy:
    def __init__(self, aty, synyp):
        self.aty = aty
        self.synyp = synyp
        self.bagalar = []

    def baga_qosu(self, baga):
        if baga not in (2, 3, 4, 5):
            raise ValueError("Баға 2..5 болуы керек")
        self.bagalar.append(baga)

    def ortasha(self):
        return sum(self.bagalar) / len(self.bagalar) if self.bagalar else 0
```

**4-тапсырма. `requirements.txt`.** Жаңа папкада:
1. venv жасап, белсендіріңіз;
2. `pip install pandas matplotlib requests` орындаңыз;
3. `pip freeze > requirements.txt` жасап, файлды ашып қараңыз — неше пакет жазылды? Неге 3 емес?
4. Екінші venv (`venv2`) жасап, `pip install -r requirements.txt` арқылы орнатып, `python -c "import pandas, matplotlib, requests; print('OK')"` арқылы тексеріңіз.

## Кеңейтілген деңгей

**5-тапсырма. Өз жобаңыздың README-і.** Жыл ішіндегі ең жақсы жобаңызды таңдаңыз (ОБП, GUI, ойын, SQLite, API немесе талдау). Теориядағы үлгі мен чек-парақ бойынша толық README.md жазыңыз: сипаттама, скриншот, мүмкіндіктер, талаптар, орнату, іске қосу, жоба құрылымы, тестілеу, автор. `requirements.txt` қосыңыз.

**6-тапсырма. Пайдаланушы нұсқаулығы.** Сол жобаға `docs/user_guide.md` жазыңыз: кіріспе, іске қосу, кемінде 3 сценарий («Қалай ... ?») нөмірленген қадамдармен және скриншоттармен, кемінде 3 сұрақтық FAQ. Оқырман — сіздің 10 жастағы туысыңыз немесе ата-анаңыз деп елестетіңіз.

**7-тапсырма. «Бөтен көз» тесті.** Құрбыңызбен репозиторий сілтемелерін алмасыңыз. Тек README бойынша (авторға сұрақ қоймай!) оның жобасын клондап, орнатып, іске қосыңыз. Кесте толтырыңыз:

| Сұрақ | Иә/Жоқ | Ескерту |
|---|---|---|
| Жобаның не істейтінін бірінші абзацтан түсіндім | | |
| Орнату командалары өзгеріссіз жұмыс істеді | | |
| Қандай файлды іске қосу керегін бірден таптым | | |
| Скриншот/сурет көрінеді | | |
| Нұсқаулық бойынша бір сценарийді орындай алдым | | |
| Ең қиын болған сәт: | | |

## ★ Олимпиадалық / шығармашылық тапсырма

**8-тапсырма. Автоматты құжат генераторы.** `inspect` модулін пайдаланып, кез келген модульдің барлық функциялары мен кластарын (әдістерімен) тауып, олардың docstring-терінен `API.md` Markdown құжатын жасайтын `docgen.py` бағдарламасын жазыңыз. Docstring-і жоқ функцияларды «⚠ құжатталмаған» деп белгілесін. 3-тапсырмадағы модульге қолданыңыз.

**9-тапсырма. README тексергіш.** README.md файлын оқып, міндетті бөлімдердің (`Орнату`, `Іске қосу`, `Талаптар`, `Авторы`) бар-жоғын, кемінде бір сурет (`![`) және бір код блогы бар-жоғын, абсолютті жолдар (`C:\`, `/Users/`) жоқ екенін тексеретін `readme_check.py` бағдарламасын жазыңыз. Нәтиже — чек-парақ түрінде (✓/✗) және жалпы балл.

## Үй тапсырмасы

1. 5–6-тапсырмаларды аяқтап, GitHub-қа жүктеңіз (`git commit -m "README, пайдаланушы нұсқаулығы және requirements.txt"`).
2. 7-тапсырмадағы құрбыңыздың пікірі бойынша README-ді жетілдіріңіз.
3. GitHub профиліңіздің бас бетіне (репозиторий `<логин>/<логин>` → README.md) өзіңіз туралы 5–7 жолдық «портфолио-визитка» жазыңыз: кімсіз, қандай жобалар жасадыңыз (сілтемелерімен).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма** — үлгі:
````markdown
# Валюта түрлендіргіш

Бағдарлама **теңгені** доллар мен еуроға аударады. Бағам [open.er-api.com](https://open.er-api.com) сайтынан алынады.

## Мүмкіндіктер
- бағамды алу;
- соманы аудару;
- тарихты сақтау.

## Орнату
1. Репозиторийді клондаңыз.
2. venv жасаңыз.
3. Тәуелділіктерді орнатыңыз:

```bash
pip install -r requirements.txt
python converter.py
```

| Код | Валюта |
|---|---|
| USD | АҚШ доллары |
| EUR | Еуро |
| RUB | Ресей рублі |

Іске қосу командасы: `python converter.py`
````

**2-тапсырма — кемшіліктер:** (1) мағынасыз атау `project1`; (2) жобаның не істейтіні айтылмаған; (3) файл аты түсініксіз (`app2_final_NEW.py`) — файлды `main.py` деп өзгерту керек; (4) орнату/іске қосу командалары жоқ; (5) `tkinter`, `json` — стандартты кітапхана, `requirements`-ке жазылмайды; `pandas` жобада қолданылмаса — артық; (6) суреттің абсолютті Windows жолы — GitHub-та көрінбейді; (7) **API кілті ашық жарияланған** — ең қауіпті қате, кілтті дереу ауыстыру (отозвать) керек; (8) байланыс «маған жазыңыз» — кімге? Автор аты жоқ; (9) құрылым жоқ (тақырыптар, тізімдер). Қайта жазылған README теориядағы үлгі бойынша.

**3-тапсырма**
```python
def bmi(salmaq, boiy):
    """Дене салмағының индексін (ДСИ, BMI) есептейді.

    Args:
        salmaq (float): дене салмағы, кг.
        boiy (float): бойы, метр (мысалы, 1.65).

    Returns:
        float: ДСИ мәні, бір ондық таңбаға дейін дөңгелектенген.

    Raises:
        ValueError: бой 0-ге тең немесе теріс болса.
    """
    if boiy <= 0:
        raise ValueError("Бой оң болуы керек")
    return round(salmaq / boiy ** 2, 1)


def bmi_sanaty(index):
    """ДСИ мәні бойынша ДДҰ санатын қайтарады.

    Args:
        index (float): ДСИ мәні.

    Returns:
        str: «Салмақ жетіспеушілігі», «Қалыпты», «Артық салмақ» немесе «Семіздік».
    """
    if index < 18.5:
        return "Салмақ жетіспеушілігі"
    if index < 25:
        return "Қалыпты"
    if index < 30:
        return "Артық салмақ"
    return "Семіздік"


class Oqushy:
    """Оқушы және оның бағалары.

    Attributes:
        aty (str): оқушының аты.
        synyp (str): сыныбы, мысалы «9А».
        bagalar (list[int]): алған бағалары (2..5).
    """

    def __init__(self, aty, synyp):
        """Бағалары бос жаңа оқушы жасайды."""
        self.aty = aty
        self.synyp = synyp
        self.bagalar = []

    def baga_qosu(self, baga):
        """Оқушыға баға қосады.

        Args:
            baga (int): 2, 3, 4 немесе 5.

        Raises:
            ValueError: баға 2..5 аралығында болмаса.
        """
        if baga not in (2, 3, 4, 5):
            raise ValueError("Баға 2..5 болуы керек")
        self.bagalar.append(baga)

    def ortasha(self):
        """Орташа бағаны қайтарады; баға жоқ болса — 0."""
        return sum(self.bagalar) / len(self.bagalar) if self.bagalar else 0


help(bmi)
```

**4-тапсырма.** Жауап: `pip freeze` шамамен 15–20 пакет көрсетеді (numpy, python-dateutil, pytz, tzdata, contourpy, cycler, fonttools, kiwisolver, packaging, pillow, pyparsing, certifi, charset-normalizer, idna, urllib3, six т.б.), себебі pandas, matplotlib және requests өздері басқа пакеттерге тәуелді — `pip` оларды да орнатады.

**5–7-тапсырмалар** — теориядағы 10 пункттік чек-парақ бойынша бағаланады (әр пункт — 10%).

**8-тапсырма**
```python
import inspect


def modul_qujaty(modul):
    """Модульдің функциялары мен кластарынан Markdown мәтін құрастырады."""
    zholdar = [f"# `{modul.__name__}` модулінің құжаттамасы", ""]
    if modul.__doc__:
        zholdar += [inspect.cleandoc(modul.__doc__), ""]
    for atau, obj in inspect.getmembers(modul):
        if atau.startswith("_"):
            continue
        if inspect.isfunction(obj) and obj.__module__ == modul.__name__:
            zholdar += sipattama(f"## Функция `{atau}{inspect.signature(obj)}`", obj)
        elif inspect.isclass(obj) and obj.__module__ == modul.__name__:
            zholdar += sipattama(f"## Класс `{atau}`", obj)
            for a_atau, adis in inspect.getmembers(obj, inspect.isfunction):
                if not a_atau.startswith("_") or a_atau == "__init__":
                    zholdar += sipattama(f"### Әдіс `{a_atau}{inspect.signature(adis)}`", adis)
    return "\n".join(zholdar)


def sipattama(taqyryp, obj):
    """Бір объектінің тақырыбы мен docstring-ін қайтарады."""
    doc = inspect.getdoc(obj)
    return [taqyryp, "", doc if doc else "⚠ құжатталмаған", ""]


if __name__ == "__main__":
    import json            # мысал ретінде стандартты модуль; өз модуліңізді импорттаңыз
    with open("API.md", "w", encoding="utf-8") as f:
        f.write(modul_qujaty(json))
    print("API.md жасалды")
```

**9-тапсырма**
```python
import re


def readme_tekseru(path="README.md"):
    """README.md файлын міндетті элементтер бойынша тексереді."""
    try:
        with open(path, encoding="utf-8") as f:
            matin = f.read()
    except FileNotFoundError:
        print("README.md табылмады")
        return 0

    tekseruler = {
        "«Талаптар» бөлімі": re.search(r"^##.*Талап", matin, re.M),
        "«Орнату» бөлімі": re.search(r"^##.*Орнату", matin, re.M),
        "«Іске қосу» бөлімі": re.search(r"^##.*Іске қосу", matin, re.M),
        "«Авторы» бөлімі": re.search(r"^##.*Автор", matin, re.M),
        "Кемінде бір сурет": "![" in matin,
        "Кемінде бір код блогы": "`" * 3 in matin,     # үш кері тырнақша
        "Абсолютті жолдар жоқ": not re.search(r"[A-Za-z]:\\|/Users/|/home/", matin),
    }
    ball = 0
    for atau, natije in tekseruler.items():
        belgi = "✓" if natije else "✗"
        ball += 1 if natije else 0
        print(f"{belgi} {atau}")
    print(f"Нәтиже: {ball}/{len(tekseruler)}")
    return ball


readme_tekseru()
```

</details>
