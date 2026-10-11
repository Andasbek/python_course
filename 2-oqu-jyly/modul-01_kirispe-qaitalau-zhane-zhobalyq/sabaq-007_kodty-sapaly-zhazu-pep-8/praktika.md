# Сабақ 7. Практикалық тапсырмалар: PEP 8, атаулар, түсініктеме, docstring

## Базалық деңгей

### 1-тапсырма. «Стиль детективі»
Төмендегі кодта PEP 8 бойынша кемінде **10** бұзушылық бар. Әрқайсысын жол нөмірімен дәптерге жаз (не дұрыс емес, қалай болуы керек), кейін кодты түзет.

```python
import random,math
def AreaCircle( R ):
  pi=3.14159
  return pi*R**2
def get(x,y = 2) :
    return x+y
MaxValue=100
l=[1,2,3]
if len(l)==0 : print("бос")
print(AreaCircle(2),get(3))
```

### 2-тапсырма. Атауларды дұрыс бер
Кестені толтыр: сол жақтағы сипаттамаға PEP 8 бойынша атау ойлап тап.

| Сипаттама | Түрі | Сенің атауың |
|---|---|---|
| Оқушының туған жылы | айнымалы | |
| Сыныптағы ең көп оқушы саны (өзгермейді) | тұрақты | |
| Тізімнен ең жоғары бағаны табу | функция | |
| Ойыншының тірі екенін білдіретін белгі | логикалық айнымалы | |
| Банк шоты | класс | |
| Бағалармен жұмыс істейтін файл | модуль | |
| Барлық қалалардың атаулары | тізім | |

### 3-тапсырма. Docstring жаз
Төмендегі функцияларға docstring қос (біреуіне — бір жолдық, екіншісіне — Args/Returns бөлімдері бар көп жолдық). Кейін `help()` арқылы тексер.

```python
def bmi(weight, height):
    return round(weight / height ** 2, 1)


def ticket_price(age, is_student=False):
    if age < 7:
        return 0
    price = 250
    if is_student:
        price = price // 2
    return price
```

```text
>>> help(bmi)
bmi(weight, height)
    Дене салмағының индексін (ДСИ) қайтарады.
```

### 4-тапсырма. Түсініктемелерді сұрыпта
Төмендегі түсініктемелердің қайсысы пайдалы, қайсысы пайдасыз? Пайдасыздарын алып тастап, қажет болса айнымалы атауын жақсарт.

```python
x = 0  # x айнымалысы 0-ге тең
speed = 5  # пиксель/кадр; 60 FPS-те ойыншы экранды ~3 секундта кесіп өтеді
for i in range(10):  # 10 рет цикл
    x += speed  # x-ке speed қосамыз
# Деңгей ауысқанда жылдамдық 20%-ға артады, бірақ 12-ден аспауы керек
speed = min(speed * 1.2, 12)
```

## Кеңейтілген деңгей

### 5-тапсырма. Толық рефакторинг
Төмендегі «Дүкен» бағдарламасын рефакторинг жаса:
- PEP 8 бойынша пішімде;
- мағыналы атаулар бер;
- кемінде 3 функцияға бөл, әрқайсысына docstring жаз;
- тұрақтыларды бөліп шығар;
- `main()` және `if __name__ == "__main__":` қос.

Бағдарламаның **әрекеті өзгермеуі керек** (бірдей енгізуге бірдей шығыс).

```python
d={"нан":180,"сүт":520,"жұмыртқа":75}
s=0
while True:
 a=input("Тауар (бос — бітіру): ")
 if a=="":break
 if a not in d:print("жоқ");continue
 b=int(input("саны: "))
 s=s+d[a]*b
if s>5000:s=s*0.95
print("Барлығы:",round(s,2),"₸")
```

### 6-тапсырма. Ruff (немесе flake8) пайдалану
1. Виртуалды ортада `pip install ruff` орнат.
2. 1-тапсырманың **бастапқы** кодын `bad_style.py` файлына сақтап, `ruff check bad_style.py` орында. Қандай қате кодтары шықты (`E…`, `F…`)?
3. `ruff format bad_style.py` орындап, нәтижені қара. Форматтаушы қандай бұзушылықтарды **түзете алмады** (мысалы, атаулар)? Неге?
4. VS Code-та «Format On Save» баптауын қос.

