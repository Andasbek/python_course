# Сабақ 90. Практикалық тапсырмалар: жоба архитектурасын жобалау

## Базалық деңгей

**1-тапсырма. Қай қабат?** Әр функцияны/әрекетті қабатқа жатқызыңыз: **И** — интерфейс, **Л** — логика, **Д** — деректер.
1. «Сақтау» батырмасын жасау
2. Шығындардың айлық қосындысын есептеу
3. `INSERT INTO shygyndar ...` орындау
4. Қате енгізуде `messagebox.showwarning` көрсету
5. Жеңілдік пайызын қолдану
6. Open-Meteo API-дан JSON алу
7. Ойыншының соқтығысын тексеру (Pygame `colliderect`)
8. Рекордтарды `records.json`-ға жазу
9. Нәтиже графигін терезеге орналастыру
10. Пароль хэшін тексеру
11. CSV файлдан сұрақтарды оқу
12. Кестедегі таңдалған жолды бөлектеу

**2-тапсырма. «Зат есім — етістік» талдауы.** «Отбасы бюджеті» жобасының user story-лері:
- US-1. Мен ата-ана ретінде шығынды сомасымен, санатымен және күнімен енгізгім келеді, себебі ақшаның қайда кететінін білгім келеді.
- US-2. Мен ата-ана ретінде әр санатқа айлық лимит қойғым келеді, себебі артық жұмсамауым керек.
- US-3. Мен ата-ана ретінде лимиттен асқан санаттар туралы ескерту көргім келеді, себебі уақытында тоқтағым келеді.
- US-4. Мен ата-ана ретінде айлар бойынша шығындар графигін көргім келеді, себебі бюджетті жоспарлағым келеді.

Зат есімдер мен етістіктерді тізіп, кластарды (атрибуттарымен), әдістерді және «класс емес» шешімдерді (атрибут, функция) анықтаңыз. Нәтижені кестеге жазыңыз.

**3-тапсырма. CRC карточкалары.** Өз жобаңыздың 3–5 негізгі класына CRC карточкасын толтырыңыз (стикер немесе `docs/architecture.md` ішінде):
```text
Класс: ...
Жауапкершілігі:          Серіктестері:
- ...                    - ...
```

**4-тапсырма. Қабаттарды бөл.** Төмендегі кодта логика, интерфейс және деректер араласып кеткен. Оны үш бөлікке бөліңіз: `Shygyn` класы және есептеу функциясы (логика), `JsonStorage` класы (деректер), консольдық интерфейс функциясы. Логика бөлігі `input`, `print`, `json`, `open` қолданбауы керек.

```python
import json


def bastau():
    try:
        with open("budget.json", encoding="utf-8") as f:
            shygyndar = json.load(f)
    except FileNotFoundError:
        shygyndar = []
    soma = int(input("Сома: "))
    sanat = input("Санат: ")
    if soma <= 0:
        print("Сома оң болуы керек!")
        return
    shygyndar.append({"soma": soma, "sanat": sanat})
    with open("budget.json", "w", encoding="utf-8") as f:
        json.dump(shygyndar, f, ensure_ascii=False)
    zhalpy = 0
    for s in shygyndar:
        if s["sanat"] == sanat:
            zhalpy += s["soma"]
    print(f"«{sanat}» санаты бойынша барлығы: {zhalpy} теңге")


bastau()
```

## Кеңейтілген деңгей

**5-тапсырма. Архитектуралық құжат.** Өз жобаңызға `docs/architecture.md` жазыңыз:
1. Модульдер кестесі (файл — қабат — міндеті — кімді шақырады);
2. Класс диаграммасы (мәтіндік сызба, Mermaid немесе draw.io суреті) — кемінде 3 класс және байланыстары;
3. Әр класқа 1–2 сөйлемдік сипаттама;
4. Бір Must user story-дің «кодтағы жолы» (қай батырма → қай әдіс → қай класс → деректер);
5. Сыртқы кітапханалар.

