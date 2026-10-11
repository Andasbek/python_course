# Урок 13. Практические задания: инкапсуляция, открытые и закрытые атрибуты

## Базовый уровень

### Задание 1. Исследование: уровни доступа
Запусти код ниже. Заранее запиши предполагаемый результат каждой строки `print`, затем проверь. Можно ожидать, что последняя строка выдаст `AttributeError`, — что происходит на самом деле и почему? Что в конце покажет `vars(car)`?

```python
class Car:
    def __init__(self, brand, mileage):
        self.brand = brand
        self._service_code = "SRV-17"
        self.__mileage = mileage

    def get_mileage(self):
        return self.__mileage


car = Car("Toyota", 120000)
print(car.brand)
print(car._service_code)
print(car.get_mileage())
print(car._Car__mileage)
car.__mileage = 5
print(car.get_mileage())
print(car.__mileage)
```

### Задание 2. `Student` — защищённые возраст и оценки
Напиши класс `Student`:
- закрытые атрибуты: `__name`, `__age`, `__marks`;
- `get_name()`, `get_age()`, `get_marks()` (возвращает копию);
- `set_age(age)` — только от 6 до 18, иначе предупреждение и `False`;
- `add_mark(mark)` — только от 1 до 5;
- `average()`.

```text
s = Student("Алия", 14)
s.set_age(45)       → Неверный возраст: 45
s.add_mark(7)       → Неверная оценка: 7
s.get_marks().append(2)  → внутренний список не меняется
s.get_marks()       → [5, 4]
```

### Задание 3. `Thermometer` — только для чтения
В классе `Thermometer` есть закрытый атрибут `__celsius`. Методы: `get_celsius()`, `get_fahrenheit()`, `get_kelvin()`, `set_celsius(value)` — не принимает значения ниже абсолютного нуля (−273.15). Для Фаренгейта и Кельвина **сеттеров нет**.

### Задание 4. Проверь защиту
Скопируй класс `BankAccount` из теории (с геттерами/сеттерами) и попробуй его «взломать»: внести отрицательную сумму, снять больше баланса, `acc.__balance = 10**9`, очистить историю через `get_history()`. Запиши результат каждого действия в таблицу: «Взломано / Защищено», причина.

## Продвинутый уровень

### Задание 5. `GameHero` — инкапсулированный персонаж
- Закрытые: `__hp` (0–100), `__xp`, `__level`.
- Открытый: `name`.
- Геттеры: `get_hp()`, `get_level()`.
- Действия: `take_damage(n)`, `heal(n)` (мёртвого персонажа лечить нельзя), `add_xp(n)` (каждые 100 xp → +1 уровень).
- Закрытый вспомогательный метод `__check_level_up()`.
- `is_alive()`.

Покажи, что игрок не может сжульничать, написав `hero.__hp = 1000`.

### Задание 6. `PinCard` — карта с PIN-кодом
- `__pin` (4 цифры, проверяется в конструкторе), `__balance`, `__attempts`.
- `withdraw(amount, pin)` — только при правильном PIN; после 3 неверных попыток карта блокируется (`__blocked = True`), после этого никакие операции не проходят.
- `change_pin(old, new)` — если старый PIN верный, а новый состоит из 4 цифр.
- Геттера для PIN **нет**.

```text
card.withdraw(5000, "1111") → Неверный PIN (осталось попыток: 2)
card.withdraw(5000, "1111") → Неверный PIN (осталось попыток: 1)
card.withdraw(5000, "1111") → Карта заблокирована!
card.withdraw(5000, "4321") → Карта заблокирована
```

### Задание 7. Изменение внутреннего устройства
Класс `Inventory` (игровой инвентарь) сначала хранит предметы в **списке**: `add_item(name)`, `remove_item(name)`, `count(name)`, `get_items()`. Напиши его и программу для проверки. Затем замени внутреннее устройство на **словарь** `{предмет: количество}` — программа проверки должна работать, **не изменив ни одной строки**. Какое преимущество инкапсуляции это показывает?

## ★ Олимпиадная / творческая задача

### Задание 8. `Matrix` — матрица неизменяемого размера
`Matrix(rows, cols)` — внутри `__data` (список списков). Методы: `get(i, j)`, `set(i, j, value)` (проверяет индексы), `get_size()` (кортеж), `transpose()` (возвращает новую матрицу), `multiply(other)` (если размеры не согласованы — `None`), `to_list()` (глубокая копия). Внешний код не должен иметь возможности изменить размер матрицы.

### Задание 9. Сейф «Секретное сообщение»
Класс `MessageVault` хранит сообщения **зашифрованными** шифром Цезаря (закрытые `__messages`, `__shift`). `add(text)`, `read(index, password)` — возвращает открытый текст, только если пароль верный, `count()`. Храни только хеш пароля (`hashlib.sha256`). Учитывай буквы русского и казахского алфавитов.

## Домашнее задание

