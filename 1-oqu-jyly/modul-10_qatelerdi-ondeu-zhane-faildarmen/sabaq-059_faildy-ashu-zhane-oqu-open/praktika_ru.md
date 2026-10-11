# Урок 59. Практические задания: открытие и чтение файла

> **Подготовка.** Создай нужные для заданий файлы в Thonny (File → New) или в Блокноте и сохрани их **в одну папку** со своей программой `.py` в кодировке UTF-8.

`proverbs.txt` (казахские пословицы в переводе):
```text
Искусство слова – первое из искусств.
Знание – неисчерпаемый клад.
Кто трудится без лени, тот сыт без просьб.
Учиться – что иглой колодец копать.
Много слов – уголь, мало слов – золото.
Где единство, там и жизнь.
```

`grades.txt` (имя ученика и оценка):
```text
Айгерим 5
Нурлан 4
Дана 5
Ерасыл 3
Мадина 4
Арман 5
```

`temperature.txt` (Алматы, дневная температура за неделю, °C):
```text
12.5
14
9.5
11
16.5
18
15
```

## Базовый уровень

**Задание 1. Книга пословиц.** Прочитай файл `proverbs.txt` с помощью `read()` и выведи на экран. Затем выведи количество символов и количество слов в файле.

```text
Искусство слова – первое из искусств.
...
Количество символов: 213
Количество слов: 37
```

(Числа могут немного отличаться в зависимости от того, как ты набрал файл.)

**Задание 2. Пронумерованные пословицы.** Прочитай тот же файл построчно с помощью `for line in f` и выведи каждую пословицу с номером и без пустых строк.

```text
1. Искусство слова – первое из искусств.
2. Знание – неисчерпаемый клад.
...
```

**Задание 3. Первая строка.** С помощью `readline()` прочитай только первую строку файла и выведи её заглавными буквами (`upper()`). Чтобы найти последнюю пословицу, используй `readlines()`.

**Задание 4. Недельная температура.** Прочитай файл `temperature.txt` и выведи среднюю, самую высокую и самую низкую температуру за неделю.

```text
Средняя: 13.79 °C
Самая тёплая: 18.0 °C
Самая холодная: 9.5 °C
```

**Задание 5. Безопасное открытие.** Спроси у пользователя имя файла и выведи, сколько в нём строк. Если файл не найден – напиши «Файл не найден» (`try...except FileNotFoundError`).

## Продвинутый уровень

**Задание 6. Журнал оценок.** Прочитай файл `grades.txt` и:
- выведи каждого ученика в виде «Имя – оценка»;
- вычисли среднюю оценку класса;
- выведи список учеников, получивших «5»;
- собери учеников в словарь (`{имя: оценка}`).

```text
Отличники: Айгерим, Дана, Арман
Средняя оценка: 4.33
```

**Задание 7. Поиск слова.** Пользователь вводит слово. Программа выводит пословицы из `proverbs.txt`, в которых встречается это слово, с номером строки (без учёта регистра). Если не найдено – «Не найдено».

```text
Слово для поиска: знание
Строка 2: Знание – неисчерпаемый клад.
```

**Задание 8. Повреждённые данные.** Специально добавь в файл `temperature.txt` строку `пятнадцать` и пустую строку. Измени задание 4 так, чтобы программа не падала: пропускай ошибочные строки и в конце сообщи их количество.

## ★ Олимпиадная / творческая задача

**Задание 9. Частота букв.** Посчитай с помощью словаря, сколько раз встречается каждая буква в файле `proverbs.txt` (знаки препинания и пробелы не учитывай, заглавные и строчные считай одинаковыми). Выведи 5 самых частых букв в порядке убывания.

**Задание 10. Викторина из файла.** Создай файл `quiz.txt`: в каждой строке `вопрос|ответ` (не менее 5 вопросов, например `Столица Казахстана?|Астана`). Программа читает вопросы из файла, спрашивает ответ у пользователя и в конце выводит количество баллов. При сравнении ответа не учитывай пробелы и регистр.

