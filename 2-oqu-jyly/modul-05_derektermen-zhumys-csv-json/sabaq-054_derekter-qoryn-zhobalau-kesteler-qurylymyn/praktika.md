# Сабақ 54. Практикалық тапсырмалар: деректер қорын жобалау

Бұл сабақта негізгі құрал — қалам, қағаз және ойлау. Схемаларды қағазда, тақтада немесе dbdiagram.io / drawdb.app сайттарында сызуға болады. Python тапсырмалары — жобаны тексеруге арналған.

## Базалық деңгей

### 1-тапсырма. Байланыс түрін анықта

Әр жұп үшін байланыс түрін (1:1, 1:N, M:N) анықта және сыртқы кілт қай кестеде болатынын (немесе аралық кесте керектігін) жаз:

| № | Мәндер жұбы |
|---|---|
| 1 | Облыс — Қала |
| 2 | Азамат — Жеке куәлік |
| 3 | Оқушы — Пән |
| 4 | Автор — Кітап |
| 5 | Ойыншы — Ойын нәтижесі (сессия) |
| 6 | Тапсырыс — Тауар (Kaspi дүкені) |
| 7 | Футбол командасы — Ойыншы (бір маусымда) |
| 8 | Мектеп — Директор |

### 2-тапсырма. Мәндер мен атрибуттарды тап

Мәтінді оқып, мәндерді (кестелерді), әрқайсысының атрибуттарын (өрістерін) және байланыстарды жаз:

> «Мектеп кітапханасы кітаптарды есепке алады. Әр кітаптың атауы, авторы, шыққан жылы және бірнеше данасы бар; әр дананың инвентарлық нөмірі болады. Оқырмандар — мектеп оқушылары: аты-жөні, сыныбы. Кітапханашы қай дананы қай оқушыға қай күні бергенін және қашан қайтарылғанын белгілейді.»

### 3-тапсырма. Кестені нормализацияла

Төмендегі кестені 3НФ-ке дейін бөл. Әр қадамда қандай мәселе шешілгенін жаз.

| order_no | date | customer | customer_phone | products | total |
|---|---|---|---|---|---|
| 101 | 2027-01-10 | Әсем Т. | +77011112233 | Дәптер ×10, Қалам ×5 | 2100 |
| 102 | 2027-01-11 | Ерлан Қ. | +77025556677 | Қалам ×2 | 240 |
| 103 | 2027-01-11 | Әсем Т. | +77011112233 | Сызғыш ×1 | 180 |

Кеңес: `customers`, `products`, `orders`, `order_items` кестелері шығуы керек. `total` өрісін сақтау керек пе — ойлан.

### 4-тапсырма. ER-диаграмманы оқы

```text
+------------+        +------------+        +-------------+
|  regions   |1      N|   cities   |1      N|  schools    |
+------------+--------+------------+--------+-------------+
| PK id      |        | PK id      |        | PK id       |
|    name    |        |    name    |        |    number   |
+------------+        | FK region_id|       |    name     |
                      +------------+        | FK city_id  |
                                            +-------------+
```

Сұрақтар:
1. Неше кесте, неше байланыс бар?
2. «Алматы облысындағы барлық мектептерді» табу үшін қай кестелерді қолдану керек?
3. Бір қала екі облысқа тиесілі бола ала ма? Неге?
4. Ауылдық мектептерді (қалада емес) қосу үшін схеманы қалай өзгертер едің?

## Кеңейтілген деңгей

### 5-тапсырма. «Үйірмелер орталығы» (топпен)

Теориядағы пәндік аймақты кеңейт:
- әр үйірменің **кестесі** (апта күні, басталу уақыты, кабинет) бар — бір үйірме аптасына бірнеше рет;
- оқушылардың **сабаққа қатысуы** белгіленеді (күні, келді/келмеді);
- үйірме соңында оқушы **сертификат** алады (нөмірі, күні).

Толық ER-диаграмма сыз (кемінде 6 кесте), әр өрістің типін, PK, FK, `UNIQUE`, `NOT NULL` шектеулерін белгіле. Тапсырыс берушінің мына сұрақтарына схема бойынша жауап табу жолын көрсет:
1. Дүйсенбі күні 204-кабинетте қандай үйірмелер бар?
2. Айгерім қаңтарда Python үйірмесіне неше рет келмеді?
3. Ермекова Г.С. жүргізетін үйірмелердің оқушылар саны қанша?

### 6-тапсырма. Схеманы Python-мен тексеру

Өз жобаңды (5-тапсырма) теориядағыдай сөздік түрінде жаз және тексеретін бағдарлама құр:
- әр кестеде `pk` бар және ол `fields` ішінде;
- әр FK бар кестеге сілтейді;
- аралық кестелерді (FK саны ≥ 2) тауып шығар;
- кестелерді **құру ретін** анықта: сілтеме жасалатын кесте сілтейтін кестеден бұрын тұруы керек.

