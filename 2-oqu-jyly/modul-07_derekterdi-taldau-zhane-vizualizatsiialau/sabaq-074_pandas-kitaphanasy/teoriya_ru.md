# Урок 74. Библиотека Pandas. Структуры Series и DataFrame.

## Темы

1. Что такое Pandas?
2. Series
3. DataFrame
4. Работа со столбцами
5. Доступ к строкам: `loc` и `iloc`
6. Сортировка и агрегаты
7. Сохранение
8. Без Pandas и с Pandas
9. Частые ошибки
10. Основные понятия

## 1. Что такое Pandas?

**Pandas** — библиотека для анализа табличных данных. Название происходит от *panel data* (термин эконометрики). Её можно представить как «Excel внутри Python», но она гораздо мощнее: обрабатывает миллионы строк, работает с CSV, Excel, JSON, SQL.

```bash
pip install pandas
```

```python
import pandas as pd          # общепринятое имя — pd
import numpy as np

print(pd.__version__)
```

Две основные структуры Pandas:

| Структура | Размерность | Аналогия |
|---|---|---|
| `Series` | одномерная | Один столбец таблицы (с именем) |
| `DataFrame` | двумерная | Вся таблица (лист Excel, таблица SQL) |

```text
          Series                 DataFrame
       index  value         index  name     grade  score
         0    92.5            0    Айгерім    9    92.5
         1    78.0            1    Нұрлан    10    78.0
         2    88.0            2    Дана       9    88.0
                                    └─ каждый столбец — Series
```

## 2. Series

**Series** — индексированный одномерный массив: значения (массив NumPy) + **индекс** (метки).

### Создание

```python
import pandas as pd

s1 = pd.Series([92.5, 78.0, 88.0])                         # индекс автоматически: 0, 1, 2
s2 = pd.Series([92.5, 78.0, 88.0], index=["Айгерім", "Нұрлан", "Дана"], name="Балл")
s3 = pd.Series({"Алматы": 2200, "Астана": 1500, "Шымкент": 1250})   # из словаря: ключи → индекс

print(s1)
print(s2)
print(s3.index.tolist(), s3.values)
```

Результат:
```text
0    92.5
1    78.0
2    88.0
dtype: float64
Айгерім    92.5
Нұрлан     78.0
Дана       88.0
Name: Балл, dtype: float64
['Алматы', 'Астана', 'Шымкент'] [2200 1500 1250]
```

### Доступ к элементам

```python
import pandas as pd

pop = pd.Series({"Алматы": 2200, "Астана": 1500, "Шымкент": 1250, "Актобе": 580})

print(pop["Астана"])          # по метке (label)
print(pop.iloc[0])            # по позиции (position)
print(pop[["Алматы", "Актобе"]])
print(pop[pop > 1000])        # маска — как в NumPy
```

Результат:
```text
1500
2200
Алматы    2200
Актобе     580
dtype: int64
Алматы     2200
Астана     1500
Шымкент    1250
dtype: int64
```

### Операции и методы

```python
import pandas as pd

temps = pd.Series([-5.2, -3.1, 0.5, 2.8, 1.1, -1.4, -6.0],
                  index=["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"])

print((temps * 9 / 5 + 32).round(1).tolist())   # векторная операция
print("Среднее:", round(temps.mean(), 2))
print("Самый тёплый день:", temps.idxmax())    # возвращает метку индекса!
print(temps.sort_values().head(3))             # 3 самых холодных дня
```

Результат:
```text
[22.6, 26.4, 32.9, 37.0, 34.0, 29.5, 21.2]
Среднее: -1.61
Самый тёплый день: Чт
Вс   -6.0
Пн   -5.2
Вт   -3.1
dtype: float64
```

`idxmax()` — «понятный человеку» вариант `argmax()` из NumPy: возвращает не номер, а **название**.

**Выравнивание по индексу** — особое свойство Pandas: при сложении двух Series значения сопоставляются не по позиции, а по **меткам индекса**.

