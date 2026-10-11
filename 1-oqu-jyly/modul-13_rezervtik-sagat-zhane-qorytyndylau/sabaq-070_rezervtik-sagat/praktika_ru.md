# Урок 70. Практические задания: восполнение пропущенных тем (сводный сборник по модулям)

> Педагог выбирает разделы, соответствующие пропущенным темам. Если все темы пройдены – каждый ученик выбирает 3–4 задания из своего самого слабого модуля.

## Быстрая диагностика (в начале урока, 7 мин)

Выбери один ответ на каждый вопрос:
1. Результат `print(7 // 2, 7 % 2)`: а) `3.5 1` б) `3 1` в) `3 0`
2. Какие числа даёт `range(2, 8, 2)`? а) 2,4,6,8 б) 2,4,6 в) 2,3,...,7
3. Результат `"Алматы"[-1]`: а) `А` б) `ы` в) ошибка
4. Результат `d = {"a": 1}; d.get("b", 0)`: а) `None` б) `0` в) `KeyError`
5. Что возвращает функция, если в ней нет `return`? а) `0` б) `None` в) ошибка

## Базовый уровень

### Модули I–II: переменные и типы

**1.** Пользователь вводит количество секунд. Переведи их в часы, минуты, секунды.

```text
Секунд: 3725
1 ч 2 мин 5 с
```

**2.** «Чек магазина»: вводятся название товара, цена, количество. Красиво выведи чек с помощью f-строк, отдельно покажи НДС 12% (считай, что цена включает НДС: НДС = сумма × 12 / 112).

### III модуль: turtle

**3.** Нарисуй с помощью `turtle` дом: квадратная стена (с заливкой), треугольная крыша, окно.

### IV модуль: ветвление

**4.** Вводится год. Високосный ли он? (Делится на 4 и не делится на 100, или делится на 400.)

```text
Год: 2028
2028 – високосный год
```

### V модуль: циклы

**5.** Выведи из чисел от 1 до 100 те, что делятся на 7 или содержат цифру 7 (игра «Семь»).

**6.** Пользователь вводит числа, пока не введёт 0. Выведи сумму положительных чисел и количество отрицательных.

### Модули VI–VII: списки и строки

**7.** Список: `temps = [-5, 2, 7, -1, 0, 12, 4]`. Выведи список положительных температур (генератором списка), номер самого тёплого и самого холодного дня.

**8.** Вводится предложение. Выведи самое длинное слово, количество слов и сокращение (аббревиатуру) из первых букв каждого слова.

```text
Предложение: центр детского научного творчества
Самое длинное слово: творчества
Количество слов: 4
Аббревиатура: ЦДНТ
```

### VIII модуль: словари и множества

**9.** Посчитай с помощью словаря, сколько раз встречается каждое слово в тексте, и выведи самое частое слово.

**10.** Списки кружков двух классов заданы как множества. Выведи кружки, которые есть в обоих классах, кружки, которые есть только в первом классе, и все кружки.

### IX модуль: функции

**11.** Функция `is_palindrome(word)`: возвращает `True`, если слово – палиндром (без учёта регистра и пробелов). Проверь: «Шалаш», «Python», «А роза упала на лапу Азора».

**12.** Функция `power(base, exp=2)`: вычисляет степень с помощью цикла (без `**`). Напиши и рекурсивный вариант.

### Модули X–XI: ошибки, файлы, модули

**13.** Запиши в файл `numbers.txt` 10 случайных чисел от 1 до 20 (`random`). Затем прочитай файл и выведи сумму чисел и наибольшее. Если файла нет – сообщение.

**14.** Пользователь вводит год рождения. С помощью `datetime` получи текущий год и вычисли возраст. Ввод не-числа – `try...except`.

## Продвинутый уровень

**15.** «Журнал оценок» (модули VI, VIII, IX, X): создай словарь `{ученик: [оценки]}`, напиши функции – вычисление среднего, поиск отличника, запись журнала в файл.

**16.** «Статистика текста» (VII, VIII, X): посчитай буквы, слова, предложения (по `.`, `!`, `?`) текста из файла; выведи 3 самые частые буквы.

## ★ Олимпиадная / творческая задача

**17.** «Числовая спираль»: вводится n, заполни таблицу n×n числами от 1 до n² по спирали и выведи её (вложенный список и циклы).

```text
n = 4
 1  2  3  4
12 13 14  5
11 16 15  6
10  9  8  7
```

## Домашнее задание

1. Перечитай шпаргалку (файл теории) модуля, который ты освоил хуже всего, и выполни 2 задания из этого модуля.
2. Подготовь к следующему уроку 2–3 вопроса по самой непонятной за год теме – обсудим на индивидуальной консультации.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Диагностика:** 1 – б; 2 – б; 3 – б; 4 – б; 5 – б.

**1.**

```python
seconds = int(input("Секунд: "))
hours = seconds // 3600
minutes = seconds % 3600 // 60
secs = seconds % 60
print(f"{hours} ч {minutes} мин {secs} с")
```

**2.**

```python
name = input("Товар: ")
price = float(input("Цена: "))
count = int(input("Количество: "))
total = price * count
vat = total * 12 / 112
print("=" * 30)
print(f"{name:<15}{count:>3} x {price:.2f}")
print(f"{'Итого:':<15}{total:>12.2f}")
print(f"{'в т.ч. НДС:':<15}{vat:>12.2f}")
print("=" * 30)
```

**3.**

