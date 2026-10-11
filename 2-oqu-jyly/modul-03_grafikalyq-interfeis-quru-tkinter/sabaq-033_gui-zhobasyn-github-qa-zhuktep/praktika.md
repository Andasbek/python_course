# Сабақ 33. Практикалық тапсырмалар: GUI жобасын қорғау және Өзіндік жұмыс №2

## Базалық деңгей: репозиторийді қорғауға дайындау (сабақтың 1-бөлімі)

### 1-тапсырма. Репозиторийді тексер (чек-парақ)
Калькулятор немесе To-Do жобаңның репозиторийін ашып, әр пунктті белгіле:

| № | Тексеріс | ✔ |
|---|---|---|
| 1 | Негізгі `.py` файлы бар және іске қосылады | ☐ |
| 2 | `README.md` бар: атауы, сипаттама, мүмкіндіктер, іске қосу | ☐ |
| 3 | README-де скриншот көрінеді | ☐ |
| 4 | `.gitignore` бар, `__pycache__/`, `venv/` репозиторийде жоқ | ☐ |
| 5 | Кемінде 4 мағыналы commit бар | ☐ |
| 6 | Код класс түрінде, әдістерде docstring бар | ☐ |
| 7 | Қате енгізуде бағдарлама құламайды | ☐ |

### 2-тапсырма. Таза папкадан тексеру
Репозиторийді басқа папкаға клонда да, сол жерден іске қос:
```bash
cd ..
git clone https://github.com/username/todo-app.git test-clone
cd test-clone
python todo_app.py
```

### 3-тапсырма. Таныстыруды дайында
Теориядағы 5 бөлімдік құрылым бойынша қысқа жоспар жаз (әр бөлімге 1–2 сөйлем).

### Қорғау регламенті
1. Кезекті мұғалім белгілейді; оқушы жобасын проектор арқылы көрсетеді.
2. Таныстыру — 3–4 мин, сұрақтар — 1 мин (мұғалім және 1–2 оқушы).
3. Тыңдаушылар өзара бағалау парағын толтырады («жақсы жағы — ұсыныс — жалпы бағалау»).
4. Мұғалім модульдік жобаның бағалау кестесін толтырады (төменде).

**Өзара бағалау парағы:**

| Жоба авторы | Жұмыс істейді ме? (0–3) | Интерфейс ыңғайлы ма? (0–3) | Таныстыру түсінікті ме? (0–3) | Жақсы жағы | Ұсыныс |
|---|---|---|---|---|---|
| | | | | | |

## Өзіндік жұмыс №2 (сабақтың 2-бөлімі, 40 минут)

> Жұмысты `ozindik-zhumys-2` папкасында орында, файл атауы `variant1.py` немесе `variant2.py`. Соңында репозиторийге push жаса.

### 1-нұсқа. «Дене салмағының индексі (ДСИ) калькуляторы»

ДСИ = салмақ (кг) / бой (м)². Мысалы, 50 кг, 160 см → 50 / 1.6² = 19.5.

**1-тапсырма (интерфейс, 3 ұпай).** «ДСИ калькуляторы» тақырыпты терезе. `grid` арқылы: «Салмағы (кг):» + `Entry`, «Бойы (см):» + `Entry`, «Есептеу» батырмасы, нәтиже `Label`-і.

**2-тапсырма (логика, 4 ұпай).** «Есептеу» басылғанда ДСИ бір ондық таңбаға дейін есептеліп, санатымен шығады:
- 18.5-тен кем — «Салмақ жеткіліксіз»;
- 18.5–24.9 — «Қалыпты салмақ»;
- 25 және одан жоғары — «Артық салмақ».

Мысал: `50`, `160` → `ДСИ: 19.5 — Қалыпты салмақ`.

**3-тапсырма (қателерді өңдеу, 2 ұпай).** Сан емес мән немесе нөл/теріс сан енгізілсе — `messagebox.showerror` арқылы «Оң сан енгізіңіз» хабары.

**4-тапсырма (кеңейту, 1 ұпай).** `Enter` пернесімен есептеу және «Тазалау» батырмасы.

### 2-нұсқа. «Температура конвертері»

°F = °C × 9/5 + 32; °C = (°F − 32) × 5/9.

**1-тапсырма (интерфейс, 3 ұпай).** «Температура конвертері» тақырыпты терезе: «Температура:» + `Entry`, екі `Radiobutton` («°C → °F», «°F → °C»), «Айырбастау» батырмасы, нәтиже `Label`-і.

