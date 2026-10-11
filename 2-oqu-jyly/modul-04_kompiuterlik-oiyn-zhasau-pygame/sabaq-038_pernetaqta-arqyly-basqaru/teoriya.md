# Сабақ 38. Пернетақта арқылы басқару. Объектіні жылжыту.

## Тақырыптар

1. Пернетақта оқиғалары: KEYDOWN және KEYUP
2. Перне тұрақтылары
3. Екі тәсіл: оқиға немесе күй
4. Кейіпкерді жылжыту: get_pressed()
5. Экран шекарасынан шықпау
6. Бағыт пен кейіпкер суреті
7. Секіру және ауырлық күші
8. Торлы (дискретті) қозғалыс
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Пернетақта оқиғалары: KEYDOWN және KEYUP

Перне басылған **сәтте** кезекке `KEYDOWN` оқиғасы, жіберілген сәтте `KEYUP` оқиғасы түседі. Оқиғаның `event.key` атрибутында қай перне екені жазылған.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
pygame.display.set_caption("Перне оқиғалары")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            print("Басылды:", pygame.key.name(event.key))
            if event.key == pygame.K_SPACE:
                print("  Бос орын — секіру!")
        elif event.type == pygame.KEYUP:
            print("Жіберілді:", pygame.key.name(event.key))
    screen.fill((0, 0, 0))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

Нәтижесі (A, Space, оң жақ бағыттауышты басқанда):
```text
Басылды: a
Жіберілді: a
Басылды: space
  Бос орын — секіру!
Жіберілді: space
Басылды: right
Жіберілді: right
```

## 2. Перне тұрақтылары

| Перне | Тұрақты | Перне | Тұрақты |
|---|---|---|---|
| ← → ↑ ↓ | `K_LEFT`, `K_RIGHT`, `K_UP`, `K_DOWN` | Бос орын | `K_SPACE` |
| A, W, S, D | `K_a`, `K_w`, `K_s`, `K_d` | Enter | `K_RETURN` |
| Escape | `K_ESCAPE` | Shift (сол) | `K_LSHIFT` |
| 0–9 | `K_0` ... `K_9` | P | `K_p` |

Әріп тұрақтылары **кіші** әріппен жазылады: `K_a`, `K_A` емес.

## 3. Екі тәсіл: оқиға немесе күй

| | Оқиға (`KEYDOWN`) | Күй (`get_pressed()`) |
|---|---|---|
| Қашан іске қосылады | Басылған сәтте **бір рет** | Перне басылып **тұрғанда әр кадрда** |
| Қайда жазылады | `for event in ...` циклінің ішінде | Оқиғалар циклінен кейін |
| Қандай әрекеттерге | Секіру, ату, пауза, мәзірді таңдау | Жүру, жүгіру, бұрылу |
| Аналогия | Есік қоңырауы: басқанда бір рет шырылдайды | Газ педалі: басып тұрғанда жүреді |

**Ереже:** «бір рет болатын» әрекет — оқиға; «ұзақ жалғасатын» әрекет — күй.

## 4. Кейіпкерді жылжыту: get_pressed()

`pygame.key.get_pressed()` — барлық перненің қазіргі күйін сақтайтын тізім тәрізді объект қайтарады: `keys[pygame.K_LEFT]` — `True`, егер сол жақ бағыттауыш дәл қазір басылып тұрса.

