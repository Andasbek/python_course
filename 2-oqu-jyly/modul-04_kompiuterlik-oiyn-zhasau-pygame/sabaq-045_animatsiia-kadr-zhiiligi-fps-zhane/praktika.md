# Сабақ 45. Практикалық тапсырмалар: анимация, FPS және уақыт

## Базалық деңгей

### 1-тапсырма. FPS тәжірибесі
Екі шаршы экранның сол жағынан оңға қарай жүреді:
- **қызыл** — кадрға тәуелді: `x += 5`;
- **жасыл** — уақытқа тәуелді: `x += 300 * dt`.
Шетке жеткенде қайта сол жаққа оралады. `1`, `2`, `3` пернелері FPS шегін 15, 30, 60 етіп ауыстырады. Экранда нақты FPS (`clock.get_fps()`) көрсетілсін. Не байқадың? Қорытындыны 2 сөйлеммен жаз.

### 2-тапсырма. Vector2-мен басқару
38-сабақтағы басқарылатын кейіпкерді `Vector2` және `dt` арқылы қайта жаз: жылдамдығы 250 пиксель/с, қиғаш жүргенде жылдамдық артпайды (`normalize()`).

### 3-тапсырма. Жыпылықтайтын жазу
«Бастау үшін Enter басыңыз» жазуы секундына 2 рет көрініп-жоғалсын (`get_ticks()` арқылы, FPS-ке тәуелсіз).

### 4-тапсырма. Жүретін адамша
Теориядағы `make_walk_frames()` функциясымен 6 кадр жасап, анимацияны экран ортасында секундына 8 кадр жылдамдықпен ойнат. `↑` / `↓` пернелері анимация жылдамдығын 1-ге арттырсын/азайтсын (2–20 аралығында), мәні экранда көрсетілсін.

## Кеңейтілген деңгей

### 5-тапсырма. Тұру/жүру анимациясы
Теориядағы `Hero` класын іске қос: тұрғанда — idle кадры, ← → басқанда — жүру анимациясы, кейіпкер жүрген жағына қарайды. Қосымша: `idle` анимациясына «тыныс алу» эффектін қос (кадр биіктігі ақырын өзгереді, 2 кадр, 2 кадр/с).

### 6-тапсырма. Жарылыс анимациясы
Тінтуірді басқан жерде 8 кадрдан тұратын жарылыс анимациясы бір рет ойнап, жоғалсын (`Explosion` спрайты). Кадрлар — бағдарламамен: радиусы артып, түсі сарыдан қызылға ауысатын дөңгелектер. Анимация жылдамдығы — 20 кадр/с.

### 7-тапсырма. Тегіс камера
Әлем ені 3000 пиксель (жерде әр 200 пиксель сайын ағаштар). Кейіпкер ← → арқылы әлемде жүреді, камера оған `lerp` арқылы тегіс еріп отырады. Барлық объектілер `x - camera_x` координатасымен салынады.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Sprite sheet жүктеу.** Kenney.nl-ден кейіпкердің жүгіру кадрлары бар спрайт парағын жүкте (немесе өзің 4 кадрлық парақты Paint-те сал). `load_sheet_frames()` арқылы кадрларды қиып, 5-тапсырмадағы `Hero`-ға қос. Қосымша: бірнеше қатарлы парақ үшін функцияны `row` параметрімен кеңейт.

**Ә. Баяу қозғалыс (slow motion).** «Ғарыш қорғаушысына» `T` пернесімен 3 секундтық «уақытты баяулату» бонусын қос: осы уақытта барлық метеориттер `dt * 0.3`-пен жаңарады, ал ойыншы — қалыпты `dt`-мен. Бонус 10 секундта бір рет қолжетімді; қалған уақыт HUD-та көрсетілсін.

## Үй тапсырмасы

1. «Ғарыш қорғаушысын» delta time-ға көшір: барлық жылдамдықтар пиксель/секунд түрінде, `update(dt)`. Метеориттерге айналу анимациясын қос (37-сабақтағы `rotate` + `dt`).
2. IV модульдің жеке жобасын («Ғарыш қорғаушысы») толық күйінде GitHub-қа жүкте: README, скриншоттар, басқару, ресурстар лицензиясы.
3. **Командалық жобаға дайындал:** 2–3 ойын идеясын ойлап кел (жанр, басты кейіпкер, мақсат, басқару, ерекшелігі). Келесі сабақта топтарға бөлініп, идеяны бекітеміз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма:**

