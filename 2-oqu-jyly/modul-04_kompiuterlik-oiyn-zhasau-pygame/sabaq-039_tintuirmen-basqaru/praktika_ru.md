# Урок 39. Практические задания: управление мышью и события

## Базовый уровень

### Задание 1. Координаты мыши
В заголовке окна должны непрерывно отображаться координаты курсора: `X: 245  Y: 130`. Пока нажата левая кнопка, фон красный, иначе — чёрный.

### Задание 2. Круг в месте щелчка
В каждом месте щелчка левой кнопкой должен появляться круг радиусом 15 случайного цвета (точки хранятся в списке). Правая кнопка — очистить всё.

### Задание 3. Hover-кнопка
В центре окна кнопка 220×70. Когда курсор над ней, её цвет становится светлее. При нажатии цвет фона случайно меняется.

### Задание 4. Персонаж, следующий за курсором
Персонаж (круг) должен двигаться к курсору со скоростью 3 пикселя/кадр (не прыгать сразу).
**Подсказка:** по `x`: если курсор правее — `x += 3`, если левее — `x -= 3`; по `y` аналогично. Если до курсора меньше 3 пикселей — не двигается.

## Продвинутый уровень

### Задание 5. «Рисовалка» (Paint)
- При движении с зажатой левой кнопкой рисуется линия;
- клавиши `1`–`5` меняют цвет;
- колёсико меняет размер кисти в диапазоне 2–40;
- `C` — очистить холст.
**Подсказка:** храни нарисованное на отдельной поверхности `canvas = pygame.Surface(...)` и на каждом кадре делай её blit на экран. Чтобы линия не прерывалась, при `MOUSEMOTION` рисуй `draw.line` от предыдущей точки к новой (поможет `event.rel`).

### Задание 6. Игра «Поймай мышку»
Каждые 800 мс (`USEREVENT` + `set_timer`) в случайном месте появляется мишень и через 2 секунды исчезает. Щелчок по мишени — +1 очко, щелчок мимо — −1. Очки показываются в заголовке. Через 30 секунд игра заканчивается, результат выводится в консоль.

### Задание 7. Сортировка цветов (drag and drop)
Внизу 6 цветных квадратов (вперемешку красные и синие), вверху две «корзины»: красная и синяя. Квадраты нужно перетащить в соответствующую корзину. Когда все квадраты на месте — «Молодец!».

## ★ Олимпиадная / творческая задача

**А. Сетка «Сапёра».** Сетка 10×10, 12 скрытых мин. Левая кнопка — открыть клетку (количество мин вокруг показывать пока в консоли или цветом), правая кнопка — поставить/снять флажок. Если нажать на мину — игра окончена.

**Б. Стрельба (принцип Angry Birds).** «Оттягивание» мышью: вектор между точкой нажатия и точкой отпускания становится начальной скоростью мяча (в обратном направлении), мяч летит с учётом гравитации.

## Домашнее задание

1. Добавь в «Рисовалку» из задания 5 сохранение рисунка в PNG по клавише `S`.
2. Используется ли мышь в вашем командном проекте? Для каких действий? Запиши.
3. Повтори тему 11-го урока (ООП): класс, `__init__`, методы — на следующем уроке будем писать классы-спрайты.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–2:**

```python
import random
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 400))
clock = pygame.time.Clock()
dots = []
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            if event.button == 1:
                color = (random.randint(50, 255), random.randint(50, 255),
                         random.randint(50, 255))
                dots.append((event.pos, color))
            elif event.button == 3:
                dots.clear()
    x, y = pygame.mouse.get_pos()
    pygame.display.set_caption(f"X: {x}  Y: {y}")
    bg = (150, 0, 0) if pygame.mouse.get_pressed()[0] else (0, 0, 0)
    screen.fill(bg)
    for pos, color in dots:
        pygame.draw.circle(screen, color, pos, 15)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задания 3–4:**

```python
import random
import pygame

