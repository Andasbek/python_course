# Сабақ 12. Объектілер тізімі. Объектілер жиынтығымен жұмыс.

## Тақырыптар

1. Объектілер тізімін құру
2. Тізімді аралау және әдістерді шақыру
3. Жиынтықпен жұмыстың негізгі алгоритмдері
4. Объектілерді сұрыптау
5. Объектілер сөздігі: кілт бойынша жылдам қатынау
6. Контейнер класы: жиынтықты басқаратын объект
7. Объектілер арасындағы байланыс
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Объектілер тізімін құру

Төмендегі барлық мысалдарда қолданылатын класс:

```python
class Student:
    """Үйірме оқушысы."""

    def __init__(self, name, grade, marks=None):
        self.name = name
        self.grade = grade
        self.marks = marks if marks is not None else []

    def average(self):
        """Орташа бағаны қайтарады."""
        return sum(self.marks) / len(self.marks) if self.marks else 0

    def info(self):
        """Қысқаша ақпарат жолы."""
        return f"{self.name} ({self.grade}-сынып): {self.average():.2f}"
```

Тізім құрудың тәсілдері:

```python
# 1) Тікелей
students = [
    Student("Әлия", 8, [5, 5, 4]),
    Student("Бекзат", 9, [4, 3, 4]),
    Student("Дана", 8, [5, 4, 5]),
    Student("Ерлан", 10, [3, 4, 3]),
    Student("Жанар", 9, [5, 5, 5]),
]

# 2) append арқылы біртіндеп
students.append(Student("Тимур", 10, [4, 4, 5]))

# 3) Басқа деректерден (кортеждер тізімі) — тізім генераторымен
raw = [("Айдос", 7, [4, 5]), ("Мадина", 7, [5, 5])]
students += [Student(name, grade, marks) for name, grade, marks in raw]

print(len(students))
```
Нәтижесі:
```text
8
```

Тізімде объектілердің өзі емес, оларға **сілтемелер** сақталады: `students[0]` және `aliya` айнымалысы бір объектіні көрсетсе, біреуі арқылы өзгерту екіншісінде де көрінеді.

## 2. Тізімді аралау және әдістерді шақыру

```python
for student in students:
    print(student.info())
```
Нәтижесі:
```text
Әлия (8-сынып): 4.67
Бекзат (9-сынып): 3.67
Дана (8-сынып): 4.67
Ерлан (10-сынып): 3.33
Жанар (9-сынып): 5.00
Тимур (10-сынып): 4.33
Айдос (7-сынып): 4.50
Мадина (7-сынып): 5.00
```

Цикл айнымалысы (`student`) — кезекпен әр объект. Атрибуттарға да, әдістерге де нүкте арқылы қатынаймыз. Нөмірмен шығару үшін — `enumerate`:

```python
for number, student in enumerate(students[:3], start=1):
    print(number, student.name)
```

**Барлық объектіні өзгерту** — мысалы, әр оқушыға үйірмеге қатысқаны үшін 5 қою:
```python
for student in students:
    student.marks.append(5)
print(students[1].marks)
```
Нәтижесі:
```text
[4, 3, 4, 5]
```

## 3. Жиынтықпен жұмыстың негізгі алгоритмдері

Келесі мысалдар бастапқы 8 оқушыға арналған (жоғарыдағы «барлығына 5 қою» мысалынсыз).

### 3.1. Іздеу (бірінші сәйкес объектіні табу)
```python
def find_by_name(students, name):
    """Аты бойынша оқушыны қайтарады; табылмаса None."""
    for student in students:
        if student.name.lower() == name.lower():
            return student
    return None


found = find_by_name(students, "дана")
if found:
    print("Табылды:", found.info())
else:
    print("Мұндай оқушы жоқ")
```
Нәтижесі:
```text
Табылды: Дана (8-сынып): 4.67
```
Маңызды: табылмаса, `None` қайтарамыз, сондықтан нәтижені **тексермей** әдісін шақырма.

### 3.2. Сүзу (шартқа сай барлық объектілер)
```python
# Цикл арқылы
eighth = []
for s in students:
    if s.grade == 8:
        eighth.append(s)

# Тізім генераторымен (қысқа)
excellent = [s for s in students if s.average() >= 4.5]
print([s.name for s in eighth])
print([s.name for s in excellent])
```
Нәтижесі:
```text
['Әлия', 'Дана']
['Әлия', 'Дана', 'Жанар', 'Айдос', 'Мадина']
```