```python
import pandas as pd

jan = pd.Series({"Алматы": 10, "Астана": 7})
feb = pd.Series({"Астана": 5, "Алматы": 12, "Шымкент": 4})
print(jan + feb)
```

Результат:
```text
Алматы     22.0
Астана     12.0
Шымкент     NaN
dtype: float64
```

**`NaN`** (Not a Number) — знак «значения нет»: поскольку данных Шымкента за январь нет, сумма неизвестна. Пропуски обрабатывают методами `isna()`, `fillna()`, `dropna()`.

### `value_counts()` — подсчёт частоты

```python
import pandas as pd

cities = pd.Series(["Алматы", "Астана", "Алматы", "Шымкент", "Алматы", "Астана"])
print(cities.value_counts())
print("Разных городов:", cities.nunique())
```

Результат:
```text
Алматы     3
Астана     2
Шымкент    1
Name: count, dtype: int64
Разных городов: 3
```

## 3. DataFrame

**DataFrame** — двумерная таблица с именованными столбцами. Каждый столбец — `Series`, у всех столбцов общий индекс.

### 3 способа создания

```python
import pandas as pd

# 1. Из словаря столбцов (ключ — имя столбца, значение — список)
df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия"],
    "grade": [9, 10, 9, 11, 10],
    "city": ["Алматы", "Астана", "Шымкент", "Алматы", "Алматы"],
    "math": [92, 78, 88, 95, 70],
    "inf": [98, 85, 90, 91, 88],
})
print(df)

# 2. Из списка записей (словарей) — результат API/скрапинга именно такой!
books = [{"title": "Путь Абая", "price": 4500}, {"title": "Кочевники", "price": 6000}]
print(pd.DataFrame(books))

# 3. Из массива NumPy
import numpy as np
print(pd.DataFrame(np.arange(6).reshape(2, 3), columns=["a", "b", "c"]))
```

Результат:
```text
      name  grade     city  math  inf
0  Айгерім      9   Алматы    92   98
1   Нұрлан     10   Астана    78   85
2     Дана      9  Шымкент    88   90
3    Ержан     11   Алматы    95   91
4     Әлия     10   Алматы    70   88
       title  price
0  Путь Абая   4500
1  Кочевники   6000
   a  b  c
0  0  1  2
1  3  4  5
```

Можно читать и напрямую из SQLite: `pd.read_sql("SELECT * FROM students", conn)` — базы данных из V модуля теперь готовы к анализу!

### Исследование таблицы

```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия"],
    "grade": [9, 10, 9, 11, 10],
    "city": ["Алматы", "Астана", "Шымкент", "Алматы", "Алматы"],
    "math": [92, 78, 88, 95, 70],
    "inf": [98, 85, 90, 91, 88],
})

print(df.shape)               # (строки, столбцы)
print(df.columns.tolist())    # названия столбцов
print(df.dtypes)              # тип каждого столбца
print(df.head(2))             # первые 2 строки (по умолчанию — 5)
print(df.tail(1))             # последняя строка
df.info()                     # общая информация: типы, количество непустых значений
print(df.describe().round(1)) # статистика числовых столбцов
```

Результат (фрагмент):
```text
(5, 5)
['name', 'grade', 'city', 'math', 'inf']
name     object
grade     int64
city     object
math      int64
inf       int64
dtype: object
...
       grade  math   inf
count    5.0   5.0   5.0
mean     9.8  84.6  90.4
std      0.8  10.4   4.8
min      9.0  70.0  85.0
25%      9.0  78.0  88.0
50%     10.0  88.0  90.0
75%     10.0  92.0  91.0
max     11.0  95.0  98.0
```

| Метод / атрибут | Что показывает |
|---|---|
| `shape` | Количество строк и столбцов |
| `columns`, `index` | Названия столбцов, метки строк |
| `dtypes` | Типы столбцов (`object` — текст; в версии Pandas 3.x текстовые столбцы отображаются как `str`) |
| `head(n)`, `tail(n)` | Начало / конец |
| `info()` | Типы, непустые значения, память |
| `describe()` | count, mean, std, min, квартили, max |

