# Урок 44. Игровое меню. Начало, пауза и окончание игры.

## Темы

1. Игровые состояния (game states)
2. Реализация машины состояний в коде
3. Класс Button
4. Меню: мышь и клавиатура
5. Перезапуск игры: reset()
6. Полный пример: меню, пауза, Game Over
7. Для больших игр: архитектура сцен (scenes)
8. Частые ошибки
9. Основные понятия

## 1. Игровые состояния (game states)

В каждый момент игра находится в **одном** состоянии. В каждом состоянии на экране показывается разное, и клавиши выполняют разные действия.

| Состояние | На экране | Клавиши | Следующее состояние |
|---|---|---|---|
| `MENU` | Название, кнопки «Играть», «Правила», «Выход» | ↑ ↓ Enter, мышь | `PLAYING`, `RULES`, выход |
| `RULES` | Правила игры | Esc | `MENU` |
| `PLAYING` | Игра, HUD | Управление, P/Esc | `PAUSED`, `GAME_OVER` |
| `PAUSED` | Остановленная игра + «ПАУЗА» | P — продолжить, M — меню | `PLAYING`, `MENU` |
| `GAME_OVER` | Очки, рекорд, «Играть снова», «Меню» | R, Enter, мышь | `PLAYING`, `MENU` |

**Схема состояний (диаграмма):**

```text
   RULES  ◀── «Правила» ──  MENU  ── «Выход» ──▶ (программа закрывается)
     │                        ▲  │
     └──────── Esc ───────────┘  │ «Играть» / Enter
                                 ▼
          ┌──── P ────▶  PLAYING  ◀──── P ────┐
          │                 │                 │
          │                 │ жизни = 0       │
          ▼                 ▼                 │
       PAUSED           GAME_OVER             │
          │  M → MENU       │  R → PLAYING    │
          └─────────────────┴─────────────────┘
```

Такая модель называется **машиной состояний** (finite state machine, FSM — конечный автомат). Она очень широко распространена в программировании: светофор, банкомат, лифт — всё это машины состояний.

## 2. Реализация машины состояний в коде

Самый простой способ — одна переменная и ветвление:

```python
MENU, PLAYING, PAUSED, GAME_OVER = "menu", "playing", "paused", "game_over"

state = MENU

def handle_key(state, key):
    """Возвращает новое состояние при нажатии клавиши."""
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

Результат:
```text
enter → playing
    p → paused
    p → playing
    p → paused
    m → menu
```

В игровом цикле в зависимости от состояния меняется каждый из трёх этапов (события → обновление → отрисовка):

```python
# Образец структуры (полный код в разделе 6)
# while running:
#     for event in pygame.event.get():
#         if state == MENU: ... события меню
#         elif state == PLAYING: ... игровые события
#     if state == PLAYING:
#         all_sprites.update()       # на ПАУЗЕ обновления НЕТ — игра «замирает»
#     draw_screen(state)
```

**Секрет паузы:** во время паузы мы не вызываем `update()`, но продолжаем рисовать экран. Тогда игра «замерзает», а окно продолжает реагировать.

## 3. Класс Button

В меню несколько кнопок — создаём их с помощью класса (ООП!):

```python
import pygame


class Button:
    """Кнопка с текстом, состоянием hover и выбора."""

    def __init__(self, text, center, font, width=260, height=60):
        self.text = text
        self.font = font
        self.rect = pygame.Rect(0, 0, width, height)
        self.rect.center = center
        self.selected = False            # выбрана ли с клавиатуры

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

## 4. Меню: мышь и клавиатура

Хорошее меню управляется обоими способами. Для клавиатуры храним индекс выбранной кнопки:

