# Сабақ 43. Практикалық тапсырмалар: дыбыстық эффектілер мен музыка

> Дайындық: `assets/sounds` папкасына 3–4 тегін дыбыс сал (мысалы, kenney.nl → «Digital Audio», «Interface Sounds»; opengameart.org). Файлдар болмаса — теориядағы `make_tone()` функциясымен тон жаса. Жұмыс тек құлаққаппен!

## Базалық деңгей

### 1-тапсырма. Дыбыс батырмалары
Терезеде үш түрлі-түсті тіктөртбұрыш-батырма. Әрқайсысына тінтуірмен басқанда әр түрлі дыбыс ойнайды (39-сабақтағы `collidepoint`). Дыбыс ойнап тұрған кезде батырма жарқырасын.

### 2-тапсырма. Фондық музыка
Бағдарлама іске қосылғанда музыка біртіндеп (`fade_ms=2000`) басталып, шексіз қайталансын. Пернелер:
- `P` — кідірту/жалғастыру;
- `↑` / `↓` — қаттылықты 0.1-ге арттыру/азайту (0.0–1.0 шегінде), қазіргі мән экранда көрсетіледі;
- `S` — 1,5 секундта біртіндеп сөндіру.

### 3-тапсырма. SoundManager
Теориядағы `SoundManager` класын өз жобаңа көшір. Әдейі жоқ файлды жүктеп көр — ойын құламай, консольге ескерту шығуы керек.

### 4-тапсырма. «Ғарыш қорғаушысына» дыбыстар
Жобаңа қос: ату дыбысы, метеорит жарылысы, кемеге тию, деңгей өту, Game Over (музыка `fadeout`). `M` — барлық дыбысты өшіру/қосу; күйі HUD-та белгішемен немесе мәтінмен көрсетіледі.

## Кеңейтілген деңгей

### 5-тапсырма. Әртүрлі жарылыстар
Жарылыс дыбысының 3 нұсқасын (немесе әр түрлі жиіліктегі 3 тон) жүктеп, әр жарылыста кездейсоқ біреуін ойнат. Үлкен метеорит жарылғанда дыбыс қаттырақ болсын.

### 6-тапсырма. Ноталық әуен
`make_tone()` арқылы «Көк тиін» немесе өзің таңдаған қарапайым әуенді ойнатқыш жаз: ноталар мен ұзақтықтар тізімде (`[(262, 0.3), (294, 0.3), ...]`), `Space` басқанда әуен басынан ойнайды. Ноталар бірінен соң бірі уақыт бойынша (`get_ticks`) ойнатылсын — `time.sleep()` қолданба (терезе қатып қалады).

### 7-тапсырма. Дыбыс қаттылығы — қашықтыққа тәуелді
Экранда тұрақты «радио» (дөңгелек) бар, одан шексіз дыбыс шығады (`loops=-1`). Тінтуір курсоры («тыңдаушы») радиоға жақындаған сайын дыбыс қатая, алыстаған сайын бәсеңдей берсін.
**Кеңес:** `volume = max(0, 1 - distance / 500)`; `channel = sound.play(-1)`, `channel.set_volume(volume)`.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Стерео-панорама.** 7-тапсырманы дамыт: курсор радионың сол жағында болса, дыбыс көбірек сол құлаққа, оң жағында — оң құлаққа естілсін. `channel.set_volume(left, right)` — екі аргументті нұсқасы.

**Ә. «Ырғақ ойыны».** Экранның жоғарғы жағынан ноталар (тіктөртбұрыштар) төрт жолақпен түседі. Нота төменгі сызыққа жеткен сәтте сәйкес пернені (`D F J K`) басу керек: дәл тисе — нотаның тоны ойнап, +ұпай; қате — «шуыл» дыбысы.

## Үй тапсырмасы

1. «Ғарыш қорғаушысын» дыбыстармен аяқтап, GitHub-қа жүкте. README-ге «Ресурстар» бөлімін қос: әр дыбыстың атауы, көзі, лицензиясы.
2. Командалық жобаң үшін «дыбыс картасын» жаса: оқиға → дыбыс файлы → қаттылық.
3. Ойлан: ойынның мәзірінде қандай батырмалар болуы керек? Пауза кезінде не болады? (Келесі сабақтың тақырыбы.)

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

