# Сабақ 19. Практикум: «Кітапхана» және «Мектеп журналы» класс жүйесін жобалау.

## Тақырыптар

1. Класс жүйесін қалай жобалаймыз?
2. Үлгі: «Кітапхана» жүйесі
3. «Мектеп журналы» жүйесінің моделі
4. Жиі кездесетін қателер
5. Негізгі ұғымдар

## 1. Класс жүйесін қалай жобалаймыз?

Бағдарламалау кодтан емес, **ойланудан** басталады. Үлкен есепті шешпес бұрын оны кластарға бөлеміз.

### 1-қадам. Есепті мәтін түрінде жазу

> «Мектеп кітапханасында **кітаптар** бар. Әр кітаптың атауы, авторы, жылы және бірегей нөмірі бар. Кітапханаға **оқырмандар** (оқушылар мен мұғалімдер) тіркеледі. Оқырман кітапты **алады** және **қайтарады**. Оқушы бір мезгілде 3 кітаптан, мұғалім 5 кітаптан артық ала алмайды. Кітапханашы **кітапты іздей** алады және **берілген кітаптар тізімін** көре алады.»

### 2-қадам. Зат есімдер → кластар мен атрибуттар, етістіктер → әдістер

| Сөз | Түрі | Шешім |
|---|---|---|
| кітапхана | зат есім | `Library` класы |
| кітап | зат есім | `Book` класы |
| оқырман, оқушы, мұғалім | зат есім | `Reader` → `StudentReader`, `TeacherReader` |
| атауы, авторы, жылы, нөмірі | қасиет | `Book` атрибуттары |
| алады, қайтарады | етістік | `Library.give_book()`, `Library.return_book()` |
| іздейді | етістік | `Library.search()` |
| 3 / 5 кітап шегі | ереже | `Reader.limit` — ұрпақта әртүрлі (полиморфизм) |

### 3-қадам. Байланыстарды анықтау

| Байланыс | Сұрақ | Мысал | Python-да |
|---|---|---|---|
| **is-a** (мұрагерлік) | «А — бұл Б-ның түрі ме?» | Оқушы — оқырманның түрі | `class StudentReader(Reader)` |
| **has-a** (композиция) | «А-да Б бар ма?» | Кітапханада кітаптар бар | `self.books = []` атрибуты |

Жиі қате: `class Library(Book)` деп жазу. Кітапхана — кітап емес! Кітапханада кітаптар **бар** — бұл композиция.

### 4-қадам. Класс сызбасы (UML)

UML (Unified Modeling Language) — кластарды сызба түрінде көрсету тілі. Әр класс — үш бөлікті тіктөртбұрыш: атауы, атрибуттары, әдістері.

```text
┌────────────────────┐        ┌──────────────────────┐
│      Library       │◆──────>│        Book          │
├────────────────────┤  1..*  ├──────────────────────┤
│ name               │        │ book_id, title       │
│ books: list[Book]  │        │ author, year         │
│ readers: list      │        │ is_available         │
├────────────────────┤        ├──────────────────────┤
│ add_book()         │        │ __str__(), __eq__()  │
│ register()         │        └──────────────────────┘
│ give_book()        │
│ return_book()      │        ┌──────────────────────┐
│ search()           │◆──────>│       Reader         │
│ __len__()          │        ├──────────────────────┤
└────────────────────┘        │ name, _books         │
                              │ limit (класс атриб.) │
                              ├──────────────────────┤
                              │ can_take(), __str__  │
                              └──────────▲───────────┘
                                         │ (мұрагерлік)
                         ┌───────────────┴──────────────┐
                ┌────────┴────────┐            ┌────────┴────────┐
                │  StudentReader  │            │  TeacherReader  │
                │  limit = 3      │            │  limit = 5      │
                └─────────────────┘            └─────────────────┘
```

Белгілер: `◆──>` — композиция («құрамында бар»), `▲` — мұрагерлік.

