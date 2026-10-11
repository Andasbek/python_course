# Сабақ 34. Pygame кітапханасын орнату. Ойын циклінің құрылымы.

## Тақырыптар

1. Pygame деген не?
2. Pygame-ді орнату
3. Ойын қалай жұмыс істейді? Кадр ұғымы
4. Tkinter мен Pygame айырмашылығы
5. Pygame бағдарламасының қаңқасы
6. Оқиғалар кезегі (event queue)
7. Ойын циклі әрекетте: кадр санауыш
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Pygame деген не?

**Pygame** — Python тілінде 2D ойындар мен мультимедиа бағдарламаларын жасауға арналған тегін кітапхана. Ол **SDL** (Simple DirectMedia Layer) атты C тіліндегі кітапхананың үстіне құрылған, сондықтан графика, дыбыс, пернетақта мен тінтуірмен жылдам жұмыс істейді.

| Pygame не істей алады | Модуль |
|---|---|
| Терезе құру, экранға салу | `pygame.display`, `pygame.draw` |
| Суреттерді жүктеу | `pygame.image` |
| Пернетақта, тінтуір, джойстик | `pygame.event`, `pygame.key`, `pygame.mouse` |
| Дыбыс пен музыка | `pygame.mixer` |
| Мәтін шығару | `pygame.font` |
| Уақытты басқару | `pygame.time` |
| Спрайттар мен қақтығыстар | `pygame.sprite`, `pygame.Rect` |

## 2. Pygame-ді орнату

Жоба үшін жеке папка мен виртуалды орта (venv) жасаймыз:

```bash
mkdir pygame-lessons
cd pygame-lessons
python -m venv venv
```

Виртуалды ортаны белсендіру:

```bash
# Windows:
venv\Scripts\activate
# macOS / Linux:
source venv/bin/activate
```

Pygame-ді орнату және тексеру:

```bash
pip install pygame
python -m pygame.examples.aliens
```

Соңғы команда Pygame-мен бірге келетін «Aliens» мысал ойынын іске қосады. Ойын ашылса — орнату сәтті.

Python ішінен нұсқаны тексеру:

```python
import pygame

print(pygame.version.ver)
```

Нәтижесі (нұсқа әртүрлі болуы мүмкін):
```text
pygame 2.6.1 (SDL 2.28.4, Python 3.12.3)
Hello from the pygame community. https://www.pygame.org/contribute.html
2.6.1
```

Алғашқы екі жолды Pygame импорт кезінде өзі шығарады — бұл қате емес.

> VS Code-та төменгі оң бұрышта `venv` интерпретаторы таңдалғанын тексеріңіз, әйтпесе `ModuleNotFoundError: No module named 'pygame'` қатесі шығады.

## 3. Ойын қалай жұмыс істейді? Кадр ұғымы

Кино секундына 24 суреттен (кадрдан) тұрады — көз оларды үздіксіз қозғалыс ретінде қабылдайды. Ойын да солай: бағдарлама экранды секундына 30–60 рет **қайта салады**. Әр сурет — **кадр** (frame), секундтағы кадр саны — **FPS** (frames per second).

Ойын бағдарламасы бір цикл ішінде әр кадрда үш нәрсені орындайды:

```text
          +---------------------------+
          |  1. ОҚИҒАЛАРДЫ ӨҢДЕУ       |  перне басылды ма? тінтуір? терезе жабылды ма?
          +-------------+-------------+
                        |
          +-------------v-------------+
          |  2. КҮЙДІ ЖАҢАРТУ          |  кейіпкерді жылжыту, ұпай санау, қақтығыс
          +-------------+-------------+
                        |
          +-------------v-------------+
          |  3. ЭКРАНҒА САЛУ           |  фонды тазалау, объектілерді салу, flip()
          +-------------+-------------+
                        |
                 clock.tick(60)  -> келесі кадр
```

