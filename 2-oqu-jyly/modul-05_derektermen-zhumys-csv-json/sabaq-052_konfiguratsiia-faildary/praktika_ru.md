# Урок 52. Практические задания: файлы конфигурации

## Базовый уровень

### Задание 1. Первый INI-файл

Создай с помощью `configparser` файл `app.ini`: в разделе `[user]` — `name`, `city`; в разделе `[view]` — `theme = light`, `font_size = 14`, `show_tips = yes`. Затем прочитай файл и выведи значения **с правильными типами**.

```text
Вывод:
Пользователь: Айдана (Алматы)
Тема: light | Размер шрифта: 14 (int) | Подсказки: True (bool)
```

### Задание 2. Программа-приветствие

При запуске программа читает имя пользователя из файла `greet.ini`. Если файла нет или имени нет — спрашивает имя и сохраняет его в файл. При следующем запуске уже не спрашивает.

```text
1-й запуск:
Как тебя зовут? Нурлан
Приятно познакомиться, Нурлан! Я запомнил твоё имя.

2-й запуск:
Рад снова тебя видеть, Нурлан! Это твой 2-й вход.
```

Подсказка: количество входов тоже храни в файле (`visits`).

### Задание 3. Значения по умолчанию

Дано `DEFAULTS = {"language": "kk", "volume": 70, "difficulty": "normal"}`. В файле `config.json` записано только `{"volume": 30}`. Напиши функцию `load_settings()`: в результате должны быть все три ключа, `volume` — из файла, остальные — по умолчанию. Проверь, удалив файл, — программа не должна падать.

```text
Вывод:
{'language': 'kk', 'volume': 30, 'difficulty': 'normal'}
```

### Задание 4. Меню настроек (консоль)

Меню: `1 — показать`, `2 — изменить язык`, `3 — изменить громкость`, `0 — выход`. Изменения сразу сохраняются в `config.json`. Язык — только `kk/ru/en`, громкость — только `0..100`; при неверном вводе — предупреждение.

## Продвинутый уровень

### Задание 5. Настройка игры на Pygame

Добавь в командную игру из уроков 46–48 (или в любую программу на Pygame) файл `game.ini`:

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

Игра должна читать и использовать эти значения. Измени файл (например, `lives = 5`, `fps = 30`) и проверь, что игра изменилась.

### Задание 6. Класс `Settings`

Расширь класс `Settings` из теории:
- метод `reset()` — возвращает все значения к значениям по умолчанию;
- `__str__` — возвращает настройки в виде красивой таблицы;
- `font_size` должен быть только в диапазоне 8..32.

### Задание 7. Tkinter: окно, сохраняющее тему

В окне Tkinter должны быть Radiobutton для выбора темы «Светлая / Тёмная» и виджет Scale «Размер шрифта». При изменении выбора интерфейс сразу меняется и сохраняется в файл `ui.json`. При повторном открытии программы применяются последние настройки.

## ★ Олимпиадная / творческая задача

### Задание 8. Приоритет источников настроек

Напиши функцию, объединяющую настройки из нескольких источников: значения по умолчанию → `config.json` → переменные окружения (с префиксом `APP_`, например `APP_VOLUME=30`) → аргументы командной строки (`--volume 30`). Выведи также, **из какого источника** пришло итоговое значение каждой настройки.

```text
Запуск: APP_LANGUAGE=ru python app.py --volume 15
Вывод:
language = ru        (переменная окружения)
theme    = dark      (config.json)
volume   = 15        (командная строка)
debug    = False     (по умолчанию)
```

### Задание 9. Конвертер INI ↔ JSON

Напиши две функции: одна превращает любой INI-файл в JSON (раздел → вложенный словарь), другая — обратно. Числа и значения `yes/no` в JSON должны стать соответственно числом и `true/false`.

## Домашнее задание

1. Добавь файл настроек в свой GUI-проект («Калькулятор» или «To-Do List» с уроков 31–32): пусть сохраняются размер и положение окна, тема, последний открытый файл.
2. Создай для своего проекта шаблон `config.example.json`, а настоящий `config.json` добавь в `.gitignore`.
3. Подумай: если хранить данные 2000 учеников в JSON-файле, что нужно сделать, чтобы найти одного ученика? На следующем уроке познакомимся с базами данных.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**

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
print(f"Пользователь: {cfg['user']['name']} ({cfg['user']['city']})")
print(f"Тема: {cfg['view']['theme']} | Размер шрифта: {size} ({type(size).__name__}) "
      f"| Подсказки: {tips} ({type(tips).__name__})")
