# Урок 100. Практические задания: доработка проекта

## Базовый уровень

### Задание 1. Самопроверка по Definition of Done (обязательно, 10 мин)
Заполните список ниже для своего проекта. Каждый пункт «Нет» — первоочередная работа на сегодня (раньше новых возможностей).

| № | Критерий | Да / Нет | Что нужно сделать |
|---|---|---|---|
| 1 | Все Must-возможности работают | | |
| 2 | Нет критичных/важных ошибок (Issues) | | |
| 3 | `python -m unittest discover -s tests` → OK | | |
| 4 | Программа не падает при неверном вводе | | |
| 5 | Обработаны случаи без интернета / без файла | | |
| 6 | Все тексты грамотные, без ошибок | | |
| 7 | Кнопки и отступы единообразны | | |
| 8 | Название окна, версия, «О программе» | | |
| 9 | Нет мёртвого кода, лишних файлов вроде `test2.py` | | |
| 10 | Запускается в чистом окружении | | |

### Задание 2. Выбор возможности (обязательно)
Оцените Should/Could-возможности из своего списка MoSCoW по таблице из раздела 2 теории и выберите **1–2 возможности** с наибольшим баллом. Для каждой откройте issue (метка `feature`).

### Задание 3. Возможность в ветке (обязательно)
Реализуйте выбранную возможность в отдельной ветке (`feature/...`). Перед слиянием: все тесты зелёные, основные сценарии проходят, для новой возможности есть хотя бы 1 тест или тест-кейс. Если не хватает времени — не сливайте, запишите в раздел README «Планы на будущее».

### Задание 4. Оформление (обязательно)
По чек-листу из раздела 5 теории:
- вынесите цвета и шрифты в константы и применяйте их через `ttk.Style` (или в Pygame — один словарь `COLORS` и один шрифт);
- добавьте окно «О программе» (или экран «Авторы» в игре);
- напишите версию в названии окна (`v1.0`);
- прочитайте все тексты вслух и исправьте орфографические ошибки.

## Продвинутый уровень

### Задание 5. Сохранение настроек
Сохраните в JSON не менее 2 параметров, выбираемых пользователем (последняя категория, размер окна, тема, звук, уровень сложности и т.п.), и восстанавливайте их при следующем запуске. Размер окна: `root.geometry()` должен сохраняться перед закрытием (`protocol("WM_DELETE_WINDOW", ...)`).

### Задание 6. Сохранение отчёта как изображения
Добавьте пункт меню «Сохранить диаграмму...», сохраняющий диаграмму в PNG (`filedialog.asksaveasfilename` + `figure.savefig`). Для игрового проекта — скриншот через `pygame.image.save(screen, "screenshot.png")` (клавиша F12).

### Задание 7. Поиск мёртвого кода
С помощью инструмента `vulture` (`pip install vulture`, `vulture qalta/`) или вручную найдите и удалите неиспользуемые функции, импорты и переменные своего проекта. После каждого удаления запускайте тесты.

## ★ Олимпиадная / творческая задача

### Задание 8. Исполняемый файл и релиз
Создайте с помощью PyInstaller `.exe` (или исполняемый файл для macOS/Linux) и проверьте его на компьютере без установленного Python. Если есть изображения и звуки, используйте параметр `--add-data` и определяйте пути в коде через `sys._MEIPASS`.

### Задание 9. Статистика использования (локальная)
Добавьте в программу небольшой модуль, записывающий в локальный `usage.json`, сколько раз использовалась каждая возможность (в интернет ничего не отправляется!). Показывайте в окне «О программе»: «Добавлено операций: 37, экспортов: 5». С учётом этики личных данных (урок 86) открыто напишите в README, что сохраняется.

## Домашнее задание
1. Feature freeze: с сегодняшнего дня новые возможности не добавляем. Незавершённые ветки не сливайте.
2. Доведите все пункты списка Definition of Done до состояния «Да». `push`.
3. К следующему уроку: сделайте 3–4 скриншота программы (главный экран, ввод, результат/диаграмма, сообщение об ошибке) и положите их в папку `docs/screenshots/`.

