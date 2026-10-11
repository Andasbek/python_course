# Урок 37. Загрузка изображений и вывод их на экран (Surface, blit).

## Темы

1. Surface — поверхность, на которой можно рисовать
2. blit — «наклеивание» одной поверхности на другую
3. Загрузка изображения из файла
4. Организация ресурсов и пути к файлам
5. Если файла нет — программа не должна падать
6. Размещение изображения: get_rect()
7. pygame.transform — преобразование изображения
8. Прозрачность: colorkey и alpha
9. Полный пример: фон, персонаж, вращающаяся монета
10. Графические ресурсы и лицензии
11. Частые ошибки
12. Основные понятия

## 1. Surface — поверхность, на которой можно рисовать

**Surface** — прямоугольный «лист бумаги» из пикселей. В Pygame:
- само окно — это Surface (`screen`);
- каждое загруженное изображение — Surface;
- мы и сами можем создать пустой Surface.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))

card = pygame.Surface((120, 80))       # пустая поверхность 120×80
card.fill((255, 200, 0))               # закрашиваем её жёлтым
pygame.draw.circle(card, (200, 0, 0), (60, 40), 30)   # рисуем на поверхности круг

print(card.get_size())
print(card.get_rect())
```

Результат:
```text
(120, 80)
<rect(0, 0, 120, 80)>
```

На Surface, как и на окне, можно рисовать функциями `fill()` и `draw`.

## 2. blit — «наклеивание» одной поверхности на другую

**blit** (block image transfer) — копирование одного Surface поверх другого. Аналогия: наклеить наклейку на страницу тетради.

```python
screen.blit(source, position)
```

- `source` — какое изображение;
- `position` — куда: кортеж `(x, y)` (левый верхний угол) или `Rect`.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
pygame.display.set_caption("Пример blit")

card = pygame.Surface((120, 80))
card.fill((255, 200, 0))
pygame.draw.circle(card, (200, 0, 0), (60, 40), 30)

clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((40, 40, 80))
    screen.blit(card, (20, 20))         # одна карточка
    screen.blit(card, (260, 200))       # та же карточка второй раз
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

Одно изображение можно blit-ить в сколько угодно мест — оно не копируется, а лишь «отпечатывается» на экране.

## 3. Загрузка изображения из файла

```python
image = pygame.image.load("assets/player.png")
```

Pygame читает форматы PNG, JPG, BMP, GIF (статичный), WEBP. Для игр удобнее всего **PNG** (поддерживает прозрачный фон).

**convert() и convert_alpha()**

Загруженное изображение нужно преобразовать в пиксельный формат экрана — иначе при каждом blit Pygame будет снова и снова его конвертировать, и игра замедлится.

| Метод | Когда |
|---|---|
| `image.convert()` | Изображения без прозрачности (фон, JPG) |
| `image.convert_alpha()` | PNG с прозрачным фоном (персонажи, предметы) |

> **Важно:** `convert()` работает только **после** вызова `set_mode()`. Иначе: `pygame.error: No video mode has been set`.

## 4. Организация ресурсов и пути к файлам

```text
my_game/
├── main.py
└── assets/
    ├── images/
    │   ├── background.jpg
    │   ├── player.png
    │   └── coin.png
    └── sounds/
```

При запуске программы из другой папки относительный путь (`"assets/player.png"`) может не сработать. Надёжный способ — строить путь к файлу **от папки, где лежит скрипт**:

```python
import os

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")
print(os.path.join(IMG_DIR, "player.png"))
```

Результат (пример):
```text
C:\Users\Aru\my_game\assets\images\player.png
```

`os.path.join` сам ставит разделитель `\` в Windows и `/` в Linux/macOS.

## 5. Если файла нет — программа не должна падать

Файл изображения может отсутствовать (например, его забыли скопировать вместе с проектом). Сделаем функцию загрузки безопасной: если изображение не найдено, вместо него вернём цветную «заглушку» (placeholder).

```python
import os
import pygame

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")


def load_image(name, size=None, alpha=True):
    """Загружает изображение; если не найдено — возвращает розовый квадрат."""
    path = os.path.join(IMG_DIR, name)
    try:
        image = pygame.image.load(path)
        image = image.convert_alpha() if alpha else image.convert()
    except (pygame.error, FileNotFoundError):
        print("Изображение не найдено:", path)
        image = pygame.Surface(size or (50, 50))
        image.fill((255, 0, 255))
    if size:
        image = pygame.transform.scale(image, size)
    return image
```

Эту функцию можно повторно использовать в любой игре с изображениями.

## 6. Размещение изображения: get_rect()

`image.get_rect()` возвращает `Rect` размером с изображение. Передав ему именованные аргументы, можно сразу указать положение:

```python
player_rect = player_img.get_rect(center=(400, 300))     # в центр
player_rect = player_img.get_rect(midbottom=(400, 600))  # на пол
screen.blit(player_img, player_rect)
```

Это прямое применение объекта Rect. Позже, чтобы перемещать персонажа, мы будем менять только `player_rect`.

## 7. pygame.transform — преобразование изображения

| Функция | Что делает |
|---|---|
| `transform.scale(img, (w, h))` | Изменение размера |
| `transform.scale_by(img, 2)` | Кратное увеличение/уменьшение (pygame 2.1.3+) |
| `transform.rotate(img, angle)` | Поворот против часовой стрелки (в градусах) |
| `transform.flip(img, flip_x, flip_y)` | Отражение по горизонтали/вертикали (зеркало) |
| `transform.smoothscale(img, (w, h))` | Качественное масштабирование |

Например, когда персонаж идёт влево, отражаем его изображение:

```python
player_left = pygame.transform.flip(player_right, True, False)
```

**Важно:** `rotate` при каждом повороте немного портит изображение. Поэтому всегда поворачиваем **оригинал**, а `Rect` повёрнутого изображения пересчитываем так, чтобы сохранялся центр.

## 8. Прозрачность: colorkey и alpha

- **colorkey** — указанный цвет становится прозрачным (приём «розового фона» из старых игр): `image.set_colorkey((255, 0, 255))`.
- **alpha** — полупрозрачность всей поверхности: `image.set_alpha(128)` (0 — невидимый, 255 — полностью видимый).

## 9. Полный пример: фон, персонаж, вращающаяся монета

Если в папке `assets/images` есть `background.jpg`, `player.png`, `coin.png`, пример покажет их, а если нет — цветные квадраты. Поэтому он запускается на любом компьютере.

```python
import os
import pygame

