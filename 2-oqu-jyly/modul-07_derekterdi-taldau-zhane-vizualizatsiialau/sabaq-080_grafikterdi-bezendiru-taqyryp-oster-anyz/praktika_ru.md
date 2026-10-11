# Урок 80. Практические задания: оформление графиков

Все задания выполняйте в **объектном стиле** (`fig, ax = plt.subplots()`). Каждый график проверяйте по чек-листу «Профессиональный график» из теории.

## Базовый уровень

**Задание 1. Спаси плохой график.** Код ниже строит «неудачный» график. Перепишите его, внеся не менее 6 улучшений: заголовок (с сообщением), подписи осей с единицами измерения, ось Y от 0, значения над столбцами, без лишних рамок, акцентный цвет (кружок с наибольшим числом учеников).

```python
import matplotlib.pyplot as plt

uyirmeler = ["Python", "Робототехника", "Шахматы", "Домбра", "Изобразительное искусство", "Футбол"]
sany = [26, 22, 14, 11, 9, 24]
plt.bar(uyirmeler, sany, color=["red", "green", "blue", "yellow", "purple", "cyan"])
plt.ylim(8, 27)
plt.show()
```

**Задание 2. Легенда и стили линий.** Четвертные оценки трёх учеников (по 5-балльной системе, средние):
- Әлия: `[4.2, 4.5, 4.7, 4.9]`
- Нұрлан: `[3.8, 3.9, 4.4, 4.6]`
- Дана: `[4.6, 4.4, 4.3, 4.5]`

По оси X — «I четверть ... IV четверть». Три линии: разный цвет, маркер и тип линии (чтобы различались и на чёрно-белом принтере). Легенда внутри графика, без рамки (`frameon=False`). Ось Y от 3.5 до 5.0 (на линейном графике начинать с 0 не обязательно — объясните в комментарии почему).

**Задание 3. Аннотация.** На графике курса USD `[502, 505, 498, 510, 515, 512, 520, 518]` (недели 1–8) добавьте аннотации со стрелками к самой низкой и самой высокой точкам (`ax.annotate`). Тексты аннотаций должны вычисляться в коде (например, `f"Мин: {min(usd)} ₸"`).

**Задание 4. Сравнение стилей.** Сохраните один и тот же график в трёх стилях с помощью `plt.style.use(...)` (`"default"`, `"ggplot"`, `"bmh"`): `style_default.png`, `style_ggplot.png`, `style_bmh.png`. Какой понравился и почему?

## Продвинутый уровень

**Задание 5. Выбор палитры.** Условная плотность населения 6 регионов Казахстана (чел./км²): Алматинская область — 9.2, Туркестанская область — 17.5, Костанайская область — 3.0, Мангистауская область — 4.6, Улытауская область — 0.9, Жамбылская область — 8.1. Постройте горизонтальную столбчатую диаграмму: цвет берётся из **последовательной** палитры (`Blues` или `viridis`) в зависимости от плотности, добавьте `colorbar`. Почему здесь не подходит категориальная палитра?

**Задание 6. Отчёт в едином стиле.** По `kitap_dukeni.csv` фигура 2×2 (`plt.subplots(2, 2, figsize=(12, 8))`):
а) выручка филиалов (столбцы), б) выручка по дням (линия), в) доли категорий (круговая), г) цена × количество (рассеяние).
Требования: `fig.suptitle`, у каждого Axes свой заголовок, во всех графиках у одной категории один цвет (`tuster = {"Книги": ..., "Канцтовары": ..., "Игрушки": ...}`), `tight_layout`, `savefig(dpi=200)`.

**Задание 7. Своя функция.** Напишите функцию `beautify(ax, title, xlabel, ylabel)`: она добавляет к заданным Axes заголовок (слева, жирный), подписи осей, мягкую сетку и убирает верхнюю/правую рамки. Примените её к 2–3 разным графикам.

## ★ Олимпиадная / творческая задача

**Задание 8. Свой файл стиля.** Создайте файл стиля Matplotlib (`.mplstyle`) — например, `uyirme.mplstyle`:
```text
figure.figsize: 9, 5
axes.spines.top: False
axes.spines.right: False
axes.grid: True
grid.alpha: 0.3
axes.titlesize: 14
axes.titleweight: bold
axes.prop_cycle: cycler('color', ['264653', '2A9D8F', 'E9C46A', 'F4A261', 'E76F51'])
```
и примените его через `plt.style.use("uyirme.mplstyle")`. Сравните один и тот же график без стиля и со стилем.

