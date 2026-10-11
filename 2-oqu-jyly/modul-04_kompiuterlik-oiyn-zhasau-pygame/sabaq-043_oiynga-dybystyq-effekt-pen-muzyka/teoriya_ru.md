# Урок 43. Добавление в игру звуковых эффектов и музыки.

## Темы

1. Виды звуков в игре
2. Запуск звуковой системы: pygame.mixer
3. Звуковые эффекты: Sound
4. Фоновая музыка: pygame.mixer.music
5. Безопасный менеджер звука
6. Звук без файла: программное создание тона
7. Дизайн игровых звуков: советы
8. Полный пример: «Пианино-игра» со звуком
9. Частые ошибки
10. Основные понятия

## 1. Виды звуков в игре

| Вид | Пример | Инструмент Pygame | Длительность |
|---|---|---|---|
| Звуковой эффект (SFX) | Выстрел, прыжок, монета, взрыв | `pygame.mixer.Sound` | Короткий (0,1–2 с) |
| Фоновая музыка | Мелодия меню, мелодия уровня | `pygame.mixer.music` | Длинная, повторяется |
| Звуки интерфейса | Нажатие кнопки, перемещение по меню | `pygame.mixer.Sound` | Очень короткие |

**Разница:** `Sound` загружает файл в память целиком — короткие звуки воспроизводятся сразу, несколько одновременно. `music` читает файл частями (stream) — экономит память для длинной мелодии, но одновременно играет только **одна** музыка.

**Форматы файлов:**

| Формат | Особенность | Рекомендация |
|---|---|---|
| WAV | Несжатый, качественный, большой размер | Короткие эффекты |
| OGG | Сжатый, качественный, открытый формат | Эффекты и музыка — лучший выбор |
| MP3 | Сжатый, широко распространённый | Музыка (pygame 2 поддерживает) |

## 2. Запуск звуковой системы: pygame.mixer

`pygame.init()` запускает и звуковой модуль (`mixer`). Чтобы звук не запаздывал, его можно предварительно настроить:

```python
import pygame

pygame.mixer.pre_init(44100, -16, 2, 512)   # частота, 16 бит, стерео, маленький буфер
pygame.init()
print(pygame.mixer.get_init())
```

Результат:
```text
(44100, -16, 2)
```

Чем меньше буфер (512), тем быстрее звук реагирует на событие.

> Если на компьютере нет звукового устройства (на некоторых компьютерах в классе), `pygame.mixer.init()` выдаёт ошибку `pygame.error`. Обработаем это в разделе 5.

## 3. Звуковые эффекты: Sound

```python
shoot_sound = pygame.mixer.Sound("assets/sounds/shoot.wav")   # 1. загрузка (до цикла!)
shoot_sound.set_volume(0.5)                                   # 2. громкость 0.0 ... 1.0
shoot_sound.play()                                            # 3. воспроизведение (при событии)
```

| Метод | Что делает |
|---|---|
| `sound.play()` | Воспроизвести один раз |
| `sound.play(loops=2)` | Воспроизвести 1 + 2 = 3 раза |
| `sound.play(loops=-1)` | Бесконечный повтор (например, гул двигателя) |
| `sound.play(fade_ms=500)` | Плавное нарастание громкости |
| `sound.stop()` | Остановить |
| `sound.fadeout(1000)` | Плавно заглушить за 1 секунду |
| `sound.set_volume(v)` | Громкость, 0.0–1.0 |
| `sound.get_length()` | Длительность (в секундах) |

**Каналы (channels).** Pygame воспроизводит несколько звуков одновременно в разных «каналах» (по умолчанию 8). Если выстрелов много, каналы переполняются, и некоторые звуки не воспроизводятся. Количество каналов можно увеличить:

```python
pygame.mixer.set_num_channels(16)
```

## 4. Фоновая музыка: pygame.mixer.music

```python
pygame.mixer.music.load("assets/sounds/theme.ogg")
pygame.mixer.music.set_volume(0.3)       # музыку лучше делать тише эффектов
pygame.mixer.music.play(-1)              # -1 — бесконечный повтор
```

| Метод | Что делает |
|---|---|
| `music.play(loops=-1, fade_ms=2000)` | Воспроизведение (с плавным началом) |
| `music.pause()` / `music.unpause()` | Пауза / продолжение (при паузе игры) |
| `music.stop()` | Остановить |
| `music.fadeout(1500)` | Плавно заглушить (при Game Over) |
| `music.set_volume(v)` | Громкость |
| `music.get_busy()` | Играет ли музыка |
| `music.queue("next.ogg")` | Поставить в очередь следующую мелодию после текущей |

