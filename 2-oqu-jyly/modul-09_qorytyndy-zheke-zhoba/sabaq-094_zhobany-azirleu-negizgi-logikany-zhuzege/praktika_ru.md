# Урок 94. Практические задания: основная логика (этап 1)

> Основное время сегодняшнего урока — **работа над собственным проектом**. Базовые задания ниже — это шаги этой работы. Тренировочные задания (4, 6, 8) — для учеников, которые хотят потренироваться в написании логики на учебном проекте или рано закончили свою задачу.

## Базовый уровень

### Задание 1. `models.py` (обязательно)
Напишите класс для каждой сущности вашей модели данных (урок 91):
- атрибуты соответствуют полям таблицы;
- есть `__repr__` (или `@dataclass`);
- хотя бы в одном классе есть валидация в `__init__`/`__post_init__` (`ValueError`).

В блоке `if __name__ == "__main__":` в конце файла создайте 2–3 объекта и выведите их на экран.

### Задание 2. Основные действия (обязательно)
По задачам урока 94 на вашей Kanban-доске напишите **3 самых важных действия** в файле `logic.py` (или `game/logic.py`, `quiz/engine.py`). Требования:
- методы возвращают значения, внутри нет `print`/`input`;
- у каждого метода однострочный docstring;
- блок проверки в консоли показывает результат.

### Задание 3. Коммиты и Kanban
В ходе урока сделайте не менее **3 коммитов** (после каждого работающего шага), указывая номер issue. В конце урока — `git push` и обновление Kanban-доски.

### Задание 4 (тренировочное). Логика «Школьной библиотеки»
Напишите класс `Book(title, author, copies)` и класс `Library`:
- `add_book(book)`;
- `give(title)` — если есть экземпляр, `copies` уменьшается на 1 и возвращается `True`; если нет — `False`;
- `give_back(title)` — количество экземпляров увеличивается на 1; если такой книги нет — `KeyError`;
- `available()` — список названий книг с количеством экземпляров > 0.

Пример:
```text
>>> lib.give("Путь Абая")
True
>>> lib.available()
['Путь Абая', 'Кочевники']
```

## Продвинутый уровень

### Задание 5. Список особых случаев
Ответьте для своей логики на вопрос «что может сломаться?» и запишите не менее 5 особых случаев (пустой список, деление на ноль, отрицательное число, повторяющееся название и т.д.). Каждый из них обработайте в коде или зарегистрируйте как issue на урок 95.

### Задание 6 (тренировочное). Месячный лимит
Добавьте в класс `Wallet` (образец из теории) методы:
- `set_limit(category_title, amount)` — месячный лимит для категории;
- `check_limits(year, month)` — возвращает словарь категорий, превысивших лимит: `{'Еда': 300}` (на сколько превышен).

### Задание 7. Выделение логики
Вынесите логику из кода «всё вместе» ниже в класс `Shop`; ввод/вывод должен остаться только в функции `main()`.

```python
items = {}
while True:
    cmd = input("Команда (add/total/exit): ")
    if cmd == "add":
        name = input("Товар: ")
        price = int(input("Цена: "))
        items[name] = items.get(name, 0) + price
        print("Добавлено")
    elif cmd == "total":
        print("Итого:", sum(items.values()))
    elif cmd == "exit":
        break
```

## ★ Олимпиадная / творческая задача

### Задание 8. Отмена (Undo)
Добавьте в класс `Wallet` метод `undo()`: он отменяет последнее действие (добавление или удаление). Его можно вызывать несколько раз подряд. *Подсказка:* храните историю действий как стек (список, `append`/`pop`).

### Задание 9. Генератор уровней игры
Для игрового проекта напишите функцию `make_level(n)`: на n-м уровне количество препятствий `3 + 2*n`, скорость `1.0 + 0.25*n` (не больше 5,0), x-координаты препятствий случайные в диапазоне 0–800, но не ближе 60 пикселей друг к другу. Используйте `random.seed(n)`, чтобы один и тот же уровень всегда был одинаковым.

## Домашнее задание
1. Завершите начатые сегодня задачи, сделайте `push`.
2. Просмотрите задачи урока 95 на Kanban и запишите самый сложный алгоритм на бумаге в виде блок-схемы/псевдокода.
3. Выберите одну функцию из своего кода и объясните её однокласснику (или кому-нибудь дома) «методом утёнка».

## Критерии оценивания
На проектных уроках (94–97) в конце каждого урока заполняется **карта прогресса**:

| Критерий | Доля | Что проверяется |
|---|---|---|
| Выполнение запланированных задач | 40% | сегодняшние issues закрыты / есть работающий код |
| Рациональность решения | 25% | логика отделена от интерфейса, есть валидация |
| Качество кода | 20% | PEP 8, понятные названия, docstring |
| Самостоятельная работа, регулярные коммиты | 15% | не менее 3 осмысленных коммитов, сделан `push` |

## Для педагога: решения
<details><summary>Показать решения</summary>

**Задания 1–3, 5** проверяются индивидуально. Каждому ученику 3 вопроса: «Что ты сегодня сделал? Где застрял? Какой твой следующий шаг?» — ответы запишите в карту прогресса.

