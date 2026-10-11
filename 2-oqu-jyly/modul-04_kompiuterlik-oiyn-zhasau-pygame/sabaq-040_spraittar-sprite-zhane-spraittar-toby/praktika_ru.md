# Урок 40. Практические задания: спрайты и группы

> Сегодняшние задания собираются в один проект — каркас игры «Защитник космоса». На следующих уроках добавим в неё столкновения, очки, звук и меню. Файл: `space_defender.py`.

## Базовый уровень

### Задание 1. Спрайт Player
Напиши класс `Player(pygame.sprite.Sprite)`:
- `image` — прозрачный Surface 60×40, на нём синий треугольник (корабль);
- `rect` — внизу по центру экрана;
- `update()` — движение клавишами ← → или A/D, не выходить за экран.
В игровом цикле используй только `all_sprites.update()` и `all_sprites.draw(screen)`.

### Задание 2. Метеориты
Спрайт `Meteor`: серый круг, размер случайный от 20 до 50, падает сверху из случайного места со скоростью 2–5, при выходе за экран — `kill()`. Каждые 500 мс должен появляться новый метеорит (`USEREVENT` + `set_timer`, 39-й урок).

### Задание 3. Счётчик
В заголовке окна должно отображаться количество спрайтов в группах: `Метеориты: 7 | Всего: 8`.

### Задание 4. Стрельба
Спрайт `Bullet`: красный прямоугольник 6×16, летит вверх со скоростью 10, при выходе за экран уничтожается. При нажатии `Space` из носа корабля вылетает пуля (одно нажатие — одна пуля).

## Продвинутый уровень

### Задание 5. Виды метеоритов (наследование)
Напиши два класса, унаследованных от `Meteor`:
- `BigMeteor` — большой (60–80), медленный (1–2), коричневого цвета;
- `SpinMeteor` — падает зигзагом (по синусоиде).
Таймер каждый раз должен случайно выбирать один из трёх видов.

### Задание 6. Ограничение стрельбы (cooldown)
Пока зажат `Space`, корабль должен стрелять автоматически, но между пулями должно проходить не менее 250 мс.
**Подсказка:** храни в классе `Player` `self.last_shot = 0` и проверяй через `pygame.time.get_ticks()`.

### Задание 7. Звёзды на фоне
Добавь на фон 80 маленьких звёзд (спрайты `BackgroundStar`, 1–3 пикселя), которые медленно движутся вниз и, уйдя за нижний край, снова появляются сверху (не `kill()`, а `rect.bottom = 0`). Звёзды — в отдельной группе и рисуются **до** корабля и метеоритов.

## ★ Олимпиадная / творческая задача

**А. Вражеский корабль.** Спрайт `EnemyShip` движется влево-вправо в верхней части экрана и каждые 1,5 секунды стреляет вниз `EnemyBullet`. Для этого спрайт должен сам создавать новый спрайт и добавлять его в группы — передай группы в конструктор как параметры.

**Б. Частицы взрыва.** Спрайт `Particle`: маленький цветной квадрат, летит в случайном направлении, постепенно уменьшается и через 30 кадров исчезает. В месте щелчка мышью должны «взрываться» 20 частиц.

## Домашнее задание

1. Доделай `space_defender.py` и загрузи на GitHub.
2. Составь список игровых объектов вашего командного проекта: для каждого объекта — имя класса, атрибуты, что происходит в `update()` (в виде таблицы).
3. Подумай: как определить, что метеорит попал в корабль? (Тема следующего урока.)

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–7 (полный каркас «Защитника космоса»):**

```python
import math
import random
import pygame

WIDTH, HEIGHT = 800, 600
FPS = 60
SPAWN_METEOR = pygame.USEREVENT + 1


class Player(pygame.sprite.Sprite):
    """Корабль игрока."""

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
    """Пуля, летящая вверх."""

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
    """Метеорит, падающий вертикально."""

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
    """Большой медленный метеорит."""

    color = (140, 90, 50)
    size_range = (60, 80)
    speed_range = (1, 2)


class SpinMeteor(Meteor):
    """Метеорит, падающий зигзагом."""

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
    """Бесконечно движущаяся звезда на фоне."""

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
        f"Метеориты: {len(meteors)} | Пули: {len(bullets)} | Всего: {len(all_sprites)}")
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
```

Примечания:
- для требования «одно нажатие — одна пуля» в задании 4 достаточно вызывать `player.shoot()` в событии `KEYDOWN`; в задании 6 это заменено автоматической стрельбой (cooldown) внутри `update()`.
- `pygame.sprite.Group(генератор)` — создание группы сразу с множеством спрайтов.
- Благодаря атрибутам класса (`color`, `size_range`) классы-потомки отличаются, не переписывая `__init__`.

**★ А. Вражеский корабль:**

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
    """Корабль, который движется влево-вправо и периодически стреляет."""

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

**★ Б. Частицы взрыва:**

```python
import random
import pygame


class Particle(pygame.sprite.Sprite):
    """Частица взрыва: летит, уменьшается и исчезает."""

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
pygame.display.set_caption("Щёлкни — взорви!")
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
