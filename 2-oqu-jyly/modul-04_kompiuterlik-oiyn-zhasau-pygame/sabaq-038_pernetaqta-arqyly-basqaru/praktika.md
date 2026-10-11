# Сабақ 38. Практикалық тапсырмалар: пернетақтамен басқару

## Базалық деңгей

### 1-тапсырма. Перне детекторы
Басылған перненің атауын терезе тақырыбына шығаратын бағдарлама жаз: `Соңғы перне: space`. `Escape` басылса — бағдарлама жабылады.

### 2-тапсырма. Басқарылатын кейіпкер
800×600 терезеде 50×50 шаршы (немесе 37-сабақтағы кейіпкер суреті) бағыттауыштармен және WASD пернелерімен төрт бағытта жылжысын. Жылдамдығы — 5 пиксель/кадр. Кейіпкер экраннан шықпауы керек.

### 3-тапсырма. Түс ауыстыру
2-тапсырмадағы кейіпкердің түсі `1`, `2`, `3` пернелерін басқанда ауыссын (қызыл, жасыл, көк). Бұл — бір реттік әрекет, қандай тәсілді қолданасың?

### 4-тапсырма. Жүгіру
`Shift` пернесі басылып тұрғанда кейіпкер екі есе жылдам жүрсін. Тақырыпта ағымдағы жылдамдық көрсетілсін.

## Кеңейтілген деңгей

### 5-тапсырма. Екі ойыншы
Бір экранда екі кейіпкер: біріншісі — WASD, екіншісі — бағыттауыштар. Әрқайсысы өз түсінде, экраннан шықпайды.

### 6-тапсырма. Секіретін кейіпкер
Теориядағы секіру мысалын кеңейт:
- `Space` — секіру (тек жерде тұрғанда);
- экранда екі платформа (тіктөртбұрыш) бар; кейіпкер жоғарыдан түскенде оларға тұра алады.
**Кеңес:** кейіпкер төмен түсіп келе жатса (`vel_y > 0`) және оның `bottom`-ы платформаның `top`-ынан сәл төмен болса және көлденеңінен қиылысса — `player.bottom = platform.top`.

### 7-тапсырма. Торлы лабиринт
10×10 тор. Кейіпкер `KEYDOWN` арқылы бір ұяшыққа жылжиды. Бірнеше ұяшық — қабырға (қара), оларға кіруге болмайды. Жасыл ұяшыққа жеткенде консольге «Жеңіс!» шықсын.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Инерциясы бар ғарыш кемесі.** Кеме ← → пернелерімен бұрылады, ↑ пернесі оны қараған бағытында жылдамдатады (үдеу), перне жіберілсе, кеме біртіндеп баяулайды (үйкеліс 0.98). Экран шетінен шыққанда қарсы жақтан кіреді. Кеме — үшбұрыш (`polygon`), бұрышын `math.cos/sin` арқылы есепте.

**Ә. Комбо-код.** Ойыншы ↑ ↑ ↓ ↓ ← → ← → тізбегін енгізсе, экран түсі өзгеріп, «Құпия режим!» деген хабар консольге шықсын. Соңғы 8 перне тізімде сақталады.

## Үй тапсырмасы

1. Болашақ командалық ойыныңның кейіпкері қандай пернелермен басқарылатынын кесте түрінде жаз (әрекет — перне — тәсіл: оқиға/күй).
2. 6-тапсырманы аяқтап, GitHub-қа жүкте.
3. Тінтуірмен басқарылатын қандай ойындарды білесің? 3 мысал жазып кел.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма:**

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((500, 200))
pygame.display.set_caption("Перне басыңыз")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_ESCAPE:
                running = False
            else:
                pygame.display.set_caption(f"Соңғы перне: {pygame.key.name(event.key)}")
    screen.fill((20, 20, 20))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**2–4-тапсырмалар:**

```python
import pygame

WIDTH, HEIGHT = 800, 600
BASE_SPEED = 5
COLORS = {pygame.K_1: (220, 50, 50), pygame.K_2: (50, 200, 50), pygame.K_3: (50, 100, 230)}

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
player = pygame.Rect(0, 0, 50, 50)
player.center = (WIDTH // 2, HEIGHT // 2)
color = (220, 50, 50)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key in COLORS:   # 3: бір реттік — оқиға
            color = COLORS[event.key]
    keys = pygame.key.get_pressed()
    speed = BASE_SPEED * 2 if keys[pygame.K_LSHIFT] or keys[pygame.K_RSHIFT] else BASE_SPEED
    if keys[pygame.K_LEFT] or keys[pygame.K_a]:
        player.x -= speed
    if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
        player.x += speed
    if keys[pygame.K_UP] or keys[pygame.K_w]:
        player.y -= speed
    if keys[pygame.K_DOWN] or keys[pygame.K_s]:
        player.y += speed
    player.clamp_ip(screen.get_rect())
    pygame.display.set_caption(f"Жылдамдық: {speed}")
    screen.fill((30, 30, 30))
    pygame.draw.rect(screen, color, player)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**5-тапсырма. Екі ойыншы:**

```python
import pygame

