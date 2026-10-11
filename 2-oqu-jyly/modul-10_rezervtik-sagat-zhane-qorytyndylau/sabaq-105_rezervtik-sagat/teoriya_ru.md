# Урок 105. Резервный час. Восполнение тем, выпавших на праздничные и выходные дни.

## Темы

1. ООП: шпаргалка
2. Tkinter: шпаргалка
3. Pygame: шпаргалка
4. Данные: CSV, JSON, SQLite
5. Интернет: requests, API, BeautifulSoup
6. Анализ данных: Pandas, Matplotlib
7. Профессиональная разработка
8. Частые ошибки («топ-6»)
9. Основные понятия

## 1. ООП: шпаргалка

| Понятие | Кратко | Синтаксис |
|---|---|---|
| Класс / объект | чертёж / вещь, созданная по этому чертежу | `class Car:` / `c = Car()` |
| Конструктор | вызывается при создании объекта | `def __init__(self, ...):` |
| Инкапсуляция | сокрытие внутренних данных | `self._balance`, `@property` |
| Наследование | получение свойств от родительского класса | `class Bus(Car):`, `super().__init__()` |
| Полиморфизм | одноимённый метод в каждом классе работает по-своему | `for s in shapes: s.area()` |
| Магические методы | работа с операторами Python | `__str__`, `__repr__`, `__len__`, `__eq__` |

```python
class Account:
    """Банковский счёт: инкапсуляция и property."""

    def __init__(self, owner, balance=0):
        self.owner = owner
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Сумма должна быть положительной")
        self._balance += amount

    def __str__(self):
        return f"{self.owner}: {self._balance} тг"


class SavingsAccount(Account):
    """Накопительный счёт: наследование и переопределение метода."""

    def __init__(self, owner, balance=0, rate=0.1):
        super().__init__(owner, balance)
        self.rate = rate

    def add_interest(self):
        self.deposit(round(self.balance * self.rate))


acc = SavingsAccount("Айгерім", 10000)
acc.add_interest()
print(acc)
```

Результат:
```text
Айгерім: 11000 тг
```

## 2. Tkinter: шпаргалка

| Задача | Код |
|---|---|
| Окно | `root = tk.Tk(); root.title("..."); root.geometry("400x300")` |
| Виджеты | `Label`, `Button`, `Entry`, `Text`, `Listbox`, `Checkbutton`, `Radiobutton`, `ttk.Combobox` |
| Размещение | `pack()` — по очереди; `grid(row, column)` — таблица; `place(x, y)` — координаты |
| Событие | `Button(..., command=func)`, `widget.bind("<Return>", func)` |
| Получить/задать значение | `entry.get()`, `label.config(text=...)`, `tk.StringVar()` |
| Диалоги | `messagebox.showinfo/askyesno`, `filedialog.askopenfilename` |
| Запуск | `root.mainloop()` |

```python
import tkinter as tk
from tkinter import messagebox


def convert():
    try:
        usd = float(entry.get().replace(",", "."))
    except ValueError:
        messagebox.showwarning("Ошибка", "Введите число")
        return
    result.config(text=f"{usd * 500:.2f} тг")    # курс условно 500


root = tk.Tk()
root.title("Конвертер валют")
tk.Label(root, text="Доллары:").grid(row=0, column=0, padx=8, pady=8)
entry = tk.Entry(root)
entry.grid(row=0, column=1, padx=8)
tk.Button(root, text="Перевести", command=convert).grid(row=1, column=0, columnspan=2)
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
while running:                                     # 1. игровой цикл
    for event in pygame.event.get():               # 2. события
        if event.type == pygame.QUIT:
            running = False
    keys = pygame.key.get_pressed()                # 3. управление
    if keys[pygame.K_LEFT]:
        player.x -= 5
    if keys[pygame.K_RIGHT]:
        player.x += 5
    if player.colliderect(coin):                   # 4. столкновение
        score += 1
        coin.x = (coin.x + 170) % 600
    screen.fill((30, 30, 50))                      # 5. отрисовка
    pygame.draw.rect(screen, (80, 160, 255), player)
    pygame.draw.ellipse(screen, (255, 215, 0), coin)
    screen.blit(font.render(f"Очки: {score}", True, (255, 255, 255)), (10, 10))
    pygame.display.flip()
    clock.tick(60)                                 # 6. FPS
pygame.quit()
```

Запомните: **события → обновление → отрисовка → `flip()` → `tick()`**. Спрайты: `pygame.sprite.Sprite`, группы: `pygame.sprite.Group`, групповое столкновение: `pygame.sprite.spritecollide`.

## 4. Данные: CSV, JSON, SQLite

