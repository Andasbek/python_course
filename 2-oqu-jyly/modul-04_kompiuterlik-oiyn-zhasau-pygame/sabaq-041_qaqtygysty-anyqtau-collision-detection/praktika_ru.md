# Урок 41. Практические задания: определение столкновений

## Базовый уровень

### Задание 1. Детектор пересечения
Два прямоугольника: один неподвижный (в центре), второй следует за мышью (`center = mouse.get_pos()`). При пересечении оба должны становиться красными, иначе — зелёными. Напиши проверку сначала **своей** функцией `rects_overlap()`, затем через `colliderect()` — совпадают ли результаты?

### Задание 2. Сборщик монет
Игрок (управление стрелками) собирает 15 монет, случайно расположенных на экране (спрайты `Coin`, `spritecollide(..., True)`). В заголовке: `Монеты: 4 / 15`. Когда собраны все — «Победа!».

### Задание 3. Столкновения в «Защитнике космоса»
Добавь в свой проект с 40-го урока:
- столкновение пули с метеоритом — оба уничтожаются, +10 очков (`groupcollide`);
- метеорит и корабль — метеорит уничтожается, −1 жизнь; если жизней 0 — игра окончена;
- очки и жизни отображаются в заголовке.

### Задание 4. Стены
На экране 5 стен (прямоугольников). Персонаж ходит между ними, но не проходит сквозь них (функция `move_with_walls`, раздел 7 теории).

## Продвинутый уровень

### Задание 5. Сравнение точности
Проверь столкновение метеорита с кораблём из задания 3 тремя способами: `collide_rect`, `collide_circle`, `collide_mask`. Добавь метеоритам атрибуты `self.radius` и `self.mask`. Какой способ «честнее»? Опиши результат эксперимента в 2–3 предложениях.

### Задание 6. Время неуязвимости
После столкновения корабль должен быть «неуязвимым» 1,5 секунды: в это время он мигает (появляется и исчезает от кадра к кадру), и жизни не уменьшаются.
**Подсказка:** `self.invincible_until = pygame.time.get_ticks() + 1500`; для мигания — `self.image.set_alpha(...)`.

### Задание 7. Arkanoid (разрушение кирпичей)
- Внизу платформа, управляемая ← →;
- мяч отскакивает от стен и платформы;
- вверху кирпичи 5×8; кирпич, в который попал мяч, разрушается, мяч отскакивает обратно;
- если мяч упал вниз — игра окончена.

## ★ Олимпиадная / творческая задача

**А. «Умный» отскок от платформы.** В Arkanoid мяч должен улетать под углом, зависящим от того, в какое место платформы он попал: в центр — вертикально вверх, в край — наискосок. Формула: `dx = (ball.centerx - paddle.centerx) / (paddle.width / 2) * MAX_DX`.

**Б. Змейка.** Игра «Змейка» на сетке: змейка удлиняется, съев яблоко; если голова коснётся своего тела или стены — игра окончена. Храни тело как список `Rect`, столкновение головы проверяй через `collidelist()`.

## Домашнее задание

1. Доделай задание 7 (Arkanoid) и загрузи на GitHub.
2. Составь для командного проекта «карту столкновений»: какой объект с каким сталкивается, какой функцией проверяется, какова реакция (таблица).
3. Как вывести очки и жизни на экран в виде текста? Посмотри в документации модуль `pygame.font`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1:**

```python
import pygame


def rects_overlap(a, b):
    return a.x < b.x + b.w and a.x + a.w > b.x and a.y < b.y + b.h and a.y + a.h > b.y


pygame.init()
screen = pygame.display.set_mode((600, 400))
clock = pygame.time.Clock()
fixed = pygame.Rect(0, 0, 150, 100)
fixed.center = (300, 200)
moving = pygame.Rect(0, 0, 80, 80)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    moving.center = pygame.mouse.get_pos()
    mine = rects_overlap(fixed, moving)
    builtin = fixed.colliderect(moving)
    pygame.display.set_caption(f"Моя: {mine} | colliderect: {builtin}")
    color = (220, 40, 40) if builtin else (40, 200, 40)
    screen.fill((20, 20, 20))
    pygame.draw.rect(screen, color, fixed)
    pygame.draw.rect(screen, color, moving, 3)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задание 2. Сборщик монет:**

```python
import random
import pygame

