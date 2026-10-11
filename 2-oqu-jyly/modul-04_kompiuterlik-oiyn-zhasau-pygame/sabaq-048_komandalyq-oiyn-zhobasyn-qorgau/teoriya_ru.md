# Урок 48. Защита командного игрового проекта. Турнир игр. Самостоятельная работа №3.

## Темы

1. README.md — «лицо» проекта
2. Запуск проекта на другом компьютере
3. Вклад участников по истории Git
4. Таблица рекордов: сохранение результатов в файл
5. Шпаргалка по Pygame
6. Частые ошибки
7. Основные понятия

## 1. README.md — «лицо» проекта

**README.md** — первый файл, который видит человек, открывший репозиторий на GitHub. Он пишется в формате Markdown и отвечает на вопросы: что это за проект, как его запустить и как в него играть.

```markdown
# Қыран

Аркада-раннер на Pygame: управляй беркутом, облетай горы и собирай золотые тумары.

![Скриншот](docs/screenshot.png)

### Установка и запуск

    git clone https://github.com/aliya-team/qyran-game.git
    cd qyran-game
    python -m venv .venv
    .venv\Scripts\activate        # Windows (Linux/macOS: source .venv/bin/activate)
    pip install -r requirements.txt
    python main.py

### Управление

| Клавиша | Действие |
|---|---|
| ↑ ↓ | Полёт вверх/вниз |
| Space | Ускорение |
| Esc | Пауза |

### Команда

- Алия Бекова — тимлид, меню и интерфейс (`main.py`, `ui.py`)
- Нурлан Сеитов — игровая логика (`player.py`, `enemies.py`)
- Дана Омарова — графика (`assets/images`, `assets.py`)
- Ерасыл Тохтаров — звук и тестирование (`sound.py`)

### Ресурсы и лицензии

- Спрайты: Kenney.nl (CC0)
- Звук удара: Freesound.org, автор …, CC BY 4.0
```

Обязательные разделы README: **название и описание**, **установка и запуск**, **управление**, **авторы**, **источники ресурсов и их лицензии**. Скриншот или GIF сильно повышает понятность.

## 2. Запуск проекта на другом компьютере

Игра, которая работает только на компьютере автора, — частая проблема. Чтобы проект запускался везде:

1. **Фиксируем зависимости** в `requirements.txt`:

```text
pygame==2.6.1
```

Файл можно создать автоматически командой `pip freeze > requirements.txt` (в активированном виртуальном окружении), а установить всё — `pip install -r requirements.txt`.

2. **Строим пути к ресурсам от папки проекта**, а не от текущего каталога:

```python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent
IMAGES_DIR = BASE_DIR / "assets" / "images"
SOUNDS_DIR = BASE_DIR / "assets" / "sounds"

print(IMAGES_DIR / "eagle.png")
```

3. **Учитываем регистр имён файлов**: в Windows `Eagle.png` и `eagle.png` — один файл, а в Linux и macOS — разные.

4. **Точка входа одна** — `main.py`, а запуск игры в нём защищён условием:

```python
def main():
    print("Игра запущена")  # здесь создаётся окно и идёт игровой цикл


if __name__ == "__main__":
    main()
```

## 3. Вклад участников по истории Git

История репозитория показывает, кто и что сделал в проекте:

```bash
git shortlog -sn --all          # количество коммитов каждого автора
git log --author="Dana" --oneline   # коммиты одного автора
git log --stat                  # какие файлы изменял каждый коммит
git blame player.py             # кто последним менял каждую строку файла
```

```text
    14  Nurlan Seitov
     9  Aliya Bekova
     6  Dana Omarova
     5  Erasyl Tokhtarov
```

> Количество коммитов — не единственный показатель! Художник может сделать мало коммитов, но каждый его коммит может содержать десятки изображений. Поэтому вместе с `shortlog` смотрят `git log --stat` и закрытые Issue/PR.

Если один человек коммитил под разными именами или почтами, объединить их в `shortlog` можно файлом `.mailmap` в корне репозитория.

## 4. Таблица рекордов: сохранение результатов в файл

Результаты игроков удобно хранить в JSON-файле. Функции ниже добавляют результат и возвращают «топ-5»:

```python
import json
from pathlib import Path

SCORES_FILE = Path("highscores.json")


def load_scores():
    """Читает список рекордов; если файла нет или он повреждён — пустой список."""
    try:
        with open(SCORES_FILE, encoding="utf-8") as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return []


def add_score(name, score, top=5):
    """Добавляет результат и сохраняет только лучшие top записей."""
    scores = load_scores()
    scores.append({"name": name, "score": score})
    scores.sort(key=lambda item: item["score"], reverse=True)
    scores = scores[:top]
    with open(SCORES_FILE, "w", encoding="utf-8") as f:
        json.dump(scores, f, ensure_ascii=False, indent=2)
    return scores


for row in add_score("Алия", 1250):
    print(row["name"], row["score"])
```

