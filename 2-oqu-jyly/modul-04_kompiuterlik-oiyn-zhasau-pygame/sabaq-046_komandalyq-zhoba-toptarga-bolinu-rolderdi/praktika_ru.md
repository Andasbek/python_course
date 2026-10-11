# Урок 46. Практические задания: начало командного игрового проекта

Результат сегодняшней практики — в каждой группе: **карта команды**, **GDD**, **Kanban-доска** и **запускаемый каркас проекта** (игра, открывающая пустое окно).

## Базовый уровень

### Задание 1. Заполнение карты команды (в группе, 7 мин)

Скопируйте шаблон ниже и заполните его в файле `TEAM.md` (или на бумаге):

```text
НАЗВАНИЕ КОМАНДЫ: ______________________
ДЕВИЗ (1 предложение): _________________

| ФИО      | Роль            | Сильная сторона     | Связь (Telegram)    |
|----------|-----------------|---------------------|---------------------|
|          | Тимлид          |                     |                     |
|          | Программист     |                     |                     |
|          | Графика         |                     |                     |
|          | Звук / Тестирование |                 |                     |

СОГЛАШЕНИЕ КОМАНДЫ (не менее 4 правил):
1. ...
2. ...
3. ...
4. ...
```

**Требование:** у каждого участника есть хотя бы одна роль; тимлид выбран; в соглашении есть правило «критикуем код, а не человека».

### Задание 2. Мозговой штурм идей (в группе, 8 мин)

1. Каждый участник записывает 2 идеи (из списка ниже или свои).
2. Каждый участник голосует 2 точками.
3. Идею, набравшую больше всего голосов, проверьте вопросами «Выполнимо ли?»:
   - Успеем ли мы сделать MVP за 2 урока?
   - Достаточно ли известных нам инструментов Pygame (спрайт, столкновение, текст, звук)?
   - Сможем ли мы сами сделать графику и звук или найдём их с открытой лицензией?

**Банк идей:**

| № | Название | Краткое описание | Сложность |
|---|---|---|---|
| 1 | «Яблоневый сад» | Двигай корзину, лови падающие яблоки, уворачивайся от гнилых | ★ |
| 2 | «Қыран» | Беркут летит между горами и собирает тумары | ★★ |
| 3 | «Защитник космоса» | Корабль стреляет по астероидам, летящим сверху | ★★ |
| 4 | «Скачки тулпаров» | Лошадь перепрыгивает препятствия (runner) | ★★ |
| 5 | «Не опоздай в школу» | Ученик уворачивается от машин на улице и добирается до школы | ★★ |
| 6 | «Герой лабиринта» | Найти в лабиринте ключ и добраться до двери | ★★★ |
| 7 | «Два игрока: теннис» | Два игрока играют на одной клавиатуре | ★★ |
| 8 | «Космический садовник» | Игрок поливает цветы и прогоняет вредителей | ★★★ |

### Задание 3. Документ дизайна игры — GDD (в группе, 10 мин)

Заполните шаблон `GDD.md`:

```text
# GDD: <название игры>

1. Жанр: ...
2. Идея одним предложением: ...
3. Цель игрока: ...
4. Управление:
   - ← → ↑ ↓ : ...
   - Space   : ...
   - Esc     : пауза
5. Механика:
   - Персонаж: ...
   - Враги/препятствия: ...
   - Бонусы: ...
   - Количество жизней: ...  Как начисляются очки: ...
6. Условие победы/поражения: ...
7. Экраны: Меню → Игра → Пауза → Game Over
8. Ресурсы:
   | Файл         | Тип         | Ответственный | Источник/лицензия |
   |--------------|-------------|---------------|-------------------|
   | player.png   | изображение |               |                   |
   | hit.wav      | звук        |               |                   |
9. MVP (Must): ...
   Should: ...
   Could: ...
   Won't: ...
```

**Требование:** в разделе MVP 4–6 пунктов; для каждого ресурса указан ответственный.

### Задание 4. Kanban-доска (в группе, 7 мин)

Разбейте пункты MVP из GDD **не менее чем на 10 конкретных задач**. Каждую задачу запишите на стикер (или в GitHub Issue): `Задача — Ответственный — Оценка (мин)`.

Пример:

```text
[To Do] Класс Player: движение вверх-вниз — Нурлан — 40 мин
[To Do] Фон 800×600 — Дана — 30 мин
[To Do] Экран меню: «Нажмите Enter, чтобы начать» — Алия — 30 мин
[To Do] Найти звук удара, записать лицензию в README — Ерасыл — 20 мин
```

