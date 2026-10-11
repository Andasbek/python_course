# Сабақ 34. Практикалық тапсырмалар: Pygame орнату және ойын циклі

## Базалық деңгей

### 1-тапсырма. Орнату және тексеру
1. `pygame-lessons` папкасын құр, виртуалды орта жаса және белсендір.
2. `pip install pygame` орында.
3. `python -m pygame.examples.aliens` арқылы мысал ойынды іске қос.
4. `check.py` файлында Pygame нұсқасын шығар.

Мысал шығару:
```text
Pygame нұсқасы: 2.6.1
```

### 2-тапсырма. Ойын қаңқасы
Теориядағы қаңқаны **қарамай**, жадыдан жаз: 640×480 терезе, тақырыбы «Менің ойыным», фон түсі қою көк `(20, 40, 90)`, 60 FPS, терезе «×» арқылы жабылады.
**Кеңес:** алдымен қағазға 5 қадамды жаз: init → set_mode → clock → цикл → quit.

### 3-тапсырма. Escape пернесімен шығу
2-тапсырмадағы ойынға `Escape` пернесі басылғанда да шығуды қос.
**Кеңес:** `event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE`.

### 4-тапсырма. Оқиғалар журналы
Әр оқиғаның атауын консольге шығаратын бағдарлама жаз. Тінтуірді жылжыт, перне бас, тінтуірді бас — қандай оқиғалар пайда болатынын дәптерге кестеге жаз.

| Әрекет | Оқиға атауы |
|---|---|
| Тінтуірді жылжыту | MouseMotion |
| ... | ... |

## Кеңейтілген деңгей

### 5-тапсырма. «Бағдаршам»
Фон түсі әр 60 кадр сайын (≈ 1 секунд) ауысып тұрсын: қызыл → сары → жасыл → қызыл ...
**Кеңес:** түстер тізімі және `frame // 60 % 3` өрнегі.

### 6-тапсырма. FPS-ті тақырыпта көрсету
Терезе тақырыбында нақты FPS көрсетілсін: `FPS: 59.9`. `clock.get_fps()` әдісін қолдан. `clock.tick(60)` мәнін 10, 30, 120-ға өзгертіп, нәтижені салыстыр.

### 7-тапсырма. Секундомер
Тақырыпта ойын басталғаннан бергі секунд саны көрсетілсін (`Уақыт: 12 с`). Бос орын (`Space`) пернесі секундомерді нөлге қайтарады.
**Кеңес:** `pygame.time.get_ticks()` — `init()`-тен бергі миллисекундтар.

## ★ Олимпиадалық / шығармашылық тапсырма

**«Түс таңдау ойыны».** Фон түсі әр кадрда біртіндеп қызылдан көкке және кері ауысып тұрады (градиент «тынысы»). Ойыншы `Space` басқан сәтте түс тоқтайды да, консольге оның RGB мәні және «таза күлгінге» `(128, 0, 128)` қаншалықты жақын екені (компоненттер айырмасының қосындысы) шығады. Ең аз айырма — жеңіс!

## Үй тапсырмасы

1. Ойын циклінің схемасын (3 кезең + tick) дәптерге салып, әр кезеңге 2 мысал жаз (мысалы, «жаңарту: кейіпкер секіреді, таймер азаяды»).
2. Үйдегі компьютерге Pygame орнатып, `python -m pygame.examples.chimp` мысалын іске қос.
3. 5-тапсырмадағы «Бағдаршамды» GitHub-тағы `pygame-lessons` репозиторийіне жүкте.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма:**

```python
import pygame

print("Pygame нұсқасы:", pygame.version.ver)
```

**2–3-тапсырмалар:**

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((640, 480))
pygame.display.set_caption("Менің ойыным")
clock = pygame.time.Clock()

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
            running = False
    screen.fill((20, 40, 90))
    pygame.display.flip()
    clock.tick(60)

pygame.quit()
```

**4-тапсырма:**

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        print(pygame.event.event_name(event.type), event.dict)
        if event.type == pygame.QUIT:
            running = False
    screen.fill((0, 0, 0))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

Күтілетін кесте: тінтуірді жылжыту — `MouseMotion`; тінтуірді басу/жіберу — `MouseButtonDown`/`MouseButtonUp`; перне — `KeyDown`/`KeyUp` (және `TextInput`); дөңгелек — `MouseWheel`; терезе жабу — `WindowClose`, `Quit`.

**5-тапсырма. Бағдаршам:**

```python
import pygame

COLORS = [(220, 0, 0), (240, 200, 0), (0, 180, 0)]

pygame.init()
screen = pygame.display.set_mode((400, 400))
pygame.display.set_caption("Бағдаршам")
clock = pygame.time.Clock()
frame = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    frame += 1
    color = COLORS[frame // 60 % 3]
    screen.fill(color)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**6–7-тапсырмалар. FPS және секундомер:**

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
clock = pygame.time.Clock()
start = pygame.time.get_ticks()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            start = pygame.time.get_ticks()
    seconds = (pygame.time.get_ticks() - start) // 1000
    pygame.display.set_caption(f"FPS: {clock.get_fps():.1f} | Уақыт: {seconds} с")
    screen.fill((40, 40, 40))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`tick(10)` → тақырыпта ≈10, `tick(120)` → ≈120 (компьютер қуатына байланысты). Оқушыларға: FPS неғұрлым жоғары болса, қозғалыс соғұрлым біркелкі, бірақ процессорға жүктеме көбірек.

**★ Түс таңдау ойыны:**

```python
import pygame

TARGET = (128, 0, 128)

pygame.init()
screen = pygame.display.set_mode((400, 400))
pygame.display.set_caption("Space басып, күлгінді ұста!")
clock = pygame.time.Clock()
value = 0          # 0..255: 0 — қызыл, 255 — көк
step = 3
stopped = False
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            stopped = not stopped
            if stopped:
                color = (255 - value, 0, value)
                diff = sum(abs(a - b) for a, b in zip(color, TARGET))
                print("Түс:", color, "| Айырма:", diff)
    if not stopped:
        value += step
        if value >= 255 or value <= 0:
            step = -step
            value = max(0, min(255, value))
    screen.fill((255 - value, 0, value))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

Мысал нәтиже:
```text
Түс: (129, 0, 126) | Айырма: 3
```

</details>