## Критерии оценивания
| Критерий | Доля | Что проверяется |
|---|---|---|
| Проект доведён до завершённого состояния | 40% | выполнено ≥ 8 пунктов списка Definition of Done, тесты зелёные |
| Рациональность выбора и реализации возможности | 25% | выбор обоснован, сделано в ветке, `main` не сломана |
| Оформление и чистота кода | 20% | единый стиль, «О программе», нет мёртвого кода |
| Самостоятельная работа, дисциплина | 15% | feature freeze соблюдена, коммиты осмысленные |

## Для педагога: решения
<details><summary>Показать решения</summary>

**Задания 1–4** проверяются индивидуально. Самые частые проблемы: (1) тесты вызывают старый API (при изменении кода тесты не обновлены); (2) файлы вроде `test.py`, `copy_of_gui.py`; (3) разные названия кнопок («OK», «Сохранить», «Готово»). Если ученик взялся за одну сложную возможность, то по истечении 50% времени предложите остановиться и перейти к более простому варианту.

**Задание 5.**
```python
import json
import tkinter as tk
from pathlib import Path

SETTINGS_FILE = Path("settings.json")


def load_settings():
    try:
        return json.loads(SETTINGS_FILE.read_text(encoding="utf-8"))
    except (FileNotFoundError, json.JSONDecodeError):
        return {"geometry": "600x400", "last_category": "Еда"}


def save_settings(data):
    SETTINGS_FILE.write_text(json.dumps(data, ensure_ascii=False, indent=2),
                             encoding="utf-8")


settings = load_settings()
root = tk.Tk()
root.title("Пример настроек")
root.geometry(settings["geometry"])


def on_close():
    settings["geometry"] = root.geometry()      # например: '640x420+100+50'
    save_settings(settings)
    root.destroy()


root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

**Задание 6.**
```python
from tkinter import filedialog, messagebox


def save_chart(figure):
    """Сохраняет рисунок Matplotlib в PNG."""
    path = filedialog.asksaveasfilename(defaultextension=".png",
                                        filetypes=[("Изображение PNG", "*.png")],
                                        initialfile="qalta_chart.png")
    if path:
        figure.savefig(path, dpi=150, bbox_inches="tight")
        messagebox.showinfo("Сохранено", f"Диаграмма сохранена:\n{path}")
```
Pygame: в цикле событий `if event.type == pygame.KEYDOWN and event.key == pygame.K_F12: pygame.image.save(screen, "screenshot.png")`.

**Задание 7.** Проверка: результаты отчёта `vulture` с «60% confidence» не удаляйте автоматически, каждый нужно проверить (например, методы, вызываемые через Tkinter `command=`, могут выглядеть «неиспользуемыми»).

**Задание 8.**
```python
import sys
from pathlib import Path


def resource_path(relative):
    """Путь к файлу ресурса: работает и при разработке, и после PyInstaller."""
    base = Path(getattr(sys, "_MEIPASS", Path(__file__).resolve().parent))
    return base / relative
```
```bash
pyinstaller --onefile --windowed --name QaltaAqshasy --add-data "assets;assets" main.py
```
(В Windows разделитель `;`, в macOS/Linux — `:`: `--add-data "assets:assets"`.)

**Задание 9.**
```python
import json
from pathlib import Path


class Usage:
    """Счётчик использования возможностей (только в локальном файле)."""

    def __init__(self, path="usage.json"):
        self.path = Path(path)
        try:
            self.counts = json.loads(self.path.read_text(encoding="utf-8"))
        except (FileNotFoundError, json.JSONDecodeError):
            self.counts = {}

    def hit(self, feature):
        self.counts[feature] = self.counts.get(feature, 0) + 1
        self.path.write_text(json.dumps(self.counts, ensure_ascii=False), encoding="utf-8")

    def summary(self):
        return (f"Добавлено операций: {self.counts.get('add', 0)}, "
                f"экспортов: {self.counts.get('export', 0)}")


u = Usage("usage_demo.json")
for _ in range(3):
    u.hit("add")
u.hit("export")
print(u.summary())
Path("usage_demo.json").unlink()
```
Результат:
```text
Добавлено операций: 3, экспортов: 1
```
</details>
