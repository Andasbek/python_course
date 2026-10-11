# Сабақ 44. Практикалық тапсырмалар: мәзір, пауза, ойын соңы

## Базалық деңгей

### 1-тапсырма. Күйлер схемасы (қағазда)
Өз командалық ойыныңның (немесе «Ғарыш қорғаушысының») күйлер схемасын сал: барлық күйлер (тіктөртбұрыштар) және ауысулар (бағыттауыштар, үстінде — перне немесе шарт). Кемінде 4 күй.

### 2-тапсырма. Button класы
Теориядағы `Button` класын жаз. Терезеде үш батырма: «Қызыл», «Жасыл», «Көк» — басқанда фон сол түске боялады. Курсор үстінде тұрғанда батырма ашығырақ.

### 3-тапсырма. Негізгі мәзір
«Ғарыш қорғаушысына» мәзір қос: ойын атауы және «Ойнау», «Ережелер», «Шығу» батырмалары. Мәзір тінтуірмен де, ↑ ↓ + Enter арқылы да басқарылады.

### 4-тапсырма. Пауза
Ойын кезінде `P` немесе `Esc` — пауза: ойын тоқтайды, үстінен жартылай мөлдір қара қабат және «ПАУЗА» жазуы. Тағы `P` — жалғастыру. Паузада музыка да кідірсін (`music.pause()`).

## Кеңейтілген деңгей

### 5-тапсырма. Game Over экраны
Өмір біткенде: «ОЙЫН АЯҚТАЛДЫ», ұпай, рекорд (42-сабақ) және екі батырма: «Қайта ойнау», «Мәзір». `reset_game()` функциясы ойынды толық бастапқы күйге қайтарады (ұпай, өмір, деңгей, барлық спрайт топтары, таймер).

### 6-тапсырма. Кері санақ
«Ойнау» басылғаннан кейін және паузадан шыққанда экран ортасында 3 → 2 → 1 → «КЕТТІК!» кері санағы көрсетілсін (әрқайсысы ≈0,7 секунд). Кері санақ кезінде ойын жаңармайды. Жаңа күй: `COUNTDOWN`.

### 7-тапсырма. Баптаулар экраны
Мәзірге «Баптаулар» батырмасын қос. Баптаулар экранында:
- «Қиындық: Жеңіл / Орташа / Қиын» (басқан сайын ауысады) — метеорит жылдамдығы мен жиілігіне әсер етеді;
- «Дыбыс: ҚОСУЛЫ / ӨШІРУЛІ»;
- «Артқа».

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Сахналар архитектурасы.** Ойынды теорияның 7-бөліміндегідей `Scene` ата-класы және `MenuScene`, `GameScene`, `PauseScene`, `GameOverScene` кластарына қайта құрастыр. Негізгі цикл 10 жолдан аспасын. Ортақ деректер (рекорд, баптаулар) `Game` объектісінде сақталады және сахналарға беріледі.

**Ә. Анимациялы мәзір.** Мәзір фонында жұлдыздар ұшып тұрсын (40-сабақ), ойын атауы ақырын «тербелсін» (y координатасы `sin` бойынша), таңдалған батырма сәл үлкейсін.

## Үй тапсырмасы

1. «Ғарыш қорғаушысын» толық сценариймен (мәзір, ережелер, ойын, пауза, Game Over, қайта ойнау) аяқтап, GitHub-қа жүкте. Бұл жоба — IV модульдің жеке жобасы.
2. README-ге басқару пернелерінің кестесін және күйлер схемасының суретін (фото немесе сызба) қос.
3. Келесі сабаққа: өз кейіпкеріңнің анимациясы үшін 3–4 кадрдан тұратын сурет жиынын (sprite sheet) тауып кел (kenney.nl).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма** — үлгі теорияның 1-бөліміндегі кесте мен схемада.

**2-тапсырма:**

```python
import pygame


class Button:
    def __init__(self, text, center, font, color):
        self.text = text
        self.font = font
        self.color = color
        self.rect = pygame.Rect(0, 0, 180, 60)
        self.rect.center = center

    def is_clicked(self, event):
        return (event.type == pygame.MOUSEBUTTONDOWN and event.button == 1
                and self.rect.collidepoint(event.pos))

    def draw(self, surface):
        hover = self.rect.collidepoint(pygame.mouse.get_pos())
        color = tuple(min(255, c + 60) for c in self.color) if hover else self.color
        pygame.draw.rect(surface, color, self.rect, border_radius=12)
        label = self.font.render(self.text, True, (255, 255, 255))
        surface.blit(label, label.get_rect(center=self.rect.center))


pygame.init()
screen = pygame.display.set_mode((640, 300))
pygame.display.set_caption("Түс батырмалары")
font = pygame.font.SysFont("arial, dejavusans", 28)
buttons = [Button("Қызыл", (120, 150), font, (180, 30, 30)),
           Button("Жасыл", (320, 150), font, (30, 150, 30)),
           Button("Көк", (520, 150), font, (30, 60, 180))]
bg = (30, 30, 30)
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        for b in buttons:
            if b.is_clicked(event):
                bg = tuple(c // 2 for c in b.color)
    screen.fill(bg)
    for b in buttons:
        b.draw(screen)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**3–5-тапсырмалар** — теорияның 6-бөліміндегі толық мысал. 4-тапсырмадағы музыка: `PLAYING → PAUSED` ауысуында `pygame.mixer.music.pause()`, кері ауысуда `unpause()`. 5-тапсырмадағы `reset_game()` ішінде қосымша: `score = 0; lives = 3; level = 1; pygame.time.set_timer(SPAWN, 600)`.

**6-тапсырма. Кері санақ (күй және салу үзіндісі):**

```python
import pygame

