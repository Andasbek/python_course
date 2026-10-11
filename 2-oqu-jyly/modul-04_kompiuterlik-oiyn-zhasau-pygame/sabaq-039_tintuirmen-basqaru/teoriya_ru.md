# Урок 39. Управление мышью. Обработка событий (event loop).

## Темы

1. События мыши
2. Состояние мыши: pygame.mouse
3. Щёлкнули по объекту? collidepoint()
4. Создание кнопки: hover и click
5. Перетаскивание (drag and drop)
6. Цикл обработки событий (event loop) подробнее
7. Собственное событие: USEREVENT и таймер
8. Сравнение способов управления
9. Частые ошибки
10. Основные понятия

## 1. События мыши

| Событие | Когда | Полезные атрибуты |
|---|---|---|
| `MOUSEBUTTONDOWN` | При нажатии кнопки | `event.pos`, `event.button` |
| `MOUSEBUTTONUP` | При отпускании кнопки | `event.pos`, `event.button` |
| `MOUSEMOTION` | При движении мыши | `event.pos`, `event.rel`, `event.buttons` |
| `MOUSEWHEEL` | При прокрутке колёсика | `event.y` (+1 вверх, −1 вниз) |

**Значения `event.button`:**

| Значение | Кнопка |
|---|---|
| 1 | Левая |
| 2 | Средняя (нажатие колёсика) |
| 3 | Правая |

- `event.pos` — координаты `(x, y)` точки щелчка;
- `event.rel` — насколько сместилась мышь относительно предыдущей позиции `(dx, dy)`;
- `event.buttons` — какие кнопки нажаты во время `MOUSEMOTION` `(левая, средняя, правая)`.

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((500, 400))
pygame.display.set_caption("События мыши")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            print("Нажата:", event.button, "точка:", event.pos)
        elif event.type == pygame.MOUSEBUTTONUP:
            print("Отпущена:", event.button)
        elif event.type == pygame.MOUSEWHEEL:
            print("Колёсико:", event.y)
    screen.fill((40, 40, 40))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

Результат (пример):
```text
Нажата: 1 точка: (120, 85)
Отпущена: 1
Нажата: 3 точка: (300, 200)
Отпущена: 3
Колёсико: 1
Колёсико: -1
```

## 2. Состояние мыши: pygame.mouse

Как и у клавиатуры, «состояние» мыши можно запросить в любой момент:

| Функция | Что возвращает |
|---|---|
| `pygame.mouse.get_pos()` | Текущая позиция курсора `(x, y)` |
| `pygame.mouse.get_pressed()` | `(левая, средняя, правая)` — `True`/`False` |
| `pygame.mouse.set_visible(False)` | Скрыть системный курсор |
| `pygame.mouse.set_pos((x, y))` | Переместить курсор |

**Пример: рисуем «прицел» на месте курсора.** Скрываем системный курсор и рисуем вместо него свой знак:

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Прицел")
pygame.mouse.set_visible(False)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    x, y = pygame.mouse.get_pos()
    left, middle, right = pygame.mouse.get_pressed()
    color = (255, 0, 0) if left else (0, 255, 0)
    screen.fill((20, 20, 20))
    pygame.draw.circle(screen, color, (x, y), 20, 2)
    pygame.draw.line(screen, color, (x - 30, y), (x + 30, y), 2)
    pygame.draw.line(screen, color, (x, y - 30), (x, y + 30), 2)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Что когда использовать?**
- одиночный «клик» (выстрел, нажатие кнопки) — событие `MOUSEBUTTONDOWN`;
- рисование с зажатой кнопкой, прицеливание, движение персонажа к курсору — `get_pos()` / `get_pressed()`.

## 3. Щёлкнули по объекту? collidepoint()

`rect.collidepoint(point)` — возвращает `True`, если точка находится внутри прямоугольника. Это основа любой кнопки, мишени, карточки.

```python
import pygame

button = pygame.Rect(100, 100, 200, 60)
print(button.collidepoint((150, 120)))
print(button.collidepoint((50, 50)))
```

Результат:
```text
True
False
```

