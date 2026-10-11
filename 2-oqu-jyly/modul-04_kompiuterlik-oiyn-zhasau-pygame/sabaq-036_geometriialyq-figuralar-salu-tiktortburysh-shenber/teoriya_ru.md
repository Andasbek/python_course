# Урок 36. Рисование геометрических фигур: прямоугольник, окружность, линия.

## Темы

1. Модуль pygame.draw
2. Прямоугольник
3. Окружность и круг
4. Линии
5. Многоугольник, эллипс, дуга
6. Объект pygame.Rect
7. Порядок рисования — слои
8. Полный пример: «Дом в ауле»
9. Частые ошибки
10. Основные понятия

## 1. Модуль pygame.draw

Первые два аргумента у всех функций одинаковые: **где рисуем** (Surface) и **каким цветом**.

| Функция | Что рисует | Основные аргументы |
|---|---|---|
| `draw.rect(surface, color, rect, width=0, border_radius=0)` | Прямоугольник | `rect = (x, y, w, h)` |
| `draw.circle(surface, color, center, radius, width=0)` | Окружность/круг | `center = (x, y)` |
| `draw.line(surface, color, start, end, width=1)` | Отрезок | две точки |
| `draw.lines(surface, color, closed, points, width=1)` | Ломаная линия | список точек |
| `draw.polygon(surface, color, points, width=0)` | Многоугольник | список точек |
| `draw.ellipse(surface, color, rect, width=0)` | Эллипс | описанный прямоугольник |
| `draw.arc(surface, color, rect, start, stop, width=1)` | Дуга | углы в радианах |
| `draw.aaline(surface, color, start, end)` | Сглаженная линия | антиалиасинг |

**Параметр `width`:** `0` (по умолчанию) — фигура **заливается**; `> 0` — рисуется только **контур** указанной толщины.

## 2. Прямоугольник

```python
pygame.draw.rect(screen, color, (x, y, ширина, высота), width, border_radius=...)
```

`(x, y)` — **левый верхний угол**.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 300))
pygame.display.set_caption("Прямоугольники")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((255, 255, 255))
    pygame.draw.rect(screen, (220, 50, 50), (50, 50, 150, 100))          # залитый
    pygame.draw.rect(screen, (50, 50, 220), (230, 50, 150, 100), 5)      # контур толщиной 5
    pygame.draw.rect(screen, (50, 180, 50), (410, 50, 150, 100),
                     border_radius=20)                                   # скруглённые углы
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

`border_radius` очень удобен, чтобы делать красивые кнопки и панели (например, кнопки игрового меню).

## 3. Окружность и круг

```python
pygame.draw.circle(screen, color, (cx, cy), radius, width)
```

Окружность задаётся **центром** и **радиусом** (в этом её отличие от прямоугольника!).

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 400))
pygame.display.set_caption("Мишень")
clock = pygame.time.Clock()
colors = [(255, 0, 0), (255, 255, 255)]
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((200, 200, 200))
    for i, radius in enumerate(range(150, 0, -30)):
        pygame.draw.circle(screen, colors[i % 2], (200, 200), radius)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

Здесь мы рисовали круги **от большего к меньшему**. Если рисовать наоборот, большой круг закрыл бы маленькие.

## 4. Линии

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
pygame.display.set_caption("Линии")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((0, 0, 0))
    pygame.draw.line(screen, (255, 255, 255), (0, 0), (399, 299), 3)   # диагональ
    for x in range(0, 400, 40):                                        # вертикальные линии сетки
        pygame.draw.line(screen, (60, 60, 60), (x, 0), (x, 299))
    zigzag = [(20, 250), (80, 200), (140, 250), (200, 200), (260, 250)]
    pygame.draw.lines(screen, (0, 255, 0), False, zigzag, 4)          # ломаная линия
    pygame.draw.aaline(screen, (255, 200, 0), (0, 299), (399, 0))      # сглаженная линия
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

