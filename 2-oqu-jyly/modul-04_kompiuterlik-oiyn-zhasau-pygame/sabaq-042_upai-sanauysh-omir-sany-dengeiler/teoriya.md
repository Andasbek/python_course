# Сабақ 42. Ұпай санауыш, өмір саны, деңгейлер. Экранға мәтін шығару.

## Тақырыптар

1. Мәтін шығарудың үш қадамы
2. Қаріп таңдау және қазақ әріптері
3. Мәтінді орналастыру
4. Ойын күйі (game state)
5. Өмірді белгішелермен көрсету
6. Деңгейлер: ойынды біртіндеп қиындату
7. Рекордты файлға сақтау
8. Толық мысал: HUD, деңгейлер, Game Over
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Мәтін шығарудың үш қадамы

Pygame-де мәтін тікелей экранға жазылмайды. Алдымен мәтіннен **сурет** (Surface) жасалады, сосын ол blit арқылы шығарылады:

```text
 1. Қаріп жүктеу          2. Мәтінді «суретке» айналдыру       3. Экранға шығару
 font = Font(...)   ──▶   text = font.render("Ұпай: 10", ...)  ──▶   screen.blit(text, (10, 10))
```

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 300))
pygame.display.set_caption("Мәтін")
font = pygame.font.Font(None, 48)                       # 1. әдепкі қаріп, өлшемі 48
text = font.render("Score: 120", True, (255, 255, 0))   # 2. мәтін → Surface
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((0, 0, 50))
    screen.blit(text, (20, 20))                         # 3. экранға
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**`render(text, antialias, color, background=None)`:**

| Аргумент | Мағынасы |
|---|---|
| `text` | Шығарылатын жол (бір жол; `\n` жұмыс істемейді) |
| `antialias` | `True` — жиектері тегіс (әрқашан `True` жазыңыз) |
| `color` | Мәтін түсі |
| `background` | Фон түсі (берілмесе — мөлдір) |

## 2. Қаріп таңдау және қазақ әріптері

| Тәсіл | Мысал | Ерекшелігі |
|---|---|---|
| Әдепкі қаріп | `pygame.font.Font(None, 36)` | Барлық жерде бар, бірақ кейбір әріптер (ә, ғ, қ, ң, ө, ұ, ү, һ, і) шаршы болып шығуы мүмкін |
| Жүйелік қаріп | `pygame.font.SysFont("arial", 36)` | Компьютерде орнатылған қаріп; басқа компьютерде болмауы мүмкін |
| Файлдан | `pygame.font.Font("assets/fonts/Roboto-Regular.ttf", 36)` | **Ең сенімді**: қаріп жобамен бірге жүреді |

**Ұсыныс:** Google Fonts сайтынан қазақ әріптерін қолдайтын тегін қаріпті (Roboto, Noto Sans, PT Sans, Montserrat) жүктеп, `assets/fonts` папкасына салыңыз. Қауіпсіз жүктеу функциясы:

```python
import os
import pygame

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
FONT_PATH = os.path.join(BASE_DIR, "assets", "fonts", "Roboto-Regular.ttf")


def get_font(size):
    """Жобадағы қаріпті, ол болмаса — жүйелік Arial-ды, ол да болмаса — әдепкісін қайтарады."""
    if os.path.exists(FONT_PATH):
        return pygame.font.Font(FONT_PATH, size)
    return pygame.font.SysFont("arial, dejavusans, freesans", size)
```

`SysFont`-қа үтірмен бірнеше атау беруге болады — бірінші табылғаны қолданылады, ешқайсысы табылмаса — әдепкі қаріп.

**Маңызды:** қаріпті (`Font`) ойын цикліне дейін **бір рет** құрыңыз — оны әр кадрда жүктеу ойынды қатты баяулатады.

## 3. Мәтінді орналастыру

Мәтін — Surface, сондықтан `get_rect()` тәсілі суреттегідей тұп-тура жұмыс істейді:

