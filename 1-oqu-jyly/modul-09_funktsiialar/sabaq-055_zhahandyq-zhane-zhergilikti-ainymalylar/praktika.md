# Сабақ 55. Практикалық тапсырмалар: көріну аймағы

## Базалық деңгей

### 1-тапсырма. Не шығады? (1)
Бағдарламаны орындамай, нәтижесін дәптерге жазыңыз. Кейін Thonny-де тексеріңіз.
```python
a = 5


def f():
    a = 10
    print("f ішінде:", a)


f()
print("Сыртта:", a)
```

### 2-тапсырма. Не шығады? (2)
```python
x = 1


def change(x):
    x = x + 100
    print("Функцияда:", x)
    return x


y = change(x)
print(x, y)
```

### 3-тапсырма. Не шығады? (3)
```python
name = "Айгерім"


def hello():
    print("Сәлем,", name)


hello()
name = "Бекзат"
hello()
```

### 4-тапсырма. Қатені түзет
Бағдарлама ойын ұпайын арттыруы керек, бірақ қате береді. Екі тәсілмен түзетіңіз: а) `global` арқылы; б) параметр мен `return` арқылы. Қайсысы жақсырақ? Неге?
```python
score = 0


def add_points(points):
    score = score + points


add_points(10)
add_points(5)
print(score)
```

### 5-тапсырма. Тұрақтылар
Жаһандық тұрақтылар құрыңыз: `SCHOOL = "№120 мектеп"`, `CITY = "Алматы"`, `YEAR = 2027`. `make_certificate(name, place)` функциясы осы тұрақтыларды пайдаланып, олимпиада грамотасының мәтінін **қайтарсын** (жол ретінде). 3 оқушыға грамота шығарыңыз.

## Кеңейтілген деңгей

### 6-тапсырма. Банк шоты
Жаһандық айнымалысыз банк шотын модельдеңіз: `deposit(balance, amount)` және `withdraw(balance, amount)` функциялары жаңа балансты қайтарсын (ақша жетпесе — ескерту шығарып, ескі балансты қайтарсын). Негізгі бағдарламада мәзір арқылы операцияларды орындаңыз.

### 7-тапсырма. Шақырулар санауышы
`greet(name)` функциясы неше рет шақырылғанын санауы керек. Екі тәсілмен жасаңыз:
- а) жаһандық `calls` айнымалысы және `global`;
- б) жаһандық сөздік `stats = {"calls": 0}` (неге бұл жағдайда `global` қажет емес?).

### 8-тапсырма. Іздеу реті
Келесі кодта қай `value` қай жерде қолданылады? Әр `print` жанына «жергілікті», «жаһандық» немесе «қате» деп жазыңыз, кейін тексеріңіз.
```python
value = "G"


def outer_print():
    print(value)


def inner_change():
    value = "L"
    print(value)


def tricky():
    print(value)


outer_print()
inner_change()
tricky()
print(value)
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Ойын «Сандарды жина»
Ойыншы 5 раунд (кезең) ойнайды. Әр раундта бағдарлама 1–10 аралығындағы сан сұрайды; сан жұп болса +2 ұпай, тақ болса +1 ұпай, 7 болса — ұпай нөлге түседі. **Ешбір `global` қолданбай**, ойын күйін (ұпай, раунд) параметрлер мен `return` арқылы беріп отырыңыз: `play_round(score, number)` → жаңа ұпай.

### 10-тапсырма. Зерттеу
`globals()` және `locals()` кірістірілген функцияларын функция ішінде және сыртында шақырып, не қайтаратынын зерттеңіз (тек `locals()`-ті функция ішінде басып шығарыңыз). Көргеніңізді 3–4 сөйлеммен сипаттаңыз.

## Үй тапсырмасы

1. «Мектеп пен сынып» аналогиясына ұқсас өз аналогияңызды ойлап тауып, көріну аймағын түсіндіретін шағын мәтін жазыңыз.
2. Өткен сабақтардағы өз бағдарламаларыңыздың бірінен `global` қолданылған (немесе жаһандық айнымалыға тәуелді) функцияны тауып, оны параметр + `return` түріне келтіріңіз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```text
f ішінде: 10
Сыртта: 5
```

**2-тапсырма**
```text
Функцияда: 101
1 101
```

**3-тапсырма** — функция жаһандық айнымалының **шақыру кезіндегі** мәнін оқиды:
```text
Сәлем, Айгерім
Сәлем, Бекзат
```

**4-тапсырма**
```python
# а) global арқылы
score = 0


def add_points(points):
    global score
    score = score + points


add_points(10)
add_points(5)
print(score)
```

```python
# б) параметр + return (жақсырақ: функция тәуелсіз, тестілеу оңай)
def add_points(score, points):
    return score + points


score = 0
score = add_points(score, 10)
score = add_points(score, 5)
print(score)
```
Нәтижесі:
```text
15
```

**5-тапсырма**
```python
SCHOOL = "№120 мектеп"
CITY = "Алматы"
YEAR = 2027


def make_certificate(name, place):
    return (f"ГРАМОТА\n{name} — {SCHOOL} оқушысы,\n"
            f"{CITY} қалалық Python олимпиадасында {place}-орын алды. {YEAR} ж.")


for student, place in [("Нұрай", 1), ("Әлихан", 2), ("Тимур", 3)]:
    print(make_certificate(student, place))
    print()
```

**6-тапсырма**
```python
def deposit(balance, amount):
    return balance + amount


def withdraw(balance, amount):
    if amount > balance:
        print("Қаражат жеткіліксіз!")
        return balance
    return balance - amount


balance = 0
while True:
    choice = input("1-салу, 2-алу, 3-баланс, 0-шығу: ")
    if choice == "1":
        balance = deposit(balance, int(input("Сома: ")))
    elif choice == "2":
        balance = withdraw(balance, int(input("Сома: ")))
    elif choice == "3":
        print("Баланс:", balance, "тг")
    elif choice == "0":
        break
```

**7-тапсырма**
```python
# а)
calls = 0


def greet(name):
    global calls
    calls += 1
    print("Сәлем,", name)


greet("Дана")
greet("Ерлан")
print("Шақырулар:", calls)
```

```python
# б) сөздіктің ішін өзгертеміз, stats айнымалысын қайта меншіктемейміз — global қажет емес
stats = {"calls": 0}


def greet(name):
    stats["calls"] += 1
    print("Сәлем,", name)


greet("Дана")
greet("Ерлан")
print("Шақырулар:", stats["calls"])
```

**8-тапсырма**
```text
G   ← outer_print: жаһандық
L   ← inner_change: жергілікті
G   ← tricky: жаһандық
G   ← сыртта: жаһандық (inner_change оны өзгерткен жоқ)
```

**9-тапсырма**
```python
def play_round(score, number):
    if number == 7:
        return 0
    if number % 2 == 0:
        return score + 2
    return score + 1


score = 0
for round_no in range(1, 6):
    n = int(input(f"{round_no}-раунд, сан (1-10): "))
    score = play_round(score, n)
    print("Ұпай:", score)
print("Қорытынды ұпай:", score)
```

**10-тапсырма**
```python
x = 5


def demo(a):
    b = a * 2
    print(locals())


demo(3)
print("x" in globals())
```
Нәтижесі:
```text
{'a': 3, 'b': 6}
True
```
`locals()` — жергілікті айнымалылардың сөздігі, `globals()` — жаһандық атаулардың сөздігі (оған функциялардың аттары да кіреді).
</details>
