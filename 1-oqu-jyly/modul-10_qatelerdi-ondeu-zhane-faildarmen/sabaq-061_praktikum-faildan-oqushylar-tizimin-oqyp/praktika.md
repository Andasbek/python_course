# Сабақ 61. Практикалық тапсырмалар: «Оқушылар үлгерімі» мини-жобасы

> **Практикум.** Жұмыс жеке немесе жұппен орындалады. Нәтиже: `grades_report.py` бағдарламасы, `report.txt` есеп файлы және әр сыныптың жеке файлдары.

## Мини-жоба: «Электрондық журнал есебі»

### 1-қадам. Деректер файлын дайындау

Thonny-де жаңа файл ашып, мына мазмұнды `students.txt` атымен бағдарламаң тұрған папкаға (UTF-8) сақта:

```text
Аты;Тегі;Сыныбы;Математика;Информатика;Қазақ тілі
Айгерім;Сейітова;7А;5;5;5
Нұрлан;Жақсылықов;7А;4;5;3
Дана;Әбілова;7Ә;5;4;5
Ерасыл;Төлегенов;7Ә;3;4;4
Мадина;Ахметова;7А;4;4;5
Арман;Құрманов;7Ә;5;5;4
Әлия;Бекова;7А;3;3;4
Санжар;Оразов;7Ә;4;5;5
```

Өз сыныбыңнан ойдан шығарылған 2–3 оқушы қос (нақты адамдардың бағасын жазба!).

### 2-қадам. Бағдарлама «қаңқасы»

`grades_report.py` файлын құрып, функциялардың бос «қаңқасын» жаз: `average()`, `read_students()`, `write_report()`. Бағдарлама іске қосылып, «Дайын!» деп шығатынына көз жеткіз.

### 3-қадам. Файлды оқу

`read_students(file_name)` функциясын жаз:
- тақырып жолын өткізіп жібер;
- әр жолды `split(";")` арқылы бөл;
- әр оқушыны `{"name", "surname", "class", "grades", "avg"}` кілттері бар сөздік ретінде тізімге қос.

Тексеру: `print(len(students))` → `8` (немесе сен қосқан оқушылармен көбірек).

### 4-қадам. Статистика

Есепте:
- әр оқушының орташа бағасы;
- әр пән бойынша сыныптың орташа бағасы;
- жалпы орташа баға;
- үздіктер (орташасы ≥ 4.5).

Тексеру: Айгерімнің орташасы – 5.00, Ерасылдікі – 3.67 (қолмен есептеп салыстыр).

### 5-қадам. Деңгейді анықтау

`level(avg)` функциясы: ≥ 4.5 → «үздік», ≥ 3.5 → «екпінді», қалғаны → «қосымша көңіл бөлу керек».

### 6-қадам. Есеп файлын жазу

`write_report(students, "report.txt")` функциясы оқушыларды орташа бағасы бойынша кему ретімен сұрыптап, кесте түрінде жазсын. Күтілетін `report.txt`:

```text
ОҚУШЫЛАР ҮЛГЕРІМІ ТУРАЛЫ ЕСЕП
====================================================
№  Аты-жөні              Сынып   Орташа  Деңгей
----------------------------------------------------
1  Айгерім Сейітова      7А        5.00  үздік
2  Дана Әбілова          7Ә        4.67  үздік
3  Арман Құрманов        7Ә        4.67  үздік
4  Санжар Оразов         7Ә        4.67  үздік
5  Мадина Ахметова       7А        4.33  екпінді
6  Нұрлан Жақсылықов     7А        4.00  екпінді
7  Ерасыл Төлегенов      7Ә        3.67  екпінді
8  Әлия Бекова           7А        3.33  қосымша көңіл бөлу керек
----------------------------------------------------
Математика пәнінен орташа: 4.12
Информатика пәнінен орташа: 4.38
Қазақ тілі пәнінен орташа: 4.38
Жалпы орташа баға: 4.29
Үздіктер: Айгерім, Дана, Арман, Санжар
```

