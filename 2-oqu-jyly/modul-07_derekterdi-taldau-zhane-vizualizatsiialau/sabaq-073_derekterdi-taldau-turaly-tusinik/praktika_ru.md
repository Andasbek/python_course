# Урок 73. Практические задания: массивы и операции NumPy

> Во всех заданиях `import numpy as np`. По возможности не используйте циклы — применяйте векторные операции и маски!

## Базовый уровень

### Задание 1. Создание массивов
Создайте с помощью NumPy и выведите на экран:
а) чётные числа от 1 до 20;
б) 11 равномерных точек от 0 до 100;
в) матрицу нулей 3×3;
г) 10 случайных целых чисел от 1 до 100 (`default_rng(1)`);
д) атрибуты `shape`, `dtype`, `size` каждого массива.

### Задание 2. Конвертер оценок
Результаты учеников по 100-балльной шкале: `[95, 72, 88, 45, 60, 81, 99, 55, 67, 90]`. С помощью векторных операций:
а) переведите в 5-балльную систему: 85+ → 5, 65–84 → 4, 50–64 → 3, <50 → 2 (подсказка: `np.where` или маски);
б) выведите количество каждой оценки (`np.unique(..., return_counts=True)`);
в) средний, медианный, наибольший, наименьший балл.

```text
По 5-балльной: [5 4 5 2 3 4 5 3 4 5]
2 — учеников: 1, 3 — учеников: 2, 4 — учеников: 3, 5 — учеников: 4
Среднее: 75.2, медиана: 76.5, макс: 99, мин: 45
```

### Задание 3. Погода за неделю
Температуры в Алматы за одну неделю: `[-5.2, -3.1, 0.5, 2.8, 1.1, -1.4, -6.0]`, дни: `["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"]`.
а) Переведите в градусы Фаренгейта;
б) названия самого тёплого и самого холодного дня (`argmax`, `argmin`);
в) названия и количество морозных (< 0) дней;
г) разница каждого дня с предыдущим (`np.diff`).

### Задание 4. Магазин
Цены товаров `prices = [350, 1200, 780, 2500, 90]` (тенге), проданное количество `qty = [40, 5, 12, 3, 100]`.
а) выручка по каждому товару;
б) общая выручка;
в) новые цены с 10% скидкой на товары дороже 1000 тенге;
г) индекс товара, принёсшего наибольшую выручку.

### Задание 5. Классный журнал (2D)
Создайте матрицу оценок (2–5) 4 ученика × 5 предметов с помощью `default_rng(3).integers(2, 6, size=(4, 5))`.
а) среднее каждого ученика (`axis=1`);
б) среднее по каждому предмету (`axis=0`);
в) есть ли ученики, получившие хотя бы одну «2» (`(m == 2).any(axis=1)`);
г) «ударники», получившие 4–5 по всем предметам (`(m >= 4).all(axis=1)`).

## Продвинутый уровень

### Задание 6. Анализ курса валют
Смоделируйте курс USD/KZT за 30 дней: начальное значение 510, ежедневное изменение `rng.normal(0, 1.5, 30)`, а курс — `510 + np.cumsum(изменения)`. Найдите: наибольший/наименьший курс и день, среднее, количество дней роста курса, наибольший рост за один день, скользящее среднее за 7 дней (подсказка: `np.convolve(x, np.ones(7) / 7, mode="valid")`).

### Задание 7. Нормализация
Нормализуйте матрицу из задания 5 в диапазон 0–1: `(m - m.min()) / (m.max() - m.min())`. Затем нормализуйте по каждому предмету (`axis=0`, `keepdims=True`). Объясните разницу.

### Задание 8. Эксперимент со скоростью
Для 1, 10, 100 тысяч и 1 млн элементов вычислите «сумму квадратов» списком и NumPy и сравните время в виде таблицы. Напишите вывод: начиная с какого размера NumPy явно быстрее?

## ★ Олимпиадная / творческая задача

### Задание 9. Игра «Жизнь» (Game of Life)
Заполните поле 10×10 случайными 0/1. Вычислите один шаг **без циклов**: найдите сумму 8 соседей каждой клетки через `np.roll`; правило — живая клетка остаётся живой при 2 или 3 соседях, мёртвая оживает при 3 соседях. Выведите 5 шагов символами `█` и `·`.

### Задание 10. Число π методом Монте-Карло
Создайте n случайных точек в квадрате через `rng.random((n, 2))` и оцените число π по доле точек, попавших внутрь единичной окружности (n = 1000, 100 000, 10 000 000). Как меняется точность?

## Домашнее задание

1. Прочитайте файл `hourly.csv` из урока 67 (или любой числовой CSV) через `np.loadtxt(..., delimiter=";", skiprows=1, usecols=1)` и найдите среднее, минимальное, максимальное значения.
2. Составьте матрицу из своих оценок (не менее 3 предметов × 5 оценок) и проанализируйте: какой предмет лучше всего, какой трудный.
3. Выполните `pip install pandas`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import numpy as np

