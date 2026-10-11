# Урок 99. Тестирование проекта. Исправление найденных ошибок.

## Темы

1. Зачем нужно тестирование и чем оно не является?
2. Пирамида тестирования
3. Как выбирать тестовые случаи?
4. Модульные тесты проекта
5. Тестирование API: `unittest.mock`
6. Ручное тестирование: тест-кейсы
7. Отчёт об ошибке (bug report)
8. 5 шагов исправления ошибки
9. Логирование: узнать об ошибке, случившейся у пользователя
10. Частые ошибки
11. Основные понятия

## 1. Зачем нужно тестирование и чем оно не является?

**Тестирование** — процесс проверки того, что программа работает согласно требованиям, и поиска ошибок.

> «Тестирование может показать наличие ошибок, но никогда не докажет их отсутствие» — Эдсгер Дейкстра.

Поэтому цель — не «показать, что программа работает», а **«попытаться её сломать»**. Хороший тестировщик — человек, который ищет самое слабое место программы.

Чем раньше найдена ошибка, тем дешевле её исправить: ошибка, найденная тестом при разработке, исправляется за минуты, а найденная пользователем после релиза — часами (и стоит репутации).

## 2. Пирамида тестирования

```text
                 ▲
                / \        Ручные / интерфейсные тесты
               /   \       (мало, медленно, зато глазами пользователя)
              /─────\
             /       \     Интеграционные тесты
            /         \    (логика + настоящая БД / файл вместе)
           /───────────\
          /             \  Модульные (unit) тесты
         /               \ (много, быстро, проверяют одну функцию)
        ───────────────────
```

| Уровень | Что проверяет | Инструмент | Пример |
|---|---|---|---|
| Модульный | одна функция/метод, изолированно | `unittest` | правильно ли считает `balance()` |
| Интеграционный | несколько модулей вместе | `unittest` + БД `:memory:` | `Wallet` + `Database`: влияет ли добавленная операция на баланс |
| Ручной (системный) | вся программа, как пользователь | таблица тест-кейсов | добавить расход из окна → диаграмма обновляется |

Почему пирамида? Модульный тест повторяется сотни раз меньше чем за секунду; ручной тест — каждый раз 10 минут. Поэтому основная нагрузка — на автоматических тестах, а ручное тестирование — дополнение к ним.

## 3. Как выбирать тестовые случаи?

### 3.1. Классы эквивалентности
Делим все возможные входные данные на группы, в которых «программа ведёт себя одинаково», и берём из каждой группы **одного** представителя.

Пример: поле «сумма» (целое число от 1 до 10 000 000):

| Класс | Представитель | Ожидаемое поведение |
|---|---|---|
| Правильное число | `800` | сохраняется |
| Ноль / отрицательное | `-5` | ошибка: «Сумма должна быть положительной» |
| Слишком большое | `99999999` | ошибка: «Сумма слишком большая» |
| Не число | `"абв"` | ошибка: «Введите число» |
| Пусто | `""` | ошибка: «Введите число» |
| Число с пробелами | `" 800 "` | сохраняется (пробелы удаляются) |

### 3.2. Граничные значения
Ошибки чаще всего бывают **на границе** (`<` вместо `<=`). Поэтому проверяем обе стороны границы:

```text
     0      1                        10 000 000   10 000 001
  ───●──────●───────────────────────────●──────────●───>
  ошибка  верно                        верно      ошибка
```

Тестовые значения: `0`, `1`, `10000000`, `10000001`.

Другие граничные случаи: пустой список, один элемент, последний день месяца, 29 февраля, смена года, равные очки.

## 4. Модульные тесты проекта

Проверяемая функция (из `qalta/logic.py`):

```python
MAX_AMOUNT = 10_000_000


def parse_amount(text):
    """Превращает текст в сумму. При ошибке ValueError (с сообщением на русском)."""
    cleaned = text.strip().replace(" ", "")
    if not cleaned.isdigit():
        raise ValueError("Введите сумму числом")
    value = int(cleaned)
    if value <= 0:
        raise ValueError("Сумма должна быть положительной")
    if value > MAX_AMOUNT:
        raise ValueError("Сумма слишком большая")
    return value


for sample in ["800", " 1 500 ", "0", "абв", "10000001"]:
    try:
        print(repr(sample), "->", parse_amount(sample))
    except ValueError as err:
        print(repr(sample), "-> ошибка:", err)
```

