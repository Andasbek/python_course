# Урок 42. Счётчик очков, количество жизней, уровни. Вывод текста на экран.

## Темы

1. Три шага вывода текста
2. Выбор шрифта и казахские буквы
3. Размещение текста
4. Состояние игры (game state)
5. Отображение жизней значками
6. Уровни: постепенное усложнение игры
7. Сохранение рекорда в файл
8. Полный пример: HUD, уровни, Game Over
9. Частые ошибки
10. Основные понятия

## 1. Три шага вывода текста

В Pygame текст не пишется прямо на экран. Сначала из текста создаётся **изображение** (Surface), затем оно выводится через blit:

```text
 1. Загрузка шрифта       2. Превращение текста в «картинку»     3. Вывод на экран
 font = Font(...)   ──▶   text = font.render("Очки: 10", ...)  ──▶   screen.blit(text, (10, 10))
```

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 300))
pygame.display.set_caption("Текст")
font = pygame.font.Font(None, 48)                       # 1. шрифт по умолчанию, размер 48
text = font.render("Score: 120", True, (255, 255, 0))   # 2. текст → Surface
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    screen.fill((0, 0, 50))
    screen.blit(text, (20, 20))                         # 3. на экран
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**`render(text, antialias, color, background=None)`:**

| Аргумент | Значение |
|---|---|
| `text` | Выводимая строка (одна строка; `\n` не работает) |
| `antialias` | `True` — сглаженные края (всегда пишите `True`) |
| `color` | Цвет текста |
| `background` | Цвет фона (если не задан — прозрачный) |

## 2. Выбор шрифта и казахские буквы

| Способ | Пример | Особенность |
|---|---|---|
| Шрифт по умолчанию | `pygame.font.Font(None, 36)` | Есть везде, но кириллица и казахские буквы (ә, ғ, қ, ң, ө, ұ, ү, һ, і) могут отображаться квадратами |
| Системный шрифт | `pygame.font.SysFont("arial", 36)` | Шрифт, установленный на компьютере; на другом компьютере его может не быть |
| Из файла | `pygame.font.Font("assets/fonts/Roboto-Regular.ttf", 36)` | **Самый надёжный**: шрифт поставляется вместе с проектом |

**Рекомендация:** скачайте с сайта Google Fonts бесплатный шрифт с поддержкой казахских букв (Roboto, Noto Sans, PT Sans, Montserrat) и положите его в папку `assets/fonts`. Безопасная функция загрузки:

```python
import os
import pygame

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
FONT_PATH = os.path.join(BASE_DIR, "assets", "fonts", "Roboto-Regular.ttf")


def get_font(size):
    """Возвращает шрифт проекта, если его нет — системный Arial, если и его нет — шрифт по умолчанию."""
    if os.path.exists(FONT_PATH):
        return pygame.font.Font(FONT_PATH, size)
    return pygame.font.SysFont("arial, dejavusans, freesans", size)
```

В `SysFont` можно передать несколько названий через запятую — используется первое найденное, если не найдено ни одно — шрифт по умолчанию.

**Важно:** создавайте шрифт (`Font`) **один раз** до игрового цикла — загрузка на каждом кадре сильно замедляет игру.

## 3. Размещение текста

Текст — это Surface, поэтому приём `get_rect()` работает точно так же, как с изображениями:

```python
title = font.render("ИГРА ОКОНЧЕНА", True, (255, 50, 50))
title_rect = title.get_rect(center=(WIDTH // 2, HEIGHT // 2))     # точно в центр
screen.blit(title, title_rect)

lives_text = font.render("Жизни: 3", True, (255, 255, 255))
screen.blit(lives_text, lives_text.get_rect(topright=(WIDTH - 10, 10)))   # в правый угол
```

**Вспомогательная функция вывода текста** — сокращает код:

```python
import pygame


def draw_text(surface, text, font, color, **position):
    """Выводит текст в заданную позицию: draw_text(screen, "Привет", font, WHITE, center=(400, 300))."""
    image = font.render(text, True, color)
    rect = image.get_rect(**position)
    surface.blit(image, rect)
    return rect
```

`**position` — приём именованных аргументов в виде словаря (`center=...`, `topleft=...` и т.д.).

**Текст с тенью** — улучшает читаемость: сначала выводим чёрным цветом со смещением на 2 пикселя, затем основным цветом.

## 4. Состояние игры (game state)

Состояние игры — все переменные, описывающие текущее положение дел в игре:

| Переменная | Пример значения | Для чего |
|---|---|---|
| `score` | 120 | Очки |
| `lives` | 3 | Оставшиеся жизни |
| `level` | 2 | Текущий уровень |
| `high_score` | 450 | Рекорд |
| `game_over` | `False` | Окончена ли игра |
| `start_time` | 15320 | Для подсчёта времени (мс) |