WIDTH, HEIGHT = 800, 600
PLAYING, COUNTDOWN = "playing", "countdown"
STEP_MS = 700
LABELS = ["3", "2", "1", "КЕТТІК!"]

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
big_font = pygame.font.SysFont("arial, dejavusans", 96, bold=True)
clock = pygame.time.Clock()
state = COUNTDOWN
countdown_start = pygame.time.get_ticks()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_p and state == PLAYING:
            state = COUNTDOWN                      # мысал: паузадан шыққандағыдай
            countdown_start = pygame.time.get_ticks()
    if state == COUNTDOWN:
        step = (pygame.time.get_ticks() - countdown_start) // STEP_MS
        if step >= len(LABELS):
            state = PLAYING
    # if state == PLAYING: all_sprites.update()   — ойын тек осы күйде жаңарады
    screen.fill((5, 5, 25))
    if state == COUNTDOWN:
        label = big_font.render(LABELS[step], True, (255, 220, 0))
        screen.blit(label, label.get_rect(center=(WIDTH // 2, HEIGHT // 2)))
    pygame.display.set_caption(state)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**7-тапсырма. Баптаулар (деректер және батырма мәтінін жаңарту):**

```python
DIFFICULTIES = [
    {"name": "Жеңіл", "speed": (2, 4), "spawn": 900},
    {"name": "Орташа", "speed": (3, 6), "spawn": 600},
    {"name": "Қиын", "speed": (5, 9), "spawn": 350},
]

settings = {"difficulty": 1, "sound": True}


def difficulty_label():
    return f"Қиындық: {DIFFICULTIES[settings['difficulty']]['name']}"


def sound_label():
    return "Дыбыс: ҚОСУЛЫ" if settings["sound"] else "Дыбыс: ӨШІРУЛІ"


def on_settings_click(button_text):
    """Батырма басылғанда баптауды өзгертеді; 'back' немесе None қайтарады."""
    if button_text.startswith("Қиындық"):
        settings["difficulty"] = (settings["difficulty"] + 1) % len(DIFFICULTIES)
    elif button_text.startswith("Дыбыс"):
        settings["sound"] = not settings["sound"]
    elif button_text == "Артқа":
        return "back"
    return None


on_settings_click(difficulty_label())
on_settings_click(sound_label())
print(difficulty_label(), "|", sound_label())
```

Нәтижесі:
```text
Қиындық: Қиын | Дыбыс: ӨШІРУЛІ
```

Батырма басылғаннан кейін оның мәтінін жаңарту: `buttons[0].text = difficulty_label()`. Метеорит: `random.randint(*DIFFICULTIES[settings["difficulty"]]["speed"])`; таймер: `set_timer(SPAWN, DIFFICULTIES[...]["spawn"])`.

**★ А. Сахналар архитектурасы (қаңқа):**

```python
import pygame

WIDTH, HEIGHT = 800, 600


class Game:
    """Ортақ деректер: экран, қаріптер, рекорд, баптаулар."""

    def __init__(self):
        pygame.init()
        self.screen = pygame.display.set_mode((WIDTH, HEIGHT))
        self.clock = pygame.time.Clock()
        self.font = pygame.font.SysFont("arial, dejavusans", 30)
        self.high_score = 0
        self.scenes = {"menu": MenuScene(self), "game": GameScene(self)}
        self.scene = self.scenes["menu"]

    def switch(self, name):
        self.scene = self.scenes[name]
        self.scene.enter()

    def run(self):
        while True:
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    return
                self.scene.handle_event(event)
            self.scene.update()
            self.scene.draw(self.screen)
            pygame.display.flip()
            self.clock.tick(60)


class Scene:
    def __init__(self, game):
        self.game = game

    def enter(self):
        pass

    def handle_event(self, event):
        pass

    def update(self):
        pass

    def draw(self, surface):
        pass


class MenuScene(Scene):
    def handle_event(self, event):
        if event.type == pygame.KEYDOWN and event.key == pygame.K_RETURN:
            self.game.switch("game")

    def draw(self, surface):
        surface.fill((10, 10, 40))
        text = self.game.font.render("Enter — ойнау", True, (255, 255, 255))
        surface.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT // 2)))


class GameScene(Scene):
    def enter(self):
        self.timer = 0

    def handle_event(self, event):
        if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
            self.game.switch("menu")

    def update(self):
        self.timer += 1

    def draw(self, surface):
        surface.fill((0, 40, 0))
        text = self.game.font.render(f"Ойын: {self.timer // 60} с (Esc — мәзір)", True,
                                     (255, 255, 255))
        surface.blit(text, (20, 20))


if __name__ == "__main__":
    Game().run()
    pygame.quit()
```

Әрі қарай: `PauseScene`, `GameOverScene` қосылады; `GameScene` ішіне «Ғарыш қорғаушысының» спрайттары мен логикасы көшіріледі.

**★ Ә.** Тербелетін атау: `y = 150 + int(10 * math.sin(pygame.time.get_ticks() / 300))`; таңдалған батырманы үлкейту: `rect.inflate(20, 10)` арқылы салу.

</details>
