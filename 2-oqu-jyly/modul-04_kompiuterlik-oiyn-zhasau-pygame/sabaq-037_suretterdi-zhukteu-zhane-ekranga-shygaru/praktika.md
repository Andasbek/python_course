# Сабақ 37. Практикалық тапсырмалар: суреттер, Surface, blit

> Дайындық: жоба папкасында `assets/images` құр. Kenney.nl сайтынан (немесе мұғалім берген архивтен) фон және 2–3 кейіпкер/зат суретін (PNG) сал. Сурет болмаса да, `load_image()` функциясы түсті шаршы көрсетеді — бағдарлама бәрібір жұмыс істейді.

## Базалық деңгей

### 1-тапсырма. Өз Surface-ың
200×120 өлшемді Surface құр: ақ фон, үстінде көк жиек (`draw.rect`, `width=4`) және ортасында жасыл дөңгелек. Оны терезенің төрт бұрышына blit жаса.

### 2-тапсырма. Фон және кейіпкер
`load_image()` функциясын теориядан көшіріп ал. Терезе 800×600:
- фонды бүкіл терезеге созып шығар (`convert()`);
- кейіпкерді (100×100) төменгі ортаға (`midbottom`) шығар (`convert_alpha()`).

### 3-тапсырма. Кейіпкерлер қатары
Бір суретті (60×60) терезенің жоғарғы бөлігіне бір қатарға 8 рет, бірдей аралықпен шығар. Цикл қолдан.

### 4-тапсырма. Айна
Кейіпкерді терезенің сол жақ жартысына, ал оның айнадағы бейнесін (`flip`) оң жақ жартысына симметриялы шығар. Ортаға тік сызық сыз.

## Кеңейтілген деңгей

### 5-тапсырма. Айналатын жұлдыз
Жұлдыз (немесе тиын) суреті терезенің ортасында бір орында айналсын. Түпнұсқадан бұруды және ортасы бойынша `get_rect(center=...)` қолдануды ұмытпа.

### 6-тапсырма. Пульсация
Сурет өлшемі біртіндеп 50-ден 150 пиксельге дейін үлкейіп, қайта кішірейіп тұрсын («тыныс алу» эффекті). Ортасы әрқашан бір нүктеде қалсын.

### 7-тапсырма. Жартылай мөлдір терезе
Ойын фонының үстіне жартылай мөлдір қара панель (`Surface` + `set_alpha(150)`) шығарып, оның ортасына кейіпкер суретін қой. Бұл — болашақ «Пауза» экранының негізі.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Параллакс-фон.** Екі қабатты фон: алыстағы таулар баяу (1 пиксель/кадр), жақын ағаштар тез (3 пиксель/кадр) солға жылжиды. Фон шексіз болуы үшін әр қабатты екі рет қатар blit жаса.

**Ә. Сурет галереясы.** `assets/images` папкасындағы барлық PNG файлдарды (`os.listdir`) автоматты түрде жүктеп, торға (4 бағанды) кішірейтіп шығар. Әр суреттің астына файл атауын жазу — 42-сабақтан кейін қосуға болады.

## Үй тапсырмасы

1. Болашақ командалық ойыныңа арналған ресурстар жинағын (фон, кейіпкер, жау, бонус) тауып, `assets/images` папкасына сал. README-ге ресурстар көзі мен лицензиясын жаз.
2. 2-тапсырманы өз ресурстарыңмен аяқта.
3. Ойлан: кейіпкерді пернетақтамен жылжыту үшін қай айнымалыны өзгерту керек?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

Төмендегі шешімдердің бәрі `load_image()` функциясын қолданады (теориядағы 5-бөлім). Қысқалық үшін ол әр шешімнің басында қайталанады.

**1-тапсырма:**

```python
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Өз Surface-ым")
card = pygame.Surface((200, 120))
card.fill((255, 255, 255))
pygame.draw.rect(card, (0, 0, 200), card.get_rect(), 4)
pygame.draw.circle(card, (0, 170, 0), (100, 60), 35)
corners = ["topleft", "topright", "bottomleft", "bottomright"]
screen_rect = screen.get_rect()
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((50, 50, 50))
    for corner in corners:
        rect = card.get_rect(**{corner: getattr(screen_rect, corner)})
        screen.blit(card, rect)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

(Қарапайым нұсқа: төрт `blit` жолы — `(0, 0)`, `(600, 0)`, `(0, 480)`, `(600, 480)`.)

**2–4-тапсырмалар (бір бағдарламада):**

```python
import os
import pygame

WIDTH, HEIGHT = 800, 600
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")