Их удобно объединить в словарь или отдельный класс:

```python
class GameState:
    """Текущее состояние игры."""

    def __init__(self):
        self.score = 0
        self.lives = 3
        self.level = 1
        self.game_over = False

    def add_points(self, points):
        self.score += points
        self.level = 1 + self.score // 100     # каждые 100 очков — новый уровень

    def lose_life(self):
        self.lives -= 1
        if self.lives <= 0:
            self.game_over = True


state = GameState()
state.add_points(250)
state.lose_life()
print(state.score, state.level, state.lives, state.game_over)
```

Результат:
```text
250 3 2 False
```

## 5. Отображение жизней значками

Значки-сердечки вместо числа — наглядно и красиво:

```python
import pygame


def make_heart(size=24):
    heart = pygame.Surface((size, size), pygame.SRCALPHA)
    r = size // 4
    pygame.draw.circle(heart, (230, 30, 60), (r, r + 2), r)
    pygame.draw.circle(heart, (230, 30, 60), (3 * r, r + 2), r)
    pygame.draw.polygon(heart, (230, 30, 60), [(0, r + 4), (size, r + 4), (size // 2, size)])
    return heart


def draw_lives(surface, heart, lives, x, y):
    for i in range(lives):
        surface.blit(heart, (x + i * (heart.get_width() + 5), y))
```

## 6. Уровни: постепенное усложнение игры

С ростом уровня игра должна усложняться, но оставаться «честной». От уровня можно сделать зависимыми:

| Параметр | Уровень 1 | Уровень 2 | Уровень 3 | Пример формулы |
|---|---|---|---|---|
| Скорость врагов | 2–4 | 3–5 | 4–6 | `randint(1 + level, 3 + level)` |
| Интервал появления | 900 мс | 750 мс | 600 мс | `max(250, 1050 - 150 * level)` |
| Виды врагов | Простые | + быстрые | + зигзаг | `types[:level]` |

При смене уровня нужно обновить таймер:

```python
import pygame

SPAWN = pygame.USEREVENT + 1


def spawn_delay(level):
    return max(250, 1050 - 150 * level)


def on_level_up(level):
    pygame.time.set_timer(SPAWN, spawn_delay(level))   # перезапуск с новым интервалом


for lvl in range(1, 6):
    print("Уровень", lvl, "→", spawn_delay(lvl), "мс")
```

Результат:
```text
Уровень 1 → 900 мс
Уровень 2 → 750 мс
Уровень 3 → 600 мс
Уровень 4 → 450 мс
Уровень 5 → 300 мс
```

Чтобы сообщить игроку о смене уровня, выводим в центре экрана на 2 секунды надпись «УРОВЕНЬ 2!» (время отслеживаем через `get_ticks()`).

## 7. Сохранение рекорда в файл

```python
HIGHSCORE_FILE = "highscore.txt"


def load_high_score():
    try:
        with open(HIGHSCORE_FILE, "r", encoding="utf-8") as f:
            return int(f.read().strip() or 0)
    except (FileNotFoundError, ValueError):
        return 0


def save_high_score(score):
    with open(HIGHSCORE_FILE, "w", encoding="utf-8") as f:
        f.write(str(score))


best = load_high_score()
current = 340
if current > best:
    save_high_score(current)
    print("Новый рекорд!", current)
```

Результат (при первом запуске):
```text
Новый рекорд! 340
```

## 8. Полный пример: HUD, уровни, Game Over

