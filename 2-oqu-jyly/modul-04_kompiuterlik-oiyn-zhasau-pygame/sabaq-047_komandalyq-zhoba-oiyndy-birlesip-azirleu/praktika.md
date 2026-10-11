# Сабақ 47. Практикалық тапсырмалар: ойынды бірлесіп әзірлеу және Git

Бүгінгі сабақ — жобаның **әзірлеу кезеңі**. Алдымен Git-пен командалық жұмысты «жаттығу алаңында» байқап көреміз (15 мин), сосын нақты ойын жобасында жұмыс істейміз (40 мин).

## Базалық деңгей

### 1-тапсырма. Командалық репозиторийге қосылу

1. Тимлид репозиторийге барлық мүшені Collaborator ретінде шақырады (үйде жасалмаса).
2. Әр мүше:

```bash
git clone https://github.com/<тимлид>/<жоба>.git
cd <жоба>
git config user.name "Атың Тегің"
git config user.email "поштаң@example.com"
git log --oneline
```

**Тексеру:** әр мүшеде `git log` бірдей коммиттерді көрсетеді.

### 2-тапсырма. Өз тармағыңды құру және PR ашу

1. Kanban-нан өз тапсырмаңды ал (мысалы, «Enemy класы»).
2. Тармақ аш: `git switch -c feature/<тапсырма>`.
3. Тапсырманы орындап, кемінде **2 мағыналы коммит** жаса.
4. `git push -u origin feature/<тапсырма>`.
5. GitHub-та Pull Request аш. Сипаттама үлгісі:

```text
## Не өзгерді
- enemies.py: Enemy класы қосылды (оңнан солға қозғалады, экраннан шықса өшеді)

## Қалай тексеру керек
1. main.py іске қосу
2. Әр секунд сайын сұр шаршы оң жақтан пайда болуы керек

Closes #4
```

### 3-тапсырма. Code review

Әр мүше командаласының PR-ын тексеріп, теориядағы чек-парақ бойынша **кемінде бір пікір** қалдырады (мақтау немесе ұсыныс). Барлығы жақсы болса — **Approve**, сосын **Merge pull request**.

### 4-тапсырма. Қақтығысты әдейі тудырып, шешу (жұппен)

1. Екеуің де `main`-ді жаңартыңдар (`git pull`).
2. Оқушы А: `git switch -c test/a`, `settings.py`-де `TITLE`-ді `"Қыран: Тау асуы"` деп өзгерт, коммит, `push`, PR → Merge.
3. Оқушы Б: `git switch -c test/b`, сол жолды `"Қыран: Дала желі"` деп өзгерт, коммит.
4. Оқушы Б: `git pull origin main` → қақтығыс шығады.
5. Б екеуің келісіп, бір атауды қалдыр, белгілерді өшір, `add`, `commit`, `push`, PR → Merge.

**Тексеру:** `git log --oneline --graph --all` ішінде біріктіру коммиті көрінеді; `settings.py`-де `<<<<<<<` белгілері қалмаған.

### 5-тапсырма. `.gitignore` қосу

Тимлид жобаға `.gitignore` файлын қосады (теориядағы мазмұнмен) және `git status` арқылы `__pycache__` бақыланбайтынын тексереді.

## Кеңейтілген деңгей

### 6-тапсырма. MVP-ді біріктіру

Командамен бірге `main.py`-ге барлық модульдерді қосыңдар: `Player`, `Enemy`, қақтығыс, ұпай мен өмір, Game Over. Нәтиже: `main` тармағынан іске қосылатын, ойнауға болатын MVP. Kanban-дағы дайын тапсырмаларды **Done** бағанына жылжытыңдар.

### 7-тапсырма. Тегтеу (tag) — алғашқы нұсқа

MVP дайын болғанда тимлид нұсқаға белгі қояды:

```bash
git tag -a v0.1 -m "MVP: ойнауға болатын алғашқы нұсқа"
git push origin v0.1
```

GitHub-та **Releases** бөлімінде нұсқаны сипаттаңдар.

### 8-тапсырма. README толықтыру

`README.md`-ге қосыңдар: ойын сипаттамасы, басқару пернелері, іске қосу нұсқаулығы (`pip install pygame`, `python main.py`), команда құрамы мен рөлдер, ресурстардың лицензиялары, скриншот.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Қақтығыс белгілерін іздейтін скрипт

Команда кездейсоқ қақтығыс белгілерін коммитке жіберіп алмауы үшін `check_conflicts.py` скриптін жазыңдар: ол ағымдағы папкадағы (ішкі папкаларымен) барлық `.py` файлдарын қарап, `<<<<<<<`, `=======` немесе `>>>>>>>` жолдарынан басталатын жолдарды тауып, файл атауы мен жол нөмірін шығарады. Ештеңе табылмаса — «Қақтығыс белгілері жоқ».

