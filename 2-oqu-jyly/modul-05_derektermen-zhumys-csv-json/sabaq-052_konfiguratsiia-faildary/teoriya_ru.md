# Урок 52. Файлы конфигурации. Хранение параметров программы.

## Темы

1. Что такое конфигурация?
2. Форматы конфигурации
3. Формат INI и `configparser`
4. Настройки в JSON и значения по умолчанию
5. Класс `Settings`
6. TOML — современный формат настроек
7. Переменные окружения и аргументы командной строки
8. Безопасность файлов настроек
9. Частые ошибки
10. Основные понятия

## 1. Что такое конфигурация?

**Конфигурация (настройки, config)** — набор параметров, управляющих поведением программы без изменения её кода.

Примеры: размер окна, язык, цветовая тема (светлая/тёмная), громкость, имя файла базы данных, адрес сервера, интервал автосохранения.

**Аналогия:** чтобы удобно сидеть в машине, ты регулируешь сиденье и зеркала. Машина (код) не меняется, меняются только настройки. Современные автомобили даже «запоминают» настройки каждого водителя — это и есть файл конфигурации.

### Недостатки «жёсткого прописывания» в коде (hardcode)

```python
# Плохо: значения «жёстко прописаны» в коде
WIDTH = 800
LANGUAGE = "kk"
VOLUME = 0.7
```

- чтобы изменить, нужно открыть код (пользователь этого делать не будет);
- при закрытии программы изменения теряются;
- трудно запускать одну программу в разных средах (школьный компьютер, дом) с разными настройками.

Решение: хранить параметры **в отдельном файле** и читать их при запуске программы.

## 2. Форматы конфигурации

| Формат | Расширение | Пример | Особенность | В Python |
|---|---|---|---|---|
| INI | `.ini`, `.cfg` | `volume = 70` | разделы, простой, есть комментарии | `configparser` |
| JSON | `.json` | `{"volume": 70}` | типы, вложенность, нет комментариев | `json` |
| TOML | `.toml` | `volume = 70` | как INI, но с типами | `tomllib` (3.11+, только чтение) |
| YAML | `.yaml` | `volume: 70` | очень читаемый, чувствителен к отступам | `pyyaml` (внешняя библиотека) |
| .env | `.env` | `API_KEY=...` | секретные данные и переменные окружения | `os.environ`, `python-dotenv` |

## 3. Формат INI и `configparser`

INI-файл состоит из **разделов** (`[section]`) и строк «ключ = значение». `#` или `;` — комментарий.

```text
; settings.ini — настройки игры «Қыран»
[window]
width = 800
height = 600
fullscreen = no

[sound]
music = yes
volume = 70

[game]
language = kk
difficulty = normal
player_name = Алия
```

### Запись INI-файла

```python
import configparser

config = configparser.ConfigParser()
config["window"] = {"width": "800", "height": "600", "fullscreen": "no"}
config["sound"] = {"music": "yes", "volume": "70"}
config["game"] = {"language": "kk", "difficulty": "normal", "player_name": "Алия"}

with open("settings.ini", "w", encoding="utf-8") as f:
    config.write(f)

with open("settings.ini", encoding="utf-8") as f:
    print(f.read())
```

Результат:

```text
[window]
width = 800
height = 600
fullscreen = no

[sound]
music = yes
volume = 70

[game]
language = kk
difficulty = normal
player_name = Алия

```

### Чтение INI-файла

`configparser` хранит всё как **строки**, но у него есть специальные методы для преобразования в нужный тип:

```python
import configparser

config = configparser.ConfigParser()
config.read("settings.ini", encoding="utf-8")

print("Разделы:", config.sections())
width = config.getint("window", "width")
fullscreen = config.getboolean("window", "fullscreen")
volume = config.getint("sound", "volume") / 100
name = config["game"]["player_name"]
theme = config.get("game", "theme", fallback="light")  # нет ключа → значение по умолчанию

print(width, type(width))
print(fullscreen, volume, name, theme)
```

