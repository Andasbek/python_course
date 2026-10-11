# Сабақ 84. Бағдарламаны тестілеу негіздері. unittest модулі. Тест жазу.

## Тақырыптар

1. Тестілеу дегеніміз не
2. Қандай жағдайларды тексеру керек
3. `unittest` модулі
4. Негізгі `assert` әдістері
5. `setUp` — әр тест алдындағы дайындық
6. Тестілеуге ыңғайлы код
7. Жиі кездесетін қателер
8. Негізгі ұғымдар

## 1. Тестілеу дегеніміз не

**Тестілеу** (тестирование, testing) — бағдарламаның нақты нәтижесін **күтілетін** нәтижемен салыстыру арқылы оның дұрыстығын тексеру.

```text
 Енгізу  ──►  [ Функция ]  ──►  Нақты нәтиже
                                    │
                                    ▼  салыстыру
                              Күтілетін мән  ──►  ӨТТІ / ҚҰЛАДЫ
```

> «Тестілеу қателердің бар екенін көрсете алады, бірақ олардың жоқ екенін ешқашан дәлелдей алмайды» (Э. Дейкстра). Тест — 100% кепілдік емес, бірақ қатені ерте табудың ең сенімді жолы.

### Қолмен және автоматты тестілеу

| | Қолмен | Автоматты |
|---|---|---|
| Кім тексереді | Адам (бағдарламаны іске қосып, енгізіп қарайды) | Тест коды |
| Жылдамдығы | Баяу | Өте жылдам |
| Қайталау | Жалықтырады, қате жіберіледі | Шексіз, бірдей |
| Қашан тиімді | Интерфейс, ыңғайлылық, дизайн | Логика, есептеулер, деректер |

### Тест түрлері (тест пирамидасы)

```text
               ▲   аз, баяу, қымбат
              / \
             / Қо\      Қолмен / қабылдау тесті (UI, пайдаланушы көзімен)
            /─────\
           / Интег-\    Интеграциялық тест (модульдер бірге: GUI + SQLite)
          / рациялық\
         /───────────\
        /  Модульдік  \  Модульдік (unit) тест — бір функция/әдіс
       /_______________\ көп, жылдам, арзан
```

Пирамиданың негізі — **модульдік тесттер** (юнит-тесттер); олар ең көп жазылады.

## 2. Қандай жағдайларды тексеру керек

Бір функцияға бірнеше **тест жағдайы** (тест-кейс) жазамыз:

| Түрі | Мысал (`bagaga_audaru(ball)`: 100 балл → 5 балдық баға) |
|---|---|
| Қалыпты жағдай | 92 → 5, 70 → 4 |
| Шекаралық жағдай | 85 → 5, 84 → 4, 65 → 4, 64 → 3, 0, 100 |
| Ерекше/бос енгізу | Бос тізім, бос жол, `None` |
| Қате енгізу | 105, −1 → `ValueError` |

**Шекаралық мәндер** — қателердің «сүйікті мекені». `>` мен `>=` шатасуы, `range`-тің соңғы мәні кірмеуі дәл осы жерде көрінеді.

### AAA үлгісі

Әр тест үш бөліктен тұрады:
1. **Arrange (Дайында)** — енгізу деректерін дайындау;
2. **Act (Орында)** — тексерілетін функцияны шақыру;
3. **Assert (Тексер)** — нәтижені күтілгенмен салыстыру.

## 3. `unittest` модулі

`unittest` — Python-ға кіріктірілген (орнатуды қажет етпейді) тестілеу құралы.

### Тексерілетін модуль: `bagalau.py`

```python
"""Оқушы баллдарын өңдейтін функциялар."""


def ortasha(ballar):
    """Баллдардың орташа мәнін қайтарады. Бос тізім үшін 0."""
    if not ballar:
        return 0
    return sum(ballar) / len(ballar)


def bagaga_audaru(ball):
    """100 балдық жүйені 5 балдық бағаға аударады."""
    if not 0 <= ball <= 100:
        raise ValueError("Балл 0..100 аралығында болуы керек")
    if ball >= 85:
        return 5
    if ball >= 65:
        return 4
    if ball >= 40:
        return 3
    return 2
```

### Тест файлы: `test_bagalau.py`

```python
import unittest

from bagalau import ortasha, bagaga_audaru


class TestOrtasha(unittest.TestCase):
    """ortasha() функциясының тесттері."""

    def test_qalypty_tizim(self):
        ballar = [80, 90, 100]                 # Arrange
        natije = ortasha(ballar)               # Act
        self.assertEqual(natije, 90)           # Assert

    def test_bos_tizim(self):
        self.assertEqual(ortasha([]), 0)

    def test_bolshek_natije(self):
        self.assertAlmostEqual(ortasha([1, 2]), 1.5)


class TestBagagaAudaru(unittest.TestCase):
    """bagaga_audaru() функциясының тесттері."""

    def test_bes(self):
        self.assertEqual(bagaga_audaru(92), 5)

    def test_shekaralar(self):
        self.assertEqual(bagaga_audaru(85), 5)
        self.assertEqual(bagaga_audaru(84), 4)
        self.assertEqual(bagaga_audaru(65), 4)
        self.assertEqual(bagaga_audaru(64), 3)
        self.assertEqual(bagaga_audaru(40), 3)
        self.assertEqual(bagaga_audaru(39), 2)

    def test_qate_ball(self):
        with self.assertRaises(ValueError):
            bagaga_audaru(105)
        with self.assertRaises(ValueError):
            bagaga_audaru(-1)


if __name__ == "__main__":
    unittest.main()
```

