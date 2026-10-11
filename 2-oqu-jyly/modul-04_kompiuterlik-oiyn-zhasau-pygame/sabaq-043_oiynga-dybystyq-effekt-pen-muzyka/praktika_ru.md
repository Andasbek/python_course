# Урок 43. Практические задания: звуковые эффекты и музыка

> Подготовка: положи в папку `assets/sounds` 3–4 бесплатных звука (например, kenney.nl → «Digital Audio», «Interface Sounds»; opengameart.org). Если файлов нет — создай тон функцией `make_tone()` из теории. Работа только в наушниках!

## Базовый уровень

### Задание 1. Звуковые кнопки
В окне три цветных прямоугольника-кнопки. При щелчке мышью по каждой воспроизводится свой звук (`collidepoint` из 39-го урока). Пока звук играет, кнопка должна подсвечиваться.

### Задание 2. Фоновая музыка
При запуске программы музыка должна плавно начинаться (`fade_ms=2000`) и повторяться бесконечно. Клавиши:
- `P` — пауза/продолжение;
- `↑` / `↓` — увеличить/уменьшить громкость на 0.1 (в пределах 0.0–1.0), текущее значение показывается на экране;
- `S` — плавно заглушить за 1,5 секунды.

### Задание 3. SoundManager
Скопируй в свой проект класс `SoundManager` из теории. Попробуй специально загрузить несуществующий файл — игра не должна упасть, в консоль должно вывестись предупреждение.

### Задание 4. Звуки в «Защитнике космоса»
Добавь в проект: звук выстрела, взрыв метеорита, попадание в корабль, переход на уровень, Game Over (музыка `fadeout`). `M` — выключить/включить весь звук; состояние показывается в HUD значком или текстом.

## Продвинутый уровень

### Задание 5. Разные взрывы
Загрузи 3 варианта звука взрыва (или 3 тона разной частоты) и при каждом взрыве воспроизводи случайный. При взрыве большого метеорита звук должен быть громче.

### Задание 6. Нотная мелодия
С помощью `make_tone()` напиши проигрыватель казахской детской песенки «Көк тиін» или простой мелодии на твой выбор: ноты и длительности хранятся в списке (`[(262, 0.3), (294, 0.3), ...]`), при нажатии `Space` мелодия играет с начала. Ноты должны воспроизводиться одна за другой по времени (`get_ticks`) — не используй `time.sleep()` (окно зависнет).

### Задание 7. Громкость зависит от расстояния
На экране неподвижное «радио» (круг), из которого бесконечно звучит звук (`loops=-1`). Чем ближе курсор мыши («слушатель») к радио, тем громче звук, чем дальше — тем тише.
**Подсказка:** `volume = max(0, 1 - distance / 500)`; `channel = sound.play(-1)`, `channel.set_volume(volume)`.

## ★ Олимпиадная / творческая задача

**А. Стереопанорама.** Разовьй задание 7: если курсор слева от радио, звук сильнее слышен в левом ухе, если справа — в правом. `channel.set_volume(left, right)` — вариант с двумя аргументами.

**Б. «Ритм-игра».** С верхней части экрана по четырём дорожкам падают ноты (прямоугольники). В момент, когда нота достигает нижней линии, нужно нажать соответствующую клавишу (`D F J K`): точно попал — звучит тон ноты и +очки; ошибся — звук «шума».

## Домашнее задание

1. Доделай «Защитника космоса» со звуками и загрузи на GitHub. Добавь в README раздел «Ресурсы»: название, источник, лицензия каждого звука.
2. Составь для командного проекта «карту звуков»: событие → звуковой файл → громкость.
3. Подумай: какие кнопки должны быть в меню игры? Что происходит во время паузы? (Тема следующего урока.)

## Для педагога: решения

<details><summary>Показать решения</summary>

Все решения используют функцию `make_tone()`, поэтому запускаются и без звуковых файлов. Если файлы есть, вместо `make_tone(...)` пишется `pygame.mixer.Sound("assets/sounds/...")`.

**Задание 1:**

