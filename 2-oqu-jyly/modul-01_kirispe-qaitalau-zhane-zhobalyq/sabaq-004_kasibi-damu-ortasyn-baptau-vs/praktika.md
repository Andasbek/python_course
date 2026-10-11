# Сабақ 4. Практикалық тапсырмалар: VS Code, venv, pip

## Базалық деңгей

### 1-тапсырма. Жоба папкасын дайындау
1. Жұмыс үстелінде (немесе `Documents` ішінде) `python_club` папкасын, оның ішінде `lesson04` папкасын құр.
2. VS Code-та **File → Open Folder** арқылы `lesson04` папкасын аш.
3. Python кеңейтімі орнатылғанын тексер (Extensions → «Python» by Microsoft).
4. `hello.py` файлын құрып, мына кодты жаз да, ▷ батырмасымен іске қос:

```python
import sys
import platform

print("Сәлем, VS Code!")
print("Python нұсқасы:", platform.python_version())
print("Операциялық жүйе:", platform.system())
print("Интерпретатор:", sys.executable)
```

5. Сол бағдарламаны терминалдан іске қос: `` Ctrl+` `` → `python hello.py`.

Тексеру: экранда Python нұсқасы 3.10 немесе одан жоғары болуы керек.

### 2-тапсырма. Терминал командалары
Терминалда мына әрекеттерді орында және әр команданы дәптерге жаз:
1. Ағымдағы папканы көрсет.
2. `data` атты жаңа папка құр.
3. Сол папкаға өтіп, қайта жоғары шық.
4. Папка ішіндегі файлдар тізімін шығар.
5. Python нұсқасын тексер.

### 3-тапсырма. Отладчикпен жұмыс
`debug_me.py` файлына мына кодты жаз. Бағдарлама 1-ден 5-ке дейінгі сандардың орташасы 3.0 болуы керек еді, бірақ басқа нәтиже шығарады. **Отладчикті** (breakpoint + F10) қолданып, `total` пен `count` мәндерін бақыла, қатені тауып түзет.

```python
numbers = [1, 2, 3, 4, 5]
total = 0
count = 0
for n in numbers:
    total += n
count += 1
print("Орташа:", total / count)
```

```text
Күтілген нәтиже: Орташа: 3.0
Қазіргі нәтиже:  Орташа: 15.0
```

### 4-тапсырма. Виртуалды орта және бірінші пакет
1. `lesson04` папкасында виртуалды орта құр: `python -m venv .venv`.
2. Оны белсендір; терминалда `(.venv)` пайда болғанын тексер.
3. VS Code-та интерпретаторды таңда: `Ctrl+Shift+P` → «Python: Select Interpreter» → `.venv`.
4. `pip list` орында — қандай пакеттер бар?
5. `pip install colorama` орнат, қайта `pip list` орында.
6. `colors.py` файлында түрлі түсті хабарламалар шығар: жасыл — «Дұрыс», қызыл — «Қате», сары — «Ескерту», көк — өз атың.

## Кеңейтілген деңгей

### 5-тапсырма. requirements.txt
1. Сол ортаға `requests` пакетін де орнат.
2. `pip freeze > requirements.txt` орындап, файлды аш — неше жол бар? Неге біз тек екі пакет орнатсақ та тізім ұзын (тәуелділіктер)?
3. Көршіңмен бірге тексер: жаңа `lesson04_copy` папкасын құрып, ішінде жаңа venv жаса және `pip install -r requirements.txt` арқылы барлық пакетті орнат.

### 6-тапсырма. «Орта диагностикасы» скрипті
`check_env.py` бағдарламасын жаз. Ол:
- Python нұсқасын шығарады және 3.10-нан төмен болса ескертеді;
- виртуалды ортада іске қосылғанын не қосылмағанын хабарлайды;
- берілген пакеттер тізімі (`colorama`, `requests`, `pygame`) үшін әрқайсысы орнатылған ба, жоқ па тексереді (`importlib.util.find_spec` қолдан).

```text
Python 3.12.4 — жарайды
Виртуалды орта: иә
colorama — орнатылған
requests — орнатылған
pygame — ЖОҚ (pip install pygame)
```

### 7-тапсырма. VS Code жылдамдығы
Педагог берген 10 жолдық кодта (немесе өз кодыңда) пернелер тіркесімімен орында: айнымалының атын барлық жерде өзгерту (`F2`), 3 жолды түсініктемеге айналдыру (`Ctrl+/`), жолды жоғары жылжыту (`Alt+↑`), кодты пішімдеу (`Shift+Alt+F`), `Ctrl+P` арқылы файлды ашу. Ең жылдам орындаған оқушы — «VS Code шебері».

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Түрлі түсті «терминал викторинасы»
`colorama` арқылы 5 сұрақтан тұратын викторина жаз (тақырыбы — бүгінгі сабақ: venv, pip, VS Code). Дұрыс жауап — жасыл, қате — қызыл, қорытынды ұпай пайызға қарай түспен (≥80% — жасыл, ≥50% — сары, қалғаны — қызыл). Сұрақтарды кортеждер тізімінде сақта, тексеруді функцияға шығар.

### 9-тапсырма. Өз модулің — өз «пакетің»
`mytools` папкасын құрып, ішіне `__init__.py` және `text_tools.py` (мысалы, `count_vowels(text)`, `reverse_words(text)` функциялары) файлдарын жаса. Басқа файлдан `from mytools.text_tools import count_vowels` арқылы импортта. Пакет пен модульдің айырмашылығын түсіндір.

## Үй тапсырмасы

1. Үйдегі компьютерге VS Code және Python кеңейтімін орнат (мүмкіндік болса).
2. Командалар шпаргалкасын толтыр: venv құру/белсендіру/өшіру, pip-тің 6 командасы, VS Code-тың 6 пернесі.
3. Келесі сабаққа: git-scm.com сайтынан Git орнатуға тырыс, `git --version` командасымен тексер.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**2-тапсырма (Windows PowerShell)**
```bash
pwd
mkdir data
cd data
cd ..
ls
python --version
```

**3-тапсырма** — қате: `count += 1` циклдің сыртында (шегініссіз), сондықтан тек бір рет орындалады.
```python
numbers = [1, 2, 3, 4, 5]
total = 0
count = 0
for n in numbers:
    total += n
    count += 1
