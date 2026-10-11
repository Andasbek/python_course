# Сабақ 99. Жобаны тестілеу. Табылған қателерді түзету.

## Тақырыптар

1. Тестілеу не үшін керек және ол не емес?
2. Тестілеу пирамидасы
3. Тест жағдайларын қалай таңдаймыз?
4. Жобаның модульдік тесттері
5. API-ды тестілеу: `unittest.mock`
6. Қолмен тестілеу: тест-кейстер
7. Қате туралы есеп (bug report)
8. Қатені түзетудің 5 қадамы
9. Логтау: пайдаланушыда болған қатені білу
10. Жиі кездесетін қателер
11. Негізгі ұғымдар

## 1. Тестілеу не үшін керек және ол не емес?

**Тестілеу** — бағдарламаның талапқа сай жұмыс істейтінін тексеру және қателерді табу процесі.

> «Тестілеу қателердің бар екенін көрсете алады, бірақ олардың жоқ екенін ешқашан дәлелдей алмайды» — Эдсгер Дейкстра.

Сондықтан мақсат — «бағдарламаның жұмыс істейтінін көрсету» емес, **«оны бұзуға тырысу»**. Жақсы тестер — бағдарламаның ең әлсіз жерін іздейтін адам.

Қате неғұрлым ерте табылса, оны түзету соғұрлым арзан: әзірлеу кезінде тест тапқан қате бірнеше минутта түзетіледі, ал релизден кейін пайдаланушы тапқан қате — бірнеше сағатта (әрі беделге нұқсан келтіреді).

## 2. Тестілеу пирамидасы

```text
                 ▲
                / \        Қолмен / интерфейстік тесттер
               /   \       (аз, баяу, бірақ пайдаланушы көзімен)
              /─────\
             /       \     Интеграциялық тесттер
            /         \    (логика + нағыз ДҚ / файл бірге)
           /───────────\
          /             \  Модульдік (unit) тесттер
         /               \ (көп, жылдам, бір функцияны тексереді)
        ───────────────────
```

| Деңгей | Нені тексереді | Құралы | Мысал |
|---|---|---|---|
| Модульдік | бір функция/әдіс, оқшау | `unittest` | `balance()` дұрыс есептей ме |
| Интеграциялық | бірнеше модуль бірге | `unittest` + `:memory:` ДҚ | `Wallet` + `Database`: қосылған операция балансқа әсер ете ме |
| Қолмен (жүйелік) | бүкіл бағдарлама, пайдаланушы ретінде | тест-кейстер кестесі | терезеден шығыс қосу → диаграмма жаңарады |

Неге пирамида? Модульдік тест бір секундтан аз уақытта жүздеген рет қайталанады; қолмен тест — әр жолы 10 минут. Сондықтан негізгі жүктеме — автоматты тесттерде, ал қолмен тестілеу — оларға қосымша.

## 3. Тест жағдайларын қалай таңдаймыз?

### 3.1. Эквиваленттік кластар
Барлық мүмкін кірісті «бағдарлама бірдей әрекет ететін» топтарға бөлеміз және әр топтан **бір** өкіл аламыз.

Мысал: «сома» өрісі (1-ден 10 000 000-ға дейінгі бүтін сан):

| Класс | Өкіл | Күтілген нәтиже |
|---|---|---|
| Дұрыс сан | `800` | сақталады |
| Нөл / теріс | `-5` | қате: «Сома оң болуы керек» |
| Тым үлкен | `99999999` | қате: «Сома тым үлкен» |
| Сан емес | `"абв"` | қате: «Сан енгізіңіз» |
| Бос | `""` | қате: «Сан енгізіңіз» |
| Бос орыны бар сан | `" 800 "` | сақталады (бос орындар алынады) |

### 3.2. Шекаралық мәндер
Қателер көбінесе **шекарада** болады (`<` орнына `<=`). Сондықтан шекараның екі жағын да тексереміз:

```text
     0      1                        10 000 000   10 000 001
  ───●──────●───────────────────────────●──────────●───>
   қате   дұрыс                        дұрыс      қате
```

Тест мәндері: `0`, `1`, `10000000`, `10000001`.

Басқа шекаралық жағдайлар: бос тізім, бір элемент, айдың соңғы күні, 29 ақпан, жыл ауысуы, бірдей ұпайлар.

## 4. Жобаның модульдік тесттері

Тексерілетін функция (`qalta/logic.py`-ден):

```python
MAX_AMOUNT = 10_000_000


def parse_amount(text):
    """Мәтінді сомаға айналдырады. Қате болса ValueError (қазақша хабарламамен)."""
    cleaned = text.strip().replace(" ", "")
    if not cleaned.isdigit():
        raise ValueError("Соманы сан түрінде енгізіңіз")
    value = int(cleaned)
    if value <= 0:
        raise ValueError("Сома оң болуы керек")
    if value > MAX_AMOUNT:
        raise ValueError("Сома тым үлкен")
    return value


for sample in ["800", " 1 500 ", "0", "абв", "10000001"]:
    try:
        print(repr(sample), "->", parse_amount(sample))
    except ValueError as err:
        print(repr(sample), "-> қате:", err)
```

