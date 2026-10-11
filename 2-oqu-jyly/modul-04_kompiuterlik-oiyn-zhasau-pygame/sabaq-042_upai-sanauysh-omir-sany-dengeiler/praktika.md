# Сабақ 42. Практикалық тапсырмалар: ұпай, өмір, деңгейлер, мәтін

> Дайындық: Google Fonts-тан қазақ әріптерін қолдайтын қаріпті (мысалы, Roboto немесе Noto Sans) жүктеп, `assets/fonts` папкасына сал. Болмаса `SysFont("arial")` қолдан.

## Базалық деңгей

### 1-тапсырма. «Сәлем, ойыншы!»
800×600 терезенің дәл ортасына үлкен (64) қаріппен `Сәлем, ойыншы!` жазуын шығар, оның астына кішірек (28) қаріппен бүгінгі күнді (`datetime.date.today()`). Қазақ әріптері дұрыс көрінуі керек.

### 2-тапсырма. Басу санауыш
Тінтуірдің әр басуында санауыш 1-ге артады, ол экранның сол жақ жоғарғы бұрышында `Басу саны: 7` түрінде көрсетіледі. `R` пернесі санауышты нөлдейді.

### 3-тапсырма. Секундомер
Экран ортасында `00:12.4` форматында (минут:секунд.ондық) ойын басталғаннан бергі уақыт көрсетілсін. `Space` — кідірту/жалғастыру.
**Кеңес:** `pygame.time.get_ticks()`; формат: `f"{m:02d}:{s:02d}.{d}"`.

### 4-тапсырма. HUD «Ғарыш қорғаушысына»
41-сабақтағы жобаңда тақырыптағы ақпаратты экранға көшір:
- сол жақ жоғарыда — `Ұпай: 120`;
- оң жақ жоғарыда — өмір жүрек белгішелерімен (`make_heart`, теорияның 5-бөлімі);
- ортада жоғарыда — `Деңгей: 2`.

## Кеңейтілген деңгей

### 5-тапсырма. Деңгейлер
«Ғарыш қорғаушысында» әр 100 ұпай сайын деңгей артсын:
- метеориттер жылдамырақ (`randint(1 + level, 3 + level)`);
- жиірек пайда болады (`max(200, 700 - 80 * level)` мс);
- деңгей ауысқанда экран ортасында 2 секунд «3-ДЕҢГЕЙ!» жазуы көрінеді.

### 6-тапсырма. Game Over және рекорд
- Өмір біткенде ойын тоқтайды, экранда «ОЙЫН АЯҚТАЛДЫ», ұпай және рекорд көрсетіледі;
- рекорд `highscore.txt` файлында сақталады;
- жаңа рекорд болса — «ЖАҢА РЕКОРД!» жазуы жыпылықтайды;
- `R` — қайта бастау, `Escape` — шығу.

### 7-тапсырма. Ұпайдың «ұшып шығуы»
Метеорит жойылған жерде `+10` жазуы пайда болып, 40 кадр бойы жоғары көтеріліп, біртіндеп мөлдірленіп жоғалсын (`FloatingText` спрайты, `set_alpha`).

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Үздік 5 нәтиже кестесі.** Ойын соңында ойыншы атын пернетақтадан енгізеді (`TEXTINPUT` оқиғасы немесе `event.unicode`), нәтиже `leaderboard.txt` файлына `Аты;ұпай` түрінде жазылады. Экранда ұпай бойынша сұрыпталған үздік 5 кесте ретінде көрсетіледі.

**Ә. Көп жолды мәтін.** `draw_multiline(surface, text, font, color, x, y, max_width)` функциясын жаз: ұзын мәтінді сөздер бойынша бөліп, `max_width`-тен аспайтын жолдарға орап шығарады (ойын ережелері экраны үшін).

## Үй тапсырмасы

1. «Ғарыш қорғаушысын» HUD, деңгейлер және рекордпен аяқтап, GitHub-қа жүкте (`highscore.txt`-ті `.gitignore`-ға қос).
2. Командалық жобаң үшін HUD эскизін қағазға сал: не көрсетіледі, қай жерде.
3. Келесі сабаққа: freesound.org, kenney.nl/assets (Audio) сайттарынан ойыныңа 2–3 тегін дыбыс (WAV/OGG) тауып кел.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–3-тапсырмалар (бір бағдарламада):**

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
pygame.display.set_caption("Мәтін жаттығулары")
big = pygame.font.SysFont("arial, dejavusans", 64)
small = pygame.font.SysFont("arial, dejavusans", 28)
clock = pygame.time.Clock()
clicks = 0
elapsed = 0          # мс, кідірткенге дейін жиналған уақыт
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
    draw_text(screen, "Сәлем, ойыншы!", big, WHITE, center=(WIDTH // 2, HEIGHT // 2 - 40))
    draw_text(screen, str(datetime.date.today()), small, WHITE,
              center=(WIDTH // 2, HEIGHT // 2 + 20))
    draw_text(screen, f"Басу саны: {clicks}", small, WHITE, topleft=(10, 10))
    draw_text(screen, f"{minutes:02d}:{seconds:02d}.{ms // 100}", big, (255, 220, 0),
              center=(WIDTH // 2, HEIGHT - 100))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**4–6-тапсырмалар.** Толық құрылым теориядағы «Тиын жаңбыры» мысалына сәйкес (8-бөлім): `draw_text`, деңгей формуласы, `set_timer` жаңарту, `load_high_score` / `save_high_score`, `R` арқылы қайта бастау. Жүректерді шығару:

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
    """Жүректерді оң жақ шетке тураланған қатармен шығарады."""
    step = heart.get_width() + 5
    for i in range(lives):
        surface.blit(heart, (right - (i + 1) * step, top))
```

«ЖАҢА РЕКОРД!» жыпылықтауы: `if new_record and pygame.time.get_ticks() // 400 % 2 == 0: draw_text(...)`.

**7-тапсырма. Ұшып шығатын ұпай:**

```python
import pygame


class FloatingText(pygame.sprite.Sprite):
    """Жоғары көтеріліп, біртіндеп жоғалатын жазу."""

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

«Ғарыш қорғаушысында»: `for meteor in hits: texts.add(FloatingText("+10", font, YELLOW, meteor.rect.center))`.

**★ А. Үздік 5 кестесі (логика және енгізу):**

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
pygame.display.set_caption("Атыңды жаз да, Enter бас")
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
        screen.blit(font.render(f"Ұпайың: {score}", True, (255, 255, 255)), (40, 40))
        screen.blit(font.render(f"Атың: {name}_", True, (255, 220, 0)), (40, 100))
    else:
        screen.blit(font.render("ҮЗДІК 5", True, (255, 220, 0)), (40, 30))
        for i, (n, p) in enumerate(load_leaderboard(), start=1):
            screen.blit(font.render(f"{i}. {n} — {p}", True, (255, 255, 255)), (40, 40 + i * 50))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

**★ Ә. Көп жолды мәтін:**

```python
import pygame


def draw_multiline(surface, text, font, color, x, y, max_width):
    """Мәтінді сөздер бойынша жолдарға орап шығарады; соңғы y-ті қайтарады."""
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

`font.size(text)` — мәтін render етілгенде қандай `(ені, биіктігі)` болатынын салмай-ақ есептейді.

</details>
