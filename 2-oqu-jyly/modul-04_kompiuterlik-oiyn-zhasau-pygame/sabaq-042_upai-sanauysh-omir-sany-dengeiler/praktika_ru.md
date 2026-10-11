# Урок 42. Практические задания: очки, жизни, уровни, текст

> Подготовка: скачай с Google Fonts шрифт с поддержкой казахских букв (например, Roboto или Noto Sans) и положи его в папку `assets/fonts`. Если его нет — используй `SysFont("arial")`.

## Базовый уровень

### Задание 1. «Привет, игрок!»
Выведи точно в центр окна 800×600 крупным шрифтом (64) надпись `Привет, игрок!`, а под ней шрифтом поменьше (28) — сегодняшнюю дату (`datetime.date.today()`). Кириллица (и казахские буквы, если используешь их) должна отображаться правильно.

### Задание 2. Счётчик щелчков
При каждом щелчке мыши счётчик увеличивается на 1 и показывается в левом верхнем углу экрана в виде `Щелчков: 7`. Клавиша `R` обнуляет счётчик.

### Задание 3. Секундомер
В центре экрана должно отображаться время с начала игры в формате `00:12.4` (минуты:секунды.десятые). `Space` — пауза/продолжение.
**Подсказка:** `pygame.time.get_ticks()`; формат: `f"{m:02d}:{s:02d}.{d}"`.

### Задание 4. HUD для «Защитника космоса»
Перенеси в своём проекте с 41-го урока информацию из заголовка на экран:
- слева вверху — `Очки: 120`;
- справа вверху — жизни значками-сердечками (`make_heart`, раздел 5 теории);
- по центру вверху — `Уровень: 2`.

## Продвинутый уровень

### Задание 5. Уровни
В «Защитнике космоса» уровень должен повышаться каждые 100 очков:
- метеориты быстрее (`randint(1 + level, 3 + level)`);
- появляются чаще (`max(200, 700 - 80 * level)` мс);
- при смене уровня в центре экрана на 2 секунды появляется надпись «УРОВЕНЬ 3!».

### Задание 6. Game Over и рекорд
- Когда жизни закончились, игра останавливается, на экране — «ИГРА ОКОНЧЕНА», очки и рекорд;
- рекорд хранится в файле `highscore.txt`;
- если рекорд новый — мигает надпись «НОВЫЙ РЕКОРД!»;
- `R` — начать заново, `Escape` — выход.

### Задание 7. «Всплывающие» очки
В месте уничтожения метеорита должна появляться надпись `+10`, которая 40 кадров поднимается вверх, постепенно становясь прозрачной, и исчезает (спрайт `FloatingText`, `set_alpha`).

## ★ Олимпиадная / творческая задача

**А. Таблица 5 лучших результатов.** В конце игры игрок вводит имя с клавиатуры (событие `TEXTINPUT` или `event.unicode`), результат записывается в файл `leaderboard.txt` в виде `Имя;очки`. На экране показывается таблица 5 лучших, отсортированных по очкам.

**Б. Многострочный текст.** Напиши функцию `draw_multiline(surface, text, font, color, x, y, max_width)`: длинный текст разбивается по словам и переносится на строки шириной не более `max_width` (для экрана правил игры).

## Домашнее задание

1. Доделай «Защитника космоса» с HUD, уровнями и рекордом и загрузи на GitHub (добавь `highscore.txt` в `.gitignore`).
2. Нарисуй на бумаге эскиз HUD для командного проекта: что показывается и где.
3. К следующему уроку: найди на сайтах freesound.org, kenney.nl/assets (Audio) 2–3 бесплатных звука (WAV/OGG) для своей игры.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–3 (в одной программе):**

```python
import datetime
import pygame

WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)


def draw_text(surface, text, font, color, **pos):
    image = font.render(text, True, color)
    surface.blit(image, image.get_rect(**pos))


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Упражнения с текстом")
big = pygame.font.SysFont("arial, dejavusans", 64)
small = pygame.font.SysFont("arial, dejavusans", 28)
clock = pygame.time.Clock()
clicks = 0
elapsed = 0          # мс, время, накопленное до паузы
started = pygame.time.get_ticks()
paused = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            clicks += 1
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r:
                clicks = 0
            elif event.key == pygame.K_SPACE:
                if paused:
                    started = pygame.time.get_ticks()
                else:
                    elapsed += pygame.time.get_ticks() - started
                paused = not paused
    total = elapsed if paused else elapsed + pygame.time.get_ticks() - started
    minutes, rest = divmod(total, 60_000)
    seconds, ms = divmod(rest, 1000)
    screen.fill((25, 35, 70))
    draw_text(screen, "Привет, игрок!", big, WHITE, center=(WIDTH // 2, HEIGHT // 2 - 40))
    draw_text(screen, str(datetime.date.today()), small, WHITE,
              center=(WIDTH // 2, HEIGHT // 2 + 20))
    draw_text(screen, f"Щелчков: {clicks}", small, WHITE, topleft=(10, 10))
    draw_text(screen, f"{minutes:02d}:{seconds:02d}.{ms // 100}", big, (255, 220, 0),
              center=(WIDTH // 2, HEIGHT - 100))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задания 4–6.** Полная структура соответствует примеру «Монетный дождь» из теории (раздел 8): `draw_text`, формула уровня, обновление `set_timer`, `load_high_score` / `save_high_score`, перезапуск по `R`. Вывод сердечек:

```python
import pygame