```python
title = font.render("ОЙЫН АЯҚТАЛДЫ", True, (255, 50, 50))
title_rect = title.get_rect(center=(WIDTH // 2, HEIGHT // 2))     # дәл ортаға
screen.blit(title, title_rect)

lives_text = font.render("Өмір: 3", True, (255, 255, 255))
screen.blit(lives_text, lives_text.get_rect(topright=(WIDTH - 10, 10)))   # оң жақ бұрышқа
```

**Мәтін шығаратын көмекші функция** — кодты қысқартады:

```python
import pygame


def draw_text(surface, text, font, color, **position):
    """Мәтінді берілген позицияға шығарады: draw_text(screen, "Сәлем", font, WHITE, center=(400, 300))."""
    image = font.render(text, True, color)
    rect = image.get_rect(**position)
    surface.blit(image, rect)
    return rect
```

`**position` — кілт сөзді аргументтерді сөздік ретінде қабылдау (`center=...`, `topleft=...` т.б.).

**Көлеңкелі мәтін** — оқылуын жақсартады: алдымен қара түспен 2 пиксель ығыстырып, сосын негізгі түспен шығарамыз.

## 4. Ойын күйі (game state)

Ойын күйі — ойынның қазіргі жағдайын сипаттайтын барлық айнымалылар:

| Айнымалы | Мысал мән | Не үшін |
|---|---|---|
| `score` | 120 | Ұпай |
| `lives` | 3 | Қалған өмір |
| `level` | 2 | Ағымдағы деңгей |
| `high_score` | 450 | Рекорд |
| `game_over` | `False` | Ойын аяқталды ма |
| `start_time` | 15320 | Уақытты есептеу үшін (мс) |

Бұларды сөздікке немесе жеке класқа біріктіру ыңғайлы:

```python
class GameState:
    """Ойынның ағымдағы күйі."""

    def __init__(self):
        self.score = 0
        self.lives = 3
        self.level = 1
        self.game_over = False

    def add_points(self, points):
        self.score += points
        self.level = 1 + self.score // 100     # әр 100 ұпай — жаңа деңгей

    def lose_life(self):
        self.lives -= 1
        if self.lives <= 0:
            self.game_over = True


state = GameState()
state.add_points(250)
state.lose_life()
print(state.score, state.level, state.lives, state.game_over)
```

Нәтижесі:
```text
250 3 2 False
```

## 5. Өмірді белгішелермен көрсету

Сан орнына жүрек белгішелері — көрнекі әрі әдемі:

```python
import pygame


def make_heart(size=24):
    heart = pygame.Surface((size, size), pygame.SRCALPHA)
    r = size // 4
    pygame.draw.circle(heart, (230, 30, 60), (r, r + 2), r)
    pygame.draw.circle(heart, (230, 30, 60), (3 * r, r + 2), r)
    pygame.draw.polygon(heart, (230, 30, 60), [(0, r + 4), (size, r + 4), (size // 2, size)])
    return heart


def draw_lives(surface, heart, lives, x, y):
    for i in range(lives):
        surface.blit(heart, (x + i * (heart.get_width() + 5), y))
```

## 6. Деңгейлер: ойынды біртіндеп қиындату

Деңгей артқан сайын ойын қиындауы керек, бірақ «әділ» болуы қажет. Неге тәуелді етуге болады:

| Параметр | 1-деңгей | 2-деңгей | 3-деңгей | Формула мысалы |
|---|---|---|---|---|
| Жау жылдамдығы | 2–4 | 3–5 | 4–6 | `randint(1 + level, 3 + level)` |
| Пайда болу аралығы | 900 мс | 750 мс | 600 мс | `max(250, 1050 - 150 * level)` |
| Жау түрлері | Қарапайым | + жылдам | + зигзаг | `types[:level]` |

Деңгей ауысқанда таймерді жаңарту керек:

```python
import pygame

SPAWN = pygame.USEREVENT + 1


def spawn_delay(level):
    return max(250, 1050 - 150 * level)


def on_level_up(level):
    pygame.time.set_timer(SPAWN, spawn_delay(level))   # жаңа аралықпен қайта бастау


for lvl in range(1, 6):
    print(lvl, "деңгей →", spawn_delay(lvl), "мс")
```

