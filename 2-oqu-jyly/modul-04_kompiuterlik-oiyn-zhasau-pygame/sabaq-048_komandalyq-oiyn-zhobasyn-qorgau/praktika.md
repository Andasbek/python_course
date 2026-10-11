# Сабақ 48. Практикалық тапсырмалар: жобаны қорғау, турнир және Өзіндік жұмыс №3

## Базалық деңгей

### 1-тапсырма. Қорғауға дайындық чек-парағы (сабақ басында, 5 мин)

Әр топ қорғау алдында тексереді:

- [ ] Ойын `main` тармағынан іске қосылады (`git pull` жасалды)
- [ ] `pygame` орнатылған, барлық сурет/дыбыс файлдары орнында
- [ ] Ойын терезесі ашық тұр, мәзір экраны көрінеді
- [ ] README дайын: сипаттама, басқару, іске қосу, команда, лицензиялар
- [ ] Әр мүше таныстырудағы өз бөлігін біледі
- [ ] `git log --oneline --graph` көрсетуге дайын
- [ ] Ең қызықты код фрагменті (10–15 жол) таңдалды

### 2-тапсырма. Жобаны қорғау регламенті

| Кезең | Уақыт | Кім |
|---|---|---|
| Таныстыру (құрылымы теорияда) | 5 мин | Барлық мүше |
| Комиссия мен топтардың сұрақтары | 3 мин | Кемінде 2 мүше жауап береді |
| Бағалау парағын толтыру | 1 мин | Басқа топтар мен педагог |

Кезек жеребе арқылы анықталады. Уақыттан асса, таймер шырылдайды — топ қорытындыға көшеді.

### 3-тапсырма. Өзара бағалау парағы

Әр топ басқа топтардың жобасын бағалайды:

```text
Бағалаушы топ: ____________   Бағаланатын топ: ____________

| Критерий                             | 1 | 2 | 3 | 4 | 5 |
|--------------------------------------|---|---|---|---|---|
| Идеяның қызықтылығы, өзіндігі        |   |   |   |   |   |
| Ойынның ыңғайлылығы мен безендірілуі |   |   |   |   |   |
| Ойын қатесіз жұмыс істеді            |   |   |   |   |   |
| Таныстыру сапасы, сұрақтарға жауап   |   |   |   |   |   |

★ Бірінші жұлдыз (мақтау): ......................................
★ Екінші жұлдыз (мақтау): ......................................
➜ Тілек (ұсыныс): ..............................................
```

### 4-тапсырма. Ойындар турнирі

Турнир кестесі (тақтада немесе ортақ кестеде):

```text
| Оқушы      | «Қыран» | «Алма бағы» | «Ғарыш қорғаушысы» | «Тұлпар» | Жиыны |
|------------|---------|-------------|--------------------|----------|-------|
| Айгерім    |   340   |     —       |        510         |   220    | 1070  |
| ...        |         |             |                    |          |       |
```

Әр ойынды 2 минут ойнап, ұпайыңды жаз. Өз тобыңның ойынын ойнамайсың.

### 5-тапсырма. Жеке рефлексия (сабақ соңында)

1. Жобада менің үлесім: ...
2. Мен үшін ең қиыны: ...
3. Командадан үйренгенім: ...
4. Келесі жобада басқаша жасайтыным: ...

## Кеңейтілген деңгей

### 6-тапсырма. Жобаның 1.0 нұсқасы

Қорғаудан кейін топтар комиссия мен басқа топтардың ұсыныстарын GitHub Issues-ке жазып, үйде кемінде 2 ұсынысты орындап, `v1.0` тегімен релиз жасайды.

### 7-тапсырма. Турнир нәтижелерін өңдейтін бағдарлама

Турнир нәтижелерін сөздік ретінде жазып, әр оқушының жиынтық ұпайын есептеп, рейтинг кестесін (кемуі бойынша) шығаратын бағдарлама жазыңыз. Әр ойынның ұпай шкаласы әртүрлі болғандықтан, ұпайларды **нормаландырыңыз**: оқушының ұпайы / сол ойындағы ең жоғары ұпай × 100.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Ойындардың «трейлері»

Өз ойыныңа 30 секундтық трейлер жаса: ойынға `F12` пернесін басқанда экранның скриншотын `screenshots/shot_001.png`, `shot_002.png` ... деп сақтайтын функция қос (`pygame.image.save(screen, path)`). Скриншоттардан слайд-шоу немесе GIF құрастыр.

---

## Өзіндік жұмыс №3. Pygame: компьютерлік ойын жасау