a = np.arange(2, 21, 2)
b = np.linspace(0, 100, 11)
c = np.zeros((3, 3))
d = np.random.default_rng(1).integers(1, 101, size=10)
for name, x in [("а", a), ("б", b), ("в", c), ("г", d)]:
    print(name, x, "| shape:", x.shape, "dtype:", x.dtype, "size:", x.size)
```

**Задание 2**
```python
import numpy as np

scores = np.array([95, 72, 88, 45, 60, 81, 99, 55, 67, 90])
five = np.where(scores >= 85, 5, np.where(scores >= 65, 4, np.where(scores >= 50, 3, 2)))
print("По 5-балльной:", five)
values, counts = np.unique(five, return_counts=True)
print(", ".join(f"{v} — учеников: {c}" for v, c in zip(values, counts)))
print(f"Среднее: {scores.mean()}, медиана: {np.median(scores)}, макс: {scores.max()}, мин: {scores.min()}")
```

**Задание 3**
```python
import numpy as np

t = np.array([-5.2, -3.1, 0.5, 2.8, 1.1, -1.4, -6.0])
days = np.array(["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"])
print("Фаренгейт:", np.round(t * 9 / 5 + 32, 1))
print("Самый тёплый:", days[t.argmax()], "Самый холодный:", days[t.argmin()])
print("Морозные дни:", days[t < 0], "количество:", (t < 0).sum())
print("Изменение за день:", np.round(np.diff(t), 1))
```

**Задание 4**
```python
import numpy as np

prices = np.array([350, 1200, 780, 2500, 90])
qty = np.array([40, 5, 12, 3, 100])
income = prices * qty
print("Выручка:", income)
print("Всего:", income.sum())
print("Новые цены:", np.where(prices > 1000, prices * 0.9, prices))
print("Индекс самого доходного товара:", income.argmax())
```

**Задание 5**
```python
import numpy as np

m = np.random.default_rng(3).integers(2, 6, size=(4, 5))
print(m)
print("Средние учеников:", m.mean(axis=1).round(2))
print("Средние предметов:", m.mean(axis=0).round(2))
print("Получившие «2»:", np.where((m == 2).any(axis=1))[0])
print("Ударники:", np.where((m >= 4).all(axis=1))[0])
```

**Задание 6**
```python
import numpy as np

rng = np.random.default_rng(10)
changes = rng.normal(0, 1.5, 30)
rate = 510 + np.cumsum(changes)
days = np.arange(1, 31)
print(f"Макс: {rate.max():.2f} (день {days[rate.argmax()]}), мин: {rate.min():.2f} (день {days[rate.argmin()]})")
print(f"Среднее: {rate.mean():.2f}")
print("Дней роста:", (np.diff(rate) > 0).sum())
print(f"Наибольший рост: {np.diff(rate).max():.2f}")
print("Скользящее среднее за 7 дней:", np.convolve(rate, np.ones(7) / 7, mode="valid").round(1))
```

**Задание 7**
```python
import numpy as np

m = np.random.default_rng(3).integers(2, 6, size=(4, 5)).astype(float)
overall = (m - m.min()) / (m.max() - m.min())
lo, hi = m.min(axis=0, keepdims=True), m.max(axis=0, keepdims=True)
per_subject = (m - lo) / np.where(hi - lo == 0, 1, hi - lo)   # защита от деления на ноль
print(overall.round(2))
print(per_subject.round(2))
```
Пояснение: первый способ приводит все оценки к общей шкале; второй сравнивает учеников внутри каждого предмета (учитывается трудность предмета).

**Задание 8**
```python
import time
import numpy as np

print(f"{'n':>10} {'список, с':>10} {'NumPy, с':>10}")
for n in (1_000, 10_000, 100_000, 1_000_000):
    lst, arr = list(range(n)), np.arange(n, dtype=np.int64)
    t = time.perf_counter(); sum(x * x for x in lst); tl = time.perf_counter() - t
    t = time.perf_counter(); (arr * arr).sum(); tn = time.perf_counter() - t
    print(f"{n:>10} {tl:>10.5f} {tn:>10.5f}")
```

**Задание 9**
```python
import numpy as np

def step(field):
    neighbors = sum(np.roll(np.roll(field, dy, 0), dx, 1)
                    for dy in (-1, 0, 1) for dx in (-1, 0, 1) if (dy, dx) != (0, 0))
    return ((neighbors == 3) | ((field == 1) & (neighbors == 2))).astype(int)

field = np.random.default_rng(5).integers(0, 2, size=(10, 10))
for gen in range(5):
    print(f"Поколение {gen}:")
    print("\n".join("".join("█" if c else "·" for c in row) for row in field))
    field = step(field)
```

**Задание 10**
```python
import numpy as np

rng = np.random.default_rng(0)
for n in (1_000, 100_000, 10_000_000):
    pts = rng.random((n, 2))
    inside = ((pts ** 2).sum(axis=1) <= 1).mean()
    print(f"n = {n:>10}: π ≈ {4 * inside:.5f}, ошибка = {abs(4 * inside - np.pi):.5f}")
```

</details>
