# Урок 75. Чтение данных из CSV-файла. Просмотр, выборка и фильтрация данных.

## Темы

1. Набор данных (dataset)
2. `pd.read_csv()` — чтение CSV
3. Алгоритм исследования данных
4. Выборка (selection)
5. Фильтрация (filtering)
6. Частые ошибки
7. Основные понятия

## 1. Набор данных (dataset)

Сегодня мы работаем с учебным набором данных «Результаты областной олимпиады по информатике». Файл создаёт следующий код (запустите его один раз):

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2027)
n = 60
girls = ["Айгерім", "Дана", "Әлия", "Аружан", "Мадина", "Жансая"]
boys = ["Нұрлан", "Ержан", "Темірлан", "Санжар", "Алихан", "Бекзат"]
cities = ["Алматы", "Астана", "Шымкент", "Караганда", "Актобе"]
gender = rng.choice(["ж", "м"], n)
names = [f"{rng.choice(girls if g == 'ж' else boys)} {chr(65 + i % 26)}." for i, g in enumerate(gender)]
df = pd.DataFrame({
    "id": range(1, n + 1),
    "name": names,
    "gender": gender,
    "grade": rng.integers(7, 12, n),
    "city": rng.choice(cities, n, p=[0.35, 0.25, 0.2, 0.1, 0.1]),
    "task1": rng.integers(0, 101, n),
    "task2": rng.integers(0, 101, n),
    "task3": rng.integers(0, 101, n),
    "date": rng.choice(["2027-02-10", "2027-02-11"], n),
})
df["total"] = df[["task1", "task2", "task3"]].sum(axis=1)
df.to_csv("olympiad.csv", index=False, encoding="utf-8")
print("olympiad.csv создан:", df.shape)
```

Результат:
```text
olympiad.csv создан: (60, 10)
```

Начало файла (в текстовом редакторе):
```text
id,name,gender,grade,city,task1,task2,task3,date,total
1,Алихан A.,м,10,Шымкент,83,95,82,2027-02-11,260
2,Мадина B.,ж,7,Шымкент,79,97,100,2027-02-10,276
3,Айгерім C.,ж,8,Алматы,25,14,2,2027-02-10,41
...
```

(Значения сгенерированы случайно, но благодаря `seed=2027` у всех они одинаковые.)

## 2. `pd.read_csv()` — чтение CSV

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
print(df.shape)
print(df.head(3))
```

Одна строка — и весь файл (60 строк, 10 столбцов) превратился в таблицу! Заголовки автоматически становятся названиями столбцов, а числа — числовыми типами.

### Важные параметры

| Параметр | Для чего | Пример |
|---|---|---|
| `sep` | Разделитель (по умолчанию `,`) | `sep=";"` — файл, экспортированный для Excel |
| `encoding` | Кодировка | `"utf-8"`, `"utf-8-sig"`, `"cp1251"` (старые файлы Windows) |
| `usecols` | Только нужные столбцы | `usecols=["name", "total"]` |
| `index_col` | Столбец, который станет индексом | `index_col="id"` |
| `parse_dates` | Преобразование столбцов с датами в `datetime` | `parse_dates=["date"]` |
| `nrows` | Только первые n строк (проверка большого файла) | `nrows=100` |
| `header`, `names` | Задать названия, если заголовка нет | `header=None, names=["a", "b"]` |
| `decimal` | Десятичный разделитель | `decimal=","` → «3,5» |
| `dtype` | Явно указать тип | `dtype={"phone": str}` — чтобы не потерялись ведущие нули |
| `na_values` | Какие значения считать «пустыми» | `na_values=["—", "нет"]` |

```python
import pandas as pd

df = pd.read_csv("olympiad.csv", index_col="id", parse_dates=["date"],
                 usecols=["id", "name", "city", "total", "date"])
print(df.dtypes)
print(df.head(3))
```

Результат:
```text
name            object
city            object
date    datetime64[ns]
total            int64
dtype: object
          name     city       date  total
id
1    Алихан A.  Шымкент 2027-02-11    260
2    Мадина B.  Шымкент 2027-02-10    276
3   Айгерім C.   Алматы 2027-02-10     41
```

Можно читать и прямо из интернета: `pd.read_csv("https://.../data.csv")`. А файл с разделителем `;` и BOM: `pd.read_csv("students.csv", sep=";", encoding="utf-8-sig")`.

## 3. Алгоритм исследования данных

