# Урок 45. Практические задания: анимация, FPS и время

## Базовый уровень

### Задание 1. Эксперимент с FPS
Два квадрата движутся слева направо по экрану:
- **красный** — зависит от кадров: `x += 5`;
- **зелёный** — зависит от времени: `x += 300 * dt`.
Дойдя до края, они снова возвращаются на левую сторону. Клавиши `1`, `2`, `3` меняют лимит FPS на 15, 30, 60. На экране должен отображаться реальный FPS (`clock.get_fps()`). Что ты заметил? Запиши вывод в 2 предложениях.

### Задание 2. Управление с Vector2
Перепиши управляемого персонажа из урока 38 с помощью `Vector2` и `dt`: скорость 250 пикселей/с, при движении по диагонали скорость не увеличивается (`normalize()`).

### Задание 3. Мигающая надпись
Надпись «Нажмите Enter, чтобы начать» должна появляться и исчезать 2 раза в секунду (через `get_ticks()`, независимо от FPS).

### Задание 4. Шагающий человечек
С помощью функции `make_walk_frames()` из теории создай 6 кадров и проигрывай анимацию в центре экрана со скоростью 8 кадров в секунду. Клавиши `↑` / `↓` пусть увеличивают/уменьшают скорость анимации на 1 (в диапазоне 2–20), значение отображается на экране.

## Продвинутый уровень

### Задание 5. Анимация стояния/ходьбы
Запусти класс `Hero` из теории: когда стоит — кадр idle, при нажатии ← → — анимация ходьбы, персонаж смотрит в сторону движения. Дополнительно: добавь в анимацию `idle` эффект «дыхания» (высота кадра слегка меняется, 2 кадра, 2 кадра/с).

### Задание 6. Анимация взрыва
В месте щелчка мыши один раз проигрывается анимация взрыва из 8 кадров и исчезает (спрайт `Explosion`). Кадры — программные: круги, радиус которых растёт, а цвет меняется с жёлтого на красный. Скорость анимации — 20 кадров/с.

### Задание 7. Плавная камера
Ширина мира — 3000 пикселей (на земле через каждые 200 пикселей деревья). Персонаж ходит по миру клавишами ← →, камера плавно следует за ним через `lerp`. Все объекты рисуются с координатой `x - camera_x`.

## ★ Олимпиадная / творческая задача

**А. Загрузка sprite sheet.** Скачай с Kenney.nl спрайтовый лист с кадрами бега персонажа (или сам нарисуй лист из 4 кадров в Paint). Вырежи кадры с помощью `load_sheet_frames()` и подключи их к `Hero` из задания 5. Дополнительно: расширь функцию параметром `row` для листа из нескольких рядов.

**Б. Замедленное движение (slow motion).** Добавь в «Защитника космоса» бонус «замедление времени» на 3 секунды по клавише `T`: в это время все метеориты обновляются с `dt * 0.3`, а игрок — с обычным `dt`. Бонус доступен раз в 10 секунд; оставшееся время отображается в HUD.

## Домашнее задание

1. Переведи «Защитника космоса» на delta time: все скорости в пикселях/секунду, `update(dt)`. Добавь метеоритам анимацию вращения (`rotate` из урока 37 + `dt`).
2. Загрузи личный проект IV модуля («Защитник космоса») в полном виде на GitHub: README, скриншоты, управление, лицензии ресурсов.
3. **Подготовься к командному проекту:** придумай 2–3 идеи игр (жанр, главный герой, цель, управление, особенность). На следующем уроке разделимся на группы и утвердим идею.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1:**

```python
import pygame

WIDTH, HEIGHT = 800, 300
LIMITS = {pygame.K_1: 15, pygame.K_2: 30, pygame.K_3: 60}

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
font = pygame.font.SysFont("arial", 24)
clock = pygame.time.Clock()
fps_limit = 60
red_x = 0
green_x = 0.0
running = True
while running:
    dt = min(clock.tick(fps_limit) / 1000, 0.1)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key in LIMITS:
            fps_limit = LIMITS[event.key]
    red_x = (red_x + 5) % WIDTH
    green_x = (green_x + 300 * dt) % WIDTH
    screen.fill((20, 20, 20))
    pygame.draw.rect(screen, (220, 50, 50), (red_x, 80, 40, 40))
    pygame.draw.rect(screen, (50, 200, 50), (round(green_x), 180, 40, 40))
    text = font.render(f"Лимит: {fps_limit} | Реальный FPS: {clock.get_fps():.0f}", True,
                       (255, 255, 255))
    screen.blit(text, (10, 10))
    pygame.display.flip()
pygame.quit()
```

