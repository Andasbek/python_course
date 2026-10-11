# Сабақ 105. Резервтік сағат. Мереке және демалыс күндеріне түскен тақырыптарды өтеу.

## Тақырыптар

1. ОБП: шпаргалка
2. Tkinter: шпаргалка
3. Pygame: шпаргалка
4. Деректер: CSV, JSON, SQLite
5. Интернет: requests, API, BeautifulSoup
6. Деректерді талдау: Pandas, Matplotlib
7. Кәсіби әзірлеу
8. Жиі кездесетін қателер («топ-6»)
9. Негізгі ұғымдар

## 1. ОБП: шпаргалка

| Ұғым | Қысқаша | Синтаксис |
|---|---|---|
| Класс / объект | сызба / сол сызба бойынша жасалған зат | `class Car:` / `c = Car()` |
| Конструктор | объект құрылғанда шақырылады | `def __init__(self, ...):` |
| Инкапсуляция | ішкі деректерді жасыру | `self._balance`, `@property` |
| Мұрагерлік | ата-кластан қасиеттер алу | `class Bus(Car):`, `super().__init__()` |
| Полиморфизм | әр класта бір атты әдіс өзінше жұмыс істейді | `for s in shapes: s.area()` |
| Магиялық әдістер | Python операторларымен жұмыс | `__str__`, `__repr__`, `__len__`, `__eq__` |

```python
class Account:
    """Банк шоты: инкапсуляция және property."""

    def __init__(self, owner, balance=0):
        self.owner = owner
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Сома оң болуы керек")
        self._balance += amount

    def __str__(self):
        return f"{self.owner}: {self._balance} тг"


class SavingsAccount(Account):
    """Жинақ шоты: мұрагерлік және әдісті қайта анықтау."""

    def __init__(self, owner, balance=0, rate=0.1):
        super().__init__(owner, balance)
        self.rate = rate

    def add_interest(self):
        self.deposit(round(self.balance * self.rate))


acc = SavingsAccount("Айгерім", 10000)
acc.add_interest()
print(acc)
```

Нәтижесі:
```text
Айгерім: 11000 тг
```

## 2. Tkinter: шпаргалка

| Міндет | Код |
|---|---|
| Терезе | `root = tk.Tk(); root.title("..."); root.geometry("400x300")` |
| Виджеттер | `Label`, `Button`, `Entry`, `Text`, `Listbox`, `Checkbutton`, `Radiobutton`, `ttk.Combobox` |
| Орналастыру | `pack()` — кезекпен; `grid(row, column)` — кесте; `place(x, y)` — координата |
| Оқиға | `Button(..., command=func)`, `widget.bind("<Return>", func)` |
| Мәнді алу/қою | `entry.get()`, `label.config(text=...)`, `tk.StringVar()` |
| Диалогтар | `messagebox.showinfo/askyesno`, `filedialog.askopenfilename` |
| Бастау | `root.mainloop()` |

```python
import tkinter as tk
from tkinter import messagebox


def convert():
    try:
        usd = float(entry.get().replace(",", "."))
    except ValueError:
        messagebox.showwarning("Қате", "Сан енгізіңіз")
        return
    result.config(text=f"{usd * 500:.2f} тг")    # бағам шартты түрде 500


root = tk.Tk()
root.title("Валюта түрлендіргіш")
tk.Label(root, text="Доллар:").grid(row=0, column=0, padx=8, pady=8)
entry = tk.Entry(root)
entry.grid(row=0, column=1, padx=8)
tk.Button(root, text="Аудару", command=convert).grid(row=1, column=0, columnspan=2)
result = tk.Label(root, text="—", font=("Arial", 14))
result.grid(row=2, column=0, columnspan=2, pady=8)
root.mainloop()
```

## 3. Pygame: шпаргалка