## ★ Олимпиадалық / шығармашылық тапсырма

### 7-тапсырма. Құру ретін автоматты анықтау (топологиялық сұрыптау)

Кестелер мен олардың FK тәуелділіктері берілген. Кестелерді құрудың дұрыс ретін табатын функция жаз. Егер тәуелділіктер «шеңбер» құраса (А → Б → А), «Циклдік тәуелділік!» деп шығар.

```text
deps = {"enrollments": ["students", "clubs"], "clubs": ["teachers", "rooms"],
        "students": [], "teachers": [], "rooms": []}
Шығару:
Құру реті: students, teachers, rooms, clubs, enrollments
```

### 8-тапсырма. Өз жобаң

Жыл бойы орындалатын жобаңа (8-сабақта талқыланған идеялар) деректер қоры керек болса, оның толық ER-диаграммасын сыз. Жоба README-іне диаграмманың суретін қос.

## Үй тапсырмасы

1. 2-тапсырмадағы кітапхана схемасын аяқта: ER-диаграмма + әр кестенің SQL `CREATE TABLE` сипаттамасы (теориядағы үлгі бойынша).
2. Ойлан: «Оқушылар базасы» (62–63-сабақтар) қосымшасы үшін қандай кестелер керек? 3–4 кесте ұсын.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**

| № | Жұп | Түрі | Жүзеге асыру |
|---|---|---|---|
| 1 | Облыс — Қала | 1:N | `cities.region_id` |
| 2 | Азамат — Жеке куәлік | 1:1 (бір уақытта бір жарамды куәлік) | `id_cards.citizen_id UNIQUE` |
| 3 | Оқушы — Пән | M:N | аралық `student_subjects` (немесе `grades`) |
| 4 | Автор — Кітап | M:N (бір кітаптың бірнеше авторы болуы мүмкін) | аралық `book_authors` |
| 5 | Ойыншы — Сессия | 1:N | `sessions.player_id` |
| 6 | Тапсырыс — Тауар | M:N | аралық `order_items` (саны, бағасы) |
| 7 | Команда — Ойыншы (бір маусым) | 1:N | `players.team_id` (бірнеше маусым болса — M:N аралық `contracts`) |
| 8 | Мектеп — Директор | 1:1 | `schools.director_id UNIQUE` |

**2-тапсырма.** Мәндер: `books` (id, title, author, year), `copies` (id, inv_no UNIQUE, book_id FK), `readers` (id, last_name, first_name, class), `loans` (id, copy_id FK, reader_id FK, issued_date, returned_date NULL). Байланыстар: books 1:N copies; copies 1:N loans; readers 1:N loans. (Оқырман мен дана арасындағы M:N — `loans` арқылы.) Қосымша: авторлар бірнеше болса, `authors` + `book_authors`.

**3-тапсырма.**
- 1НФ: `products` ұяшығындағы тізімді жолдарға бөлеміз: (order_no, product, qty).
- 2НФ: `date`, `customer` тек `order_no`-ға тәуелді → `orders(order_no, date, customer...)` және `order_items(order_no, product, qty)`.
- 3НФ: `customer_phone` тапсырысқа емес, клиентке тәуелді → `customers(id, name, phone)`, `orders.customer_id`. Тауар бағасы → `products(id, name, price)`.
- Нәтиже: `customers(id, name, phone)`, `products(id, name, price)`, `orders(id, date, customer_id)`, `order_items(id, order_id, product_id, qty, price)`.
- `total` — есептелетін мән, оны сақтамаған дұрыс (немесе тек тарихи бағаны `order_items.price` ретінде сақтау: тауар бағасы кейін өзгеруі мүмкін).

**4-тапсырма.** 1) 3 кесте, 2 байланыс (1:N). 2) `regions` → `cities` → `schools` (`region.name = 'Алматы облысы'`, cities арқылы schools). 3) Жоқ: `cities` кестесінде бір ғана `region_id` өрісі бар. 4) `settlements` (елді мекен) деген жалпы кесте жасап, `type` өрісін (қала/ауыл) қосу немесе `schools`-қа `village_id` қосу.

**5-тапсырма (үлгі жауап).**

