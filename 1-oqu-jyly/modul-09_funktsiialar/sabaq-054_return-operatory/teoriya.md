# Сабақ 54. return операторы. Функциядан мән қайтару.

## Тақырыптар

1. return операторы
2. print және return — басты айырмашылық
3. Қайтарылған мәнді қолдану тәсілдері
4. return функцияны аяқтайды
5. True / False қайтаратын функциялар
6. Бірнеше мән қайтару
7. None — «ештеңе»
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. return операторы

```
def функция(параметрлер):
    ...
    return мән
```

`return`:
1. мәнді функция **шақырылған жерге қайтарады**;
2. функцияның жұмысын **бірден аяқтайды**.

```python
def area(a, b):
    return a * b


room1 = area(3, 4)
room2 = area(5, 2)
print("Бірінші бөлме:", room1)
print("Жалпы аудан:", room1 + room2)
print("Ас үй үлкен бе?", area(4, 4) > 15)
```

Нәтижесі:
```text
Бірінші бөлме: 12
Жалпы аудан: 22
Ас үй үлкен бе? True
```

Аналогия — **банкомат**: картаны салып, соманы енгізесіз (аргументтер), банкомат іште бірдеңе есептейді де, ақшаны **қолыңызға береді** (return). Сол ақшамен не істейтініңізді өзіңіз шешесіз. Ал `print` — банкомат экранда «20 000 теңге» деп **көрсетіп** қана қойғанмен бірдей: ақша қолыңызда жоқ!

## 2. print және return — басты айырмашылық

| | `print()` | `return` |
|---|---|---|
| Не істейді | мәнді **экранға** шығарады | мәнді **бағдарламаға** қайтарады |
| Нәтижені қолдануға бола ма | жоқ | иә: айнымалыға, өрнекке, шартқа |
| Функцияны аяқтай ма | жоқ | иә |
| Кім үшін | адам үшін | бағдарлама үшін |

```python
def square_print(n):
    print(n * n)


def square_return(n):
    return n * n


a = square_print(5)      # экранға 25 шығады, a = None
b = square_return(5)     # экранға ештеңе шықпайды, b = 25
print(a, b)
print(square_return(3) + square_return(4))
```

Нәтижесі:
```text
25
None 25
25
```

**Ереже:** функция бірдеңені **есептесе**, нәтижені `return` арқылы қайтарған дұрыс. Ал оны экранға шығару — шақырушы бағдарламаның шешімі. Мұндай функцияны кез келген жерде қайта қолдануға болады.

## 3. Қайтарылған мәнді қолдану тәсілдері

```python
def celsius_to_fahrenheit(c):
    return c * 9 / 5 + 32


# 1) айнымалыға сақтау
t = celsius_to_fahrenheit(25)
print(t)

# 2) өрнекте
print(celsius_to_fahrenheit(0) - 32)

# 3) шартта
if celsius_to_fahrenheit(-40) == -40:
    print("-40 градуста шкалалар тең!")

# 4) f-жолда
print(f"Алматыда +30°C = {celsius_to_fahrenheit(30)}°F")

# 5) басқа функцияның аргументі ретінде
print(round(celsius_to_fahrenheit(36.6), 1))
```

Нәтижесі:
```text
77.0
0.0
-40 градуста шкалалар тең!
Алматыда +30°C = 86.0°F
97.9
```

### Функциялар тізбегі

Бір функцияның нәтижесі екіншісіне беріледі:

```python
def price_with_discount(price, percent):
    return price - price * percent / 100


def price_with_tax(price, tax=12):
    return price + price * tax / 100


final = price_with_tax(price_with_discount(10000, 20))
print(final)
```

Нәтижесі:
```text
8960.0
```

## 4. return функцияны аяқтайды

`return`-нен кейінгі жолдар орындалмайды:

```python
def test():
    print("return-ге дейін")
    return 1
    print("Бұл жол ешқашан орындалмайды")


print(test())
```

Нәтижесі:
```text
return-ге дейін
1
```

### Бірнеше return — тармақталумен

```python
def sign(n):
    if n > 0:
        return "оң"
    elif n < 0:
        return "теріс"
    return "нөл"


print(sign(5), sign(-3), sign(0))
```

Нәтижесі:
```text
оң теріс нөл
```

### Циклден ерте қайтару

```python
def find_index(items, target):
    for i in range(len(items)):
        if items[i] == target:
            return i          # табылды — бірден шығамыз
    return -1                 # цикл аяқталды, табылмады


cities = ["Алматы", "Астана", "Шымкент"]
print(find_index(cities, "Астана"))
print(find_index(cities, "Тараз"))
```

Нәтижесі:
```text
1
-1
```

Бұл `str.find()` әдісі сияқты жұмыс істейді: табылмаса -1.

## 5. True / False қайтаратын функциялар

Атауы көбіне `is_`, `has_`, `can_` деп басталады. Оларды тікелей `if`-те қолданамыз:

