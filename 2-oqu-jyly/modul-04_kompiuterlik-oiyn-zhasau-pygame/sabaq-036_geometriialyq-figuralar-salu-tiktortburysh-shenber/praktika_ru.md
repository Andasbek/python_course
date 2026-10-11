# Урок 36. Практические задания: геометрические фигуры

> Каждое задание выполняй в отдельном файле (`task1.py`, `task2.py` ...). Цвета записывай как константы. Положение фигур по возможности вычисляй относительно размеров окна (`WIDTH`, `HEIGHT`) или другого `Rect`.

## Базовый уровень

### Задание 1. Светофор
В окне 400×600 нарисуй внутри чёрного прямоугольника (со скруглёнными углами) три круга: красный, жёлтый, зелёный. Круги должны стоять на одной вертикали на одинаковом расстоянии.

### Задание 2. Снеговик
Три белых круга (уменьшаются снизу вверх), чёрные глаза, нос-морковка (оранжевый треугольник), руки (коричневые линии), шляпа-ведро (прямоугольник). Фон — зимнее голубое небо и белый снег.

### Задание 3. Сетка
Нарисуй в окне 600×600 сетку 10×10 (серые линии). Две оси, проходящие через центр (X и Y), выдели толстой красной линией.

### Задание 4. Атрибуты Rect
С помощью `pygame.Rect` создай прямоугольник 120×80 и поочерёдно нарисуй его в четырёх углах и в центре окна (800×600) (пять прямоугольников). Используй только атрибуты `topleft`, `topright`, `bottomleft`, `bottomright`, `center`.

## Продвинутый уровень

### Задание 5. Юрта
Нарисуй казахскую юрту (киіз үй): белый прямоугольник — кереге (решётчатая стена), купол-шанырак — эллипс или дуга, дверь — красный прямоугольник, ромбовидный узор кереге — диагональные линии. На фоне — горы (треугольники) и степь.

### Задание 6. Звезда
С помощью `polygon` нарисуй пятиконечную звезду с центром `(300, 300)`, внешним радиусом 150 и внутренним радиусом 60.
**Подсказка:** 10 точек; угол `i`-й точки `math.radians(-90 + i * 36)`, радиус для чётных `i` — внешний, для нечётных — внутренний; координаты: `x = cx + r * cos(a)`, `y = cy + r * sin(a)`.

### Задание 7. Прыгающий мяч
Мяч радиусом 25 должен двигаться, «отражаясь» от границ окна.
**Подсказка:** `x += dx`, `y += dy`; если `x - r < 0` или `x + r > WIDTH`, то `dx = -dx`.

## ★ Олимпиадная / творческая задача

**А. Аналоговые часы.** Нарисуй часы, показывающие реальное время: круглый циферблат, 12 отметок (линии), часовая, минутная и секундная стрелки (`datetime.now()` и тригонометрия). Стрелки должны обновляться каждую секунду.

**Б. «Мой персонаж».** Нарисуй только из фигур персонажа своей будущей игры. Все его части должны располагаться относительно одного `Rect` (`body`), то есть при изменении `body.center` персонаж перемещается целиком.

## Домашнее задание

1. Нарисуй картинку «Мой аул»: не менее 6 разных функций `draw`, 15 фигур.
2. Сохрани рисунок через `pygame.image.save(screen, "aula.png")` и загрузи вместе с кодом на GitHub.
3. К следующему уроку: найди в интернете бесплатные PNG-изображения для игры (персонаж, фон). Рекомендуемый сайт: kenney.nl (лицензия CC0).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1. Светофор:**

```python
import pygame

WIDTH, HEIGHT = 400, 600
COLORS = [(230, 0, 0), (240, 200, 0), (0, 190, 0)]

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Светофор")
body = pygame.Rect(0, 0, 160, 440)
body.center = (WIDTH // 2, HEIGHT // 2)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((200, 220, 255))
    pygame.draw.rect(screen, (30, 30, 30), body, border_radius=25)
    for i, color in enumerate(COLORS):
        cy = body.top + 80 + i * 140
        pygame.draw.circle(screen, color, (body.centerx, cy), 55)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задание 2. Снеговик:**

```python
import pygame

WIDTH, HEIGHT = 600, 600
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Снеговик")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((100, 150, 220))
    pygame.draw.rect(screen, WHITE, (0, 480, WIDTH, 120))
    cx = WIDTH // 2
    pygame.draw.circle(screen, WHITE, (cx, 430), 90)
    pygame.draw.circle(screen, WHITE, (cx, 290), 65)
    pygame.draw.circle(screen, WHITE, (cx, 185), 45)
    pygame.draw.circle(screen, BLACK, (cx - 15, 175), 6)
    pygame.draw.circle(screen, BLACK, (cx + 15, 175), 6)
    pygame.draw.polygon(screen, (255, 140, 0), [(cx, 185), (cx + 45, 195), (cx, 200)])
    pygame.draw.line(screen, (110, 70, 30), (cx - 60, 280), (cx - 140, 220), 6)
    pygame.draw.line(screen, (110, 70, 30), (cx + 60, 280), (cx + 140, 220), 6)
    pygame.draw.rect(screen, (60, 60, 60), (cx - 35, 105, 70, 45))
    for y in (270, 300, 330):
        pygame.draw.circle(screen, BLACK, (cx, y), 6)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задание 3. Сетка:**

