# Урок 38. Управление с клавиатуры. Перемещение объекта.

## Темы

1. События клавиатуры: KEYDOWN и KEYUP
2. Константы клавиш
3. Два способа: событие или состояние
4. Перемещение персонажа: get_pressed()
5. Не выходить за границы экрана
6. Направление и изображение персонажа
7. Прыжок и сила тяжести
8. Сеточное (дискретное) перемещение
9. Частые ошибки
10. Основные понятия

## 1. События клавиатуры: KEYDOWN и KEYUP

В **момент** нажатия клавиши в очередь попадает событие `KEYDOWN`, в момент отпускания — событие `KEYUP`. В атрибуте `event.key` записано, какая это клавиша.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
pygame.display.set_caption("События клавиш")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            print("Нажата:", pygame.key.name(event.key))
            if event.key == pygame.K_SPACE:
                print("  Пробел — прыжок!")
        elif event.type == pygame.KEYUP:
            print("Отпущена:", pygame.key.name(event.key))
    screen.fill((0, 0, 0))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

Результат (при нажатии A, Space и стрелки вправо):
```text
Нажата: a
Отпущена: a
Нажата: space
  Пробел — прыжок!
Отпущена: space
Нажата: right
Отпущена: right
```

## 2. Константы клавиш

| Клавиша | Константа | Клавиша | Константа |
|---|---|---|---|
| ← → ↑ ↓ | `K_LEFT`, `K_RIGHT`, `K_UP`, `K_DOWN` | Пробел | `K_SPACE` |
| A, W, S, D | `K_a`, `K_w`, `K_s`, `K_d` | Enter | `K_RETURN` |
| Escape | `K_ESCAPE` | Shift (левый) | `K_LSHIFT` |
| 0–9 | `K_0` ... `K_9` | P | `K_p` |

Константы букв пишутся **строчными** буквами: `K_a`, а не `K_A`.

## 3. Два способа: событие или состояние

| | Событие (`KEYDOWN`) | Состояние (`get_pressed()`) |
|---|---|---|
| Когда срабатывает | **Один раз** в момент нажатия | **На каждом кадре**, пока клавиша нажата |
| Где пишется | Внутри цикла `for event in ...` | После цикла событий |
| Для каких действий | Прыжок, выстрел, пауза, выбор в меню | Ходьба, бег, поворот |
| Аналогия | Дверной звонок: звенит один раз при нажатии | Педаль газа: едет, пока нажата |

**Правило:** «одноразовое» действие — событие; «длительное» действие — состояние.

## 4. Перемещение персонажа: get_pressed()

`pygame.key.get_pressed()` возвращает похожий на список объект с текущим состоянием всех клавиш: `keys[pygame.K_LEFT]` — `True`, если стрелка влево нажата прямо сейчас.

```python
import pygame

WIDTH, HEIGHT = 800, 600
SPEED = 5

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Перемещение персонажа")
clock = pygame.time.Clock()

player = pygame.Rect(0, 0, 50, 50)
player.center = (WIDTH // 2, HEIGHT // 2)

running = True
while running:
    # 1. События
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 2. Обновление
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] or keys[pygame.K_a]:
        player.x -= SPEED
    if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
        player.x += SPEED
    if keys[pygame.K_UP] or keys[pygame.K_w]:
        player.y -= SPEED
    if keys[pygame.K_DOWN] or keys[pygame.K_s]:
        player.y += SPEED

    # 3. Отрисовка
    screen.fill((30, 30, 30))
    pygame.draw.rect(screen, (0, 200, 255), player)
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

**Обратите внимание:** мы использовали не `if ... elif`, а четыре отдельных `if`. Поэтому при одновременном нажатии двух клавиш (→ и ↑) персонаж движется **по диагонали**.

**Скорость** (`SPEED`) — смещение за один кадр в пикселях. При 60 FPS `SPEED = 5` — это 300 пикселей в секунду.

## 5. Не выходить за границы экрана

**Способ 1 — условием:**

```python
if player.left < 0:
    player.left = 0
if player.right > WIDTH:
    player.right = WIDTH
if player.top < 0:
    player.top = 0
if player.bottom > HEIGHT:
    player.bottom = HEIGHT
```

**Способ 2 — `clamp_ip()`:** «запирает» Rect внутри другого Rect.

```python
player.clamp_ip(screen.get_rect())
```

**Способ 3 — «телепорт»** (выход с одной стороны экрана и вход с другой, как в Pac-Man):

```python
if player.right < 0:
    player.left = WIDTH
elif player.left > WIDTH:
    player.right = 0
