# Сабақ 43. Ойынға дыбыстық эффект пен музыка қосу.

## Тақырыптар

1. Ойындағы дыбыс түрлері
2. Дыбыс жүйесін іске қосу: pygame.mixer
3. Дыбыстық эффектілер: Sound
4. Фондық музыка: pygame.mixer.music
5. Қауіпсіз дыбыс менеджері
6. Файлсыз дыбыс: тонды бағдарламамен жасау
7. Ойын дыбыстарының дизайны: кеңестер
8. Толық мысал: дыбысы бар «Пианино-ойын»
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Ойындағы дыбыс түрлері

| Түрі | Мысал | Pygame құралы | Ұзақтығы |
|---|---|---|---|
| Дыбыстық эффект (SFX) | Ату, секіру, тиын, жарылыс | `pygame.mixer.Sound` | Қысқа (0,1–2 с) |
| Фондық музыка | Мәзір әуені, деңгей әуені | `pygame.mixer.music` | Ұзақ, қайталанады |
| Интерфейс дыбыстары | Батырманы басу, мәзірде жылжу | `pygame.mixer.Sound` | Өте қысқа |

**Айырмашылығы:** `Sound` файлды толығымен жадқа жүктейді — қысқа дыбыстар бірден, бір мезгілде бірнешеуі ойнайды. `music` файлды бөліктермен оқиды (stream) — ұзақ әуенге жадты үнемдейді, бірақ бір уақытта тек **бір** музыка ойнайды.

**Файл форматтары:**

| Формат | Ерекшелігі | Ұсыныс |
|---|---|---|
| WAV | Сығылмаған, сапалы, көлемі үлкен | Қысқа эффектілер |
| OGG | Сығылған, сапалы, ашық формат | Эффектілер мен музыка — ең жақсы таңдау |
| MP3 | Сығылған, кең таралған | Музыка (pygame 2 қолдайды) |

## 2. Дыбыс жүйесін іске қосу: pygame.mixer

`pygame.init()` дыбыс модулін (`mixer`) де іске қосады. Дыбыс кешігіп шықпауы үшін оны алдын ала баптауға болады:

```python
import pygame

pygame.mixer.pre_init(44100, -16, 2, 512)   # жиілік, 16 бит, стерео, кішкене буфер
pygame.init()
print(pygame.mixer.get_init())
```

Нәтижесі:
```text
(44100, -16, 2)
```

Буфер (512) кішірек болса, дыбыс оқиғаға тезірек жауап береді.

> Компьютерде дыбыс құрылғысы болмаса (кейбір сыныптағы компьютерлерде), `pygame.mixer.init()` `pygame.error` қатесін береді. Мұны 5-бөлімде өңдейміз.

## 3. Дыбыстық эффектілер: Sound

```python
shoot_sound = pygame.mixer.Sound("assets/sounds/shoot.wav")   # 1. жүктеу (циклге дейін!)
shoot_sound.set_volume(0.5)                                   # 2. қаттылық 0.0 ... 1.0
shoot_sound.play()                                            # 3. ойнату (оқиға болғанда)
```

| Әдіс | Не істейді |
|---|---|
| `sound.play()` | Бір рет ойнату |
| `sound.play(loops=2)` | 1 + 2 = 3 рет ойнату |
| `sound.play(loops=-1)` | Шексіз қайталау (мысалы, қозғалтқыш гуілі) |
| `sound.play(fade_ms=500)` | Біртіндеп қаттылау |
| `sound.stop()` | Тоқтату |
| `sound.fadeout(1000)` | 1 секунд ішінде біртіндеп сөндіру |
| `sound.set_volume(v)` | Қаттылық, 0.0–1.0 |
| `sound.get_length()` | Ұзақтығы (секунд) |

**Арналар (channels).** Pygame бір уақытта бірнеше дыбысты әр түрлі «арналарда» ойнатады (әдепкі бойынша 8). Атыс көп болса, арналар толып, кейбір дыбыстар шықпай қалады. Арна санын көбейтуге болады:

```python
pygame.mixer.set_num_channels(16)
```

## 4. Фондық музыка: pygame.mixer.music

```python
pygame.mixer.music.load("assets/sounds/theme.ogg")
pygame.mixer.music.set_volume(0.3)       # музыка эффектілерден тынышырақ болғаны жөн
pygame.mixer.music.play(-1)              # -1 — шексіз қайталау
```

