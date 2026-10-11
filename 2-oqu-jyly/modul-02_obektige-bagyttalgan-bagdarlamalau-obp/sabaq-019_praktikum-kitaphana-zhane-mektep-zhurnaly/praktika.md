# Сабақ 19. Практикалық тапсырмалар: «Кітапхана» және «Мектеп журналы»

Практикум жұппен орындалады. Әр кезеңнен кейін `git commit` жасаңыз. Барлық код `library/` және `journal/` папкаларында сақталады.

## Базалық деңгей

### 1-тапсырма. «Кітапхана» үлгісін іске қосу және кеңейту
1. Теориядағы «Кітапхана» кодын `library/library.py` файлына көшіріп, іске қосыңыз.
2. `Library` класына `available_books()` әдісін қосыңыз — бос кітаптар тізімін қайтарсын.
3. `Library` класына `report()` әдісін қосыңыз — қай оқырманда қай кітаптар бар екенін шығарсын.

Мысал шығару:
```text
Оқушы (7-сынып) Ару: «Абай жолы»
Мұғалім Сәуле апай: «Көшпенділер»
```

### 2-тапсырма. «Мектеп журналы»: кластарды жобалау (қағазда)
Теориядағы 3-бөлімнің мәтіні бойынша:
1. Зат есім/етістік кестесін толтырыңыз.
2. is-a және has-a байланыстарын анықтаңыз.
3. UML класс сызбасын дәптерге сызыңыз (кемінде 2 класс: `Student`, `ClassJournal`).

### 3-тапсырма. `Student` класы
`Student(name)` класы: жабық `_grades` сөздігі (`{пән: [бағалар]}`), әдістері:
- `add_grade(subject, grade)` — бағаны тексереді (2–5 аралығы, әйтпесе `ValueError`), қосады;
- `average(subject=None)` — пән берілсе сол пән бойынша, берілмесе барлық бағалар бойынша орташа (бағалар жоқ болса `0`);
- `__str__` — «Айжан: орташа 4.67».

### 4-тапсырма. `ClassJournal` класы
`ClassJournal(class_name)` класы: оқушылар тізімі, әдістері:
- `add_student(student)`, `find(name)`;
- `best_student()` — орташа бағасы ең жоғары оқушы;
- `report()` — барлық оқушылардың орташа бағасы кему ретімен;
- `__len__` — оқушылар саны.

Мысал шығару (`report`):
```text
=== 7 «А» сыныбы: үлгерім есебі ===
1. Айжан: 4.67
2. Бекзат: 4.25
3. Нұрлан: 3.5
Үздік оқушы: Айжан
```

## Кеңейтілген деңгей

### 5-тапсырма. Пән бойынша есеп
`ClassJournal.subject_report(subject)` әдісі: пән бойынша әр оқушының орташасын, сыныптың жалпы орташасын және «2» алған оқушылар тізімін шығарсын.

### 6-тапсырма. Кітапханада кешіктіру айыппұлы
`Book` класына берілген күнін (`taken_day`, бүтін сан — жылдың күні) сақтаңыз. `return_book(reader, book_id, day)` кешіктірілген әр күн үшін айыппұл есептесін: оқушыға 14 күн, мұғалімге 30 күн тегін (бұл мәнді `Reader` ұрпақтарында класс атрибуты ретінде қайта анықтаңыз — полиморфизм), одан кейін әр күнге 50 тг.

## ★ Олимпиадалық / шығармашылық тапсырма

### 7-тапсырма. Мәтіндік мәзірі бар журнал
«Мектеп журналына» консольдік мәзір жасаңыз:
```text
1 — Оқушы қосу
2 — Баға қою
3 — Оқушы бағаларын көру
4 — Сынып есебі
0 — Шығу
```
Қосымша: деректерді `journal.json` файлына сақтау/жүктеу (`json` модулі, 1-жылда өтілген файлдармен жұмыс).

## Үй тапсырмасы

1. «Мектеп журналы» жүйесін аяқтап, GitHub-қа жүктеңіз. README.md файлында жобаның сипаттамасы, кластар тізімі және іске қосу нұсқауы болсын.
2. Келесі сабақта мини-жоба қорғалады: 3 минуттық таныстыруды дайындаңыз (не істейді, қандай кластар, ОБП принциптері қайда қолданылды).
3. ОБП бойынша теорияны қайталаңыз (9–18 сабақтар) — өзіндік жұмыс №1 болады.

