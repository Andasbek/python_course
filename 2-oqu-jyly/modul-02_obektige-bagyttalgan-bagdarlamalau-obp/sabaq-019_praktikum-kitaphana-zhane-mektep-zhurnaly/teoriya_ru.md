# Урок 19. Практикум: проектирование системы классов «Библиотека» и «Школьный журнал».

## Темы

1. Как проектировать систему классов?
2. Образец: система «Библиотека»
3. Модель системы «Школьный журнал»
4. Частые ошибки
5. Основные понятия

## 1. Как проектировать систему классов?

Программирование начинается не с кода, а с **размышления**. Прежде чем решать большую задачу, разбиваем её на классы.

### Шаг 1. Записать задачу в виде текста

> «В школьной библиотеке есть **книги**. У каждой книги есть название, автор, год и уникальный номер. В библиотеке регистрируются **читатели** (ученики и преподаватели). Читатель **берёт** и **возвращает** книгу. Ученик может одновременно взять не больше 3 книг, преподаватель — не больше 5. Библиотекарь может **искать книгу** и видеть **список выданных книг**.»

### Шаг 2. Существительные → классы и атрибуты, глаголы → методы

| Слово | Вид | Решение |
|---|---|---|
| библиотека | существительное | класс `Library` |
| книга | существительное | класс `Book` |
| читатель, ученик, преподаватель | существительное | `Reader` → `StudentReader`, `TeacherReader` |
| название, автор, год, номер | свойство | атрибуты `Book` |
| берёт, возвращает | глагол | `Library.give_book()`, `Library.return_book()` |
| ищет | глагол | `Library.search()` |
| лимит 3 / 5 книг | правило | `Reader.limit` — разный в потомках (полиморфизм) |

### Шаг 3. Определение связей

| Связь | Вопрос | Пример | В Python |
|---|---|---|---|
| **is-a** (наследование) | «А — это разновидность Б?» | Ученик — разновидность читателя | `class StudentReader(Reader)` |
| **has-a** (композиция) | «У А есть Б?» | В библиотеке есть книги | атрибут `self.books = []` |

Частая ошибка: писать `class Library(Book)`. Библиотека — не книга! В библиотеке **есть** книги — это композиция.

### Шаг 4. Схема классов (UML)

UML (Unified Modeling Language) — язык изображения классов в виде схем. Каждый класс — прямоугольник из трёх частей: имя, атрибуты, методы.

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
                              │ limit (атриб. класса)│
                              ├──────────────────────┤
                              │ can_take(), __str__  │
                              └──────────▲───────────┘
                                         │ (наследование)
                         ┌───────────────┴──────────────┐
                ┌────────┴────────┐            ┌────────┴────────┐
                │  StudentReader  │            │  TeacherReader  │
                │  limit = 3      │            │  limit = 5      │
                └─────────────────┘            └─────────────────┘
```

Обозначения: `◆──>` — композиция («содержит в составе»), `▲` — наследование.

### Шаг 5. Писать код поэтапно

1. Сначала самый простой класс (`Book`) — пишем, проверяем.
2. Затем `Reader` и его потомки — проверяем.
3. В конце объединяющий их `Library`.
4. После каждого этапа делаем `git commit`.

## 2. Образец: система «Библиотека»

```python
class Book:
    def __init__(self, book_id, title, author, year):
        self.book_id = book_id
        self.title = title
        self.author = author
        self.year = year
        self.is_available = True

    def __str__(self):
        status = "в наличии" if self.is_available else "выдана"
        return f"[{self.book_id}] «{self.title}», {self.author}, {self.year} ({status})"

    def __repr__(self):
        return f"Book({self.book_id}, {self.title!r})"

    def __eq__(self, other):
        if not isinstance(other, Book):
            return NotImplemented
        return self.book_id == other.book_id


class Reader:
    limit = 1      # переопределяется в потомках

    def __init__(self, name):
        self.name = name
        self._books = []          # закрытый список (инкапсуляция)

    @property
    def books(self):
        return list(self._books)  # отдаём копию

    def can_take(self):
        return len(self._books) < self.limit

    def role(self):
        return "Читатель"

    def __str__(self):
        return f"{self.role()} {self.name}: книг {len(self._books)}/{self.limit}"