Результат:

```text
Разделы: ['window', 'sound', 'game']
800 <class 'int'>
False 0.7 Алия light
```

| Метод | Результат |
|---|---|
| `config.get(sec, key)` | строка |
| `config.getint(sec, key)` | целое число |
| `config.getfloat(sec, key)` | дробное число |
| `config.getboolean(sec, key)` | `True`/`False` (понимает `yes/no`, `on/off`, `true/false`, `1/0`) |
| `fallback=...` | значение, возвращаемое, если нет ключа или раздела |
| `config.has_section(sec)`, `config.has_option(sec, key)` | проверка наличия |

### Изменение значения и сохранение

```python
import configparser

config = configparser.ConfigParser()
config.read("settings.ini", encoding="utf-8")

config["sound"]["volume"] = "40"          # только строка!
config.set("game", "difficulty", "hard")
if not config.has_section("stats"):
    config.add_section("stats")
config["stats"]["best_score"] = str(1450)

with open("settings.ini", "w", encoding="utf-8") as f:
    config.write(f)
print("Сохранено:", dict(config["sound"]), dict(config["stats"]))
```

Результат:

```text
Сохранено: {'music': 'yes', 'volume': '40'} {'best_score': '1450'}
```

> **Обрати внимание:** если файла нет, `config.read()` **не выдаёт ошибку**, а просто даёт пустую конфигурацию. Поэтому значения `fallback` или настройки по умолчанию обязательны.

> Ключи нечувствительны к регистру: `Volume` и `volume` — один ключ (по умолчанию переводятся в нижний регистр).

## 4. Настройки в JSON и значения по умолчанию

Преимущество JSON — сохраняются типы (`int`, `bool`, список), есть вложенная структура. Самый важный приём — **настройки по умолчанию + настройки пользователя**:

```python
import json

DEFAULTS = {
    "language": "kk",
    "theme": "light",
    "volume": 70,
    "window": {"width": 800, "height": 600},
    "recent_files": [],
}


def load_settings(path="config.json"):
    """Берёт значения по умолчанию и записывает поверх них значения из файла."""
    settings = json.loads(json.dumps(DEFAULTS))  # глубокая копия
    try:
        with open(path, encoding="utf-8") as f:
            user = json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return settings
    for key, value in user.items():
        if key in settings and isinstance(settings[key], dict) and isinstance(value, dict):
            settings[key].update(value)       # объединяем вложенный словарь
        elif key in settings:
            settings[key] = value             # неизвестные ключи игнорируем
    return settings


def save_settings(settings, path="config.json"):
    with open(path, "w", encoding="utf-8") as f:
        json.dump(settings, f, ensure_ascii=False, indent=2)


# Файл, в котором пользователь изменил только два значения
with open("config.json", "w", encoding="utf-8") as f:
    json.dump({"theme": "dark", "window": {"width": 1024}}, f)

s = load_settings()
print(s["theme"], s["language"], s["window"])
```

Результат:

```text
dark kk {'width': 1024, 'height': 600}
```

Почему это важно:
- если файла нет — программа работает со значениями по умолчанию;
- если в новой версии программы добавлен новый параметр, а в старом файле его нет, — берётся значение по умолчанию;
- если файл повреждён — программа не падает.

## 5. Класс `Settings`

Обернуть настройки в класс — удобно и соответствует ООП:

```python
import json
from pathlib import Path


class Settings:
    """Настройки приложения: загрузка из JSON-файла, проверка, сохранение."""

    DEFAULTS = {"language": "kk", "theme": "light", "volume": 70, "font_size": 12}
    ALLOWED = {"language": {"kk", "ru", "en"}, "theme": {"light", "dark"}}

    def __init__(self, path="config.json"):
        self.path = Path(path)
        self.data = dict(self.DEFAULTS)
        self.load()

    def load(self):
        if self.path.exists():
            try:
                loaded = json.loads(self.path.read_text(encoding="utf-8"))
                for key, value in loaded.items():
                    if key in self.DEFAULTS:
                        self.set(key, value, save=False)
            except json.JSONDecodeError:
                print("Файл настроек повреждён, используются значения по умолчанию")

    def get(self, key):
        return self.data[key]

    def set(self, key, value, save=True):
        """Проверяет и устанавливает значение; при неверном значении — ValueError."""
        if key not in self.DEFAULTS:
            raise KeyError(f"Неизвестная настройка: {key}")
        if key in self.ALLOWED and value not in self.ALLOWED[key]:
            raise ValueError(f"Недопустимое значение для {key}: {value}")
        if key == "volume" and not 0 <= int(value) <= 100:
            raise ValueError("Громкость должна быть в диапазоне 0..100")
        self.data[key] = value
        if save:
            self.save()

    def save(self):
        self.path.write_text(json.dumps(self.data, ensure_ascii=False, indent=2), encoding="utf-8")


settings = Settings()
settings.set("theme", "dark")
settings.set("volume", 35)
try:
    settings.set("language", "fr")
except ValueError as e:
    print("Ошибка:", e)
print(Settings().data)   # новый объект — заново загружается из файла
```

Результат (при первом запуске в пустой папке):

```text
Ошибка: Недопустимое значение для language: fr
{'language': 'kk', 'theme': 'dark', 'volume': 35, 'font_size': 12}
```

### Использование в приложении Tkinter (идея)

```python
import tkinter as tk

THEMES = {"light": ("white", "black"), "dark": ("#222222", "#eeeeee")}
theme = "dark"  # в реальном проекте: settings.get("theme")

root = tk.Tk()
root.title("Пример настроек")
bg, fg = THEMES[theme]
root.configure(bg=bg)
tk.Label(root, text="Привет! Тема взята из настроек.", bg=bg, fg=fg, font=("Arial", 14)).pack(padx=20, pady=20)
root.mainloop()
```

## 6. TOML — современный формат настроек

TOML похож на INI, но у значений есть типы (строки в кавычках, числа, `true/false`, списки). Начиная с Python 3.11 стандартный модуль `tomllib` умеет **читать** TOML (для записи нужна внешняя библиотека). Файл `pyproject.toml` проектов Python пишется в этом формате.

```text
# config.toml
title = "Қыран"

[window]
width = 800
fullscreen = false

[sound]
volume = 70
tracks = ["menu.ogg", "level1.ogg"]
```

```python
import sys

if sys.version_info >= (3, 11):
    import tomllib

    text = 'title = "Қыран"\n[window]\nwidth = 800\nfullscreen = false\n'
    config = tomllib.loads(text)
    print(config["title"], config["window"]["width"], config["window"]["fullscreen"])
else:
    print("tomllib есть только в Python 3.11+")
```

Результат (Python 3.11+):

```text
Қыран 800 False
```

## 7. Переменные окружения и аргументы командной строки

Настройки можно брать не только из файла, но и из **переменных окружения операционной системы** (environment variables). Это особенно применяется на серверах и для секретных данных.

```python
import os

# Если переменная окружения есть — берём её, если нет — значение по умолчанию
db_name = os.environ.get("APP_DB", "school.db")
debug = os.environ.get("APP_DEBUG", "0") == "1"
print("База данных:", db_name, "| Debug:", debug)
```

Результат (если переменные окружения не установлены):

```text
База данных: school.db | Debug: False
```

Установка в терминале:

```bash
# Linux / macOS
APP_DEBUG=1 python main.py
# Windows PowerShell
$env:APP_DEBUG = "1"; python main.py
```

Аргументы командной строки — передача параметров при запуске программы: `python main.py --level 3`. Самый простой вариант — список `sys.argv`; профессиональный — модуль `argparse`.

```python
import sys

print("Аргументы:", sys.argv[1:])
level = int(sys.argv[1]) if len(sys.argv) > 1 else 1
print("Начальный уровень:", level)
```

