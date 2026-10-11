# Урок 63. Практические задания: модуль random

## Базовый уровень

**Задание 1. Два кубика.** Брось два кубика и выведи значение каждого и их сумму. Если выпали одинаковые – напиши «Дубль!».

```text
Кубик 1: 4
Кубик 2: 4
Сумма: 8
Дубль!
```

**Задание 2. Выбор дежурного.** Составь список из 8 учеников класса. Программа должна:
а) выбрать одного дежурного на сегодня (`choice`);
б) выбрать 5 разных дежурных на 5 дней недели (`sample`) и вывести в виде «Понедельник – Дана».

**Задание 3. Генератор погоды.** Выведи для 7 дней недели случайную температуру (`uniform(-10, 10)`, с одним знаком после запятой) и случайный тип погоды (`["Ясно", "Облачно", "Дождь", "Снег"]`). В конце вычисли среднюю температуру за неделю.

**Задание 4. Эксперимент с монетой.** Подбрось монету 100 раз. Выведи, сколько раз выпали «Орёл» и «Решка» и их процент. Попробуй 1000 и 10000 бросков – приближаются ли проценты к 50%?

**Задание 5. Случайный пример.** Программа выбирает два случайных числа от 10 до 99 и случайную операцию (`+` или `-`) и спрашивает у ученика ответ. Сообщи, верен ли ответ.

## Продвинутый уровень

**Задание 6. «Камень, ножницы, бумага».** Игра с компьютером из 5 раундов. Игрок вводит `камень`, `ножницы` или `бумага`, компьютер выбирает случайно. Выведи победителя каждого раунда и итоговый счёт. Обработай неверный ввод (например, «камен»).

```text
Раунд 1. Твой выбор: камень
Компьютер: ножницы
Ты выиграл!
...
Итоговый счёт: Ты 3 – 1 Компьютер (1 ничья)
```

**Задание 7. Генератор паролей.** Пользователь вводит длину пароля (8–32). В пароле **обязательно** должны быть хотя бы одна строчная буква, одна заглавная буква, одна цифра и один специальный символ. Подсказка: сначала возьми по одному символу из каждой группы, остальное заполни из общего набора, а в конце перемешай с помощью `shuffle`.

**Задание 8. Викторина.** Словарь: `{вопрос: ответ}` (не менее 8 вопросов, например о Казахстане). Программа выбирает случайные 5 вопросов (без повторов), задаёт их ученику и подсчитывает баллы.

## ★ Олимпиадная / творческая задача

**Задание 9. Найти π с помощью случайности (Монте-Карло).** Брось N случайных точек в квадрат со стороной 1 (`x = random.random()`, `y = random.random()`). Если M – количество точек, попавших внутрь четверти круга (`x² + y² ≤ 1`), то π ≈ 4·M/N. Сравни результат с `math.pi` для N = 1000, 100000, 1000000.

**Задание 10. Turtle – случайное звёздное небо.** С помощью `turtle` нарисуй на чёрном фоне 50 «звёзд» (точек или маленьких звёздочек) в случайных местах, случайного размера и случайного цвета. Можно использовать метод `t.dot(size, color)`.

## Домашнее задание

1. «Волшебный шар предсказаний»: пользователь задаёт вопросы, пока не напишет «стоп», программа случайно даёт один из 10 ответов. На пустой вопрос пусть отвечает «Задай вопрос!».
2. Добавь в игру из задания 6 возможность сохранять рекорд (наибольшее число побед) в файл (способом из урока 60).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.**

```python
import random

d1 = random.randint(1, 6)
d2 = random.randint(1, 6)
print("Кубик 1:", d1)
print("Кубик 2:", d2)
print("Сумма:", d1 + d2)
if d1 == d2:
    print("Дубль!")
```

**Задание 2.**

```python
import random

students = ["Айгерим", "Нурлан", "Дана", "Ерасыл", "Мадина", "Арман", "Алия", "Санжар"]
print("Дежурный сегодня:", random.choice(students))
days = ["Понедельник", "Вторник", "Среда", "Четверг", "Пятница"]
duty = random.sample(students, 5)
for i in range(5):
    print(days[i], "–", duty[i])
```

**Задание 3.**

