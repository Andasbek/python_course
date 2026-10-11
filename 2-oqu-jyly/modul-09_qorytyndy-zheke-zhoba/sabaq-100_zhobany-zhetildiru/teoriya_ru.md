# Урок 100. Доработка проекта. Добавление дополнительных возможностей.

## Темы

1. «Работает» ≠ «завершено»
2. Как выбрать дополнительную возможность?
3. Безопасное добавление возможности: отдельная ветка
4. Примеры возможностей
5. Оформление: единый стиль
6. Заморозка возможностей (feature freeze)
7. Дополнительно: превращение проекта в файл `.exe`
8. Частые ошибки
9. Основные понятия

## 1. «Работает» ≠ «завершено»

MVP решает основную проблему, но отдавать его незнакомому человеку ещё рано. **Завершённый продукт** — им может пользоваться любой пользователь без объяснений, он не падает и выглядит аккуратно.

**Критерии завершённости (Definition of Done)** — список, определяющий, что проект «готов»:

| Раздел | Критерий |
|---|---|
| Функционал | есть все Must и выбранные Should-возможности из ТЗ |
| Устойчивость | нет критичных/важных ошибок; все тесты зелёные; обработаны случаи без интернета/без файла |
| Удобство | все тексты грамотные и на одном языке; кнопки единообразны; пустые состояния и ошибки объяснены |
| Оформление | единый стиль (цвета, шрифты, отступы); название и значок окна; «О программе» |
| Код | PEP 8; docstring; нет мёртвого кода (неиспользуемых функций, закомментированного кода) |
| Репозиторий | README, `requirements.txt`, `.gitignore`; запускается в чистом окружении |

> **Правило:** прежде чем добавлять новую возможность, приведите существующие в соответствие с критериями. Лучше 5 безупречных возможностей, чем 10 недоделанных.

## 2. Как выбрать дополнительную возможность?

Время ограничено, поэтому обычно успевают добавить только 1–2 возможности. Для выбора оцениваем каждого кандидата тремя вопросами:

| Возможность | Польза для пользователя (1–3) | Техническая ценность (1–3) | Риск/время (1–3, меньше — лучше) | Баллы = П + Т − Р |
|---|---|---|---|---|
| Фильтр по месяцу | 3 | 2 | 1 | **4** |
| Перевод в доллары (API) | 2 | 3 | 2 | **3** |
| Экспорт в CSV | 2 | 2 | 1 | **3** |
| Ночной режим | 1 | 2 | 2 | 1 |
| Несколько пользователей + пароль | 2 | 2 | 3 | 1 |

**«Техническая ценность»** — добавляет ли возможность новую технологию или важную часть архитектуры? Например, подключение API расширяет проект работой с сетью, JSON и обработкой сетевых ошибок.

## 3. Безопасное добавление возможности: отдельная ветка

Новая возможность не должна сломать работающую версию. Поэтому делаем её **в отдельной ветке**:

```bash
git switch -c feature/export-csv      # новая ветка
# ... пишем код, проверяем, коммиты ...
python -m unittest discover -s tests  # все тесты зелёные?
git switch main
git merge feature/export-csv          # сливаем, только если работает
git push
```

Если возможность не готова вовремя — ветку не сливаем, `main` остаётся стабильной. Это самое важное правило безопасности перед релизом.

## 4. Примеры возможностей

### 4.1. Сохранение настроек (config)
Выбранные пользователем параметры (тема, валюта, последняя категория, размер окна) должны сохраняться до следующего запуска — правило удобства «не заставляй запоминать».

```python
"""settings.py — пользовательские настройки."""
import json
from pathlib import Path

DEFAULTS = {"theme": "light", "currency": "KZT", "last_category": "Еда",
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

Результат:
```text
dark
```

`__getitem__` и `__setitem__` — магические методы: позволяют работать с объектом как со словарём: `settings["theme"]`.

### 4.2. Экспорт с диалогом «Сохранить»
```python
import csv
from tkinter import filedialog, messagebox