WIDTH, HEIGHT = 800, 600
TOTAL = 15


class Coin(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((24, 24), pygame.SRCALPHA)
        pygame.draw.circle(self.image, (255, 215, 0), (12, 12), 12)
        self.rect = self.image.get_rect(
            center=(random.randint(20, WIDTH - 20), random.randint(20, HEIGHT - 20)))


class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((40, 40))
        self.image.fill((0, 150, 255))
        self.rect = self.image.get_rect(center=(WIDTH // 2, HEIGHT // 2))

    def update(self):
        keys = pygame.key.get_pressed()
        self.rect.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 5
        self.rect.y += (keys[pygame.K_DOWN] - keys[pygame.K_UP]) * 5
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
player = Player()
coins = pygame.sprite.Group(Coin() for _ in range(TOTAL))
players = pygame.sprite.Group(player)
collected = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    players.update()
    collected += len(pygame.sprite.spritecollide(player, coins, True))
    pygame.display.set_caption(f"Монеты: {collected} / {TOTAL}")
    if collected == TOTAL:
        print("Победа!")
        running = False
    screen.fill((30, 90, 30))
    coins.draw(screen)
    players.draw(screen)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`keys[K_RIGHT] - keys[K_LEFT]` — `True`/`False` считаются как числа (1/0), поэтому результат −1, 0 или 1.

**Задание 3** — полный пример из теории (раздел 4).

**Задание 4. Стены:**

```python
import pygame

WIDTH, HEIGHT = 800, 600
SPEED = 5


def move_with_walls(rect, dx, dy, walls):
    rect.x += dx
    for wall in walls:
        if rect.colliderect(wall):
            if dx > 0:
                rect.right = wall.left
            elif dx < 0:
                rect.left = wall.right
    rect.y += dy
    for wall in walls:
        if rect.colliderect(wall):
            if dy > 0:
                rect.bottom = wall.top
            elif dy < 0:
                rect.top = wall.bottom


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Стены")
clock = pygame.time.Clock()
walls = [pygame.Rect(150, 0, 30, 400), pygame.Rect(330, 200, 30, 400),
         pygame.Rect(500, 0, 30, 350), pygame.Rect(530, 320, 150, 30),
         pygame.Rect(0, 560, 120, 40)]
player = pygame.Rect(40, 40, 30, 30)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    dx = (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * SPEED
    dy = (keys[pygame.K_DOWN] - keys[pygame.K_UP]) * SPEED
    move_with_walls(player, dx, dy, walls)
    player.clamp_ip(screen.get_rect())
    screen.fill((240, 240, 240))
    for wall in walls:
        pygame.draw.rect(screen, (60, 60, 60), wall)
    pygame.draw.rect(screen, (220, 50, 50), player)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задание 5.** В `__init__` метеорита: `self.radius = size // 2` и `self.mask = pygame.mask.from_surface(self.image)`. Кораблю: `self.mask = pygame.mask.from_surface(self.image)`. Ожидаемый вывод: `collide_rect` фиксирует столкновение, как только метеорит «приблизился» к кораблю; `collide_circle` хорошо подходит для круглых метеоритов; `collide_mask` самый точный, но при большом количестве объектов может замедлять игру.

**Задание 6. Время неуязвимости (дополнение к классу Player):**

```python
import pygame


class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((60, 40), pygame.SRCALPHA)
        pygame.draw.polygon(self.image, (0, 190, 255), [(30, 0), (60, 40), (0, 40)])
        self.rect = self.image.get_rect(midbottom=(400, 590))
        self.invincible_until = 0

    def is_invincible(self):
        return pygame.time.get_ticks() < self.invincible_until

    def hit(self):
        """Вызывается при столкновении; возвращает True, если нужно уменьшить жизни."""
        if self.is_invincible():
            return False
        self.invincible_until = pygame.time.get_ticks() + 1500
        return True

    def update(self):
        keys = pygame.key.get_pressed()
        self.rect.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 7
        self.rect.clamp_ip(pygame.Rect(0, 0, 800, 600))
        if self.is_invincible():
            visible = (pygame.time.get_ticks() // 100) % 2 == 0
            self.image.set_alpha(255 if visible else 60)
        else:
            self.image.set_alpha(255)
```

В игровом цикле: `if pygame.sprite.spritecollide(player, meteors, True) and player.hit(): lives -= 1`.

**Задание 7. Arkanoid (с отскоком из ★ А):**

```python
import pygame

WIDTH, HEIGHT = 800, 600
MAX_DX = 7

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Arkanoid")
clock = pygame.time.Clock()

paddle = pygame.Rect(0, 0, 120, 15)
paddle.midbottom = (WIDTH // 2, HEIGHT - 20)
ball = pygame.Rect(0, 0, 16, 16)
ball.center = (WIDTH // 2, HEIGHT // 2)
dx, dy = 4, -5
bricks = []
for row in range(5):
    for col in range(8):
        bricks.append(pygame.Rect(20 + col * 96, 50 + row * 30, 90, 24))
colors = [(230, 60, 60), (240, 150, 40), (240, 220, 50), (80, 200, 80), (70, 140, 240)]

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    paddle.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 8
    paddle.clamp_ip(screen.get_rect())

    ball.x += dx
    ball.y += dy
    if ball.left <= 0 or ball.right >= WIDTH:
        dx = -dx
    if ball.top <= 0:
        dy = -dy
    if ball.colliderect(paddle) and dy > 0:
        dy = -dy
        offset = (ball.centerx - paddle.centerx) / (paddle.width / 2)
        dx = int(offset * MAX_DX) or 1
    index = ball.collidelist(bricks)
    if index != -1:
        bricks.pop(index)
        dy = -dy
    if ball.top > HEIGHT:
        print("Игра окончена!")
        running = False
    if not bricks:
        print("Победа! Все кирпичи разрушены")
        running = False

    screen.fill((15, 15, 30))
    for i, brick in enumerate(bricks):
        pygame.draw.rect(screen, colors[(brick.y - 50) // 30], brick, border_radius=4)
    pygame.draw.rect(screen, (200, 200, 200), paddle, border_radius=7)
    pygame.draw.ellipse(screen, (255, 255, 255), ball)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**★ Б. Змейка:**

```python
import random
import pygame

CELL, N = 25, 24
SIZE = CELL * N

pygame.init()
screen = pygame.display.set_mode((SIZE, SIZE))
clock = pygame.time.Clock()
snake = [pygame.Rect(CELL * 5, CELL * 5, CELL, CELL)]
direction = (1, 0)
apple = pygame.Rect(CELL * 10, CELL * 10, CELL, CELL)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            new = {pygame.K_UP: (0, -1), pygame.K_DOWN: (0, 1),
                   pygame.K_LEFT: (-1, 0), pygame.K_RIGHT: (1, 0)}.get(event.key)
            if new and (new[0] != -direction[0] or new[1] != -direction[1]):
                direction = new
    head = snake[0].move(direction[0] * CELL, direction[1] * CELL)
    if not screen.get_rect().contains(head) or head.collidelist(snake) != -1:
        print("Игра окончена! Длина:", len(snake))
        running = False
        continue
    snake.insert(0, head)
    if head.colliderect(apple):
        apple.topleft = (random.randrange(N) * CELL, random.randrange(N) * CELL)
    else:
        snake.pop()
    pygame.display.set_caption(f"Змейка: {len(snake)}")
    screen.fill((20, 20, 20))
    pygame.draw.rect(screen, (220, 30, 30), apple)
    for part in snake:
        pygame.draw.rect(screen, (40, 200, 40), part.inflate(-2, -2))
    pygame.display.flip()
    clock.tick(10)
pygame.quit()
```

</details>
