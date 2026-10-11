# Сабақ 48. Командалық ойын жобасын қорғау. Ойындар турнирі. Өзіндік жұмыс №3.

## Тақырыптар

1. README.md — жобаның «беті»
2. Жобаны басқа компьютерде іске қосу
3. Git тарихы бойынша мүшелердің үлесі
4. Рекордтар кестесі: нәтижелерді файлға сақтау
5. Pygame «шпаргалкасы»
6. Жиі кездесетін қателер
7. Негізгі ұғымдар

## 1. README.md — жобаның «беті»

**README.md** — репозиторийді GitHub-та ашқан адам көретін бірінші файл. Ол Markdown форматында жазылады және мына сұрақтарға жауап береді: бұл қандай жоба, оны қалай іске қосады, қалай ойнайды.

```markdown
# Қыран

Pygame-дегі аркада-раннер: бүркітті басқар, тауларды айналып ұш және алтын тұмарларды жина.

![Скриншот](docs/screenshot.png)

### Орнату және іске қосу

    git clone https://github.com/aliya-team/qyran-game.git
    cd qyran-game
    python -m venv .venv
    .venv\Scripts\activate        # Windows (Linux/macOS: source .venv/bin/activate)
    pip install -r requirements.txt
    python main.py

### Басқару

| Перне | Әрекет |
|---|---|
| ↑ ↓ | Жоғары/төмен ұшу |
| Space | Жылдамдату |
| Esc | Үзіліс |

### Команда

- Әлия Бекова — тимлид, мәзір және интерфейс (`main.py`, `ui.py`)
- Нұрлан Сейітов — ойын логикасы (`player.py`, `enemies.py`)
- Дана Омарова — графика (`assets/images`, `assets.py`)
- Ерасыл Тохтаров — дыбыс және тестілеу (`sound.py`)

### Ресурстар және лицензиялар

- Спрайттар: Kenney.nl (CC0)
- Соққы дыбысы: Freesound.org, авторы …, CC BY 4.0
```

README-дің міндетті бөлімдері: **атауы мен сипаттамасы**, **орнату және іске қосу**, **басқару**, **авторлар**, **ресурс көздері және олардың лицензиялары**. Скриншот немесе GIF түсінікті болуын едәуір арттырады.

## 2. Жобаны басқа компьютерде іске қосу

Тек автордың компьютерінде ғана жұмыс істейтін ойын — жиі кездесетін мәселе. Жоба кез келген жерде іске қосылуы үшін:

1. **Тәуелділіктерді** `requirements.txt` файлында **бекітеміз**:

```text
pygame==2.6.1
```

Файлды `pip freeze > requirements.txt` командасымен (белсендірілген виртуалды ортада) автоматты түрде жасауға, ал бәрін `pip install -r requirements.txt` арқылы орнатуға болады.

2. **Ресурс жолдарын** ағымдағы каталогтан емес, **жоба папкасынан құрамыз**:

```python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent
IMAGES_DIR = BASE_DIR / "assets" / "images"
SOUNDS_DIR = BASE_DIR / "assets" / "sounds"

print(IMAGES_DIR / "eagle.png")
```

3. **Файл атауларының регистрін ескереміз**: Windows-та `Eagle.png` пен `eagle.png` — бір файл, ал Linux пен macOS-та — әртүрлі.

4. **Кіру нүктесі біреу** — `main.py`, ал ондағы ойынды іске қосу шартпен қорғалған:

```python
def main():
    print("Ойын іске қосылды")  # мұнда терезе жасалып, ойын циклі жүреді


if __name__ == "__main__":
    main()
```

## 3. Git тарихы бойынша мүшелердің үлесі

Репозиторий тарихы жобада кім не істегенін көрсетеді:

```bash
git shortlog -sn --all          # әр автордың коммит саны
git log --author="Dana" --oneline   # бір автордың коммиттері
git log --stat                  # әр коммит қай файлдарды өзгертті
git blame player.py             # файлдың әр жолын соңғы рет кім өзгертті
```

```text
    14  Nurlan Seitov
     9  Aliya Bekova
     6  Dana Omarova
     5  Erasyl Tokhtarov
```

> Коммит саны — жалғыз көрсеткіш емес! Суретші аз коммит жасауы мүмкін, бірақ оның әр коммитінде ондаған сурет болуы мүмкін. Сондықтан `shortlog`-пен бірге `git log --stat` пен жабылған Issue/PR қаралады.

Егер бір адам әртүрлі атпен немесе поштамен коммит жасаған болса, оларды `shortlog`-та репозиторий түбіріндегі `.mailmap` файлы арқылы біріктіруге болады.

## 4. Рекордтар кестесі: нәтижелерді файлға сақтау

Ойыншылардың нәтижелерін JSON файлында сақтау ыңғайлы. Төмендегі функциялар нәтижені қосып, «үздік 5-тікті» қайтарады:

```python
import json
from pathlib import Path

SCORES_FILE = Path("highscores.json")


def load_scores():
    """Рекордтар тізімін оқиды; файл жоқ немесе бүлінген болса — бос тізім."""
    try:
        with open(SCORES_FILE, encoding="utf-8") as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return []


def add_score(name, score, top=5):
    """Нәтижені қосып, тек үздік top жазбаны сақтайды."""
    scores = load_scores()
    scores.append({"name": name, "score": score})
    scores.sort(key=lambda item: item["score"], reverse=True)
    scores = scores[:top]
    with open(SCORES_FILE, "w", encoding="utf-8") as f:
        json.dump(scores, f, ensure_ascii=False, indent=2)
    return scores


for row in add_score("Әлия", 1250):
    print(row["name"], row["score"])
```