```

Результат:

```text
Пользователь: Айдана (Алматы)
Тема: light | Размер шрифта: 14 (int) | Подсказки: True (bool)
```

**Задание 2**

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
    name = input("Как тебя зовут? ").strip()
    config["user"]["name"] = name
    print(f"Приятно познакомиться, {name}! Я запомнил твоё имя.")
else:
    print(f"Рад снова тебя видеть, {name}! Это твой {visits}-й вход.")

config["user"]["visits"] = str(visits)
with open(FILE, "w", encoding="utf-8") as f:
    config.write(f)
```

**Задание 3**

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

Результат:

```text
{'language': 'kk', 'volume': 30, 'difficulty': 'normal'}
{'language': 'kk', 'volume': 70, 'difficulty': 'normal'}
```

**Задание 4**

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
    choice = input("\n1 — показать, 2 — язык, 3 — громкость, 0 — выход: ")
    if choice == "0":
        break
    elif choice == "1":
        for key, value in settings.items():
            print(f"  {key}: {value}")
    elif choice == "2":
        lang = input("Язык (kk/ru/en): ").strip()
        if lang in ("kk", "ru", "en"):
            settings["language"] = lang
            save(settings)
            print("Сохранено")
        else:
            print("Такого языка нет")
    elif choice == "3":
        value = input("Громкость (0..100): ")
        if value.isdigit() and 0 <= int(value) <= 100:
            settings["volume"] = int(value)
            save(settings)
            print("Сохранено")
        else:
            print("Введи число от 0 до 100")
```

**Задание 5 (часть, читающая настройки)**

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
pygame.display.set_caption(f"Жизни: {LIVES}, FPS: {FPS}")
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

Благодаря `fallback` игра запускается со значениями по умолчанию, даже если `game.ini` нет.

**Задание 6**

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
            raise KeyError(f"Неизвестная настройка: {key}")
        if key in self.ALLOWED and value not in self.ALLOWED[key]:
            raise ValueError(f"{key}: недопустимое значение {value!r}")
        if key in self.RANGES:
            low, high = self.RANGES[key]
            if not low <= int(value) <= high:
                raise ValueError(f"{key}: должно быть в диапазоне {low}..{high}")
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
    print("Ошибка:", e)
print(s)
s.reset()
print("Восстановлено:", s.data["font_size"])
```

Результат:

```text
Ошибка: font_size: должно быть в диапазоне 8..32
language  | kk
theme     | light
volume    | 70
font_size | 20
Восстановлено: 12
```

**Задание 7**

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
root.title("Настройки темы")
theme_var = tk.StringVar(value=saved.get("theme", "light"))
size_var = tk.IntVar(value=saved.get("font_size", 14))

label = tk.Label(root, text="Привет, кружок Python!")
label.pack(padx=20, pady=20)
frame = tk.Frame(root)
frame.pack()


def apply_and_save(*args):
    bg, fg = THEMES[theme_var.get()]
    root.configure(bg=bg)
    label.configure(bg=bg, fg=fg, font=("Arial", size_var.get()))
    with open(FILE, "w", encoding="utf-8") as f:
        json.dump({"theme": theme_var.get(), "font_size": size_var.get()}, f)


tk.Radiobutton(frame, text="Светлая", variable=theme_var, value="light", command=apply_and_save).pack(side="left")
tk.Radiobutton(frame, text="Тёмная", variable=theme_var, value="dark", command=apply_and_save).pack(side="left")
tk.Scale(root, from_=8, to=32, orient="horizontal", label="Размер шрифта",
         variable=size_var, command=apply_and_save).pack(fill="x", padx=20, pady=10)

apply_and_save()
root.mainloop()
```

**Задание 8**

```python
import json
import os
import sys

DEFAULTS = {"language": "kk", "theme": "light", "volume": 70, "debug": False}


def convert(value, sample):
    """Преобразует строку к типу значения по умолчанию."""
    if isinstance(sample, bool):
        return value.lower() in ("1", "true", "yes", "on")
    if isinstance(sample, int):
        return int(value)
    return value


def load_all(argv, path="config.json"):
    values = {k: (v, "по умолчанию") for k, v in DEFAULTS.items()}
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
            values[k] = (convert(env, DEFAULTS[k]), "переменная окружения")
    for i, arg in enumerate(argv):
        key = arg[2:] if arg.startswith("--") else None
        if key in DEFAULTS and i + 1 < len(argv):
            values[key] = (convert(argv[i + 1], DEFAULTS[key]), "командная строка")
    return values


for key, (value, source) in load_all(sys.argv[1:]).items():
    print(f"{key:<8} = {str(value):<10} ({source})")
```

**Задание 9**

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

Результат:

```text
{"window": {"width": 800, "fullscreen": false}, "game": {"name": "Қыран"}}
[window]
width = 800
fullscreen = no
[game]
name = Қыран
```

</details>
