# Урок 58. Сортировка и группировка: ORDER BY, GROUP BY, агрегатные функции.

## Темы

1. Рабочая база: результаты олимпиады
2. `ORDER BY` — сортировка
3. Агрегатные функции
4. `GROUP BY` — группировка
5. `HAVING` — фильтрация групп
6. Полная структура предложения `SELECT`
7. Сравнение SQL и Python
8. Частые ошибки
9. Основные понятия

## 1. Рабочая база: результаты олимпиады

```python
import sqlite3

RESULTS = [
    ("Айгерим", "9А", "Алматы", "информатика", 95),
    ("Айгерим", "9А", "Алматы", "математика", 82),
    ("Бауыржан", "9А", "Қонаев", "информатика", 78),
    ("Даулет", "9Б", "Алматы", "физика", 88),
    ("Даулет", "9Б", "Алматы", "информатика", 91),
    ("Сымбат", "9Б", "Алматы", "математика", 100),
    ("Ерлан", "9А", "Талғар", "физика", 64),
    ("Мадина", "9Б", "Алматы", "информатика", 91),
    ("Темирлан", "10А", "Қонаев", "математика", 73),
    ("Аружан", "10А", "Алматы", "физика", None),     # результата ещё нет
]

conn = sqlite3.connect("olympiad.db")
conn.executescript("""
    DROP TABLE IF EXISTS results;
    CREATE TABLE results (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        class TEXT,
        city TEXT,
        subject TEXT,
        score INTEGER
    );
""")
conn.executemany("INSERT INTO results (name, class, city, subject, score) VALUES (?, ?, ?, ?, ?)", RESULTS)
conn.commit()
conn.close()
print("olympiad.db: 10 результатов")
```

Результат:

```text
olympiad.db: 10 результатов
```

## 2. `ORDER BY` — сортировка

По умолчанию порядок результата `SELECT` **не гарантирован** (чаще всего это порядок добавления, но полагаться на него нельзя). Если нужен определённый порядок — `ORDER BY`.

```sql
SELECT ... FROM ... [WHERE ...] ORDER BY поле1 [ASC|DESC], поле2 [ASC|DESC];
```

- `ASC` — по возрастанию (по умолчанию): 1, 2, 3 / А, Б, В;
- `DESC` — по убыванию: 100, 95, 91 ...

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")

print("По баллу (убывание):")
for name, subject, score in conn.execute(
    "SELECT name, subject, score FROM results WHERE score IS NOT NULL ORDER BY score DESC"
):
    print(f"  {score:>3}  {name} ({subject})")

print("По предмету, внутри — по убыванию балла:")
for subject, name, score in conn.execute(
    "SELECT subject, name, score FROM results WHERE score IS NOT NULL ORDER BY subject, score DESC"
):
    print(f"  {subject:<12} {name:<9} {score}")
conn.close()
```

Результат:

```text
По баллу (убывание):
  100  Сымбат (математика)
   95  Айгерим (информатика)
   91  Даулет (информатика)
   91  Мадина (информатика)
   88  Даулет (физика)
   82  Айгерим (математика)
   78  Бауыржан (информатика)
   73  Темирлан (математика)
   64  Ерлан (физика)
По предмету, внутри — по убыванию балла:
  информатика  Айгерим   95
  информатика  Даулет    91
  информатика  Мадина    91
  информатика  Бауыржан  78
  математика   Сымбат    100
  математика   Айгерим   82
  математика   Темирлан  73
  физика       Даулет    88
  физика       Ерлан     64
```

**Сортировка по нескольким полям:** сначала по первому полю, а если значения первого поля равны — по второму. Это как в телефонной книге: сначала по фамилии, а при одинаковой фамилии — по имени.

> **Внимание:** по умолчанию SQLite сравнивает текст по порядку байтов. Поэтому казахские буквы (Ә, Ғ, Қ, Ң, Ө, Ұ, Ү, Һ, І) могут оказаться не на своём месте в алфавите, а **после** стандартных букв кириллицы. Если нужен точный алфавитный порядок — сортируем в Python со специальным ключом.

### `LIMIT` и `OFFSET` — «топ-N» и страницы

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")
top3 = conn.execute("SELECT name, score FROM results ORDER BY score DESC LIMIT 3").fetchall()
print("Лучшая тройка:", top3)

page_size = 4
for page in (1, 2, 3):
    rows = conn.execute(
        "SELECT name FROM results ORDER BY id LIMIT ? OFFSET ?",
        (page_size, (page - 1) * page_size),
    ).fetchall()
    print(f"Страница {page}:", [r[0] for r in rows])
conn.close()
```

