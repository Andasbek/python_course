# Сабақ 5. Практикалық тапсырмалар: Git — init, add, commit, log

## Базалық деңгей

### 1-тапсырма. Git-ті баптау
1. Терминалда Git нұсқасын тексер.
2. Атыңды (латынша) және поштаңды баптап, `git config --list` арқылы тексер.
3. Әдепкі тармақ атауын `main` етіп орнат.

### 2-тапсырма. Бірінші репозиторий
1. `git_practice` папкасын құрып, VS Code-та аш.
2. `git init` орында. Жасырын `.git` папкасы пайда болғанын тексер (`ls -Force` — Windows, `ls -a` — macOS/Linux).
3. `about_me.py` файлын құр:
```python
name = "Айгерім"
print("Менің атым", name)
```
4. `git status` → `git add about_me.py` → `git status` → `git commit -m "about_me.py файлы құрылды"`.
5. Әр қадамнан кейін `git status` не көрсеткенін дәптерге қысқаша жаз (файлдың күйі қандай).

### 3-тапсырма. Үш коммит
`about_me.py` файлын үш рет өзгертіп, әр өзгерістен кейін коммит жаса:
1. Жасыңды шығаратын жол қос → коммит «Жасы қосылды».
2. Хоббиіңді шығаратын жол қос → коммит «Хобби қосылды».
3. Деректерді сөздікке жинап, циклмен шығар → коммит «Деректер сөздікке жиналды».

Соңында `git log --oneline` орында — 4 коммит көрінуі керек:
```text
a81c3d0 (HEAD -> main) Деректер сөздікке жиналды
5e2f911 Хобби қосылды
c09b7aa Жасы қосылды
1d4e6f2 about_me.py файлы құрылды
```

### 4-тапсырма. git diff және git restore
1. `about_me.py` файлына әдейі «қате» жол қос, мысалы `print(10 / 0)`.
2. `git diff` орында — қосылған жол `+` белгісімен көрінеді.
3. `git restore about_me.py` арқылы файлды соңғы коммиттегі күйге қайтар.
4. Файлды ашып, қате жолдың жоғалғанын тексер, `git status` — «working tree clean».

### 5-тапсырма. .gitignore
1. Папкада виртуалды орта құр (`python -m venv .venv`) және `git status` орында — не көрінеді?
2. `.gitignore` файлын құрып, ішіне `.venv/`, `__pycache__/`, `*.pyc` жаз.
3. Қайта `git status` — `.venv` жоғалып, тек `.gitignore` көрінуі керек.
4. `.gitignore`-ды коммитте: «.gitignore қосылды».

## Кеңейтілген деңгей

### 6-тапсырма. «Калькулятор» жобасы — қадамдап
Жаңа `calculator` репозиторийін құр және жобаны әр қадамнан кейін коммиттеп әзірле:

| Қадам | Не істеу керек | Коммит хабарламасы |
|---|---|---|
| 1 | `.gitignore` | `.gitignore қосылды` |
| 2 | `add(a, b)`, `sub(a, b)` функциялары | `Қосу мен азайту функциялары` |
| 3 | `mul(a, b)`, `div(a, b)` (нөлге бөлуді тексер) | `Көбейту және бөлу, нөлге бөлуді тексеру` |
| 4 | `main()` — мәзір: сандар мен амалды енгізу | `Пайдаланушы мәзірі қосылды` |
| 5 | Циклмен қайталау, «0» — шығу | `Калькулятор циклде жұмыс істейді` |

Соңында `git log --oneline` — кемінде 5 коммит. Педагогке тарихты көрсет.

```text
Бірінші сан: 12
Амал (+ - * /): /
Екінші сан: 0
Қате: нөлге бөлуге болмайды!
```

### 7-тапсырма. Тарихқа саяхат
1. `git log --oneline` арқылы 2-коммиттің хэшін тап.
2. `git show <хэш>` — сол коммитте не өзгергенін қара.
3. `git show <хэш>:calc.py` — сол кездегі файлдың толық мәтінін көр.
4. `git log --stat` — әр коммитте қай файлдар және неше жол өзгергенін көр.

### 8-тапсырма. VS Code Source Control
Калькуляторға дәрежеге шығару (`power`) функциясын қос. Бұл жолы **терминалсыз**, тек VS Code-тың Source Control панелі арқылы: өзгерісті қара (diff), `+` арқылы индекске қос, хабарлама жазып ✓ бас. Терминалда `git log --oneline` арқылы тексер.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. Git-ті Python-нан басқару
`subprocess` модулі арқылы Git командаларын орындайтын «Git көмекшісі» скриптін жаз: ол `git status --short` нәтижесін оқып, өзгерген файлдарды санайды, пайдаланушыдан коммит хабарламасын сұрайды (бос болса — қабылдамайды, 5 таңбадан қысқа болса — ескертеді), кейін `git add .` және `git commit -m` орындайды.