**Ережелер:**
- тест файлының аты `test_` деп басталады;
- тест класы `unittest.TestCase`-тен мұраланады (мұрагерлік);
- әр тест — `test_` деп басталатын **әдіс**; басқа атаулы әдістер тест ретінде іске қосылмайды;
- әр тест бір нәрсені тексереді және басқа тесттерге тәуелсіз.

### Іске қосу

```bash
python -m unittest -v
```

Нәтижесі:
```text
test_bes (test_bagalau.TestBagagaAudaru.test_bes) ... ok
test_qate_ball (test_bagalau.TestBagagaAudaru.test_qate_ball) ... ok
test_shekaralar (test_bagalau.TestBagagaAudaru.test_shekaralar) ... ok
test_bolshek_natije (test_bagalau.TestOrtasha.test_bolshek_natije) ... ok
test_bos_tizim (test_bagalau.TestOrtasha.test_bos_tizim) ... ok
test_qalypty_tizim (test_bagalau.TestOrtasha.test_qalypty_tizim) ... ok

----------------------------------------------------------------------
Ran 6 tests in 0.001s

OK
```

Басқа тәсілдер: `python test_bagalau.py` (файлдың соңындағы `unittest.main()` арқылы), `python -m unittest test_bagalau` (бір файл), `python -m unittest test_bagalau.TestOrtasha.test_bos_tizim` (бір тест). `-v` (verbose) — әр тесттің атын көрсетеді. Параметрсіз `python -m unittest` ағымдағы папкадағы барлық `test*.py` файлдарын өзі табады (discovery).

### Тест құлағанда

Енді `bagalau.py`-да әдейі қате жасайық: `if ball >= 85:` орнына `if ball > 85:`.

Нәтижесі:
```text
..F...
======================================================================
FAIL: test_shekaralar (test_bagalau.TestBagagaAudaru.test_shekaralar)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test_bagalau.py", line 26, in test_shekaralar
    self.assertEqual(bagaga_audaru(85), 5)
AssertionError: 4 != 5

----------------------------------------------------------------------
Ran 6 tests in 0.001s

FAILED (failures=1)
```

Әр нүкте `.` — өткен тест, `F` — құлаған тест (failure), `E` — тест ішінде күтпеген қате (error, мысалы, `NameError`). Хабар нақты айтады: 85 балл үшін 4 қайтты, ал 5 күтілген. Тек шекаралық тест құлады — қалыпты жағдай (92) қатені «көрмеді»!

## 4. Негізгі `assert` әдістері

| Әдіс | Тексереді |
|---|---|
| `assertEqual(a, b)` | `a == b` |
| `assertNotEqual(a, b)` | `a != b` |
| `assertTrue(x)` / `assertFalse(x)` | `bool(x)` ақиқат / жалған |
| `assertIs(a, b)` / `assertIsNone(x)` | `a is b` / `x is None` |
| `assertIn(a, b)` / `assertNotIn(a, b)` | `a in b` |
| `assertIsInstance(a, Klass)` | `a` берілген класс объектісі ме |
| `assertAlmostEqual(a, b, places=7)` | Бөлшек сандардың жуық теңдігі |
| `assertGreater(a, b)` / `assertLess(a, b)` | `a > b` / `a < b` |
| `assertRaises(Qate)` | Блок ішінде `Qate` шығуы керек |

**Неге `assertAlmostEqual`?** Компьютерде `0.1 + 0.2 == 0.3` → `False` (нәтиже `0.30000000000000004`). Бөлшек сандарды `assertEqual`-мен салыстыру — тұзақ.

## 5. `setUp` — әр тест алдындағы дайындық

Бірнеше тестке бірдей объект керек болса, оны `setUp` әдісінде жасаймыз. Ол **әр** тесттің алдында қайта шақырылады — сондықтан тесттер бір-біріне әсер етпейді. Класты тестілеу мысалы:

```python
import unittest


class Sebet:
    """Дүкен себеті."""

    def __init__(self):
        self.tauarlar = {}

    def qosu(self, atau, baga, sany=1):
        if baga <= 0 or sany <= 0:
            raise ValueError("Баға мен сан оң болуы керек")
        self.tauarlar[atau] = self.tauarlar.get(atau, 0) + baga * sany

    def zhalpy(self):
        return sum(self.tauarlar.values())


class TestSebet(unittest.TestCase):

    def setUp(self):
        self.sebet = Sebet()            # әр тест жаңа, бос себеттен басталады
        self.sebet.qosu("Нан", 250, 2)

    def test_qosu(self):
        self.assertIn("Нан", self.sebet.tauarlar)
        self.assertEqual(self.sebet.zhalpy(), 500)

    def test_qaita_qosu(self):
        self.sebet.qosu("Нан", 250)
        self.assertEqual(self.sebet.zhalpy(), 750)

    def test_teris_baga(self):
        with self.assertRaises(ValueError):
            self.sebet.qosu("Сүт", -480)


if __name__ == "__main__":
    unittest.main()
```