**Задание 9. «Инфографика».** На одной фигуре: крупный заголовок, одно ключевое число (крупным шрифтом через `fig.text`, например «233 100 ₸»), рядом небольшая столбчатая диаграмма и источник данных. Результат должен годиться для публикации в социальной сети.

## Домашнее задание

1. Выберите самый неудачный из своих графиков, построенных на уроках 78–79, и улучшите его по чек-листу. Сохраните рисунки «до» и «после».
2. Следующий урок — **практикум**: подумайте, какой набор данных выберете (список — в `praktika_ru.md` следующего урока). Можно заранее найти данные и подготовить их в виде CSV.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import matplotlib.pyplot as plt

uyirmeler = ["Python", "Робототехника", "Шахматы", "Домбра", "Изобразительное искусство", "Футбол"]
sany = [26, 22, 14, 11, 9, 24]

# сортировка: самый большой сверху
juptar = sorted(zip(sany, uyirmeler))
sany_s = [j[0] for j in juptar]
atau_s = [j[1] for j in juptar]
tuster = ["#2A9D8F" if s == max(sany) else "lightgray" for s in sany_s]

fig, ax = plt.subplots(figsize=(8, 4.5))
b = ax.barh(atau_s, sany_s, color=tuster)
ax.bar_label(b, padding=3)
ax.set_title("Python — самый популярный кружок", loc="left", fontsize=14, fontweight="bold")
ax.set_xlabel("Количество учеников, чел.")
ax.set_xlim(0, 30)
ax.spines[["top", "right"]].set_visible(False)
plt.tight_layout()
plt.show()
```
Улучшения: горизонтальные столбцы (длинные названия), отсортировано, ось от 0, подписаны значения, акцент, заголовок с сообщением, ось с единицами, нет лишних рамок.

**Задание 2**
```python
import matplotlib.pyplot as plt

toqsan = ["I четверть", "II четверть", "III четверть", "IV четверть"]
fig, ax = plt.subplots(figsize=(8, 5))
ax.plot(toqsan, [4.2, 4.5, 4.7, 4.9], color="tab:blue", marker="o", linestyle="-", label="Әлия")
ax.plot(toqsan, [3.8, 3.9, 4.4, 4.6], color="tab:orange", marker="s", linestyle="--", label="Нұрлан")
ax.plot(toqsan, [4.6, 4.4, 4.3, 4.5], color="tab:green", marker="^", linestyle=":", label="Дана")
ax.set_ylim(3.5, 5.0)
ax.set_title("Нұрлан показал наибольший рост", loc="left", fontweight="bold")
ax.set_ylabel("Средняя оценка (5-балльная)")
ax.legend(frameon=False, loc="lower right")
ax.grid(axis="y", alpha=0.3)
plt.show()
# На линейном графике мы показываем изменение (тренд), а не длину,
# поэтому ось Y можно приблизить к области данных.
```

**Задание 3**
```python
import matplotlib.pyplot as plt

aptalar = list(range(1, 9))
usd = [502, 505, 498, 510, 515, 512, 520, 518]
i_min = usd.index(min(usd))
i_max = usd.index(max(usd))

fig, ax = plt.subplots()
ax.plot(aptalar, usd, marker="o", color="tab:green")
ax.annotate(f"Мин: {min(usd)} ₸", xy=(aptalar[i_min], usd[i_min]),
            xytext=(aptalar[i_min] + 0.5, usd[i_min] - 4), arrowprops={"arrowstyle": "->"})
ax.annotate(f"Макс: {max(usd)} ₸", xy=(aptalar[i_max], usd[i_max]),
            xytext=(aptalar[i_max] - 2.5, usd[i_max] + 2), arrowprops={"arrowstyle": "->"})
ax.set_ylim(490, 528)
ax.set_title("Курс USD, 8 недель")
ax.set_xlabel("Неделя")
ax.set_ylabel("Тенге")
plt.show()
```

**Задание 4**
```python
import matplotlib.pyplot as plt

for stil in ["default", "ggplot", "bmh"]:
    plt.style.use(stil)
    fig, ax = plt.subplots()
    ax.plot([1, 2, 3, 4, 5], [3, 5, 4, 7, 6], marker="o")
    ax.set_title(f"Стиль: {stil}")
    fig.savefig(f"style_{stil}.png", dpi=120)
    plt.close(fig)
plt.style.use("default")
```

**Задание 5**
```python
import matplotlib.pyplot as plt
from matplotlib.colors import Normalize
from matplotlib.cm import ScalarMappable

onirler = ["Алматинская обл.", "Туркестанская обл.", "Костанайская обл.",
           "Мангистауская обл.", "Улытауская обл.", "Жамбылская обл."]
