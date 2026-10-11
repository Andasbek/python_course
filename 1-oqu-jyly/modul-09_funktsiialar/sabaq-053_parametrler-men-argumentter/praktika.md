# Сабақ 53. Практикалық тапсырмалар: параметрлер мен аргументтер

## Базалық деңгей

### 1-тапсырма. Жеке сәлемдесу
`greet(name)` функциясын жазыңыз. Бағдарлама сыныптастарыңыздың тізімі бойынша цикл жүргізіп, әрқайсысына функцияны шақырсын.

```text
Сәлем, Айгерім! Python үйірмесіне қош келдің!
Сәлем, Бекзат! Python үйірмесіне қош келдің!
```

### 2-тапсырма. Тіктөртбұрыш
`draw_rectangle(width, height, symbol)` функциясы символдардан тіктөртбұрыш салсын.

Мысал: `draw_rectangle(6, 3, "#")`
```text
######
######
######
```

### 3-тапсырма. Көбейту кестесі
`multiplication_table(n, limit=10)` функциясы `n` санының 1-ден `limit`-ке дейінгі көбейту кестесін шығарсын. Функцияны `multiplication_table(7)` және `multiplication_table(3, 5)` деп шақырыңыз.

### 4-тапсырма. Валюта айырбастау
`convert(tenge, rate=480, currency="USD")` функциясы теңгені валютаға айырбастап, 2 ондық белгімен шығарсын.

Мысал:
```text
convert(10000)                     → 10000 тг = 20.83 USD
convert(10000, 520, "EUR")         → 10000 тг = 19.23 EUR
convert(10000, currency="RUB", rate=5.2) → 10000 тг = 1923.08 RUB
```
(Бағамдар шартты, мысал үшін алынған.)

### 5-тапсырма. Бағаны бағалау
`grade_comment(name, score)` функциясы ұпайға қарай пікір шығарсын: 90+ «Тамаша», 70–89 «Жақсы», 50–69 «Орташа», 50-ден төмен «Көбірек дайындал».

## Кеңейтілген деңгей

### 6-тапсырма. Символдық үшбұрыш
`triangle(height, symbol="*", align="left")` функциясы: `align="left"` болса тік бұрышты үшбұрыш, `align="center"` болса тең бүйірлі «шырша» салсын.
```text
triangle(3)              triangle(3, "^", "center")
*                          ^
**                        ^^^
***                      ^^^^^
```

### 7-тапсырма. Тізім статистикасы
`print_stats(numbers, title="Статистика")` функциясы тізімнің ең үлкен, ең кіші, қосынды, орташа мәндерін тақырыппен шығарсын. Бос тізім берілсе — «Тізім бос» деп шығарсын.

### 8-тапсырма. Бонус
`add_bonus(scores, bonus=5, limit=100)` функциясы сөздіктегі әр оқушының ұпайына бонус қоссын, бірақ ұпай `limit`-тен аспасын. Функция сөздікті өзгертеді — шақырғаннан кейін сөздікті функциядан тыс шығарып, өзгергенін тексеріңіз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Шахмат тақтасы
`chessboard(size=8, white="□", black="■")` функциясы шахмат тақтасын салсын (сол жақ жоғарғы ұяшық ақ).

### 10-тапсырма. Мәтінді рамкаға алу
`frame(lines, symbol="*", padding=1)` функциясы жолдар тізімін рамкаға алып шығарсын. Рамка ені ең ұзын жолға қарай есептелсін, `padding` — мәтін мен рамка арасындағы бос орындар саны.
```text
frame(["Python", "үйірмесі", "2027"], "#", 2)
##############
#  Python    #
#  үйірмесі  #
#  2027      #
##############
```

## Үй тапсырмасы

1. `birthday_card(name, age, wish="бақытты бол")` функциясымен туған күн құттықтауын жасаңыз. Отбасы мүшелерінің әрқайсысына шақырыңыз.
2. Өткен сабақтағы «үй суреті» бағдарламасын `draw_house(width=6, roof_symbol="/")` түрінде параметрлі етіп қайта жазуға тырысыңыз.
3. Кестені толтырыңыз: кірістірілген функциялардың қайсысы әдепкі параметрлерді қолданады? (`print` — `sep`, `end`; `round` — `ndigits`; `sorted` — `reverse` ...)

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
def greet(name):
    print(f"Сәлем, {name}! Python үйірмесіне қош келдің!")


classmates = ["Айгерім", "Бекзат", "Дана"]
for person in classmates:
    greet(person)
```

**2-тапсырма**
```python
def draw_rectangle(width, height, symbol):
    for i in range(height):
        print(symbol * width)


draw_rectangle(6, 3, "#")
```

**3-тапсырма**
```python
def multiplication_table(n, limit=10):
    for i in range(1, limit + 1):
        print(f"{n} × {i} = {n * i}")


multiplication_table(7)
multiplication_table(3, 5)
```

**4-тапсырма**
```python
def convert(tenge, rate=480, currency="USD"):
    print(f"{tenge} тг = {tenge / rate:.2f} {currency}")


convert(10000)
convert(10000, 520, "EUR")
convert(10000, currency="RUB", rate=5.2)
```
Нәтижесі:
```text
10000 тг = 20.83 USD
10000 тг = 19.23 EUR
10000 тг = 1923.08 RUB
```

**5-тапсырма**
```python
def grade_comment(name, score):
    if score >= 90:
        comment = "Тамаша"
    elif score >= 70:
        comment = "Жақсы"
    elif score >= 50:
        comment = "Орташа"
    else:
        comment = "Көбірек дайындал"
    print(f"{name}: {score} — {comment}")


grade_comment("Нұрай", 95)
grade_comment("Тимур", 48)
```

**6-тапсырма**
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

**7-тапсырма**
```python
def print_stats(numbers, title="Статистика"):
    print(f"=== {title} ===")
    if len(numbers) == 0:
        print("Тізім бос")
        return
    print("Ең үлкен:", max(numbers))
    print("Ең кіші:", min(numbers))
    print("Қосынды:", sum(numbers))
    print("Орташа:", round(sum(numbers) / len(numbers), 2))


print_stats([5, 3, 8, 1])
print_stats([], "Бос тізім")
```
Ескерту: `return` келесі сабақта толық өтіледі; мұнда ол тек функциядан ерте шығу үшін қолданылған. Оның орнына `else` блогын қолдануға болады.

**8-тапсырма**
```python
def add_bonus(scores, bonus=5, limit=100):
    for name in scores:
        scores[name] = min(scores[name] + bonus, limit)


results = {"Әлихан": 80, "Нұрай": 98, "Тимур": 60}
add_bonus(results)
print(results)
add_bonus(results, bonus=10)
print(results)
```
Нәтижесі:
```text
{'Әлихан': 85, 'Нұрай': 100, 'Тимур': 65}
{'Әлихан': 95, 'Нұрай': 100, 'Тимур': 75}
```

**9-тапсырма**
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

**10-тапсырма**
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


frame(["Python", "үйірмесі", "2027"], "#", 2)
```
Нәтижесі:
```text
##############
#  Python    #
#  үйірмесі  #
#  2027      #
##############
```
Рамка ені: ең ұзын жол + 2·padding + 2. `ljust(width)` — жолды оң жағынан бос орындармен толықтырады (оның орнына `line + " " * (width - len(line))` жазуға болады).
</details>
