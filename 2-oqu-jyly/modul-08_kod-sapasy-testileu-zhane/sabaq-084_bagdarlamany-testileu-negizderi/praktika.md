# Сабақ 84. Практикалық тапсырмалар: unittest арқылы тест жазу

Жұмыс құрылымы: әр тапсырмаға бөлек папка; тексерілетін код — `<модуль>.py`, тесттер — `test_<модуль>.py`. Іске қосу: `python -m unittest -v`. Әр функцияға кемінде бір қалыпты, бір шекаралық және (қажет болса) бір қате енгізу тестін жазыңыз.

## Базалық деңгей

**1-тапсырма. Математикалық функциялар.** `matematika.py` модулін жасап, оған тесттер жазыңыз (әр функцияға кемінде 3 тест):

```python
def zhup_pa(n):
    """n жұп болса True қайтарады."""
    return n % 2 == 0


def faktorial(n):
    """n! мәнін қайтарады. Теріс n үшін ValueError."""
    if n < 0:
        raise ValueError("Теріс санның факториалы жоқ")
    natije = 1
    for i in range(2, n + 1):
        natije *= i
    return natije


def en_ulken(tizim):
    """Тізімнің ең үлкен элементін қайтарады. Бос тізім үшін None."""
    if not tizim:
        return None
    maks = tizim[0]
    for x in tizim:
        if x > maks:
            maks = x
    return maks
```

Кеңес: `faktorial(0)` неге тең? `en_ulken([-5, -2, -9])` ше? Бұлар — маңызды шекаралық жағдайлар.

**2-тапсырма. Палиндром.** `palindrom(soz)` функциясын жазыңыз: сөз/сөйлем екі жақтан бірдей оқылса `True` (әріп регистрі мен бос орындар ескерілмейді). Тесттер: `"Қазақ"` → `True`, `"Python"` → `False`, `"А роза упала на лапу Азора"` → `True`, `""` → `True`, `"ab"` → `False`.

**3-тапсырма. Тесттер қатені табады.** Мұғалім жеңілдікпен бағаны есептейтін функция жазды. Ереже: сома **5000 теңгеден бастап** (5000 кіреді) — 10% жеңілдік, **20 000 теңгеден бастап** — 15%, одан аз болса — жеңілдік жоқ. Теріс сома — `ValueError`. Тесттер жазып, функциядағы **екі** қатені табыңыз, сосын түзетіңіз.

```python
def zhenildikpen(soma):
    """Жеңілдікті ескеріп, төленетін соманы қайтарады."""
    if soma > 20000:
        return soma * 0.85
    if soma > 5000:
        return soma * 0.9
    return soma
```

**4-тапсырма. Класты тестілеу.** Теориядағы `Sebet` класына тағы 3 тест қосыңыз: бос себеттің сомасы 0; бірнеше түрлі тауардың сомасы; `sany=0` болса `ValueError`. Барлығы `setUp` арқылы.

## Кеңейтілген деңгей

**5-тапсырма. ЖСН тексеру (TDD).** Қазақстан азаматының жеке сәйкестендіру нөмірі (ЖСН, ИИН) 12 цифрдан тұрады; соңғы цифры — бақылау цифры. Алгоритм:
1. Алғашқы 11 цифрды 1, 2, 3, ..., 11 салмақтарына көбейтіп қосамыз, 11-ге бөлгендегі қалдықты аламыз.
2. Қалдық 10-нан кіші болса — ол бақылау цифры.
3. Қалдық 10 болса — салмақтарды 3, 4, 5, 6, 7, 8, 9, 10, 11, 1, 2 деп алып қайта есептейміз. Қалдық тағы 10 болса — мұндай ЖСН жарамсыз.

**Алдымен** `test_zhsn.py` тесттерін жазыңыз (олар құлайды — «қызыл»), **содан кейін** `zhsn_durys_pa(zhsn)` функциясын жазыңыз («жасыл»). Тест деректері (ойдан шығарылған нөмірлер):
- `"050315501230"` → `True`;
- `"081212600455"` → `True`;
- `"050315501301"` → `True` (бақылау цифры екінші салмақтар арқылы есептеледі);
- `"050315501231"` → `False` (бақылау цифры қате);
- `"05031550123"` (11 цифр), `"05031550123A"` (әріп бар) → `False`.

