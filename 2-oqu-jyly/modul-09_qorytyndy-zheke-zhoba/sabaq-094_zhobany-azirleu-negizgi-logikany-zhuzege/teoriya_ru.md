# Урок 94. Разработка проекта: реализация основной логики (этап 1).

## Темы

1. Что такое основная логика?
2. С чего начинаем? Порядок действий
3. Пример проекта: `models.py`
4. Пример проекта: `logic.py` — этап 1
5. Образцы логики для других типов проектов
6. Как искать решение, если код не работает
7. Частые ошибки
8. Основные понятия

## 1. Что такое основная логика?

**Основная логика (бизнес-логика, business logic)** — «мозг» программы: вычисления, правила, решения. Она **не знает**, как выглядит экран и в каком файле хранятся данные.

| Проект | Основная логика | Не логика (другой слой) |
|---|---|---|
| Карманные деньги | расчёт баланса, сумма по категориям, проверка лимита | окно Tkinter, запрос SQLite |
| Викторина | проверка ответа, подсчёт очков, перемешивание вопросов | вывод вопросов на экран, чтение JSON |
| Игра (Pygame) | жизни игрока, очки, правило перехода на уровень | отрисовка спрайтов, звук |
| Библиотека | правило выдачи/возврата книги, определение просрочки | окна, БД |

**Аналогия:** логика — повар на кухне, интерфейс — официант, база данных — склад. Повар не знает, к какому столу отнесут блюдо, — он просто готовит вкусную еду. Даже если заменить официанта (Tkinter → веб-сайт), повар не меняется.

Почему логику пишем отдельно?
1. **Легко тестировать** — проверяем функцию, не открывая окна (модуль `unittest`).
2. **Поэтапная разработка** — интерфейса ещё нет, а логика уже работает.
3. **Повторное использование** — к этой же логике позже можно добавить веб-интерфейс (например, на Flask).

## 2. С чего начинаем? Порядок действий

1. Берём с Kanban-доски **одну** задачу.
2. По модели данных пишем классы — `models.py`.
3. Пишем одно самое важное действие (например, «добавить операцию») в `logic.py`.
4. В конце файла в блоке `if __name__ == "__main__":` **проверяем вручную**.
5. Если работает → коммит. Переходим к следующему действию.

```text
     ┌──────────────┐
     │ Взять задачу │
     └──────┬───────┘
            v
   ┌─────────────────┐     нет    ┌───────────┐
   │ Написать 5–15   │ ─────────> │ Отладка   │
   │ строк, запустить│ <───────── │ (debug)   │
   └──────┬──────────┘            └───────────┘
          │ работает
          v
   ┌─────────────┐
   │ git commit  │ ──> следующий маленький шаг
   └─────────────┘
```

> **Правило:** не пишите код дольше 15 минут без запуска. Найти 1 ошибку после 10 строк гораздо проще, чем написать сразу 100 строк и потом искать 10 ошибок.

## 3. Пример проекта: `models.py`

Создаём классы по таблицам `category` и `operation` модели данных. Модуль `dataclasses` в Python автоматически пишет методы `__init__`, `__repr__` и `__eq__` — это избавляет от ручного написания магических методов.

```python
"""models.py — основные классы данных проекта."""
from dataclasses import dataclass
import datetime

KINDS = ("доход", "расход")


@dataclass
class Category:
    """Категория операции."""
    id: int
    title: str
    kind: str          # "доход" или "расход"


@dataclass
class Operation:
    """Одна денежная операция (доход или расход)."""
    amount: int
    category: Category
    op_date: datetime.date
    note: str = ""
    id: int | None = None   # присваивается при сохранении в БД

    def __post_init__(self):
        # вызывается автоматически после __init__: проверяем данные
        if not isinstance(self.amount, int) or self.amount <= 0:
            raise ValueError("Сумма должна быть целым положительным числом")
        if self.category.kind not in KINDS:
            raise ValueError(f"Неизвестный тип категории: {self.category.kind}")

    @property
    def signed_amount(self):
        """Расход — со знаком минус, доход — со знаком плюс."""
        return self.amount if self.category.kind == "доход" else -self.amount


if __name__ == "__main__":
    food = Category(1, "Еда", "расход")
    op = Operation(800, food, datetime.date(2027, 4, 15), "Обед")
    print(op.category.title, op.signed_amount)
    try:
        Operation(-5, food, datetime.date.today())
    except ValueError as err:
        print("Ошибка:", err)
```