def export_operations(operations):
    """Сохраняет операции в выбранный пользователем CSV-файл."""
    path = filedialog.asksaveasfilename(
        title="Сохранить как CSV",
        defaultextension=".csv",
        filetypes=[("Файл CSV", "*.csv"), ("Все файлы", "*.*")],
        initialfile="qalta_export.csv",
    )
    if not path:                       # пользователь нажал «Отмена»
        return
    try:
        with open(path, "w", newline="", encoding="utf-8-sig") as f:
            writer = csv.writer(f, delimiter=";")
            writer.writerow(["Дата", "Категория", "Сумма", "Комментарий"])
            for op in operations:
                writer.writerow([op["op_date"], op["category"], op["amount"], op["note"]])
    except OSError as err:
        messagebox.showerror("Ошибка", f"Не удалось сохранить файл:\n{err}")
        return
    messagebox.showinfo("Экспорт", f"Сохранено записей: {len(operations)}\n{path}")
```

Сохранить диаграмму как изображение ещё проще: `self.figure.savefig(path, dpi=150)`.

### 4.3. Примеры возможностей для игрового проекта
- таблица рекордов (JSON) и её показ в меню;
- уровни сложности (лёгкий/средний/сложный — коэффициент скорости);
- настройка включения/выключения звука (`settings["sound"]`);
- экран паузы (конечный автомат состояний).

## 5. Оформление: единый стиль

Единый стиль задаётся в одном месте — `ttk.Style`. Цвета и шрифты — как константы:

```python
import tkinter as tk
from tkinter import ttk, messagebox

PRIMARY = "#1565c0"      # основной цвет
ACCENT = "#2e7d32"       # акцент (успешное действие)
DANGER = "#c62828"       # опасное действие
FONT = ("Segoe UI", 11)
APP_NAME = "Карманные деньги"
VERSION = "1.0"


def apply_style(root):
    """Общий стиль для всего приложения."""
    style = ttk.Style(root)
    style.theme_use("clam")                       # одинаково выглядит во всех ОС
    style.configure(".", font=FONT)
    style.configure("TButton", padding=(10, 5))
    style.configure("Primary.TButton", background=PRIMARY, foreground="white")
    style.map("Primary.TButton", background=[("active", "#0d47a1")])
    style.configure("Danger.TButton", background=DANGER, foreground="white")
    style.configure("Title.TLabel", font=("Segoe UI", 18, "bold"), foreground=PRIMARY)


def build_menu(root):
    """Меню: Файл и Справка."""
    menubar = tk.Menu(root)
    file_menu = tk.Menu(menubar, tearoff=False)
    file_menu.add_command(label="Экспорт в CSV...", accelerator="Ctrl+E")
    file_menu.add_separator()
    file_menu.add_command(label="Выход", command=root.destroy)
    menubar.add_cascade(label="Файл", menu=file_menu)

    help_menu = tk.Menu(menubar, tearoff=False)
    help_menu.add_command(label="О программе", command=lambda: messagebox.showinfo(
        "О программе",
        f"{APP_NAME} v{VERSION}\nПриложение для учёта доходов и расходов.\n"
        "Автор: Имя Фамилия, 2027. Лицензия MIT."))
    menubar.add_cascade(label="Справка", menu=help_menu)
    root.config(menu=menubar)


if __name__ == "__main__":
    root = tk.Tk()
    root.title(f"{APP_NAME} v{VERSION}")
    apply_style(root)
    build_menu(root)
    ttk.Label(root, text="Баланс: 12 450 тг", style="Title.TLabel").pack(padx=20, pady=10)
    ttk.Button(root, text="Сохранить", style="Primary.TButton").pack(pady=4)
    ttk.Button(root, text="Удалить", style="Danger.TButton").pack(pady=4)
    root.mainloop()