### 3.3. Санау, қосынды, орташа
```python
count_excellent = sum(1 for s in students if s.average() >= 4.5)
class_avg = sum(s.average() for s in students) / len(students)
print("Үздіктер саны:", count_excellent)
print(f"Топтың орташа бағасы: {class_avg:.2f}")
```
Нәтижесі:
```text
Үздіктер саны: 5
Топтың орташа бағасы: 4.40
```

### 3.4. Ең үлкен / ең кіші (key параметрі)
`max()` объектілерді өздігінен салыстыра алмайды — **неге** қарап салыстыру керегін `key` арқылы айтамыз:

```python
best = max(students, key=lambda s: s.average())
youngest = min(students, key=lambda s: s.grade)
print("Үздік:", best.name)
print("Ең кіші сынып:", youngest.name)
```
Нәтижесі:
```text
Үздік: Жанар
Ең кіші сынып: Айдос
```
`lambda s: s.average()` — «оқушыны оның орташа бағасы бойынша салыстыр» деген шағын анонимді функция. Тең мәндер болса, `max` біріншісін қайтарады (Жанар мен Мадинаның екеуі де 5.00).

Цикл арқылы да жазуға болады (алгоритмді түсіну үшін):
```python
best = students[0]
for s in students[1:]:
    if s.average() > best.average():
        best = s
print(best.name)
```

### 3.5. Топтау (сынып бойынша)
```python
by_grade = {}
for s in students:
    by_grade.setdefault(s.grade, []).append(s.name)
for grade in sorted(by_grade):
    print(grade, by_grade[grade])
```
Нәтижесі:
```text
7 ['Айдос', 'Мадина']
8 ['Әлия', 'Дана']
9 ['Бекзат', 'Жанар']
10 ['Ерлан', 'Тимур']
```

### 3.6. Жою
```python
# Шарт бойынша жою — жаңа тізім құрамыз (циклде remove жасамаймыз!)
students = [s for s in students if s.grade != 10]

# Бір нақты объектіні жою
aidos = find_by_name(students, "Айдос")
if aidos:
    students.remove(aidos)
print(len(students))
```
Нәтижесі:
```text
5
```

## 4. Объектілерді сұрыптау

```python
# Орташа баға бойынша кему ретімен
for s in sorted(students, key=lambda s: s.average(), reverse=True):
    print(s.info())
```
Нәтижесі:
```text
Жанар (9-сынып): 5.00
Мадина (7-сынып): 5.00
Әлия (8-сынып): 4.67
Дана (8-сынып): 4.67
Бекзат (9-сынып): 3.67
```

- `sorted(тізім, key=...)` — **жаңа** сұрыпталған тізім қайтарады, бастапқысы өзгермейді;
- `тізім.sort(key=...)` — тізімнің **өзін** сұрыптайды;
- бірнеше критерий бойынша — `key` кортеж қайтарады: `key=lambda s: (s.grade, s.name)` — алдымен сынып, сыныпта — аты бойынша;
- бір критерий кему, екіншісі өсу ретімен: `key=lambda s: (-s.average(), s.name)`.

```python
for s in sorted(students, key=lambda s: (s.grade, s.name)):
    print(s.grade, s.name)
```
Нәтижесі:
```text
7 Мадина
8 Дана
8 Әлия
9 Бекзат
9 Жанар
```
(Ескерту: стандартты сұрыптау Unicode коды бойынша жүреді, сондықтан қазақтың «ә», «ө», «ү» әріптері орыс-латын әліпбиінен кейін тұрады: «Дана» «Әлия»-дан бұрын.)

## 5. Объектілер сөздігі: кілт бойынша жылдам қатынау

Тізімде оқушыны табу үшін бәрін аралау керек. Егер объектіні **бірегей кілт** (ID, логин, ISBN, нөмір) бойынша жиі іздесек — **сөздік** ыңғайлы:

```python
class Book:
    """Кітап."""

    def __init__(self, isbn, title, author, year):
        self.isbn = isbn
        self.title = title
        self.author = author
        self.year = year
        self.is_available = True

    def info(self):
        status = "бар" if self.is_available else "берілген"
        return f"[{self.isbn}] «{self.title}» — {self.author}, {self.year} ({status})"


catalog = {}
for book in [
    Book("978-601-01", "Абай жолы", "М. Әуезов", 1942),
    Book("978-601-02", "Қан мен тер", "Ә. Нұрпейісов", 1961),
    Book("978-601-03", "Көшпенділер", "І. Есенберлин", 1976),
]:
    catalog[book.isbn] = book

print(catalog["978-601-02"].info())
print("978-601-99" in catalog)
for isbn, book in catalog.items():
    print(isbn, book.title)
```
Нәтижесі:
```text
[978-601-02] «Қан мен тер» — Ә. Нұрпейісов, 1961 (бар)
False
978-601-01 Абай жолы
978-601-02 Қан мен тер
978-601-03 Көшпенділер
```