```python
import turtle

t = turtle.Turtle()
t.fillcolor("wheat")
t.begin_fill()
for _ in range(4):
    t.forward(150)
    t.left(90)
t.end_fill()
# крыша
t.penup(); t.goto(0, 150); t.pendown()
t.fillcolor("brown")
t.begin_fill()
t.goto(75, 230)
t.goto(150, 150)
t.goto(0, 150)
t.end_fill()
# окно
t.penup(); t.goto(50, 60); t.pendown()
t.fillcolor("lightblue")
t.begin_fill()
for _ in range(4):
    t.forward(50)
    t.left(90)
t.end_fill()
turtle.done()
```

**4.**

```python
year = int(input("Год: "))
if (year % 4 == 0 and year % 100 != 0) or year % 400 == 0:
    print(year, "– високосный год")
else:
    print(year, "– не високосный год")
```

**5.**

```python
for n in range(1, 101):
    if n % 7 == 0 or "7" in str(n):
        print(n, end=" ")
print()
```

**6.**

```python
positive_sum = 0
negative_count = 0
while True:
    n = int(input("Число (0 – остановить): "))
    if n == 0:
        break
    if n > 0:
        positive_sum += n
    else:
        negative_count += 1
print("Сумма положительных чисел:", positive_sum)
print("Количество отрицательных чисел:", negative_count)
```

**7.**

```python
temps = [-5, 2, 7, -1, 0, 12, 4]
print("Положительные:", [t for t in temps if t > 0])
print("Самый тёплый день:", temps.index(max(temps)) + 1)
print("Самый холодный день:", temps.index(min(temps)) + 1)
```

**8.**

```python
sentence = input("Предложение: ")
words = sentence.split()
longest = ""
for w in words:
    if len(w) > len(longest):
        longest = w
abbr = "".join([w[0].upper() for w in words])
print("Самое длинное слово:", longest)
print("Количество слов:", len(words))
print("Аббревиатура:", abbr)
```

**9.**

```python
text = "мы изучаем python python интересный мы программисты"
counts = {}
for word in text.lower().split():
    counts[word] = counts.get(word, 0) + 1
best = ""
for word, cnt in counts.items():
    if best == "" or cnt > counts[best]:
        best = word
print(counts)
print("Самое частое:", best, counts[best])
```

**10.**

```python
class_a = {"Python", "шахматы", "робототехника", "домбра"}
class_b = {"Python", "футбол", "домбра", "рисование"}
print("В обоих:", class_a & class_b)
print("Только в 1-м классе:", class_a - class_b)
print("Все:", class_a | class_b)
```

**11.**

```python
def is_palindrome(word):
    clean = word.lower().replace(" ", "")
    return clean == clean[::-1]


print(is_palindrome("Шалаш"), is_palindrome("Python"),
      is_palindrome("А роза упала на лапу Азора"))
```

Результат:
```text
True False True
```

**12.**

```python
def power(base, exp=2):
    result = 1
    for _ in range(exp):
        result *= base
    return result


def power_rec(base, exp=2):
    if exp == 0:
        return 1
    return base * power_rec(base, exp - 1)


print(power(3), power(2, 10), power_rec(2, 10))
```

**13.**

```python
import random

with open("numbers.txt", "w", encoding="utf-8") as f:
    for _ in range(10):
        f.write(str(random.randint(1, 20)) + "\n")

try:
    with open("numbers.txt", encoding="utf-8") as f:
        numbers = [int(line) for line in f if line.strip()]
    print("Числа:", numbers)
    print("Сумма:", sum(numbers), "Наибольшее:", max(numbers))
except FileNotFoundError:
    print("Файл не найден")
```

**14.**

```python
from datetime import date

try:
    year = int(input("Год рождения: "))
    print("Возраст (после дня рождения в этом году):", date.today().year - year)
except ValueError:
    print("Напиши год цифрами!")
```

**15.**

```python
journal = {"Айгерим": [5, 5, 4], "Нурлан": [4, 3, 4], "Дана": [5, 4, 5]}


def average(grades):
    return sum(grades) / len(grades)


def best_student(journal):
    best = ""
    for name in journal:
        if best == "" or average(journal[name]) > average(journal[best]):
            best = name
    return best


def save_journal(journal, file_name):
    with open(file_name, "w", encoding="utf-8") as f:
        for name, grades in journal.items():
            f.write(f"{name};{' '.join(map(str, grades))};{average(grades):.2f}\n")


print("Отличник:", best_student(journal))
save_journal(journal, "journal.txt")
```

**16.**

```python
try:
    with open("text.txt", encoding="utf-8") as f:
        text = f.read()
except FileNotFoundError:
    text = "Python – отличный язык! Мы его изучаем. А ты изучаешь?"

letters = [ch for ch in text.lower() if ch.isalpha()]
words = text.split()
sentences = 0
for ch in text:
    if ch in ".!?":
        sentences += 1
counts = {}
for ch in letters:
    counts[ch] = counts.get(ch, 0) + 1
top = sorted(counts.items(), key=lambda pair: pair[1], reverse=True)[:3]
print("Букв:", len(letters), "Слов:", len(words), "Предложений:", sentences)
print("3 самые частые буквы:", top)
```

(`lambda` – дополнительное пояснение для сильных учеников: «однострочная безымянная функция»; вместо неё можно написать `def get_count(pair): return pair[1]`.)

**17.**

```python
n = int(input("n = "))
grid = [[0] * n for _ in range(n)]
top, bottom, left, right = 0, n - 1, 0, n - 1
num = 1
while num <= n * n:
    for c in range(left, right + 1):
        grid[top][c] = num
        num += 1
    top += 1
    for r in range(top, bottom + 1):
        grid[r][right] = num
        num += 1
    right -= 1
    for c in range(right, left - 1, -1):
        grid[bottom][c] = num
        num += 1
    bottom -= 1
    for r in range(bottom, top - 1, -1):
        grid[r][left] = num
        num += 1
    left += 1
for row in grid:
    print(" ".join(f"{x:2}" for x in row))
```

</details>