Получив новый набор данных, мы не бросаемся сразу «анализировать». Сначала — **знакомство** (EDA — Exploratory Data Analysis, разведочный анализ):

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

print("1. Размер:", df.shape)
print("2. Столбцы:", df.columns.tolist())
print("3. Начало и конец:")
print(df.head(3))
print(df.tail(2))
print("4. Случайные строки:")
print(df.sample(3, random_state=1))
print("5. Типы и пустые значения:")
df.info()
print("6. Числовая статистика:")
print(df[["task1", "task2", "task3", "total"]].describe().round(1))
print("7. Категории:")
print(df["city"].value_counts())
print("Классы:", sorted(df["grade"].unique().tolist()), "| число городов:", df["city"].nunique())
```

| Шаг | Метод | Вопрос |
|---|---|---|
| 1 | `shape` | Сколько строк, сколько столбцов? |
| 2 | `columns` | Какие данные есть? |
| 3 | `head`, `tail` | Как выглядят данные? Нет ли «мусора» в конце? |
| 4 | `sample` | Что в середине? |
| 5 | `info` | Правильные ли типы? Есть ли пустые значения? |
| 6 | `describe` | Разумен ли диапазон значений (например, нет ли балла > 100)? |
| 7 | `value_counts`, `unique` | Какие есть категории и как часто они встречаются? |

## 4. Выборка (selection)

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

names = df["name"]                              # один столбец — Series
short = df[["name", "city", "total"]]           # несколько столбцов — DataFrame
first10 = df.iloc[:10]                          # первые 10 строк
block = df.iloc[5:8, [1, 4, 9]]                 # строки 5–7, столбцы 1, 4, 9
by_label = df.loc[df.index[:3], "task1":"task3"]   # срез столбцов по названию (последний включается)
print(block)
print(by_label)
```

Самые высокие / низкие результаты — без сортировки:

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
print(df.nlargest(5, "total")[["name", "city", "total"]])     # лучшие 5
print(df.nsmallest(3, "task2")[["name", "task2"]])
```

## 5. Фильтрация (filtering)

Фильтрация — как в NumPy, с помощью **логической маски**.

### Одно условие

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

mask = df["city"] == "Алматы"
print(mask.head(3).tolist())        # последовательность True/False
almaty = df[mask]
print("Участников из Алматы:", len(almaty))
print("Больше 200:", len(df[df["total"] > 200]))
```

### Несколько условий: `&`, `|`, `~`

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

# ДЕВОЧКИ 9-го класса (должны выполняться оба условия) — каждое условие в скобках!
girls9 = df[(df["grade"] == 9) & (df["gender"] == "ж")]
# Астана ИЛИ Шымкент
south_capital = df[(df["city"] == "Астана") | (df["city"] == "Шымкент")]
# НЕ из Алматы
not_almaty = df[~(df["city"] == "Алматы")]
print(len(girls9), len(south_capital), len(not_almaty))
```

| Оператор | Значение | Аналог в Python (для простых значений) |
|---|---|---|
| `&` | и | `and` |
| `\|` | или | `or` |
| `~` | не | `not` |

### Удобные методы

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")

big_cities = df[df["city"].isin(["Алматы", "Астана", "Шымкент"])]   # одно из списка
middle = df[df["total"].between(120, 180)]                          # диапазон (с границами)
aig = df[df["name"].str.contains("Айгерім")]                        # внутри текста
starts_a = df[df["name"].str.startswith("А")]                       # начало текста
full_task1 = df[df["task1"] == df["task1"].max()]                   # равно максимальному значению
print(len(big_cities), len(middle), len(aig), len(starts_a), len(full_task1))
```

### `query()` — запись, похожая на SQL

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
min_total = 150
result = df.query("grade >= 10 and city == 'Алматы' and total > @min_total")   # @ — переменная Python
print(result[["name", "grade", "total"]])
```

`query` делает длинные условия более читаемыми. Сравните с SQL:

| SQL | Pandas |
|---|---|
| `SELECT name, total FROM t` | `df[["name", "total"]]` |
| `WHERE city = 'Алматы'` | `df[df["city"] == "Алматы"]` |
| `WHERE grade IN (9, 10)` | `df[df["grade"].isin([9, 10])]` |
| `WHERE total BETWEEN 100 AND 200` | `df[df["total"].between(100, 200)]` |
| `WHERE name LIKE '%ан%'` | `df[df["name"].str.contains("ан")]` |
| `ORDER BY total DESC LIMIT 5` | `df.sort_values("total", ascending=False).head(5)` |
| `SELECT COUNT(*)` | `len(df)` |

### Фильтрация + выборка + сортировка + сохранение — в одной цепочке

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
winners = (
    df[(df["grade"] >= 10) & (df["total"] >= 180)]       # фильтрация
    .sort_values("total", ascending=False)               # сортировка
    [["name", "grade", "city", "total"]]                 # выбор столбцов
)
winners.to_csv("winners.csv", index=False, encoding="utf-8-sig")
print(f"{len(winners)} победителей сохранено в файл winners.csv")
print(winners.head())
```