**6-тапсырма. Файлмен жұмыс тесті.** `ortasha_fail(path)` функциясы әр жолында бір балл жазылған мәтіндік файлды оқып, орташа баллды қайтарады (бос жолдарды өткізіп жібереді). Тестте `setUp` уақытша файл жасасын, `tearDown` оны жойсын (`os.remove`). Файл жоқ болса — `FileNotFoundError` күтілетінін тексеріңіз.

**7-тапсырма. Талдау жобасына тест.** Өз талдау жобаңыздағы (81-сабақ) `clean_data(df)` функциясына тест жазыңыз: кішкентай DataFrame жасап (дубликаты, бос мәні, қате мәні бар), тазалаудан кейін жол саны мен мәндердің дұрыстығын тексеріңіз.

## ★ Олимпиадалық / шығармашылық тапсырма

**8-тапсырма. «Мутанттарды аулау».** 1-тапсырмадағы `faktorial` функциясына әдейі шағын өзгерістер («мутанттар») енгізіңіз: `range(2, n + 1)` → `range(2, n)`; `natije = 1` → `natije = 0`; `n < 0` → `n <= 0`. Сіздің тесттеріңіз әр мутантты «өлтіре» ме (кемінде бір тест құлай ма)? Өлмеген мутант болса — жаңа тест қосыңыз. Бұл — тесттер сапасын тексерудің кәсіби әдісі (mutation testing).

**9-тапсырма. Өз тест жүргізгішің.** `unittest`-сіз, қарапайым өз тест жүргізгішіңізді жазыңыз: модульдегі `test_` деп басталатын барлық функцияларды тауып (`globals()`), әрқайсысын іске қосып, `AssertionError` болса «FAIL», басқа қате болса «ERROR», әйтпесе «OK» деп шығарсын және соңында қорытынды санын көрсетсін.

## Үй тапсырмасы

1. Өз жобаңыздың (ОБП, GUI, SQLite немесе талдау) кемінде 2 функциясына/әдісіне `test_*.py` файлында барлығы кемінде 6 тест жазыңыз.
2. `python -m unittest -v` нәтижесінің скриншотын README-ге қосыңыз (немесе мәтінін көшіріңіз).
3. GitHub-қа жүктеңіз: `git commit -m "Модульдік тесттер қосылды"`.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

Ыңғайлы болуы үшін шешімдерде код пен тесттер бір блокта берілген; оқушылар оларды екі файлға бөледі (`from matematika import ...`).

**1-тапсырма**
```python
import unittest


def zhup_pa(n):
    return n % 2 == 0


def faktorial(n):
    if n < 0:
        raise ValueError("Теріс санның факториалы жоқ")
    natije = 1
    for i in range(2, n + 1):
        natije *= i
    return natije


def en_ulken(tizim):
    if not tizim:
        return None
    maks = tizim[0]
    for x in tizim:
        if x > maks:
            maks = x
    return maks


class TestZhupPa(unittest.TestCase):
    def test_zhup(self):
        self.assertTrue(zhup_pa(4))

    def test_taq(self):
        self.assertFalse(zhup_pa(7))

    def test_nol_zhane_teris(self):
        self.assertTrue(zhup_pa(0))
        self.assertTrue(zhup_pa(-2))


class TestFaktorial(unittest.TestCase):
    def test_qalypty(self):
        self.assertEqual(faktorial(5), 120)

    def test_nol_zhane_bir(self):
        self.assertEqual(faktorial(0), 1)
        self.assertEqual(faktorial(1), 1)

    def test_teris(self):
        with self.assertRaises(ValueError):
            faktorial(-3)


class TestEnUlken(unittest.TestCase):
    def test_qalypty(self):
        self.assertEqual(en_ulken([3, 9, 2]), 9)

    def test_barlygy_teris(self):
        self.assertEqual(en_ulken([-5, -2, -9]), -2)

    def test_bos(self):
        self.assertIsNone(en_ulken([]))

    def test_bir_element(self):
        self.assertEqual(en_ulken([7]), 7)


if __name__ == "__main__":
    unittest.main()
```

