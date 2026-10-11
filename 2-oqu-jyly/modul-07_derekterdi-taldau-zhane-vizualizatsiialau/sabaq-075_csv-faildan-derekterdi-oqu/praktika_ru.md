# Урок 75. Практические задания: чтение CSV, просмотр, выборка и фильтрация

> Данные: `olympiad.csv` (создайте кодом из раздела 1 теории). Столбцы: `id, name, gender, grade, city, task1, task2, task3, date, total`.

## Базовый уровень

### Задание 1. Знакомство с данными (EDA)
Прочитайте файл `olympiad.csv` и выполните 7 шагов из теории. Запишите ответы в тетрадь:
а) сколько участников, сколько столбцов?
б) есть ли пустые значения?
в) какая задача (task1/2/3) в среднем оказалась самой трудной?
г) из какого города больше / меньше всего участников?
д) количество девочек и мальчиков.

### Задание 2. Параметры `read_csv`
а) Прочитайте только столбцы `name`, `city`, `total` (`usecols`), индекс — `id`.
б) Прочитайте только первые 10 строк (`nrows`).
в) Прочитайте столбец `date` как дату (`parse_dates`) и проверьте его тип.
г) Правильно прочитайте файл `students.csv` из урока 61 с разделителем `;` и кодировкой `utf-8-sig`.

### Задание 3. Выборка
а) все данные участника в 15-й строке (`iloc`);
б) имя и общий балл первых 5 участников;
в) лучшие 5 и худшие 3 результата (`nlargest`, `nsmallest`);
г) те, кто получил за `task1` 100 или 0 баллов.

### Задание 4. Фильтрация
Напишите фильтры, отвечающие на следующие вопросы (для каждого выведите количество):
а) ученики 11-го класса;
б) девочки из Астаны;
в) участники из Шымкента или Актобе (`isin`);
г) те, чей общий балл от 120 до 180 (`between`);
д) не из Алматы, 10–11 класс, общий балл больше 200;
е) те, чьё имя начинается на букву «Д» (`str.startswith`).

### Задание 5. Протокол победителей
Правило награждения: общий балл ≥ 220 — I место, 190–219 — II место, 160–189 — III место. Заполните столбец `prize` с помощью `loc`, отсортируйте призёров (только `name`, `grade`, `city`, `total`, `prize`) по баллу и сохраните в файл `prizes.csv` (`utf-8-sig`). Выведите количество каждой награды.

## Продвинутый уровень

### Задание 6. `query` vs маска
Перепишите пункты д) и б) задания 4 с помощью `query()`. Какая запись легче читается? В одном пункте используйте переменную Python (`@`).

### Задание 7. «Стабильные» участники
Найдите тех, кто получил не менее 50 баллов за каждую из трёх задач. Подсказка: `(df[["task1", "task2", "task3"]] >= 50).all(axis=1)`. А как насчёт тех, кто получил 90+ хотя бы за одну задачу (`any`)?

### Задание 8. Отчёт по городам
Для каждого города с помощью цикла (пока без `groupby`): количество участников, средний общий балл, лучший участник. Соберите результат в DataFrame и отсортируйте по среднему баллу. (На уроке 77 мы сделаем это одной строкой с помощью `groupby`!)

## ★ Олимпиадная / творческая задача

### Задание 9. Интерактивный фильтр
Консольная программа запрашивает у пользователя город (или «все»), диапазон классов и минимальный балл, выводит таблицу подходящих участников и предлагает сохранить результат в файл. Обработайте ошибки ввода.

### Задание 10. Реальные открытые данные
Скачайте любой набор данных CSV (не менее 100 строк) с портала `data.egov.kz`, с Kaggle или из другого открытого источника, выполните 7 шагов EDA, сформулируйте не менее 3 осмысленных вопросов-фильтров и ответьте на них. Укажите источник данных и лицензию (урок 71!).

## Домашнее задание

1. Прочитайте файл `class.csv`, созданный в домашнем задании урока 74, составьте 5 разных вопросов-фильтров и ответьте на них.
2. Дополните таблицу соответствия Pandas и SQL (из теории) своими примерами.
3. Подумайте: какие «дефекты» могут быть в реальных данных? (пустая ячейка, неправильно написанный город, повторяющаяся строка...) Запишите не менее 5 примеров.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
print("Размер:", df.shape)
print("Пустые значения:", df.isna().sum().sum())
means = df[["task1", "task2", "task3"]].mean()
print(means.round(1))
print("Самая трудная задача:", means.idxmin())
counts = df["city"].value_counts()
print("Больше всего:", counts.idxmax(), "| меньше всего:", counts.idxmin())
print(df["gender"].value_counts())
```

**Задание 2**
```python
import pandas as pd

