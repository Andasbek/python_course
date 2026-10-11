# Урок 64. Практические задания: time и datetime

## Базовый уровень

**Задание 1. Какой сегодня день?** Выведи сегодняшнюю дату в трёх видах: `2027-04-26`, `26.04.2027` и по-русски `26 апреля 2027 года, понедельник`.

**Задание 2. Обратный отсчёт.** Пользователь вводит количество секунд. Программа каждую секунду выводит оставшееся время в формате `ММ:СС`, а в конце пишет «Время вышло!».

```text
Сколько секунд? 75
01:15
01:14
...
00:01
Время вышло!
```

**Задание 3. До праздника.** Вычисли, сколько дней осталось (начиная с сегодняшнего дня) до следующих праздников: Наурыз (22 марта), День Независимости (16 декабря), Новый год (1 января), твой день рождения. Если праздник в этом году уже прошёл – бери дату следующего года.

**Задание 4. Найди день недели.** Пользователь вводит дату в формате `ДД.ММ.ГГГГ`, программа выводит по-русски, какой это день недели. Неверный формат обработай с помощью `try...except`.

```text
Дата: 16.12.1991
Это – понедельник
```

**Задание 5. Медленный текст.** Напиши функцию `type_text(text, delay)`: она выводит текст побуквенно, делая паузу `delay` секунд после каждого символа (`print(ch, end="", flush=True)`). Выведи первое предложение сказки с эффектом «печатной машинки».

## Продвинутый уровень

**Задание 6. Калькулятор возраста+.** Вводится дата рождения. Выведи: полный возраст; количество прожитых дней, часов, минут; сколько дней осталось до следующего дня рождения; на какую дату придётся твой 10 000-й день.

**Задание 7. Тест скорости.** Программа выводит 5 случайных слов (из списка) и просит быстро набрать их в одну строку. Измерь время набора, посчитай количество ошибок и скорость (символов в минуту).

```text
Набери: яблоко дождь компьютер гора река
> яблоко дождь компьютер гора река
Время: 9.42 с, ошибок: 0, скорость: 204 симв/мин
```

**Задание 8. Расписание уроков.** Уроки начинаются в 14:00, каждый урок 45 минут, перемена 10 минут. Вычисли с помощью `timedelta` и выведи время начала и окончания 4 уроков.

```text
Урок 1: 14:00 – 14:45
Урок 2: 14:55 – 15:40
Урок 3: 15:50 – 16:35
Урок 4: 16:45 – 17:30
```

## ★ Олимпиадная / творческая задача

**Задание 9. Календарь.** Пользователь вводит год и месяц. Программа выводит календарь этого месяца в виде таблицы с днями недели (определи, на какой день недели приходится первое число месяца, с помощью `weekday()`, а количество дней в месяце – вычтя 1 день из 1-го числа следующего месяца).

```text
    Апрель 2027
Пн Вт Ср Чт Пт Сб Вс
          1  2  3  4
 5  6  7  8  9 10 11
12 13 14 15 16 17 18
19 20 21 22 23 24 25
26 27 28 29 30
```

**Задание 10. Трекер привычек.** При каждом запуске программа записывает сегодняшнюю дату в файл `habits.txt` (только один раз за день). Затем вычисляет, сколько дней «подряд» (streak) ты выполняешь привычку.

## Домашнее задание

1. Программа «До конца учебного года»: напиши программу, которая вычисляет, сколько дней, недель и уроков Python (понедельник и среда) осталось до 25 мая.
2. Узнай, на какой день недели пришёлся твой день рождения, и спроси у родителей какую-нибудь интересную историю об этом дне.
3. Подготовка к итоговому проекту: придумай к следующему уроку 2–3 идеи своего проекта (игра, тренажёр, дневник, викторина и т.д.).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.**

```python
from datetime import date

months = ["января", "февраля", "марта", "апреля", "мая", "июня",
          "июля", "августа", "сентября", "октября", "ноября", "декабря"]
week = ["понедельник", "вторник", "среда", "четверг", "пятница", "суббота", "воскресенье"]
d = date.today()
print(d)
print(d.strftime("%d.%m.%Y"))
print(f"{d.day} {months[d.month - 1]} {d.year} года, {week[d.weekday()]}")
```

**Задание 2.**

```python
import time

seconds = int(input("Сколько секунд? "))
for left in range(seconds, 0, -1):
    print(f"{left // 60:02d}:{left % 60:02d}")
    time.sleep(1)
print("Время вышло!")
```

**Задание 3.**