Кеңес: сұрыптау үшін `sorted(students, key=get_avg, reverse=True)` – мұнда `get_avg` – оқушы сөздігін алып, оның `"avg"` мәнін қайтаратын өз функцияң.

### 7-қадам. Сыныптар бойынша файлдар және қателерге төзімділік

- Әр сыныпты жеке файлға жаз: `class_7А.txt`, `class_7Ә.txt` (`Аты Тегі – орташа`).
- Файл жоқ болса: «students.txt файлы табылмады!».
- Бүлінген жолдар (баға орнына «н», өрістер саны 6 емес) өткізіліп, олардың нөмірлері экранға шығуы керек. Тексеру үшін файлдың соңына `Әлия;Бекова;7А;3;н;4` жолын қос.

Экрандағы күтілетін нәтиже:
```text
Оқушылар саны: 8
report.txt дайын. Сынып файлдары: ['7А', '7Ә']
```

## Базалық деңгей (практикумды бөліп орындауға арналған тапсырмалар)

Мини-жобаны толық орындай алмаған оқушылар мына қадамдардан бастайды:

1. `students.txt` файлын оқып, әр оқушының тек атын және тегін экранға шығар (тақырып жолынсыз).
2. Әр оқушының үш бағасының орташасын экранға шығар: `Айгерім – 5.00`.
3. Нәтижені `averages.txt` файлына жаз.
4. «Информатикадан» 5 алған оқушылардың тізімін `informatics_5.txt` файлына жаз.

## Кеңейтілген деңгей

5. **Пән таңдау.** Пайдаланушы пән атауын енгізеді, бағдарлама сол пән бойынша ең жоғары бағалы оқушыларды және сыныптың орташасын шығарады. Пән атауы қате болса – түсінікті хабарлама.
6. **Сыныптарды салыстыру.** 7А және 7Ә сыныптарының орташа бағасын есептеп, қай сынып озық екенін есепке қос.
7. **Бағаны түзету.** Пайдаланушы оқушының атын, пәнді және жаңа бағаны енгізеді; бағдарлама `students.txt` файлын **жаңартып** қайта сақтайды (тақырып жолы сақталсын!).

## ★ Олимпиадалық / шығармашылық тапсырма

8. **Мәтіндік диаграмма.** Есепке әр оқушының орташа бағасын жұлдызшалармен «бағаналы диаграмма» түрінде қос (1 балл = 4 жұлдызша):
```text
Айгерім  ********************  5.00
Әлия     *************         3.33
```
9. **HTML-есеп.** Есепті `report.html` файлына `<table>` кестесі түрінде жаз, үздіктердің жолын жасыл түске боя (`<tr style="background:#c8f7c5">`). Файлды браузерде аш.

## Үй тапсырмасы

1. Өз қызығушылығыңа сай деректер файлын құр (мысалы: футбол командалары және ұпайлары, кітаптар және беттер саны, қалалар және температура) – кемінде 8 жол, 3–4 баған.
2. Осы практикумдағы сызба бойынша оны оқып, кемінде 3 статистикалық көрсеткішті есептейтін және есеп файлын жазатын бағдарлама жаз.

## Бағалау критерийлері

| Критерий | Сипаттамасы | Ұпай |
|---|---|---|
| Бағдарламаның дұрыс жұмыс істеуі және тапсырма талабына сай келуі | Файл оқылады, орташа мәндер дұрыс, `report.txt` және сынып файлдары құрылады | 40 |
| Алгоритмнің тиімділігі, шешімнің ұтымдылығы | Функцияларға бөлінген, деректер сөздік/тізімде, қайталанатын код жоқ, қателер өңделеді | 25 |
| Кодтың оқылымдылығы | Түсінікті атаулар, түсініктемелер, есептің пішімі әдемі | 20 |
| Өз бетінше және мерзімінде орындау | Сабақ соңына дейін өзі аяқтады | 15 |
| **Барлығы** | | **100** |