`sort(key=..., reverse=True)` ұпай бойынша кему ретімен сұрыптайды, ал `[:top]` кесіндісі үздік жазбаларды қалдырады.

## 5. Pygame «шпаргалкасы»

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((640, 480))      # терезе
clock = pygame.time.Clock()                        # уақыт
font = pygame.font.SysFont("arial", 24)            # қаріп


class Ball(pygame.sprite.Sprite):
    """Спрайт: image + rect + update()."""

    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((30, 30), pygame.SRCALPHA)
        pygame.draw.circle(self.image, (255, 80, 80), (15, 15), 15)
        self.rect = self.image.get_rect(center=(320, 240))

    def update(self):
        keys = pygame.key.get_pressed()
        self.rect.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 5


balls = pygame.sprite.Group(Ball())                # спрайттар тобы
running = True
while running:                                      # ойын циклі
    for event in pygame.event.get():                # 1) оқиғалар
        if event.type == pygame.QUIT:
            running = False
    balls.update()                                  # 2) жаңарту
    screen.fill((30, 30, 60))                       # 3) салу
    balls.draw(screen)
    screen.blit(font.render("Сәлем, Pygame!", True, (255, 255, 255)), (10, 10))
    pygame.display.flip()
    clock.tick(60)                                  # 60 FPS
pygame.quit()
```

| Не керек? | Қалай? |
|---|---|
| Тіктөртбұрыш/шеңбер/сызық | `pygame.draw.rect`, `pygame.draw.circle`, `pygame.draw.line` |
| Сурет жүктеу | `pygame.image.load("a.png").convert_alpha()` |
| Перне басылды ма (бір рет) | `event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE` |
| Перне басылып тұр ма | `pygame.key.get_pressed()[pygame.K_LEFT]` |
| Тінтуір шерту | `event.type == pygame.MOUSEBUTTONDOWN`, `event.pos` |
| Екі rect қақтығысы | `rect1.colliderect(rect2)` |
| Спрайт пен топ қақтығысы | `pygame.sprite.spritecollide(sprite, group, dokill)` |
| Мәтін | `font.render(text, True, color)` + `screen.blit` |
| Дыбыс | `pygame.mixer.Sound("hit.wav").play()` |
| Таймер оқиғасы | `pygame.time.set_timer(pygame.USEREVENT + 1, 1000)` |

## Жиі кездесетін қателер

**1. Ойын басқа компьютерде ашылмайды** — онда `pygame` орнатылмаған немесе сурет жолы қате. → `requirements.txt` және `Path(__file__).parent`-тен құрылған жолдарды қолдану.

**2. `pygame.display.flip()` ұмытылады** — экранда ештеңе көрінбейді:

```python
# Қате: салынды, бірақ экран жаңартылмады
screen.fill((0, 0, 0))
pygame.draw.circle(screen, (255, 0, 0), (100, 100), 20)
# Дұрыс: салғаннан кейін
pygame.display.flip()
```

**3. `clock.tick()` жоқ** — ойын компьютер жылдамдығына байланысты тым жылдам жүреді. Дұрыс: циклдің соңында `clock.tick(60)`.

**4. `screen.fill()` жоқ** — қозғалған объект «із» қалдырады. Дұрыс: әр кадрдың басында фонды бояу.

**5. Қаріп әр кадрда жасалады.** `pygame.font.SysFont` циклдің ішінде шақырылса, ойын баяулайды. Дұрыс: қаріпті циклден **бұрын** бір рет жасау.

**6. README-де іске қосу нұсқаулығы жоқ.** Репозиторийді ашқан адам қандай кітапхана орнату керегін және қай файлды іске қосу керегін білмейді. → Нақты командалары бар «Орнату және іске қосу» бөлімі.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| README.md | GitHub репозиторийдің басты бетінде көрсететін, Markdown форматындағы жоба сипаттамасы |
| `requirements.txt` | Жобаның сыртқы кітапханаларының нұсқалары көрсетілген тізімі |
| Виртуалды орта (venv) | Бір жобаның интерпретаторы мен кітапханалары бар оқшауланған папка |
| Кіру нүктесі | Бағдарлама іске қосылатын файл (`main.py`) |
| `git shortlog -sn` | Әр автордың коммит санын көрсететін команда |
| `git blame` | Әр жолдың соңғы өзгерісінің авторын көрсететін команда |
| Рекордтар кестесі | Ойынды іске қосулар арасында сақталатын үздік нәтижелер тізімі |

## Ескерту

- Markdown-да README ішіндегі код блогы тілі көрсетілген үш кері тырнақшамен рәсімделеді (мысалы, ашатын тырнақшалардан кейін бірден `bash` немесе `python`) — GitHub синтаксисті бояп көрсетеді.
- `pip freeze` ортадағы **барлық** пакетті шығарады; ғаламдық Python-ға артық нәрсе орнатылған болса, файлға қоқыс түседі. Сондықтан `requirements.txt` тек жобаның таза виртуалды ортасынан жасалады.
- `Path(__file__).resolve().parent` `os.getcwd()`-тен сенімдірек: ағымдағы папка скрипт қай жерден іске қосылғанына байланысты, ал `__file__` — әрқашан модульдің өз жолы.
- Рекордтарды сақтағанда `json.JSONDecodeError`-ды ұстаңыз: ойын жазу кезінде апатты түрде тоқтаса, файл бос немесе қиылған болып қалуы мүмкін.
- Құжаттама: https://docs.python.org/3/library/venv.html, https://pip.pypa.io/en/stable/reference/requirements-file-format/, https://www.pygame.org/docs/