`draw.lines(..., closed, points)`: если `closed=True`, последняя точка соединяется с первой.

## 5. Многоугольник, эллипс, дуга

```python
import math
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 250))
pygame.display.set_caption("Многоугольник, эллипс, дуга")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((255, 255, 255))
    pygame.draw.polygon(screen, (255, 140, 0), [(100, 30), (180, 200), (20, 200)])  # треугольник
    pygame.draw.ellipse(screen, (120, 0, 200), (220, 60, 160, 100))                 # эллипс
    pygame.draw.rect(screen, (200, 200, 200), (220, 60, 160, 100), 1)              # его «коробка»
    pygame.draw.arc(screen, (0, 120, 255), (420, 50, 150, 150), 0, math.pi, 6)    # дуга-полуокружность
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

- `ellipse` — эллипс, вписанный в заданный прямоугольник; если ширина и высота равны — круг.
- Углы `arc` задаются **в радианах**: `0` — справа, `math.pi / 2` — сверху, `math.pi` — слева. Перевод угла из градусов: `math.radians(90)`.

## 6. Объект pygame.Rect

В играх прямоугольник — самый важный объект: положение персонажа, зона столкновения, кнопка — всё это `Rect`. Он хранит только координаты (сам не рисуется).

```python
import pygame

player = pygame.Rect(100, 50, 40, 60)       # x, y, ширина, высота
print(player)
print("Лево:", player.left, "Право:", player.right)
print("Верх:", player.top, "Низ:", player.bottom)
print("Центр:", player.center)

player.center = (400, 300)                   # перемещаем центр
print("Новое положение:", player.topleft)
player.move_ip(10, -5)                       # перемещение на месте (in place)
print("После перемещения:", player.topleft)
```

Результат:
```text
<rect(100, 50, 40, 60)>
Лево: 100 Право: 140
Верх: 50 Низ: 110
Центр: (120, 80)
Новое положение: (380, 270)
После перемещения: (390, 265)
```

**Атрибуты Rect (схема):**

```text
 topleft ●───── midtop ─────● topright
         │                  │
 midleft ●     center ●     ● midright
         │                  │
 bottomleft ●── midbottom ──● bottomright
