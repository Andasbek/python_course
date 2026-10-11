# Урок 50. Практические задания: работа с CSV-файлами

Во всех заданиях обязательно `import csv` и открытие файлов с `encoding="utf-8", newline=""`.

**Подготовка.** Создайте файл `journal.csv` (в VS Code или в Excel, сохранив как «CSV UTF-8»):

```text
name,class,math,informatics,physics
Айгерим Нурланкызы,9А,5,5,4
Бауыржан Сеитов,9А,3,4,4
Даулет Оспанов,9Б,4,5,5
Сымбат Алиева,9Б,5,5,5
Ерлан Жакыпов,9А,3,3,4
Мадина Касымова,9Б,4,4,3
```

## Базовый уровень

### Задание 1. Вывод журнала в виде таблицы

Прочитай `journal.csv` с помощью `csv.reader` и выведи столбцы в виде выровненной таблицы.

```text
Вывод:
name                 class  math  informatics  physics
Айгерим Нурланкызы   9А     5     5            4
Бауыржан Сеитов      9А     3     4            4
...
```

### Задание 2. Средний балл

С помощью `csv.DictReader` вычисли и выведи средний балл каждого ученика по трём предметам. В конце выведи общий средний балл класса (всех учеников).

```text
Вывод:
Айгерим Нурланкызы: 4.67
Бауыржан Сеитов: 3.67
Даулет Оспанов: 4.67
Сымбат Алиева: 5.00
Ерлан Жакыпов: 3.33
Мадина Касымова: 3.67
Общий средний: 4.17
```

### Задание 3. Запись списка отличников

Учеников, получивших «5» по всем предметам (отличников), и тех, у кого есть хотя бы одна «3», запиши с помощью `csv.writer` в отдельные файлы `excellent.csv` и `need_help.csv` (вместе со строкой заголовка).

```text
excellent.csv:
name,class
Сымбат Алиева,9Б

need_help.csv:
name,class,subjects
Бауыржан Сеитов,9А,math
Ерлан Жакыпов,9А,"math, informatics"
Мадина Касымова,9Б,physics
```

Обрати внимание: значение «math, informatics» `csv.writer` сам берёт в кавычки.

### Задание 4. Список покупок

Пользователь вводит название товара, цену и количество (пустое название — остановка). Программа записывает их в файл `shopping.csv` с помощью `DictWriter` и в конце выводит общую сумму.

```text
Ввод:
Товар: Тетрадь
Цена: 150
Количество: 10
Товар: Ручка
Цена: 120
Количество: 5
Товар:
Вывод:
Итого: 2100 ₸. Список сохранён в файл shopping.csv
```

## Продвинутый уровень

### Задание 5. Отчёт по классам

По `journal.csv` вычисли средний балл каждого класса (9А, 9Б) по каждому предмету и запиши в файл `report.csv`. Чтобы файл правильно открывался в Excel, используй `utf-8-sig` и разделитель `;`, средние значения записывай с одним знаком после запятой.

```text
report.csv:
class;math;informatics;physics
9А;3.7;4.0;4.0
9Б;4.3;4.7;4.3
```

### Задание 6. Поиск

Пользователь вводит часть фамилии или имени (например, «али»). Программа без учёта регистра выводит данные всех подходящих учеников. Если никто не найден — «Не найдено».

### Задание 7. Конвертер CSV

Напиши функцию, меняющую разделитель любого CSV-файла: `convert(src, dst, old=",", new=";")`. Запятые в кавычках не должны пострадать. Проверь: преобразуй файл `need_help.csv` из задания 3.

## ★ Олимпиадная / творческая задача

### Задание 8. Анализ открытых данных

Скачай с порталов data.egov.kz или stat.gov.kz (или возьми файл у педагога) любые CSV-данные по областям Казахстана (численность населения, количество школ и т.д.). С помощью модуля `csv`:
1. выведи названия столбцов и количество записей;
2. найди область с наибольшим и наименьшим значением;
3. создай новый CSV-файл, отсортированный по значениям.

Если файла от педагога нет, используй эти данные (`cities.csv`, тыс. человек, примерно на 2024 г.):