```python
import pygame

SIZE = 600
STEP = SIZE // 10

pygame.init()
screen = pygame.display.set_mode((SIZE, SIZE))
pygame.display.set_caption("Сетка")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((255, 255, 255))
    for i in range(0, SIZE + 1, STEP):
        pygame.draw.line(screen, (180, 180, 180), (i, 0), (i, SIZE))
        pygame.draw.line(screen, (180, 180, 180), (0, i), (SIZE, i))
    pygame.draw.line(screen, (220, 0, 0), (SIZE // 2, 0), (SIZE // 2, SIZE), 3)
    pygame.draw.line(screen, (220, 0, 0), (0, SIZE // 2), (SIZE, SIZE // 2), 3)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задание 4. Атрибуты Rect:**

```python
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Атрибуты Rect")
positions = {
    "topleft": (0, 0),
    "topright": (WIDTH, 0),
    "bottomleft": (0, HEIGHT),
    "bottomright": (WIDTH, HEIGHT),
    "center": (WIDTH // 2, HEIGHT // 2),
}
rects = []
for attr, point in positions.items():
    r = pygame.Rect(0, 0, 120, 80)
    setattr(r, attr, point)
    rects.append(r)
    print(attr, r)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((30, 30, 30))
    for r in rects:
        pygame.draw.rect(screen, (0, 200, 200), r, border_radius=10)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

Результат в консоли:
```text
topleft <rect(0, 0, 120, 80)>
topright <rect(680, 0, 120, 80)>
bottomleft <rect(0, 520, 120, 80)>
bottomright <rect(680, 520, 120, 80)>
center <rect(340, 260, 120, 80)>
```
(Вместо `setattr` ученики могут написать пять отдельных строк: `r1.topleft = (0, 0)` и т.д.)

**Задание 5. Юрта:**

```python
import math
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Юрта")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((150, 200, 240))
    pygame.draw.polygon(screen, (120, 120, 140), [(0, 420), (180, 180), (360, 420)])
    pygame.draw.polygon(screen, (100, 100, 120), [(250, 420), (480, 140), (720, 420)])
    pygame.draw.polygon(screen, (255, 255, 255), [(430, 200), (480, 140), (530, 200)])
    pygame.draw.rect(screen, (190, 170, 90), (0, 420, WIDTH, 180))
    wall = pygame.Rect(250, 330, 300, 150)
    pygame.draw.rect(screen, (245, 240, 225), wall)
    for x in range(wall.left, wall.right, 30):            # узор кереге
        pygame.draw.line(screen, (170, 120, 70), (x, wall.top), (x + 30, wall.bottom), 2)
        pygame.draw.line(screen, (170, 120, 70), (x + 30, wall.top), (x, wall.bottom), 2)
    dome = pygame.Rect(wall.left, wall.top - 110, wall.width, 220)
    pygame.draw.ellipse(screen, (245, 240, 225), dome)
    pygame.draw.rect(screen, (245, 240, 225), (wall.left, wall.top, wall.width, 10))
    pygame.draw.arc(screen, (170, 120, 70), dome, 0, math.pi, 3)
    pygame.draw.circle(screen, (170, 120, 70), (wall.centerx, dome.top + 8), 18, 4)  # шанырак
    door = pygame.Rect(0, 0, 60, 90)
    door.midbottom = wall.midbottom
    pygame.draw.rect(screen, (200, 30, 30), door)
    pygame.draw.rect(screen, (240, 200, 0), door, 3)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задание 6. Звезда:**

```python
import math
import pygame


def star_points(cx, cy, outer, inner):
    points = []
    for i in range(10):
        angle = math.radians(-90 + i * 36)
        r = outer if i % 2 == 0 else inner
        points.append((cx + r * math.cos(angle), cy + r * math.sin(angle)))
    return points


pygame.init()
screen = pygame.display.set_mode((600, 600))
pygame.display.set_caption("Звезда")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((10, 10, 50))
    pygame.draw.polygon(screen, (255, 215, 0), star_points(300, 300, 150, 60))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задание 7. Прыгающий мяч:**

```python
import pygame

WIDTH, HEIGHT = 800, 600
R = 25

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Прыгающий мяч")
clock = pygame.time.Clock()
x, y = 100, 100
dx, dy = 5, 4
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    x += dx
    y += dy
    if x - R < 0 or x + R > WIDTH:
        dx = -dx
    if y - R < 0 or y + R > HEIGHT:
        dy = -dy
    screen.fill((20, 20, 20))
    pygame.draw.circle(screen, (255, 80, 80), (x, y), R)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**★ А. Аналоговые часы:**

```python
import math
from datetime import datetime
import pygame

SIZE = 500
C = SIZE // 2
R = 200


def hand(surface, angle_deg, length, width, color):
    a = math.radians(angle_deg - 90)
    end = (C + length * math.cos(a), C + length * math.sin(a))
    pygame.draw.line(surface, color, (C, C), end, width)


pygame.init()
screen = pygame.display.set_mode((SIZE, SIZE))
pygame.display.set_caption("Часы")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    now = datetime.now()
    screen.fill((240, 240, 240))
    pygame.draw.circle(screen, (255, 255, 255), (C, C), R)
    pygame.draw.circle(screen, (0, 0, 0), (C, C), R, 4)
    for i in range(12):
        a = math.radians(i * 30)
        p1 = (C + (R - 20) * math.cos(a), C + (R - 20) * math.sin(a))
        p2 = (C + (R - 5) * math.cos(a), C + (R - 5) * math.sin(a))
        pygame.draw.line(screen, (0, 0, 0), p1, p2, 4)
    hand(screen, (now.hour % 12) * 30 + now.minute * 0.5, 100, 8, (0, 0, 0))
    hand(screen, now.minute * 6 + now.second * 0.1, 150, 5, (40, 40, 40))
    hand(screen, now.second * 6, 170, 2, (220, 0, 0))
    pygame.draw.circle(screen, (220, 0, 0), (C, C), 8)
    pygame.display.flip()
    clock.tick(10)
pygame.quit()
```

**★ Б.** Творческое задание; критерий проверки — перемещаются ли все части вместе при изменении `body.center`.

</details>