tygyzdyq = [9.2, 17.5, 3.0, 4.6, 0.9, 8.1]

norm = Normalize(vmin=0, vmax=max(tygyzdyq))
cmap = plt.get_cmap("Blues")
tuster = [cmap(norm(t)) for t in tygyzdyq]

fig, ax = plt.subplots(figsize=(8, 4.5))
ax.barh(onirler, tygyzdyq, color=tuster, edgecolor="gray")
ax.invert_yaxis()
ax.set_xlabel("Плотность населения, чел./км² (условно)")
ax.set_title("Плотность населения регионов", loc="left", fontweight="bold")
fig.colorbar(ScalarMappable(norm=norm, cmap=cmap), ax=ax, label="чел./км²")
plt.tight_layout()
plt.show()
# Здесь значение меняется в порядке "мало → много", поэтому только последовательная
# палитра передаёт смысл "темнее = больше"; категориальные цвета порядок не показывают.
```

**Задание 6**
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("kitap_dukeni.csv")
df["tusim"] = df["sany"] * df["baga"]
tuster = {"Книги": "#264653", "Канцтовары": "#E9C46A", "Игрушки": "#E76F51"}

fig, ax = plt.subplots(2, 2, figsize=(12, 8))

filial = df.groupby("filial")["tusim"].sum().sort_values(ascending=False) / 1000
ax[0, 0].bar(filial.index, filial.values, color="#2A9D8F")
ax[0, 0].set_title("Выручка филиалов")
ax[0, 0].set_ylabel("тыс. ₸")

kun = df.groupby("kun", sort=False)["tusim"].sum() / 1000
ax[0, 1].plot(kun.index, kun.values, marker="o", color="#2A9D8F")
ax[0, 1].set_title("Выручка по дням")
ax[0, 1].set_ylabel("тыс. ₸")

sanat = df.groupby("sanat")["tusim"].sum()
ax[1, 0].pie(sanat.values, labels=sanat.index, autopct="%1.0f%%",
             colors=[tuster[s] for s in sanat.index], startangle=90)
ax[1, 0].set_title("Доли категорий")

for s, top in df.groupby("sanat"):
    ax[1, 1].scatter(top["baga"], top["sany"], color=tuster[s], label=s, s=60)
ax[1, 1].set_title("Цена и количество")
ax[1, 1].set_xlabel("Цена, ₸")
ax[1, 1].set_ylabel("Штук")
ax[1, 1].legend(frameon=False)

for a in [ax[0, 0], ax[0, 1], ax[1, 1]]:
    a.spines[["top", "right"]].set_visible(False)
fig.suptitle("Книжные магазины: недельный отчёт", fontsize=16, fontweight="bold")
plt.tight_layout()
fig.savefig("esep.png", dpi=200)
plt.show()
```

**Задание 7**
```python
import matplotlib.pyplot as plt


def beautify(ax, title, xlabel="", ylabel=""):
    """Оформляет Axes в едином профессиональном стиле."""
    ax.set_title(title, loc="left", fontsize=13, fontweight="bold")
    ax.set_xlabel(xlabel)
    ax.set_ylabel(ylabel)
    ax.grid(axis="y", linestyle="--", alpha=0.4)
    ax.set_axisbelow(True)
    ax.spines[["top", "right"]].set_visible(False)


fig, ax = plt.subplots(1, 2, figsize=(10, 4))
ax[0].bar(["A", "B", "C"], [3, 7, 5])
beautify(ax[0], "Столбцы", "Категория", "Значение")
ax[1].plot([1, 2, 3, 4], [2, 3, 5, 4], marker="o")
beautify(ax[1], "Линия", "Время", "Значение")
plt.tight_layout()
plt.show()
```

**Задания 8–9** — творческие; оценивание: файл стиля работает, графики удовлетворяют не менее 8 пунктам чек-листа. Образец к заданию 9:
```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(8, 4.5))
fig.text(0.05, 0.80, "Книжные магазины, неделя", fontsize=16, fontweight="bold")
fig.text(0.05, 0.50, "233 100 ₸", fontsize=40, color="#E76F51", fontweight="bold")
fig.text(0.05, 0.40, "общая выручка", fontsize=12, color="gray")
ax = fig.add_axes([0.55, 0.15, 0.4, 0.6])
ax.bar(["Алматы", "Астана", "Шымкент"], [116.2, 80.1, 36.8], color="#2A9D8F")
ax.set_ylabel("тыс. ₸")
ax.spines[["top", "right"]].set_visible(False)
fig.text(0.05, 0.05, "Источник данных: kitap_dukeni.csv", fontsize=8, color="gray")
plt.show()
```

</details>
