# Сабақ 31. Практикалық тапсырмалар: «Калькулятор» терезелік қосымшасы

> Практикумның мақсаты — сабақ соңында GitHub-қа жүктеуге дайын, жұмыс істейтін калькулятор. Жұмысты кезең-кезеңімен орында, әр кезеңнен кейін бағдарламаны іске қосып тексер және `git commit` жаса.

## Базалық деңгей

### 1-қадам. Жоба папкасы және терезе
1. `calculator` папкасын құр, ішінде `git init` орында.
2. `calculator.py` файлында 300×400 өлшемді, «Калькулятор» тақырыпты, өлшемі өзгермейтін терезе жаса.
3. Терезенің жоғарғы жағына оң жаққа тураланған, 24 өлшемді қаріпті дисплей (`Entry`) орналастыр. Дисплейде бастапқыда `0` тұрсын.

```bash
mkdir calculator
cd calculator
git init
git add calculator.py
git commit -m "Калькулятор терезесі мен дисплейі"
```

### 2-қадам. Батырмалар торы
Теориядағы макет бойынша 4 бағанды тор құр: `C ⌫ % /`, `7 8 9 *`, `4 5 6 -`, `1 2 3 +`, `0 . =`. «0» батырмасы екі баған алсын (`columnspan=2`).
**Кеңес:** батырмаларды кортеждер тізімімен сипаттап, циклмен құр.

### 3-қадам. Цифрлар мен амалдарды енгізу
`press(symbol)` функциясын жаз: батырма басылғанда таңба өрнектің соңына қосылып, дисплейде көрінсін.

Мысал: `1`, `2`, `+`, `5` басылды → дисплейде `12+5`.

### 4-қадам. Есептеу, тазалау, өшіру
- `=` — өрнекті есептеп, нәтижені шығарады (`12+5` → `17`);
- `C` — дисплейді `0`-ге қайтарады;
- `⌫` — соңғы таңбаны өшіреді (`125` → `12`).

### 5-қадам. Қателерді өңдеу
- `8/0` → дисплейде «Нөлге бөлуге болмайды»;
- `8*` → «Қате өрнек»;
- `6/3` → `2` (емес `2.0`).

## Кеңейтілген деңгей

### 6-тапсырма. Пернетақтадан басқару
Цифрлар мен `+ - * / .` пернелері, `Enter` (есептеу), `BackSpace` (өшіру), `Escape` (тазалау) жұмыс істесін.

### 7-тапсырма. Қосымша батырмалар
`x²` (квадрат) және `√` (түбір) батырмаларын қос. Теріс санның түбірін алғанда «Қате» шықсын.
Мысал: `9` → `√` → `3`; `12` → `x²` → `144`.

### 8-тапсырма. Есептеу тарихы
Терезенің оң жағына (немесе төменгі жағына) `Listbox` қосып, әр есептеуді `12+5 = 17` түрінде сақта. Тарихтағы жолды екі рет басқанда нәтиже дисплейге қайта шықсын.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. eval-сыз калькулятор.** `eval` қолданбай, екі санды және бір амалды бөлек сақтайтын «қалта калькуляторы» логикасын жаз: сан енгізіледі → амал басылады → екінші сан → `=`. `2 + 3 =` → `5`, одан кейін `* 4 =` → `20`.

**Ә. Валюта конвертері қосымшасы.** Калькуляторға «₸ ↔ $» режимін қос: `Combobox`-тан бағытты таңдап, бағамды (мысалы, 1 $ = 480 ₸) бір өрісте сақта. API-ды кейін (VI модульде) қосамыз.

## Үй тапсырмасы

1. Калькуляторды аяқтап, GitHub-та `calculator` атты репозиторийге жүкте.
2. Репозиторийге `README.md` жаз: жоба сипаттамасы, мүмкіндіктері, іске қосу командасы, интерфейс скриншоты.
3. Тестілеу кестесіндегі (теорияда) 9 тексерісті өз калькуляторыңда орындап, нәтижесін `README.md`-ге «Тестілеу» бөлімі ретінде қос.

## Бағалау критерийлері

| Критерий | Сипаттама | Ұпай (%) |
|---|---|---|
| Бағдарламаның дұрыс жұмысы және талапқа сай келуі | 1–5 қадам толық орындалған, барлық тест өтеді | 40 |
| Алгоритмнің тиімділігі, шешімнің ұтымдылығы | Батырмалар цикл/тізіммен құрылған, бір ортақ `press()` функциясы, қателер өңделген | 25 |
| Кодтың оқылымдылығы | Түсінікті атаулар, класс құрылымы, docstring, түсініктемелер | 20 |
| Өз бетінше және мерзімінде орындау | Сабақ соңында жұмыс істейтін нұсқа + commit тарихы | 15 |
| **Барлығы** | | **100** |

Қосымша мүмкіндіктер (6–8, ★) жоғары деңгейге (85–100%) жетуге көмектеседі.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–6 қадамдар.** Толық шешім теориядағы «Толық үлгі: Калькулятор қосымшасы» бөлімінде берілген (`Calculator` класы). Терезе өлшемі үшін `root.geometry("300x400")` қосуға болады; `grid` бағандары біркелкі созылуы үшін:

```python
import tkinter as tk

root = tk.Tk()
root.geometry("300x400")
for col in range(4):
    root.columnconfigure(col, weight=1)
for row in range(6):
    root.rowconfigure(row, weight=1)
```

**7-тапсырма. x² және √ (Calculator класына қосылатын әдістер):**

