# Сабақ 106. Резервтік сағат. Күрделі тақырыптар бойынша жеке кеңес беру.

## Тақырыптар

1. Объектілер, сілтемелер және өзгермелі мәндер
2. `self` және класс/объект атрибуттары
3. Циклдегі `lambda` (Tkinter батырмалары)
4. Оқиғалар циклі: неге терезе «қатып» қалады?
5. `JOIN` түрлері
6. Кірістірілген JSON-да жол табу (API жауаптары)
7. Pandas: жолдар мен бағандарды таңдау
8. Git: біріктіру қақтығысы (merge conflict)
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Объектілер, сілтемелер және өзгермелі мәндер

**Негізгі идея:** айнымалы — объектінің өзі емес, оған **жапсырылған белгі (сілтеме)**. `b = a` көшірме жасамайды, екінші белгіні сол объектіге жапсырады.

```python
a = [1, 2, 3]
b = a              # сол тізімге екінші белгі
b.append(4)
print(a)           # a да өзгерді!
c = a.copy()       # жаңа тізім (көшірме)
c.append(5)
print(a, c)
print(a is b, a is c)
```

Нәтижесі:
```text
[1, 2, 3, 4]
[1, 2, 3, 4] [1, 2, 3, 4, 5]
True False
```

**Типтік қате — өзгермелі әдепкі параметр:**
```python
def add_score(score, scores=[]):       # тізім БІР РЕТ, функция анықталғанда құрылады
    scores.append(score)
    return scores


print(add_score(10))
print(add_score(20))                   # күтпеген нәтиже!
```
Нәтижесі:
```text
[10]
[10, 20]
```
Дұрысы:
```python
def add_score(score, scores=None):
    if scores is None:
        scores = []
    scores.append(score)
    return scores


print(add_score(10), add_score(20))
```
Нәтижесі:
```text
[10] [20]
```

> Python Tutor-да осы кодты іске қосып, жебелерді (сілтемелерді) көзбен көріңіз — бұл тақырыпты түсінудің ең жылдам жолы.

## 2. `self` және класс/объект атрибуттары

**Аналогия:** класс — торт пісіретін қалып; объект — сол қалыптан шыққан нақты торт. `self` — «дәл осы торт». Әдіс шақырылғанда Python `self`-ке объектінің өзін автоматты береді: `cake.decorate("гүл")` ≡ `Cake.decorate(cake, "гүл")`.

```python
class Student:
    school = "№25 мектеп"            # класс атрибуты: БАРЛЫҚ объектіге ортақ

    def __init__(self, name):
        self.name = name             # объект атрибуты: әр объектінің өзінікі
        self.grades = []             # әр оқушының ӨЗ тізімі

    def add_grade(self, grade):
        self.grades.append(grade)


a = Student("Аян")
b = Student("Бибігүл")
a.add_grade(5)
print(a.grades, b.grades)
Student.school = "№120 мектеп"
print(a.school, b.school)
```

Нәтижесі:
```text
[5] []
№120 мектеп №120 мектеп
```

Ереже: өзгеретін деректер (тізімдер, сөздіктер) — **тек `__init__` ішінде `self.` арқылы**.

## 3. Циклдегі `lambda` (Tkinter батырмалары)

```python
buttons = []
for i in range(3):
    buttons.append(lambda: print("Батырма", i))   # i мәні емес, i АЙНЫМАЛЫСЫ есте сақталады
for b in buttons:
    b()
```

Нәтижесі:
```text
Батырма 2
Батырма 2
Батырма 2
```

`lambda` шақырылған **сәтте** `i`-дің мәнін іздейді — ал цикл бітті, `i = 2`. Шешімі — мәнді әдепкі параметр арқылы «бекіту»:

```python
buttons = [lambda n=i: print("Батырма", n) for i in range(3)]
for b in buttons:
    b()
```

Нәтижесі:
```text
Батырма 0
Батырма 1
Батырма 2
```

Tkinter-де: `tk.Button(root, text=name, command=lambda n=name: show(n))`.

## 4. Оқиғалар циклі: неге терезе «қатып» қалады?

Tkinter (`mainloop`) мен Pygame (`while running`) — екеуі де **цикл**: «оқиғаны күт → өңде → экранды сал → қайтадан». Егер өңдеуші функция ұзақ жұмыс істесе (`time.sleep(5)`, шексіз `while`, баяу API), цикл тоқтайды — терезе жауап бермейді.

```python
import tkinter as tk

root = tk.Tk()
label = tk.Label(root, text="0", font=("Arial", 24))
label.pack(padx=40, pady=20)
seconds = 0


def tick():
    """Әр секунд сайын өзін қайта жоспарлайды — цикл тоқтамайды."""
    global seconds
    seconds += 1
    label.config(text=str(seconds))
    root.after(1000, tick)          # ҚАТЕ болар еді: while True: time.sleep(1)


root.after(1000, tick)
root.mainloop()
```

