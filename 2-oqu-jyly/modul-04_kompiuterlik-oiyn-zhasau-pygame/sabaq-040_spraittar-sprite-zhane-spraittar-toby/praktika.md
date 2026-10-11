# Сабақ 40. Практикалық тапсырмалар: спрайттар және топтар

> Бүгінгі тапсырмалар бір жобаға жиналады — «Ғарыш қорғаушысы» ойынының қаңқасы. Келесі сабақтарда оған қақтығыс, ұпай, дыбыс және мәзір қосамыз. Файл: `space_defender.py`.

## Базалық деңгей

### 1-тапсырма. Player спрайты
`Player(pygame.sprite.Sprite)` класын жаз:
- `image` — 60×40 мөлдір Surface, үстінде көк үшбұрыш (кеме);
- `rect` — экранның төменгі ортасында;
- `update()` — ← → немесе A/D пернелерімен жылжу, экраннан шықпау.
Ойын циклінде тек `all_sprites.update()` және `all_sprites.draw(screen)` қолдан.

### 2-тапсырма. Метеориттер
`Meteor` спрайты: сұр дөңгелек, өлшемі 20–50 кездейсоқ, жоғарыдан кездейсоқ жерден 2–5 жылдамдықпен түседі, экраннан шыққанда `kill()`. Әр 500 мс сайын жаңа метеорит пайда болсын (`USEREVENT` + `set_timer`, 39-сабақ).

### 3-тапсырма. Санауыш
Терезе тақырыбында топтардағы спрайттар саны көрсетілсін: `Метеориттер: 7 | Барлығы: 8`.

### 4-тапсырма. Оқ ату
`Bullet` спрайты: 6×16 қызыл тіктөртбұрыш, жоғары қарай 10 жылдамдықпен ұшады, экраннан шыққанда жойылады. `Space` басылғанда кеменің ұшынан оқ шығады (бір басу — бір оқ).

## Кеңейтілген деңгей

### 5-тапсырма. Метеорит түрлері (мұрагерлік)
`Meteor` класынан мұраланған екі класс жаз:
- `BigMeteor` — үлкен (60–80), баяу (1–2), қоңыр түсті;
- `SpinMeteor` — зигзагпен (синус бойынша) түседі.
Таймер әр жолы үш түрдің біреуін кездейсоқ таңдасын.

### 6-тапсырма. Оқ шектеуі (cooldown)
`Space` басып тұрғанда кеме автоматты түрде атсын, бірақ оқтар арасында кемінде 250 мс өтсін.
**Кеңес:** `Player` класында `self.last_shot = 0` сақта және `pygame.time.get_ticks()` арқылы тексер.

### 7-тапсырма. Фондағы жұлдыздар
Фонға 80 кішкене жұлдыз (`BackgroundStar` спрайттары, 1–3 пиксель) қос, олар баяу төмен жылжып, төменнен шыққанда жоғарыдан қайта пайда болады (`kill()` емес, `rect.bottom = 0`). Жұлдыздар бөлек топта және кеме мен метеориттерден **бұрын** салынады.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Дұшпан кемесі.** `EnemyShip` спрайты экранның жоғарғы бөлігінде солға-оңға жүреді және әр 1,5 секунд сайын төмен қарай `EnemyBullet` атады. Ол үшін спрайт өзі жаңа спрайт құрып, оны топтарға қосуы керек — топтарды конструкторға параметр ретінде бер.

**Ә. Жарылыс бөлшектері.** `Particle` спрайты: кішкене түрлі-түсті шаршы, кездейсоқ бағытта ұшып, біртіндеп кішірейеді және 30 кадрдан кейін жойылады. Тінтуір басылған жерде 20 бөлшек «жарылсын».

## Үй тапсырмасы

1. `space_defender.py`-ды аяқтап, GitHub-қа жүкте.
2. Командалық жобаңдағы ойын объектілерінің тізімін жаса: әр объект үшін класс атауы, атрибуттары, `update()`-те не болатыны (кесте түрінде).
3. Ойлан: метеорит кемеге тигенін қалай анықтауға болады? (Келесі сабақтың тақырыбы.)

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–7-тапсырмалар (толық «Ғарыш қорғаушысы» қаңқасы):**

