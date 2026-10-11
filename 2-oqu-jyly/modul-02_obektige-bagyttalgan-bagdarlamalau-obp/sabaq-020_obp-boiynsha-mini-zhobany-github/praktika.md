# Сабақ 20. Практикалық тапсырмалар: мини-жоба қорғау және Өзіндік жұмыс №1

## Базалық деңгей: репозиторийді дайындау (чек-парақ)

### 1-тапсырма. Репозиторий чек-парағы
Жобаңызды жүктемес бұрын әр пунктті тексеріп, белгі қойыңыз:

| № | Пункт | ✓ |
|---|---|---|
| 1 | Репозиторий атауы түсінікті (`school-journal`, `library-oop`) | |
| 2 | `README.md`: сипаттама, мүмкіндіктер, кластар, ОБП принциптері, іске қосу, автор | |
| 3 | `.gitignore`: `__pycache__/`, `venv/`, `.vscode/` | |
| 4 | Кластар бөлек файлда (`models.py`), кіру нүктесі — `main.py` | |
| 5 | `if __name__ == "__main__":` қолданылған | |
| 6 | Кемінде 3 мағыналы commit | |
| 7 | Код PEP 8 бойынша (бос жолдар, атаулар, шегіністер) | |
| 8 | Кемінде бір скриншот немесе шығыс мысалы README-де | |

### 2-тапсырма. Жүктеу
`git status` → `git add .` → `git commit -m "..."` → `git push`. GitHub-та репозиторийді ашып, README дұрыс көрсетілгенін тексеріңіз. Сілтемені педагогке жіберіңіз.

### 3-тапсырма. Жобаны қорғау
3–4 минуттық таныстыру (құрылымы теорияның 6-бөлімінде). Тыңдаушылар бағалау парағын толтырады (төмендегі кесте).

## Өзіндік жұмыс №1 (40 минут)

### 1-нұсқа

**А бөлімі. Теория (әр сұрақ — 2 ұпай, барлығы 10)**
1. Класс пен объектінің айырмашылығы неде? Мысал келтіріңіз.
2. `self` параметрі нені білдіреді?
3. `_balance` және `__balance` атрибуттарының айырмашылығы қандай?
4. `super()` функциясы не үшін қолданылады?
5. `__str__` әдісі қашан шақырылады және не қайтаруы тиіс?

**В бөлімі. Кодты оқу (10 ұпай).** Бағдарлама не шығарады? Түсіндіріңіз.
```python
class Animal:
    def __init__(self, name):
        self.name = name

    def voice(self):
        return "..."

    def __str__(self):
        return f"{self.name} дейді: {self.voice()}"


class Sheep(Animal):
    def voice(self):
        return "Мә-ә"


class Lamb(Sheep):
    def voice(self):
        return super().voice() + "!"


for a in [Animal("Тас"), Sheep("Қошқар"), Lamb("Қозы")]:
    print(a)
```

**С бөлімі. Бағдарламалау (1-тапсырма — 15 ұпай, 2-тапсырма — 15 ұпай)**

1. `BankAccount(owner, balance=0)` класын жазыңыз: `balance` — тек оқуға болатын property; `deposit(amount)` (оң сан ғана), `withdraw(amount)` (қалдық жеткілікті болса ғана, әйтпесе хабарлама); `__str__` — «Иесі: ..., қалдық: ... тг».
2. `Shape` ата-класы (`area()` → 0) және одан `Rectangle(a, b)`, `Circle(r)` ұрпақтарын жазыңыз. Фигуралар тізімін құрып, полиморфизм арқылы әрқайсысының ауданын және ауданы ең үлкен фигураны шығарыңыз.

### 2-нұсқа

**А бөлімі. Теория (әр сұрақ — 2 ұпай, барлығы 10)**
1. Конструктор (`__init__`) дегеніміз не және ол қашан шақырылады?
2. Инкапсуляция дегеніміз не? Ол не үшін керек?
3. Мұрагерлік дегеніміз не? Python-да қалай жазылады?
4. Полиморфизм дегеніміз не? Мысал келтіріңіз.
5. `__eq__` әдісі жоқ болса, `obj1 == obj2` қалай салыстырады?

