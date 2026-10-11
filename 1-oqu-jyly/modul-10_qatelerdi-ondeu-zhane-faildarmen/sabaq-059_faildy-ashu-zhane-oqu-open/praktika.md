# Сабақ 59. Практикалық тапсырмалар: файлды ашу және оқу

> **Дайындық.** Тапсырмаларға қажетті файлдарды Thonny-де (Файл → Жаңа) немесе Блокнотта құрып, `.py` бағдарламаңмен **бір папкаға** UTF-8 кодтауымен сақта.

`proverbs.txt`:
```text
Өнер алды – қызыл тіл.
Білім – таусылмас кен.
Еңбек етсең ерінбей, тояды қарның тіленбей.
Оқу – инемен құдық қазғандай.
Көп сөз – көмір, аз сөз – алтын.
Бірлік бар жерде тірлік бар.
```

`grades.txt` (оқушы аты және бағасы):
```text
Айгерім 5
Нұрлан 4
Дана 5
Ерасыл 3
Мадина 4
Арман 5
```

`temperature.txt` (Алматы, бір аптадағы түскі температура, °C):
```text
12.5
14
9.5
11
16.5
18
15
```

## Базалық деңгей

**1-тапсырма. Мақалдар кітабы.** `proverbs.txt` файлын `read()` арқылы оқып, экранға шығар. Содан кейін файлдағы таңбалар санын және сөз санын шығар.

```text
Өнер алды – қызыл тіл.
...
Таңбалар саны: 182
Сөз саны: 33
```

(Сандар файлды қалай терген болуыңа қарай сәл өзгеруі мүмкін.)

**2-тапсырма. Нөмірленген мақалдар.** Сол файлды `for line in f` арқылы жолма-жол оқып, әр мақалды нөмірімен және бос жолсыз шығар.

```text
1. Өнер алды – қызыл тіл.
2. Білім – таусылмас кен.
...
```

**3-тапсырма. Алғашқы жол.** `readline()` арқылы файлдың тек бірінші жолын оқы да, оны бас әріппен (`upper()`) шығар. Соңғы мақалды табу үшін `readlines()` қолдан.

**4-тапсырма. Апталық температура.** `temperature.txt` файлын оқып, апталық орташа, ең жоғары және ең төмен температураны шығар.

```text
Орташа: 13.79 °C
Ең жылы: 18.0 °C
Ең суық: 9.5 °C
```

**5-тапсырма. Қауіпсіз ашу.** Пайдаланушыдан файл атауын сұрап, файлда неше жол бар екенін шығар. Файл табылмаса – «Файл табылмады» деп жаз (`try...except FileNotFoundError`).

## Кеңейтілген деңгей

**6-тапсырма. Бағалар журналы.** `grades.txt` файлын оқып:
- әр оқушыны «Аты – бағасы» түрінде шығар;
- сыныптың орташа бағасын есепте;
- «5» алған оқушылардың тізімін шығар;
- оқушыларды сөздікке (`{аты: баға}`) жина.

```text
Үздіктер: Айгерім, Дана, Арман
Орташа баға: 4.33
```

**7-тапсырма. Сөз іздеу.** Пайдаланушы бір сөз енгізеді. Бағдарлама `proverbs.txt` ішінде осы сөз кездесетін мақалдарды жол нөмірімен шығарады (кіші/бас әріпке қарамай). Табылмаса – «Табылмады».

```text
Іздейтін сөз: білім
2-жол: Білім – таусылмас кен.
```

**8-тапсырма. Бүлінген деректер.** `temperature.txt` файлына әдейі `он бес` және бос жол қос. 4-тапсырманы бағдарлама құламайтындай етіп өзгерт: қате жолдарды өткізіп жіберіп, олардың санын соңында хабарла.

## ★ Олимпиадалық / шығармашылық тапсырма

**9-тапсырма. Әріп жиілігі.** `proverbs.txt` файлындағы әр әріптің неше рет кездесетінін сөздік арқылы сана (тыныс белгілері мен бос орындарды есептеме, бас/кіші әріпті бірдей деп ал). Ең жиі кездесетін 5 әріпті кему ретімен шығар.

**10-тапсырма. Викторина файлдан.** `quiz.txt` файлын құр: әр жолда `сұрақ|жауап` (кемінде 5 сұрақ, мысалы `Қазақстанның астанасы?|Астана`). Бағдарлама сұрақтарды файлдан оқып, пайдаланушыдан жауап сұрайды да, соңында ұпайды шығарады. Жауапты салыстырғанда бос орын мен регистрді елеме.

