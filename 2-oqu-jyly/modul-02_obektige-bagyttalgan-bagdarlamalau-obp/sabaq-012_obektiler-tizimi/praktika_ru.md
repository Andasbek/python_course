# Урок 12. Практические задания: список объектов и работа с коллекцией

Общие данные для заданий (скопируй):

```python
CITIES = [
    ("Алматы", 2230, 683, "юг"),
    ("Астана", 1420, 797, "центр"),
    ("Шымкент", 1220, 1170, "юг"),
    ("Актобе", 590, 2330, "запад"),
    ("Караганда", 500, 550, "центр"),
    ("Тараз", 430, 180, "юг"),
    ("Павлодар", 330, 400, "север"),
    ("Атырау", 310, 200, "запад"),
]
# (название, население тыс. человек, площадь км², регион)
```

## Базовый уровень

### Задание 1. Список объектов `City`
Напиши класс `City`: атрибуты `name`, `population` (тыс. человек), `area`, `region`; методы `density()` (чел./км², целое) и `info()`. Создай из данных `CITIES` список объектов генератором списка и выведи все города.

```text
Алматы (юг): 2230 тыс. человек, 3265 чел./км²
Астана (центр): 1420 тыс. человек, 1782 чел./км²
...
```

### Задание 2. Агрегирование
По списку из задания 1 выведи:
- общее население всех городов;
- количество городов с населением больше 1 миллиона;
- город с самой большой площадью (`max` + `key`);
- самый плотно заселённый город.

```text
Общее население: 7030 тыс. человек
Городов-миллионников: 3
Самая большая площадь: Актобе
Самый плотный: Алматы
```

### Задание 3. Поиск и фильтрация
- `find_city(cities, name)` — ищет город по названию (без учёта регистра), если не найден — `None`.
- Запроси у пользователя название региона и выведи города этого региона.

```text
Город: шымкент
Шымкент (юг): 1220 тыс. человек, 1043 чел./км²
Регион: запад
Актобе, Атырау
```

### Задание 4. Сортировка
Выведи города, отсортированные: а) по населению в порядке убывания; б) по названию в алфавитном порядке; в) сначала по региону, а внутри региона — по населению в порядке убывания.

## Продвинутый уровень

### Задание 5. `Classroom` — класс-контейнер
Напиши классы `Student` (`name`, `marks`, методы `add_mark`, `average`) и `Classroom`. Методы `Classroom`:
- `add_student(student)` — если имя повторяется, не добавляет;
- `find(name)`;
- `remove(name)`;
- `top(n=3)` — `n` учеников с самым высоким средним баллом;
- `average()` — средний балл по классу;
- `below(limit)` — ученики со средним баллом ниже `limit` (список приглашённых на дополнительное занятие);
- `report()` — отчёт в виде таблицы.

```text
№  Имя       Средний
1  Жанар     5.00
2  Алия      4.67
...
По классу: 4.20
На дополнительное занятие: Бекзат, Ерлан
```

### Задание 6. «Футбольный турнир»
Класс `Team`: `name`, `wins`, `draws`, `losses`, `goals_for`, `goals_against`; методы `points()` (победа — 3, ничья — 1), `goal_diff()`, `play(other, my_goals, their_goals)` — обновляет статистику обеих команд. Создай 4 команды, введи результаты 6 матчей и выведи турнирную таблицу, отсортированную по очкам → разнице мячей → забитым мячам.

```text
Место Команда       И  В  Н   П   Мячи   О
1     Кайрат        3  3  0   0    7:2   9
2     Астана        3  2  0   1    5:4   6
...
```

### Задание 7. Доступ через словарь
Напиши класс `PhoneBook`, хранящий объекты `Contact` (`phone`, `name`, `city`) в словаре, где ключ — номер телефона: `add`, `find_by_phone` (быстро — через словарь), `find_by_name` (частичное совпадение, возвращает список), `by_city()` — группировка `{город: [имена]}`.

## ★ Олимпиадная / творческая задача

### Задание 8. Симуляция «Звёздное небо»
Класс `Particle`: `x`, `y`, `vx`, `vy`; метод `move()` (смещение координат на скорость, отражение от границ 0–100 — знак скорости меняется). Создай 20 случайных частиц и проведи симуляцию из 50 шагов. Каждые 10 шагов выводи «центр масс» частиц (среднее x, y) и расстояние между двумя ближайшими друг к другу частицами.

### Задание 9. Места в рейтинге
Отсортируй список учеников по среднему баллу и присвой **спортивные** номера мест: ученики с одинаковым баллом получают одинаковое место, следующее место пропускается (1, 2, 2, 4, ...). Среди учеников с одинаковым баллом — по алфавиту.

```text
1. Жанар 5.00
1. Мадина 5.00
3. Алия 4.67
3. Дана 4.67
5. Айдос 4.50
```

## Домашнее задание