def load_image(name, size=None, alpha=True):
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
pygame.display.set_caption("Фон және кейіпкерлер")
background = load_image("background.jpg", (WIDTH, HEIGHT), alpha=False)
hero = load_image("player.png", (100, 100))
hero_mirror = pygame.transform.flip(hero, True, False)
small = load_image("player.png", (60, 60))
gap = (WIDTH - 8 * 60) // 9

clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.blit(background, (0, 0))
    for i in range(8):                                      # 3-тапсырма
        screen.blit(small, (gap + i * (60 + gap), 20))
    screen.blit(hero, hero.get_rect(midbottom=(WIDTH // 2, HEIGHT)))   # 2-тапсырма
    pygame.draw.line(screen, (255, 255, 255), (WIDTH // 2, 120), (WIDTH // 2, 400), 2)
    screen.blit(hero, hero.get_rect(center=(WIDTH // 4, 260)))         # 4-тапсырма
    screen.blit(hero_mirror, hero_mirror.get_rect(center=(3 * WIDTH // 4, 260)))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**5–6-тапсырмалар (айналу және пульсация):**

```python
import pygame

WIDTH, HEIGHT = 600, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Айналу және пульсация")
original = pygame.Surface((100, 100), pygame.SRCALPHA)   # мөлдір фонды бет
pygame.draw.polygon(original, (255, 215, 0),
                    [(50, 0), (62, 38), (100, 38), (69, 61), (81, 100),
                     (50, 76), (19, 100), (31, 61), (0, 38), (38, 38)])
angle = 0
size = 50
grow = 2
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    angle = (angle + 2) % 360
    size += grow
    if size >= 150 or size <= 50:
        grow = -grow
    spinning = pygame.transform.rotate(original, angle)
    pulsing = pygame.transform.scale(original, (size, size))
    screen.fill((20, 20, 60))
    screen.blit(spinning, spinning.get_rect(center=(WIDTH // 4, HEIGHT // 2)))
    screen.blit(pulsing, pulsing.get_rect(center=(3 * WIDTH // 4, HEIGHT // 2)))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`pygame.SRCALPHA` — мөлдір фоны бар Surface құру жалауы (сурет файлы жоқ кезде ыңғайлы).

**7-тапсырма. Жартылай мөлдір панель:**

```python
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Пауза панелі")
panel = pygame.Surface((400, 250))
panel.fill((0, 0, 0))
panel.set_alpha(150)
panel_rect = panel.get_rect(center=(WIDTH // 2, HEIGHT // 2))
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((100, 180, 255))
    pygame.draw.rect(screen, (40, 160, 40), (0, 450, WIDTH, 150))
    pygame.draw.circle(screen, (255, 220, 0), (650, 100), 60)
    screen.blit(panel, panel_rect)
    pygame.draw.circle(screen, (255, 80, 80), panel_rect.center, 40)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ А. Параллакс:**

```python
import pygame

WIDTH, HEIGHT = 800, 400

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Параллакс")

far = pygame.Surface((WIDTH, HEIGHT), pygame.SRCALPHA)
for x in range(0, WIDTH, 200):
    pygame.draw.polygon(far, (110, 110, 160), [(x, 300), (x + 100, 120), (x + 200, 300)])
near = pygame.Surface((WIDTH, HEIGHT), pygame.SRCALPHA)
for x in range(0, WIDTH, 100):
    pygame.draw.rect(near, (90, 60, 30), (x + 45, 300, 10, 60))
    pygame.draw.circle(near, (30, 130, 30), (x + 50, 290), 30)

far_x = 0
near_x = 0
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    far_x = (far_x - 1) % WIDTH
    near_x = (near_x - 3) % WIDTH
    screen.fill((180, 220, 255))
    screen.blit(far, (far_x - WIDTH, 0))
    screen.blit(far, (far_x, 0))
    pygame.draw.rect(screen, (60, 160, 60), (0, 350, WIDTH, 50))
    screen.blit(near, (near_x - WIDTH, 0))
    screen.blit(near, (near_x, 0))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**★ Ә. Галерея:**

```python
import os
import pygame

WIDTH, HEIGHT = 800, 600
COLS, THUMB, GAP = 4, 150, 40
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Галерея")
thumbs = []
if os.path.isdir(IMG_DIR):
    for name in sorted(os.listdir(IMG_DIR)):
        if name.lower().endswith(".png"):
            img = pygame.image.load(os.path.join(IMG_DIR, name)).convert_alpha()
            thumbs.append(pygame.transform.smoothscale(img, (THUMB, THUMB)))
print("Жүктелген суреттер:", len(thumbs))
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((30, 30, 30))
    for i, img in enumerate(thumbs):
        x = GAP + (i % COLS) * (THUMB + GAP)
        y = GAP + (i // COLS) * (THUMB + GAP)
        screen.blit(img, (x, y))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

</details>
