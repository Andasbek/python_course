# Урок 105. Практические задания: повторение и восполняемые темы

> Если на этом уроке восполняется конкретная непройденная тема, педагог даёт базовые задания из практики того урока (praktika_ru.md). Сборник ниже — задания для **общего повторения**, охватывающие все модули. Каждый ученик начинает с заданий по модулям, которые на диагностике оказались «жёлтыми/красными».

## Базовый уровень

### Задание 1. ООП: «Школьная столовая»
Напишите класс `Dish(name, price, calories)` и класс `Order`:
- `Order.add(dish, qty=1)` — добавить блюдо;
- `Order.total()` — общая стоимость;
- `Order.__len__` — количество блюд (сколько штук);
- `Order.__str__` — вывод в виде чека.
Дочерний класс `StudentOrder(Order)` даёт скидку 10% (переопределите метод `total()` через `super()`).

Пример вывода:
```text
Плов x1 = 900 тг
Компот x2 = 400 тг
Итого: 1300 тг, со скидкой: 1170 тг
```

### Задание 2. SQLite: «Кружки»
Создайте таблицы `club(id, title)` и `member(id, name, age, club_id)`, добавьте не менее 6 учеников и 3 кружков. Запросы:
1. Ученики старше 13 лет, по убыванию возраста.
2. Количество учеников и средний возраст в каждом кружке (`JOIN` + `GROUP BY`).
3. Перевести одного ученика в другой кружок (`UPDATE`) и удалить самого младшего ученика (`DELETE`).

### Задание 3. JSON + CSV: «Книги»
В файле `books.json` есть список книг (название, автор, год, количество страниц). Программа: (1) читает JSON; (2) сортирует книги, вышедшие после 1950 года, по количеству страниц; (3) записывает результат в `books.csv` (разделитель `;`, кодировка `utf-8-sig`).

```text
[{"title": "Путь Абая", "author": "М. Ауэзов", "year": 1942, "pages": 1200},
 {"title": "Кочевники", "author": "И. Есенберлин", "year": 1976, "pages": 900},
 {"title": "Белый пароход", "author": "Ч. Айтматов", "year": 1970, "pages": 180}]
```

### Задание 4. Tkinter: «Индекс массы тела»
В окне должны быть поля для роста (см) и веса (кг), кнопка «Рассчитать» и метка с результатом. ИМТ = вес / (рост/100)². При неверном вводе — предупреждение `messagebox`. Цвет текста результата: меньше 18,5 — синий, 18,5–25 — зелёный, больше 25 — оранжевый.

## Продвинутый уровень

### Задание 5. API + Pandas: «Недельная температура в Алматы»
Получите 7-дневный прогноз из API Open-Meteo (`https://api.open-meteo.com/v1/forecast`, параметры: `latitude=43.24`, `longitude=76.89`, `daily=temperature_2m_max,temperature_2m_min`, `timezone=Asia/Almaty`) и преобразуйте его в Pandas DataFrame. Выведите: самый тёплый день, средний суточный перепад (max − min). Постройте столбчатую диаграмму.

### Задание 6. Pygame: «Собери монеты»
Доработайте игру-шпаргалку из теории: (1) игрок двигается также вверх-вниз и не выходит за пределы экрана; (2) монета появляется в случайном месте; (3) таймер на 30 секунд; когда время вышло — экран «Игра окончена: N очков».

### Задание 7. Найди ошибку (из всех модулей)
Найдите и исправьте ошибку в каждом фрагменте:

```python
# А
class Dog:
    def __init__(self, name):
        self.name = name

    def bark():
        return f"{self.name}: Гав-гав!"
```
```python
# Б
import sqlite3
conn = sqlite3.connect("shop.db")
conn.execute("CREATE TABLE IF NOT EXISTS item (name TEXT, price INTEGER)")
name = "Ручка"
conn.execute(f"INSERT INTO item VALUES ('{name}', 150)")
```
```python
# В
import pandas as pd
df = pd.DataFrame({"a": [1, None, 3]})
df.fillna(0)
print(df["a"].sum() / len(df))
```

## ★ Олимпиадная / творческая задача

### Задание 8. «Три технологии — одна программа»
Объедините три модуля в одной небольшой программе: получите курс валют из API (`https://open.er-api.com/v6/latest/USD`), сохраняйте результат каждого запроса в SQLite (`rate(date, currency, value)`), а в окне Tkinter показывайте последние 10 записей в таблице. Программа должна открываться и без интернета (показывает только старые данные).

