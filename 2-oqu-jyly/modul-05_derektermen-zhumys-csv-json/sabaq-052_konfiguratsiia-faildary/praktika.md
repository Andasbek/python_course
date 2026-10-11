# Сабақ 52. Практикалық тапсырмалар: конфигурация файлдары

## Базалық деңгей

### 1-тапсырма. Бірінші INI файлы

`configparser` арқылы `app.ini` файлын жаса: `[user]` бөлімінде `name`, `city`; `[view]` бөлімінде `theme = light`, `font_size = 14`, `show_tips = yes`. Содан кейін файлды оқып, мәндерді **дұрыс типтермен** шығар.

```text
Шығару:
Пайдаланушы: Айдана (Алматы)
Тақырып: light | Қаріп өлшемі: 14 (int) | Кеңестер: True (bool)
```

### 2-тапсырма. Сәлемдесу бағдарламасы

Бағдарлама іске қосылғанда `greet.ini` файлынан пайдаланушының атын оқиды. Файл жоқ болса немесе атау жоқ болса — атын сұрап, файлға сақтайды. Келесі іске қосуда енді сұрамайды.

```text
1-іске қосу:
Атың кім? Нұрлан
Танысқаныма қуаныштымын, Нұрлан! Атыңды есте сақтадым.

2-іске қосу:
Қайта көріскенше қуаныштымын, Нұрлан! Бұл сенің 2-кіруің.
```

Кеңес: кіру санын да файлда сақта (`visits`).

### 3-тапсырма. Әдепкі мәндер

`DEFAULTS = {"language": "kk", "volume": 70, "difficulty": "normal"}` берілген. `config.json` файлында тек `{"volume": 30}` жазылған. `load_settings()` функциясын жаз: нәтижесінде барлық үш кілт болуы керек, `volume` — файлдан, қалғаны — әдепкіден. Файлды жойып тексер — бағдарлама құламауы керек.

```text
Шығару:
{'language': 'kk', 'volume': 30, 'difficulty': 'normal'}
```

### 4-тапсырма. Баптаулар мәзірі (консоль)

Мәзір: `1 — көрсету`, `2 — тілді өзгерту`, `3 — дыбыс деңгейін өзгерту`, `0 — шығу`. Өзгерістер бірден `config.json`-ға сақталсын. Тіл тек `kk/ru/en`, дыбыс тек `0..100` болуы керек; қате енгізілсе — ескерту.

## Кеңейтілген деңгей

### 5-тапсырма. Pygame ойынын баптау

46–48-сабақтардағы командалық ойынға (немесе кез келген Pygame бағдарламаға) `game.ini` файлын қос:

```text
[window]
width = 800
height = 600
fps = 60

[game]
lives = 3
enemy_speed = 4

[sound]
enabled = yes
volume = 70
```

Ойын осы мәндерді оқып қолдансын. Файлды өзгертіп (мысалы, `lives = 5`, `fps = 30`), ойынның өзгергенін тексер.

### 6-тапсырма. `Settings` класы

Теориядағы `Settings` класын кеңейт:
- `reset()` әдісі — барлық мәндерді әдепкіге қайтарады;
- `__str__` — баптауларды әдемі кесте түрінде қайтарады;
- `font_size` тек 8..32 аралығында болсын.

### 7-тапсырма. Tkinter: тақырыпты сақтайтын терезе

Tkinter терезесінде «Ашық / Күңгірт» тақырыбын таңдайтын Radiobutton-дар және «Қаріп өлшемі» Scale виджеті болсын. Таңдау өзгергенде интерфейс бірден өзгеріп, `ui.json` файлына сақталсын. Бағдарламаны қайта ашқанда соңғы баптаулар қолданылсын.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Баптау көздерінің басымдығы

Үш көзден баптауларды біріктіретін функция жаз: әдепкі мәндер → `config.json` → орта айнымалылары (`APP_` префиксімен, мысалы `APP_VOLUME=30`) → командалық жол аргументтері (`--volume 30`). Әр баптаудың соңғы мәні **қай көзден** келгенін де шығар.