```python
def is_even(n):
    return n % 2 == 0


def is_palindrome(text):
    clean = text.lower().replace(" ", "")
    return clean == clean[::-1]


def is_leap_year(year):
    return (year % 4 == 0 and year % 100 != 0) or year % 400 == 0


print(is_even(10), is_even(7))
if is_palindrome("Қазақ"):
    print("Палиндром!")
for y in [2024, 2027, 2000, 1900]:
    print(y, is_leap_year(y))
```

Нәтижесі:
```text
True False
Палиндром!
2024 True
2027 False
2000 True
1900 False
```

`return n % 2 == 0` — салыстырудың нәтижесі өзі `True` немесе `False`, сондықтан `if ... : return True else: return False` деп ұзақ жазудың қажеті жоқ.

### Жай санды тексеру

```python
def is_prime(n):
    if n < 2:
        return False
    for d in range(2, int(n ** 0.5) + 1):
        if n % d == 0:
            return False
    return True


primes = []
for x in range(1, 30):
    if is_prime(x):
        primes.append(x)
print(primes)
```

Нәтижесі:
```text
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
```

## 6. Бірнеше мән қайтару

Python-да үтір арқылы бірнеше мән қайтаруға болады — олар **кортеж** түрінде беріледі және бірден бірнеше айнымалыға «ашылады»:

```python
def min_max_avg(numbers):
    return min(numbers), max(numbers), sum(numbers) / len(numbers)


grades = [5, 3, 4, 5, 4]
low, high, avg = min_max_avg(grades)
print(f"Ең төмен: {low}, ең жоғары: {high}, орташа: {avg}")
print(min_max_avg([1, 2, 3]))
```

Нәтижесі:
```text
Ең төмен: 3, ең жоғары: 5, орташа: 4.2
(1, 3, 2.0)
```

```python
def split_time(total_minutes):
    return total_minutes // 60, total_minutes % 60


h, m = split_time(135)
print(f"{h} сағ {m} мин")
```

Нәтижесі:
```text
2 сағ 15 мин
```

## 7. None — «ештеңе»

`return` жоқ (немесе бос `return`) функция `None` қайтарады. `None` — «мән жоқ» деген арнайы мән, түрі `NoneType`.

```python
def nothing():
    pass


result = nothing()
print(result, type(result))
```

Нәтижесі:
```text
None <class 'NoneType'>
```

Мәнді қайтармайтын функциялар да қажет — мысалы, мәзірді шығару, сурет салу. Бірақ **есептейтін** функциялар әрқашан `return` қолдануы керек.

## Жиі кездесетін қателер

**1. return орнына print**

```python
def add(a, b):
    print(a + b)          # қате ой: нәтиже қайтарылмайды

def add_ok(a, b):
    return a + b

total = add_ok(2, 3) * 10
print(total)              # 50
```

**2. Қайтарылған мәнді ешқайда сақтамау**

```python
def double(x):
    return x * 2

double(5)                 # нәтиже жоғалды
y = double(5)
print(y)                  # 10
```

**3. return-ді циклдің ішінде ерте жазу**

```python
def total_sum(numbers):
    s = 0
    for n in numbers:
        s += n
        # return s        # қате: бірінші элементтен кейін-ақ шығып кетеді
    return s              # дұрыс: циклден кейін

print(total_sum([1, 2, 3]))   # 6
```

**4. Барлық тармақта return жазбау**

```python
def grade(score):
    if score >= 50:
        return "өтті"
    # score < 50 болса — None қайтарады!
    return "өтпеді"

print(grade(30))
```

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `return` | функциядан мән қайтаратын және оны аяқтайтын оператор |
| Қайтарылатын мән | функция шақырылған жерге берілетін нәтиже |
| `None` | «мән жоқ»; return-сіз функцияның нәтижесі |
| Предикат | `True`/`False` қайтаратын функция (`is_even`) |
| Ерте қайтару | шарт орындалғанда функциядан бірден шығу |
| Бірнеше мән қайтару | `return a, b` — кортеж қайтарады |

## Ескерту

- Интерактивті режимде (`>>>`) функцияның қайтарған мәні экранға автоматты түрде шығады, ал `.py` файлында — жоқ. Сондықтан файлда нәтижені көру үшін `print()` керек.
- PEP 8 функцияда `return` операторларын біркелкі жазуды ұсынады: бір тармақ мән қайтарса, қалғандары да мәнді (қажет болса `return None`) анық қайтаруы керек.
- `return a, b` кортеж қайтарады; оны ашқанда айнымалылар саны элементтер санына тең болмаса, `ValueError` шығады.
- Бөлшек сандарды салыстыруда `==` қолданбаған жөн (`0.1 + 0.2 == 0.3` → `False`); оның орнына `math.isclose()` бар.
- Функцияның қайтаратын мәнінің түрін аннотациямен көрсетуге болады: `def area(a: float, b: float) -> float:` — бұл тек кеңес, Python оны тексермейді.
- Құжаттама: https://docs.python.org/3/reference/simple_stmts.html#the-return-statement