Результат:
```text
Еда -800
Ошибка: Сумма должна быть целым положительным числом
```

`dataclass` не обязателен: можно написать `__init__` вручную. Главное — чтобы **данные были в одном месте и в одном формате**.

## 4. Пример проекта: `logic.py` — этап 1

На первом этапе пишем самые необходимые действия: добавление, баланс, сумма по категориям. Данные пока хранятся **в памяти** (в списке) — БД подключается на следующем этапе.

```python
"""logic.py — основная логика «Карманных денег» (этап 1)."""
from dataclasses import dataclass
import datetime


@dataclass
class Category:
    id: int
    title: str
    kind: str


@dataclass
class Operation:
    amount: int
    category: Category
    op_date: datetime.date
    note: str = ""

    @property
    def signed_amount(self):
        return self.amount if self.category.kind == "доход" else -self.amount


class Wallet:
    """Кошелёк пользователя: список операций и вычисления."""

    def __init__(self, owner):
        self.owner = owner
        self._operations = []

    def add(self, operation):
        """Добавляет операцию."""
        self._operations.append(operation)

    @property
    def operations(self):
        """Копия операций (чтобы снаружи нельзя было испортить список)."""
        return list(self._operations)

    def balance(self):
        """Текущий баланс: доходы минус расходы."""
        return sum(op.signed_amount for op in self._operations)

    def total_by_category(self, kind="расход"):
        """Сумма по категориям: {'Еда': 2300, ...}"""
        totals = {}
        for op in self._operations:
            if op.category.kind == kind:
                title = op.category.title
                totals[title] = totals.get(title, 0) + op.amount
        return totals


if __name__ == "__main__":
    salary = Category(1, "Родители", "доход")
    food = Category(2, "Еда", "расход")
    bus = Category(3, "Транспорт", "расход")

    w = Wallet("Даулет")
    day = datetime.date(2027, 4, 14)
    w.add(Operation(3000, salary, day))
    w.add(Operation(800, food, day, "Обед"))
    w.add(Operation(200, bus, day))
    w.add(Operation(500, food, day + datetime.timedelta(days=1)))

    print("Баланс:", w.balance(), "тг")
    print("Расходы:", w.total_by_category())
```

Результат:
```text
Баланс: 1500 тг
Расходы: {'Еда': 1300, 'Транспорт': 200}
```

Обратите внимание:
- Внутри `Wallet` нет ни одного `print` или `input` — он только **возвращает** значения. Вывод — обязанность интерфейса.
- Свойство `operations` возвращает копию списка — инкапсуляция.
- Чтобы сократить код, здесь `Category` и `Operation` написаны заново; в настоящем проекте импортируем: `from qalta.models import Category, Operation`.

## 5. Образцы логики для других типов проектов

### Викторина
```python
import random


class Question:
    def __init__(self, text, options, correct_index):
        self.text = text
        self.options = options
        self.correct_index = correct_index

    def check(self, answer_index):
        return answer_index == self.correct_index


class QuizGame:
    def __init__(self, questions, shuffle=True):
        self.questions = list(questions)
        if shuffle:
            random.shuffle(self.questions)
        self.current = 0
        self.score = 0

    def current_question(self):
        return self.questions[self.current] if not self.is_over() else None

    def answer(self, index):
        """Проверяет ответ и переходит к следующему вопросу. True, если верно."""
        ok = self.questions[self.current].check(index)
        if ok:
            self.score += 1
        self.current += 1
        return ok

    def is_over(self):
        return self.current >= len(self.questions)


q = [Question("Столица Казахстана?", ["Алматы", "Астана", "Шымкент"], 1),
     Question("2 + 2 * 2 = ?", ["8", "6", "4"], 1)]
game = QuizGame(q, shuffle=False)
game.answer(1)
game.answer(0)
print(f"Результат: {game.score}/{len(q)}, завершено: {game.is_over()}")
```
Результат:
```text
Результат: 1/2, завершено: True
```

### Игра (логика без Pygame)
```python
class Player:
    """Состояние игрока: не знает, как рисуется на экране."""

    def __init__(self, lives=3):
        self.lives = lives
        self.score = 0
        self.level = 1

    def hit(self):
        self.lives = max(0, self.lives - 1)

    def collect_coin(self, value=10):
        self.score += value
        if self.score >= self.level * 100:   # каждые 100 очков — новый уровень
            self.level += 1

    @property
    def is_alive(self):
        return self.lives > 0


p = Player()
for _ in range(12):
    p.collect_coin()
p.hit()
print(p.score, p.level, p.lives, p.is_alive)
```
Результат:
```text
120 2 2 True
```

