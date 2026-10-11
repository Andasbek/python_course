# Сабақ 51. JSON форматы. json модулі: сериялау және десериялау.

## Тақырыптар

1. JSON дегеніміз не?
2. Сериялау және десериялау
3. JSON файлдары: `dump` және `load`
4. Кірістірілген құрылымдармен жұмыс
5. Қателерді өңдеу
6. JSON-ға сыймайтын типтер
7. Объектілерді (класс даналарын) сериялау
8. JSON, CSV және pickle салыстыру
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. JSON дегеніміз не?

**JSON (JavaScript Object Notation)** — деректерді мәтін түрінде сақтау және алмасу форматы. Ол JavaScript тілінен шыққан, бірақ қазір барлық тілдерде қолданылады. Веб-API-лардың басым көпшілігі деректерді JSON түрінде береді.

```json
{
  "name": "Айдана",
  "age": 14,
  "city": "Алматы",
  "is_member": true,
  "clubs": ["Python", "Робототехника"],
  "scores": {"python": 98, "robotics": 91},
  "mentor": null
}
```

Бұл Python сөздігіне өте ұқсас! Бірақ бірнеше айырмашылық бар.

### JSON ережелері

1. **Объект** `{...}` — «кілт: мән» жұптары; кілт — **тек жол**, **тек қос тырнақшада**.
2. **Массив** `[...]` — мәндер тізімі.
3. Жолдар — **тек қос тырнақша** `"..."` (жалқы `'...'` — қате).
4. Логикалық мәндер — `true` / `false` (кіші әріппен), бос мән — `null`.
5. Соңғы элементтен кейін үтір қоюға **болмайды**: `[1, 2, 3,]` — қате.
6. Түсініктеме (`#`, `//`) жазуға **болмайды**.

### Типтердің сәйкестігі

| JSON | Python |
|---|---|
| object `{}` | `dict` |
| array `[]` | `list` (кортеж `tuple` те массивке айналады) |
| string `"..."` | `str` |
| number (бүтін) | `int` |
| number (бөлшек) | `float` |
| `true` / `false` | `True` / `False` |
| `null` | `None` |

JSON-да **жоқ** типтер: `set`, `datetime`, `bytes`, өз класыңның объектісі. Оларды сақтау үшін алдымен JSON типтеріне айналдыру керек (төменде).

## 2. Сериялау және десериялау

- **Сериялау (serialization)** — Python объектісін JSON мәтініне (немесе файлға) айналдыру.
- **Десериялау (deserialization)** — JSON мәтінінен Python объектісін қалпына келтіру.

**Аналогия:** жиһазды (сөздік) пошта арқылы жіберу үшін оны бөлшектеп, қорапқа саламыз (сериялау). Алушы қорапты ашып, жиһазды қайта құрастырады (десериялау). Қорап ішіндегі нұсқаулық — JSON форматы.

```text
Python объектісі  --dumps / dump-->  JSON мәтіні / файл
Python объектісі  <--loads / load--  JSON мәтіні / файл
```

| Функция | Не істейді | Еске сақтау |
|---|---|---|
| `json.dumps(obj)` | объект → **жол** | **s** = string |
| `json.loads(text)` | **жол** → объект | **s** = string |
| `json.dump(obj, f)` | объект → **файл** | |
| `json.load(f)` | **файл** → объект | |

### `dumps` және `loads` — жолмен жұмыс

```python
import json

student = {
    "name": "Айдана",
    "age": 14,
    "is_member": True,
    "clubs": ["Python", "Робототехника"],
    "mentor": None,
}

text = json.dumps(student)
print(type(text))
print(text)

back = json.loads(text)
print(type(back))
print(back["clubs"][0])
```

Нәтижесі:

```text
<class 'str'>
{"name": "Айдана", "age": 14, "is_member": true, "clubs": ["Python", "Робототехника"], "mentor": null}
<class 'dict'>
Python
```