| Әдіс | Не істейді |
|---|---|
| `music.play(loops=-1, fade_ms=2000)` | Ойнату (біртіндеп басталады) |
| `music.pause()` / `music.unpause()` | Кідірту / жалғастыру (ойын паузасында) |
| `music.stop()` | Тоқтату |
| `music.fadeout(1500)` | Біртіндеп сөндіру (Game Over кезінде) |
| `music.set_volume(v)` | Қаттылық |
| `music.get_busy()` | Музыка ойнап тұр ма |
| `music.queue("next.ogg")` | Ағымдағысынан кейін келесі әуенді кезекке қою |

**Мәзір мен ойын әуенін ауыстыру:** жаңа файлды `load()` + `play()` жасасақ, алдыңғысы автоматты түрде тоқтайды.

## 5. Қауіпсіз дыбыс менеджері

Дыбыс файлы жоқ болуы немесе компьютерде дыбыс картасы істемеуі мүмкін. Ойын бұл жағдайда да **жұмыс істеуі** керек — тек дыбыссыз. Сондықтан барлық дыбыс жұмысын бір класқа жинаймыз:

```python
import os
import pygame

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
SOUND_DIR = os.path.join(BASE_DIR, "assets", "sounds")


class SoundManager:
    """Дыбыстарды жүктейді және ойнатады; қате болса, үнсіз жұмыс істейді."""

    def __init__(self):
        self.enabled = True
        self.sounds = {}
        try:
            if not pygame.mixer.get_init():
                pygame.mixer.init()
        except pygame.error:
            print("Дыбыс құрылғысы табылмады — ойын дыбыссыз жалғасады")
            self.enabled = False

    def load(self, name, filename, volume=0.6):
        if not self.enabled:
            return
        try:
            sound = pygame.mixer.Sound(os.path.join(SOUND_DIR, filename))
            sound.set_volume(volume)
            self.sounds[name] = sound
        except (pygame.error, FileNotFoundError):
            print("Дыбыс табылмады:", filename)

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
            print("Музыка табылмады:", filename)

    def toggle_mute(self):
        """M пернесі: барлық дыбысты өшіру/қосу."""
        if not pygame.mixer.get_init():
            return
        self.enabled = not self.enabled
        if self.enabled:
            pygame.mixer.music.unpause()
        else:
            pygame.mixer.music.pause()
            pygame.mixer.stop()
```

Ойында қолдану:

```python
sounds = SoundManager()
sounds.load("shoot", "shoot.wav", 0.4)
sounds.load("boom", "explosion.ogg", 0.7)
sounds.load("coin", "coin.wav")
sounds.play_music("theme.ogg")
# ... ойын циклінде:
# оқ атылғанда:      sounds.play("shoot")
# метеорит жарылса:  sounds.play("boom")
# M пернесі:         sounds.toggle_mute()
```

## 6. Файлсыз дыбыс: тонды бағдарламамен жасау

Дыбыс — ауаның тербелісі, ал цифрлық дыбыс — сандар тізбегі (үлгілер, samples). Секундына 44100 үлгі; әр үлгі −32768...32767 аралығындағы сан. Синусоида — таза тон. Pygame `Sound(buffer=...)` арқылы осындай сандардан дыбыс жасай алады:

```python
import math
from array import array
import pygame


def make_tone(frequency=440, duration=0.2, volume=0.5):
    """Берілген жиіліктегі (Гц) тонды Sound ретінде қайтарады."""
    rate, size, channels = pygame.mixer.get_init()
    n = int(rate * duration)
    samples = array("h")
    for i in range(n):
        value = int(volume * 32767 * math.sin(2 * math.pi * frequency * i / rate))
        for _ in range(channels):          # стерео болса — әр арнаға бір мән
            samples.append(value)
    return pygame.mixer.Sound(buffer=samples)


pygame.mixer.pre_init(44100, -16, 2, 512)
pygame.init()
try:
    beep = make_tone(880, 0.15)
    print("Ұзақтығы:", round(beep.get_length(), 2), "с")
    beep.play()
    pygame.time.wait(300)
except pygame.error:
    print("Дыбыс құрылғысы жоқ")
pygame.quit()
```

Нәтижесі:
```text
Ұзақтығы: 0.15 с
```

| Нота | Жиілік (Гц) |
|---|---|
| До (C4) | 262 |
| Ре (D4) | 294 |
| Ми (E4) | 330 |
| Фа (F4) | 349 |
| Соль (G4) | 392 |
| Ля (A4) | 440 |
| Си (B4) | 494 |

