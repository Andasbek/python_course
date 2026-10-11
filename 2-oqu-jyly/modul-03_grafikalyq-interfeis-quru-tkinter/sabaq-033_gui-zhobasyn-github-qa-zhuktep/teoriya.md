# Сабақ 33. GUI жобасын GitHub-қа жүктеп қорғау. Өзіндік жұмыс №2.

## Тақырыптар

1. Жақсы репозиторийдің құрылымы
2. README.md жазу
3. Скриншот жасау
4. Мағыналы commit хабарламалары
5. GitHub-қа жүктеу: командалар реті
6. Жарияламас бұрын жобаны тексеру
7. Tkinter бойынша шпаргалка
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Жақсы репозиторийдің құрылымы

GitHub-тағы репозиторий — сенің **портфолиоң**. Оны басқа әзірлеушілер немесе жұмыс беруші көре алады. Ұқыпты рәсімделген жоба «бұл адам өз жұмысын сыйлайды» дегенді білдіреді.

```text
todo-app/
├── todo_app.py          # негізгі бағдарлама
├── README.md            # жоба сипаттамасы
├── .gitignore           # репозиторийге түспейтін файлдар
└── screenshots/
    └── main_window.png  # интерфейс скриншоты
```

**`.gitignore` мысалы** — уақытша файлдар мен жеке деректерді Git-ке қоспау үшін:

```text
__pycache__/
*.pyc
venv/
.vscode/
tasks.txt
```

## 2. README.md жазу

README — жобаның «визит карточкасы». Ол Markdown белгілеу тілінде жазылады.

**README үлгісі:**

```text
# To-Do List — тапсырмалар тізімі

Tkinter кітапханасында жасалған, деректерін файлда сақтайтын тапсырмалар тізімі.

## Мүмкіндіктері
- тапсырма қосу (батырма немесе Enter);
- орындалды деп белгілеу (екі рет басу);
- өңдеу және өшіру;
- терезе жабылғанда автоматты сақтау.

## Скриншот
![Негізгі терезе](screenshots/main_window.png)

## Іске қосу
python todo_app.py

## Қолданылған технологиялар
Python 3.12, Tkinter

## Авторы
Әлихан Серікұлы, 2026
```

**Жақсы README-дің 5 белгісі:**
1. Бірінші сөйлемнен-ақ жоба не істейтіні түсінікті.
2. Скриншот бар.
3. Іске қосу нұсқаулығы нақты (командамен).
4. Мүмкіндіктер тізім түрінде.
5. Автор мен технологиялар көрсетілген.

## 3. Скриншот жасау

| ОЖ | Тәсіл |
|---|---|
| Windows | `Win + Shift + S` → аймақты таңдау → Paint-ке қойып, PNG сақтау |
| macOS | `Cmd + Shift + 4` → аймақты таңдау |
| Linux | `PrtSc` немесе «Скриншот» қолданбасы |

Скриншотты `screenshots/` папкасына қойып, README-ге `![сипаттама](жол)` арқылы қосамыз.

## 4. Мағыналы commit хабарламалары

| Нашар | Жақсы |
|---|---|
| `asdf` | `Батырмалар торын grid арқылы құру` |
| `fix` | `Нөлге бөлу қатесін өңдеу` |
| `update` | `Тапсырмаларды файлға сақтау функциясын қосу` |
| `final final 2` | `README мен скриншот қосу` |

Ереже: commit хабарламасы «осы commit не өзгертті?» деген сұраққа қысқа жауап береді.

## 5. GitHub-қа жүктеу: командалар реті

Жергілікті жоба дайын, GitHub-та бос `todo-app` репозиторийі құрылған жағдай:

```bash
cd todo-app
git status
git add .
git commit -m "README, скриншот және .gitignore қосу"
git branch -M main
git remote add origin https://github.com/username/todo-app.git
git push -u origin main
```

Репозиторий бұрыннан байланысқан болса, тек:

```bash
git add .
git commit -m "Өңдеу функциясын қосу"
git push
```

**Тексеру:** браузерде репозиторийді ашып, README дұрыс көрсетілгенін, скриншот ашылатынын, `tasks.txt` және `__pycache__` жоқ екенін көз жеткізу.

## 6. Жарияламас бұрын жобаны тексеру

**Таза көшірмеден іске қосу.** Жоба тек сіздің компьютеріңізде емес, кез келген жерде жұмыс істеуі керек. Репозиторийді басқа папкаға клондап, сол жерден іске қосып көріңіз:

```bash
git clone https://github.com/username/todo-app.git test-copy
cd test-copy
python todo_app.py
```