```text
Шығару мысалы:
settings.py:3: <<<<<<< HEAD
settings.py:5: =======
settings.py:7: >>>>>>> main
Барлығы 3 белгі табылды!
```

### 10-тапсырма. Ойынға деңгейлер мен рекорд

MVP-ден кейін: ұпай 100-ге еселі болған сайын жаулардың жылдамдығы артсын (деңгей), ал ең жоғары ұпай `highscore.txt` файлына сақталсын (файл `.gitignore`-де тұр).

## Үй тапсырмасы

1. Kanban-дағы өз тапсырмаларыңды аяқтап, PR арқылы `main`-ге біріктіріңдер.
2. Ойынды кемінде 3 рет толық ойнап, табылған қателерді GitHub **Issues**-ке жазыңдар (тестілеуші — кемінде 5 жазба).
3. Келесі сабаққа: 5 минуттық таныстыру (презентация немесе тікелей демонстрация) дайындаңдар. Әр мүше өзі жасаған бөлік туралы айтады.

## Бағалау критерийлері

Әзірлеу кезеңін бағалау (жеке, әр оқушыға):

| Критерий | Балл |
|---|---|
| Өз тармағында жұмыс істейді, тармақ атауы келісімге сай | 15 |
| Кемінде 2 мағыналы коммит, хабарламалары түсінікті | 15 |
| Pull Request сипаттамасымен ашылған және біріктірілген | 20 |
| Командаластың кодын тексеріп, пікір қалдырған (code review) | 15 |
| Қақтығысты дұрыс шешкен (4-тапсырма) | 15 |
| Kanban тапсырмасы орындалған, код ойынға қосылып, жұмыс істейді | 20 |
| **Барлығы** | **100** |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–5, 7–8-тапсырмалар** — Git командалары теорияда толық берілген. Жиі кездесетін жағдайлар:
- `push` кезінде логин/пароль сұраса — GitHub пароль емес, **Personal Access Token** немесе VS Code арқылы кіруді қажет етеді;
- `git pull` редактор ашса (біріктіру хабарламасы) — VS Code-та файлды сақтап жабу, немесе `git config --global core.editor "code --wait"`;
- оқушы `main`-де коммит жасап қойса: `git switch -c feature/x` (коммит жаңа тармаққа көшеді), сосын `git switch main` және `git reset --hard origin/main`.

**6-тапсырма. MVP үлгісі (оқу үшін бір файлда; командада модульдерге бөлінеді)**

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
    """Бүркіт: жоғары-төмен қозғалады."""

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
    """Оңнан солға ұшатын объект: кедергі немесе тұмар."""

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
        info = font.render(f"Ұпай: {score}   Өмір: {lives}", True, WHITE)
        screen.blit(info, (10, 10))
        if game_over:
            text = font.render("Ойын аяқталды! Enter — қайта бастау", True, WHITE)
            screen.blit(text, text.get_rect(center=(WIDTH // 2, HEIGHT // 2)))
        pygame.display.flip()
        clock.tick(FPS)

    pygame.quit()


if __name__ == "__main__":
    main()
```

**9-тапсырма. Қақтығыс белгілерін іздеу**

```python
import os

MARKERS = ("<<<<<<<", "=======", ">>>>>>>")
found = 0

for folder, subfolders, files in os.walk("."):
    # виртуалды орта мен .git папкаларын өткізіп жібереміз
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
    print(f"Барлығы {found} белгі табылды!")
else:
    print("Қақтығыс белгілері жоқ")
```

Ескерту: `=======` жолы кейде түсініктемеде де кездеседі, бірақ Python кодында ол жол басында сирек тұрады — оқушылармен бұл «жалған дабыл» мүмкіндігін талқылаңыз.

**10-тапсырма. Рекордты сақтау (үзінді)**

```python
import os

HIGHSCORE_FILE = "highscore.txt"


def load_highscore():
    """Файлдан рекордты оқиды; файл жоқ немесе бұзылған болса — 0."""
    if not os.path.exists(HIGHSCORE_FILE):
        return 0
    with open(HIGHSCORE_FILE, encoding="utf-8") as f:
        text = f.read().strip()
    return int(text) if text.isdigit() else 0


def save_highscore(score):
    """Жаңа ұпай рекордтан жоғары болса, файлға жазады."""
    if score > load_highscore():
        with open(HIGHSCORE_FILE, "w", encoding="utf-8") as f:
            f.write(str(score))


save_highscore(250)
print("Рекорд:", load_highscore())
```

Нәтижесі (бірінші іске қосуда):

```text
Рекорд: 250
```

Деңгей: жау жылдамдығы `5 + score // 100` — 6-тапсырма шешімінде көрсетілген.

</details>
