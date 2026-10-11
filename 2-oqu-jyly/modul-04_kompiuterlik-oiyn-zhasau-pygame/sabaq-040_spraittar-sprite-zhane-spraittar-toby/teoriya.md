# Сабақ 40. Спрайттар (Sprite) және спрайттар тобы (Group). ОБП-ны ойында қолдану.

## Тақырыптар

1. Мәселе: объектілер көбейгенде
2. Спрайт деген не?
3. Алғашқы спрайт: Player
4. Спрайттар тобы: Group
5. Толық мысал: «Жұлдыз жаңбыры»
6. Конструкторға параметр беру
7. Мұрагерлік пен полиморфизм ойында
8. Спрайттардың «өмірлік циклі»
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Мәселе: объектілер көбейгенде

Қарапайым ойында кейіпкер бір `Rect` және бірнеше айнымалы болуы мүмкін. Ал 30 жау, 100 оқ, 20 тиын болса ше?

```python
enemies_x = [100, 200, 300]
enemies_y = [0, -50, -100]
enemies_speed = [2, 3, 4]
enemies_alive = [True, True, True]
```

Тізімдер «шашылып» кетеді: бір жауды өшіргенде төрт тізімнен бірдей индексті өшіру керек — қате жасау өте оңай.

**Шешім — ОБП:** әр жау — бір объект, оның барлық деректері (сурет, орны, жылдамдығы) мен әрекеттері (`update`) бір класта. Pygame бұл үшін дайын құрал береді — **спрайттар**.

## 2. Спрайт деген не?

**Спрайт** (sprite) — ойындағы кез келген қозғалатын немесе өзара әрекеттесетін объект: кейіпкер, жау, оқ, тиын, бонус.

`pygame.sprite.Sprite` — барлық спрайттардың ата-класы. Одан мұраланған класта **міндетті** түрде екі атрибут болуы керек:

| Атрибут | Не сақтайды |
|---|---|
| `self.image` | Спрайттың суреті (Surface) |
| `self.rect` | Орны мен өлшемі (Rect) |

Және әдетте бір әдіс:

| Әдіс | Не істейді |
|---|---|
| `update(self)` | Әр кадрда спрайттың күйін жаңартады (қозғалыс, анимация) |

## 3. Алғашқы спрайт: Player

```python
import pygame

WIDTH, HEIGHT = 800, 600


class Player(pygame.sprite.Sprite):
    """Ойыншы басқаратын кейіпкер."""

    def __init__(self):
        super().__init__()                              # ата-класты баптау — МІНДЕТТІ
        self.image = pygame.Surface((50, 50))
        self.image.fill((0, 200, 255))
        self.rect = self.image.get_rect(midbottom=(WIDTH // 2, HEIGHT - 10))
        self.speed = 6

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT]:
            self.rect.x += self.speed
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))
```

Сурет файлынан жүктеу үшін `self.image = load_image("player.png", (50, 50))` деп жазу жеткілікті (`load_image` — суретті қауіпсіз жүктейтін функция) — қалған код өзгермейді.

## 4. Спрайттар тобы: Group

**Group** — спрайттарды сақтайтын арнайы контейнер (тізімге ұқсас). Оның басты артықшылығы:

| Әдіс | Не істейді |
|---|---|
| `group.add(sprite)` | Спрайтты топқа қосу |
| `group.update()` | Топтағы **әр** спрайттың `update()` әдісін шақыру |
| `group.draw(surface)` | Әр спрайттың `image`-ін оның `rect` орнына blit жасау |
| `len(group)` | Топтағы спрайттар саны |
| `group.empty()` | Топты тазалау |
| `sprite.kill()` | Спрайтты **барлық** топтардан өшіру |
| `sprite.alive()` | Спрайт әлі қандай да бір топта ма |
| `for s in group:` | Спрайттарды аралап шығу |

**Аналогия:** Group — оркестр дирижері. Ол бір қимылмен барлық музыканттарға белгі береді (`update()`), әрқайсысына жеке айтпайды.