| Тізім `[obj, obj]` | Сөздік `{кілт: obj}` |
|---|---|
| ретімен сақтау, нөмір бойынша қатынау | бірегей кілт бойынша **жылдам** іздеу |
| бірдей объектілер болуы мүмкін | кілт қайталанбайды |
| іздеу — бүкіл тізімді аралау | іздеу — бірден `d[key]` |
| мысал: ойындағы жаулар, кезек | мысал: ID бойынша оқушылар, ISBN бойынша кітаптар |

## 6. Контейнер класы: жиынтықты басқаратын объект

Тізімді және оған арналған функцияларды бөлек ұстаудың орнына оларды **бір класқа** біріктіреміз. Мұндай класс **контейнер** (немесе менеджер) деп аталады: «Кітапханада кітаптар **бар**» (композиция).

```text
┌──────────────────────────┐          ┌────────────────────┐
│         Library          │ 1     *  │       Book         │
├──────────────────────────┤◆────────►├────────────────────┤
│ name: str                │          │ isbn, title,       │
│ books: dict[str, Book]   │          │ author, year,      │
├──────────────────────────┤          │ is_available       │
│ add_book(book)           │          ├────────────────────┤
│ find_by_author(author)   │          │ info()             │
│ give_out(isbn)           │          └────────────────────┘
│ return_book(isbn)        │
│ available_books()        │
│ report()                 │
└──────────────────────────┘
```

```python
class Library:
    """Кітаптар жиынтығын басқаратын кітапхана."""

    def __init__(self, name):
        self.name = name
        self.books = {}            # isbn → Book

    def add_book(self, book):
        """Кітап қосады (ISBN қайталанса — қоспайды)."""
        if book.isbn in self.books:
            print(f"ISBN {book.isbn} бұрыннан бар")
            return False
        self.books[book.isbn] = book
        return True

    def find_by_author(self, author):
        """Автордың барлық кітаптарын тізім ретінде қайтарады."""
        return [b for b in self.books.values() if author.lower() in b.author.lower()]

    def give_out(self, isbn):
        """Кітапты оқырманға береді."""
        book = self.books.get(isbn)
        if book is None:
            print("Мұндай кітап жоқ")
            return False
        if not book.is_available:
            print(f"«{book.title}» қазір берілген")
            return False
        book.is_available = False
        return True

    def return_book(self, isbn):
        """Кітапты қайтарып алады."""
        book = self.books.get(isbn)
        if book and not book.is_available:
            book.is_available = True
            return True
        return False

    def available_books(self):
        """Қолжетімді кітаптарды жыл бойынша сұрыптап қайтарады."""
        free = [b for b in self.books.values() if b.is_available]
        return sorted(free, key=lambda b: b.year)

    def report(self):
        """Кітапхана туралы есепті шығарады."""
        total = len(self.books)
        free = len(self.available_books())
        print(f"«{self.name}»: барлығы {total}, қолжетімді {free}, берілген {total - free}")


lib = Library("Мектеп кітапханасы")
lib.add_book(Book("978-601-01", "Абай жолы", "М. Әуезов", 1942))
lib.add_book(Book("978-601-02", "Қан мен тер", "Ә. Нұрпейісов", 1961))
lib.add_book(Book("978-601-03", "Көшпенділер", "І. Есенберлин", 1976))
lib.add_book(Book("978-601-04", "Қараш-Қараш оқиғасы", "М. Әуезов", 1927))
lib.add_book(Book("978-601-01", "Көшірме", "Белгісіз", 2000))

lib.give_out("978-601-01")
lib.give_out("978-601-01")
for book in lib.find_by_author("Әуезов"):
    print(book.info())
lib.report()
```
Нәтижесі:
```text
ISBN 978-601-01 бұрыннан бар
«Абай жолы» қазір берілген
[978-601-01] «Абай жолы» — М. Әуезов, 1942 (берілген)
[978-601-04] «Қараш-Қараш оқиғасы» — М. Әуезов, 1927 (бар)
«Мектеп кітапханасы»: барлығы 4, қолжетімді 3, берілген 1
```

