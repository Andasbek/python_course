# Сабақ 68. Практикалық тапсырмалар: Кілттерді қауіпсіз сақтау және .gitignore

> **Маңызды:** тапсырмаларда нақты кілт қолданбаймыз — жалған (ойдан шығарылған) мәндермен жұмыс істейміз, мысалы `test_key_12345`.

## Базалық деңгей

### 1-тапсырма. «Қатені тап»
Төмендегі жобада қауіпсіздік тұрғысынан кемінде 5 қате бар. Оларды тауып, әрқайсысын қалай түзету керектігін дәптерге жазыңыз.

```text
weather_bot/
├── bot.py            ← TOKEN = "123456:ABC..." деп жазылған
├── config.json       ← {"api_key": "3f9a7c...", "city": "Almaty"}
├── users.db          ← пайдаланушылардың телефондары
├── .venv/
├── README.md         ← «Менің кілтім: 3f9a7c..., өзіңіз қолдана беріңіз»
└── (.gitignore жоқ)
Соңғы коммит: "кілтті өшірдім"
```

### 2-тапсырма. Орта айнымалысы
1. Терминалда `CLUB_NAME` орта айнымалысын орнатыңыз (мәні — «Python үйірмесі»).
2. Python бағдарламасы оны `os.getenv()` арқылы оқып, сәлемдессін. Айнымалы жоқ болса — «Үйірме атауы көрсетілмеген» деп шығарсын.
3. Терминалды жауып, қайта ашып тексеріңіз — `export` / `$env:` арқылы орнатылған айнымалы сақталды ма? Неге?

```text
Сәлем! Сіз «Python үйірмесі» бағдарламасын іске қостыңыз.
```

### 3-тапсырма. `.env` + `python-dotenv`
1. `pip install python-dotenv`.
2. `.env` файлын жасаңыз: `WEATHER_API_KEY=test_key_12345`, `CITY=Almaty`, `UNITS=metric`.
3. `config.py` модулі осы мәндерді оқып, `main.py` оларды пайдалансын. Кілтті экранға толық шығармаңыз — тек маскаланған түрде.

```text
Қала: Almaty
Өлшем бірлігі: metric
Кілт: tes***********
```

### 4-тапсырма. `.gitignore` жазу
Жобаңыз үшін толық `.gitignore` жазыңыз (құпиялар, Python, venv, деректер қоры, редакторлар). `git init`, сосын `git status` орындап, `.env`, `.venv/`, `*.db` тізімде жоқ екенін, ал `.env.example` бар екенін тексеріңіз. `git check-ignore -v .env` нәтижесін жазып алыңыз.

### 5-тапсырма. `.env.example` және README
`.env.example` файлын жасап, README.md-ге «Іске қосу» бөлімін жазыңыз:
```text
1. pip install -r requirements.txt
2. .env.example файлын .env деп көшіріңіз
3. .env ішіне өз кілтіңізді жазыңыз
4. python main.py
```

## Кеңейтілген деңгей

### 6-тапсырма. Өз `.env` оқырманыңыз
Кітапханасыз `load_env(path=".env")` функциясын жазыңыз: файлды жолма-жол оқиды, бос жолдар мен `#` түсініктемелерін өткізеді, `КІЛТ=МӘН` жұбын бөледі (мәндегі `=` белгісін сақтайды), тырнақшаларды алып тастайды және `os.environ`-ға жазады (бар айнымалыны қайта жазбайды). Қайтаратыны — жүктелген айнымалылар саны.

### 7-тапсырма. Міндетті айнымалылар
`require(*names)` функциясы барлық аталған орта айнымалыларын тексеріп, жоқтарының **барлығын** бір хабарда шығарсын және бағдарламаны тоқтатсын (`SystemExit`).

```text
Қате: келесі айнымалылар орнатылмаған: WEATHER_API_KEY, TELEGRAM_TOKEN
.env.example файлын қараңыз.
```

### 8-тапсырма. `.gitignore` үлгілерін тексеру
`fnmatch` модулі арқылы қарапайым тексергіш жазыңыз: үлгілер тізімі және файлдар тізімі берілген, әр файл үшін «елемейді / қосылады» деп шығарсын. `!` ерекшелігін ескеріңіз (соңғы сәйкес келген ереже жеңеді).

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Құпия іздегіш (secret scanner)
Жоба папкасындағы барлық `.py`, `.json`, `.txt`, `.md` файлдарын (`pathlib.Path.rglob`) ақтарып, күдікті жолдарды табатын бағдарлама жазыңыз (`re` модулі):
- `api_key`, `token`, `password`, `secret` сөздерінен кейін `=` немесе `:` және 8+ таңбалы мән;
- 32 таңбалы он алтылық (hex) жолдар.

Нәтиже: файл, жол нөмірі, маскаланған мән. `.venv` папкасын өткізіп жіберіңіз.

```text
⚠ config.json:2  api_key = 3f9***
⚠ bot.py:5       TOKEN = 123***
Барлығы: 2 күдікті жол
```

### 10-тапсырма. Pre-commit ескерту
9-тапсырманы Git «ілмегі» (hook) ретінде қосып көріңіз: `.git/hooks/pre-commit` файлы скриптті іске қосады, күдікті жол табылса, коммитті тоқтатады (шығу коды 1). Нәтижесін сыныпқа көрсетіңіз.

## Үй тапсырмасы

