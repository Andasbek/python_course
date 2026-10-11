# Урок 68. Практические задания: безопасное хранение ключей и .gitignore

> **Важно:** в заданиях реальные ключи не используем — работаем с фиктивными (придуманными) значениями, например `test_key_12345`.

## Базовый уровень

### Задание 1. «Найди ошибку»
В проекте ниже не менее 5 ошибок с точки зрения безопасности. Найдите их и запишите в тетрадь, как исправить каждую.

```text
weather_bot/
├── bot.py            ← записано TOKEN = "123456:ABC..."
├── config.json       ← {"api_key": "3f9a7c...", "city": "Almaty"}
├── users.db          ← телефоны пользователей
├── .venv/
├── README.md         ← «Мой ключ: 3f9a7c..., пользуйтесь на здоровье»
└── (.gitignore нет)
Последний коммит: "удалил ключ"
```

### Задание 2. Переменная окружения
1. Установите в терминале переменную окружения `CLUB_NAME` (значение — «Кружок Python»).
2. Программа на Python должна прочитать её через `os.getenv()` и поздороваться. Если переменной нет — вывести «Название кружка не указано».
3. Закройте и снова откройте терминал и проверьте — сохранилась ли переменная, установленная через `export` / `$env:`? Почему?

```text
Привет! Вы запустили программу «Кружок Python».
```

### Задание 3. `.env` + `python-dotenv`
1. `pip install python-dotenv`.
2. Создайте файл `.env`: `WEATHER_API_KEY=test_key_12345`, `CITY=Almaty`, `UNITS=metric`.
3. Модуль `config.py` должен читать эти значения, а `main.py` — использовать их. Не выводите ключ на экран полностью — только в замаскированном виде.

```text
Город: Almaty
Единицы измерения: metric
Ключ: tes***********
```

### Задание 4. Написание `.gitignore`
Напишите полный `.gitignore` для своего проекта (секреты, Python, venv, база данных, редакторы). Выполните `git init`, затем `git status` и убедитесь, что `.env`, `.venv/`, `*.db` в списке нет, а `.env.example` есть. Запишите результат `git check-ignore -v .env`.

### Задание 5. `.env.example` и README
Создайте файл `.env.example` и напишите в README.md раздел «Запуск»:
```text
1. pip install -r requirements.txt
2. Скопируйте файл .env.example как .env
3. Впишите в .env свой ключ
4. python main.py
```

## Продвинутый уровень

### Задание 6. Собственный читатель `.env`
Напишите без библиотек функцию `load_env(path=".env")`: читает файл построчно, пропускает пустые строки и комментарии `#`, разделяет пару `КЛЮЧ=ЗНАЧЕНИЕ` (сохраняя знак `=` в значении), удаляет кавычки и записывает в `os.environ` (существующую переменную не перезаписывает). Возвращает число загруженных переменных.

### Задание 7. Обязательные переменные
Функция `require(*names)` должна проверить все названные переменные окружения, вывести **все** отсутствующие в одном сообщении и остановить программу (`SystemExit`).

```text
Ошибка: не установлены следующие переменные: WEATHER_API_KEY, TELEGRAM_TOKEN
Смотрите файл .env.example.
```

### Задание 8. Проверка шаблонов `.gitignore`
С помощью модуля `fnmatch` напишите простой проверяльщик: дан список шаблонов и список файлов, для каждого файла вывести «игнорируется / добавляется». Учтите исключение `!` (побеждает последнее совпавшее правило).

## ★ Олимпиадная / творческая задача

### Задание 9. Поисковик секретов (secret scanner)
Напишите программу, которая просматривает все файлы `.py`, `.json`, `.txt`, `.md` в папке проекта (`pathlib.Path.rglob`) и находит подозрительные строки (модуль `re`):
- после слов `api_key`, `token`, `password`, `secret` идёт `=` или `:` и значение из 8+ символов;
- шестнадцатеричные (hex) строки из 32 символов.

Результат: файл, номер строки, замаскированное значение. Папку `.venv` пропускайте.

```text
⚠ config.json:2  api_key = 3f9***
⚠ bot.py:5       TOKEN = 123***
Всего подозрительных строк: 2
```

### Задание 10. Предупреждение pre-commit
Попробуйте подключить задание 9 как «хук» (hook) Git: файл `.git/hooks/pre-commit` запускает скрипт и, если найдена подозрительная строка, останавливает коммит (код выхода 1). Покажите результат классу.

## Домашнее задание

1. Проверьте все свои репозитории на GitHub на сегодняшний день: есть ли `.gitignore`, нет ли секретов или файлов `.db`? Если найдутся — исправьте (если это ключ — замените его!).
2. Сделайте плакат «Правила информационной безопасности» (5–7 правил, на русском языке).
3. К следующему уроку: на любом сайте щёлкните правой кнопкой мыши → «Просмотр кода страницы» (View Page Source) и запишите, какие теги вы узнаёте.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1 — ответ**
1. Токен внутри `bot.py` → `.env` + `os.getenv("TELEGRAM_TOKEN")`.
2. Ключ внутри `config.json` → ключ в `.env`, в `config.json` только публичные настройки (город).
3. `users.db` в репозитории → личные данные; `*.db` → в `.gitignore`.
4. Ключ в README → удалить, заменить ключ, в README только инструкция.
5. Нет `.gitignore` → добавить `.env`, `.venv/`, `*.db`, `__pycache__/`.
6. Коммит «удалил ключ» → ключ остался в истории: обязательно удалить/заменить ключ в сервисе.
7. `.venv/` может попасть в репозиторий → `.gitignore` + `requirements.txt`.

**Задание 2**
```python
import os

name = os.getenv("CLUB_NAME")
if name:
    print(f"Привет! Вы запустили программу «{name}».")
else:
    print("Название кружка не указано")
```
Ответ на вопрос 3: `export` / `$env:` действуют только на текущий сеанс терминала; чтобы сделать переменную постоянной, в Windows — `setx`, в Linux/macOS — `~/.bashrc` / `~/.zshrc`.

**Задание 3**
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
        return "(нет)"
    return secret[:visible] + "*" * (len(secret) - visible)
```
```python
# main.py
# import config
# print("Город:", config.CITY)
# print("Единицы измерения:", config.UNITS)
# print("Ключ:", config.mask(config.WEATHER_API_KEY))
```

**Задание 4 — образец `.gitignore`**
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

**Задание 6**
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
        print(f"Файл {path} не найден")
    return loaded

print("Загружено:", load_env())
```

**Задание 7**
```python
import os

def require(*names):
    missing = [n for n in names if not os.getenv(n)]
    if missing:
        raise SystemExit("Ошибка: не установлены следующие переменные: "
                         + ", ".join(missing) + "\nСмотрите файл .env.example.")

require("WEATHER_API_KEY", "TELEGRAM_TOKEN")
print("Все переменные установлены")
```

**Задание 8**
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
    print(f"{f:25} {'игнорируется' if is_ignored(f, patterns) else 'добавляется'}")
```

**Задание 9**
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
    print("Всего подозрительных строк:", found)
    return found

if __name__ == "__main__":
    raise SystemExit(1 if scan() else 0)
```

**Задание 10** — `.git/hooks/pre-commit` (Linux/macOS, `chmod +x`):
```bash
#!/bin/sh
python3 secret_scanner.py || { echo "Коммит остановлен: найден секрет"; exit 1; }
```

</details>
