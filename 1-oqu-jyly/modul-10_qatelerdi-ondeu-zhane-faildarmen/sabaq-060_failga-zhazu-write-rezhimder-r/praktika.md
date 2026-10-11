# Сабақ 60. Практикалық тапсырмалар: файлға жазу, режимдер

## Базалық деңгей

**1-тапсырма. Визит карточкасы.** Пайдаланушыдан аты-жөнін, жасын, қаласын және хоббиін сұрап, оларды `card.txt` файлына әр деректі жеке жолға жаз (`w` режимі). Содан кейін файлды оқып, экранға шығар.

```text
Атың: Ерасыл
Жасың: 13
Қалаң: Алматы
Хоббиің: футбол
--- card.txt мазмұны ---
Аты: Ерасыл
Жасы: 13
Қаласы: Алматы
Хоббиі: футбол
```

**2-тапсырма. Көбейту кестесі файлда.** Пайдаланушы енгізген санның 1-ден 10-ға дейінгі көбейту кестесін `table_N.txt` файлына жаз (N – сан). Мысалы, 7 енгізілсе – `table_7.txt`.

```text
7 x 1 = 7
7 x 2 = 14
...
7 x 10 = 70
```

**3-тапсырма. `w` пен `a` тәжірибесі.** Бағдарлама `log.txt` файлына «Бағдарлама іске қосылды» деп жазады. Бағдарламаны 3 рет `w` режимімен, содан кейін 3 рет `a` режимімен іске қос. Әр жолы файл мазмұнын оқып шығар. Айырмашылықты дәптеріңе жаз.

**4-тапсырма. Күнделік.** Пайдаланушы бір жазба енгізеді, бағдарлама оны `diary.txt` файлының соңына қосады (`a` режимі), содан кейін күнделіктегі барлық жазбаларды нөмірімен шығарады.

```text
Бүгінгі жазба: Python-да файлға жазуды үйрендім
--- Менің күнделігім ---
1. Ағылшыннан 5 алдым
2. Python-да файлға жазуды үйрендім
```

**5-тапсырма. Сатып алу тізімі.** Тізім: `["нан", "сүт", "жұмыртқа", "алма"]`. Оны үш тәсілмен үш файлға жаз: `write()` + цикл (`list1.txt`), `writelines()` (`list2.txt`), `print(file=f)` (`list3.txt`). Үш файл бірдей болуы керек.

## Кеңейтілген деңгей

**6-тапсырма. Жұп-тақ сұрыптағыш.** `numbers.txt` файлын құр (әр жолда бір сан, кемінде 10 сан). Бағдарлама оны оқып, жұп сандарды `even.txt`, тақ сандарды `odd.txt` файлына жазады, ал экранға әр файлда неше сан бар екенін шығарады. Санға айналмайтын жолдарды өткізіп жібер.

**7-тапсырма. Рекорд кестесі.** Бағдарлама ойыншының атын және ұпайын сұрап, `scores.txt` файлына `аты;ұпай` түрінде қосады. Содан кейін файлдағы барлық нәтижелерді оқып, ұпай бойынша кему ретімен сұрыптап, ТОП-3 ойыншыны шығарады.

```text
Атың: Дана
Ұпайың: 340
=== ТОП-3 ===
1. Дана – 340
2. Арман – 310
3. Айгерім – 250
```

**8-тапсырма. Телефон кітабы.** Мәзірі бар бағдарлама: `1 – контакт қосу`, `2 – барлығын көрсету`, `3 – іздеу`, `0 – шығу`. Контактілер `contacts.txt` файлында `аты;нөмірі` түрінде сақталады. Бағдарлама жабылып, қайта ашылса да контактілер сақталуы керек. Файл жоқ кезде бағдарлама құламауы тиіс.

## ★ Олимпиадалық / шығармашылық тапсырма

**9-тапсырма. Файлдағы сөзді ауыстыру.** Пайдаланушы файл атауын, ескі сөзді және жаңа сөзді енгізеді. Бағдарлама файлдағы барлық ескі сөзді жаңасымен ауыстырып, файлды **сол атпен** қайта сақтайды және неше ауыстыру жасалғанын хабарлайды. (Есіңде болсын: алдымен толық оқу, жабу, содан кейін `w`!) Түпнұсқаның көшірмесін `файл_атауы.bak` ретінде сақта.

**10-тапсырма. HTML-визиткa.** 1-тапсырмадағы деректерді `card.html` файлына қарапайым HTML түрінде жаз (`<h1>` – аты, `<p>` – қалғаны). Файлды браузерде ашып көр! Бұл – келесі жылғы веб-тақырыптарға алғашқы қадам.

## Үй тапсырмасы

1. «Мақсаттар тізімі» бағдарламасы: пайдаланушы осы аптаға 3 мақсатын енгізеді, бағдарлама оларды `goals.txt` файлына жазады. Бағдарламаны қайта ашқанда, алдымен бұрынғы мақсаттар шығатын болсын.
2. Кесте құрастыр: `r`, `w`, `a` режимдерінің әрқайсысы үшін «файл бар болса» және «файл жоқ болса» не болатынын жаз.
3. Келесі сабақтағы практикумға дайындал: 41 және 49-сабақтарда тізім мен сөздік бойынша не өткенімізді қайталап кел.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.**