WIDTH, HEIGHT = 800, 600
SPEED = 5
CONTROLS = [
    {"left": pygame.K_a, "right": pygame.K_d, "up": pygame.K_w, "down": pygame.K_s},
    {"left": pygame.K_LEFT, "right": pygame.K_RIGHT, "up": pygame.K_UP, "down": pygame.K_DOWN},
]

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Екі ойыншы")
clock = pygame.time.Clock()
players = [pygame.Rect(100, 275, 50, 50), pygame.Rect(650, 275, 50, 50)]
colors = [(255, 100, 100), (100, 150, 255)]
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    for rect, ctrl in zip(players, CONTROLS):
        if keys[ctrl["left"]]:
            rect.x -= SPEED
        if keys[ctrl["right"]]:
            rect.x += SPEED
        if keys[ctrl["up"]]:
            rect.y -= SPEED
        if keys[ctrl["down"]]:
            rect.y += SPEED
        rect.clamp_ip(screen.get_rect())
    screen.fill((25, 25, 25))
    for rect, col in zip(players, colors):
        pygame.draw.rect(screen, col, rect)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**6-тапсырма. Платформалар:**

```python
import pygame

WIDTH, HEIGHT = 800, 500
GROUND = 450
GRAVITY = 0.8
JUMP = -16
SPEED = 5

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Платформалар")
clock = pygame.time.Clock()
platforms = [pygame.Rect(200, 340, 160, 20), pygame.Rect(450, 230, 160, 20),
             pygame.Rect(0, GROUND, WIDTH, HEIGHT - GROUND)]
player = pygame.Rect(50, 0, 40, 60)
player.bottom = GROUND
vel_y = 0.0
on_ground = True
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE and on_ground:
            vel_y = JUMP
            on_ground = False
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player.x -= SPEED
    if keys[pygame.K_RIGHT]:
        player.x += SPEED

    vel_y += GRAVITY
    old_bottom = player.bottom
    player.y += int(vel_y)
    on_ground = False
    for plat in platforms:
        overlap_x = player.right > plat.left and player.left < plat.right
        if vel_y >= 0 and overlap_x and old_bottom <= plat.top <= player.bottom:
            player.bottom = plat.top
            vel_y = 0
            on_ground = True
    player.clamp_ip(screen.get_rect())

    screen.fill((135, 206, 235))
    for plat in platforms:
        pygame.draw.rect(screen, (90, 60, 30), plat)
    pygame.draw.rect(screen, (220, 40, 40), player)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`old_bottom <= plat.top <= player.bottom` — кейіпкер осы кадрда платформаның үстіңгі сызығын жоғарыдан төмен **кесіп өтті** ме, соны тексереді. Төменнен секіргенде (`vel_y < 0`) платформа «өткізеді».

**7-тапсырма. Лабиринт:**

```python
import pygame

CELL = 50
MAZE = [
    "S...#.....",
    ".##.#.###.",
    ".#..#...#.",
    ".#.####.#.",
    ".#......#.",
    ".######.#.",
    "......#.#.",
    ".####.#.#.",
    ".#....#...",
    ".#.####.#F",
]
SIZE = len(MAZE) * CELL

pygame.init()
screen = pygame.display.set_mode((SIZE, SIZE))
pygame.display.set_caption("Лабиринт")
clock = pygame.time.Clock()
col, row = 0, 0
moves = {pygame.K_LEFT: (-1, 0), pygame.K_RIGHT: (1, 0),
         pygame.K_UP: (0, -1), pygame.K_DOWN: (0, 1)}
won = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key in moves and not won:
            dc, dr = moves[event.key]
            nc, nr = col + dc, row + dr
            if 0 <= nc < 10 and 0 <= nr < 10 and MAZE[nr][nc] != "#":
                col, row = nc, nr
                if MAZE[row][col] == "F":
                    print("Жеңіс!")
                    won = True
    screen.fill((255, 255, 255))
    for r, line in enumerate(MAZE):
        for c, ch in enumerate(line):
            if ch == "#":
                pygame.draw.rect(screen, (0, 0, 0), (c * CELL, r * CELL, CELL, CELL))
            elif ch == "F":
                pygame.draw.rect(screen, (0, 200, 0), (c * CELL, r * CELL, CELL, CELL))
    pygame.draw.circle(screen, (220, 0, 0),
                       (col * CELL + CELL // 2, row * CELL + CELL // 2), CELL // 3)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ А. Ғарыш кемесі:**

```python
import math
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Ғарыш кемесі")
clock = pygame.time.Clock()
x, y = WIDTH / 2, HEIGHT / 2
vx, vy = 0.0, 0.0
angle = -90.0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        angle -= 4
    if keys[pygame.K_RIGHT]:
        angle += 4
    rad = math.radians(angle)
    if keys[pygame.K_UP]:
        vx += 0.2 * math.cos(rad)
        vy += 0.2 * math.sin(rad)
    vx *= 0.98
    vy *= 0.98
    x = (x + vx) % WIDTH
    y = (y + vy) % HEIGHT
    nose = (x + 20 * math.cos(rad), y + 20 * math.sin(rad))
    left = (x + 12 * math.cos(rad + 2.5), y + 12 * math.sin(rad + 2.5))
    right = (x + 12 * math.cos(rad - 2.5), y + 12 * math.sin(rad - 2.5))
    screen.fill((0, 0, 20))
    pygame.draw.polygon(screen, (220, 220, 255), [nose, left, right])
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**★ Ә. Комбо-код:**

```python
import pygame

CODE = [pygame.K_UP, pygame.K_UP, pygame.K_DOWN, pygame.K_DOWN,
        pygame.K_LEFT, pygame.K_RIGHT, pygame.K_LEFT, pygame.K_RIGHT]

pygame.init()
screen = pygame.display.set_mode((400, 300))
clock = pygame.time.Clock()
history = []
bg = (30, 30, 30)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            history.append(event.key)
            history = history[-8:]
            if history == CODE:
                print("Құпия режим!")
                bg = (120, 0, 160)
    screen.fill(bg)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

</details>
