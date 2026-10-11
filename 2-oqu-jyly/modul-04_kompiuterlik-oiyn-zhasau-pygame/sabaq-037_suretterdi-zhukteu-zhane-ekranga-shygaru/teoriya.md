# Сабақ 37. Суреттерді жүктеу және экранға шығару (Surface, blit).

## Тақырыптар

1. Surface — сурет салуға болатын бет
2. blit — бір бетті екіншісіне «жапсыру»
3. Суретті файлдан жүктеу
4. Ресурстарды ұйымдастыру және файл жолдары
5. Файл жоқ болса — бағдарлама құламасын
6. Суретті орналастыру: get_rect()
7. pygame.transform — суретті түрлендіру
8. Мөлдірлік: colorkey және alpha
9. Толық мысал: фон, кейіпкер, айналатын тиын
10. Графикалық ресурстар және лицензиялар
11. Жиі кездесетін қателер
12. Негізгі ұғымдар

## 1. Surface — сурет салуға болатын бет

**Surface** — пиксельдерден тұратын тіктөртбұрышты «қағаз парағы». Pygame-де:
- терезенің өзі — Surface (`screen`);
- жүктелген әр сурет — Surface;
- өзіміз де бос Surface құра аламыз.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))

card = pygame.Surface((120, 80))       # 120×80 бос бет
card.fill((255, 200, 0))               # оны сарыға бояу
pygame.draw.circle(card, (200, 0, 0), (60, 40), 30)   # бетке шеңбер салу

print(card.get_size())
print(card.get_rect())
```

Нәтижесі:
```text
(120, 80)
<rect(0, 0, 120, 80)>
```

Surface-қа да, терезеге салғандай, `fill()` және `draw` функцияларымен сурет салуға болады.

## 2. blit — бір бетті екіншісіне «жапсыру»

**blit** (block image transfer) — бір Surface-ты екіншісінің үстіне көшіру. Аналогия: жапсырманы (наклейка) дәптер бетіне жапсыру.

```python
screen.blit(source, position)
```

- `source` — қай суретті;
- `position` — қай жерге: `(x, y)` кортежі (сол жақ жоғарғы бұрыш) немесе `Rect`.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
pygame.display.set_caption("blit мысалы")

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
    screen.blit(card, (20, 20))         # бір карточканы
    screen.blit(card, (260, 200))       # сол карточканы екінші рет
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

Бір суретті бірнеше жерге шексіз blit жасауға болады — ол көшірілмейді, тек экранға «басылады».

## 3. Суретті файлдан жүктеу

```python
image = pygame.image.load("assets/player.png")
```

Pygame PNG, JPG, BMP, GIF (статикалық), WEBP форматтарын оқиды. Ойындар үшін ең қолайлысы — **PNG** (мөлдір фонды қолдайды).

**convert() және convert_alpha()**

Жүктелген суретті экранның пиксель форматына айналдыру керек — әйтпесе әр blit кезінде Pygame оны қайта-қайта түрлендіріп, ойын баяулайды.

| Әдіс | Қашан |
|---|---|
| `image.convert()` | Мөлдірлігі жоқ суреттер (фон, JPG) |
| `image.convert_alpha()` | Мөлдір фоны бар PNG (кейіпкерлер, заттар) |

> **Маңызды:** `convert()` тек `set_mode()` шақырылғаннан **кейін** жұмыс істейді. Әйтпесе: `pygame.error: No video mode has been set`.

## 4. Ресурстарды ұйымдастыру және файл жолдары

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

Бағдарламаны басқа папкадан іске қосқанда салыстырмалы жол (`"assets/player.png"`) жұмыс істемей қалуы мүмкін. Сенімді тәсіл — файл жолын **скрипт орналасқан папкадан** құру:

```python
import os

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")
print(os.path.join(IMG_DIR, "player.png"))
```

Нәтижесі (мысал):
```text
C:\Users\Aru\my_game\assets\images\player.png
```

`os.path.join` Windows-та `\`, Linux/macOS-та `/` бөлгішін өзі қояды.

## 5. Файл жоқ болса — бағдарлама құламасын

Сурет файлы жоқ болуы мүмкін (мысалы, жобаны көшіргенде ұмытылса). Жүктеу функциясын қауіпсіз жасаймыз: сурет табылмаса, оның орнына түрлі-түсті «бос орын» (placeholder) қайтарамыз.

```python
import os
import pygame

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")