```text
Іске қосу: APP_LANGUAGE=ru python app.py --volume 15
Шығару:
language = ru        (орта айнымалысы)
theme    = dark      (config.json)
volume   = 15        (командалық жол)
debug    = False     (әдепкі)
```

### 9-тапсырма. INI ↔ JSON түрлендіргіш

Кез келген INI файлды JSON-ға айналдыратын (бөлім → ішкі сөздік) және кері түрлендіретін екі функция жаз. Сандар мен `yes/no` мәндері JSON-да тиісінше сан және `true/false` болсын.

## Үй тапсырмасы

1. Өзіңнің GUI жобаңа (31–32-сабақтардағы «Калькулятор» немесе «To-Do List») баптау файлын қос: терезе өлшемі мен орны, тақырып, соңғы ашылған файл сақталсын.
2. Жобаңа `config.example.json` үлгісін жасап, нақты `config.json`-ды `.gitignore`-ге қос.
3. Ойлан: 2000 оқушының деректерін JSON файлында сақтасақ, бір оқушыны табу үшін не істеу керек? Келесі сабақта деректер қорымен танысамыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**

```python
import configparser

config = configparser.ConfigParser()
config["user"] = {"name": "Айдана", "city": "Алматы"}
config["view"] = {"theme": "light", "font_size": "14", "show_tips": "yes"}
with open("app.ini", "w", encoding="utf-8") as f:
    config.write(f)

cfg = configparser.ConfigParser()
cfg.read("app.ini", encoding="utf-8")
size = cfg.getint("view", "font_size")
tips = cfg.getboolean("view", "show_tips")
print(f"Пайдаланушы: {cfg['user']['name']} ({cfg['user']['city']})")
print(f"Тақырып: {cfg['view']['theme']} | Қаріп өлшемі: {size} ({type(size).__name__}) "
      f"| Кеңестер: {tips} ({type(tips).__name__})")
```

Нәтижесі:

```text
Пайдаланушы: Айдана (Алматы)
Тақырып: light | Қаріп өлшемі: 14 (int) | Кеңестер: True (bool)
```

**2-тапсырма**

```python
import configparser

FILE = "greet.ini"
config = configparser.ConfigParser()
config.read(FILE, encoding="utf-8")

if not config.has_section("user"):
    config.add_section("user")

name = config.get("user", "name", fallback="")
visits = config.getint("user", "visits", fallback=0) + 1

if not name:
    name = input("Атың кім? ").strip()
    config["user"]["name"] = name
    print(f"Танысқаныма қуаныштымын, {name}! Атыңды есте сақтадым.")
else:
    print(f"Қайта көріскенше қуаныштымын, {name}! Бұл сенің {visits}-кіруің.")

config["user"]["visits"] = str(visits)
with open(FILE, "w", encoding="utf-8") as f:
    config.write(f)
```

**3-тапсырма**

```python
import json

DEFAULTS = {"language": "kk", "volume": 70, "difficulty": "normal"}


def load_settings(path="config.json"):
    settings = dict(DEFAULTS)
    try:
        with open(path, encoding="utf-8") as f:
            user = json.load(f)
        settings.update({k: v for k, v in user.items() if k in DEFAULTS})
    except (FileNotFoundError, json.JSONDecodeError):
        pass
    return settings


with open("config.json", "w", encoding="utf-8") as f:
    json.dump({"volume": 30}, f)
print(load_settings())
print(load_settings("jok.json"))
```

Нәтижесі:

```text
{'language': 'kk', 'volume': 30, 'difficulty': 'normal'}
{'language': 'kk', 'volume': 70, 'difficulty': 'normal'}
```

**4-тапсырма**