**2-тапсырма (логика, 4 ұпай).** Таңдалған бағыт бойынша есептеп, бір ондық таңбамен шығару. Қосымша: нәтиже бойынша °C-тағы мәнге қарай сипаттама — 0-ден төмен «Аяз», 0–20 «Салқын», 20-дан жоғары «Жылы».

Мысал: `25`, «°C → °F» → `77.0 °F — Жылы`; `14`, «°F → °C» → `-10.0 °C — Аяз`.

**3-тапсырма (қателерді өңдеу, 2 ұпай).** Сан емес мән енгізілсе — `messagebox.showerror` арқылы «Сан енгізіңіз». −273.15 °C-тан (абсолют нөл) төмен мән — «Мұндай температура болмайды».

**4-тапсырма (кеңейту, 1 ұпай).** `Enter` пернесімен есептеу және «Тазалау» батырмасы.

## Кеңейтілген деңгей

Негізгі нұсқаны ерте аяқтаған оқушыларға:
- **А.** Есептеу тарихын `Listbox`-та көрсет (соңғы 5 нәтиже).
- **Ә.** Қосымшаны класс түрінде қайта жаз (егер функциялармен жазылған болса), docstring қос.

## ★ Олимпиадалық / шығармашылық тапсырма

Екі нұсқаны бір қосымшаға біріктір: `ttk.Notebook` (қойындылар) арқылы «ДСИ» және «Температура» қойындылары бар «Денсаулық пен ауа райы көмекшісі» жаса.

## Үй тапсырмасы

1. Қорғауда алған ұсыныстар бойынша GUI жобаңды жетілдіріп, push жаса.
2. Pygame кітапханасы туралы 1–2 ойын мысалын іздеп тап (pygame.org сайтынан) және оның қандай жанрда екенін жазып кел.
3. Мүмкіндігінше үйдегі компьютерге `pip install pygame` орнат.

## Бағалау критерийлері

**Өзіндік жұмыс №2 (10 ұпай = 100%)**

| Критерий | Сипаттама | % |
|---|---|---|
| Бағдарламаның дұрыс жұмысы және талапқа сай келуі | 1–2 тапсырмалар орындалған, мысалдар дұрыс | 40 |
| Алгоритмнің тиімділігі, шешімнің ұтымдылығы | Қателер өңделген (3-тапсырма), артық қайталау жоқ | 25 |
| Кодтың оқылымдылығы | Атаулар, түсініктеме, құрылым (функция/класс) | 20 |
| Өз бетінше және уақытында тапсыру | 40 мин ішінде push жасалған | 15 |

Ұпайды пайызға аудару: 9–10 ұпай — жоғары деңгей (85–100%), 6–8 — орта (60–84%), 4–5 — бастапқы (40–59%).

**GUI модульдік жобасын бағалау (қорғау)**

| Критерий | % | Ұпай |
|---|---|---|
| Техникалық талаптардың орындалуы (модуль тақырыптарын қамтуы: виджеттер, орналастыру, оқиғалар, диалогтар) | 30 | |
| Бағдарламаның тұрақты әрі қатесіз жұмысы | 25 | |
| Кодтың құрылымы мен сапасы (PEP 8, кластарға бөлу, docstring) | 20 | |
| Пайдаланушыға ыңғайлылығы және безендірілуі | 15 | |
| GitHub-қа дұрыс жүктеу және README | 10 | |
| **Барлығы** | **100** | |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-нұсқа. ДСИ калькуляторы (толық шешім, кеңейтуімен):**

```python
import tkinter as tk
from tkinter import messagebox


class BmiApp:
    """Дене салмағының индексін есептейтін қосымша."""

    def __init__(self, root):
        self.root = root
        root.title("ДСИ калькуляторы")
        tk.Label(root, text="Салмағы (кг):").grid(row=0, column=0, sticky="e", padx=5, pady=5)
        self.weight = tk.Entry(root)
        self.weight.grid(row=0, column=1, padx=5)
        tk.Label(root, text="Бойы (см):").grid(row=1, column=0, sticky="e", padx=5, pady=5)
        self.height = tk.Entry(root)
        self.height.grid(row=1, column=1, padx=5)
        tk.Button(root, text="Есептеу", command=self.calculate).grid(row=2, column=0, pady=5)
        tk.Button(root, text="Тазалау", command=self.clear).grid(row=2, column=1)
        self.result = tk.Label(root, text="", font=("Arial", 12, "bold"))
        self.result.grid(row=3, column=0, columnspan=2, pady=10)
        root.bind("<Return>", lambda event: self.calculate())

    @staticmethod
    def category(bmi):
        if bmi < 18.5:
            return "Салмақ жеткіліксіз"
        if bmi < 25:
            return "Қалыпты салмақ"
        return "Артық салмақ"

    def calculate(self):
        try:
            weight = float(self.weight.get())
            height = float(self.height.get()) / 100
            if weight <= 0 or height <= 0:
                raise ValueError
        except ValueError:
            messagebox.showerror("Қате", "Оң сан енгізіңіз")
            return
        bmi = weight / height ** 2
        self.result.config(text=f"ДСИ: {bmi:.1f} — {self.category(bmi)}")

    def clear(self):
        self.weight.delete(0, tk.END)
        self.height.delete(0, tk.END)
        self.result.config(text="")


if __name__ == "__main__":
    root = tk.Tk()
    BmiApp(root)
    root.mainloop()
```