```

| Атрибут | Значение |
|---|---|
| `x`, `y`, `left`, `top` | Координаты левого верхнего угла |
| `right`, `bottom` | Правая граница (`x + w`), нижняя граница (`y + h`) |
| `width`, `height`, `size` | Размеры |
| `center`, `centerx`, `centery` | Центр |
| `topleft`, `bottomright`, `midtop` и т.д. | Углы и середины сторон |

**Польза:** поставить объект в центр экрана теперь можно одной строкой: `rect.center = (WIDTH // 2, HEIGHT // 2)`. «Поставить» на пол: `rect.bottom = HEIGHT`.

Рисование Rect: `pygame.draw.rect(screen, color, player)`.

## 7. Порядок рисования — слои

В Pygame фигура, нарисованная позже, ложится **поверх** предыдущей (как на холсте художника). Поэтому порядок рисования пейзажа: **небо → солнце → горы → земля → дом → окно**.

## 8. Полный пример: «Дом в ауле»

```python
import pygame

WIDTH, HEIGHT = 800, 600
SKY = (135, 206, 235)
GRASS = (60, 170, 60)
SUN = (255, 215, 0)
WALL = (205, 133, 63)
ROOF = (178, 34, 34)
GLASS = (173, 216, 230)
BROWN = (101, 67, 33)
LEAVES = (34, 120, 34)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Дом в ауле")
clock = pygame.time.Clock()

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    screen.fill(SKY)                                              # 1. небо
    pygame.draw.circle(screen, SUN, (680, 100), 60)               # 2. солнце
    for dx, dy in [(1, 0), (1, 1), (0, 1), (-1, 1),
                   (-1, 0), (-1, -1), (0, -1), (1, -1)]:          #    солнечные лучи
        pygame.draw.line(screen, SUN, (680, 100),
                         (680 + 90 * dx, 100 + 90 * dy), 3)
    pygame.draw.rect(screen, GRASS, (0, 450, WIDTH, 150))         # 3. земля
    house = pygame.Rect(0, 0, 260, 200)                           # 4. стена дома
    house.midbottom = (300, 470)
    pygame.draw.rect(screen, WALL, house)
    pygame.draw.polygon(screen, ROOF, [(house.left - 20, house.top),
                                       (house.centerx, house.top - 120),
                                       (house.right + 20, house.top)])   # 5. крыша
    window = pygame.Rect(0, 0, 70, 70)                            # 6. окно
    window.center = (house.left + 70, house.centery)
    pygame.draw.rect(screen, GLASS, window)
    pygame.draw.rect(screen, BROWN, window, 4)
    pygame.draw.line(screen, BROWN, window.midtop, window.midbottom, 3)
    pygame.draw.line(screen, BROWN, window.midleft, window.midright, 3)
    door = pygame.Rect(0, 0, 60, 110)                             # 7. дверь
    door.bottomright = (house.right - 30, house.bottom)
    pygame.draw.rect(screen, BROWN, door, border_radius=8)
    pygame.draw.circle(screen, SUN, (door.right - 12, door.centery), 5)
    pygame.draw.rect(screen, BROWN, (600, 330, 30, 130))          # 8. ствол дерева
    pygame.draw.circle(screen, LEAVES, (615, 300), 70)            #    крона дерева

    pygame.display.flip()
    clock.tick(30)

pygame.quit()
```

Обратите внимание: окно и дверь дома размещены **относительно** прямоугольника `house`. Если теперь изменить значение `house.midbottom`, весь дом переместится вместе с окном и дверью!

## Частые ошибки

**1. Передача угла вместо центра окружности** — окружность частично выходит за экран. Для окружности — **центр**, для прямоугольника — **левый верхний угол**.

**2. Перепутан порядок аргументов**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((200, 200))
pygame.draw.circle(screen, (100, 100), 50, (255, 0, 0))   # ошибка: цвет и центр перепутаны
```
Правильно: `pygame.draw.circle(screen, (255, 0, 0), (100, 100), 50)` — сначала поверхность, цвет, затем геометрия.

**3. Толщина контура больше радиуса** — при `width=60`, `radius=50` рисуется залитый круг или не рисуется ничего. `width` < радиуса.

**4. Закраска фона после фигур** — `screen.fill()` закрывает всё. Фон — **самое первое** действие каждого кадра.

**5. Углы `arc` в градусах** — `arc(..., 0, 180)` не рисует ожидаемую полуокружность. Правильно: `0, math.pi` или `math.radians(180)`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `pygame.draw` | Модуль для рисования геометрических фигур |
| Залитая фигура / контур | `width=0` / `width>0` |
| `pygame.Rect` | Объект, хранящий координаты и размеры прямоугольной области |
| `center`, `midbottom`, `topleft` | «Опорные» точки Rect |
| Многоугольник (polygon) | Замкнутая фигура, задаваемая списком точек |
| Радиан | Единица измерения угла: 180° = π |
| Слои (порядок рисования) | Фигура, нарисованная позже, закрывает предыдущую |
| Антиалиасинг | Сглаживание края линии (`aaline`) |

## Примечание

- В функциях `pygame.draw` `width=0` (по умолчанию) — заливка фигуры, `width > 0` — только контур. Если у окружности `width` больше радиуса, ничего не рисуется.
- Параметр `border_radius` появился в Pygame 2.0.0; в Pygame 1.9 его нет.
- `pygame.draw.line` и `circle` не сглаживают линии (нет антиалиасинга). Для гладких линий есть `pygame.draw.aaline()`.
- Все функции `pygame.draw` возвращают `Rect` изменённой области — его можно использовать для `pygame.display.update(rect)`.
- Документация `pygame.draw`: https://www.pygame.org/docs/ref/draw.html