1. Объедини класс `Student` из задания 2 с контейнером `Classroom` из 12-го урока: `Classroom` должен обращаться к оценкам учеников только через методы `Student`.
2. Просмотри классы своего проекта: какие атрибуты должны быть закрытыми? Добавь инкапсуляцию хотя бы в один класс, сделай push.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1** — результаты: `Toyota`; `SRV-17` (работает, но плохой стиль); `120000`; `120000` («взлом» через name mangling); `car.__mileage = 5` создаёт новый атрибут, поэтому `get_mileage()` → `120000`; последний `print(car.__mileage)` → `5` (имя, записанное вне класса, не искажается — читается новый атрибут). `vars(car)` → `{'brand': 'Toyota', '_service_code': 'SRV-17', '_Car__mileage': 120000, '__mileage': 5}`.

**Задание 2**
```python
class Student:
    """Инкапсулированный ученик."""
    MIN_AGE = 6
    MAX_AGE = 18

    def __init__(self, name, age):
        self.__name = name
        self.__age = self.MIN_AGE
        self.__marks = []
        self.set_age(age)

    def get_name(self):
        return self.__name

    def get_age(self):
        return self.__age

    def get_marks(self):
        return list(self.__marks)

    def set_age(self, age):
        if not self.MIN_AGE <= age <= self.MAX_AGE:
            print(f"Неверный возраст: {age}")
            return False
        self.__age = age
        return True

    def add_mark(self, mark):
        if not 1 <= mark <= 5:
            print(f"Неверная оценка: {mark}")
            return False
        self.__marks.append(mark)
        return True

    def average(self):
        return sum(self.__marks) / len(self.__marks) if self.__marks else 0


s = Student("Алия", 14)
s.set_age(45)
s.add_mark(5)
s.add_mark(4)
s.add_mark(7)
s.get_marks().append(2)
print(s.get_name(), s.get_age(), s.get_marks(), s.average())
```

**Задание 3**
```python
class Thermometer:
    """Термометр."""
    ABSOLUTE_ZERO = -273.15

    def __init__(self, celsius=0):
        self.__celsius = 0
        self.set_celsius(celsius)

    def get_celsius(self):
        return self.__celsius

    def get_fahrenheit(self):
        return self.__celsius * 9 / 5 + 32

    def get_kelvin(self):
        return self.__celsius - self.ABSOLUTE_ZERO

    def set_celsius(self, value):
        if value < self.ABSOLUTE_ZERO:
            print("Температура не может быть ниже абсолютного нуля")
            return False
        self.__celsius = value
        return True


t = Thermometer(25)
print(t.get_celsius(), t.get_fahrenheit(), round(t.get_kelvin(), 2))
t.set_celsius(-300)
```

**Задание 4** — все действия отражены: отрицательная сумма — проверка в `deposit`; снятие лишнего — проверка в `withdraw`; `acc.__balance = 10**9` — новый открытый атрибут, `get_balance()` не меняется; `get_history().clear()` — очищается копия, внутренняя история сохраняется. Намеренно взломать можно только через `acc._BankAccount__balance = ...` (закрытость в Python — это соглашение).

**Задание 5**
```python
class GameHero:
    """Инкапсулированный игровой персонаж."""
    MAX_HP = 100
    XP_PER_LEVEL = 100

    def __init__(self, name):
        self.name = name
        self.__hp = self.MAX_HP
        self.__xp = 0
        self.__level = 1

    def get_hp(self):
        return self.__hp

    def get_level(self):
        return self.__level

    def is_alive(self):
        return self.__hp > 0

    def take_damage(self, n):
        if n > 0:
            self.__hp = max(0, self.__hp - n)

    def heal(self, n):
        if not self.is_alive():
            print(f"{self.name} мёртв — лечение невозможно")
            return
        self.__hp = min(self.MAX_HP, self.__hp + n)

    def add_xp(self, n):
        if n > 0:
            self.__xp += n
            self.__check_level_up()

    def __check_level_up(self):
        new_level = 1 + self.__xp // self.XP_PER_LEVEL
        if new_level > self.__level:
            print(f"{self.name}: новый уровень {new_level}!")
            self.__level = new_level


hero = GameHero("Батыр")
hero.take_damage(30)
hero.heal(50)
hero.add_xp(250)
hero.__hp = 1000                 # «чит» — новый атрибут, внутренний HP не меняется
print(hero.get_hp(), hero.get_level())
```