**Требование:** у каждого участника не менее 2 задач; ни одна задача не превышает 60 минут.

### Задание 5. Каркас проекта (тимлид + программист, 10 мин)

Создайте папки и файлы по структуре из теории. Напишите `settings.py` и `main.py` и проверьте, что открывается пустое окно (цвет фона, название, закрывается по Esc). В заголовке окна должно быть название игры.

## Продвинутый уровень

### Задание 6. Каркас с состояниями экрана (state)

Напишите `main.py` в виде класса `Game`. Состояние игры хранится в переменной `self.state`: `"menu"`, `"play"`, `"pause"`, `"game_over"`. Для каждого состояния — отдельный метод: `draw_menu()`, `draw_play()` и т.д. Enter — начинает игру, P — пауза, Esc — выход. Этот каркас мы используем как «общий каркас», в который каждый участник добавляет свою часть.

### Задание 7. Спрайты-заглушки (placeholder)

Напишите в `assets.py` функцию `load_image(name, size)`: если файл не найден, программа не должна падать, а должна вернуть **прямоугольник красного цвета**. Тогда игра будет работать, даже если художник ещё не нарисовал изображение.

## ★ Олимпиадная / творческая задача

### Задание 8. «Критический путь» плана проекта

Определите зависимости между задачами на Kanban-доске (например, задача «Столкновение» начинается только тогда, когда готовы «Класс Player» и «Класс Enemy»). Напишите на Python словарь задач и программу, которая вычисляет самое раннее время начала каждой задачи и минимальную длительность всего проекта.

```text
Вход (внутри кода):
tasks = {
    "settings": (10, []),
    "player":   (40, ["settings"]),
    "enemy":    (40, ["settings"]),
    "collision":(30, ["player", "enemy"]),
    "menu":     (30, ["settings"]),
    "score":    (20, ["collision"]),
}
Выход:
settings   начало: 0 мин, конец: 10 мин
...
Минимальная длительность проекта: 100 мин
```

## Домашнее задание

1. Тимлид: создать на GitHub командный репозиторий и пригласить всех участников как **Collaborator**; записать в `README.md` название игры и состав команды.
2. Каждый участник: начать свою первую задачу на Kanban-доске и принести готовый результат к следующему уроку (программист — каркас класса, графика — не менее 2 спрайтов, звук — не менее 2 звуковых файлов и запись о лицензии).
3. Все: повторить основные команды Git (`clone`, `add`, `commit`, `push`, `pull`) (уроки 5–6 второго года).

## Критерии оценивания

Сегодняшний урок — этап планирования проекта. Группа получает общий балл, каждый участник получает дополнительный балл за личную активность.

| Критерий | Балл |
|---|---|
| Карта команды полная: роли, правила соглашения | 15 |
| Все 9 разделов GDD заполнены, идея понятная и выполнимая | 30 |
| MVP определён правильно (приоритеты по MoSCoW) | 15 |
| Kanban: ≥10 конкретных задач, у каждого участника ≥2 задач, время оценено | 20 |
| Каркас проекта: структура, `settings.py`, окно открывается | 10 |
| Представление идеи в 1-минутном питче | 10 |
| **Итого** | **100** |

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–4** — творческие, единственного правильного ответа нет. При проверке обратите внимание:
- если идея слишком большая, сократите MVP вместе с группой;
- проверьте, что все задачи по коду не собраны у одного ученика;
- у участников, отвечающих за графику/звук, тоже должна быть задача по коду (`assets.py`, `sound.py`).

**Задание 5. `settings.py` + `main.py` (простой каркас)**

```python
# settings.py
WIDTH = 800
HEIGHT = 600
FPS = 60
TITLE = "Қыран"
SKY_BLUE = (135, 206, 235)
WHITE = (255, 255, 255)
```

```python
# main.py
import sys
import pygame

WIDTH, HEIGHT, FPS = 800, 600, 60  # в реальном проекте: from settings import ...
TITLE = "Қыран"
SKY_BLUE = (135, 206, 235)

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption(TITLE)
clock = pygame.time.Clock()

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
            running = False
    screen.fill(SKY_BLUE)
    pygame.display.flip()
    clock.tick(FPS)

pygame.quit()
sys.exit()
```

**Задание 6. Класс `Game` с состояниями**