### 5-қадам. Кодты кезеңмен жазу

1. Алдымен ең қарапайым класс (`Book`) — жазамыз, тексереміз.
2. Содан кейін `Reader` және ұрпақтары — тексереміз.
3. Соңында оларды біріктіретін `Library`.
4. Әр кезеңнен кейін `git commit` жасаймыз.

## 2. Үлгі: «Кітапхана» жүйесі

```python
class Book:
    def __init__(self, book_id, title, author, year):
        self.book_id = book_id
        self.title = title
        self.author = author
        self.year = year
        self.is_available = True

    def __str__(self):
        status = "бар" if self.is_available else "берілген"
        return f"[{self.book_id}] «{self.title}», {self.author}, {self.year} ({status})"

    def __repr__(self):
        return f"Book({self.book_id}, {self.title!r})"

    def __eq__(self, other):
        if not isinstance(other, Book):
            return NotImplemented
        return self.book_id == other.book_id


class Reader:
    limit = 1      # ұрпақтарда қайта анықталады

    def __init__(self, name):
        self.name = name
        self._books = []          # жабық тізім (инкапсуляция)

    @property
    def books(self):
        return list(self._books)  # көшірмесін береміз

    def can_take(self):
        return len(self._books) < self.limit

    def role(self):
        return "Оқырман"

    def __str__(self):
        return f"{self.role()} {self.name}: {len(self._books)}/{self.limit} кітап"


class StudentReader(Reader):
    limit = 3

    def __init__(self, name, grade):
        super().__init__(name)
        self.grade = grade

    def role(self):
        return f"Оқушы ({self.grade}-сынып)"


class TeacherReader(Reader):
    limit = 5

    def role(self):
        return "Мұғалім"


class Library:
    def __init__(self, name):
        self.name = name
        self.books = []
        self.readers = []

    def add_book(self, book):
        if book in self.books:
            print(f"{book.book_id} нөмірлі кітап бұрыннан бар")
            return
        self.books.append(book)

    def register(self, reader):
        self.readers.append(reader)

    def find_by_id(self, book_id):
        for b in self.books:
            if b.book_id == book_id:
                return b
        return None

    def give_book(self, reader, book_id):
        book = self.find_by_id(book_id)
        if book is None:
            print("Мұндай кітап жоқ")
        elif not book.is_available:
            print(f"«{book.title}» қазір басқа оқырманда")
        elif not reader.can_take():
            print(f"{reader.name}: шекке жеттіңіз ({reader.limit} кітап)")
        else:
            book.is_available = False
            reader._books.append(book)
            print(f"{reader.name} «{book.title}» кітабын алды")

    def return_book(self, reader, book_id):
        book = self.find_by_id(book_id)
        if book in reader._books:
            reader._books.remove(book)
            book.is_available = True
            print(f"{reader.name} «{book.title}» кітабын қайтарды")
        else:
            print("Бұл оқырманда мұндай кітап жоқ")

    def search(self, text):
        text = text.lower()
        return [b for b in self.books
                if text in b.title.lower() or text in b.author.lower()]

    def __len__(self):
        return len(self.books)

    def __str__(self):
        free = sum(1 for b in self.books if b.is_available)
        return f"{self.name}: {len(self)} кітап, оның {free} бос"


lib = Library("№25 мектеп кітапханасы")
lib.add_book(Book(1, "Абай жолы", "М. Әуезов", 1942))
lib.add_book(Book(2, "Көшпенділер", "І. Есенберлин", 1976))
lib.add_book(Book(3, "Қан мен тер", "Ә. Нұрпейісов", 1961))
lib.add_book(Book(1, "Абай жолы", "М. Әуезов", 1942))

aru = StudentReader("Ару", 7)
teacher = TeacherReader("Сәуле апай")
lib.register(aru)
lib.register(teacher)

lib.give_book(aru, 1)
lib.give_book(teacher, 1)
lib.give_book(teacher, 2)
print(aru)
print(teacher)
print(lib)
for b in lib.search("әуезов"):
    print(b)
lib.return_book(aru, 1)
print(lib)
```

