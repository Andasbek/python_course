# Сабақ 100. Жобаны жетілдіру. Қосымша мүмкіндіктер қосу.

## Тақырыптар

1. «Жұмыс істейді» ≠ «аяқталған»
2. Қосымша мүмкіндікті қалай таңдаймыз?
3. Мүмкіндікті қауіпсіз қосу: жеке тармақ
4. Үлгі мүмкіндіктер
5. Безендіру: біркелкі стиль
6. Мүмкіндіктерді қатыру (feature freeze)
7. Қосымша: жобаны `.exe` файлына айналдыру
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. «Жұмыс істейді» ≠ «аяқталған»

MVP — негізгі мәселені шешеді, бірақ оны бейтаныс адамға беруге әлі ерте. **Аяқталған өнім** — оны кез келген пайдаланушы түсіндірмесіз қолдана алады, ол құламайды және ұқыпты көрінеді.

**Аяқталғандық критерийлері (Definition of Done)** — жобаның «дайын» екенін анықтайтын тізім:

| Бөлім | Критерий |
|---|---|
| Функционал | ТТ-дағы барлық Must және таңдалған Should мүмкіндіктер бар |
| Тұрақтылық | сыни/маңызды қателер жоқ; барлық тест жасыл; интернетсіз/файлсыз жағдайлар өңделген |
| Ыңғайлылық | барлық мәтін қазақша және сауатты; батырмалар біркелкі; бос күйлер мен қателер түсіндірілген |
| Безендіру | бір стиль (түстер, қаріптер, аралықтар); терезе атауы мен белгішесі; «Бағдарлама туралы» |
| Код | PEP 8; docstring; өлі код (қолданылмайтын функциялар, түсініктемеге алынған код) жоқ |
| Репозиторий | README, `requirements.txt`, `.gitignore`; таза ортада іске қосылады |

> **Ереже:** жаңа мүмкіндік қоспас бұрын бар мүмкіндіктер критерийлерге сай болсын. Жарты-жартылай 10 мүмкіндіктен гөрі мінсіз 5 мүмкіндік жақсы.

## 2. Қосымша мүмкіндікті қалай таңдаймыз?

Уақыт шектеулі, сондықтан әдетте 1–2 мүмкіндік қана қосылады. Таңдау үшін әр үміткерді үш сұрақпен бағалаймыз:

| Мүмкіндік | Пайдаланушыға пайдасы (1–3) | Техникалық құндылығы (1–3) | Тәуекел/уақыт (1–3, аз — жақсы) | Ұпай = П + Қ − Т |
|---|---|---|---|---|
| Ай бойынша сүзгі | 3 | 2 | 1 | **4** |
| Долларға аудару (API) | 2 | 3 | 2 | **3** |
| CSV экспорт | 2 | 2 | 1 | **3** |
| Түнгі режим | 1 | 2 | 2 | 1 |
| Бірнеше пайдаланушы + құпия сөз | 2 | 2 | 3 | 1 |

**«Техникалық құндылығы»** — мүмкіндік жаңа технологияны немесе архитектураның маңызды бөлігін қоса ма? Мысалы, API қосу жобаны желімен, JSON-мен жұмыс және желі қателерін өңдеумен кеңейтеді.

## 3. Мүмкіндікті қауіпсіз қосу: жеке тармақ

Жаңа мүмкіндік жұмыс істеп тұрған нұсқаны бұзбауы керек. Сондықтан оны **жеке тармақта** жасаймыз:

```bash
git switch -c feature/export-csv      # жаңа тармақ
# ... код жазу, тексеру, коммиттер ...
python -m unittest discover -s tests  # барлық тест жасыл ма?
git switch main
git merge feature/export-csv          # тек жұмыс істесе ғана біріктіреміз
git push
```

Мүмкіндік уақытында бітпесе — тармақты біріктірмейміз, `main` тұрақты күйде қалады. Бұл — релиз алдындағы ең маңызды қауіпсіздік ережесі.

## 4. Үлгі мүмкіндіктер

### 4.1. Баптауларды сақтау (config)
Пайдаланушы таңдаған параметрлер (тақырып, валюта, соңғы санат, терезе өлшемі) келесі іске қосуда сақталуы керек — ыңғайлылықтың «есте сақтауды талап етпе» ережесі.

```python
"""settings.py — пайдаланушы баптаулары."""
import json
from pathlib import Path

DEFAULTS = {"theme": "light", "currency": "KZT", "last_category": "Тамақ",
            "geometry": "820x480"}


class Settings:
    def __init__(self, path):
        self.path = Path(path)
        self.data = dict(DEFAULTS)
        try:
            self.data.update(json.loads(self.path.read_text(encoding="utf-8")))
        except (FileNotFoundError, json.JSONDecodeError):
            pass

    def __getitem__(self, key):
        return self.data[key]

    def __setitem__(self, key, value):
        self.data[key] = value
        self.save()

    def save(self):
        self.path.write_text(json.dumps(self.data, ensure_ascii=False, indent=2),
                             encoding="utf-8")


if __name__ == "__main__":
    s = Settings("settings_demo.json")
    s["theme"] = "dark"
    print(Settings("settings_demo.json")["theme"])
    Path("settings_demo.json").unlink()
```