```python
import json

FILE = "config.json"
DEFAULTS = {"language": "kk", "volume": 70}


def load():
    try:
        with open(FILE, encoding="utf-8") as f:
            return {**DEFAULTS, **json.load(f)}
    except (FileNotFoundError, json.JSONDecodeError):
        return dict(DEFAULTS)


def save(data):
    with open(FILE, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)


settings = load()
while True:
    choice = input("\n1 — көрсету, 2 — тіл, 3 — дыбыс, 0 — шығу: ")
    if choice == "0":
        break
    elif choice == "1":
        for key, value in settings.items():
            print(f"  {key}: {value}")
    elif choice == "2":
        lang = input("Тіл (kk/ru/en): ").strip()
        if lang in ("kk", "ru", "en"):
            settings["language"] = lang
            save(settings)
            print("Сақталды")
        else:
            print("Мұндай тіл жоқ")
    elif choice == "3":
        value = input("Дыбыс (0..100): ")
        if value.isdigit() and 0 <= int(value) <= 100:
            settings["volume"] = int(value)
            save(settings)
            print("Сақталды")
        else:
            print("0-ден 100-ге дейінгі сан енгіз")
```

**5-тапсырма (баптауды оқитын бөлік)**

```python
import configparser
import pygame

cfg = configparser.ConfigParser()
cfg.read("game.ini", encoding="utf-8")

WIDTH = cfg.getint("window", "width", fallback=800)
HEIGHT = cfg.getint("window", "height", fallback=600)
FPS = cfg.getint("window", "fps", fallback=60)
LIVES = cfg.getint("game", "lives", fallback=3)
ENEMY_SPEED = cfg.getint("game", "enemy_speed", fallback=4)
SOUND_ON = cfg.getboolean("sound", "enabled", fallback=True)
VOLUME = cfg.getint("sound", "volume", fallback=70) / 100

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption(f"Өмір: {LIVES}, FPS: {FPS}")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((135, 206, 235))
    pygame.display.flip()
    clock.tick(FPS)
pygame.quit()
```

`fallback` арқылы `game.ini` жоқ болса да ойын әдепкі мәндермен іске қосылады.

**6-тапсырма**

```python
import json
from pathlib import Path


class Settings:
    DEFAULTS = {"language": "kk", "theme": "light", "volume": 70, "font_size": 12}
    ALLOWED = {"language": {"kk", "ru", "en"}, "theme": {"light", "dark"}}
    RANGES = {"volume": (0, 100), "font_size": (8, 32)}

    def __init__(self, path="config.json"):
        self.path = Path(path)
        self.data = dict(self.DEFAULTS)
        if self.path.exists():
            try:
                for key, value in json.loads(self.path.read_text(encoding="utf-8")).items():
                    if key in self.DEFAULTS:
                        self.set(key, value, save=False)
            except (json.JSONDecodeError, ValueError):
                self.data = dict(self.DEFAULTS)

    def set(self, key, value, save=True):
        if key not in self.DEFAULTS:
            raise KeyError(f"Белгісіз баптау: {key}")
        if key in self.ALLOWED and value not in self.ALLOWED[key]:
            raise ValueError(f"{key}: рұқсат етілмеген мән {value!r}")
        if key in self.RANGES:
            low, high = self.RANGES[key]
            if not low <= int(value) <= high:
                raise ValueError(f"{key}: {low}..{high} аралығында болуы керек")
            value = int(value)
        self.data[key] = value
        if save:
            self.save()

    def reset(self):
        self.data = dict(self.DEFAULTS)
        self.save()

    def save(self):
        self.path.write_text(json.dumps(self.data, ensure_ascii=False, indent=2), encoding="utf-8")

    def __str__(self):
        return "\n".join(f"{key:<10}| {value}" for key, value in self.data.items())


s = Settings("test_config.json")
s.set("font_size", 20)
try:
    s.set("font_size", 50)
except ValueError as e:
    print("Қате:", e)
print(s)
s.reset()
print("Қалпына келтірілді:", s.data["font_size"])
```

Нәтижесі:

```text
Қате: font_size: 8..32 аралығында болуы керек
language  | kk
theme     | light
volume    | 70
font_size | 20
Қалпына келтірілді: 12
```

**7-тапсырма**