print("Орташа:", total / count)
```

**4-тапсырма**
```bash
python -m venv .venv
.venv\Scripts\activate
pip list
pip install colorama
pip list
```
```python
from colorama import Fore, Style, init

init()
print(Fore.GREEN + "Дұрыс" + Style.RESET_ALL)
print(Fore.RED + "Қате" + Style.RESET_ALL)
print(Fore.YELLOW + "Ескерту" + Style.RESET_ALL)
print(Fore.BLUE + "Айгерім" + Style.RESET_ALL)
```

**5-тапсырма** — `requests` өзімен бірге `certifi`, `charset-normalizer`, `idna`, `urllib3` пакеттерін орнатады (тәуелділіктер), сондықтан `requirements.txt`-те 6 жол болады.
```bash
pip install requests
pip freeze > requirements.txt
cd ..
mkdir lesson04_copy
cd lesson04_copy
python -m venv .venv
.venv\Scripts\activate
pip install -r ..\lesson04\requirements.txt
```

**6-тапсырма**
```python
import sys
import platform
import importlib.util

PACKAGES = ["colorama", "requests", "pygame"]


def check_python():
    """Python нұсқасын тексереді."""
    version = platform.python_version()
    if sys.version_info >= (3, 10):
        print(f"Python {version} — жарайды")
    else:
        print(f"Python {version} — ескі нұсқа, 3.10+ орнат!")


def in_venv():
    """Виртуалды ортада іске қосылса True қайтарады."""
    return sys.prefix != sys.base_prefix


def check_packages(packages):
    """Әр пакеттің орнатылғанын тексереді."""
    for name in packages:
        if importlib.util.find_spec(name) is not None:
            print(f"{name} — орнатылған")
        else:
            print(f"{name} — ЖОҚ (pip install {name})")


def main():
    check_python()
    print("Виртуалды орта:", "иә" if in_venv() else "жоқ")
    check_packages(PACKAGES)


if __name__ == "__main__":
    main()
```

**8-тапсырма**
```python
from colorama import Fore, Style, init

QUESTIONS = [
    ("Виртуалды орта құратын команда: python -m ...?", "venv"),
    ("Пакет орнату командасы: pip ...?", "install"),
    ("Тәуелділіктер тізімі сақталатын файл?", "requirements.txt"),
    ("Python пакеттерінің ресми репозиторийі?", "pypi"),
    ("VS Code-та терминалды ашатын перне: Ctrl + ?", "`"),
]


def color_print(text, color):
    """Мәтінді берілген түспен шығарады."""
    print(color + text + Style.RESET_ALL)


def ask(question, answer):
    """Сұрақ қояды, дұрыс болса True қайтарады."""
    reply = input(question + " ").strip().lower()
    if reply == answer.lower():
        color_print("Дұрыс!", Fore.GREEN)
        return True
    color_print(f"Қате. Дұрыс жауабы: {answer}", Fore.RED)
    return False


def main():
    init()
    score = sum(ask(q, a) for q, a in QUESTIONS)
    percent = score / len(QUESTIONS) * 100
    if percent >= 80:
        color = Fore.GREEN
    elif percent >= 50:
        color = Fore.YELLOW
    else:
        color = Fore.RED
    color_print(f"Нәтиже: {score}/{len(QUESTIONS)} ({percent:.0f}%)", color)


if __name__ == "__main__":
    main()
```

**9-тапсырма** — `mytools/text_tools.py`:
```python
VOWELS = "аәеиоөұүыіэюяуaeiou"


def count_vowels(text):
    """Мәтіндегі дауысты дыбыстар санын қайтарады."""
    return sum(1 for ch in text.lower() if ch in VOWELS)


def reverse_words(text):
    """Сөздердің ретін керісінше қайтарады."""
    return " ".join(reversed(text.split()))
```
Негізгі файл:
```python
from mytools.text_tools import count_vowels, reverse_words

print(count_vowels("Python үйірмесі"))
print(reverse_words("мен Python үйренемін"))
```
Модуль — бір `.py` файл; пакет — модульдері бар, `__init__.py` файлы бар папка.

</details>