**Задание 4.**
```python
class Book:
    def __init__(self, title, author, copies=1):
        self.title = title
        self.author = author
        self.copies = copies

    def __repr__(self):
        return f"Book({self.title!r}, {self.copies} экз.)"


class Library:
    def __init__(self):
        self._books = {}

    def add_book(self, book):
        """Добавляет книгу (если уже есть — увеличивает число экземпляров)."""
        if book.title in self._books:
            self._books[book.title].copies += book.copies
        else:
            self._books[book.title] = book

    def give(self, title):
        """Выдаёт книгу. True, если успешно."""
        book = self._books.get(title)
        if book is None or book.copies == 0:
            return False
        book.copies -= 1
        return True

    def give_back(self, title):
        """Возвращает книгу."""
        if title not in self._books:
            raise KeyError(f"В библиотеке нет книги '{title}'")
        self._books[title].copies += 1

    def available(self):
        """Названия имеющихся в наличии книг."""
        return [t for t, b in self._books.items() if b.copies > 0]


lib = Library()
lib.add_book(Book("Путь Абая", "М. Ауэзов", 2))
lib.add_book(Book("Кочевники", "И. Есенберлин", 1))
print(lib.give("Путь Абая"))
print(lib.available())
print(lib.give("Кочевники"), lib.give("Кочевники"))
print(lib.available())
```
Результат:
```text
True
['Путь Абая', 'Кочевники']
True False
['Путь Абая']
```

**Задание 6.**
```python
import datetime


class Category:
    def __init__(self, title, kind):
        self.title = title
        self.kind = kind


class Operation:
    def __init__(self, amount, category, op_date):
        self.amount = amount
        self.category = category
        self.op_date = op_date


class Wallet:
    def __init__(self):
        self._operations = []
        self._limits = {}

    def add(self, op):
        self._operations.append(op)

    def set_limit(self, category_title, amount):
        """Устанавливает месячный лимит для категории."""
        if amount <= 0:
            raise ValueError("Лимит должен быть положительным числом")
        self._limits[category_title] = amount

    def spent_in_month(self, year, month):
        totals = {}
        for op in self._operations:
            d = op.op_date
            if op.category.kind == "расход" and d.year == year and d.month == month:
                totals[op.category.title] = totals.get(op.category.title, 0) + op.amount
        return totals

    def check_limits(self, year, month):
        """Категории, превысившие лимит: {категория: сумма превышения}."""
        spent = self.spent_in_month(year, month)
        return {title: spent[title] - limit
                for title, limit in self._limits.items()
                if spent.get(title, 0) > limit}


food = Category("Еда", "расход")
w = Wallet()
w.add(Operation(1500, food, datetime.date(2027, 4, 1)))
w.add(Operation(1800, food, datetime.date(2027, 4, 10)))
w.add(Operation(900, food, datetime.date(2027, 5, 1)))
w.set_limit("Еда", 3000)
print(w.check_limits(2027, 4))
print(w.check_limits(2027, 5))
```
Результат:
```text
{'Еда': 300}
{}
```

**Задание 7.**
```python
class Shop:
    """Логика корзины: ничего не знает о вводе/выводе."""

    def __init__(self):
        self.items = {}

    def add(self, name, price):
        if price < 0:
            raise ValueError("Цена не может быть отрицательной")
        self.items[name] = self.items.get(name, 0) + price

    def total(self):
        return sum(self.items.values())


def main():
    shop = Shop()
    while True:
        cmd = input("Команда (add/total/exit): ")
        if cmd == "add":
            name = input("Товар: ")
            try:
                shop.add(name, int(input("Цена: ")))
                print("Добавлено")
            except ValueError as err:
                print("Ошибка:", err)
        elif cmd == "total":
            print("Итого:", shop.total())
        elif cmd == "exit":
            break


if __name__ == "__main__":
    main()
```
(`int("абв")` тоже вызывает `ValueError` — его сообщение будет на английском; можно обсудить, как заменить и его на русское.)

**Задание 8.**
```python
class Wallet:
    def __init__(self):
        self._operations = []
        self._history = []          # ("add", op) или ("remove", op, index)

    def add(self, op):
        self._operations.append(op)
        self._history.append(("add", op))

    def remove(self, index):
        op = self._operations.pop(index)
        self._history.append(("remove", op, index))

    def undo(self):
        """Отменяет последнее действие. False, если история пуста."""
        if not self._history:
            return False
        action = self._history.pop()
        if action[0] == "add":
            self._operations.remove(action[1])
        else:
            self._operations.insert(action[2], action[1])
        return True

    def __len__(self):
        return len(self._operations)


w = Wallet()
w.add(100)
w.add(200)
w.remove(0)
print(len(w))     # 1
w.undo()          # отмена удаления
print(len(w))     # 2
w.undo()
w.undo()
print(len(w), w.undo())   # 0 False
```
Результат:
```text
1
2
0 False
```

**Задание 9.**
```python
import random


def make_level(n, width=800, gap=60):
    """Препятствия и скорость n-го уровня."""
    rnd = random.Random(n)            # генератор только для этого уровня
    count = 3 + 2 * n
    speed = min(5.0, 1.0 + 0.25 * n)
    xs = []
    attempts = 0
    while len(xs) < count and attempts < 10000:
        attempts += 1
        x = rnd.randint(0, width)
        if all(abs(x - other) >= gap for other in xs):
            xs.append(x)
    return {"level": n, "speed": speed, "obstacles": sorted(xs)}


print(make_level(1))
print(make_level(1) == make_level(1))
```
Примечание: если препятствий больше, чем `width / gap` ≈ 13, разместить их все невозможно — ограничение `attempts` защищает от бесконечного цикла; обсудите этот случай с учеником. (Вместо `random.seed(n)` использован отдельный генератор `random.Random(n)` — он не влияет на другие случайные части игры.)
</details>