```python
import pygame

pygame.init()
screen = pygame.display.set_mode((640, 480))
clock = pygame.time.Clock()
player = pygame.Rect(300, 400, 40, 40)
coin = pygame.Rect(100, 100, 20, 20)
score = 0
font = pygame.font.SysFont("arial", 24)

running = True
while running:                                     # 1. ойын циклі
    for event in pygame.event.get():               # 2. оқиғалар
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()                # 3. басқару
    if keys[pygame.K_LEFT]:
        player.x -= 5
    if keys[pygame.K_RIGHT]:
        player.x += 5
    if player.colliderect(coin):                   # 4. қақтығыс
        score += 1
        coin.x = (coin.x + 170) % 600
    screen.fill((30, 30, 50))                      # 5. салу
    pygame.draw.rect(screen, (80, 160, 255), player)
    pygame.draw.ellipse(screen, (255, 215, 0), coin)
    screen.blit(font.render(f"Ұпай: {score}", True, (255, 255, 255)), (10, 10))
    pygame.display.flip()
    clock.tick(60)                                 # 6. FPS
pygame.quit()
```

Есте сақтау: **оқиғалар → жаңарту → салу → `flip()` → `tick()`**. Спрайттар: `pygame.sprite.Sprite`, топтар: `pygame.sprite.Group`, топтық қақтығыс: `pygame.sprite.spritecollide`.

## 4. Деректер: CSV, JSON, SQLite

| Формат | Оқу | Жазу |
|---|---|---|
| CSV | `csv.DictReader(f)` | `csv.DictWriter(f, fieldnames=...)` |
| JSON | `json.load(f)` | `json.dump(obj, f, ensure_ascii=False, indent=2)` |
| SQLite | `conn.execute("SELECT ...", (p,)).fetchall()` | `with conn: conn.execute("INSERT ...", (...))` |

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE class (id INTEGER PRIMARY KEY, name TEXT);
CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT, score INTEGER,
                      class_id INTEGER REFERENCES class(id));
INSERT INTO class VALUES (1, '8А'), (2, '8Ә');
INSERT INTO student (name, score, class_id) VALUES
    ('Дана', 92, 1), ('Ерлан', 75, 1), ('Сәуле', 88, 2), ('Арман', 64, 2);
""")
query = """
    SELECT c.name, COUNT(*), ROUND(AVG(s.score), 1)
    FROM student AS s JOIN class AS c ON c.id = s.class_id
    GROUP BY c.name
    ORDER BY AVG(s.score) DESC
