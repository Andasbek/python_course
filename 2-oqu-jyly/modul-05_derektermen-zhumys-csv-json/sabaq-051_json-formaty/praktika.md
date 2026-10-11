# Сабақ 51. Практикалық тапсырмалар: JSON сериялау және десериялау

Барлық тапсырмада: `import json`, файлдар `encoding="utf-8"`, жазғанда `ensure_ascii=False, indent=2`.

## Базалық деңгей

### 1-тапсырма. Менің профилім

Өзің туралы сөздік жаса: аты, жасы, қаласы, үйірмеге қатысады ма (`True`/`False`), хоббилер тізімі, сүйікті пәндер бойынша бағалар сөздігі. Оны `profile.json` файлына сақта, содан кейін файлдан оқып, мына түрде шығар:

```text
Шығару:
Аты: Айдана (14 жас, Алматы)
Хоббилері: шахмат, домбыра, Python
Ең жоғары баға: информатика — 5
```

### 2-тапсырма. JSON-ды түзет

Төмендегі мәтінде 4 қате бар. Қателерді тауып, түзетілген мәтінді `json.loads` арқылы тексер.

```text
{
  'city': "Тараз",
  "founded": 2000 жыл,
  "is_capital": False,
  "districts": ["Жамбыл", "Әулиеата",],
}
```

### 3-тапсырма. Кітапхана: кітап қосу

`library.json` файлында кітаптар тізімі сақталады (файл жоқ болса — бос тізімнен бастау). Бағдарлама пайдаланушыдан кітап атауын, авторын және жылын сұрап, тізімге қосады және файлды қайта жазады. Соңында барлық кітаптарды жылы бойынша сұрыптап шығарады.

```text
Енгізу:
Атауы: Абай жолы
Авторы: Мұхтар Әуезов
Жылы: 1942
Шығару:
Кітап қосылды. Кітапханада барлығы 3 кітап:
1942 — «Абай жолы» (Мұхтар Әуезов)
1957 — «Менің атым Қожа» (Бердібек Соқпақбаев)
1976 — «Көшпенділер» (Ілияс Есенберлин)
```

### 4-тапсырма. Ауа райы деректерін оқу

API-дан келген жауапқа ұқсас мына JSON жолын (`json.loads`) өңде:

```text
{"city": "Астана", "unit": "°C",
 "days": [
   {"date": "2027-01-04", "min": -24, "max": -15, "desc": "аяз"},
   {"date": "2027-01-05", "min": -20, "max": -12, "desc": "қар"},
   {"date": "2027-01-06", "min": -27, "max": -18, "desc": "ашық"}
 ]}
```

Әр күнді шығар, ең суық күнді және орташа ең жоғары температураны тап.

```text
Шығару:
Астана:
2027-01-04: -24..-15 °C, аяз
2027-01-05: -20..-12 °C, қар
2027-01-06: -27..-18 °C, ашық
Ең суық күн: 2027-01-06 (-27 °C)
Орташа max: -15.0 °C
```

## Кеңейтілген деңгей

### 5-тапсырма. Ойынның сақталуы (save game)

Pygame ойынымыз үшін ойын күйін сақтайтын функциялар жаз:
- `save_game(state, path="save.json")` — күйді сақтайды;
- `load_game(path="save.json")` — күйді оқиды; файл жоқ немесе бұзылған болса, жаңа ойынның күйін қайтарады.

Күй мысалы: `{"player": {"name": "Әлия", "x": 120, "y": 300, "lives": 2}, "level": 3, "score": 1450, "inventory": ["кілт", "қалқан"]}`.

### 6-тапсырма. CSV → JSON түрлендіргіш

50-сабақтағы `journal.csv` файлын оқып, оны мына құрылымдағы `journal.json`-ға айналдыр (сыныптар бойынша топталған, бағалар — сандар):

```text
{
  "9А": [
    {"name": "Айгерім Нұрланқызы", "marks": {"math": 5, "informatics": 5, "physics": 4}},
    ...
  ],
  "9Ә": [...]
}
```