Нәтижесі:
```text
1 нөмірлі кітап бұрыннан бар
Ару «Абай жолы» кітабын алды
«Абай жолы» қазір басқа оқырманда
Сәуле апай «Көшпенділер» кітабын алды
Оқушы (7-сынып) Ару: 1/3 кітап
Мұғалім Сәуле апай: 1/5 кітап
№25 мектеп кітапханасы: 3 кітап, оның 1 бос
[1] «Абай жолы», М. Әуезов, 1942 (берілген)
Ару «Абай жолы» кітабын қайтарды
№25 мектеп кітапханасы: 3 кітап, оның 2 бос
```

### Мұнда ОБП принциптері қайда?

| Принцип | Кодтағы орны |
|---|---|
| Инкапсуляция | `Reader._books` жабық, сырттан `books` property арқылы көшірмесі ғана оқылады |
| Мұрагерлік | `StudentReader`, `TeacherReader` ← `Reader` |
| Полиморфизм | `limit` және `role()` әр ұрпақта өзінше; `can_take()` бәріне ортақ |
| Абстракция | `Library` пайдаланушыға тек `give_book`, `return_book`, `search` береді |
| Магиялық әдістер | `__str__`, `__repr__`, `__eq__` (`in` үшін), `__len__` |

## 3. «Мектеп журналы» жүйесінің моделі

> «Мектеп журналында **сыныптар** бар. Әр сыныпта **оқушылар** бар. Оқушыға **пәндер** бойынша **бағалар** (2–5) қойылады. Журнал оқушының пән бойынша **орташа бағасын** есептей алады, **үздік оқушыны** анықтайды, сыныптың **үлгерім есебін** шығарады.»

Сол талдау қадамдарын қолданамыз:

| Сөз | Түрі | Шешім |
|---|---|---|
| сынып журналы | зат есім | `ClassJournal` класы |
| оқушы | зат есім | `Student` класы |
| пән | зат есім | сөздік кілті болатын жол (пәннің өз қасиеттері пайда болса ғана жеке `Subject` класы керек) |
| бағалар | қасиет | `Student` ішіндегі `{пән: [бағалар]}` сөздігі |
| баға қою | етістік | ауқымды тексеретін `Student.add_grade()` |
| орташа балл, үздік оқушы, есеп | етістік | `Student.average()`, `ClassJournal.best_student()`, `ClassJournal.report()` |

Байланыстар: журналда оқушылар **бар** (has-a, композиция), оқушыда бағалар **бар** (has-a). Мұнда мұрагерлік қажет емес.

```python
class Student:
    def __init__(self, name):
        self.name = name
        self._grades = {}              # {пән: [бағалар]}

    def add_grade(self, subject, grade):
        if not 2 <= grade <= 5:
            raise ValueError("Баға 2-ден 5-ке дейін болуы керек")
        self._grades.setdefault(subject, []).append(grade)

    def average(self, subject=None):
        if subject is None:
            marks = [g for lst in self._grades.values() for g in lst]
        else:
            marks = self._grades.get(subject, [])
        return round(sum(marks) / len(marks), 2) if marks else 0

    def __str__(self):
        return f"{self.name}: орташа балл {self.average()}"


class ClassJournal:
    def __init__(self, class_name):
        self.class_name = class_name
        self.students = []

    def add_student(self, student):
        self.students.append(student)

    def best_student(self):
        return max(self.students, key=lambda s: s.average(), default=None)

    def __len__(self):
        return len(self.students)

    def report(self):
        print(f"{self.class_name} сыныбы, оқушылар саны: {len(self)}")
        for s in sorted(self.students, key=lambda s: s.average(), reverse=True):
            print(" ", s)


j = ClassJournal("8 «А»")
dana, askar = Student("Дана"), Student("Асқар")
j.add_student(dana)
j.add_student(askar)
dana.add_grade("Математика", 5)
dana.add_grade("Информатика", 4)
askar.add_grade("Математика", 3)
askar.add_grade("Информатика", 5)
askar.add_grade("Информатика", 4)
j.report()
print("Үздік:", j.best_student().name)
print("Дана, математика:", dana.average("Математика"))
```

