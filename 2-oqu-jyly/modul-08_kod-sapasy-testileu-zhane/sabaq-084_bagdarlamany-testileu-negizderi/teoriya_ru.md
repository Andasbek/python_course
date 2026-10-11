# Урок 84. Основы тестирования программ. Модуль unittest. Написание тестов.

## Темы

1. Что такое тестирование
2. Какие случаи нужно проверять
3. Модуль `unittest`
4. Основные методы `assert`
5. `setUp` — подготовка перед каждым тестом
6. Код, удобный для тестирования
7. Частые ошибки
8. Основные понятия

## 1. Что такое тестирование

**Тестирование** (testing) — проверка правильности программы путём сравнения её фактического результата с **ожидаемым**.

```text
 Ввод  ──►  [ Функция ]  ──►  Фактический результат
                                    │
                                    ▼  сравнение
                              Ожидаемое значение  ──►  ПРОЙДЕН / УПАЛ
```

> «Тестирование может показать наличие ошибок, но никогда не докажет их отсутствие» (Э. Дейкстра). Тест — не 100% гарантия, но самый надёжный способ обнаружить ошибку рано.

### Ручное и автоматическое тестирование

| | Ручное | Автоматическое |
|---|---|---|
| Кто проверяет | Человек (запускает программу, вводит данные и смотрит) | Тестовый код |
| Скорость | Медленно | Очень быстро |
| Повторение | Утомляет, допускаются ошибки | Бесконечно, одинаково |
| Когда эффективно | Интерфейс, удобство, дизайн | Логика, вычисления, данные |

### Виды тестов (пирамида тестирования)

```text
               ▲   мало, медленно, дорого
              / \
             / Ру\      Ручной / приёмочный тест (UI, глазами пользователя)
            /─────\
           / Интег-\    Интеграционный тест (модули вместе: GUI + SQLite)
          / рацион. \
         /───────────\
        /  Модульные  \  Модульный (unit) тест — одна функция/метод
       /_______________\ много, быстро, дёшево
```

Основание пирамиды — **модульные тесты** (юнит-тесты); их пишут больше всего.

## 2. Какие случаи нужно проверять

Для одной функции пишем несколько **тестовых случаев** (тест-кейсов):

| Вид | Пример (`bagaga_audaru(ball)`: 100 баллов → оценка по 5-балльной шкале) |
|---|---|
| Обычный случай | 92 → 5, 70 → 4 |
| Граничный случай | 85 → 5, 84 → 4, 65 → 4, 64 → 3, 0, 100 |
| Особый/пустой ввод | Пустой список, пустая строка, `None` |
| Ошибочный ввод | 105, −1 → `ValueError` |

**Граничные значения** — «любимое место» ошибок. Путаница `>` и `>=`, невключённое последнее значение `range` проявляются именно здесь.

### Шаблон AAA

Каждый тест состоит из трёх частей:
1. **Arrange (Подготовь)** — подготовить входные данные;
2. **Act (Выполни)** — вызвать проверяемую функцию;
3. **Assert (Проверь)** — сравнить результат с ожидаемым.

## 3. Модуль `unittest`

`unittest` — встроенный в Python (не требует установки) инструмент тестирования.

### Проверяемый модуль: `bagalau.py`

```python
"""Функции для обработки баллов учеников."""


def ortasha(ballar):
    """Возвращает среднее значение баллов. Для пустого списка — 0."""
    if not ballar:
        return 0
    return sum(ballar) / len(ballar)


def bagaga_audaru(ball):
    """Переводит 100-балльную шкалу в оценку по 5-балльной системе."""
    if not 0 <= ball <= 100:
        raise ValueError("Балл должен быть в диапазоне 0..100")
    if ball >= 85:
        return 5
    if ball >= 65:
        return 4
    if ball >= 40:
        return 3
    return 2
```

### Файл тестов: `test_bagalau.py`

