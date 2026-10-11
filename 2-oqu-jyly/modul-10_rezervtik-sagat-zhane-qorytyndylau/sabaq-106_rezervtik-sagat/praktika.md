# Сабақ 106. Практикалық тапсырмалар: диагностика және кеңес станциялары

## Базалық деңгей

### 1-тапсырма. Диагностикалық тест (15 мин, компьютерсіз)
Әр сұраққа жауап жазыңыз. Содан кейін педагог берген кілт бойынша өзіңіз тексеріп, қате кеткен тақырыптарды белгілеңіз — бұл сіздің станциялар маршрутыңыз.

**А. Сілтемелер**
1. Не шығады?
```python
x = [1, 2]
y = x
y += [3]
print(x)
```
2. `def f(items=[]):` деп жазудың қаупі неде?

**Ә. `self`, атрибуттар**
3. Не шығады?
```python
class Box:
    things = []

    def put(self, t):
        self.things.append(t)


a, b = Box(), Box()
a.put("доп")
print(b.things)
```
4. `obj.method(5)` шақыруы класс арқылы қалай жазылады?

**Б. Циклдегі lambda**
5. Не шығады?
```python
fs = [lambda: k * 10 for k in range(1, 4)]
print([f() for f in fs])
```
6. Осы кодты `[10, 20, 30]` шығатындай қалай түзетесіз?

**В. Оқиғалар циклі**
7. Батырма басылғанда `time.sleep(3)` орындалса, терезеге не болады?
8. Tkinter-де әр 500 мс сайын функцияны шақыру үшін қандай әдіс қолданылады?

**Г. JOIN**
9. 5 оқушының 2-еуінің үйірмесі жоқ (`club_id` = NULL). `student JOIN club` неше жол береді? `student LEFT JOIN club` ше?
10. Әр үйірмедегі оқушы санын, бос үйірмелерді де қоса, қалай есептейміз?

**Ғ. Кірістірілген JSON**
11. `data = {"items": [{"name": "A", "tags": ["x", "y"]}]}`. `"y"` мәнін қалай аламыз?
12. Кілт болмауы мүмкін болса, `KeyError`-сыз қалай оқимыз?

**Д. Pandas таңдау**
13. `temp` бағаны 0-ден үлкен **және** `city` «Астана» болатын жолдарды таңдаңыз.
14. `df.iloc[0]` мен `df.loc[0]` айырмашылығы неде?

**Е. Git қақтығысы**
15. `<<<<<<< HEAD` пен `=======` арасында қай нұсқа тұр?
16. Қақтығысты түзеткеннен кейін қандай екі команда орындалады?

### 2-тапсырма. Станция карточкалары
Маршрутыңыздағы әр тақырып бойынша: теориядағы түсіндірмені оқыңыз → мысалды іске қосыңыз (Python Tutor-да да) → төмендегі станция тапсырмасын орындаңыз.

**А-станция.** Функция оқушылар тізімін алып, әр оқушының атына «(тексерілді)» қосылған **жаңа** тізім қайтарсын, бірақ бастапқы тізім өзгермесін. Тексеру: функциядан кейін бастапқы тізімді шығарыңыз.

**Ә-станция.** `Team` класында әр команданың өз ойыншылар тізімі болсын, ал `league = "Мектеп лигасы"` барлық командаға ортақ болсын. Екі команда құрып, біреуіне ойыншы қосыңыз — екіншісінде пайда болмайтынын көрсетіңіз.

**Б-станция.** Tkinter-де 1-ден 5-ке дейін бес батырма циклмен жасалсын; әр батырма басқанда белгіде «Сіз N-ді таңдадыңыз» шықсын.

**В-станция.** Tkinter-де 10-нан 0-ге дейін кері санайтын таймер жасаңыз («Бастау» батырмасы), терезе санау кезінде қатпауы керек; 0-де «Уақыт бітті!» шықсын.

**Г-станция.** `author(id, name)` және `book(id, title, author_id)` кестелерін құрып, кітабы жоқ авторды да қосыңыз. Әр автордың кітап санын (0-ді қоса) шығарыңыз.

**Ғ-станция.** Төмендегі API-жауаптан әр күннің күнін және ең жоғары температурасын жұппен шығарыңыз, сосын ең суық күнді табыңыз.
```text
{"daily": {"time": ["2027-05-20", "2027-05-21", "2027-05-22"],
           "temperature_2m_max": [24.1, 19.7, 22.3]}}
```

**Д-станция.** `df` кестесінде `name`, `grade` (сынып), `score` бағандары бар. 8-сыныптың 80-нен жоғары ұпай алған оқушыларының аттарын тізім ретінде шығарыңыз, ал барлық 0-ден кем ұпайды `None`-ға ауыстырыңыз.

**Е-станция.** Педагог дайындаған жаттығу репозиторийінде (`conflict-practice`) `main` және `feature` тармақтарын біріктіріп (`git merge feature`), қақтығысты шешіңіз де, нәтижені коммиттеңіз.

## Кеңейтілген деңгей