Барлық шешімдер `make_tone()` функциясын қолданады, сондықтан дыбыс файлдарынсыз да іске қосылады. Дыбыс файлдары бар болса, `make_tone(...)` орнына `pygame.mixer.Sound("assets/sounds/...")` жазылады.

**1-тапсырма:**

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
pygame.display.set_caption("Дыбыс батырмалары")
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

`sound.play()` ойнап тұрған `Channel` объектісін қайтарады; `channel.get_busy()` — әлі ойнап тұр ма.

**2-тапсырма. Музыка басқару** (`theme.ogg` файлы қажет; файл болмаса, ескерту шығады):

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((500, 200))
pygame.display.set_caption("P — пауза, ↑↓ — қаттылық, S — сөндіру")
font = pygame.font.SysFont("arial", 30)
clock = pygame.time.Clock()
volume = 0.5
paused = False
try:
    pygame.mixer.music.load("assets/sounds/theme.ogg")
    pygame.mixer.music.set_volume(volume)
    pygame.mixer.music.play(-1, fade_ms=2000)
except (pygame.error, FileNotFoundError):
    print("Музыка файлы табылмады")
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
    status = "Пауза" if paused else "Ойнап тұр"
    screen.blit(font.render(f"Қаттылық: {volume:.1f}  |  {status}", True, (255, 255, 255)), (30, 80))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**3–4-тапсырмалар.** `SoundManager` — теорияның 5-бөлімі. «Ғарыш қорғаушысына» қосу нүктелері:
- `Player.shoot()` ішінде → `sounds.play("shoot")`;
- `groupcollide` нәтижесі бос емес болса → `sounds.play("boom")`;
- кемеге тию → `sounds.play("hit")`;
- деңгей өзгерсе → `sounds.play("level")`;
- `lives <= 0` → `pygame.mixer.music.fadeout(1500)`;
- `KEYDOWN` + `K_m` → `sounds.toggle_mute()`; HUD: `"Дыбыс: ҚОСУЛЫ" if sounds.enabled else "Дыбыс: ӨШІРУЛІ"`.

**5-тапсырма:**

```python
import random

# boom_sounds = [make_tone(110, 0.3), make_tone(90, 0.35), make_tone(70, 0.4)]


def play_explosion(boom_sounds, big=False):
    sound = random.choice(boom_sounds)
    sound.set_volume(1.0 if big else 0.5)
    sound.play()
```

**6-тапсырма. Ноталық әуен:**

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
pygame.display.set_caption("Space — әуенді ойнату")
clock = pygame.time.Clock()
notes = [(make_tone(f, d), int(d * 1000)) for f, d in MELODY]
index = -1             # -1 — ойнап тұрған жоқ
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

**7-тапсырма және ★ А (стерео):**

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
pygame.display.set_caption("Радиоға жақында")
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
    pan = (mouse[0] - radio[0]) / 400          # −1 (сол) ... +1 (оң)
    pan = max(-1.0, min(1.0, pan))
    left = volume * min(1.0, 1 - pan)          # курсор солда (pan < 0) — сол арна толық
    right = volume * min(1.0, 1 + pan)         # курсор оңда (pan > 0) — оң арна толық
    channel.set_volume(left, right)
    screen.fill((15, 15, 30))
    pygame.draw.circle(screen, (200, 120, 40), radio, 30)
    pygame.draw.circle(screen, (90, 90, 120), radio, 500, 1)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`pan = -1` болса: сол арна — толық, оң арна — 0; `pan = 0` (курсор радионың тура үстінде немесе астында): екі арна да бірдей. 7-тапсырманың жай нұсқасы үшін `channel.set_volume(volume)` жеткілікті.

**★ Ә. Ырғақ ойыны.** Шығармашылық тапсырма. Негізгі элементтер: `Note` спрайты (`lane`, `rect`), `HIT_LINE = 520`, `KEYDOWN` кезінде сол жолақтағы ең төменгі нотаның `abs(rect.centery - HIT_LINE) < 25` шартын тексеру, тиген нотаны `kill()` + тон ойнату + ұпай.

</details>
