# Урок 16. Практические задания: параметры пера и закрашивание

> Циклы ещё не изучены — повторяющиеся команды пишите отдельными строками. Для закрашивания фигура должна быть замкнутой.

## Базовый уровень

### Задание 1. «Радужная линия»
Нарисуйте прямую линию толщиной 10, у которой цвет меняется каждые 60 пикселей: красный, оранжевый, жёлтый, зелёный, синий, фиолетовый.

### Задание 2. «Пунктир»
Как разметку посередине дороги, нарисуйте белую пунктирную линию — не на белом, а на сером фоне (`bgcolor("gray")`): 5 штрихов (каждый по 40), промежутки по 20 пикселей.

### Задание 3. «Два квадрата»
Нарисуйте два отдельных квадрата рядом друг с другом: первый красный (сторона 80), второй синий (сторона 80). Расстояние между ними — 40 пикселей, соединяющей линии быть не должно.

### Задание 4. «Закрашенный квадрат»
Нарисуйте квадрат (сторона 120) с чёрной обводкой толщиной 3 и жёлтой заливкой. В конце спрячьте черепашку.

### Задание 5. «Светофор»
Внутри чёрного прямоугольника (60 × 180) нарисуйте три разноцветных круга (красный, жёлтый, зелёный). Подсказка: `circle(25)` и `penup`/`pendown`.

## Продвинутый уровень

### Задание 6. «Выбери цвет»
Спросите у пользователя цвет заливки на английском (например, `green`) и нарисуйте треугольник, закрашенный этим цветом.

Пример ввода:
```text
Введите цвет (на английском): purple
```

### Задание 7. «Флаг Казахстана» (упрощённый)
Нарисуйте голубой (`"#00AFCA"`) прямоугольник (300 × 150) и в его центре — золотисто-жёлтый (`"#FEC50C"`) круг (солнце).

### Задание 8. «Мишень»
Нарисуйте мишень из трёх кругов с общим центром: большой — красный (радиус 90), средний — белый (60), маленький — красный (30). Подсказка: начните с большого круга, для следующего круга поднимите черепашку на 30 пикселей вверх (при поднятом пере).

## ★ Олимпиадная / творческая задача

### Задание 9. «Снеговик»
Нарисуйте снеговика из трёх белых кругов (уменьшаются снизу вверх), сделайте фон голубым, глаза нарисуйте с помощью `dot()`, нос — оранжевым треугольником.

### Задание 10. «Часть шахматной доски»
Шахматная доска 2 × 2: два чёрных и два белых квадрата (сторона 60), все с чёрной обводкой.

## Домашнее задание

1. Нарисуйте первую букву своей фамилии цветным пером толщиной 8.
2. Раскрасьте рисунок «Дом»: стены — коричневые, крыша — красная, окно — жёлтое.
3. Подумайте: что будет, если написать команду `begin_fill()` после того, как фигура уже нарисована? Проверьте и запишите результат.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.**

```python
import turtle

t = turtle.Turtle()
t.pensize(10)
t.penup()
t.back(180)
t.pendown()
t.pencolor("red")
t.forward(60)
t.pencolor("orange")
t.forward(60)
t.pencolor("yellow")
t.forward(60)
t.pencolor("green")
t.forward(60)
t.pencolor("blue")
t.forward(60)
t.pencolor("purple")
t.forward(60)
turtle.done()
```

**Задание 2.**

```python
import turtle

turtle.bgcolor("gray")
t = turtle.Turtle()
t.pencolor("white")
t.pensize(5)
t.forward(40)
t.penup()
t.forward(20)
t.pendown()
t.forward(40)
t.penup()
t.forward(20)
t.pendown()
t.forward(40)
t.penup()
t.forward(20)
t.pendown()
t.forward(40)
t.penup()
t.forward(20)
t.pendown()
t.forward(40)
turtle.done()
```

**Задание 3.**

```python
import turtle

t = turtle.Turtle()
t.pencolor("red")
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.penup()
t.forward(120)    # сторона 80 + промежуток 40
t.pendown()
t.pencolor("blue")
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
t.forward(80)
t.left(90)
turtle.done()
```

**Задание 4.**

```python
import turtle

t = turtle.Turtle()
t.pensize(3)
t.color("black", "yellow")
t.begin_fill()
t.forward(120)
t.left(90)
t.forward(120)
t.left(90)
t.forward(120)
t.left(90)
t.forward(120)
t.left(90)
t.end_fill()
t.hideturtle()
turtle.done()
```

**Задание 5.** Левый нижний угол прямоугольника — (0; 0). Окружность `circle(25)` рисуется выше черепашки, поэтому ставим черепашку в нижнюю точку окружности.

