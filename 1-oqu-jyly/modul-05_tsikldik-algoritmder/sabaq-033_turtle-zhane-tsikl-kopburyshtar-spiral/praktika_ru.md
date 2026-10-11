# Урок 33. Практические задания: turtle и цикл

Во всех заданиях должны быть `import turtle` и в конце `turtle.done()`. В сложных рисунках используй `t.speed(0)`.

## Базовый уровень

### Задание 1. Набор многоугольников
С помощью цикла нарисуй в одном окне: красный треугольник, зелёный квадрат, синий шестиугольник. Между фигурами перемещай черепашку с помощью `penup()` / `goto()` / `pendown()`.

### Задание 2. Универсальный многоугольник
Пользователь вводит число сторон `n`, длину `a` и цвет (`red`, `blue` и т. д.). Программа рисует этот многоугольник и закрашивает его.

### Задание 3. Золотая звезда
На красном фоне (`bgcolor`) нарисуй жёлтую закрашенную пятиконечную звезду.

### Задание 4. Квадратная спираль
Нарисуй квадратную спираль из 40 шагов, у которой сторона начинается с 5 и на каждом шаге увеличивается на 5.

## Продвинутый уровень

### Задание 5. Розетка
Нарисуй розетку, повернув шестиугольник 18 раз. При каждом повороте пусть меняется цвет (3 цвета, через `i % 3`).

### Задание 6. Увеличивающиеся квадраты
Нарисуй 10 квадратов размером 20, 40, 60, ..., 200 с центром в одной точке (концентрические квадраты).

**Подсказка:** перед рисованием каждого квадрата переведи черепашку в точку `goto(-size / 2, size / 2)`.

### Задание 7. Лестница
Пусть черепашка нарисует лестницу из 8 ступеней (каждая ступень: вверх 20, вправо 30).

## ★ Олимпиадная / творческая задача

### Задание 8. Узор в стиле казахского орнамента
С помощью вложенных циклов придумай симметричный узор (например, изобрази мотив «кошкар мүйіз» — «бараньи рога» — дугами окружности с помощью `circle(r, extent)`). Узор должен повторяться 4 или 8 раз и быть симметричным.

### Задание 9. Ночное небо
На чёрном фоне нарисуй 20 звёзд, расположенных не случайно, но в разных местах. Координаты звезды вычисляй из переменной цикла, например: `x = (i * 97) % 600 - 300`, `y = (i * 53) % 400 - 200`; размер `10 + i % 4 * 5`.

## Домашнее задание

1. Нарисуй многоугольники от 3 до 8 сторон в один ряд (слева направо).
2. Нарисуй розетку вокруг первой буквы своего имени (букву можно написать с помощью `t.write("А", font=("Arial", 40, "bold"))`).
3. Поэкспериментируй со спиральными узорами, меняя 2–3 параметра; подготовь код 2 самых понравившихся вариантов и свой ответ на вопрос «почему этот угол даёт такой узор?».

## Критерии оценивания (творческая работа — задание 8/9)

| Критерий | Баллы |
|---|---|
| Цикл (и вложенный цикл) использован правильно | 3 |
| Углы и размеры вычислены правильно (фигура замыкается) | 2 |
| Цвета, заливка, скорость использованы уместно | 2 |
| Узор симметричный, аккуратный | 2 |
| Оригинальность идеи | 1 |
| **Итого** | **10** |

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import turtle

t = turtle.Turtle()
t.penup()
t.goto(-250, 0)
t.pendown()
t.color("red")
for i in range(3):
    t.forward(100)
    t.left(120)

t.penup()
t.goto(-50, 0)
t.pendown()
t.color("green")
for i in range(4):
    t.forward(100)
    t.left(90)

t.penup()
t.goto(150, 0)
t.pendown()
t.color("blue")
for i in range(6):
    t.forward(60)
    t.left(60)
turtle.done()
```

**Задание 2**
```python
import turtle

n = int(input("Число сторон: "))
a = int(input("Длина: "))
col = input("Цвет (по-английски): ")
t = turtle.Turtle()
t.color(col)
t.begin_fill()
for i in range(n):
    t.forward(a)
    t.right(360 / n)
t.end_fill()
turtle.done()
```

**Задание 3**
```python
import turtle

turtle.bgcolor("red")
t = turtle.Turtle()
t.color("yellow")
t.penup()
t.goto(-100, 30)
t.pendown()
t.begin_fill()
for i in range(5):
    t.forward(200)
    t.right(144)
t.end_fill()
t.hideturtle()
turtle.done()
```

**Задание 4**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
for i in range(1, 41):
    t.forward(i * 5)
    t.right(90)
turtle.done()
```

**Задание 5**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
for k in range(18):
    if k % 3 == 0:
        t.color("red")
    elif k % 3 == 1:
        t.color("green")
    else:
        t.color("blue")
    for i in range(6):
        t.forward(80)
        t.right(60)
    t.right(20)
t.hideturtle()
turtle.done()
```

**Задание 6**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
for size in range(20, 201, 20):
    t.penup()
    t.goto(-size / 2, size / 2)
    t.pendown()
    for i in range(4):
        t.forward(size)
        t.right(90)
turtle.done()
```

**Задание 7**
```python
import turtle

t = turtle.Turtle()
t.penup()
t.goto(-150, -150)
t.pendown()
for step in range(8):
    t.left(90)
    t.forward(20)
    t.right(90)
    t.forward(30)
turtle.done()
```

**Задание 8 (образец)**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
t.pensize(3)
t.color("darkred")
for k in range(8):
    t.forward(80)
    t.circle(20, 270)      # завиток рога
    t.penup()
    t.goto(0, 0)
    t.pendown()
    t.setheading(k * 45 + 45)
t.hideturtle()
turtle.done()
```

**Задание 9**
```python
import turtle

turtle.bgcolor("black")
turtle.tracer(0)
t = turtle.Turtle()
t.hideturtle()
t.color("white")
for i in range(20):
    x = (i * 97) % 600 - 300
    y = (i * 53) % 400 - 200
    size = 10 + i % 4 * 5
    t.penup()
    t.goto(x, y)
    t.pendown()
    t.begin_fill()
    for j in range(5):
        t.forward(size)
        t.right(144)
    t.end_fill()
turtle.update()
turtle.done()
```

**Домашнее задание (1)**
```python
import turtle

t = turtle.Turtle()
t.speed(0)
x = -330
for n in range(3, 9):
    t.penup()
    t.goto(x, 0)
    t.pendown()
    for i in range(n):
        t.forward(40)
        t.left(360 / n)
    x += 110
turtle.done()
```
</details>