```sql
CREATE TABLE teachers (id INTEGER PRIMARY KEY, last_name TEXT NOT NULL, first_name TEXT NOT NULL, phone TEXT UNIQUE);
CREATE TABLE rooms (id INTEGER PRIMARY KEY, number TEXT NOT NULL UNIQUE, seats INTEGER);
CREATE TABLE clubs (id INTEGER PRIMARY KEY, title TEXT NOT NULL UNIQUE, teacher_id INTEGER REFERENCES teachers(id));
CREATE TABLE schedule (
    id INTEGER PRIMARY KEY,
    club_id INTEGER NOT NULL REFERENCES clubs(id),
    room_id INTEGER NOT NULL REFERENCES rooms(id),
    weekday INTEGER CHECK (weekday BETWEEN 1 AND 7),
    start_time TEXT NOT NULL
);
CREATE TABLE students (id INTEGER PRIMARY KEY, last_name TEXT NOT NULL, first_name TEXT NOT NULL, birth_date TEXT);
CREATE TABLE enrollments (
    id INTEGER PRIMARY KEY,
    student_id INTEGER NOT NULL REFERENCES students(id),
    club_id INTEGER NOT NULL REFERENCES clubs(id),
    enrolled TEXT,
    UNIQUE (student_id, club_id)
);
CREATE TABLE attendance (
    id INTEGER PRIMARY KEY,
    enrollment_id INTEGER NOT NULL REFERENCES enrollments(id),
    lesson_date TEXT NOT NULL,
    present INTEGER NOT NULL DEFAULT 1,
    UNIQUE (enrollment_id, lesson_date)
);
CREATE TABLE certificates (
    id INTEGER PRIMARY KEY,
    enrollment_id INTEGER NOT NULL UNIQUE REFERENCES enrollments(id),
    cert_no TEXT NOT NULL UNIQUE,
    issued TEXT
);
```

Сұрақтарға жол: 1) `schedule` (weekday = 1) + `rooms` (number = '204') + `clubs`. 2) `students` → `enrollments` (Python үйірмесі) → `attendance` (қаңтар, present = 0) → санау. 3) `teachers` → `clubs` → `enrollments` → санау. `certificates` мен `enrollments` — 1:1.

**6-тапсырма**

```python
schema = {
    "teachers": {"pk": "id", "fields": ["id", "last_name", "phone"], "fk": {}},
    "rooms": {"pk": "id", "fields": ["id", "number", "seats"], "fk": {}},
    "clubs": {"pk": "id", "fields": ["id", "title", "teacher_id"], "fk": {"teacher_id": "teachers"}},
    "schedule": {"pk": "id", "fields": ["id", "club_id", "room_id", "weekday"],
                 "fk": {"club_id": "clubs", "room_id": "rooms"}},
    "students": {"pk": "id", "fields": ["id", "last_name"], "fk": {}},
    "enrollments": {"pk": "id", "fields": ["id", "student_id", "club_id"],
                    "fk": {"student_id": "students", "club_id": "clubs"}},
}

ok = True
for table, info in schema.items():
    if info["pk"] not in info["fields"]:
        print(f"ҚАТЕ: {table} кестесінде PK өрісі жоқ")
        ok = False
    for field, target in info["fk"].items():
        if target not in schema:
            print(f"ҚАТЕ: {table}.{field} жоқ кестеге ({target}) сілтейді")
            ok = False
print("Схема дұрыс" if ok else "Схемада қате бар")
print("Аралық кестелер:", [t for t, i in schema.items() if len(i["fk"]) >= 2])

created = []
while len(created) < len(schema):
    for table, info in schema.items():
        if table not in created and all(t in created for t in info["fk"].values()):
            created.append(table)
print("Құру реті:", ", ".join(created))
```

Нәтижесі:

```text
Схема дұрыс
Аралық кестелер: ['schedule', 'enrollments']
Құру реті: teachers, rooms, clubs, schedule, students, enrollments
```

Ескерту: `schedule` — формальды түрде екі FK бар кесте (clubs мен rooms арасындағы M:N), бірақ оның өз мағынасы бар (сабақ уақыты). Оқушылармен талқылаңыз: «аралық кесте» өз атрибуттары бар толыққанды мәнге айнала алады.

Жоғарыдағы `while` циклі циклдік тәуелділік болса шексіз айналады — оны 7-тапсырма шешеді.

**7-тапсырма**

```python
def creation_order(deps):
    """Кестелерді тәуелділік бойынша сұрыптайды (Кан алгоритмі)."""
    order = []
    remaining = dict(deps)
    while remaining:
        ready = [t for t, d in remaining.items() if all(x in order for x in d)]
        if not ready:
            return None  # ешбірін құруға болмайды → циклдік тәуелділік
        for t in ready:
            order.append(t)
            del remaining[t]
    return order


deps = {"enrollments": ["students", "clubs"], "clubs": ["teachers", "rooms"],
        "students": [], "teachers": [], "rooms": []}
result = creation_order(deps)
print("Құру реті:", ", ".join(result))

bad = {"a": ["b"], "b": ["a"]}
print("Құру реті:", ", ".join(creation_order(bad)) if creation_order(bad) else "Циклдік тәуелділік!")
```

Нәтижесі:

```text
Құру реті: students, teachers, rooms, clubs, enrollments
Құру реті: Циклдік тәуелділік!
```

</details>
