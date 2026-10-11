# Урок 80. Оформление графиков: заголовок, оси, легенда, цветовая палитра.

## Темы

1. Два стиля: `plt.` и `fig, ax`
2. Заголовок и оси
3. Сетка, легенда, аннотация
4. Цвета и палитры
5. Стили и общие настройки
6. Полный профессиональный пример: «до» и «после»
7. Чек-лист «Профессиональный график»
8. Частые ошибки
9. Основные понятия

## 1. Два стиля: `plt.` и `fig, ax`

Запись `plt.title()`, `plt.xlabel()` — это **стиль pyplot (с состоянием)**. В профессиональном коде чаще используется **объектный стиль**: фигуру и оси сохраняем в переменные и вызываем их методы (это обычные объекты Python с методами).

```python
import matplotlib.pyplot as plt

fig, ax = plt.subplots(figsize=(8, 5))     # фигура размером 8×5 дюймов
ax.plot([1, 2, 3, 4], [10, 20, 15, 25])
ax.set_title("Объектный стиль")
ax.set_xlabel("X")
ax.set_ylabel("Y")
plt.show()
```

| Стиль pyplot | Объектный стиль |
|---|---|
| `plt.title(...)` | `ax.set_title(...)` |
| `plt.xlabel(...)` / `plt.ylabel(...)` | `ax.set_xlabel(...)` / `ax.set_ylabel(...)` |
| `plt.xlim(...)` / `plt.ylim(...)` | `ax.set_xlim(...)` / `ax.set_ylim(...)` |
| `plt.xticks(...)` | `ax.set_xticks(...)`, `ax.set_xticklabels(...)` |
| `plt.legend()` | `ax.legend()` |
| `plt.grid()` | `ax.grid()` |

**Чем лучше объектный стиль?** Когда на одной фигуре несколько графиков (`plt.subplots(2, 2)`), мы точно управляем каждым; код понятнее и надёжнее.

## 2. Заголовок и оси

### Заголовок

Хороший заголовок говорит, **что показано**, и по возможности — **главный вывод**:

- Плохо: «График 1», «Данные».
- Хорошо: «Филиал в Алматы даёт половину недельной выручки».

```python
ax.set_title("Недельная выручка филиалов", fontsize=14, fontweight="bold", loc="left")
fig.suptitle("Сеть книжных магазинов, март 2027")    # заголовок всей фигуры
```

### Подписи осей — всегда **с единицами измерения**

```python
ax.set_xlabel("День недели")
ax.set_ylabel("Выручка, тыс. тенге")      # единица: тенге? тыс. тенге? °C? см?
```

### Пределы и деления осей (ticks)

```python
ax.set_ylim(0, 120)                     # Y от 0 до 120
ax.set_xticks([0, 1, 2, 3])             # положение делений
ax.set_xticklabels(["Янв", "Фев", "Мар", "Апр"], rotation=45)   # подписи делений, повёрнутые на 45°
ax.tick_params(axis="both", labelsize=10)
```

Если длинные подписи налезают друг на друга — `rotation=45` или горизонтальные столбцы (`barh`).

### Делаем большие числа читаемыми

Запись вида `1e6` человек не понимает. Проще всего заранее разделить значения:

```python
import matplotlib.pyplot as plt

filial = ["Алматы", "Астана", "Шымкент"]
tusim = [116200, 80100, 36800]
tusim_myn = [t / 1000 for t in tusim]        # в тыс. тенге

fig, ax = plt.subplots()
ax.bar(filial, tusim_myn)
ax.set_ylabel("Выручка, тыс. тенге")
plt.show()
```

Или с помощью форматтера: `ax.yaxis.set_major_formatter("{x:,.0f} ₸")`.

## 3. Сетка, легенда, аннотация

### Сетка (grid)

Сетка помогает «считывать» значения, но не должна отвлекать от данных:

```python
ax.grid(axis="y", color="lightgray", linestyle="--", linewidth=0.7)
ax.set_axisbelow(True)         # чтобы сетка была позади столбцов
```

### Легенда (legend)

```python
ax.plot(x, almaty, label="Алматы")
ax.plot(x, astana, label="Астана")
ax.legend(loc="upper left", title="Город", frameon=False, fontsize=10)
```

Значения `loc`: `"best"` (по умолчанию, выбирает сам), `"upper right"`, `"upper left"`, `"lower right"`, `"lower left"`, `"center"`... Разместить **вне** графика: `ax.legend(bbox_to_anchor=(1.02, 1), loc="upper left")`.

**Совет:** если линия одна — легенда не нужна, достаточно заголовка. Если линий мало, подпись прямо в конце линии (`ax.text`) без легенды — ещё понятнее.

### Аннотация (annotation)

Привлекаем внимание зрителя к самой важной точке:

```python
ax.annotate("Рекорд: 63,8 тыс. ₸",
            xy=(5, 63.8),                    # точка, на которую указываем
            xytext=(2.5, 58),                # положение текста
            arrowprops={"arrowstyle": "->", "color": "black"})
```

## 4. Цвета и палитры