Ереже: Tkinter-де қайталанатын әрекет — `after()`, ұзақ әрекет — бөлек ағын (`threading`); Pygame-де — цикл ішінде уақытты `pygame.time.get_ticks()` арқылы тексеру, `sleep` емес.

## 5. `JOIN` түрлері

```text
student                    club
id | name    | club_id     id | title
1  | Аян     | 1           1  | Python
2  | Бибігүл | 2           2  | Шахмат
3  | Дәулет  | NULL        3  | Робототехника

INNER JOIN — тек жұбы барлар:    Аян–Python, Бибігүл–Шахмат
LEFT JOIN (student сол жақта) —   + Дәулет–NULL (үйірмесіз оқушы да шығады)
LEFT JOIN (club сол жақта) —      + NULL–Робототехника (оқушысыз үйірме)
```

```python
import sqlite3

conn = sqlite3.connect(":memory:")
conn.executescript("""
CREATE TABLE club (id INTEGER PRIMARY KEY, title TEXT);
CREATE TABLE student (id INTEGER PRIMARY KEY, name TEXT, club_id INTEGER);
INSERT INTO club VALUES (1, 'Python'), (2, 'Шахмат'), (3, 'Робототехника');
INSERT INTO student VALUES (1, 'Аян', 1), (2, 'Бибігүл', 2), (3, 'Дәулет', NULL);
""")
print(conn.execute("""SELECT c.title, COUNT(s.id) FROM club c
                      LEFT JOIN student s ON s.club_id = c.id
                      GROUP BY c.id""").fetchall())
print(conn.execute("""SELECT c.title, COUNT(s.id) FROM club c
                      JOIN student s ON s.club_id = c.id
                      GROUP BY c.id""").fetchall())
```

Нәтижесі:
```text
[('Python', 1), ('Шахмат', 1), ('Робототехника', 0)]
[('Python', 1), ('Шахмат', 1)]
```

Ереже: «**барлық** үйірмелерді, тіпті бос болса да» деген сұрақ → `LEFT JOIN` және `COUNT(s.id)` (`COUNT(*)` емес — ол бос жолды да санайды).

## 6. Кірістірілген JSON-да жол табу (API жауаптары)

API жауабы — сөздіктер мен тізімдердің «матрешкасы». Қажетті мәнге жету үшін жолды **сыртынан ішіне қарай** қадаммен жазамыз:

```python
answer = {
    "city": {"name": "Астана", "coord": {"lat": 51.17, "lon": 71.45}},
    "list": [
        {"dt_txt": "2027-05-20 12:00", "main": {"temp": 21.5}, "weather": [{"description": "ашық"}]},
        {"dt_txt": "2027-05-20 15:00", "main": {"temp": 23.1}, "weather": [{"description": "бұлтты"}]},
    ],
}
# answer -> "list" (тізім) -> [1] (екінші элемент) -> "weather" (тізім) -> [0] -> "description"
print(answer["list"][1]["weather"][0]["description"])
print(answer["city"]["coord"]["lat"])
temps = [item["main"]["temp"] for item in answer["list"]]
print(max(temps))
print(answer.get("country", "белгісіз"))         # кілт болмаса — қатесіз
```

Нәтижесі:
```text
бұлтты
51.17
23.1
белгісіз
```

Әдіс: жауапты `print(json.dumps(answer, ensure_ascii=False, indent=2))` арқылы әдемі шығарып, әр деңгейде `type(...)` тексеріңіз: `dict` болса — `["кілт"]`, `list` болса — `[индекс]` немесе цикл.

## 7. Pandas: жолдар мен бағандарды таңдау

| Не керек | Код |
|---|---|
| Бір баған (Series) | `df["temp"]` |
| Бірнеше баған | `df[["city", "temp"]]` |
| Шарт бойынша жолдар | `df[df["temp"] > 0]` |
| Екі шарт | `df[(df["temp"] > 0) & (df["city"] == "Алматы")]` — жақша міндетті, `and` емес `&` |
| Белгі бойынша | `df.loc[2, "temp"]`, `df.loc[df["temp"] > 0, "city"]` |
| Позиция бойынша | `df.iloc[0]`, `df.iloc[:3, 1]` |
| Мәнді өзгерту | `df.loc[df["temp"] < -40, "temp"] = None` |

