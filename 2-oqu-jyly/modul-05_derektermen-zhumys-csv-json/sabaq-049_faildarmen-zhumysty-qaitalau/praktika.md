# Сабақ 49. Практикалық тапсырмалар: мәтіндік және екілік файлдар

Барлық тапсырмаларды бөлек папкада (`sabaq49/`) орында. Мәтіндік файлдарда әрқашан `encoding="utf-8"`.

## Базалық деңгей

### 1-тапсырма. Күнделік

Бағдарлама пайдаланушыдан бір жазба сұрап, оны `diary.txt` файлының **соңына** бүгінгі күнмен бірге қосады. Содан кейін файлдағы барлық жазбаларды нөмірлеп шығарады.

Кеңес: күнді `datetime.date.today()` арқылы аласың.

```text
Енгізу:
Бүгінгі жазбаң: Python үйірмесінде файлдарды қайталадық
Шығару:
1. 2026-12-28 | Pygame жобасын қорғадық
2. 2026-12-29 | Python үйірмесінде файлдарды қайталадық
```

### 2-тапсырма. Бағалар файлы

`grades.txt` файлында әр жолда оқушының аты мен бағасы бос орын арқылы жазылған:

```text
Айгерім 95
Бауыржан 78
Дәулет 88
Сымбат 100
Ерлан 64
```

Бағдарлама файлды оқып: орташа балды, ең жоғары баллы оқушыны және 80-нен төмен алғандар тізімін шығарсын. Нәтижені `report.txt` файлына да жазсын.

```text
Шығару:
Орташа балл: 85.0
Үздік: Сымбат (100)
80-нен төмен: Бауыржан, Ерлан
```

### 3-тапсырма. Мәтін статистикасы

Кез келген `.txt` файлының (мысалы, Абайдың бір өлеңі) атын сұрап: жол санын, сөз санын, таңба санын және файлдың байттағы өлшемін шығар. Неліктен таңба саны мен байт саны әртүрлі екенін түсіндір. Файл табылмаса — түсінікті хабарлама.

```text
Енгізу:
Файл атауы: abai.txt
Шығару:
Жолдар: 4
Сөздер: 20
Таңбалар: 128
Өлшемі: 236 байт
```

### 4-тапсырма. `str` → `bytes` зерттеу

Пайдаланушы бір сөз енгізеді. Бағдарлама сөздің әр таңбасы үшін оның UTF-8 байттарын он алтылық жүйеде шығарады.

```text
Енгізу:
Сөз: Ақ
Шығару:
'А' -> d0 90 (2 байт)
'қ' -> d2 9b (2 байт)
Барлығы: 4 байт
```

Кеңес: `ch.encode("utf-8").hex(" ")`.

### 5-тапсырма. Суретті көшіру

Кез келген `.png` суретті `"rb"`/`"wb"` арқылы `copy.png` етіп көшір. Көшірмені ашып, сурет бұзылмағанын тексер. Екі файлдың өлшемін салыстыр.

## Кеңейтілген деңгей

### 6-тапсырма. Файл форматын анықтаушы

Ағымдағы папкадағы **барлық** файлдарды қарап шығып, әрқайсысының атын, өлшемін және сигнатурасы бойынша нақты форматын шығаратын бағдарлама жаз. Кеңейтімі мен нақты форматы сәйкес келмейтін файлдарды «⚠ Күдікті» деп белгіле.

```text
Шығару:
cat.png        15320 байт  PNG сурет
photo.jpg      48211 байт  JPEG сурет
secret.jpg       742 байт  PNG сурет   ⚠ Күдікті
notes.txt        120 байт  белгісіз формат
```

### 7-тапсырма. Қарапайым шифрлау (XOR)

Кез келген файлды (мәтін немесе сурет) екілік режимде оқып, әр байтты кілт-санмен XOR (`^`) амалы арқылы өзгертіп, `.enc` файлына жаз. Сол бағдарламаны `.enc` файлына қайта қолданса, бастапқы файл қалпына келуі керек (XOR-дың қасиеті: `(a ^ k) ^ k == a`).

### 8-тапсырма. Ойын рекордтарының кестесі

