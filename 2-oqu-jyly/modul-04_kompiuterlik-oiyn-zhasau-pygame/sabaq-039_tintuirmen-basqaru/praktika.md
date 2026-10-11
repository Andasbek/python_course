# Сабақ 39. Практикалық тапсырмалар: тінтуірмен басқару және оқиғалар

## Базалық деңгей

### 1-тапсырма. Тінтуір координаталары
Терезе тақырыбында курсордың координаталары үздіксіз көрсетілсін: `X: 245  Y: 130`. Сол жақ батырма басылып тұрғанда фон қызыл, әйтпесе қара болсын.

### 2-тапсырма. Басқан жерге дөңгелек
Сол жақ батырманы басқан әр жерде радиусы 15 кездейсоқ түсті дөңгелек пайда болсын (нүктелер тізімде сақталады). Оң жақ батырма — бәрін тазалау.

### 3-тапсырма. Hover-батырма
Терезе ортасында 220×70 батырма. Курсор үстінде тұрғанда түсі ашығырақ болсын. Басқанда фон түсі кездейсоқ өзгерсін.

### 4-тапсырма. Курсорға еретін кейіпкер
Кейіпкер (дөңгелек) курсорға қарай 3 пиксель/кадр жылдамдықпен жүрсін (бірден секірмейді).
**Кеңес:** `x` бойынша: курсор оңда болса `x += 3`, солда болса `x -= 3`; `y` бойынша да солай. Курсорға 3 пикселден жақын болса — қозғалмайды.

## Кеңейтілген деңгей

### 5-тапсырма. «Сурет салғыш» (Paint)
- Сол жақ батырманы басып тұрып жылжытқанда сызық салынады;
- `1`–`5` пернелері түсті ауыстырады;
- дөңгелек қылқалам өлшемін 2–40 аралығында өзгертеді;
- `C` — кенепті тазалау.
**Кеңес:** салынғанды жеке `canvas = pygame.Surface(...)` бетінде сақта, әр кадрда оны экранға blit жаса. Сызық үзілмес үшін `MOUSEMOTION` кезінде алдыңғы нүктеден жаңа нүктеге `draw.line` сал (`event.rel` көмектеседі).

### 6-тапсырма. «Тышқан аулау» ойыны
Әр 800 мс сайын (`USEREVENT` + `set_timer`) кездейсоқ жерде нысана пайда болады, 2 секундтан кейін жоғалады. Нысанаға басу — +1 ұпай, бос жерге басу — −1. Ұпай тақырыпта көрсетіледі. 30 секундтан кейін ойын аяқталып, нәтиже консольге шығады.

### 7-тапсырма. Түстерді сұрыптау (drag and drop)
Төменде 6 түрлі-түсті шаршы (қызыл және көк аралас), жоғарыда екі «себет»: қызыл және көк. Шаршыларды сүйреп, сәйкес себетке апару керек. Барлық шаршы дұрыс орналасқанда — «Жарайсың!».

## ★ Олимпиадалық / шығармашылық тапсырма

**А. «Сапер» торы.** 10×10 тор, 12 жасырын мина. Сол жақ батырма — ұяшықты ашу (айналасындағы мина санын көрсету — әзірге консольге немесе түспен), оң жақ батырма — жалауша қою/алу. Минаға басса — ойын аяқталады.

**Ә. Ату (Angry Birds қағидасы).** Тінтуірмен «тартып» жіберу: басқан нүкте мен жіберген нүкте арасындағы вектор доптың бастапқы жылдамдығы болады (кері бағытта), доп гравитациямен ұшады.

## Үй тапсырмасы

1. 5-тапсырмадағы «Сурет салғышқа» `S` пернесімен суретті PNG-ге сақтауды қос.
2. Командалық жобаңда тінтуір қолданыла ма? Қай әрекеттерге? Жазып кел.
3. 11-сабақтағы (ОБП) класс, `__init__`, әдістер тақырыбын қайтала — келесі сабақта спрайт-кластар жазамыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–2-тапсырмалар:**

```python
import random
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 400))
clock = pygame.time.Clock()
dots = []
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            if event.button == 1:
                color = (random.randint(50, 255), random.randint(50, 255),
                         random.randint(50, 255))
                dots.append((event.pos, color))
            elif event.button == 3:
                dots.clear()
    x, y = pygame.mouse.get_pos()
    pygame.display.set_caption(f"X: {x}  Y: {y}")
    bg = (150, 0, 0) if pygame.mouse.get_pressed()[0] else (0, 0, 0)
    screen.fill(bg)
    for pos, color in dots:
        pygame.draw.circle(screen, color, pos, 15)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**3–4-тапсырмалар:**

```python
import random
import pygame