`sort(key=..., reverse=True)` сортирует по очкам по убыванию, а срез `[:top]` оставляет лучшие записи.

## 5. Шпаргалка по Pygame

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((640, 480))      # окно
clock = pygame.time.Clock()                        # время
font = pygame.font.SysFont("arial", 24)            # шрифт


class Ball(pygame.sprite.Sprite):
    """Спрайт: image + rect + update()."""

    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((30, 30), pygame.SRCALPHA)
        pygame.draw.circle(self.image, (255, 80, 80), (15, 15), 15)
        self.rect = self.image.get_rect(center=(320, 240))

    def update(self):
        keys = pygame.key.get_pressed()
        self.rect.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 5


balls = pygame.sprite.Group(Ball())                # группа спрайтов
running = True
while running:                                      # игровой цикл
    for event in pygame.event.get():                # 1) события
        if event.type == pygame.QUIT:
            running = False
    balls.update()                                  # 2) обновление
    screen.fill((30, 30, 60))                       # 3) отрисовка
    balls.draw(screen)
    screen.blit(font.render("Привет, Pygame!", True, (255, 255, 255)), (10, 10))
    pygame.display.flip()
    clock.tick(60)                                  # 60 FPS
pygame.quit()
```

| Что нужно? | Как? |
|---|---|
| Прямоугольник/круг/линия | `pygame.draw.rect`, `pygame.draw.circle`, `pygame.draw.line` |
| Загрузка изображения | `pygame.image.load("a.png").convert_alpha()` |
| Нажата ли клавиша (один раз) | `event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE` |
| Удерживается ли клавиша | `pygame.key.get_pressed()[pygame.K_LEFT]` |
| Щелчок мыши | `event.type == pygame.MOUSEBUTTONDOWN`, `event.pos` |
| Столкновение двух rect | `rect1.colliderect(rect2)` |
| Столкновение спрайта и группы | `pygame.sprite.spritecollide(sprite, group, dokill)` |
| Текст | `font.render(text, True, color)` + `screen.blit` |
| Звук | `pygame.mixer.Sound("hit.wav").play()` |
| Событие таймера | `pygame.time.set_timer(pygame.USEREVENT + 1, 1000)` |

## Частые ошибки

**1. Игра не открывается на другом компьютере** — там не установлен `pygame` или неверен путь к изображению. → Использовать `requirements.txt` и пути от `Path(__file__).parent`.

**2. Забывают `pygame.display.flip()`** — на экране ничего не видно:

```python
# Ошибка: нарисовано, но экран не обновлён
screen.fill((0, 0, 0))
pygame.draw.circle(screen, (255, 0, 0), (100, 100), 20)
# Правильно: после отрисовки
pygame.display.flip()
```

**3. Нет `clock.tick()`** — игра идёт слишком быстро, в зависимости от скорости компьютера. Правильно: `clock.tick(60)` в конце цикла.

**4. Нет `screen.fill()`** — движущийся объект оставляет «след». Правильно: в начале каждого кадра закрашивать фон.

**5. Шрифт создаётся в каждом кадре.** Если `pygame.font.SysFont` вызывается внутри цикла, игра тормозит. Правильно: создать шрифт один раз **до** цикла.

**6. В README нет инструкции по запуску.** Человек, открывший репозиторий, не знает, какие библиотеки ставить и какой файл запускать. → Раздел «Установка и запуск» с точными командами.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| README.md | Файл-описание проекта в формате Markdown, который GitHub показывает на главной странице репозитория |
| `requirements.txt` | Список внешних библиотек проекта с версиями |
| Виртуальное окружение (venv) | Изолированная папка с интерпретатором и библиотеками одного проекта |
| Точка входа | Файл, с которого запускается программа (`main.py`) |
| `git shortlog -sn` | Команда, показывающая количество коммитов каждого автора |
| `git blame` | Команда, показывающая автора последнего изменения каждой строки |
| Таблица рекордов | Список лучших результатов, сохраняемый между запусками игры |

## Примечание

- В Markdown блок кода внутри README оформляют тройными обратными кавычками с указанием языка (например, `bash` или `python` сразу после открывающих кавычек) — GitHub подсветит синтаксис.
- `pip freeze` выводит **все** пакеты окружения; если ставили лишнее в глобальный Python, в файл попадёт мусор. Поэтому `requirements.txt` создают только из чистого виртуального окружения проекта.
- `Path(__file__).resolve().parent` надёжнее, чем `os.getcwd()`: текущая папка зависит от того, откуда запущен скрипт, а `__file__` — всегда путь к самому модулю.
- При сохранении рекордов ловите `json.JSONDecodeError`: если игра аварийно завершилась во время записи, файл может оказаться пустым или обрезанным.
- Документация: https://docs.python.org/3/library/venv.html, https://pip.pypa.io/en/stable/reference/requirements-file-format/, https://www.pygame.org/docs/