**Смена мелодии меню и игры:** если сделать `load()` + `play()` нового файла, предыдущий автоматически остановится.

## 5. Безопасный менеджер звука

Звукового файла может не оказаться, или звуковая карта на компьютере может не работать. Игра и в этом случае должна **работать** — просто без звука. Поэтому собираем всю работу со звуком в один класс:

```python
import os
import pygame

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
SOUND_DIR = os.path.join(BASE_DIR, "assets", "sounds")


class SoundManager:
    """Загружает и воспроизводит звуки; при ошибке работает беззвучно."""

    def __init__(self):
        self.enabled = True
        self.sounds = {}
        try:
            if not pygame.mixer.get_init():
                pygame.mixer.init()
        except pygame.error:
            print("Звуковое устройство не найдено — игра продолжится без звука")
            self.enabled = False

    def load(self, name, filename, volume=0.6):
        if not self.enabled:
            return
        try:
            sound = pygame.mixer.Sound(os.path.join(SOUND_DIR, filename))
            sound.set_volume(volume)
            self.sounds[name] = sound
        except (pygame.error, FileNotFoundError):
            print("Звук не найден:", filename)

    def play(self, name):
        if self.enabled and name in self.sounds:
            self.sounds[name].play()

    def play_music(self, filename, volume=0.3):
        if not self.enabled:
            return
        try:
            pygame.mixer.music.load(os.path.join(SOUND_DIR, filename))
            pygame.mixer.music.set_volume(volume)
            pygame.mixer.music.play(-1, fade_ms=1000)
        except (pygame.error, FileNotFoundError):
            print("Музыка не найдена:", filename)

    def toggle_mute(self):
        """Клавиша M: выключить/включить весь звук."""
        if not pygame.mixer.get_init():
            return
        self.enabled = not self.enabled
        if self.enabled:
            pygame.mixer.music.unpause()
        else:
            pygame.mixer.music.pause()
            pygame.mixer.stop()
```

Использование в игре:

```python
sounds = SoundManager()
sounds.load("shoot", "shoot.wav", 0.4)
sounds.load("boom", "explosion.ogg", 0.7)
sounds.load("coin", "coin.wav")
sounds.play_music("theme.ogg")
# ... в игровом цикле:
# при выстреле:          sounds.play("shoot")
# при взрыве метеорита:  sounds.play("boom")
# клавиша M:             sounds.toggle_mute()
```

## 6. Звук без файла: программное создание тона

Звук — это колебание воздуха, а цифровой звук — последовательность чисел (отсчётов, samples). 44100 отсчётов в секунду; каждый отсчёт — число в диапазоне −32768...32767. Синусоида — чистый тон. Pygame может создать звук из таких чисел через `Sound(buffer=...)`:

```python
import math
from array import array
import pygame


def make_tone(frequency=440, duration=0.2, volume=0.5):
    """Возвращает тон заданной частоты (Гц) в виде Sound."""
    rate, size, channels = pygame.mixer.get_init()
    n = int(rate * duration)
    samples = array("h")
    for i in range(n):
        value = int(volume * 32767 * math.sin(2 * math.pi * frequency * i / rate))
        for _ in range(channels):          # если стерео — по одному значению на канал
            samples.append(value)
    return pygame.mixer.Sound(buffer=samples)


pygame.mixer.pre_init(44100, -16, 2, 512)
pygame.init()
try:
    beep = make_tone(880, 0.15)
    print("Длительность:", round(beep.get_length(), 2), "с")
    beep.play()
    pygame.time.wait(300)
except pygame.error:
    print("Нет звукового устройства")
pygame.quit()
```

Результат:
```text
Длительность: 0.15 с
```

| Нота | Частота (Гц) |
|---|---|
| До (C4) | 262 |
| Ре (D4) | 294 |
| Ми (E4) | 330 |
| Фа (F4) | 349 |
| Соль (G4) | 392 |
| Ля (A4) | 440 |
| Си (B4) | 494 |

Этот приём очень удобен для прототипа: даже если звуковые файлы ещё не готовы, в игру можно добавить «дзынь» и «бип».

## 7. Дизайн игровых звуков: советы

1. **Каждому важному действию — звук:** выстрел, попадание, сбор, потеря жизни, переход на уровень, Game Over.
2. **Музыка тише:** музыка 0.2–0.4, эффекты 0.5–0.8.
3. **Повторяющийся звук не должен надоедать:** сделайте для одного действия 2–3 варианта и выбирайте через `random.choice`.
4. **Возможность «выключить звук» обязательна** (клавиша M или кнопка в меню).
5. **Во время паузы** — `music.pause()`; при Game Over — `music.fadeout()`.
6. **Лицензия:** укажите в README источник и автора каждого звука.