**Аналогия: мультфильм салушы.** Әр парақта суретші: (1) режиссердің нұсқауын тыңдайды, (2) кейіпкердің жаңа орнын есептейді, (3) жаңа суретті салады. Парақтарды тез аударсақ — мультфильм шығады.

## 4. Tkinter мен Pygame айырмашылығы

| | Tkinter | Pygame |
|---|---|---|
| Цикл | `mainloop()` — дайын, жасырын | `while running:` — өзіміз жазамыз |
| Экран | Виджеттер өздері салынады | Әр кадрда бәрін өзіміз қайта саламыз |
| Оқиғалар | `command=`, `bind()` | Цикл ішінде `pygame.event.get()` |
| Мақсаты | Қосымшалар (формалар, батырмалар) | Ойындар, анимация |

## 5. Pygame бағдарламасының қаңқасы

Кез келген Pygame ойыны мына қаңқадан басталады — оны жатқа білу керек:

```python
import pygame

pygame.init()                                   # 1. Барлық модульдерді іске қосу
screen = pygame.display.set_mode((800, 600))    # 2. Терезе құру
pygame.display.set_caption("Менің алғашқы ойыным")
clock = pygame.time.Clock()                     # 3. Уақытты басқаратын сағат

running = True
while running:                                  # 4. ОЙЫН ЦИКЛІ
    # 4.1 Оқиғаларды өңдеу
    for event in pygame.event.get():
        if event.type == pygame.QUIT:           # терезенің «×» батырмасы
            running = False

    # 4.2 Күйді жаңарту (әзірге бос)

    # 4.3 Экранға салу
    screen.fill((30, 30, 60))                   # фонды бояу
    pygame.display.flip()                       # салынғанды экранға көрсету

    clock.tick(60)                              # секундына 60 кадрдан аспау

pygame.quit()                                   # 5. Pygame-ді дұрыс жабу
```

**Жол-жолымен талдау:**

| Жол | Түсіндірме |
|---|---|
| `pygame.init()` | Pygame-нің барлық ішкі модульдерін (дисплей, дыбыс, қаріп) дайындайды |
| `set_mode((800, 600))` | Ені 800, биіктігі 600 пиксель терезе құрып, оның бетін (`Surface`) қайтарады |
| `set_caption(...)` | Терезе тақырыбы |
| `pygame.time.Clock()` | Кадр жылдамдығын бақылайтын объект |
| `running = True` | Цикл жалауы (флаг): `False` болғанда ойын тоқтайды |
| `pygame.event.get()` | Соңғы кадрдан бергі барлық оқиғалар тізімін қайтарып, кезекті тазалайды |
| `pygame.QUIT` | Пайдаланушы терезені жапқысы келгенде пайда болатын оқиға |
| `screen.fill(...)` | Бүкіл экранды бір түспен бояйды (алдыңғы кадрды «өшіреді») |
| `pygame.display.flip()` | Жадта салынған кадрды экранға шығарады |
| `clock.tick(60)` | Цикл секундына 60 реттен жиі айналмауы үшін күтеді |
| `pygame.quit()` | Ресурстарды босатып, терезені жабады |

## 6. Оқиғалар кезегі (event queue)

Пайдаланушының әр әрекеті (перне басу, тінтуірді жылжыту, терезені жабу) **оқиға** ретінде кезекке түседі. Біз әр кадрда кезекті `pygame.event.get()` арқылы оқимыз. Егер оқымасақ, кезек толып, операциялық жүйе «Бағдарлама жауап бермейді» деп ескертеді.

Оқиғаларды көру үшін оларды консольге шығарайық:

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((400, 300))
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        print(pygame.event.event_name(event.type))
        if event.type == pygame.QUIT:
            running = False
    screen.fill((0, 0, 0))
    pygame.display.flip()
    clock.tick(30)
