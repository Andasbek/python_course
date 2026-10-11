# Сабақ 36. Геометриялық фигуралар салу: тіктөртбұрыш, шеңбер, сызық.

## Тақырыптар

1. pygame.draw модулі
2. Тіктөртбұрыш
3. Шеңбер және дөңгелек
4. Сызықтар
5. Көпбұрыш, эллипс, доға
6. pygame.Rect объектісі
7. Салу реті — қабаттар
8. Толық мысал: «Ауылдағы үй»
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. pygame.draw модулі

Барлық функциялардың алғашқы екі аргументі бірдей: **қайда саламыз** (Surface) және **қандай түспен**.

| Функция | Не салады | Негізгі аргументтер |
|---|---|---|
| `draw.rect(surface, color, rect, width=0, border_radius=0)` | Тіктөртбұрыш | `rect = (x, y, w, h)` |
| `draw.circle(surface, color, center, radius, width=0)` | Шеңбер/дөңгелек | `center = (x, y)` |
| `draw.line(surface, color, start, end, width=1)` | Кесінді | екі нүкте |
| `draw.lines(surface, color, closed, points, width=1)` | Сынық сызық | нүктелер тізімі |
| `draw.polygon(surface, color, points, width=0)` | Көпбұрыш | нүктелер тізімі |
| `draw.ellipse(surface, color, rect, width=0)` | Эллипс | сыртқы тіктөртбұрыш |
| `draw.arc(surface, color, rect, start, stop, width=1)` | Доға | бұрыштар радианмен |
| `draw.aaline(surface, color, start, end)` | Тегістелген сызық | антиалиасинг |

**`width` параметрі:** `0` (әдепкі) — фигура **толтырылады**; `> 0` — тек осы қалыңдықтағы **контур** салынады.

## 2. Тіктөртбұрыш

```python
pygame.draw.rect(screen, color, (x, y, ені, биіктігі), width, border_radius=...)
```

`(x, y)` — **сол жақ жоғарғы бұрыш**.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 300))
pygame.display.set_caption("Тіктөртбұрыштар")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((255, 255, 255))
    pygame.draw.rect(screen, (220, 50, 50), (50, 50, 150, 100))          # толтырылған
    pygame.draw.rect(screen, (50, 50, 220), (230, 50, 150, 100), 5)      # контур, қалыңдығы 5
    pygame.draw.rect(screen, (50, 180, 50), (410, 50, 150, 100),
                     border_radius=20)                                   # бұрыштары дөңгелек
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

`border_radius` — батырмалар мен панельдерді әдемі ету үшін өте ыңғайлы (мысалы, ойын мәзірінің батырмалары).

## 3. Шеңбер және дөңгелек

```python
pygame.draw.circle(screen, color, (cx, cy), radius, width)
```

Шеңбер **ортасы** және **радиусы** арқылы беріледі (тіктөртбұрыштан айырмашылығы осы!).

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 400))
pygame.display.set_caption("Нысана")
clock = pygame.time.Clock()
colors = [(255, 0, 0), (255, 255, 255)]
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((200, 200, 200))
    for i, radius in enumerate(range(150, 0, -30)):
        pygame.draw.circle(screen, colors[i % 2], (200, 200), radius)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

Мұнда шеңберлерді **үлкенінен кішісіне** қарай салдық. Керісінше салсақ, үлкен дөңгелек кішілерін жауып тастар еді.

## 4. Сызықтар

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
pygame.display.set_caption("Сызықтар")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((0, 0, 0))
    pygame.draw.line(screen, (255, 255, 255), (0, 0), (399, 299), 3)   # диагональ
    for x in range(0, 400, 40):                                        # тік тор сызықтары
        pygame.draw.line(screen, (60, 60, 60), (x, 0), (x, 299))
    zigzag = [(20, 250), (80, 200), (140, 250), (200, 200), (260, 250)]
    pygame.draw.lines(screen, (0, 255, 0), False, zigzag, 4)          # сынық сызық
    pygame.draw.aaline(screen, (255, 200, 0), (0, 299), (399, 0))      # тегіс сызық
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

`draw.lines(..., closed, points)`: `closed=True` болса, соңғы нүкте біріншімен қосылады.

## 5. Көпбұрыш, эллипс, доға