Нәтижесі:
```text
'800' -> 800
' 1 500 ' -> 1500
'0' -> қате: Сома оң болуы керек
'абв' -> қате: Соманы сан түрінде енгізіңіз
'10000001' -> қате: Сома тым үлкен
```

Енді осы функцияға және `Wallet`-ке тесттер — `tests/test_logic.py`:

```python
"""tests/test_logic.py — «Қалта ақшасы» логикасының тесттері."""
import unittest

from qalta.database import Database
from qalta.logic import Wallet, parse_amount, MAX_AMOUNT


class ParseAmountTest(unittest.TestCase):
    """parse_amount: эквиваленттік кластар мен шекаралық мәндер."""

    def test_valid_numbers(self):
        self.assertEqual(parse_amount("800"), 800)
        self.assertEqual(parse_amount(" 1 500 "), 1500)

    def test_boundaries(self):
        self.assertEqual(parse_amount("1"), 1)
        self.assertEqual(parse_amount(str(MAX_AMOUNT)), MAX_AMOUNT)
        with self.assertRaises(ValueError):
            parse_amount("0")
        with self.assertRaises(ValueError):
            parse_amount(str(MAX_AMOUNT + 1))

    def test_not_a_number(self):
        for bad in ["", "абв", "12.5", "-5", "1e3"]:
            with self.subTest(text=bad):
                with self.assertRaises(ValueError):
                    parse_amount(bad)


class WalletTest(unittest.TestCase):
    """Wallet + Database бірге (интеграциялық), уақытша ДҚ-мен."""

    def setUp(self):
        # Әр тест алдында жаңа, бос ДҚ — тесттер бір-біріне әсер етпейді
        self.db = Database(":memory:")
        self.wallet = Wallet(self.db)
        cats = {c["title"]: c["id"] for c in self.db.categories()}
        self.parents, self.food = cats["Ата-ана"], cats["Тамақ"]

    def tearDown(self):
        self.db.close()

    def test_empty_balance_is_zero(self):
        self.assertEqual(self.wallet.balance(), 0)

    def test_income_minus_expense(self):
        self.wallet.add_operation(self.parents, 3000, "2027-05-01")
        self.wallet.add_operation(self.food, 800, "2027-05-02")
        self.assertEqual(self.wallet.balance(), 2200)

    def test_balance_by_month(self):
        self.wallet.add_operation(self.food, 500, "2027-04-30")
        self.wallet.add_operation(self.food, 700, "2027-05-01")
        self.assertEqual(self.wallet.balance("2027-04"), -500)
        self.assertEqual(self.wallet.balance("2027-05"), -700)

    def test_delete_restores_balance(self):
        op_id = self.wallet.add_operation(self.food, 800, "2027-05-02")
        self.assertTrue(self.wallet.delete_operation(op_id))
        self.assertEqual(self.wallet.balance(), 0)

    def test_delete_missing_returns_false(self):
        self.assertFalse(self.wallet.delete_operation(9999))

    def test_negative_amount_rejected(self):
        with self.assertRaises(ValueError):
            self.wallet.add_operation(self.food, -100, "2027-05-02")
        self.assertEqual(self.wallet.operations(), [])


if __name__ == "__main__":
    unittest.main()
```

Іске қосу (жоба түбірінен):
```bash
python -m unittest discover -s tests -v
```

Нәтижесі:
```text
test_boundaries (test_logic.ParseAmountTest.test_boundaries) ... ok
test_not_a_number (test_logic.ParseAmountTest.test_not_a_number) ... ok
test_valid_numbers (test_logic.ParseAmountTest.test_valid_numbers) ... ok
test_balance_by_month (test_logic.WalletTest.test_balance_by_month) ... ok
...
----------------------------------------------------------------------
Ran 9 tests in 0.012s

OK
```

Жаңа тәсілдер:
- **`subTest`** — бір тестте көп мәнді тексеру; қайсысы құлағаны жеке көрсетіледі.
- **`:memory:` ДҚ** — нағыз `qalta.db` файлына тиіспейміз, әр тест таза ДҚ-дан басталады.
- **`tearDown`** — әр тесттен кейін тазалау.
- Тест атауы **не тексерілетінін** айтады: `test_delete_restores_balance`.

> `tests/` папкасында бос `__init__.py` болса, `discover` оны пакет ретінде дұрыс табады.

## 5. API-ды тестілеу: `unittest.mock`