```python
import pygame

WIDTH, HEIGHT = 800, 300
LIMITS = {pygame.K_1: 15, pygame.K_2: 30, pygame.K_3: 60}

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
font = pygame.font.SysFont("arial", 24)
clock = pygame.time.Clock()
fps_limit = 60
red_x = 0
green_x = 0.0
running = True
while running:
    dt = min(clock.tick(fps_limit) / 1000, 0.1)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key in LIMITS:
            fps_limit = LIMITS[event.key]
    red_x = (red_x + 5) % WIDTH
    green_x = (green_x + 300 * dt) % WIDTH
    screen.fill((20, 20, 20))
    pygame.draw.rect(screen, (220, 50, 50), (red_x, 80, 40, 40))
    pygame.draw.rect(screen, (50, 200, 50), (round(green_x), 180, 40, 40))
    text = font.render(f"Шек: {fps_limit} | Нақты FPS: {clock.get_fps():.0f}", True,
                       (255, 255, 255))
    screen.blit(text, (10, 10))
    pygame.display.flip()
pygame.quit()
```

Күтілетін қорытынды: FPS төмендегенде қызыл шаршы баяулайды (жылдамдығы FPS-ке тәуелді), ал жасыл шаршының жылдамдығы өзгермейді — тек қозғалысы «секірмелі» болады.

**2–3-тапсырмалар:**

```python
import pygame
from pygame.math import Vector2

WIDTH, HEIGHT = 800, 600
SPEED = 250

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
font = pygame.font.SysFont("arial, dejavusans", 30)
clock = pygame.time.Clock()
pos = Vector2(WIDTH / 2, HEIGHT / 2)
running = True
while running:
    dt = min(clock.tick(60) / 1000, 0.05)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    direction = Vector2(keys[pygame.K_RIGHT] - keys[pygame.K_LEFT],
                        keys[pygame.K_DOWN] - keys[pygame.K_UP])
    if direction.length_squared() > 0:
        direction = direction.normalize()
    pos += direction * SPEED * dt
    pos.x = max(20, min(WIDTH - 20, pos.x))
    pos.y = max(20, min(HEIGHT - 20, pos.y))
    screen.fill((30, 30, 50))
    pygame.draw.circle(screen, (0, 200, 255), (round(pos.x), round(pos.y)), 20)
    if pygame.time.get_ticks() // 250 % 2 == 0:          # 250 мс көрінеді, 250 мс жоқ
        text = font.render("Бастау үшін Enter басыңыз", True, (255, 255, 255))
        screen.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT - 60)))
    pygame.display.flip()
pygame.quit()
```

Секундына 2 рет жыпылықтау: бір цикл (көріну + жоғалу) = 500 мс, яғни әр жартысы 250 мс.

**4-тапсырма:**

```python
import math
import pygame


def make_walk_frames(count=6, size=60):
    frames = []
    for i in range(count):
        surf = pygame.Surface((size, size), pygame.SRCALPHA)
        swing = int(12 * math.sin(2 * math.pi * i / count))
        cx = size // 2
        pygame.draw.circle(surf, (255, 220, 180), (cx, 10), 8)
        pygame.draw.line(surf, (40, 120, 220), (cx, 18), (cx, 38), 4)
        pygame.draw.line(surf, (40, 40, 40), (cx, 38), (cx + swing, 56), 4)
        pygame.draw.line(surf, (40, 40, 40), (cx, 38), (cx - swing, 56), 4)
        pygame.draw.line(surf, (40, 120, 220), (cx, 24), (cx - swing, 34), 3)
        pygame.draw.line(surf, (40, 120, 220), (cx, 24), (cx + swing, 34), 3)
        frames.append(pygame.transform.scale(surf, (size * 3, size * 3)))
    return frames


pygame.init()
screen = pygame.display.set_mode((600, 400))
font = pygame.font.SysFont("arial, dejavusans", 26)
clock = pygame.time.Clock()
frames = make_walk_frames()
anim_fps = 8
index = 0.0
running = True
while running:
    dt = clock.tick(60) / 1000
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP:
                anim_fps = min(20, anim_fps + 1)
            elif event.key == pygame.K_DOWN:
                anim_fps = max(2, anim_fps - 1)
    index = (index + anim_fps * dt) % len(frames)
    screen.fill((235, 235, 235))
    frame = frames[int(index)]
    screen.blit(frame, frame.get_rect(center=(300, 200)))
    screen.blit(font.render(f"Анимация: {anim_fps} кадр/с", True, (0, 0, 0)), (10, 10))
    pygame.display.flip()
pygame.quit()
```

**5-тапсырма.** Теориядағы `Hero` класы + 4-тапсырмадағы `make_walk_frames()`; ойын циклі: `dt = clock.tick(60) / 1000`, `heroes.update(dt)`, `heroes.draw(screen)`. Idle «тынысы»:

```python
import pygame


def make_idle_frames(base):
    """Негізгі кадрдан биіктігі сәл өзгеретін екі кадр жасайды."""
    w, h = base.get_size()
    squashed = pygame.transform.scale(base, (w, h - 4))
    frame2 = pygame.Surface((w, h), pygame.SRCALPHA)
    frame2.blit(squashed, (0, 4))
    return [base, frame2]
```

