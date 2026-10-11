# Сабақ 105. Практикалық тапсырмалар: қайталау және өтелетін тақырыптар

> Егер осы сабақта нақты бір өтпей қалған тақырып өтелсе, педагог сол тақырыптың сабағындағы (praktika.md) базалық тапсырмаларды береді. Төмендегі жинақ — барлық модульдерді қамтитын **жалпы қайталау** тапсырмалары. Әр оқушы диагностикадағы «сары/қызыл» модульдерінің тапсырмаларынан бастайды.

## Базалық деңгей

### 1-тапсырма. ОБП: «Мектеп асханасы»
`Dish(name, price, calories)` класын және `Order` класын жазыңыз:
- `Order.add(dish, qty=1)` — тағам қосу;
- `Order.total()` — жалпы бағасы;
- `Order.__len__` — тағамдар саны (қанша дана);
- `Order.__str__` — чек түрінде шығару.
`StudentOrder(Order)` ұрпақ класы 10% жеңілдік береді (`total()` әдісін `super()` арқылы қайта анықтаңыз).

Мысал шығару:
```text
Палау x1 = 900 тг
Компот x2 = 400 тг
Барлығы: 1300 тг, жеңілдікпен: 1170 тг
```

### 2-тапсырма. SQLite: «Үйірмелер»
`club(id, title)` және `member(id, name, age, club_id)` кестелерін құрып, кемінде 6 оқушы мен 3 үйірмені қосыңыз. Сұраныстар:
1. 13 жастан үлкен оқушылар, жасы бойынша кему ретімен.
2. Әр үйірмедегі оқушылар саны және орташа жасы (`JOIN` + `GROUP BY`).
3. Бір оқушының үйірмесін ауыстыру (`UPDATE`) және ең кіші оқушыны жою (`DELETE`).

### 3-тапсырма. JSON + CSV: «Кітаптар»
`books.json` файлында кітаптар тізімі бар (атауы, авторы, жылы, беті). Бағдарлама: (1) JSON-ды оқиды; (2) 1950 жылдан кейін шыққан кітаптарды бет саны бойынша сұрыптайды; (3) нәтижені `books.csv`-ге (`;` бөлгішімен, `utf-8-sig`) жазады.

```text
[{"title": "Абай жолы", "author": "М. Әуезов", "year": 1942, "pages": 1200},
 {"title": "Көшпенділер", "author": "І. Есенберлин", "year": 1976, "pages": 900},
 {"title": "Ақ кеме", "author": "Ш. Айтматов", "year": 1970, "pages": 180}]
```

### 4-тапсырма. Tkinter: «Дене салмағының индексі»
Терезеде бой (см) мен салмақ (кг) өрістері, «Есептеу» батырмасы және нәтиже белгісі болсын. ДСИ = салмақ / (бой/100)². Қате енгізуде — `messagebox` ескертуі. Нәтиже мәтінінің түсі: 18,5-тен аз — көк, 18,5–25 — жасыл, 25-тен көп — қызғылт сары.

## Кеңейтілген деңгей

### 5-тапсырма. API + Pandas: «Алматыдағы апталық температура»
Open-Meteo API-дан (`https://api.open-meteo.com/v1/forecast`, параметрлер: `latitude=43.24`, `longitude=76.89`, `daily=temperature_2m_max,temperature_2m_min`, `timezone=Asia/Almaty`) 7 күндік болжамды алып, Pandas DataFrame-ге айналдырыңыз. Шығарыңыз: ең жылы күн, орташа тәуліктік айырма (max − min). Бағаналы диаграмма салыңыз.

### 6-тапсырма. Pygame: «Тиындарды жина»
Теориядағы шпаргалка-ойынды жетілдіріңіз: (1) ойыншы жоғары-төмен де қозғалады және экраннан шықпайды; (2) тиын кездейсоқ орында пайда болады; (3) 30 секундтық таймер; уақыт біткенде «Ойын аяқталды: N ұпай» экраны.