**Уақыты:** 35 минут. **Бөлімдері:** А — теориялық сұрақтар (жазбаша), Б — практикалық тапсырма (`.py` файл). Файлды `osh3_Тегің.py` деп сақта.

### 1-нұсқа

**А бөлімі. Сұрақтар**

1. Ойын циклінің үш негізгі кезеңін ретімен жаз.
2. `pygame.display.flip()` не үшін қажет? Ол жоқ болса не болады?
3. Мына код не істейді? `clock.tick(30)`
4. `KEYDOWN` оқиғасы мен `pygame.key.get_pressed()` айырмашылығы неде? Әрқайсысына мысал келтір.
5. `pygame.sprite.spritecollide(player, coins, True)` — үшінші аргумент `True` нені білдіреді? Функция не қайтарады?

**Б бөлімі. «Жұлдыз жинаушы» ойыны**

Талаптар:
1. Терезе 600×400, тақырыбы «Жұлдыз жинаушы», фон — қою көк, FPS = 60.
2. Ойыншы — 40×40 жасыл шаршы, `Player(pygame.sprite.Sprite)` класы; төрт бағыттауыш пернемен қозғалады, терезеден шықпайды.
3. Жұлдыз — сары шеңбер (радиусы 12), `Star` спрайты, кездейсоқ орында пайда болады.
4. Ойыншы жұлдызға тигенде ұпай +1, жұлдыз жаңа кездейсоқ орынға ауысады.
5. Экранның сол жақ жоғарғы бұрышында «Ұпай: N» мәтіні.
6. 30 секундтан кейін ойын тоқтап, экран ортасында «Уақыт бітті! Ұпай: N» шығады. Қалған уақыт экранда көрсетіледі.

### 2-нұсқа

**А бөлімі. Сұрақтар**

1. `pygame.init()` және `pygame.quit()` не үшін шақырылады?
2. Pygame координаталар жүйесінде (0, 0) нүктесі қайда? `y` өскенде объект қай жаққа жылжиды?
3. Неге әр кадрдың басында `screen.fill(...)` шақырамыз?
4. `Sprite` класынан мұрагерлік алған объектіде міндетті түрде қандай екі атрибут болуы керек?
5. `rect.collidepoint(event.pos)` не тексереді? Оны қай оқиғамен бірге қолданамыз?

**Б бөлімі. «Шарларды шерт» ойыны**

Талаптар:
1. Терезе 600×400, тақырыбы «Шарларды шерт», фон — ақ, FPS = 60.
2. `Balloon(pygame.sprite.Sprite)` класы: түсі кездейсоқ шеңбер (радиусы 25), кездейсоқ орында пайда болады.
3. Әр 1 секунд сайын жаңа шар пайда болады (`pygame.time.set_timer`).
4. Шар 2 секундтан кейін өзі жоғалады — бұл «жіберіп алу», жіберіп алулар саны +1.
5. Тінтуірмен шарды шерткенде ол жоғалып, ұпай +1.
6. Экранда «Ұпай: N   Жіберілген: M». 5 шар жіберілгенде ойын тоқтап, «Ойын аяқталды! Ұпай: N» шығады.

## Үй тапсырмасы

1. Өзіндік жұмыстың өз нұсқаңды үйде жетілдір: дыбыс және рекордты файлға сақтау қос.
2. Командалық жобаның README-іне қорғаудан кейінгі «Болашақ жоспарлар» бөлімін жаз.
3. Келесі модульге дайындық: 1-жылдағы файлдармен жұмысты (`open`, `read`, `write`, `with`) қайтала.

## Бағалау критерийлері

**Командалық жоба (модульдік жоба), 100 ұпай:**

| Критерий | Үлесі | Ұпай |
|---|---|---|
| Техникалық талаптардың орындалуы (модуль тақырыптарын қамтуы) | 30% | 30 |
| Бағдарламаның тұрақты әрі қатесіз жұмыс істеуі | 25% | 25 |
| Кодтың құрылымы мен сапасы (PEP 8, кластарға бөлу, docstring) | 20% | 20 |
| Пайдаланушыға ыңғайлылығы және безендірілуі | 15% | 15 |
| Жұмысты GitHub-қа дұрыс жүктеуі және README жазуы | 10% | 10 |

Жеке балл = командалық балл × жеке үлес коэффициенті (0,8 / 0,9 / 1,0; ерекше белсенділік үшін +5 ұпай, бірақ 100-ден аспайды).

**Өзіндік жұмыс №3, 100 ұпай:**