Контейнер класының артықшылықтары:
- жиынтықпен жұмыстың **барлық ережесі бір жерде** (қайталанатын ISBN-ді қоспау, берілген кітапты қайта бермеу);
- негізгі бағдарлама қысқа әрі түсінікті: `lib.give_out(...)`;
- кейін деректерді файлға, деректер қорына сақтауды тек осы класс ішінде өзгертеміз.

## 7. Объектілер арасындағы байланыс

Объектілер бір-біріне сілтеме жасай алады. Мысалы, кітапты кім алғанын сақтайық:

```python
class Reader:
    """Кітапхана оқырманы."""

    def __init__(self, name):
        self.name = name
        self.borrowed = []          # оқырмандағы Book объектілері

    def take(self, book):
        if book.is_available:
            book.is_available = False
            book.reader = self      # кітап «өз оқырманын» біледі
            self.borrowed.append(book)


reader = Reader("Әлия")
book = Book("978-601-05", "Менің атым Қожа", "Б. Соқпақбаев", 1957)
reader.take(book)
print(book.reader.name, [b.title for b in reader.borrowed])
```
Нәтижесі:
```text
Әлия ['Менің атым Қожа']
```

## Жиі кездесетін қателер

**1. Объектіні тікелей `print` жасау.**
```text
print(students)  →  [<__main__.Student object at 0x...>, ...]
```
`info()` әдісінің нәтижесін немесе атрибуттарды шығар, не болмаса класта объектінің «әдемі» жолдық көрінісін беретін `__str__` әдісін анықта.

**2. `key`-сіз `max()` немесе `sorted()`.**
```text
max(students)  →  TypeError: '>' not supported between instances of 'Student' and 'Student'
```
Шешімі: `max(students, key=lambda s: s.average())`.

**3. `key`-ге әдістің нәтижесін емес, әдістің өзін беру.**
`key=lambda s: s.average` (жақшасыз) — әдістерді салыстыруға тырысады → `TypeError`. Дұрысы: `s.average()`.

**4. Табылмаған (`None`) объектінің әдісін шақыру.**
```text
find_by_name(students, "Асқар").info()  →  AttributeError: 'NoneType' object has no attribute 'info'
```
Алдымен `if found:` арқылы тексер.

**5. Циклде аралап жатқан тізімнен жою** — кейбір объектілер өткізіліп кетеді. Сүзу арқылы жаңа тізім құр.

**6. Бір объектіні тізімге бірнеше рет қосу** (сілтеме көшіріледі):
```python
s = Student("Әлия", 8)
group = [s] * 3          # үш сілтеме — БІР объектіге
group[0].name = "Дана"
print([x.name for x in group])   # ['Дана', 'Дана', 'Дана']
```
Дұрысы: `[Student(f"Оқушы {i}", 8) for i in range(3)]`.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Объектілер тізімі | Элементтері объектілер болатын тізім |
| Объектілер сөздігі | `{кілт: объект}` — бірегей кілт бойынша қатынау |
| Іздеу | Шартқа сай бірінші объектіні табу |
| Сүзу (фильтрлеу) | Шартқа сай барлық объектілерді таңдау |
| Агрегация | Жиынтық бойынша бір мән есептеу (қосынды, орташа, макс) |
| `key` параметрі | Сұрыптау/салыстыру критерийін беретін функция |
| `lambda` | Бір жолдық анонимді функция |
| Контейнер класы | Объектілер жиынтығын сақтап, басқаратын класс |
| Композиция | «Бар» қатынасы: кітапханада кітаптар бар |

## Ескерту

- `sorted()` және `list.sort()` тұрақты (stable) сұрыптау жасайды: кілттері тең объектілер бастапқы ретін сақтайды. Сондықтан бірнеше критерий бойынша кезең-кезеңімен сұрыптауға болады.
- `lambda` орнына `operator` модулінің функцияларын қолдануға болады: `sorted(books, key=operator.attrgetter("year"))` — бұл қысқа әрі сәл жылдамырақ. Құжаттама: https://docs.python.org/3/howto/sorting.html.
- `max()` және `min()` бос тізімде `ValueError` шығарады; оны болдырмау үшін `default` параметрі бар: `max(students, key=..., default=None)`.
- Сөздіктегі `in` арқылы іздеу орта есеппен тұрақты уақытта (O(1)) орындалады, ал тізімдегі іздеу — элементтер санына пропорционал (O(n)). Объектілер көп болғанда бұл айырма елеулі.
- Қазақ әріптерін әліпби бойынша дұрыс сұрыптау үшін өз кілтіңді жазу (әріптердің ретін беретін жол арқылы) немесе `locale`/`PyICU` кітапханаларын қолдану керек.