class StudentReader(Reader):
    limit = 3

    def __init__(self, name, grade):
        super().__init__(name)
        self.grade = grade

    def role(self):
        return f"Ученик ({self.grade} класс)"


class TeacherReader(Reader):
    limit = 5

    def role(self):
        return "Преподаватель"


class Library:
    def __init__(self, name):
        self.name = name
        self.books = []
        self.readers = []

    def add_book(self, book):
        if book in self.books:
            print(f"Книга с номером {book.book_id} уже есть")
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
            print("Такой книги нет")
        elif not book.is_available:
            print(f"«{book.title}» сейчас у другого читателя")
        elif not reader.can_take():
            print(f"{reader.name}: вы достигли лимита ({reader.limit} книг)")
        else:
            book.is_available = False
            reader._books.append(book)
            print(f"{reader.name} взял(а) книгу «{book.title}»")

    def return_book(self, reader, book_id):
        book = self.find_by_id(book_id)
        if book in reader._books:
            reader._books.remove(book)
            book.is_available = True
            print(f"{reader.name} вернул(а) книгу «{book.title}»")
        else:
            print("У этого читателя нет такой книги")

    def search(self, text):
        text = text.lower()
        return [b for b in self.books
                if text in b.title.lower() or text in b.author.lower()]

    def __len__(self):
        return len(self.books)

    def __str__(self):
        free = sum(1 for b in self.books if b.is_available)
        return f"{self.name}: книг {len(self)}, из них свободно {free}"


lib = Library("Библиотека школы №25")
lib.add_book(Book(1, "Путь Абая", "М. Ауэзов", 1942))
lib.add_book(Book(2, "Кочевники", "И. Есенберлин", 1976))
lib.add_book(Book(3, "Кровь и пот", "А. Нурпеисов", 1961))
lib.add_book(Book(1, "Путь Абая", "М. Ауэзов", 1942))

aru = StudentReader("Ару", 7)
teacher = TeacherReader("Сауле апай")
lib.register(aru)
lib.register(teacher)

lib.give_book(aru, 1)
lib.give_book(teacher, 1)
lib.give_book(teacher, 2)
print(aru)
print(teacher)
print(lib)
for b in lib.search("ауэзов"):
    print(b)
lib.return_book(aru, 1)
print(lib)
```

Результат:
```text
Книга с номером 1 уже есть
Ару взял(а) книгу «Путь Абая»
«Путь Абая» сейчас у другого читателя
Сауле апай взял(а) книгу «Кочевники»
Ученик (7 класс) Ару: книг 1/3
Преподаватель Сауле апай: книг 1/5
Библиотека школы №25: книг 3, из них свободно 1
[1] «Путь Абая», М. Ауэзов, 1942 (выдана)
Ару вернул(а) книгу «Путь Абая»
Библиотека школы №25: книг 3, из них свободно 2
```

### Где здесь принципы ООП?

| Принцип | Место в коде |
|---|---|
| Инкапсуляция | `Reader._books` закрыт, снаружи через property `books` читается только копия |
| Наследование | `StudentReader`, `TeacherReader` ← `Reader` |
| Полиморфизм | `limit` и `role()` в каждом потомке свои; `can_take()` общий для всех |
| Абстракция | `Library` даёт пользователю только `give_book`, `return_book`, `search` |
| Магические методы | `__str__`, `__repr__`, `__eq__` (для `in`), `__len__` |

## 3. Модель системы «Школьный журнал»

> «В школьном журнале есть **классы**. В каждом классе есть **ученики**. Ученику по **предметам** ставятся **оценки** (2–5). Журнал может вычислить **средний балл** ученика по предмету, определить **лучшего ученика**, вывести **отчёт об успеваемости** класса.»

Применяем те же шаги анализа:

| Слово | Вид | Решение |
|---|---|---|
| журнал класса | существительное | класс `ClassJournal` |
| ученик | существительное | класс `Student` |
| предмет | существительное | строка-ключ словаря (отдельный класс `Subject` нужен, только если у предмета появятся свои свойства) |
| оценки | свойство | словарь `{предмет: [оценки]}` внутри `Student` |
| ставить оценку | глагол | `Student.add_grade()` с проверкой диапазона |
| средний балл, лучший ученик, отчёт | глагол | `Student.average()`, `ClassJournal.best_student()`, `ClassJournal.report()` |

Связи: в журнале **есть** ученики (has-a, композиция), у ученика **есть** оценки (has-a). Наследование здесь не нужно.

```python
class Student:
    def __init__(self, name):
        self.name = name
        self._grades = {}              # {предмет: [оценки]}

    def add_grade(self, subject, grade):
        if not 2 <= grade <= 5:
            raise ValueError("Оценка должна быть от 2 до 5")
        self._grades.setdefault(subject, []).append(grade)

    def average(self, subject=None):
        if subject is None:
            marks = [g for lst in self._grades.values() for g in lst]
        else:
            marks = self._grades.get(subject, [])
        return round(sum(marks) / len(marks), 2) if marks else 0

    def __str__(self):
        return f"{self.name}: средний балл {self.average()}"


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
        print(f"Класс {self.class_name}, учеников: {len(self)}")
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
print("Лучший:", j.best_student().name)
print("Дана, математика:", dana.average("Математика"))
```

Результат:
```text
Класс 8 «А», учеников: 2
  Дана: средний балл 4.5
  Асқар: средний балл 4.0