def load_image(name, size=None, alpha=True):
    """Суретті жүктейді; табылмаса — қызғылт шаршы қайтарады."""
    path = os.path.join(IMG_DIR, name)
    try:
        image = pygame.image.load(path)
        image = image.convert_alpha() if alpha else image.convert()
    except (pygame.error, FileNotFoundError):
        print("Сурет табылмады:", path)
        image = pygame.Surface(size or (50, 50))
        image.fill((255, 0, 255))
    if size:
        image = pygame.transform.scale(image, size)
    return image
```

Бұл функцияны суреттер қолданылатын кез келген ойында қайта қолдануға болады.

## 6. Суретті орналастыру: get_rect()

`image.get_rect()` суреттің өлшеміндей `Rect` қайтарады. Оған кілт сөзді аргументтер беріп, бірден орнын көрсетуге болады:

```python
player_rect = player_img.get_rect(center=(400, 300))     # ортаға
player_rect = player_img.get_rect(midbottom=(400, 600))  # еденге
screen.blit(player_img, player_rect)
```

Бұл — Rect объектісінің тікелей қолданысы. Кейін кейіпкерді жылжыту үшін тек `player_rect`-ті өзгертеміз.

## 7. pygame.transform — суретті түрлендіру

| Функция | Не істейді |
|---|---|
| `transform.scale(img, (w, h))` | Өлшемін өзгерту |
| `transform.scale_by(img, 2)` | Еселеп үлкейту/кішірейту (pygame 2.1.3+) |
| `transform.rotate(img, angle)` | Сағат тіліне қарсы бұру (градуспен) |
| `transform.flip(img, flip_x, flip_y)` | Көлденең/тік айналдыру (айна) |
| `transform.smoothscale(img, (w, h))` | Сапалы масштабтау |

Мысалы, кейіпкер солға жүргенде оның бейнесін айналдырамыз:

```python
player_left = pygame.transform.flip(player_right, True, False)
```

**Маңызды:** `rotate` әр бұрғанда суретті сәл бұзады. Сондықтан әрқашан **түпнұсқадан** бұрамыз, ал бұрылған суреттің `Rect`-ін ортасы сақталатындай қайта есептейміз.

## 8. Мөлдірлік: colorkey және alpha

- **colorkey** — көрсетілген бір түс мөлдір болады (ескі ойындардағы «қызғылт фон» тәсілі): `image.set_colorkey((255, 0, 255))`.
- **alpha** — бүкіл беттің жартылай мөлдірлігі: `image.set_alpha(128)` (0 — көрінбейді, 255 — толық).

## 9. Толық мысал: фон, кейіпкер, айналатын тиын

Бұл мысал `assets/images` папкасында `background.jpg`, `player.png`, `coin.png` болса — оларды көрсетеді, болмаса — түсті шаршыларды. Сондықтан кез келген компьютерде іске қосылады.

```python
import os
import pygame

WIDTH, HEIGHT = 800, 600
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")


def load_image(name, size=None, alpha=True):
    """Суретті жүктейді; табылмаса — қызғылт шаршы қайтарады."""
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
pygame.display.set_caption("Суреттер")
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

**Талдау:**
- суреттер циклден **бұрын**, бір рет жүктеледі (циклде жүктеу ойынды қатты баяулатады);
- тиын әр кадрда **түпнұсқадан** бұрылады, `rect` ортасы бойынша есептеледі — тиын «секірмей», бір орында айналады;
- `player_rect.move(-150, 0)` — жылжытылған жаңа Rect қайтарады (түпнұсқа өзгермейді).