Нәтижесі:
```text
dark
```

`__getitem__` және `__setitem__` — магиялық әдістер: объектімен сөздік сияқты жұмыс істеуге мүмкіндік береді: `settings["theme"]`.

### 4.2. Экспорт: «Сақтау» диалогымен
```python
import csv
from tkinter import filedialog, messagebox


def export_operations(operations):
    """Операцияларды пайдаланушы таңдаған CSV файлына сақтайды."""
    path = filedialog.asksaveasfilename(
        title="CSV ретінде сақтау",
        defaultextension=".csv",
        filetypes=[("CSV файл", "*.csv"), ("Барлық файлдар", "*.*")],
        initialfile="qalta_export.csv",
    )
    if not path:                       # пайдаланушы «Болдырмау» басты
        return
    try:
        with open(path, "w", newline="", encoding="utf-8-sig") as f:
            writer = csv.writer(f, delimiter=";")
            writer.writerow(["Күні", "Санат", "Сома", "Түсініктеме"])
            for op in operations:
                writer.writerow([op["op_date"], op["category"], op["amount"], op["note"]])
    except OSError as err:
        messagebox.showerror("Қате", f"Файлды сақтау мүмкін болмады:\n{err}")
        return
    messagebox.showinfo("Экспорт", f"{len(operations)} жазба сақталды:\n{path}")
```

Диаграмманы сурет ретінде сақтау одан да оңай: `self.figure.savefig(path, dpi=150)`.

### 4.3. Ойын жобасына арналған мысал мүмкіндіктер
- рекордтар кестесі (JSON) және оны мәзірде көрсету;
- қиындық деңгейлері (жеңіл/орта/қиын — жылдамдық коэффициенті);
- дыбысты қосу/өшіру баптауы (`settings["sound"]`);
- кідіріс экраны (күйлердің ақырлы автоматы).

## 5. Безендіру: біркелкі стиль

Біркелкі стиль бір жерде анықталады — `ttk.Style`. Түстер мен қаріптер тұрақтылар ретінде:

```python
import tkinter as tk
from tkinter import ttk, messagebox

PRIMARY = "#1565c0"      # негізгі түс
ACCENT = "#2e7d32"       # екпін (сәтті әрекет)
DANGER = "#c62828"       # қауіпті әрекет
FONT = ("Segoe UI", 11)
APP_NAME = "Қалта ақшасы"
VERSION = "1.0"


def apply_style(root):
    """Бүкіл қосымшаға ортақ стиль."""
    style = ttk.Style(root)
    style.theme_use("clam")                       # барлық ОЖ-да бірдей көрінеді
    style.configure(".", font=FONT)
    style.configure("TButton", padding=(10, 5))
    style.configure("Primary.TButton", background=PRIMARY, foreground="white")
    style.map("Primary.TButton", background=[("active", "#0d47a1")])
    style.configure("Danger.TButton", background=DANGER, foreground="white")
    style.configure("Title.TLabel", font=("Segoe UI", 18, "bold"), foreground=PRIMARY)


def build_menu(root):
    """Мәзір: Файл және Анықтама."""
    menubar = tk.Menu(root)
    file_menu = tk.Menu(menubar, tearoff=False)
    file_menu.add_command(label="CSV экспорт...", accelerator="Ctrl+E")
    file_menu.add_separator()
    file_menu.add_command(label="Шығу", command=root.destroy)
    menubar.add_cascade(label="Файл", menu=file_menu)

    help_menu = tk.Menu(menubar, tearoff=False)
    help_menu.add_command(label="Бағдарлама туралы", command=lambda: messagebox.showinfo(
        "Бағдарлама туралы",
        f"{APP_NAME} v{VERSION}\nКіріс пен шығысты есептейтін қосымша.\n"
        "Авторы: Аты-жөні, 2027. MIT лицензиясы."))
    menubar.add_cascade(label="Анықтама", menu=help_menu)
    root.config(menu=menubar)


if __name__ == "__main__":
    root = tk.Tk()
    root.title(f"{APP_NAME} v{VERSION}")
    apply_style(root)
    build_menu(root)
    ttk.Label(root, text="Баланс: 12 450 тг", style="Title.TLabel").pack(padx=20, pady=10)
    ttk.Button(root, text="Сақтау", style="Primary.TButton").pack(pady=4)
    ttk.Button(root, text="Жою", style="Danger.TButton").pack(pady=4)
    root.mainloop()
```

Безендірудің чек-парағы:
- [ ] Барлық батырмалар бір стильде; қауіпті әрекет — қызыл, негізгі әрекет — негізгі түсте.
- [ ] Аралықтар бірдей (`padx`/`pady` = 8 немесе 10).
- [ ] Терезе атауында бағдарлама аты мен нұсқасы.
- [ ] Терезе белгішесі: `root.iconphoto(True, tk.PhotoImage(file="assets/icon.png"))`.
- [ ] «Бағдарлама туралы» терезесі: атауы, нұсқасы, авторы, жылы.
- [ ] Бос күйлер: «Әзірге операция жоқ» сияқты кеңестер.
- [ ] Емле: барлық мәтінді дауыстап оқып шығу (қазақ әріптері: ә, ғ, қ, ң, ө, ұ, ү, һ, і).

