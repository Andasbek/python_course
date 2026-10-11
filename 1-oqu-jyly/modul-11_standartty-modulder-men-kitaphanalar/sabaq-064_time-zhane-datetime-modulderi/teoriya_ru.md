# Урок 64. Модули time и datetime. Работа со временем.

## Темы

1. Модуль time
2. Модуль datetime
3. timedelta – промежуток времени
4. Форматирование даты: strftime()
5. Чтение даты из строки: strptime()
6. Объединяем время и файл: журнал
7. time и datetime: что когда использовать?
8. Частые ошибки
9. Основные понятия

## 1. Модуль time

Модуль `time` – работа со временем на «низком уровне»: подсчёт секунд, создание пауз.

### Метка времени (timestamp)

Компьютер считает время как количество секунд, прошедших с **1 января 1970 года** (UTC). Этот момент называется «эпохой Unix» (epoch).

```python
import time

now = time.time()
print("Секунд прошло с 1970 года:", now)
print("В понятном человеку виде:", time.ctime())
```

Результат (пример):
```text
Секунд прошло с 1970 года: 1808713800.123456
В понятном человеку виде: Mon Apr 26 14:30:00 2027
```

### sleep() – пауза

`time.sleep(секунды)` «усыпляет» программу на указанное время:

```python
import time

print("Ракета готовится к запуску...")
for i in range(5, 0, -1):
    print(i)
    time.sleep(1)
print("Пуск!")
```

Результат (каждое число выводится с интервалом в 1 секунду):
```text
Ракета готовится к запуску...
5
4
3
2
1
Пуск!
```

`sleep(0.5)` – полсекунды. Это используется для анимации, эффекта «медленно печатаемого текста», пауз в играх.

### Секундомер: измерение времени выполнения

```python
import time

start = time.time()
total = 0
for i in range(1_000_000):
    total += i
finish = time.time()
print("Сумма:", total)
print(f"Время: {finish - start:.3f} секунд")
```

Результат (зависит от компьютера):
```text
Сумма: 499999500000
Время: 0.084 секунд
```

(`1_000_000` – подчёркивания внутри числа ставятся для удобства чтения, Python читает его как 1000000. Для более точного измерения есть `time.perf_counter()`.)

### Игра на реакцию

```python
import random
import time

print("Приготовься! Когда появится надпись 'ДАВАЙ!', нажми Enter.")
time.sleep(random.uniform(2, 5))
start = time.time()
input("ДАВАЙ!")
reaction = time.time() - start
print(f"Твоя реакция: {reaction:.3f} секунд")
if reaction < 0.3:
    print("Отлично! Настоящий ниндзя!")
```

### localtime() – составные части времени

```python
import time

t = time.localtime()
print("Год:", t.tm_year)
print("Месяц:", t.tm_mon)
print("День:", t.tm_mday)
print(f"Время: {t.tm_hour:02d}:{t.tm_min:02d}")
```

## 2. Модуль datetime

`datetime` – удобная работа с датой и временем: календарь, разница, форматирование. В модуле есть несколько **классов** (типов объектов):

| Класс | Что хранит | Пример |
|---|---|---|
| `date` | Дату: год, месяц, день | `2027-04-26` |
| `time` | Время: часы, минуты, секунды | `14:30:00` |
| `datetime` | Дату + время | `2027-04-26 14:30:00` |
| `timedelta` | Промежуток времени (разницу) | `29 days` |

```python
from datetime import date, datetime, timedelta
```

> Название модуля и класса совпадают – `datetime`. Чтобы не путаться, удобно писать `from datetime import ...`, как показано выше.

### Сегодняшняя дата и текущее время

```python
from datetime import date, datetime

today = date.today()
now = datetime.now()
print("Сегодня:", today)
print("Сейчас:", now)
print("Год:", today.year, "Месяц:", today.month, "День:", today.day)
print("Час:", now.hour, "Минута:", now.minute)
```

Результат (пример):
```text
Сегодня: 2027-04-26
Сейчас: 2027-04-26 14:30:15.482913
Год: 2027 Месяц: 4 День: 26
Час: 14 Минута: 30
```

### Создание конкретной даты

```python
from datetime import date, datetime

independence = date(1991, 12, 16)
lesson = datetime(2027, 4, 26, 14, 30)
print(independence)
print(lesson)
```