```python
name = input("Атың: ")
age = input("Жасың: ")
city = input("Қалаң: ")
hobby = input("Хоббиің: ")
with open("card.txt", "w", encoding="utf-8") as f:
    f.write(f"Аты: {name}\n")
    f.write(f"Жасы: {age}\n")
    f.write(f"Қаласы: {city}\n")
    f.write(f"Хоббиі: {hobby}\n")
print("--- card.txt мазмұны ---")
with open("card.txt", encoding="utf-8") as f:
    print(f.read(), end="")
```

**2-тапсырма.**

```python
n = int(input("Сан: "))
file_name = f"table_{n}.txt"
with open(file_name, "w", encoding="utf-8") as f:
    for i in range(1, 11):
        print(f"{n} x {i} = {n * i}", file=f)
print(file_name, "файлы құрылды.")
```

**3-тапсырма.** `w` режимінде файлда әрқашан бір жол қалады, `a` режимінде әр іске қосқан сайын бір жол қосылады.

```python
mode = "a"   # "w" деп өзгертіп, тәжірибе жаса
with open("log.txt", mode, encoding="utf-8") as f:
    f.write("Бағдарлама іске қосылды\n")
with open("log.txt", encoding="utf-8") as f:
    print(f.read())
```

**4-тапсырма.**

```python
entry = input("Бүгінгі жазба: ")
with open("diary.txt", "a", encoding="utf-8") as f:
    f.write(entry + "\n")
print("--- Менің күнделігім ---")
with open("diary.txt", encoding="utf-8") as f:
    number = 1
    for line in f:
        print(f"{number}. {line.strip()}")
        number += 1
```

**5-тапсырма.**

```python
items = ["нан", "сүт", "жұмыртқа", "алма"]
with open("list1.txt", "w", encoding="utf-8") as f:
    for item in items:
        f.write(item + "\n")
with open("list2.txt", "w", encoding="utf-8") as f:
    f.writelines([item + "\n" for item in items])
with open("list3.txt", "w", encoding="utf-8") as f:
    for item in items:
        print(item, file=f)
```

**6-тапсырма.**

```python
evens = []
odds = []
try:
    with open("numbers.txt", encoding="utf-8") as f:
        for line in f:
            try:
                n = int(line)
            except ValueError:
                continue
            if n % 2 == 0:
                evens.append(n)
            else:
                odds.append(n)
except FileNotFoundError:
    print("numbers.txt табылмады")

with open("even.txt", "w", encoding="utf-8") as f:
    for n in evens:
        print(n, file=f)
with open("odd.txt", "w", encoding="utf-8") as f:
    for n in odds:
        print(n, file=f)
print("Жұп сандар:", len(evens), "| Тақ сандар:", len(odds))
```

**7-тапсырма.**

```python
name = input("Атың: ")
points = int(input("Ұпайың: "))
with open("scores.txt", "a", encoding="utf-8") as f:
    f.write(f"{name};{points}\n")

table = []
with open("scores.txt", encoding="utf-8") as f:
    for line in f:
        player, value = line.strip().split(";")
        table.append([int(value), player])
table.sort(reverse=True)
print("=== ТОП-3 ===")
place = 1
for value, player in table[:3]:
    print(f"{place}. {player} – {value}")
    place += 1
```

**8-тапсырма.**

```python
FILE_NAME = "contacts.txt"

def load_contacts():
    contacts = {}
    try:
        with open(FILE_NAME, encoding="utf-8") as f:
            for line in f:
                if ";" in line:
                    name, phone = line.strip().split(";", 1)
                    contacts[name] = phone
    except FileNotFoundError:
        pass
    return contacts

def save_contacts(contacts):
    with open(FILE_NAME, "w", encoding="utf-8") as f:
        for name, phone in contacts.items():
            f.write(f"{name};{phone}\n")

contacts = load_contacts()
while True:
    choice = input("1-қосу, 2-барлығы, 3-іздеу, 0-шығу: ")
    if choice == "1":
        name = input("Аты: ")
        phone = input("Нөмірі: ")
        contacts[name] = phone
        save_contacts(contacts)
        print("Сақталды.")
    elif choice == "2":
        if not contacts:
            print("Кітап бос.")
        for name, phone in contacts.items():
            print(f"{name}: {phone}")
    elif choice == "3":
        name = input("Кімді іздейміз? ")
        print(contacts.get(name, "Табылмады"))
    elif choice == "0":
        print("Сау бол!")
        break
    else:
        print("Мәзірден таңда.")
```

**9-тапсырма.**

```python
file_name = input("Файл атауы: ")
old = input("Ескі сөз: ")
new = input("Жаңа сөз: ")
try:
    with open(file_name, encoding="utf-8") as f:
        text = f.read()
except FileNotFoundError:
    print("Файл табылмады.")
else:
    count = text.count(old)
    with open(file_name + ".bak", "w", encoding="utf-8") as f:
        f.write(text)
    with open(file_name, "w", encoding="utf-8") as f:
        f.write(text.replace(old, new))
    print("Ауыстырулар саны:", count)
```

**10-тапсырма.**

```python
name = input("Атың: ")
city = input("Қалаң: ")
hobby = input("Хоббиің: ")
with open("card.html", "w", encoding="utf-8") as f:
    print("<!DOCTYPE html>", file=f)
    print('<html><head><meta charset="utf-8"><title>Визитка</title></head><body>', file=f)
    print(f"<h1>{name}</h1>", file=f)
    print(f"<p>Қаласы: {city}</p>", file=f)
    print(f"<p>Хоббиі: {hobby}</p>", file=f)
    print("</body></html>", file=f)
print("card.html құрылды – оны браузерде аш!")
```

</details>
