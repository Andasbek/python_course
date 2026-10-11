# Сабақ 44. Ойын мәзірі. Ойынның басталуы, кідірісі және аяқталуы.

## Тақырыптар

1. Ойын күйлері (game states)
2. Күйлер машинасын кодта жүзеге асыру
3. Button класы
4. Мәзір: тінтуір және пернетақта
5. Ойынды қайта бастау: reset()
6. Толық мысал: мәзір, пауза, Game Over
7. Үлкен ойындарға: сахналар (scenes) архитектурасы
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Ойын күйлері (game states)

Ойын әр уақытта **бір** күйде болады. Әр күйде экранда басқа нәрсе көрсетіледі және пернелер басқа әрекет жасайды.

| Күй | Экранда | Пернелер | Келесі күй |
|---|---|---|---|
| `MENU` | Атау, «Ойнау», «Ережелер», «Шығу» батырмалары | ↑ ↓ Enter, тінтуір | `PLAYING`, `RULES`, шығу |
| `RULES` | Ойын ережелері | Esc | `MENU` |
| `PLAYING` | Ойын, HUD | Басқару, P/Esc | `PAUSED`, `GAME_OVER` |
| `PAUSED` | Тоқтаған ойын + «ПАУЗА» | P — жалғастыру, M — мәзір | `PLAYING`, `MENU` |
| `GAME_OVER` | Ұпай, рекорд, «Қайта ойнау», «Мәзір» | R, Enter, тінтуір | `PLAYING`, `MENU` |

**Күйлер схемасы (диаграмма):**

```text
   RULES  ◀── «Ережелер» ──  MENU  ── «Шығу» ──▶ (бағдарлама жабылады)
     │                        ▲  │
     └──────── Esc ───────────┘  │ «Ойнау» / Enter
                                 ▼
          ┌──── P ────▶  PLAYING  ◀──── P ────┐
          │                 │                 │
          │                 │ өмір = 0        │
          ▼                 ▼                 │
       PAUSED           GAME_OVER             │
          │  M → MENU       │  R → PLAYING    │
          └─────────────────┴─────────────────┘
```

Мұндай модель **күйлер машинасы** (finite state machine, FSM) деп аталады. Ол бағдарламалауда өте кең тараған: бағдаршам, банкомат, лифт — бәрі күйлер машинасы.

## 2. Күйлер машинасын кодта жүзеге асыру

Ең қарапайым тәсіл — бір айнымалы және тармақталу:

```python
MENU, PLAYING, PAUSED, GAME_OVER = "menu", "playing", "paused", "game_over"

state = MENU

def handle_key(state, key):
    """Перне басылғанда жаңа күйді қайтарады."""
    if state == MENU and key == "enter":
        return PLAYING
    if state == PLAYING and key == "p":
        return PAUSED
    if state == PAUSED and key == "p":
        return PLAYING
    if state == PAUSED and key == "m":
        return MENU
    if state == GAME_OVER and key == "r":
        return PLAYING
    return state

for key in ["enter", "p", "p", "p", "m"]:
    state = handle_key(state, key)
    print(f"{key:>5} → {state}")
```

Нәтижесі:
```text
enter → playing
    p → paused
    p → playing
    p → paused
    m → menu
```

Ойын циклінде күйге қарай үш кезеңнің (оқиға → жаңарту → салу) әрқайсысы өзгереді:

```python
# Үлгі құрылым (толық код 6-бөлімде)
# while running:
#     for event in pygame.event.get():
#         if state == MENU: ... мәзір оқиғалары
#         elif state == PLAYING: ... ойын оқиғалары
#     if state == PLAYING:
#         all_sprites.update()       # ПАУЗАДА жаңарту ЖОҚ — ойын «қатады»
#     draw_screen(state)
```

**Паузаның құпиясы:** пауза кезінде біз `update()`-ті шақырмаймыз, бірақ экранды салуды жалғастырамыз. Сонда ойын «тоңып» қалады, ал терезе жауап беріп тұрады.

## 3. Button класы

Мәзірде бірнеше батырма болады — оларды класс арқылы жасаймыз (ОБП!):

```python
import pygame


class Button:
    """Мәтіні бар, hover және таңдалу күйі бар батырма."""

    def __init__(self, text, center, font, width=260, height=60):
        self.text = text
        self.font = font
        self.rect = pygame.Rect(0, 0, width, height)
        self.rect.center = center
        self.selected = False            # пернетақтамен таңдалған ба

    def is_hovered(self):
        return self.rect.collidepoint(pygame.mouse.get_pos())

    def is_clicked(self, event):
        return (event.type == pygame.MOUSEBUTTONDOWN and event.button == 1
                and self.rect.collidepoint(event.pos))

    def draw(self, surface):
        active = self.selected or self.is_hovered()
        color = (90, 170, 255) if active else (40, 90, 170)
        pygame.draw.rect(surface, color, self.rect, border_radius=14)
        pygame.draw.rect(surface, (255, 255, 255), self.rect, 2, border_radius=14)
        label = self.font.render(self.text, True, (255, 255, 255))
        surface.blit(label, label.get_rect(center=self.rect.center))
```