Результат:
```text
1991-12-16
2027-04-26 14:30:00
```

Если создать несуществующую дату – `ValueError`: `date(2027, 2, 30)` → `day is out of range for month`.

### День недели

`weekday()` – понедельник = 0, …, воскресенье = 6. Русские названия берём из списка:

```python
from datetime import date

week = ["понедельник", "вторник", "среда", "четверг", "пятница", "суббота", "воскресенье"]
d = date(1991, 12, 16)
print("День Независимости пришёлся на день недели:", week[d.weekday()])
```

Результат:
```text
День Независимости пришёлся на день недели: понедельник
```

## 3. timedelta – промежуток времени

**Разность двух дат** – объект `timedelta`:

```python
from datetime import date

start = date(2027, 4, 26)
summer = date(2027, 5, 25)
left = summer - start
print("Разница:", left)
print("До каникул осталось", left.days, "дней")
```

Результат:
```text
Разница: 29 days, 0:00:00
До каникул осталось 29 дней
```

**Прибавление времени к дате:**

```python
from datetime import date, datetime, timedelta

d = date(2027, 4, 26)
print("Через 100 дней:", d + timedelta(days=100))
print("Неделю назад:", d - timedelta(weeks=1))

lesson = datetime(2027, 4, 26, 14, 30)
end = lesson + timedelta(hours=1, minutes=40)
print("Урок закончится:", end.strftime("%H:%M"))
```

Результат:
```text
Через 100 дней: 2027-08-04
Неделю назад: 2027-04-19
Урок закончится: 16:10
```

Параметры `timedelta`: `weeks`, `days`, `hours`, `minutes`, `seconds`. `.days` – целые дни, `.total_seconds()` – всего секунд.

**Сравнение дат:** работают `<`, `>`, `==`:

```python
from datetime import date

deadline = date(2027, 5, 10)
if date(2027, 4, 26) < deadline:
    print("Время на сдачу проекта ещё есть")
```

## 4. Форматирование даты: strftime()

`strftime(формат)` – превращает дату в **строку** нужного вида (string format time).

| Код | Смысл | Пример |
|---|---|---|
| `%d` | День (01–31) | `26` |
| `%m` | Номер месяца (01–12) | `04` |
| `%Y` | Год (4 цифры) | `2027` |
| `%y` | Год (2 цифры) | `27` |
| `%H` | Час (00–23) | `14` |
| `%M` | Минута | `30` |
| `%S` | Секунда | `05` |
| `%A` / `%B` | День недели / название месяца (по-английски) | `Monday` / `April` |

```python
from datetime import datetime

dt = datetime(2027, 4, 26, 14, 30, 5)
print(dt.strftime("%d.%m.%Y"))
print(dt.strftime("%H:%M:%S"))
print(dt.strftime("Дата: %d.%m.%y, время: %H:%M"))
```

Результат:
```text
26.04.2027
14:30:05
Дата: 26.04.27, время: 14:30
```

Чтобы вывести название месяца по-русски, используем свой список:

```python
from datetime import date

months = ["января", "февраля", "марта", "апреля", "мая", "июня",
          "июля", "августа", "сентября", "октября", "ноября", "декабря"]
d = date(2027, 4, 26)
print(f"{d.day} {months[d.month - 1]} {d.year} года")
```

Результат:
```text
26 апреля 2027 года
```

## 5. Чтение даты из строки: strptime()

Пользователь вводит дату как текст – превращаем её в дату с помощью `datetime.strptime(строка, формат)` (string parse time):

```python
from datetime import datetime

text = "16.12.2027"
d = datetime.strptime(text, "%d.%m.%Y").date()
print(d, type(d))
```

Результат:
```text
2027-12-16 <class 'datetime.date'>
```

Если строка не соответствует формату – `ValueError`. Поэтому используем `try...except`:

### Пример: калькулятор возраста

```python
from datetime import date, datetime

text = input("Введи дату рождения (ДД.ММ.ГГГГ): ")
try:
    birthday = datetime.strptime(text, "%d.%m.%Y").date()
except ValueError:
    print("Неверный формат даты. Например: 15.07.2013")
else:
    today = date.today()
    days_lived = (today - birthday).days
    age = today.year - birthday.year
    if (today.month, today.day) < (birthday.month, birthday.day):
        age -= 1       # день рождения в этом году ещё не наступил
    print("Твой возраст:", age)
    print("Прожито дней:", days_lived)
```