Цикл Pygame потом будет лишь вызывать `player.hit()` или `player.collect_coin()`.

## 6. Как искать решение, если код не работает

**«Правило 15 минут»:** 15 минут ищите решение сами, потом просите помощи.

| Шаг | Что делаем |
|---|---|
| 1. Прочитать ошибку | **последняя строка** Traceback — тип и причина ошибки; номер строки выше — место |
| 2. `print` / отладчик | выведите и посмотрите значения переменных; `breakpoint()` останавливает программу в отладчике `pdb` |
| 3. «Метод утёнка» | объясните код вслух построчно (игрушечному утёнку, коллеге) — часто ошибку находите сами |
| 4. Документация | docs.python.org, официальная документация библиотеки |
| 5. Поиск | поиск текста ошибки на английском (Stack Overflow) |
| 6. Правильно задать вопрос | коллеге или на форуме: *что хочу сделать, что сделал, что ожидал, что получилось* + минимальный пример кода |

**Об ИИ-помощниках:** их можно использовать для объяснений и поиска ошибок, но автор должен понимать каждую строку своего проекта. Не вставляйте код, который не поняли; если пользовались помощью, укажите это в README.

## Частые ошибки

**1. `print`/`input` внутри логики.**
```python
class WalletBad:
    def __init__(self):
        self.ops = []

    def add(self):
        amount = int(input("Сумма: "))   # ошибка: логика не общается с пользователем
        self.ops.append(amount)
```
Объяснение: это невозможно подключить к Tkinter и протестировать. Правильно — принимать значение как параметр:
```python
class WalletGood:
    def __init__(self):
        self.ops = []

    def add(self, amount):
        self.ops.append(amount)
```

**2. «Проглатывание» ошибки.**
```python
def parse_amount(text):
    try:
        return int(text)
    except ValueError:
        return 0       # ошибка: сохранится «0 тенге», пользователь не узнает
```
Правильно: передать ошибку выше или выбросить её с понятным сообщением:
```python
def parse_amount(text):
    try:
        value = int(text)
    except ValueError:
        raise ValueError("Сумма должна быть числом") from None
    if value <= 0:
        raise ValueError("Сумма должна быть положительной")
    return value
```

**3. Общий список (на уровне класса).**
```python
class Wallet:
    operations = []        # ошибка: один список, общий для всех кошельков!

a, b = Wallet(), Wallet()
a.operations.append(100)
print(b.operations)        # [100] — добавилось и в b
```
Правильно: создавать список внутри `__init__`: `self.operations = []`.

**4. Написать всё сразу и ни разу не запустить.** Маленький шаг → запуск.

**5. Два часа работы без коммита.** Если компьютер зависнет — всё пропадёт. Коммит после каждого работающего шага.

## Основные понятия
| Понятие | Объяснение |
|---|---|
| Основная (бизнес-) логика | вычисления и правила; не зависят от интерфейса и способа хранения |
| Инкрементная разработка | наращивание программы маленькими проверенными шагами |
| Валидация | проверка правильности входных данных |
| `dataclass` | декоратор, автоматически создающий `__init__`, `__repr__`, `__eq__` |
| `__post_init__` | метод, вызываемый после создания объекта dataclass |
| Метод утёнка (rubber duck) | поиск ошибки путём объяснения кода вслух |

## Примечание

- Запись `int | None` в аннотациях работает с Python 3.10; в более старых версиях пишут `Optional[int]` из модуля `typing`.
- Изменяемые значения по умолчанию в `dataclass` задают через `field(default_factory=list)`: строка `items: list = []` вызовет `ValueError`.
- `@dataclass(frozen=True)` делает объект неизменяемым (и хешируемым) — удобно для значений вроде категории, которые не должны меняться после создания.
- Для денег, если нужны дробные значения, используйте `decimal.Decimal`, а не `float`.
- Одно подчёркивание (`self._operations`) — лишь соглашение PEP 8 о «внутреннем» атрибуте; Python не запрещает к нему обращаться.
- Документация: https://docs.python.org/3/library/dataclasses.html