Pygame ойынымыз үшін `scores.txt` файлына «аты;ұпай» форматында рекордтар сақталады. Функциялар жаз:
- `add_score(name, points)` — жаңа нәтижені қосады;
- `top_scores(n=5)` — ең жақсы `n` нәтижені кему ретімен қайтарады.
Файл жоқ болса, бағдарлама құламауы керек.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. PNG суретінің өлшемін «қолмен» оқу

PNG файлында сигнатурадан кейін IHDR бөлігі тұрады: 16–19 байттар — суреттің **ені**, 20–23 байттар — **биіктігі** (big-endian 4 байттық бүтін сан). Кітапханаларсыз (тек `open(..., "rb")` және `int.from_bytes`) кез келген PNG суретінің өлшемін шығаратын бағдарлама жаз.

```text
Енгізу:
Файл: cat.png
Шығару:
cat.png: 640 × 480 пиксель
```

### 10-тапсырма. Hex-қарағыш

Кез келген файлдың алғашқы 64 байтын «hex dump» түрінде шығар: әр жолда 16 байт — сол жақта ығысу (offset), ортасында он алтылық мәндер, оң жақта ASCII таңбалар (басылмайтын таңбалар орнына `.`).

```text
00000000  89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52  .PNG........IHDR
```

## Үй тапсырмасы

1. 2-тапсырманы жетілдір: бағалар файлына жаңа оқушы қосу мүмкіндігін (`"a"` режимі) және «Айгерім 95» форматына сай келмейтін жолдарды өткізіп жіберуді қос.
2. Өз компьютеріңдегі 5 түрлі файлдың (сурет, PDF, docx, mp3, txt) алғашқы 8 байтын шығарып, кестеге жаз.
3. Ойлан: сынып журналын (аты, пәндер, бағалар) мәтіндік файлда сақтау неге қолайсыз? Келесі сабақта CSV форматымен танысамыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**

```python
import datetime

entry = input("Бүгінгі жазбаң: ").strip()
today = datetime.date.today().isoformat()

with open("diary.txt", "a", encoding="utf-8") as f:
    f.write(f"{today} | {entry}\n")

with open("diary.txt", encoding="utf-8") as f:
    for number, line in enumerate(f, start=1):
        print(f"{number}. {line.strip()}")
```

**2-тапсырма**

```python
grades = {}
with open("grades.txt", encoding="utf-8") as f:
    for line in f:
        parts = line.split()
        if len(parts) == 2 and parts[1].isdigit():
            grades[parts[0]] = int(parts[1])

average = sum(grades.values()) / len(grades)
best = max(grades, key=grades.get)
low = [name for name, g in grades.items() if g < 80]

lines = [
    f"Орташа балл: {average:.1f}",
    f"Үздік: {best} ({grades[best]})",
    "80-нен төмен: " + ", ".join(low),
]
with open("report.txt", "w", encoding="utf-8") as f:
    f.write("\n".join(lines) + "\n")
print("\n".join(lines))
```

Нәтижесі (берілген файл бойынша):

```text
Орташа балл: 85.0
Үздік: Сымбат (100)
80-нен төмен: Бауыржан, Ерлан
```

**3-тапсырма**

```python
from pathlib import Path

name = input("Файл атауы: ")
path = Path(name)
try:
    text = path.read_text(encoding="utf-8")
except FileNotFoundError:
    print("Мұндай файл жоқ")
except UnicodeDecodeError:
    print("Бұл мәтіндік файл емес")
else:
    print("Жолдар:", len(text.splitlines()))
    print("Сөздер:", len(text.split()))
    print("Таңбалар:", len(text))
    print("Өлшемі:", path.stat().st_size, "байт")
```

Түсіндірме: кириллица әріптері UTF-8-де 2 байттан алады, сондықтан байт саны таңба санынан көп.

**4-тапсырма**

```python
word = input("Сөз: ")
total = 0
for ch in word:
    b = ch.encode("utf-8")
    total += len(b)
    print(f"'{ch}' -> {b.hex(' ')} ({len(b)} байт)")
print("Барлығы:", total, "байт")
```

**5-тапсырма**

```python
import os

with open("cat.png", "rb") as fin:
    data = fin.read()
with open("copy.png", "wb") as fout:
    fout.write(data)

print(os.path.getsize("cat.png"), os.path.getsize("copy.png"))
```

**6-тапсырма**

