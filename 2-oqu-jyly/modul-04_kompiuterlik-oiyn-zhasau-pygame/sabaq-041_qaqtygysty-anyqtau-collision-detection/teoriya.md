# Сабақ 41. Қақтығысты анықтау (collision detection).

## Тақырыптар

1. Тіктөртбұрыштардың қиылысуы (AABB)
2. Rect деңгейіндегі тексерістер
3. Спрайттар арасындағы қақтығыс
4. Толық мысал: «Ғарыш қорғаушысы» + қақтығыстар
5. Дәлірек қақтығыс: шеңбер және маска
6. Хитбокс (hitbox)
7. Қақтығысқа жауап: қабырғадан өтпеу
8. Ойындардағы қақтығыстар картасы
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Тіктөртбұрыштардың қиылысуы (AABB)

Ойындардағы ең жиі тәсіл — объектілерді осьтерге параллель тіктөртбұрыштармен (AABB — Axis-Aligned Bounding Box) салыстыру.

Екі тіктөртбұрыш A және B **қиылыспайды**, егер:
- A толығымен B-ның **сол жағында** болса: `A.right <= B.left`, немесе
- A толығымен B-ның **оң жағында**: `A.left >= B.right`, немесе
- A толығымен **жоғарыда**: `A.bottom <= B.top`, немесе
- A толығымен **төменде**: `A.top >= B.bottom`.

Қалған барлық жағдайда — **қиылысады**:

```python
def rects_overlap(a, b):
    """a, b — (x, y, w, h) кортеждері."""
    ax, ay, aw, ah = a
    bx, by, bw, bh = b
    return ax < bx + bw and ax + aw > bx and ay < by + bh and ay + ah > by

print(rects_overlap((0, 0, 50, 50), (40, 40, 50, 50)))
print(rects_overlap((0, 0, 50, 50), (60, 0, 50, 50)))
```

Нәтижесі:
```text
True
False
```

```text
 Қиылысады:                 Қиылыспайды:
 +------+                   +------+   +------+
 |  A   |                   |  A   |   |  B   |
 |   +--+---+               +------+   +------+
 +---+--+   |               A.right <= B.left
     |  B   |
     +------+
```

Pygame бұл тексерісті бізге дайын береді.

## 2. Rect деңгейіндегі тексерістер

| Әдіс | Не тексереді | Қайтарады |
|---|---|---|
| `a.colliderect(b)` | Екі Rect қиылыса ма | `True`/`False` |
| `a.collidepoint((x, y))` | Нүкте Rect ішінде ме | `True`/`False` |
| `a.collidelist(list_of_rects)` | Тізімдегі қай Rect-пен қиылысады | бірінші индекс немесе `-1` |
| `a.collidelistall(list_of_rects)` | Барлық қиылысатындар | индекстер тізімі |
| `a.contains(b)` | b толығымен a ішінде ме | `True`/`False` |

```python
import pygame

player = pygame.Rect(100, 100, 50, 50)
walls = [pygame.Rect(0, 0, 800, 20), pygame.Rect(140, 120, 20, 200),
         pygame.Rect(300, 300, 50, 50)]

print(player.colliderect(walls[1]))
print(player.collidelist(walls))
print(player.collidelistall(walls))
```

Нәтижесі:
```text
True
1
[1]
```

## 3. Спрайттар арасындағы қақтығыс

Спрайттармен жұмыс істегенде `pygame.sprite` модулінің функциялары ыңғайлы:

| Функция | Не істейді | Қайтарады |
|---|---|---|
| `spritecollide(sprite, group, dokill)` | Бір спрайт пен топтың қақтығысы | тиген спрайттар **тізімі** |
| `spritecollideany(sprite, group)` | Топтан кем дегенде біреуі тиді ме | бірінші тиген спрайт немесе `None` |
| `groupcollide(g1, g2, dokill1, dokill2)` | Екі топтың қақтығысы | `{g1 спрайты: [тиген g2 спрайттары]}` сөздігі |
| `collide_rect(s1, s2)` | Екі спрайттың rect-тері | `True`/`False` |

**`dokill` параметрі:** `True` болса, тиген спрайттар бірден `kill()` жасалады (барлық топтан өшеді).

**Типтік жағдайлар:**

```python
import pygame

# Ойыншы тиындарды жинайды: тиген тиындар жойылады, ұпай қосылады
hits = pygame.sprite.spritecollide(player, coins, True)
score += len(hits)

# Ойыншыға кез келген жау тиді ме? (жауды жоймаймыз)
if pygame.sprite.spritecollideany(player, enemies):
    lives -= 1

# Оқтар мен метеориттер: екеуі де жойылады
hits = pygame.sprite.groupcollide(meteors, bullets, True, True)
for meteor in hits:
    score += 10
```

> Бұл үзінді — үлгі; онда `player`, `coins`, `enemies` т.б. алдын ала құрылған деп есептеледі.

## 4. Толық мысал: «Ғарыш қорғаушысы» + қақтығыстар