## 4. Мәзір: тінтуір және пернетақта

Жақсы мәзір екі тәсілмен де басқарылады. Пернетақта үшін таңдалған батырманың индексін сақтаймыз:

```python
import pygame


class Menu:
    """Батырмалар тізімі: ↑ ↓ таңдау, Enter немесе тінтуір — растау."""

    def __init__(self, buttons):
        self.buttons = buttons
        self.index = 0
        self.buttons[0].selected = True

    def move(self, step):
        self.buttons[self.index].selected = False
        self.index = (self.index + step) % len(self.buttons)
        self.buttons[self.index].selected = True

    def handle_event(self, event):
        """Таңдалған батырманың мәтінін немесе None қайтарады."""
        if event.type == pygame.KEYDOWN:
            if event.key in (pygame.K_UP, pygame.K_w):
                self.move(-1)
            elif event.key in (pygame.K_DOWN, pygame.K_s):
                self.move(1)
            elif event.key in (pygame.K_RETURN, pygame.K_SPACE):
                return self.buttons[self.index].text
        for button in self.buttons:
            if button.is_clicked(event):
                return button.text
        return None

    def draw(self, surface):
        for button in self.buttons:
            button.draw(surface)
```

`% len(self.buttons)` — соңғы батырмадан кейін біріншісіне «айналып» келу үшін.

## 5. Ойынды қайта бастау: reset()

«Қайта ойнау» үшін барлық ойын айнымалыларын бастапқы мәнге қайтару керек. Оларды бір функцияға жинау — қателерден сақтайды:

```python
def reset_game():
    """Жаңа ойынның бастапқы күйін қайтарады."""
    return {"score": 0, "lives": 3, "level": 1, "meteors": [], "player_x": 400}

game = reset_game()
game["score"] = 250
game["lives"] = 0
game = reset_game()            # «Қайта ойнау»
print(game["score"], game["lives"])
```

Нәтижесі:
```text
0 3
```

Спрайттармен жұмыс істегенде `reset` ішінде топтарды тазалап (`group.empty()`), ойыншыны қайта құрамыз.

## 6. Толық мысал: мәзір, пауза, Game Over