```python
import json
import tkinter as tk

FILE = "ui.json"
THEMES = {"light": ("white", "black"), "dark": ("#222222", "#eeeeee")}

try:
    with open(FILE, encoding="utf-8") as f:
        saved = json.load(f)
except (FileNotFoundError, json.JSONDecodeError):
    saved = {}

root = tk.Tk()
root.title("Тақырып баптаулары")
theme_var = tk.StringVar(value=saved.get("theme", "light"))
size_var = tk.IntVar(value=saved.get("font_size", 14))

label = tk.Label(root, text="Сәлем, Python үйірмесі!")
label.pack(padx=20, pady=20)
frame = tk.Frame(root)
frame.pack()


def apply_and_save(*args):
    bg, fg = THEMES[theme_var.get()]
    root.configure(bg=bg)
    label.configure(bg=bg, fg=fg, font=("Arial", size_var.get()))
    with open(FILE, "w", encoding="utf-8") as f:
        json.dump({"theme": theme_var.get(), "font_size": size_var.get()}, f)


tk.Radiobutton(frame, text="Ашық", variable=theme_var, value="light", command=apply_and_save).pack(side="left")
tk.Radiobutton(frame, text="Күңгірт", variable=theme_var, value="dark", command=apply_and_save).pack(side="left")
tk.Scale(root, from_=8, to=32, orient="horizontal", label="Қаріп өлшемі",
         variable=size_var, command=apply_and_save).pack(fill="x", padx=20, pady=10)

apply_and_save()
root.mainloop()
```

**8-тапсырма**

```python
import json
import os
import sys

DEFAULTS = {"language": "kk", "theme": "light", "volume": 70, "debug": False}


def convert(value, sample):
    """Жолды әдепкі мәннің типіне айналдырады."""
    if isinstance(sample, bool):
        return value.lower() in ("1", "true", "yes", "on")
    if isinstance(sample, int):
        return int(value)
    return value


def load_all(argv, path="config.json"):
    values = {k: (v, "әдепкі") for k, v in DEFAULTS.items()}
    try:
        with open(path, encoding="utf-8") as f:
            for k, v in json.load(f).items():
                if k in DEFAULTS:
                    values[k] = (v, "config.json")
    except (FileNotFoundError, json.JSONDecodeError):
        pass
    for k in DEFAULTS:
        env = os.environ.get("APP_" + k.upper())
        if env is not None:
            values[k] = (convert(env, DEFAULTS[k]), "орта айнымалысы")
    for i, arg in enumerate(argv):
        key = arg[2:] if arg.startswith("--") else None
        if key in DEFAULTS and i + 1 < len(argv):
            values[key] = (convert(argv[i + 1], DEFAULTS[key]), "командалық жол")
    return values


for key, (value, source) in load_all(sys.argv[1:]).items():
    print(f"{key:<8} = {str(value):<10} ({source})")
```

**9-тапсырма**

```python
import configparser
import json


def ini_to_dict(text):
    cfg = configparser.ConfigParser()
    cfg.read_string(text)
    result = {}
    for section in cfg.sections():
        result[section] = {}
        for key, raw in cfg[section].items():
            if raw.lstrip("-").isdigit():
                value = int(raw)
            elif raw.lower() in ("yes", "no", "true", "false", "on", "off"):
                value = cfg.getboolean(section, key)
            else:
                value = raw
            result[section][key] = value
    return result


def dict_to_ini(data):
    cfg = configparser.ConfigParser()
    for section, items in data.items():
        cfg[section] = {k: ("yes" if v is True else "no" if v is False else str(v)) for k, v in items.items()}
    lines = []
    for section in cfg.sections():
        lines.append(f"[{section}]")
        lines += [f"{k} = {v}" for k, v in cfg[section].items()]
    return "\n".join(lines)


ini = "[window]\nwidth = 800\nfullscreen = no\n[game]\nname = Қыран\n"
data = ini_to_dict(ini)
print(json.dumps(data, ensure_ascii=False))
print(dict_to_ini(data))
```

Нәтижесі:

```text
{"window": {"width": 800, "fullscreen": false}, "game": {"name": "Қыран"}}
[window]
width = 800
fullscreen = no
[game]
name = Қыран
```

</details>