**Для круглой мишени** — формула расстояния (теорема Пифагора):

```python
import math

def inside_circle(point, center, radius):
    return math.dist(point, center) <= radius

print(inside_circle((105, 100), (100, 100), 20))
print(inside_circle((130, 100), (100, 100), 20))
```

Результат:
```text
True
False
```

## 4. Создание кнопки: hover и click

Игровая кнопка меняет цвет, когда на неё наводят курсор (hover), а при нажатии выполняет действие:

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
pygame.display.set_caption("Кнопка")
clock = pygame.time.Clock()
button = pygame.Rect(0, 0, 200, 60)
button.center = (200, 150)
clicks = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if button.collidepoint(event.pos):
                clicks += 1
                pygame.display.set_caption(f"Нажато: {clicks} раз")
    hover = button.collidepoint(pygame.mouse.get_pos())
    color = (80, 180, 255) if hover else (40, 110, 200)
    screen.fill((30, 30, 30))
    pygame.draw.rect(screen, color, button, border_radius=12)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

Для текста на кнопке используется модуль `pygame.font`; из таких кнопок можно собрать игровое меню.

## 5. Перетаскивание (drag and drop)

Перетаскивание состоит из трёх событий:
1. `MOUSEBUTTONDOWN` — если нажали на объект, мы его «схватили» (`dragging = True`) и запоминаем разницу между курсором и углом объекта (`offset`);
2. `MOUSEMOTION` — если держим объект, перемещаем его вместе с курсором;
3. `MOUSEBUTTONUP` — «отпустили» (`dragging = False`).

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Перетаскивание")
clock = pygame.time.Clock()
box = pygame.Rect(250, 150, 100, 100)
dragging = False
offset_x = offset_y = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if box.collidepoint(event.pos):
                dragging = True
                offset_x = box.x - event.pos[0]
                offset_y = box.y - event.pos[1]
        elif event.type == pygame.MOUSEBUTTONUP and event.button == 1:
            dragging = False
        elif event.type == pygame.MOUSEMOTION and dragging:
            box.x = event.pos[0] + offset_x
            box.y = event.pos[1] + offset_y
    screen.fill((240, 240, 240))
    pygame.draw.rect(screen, (255, 150, 0) if dragging else (0, 150, 255), box)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

Без `offset` объект «прыгал» бы к курсору своим левым верхним углом.

## 6. Цикл обработки событий (event loop) подробнее

**Очередь событий.** Операционная система ставит все действия пользователя в очередь (queue) как события. На каждом кадре мы опустошаем очередь:

```text
Пользователь ──▶  [ MOUSEMOTION | KEYDOWN | MOUSEBUTTONDOWN | ... ]  ──▶  pygame.event.get()
                         очередь событий (FIFO: первым пришёл — первым вышел)
```

| Функция | Что делает |
|---|---|
| `pygame.event.get()` | Забирает все события и очищает очередь (основная в играх) |
| `pygame.event.wait()` | Останавливает программу и ждёт, пока придёт событие (для неигровых программ) |
| `pygame.event.post(event)` | Добавить своё событие в очередь |
| `pygame.event.set_blocked(type)` | Игнорировать определённый тип событий |

**Хорошая структура — вынести обработку событий в функцию.** Чем длиннее цикл, тем удобнее обрабатывать события в отдельной функции:

```python
import pygame


def handle_events(state):
    """Обрабатывает события; возвращает True, если игру нужно продолжать."""
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            return False
        if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
            return False
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            state["points"].append(event.pos)
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 3:
            state["points"].clear()
    return True


pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Левая — точка, правая — очистить")
clock = pygame.time.Clock()
state = {"points": []}
while handle_events(state):
    screen.fill((255, 255, 255))
    if len(state["points"]) > 1:
        pygame.draw.lines(screen, (0, 0, 200), False, state["points"], 3)
    for p in state["points"]:
        pygame.draw.circle(screen, (200, 0, 0), p, 5)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

## 7. Собственное событие: USEREVENT и таймер

`pygame.time.set_timer(event_type, ms)` — каждые `ms` миллисекунд добавляет наше событие в очередь. Например, чтобы каждую секунду появлялась новая мишень:

```python
import random
import pygame