```python
import random
import pygame

WIDTH, HEIGHT = 800, 600
MENU, RULES, PLAYING, PAUSED, GAME_OVER = range(5)
SPAWN = pygame.USEREVENT + 1
WHITE, YELLOW, RED = (255, 255, 255), (255, 220, 0), (230, 50, 50)


class Button:
    def __init__(self, text, center, font, width=280, height=60):
        self.text = text
        self.font = font
        self.rect = pygame.Rect(0, 0, width, height)
        self.rect.center = center
        self.selected = False

    def is_clicked(self, event):
        return (event.type == pygame.MOUSEBUTTONDOWN and event.button == 1
                and self.rect.collidepoint(event.pos))

    def draw(self, surface):
        active = self.selected or self.rect.collidepoint(pygame.mouse.get_pos())
        pygame.draw.rect(surface, (90, 170, 255) if active else (40, 90, 170),
                         self.rect, border_radius=14)
        label = self.font.render(self.text, True, WHITE)
        surface.blit(label, label.get_rect(center=self.rect.center))


class Menu:
    def __init__(self, buttons):
        self.buttons = buttons
        self.index = 0
        buttons[0].selected = True

    def handle_event(self, event):
        if event.type == pygame.KEYDOWN and event.key in (pygame.K_UP, pygame.K_DOWN):
            self.buttons[self.index].selected = False
            step = -1 if event.key == pygame.K_UP else 1
            self.index = (self.index + step) % len(self.buttons)
            self.buttons[self.index].selected = True
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_RETURN:
            return self.buttons[self.index].text
        for b in self.buttons:
            if b.is_clicked(event):
                return b.text
        return None

    def draw(self, surface):
        for b in self.buttons:
            b.draw(surface)


class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((60, 40), pygame.SRCALPHA)
        pygame.draw.polygon(self.image, (0, 190, 255), [(30, 0), (60, 40), (0, 40)])
        self.rect = self.image.get_rect(midbottom=(WIDTH // 2, HEIGHT - 10))

    def update(self):
        keys = pygame.key.get_pressed()
        self.rect.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 7
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))


class Meteor(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        size = random.randint(25, 55)
        self.image = pygame.Surface((size, size), pygame.SRCALPHA)
        pygame.draw.circle(self.image, (160, 160, 160), (size // 2, size // 2), size // 2)
        self.rect = self.image.get_rect(midbottom=(random.randint(30, WIDTH - 30), 0))
        self.speed = random.randint(3, 6)

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            self.kill()


def draw_text(surface, text, font, color, **pos):
    image = font.render(text, True, color)
    surface.blit(image, image.get_rect(**pos))


pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Ғарыш қорғаушысы")
clock = pygame.time.Clock()
font = pygame.font.SysFont("arial, dejavusans", 30)
big_font = pygame.font.SysFont("arial, dejavusans", 64, bold=True)
pygame.time.set_timer(SPAWN, 500)

main_menu = Menu([Button("Ойнау", (WIDTH // 2, 280), font),
                  Button("Ережелер", (WIDTH // 2, 360), font),
                  Button("Шығу", (WIDTH // 2, 440), font)])
over_menu = Menu([Button("Қайта ойнау", (WIDTH // 2, 380), font),
                  Button("Мәзір", (WIDTH // 2, 460), font)])
overlay = pygame.Surface((WIDTH, HEIGHT))
overlay.fill((0, 0, 0))
overlay.set_alpha(150)

player = Player()
all_sprites = pygame.sprite.Group()
meteors = pygame.sprite.Group()
survived = 0


def reset_game():
    """Ойынды бастапқы күйге қайтарады."""
    global player, survived
    all_sprites.empty()
    meteors.empty()
    player = Player()
    all_sprites.add(player)
    survived = 0


state = MENU
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif state == MENU:
            choice = main_menu.handle_event(event)
            if choice == "Ойнау":
                reset_game()
                state = PLAYING
            elif choice == "Ережелер":
                state = RULES
            elif choice == "Шығу":
                running = False
        elif state == RULES:
            if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                state = MENU
        elif state == PLAYING:
            if event.type == SPAWN:
                m = Meteor()
                all_sprites.add(m)
                meteors.add(m)
            elif event.type == pygame.KEYDOWN and event.key in (pygame.K_p, pygame.K_ESCAPE):
                state = PAUSED
        elif state == PAUSED:
            if event.type == pygame.KEYDOWN:
                if event.key in (pygame.K_p, pygame.K_ESCAPE):
                    state = PLAYING
                elif event.key == pygame.K_m:
                    state = MENU
        elif state == GAME_OVER:
            choice = over_menu.handle_event(event)
            if choice == "Қайта ойнау":
                reset_game()
                state = PLAYING
            elif choice == "Мәзір":
                state = MENU

    if state == PLAYING:                      # жаңарту тек ойын күйінде
        all_sprites.update()
        survived += 1
        if pygame.sprite.spritecollideany(player, meteors):
            state = GAME_OVER

    screen.fill((5, 5, 25))
    if state == MENU:
        draw_text(screen, "ҒАРЫШ ҚОРҒАУШЫСЫ", big_font, YELLOW, center=(WIDTH // 2, 150))
        main_menu.draw(screen)
    elif state == RULES:
        rules = ["← → — кемені басқару", "P / Esc — пауза",
                 "Метеориттерден қаш!", "Esc — мәзірге оралу"]
        for i, line in enumerate(rules):
            draw_text(screen, line, font, WHITE, center=(WIDTH // 2, 180 + i * 60))
    else:                                     # PLAYING, PAUSED, GAME_OVER — ойын көрінеді
        all_sprites.draw(screen)
        draw_text(screen, f"Уақыт: {survived // 60} с", font, WHITE, topleft=(10, 10))
        if state == PAUSED:
            screen.blit(overlay, (0, 0))
            draw_text(screen, "ПАУЗА", big_font, WHITE, center=(WIDTH // 2, HEIGHT // 2 - 30))
            draw_text(screen, "P — жалғастыру, M — мәзір", font, WHITE,
                      center=(WIDTH // 2, HEIGHT // 2 + 40))
        elif state == GAME_OVER:
            screen.blit(overlay, (0, 0))
            draw_text(screen, "ОЙЫН АЯҚТАЛДЫ", big_font, RED, center=(WIDTH // 2, 200))
            draw_text(screen, f"Сен {survived // 60} секунд шыдадың", font, WHITE,
                      center=(WIDTH // 2, 280))
            over_menu.draw(screen)
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

**Талдау:**
- күйлер `range(5)` арқылы нөмірленген тұрақтылар (`MENU = 0`, ...);
- оқиғалар күйге қарай бөлек өңделеді — `P` пернесі тек ойын мен паузада мағыналы;
- пауза мен Game Over кезінде ойын **көрінеді**, бірақ жаңармайды, үстінен жартылай мөлдір қабат (альфа арнасы бар Surface) салынады;
- таймер (`SPAWN`) тек `PLAYING` күйінде өңделеді — паузада метеориттер жиналмайды.

## 7. Үлкен ойындарға: сахналар (scenes) архитектурасы

Ойын өскенде бір `while` циклі тым ұзарып кетеді. Кәсіби тәсіл — әр күйді жеке **класқа** (сахнаға) бөлу. Әр сахнаның бірдей үш әдісі бар — бұл полиморфизм:

```python
class Scene:
    """Барлық сахналардың ата-класы."""

    def handle_event(self, event):
        """Оқиғаны өңдейді; келесі сахна атауын немесе None қайтарады."""
        return None

    def update(self):
        pass

    def draw(self, surface):
        pass