`describe()` — самый быстрый способ познакомиться с новым набором данных: с первого взгляда видны среднее, разброс, крайние значения.

## 4. Работа со столбцами

```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия"],
    "grade": [9, 10, 9, 11, 10],
    "math": [92, 78, 88, 95, 70],
    "inf": [98, 85, 90, 91, 88],
})

print(df["math"].mean())                 # один столбец → Series
print(df[["name", "inf"]])               # несколько столбцов → DataFrame (двойные скобки!)

df["avg"] = (df["math"] + df["inf"]) / 2            # новый вычисляемый столбец
df["passed"] = df["avg"] >= 85                       # логический столбец
df = df.rename(columns={"inf": "informatics"})       # переименование
df = df.drop(columns=["grade"])                      # удаление столбца
print(df)
```

Результат:
```text
84.6
      name  inf
0  Айгерім   98
1   Нұрлан   85
2     Дана   90
3    Ержан   91
4     Әлия   88
      name  math  informatics   avg  passed
0  Айгерім    92           98  95.0    True
1   Нұрлан    78           85  81.5   False
2     Дана    88           90  89.0    True
3    Ержан    95           91  93.0    True
4     Әлия    70           88  79.0   False
```

## 5. Доступ к строкам: `loc` и `iloc`

| | `loc` | `iloc` |
|---|---|---|
| На что опирается | **Метка** (имя индекса, имя столбца) | **Позиция** (номер) |
| Пример | `df.loc[2, "math"]` | `df.iloc[2, 3]` |
| Срез | Последний **включается** | Последний не включается (как в Python) |

```python
import pandas as pd

df = pd.DataFrame({"math": [92, 78, 88], "inf": [98, 85, 90]},
                  index=["Айгерім", "Нұрлан", "Дана"])

print(df.loc["Нұрлан"])                  # одна строка → Series
print(df.loc["Дана", "inf"])             # одна ячейка
print(df.iloc[0])                        # первая строка
print(df.iloc[:2, 1].tolist())           # 2-й столбец первых 2 строк
df.loc["Дана", "math"] = 90              # изменение значения
print(df)
```

Результат:
```text
math    78
inf     85
Name: Нұрлан, dtype: int64
90
math    92
inf     98
Name: Айгерім, dtype: int64
[98, 85]
         math  inf
Айгерім    92   98
Нұрлан     78   85
Дана       90   90
```

Индексом можно сделать любой столбец: `df.set_index("name")`; обратно — `df.reset_index()`.

Фильтрация по условию:
```python
import pandas as pd
df = pd.DataFrame({"name": ["Айгерім", "Нұрлан", "Дана"], "math": [92, 78, 88]})
print(df[df["math"] > 80])
```

## 6. Сортировка и агрегаты

```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Айгерім", "Нұрлан", "Дана", "Ержан", "Әлия"],
    "city": ["Алматы", "Астана", "Шымкент", "Алматы", "Алматы"],
    "math": [92, 78, 88, 95, 70],
    "inf": [98, 85, 90, 91, 88],
})

print(df.sort_values("math", ascending=False).head(3)[["name", "math"]])
print(df.sort_values(["city", "inf"], ascending=[True, False])[["city", "name", "inf"]])
print(df[["math", "inf"]].mean())            # среднее каждого столбца (axis=0)
print(df[["math", "inf"]].mean(axis=1).tolist())  # среднее каждого ученика
print(df["city"].value_counts())
```

Результат (фрагмент):
```text
      name  math
3    Ержан    95
0  Айгерім    92
2     Дана    88
...
math    84.6
inf     90.4
dtype: float64
[95.0, 81.5, 89.0, 93.0, 79.0]
```

## 7. Сохранение