```python
import turtle

t = turtle.Turtle()
t.speed(0)
# корпус
t.color("black", "black")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(180)
t.left(90)
t.forward(60)
t.left(90)
t.forward(180)
t.left(90)
t.end_fill()
# зелёный (нижний) сигнал: центр (30; 30)
t.penup()
t.forward(30)
t.left(90)
t.forward(5)
t.right(90)
t.pendown()
t.color("green", "green")
t.begin_fill()
t.circle(25)
t.end_fill()
# жёлтый сигнал: на 60 выше
t.penup()
t.left(90)
t.forward(60)
t.right(90)
t.pendown()
t.color("yellow", "yellow")
t.begin_fill()
t.circle(25)
t.end_fill()
# красный сигнал
t.penup()
t.left(90)
t.forward(60)
t.right(90)
t.pendown()
t.color("red", "red")
t.begin_fill()
t.circle(25)
t.end_fill()
t.hideturtle()
turtle.done()
```

**Задание 6.**

```python
import turtle

tus = input("Введите цвет (на английском): ")
t = turtle.Turtle()
t.fillcolor(tus)
t.begin_fill()
t.forward(150)
t.left(120)
t.forward(150)
t.left(120)
t.forward(150)
t.left(120)
t.end_fill()
turtle.done()
```

**Задание 7.**

```python
import turtle

t = turtle.Turtle()
t.speed(0)
t.penup()
t.back(150)
t.right(90)
t.forward(75)
t.left(90)
t.pendown()
t.color("#00AFCA", "#00AFCA")
t.begin_fill()
t.forward(300)
t.left(90)
t.forward(150)
t.left(90)
t.forward(300)
t.left(90)
t.forward(150)
t.left(90)
t.end_fill()
# солнце: центр в середине флага (0; 0), радиус 35
t.penup()
t.forward(150)
t.left(90)
t.forward(40)
t.right(90)
t.pendown()
t.color("#FEC50C", "#FEC50C")
t.begin_fill()
t.circle(35)
t.end_fill()
t.hideturtle()
turtle.done()
```

**Задание 8.** Чтобы центр был в точке (0; 0), окружность радиуса r начинаем из точки (0; −r).

```python
import turtle

t = turtle.Turtle()
t.speed(0)
t.penup()
t.right(90)
t.forward(90)
t.left(90)
t.pendown()
t.color("red", "red")
t.begin_fill()
t.circle(90)
t.end_fill()
t.penup()
t.left(90)
t.forward(30)
t.right(90)
t.pendown()
t.color("white", "white")
t.begin_fill()
t.circle(60)
t.end_fill()
t.penup()
t.left(90)
t.forward(30)
t.right(90)
t.pendown()
t.color("red", "red")
t.begin_fill()
t.circle(30)
t.end_fill()
t.hideturtle()
turtle.done()
```

**Задание 9** (одно из возможных решений).

```python
import turtle

turtle.bgcolor("skyblue")
t = turtle.Turtle()
t.speed(0)
t.color("white", "white")
# нижний шар (радиус 70)
t.penup()
t.right(90)
t.forward(200)
t.left(90)
t.pendown()
t.begin_fill()
t.circle(70)
t.end_fill()
# средний шар (радиус 50)
t.penup()
t.left(90)
t.forward(140)
t.right(90)
t.pendown()
t.begin_fill()
t.circle(50)
t.end_fill()
# голова (радиус 35)
t.penup()
t.left(90)
t.forward(100)
t.right(90)
t.pendown()
t.begin_fill()
t.circle(35)
t.end_fill()
# глаза
t.penup()
t.left(90)
t.forward(45)
t.left(90)
t.forward(12)
t.dot(8, "black")
t.back(24)
t.dot(8, "black")
# нос
t.forward(12)
t.left(90)
t.forward(15)
t.left(90)
t.pendown()
t.color("orange", "orange")
t.begin_fill()
t.forward(25)
t.left(160)
t.forward(25)
t.end_fill()
t.hideturtle()
turtle.done()
```

**Задание 10.**

```python
import turtle

t = turtle.Turtle()
t.speed(0)
# квадрат 1 (чёрный)
t.color("black", "black")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.end_fill()
# квадрат 2 (белый) — справа
t.forward(60)
t.color("black", "white")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.end_fill()
# квадрат 3 (чёрный) — справа вверху
t.left(90)
t.forward(60)
t.right(90)
t.color("black", "black")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.end_fill()
# квадрат 4 (белый) — слева вверху
t.back(60)
t.color("black", "white")
t.begin_fill()
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.forward(60)
t.left(90)
t.end_fill()
t.hideturtle()
turtle.done()
```

**Домашнее задание, вопрос 3.** Если `begin_fill()` написать после фигуры, заливка не будет начата и фигура не закрасится (заливка применяется только к пути между `begin_fill` и `end_fill`).

</details>
