# Сабақ 31. Практикум: «Калькулятор» терезелік қосымшасын құру.

## Тақырыптар

1. Қосымшаны жоспарлаудан бастаймыз
2. Кодтың құрылымы: неге класс?
3. Батырмаларды цикл арқылы құру
4. Бір функция — көп батырма: lambda
5. Өрнекті есептеу: eval() және оның қауіптілігі
6. Қателерді өңдеу
7. Толық үлгі: «Калькулятор» қосымшасы
8. Тестілеу
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Қосымшаны жоспарлаудан бастаймыз

Кәсіби бағдарламашы бірден код жазбайды. Алдымен **талаптарды** (требования) анықтайды.

**«Калькулятор» қосымшасына қойылатын талаптар (техникалық тапсырма):**

| № | Талап | Міндетті ме? |
|---|---|---|
| 1 | Дисплейде енгізілген өрнек пен нәтиже көрсетіледі | Иә |
| 2 | 0–9 цифр батырмалары, нүкте `.` | Иә |
| 3 | Амалдар: `+`, `-`, `*`, `/` | Иә |
| 4 | `=` — нәтижені есептеу, `C` — тазалау, `⌫` — соңғы таңбаны өшіру | Иә |
| 5 | Нөлге бөлгенде бағдарлама құламай, «Қате» деп жазады | Иә |
| 6 | Пернетақтадан да енгізуге болады | Қосымша |
| 7 | `%`, `x²`, `√` батырмалары, есептеу тарихы | Қосымша |

**Интерфейс макеті:**

```text
+-------------------------------+
|                     125+37    |   <- дисплей (row=0, columnspan=4)
+-------+-------+-------+-------+
|   C   |   ⌫   |   %   |   /   |   row=1
+-------+-------+-------+-------+
|   7   |   8   |   9   |   *   |   row=2
+-------+-------+-------+-------+
|   4   |   5   |   6   |   -   |   row=3
+-------+-------+-------+-------+
|   1   |   2   |   3   |   +   |   row=4
+-------+-------+-------+-------+
|       0       |   .   |   =   |   row=5 («0» екі баған алады)
+---------------+-------+-------+
```

Макет бізге `grid` торының жолдары (`row`) мен бағандарын (`column`) бірден көрсетеді.

## 2. Кодтың құрылымы: неге класс?

Класс деректер мен әрекеттерді бір жерге біріктіреді. Калькуляторда:
- **деректер (атрибуттар):** `self.expression` — енгізіліп жатқан өрнек, `self.display_var` — дисплейдегі мәтін;
- **әрекеттер (әдістер):** `create_widgets()`, `press(symbol)`, `calculate()`, `clear()`, `backspace()`.

```text
Calculator
 ├── __init__(root)         терезені баптау
 ├── create_widgets()       дисплей мен батырмаларды құру
 ├── press(symbol)          таңбаны өрнекке қосу
 ├── calculate()            «=» басылғанда есептеу
 ├── clear() / backspace()  тазалау
 └── on_key(event)          пернетақта оқиғасы
```

## 3. Батырмаларды цикл арқылы құру

20 батырманы бір-бірлеп жазу — ұзақ әрі қате көп. Оның орнына батырмалардың **тізімін** жасап, циклмен құрамыз:

```python
import tkinter as tk

root = tk.Tk()
root.title("Батырмалар торы")

buttons = [
    "7", "8", "9", "/",
    "4", "5", "6", "*",
    "1", "2", "3", "-",
    "0", ".", "=", "+",
]

for index, text in enumerate(buttons):
    row = index // 4 + 1      # жол нөмірі
    col = index % 4           # баған нөмірі
    btn = tk.Button(root, text=text, width=5, height=2, font=("Arial", 14))
    btn.grid(row=row, column=col, padx=2, pady=2)

root.mainloop()
```

`index // 4` және `index % 4` — бүтін бөлу мен қалдық арқылы бір өлшемді тізімді 4 бағанды торға «қию».

## 4. Бір функция — көп батырма: lambda