**6-тапсырма. Жоба қаңқасы.** Репозиторийде папкалар құрылымын (теориядағы үлгі бойынша) жасаңыз:
- барлық кластар `app/` ішінде, әр әдістің атауы, параметрлері және docstring-і бар; әзірге жүзеге асырылмаған әдістерде `raise NotImplementedError`;
- `main.py` қатесіз іске қосылады (кемінде бір логикалық класты қолданып, қысқа демонстрация көрсетеді);
- `tests/test_models.py` ішінде кемінде 1 өтетін тест;
- `.gitignore` (`venv/`, `__pycache__/`, `*.db`, `.env`, `*.log`);
- коммит: `git commit -m "Жоба қаңқасы: архитектура"`.

**7-тапсырма. Mermaid диаграммасы.** Класс диаграммаңызды README-ге Mermaid форматында қосып, GitHub-та сурет болып көрінетінін тексеріңіз.

## ★ Олимпиадалық / шығармашылық тапсырма

**8-тапсырма. Тәуелділік тексергіш.** `ast` арқылы жобаның барлық `.py` файлдарындағы импорттарды тауып, модульдер тәуелділігін шығаратын `deps.py` бағдарламасын жазыңыз. Ол: (а) «модуль → импорттайтын модульдер» тізімін шығарсын; (ә) **қабат ережесін** тексерсін: `models.py` пен `storage.py` ішінде `tkinter`/`pygame` импорты болса — ескерту; (б) ★★ циклдік импортты тапсын.

**9-тапсырма. Ауыстырылатын қойма (полиморфизм).** Бірдей интерфейсі (`saqtau(zhazba)`, `barlygy()`) бар екі класс жазыңыз: `JsonStorage` және `SqliteStorage`. Логика класы (`Budget`) қойманы конструктор арқылы алсын (`Budget(storage)`) және қойма түрін білмесін. Бір `unittest` тест класын екі қоймамен де іске қосып, екеуінде де өтетінін көрсетіңіз. Бұл — «интерфейс арқылы тәуелділік» идеясы.

## Үй тапсырмасы

1. 5–6-тапсырмаларды аяқтап, GitHub-қа жүктеңіз.
2. Келесі сабаққа (деректер моделі): жобаңызда **қандай деректер** сақталатынын тізіп келіңіз — әр класс үшін қандай атрибуттар файлда/деректер қорында сақталуы керек?

## Бағалау критерийлері

| Көрсеткіш | Ұпай | Толық ұпай үшін |
|---|---|---|
| Қабаттарға бөлу | 0–5 | Интерфейс, логика, деректер бөлек модульдерде; тәуелділік бағыты дұрыс |
| Кластар және жауапкершіліктер | 0–6 | Кластар ТТ-дан шыққан, әрқайсысының бір жауапкершілігі бар, «құдай-класс» жоқ |
| Класс диаграммасы | 0–4 | ≥ 3 класс, атрибуттар, әдістер, байланыстар дұрыс белгіленген |
| Жоба қаңқасы | 0–6 | Папкалар құрылымы, docstring-тер, `main.py` іске қосылады, ≥ 1 тест өтеді |
| Құжат және GitHub | 0–4 | `docs/architecture.md`, коммит, `.gitignore` |
| **Барлығы** | **25** | |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма:** 1 — И; 2 — Л; 3 — Д; 4 — И; 5 — Л; 6 — Д (сыртқы дерек көзі); 7 — Л (ойын ережесі; Pygame-тің `Rect` объектісі қолданылса да, бұл логика); 8 — Д; 9 — И; 10 — Л (немесе қауіпсіздік қызметі); 11 — Д; 12 — И.

**2-тапсырма — үлгі:**