WIDTH, HEIGHT = 700, 500
SPEED = 3

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Кнопка и преследователь")
clock = pygame.time.Clock()
button = pygame.Rect(0, 0, 220, 70)
button.center = (WIDTH // 2, HEIGHT // 2)
bg = (30, 30, 30)
hx, hy = 50, 50
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if button.collidepoint(event.pos):
                bg = (random.randint(0, 120), random.randint(0, 120), random.randint(0, 120))
    mx, my = pygame.mouse.get_pos()
    if abs(mx - hx) > SPEED:
        hx += SPEED if mx > hx else -SPEED
    if abs(my - hy) > SPEED:
        hy += SPEED if my > hy else -SPEED
    hover = button.collidepoint((mx, my))
    screen.fill(bg)
    pygame.draw.rect(screen, (90, 200, 255) if hover else (40, 120, 200), button,
                     border_radius=15)
    pygame.draw.circle(screen, (255, 200, 0), (hx, hy), 18)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задание 5. Рисовалка (с клавишей S из домашнего задания):**

```python
import pygame

WIDTH, HEIGHT = 900, 600
PALETTE = {pygame.K_1: (0, 0, 0), pygame.K_2: (220, 0, 0), pygame.K_3: (0, 160, 0),
           pygame.K_4: (0, 0, 220), pygame.K_5: (255, 200, 0)}

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
canvas = pygame.Surface((WIDTH, HEIGHT))
canvas.fill((255, 255, 255))
clock = pygame.time.Clock()
color = (0, 0, 0)
brush = 6
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key in PALETTE:
                color = PALETTE[event.key]
            elif event.key == pygame.K_c:
                canvas.fill((255, 255, 255))
            elif event.key == pygame.K_s:
                pygame.image.save(canvas, "suret.png")
                print("Сохранено")
        elif event.type == pygame.MOUSEWHEEL:
            brush = max(2, min(40, brush + event.y * 2))
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            pygame.draw.circle(canvas, color, event.pos, brush // 2)
        elif event.type == pygame.MOUSEMOTION and event.buttons[0]:
            start = (event.pos[0] - event.rel[0], event.pos[1] - event.rel[1])
            pygame.draw.line(canvas, color, start, event.pos, brush)
            pygame.draw.circle(canvas, color, event.pos, brush // 2)
    pygame.display.set_caption(f"Кисть: {brush}px | 1-5 цвет, C очистить, S сохранить")
    screen.blit(canvas, (0, 0))
    pygame.draw.circle(screen, color, pygame.mouse.get_pos(), brush // 2, 1)
    pygame.display.flip()
    clock.tick(120)
pygame.quit()
```

Имя файла лучше писать латиницей (`suret.png`) — в некоторых системах имена файлов на кириллице вызывают трудности.

**Задание 6. Поймай мышку:**

```python
import random
import pygame

WIDTH, HEIGHT = 700, 500
SPAWN = pygame.USEREVENT + 1
GAME_TIME = 30_000
LIFETIME = 2000

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
pygame.time.set_timer(SPAWN, 800)
targets = []          # [rect, время появления]
score = 0
start = pygame.time.get_ticks()
running = True
while running:
    now = pygame.time.get_ticks()
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == SPAWN:
            rect = pygame.Rect(random.randint(0, WIDTH - 50), random.randint(0, HEIGHT - 50), 50, 50)
            targets.append([rect, now])
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            hit = False
            for item in targets[:]:
                if item[0].collidepoint(event.pos):
                    targets.remove(item)
                    hit = True
                    break
            score += 1 if hit else -1
    targets = [t for t in targets if now - t[1] < LIFETIME]
    left = max(0, (GAME_TIME - (now - start)) // 1000)
    pygame.display.set_caption(f"Очки: {score} | Время: {left} с")
    if now - start >= GAME_TIME:
        print("Игра окончена! Очки:", score)
        running = False
    screen.fill((30, 60, 30))
    for rect, _ in targets:
        pygame.draw.ellipse(screen, (150, 150, 150), rect)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задание 7. Сортировка цветов:**

```python
import random
import pygame

WIDTH, HEIGHT = 700, 500
RED, BLUE = (220, 50, 50), (50, 90, 220)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Перетащи квадраты в корзины")
clock = pygame.time.Clock()
baskets = {RED: pygame.Rect(80, 40, 250, 160), BLUE: pygame.Rect(370, 40, 250, 160)}
squares = []
for i in range(6):
    color = RED if i % 2 == 0 else BLUE
    squares.append([pygame.Rect(60 + i * 100, 380, 60, 60), color])
random.shuffle(squares)
for i, sq in enumerate(squares):
    sq[0].x = 60 + i * 100
dragged = None
offset = (0, 0)
done = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            for sq in reversed(squares):
                if sq[0].collidepoint(event.pos):
                    dragged = sq
                    offset = (sq[0].x - event.pos[0], sq[0].y - event.pos[1])
                    break
        elif event.type == pygame.MOUSEBUTTONUP and event.button == 1:
            dragged = None
            ok = all(baskets[c].contains(r) for r, c in squares)
            if ok and not done:
                print("Молодец!")
                done = True
        elif event.type == pygame.MOUSEMOTION and dragged:
            dragged[0].topleft = (event.pos[0] + offset[0], event.pos[1] + offset[1])
    screen.fill((245, 245, 245))
    for color, rect in baskets.items():
        pygame.draw.rect(screen, color, rect, 4, border_radius=10)
    for rect, color in squares:
        pygame.draw.rect(screen, color, rect)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`rect.contains(other)` — `True`, если `other` полностью находится внутри `rect`.

**★ А. Сетка «Сапёра» (основная логика):**

```python
import random
import pygame

N, CELL, MINES = 10, 40, 12

pygame.init()
screen = pygame.display.set_mode((N * CELL, N * CELL))
pygame.display.set_caption("Сапёр")
clock = pygame.time.Clock()
mines = set(random.sample([(c, r) for c in range(N) for r in range(N)], MINES))
opened, flags = set(), set()
game_over = False


def neighbours(c, r):
    return sum((c + dc, r + dr) in mines
               for dc in (-1, 0, 1) for dr in (-1, 0, 1) if dc or dr)


running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and not game_over:
            cell = (event.pos[0] // CELL, event.pos[1] // CELL)
            if event.button == 1 and cell not in flags:
                if cell in mines:
                    print("Бум! Игра окончена")
                    game_over = True
                else:
                    opened.add(cell)
                    print("Мин вокруг:", neighbours(*cell))
            elif event.button == 3 and cell not in opened:
                flags ^= {cell}
    screen.fill((180, 180, 180))
    for c in range(N):
        for r in range(N):
            rect = (c * CELL + 1, r * CELL + 1, CELL - 2, CELL - 2)
            if (c, r) in opened:
                shade = 255 - neighbours(c, r) * 30
                pygame.draw.rect(screen, (shade, shade, 255), rect)
            elif (c, r) in flags:
                pygame.draw.rect(screen, (230, 60, 60), rect)
            elif game_over and (c, r) in mines:
                pygame.draw.rect(screen, (0, 0, 0), rect)
            else:
                pygame.draw.rect(screen, (120, 120, 120), rect)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

(Вывод чисел в клетках добавляется после 42-го урока.)

**★ Б. Стрельба:**

```python
import pygame

WIDTH, HEIGHT = 900, 500
GRAVITY = 0.4

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Оттяни и отпусти!")
clock = pygame.time.Clock()
start_pos = (120, 380)
ball = list(start_pos)
vel = [0.0, 0.0]
flying = False
pulling = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1 and not flying:
            pulling = True
        elif event.type == pygame.MOUSEBUTTONUP and event.button == 1 and pulling:
            pulling = False
            flying = True
            mx, my = event.pos
            vel = [(start_pos[0] - mx) * 0.15, (start_pos[1] - my) * 0.15]
    if flying:
        vel[1] += GRAVITY
        ball[0] += vel[0]
        ball[1] += vel[1]
        if ball[1] > HEIGHT or ball[0] > WIDTH or ball[0] < 0:
            ball = list(start_pos)
            flying = False
    screen.fill((180, 220, 255))
    pygame.draw.rect(screen, (80, 160, 80), (0, 420, WIDTH, 80))
    if pulling:
        pygame.draw.line(screen, (90, 50, 20), start_pos, pygame.mouse.get_pos(), 3)
    pygame.draw.circle(screen, (220, 30, 30), (int(ball[0]), int(ball[1])), 15)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

</details>