```python
import unittest

from bagalau import ortasha, bagaga_audaru


class TestOrtasha(unittest.TestCase):
    """Тесты функции ortasha()."""

    def test_qalypty_tizim(self):
        ballar = [80, 90, 100]                 # Arrange
        natije = ortasha(ballar)               # Act
        self.assertEqual(natije, 90)           # Assert

    def test_bos_tizim(self):
        self.assertEqual(ortasha([]), 0)

    def test_bolshek_natije(self):
        self.assertAlmostEqual(ortasha([1, 2]), 1.5)


class TestBagagaAudaru(unittest.TestCase):
    """Тесты функции bagaga_audaru()."""

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

**Правила:**
- имя файла тестов начинается с `test_`;
- тестовый класс наследуется от `unittest.TestCase` (наследование);
- каждый тест — это **метод**, начинающийся с `test_`; методы с другими именами как тесты не запускаются;
- каждый тест проверяет одну вещь и не зависит от других тестов.

### Запуск

```bash
python -m unittest -v
```

Результат:
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

Другие способы: `python test_bagalau.py` (через `unittest.main()` в конце файла), `python -m unittest test_bagalau` (один файл), `python -m unittest test_bagalau.TestOrtasha.test_bos_tizim` (один тест). `-v` (verbose) — показывает имя каждого теста. Без параметров `python -m unittest` сам находит все файлы `test*.py` в текущей папке (discovery).

### Когда тест падает

Теперь специально сделаем ошибку в `bagalau.py`: вместо `if ball >= 85:` напишем `if ball > 85:`.

Результат:
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

Каждая точка `.` — пройденный тест, `F` — упавший тест (failure), `E` — неожиданная ошибка внутри теста (error, например, `NameError`). Сообщение говорит точно: для 85 баллов вернулось 4, а ожидалось 5. Упал только граничный тест — обычный случай (92) ошибку «не увидел»!

## 4. Основные методы `assert`

| Метод | Проверяет |
|---|---|
| `assertEqual(a, b)` | `a == b` |
| `assertNotEqual(a, b)` | `a != b` |
| `assertTrue(x)` / `assertFalse(x)` | `bool(x)` истинно / ложно |
| `assertIs(a, b)` / `assertIsNone(x)` | `a is b` / `x is None` |
| `assertIn(a, b)` / `assertNotIn(a, b)` | `a in b` |
| `assertIsInstance(a, Klass)` | Является ли `a` объектом заданного класса |
| `assertAlmostEqual(a, b, places=7)` | Приближённое равенство дробных чисел |
| `assertGreater(a, b)` / `assertLess(a, b)` | `a > b` / `a < b` |
| `assertRaises(Qate)` | Внутри блока должна возникнуть `Qate` |

**Почему `assertAlmostEqual`?** В компьютере `0.1 + 0.2 == 0.3` → `False` (результат `0.30000000000000004`). Сравнивать дробные числа через `assertEqual` — ловушка.

## 5. `setUp` — подготовка перед каждым тестом

Если нескольким тестам нужен один и тот же объект, создаём его в методе `setUp`. Он вызывается заново перед **каждым** тестом — поэтому тесты не влияют друг на друга. Пример тестирования класса:

```python
import unittest


class Sebet:
    """Корзина магазина."""

    def __init__(self):
        self.tauarlar = {}

    def qosu(self, atau, baga, sany=1):
        if baga <= 0 or sany <= 0:
            raise ValueError("Цена и количество должны быть положительными")
        self.tauarlar[atau] = self.tauarlar.get(atau, 0) + baga * sany

    def zhalpy(self):
        return sum(self.tauarlar.values())


class TestSebet(unittest.TestCase):

    def setUp(self):
        self.sebet = Sebet()            # каждый тест начинается с новой пустой корзины
        self.sebet.qosu("Хлеб", 250, 2)

    def test_qosu(self):
        self.assertIn("Хлеб", self.sebet.tauarlar)
        self.assertEqual(self.sebet.zhalpy(), 500)

    def test_qaita_qosu(self):
        self.sebet.qosu("Хлеб", 250)
        self.assertEqual(self.sebet.zhalpy(), 750)

    def test_teris_baga(self):
        with self.assertRaises(ValueError):
            self.sebet.qosu("Молоко", -480)