WIDTH, HEIGHT = 800, 600
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")


def load_image(name, size=None, alpha=True):
    """Загружает изображение; если не найдено — возвращает розовый квадрат."""
    path = os.path.join(IMG_DIR, name)
    try:
        image = pygame.image.load(path)
        image = image.convert_alpha() if alpha else image.convert()
    except (pygame.error, FileNotFoundError):
        image = pygame.Surface(size or (50, 50))
        image.fill((255, 0, 255))
    if size:
        image = pygame.transform.scale(image, size)
    return image


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Изображения")
clock = pygame.time.Clock()

background = load_image("background.jpg", (WIDTH, HEIGHT), alpha=False)
player = load_image("player.png", (80, 100))
player_left = pygame.transform.flip(player, True, False)
coin = load_image("coin.png", (48, 48))

player_rect = player.get_rect(midbottom=(WIDTH // 2, HEIGHT - 20))
angle = 0

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    angle = (angle + 3) % 360
    rotated = pygame.transform.rotate(coin, angle)
    coin_rect = rotated.get_rect(center=(WIDTH // 2, 150))

    screen.blit(background, (0, 0))
    screen.blit(player, player_rect)
    screen.blit(player_left, player_rect.move(-150, 0))
    screen.blit(rotated, coin_rect)
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

**Разбор:**
- изображения загружаются один раз **до** цикла (загрузка в цикле сильно замедляет игру);
- монета на каждом кадре поворачивается **из оригинала**, `rect` вычисляется по центру — монета вращается на месте, не «прыгая»;
- `player_rect.move(-150, 0)` — возвращает новый смещённый Rect (оригинал не меняется).

## 10. Графические ресурсы и лицензии

Не любую картинку из интернета можно вставить в игру — на неё есть авторское право.

| Лицензия | Значение |
|---|---|
| CC0 (Public Domain) | Можно свободно использовать, указывать автора не обязательно |
| CC BY | Свободно, но обязательно нужно указать автора |
| Некоммерческая (NC) | Только в некоммерческих проектах |

Рекомендуемые бесплатные ресурсы: **kenney.nl** (CC0), **opengameart.org**, **itch.io** (раздел бесплатных ассетов). Возьмите за привычку указывать источники ресурсов в README проекта.

## Частые ошибки

**1. `FileNotFoundError: No file 'player.png' found`** — файл в другой папке или неверное имя (`Player.PNG` ≠ `player.png`). Стройте полный путь через `os.path`.

**2. `convert()` вызван до создания окна**
```python
import pygame
pygame.init()
img = pygame.Surface((10, 10)).convert()   # pygame.error: No video mode has been set
screen = pygame.display.set_mode((400, 300))
```
Правильно: сначала `set_mode()`, затем `convert()`.

**3. Загрузка изображения в цикле**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((400, 300))
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    bg = pygame.Surface((400, 300))   # создание/загрузка заново на каждом кадре — медленно!
    screen.blit(bg, (0, 0))
    pygame.display.flip()
```
Правильно: загрузка и подготовка — до цикла.

**4. `convert()` для прозрачного PNG** — прозрачный фон становится чёрным. Для PNG-персонажей — `convert_alpha()`.

**5. Повторный поворот уже повёрнутого изображения** (`coin = rotate(coin, 3)`) — изображение размывается и всё увеличивается. Всегда поворачивайте оригинал.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Surface | Поверхность из пикселей; и окно, и изображение — это Surface |
| blit | Копирование одного Surface поверх другого |
| `image.load()` | Загрузка изображения из файла |
| `convert()` / `convert_alpha()` | Преобразование изображения в формат экрана (для скорости) |
| `get_rect()` | Получение Rect размером с изображение |
| `pygame.transform` | Модуль масштабирования, поворота, отражения |
| colorkey | Один цвет, который становится прозрачным |
| Ассеты (assets) | Ресурсы игры: изображения, звуки, шрифты |
| Лицензия | Право и условия использования ресурса |

## Примечание

- `convert()` и `convert_alpha()` приводят изображение к формату экрана и ускоряют `blit` в несколько раз. Вызывать их можно только **после** `pygame.display.set_mode()`, иначе будет ошибка.
- Если поворачивать изображение `pygame.transform.rotate()` каждый кадр заново, качество портится, а размер растёт. Всегда поворачивайте **оригинал** на нужный угол.
- `scale()` масштабирует быстро, но грубо; для качественного результата есть `pygame.transform.smoothscale()` (только для 24/32-битных поверхностей).
- Форматы PNG, JPG, BMP поддерживаются всегда; есть ли поддержка расширенных форматов, показывает `pygame.image.get_extended()`.
- Пути к файлам надёжнее строить так: `pathlib.Path(__file__).parent / "assets"` — программа работает, из какой бы папки её ни запустили.