## 10. Графикалық ресурстар және лицензиялар

Интернеттегі кез келген суретті ойынға қоюға болмайды — оның авторлық құқығы бар.

| Лицензия | Мағынасы |
|---|---|
| CC0 (Public Domain) | Еркін қолдануға болады, авторды көрсету міндетті емес |
| CC BY | Еркін, бірақ авторды міндетті түрде көрсету керек |
| Коммерциялық емес (NC) | Тек коммерциялық емес жобаларда |

Ұсынылатын тегін ресурстар: **kenney.nl** (CC0), **opengameart.org**, **itch.io** (тегін ассеттер бөлімі). Жоба README-сінде ресурстар көзін жазуды әдетке айналдырыңыз.

## Жиі кездесетін қателер

**1. `FileNotFoundError: No file 'player.png' found`** — файл басқа папкада немесе атауы қате (`Player.PNG` ≠ `player.png`). `os.path` арқылы толық жол құрыңыз.

**2. `convert()` терезеден бұрын шақырылған**
```python
import pygame
pygame.init()
img = pygame.Surface((10, 10)).convert()   # pygame.error: No video mode has been set
screen = pygame.display.set_mode((400, 300))
```
Дұрысы: алдымен `set_mode()`, сосын `convert()`.

**3. Циклде суретті жүктеу**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((400, 300))
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    bg = pygame.Surface((400, 300))   # әр кадрда қайта құру/жүктеу — баяу!
    screen.blit(bg, (0, 0))
    pygame.display.flip()
```
Дұрысы: жүктеу мен дайындау — циклге дейін.

**4. Мөлдір PNG-ге `convert()` қолдану** — мөлдір фон қара болып шығады. PNG кейіпкерлер үшін — `convert_alpha()`.

**5. Бұрылған суретті қайта бұру** (`coin = rotate(coin, 3)`) — сурет бұлдырап, үлкейе береді. Әрқашан түпнұсқадан бұрыңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Surface | Пиксельдерден тұратын бет; терезе де, сурет те Surface |
| blit | Бір Surface-ты екіншісінің үстіне көшіру |
| `image.load()` | Суретті файлдан жүктеу |
| `convert()` / `convert_alpha()` | Суретті экран форматына айналдыру (жылдамдық үшін) |
| `get_rect()` | Сурет өлшеміндей Rect алу |
| `pygame.transform` | Масштабтау, бұру, айналдыру модулі |
| colorkey | Мөлдір болатын бір түс |
| Ассеттер (assets) | Ойын ресурстары: суреттер, дыбыстар, қаріптер |
| Лицензия | Ресурсты қолдану құқығы мен шарттары |

## Ескерту

- `convert()` және `convert_alpha()` суретті экран форматына келтіріп, `blit`-ті бірнеше есе жылдамдатады. Оларды тек `pygame.display.set_mode()`-дан **кейін** шақыруға болады, әйтпесе қате шығады.
- `pygame.transform.rotate()` суретті әр кадрда қайта бұрсаңыз, сапасы бұзылып, өлшемі өсе береді. Әрқашан **түпнұсқа** суретті қажетті бұрышқа бұрыңыз.
- `scale()` жылдам, бірақ дөрекі масштабтайды; сапалы нәтиже үшін `pygame.transform.smoothscale()` (тек 24/32 биттік беттер үшін) қолданылады.
- PNG, JPG, BMP форматтары әрқашан қолдау табады; кеңейтілген форматтар бар-жоғын `pygame.image.get_extended()` көрсетеді.
- Файл жолдарын `pathlib.Path(__file__).parent / "assets"` түрінде құрастырған сенімді — бағдарлама кез келген папкадан іске қосылса да жұмыс істейді.