Нәтижесі:
```text
1 деңгей → 900 мс
2 деңгей → 750 мс
3 деңгей → 600 мс
4 деңгей → 450 мс
5 деңгей → 300 мс
```

Ойыншыға деңгей ауысқанын хабарлау үшін экран ортасында 2 секунд бойы «2-ДЕҢГЕЙ!» жазуын шығарамыз (уақытты `get_ticks()` арқылы бақылаймыз).

## 7. Рекордты файлға сақтау

```python
HIGHSCORE_FILE = "highscore.txt"


def load_high_score():
    try:
        with open(HIGHSCORE_FILE, "r", encoding="utf-8") as f:
            return int(f.read().strip() or 0)
    except (FileNotFoundError, ValueError):
        return 0


def save_high_score(score):
    with open(HIGHSCORE_FILE, "w", encoding="utf-8") as f:
        f.write(str(score))


best = load_high_score()
current = 340
if current > best:
    save_high_score(current)
    print("Жаңа рекорд!", current)
```

Нәтижесі (алғашқы іске қосуда):
```text
Жаңа рекорд! 340
```

## 8. Толық мысал: HUD, деңгейлер, Game Over

```python
import random
import pygame

WIDTH, HEIGHT = 800, 600
SPAWN = pygame.USEREVENT + 1
WHITE, YELLOW, RED = (255, 255, 255), (255, 220, 0), (230, 40, 40)
HIGHSCORE_FILE = "highscore.txt"


def load_high_score():
    try:
        with open(HIGHSCORE_FILE, encoding="utf-8") as f:
            return int(f.read().strip() or 0)
    except (FileNotFoundError, ValueError):
        return 0


def save_high_score(score):
    with open(HIGHSCORE_FILE, "w", encoding="utf-8") as f:
        f.write(str(score))


def draw_text(surface, text, font, color, **pos):
    shadow = font.render(text, True, (0, 0, 0))
    image = font.render(text, True, color)
    rect = image.get_rect(**pos)
    surface.blit(shadow, rect.move(2, 2))
    surface.blit(image, rect)


class Coin(pygame.sprite.Sprite):
    def __init__(self, level):
        super().__init__()
        self.image = pygame.Surface((26, 26), pygame.SRCALPHA)
        pygame.draw.circle(self.image, YELLOW, (13, 13), 13)
        self.rect = self.image.get_rect(midbottom=(random.randint(20, WIDTH - 20), 0))
        self.speed = random.randint(1 + level, 3 + level)

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            self.kill()


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Тиын жаңбыры")
clock = pygame.time.Clock()
font = pygame.font.SysFont("arial, dejavusans", 28)
big_font = pygame.font.SysFont("arial, dejavusans", 64, bold=True)

basket = pygame.Rect(0, 0, 100, 20)
basket.midbottom = (WIDTH // 2, HEIGHT - 10)
coins = pygame.sprite.Group()
score, lives, level = 0, 3, 1
high_score = load_high_score()
level_message_until = pygame.time.get_ticks() + 2000
game_over = False
pygame.time.set_timer(SPAWN, 900)

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == SPAWN and not game_over:
            coins.add(Coin(level))
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_r and game_over:
            score, lives, level, game_over = 0, 3, 1, False
            coins.empty()
            pygame.time.set_timer(SPAWN, 900)

    if not game_over:
        keys = pygame.key.get_pressed()
        basket.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 8
        basket.clamp_ip(screen.get_rect())
        for coin in list(coins):
            coin.update()
            if coin.rect.colliderect(basket):
                coin.kill()
                score += 10
            elif not coin.alive():
                lives -= 1
        new_level = 1 + score // 100
        if new_level != level:
            level = new_level
            pygame.time.set_timer(SPAWN, max(250, 1050 - 150 * level))
            level_message_until = pygame.time.get_ticks() + 2000
        if lives <= 0:
            game_over = True
            if score > high_score:
                high_score = score
                save_high_score(score)

    screen.fill((20, 30, 60))
    coins.draw(screen)
    pygame.draw.rect(screen, (150, 90, 40), basket, border_radius=6)
    draw_text(screen, f"Ұпай: {score}", font, WHITE, topleft=(10, 10))
    draw_text(screen, f"Рекорд: {high_score}", font, YELLOW, midtop=(WIDTH // 2, 10))
    draw_text(screen, f"Өмір: {lives}   Деңгей: {level}", font, WHITE, topright=(WIDTH - 10, 10))
    if pygame.time.get_ticks() < level_message_until and not game_over:
        draw_text(screen, f"{level}-ДЕҢГЕЙ", big_font, YELLOW, center=(WIDTH // 2, HEIGHT // 2))
    if game_over:
        draw_text(screen, "ОЙЫН АЯҚТАЛДЫ", big_font, RED, center=(WIDTH // 2, HEIGHT // 2 - 30))
        draw_text(screen, "Қайта бастау үшін R басыңыз", font, WHITE,
                  center=(WIDTH // 2, HEIGHT // 2 + 40))
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

**Талдау:** жіберіп алынған тиын (`kill()` экран сыртында) өмірді азайтады; әр 100 ұпай — жаңа деңгей және жиірек тиын; ойын аяқталғанда рекорд тексеріледі; `R` пернесі күйді бастапқы мәндерге қайтарады.

## Жиі кездесетін қателер

**1. Қаріпті циклде құру**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((300, 200))
for frame in range(3):
    font = pygame.font.Font(None, 36)   # әр кадрда жүктеу — өте баяу
```
Дұрысы: қаріп — циклге дейін; циклде тек `render`.