Результат:
```text
'800' -> 800
' 1 500 ' -> 1500
'0' -> ошибка: Сумма должна быть положительной
'абв' -> ошибка: Введите сумму числом
'10000001' -> ошибка: Сумма слишком большая
```

Теперь тесты для этой функции и для `Wallet` — `tests/test_logic.py`:

```python
"""tests/test_logic.py — тесты логики «Карманных денег»."""
import unittest

from qalta.database import Database
from qalta.logic import Wallet, parse_amount, MAX_AMOUNT


class ParseAmountTest(unittest.TestCase):
    """parse_amount: классы эквивалентности и граничные значения."""

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
    """Wallet + Database вместе (интеграционный), с временной БД."""

    def setUp(self):
        # Перед каждым тестом новая пустая БД — тесты не влияют друг на друга
        self.db = Database(":memory:")
        self.wallet = Wallet(self.db)
        cats = {c["title"]: c["id"] for c in self.db.categories()}
        self.parents, self.food = cats["Родители"], cats["Еда"]

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

Запуск (из корня проекта):
```bash
python -m unittest discover -s tests -v
```

Результат:
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

Новые приёмы:
- **`subTest`** — проверка множества значений в одном тесте; какое из них упало, показывается отдельно.
- **БД `:memory:`** — не трогаем настоящий файл `qalta.db`, каждый тест начинается с чистой БД.
- **`tearDown`** — очистка после каждого теста.
- Имя теста говорит, **что проверяется**: `test_delete_restores_balance`.

> Если в папке `tests/` есть пустой `__init__.py`, `discover` правильно найдёт её как пакет.

## 5. Тестирование API: `unittest.mock`

В тестах мы не выходим в настоящий интернет: это медленно, нестабильно, и у API есть ограничения. Вместо этого заменяем `requests.get` **фиктивным объектом** (mock):

```python
import unittest
from unittest import mock

import requests


def get_kzt_rate():
    """Получает курс из API; при ошибке None."""
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

`mock.patch("requests.get")` на время теста заменяет `requests.get` фиктивной функцией; `side_effect` — выбросить ошибку при вызове. Так мы проверяем случай «нет интернета», не отключая интернет.

## 6. Ручное тестирование: тест-кейсы

Интерфейс трудно проверять автоматически, поэтому проверяем его вручную по **таблице тест-кейсов**. Тест-кейс — воспроизводимая проверка с конкретными шагами.

| ID | Проверка | Предусловие | Шаги | Ожидаемое поведение | Фактический результат | Статус |
|---|---|---|---|---|---|---|
| ТК-01 | Добавить расход | пустая БД | 800, «Еда», Сохранить | в таблице 1 строка, баланс −800 | совпадает | Пройден |
| ТК-02 | Пустая сумма | — | оставить поле пустым, Сохранить | предупреждение, записи нет | совпадает | Пройден |
| ТК-03 | Длинный комментарий | — | 1000 символов | сохраняется, таблица не ломается | ширина таблицы сломалась | **Не пройден** → #21 |
| ТК-04 | Удаление | есть 1 запись | выбрать, Удалить, Да | записи нет, баланс 0 | совпадает | Пройден |
| ТК-05 | Без интернета | Wi-Fi выключен | открыть программу | «Курс недоступен», остальное работает | совпадает | Пройден |

**Самый эффективный способ — перекрёстное тестирование:** вашу программу проверяет другой человек по вашим тест-кейсам — автор подсознательно обходит слабые места своей программы.

## 7. Отчёт об ошибке (bug report)

Когда ошибка найдена, её описывают так, чтобы **другой человек мог её воспроизвести** (например, в виде GitHub Issue):

```text
Название: При длинном комментарии ломается ширина таблицы
Шаги:
  1. Открыть программу
  2. Сумма 100, комментарий — текст из 1000 символов
  3. Нажать «Сохранить»
Ожидалось: текст в таблице отображается сокращённым
Фактически: таблица выходит за пределы окна, диаграмма не видна
Окружение: Windows 10, Python 3.11, коммит a1b2c3d
Важность: важно
```

Жизненный цикл ошибки: `Новая → В работе → Исправлена → Проверена → Закрыта`. Закрывает **тот, кто проверил**, а не тот, кто исправил, — или как минимум повторно проходит тест-кейс.

## 8. 5 шагов исправления ошибки

```text
1. Воспроизвести   Найти шаги, стабильно вызывающие ошибку
2. Написать тест   Написать тест, показывающий ошибку, — он КРАСНЫЙ (падает)
3. Найти место     print / logging / отладчик; последняя строка traceback
4. Исправить       минимальным изменением
5. Проверить       новый тест ЗЕЛЁНЫЙ + все старые тесты зелёные
```

