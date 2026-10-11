# Урок 47. Практические задания: совместная разработка игры и Git

Сегодняшний урок — **этап разработки** проекта. Сначала попробуем командную работу с Git на «тренировочной площадке» (15 мин), затем поработаем над реальным игровым проектом (40 мин).

## Базовый уровень

### Задание 1. Подключение к командному репозиторию

1. Тимлид приглашает всех участников в репозиторий как Collaborator (если это не сделано дома).
2. Каждый участник:

```bash
git clone https://github.com/<тимлид>/<проект>.git
cd <проект>
git config user.name "Имя Фамилия"
git config user.email "твоя_почта@example.com"
git log --oneline
```

**Проверка:** у каждого участника `git log` показывает одинаковые коммиты.

### Задание 2. Создание своей ветки и открытие PR

1. Возьми свою задачу с Kanban-доски (например, «Класс Enemy»).
2. Создай ветку: `git switch -c feature/<задача>`.
3. Выполни задачу и сделай не менее **2 осмысленных коммитов**.
4. `git push -u origin feature/<задача>`.
5. Открой Pull Request на GitHub. Шаблон описания:

```text
## Что изменилось
- enemies.py: добавлен класс Enemy (движется справа налево, удаляется при выходе за экран)

## Как проверить
1. Запустить main.py
2. Каждую секунду справа должен появляться серый квадрат

Closes #4
```

### Задание 3. Code review

Каждый участник проверяет PR товарища по команде и оставляет по чек-листу из теории **не менее одного комментария** (похвалу или предложение). Если всё хорошо — **Approve**, затем **Merge pull request**.

### Задание 4. Намеренно создать конфликт и разрешить его (в парах)

1. Оба обновите `main` (`git pull`).
2. Ученик А: `git switch -c test/a`, в `settings.py` измени `TITLE` на `"Қыран: Горный перевал"`, коммит, `push`, PR → Merge.
3. Ученик Б: `git switch -c test/b`, измени ту же строку на `"Қыран: Степной ветер"`, коммит.
4. Ученик Б: `git pull origin main` → возникает конфликт.
5. Б, договорившись вдвоём, оставь одно название, удали метки, `add`, `commit`, `push`, PR → Merge.

**Проверка:** в `git log --oneline --graph --all` виден коммит слияния; в `settings.py` не осталось меток `<<<<<<<`.

### Задание 5. Добавление `.gitignore`

Тимлид добавляет в проект файл `.gitignore` (с содержимым из теории) и с помощью `git status` проверяет, что `__pycache__` не отслеживается.

## Продвинутый уровень

### Задание 6. Объединение MVP

Вместе с командой добавьте в `main.py` все модули: `Player`, `Enemy`, столкновение, очки и жизни, Game Over. Результат: играбельный MVP, запускаемый из ветки `main`. Готовые задачи на Kanban-доске переместите в колонку **Done**.

### Задание 7. Тегирование (tag) — первая версия

Когда MVP готов, тимлид ставит метку версии:

```bash
git tag -a v0.1 -m "MVP: первая играбельная версия"
git push origin v0.1
```

Опишите версию на GitHub в разделе **Releases**.

### Задание 8. Дополнение README

Добавьте в `README.md`: описание игры, клавиши управления, инструкцию по запуску (`pip install pygame`, `python main.py`), состав команды и роли, лицензии ресурсов, скриншот.

## ★ Олимпиадная / творческая задача

### Задание 9. Скрипт поиска меток конфликта

Чтобы команда случайно не отправила метки конфликта в коммит, напишите скрипт `check_conflicts.py`: он просматривает все `.py`-файлы в текущей папке (вместе с вложенными папками), находит строки, начинающиеся с `<<<<<<<`, `=======` или `>>>>>>>`, и выводит имя файла и номер строки. Если ничего не найдено — «Меток конфликта нет».

```text
Пример вывода:
settings.py:3: <<<<<<< HEAD
settings.py:5: =======
settings.py:7: >>>>>>> main
Всего найдено меток: 3!
```

### Задание 10. Уровни и рекорд в игре

После MVP: каждый раз, когда очки становятся кратными 100, скорость врагов растёт (уровень), а наивысший результат сохраняется в файл `highscore.txt` (файл находится в `.gitignore`).

## Домашнее задание

1. Завершите свои задачи на Kanban-доске и объедините их с `main` через PR.
2. Сыграйте в игру полностью не менее 3 раз и запишите найденные ошибки в GitHub **Issues** (тестировщик — не менее 5 записей).
3. К следующему уроку: подготовьте 5-минутное представление (презентацию или живую демонстрацию). Каждый участник рассказывает о той части, которую сделал сам.

## Критерии оценивания

Оценивание этапа разработки (индивидуально, для каждого ученика):

| Критерий | Балл |
|---|---|
| Работает в своей ветке, имя ветки соответствует соглашению | 15 |
| Не менее 2 осмысленных коммитов, сообщения понятные | 15 |
| Pull Request открыт с описанием и объединён | 20 |
| Проверил код товарища и оставил комментарий (code review) | 15 |
| Правильно разрешил конфликт (задание 4) | 15 |
| Задача с Kanban-доски выполнена, код добавлен в игру и работает | 20 |
| **Итого** | **100** |

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 1–5, 7–8** — команды Git полностью даны в теории. Частые ситуации:
- если при `push` запрашивается логин/пароль — GitHub требует не пароль, а **Personal Access Token** или вход через VS Code;
- если `git pull` открывает редактор (сообщение слияния) — сохранить и закрыть файл в VS Code или `git config --global core.editor "code --wait"`;
- если ученик сделал коммит в `main`: `git switch -c feature/x` (коммит переходит в новую ветку), затем `git switch main` и `git reset --hard origin/main`.