`tearDown` — әр тесттен **кейін** орындалады (мысалы, уақытша файлды жою, деректер қорын жабу).

## 6. Тестілеуге ыңғайлы код

Функция тестіленуі оңай болуы үшін:
- **есептеу логикасын** `input()`/`print()`-тен бөлек функцияға шығарыңыз (функция мәнді `return` арқылы қайтарсын);
- бір функция — бір міндет;
- GUI (Tkinter) немесе деректер қорынан логиканы бөлек модульге шығарыңыз.

```python
# Тестілеуге қиын:
def esepte():
    a = int(input("Сан: "))
    print(a * a)


# Тестілеуге оңай:
def kvadrat(a):
    return a * a
```

**TDD (Test-Driven Development, тест арқылы әзірлеу)** — алдымен тест жазып (ол құлайды, «қызыл»), содан кейін оны өткізетін кодты жазу («жасыл»), сосын кодты жақсарту («рефакторинг»). Бұл тәсіл кодты талап бойынша жазуға үйретеді.

Ескерту: кәсіби жобаларда `unittest`-тен басқа `pytest` кітапханасы да кең қолданылады (`pip install pytest`); ол `unittest` тесттерін де іске қоса алады.

## Жиі кездесетін қателер

**1. Тест әдісінің аты `test_`-сіз.**
```python
class TestOrtasha(unittest.TestCase):
    def tekseru_bos(self):              # іске қосылмайды!
        self.assertEqual(ortasha([]), 0)
```
Дұрысы: `def test_bos(self):`.

**2. `self`-ті ұмыту:** `assertEqual(a, b)` → `NameError`. Дұрысы: `self.assertEqual(a, b)`.

**3. `assertRaises`-ке функцияны шақырып беру.**
```python
self.assertRaises(ValueError, bagaga_audaru(105))   # қате тест ішінде шығып кетеді
```
Дұрысы:
```python
with self.assertRaises(ValueError):
    bagaga_audaru(105)
```
(немесе `self.assertRaises(ValueError, bagaga_audaru, 105)` — функция мен аргумент бөлек).

**4. Тесттің дұрыс жауабын кодтан көшіру.** `assertEqual(ortasha([3, 4]), ortasha([3, 4]))` — әрдайым өтеді, ештеңе тексермейді. Күтілетін мәнді **қолмен есептеңіз**.

**5. Тесттердің бір-біріне тәуелділігі.** Бір тест екіншісі жасаған деректі пайдаланса, тесттер реті өзгергенде құлайды. Әр тест өз дайындығын `setUp`-та жасасын.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Тестілеу | Нақты нәтижені күтілетінмен салыстыру арқылы тексеру |
| Модульдік тест (unit test) | Бір функцияны/әдісті бөлек тексеретін тест |
| Тест жағдайы (test case) | Енгізу + күтілетін нәтиже жұбы |
| Шекаралық мән | Шарттың ауысатын нүктесіндегі мән (84/85) |
| `unittest.TestCase` | Тест класының ата-класы |
| `assertEqual`, `assertRaises` | Тексеру әдістері |
| `setUp` / `tearDown` | Әр тесттің алдындағы / кейінгі дайындық |
| OK / FAIL / ERROR | Өтті / тексеру сәтсіз / тест ішінде қате |
| TDD | Алдымен тест, сосын код |

## Ескерту

- Тест файлдары импортталатын модуль болуы керек: атауы `test-bagalau.py` немесе `test bagalau.py` емес, `test_bagalau.py`, әйтпесе `python -m unittest` оларды таппайды. Ішкі қалталарда іздеу үшін оларда `__init__.py` болуы немесе `python -m unittest discover -s tests` деп нақты іске қосу керек.
- `self.subTest(...)` бір тесттің ішінде циклмен көп мәнді тексеріп, тек біріншісін емес, **барлық** сәтсіз жағдайларды көруге мүмкіндік береді: `for b, o in [(85, 5), (84, 4)]: with self.subTest(ball=b): ...`.
- `unittest`-те тесттер файлдағы ретпен емес, әдіс атаулары бойынша әліпби ретімен іске қосылады; сондықтан тесттер бір-біріне тәуелді болмауы керек.
- Сыртқы тәуелділіктерді (желі, уақыт, `input()`) алмастыру үшін кіріктірілген `unittest.mock` модулі қолданылады, мысалы `@patch("builtins.input", return_value="5")`.
- `pytest` тесттерді кәдімгі функциялар мен кәдімгі `assert` арқылы жазуға мүмкіндік береді; `pytest` командасы `unittest` тесттерін де іске қосады.
- Құжаттама: [unittest](https://docs.python.org/3/library/unittest.html), [unittest.mock](https://docs.python.org/3/library/unittest.mock.html).