### Задание 9. Составить мини-викторину
Составьте в JSON викторину из 2 вопросов по каждому модулю (всего 14) и напишите программу, которая проводит её в консоли (класс `QuizGame` — образец из 94-го урока). Эта викторина может быть использована в итоговой игре на 108-м уроке.

## Домашнее задание
1. Перечитайте теорию модуля, оказавшегося «красным» на диагностике (teoriya_ru.md соответствующего урока), и выполните одно из его базовых заданий.
2. Подготовьте к следующему уроку (индивидуальная консультация) не менее 2 конкретных вопросов: «Я не понимаю ...», «В моём коде возникает ошибка ..., не знаю почему».

## Для педагога: решения
<details><summary>Показать решения</summary>

**Задание 1.**
```python
class Dish:
    def __init__(self, name, price, calories):
        self.name = name
        self.price = price
        self.calories = calories


class Order:
    def __init__(self):
        self.items = {}                 # блюдо -> количество

    def add(self, dish, qty=1):
        self.items[dish] = self.items.get(dish, 0) + qty

    def total(self):
        return sum(d.price * q for d, q in self.items.items())

    def __len__(self):
        return sum(self.items.values())

    def __str__(self):
        lines = [f"{d.name} x{q} = {d.price * q} тг" for d, q in self.items.items()]
        return "\n".join(lines)


class StudentOrder(Order):
    DISCOUNT = 0.10

    def total(self):
        return round(super().total() * (1 - self.DISCOUNT))


order = StudentOrder()
order.add(Dish("Плов", 900, 650))
order.add(Dish("Компот", 200, 90), 2)
print(order)
print(f"Итого: {Order.total(order)} тг, со скидкой: {order.total()} тг")
print("Количество штук:", len(order))
```
Результат:
```text
Плов x1 = 900 тг
Компот x2 = 400 тг
Итого: 1300 тг, со скидкой: 1170 тг
Количество штук: 3
```
`Order.total(order)` — прямой вызов метода родительского класса (сумма без скидки); хороший пример для обсуждения.

**Задание 2.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
CREATE TABLE club (id INTEGER PRIMARY KEY, title TEXT NOT NULL);
CREATE TABLE member (id INTEGER PRIMARY KEY, name TEXT, age INTEGER,
                     club_id INTEGER REFERENCES club(id));
INSERT INTO club VALUES (1, 'Python'), (2, 'Робототехника'), (3, '3D-моделирование');
INSERT INTO member (name, age, club_id) VALUES
    ('Әлихан', 14, 1), ('Мадина', 12, 1), ('Тимур', 15, 2),
    ('Аружан', 11, 3), ('Ерасыл', 13, 2), ('Дана', 16, 1);
