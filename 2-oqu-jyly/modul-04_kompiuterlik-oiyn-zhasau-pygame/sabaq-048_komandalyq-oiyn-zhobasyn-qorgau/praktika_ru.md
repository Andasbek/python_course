# Урок 48. Практические задания: защита проекта, турнир и Самостоятельная работа №3

## Базовый уровень

### Задание 1. Чек-лист подготовки к защите (в начале урока, 5 мин)

Каждая группа перед защитой проверяет:

- [ ] Игра запускается из ветки `main` (выполнен `git pull`)
- [ ] `pygame` установлен, все файлы изображений/звуков на месте
- [ ] Окно игры открыто, виден экран меню
- [ ] README готов: описание, управление, запуск, команда, лицензии
- [ ] Каждый участник знает свою часть представления
- [ ] `git log --oneline --graph` готов к показу
- [ ] Выбран самый интересный фрагмент кода (10–15 строк)

### Задание 2. Регламент защиты проекта

| Этап | Время | Кто |
|---|---|---|
| Представление (структура — в теории) | 5 мин | Все участники |
| Вопросы комиссии и групп | 3 мин | Отвечают не менее 2 участников |
| Заполнение оценочного листа | 1 мин | Другие группы и педагог |

Очерёдность определяется жеребьёвкой. Если время превышено, звенит таймер — группа переходит к итогам.

### Задание 3. Лист взаимооценивания

Каждая группа оценивает проекты других групп:

```text
Оценивающая группа: ____________   Оцениваемая группа: ____________

| Критерий                             | 1 | 2 | 3 | 4 | 5 |
|--------------------------------------|---|---|---|---|---|
| Интересность и оригинальность идеи   |   |   |   |   |   |
| Удобство и оформление игры           |   |   |   |   |   |
| Игра работала без ошибок             |   |   |   |   |   |
| Качество представления, ответы       |   |   |   |   |   |

★ Первая звезда (похвала): ......................................
★ Вторая звезда (похвала): ......................................
➜ Пожелание (предложение): ......................................
```

### Задание 4. Турнир игр

Турнирная таблица (на доске или в общей таблице):

```text
| Ученик     | «Қыран» | «Яблоневый сад» | «Защитник космоса» | «Тулпар» | Сумма |
|------------|---------|-----------------|--------------------|----------|-------|
| Айгерим    |   340   |       —         |        510         |   220    | 1070  |
| ...        |         |                 |                    |          |       |
```

Сыграй в каждую игру 2 минуты и запиши свои очки. В игру своей группы ты не играешь.

### Задание 5. Личная рефлексия (в конце урока)

1. Мой вклад в проект: ...
2. Самым трудным для меня было: ...
3. Чему я научился у команды: ...
4. Что в следующем проекте я сделаю иначе: ...

## Продвинутый уровень

### Задание 6. Версия проекта 1.0

После защиты группы записывают предложения комиссии и других групп в GitHub Issues, дома выполняют не менее 2 предложений и делают релиз с тегом `v1.0`.

### Задание 7. Программа обработки результатов турнира

Напишите программу, которая хранит результаты турнира в виде словаря, вычисляет суммарный балл каждого ученика и выводит рейтинговую таблицу (по убыванию). Поскольку шкала очков в каждой игре разная, **нормализуйте** очки: очки ученика / наибольшие очки в этой игре × 100.

## ★ Олимпиадная / творческая задача

### Задание 8. «Трейлер» игры

Сделай 30-секундный трейлер своей игры: добавь в игру функцию, которая по нажатию клавиши `F12` сохраняет скриншот экрана как `screenshots/shot_001.png`, `shot_002.png` ... (`pygame.image.save(screen, path)`). Собери из скриншотов слайд-шоу или GIF.

---

## Самостоятельная работа №3. Pygame: создание компьютерной игры

**Время:** 35 минут. **Части:** А — теоретические вопросы (письменно), Б — практическое задание (файл `.py`). Сохрани файл как `osh3_Фамилия.py`.

### Вариант 1

**Часть А. Вопросы**