### 3-тапсырма. Тәлімгер болу
Диагностикада 2/2 алған тақырыбыңыз бойынша станцияда тәлімгер болыңыз. Ережелер: пернетақтаны ұстамайсыз; жауапты айтпайсыз — бағыттаушы сұрақ қоясыз («Бұл жолда `k` нешеге тең?», «`type()` не көрсетеді?»). Сабақ соңында 2 сөйлем жазыңыз: қай сұрағыңыз ең көп көмектесті?

### 4-тапсырма. Өз қате-мысалыңыз
Маршрутыңыздағы тақырып бойынша **өзіңіз** «тұзақ» код ойлап табыңыз (5–8 жол): бір қарағанда дұрыс, бірақ күтпеген нәтиже береді. Оны құрбыңызға шешуге беріңіз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 5-тапсырма. Терең көшірме
`copy.copy` пен `copy.deepcopy` айырмашылығын көрсететін мысал құрыңыз (тізімдер ішіндегі тізімдер), содан кейін `copy` модулінсіз кірістірілген тізімдер мен сөздіктерді терең көшіретін өз рекурсиялық функцияңызды жазыңыз.

### 6-тапсырма. JSON жолдарын табушы
Кез келген кірістірілген JSON құрылымындағы барлық «жапырақ» мәндердің толық жолын шығаратын функция жазыңыз: `data["list"][1]["main"]["temp"] = 23.1`. API жауаптарын зерттеуге ыңғайлы құрал болады.

## Үй тапсырмасы
1. Маршрутыңыздағы тақырыптың қайта тексеру сұрақтарына (педагог береді) жауап беріңіз.
2. Қорытынды жобаңызда осы тақырыптардың қатесі бар ма — тексеріңіз (мысалы, өзгермелі әдепкі параметр, циклдегі `lambda`).
3. Келесі сабаққа: жобаңызда не аяқталмағанын (Issues) тізімдеп келіңіз.

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**Диагностика кілті.**
1. `[1, 2, 3]` — `+=` тізімді орнында өзгертеді, `y` мен `x` — бір объект.
2. Тізім функция анықталғанда бір рет құрылады және барлық шақыруға ортақ болады.
3. `['доп']` — `things` класс атрибуты, барлық объектіге ортақ.
4. `Class.method(obj, 5)`.
5. `[30, 30, 30]`.
6. `[lambda k=k: k * 10 for k in range(1, 4)]`.
7. Терезе 3 секунд «қатып» қалады (жауап бермейді, қайта салынбайды).
8. `root.after(500, func)` (функция ішінде қайта шақыру).
9. `JOIN` — 3 жол; `LEFT JOIN` — 5 жол.
10. `SELECT c.title, COUNT(s.id) FROM club c LEFT JOIN student s ON s.club_id = c.id GROUP BY c.id`.
11. `data["items"][0]["tags"][1]`.
12. `d.get("кілт", әдепкі)`.
13. `df[(df["temp"] > 0) & (df["city"] == "Астана")]`.
14. `iloc` — позиция бойынша (0-ші жол), `loc` — индекс белгісі бойынша (белгісі 0 жол); сұрыптаудан/сүзуден кейін олар әртүрлі болуы мүмкін.
15. Ағымдағы тармақтың (біздің) нұсқасы.
16. `git add <файл>` және `git commit`.

Бағалау: тақырып бойынша 0/2 немесе 1/2 — маршрутқа қосылады; 2/2 — тәлімгер болуға ұсынылады.

**А-станция.**
```python
def mark_checked(names):
    """Жаңа тізім қайтарады; бастапқы тізім өзгермейді."""
    return [name + " (тексерілді)" for name in names]


students = ["Аян", "Бибігүл"]
checked = mark_checked(students)
print(students)
print(checked)
```
Нәтижесі:
```text
['Аян', 'Бибігүл']
['Аян (тексерілді)', 'Бибігүл (тексерілді)']
```

**Ә-станция.**
```python
class Team:
    league = "Мектеп лигасы"

    def __init__(self, name):
        self.name = name
        self.players = []


a, b = Team("Барыс"), Team("Қыран")
a.players.append("Ерлан")
print(a.players, b.players, a.league == b.league)
```
Нәтижесі:
```text
['Ерлан'] [] True
```

**Б-станция.**
```python
import tkinter as tk

root = tk.Tk()
root.title("Таңдау")
label = tk.Label(root, text="Санды таңдаңыз", font=("Arial", 14))
label.pack(pady=8)
frame = tk.Frame(root)
frame.pack(padx=10, pady=10)
for n in range(1, 6):
    tk.Button(frame, text=str(n), width=4,
              command=lambda n=n: label.config(text=f"Сіз {n}-ді таңдадыңыз")
              ).pack(side="left", padx=3)
root.mainloop()
```

**В-станция.**
```python
import tkinter as tk

root = tk.Tk()
root.title("Кері санақ")
label = tk.Label(root, text="10", font=("Arial", 32))
label.pack(padx=40, pady=10)


def countdown(n):
    if n < 0:
        label.config(text="Уақыт бітті!")
        start_button.config(state="normal")
        return
    label.config(text=str(n))
    root.after(1000, countdown, n - 1)     # after қосымша аргументтерді функцияға береді


def start():
    start_button.config(state="disabled")  # қайта басып, екі таймер іске қоспау үшін
    countdown(10)


start_button = tk.Button(root, text="Бастау", command=start)
start_button.pack(pady=10)
root.mainloop()
```