## 6. Мүмкіндіктерді қатыру (feature freeze)

Кәсіби командаларда релизге бірнеше күн қалғанда **мүмкіндіктер қатырылады**: одан кейін тек қателер түзетіледі, құжат жазылады, презентация дайындалады. Себебі әр жаңа мүмкіндік — жаңа қате тәуекелі, ал оны тестілеуге уақыт жоқ.

Релиз алдындағы әдеттегі рет:
```text
мүмкіндіктерді әзірлеу ──► FEATURE FREEZE
                           ├── тек қате түзету және құжаттау
                           └── релиз v1.0 (тег, GitHub Release)
```

Feature freeze-ден кейін аяқталмаған идеялар README-дегі **«Болашақтағы жоспарлар»** (Roadmap) бөліміне немесе `enhancement` белгісі бар Issues-ке жазылады — сонда жобаның даму перспективасы көрінеді.

## 7. Қосымша: жобаны `.exe` файлына айналдыру

Python орнатылмаған компьютерде іске қосу үшін PyInstaller қолданылады (міндетті емес):

```bash
pip install pyinstaller
pyinstaller --onefile --windowed --name QaltaAqshasy main.py
```

Дайын файл `dist/QaltaAqshasy.exe` ішінде пайда болады. `build/`, `dist/`, `*.spec` файлдарын `.gitignore`-ға қосыңыз; `.exe`-ні GitHub Releases бетіне жүктеуге болады.

## Жиі кездесетін қателер

**1. «Тағы бір мүмкіндік» синдромы.** Релизге екі күн қалғанда күрделі мүмкіндік бастап, бүкіл жобаны бұзу. Дұрысы: жеке тармақ + feature freeze.

**2. Тесттерді іске қоспай біріктіру.** `merge`-ден кейін бағдарлама іске қосылмайды. Дұрысы: біріктірмес бұрын барлық тест және негізгі сценарийлер.

**3. Стильді әр виджетке жеке жазу.**
```python
import tkinter as tk

root = tk.Tk()
tk.Button(root, text="Сақтау", bg="#1565c0", fg="white", font=("Arial", 11))
tk.Button(root, text="Ашу", bg="#1565c1", fg="white", font=("Arial", 12))   # сәл басқа!
```
Түсіндірме: түстер мен қаріптер бірте-бірте «айырылып» кетеді. Дұрысы: тұрақтылар және `ttk.Style`.

**4. Өлі код.** Түсініктемеге алынған ескі нұсқалар, `test2.py`, `old_gui.py`. Мұндай код оқырман мен сүйемелдеушіні шатастырады. Дұрысы: өшіру — Git тарихында бәрібір сақталады.

**5. Баптаулар файлын репозиторийге жүктеу** — әр пайдаланушының өз баптаулары болуы керек. `settings.json`-ды `.gitignore`-ға, ал әдепкі мәндерді кодқа.

## Негізгі ұғымдар
| Ұғым | Түсіндірме |
|---|---|
| Definition of Done | жобаның «дайын» екенін анықтайтын критерийлер тізімі |
| Мүмкіндік тармағы (feature branch) | жаңа мүмкіндік жасалатын жеке Git тармағы |
| `ttk.Style` | ttk виджеттерінің біркелкі стилін бір жерде баптау құралы |
| Баптаулар файлы | пайдаланушы параметрлерін сақтайтын файл (JSON) |
| Feature freeze | релиз алдында жаңа мүмкіндік қосуды тоқтату |
| Өлі код | бағдарламада қолданылмайтын код |
| PyInstaller | Python бағдарламасын орындалатын файлға айналдыратын құрал |

## Ескерту

- `"clam"` тақырыбы `ttk.Button`-ның `background`-ын өзгертуге мүмкіндік береді; Windows (`"vista"`, `"winnative"`) және macOS (`"aqua"`) төл тақырыптарында батырманың фон түсі жиі еленбейді.
- `Path.read_text()`/`write_text()` әдепкі бойынша жүйенің кодтауын қолданады — әрдайым `encoding="utf-8"` беріңіз, әйтпесе Windows-та қазақ әріптері cp1251-де сақталуы мүмкін.
- Баптаулар файлын бағдарламаның қасында емес, пайдаланушы бумасында (`Path.home() / ".qalta" / "settings.json"`) сақтаған дұрыс: онда пайдаланушының жазу құқығы міндетті түрде бар.
- `filedialog.asksaveasfilename()` «Болдырмау» басылғанда бос жол `""` қайтарады — файлды ашпас бұрын нәтижені тексеріңіз.
- PyInstaller файлды тек өзі іске қосылған ОЖ үшін жинайды: Windows-қа арналған `.exe`-ні Windows-та жинау керек.
- Құжаттама: https://docs.python.org/3/library/tkinter.ttk.html#ttk-styling және https://pyinstaller.org/en/stable/
