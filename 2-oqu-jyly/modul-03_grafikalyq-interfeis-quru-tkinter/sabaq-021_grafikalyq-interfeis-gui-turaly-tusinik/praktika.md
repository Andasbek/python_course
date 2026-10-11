# Сабақ 21. Практикалық тапсырмалар: GUI және Tkinter-мен танысу

## Базалық деңгей

### 1-тапсырма. CLI ме, GUI ме?
Кестені толтырыңыз (дәптерге): қосымша → интерфейс түрі → енгізу тәсілі.
`Калькулятор (Windows)`, `git`, `pip install`, `Telegram`, `Minecraft`, `IDLE Shell`, `Kaspi.kz`, `python -m venv venv`, `Блокнот`, `eGov.kz`.

### 2-тапсырма. Алғашқы терезе
Тақырыбы «Python үйірмесі» болатын терезе ашып, онда екі жазу (`Label`) шығарыңыз: өз атыңыз және «Алматы, 2026». Әр жазуға `font=("Arial", 16)` беріңіз.

### 3-тапсырма. Батырма — print
«Консольге жаз» батырмасы бар терезе жасаңыз. Басқан сайын консольге «Батырма басылды!» шықсын. Батырманы 5 рет басып, консольді бақылаңыз.

### 4-тапсырма. Санауыш
Терезеде «0» жазуы және «+1» батырмасы болсын. Басқан сайын сан 1-ге артсын (`label.config(text=...)`). Кеңес: санауышты глобал айнымалыда немесе тізімде сақтаңыз.

## Кеңейтілген деңгей

### 5-тапсырма. Бірнеше батырма
Терезеде «Қазақша», «Русский», «English» батырмалары және бір жазу болсын. Басылған батырмаға сәйкес жазу «Сәлеметсіз бе!», «Здравствуйте!», «Hello!» болып өзгерсін.

### 6-тапсырма. Консольдіктен GUI-ге
1-жылда жазған қарапайым консольдік бағдарламаңызды алыңыз (мысалы, BMI есептеу немесе тиындарды теңгеге айналдыру). Есептеу бөлігін функцияға шығарып, теориядағы температура мысалы сияқты GUI нұсқасын жасаңыз (Entry + Button + Label).

## ★ Олимпиадалық / шығармашылық тапсырма

### 7-тапсырма. «Сиқырлы шар»
«Сұрағыңызды ойлап, батырманы басыңыз» жазуы және «Жауап алу» батырмасы бар терезе. Басқанда кездейсоқ жауап («Иә», «Жоқ», «Әрине!», «Кейінірек сұра», «Күмәнім бар») шығады (`random.choice`). Қосымша: жауап түсін өзгертіңіз (`fg="green"` — оң жауапқа, `fg="red"` — теріс жауапқа).

## Үй тапсырмасы

1. Үйдегі компьютерде `python -m tkinter` командасын орындаңыз.
2. 4-тапсырмадағы санауышқа «−1» және «Нөлдеу» батырмаларын қосыңыз.
3. Кодты GitHub-қа `gui/lesson21_counter.py` деп жүктеңіз.
4. Ойланыңыз: терезенің өлшемін, түсін, орнын қалай өзгертуге болады?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.** GUI: Калькулятор, Telegram, Minecraft, Kaspi.kz (мобильді/веб GUI), Блокнот, eGov.kz (веб). CLI: git, pip install, python -m venv venv. IDLE Shell — терезелік бағдарлама ішіндегі интерактивті консоль (аралас).

**2-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Python үйірмесі")
tk.Label(root, text="Ару Серікқызы", font=("Arial", 16)).pack(padx=20, pady=5)
tk.Label(root, text="Алматы, 2026", font=("Arial", 16)).pack(padx=20, pady=5)
root.mainloop()
```

**3-тапсырма**
```python
import tkinter as tk


def on_click():
    print("Батырма басылды!")


root = tk.Tk()
root.title("Консоль")
tk.Button(root, text="Консольге жаз", command=on_click).pack(padx=30, pady=20)
root.mainloop()
```

**4-тапсырма** (үй тапсырмасымен бірге)
```python
import tkinter as tk

count = 0


def change(delta):
    global count
    count += delta
    label.config(text=str(count))


def reset():
    global count
    count = 0
    label.config(text="0")


root = tk.Tk()
root.title("Санауыш")
label = tk.Label(root, text="0", font=("Arial", 32))
label.pack(padx=40, pady=10)
tk.Button(root, text="+1", command=lambda: change(1)).pack(fill="x")
tk.Button(root, text="−1", command=lambda: change(-1)).pack(fill="x")
tk.Button(root, text="Нөлдеу", command=reset).pack(fill="x")
root.mainloop()
```
Ескерту: `lambda` — аргументі бар функцияны `command`-қа беру тәсілі (25-сабақта толық түсіндіріледі).

**5-тапсырма**
```python
import tkinter as tk


def kz():
    label.config(text="Сәлеметсіз бе!")


def ru():
    label.config(text="Здравствуйте!")


def en():
    label.config(text="Hello!")


root = tk.Tk()
root.title("Тілдер")
label = tk.Label(root, text="Тілді таңдаңыз", font=("Arial", 18))
label.pack(pady=10)
tk.Button(root, text="Қазақша", command=kz).pack(side="left", padx=5, pady=5)
tk.Button(root, text="Русский", command=ru).pack(side="left", padx=5, pady=5)
tk.Button(root, text="English", command=en).pack(side="left", padx=5, pady=5)
root.mainloop()
```

**6-тапсырма** (BMI мысалы)
```python
import tkinter as tk


def bmi(weight, height_cm):
    h = height_cm / 100
    return round(weight / (h * h), 1)


def calculate():
    w = float(weight_entry.get())
    h = float(height_entry.get())
    result.config(text=f"BMI = {bmi(w, h)}")


root = tk.Tk()
root.title("BMI")
tk.Label(root, text="Салмақ (кг):").pack()
weight_entry = tk.Entry(root)
weight_entry.pack()
tk.Label(root, text="Бой (см):").pack()
height_entry = tk.Entry(root)
height_entry.pack()
tk.Button(root, text="Есептеу", command=calculate).pack(pady=5)
result = tk.Label(root, text="")
result.pack()
root.mainloop()
```

**7-тапсырма**
```python
import random
import tkinter as tk

POSITIVE = ["Иә", "Әрине!"]
NEGATIVE = ["Жоқ", "Күмәнім бар"]
NEUTRAL = ["Кейінірек сұра"]


def answer():
    text = random.choice(POSITIVE + NEGATIVE + NEUTRAL)
    if text in POSITIVE:
        color = "green"
    elif text in NEGATIVE:
        color = "red"
    else:
        color = "gray"
    result.config(text=text, fg=color)


root = tk.Tk()
root.title("Сиқырлы шар")
tk.Label(root, text="Сұрағыңызды ойлап, батырманы басыңыз").pack(padx=20, pady=10)
tk.Button(root, text="Жауап алу", command=answer).pack()
result = tk.Label(root, text="?", font=("Arial", 28, "bold"))
result.pack(pady=15)
root.mainloop()
```

</details>