**Г-станция.**
```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE author (id INTEGER PRIMARY KEY, name TEXT);
CREATE TABLE book (id INTEGER PRIMARY KEY, title TEXT, author_id INTEGER REFERENCES author(id));
INSERT INTO author VALUES (1, 'М. Әуезов'), (2, 'Ғ. Мүсірепов'), (3, 'Жаңа автор');
INSERT INTO book (title, author_id) VALUES
    ('Абай жолы', 1), ('Қараш-Қараш оқиғасы', 1), ('Ұлпан', 2);
""")
for name, count in conn.execute("""
        SELECT a.name, COUNT(b.id) FROM author a
        LEFT JOIN book b ON b.author_id = a.id
        GROUP BY a.id ORDER BY a.id"""):
    print(f"{name}: {count}")
```
Нәтижесі:
```text
М. Әуезов: 2
Ғ. Мүсірепов: 1
Жаңа автор: 0
```

**Ғ-станция.**
```python
import json

text = """{"daily": {"time": ["2027-05-20", "2027-05-21", "2027-05-22"],
           "temperature_2m_max": [24.1, 19.7, 22.3]}}"""
daily = json.loads(text)["daily"]
pairs = list(zip(daily["time"], daily["temperature_2m_max"]))
for day, t in pairs:
    print(day, t)
coldest = min(pairs, key=lambda p: p[1])
print("Ең суық күн:", coldest[0])
```
Нәтижесі:
```text
2027-05-20 24.1
2027-05-21 19.7
2027-05-22 22.3
Ең суық күн: 2027-05-21
```

**Д-станция.**
```python
import pandas as pd

df = pd.DataFrame({"name": ["Аян", "Бибігүл", "Дәулет", "Есет"],
                   "grade": [8, 8, 9, 8],
                   "score": [85, 78, 92, -1]})
best = df.loc[(df["grade"] == 8) & (df["score"] > 80), "name"].tolist()
print(best)
df.loc[df["score"] < 0, "score"] = None
print(df["score"].tolist())
```
Нәтижесі:
```text
['Аян']
[85.0, 78.0, 92.0, nan]
```
(`None` қойылғанда баған `float` түріне ауысады және бос мән `nan` болып көрінеді — талқылауға пайдалы.)

**Е-станция.** Жаттығу репозиторийін дайындау (педагогке):
```bash
git init conflict-practice
cd conflict-practice
echo "GAME_SPEED = 5" > settings.py
git add . && git commit -m "Бастапқы баптау"
git switch -c feature
echo "GAME_SPEED = 8" > settings.py
git commit -am "Жылдамдық 8"
git switch main
echo "GAME_SPEED = 6" > settings.py
git commit -am "Жылдамдық 6"
git merge feature        # -> CONFLICT (content): Merge conflict in settings.py
```
(Ескерту: `git switch main` командасы әдепкі тармақ `main` болғанда жұмыс істейді; ескі Git нұсқаларында ол `master` болуы мүмкін — `git branch` арқылы тексеріңіз.)

**5-тапсырма.**
```python
import copy

board = [[0, 0], [0, 0]]
shallow = copy.copy(board)
deep = copy.deepcopy(board)
board[0][0] = 1
print(shallow[0][0], deep[0][0])     # 1 0 — таяз көшірме ішкі тізімдерді бөліседі


def deep_copy(obj):
    """Тізімдер мен сөздіктерді рекурсивті түрде көшіреді."""
    if isinstance(obj, list):
        return [deep_copy(item) for item in obj]
    if isinstance(obj, dict):
        return {key: deep_copy(value) for key, value in obj.items()}
    return obj                         # сан, жол т.б. — өзгермейтін, көшіру қажет емес


data = {"scores": [[1, 2], [3]], "name": "Аян"}
clone = deep_copy(data)
data["scores"][0].append(99)
print(clone["scores"])
```
Нәтижесі:
```text
1 0
[[1, 2], [3]]
```

**6-тапсырма.**
```python
def leaf_paths(obj, path="data"):
    """Кірістірілген құрылымның барлық жапырақ мәндерінің жолдарын береді."""
    if isinstance(obj, dict):
        for key, value in obj.items():
            yield from leaf_paths(value, f'{path}["{key}"]')
    elif isinstance(obj, list):
        for i, value in enumerate(obj):
            yield from leaf_paths(value, f"{path}[{i}]")
    else:
        yield path, obj


answer = {"list": [{"main": {"temp": 21.5}}, {"main": {"temp": 23.1}}], "ok": True}
for p, value in leaf_paths(answer):
    print(p, "=", value)
```
Нәтижесі:
```text
data["list"][0]["main"]["temp"] = 21.5
data["list"][1]["main"]["temp"] = 23.1
data["ok"] = True
```
</details>
