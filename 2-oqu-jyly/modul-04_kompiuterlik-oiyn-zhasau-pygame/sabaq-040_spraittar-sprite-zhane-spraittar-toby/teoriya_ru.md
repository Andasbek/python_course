# Урок 40. Спрайты (Sprite) и группы спрайтов (Group). Применение ООП в игре.

## Темы

1. Проблема: когда объектов становится много
2. Что такое спрайт?
3. Первый спрайт: Player
4. Группа спрайтов: Group
5. Полный пример: «Звездопад»
6. Передача параметров в конструктор
7. Наследование и полиморфизм в игре
8. «Жизненный цикл» спрайта
9. Частые ошибки
10. Основные понятия

## 1. Проблема: когда объектов становится много

В простой игре персонаж может быть одним `Rect` и несколькими переменными. А если врагов 30, пуль 100, монет 20?

```python
enemies_x = [100, 200, 300]
enemies_y = [0, -50, -100]
enemies_speed = [2, 3, 4]
enemies_alive = [True, True, True]
```

Списки «разбегаются»: при удалении одного врага нужно удалить элемент с одним и тем же индексом из четырёх списков — ошибиться очень легко.

**Решение — ООП:** каждый враг — один объект, все его данные (изображение, положение, скорость) и действия (`update`) — в одном классе. Pygame даёт для этого готовый инструмент — **спрайты**.

## 2. Что такое спрайт?

**Спрайт** (sprite) — любой движущийся или взаимодействующий объект в игре: персонаж, враг, пуля, монета, бонус.

`pygame.sprite.Sprite` — родительский класс всех спрайтов. В классе-наследнике **обязательно** должны быть два атрибута:

| Атрибут | Что хранит |
|---|---|
| `self.image` | Изображение спрайта (Surface) |
| `self.rect` | Положение и размер (Rect) |

И обычно один метод:

| Метод | Что делает |
|---|---|
| `update(self)` | Обновляет состояние спрайта на каждом кадре (движение, анимация) |

## 3. Первый спрайт: Player

```python
import pygame

WIDTH, HEIGHT = 800, 600


class Player(pygame.sprite.Sprite):
    """Персонаж, управляемый игроком."""

    def __init__(self):
        super().__init__()                              # настройка родительского класса — ОБЯЗАТЕЛЬНО
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

Чтобы загрузить изображение из файла, достаточно написать `self.image = load_image("player.png", (50, 50))` (`load_image` — функция безопасной загрузки изображения) — остальной код не меняется.

## 4. Группа спрайтов: Group

**Group** — специальный контейнер для спрайтов (похож на список). Его главное преимущество:

| Метод | Что делает |
|---|---|
| `group.add(sprite)` | Добавить спрайт в группу |
| `group.update()` | Вызвать метод `update()` у **каждого** спрайта группы |
| `group.draw(surface)` | Сделать blit `image` каждого спрайта в место его `rect` |
| `len(group)` | Количество спрайтов в группе |
| `group.empty()` | Очистить группу |
| `sprite.kill()` | Удалить спрайт из **всех** групп |
| `sprite.alive()` | Состоит ли спрайт ещё хоть в одной группе |
| `for s in group:` | Перебор спрайтов |

**Аналогия:** Group — дирижёр оркестра. Одним жестом он подаёт знак всем музыкантам (`update()`), а не обращается к каждому отдельно.

Один спрайт может состоять в нескольких группах. Обычно:
- `all_sprites` — все, для рисования и обновления;
- `enemies`, `bullets`, `coins` — для проверки столкновений.

## 5. Полный пример: «Звездопад»

```python
import random
import pygame

WIDTH, HEIGHT = 800, 600
FPS = 60


class Player(pygame.sprite.Sprite):
    """Игрок, движущийся внизу влево-вправо."""

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
    """Звезда, падающая сверху."""

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
        if self.rect.top > HEIGHT:      # вышла за экран — удалить
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

    if random.random() < 0.05:          # примерно одна звезда каждые 20 кадров
        star = Star()
        all_sprites.add(star)
        stars.add(star)

    all_sprites.update()                # ОДНА строка — всё обновляется

    screen.fill((10, 10, 40))
    all_sprites.draw(screen)            # ОДНА строка — всё рисуется
    pygame.display.set_caption(f"Звёзд на экране: {len(stars)}")
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
```

**Разбор:**
- игровой цикл стал очень коротким и понятным: события → `all_sprites.update()` → `all_sprites.draw(screen)`;
- каждая звезда «знает» свою скорость и сама удаляется при выходе за экран (`kill()`), поэтому память не переполняется;
- `len(stars)` — счётчик в заголовке: количество звёзд держится на постоянном уровне.

## 6. Передача параметров в конструктор

Чтобы спрайты были гибкими, передаём параметры в `__init__`:

```python
import pygame


