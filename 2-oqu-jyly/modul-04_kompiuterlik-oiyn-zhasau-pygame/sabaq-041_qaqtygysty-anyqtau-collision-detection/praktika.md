# Сабақ 41. Практикалық тапсырмалар: қақтығысты анықтау

## Базалық деңгей

### 1-тапсырма. Қиылысу детекторы
Екі тіктөртбұрыш: біреуі тұрақты (ортада), екіншісі тінтуірге ереді (`center = mouse.get_pos()`). Қиылысқанда екеуі де қызыл, әйтпесе жасыл болсын. Тексерісті алдымен **өз** `rects_overlap()` функцияңмен, сосын `colliderect()` арқылы жаз — нәтижелері бірдей ме?

### 2-тапсырма. Тиын жинаушы
Ойыншы (бағыттауыштармен) экранда кездейсоқ орналасқан 15 тиынды жинайды (`Coin` спрайттары, `spritecollide(..., True)`). Тақырыпта: `Тиындар: 4 / 15`. Барлығы жиналғанда — «Жеңіс!».

### 3-тапсырма. «Ғарыш қорғаушысына» қақтығыстар
40-сабақтағы жобаңа қос:
- оқ пен метеорит қақтығысы — екеуі де жойылады, +10 ұпай (`groupcollide`);
- метеорит пен кеме — метеорит жойылады, −1 өмір; өмір 0 болса — ойын аяқталады;
- ұпай мен өмір тақырыпта көрсетіледі.

### 4-тапсырма. Қабырғалар
Экранда 5 қабырға (тіктөртбұрыш). Кейіпкер оларды аралап жүреді, бірақ ішінен өтпейді (`move_with_walls` функциясы, теорияның 7-бөлімі).

## Кеңейтілген деңгей

### 5-тапсырма. Дәлдікті салыстыру
3-тапсырмадағы метеорит–кеме қақтығысын үш тәсілмен тексер: `collide_rect`, `collide_circle`, `collide_mask`. Метеориттерге `self.radius` және `self.mask` атрибуттарын қос. Қайсысы «әділірек»? Тәжірибе нәтижесін 2–3 сөйлеммен жаз.

### 6-тапсырма. Қорғаныс уақыты
Кеме соқтығысқаннан кейін 1,5 секунд «қол жетпейтін» болсын: бұл уақытта ол жыпылықтайды (кадр сайын көрініп-жоғалады) және өмір азаймайды.
**Кеңес:** `self.invincible_until = pygame.time.get_ticks() + 1500`; жыпылықтау үшін `self.image.set_alpha(...)`.

### 7-тапсырма. Arkanoid (кірпіш бұзу)
- Төменде ← → басқарылатын тақтайша;
- доп қабырғалардан, тақтайшадан шағылысады;
- жоғарыда 5×8 кірпіштер; доп тиген кірпіш жойылады, доп кері шағылысады;
- доп төмен түсіп кетсе — ойын аяқталады.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Тақтайшадан «ақылды» шағылысу.** Arkanoid-та доп тақтайшаның қай жеріне тисе, сол жерге байланысты бұрышпен ұшсын: ортасына — тік жоғары, шетіне — қиғаш. Формула: `dx = (ball.centerx - paddle.centerx) / (paddle.width / 2) * MAX_DX`.

**Ә. Жылан.** Торлы «Жылан» ойыны: жылан алманы жегенде ұзарады; басы өз денесіне немесе қабырғаға тисе — ойын аяқталады. Денені `Rect` тізімі ретінде сақта, басының қақтығысын `collidelist()` арқылы тексер.

## Үй тапсырмасы

1. 7-тапсырманы (Arkanoid) аяқтап, GitHub-қа жүкте.
2. Командалық жобаң үшін «қақтығыстар картасын» жаса: қай объект қай объектімен соқтығысады, қандай функциямен тексеріледі, жауабы қандай (кесте).
3. Ұпай мен өмірді экранға мәтін етіп қалай шығаруға болады? `pygame.font` модулін құжаттамадан қарап кел.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма:**