### Способы задать цвет

| Способ | Пример |
|---|---|
| Название | `"red"`, `"steelblue"`, `"darkorange"`, `"seagreen"` |
| Короткий код | `"r"`, `"g"`, `"b"`, `"k"` (чёрный) |
| HEX-код | `"#1f77b4"`, `"#E63946"` |
| Палитра Tableau | `"tab:blue"`, `"tab:orange"`, `"tab:green"` ... (10 цветов) |
| Оттенок серого | `"0.7"` (0 — чёрный, 1 — белый) |
| Кортеж RGB | `(0.2, 0.4, 0.6)` — каждое значение 0..1 |

### Виды палитр

```text
Категориальная (qualitative) ■ ■ ■ ■ ■ — непохожие друг на друга цвета: города, категории         → tab10, Set2
Последовательная (sequential) □ ▫ ▪ ■ █ — от светлого к тёмному: «мало → много» (плотность населения) → viridis, Blues
Расходящаяся (diverging)      ■ ▪ □ ▪ ■ — нейтральный центр: «минус ← 0 → плюс» (отклонение температуры) → coolwarm, RdBu
```

Получение списка цветов из цветовой карты:

```python
import matplotlib.pyplot as plt

cmap = plt.get_cmap("viridis")
tuster = [cmap(i / 4) for i in range(5)]      # 5 равномерных цветов
fig, ax = plt.subplots()
ax.bar(["A", "B", "C", "D", "E"], [5, 8, 12, 15, 20], color=tuster)
plt.show()
```

### Правила выбора цвета

1. **Цвет со смыслом:** хорошо — зелёный, плохо — красный; холодно — синий, жарко — красный.
2. **Меньше цветов — больше смысла.** Все столбцы одного цвета (серые), а тот, на который нужно обратить внимание, — яркий (**акцент**).
3. Учитывать **цветовую слепоту** (дальтонизм — примерно 8% мужчин): не ставить рядом красный и зелёный; палитры `viridis`, `tab10`, `cividis` безопасны.
4. Чтобы линии различались и при чёрно-белой печати, задавайте им разные `linestyle`/`marker`.
5. Во всех графиках одного отчёта у одной категории — **один цвет** (Алматы всегда синий).

### Пример акцента

```python
import matplotlib.pyplot as plt

kunder = ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"]
tusim = [20.9, 18.2, 24.1, 22.4, 32.0, 63.8, 51.7]     # тыс. тенге
tuster = ["tab:orange" if t == max(tusim) else "lightgray" for t in tusim]

fig, ax = plt.subplots(figsize=(8, 4.5))
baganalar = ax.bar(kunder, tusim, color=tuster)
ax.bar_label(baganalar, fmt="%.1f")
ax.set_title("Суббота — самый доходный день недели", loc="left", fontsize=14, fontweight="bold")
ax.set_ylabel("Выручка, тыс. тенге")
ax.spines[["top", "right"]].set_visible(False)       # убрать лишние рамки
ax.grid(axis="y", linestyle="--", alpha=0.4)
ax.set_axisbelow(True)
plt.tight_layout()
plt.show()
```

Результат: шесть серых столбцов и один оранжевый (Сб). Заголовок сообщает главную мысль графика.

## 5. Стили и общие настройки

Готовые стили одной строкой меняют вид всего графика:

```python
import matplotlib.pyplot as plt

print(plt.style.available[:6])       # начало списка доступных стилей
plt.style.use("ggplot")              # или "seaborn-v0_8", "bmh", "fivethirtyeight", "default"
```

Глобальные настройки (`rcParams`) — один раз в начале программы:

```python
import matplotlib.pyplot as plt

plt.rcParams["figure.figsize"] = (8, 5)
plt.rcParams["font.size"] = 11
plt.rcParams["axes.spines.top"] = False
plt.rcParams["axes.spines.right"] = False
```

## 6. Полный профессиональный пример: «до» и «после»

**До:**

```python
import matplotlib.pyplot as plt

ailar = ["Янв", "Фев", "Мар", "Апр", "Май", "Июн", "Июл", "Авг", "Сен", "Окт", "Ноя", "Дек"]
almaty = [-4.7, -3.0, 3.9, 11.6, 16.8, 21.8, 24.4, 23.4, 18.0, 10.7, 2.7, -2.9]
astana = [-14.2, -13.4, -6.0, 5.5, 13.6, 19.1, 20.9, 18.8, 12.5, 4.4, -5.0, -11.5]

plt.plot(ailar, almaty)
plt.plot(ailar, astana)
plt.show()
```

**После:**