```python
import math
from array import array
import pygame


def make_tone(frequency, duration=0.3, volume=0.4):
    rate, size, channels = pygame.mixer.get_init()
    n = int(rate * duration)
    samples = array("h")
    for i in range(n):
        value = int(volume * (1 - i / n) * 32767 * math.sin(2 * math.pi * frequency * i / rate))
        samples.extend([value] * channels)
    return pygame.mixer.Sound(buffer=samples)


pygame.mixer.pre_init(44100, -16, 2, 512)
pygame.init()
screen = pygame.display.set_mode((600, 300))
pygame.display.set_caption("Звуковые кнопки")
clock = pygame.time.Clock()
buttons = [
    {"rect": pygame.Rect(40, 90, 150, 120), "color": (220, 60, 60), "sound": make_tone(262)},
    {"rect": pygame.Rect(225, 90, 150, 120), "color": (60, 200, 60), "sound": make_tone(392)},
    {"rect": pygame.Rect(410, 90, 150, 120), "color": (60, 110, 230), "sound": make_tone(523)},
]
for b in buttons:
    b["channel"] = None
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            for b in buttons:
                if b["rect"].collidepoint(event.pos):
                    b["channel"] = b["sound"].play()
    screen.fill((25, 25, 25))
    for b in buttons:
        playing = b["channel"] is not None and b["channel"].get_busy()
        color = tuple(min(255, c + 70) for c in b["color"]) if playing else b["color"]
        pygame.draw.rect(screen, color, b["rect"], border_radius=12)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`sound.play()` возвращает объект `Channel`, на котором звучит звук; `channel.get_busy()` — играет ли он ещё.

**Задание 2. Управление музыкой** (нужен файл `theme.ogg`; если файла нет, выводится предупреждение):

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((500, 200))
pygame.display.set_caption("P — пауза, ↑↓ — громкость, S — заглушить")
font = pygame.font.SysFont("arial", 30)
clock = pygame.time.Clock()
volume = 0.5
paused = False
try:
    pygame.mixer.music.load("assets/sounds/theme.ogg")
    pygame.mixer.music.set_volume(volume)
    pygame.mixer.music.play(-1, fade_ms=2000)
except (pygame.error, FileNotFoundError):
    print("Файл музыки не найден")
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_p:
                if paused:
                    pygame.mixer.music.unpause()
                else:
                    pygame.mixer.music.pause()
                paused = not paused
            elif event.key in (pygame.K_UP, pygame.K_DOWN):
                step = 0.1 if event.key == pygame.K_UP else -0.1
                volume = round(min(1.0, max(0.0, volume + step)), 1)
                pygame.mixer.music.set_volume(volume)
            elif event.key == pygame.K_s:
                pygame.mixer.music.fadeout(1500)
    screen.fill((20, 20, 50))
    status = "Пауза" if paused else "Играет"
    screen.blit(font.render(f"Громкость: {volume:.1f}  |  {status}", True, (255, 255, 255)), (30, 80))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**Задания 3–4.** `SoundManager` — раздел 5 теории. Точки подключения в «Защитнике космоса»:
- внутри `Player.shoot()` → `sounds.play("shoot")`;
- если результат `groupcollide` не пуст → `sounds.play("boom")`;
- попадание в корабль → `sounds.play("hit")`;
- смена уровня → `sounds.play("level")`;
- `lives <= 0` → `pygame.mixer.music.fadeout(1500)`;
- `KEYDOWN` + `K_m` → `sounds.toggle_mute()`; HUD: `"Звук: ВКЛ" if sounds.enabled else "Звук: ВЫКЛ"`.

**Задание 5:**

```python
import random

# boom_sounds = [make_tone(110, 0.3), make_tone(90, 0.35), make_tone(70, 0.4)]


def play_explosion(boom_sounds, big=False):
    sound = random.choice(boom_sounds)
    sound.set_volume(1.0 if big else 0.5)
    sound.play()
```

**Задание 6. Нотная мелодия:**

```python
import math
from array import array
import pygame

MELODY = [(392, 0.3), (330, 0.3), (330, 0.3), (349, 0.3), (294, 0.3), (294, 0.3),
          (262, 0.3), (330, 0.3), (392, 0.6)]


def make_tone(frequency, duration, volume=0.4):
    rate, size, channels = pygame.mixer.get_init()
    n = int(rate * duration)
    samples = array("h")
    for i in range(n):
        value = int(volume * (1 - i / n) * 32767 * math.sin(2 * math.pi * frequency * i / rate))
        samples.extend([value] * channels)
    return pygame.mixer.Sound(buffer=samples)


pygame.mixer.pre_init(44100, -16, 2, 512)
pygame.init()
screen = pygame.display.set_mode((400, 200))
pygame.display.set_caption("Space — сыграть мелодию")
clock = pygame.time.Clock()
notes = [(make_tone(f, d), int(d * 1000)) for f, d in MELODY]
index = -1             # -1 — не играет
next_time = 0
running = True
while running:
    now = pygame.time.get_ticks()
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            index = 0
            next_time = now
    if 0 <= index < len(notes) and now >= next_time:
        sound, length = notes[index]
        sound.play()
        next_time = now + length
        index += 1
    screen.fill((40, 0, 60) if 0 <= index < len(notes) else (20, 20, 20))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задание 7 и ★ А (стерео):**

```python
import math
from array import array
import pygame


def make_hum(frequency=220, duration=1.0):
    rate, size, channels = pygame.mixer.get_init()
    samples = array("h")
    for i in range(int(rate * duration)):
        value = int(0.3 * 32767 * math.sin(2 * math.pi * frequency * i / rate))
        samples.extend([value] * channels)
    return pygame.mixer.Sound(buffer=samples)


pygame.mixer.pre_init(44100, -16, 2, 512)
pygame.init()
screen = pygame.display.set_mode((800, 500))
pygame.display.set_caption("Подойди к радио")
clock = pygame.time.Clock()
radio = (400, 250)
channel = make_hum().play(-1)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    mouse = pygame.mouse.get_pos()
    distance = math.dist(mouse, radio)
    volume = max(0.0, 1 - distance / 500)
    pan = (mouse[0] - radio[0]) / 400          # −1 (лево) ... +1 (право)
    pan = max(-1.0, min(1.0, pan))
    left = volume * min(1.0, 1 - pan)          # курсор слева (pan < 0) — левый канал полностью
    right = volume * min(1.0, 1 + pan)         # курсор справа (pan > 0) — правый канал полностью
    channel.set_volume(left, right)
    screen.fill((15, 15, 30))
    pygame.draw.circle(screen, (200, 120, 40), radio, 30)
    pygame.draw.circle(screen, (90, 90, 120), radio, 500, 1)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

При `pan = -1`: левый канал — полностью, правый — 0; при `pan = 0` (курсор точно над или под радио): оба канала одинаковы. Для простого варианта задания 7 достаточно `channel.set_volume(volume)`.

**★ Б. Ритм-игра.** Творческое задание. Основные элементы: спрайт `Note` (`lane`, `rect`), `HIT_LINE = 520`, при `KEYDOWN` проверка условия `abs(rect.centery - HIT_LINE) < 25` для самой нижней ноты на этой дорожке, для попавшей ноты — `kill()` + воспроизведение тона + очки.

</details>