## Үй тапсырмасы

1. Өзіңе ұнайтын 5–10 өлең жолынан тұратын `my_poem.txt` файлын құр. Бағдарлама: жолдар санын, ең ұзын жолды және ең көп кездесетін сөзді шығарсын.
2. `with` конструкциясы мен `close()` әдісінің айырмашылығын өз сөзіңмен 3–4 сөйлеммен жаз.
3. Келесі сабаққа ойлан: бағдарлама нәтижесін (мысалы, орташа бағаны) файлға қалай сақтауға болар еді?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма.**

```python
with open("proverbs.txt", encoding="utf-8") as f:
    text = f.read()
print(text)
print("Таңбалар саны:", len(text))
print("Сөз саны:", len(text.split()))
```

Ескерту: `split()` сызықшаны (–) да «сөз» деп санайды; мұны оқушылармен талқылауға болады.

**2-тапсырма.**

```python
with open("proverbs.txt", encoding="utf-8") as f:
    number = 1
    for line in f:
        print(f"{number}. {line.strip()}")
        number += 1
```

**3-тапсырма.**

```python
with open("proverbs.txt", encoding="utf-8") as f:
    first = f.readline()
print(first.strip().upper())

with open("proverbs.txt", encoding="utf-8") as f:
    lines = f.readlines()
print("Соңғы мақал:", lines[-1].strip())
```

**4-тапсырма.**

```python
temps = []
with open("temperature.txt", encoding="utf-8") as f:
    for line in f:
        if line.strip():
            temps.append(float(line))
print(f"Орташа: {sum(temps) / len(temps):.2f} °C")
print("Ең жылы:", max(temps), "°C")
print("Ең суық:", min(temps), "°C")
```

**5-тапсырма.**

```python
name = input("Файл атауы: ")
try:
    with open(name, encoding="utf-8") as f:
        lines = f.readlines()
    print("Жолдар саны:", len(lines))
except FileNotFoundError:
    print("Файл табылмады")
```

**6-тапсырма.**

```python
journal = {}
with open("grades.txt", encoding="utf-8") as f:
    for line in f:
        parts = line.split()
        if len(parts) == 2:
            journal[parts[0]] = int(parts[1])

for name, grade in journal.items():
    print(f"{name} – {grade}")
best = [name for name, grade in journal.items() if grade == 5]
print("Үздіктер:", ", ".join(best))
print(f"Орташа баға: {sum(journal.values()) / len(journal):.2f}")
```

**7-тапсырма.**

```python
word = input("Іздейтін сөз: ").strip().lower()
found = False
with open("proverbs.txt", encoding="utf-8") as f:
    number = 0
    for line in f:
        number += 1
        if word in line.lower():
            print(f"{number}-жол: {line.strip()}")
            found = True
if not found:
    print("Табылмады")
```

**8-тапсырма.**

```python
temps = []
bad = 0
with open("temperature.txt", encoding="utf-8") as f:
    for line in f:
        try:
            temps.append(float(line))
        except ValueError:
            bad += 1
if temps:
    print(f"Орташа: {sum(temps) / len(temps):.2f} °C")
    print("Ең жылы:", max(temps), "°C")
    print("Ең суық:", min(temps), "°C")
print("Өткізілген қате жолдар:", bad)
```

**9-тапсырма.**

```python
counts = {}
with open("proverbs.txt", encoding="utf-8") as f:
    for ch in f.read().lower():
        if ch.isalpha():
            counts[ch] = counts.get(ch, 0) + 1

pairs = []
for letter, cnt in counts.items():
    pairs.append([cnt, letter])
pairs.sort(reverse=True)
for cnt, letter in pairs[:5]:
    print(letter, "-", cnt)
```

**10-тапсырма.**

```python
score = 0
total = 0
with open("quiz.txt", encoding="utf-8") as f:
    for line in f:
        if "|" not in line:
            continue
        question, answer = line.strip().split("|")
        total += 1
        user = input(question + " ")
        if user.strip().lower() == answer.strip().lower():
            print("Дұрыс!")
            score += 1
        else:
            print("Қате. Дұрыс жауап:", answer)
print(f"Нәтиже: {score}/{total}")
```

</details>