Байқа: `True` → `true`, `None` → `null`. Ал кириллица `А...` деп «қашып» жазылды — бұл қате емес, бірақ оқуға ыңғайсыз.

### `ensure_ascii=False` және `indent`

```python
import json

student = {"name": "Айдана", "clubs": ["Python", "Домбыра"], "scores": {"python": 98}}
print(json.dumps(student, ensure_ascii=False, indent=2))
```

Нәтижесі:

```text
{
  "name": "Айдана",
  "clubs": [
    "Python",
    "Домбыра"
  ],
  "scores": {
    "python": 98
  }
}
```

| Параметр | Мағынасы |
|---|---|
| `ensure_ascii=False` | қазақ/орыс әріптерін сол күйінде жазу |
| `indent=2` (немесе 4) | шегініспен «әдемі» жазу (адамға оқуға ыңғайлы) |
| `sort_keys=True` | кілттерді әліпби бойынша сұрыптау |
| `separators=(",", ":")` | бос орынсыз, ең ықшам түрде жазу (желі арқылы жіберуге) |

## 3. JSON файлдары: `dump` және `load`

```python
import json

library = {
    "school": "№ 25 мектеп-лицей",
    "books": [
        {"title": "Абай жолы", "author": "Мұхтар Әуезов", "year": 1942, "available": True},
        {"title": "Көшпенділер", "author": "Ілияс Есенберлин", "year": 1976, "available": False},
        {"title": "Менің атым Қожа", "author": "Бердібек Соқпақбаев", "year": 1957, "available": True},
    ],
}

# Сериялау: файлға жазу
with open("library.json", "w", encoding="utf-8") as f:
    json.dump(library, f, ensure_ascii=False, indent=2)

# Десериялау: файлдан оқу
with open("library.json", encoding="utf-8") as f:
    data = json.load(f)

print(data["school"])
for book in data["books"]:
    status = "бар" if book["available"] else "берілген"
    print(f"- «{book['title']}» ({book['author']}, {book['year']}) — {status}")
```

Нәтижесі:

```text
№ 25 мектеп-лицей
- «Абай жолы» (Мұхтар Әуезов, 1942) — бар
- «Көшпенділер» (Ілияс Есенберлин, 1976) — берілген
- «Менің атым Қожа» (Бердібек Соқпақбаев, 1957) — бар
```

`library.json` файлының ішін VS Code-та ашсаң, ол оқуға ыңғайлы, шегіністермен жазылған мәтін болады.

## 4. Кірістірілген құрылымдармен жұмыс

JSON-дағы деректерге «жол» бойынша жетеміз: `data["books"][0]["title"]` — «books тізімінің 0-элементінің title кілті».

```python
import json

text = """
{
  "team": "Қыран",
  "members": [
    {"name": "Әлия", "role": "тимлид", "commits": 9},
    {"name": "Нұрлан", "role": "код", "commits": 14},
    {"name": "Дана", "role": "графика", "commits": 6}
  ],
  "game": {"title": "Қыран", "version": "1.0", "levels": [1, 2, 3]}
}
"""
data = json.loads(text)

print("Ойын:", data["game"]["title"], "v" + data["game"]["version"])
print("Деңгейлер саны:", len(data["game"]["levels"]))
total = sum(m["commits"] for m in data["members"])
top = max(data["members"], key=lambda m: m["commits"])
print("Барлық коммит:", total)
print("Ең белсенді:", top["name"])

# Өзгерту және жаңа элемент қосу
data["members"].append({"name": "Ерасыл", "role": "дыбыс", "commits": 5})
data["game"]["version"] = "1.1"
print(json.dumps(data["game"], ensure_ascii=False))
```

Нәтижесі:

```text
Ойын: Қыран v1.0
Деңгейлер саны: 3
Барлық коммит: 29
Ең белсенді: Нұрлан
{"title": "Қыран", "version": "1.1", "levels": [1, 2, 3]}
```

Кілт жоқ болуы мүмкін болса, `data.get("key", әдепкі)` қолданамыз — `KeyError` болмайды.

