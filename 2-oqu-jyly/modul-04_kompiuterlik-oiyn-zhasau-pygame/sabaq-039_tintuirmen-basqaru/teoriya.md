# Сабақ 39. Тінтуірмен басқару. Оқиғаларды өңдеу (event loop).

## Тақырыптар

1. Тінтуір оқиғалары
2. Тінтуір күйі: pygame.mouse
3. Объектіге басылды ма? collidepoint()
4. Батырма жасау: hover және click
5. Сүйреп апару (drag and drop)
6. Оқиғаларды өңдеу циклі (event loop) тереңірек
7. Өз оқиғамыз: USEREVENT және таймер
8. Басқару тәсілдерін салыстыру
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Тінтуір оқиғалары

| Оқиға | Қашан | Пайдалы атрибуттар |
|---|---|---|
| `MOUSEBUTTONDOWN` | Батырма басылғанда | `event.pos`, `event.button` |
| `MOUSEBUTTONUP` | Батырма жіберілгенде | `event.pos`, `event.button` |
| `MOUSEMOTION` | Тінтуір жылжығанда | `event.pos`, `event.rel`, `event.buttons` |
| `MOUSEWHEEL` | Дөңгелек айналғанда | `event.y` (+1 жоғары, −1 төмен) |

**`event.button` мәндері:**

| Мән | Батырма |
|---|---|
| 1 | Сол жақ |
| 2 | Ортаңғы (дөңгелекті басу) |
| 3 | Оң жақ |

- `event.pos` — басылған нүктенің `(x, y)` координатасы;
- `event.rel` — алдыңғы позициядан қаншаға жылжығаны `(dx, dy)`;
- `event.buttons` — `MOUSEMOTION` кезінде қай батырмалар басылып тұрғаны `(сол, орта, оң)`.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((500, 400))
pygame.display.set_caption("Тінтуір оқиғалары")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            print("Басылды:", event.button, "нүкте:", event.pos)
        elif event.type == pygame.MOUSEBUTTONUP:
            print("Жіберілді:", event.button)
        elif event.type == pygame.MOUSEWHEEL:
            print("Дөңгелек:", event.y)
    screen.fill((40, 40, 40))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

Нәтижесі (мысал):
```text
Басылды: 1 нүкте: (120, 85)
Жіберілді: 1
Басылды: 3 нүкте: (300, 200)
Жіберілді: 3
Дөңгелек: 1
Дөңгелек: -1
```

## 2. Тінтуір күйі: pygame.mouse

Пернетақтадағыдай, тінтуірдің де «күйін» кез келген уақытта сұрауға болады:

| Функция | Не қайтарады |
|---|---|
| `pygame.mouse.get_pos()` | Курсордың қазіргі `(x, y)` позициясы |
| `pygame.mouse.get_pressed()` | `(сол, орта, оң)` — `True`/`False` |
| `pygame.mouse.set_visible(False)` | Жүйелік курсорды жасыру |
| `pygame.mouse.set_pos((x, y))` | Курсорды жылжыту |

**Мысал: курсордың орнына «көздеу» салу.** Жүйелік курсорды жасырып, оның орнына өз белгімізді саламыз:

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Көздеу")
pygame.mouse.set_visible(False)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    x, y = pygame.mouse.get_pos()
    left, middle, right = pygame.mouse.get_pressed()
    color = (255, 0, 0) if left else (0, 255, 0)
    screen.fill((20, 20, 20))
    pygame.draw.circle(screen, color, (x, y), 20, 2)
    pygame.draw.line(screen, color, (x - 30, y), (x + 30, y), 2)
    pygame.draw.line(screen, color, (x, y - 30), (x, y + 30), 2)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Қашан не қолданамыз?**
- бір рет «нұқу» (ату, батырманы басу) — `MOUSEBUTTONDOWN` оқиғасы;
- басып тұрып салу, көздеу, кейіпкердің курсорға қарай жүруі — `get_pos()` / `get_pressed()`.

## 3. Объектіге басылды ма? collidepoint()

`rect.collidepoint(point)` — нүкте тіктөртбұрыштың ішінде болса, `True` қайтарады. Бұл — кез келген батырманың, нысананың, карточканың негізі.

```python
import pygame

button = pygame.Rect(100, 100, 200, 60)
print(button.collidepoint((150, 120)))
print(button.collidepoint((50, 50)))
```

Нәтижесі:
```text
True
False
```

**Дөңгелек нысана үшін** — қашықтық формуласы (Пифагор теоремасы):