```python
import pandas as pd

df = pd.DataFrame({"city": ["Алматы", "Астана", "Ақтау", "Алматы"],
                   "temp": [12.5, -3.0, 15.2, 9.8]})
warm_almaty = df[(df["temp"] > 10) & (df["city"] == "Алматы")]
print(warm_almaty)
print(df.loc[df["temp"] < 0, "city"].tolist())
```

Нәтижесі:
```text
     city  temp
0  Алматы  12.5
['Астана']
```

Типтік қате: `df[df["temp"] > 0 and df["city"] == "Алматы"]` → `ValueError: The truth value of a Series is ambiguous`. Себебі `and` бүкіл бағанды бір `True/False`-қа айналдыра алмайды; әр жолды бөлек салыстыру үшін `&` керек.

## 8. Git: біріктіру қақтығысы (merge conflict)

Екі адам (немесе екі тармақ) бір файлдың **бір жолын** әртүрлі өзгертсе, Git қайсысын алатынын білмейді және файлға белгі қояды:

```text
<<<<<<< HEAD
GAME_SPEED = 5
=======
GAME_SPEED = 8
>>>>>>> feature/levels
```

Шешу қадамдары:
1. `git status` — қай файлдарда қақтығыс бар (`both modified`).
2. Файлды ашып, **дұрыс нұсқаны қалдырыңыз** (немесе екеуін біріктіріңіз); `<<<<<<<`, `=======`, `>>>>>>>` белгілерін өшіріңіз.
3. Бағдарламаны іске қосып тексеріңіз.
4. `git add <файл>` және `git commit`.

```bash
git status
# файлды түзеткеннен кейін:
git add game/settings.py
git commit -m "Біріктіру: ойын жылдамдығы 8 болып қалдырылды"
```

Қақтығыстың алдын алу: жұмыс басында `git pull`; жиі, шағын коммиттер; командада бір файлды бір уақытта екі адам өзгертпеуге келісу.

## Жиі кездесетін қателер

**1. Көшірменің орнына `b = a`.** `b`-ны өзгерту `a`-ны да өзгертеді. Дұрысы: `a.copy()` немесе `list(a)`; кірістірілген құрылымдар үшін — `copy.deepcopy(a)`.

**2. `LEFT JOIN`-мен бірге `COUNT(*)`.** Жұбы жоқ топ үшін `COUNT(*)` 1 қайтарады (`NULL` бар жол саналады). Дұрысы: `COUNT(s.id)` — тек бос емес мәндерді санайды.

**3. Pandas-та `&`/`|` орнына `and`/`or`.** `ValueError: The truth value of a Series is ambiguous` береді. Дұрысы: `&`, `|`, `~` және әр шарттың айналасында жақша.

**4. Қақтығыс белгілерімен коммит жасау.** Файлда `<<<<<<<` және `>>>>>>>` қалып қойған — бағдарлама іске қосылмайды (`SyntaxError`). Дұрысы: `git add` алдында белгілерді іздеумен тауып, бағдарламаны іске қосу.

## Негізгі ұғымдар
| Ұғым | Түсіндірме |
|---|---|
| Сілтеме | айнымалы мен объект арасындағы байланыс («белгі») |
| Өзгермелі объект | орнында өзгертуге болатын объект (тізім, сөздік, жиын) |
| Класс атрибуты | барлық объектіге ортақ атрибут |
| Тұйықталу (closure) | функцияның сыртқы айнымалыны «есте сақтауы» |
| Оқиғалар циклі | оқиғаларды күтіп, өңдеп, экранды салатын негізгі цикл |
| `LEFT JOIN` | сол жақ кестенің барлық жолын сақтайтын біріктіру |
| Логикалық маска | Pandas-та жолдарды таңдайтын `True/False` бағаны |
| Біріктіру қақтығысы | Git бір жолдың екі нұсқасын автоматты біріктіре алмаған жағдай |

## Ескерту

- `a.copy()` пен `list(a)` **үстірт** көшірме жасайды: кірістірілген тізімдер ортақ болып қалады. Толық көшірме үшін `copy.deepcopy()` қолданыңыз.
- `is` операторы объектілердің бірегейлігін, `==` — мәндерін салыстырады; `x is None` — PEP 8 ұсынатын `None`-ға тексеру.
- `lambda n=i:` тәсілінің орнына `functools.partial(show, i)` қолдануға болады — ол да аргумент мәнін құрылған сәтте бекітеді.
- Pandas `df.query("temp > 10 and city == 'Алматы'")` жазбасын қолдайды — сұраныс жолының ішінде `and`/`or` рұқсат етілген.
- Python Tutor (https://pythontutor.com) жадтағы сілтемелер мен объектілерді көрнекі көрсетеді; Python деректер моделі https://docs.python.org/3/reference/datamodel.html бетінде сипатталған.