""")
print(conn.execute("SELECT name, age FROM member WHERE age > 13 ORDER BY age DESC").fetchall())
for row in conn.execute("""
        SELECT c.title, COUNT(m.id), ROUND(AVG(m.age), 1)
        FROM club c LEFT JOIN member m ON m.club_id = c.id
        GROUP BY c.id ORDER BY c.id"""):
    print(row)
with conn:
    conn.execute("UPDATE member SET club_id = ? WHERE name = ?", (3, "Мадина"))
    conn.execute("DELETE FROM member WHERE age = (SELECT MIN(age) FROM member)")
print(conn.execute("SELECT name, club_id FROM member ORDER BY id").fetchall())
```
Результат:
```text
[('Дана', 16), ('Тимур', 15), ('Әлихан', 14)]
('Python', 3, 14.0)
('Робототехника', 2, 14.0)
('3D-моделирование', 1, 11.0)
[('Әлихан', 1), ('Мадина', 3), ('Тимур', 2), ('Ерасыл', 2), ('Дана', 1)]
```

**Задание 3.**
```python
import csv
import json

with open("books.json", encoding="utf-8") as f:
    books = json.load(f)

modern = sorted((b for b in books if b["year"] > 1950), key=lambda b: b["pages"])

with open("books.csv", "w", newline="", encoding="utf-8-sig") as f:
    writer = csv.DictWriter(f, fieldnames=["title", "author", "year", "pages"], delimiter=";")
    writer.writeheader()
    writer.writerows(modern)
print("Записано:", [b["title"] for b in modern])
```
Результат (для данных из условия):
```text
Записано: ['Белый пароход', 'Кочевники']
```

**Задание 4.**
```python
import tkinter as tk
from tkinter import messagebox


def calculate():
    try:
        height = float(height_entry.get().replace(",", ".")) / 100
        weight = float(weight_entry.get().replace(",", "."))
        if height <= 0 or weight <= 0:
            raise ValueError
    except ValueError:
        messagebox.showwarning("Ошибка", "Введите рост и вес положительными числами")
        return
    bmi = weight / height ** 2
    color = "blue" if bmi < 18.5 else "green" if bmi <= 25 else "darkorange"
    result.config(text=f"ИМТ: {bmi:.1f}", fg=color)


root = tk.Tk()
root.title("Индекс массы тела")
tk.Label(root, text="Рост (см):").grid(row=0, column=0, sticky="w", padx=8, pady=4)
height_entry = tk.Entry(root)
height_entry.grid(row=0, column=1, padx=8)
tk.Label(root, text="Вес (кг):").grid(row=1, column=0, sticky="w", padx=8, pady=4)
weight_entry = tk.Entry(root)
weight_entry.grid(row=1, column=1, padx=8)
tk.Button(root, text="Рассчитать", command=calculate).grid(row=2, column=0, columnspan=2, pady=6)
result = tk.Label(root, text="", font=("Arial", 14))
result.grid(row=3, column=0, columnspan=2, pady=6)
root.mainloop()
```

**Задание 5.**
```python
import matplotlib.pyplot as plt
import pandas as pd
import requests

params = {"latitude": 43.24, "longitude": 76.89,
          "daily": "temperature_2m_max,temperature_2m_min", "timezone": "Asia/Almaty"}
try:
    data = requests.get("https://api.open-meteo.com/v1/forecast",
                        params=params, timeout=10).json()["daily"]
except (requests.RequestException, KeyError) as err:
    raise SystemExit(f"Данные не получены: {err}")

df = pd.DataFrame({"date": data["time"],
                   "tmax": data["temperature_2m_max"],
                   "tmin": data["temperature_2m_min"]})
df["range"] = df["tmax"] - df["tmin"]
warmest = df.loc[df["tmax"].idxmax()]
print(f"Самый тёплый день: {warmest['date']} ({warmest['tmax']} °C)")
print(f"Средний суточный перепад: {df['range'].mean():.1f} °C")

plt.bar(df["date"], df["tmax"], color="#ef6c00", label="Максимум")
plt.bar(df["date"], df["tmin"], color="#1565c0", label="Минимум")
plt.title("Алматы: прогноз на 7 дней")
plt.ylabel("°C")
plt.xticks(rotation=45)
plt.legend()
plt.tight_layout()
plt.show()
```

**Задание 6.**
```python
import random

import pygame

pygame.init()
W, H = 640, 480
screen = pygame.display.set_mode((W, H))
pygame.display.set_caption("Собери монеты")
clock = pygame.time.Clock()
font = pygame.font.SysFont("arial", 26)
player = pygame.Rect(300, 220, 40, 40)
coin = pygame.Rect(random.randint(0, W - 20), random.randint(40, H - 20), 20, 20)
score, GAME_TIME = 0, 30
start = pygame.time.get_ticks()
running, over = True, False
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    left = GAME_TIME - (pygame.time.get_ticks() - start) // 1000
    if left <= 0:
        over = True
    if not over:
        keys = pygame.key.get_pressed()
        player.x += (keys[pygame.K_RIGHT] - keys[pygame.K_LEFT]) * 5
        player.y += (keys[pygame.K_DOWN] - keys[pygame.K_UP]) * 5
        player.clamp_ip(screen.get_rect())        # не выходит за пределы экрана
        if player.colliderect(coin):
            score += 1
            coin.topleft = (random.randint(0, W - 20), random.randint(40, H - 20))
    screen.fill((30, 30, 50))
    if over:
        text = font.render(f"Игра окончена: {score} очков", True, (255, 255, 255))
        screen.blit(text, text.get_rect(center=(W // 2, H // 2)))
    else:
        pygame.draw.rect(screen, (80, 160, 255), player)
        pygame.draw.ellipse(screen, (255, 215, 0), coin)
        screen.blit(font.render(f"Очки: {score}   Время: {left}", True, (255, 255, 255)), (10, 8))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**Задание 7.** А — у метода `bark` нет параметра `self` (`def bark(self):`). Б — SQL через f-строку (риск инъекции) и нет `commit`: `with conn: conn.execute("INSERT INTO item VALUES (?, ?)", (name, 150))`. В — результат `fillna` не присвоен: `df = df.fillna(0)` (или, в зависимости от замысла, `df["a"].mean()` — среднее, игнорирующее пустые значения; исходный код даёт `4.0 / 3`, и это хороший пример для обсуждения выбора между «считать как 0» и «игнорировать»).

**Задания 8, 9** — творческие; в задании 8 ожидается объединение шаблонов `get_kzt_rate` (с кэшем) и `Database` из 96-го урока.
</details>