```python
import math

def inside_circle(point, center, radius):
    return math.dist(point, center) <= radius

print(inside_circle((105, 100), (100, 100), 20))
print(inside_circle((130, 100), (100, 100), 20))
```

Нәтижесі:
```text
True
False
```

## 4. Батырма жасау: hover және click

Ойын батырмасы курсор үстіне келгенде түсін өзгертеді (hover), басылғанда әрекет орындайды:

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
pygame.display.set_caption("Батырма")
clock = pygame.time.Clock()
button = pygame.Rect(0, 0, 200, 60)
button.center = (200, 150)
clicks = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if button.collidepoint(event.pos):
                clicks += 1
                pygame.display.set_caption(f"Басылды: {clicks} рет")
    hover = button.collidepoint(pygame.mouse.get_pos())
    color = (80, 180, 255) if hover else (40, 110, 200)
    screen.fill((30, 30, 30))
    pygame.draw.rect(screen, color, button, border_radius=12)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

Батырмаға мәтін жазу үшін `pygame.font` модулі қолданылады; мұндай батырмалардан ойын мәзірін құруға болады.

## 5. Сүйреп апару (drag and drop)

Сүйреп апару үш оқиғадан тұрады:
1. `MOUSEBUTTONDOWN` — объектінің үстінде басылса, «ұстап алдық» (`dragging = True`) және курсор мен объект бұрышының арасындағы айырманы (`offset`) есте сақтаймыз;
2. `MOUSEMOTION` — ұстап тұрсақ, объектіні курсормен бірге жылжытамыз;
3. `MOUSEBUTTONUP` — «жібердік» (`dragging = False`).

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Сүйреп апару")
clock = pygame.time.Clock()
box = pygame.Rect(250, 150, 100, 100)
dragging = False
offset_x = offset_y = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if box.collidepoint(event.pos):
                dragging = True
                offset_x = box.x - event.pos[0]
                offset_y = box.y - event.pos[1]
        elif event.type == pygame.MOUSEBUTTONUP and event.button == 1:
            dragging = False
        elif event.type == pygame.MOUSEMOTION and dragging:
            box.x = event.pos[0] + offset_x
            box.y = event.pos[1] + offset_y
    screen.fill((240, 240, 240))
    pygame.draw.rect(screen, (255, 150, 0) if dragging else (0, 150, 255), box)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`offset` болмаса, объект курсорға сол жақ жоғарғы бұрышымен «секіріп» жабысар еді.

## 6. Оқиғаларды өңдеу циклі (event loop) тереңірек

**Оқиғалар кезегі.** Операциялық жүйе пайдаланушының барлық әрекетін оқиға ретінде кезекке (queue) қояды. Біз әр кадрда кезекті босатамыз:

```text
Пайдаланушы  ──▶  [ MOUSEMOTION | KEYDOWN | MOUSEBUTTONDOWN | ... ]  ──▶  pygame.event.get()
                         оқиғалар кезегі (FIFO: бірінші кірген — бірінші шығады)
```

| Функция | Не істейді |
|---|---|
| `pygame.event.get()` | Барлық оқиғаны алып, кезекті тазалайды (ойындарда негізгі) |
| `pygame.event.wait()` | Оқиға келгенше бағдарламаны тоқтатып күтеді (ойын емес бағдарламаларға) |
| `pygame.event.post(event)` | Кезекке өз оқиғамызды қосу |
| `pygame.event.set_blocked(type)` | Белгілі бір оқиға түрін елемеу |

**Жақсы құрылым — оқиғаларды функцияға шығару.** Цикл ұзарған сайын оқиғаларды бөлек функцияда өңдеген ыңғайлы:

```python
import pygame


def handle_events(state):
    """Оқиғаларды өңдейді; ойынды жалғастыру керек болса True қайтарады."""
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            return False
        if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
            return False
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            state["points"].append(event.pos)
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 3:
            state["points"].clear()
    return True


pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Сол — нүкте, оң — тазалау")
clock = pygame.time.Clock()
state = {"points": []}
while handle_events(state):
    screen.fill((255, 255, 255))
    if len(state["points"]) > 1:
        pygame.draw.lines(screen, (0, 0, 200), False, state["points"], 3)
    for p in state["points"]:
        pygame.draw.circle(screen, (200, 0, 0), p, 5)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

## 7. Өз оқиғамыз: USEREVENT және таймер

`pygame.time.set_timer(event_type, ms)` — әр `ms` миллисекунд сайын кезекке өз оқиғамызды қосады. Мысалы, әр секунд сайын жаңа нысана пайда болуы үшін:

```python
import random
import pygame

