# Сабақ 45. Анимация. Кадр жиілігі (FPS) және уақытты басқару.

## Тақырыптар

1. FPS және Clock
2. Мәселе: кадрға тәуелді қозғалыс
3. Шешімі: delta time (dt)
4. Бөлшек координаталар: Vector2
5. Толық мысал: уақытқа тәуелді қозғалыс
6. Кадрлық анимация
7. Sprite sheet: бір суретте көп кадр
8. Анимация күйлері
9. Уақытқа байланысты басқа құралдар
10. Жиі кездесетін қателер
11. Негізгі ұғымдар

## 1. FPS және Clock

**FPS** (frames per second) — секундтағы кадр саны.

| FPS | Әсері | Қолданылуы |
|---|---|---|
| 12–24 | Мультфильм, «секірмелі» қозғалыс | Кадрлық анимация, кино |
| 30 | Қалыпты | Консольдік ойындар, баяу ойындар |
| 60 | Тегіс | Көптеген компьютерлік ойындар |
| 120–144 | Өте тегіс | Спорттық ойындар, арнайы мониторлар |

`pygame.time.Clock` әдістері:

| Әдіс | Не істейді |
|---|---|
| `clock.tick(fps)` | Кадр жиілігін шектейді; алдыңғы шақырудан бергі **миллисекундтарды** қайтарады |
| `clock.get_fps()` | Соңғы бірнеше кадр бойынша орташа нақты FPS |
| `clock.get_time()` | Соңғы `tick` аралығы (мс) |

**Маңызды:** `tick(60)` — «60-тан **аспасын**» деген сөз. Компьютер әлсіз болса немесе ойында объектілер көп болса, нақты FPS 40, 30-ға дейін түсуі мүмкін.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 200))
clock = pygame.time.Clock()
for frame in range(5):
    dt = clock.tick(60)
    print(f"Кадр {frame}: {dt} мс")
pygame.quit()
```

Нәтижесі (мысал):
```text
Кадр 0: 0 мс
Кадр 1: 17 мс
Кадр 2: 16 мс
Кадр 3: 17 мс
Кадр 4: 16 мс
```

60 FPS кезінде бір кадр ≈ 1000 / 60 ≈ 16,7 мс.

## 2. Мәселе: кадрға тәуелді қозғалыс

Осы уақытқа дейін біз жылдамдықты **пиксель/кадр** түрінде бердік: `rect.x += 5`.

| Компьютер | FPS | Жылдамдық |
|---|---|---|
| Күшті | 60 | 5 × 60 = 300 пиксель/с |
| Әлсіз | 30 | 5 × 30 = **150** пиксель/с |

Әлсіз компьютерде ойын баяу «жүзеді», ал FPS шектелмесе, күштісінде кейіпкер «ұшып» кетеді. Ойын әр компьютерде әр түрлі жылдамдықпен жүрмеуі керек.

## 3. Шешімі: delta time (dt)

**Delta time** — алдыңғы кадрдан бергі өткен уақыт (секундпен). Жылдамдықты **пиксель/секунд** түрінде беріп, әр кадрда оны `dt`-ке көбейтеміз:

```text
жылжу = жылдамдық (пиксель/с) × dt (с)
```

| FPS | dt | 300 × dt | Секундтағы жылжу |
|---|---|---|---|
| 60 | 0,0167 с | 5 пиксель | 5 × 60 = 300 |
| 30 | 0,0333 с | 10 пиксель | 10 × 30 = 300 |

Кадр аз болса, әр кадрда көбірек жылжиды — нәтижесінде жылдамдық барлық компьютерде **бірдей**.

```python
dt = clock.tick(60) / 1000      # миллисекунд → секунд
x += SPEED * dt                 # SPEED — пиксель/секунд
```

## 4. Бөлшек координаталар: Vector2

`Rect` тек бүтін сандарды сақтайды: `rect.x += 2.5` → бөлшек бөлік жоғалады, ал `dt`-мен жылжу әрқашан бөлшек. Сондықтан нақты орынды бөлек `float` айнымалыда немесе **`pygame.math.Vector2`** векторында сақтаймыз, ал `rect`-ті тек салу үшін жаңартамыз:

```python
from pygame.math import Vector2