```python
import pygame

WIDTH, HEIGHT = 800, 600
SPEED = 5

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Кейіпкерді жылжыту")
clock = pygame.time.Clock()

player = pygame.Rect(0, 0, 50, 50)
player.center = (WIDTH // 2, HEIGHT // 2)

running = True
while running:
    # 1. Оқиғалар
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 2. Жаңарту
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] or keys[pygame.K_a]:
        player.x -= SPEED
    if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
        player.x += SPEED
    if keys[pygame.K_UP] or keys[pygame.K_w]:
        player.y -= SPEED
    if keys[pygame.K_DOWN] or keys[pygame.K_s]:
        player.y += SPEED

    # 3. Салу
    screen.fill((30, 30, 30))
    pygame.draw.rect(screen, (0, 200, 255), player)
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

**Назар аударыңыз:** `if ... elif` емес, төрт бөлек `if` қолдандық. Сондықтан екі пернені қатар басса (→ және ↑), кейіпкер **қиғаш** жүреді.

**Жылдамдық** (`SPEED`) — бір кадрдағы жылжу, пиксельмен. 60 FPS кезінде `SPEED = 5` — секундына 300 пиксель.

## 5. Экран шекарасынан шықпау

**1-тәсіл — шартпен:**

```python
if player.left < 0:
    player.left = 0
if player.right > WIDTH:
    player.right = WIDTH
if player.top < 0:
    player.top = 0
if player.bottom > HEIGHT:
    player.bottom = HEIGHT
```

**2-тәсіл — `clamp_ip()`:** Rect-ті басқа Rect ішіне «қамайды».

```python
player.clamp_ip(screen.get_rect())
```

**3-тәсіл — «телепорт»** (экранның бір жағынан шығып, екінші жағынан кіру, мысалы Pac-Man-дағыдай):

```python
if player.right < 0:
    player.left = WIDTH
elif player.left > WIDTH:
    player.right = 0
```

## 6. Бағыт пен кейіпкер суреті

Кейіпкер қай жаққа жүрсе, сол жаққа қарап тұруы керек. Ол үшін бағытты есте сақтап, суреттің екі нұсқасын қолданамыз (`pygame.transform.flip`):

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 300))
player_right = pygame.Surface((60, 40), pygame.SRCALPHA)
pygame.draw.polygon(player_right, (255, 120, 0), [(0, 0), (60, 20), (0, 40)])  # оңға қараған «балық»
player_left = pygame.transform.flip(player_right, True, False)
rect = player_right.get_rect(center=(300, 150))
facing_right = True
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        rect.x -= 4
        facing_right = False
    if keys[pygame.K_RIGHT]:
        rect.x += 4
        facing_right = True
    rect.clamp_ip(screen.get_rect())
    screen.fill((0, 60, 120))
    screen.blit(player_right if facing_right else player_left, rect)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

## 7. Секіру және ауырлық күші

Платформалық ойындарда (Mario сияқты) кейіпкер секіреді де, қайта жерге түседі. Ол үшін **тік жылдамдық** (`vel_y`) және **гравитация** (әр кадрда жылдамдыққа қосылатын тұрақты) қажет:

```text
Секіру сәтінде:  vel_y = -15        (жоғары қарай итеру)
Әр кадрда:       vel_y += GRAVITY   (ауырлық күші баяулатады, сосын төмен тартады)
                 y     += vel_y
Жерге тигенде:   y = жер деңгейі, vel_y = 0
```

```python
import pygame

WIDTH, HEIGHT = 800, 400
GROUND = 350
GRAVITY = 0.8
JUMP_POWER = -15
SPEED = 5

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Секіру: ← → және Space")
clock = pygame.time.Clock()

player = pygame.Rect(100, 0, 40, 60)
player.bottom = GROUND
vel_y = 0
on_ground = True

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            if on_ground:                 # тек жерде тұрғанда секіре алады
                vel_y = JUMP_POWER
                on_ground = False

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player.x -= SPEED
    if keys[pygame.K_RIGHT]:
        player.x += SPEED

    vel_y += GRAVITY
    player.y += int(vel_y)
    if player.bottom >= GROUND:
        player.bottom = GROUND
        vel_y = 0
        on_ground = True
    player.clamp_ip(screen.get_rect())

    screen.fill((135, 206, 235))
    pygame.draw.rect(screen, (60, 160, 60), (0, GROUND, WIDTH, HEIGHT - GROUND))
    pygame.draw.rect(screen, (200, 50, 50), player)
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