**Задание 6. Пример MVP (в одном файле для обучения; в команде разбивается на модули)**

```python
import random
import pygame

WIDTH, HEIGHT, FPS = 800, 600, 60
SKY = (135, 206, 235)
WHITE = (255, 255, 255)
BROWN = (139, 69, 19)
GRAY = (90, 90, 90)
GOLD = (255, 215, 0)


class Player(pygame.sprite.Sprite):
    """Беркут: двигается вверх-вниз."""

    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 40))
        self.image.fill(BROWN)
        self.rect = self.image.get_rect(center=(100, HEIGHT // 2))
        self.speed = 6

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_UP]:
            self.rect.y -= self.speed
        if keys[pygame.K_DOWN]:
            self.rect.y += self.speed
        self.rect.clamp_ip(pygame.Rect(0, 0, WIDTH, HEIGHT))


class Flying(pygame.sprite.Sprite):
    """Объект, летящий справа налево: препятствие или тумар."""

    def __init__(self, color, speed):
        super().__init__()
        self.image = pygame.Surface((36, 36))
        self.image.fill(color)
        y = random.randint(10, HEIGHT - 46)
        self.rect = self.image.get_rect(topleft=(WIDTH, y))
        self.speed = speed

    def update(self):
        self.rect.x -= self.speed
        if self.rect.right < 0:
            self.kill()


def main():
    pygame.init()
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Қыран")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont("arial", 28)

    spawn = pygame.USEREVENT + 1
    pygame.time.set_timer(spawn, 700)

    player = Player()
    all_sprites = pygame.sprite.Group(player)
    enemies = pygame.sprite.Group()
    coins = pygame.sprite.Group()
    score, lives, game_over = 0, 3, False

    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == spawn and not game_over:
                if random.random() < 0.7:
                    obj = Flying(GRAY, 5 + score // 100)
                    enemies.add(obj)
                else:
                    obj = Flying(GOLD, 4)
                    coins.add(obj)
                all_sprites.add(obj)
            elif event.type == pygame.KEYDOWN and game_over and event.key == pygame.K_RETURN:
                for sprite in enemies.sprites() + coins.sprites():
                    sprite.kill()
                score, lives, game_over = 0, 3, False

        if not game_over:
            all_sprites.update()
            if pygame.sprite.spritecollide(player, enemies, True):
                lives -= 1
                if lives <= 0:
                    game_over = True
            score += 10 * len(pygame.sprite.spritecollide(player, coins, True))

        screen.fill(SKY)
        all_sprites.draw(screen)
        info = font.render(f"Очки: {score}   Жизни: {lives}", True, WHITE)
        screen.blit(info, (10, 10))
        if game_over:
            text = font.render("Игра окончена! Enter — начать заново", True, WHITE)
            screen.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT // 2)))
        pygame.display.flip()
        clock.tick(FPS)

    pygame.quit()


if __name__ == "__main__":
    main()
```

**Задание 9. Поиск меток конфликта**

```python
import os

MARKERS = ("<<<<<<<", "=======", ">>>>>>>")
found = 0

for folder, subfolders, files in os.walk("."):
    # пропускаем папки виртуального окружения и .git
    subfolders[:] = [s for s in subfolders if s not in (".git", ".venv", "venv", "__pycache__")]
    for name in files:
        if not name.endswith(".py"):
            continue
        path = os.path.join(folder, name)
        with open(path, encoding="utf-8") as f:
            for number, line in enumerate(f, start=1):
                if line.startswith(MARKERS):
                    print(f"{os.path.relpath(path)}:{number}: {line.rstrip()}")
                    found += 1

if found:
    print(f"Всего найдено меток: {found}!")
else:
    print("Меток конфликта нет")
```

Примечание: строка `=======` иногда встречается и в комментариях, но в коде Python она редко стоит в начале строки — обсудите с учениками возможность такой «ложной тревоги».

**Задание 10. Сохранение рекорда (фрагмент)**

```python
import os

HIGHSCORE_FILE = "highscore.txt"


def load_highscore():
    """Читает рекорд из файла; если файла нет или он повреждён — 0."""
    if not os.path.exists(HIGHSCORE_FILE):
        return 0
    with open(HIGHSCORE_FILE, encoding="utf-8") as f:
        text = f.read().strip()
    return int(text) if text.isdigit() else 0


def save_highscore(score):
    """Если новый результат выше рекорда, записывает его в файл."""
    if score > load_highscore():
        with open(HIGHSCORE_FILE, "w", encoding="utf-8") as f:
            f.write(str(score))


save_highscore(250)
print("Рекорд:", load_highscore())
```

Результат (при первом запуске):

```text
Рекорд: 250
```

Уровень: скорость врага `5 + score // 100` — показано в решении задания 6.

</details>