**В бөлімі. Кодты оқу (10 ұпай).** Бағдарлама не шығарады? Түсіндіріңіз.
```python
class Counter:
    total = 0

    def __init__(self, name):
        self.name = name
        self.items = []
        Counter.total += 1

    def add(self, x):
        self.items.append(x)
        return self

    def __len__(self):
        return len(self.items)


a = Counter("A")
b = Counter("B")
a.add(1).add(2).add(3)
b.add(10)
print(len(a), len(b), Counter.total)
print(a.name if len(a) > len(b) else b.name)
```

**С бөлімі. Бағдарламалау (1-тапсырма — 15 ұпай, 2-тапсырма — 15 ұпай)**

1. `Student(name)` класын жазыңыз: жабық `_grades` тізімі; `add_grade(g)` (тек 1–10 аралығы); `average` — property (бағалар жоқ болса 0); `__str__` — «Аты: ..., орташа: ...»; `__eq__` — орташа бағалары тең болса тең.
2. `Employee(name, salary)` ата-класы және `Manager` ұрпағы (`bonus` атрибуты `super().__init__` арқылы). `total_income()` әдісін Manager-де қайта анықтаңыз (`salary + bonus`). Қызметкерлер тізімінен жалпы табысты есептеңіз.

## Кеңейтілген деңгей

### 4-тапсырма. Код-ревью
Көршіңіздің репозиторийін ашып, GitHub-та **Issue** құрыңыз: 2 жақсы жағы + 1 жетілдіру ұсынысы.