Әр батырмаға жеке функция жазбау үшін бір ортақ `press(symbol)` функциясын жасаймыз. Бірақ `command` параметріне аргументі бар функцияны тікелей беруге болмайды, сондықтан `lambda` қолданамыз:

```python
import tkinter as tk

def press(symbol):
    print("Басылды:", symbol)

root = tk.Tk()
for i, text in enumerate(["1", "2", "3"]):
    tk.Button(root, text=text, width=5,
              command=lambda s=text: press(s)).grid(row=0, column=i)
root.mainloop()
```

Нәтижесі (батырмаларды кезекпен басқанда консольде):
```text
Басылды: 1
Басылды: 2
Басылды: 3
```

**Маңызды:** `lambda s=text: press(s)` жазбасындағы `s=text` — әдепкі мән. Ол әр итерациядағы `text`-ті «есте сақтап» қалады. Егер `lambda: press(text)` деп жазсақ, барлық батырма соңғы мәнді («3») басатын болады (төмендегі «Жиі кездесетін қателер» бөлімін қара).

## 5. Өрнекті есептеу: eval() және оның қауіптілігі

Python-да `eval("2+3*4")` жолдағы өрнекті есептеп, `14` қайтарады. Бұл калькуляторға өте ыңғайлы. Бірақ `eval` **кез келген** Python кодын орындайды, сондықтан пайдаланушы енгізген мәтінді тексерусіз беру қауіпті.

Қауіпсіздік үшін екі шара қолданамыз:
1. өрнекте тек рұқсат етілген таңбалар (`0123456789+-*/.()% `) бар екенін тексереміз;
2. `eval`-ға бос ортаны береміз: `eval(expr, {"__builtins__": {}}, {})`.

```python
ALLOWED = set("0123456789+-*/.() ")

def safe_eval(expr):
    """Тек арифметикалық өрнекті есептейді."""
    if not expr or not set(expr) <= ALLOWED:
        raise ValueError("Рұқсат етілмеген таңба")
    return eval(expr, {"__builtins__": {}}, {})

print(safe_eval("2+3*4"))
print(safe_eval("(10-4)/4"))
try:
    print(safe_eval("10/0"))
except ZeroDivisionError:
    print("Нөлге бөлуге болмайды!")
```

Нәтижесі:
```text
14
1.5
Нөлге бөлуге болмайды!
```

**Нәтижені әдемілеу.** `6/2` нәтижесі `3.0` болып шығады. Бүтін болса, `.0`-ді алып тастаймыз, ал ұзын бөлшекті дөңгелектейміз:

```python
def format_number(value):
    if isinstance(value, float) and value.is_integer():
        return str(int(value))
    if isinstance(value, float):
        return str(round(value, 10))
    return str(value)

print(format_number(3.0), format_number(0.1 + 0.2), format_number(7))
```

Нәтижесі:
```text
3 0.3 7
```

## 6. Қателерді өңдеу

| Жағдай | Мысал | Қандай қате | Не көрсетеміз |
|---|---|---|---|
| Нөлге бөлу | `5/0` | `ZeroDivisionError` | «Нөлге бөлуге болмайды» |
| Аяқталмаған өрнек | `5+` | `SyntaxError` | «Қате өрнек» |
| Бос өрнек | `` | біз өзіміз `ValueError` | ештеңе өзгермейді |
| Бөтен таңба | `abc` | біз өзіміз `ValueError` | «Қате өрнек» |

`except` блоктарын нақты қате түрлерімен жазамыз — бұл бағдарламаны түсінікті етеді.

## 7. Толық үлгі: «Калькулятор» қосымшасы

