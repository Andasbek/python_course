# Урок 49. Практические задания: текстовые и двоичные файлы

Все задания выполняй в отдельной папке (`sabaq49/`). Для текстовых файлов всегда `encoding="utf-8"`.

## Базовый уровень

### Задание 1. Дневник

Программа запрашивает у пользователя одну запись и добавляет её **в конец** файла `diary.txt` вместе с сегодняшней датой. Затем выводит все записи из файла с нумерацией.

Подсказка: дату получаешь через `datetime.date.today()`.

```text
Ввод:
Твоя запись на сегодня: На кружке Python повторили файлы
Вывод:
1. 2026-12-28 | Защитили проект на Pygame
2. 2026-12-29 | На кружке Python повторили файлы
```

### Задание 2. Файл оценок

В файле `grades.txt` в каждой строке через пробел записаны имя ученика и его оценка:

```text
Айгерим 95
Бауыржан 78
Даулет 88
Сымбат 100
Ерлан 64
```

Программа читает файл и выводит: средний балл, ученика с наивысшим баллом и список тех, кто получил ниже 80. Результат также записывается в файл `report.txt`.

```text
Вывод:
Средний балл: 85.0
Лучший: Сымбат (100)
Ниже 80: Бауыржан, Ерлан
```

### Задание 3. Статистика текста

Запроси имя любого `.txt`-файла (например, одного из стихотворений Абая) и выведи: количество строк, слов, символов и размер файла в байтах. Объясни, почему количество символов и количество байтов различаются. Если файл не найден — понятное сообщение.

```text
Ввод:
Имя файла: abai.txt
Вывод:
Строк: 4
Слов: 20
Символов: 128
Размер: 236 байт
```

### Задание 4. Исследование `str` → `bytes`

Пользователь вводит одно слово. Программа для каждого символа слова выводит его байты UTF-8 в шестнадцатеричной системе.

```text
Ввод:
Слово: Ақ
Вывод:
'А' -> d0 90 (2 байт)
'қ' -> d2 9b (2 байт)
Всего: 4 байт
```

Подсказка: `ch.encode("utf-8").hex(" ")`.

### Задание 5. Копирование изображения

Скопируй любое изображение `.png` в `copy.png` с помощью `"rb"`/`"wb"`. Открой копию и проверь, что изображение не повреждено. Сравни размеры двух файлов.

## Продвинутый уровень

### Задание 6. Определитель формата файла

Напиши программу, которая просматривает **все** файлы в текущей папке и для каждого выводит имя, размер и настоящий формат по сигнатуре. Файлы, у которых расширение не совпадает с настоящим форматом, пометь как «⚠ Подозрительный».

```text
Вывод:
cat.png        15320 байт  Изображение PNG
photo.jpg      48211 байт  Изображение JPEG
secret.jpg       742 байт  Изображение PNG   ⚠ Подозрительный
notes.txt        120 байт  неизвестный формат
```

### Задание 7. Простое шифрование (XOR)

Прочитай любой файл (текст или изображение) в двоичном режиме, измени каждый байт операцией XOR (`^`) с числом-ключом и запиши в файл `.enc`. Если применить ту же программу к файлу `.enc`, исходный файл должен восстановиться (свойство XOR: `(a ^ k) ^ k == a`).

### Задание 8. Таблица рекордов игры

Для нашей игры на Pygame рекорды хранятся в файле `scores.txt` в формате «имя;очки». Напиши функции:
- `add_score(name, points)` — добавляет новый результат;
- `top_scores(n=5)` — возвращает `n` лучших результатов в порядке убывания.
Если файла нет, программа не должна падать.

## ★ Олимпиадная / творческая задача

### Задание 9. Чтение размера PNG-изображения «вручную»

В PNG-файле после сигнатуры идёт блок IHDR: байты 16–19 — **ширина** изображения, байты 20–23 — **высота** (4-байтовое целое число в порядке big-endian). Без библиотек (только `open(..., "rb")` и `int.from_bytes`) напиши программу, которая выводит размер любого PNG-изображения.

```text
Ввод:
Файл: cat.png
Вывод:
cat.png: 640 × 480 пикселей
```

### Задание 10. Hex-просмотрщик

Выведи первые 64 байта любого файла в виде «hex dump»: в каждой строке 16 байтов — слева смещение (offset), в середине шестнадцатеричные значения, справа символы ASCII (вместо непечатаемых символов — `.`).

```text
00000000  89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52  .PNG........IHDR
```

## Домашнее задание

1. Доработай задание 2: добавь возможность добавлять нового ученика в файл оценок (режим `"a"`) и пропускать строки, не соответствующие формату «Айгерим 95».
2. Выведи первые 8 байтов 5 разных файлов на своём компьютере (изображение, PDF, docx, mp3, txt) и запиши их в таблицу.
3. Подумай: почему хранить классный журнал (имя, предметы, оценки) в текстовом файле неудобно? На следующем уроке познакомимся с форматом CSV.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**

