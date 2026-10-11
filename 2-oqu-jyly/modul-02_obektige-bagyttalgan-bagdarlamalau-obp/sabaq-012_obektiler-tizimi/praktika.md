# Сабақ 12. Практикалық тапсырмалар: объектілер тізімі және жиынтықпен жұмыс

Тапсырмалар үшін ортақ деректер (көшіріп ал):

```python
CITIES = [
    ("Алматы", 2230, 683, "оңтүстік"),
    ("Астана", 1420, 797, "орталық"),
    ("Шымкент", 1220, 1170, "оңтүстік"),
    ("Ақтөбе", 590, 2330, "батыс"),
    ("Қарағанды", 500, 550, "орталық"),
    ("Тараз", 430, 180, "оңтүстік"),
    ("Павлодар", 330, 400, "солтүстік"),
    ("Атырау", 310, 200, "батыс"),
]
# (атауы, халқы мың адам, ауданы км², өңірі)
```

## Базалық деңгей

### 1-тапсырма. `City` объектілерінің тізімі
`City` класын жаз: атрибуттары `name`, `population` (мың адам), `area`, `region`; әдістері `density()` (адам/км², бүтін) және `info()`. `CITIES` деректерінен объектілер тізімін тізім генераторымен құр және бәрін шығар.

```text
Алматы (оңтүстік): 2230 мың адам, 3265 адам/км²
Астана (орталық): 1420 мың адам, 1782 адам/км²
...
```

### 2-тапсырма. Агрегация
1-тапсырманың тізімі бойынша шығар:
- барлық қалалардың жалпы халқы;
- халқы 1 миллионнан асатын қалалар саны;
- ең үлкен аумақты қала (`max` + `key`);
- ең тығыз қоныстанған қала.

```text
Жалпы халқы: 7030 мың адам
Миллионер қалалар: 3
Ең үлкен аумақ: Ақтөбе
Ең тығыз: Алматы
```

### 3-тапсырма. Іздеу және сүзу
- `find_city(cities, name)` — атауы бойынша (әріп регистріне қарамай) іздейді, табылмаса `None`.
- Пайдаланушыдан өңір атауын сұрап, сол өңірдегі қалаларды шығар.

```text
Қала: шымкент
Шымкент (оңтүстік): 1220 мың адам, 1043 адам/км²
Өңір: батыс
Ақтөбе, Атырау
```

### 4-тапсырма. Сұрыптау
Қалаларды: а) халқы бойынша кему ретімен; б) атауы бойынша алфавитпен; в) алдымен өңір, өңір ішінде — халқы кему ретімен сұрыптап шығар.

## Кеңейтілген деңгей

### 5-тапсырма. `Classroom` — контейнер класы
`Student` (`name`, `marks`, әдістері `add_mark`, `average`) және `Classroom` кластарын жаз. `Classroom` әдістері:
- `add_student(student)` — аты қайталанса, қоспайды;
- `find(name)`;
- `remove(name)`;
- `top(n=3)` — орташа бағасы ең жоғары `n` оқушы;
- `average()` — сынып бойынша орташа;
- `below(limit)` — орташасы `limit`-тен төмен оқушылар (қосымша сабаққа шақыру тізімі);
- `report()` — кесте түрінде есеп.

```text
№  Аты       Орташа
1  Жанар     5.00
2  Әлия      4.67
...
Сынып бойынша: 4.20
Қосымша сабаққа: Бекзат, Ерлан
```

### 6-тапсырма. «Футбол турнирі»
`Team` класы: `name`, `wins`, `draws`, `losses`, `goals_for`, `goals_against`; әдістері `points()` (жеңіс — 3, тең — 1), `goal_diff()`, `play(other, my_goals, their_goals)` — екі команданың да статистикасын жаңартады. 4 команда құрып, 6 ойын нәтижесін енгіз де, турнир кестесін ұпай → гол айырмасы → соққан гол бойынша сұрыптап шығар.

```text
Орын Команда       О  Ж  Т  Ж-с  Гол    Ұ
1    Қайрат        3  3  0  0   7:2    9
2    Астана        3  2  0  1   5:4    6
...
```

### 7-тапсырма. Сөздікпен қатынау
`Contact` (`phone`, `name`, `city`) объектілерін телефон нөмірі кілт болатын сөздікте сақтайтын `PhoneBook` класын жаз: `add`, `find_by_phone` (жылдам — сөздік арқылы), `find_by_name` (ішінара сәйкестік, тізім қайтарады), `by_city()` — `{қала: [аттар]}` топтау.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. «Жұлдыздар аспаны» симуляциясы
`Particle` класы: `x`, `y`, `vx`, `vy`; әдістері `move()` (координатаны жылдамдыққа жылжыту, 0–100 шекарасынан шағылысу — жылдамдық таңбасы өзгереді). 20 кездейсоқ бөлшек құрып, 50 қадам симуляция жаса. Әр 10 қадам сайын: бөлшектердің «масса центрін» (x, y орташасы) және бір-біріне ең жақын екі бөлшектің ара қашықтығын шығар.

