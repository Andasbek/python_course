# Урок 12. Список объектов. Работа с коллекцией объектов.

## Темы

1. Создание списка объектов
2. Обход списка и вызов методов
3. Основные алгоритмы работы с коллекцией
4. Сортировка объектов
5. Словарь объектов: быстрый доступ по ключу
6. Класс-контейнер: объект, управляющий коллекцией
7. Связи между объектами
8. Частые ошибки
9. Основные понятия

## 1. Создание списка объектов

Класс, который используется во всех примерах ниже:

```python
class Student:
    """Учащийся кружка."""

    def __init__(self, name, grade, marks=None):
        self.name = name
        self.grade = grade
        self.marks = marks if marks is not None else []

    def average(self):
        """Возвращает средний балл."""
        return sum(self.marks) / len(self.marks) if self.marks else 0

    def info(self):
        """Строка с краткой информацией."""
        return f"{self.name} ({self.grade} класс): {self.average():.2f}"
```

Способы создания списка:

```python
# 1) Напрямую
students = [
    Student("Әлия", 8, [5, 5, 4]),
    Student("Бекзат", 9, [4, 3, 4]),
    Student("Дана", 8, [5, 4, 5]),
    Student("Ерлан", 10, [3, 4, 3]),
    Student("Жанар", 9, [5, 5, 5]),
]

# 2) Постепенно через append
students.append(Student("Тимур", 10, [4, 4, 5]))

# 3) Из других данных (списка кортежей) — генератором списка
raw = [("Айдос", 7, [4, 5]), ("Мадина", 7, [5, 5])]
students += [Student(name, grade, marks) for name, grade, marks in raw]

print(len(students))
```
Результат:
```text
8
```

В списке хранятся не сами объекты, а **ссылки** на них: если `students[0]` и переменная `aliya` указывают на один объект, изменение через одну из них видно и через другую.

## 2. Обход списка и вызов методов

```python
for student in students:
    print(student.info())
```
Результат:
```text
Әлия (8 класс): 4.67
Бекзат (9 класс): 3.67
Дана (8 класс): 4.67
Ерлан (10 класс): 3.33
Жанар (9 класс): 5.00
Тимур (10 класс): 4.33
Айдос (7 класс): 4.50
Мадина (7 класс): 5.00
```

Переменная цикла (`student`) — по очереди каждый объект. И к атрибутам, и к методам обращаемся через точку. Для вывода с номерами — `enumerate`:

```python
for number, student in enumerate(students[:3], start=1):
    print(number, student.name)
```

**Изменение всех объектов** — например, поставить каждому ученику 5 за посещение кружка:
```python
for student in students:
    student.marks.append(5)
print(students[1].marks)
```
Результат:
```text
[4, 3, 4, 5]
```

## 3. Основные алгоритмы работы с коллекцией

Следующие примеры относятся к исходным 8 ученикам (без примера «всем поставить 5» выше).

### 3.1. Поиск (найти первый подходящий объект)
```python
def find_by_name(students, name):
    """Возвращает ученика по имени; если не найден — None."""
    for student in students:
        if student.name.lower() == name.lower():
            return student
    return None


found = find_by_name(students, "дана")
if found:
    print("Найден:", found.info())
else:
    print("Такого ученика нет")
```
Результат:
```text
Найден: Дана (8 класс): 4.67
```
Важно: если ничего не найдено, возвращаем `None`, поэтому **не вызывай** метод результата без проверки.

### 3.2. Фильтрация (все объекты, удовлетворяющие условию)
```python
# Через цикл
eighth = []
for s in students:
    if s.grade == 8:
        eighth.append(s)

# Генератором списка (коротко)
excellent = [s for s in students if s.average() >= 4.5]
print([s.name for s in eighth])
print([s.name for s in excellent])
```
Результат:
```text
['Әлия', 'Дана']
['Әлия', 'Дана', 'Жанар', 'Айдос', 'Мадина']
```

