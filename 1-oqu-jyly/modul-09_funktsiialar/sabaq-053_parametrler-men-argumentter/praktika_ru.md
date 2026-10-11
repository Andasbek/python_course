# Урок 53. Практические задания: параметры и аргументы

## Базовый уровень

### Задание 1. Личное приветствие
Напишите функцию `greet(name)`. Программа должна пройти циклом по списку ваших одноклассников и вызвать функцию для каждого.

```text
Привет, Айгерим! Добро пожаловать в кружок Python!
Привет, Бекзат! Добро пожаловать в кружок Python!
```

### Задание 2. Прямоугольник
Функция `draw_rectangle(width, height, symbol)` должна рисовать прямоугольник из символов.

Пример: `draw_rectangle(6, 3, "#")`
```text
######
######
######
```

### Задание 3. Таблица умножения
Функция `multiplication_table(n, limit=10)` должна выводить таблицу умножения числа `n` от 1 до `limit`. Вызовите функцию как `multiplication_table(7)` и `multiplication_table(3, 5)`.

### Задание 4. Обмен валюты
Функция `convert(tenge, rate=480, currency="USD")` должна переводить тенге в валюту и выводить результат с 2 знаками после запятой.

Пример:
```text
convert(10000)                     → 10000 тг = 20.83 USD
convert(10000, 520, "EUR")         → 10000 тг = 19.23 EUR
convert(10000, currency="RUB", rate=5.2) → 10000 тг = 1923.08 RUB
```
(Курсы условные, взяты для примера.)

### Задание 5. Отзыв об оценке
Функция `grade_comment(name, score)` должна выводить отзыв в зависимости от количества баллов: 90+ «Отлично», 70–89 «Хорошо», 50–69 «Средне», ниже 50 «Готовься больше».

## Продвинутый уровень

### Задание 6. Треугольник из символов
Функция `triangle(height, symbol="*", align="left")`: при `align="left"` рисует прямоугольный треугольник, при `align="center"` — равнобедренную «ёлочку».
```text
triangle(3)              triangle(3, "^", "center")
*                          ^
**                        ^^^
***                      ^^^^^
```

### Задание 7. Статистика списка
Функция `print_stats(numbers, title="Статистика")` должна выводить с заголовком наибольшее, наименьшее значения, сумму и среднее списка. Если передан пустой список — выводить «Список пуст».

### Задание 8. Бонус
Функция `add_bonus(scores, bonus=5, limit=100)` должна добавлять бонус к баллам каждого ученика в словаре, но так, чтобы баллы не превышали `limit`. Функция изменяет словарь — после вызова выведите словарь вне функции и проверьте, что он изменился.

## ★ Олимпиадная / творческая задача

### Задание 9. Шахматная доска
Функция `chessboard(size=8, white="□", black="■")` должна рисовать шахматную доску (левая верхняя клетка белая).

### Задание 10. Текст в рамке
Функция `frame(lines, symbol="*", padding=1)` должна выводить список строк в рамке. Ширина рамки вычисляется по самой длинной строке, `padding` — количество пробелов между текстом и рамкой.
```text
frame(["Кружок", "Python", "2027"], "#", 2)
############
#  Кружок  #
#  Python  #
#  2027    #
############
```

## Домашнее задание

1. С помощью функции `birthday_card(name, age, wish="будь счастлив")` создайте поздравление с днём рождения. Вызовите её для каждого члена семьи.
2. Попробуйте переписать программу «рисунок дома» с прошлого урока с параметрами: `draw_house(width=6, roof_symbol="/")`.
3. Заполните таблицу: какие встроенные функции используют параметры по умолчанию? (`print` — `sep`, `end`; `round` — `ndigits`; `sorted` — `reverse` ...)

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
def greet(name):
    print(f"Привет, {name}! Добро пожаловать в кружок Python!")


classmates = ["Айгерим", "Бекзат", "Дана"]
for person in classmates:
    greet(person)
```

**Задание 2**
```python
def draw_rectangle(width, height, symbol):
    for i in range(height):
        print(symbol * width)


draw_rectangle(6, 3, "#")
```

**Задание 3**
```python
def multiplication_table(n, limit=10):
    for i in range(1, limit + 1):
        print(f"{n} × {i} = {n * i}")


multiplication_table(7)
multiplication_table(3, 5)
```

**Задание 4**
```python
def convert(tenge, rate=480, currency="USD"):
    print(f"{tenge} тг = {tenge / rate:.2f} {currency}")


convert(10000)
convert(10000, 520, "EUR")
convert(10000, currency="RUB", rate=5.2)
```
Результат:
```text
10000 тг = 20.83 USD
10000 тг = 19.23 EUR
10000 тг = 1923.08 RUB
```

**Задание 5**
```python
def grade_comment(name, score):
    if score >= 90:
        comment = "Отлично"
    elif score >= 70:
        comment = "Хорошо"
    elif score >= 50:
        comment = "Средне"
    else:
        comment = "Готовься больше"
    print(f"{name}: {score} — {comment}")


grade_comment("Нурай", 95)
grade_comment("Тимур", 48)
```

**Задание 6**
```python
def triangle(height, symbol="*", align="left"):
    for i in range(1, height + 1):
        if align == "center":
            print(" " * (height - i) + symbol * (2 * i - 1))
        else:
            print(symbol * i)


triangle(3)
triangle(3, "^", "center")
```

**Задание 7**
```python
def print_stats(numbers, title="Статистика"):
    print(f"=== {title} ===")
    if len(numbers) == 0:
        print("Список пуст")
        return
    print("Наибольшее:", max(numbers))
    print("Наименьшее:", min(numbers))
    print("Сумма:", sum(numbers))
    print("Среднее:", round(sum(numbers) / len(numbers), 2))


print_stats([5, 3, 8, 1])
print_stats([], "Пустой список")
```
Примечание: `return` подробно изучается на следующем уроке; здесь он используется только для досрочного выхода из функции. Вместо него можно использовать блок `else`.

**Задание 8**
```python
def add_bonus(scores, bonus=5, limit=100):
    for name in scores:
        scores[name] = min(scores[name] + bonus, limit)


results = {"Алихан": 80, "Нурай": 98, "Тимур": 60}
add_bonus(results)
print(results)
add_bonus(results, bonus=10)
print(results)
```
Результат:
```text
{'Алихан': 85, 'Нурай': 100, 'Тимур': 65}
{'Алихан': 95, 'Нурай': 100, 'Тимур': 75}
```

**Задание 9**
```python
def chessboard(size=8, white="□", black="■"):
    for row in range(size):
        line = ""
        for col in range(size):
            if (row + col) % 2 == 0:
                line += white
            else:
                line += black
        print(line)


chessboard()
chessboard(4, ".", "#")
```

**Задание 10**
```python
def frame(lines, symbol="*", padding=1):
    width = 0
    for line in lines:
        if len(line) > width:
            width = len(line)
    inner = width + 2 * padding
    print(symbol * (inner + 2))
    for line in lines:
        print(symbol + " " * padding + line.ljust(width) + " " * padding + symbol)
    print(symbol * (inner + 2))


frame(["Кружок", "Python", "2027"], "#", 2)
```
Результат:
```text
############
#  Кружок  #
#  Python  #
#  2027    #
############
```
Ширина рамки: самая длинная строка + 2·padding + 2. `ljust(width)` дополняет строку пробелами справа (вместо этого можно написать `line + " " * (width - len(line))`).
</details>