```python
import pygame


def rects_overlap(a, b):
    return a.x < b.x + b.w and a.x + a.w > b.x and a.y < b.y + b.h and a.y + a.h > b.y


pygame.init()
screen = pygame.display.set_mode((600, 400))
clock = pygame.time.Clock()
fixed = pygame.Rect(0, 0, 150, 100)
fixed.center = (300, 200)
moving = pygame.Rect(0, 0, 80, 80)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    moving.center = pygame.mouse.get_pos()
    mine = rects_overlap(fixed, moving)
    builtin = fixed.colliderect(moving)
    pygame.display.set_caption(f"Менікі: {mine} | colliderect: {builtin}")
    color = (220, 40, 40) if builtin else (40, 200, 40)
    screen.fill((20, 20, 20))
    pygame.draw.rect(screen, color, fixed)
    pygame.draw.rect(screen, color, moving, 3)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**2-тапсырма. Тиын жинаушы:**

```python
import random
import pygame

WIDTH, HEIGHT = 800, 600
TOTAL = 15


class Coin(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((24, 24), pygame.SRCALPHA)
        pygame.draw.circle(self.image, (255, 215, 0), (12, 12), 12)
        self.rect = self.image.get_rect(
            center=(random.randint(20, WIDTH - 20), random.randint(20, HEIGHT - 20)))


class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((40, 40))
        self.image.fill((0, 150, 255))
        self.rect = self.image.get_rect(center=(WIDTH // 2, HEIGHT // 2))

    def update(self):
        keys = pygame.key.get_pressed()
        self.rect.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 5
        self.rect.y += (keys[pygame.K_DOWN] - keys[pygame.K_UP]) * 5
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
player = Player()
coins = pygame.sprite.Group(Coin() for _ in range(TOTAL))
players = pygame.sprite.Group(player)
collected = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    players.update()
    collected += len(pygame.sprite.spritecollide(player, coins, True))
    pygame.display.set_caption(f"Тиындар: {collected} / {TOTAL}")
    if collected == TOTAL:
        print("Жеңіс!")
        running = False
    screen.fill((30, 90, 30))
    coins.draw(screen)
    players.draw(screen)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`keys[K_RIGHT] - keys[K_LEFT]` — `True`/`False` сандай есептеледі (1/0), сондықтан нәтиже −1, 0 немесе 1.

**3-тапсырма** — теориядағы толық мысал (4-бөлім).

**4-тапсырма. Қабырғалар:**

```python
import pygame

WIDTH, HEIGHT = 800, 600
SPEED = 5


def move_with_walls(rect, dx, dy, walls):
    rect.x += dx
    for wall in walls:
        if rect.colliderect(wall):
            if dx > 0:
                rect.right = wall.left
            elif dx < 0:
                rect.left = wall.right
    rect.y += dy
    for wall in walls:
        if rect.colliderect(wall):
            if dy > 0:
                rect.bottom = wall.top
            elif dy < 0:
                rect.top = wall.bottom


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Қабырғалар")
clock = pygame.time.Clock()
walls = [pygame.Rect(150, 0, 30, 400), pygame.Rect(330, 200, 30, 400),
         pygame.Rect(500, 0, 30, 350), pygame.Rect(530, 320, 150, 30),
         pygame.Rect(0, 560, 120, 40)]
player = pygame.Rect(40, 40, 30, 30)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    dx = (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * SPEED
    dy = (keys[pygame.K_DOWN] - keys[pygame.K_UP]) * SPEED
    move_with_walls(player, dx, dy, walls)
    player.clamp_ip(screen.get_rect())
    screen.fill((240, 240, 240))
    for wall in walls:
        pygame.draw.rect(screen, (60, 60, 60), wall)
    pygame.draw.rect(screen, (220, 50, 50), player)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**5-тапсырма.** Метеорит `__init__`-іне: `self.radius = size // 2` және `self.mask = pygame.mask.from_surface(self.image)`. Кемеге: `self.mask = pygame.mask.from_surface(self.image)`. Күтілетін қорытынды: `collide_rect` метеорит кемеге «жақындағанда-ақ» соқтығыс тіркейді; `collide_circle` дөңгелек метеориттерге жақсы; `collide_mask` ең дәл, бірақ көп объект болса баяулауы мүмкін.

**6-тапсырма. Қорғаныс уақыты (Player класына қосымша):**

```python
import pygame


class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((60, 40), pygame.SRCALPHA)
        pygame.draw.polygon(self.image, (0, 190, 255), [(30, 0), (60, 40), (0, 40)])
        self.rect = self.image.get_rect(midbottom=(400, 590))
        self.invincible_until = 0

    def is_invincible(self):
        return pygame.time.get_ticks() < self.invincible_until

    def hit(self):
        """Соқтығысқанда шақырылады; өмір азаю керек болса True қайтарады."""
        if self.is_invincible():
            return False
        self.invincible_until = pygame.time.get_ticks() + 1500
        return True

    def update(self):
        keys = pygame.key.get_pressed()
        self.rect.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 7
        self.rect.clamp_ip(pygame.Rect(0, 0, 800, 600))
        if self.is_invincible():
            visible = (pygame.time.get_ticks() // 100) % 2 == 0
            self.image.set_alpha(255 if visible else 60)
        else:
            self.image.set_alpha(255)
```

Ойын циклінде: `if pygame.sprite.spritecollide(player, meteors, True) and player.hit(): lives -= 1`.

**7-тапсырма. Arkanoid (★ А шағылысуымен):**

```python
import pygame

WIDTH, HEIGHT = 800, 600
MAX_DX = 7

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Arkanoid")
clock = pygame.time.Clock()

paddle = pygame.Rect(0, 0, 120, 15)
paddle.midbottom = (WIDTH // 2, HEIGHT - 20)
ball = pygame.Rect(0, 0, 16, 16)
ball.center = (WIDTH // 2, HEIGHT // 2)
dx, dy = 4, -5
bricks = []
for row in range(5):
    for col in range(8):
        bricks.append(pygame.Rect(20 + col * 96, 50 + row * 30, 90, 24))
colors = [(230, 60, 60), (240, 150, 40), (240, 220, 50), (80, 200, 80), (70, 140, 240)]

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    paddle.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 8
    paddle.clamp_ip(screen.get_rect())

    ball.x += dx
    ball.y += dy
    if ball.left <= 0 or ball.right >= WIDTH:
        dx = -dx
    if ball.top <= 0:
        dy = -dy
    if ball.colliderect(paddle) and dy > 0:
        dy = -dy
        offset = (ball.centerx - paddle.centerx) / (paddle.width / 2)
        dx = int(offset * MAX_DX) or 1
    index = ball.collidelist(bricks)
    if index != -1:
        bricks.pop(index)
        dy = -dy
    if ball.top > HEIGHT:
        print("Ойын аяқталды!")
        running = False
    if not bricks:
        print("Жеңіс! Барлық кірпіш бұзылды")
        running = False

    screen.fill((15, 15, 30))
    for i, brick in enumerate(bricks):
        pygame.draw.rect(screen, colors[(brick.y - 50) // 30], brick, border_radius=4)
    pygame.draw.rect(screen, (200, 200, 200), paddle, border_radius=7)
    pygame.draw.ellipse(screen, (255, 255, 255), ball)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**★ Ә. Жылан:**

```python
import random
import pygame

CELL, N = 25, 24
SIZE = CELL * N

pygame.init()
screen = pygame.display.set_mode((SIZE, SIZE))
clock = pygame.time.Clock()
snake = [pygame.Rect(CELL * 5, CELL * 5, CELL, CELL)]
direction = (1, 0)
apple = pygame.Rect(CELL * 10, CELL * 10, CELL, CELL)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            new = {pygame.K_UP: (0, -1), pygame.K_DOWN: (0, 1),
                   pygame.K_LEFT: (-1, 0), pygame.K_RIGHT: (1, 0)}.get(event.key)
            if new and (new[0] != -direction[0] or new[1] != -direction[1]):
                direction = new
    head = snake[0].move(direction[0] * CELL, direction[1] * CELL)
    if not screen.get_rect().contains(head) or head.collidelist(snake) != -1:
        print("Ойын аяқталды! Ұзындығы:", len(snake))
        running = False
        continue
    snake.insert(0, head)
    if head.colliderect(apple):
        apple.topleft = (random.randrange(N) * CELL, random.randrange(N) * CELL)
    else:
        snake.pop()
    pygame.display.set_caption(f"Жылан: {len(snake)}")
    screen.fill((20, 20, 20))
    pygame.draw.rect(screen, (220, 30, 30), apple)
    for part in snake:
        pygame.draw.rect(screen, (40, 200, 40), part.inflate(-2, -2))
    pygame.display.flip()
    clock.tick(10)
pygame.quit()
```

</details>
