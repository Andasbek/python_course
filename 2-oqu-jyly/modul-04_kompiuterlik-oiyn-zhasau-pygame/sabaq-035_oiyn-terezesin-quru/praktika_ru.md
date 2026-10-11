# Урок 35. Практические задания: окно, цвета, координаты

> Во всех заданиях размер окна и цвета записывай в начале файла как константы. Для рисования пока используем только `screen.fill(color, (x, y, w, h))` и `screen.set_at()`.

## Базовый уровень

### Задание 1. «Координатный диктант» (в тетради)
Дано окно 800×600. Запиши координаты точек:
1. Центр окна.
2. Правый верхний угол.
3. Левый нижний угол.
4. Левый верхний угол квадрата 50×50, чтобы он касался правого нижнего угла.
5. Левый верхний угол прямоугольника 200×100, чтобы он стоял точно в центре.

### Задание 2. Трёхцветный флаг
В окне 600×400 нарисуй флаг из трёх горизонтальных полос (например, белая–синяя–красная). Высота полосы должна вычисляться через `HEIGHT // 3` — при изменении размера окна флаг не должен ломаться.

### Задание 3. Флаг с вертикальными полосами
Нарисуй флаг из трёх вертикальных полос (например, зелёная–белая–красная). Ширина полосы — `WIDTH // 3`.

### Задание 4. Настройки окна
Окно: 800×600, заголовок «Мой мир», размер изменяемый (`RESIZABLE`), значок — зелёный квадрат 32×32. При изменении размера окна в консоль должен выводиться новый размер.

Пример вывода:
```text
Новый размер: 1024 x 700
```

## Продвинутый уровень

### Задание 5. Палитра цветов
Раздели окно на сетку 4×2 и закрась каждую ячейку цветом по названию (`pygame.Color("red")`, `"orange"`, `"yellow"`, `"green"`, `"cyan"`, `"blue"`, `"purple"`, `"pink"`). Между ячейками должна быть белая рамка в 5 пикселей.

### Задание 6. Градиент
В окне 400×400 нарисуй градиент, плавно переходящий сверху вниз от чёрного к синему (каждая строка — высотой 1 пиксель, через `fill`).
**Подсказка:** `blue = y * 255 // (HEIGHT - 1)`.

### Задание 7. Четверти
Раздели окно через центр на 4 четверти и закрась каждую своим цветом. При щелчке мышью в консоль должно выводиться, по какой четверти щёлкнули («Верхняя левая», «Нижняя правая» и т.д.).
**Подсказка:** событие `pygame.MOUSEBUTTONDOWN` и `event.pos`.

## ★ Олимпиадная / творческая задача

**А. Шахматная доска.** Нарисуй доску 8×8 двумя цветами. Доска — в центре окна, размер клетки вычисляется по меньшей стороне окна (`min(WIDTH, HEIGHT) // 8`).

**Б. Случайный «пиксель-арт».** Заполни сетку из 20×20 клеток случайными цветами; клавиша `Space` — новая картинка, клавиша `S` — сохранение через `pygame.image.save(screen, "art.png")`.

## Домашнее задание

1. Используя цвета флага Казахстана (голубой `#00AFCA`, золотой `#FEC50A`), нарисуй эскиз «небо и солнце» только из прямоугольников.
2. Запиши в тетради координаты 5 важных точек (центр, 4 угла) для окна 1280×720.
3. К следующему уроку: посмотри в официальной документации (pygame.org/docs), какие функции есть в модуле `pygame.draw`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Ответы к заданию 1:** 1) `(400, 300)`; 2) `(799, 0)`; 3) `(0, 599)`; 4) `(750, 550)`; 5) `(300, 250)`.

**Задания 2–3 (два флага в одной программе, переключение через Space):**

```python
import pygame

WIDTH, HEIGHT = 600, 400
WHITE = (255, 255, 255)
BLUE = (0, 57, 166)
RED = (213, 43, 30)
GREEN = (0, 146, 70)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Флаги — нажмите Space")
clock = pygame.time.Clock()
vertical = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            vertical = not vertical
    if vertical:
        stripe = WIDTH // 3
        for i, color in enumerate([GREEN, WHITE, RED]):
            screen.fill(color, (i * stripe, 0, stripe, HEIGHT))
    else:
        stripe = HEIGHT // 3
        for i, color in enumerate([WHITE, BLUE, RED]):
            screen.fill(color, (0, i * stripe, WIDTH, stripe))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задание 4:**

```python
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT), pygame.RESIZABLE)
pygame.display.set_caption("Мой мир")
icon = pygame.Surface((32, 32))
icon.fill((0, 180, 0))
pygame.display.set_icon(icon)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.VIDEORESIZE:
            print(f"Новый размер: {event.w} x {event.h}")
    screen.fill((60, 60, 90))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задание 5. Палитра:**

```python
import pygame

WIDTH, HEIGHT = 800, 400
NAMES = ["red", "orange", "yellow", "green", "cyan", "blue", "purple", "pink"]
COLS, ROWS, GAP = 4, 2, 5

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Палитра")
cell_w = WIDTH // COLS
cell_h = HEIGHT // ROWS
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((255, 255, 255))
    for i, name in enumerate(NAMES):
        col = i % COLS
        row = i // COLS
        rect = (col * cell_w + GAP, row * cell_h + GAP,
                cell_w - 2 * GAP, cell_h - 2 * GAP)
        screen.fill(pygame.Color(name), rect)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задание 6. Градиент:**

```python
import pygame

WIDTH, HEIGHT = 400, 400

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Градиент")
for y in range(HEIGHT):
    blue = y * 255 // (HEIGHT - 1)
    screen.fill((0, 0, blue), (0, y, WIDTH, 1))
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задание 7. Четверти:**

```python
import pygame

WIDTH, HEIGHT = 600, 400
CX, CY = WIDTH // 2, HEIGHT // 2

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Четверти")
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            x, y = event.pos
            vertical = "Верхняя" if y < CY else "Нижняя"
            horizontal = "левая" if x < CX else "правая"
            print(vertical, horizontal, event.pos)
    screen.fill((230, 80, 80), (0, 0, CX, CY))
    screen.fill((80, 200, 80), (CX, 0, WIDTH - CX, CY))
    screen.fill((80, 80, 230), (0, CY, CX, HEIGHT - CY))
    screen.fill((240, 200, 60), (CX, CY, WIDTH - CX, HEIGHT - CY))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ А. Шахматная доска:**

```python
import pygame

WIDTH, HEIGHT = 800, 600
LIGHT = (240, 217, 181)
DARK = (181, 136, 99)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Шахматная доска")
cell = min(WIDTH, HEIGHT) // 8
left = (WIDTH - cell * 8) // 2
top = (HEIGHT - cell * 8) // 2
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((40, 40, 40))
    for row in range(8):
        for col in range(8):
            color = LIGHT if (row + col) % 2 == 0 else DARK
            screen.fill(color, (left + col * cell, top + row * cell, cell, cell))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ Б. Пиксель-арт:**

```python
import pygame
import random

SIZE, CELL = 20, 20
WIDTH = HEIGHT = SIZE * CELL


def draw_art(surface):
    for row in range(SIZE):
        for col in range(SIZE):
            color = (random.randint(0, 255), random.randint(0, 255),
                     random.randint(0, 255))
            surface.fill(color, (col * CELL, row * CELL, CELL, CELL))


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space — новая картинка, S — сохранить")
draw_art(screen)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                draw_art(screen)
            elif event.key == pygame.K_s:
                pygame.image.save(screen, "art.png")
                print("Сохранено: art.png")
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

</details>