Деңгейлер: 85–100 – жоғары; 60–84 – орта; 40–59 – бастапқы.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**Мини-жобаның толық шешімі (1–7 қадамдар):**

```python
SUBJECTS = ["Математика", "Информатика", "Қазақ тілі"]


def average(grades):
    """Бағалар тізімінің орташасы."""
    return sum(grades) / len(grades)


def read_students(file_name):
    """Файлдан оқушыларды оқып, сөздіктер тізімін қайтарады."""
    students = []
    bad_lines = []
    with open(file_name, encoding="utf-8") as f:
        f.readline()                              # тақырып жолы
        line_no = 1
        for line in f:
            line_no += 1
            parts = line.strip().split(";")
            if len(parts) != 6:
                if line.strip():
                    bad_lines.append(line_no)
                continue
            try:
                grades = [int(parts[3]), int(parts[4]), int(parts[5])]
            except ValueError:
                bad_lines.append(line_no)
                continue
            students.append({
                "name": parts[0].strip(),
                "surname": parts[1].strip(),
                "class": parts[2].strip(),
                "grades": grades,
                "avg": average(grades),
            })
    return students, bad_lines


def level(avg):
    """Орташа баға бойынша үлгерім деңгейі."""
    if avg >= 4.5:
        return "үздік"
    elif avg >= 3.5:
        return "екпінді"
    return "қосымша көңіл бөлу керек"


def get_avg(student):
    """Сұрыптау кілті: оқушының орташа бағасы."""
    return student["avg"]


def write_report(students, file_name):
    """Есепті файлға жазады."""
    ranked = sorted(students, key=get_avg, reverse=True)
    with open(file_name, "w", encoding="utf-8") as f:
        print("ОҚУШЫЛАР ҮЛГЕРІМІ ТУРАЛЫ ЕСЕП", file=f)
        print("=" * 52, file=f)
        print(f"{'№':<3}{'Аты-жөні':<22}{'Сынып':<7}{'Орташа':>7}  Деңгей", file=f)
        print("-" * 52, file=f)
        place = 1
        for s in ranked:
            full_name = s["name"] + " " + s["surname"]
            print(f"{place:<3}{full_name:<22}{s['class']:<7}{s['avg']:>7.2f}  {level(s['avg'])}", file=f)
            place += 1
        print("-" * 52, file=f)
        for i in range(len(SUBJECTS)):
            marks = [s["grades"][i] for s in students]
            print(f"{SUBJECTS[i]} пәнінен орташа: {average(marks):.2f}", file=f)
        all_avg = average([s["avg"] for s in students])
        print(f"Жалпы орташа баға: {all_avg:.2f}", file=f)
        best = [s["name"] for s in students if s["avg"] >= 4.5]
        print("Үздіктер:", ", ".join(best), file=f)


def write_by_class(students):
    """Әр сыныпты жеке файлға жазады: class_7А.txt т.б."""
    classes = {}
    for s in students:
        if s["class"] not in classes:
            classes[s["class"]] = []
        classes[s["class"]].append(s)
    for class_name, group in classes.items():
        with open(f"class_{class_name}.txt", "w", encoding="utf-8") as f:
            for s in group:
                print(f"{s['name']} {s['surname']} – {s['avg']:.2f}", file=f)
    return list(classes.keys())


try:
    students, bad = read_students("students.txt")
except FileNotFoundError:
    print("students.txt файлы табылмады!")
else:
    if bad:
        print("Өткізілген қате жолдар:", bad)
    write_report(students, "report.txt")
    names = write_by_class(students)
    print("Оқушылар саны:", len(students))
    print("report.txt дайын. Сынып файлдары:", names)
```

**Базалық 1–4 тапсырмалар:**