**2-тапсырма**
```python
import unittest


def palindrom(soz):
    """Регистр мен бос орындарды ескермей, палиндромды тексереді."""
    taza = soz.replace(" ", "").lower()
    return taza == taza[::-1]


class TestPalindrom(unittest.TestCase):
    def test_soz(self):
        self.assertTrue(palindrom("Қазақ"))

    def test_palindrom_emes(self):
        self.assertFalse(palindrom("Python"))

    def test_soilem(self):
        self.assertTrue(palindrom("А роза упала на лапу Азора"))

    def test_bos(self):
        self.assertTrue(palindrom(""))

    def test_eki_aripi(self):
        self.assertFalse(palindrom("ab"))


if __name__ == "__main__":
    unittest.main()
```

**3-тапсырма.** Қателер: (1) `>` орнына `>=` болуы керек — 5000 және 20 000 сомаларында жеңілдік берілмейді; (2) теріс сома тексерілмейді. Табатын тесттер — шекаралық мәндер және теріс сома:
```python
import unittest


def zhenildikpen(soma):
    """Жеңілдікті ескеріп, төленетін соманы қайтарады."""
    if soma < 0:
        raise ValueError("Сома теріс болмайды")
    if soma >= 20000:
        return soma * 0.85
    if soma >= 5000:
        return soma * 0.9
    return soma


class TestZhenildik(unittest.TestCase):
    def test_zhenildik_zhoq(self):
        self.assertEqual(zhenildikpen(4999), 4999)

    def test_shekara_5000(self):
        self.assertAlmostEqual(zhenildikpen(5000), 4500)

    def test_shekara_20000(self):
        self.assertAlmostEqual(zhenildikpen(20000), 17000)

    def test_ulken(self):
        self.assertAlmostEqual(zhenildikpen(30000), 25500)

    def test_teris(self):
        with self.assertRaises(ValueError):
            zhenildikpen(-100)


if __name__ == "__main__":
    unittest.main()
```

**4-тапсырма**
```python
import unittest


class Sebet:
    def __init__(self):
        self.tauarlar = {}

    def qosu(self, atau, baga, sany=1):
        if baga <= 0 or sany <= 0:
            raise ValueError("Баға мен сан оң болуы керек")
        self.tauarlar[atau] = self.tauarlar.get(atau, 0) + baga * sany

    def zhalpy(self):
        return sum(self.tauarlar.values())


class TestSebetQosymsha(unittest.TestCase):
    def setUp(self):
        self.sebet = Sebet()

    def test_bos_sebet(self):
        self.assertEqual(self.sebet.zhalpy(), 0)

    def test_birneshe_tauar(self):
        self.sebet.qosu("Нан", 250, 2)
        self.sebet.qosu("Сүт", 480)
        self.sebet.qosu("Алма", 900, 3)
        self.assertEqual(self.sebet.zhalpy(), 3680)

    def test_nol_sany(self):
        with self.assertRaises(ValueError):
            self.sebet.qosu("Нан", 250, 0)


if __name__ == "__main__":
    unittest.main()
```

**5-тапсырма**
```python
import unittest


def zhsn_durys_pa(zhsn):
    """ЖСН-нің форматы мен бақылау цифрын тексереді."""
    if len(zhsn) != 12 or not zhsn.isdigit():
        return False
    cifrlar = [int(c) for c in zhsn]
    salmaq1 = list(range(1, 12))
    salmaq2 = [3, 4, 5, 6, 7, 8, 9, 10, 11, 1, 2]
    baqylau = sum(c * s for c, s in zip(cifrlar[:11], salmaq1)) % 11
    if baqylau == 10:
        baqylau = sum(c * s for c, s in zip(cifrlar[:11], salmaq2)) % 11
        if baqylau == 10:
            return False
    return baqylau == cifrlar[11]


class TestZhsn(unittest.TestCase):
    def test_durys(self):
        self.assertTrue(zhsn_durys_pa("050315501230"))
        self.assertTrue(zhsn_durys_pa("081212600455"))

    def test_ekinshi_salmaq(self):
        self.assertTrue(zhsn_durys_pa("050315501301"))

    def test_qate_baqylau(self):
        self.assertFalse(zhsn_durys_pa("050315501231"))

    def test_uzyndyq(self):
        self.assertFalse(zhsn_durys_pa("05031550123"))

    def test_arip(self):
        self.assertFalse(zhsn_durys_pa("05031550123A"))


if __name__ == "__main__":
    unittest.main()
```