### 9-тапсырма. Рейтингтегі орындар
Оқушылар тізімін орташа балл бойынша сұрыптап, **спорттық** орын нөмірін бер: бірдей ұпайлы оқушылар бірдей орын алады, келесі орын өткізіліп кетеді (1, 2, 2, 4, ...). Бірдей ұпайлылар ішінде — алфавит бойынша.

```text
1. Жанар 5.00
1. Мадина 5.00
3. Әлия 4.67
3. Дана 4.67
5. Айдос 4.50
```

## Үй тапсырмасы

1. 5-тапсырманы аяқта және GitHub-тағы `oop-practice` репозиторийіне `classroom.py` ретінде push жаса.
2. Өз жобаңда «контейнер» класы керек пе (мысалы, `Game` ішінде `enemies` тізімі, `Diary` ішінде `tasks`)? UML сызбасын жетілдір.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–4-тапсырмалар**
```python
CITIES = [
    ("Алматы", 2230, 683, "оңтүстік"),
    ("Астана", 1420, 797, "орталық"),
    ("Шымкент", 1220, 1170, "оңтүстік"),
    ("Ақтөбе", 590, 2330, "батыс"),
    ("Қарағанды", 500, 550, "орталық"),
    ("Тараз", 430, 180, "оңтүстік"),
    ("Павлодар", 330, 400, "солтүстік"),
    ("Атырау", 310, 200, "батыс"),
]


class City:
    """Қазақстан қаласы."""

    def __init__(self, name, population, area, region):
        self.name = name
        self.population = population   # мың адам
        self.area = area
        self.region = region

    def density(self):
        """Халық тығыздығы, адам/км²."""
        return round(self.population * 1000 / self.area)

    def info(self):
        return (f"{self.name} ({self.region}): {self.population} мың адам, "
                f"{self.density()} адам/км²")


def find_city(cities, name):
    """Атауы бойынша қаланы қайтарады, табылмаса None."""
    for city in cities:
        if city.name.lower() == name.strip().lower():
            return city
    return None


cities = [City(*row) for row in CITIES]

# 1
for city in cities:
    print(city.info())

# 2
print("Жалпы халқы:", sum(c.population for c in cities), "мың адам")
print("Миллионер қалалар:", sum(1 for c in cities if c.population > 1000))
print("Ең үлкен аумақ:", max(cities, key=lambda c: c.area).name)
print("Ең тығыз:", max(cities, key=lambda c: c.density()).name)

# 3
found = find_city(cities, input("Қала: "))
print(found.info() if found else "Табылмады")
region = input("Өңір: ").strip().lower()
print(", ".join(c.name for c in cities if c.region == region))

# 4
for c in sorted(cities, key=lambda c: c.population, reverse=True):
    print(c.name, c.population)
for c in sorted(cities, key=lambda c: c.name):
    print(c.name)
for c in sorted(cities, key=lambda c: (c.region, -c.population)):
    print(c.region, c.name, c.population)
```

**5-тапсырма**
```python
class Student:
    """Оқушы."""

    def __init__(self, name, marks=None):
        self.name = name
        self.marks = list(marks) if marks else []

    def add_mark(self, mark):
        if 1 <= mark <= 5:
            self.marks.append(mark)

    def average(self):
        return sum(self.marks) / len(self.marks) if self.marks else 0


class Classroom:
    """Оқушылар жиынтығын басқаратын сынып."""

    def __init__(self, title):
        self.title = title
        self.students = []

    def find(self, name):
        for s in self.students:
            if s.name.lower() == name.lower():
                return s
        return None

    def add_student(self, student):
        if self.find(student.name):
            print(f"{student.name} бұрыннан бар")
            return False
        self.students.append(student)
        return True

    def remove(self, name):
        student = self.find(name)
        if student:
            self.students.remove(student)
            return True
        return False

    def top(self, n=3):
        return sorted(self.students, key=lambda s: s.average(), reverse=True)[:n]

    def average(self):
        if not self.students:
            return 0
        return sum(s.average() for s in self.students) / len(self.students)

    def below(self, limit):
        return [s for s in self.students if s.average() < limit]

    def report(self):
        print(f"{'№':<3}{'Аты':<10}Орташа")
        ranked = sorted(self.students, key=lambda s: s.average(), reverse=True)
        for i, s in enumerate(ranked, start=1):
            print(f"{i:<3}{s.name:<10}{s.average():.2f}")
        print(f"Сынып бойынша: {self.average():.2f}")
        weak = self.below(4)
        if weak:
            print("Қосымша сабаққа:", ", ".join(s.name for s in weak))


room = Classroom("8 «А»")
for name, marks in [("Әлия", [5, 5, 4]), ("Бекзат", [4, 3, 4]), ("Дана", [5, 4, 4]),
                    ("Ерлан", [3, 4, 3]), ("Жанар", [5, 5, 5])]:
    room.add_student(Student(name, marks))
room.report()
print([s.name for s in room.top(2)])
```