## Бағалау критерийлері

| Критерий | Ұпай |
|---|---|
| Жобалау: зат есім/етістік кестесі және UML сызбасы дұрыс | 15 |
| `Student` класы: инкапсуляция, баға тексерісі, орташа есептеу | 20 |
| `ClassJournal` класы: композиция, үздік оқушы, есеп | 20 |
| Магиялық әдістер (`__str__`, `__len__` т.б.) орынды қолданылған | 15 |
| Кеңейтілген тапсырма (5 немесе 6) орындалған | 15 |
| Код сапасы: PEP 8, атаулар, docstring, git commit-тер | 15 |
| **Барлығы** | **100** |

Бағалау шкаласы: 85–100 — «өте жақсы», 70–84 — «жақсы», 50–69 — «қанағаттанарлық», 50-ден төмен — пысықтау қажет.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма** (`Library` класына қосылатын әдістер)
```python
def available_books(self):
    return [b for b in self.books if b.is_available]

def report(self):
    for r in self.readers:
        titles = ", ".join(f"«{b.title}»" for b in r.books)
        print(f"{r.role()} {r.name}: {titles if titles else 'кітап жоқ'}")
```

**2-тапсырма.** Үлгі кесте: сынып → `ClassJournal`; оқушы → `Student`; бағалар, пәндер → `Student._grades` сөздігі; «баға қою» → `add_grade`; «орташа есептеу» → `average`; «үздікті анықтау» → `best_student`; «есеп шығару» → `report`. Байланыстар: `ClassJournal` has-a `Student` (композиция); мұрагерлік міндетті емес.

**3–5-тапсырмалар**
```python
class Student:
    """Оқушы және оның пәндер бойынша бағалары."""

    def __init__(self, name):
        self.name = name
        self._grades = {}

    def add_grade(self, subject, grade):
        if not 2 <= grade <= 5:
            raise ValueError("Баға 2-ден 5-ке дейін болуы керек")
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
        return f"{self.name}: орташа {self.average()}"

    def __repr__(self):
        return f"Student({self.name!r})"


class ClassJournal:
    """Сынып журналы."""

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
        print(f"=== {self.class_name} сыныбы: үлгерім есебі ===")
        ranked = sorted(self.students, key=lambda s: s.average(), reverse=True)
        for i, s in enumerate(ranked, 1):
            print(f"{i}. {s.name}: {s.average()}")
        best = self.best_student()
        if best:
            print("Үздік оқушы:", best.name)

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
            print("Сынып орташасы:", round(sum(avgs) / len(avgs), 2))
        print("«2» алғандар:", ", ".join(failing) if failing else "жоқ")

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

**6-тапсырма** (негізгі өзгерістер)
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


print(calc_fine(StudentReader("Ару"), 10, 30))    # 6 күн кешікті -> 300
print(calc_fine(TeacherReader("Сәуле"), 10, 30))  # 0
```
`Library.give_book` ішінде `book.taken_day = day` сақталады, `return_book` ішінде `calc_fine(reader, book.taken_day, day)` шақырылады.

**7-тапсырма** (мәзір қаңқасы; `Student`, `ClassJournal` жоғарыдағыдай)
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
        print("\n1 — Оқушы қосу\n2 — Баға қою\n3 — Оқушы бағаларын көру\n4 — Сынып есебі\n0 — Шығу")
        choice = input("Таңдау: ")
        if choice == "1":
            journal.add_student(student_cls(input("Аты: ")))
        elif choice == "2":
            st = journal.find(input("Оқушы: "))
            if st is None:
                print("Табылмады")
                continue
            try:
                st.add_grade(input("Пән: "), int(input("Баға: ")))
            except ValueError as e:
                print("Қате:", e)
        elif choice == "3":
            st = journal.find(input("Оқушы: "))
            print(st._grades if st else "Табылмады")
        elif choice == "4":
            journal.report()
        elif choice == "0":
            save(journal)
            print("Сақталды. Сау болыңыз!")
            break
```
Іске қосу: `j = ClassJournal("7 «А»"); load(j, Student); menu(j, Student)`.

</details>