```python
import pygame


class Menu:
    """Список кнопок: ↑ ↓ — выбор, Enter или мышь — подтверждение."""

    def __init__(self, buttons):
        self.buttons = buttons
        self.index = 0
        self.buttons[0].selected = True

    def move(self, step):
        self.buttons[self.index].selected = False
        self.index = (self.index + step) % len(self.buttons)
        self.buttons[self.index].selected = True

    def handle_event(self, event):
        """Возвращает текст выбранной кнопки или None."""
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

`% len(self.buttons)` — чтобы после последней кнопки «зациклиться» на первую.

## 5. Перезапуск игры: reset()

Для «Играть снова» нужно вернуть все игровые переменные к начальным значениям. Собрать их в одной функции — значит уберечься от ошибок:

```python
def reset_game():
    """Возвращает начальное состояние новой игры."""
    return {"score": 0, "lives": 3, "level": 1, "meteors": [], "player_x": 400}

game = reset_game()
game["score"] = 250
game["lives"] = 0
game = reset_game()            # «Играть снова»
print(game["score"], game["lives"])
```

Результат:
```text
0 3
```

При работе со спрайтами внутри `reset` очищаем группы (`group.empty()`) и создаём игрока заново.

## 6. Полный пример: меню, пауза, Game Over

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
pygame.display.set_caption("Защитник космоса")
clock = pygame.time.Clock()
font = pygame.font.SysFont("arial, dejavusans", 30)
big_font = pygame.font.SysFont("arial, dejavusans", 64, bold=True)
pygame.time.set_timer(SPAWN, 500)

main_menu = Menu([Button("Играть", (WIDTH // 2, 280), font),
                  Button("Правила", (WIDTH // 2, 360), font),
                  Button("Выход", (WIDTH // 2, 440), font)])
over_menu = Menu([Button("Играть снова", (WIDTH // 2, 380), font),
                  Button("Меню", (WIDTH // 2, 460), font)])
overlay = pygame.Surface((WIDTH, HEIGHT))
overlay.fill((0, 0, 0))
overlay.set_alpha(150)

player = Player()
all_sprites = pygame.sprite.Group()
meteors = pygame.sprite.Group()
survived = 0


def reset_game():
    """Возвращает игру в начальное состояние."""
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
            if choice == "Играть":
                reset_game()
                state = PLAYING
            elif choice == "Правила":
                state = RULES
            elif choice == "Выход":
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
            if choice == "Играть снова":
                reset_game()
                state = PLAYING
            elif choice == "Меню":
                state = MENU

    if state == PLAYING:                      # обновление только в состоянии игры
        all_sprites.update()
        survived += 1
        if pygame.sprite.spritecollideany(player, meteors):
            state = GAME_OVER

    screen.fill((5, 5, 25))
    if state == MENU:
        draw_text(screen, "ЗАЩИТНИК КОСМОСА", big_font, YELLOW, center=(WIDTH // 2, 150))
        main_menu.draw(screen)
    elif state == RULES:
        rules = ["← → — управление кораблём", "P / Esc — пауза",
                 "Уворачивайся от метеоритов!", "Esc — вернуться в меню"]
        for i, line in enumerate(rules):
            draw_text(screen, line, font, WHITE, center=(WIDTH // 2, 180 + i * 60))
    else:                                     # PLAYING, PAUSED, GAME_OVER — игра видна
        all_sprites.draw(screen)
        draw_text(screen, f"Время: {survived // 60} с", font, WHITE, topleft=(10, 10))
        if state == PAUSED:
            screen.blit(overlay, (0, 0))
            draw_text(screen, "ПАУЗА", big_font, WHITE, center=(WIDTH // 2, HEIGHT // 2 - 30))
            draw_text(screen, "P — продолжить, M — меню", font, WHITE,
                      center=(WIDTH // 2, HEIGHT // 2 + 40))
        elif state == GAME_OVER:
            screen.blit(overlay, (0, 0))
            draw_text(screen, "ИГРА ОКОНЧЕНА", big_font, RED, center=(WIDTH // 2, 200))
            draw_text(screen, f"Ты продержался {survived // 60} с", font, WHITE,
                      center=(WIDTH // 2, 280))
            over_menu.draw(screen)
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

**Разбор:**
- состояния — константы, пронумерованные через `range(5)` (`MENU = 0`, ...);
- события обрабатываются отдельно в зависимости от состояния — клавиша `P` имеет смысл только в игре и на паузе;
- во время паузы и Game Over игра **видна**, но не обновляется, поверх рисуется полупрозрачный слой (Surface с альфа-каналом);
- таймер (`SPAWN`) обрабатывается только в состоянии `PLAYING` — во время паузы метеориты не накапливаются.

## 7. Для больших игр: архитектура сцен (scenes)

Когда игра растёт, один цикл `while` становится слишком длинным. Профессиональный подход — вынести каждое состояние в отдельный **класс** (сцену). У каждой сцены одни и те же три метода — это полиморфизм:

```python
class Scene:
    """Родительский класс всех сцен."""

    def handle_event(self, event):
        """Обрабатывает событие; возвращает имя следующей сцены или None."""
        return None

    def update(self):
        pass

    def draw(self, surface):
        pass