```python
# main.py — общий каркас команды
import pygame

WIDTH, HEIGHT, FPS = 800, 600, 60
TITLE = "Қыран"
SKY_BLUE = (135, 206, 235)
DARK = (20, 30, 50)
WHITE = (255, 255, 255)


class Game:
    """Основной класс игры: переключает состояния и ведёт игровой цикл."""

    def __init__(self):
        pygame.init()
        self.screen = pygame.display.set_mode((WIDTH, HEIGHT))
        pygame.display.set_caption(TITLE)
        self.clock = pygame.time.Clock()
        self.font = pygame.font.SysFont("arial", 36)
        self.state = "menu"
        self.running = True

    def draw_text(self, text, y):
        surface = self.font.render(text, True, WHITE)
        rect = surface.get_rect(center=(WIDTH // 2, y))
        self.screen.blit(surface, rect)

    def handle_events(self):
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                self.running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    self.running = False
                elif event.key == pygame.K_RETURN and self.state in ("menu", "game_over"):
                    self.state = "play"
                elif event.key == pygame.K_p and self.state == "play":
                    self.state = "pause"
                elif event.key == pygame.K_p and self.state == "pause":
                    self.state = "play"
                elif event.key == pygame.K_g and self.state == "play":
                    self.state = "game_over"  # временно: для проверки

    def draw_menu(self):
        self.screen.fill(DARK)
        self.draw_text(TITLE, 200)
        self.draw_text("Нажмите Enter, чтобы начать", 320)

    def draw_play(self):
        self.screen.fill(SKY_BLUE)
        self.draw_text("Идёт игра (P — пауза)", 50)
        # здесь программисты добавляют спрайты

    def draw_pause(self):
        self.draw_play()
        self.draw_text("ПАУЗА", HEIGHT // 2)

    def draw_game_over(self):
        self.screen.fill((80, 0, 0))
        self.draw_text("Игра окончена!", 250)
        self.draw_text("Начать заново: Enter", 330)

    def run(self):
        while self.running:
            self.handle_events()
            if self.state == "menu":
                self.draw_menu()
            elif self.state == "play":
                self.draw_play()
            elif self.state == "pause":
                self.draw_pause()
            elif self.state == "game_over":
                self.draw_game_over()
            pygame.display.flip()
            self.clock.tick(FPS)
        pygame.quit()


if __name__ == "__main__":
    Game().run()
```

**Задание 7. Неломающийся `load_image`**

```python
# assets.py
import os
import pygame

ASSETS_DIR = os.path.join(os.path.dirname(os.path.abspath(__file__)), "assets")


def load_image(name, size=(50, 50)):
    """Загружает изображение; если файла нет — возвращает красную заглушку (placeholder)."""
    path = os.path.join(ASSETS_DIR, "images", name)
    try:
        image = pygame.image.load(path)
        if pygame.display.get_surface() is not None:
            image = image.convert_alpha()
        if size is not None:
            image = pygame.transform.scale(image, size)
        return image
    except (FileNotFoundError, pygame.error):
        print(f"Предупреждение: {name} не найден, использовано временное изображение")
        placeholder = pygame.Surface(size if size else (50, 50))
        placeholder.fill((255, 0, 0))
        return placeholder
```

**Задание 8. Критический путь (самое раннее время с учётом зависимостей)**

```python
tasks = {
    "settings": (10, []),
    "player": (40, ["settings"]),
    "enemy": (40, ["settings"]),
    "collision": (30, ["player", "enemy"]),
    "menu": (30, ["settings"]),
    "score": (20, ["collision"]),
}

finish = {}


def finish_time(name):
    """Рекурсивно вычисляет самое раннее время завершения задачи."""
    if name not in finish:
        duration, deps = tasks[name]
        start = max((finish_time(d) for d in deps), default=0)
        finish[name] = start + duration
    return finish[name]


for name in tasks:
    end = finish_time(name)
    start = end - tasks[name][0]
    print(f"{name:<10} начало: {start} мин, конец: {end} мин")

print("Минимальная длительность проекта:", max(finish.values()), "мин")
```

Результат:

```text
settings   начало: 0 мин, конец: 10 мин
player     начало: 10 мин, конец: 50 мин
enemy      начало: 10 мин, конец: 50 мин
collision  начало: 50 мин, конец: 80 мин
menu       начало: 10 мин, конец: 40 мин
score      начало: 80 мин, конец: 100 мин
Минимальная длительность проекта: 100 мин
```

Критический путь: settings → player (или enemy) → collision → score. Задержка этих задач задерживает весь проект.

</details>