`Hero.__init__`-те: `"idle": make_idle_frames(walk_frames[0])`, ал idle үшін анимация жылдамдығы 2 кадр/с: `speed = 2 if self.state == "idle" else self.ANIM_FPS`.

**6-тапсырма. Жарылыс:**

```python
import pygame


def make_explosion_frames(count=8, size=80):
    frames = []
    for i in range(count):
        t = i / (count - 1)
        surf = pygame.Surface((size, size), pygame.SRCALPHA)
        radius = int(8 + t * (size // 2 - 8))
        color = (255, int(230 * (1 - t)), 0, int(255 * (1 - t * 0.7)))
        pygame.draw.circle(surf, color, (size // 2, size // 2), radius)
        frames.append(surf)
    return frames


class Explosion(pygame.sprite.Sprite):
    ANIM_FPS = 20

    def __init__(self, pos, frames):
        super().__init__()
        self.frames = frames
        self.index = 0.0
        self.image = frames[0]
        self.rect = self.image.get_rect(center=pos)

    def update(self, dt):
        self.index += self.ANIM_FPS * dt
        if self.index >= len(self.frames):
            self.kill()                       # бір рет ойнап, жоғалады
            return
        self.image = self.frames[int(self.index)]


pygame.init()
screen = pygame.display.set_mode((700, 450))
clock = pygame.time.Clock()
frames = make_explosion_frames()
explosions = pygame.sprite.Group()
running = True
while running:
    dt = clock.tick(60) / 1000
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            explosions.add(Explosion(event.pos, frames))
    explosions.update(dt)
    screen.fill((10, 10, 30))
    explosions.draw(screen)
    pygame.display.flip()
pygame.quit()
```

**7-тапсырма. Тегіс камера:**

```python
import pygame

WIDTH, HEIGHT = 800, 400
WORLD_W = 3000
SPEED = 350


def lerp(a, b, t):
    return a + (b - a) * t


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
player_x = 100.0
camera_x = 0.0
trees = list(range(100, WORLD_W, 200))
running = True
while running:
    dt = min(clock.tick(60) / 1000, 0.05)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    player_x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * SPEED * dt
    player_x = max(0, min(WORLD_W, player_x))
    target = max(0, min(WORLD_W - WIDTH, player_x - WIDTH / 2))
    camera_x = lerp(camera_x, target, min(1, 5 * dt))
    screen.fill((150, 210, 255))
    pygame.draw.rect(screen, (70, 160, 70), (0, 330, WIDTH, 70))
    for tx in trees:
        sx = round(tx - camera_x)
        if -50 < sx < WIDTH + 50:
            pygame.draw.rect(screen, (100, 60, 30), (sx - 8, 260, 16, 70))
            pygame.draw.circle(screen, (30, 120, 30), (sx, 250), 35)
    pygame.draw.rect(screen, (220, 40, 40), (round(player_x - camera_x) - 15, 290, 30, 40))
    pygame.display.set_caption(f"Ойыншы: {player_x:.0f} | Камера: {camera_x:.0f}")
    pygame.display.flip()
pygame.quit()
```

`lerp(..., min(1, 5 * dt))` — `t`-ны да `dt`-ке байланыстырдық, сонда камераның «жұмсақтығы» FPS-ке тәуелсіз болады.

**★ А. Көп қатарлы sprite sheet:**

```python
import pygame


def load_sheet_row(sheet, frame_w, frame_h, row=0, count=None):
    """Спрайт парағының row-қатарынан кадрларды қияды."""
    total = sheet.get_width() // frame_w
    count = count or total
    return [sheet.subsurface((i * frame_w, row * frame_h, frame_w, frame_h)).copy()
            for i in range(count)]


demo = pygame.Surface((256, 128))
print(len(load_sheet_row(demo, 64, 64, row=1)), "кадр (2-қатар)")
```

Нәтижесі:
```text
4 кадр (2-қатар)
```

**★ Ә. Баяу қозғалыс (ойын циклінің үзіндісі):**

```python
import pygame

SLOW_TIME = 3000
COOLDOWN = 10000
slow_until = 0
last_used = -COOLDOWN


def time_scale(now):
    return 0.3 if now < slow_until else 1.0


def try_activate(now):
    """T пернесі басылғанда шақырылады."""
    global slow_until, last_used
    if now - last_used >= COOLDOWN:
        slow_until = now + SLOW_TIME
        last_used = now
        return True
    return False


# Ойын циклінде:
# now = pygame.time.get_ticks()
# player_group.update(dt)
# meteors.update(dt * time_scale(now))
# ready_in = max(0, COOLDOWN - (now - last_used)) // 1000  -> HUD: f"Баяулату: {ready_in} с"
```

</details>
