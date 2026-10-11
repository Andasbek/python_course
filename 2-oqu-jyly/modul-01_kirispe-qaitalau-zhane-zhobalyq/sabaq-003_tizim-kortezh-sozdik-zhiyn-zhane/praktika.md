# Сабақ 3. Практикалық тапсырмалар: мәліметтер құрылымдары, функциялар, кодты құрылымдау

## Базалық деңгей

### 1-тапсырма. «Сатып алу тізімі»
Бос тізім құр. Пайдаланушы «стоп» деп жазғанша тауар атауларын енгізеді. Бағдарлама:
- қайталанған тауарды екінші рет қоспайды («... тізімде бар» деп хабарлайды);
- соңында тізімді нөмірлеп, алфавит бойынша шығарады.

```text
Тауар: нан
Тауар: сүт
Тауар: нан
нан тізімде бар
Тауар: стоп
1. нан
2. сүт
```

### 2-тапсырма. «Қалалар мен халық»
Сөздік құр: Қазақстанның 5 қаласы және халық саны (мың адам): Алматы — 2230, Астана — 1420, Шымкент — 1220, Қарағанды — 500, Ақтөбе — 590. Бағдарлама:
1. қала атауын сұрап, халқын шығарады (жоқ болса — «Деректер жоқ», `get` арқылы);
2. халқы 1 миллионнан асатын қалаларды шығарады;
3. барлық қалалардың жалпы халқын есептейді.

### 3-тапсырма. «Ортақ үйірмелер»
Екі жиын бер: шахмат үйірмесінің және Python үйірмесінің оқушылары. Шығар:
- екі үйірмеге де қатысатындар;
- тек біреуіне ғана қатысатындар (`^` симметриялық айырма);
- жалпы бірегей оқушылар саны.

### 4-тапсырма. «Функциялар жинағы»
Үш функция жаз және `main()` ішінде тексер:
- `is_even(n)` — сан жұп болса `True`;
- `min_max(numbers)` — тізімнің ең кіші және ең үлкен элементін кортеж ретінде қайтарады;
- `celsius_to_fahrenheit(c)` — `F = C * 9 / 5 + 32`.

```text
is_even(10) → True
min_max([4, 9, 1, 7]) → (1, 9)
celsius_to_fahrenheit(25) → 77.0
```

## Кеңейтілген деңгей

### 5-тапсырма. «Сынып журналы» (кодты құрылымдау)
Төмендегі құрылымдалмаған кодты функцияларға бөліп қайта жаз: `add_grade`, `student_average`, `class_average`, `print_journal`, `main`. Есептеу функцияларында `print` болмауы керек.

```python
journal = {}
journal["Әлия"] = [5, 4]
journal["Бекзат"] = [3, 4, 4]
journal["Әлия"].append(5)
for name in journal:
    s = 0
    for g in journal[name]:
        s += g
    print(name, round(s / len(journal[name]), 2))
total = 0
count = 0
for name in journal:
    for g in journal[name]:
        total += g
        count += 1
print("Сынып бойынша:", round(total / count, 2))
```

Күтілетін нәтиже:
```text
Әлия        4.67
Бекзат      3.67
Сынып бойынша: 4.17
```

### 6-тапсырма. «Модульге бөлу»
5-тапсырманың функцияларын `journal_utils.py` файлына көшір, ал `main.py` файлында оларды `import` арқылы қолдан. `journal_utils.py` файлын тікелей іске қосқанда шағын тест орындалсын, ал импорт кезінде — орындалмасын (`if __name__ == "__main__":`).

### 7-тапсырма. «Сөздер жиілігі»
Мәтінді енгіз. `word_frequency(text)` функциясы сөздерді кіші әріпке келтіріп, тыныс белгілерін алып тастап, жиілік сөздігін қайтарсын. `top_words(freq, n)` функциясы ең жиі кездесетін `n` сөзді `(сөз, саны)` кортеждерінің тізімі ретінде қайтарсын.

```text
Мәтін: Білім — бақыт. Білім алу — бақытқа жол, білім — күш!
білім: 3
бақыт: 1
алу: 1
```

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. «Анаграммалар тобы»
Сөздер тізімі берілген. Бір-біріне анаграмма болатын сөздерді топтарға біріктір (бір әріптерден тұратын сөздер). Кеңес: сөздіктің кілті ретінде сұрыпталған әріптердің кортежін не жолын қолдан.

```text
Кіріс: ["асыл", "сыла", "ақын", "қаны", "ылас", "мектеп"]
Шығыс:
асыл, сыла, ылас
ақын, қаны
мектеп
```

### 9-тапсырма. «Мини-қойма»
Дүкен қоймасының бағдарламасы: сөздік `{тауар: [саны, бағасы]}`. Мәзір: 1 — тауар қосу, 2 — сату (саны азаяды, жеткіліксіз болса ескерту), 3 — қойма тізімі, 4 — қойманың жалпы құны, 0 — шығу. Әр мәзір пункті — бөлек функция.

## Үй тапсырмасы

1. 9-тапсырманы аяқта (немесе бастаған тапсырманы жетілдір) — бағдарлама кемінде 4 функциядан және `main()`-нен тұрсын.
2. Келесі сабаққа: үйдегі компьютерге VS Code орнатуға тырыс (code.visualstudio.com) және Python нұсқасын тексер (`python --version`).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
items = []
while True:
    item = input("Тауар: ").strip().lower()
    if item == "стоп":
        break
    if item in items:
        print(item, "тізімде бар")
    else:
        items.append(item)

for i, item in enumerate(sorted(items), start=1):
    print(f"{i}. {item}")