### 5-тапсырма. README-ге класс сызбасын қосу
README-ге мәтіндік UML сызбасын (```text блогында) немесе сызба суретін қосыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 6-тапсырма. GitHub профилі
Аккаунтыңыздың атымен бірдей репозиторий құрып (`<логин>/<логин>`), оның README.md-сіне өзіңіз туралы профиль жазыңыз: кім екеніңіз, қызығушылықтарыңыз, жобаларыңызға сілтемелер. Бұл — портфолиоңыздың «басты беті».

## Үй тапсырмасы

1. Қорғауда айтылған ескертулерді түзетіп, жаңа commit жасаңыз.
2. Өзіндік жұмыстағы қателеріңізді талдап, дұрыс шешімді дәптерге жазыңыз.
3. Келесі модульге дайындық: компьютеріңізде `python -m tkinter` командасын орындап, Tkinter терезесі ашылатынын тексеріңіз.

## Бағалау критерийлері

**Өзіндік жұмыс №1** (50 ұпай → пайыз)

| Бөлім | Не бағаланады | Ұпай |
|---|---|---|
| А. Теория | Әр сұраққа толық әрі дұрыс жауап | 10 |
| В. Кодты оқу | Дұрыс нәтиже (5) + түсіндірме (5) | 10 |
| С1 | Класс құрылымы (5), инкапсуляция/тексеріс (5), магиялық әдіс (5) | 15 |
| С2 | Мұрагерлік/`super()` (5), override/полиморфизм (5), нәтиже дұрыс (5) | 15 |
| **Барлығы** | | **50** |

Шкала: 45–50 — «5», 35–44 — «4», 25–34 — «3», 25-тен төмен — қайта тапсыру.

**Мини-жобаны қорғау** (100 ұпай)

| Критерий | Пайыз |
|---|---|
| Идеяның дербестігі мен өзектілігі | 20% |
| Техникалық орындалуы (кластар, ОБП принциптері, жұмыс істеуі) | 30% |
| Код сапасы (PEP 8, атаулар, docstring, құрылым) | 15% |
| GitHub репозиторийі (README, .gitignore, commit тарихы) | 15% |
| Таныстыру және сұрақтарға жауап | 20% |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-нұсқа, А бөлімі (қысқа жауаптар)**
1. Класс — үлгі/сызба (мыс. `Car`), объект — сол үлгімен құрылған нақты дана (`my_car = Car()`).
2. `self` — әдіс шақырылып тұрған нақты объектіге сілтеме.
3. `_balance` — «келісім бойынша» жабық (қол жеткізуге болады), `__balance` — атауы бұрмаланады (`_Класс__balance`), сырттан тікелей қол жеткізу қиын.
4. Ата-кластың әдістерін (соның ішінде `__init__`) ұрпақ класс ішінен шақыру үшін.
5. `print()`/`str()` кезінде; жол (str) қайтаруы тиіс.

**1-нұсқа, В бөлімі**
```text
Тас дейді: ...
Қошқар дейді: Мә-ә
Қозы дейді: Мә-ә!
```
`__str__` ата-класта бір рет жазылған, бірақ `self.voice()` объектінің өз класындағы (қайта анықталған) нұсқаны шақырады — полиморфизм. `Lamb` `super()` арқылы `Sheep` нұсқасын кеңейтеді.

**1-нұсқа, С1**
```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    def deposit(self, amount):
        if amount > 0:
            self._balance += amount
        else:
            print("Сома оң болуы керек")

    def withdraw(self, amount):
        if 0 < amount <= self._balance:
            self._balance -= amount
        else:
            print("Қаражат жеткіліксіз немесе сома қате")

    def __str__(self):
        return f"Иесі: {self.owner}, қалдық: {self._balance} тг"


acc = BankAccount("Ерлан", 1000)
acc.deposit(500)
acc.withdraw(2000)
print(acc)
```

**1-нұсқа, С2**
```python
import math

class Shape:
    def area(self):
        return 0

class Rectangle(Shape):
    def __init__(self, a, b):
        self.a, self.b = a, b
    def area(self):
        return self.a * self.b

class Circle(Shape):
    def __init__(self, r):
        self.r = r
    def area(self):
        return round(math.pi * self.r ** 2, 2)

shapes = [Rectangle(3, 4), Circle(2), Rectangle(5, 1)]
for s in shapes:
    print(type(s).__name__, s.area())
biggest = max(shapes, key=lambda s: s.area())
print("Ең үлкені:", type(biggest).__name__, biggest.area())
```

**2-нұсқа, А бөлімі**
1. Объект құрылған сәтте автоматты шақырылатын, атрибуттарды бастапқы мәнмен толтыратын әдіс.
2. Деректерді сырттан тікелей өзгертуден қорғау, оларға тек әдістер/property арқылы қол жеткізу; қате мәндерден қорғайды.
3. Жаңа класстың бар класстың атрибуттары мен әдістерін алуы: `class Child(Parent):`.
4. Бір атаулы әдістің әртүрлі кластарда өзінше орындалуы; мыс. `len("abc")` және `len([1, 2])`, немесе `speak()` әр жануарда.
5. Екі айнымалы бір объектіге сілтейтінін (идентичность, `is` сияқты) тексереді.

**2-нұсқа, В бөлімі**
```text
3 1 2
A
```
`add` `self` қайтарады, сондықтан шақыруларды тізбектеуге болады; `total` — класс атрибуты, екі объект құрылды.

**2-нұсқа, С1**
```python
class Student:
    def __init__(self, name):
        self.name = name
        self._grades = []

    def add_grade(self, g):
        if 1 <= g <= 10:
            self._grades.append(g)
        else:
            print("Баға 1–10 аралығында болуы керек")

    @property
    def average(self):
        if not self._grades:
            return 0
        return round(sum(self._grades) / len(self._grades), 2)

    def __str__(self):
        return f"Аты: {self.name}, орташа: {self.average}"

    def __eq__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.average == other.average


a = Student("Ару")
b = Student("Ерасыл")
for g in (8, 10, 9):
    a.add_grade(g)
for g in (9, 9):
    b.add_grade(g)
print(a, b, a == b, sep="\n")
```

**2-нұсқа, С2**
```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def total_income(self):
        return self.salary


class Manager(Employee):
    def __init__(self, name, salary, bonus):
        super().__init__(name, salary)
        self.bonus = bonus

    def total_income(self):
        return super().total_income() + self.bonus


staff = [Employee("Әйгерім", 300000), Manager("Бауыржан", 450000, 100000)]
total = 0
for e in staff:
    print(e.name, e.total_income())
    total += e.total_income()
print("Жалпы табыс:", total)
```

</details>
