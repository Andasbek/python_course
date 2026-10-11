# Урок 78. Практические задания: линейные и столбчатые диаграммы

Каждое задание выполняйте в отдельном файле (`t1.py`, `t2.py`, ...). У каждого графика обязательно должны быть заголовок и подписи осей. Каждый график также сохраните в `png`-файл — они понадобятся для отчёта на уроке 81.

## Базовый уровень

**Задание 1. Шагомер.** Ерлан всю неделю записывал количество шагов со своего смартфона:

| Пн | Вт | Ср | Чт | Пт | Сб | Вс |
|---|---|---|---|---|---|---|
| 6200 | 8100 | 5400 | 9800 | 7300 | 12500 | 3900 |

Постройте линейный график с круглыми маркерами. Отметьте рекомендуемый врачами уровень 10 000 шагов с помощью `plt.axhline(10000, color="red", linestyle="--")`. Сколько дней Ерлан выполнил норму? (посчитайте кодом)

**Задание 2. Опрос.** В классе провели опрос «На какой платформе ты проводишь больше времени?»: YouTube — 11, TikTok — 9, Instagram — 6, Telegram — 4, Игры — 8. Постройте столбчатую диаграмму и подпишите значение над каждым столбцом (`bar_label`).

**Задание 3. Курс валюты.** Курс USD (тенге) за месяц по неделям:
`[502, 505, 498, 510, 515, 512, 520, 518]`. Номера недель 1–8. Постройте линейный график; отметьте самую высокую точку с помощью `plt.annotate` или `plt.text` (например: `plt.text(7, 520, "Максимум")`).

**Задание 4. Горизонтальный рейтинг.** Некоторые из самых высоких гор Казахстана (приблизительно, м): Хан-Тенгри — 6995, Талгар — 4979, Белуха — 4506, Бесбакан — 4380, Музтау (Сайрам) — 4238. Постройте горизонтальную столбчатую диаграмму так, чтобы самая высокая гора была сверху.

## Продвинутый уровень

**Задание 5. Сравнение городов.** К температурам Алматы и Астаны из теории добавьте Шымкент:
`shymkent = [-1.0, 1.0, 7.5, 14.5, 20.0, 25.5, 28.0, 26.5, 20.5, 13.0, 5.5, 0.5]`.
Три линии, у каждой свой цвет и маркер, легенда, сетка (grid). Вычислите кодом: в каком месяце разница температур между Алматы и Астаной наибольшая?

**Задание 6. Диаграммы выручки (`kitap_dukeni.csv`).**
а) Столбчатая диаграмма выручки по филиалам (в порядке убывания);
б) Линейный график выручки по дням недели (порядок дней Пн→Вс должен сохраниться: `groupby("kun", sort=False)`);
в) Сохраните оба графика как `filial.png`, `kunder.png`.

**Задание 7. Сгруппированные столбцы.** С помощью `pivot_table` получите таблицу выручки филиал × категория и одной строкой `kesteh.plot(kind="bar")` постройте сгруппированную столбчатую диаграмму. Затем постройте точно такой же график на чистом Matplotlib (`plt.bar` + сдвиг).

## ★ Олимпиадная / творческая задача

**Задание 8. Два графика в одном окне.** С помощью `plt.subplot(1, 2, 1)` и `plt.subplot(1, 2, 2)` нарисуйте в левой части одной фигуры линию шагов, а в правой — столбцы по дням «норма выполнена/не выполнена» (зелёным/красным цветом). Подсказка: создайте список цветов с условием: `["green" if q >= 10000 else "red" for q in qadam]`.

**Задание 9. Накопленный рост.** Вычислите **накопленную сумму** шагов из задания 1 (сколько всего пройдено к каждому дню) (подсказка: `itertools.accumulate` или `cumsum()` в Pandas) и покажите на одном графике с двумя осями Y: слева — ежедневные столбцы, справа — накопленная линия (`ax2 = ax.twinx()`).

## Домашнее задание

1. По своим данным с уроков 76–77 постройте не менее одного линейного и одной столбчатой диаграммы.
2. Под каждым графиком (в README или в комментарии) ответьте 1–2 предложениями на вопрос «Что видно на графике?».
3. Сохраните рисунки в папку `data-analysis/images/` и загрузите на GitHub.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import matplotlib.pyplot as plt

kunder = ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"]
qadam = [6200, 8100, 5400, 9800, 7300, 12500, 3900]

plt.plot(kunder, qadam, marker="o", color="purple")
plt.axhline(10000, color="red", linestyle="--", label="Норма: 10 000")
plt.title("Количество шагов Ерлана за неделю")
plt.xlabel("День недели")
plt.ylabel("Шаги")
plt.legend()
plt.savefig("qadam.png", dpi=150, bbox_inches="tight")
plt.show()

norma = len([q for q in qadam if q >= 10000])
print("Дней с выполненной нормой:", norma)
```
Ответ: 1 день (Сб).

**Задание 2**
```python
import matplotlib.pyplot as plt

platformalar = ["YouTube", "TikTok", "Instagram", "Telegram", "Игры"]
sany = [11, 9, 6, 4, 8]

b = plt.bar(platformalar, sany, color="coral")
plt.bar_label(b)
plt.title("На какой платформе ученики проводят больше времени")
plt.ylabel("Количество учеников")
plt.savefig("platforma.png", dpi=150, bbox_inches="tight")
plt.show()
```

**Задание 3**
```python
import matplotlib.pyplot as plt