```python
import pandas as pd

df = pd.DataFrame({"name": ["Айгерім", "Нұрлан"], "math": [92, 78]})
df.to_csv("students_pandas.csv", index=False, encoding="utf-8-sig")   # index=False — не записывать столбец 0,1,2
df.to_json("students_pandas.json", orient="records", force_ascii=False, indent=2)
print("Сохранено")
```

Есть и `df.to_excel("file.xlsx")` (нужна библиотека `openpyxl`).

## 8. Без Pandas и с Pandas

Задача: «отсортировать учеников из Алматы по баллу и сохранить в CSV».

| Без Pandas (csv + цикл) | С Pandas |
|---|---|
| ~15 строк: открыть файл, `DictReader`, цикл, `int()`, `sorted(key=...)`, `DictWriter` | 1 строка |

```python
import pandas as pd

df = pd.DataFrame({"name": ["Айгерім", "Нұрлан", "Ержан"], "city": ["Алматы", "Астана", "Алматы"],
                   "score": [92.5, 78.0, 95.5]})
df[df["city"] == "Алматы"].sort_values("score", ascending=False).to_csv("almaty.csv", index=False)
print(open("almaty.csv", encoding="utf-8").read())
```

Результат:
```text
name,city,score
Ержан,Алматы,95.5
Айгерім,Алматы,92.5
```

## Частые ошибки

**1. Выбор нескольких столбцов в одинарных скобках:**
```python
import pandas as pd
df = pd.DataFrame({"a": [1], "b": [2], "c": [3]})
# Неправильно: df["a", "b"]  → KeyError
print(df[["a", "b"]])   # Правильно: нужно передать список — двойные скобки
```

**2. `KeyError: 'Math'`** — название столбца чувствительно к регистру и учитывает пробелы. Проверьте через `print(df.columns.tolist())`.

**3. Результат метода не сохранён:**
```python
import pandas as pd
df = pd.DataFrame({"x": [3, 1, 2]})
df.sort_values("x")          # df не изменится! Возвращается новая таблица и теряется
df = df.sort_values("x")     # Правильно: присвоить результат
print(df["x"].tolist())
```

**4. Путаница `loc` и `iloc`** — если индекс числовой (0, 1, 2), после сортировки он перестаёт совпадать с позициями. Метка — `loc`, позиция — `iloc`.

**5. Запись индекса при `to_csv`** — в файле появляется безымянный столбец «Unnamed: 0». `index=False`.

**6. Перебор строк циклом** (`for i in range(len(df))`) — медленно и чуждо стилю Pandas. Векторные операции: `df["avg"] = (df["a"] + df["b"]) / 2`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Pandas | Библиотека анализа табличных данных |
| `Series` | Одномерный массив с индексом (столбец таблицы) |
| `DataFrame` | Двумерная таблица с именованными столбцами |
| Индекс | Метки строк (числа или названия) |
| `NaN` | Отсутствующее (неизвестное) значение |
| `loc` / `iloc` | Доступ по метке / по позиции |
| `describe()` | Описательная статистика числовых столбцов |
| `value_counts()` | Частота встречаемости значений |
| `idxmax()` | Метка индекса наибольшего значения |

## Примечание

- `df["col"]` возвращает Series, а `df[["col"]]` — DataFrame из одного столбца.
- `loc` включает правую границу среза (`df.loc[0:2]` — 3 строки), `iloc` — нет (`df.iloc[0:2]` — 2 строки).
- Большинство методов (`sort_values`, `drop`, `rename`) возвращают **новый** объект; результат нужно присвоить: `df = df.sort_values("x")`.
- `df.to_csv("file.csv")` по умолчанию сохраняет индекс отдельным столбцом — обычно добавляют `index=False`.
- Цепочечное присваивание вида `df["a"][0] = 1` в pandas 2.x даёт предупреждение, а в режиме Copy-on-Write (по умолчанию в pandas 3.0) не работает вовсе — пишите `df.loc[0, "a"] = 1` (https://pandas.pydata.org/docs/).