```python
averages = []
with open("students.txt", encoding="utf-8") as f:
    f.readline()
    for line in f:
        parts = line.strip().split(";")
        if len(parts) != 6:
            continue
        name, surname = parts[0], parts[1]
        grades = [int(parts[3]), int(parts[4]), int(parts[5])]
        avg = sum(grades) / 3
        print(name, surname)
        averages.append(f"{name} – {avg:.2f}")
        if grades[1] == 5:
            with open("informatics_5.txt", "a", encoding="utf-8") as out:
                print(name, surname, file=out)

with open("averages.txt", "w", encoding="utf-8") as f:
    for row in averages:
        print(row, file=f)
```

Ескерту: `informatics_5.txt` үшін `a` режимі қолданылғандықтан, бағдарламаны қайта іске қосқанда жолдар қайталанады – оқушылармен талқыла және тізімге жинап, соңында `w` режимімен жазу нұсқасын көрсет.

**5-тапсырма (мини-жоба функцияларын қолданып):**

```python
SUBJECTS = ["Математика", "Информатика", "Қазақ тілі"]
subject = input("Пән: ").strip()
if subject not in SUBJECTS:
    print("Мұндай пән жоқ. Таңда:", ", ".join(SUBJECTS))
else:
    index = SUBJECTS.index(subject)
    marks = {}
    with open("students.txt", encoding="utf-8") as f:
        f.readline()
        for line in f:
            parts = line.strip().split(";")
            if len(parts) == 6:
                marks[parts[0]] = int(parts[3 + index])
    top = max(marks.values())
    leaders = [name for name, m in marks.items() if m == top]
    print("Ең жоғары баға:", top, "–", ", ".join(leaders))
    print(f"Орташа: {sum(marks.values()) / len(marks):.2f}")
```

**6-тапсырма.** Сынып бойынша топтап, әр топтың `avg` мәндерінің орташасын есептеу (`write_by_class` ішіндегі `classes` сөздігін қайта қолдануға болады). Деректер бойынша: 7А – 4.17, 7Ә – 4.42 → 7Ә озық.

**7-тапсырма:**

```python
SUBJECTS = ["Математика", "Информатика", "Қазақ тілі"]
name = input("Оқушының аты: ")
subject = input("Пән: ")
new_grade = input("Жаңа баға: ")
with open("students.txt", encoding="utf-8") as f:
    lines = f.readlines()
changed = False
for i in range(1, len(lines)):
    parts = lines[i].strip().split(";")
    if len(parts) == 6 and parts[0] == name and subject in SUBJECTS:
        parts[3 + SUBJECTS.index(subject)] = new_grade
        lines[i] = ";".join(parts) + "\n"
        changed = True
with open("students.txt", "w", encoding="utf-8") as f:
    f.writelines(lines)
print("Жаңартылды." if changed else "Оқушы немесе пән табылмады.")
```

**8-тапсырма (есепке қосылатын фрагмент):**

```python
students = [{"name": "Айгерім", "avg": 5.0}, {"name": "Әлия", "avg": 10 / 3}]
for s in students:
    bar = "*" * round(s["avg"] * 4)
    print(f"{s['name']:<9}{bar:<21}{s['avg']:.2f}")
```

**9-тапсырма:**

```python
rows = [("Айгерім Сейітова", "7А", 5.0), ("Әлия Бекова", "7А", 3.33)]
with open("report.html", "w", encoding="utf-8") as f:
    print('<html><head><meta charset="utf-8"><title>Есеп</title></head><body>', file=f)
    print("<h1>Оқушылар үлгерімі</h1><table border='1'>", file=f)
    print("<tr><th>Аты-жөні</th><th>Сынып</th><th>Орташа</th></tr>", file=f)
    for full_name, class_name, avg in rows:
        style = ' style="background:#c8f7c5"' if avg >= 4.5 else ""
        print(f"<tr{style}><td>{full_name}</td><td>{class_name}</td><td>{avg:.2f}</td></tr>", file=f)
    print("</table></body></html>", file=f)
```

(Толық нұсқада `rows` орнына `read_students()` нәтижесі қолданылады.)

</details>