Результат:

```text
Лучшая тройка: [('Сымбат', 100), ('Айгерим', 95), ('Даулет', 91)]
Страница 1: ['Айгерим', 'Айгерим', 'Бауыржан', 'Даулет']
Страница 2: ['Даулет', 'Сымбат', 'Ерлан', 'Мадина']
Страница 3: ['Темирлан', 'Аружан']
```

`OFFSET n` — пропустить первые `n` записей. Так работают кнопки «1, 2, 3 ... следующая страница» на сайтах.

В `ORDER BY score DESC` значения `NULL` в SQLite стоят **в самом конце** (при убывании), а при возрастании — в самом начале.

## 3. Агрегатные функции

**Агрегатная функция** — функция, вычисляющая **одно** итоговое значение из значений многих записей.

| Функция | Что вычисляет | Отношение к `NULL` |
|---|---|---|
| `COUNT(*)` | количество записей | считает все записи |
| `COUNT(поле)` | количество записей с непустым полем | `NULL` не считает |
| `COUNT(DISTINCT поле)` | количество различных значений | |
| `SUM(поле)` | сумму | `NULL` игнорирует |
| `AVG(поле)` | среднее арифметическое | `NULL` игнорирует |
| `MIN(поле)` / `MAX(поле)` | наименьшее / наибольшее | `NULL` игнорирует |

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")
row = conn.execute("""
    SELECT COUNT(*), COUNT(score), COUNT(DISTINCT name),
           SUM(score), ROUND(AVG(score), 1), MIN(score), MAX(score)
    FROM results
""").fetchone()
labels = ["Всего записей", "С результатом", "Разных учеников", "Сумма", "Среднее", "Минимум", "Максимум"]
for label, value in zip(labels, row):
    print(f"{label:<15}: {value}")
conn.close()
```

Результат:

```text
Всего записей  : 10
С результатом  : 9
Разных учеников: 8
Сумма          : 762
Среднее        : 84.7
Минимум        : 64
Максимум       : 100
```

Обрати внимание: `COUNT(*)` = 10, а `COUNT(score)` = 9 — результат Аружан равен `NULL`. `AVG` тоже делил на 9 значений (762 / 9 = 84.67).

`ROUND(значение, n)` — округление до `n` знаков.

Агрегат вместе с `WHERE`:

```sql
SELECT COUNT(*), MAX(score) FROM results WHERE subject = 'информатика';   -- 4 и 95
```

## 4. `GROUP BY` — группировка

`GROUP BY` делит записи на **группы** по одинаковым значениям поля, а агрегатные функции вычисляются **отдельно для каждой группы**.

**Аналогия:** все тетради раскладывают в стопки по классам (группировка), затем считают тетради в каждой стопке (COUNT) или вычисляют среднюю оценку (AVG).

```text
results                         GROUP BY subject              Итог
информатика  95  ─┐
информатика  78   ├─► информатика: 95,78,91,91 ─► AVG=88.8, COUNT=4
информатика  91   │
информатика  91  ─┘
математика   82  ─┐
математика  100   ├─► математика: 82,100,73    ─► AVG=85.0, COUNT=3
математика   73  ─┘
физика       88  ─┐
физика       64   ├─► физика: 88,64,NULL       ─► AVG=76.0, COUNT(score)=2
физика     NULL  ─┘
```

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")
print("По предметам:")
for subject, count, avg, best in conn.execute("""
    SELECT subject, COUNT(score), ROUND(AVG(score), 1), MAX(score)
    FROM results
    GROUP BY subject
    ORDER BY AVG(score) DESC
"""):
    print(f"  {subject:<12} участников: {count}, среднее: {avg}, лучший: {best}")

print("По классам:")
for cls, avg in conn.execute(
    "SELECT class, ROUND(AVG(score), 1) AS avg_score FROM results GROUP BY class ORDER BY avg_score DESC"
):
    print(f"  {cls}: {avg}")
conn.close()
```