Тест из шага 2 называется **регрессионным тестом**: он следит за тем, чтобы эта ошибка не появилась снова в будущем (регрессия).

Пример. Отчёт об ошибке: «В отчёте за февраль не учитываются дни после 28-го» (в коде было написано `month_end = 28`).

```python
import calendar
import datetime


def days_in_month(year, month):
    """Количество дней в месяце (с учётом високосного года)."""
    return calendar.monthrange(year, month)[1]


def month_range(year, month):
    """Первый и последний день месяца."""
    first = datetime.date(year, month, 1)
    last = datetime.date(year, month, days_in_month(year, month))
    return first, last


# Регрессионные проверки
assert month_range(2028, 2)[1].day == 29       # 2028 — високосный год
assert month_range(2027, 2)[1].day == 28
assert month_range(2027, 12)[1] == datetime.date(2027, 12, 31)
print(month_range(2028, 2))
```

Результат:
```text
(datetime.date(2028, 2, 1), datetime.date(2028, 2, 29))
```

## 9. Логирование: узнать об ошибке, случившейся у пользователя

Если пользователь говорит «что-то сломалось», как узнать, что произошло? Непредвиденные ошибки записываем в файл с помощью модуля `logging`:

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
    """Выполняет сохранение; непредвиденную ошибку записывает в лог."""
    try:
        return save_function(*args)
    except ValueError:
        raise                                   # ошибка пользователя — покажет интерфейс
    except Exception:
        log.exception("Непредвиденная ошибка при сохранении, args=%s", args)
        return None
```

`log.exception` записывает traceback полностью. Добавьте `qalta.log` в `.gitignore`.

## Частые ошибки

**1. Тест ничего не проверяет.**
```python
def test_balance(self):
    self.wallet.balance()          # нет assert — тест всегда «проходит»
```
Правильно: `self.assertEqual(self.wallet.balance(), 0)`.

**2. Тесты зависят друг от друга.** Тест 1 добавляет запись, тест 2 ожидает, что «запись есть». Порядок выполнения тестов не гарантирован. Правильно: каждый тест готовит свои данные в `setUp`.

**3. Тестирование на настоящем файле БД** — тесты портят данные пользователя. Правильно: `Database(":memory:")`.

**4. Сравнение дробных чисел через `assertEqual`.** `0.1 + 0.2 == 0.3` → `False`. Правильно: `self.assertAlmostEqual(a, b, places=2)`.

**5. Исправить ошибку и не перезапустить тесты.** Исправление может сломать что-то другое. После каждого исправления: `python -m unittest discover -s tests`.

**6. «У меня работает».** Проверьте на другом компьютере: `git clone` в чистую папку, новый `venv`, `pip install -r requirements.txt`.

## Основные понятия
| Понятие | Объяснение |
|---|---|
| Модульный тест | автоматический тест, изолированно проверяющий одну функцию |
| Интеграционный тест | тест, проверяющий несколько модулей вместе |
| Класс эквивалентности | группа входных значений, на которых программа ведёт себя одинаково |
| Граничное значение | значение на границе класса (0, 1, max, max+1) |
| Тест-кейс | описание ручной проверки с шагами и ожидаемым результатом |
| Mock | фиктивный объект, используемый в тесте вместо настоящего (API, файла) |
| Отчёт об ошибке | описание, достаточное для воспроизведения ошибки |
| Регрессионный тест | тест, проверяющий, что исправленная ошибка не появится снова |
| `subTest` | отдельная проверка нескольких случаев внутри одного теста |

## Примечание

- Параметр `encoding` у `logging.basicConfig()` появился в Python 3.9; в более ранних версиях кодировку задают через `logging.FileHandler(..., encoding="utf-8")`.
- `unittest.main()` и `discover` находят только методы, имена которых начинаются с `test`, и только файлы по шаблону `test*.py`.
- `mock.patch` заменяет объект **там, где он используется**: если в модуле написано `from requests import get`, патчить нужно `"qalta.currency.get"`, а не `"requests.get"`.
- Популярная альтернатива `unittest` — сторонняя библиотека `pytest`: тесты пишутся обычными функциями с `assert`, а существующие `unittest`-тесты она тоже запускает.
- Покрытие кода тестами измеряет пакет `coverage` (`coverage run -m unittest discover` → `coverage report`).
- Документация: https://docs.python.org/3/library/unittest.html и https://docs.python.org/3/library/unittest.mock.html