1. Заверши задание 5 и сделай push в репозиторий `oop-practice` на GitHub в виде файла `classroom.py`.
2. Нужен ли в твоём проекте класс-«контейнер» (например, список `enemies` в `Game`, `tasks` в `Diary`)? Доработай UML-схему.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–4**
```python
CITIES = [
    ("Алматы", 2230, 683, "юг"),
    ("Астана", 1420, 797, "центр"),
    ("Шымкент", 1220, 1170, "юг"),
    ("Актобе", 590, 2330, "запад"),
    ("Караганда", 500, 550, "центр"),
    ("Тараз", 430, 180, "юг"),
    ("Павлодар", 330, 400, "север"),
    ("Атырау", 310, 200, "запад"),
]


class City:
    """Город Казахстана."""

    def __init__(self, name, population, area, region):
        self.name = name
        self.population = population   # тыс. человек
        self.area = area
        self.region = region

    def density(self):
        """Плотность населения, чел./км²."""
        return round(self.population * 1000 / self.area)

    def info(self):
        return (f"{self.name} ({self.region}): {self.population} тыс. человек, "
                f"{self.density()} чел./км²")


def find_city(cities, name):
    """Возвращает город по названию, если не найден — None."""
    for city in cities:
        if city.name.lower() == name.strip().lower():
            return city
    return None


cities = [City(*row) for row in CITIES]

# 1
for city in cities:
    print(city.info())

# 2
print("Общее население:", sum(c.population for c in cities), "тыс. человек")
print("Городов-миллионников:", sum(1 for c in cities if c.population > 1000))
print("Самая большая площадь:", max(cities, key=lambda c: c.area).name)
print("Самый плотный:", max(cities, key=lambda c: c.density()).name)

# 3
found = find_city(cities, input("Город: "))
print(found.info() if found else "Не найден")
region = input("Регион: ").strip().lower()
print(", ".join(c.name for c in cities if c.region == region))

# 4
for c in sorted(cities, key=lambda c: c.population, reverse=True):
    print(c.name, c.population)
for c in sorted(cities, key=lambda c: c.name):
    print(c.name)
for c in sorted(cities, key=lambda c: (c.region, -c.population)):
    print(c.region, c.name, c.population)
```

**Задание 5**
```python
class Student:
    """Ученик."""

    def __init__(self, name, marks=None):
        self.name = name
        self.marks = list(marks) if marks else []

    def add_mark(self, mark):
        if 1 <= mark <= 5:
            self.marks.append(mark)

    def average(self):
        return sum(self.marks) / len(self.marks) if self.marks else 0


class Classroom:
    """Класс, управляющий коллекцией учеников."""

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
            print(f"{student.name} уже есть")
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
        print(f"{'№':<3}{'Имя':<10}Средний")
        ranked = sorted(self.students, key=lambda s: s.average(), reverse=True)
        for i, s in enumerate(ranked, start=1):
            print(f"{i:<3}{s.name:<10}{s.average():.2f}")
        print(f"По классу: {self.average():.2f}")
        weak = self.below(4)
        if weak:
            print("На дополнительное занятие:", ", ".join(s.name for s in weak))


room = Classroom("8 «А»")
for name, marks in [("Алия", [5, 5, 4]), ("Бекзат", [4, 3, 4]), ("Дана", [5, 4, 4]),
                    ("Ерлан", [3, 4, 3]), ("Жанар", [5, 5, 5])]:
    room.add_student(Student(name, marks))
room.report()
print([s.name for s in room.top(2)])
```

**Задание 6**
```python
class Team:
    """Футбольная команда."""

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


teams = {name: Team(name) for name in ["Кайрат", "Астана", "Тобол", "Ордабасы"]}
results = [
    ("Кайрат", "Астана", 2, 1), ("Тобол", "Ордабасы", 0, 0),
    ("Кайрат", "Тобол", 3, 0), ("Астана", "Ордабасы", 2, 1),
    ("Кайрат", "Ордабасы", 2, 1), ("Астана", "Тобол", 2, 1),
]
for home, away, g1, g2 in results:
    teams[home].play(teams[away], g1, g2)

table = sorted(teams.values(),
               key=lambda t: (t.points(), t.goal_diff(), t.goals_for), reverse=True)
print(f"{'Место':<6}{'Команда':<12}{'И':>3}{'В':>3}{'Н':>3}{'П':>4}{'Мячи':>7}{'О':>4}")
for place, t in enumerate(table, start=1):
    goals = f"{t.goals_for}:{t.goals_against}"
    print(f"{place:<6}{t.name:<12}{t.games():>3}{t.wins:>3}{t.draws:>3}"
          f"{t.losses:>4}{goals:>7}{t.points():>4}")
```

**Задание 7**
```python
class Contact:
    """Контакт."""

    def __init__(self, phone, name, city):
        self.phone = phone
        self.name = name
        self.city = city


class PhoneBook:
    """Телефонная книжка."""

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
book.add(Contact("+77011234567", "Алия Серикова", "Алматы"))
book.add(Contact("+77079876543", "Ерлан Ахметов", "Астана"))
book.add(Contact("+77775554433", "Алибек Нуров", "Алматы"))
print(book.find_by_phone("+77079876543").name)
print([c.name for c in book.find_by_name("али")])
print(book.by_city())
```

**Задание 8**
```python
import math
import random


class Particle:
    """Частица внутри коробки."""
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
        print(f"Шаг {step}: центр ({cx:.1f}; {cy:.1f}), ближайшие {closest:.2f}")
```

**Задание 9**
```python
def ranking(students):
    """Возвращает список пар (место, ученик)."""
    ordered = sorted(students, key=lambda s: (-round(s.average(), 2), s.name))
    result = []
    for index, student in enumerate(ordered):
        if index > 0 and round(student.average(), 2) == round(ordered[index - 1].average(), 2):
            place = result[-1][0]
        else:
            place = index + 1
        result.append((place, student))
    return result


group = [Student("Алия", [5, 5, 4]), Student("Жанар", [5, 5, 5]), Student("Дана", [5, 4, 5]),
         Student("Мадина", [5, 5]), Student("Айдос", [4, 5])]
for place, s in ranking(group):
    print(f"{place}. {s.name} {s.average():.2f}")
```
(Задание 9 использует класс `Student` из задания 5.)

</details>
