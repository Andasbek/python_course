# Урок 84. Практические задания: написание тестов с помощью unittest

Структура работы: для каждого задания отдельная папка; проверяемый код — `<модуль>.py`, тесты — `test_<модуль>.py`. Запуск: `python -m unittest -v`. Для каждой функции напишите хотя бы один тест на обычный случай, один на граничный и (если нужно) один на ошибочный ввод.

## Базовый уровень

**Задание 1. Математические функции.** Создайте модуль `matematika.py` и напишите к нему тесты (не менее 3 тестов на каждую функцию):

```python
def zhup_pa(n):
    """Возвращает True, если n чётное."""
    return n % 2 == 0


def faktorial(n):
    """Возвращает значение n!. Для отрицательного n — ValueError."""
    if n < 0:
        raise ValueError("Факториала отрицательного числа не существует")
    natije = 1
    for i in range(2, n + 1):
        natije *= i
    return natije


def en_ulken(tizim):
    """Возвращает наибольший элемент списка. Для пустого списка — None."""
    if not tizim:
        return None
    maks = tizim[0]
    for x in tizim:
        if x > maks:
            maks = x
    return maks
```

Подсказка: чему равно `faktorial(0)`? А `en_ulken([-5, -2, -9])`? Это важные граничные случаи.

**Задание 2. Палиндром.** Напишите функцию `palindrom(soz)`: возвращает `True`, если слово/предложение читается одинаково в обе стороны (регистр букв и пробелы не учитываются). Тесты: `"Шалаш"` → `True`, `"Python"` → `False`, `"А роза упала на лапу Азора"` → `True`, `""` → `True`, `"ab"` → `False`.

**Задание 3. Тесты находят ошибку.** Учитель написал функцию расчёта цены со скидкой. Правило: **начиная с 5000 тенге** (5000 включительно) — скидка 10%, **начиная с 20 000 тенге** — 15%, если меньше — скидки нет. Отрицательная сумма — `ValueError`. Напишите тесты, найдите **две** ошибки в функции, затем исправьте их.

```python
def zhenildikpen(soma):
    """Возвращает сумму к оплате с учётом скидки."""
    if soma > 20000:
        return soma * 0.85
    if soma > 5000:
        return soma * 0.9
    return soma
```

**Задание 4. Тестирование класса.** Добавьте к классу `Sebet` из теории ещё 3 теста: сумма пустой корзины равна 0; сумма нескольких разных товаров; `ValueError` при `sany=0`. Всё — через `setUp`.

## Продвинутый уровень

**Задание 5. Проверка ИИН (TDD).** Индивидуальный идентификационный номер гражданина Казахстана (ИИН) состоит из 12 цифр; последняя цифра — контрольная. Алгоритм:
1. Первые 11 цифр умножаем на веса 1, 2, 3, ..., 11 и складываем, берём остаток от деления на 11.
2. Если остаток меньше 10 — это контрольная цифра.
3. Если остаток равен 10 — пересчитываем с весами 3, 4, 5, 6, 7, 8, 9, 10, 11, 1, 2. Если остаток снова 10 — такой ИИН недействителен.

**Сначала** напишите тесты `test_zhsn.py` (они упадут — «красный»), **затем** — функцию `zhsn_durys_pa(zhsn)` («зелёный»). Тестовые данные (вымышленные номера):
- `"050315501230"` → `True`;
- `"081212600455"` → `True`;
- `"050315501301"` → `True` (контрольная цифра вычисляется по вторым весам);
- `"050315501231"` → `False` (неверная контрольная цифра);
- `"05031550123"` (11 цифр), `"05031550123A"` (есть буква) → `False`.

**Задание 6. Тест работы с файлом.** Функция `ortasha_fail(path)` читает текстовый файл, в каждой строке которого записан один балл, и возвращает средний балл (пустые строки пропускает). В тесте `setUp` должен создавать временный файл, а `tearDown` — удалять его (`os.remove`). Проверьте, что при отсутствии файла ожидается `FileNotFoundError`.

**Задание 7. Тест для аналитического проекта.** Напишите тест для функции `clean_data(df)` из своего аналитического проекта (урок 81): создайте маленький DataFrame (с дубликатом, пропуском, ошибочным значением) и проверьте количество строк и правильность значений после очистки.

## ★ Олимпиадная / творческая задача

