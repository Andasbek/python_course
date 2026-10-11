# Урок 19. Практические задания: «Библиотека» и «Школьный журнал»

Практикум выполняется в парах. После каждого этапа делайте `git commit`. Весь код хранится в папках `library/` и `journal/`.

## Базовый уровень

### Задание 1. Запуск и расширение образца «Библиотека»
1. Скопируйте код «Библиотеки» из теории в файл `library/library.py` и запустите.
2. Добавьте в класс `Library` метод `available_books()` — пусть возвращает список свободных книг.
3. Добавьте в класс `Library` метод `report()` — пусть выводит, у какого читателя какие книги.

Пример вывода:
```text
Ученик (7 класс) Ару: «Путь Абая»
Учитель Сауле апай: «Кочевники»
```

### Задание 2. «Школьный журнал»: проектирование классов (на бумаге)
По тексту раздела 3 теории:
1. Заполните таблицу существительных/глаголов.
2. Определите связи is-a и has-a.
3. Нарисуйте в тетради UML-схему классов (не менее 2 классов: `Student`, `ClassJournal`).

### Задание 3. Класс `Student`
Класс `Student(name)`: закрытый словарь `_grades` (`{предмет: [оценки]}`), методы:
- `add_grade(subject, grade)` — проверяет оценку (в диапазоне 2–5, иначе `ValueError`), добавляет её;
- `average(subject=None)` — если предмет указан, средний балл по нему, если нет — по всем оценкам (если оценок нет — `0`);
- `__str__` — «Айжан: средний балл 4.67».

### Задание 4. Класс `ClassJournal`
Класс `ClassJournal(class_name)`: список учеников, методы:
- `add_student(student)`, `find(name)`;
- `best_student()` — ученик с наивысшим средним баллом;
- `report()` — средние баллы всех учеников в порядке убывания;
- `__len__` — количество учеников.

Пример вывода (`report`):
```text
=== Класс 7 «А»: отчёт об успеваемости ===
1. Айжан: 4.67
2. Бекзат: 4.25
3. Нұрлан: 3.5
Лучший ученик: Айжан
```

## Продвинутый уровень

### Задание 5. Отчёт по предмету
Метод `ClassJournal.subject_report(subject)`: пусть выводит средний балл каждого ученика по предмету, общий средний балл класса и список учеников, получивших «2».

### Задание 6. Штраф за просрочку в библиотеке
Сохраняйте в классе `Book` день выдачи (`taken_day`, целое число — день года). `return_book(reader, book_id, day)` пусть начисляет штраф за каждый день просрочки: ученику 14 дней, учителю 30 дней бесплатно (переопределите это значение как атрибут класса в потомках `Reader` — полиморфизм), после этого 50 тг за каждый день.

## ★ Олимпиадная / творческая задача

### Задание 7. Журнал с текстовым меню
Создайте для «Школьного журнала» консольное меню:
```text
1 — Добавить ученика
2 — Поставить оценку
3 — Посмотреть оценки ученика
4 — Отчёт по классу
0 — Выход
```
Дополнительно: сохранение/загрузка данных в файл `journal.json` (модуль `json`, работа с файлами из 1-го года).

## Домашнее задание

1. Завершите систему «Школьный журнал» и загрузите на GitHub. В файле README.md должны быть описание проекта, список классов и инструкция по запуску.
2. На следующем уроке будет защита мини-проекта: подготовьте 3-минутную презентацию (что делает, какие классы, где применены принципы ООП).
3. Повторите теорию по ООП (уроки 9–18) — будет самостоятельная работа №1.

## Критерии оценивания

| Критерий | Баллы |
|---|---|
| Проектирование: таблица существительных/глаголов и UML-схема верны | 15 |
| Класс `Student`: инкапсуляция, проверка оценки, вычисление среднего | 20 |
| Класс `ClassJournal`: композиция, лучший ученик, отчёт | 20 |
| Магические методы (`__str__`, `__len__` и др.) применены уместно | 15 |
| Выполнено продвинутое задание (5 или 6) | 15 |
| Качество кода: PEP 8, имена, docstring, git-коммиты | 15 |
| **Итого** | **100** |

Шкала оценивания: 85–100 — «отлично», 70–84 — «хорошо», 50–69 — «удовлетворительно», ниже 50 — требуется доработка.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1** (методы, добавляемые в класс `Library`)
```python
def available_books(self):
    return [b for b in self.books if b.is_available]

def report(self):
    for r in self.readers:
        titles = ", ".join(f"«{b.title}»" for b in r.books)
        print(f"{r.role()} {r.name}: {titles if titles else 'нет книг'}")
```

**Задание 2.** Образец таблицы: класс → `ClassJournal`; ученик → `Student`; оценки, предметы → словарь `Student._grades`; «поставить оценку» → `add_grade`; «вычислить средний» → `average`; «определить лучшего» → `best_student`; «вывести отчёт» → `report`. Связи: `ClassJournal` has-a `Student` (композиция); наследование не обязательно.