### 3.3. Подсчёт, сумма, среднее
```python
count_excellent = sum(1 for s in students if s.average() >= 4.5)
class_avg = sum(s.average() for s in students) / len(students)
print("Количество отличников:", count_excellent)
print(f"Средний балл группы: {class_avg:.2f}")
```
Результат:
```text
Количество отличников: 5
Средний балл группы: 4.40
```

### 3.4. Максимум / минимум (параметр key)
`max()` не умеет сравнивать объекты сам по себе — через `key` мы указываем, **по чему** их сравнивать:

```python
best = max(students, key=lambda s: s.average())
youngest = min(students, key=lambda s: s.grade)
print("Лучший:", best.name)
print("Самый младший класс:", youngest.name)
```
Результат:
```text
Лучший: Жанар
Самый младший класс: Айдос
```
`lambda s: s.average()` — маленькая анонимная функция «сравнивай ученика по его среднему баллу». Если значения равны, `max` возвращает первый (у Жанар и Мадины по 5.00).

Можно написать и через цикл (чтобы понять алгоритм):
```python
best = students[0]
for s in students[1:]:
    if s.average() > best.average():
        best = s
print(best.name)
```

### 3.5. Группировка (по классам)
```python
by_grade = {}
for s in students:
    by_grade.setdefault(s.grade, []).append(s.name)
for grade in sorted(by_grade):
    print(grade, by_grade[grade])
```
Результат:
```text
7 ['Айдос', 'Мадина']
8 ['Әлия', 'Дана']
9 ['Бекзат', 'Жанар']
10 ['Ерлан', 'Тимур']
```

### 3.6. Удаление
```python
# Удаление по условию — создаём новый список (не делаем remove в цикле!)
students = [s for s in students if s.grade != 10]

# Удаление одного конкретного объекта
aidos = find_by_name(students, "Айдос")
if aidos:
    students.remove(aidos)
print(len(students))
```
Результат:
```text
5
```

## 4. Сортировка объектов

```python
# По среднему баллу в порядке убывания
for s in sorted(students, key=lambda s: s.average(), reverse=True):
    print(s.info())
```
Результат:
```text
Жанар (9 класс): 5.00
Мадина (7 класс): 5.00
Әлия (8 класс): 4.67
Дана (8 класс): 4.67
Бекзат (9 класс): 3.67
```

- `sorted(список, key=...)` — возвращает **новый** отсортированный список, исходный не меняется;
- `список.sort(key=...)` — сортирует **сам** список;
- по нескольким критериям — `key` возвращает кортеж: `key=lambda s: (s.grade, s.name)` — сначала по классу, внутри класса — по имени;
- один критерий по убыванию, другой по возрастанию: `key=lambda s: (-s.average(), s.name)`.

```python
for s in sorted(students, key=lambda s: (s.grade, s.name)):
    print(s.grade, s.name)
```
Результат:
```text
7 Мадина
8 Дана
8 Әлия
9 Бекзат
9 Жанар
```
(Внимание: стандартная сортировка идёт по кодам Unicode, поэтому казахские буквы «ә», «ө», «ү» оказываются после русско-латинского алфавита: «Дана» стоит раньше «Әлия».)

## 5. Словарь объектов: быстрый доступ по ключу

Чтобы найти ученика в списке, нужно перебрать всех. Если объект часто ищут по **уникальному ключу** (ID, логин, ISBN, номер) — удобнее **словарь**:

```python
class Book:
    """Книга."""

    def __init__(self, isbn, title, author, year):
        self.isbn = isbn
        self.title = title
        self.author = author
        self.year = year
        self.is_available = True

    def info(self):
        status = "в наличии" if self.is_available else "выдана"
        return f"[{self.isbn}] «{self.title}» — {self.author}, {self.year} ({status})"


catalog = {}
for book in [
    Book("978-601-01", "Путь Абая", "М. Ауэзов", 1942),
    Book("978-601-02", "Кровь и пот", "А. Нурпеисов", 1961),
    Book("978-601-03", "Кочевники", "И. Есенберлин", 1976),
]:
    catalog[book.isbn] = book

print(catalog["978-601-02"].info())
print("978-601-99" in catalog)
for isbn, book in catalog.items():
    print(isbn, book.title)
```
Результат:
```text
[978-601-02] «Кровь и пот» — А. Нурпеисов, 1961 (в наличии)
False
978-601-01 Путь Абая
978-601-02 Кровь и пот
978-601-03 Кочевники
```

| Список `[obj, obj]` | Словарь `{ключ: obj}` |
|---|---|
| хранение по порядку, доступ по номеру | **быстрый** поиск по уникальному ключу |
| могут быть одинаковые объекты | ключ не повторяется |
| поиск — перебор всего списка | поиск — сразу `d[key]` |
| пример: враги в игре, очередь | пример: ученики по ID, книги по ISBN |

## 6. Класс-контейнер: объект, управляющий коллекцией

Вместо того чтобы держать список и функции для него отдельно, объединим их в **один класс**. Такой класс называется **контейнером** (или менеджером): «в библиотеке **есть** книги» (композиция).

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
    """Библиотека, управляющая коллекцией книг."""

    def __init__(self, name):
        self.name = name
        self.books = {}            # isbn → Book

    def add_book(self, book):
        """Добавляет книгу (если ISBN повторяется — не добавляет)."""
        if book.isbn in self.books:
            print(f"ISBN {book.isbn} уже существует")
            return False
        self.books[book.isbn] = book
        return True

    def find_by_author(self, author):
        """Возвращает список всех книг автора."""
        return [b for b in self.books.values() if author.lower() in b.author.lower()]

    def give_out(self, isbn):
        """Выдаёт книгу читателю."""
        book = self.books.get(isbn)
        if book is None:
            print("Такой книги нет")
            return False
        if not book.is_available:
            print(f"«{book.title}» сейчас выдана")
            return False
        book.is_available = False
        return True

    def return_book(self, isbn):
        """Принимает книгу обратно."""
        book = self.books.get(isbn)
        if book and not book.is_available:
            book.is_available = True
            return True
        return False

    def available_books(self):
        """Возвращает доступные книги, отсортированные по году."""
        free = [b for b in self.books.values() if b.is_available]
        return sorted(free, key=lambda b: b.year)

    def report(self):
        """Выводит отчёт о библиотеке."""
        total = len(self.books)
        free = len(self.available_books())
        print(f"«{self.name}»: всего {total}, доступно {free}, выдано {total - free}")


lib = Library("Школьная библиотека")
lib.add_book(Book("978-601-01", "Путь Абая", "М. Ауэзов", 1942))
lib.add_book(Book("978-601-02", "Кровь и пот", "А. Нурпеисов", 1961))
lib.add_book(Book("978-601-03", "Кочевники", "И. Есенберлин", 1976))
lib.add_book(Book("978-601-04", "Караш-Караш", "М. Ауэзов", 1927))
lib.add_book(Book("978-601-01", "Копия", "Неизвестен", 2000))

lib.give_out("978-601-01")
lib.give_out("978-601-01")
for book in lib.find_by_author("Ауэзов"):
    print(book.info())
lib.report()
```
Результат:
```text
ISBN 978-601-01 уже существует
«Путь Абая» сейчас выдана
[978-601-01] «Путь Абая» — М. Ауэзов, 1942 (выдана)
[978-601-04] «Караш-Караш» — М. Ауэзов, 1927 (в наличии)
«Школьная библиотека»: всего 4, доступно 3, выдано 1
```

Преимущества класса-контейнера:
- **все правила** работы с коллекцией **в одном месте** (не добавлять повторяющийся ISBN, не выдавать уже выданную книгу);
- основная программа короткая и понятная: `lib.give_out(...)`;
- позже сохранение данных в файл или базу данных меняется только внутри этого класса.

## 7. Связи между объектами

Объекты могут ссылаться друг на друга. Например, сохраним, кто взял книгу:

```python
class Reader:
    """Читатель библиотеки."""

    def __init__(self, name):
        self.name = name
        self.borrowed = []          # объекты Book на руках у читателя

    def take(self, book):
        if book.is_available:
            book.is_available = False
            book.reader = self      # книга «знает» своего читателя
            self.borrowed.append(book)