pygame.quit()
```

Нәтижесі (тінтуірді жылжытып, перне басып, терезені жапқанда):
```text
WindowShown
MouseMotion
MouseMotion
KeyDown
KeyUp
WindowClose
Quit
```

## 7. Ойын циклі әрекетте: кадр санауыш

Әр кадрда айнымалыны өзгертіп, циклдің шынымен қайталанатынын көрейік. Фон түсі әр кадрда ақырын өзгереді:

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("Кадр санауыш")
clock = pygame.time.Clock()

frame = 0
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    frame += 1                       # күйді жаңарту
    red = frame % 256                # 0..255 аралығында айналады

    screen.fill((red, 0, 100))
    pygame.display.flip()
    pygame.display.set_caption(f"Кадр: {frame}")
    clock.tick(60)

pygame.quit()
print("Барлығы кадр:", frame)
```

Нәтижесі (терезе 10 секунд ашық тұрса, шамамен):
```text
Барлығы кадр: 600
```

60 FPS × 10 секунд ≈ 600 кадр. `clock.tick(60)` жолын алып тастасаңыз, сан әлдеқайда көп болады — компьютер циклді мүмкіндігінше жылдам айналдырады.

## Жиі кездесетін қателер

**1. `pygame.init()` ұмытылған** — кейбір модульдер (мысалы, қаріп) `pygame.error: font not initialized` қатесін береді.

**2. Оқиғаларды өңдемеу**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((400, 300))
while True:
    screen.fill((0, 0, 0))
    pygame.display.flip()
```
Терезе «қатып» қалады және жабылмайды. Дұрысы: циклде `for event in pygame.event.get():` және `QUIT` тексерісі міндетті.

**3. `flip()` шақырылмаған** — салынған ештеңе экранда көрінбейді (терезе қара немесе ақ).

**4. `pygame.quit()` цикл ішінде**
```python
import pygame
pygame.init()
screen = pygame.display.set_mode((400, 300))
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
    screen.fill((0, 0, 0))   # pygame.error: display Surface quit
```
Дұрысы: циклде тек `running = False` қойып, `pygame.quit()`-ті циклден **кейін** шақыру.

**5. Файлды `pygame.py` деп атау** — `import pygame` сіздің файлыңызды импорттап, `AttributeError: module 'pygame' has no attribute 'init'` береді. Файлды басқаша атаңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Pygame | Python-да 2D ойындар жасауға арналған кітапхана |
| SDL | Pygame негізделген мультимедиа кітапханасы |
| Ойын циклі (game loop) | Ойын бойы қайталанатын «оқиғалар → жаңарту → салу» циклі |
| Кадр (frame) | Ойын циклінің бір айналымы, экрандағы бір сурет |
| FPS | Секундтағы кадр саны |
| Оқиға (event) | Пайдаланушы әрекеті туралы хабар (перне, тінтуір, жабу) |
| `Clock` | Кадр жиілігін басқаратын объект |
| `flip()` | Салынған кадрды экранға шығару |

## Ескерту

- `pygame` пен `pygame-ce` (community edition) — екі бөлек пакет, екеуі де `import pygame` арқылы импортталады. Оларды бір виртуалды ортаға қатар орнатпаңыз — бір-бірімен қақтығысады.
- Python-ның ең жаңа нұсқасы үшін pygame-нің дайын пакеттері (wheel) кешігіп шығуы мүмкін. `pip install pygame` бастапқы кодтан құрастыруға тырысып, қате берсе — Python-ның алдыңғы нұсқасын қолданыңыз.
- Импорт кезіндегі сәлемдесу хабарламасын жасыру үшін `import pygame`-ге дейін `PYGAME_HIDE_SUPPORT_PROMPT` орта айнымалысын орнатуға болады: `os.environ["PYGAME_HIDE_SUPPORT_PROMPT"] = "1"`.
- `pygame.quit()` тек Pygame модульдерін тоқтатады, бағдарламаны аяқтамайды. Цикл ішінен толық шығу үшін одан кейін `sys.exit()` шақырылады.
- Pygame ресми құжаттамасы: https://www.pygame.org/docs/
