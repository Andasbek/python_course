# Сабақ 36. Практикалық тапсырмалар: геометриялық фигуралар

> Әр тапсырманы жеке файлда орында (`task1.py`, `task2.py` ...). Түстерді тұрақтылар ретінде жаз. Фигуралардың орнын мүмкіндігінше терезе өлшеміне (`WIDTH`, `HEIGHT`) немесе басқа `Rect`-ке қатысты есепте.

## Базалық деңгей

### 1-тапсырма. Бағдаршам
400×600 терезеде қара тіктөртбұрыш (бұрыштары дөңгелек) ішінде үш дөңгелек сал: қызыл, сары, жасыл. Дөңгелектер тік бір сызықта, бірдей қашықтықта болсын.

### 2-тапсырма. Қардан адам
Үш ақ дөңгелек (төменнен жоғары кішірейеді), қара көздер, сәбіз-мұрын (қызғылт сары үшбұрыш), қолдар (қоңыр сызықтар), шелек-қалпақ (тіктөртбұрыш). Фон — қысқы көк аспан және ақ қар.

### 3-тапсырма. Тор
600×600 терезеге 10×10 тор сыз (сұр сызықтар). Ортасы арқылы өтетін екі осьті (X пен Y) қалың қызыл сызықпен белгіле.

### 4-тапсырма. Rect атрибуттары
`pygame.Rect` арқылы 120×80 тіктөртбұрыш құрып, оны кезекпен терезенің (800×600) төрт бұрышына және ортасына орналастырып сал (бес тіктөртбұрыш). Тек `topleft`, `topright`, `bottomleft`, `bottomright`, `center` атрибуттарын қолдан.

## Кеңейтілген деңгей

### 5-тапсырма. Киіз үй
Қазақтың киіз үйін сал: ақ тіктөртбұрыш — керегесі, күмбез-шаңырақ — эллипс немесе доға, есігі — қызыл тіктөртбұрыш, керегенің ромб тәрізді өрнегі — қиғаш сызықтар. Фонда — тау (үшбұрыштар) және дала.

### 6-тапсырма. Жұлдыз
Ортасы `(300, 300)`, сыртқы радиусы 150, ішкі радиусы 60 бес бұрышты жұлдызды `polygon` арқылы сал.
**Кеңес:** 10 нүкте; `i`-нүктенің бұрышы `math.radians(-90 + i * 36)`, радиусы жұп `i` үшін сыртқы, тақ үшін ішкі; координаталар: `x = cx + r * cos(a)`, `y = cy + r * sin(a)`.

### 7-тапсырма. Секіретін доп
Радиусы 25 доп терезе шекараларынан «шағылысып» қозғалсын.
**Кеңес:** `x += dx`, `y += dy`; егер `x - r < 0` немесе `x + r > WIDTH` болса, `dx = -dx`.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Аналогтық сағат.** Шынайы уақытты көрсететін сағат сал: дөңгелек циферблат, 12 белгі (сызықтар), сағат, минут және секунд тілдері (`datetime.now()` және тригонометрия). Тілдер әр секунд сайын жаңарып тұрсын.

**Ә. «Менің кейіпкерім».** Тек фигуралардан болашақ ойыныңның кейіпкерін сал. Барлық бөліктері бір `Rect`-ке (`body`) қатысты орналассын, яғни `body.center` өзгергенде кейіпкер тұтасымен жылжиды.

## Үй тапсырмасы

1. «Менің аулам» суретін сал: кемінде 6 түрлі `draw` функциясы, 15 фигура.
2. Суретті `pygame.image.save(screen, "aula.png")` арқылы сақтап, кодымен бірге GitHub-қа жүкте.
3. Келесі сабаққа: интернеттен ойынға арналған тегін PNG сурет (кейіпкер, фон) тауып кел. Ұсынылатын сайт: kenney.nl (лицензиясы CC0).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма. Бағдаршам:**