Бұл тәсіл прототипке өте ыңғайлы: дыбыс файлдары әлі дайын болмаса да, ойынға «дың» мен «бип» қосуға болады.

## 7. Ойын дыбыстарының дизайны: кеңестер

1. **Әр маңызды әрекетке — дыбыс:** ату, тию, жинау, өмір жоғалту, деңгей өту, Game Over.
2. **Музыка тынышырақ:** музыка 0.2–0.4, эффектілер 0.5–0.8.
3. **Қайталанатын дыбыс жалықтырмасын:** бір әрекетке 2–3 нұсқа жасап, `random.choice` арқылы таңдаңыз.
4. **«Дыбысты өшіру» мүмкіндігі міндетті** (M пернесі немесе мәзірдегі батырма).
5. **Пауза кезінде** — `music.pause()`; Game Over кезінде — `music.fadeout()`.
6. **Лицензия:** README-де әр дыбыстың көзі мен авторын жазыңыз.

## 8. Толық мысал: дыбысы бар «Пианино-ойын»

Файлсыз жұмыс істейді: `A S D F G H J` пернелері ноталарды ойнатады, басылған перне экранда жарқырайды.

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
        fade = 1 - i / n                       # соңына қарай біртіндеп сөнеді
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
    print("Дыбыс құрылғысы жоқ — тек көрініс жұмыс істейді")
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

## Жиі кездесетін қателер

**1. Дыбысты циклде жүктеу**
```python
import pygame
pygame.init()
for frame in range(3):
    s = pygame.mixer.Sound(buffer=bytes(1000))   # әр кадрда жаңа Sound — баяу әрі жадты толтырады
```
Дұрысы: барлық дыбыс — циклге дейін, бір рет.

**2. Ұзақ музыканы `Sound` арқылы ойнату** — 5 минуттық әуен жадқа толық жүктеледі (ондаған МБ). Музыка — `pygame.mixer.music`.

**3. `get_pressed()` арқылы ату дыбысын ойнату** — перне басылып тұрғанда дыбыс әр кадрда қайта басталып, «шырылдаққа» айналады. Дыбыс — оқиға болған сәтте бір рет.

**4. Файл форматы қолдау таппайды** — кейбір WAV файлдары (24/32 бит) қате береді: `pygame.error: Unable to open file`. Файлды OGG немесе 16 биттік WAV-қа айналдырыңыз (мысалы, Audacity бағдарламасымен).

**5. Дыбыс құрылғысы жоқ компьютерде ойын құлайды** — `try...except pygame.error` және `SoundManager` қолданыңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| `pygame.mixer` | Дыбыспен жұмыс істейтін модуль |
| `Sound` | Жадқа толық жүктелетін қысқа дыбыс (эффект) |
| `mixer.music` | Ағынмен (stream) ойнатылатын фондық музыка |
| Арна (channel) | Бір дыбыс ойнайтын «жол»; бір мезгілде бірнеше арна |
| Қаттылық (volume) | 0.0 (үнсіз) ... 1.0 (толық) |
| Fade in / fade out | Біртіндеп қаттылау / сөну |
| Үлгі (sample) | Цифрлық дыбыстың бір өлшемі |
| Жиілік (Гц) | Тербеліс саны секундына; нотаның биіктігі |
| SFX | Дыбыстық эффектілер (sound effects) |

## Ескерту

- Ең сенімді форматтар — OGG және WAV. MP3 қолдауы Pygame нұсқасы мен құрастыруына байланысты, сондықтан ойын ресурстарын OGG-ге айналдырған дұрыс.
- Дыбыс кешігуін азайту үшін `pygame.init()`-ке дейін `pygame.mixer.pre_init(44100, -16, 2, 512)` шақыруға болады (соңғы сан — буфер өлшемі).
- Әдепкіде 8 арна бар: бір уақытта 8-ден көп `Sound` ойнатылмайды. Арна санын `pygame.mixer.set_num_channels(n)` арқылы өзгертуге болады.
- `pygame.mixer.music` бір уақытта тек бір әуенді ағынмен ойнатады; `pygame.mixer.get_init()` миксер іске қосылмаса `None` қайтарады.
- `pygame.mixer` құжаттамасы: https://www.pygame.org/docs/ref/mixer.html