Тестте нағыз интернетке шықпаймыз: ол баяу, тұрақсыз және API шектеулері бар. Оның орнына `requests.get`-ті **жалған объектімен** (mock) ауыстырамыз:

```python
import unittest
from unittest import mock

import requests


def get_kzt_rate():
    """API-дан бағамды алады; қате болса None."""
    try:
        response = requests.get("https://open.er-api.com/v6/latest/USD", timeout=5)
        response.raise_for_status()
        return response.json()["rates"]["KZT"]
    except (requests.RequestException, KeyError, ValueError):
        return None


class RateTest(unittest.TestCase):
    @mock.patch("requests.get")
    def test_success(self, fake_get):
        fake_get.return_value.json.return_value = {"rates": {"KZT": 500.0}}
        self.assertEqual(get_kzt_rate(), 500.0)

    @mock.patch("requests.get", side_effect=requests.ConnectionError)
    def test_no_internet(self, fake_get):
        self.assertIsNone(get_kzt_rate())

    @mock.patch("requests.get")
    def test_broken_answer(self, fake_get):
        fake_get.return_value.json.return_value = {"error": "bad"}
        self.assertIsNone(get_kzt_rate())


if __name__ == "__main__":
    unittest.main()
```

`mock.patch("requests.get")` тест уақытында `requests.get`-ті жалған функциямен ауыстырады; `side_effect` — шақырғанда қате көтеру. Осылайша «интернет жоқ» жағдайын интернет өшірмей тексереміз.

## 6. Қолмен тестілеу: тест-кейстер

Интерфейсті автоматты тексеру қиын, сондықтан **тест-кейстер кестесі** бойынша қолмен тексереміз. Тест-кейс — қайталанатын, нақты қадамдары бар тексеру.

| ID | Тексеру | Алғышарт | Қадамдар | Күтілген нәтиже | Нақты нәтиже | Күйі |
|---|---|---|---|---|---|---|
| ТК-01 | Шығыс қосу | бос ДҚ | 800, «Тамақ», Сақтау | кестеде 1 жол, баланс −800 | сәйкес | Өтті |
| ТК-02 | Бос сома | — | өрісті бос қалдырып Сақтау | ескерту, жазба жоқ | сәйкес | Өтті |
| ТК-03 | Ұзын түсініктеме | — | 1000 таңба | сақталады, кесте бұзылмайды | кесте ені бұзылды | **Өтпеді** → #21 |
| ТК-04 | Жою | 1 жазба бар | таңдау, Жою, Иә | жазба жоқ, баланс 0 | сәйкес | Өтті |
| ТК-05 | Интернетсіз | Wi-Fi өшірулі | бағдарламаны ашу | «Бағам қолжетімсіз», қалғаны жұмыс істейді | сәйкес | Өтті |

**Ең тиімді тәсіл — айқас тестілеу:** бағдарламаңызды сіздің тест-кейстеріңіз бойынша басқа адам тексереді — автор өз бағдарламасының әлсіз жерлерін еріксіз айналып өтеді.

## 7. Қате туралы есеп (bug report)

Қате табылғанда оны **басқа адам қайталай алатындай** жазамыз (мысалы, GitHub Issue түрінде):

```text
Атауы: Ұзын түсініктемеде кесте ені бұзылады
Қадамдар:
  1. Бағдарламаны ашу
  2. Сома 100, түсініктеме — 1000 таңбалы мәтін
  3. «Сақтау» басу
Күтілді: кестеде мәтін қысқартылып көрінеді
Нақты: кесте терезеден шығып кетеді, диаграмма көрінбейді
Орта: Windows 10, Python 3.11, коммит a1b2c3d
Маңыздылығы: маңызды
```

Қатенің өмірлік циклі: `Жаңа → Жұмыста → Түзетілді → Тексерілді → Жабық`. Түзеткен адам емес, **тексерген адам** жабады — немесе кем дегенде тест-кейсті қайта өтеді.

## 8. Қатені түзетудің 5 қадамы

```text
1. Қайталау      Қатені тұрақты шығаратын қадамдарды табу
2. Тест жазу     Қатені көрсететін тест жазу — ол ҚЫЗЫЛ (құлайды)
3. Орнын табу    print / logging / дебаггер; traceback-тың соңғы жолы
4. Түзету        ең аз өзгеріспен
5. Тексеру       жаңа тест ЖАСЫЛ + барлық ескі тесттер жасыл
```

2-қадамдағы тест **регрессиялық тест** деп аталады: ол бұл қатенің болашақта қайта пайда болмауын (регрессия) бақылайды.

Мысал. Қате есебі: «Ақпан айының есебінде 28-інен кейінгі күндер саналмайды» (кодта `month_end = 28` деп жазылған).