Результат:

```text
По предметам:
  информатика  участников: 4, среднее: 88.8, лучший: 95
  математика   участников: 3, среднее: 85.0, лучший: 100
  физика       участников: 2, среднее: 76.0, лучший: 88
По классам:
  9Б: 92.5
  9А: 79.8
  10А: 73.0
```

**Важное правило:** в списке `SELECT` запроса с `GROUP BY` должны быть только (1) поля группировки и (2) агрегатные функции. `SELECT subject, name, AVG(score) ... GROUP BY subject` — `name` чьё это имя? В группе несколько учеников! (SQLite не выдаст ошибку, но вернёт какое-то случайное имя — это логическая ошибка.)

### Группировка по нескольким полям

```sql
SELECT city, subject, COUNT(*) FROM results GROUP BY city, subject;
-- каждая пара (город, предмет) — отдельная группа
```

## 5. `HAVING` — фильтрация групп

`WHERE` фильтрует **записи до** группировки. `HAVING` фильтрует **группы после** группировки, поэтому внутри него можно использовать агрегатные функции.

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")

rows = conn.execute("""
    SELECT name, COUNT(*) AS olympiads, SUM(score) AS total
    FROM results
    GROUP BY name
    HAVING COUNT(*) >= 2
    ORDER BY total DESC
""").fetchall()
print("Участвовали в двух и более олимпиадах:", rows)

rows = conn.execute("""
    SELECT city, ROUND(AVG(score), 1)
    FROM results
    WHERE subject = ?
    GROUP BY city
    HAVING AVG(score) > ?
""", ("информатика", 80)).fetchall()
print("Города со средним баллом по информатике выше 80:", rows)
conn.close()
```

Результат:

```text
Участвовали в двух и более олимпиадах: [('Даулет', 2, 179), ('Айгерим', 2, 177)]
Города со средним баллом по информатике выше 80: [('Алматы', 92.3)]
```

| | `WHERE` | `HAVING` |
|---|---|---|
| Что фильтрует | отдельные записи | группы |
| Когда выполняется | до группировки | после группировки |
| Агрегатные функции | **нельзя** | можно |
| Пример | `WHERE subject = 'физика'` | `HAVING COUNT(*) >= 2` |

## 6. Полная структура предложения `SELECT`

**Порядок записи:**

```sql
SELECT    поля, агрегаты
FROM      таблица
WHERE     условие для записей
GROUP BY  поля группировки
HAVING    условие для групп
ORDER BY  сортировка
LIMIT     количество OFFSET пропуск;
```

**Порядок выполнения** (внутри СУБД):

```text
1. FROM      — из какой таблицы?
2. WHERE     — какие записи остаются?
3. GROUP BY  — как группируем?
4. HAVING    — какие группы остаются?
5. SELECT    — какие столбцы вычисляем?
6. ORDER BY  — как сортируем?
7. LIMIT     — сколько строк выдаём?
```

Поэтому внутри `WHERE` нельзя использовать имя, заданное через `AS` (`avg_score`), — оно ещё не вычислено. А внутри `ORDER BY` — можно.

**Чтобы запомнить:** «**F**ROM — **W**HERE — **G**ROUP — **H**AVING — **S**ELECT — **O**RDER — **L**IMIT».

## 7. Сравнение SQL и Python

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")

# Способ Python: забираем все данные и считаем сами
by_city = {}
for city, score in conn.execute("SELECT city, score FROM results WHERE score IS NOT NULL"):
    by_city.setdefault(city, []).append(score)
python_result = {c: round(sum(s) / len(s), 1) for c, s in by_city.items()}

# Способ SQL: вычисления выполняет база данных
sql_result = dict(conn.execute(
    "SELECT city, ROUND(AVG(score), 1) FROM results WHERE score IS NOT NULL GROUP BY city"
))
print(python_result)
print(sql_result)
print("Одинаковы?", python_result == sql_result)
conn.close()
```