```python
from datetime import date

def days_until(month, day):
    today = date.today()
    target = date(today.year, month, day)
    if target < today:
        target = date(today.year + 1, month, day)
    return (target - today).days

print("До Наурыза:", days_until(3, 22), "дн.")
print("До Дня Независимости:", days_until(12, 16), "дн.")
print("До Нового года:", days_until(1, 1), "дн.")
print("До моего дня рождения:", days_until(7, 15), "дн.")
```

**Задание 4.**

```python
from datetime import datetime

week = ["понедельник", "вторник", "среда", "четверг", "пятница", "суббота", "воскресенье"]
text = input("Дата: ")
try:
    d = datetime.strptime(text, "%d.%m.%Y")
    print("Это –", week[d.weekday()])
except ValueError:
    print("Неверный формат или такой даты нет. Например: 16.12.1991")
```

**Задание 5.**

```python
import time

def type_text(text, delay=0.05):
    for ch in text:
        print(ch, end="", flush=True)
        time.sleep(delay)
    print()

type_text("Давным-давно, в стародавние времена, жил-был в степи один батыр...", 0.08)
```

**Задание 6.**

```python
from datetime import date, datetime, timedelta

text = input("Дата рождения (ДД.ММ.ГГГГ): ")
try:
    birthday = datetime.strptime(text, "%d.%m.%Y").date()
except ValueError:
    print("Неверный формат")
else:
    today = date.today()
    age = today.year - birthday.year
    if (today.month, today.day) < (birthday.month, birthday.day):
        age -= 1
    days = (today - birthday).days
    print("Возраст:", age)
    print("Дней:", days, "| Часов:", days * 24, "| Минут:", days * 24 * 60)
    next_bd = date(today.year, birthday.month, birthday.day)
    if next_bd < today:
        next_bd = date(today.year + 1, birthday.month, birthday.day)
    print("До следующего дня рождения:", (next_bd - today).days, "дн.")
    print("Твой 10 000-й день:", (birthday + timedelta(days=10000)).strftime("%d.%m.%Y"))
```

Примечание: для родившихся 29 февраля `date(year, 2, 29)` в невисокосный год вызывает `ValueError` – можно предложить сильным ученикам как дополнительное задание.

**Задание 7.**

```python
import random
import time

words = ["яблоко", "дождь", "компьютер", "гора", "река", "книга", "Python", "школа", "степь", "звезда"]
target = " ".join(random.sample(words, 5))
print("Набери:", target)
start = time.time()
typed = input("> ")
elapsed = time.time() - start

errors = 0
for i in range(max(len(target), len(typed))):
    if i >= len(target) or i >= len(typed) or target[i] != typed[i]:
        errors += 1
speed = len(typed) / elapsed * 60
print(f"Время: {elapsed:.2f} с, ошибок: {errors}, скорость: {speed:.0f} симв/мин")
```

**Задание 8.**

```python
from datetime import datetime, timedelta

start = datetime(2027, 4, 26, 14, 0)
lesson = timedelta(minutes=45)
pause = timedelta(minutes=10)
for i in range(1, 5):
    end = start + lesson
    print(f"Урок {i}: {start.strftime('%H:%M')} – {end.strftime('%H:%M')}")
    start = end + pause
```

**Задание 9.**

```python
from datetime import date, timedelta

months = ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь",
          "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"]
year = int(input("Год: "))
month = int(input("Месяц: "))

first = date(year, month, 1)
if month == 12:
    next_first = date(year + 1, 1, 1)
else:
    next_first = date(year, month + 1, 1)
days_in_month = (next_first - timedelta(days=1)).day

print(f"{months[month - 1]} {year}".center(20))
print("Пн Вт Ср Чт Пт Сб Вс")
line = "   " * first.weekday()
for day in range(1, days_in_month + 1):
    line += f"{day:2} "
    if (first.weekday() + day) % 7 == 0:
        print(line.rstrip())
        line = ""
if line:
    print(line.rstrip())
```

**Задание 10.**

```python
from datetime import date, timedelta

FILE_NAME = "habits.txt"
today = date.today()
days = set()
try:
    with open(FILE_NAME, encoding="utf-8") as f:
        for line in f:
            if line.strip():
                days.add(line.strip())
except FileNotFoundError:
    pass

if today.isoformat() not in days:
    with open(FILE_NAME, "a", encoding="utf-8") as f:
        f.write(today.isoformat() + "\n")
    days.add(today.isoformat())

streak = 0
d = today
while d.isoformat() in days:
    streak += 1
    d -= timedelta(days=1)
print("Дней подряд:", streak)
```

</details>
