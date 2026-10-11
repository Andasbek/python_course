# Урок 61. Практические задания: мини-проект «Успеваемость учеников»

> **Практикум.** Работа выполняется индивидуально или в парах. Результат: программа `grades_report.py`, файл отчёта `report.txt` и отдельные файлы для каждого класса.

## Мини-проект: «Отчёт электронного журнала»

### Шаг 1. Подготовка файла данных

Открой в Thonny новый файл и сохрани следующее содержимое под именем `students.txt` в папку с программой (UTF-8):

```text
Имя;Фамилия;Класс;Математика;Информатика;Казахский язык
Айгерим;Сейитова;7А;5;5;5
Нурлан;Жаксылыков;7А;4;5;3
Дана;Абилова;7Б;5;4;5
Ерасыл;Толегенов;7Б;3;4;4
Мадина;Ахметова;7А;4;4;5
Арман;Курманов;7Б;5;5;4
Алия;Бекова;7А;3;3;4
Санжар;Оразов;7Б;4;5;5
```

Добавь 2–3 придуманных ученика из своего класса (не записывай оценки реальных людей!).

### Шаг 2. «Каркас» программы

Создай файл `grades_report.py` и напиши пустой «каркас» функций: `average()`, `read_students()`, `write_report()`. Убедись, что программа запускается и выводит «Готово!».

### Шаг 3. Чтение файла

Напиши функцию `read_students(file_name)`:
- пропусти строку заголовка;
- раздели каждую строку с помощью `split(";")`;
- добавляй каждого ученика в список как словарь с ключами `{"name", "surname", "class", "grades", "avg"}`.

Проверка: `print(len(students))` → `8` (или больше с добавленными тобой учениками).

### Шаг 4. Статистика

Вычисли:
- среднюю оценку каждого ученика;
- среднюю оценку класса по каждому предмету;
- общую среднюю оценку;
- отличников (среднее ≥ 4.5).

Проверка: среднее у Айгерим – 5.00, у Ерасыла – 3.67 (посчитай вручную и сравни).

### Шаг 5. Определение уровня

Функция `level(avg)`: ≥ 4.5 → «отличник», ≥ 3.5 → «хорошист», остальные → «нужно дополнительное внимание».

### Шаг 6. Запись файла отчёта

Функция `write_report(students, "report.txt")` должна отсортировать учеников по средней оценке в порядке убывания и записать их в виде таблицы. Ожидаемый `report.txt`:

```text
ОТЧЁТ ОБ УСПЕВАЕМОСТИ УЧЕНИКОВ
====================================================
№  Имя и фамилия         Класс  Среднее  Уровень
----------------------------------------------------
1  Айгерим Сейитова      7А        5.00  отличник
2  Дана Абилова          7Б        4.67  отличник
3  Арман Курманов        7Б        4.67  отличник
4  Санжар Оразов         7Б        4.67  отличник
5  Мадина Ахметова       7А        4.33  хорошист
6  Нурлан Жаксылыков     7А        4.00  хорошист
7  Ерасыл Толегенов      7Б        3.67  хорошист
8  Алия Бекова           7А        3.33  нужно дополнительное внимание
----------------------------------------------------
Среднее по предмету Математика: 4.12
Среднее по предмету Информатика: 4.38
Среднее по предмету Казахский язык: 4.38
Общая средняя оценка: 4.29
Отличники: Айгерим, Дана, Арман, Санжар
```

Подсказка: для сортировки – `sorted(students, key=get_avg, reverse=True)`, где `get_avg` – твоя функция, которая принимает словарь ученика и возвращает его значение `"avg"`.

### Шаг 7. Файлы по классам и устойчивость к ошибкам

- Запиши каждый класс в отдельный файл: `class_7А.txt`, `class_7Б.txt` (`Имя Фамилия – среднее`).
- Если файла нет: «Файл students.txt не найден!».
- Повреждённые строки («н» вместо оценки, количество полей не равно 6) должны пропускаться, а их номера – выводиться на экран. Для проверки добавь в конец файла строку `Алия;Бекова;7А;3;н;4`.

Ожидаемый результат на экране:
```text
Количество учеников: 8
report.txt готов. Файлы классов: ['7А', '7Б']
```

## Базовый уровень (задания для поэтапного выполнения практикума)

Ученики, которые не успевают выполнить мини-проект полностью, начинают с этих шагов:

1. Прочитай файл `students.txt` и выведи на экран только имя и фамилию каждого ученика (без строки заголовка).
2. Выведи на экран среднее трёх оценок каждого ученика: `Айгерим – 5.00`.
3. Запиши результат в файл `averages.txt`.
4. Запиши список учеников, получивших 5 по «Информатике», в файл `informatics_5.txt`.