## 5. Қателерді өңдеу

Қате JSON мәтінін оқығанда `json.JSONDecodeError` шығады:

```python
import json

bad_texts = [
    "{'name': 'Әлия'}",          # жалқы тырнақша
    '{"scores": [1, 2, 3,]}',    # соңғы үтір
    '{"ok": True}',              # True емес, true болу керек
    '{"ok": true}',              # дұрыс
]

for text in bad_texts:
    try:
        print("Дұрыс:", json.loads(text))
    except json.JSONDecodeError as e:
        print(f"Қате: {e.msg} (позиция {e.pos})")
```

Нәтижесі:

```text
Қате: Expecting property name enclosed in double quotes (позиция 1)
Қате: Illegal trailing comma before end of array (позиция 19)
Қате: Expecting value (позиция 7)
Дұрыс: {'ok': True}
```

(Python 3.12 және одан ескі нұсқаларда екінші жолдағы хабарлама `Expecting value` деп шығады — қатенің мәні бірдей.)

Файлды оқитын сенімді функция:

```python
import json


def load_json(path, default=None):
    """JSON файлды оқиды; файл жоқ немесе бұзылған болса default қайтарады."""
    try:
        with open(path, encoding="utf-8") as f:
            return json.load(f)
    except FileNotFoundError:
        return default
    except json.JSONDecodeError:
        print(f"Ескерту: {path} файлы бұзылған")
        return default


print(load_json("jok.json", default={"records": []}))
```

Нәтижесі:

```text
{'records': []}
```

## 6. JSON-ға сыймайтын типтер

```python
import json
from datetime import date

data = {"tags": {"python", "json"}, "today": date(2027, 1, 4)}
try:
    json.dumps(data)
except TypeError as e:
    print("Қате:", e)

# Шешім: алдымен JSON типтеріне айналдыру
safe = {"tags": sorted(data["tags"]), "today": data["today"].isoformat()}
print(json.dumps(safe))
```

Нәтижесі:

```text
Қате: Object of type set is not JSON serializable
{"tags": ["json", "python"], "today": "2027-01-04"}
```

Тағы бір ерекшелік: JSON объектісінің кілттері — **тек жолдар**. Python сөздігінің `int` кілттері жолға айналады: `{1: "a"}` → `{"1": "a"}`, қайта оқығанда да `"1"` болып қалады.

## 7. Объектілерді (класс даналарын) сериялау

Класс объектісін JSON-ға тікелей жазу мүмкін емес, сондықтан класқа `to_dict()` және `from_dict()` әдістерін қосамыз:

```python
import json


class Student:
    """Үйірме оқушысы."""

    def __init__(self, name, grade, scores=None):
        self.name = name
        self.grade = grade
        self.scores = scores or {}

    def to_dict(self):
        """Объектіні JSON-ға жарамды сөздікке айналдырады."""
        return {"name": self.name, "grade": self.grade, "scores": self.scores}

    @classmethod
    def from_dict(cls, data):
        """Сөздіктен жаңа объект жасайды."""
        return cls(data["name"], data["grade"], data.get("scores", {}))

    def __repr__(self):
        return f"Student({self.name!r}, {self.grade})"


students = [Student("Айдана", 8, {"python": 98}), Student("Темірлан", 9)]

with open("students.json", "w", encoding="utf-8") as f:
    json.dump([s.to_dict() for s in students], f, ensure_ascii=False, indent=2)

with open("students.json", encoding="utf-8") as f:
    loaded = [Student.from_dict(d) for d in json.load(f)]

print(loaded)
print(loaded[0].scores["python"])
```

Нәтижесі:

```text
[Student('Айдана', 8), Student('Темірлан', 9)]
98
```

Бұл тәсіл баптау файлдарында, ойынды сақтауда және API арқылы алынған деректермен жұмыста жиі қолданылады.

## 8. JSON, CSV және pickle салыстыру