## Домашнее задание

1. Создай файл `my_poem.txt` из 5–10 понравившихся тебе строк стихотворения. Программа должна выводить количество строк, самую длинную строку и самое частое слово.
2. Своими словами в 3–4 предложениях опиши разницу между конструкцией `with` и методом `close()`.
3. Подумай к следующему уроку: как можно было бы сохранить результат программы (например, среднюю оценку) в файл?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.**

```python
with open("proverbs.txt", encoding="utf-8") as f:
    text = f.read()
print(text)
print("Количество символов:", len(text))
print("Количество слов:", len(text.split()))
```

Примечание: `split()` считает «словом» и тире (–); это можно обсудить с учениками.

**Задание 2.**

```python
with open("proverbs.txt", encoding="utf-8") as f:
    number = 1
    for line in f:
        print(f"{number}. {line.strip()}")
        number += 1
```

**Задание 3.**

```python
with open("proverbs.txt", encoding="utf-8") as f:
    first = f.readline()
print(first.strip().upper())

with open("proverbs.txt", encoding="utf-8") as f:
    lines = f.readlines()
print("Последняя пословица:", lines[-1].strip())
```

**Задание 4.**

```python
temps = []
with open("temperature.txt", encoding="utf-8") as f:
    for line in f:
        if line.strip():
            temps.append(float(line))
print(f"Средняя: {sum(temps) / len(temps):.2f} °C")
print("Самая тёплая:", max(temps), "°C")
print("Самая холодная:", min(temps), "°C")
```

**Задание 5.**

```python
name = input("Имя файла: ")
try:
    with open(name, encoding="utf-8") as f:
        lines = f.readlines()
    print("Количество строк:", len(lines))
except FileNotFoundError:
    print("Файл не найден")
```

**Задание 6.**

```python
journal = {}
with open("grades.txt", encoding="utf-8") as f:
    for line in f:
        parts = line.split()
        if len(parts) == 2:
            journal[parts[0]] = int(parts[1])

for name, grade in journal.items():
    print(f"{name} – {grade}")
best = [name for name, grade in journal.items() if grade == 5]
print("Отличники:", ", ".join(best))
print(f"Средняя оценка: {sum(journal.values()) / len(journal):.2f}")
```

**Задание 7.**

```python
word = input("Слово для поиска: ").strip().lower()
found = False
with open("proverbs.txt", encoding="utf-8") as f:
    number = 0
    for line in f:
        number += 1
        if word in line.lower():
            print(f"Строка {number}: {line.strip()}")
            found = True
if not found:
    print("Не найдено")
```

**Задание 8.**

```python
temps = []
bad = 0
with open("temperature.txt", encoding="utf-8") as f:
    for line in f:
        try:
            temps.append(float(line))
        except ValueError:
            bad += 1
if temps:
    print(f"Средняя: {sum(temps) / len(temps):.2f} °C")
    print("Самая тёплая:", max(temps), "°C")
    print("Самая холодная:", min(temps), "°C")
print("Пропущено ошибочных строк:", bad)
```

**Задание 9.**

```python
counts = {}
with open("proverbs.txt", encoding="utf-8") as f:
    for ch in f.read().lower():
        if ch.isalpha():
            counts[ch] = counts.get(ch, 0) + 1

pairs = []
for letter, cnt in counts.items():
    pairs.append([cnt, letter])
pairs.sort(reverse=True)
for cnt, letter in pairs[:5]:
    print(letter, "-", cnt)
```

**Задание 10.**

```python
score = 0
total = 0
with open("quiz.txt", encoding="utf-8") as f:
    for line in f:
        if "|" not in line:
            continue
        question, answer = line.strip().split("|")
        total += 1
        user = input(question + " ")
        if user.strip().lower() == answer.strip().lower():
            print("Верно!")
            score += 1
        else:
            print("Неверно. Правильный ответ:", answer)
print(f"Результат: {score}/{total}")
```

</details>