```python
import math
import random
import pygame

WIDTH, HEIGHT = 800, 600
FPS = 60
SPAWN_METEOR = pygame.USEREVENT + 1


class Player(pygame.sprite.Sprite):
    """Ойыншының кемесі."""

    def __init__(self, all_sprites, bullets):
        super().__init__()
        self.image = pygame.Surface((60, 40), pygame.SRCALPHA)
        pygame.draw.polygon(self.image, (0, 190, 255), [(30, 0), (60, 40), (0, 40)])
        self.rect = self.image.get_rect(midbottom=(WIDTH // 2, HEIGHT - 10))
        self.speed = 7
        self.all_sprites = all_sprites
        self.bullets = bullets
        self.last_shot = 0
        self.cooldown = 250

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            self.rect.x += self.speed
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))
        if keys[pygame.K_SPACE]:
            self.shoot()

    def shoot(self):
        now = pygame.time.get_ticks()
        if now - self.last_shot >= self.cooldown:
            self.last_shot = now
            bullet = Bullet(self.rect.centerx, self.rect.top)
            self.all_sprites.add(bullet)
            self.bullets.add(bullet)


class Bullet(pygame.sprite.Sprite):
    """Жоғары ұшатын оқ."""

    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((6, 16))
        self.image.fill((255, 70, 70))
        self.rect = self.image.get_rect(midbottom=(x, y))
        self.speed = -10

    def update(self):
        self.rect.y += self.speed
        if self.rect.bottom < 0:
            self.kill()


class Meteor(pygame.sprite.Sprite):
    """Тік түсетін метеорит."""

    color = (150, 150, 150)
    size_range = (20, 50)
    speed_range = (2, 5)

    def __init__(self):
        super().__init__()
        size = random.randint(*self.size_range)
        self.image = pygame.Surface((size, size), pygame.SRCALPHA)
        pygame.draw.circle(self.image, self.color, (size // 2, size // 2), size // 2)
        self.rect = self.image.get_rect(midbottom=(random.randint(30, WIDTH - 30), 0))
        self.speed = random.randint(*self.speed_range)

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            self.kill()


class BigMeteor(Meteor):
    """Үлкен, баяу метеорит."""

    color = (140, 90, 50)
    size_range = (60, 80)
    speed_range = (1, 2)


class SpinMeteor(Meteor):
    """Зигзагпен түсетін метеорит."""

    color = (120, 200, 200)

    def __init__(self):
        super().__init__()
        self.base_x = self.rect.centerx
        self.t = 0

    def update(self):
        super().update()
        self.t += 1
        self.rect.centerx = self.base_x + int(50 * math.sin(self.t / 12))


class BackgroundStar(pygame.sprite.Sprite):
    """Фондағы шексіз жылжитын жұлдыз."""

    def __init__(self):
        super().__init__()
        size = random.randint(1, 3)
        self.image = pygame.Surface((size, size))
        self.image.fill((255, 255, 255))
        self.rect = self.image.get_rect(
            topleft=(random.randint(0, WIDTH), random.randint(0, HEIGHT)))
        self.speed = size

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            self.rect.bottom = 0
            self.rect.x = random.randint(0, WIDTH)


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
pygame.time.set_timer(SPAWN_METEOR, 500)

background = pygame.sprite.Group(BackgroundStar() for _ in range(80))
all_sprites = pygame.sprite.Group()
meteors = pygame.sprite.Group()
bullets = pygame.sprite.Group()
player = Player(all_sprites, bullets)
all_sprites.add(player)

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == SPAWN_METEOR:
            meteor = random.choice([Meteor, BigMeteor, SpinMeteor])()
            all_sprites.add(meteor)
            meteors.add(meteor)

    background.update()
    all_sprites.update()

    screen.fill((5, 5, 25))
    background.draw(screen)
    all_sprites.draw(screen)
    pygame.display.set_caption(
        f"Метеориттер: {len(meteors)} | Оқтар: {len(bullets)} | Барлығы: {len(all_sprites)}")
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
```

Ескертпелер:
- 4-тапсырмада «бір басу — бір оқ» талабы үшін `KEYDOWN` оқиғасында `player.shoot()` шақыру жеткілікті; 6-тапсырмада ол `update()` ішіндегі автоматты атумен (cooldown) ауыстырылды.
- `pygame.sprite.Group(генератор)` — топты бірден көп спрайтпен құру.
- Кластық атрибуттар (`color`, `size_range`) арқылы ұрпақ кластар `__init__`-ті қайта жазбай-ақ өзгешеленеді.

**★ А. Дұшпан кемесі:**

```python
import pygame

WIDTH = 800


class EnemyBullet(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((6, 14))
        self.image.fill((255, 220, 0))
        self.rect = self.image.get_rect(midtop=(x, y))

    def update(self):
        self.rect.y += 6
        if self.rect.top > 600:
            self.kill()


class EnemyShip(pygame.sprite.Sprite):
    """Солға-оңға жүріп, мерзімді түрде ататын кеме."""

    def __init__(self, all_sprites, enemy_bullets):
        super().__init__()
        self.image = pygame.Surface((70, 30))
        self.image.fill((200, 40, 120))
        self.rect = self.image.get_rect(midtop=(WIDTH // 2, 40))
        self.dx = 3
        self.all_sprites = all_sprites
        self.enemy_bullets = enemy_bullets
        self.last_shot = 0

    def update(self):
        self.rect.x += self.dx
        if self.rect.left < 0 or self.rect.right > WIDTH:
            self.dx = -self.dx
        now = pygame.time.get_ticks()
        if now - self.last_shot > 1500:
            self.last_shot = now
            b = EnemyBullet(self.rect.centerx, self.rect.bottom)
            self.all_sprites.add(b)
            self.enemy_bullets.add(b)
```

**★ Ә. Жарылыс бөлшектері:**

```python
import random
import pygame


class Particle(pygame.sprite.Sprite):
    """Жарылыс бөлшегі: ұшып, кішірейіп, жойылады."""

    def __init__(self, pos):
        super().__init__()
        self.size = random.randint(4, 9)
        self.color = random.choice([(255, 200, 0), (255, 120, 0), (255, 40, 40)])
        self.pos = list(pos)
        self.vel = [random.uniform(-4, 4), random.uniform(-4, 4)]
        self.life = 30
        self.image = pygame.Surface((self.size, self.size))
        self.image.fill(self.color)
        self.rect = self.image.get_rect(center=pos)

    def update(self):
        self.pos[0] += self.vel[0]
        self.pos[1] += self.vel[1]
        self.life -= 1
        if self.life <= 0:
            self.kill()
            return
        size = max(1, self.size * self.life // 30)
        self.image = pygame.Surface((size, size))
        self.image.fill(self.color)
        self.rect = self.image.get_rect(center=(int(self.pos[0]), int(self.pos[1])))


pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Басып жарыл!")
clock = pygame.time.Clock()
particles = pygame.sprite.Group()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            particles.add(Particle(event.pos) for _ in range(20))
    particles.update()
    screen.fill((0, 0, 0))
    particles.draw(screen)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

</details>