| Зат есім | Шешім |
|---|---|
| Шығын | Класс `Shygyn` (soma, sanat, kuni) |
| Санат | Атрибут `str` (MVP); кеңейтілген нұсқада — `Sanat` класы (atau, limit) |
| Лимит | `Budget.limitter` сөздігінің мәні `{sanat: soma}` |
| Ескерту | `Budget.limitten_asqandar()` әдісінің нәтижесі (тізім); көрсету — интерфейсте |
| График | `stats.ailyq_grafik()` функциясы |
| Бюджет (жасырын зат есім: барлық шығындар мен лимиттер) | Класс `Budget` |
| Ата-ана | Рөл, класс емес (бір пайдаланушы) |

Етістіктер → әдістер: енгізу → `Budget.qosu(shygyn)`; лимит қою → `Budget.limit_qoiu(sanat, soma)`; ескерту көру → `Budget.limitten_asqandar(ai)`; график көру → `stats.ailyq_grafik(shygyndar)`; (жасырын) сақтау/жүктеу → `Storage.saqtau()`, `Storage.zhukteu()`.

**4-тапсырма**
```python
import json


# ---------- ЛОГИКА (models.py) ----------
class Shygyn:
    """Бір шығын жазбасы."""

    def __init__(self, soma, sanat):
        if soma <= 0:
            raise ValueError("Сома оң болуы керек")
        self.soma = soma
        self.sanat = sanat

    def to_dict(self):
        """Сақтауға арналған сөздік."""
        return {"soma": self.soma, "sanat": self.sanat}


def sanat_qosyndysy(shygyndar, sanat):
    """Берілген санат бойынша шығындар қосындысы."""
    return sum(s.soma for s in shygyndar if s.sanat == sanat)


# ---------- ДЕРЕКТЕР (storage.py) ----------
class JsonStorage:
    """Шығындарды JSON файлында сақтайды."""

    def __init__(self, path="budget.json"):
        self.path = path

    def zhukteu(self):
        """Файлдан Shygyn объектілерінің тізімін оқиды."""
        try:
            with open(self.path, encoding="utf-8") as f:
                return [Shygyn(d["soma"], d["sanat"]) for d in json.load(f)]
        except FileNotFoundError:
            return []

    def saqtau(self, shygyndar):
        """Тізімді файлға жазады."""
        with open(self.path, "w", encoding="utf-8") as f:
            json.dump([s.to_dict() for s in shygyndar], f, ensure_ascii=False)


# ---------- ИНТЕРФЕЙС (cli.py / main.py) ----------
def bastau(storage):
    """Консольдық интерфейс: енгізу, сақтау, нәтижені көрсету."""
    shygyndar = storage.zhukteu()
    try:
        shygyn = Shygyn(int(input("Сома: ")), input("Санат: "))
    except ValueError as e:
        print("Қате:", e)
        return
    shygyndar.append(shygyn)
    storage.saqtau(shygyndar)
    zhalpy = sanat_qosyndysy(shygyndar, shygyn.sanat)
    print(f"«{shygyn.sanat}» санаты бойынша барлығы: {zhalpy} теңге")


if __name__ == "__main__":
    bastau(JsonStorage())
```
Енді `Shygyn` мен `sanat_qosyndysy` файлсыз және енгізусіз тестіленеді, ал `JsonStorage`-ті кейін `SqliteStorage`-ке ауыстыруға болады.