aptalar = list(range(1, 9))
usd = [502, 505, 498, 510, 515, 512, 520, 518]

plt.plot(aptalar, usd, marker="o")
max_i = usd.index(max(usd))
plt.annotate("Максимум", xy=(aptalar[max_i], usd[max_i]),
             xytext=(aptalar[max_i] - 2, usd[max_i] + 2),
             arrowprops={"arrowstyle": "->"})
plt.title("Курс USD, тенге")
plt.xlabel("Неделя")
plt.ylabel("Тенге")
plt.show()
```

**Задание 4**
```python
import matplotlib.pyplot as plt

taular = ["Хан-Тенгри", "Талгар", "Белуха", "Бесбакан", "Музтау (Сайрам)"]
biiktik = [6995, 4979, 4506, 4380, 4238]

plt.barh(taular, biiktik, color="sienna")
plt.gca().invert_yaxis()
plt.xlabel("Высота, м")
plt.title("Высокие горы Казахстана (приблизительно)")
plt.show()
```

**Задание 5**
```python
import matplotlib.pyplot as plt

ailar = ["Янв", "Фев", "Мар", "Апр", "Май", "Июн",
         "Июл", "Авг", "Сен", "Окт", "Ноя", "Дек"]
almaty = [-4.7, -3.0, 3.9, 11.6, 16.8, 21.8, 24.4, 23.4, 18.0, 10.7, 2.7, -2.9]
astana = [-14.2, -13.4, -6.0, 5.5, 13.6, 19.1, 20.9, 18.8, 12.5, 4.4, -5.0, -11.5]
shymkent = [-1.0, 1.0, 7.5, 14.5, 20.0, 25.5, 28.0, 26.5, 20.5, 13.0, 5.5, 0.5]

plt.plot(ailar, almaty, marker="o", label="Алматы")
plt.plot(ailar, astana, marker="s", label="Астана")
plt.plot(ailar, shymkent, marker="^", label="Шымкент")
plt.title("Среднемесячная температура трёх городов")
plt.ylabel("°C")
plt.grid(True, alpha=0.3)
plt.legend()
plt.show()

airma = [a - b for a, b in zip(almaty, astana)]
i = airma.index(max(airma))
print("Месяц с наибольшей разницей:", ailar[i], round(airma[i], 1), "°C")
```
Ответ: февраль (10,4 °C).

**Задание 6**
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("kitap_dukeni.csv")
df["tusim"] = df["sany"] * df["baga"]

filial = df.groupby("filial")["tusim"].sum().sort_values(ascending=False)
plt.bar(filial.index, filial.values, color="teal")
plt.title("Недельная выручка по филиалам")
plt.ylabel("Тенге")
plt.savefig("filial.png", dpi=150, bbox_inches="tight")
plt.show()

kunder = df.groupby("kun", sort=False)["tusim"].sum()
plt.plot(kunder.index, kunder.values, marker="o", color="darkorange")
plt.title("Выручка по дням недели")
plt.ylabel("Тенге")
plt.savefig("kunder.png", dpi=150, bbox_inches="tight")
plt.show()
```

**Задание 7**
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("kitap_dukeni.csv")
df["tusim"] = df["sany"] * df["baga"]
kesteh = pd.pivot_table(df, values="tusim", index="filial", columns="sanat",
                        aggfunc="sum", fill_value=0)

# Способ 1: Pandas
kesteh.plot(kind="bar", title="Выручка: филиал × категория")
plt.ylabel("Тенге")
plt.show()

# Способ 2: чистый Matplotlib
x = list(range(len(kesteh.index)))
eni = 0.25
for j, sanat in enumerate(kesteh.columns):
    plt.bar([i + (j - 1) * eni for i in x], kesteh[sanat], width=eni, label=sanat)
plt.xticks(x, kesteh.index)
plt.ylabel("Тенге")
plt.legend()
plt.show()
```

**Задание 8**
```python
import matplotlib.pyplot as plt

kunder = ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"]
qadam = [6200, 8100, 5400, 9800, 7300, 12500, 3900]
tuster = ["green" if q >= 10000 else "red" for q in qadam]

plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
plt.plot(kunder, qadam, marker="o")
plt.title("Шаги")

plt.subplot(1, 2, 2)
plt.bar(kunder, qadam, color=tuster)
plt.axhline(10000, color="black", linestyle="--")
plt.title("Норма выполнена?")

plt.tight_layout()
plt.show()
```

**Задание 9**
```python
import pandas as pd
import matplotlib.pyplot as plt

kunder = ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"]
qadam = pd.Series([6200, 8100, 5400, 9800, 7300, 12500, 3900], index=kunder)
zhinaq = qadam.cumsum()

fig, ax = plt.subplots()
ax.bar(kunder, qadam, color="lightblue", label="За день")
ax.set_ylabel("Шагов за день")
ax2 = ax.twinx()
ax2.plot(kunder, zhinaq, color="navy", marker="o", label="Накоплено")
ax2.set_ylabel("Накоплено шагов")
ax.set_title("Шаги за неделю")
plt.show()
print("Всего за неделю:", zhinaq.iloc[-1])
```
Ответ: 53 200 шагов.

</details>
