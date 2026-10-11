# Сабақ 73. Практикалық тапсырмалар: NumPy массивтері және амалдар

> Барлық тапсырмада `import numpy as np`. Циклдерді мүмкіндігінше қолданбаңыз — векторлық амалдар мен маскаларды пайдаланыңыз!

## Базалық деңгей

### 1-тапсырма. Массивтер жасау
NumPy арқылы жасаңыз және экранға шығарыңыз:
а) 1-ден 20-ға дейінгі жұп сандар;
ә) 0-ден 100-ге дейін 11 бірқалыпты нүкте;
б) 3×3 нөлдер матрицасы;
в) 1–100 аралығындағы 10 кездейсоқ бүтін сан (`default_rng(1)`);
г) әр массивтің `shape`, `dtype`, `size` атрибуттары.

### 2-тапсырма. Бағалар конвертері
Оқушылардың 100 балдық нәтижелері: `[95, 72, 88, 45, 60, 81, 99, 55, 67, 90]`. Векторлық амалдармен:
а) 5 балдық жүйеге айналдырыңыз: 85+ → 5, 65–84 → 4, 50–64 → 3, <50 → 2 (кеңес: `np.where` немесе маскалар);
ә) әр бағаның санын шығарыңыз (`np.unique(..., return_counts=True)`);
б) орташа, медиана, ең жоғары, ең төмен балл.

```text
5 балдық: [5 4 5 2 3 4 5 3 4 5]
2 — 1 оқушы, 3 — 2 оқушы, 4 — 3 оқушы, 5 — 4 оқушы
Орташа: 75.2, медиана: 76.5, макс: 99, мин: 45
```

### 3-тапсырма. Апталық ауа райы
Бір аптаның Алматы температуралары: `[-5.2, -3.1, 0.5, 2.8, 1.1, -1.4, -6.0]`, күндер: `["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"]`.
а) Фаренгейтке айналдырыңыз;
б) ең жылы және ең суық күннің атауы (`argmax`, `argmin`);
в) аязды (< 0) күндер атаулары мен саны;
г) әр күннің алдыңғы күннен айырмасы (`np.diff`).

### 4-тапсырма. Дүкен
Тауарлар бағасы `prices = [350, 1200, 780, 2500, 90]` (теңге), сатылған саны `qty = [40, 5, 12, 3, 100]`.
а) әр тауардан түскен табыс;
б) жалпы табыс;
в) 1000 теңгеден қымбат тауарларға 10% жеңілдік қолданылған жаңа бағалар;
г) ең көп табыс әкелген тауардың индексі.

### 5-тапсырма. Сынып журналы (2D)
4 оқушы × 5 пәннен тұратын бағалар матрицасын (2–5) `default_rng(3).integers(2, 6, size=(4, 5))` арқылы жасаңыз.
а) әр оқушының орташасы (`axis=1`);
б) әр пәннің орташасы (`axis=0`);
в) кемінде бір «2» алған оқушылар бар ма (`(m == 2).any(axis=1)`);
г) барлық пәннен 4–5 алған «екпінділер» (`(m >= 4).all(axis=1)`).

## Кеңейтілген деңгей

### 6-тапсырма. Валюта бағамы талдауы
30 күндік USD/KZT бағамын модельдеңіз: бастапқы мән 510, әр күнгі өзгеріс `rng.normal(0, 1.5, 30)`, ал бағам — `510 + np.cumsum(өзгерістер)`. Табыңыз: ең жоғары/төмен бағам және күні, орташа, бағам өскен күндер саны, ең үлкен бір күндік өсу, 7 күндік жылжымалы орташа (кеңес: `np.convolve(x, np.ones(7) / 7, mode="valid")`).

### 7-тапсырма. Нормалау
5-тапсырмадағы матрицаны 0–1 аралығына нормалаңыз: `(m - m.min()) / (m.max() - m.min())`. Содан кейін әр пән бойынша нормалаңыз (`axis=0`, `keepdims=True`). Айырмашылықты түсіндіріңіз.

### 8-тапсырма. Жылдамдық эксперименті
1, 10, 100 мың, 1 млн элемент үшін «квадраттардың қосындысын» тізіммен және NumPy-мен есептеп, уақыттарын кесте түрінде салыстырыңыз. Қорытынды жазыңыз: қай өлшемнен бастап NumPy айқын жылдам?

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. «Тіршілік» ойыны (Game of Life)
10×10 өрісті кездейсоқ 0/1-мен толтырыңыз. Бір қадамды **циклсіз** есептеңіз: әр торкөздің 8 көршісінің қосындысын `np.roll` арқылы табыңыз; ереже — тірі торкөз 2 немесе 3 көршімен тірі қалады, өлі торкөз 3 көршімен тіріледі. 5 қадамды `█` және `·` таңбаларымен шығарыңыз.

### 10-тапсырма. Монте-Карло әдісімен π
`rng.random((n, 2))` арқылы шаршыда n кездейсоқ нүкте жасап, бірлік шеңбер ішіне түскен нүктелер үлесі бойынша π санын бағалаңыз (n = 1000, 100 000, 10 000 000). Дәлдік қалай өзгереді?

## Үй тапсырмасы

1. 67-сабақта жасаған `hourly.csv` (немесе кез келген сандық CSV) файлын `np.loadtxt(..., delimiter=";", skiprows=1, usecols=1)` арқылы оқып, орташа, мин, макс мәндерін табыңыз.
2. Өз бағаларыңыздан (кемінде 3 пән × 5 баға) матрица құрып, талдаңыз: қай пән ең жақсы, қай пән қиын.
3. `pip install pandas` орындаңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import numpy as np