## Продвинутый уровень

5. **Выбор предмета.** Пользователь вводит название предмета, программа выводит учеников с самой высокой оценкой по этому предмету и среднее класса. Если название предмета неверное – понятное сообщение.
6. **Сравнение классов.** Вычисли среднюю оценку классов 7А и 7Б и добавь в отчёт, какой класс лидирует.
7. **Исправление оценки.** Пользователь вводит имя ученика, предмет и новую оценку; программа **обновляет** файл `students.txt` и сохраняет его заново (строка заголовка должна сохраниться!).

## ★ Олимпиадная / творческая задача

8. **Текстовая диаграмма.** Добавь в отчёт среднюю оценку каждого ученика в виде «столбчатой диаграммы» из звёздочек (1 балл = 4 звёздочки):
```text
Айгерим  ********************  5.00
Алия     *************         3.33
```
9. **HTML-отчёт.** Запиши отчёт в файл `report.html` в виде таблицы `<table>`, строки отличников окрась в зелёный цвет (`<tr style="background:#c8f7c5">`). Открой файл в браузере.

## Домашнее задание

1. Создай файл данных по своим интересам (например: футбольные команды и очки, книги и количество страниц, города и температура) – не менее 8 строк, 3–4 столбца.
2. По схеме этого практикума напиши программу, которая читает его, вычисляет не менее 3 статистических показателей и записывает файл отчёта.

## Критерии оценивания

| Критерий | Описание | Баллы |
|---|---|---|
| Правильная работа программы и соответствие требованиям задания | Файл читается, средние значения верны, `report.txt` и файлы классов создаются | 40 |
| Эффективность алгоритма, рациональность решения | Разбиение на функции, данные в словаре/списке, нет повторяющегося кода, ошибки обрабатываются | 25 |
| Читаемость кода | Понятные имена, комментарии, красивое оформление отчёта | 20 |
| Самостоятельность и своевременность выполнения | Завершил сам до конца урока | 15 |
| **Итого** | | **100** |

Уровни: 85–100 – высокий; 60–84 – средний; 40–59 – начальный.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Полное решение мини-проекта (шаги 1–7):**

```python
SUBJECTS = ["Математика", "Информатика", "Казахский язык"]


def average(grades):
    """Среднее значение списка оценок."""
    return sum(grades) / len(grades)


def read_students(file_name):
    """Читает учеников из файла и возвращает список словарей."""
    students = []
    bad_lines = []
    with open(file_name, encoding="utf-8") as f:
        f.readline()                              # строка заголовка
        line_no = 1
        for line in f:
            line_no += 1
            parts = line.strip().split(";")
            if len(parts) != 6:
                if line.strip():
                    bad_lines.append(line_no)
                continue
            try:
                grades = [int(parts[3]), int(parts[4]), int(parts[5])]
            except ValueError:
                bad_lines.append(line_no)
                continue
            students.append({
                "name": parts[0].strip(),
                "surname": parts[1].strip(),
                "class": parts[2].strip(),
                "grades": grades,
                "avg": average(grades),
            })
    return students, bad_lines


def level(avg):
    """Уровень успеваемости по средней оценке."""
    if avg >= 4.5:
        return "отличник"
    elif avg >= 3.5:
        return "хорошист"
    return "нужно дополнительное внимание"


def get_avg(student):
    """Ключ сортировки: средняя оценка ученика."""
    return student["avg"]


def write_report(students, file_name):
    """Записывает отчёт в файл."""
    ranked = sorted(students, key=get_avg, reverse=True)
    with open(file_name, "w", encoding="utf-8") as f:
        print("ОТЧЁТ ОБ УСПЕВАЕМОСТИ УЧЕНИКОВ", file=f)
        print("=" * 52, file=f)
        print(f"{'№':<3}{'Имя и фамилия':<22}{'Класс':<7}{'Среднее':>7}  Уровень", file=f)
        print("-" * 52, file=f)
        place = 1
        for s in ranked:
            full_name = s["name"] + " " + s["surname"]
            print(f"{place:<3}{full_name:<22}{s['class']:<7}{s['avg']:>7.2f}  {level(s['avg'])}", file=f)
            place += 1
        print("-" * 52, file=f)
        for i in range(len(SUBJECTS)):
            marks = [s["grades"][i] for s in students]
            print(f"Среднее по предмету {SUBJECTS[i]}: {average(marks):.2f}", file=f)
        all_avg = average([s["avg"] for s in students])
        print(f"Общая средняя оценка: {all_avg:.2f}", file=f)
        best = [s["name"] for s in students if s["avg"] >= 4.5]
        print("Отличники:", ", ".join(best), file=f)


def write_by_class(students):
    """Записывает каждый класс в отдельный файл: class_7А.txt и т.д."""
    classes = {}
    for s in students:
        if s["class"] not in classes:
            classes[s["class"]] = []
        classes[s["class"]].append(s)
    for class_name, group in classes.items():
        with open(f"class_{class_name}.txt", "w", encoding="utf-8") as f:
            for s in group:
                print(f"{s['name']} {s['surname']} – {s['avg']:.2f}", file=f)
    return list(classes.keys())


try:
    students, bad = read_students("students.txt")
except FileNotFoundError:
    print("Файл students.txt не найден!")
else:
    if bad:
        print("Пропущены ошибочные строки:", bad)
    write_report(students, "report.txt")
    names = write_by_class(students)
    print("Количество учеников:", len(students))
    print("report.txt готов. Файлы классов:", names)
```

