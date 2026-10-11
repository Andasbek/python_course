# Сабақ 46. Практикалық тапсырмалар: командалық ойын жобасын бастау

Бүгінгі практиканың нәтижесі — әр топта: **команда картасы**, **GDD**, **Kanban тақтасы** және **жобаның іске қосылатын қаңқасы** (бос терезе ашылатын ойын).

## Базалық деңгей

### 1-тапсырма. Команда картасын толтыру (топпен, 7 мин)

Төмендегі шаблонды көшіріп, `TEAM.md` файлына (немесе қағазға) толтырыңдар:

```text
КОМАНДА АТАУЫ: ______________________
ҰРАН (1 сөйлем): ____________________

| Аты-жөні | Рөлі            | Күшті жағы          | Байланыс (Telegram) |
|----------|-----------------|---------------------|---------------------|
|          | Тимлид          |                     |                     |
|          | Бағдарламашы    |                     |                     |
|          | Графика         |                     |                     |
|          | Дыбыс / Тестілеу|                     |                     |

КОМАНДА КЕЛІСІМІ (кемінде 4 ереже):
1. ...
2. ...
3. ...
4. ...
```

**Талап:** әр мүшенің кемінде бір рөлі бар; тимлид сайланған; келісімде «сынды кодқа айтамыз» ережесі бар.

### 2-тапсырма. Идеялар брейнштормы (топпен, 8 мин)

1. Әр мүше 2 идея жазады (төмендегі тізімнен немесе өзінен).
2. Әр мүше 2 нүктемен дауыс береді.
3. Ең көп дауыс алған идеяны «Орындала ма?» сұрақтарымен тексеріңдер:
   - 2 сабақта MVP-ді жасай аламыз ба?
   - Біз білетін Pygame құралдары жеткілікті ме (спрайт, қақтығыс, мәтін, дыбыс)?
   - Графика мен дыбысты өзіміз жасай аламыз ба немесе ашық лицензиялы табамыз ба?

**Идеялар банкі:**

| № | Атауы | Қысқаша сипаттамасы | Күрделілігі |
|---|---|---|---|
| 1 | «Алма бағы» | Себетті жылжытып, құлаған алмаларды ұста, шіріген алмадан қаш | ★ |
| 2 | «Қыран» | Бүркіт таулар арасынан ұшып, тұмарлар жинайды | ★★ |
| 3 | «Ғарыш қорғаушысы» | Кеме жоғарыдан келе жатқан астероидтарды атады | ★★ |
| 4 | «Тұлпар жарысы» | Жылқы кедергілерден секіреді (runner) | ★★ |
| 5 | «Мектепке кешікпе» | Оқушы көшеде көліктерден қашып, мектепке жетеді | ★★ |
| 6 | «Лабиринт батыры» | Лабиринттен кілт тауып, есікке жету | ★★★ |
| 7 | «Екі ойыншы: теннис» | Бір пернетақтада екі ойыншы ойнайды | ★★ |
| 8 | «Кеңістік бағбаны» | Ойыншы гүлдерді суарып, зиянкестерді қуады | ★★★ |

### 3-тапсырма. Ойын дизайны құжаты — GDD (топпен, 10 мин)

`GDD.md` шаблонын толтырыңдар:

```text
# GDD: <ойын атауы>

1. Жанры: ...
2. Бір сөйлеммен идея: ...
3. Ойыншының мақсаты: ...
4. Басқару:
   - ← → ↑ ↓ : ...
   - Space   : ...
   - Esc     : пауза
5. Механика:
   - Кейіпкер: ...
   - Жаулар/кедергілер: ...
   - Бонустар: ...
   - Өмір саны: ...  Ұпай қалай беріледі: ...
6. Жеңіс/жеңіліс шарты: ...
7. Экрандар: Мәзір → Ойын → Пауза → Game Over
8. Ресурстар:
   | Файл         | Түрі   | Жауапты | Дереккөз/лицензия |
   |--------------|--------|---------|-------------------|
   | player.png   | сурет  |         |                   |
   | hit.wav      | дыбыс  |         |                   |
9. MVP (Must): ...
   Should: ...
   Could: ...
   Won't: ...
```

**Талап:** MVP бөлімінде 4–6 пункт; әр ресурстың жауаптысы көрсетілген.

