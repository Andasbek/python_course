# Урок 41. Определение столкновений (collision detection).

## Темы

1. Пересечение прямоугольников (AABB)
2. Проверки на уровне Rect
3. Столкновения между спрайтами
4. Полный пример: «Защитник космоса» + столкновения
5. Более точное столкновение: окружность и маска
6. Хитбокс (hitbox)
7. Реакция на столкновение: не проходить сквозь стену
8. Карта столкновений в играх
9. Частые ошибки
10. Основные понятия

## 1. Пересечение прямоугольников (AABB)

Самый распространённый способ в играх — сравнивать объекты по прямоугольникам, параллельным осям (AABB — Axis-Aligned Bounding Box).

Два прямоугольника A и B **не пересекаются**, если:
- A полностью **слева** от B: `A.right <= B.left`, или
- A полностью **справа** от B: `A.left >= B.right`, или
- A полностью **выше**: `A.bottom <= B.top`, или
- A полностью **ниже**: `A.top >= B.bottom`.

Во всех остальных случаях — **пересекаются**:

```python
def rects_overlap(a, b):
    """a, b — кортежи (x, y, w, h)."""
    ax, ay, aw, ah = a
    bx, by, bw, bh = b
    return ax < bx + bw and ax + aw > bx and ay < by + bh and ay + ah > by

print(rects_overlap((0, 0, 50, 50), (40, 40, 50, 50)))
print(rects_overlap((0, 0, 50, 50), (60, 0, 50, 50)))
```

Результат:
```text
True
False
```

```text
 Пересекаются:              Не пересекаются:
 +------+                   +------+   +------+
 |  A   |                   |  A   |   |  B   |
 |   +--+---+               +------+   +------+
 +---+--+   |               A.right <= B.left
     |  B   |
     +------+
```

Pygame предоставляет эту проверку в готовом виде.

## 2. Проверки на уровне Rect

| Метод | Что проверяет | Возвращает |
|---|---|---|
| `a.colliderect(b)` | Пересекаются ли два Rect | `True`/`False` |
| `a.collidepoint((x, y))` | Находится ли точка внутри Rect | `True`/`False` |
| `a.collidelist(list_of_rects)` | С каким Rect из списка пересекается | первый индекс или `-1` |
| `a.collidelistall(list_of_rects)` | Все пересекающиеся | список индексов |
| `a.contains(b)` | Находится ли b полностью внутри a | `True`/`False` |

```python
import pygame

player = pygame.Rect(100, 100, 50, 50)
walls = [pygame.Rect(0, 0, 800, 20), pygame.Rect(140, 120, 20, 200),
         pygame.Rect(300, 300, 50, 50)]

print(player.colliderect(walls[1]))
print(player.collidelist(walls))
print(player.collidelistall(walls))
```

Результат:
```text
True
1
[1]
```

## 3. Столкновения между спрайтами

При работе со спрайтами удобны функции модуля `pygame.sprite`:

| Функция | Что делает | Возвращает |
|---|---|---|
| `spritecollide(sprite, group, dokill)` | Столкновение одного спрайта с группой | **список** задетых спрайтов |
| `spritecollideany(sprite, group)` | Задел ли хотя бы один спрайт группы | первый задетый спрайт или `None` |
| `groupcollide(g1, g2, dokill1, dokill2)` | Столкновение двух групп | словарь `{спрайт g1: [задетые спрайты g2]}` |
| `collide_rect(s1, s2)` | Rect двух спрайтов | `True`/`False` |

**Параметр `dokill`:** если `True`, задетые спрайты сразу удаляются через `kill()` (из всех групп).

**Типичные ситуации:**

```python
import pygame

# Игрок собирает монеты: задетые монеты удаляются, очки начисляются
hits = pygame.sprite.spritecollide(player, coins, True)
score += len(hits)

# Задел ли игрока какой-нибудь враг? (врага не удаляем)
if pygame.sprite.spritecollideany(player, enemies):
    lives -= 1

# Пули и метеориты: удаляются оба
hits = pygame.sprite.groupcollide(meteors, bullets, True, True)
for meteor in hits:
    score += 10
```

> Этот фрагмент — образец; предполагается, что `player`, `coins`, `enemies` и т.д. созданы заранее.

## 4. Полный пример: «Защитник космоса» + столкновения

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

    # 1) Пуля попала в метеорит: удаляются оба, +10 очков
    hits = pygame.sprite.groupcollide(meteors, bullets, True, True)
    score += 10 * len(hits)

    # 2) Метеорит попал в корабль: метеорит удаляется, −1 жизнь
    crashed = pygame.sprite.spritecollide(player, meteors, True,
                                          pygame.sprite.collide_circle)
    if crashed:
        lives -= len(crashed)
        if lives <= 0:
            print("Игра окончена! Очки:", score)
            running = False

    screen.fill((5, 5, 25))
    all_sprites.draw(screen)
    pygame.display.set_caption(f"Очки: {score} | Жизни: {lives}")
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

Результат (в консоли в конце игры, пример):
```text
Игра окончена! Очки: 230
```

## 5. Более точное столкновение: окружность и маска

Прямоугольник — грубое приближение. Угол круглого метеорита может «задеть, не задев» корабль — игрок расстроится.

```text
 +---------+          По Rect — задел,
 |  .---.  |+------+  а на глаз — нет!
 | (     ) ||корабль|
 |  '---'  |+------+
 +---------+
```