### 7-тапсырма. Қатені табу (барлық модульдерден)
Әр үзіндідегі қатені тауып, түзетіңіз:

```python
# А
class Dog:
    def __init__(self, name):
        self.name = name

    def bark():
        return f"{self.name}: Ау-ау!"
```
```python
# Ә
import sqlite3
conn = sqlite3.connect("shop.db")
conn.execute("CREATE TABLE IF NOT EXISTS item (name TEXT, price INTEGER)")
name = "Қалам"
conn.execute(f"INSERT INTO item VALUES ('{name}', 150)")
```
```python
# Б
import pandas as pd
df = pd.DataFrame({"a": [1, None, 3]})
df.fillna(0)
print(df["a"].sum() / len(df))
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. «Үш технология — бір бағдарлама»
Бір шағын бағдарламада үш модульді біріктіріңіз: валюта бағамын API-дан алып (`https://open.er-api.com/v6/latest/USD`), әр сұраныстың нәтижесін SQLite-қа (`rate(date, currency, value)`) сақтаңыз, ал Tkinter терезесінде соңғы 10 жазбаны кестеде көрсетіңіз. Бағдарлама интернетсіз де ашылуы керек (тек ескі деректерді көрсетеді).

### 9-тапсырма. Мини-викторина құрастыру
Әр модуль бойынша 2 сұрақтан (барлығы 14) тұратын викторинаны JSON-да құрастырып, оны консольде ойнайтын бағдарлама жазыңыз (`QuizGame` класы — 94-сабақтағы үлгі). Бұл викторина 108-сабақтағы қорытынды ойында қолданылуы мүмкін.

## Үй тапсырмасы
1. Диагностикада «қызыл» болған модульдің теориясын (тиісті сабақтың teoriya.md) қайта оқып, оның базалық тапсырмаларының бірін орындаңыз.
2. Келесі сабаққа (жеке кеңес) кемінде 2 нақты сұрақ дайындаңыз: «Мен ... түсінбеймін», «Менің кодымда ... қате шығады, себебін білмеймін».

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.**
```python
class Dish:
    def __init__(self, name, price, calories):
        self.name = name
        self.price = price
        self.calories = calories


class Order:
    def __init__(self):
        self.items = {}                 # тағам -> саны

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
order.add(Dish("Палау", 900, 650))
order.add(Dish("Компот", 200, 90), 2)
print(order)
print(f"Барлығы: {Order.total(order)} тг, жеңілдікпен: {order.total()} тг")
print("Дана саны:", len(order))
```
Нәтижесі:
```text
Палау x1 = 900 тг
Компот x2 = 400 тг
Барлығы: 1300 тг, жеңілдікпен: 1170 тг
Дана саны: 3
```
`Order.total(order)` — ата-класс әдісін тікелей шақыру (жеңілдіксіз сома); талқылауға жақсы мысал.