if __name__ == "__main__":
    unittest.main()
```

`tearDown` — выполняется **после** каждого теста (например, удалить временный файл, закрыть базу данных).

## 6. Код, удобный для тестирования

Чтобы функцию было легко тестировать:
- выносите **логику вычислений** в функцию, отдельную от `input()`/`print()` (функция пусть возвращает значение через `return`);
- одна функция — одна задача;
- выносите логику в отдельный модуль, отдельно от GUI (Tkinter) или базы данных.

```python
# Трудно тестировать:
def esepte():
    a = int(input("Число: "))
    print(a * a)


# Легко тестировать:
def kvadrat(a):
    return a * a
```

**TDD (Test-Driven Development, разработка через тестирование)** — сначала пишем тест (он падает, «красный»), затем код, который его проходит («зелёный»), потом улучшаем код («рефакторинг»). Этот подход учит писать код по требованиям.

Примечание: в профессиональных проектах кроме `unittest` широко используется библиотека `pytest` (`pip install pytest`); она умеет запускать и тесты `unittest`.

## Частые ошибки

**1. Имя тестового метода без `test_`.**
```python
class TestOrtasha(unittest.TestCase):
    def tekseru_bos(self):              # не запустится!
        self.assertEqual(ortasha([]), 0)
```
Правильно: `def test_bos(self):`.

**2. Забыли `self`:** `assertEqual(a, b)` → `NameError`. Правильно: `self.assertEqual(a, b)`.

**3. Передача в `assertRaises` вызванной функции.**
```python
self.assertRaises(ValueError, bagaga_audaru(105))   # ошибка вылетит прямо внутри теста
```
Правильно:
```python
with self.assertRaises(ValueError):
    bagaga_audaru(105)
```
(или `self.assertRaises(ValueError, bagaga_audaru, 105)` — функция и аргумент отдельно).

**4. Ожидаемый ответ теста скопирован из кода.** `assertEqual(ortasha([3, 4]), ortasha([3, 4]))` — проходит всегда и ничего не проверяет. Ожидаемое значение **вычисляйте вручную**.

**5. Зависимость тестов друг от друга.** Если один тест использует данные, созданные другим, тесты упадут при изменении порядка. Пусть каждый тест готовит своё в `setUp`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Тестирование | Проверка путём сравнения фактического результата с ожидаемым |
| Модульный тест (unit test) | Тест, проверяющий отдельно одну функцию/метод |
| Тестовый случай (test case) | Пара «ввод + ожидаемый результат» |
| Граничное значение | Значение в точке, где меняется условие (84/85) |
| `unittest.TestCase` | Родительский класс тестового класса |
| `assertEqual`, `assertRaises` | Методы проверки |
| `setUp` / `tearDown` | Подготовка перед / после каждого теста |
| OK / FAIL / ERROR | Пройден / проверка не прошла / ошибка внутри теста |
| TDD | Сначала тест, потом код |

## Примечание

- Файлы тестов должны быть импортируемыми модулями: имя `test_bagalau.py`, а не `test-bagalau.py` или `test bagalau.py`, иначе `python -m unittest` их не найдёт. Для поиска в подпапках в них нужен `__init__.py` или явный запуск `python -m unittest discover -s tests`.
- `self.subTest(...)` позволяет проверить много значений в цикле внутри одного теста и увидеть **все** неудачные случаи, а не только первый: `for b, o in [(85, 5), (84, 4)]: with self.subTest(ball=b): ...`.
- Порядок запуска тестов в `unittest` — алфавитный по именам методов, а не порядок в файле; поэтому тесты не должны зависеть друг от друга.
- Для подмены внешних зависимостей (сеть, время, `input()`) используется встроенный модуль `unittest.mock`, например `@patch("builtins.input", return_value="5")`.
- `pytest` позволяет писать тесты обычными функциями и обычным `assert`; команда `pytest` запускает и тесты `unittest`.
- Документация: [unittest](https://docs.python.org/3/library/unittest.html), [unittest.mock](https://docs.python.org/3/library/unittest.mock.html).
