# Сабақ 52. Конфигурация файлдары. Бағдарлама параметрлерін сақтау.

## Тақырыптар

1. Конфигурация дегеніміз не?
2. Конфигурация форматтары
3. INI форматы және `configparser`
4. JSON баптаулар және әдепкі мәндер
5. `Settings` класы
6. TOML — заманауи баптау форматы
7. Орта айнымалылары және командалық жол аргументтері
8. Баптау файлдарының қауіпсіздігі
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Конфигурация дегеніміз не?

**Конфигурация (баптау, config)** — бағдарламаның кодын өзгертпей, оның мінез-құлқын басқаратын параметрлер жиыны.

Мысалдар: терезе өлшемі, тіл, түс тақырыбы (ашық/күңгірт), дыбыс деңгейі, деректер қорының файл аты, сервер адресі, автоматты сақтау аралығы.

**Аналогия:** көлікте ыңғайлы отыру үшін орындық пен айналарды реттейсің. Көлік (код) өзгермейді, тек баптаулары өзгереді. Заманауи көліктер тіпті әр жүргізушінің баптауларын «есте сақтайды» — бұл конфигурация файлы.

### Кодқа «қатты жазудың» (hardcode) кемшілігі

```python
# Нашар: мәндер код ішінде «қатты жазылған»
WIDTH = 800
LANGUAGE = "kk"
VOLUME = 0.7
```

- өзгерту үшін кодты ашу керек (пайдаланушы мұны істемейді);
- бағдарламаны жапқанда өзгерістер жоғалады;
- бір бағдарламаны әртүрлі ортада (мектеп компьютері, үй) әртүрлі баптаумен іске қосу қиын.

Шешім: параметрлерді **бөлек файлда** сақтап, бағдарлама басталғанда оқу.

## 2. Конфигурация форматтары

| Формат | Кеңейтімі | Мысал | Ерекшелігі | Python-да |
|---|---|---|---|---|
| INI | `.ini`, `.cfg` | `volume = 70` | бөлімдер, қарапайым, түсініктеме бар | `configparser` |
| JSON | `.json` | `{"volume": 70}` | типтер, кірістіру, түсініктеме жоқ | `json` |
| TOML | `.toml` | `volume = 70` | INI сияқты, бірақ типтері бар | `tomllib` (3.11+, тек оқу) |
| YAML | `.yaml` | `volume: 70` | өте оқылымды, шегініске сезімтал | `pyyaml` (сыртқы кітапхана) |
| .env | `.env` | `API_KEY=...` | құпия деректер мен орта айнымалылары | `os.environ`, `python-dotenv` |

## 3. INI форматы және `configparser`

INI файлы **бөлімдерден** (`[section]`) және «кілт = мән» жолдарынан тұрады. `#` немесе `;` — түсініктеме.

```text
; settings.ini — «Қыран» ойынының баптаулары
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
player_name = Әлия
```

### INI файлын жазу

```python
import configparser

config = configparser.ConfigParser()
config["window"] = {"width": "800", "height": "600", "fullscreen": "no"}
config["sound"] = {"music": "yes", "volume": "70"}
config["game"] = {"language": "kk", "difficulty": "normal", "player_name": "Әлия"}

with open("settings.ini", "w", encoding="utf-8") as f:
    config.write(f)

with open("settings.ini", encoding="utf-8") as f:
    print(f.read())
```

Нәтижесі:

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
player_name = Әлия

```

### INI файлын оқу

`configparser` бәрін **жол** ретінде сақтайды, бірақ типке айналдыратын арнайы әдістері бар:

```python
import configparser

config = configparser.ConfigParser()
config.read("settings.ini", encoding="utf-8")

print("Бөлімдер:", config.sections())
width = config.getint("window", "width")
fullscreen = config.getboolean("window", "fullscreen")
volume = config.getint("sound", "volume") / 100
name = config["game"]["player_name"]
theme = config.get("game", "theme", fallback="light")  # жоқ кілт → әдепкі мән