**Задание 8. «Охота на мутантов».** Специально внесите в функцию `faktorial` из задания 1 небольшие изменения («мутанты»): `range(2, n + 1)` → `range(2, n)`; `natije = 1` → `natije = 0`; `n < 0` → `n <= 0`. «Убивают» ли ваши тесты каждого мутанта (падает ли хотя бы один тест)? Если какой-то мутант выжил — добавьте новый тест. Это профессиональный метод проверки качества тестов (mutation testing).

**Задание 9. Свой запускатель тестов.** Напишите без `unittest` простой собственный запускатель тестов: он находит в модуле все функции, начинающиеся с `test_` (`globals()`), запускает каждую и выводит «FAIL» при `AssertionError`, «ERROR» при другой ошибке, иначе «OK», а в конце показывает итоговое количество.

## Домашнее задание

1. Напишите для не менее чем 2 функций/методов своего проекта (ООП, GUI, SQLite или анализ) в файле `test_*.py` всего не менее 6 тестов.
2. Добавьте в README скриншот результата `python -m unittest -v` (или скопируйте его текст).
3. Загрузите на GitHub: `git commit -m "Добавлены модульные тесты"`.

## Для педагога: решения

<details><summary>Показать решения</summary>

Для удобства в решениях код и тесты даны в одном блоке; ученики разделяют их на два файла (`from matematika import ...`).

**Задание 1**
```python
import unittest


def zhup_pa(n):
    return n % 2 == 0


def faktorial(n):
    if n < 0:
        raise ValueError("Факториала отрицательного числа не существует")
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

**Задание 2**
```python
import unittest


def palindrom(soz):
    """Проверяет, является ли строка палиндромом, без учёта регистра и пробелов."""
    taza = soz.replace(" ", "").lower()
    return taza == taza[::-1]


class TestPalindrom(unittest.TestCase):
    def test_soz(self):
        self.assertTrue(palindrom("Шалаш"))

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

**Задание 3.** Ошибки: (1) вместо `>` должно быть `>=` — на суммах 5000 и 20 000 скидка не предоставляется; (2) отрицательная сумма не проверяется. Находящие их тесты — граничные значения и отрицательная сумма:
```python
import unittest


def zhenildikpen(soma):
    """Возвращает сумму к оплате с учётом скидки."""
    if soma < 0:
        raise ValueError("Сумма не может быть отрицательной")
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

**Задание 4**
```python
import unittest


class Sebet:
    def __init__(self):
        self.tauarlar = {}

    def qosu(self, atau, baga, sany=1):
        if baga <= 0 or sany <= 0:
            raise ValueError("Цена и количество должны быть положительными")
        self.tauarlar[atau] = self.tauarlar.get(atau, 0) + baga * sany

    def zhalpy(self):
        return sum(self.tauarlar.values())


class TestSebetQosymsha(unittest.TestCase):
    def setUp(self):
        self.sebet = Sebet()

    def test_bos_sebet(self):
        self.assertEqual(self.sebet.zhalpy(), 0)

    def test_birneshe_tauar(self):
        self.sebet.qosu("Хлеб", 250, 2)
        self.sebet.qosu("Молоко", 480)
        self.sebet.qosu("Яблоки", 900, 3)
        self.assertEqual(self.sebet.zhalpy(), 3680)

    def test_nol_sany(self):
        with self.assertRaises(ValueError):
            self.sebet.qosu("Хлеб", 250, 0)


if __name__ == "__main__":
    unittest.main()
```

**Задание 5**
```python
import unittest


def zhsn_durys_pa(zhsn):
    """Проверяет формат ИИН и его контрольную цифру."""
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

**Задание 6**
```python
import os
import unittest


def ortasha_fail(path):
    """Возвращает средний балл из файла (пустые строки не учитываются)."""
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

**Задание 7** — образец (меняется в зависимости от функции ученика):
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

**Задание 8.** Тесты из задания 1: мутант `range(2, n)` → `faktorial(5)` = 24, падает `test_qalypty`; `natije = 0` → все результаты 0, падает; `n <= 0` → `faktorial(0)` выбрасывает `ValueError`, падает `test_nol_zhane_bir` (ERROR). Все мутанты «убиты». Если у ученика нет теста `faktorial(0)`, третий мутант выживет — обсудите важность именно этого граничного теста.

**Задание 9**
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
    print(f"Всего: {ok + fail + error}, OK: {ok}, FAIL: {fail}, ERROR: {error}")


zhurgizu()
```

</details>