class MenuScene(Scene):
    def handle_event(self, event):
        return "game"          # мысал: кез келген оқиғада ойынға өту


class GameScene(Scene):
    def update(self):
        print("Ойын жаңарып жатыр")


scenes = {"menu": MenuScene(), "game": GameScene()}
current = "menu"
next_scene = scenes[current].handle_event(None)
if next_scene:
    current = next_scene
scenes[current].update()
```

Нәтижесі:
```text
Ойын жаңарып жатыр
```

Негізгі цикл бұдан кейін тек: `scene.handle_event()` → `scene.update()` → `scene.draw()`. Үлкен жобада әр сахнаны бөлек файлда сақтау ыңғайлы.

## Жиі кездесетін қателер

**1. Паузада `update()` шақырылады** — ойын кідірмейді, тек жазу шығады. Жаңартуды `if state == PLAYING:` ішіне қойыңыз.

**2. Қайта бастағанда ескі объектілер қалады** — метеориттер мен оқтар экранда қалып, жаңа ойын бірден аяқталады. `reset_game()` ішінде барлық топты тазалаңыз.

**3. Бір басу екі күйден өтеді**
```python
state = "playing"
key = "p"
if state == "playing" and key == "p":
    state = "paused"
if state == "paused" and key == "p":     # бұл да орындалады!
    state = "playing"
print(state)
```
Нәтижесі `playing`: бір басу екі ауысуды қатар орындап, ойын паузаға мүлде кірмей қалды. Дұрысы: `if ... elif ...` — бір оқиға тек бір ауысу жасайды.

**4. Пауза кезінде таймер жұмыс істей береді** — паузадан кейін бірден 10 жау шығады. Таймер оқиғасын тек `PLAYING` күйінде өңдеңіз (немесе паузада `set_timer(SPAWN, 0)` арқылы тоқтатыңыз).

**5. Мәзірде батырма бір басуда бірнеше рет іске қосылады** — `get_pressed()` қолданылған. Мәзір — тек оқиғалар (`KEYDOWN`, `MOUSEBUTTONDOWN`) арқылы.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Ойын күйі (state) | Ойынның қазіргі режимі: мәзір, ойын, пауза, соңы |
| Күйлер машинасы (FSM) | Күйлер мен олардың арасындағы ауысулар моделі |
| Ауысу (transition) | Бір күйден екіншісіне өту шарты мен әрекеті |
| Мәзір | Ойынның бастапқы экраны, батырмалар тізімі |
| Пауза | Ойын жаңармайтын, бірақ экран салынатын күй |
| `reset()` | Ойынды бастапқы мәндерге қайтару |
| Сахна (scene) | Бір күйдің логикасы мен көрінісін біріктіретін класс |
| Overlay | Ойын үстінен салынатын жартылай мөлдір қабат |

## Ескерту

- Күйлерді жолдармен (`"menu"`) емес, `enum.Enum` арқылы сақтау қателерді азайтады: атаудағы қате бірден `AttributeError` береді.
- Python 3.10+ нұсқасында күйлерді `match state:` / `case ...:` құрылымымен өңдеуге болады — ұзын `if...elif` тізбегінен оқуға жеңіл.
- Әр экранға бөлек `while` циклін жазбаңыз — бір негізгі цикл және күй айнымалысы кодты қарапайым етеді.
- Паузадан шыққанда `clock.tick()`-ті бір рет бос шақырыңыз немесе `dt`-ні шектеңіз — әйтпесе пауза уақыты бір үлкен «секіріс» болып есептеледі.
- `reset()` ішінде спрайт топтарын тазалап қана қоймай, барлық ойын айнымалыларын (ұпай, өмір, деңгей) бастапқы мәнге қайтарыңыз.