| Критерий | Үлесі | Ұпай |
|---|---|---|
| Бағдарламаның дұрыс жұмыс істеуі және тапсырма талабына сай келуі (Б бөлімінің 6 талабы × 5 ұпай + А бөлімі 5 сұрақ × 2 ұпай) | 40% | 40 |
| Алгоритмнің тиімділігі, шешімнің ұтымдылығы (Sprite/Group қолдану, таймер, қаріпті бір рет жасау) | 25% | 25 |
| Кодтың оқылымдылығы: атау беру, түсініктеме, құрылым | 20% | 20 |
| Жұмысты өз бетінше орындауы және мерзімінде тапсыруы | 15% | 15 |

Меңгеру деңгейі: 85–100 — жоғары, 60–84 — орта, 40–59 — бастапқы.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

### Өзіндік жұмыс №3, 1-нұсқа, А бөлімі

1. Оқиғаларды өңдеу (`pygame.event.get()`) → ойын күйін жаңарту (`update`) → экранға салу (`fill`, `draw`, `blit`, `flip`).
2. Салынған кадрды экранға шығарады (буферді ауыстырады). Ол жоқ болса, экран бос/қара күйінде қалады.
3. Цикл секундына 30 реттен жиі орындалмайтындай кідіреді (30 FPS шектеуі).
4. `KEYDOWN` — перне басылған сәтте бір рет болатын оқиға (мысалы, `Space` — секіру, ату). `get_pressed()` — перне басылып тұрғанын әр кадрда тексереді (мысалы, бағыттауышпен үздіксіз қозғалу).
5. `True` — қақтығысқан спрайттар `coins` тобынан (және барлық топтардан) өшіріледі. Функция қақтығысқан спрайттар **тізімін** қайтарады.

### 1-нұсқа, Б бөлімі

```python
import random
import pygame

WIDTH, HEIGHT, FPS = 600, 400, 60
GAME_TIME = 30  # секунд
DARK_BLUE = (10, 20, 70)
GREEN = (40, 200, 80)
YELLOW = (255, 220, 0)
WHITE = (255, 255, 255)


class Player(pygame.sprite.Sprite):
    """Жасыл шаршы: бағыттауыш пернелермен қозғалады."""

    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((40, 40))
        self.image.fill(GREEN)
        self.rect = self.image.get_rect(center=(WIDTH // 2, HEIGHT // 2))
        self.speed = 5

    def update(self):
        keys = pygame.key.get_pressed()
        dx = (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * self.speed
        dy = (keys[pygame.K_DOWN] - keys[pygame.K_UP]) * self.speed
        self.rect.move_ip(dx, dy)
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))


class Star(pygame.sprite.Sprite):
    """Сары жұлдыз-шеңбер: кездейсоқ орында пайда болады."""

    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((24, 24), pygame.SRCALPHA)
        pygame.draw.circle(self.image, YELLOW, (12, 12), 12)
        self.rect = self.image.get_rect()
        self.relocate()

    def relocate(self):
        self.rect.center = (random.randint(20, WIDTH - 20), random.randint(40, HEIGHT - 20))


def main():
    pygame.init()
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Жұлдыз жинаушы")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont("arial", 24)
    big_font = pygame.font.SysFont("arial", 40)

    player = Player()
    star = Star()
    sprites = pygame.sprite.Group(player, star)
    score = 0
    start_ticks = pygame.time.get_ticks()

    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        seconds_left = GAME_TIME - (pygame.time.get_ticks() - start_ticks) // 1000
        time_over = seconds_left <= 0

        if not time_over:
            sprites.update()
            if player.rect.colliderect(star.rect):
                score += 1
                star.relocate()

        screen.fill(DARK_BLUE)
        sprites.draw(screen)
        screen.blit(font.render(f"Ұпай: {score}", True, WHITE), (10, 10))
        screen.blit(font.render(f"Уақыт: {max(seconds_left, 0)}", True, WHITE), (WIDTH - 120, 10))
        if time_over:
            text = big_font.render(f"Уақыт бітті! Ұпай: {score}", True, WHITE)
            screen.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT // 2)))
        pygame.display.flip()
        clock.tick(FPS)

    pygame.quit()


if __name__ == "__main__":
    main()
```

### 2-нұсқа, А бөлімі

1. `pygame.init()` — Pygame-нің барлық модульдерін (дисплей, қаріп, дыбыс) іске қосады; `pygame.quit()` — оларды дұрыс жабады, ресурстарды босатады.
2. (0, 0) — терезенің **сол жақ жоғарғы** бұрышы; `y` өскенде объект **төмен** жылжиды.
3. Алдыңғы кадрдағы суретті өшіру үшін; әйтпесе қозғалған объектілер «із» қалдырады.
4. `self.image` (Surface — сыртқы түрі) және `self.rect` (орны мен өлшемі).
5. Нүкте (тінтуір координатасы) тіктөртбұрыштың ішінде ме екенін тексереді; `MOUSEBUTTONDOWN` оқиғасымен бірге.