```text
Өзгерген файлдар: 2
 M calc.py
?? notes.txt
Коммит хабарламасы: Бөлу функциясы түзетілді
Коммит сәтті жасалды!
```

### 10-тапсырма. «Git-детектив»
Педагог дайындаған репозиторийде (немесе көршіңнің репозиторийінде) бағдарлама дұрыс жұмыс істемейді. `git log` және `git show` арқылы қатені қай коммит әкелгенін тап, қатені түзететін жаңа коммит жаса. Қосымша: `git revert` командасымен танысып, оның `git restore`-дан айырмашылығын түсіндір.

## Үй тапсырмасы

1. Өткен сабақтардағы кез келген бағдарламаңды (мысалы, «Сан табу» ойыны) жеке репозиторийге айналдыр: `.gitignore`, кемінде 4 мағыналы коммит.
2. Келесі сабаққа **github.com** сайтында аккаунт тірке (13 жастан бастап; жасы кіші оқушылар — ата-анасымен бірге немесе педагогтің көмегімен). Пайдаланушы атауын ойлап таңда: ол портфолиоңның мекенжайы болады (мысалы, `aigerim-dev`).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```bash
git --version
git config --global user.name "Aigerim Nurlanqyzy"
git config --global user.email "aigerim@example.com"
git config --global init.defaultBranch main
git config --list
```

**2-тапсырма** — күйлер: `git add`-ке дейін — Untracked; `git add`-тен кейін — Changes to be committed (staged); коммиттен кейін — nothing to commit, working tree clean.

**3-тапсырма** — соңғы нұсқа:
```python
me = {"Аты": "Айгерім", "Жасы": 13, "Хоббиі": "шахмат"}
for key, value in me.items():
    print(f"{key}: {value}")
```
```bash
git add about_me.py
git commit -m "Деректер сөздікке жиналды"
git log --oneline
```

**5-тапсырма**
```text
.venv/
__pycache__/
*.pyc
```
```bash
git add .gitignore
git commit -m ".gitignore қосылды"
```

**6-тапсырма** — `calc.py` соңғы нұсқасы:
```python
def add(a, b):
    """Қосынды."""
    return a + b


def sub(a, b):
    """Айырма."""
    return a - b


def mul(a, b):
    """Көбейтінді."""
    return a * b


def div(a, b):
    """Бөлінді; нөлге бөлсе None қайтарады."""
    if b == 0:
        return None
    return a / b


OPERATIONS = {"+": add, "-": sub, "*": mul, "/": div}


def main():
    while True:
        first = input("Бірінші сан (0 — шығу): ")
        if first == "0":
            break
        op = input("Амал (+ - * /): ")
        second = input("Екінші сан: ")
        if op not in OPERATIONS:
            print("Белгісіз амал!")
            continue
        result = OPERATIONS[op](float(first), float(second))
        if result is None:
            print("Қате: нөлге бөлуге болмайды!")
        else:
            print("Нәтиже:", result)


if __name__ == "__main__":
    main()
```

**7-тапсырма**
```bash
git log --oneline
git show c09b7aa
git show c09b7aa:calc.py
git log --stat
```

**9-тапсырма**
```python
import subprocess


def run_git(*args):
    """Git командасын орындап, шығысын қайтарады."""
    result = subprocess.run(["git", *args], capture_output=True, text=True)
    return result.returncode, result.stdout.strip()


def main():
    code, status = run_git("status", "--short")
    if code != 0:
        print("Бұл папка Git репозиторийі емес!")
        return
    if not status:
        print("Өзгеріс жоқ — коммиттейтін ештеңе жоқ.")
        return
    lines = status.splitlines()
    print("Өзгерген файлдар:", len(lines))
    for line in lines:
        print(line)

    while True:
        message = input("Коммит хабарламасы: ").strip()
        if not message:
            print("Хабарлама бос болмауы керек!")
        elif len(message) < 5:
            print("Тым қысқа, толығырақ жаз.")
        else:
            break

    run_git("add", ".")
    code, output = run_git("commit", "-m", message)
    print("Коммит сәтті жасалды!" if code == 0 else output)


if __name__ == "__main__":
    main()
```

**10-тапсырма** — `git revert <хэш>` көрсетілген коммиттің өзгерістеріне кері жаңа коммит жасайды (тарих сақталады), ал `git restore` тек жұмыс каталогындағы коммиттелмеген өзгерістерді қайтарады.

</details>