```python
import tkinter as tk

ALLOWED = set("0123456789+-*/.() ")


class Calculator:
    """Tkinter негізіндегі қарапайым калькулятор."""

    BUTTONS = [
        ("C", 1, 0, 1), ("⌫", 1, 1, 1), ("%", 1, 2, 1), ("/", 1, 3, 1),
        ("7", 2, 0, 1), ("8", 2, 1, 1), ("9", 2, 2, 1), ("*", 2, 3, 1),
        ("4", 3, 0, 1), ("5", 3, 1, 1), ("6", 3, 2, 1), ("-", 3, 3, 1),
        ("1", 4, 0, 1), ("2", 4, 1, 1), ("3", 4, 2, 1), ("+", 4, 3, 1),
        ("0", 5, 0, 2), (".", 5, 2, 1), ("=", 5, 3, 1),
    ]

    def __init__(self, root):
        self.root = root
        self.root.title("Калькулятор")
        self.root.resizable(False, False)
        self.expression = ""
        self.display_var = tk.StringVar(value="0")
        self.create_widgets()
        self.root.bind("<Key>", self.on_key)

    def create_widgets(self):
        display = tk.Entry(self.root, textvariable=self.display_var,
                           font=("Arial", 24), justify="right",
                           bd=6, relief="ridge", state="readonly")
        display.grid(row=0, column=0, columnspan=4, sticky="nsew",
                     padx=5, pady=5)
        for text, row, col, span in self.BUTTONS:
            color = "#f0f0f0"
            if text in "+-*/%":
                color = "#ffd27f"
            elif text == "=":
                color = "#7fd17f"
            elif text in ("C", "⌫"):
                color = "#f28b82"
            btn = tk.Button(self.root, text=text, font=("Arial", 16),
                            bg=color, width=4, height=2,
                            command=lambda t=text: self.press(t))
            btn.grid(row=row, column=col, columnspan=span,
                     sticky="nsew", padx=2, pady=2)

    def update_display(self):
        self.display_var.set(self.expression or "0")

    def press(self, symbol):
        if symbol == "C":
            self.clear()
        elif symbol == "⌫":
            self.backspace()
        elif symbol == "=":
            self.calculate()
        elif symbol == "%":
            self.percent()
        else:
            self.expression += symbol
            self.update_display()

    def clear(self):
        self.expression = ""
        self.update_display()

    def backspace(self):
        self.expression = self.expression[:-1]
        self.update_display()

    def percent(self):
        try:
            value = self.safe_eval(self.expression) / 100
            self.expression = self.format_number(value)
        except (ValueError, SyntaxError, ZeroDivisionError):
            self.expression = ""
        self.update_display()

    @staticmethod
    def safe_eval(expr):
        if not expr or not set(expr) <= ALLOWED:
            raise ValueError("Қате өрнек")
        return eval(expr, {"__builtins__": {}}, {})

    @staticmethod
    def format_number(value):
        if isinstance(value, float) and value.is_integer():
            return str(int(value))
        if isinstance(value, float):
            return str(round(value, 10))
        return str(value)

    def calculate(self):
        try:
            result = self.safe_eval(self.expression)
            self.expression = self.format_number(result)
            self.update_display()
        except ZeroDivisionError:
            self.expression = ""
            self.display_var.set("Нөлге бөлуге болмайды")
        except (SyntaxError, ValueError):
            self.expression = ""
            self.display_var.set("Қате өрнек")

    def on_key(self, event):
        if event.char in "0123456789+-*/.()":
            self.press(event.char)
        elif event.keysym in ("Return", "KP_Enter"):
            self.calculate()
        elif event.keysym == "BackSpace":
            self.backspace()
        elif event.keysym == "Escape":
            self.clear()


if __name__ == "__main__":
    root = tk.Tk()
    app = Calculator(root)
    root.mainloop()
```

**Кодты талдау:**
- `BUTTONS` — кортеждер тізімі: (мәтін, жол, баған, неше баған алады). Интерфейсті өзгерту үшін тек осы кестені түзету жеткілікті.
- `@staticmethod` — `self`-ті қажет етпейтін көмекші әдістер (ОБП модулінен).
- `state="readonly"` — пайдаланушы дисплейге тікелей жаза алмайды, тек батырма/перне арқылы.
- `on_key` — `event.char` басылған таңбаны, `event.keysym` перненің атауын береді.
- `if __name__ == "__main__":` — файл модуль ретінде импортталса, терезе өздігінен ашылмайды.

## 8. Тестілеу

Қосымшаны дайын деп айту үшін оны жүйелі түрде тексереміз:

| № | Әрекет | Нәтиже |
|---|---|---|
| 1 | `2 + 3 * 4 =` | `14` |
| 2 | `7 / 2 =` | `3.5` |
| 3 | `6 / 2 =` | `3` (емес `3.0`) |
| 4 | `5 / 0 =` | «Нөлге бөлуге болмайды» |
| 5 | `5 + =` | «Қате өрнек» |
| 6 | `0.1 + 0.2 =` | `0.3` |
| 7 | `1 2 3 ⌫` | `12` |
| 8 | `C` | `0` |
| 9 | Пернетақтадан `9*9` + Enter | `81` |

## Жиі кездесетін қателер

**1. lambda ішінде циклдің айнымалысы «жабылып» қалады**
```python
import tkinter as tk
root = tk.Tk()
for i, text in enumerate(["1", "2", "3"]):
    tk.Button(root, text=text, command=lambda: print(text)).grid(row=0, column=i)
```
Кез келген батырма `3` басады, себебі `lambda` `text`-тің мәнін басылған сәтте іздейді, ал ол кезде цикл аяқталған.
Дұрысы:
```python
import tkinter as tk
root = tk.Tk()
for i, text in enumerate(["1", "2", "3"]):
    tk.Button(root, text=text, command=lambda t=text: print(t)).grid(row=0, column=i)
```

**2. Функцияны `command`-қа жақшамен беру**
```python
import tkinter as tk
def calculate():
    print("Есептеу")
root = tk.Tk()
tk.Button(root, text="=", command=calculate())   # қате: функция бірден орындалады
```
Дұрысы: `command=calculate` (жақшасыз).

**3. Қатені өңдемеу** — `eval("5/0")` терезені «қатырмайды», бірақ консольде `Traceback` шығып, нәтиже көрсетілмейді. Әрқашан `try...except` қолданыңыз.

**4. `grid` пен `pack`-ты бір контейнерде араластыру** — Tkinter қате береді немесе қатып қалады. Бір терезеде (немесе бір `Frame` ішінде) бір ғана менеджер қолданыңыз.

**5. Нәтижені санға айналдыруды ұмыту** — `self.expression = result` (сан) деп жазсаңыз, келесі `+=` амалы `TypeError` береді. Нәтижені әрқашан `str()` арқылы жолға айналдырыңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Техникалық тапсырма (ТТ) | Бағдарламаға қойылатын талаптардың тізімі |
| Макет (прототип) | Интерфейстің алдын ала салынған сызбасы |
| `lambda` | Бір жолдық атаусыз функция |
| `eval()` | Жолдағы Python өрнегін есептейтін функция (абайлап қолдану керек) |
| `columnspan` | `grid`-те виджеттің бірнеше бағанды алуы |
| `sticky` | Виджеттің ұяшық ішінде созылу бағыты (`n`, `s`, `e`, `w`) |
| `event.char` / `event.keysym` | Басылған перненің таңбасы / атауы |
| Тестілеу кестесі | Әрекеттер мен күтілетін нәтижелер тізімі |

## Ескерту

- `eval()` жолдағы кез келген Python кодын орындайды. Калькуляторда оны тек алдын ала тексерілген жолға қолданыңыз: мысалы, жолда тек рұқсат етілген таңбалар (`0123456789.+-*/()`) бар екенін `set(expr) <= set("0123456789.+-*/() ")` арқылы тексеріңіз.
- `/` операторы әрқашан `float` қайтарады: `6 / 2` → `3.0`. Артық `.0`-ді алып тастау үшін `result.is_integer()` әдісін қолдануға болады.
- Бөлшек сандар екілік жүйеде дәл сақталмайды: `0.1 + 0.2` → `0.30000000000000004`. Экранға шығарарда `round(result, 10)` қолданыңыз, ал дәл есептеулер (ақша) үшін `decimal.Decimal` модулі бар.
- PEP 8 бойынша класс атаулары `CapWords` стилінде (`Calculator`), ал әдістер мен айнымалылар `snake_case` стилінде (`create_widgets`) жазылады.
- Tkinter ресми құжаттамасы: https://docs.python.org/3/library/tkinter.html