| Способ | Функция | Точность | Скорость | Когда |
|---|---|---|---|---|
| Прямоугольник | `collide_rect` (по умолчанию) | Низкая | Очень быстро | Много объектов, прямоугольные формы |
| Окружность | `collide_circle` | Средняя | Быстро | Круглые объекты (мяч, метеорит) |
| Маска (пиксели) | `collide_mask` | Высокая | Медленно | Изображения сложной формы |
| Уменьшенный rect | `collide_rect_ratio(0.7)` | Средняя | Быстро | «Честный» хитбокс |

Последний аргумент функций `spritecollide` и `groupcollide` — `collided` — способ проверки:

```python
hits = pygame.sprite.spritecollide(player, meteors, False, pygame.sprite.collide_circle)
hits = pygame.sprite.spritecollide(player, meteors, False, pygame.sprite.collide_mask)
hits = pygame.sprite.spritecollide(player, meteors, False,
                                   pygame.sprite.collide_rect_ratio(0.7))
```

- `collide_circle` использует атрибут спрайта `radius` (если его нет — вычисляет по rect). Если задать его самим, будет точнее: `self.radius = size // 2`.
- `collide_mask` использует атрибут спрайта `mask`: `self.mask = pygame.mask.from_surface(self.image)` — «карта» непрозрачных пикселей изображения.

## 6. Хитбокс (hitbox)

**Хитбокс** — область, в которой проверяется столкновение. Он может быть меньше изображения, тогда игра ощущается «честной». Метод `inflate()` увеличивает/уменьшает Rect (центр сохраняется):

```python
import pygame

image_rect = pygame.Rect(100, 100, 64, 64)
hitbox = image_rect.inflate(-20, -20)
print(hitbox)
```

Результат:
```text
<rect(110, 110, 44, 44)>
```

## 7. Реакция на столкновение: не проходить сквозь стену

Определение столкновения — лишь половина дела. Вторая половина — **реакция**: монета исчезает, враг взрывается, а **стена не пропускает персонажа**.

Самый надёжный способ — выполнять движение **отдельно** по осям: сначала сдвинуться по X и проверить; затем сдвинуться по Y и проверить.

```python
import pygame


def move_with_walls(rect, dx, dy, walls):
    """Перемещает rect, но не даёт войти в стены."""
    rect.x += dx
    for wall in walls:
        if rect.colliderect(wall):
            if dx > 0:
                rect.right = wall.left      # шёл вправо — останавливается слева от стены
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

Результат:
```text
(70, 50)
```

Персонаж пытался сдвинуться на 40 пикселей вправо, но остановился у левой стороны стены (x = 100 − 30 = 70).

## 8. Карта столкновений в играх

| Игра | Что с чем | Реакция |
|---|---|---|
| Space Invaders | Пуля ↔ враг | Оба уничтожаются, +очки |
| Mario | Персонаж ↔ платформа | Стоит, не падает |
| Pac-Man | Персонаж ↔ точка | Точка собирается |
| Snake | Голова ↔ хвост | Игра окончена |
| Arkanoid | Мяч ↔ кирпич | Кирпич разрушается, мяч отскакивает |

## Частые ошибки

**1. Передача группы первым аргументом `spritecollide`**
```python
import pygame
players = pygame.sprite.Group()
enemies = pygame.sprite.Group()
pygame.sprite.spritecollide(players, enemies, False)   # AttributeError: у Group нет rect
```
Правильно: первый аргумент — **один спрайт**; для двух групп — `groupcollide`.

**2. Неосторожный `dokill=True`** — `spritecollide(player, enemies, True)` удаляет врага. Если враг, задевший игрока, не должен исчезать — `False`.

**3. Проверка столкновений после отрисовки** — игрок один кадр виден «внутри» врага. Порядок: `update()` → столкновения → отрисовка.

**4. Уменьшение жизней на каждом кадре** — если враг касается корабля 30 кадров, уходит 30 жизней. Решение: удалить задевшего врага или ввести короткое «время неуязвимости» (invincibility, например 1 секунда).

**5. Нет `mask` для `collide_mask`** — Pygame пересчитывает её при каждой проверке (медленно). Создайте один раз в `__init__`: `self.mask = pygame.mask.from_surface(self.image)`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Определение столкновений | Проверка касания/пересечения двух объектов |
| AABB | Ограничивающий прямоугольник, параллельный осям |
| `colliderect` | Проверка пересечения двух Rect |
| `spritecollide` | Столкновение спрайта с группой, список задетых спрайтов |
| `groupcollide` | Столкновение двух групп, возвращает словарь |
| `dokill` | Флаг автоматического удаления задетых спрайтов |
| `collide_circle`, `collide_mask` | Точная проверка по окружности и по пикселям |
| Хитбокс | Область, в которой проверяется столкновение |
| Реакция на столкновение | Действие после проверки: удаление, очки, остановка |

## Примечание

- `colliderect()` не считает столкновением касание только краями: правая и нижняя граница `Rect` не входят в область.
- Чтобы `pygame.sprite.collide_mask` не пересчитывал маску при каждом вызове, заранее создайте у спрайта атрибут `self.mask = pygame.mask.from_surface(self.image)`.
- Очень быстрый объект может за один кадр «перепрыгнуть» препятствие (tunneling). Решение — разбить движение на несколько маленьких шагов или ограничить скорость.
- `groupcollide()` возвращает словарь: ключ — спрайт первой группы, значение — список столкнувшихся с ним спрайтов второй группы.
- Документация функций столкновений: https://www.pygame.org/docs/ref/sprite.html#pygame.sprite.spritecollide