```

Чек-лист оформления:
- [ ] Все кнопки в одном стиле; опасное действие — красное, основное действие — основного цвета.
- [ ] Отступы одинаковые (`padx`/`pady` = 8 или 10).
- [ ] В названии окна — имя программы и версия.
- [ ] Значок окна: `root.iconphoto(True, tk.PhotoImage(file="assets/icon.png"))`.
- [ ] Окно «О программе»: название, версия, автор, год.
- [ ] Пустые состояния: подсказки вроде «Пока нет операций».
- [ ] Орфография: прочитайте весь текст вслух (если интерфейс на казахском языке — проверьте буквы ә, ғ, қ, ң, ө, ұ, ү, һ, і).

## 6. Заморозка возможностей (feature freeze)

В профессиональных командах за несколько дней до релиза **возможности замораживаются**: после этого только исправляются ошибки, пишется документация, готовится презентация. Причина: каждая новая возможность — риск новой ошибки, а времени на её тестирование нет.

Типичная последовательность перед релизом:
```text
разработка возможностей ──► FEATURE FREEZE
                            ├── только исправление ошибок и документирование
                            └── релиз v1.0 (тег, GitHub Release)
```

Незавершённые после feature freeze идеи записываются в раздел README **«Планы на будущее»** (Roadmap) или в Issues с меткой `enhancement` — так видна перспектива развития проекта.

## 7. Дополнительно: превращение проекта в файл `.exe`

Для запуска на компьютере без установленного Python используется PyInstaller (необязательно):

```bash
pip install pyinstaller
pyinstaller --onefile --windowed --name QaltaAqshasy main.py
```

Готовый файл появится в `dist/QaltaAqshasy.exe`. Добавьте `build/`, `dist/`, `*.spec` в `.gitignore`; `.exe` можно загрузить на страницу GitHub Releases.

## Частые ошибки

**1. Синдром «ещё одной возможности».** За два дня до релиза начать сложную возможность и сломать весь проект. Правильно: отдельная ветка + feature freeze.

**2. Слияние без запуска тестов.** После `merge` программа не запускается. Правильно: перед слиянием — все тесты и основные сценарии.

**3. Задавать стиль каждому виджету отдельно.**
```python
import tkinter as tk

root = tk.Tk()
tk.Button(root, text="Сохранить", bg="#1565c0", fg="white", font=("Arial", 11))
tk.Button(root, text="Открыть", bg="#1565c1", fg="white", font=("Arial", 12))   # немного другой!
```
Объяснение: цвета и шрифты постепенно «расходятся». Правильно: константы и `ttk.Style`.

**4. Мёртвый код.** Закомментированные старые версии, `test2.py`, `old_gui.py`. Такой код путает читателя и сопровождающего. Правильно: удалить — в истории Git всё равно сохранится.

**5. Загрузить файл настроек в репозиторий** — у каждого пользователя должны быть свои настройки. `settings.json` — в `.gitignore`, а значения по умолчанию — в код.

## Основные понятия
| Понятие | Объяснение |
|---|---|
| Definition of Done | список критериев, определяющих, что проект «готов» |
| Ветка возможности (feature branch) | отдельная ветка Git, в которой создаётся новая возможность |
| `ttk.Style` | инструмент настройки единого стиля виджетов ttk в одном месте |
| Файл настроек | файл (JSON), хранящий параметры пользователя |
| Feature freeze | прекращение добавления новых возможностей перед релизом |
| Мёртвый код | код, не используемый в программе |
| PyInstaller | инструмент, превращающий программу на Python в исполняемый файл |

## Примечание

- Тема `"clam"` поддерживает изменение `background` у `ttk.Button`; в родных темах Windows (`"vista"`, `"winnative"`) и macOS (`"aqua"`) цвет фона кнопки часто игнорируется.
- `Path.read_text()`/`write_text()` по умолчанию используют кодировку системы — всегда передавайте `encoding="utf-8"`, иначе кириллица в Windows может сохраниться в cp1251.
- Файл настроек лучше хранить в папке пользователя (`Path.home() / ".qalta" / "settings.json"`), а не рядом с программой: туда у пользователя гарантированно есть права на запись.
- `filedialog.asksaveasfilename()` возвращает пустую строку `""` при нажатии «Отмена» — проверяйте результат перед открытием файла.
- PyInstaller собирает файл только для той ОС, на которой запущен: `.exe` для Windows нужно собирать в Windows.
- Документация: https://docs.python.org/3/library/tkinter.ttk.html#ttk-styling и https://pyinstaller.org/en/stable/