```text
city,region,population
Алматы,г. Алматы,2228
Астана,г. Астана,1423
Шымкент,г. Шымкент,1219
Актобе,Актюбинская обл.,575
Караганда,Карагандинская обл.,505
Тараз,Жамбылская обл.,368
Усть-Каменогорск,Восточно-Казахстанская обл.,368
Павлодар,Павлодарская обл.,363
```

### Задание 9. Свой читатель CSV

Не используя модуль `csv`, напиши функцию `parse_line(line)`, которая разделяет одну строку CSV с учётом правил о кавычках. Тест:

```text
parse_line('Айгерим,"Алматы, Медеу","Шахматы ""Белый король"""')
→ ['Айгерим', 'Алматы, Медеу', 'Шахматы "Белый король"']
```

## Домашнее задание

1. Запиши расходы своей семьи за неделю в файл `expenses.csv` (`date,category,amount`, не менее 10 записей). Программа должна выводить сумму расходов по категориям и категорию, на которую ушло больше всего денег.
2. Открой файл в Excel (или Google Sheets) и проверь, что он отображается правильно. Если нет — найди причину и исправь.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Создание файла подготовки кодом (для проверки)**

```python
import csv

rows = [
    ["name", "class", "math", "informatics", "physics"],
    ["Айгерим Нурланкызы", "9А", 5, 5, 4],
    ["Бауыржан Сеитов", "9А", 3, 4, 4],
    ["Даулет Оспанов", "9Б", 4, 5, 5],
    ["Сымбат Алиева", "9Б", 5, 5, 5],
    ["Ерлан Жакыпов", "9А", 3, 3, 4],
    ["Мадина Касымова", "9Б", 4, 4, 3],
]
with open("journal.csv", "w", encoding="utf-8", newline="") as f:
    csv.writer(f).writerows(rows)
print("journal.csv готов")
```

**Задание 1**

```python
import csv

widths = [21, 7, 6, 13, 8]
with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.reader(f):
        line = "".join(value.ljust(width) for value, width in zip(row, widths))
        print(line.rstrip())
```

**Задание 2**

```python
import csv

SUBJECTS = ["math", "informatics", "physics"]
all_marks = []

with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        marks = [int(row[s]) for s in SUBJECTS]
        all_marks.extend(marks)
        print(f"{row['name']}: {sum(marks) / len(marks):.2f}")

print(f"Общий средний: {sum(all_marks) / len(all_marks):.2f}")
```

Результат:

```text
Айгерим Нурланкызы: 4.67
Бауыржан Сеитов: 3.67
Даулет Оспанов: 4.67
Сымбат Алиева: 5.00
Ерлан Жакыпов: 3.33
Мадина Касымова: 3.67
Общий средний: 4.17
```

**Задание 3**

```python
import csv

SUBJECTS = ["math", "informatics", "physics"]
excellent = [["name", "class"]]
need_help = [["name", "class", "subjects"]]

with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        marks = {s: int(row[s]) for s in SUBJECTS}
        if all(m == 5 for m in marks.values()):
            excellent.append([row["name"], row["class"]])
        weak = [s for s, m in marks.items() if m == 3]
        if weak:
            need_help.append([row["name"], row["class"], ", ".join(weak)])

for filename, rows in [("excellent.csv", excellent), ("need_help.csv", need_help)]:
    with open(filename, "w", encoding="utf-8", newline="") as f:
        csv.writer(f).writerows(rows)
    print(filename, "— записей:", len(rows) - 1)
```

Результат:

```text
excellent.csv — записей: 1
need_help.csv — записей: 3
```

**Задание 4**

```python
import csv

items = []
while True:
    name = input("Товар: ").strip()
    if not name:
        break
    price = int(input("Цена: "))
    qty = int(input("Количество: "))
    items.append({"item": name, "price": price, "qty": qty, "total": price * qty})

with open("shopping.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["item", "price", "qty", "total"])
    writer.writeheader()
    writer.writerows(items)

print(f"Итого: {sum(i['total'] for i in items)} ₸. Список сохранён в файл shopping.csv")
```

**Задание 5**