**Задание 6**
```python
class PinCard:
    """Банковская карта с PIN-кодом."""
    MAX_ATTEMPTS = 3

    def __init__(self, pin, balance=0):
        if not self.__valid_pin(pin):
            raise ValueError("PIN должен состоять из 4 цифр")
        self.__pin = pin
        self.__balance = balance
        self.__attempts = 0
        self.__blocked = False

    @staticmethod
    def __valid_pin(pin):
        return isinstance(pin, str) and len(pin) == 4 and pin.isdigit()

    def __check(self, pin):
        if self.__blocked:
            print("Карта заблокирована")
            return False
        if pin == self.__pin:
            self.__attempts = 0
            return True
        self.__attempts += 1
        left = self.MAX_ATTEMPTS - self.__attempts
        if left == 0:
            self.__blocked = True
            print("Карта заблокирована!")
        else:
            print(f"Неверный PIN (осталось попыток: {left})")
        return False

    def get_balance(self, pin):
        return self.__balance if self.__check(pin) else None

    def withdraw(self, amount, pin):
        if not self.__check(pin):
            return False
        if not 0 < amount <= self.__balance:
            print("Недостаточно средств")
            return False
        self.__balance -= amount
        return True

    def change_pin(self, old, new):
        if self.__check(old) and self.__valid_pin(new):
            self.__pin = new
            return True
        return False


card = PinCard("4321", 20000)
for _ in range(3):
    card.withdraw(5000, "1111")
card.withdraw(5000, "4321")
```

**Задание 7**
```python
class Inventory:
    """Игровой инвентарь (внутри — словарь)."""

    def __init__(self):
        self.__items = {}

    def add_item(self, name):
        self.__items[name] = self.__items.get(name, 0) + 1

    def remove_item(self, name):
        if self.__items.get(name, 0) == 0:
            return False
        self.__items[name] -= 1
        if self.__items[name] == 0:
            del self.__items[name]
        return True

    def count(self, name):
        return self.__items.get(name, 0)

    def get_items(self):
        result = []
        for name, qty in self.__items.items():
            result += [name] * qty
        return result


# Программа проверки — одинакова и для версии со списком, и для версии со словарём
bag = Inventory()
for item in ["меч", "яблоко", "яблоко", "щит"]:
    bag.add_item(item)
bag.remove_item("яблоко")
print(bag.count("яблоко"), sorted(bag.get_items()))
```
Версия со списком: `self.__items = []`, `append`, `remove`, `count` → `self.__items.count(name)`, `get_items` → `list(self.__items)`. Поскольку внешний код использует только открытые методы, внутреннее устройство можно свободно менять.

**Задание 8**
```python
import copy


class Matrix:
    """Матрица фиксированного размера."""

    def __init__(self, rows, cols, fill=0):
        self.__rows = rows
        self.__cols = cols
        self.__data = [[fill] * cols for _ in range(rows)]

    def __check(self, i, j):
        if not (0 <= i < self.__rows and 0 <= j < self.__cols):
            raise IndexError("Индекс за пределами матрицы")

    def get(self, i, j):
        self.__check(i, j)
        return self.__data[i][j]

    def set(self, i, j, value):
        self.__check(i, j)
        self.__data[i][j] = value

    def get_size(self):
        return (self.__rows, self.__cols)

    def transpose(self):
        result = Matrix(self.__cols, self.__rows)
        for i in range(self.__rows):
            for j in range(self.__cols):
                result.set(j, i, self.__data[i][j])
        return result

    def multiply(self, other):
        rows2, cols2 = other.get_size()
        if self.__cols != rows2:
            return None
        result = Matrix(self.__rows, cols2)
        for i in range(self.__rows):
            for j in range(cols2):
                total = sum(self.__data[i][k] * other.get(k, j) for k in range(self.__cols))
                result.set(i, j, total)
        return result

    def to_list(self):
        return copy.deepcopy(self.__data)


a = Matrix(2, 3)
for i in range(2):
    for j in range(3):
        a.set(i, j, i + j)
print(a.to_list(), a.transpose().to_list())
print(a.multiply(a.transpose()).to_list())
```

**Задание 9**
```python
import hashlib

ALPHABET = "абвгғдеёжзийкқлмнңоөпрстуұүфхһцчшщъыіьэюя"


class MessageVault:
    """Сейф зашифрованных сообщений."""

    def __init__(self, password, shift=3):
        self.__password_hash = self.__hash(password)
        self.__shift = shift
        self.__messages = []

    @staticmethod
    def __hash(text):
        return hashlib.sha256(text.encode("utf-8")).hexdigest()

    def __caesar(self, text, shift):
        result = []
        for ch in text:
            lower = ch.lower()
            if lower in ALPHABET:
                new = ALPHABET[(ALPHABET.index(lower) + shift) % len(ALPHABET)]
                result.append(new.upper() if ch.isupper() else new)
            else:
                result.append(ch)
        return "".join(result)

    def add(self, text):
        self.__messages.append(self.__caesar(text, self.__shift))

    def read(self, index, password):
        if self.__hash(password) != self.__password_hash:
            print("Неверный пароль")
            return None
        return self.__caesar(self.__messages[index], -self.__shift)

    def count(self):
        return len(self.__messages)


vault = MessageVault("секрет123")
vault.add("Завтра в 10 часов встречаемся в кружке")
print(vault.read(0, "неверно"))
print(vault.read(0, "секрет123"))
```

</details>