Результат:

```text
{'Алматы': 91.2, 'Қонаев': 75.5, 'Талғар': 64.0}
{'Алматы': 91.2, 'Талғар': 64.0, 'Қонаев': 75.5}
Одинаковы? True
```

(В результате SQL «Қонаев» стоит после «Талғар» — `GROUP BY` расположил группы по порядку байтов, а код буквы «Қ» больше, чем у «Т». Это пример к предупреждению об алфавите выше. При сравнении словарей порядок не важен, поэтому `True`.)

Результат одинаковый, но на миллионе записей способ SQL гораздо эффективнее: данные не перекачиваются с диска в Python, а СУБД оптимизирована для вычислений.

## Частые ошибки

**1. Агрегат внутри `WHERE`.**

```python
import sqlite3

conn = sqlite3.connect("olympiad.db")
try:
    conn.execute("SELECT subject FROM results WHERE AVG(score) > 80 GROUP BY subject")
except sqlite3.OperationalError as e:
    print("Ошибка:", e)
print(conn.execute("SELECT subject FROM results GROUP BY subject HAVING AVG(score) > 80").fetchall())
conn.close()
```

Результат:

```text
Ошибка: misuse of aggregate: AVG()
[('информатика',), ('математика',)]
```

**2. Агрегат и обычное поле без `GROUP BY`.** `SELECT name, MAX(score) FROM results` возвращает одну строку; в SQLite `name` соответствует максимуму (это особенность SQLite, в других СУБД — ошибка). Для понятности: `ORDER BY score DESC LIMIT 1`.

**3. `LIMIT` без `ORDER BY`.** Если для «лучшей тройки» написать `LIMIT 3` и забыть `ORDER BY` — выведутся 3 случайные записи.

**4. Путаница `COUNT(*)` и `COUNT(поле)`** — в полях с `NULL` результат разный.

**5. Деление целых чисел.** В SQLite, если `SUM(score) / COUNT(score)` делит два целых числа, результат **целый** (дробная часть отбрасывается): `762 / 9 = 84`. Правильно: `AVG(score)` или `SUM(score) * 1.0 / COUNT(score)`.

**6. `DESC` действует только на одно поле.** `ORDER BY subject, score DESC` — `subject` по возрастанию, только `score` по убыванию.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| `ORDER BY` | Сортировка результата |
| `ASC` / `DESC` | По возрастанию / по убыванию |
| `LIMIT` / `OFFSET` | Ограничение количества результатов / пропуск первых записей |
| Пагинация | Разбиение большого результата на страницы |
| Агрегатная функция | Функция, вычисляющая один итог из многих значений |
| `COUNT`, `SUM`, `AVG`, `MIN`, `MAX` | Количество, сумма, среднее, минимум, максимум |
| `GROUP BY` | Группировка записей по одинаковым значениям |
| `HAVING` | Фильтрация групп по агрегатному условию |
| `ROUND(x, n)` | Округление числа до n знаков |
| `COUNT(DISTINCT x)` | Количество различных значений |

## Примечание

- Агрегатные функции пропускают `NULL`: `COUNT(score)` считает только заполненные значения, а `COUNT(*)` — все строки; `AVG` делит только на число непустых значений.
- `AVG()` всегда возвращает дробное число (`REAL`); для округления используйте `ROUND(AVG(score), 1)`.
- SQLite разрешает в `SELECT` поля, не входящие в `GROUP BY` и не обёрнутые в агрегат, но их значение берётся из произвольной строки группы — в PostgreSQL такой запрос вызовет ошибку. Пишите переносимый SQL.
- `NULL` при `ORDER BY ... ASC` в SQLite идут первыми; с версии 3.30 порядок можно задать явно: `ORDER BY score DESC NULLS LAST`.
- Сортировка кириллицы через `ORDER BY` идёт по кодам символов, а не по алфавиту (казахские `ә`, `ң`, `ө` окажутся в конце); для правильного порядка сортируют в Python или регистрируют свою функцию сравнения `conn.create_collation()`.
- Документация: https://www.sqlite.org/lang_aggfunc.html и https://www.sqlite.org/lang_select.html