```python
import matplotlib.pyplot as plt

ailar = ["Янв", "Фев", "Мар", "Апр", "Май", "Июн", "Июл", "Авг", "Сен", "Окт", "Ноя", "Дек"]
almaty = [-4.7, -3.0, 3.9, 11.6, 16.8, 21.8, 24.4, 23.4, 18.0, 10.7, 2.7, -2.9]
astana = [-14.2, -13.4, -6.0, 5.5, 13.6, 19.1, 20.9, 18.8, 12.5, 4.4, -5.0, -11.5]

fig, ax = plt.subplots(figsize=(9, 5))
ax.plot(ailar, almaty, color="#E76F51", marker="o", linewidth=2, label="Алматы")
ax.plot(ailar, astana, color="#264653", marker="s", linewidth=2, linestyle="--", label="Астана")
ax.axhline(0, color="gray", linewidth=0.8)
ax.fill_between(ailar, almaty, astana, color="gray", alpha=0.1)   # закрасить разницу

ax.set_title("Зимой в Астане до 10 °C холоднее, чем в Алматы", loc="left",
             fontsize=14, fontweight="bold")
ax.set_xlabel("Месяц")
ax.set_ylabel("Средняя температура, °C")
ax.set_ylim(-20, 30)
ax.grid(axis="y", linestyle=":", alpha=0.5)
ax.spines[["top", "right"]].set_visible(False)
ax.legend(loc="upper left", frameon=False)
ax.annotate("Февраль: разница 10,4 °C", xy=(1, -13.4), xytext=(2.5, -18),
            arrowprops={"arrowstyle": "->"})
fig.text(0.01, 0.01, "Источник данных: климатические средние значения (приблизительно)", fontsize=8, color="gray")
plt.tight_layout()
plt.savefig("temperatura.png", dpi=200, bbox_inches="tight")
plt.show()
```

## 7. Чек-лист «Профессиональный график»

| № | Проверка | Да/Нет |
|---|---|---|
| 1 | Тип диаграммы соответствует вопросу | |
| 2 | Есть заголовок, и он передаёт сообщение | |
| 3 | Оси подписаны, указаны **единицы измерения** | |
| 4 | В столбчатой диаграмме ось Y начинается с 0 | |
| 5 | Числа читаемы (нет `1e6`, подписи не налезают друг на друга) | |
| 6 | Если рядов несколько — есть легенда (или прямые подписи) | |
| 7 | Цвета осмысленные, не больше 5–6, безопасны для дальтоников | |
| 8 | Нет лишних элементов (толстая рамка, яркая сетка, 3D-эффект) | |
| 9 | Важная точка выделена аннотацией/акцентом | |
| 10 | Указан источник данных, рисунок сохранён в высоком качестве | |

## Частые ошибки

**1. Смешивание стилей.**
```python
fig, ax = plt.subplots(1, 2)
plt.title("Слева")      # попадёт только на последний (правый) график!
```
Правильно:
```python
fig, ax = plt.subplots(1, 2)
ax[0].set_title("Слева")
ax[1].set_title("Справа")
```

**2. Запись `ax.title("...")` или `ax.xlabel("...")`** — вызывает `TypeError` или `AttributeError`. У Axes методы называются `set_title`, `set_xlabel`, `set_ylim`: в объектном стиле большинство методов настройки начинаются с `set_`.

**3. Подписи осей обрезаются** — `plt.tight_layout()` или `savefig(..., bbox_inches="tight")`.

**4. «Радуга»** — каждый столбец своего цвета, но цвет не несёт смысла. Категории и так различаются по оси X — цвет лишний.

**5. Стиль влияет на всю программу.** `plt.style.use("dark_background")` применяется ко всем последующим графикам; вернуть: `plt.style.use("default")`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `fig, ax = plt.subplots()` | Создание объектов фигуры и осей |
| Ticks (деления) | Отмеченные значения на оси и их подписи |
| Легенда (legend) | Блок, поясняющий, что означают цвета/линии |
| Аннотация (annotation) | Текст на графике, указывающий на точку (со стрелкой) |
| Палитра | Набор цветов, используемых в графике |
| Цветовая карта (colormap) | Непрерывная шкала, превращающая число в цвет (`viridis`) |
| Акцент | Выделение главного элемента особым цветом |
| `spines` | Линии рамки графика |

## Примечание

- Не смешивайте два стиля в одной фигуре: функции `plt.*` работают с «текущими» осями (`plt.gca()`), которые не всегда совпадают с нужным `ax`.
- Стиль `"seaborn"` в Matplotlib 3.6+ переименован в `"seaborn-v0_8"`; старое имя вызывает ошибку `OSError: 'seaborn' not found in the style library`.
- `ax.set_xticklabels()` без предварительного `ax.set_xticks()` выдаёт предупреждение `FixedFormatter should only be used together with FixedLocator`; задавайте деления и подписи вместе: `ax.set_xticks(pos, labels)` (Matplotlib 3.5+).
- Кириллица и казахские буквы (ә, ғ, қ, ң, ө, ұ, ү, һ, і) отображаются стандартным шрифтом DejaVu Sans; если вместо букв видны квадраты, задайте шрифт через `plt.rcParams["font.family"]`.
- `fig.savefig()` надёжнее, чем `plt.savefig()`, когда открыто несколько фигур: сохраняется именно нужная.
- Документация: [Customizing Matplotlib](https://matplotlib.org/stable/users/explain/customizing.html) и [Choosing Colormaps](https://matplotlib.org/stable/users/explain/colors/colormaps.html).