```python
import random
import pygame

WIDTH, HEIGHT = 800, 600
SPAWN = pygame.USEREVENT + 1
WHITE, YELLOW, RED = (255, 255, 255), (255, 220, 0), (230, 40, 40)
HIGHSCORE_FILE = "highscore.txt"


def load_high_score():
    try:
        with open(HIGHSCORE_FILE, encoding="utf-8") as f:
            return int(f.read().strip() or 0)
    except (FileNotFoundError, ValueError):
        return 0


def save_high_score(score):
    with open(HIGHSCORE_FILE, "w", encoding="utf-8") as f:
        f.write(str(score))


def draw_text(surface, text, font, color, **pos):
    shadow = font.render(text, True, (0, 0, 0))
    image = font.render(text, True, color)
    rect = image.get_rect(**pos)
    surface.blit(shadow, rect.move(2, 2))
    surface.blit(image, rect)


class Coin(pygame.sprite.Sprite):
    def __init__(self, level):
        super().__init__()
        self.image = pygame.Surface((26, 26), pygame.SRCALPHA)
        pygame.draw.circle(self.image, YELLOW, (13, 13), 13)
        self.rect = self.image.get_rect(midbottom=(random.randint(20, WIDTH - 20), 0))
        self.speed = random.randint(1 + level, 3 + level)

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            self.kill()


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Монетный дождь")
clock = pygame.time.Clock()
font = pygame.font.SysFont("arial, dejavusans", 28)
big_font = pygame.font.SysFont("arial, dejavusans", 64, bold=True)

basket = pygame.Rect(0, 0, 100, 20)
basket.midbottom = (WIDTH // 2, HEIGHT - 10)
coins = pygame.sprite.Group()
score, lives, level = 0, 3, 1
high_score = load_high_score()
level_message_until = pygame.time.get_ticks() + 2000
game_over = False
pygame.time.set_timer(SPAWN, 900)

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == SPAWN and not game_over:
            coins.add(Coin(level))
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_r and game_over:
            score, lives, level, game_over = 0, 3, 1, False
            coins.empty()
            pygame.time.set_timer(SPAWN, 900)

    if not game_over:
        keys = pygame.key.get_pressed()
        basket.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 8
        basket.clamp_ip(screen.get_rect())
        for coin in list(coins):
            coin.update()
            if coin.rect.colliderect(basket):
                coin.kill()
                score += 10
            elif not coin.alive():
                lives -= 1
        new_level = 1 + score // 100
        if new_level != level:
            level = new_level
            pygame.time.set_timer(SPAWN, max(250, 1050 - 150 * level))
            level_message_until = pygame.time.get_ticks() + 2000
        if lives <= 0:
            game_over = True
            if score > high_score:
                high_score = score
                save_high_score(score)

    screen.fill((20, 30, 60))
    coins.draw(screen)
    pygame.draw.rect(screen, (150, 90, 40), basket, border_radius=6)
    draw_text(screen, f"Очки: {score}", font, WHITE, topleft=(10, 10))
    draw_text(screen, f"Рекорд: {high_score}", font, YELLOW, midtop=(WIDTH // 2, 10))
    draw_text(screen, f"Жизни: {lives}   Уровень: {level}", font, WHITE, topright=(WIDTH - 10, 10))
    if pygame.time.get_ticks() < level_message_until and not game_over:
        draw_text(screen, f"УРОВЕНЬ {level}", big_font, YELLOW, center=(WIDTH // 2, HEIGHT // 2))
    if game_over:
        draw_text(screen, "ИГРА ОКОНЧЕНА", big_font, RED, center=(WIDTH // 2, HEIGHT // 2 - 30))
        draw_text(screen, "Нажмите R, чтобы начать заново", font, WHITE,
                  center=(WIDTH // 2, HEIGHT // 2 + 40))
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

**Разбор:** пропущенная монета (`kill()` за пределами экрана) уменьшает жизни; каждые 100 очков — новый уровень и более частые монеты; при окончании игры проверяется рекорд; клавиша `R` возвращает состояние к начальным значениям.

## Частые ошибки

**1. Создание шрифта в цикле**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((300, 200))
for frame in range(3):
    font = pygame.font.Font(None, 36)   # загрузка на каждом кадре — очень медленно
```
Правильно: шрифт — до цикла; в цикле только `render`.

**2. Нет `pygame.init()`** — `pygame.error: font not initialized`.

**3. Передача числа напрямую в render**
```python
import pygame
pygame.init()
font = pygame.font.Font(None, 36)
score = 10
image = font.render(score, True, (255, 255, 255))   # TypeError: text must be a unicode or bytes
```
Правильно: `font.render(f"Очки: {score}", True, ...)` или `str(score)`.

**4. Квадраты (□) вместо кириллицы и казахских букв** — в шрифте нет этих символов. Добавьте в проект TTF-шрифт с поддержкой казахских букв.

**5. Многострочный текст через `\n`** — `render` не понимает перевод строки. Рендерите каждую строку отдельно, увеличивая `y`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `pygame.font.Font` / `SysFont` | Загрузка шрифта из файла / из системы |
| `render()` | Создание Surface из текста |
| Антиалиасинг | Сглаживание краёв букв |
| HUD | Информационная панель на игровом экране (очки, жизни, уровень) |
| Состояние игры | Набор переменных, описывающих текущее положение дел в игре |
| Уровень | Ступень сложности игры |
| Рекорд (high score) | Лучший результат, хранящийся в файле |
| TTF | Формат файла шрифта (TrueType Font) |

## Примечание

- `Font.render()` не переносит строку по символу `\n` — каждую строку многострочного текста рисуйте отдельно.
- `render()` — сравнительно медленная операция. Если текст не меняется (например, заголовок), отрисуйте его один раз и сохраните готовый Surface; счёт перерисовывайте только при изменении.
- Для форматирования чисел удобны f-строки: `f"{score:06d}"` → `000150`, `f"{time_left:.1f}"` → `12.5`.
- При чтении файла рекорда обрабатывайте через `try...except` случаи `FileNotFoundError` (файла нет) и `ValueError` (в файле не число).
- Документация `pygame.font`: https://www.pygame.org/docs/ref/font.html