```python
import random

days = ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"]
kinds = ["Ясно", "Облачно", "Дождь", "Снег"]
temps = []
for day in days:
    t = round(random.uniform(-10, 10), 1)
    temps.append(t)
    print(f"{day}: {t:>5} °C, {random.choice(kinds)}")
print(f"Средняя: {sum(temps) / len(temps):.1f} °C")
```

**Задание 4.**

```python
import random

for n in [100, 1000, 10000]:
    heads = 0
    for _ in range(n):
        if random.randint(0, 1) == 0:
            heads += 1
    tails = n - heads
    print(f"N={n}: Орёл {heads} ({heads / n * 100:.1f}%), Решка {tails} ({tails / n * 100:.1f}%)")
```

**Задание 5.**

```python
import random

a = random.randint(10, 99)
b = random.randint(10, 99)
op = random.choice(["+", "-"])
correct = a + b if op == "+" else a - b
try:
    answer = int(input(f"{a} {op} {b} = "))
    if answer == correct:
        print("Верно!")
    else:
        print("Неверно. Правильный ответ:", correct)
except ValueError:
    print("Ответ должен быть числом. Правильный ответ:", correct)
```

**Задание 6.**

```python
import random

options = ["камень", "ножницы", "бумага"]
beats = {"камень": "ножницы", "ножницы": "бумага", "бумага": "камень"}
player_score = 0
computer_score = 0
draws = 0
round_no = 1
while round_no <= 5:
    player = input(f"Раунд {round_no}. Твой выбор: ").strip().lower()
    if player not in options:
        print("Только: камень, ножницы или бумага!")
        continue
    computer = random.choice(options)
    print("Компьютер:", computer)
    if player == computer:
        print("Ничья!")
        draws += 1
    elif beats[player] == computer:
        print("Ты выиграл!")
        player_score += 1
    else:
        print("Компьютер выиграл!")
        computer_score += 1
    round_no += 1
print(f"Итоговый счёт: Ты {player_score} – {computer_score} Компьютер ({draws} ничья)")
```

**Задание 7.**

```python
import random

lower = "abcdefghijklmnopqrstuvwxyz"
upper = lower.upper()
digits = "0123456789"
symbols = "!@#$%&*?"

while True:
    try:
        length = int(input("Длина (8-32): "))
        if 8 <= length <= 32:
            break
    except ValueError:
        pass
    print("Введи число от 8 до 32.")

chars = [random.choice(lower), random.choice(upper),
         random.choice(digits), random.choice(symbols)]
all_chars = lower + upper + digits + symbols
for _ in range(length - 4):
    chars.append(random.choice(all_chars))
random.shuffle(chars)
print("Пароль:", "".join(chars))
```

**Задание 8.**

```python
import random

quiz = {
    "Столица Казахстана?": "астана",
    "Самая высокая вершина – Хан-... ?": "тенгри",
    "В каком году Казахстан обрёл независимость?": "1991",
    "Самое большое озеро (целиком в Казахстане)?": "балхаш",
    "Знаменитый каток в Алматы?": "медеу",
    "Национальная валюта Казахстана?": "тенге",
    "Автор романа «Путь Абая»?": "мухтар ауэзов",
    "Байконур – это ...?": "космодром",
}
questions = random.sample(list(quiz.keys()), 5)
score = 0
for q in questions:
    answer = input(q + " ").strip().lower()
    if answer == quiz[q]:
        print("Верно!")
        score += 1
    else:
        print("Неверно. Ответ:", quiz[q])
print(f"Баллы: {score}/5")
```

**Задание 9.**

```python
import math
import random

for n in [1000, 100000, 1000000]:
    inside = 0
    for _ in range(n):
        x = random.random()
        y = random.random()
        if x * x + y * y <= 1:
            inside += 1
    estimate = 4 * inside / n
    print(f"N={n}: π ≈ {estimate:.5f}, погрешность = {abs(estimate - math.pi):.5f}")
```

Примечание: при N = 1 000 000 цикл работает несколько секунд – это нормально.

**Задание 10.**

```python
import random
import turtle

screen = turtle.Screen()
screen.bgcolor("black")
t = turtle.Turtle()
t.hideturtle()
t.speed(0)
t.penup()
colors = ["white", "yellow", "lightblue", "pink", "gold"]
for _ in range(50):
    t.goto(random.randint(-300, 300), random.randint(-250, 250))
    t.dot(random.randint(2, 8), random.choice(colors))
turtle.done()
```

</details>
