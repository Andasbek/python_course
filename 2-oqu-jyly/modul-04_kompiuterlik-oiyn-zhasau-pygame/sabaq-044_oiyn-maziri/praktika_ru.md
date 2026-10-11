# Урок 44. Практические задания: меню, пауза, конец игры

## Базовый уровень

### Задание 1. Схема состояний (на бумаге)
Нарисуй схему состояний своей командной игры (или «Защитника космоса»): все состояния (прямоугольники) и переходы (стрелки, над ними — клавиша или условие). Не менее 4 состояний.

### Задание 2. Класс Button
Напиши класс `Button` из теории. В окне три кнопки: «Красный», «Зелёный», «Синий» — при нажатии фон окрашивается в этот цвет. Когда курсор над кнопкой, она становится светлее.

### Задание 3. Главное меню
Добавь в «Защитника космоса» меню: название игры и кнопки «Играть», «Правила», «Выход». Меню управляется и мышью, и клавишами ↑ ↓ + Enter.

### Задание 4. Пауза
Во время игры `P` или `Esc` — пауза: игра останавливается, поверх — полупрозрачный чёрный слой и надпись «ПАУЗА». Снова `P` — продолжить. На паузе должна приостанавливаться и музыка (`music.pause()`).

## Продвинутый уровень

### Задание 5. Экран Game Over
Когда жизни закончились: «ИГРА ОКОНЧЕНА», очки, рекорд (42-й урок) и две кнопки: «Играть снова», «Меню». Функция `reset_game()` полностью возвращает игру в начальное состояние (очки, жизни, уровень, все группы спрайтов, таймер).

### Задание 6. Обратный отсчёт
После нажатия «Играть» и при выходе из паузы в центре экрана должен показываться обратный отсчёт 3 → 2 → 1 → «ПОЕХАЛИ!» (каждый ≈0,7 секунды). Во время отсчёта игра не обновляется. Новое состояние: `COUNTDOWN`.

### Задание 7. Экран настроек
Добавь в меню кнопку «Настройки». На экране настроек:
- «Сложность: Лёгкая / Средняя / Высокая» (меняется при каждом нажатии) — влияет на скорость и частоту метеоритов;
- «Звук: ВКЛ / ВЫКЛ»;
- «Назад».

## ★ Олимпиадная / творческая задача

**А. Архитектура сцен.** Перестрой игру, как в разделе 7 теории: родительский класс `Scene` и классы `MenuScene`, `GameScene`, `PauseScene`, `GameOverScene`. Основной цикл — не длиннее 10 строк. Общие данные (рекорд, настройки) хранятся в объекте `Game` и передаются сценам.

**Б. Анимированное меню.** На фоне меню должны лететь звёзды (40-й урок), название игры слегка «покачиваться» (координата y по `sin`), выбранная кнопка — немного увеличиваться.

## Домашнее задание

1. Доделай «Защитника космоса» с полным сценарием (меню, правила, игра, пауза, Game Over, играть снова) и загрузи на GitHub. Этот проект — индивидуальный проект IV модуля.
2. Добавь в README таблицу клавиш управления и изображение схемы состояний (фото или чертёж).
3. К следующему уроку: найди набор изображений из 3–4 кадров (sprite sheet) для анимации своего персонажа (kenney.nl).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1** — образец в таблице и схеме раздела 1 теории.

**Задание 2:**

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
pygame.display.set_caption("Цветные кнопки")
font = pygame.font.SysFont("arial, dejavusans", 28)
buttons = [Button("Красный", (120, 150), font, (180, 30, 30)),
           Button("Зелёный", (320, 150), font, (30, 150, 30)),
           Button("Синий", (520, 150), font, (30, 60, 180))]
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

**Задания 3–5** — полный пример из раздела 6 теории. Музыка в задании 4: при переходе `PLAYING → PAUSED` — `pygame.mixer.music.pause()`, при обратном переходе — `unpause()`. В `reset_game()` для задания 5 дополнительно: `score = 0; lives = 3; level = 1; pygame.time.set_timer(SPAWN, 600)`.

**Задание 6. Обратный отсчёт (фрагмент состояния и отрисовки):**

```python
import pygame

WIDTH, HEIGHT = 800, 600
PLAYING, COUNTDOWN = "playing", "countdown"
STEP_MS = 700
LABELS = ["3", "2", "1", "ПОЕХАЛИ!"]

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
            state = COUNTDOWN                      # пример: как при выходе из паузы
            countdown_start = pygame.time.get_ticks()
    if state == COUNTDOWN:
        step = (pygame.time.get_ticks() - countdown_start) // STEP_MS
        if step >= len(LABELS):
            state = PLAYING
    # if state == PLAYING: all_sprites.update()   — игра обновляется только в этом состоянии
    screen.fill((5, 5, 25))
    if state == COUNTDOWN:
        label = big_font.render(LABELS[step], True, (255, 220, 0))
        screen.blit(label, label.get_rect(center=(WIDTH // 2, HEIGHT // 2)))
    pygame.display.set_caption(state)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задание 7. Настройки (данные и обновление текста кнопки):**

```python
DIFFICULTIES = [
    {"name": "Лёгкая", "speed": (2, 4), "spawn": 900},
    {"name": "Средняя", "speed": (3, 6), "spawn": 600},
    {"name": "Высокая", "speed": (5, 9), "spawn": 350},
]

settings = {"difficulty": 1, "sound": True}


def difficulty_label():
    return f"Сложность: {DIFFICULTIES[settings['difficulty']]['name']}"


def sound_label():
    return "Звук: ВКЛ" if settings["sound"] else "Звук: ВЫКЛ"


def on_settings_click(button_text):
    """При нажатии кнопки меняет настройку; возвращает 'back' или None."""
    if button_text.startswith("Сложность"):
        settings["difficulty"] = (settings["difficulty"] + 1) % len(DIFFICULTIES)
    elif button_text.startswith("Звук"):
        settings["sound"] = not settings["sound"]
    elif button_text == "Назад":
        return "back"
    return None


on_settings_click(difficulty_label())
on_settings_click(sound_label())
print(difficulty_label(), "|", sound_label())
```

Результат:
```text
Сложность: Высокая | Звук: ВЫКЛ
```

Обновление текста кнопки после нажатия: `buttons[0].text = difficulty_label()`. Метеорит: `random.randint(*DIFFICULTIES[settings["difficulty"]]["speed"])`; таймер: `set_timer(SPAWN, DIFFICULTIES[...]["spawn"])`.

**★ А. Архитектура сцен (каркас):**

```python
import pygame

WIDTH, HEIGHT = 800, 600


class Game:
    """Общие данные: экран, шрифты, рекорд, настройки."""

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
        text = self.game.font.render("Enter — играть", True, (255, 255, 255))
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
        text = self.game.font.render(f"Игра: {self.timer // 60} с (Esc — меню)", True,
                                     (255, 255, 255))
        surface.blit(text, (20, 20))


if __name__ == "__main__":
    Game().run()
    pygame.quit()
```

Далее добавляются `PauseScene`, `GameOverScene`; в `GameScene` переносятся спрайты и логика «Защитника космоса».

**★ Б.** Покачивающееся название: `y = 150 + int(10 * math.sin(pygame.time.get_ticks() / 300))`; увеличение выбранной кнопки: рисовать через `rect.inflate(20, 10)`.

</details>