### 7-тапсырма. Класс объектілерін сақтау

`Contact` класын жаз (аты, телефоны, тегтер жиыны — `set`). `to_dict()`/`from_dict()` әдістері арқылы контактілер тізімін `contacts.json`-ға сақтап, қайта оқы. Жиынды JSON-ға қалай жазу керек екенін ойлан.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. JSON-дағы барлық жолдарды табу

Кез келген кірістірілген JSON құрылымындағы **барлық** мәндердің «жолын» шығаратын рекурсивті функция жаз:

```text
Кіріс: {"team": "Қыран", "members": [{"name": "Әлия"}, {"name": "Нұрлан"}], "game": {"levels": [1, 2]}}
Шығару:
team = Қыран
members[0].name = Әлия
members[1].name = Нұрлан
game.levels[0] = 1
game.levels[1] = 2
```

### 9-тапсырма. Викторина JSON-нан

Сұрақтар `quiz.json` файлында сақталатын викторина бағдарламасын жаз (сұрақ, жауап нұсқалары, дұрыс жауап индексі, түсіндірме). Нәтиже (аты, ұпай, күні) `results.json` файлына қосылып отырсын. Сұрақтарды Қазақстан тарихы немесе Python тақырыбында өзің құрастыр (кемінде 5).

## Үй тапсырмасы

1. 9-тапсырманы аяқта немесе өз «сүйікті фильмдер/кітаптар» каталогыңды JSON файлында жаса (кемінде 5 жазба, әрқайсысында тізім және ішкі сөздік болсын).
2. Браузерде `https://open.er-api.com/v6/latest/USD` адресін ашып, JSON құрылымын қара: қандай кілттер бар? Теңге бағамына қандай «жол» арқылы жетуге болады? (API-мен 66-сабақта жұмыс істейміз.)

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**

```python
import json

profile = {
    "name": "Айдана",
    "age": 14,
    "city": "Алматы",
    "is_member": True,
    "hobbies": ["шахмат", "домбыра", "Python"],
    "marks": {"математика": 4, "информатика": 5, "физика": 4},
}
with open("profile.json", "w", encoding="utf-8") as f:
    json.dump(profile, f, ensure_ascii=False, indent=2)

with open("profile.json", encoding="utf-8") as f:
    p = json.load(f)

best = max(p["marks"], key=p["marks"].get)
print(f"Аты: {p['name']} ({p['age']} жас, {p['city']})")
print("Хоббилері:", ", ".join(p["hobbies"]))
print(f"Ең жоғары баға: {best} — {p['marks'][best]}")
```

Нәтижесі:

```text
Аты: Айдана (14 жас, Алматы)
Хоббилері: шахмат, домбыра, Python
Ең жоғары баға: информатика — 5
```

**2-тапсырма.** Қателер: (1) `'city'` — жалқы тырнақша; (2) `2000 жыл` — сан емес (не `2000`, не `"2000 жыл"`); (3) `False` → `false`; (4) массивтегі және объектідегі соңғы үтірлер.

```python
import json

fixed = """
{
  "city": "Тараз",
  "founded": 2000,
  "is_capital": false,
  "districts": ["Жамбыл", "Әулиеата"]
}
"""
print(json.loads(fixed))
```

Нәтижесі:

```text
{'city': 'Тараз', 'founded': 2000, 'is_capital': False, 'districts': ['Жамбыл', 'Әулиеата']}
```

(Ескерту: Тараздың тарихы 2000 жылдан асады — 2002 жылы 2000 жылдығы тойланған; мысал тек синтаксисті үйретуге арналған.)

**3-тапсырма**