| | CSV | JSON | pickle |
|---|---|---|---|
| Түрі | мәтін | мәтін | екілік |
| Құрылым | жазық кесте | кірістірілген (дарақ) | кез келген Python объектісі |
| Типтер | бәрі жол | str, int, float, bool, null, list, dict | барлығы |
| Excel ашады | иә | жоқ | жоқ |
| Басқа тілдер оқиды | иә | иә (әмбебап) | тек Python |
| Қауіпсіздік | қауіпсіз | қауіпсіз | бөтен файл қауіпті |
| Қолдану | журнал, есеп | баптау, API, ойын сақтауы | уақытша кэш |

## Жиі кездесетін қателер

**1. `dump` пен `dumps`-ты шатастыру.**

```python
import json

data = {"a": 1}
with open("a.json", "w", encoding="utf-8") as f:
    # json.dumps(data, f)  # Қате: dumps файл қабылдамайды (TypeError)
    json.dump(data, f)     # Дұрыс
```

**2. Кириллица `А` болып жазылады.** Дұрыс: `ensure_ascii=False` және файлды `encoding="utf-8"` арқылы ашу.

**3. JSON-ды қолмен жазғанда жалқы тырнақша немесе соңғы үтір.** Дұрыс: тек `"..."`, соңғы үтірсіз. Тексеру үшін `python -m json.tool file.json` командасы:

```bash
python -m json.tool library.json
```

**4. `json.load` бос файлды оқиды** → `JSONDecodeError: Expecting value`. Дұрыс: `try/except` немесе файлды бастапқыда `[]` / `{}` мәнімен құру.

**5. `"w"` режимінде ашып, `json.load` жасау** → файл өшіп кетеді. Оқу үшін — `"r"`, жазу үшін — `"w"`, екеуін бір уақытта жасамаймыз: алдымен оқып, өзгертіп, сосын қайта жазамыз.

**6. Кілт санын сақтау.** `{1: "бір"}` сақталып, оқылғанда `{"1": "бір"}` болады — `data[1]` → `KeyError`. Дұрыс: `data["1"]` немесе оқығаннан кейін кілттерді `int`-ке айналдыру.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| JSON | Деректерді алмасуға арналған әмбебап мәтіндік формат |
| Объект / массив | JSON-дағы `{}` (сөздік) және `[]` (тізім) |
| Сериялау | Объектіні мәтінге/файлға айналдыру (`dump`, `dumps`) |
| Десериялау | Мәтіннен/файлдан объектіні қалпына келтіру (`load`, `loads`) |
| `ensure_ascii=False` | Ұлттық әріптерді сол күйінде сақтау |
| `indent` | Шегініспен әдемі жазу |
| `JSONDecodeError` | Қате JSON мәтінін оқығандағы қате |
| `to_dict` / `from_dict` | Объектіні сөздікке айналдыру және керісінше |

## Ескерту

- `json.load()` жаңа объектілерді қайтарады: оқылған сөздікті өзгерту файлды өзгертпейді — өзгерістерді сақтау үшін файлды `json.dump()` арқылы қайта жазу керек.
- `json` модулі әдепкі бойынша `NaN` және `Infinity` мәндерін өткізеді (`float("nan")` → `NaN`), бірақ JSON стандарты оларға рұқсат бермейді; қатаң режим — `json.dumps(obj, allow_nan=False)`.
- Бөлшек ақша сомалары үшін `float` қателіктерінен аулақ болу мақсатында `json.loads(text, parse_float=decimal.Decimal)` қолданыңыз.
- Стандартты емес типтерді `default` параметрі арқылы сериялауға болады: `json.dumps(data, default=str)` `date`, `Decimal` және т.б. жолға айналдырады; `dataclass` объектілері үшін `dataclasses.asdict(obj)` ыңғайлы.
- Жазу кезіндегі ақаудан файл бүлінбеуі үшін алдымен уақытша файлға жазып, содан кейін түпнұсқаны соны мен алмастырады: `os.replace("data.tmp", "data.json")` — бұл амал атомарлы.
- Құжаттама: https://docs.python.org/3/library/json.html
