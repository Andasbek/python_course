# Сабақ 13. Практикалық тапсырмалар: инкапсуляция, ашық және жабық атрибуттар

## Базалық деңгей

### 1-тапсырма. Зерттеу: қатынау деңгейлері
Төмендегі кодты іске қос. Әр `print` жолының нәтижесін алдын ала болжап жаз, кейін тексер. Соңғы жол `AttributeError` береді деп күтуге болады — шын мәнінде не болады және неге? Соңында `vars(car)` нені көрсетеді?

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

### 2-тапсырма. `Student` — қорғалған жас пен бағалар
`Student` класын жаз:
- жабық атрибуттар: `__name`, `__age`, `__marks`;
- `get_name()`, `get_age()`, `get_marks()` (көшірме қайтарады);
- `set_age(age)` — тек 6–18 аралығы, әйтпесе ескерту және `False`;
- `add_mark(mark)` — тек 1–5;
- `average()`.

```text
s = Student("Әлия", 14)
s.set_age(45)       → Қате жас: 45
s.add_mark(7)       → Қате баға: 7
s.get_marks().append(2)  → ішкі тізім өзгермейді
s.get_marks()       → [5, 4]
```

### 3-тапсырма. `Thermometer` — тек оқуға арналған
`Thermometer` класында `__celsius` жабық атрибуты бар. Әдістері: `get_celsius()`, `get_fahrenheit()`, `get_kelvin()`, `set_celsius(value)` — абсолюттік нөлден (−273.15) төмен мәнді қабылдамайды. Фаренгейт пен Кельвинге **сеттер жоқ**.

### 4-тапсырма. Қорғауды тексер
Теориядағы `BankAccount` класын (геттер/сеттерлермен) көшіріп ал және оны «бұзуға» тырыс: теріс сома салу, баланстан көп шешу, `acc.__balance = 10**9`, тарихты `get_history()` арқылы тазалау. Әр әрекеттің нәтижесін кестеге жаз: «Бұзылды / Қорғалды», себебі.

## Кеңейтілген деңгей

### 5-тапсырма. `GameHero` — инкапсуляцияланған кейіпкер
- Жабық: `__hp` (0–100), `__xp`, `__level`.
- Ашық: `name`.
- Геттерлер: `get_hp()`, `get_level()`.
- Әрекеттер: `take_damage(n)`, `heal(n)` (өлі кейіпкерді емдеуге болмайды), `add_xp(n)` (әр 100 xp → +1 деңгей).
- Жабық көмекші әдіс `__check_level_up()`.
- `is_alive()`.

Ойыншы `hero.__hp = 1000` деп «чит» жасай алмайтынын көрсет.

### 6-тапсырма. `PinCard` — PIN-коды бар карта
- `__pin` (4 цифр, конструкторда тексеріледі), `__balance`, `__attempts`.
- `withdraw(amount, pin)` — PIN дұрыс болса ғана; 3 қате әрекеттен кейін карта бұғатталады (`__blocked = True`), кейін ешқандай операция жүрмейді.
- `change_pin(old, new)` — ескі PIN дұрыс және жаңасы 4 цифр болса.
- PIN-ге геттер **жоқ**.

```text
card.withdraw(5000, "1111") → Қате PIN (қалған әрекет: 2)
card.withdraw(5000, "1111") → Қате PIN (қалған әрекет: 1)
card.withdraw(5000, "1111") → Карта бұғатталды!
card.withdraw(5000, "4321") → Карта бұғатталған
```

### 7-тапсырма. Ішкі құрылымды өзгерту
`Inventory` (ойын инвентары) класы алдымен заттарды **тізімде** сақтайды: `add_item(name)`, `remove_item(name)`, `count(name)`, `get_items()`. Оны жазып, тексеретін бағдарлама жаз. Кейін ішкі құрылымды **сөздікке** `{зат: саны}` ауыстыр — тексеретін бағдарлама **бір жол да өзгермей** жұмыс істеуі керек. Бұл инкапсуляцияның қандай артықшылығын көрсетеді?

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. `Matrix` — өлшемі өзгермейтін матрица
`Matrix(rows, cols)` — ішінде `__data` (тізімдер тізімі). Әдістері: `get(i, j)`, `set(i, j, value)` (индексті тексереді), `get_size()` (кортеж), `transpose()` (жаңа матрица қайтарады), `multiply(other)` (өлшемдер сәйкес келмесе — `None`), `to_list()` (терең көшірме). Сыртқы код матрицаның өлшемін өзгерте алмауы керек.

### 9-тапсырма. «Құпия хабар» сейфі
`MessageVault` класы хабарламаларды Цезарь шифрымен **шифрланған** күйде сақтайды (жабық `__messages`, жабық `__shift`). `add(text)`, `read(index, password)` — пароль дұрыс болса ғана ашық мәтінді қайтарады, `count()`. Парольдің хэшін ғана сақта (`hashlib.sha256`). Қазақ әліпбиіндегі әріптерді ескер.

## Үй тапсырмасы

1. 2-тапсырмадағы `Student` класын 12-сабақтағы `Classroom` контейнерімен біріктір: `Classroom` оқушылардың бағаларына тек `Student` әдістері арқылы қатынасын.
2. Өз жоба кластарыңды қарап шық: қай атрибуттар жабық болуы керек? Кемінде бір класқа инкапсуляция қос, push жаса.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма** — нәтижелер: `Toyota`; `SRV-17` (жұмыс істейді, бірақ жаман стиль); `120000`; `120000` (name mangling арқылы «бұзу»); `car.__mileage = 5` жаңа атрибут құрады, сондықтан `get_mileage()` → `120000`; соңғы `print(car.__mileage)` → `5` (класс сыртында жазылған атау бұрмаланбайды, жаңа атрибутты оқиды). `vars(car)` → `{'brand': 'Toyota', '_service_code': 'SRV-17', '_Car__mileage': 120000, '__mileage': 5}`.