```python
import calendar
import datetime


def days_in_month(year, month):
    """Айдағы күн саны (кібісе жылды ескереді)."""
    return calendar.monthrange(year, month)[1]


def month_range(year, month):
    """Айдың бірінші және соңғы күні."""
    first = datetime.date(year, month, 1)
    last = datetime.date(year, month, days_in_month(year, month))
    return first, last


# Регрессиялық тексерулер
assert month_range(2028, 2)[1].day == 29       # 2028 — кібісе жыл
assert month_range(2027, 2)[1].day == 28
assert month_range(2027, 12)[1] == datetime.date(2027, 12, 31)
print(month_range(2028, 2))
```

Нәтижесі:
```text
(datetime.date(2028, 2, 1), datetime.date(2028, 2, 29))
```

## 9. Логтау: пайдаланушыда болған қатені білу

Пайдаланушы «бір нәрсе бұзылды» десе, не болғанын қалай білеміз? Күтпеген қателерді `logging` модулі арқылы файлға жазамыз:

```python
import logging

logging.basicConfig(
    filename="qalta.log",
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s: %(message)s",
    encoding="utf-8",
)
log = logging.getLogger("qalta")


def safe_save(save_function, *args):
    """Сақтауды орындайды; күтпеген қатені логқа жазады."""
    try:
        return save_function(*args)
    except ValueError:
        raise                                   # пайдаланушы қатесі — интерфейс көрсетеді
    except Exception:
        log.exception("Сақтау кезінде күтпеген қате, args=%s", args)
        return None
```

`log.exception` traceback-ты толық жазады. `qalta.log`-ты `.gitignore`-ға қосыңыз.

## Жиі кездесетін қателер

**1. Тест ештеңені тексермейді.**
```python
def test_balance(self):
    self.wallet.balance()          # assert жоқ — тест әрқашан «өтеді»
```
Дұрысы: `self.assertEqual(self.wallet.balance(), 0)`.

**2. Тесттер бір-біріне тәуелді.** 1-тест жазба қосады, 2-тест «жазба бар» деп күтеді. Тесттердің орындалу реті кепілдік берілмейді. Дұрысы: әр тест өз деректерін `setUp`-та дайындайды.

**3. Нағыз ДҚ файлында тестілеу** — тесттер пайдаланушының деректерін бұзады. Дұрысы: `Database(":memory:")`.

**4. Бөлшек сандарды `assertEqual`-мен салыстыру.** `0.1 + 0.2 == 0.3` → `False`. Дұрысы: `self.assertAlmostEqual(a, b, places=2)`.

**5. Қатені түзетіп, тесттерді қайта іске қоспау.** Түзету басқа жерді бұзуы мүмкін. Әр түзетуден кейін: `python -m unittest discover -s tests`.

**6. «Менде жұмыс істейді».** Басқа компьютерде тексеріңіз: таза папкаға `git clone`, жаңа `venv`, `pip install -r requirements.txt`.

## Негізгі ұғымдар
| Ұғым | Түсіндірме |
|---|---|
| Модульдік тест | бір функцияны оқшау тексеретін автоматты тест |
| Интеграциялық тест | бірнеше модульді бірге тексеретін тест |
| Эквиваленттік класс | бағдарлама бірдей әрекет ететін кіріс мәндер тобы |
| Шекаралық мән | кластың шекарасындағы мән (0, 1, max, max+1) |
| Тест-кейс | қадамдары мен күтілетін нәтижесі бар қолмен тексеру сипаттамасы |
| Mock | тестте нағыз объектінің (API, файл) орнына қолданылатын жалған объект |
| Қате туралы есеп | қатені қайталауға жеткілікті сипаттама |
| Регрессиялық тест | түзетілген қатенің қайта пайда болмауын тексеретін тест |
| `subTest` | бір тест ішінде бірнеше жағдайды жеке тексеру |

## Ескерту

- `logging.basicConfig()`-тің `encoding` параметрі Python 3.9-да пайда болды; ертерек нұсқаларда кодтауды `logging.FileHandler(..., encoding="utf-8")` арқылы береді.
- `unittest.main()` пен `discover` тек атауы `test`-тен басталатын әдістерді және тек `test*.py` үлгісіне сай файлдарды табады.
- `mock.patch` объектіні **қолданылатын жерінде** ауыстырады: модульде `from requests import get` деп жазылса, `"requests.get"` емес, `"qalta.currency.get"` патчталуы керек.
- `unittest`-ке танымал балама — бөгде `pytest` кітапханасы: тесттер `assert` бар қарапайым функциялар түрінде жазылады, ал бар `unittest`-тесттерді де ол іске қоса алады.
- Кодтың тесттермен қамтылуын `coverage` пакеті өлшейді (`coverage run -m unittest discover` → `coverage report`).
- Құжаттама: https://docs.python.org/3/library/unittest.html және https://docs.python.org/3/library/unittest.mock.html