```python
import pygame

WIDTH, HEIGHT = 400, 600
COLORS = [(230, 0, 0), (240, 200, 0), (0, 190, 0)]

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Бағдаршам")
body = pygame.Rect(0, 0, 160, 440)
body.center = (WIDTH // 2, HEIGHT // 2)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((200, 220, 255))
    pygame.draw.rect(screen, (30, 30, 30), body, border_radius=25)
    for i, color in enumerate(COLORS):
        cy = body.top + 80 + i * 140
        pygame.draw.circle(screen, color, (body.centerx, cy), 55)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**2-тапсырма. Қардан адам:**

```python
import pygame

WIDTH, HEIGHT = 600, 600
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Қардан адам")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((100, 150, 220))
    pygame.draw.rect(screen, WHITE, (0, 480, WIDTH, 120))
    cx = WIDTH // 2
    pygame.draw.circle(screen, WHITE, (cx, 430), 90)
    pygame.draw.circle(screen, WHITE, (cx, 290), 65)
    pygame.draw.circle(screen, WHITE, (cx, 185), 45)
    pygame.draw.circle(screen, BLACK, (cx - 15, 175), 6)
    pygame.draw.circle(screen, BLACK, (cx + 15, 175), 6)
    pygame.draw.polygon(screen, (255, 140, 0), [(cx, 185), (cx + 45, 195), (cx, 200)])
    pygame.draw.line(screen, (110, 70, 30), (cx - 60, 280), (cx - 140, 220), 6)
    pygame.draw.line(screen, (110, 70, 30), (cx + 60, 280), (cx + 140, 220), 6)
    pygame.draw.rect(screen, (60, 60, 60), (cx - 35, 105, 70, 45))
    for y in (270, 300, 330):
        pygame.draw.circle(screen, BLACK, (cx, y), 6)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**3-тапсырма. Тор:**