class MenuScene(Scene):
    def handle_event(self, event):
        return "game"          # пример: при любом событии переход в игру


class GameScene(Scene):
    def update(self):
        print("Игра обновляется")


scenes = {"menu": MenuScene(), "game": GameScene()}
current = "menu"
next_scene = scenes[current].handle_event(None)
if next_scene:
    current = next_scene
scenes[current].update()
```

Результат:
```text
Игра обновляется
```

После этого основной цикл — это только: `scene.handle_event()` → `scene.update()` → `scene.draw()`. В большом проекте каждую сцену удобно хранить в отдельном файле.

## Частые ошибки

**1. На паузе вызывается `update()`** — игра не останавливается, только появляется надпись. Поместите обновление внутрь `if state == PLAYING:`.

**2. При перезапуске остаются старые объекты** — метеориты и пули остаются на экране, и новая игра сразу заканчивается. Очищайте все группы внутри `reset_game()`.

**3. Одно нажатие проходит через два состояния**
```python
state = "playing"
key = "p"
if state == "playing" and key == "p":
    state = "paused"
if state == "paused" and key == "p":     # это тоже выполнится!
    state = "playing"
print(state)
```
Результат `playing`: одно нажатие выполнило подряд два перехода, и игра вообще не вошла в паузу. Правильно: `if ... elif ...` — одно событие делает только один переход.

**4. Таймер продолжает работать во время паузы** — после паузы сразу появляются 10 врагов. Обрабатывайте событие таймера только в состоянии `PLAYING` (или останавливайте его на паузе через `set_timer(SPAWN, 0)`).

**5. Кнопка меню срабатывает несколько раз за одно нажатие** — использован `get_pressed()`. Меню — только через события (`KEYDOWN`, `MOUSEBUTTONDOWN`).

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Игровое состояние (state) | Текущий режим игры: меню, игра, пауза, конец |
| Машина состояний (FSM) | Модель состояний и переходов между ними |
| Переход (transition) | Условие и действие перехода из одного состояния в другое |
| Меню | Начальный экран игры, список кнопок |
| Пауза | Состояние, в котором игра не обновляется, но экран рисуется |
| `reset()` | Возврат игры к начальным значениям |
| Сцена (scene) | Класс, объединяющий логику и отображение одного состояния |
| Overlay | Полупрозрачный слой, рисуемый поверх игры |

## Примечание

- Хранить состояния не строками (`"menu"`), а через `enum.Enum` надёжнее: опечатка в имени сразу даёт `AttributeError`.
- В Python 3.10+ состояния можно обрабатывать конструкцией `match state:` / `case ...:` — читается легче длинной цепочки `if...elif`.
- Не пишите для каждого экрана отдельный цикл `while` — один основной цикл и переменная состояния делают код проще.
- После выхода из паузы один раз вызовите `clock.tick()` впустую или ограничьте `dt` — иначе время паузы будет посчитано как один большой «скачок».
- В `reset()` не только очищайте группы спрайтов, но и возвращайте к начальным значениям все игровые переменные (очки, жизни, уровень).
