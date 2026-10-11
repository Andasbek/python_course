# Урок 74. Практические задания: Pandas — Series и DataFrame

> Во всех заданиях `import pandas as pd`. Старайтесь решать без циклов, методами Pandas.

## Базовый уровень

### Задание 1. Series городов
Создайте из словаря `Series` с населением 6 городов Казахстана (тыс. человек): Алматы 2200, Астана 1500, Шымкент 1250, Караганда 500, Актобе 580, Тараз 370.
а) город с наибольшим и наименьшим населением (`idxmax`, `idxmin`);
б) города с населением более 1 миллиона;
в) отсортируйте население по возрастанию;
г) общее население всех городов и доля каждого города (%), с 1 знаком после запятой.

```text
Больше всего: Алматы, меньше всего: Тараз
Города-миллионники: ['Алматы', 'Астана', 'Шымкент']
...
Алматы     34.4
Астана     23.4
...
```

### Задание 2. Шаги за неделю
Количество шагов за одну неделю: Пн 8200, Вт 10500, Ср 6400, Чт 12000, Пт 9100, Сб 15300, Вс 4800.
а) среднее, медиана, самый активный день;
б) количество дней, когда выполнена норма 10 000 шагов;
в) отклонение каждого дня от нормы (`steps - 10000`);
г) сумма за выходные дни (Сб, Вс).

### Задание 3. Таблица класса
Создайте из словаря DataFrame для 6 учеников (столбцы: `name`, `gender` (м/ж), `grade`, `math`, `kazakh`, `english`). Затем:
а) выведите `shape`, `dtypes`, `head(3)`, `describe()`;
б) добавьте столбцы `total` (сумма трёх предметов) и `avg` (среднее, 1 знак после запятой);
в) отсортируйте по `avg` по убыванию и покажите только столбцы `name` и `avg`;
г) выведите имя ученика с самой высокой оценкой по английскому языку.

### Задание 4. DataFrame из результата API
Создайте DataFrame из приведённого ниже «ответа API» (список записей), переведите цену в тенге (1 £ = 650 ₸), добавьте столбец `price_kzt`, покажите 3 самые дорогие книги и сохраните таблицу в `books.csv`.

```python
books = [
    {"title": "A Light in the Attic", "price": 51.77, "rating": 3},
    {"title": "Tipping the Velvet", "price": 53.74, "rating": 1},
    {"title": "Soumission", "price": 50.10, "rating": 1},
    {"title": "Sharp Objects", "price": 47.82, "rating": 4},
    {"title": "Sapiens", "price": 54.23, "rating": 5},
]
```

### Задание 5. Упражнение на `loc` / `iloc`
Проиндексируйте таблицу из задания 3 с помощью `set_index("name")`. Затем:
а) все оценки одного ученика (`loc`);
б) оценки двух учеников по математике;
в) строки 2–4 таблицы и первые 2 столбца (`iloc`);
г) измените оценку одного ученика по казахскому языку и покажите результат.

## Продвинутый уровень

### Задание 6. SQLite → Pandas
Получите DataFrame из базы `school.db` урока 61 с помощью `pd.read_sql("SELECT * FROM students", conn)`. Выведите количество учеников по городам (`value_counts`), ученика с наивысшим баллом и средний балл.

### Задание 7. Арифметика Series
Цены товаров двух магазинов даны в виде Series (наборы товаров совпадают не полностью). Для каждого товара найдите, в каком магазине он дешевле, и разницу в ценах; товары, которые есть только в одном магазине (`NaN`), покажите отдельно (подсказка: `isna()`).

```python
import pandas as pd

shop_a = pd.Series({"хлеб": 230, "молоко": 520, "яйца": 950, "сыр": 2800})
shop_b = pd.Series({"молоко": 480, "хлеб": 250, "яйца": 990, "айран": 450})
```

### Задание 8. DataFrame погоды
Из словаря `daily` Open-Meteo урока 67 (столбцы — списки) сразу создайте `pd.DataFrame(data["daily"])`. Преобразуйте столбец `time` в дату с помощью `pd.to_datetime` и сделайте его индексом; добавьте столбец `range` (макс − мин); найдите день с наибольшим суточным перепадом. (Если нет интернета, используйте образцовые данные.)

## ★ Олимпиадная / творческая задача

### Задание 9. Мини-«Excel»
Консольная программа: пользователь вводит названия столбцов и строки, программа создаёт DataFrame и ждёт команды: `show`, `sort <столбец>`, `mean <столбец>`, `add <новый> = <столбец1> + <столбец2>`, `save <файл>`, `exit`.

### Задание 10. Личный бюджет
Внесите в DataFrame свои расходы за один месяц (дата, категория, сумма) — не менее 15 записей. Посчитайте общие расходы по категориям (`groupby` — предварительное знакомство с темой следующих уроков), 3 самые дорогие покупки и средний расход в день.

## Домашнее задание

1. Создайте DataFrame о своём (или вымышленном) классе — не менее 10 строк и 5 столбцов — и сохраните как `class.csv`: он понадобится на следующем уроке.
2. Письменно объясните, что означают значения 25%, 50%, 75% в результате `describe()`.
3. Найдите в интернете открытый набор данных (CSV): например, на портале `data.egov.kz` или на Kaggle. Сохраните ссылку.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import pandas as pd

pop = pd.Series({"Алматы": 2200, "Астана": 1500, "Шымкент": 1250,
                 "Караганда": 500, "Актобе": 580, "Тараз": 370})
print("Больше всего:", pop.idxmax(), ", меньше всего:", pop.idxmin())
print("Города-миллионники:", pop[pop > 1000].index.tolist())
print(pop.sort_values())
print("Всего:", pop.sum())
print((pop / pop.sum() * 100).round(1))
```

**Задание 2**
```python
import pandas as pd