SPAWN = pygame.USEREVENT + 1          # өз оқиға түріміз

pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Нысаналар: басып жой!")
pygame.time.set_timer(SPAWN, 1000)    # әр 1000 мс сайын
clock = pygame.time.Clock()
targets = []
score = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == SPAWN:
            rect = pygame.Rect(random.randint(0, 560), random.randint(0, 360), 40, 40)
            targets.append(rect)
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            for t in targets[:]:
                if t.collidepoint(event.pos):
                    targets.remove(t)
                    score += 1
                    pygame.display.set_caption(f"Ұпай: {score}")
                    break
    screen.fill((20, 20, 40))
    for t in targets:
        pygame.draw.rect(screen, (255, 80, 80), t, border_radius=20)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`targets[:]` — тізімнің көшірмесі бойынша жүреміз, себебі цикл ішінде түпнұсқадан элемент өшіріп жатырмыз.

## 8. Басқару тәсілдерін салыстыру

| Құрылғы | Оқиға (бір рет) | Күй (үздіксіз) |
|---|---|---|
| Пернетақта | `KEYDOWN`, `KEYUP` | `pygame.key.get_pressed()` |
| Тінтуір | `MOUSEBUTTONDOWN`, `MOUSEBUTTONUP`, `MOUSEWHEEL` | `pygame.mouse.get_pos()`, `get_pressed()` |
| Таймер | `USEREVENT` + `set_timer` | `pygame.time.get_ticks()` |

## Жиі кездесетін қателер

**1. `MOUSEBUTTONDOWN`-ды батырма нөмірінсіз тексеру** — дөңгелекті айналдыру да ескі жүйелерде `button` 4/5 ретінде келеді, «ату» кездейсоқ орындалады. Сол жақ үшін: `event.button == 1`.

**2. `event.pos`-ты `MOUSEBUTTONDOWN`-нан тыс қолдану**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((300, 200))
for event in pygame.event.get():
    print(event.pos)   # AttributeError: QUIT, KEYDOWN оқиғаларында pos жоқ
```
Дұрысы: алдымен `event.type`-ты тексеру.

**3. Тізімнен цикл ішінде өшіру** — `for t in targets: targets.remove(t)` кейбір элементтерді өткізіп жібереді. Көшірме бойынша жүру керек: `for t in targets[:]`.

**4. Сүйреуде `offset` жоқ** — объект курсорға «секіреді».

**5. `get_pressed()` арқылы «бір рет» басуды тексеру** — 60 FPS-те бір басу 5–10 кадрға созылады, әрекет бірнеше рет орындалады. Бір реттік әрекет — оқиға арқылы.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `MOUSEBUTTONDOWN` / `UP` | Тінтуір батырмасы басылды / жіберілді |
| `MOUSEMOTION` | Тінтуір жылжыды; `rel` — жылжу шамасы |
| `MOUSEWHEEL` | Дөңгелек айналды; `y` — бағыты |
| `event.button` | 1 — сол, 2 — орта, 3 — оң |
| `collidepoint()` | Нүктенің Rect ішінде екенін тексеру |
| Hover | Курсор объектінің үстінде тұрған күй |
| Drag and drop | Объектіні тінтуірмен сүйреп апару |
| Оқиғалар кезегі | Әрекеттер тізбегі (FIFO), `event.get()` арқылы оқылады |
| `USEREVENT`, `set_timer` | Өз оқиға түрі және оны мерзімді жасау |

## Ескерту

- `event.button` мәндері: 1 — сол жақ, 2 — ортаңғы, 3 — оң жақ батырма. Pygame 2-де дөңгелек үшін бөлек `pygame.MOUSEWHEEL` оқиғасы бар (`event.y` — айналу бағыты).
- `pygame.mouse.get_pressed()` әдепкіде үш элементті кортеж қайтарады; `get_pressed(num_buttons=5)` қосымша батырмаларды да береді.
- `pygame.time.set_timer(event, millis)` таймерді іске қосады, `set_timer(event, 0)` — тоқтатады. Pygame 2.0.1-ден бастап `loops` параметрі бар: `loops=1` — бір рет қана.
- Өз оқиғаларыңыз үшін `USEREVENT + 1` орнына `pygame.event.custom_type()` қолданған сенімді — ол қайталанбайтын нөмір береді.
- Курсорды жасыру — `pygame.mouse.set_visible(False)`, жүйелік курсор түрін өзгерту — `pygame.mouse.set_cursor(pygame.SYSTEM_CURSOR_HAND)` (Pygame 2).