### 2-нұсқа, Б бөлімі

```python
import random
import pygame

WIDTH, HEIGHT, FPS = 600, 400, 60
RADIUS = 25
LIFETIME = 2000  # миллисекунд
MAX_MISSES = 5
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)


class Balloon(pygame.sprite.Sprite):
    """Кездейсоқ түсті шар: 2 секундтан кейін өзі жоғалады."""

    def __init__(self):
        super().__init__()
        color = (random.randint(50, 255), random.randint(50, 255), random.randint(50, 255))
        self.image = pygame.Surface((RADIUS * 2, RADIUS * 2), pygame.SRCALPHA)
        pygame.draw.circle(self.image, color, (RADIUS, RADIUS), RADIUS)
        x = random.randint(RADIUS, WIDTH - RADIUS)
        y = random.randint(RADIUS + 40, HEIGHT - RADIUS)
        self.rect = self.image.get_rect(center=(x, y))
        self.born = pygame.time.get_ticks()

    def expired(self):
        return pygame.time.get_ticks() - self.born > LIFETIME


def main():
    pygame.init()
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Шарларды шерт")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont("arial", 24)
    big_font = pygame.font.SysFont("arial", 40)

    spawn_event = pygame.USEREVENT + 1
    pygame.time.set_timer(spawn_event, 1000)

    balloons = pygame.sprite.Group()
    score, misses = 0, 0

    running = True
    while running:
        game_over = misses >= MAX_MISSES
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == spawn_event and not game_over:
                balloons.add(Balloon())
            elif event.type == pygame.MOUSEBUTTONDOWN and not game_over:
                for balloon in balloons:
                    if balloon.rect.collidepoint(event.pos):
                        balloon.kill()
                        score += 1
                        break

        if not game_over:
            for balloon in list(balloons):
                if balloon.expired():
                    balloon.kill()
                    misses += 1

        screen.fill(WHITE)
        balloons.draw(screen)
        screen.blit(font.render(f"Ұпай: {score}   Жіберілген: {misses}", True, BLACK), (10, 10))
        if game_over:
            text = big_font.render(f"Ойын аяқталды! Ұпай: {score}", True, BLACK)
            screen.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT // 2)))
        pygame.display.flip()
        clock.tick(FPS)

    pygame.quit()


if __name__ == "__main__":
    main()
```

Ескерту: `collidepoint` шаршы `rect` бойынша тексереді (шеңбердің бұрыштары да есептеледі) — бұл өзіндік жұмыс үшін жеткілікті. Дәлірек тексеру: центрге дейінгі қашықтық `<= RADIUS`.

### 7-тапсырма. Турнир рейтингі

```python
results = {
    "Айгерім": {"Қыран": 340, "Алма бағы": 120, "Ғарыш": 510},
    "Бауыржан": {"Қыран": 420, "Алма бағы": 90, "Ғарыш": 300},
    "Сымбат": {"Қыран": 150, "Алма бағы": 160, "Ғарыш": 450},
}

# әр ойындағы ең жоғары ұпай
best = {}
for scores in results.values():
    for game, points in scores.items():
        best[game] = max(best.get(game, 0), points)

totals = {}
for name, scores in results.items():
    totals[name] = sum(points / best[game] * 100 for game, points in scores.items())

ranking = sorted(totals.items(), key=lambda item: item[1], reverse=True)
for place, (name, total) in enumerate(ranking, start=1):
    print(f"{place}. {name:<10} {total:.1f}")
```

Нәтижесі:

```text
1. Айгерім    256.0
2. Сымбат     223.9
3. Бауыржан   215.1
```

### 8-тапсырма. Скриншот функциясы (үзінді)

```python
import os
import pygame


def save_screenshot(screen, folder="screenshots"):
    """Экранды screenshots/shot_001.png, shot_002.png ... етіп сақтайды."""
    os.makedirs(folder, exist_ok=True)
    number = len(os.listdir(folder)) + 1
    path = os.path.join(folder, f"shot_{number:03}.png")
    pygame.image.save(screen, path)
    return path

# Ойын циклінде:
# if event.type == pygame.KEYDOWN and event.key == pygame.K_F12:
#     print("Сақталды:", save_screenshot(screen))
```

</details>