SPAWN = pygame.USEREVENT + 1          # собственный тип события

pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Мишени: щёлкни и уничтожь!")
pygame.time.set_timer(SPAWN, 1000)    # каждые 1000 мс
clock = pygame.time.Clock()
targets = []
score = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == SPAWN:
            rect = pygame.Rect(random.randint(0, 560), random.randint(0, 360), 40, 40)
            targets.append(rect)
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            for t in targets[:]:
                if t.collidepoint(event.pos):
                    targets.remove(t)
                    score += 1
                    pygame.display.set_caption(f"Очки: {score}")
                    break
    screen.fill((20, 20, 40))
    for t in targets:
        pygame.draw.rect(screen, (255, 80, 80), t, border_radius=20)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`targets[:]` — проходим по копии списка, потому что внутри цикла удаляем элементы из оригинала.

## 8. Сравнение способов управления

| Устройство | Событие (один раз) | Состояние (непрерывно) |
|---|---|---|
| Клавиатура | `KEYDOWN`, `KEYUP` | `pygame.key.get_pressed()` |
| Мышь | `MOUSEBUTTONDOWN`, `MOUSEBUTTONUP`, `MOUSEWHEEL` | `pygame.mouse.get_pos()`, `get_pressed()` |
| Таймер | `USEREVENT` + `set_timer` | `pygame.time.get_ticks()` |

## Частые ошибки

**1. Проверка `MOUSEBUTTONDOWN` без номера кнопки** — прокрутка колёсика в старых системах тоже приходит как `button` 4/5, и «выстрел» срабатывает случайно. Для левой кнопки: `event.button == 1`.

**2. Использование `event.pos` вне `MOUSEBUTTONDOWN`**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((300, 200))
for event in pygame.event.get():
    print(event.pos)   # AttributeError: у событий QUIT, KEYDOWN нет pos
```
Правильно: сначала проверять `event.type`.

**3. Удаление из списка внутри цикла по нему** — `for t in targets: targets.remove(t)` пропускает некоторые элементы. Нужно проходить по копии: `for t in targets[:]`.

**4. Нет `offset` при перетаскивании** — объект «прыгает» к курсору.

**5. Проверка «одиночного» нажатия через `get_pressed()`** — при 60 FPS одно нажатие длится 5–10 кадров, и действие выполняется несколько раз. Одноразовое действие — через событие.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `MOUSEBUTTONDOWN` / `UP` | Кнопка мыши нажата / отпущена |
| `MOUSEMOTION` | Мышь сдвинулась; `rel` — величина смещения |
| `MOUSEWHEEL` | Колёсико прокручено; `y` — направление |
| `event.button` | 1 — левая, 2 — средняя, 3 — правая |
| `collidepoint()` | Проверка, что точка внутри Rect |
| Hover | Состояние, когда курсор находится над объектом |
| Drag and drop | Перетаскивание объекта мышью |
| Очередь событий | Последовательность действий (FIFO), читается через `event.get()` |
| `USEREVENT`, `set_timer` | Собственный тип события и его периодическое создание |

## Примечание

- Значения `event.button`: 1 — левая, 2 — средняя, 3 — правая кнопка. В Pygame 2 для колёсика есть отдельное событие `pygame.MOUSEWHEEL` (`event.y` — направление прокрутки).
- `pygame.mouse.get_pressed()` по умолчанию возвращает кортеж из трёх элементов; `get_pressed(num_buttons=5)` даёт и дополнительные кнопки.
- `pygame.time.set_timer(event, millis)` запускает таймер, `set_timer(event, 0)` — останавливает. С Pygame 2.0.1 есть параметр `loops`: `loops=1` — сработать один раз.
- Для собственных событий надёжнее вместо `USEREVENT + 1` использовать `pygame.event.custom_type()` — он выдаёт неповторяющийся номер.
- Скрыть курсор — `pygame.mouse.set_visible(False)`, изменить вид системного курсора — `pygame.mouse.set_cursor(pygame.SYSTEM_CURSOR_HAND)` (Pygame 2).