Результат (если сегодня 26.04.2027):
```text
Введи дату рождения (ДД.ММ.ГГГГ): 15.07.2013
Твой возраст: 13
Прожито дней: 5033
```

(Сравнение кортежей `(4, 26) < (7, 15)` сначала сравнивает месяцы, а если они равны – дни: кортежи сравниваются поэлементно.)

## 6. Объединяем время и файл: журнал

Объединим дозапись в файл (режим `a`) с меткой времени – такой **лог** есть в любой настоящей программе:

```python
from datetime import datetime

action = input("Что ты сделал? ")
stamp = datetime.now().strftime("%d.%m.%Y %H:%M")
with open("log.txt", "a", encoding="utf-8") as f:
    f.write(f"[{stamp}] {action}\n")
print("Записано в журнал.")
```

`log.txt` (пример):
```text
[26.04.2027 14:35] Пришёл на урок Python
[26.04.2027 15:10] Изучил модуль datetime
```

## 7. time и datetime: что когда использовать?

| Задача | Модуль |
|---|---|
| Сделать паузу | `time.sleep()` |
| Измерить скорость кода | `time.time()` / `time.perf_counter()` |
| Сегодняшняя дата, день недели | `datetime.date.today()` |
| Разница дат, прибавление к дате | `datetime` + `timedelta` |
| Красивый вывод даты / чтение из текста | `strftime()` / `strptime()` |

## Частые ошибки

**1. Путаница с именем `datetime`.**

```python
import datetime
# datetime.now()  -> AttributeError: module 'datetime' has no attribute 'now'
print(datetime.datetime.now())   # модуль.класс.метод
```

Или: `from datetime import datetime` → `datetime.now()`.

**2. Сравнение введённой пользователем строки с датой.** `"16.12.2027" < date.today()` → `TypeError`. Сначала преобразуй через `strptime`.

**3. Регистр букв в формате.** `%m` – месяц, `%M` – минута! `%Y` – год из 4 цифр, `%y` – из 2.

**4. Месяцы в `timedelta`.** `timedelta(months=1)` → `TypeError`: так как месяцы разной длины, у `timedelta` нет `months`. Используй `days=30` или меняй месяц вручную.

**5. Думать, что `sleep` в миллисекундах.** `time.sleep(500)` – не 500 миллисекунд, а 500 **секунд** (больше 8 минут)! Полсекунды – `time.sleep(0.5)`.

**6. Вычисление возраста только по разнице лет.** Если день рождения в этом году ещё не наступил, возраст получится на год больше – учитывай и месяц, и день.

## Основные понятия

| Понятие | Пояснение |
|---|---|
| Метка времени (timestamp) | Количество секунд, прошедших с 01.01.1970 |
| `time.sleep(s)` | Приостановить программу на s секунд |
| `time.time()` | Текущая метка времени |
| `date` | Класс, хранящий дату (год, месяц, день) |
| `datetime` | Класс, хранящий дату и время |
| `timedelta` | Промежуток времени |
| `weekday()` | Номер дня недели (0 – понедельник) |
| `strftime()` | Форматирование даты в строку |
| `strptime()` | Преобразование строки в дату |

## Примечание

- `datetime.now()` и `date.today()` возвращают **наивное** (naive) время без часового пояса. Для работы с поясами используй `datetime.now(timezone.utc)` или модуль `zoneinfo` (Python 3.9+): `datetime.now(ZoneInfo("Asia/Almaty"))`.
- `datetime.utcnow()` устарел начиная с Python 3.12 – вместо него пиши `datetime.now(timezone.utc)`.
- Для замера скорости кода лучше `time.perf_counter()` (самый точный счётчик, не зависит от перевода часов) или модуль `timeit`; `time.time()` может «прыгать» при синхронизации системных часов.
- Названия месяцев и дней в `%A`/`%B` зависят от локали системы; надёжнее хранить собственные списки названий, как в примерах выше.
- Формат ISO 8601 (`2027-04-26T14:30:00`) удобно читать и писать методами `isoformat()` и `datetime.fromisoformat()` – это стандартный способ хранить даты в файлах.
- Документация: [datetime](https://docs.python.org/3/library/datetime.html), [time](https://docs.python.org/3/library/time.html), [коды strftime](https://docs.python.org/3/library/datetime.html#format-codes).