**Задания 3–5**
```python
class Student:
    """Ученик и его оценки по предметам."""

    def __init__(self, name):
        self.name = name
        self._grades = {}

    def add_grade(self, subject, grade):
        if not 2 <= grade <= 5:
            raise ValueError("Оценка должна быть от 2 до 5")
        self._grades.setdefault(subject, []).append(grade)

    def grades(self, subject):
        return list(self._grades.get(subject, []))

    def average(self, subject=None):
        if subject is None:
            all_grades = [g for lst in self._grades.values() for g in lst]
        else:
            all_grades = self._grades.get(subject, [])
        if not all_grades:
            return 0
        return round(sum(all_grades) / len(all_grades), 2)

    def __str__(self):
        return f"{self.name}: средний балл {self.average()}"

    def __repr__(self):
        return f"Student({self.name!r})"


class ClassJournal:
    """Классный журнал."""

    def __init__(self, class_name):
        self.class_name = class_name
        self.students = []

    def add_student(self, student):
        self.students.append(student)

    def find(self, name):
        for s in self.students:
            if s.name == name:
                return s
        return None

    def best_student(self):
        if not self.students:
            return None
        return max(self.students, key=lambda s: s.average())

    def report(self):
        print(f"=== Класс {self.class_name}: отчёт об успеваемости ===")
        ranked = sorted(self.students, key=lambda s: s.average(), reverse=True)
        for i, s in enumerate(ranked, 1):
            print(f"{i}. {s.name}: {s.average()}")
        best = self.best_student()
        if best:
            print("Лучший ученик:", best.name)

    def subject_report(self, subject):
        print(f"--- {subject} ---")
        avgs = []
        failing = []
        for s in self.students:
            avg = s.average(subject)
            avgs.append(avg)
            print(f"{s.name}: {avg}")
            if 2 in s.grades(subject):
                failing.append(s.name)
        if avgs:
            print("Средний балл класса:", round(sum(avgs) / len(avgs), 2))
        print("Получили «2»:", ", ".join(failing) if failing else "нет")

    def __len__(self):
        return len(self.students)


j = ClassJournal("7 «А»")
data = {
    "Айжан": [("Математика", 5), ("Математика", 4), ("Физика", 5)],
    "Бекзат": [("Математика", 4), ("Физика", 5), ("Физика", 4), ("Математика", 4)],
    "Нұрлан": [("Математика", 2), ("Физика", 5)],
}
for name, marks in data.items():
    st = Student(name)
    for subj, g in marks:
        st.add_grade(subj, g)
    j.add_student(st)

j.report()
j.subject_report("Математика")
print(len(j))
```

**Задание 6** (основные изменения)
```python
class Reader:
    limit = 1
    free_days = 7

    def __init__(self, name):
        self.name = name
        self._books = []


class StudentReader(Reader):
    limit = 3
    free_days = 14


class TeacherReader(Reader):
    limit = 5
    free_days = 30


FINE_PER_DAY = 50


def calc_fine(reader, taken_day, return_day):
    late = return_day - taken_day - reader.free_days
    return max(0, late) * FINE_PER_DAY


print(calc_fine(StudentReader("Ару"), 10, 30))    # просрочка 6 дней -> 300
print(calc_fine(TeacherReader("Сауле"), 10, 30))  # 0
```
В `Library.give_book` сохраняется `book.taken_day = day`, в `return_book` вызывается `calc_fine(reader, book.taken_day, day)`.

**Задание 7** (каркас меню; `Student`, `ClassJournal` — как выше)
```python
import json


def save(journal, path="journal.json"):
    data = {s.name: s._grades for s in journal.students}
    with open(path, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)


def load(journal, student_cls, path="journal.json"):
    try:
        with open(path, encoding="utf-8") as f:
            data = json.load(f)
    except FileNotFoundError:
        return
    for name, grades in data.items():
        st = student_cls(name)
        st._grades = grades
        journal.add_student(st)


def menu(journal, student_cls):
    while True:
        print("\n1 — Добавить ученика\n2 — Поставить оценку\n3 — Посмотреть оценки ученика\n4 — Отчёт по классу\n0 — Выход")
        choice = input("Выбор: ")
        if choice == "1":
            journal.add_student(student_cls(input("Имя: ")))
        elif choice == "2":
            st = journal.find(input("Ученик: "))
            if st is None:
                print("Не найден")
                continue
            try:
                st.add_grade(input("Предмет: "), int(input("Оценка: ")))
            except ValueError as e:
                print("Ошибка:", e)
        elif choice == "3":
            st = journal.find(input("Ученик: "))
            print(st._grades if st else "Не найден")
        elif choice == "4":
            journal.report()
        elif choice == "0":
            save(journal)
            print("Сохранено. До свидания!")
            break
```
Запуск: `j = ClassJournal("7 «А»"); load(j, Student); menu(j, Student)`.

</details>