```python
import json
import os

FILE = "library.json"

books = []
if os.path.exists(FILE):
    with open(FILE, encoding="utf-8") as f:
        books = json.load(f)

title = input("Атауы: ").strip()
author = input("Авторы: ").strip()
year = int(input("Жылы: "))
books.append({"title": title, "author": author, "year": year})

with open(FILE, "w", encoding="utf-8") as f:
    json.dump(books, f, ensure_ascii=False, indent=2)

print(f"Кітап қосылды. Кітапханада барлығы {len(books)} кітап:")
for b in sorted(books, key=lambda b: b["year"]):
    print(f"{b['year']} — «{b['title']}» ({b['author']})")
```

**4-тапсырма**

```python
import json

text = """{"city": "Астана", "unit": "°C",
 "days": [
   {"date": "2027-01-04", "min": -24, "max": -15, "desc": "аяз"},
   {"date": "2027-01-05", "min": -20, "max": -12, "desc": "қар"},
   {"date": "2027-01-06", "min": -27, "max": -18, "desc": "ашық"}
 ]}"""
data = json.loads(text)
unit = data["unit"]

print(data["city"] + ":")
for d in data["days"]:
    print(f"{d['date']}: {d['min']}..{d['max']} {unit}, {d['desc']}")

coldest = min(data["days"], key=lambda d: d["min"])
avg_max = sum(d["max"] for d in data["days"]) / len(data["days"])
print(f"Ең суық күн: {coldest['date']} ({coldest['min']} {unit})")
print(f"Орташа max: {avg_max:.1f} {unit}")
```

Нәтижесі:

```text
Астана:
2027-01-04: -24..-15 °C, аяз
2027-01-05: -20..-12 °C, қар
2027-01-06: -27..-18 °C, ашық
Ең суық күн: 2027-01-06 (-27 °C)
Орташа max: -15.0 °C
```

**5-тапсырма**

```python
import json

NEW_GAME = {
    "player": {"name": "Ойыншы", "x": 100, "y": 300, "lives": 3},
    "level": 1,
    "score": 0,
    "inventory": [],
}


def save_game(state, path="save.json"):
    """Ойын күйін JSON файлына сақтайды."""
    with open(path, "w", encoding="utf-8") as f:
        json.dump(state, f, ensure_ascii=False, indent=2)


def load_game(path="save.json"):
    """Сақталған күйді оқиды; мүмкін болмаса — жаңа ойын."""
    try:
        with open(path, encoding="utf-8") as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError):
        return json.loads(json.dumps(NEW_GAME))  # терең көшірме


state = load_game()
print("Жүктелді:", state["level"], "деңгей,", state["score"], "ұпай")
state.update({"level": 3, "score": 1450, "inventory": ["кілт", "қалқан"]})
state["player"].update({"name": "Әлия", "x": 120, "lives": 2})
save_game(state)
print("Қайта жүктелді:", load_game()["inventory"])
```

Нәтижесі (бірінші іске қосуда):

```text
Жүктелді: 1 деңгей, 0 ұпай
Қайта жүктелді: ['кілт', 'қалқан']
```

`json.loads(json.dumps(...))` — `NEW_GAME` сөздігін кездейсоқ өзгертіп алмау үшін терең көшірме (немесе `copy.deepcopy`).

**6-тапсырма**

```python
import csv
import json

SUBJECTS = ["math", "informatics", "physics"]
rows = [
    ["name", "class", "math", "informatics", "physics"],
    ["Айгерім Нұрланқызы", "9А", 5, 5, 4],
    ["Дәулет Оспанов", "9Ә", 4, 5, 5],
]
with open("journal.csv", "w", encoding="utf-8", newline="") as f:
    csv.writer(f).writerows(rows)  # тексеру үшін шағын файл

result = {}
with open("journal.csv", encoding="utf-8", newline="") as f:
    for row in csv.DictReader(f):
        student = {"name": row["name"], "marks": {s: int(row[s]) for s in SUBJECTS}}
        result.setdefault(row["class"], []).append(student)

with open("journal.json", "w", encoding="utf-8") as f:
    json.dump(result, f, ensure_ascii=False, indent=2)
print(json.dumps(result, ensure_ascii=False))
```

