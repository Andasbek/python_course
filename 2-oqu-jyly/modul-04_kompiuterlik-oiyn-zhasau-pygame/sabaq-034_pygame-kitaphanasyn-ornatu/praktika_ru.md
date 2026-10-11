# Урок 34. Практические задания: установка Pygame и игровой цикл

## Базовый уровень

### Задание 1. Установка и проверка
1. Создай папку `pygame-lessons`, создай и активируй виртуальное окружение.
2. Выполни `pip install pygame`.
3. Запусти игру-пример командой `python -m pygame.examples.aliens`.
4. В файле `check.py` выведи версию Pygame.

Пример вывода:
```text
Версия Pygame: 2.6.1
```

### Задание 2. Каркас игры
**Не подглядывая** в теорию, напиши каркас по памяти: окно 640×480, заголовок «Моя игра», цвет фона тёмно-синий `(20, 40, 90)`, 60 FPS, окно закрывается через «×».
**Подсказка:** сначала запиши на бумаге 5 шагов: init → set_mode → clock → цикл → quit.

### Задание 3. Выход по клавише Escape
Добавь в игру из задания 2 выход также по нажатию клавиши `Escape`.
**Подсказка:** `event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE`.

### Задание 4. Журнал событий
Напиши программу, которая выводит в консоль название каждого события. Подвигай мышь, нажми клавишу, щёлкни мышью — запиши в тетрадь в таблицу, какие события появляются.

| Действие | Название события |
|---|---|
| Движение мыши | MouseMotion |
| ... | ... |

## Продвинутый уровень

### Задание 5. «Светофор»
Цвет фона должен меняться каждые 60 кадров (≈ 1 секунда): красный → жёлтый → зелёный → красный ...
**Подсказка:** список цветов и выражение `frame // 60 % 3`.

### Задание 6. Показ FPS в заголовке
В заголовке окна должен отображаться реальный FPS: `FPS: 59.9`. Используй метод `clock.get_fps()`. Измени значение `clock.tick(60)` на 10, 30, 120 и сравни результаты.

### Задание 7. Секундомер
В заголовке должно отображаться количество секунд с начала игры (`Время: 12 с`). Клавиша пробел (`Space`) сбрасывает секундомер на ноль.
**Подсказка:** `pygame.time.get_ticks()` — миллисекунды с момента `init()`.

## ★ Олимпиадная / творческая задача

**«Игра на выбор цвета».** Цвет фона на каждом кадре постепенно меняется от красного к синему и обратно (градиентное «дыхание»). В момент нажатия игроком `Space` цвет останавливается, а в консоль выводится его значение RGB и то, насколько оно близко к «чистому фиолетовому» `(128, 0, 128)` (сумма разностей компонент). Наименьшая разница — победа!

## Домашнее задание

1. Нарисуй в тетради схему игрового цикла (3 этапа + tick) и напиши по 2 примера к каждому этапу (например, «обновление: персонаж прыгает, таймер уменьшается»).
2. Установи Pygame на домашний компьютер и запусти пример `python -m pygame.examples.chimp`.
3. Загрузи «Светофор» из задания 5 в репозиторий `pygame-lessons` на GitHub.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1:**

```python
import pygame

print("Версия Pygame:", pygame.version.ver)
```

**Задания 2–3:**

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((640, 480))
pygame.display.set_caption("Моя игра")
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

**Задание 4:**

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

Ожидаемая таблица: движение мыши — `MouseMotion`; нажатие/отпускание кнопки мыши — `MouseButtonDown`/`MouseButtonUp`; клавиша — `KeyDown`/`KeyUp` (и `TextInput`); колёсико — `MouseWheel`; закрытие окна — `WindowClose`, `Quit`.

**Задание 5. Светофор:**

```python
import pygame

COLORS = [(220, 0, 0), (240, 200, 0), (0, 180, 0)]

pygame.init()
screen = pygame.display.set_mode((400, 400))
pygame.display.set_caption("Светофор")
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

**Задания 6–7. FPS и секундомер:**

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
    pygame.display.set_caption(f"FPS: {clock.get_fps():.1f} | Время: {seconds} с")
    screen.fill((40, 40, 40))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

`tick(10)` → в заголовке ≈10, `tick(120)` → ≈120 (зависит от мощности компьютера). Для учеников: чем выше FPS, тем плавнее движение, но тем больше нагрузка на процессор.

**★ Игра на выбор цвета:**

```python
import pygame

TARGET = (128, 0, 128)

pygame.init()
screen = pygame.display.set_mode((400, 400))
pygame.display.set_caption("Нажми Space и поймай фиолетовый!")
clock = pygame.time.Clock()
value = 0          # 0..255: 0 — красный, 255 — синий
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
                print("Цвет:", color, "| Разница:", diff)
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

Пример результата:
```text
Цвет: (129, 0, 126) | Разница: 3
```

</details>