```python
import math


def square(self):
    try:
        value = self.safe_eval(self.expression)
        self.expression = self.format_number(value ** 2)
        self.update_display()
    except (ValueError, SyntaxError, ZeroDivisionError):
        self.expression = ""
        self.display_var.set("Қате")


def square_root(self):
    try:
        value = self.safe_eval(self.expression)
        if value < 0:
            raise ValueError("Теріс сан")
        self.expression = self.format_number(math.sqrt(value))
        self.update_display()
    except (ValueError, SyntaxError, ZeroDivisionError):
        self.expression = ""
        self.display_var.set("Қате")
```

`BUTTONS` тізіміне `("x²", 6, 0, 1)`, `("√", 6, 1, 1)` қосып, `press()` ішінде `elif symbol == "x²": self.square()` және `elif symbol == "√": self.square_root()` тармақтарын жазу керек.

**8-тапсырма. Есептеу тарихы:**

```python
import tkinter as tk

ALLOWED = set("0123456789+-*/.() ")


class HistoryCalculator:
    """Тарихы бар қысқартылған калькулятор (енгізу өрісі арқылы)."""

    def __init__(self, root):
        self.root = root
        root.title("Тарихы бар калькулятор")
        self.entry = tk.Entry(root, font=("Arial", 18), justify="right")
        self.entry.grid(row=0, column=0, padx=5, pady=5, sticky="ew")
        tk.Button(root, text="=", width=4,
                  command=self.calculate).grid(row=0, column=1)
        self.history = tk.Listbox(root, width=30, height=8)
        self.history.grid(row=1, column=0, columnspan=2, padx=5, pady=5)
        self.history.bind("<Double-Button-1>", self.restore)
        root.bind("<Return>", lambda event: self.calculate())

    def calculate(self):
        expr = self.entry.get()
        try:
            if not expr or not set(expr) <= ALLOWED:
                raise ValueError
            result = eval(expr, {"__builtins__": {}}, {})
            if isinstance(result, float) and result.is_integer():
                result = int(result)
            self.history.insert(tk.END, f"{expr} = {result}")
            self.entry.delete(0, tk.END)
            self.entry.insert(0, str(result))
        except ZeroDivisionError:
            self.entry.delete(0, tk.END)
            self.entry.insert(0, "Нөлге бөлуге болмайды")
        except (SyntaxError, ValueError):
            self.entry.delete(0, tk.END)
            self.entry.insert(0, "Қате өрнек")

    def restore(self, event):
        selection = self.history.curselection()
        if selection:
            line = self.history.get(selection[0])
            result = line.split(" = ")[1]
            self.entry.delete(0, tk.END)
            self.entry.insert(0, result)


if __name__ == "__main__":
    root = tk.Tk()
    HistoryCalculator(root)
    root.mainloop()
```

**★ А. eval-сыз калькулятор (логика бөлек функцияда, консольде тексеруге болады):**

```python
class PocketCalculator:
    """Екі операнд пен бір амалды сақтайтын калькулятор логикасы."""

    def __init__(self):
        self.current = "0"
        self.first = None
        self.operation = None
        self.new_number = True

    def digit(self, d):
        if self.new_number:
            self.current = d
            self.new_number = False
        else:
            self.current += d

    def set_operation(self, op):
        if self.operation and not self.new_number:
            self.equals()
        self.first = float(self.current)
        self.operation = op
        self.new_number = True

    def equals(self):
        if self.operation is None:
            return self.current
        second = float(self.current)
        if self.operation == "+":
            result = self.first + second
        elif self.operation == "-":
            result = self.first - second
        elif self.operation == "*":
            result = self.first * second
        elif second == 0:
            self.current = "Қате"
            self.operation = None
            self.new_number = True
            return self.current
        else:
            result = self.first / second
        if result.is_integer():
            result = int(result)
        self.current = str(result)
        self.operation = None
        self.new_number = True
        return self.current


calc = PocketCalculator()
calc.digit("2")
calc.set_operation("+")
calc.digit("3")
print(calc.equals())
calc.set_operation("*")
calc.digit("4")
print(calc.equals())
```

Нәтижесі:
```text
5
20
```
Бұл класты Tkinter батырмаларына қосу: цифрлар → `calc.digit(t)`, амалдар → `calc.set_operation(t)`, `=` → `display_var.set(calc.equals())`.

**★ Ә. Валюта конвертері (қысқа үлгі):**

```python
import tkinter as tk
from tkinter import ttk

RATE = 480  # 1 доллардың теңгедегі бағамы (мысал)


def convert():
    try:
        amount = float(amount_entry.get())
    except ValueError:
        result_label.config(text="Сан енгізіңіз")
        return
    if direction.get() == "₸ → $":
        result_label.config(text=f"{amount / RATE:.2f} $")
    else:
        result_label.config(text=f"{amount * RATE:.2f} ₸")


root = tk.Tk()
root.title("Валюта конвертері")
amount_entry = tk.Entry(root, font=("Arial", 14))
amount_entry.grid(row=0, column=0, padx=5, pady=5)
direction = ttk.Combobox(root, values=["₸ → $", "$ → ₸"], state="readonly")
direction.current(0)
direction.grid(row=0, column=1, padx=5)
tk.Button(root, text="Айырбастау", command=convert).grid(row=1, column=0, columnspan=2)
result_label = tk.Label(root, text="", font=("Arial", 16))
result_label.grid(row=2, column=0, columnspan=2, pady=10)
root.mainloop()
```

</details>