```

## 6. Направление и изображение персонажа

Персонаж должен смотреть в ту сторону, куда идёт. Для этого запоминаем направление и используем два варианта изображения (`pygame.transform.flip`):

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 300))
player_right = pygame.Surface((60, 40), pygame.SRCALPHA)
pygame.draw.polygon(player_right, (255, 120, 0), [(0, 0), (60, 20), (0, 40)])  # «рыбка», смотрящая вправо
player_left = pygame.transform.flip(player_right, True, False)
rect = player_right.get_rect(center=(300, 150))
facing_right = True
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        rect.x -= 4
        facing_right = False
    if keys[pygame.K_RIGHT]:
        rect.x += 4
        facing_right = True
    rect.clamp_ip(screen.get_rect())
    screen.fill((0, 60, 120))
    screen.blit(player_right if facing_right else player_left, rect)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

## 7. Прыжок и сила тяжести

В платформерах (как Mario) персонаж прыгает и снова опускается на землю. Для этого нужны **вертикальная скорость** (`vel_y`) и **гравитация** (константа, прибавляемая к скорости на каждом кадре):

```text
В момент прыжка: vel_y = -15        (толчок вверх)
На каждом кадре: vel_y += GRAVITY   (сила тяжести замедляет, затем тянет вниз)
                 y     += vel_y
При касании земли: y = уровень земли, vel_y = 0
```

```python
import pygame

WIDTH, HEIGHT = 800, 400
GROUND = 350
GRAVITY = 0.8
JUMP_POWER = -15
SPEED = 5

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Прыжок: ← → и Space")
clock = pygame.time.Clock()

player = pygame.Rect(100, 0, 40, 60)
player.bottom = GROUND
vel_y = 0
on_ground = True

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            if on_ground:                 # прыгать можно только стоя на земле
                vel_y = JUMP_POWER
                on_ground = False

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player.x -= SPEED
    if keys[pygame.K_RIGHT]:
        player.x += SPEED

    vel_y += GRAVITY
    player.y += int(vel_y)
    if player.bottom >= GROUND:
        player.bottom = GROUND
        vel_y = 0
        on_ground = True
    player.clamp_ip(screen.get_rect())

    screen.fill((135, 206, 235))
    pygame.draw.rect(screen, (60, 160, 60), (0, GROUND, WIDTH, HEIGHT - GROUND))
    pygame.draw.rect(screen, (200, 50, 50), player)
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

Здесь используются оба способа: **ходьба** — `get_pressed()` (непрерывно), **прыжок** — `KEYDOWN` (один раз). Флаг `on_ground` не даёт «прыгнуть второй раз» в воздухе.

## 8. Сеточное (дискретное) перемещение

В шахматах, «Змейке», лабиринтах персонаж за одно нажатие перемещается на одну **клетку**. Здесь используем `KEYDOWN`:

```python
CELL = 40
col, row = 5, 5

def move(key, col, row):
    if key == "left":
        col -= 1
    elif key == "right":
        col += 1
    elif key == "up":
        row -= 1
    elif key == "down":
        row += 1
    return col, row

col, row = move("right", col, row)
col, row = move("up", col, row)
print("Клетка:", col, row, "| Пиксели:", col * CELL, row * CELL)
```

Результат:
```text
Клетка: 6 4 | Пиксели: 240 160
```

## Частые ошибки

**1. Ходьба через KEYDOWN** — персонаж сдвигается на 5 пикселей только один раз за нажатие, а если держать клавишу — останавливается. Для непрерывного движения — `get_pressed()`.

**2. Направления через `if ... elif`** — движение по диагонали невозможно, некоторые клавиши «проглатываются».

**3. Константа с заглавной буквой**
```python
import pygame
print(pygame.K_A)   # AttributeError: K_A не существует
```
Правильно: `pygame.K_a`.

**4. Вызов `get_pressed()` до цикла событий** — если очередь не прочитана, состояние клавиш не обновляется. Сначала `pygame.event.get()`, потом `get_pressed()`.

**5. Прибавление дробного числа к координате Rect** — `Rect` хранит только целые числа, `player.x += 0.5` ничего не меняет. Для дробной скорости храните координату в отдельной переменной `float` и пишите `player.x = int(pos_x)` (или используйте `pygame.math.Vector2`).

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `KEYDOWN` / `KEYUP` | Событие в момент нажатия / отпускания клавиши |
| `event.key` | Код клавиши в событии |
| `pygame.key.get_pressed()` | Текущее состояние всех клавиш |
| Константа клавиши | Коды клавиш вроде `K_LEFT`, `K_SPACE`, `K_a` |
| Скорость | Величина смещения за один кадр (пиксели/кадр) |
| `clamp_ip()` | Удержание Rect в пределах другого Rect |
| Гравитация | Константа, прибавляемая к вертикальной скорости на каждом кадре |
| Дискретное перемещение | Перемещение скачками из клетки в клетку по сетке |

## Примечание

- `pygame.key.get_pressed()` обновляет состояние клавиатуры только при обработке очереди событий (`pygame.event.get()` или `pygame.event.pump()`) — выполняйте этот вызов каждый кадр.
- Чтобы при удержании клавиши событие `KEYDOWN` повторялось, используют `pygame.key.set_repeat(delay, interval)` (в миллисекундах).
- При движении по диагонали скорость растёт в √2 раз. Для одинаковой скорости нормализуйте вектор направления: `pygame.math.Vector2(dx, dy).normalize()` (нулевой вектор нормализовать нельзя).
- Многие клавиатуры не распознают одновременно больше 3–4 клавиш (ghosting) — проверяйте комбинации клавиш, если управление рассчитано на двух игроков.
- Для ввода текста удобно событие `pygame.TEXTINPUT` (Pygame 2): `event.text` даёт символ с учётом раскладки.