Тексеру: 50 кг, 160 см → `ДСИ: 19.5 — Қалыпты салмақ`; 45 кг, 170 см → `15.6 — Салмақ жеткіліксіз`; 90 кг, 175 см → `29.4 — Артық салмақ`.

**2-нұсқа. Температура конвертері:**

```python
import tkinter as tk
from tkinter import messagebox


class TempApp:
    """Цельсий мен Фаренгейт арасындағы конвертер."""

    def __init__(self, root):
        self.root = root
        root.title("Температура конвертері")
        tk.Label(root, text="Температура:").grid(row=0, column=0, padx=5, pady=5)
        self.entry = tk.Entry(root)
        self.entry.grid(row=0, column=1, padx=5)
        self.mode = tk.StringVar(value="c2f")
        tk.Radiobutton(root, text="°C → °F", variable=self.mode,
                       value="c2f").grid(row=1, column=0)
        tk.Radiobutton(root, text="°F → °C", variable=self.mode,
                       value="f2c").grid(row=1, column=1)
        tk.Button(root, text="Айырбастау", command=self.convert).grid(row=2, column=0, pady=5)
        tk.Button(root, text="Тазалау", command=self.clear).grid(row=2, column=1)
        self.result = tk.Label(root, text="", font=("Arial", 12, "bold"))
        self.result.grid(row=3, column=0, columnspan=2, pady=10)
        root.bind("<Return>", lambda event: self.convert())

    @staticmethod
    def describe(celsius):
        if celsius < 0:
            return "Аяз"
        if celsius <= 20:
            return "Салқын"
        return "Жылы"

    def convert(self):
        try:
            value = float(self.entry.get())
        except ValueError:
            messagebox.showerror("Қате", "Сан енгізіңіз")
            return
        if self.mode.get() == "c2f":
            celsius = value
            text = f"{value * 9 / 5 + 32:.1f} °F"
        else:
            celsius = (value - 32) * 5 / 9
            text = f"{celsius:.1f} °C"
        if celsius < -273.15:
            messagebox.showerror("Қате", "Мұндай температура болмайды")
            return
        self.result.config(text=f"{text} — {self.describe(celsius)}")

    def clear(self):
        self.entry.delete(0, tk.END)
        self.result.config(text="")


if __name__ == "__main__":
    root = tk.Tk()
    TempApp(root)
    root.mainloop()
```

Тексеру: 25 °C → `77.0 °F — Жылы`; 14 °F → `-10.0 °C — Аяз`; 0 °C → `32.0 °F — Салқын`.

**Кеңейтілген А. Тарих (соңғы 5 нәтиже)** — кез келген нұсқаға қосылатын үзінді:

```python
import tkinter as tk

root = tk.Tk()
history = tk.Listbox(root, height=5, width=35)
history.grid(row=4, column=0, columnspan=2, padx=5, pady=5)


def add_to_history(text):
    history.insert(0, text)          # жаңасы жоғарыда
    if history.size() > 5:
        history.delete(5, tk.END)    # 5-тен артығын өшіру
```

**★ Біріктірілген қосымша (ttk.Notebook):**

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Денсаулық пен ауа райы көмекшісі")
notebook = ttk.Notebook(root)
bmi_tab = tk.Frame(notebook)
temp_tab = tk.Frame(notebook)
notebook.add(bmi_tab, text="ДСИ")
notebook.add(temp_tab, text="Температура")
notebook.pack(fill="both", expand=True)
# BmiApp(bmi_tab) және TempApp(temp_tab) — жоғарыдағы кластар.
# Олардағы root.title(...) жолын тек терезе болғанда шақыру үшін
# isinstance(root, tk.Tk) тексерісін қосу жеткілікті.
root.mainloop()
```

</details>