Лучший: Дана
Дана, математика: 5.0
```

Полезные приёмы из этого кода:
- `dict.setdefault(key, [])` создаёт пустой список для нового предмета и сразу возвращает его;
- `max(..., key=..., default=None)` не падает на пустом журнале;
- `raise ValueError` в `add_grade` не даёт сохранить некорректную оценку — проверка данных находится внутри класса (инкапсуляция).

## Частые ошибки

**1. Наследование вместо композиции**

```python
class Book:
    pass

class Library(Book):     # ОШИБОЧНАЯ мысль: библиотека — не книга
    pass
```

Правильно: `class Library:` и внутри `self.books = []`.

**2. Запихнуть всё в один класс («божественный класс»)** — один класс `Library` на 300 строк. Разделяйте ответственность: книга описывает себя, читатель знает свой лимит, библиотека координирует.

**3. Хранить имя вместо объекта** — `reader._books.append("Путь Абая")`. Тогда статус книги изменить не получится. Храните сам объект.

**4. Отдавать список наружу напрямую**

```python
class Reader:
    def __init__(self):
        self._books = []

    def get_books(self):
        return self._books          # можно изменить снаружи!

r = Reader()
r.get_books().append("что угодно")
print(r._books)
```

Решение — возвращать копию: `return list(self._books)`.

**5. Сразу писать всю систему без проверки** — ошибки найти труднее. Проверяйте каждый класс сразу после написания.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Проектирование | Определение классов, атрибутов, методов и связей до написания кода |
| UML-диаграмма классов | Схема классов и связей между ними |
| is-a | Связь «является разновидностью» → наследование |
| has-a, композиция | Связь «имеет в составе» → объект как атрибут |
| Ответственность | Каждый класс должен выполнять только своё дело |

## Примечание

- Изменяемое значение по умолчанию — ловушка: в `def __init__(self, books=[])` один и тот же список будет общим для всех объектов. Пишите `books=None` и внутри `self.books = books if books is not None else []`.
- Список, объявленный на уровне класса (`class Library: books = []`), тоже общий для всех объектов; изменяемые данные создавайте в `__init__`.
- Правило проектирования «предпочитайте композицию наследованию»: наследование уместно только для настоящей связи is-a.
- Для классов, которые в основном хранят данные, удобен `@dataclass` (Python 3.7+); для списков — `field(default_factory=list)`.
- Аннотации вида `list[Book]` работают с Python 3.9; в более старых версиях — `typing.List[Book]`.
- По PEP 8 имена классов пишутся в стиле `CapWords` (`ClassJournal`), методы и атрибуты — `snake_case` (`add_grade`).