pos = Vector2(100, 200)            # орын
vel = Vector2(300, -150)           # жылдамдық, пиксель/с
dt = 0.016
pos += vel * dt                    # векторлық амал
print(pos)
print(round(pos.x), round(pos.y))
print("Жылдамдық модулі:", round(vel.length(), 1))
direction = Vector2(3, 4).normalize()
print("Бірлік вектор:", direction)
```

Нәтижесі:
```text
[104.8, 197.6]
105 198
Жылдамдық модулі: 335.4
Бірлік вектор: [0.6, 0.8]
```

`normalize()` — ұзындығы 1-ге тең вектор. Қиғаш жүргенде жылдамдық артып кетпеуі үшін бағыт векторын нормалаймыз (→ және ↑ қатар басылса, √2 есе жылдам жүрмейді).

## 5. Толық мысал: уақытқа тәуелді қозғалыс

```python
import pygame
from pygame.math import Vector2

WIDTH, HEIGHT = 800, 600
SPEED = 300                        # пиксель/секунд


class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill((0, 200, 255))
        self.rect = self.image.get_rect(center=(WIDTH // 2, HEIGHT // 2))
        self.pos = Vector2(self.rect.center)

    def update(self, dt):
        keys = pygame.key.get_pressed()
        direction = Vector2(keys[pygame.K_RIGHT] - keys[pygame.K_LEFT],
                            keys[pygame.K_DOWN] - keys[pygame.K_UP])
        if direction.length() > 0:
            direction = direction.normalize()
        self.pos += direction * SPEED * dt
        self.pos.x = max(25, min(WIDTH - 25, self.pos.x))
        self.pos.y = max(25, min(HEIGHT - 25, self.pos.y))
        self.rect.center = (round(self.pos.x), round(self.pos.y))


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
font = pygame.font.SysFont("arial", 24)
player = Player()
group = pygame.sprite.Group(player)
fps_limit = 60
running = True
while running:
    dt = clock.tick(fps_limit) / 1000
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_f:
            fps_limit = 15 if fps_limit == 60 else 60     # F — FPS-ті ауыстыру
    group.update(dt)                                      # update-ке аргумент беруге болады
    screen.fill((25, 25, 25))
    group.draw(screen)
    info = font.render(f"FPS: {clock.get_fps():.0f} (шегі {fps_limit}) — F басыңыз",
                       True, (255, 255, 255))
    screen.blit(info, (10, 10))
    pygame.display.flip()
pygame.quit()
```

`F` пернесімен FPS-ті 15-ке түсіріп көріңіз: қозғалыс «секірмелі» болады, бірақ кейіпкер секундына бәрібір 300 пиксель жүреді!

`group.update(dt)` — топтың `update()`-іне берілген аргументтер әр спрайттың `update()`-іне жіберіледі.

## 6. Кадрлық анимация

Кадрлық анимация — бірнеше суретті (кадрды) кезекпен көрсету. Негізгі айнымалылар:

| Айнымалы | Мағынасы |
|---|---|
| `frames` | Кадрлар (Surface) тізімі |
| `frame_index` | Қазір көрсетілетін кадр нөмірі (бөлшек болуы мүмкін) |
| `animation_speed` | Секундына неше кадр ауысады (анимация FPS-і) |

**Маңызды:** анимация жылдамдығы ойын FPS-інен **бөлек**. Ойын 60 FPS-те жүреді, ал жүгіру анимациясы секундына 8–12 кадр ауысса жеткілікті.

```python
frame_index += animation_speed * dt          # мысалы, 10 кадр/с × 0.016 с = 0.16
if frame_index >= len(frames):
    frame_index = 0                          # басынан (цикл)
image = frames[int(frame_index)]
```

**Кадрларды бағдарламамен жасау** (сурет файлдары жоқ кезде ыңғайлы):

```python
import math
import pygame


def make_walk_frames(count=6, size=60):
    """Аяқтары қозғалатын қарапайым «адамша» кадрлары."""
    frames = []
    for i in range(count):
        surf = pygame.Surface((size, size), pygame.SRCALPHA)
        swing = int(12 * math.sin(2 * math.pi * i / count))
        cx = size // 2
        pygame.draw.circle(surf, (255, 220, 180), (cx, 10), 8)                  # бас
        pygame.draw.line(surf, (40, 120, 220), (cx, 18), (cx, 38), 4)            # дене
        pygame.draw.line(surf, (40, 40, 40), (cx, 38), (cx + swing, 56), 4)      # аяқ 1
        pygame.draw.line(surf, (40, 40, 40), (cx, 38), (cx - swing, 56), 4)      # аяқ 2
        pygame.draw.line(surf, (40, 120, 220), (cx, 24), (cx - swing, 34), 3)    # қол 1
        pygame.draw.line(surf, (40, 120, 220), (cx, 24), (cx + swing, 34), 3)    # қол 2
        frames.append(surf)
    return frames
```

## 7. Sprite sheet: бір суретте көп кадр

Ойын суретшілері кадрларды бір үлкен суретке (sprite sheet, «спрайт парағы») қатар орналастырады:

```text
+------+------+------+------+
|кадр 0|кадр 1|кадр 2|кадр 3|   әр кадр 64×64, барлығы 256×64
+------+------+------+------+
```

Кадрларды `subsurface()` арқылы «қиып» аламыз:

```python
import pygame


def load_sheet_frames(sheet, frame_w, frame_h):
    """Спрайт парағын бір қатардағы кадрларға бөледі."""
    frames = []
    for i in range(sheet.get_width() // frame_w):
        rect = pygame.Rect(i * frame_w, 0, frame_w, frame_h)
        frames.append(sheet.subsurface(rect).copy())
    return frames


demo_sheet = pygame.Surface((256, 64))
print(len(load_sheet_frames(demo_sheet, 64, 64)), "кадр")
```

Нәтижесі:
```text
4 кадр
```

Нақты файлмен: `sheet = pygame.image.load("assets/images/hero_run.png").convert_alpha()`.

## 8. Анимация күйлері

Кейіпкер тұрғанда — «тыныс алу» (idle), жүргенде — «жүгіру» (run), секіргенде — «секіру» (jump) анимациясы. Бұл — күйлер машинасының (state machine) кішкене нұсқасы:

```python
import pygame
from pygame.math import Vector2

WIDTH, HEIGHT = 800, 400


class Hero(pygame.sprite.Sprite):
    """Тұру және жүру анимациясы бар кейіпкер."""

    SPEED = 220
    ANIM_FPS = 10

    def __init__(self, walk_frames):
        super().__init__()
        self.animations = {
            "idle": [walk_frames[0]],
            "run": walk_frames,
        }
        self.state = "idle"
        self.frame_index = 0.0
        self.facing_right = True
        self.image = walk_frames[0]
        self.rect = self.image.get_rect(midbottom=(WIDTH // 2, HEIGHT - 40))
        self.pos = Vector2(self.rect.midbottom)

    def update(self, dt):
        keys = pygame.key.get_pressed()
        move = keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]
        new_state = "run" if move else "idle"
        if new_state != self.state:          # күй ауысса — анимация басынан
            self.state = new_state
            self.frame_index = 0.0
        if move:
            self.facing_right = move > 0
        self.pos.x += move * self.SPEED * dt
        self.pos.x = max(30, min(WIDTH - 30, self.pos.x))

        frames = self.animations[self.state]
        self.frame_index = (self.frame_index + self.ANIM_FPS * dt) % len(frames)
        image = frames[int(self.frame_index)]
        if not self.facing_right:
            image = pygame.transform.flip(image, True, False)
        self.image = image
        self.rect = self.image.get_rect(midbottom=(round(self.pos.x), round(self.pos.y)))
```

## 9. Уақытқа байланысты басқа құралдар

**Таймер get_ticks() арқылы** (мысалы, жарылыс 0,5 секундтан кейін жоғалады):

```python
# self.born = pygame.time.get_ticks()
# if pygame.time.get_ticks() - self.born > 500: self.kill()
```

**Тегіс өту (lerp — сызықтық интерполяция).** Камера немесе мәзір элементі мақсатқа бірден секірмей, біртіндеп жақындайды:

```python
def lerp(a, b, t):
    """a-дан b-ға қарай t үлесін (0..1) жүру."""
    return a + (b - a) * t

camera_x = 0.0
target = 100.0
for frame in range(5):
    camera_x = lerp(camera_x, target, 0.3)
    print(round(camera_x, 1), end=" ")
```

Нәтижесі:
```text
30.0 51.0 65.7 76.0 83.2 
```

Әр кадрда қалған қашықтықтың 30%-ын жүреміз: алдымен жылдам, сосын баяу — қозғалыс «жұмсақ» көрінеді.

## Жиі кездесетін қателер

**1. `dt`-ті миллисекундпен қалдыру**
```python
import pygame
pygame.init()
clock = pygame.time.Clock()
x = 0
SPEED = 300
dt = clock.tick(60)        # 16 (мс!)
x += SPEED * dt            # 4800 пиксель — кейіпкер бір кадрда экраннан ұшып кетеді
```
Дұрысы: `dt = clock.tick(60) / 1000`.

**2. Бөлшек жылжуды Rect-ке қосу** — `rect.x += 300 * 0.016` (4.8) → `rect.x` тек 4-ке өзгереді, ал 2.4 болса — 2-ге; қозғалыс біркелкі емес немесе мүлде тоқтап қалады. Орынды `Vector2`-де сақтаңыз.

**3. Анимацияны әр кадрда ауыстыру** — `frame_index += 1`: 60 FPS-те 6 кадрлық анимация секундына 10 рет айналып, кейіпкер «дірілдейді». Анимация жылдамдығын `ANIM_FPS * dt` арқылы басқарыңыз.

**4. `frame_index`-ті бүтін санға айналдырмау** — `frames[2.7]` → `TypeError: list indices must be integers`. Дұрысы: `frames[int(frame_index)]`.

**5. Ойын «қатып» қалғаннан кейін dt өте үлкен** — терезені сүйрегенде немесе компьютер «ойланып» қалса, dt 0,5 секунд болып, кейіпкер қабырғадан «өтіп» кетуі мүмкін. Шешімі: `dt = min(dt, 0.05)`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| FPS | Секундтағы кадр саны |
| `clock.tick()` | FPS-ті шектеп, кадр аралығын (мс) қайтарады |
| Delta time (dt) | Алдыңғы кадрдан бергі уақыт (с) |
| Пиксель/секунд | FPS-ке тәуелсіз жылдамдық бірлігі |
| `Vector2` | Бөлшек координаталы екі өлшемді вектор |
| `normalize()` | Векторды ұзындығы 1-ге келтіру |
| Кадрлық анимация | Кадрларды кезекпен ауыстырып көрсету |
| Sprite sheet | Бір суретке жиналған анимация кадрлары |
| `subsurface()` | Беттің бір бөлігін жеке Surface ретінде алу |
| Lerp | Екі мән арасындағы тегіс өту |

## Ескерту

- `clock.tick(fps)` өткен уақытты миллисекундпен қайтарады; секундқа айналдыру үшін 1000-ға бөліңіз: `dt = clock.tick(60) / 1000`.
- Терезені сүйреу немесе компьютердің «қатып қалуы» `dt`-ні өте үлкен етуі мүмкін. Оны шектеңіз: `dt = min(dt, 0.05)`.
- `clock.tick_busy_loop()` дәлірек, бірақ процессорды көбірек жүктейді; көп жағдайда `tick()` жеткілікті.
- `pygame.time.get_ticks()` — `pygame.init()`-тен бері өткен миллисекундтар; Python-ның өзінде дәл уақыт өлшеу үшін `time.perf_counter()` бар.
- `pygame.time` құжаттамасы: https://www.pygame.org/docs/ref/time.html