**2. `pygame.init()` жоқ** — `pygame.error: font not initialized`.

**3. Санды тікелей render-ге беру**
```python
import pygame
pygame.init()
font = pygame.font.Font(None, 36)
score = 10
image = font.render(score, True, (255, 255, 255))   # TypeError: text must be a unicode or bytes
```
Дұрысы: `font.render(f"Ұпай: {score}", True, ...)` немесе `str(score)`.

**4. Қазақ әріптерінің орнына шаршылар (□)** — қаріпте ол таңбалар жоқ. Жобаға қазақ әріптерін қолдайтын TTF қаріп қосыңыз.

**5. `\n` арқылы көп жолды мәтін** — `render` жаңа жолды түсінбейді. Әр жолды бөлек render жасап, `y`-ті арттырып шығарыңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `pygame.font.Font` / `SysFont` | Файлдан / жүйеден қаріп жүктеу |
| `render()` | Мәтіннен Surface жасау |
| Антиалиасинг | Әріп жиектерін тегістеу |
| HUD | Ойын экранындағы ақпараттық панель (ұпай, өмір, деңгей) |
| Ойын күйі | Ойынның қазіргі жағдайын сипаттайтын айнымалылар жиыны |
| Деңгей | Ойын қиындығының сатысы |
| Рекорд (high score) | Файлда сақталатын ең жоғары нәтиже |
| TTF | Қаріп файлының форматы (TrueType Font) |

## Ескерту

- `Font.render()` `\n` таңбасын жаңа жолға ауыстырмайды — көп жолды мәтіннің әр жолын бөлек салыңыз.
- `render()` — салыстырмалы түрде баяу операция. Мәтін өзгермесе (мысалы, атау), оны бір рет салып, дайын Surface-ті сақтап қойыңыз; ұпайды тек өзгергенде қайта салыңыз.
- Сандарды пішімдеу үшін f-жолдар ыңғайлы: `f"{score:06d}"` → `000150`, `f"{time_left:.1f}"` → `12.5`.
- Рекорд файлын оқығанда `FileNotFoundError` (файл жоқ) және `ValueError` (файлда сан емес мәтін) жағдайларын `try...except` арқылы өңдеңіз.
- `pygame.font` құжаттамасы: https://www.pygame.org/docs/ref/font.html