**8-тапсырма**
```python
import ast
import os
import sys

TYIM = {"models.py", "storage.py"}            # бұл модульдер интерфейсті білмеуі керек
UI_KITAPHANALAR = {"tkinter", "pygame"}


def importtar(path):
    """Файлдағы импортталған модульдер атауларының жиынын қайтарады."""
    with open(path, encoding="utf-8") as f:
        agash = ast.parse(f.read())
    ataular = set()
    for t in ast.walk(agash):
        if isinstance(t, ast.Import):
            ataular.update(a.name.split(".")[0] for a in t.names)
        elif isinstance(t, ast.ImportFrom) and t.module:
            ataular.add(t.module)
    return ataular


def tauyeldilik_grafy(papka):
    """{модуль: импорттары} сөздігін қайтарады."""
    graf = {}
    for tubir, papkalar, faildar in os.walk(papka):
        papkalar[:] = [p for p in papkalar if p not in ("venv", ".git", "__pycache__")]
        for fail in faildar:
            if fail.endswith(".py"):
                graf[os.path.join(tubir, fail)] = importtar(os.path.join(tubir, fail))
    return graf


def tekseru(papka):
    graf = tauyeldilik_grafy(papka)
    for modul, imp in sorted(graf.items()):
        print(f"{modul} → {', '.join(sorted(imp)) or '—'}")
        if os.path.basename(modul) in TYIM:
            for k in imp & UI_KITAPHANALAR:
                print(f"  Ескерту: {modul} ішінде «{k}» импорты — қабат ережесі бұзылды")


tekseru(sys.argv[1] if len(sys.argv) > 1 else ".")
```
Нәтижесі: әр модульдің импорттары тізімі; `models.py` немесе `storage.py` ішінде `tkinter`/`pygame` табылса — ескерту.

**9-тапсырма**
```python
import json
import os
import sqlite3
import unittest


class JsonStorage:
    """Жазбаларды JSON файлында сақтайды."""

    def __init__(self, path):
        self.path = path

    def saqtau(self, zhazba):
        barlygy = self.barlygy()
        barlygy.append(zhazba)
        with open(self.path, "w", encoding="utf-8") as f:
            json.dump(barlygy, f, ensure_ascii=False)

    def barlygy(self):
        if not os.path.exists(self.path):
            return []
        with open(self.path, encoding="utf-8") as f:
            return json.load(f)


class SqliteStorage:
    """Жазбаларды SQLite-та сақтайды."""

    def __init__(self, path):
        self.conn = sqlite3.connect(path)
        self.conn.execute("CREATE TABLE IF NOT EXISTS z (soma INTEGER, sanat TEXT)")

    def saqtau(self, zhazba):
        self.conn.execute("INSERT INTO z VALUES (?, ?)", (zhazba["soma"], zhazba["sanat"]))
        self.conn.commit()

    def barlygy(self):
        rows = self.conn.execute("SELECT soma, sanat FROM z").fetchall()
        return [{"soma": s, "sanat": n} for s, n in rows]


class Budget:
    """Бюджет логикасы: қойманың түрін білмейді."""

    def __init__(self, storage):
        self.storage = storage

    def qosu(self, soma, sanat):
        if soma <= 0:
            raise ValueError("Сома оң болуы керек")
        self.storage.saqtau({"soma": soma, "sanat": sanat})

    def zhalpy(self):
        return sum(z["soma"] for z in self.storage.barlygy())


class BudgetTestMixin:
    """Кез келген қоймаға ортақ тесттер."""

    def make_storage(self):
        raise NotImplementedError

    def setUp(self):
        self.budget = Budget(self.make_storage())

    def test_qosu_zhane_zhalpy(self):
        self.budget.qosu(500, "Тамақ")
        self.budget.qosu(300, "Көлік")
        self.assertEqual(self.budget.zhalpy(), 800)

    def test_teris_soma(self):
        with self.assertRaises(ValueError):
            self.budget.qosu(-1, "Тамақ")


class TestJson(BudgetTestMixin, unittest.TestCase):
    PATH = "test_budget.json"

    def make_storage(self):
        if os.path.exists(self.PATH):
            os.remove(self.PATH)
        return JsonStorage(self.PATH)

    def tearDown(self):
        if os.path.exists(self.PATH):
            os.remove(self.PATH)


class TestSqlite(BudgetTestMixin, unittest.TestCase):
    def make_storage(self):
        return SqliteStorage(":memory:")


if __name__ == "__main__":
    unittest.main(verbosity=2)
```
Нәтижесі: 4 тест (әр қоймаға 2) — OK.

</details>