```python
import pygame

SIZE = 600
STEP = SIZE // 10

pygame.init()
screen = pygame.display.set_mode((SIZE, SIZE))
pygame.display.set_caption("Тор")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((255, 255, 255))
    for i in range(0, SIZE + 1, STEP):
        pygame.draw.line(screen, (180, 180, 180), (i, 0), (i, SIZE))
        pygame.draw.line(screen, (180, 180, 180), (0, i), (SIZE, i))
    pygame.draw.line(screen, (220, 0, 0), (SIZE // 2, 0), (SIZE // 2, SIZE), 3)
    pygame.draw.line(screen, (220, 0, 0), (0, SIZE // 2), (SIZE, SIZE // 2), 3)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**4-тапсырма. Rect атрибуттары:**

```python
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Rect атрибуттары")
positions = {
    "topleft": (0, 0),
    "topright": (WIDTH, 0),
    "bottomleft": (0, HEIGHT),
    "bottomright": (WIDTH, HEIGHT),
    "center": (WIDTH // 2, HEIGHT // 2),
}
rects = []
for attr, point in positions.items():
    r = pygame.Rect(0, 0, 120, 80)
    setattr(r, attr, point)
    rects.append(r)
    print(attr, r)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((30, 30, 30))
    for r in rects:
        pygame.draw.rect(screen, (0, 200, 200), r, border_radius=10)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

Консольдегі нәтиже:
```text
topleft <rect(0, 0, 120, 80)>
topright <rect(680, 0, 120, 80)>
bottomleft <rect(0, 520, 120, 80)>
bottomright <rect(680, 520, 120, 80)>
center <rect(340, 260, 120, 80)>
```
(`setattr` орнына оқушылар бес бөлек жол жаза алады: `r1.topleft = (0, 0)` т.б.)

**5-тапсырма. Киіз үй:**

```python
import math
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Киіз үй")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((150, 200, 240))
    pygame.draw.polygon(screen, (120, 120, 140), [(0, 420), (180, 180), (360, 420)])
    pygame.draw.polygon(screen, (100, 100, 120), [(250, 420), (480, 140), (720, 420)])
    pygame.draw.polygon(screen, (255, 255, 255), [(430, 200), (480, 140), (530, 200)])
    pygame.draw.rect(screen, (190, 170, 90), (0, 420, WIDTH, 180))
    wall = pygame.Rect(250, 330, 300, 150)
    pygame.draw.rect(screen, (245, 240, 225), wall)
    for x in range(wall.left, wall.right, 30):            # кереге өрнегі
        pygame.draw.line(screen, (170, 120, 70), (x, wall.top), (x + 30, wall.bottom), 2)
        pygame.draw.line(screen, (170, 120, 70), (x + 30, wall.top), (x, wall.bottom), 2)
    dome = pygame.Rect(wall.left, wall.top - 110, wall.width, 220)
    pygame.draw.ellipse(screen, (245, 240, 225), dome)
    pygame.draw.rect(screen, (245, 240, 225), (wall.left, wall.top, wall.width, 10))
    pygame.draw.arc(screen, (170, 120, 70), dome, 0, math.pi, 3)
    pygame.draw.circle(screen, (170, 120, 70), (wall.centerx, dome.top + 8), 18, 4)  # шаңырақ
    door = pygame.Rect(0, 0, 60, 90)
    door.midbottom = wall.midbottom
    pygame.draw.rect(screen, (200, 30, 30), door)
    pygame.draw.rect(screen, (240, 200, 0), door, 3)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**6-тапсырма. Жұлдыз:**

```python
import math
import pygame


def star_points(cx, cy, outer, inner):
    points = []
    for i in range(10):
        angle = math.radians(-90 + i * 36)
        r = outer if i % 2 == 0 else inner
        points.append((cx + r * math.cos(angle), cy + r * math.sin(angle)))
    return points


pygame.init()
screen = pygame.display.set_mode((600, 600))
pygame.display.set_caption("Жұлдыз")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((10, 10, 50))
    pygame.draw.polygon(screen, (255, 215, 0), star_points(300, 300, 150, 60))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**7-тапсырма. Секіретін доп:**

```python
import pygame

WIDTH, HEIGHT = 800, 600
R = 25

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Секіретін доп")
clock = pygame.time.Clock()
x, y = 100, 100
dx, dy = 5, 4
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    x += dx
    y += dy
    if x - R < 0 or x + R > WIDTH:
        dx = -dx
    if y - R < 0 or y + R > HEIGHT:
        dy = -dy
    screen.fill((20, 20, 20))
    pygame.draw.circle(screen, (255, 80, 80), (x, y), R)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**★ А. Аналогтық сағат:**

```python
import math
from datetime import datetime
import pygame

SIZE = 500
C = SIZE // 2
R = 200


def hand(surface, angle_deg, length, width, color):
    a = math.radians(angle_deg - 90)
    end = (C + length * math.cos(a), C + length * math.sin(a))
    pygame.draw.line(surface, color, (C, C), end, width)


pygame.init()
screen = pygame.display.set_mode((SIZE, SIZE))
pygame.display.set_caption("Сағат")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    now = datetime.now()
    screen.fill((240, 240, 240))
    pygame.draw.circle(screen, (255, 255, 255), (C, C), R)
    pygame.draw.circle(screen, (0, 0, 0), (C, C), R, 4)
    for i in range(12):
        a = math.radians(i * 30)
        p1 = (C + (R - 20) * math.cos(a), C + (R - 20) * math.sin(a))
        p2 = (C + (R - 5) * math.cos(a), C + (R - 5) * math.sin(a))
        pygame.draw.line(screen, (0, 0, 0), p1, p2, 4)
    hand(screen, (now.hour % 12) * 30 + now.minute * 0.5, 100, 8, (0, 0, 0))
    hand(screen, now.minute * 6 + now.second * 0.1, 150, 5, (40, 40, 40))
    hand(screen, now.second * 6, 170, 2, (220, 0, 0))
    pygame.draw.circle(screen, (220, 0, 0), (C, C), 8)
    pygame.display.flip()
    clock.tick(10)
pygame.quit()
```

**★ Ә.** Шығармашылық тапсырма; тексеру критерийі — `body.center`-ді өзгерткенде барлық бөлік бірге жылжиды ма.

</details>
