# Сабақ 67. Практикалық тапсырмалар: интерфейс және деректерді сақтау

> Бүгін өз жобаңа **мәзір**, **енгізуді тексеру** және **файлда сақтау** мүмкіндіктерін қосып, барлық бөліктерді `main()` арқылы біріктіресің.

## Базалық деңгей (міндетті)

**1-қадам. Мәзір.** `show_menu()` және `main()` функцияларын жаз. Мәзірде кемінде 3 пункт және «0 – Шығу» болсын. Қате таңдауда бағдарлама құламай, қайта сұрауы керек.

**2-қадам. Көмекші функциялар.** Жобаңа қажетті енгізу функцияларын қос: `read_int(prompt, low, high)`, `read_choice(prompt, options)`, `read_text(prompt)`. Бағдарламадағы барлық `int(input(...))` жолдарын осыларға ауыстыр.

**3-қадам. Деректер файлы.** 66-сабақта код ішінде жазылған деректерді (сұрақтар, сөздер, тауарлар) мәтіндік файлға шығар және `load_...()` функциясымен оқы. Файл жоқ болса, бағдарлама құламауы тиіс.

**4-қадам. Нәтижені сақтау.** Жобаңның нәтижесін (ұпай, операция, тапсырма, күй) файлға жазатын `save_...()` функциясын жаз. Бағдарламаны жауып, қайта ашып тексер – деректер сақталды ма?

**5-қадам. Безендіру.** Тақырып (`center`), бөлгіш сызықтар, туралау, түсінікті хабарламалар («Сақталды!», «Табылмады»).

## Кеңейтілген деңгей

**6-қадам. Деректерді өзгерту.** Деректерді **өзгертуге/жоюға** мүмкіндік бер (мысалы, тапсырманы «орындалды» деп белгілеу, сұрақты жою) – «жүктеу → өзгерту → `w` арқылы сақтау» үлгісі.

**7-қадам. Статистика экраны.** Сақталған деректер бойынша қорытынды шығар: ТОП-5, орташа мән, барлығы, ең жиі т.б.

**8-қадам. «Пайдаланушы тесті».** Көршіңе бағдарламаңды **ешқандай түсіндірусіз** қолданып көруге бер. Оның қай жерде тосылғанын, не сұрағанын жазып ал – сол жерлерді жақсарт.

## ★ Олимпиадалық / шығармашылық тапсырма

**9. Баптаулар файлы.** `settings.txt` файлын құр (мысалы, `level=2`, `sound=иә`, `questions=10`). Бағдарлама іске қосылғанда баптауларды сөздікке оқиды, мәзірде оларды өзгертуге болады.

**10. Turtle-қосымша.** Жобаңның нәтижесін графикалық түрде көрсет: мысалы, викторинадан кейін ұпайға сай `turtle` арқылы медаль (алтын/күміс/қола шеңбер) немесе нәтижелердің бағаналы диаграммасын сал.

## Көмек карточкалары

**А. Тапсырманы мәзірден таңдау (нөмір арқылы):**

```python
tasks = ["Математика: №125", "Ағылшын: сөздер", "Python: жоба"]
for i in range(len(tasks)):
    print(f"{i + 1}. {tasks[i]}")
num = input("Қай тапсырманы жоямыз? ")
if num.isdigit() and 1 <= int(num) <= len(tasks):
    removed = tasks.pop(int(num) - 1)
    print("Жойылды:", removed)
else:
    print("Мұндай нөмір жоқ.")
```

**Ә. Баптауларды `кілт=мән` түрінде оқу:**

```python
settings = {"level": "1", "questions": "5"}     # әдепкі мәндер
try:
    with open("settings.txt", encoding="utf-8") as f:
        for line in f:
            if "=" in line:
                key, value = line.strip().split("=", 1)
                settings[key] = value
except FileNotFoundError:
    pass
print(settings)
```

**Б. Тамагочидің күйін бір жолға сақтау және оқу:**

```python
pet = {"name": "Мысық", "hunger": 40, "mood": 55}
with open("pet.txt", "w", encoding="utf-8") as f:
    f.write(f"{pet['name']};{pet['hunger']};{pet['mood']}\n")

with open("pet.txt", encoding="utf-8") as f:
    name, hunger, mood = f.readline().strip().split(";")
pet = {"name": name, "hunger": int(hunger), "mood": int(mood)}
print(pet)
```

## Үй тапсырмасы