### 7-тапсырма. Модуль құжаттамасы
3-сабақтағы `grades_utils.py` (немесе өз модулің) файлына модуль docstring-ін, әр функцияға docstring және тип аннотацияларын қос. Басқа файлда `import grades_utils` → `help(grades_utils)` орындап, модульдің толық «анықтамалығы» шыққанын тексер. Өзгерістерді GitHub-қа push жаса: `git commit -m "Docstring және тип аннотациялары қосылды"`.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Өз «мини-линтерің»
`mini_lint.py` бағдарламасын жаз: ол берілген `.py` файлын жолма-жол оқып, мына бұзушылықтарды табады:
- жол 79 таңбадан ұзын;
- шегініс 4-ке еселі емес;
- жол соңында артық бос орын бар;
- Tab таңбасы бар;
- `def` атауы `snake_case` емес (бас әріп бар);
- функцияның келесі жолында docstring жоқ.

```text
bad_style.py:2: функция атауы snake_case емес: AreaCircle
bad_style.py:3: шегініс 4-ке еселі емес (2)
bad_style.py:3: docstring жоқ: AreaCircle
...
Барлығы: 7 ескерту
```

### 9-тапсырма. «Docstring генераторы»
`inspect` модулін қолданып, модульдегі барлық функциялардың аттарын, параметрлерін және docstring-терін Markdown кестесі түрінде шығаратын бағдарлама жаз (жобаның құжаттамасын автоматты жасау). Docstring-і жоқ функцияларды «⚠ құжатталмаған» деп белгіле.

## Үй тапсырмасы

1. Портфолиодағы калькулятор жобаңды PEP 8 бойынша толық рефакторинг жасап, docstring-тер қосып, GitHub-қа push жаса.
2. «Менің код жазу ережелерім» — 7 ереже (өз сөзіңмен, мысалдармен) — README-нің соңына немесе бөлек `STYLE.md` файлына жаз.
3. Келесі сабақ — диагностикалық жұмыс: 1-жыл тақырыптарын және осы модульдің құралдарын (venv, Git, PEP 8) қайтала. Жыл бойы жасағың келетін жоба идеясын ойлан.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма** — бұзушылықтар: (1) бір жолда екі импорт; (2) `math` қолданылмайды; (3) функциялар алдында 2 бос жол жоқ; (4) `AreaCircle` — PascalCase, функцияға `snake_case` керек; (5) жақша ішіндегі артық бос орындар `( R )`; (6) параметр атауы `R` бас әріппен; (7) шегініс 2 бос орын; (8) `pi=3.14159` — `=` айналасында бос орын жоқ, сондай-ақ `math.pi` қолданған дұрыс; (9) `y = 2` әдепкі мәнде бос орын болмауы керек; (10) `:` алдында бос орын `) :`; (11) `get` — мағынасыз атау; (12) `MaxValue` — тұрақты `MAX_VALUE` болуы керек; (13) `l` — шатастыратын атау; (14) `if ... : print(...)` бір жолда; (15) `len(l)==0` орнына `not numbers`; (16) үтірден кейін бос орын жоқ; (17) docstring жоқ.
```python
import math

MAX_VALUE = 100


def circle_area(radius):
    """Шеңбердің ауданын қайтарады."""
    return math.pi * radius ** 2


def add_numbers(x, y=2):
    """Екі санның қосындысын қайтарады."""
    return x + y


numbers = [1, 2, 3]
if not numbers:
    print("бос")
print(circle_area(2), add_numbers(3))
```

**2-тапсырма (үлгі)**: `birth_year`, `MAX_CLASS_SIZE`, `find_max_grade()`, `is_alive`, `BankAccount`, `grades_utils.py`, `city_names`.

**3-тапсырма**
```python
def bmi(weight, height):
    """Дене салмағының индексін (ДСИ) қайтарады."""
    return round(weight / height ** 2, 1)


def ticket_price(age, is_student=False):
    """Автобус билетінің бағасын есептейді.

    Args:
        age: жолаушының жасы.
        is_student: оқушы немесе студент болса True (50% жеңілдік).

    Returns:
        Билет бағасы теңгемен (7 жасқа дейін — тегін).
    """
    if age < 7:
        return 0
    price = 250
    if is_student:
        price = price // 2
    return price


help(bmi)
help(ticket_price)
```

