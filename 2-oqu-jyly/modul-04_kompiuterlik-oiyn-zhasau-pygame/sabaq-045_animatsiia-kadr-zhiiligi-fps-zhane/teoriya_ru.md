# Урок 45. Анимация. Частота кадров (FPS) и управление временем.

## Темы

1. FPS и Clock
2. Проблема: движение, зависящее от кадров
3. Решение: delta time (dt)
4. Дробные координаты: Vector2
5. Полный пример: движение, зависящее от времени
6. Покадровая анимация
7. Sprite sheet: много кадров на одной картинке
8. Состояния анимации
9. Другие инструменты, связанные со временем
10. Частые ошибки
11. Основные понятия

## 1. FPS и Clock

**FPS** (frames per second) — количество кадров в секунду.

| FPS | Эффект | Применение |
|---|---|---|
| 12–24 | Мультфильм, «прерывистое» движение | Покадровая анимация, кино |
| 30 | Нормально | Консольные игры, медленные игры |
| 60 | Плавно | Большинство компьютерных игр |
| 120–144 | Очень плавно | Киберспортивные игры, специальные мониторы |

Методы `pygame.time.Clock`:

| Метод | Что делает |
|---|---|
| `clock.tick(fps)` | Ограничивает частоту кадров; возвращает **миллисекунды**, прошедшие с предыдущего вызова |
| `clock.get_fps()` | Средний реальный FPS за последние несколько кадров |
| `clock.get_time()` | Интервал последнего `tick` (мс) |

**Важно:** `tick(60)` означает «**не больше** 60». Если компьютер слабый или в игре много объектов, реальный FPS может упасть до 40, 30.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 200))
clock = pygame.time.Clock()
for frame in range(5):
    dt = clock.tick(60)
    print(f"Кадр {frame}: {dt} мс")
pygame.quit()
```

Результат (пример):
```text
Кадр 0: 0 мс
Кадр 1: 17 мс
Кадр 2: 16 мс
Кадр 3: 17 мс
Кадр 4: 16 мс
```

При 60 FPS один кадр ≈ 1000 / 60 ≈ 16,7 мс.

## 2. Проблема: движение, зависящее от кадров

До сих пор мы задавали скорость в виде **пиксель/кадр**: `rect.x += 5`.

| Компьютер | FPS | Скорость |
|---|---|---|
| Мощный | 60 | 5 × 60 = 300 пикселей/с |
| Слабый | 30 | 5 × 30 = **150** пикселей/с |

На слабом компьютере игра медленно «плывёт», а на мощном, если FPS не ограничен, персонаж «улетает». Игра не должна работать с разной скоростью на разных компьютерах.

## 3. Решение: delta time (dt)

**Delta time** — время, прошедшее с предыдущего кадра (в секундах). Задаём скорость в виде **пиксель/секунда** и в каждом кадре умножаем её на `dt`:

```text
смещение = скорость (пиксель/с) × dt (с)
```

| FPS | dt | 300 × dt | Смещение за секунду |
|---|---|---|---|
| 60 | 0,0167 с | 5 пикселей | 5 × 60 = 300 |
| 30 | 0,0333 с | 10 пикселей | 10 × 30 = 300 |

Если кадров меньше, за каждый кадр объект смещается больше — в итоге скорость на всех компьютерах **одинаковая**.

```python
dt = clock.tick(60) / 1000      # миллисекунды → секунды
x += SPEED * dt                 # SPEED — пиксели/секунду
```

## 4. Дробные координаты: Vector2

`Rect` хранит только целые числа: `rect.x += 2.5` → дробная часть теряется, а смещение с `dt` всегда дробное. Поэтому точное положение храним в отдельной переменной `float` или в векторе **`pygame.math.Vector2`**, а `rect` обновляем только для рисования:

```python
from pygame.math import Vector2

pos = Vector2(100, 200)            # положение
vel = Vector2(300, -150)           # скорость, пиксели/с
dt = 0.016
pos += vel * dt                    # векторная операция
print(pos)
print(round(pos.x), round(pos.y))
print("Модуль скорости:", round(vel.length(), 1))
direction = Vector2(3, 4).normalize()
print("Единичный вектор:", direction)
```

Результат:
```text
[104.8, 197.6]
105 198
Модуль скорости: 335.4
Единичный вектор: [0.6, 0.8]
```

`normalize()` — вектор длиной 1. Нормализуем вектор направления, чтобы при движении по диагонали скорость не увеличивалась (если одновременно нажаты → и ↑, персонаж не будет двигаться в √2 раз быстрее).

## 5. Полный пример: движение, зависящее от времени

```python
import pygame
from pygame.math import Vector2

WIDTH, HEIGHT = 800, 600
SPEED = 300                        # пиксели/секунду