**6-тапсырма**
```python
import os
import unittest


def ortasha_fail(path):
    """Файлдағы баллдардың орташасын қайтарады (бос жолдар ескерілмейді)."""
    with open(path, encoding="utf-8") as f:
        ballar = [int(zhol) for zhol in f if zhol.strip()]
    if not ballar:
        return 0
    return sum(ballar) / len(ballar)


class TestOrtashaFail(unittest.TestCase):
    def setUp(self):
        self.path = "test_ballar.txt"
        with open(self.path, "w", encoding="utf-8") as f:
            f.write("80\n\n90\n100\n")

    def tearDown(self):
        if os.path.exists(self.path):
            os.remove(self.path)

    def test_ortasha(self):
        self.assertEqual(ortasha_fail(self.path), 90)

    def test_fail_zhoq(self):
        with self.assertRaises(FileNotFoundError):
            ortasha_fail("zhoq_fail.txt")


if __name__ == "__main__":
    unittest.main()
```

**7-тапсырма** — үлгі (оқушы функциясына қарай өзгереді):
```python
import unittest

import pandas as pd


def clean_data(df):
    df = df.drop_duplicates()
    df = df.dropna(subset=["aty"])
    df = df[df["ball"].between(0, 100)].copy()
    return df


class TestCleanData(unittest.TestCase):
    def setUp(self):
        self.df = pd.DataFrame({
            "aty": ["Әлия", "Әлия", None, "Ерлан", "Дана"],
            "ball": [87, 87, 70, 150, 92],
        })

    def test_zhol_sany(self):
        self.assertEqual(len(clean_data(self.df)), 2)

    def test_qate_ball_zhoq(self):
        taza = clean_data(self.df)
        self.assertTrue(taza["ball"].between(0, 100).all())

    def test_bastapqy_ozgermeidi(self):
        clean_data(self.df)
        self.assertEqual(len(self.df), 5)


if __name__ == "__main__":
    unittest.main()
```

**8-тапсырма.** 1-тапсырмадағы тесттер: `range(2, n)` мутанты → `faktorial(5)` = 24, `test_qalypty` құлайды; `natije = 0` → барлық нәтиже 0, құлайды; `n <= 0` → `faktorial(0)` `ValueError` береді, `test_nol_zhane_bir` құлайды (ERROR). Барлық мутант «өлтірілді». Егер оқушыда `faktorial(0)` тесті болмаса, үшінші мутант тірі қалады — дәл осы шекаралық тесттің маңызын талқылаңыз.

**9-тапсырма**
```python
def kvadrat(x):
    return x * x


def test_kvadrat_on():
    assert kvadrat(3) == 9


def test_kvadrat_teris():
    assert kvadrat(-2) == 4


def test_qasaqana_qate():
    assert kvadrat(2) == 5, "2·2 ≠ 5"


def zhurgizu():
    ok = fail = error = 0
    for atau, obj in list(globals().items()):
        if atau.startswith("test_") and callable(obj):
            try:
                obj()
                print(f"OK     {atau}")
                ok += 1
            except AssertionError as e:
                print(f"FAIL   {atau}: {e}")
                fail += 1
            except Exception as e:
                print(f"ERROR  {atau}: {type(e).__name__}: {e}")
                error += 1
    print(f"Барлығы: {ok + fail + error}, OK: {ok}, FAIL: {fail}, ERROR: {error}")


zhurgizu()
```

</details>