Нәтижесі:
```text
8 «А» сыныбы, оқушылар саны: 2
  Дана: орташа балл 4.5
  Асқар: орташа балл 4.0
Үздік: Дана
Дана, математика: 5.0
```

Осы кодтағы пайдалы тәсілдер:
- `dict.setdefault(key, [])` жаңа пән үшін бос тізім жасап, оны бірден қайтарады;
- `max(..., key=..., default=None)` бос журналда қате бермейді;
- `add_grade` ішіндегі `raise ValueError` қате бағаны сақтатпайды — деректерді тексеру класс ішінде (инкапсуляция).

## Жиі кездесетін қателер

**1. Композиция орнына мұрагерлік**

```python
class Book:
    pass

class Library(Book):     # ҚАТЕ ой: кітапхана — кітап емес
    pass
```

Дұрысы: `class Library:` және ішінде `self.books = []`.

**2. Бір класқа бәрін тығу («құдай класы»)** — 300 жолдық бір `Library` класы. Жауапкершілікті бөліңіз: кітап өзін сипаттайды, оқырман өз шегін біледі, кітапхана үйлестіреді.

**3. Объектінің орнына оның атын сақтау** — `reader._books.append("Абай жолы")`. Сонда кітаптың статусын өзгерте алмайсыз. Объектінің өзін сақтаңыз.

**4. Тізімді сыртқа тікелей беру**

```python
class Reader:
    def __init__(self):
        self._books = []

    def get_books(self):
        return self._books          # сырттан өзгертуге болады!

r = Reader()
r.get_books().append("кез келген нәрсе")
print(r._books)
```

Шешімі — көшірме қайтару: `return list(self._books)`.

**5. Жүйені тексермей бірден толық жазу** — қателерді табу қиындайды. Әр класты жазған соң бірден тексеріңіз.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| Жобалау | Кодқа дейін кластарды, атрибуттарды, әдістерді, байланыстарды анықтау |
| UML класс диаграммасы | Кластар мен олардың байланыстарының сызбасы |
| is-a | «...-ның түрі» байланысы → мұрагерлік |
| has-a, композиция | «...-да бар» байланысы → объект атрибут ретінде |
| Жауапкершілік | Әр класс тек өз ісін атқаруы керек |

## Ескерту

- Өзгермелі әдепкі мән — тұзақ: `def __init__(self, books=[])` ішінде бір тізім барлық объектілерге ортақ болады. `books=None` деп жазып, ішінде `self.books = books if books is not None else []` қолданыңыз.
- Класс деңгейінде жарияланған тізім (`class Library: books = []`) де барлық объектілерге ортақ; өзгермелі деректерді `__init__` ішінде құрыңыз.
- «Мұрагерліктен гөрі композицияны таңдаңыз» жобалау ережесі: мұрагерлік тек нақты is-a байланысы болғанда орынды.
- Негізінен деректер сақтайтын кластар үшін `@dataclass` (Python 3.7+) ыңғайлы; тізімдер үшін — `field(default_factory=list)`.
- `list[Book]` түріндегі аннотациялар Python 3.9-дан бастап жұмыс істейді; ескі нұсқаларда — `typing.List[Book]`.
- PEP 8 бойынша класс атаулары `CapWords` стилінде (`ClassJournal`), әдістер мен атрибуттар — `snake_case` (`add_grade`) жазылады.