print(width, type(width))
print(fullscreen, volume, name, theme)
```

Нәтижесі:

```text
Бөлімдер: ['window', 'sound', 'game']
800 <class 'int'>
False 0.7 Әлия light
```

| Әдіс | Нәтижесі |
|---|---|
| `config.get(sec, key)` | жол |
| `config.getint(sec, key)` | бүтін сан |
| `config.getfloat(sec, key)` | бөлшек сан |
| `config.getboolean(sec, key)` | `True`/`False` (`yes/no`, `on/off`, `true/false`, `1/0` түсінеді) |
| `fallback=...` | кілт немесе бөлім жоқ болса қайтарылатын мән |
| `config.has_section(sec)`, `config.has_option(sec, key)` | бар-жоғын тексеру |

### Мәнді өзгерту және сақтау

```python
import configparser

config = configparser.ConfigParser()
config.read("settings.ini", encoding="utf-8")

config["sound"]["volume"] = "40"          # тек жол!
config.set("game", "difficulty", "hard")
if not config.has_section("stats"):
    config.add_section("stats")
config["stats"]["best_score"] = str(1450)

with open("settings.ini", "w", encoding="utf-8") as f:
    config.write(f)
print("Сақталды:", dict(config["sound"]), dict(config["stats"]))
```

Нәтижесі:

```text
Сақталды: {'music': 'yes', 'volume': '40'} {'best_score': '1450'}
```

> **Назар аудар:** `config.read()` файл жоқ болса **қате шығармайды**, жай бос конфигурация береді. Сондықтан `fallback` мәндері немесе әдепкі баптаулар міндетті.

> Кілттер регистрге сезімсіз: `Volume` пен `volume` — бір кілт (әдепкіде кіші әріпке айналады).

## 4. JSON баптаулар және әдепкі мәндер

JSON-ның артықшылығы — типтер (`int`, `bool`, тізім) сақталады, кірістірілген құрылым бар. Ең маңызды тәсіл — **әдепкі баптаулар + пайдаланушы баптаулары**:

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
    """Әдепкі мәндерді алып, оның үстіне файлдағы мәндерді жазады."""
    settings = json.loads(json.dumps(DEFAULTS))  # терең көшірме
    try:
        with open(path, encoding="utf-8") as f:
            user = json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return settings
    for key, value in user.items():
        if key in settings and isinstance(settings[key], dict) and isinstance(value, dict):
            settings[key].update(value)       # ішкі сөздікті біріктіру
        elif key in settings:
            settings[key] = value             # белгісіз кілттерді елемейміз
    return settings


def save_settings(settings, path="config.json"):
    with open(path, "w", encoding="utf-8") as f:
        json.dump(settings, f, ensure_ascii=False, indent=2)


# Пайдаланушы тек екі мәнді өзгерткен файл
with open("config.json", "w", encoding="utf-8") as f:
    json.dump({"theme": "dark", "window": {"width": 1024}}, f)

s = load_settings()
print(s["theme"], s["language"], s["window"])
```

Нәтижесі:

```text
dark kk {'width': 1024, 'height': 600}
```

Неге бұл маңызды:
- файл жоқ болса — бағдарлама әдепкі мәндермен жұмыс істейді;
- бағдарламаның жаңа нұсқасында жаңа параметр қосылса, ескі файлда ол жоқ болса да — әдепкі мән алынады;
- файл бұзылса — бағдарлама құламайды.

## 5. `Settings` класы

Баптауларды класқа орау — ыңғайлы және ОБП-ға сай:

```python
import json
from pathlib import Path


class Settings:
    """Қосымшаның баптаулары: JSON файлдан жүктеу, тексеру, сақтау."""

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
                print("Баптау файлы бұзылған, әдепкі мәндер қолданылады")

    def get(self, key):
        return self.data[key]

    def set(self, key, value, save=True):
        """Мәнді тексеріп орнатады; қате мән болса ValueError."""
        if key not in self.DEFAULTS:
            raise KeyError(f"Белгісіз баптау: {key}")
        if key in self.ALLOWED and value not in self.ALLOWED[key]:
            raise ValueError(f"{key} үшін рұқсат етілмеген мән: {value}")
        if key == "volume" and not 0 <= int(value) <= 100:
            raise ValueError("Дыбыс деңгейі 0..100 аралығында болуы керек")
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
    print("Қате:", e)
print(Settings().data)   # жаңа объект — файлдан қайта жүктеледі
```

Нәтижесі (бос папкада бірінші іске қосуда):

```text
Қате: language үшін рұқсат етілмеген мән: fr
{'language': 'kk', 'theme': 'dark', 'volume': 35, 'font_size': 12}
```

### Tkinter қосымшасында қолдану (идея)

```python
import tkinter as tk

THEMES = {"light": ("white", "black"), "dark": ("#222222", "#eeeeee")}
theme = "dark"  # нақты жобада: settings.get("theme")

root = tk.Tk()
root.title("Баптаулар мысалы")
bg, fg = THEMES[theme]
root.configure(bg=bg)
tk.Label(root, text="Сәлем! Тақырып баптаудан алынды.", bg=bg, fg=fg, font=("Arial", 14)).pack(padx=20, pady=20)
root.mainloop()
```

## 6. TOML — заманауи баптау форматы

TOML INI-ге ұқсас, бірақ мәндердің типі бар (жолдар тырнақшада, сандар, `true/false`, тізімдер). Python 3.11-ден бастап стандартты `tomllib` модулі TOML-ды **оқи** алады (жазу үшін сыртқы кітапхана керек). Python жобаларының `pyproject.toml` файлы осы форматта жазылады.

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
    print("tomllib тек Python 3.11+ нұсқасында бар")
```

Нәтижесі (Python 3.11+):

```text
Қыран 800 False
```

## 7. Орта айнымалылары және командалық жол аргументтері

Баптауды тек файлдан емес, **операциялық жүйенің орта айнымалыларынан** (environment variables) да алуға болады. Бұл әсіресе серверлерде және құпия деректер үшін қолданылады.

```python
import os

# Орта айнымалысы болса — соны, болмаса — әдепкі мәнді аламыз
db_name = os.environ.get("APP_DB", "school.db")
debug = os.environ.get("APP_DEBUG", "0") == "1"
print("Деректер қоры:", db_name, "| Debug:", debug)
```

Нәтижесі (орта айнымалылары орнатылмаған болса):

```text
Деректер қоры: school.db | Debug: False
```

Терминалда орнату:

```bash
# Linux / macOS
APP_DEBUG=1 python main.py
# Windows PowerShell
$env:APP_DEBUG = "1"; python main.py
```

Командалық жол аргументтері — бағдарламаны іске қосқанда параметр беру: `python main.py --level 3`. Ең қарапайым түрі — `sys.argv` тізімі; кәсіби түрі — `argparse` модулі.

```python
import sys

print("Аргументтер:", sys.argv[1:])
level = int(sys.argv[1]) if len(sys.argv) > 1 else 1
print("Басталатын деңгей:", level)
```

### Баптау көздерінің басымдығы

Әдетте бағдарлама баптауларды мына ретпен қарайды (жоғарыдағысы басым):

```text
1. Командалық жол аргументі   (python main.py --volume 30)
2. Орта айнымалысы            (APP_VOLUME=30)
3. Пайдаланушының config файлы (config.json)
4. Кодтағы әдепкі мәндер       (DEFAULTS)
```

## 8. Баптау файлдарының қауіпсіздігі

- **Құпиялар** (API кілттері, парольдер) — `config.json`-ға емес, орта айнымалыларына немесе `.env` файлына сақталады, ал `.env` `.gitignore`-ге жазылады.
- Репозиторийге **үлгі** файлды (`config.example.json`) жүктейміз, ал нақты `config.json`-ды `.gitignore`-ге қосамыз.
- Пайдаланушы баптауларын **тексереміз** (валидация): `volume = 500` немесе `language = "xyz"` бағдарламаны бұзбауы керек.
- Пайдаланушы баптауларын бағдарлама папкасында емес, үй папкасында сақтау дұрыс: `Path.home() / ".qyran" / "config.json"`.

## Жиі кездесетін қателер

**1. `configparser`-ге сан жазу.**

```python
import configparser