```python
import csv

SUBJECTS = ["math", "informatics", "physics"]
groups = {}

with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        groups.setdefault(row["class"], []).append(row)

with open("report.csv", "w", encoding="utf-8-sig", newline="") as f:
    writer = csv.writer(f, delimiter=";")
    writer.writerow(["class"] + SUBJECTS)
    for cls, rows in sorted(groups.items()):
        averages = [f"{sum(int(r[s]) for r in rows) / len(rows):.1f}" for s in SUBJECTS]
        writer.writerow([cls] + averages)

with open("report.csv", encoding="utf-8-sig") as f:
    print(f.read())
```

Результат:

```text
class;math;informatics;physics
9А;3.7;4.0;4.0
9Б;4.3;4.7;4.3
```

**Задание 6**

```python
import csv

query = input("Поиск: ").strip().lower()
found = False
with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        if query in row["name"].lower():
            print(", ".join(f"{k}: {v}" for k, v in row.items()))
            found = True
if not found:
    print("Не найдено")
```

**Задание 7**

```python
import csv


def convert(src, dst, old=",", new=";"):
    """Меняет разделитель CSV-файла, сохраняя кавычки."""
    with open(src, encoding="utf-8", newline="") as fin, \
         open(dst, "w", encoding="utf-8", newline="") as fout:
        writer = csv.writer(fout, delimiter=new)
        for row in csv.reader(fin, delimiter=old):
            writer.writerow(row)


with open("need_help.csv", "w", encoding="utf-8", newline="") as f:
    csv.writer(f).writerows([["name", "subjects"], ["Ерлан", "math, informatics"]])
convert("need_help.csv", "need_help_semicolon.csv")
with open("need_help_semicolon.csv", encoding="utf-8") as f:
    print(f.read())
```

Результат:

```text
name;subjects
Ерлан;math, informatics
```

Примечание: поскольку новый разделитель — `;`, значение «math, informatics» теперь брать в кавычки не нужно — `csv.writer` решает это сам.

**Задание 8 (по данному cities.csv)**

```python
import csv

data = """city,region,population
Алматы,г. Алматы,2228
Астана,г. Астана,1423
Шымкент,г. Шымкент,1219
Актобе,Актюбинская обл.,575
Караганда,Карагандинская обл.,505
Тараз,Жамбылская обл.,368
Усть-Каменогорск,Восточно-Казахстанская обл.,368
Павлодар,Павлодарская обл.,363
"""
with open("cities.csv", "w", encoding="utf-8", newline="") as f:
    f.write(data)

with open("cities.csv", encoding="utf-8", newline="") as f:
    reader = csv.DictReader(f)
    rows = list(reader)
    print("Столбцы:", reader.fieldnames, "| Записей:", len(rows))

for row in rows:
    row["population"] = int(row["population"])

biggest = max(rows, key=lambda r: r["population"])
smallest = min(rows, key=lambda r: r["population"])
print("Наибольшее:", biggest["city"], biggest["population"])
print("Наименьшее:", smallest["city"], smallest["population"])

rows.sort(key=lambda r: r["population"], reverse=True)
with open("cities_sorted.csv", "w", encoding="utf-8", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["city", "region", "population"])
    writer.writeheader()
    writer.writerows(rows)
```

Результат:

```text
Столбцы: ['city', 'region', 'population'] | Записей: 8
Наибольшее: Алматы 2228
Наименьшее: Павлодар 363
```

**Задание 9**

```python
def parse_line(line, sep=","):
    """Разделяет строку CSV с учётом правил о кавычках (конечный автомат)."""
    result, value = [], ""
    in_quotes = False
    i = 0
    while i < len(line):
        ch = line[i]
        if in_quotes:
            if ch == '"':
                if i + 1 < len(line) and line[i + 1] == '"':
                    value += '"'      # двойная кавычка → одна кавычка
                    i += 1
                else:
                    in_quotes = False
            else:
                value += ch
        elif ch == '"':
            in_quotes = True
        elif ch == sep:
            result.append(value)
            value = ""
        else:
            value += ch
        i += 1
    result.append(value)
    return result


print(parse_line('Айгерим,"Алматы, Медеу","Шахматы ""Белый король"""'))
```

Результат:

```text
['Айгерим', 'Алматы, Медеу', 'Шахматы "Белый король"']
```

</details>