```python
import random
import pygame

WIDTH, HEIGHT = 800, 600
SPAWN = pygame.USEREVENT + 1


class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((60, 40), pygame.SRCALPHA)
        pygame.draw.polygon(self.image, (0, 190, 255), [(30, 0), (60, 40), (0, 40)])
        self.rect = self.image.get_rect(midbottom=(WIDTH // 2, HEIGHT - 10))

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            self.rect.x -= 7
        if keys[pygame.K_RIGHT]:
            self.rect.x += 7
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))


class Bullet(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((6, 16))
        self.image.fill((255, 70, 70))
        self.rect = self.image.get_rect(midbottom=(x, y))

    def update(self):
        self.rect.y -= 10
        if self.rect.bottom < 0:
            self.kill()


class Meteor(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        size = random.randint(25, 55)
        self.image = pygame.Surface((size, size), pygame.SRCALPHA)
        pygame.draw.circle(self.image, (160, 160, 160), (size // 2, size // 2), size // 2)
        self.rect = self.image.get_rect(midbottom=(random.randint(30, WIDTH - 30), 0))
        self.speed = random.randint(2, 5)

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            self.kill()


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
pygame.time.set_timer(SPAWN, 600)

all_sprites = pygame.sprite.Group()
meteors = pygame.sprite.Group()
bullets = pygame.sprite.Group()
player = Player()
all_sprites.add(player)
score = 0
lives = 3

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == SPAWN:
            m = Meteor()
            all_sprites.add(m)
            meteors.add(m)
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            b = Bullet(player.rect.centerx, player.rect.top)
            all_sprites.add(b)
            bullets.add(b)

    all_sprites.update()

    # 1) Оқ метеоритке тиді: екеуі де жойылады, +10 ұпай
    hits = pygame.sprite.groupcollide(meteors, bullets, True, True)
    score += 10 * len(hits)

    # 2) Метеорит кемеге тиді: метеорит жойылады, −1 өмір
    crashed = pygame.sprite.spritecollide(player, meteors, True,
                                          pygame.sprite.collide_circle)
    if crashed:
        lives -= len(crashed)
        if lives <= 0:
            print("Ойын аяқталды! Ұпай:", score)
            running = False

    screen.fill((5, 5, 25))
    all_sprites.draw(screen)
    pygame.display.set_caption(f"Ұпай: {score} | Өмір: {lives}")
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

Нәтижесі (ойын соңында консольде, мысал):
```text
Ойын аяқталды! Ұпай: 230
```

## 5. Дәлірек қақтығыс: шеңбер және маска

Тіктөртбұрыш — дөрекі жуықтау. Дөңгелек метеориттің бұрышы кемеге «тимей-ақ тиіп» кетуі мүмкін — ойыншы ренжиді.

```text
 +---------+          Rect бойынша — тиді,
 |  .---.  |+------+  ал көзбен қарағанда — жоқ!
 | (     ) ||кеме  |
 |  '---'  |+------+
 +---------+
```

| Тәсіл | Функция | Дәлдігі | Жылдамдығы | Қашан |
|---|---|---|---|---|
| Тіктөртбұрыш | `collide_rect` (әдепкі) | Төмен | Өте жылдам | Көп объект, тіктөртбұрышты пішіндер |
| Шеңбер | `collide_circle` | Орташа | Жылдам | Дөңгелек объектілер (доп, метеорит) |
| Маска (пиксельдер) | `collide_mask` | Жоғары | Баяу | Күрделі пішінді суреттер |
| Кішірейтілген rect | `collide_rect_ratio(0.7)` | Орташа | Жылдам | «Әділ» хитбокс |

`spritecollide` және `groupcollide` функцияларының соңғы аргументі — `collided` — тексеру тәсілі:

```python
hits = pygame.sprite.spritecollide(player, meteors, False, pygame.sprite.collide_circle)
hits = pygame.sprite.spritecollide(player, meteors, False, pygame.sprite.collide_mask)
hits = pygame.sprite.spritecollide(player, meteors, False,
                                   pygame.sprite.collide_rect_ratio(0.7))
```

- `collide_circle` спрайттың `radius` атрибутын қолданады (болмаса — rect-тен есептейді). Оны өзіміз қойсақ, дәлірек: `self.radius = size // 2`.
- `collide_mask` спрайттың `mask` атрибутын қолданады: `self.mask = pygame.mask.from_surface(self.image)` — суреттің мөлдір емес пиксельдерінің «картасы».

## 6. Хитбокс (hitbox)

**Хитбокс** — қақтығыс тексерілетін аймақ. Ол суреттен кішірек болуы мүмкін, сонда ойын «әділ» сезіледі. `inflate()` әдісі Rect-ті үлкейтеді/кішірейтеді (ортасы сақталады):

```python
import pygame

image_rect = pygame.Rect(100, 100, 64, 64)
hitbox = image_rect.inflate(-20, -20)
print(hitbox)
```

Нәтижесі:
```text
<rect(110, 110, 44, 44)>
```