### Приоритет источников настроек

Обычно программа просматривает настройки в таком порядке (верхний имеет приоритет):

```text
1. Аргумент командной строки        (python main.py --volume 30)
2. Переменная окружения             (APP_VOLUME=30)
3. Файл config пользователя         (config.json)
4. Значения по умолчанию в коде     (DEFAULTS)
```

## 8. Безопасность файлов настроек

- **Секреты** (API-ключи, пароли) хранятся не в `config.json`, а в переменных окружения или в файле `.env`, а `.env` записывается в `.gitignore`.
- В репозиторий загружаем файл-**шаблон** (`config.example.json`), а настоящий `config.json` добавляем в `.gitignore`.
- Настройки пользователя **проверяем** (валидация): `volume = 500` или `language = "xyz"` не должны ломать программу.
- Настройки пользователя правильно хранить не в папке программы, а в домашней папке: `Path.home() / ".qyran" / "config.json"`.

## Частые ошибки

**1. Запись числа в `configparser`.**

```python
import configparser

config = configparser.ConfigParser()
config["sound"] = {}
# config["sound"]["volume"] = 70   # TypeError: option values must be strings
config["sound"]["volume"] = str(70)  # правильно
print(config["sound"]["volume"])
```

**2. Использование `get` вместо `getboolean`.**

```python
import configparser

config = configparser.ConfigParser()
config.read_string("[window]\nfullscreen = no\n")
if config.get("window", "fullscreen"):         # "no" — непустая строка, то есть True!
    print("Ошибка: полноэкранный режим включился")
if not config.getboolean("window", "fullscreen"):
    print("Правильно: полноэкранный режим выключен")
```

Результат:

```text
Ошибка: полноэкранный режим включился
Правильно: полноэкранный режим выключен
```

**3. Падение при отсутствии файла.** `json.load` → `FileNotFoundError`. Правильно: значения по умолчанию + `try/except`.

**4. Прямое изменение словаря `DEFAULTS`.** `settings = DEFAULTS; settings["volume"] = 10` — теперь изменился и `DEFAULTS` (две ссылки на один объект). Правильно: `dict(DEFAULTS)` или глубокая копия.

**5. Запись пароля в файл настроек и загрузка на GitHub.** Правильно: переменные окружения / `.env` + `.gitignore`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Конфигурация (настройки) | Параметры, управляющие программой без изменения кода |
| Hardcode | Запись значения прямо внутри кода |
| INI | Формат из разделов и строк «ключ = значение» |
| `configparser` | Стандартный модуль для чтения/записи INI-файлов |
| Значение по умолчанию (default) | Значение, используемое, если настройка не задана |
| `fallback` | Значение по умолчанию для отсутствующего ключа в `configparser` |
| TOML | Современный формат настроек с типами (`tomllib`) |
| Переменная окружения | Именованное значение, устанавливаемое на уровне ОС (`os.environ`) |
| Валидация | Проверка правильности введённого значения |

## Примечание

- У `configparser` по умолчанию включена **интерполяция**: `%` в значении считается началом подстановки (`%(name)s`), поэтому строка `progress = 50%` вызовет `InterpolationSyntaxError`. Решения: писать `%%` или создавать `configparser.ConfigParser(interpolation=None)`.
- Раздел `[DEFAULT]` в INI особый: его ключи доступны во всех остальных разделах как значения по умолчанию.
- `configparser` при записи не сохраняет комментарии исходного файла — после `config.write()` они пропадут.
- `tomllib.load()` требует файл, открытый в **двоичном** режиме: `with open("config.toml", "rb") as f: tomllib.load(f)`.
- Для разбора аргументов командной строки вместо ручной работы с `sys.argv` используйте `argparse`: он сам проверяет типы (`type=int`), выводит справку `--help` и сообщения об ошибках.
- Документация: https://docs.python.org/3/library/configparser.html, https://docs.python.org/3/library/tomllib.html, https://docs.python.org/3/library/argparse.html