class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill((0, 200, 255))
        self.rect = self.image.get_rect(center=(WIDTH // 2, HEIGHT // 2))
        self.pos = Vector2(self.rect.center)

    def update(self, dt):
        keys = pygame.key.get_pressed()
        direction = Vector2(keys[pygame.K_RIGHT] - keys[pygame.K_LEFT],
                            keys[pygame.K_DOWN] - keys[pygame.K_UP])
        if direction.length() > 0:
            direction = direction.normalize()
        self.pos += direction * SPEED * dt
        self.pos.x = max(25, min(WIDTH - 25, self.pos.x))
        self.pos.y = max(25, min(HEIGHT - 25, self.pos.y))
        self.rect.center = (round(self.pos.x), round(self.pos.y))


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
font = pygame.font.SysFont("arial", 24)
player = Player()
group = pygame.sprite.Group(player)
fps_limit = 60
running = True
while running:
    dt = clock.tick(fps_limit) / 1000
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_f:
            fps_limit = 15 if fps_limit == 60 else 60     # F — переключить FPS
    group.update(dt)                                      # в update можно передать аргумент
    screen.fill((25, 25, 25))
    group.draw(screen)
    info = font.render(f"FPS: {clock.get_fps():.0f} (лимит {fps_limit}) — нажмите F",
                       True, (255, 255, 255))
    screen.blit(info, (10, 10))
    pygame.display.flip()
pygame.quit()
```

Попробуйте снизить FPS до 15 клавишей `F`: движение станет «прерывистым», но персонаж всё равно будет проходить 300 пикселей в секунду!

`group.update(dt)` — аргументы, переданные в `update()` группы, передаются в `update()` каждого спрайта.

## 6. Покадровая анимация

Покадровая анимация — поочерёдный показ нескольких картинок (кадров). Основные переменные:

| Переменная | Значение |
|---|---|
| `frames` | Список кадров (Surface) |
| `frame_index` | Номер кадра, показываемого сейчас (может быть дробным) |
| `animation_speed` | Сколько кадров сменяется в секунду (FPS анимации) |

**Важно:** скорость анимации **отделена** от FPS игры. Игра работает на 60 FPS, а для анимации бега достаточно смены 8–12 кадров в секунду.

```python
frame_index += animation_speed * dt          # например, 10 кадров/с × 0.016 с = 0.16
if frame_index >= len(frames):
    frame_index = 0                          # с начала (цикл)
image = frames[int(frame_index)]
```

**Создание кадров программно** (удобно, когда нет файлов с картинками):

```python
import math
import pygame


def make_walk_frames(count=6, size=60):
    """Кадры простого «человечка» с двигающимися ногами."""
    frames = []
    for i in range(count):
        surf = pygame.Surface((size, size), pygame.SRCALPHA)
        swing = int(12 * math.sin(2 * math.pi * i / count))
        cx = size // 2
        pygame.draw.circle(surf, (255, 220, 180), (cx, 10), 8)                  # голова
        pygame.draw.line(surf, (40, 120, 220), (cx, 18), (cx, 38), 4)            # тело
        pygame.draw.line(surf, (40, 40, 40), (cx, 38), (cx + swing, 56), 4)      # нога 1
        pygame.draw.line(surf, (40, 40, 40), (cx, 38), (cx - swing, 56), 4)      # нога 2
        pygame.draw.line(surf, (40, 120, 220), (cx, 24), (cx - swing, 34), 3)    # рука 1
        pygame.draw.line(surf, (40, 120, 220), (cx, 24), (cx + swing, 34), 3)    # рука 2
        frames.append(surf)
    return frames
```

## 7. Sprite sheet: много кадров на одной картинке

Игровые художники располагают кадры в ряд на одной большой картинке (sprite sheet, «спрайтовый лист»):

```text
+------+------+------+------+
|кадр 0|кадр 1|кадр 2|кадр 3|   каждый кадр 64×64, всего 256×64
+------+------+------+------+
```

Кадры «вырезаем» с помощью `subsurface()`:

```python
import pygame


def load_sheet_frames(sheet, frame_w, frame_h):
    """Делит спрайтовый лист на кадры одного ряда."""
    frames = []
    for i in range(sheet.get_width() // frame_w):
        rect = pygame.Rect(i * frame_w, 0, frame_w, frame_h)
        frames.append(sheet.subsurface(rect).copy())
    return frames


demo_sheet = pygame.Surface((256, 64))
print(len(load_sheet_frames(demo_sheet, 64, 64)), "кадра")
```

Результат:
```text
4 кадра
```

С реальным файлом: `sheet = pygame.image.load("assets/images/hero_run.png").convert_alpha()`.

## 8. Состояния анимации

Когда персонаж стоит — анимация «дыхания» (idle), когда идёт — «бег» (run), когда прыгает — «прыжок» (jump). Это маленькая версия машины состояний (state machine):

```python
import pygame
from pygame.math import Vector2

WIDTH, HEIGHT = 800, 400


class Hero(pygame.sprite.Sprite):
    """Персонаж с анимацией стояния и ходьбы."""

    SPEED = 220
    ANIM_FPS = 10

    def __init__(self, walk_frames):
        super().__init__()
        self.animations = {
            "idle": [walk_frames[0]],
            "run": walk_frames,
        }
        self.state = "idle"
        self.frame_index = 0.0
        self.facing_right = True
        self.image = walk_frames[0]
        self.rect = self.image.get_rect(midbottom=(WIDTH // 2, HEIGHT - 40))
        self.pos = Vector2(self.rect.midbottom)

    def update(self, dt):
        keys = pygame.key.get_pressed()
        move = keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]
        new_state = "run" if move else "idle"
        if new_state != self.state:          # если состояние сменилось — анимация с начала
            self.state = new_state
            self.frame_index = 0.0
        if move:
            self.facing_right = move > 0
        self.pos.x += move * self.SPEED * dt
        self.pos.x = max(30, min(WIDTH - 30, self.pos.x))

        frames = self.animations[self.state]
        self.frame_index = (self.frame_index + self.ANIM_FPS * dt) % len(frames)
        image = frames[int(self.frame_index)]
        if not self.facing_right:
            image = pygame.transform.flip(image, True, False)
        self.image = image
        self.rect = self.image.get_rect(midbottom=(round(self.pos.x), round(self.pos.y)))
```

## 9. Другие инструменты, связанные со временем

**Таймер через get_ticks()** (например, взрыв исчезает через 0,5 секунды):

```python
# self.born = pygame.time.get_ticks()
# if pygame.time.get_ticks() - self.born > 500: self.kill()
```

**Плавный переход (lerp — линейная интерполяция).** Камера или элемент меню не прыгает к цели сразу, а приближается постепенно:

```python
def lerp(a, b, t):
    """Пройти долю t (0..1) пути от a к b."""
    return a + (b - a) * t

camera_x = 0.0
target = 100.0
for frame in range(5):
    camera_x = lerp(camera_x, target, 0.3)
    print(round(camera_x, 1), end=" ")
```

Результат:
```text
30.0 51.0 65.7 76.0 83.2 
```

В каждом кадре проходим 30% оставшегося расстояния: сначала быстро, потом медленно — движение выглядит «мягким».

## Частые ошибки

**1. Оставить `dt` в миллисекундах**
```python
import pygame
pygame.init()
clock = pygame.time.Clock()
x = 0
SPEED = 300
dt = clock.tick(60)        # 16 (мс!)
x += SPEED * dt            # 4800 пикселей — персонаж за один кадр вылетает за экран
```
Правильно: `dt = clock.tick(60) / 1000`.

**2. Прибавлять дробное смещение к Rect** — `rect.x += 300 * 0.016` (4.8) → `rect.x` меняется только на 4, а если 2.4 — на 2; движение неравномерное или вообще останавливается. Храните положение в `Vector2`.

**3. Менять кадр анимации в каждом кадре игры** — `frame_index += 1`: при 60 FPS анимация из 6 кадров прокручивается 10 раз в секунду, и персонаж «дрожит». Управляйте скоростью анимации через `ANIM_FPS * dt`.

**4. Не превращать `frame_index` в целое число** — `frames[2.7]` → `TypeError: list indices must be integers`. Правильно: `frames[int(frame_index)]`.

**5. Очень большой dt после «зависания» игры** — если перетаскивать окно или компьютер «задумается», dt может стать 0,5 секунды, и персонаж «пройдёт» сквозь стену. Решение: `dt = min(dt, 0.05)`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| FPS | Количество кадров в секунду |
| `clock.tick()` | Ограничивает FPS и возвращает интервал между кадрами (мс) |
| Delta time (dt) | Время с предыдущего кадра (с) |
| Пиксель/секунда | Единица скорости, не зависящая от FPS |
| `Vector2` | Двумерный вектор с дробными координатами |
| `normalize()` | Приведение вектора к длине 1 |
| Покадровая анимация | Показ кадров поочерёдной сменой |
| Sprite sheet | Кадры анимации, собранные на одной картинке |
| `subsurface()` | Получение части поверхности как отдельного Surface |
| Lerp | Плавный переход между двумя значениями |

## Примечание

- `clock.tick(fps)` возвращает прошедшее время в миллисекундах; чтобы перевести в секунды, разделите на 1000: `dt = clock.tick(60) / 1000`.
- Перетаскивание окна или «зависание» компьютера может сделать `dt` очень большим. Ограничивайте его: `dt = min(dt, 0.05)`.
- `clock.tick_busy_loop()` точнее, но сильнее нагружает процессор; в большинстве случаев достаточно `tick()`.
- `pygame.time.get_ticks()` — миллисекунды, прошедшие с `pygame.init()`; в самом Python для точного измерения времени есть `time.perf_counter()`.
- Документация `pygame.time`: https://www.pygame.org/docs/ref/time.html