class Bullet(pygame.sprite.Sprite):
    """Пуля, выпущенная игроком."""

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

Когда игрок стреляет (`KEYDOWN` + `K_SPACE`):

```python
bullet = Bullet(player.rect.centerx, player.rect.top)
all_sprites.add(bullet)
bullets.add(bullet)
```

## 7. Наследование и полиморфизм в игре

Пусть есть несколько видов врагов: падающие вертикально, зигзагом, быстро. Общие свойства выносим в родительский класс, а особенности задаём в классах-потомках, переопределяя (override) `update()`:

```python
import math
import random
import pygame

WIDTH, HEIGHT = 800, 600


class Enemy(pygame.sprite.Sprite):
    """Родительский класс всех врагов: падает вертикально."""

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
    """Быстрый враг."""

    color = (255, 150, 0)

    def __init__(self):
        super().__init__()
        self.speed = 7


class ZigzagEnemy(Enemy):
    """Враг, падающий зигзагом."""

    color = (150, 60, 220)

    def __init__(self):
        super().__init__()
        self.start_x = self.rect.centerx
        self.timer = 0

    def update(self):
        super().update()                         # родительский класс: падение вниз
        self.timer += 1
        self.rect.centerx = self.start_x + int(60 * math.sin(self.timer / 15))
```

В игровом цикле ничего не меняется: `enemies.update()` вызывает **собственный** `update()` каждого врага — это и есть **полиморфизм**.

```python
enemy_classes = [Enemy, FastEnemy, ZigzagEnemy]
new_enemy = random.choice(enemy_classes)()     # враг случайного вида
```

## 8. «Жизненный цикл» спрайта

```text
  Star()  ──▶  group.add(star)  ──▶  [каждый кадр: update() + draw()]  ──▶  star.kill()
  создан       добавлен в группу      «живёт» в игре                         вышел из всех групп
```

## Частые ошибки

**1. Забыт `super().__init__()`**
```python
import pygame
class Coin(pygame.sprite.Sprite):
    def __init__(self):
        self.image = pygame.Surface((20, 20))
        self.rect = self.image.get_rect()
group = pygame.sprite.Group()
group.add(Coin())     # AttributeError: ... '_Sprite__g'
```
Правильно: первая строка `__init__` — `super().__init__()`.

**2. Атрибуты `image` или `rect` названы иначе** (`self.img`, `self.position`) — `group.draw()` их не найдёт и выдаст ошибку. Имена должны быть точно `image` и `rect`.

**3. Рисование внутри `update()`** — `update` только меняет состояние, рисует `group.draw()`. Не смешивайте логику и отрисовку.

**4. Не удаляются спрайты, вышедшие за экран** — пули и враги накапливаются бесконечно, и игра постепенно замедляется. Используйте `kill()`.

**5. Спрайт добавлен только в одну группу** — если добавить в `enemies`, но не в `all_sprites`, враг не будет виден на экране (или наоборот — не будут проверяться столкновения).

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Спрайт | Отдельный объект в игре (персонаж, враг, пуля, монета) |
| `pygame.sprite.Sprite` | Родительский класс всех спрайтов |
| `image`, `rect` | Обязательные атрибуты спрайта: изображение и положение |
| `update()` | Метод, обновляющий состояние спрайта на каждом кадре |
| `pygame.sprite.Group` | Группа спрайтов: `update()`, `draw()` |
| `kill()` | Удаление спрайта из всех групп |
| Наследование | `FastEnemy(Enemy)` — получение свойств родительского класса |
| Полиморфизм | `group.update()` вызывает собственный `update()` каждого спрайта |

## Примечание

- В методе `__init__` класса спрайта обязательно вызывайте `super().__init__()` — иначе при добавлении спрайта в группу возникнет ошибка.
- `kill()` удаляет спрайт из **всех** групп, но не уничтожает сам объект: если на него есть другая ссылка, он остаётся в памяти.
- `Group.update(*args)` передаёт аргументы в метод `update()` каждого спрайта, например `all_sprites.update(dt)`.
- Для управления порядком рисования есть `pygame.sprite.LayeredUpdates` (группа со слоями), а для одного спрайта — `pygame.sprite.GroupSingle`.
- Документация `pygame.sprite`: https://www.pygame.org/docs/ref/sprite.html