Бір спрайт бірнеше топта бола алады. Әдетте:
- `all_sprites` — салу және жаңарту үшін бәрі;
- `enemies`, `bullets`, `coins` — қақтығыстарды тексеру үшін.

## 5. Толық мысал: «Жұлдыз жаңбыры»

```python
import random
import pygame

WIDTH, HEIGHT = 800, 600
FPS = 60


class Player(pygame.sprite.Sprite):
    """Төменде солға-оңға жүретін ойыншы."""

    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((60, 30), pygame.SRCALPHA)
        pygame.draw.rect(self.image, (0, 200, 255), (0, 0, 60, 30), border_radius=8)
        self.rect = self.image.get_rect(midbottom=(WIDTH // 2, HEIGHT - 10))
        self.speed = 7

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            self.rect.x += self.speed
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))


class Star(pygame.sprite.Sprite):
    """Жоғарыдан түсетін жұлдыз."""

    def __init__(self):
        super().__init__()
        size = random.randint(10, 30)
        self.image = pygame.Surface((size, size), pygame.SRCALPHA)
        pygame.draw.circle(self.image, (255, 230, 0), (size // 2, size // 2), size // 2)
        self.rect = self.image.get_rect(
            midbottom=(random.randint(20, WIDTH - 20), 0))
        self.speed = random.randint(2, 6)

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:      # экраннан шықса — жою
            self.kill()


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()

all_sprites = pygame.sprite.Group()
stars = pygame.sprite.Group()
player = Player()
all_sprites.add(player)

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    if random.random() < 0.05:          # шамамен әр 20 кадрда бір жұлдыз
        star = Star()
        all_sprites.add(star)
        stars.add(star)

    all_sprites.update()                # БІР жол — бәрі жаңарады

    screen.fill((10, 10, 40))
    all_sprites.draw(screen)            # БІР жол — бәрі салынады
    pygame.display.set_caption(f"Жұлдыздар экранда: {len(stars)}")
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
```

**Талдау:**
- ойын циклі өте қысқа әрі түсінікті болды: оқиғалар → `all_sprites.update()` → `all_sprites.draw(screen)`;
- әр жұлдыз өз жылдамдығын «біледі» және экраннан шыққанда өзін-өзі жояды (`kill()`), сондықтан жады толып кетпейді;
- `len(stars)` — тақырыпта санау: жұлдыз саны тұрақты деңгейде қалады.

## 6. Конструкторға параметр беру

Спрайттарды икемді ету үшін `__init__`-ке параметрлер береміз:

```python
import pygame


class Bullet(pygame.sprite.Sprite):
    """Ойыншы атқан оқ."""

    def __init__(self, x, y, speed=-10):
        super().__init__()
        self.image = pygame.Surface((6, 16))
        self.image.fill((255, 80, 80))
        self.rect = self.image.get_rect(midbottom=(x, y))
        self.speed = speed

    def update(self):
        self.rect.y += self.speed
        if self.rect.bottom < 0:
            self.kill()
```

Ойыншы атқанда (`KEYDOWN` + `K_SPACE`):

```python
bullet = Bullet(player.rect.centerx, player.rect.top)
all_sprites.add(bullet)
bullets.add(bullet)
```

## 7. Мұрагерлік пен полиморфизм ойында

Жаулардың бірнеше түрі бар делік: тік түсетін, зигзагпен түсетін, тез түсетін. Ортақ қасиеттерін ата-класқа шығарамыз, ерекшелігін ұрпақ кластарда `update()`-ті қайта анықтау (override) арқылы береміз:

```python
import math
import random
import pygame

WIDTH, HEIGHT = 800, 600


class Enemy(pygame.sprite.Sprite):
    """Барлық жаулардың ата-класы: тік түседі."""

    color = (220, 60, 60)

    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((40, 40))
        self.image.fill(self.color)
        self.rect = self.image.get_rect(midbottom=(random.randint(40, WIDTH - 40), 0))
        self.speed = 3

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            self.kill()


class FastEnemy(Enemy):
    """Жылдам жау."""

    color = (255, 150, 0)

    def __init__(self):
        super().__init__()
        self.speed = 7


class ZigzagEnemy(Enemy):
    """Зигзагпен түсетін жау."""

    color = (150, 60, 220)

    def __init__(self):
        super().__init__()
        self.start_x = self.rect.centerx
        self.timer = 0

    def update(self):
        super().update()                         # ата-класс: төмен түсу
        self.timer += 1
        self.rect.centerx = self.start_x + int(60 * math.sin(self.timer / 15))
```

Ойын циклінде ешнәрсе өзгермейді: `enemies.update()` әр жаудың **өз** `update()` әдісін шақырады — бұл **полиморфизм**.

```python
enemy_classes = [Enemy, FastEnemy, ZigzagEnemy]
new_enemy = random.choice(enemy_classes)()     # кездейсоқ түрдегі жау
```

## 8. Спрайттардың «өмірлік циклі»

```text
  Star()  ──▶  group.add(star)  ──▶  [әр кадр: update() + draw()]  ──▶  star.kill()
  құрылды      топқа қосылды         ойында «өмір сүреді»                 барлық топтан шықты
```

## Жиі кездесетін қателер

**1. `super().__init__()` ұмытылған**
```python
import pygame
class Coin(pygame.sprite.Sprite):
    def __init__(self):
        self.image = pygame.Surface((20, 20))
        self.rect = self.image.get_rect()
group = pygame.sprite.Group()
group.add(Coin())     # AttributeError: ... '_Sprite__g'
```
Дұрысы: `__init__`-тің бірінші жолы — `super().__init__()`.

**2. `image` немесе `rect` атауы басқаша** (`self.img`, `self.position`) — `group.draw()` оларды таппай, қате береді. Атаулар дәл `image` және `rect` болуы керек.

**3. `update()` ішінде салу** — `update` тек күйді өзгертеді, салуды `group.draw()` жасайды. Логика мен салуды араластырмаңыз.

**4. Экраннан шыққан спрайттарды жоймау** — оқтар мен жаулар шексіз жинақталып, ойын біртіндеп баяулайды. `kill()` қолданыңыз.

**5. Спрайтты тек бір топқа қосу** — `enemies`-ке қосып, `all_sprites`-қа қоспасаңыз, жау экранда көрінбейді (немесе керісінше — қақтығыс тексерілмейді).

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Спрайт | Ойындағы жеке объект (кейіпкер, жау, оқ, тиын) |
| `pygame.sprite.Sprite` | Барлық спрайттардың ата-класы |
| `image`, `rect` | Спрайттың міндетті атрибуттары: сурет және орын |
| `update()` | Спрайт күйін әр кадрда жаңартатын әдіс |
| `pygame.sprite.Group` | Спрайттар тобы: `update()`, `draw()` |
| `kill()` | Спрайтты барлық топтан өшіру |
| Мұрагерлік | `FastEnemy(Enemy)` — ата-класстың қасиеттерін алу |
| Полиморфизм | `group.update()` әр спрайттың өз `update()`-ін шақыруы |

## Ескерту

- Спрайт класының `__init__` әдісінде міндетті түрде `super().__init__()` шақырыңыз — әйтпесе спрайтты топқа қосқанда қате шығады.
- `kill()` спрайтты **барлық** топтардан алып тастайды, бірақ объектінің өзін жоймайды: оған басқа сілтеме болса, ол жадта қала береді.
- `Group.update(*args)` берілген аргументтерді әр спрайттың `update()` әдісіне жібереді, мысалы `all_sprites.update(dt)`.
- Салу ретін басқару үшін `pygame.sprite.LayeredUpdates` (қабаттары бар топ), ал бір ғана спрайт үшін `pygame.sprite.GroupSingle` бар.
- `pygame.sprite` құжаттамасы: https://www.pygame.org/docs/ref/sprite.html
