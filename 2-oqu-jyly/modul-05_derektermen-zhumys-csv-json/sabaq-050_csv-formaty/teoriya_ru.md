# Урок 50. Формат CSV. Модуль csv: чтение и запись файла.

## Темы

1. Что такое CSV?
2. `csv.reader` — чтение таблицы
3. `csv.writer` — запись таблицы
4. `csv.DictReader` — чтение по названиям столбцов
5. `csv.DictWriter` — запись словарей
6. Совместимость с Excel
7. Частые ошибки
8. Основные понятия

## 1. Что такое CSV?

**CSV (Comma-Separated Values)** — «значения, разделённые запятыми». Это самый распространённый способ хранения таблицы в простом текстовом файле.

- Каждая **строка** — одна запись таблицы (row).
- Значения внутри строки разделяются **разделителем** (delimiter) — обычно запятой `,`.
- Первая строка чаще всего — **заголовок** (header), названия столбцов.

`students.csv`:

```text
name,grade,city,score
Айгерим,9,Алматы,95
Бауыржан,8,Астана,78
Даулет,10,Шымкент,88
Сымбат,9,Алматы,100
```

Если открыть в Excel — это таблица:

| name | grade | city | score |
|---|---|---|---|
| Айгерим | 9 | Алматы | 95 |
| Бауыржан | 8 | Астана | 78 |
| Даулет | 10 | Шымкент | 88 |
| Сымбат | 9 | Алматы | 100 |

**Где используется:** экспорт из Excel/Google Sheets, выгрузка журнала из kundelik.kz и других систем, порталы открытых данных (data.egov.kz), научные измерения, банковские выписки.

### Правила CSV (RFC 4180)

1. Если внутри значения есть разделитель (запятая) — значение берётся в **двойные кавычки**: `"Алматы, Медеуский район"`.
2. Если внутри значения есть двойная кавычка — она записывается **дважды**: `"Клуб «Жұлдыз» ""Star"""`.
3. Если внутри значения есть перевод строки — значение стоит в кавычках.

```text
name,address,club
Айгерим,"Алматы, Медеуский район","Шахматы ""Белый король"""
```

Такую строку `line.split(",")` правильно разделить не может — она разобьёт её на две части: `"Алматы` и `Медеуский район"`. Поэтому **всегда используем модуль `csv`** — он знает все правила.

## 2. `csv.reader` — чтение таблицы

```python
import csv

# Сначала создадим файл-образец
with open("students.csv", "w", encoding="utf-8", newline="") as f:
    f.write("name,grade,city,score\n")
    f.write("Айгерим,9,Алматы,95\n")
    f.write("Бауыржан,8,Астана,78\n")
    f.write("Даулет,10,Шымкент,88\n")
    f.write("Сымбат,9,Алматы,100\n")

with open("students.csv", encoding="utf-8", newline="") as f:
    reader = csv.reader(f)
    header = next(reader)          # первая строка — заголовок
    print("Столбцы:", header)
    for row in reader:
        print(row)
```

Результат:

```text
Столбцы: ['name', 'grade', 'city', 'score']
['Айгерим', '9', 'Алматы', '95']
['Бауыржан', '8', 'Астана', '78']
['Даулет', '10', 'Шымкент', '88']
['Сымбат', '9', 'Алматы', '100']
```

Важно:
- `reader` — **итератор**: на каждом шаге отдаёт одну строку в виде **списка**;
- все значения — **строки** (`str`)! `'95'` — не число. Для вычислений `int(row[3])`;
- `next(reader)` отдельно забирает заголовок, и в цикле остаются только данные.

### Зачем нужен `newline=""`?

Модуль `csv` сам управляет концами строк. Если не указать `newline=""`, в файле, записанном в Windows, между строками появятся **пустые строки**, а при чтении могут испортиться многострочные значения в кавычках. Правило: **CSV-файл всегда открываем с `newline=""`.**

## 3. `csv.writer` — запись таблицы

```python
import csv

products = [
    ["Товар", "Цена (₸)", "Количество"],
    ["Хлеб", 250, 2],
    ["Молоко", 520, 1],
    ["Яблоки, красные", 890, 3],     # в значении есть запятая!
]

with open("shop.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.writer(f)
    writer.writerows(products)        # сразу несколько строк
    writer.writerow(["Итого", "", 6])  # одна строка

with open("shop.csv", encoding="utf-8") as f:
    print(f.read())
```

Результат:

```text
Товар,Цена (₸),Количество
Хлеб,250,2
Молоко,520,1
"Яблоки, красные",890,3
Итого,,6
```