a = pd.read_csv("olympiad.csv", usecols=["id", "name", "city", "total"], index_col="id")
b = pd.read_csv("olympiad.csv", nrows=10)
c = pd.read_csv("olympiad.csv", parse_dates=["date"])
print(a.head(3))
print(b.shape)
print(c["date"].dtype)
# г) d = pd.read_csv("students.csv", sep=";", encoding="utf-8-sig")
```

**Задание 3**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
print(df.iloc[14])
print(df.loc[:4, ["name", "total"]])
print(df.nlargest(5, "total")[["name", "total"]])
print(df.nsmallest(3, "total")[["name", "total"]])
print(df[df["task1"].isin([0, 100])][["name", "task1"]])
```

**Задание 4**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
filters = {
    "а) 11 класс": df["grade"] == 11,
    "б) девочки Астаны": (df["city"] == "Астана") & (df["gender"] == "ж"),
    "в) Шымкент/Актобе": df["city"].isin(["Шымкент", "Актобе"]),
    "г) 120–180": df["total"].between(120, 180),
    "д) не из Алматы, 10–11, >200": (df["city"] != "Алматы") & (df["grade"] >= 10) & (df["total"] > 200),
    "е) на букву «Д»": df["name"].str.startswith("Д"),
}
for title, mask in filters.items():
    print(f"{title}: {mask.sum()}")
```

**Задание 5**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
df["prize"] = ""
df.loc[df["total"] >= 220, "prize"] = "I место"
df.loc[df["total"].between(190, 219), "prize"] = "II место"
df.loc[df["total"].between(160, 189), "prize"] = "III место"
prizes = (df[df["prize"] != ""]
          .sort_values("total", ascending=False)
          [["name", "grade", "city", "total", "prize"]])
prizes.to_csv("prizes.csv", index=False, encoding="utf-8-sig")
print(prizes["prize"].value_counts())
```

**Задание 6**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
limit = 200
g = df.query("city != 'Алматы' and grade >= 10 and total > @limit")
b = df.query("city == 'Астана' and gender == 'ж'")
print(len(g), len(b))
```

**Задание 7**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
tasks = df[["task1", "task2", "task3"]]
stable = df[(tasks >= 50).all(axis=1)]
stars = df[(tasks >= 90).any(axis=1)]
print("50+ за все задачи:", len(stable))
print(stable[["name", "task1", "task2", "task3"]])
print("90+ хотя бы за одну задачу:", len(stars))
```

**Задание 8**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
rows = []
for city in df["city"].unique():
    part = df[df["city"] == city]
    best = part.loc[part["total"].idxmax()]
    rows.append({"city": city, "count": len(part),
                 "avg_total": round(part["total"].mean(), 1),
                 "best": f"{best['name']} ({best['total']})"})
report = pd.DataFrame(rows).sort_values("avg_total", ascending=False)
print(report.to_string(index=False))
```

**Задание 9**
```python
import pandas as pd

df = pd.read_csv("olympiad.csv")
city = input("Город (или «все»): ").strip()
try:
    g_from, g_to = map(int, input("Диапазон классов (например, 9 11): ").split())
    min_total = int(input("Минимальный балл: "))
except ValueError:
    raise SystemExit("Ошибка ввода: ожидались числа")
mask = df["grade"].between(g_from, g_to) & (df["total"] >= min_total)
if city.lower() != "все":
    mask &= df["city"] == city
result = df[mask].sort_values("total", ascending=False)[["name", "grade", "city", "total"]]
if result.empty:
    print("Подходящих участников нет")
else:
    print(result.to_string(index=False))
    if input("Сохранить в файл? (да/нет): ").lower() == "да":
        result.to_csv("filter_result.csv", index=False, encoding="utf-8-sig")
        print("filter_result.csv сохранён")
```

**Задание 10** — творческое; оценивание: указаны источник данных и лицензия, выполнены 7 шагов EDA, вопросы-фильтры осмысленны и записаны правильно.

</details>