Результат:
```text
6 победителей сохранено в файл winners.csv
         name  grade     city  total
0   Алихан A.     10  Шымкент    260
19    Дана T.     10   Алматы    238
41  Бекзат P.     10   Актобе    216
46  Жансая U.     11   Алматы    211
26  Санжар A.     10  Шымкент    187
```

«Цепочка» в скобках (method chaining) — распространённый в Pandas стиль: каждый шаг на новой строке, читать легко.

### Изменение через фильтрацию

```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
df["prize"] = "участник"                                  # для всех
df.loc[df["total"] >= 200, "prize"] = "I место"           # для строк, удовлетворяющих условию
df.loc[df["total"].between(170, 199), "prize"] = "II место"
print(df["prize"].value_counts())
```

Результат:
```text
prize
участник    40
I место     12
II место     8
Name: count, dtype: int64
```

**Важно:** для изменения пишем **`df.loc[маска, "столбец"] = значение`**, а не `df[маска]["столбец"] = значение` (это изменяет копию и выдаёт предупреждение).

## Частые ошибки

**1. `FileNotFoundError`** — файл находится не в одной папке с программой. Проверьте текущую папку через `import os; print(os.getcwd())` или укажите полный путь.

**2. `UnicodeDecodeError`** — файл в другой кодировке. Попробуйте `encoding="cp1251"` или `encoding="utf-8-sig"`.

**3. Все данные в одном столбце** (`df.shape` → `(60, 1)`) — другой разделитель: `sep=";"`.

**4. Условия без скобок:**
```python
import pandas as pd
df = pd.DataFrame({"grade": [9, 10], "total": [150, 210]})
# Ошибка: df[df["grade"] == 10 & df["total"] > 200]   → & выполняется первым, TypeError/неверный результат
print(df[(df["grade"] == 10) & (df["total"] > 200)])   # Правильно: каждое условие в скобках
```

**5. Использование `and` / `or`** — `ValueError: The truth value of a Series is ambiguous`. В Pandas — `&`, `|`, `~`.

**6. `SettingWithCopyWarning`** — запись вида `df[df.x > 0]["y"] = 1`. Правильно: `df.loc[df.x > 0, "y"] = 1`.

**7. Числа прочитались как текст** (`dtype: object`) — в столбце есть значения вроде «—», «нет». `na_values=["—"]` или `pd.to_numeric(df["col"], errors="coerce")`.

## Основные понятия

| Понятие | Пояснение |
|---|---|
| Набор данных (dataset) | Совокупность данных, предназначенных для анализа |
| `read_csv` | Функция чтения CSV-файла в DataFrame |
| EDA | Разведочный анализ: первое знакомство с данными |
| `sample` | Случайные строки |
| Логическая маска | Series из `True`/`False` для фильтрации строк |
| `isin` / `between` | Условия вхождения в список / попадания в диапазон |
| `.str` | Набор методов для текстовых столбцов |
| `query` | Метод записи условия в виде текста |
| `nlargest` / `nsmallest` | n строк с наибольшими / наименьшими значениями |
| Method chaining | Стиль последовательного вызова методов цепочкой |

## Примечание

- `read_csv` сам определяет типы столбцов; задать их явно можно через `dtype={"phone": str}` — иначе телефоны и коды с ведущими нулями превратятся в числа и потеряют `0`.
- В условиях фильтрации используются `&`, `|`, `~` и скобки вокруг каждого условия; `and`/`or` вызовут `ValueError`.
- `df.query()` обращается к внешним переменным через `@`: `df.query("total > @limit")`; имена столбцов с пробелами берутся в обратные кавычки.
- Изменение через фильтр делайте одной операцией `df.loc[mask, "col"] = value`; запись `df[mask]["col"] = value` не изменит исходную таблицу (`SettingWithCopyWarning`).
- Для проверки на несколько значений `isin([...])` удобнее цепочки `|`; `between(a, b)` по умолчанию включает обе границы. Документация: https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html.