Бағдарлама ашылмаса — қандай да бір файл push жасалмаған немесе `.gitignore`-ға қате қосылған.

**Файлдарға салыстырмалы жол.** Деректер файлын «ағымдағы папкадан» емес, скрипт жатқан папкадан іздеген дұрыс — сонда бағдарлама қай папкадан іске қосылса да жұмыс істейді:

```python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent
DATA_FILE = BASE_DIR / "tasks.txt"

if DATA_FILE.exists():
    print(DATA_FILE.read_text(encoding="utf-8"))
```

**Тәуелділіктер тізімі (`requirements.txt`).** Tkinter — стандартты кітапхана, оны орнатудың қажеті жоқ. Ал жобада сыртқы кітапханалар (`pip install ...`) қолданылса, олардың тізімін файлға сақтаймыз:

```bash
pip freeze > requirements.txt     # тізімді жасау
pip install -r requirements.txt   # басқа компьютерде орнату
```

## 7. Tkinter бойынша шпаргалка

```python
import tkinter as tk
from tkinter import ttk, messagebox

root = tk.Tk()
root.title("Шпаргалка")
root.geometry("300x200")

name_var = tk.StringVar()
tk.Label(root, text="Атыңыз:").grid(row=0, column=0, padx=5, pady=5)
tk.Entry(root, textvariable=name_var).grid(row=0, column=1)
grade = ttk.Combobox(root, values=["7", "8", "9", "10"], state="readonly")
grade.grid(row=1, column=1)
agree = tk.BooleanVar()
tk.Checkbutton(root, text="Келісемін", variable=agree).grid(row=2, column=1)


def greet():
    if not name_var.get().strip():
        messagebox.showwarning("Ескерту", "Атыңызды жазыңыз")
        return
    messagebox.showinfo("Сәлем", f"Сәлем, {name_var.get()}! Сынып: {grade.get()}")


tk.Button(root, text="Жіберу", command=greet).grid(row=3, column=1, pady=10)
root.bind("<Return>", lambda event: greet())
root.mainloop()
```

## Жиі кездесетін қателер

**1. `git push` кезінде «rejected»** — GitHub-тағы репозиторийде README жасалып қойылған, ал жергілікті тарихта ол жоқ. Шешімі:
```bash
git pull origin main --rebase
git push
```

**2. `venv/` немесе `__pycache__/` репозиторийге түсіп кеткен.** `.gitignore`-ды commit-тен **бұрын** жасау керек. Түсіп кетсе:
```bash
git rm -r --cached __pycache__
git commit -m "__pycache__ жою"
```

**3. README-дегі сурет ашылмайды** — жол қате немесе файл push жасалмаған. Жол регистрді ажыратады: `Screenshots/Main.PNG` ≠ `screenshots/main.png`.

**4. Басқа компьютерде бағдарлама ашылмайды.** Репозиторийді басқа папкаға `git clone` жасап, сол жерден іске қосып көріңіз — барлық файл бар ма, жолдар салыстырмалы ма?

**5. Код бар, бірақ терезе ашылмайды** — `root.mainloop()` ұмытылған.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Портфолио | Үздік жұмыстардың жинағы (GitHub профилі) |
| README.md | Жобаның сипаттамасы, репозиторийдің басты бетінде көрінеді |
| `.gitignore` | Git бақыламайтын файлдар тізімі |
| Commit хабарламасы | Өзгерістің қысқа сипаттамасы |
| `remote` / `origin` | Сыртқы (GitHub-тағы) репозиторий және оның әдепкі атауы |
| `git clone` | Репозиторийдің толық көшірмесін жүктеу |
| `requirements.txt` | Жобаға қажетті сыртқы кітапханалар тізімі |

## Ескерту

- `.gitignore` бұрыннан бақыланып жүрген файлдарға әсер етпейді — оларды алдымен `git rm --cached` арқылы индекстен алып тастау керек.
- GitHub 2021 жылдан бастап HTTPS арқылы push жасағанда аккаунт құпиясөзін қабылдамайды: personal access token, SSH кілті немесе Git Credential Manager қажет.
- Жаңа репозиторийдегі әдепкі тармақ GitHub-та `main`, ал Git-тің ескі нұсқаларында `master` болуы мүмкін; `git branch -M main` атауды біркелкі етеді.
- Tkinter Python-ның стандартты кітапханасына кіреді, бірақ кейбір Linux дистрибутивтерінде бөлек орнатылады (мысалы, `sudo apt install python3-tk`).
- Markdown синтаксисі бойынша GitHub құжаттамасы: https://docs.github.com/en/get-started/writing-on-github