**2-тапсырма**
```python
class Student:
    """Инкапсуляцияланған оқушы."""
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
            print(f"Қате жас: {age}")
            return False
        self.__age = age
        return True

    def add_mark(self, mark):
        if not 1 <= mark <= 5:
            print(f"Қате баға: {mark}")
            return False
        self.__marks.append(mark)
        return True

    def average(self):
        return sum(self.__marks) / len(self.__marks) if self.__marks else 0


s = Student("Әлия", 14)
s.set_age(45)
s.add_mark(5)
s.add_mark(4)
s.add_mark(7)
s.get_marks().append(2)
print(s.get_name(), s.get_age(), s.get_marks(), s.average())
```

**3-тапсырма**
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
            print("Абсолюттік нөлден төмен температура болмайды")
            return False
        self.__celsius = value
        return True


t = Thermometer(25)
print(t.get_celsius(), t.get_fahrenheit(), round(t.get_kelvin(), 2))
t.set_celsius(-300)
```

**4-тапсырма** — барлық әрекет қорғалады: теріс сома — `deposit` тексерісі; артық шешу — `withdraw` тексерісі; `acc.__balance = 10**9` — жаңа ашық атрибут, `get_balance()` өзгермейді; `get_history().clear()` — көшірме тазаланады, ішкі тарих сақталады. Тек `acc._BankAccount__balance = ...` арқылы әдейі бұзуға болады (Python-дағы жабықтық — келісім).

**5-тапсырма**
```python
class GameHero:
    """Инкапсуляцияланған ойын кейіпкері."""
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
            print(f"{self.name} өлі — емдеу мүмкін емес")
            return
        self.__hp = min(self.MAX_HP, self.__hp + n)

    def add_xp(self, n):
        if n > 0:
            self.__xp += n
            self.__check_level_up()

    def __check_level_up(self):
        new_level = 1 + self.__xp // self.XP_PER_LEVEL
        if new_level > self.__level:
            print(f"{self.name}: жаңа деңгей {new_level}!")
            self.__level = new_level


hero = GameHero("Батыр")
hero.take_damage(30)
hero.heal(50)
hero.add_xp(250)
hero.__hp = 1000                 # «чит» — жаңа атрибут, ішкі HP өзгермейді
print(hero.get_hp(), hero.get_level())
```

**6-тапсырма**
```python
class PinCard:
    """PIN-коды бар банк картасы."""
    MAX_ATTEMPTS = 3

    def __init__(self, pin, balance=0):
        if not self.__valid_pin(pin):
            raise ValueError("PIN 4 цифрдан тұруы керек")
        self.__pin = pin
        self.__balance = balance
        self.__attempts = 0
        self.__blocked = False

    @staticmethod
    def __valid_pin(pin):
        return isinstance(pin, str) and len(pin) == 4 and pin.isdigit()

    def __check(self, pin):
        if self.__blocked:
            print("Карта бұғатталған")
            return False
        if pin == self.__pin:
            self.__attempts = 0
            return True
        self.__attempts += 1
        left = self.MAX_ATTEMPTS - self.__attempts
        if left == 0:
            self.__blocked = True
            print("Карта бұғатталды!")
        else:
            print(f"Қате PIN (қалған әрекет: {left})")
        return False

    def get_balance(self, pin):
        return self.__balance if self.__check(pin) else None

    def withdraw(self, amount, pin):
        if not self.__check(pin):
            return False
        if not 0 < amount <= self.__balance:
            print("Қаражат жеткіліксіз")
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

**7-тапсырма**
```python
class Inventory:
    """Ойын инвентары (ішінде — сөздік)."""

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


# Тексеретін бағдарлама — тізім нұсқасымен де, сөздік нұсқасымен де бірдей
bag = Inventory()
for item in ["қылыш", "алма", "алма", "қалқан"]:
    bag.add_item(item)
bag.remove_item("алма")
print(bag.count("алма"), sorted(bag.get_items()))
```
Тізім нұсқасы: `self.__items = []`, `append`, `remove`, `count` → `self.__items.count(name)`, `get_items` → `list(self.__items)`. Сыртқы код тек ашық әдістерді қолданатындықтан, ішкі құрылымды еркін ауыстыруға болады.

**8-тапсырма**
```python
import copy


class Matrix:
    """Өлшемі тұрақты матрица."""

    def __init__(self, rows, cols, fill=0):
        self.__rows = rows
        self.__cols = cols
        self.__data = [[fill] * cols for _ in range(rows)]

    def __check(self, i, j):
        if not (0 <= i < self.__rows and 0 <= j < self.__cols):
            raise IndexError("Индекс матрицадан тыс")

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

**9-тапсырма**
```python
import hashlib

ALPHABET = "абвгғдеёжзийкқлмнңоөпрстуұүфхһцчшщъыіьэюя"


class MessageVault:
    """Шифрланған хабарламалар сейфі."""

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
            print("Пароль қате")
            return None
        return self.__caesar(self.__messages[index], -self.__shift)

    def count(self):
        return len(self.__messages)


vault = MessageVault("құпия123")
vault.add("Ертең сағат 10-да үйірмеде кездесеміз")
print(vault.read(0, "қате"))
print(vault.read(0, "құпия123"))
```

</details>