### 4-тапсырма. Kanban тақтасы (топпен, 7 мин)

GDD-дегі MVP пункттерін **кемінде 10 нақты тапсырмаға** бөліңдер. Әр тапсырманы стикерге (немесе GitHub Issue-ге) жазыңдар: `Тапсырма — Жауапты — Бағалау (мин)`.

Мысал:

```text
[To Do] Player класы: жоғары-төмен қозғалыс — Нұрлан — 40 мин
[To Do] Фон суреті 800×600 — Дана — 30 мин
[To Do] Мәзір экраны: «Бастау үшін Enter» — Әлия — 30 мин
[To Do] Соққы дыбысын табу, README-ге лицензия жазу — Ерасыл — 20 мин
```

**Талап:** әр мүшеде кемінде 2 тапсырма; бірде-бір тапсырма 60 минуттан аспайды.

### 5-тапсырма. Жобаның қаңқасы (тимлид + бағдарламашы, 10 мин)

Теориядағы құрылым бойынша папкалар мен файлдарды құрыңдар. `settings.py` және `main.py` жазып, бос терезе ашылатынын тексеріңдер (фон түсі, атауы, Esc басқанда жабылады). Терезе тақырыбында ойын атауы тұрсын.

## Кеңейтілген деңгей

### 6-тапсырма. Экран күйлері (state) бар қаңқа

`main.py`-ді `Game` класы ретінде жазыңдар. Ойынның күйі `self.state` айнымалысында сақталсын: `"menu"`, `"play"`, `"pause"`, `"game_over"`. Әр күй үшін бөлек әдіс: `draw_menu()`, `draw_play()` т.б. Enter — ойынды бастайды, P — пауза, Esc — шығу. Бұл қаңқаны әр мүше өз бөлігін қосатын «ортақ қаңқа» ретінде қолданамыз.

### 7-тапсырма. Placeholder спрайттар

`assets.py` ішінде `load_image(name, size)` функциясын жазыңдар: егер файл табылмаса, бағдарлама құламай, **қызыл түсті тіктөртбұрыш** қайтарсын. Сонда суретші әлі сурет салмаса да, ойын жұмыс істей береді.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Жоба жоспарының «сыни жолы»

Kanban-дағы тапсырмалар арасындағы тәуелділікті анықтаңдар (мысалы, «Қақтығыс» тапсырмасы «Player класы» мен «Enemy класы» дайын болғанда ғана басталады). Python-да тапсырмалар сөздігін жазып, әр тапсырманың ең ерте басталу уақытын және бүкіл жобаның ең аз ұзақтығын есептейтін бағдарлама жазыңдар.

```text
Кіріс (код ішінде):
tasks = {
    "settings": (10, []),
    "player":   (40, ["settings"]),
    "enemy":    (40, ["settings"]),
    "collision":(30, ["player", "enemy"]),
    "menu":     (30, ["settings"]),
    "score":    (20, ["collision"]),
}
Шығыс:
settings   басталады: 0 мин, аяқталады: 10 мин
...
Жобаның ең аз ұзақтығы: 100 мин
```

## Үй тапсырмасы

1. Тимлид: GitHub-та командалық репозиторий ашып, барлық мүшелерді **Collaborator** ретінде шақырсын; `README.md`-ге ойын атауы мен команда құрамын жазсын.
2. Әр мүше: Kanban-дағы өзінің бірінші тапсырмасын бастап, келесі сабаққа дайын нәтиже әкелсін (бағдарламашы — класс қаңқасы, графика — кемінде 2 спрайт, дыбыс — кемінде 2 дыбыс файлы және лицензия жазбасы).
3. Барлығы: Git-тің негізгі командаларын (`clone`, `add`, `commit`, `push`, `pull`) қайталап келсін (2-жылдың 5–6-сабақтары).

## Бағалау критерийлері

Бүгінгі сабақ — жобаның жоспарлау кезеңі. Топ ортақ балл алады, әр мүше жеке белсенділігі үшін қосымша балл алады.