**2-тапсырма.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.execute("PRAGMA foreign_keys = ON")
conn.executescript("""
CREATE TABLE club (id INTEGER PRIMARY KEY, title TEXT NOT NULL);
CREATE TABLE member (id INTEGER PRIMARY KEY, name TEXT, age INTEGER,
                     club_id INTEGER REFERENCES club(id));
INSERT INTO club VALUES (1, 'Python'), (2, 'Робототехника'), (3, '3D-модельдеу');
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
Нәтижесі:
```text
[('Дана', 16), ('Тимур', 15), ('Әлихан', 14)]
('Python', 3, 14.0)
('Робототехника', 2, 14.0)
('3D-модельдеу', 1, 11.0)
[('Әлихан', 1), ('Мадина', 3), ('Тимур', 2), ('Ерасыл', 2), ('Дана', 1)]
```

**3-тапсырма.**
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
print("Жазылды:", [b["title"] for b in modern])
```
Нәтижесі (берілген деректер үшін):
```text
Жазылды: ['Ақ кеме', 'Көшпенділер']
```

**4-тапсырма.**
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
        messagebox.showwarning("Қате", "Бой мен салмақты оң сан түрінде енгізіңіз")
        return
    bmi = weight / height ** 2
    color = "blue" if bmi < 18.5 else "green" if bmi <= 25 else "darkorange"
    result.config(text=f"ДСИ: {bmi:.1f}", fg=color)


root = tk.Tk()
root.title("Дене салмағының индексі")
tk.Label(root, text="Бой (см):").grid(row=0, column=0, sticky="w", padx=8, pady=4)
height_entry = tk.Entry(root)
height_entry.grid(row=0, column=1, padx=8)
tk.Label(root, text="Салмақ (кг):").grid(row=1, column=0, sticky="w", padx=8, pady=4)
weight_entry = tk.Entry(root)
weight_entry.grid(row=1, column=1, padx=8)
tk.Button(root, text="Есептеу", command=calculate).grid(row=2, column=0, columnspan=2, pady=6)
result = tk.Label(root, text="", font=("Arial", 14))
result.grid(row=3, column=0, columnspan=2, pady=6)
root.mainloop()
```

**5-тапсырма.**
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
    raise SystemExit(f"Деректер алынбады: {err}")

df = pd.DataFrame({"date": data["time"],
                   "tmax": data["temperature_2m_max"],
                   "tmin": data["temperature_2m_min"]})
df["range"] = df["tmax"] - df["tmin"]
warmest = df.loc[df["tmax"].idxmax()]
print(f"Ең жылы күн: {warmest['date']} ({warmest['tmax']} °C)")
print(f"Орташа тәуліктік айырма: {df['range'].mean():.1f} °C")

plt.bar(df["date"], df["tmax"], color="#ef6c00", label="Ең жоғары")
plt.bar(df["date"], df["tmin"], color="#1565c0", label="Ең төмен")
plt.title("Алматы: 7 күндік болжам")
plt.ylabel("°C")
plt.xticks(rotation=45)
plt.legend()
plt.tight_layout()
plt.show()
```

**6-тапсырма.**
```python
import random

import pygame

pygame.init()
W, H = 640, 480
screen = pygame.display.set_mode((W, H))
pygame.display.set_caption("Тиындарды жина")
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
        player.clamp_ip(screen.get_rect())        # экраннан шықпайды
        if player.colliderect(coin):
            score += 1
            coin.topleft = (random.randint(0, W - 20), random.randint(40, H - 20))
    screen.fill((30, 30, 50))
    if over:
        text = font.render(f"Ойын аяқталды: {score} ұпай", True, (255, 255, 255))
        screen.blit(text, text.get_rect(center=(W // 2, H // 2)))
    else:
        pygame.draw.rect(screen, (80, 160, 255), player)
        pygame.draw.ellipse(screen, (255, 215, 0), coin)
        screen.blit(font.render(f"Ұпай: {score}   Уақыт: {left}", True, (255, 255, 255)), (10, 8))
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
```

**7-тапсырма.** А — `bark` әдісінде `self` параметрі жоқ (`def bark(self):`). Ә — f-жолмен SQL (инъекция қаупі) және `commit` жоқ: `with conn: conn.execute("INSERT INTO item VALUES (?, ?)", (name, 150))`. Б — `fillna` нәтижесі меншіктелмеген: `df = df.fillna(0)` (немесе ниетке байланысты `df["a"].mean()` — бос мәнді елемейтін орташа; бастапқы код `4.0 / 3` береді, ал бұл «0 деп санау» мен «елемеу» арасындағы таңдауды талқылауға жақсы мысал).

**8, 9-тапсырмалар** — шығармашылық; 8-тапсырмада 96-сабақтағы `get_kzt_rate` (кэшпен) пен `Database` үлгілерін біріктіру күтіледі.
</details>