a = np.arange(2, 21, 2)
b = np.linspace(0, 100, 11)
c = np.zeros((3, 3))
d = np.random.default_rng(1).integers(1, 101, size=10)
for name, x in [("а", a), ("ә", b), ("б", c), ("в", d)]:
    print(name, x, "| shape:", x.shape, "dtype:", x.dtype, "size:", x.size)
```

**2-тапсырма**
```python
import numpy as np

scores = np.array([95, 72, 88, 45, 60, 81, 99, 55, 67, 90])
five = np.where(scores >= 85, 5, np.where(scores >= 65, 4, np.where(scores >= 50, 3, 2)))
print("5 балдық:", five)
values, counts = np.unique(five, return_counts=True)
print(", ".join(f"{v} — {c} оқушы" for v, c in zip(values, counts)))
print(f"Орташа: {scores.mean()}, медиана: {np.median(scores)}, макс: {scores.max()}, мин: {scores.min()}")
```

**3-тапсырма**
```python
import numpy as np

t = np.array([-5.2, -3.1, 0.5, 2.8, 1.1, -1.4, -6.0])
days = np.array(["Дс", "Сс", "Ср", "Бс", "Жм", "Сб", "Жс"])
print("Фаренгейт:", np.round(t * 9 / 5 + 32, 1))
print("Ең жылы:", days[t.argmax()], "Ең суық:", days[t.argmin()])
print("Аязды күндер:", days[t < 0], "саны:", (t < 0).sum())
print("Күндік өзгеріс:", np.round(np.diff(t), 1))
```

**4-тапсырма**
```python
import numpy as np

prices = np.array([350, 1200, 780, 2500, 90])
qty = np.array([40, 5, 12, 3, 100])
income = prices * qty
print("Табыс:", income)
print("Жалпы:", income.sum())
print("Жаңа бағалар:", np.where(prices > 1000, prices * 0.9, prices))
print("Ең табысты тауар индексі:", income.argmax())
```

**5-тапсырма**
```python
import numpy as np

m = np.random.default_rng(3).integers(2, 6, size=(4, 5))
print(m)
print("Оқушылар ортасы:", m.mean(axis=1).round(2))
print("Пәндер ортасы:", m.mean(axis=0).round(2))
print("«2» алғандар:", np.where((m == 2).any(axis=1))[0])
print("Екпінділер:", np.where((m >= 4).all(axis=1))[0])
```

**6-тапсырма**
```python
import numpy as np

rng = np.random.default_rng(10)
changes = rng.normal(0, 1.5, 30)
rate = 510 + np.cumsum(changes)
days = np.arange(1, 31)
print(f"Макс: {rate.max():.2f} ({days[rate.argmax()]}-күн), мин: {rate.min():.2f} ({days[rate.argmin()]}-күн)")
print(f"Орташа: {rate.mean():.2f}")
print("Өскен күндер:", (np.diff(rate) > 0).sum())
print(f"Ең үлкен өсу: {np.diff(rate).max():.2f}")
print("7 күндік жылжымалы орташа:", np.convolve(rate, np.ones(7) / 7, mode="valid").round(1))
```

**7-тапсырма**
```python
import numpy as np

m = np.random.default_rng(3).integers(2, 6, size=(4, 5)).astype(float)
overall = (m - m.min()) / (m.max() - m.min())
lo, hi = m.min(axis=0, keepdims=True), m.max(axis=0, keepdims=True)
per_subject = (m - lo) / np.where(hi - lo == 0, 1, hi - lo)   # нөлге бөлуден қорғау
print(overall.round(2))
print(per_subject.round(2))
```
Түсіндірме: бірінші — барлық бағаны ортақ шкалаға келтіреді; екінші — әр пән ішінде оқушыларды салыстырады (пәннің қиындығы ескеріледі).

**8-тапсырма**
```python
import time
import numpy as np

print(f"{'n':>10} {'тізім, с':>10} {'NumPy, с':>10}")
for n in (1_000, 10_000, 100_000, 1_000_000):
    lst, arr = list(range(n)), np.arange(n, dtype=np.int64)
    t = time.perf_counter(); sum(x * x for x in lst); tl = time.perf_counter() - t
    t = time.perf_counter(); (arr * arr).sum(); tn = time.perf_counter() - t
    print(f"{n:>10} {tl:>10.5f} {tn:>10.5f}")
```

**9-тапсырма**
```python
import numpy as np

def step(field):
    neighbors = sum(np.roll(np.roll(field, dy, 0), dx, 1)
                    for dy in (-1, 0, 1) for dx in (-1, 0, 1) if (dy, dx) != (0, 0))
    return ((neighbors == 3) | ((field == 1) & (neighbors == 2))).astype(int)

field = np.random.default_rng(5).integers(0, 2, size=(10, 10))
for gen in range(5):
    print(f"{gen}-ұрпақ:")
    print("\n".join("".join("█" if c else "·" for c in row) for row in field))
    field = step(field)
```

**10-тапсырма**
```python
import numpy as np

rng = np.random.default_rng(0)
for n in (1_000, 100_000, 10_000_000):
    pts = rng.random((n, 2))
    inside = ((pts ** 2).sum(axis=1) <= 1).mean()
    print(f"n = {n:>10}: π ≈ {4 * inside:.5f}, қате = {abs(4 * inside - np.pi):.5f}")
```

</details>