**4-тапсырма** — пайдалы: `speed` жолындағы түсініктеме (бірлік пен мағына) және деңгей ауысуы туралы түсініктеме (себебі мен шегі). Қалғандары кодты қайталайды.
```python
position_x = 0
speed = 5  # пиксель/кадр; 60 FPS-те ойыншы экранды ~3 секундта кесіп өтеді
for _ in range(10):
    position_x += speed
# Деңгей ауысқанда жылдамдық 20%-ға артады, бірақ 12-ден аспауы керек
speed = min(speed * 1.2, 12)
```

**5-тапсырма**
```python
"""«Дүкен» бағдарламасы: себеттің сомасын есептейді."""

PRICES = {"нан": 180, "сүт": 520, "жұмыртқа": 75}
DISCOUNT_THRESHOLD = 5000
DISCOUNT_RATE = 0.05


def read_cart(prices):
    """Пайдаланушыдан тауарларды оқып, себеттің жалпы сомасын қайтарады."""
    total = 0
    while True:
        product = input("Тауар (бос — бітіру): ")
        if product == "":
            break
        if product not in prices:
            print("жоқ")
            continue
        count = int(input("саны: "))
        total += prices[product] * count
    return total


def apply_discount(total):
    """Сома шектен асса, жеңілдік қолданады."""
    if total > DISCOUNT_THRESHOLD:
        return total * (1 - DISCOUNT_RATE)
    return total


def main():
    total = apply_discount(read_cart(PRICES))
    print("Барлығы:", round(total, 2), "₸")


if __name__ == "__main__":
    main()
```

**6-тапсырма** — `ruff check` әдепкі ережелермен `E401` (бір жолда бірнеше импорт), `F401` (қолданылмаған импорт), `E701` (бір жолда бірнеше оператор), `E741` (`l` атауы) сияқты кодтарды көрсетеді; `ruff format` бос орындар мен шегіністерді түзетеді, бірақ атауларды өзгертпейді — атаудың мағынасын тек адам біледі, оны өзгерту бағдарламаның басқа бөліктерін бұзуы мүмкін.

**8-тапсырма**
```python
import re
import sys

MAX_LINE = 79
SNAKE_CASE = re.compile(r"^[a-z_][a-z0-9_]*$")


def check_file(path):
    """Файлды тексеріп, ескертулер тізімін қайтарады."""
    warnings = []
    with open(path, encoding="utf-8") as file:
        lines = file.read().splitlines()

    for number, line in enumerate(lines, start=1):
        if len(line) > MAX_LINE:
            warnings.append((number, f"жол тым ұзын ({len(line)} таңба)"))
        if "\t" in line:
            warnings.append((number, "Tab таңбасы бар"))
        if line != line.rstrip():
            warnings.append((number, "жол соңында артық бос орын"))
        indent = len(line) - len(line.lstrip(" "))
        if line.strip() and indent % 4 != 0:
            warnings.append((number, f"шегініс 4-ке еселі емес ({indent})"))

        match = re.match(r"\s*def\s+(\w+)", line)
        if match:
            name = match.group(1)
            if not SNAKE_CASE.match(name):
                warnings.append((number, f"функция атауы snake_case емес: {name}"))
            next_line = lines[number].strip() if number < len(lines) else ""
            if not next_line.startswith(('"""', "'''")):
                warnings.append((number, f"docstring жоқ: {name}"))
    return warnings


def main():
    path = sys.argv[1] if len(sys.argv) > 1 else input("Файл: ")
    warnings = check_file(path)
    for number, text in warnings:
        print(f"{path}:{number}: {text}")
    print("Барлығы:", len(warnings), "ескерту")


if __name__ == "__main__":
    main()
```

**9-тапсырма**
```python
import inspect

import grades_utils


def document_module(module):
    """Модуль функцияларының кестесін Markdown түрінде қайтарады."""
    rows = ["| Функция | Параметрлері | Сипаттамасы |", "|---|---|---|"]
    for name, func in inspect.getmembers(module, inspect.isfunction):
        signature = inspect.signature(func)
        doc = inspect.getdoc(func)
        summary = doc.splitlines()[0] if doc else "⚠ құжатталмаған"
        rows.append(f"| `{name}` | `{signature}` | {summary} |")
    return "\n".join(rows)


print(document_module(grades_utils))
```

</details>