```python
from pathlib import Path

SIGNATURES = {
    b"\x89PNG": ("PNG сурет", {".png"}),
    b"\xff\xd8\xff": ("JPEG сурет", {".jpg", ".jpeg"}),
    b"GIF8": ("GIF анимация", {".gif"}),
    b"PK\x03\x04": ("ZIP архив", {".zip", ".docx", ".xlsx", ".pptx"}),
    b"%PDF": ("PDF құжат", {".pdf"}),
}

for path in sorted(Path(".").iterdir()):
    if not path.is_file():
        continue
    with open(path, "rb") as f:
        head = f.read(8)
    kind, warn = "белгісіз формат", ""
    for sign, (name, extensions) in SIGNATURES.items():
        if head.startswith(sign):
            kind = name
            if path.suffix.lower() not in extensions:
                warn = "⚠ Күдікті"
            break
    print(f"{path.name:<14}{path.stat().st_size:>8} байт  {kind:<14}{warn}")
```

**7-тапсырма**

```python
def xor_file(src, dst, key=77):
    """Әр байтты key-мен XOR жасайды. Екі рет қолданса — бастапқы файл."""
    with open(src, "rb") as f:
        data = f.read()
    result = bytes(b ^ key for b in data)
    with open(dst, "wb") as f:
        f.write(result)


with open("secret.txt", "w", encoding="utf-8") as f:
    f.write("Құпия хабар: ертең 15:00-де кездесеміз")

xor_file("secret.txt", "secret.enc")
xor_file("secret.enc", "secret_back.txt")
with open("secret_back.txt", encoding="utf-8") as f:
    print(f.read())
```

Нәтижесі:

```text
Құпия хабар: ертең 15:00-де кездесеміз
```

Ескерту оқушыларға: XOR — оқу мақсатындағы «шифр», оны шынайы құпиялық үшін қолдануға болмайды.

**8-тапсырма**

```python
import os

SCORES_FILE = "scores.txt"


def add_score(name, points):
    """Нәтижені файлдың соңына қосады."""
    with open(SCORES_FILE, "a", encoding="utf-8") as f:
        f.write(f"{name};{points}\n")


def top_scores(n=5):
    """Ең жақсы n нәтижені [(аты, ұпай), ...] түрінде қайтарады."""
    if not os.path.exists(SCORES_FILE):
        return []
    result = []
    with open(SCORES_FILE, encoding="utf-8") as f:
        for line in f:
            parts = line.strip().split(";")
            if len(parts) == 2 and parts[1].isdigit():
                result.append((parts[0], int(parts[1])))
    result.sort(key=lambda item: item[1], reverse=True)
    return result[:n]


for name, pts in [("Әлия", 340), ("Нұрлан", 520), ("Дана", 410)]:
    add_score(name, pts)
for place, (name, pts) in enumerate(top_scores(3), start=1):
    print(f"{place}. {name} — {pts}")
```

Нәтижесі (файл бос болған жағдайда):

```text
1. Нұрлан — 520
2. Дана — 410
3. Әлия — 340
```

**9-тапсырма**

```python
name = input("Файл: ")
with open(name, "rb") as f:
    head = f.read(24)

if not head.startswith(b"\x89PNG\r\n\x1a\n"):
    print("Бұл PNG файл емес")
else:
    width = int.from_bytes(head[16:20], "big")
    height = int.from_bytes(head[20:24], "big")
    print(f"{name}: {width} × {height} пиксель")
```

**10-тапсырма**

```python
def hex_dump(path, limit=64):
    """Файлдың алғашқы limit байтын hex dump түрінде шығарады."""
    with open(path, "rb") as f:
        data = f.read(limit)
    for offset in range(0, len(data), 16):
        row = data[offset:offset + 16]
        hex_part = " ".join(f"{b:02x}" for b in row)
        text_part = "".join(chr(b) if 32 <= b < 127 else "." for b in row)
        print(f"{offset:08x}  {hex_part:<47}  {text_part}")


with open("demo.bin", "wb") as f:
    f.write(b"\x89PNG\r\n\x1a\n" + "Сәлем, Python!".encode("utf-8"))
hex_dump("demo.bin")
```

Нәтижесі:

```text
00000000  89 50 4e 47 0d 0a 1a 0a d0 a1 d3 99 d0 bb d0 b5  .PNG............
00000010  d0 bc 2c 20 50 79 74 68 6f 6e 21                 .., Python!
```

</details>