## 8. Полный пример: «Пианино-игра» со звуком

Работает без файлов: клавиши `A S D F G H J` воспроизводят ноты, нажатая клавиша подсвечивается на экране.

```python
import math
from array import array
import pygame

WIDTH, HEIGHT = 700, 300
NOTES = [("A", pygame.K_a, 262), ("S", pygame.K_s, 294), ("D", pygame.K_d, 330),
         ("F", pygame.K_f, 349), ("G", pygame.K_g, 392), ("H", pygame.K_h, 440),
         ("J", pygame.K_j, 494)]


def make_tone(frequency, duration=0.4, volume=0.4):
    rate, size, channels = pygame.mixer.get_init()
    n = int(rate * duration)
    samples = array("h")
    for i in range(n):
        fade = 1 - i / n                       # к концу постепенно затухает
        value = int(volume * fade * 32767 * math.sin(2 * math.pi * frequency * i / rate))
        for _ in range(channels):
            samples.append(value)
    return pygame.mixer.Sound(buffer=samples)


pygame.mixer.pre_init(44100, -16, 2, 512)
pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Пианино: A S D F G H J")
font = pygame.font.SysFont("arial", 32)
clock = pygame.time.Clock()
try:
    tones = {key: make_tone(freq) for _, key, freq in NOTES}
except pygame.error:
    tones = {}
    print("Нет звукового устройства — работает только изображение")
key_w = WIDTH // len(NOTES)
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key in tones:
            tones[event.key].play()
    pressed = pygame.key.get_pressed()
    screen.fill((30, 30, 30))
    for i, (label, key, _) in enumerate(NOTES):
        rect = pygame.Rect(i * key_w + 4, 40, key_w - 8, 220)
        color = (255, 220, 0) if pressed[key] else (245, 245, 245)
        pygame.draw.rect(screen, color, rect, border_radius=8)
        text = font.render(label, True, (40, 40, 40))
        screen.blit(text, text.get_rect(midbottom=(rect.centerx, rect.bottom - 10)))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

## Частые ошибки

**1. Загрузка звука в цикле**
```python
import pygame
pygame.init()
for frame in range(3):
    s = pygame.mixer.Sound(buffer=bytes(1000))   # новый Sound на каждом кадре — медленно и забивает память
```
Правильно: все звуки — до цикла, один раз.

**2. Воспроизведение длинной музыки через `Sound`** — 5-минутная мелодия полностью загружается в память (десятки МБ). Музыка — `pygame.mixer.music`.

**3. Воспроизведение звука выстрела через `get_pressed()`** — пока клавиша нажата, звук перезапускается на каждом кадре и превращается в «трещотку». Звук — один раз в момент события.

**4. Формат файла не поддерживается** — некоторые WAV-файлы (24/32 бит) выдают ошибку: `pygame.error: Unable to open file`. Конвертируйте файл в OGG или 16-битный WAV (например, программой Audacity).

**5. Игра падает на компьютере без звукового устройства** — используйте `try...except pygame.error` и `SoundManager`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `pygame.mixer` | Модуль для работы со звуком |
| `Sound` | Короткий звук (эффект), полностью загружаемый в память |
| `mixer.music` | Фоновая музыка, воспроизводимая потоком (stream) |
| Канал (channel) | «Дорожка», на которой играет один звук; одновременно несколько каналов |
| Громкость (volume) | 0.0 (тишина) ... 1.0 (полная) |
| Fade in / fade out | Плавное нарастание / затухание |
| Отсчёт (sample) | Одно измерение цифрового звука |
| Частота (Гц) | Число колебаний в секунду; высота ноты |
| SFX | Звуковые эффекты (sound effects) |

## Примечание

- Самые надёжные форматы — OGG и WAV. Поддержка MP3 зависит от версии и сборки Pygame, поэтому игровые ресурсы лучше конвертировать в OGG.
- Чтобы уменьшить задержку звука, до `pygame.init()` можно вызвать `pygame.mixer.pre_init(44100, -16, 2, 512)` (последнее число — размер буфера).
- По умолчанию есть 8 каналов: одновременно звучит не больше 8 `Sound`. Количество каналов меняется через `pygame.mixer.set_num_channels(n)`.
- `pygame.mixer.music` воспроизводит потоком только одну мелодию за раз; `pygame.mixer.get_init()` возвращает `None`, если микшер не запущен.
- Документация `pygame.mixer`: https://www.pygame.org/docs/ref/mixer.html