`csv.writer` **автоматически** взял значение «Яблоки, красные» в кавычки, а числа сам преобразовал в строки.

| Метод | Что делает |
|---|---|
| `writer.writerow(list)` | записывает одну строку |
| `writer.writerows(list_of_lists)` | записывает несколько строк |

## 4. `csv.DictReader` — чтение по названиям столбцов

`row[3]` — непонятно, какой это столбец. `DictReader` отдаёт каждую строку в виде **словаря**, ключи берутся из строки заголовка:

```python
import csv

with open("students.csv", encoding="utf-8", newline="") as f:
    reader = csv.DictReader(f)
    print("Столбцы:", reader.fieldnames)
    for row in reader:
        print(f"{row['name']} ({row['city']}) — {row['score']} баллов")
```

Результат:

```text
Столбцы: ['name', 'grade', 'city', 'score']
Айгерим (Алматы) — 95 баллов
Бауыржан (Астана) — 78 баллов
Даулет (Шымкент) — 88 баллов
Сымбат (Алматы) — 100 баллов
```

### Обработка данных: фильтрация, сортировка, вычисления

```python
import csv

with open("students.csv", encoding="utf-8", newline="") as f:
    students = list(csv.DictReader(f))

# Преобразование: строки в числа
for s in students:
    s["score"] = int(s["score"])
    s["grade"] = int(s["grade"])

almaty = [s["name"] for s in students if s["city"] == "Алматы"]
average = sum(s["score"] for s in students) / len(students)
ranking = sorted(students, key=lambda s: s["score"], reverse=True)

print("Из Алматы:", ", ".join(almaty))
print(f"Средний балл: {average:.2f}")
print("Рейтинг:")
for place, s in enumerate(ranking, start=1):
    print(f"  {place}. {s['name']} — {s['score']}")
```

Результат:

```text
Из Алматы: Айгерим, Сымбат
Средний балл: 90.25
Рейтинг:
  1. Сымбат — 100
  2. Айгерим — 95
  3. Даулет — 88
  4. Бауыржан — 78
```

### Группировка: статистика по городам

```python
import csv

with open("students.csv", encoding="utf-8", newline="") as f:
    rows = list(csv.DictReader(f))

by_city = {}
for row in rows:
    by_city.setdefault(row["city"], []).append(int(row["score"]))

for city, scores in sorted(by_city.items()):
    print(f"{city}: учеников — {len(scores)}, в среднем {sum(scores) / len(scores):.1f}")
```

Результат:

```text
Алматы: учеников — 2, в среднем 97.5
Астана: учеников — 1, в среднем 78.0
Шымкент: учеников — 1, в среднем 88.0
```

## 5. `csv.DictWriter` — запись словарей

```python
import csv

results = [
    {"name": "Айгерим", "subject": "Информатика", "score": 95},
    {"name": "Даулет", "subject": "Математика", "score": 88},
]

with open("olympiad.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "subject", "score"])
    writer.writeheader()          # строка заголовка
    writer.writerows(results)
    writer.writerow({"name": "Сымбат", "subject": "Физика", "score": 100})

with open("olympiad.csv", encoding="utf-8") as f:
    print(f.read())
```

Результат:

```text
name,subject,score
Айгерим,Информатика,95
Даулет,Математика,88
Сымбат,Физика,100
```

`fieldnames` определяет порядок столбцов. Если в словаре есть ключ, которого нет в `fieldnames`, — `ValueError`; наоборот, если в словаре нет какого-то ключа — ячейка остаётся пустой.

### Добавление записи в файл

```python
import csv
import os

FILE = "visits.csv"
FIELDS = ["date", "name", "minutes"]


def add_visit(date, name, minutes):
    """Добавляет запись о посещении кружка; если файл новый, пишет заголовок."""
    is_new = not os.path.exists(FILE)
    with open(FILE, "a", encoding="utf-8", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=FIELDS)
        if is_new:
            writer.writeheader()
        writer.writerow({"date": date, "name": name, "minutes": minutes})


add_visit("2026-12-31", "Айгерим", 90)
add_visit("2026-12-31", "Даулет", 75)
with open(FILE, encoding="utf-8") as f:
    print(f.read())
```

Результат (при первом запуске):

```text
date,name,minutes
2026-12-31,Айгерим,90
2026-12-31,Даулет,75
```

## 6. Совместимость с Excel