1. Запиши по порядку три основных этапа игрового цикла.
2. Для чего нужен `pygame.display.flip()`? Что будет, если его нет?
3. Что делает этот код? `clock.tick(30)`
4. В чём разница между событием `KEYDOWN` и `pygame.key.get_pressed()`? Приведи пример для каждого.
5. `pygame.sprite.spritecollide(player, coins, True)` — что означает третий аргумент `True`? Что возвращает функция?

**Часть Б. Игра «Собиратель звёзд»**

Требования:
1. Окно 600×400, заголовок «Собиратель звёзд», фон — тёмно-синий, FPS = 60.
2. Игрок — зелёный квадрат 40×40, класс `Player(pygame.sprite.Sprite)`; двигается четырьмя клавишами-стрелками, не выходит за пределы окна.
3. Звезда — жёлтый круг (радиус 12), спрайт `Star`, появляется в случайном месте.
4. Когда игрок касается звезды, очки +1, звезда перемещается в новое случайное место.
5. В левом верхнем углу экрана текст «Очки: N».
6. Через 30 секунд игра останавливается, в центре экрана выводится «Время вышло! Очки: N». Оставшееся время показывается на экране.

### Вариант 2

**Часть А. Вопросы**

1. Для чего вызываются `pygame.init()` и `pygame.quit()`?
2. Где находится точка (0, 0) в системе координат Pygame? Куда смещается объект при увеличении `y`?
3. Зачем в начале каждого кадра вызываем `screen.fill(...)`?
4. Какие два атрибута обязательно должны быть у объекта, унаследованного от класса `Sprite`?
5. Что проверяет `rect.collidepoint(event.pos)`? С каким событием его используют?

**Часть Б. Игра «Лопни шарики»**

Требования:
1. Окно 600×400, заголовок «Лопни шарики», фон — белый, FPS = 60.
2. Класс `Balloon(pygame.sprite.Sprite)`: круг случайного цвета (радиус 25), появляется в случайном месте.
3. Каждую 1 секунду появляется новый шарик (`pygame.time.set_timer`).
4. Шарик сам исчезает через 2 секунды — это «промах», количество промахов +1.
5. При щелчке мышью по шарику он исчезает, очки +1.
6. На экране «Очки: N   Промахи: M». После 5 промахов игра останавливается и выводится «Игра окончена! Очки: N».

## Домашнее задание

1. Доработай дома свой вариант самостоятельной работы: добавь звук и сохранение рекорда в файл.
2. Напиши в README командного проекта раздел «Планы на будущее» после защиты.
3. Подготовка к следующему модулю: повтори работу с файлами из 1-го года (`open`, `read`, `write`, `with`).

## Критерии оценивания

**Командный проект (модульный проект), 100 баллов:**

| Критерий | Доля | Баллы |
|---|---|---|
| Выполнение технических требований (охват тем модуля) | 30% | 30 |
| Стабильная и безошибочная работа программы | 25% | 25 |
| Структура и качество кода (PEP 8, разбиение на классы, docstring) | 20% | 20 |
| Удобство для пользователя и оформление | 15% | 15 |
| Правильная загрузка работы на GitHub и написание README | 10% | 10 |

Личный балл = командный балл × коэффициент личного вклада (0,8 / 0,9 / 1,0; за особую активность +5 баллов, но не больше 100).

**Самостоятельная работа №3, 100 баллов:**

| Критерий | Доля | Баллы |
|---|---|---|
| Правильная работа программы и соответствие требованиям задания (6 требований части Б × 5 баллов + часть А 5 вопросов × 2 балла) | 40% | 40 |
| Эффективность алгоритма, рациональность решения (использование Sprite/Group, таймер, однократное создание шрифта) | 25% | 25 |
| Читаемость кода: именование, комментарии, структура | 20% | 20 |
| Самостоятельное выполнение работы и своевременная сдача | 15% | 15 |

Уровень освоения: 85–100 — высокий, 60–84 — средний, 40–59 — начальный.

## Для педагога: решения

<details><summary>Показать решения</summary>

### Самостоятельная работа №3, Вариант 1, часть А