| Критерий | Балл |
|---|---|
| Команда картасы толық: рөлдер, келісім ережелері | 15 |
| GDD барлық 9 бөлімі толтырылған, идея анық және орындалатын | 30 |
| MVP дұрыс анықталған (MoSCoW бойынша басымдық) | 15 |
| Kanban: ≥10 нақты тапсырма, әр мүшеде ≥2 тапсырма, уақыт бағаланған | 20 |
| Жоба қаңқасы: құрылым, `settings.py`, терезе ашылады | 10 |
| Идеяны 1 минуттық питчте таныстыру | 10 |
| **Барлығы** | **100** |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–4-тапсырмалар** — шығармашылық, бір дұрыс жауабы жоқ. Тексерерде назар аударыңыз:
- идея тым үлкен болса, топпен бірге MVP-ді қысқартыңыз;
- бір оқушыға барлық код тапсырмасы жиналмағанын тексеріңіз;
- графика/дыбыс мүшелерінде де код тапсырмасы болуы керек (`assets.py`, `sound.py`).

**5-тапсырма. `settings.py` + `main.py` (қарапайым қаңқа)**

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

WIDTH, HEIGHT, FPS = 800, 600, 60  # нақты жобада: from settings import ...
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

**6-тапсырма. Күйлері бар `Game` класы**

```python
# main.py — командалық ортақ қаңқа
import pygame

WIDTH, HEIGHT, FPS = 800, 600, 60
TITLE = "Қыран"
SKY_BLUE = (135, 206, 235)
DARK = (20, 30, 50)
WHITE = (255, 255, 255)


class Game:
    """Ойынның негізгі класы: күйлерді ауыстырады және ойын циклін жүргізеді."""

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
                    self.state = "game_over"  # уақытша: тексеру үшін

    def draw_menu(self):
        self.screen.fill(DARK)
        self.draw_text(TITLE, 200)
        self.draw_text("Бастау үшін Enter басыңыз", 320)

    def draw_play(self):
        self.screen.fill(SKY_BLUE)
        self.draw_text("Ойын жүріп жатыр (P — пауза)", 50)
        # мұнда бағдарламашылар спрайттарды қосады

    def draw_pause(self):
        self.draw_play()
        self.draw_text("ПАУЗА", HEIGHT // 2)

    def draw_game_over(self):
        self.screen.fill((80, 0, 0))
        self.draw_text("Ойын аяқталды!", 250)
        self.draw_text("Қайта бастау: Enter", 330)

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

**7-тапсырма. Құламайтын `load_image`**

```python
# assets.py
import os
import pygame

ASSETS_DIR = os.path.join(os.path.dirname(os.path.abspath(__file__)), "assets")


def load_image(name, size=(50, 50)):
    """Суретті жүктейді; файл жоқ болса — қызыл placeholder қайтарады."""
    path = os.path.join(ASSETS_DIR, "images", name)
    try:
        image = pygame.image.load(path)
        if pygame.display.get_surface() is not None:
            image = image.convert_alpha()
        if size is not None:
            image = pygame.transform.scale(image, size)
        return image
    except (FileNotFoundError, pygame.error):
        print(f"Ескерту: {name} табылмады, уақытша сурет қолданылды")
        placeholder = pygame.Surface(size if size else (50, 50))
        placeholder.fill((255, 0, 0))
        return placeholder
```

**8-тапсырма. Сыни жол (тәуелділіктер бойынша ең ерте уақыт)**

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
    """Тапсырманың ең ерте аяқталу уақытын рекурсиямен есептейді."""
    if name not in finish:
        duration, deps = tasks[name]
        start = max((finish_time(d) for d in deps), default=0)
        finish[name] = start + duration
    return finish[name]


for name in tasks:
    end = finish_time(name)
    start = end - tasks[name][0]
    print(f"{name:<10} басталады: {start} мин, аяқталады: {end} мин")

print("Жобаның ең аз ұзақтығы:", max(finish.values()), "мин")
```

Нәтижесі:

```text
settings   басталады: 0 мин, аяқталады: 10 мин
player     басталады: 10 мин, аяқталады: 50 мин
enemy      басталады: 10 мин, аяқталады: 50 мин
collision  басталады: 50 мин, аяқталады: 80 мин
menu       басталады: 10 мин, аяқталады: 40 мин
score      басталады: 80 мин, аяқталады: 100 мин
Жобаның ең аз ұзақтығы: 100 мин
```

Сыни жол: settings → player (немесе enemy) → collision → score. Бұл тапсырмалардың кешігуі бүкіл жобаны кешіктіреді.

</details>