steps = pd.Series([8200, 10500, 6400, 12000, 9100, 15300, 4800],
                  index=["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"])
print("Среднее:", round(steps.mean()), "Медиана:", steps.median(), "Самый активный:", steps.idxmax())
print("Норма выполнена:", (steps >= 10000).sum(), "дн.")
print(steps - 10000)
print("Выходные дни:", steps[["Сб", "Вс"]].sum())
```

**Задание 3**
```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия", "Темірлан"],
    "gender": ["ж", "м", "ж", "м", "ж", "м"],
    "grade": [9, 9, 9, 9, 9, 9],
    "math": [92, 78, 88, 95, 70, 84],
    "kazakh": [95, 82, 91, 76, 89, 80],
    "english": [88, 90, 79, 85, 93, 72],
})
print(df.shape)
print(df.dtypes)
print(df.head(3))
print(df.describe())
df["total"] = df[["math", "kazakh", "english"]].sum(axis=1)
df["avg"] = (df["total"] / 3).round(1)
print(df.sort_values("avg", ascending=False)[["name", "avg"]])
print("Лучший по английскому языку:", df.loc[df["english"].idxmax(), "name"])
```

**Задание 4**
```python
import pandas as pd

books = [
    {"title": "A Light in the Attic", "price": 51.77, "rating": 3},
    {"title": "Tipping the Velvet", "price": 53.74, "rating": 1},
    {"title": "Soumission", "price": 50.10, "rating": 1},
    {"title": "Sharp Objects", "price": 47.82, "rating": 4},
    {"title": "Sapiens", "price": 54.23, "rating": 5},
]
df = pd.DataFrame(books)
df["price_kzt"] = (df["price"] * 650).round()
print(df.sort_values("price_kzt", ascending=False).head(3))
df.to_csv("books.csv", index=False, encoding="utf-8-sig")
```

**Задание 5**
```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия", "Темірлан"],
    "math": [92, 78, 88, 95, 70, 84],
    "kazakh": [95, 82, 91, 76, 89, 80],
    "english": [88, 90, 79, 85, 93, 72],
}).set_index("name")
print(df.loc["Дана"])
print(df.loc[["Айгерім", "Ержан"], "math"])
print(df.iloc[1:4, :2])
df.loc["Нұрлан", "kazakh"] = 87
print(df.loc["Нұрлан"])
```

**Задание 6**
```python
import sqlite3
import pandas as pd

with sqlite3.connect("school.db") as conn:
    df = pd.read_sql("SELECT * FROM students", conn)
print(df["city"].value_counts())
print("Лучший:", df.loc[df["score"].idxmax(), "name"])
print("Средний балл:", round(df["score"].mean(), 1))
```

**Задание 7**
```python
import pandas as pd

shop_a = pd.Series({"хлеб": 230, "молоко": 520, "яйца": 950, "сыр": 2800})
shop_b = pd.Series({"молоко": 480, "хлеб": 250, "яйца": 990, "айран": 450})
diff = shop_a - shop_b
both = diff.dropna()
print("Разница (A − B):")
print(both)
print("Дешевле в A:", both[both < 0].index.tolist())
print("Дешевле в B:", both[both > 0].index.tolist())
print("Только в одном магазине:", diff[diff.isna()].index.tolist())
```

**Задание 8**
```python
import pandas as pd

daily = {"time": ["2027-03-01", "2027-03-02", "2027-03-03"],
         "temperature_2m_max": [5.1, 8.4, 3.0], "temperature_2m_min": [-4.2, -1.0, -6.5]}
df = pd.DataFrame(daily)
df["time"] = pd.to_datetime(df["time"])
df = df.set_index("time")
df["range"] = df["temperature_2m_max"] - df["temperature_2m_min"]
print(df)
print("Наибольший перепад:", df["range"].idxmax().date(), f"({df['range'].max():.1f} °C)")
```

**Задание 9** (краткий образец)
```python
import pandas as pd

cols = input("Столбцы (через запятую): ").split(",")
rows = []
while (line := input("Строка (пустая — завершить): ").strip()):
    rows.append([float(v) if v.replace(".", "", 1).lstrip("-").isdigit() else v
                 for v in line.split(",")])
df = pd.DataFrame(rows, columns=[c.strip() for c in cols])
while True:
    cmd = input("> ").split()
    if not cmd:
        continue
    if cmd[0] == "show":
        print(df)
    elif cmd[0] == "sort":
        df = df.sort_values(cmd[1])
    elif cmd[0] == "mean":
        print(df[cmd[1]].mean())
    elif cmd[0] == "add" and len(cmd) == 6:
        df[cmd[1]] = df[cmd[3]] + df[cmd[5]]
    elif cmd[0] == "save":
        df.to_csv(cmd[1], index=False)
    elif cmd[0] == "exit":
        break
```

**Задание 10**
```python
import pandas as pd

data = {
    "date": pd.date_range("2027-03-01", periods=15, freq="2D"),
    "category": ["еда", "транспорт", "развлечения", "еда", "книги", "еда", "транспорт", "одежда",
                 "еда", "развлечения", "транспорт", "еда", "книги", "еда", "транспорт"],
    "amount": [1500, 300, 2000, 1200, 3500, 900, 300, 8000, 1100, 2500, 300, 1400, 2800, 700, 300],
}
df = pd.DataFrame(data)
print(df.groupby("category")["amount"].sum().sort_values(ascending=False))
print(df.nlargest(3, "amount"))
print("Средний расход в день:", round(df["amount"].sum() / 30), "₸")
```

</details>