WIDTH, HEIGHT = 700, 500
SPEED = 3

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Батырма және ізшіл")
clock = pygame.time.Clock()
button = pygame.Rect(0, 0, 220, 70)
button.center = (WIDTH // 2, HEIGHT // 2)
bg = (30, 30, 30)
hx, hy = 50, 50
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if button.collidepoint(event.pos):
                bg = (random.randint(0, 120), random.randint(0, 120), random.randint(0, 120))
    mx, my = pygame.mouse.get_pos()
    if abs(mx - hx) > SPEED:
        hx += SPEED if mx > hx else -SPEED
    if abs(my - hy) > SPEED:
        hy += SPEED if my > hy else -SPEED
    hover = button.collidepoint((mx, my))
    screen.fill(bg)
    pygame.draw.rect(screen, (90, 200, 255) if hover else (40, 120, 200), button,
                     border_radius=15)
    pygame.draw.circle(screen, (255, 200, 0), (hx, hy), 18)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**5-тапсырма. Сурет салғыш (үй тапсырмасындағы S пернесімен):**

```python
import pygame

WIDTH, HEIGHT = 900, 600
PALETTE = {pygame.K_1: (0, 0, 0), pygame.K_2: (220, 0, 0), pygame.K_3: (0, 160, 0),
           pygame.K_4: (0, 0, 220), pygame.K_5: (255, 200, 0)}

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
canvas = pygame.Surface((WIDTH, HEIGHT))
canvas.fill((255, 255, 255))
clock = pygame.time.Clock()
color = (0, 0, 0)
brush = 6
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key in PALETTE:
                color = PALETTE[event.key]
            elif event.key == pygame.K_c:
                canvas.fill((255, 255, 255))
            elif event.key == pygame.K_s:
                pygame.image.save(canvas, "suret.png")
                print("Сақталды")
        elif event.type == pygame.MOUSEWHEEL:
            brush = max(2, min(40, brush + event.y * 2))
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            pygame.draw.circle(canvas, color, event.pos, brush // 2)
        elif event.type == pygame.MOUSEMOTION and event.buttons[0]:
            start = (event.pos[0] - event.rel[0], event.pos[1] - event.rel[1])
            pygame.draw.line(canvas, color, start, event.pos, brush)
            pygame.draw.circle(canvas, color, event.pos, brush // 2)
    pygame.display.set_caption(f"Қылқалам: {brush}px | 1-5 түс, C тазалау, S сақтау")
    screen.blit(canvas, (0, 0))
    pygame.draw.circle(screen, color, pygame.mouse.get_pos(), brush // 2, 1)
    pygame.display.flip()
    clock.tick(120)
pygame.quit()
```

Файл атауын латын әрпімен жазған дұрыс (`suret.png`) — кейбір жүйелерде кириллицалы файл атаулары қиындық тудырады.

**6-тапсырма. Тышқан аулау:**

```python
import random
import pygame

WIDTH, HEIGHT = 700, 500
SPAWN = pygame.USEREVENT + 1
GAME_TIME = 30_000
LIFETIME = 2000

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
pygame.time.set_timer(SPAWN, 800)
targets = []          # [rect, пайда болған уақыты]
score = 0
start = pygame.time.get_ticks()
running = True
while running:
    now = pygame.time.get_ticks()
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == SPAWN:
            rect = pygame.Rect(random.randint(0, WIDTH - 50), random.randint(0, HEIGHT - 50), 50, 50)
            targets.append([rect, now])
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            hit = False
            for item in targets[:]:
                if item[0].collidepoint(event.pos):
                    targets.remove(item)
                    hit = True
                    break
            score += 1 if hit else -1
    targets = [t for t in targets if now - t[1] < LIFETIME]
    left = max(0, (GAME_TIME - (now - start)) // 1000)
    pygame.display.set_caption(f"Ұпай: {score} | Уақыт: {left} с")
    if now - start >= GAME_TIME:
        print("Ойын аяқталды! Ұпай:", score)
        running = False
    screen.fill((30, 60, 30))
    for rect, _ in targets:
        pygame.draw.ellipse(screen, (150, 150, 150), rect)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**7-тапсырма. Түстерді сұрыптау:**

```python
import random
import pygame

WIDTH, HEIGHT = 700, 500
RED, BLUE = (220, 50, 50), (50, 90, 220)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Шаршыларды себеттерге апар")
clock = pygame.time.Clock()
baskets = {RED: pygame.Rect(80, 40, 250, 160), BLUE: pygame.Rect(370, 40, 250, 160)}
squares = []
for i in range(6):
    color = RED if i % 2 == 0 else BLUE
    squares.append([pygame.Rect(60 + i * 100, 380, 60, 60), color])
random.shuffle(squares)
for i, sq in enumerate(squares):
    sq[0].x = 60 + i * 100
dragged = None
offset = (0, 0)
done = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            for sq in reversed(squares):
                if sq[0].collidepoint(event.pos):
                    dragged = sq
                    offset = (sq[0].x - event.pos[0], sq[0].y - event.pos[1])
                    break
        elif event.type == pygame.MOUSEBUTTONUP and event.button == 1:
            dragged = None
            ok = all(baskets[c].contains(r) for r, c in squares)
            if ok and not done:
                print("Жарайсың!")
                done = True
        elif event.type == pygame.MOUSEMOTION and dragged:
            dragged[0].topleft = (event.pos[0] + offset[0], event.pos[1] + offset[1])
    screen.fill((245, 245, 245))
    for color, rect in baskets.items():
        pygame.draw.rect(screen, color, rect, 4, border_radius=10)
    for rect, color in squares:
        pygame.draw.rect(screen, color, rect)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`rect.contains(other)` — `other` толығымен `rect` ішінде болса, `True`.

**★ А. Сапер торы (негізгі логика):**

```python
import random
import pygame

N, CELL, MINES = 10, 40, 12

pygame.init()
screen = pygame.display.set_mode((N * CELL, N * CELL))
pygame.display.set_caption("Сапер")
clock = pygame.time.Clock()
mines = set(random.sample([(c, r) for c in range(N) for r in range(N)], MINES))
opened, flags = set(), set()
game_over = False


def neighbours(c, r):
    return sum((c + dc, r + dr) in mines
               for dc in (-1, 0, 1) for dr in (-1, 0, 1) if dc or dr)


running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and not game_over:
            cell = (event.pos[0] // CELL, event.pos[1] // CELL)
            if event.button == 1 and cell not in flags:
                if cell in mines:
                    print("Бум! Ойын аяқталды")
                    game_over = True
                else:
                    opened.add(cell)
                    print("Айналасында мина:", neighbours(*cell))
            elif event.button == 3 and cell not in opened:
                flags ^= {cell}
    screen.fill((180, 180, 180))
    for c in range(N):
        for r in range(N):
            rect = (c * CELL + 1, r * CELL + 1, CELL - 2, CELL - 2)
            if (c, r) in opened:
                shade = 255 - neighbours(c, r) * 30
                pygame.draw.rect(screen, (shade, shade, 255), rect)
            elif (c, r) in flags:
                pygame.draw.rect(screen, (230, 60, 60), rect)
            elif game_over and (c, r) in mines:
                pygame.draw.rect(screen, (0, 0, 0), rect)
            else:
                pygame.draw.rect(screen, (120, 120, 120), rect)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

(Сандарды ұяшыққа жазу 42-сабақтан кейін қосылады.)

**★ Ә. Ату:**

```python
import pygame

WIDTH, HEIGHT = 900, 500
GRAVITY = 0.4

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Тартып жібер!")
clock = pygame.time.Clock()
start_pos = (120, 380)
ball = list(start_pos)
vel = [0.0, 0.0]
flying = False
pulling = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1 and not flying:
            pulling = True
        elif event.type == pygame.MOUSEBUTTONUP and event.button == 1 and pulling:
            pulling = False
            flying = True
            mx, my = event.pos
            vel = [(start_pos[0] - mx) * 0.15, (start_pos[1] - my) * 0.15]
    if flying:
        vel[1] += GRAVITY
        ball[0] += vel[0]
        ball[1] += vel[1]
        if ball[1] > HEIGHT or ball[0] > WIDTH or ball[0] < 0:
            ball = list(start_pos)
            flying = False
    screen.fill((180, 220, 255))
    pygame.draw.rect(screen, (80, 160, 80), (0, 420, WIDTH, 80))
    if pulling:
        pygame.draw.line(screen, (90, 50, 20), start_pos, pygame.mouse.get_pos(), 3)
    pygame.draw.circle(screen, (220, 30, 30), (int(ball[0]), int(ball[1])), 15)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

</details>
