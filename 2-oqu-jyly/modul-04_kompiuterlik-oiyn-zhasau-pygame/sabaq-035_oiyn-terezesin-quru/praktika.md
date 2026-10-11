# Сабақ 35. Практикалық тапсырмалар: терезе, түстер, координаталар

> Барлық тапсырмада терезе өлшемі мен түстерді файл басында тұрақтылар ретінде жаз. Салу үшін әзірге тек `screen.fill(color, (x, y, w, h))` және `screen.set_at()` қолданамыз.

## Базалық деңгей

### 1-тапсырма. «Координаталар диктанты» (дәптерде)
800×600 терезе берілген. Нүктелердің координаталарын жаз:
1. Терезенің ортасы.
2. Оң жақ жоғарғы бұрыш.
3. Сол жақ төменгі бұрыш.
4. 50×50 шаршы оң жақ төменгі бұрышқа тиіп тұруы үшін оның сол жақ жоғарғы бұрышы.
5. 200×100 тіктөртбұрыш дәл ортада тұруы үшін оның сол жақ жоғарғы бұрышы.

### 2-тапсырма. Үш түсті жалау
600×400 терезеде көлденең үш жолақты жалау сал (мысалы, ақ–көк–қызыл). Жолақ биіктігі `HEIGHT // 3` арқылы есептелсін — терезе өлшемін өзгерткенде жалау бұзылмауы керек.

### 3-тапсырма. Тік жолақты жалау
Тік үш жолақты жалау сал (мысалы, жасыл–ақ–қызыл). Жолақ ені `WIDTH // 3`.

### 4-тапсырма. Терезе баптаулары
Терезе: 800×600, тақырыбы «Менің әлемім», өлшемі өзгертіледі (`RESIZABLE`), белгішесі — 32×32 жасыл шаршы. Терезе өлшемі өзгергенде консольге жаңа өлшем шықсын.

Мысал шығару:
```text
Жаңа өлшем: 1024 x 700
```

## Кеңейтілген деңгей

### 5-тапсырма. Түстер палитрасы
Терезені 4×2 торға бөліп, әр ұяшыққа түс атауымен (`pygame.Color("red")`, `"orange"`, `"yellow"`, `"green"`, `"cyan"`, `"blue"`, `"purple"`, `"pink"`) бояп шық. Ұяшықтар арасында 5 пиксель ақ жиек болсын.

### 6-тапсырма. Градиент
400×400 терезеде жоғарыдан төмен қарай қара түстен көкке біртіндеп ауысатын градиент сал (әр жол — биіктігі 1 пиксель, `fill` арқылы).
**Кеңес:** `blue = y * 255 // (HEIGHT - 1)`.

### 7-тапсырма. Ширектер
Терезені ортасынан 4 ширекке бөліп, әр ширекті әр түрлі түске боя. Тінтуірді басқанда консольге қай ширекке басылғаны шықсын («Жоғарғы сол», «Төменгі оң» т.б.).
**Кеңес:** `pygame.MOUSEBUTTONDOWN` оқиғасы және `event.pos`.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Шахмат тақтасы.** 8×8 тақтаны екі түспен сал. Тақта терезенің ортасында, ұяшық өлшемі терезенің кіші жағына байланысты есептелсін (`min(WIDTH, HEIGHT) // 8`).

**Ә. Кездейсоқ «пиксель-арт».** 20×20 ұяшықтан тұратын торды кездейсоқ түстермен толтыр; `Space` пернесі — жаңа сурет, `S` пернесі — `pygame.image.save(screen, "art.png")` арқылы сақтау.

## Үй тапсырмасы

1. Қазақстан туының түстерін (көгілдір `#00AFCA`, алтын `#FEC50A`) қолданып, «аспан және күн» нобайын тек тіктөртбұрыштармен сал.
2. Дәптерге 1280×720 терезе үшін 5 маңызды нүктенің (орта, 4 бұрыш) координаталарын жаз.
3. Келесі сабаққа: `pygame.draw` модулінде қандай функциялар бар екенін ресми құжаттамадан (pygame.org/docs) қарап кел.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма жауаптары:** 1) `(400, 300)`; 2) `(799, 0)`; 3) `(0, 599)`; 4) `(750, 550)`; 5) `(300, 250)`.