```python
import datetime

entry = input("Твоя запись на сегодня: ").strip()
today = datetime.date.today().isoformat()

with open("diary.txt", "a", encoding="utf-8") as f:
    f.write(f"{today} | {entry}\n")

with open("diary.txt", encoding="utf-8") as f:
    for number, line in enumerate(f, start=1):
        print(f"{number}. {line.strip()}")
```

**Задание 2**

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
    f"Средний балл: {average:.1f}",
    f"Лучший: {best} ({grades[best]})",
    "Ниже 80: " + ", ".join(low),
]
with open("report.txt", "w", encoding="utf-8") as f:
    f.write("\n".join(lines) + "\n")
print("\n".join(lines))
```

Результат (для данного файла):

```text
Средний балл: 85.0
Лучший: Сымбат (100)
Ниже 80: Бауыржан, Ерлан
```

**Задание 3**

```python
from pathlib import Path

name = input("Имя файла: ")
path = Path(name)
try:
    text = path.read_text(encoding="utf-8")
except FileNotFoundError:
    print("Такого файла нет")
except UnicodeDecodeError:
    print("Это не текстовый файл")
else:
    print("Строк:", len(text.splitlines()))
    print("Слов:", len(text.split()))
    print("Символов:", len(text))
    print("Размер:", path.stat().st_size, "байт")
```

Объяснение: буквы кириллицы в UTF-8 занимают по 2 байта, поэтому байтов больше, чем символов.

**Задание 4**

```python
word = input("Слово: ")
total = 0
for ch in word:
    b = ch.encode("utf-8")
    total += len(b)
    print(f"'{ch}' -> {b.hex(' ')} ({len(b)} байт)")
print("Всего:", total, "байт")
```

**Задание 5**

```python
import os

with open("cat.png", "rb") as fin:
    data = fin.read()
with open("copy.png", "wb") as fout:
    fout.write(data)

print(os.path.getsize("cat.png"), os.path.getsize("copy.png"))
```

**Задание 6**

```python
from pathlib import Path

SIGNATURES = {
    b"\x89PNG": ("Изображение PNG", {".png"}),
    b"\xff\xd8\xff": ("Изображение JPEG", {".jpg", ".jpeg"}),
    b"GIF8": ("Анимация GIF", {".gif"}),
    b"PK\x03\x04": ("Архив ZIP", {".zip", ".docx", ".xlsx", ".pptx"}),
    b"%PDF": ("Документ PDF", {".pdf"}),
}

for path in sorted(Path(".").iterdir()):
    if not path.is_file():
        continue
    with open(path, "rb") as f:
        head = f.read(8)
    kind, warn = "неизвестный формат", ""
    for sign, (name, extensions) in SIGNATURES.items():
        if head.startswith(sign):
            kind = name
            if path.suffix.lower() not in extensions:
                warn = "⚠ Подозрительный"
            break
    print(f"{path.name:<14}{path.stat().st_size:>8} байт  {kind:<14}{warn}")
```

**Задание 7**

```python
def xor_file(src, dst, key=77):
    """Применяет XOR с key к каждому байту. Если применить дважды — исходный файл."""
    with open(src, "rb") as f:
        data = f.read()
    result = bytes(b ^ key for b in data)
    with open(dst, "wb") as f:
        f.write(result)


with open("secret.txt", "w", encoding="utf-8") as f:
    f.write("Секретное сообщение: встречаемся завтра в 15:00")

xor_file("secret.txt", "secret.enc")
xor_file("secret.enc", "secret_back.txt")
with open("secret_back.txt", encoding="utf-8") as f:
    print(f.read())
```

Результат:

```text
Секретное сообщение: встречаемся завтра в 15:00
```

Примечание для учеников: XOR — учебный «шифр», для настоящей секретности его использовать нельзя.

**Задание 8**

```python
import os

SCORES_FILE = "scores.txt"


def add_score(name, points):
    """Добавляет результат в конец файла."""
    with open(SCORES_FILE, "a", encoding="utf-8") as f:
        f.write(f"{name};{points}\n")


def top_scores(n=5):
    """Возвращает n лучших результатов в виде [(имя, очки), ...]."""
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


for name, pts in [("Алия", 340), ("Нурлан", 520), ("Дана", 410)]:
    add_score(name, pts)
for place, (name, pts) in enumerate(top_scores(3), start=1):
    print(f"{place}. {name} — {pts}")
```

Результат (если файл был пуст):

```text
1. Нурлан — 520
2. Дана — 410
3. Алия — 340
```

**Задание 9**

```python
name = input("Файл: ")
with open(name, "rb") as f:
    head = f.read(24)

if not head.startswith(b"\x89PNG\r\n\x1a\n"):
    print("Это не PNG-файл")
else:
    width = int.from_bytes(head[16:20], "big")
    height = int.from_bytes(head[20:24], "big")
    print(f"{name}: {width} × {height} пикселей")
```

**Задание 10**

```python
def hex_dump(path, limit=64):
    """Выводит первые limit байтов файла в виде hex dump."""
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

Результат:

```text
00000000  89 50 4e 47 0d 0a 1a 0a d0 a1 d3 99 d0 bb d0 b5  .PNG............
00000010  d0 bc 2c 20 50 79 74 68 6f 6e 21                 .., Python!
```

</details>