1. Жобаңның барлық бөліктері `main()` арқылы біріктірілген, бағдарлама басынан аяғына дейін жұмыс істейтін күйге жеткіз.
2. Прогресс-парақты толтыр.
3. Келесі сабаққа: бағдарламаңды «бұзуға» тырысатын 10 түрлі әрекет ойлап кел (қате енгізу, бос файл, т.б.) – бұл тестілеуге дайындық.

## Бағалау критерийлері

| Критерий | Сипаттамасы | Ұпай |
|---|---|---|
| Бағдарламаның дұрыс жұмыс істеуі | Мәзір жұмыс істейді, деректер сақталады және жүктеледі | 40 |
| Алгоритмнің тиімділігі | Логика / интерфейс / сақтау бөлінген, көмекші функциялар қолданылған | 25 |
| Кодтың оқылымдылығы және безендірілуі | Түсінікті хабарламалар, безендіру, түсініктемелер | 20 |
| Өз бетінше және жоспар бойынша жұмыс | Жоспар орындалды, прогресс-парақ толтырылды | 15 |
| **Барлығы** | | **100** |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**Демо-жоба «Қазақстан викторинасы» – 67-сабақ соңындағы толық нұсқасы.**

`questions.txt`:

```text
# Қазақстан викторинасы: сұрақ|жауап
Қазақстанның астанасы қай қала?|астана
Қазақстанның ұлттық валютасы?|теңге
Қазақстан тәуелсіздігін қай жылы жариялады?|1991
Балқаш – көл ме, әлде өзен бе?|көл
Алматыдағы биік таулы мұз айдыны?|медеу
«Абай жолы» роман-эпопеясының авторы?|мұхтар әуезов
Байқоңыр ғарыш айлағы қай облыста орналасқан?|қызылорда
Қазақстан туындағы құс қандай?|бүркіт
```

`quiz.py`:

```python
"""Қазақстан викторинасы – қорытынды жобаның демо-нұсқасы (67-сабақ)."""
import random
from datetime import datetime

QUESTIONS_FILE = "questions.txt"
RESULTS_FILE = "results.txt"
LINE = "=" * 40


# ---------- Деректерді сақтау ----------

def load_questions(file_name):
    """Сұрақтарды файлдан оқиды: әр жолда 'сұрақ|жауап'."""
    questions = {}
    try:
        with open(file_name, encoding="utf-8") as f:
            for line in f:
                line = line.strip()
                if line == "" or line.startswith("#") or "|" not in line:
                    continue
                question, answer = line.split("|", 1)
                questions[question.strip()] = answer.strip()
    except FileNotFoundError:
        print(f"Ескерту: {file_name} табылмады.")
    return questions


def add_question(file_name, question, answer):
    """Жаңа сұрақты файлдың соңына қосады."""
    with open(file_name, "a", encoding="utf-8") as f:
        f.write(f"{question}|{answer}\n")


def save_result(file_name, name, score, total):
    """Нәтижені файлға қосады: аты;ұпай;барлығы;күні."""
    stamp = datetime.now().strftime("%d.%m.%Y %H:%M")
    with open(file_name, "a", encoding="utf-8") as f:
        f.write(f"{name};{score};{total};{stamp}\n")


def load_results(file_name):
    """Нәтижелерді [пайыз, аты, ұпай, барлығы, күні] тізімі ретінде қайтарады."""
    results = []
    try:
        with open(file_name, encoding="utf-8") as f:
            for line in f:
                parts = line.strip().split(";")
                if len(parts) != 4:
                    continue
                try:
                    score = int(parts[1])
                    total = int(parts[2])
                except ValueError:
                    continue
                percent = round(score / total * 100)
                results.append([percent, parts[0], score, total, parts[3]])
    except FileNotFoundError:
        pass
    return results


# ---------- Негізгі логика ----------

def check_answer(user_answer, correct_answer):
    """Жауап дұрыс болса True қайтарады."""
    return user_answer.strip().lower() == correct_answer.strip().lower()


def grade(score, total):
    """Ұпайды бағаға айналдырады."""
    percent = score / total * 100
    if percent >= 85:
        return "5 – керемет!"
    elif percent >= 65:
        return "4 – жақсы"
    elif percent >= 40:
        return "3 – жаман емес"
    return "2 – тағы дайындал"


# ---------- Интерфейс ----------

def read_text(prompt):
    """Бос емес жол енгізілгенше сұрайды."""
    while True:
        text = input(prompt).strip()
        if text:
            return text
        print("Бос болмауы керек!")


def read_choice(prompt, options):
    """Тек рұқсат етілген мәндердің бірін қабылдайды."""
    while True:
        choice = input(prompt).strip()
        if choice in options:
            return choice
        print("Мүмкін нұсқалар:", ", ".join(options))


def show_menu():
    print()
    print(LINE)
    print("ҚАЗАҚСТАН ВИКТОРИНАСЫ".center(40))
    print(LINE)
    print("  1 – Ойнау")
    print("  2 – Рекордтар (ТОП-5)")
    print("  3 – Жаңа сұрақ қосу")
    print("  0 – Шығу")
    print(LINE)


def play(questions):
    """Ойын: сұрақтарды кездейсоқ ретпен қояды, ұпайды қайтарады."""
    items = list(questions.items())
    random.shuffle(items)
    score = 0
    for i in range(len(items)):
        question, answer = items[i]
        user = input(f"\n[{i + 1}/{len(items)}] {question}\n> ")
        if check_answer(user, answer):
            print("✔ Дұрыс!")
            score += 1
        else:
            print("✘ Қате. Дұрыс жауап:", answer)
    return score


def show_records(results):
    if not results:
        print("Әзірге нәтижелер жоқ. Бірінші болып ойна!")
        return
    results.sort(reverse=True)
    print(f"{'№':<3}{'Аты':<12}{'Ұпай':>6}{'%':>6}  Күні")
    place = 1
    for percent, name, score, total, stamp in results[:5]:
        print(f"{place:<3}{name:<12}{str(score) + '/' + str(total):>6}{percent:>5}%  {stamp}")
        place += 1


def main():
    while True:
        show_menu()
        choice = read_choice("Таңдауың: ", ["1", "2", "3", "0"])
        if choice == "1":
            questions = load_questions(QUESTIONS_FILE)
            if not questions:
                print("Сұрақтар жоқ. Алдымен сұрақ қос (3).")
                continue
            name = read_text("Атың: ")
            score = play(questions)
            total = len(questions)
            print(f"\n{name}, нәтижең: {score}/{total}. Баға: {grade(score, total)}")
            save_result(RESULTS_FILE, name, score, total)
            print("Нәтиже сақталды.")
        elif choice == "2":
            show_records(load_results(RESULTS_FILE))
        elif choice == "3":
            question = read_text("Сұрақ: ")
            answer = read_text("Дұрыс жауап: ")
            add_question(QUESTIONS_FILE, question, answer)
            print("Сұрақ қосылды!")
        else:
            print("Сау бол! Келесі кездескенше!")
            break


main()
```

`results.txt` (бірнеше ойыннан кейін):

```text
Дана;7;8;05.05.2027 14:20
Арман;6;8;05.05.2027 14:35
```

**9-тапсырма (баптаулар) – мәзірде өзгерту фрагменті:**

```python
def save_settings(file_name, settings):
    with open(file_name, "w", encoding="utf-8") as f:
        for key, value in settings.items():
            f.write(f"{key}={value}\n")


settings = {"level": "1", "questions": "5"}
key = input("Қай баптауды өзгертеміз? ")
if key in settings:
    settings[key] = input("Жаңа мән: ")
    save_settings("settings.txt", settings)
    print("Сақталды.")
else:
    print("Мұндай баптау жоқ.")
```

**10-тапсырма (медаль):**

```python
import turtle


def draw_medal(percent):
    if percent >= 85:
        color = "gold"
    elif percent >= 65:
        color = "silver"
    else:
        color = "peru"
    t = turtle.Turtle()
    t.hideturtle()
    t.penup()
    t.goto(0, -80)
    t.pendown()
    t.fillcolor(color)
    t.begin_fill()
    t.circle(80)
    t.end_fill()
    t.penup()
    t.goto(0, -15)
    t.write(f"{percent}%", align="center", font=("Arial", 24, "bold"))
    turtle.done()


draw_medal(88)
```

**Педагогке кеңестер:**
- Сабақтың бірінші жартысында барлық оқушыда мәзір жұмыс істейтінін тексер; екінші жартысында – файлдар.
- Әлсіз оқушыларға демо-жобаның `load_questions` / `save_result` функцияларын үлгі ретінде көрсетіп, оны өз деректеріне бейімдеуге көмектес.
- «Пайдаланушы тестінен» кейін әр оқушы кемінде бір жақсартуды енгізуі тиіс.

</details>