Ожидаемый вывод: при снижении FPS красный квадрат замедляется (его скорость зависит от FPS), а скорость зелёного квадрата не меняется — только движение становится «прерывистым».

**Задания 2–3:**

```python
import pygame
from pygame.math import Vector2

WIDTH, HEIGHT = 800, 600
SPEED = 250

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
font = pygame.font.SysFont("arial, dejavusans", 30)
clock = pygame.time.Clock()
pos = Vector2(WIDTH / 2, HEIGHT / 2)
running = True
while running:
    dt = min(clock.tick(60) / 1000, 0.05)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    direction = Vector2(keys[pygame.K_RIGHT] - keys[pygame.K_LEFT],
                        keys[pygame.K_DOWN] - keys[pygame.K_UP])
    if direction.length_squared() > 0:
        direction = direction.normalize()
    pos += direction * SPEED * dt
    pos.x = max(20, min(WIDTH - 20, pos.x))
    pos.y = max(20, min(HEIGHT - 20, pos.y))
    screen.fill((30, 30, 50))
    pygame.draw.circle(screen, (0, 200, 255), (round(pos.x), round(pos.y)), 20)
    if pygame.time.get_ticks() // 250 % 2 == 0:          # 250 мс видна, 250 мс нет
        text = font.render("Нажмите Enter, чтобы начать", True, (255, 255, 255))
        screen.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT - 60)))
    pygame.display.flip()
pygame.quit()
```

Мигание 2 раза в секунду: один цикл (появление + исчезновение) = 500 мс, то есть каждая половина — 250 мс.

**Задание 4:**

```python
import math
import pygame


def make_walk_frames(count=6, size=60):
    frames = []
    for i in range(count):
        surf = pygame.Surface((size, size), pygame.SRCALPHA)
        swing = int(12 * math.sin(2 * math.pi * i / count))
        cx = size // 2
        pygame.draw.circle(surf, (255, 220, 180), (cx, 10), 8)
        pygame.draw.line(surf, (40, 120, 220), (cx, 18), (cx, 38), 4)
        pygame.draw.line(surf, (40, 40, 40), (cx, 38), (cx + swing, 56), 4)
        pygame.draw.line(surf, (40, 40, 40), (cx, 38), (cx - swing, 56), 4)
        pygame.draw.line(surf, (40, 120, 220), (cx, 24), (cx - swing, 34), 3)
        pygame.draw.line(surf, (40, 120, 220), (cx, 24), (cx + swing, 34), 3)
        frames.append(pygame.transform.scale(surf, (size * 3, size * 3)))
    return frames


pygame.init()
screen = pygame.display.set_mode((600, 400))
font = pygame.font.SysFont("arial, dejavusans", 26)
clock = pygame.time.Clock()
frames = make_walk_frames()
anim_fps = 8
index = 0.0
running = True
while running:
    dt = clock.tick(60) / 1000
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP:
                anim_fps = min(20, anim_fps + 1)
            elif event.key == pygame.K_DOWN:
                anim_fps = max(2, anim_fps - 1)
    index = (index + anim_fps * dt) % len(frames)
    screen.fill((235, 235, 235))
    frame = frames[int(index)]
    screen.blit(frame, frame.get_rect(center=(300, 200)))
    screen.blit(font.render(f"Анимация: {anim_fps} кадр/с", True, (0, 0, 0)), (10, 10))
    pygame.display.flip()
pygame.quit()
```

**Задание 5.** Класс `Hero` из теории + `make_walk_frames()` из задания 4; игровой цикл: `dt = clock.tick(60) / 1000`, `heroes.update(dt)`, `heroes.draw(screen)`. «Дыхание» idle:

```python
import pygame


def make_idle_frames(base):
    """Создаёт из основного кадра два кадра со слегка разной высотой."""
    w, h = base.get_size()
    squashed = pygame.transform.scale(base, (w, h - 4))
    frame2 = pygame.Surface((w, h), pygame.SRCALPHA)
    frame2.blit(squashed, (0, 4))
    return [base, frame2]
```

В `Hero.__init__`: `"idle": make_idle_frames(walk_frames[0])`, а скорость анимации для idle — 2 кадра/с: `speed = 2 if self.state == "idle" else self.ANIM_FPS`.