1. Осы уақытқа дейінгі барлық GitHub репозиторийлеріңізді тексеріңіз: `.gitignore` бар ма, құпия немесе `.db` файлдар жоқ па? Табылса — түзетіңіз (кілт болса — ауыстырыңыз!).
2. «Ақпараттық қауіпсіздік ережелері» плакатын (5–7 ереже, қазақша) жасаңыз.
3. Келесі сабаққа: кез келген сайтта тышқанның оң батырмасы → «Просмотр кода страницы» (View Page Source) ашып, қандай тегтерді танитыныңызды жазыңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма — жауабы**
1. `bot.py` ішінде токен → `.env` + `os.getenv("TELEGRAM_TOKEN")`.
2. `config.json` ішінде кілт → кілтті `.env`-ке, `config.json`-да тек жария баптаулар (қала).
3. `users.db` репозиторийде → жеке деректер; `*.db` → `.gitignore`.
4. README-де кілт → өшіру, кілтті ауыстыру, README-де тек нұсқау.
5. `.gitignore` жоқ → `.env`, `.venv/`, `*.db`, `__pycache__/` қосу.
6. «Кілтті өшірдім» коммиті → кілт тарихта қалды: кілтті сервисте жою/ауыстыру міндетті.
7. `.venv/` репозиторийге түсуі мүмкін → `.gitignore` + `requirements.txt`.

**2-тапсырма**
```python
import os

name = os.getenv("CLUB_NAME")
if name:
    print(f"Сәлем! Сіз «{name}» бағдарламасын іске қостыңыз.")
else:
    print("Үйірме атауы көрсетілмеген")
```
3-сұраққа жауап: `export` / `$env:` тек ағымдағы терминал сеансына әсер етеді; тұрақты болуы үшін Windows-та `setx`, Linux/macOS-та `~/.bashrc` / `~/.zshrc`.

**3-тапсырма**
```python
# config.py
import os
from dotenv import load_dotenv

load_dotenv()
WEATHER_API_KEY = os.getenv("WEATHER_API_KEY", "")
CITY = os.getenv("CITY", "Almaty")
UNITS = os.getenv("UNITS", "metric")


def mask(secret, visible=3):
    if not secret:
        return "(жоқ)"
    return secret[:visible] + "*" * (len(secret) - visible)
```
```python
# main.py
# import config
# print("Қала:", config.CITY)
# print("Өлшем бірлігі:", config.UNITS)
# print("Кілт:", config.mask(config.WEATHER_API_KEY))
```

**4-тапсырма — `.gitignore` үлгісі**
```text
.env
*.key
__pycache__/
*.pyc
.venv/
venv/
*.db
*.sqlite3
backups/
.vscode/
.idea/
.DS_Store
!.env.example
```

**6-тапсырма**
```python
import os

def load_env(path=".env"):
    loaded = 0
    try:
        with open(path, encoding="utf-8") as f:
            for line in f:
                line = line.strip()
                if not line or line.startswith("#") or "=" not in line:
                    continue
                key, value = line.split("=", 1)
                key, value = key.strip(), value.strip().strip('"').strip("'")
                if key and key not in os.environ:
                    os.environ[key] = value
                    loaded += 1
    except FileNotFoundError:
        print(f"{path} файлы табылмады")
    return loaded

print("Жүктелді:", load_env())
```

**7-тапсырма**
```python
import os

def require(*names):
    missing = [n for n in names if not os.getenv(n)]
    if missing:
        raise SystemExit("Қате: келесі айнымалылар орнатылмаған: "
                         + ", ".join(missing) + "\n.env.example файлын қараңыз.")

require("WEATHER_API_KEY", "TELEGRAM_TOKEN")
print("Барлық айнымалы орнатылған")
```

**8-тапсырма**
```python
from fnmatch import fnmatch

def is_ignored(path, patterns):
    ignored = False
    name = path.rstrip("/").split("/")[-1]
    for p in patterns:
        negate = p.startswith("!")
        rule = p[1:] if negate else p
        rule = rule.rstrip("/")
        parts = path.split("/")
        if fnmatch(name, rule) or any(fnmatch(part, rule) for part in parts):
            ignored = not negate
    return ignored

patterns = [".env*", "*.db", ".venv/", "__pycache__/", "!.env.example"]
files = [".env", ".env.example", "students.db", "main.py", ".venv/lib/x.py", "src/__pycache__/a.pyc"]
for f in files:
    print(f"{f:25} {'елемейді' if is_ignored(f, patterns) else 'қосылады'}")
```

**9-тапсырма**
```python
import re
from pathlib import Path

PATTERNS = [
    re.compile(r"(api_?key|token|password|secret)\s*[=:]\s*['\"]?([A-Za-z0-9:_\-]{8,})", re.I),
    re.compile(r"()\b([0-9a-f]{32})\b"),
]
SKIP_DIRS = {".venv", "venv", ".git", "__pycache__"}

def scan(root="."):
    found = 0
    for path in Path(root).rglob("*"):
        if path.suffix not in {".py", ".json", ".txt", ".md"} or SKIP_DIRS & set(path.parts):
            continue
        try:
            lines = path.read_text(encoding="utf-8").splitlines()
        except (UnicodeDecodeError, OSError):
            continue
        for no, line in enumerate(lines, 1):
            for pat in PATTERNS:
                m = pat.search(line)
                if m:
                    label = m.group(1) or "hex"
                    print(f"⚠ {path}:{no}  {label} = {m.group(2)[:3]}***")
                    found += 1
                    break
    print("Барлығы:", found, "күдікті жол")
    return found

if __name__ == "__main__":
    raise SystemExit(1 if scan() else 0)
```

**10-тапсырма** — `.git/hooks/pre-commit` (Linux/macOS, `chmod +x`):
```bash
#!/bin/sh
python3 secret_scanner.py || { echo "Коммит тоқтатылды: құпия табылды"; exit 1; }
```

</details>