1. Обработка событий (`pygame.event.get()`) → обновление состояния игры (`update`) → отрисовка на экране (`fill`, `draw`, `blit`, `flip`).
2. Выводит нарисованный кадр на экран (переключает буфер). Если его нет, экран остаётся пустым/чёрным.
3. Делает паузу так, чтобы цикл выполнялся не чаще 30 раз в секунду (ограничение 30 FPS).
4. `KEYDOWN` — событие, которое происходит один раз в момент нажатия клавиши (например, `Space` — прыжок, выстрел). `get_pressed()` — в каждом кадре проверяет, удерживается ли клавиша (например, непрерывное движение стрелками).
5. `True` — столкнувшиеся спрайты удаляются из группы `coins` (и из всех групп). Функция возвращает **список** столкнувшихся спрайтов.

### Вариант 1, часть Б

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
    """Зелёный квадрат: двигается клавишами-стрелками."""

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
    """Жёлтая звезда-круг: появляется в случайном месте."""

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
    pygame.display.set_caption("Собиратель звёзд")
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
        screen.blit(font.render(f"Очки: {score}", True, WHITE), (10, 10))
        screen.blit(font.render(f"Время: {max(seconds_left, 0)}", True, WHITE), (WIDTH - 120, 10))
        if time_over:
            text = big_font.render(f"Время вышло! Очки: {score}", True, WHITE)
            screen.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT // 2)))
        pygame.display.flip()
        clock.tick(FPS)

    pygame.quit()


if __name__ == "__main__":
    main()
```

### Вариант 2, часть А

1. `pygame.init()` — запускает все модули Pygame (дисплей, шрифт, звук); `pygame.quit()` — правильно закрывает их, освобождает ресурсы.
2. (0, 0) — **левый верхний** угол окна; при увеличении `y` объект смещается **вниз**.
3. Чтобы стереть изображение предыдущего кадра; иначе движущиеся объекты оставляют «след».
4. `self.image` (Surface — внешний вид) и `self.rect` (положение и размер).
5. Проверяет, находится ли точка (координата мыши) внутри прямоугольника; используется вместе с событием `MOUSEBUTTONDOWN`.

### Вариант 2, часть Б

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
    """Шарик случайного цвета: сам исчезает через 2 секунды."""

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
    pygame.display.set_caption("Лопни шарики")
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
        screen.blit(font.render(f"Очки: {score}   Промахи: {misses}", True, BLACK), (10, 10))
        if game_over:
            text = big_font.render(f"Игра окончена! Очки: {score}", True, BLACK)
            screen.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT // 2)))
        pygame.display.flip()
        clock.tick(FPS)

    pygame.quit()


if __name__ == "__main__":
    main()
```

Примечание: `collidepoint` проверяет по квадратному `rect` (учитываются и углы вокруг круга) — для самостоятельной работы этого достаточно. Более точная проверка: расстояние до центра `<= RADIUS`.

### Задание 7. Рейтинг турнира

```python
results = {
    "Айгерим": {"Қыран": 340, "Яблоневый сад": 120, "Космос": 510},
    "Бауыржан": {"Қыран": 420, "Яблоневый сад": 90, "Космос": 300},
    "Сымбат": {"Қыран": 150, "Яблоневый сад": 160, "Космос": 450},
}

# наибольшие очки в каждой игре
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

Результат:

```text
1. Айгерим    256.0
2. Сымбат     223.9
3. Бауыржан   215.1
```

### Задание 8. Функция скриншота (фрагмент)

```python
import os
import pygame


def save_screenshot(screen, folder="screenshots"):
    """Сохраняет экран как screenshots/shot_001.png, shot_002.png ..."""
    os.makedirs(folder, exist_ok=True)
    number = len(os.listdir(folder)) + 1
    path = os.path.join(folder, f"shot_{number:03}.png")
    pygame.image.save(screen, path)
    return path

# В игровом цикле:
# if event.type == pygame.KEYDOWN and event.key == pygame.K_F12:
#     print("Сохранено:", save_screenshot(screen))
```

</details>