**Задание 6. Взрыв:**

```python
import pygame


def make_explosion_frames(count=8, size=80):
    frames = []
    for i in range(count):
        t = i / (count - 1)
        surf = pygame.Surface((size, size), pygame.SRCALPHA)
        radius = int(8 + t * (size // 2 - 8))
        color = (255, int(230 * (1 - t)), 0, int(255 * (1 - t * 0.7)))
        pygame.draw.circle(surf, color, (size // 2, size // 2), radius)
        frames.append(surf)
    return frames


class Explosion(pygame.sprite.Sprite):
    ANIM_FPS = 20

    def __init__(self, pos, frames):
        super().__init__()
        self.frames = frames
        self.index = 0.0
        self.image = frames[0]
        self.rect = self.image.get_rect(center=pos)

    def update(self, dt):
        self.index += self.ANIM_FPS * dt
        if self.index >= len(self.frames):
            self.kill()                       # проигрывается один раз и исчезает
            return
        self.image = self.frames[int(self.index)]


pygame.init()
screen = pygame.display.set_mode((700, 450))
clock = pygame.time.Clock()
frames = make_explosion_frames()
explosions = pygame.sprite.Group()
running = True
while running:
    dt = clock.tick(60) / 1000
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            explosions.add(Explosion(event.pos, frames))
    explosions.update(dt)
    screen.fill((10, 10, 30))
    explosions.draw(screen)
    pygame.display.flip()
pygame.quit()
```

**Задание 7. Плавная камера:**

```python
import pygame

WIDTH, HEIGHT = 800, 400
WORLD_W = 3000
SPEED = 350


def lerp(a, b, t):
    return a + (b - a) * t


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
player_x = 100.0
camera_x = 0.0
trees = list(range(100, WORLD_W, 200))
running = True
while running:
    dt = min(clock.tick(60) / 1000, 0.05)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    player_x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * SPEED * dt
    player_x = max(0, min(WORLD_W, player_x))
    target = max(0, min(WORLD_W - WIDTH, player_x - WIDTH / 2))
    camera_x = lerp(camera_x, target, min(1, 5 * dt))
    screen.fill((150, 210, 255))
    pygame.draw.rect(screen, (70, 160, 70), (0, 330, WIDTH, 70))
    for tx in trees:
        sx = round(tx - camera_x)
        if -50 < sx < WIDTH + 50:
            pygame.draw.rect(screen, (100, 60, 30), (sx - 8, 260, 16, 70))
            pygame.draw.circle(screen, (30, 120, 30), (sx, 250), 35)
    pygame.draw.rect(screen, (220, 40, 40), (round(player_x - camera_x) - 15, 290, 30, 40))
    pygame.display.set_caption(f"Игрок: {player_x:.0f} | Камера: {camera_x:.0f}")
    pygame.display.flip()
pygame.quit()
```

`lerp(..., min(1, 5 * dt))` — мы привязали к `dt` и `t`, тогда «мягкость» камеры не зависит от FPS.

**★ А. Многорядный sprite sheet:**

```python
import pygame


def load_sheet_row(sheet, frame_w, frame_h, row=0, count=None):
    """Вырезает кадры из ряда row спрайтового листа."""
    total = sheet.get_width() // frame_w
    count = count or total
    return [sheet.subsurface((i * frame_w, row * frame_h, frame_w, frame_h)).copy()
            for i in range(count)]


demo = pygame.Surface((256, 128))
print(len(load_sheet_row(demo, 64, 64, row=1)), "кадра (2-й ряд)")
```

Результат:
```text
4 кадра (2-й ряд)
```

**★ Б. Замедленное движение (фрагмент игрового цикла):**

```python
import pygame

SLOW_TIME = 3000
COOLDOWN = 10000
slow_until = 0
last_used = -COOLDOWN


def time_scale(now):
    return 0.3 if now < slow_until else 1.0


def try_activate(now):
    """Вызывается при нажатии клавиши T."""
    global slow_until, last_used
    if now - last_used >= COOLDOWN:
        slow_until = now + SLOW_TIME
        last_used = now
        return True
    return False


# В игровом цикле:
# now = pygame.time.get_ticks()
# player_group.update(dt)
# meteors.update(dt * time_scale(now))
# ready_in = max(0, COOLDOWN - (now - last_used)) // 1000  -> HUD: f"Замедление: {ready_in} с"
```

</details>