## 7. Қақтығысқа жауап: қабырғадан өтпеу

Қақтығысты анықтау — жартысы ғана. Екінші жартысы — **жауап**: тиын жоғалады, жау жарылады, ал **қабырға кейіпкерді өткізбейді**.

Ең сенімді тәсіл — қозғалысты осьтер бойынша **бөлек** жасау: алдымен X бойынша жылжып, тексеру; сосын Y бойынша жылжып, тексеру.

```python
import pygame


def move_with_walls(rect, dx, dy, walls):
    """rect-ті жылжытады, бірақ қабырғаларға кіргізбейді."""
    rect.x += dx
    for wall in walls:
        if rect.colliderect(wall):
            if dx > 0:
                rect.right = wall.left      # оңға жүрді — қабырғаның сол жағына тоқтайды
            elif dx < 0:
                rect.left = wall.right
    rect.y += dy
    for wall in walls:
        if rect.colliderect(wall):
            if dy > 0:
                rect.bottom = wall.top
            elif dy < 0:
                rect.top = wall.bottom


player = pygame.Rect(50, 50, 30, 30)
walls = [pygame.Rect(100, 0, 20, 200)]
move_with_walls(player, 40, 0, walls)
print(player.topleft)
```

Нәтижесі:
```text
(70, 50)
```

Кейіпкер 40 пиксель оңға жүруге тырысты, бірақ қабырғаның сол жағында (x = 100 − 30 = 70) тоқтады.

## 8. Ойындардағы қақтығыстар картасы

| Ойын | Не мен не | Жауап |
|---|---|---|
| Space Invaders | Оқ ↔ жау | Екеуі де жойылады, +ұпай |
| Mario | Кейіпкер ↔ платформа | Құламай тұрады |
| Pac-Man | Кейіпкер ↔ нүкте | Нүкте жиналады |
| Snake | Бас ↔ құйрық | Ойын аяқталады |
| Arkanoid | Доп ↔ кірпіш | Кірпіш жойылады, доп шағылысады |

## Жиі кездесетін қателер

**1. `spritecollide` бірінші аргументіне топ беру**
```python
import pygame
players = pygame.sprite.Group()
enemies = pygame.sprite.Group()
pygame.sprite.spritecollide(players, enemies, False)   # AttributeError: Group-та rect жоқ
```
Дұрысы: бірінші аргумент — **бір спрайт**; екі топ үшін — `groupcollide`.

**2. `dokill=True` абайсыз** — `spritecollide(player, enemies, True)` жауды жояды. Ойыншыға тиген жау жоғалмауы керек болса — `False`.

**3. Қақтығысты салудан кейін тексеру** — ойыншы бір кадр бойы жаудың «ішінде» көрінеді. Тәртіп: `update()` → қақтығыстар → салу.

**4. Әр кадрда өмірді азайту** — жау кемеге 30 кадр бойы тиіп тұрса, 30 өмір кетеді. Шешімі: тиген жауды жою немесе қысқа «қорғаныс уақытын» (invincibility, мысалы 1 секунд) енгізу.

**5. `collide_mask` үшін `mask` жоқ** — Pygame оны әр тексеріс сайын қайта есептейді (баяу). `__init__`-те бір рет `self.mask = pygame.mask.from_surface(self.image)` құрыңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Қақтығысты анықтау | Екі объектінің түйіскенін/қиылысқанын тексеру |
| AABB | Осьтерге параллель қоршаушы тіктөртбұрыш |
| `colliderect` | Екі Rect-тің қиылысуын тексеру |
| `spritecollide` | Спрайт пен топ қақтығысы, тиген спрайттар тізімі |
| `groupcollide` | Екі топ қақтығысы, сөздік қайтарады |
| `dokill` | Тиген спрайттарды автоматты жою жалауы |
| `collide_circle`, `collide_mask` | Шеңбер және пиксельдік дәл тексеріс |
| Хитбокс | Қақтығыс тексерілетін аймақ |
| Қақтығысқа жауап | Тексерістен кейінгі әрекет: жою, ұпай, тоқтату |

## Ескерту

- `colliderect()` тек шеттерімен жанасуды қақтығыс деп санамайды: `Rect`-тің оң және төменгі шекарасы аймаққа кірмейді.
- `pygame.sprite.collide_mask` маска әр шақыруда қайта есептелмеуі үшін спрайтқа `self.mask = pygame.mask.from_surface(self.image)` атрибутын алдын ала жасаңыз.
- Өте жылдам объект бір кадрда кедергіні «секіріп өтуі» мүмкін (tunneling). Шешімі — қозғалысты бірнеше кіші қадамға бөлу немесе жылдамдықты шектеу.
- `groupcollide()` сөздік қайтарады: кілт — бірінші топтың спрайты, мән — онымен соқтығысқан екінші топ спрайттарының тізімі.
- Қақтығыс функцияларының құжаттамасы: https://www.pygame.org/docs/ref/sprite.html#pygame.sprite.spritecollide
