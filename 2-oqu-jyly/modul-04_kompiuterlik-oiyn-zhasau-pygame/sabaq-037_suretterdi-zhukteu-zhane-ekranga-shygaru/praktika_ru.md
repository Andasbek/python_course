# Урок 37. Практические задания: изображения, Surface, blit

> Подготовка: создай в папке проекта `assets/images`. Положи туда с сайта Kenney.nl (или из архива, выданного учителем) фон и 2–3 изображения персонажей/предметов (PNG). Даже если изображений нет, функция `load_image()` покажет цветной квадрат — программа всё равно будет работать.

## Базовый уровень

### Задание 1. Свой Surface
Создай Surface размером 200×120: белый фон, на нём синяя рамка (`draw.rect`, `width=4`) и в центре зелёный круг. Сделай blit этой поверхности в четыре угла окна.

### Задание 2. Фон и персонаж
Скопируй функцию `load_image()` из теории. Окно 800×600:
- выведи фон, растянутый на всё окно (`convert()`);
- выведи персонажа (100×100) внизу по центру (`midbottom`) (`convert_alpha()`).

### Задание 3. Ряд персонажей
Выведи одно изображение (60×60) в верхней части окна 8 раз в один ряд с одинаковыми промежутками. Используй цикл.

### Задание 4. Зеркало
Выведи персонажа в левой половине окна, а его зеркальное отражение (`flip`) — симметрично в правой половине. Проведи посередине вертикальную линию.

## Продвинутый уровень

### Задание 5. Вращающаяся звезда
Изображение звезды (или монеты) должно вращаться на месте в центре окна. Не забудь поворачивать оригинал и использовать `get_rect(center=...)` по центру.

### Задание 6. Пульсация
Размер изображения должен постепенно увеличиваться от 50 до 150 пикселей и снова уменьшаться (эффект «дыхания»). Центр всегда остаётся в одной точке.

### Задание 7. Полупрозрачное окно
Выведи поверх игрового фона полупрозрачную чёрную панель (`Surface` + `set_alpha(150)`) и помести в её центр изображение персонажа. Это основа будущего экрана «Пауза».

## ★ Олимпиадная / творческая задача

**А. Параллакс-фон.** Двухслойный фон: дальние горы движутся влево медленно (1 пиксель/кадр), ближние деревья — быстро (3 пикселя/кадр). Чтобы фон был бесконечным, делай blit каждого слоя дважды рядом.

**Б. Галерея изображений.** Автоматически загрузи все PNG-файлы из папки `assets/images` (`os.listdir`) и выведи их уменьшенными в сетке (4 столбца). Подпись с именем файла под каждым изображением можно добавить после 42-го урока.

## Домашнее задание

1. Найди набор ресурсов для будущей командной игры (фон, персонаж, враг, бонус) и положи их в папку `assets/images`. Запиши в README источник ресурсов и лицензию.
2. Доделай задание 2 со своими ресурсами.
3. Подумай: какую переменную нужно менять, чтобы перемещать персонажа с клавиатуры?

## Для педагога: решения

<details><summary>Показать решения</summary>

Все решения ниже используют функцию `load_image()` (раздел 5 теории). Для краткости она повторяется в начале каждого решения.

**Задание 1:**

```python
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Мой Surface")
card = pygame.Surface((200, 120))
card.fill((255, 255, 255))
pygame.draw.rect(card, (0, 0, 200), card.get_rect(), 4)
pygame.draw.circle(card, (0, 170, 0), (100, 60), 35)
corners = ["topleft", "topright", "bottomleft", "bottomright"]
screen_rect = screen.get_rect()
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((50, 50, 50))
    for corner in corners:
        rect = card.get_rect(**{corner: getattr(screen_rect, corner)})
        screen.blit(card, rect)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

(Простой вариант: четыре строки `blit` — `(0, 0)`, `(600, 0)`, `(0, 480)`, `(600, 480)`.)

**Задания 2–4 (в одной программе):**

```python
import os
import pygame

WIDTH, HEIGHT = 800, 600
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")


def load_image(name, size=None, alpha=True):
    path = os.path.join(IMG_DIR, name)
    try:
        image = pygame.image.load(path)
        image = image.convert_alpha() if alpha else image.convert()
    except (pygame.error, FileNotFoundError):
        image = pygame.Surface(size or (50, 50))
        image.fill((255, 0, 255))
    if size:
        image = pygame.transform.scale(image, size)
    return image


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Фон и персонажи")
background = load_image("background.jpg", (WIDTH, HEIGHT), alpha=False)
hero = load_image("player.png", (100, 100))
hero_mirror = pygame.transform.flip(hero, True, False)
small = load_image("player.png", (60, 60))
gap = (WIDTH - 8 * 60) // 9

clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.blit(background, (0, 0))
    for i in range(8):                                      # задание 3
        screen.blit(small, (gap + i * (60 + gap), 20))
    screen.blit(hero, hero.get_rect(midbottom=(WIDTH // 2, HEIGHT)))   # задание 2
    pygame.draw.line(screen, (255, 255, 255), (WIDTH // 2, 120), (WIDTH // 2, 400), 2)
    screen.blit(hero, hero.get_rect(center=(WIDTH // 4, 260)))         # задание 4
    screen.blit(hero_mirror, hero_mirror.get_rect(center=(3 * WIDTH // 4, 260)))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задания 5–6 (вращение и пульсация):**

```python
import pygame

WIDTH, HEIGHT = 600, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Вращение и пульсация")
original = pygame.Surface((100, 100), pygame.SRCALPHA)   # поверхность с прозрачным фоном
pygame.draw.polygon(original, (255, 215, 0),
                    [(50, 0), (62, 38), (100, 38), (69, 61), (81, 100),
                     (50, 76), (19, 100), (31, 61), (0, 38), (38, 38)])
angle = 0
size = 50
grow = 2
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    angle = (angle + 2) % 360
    size += grow
    if size >= 150 or size <= 50:
        grow = -grow
    spinning = pygame.transform.rotate(original, angle)
    pulsing = pygame.transform.scale(original, (size, size))
    screen.fill((20, 20, 60))
    screen.blit(spinning, spinning.get_rect(center=(WIDTH // 4, HEIGHT // 2)))
    screen.blit(pulsing, pulsing.get_rect(center=(3 * WIDTH // 4, HEIGHT // 2)))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`pygame.SRCALPHA` — флаг для создания Surface с прозрачным фоном (удобно, когда нет файла изображения).

**Задание 7. Полупрозрачная панель:**

```python
import pygame

WIDTH, HEIGHT = 800, 600

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Панель паузы")
panel = pygame.Surface((400, 250))
panel.fill((0, 0, 0))
panel.set_alpha(150)
panel_rect = panel.get_rect(center=(WIDTH // 2, HEIGHT // 2))
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((100, 180, 255))
    pygame.draw.rect(screen, (40, 160, 40), (0, 450, WIDTH, 150))
    pygame.draw.circle(screen, (255, 220, 0), (650, 100), 60)
    screen.blit(panel, panel_rect)
    pygame.draw.circle(screen, (255, 80, 80), panel_rect.center, 40)
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ А. Параллакс:**

```python
import pygame

WIDTH, HEIGHT = 800, 400

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Параллакс")

far = pygame.Surface((WIDTH, HEIGHT), pygame.SRCALPHA)
for x in range(0, WIDTH, 200):
    pygame.draw.polygon(far, (110, 110, 160), [(x, 300), (x + 100, 120), (x + 200, 300)])
near = pygame.Surface((WIDTH, HEIGHT), pygame.SRCALPHA)
for x in range(0, WIDTH, 100):
    pygame.draw.rect(near, (90, 60, 30), (x + 45, 300, 10, 60))
    pygame.draw.circle(near, (30, 130, 30), (x + 50, 290), 30)

far_x = 0
near_x = 0
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    far_x = (far_x - 1) % WIDTH
    near_x = (near_x - 3) % WIDTH
    screen.fill((180, 220, 255))
    screen.blit(far, (far_x - WIDTH, 0))
    screen.blit(far, (far_x, 0))
    pygame.draw.rect(screen, (60, 160, 60), (0, 350, WIDTH, 50))
    screen.blit(near, (near_x - WIDTH, 0))
    screen.blit(near, (near_x, 0))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**★ Б. Галерея:**

```python
import os
import pygame

WIDTH, HEIGHT = 800, 600
COLS, THUMB, GAP = 4, 150, 40
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
IMG_DIR = os.path.join(BASE_DIR, "assets", "images")

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Галерея")
thumbs = []
if os.path.isdir(IMG_DIR):
    for name in sorted(os.listdir(IMG_DIR)):
        if name.lower().endswith(".png"):
            img = pygame.image.load(os.path.join(IMG_DIR, name)).convert_alpha()
            thumbs.append(pygame.transform.smoothscale(img, (THUMB, THUMB)))
print("Загружено изображений:", len(thumbs))
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((30, 30, 30))
    for i, img in enumerate(thumbs):
        x = GAP + (i % COLS) * (THUMB + GAP)
        y = GAP + (i // COLS) * (THUMB + GAP)
        screen.blit(img, (x, y))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

</details>