config = configparser.ConfigParser()
config["sound"] = {}
# config["sound"]["volume"] = 70   # TypeError: option values must be strings
config["sound"]["volume"] = str(70)  # дұрыс
print(config["sound"]["volume"])
```

**2. `getboolean` орнына `get` қолдану.**

```python
import configparser

config = configparser.ConfigParser()
config.read_string("[window]\nfullscreen = no\n")
if config.get("window", "fullscreen"):         # "no" — бос емес жол, яғни True!
    print("Қате: толық экран қосылып кетті")
if not config.getboolean("window", "fullscreen"):
    print("Дұрыс: толық экран өшірулі")
```

Нәтижесі:

```text
Қате: толық экран қосылып кетті
Дұрыс: толық экран өшірулі
```

**3. Файл жоқ болғанда құлау.** `json.load` → `FileNotFoundError`. Дұрыс: әдепкі мәндер + `try/except`.

**4. `DEFAULTS` сөздігін тікелей өзгерту.** `settings = DEFAULTS; settings["volume"] = 10` — енді `DEFAULTS` те өзгерді (бір объектіге екі сілтеме). Дұрыс: `dict(DEFAULTS)` немесе терең көшірме.

**5. Парольді баптау файлына жазып, GitHub-қа жүктеу.** Дұрыс: орта айнымалылары / `.env` + `.gitignore`.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| Конфигурация (баптау) | Кодты өзгертпей бағдарламаны басқаратын параметрлер |
| Hardcode | Мәнді кодтың ішіне тікелей жазу |
| INI | Бөлімдерден және «кілт = мән» жолдарынан тұратын формат |
| `configparser` | INI файлдарын оқитын/жазатын стандартты модуль |
| Әдепкі мән (default) | Баптау берілмесе қолданылатын мән |
| `fallback` | `configparser`-де жоқ кілт үшін әдепкі мән |
| TOML | Типтері бар заманауи баптау форматы (`tomllib`) |
| Орта айнымалысы | ОЖ деңгейінде орнатылатын атаулы мән (`os.environ`) |
| Валидация | Енгізілген мәннің дұрыстығын тексеру |

## Ескерту

- `configparser`-де әдепкі бойынша **интерполяция** қосулы: мәндегі `%` алмастырудың басы деп саналады (`%(name)s`), сондықтан `progress = 50%` жолы `InterpolationSyntaxError` тудырады. Шешімдері: `%%` деп жазу немесе `configparser.ConfigParser(interpolation=None)` құру.
- INI-дегі `[DEFAULT]` бөлімі ерекше: оның кілттері қалған барлық бөлімде әдепкі мән ретінде қолжетімді.
- `configparser` жазу кезінде бастапқы файлдың түсініктемелерін сақтамайды — `config.write()`-тан кейін олар жоғалады.
- `tomllib.load()` **екілік** режимде ашылған файлды талап етеді: `with open("config.toml", "rb") as f: tomllib.load(f)`.
- Командалық жол аргументтерін талдау үшін `sys.argv`-мен қолмен жұмыс істеудің орнына `argparse` қолданыңыз: ол типтерді өзі тексереді (`type=int`), `--help` анықтамасын және қате хабарламаларын шығарады.
- Құжаттама: https://docs.python.org/3/library/configparser.html, https://docs.python.org/3/library/tomllib.html, https://docs.python.org/3/library/argparse.html