```python
import math
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 250))
pygame.display.set_caption("Көпбұрыш, эллипс, доға")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((255, 255, 255))
    pygame.draw.polygon(screen, (255, 140, 0), [(100, 30), (180, 200), (20, 200)])  # үшбұрыш
    pygame.draw.ellipse(screen, (120, 0, 200), (220, 60, 160, 100))                 # эллипс
    pygame.draw.rect(screen, (200, 200, 200), (220, 60, 160, 100), 1)              # оның «қорабы»
    pygame.draw.arc(screen, (0, 120, 255), (420, 50, 150, 150), 0, math.pi, 6)    # жарты шеңбер доға
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

- `ellipse` — берілген тіктөртбұрыштың ішіне сызылған эллипс; ені мен биіктігі тең болса — дөңгелек.
- `arc` бұрыштары **радианмен**: `0` — оң жақ, `math.pi / 2` — жоғары, `math.pi` — сол жақ. Бұрышты градустан айналдыру: `math.radians(90)`.

## 6. pygame.Rect объектісі

Ойындарда тіктөртбұрыш — ең маңызды объект: кейіпкердің орны, қақтығыс аймағы, батырма — бәрі `Rect`. Ол тек координаталарды сақтайды (өзі салынбайды).

```python
import pygame

player = pygame.Rect(100, 50, 40, 60)       # x, y, ені, биіктігі
print(player)
print("Сол:", player.left, "Оң:", player.right)
print("Жоғары:", player.top, "Төмен:", player.bottom)
print("Ортасы:", player.center)

player.center = (400, 300)                   # ортасын жылжыту
print("Жаңа орны:", player.topleft)
player.move_ip(10, -5)                       # орнында жылжыту (in place)
print("Жылжығаннан кейін:", player.topleft)
```

Нәтижесі:
```text
<rect(100, 50, 40, 60)>
Сол: 100 Оң: 140
Жоғары: 50 Төмен: 110
Ортасы: (120, 80)
Жаңа орны: (380, 270)
Жылжығаннан кейін: (390, 265)
```

**Rect атрибуттары (схема):**

```text
 topleft ●───── midtop ─────● topright
         │                  │
 midleft ●     center ●     ● midright
         │                  │
 bottomleft ●── midbottom ──● bottomright