"""
for name, count, avg in conn.execute(query):
    print(f"{name}: {count} оқушы, орташа {avg}")
```

Нәтижесі:
```text
8А: 2 оқушы, орташа 83.5
8Ә: 2 оқушы, орташа 76.0
```

## 5. Интернет: requests, API, BeautifulSoup

```python
import requests

URL = "https://api.open-meteo.com/v1/forecast"
params = {"latitude": 43.24, "longitude": 76.89, "current_weather": "true"}  # Алматы
try:
    response = requests.get(URL, params=params, timeout=5)
    response.raise_for_status()
    weather = response.json()["current_weather"]
    print(f"Алматы: {weather['temperature']} °C, жел {weather['windspeed']} км/сағ")
except requests.RequestException as err:
    print("Деректерді алу мүмкін болмады:", err)
```

Нәтижесі (мысал):
```text
Алматы: 18.4 °C, жел 7.2 км/сағ
```

BeautifulSoup: `soup = BeautifulSoup(html, "html.parser")`, `soup.find("h1").text`, `soup.find_all("a")`, `a.get("href")`. Этика: `robots.txt`-ты тексеру, сұраныстар арасында кідіріс (`time.sleep(1)`), жеке деректерді жинамау. API кілттері — `.env`-те, `.gitignore`-да.

## 6. Деректерді талдау: Pandas, Matplotlib

```python
import pandas as pd

df = pd.DataFrame({
    "city": ["Алматы", "Астана", "Алматы", "Шымкент", "Астана"],
    "month": ["қаң", "қаң", "ақп", "қаң", "ақп"],
    "temp": [-5.2, -16.8, -3.1, 1.4, None],
})
df = df.dropna()                                   # тазалау: бос мәндер
summary = df.groupby("city")["temp"].mean().round(1).sort_values()
print(summary)
```

Нәтижесі:
```text
city
Астана    -16.8
Алматы     -4.2
Шымкент     1.4
Name: temp, dtype: float64
```

Matplotlib: `plt.plot` (сызықтық), `plt.bar` (бағаналы), `plt.pie` (дөңгелек), `plt.hist`, `plt.scatter`; безендіру: `title`, `xlabel`, `ylabel`, `legend`, `grid`; сақтау: `plt.savefig("chart.png")`.

## 7. Кәсіби әзірлеу

| Тақырып | Есте сақтау керек |
|---|---|
| Git | `git add .` → `git commit -m "..."` → `git push`; `git pull` — жұмыс басында |
| PEP 8 | `snake_case` функциялар, `CamelCase` кластар, 4 бос орын, мағыналы атаулар |
| Debugging | traceback-тың соңғы жолы; `print`/дебаггер; `logging.info/warning/error` |
| unittest | `class T(unittest.TestCase)`, `test_...`, `assertEqual`, `assertRaises`, `setUp` |
| README | не, кімге, орнату, қолдану, құрылым, автор |
| Қауіпсіздік | құпия сөздер мен кілттер кодта емес; SQL-де `?`; жеке деректерді жинамау |
| Code review | орын + салдары + ұсыныс; кодты талқылау, адамды емес |

## Жиі кездесетін қателер («топ-6»)

**1. `self`-ті ұмыту.**
```python
class Counter:
    def __init__(self):
        count = 0             # қате: жергілікті айнымалы, атрибут емес
```
Дұрысы: `self.count = 0`.

**2. `command=func()` — жақшамен** (Tkinter): функция бірден орындалады. Дұрысы: `command=func`.

**3. Pygame-де `pygame.display.flip()` жоқ** — экран жаңармайды; `clock.tick()` жоқ — ойын тым жылдам.

**4. `commit` ұмыту** (SQLite) — деректер сақталмайды. Дұрысы: `with conn:`.

**5. `timeout`-сыз сұраныс** (requests) — бағдарлама «қатып» қалады.

**6. Pandas-та нәтижені меншіктемеу.** `df.dropna()` бастапқы кестені өзгертпейді. Дұрысы: `df = df.dropna()`.

## Негізгі ұғымдар
| Ұғым | Түсіндірме |
|---|---|
| Шпаргалка | тақырыптың ең маңызды ұғымдары мен үлгілерінің қысқа жинағы |
| Ойын циклі | қайталанатын реттілік: оқиғалар → жаңарту → салу |
| Транзакция | толығымен орындалатын немесе мүлде орындалмайтын SQL операциялар тобы (`with conn:`) |
| `DataFrame` | Pandas кестесі: индексі бар жолдар және аталған бағандар |
| Біріктірілген есеп | бірден бірнеше технологияны қажет ететін есеп (мысалы, API + SQLite + Tkinter) |

## Ескерту

- Сеттерсіз `@property` атрибутты тек оқуға арналған етеді: `acc.balance = 5` `AttributeError` береді.
- Pygame-дегі `pygame.Rect` координаттарды бүтін сан ретінде сақтайды: бөлшек жылдамдықпен қозғалғанда орынды бөлек `float` түрінде сақтап, салу алдында `rect`-ке көшіріңіз.
- Pandas 2.x-та `df[df.x > 0]["y"] = 1` сияқты тізбекті меншіктеу бастапқы кестені өзгертпейді — `df.loc[df.x > 0, "y"] = 1` қолданыңыз.
- `requests`-те сұраныс параметрлерін URL жолын қолмен құрастырмай, `params=` (сөздік) арқылы беріңіз — кітапхана кириллица мен арнайы таңбаларды өзі дұрыс кодтайды.
- Python ресми оқулығы — https://docs.python.org/3/tutorial/; Pandas құжаттамасы — https://pandas.pydata.org/docs/; Pygame — https://www.pygame.org/docs/