| Формат | Чтение | Запись |
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
INSERT INTO class VALUES (1, '8А'), (2, '8Б');
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
    print(f"{name}: учеников {count}, средний балл {avg}")
```

Результат:
```text
8А: учеников 2, средний балл 83.5
8Б: учеников 2, средний балл 76.0
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
    print(f"Алматы: {weather['temperature']} °C, ветер {weather['windspeed']} км/ч")
except requests.RequestException as err:
    print("Не удалось получить данные:", err)
```

Результат (пример):
```text
Алматы: 18.4 °C, ветер 7.2 км/ч
```

BeautifulSoup: `soup = BeautifulSoup(html, "html.parser")`, `soup.find("h1").text`, `soup.find_all("a")`, `a.get("href")`. Этика: проверять `robots.txt`, делать паузу между запросами (`time.sleep(1)`), не собирать персональные данные. API-ключи — в `.env`, а `.env` — в `.gitignore`.

## 6. Анализ данных: Pandas, Matplotlib

```python
import pandas as pd

df = pd.DataFrame({
    "city": ["Алматы", "Астана", "Алматы", "Шымкент", "Астана"],
    "month": ["янв", "янв", "фев", "янв", "фев"],
    "temp": [-5.2, -16.8, -3.1, 1.4, None],
})
df = df.dropna()                                   # очистка: пустые значения
summary = df.groupby("city")["temp"].mean().round(1).sort_values()
print(summary)
```

Результат:
```text
city
Астана    -16.8
Алматы     -4.2
Шымкент     1.4
Name: temp, dtype: float64
```

Matplotlib: `plt.plot` (линейный), `plt.bar` (столбчатый), `plt.pie` (круговой), `plt.hist`, `plt.scatter`; оформление: `title`, `xlabel`, `ylabel`, `legend`, `grid`; сохранение: `plt.savefig("chart.png")`.

## 7. Профессиональная разработка

| Тема | Что нужно запомнить |
|---|---|
| Git | `git add .` → `git commit -m "..."` → `git push`; `git pull` — в начале работы |
| PEP 8 | функции в `snake_case`, классы в `CamelCase`, 4 пробела, осмысленные имена |
| Отладка | последняя строка traceback; `print`/отладчик; `logging.info/warning/error` |
| unittest | `class T(unittest.TestCase)`, `test_...`, `assertEqual`, `assertRaises`, `setUp` |
| README | что, для кого, установка, использование, структура, автор |
| Безопасность | пароли и ключи не в коде; в SQL — `?`; не собирать персональные данные |
| Code review | место + последствие + предложение; обсуждаем код, а не человека |

## Частые ошибки («топ-6»)

**1. Забыли `self`.**
```python
class Counter:
    def __init__(self):
        count = 0             # ошибка: локальная переменная, а не атрибут
```
Правильно: `self.count = 0`.

**2. `command=func()` — со скобками** (Tkinter): функция выполняется сразу. Правильно: `command=func`.

**3. В Pygame нет `pygame.display.flip()`** — экран не обновляется; нет `clock.tick()` — игра идёт слишком быстро.

**4. Забыли `commit`** (SQLite) — данные не сохраняются. Правильно: `with conn:`.

**5. Запрос без `timeout`** (requests) — программа «зависает».

**6. В Pandas не присвоили результат.** `df.dropna()` не изменяет исходную таблицу. Правильно: `df = df.dropna()`.

## Основные понятия
| Понятие | Пояснение |
|---|---|
| Шпаргалка | краткий сборник самых важных понятий и шаблонов темы |
| Игровой цикл | повторяющаяся последовательность: события → обновление → отрисовка |
| Транзакция | группа SQL-операций, выполняемых целиком или не выполняемых вовсе (`with conn:`) |
| `DataFrame` | таблица Pandas: строки с индексом и именованные столбцы |
| Комплексная задача | задача, требующая нескольких технологий сразу (например, API + SQLite + Tkinter) |

## Примечание

- `@property` без сеттера делает атрибут доступным только для чтения: `acc.balance = 5` вызовет `AttributeError`.
- В Pygame `pygame.Rect` хранит координаты целыми числами: при движении с дробной скоростью храните позицию отдельно во `float` и копируйте её в `rect` перед отрисовкой.
- В Pandas 2.x цепочечные присваивания вида `df[df.x > 0]["y"] = 1` не меняют исходную таблицу — используйте `df.loc[df.x > 0, "y"] = 1`.
- В `requests` параметры запроса передавайте через `params=` (словарь), а не склеивайте строку URL вручную — библиотека сама правильно закодирует кириллицу и спецсимволы.
- Официальный учебник Python — https://docs.python.org/3/tutorial/; документация Pandas — https://pandas.pydata.org/docs/; Pygame — https://www.pygame.org/docs/