**2–3-тапсырмалар (екі жалау бір бағдарламада, Space арқылы ауысады):**

```python
import pygame

WIDTH, HEIGHT = 600, 400
WHITE = (255, 255, 255)
BLUE = (0, 57, 166)
RED = (213, 43, 30)
GREEN = (0, 146, 70)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Жалаулар — Space басыңыз")
clock = pygame.time.Clock()
vertical = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            vertical = not vertical
    if vertical:
        stripe = WIDTH // 3
        for i, color in enumerate([GREEN, WHITE, RED]):
            screen.fill(color, (i * stripe, 0, stripe, HEIGHT))
    else:
        stripe = HEIGHT // 3
        for i, color in enumerate([WHITE, BLUE, RED]):
            screen.fill(color, (0, i * stripe, WIDTH, stripe))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**4-тапсырма:**

```python
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT), pygame.RESIZABLE)
pygame.display.set_caption("Менің әлемім")
icon = pygame.Surface((32, 32))
icon.fill((0, 180, 0))
pygame.display.set_icon(icon)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.VIDEORESIZE:
            print(f"Жаңа өлшем: {event.w} x {event.h}")
    screen.fill((60, 60, 90))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**5-тапсырма. Палитра:**

```python
import pygame

WIDTH, HEIGHT = 800, 400
NAMES = ["red", "orange", "yellow", "green", "cyan", "blue", "purple", "pink"]
COLS, ROWS, GAP = 4, 2, 5

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Палитра")
cell_w = WIDTH // COLS
cell_h = HEIGHT // ROWS
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((255, 255, 255))
    for i, name in enumerate(NAMES):
        col = i % COLS
        row = i // COLS
        rect = (col * cell_w + GAP, row * cell_h + GAP,
                cell_w - 2 * GAP, cell_h - 2 * GAP)
        screen.fill(pygame.Color(name), rect)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**6-тапсырма. Градиент:**

```python
import pygame

WIDTH, HEIGHT = 400, 400

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Градиент")
for y in range(HEIGHT):
    blue = y * 255 // (HEIGHT - 1)
    screen.fill((0, 0, blue), (0, y, WIDTH, 1))
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**7-тапсырма. Ширектер:**

```python
import pygame

WIDTH, HEIGHT = 600, 400
CX, CY = WIDTH // 2, HEIGHT // 2

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Ширектер")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            x, y = event.pos
            vertical = "Жоғарғы" if y < CY else "Төменгі"
            horizontal = "сол" if x < CX else "оң"
            print(vertical, horizontal, event.pos)
    screen.fill((230, 80, 80), (0, 0, CX, CY))
    screen.fill((80, 200, 80), (CX, 0, WIDTH - CX, CY))
    screen.fill((80, 80, 230), (0, CY, CX, HEIGHT - CY))
    screen.fill((240, 200, 60), (CX, CY, WIDTH - CX, HEIGHT - CY))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ А. Шахмат тақтасы:**

```python
import pygame

WIDTH, HEIGHT = 800, 600
LIGHT = (240, 217, 181)
DARK = (181, 136, 99)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Шахмат тақтасы")
cell = min(WIDTH, HEIGHT) // 8
left = (WIDTH - cell * 8) // 2
top = (HEIGHT - cell * 8) // 2
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((40, 40, 40))
    for row in range(8):
        for col in range(8):
            color = LIGHT if (row + col) % 2 == 0 else DARK
            screen.fill(color, (left + col * cell, top + row * cell, cell, cell))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ Ә. Пиксель-арт:**

```python
import pygame
import random

SIZE, CELL = 20, 20
WIDTH = HEIGHT = SIZE * CELL


def draw_art(surface):
    for row in range(SIZE):
        for col in range(SIZE):
            color = (random.randint(0, 255), random.randint(0, 255),
                     random.randint(0, 255))
            surface.fill(color, (col * CELL, row * CELL, CELL, CELL))


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space — жаңа сурет, S — сақтау")
draw_art(screen)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                draw_art(screen)
            elif event.key == pygame.K_s:
                pygame.image.save(screen, "art.png")
                print("Сақталды: art.png")
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

</details>