**6-тапсырма**
```python
class Team:
    """Футбол командасы."""

    def __init__(self, name):
        self.name = name
        self.wins = self.draws = self.losses = 0
        self.goals_for = self.goals_against = 0

    def points(self):
        return self.wins * 3 + self.draws

    def goal_diff(self):
        return self.goals_for - self.goals_against

    def games(self):
        return self.wins + self.draws + self.losses

    def _record(self, scored, conceded):
        self.goals_for += scored
        self.goals_against += conceded
        if scored > conceded:
            self.wins += 1
        elif scored == conceded:
            self.draws += 1
        else:
            self.losses += 1

    def play(self, other, my_goals, their_goals):
        self._record(my_goals, their_goals)
        other._record(their_goals, my_goals)


teams = {name: Team(name) for name in ["Қайрат", "Астана", "Тобыл", "Ордабасы"]}
results = [
    ("Қайрат", "Астана", 2, 1), ("Тобыл", "Ордабасы", 0, 0),
    ("Қайрат", "Тобыл", 3, 0), ("Астана", "Ордабасы", 2, 1),
    ("Қайрат", "Ордабасы", 2, 1), ("Астана", "Тобыл", 2, 1),
]
for home, away, g1, g2 in results:
    teams[home].play(teams[away], g1, g2)

table = sorted(teams.values(),
               key=lambda t: (t.points(), t.goal_diff(), t.goals_for), reverse=True)
print(f"{'Орын':<5}{'Команда':<12}{'О':>3}{'Ж':>3}{'Т':>3}{'Ж-с':>4}{'Гол':>7}{'Ұ':>4}")
for place, t in enumerate(table, start=1):
    goals = f"{t.goals_for}:{t.goals_against}"
    print(f"{place:<5}{t.name:<12}{t.games():>3}{t.wins:>3}{t.draws:>3}"
          f"{t.losses:>4}{goals:>7}{t.points():>4}")
```

**7-тапсырма**
```python
class Contact:
    """Байланыс."""

    def __init__(self, phone, name, city):
        self.phone = phone
        self.name = name
        self.city = city


class PhoneBook:
    """Телефон кітапшасы."""

    def __init__(self):
        self.contacts = {}

    def add(self, contact):
        self.contacts[contact.phone] = contact

    def find_by_phone(self, phone):
        return self.contacts.get(phone)

    def find_by_name(self, part):
        return [c for c in self.contacts.values() if part.lower() in c.name.lower()]

    def by_city(self):
        groups = {}
        for c in self.contacts.values():
            groups.setdefault(c.city, []).append(c.name)
        return groups


book = PhoneBook()
book.add(Contact("+77011234567", "Әлия Серікова", "Алматы"))
book.add(Contact("+77079876543", "Ерлан Ахметов", "Астана"))
book.add(Contact("+77775554433", "Әлібек Нұров", "Алматы"))
print(book.find_by_phone("+77079876543").name)
print([c.name for c in book.find_by_name("әлі")])
print(book.by_city())
```

**8-тапсырма**
```python
import math
import random


class Particle:
    """Қорап ішіндегі бөлшек."""
    SIZE = 100

    def __init__(self):
        self.x = random.uniform(0, self.SIZE)
        self.y = random.uniform(0, self.SIZE)
        self.vx = random.uniform(-3, 3)
        self.vy = random.uniform(-3, 3)

    def move(self):
        self.x += self.vx
        self.y += self.vy
        if not 0 <= self.x <= self.SIZE:
            self.vx = -self.vx
            self.x = max(0, min(self.SIZE, self.x))
        if not 0 <= self.y <= self.SIZE:
            self.vy = -self.vy
            self.y = max(0, min(self.SIZE, self.y))

    def distance(self, other):
        return math.hypot(self.x - other.x, self.y - other.y)


particles = [Particle() for _ in range(20)]
for step in range(1, 51):
    for p in particles:
        p.move()
    if step % 10 == 0:
        cx = sum(p.x for p in particles) / len(particles)
        cy = sum(p.y for p in particles) / len(particles)
        closest = min(particles[i].distance(particles[j])
                      for i in range(len(particles))
                      for j in range(i + 1, len(particles)))
        print(f"{step}-қадам: центр ({cx:.1f}; {cy:.1f}), ең жақын {closest:.2f}")
```

**9-тапсырма**
```python
def ranking(students):
    """(орын, оқушы) жұптарының тізімін қайтарады."""
    ordered = sorted(students, key=lambda s: (-round(s.average(), 2), s.name))
    result = []
    for index, student in enumerate(ordered):
        if index > 0 and round(student.average(), 2) == round(ordered[index - 1].average(), 2):
            place = result[-1][0]
        else:
            place = index + 1
        result.append((place, student))
    return result


group = [Student("Әлия", [5, 5, 4]), Student("Жанар", [5, 5, 5]), Student("Дана", [5, 4, 5]),
         Student("Мадина", [5, 5]), Student("Айдос", [4, 5])]
for place, s in ranking(group):
    print(f"{place}. {s.name} {s.average():.2f}")
```
(9-тапсырма 5-тапсырмадағы `Student` класын қолданады.)

</details>