```

| Атрибут | Мағынасы |
|---|---|
| `x`, `y`, `left`, `top` | Сол жақ жоғарғы бұрыштың координаталары |
| `right`, `bottom` | Оң жақ шекарасы (`x + w`), төменгі шекарасы (`y + h`) |
| `width`, `height`, `size` | Өлшемдері |
| `center`, `centerx`, `centery` | Ортасы |
| `topleft`, `bottomright`, `midtop` т.б. | Бұрыштар мен қабырғалардың орталары |

**Пайдасы:** объектіні экран ортасына қою енді бір жол: `rect.center = (WIDTH // 2, HEIGHT // 2)`. Еденге «қою»: `rect.bottom = HEIGHT`.

Rect-ті салу: `pygame.draw.rect(screen, color, player)`.

## 7. Салу реті — қабаттар

Pygame-де кейін салынған фигура алдыңғысының **үстіне** түседі (суретшінің кенебіндегідей). Сондықтан пейзаж салу реті: **аспан → күн → тау → жер → үй → терезе**.

## 8. Толық мысал: «Ауылдағы үй»

```python
import pygame

WIDTH, HEIGHT = 800, 600
SKY = (135, 206, 235)
GRASS = (60, 170, 60)
SUN = (255, 215, 0)
WALL = (205, 133, 63)
ROOF = (178, 34, 34)
GLASS = (173, 216, 230)
BROWN = (101, 67, 33)
LEAVES = (34, 120, 34)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Ауылдағы үй")
clock = pygame.time.Clock()

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    screen.fill(SKY)                                              # 1. аспан
    pygame.draw.circle(screen, SUN, (680, 100), 60)               # 2. күн
    for dx, dy in [(1, 0), (1, 1), (0, 1), (-1, 1),
                   (-1, 0), (-1, -1), (0, -1), (1, -1)]:          #    күн сәулелері
        pygame.draw.line(screen, SUN, (680, 100),
                         (680 + 90 * dx, 100 + 90 * dy), 3)
    pygame.draw.rect(screen, GRASS, (0, 450, WIDTH, 150))         # 3. жер
    house = pygame.Rect(0, 0, 260, 200)                           # 4. үй қабырғасы
    house.midbottom = (300, 470)
    pygame.draw.rect(screen, WALL, house)
    pygame.draw.polygon(screen, ROOF, [(house.left - 20, house.top),
                                       (house.centerx, house.top - 120),
                                       (house.right + 20, house.top)])   # 5. шатыр
    window = pygame.Rect(0, 0, 70, 70)                            # 6. терезе
    window.center = (house.left + 70, house.centery)
    pygame.draw.rect(screen, GLASS, window)
    pygame.draw.rect(screen, BROWN, window, 4)
    pygame.draw.line(screen, BROWN, window.midtop, window.midbottom, 3)
    pygame.draw.line(screen, BROWN, window.midleft, window.midright, 3)
    door = pygame.Rect(0, 0, 60, 110)                             # 7. есік
    door.bottomright = (house.right - 30, house.bottom)
    pygame.draw.rect(screen, BROWN, door, border_radius=8)
    pygame.draw.circle(screen, SUN, (door.right - 12, door.centery), 5)
    pygame.draw.rect(screen, BROWN, (600, 330, 30, 130))          # 8. ағаш діңі
    pygame.draw.circle(screen, LEAVES, (615, 300), 70)            #    ағаш жапырағы

    pygame.display.flip()
    clock.tick(30)

pygame.quit()
```

Назар аударыңыз: үйдің терезесі мен есігі `house` тіктөртбұрышына **қатысты** орналастырылған. Енді `house.midbottom` мәнін өзгертсек, бүкіл үй терезесі мен есігімен бірге жылжиды!

## Жиі кездесетін қателер

**1. Шеңбердің ортасы орнына бұрышын беру** — шеңбер жартылай экраннан шығып кетеді. Шеңбер үшін — **орта**, тіктөртбұрыш үшін — **сол жақ жоғарғы бұрыш**.

**2. Аргументтер реті шатасқан**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((200, 200))
pygame.draw.circle(screen, (100, 100), 50, (255, 0, 0))   # қате: түс пен орта ауысқан
```
Дұрысы: `pygame.draw.circle(screen, (255, 0, 0), (100, 100), 50)` — алдымен бет, түс, сосын геометрия.

**3. Контур қалыңдығы радиустан үлкен** — `width=60`, `radius=50` болса, толтырылған дөңгелек салынады немесе ештеңе салынбайды. `width` < радиус.

**4. Фонды фигуралардан кейін бояу** — `screen.fill()` бәрін жауып тастайды. Фон — әр кадрдың **ең бірінші** әрекеті.

**5. `arc` бұрыштарын градуспен беру** — `arc(..., 0, 180)` күткендей жарты шеңбер салмайды. Дұрысы: `0, math.pi` немесе `math.radians(180)`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `pygame.draw` | Геометриялық фигуралар салатын модуль |
| Толтырылған фигура / контур | `width=0` / `width>0` |
| `pygame.Rect` | Тіктөртбұрышты аймақтың координаталары мен өлшемдерін сақтайтын объект |
| `center`, `midbottom`, `topleft` | Rect-тің «тұтқа» нүктелері |
| Көпбұрыш (polygon) | Нүктелер тізімімен берілетін тұйық фигура |
| Радиан | Бұрыштың өлшем бірлігі: 180° = π |
| Қабаттар (салу реті) | Кейін салынған фигура алдыңғысын жабады |
| Антиалиасинг | Сызық жиегін тегістеу (`aaline`) |

## Ескерту

- `pygame.draw` функцияларындағы `width=0` (әдепкі) — фигураны толық бояу, `width > 0` — тек контур. Шеңберде `width` радиустан үлкен болса, ештеңе салынбайды.
- `border_radius` параметрі Pygame 2.0.0 нұсқасында пайда болған; Pygame 1.9-да бұл параметр жоқ.
- `pygame.draw.line` және `circle` сызықтарды тегістемейді (антиалиасинг жоқ). Тегіс сызық үшін `pygame.draw.aaline()` бар.
- Барлық `pygame.draw` функциялары өзгерген аймақтың `Rect`-ін қайтарады — оны `pygame.display.update(rect)` үшін қолдануға болады.
- `pygame.draw` құжаттамасы: https://www.pygame.org/docs/ref/draw.html