```

**2-тапсырма**
```python
population = {
    "Алматы": 2230,
    "Астана": 1420,
    "Шымкент": 1220,
    "Қарағанды": 500,
    "Ақтөбе": 590,
}

city = input("Қала: ").strip()
print(population.get(city, "Деректер жоқ"))

print("Миллионнан асатын қалалар:")
for name, people in population.items():
    if people > 1000:
        print("-", name)

print("Жалпы халқы:", sum(population.values()), "мың адам")
```

**3-тапсырма**
```python
chess = {"Айдар", "Мадина", "Тимур", "Әсел"}
python = {"Мадина", "Ерлан", "Тимур", "Дана"}

print("Екеуіне де:", chess & python)
print("Тек біреуіне:", chess ^ python)
print("Жалпы оқушылар:", len(chess | python))
```

**4-тапсырма**
```python
def is_even(n):
    """Сан жұп болса True қайтарады."""
    return n % 2 == 0


def min_max(numbers):
    """Тізімнің ең кіші және ең үлкен элементін кортеж ретінде қайтарады."""
    return min(numbers), max(numbers)


def celsius_to_fahrenheit(c):
    """Цельсийді Фаренгейтке айналдырады."""
    return c * 9 / 5 + 32


def main():
    print("is_even(10) →", is_even(10))
    print("min_max([4, 9, 1, 7]) →", min_max([4, 9, 1, 7]))
    print("celsius_to_fahrenheit(25) →", celsius_to_fahrenheit(25))


if __name__ == "__main__":
    main()
```

**5-тапсырма**
```python
def add_grade(journal, name, grade):
    """Оқушыға баға қосады (оқушы жоқ болса, жасайды)."""
    journal.setdefault(name, []).append(grade)


def student_average(grades):
    """Бір оқушының орташа бағасын қайтарады."""
    return sum(grades) / len(grades) if grades else 0


def class_average(journal):
    """Сыныптағы барлық бағалардың орташасын қайтарады."""
    all_grades = [g for grades in journal.values() for g in grades]
    return student_average(all_grades)


def print_journal(journal):
    """Журналды кесте түрінде шығарады."""
    for name, grades in journal.items():
        print(f"{name:<12}{student_average(grades):.2f}")
    print("Сынып бойынша:", round(class_average(journal), 2))


def main():
    journal = {}
    add_grade(journal, "Әлия", 5)
    add_grade(journal, "Әлия", 4)
    add_grade(journal, "Бекзат", 3)
    add_grade(journal, "Бекзат", 4)
    add_grade(journal, "Бекзат", 4)
    add_grade(journal, "Әлия", 5)
    print_journal(journal)


if __name__ == "__main__":
    main()
```

**6-тапсырма** — `journal_utils.py` ішінде 5-тапсырманың функциялары және соңында:
```python
if __name__ == "__main__":
    test = {"А": [5, 5], "Б": [3]}
    print(class_average(test))  # 4.333...
```
`main.py`:
```python
from journal_utils import add_grade, print_journal


def main():
    journal = {}
    for name, grade in [("Әлия", 5), ("Әлия", 4), ("Бекзат", 3)]:
        add_grade(journal, name, grade)
    print_journal(journal)


if __name__ == "__main__":
    main()
```

**7-тапсырма**
```python
PUNCTUATION = ".,!?;:—-«»\"'()"


def word_frequency(text):
    """Сөздердің жиілік сөздігін қайтарады."""
    freq = {}
    for word in text.lower().split():
        word = word.strip(PUNCTUATION)
        if word:
            freq[word] = freq.get(word, 0) + 1
    return freq


def top_words(freq, n):
    """Ең жиі n сөзді (сөз, саны) кортеждері ретінде қайтарады."""
    return sorted(freq.items(), key=lambda pair: pair[1], reverse=True)[:n]


def main():
    text = input("Мәтін: ")
    for word, count in top_words(word_frequency(text), 3):
        print(f"{word}: {count}")


if __name__ == "__main__":
    main()
```

**8-тапсырма**
```python
def group_anagrams(words):
    """Анаграммаларды топтарға біріктіреді."""
    groups = {}
    for word in words:
        key = "".join(sorted(word))
        groups.setdefault(key, []).append(word)
    return list(groups.values())


words = ["асыл", "сыла", "ақын", "қаны", "ылас", "мектеп"]
for group in group_anagrams(words):
    print(", ".join(group))
```

**9-тапсырма**
```python
def add_item(store):
    name = input("Тауар: ").strip()
    count = int(input("Саны: "))
    price = int(input("Бағасы: "))
    if name in store:
        store[name][0] += count
        store[name][1] = price
    else:
        store[name] = [count, price]


def sell_item(store):
    name = input("Тауар: ").strip()
    if name not in store:
        print("Мұндай тауар жоқ")
        return
    count = int(input("Саны: "))
    if count > store[name][0]:
        print("Қоймада жеткіліксіз! Бар:", store[name][0])
    else:
        store[name][0] -= count
        print("Сатылды:", count * store[name][1], "₸")


def show_store(store):
    for name, (count, price) in store.items():
        print(f"{name:<15}{count:>5} дана {price:>8} ₸")


def store_value(store):
    return sum(count * price for count, price in store.values())


def main():
    store = {"дәптер": [100, 150], "қалам": [200, 120]}
    while True:
        choice = input("1-қосу 2-сату 3-тізім 4-құны 0-шығу: ")
        if choice == "1":
            add_item(store)
        elif choice == "2":
            sell_item(store)
        elif choice == "3":
            show_store(store)
        elif choice == "4":
            print("Қойма құны:", store_value(store), "₸")
        elif choice == "0":
            break


if __name__ == "__main__":
    main()
```

</details>