**Базовые задания 1–4:**

```python
averages = []
with open("students.txt", encoding="utf-8") as f:
    f.readline()
    for line in f:
        parts = line.strip().split(";")
        if len(parts) != 6:
            continue
        name, surname = parts[0], parts[1]
        grades = [int(parts[3]), int(parts[4]), int(parts[5])]
        avg = sum(grades) / 3
        print(name, surname)
        averages.append(f"{name} – {avg:.2f}")
        if grades[1] == 5:
            with open("informatics_5.txt", "a", encoding="utf-8") as out:
                print(name, surname, file=out)

with open("averages.txt", "w", encoding="utf-8") as f:
    for row in averages:
        print(row, file=f)
```

Примечание: так как для `informatics_5.txt` используется режим `a`, при повторном запуске программы строки дублируются – обсуди это с учениками и покажи вариант, где список собирается, а в конце записывается в режиме `w`.

**Задание 5 (с использованием функций мини-проекта):**

```python
SUBJECTS = ["Математика", "Информатика", "Казахский язык"]
subject = input("Предмет: ").strip()
if subject not in SUBJECTS:
    print("Такого предмета нет. Выбери:", ", ".join(SUBJECTS))
else:
    index = SUBJECTS.index(subject)
    marks = {}
    with open("students.txt", encoding="utf-8") as f:
        f.readline()
        for line in f:
            parts = line.strip().split(";")
            if len(parts) == 6:
                marks[parts[0]] = int(parts[3 + index])
    top = max(marks.values())
    leaders = [name for name, m in marks.items() if m == top]
    print("Самая высокая оценка:", top, "–", ", ".join(leaders))
    print(f"Среднее: {sum(marks.values()) / len(marks):.2f}")
```

**Задание 6.** Сгруппировать по классам и вычислить среднее значений `avg` каждой группы (можно повторно использовать словарь `classes` из `write_by_class`). По данным: 7А – 4.17, 7Б – 4.42 → лидирует 7Б.

**Задание 7:**

```python
SUBJECTS = ["Математика", "Информатика", "Казахский язык"]
name = input("Имя ученика: ")
subject = input("Предмет: ")
new_grade = input("Новая оценка: ")
with open("students.txt", encoding="utf-8") as f:
    lines = f.readlines()
changed = False
for i in range(1, len(lines)):
    parts = lines[i].strip().split(";")
    if len(parts) == 6 and parts[0] == name and subject in SUBJECTS:
        parts[3 + SUBJECTS.index(subject)] = new_grade
        lines[i] = ";".join(parts) + "\n"
        changed = True
with open("students.txt", "w", encoding="utf-8") as f:
    f.writelines(lines)
print("Обновлено." if changed else "Ученик или предмет не найден.")
```

**Задание 8 (фрагмент, добавляемый в отчёт):**

```python
students = [{"name": "Айгерим", "avg": 5.0}, {"name": "Алия", "avg": 10 / 3}]
for s in students:
    bar = "*" * round(s["avg"] * 4)
    print(f"{s['name']:<9}{bar:<21}{s['avg']:.2f}")
```

**Задание 9:**

```python
rows = [("Айгерим Сейитова", "7А", 5.0), ("Алия Бекова", "7А", 3.33)]
with open("report.html", "w", encoding="utf-8") as f:
    print('<html><head><meta charset="utf-8"><title>Отчёт</title></head><body>', file=f)
    print("<h1>Успеваемость учеников</h1><table border='1'>", file=f)
    print("<tr><th>Имя и фамилия</th><th>Класс</th><th>Среднее</th></tr>", file=f)
    for full_name, class_name, avg in rows:
        style = ' style="background:#c8f7c5"' if avg >= 4.5 else ""
        print(f"<tr{style}><td>{full_name}</td><td>{class_name}</td><td>{avg:.2f}</td></tr>", file=f)
    print("</table></body></html>", file=f)
```

(В полной версии вместо `rows` используется результат `read_students()`.)

</details>