reader = Reader("Әлия")
book = Book("978-601-05", "Меня зовут Кожа", "Б. Сокпакбаев", 1957)
reader.take(book)
print(book.reader.name, [b.title for b in reader.borrowed])
```
Результат:
```text
Әлия ['Меня зовут Кожа']
```

## Частые ошибки

**1. Прямой `print` объекта.**
```text
print(students)  →  [<__main__.Student object at 0x...>, ...]
```
Выводи результат метода `info()` или атрибуты либо определи в классе метод `__str__`, задающий «красивое» строковое представление объекта.

**2. `max()` или `sorted()` без `key`.**
```text
max(students)  →  TypeError: '>' not supported between instances of 'Student' and 'Student'
```
Решение: `max(students, key=lambda s: s.average())`.

**3. Передача в `key` самого метода, а не его результата.**
`key=lambda s: s.average` (без скобок) — попытка сравнивать методы → `TypeError`. Правильно: `s.average()`.

**4. Вызов метода у ненайденного (`None`) объекта.**
```text
find_by_name(students, "Аскар").info()  →  AttributeError: 'NoneType' object has no attribute 'info'
```
Сначала проверь через `if found:`.

**5. Удаление из списка, который обходишь в цикле**, — некоторые объекты будут пропущены. Создавай новый список фильтрацией.

**6. Добавление одного объекта в список несколько раз** (копируется ссылка):
```python
s = Student("Әлия", 8)
group = [s] * 3          # три ссылки — на ОДИН объект
group[0].name = "Дана"
print([x.name for x in group])   # ['Дана', 'Дана', 'Дана']
```
Правильно: `[Student(f"Ученик {i}", 8) for i in range(3)]`.

## Основные понятия

| Понятие | Пояснение |
|---|---|
| Список объектов | Список, элементами которого являются объекты |
| Словарь объектов | `{ключ: объект}` — доступ по уникальному ключу |
| Поиск | Нахождение первого объекта, удовлетворяющего условию |
| Фильтрация | Выбор всех объектов, удовлетворяющих условию |
| Агрегирование | Вычисление одного значения по коллекции (сумма, среднее, максимум) |
| Параметр `key` | Функция, задающая критерий сортировки/сравнения |
| `lambda` | Однострочная анонимная функция |
| Класс-контейнер | Класс, хранящий коллекцию объектов и управляющий ею |
| Композиция | Отношение «имеет»: в библиотеке есть книги |

## Примечание

- `sorted()` и `list.sort()` выполняют устойчивую (stable) сортировку: объекты с равными ключами сохраняют исходный порядок. Поэтому можно сортировать поэтапно по нескольким критериям.
- Вместо `lambda` можно использовать функции модуля `operator`: `sorted(books, key=operator.attrgetter("year"))` — короче и немного быстрее. Документация: https://docs.python.org/3/howto/sorting.html.
- `max()` и `min()` на пустом списке вызывают `ValueError`; чтобы этого избежать, есть параметр `default`: `max(students, key=..., default=None)`.
- Поиск `in` в словаре выполняется в среднем за постоянное время (O(1)), а поиск в списке — пропорционально числу элементов (O(n)). При большом количестве объектов разница заметна.
- Для правильной алфавитной сортировки казахских букв нужен собственный ключ (через строку с порядком алфавита) или библиотеки `locale`/`PyICU`.