def make_heart(size=24):
    heart = pygame.Surface((size, size), pygame.SRCALPHA)
    r = size // 4
    pygame.draw.circle(heart, (230, 30, 60), (r, r + 2), r)
    pygame.draw.circle(heart, (230, 30, 60), (3 * r, r + 2), r)
    pygame.draw.polygon(heart, (230, 30, 60), [(0, r + 4), (size, r + 4), (size // 2, size)])
    return heart


def draw_lives_right(surface, heart, lives, right, top):
    """Выводит сердечки рядом, выровненные по правому краю."""
    step = heart.get_width() + 5
    for i in range(lives):
        surface.blit(heart, (right - (i + 1) * step, top))
```

Мигание «НОВЫЙ РЕКОРД!»: `if new_record and pygame.time.get_ticks() // 400 % 2 == 0: draw_text(...)`.

**Задание 7. Всплывающие очки:**

```python
import pygame


class FloatingText(pygame.sprite.Sprite):
    """Надпись, поднимающаяся вверх и постепенно исчезающая."""

    def __init__(self, text, font, color, pos, life=40):
        super().__init__()
        self.base = font.render(text, True, color)
        self.image = self.base.copy()
        self.rect = self.image.get_rect(center=pos)
        self.life = life
        self.max_life = life

    def update(self):
        self.life -= 1
        if self.life <= 0:
            self.kill()
            return
        self.rect.y -= 1
        self.image = self.base.copy()
        self.image.set_alpha(255 * self.life // self.max_life)


pygame.init()
screen = pygame.display.set_mode((600, 400))
font = pygame.font.SysFont("arial", 28, bold=True)
texts = pygame.sprite.Group()
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            texts.add(FloatingText("+10", font, (255, 220, 0), event.pos))
    texts.update()
    screen.fill((10, 10, 30))
    texts.draw(screen)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

В «Защитнике космоса»: `for meteor in hits: texts.add(FloatingText("+10", font, YELLOW, meteor.rect.center))`.

**★ А. Таблица 5 лучших (логика и ввод):**

```python
import pygame

LEADERBOARD = "leaderboard.txt"


def load_leaderboard():
    rows = []
    try:
        with open(LEADERBOARD, encoding="utf-8") as f:
            for line in f:
                if ";" in line:
                    name, points = line.strip().rsplit(";", 1)
                    rows.append((name, int(points)))
    except FileNotFoundError:
        pass
    return sorted(rows, key=lambda r: r[1], reverse=True)[:5]


def add_result(name, points):
    with open(LEADERBOARD, "a", encoding="utf-8") as f:
        f.write(f"{name};{points}\n")


pygame.init()
screen = pygame.display.set_mode((600, 450))
pygame.display.set_caption("Введи имя и нажми Enter")
font = pygame.font.SysFont("arial, dejavusans", 30)
clock = pygame.time.Clock()
score = 270
name = ""
entered = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and not entered:
            if event.key == pygame.K_RETURN and name.strip():
                add_result(name.strip(), score)
                entered = True
            elif event.key == pygame.K_BACKSPACE:
                name = name[:-1]
        elif event.type == pygame.TEXTINPUT and not entered and len(name) < 12:
            name += event.text
    screen.fill((20, 20, 40))
    if not entered:
        screen.blit(font.render(f"Твои очки: {score}", True, (255, 255, 255)), (40, 40))
        screen.blit(font.render(f"Имя: {name}_", True, (255, 220, 0)), (40, 100))
    else:
        screen.blit(font.render("ЛУЧШИЕ 5", True, (255, 220, 0)), (40, 30))
        for i, (n, p) in enumerate(load_leaderboard(), start=1):
            screen.blit(font.render(f"{i}. {n} — {p}", True, (255, 255, 255)), (40, 40 + i * 50))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ Б. Многострочный текст:**

```python
import pygame


def draw_multiline(surface, text, font, color, x, y, max_width):
    """Выводит текст, перенося его по словам на строки; возвращает последний y."""
    line = ""
    for word in text.split():
        test = f"{line} {word}".strip()
        if font.size(test)[0] <= max_width:
            line = test
        else:
            surface.blit(font.render(line, True, color), (x, y))
            y += font.get_linesize()
            line = word
    if line:
        surface.blit(font.render(line, True, color), (x, y))
        y += font.get_linesize()
    return y
```

`font.size(text)` — вычисляет, какой `(ширина, высота)` будет у текста после render, не рисуя его.

</details>