В Казахстане Excel чаще всего работает с русскими/казахскими региональными настройками. В них:
- в качестве разделителя ожидается **точка с запятой** `;` (потому что запятая — разделитель дробной части: `3,14`);
- чтобы правильно распознать кириллицу в файле UTF-8, в начале файла нужна метка **BOM** — её даёт `encoding="utf-8-sig"`.

```python
import csv

rows = [["Имя", "Средний балл"], ["Алия", "4,8"], ["Ерасыл", "4,5"]]

with open("for_excel.csv", "w", encoding="utf-8-sig", newline="") as f:
    writer = csv.writer(f, delimiter=";")
    writer.writerows(rows)

with open("for_excel.csv", encoding="utf-8-sig", newline="") as f:
    for row in csv.reader(f, delimiter=";"):
        print(row)
```

Результат:

```text
['Имя', 'Средний балл']
['Алия', '4,8']
['Ерасыл', '4,5']
```

| Параметр | Значение | Пример |
|---|---|---|
| `delimiter` | разделитель столбцов | `","`, `";"`, `"\t"` (TSV) |
| `quotechar` | символ кавычки | `'"'` (по умолчанию) |
| `quoting` | какие значения брать в кавычки | `csv.QUOTE_MINIMAL` (по умолчанию), `csv.QUOTE_ALL` |
| `encoding="utf-8-sig"` | добавление/пропуск BOM | для Excel |

Если разделитель неизвестен, `csv.Sniffer().sniff(sample)` попытается определить его автоматически.

## Частые ошибки

**1. Сравнение чисел как строк.**

```python
scores = ["95", "100", "78"]
print(max(scores))                    # '95' — ошибка! Строки сравниваются по алфавиту
print(max(int(s) for s in scores))    # 100 — правильно
```

Результат:

```text
95
100
```

**2. Нет `newline=""`** → в Windows между строками появляются пустые строки. Правильно: `open(..., newline="")`.

**3. Обработка строки заголовка как данных.** `int("score")` → `ValueError`. Правильно: `next(reader)` или `DictReader`.

**4. Чтение CSV через `split(",")`** → запятая в кавычках всё ломает. Правильно: `csv.reader`.

**5. Пустая строка или пустая ячейка.** Пустая строка в конце файла в `reader` даёт `[]`; пустая ячейка — `''`. Проверка:

```python
import csv
import io

data = io.StringIO("name,score\nАлия,90\n\nЕрлан,\n")
for row in csv.DictReader(data):
    if not row["score"]:
        print(f"{row['name']}: нет балла, пропускаем")
        continue
    print(row["name"], int(row["score"]))
```

Результат:

```text
Алия 90
Ерлан: нет балла, пропускаем
```

(`io.StringIO` позволяет читать строку как «файл»; удобно для проверки. `DictReader` сам пропускает пустые строки.)

**6. «Кракозябры» в Excel** (`Ð�Ð»Ð¸Ñ`) → файл в UTF-8 без BOM. Правильно: `encoding="utf-8-sig"`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| CSV | Текстовая таблица, значения в которой разделены запятой (или другим разделителем) |
| Заголовок (header) | Первая строка с названиями столбцов |
| Разделитель (delimiter) | Символ, разделяющий значения: `,` `;` `\t` |
| Взятие в кавычки (quoting) | Заключение значения, содержащего разделитель, в `"..."` |
| `csv.reader` / `csv.writer` | Чтение/запись строк в виде списков |
| `csv.DictReader` / `csv.DictWriter` | Чтение/запись строк в виде словарей |
| `fieldnames` | Список и порядок столбцов в DictWriter |
| BOM | Метка в начале файла; сообщает Excel, что это UTF-8 |
| TSV | Значения, разделённые табуляцией |

## Примечание

- `csv.reader` не преобразует типы: даже с `quoting=csv.QUOTE_NONNUMERIC` числами становятся только значения без кавычек, а результат всегда `float`. Надёжнее явно вызывать `int()`/`float()` с обработкой `ValueError`.
- `DictReader` при «лишних» ячейках в строке складывает их в список под ключом `None` (меняется параметром `restkey`), а недостающим присваивает `None` (параметр `restval`).
- `DictWriter(..., extrasaction="ignore")` молча пропускает ключи словаря, которых нет в `fieldnames`, вместо `ValueError`.
- Для очень больших таблиц не делайте `list(csv.DictReader(f))` — обрабатывайте строки в цикле `for`, тогда в памяти находится только одна строка.
- Дробные числа в «русском» Excel пишутся через запятую (`4,8`); перед `float()` замените её: `float(value.replace(",", "."))`.
- Документация модуля: https://docs.python.org/3/library/csv.html