Мұнда екі тәсіл де бар: **жүру** — `get_pressed()` (үздіксіз), **секіру** — `KEYDOWN` (бір рет). `on_ground` жалауы ауада «екінші рет секіруге» жол бермейді.

## 8. Торлы (дискретті) қозғалыс

Шахмат, «Жылан», лабиринт ойындарында кейіпкер бір басуда бір **ұяшыққа** жылжиды. Мұнда `KEYDOWN` қолданамыз:

```python
CELL = 40
col, row = 5, 5

def move(key, col, row):
    if key == "left":
        col -= 1
    elif key == "right":
        col += 1
    elif key == "up":
        row -= 1
    elif key == "down":
        row += 1
    return col, row

col, row = move("right", col, row)
col, row = move("up", col, row)
print("Ұяшық:", col, row, "| Пиксель:", col * CELL, row * CELL)
```

Нәтижесі:
```text
Ұяшық: 6 4 | Пиксель: 240 160
```

## Жиі кездесетін қателер

**1. Жүруді KEYDOWN арқылы жасау** — кейіпкер әр басуда тек бір рет 5 пиксель жылжиды, перне басып тұрса — тоқтап қалады. Үздіксіз қозғалыс үшін — `get_pressed()`.

**2. `if ... elif` арқылы бағыттар** — қиғаш жүру мүмкін емес, кейбір пернелер «жұтылады».

**3. Үлкен әріп тұрақтысы**
```python
import pygame
print(pygame.K_A)   # AttributeError: K_A жоқ
```
Дұрысы: `pygame.K_a`.

**4. `get_pressed()`-ті оқиғалар цикліне дейін шақыру** — кезек оқылмаса, пернелер күйі жаңармайды. Әуелі `pygame.event.get()`, содан кейін `get_pressed()`.

**5. Rect координатасына бөлшек сан қосу** — `Rect` тек бүтін сандарды сақтайды, `player.x += 0.5` ештеңе өзгертпейді. Бөлшек жылдамдық үшін координатаны бөлек `float` айнымалыда сақтап, `player.x = int(pos_x)` деп жазыңыз (немесе `pygame.math.Vector2` қолданыңыз).

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `KEYDOWN` / `KEYUP` | Перне басылған / жіберілген сәттегі оқиға |
| `event.key` | Оқиғадағы перненің коды |
| `pygame.key.get_pressed()` | Барлық перненің қазіргі күйі |
| Перне тұрақтысы | `K_LEFT`, `K_SPACE`, `K_a` сияқты перне кодтары |
| Жылдамдық | Бір кадрдағы жылжу шамасы (пиксель/кадр) |
| `clamp_ip()` | Rect-ті басқа Rect шегінде ұстау |
| Гравитация | Әр кадрда тік жылдамдыққа қосылатын тұрақты |
| Дискретті қозғалыс | Тор бойынша ұяшықтан ұяшыққа секіріп жылжу |

## Ескерту

- `pygame.key.get_pressed()` пернетақта күйін тек оқиғалар кезегі өңделгенде (`pygame.event.get()` немесе `pygame.event.pump()`) жаңартады — бұл шақыруды әр кадрда орындаңыз.
- Перне басылып тұрғанда `KEYDOWN` оқиғасы қайталануы үшін `pygame.key.set_repeat(delay, interval)` (миллисекундпен) қолданылады.
- Диагональ бойымен қозғалғанда жылдамдық √2 есе өседі. Біркелкі жылдамдық үшін бағыт векторын `pygame.math.Vector2(dx, dy).normalize()` арқылы қалыптандырыңыз (нөлдік векторды қалыптандыруға болмайды).
- Көптеген пернетақталар бір уақытта 3–4-тен артық пернені тани алмайды (ghosting) — екі ойыншыға арналған басқару перне комбинацияларын тексеріп таңдаңыз.
- Мәтін енгізу үшін `pygame.TEXTINPUT` оқиғасы ыңғайлы (Pygame 2): `event.text` раскладкаға сәйкес таңбаны береді.