Нәтижесі:

```text
{"9А": [{"name": "Айгерім Нұрланқызы", "marks": {"math": 5, "informatics": 5, "physics": 4}}], "9Ә": [{"name": "Дәулет Оспанов", "marks": {"math": 4, "informatics": 5, "physics": 5}}]}
```

**7-тапсырма**

```python
import json


class Contact:
    """Телефон кітапшасының контактісі."""

    def __init__(self, name, phone, tags=None):
        self.name = name
        self.phone = phone
        self.tags = set(tags or [])

    def to_dict(self):
        # set JSON-ға сыймайды → сұрыпталған тізімге айналдырамыз
        return {"name": self.name, "phone": self.phone, "tags": sorted(self.tags)}

    @classmethod
    def from_dict(cls, data):
        return cls(data["name"], data["phone"], data.get("tags", []))

    def __str__(self):
        return f"{self.name}: {self.phone} [{', '.join(sorted(self.tags))}]"


contacts = [
    Contact("Анам", "+7 701 111 22 33", {"отбасы"}),
    Contact("Ерлан", "+7 747 555 66 77", {"сынып", "футбол"}),
]
with open("contacts.json", "w", encoding="utf-8") as f:
    json.dump([c.to_dict() for c in contacts], f, ensure_ascii=False, indent=2)

with open("contacts.json", encoding="utf-8") as f:
    for c in (Contact.from_dict(d) for d in json.load(f)):
        print(c)
```

Нәтижесі:

```text
Анам: +7 701 111 22 33 [отбасы]
Ерлан: +7 747 555 66 77 [сынып, футбол]
```

**8-тапсырма**

```python
def walk(value, path=""):
    """Кірістірілген құрылымның барлық «жапырақтарын» жолымен шығарады."""
    if isinstance(value, dict):
        for key, item in value.items():
            walk(item, f"{path}.{key}" if path else key)
    elif isinstance(value, list):
        for i, item in enumerate(value):
            walk(item, f"{path}[{i}]")
    else:
        print(f"{path} = {value}")


data = {"team": "Қыран", "members": [{"name": "Әлия"}, {"name": "Нұрлан"}], "game": {"levels": [1, 2]}}
walk(data)
```

Нәтижесі:

```text
team = Қыран
members[0].name = Әлия
members[1].name = Нұрлан
game.levels[0] = 1
game.levels[1] = 2
```

**9-тапсырма (үлгі)**

```python
import json
import os
from datetime import date

QUIZ = [
    {"q": "Қазақ хандығы қай жылы құрылды?", "options": ["1465", "1731", "1991"], "answer": 0,
     "why": "1465 жылы Керей мен Жәнібек сұлтандар негізін қалады."},
    {"q": "Python-да тізімге элемент қосатын әдіс?", "options": ["add", "append", "push"], "answer": 1,
     "why": "list.append(x) — тізімнің соңына қосады."},
]

if not os.path.exists("quiz.json"):
    with open("quiz.json", "w", encoding="utf-8") as f:
        json.dump(QUIZ, f, ensure_ascii=False, indent=2)

with open("quiz.json", encoding="utf-8") as f:
    questions = json.load(f)

name = input("Атың: ")
score = 0
for item in questions:
    print("\n" + item["q"])
    for i, option in enumerate(item["options"], start=1):
        print(f"  {i}) {option}")
    choice = int(input("Жауап нөмірі: ")) - 1
    if choice == item["answer"]:
        score += 1
        print("Дұрыс!")
    else:
        print("Қате.", item["why"])

results = []
if os.path.exists("results.json"):
    with open("results.json", encoding="utf-8") as f:
        results = json.load(f)
results.append({"name": name, "score": score, "total": len(questions), "date": date.today().isoformat()})
with open("results.json", "w", encoding="utf-8") as f:
    json.dump(results, f, ensure_ascii=False, indent=2)
print(f"\n{name}, нәтижең: {score}/{len(questions)}")
```

</details>
