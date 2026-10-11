# Урок 4. Практические задания: VS Code, venv, pip

## Базовый уровень

### Задание 1. Подготовка папки проекта
1. На рабочем столе (или внутри `Documents`) создай папку `python_club`, а в ней — папку `lesson04`.
2. В VS Code открой папку `lesson04` через **File → Open Folder**.
3. Проверь, что установлено расширение Python (Extensions → «Python» by Microsoft).
4. Создай файл `hello.py`, напиши в нём следующий код и запусти кнопкой ▷:

```python
import sys
import platform

print("Привет, VS Code!")
print("Версия Python:", platform.python_version())
print("Операционная система:", platform.system())
print("Интерпретатор:", sys.executable)
```

5. Запусти ту же программу из терминала: `` Ctrl+` `` → `python hello.py`.

Проверка: на экране версия Python должна быть 3.10 или выше.

### Задание 2. Команды терминала
Выполни в терминале следующие действия и запиши каждую команду в тетрадь:
1. Покажи текущую папку.
2. Создай новую папку `data`.
3. Перейди в эту папку и вернись обратно наверх.
4. Выведи список файлов в папке.
5. Проверь версию Python.

### Задание 3. Работа с отладчиком
Запиши в файл `debug_me.py` следующий код. Программа должна была вывести среднее чисел от 1 до 5, равное 3.0, но выводит другой результат. С помощью **отладчика** (breakpoint + F10) следи за значениями `total` и `count`, найди и исправь ошибку.

```python
numbers = [1, 2, 3, 4, 5]
total = 0
count = 0
for n in numbers:
    total += n
count += 1
print("Среднее:", total / count)
```

```text
Ожидаемый результат: Среднее: 3.0
Текущий результат:   Среднее: 15.0
```

### Задание 4. Виртуальное окружение и первый пакет
1. В папке `lesson04` создай виртуальное окружение: `python -m venv .venv`.
2. Активируй его; проверь, что в терминале появилось `(.venv)`.
3. Выбери интерпретатор в VS Code: `Ctrl+Shift+P` → «Python: Select Interpreter» → `.venv`.
4. Выполни `pip list` — какие пакеты есть?
5. Установи `pip install colorama`, снова выполни `pip list`.
6. В файле `colors.py` выведи цветные сообщения: зелёным — «Верно», красным — «Ошибка», жёлтым — «Предупреждение», синим — своё имя.

## Продвинутый уровень

### Задание 5. requirements.txt
1. Установи в то же окружение пакет `requests`.
2. Выполни `pip freeze > requirements.txt` и открой файл — сколько в нём строк? Почему список длинный, хотя мы установили только два пакета (зависимости)?
3. Проверь вместе с соседом: создай новую папку `lesson04_copy`, сделай в ней новый venv и установи все пакеты через `pip install -r requirements.txt`.

### Задание 6. Скрипт «Диагностика окружения»
Напиши программу `check_env.py`. Она:
- выводит версию Python и предупреждает, если она ниже 3.10;
- сообщает, запущена ли она в виртуальном окружении;
- для заданного списка пакетов (`colorama`, `requests`, `pygame`) проверяет, установлен ли каждый из них (используй `importlib.util.find_spec`).

```text
Python 3.12.4 — подходит
Виртуальное окружение: да
colorama — установлен
requests — установлен
pygame — НЕТ (pip install pygame)
```

### Задание 7. Скорость в VS Code
В коде из 10 строк, выданном педагогом (или в своём коде), выполни с помощью сочетаний клавиш: переименование переменной везде (`F2`), превращение 3 строк в комментарий (`Ctrl+/`), перемещение строки вверх (`Alt+↑`), форматирование кода (`Shift+Alt+F`), открытие файла через `Ctrl+P`. Учащийся, выполнивший всё быстрее всех, — «Мастер VS Code».

## ★ Олимпиадная / творческая задача

### Задание 8. Цветная «терминальная викторина»
С помощью `colorama` напиши викторину из 5 вопросов (тема — сегодняшний урок: venv, pip, VS Code). Правильный ответ — зелёным, неправильный — красным, итоговый балл — цветом в зависимости от процента (≥80% — зелёный, ≥50% — жёлтый, иначе — красный). Храни вопросы в списке кортежей, проверку вынеси в функцию.

### Задание 9. Свой модуль — свой «пакет»
Создай папку `mytools`, а в ней файлы `__init__.py` и `text_tools.py` (например, с функциями `count_vowels(text)`, `reverse_words(text)`). Импортируй из другого файла через `from mytools.text_tools import count_vowels`. Объясни разницу между пакетом и модулем.

## Домашнее задание

1. Установи VS Code и расширение Python на домашний компьютер (если есть возможность).
2. Заполни шпаргалку команд: создание/активация/деактивация venv, 6 команд pip, 6 сочетаний клавиш VS Code.
3. К следующему уроку: попробуй установить Git с сайта git-scm.com и проверь командой `git --version`.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 2 (Windows PowerShell)**
```bash
pwd
mkdir data
cd data
cd ..
ls
python --version
```

**Задание 3** — ошибка: `count += 1` находится вне цикла (без отступа), поэтому выполняется только один раз.
```python
numbers = [1, 2, 3, 4, 5]
total = 0
count = 0
for n in numbers:
    total += n
    count += 1
print("Среднее:", total / count)
```

**Задание 4**
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
print(Fore.GREEN + "Верно" + Style.RESET_ALL)
print(Fore.RED + "Ошибка" + Style.RESET_ALL)
print(Fore.YELLOW + "Предупреждение" + Style.RESET_ALL)
print(Fore.BLUE + "Айгерим" + Style.RESET_ALL)
```

**Задание 5** — `requests` устанавливает вместе с собой пакеты `certifi`, `charset-normalizer`, `idna`, `urllib3` (зависимости), поэтому в `requirements.txt` будет 6 строк.
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

**Задание 6**
```python
import sys
import platform
import importlib.util

PACKAGES = ["colorama", "requests", "pygame"]


def check_python():
    """Проверяет версию Python."""
    version = platform.python_version()
    if sys.version_info >= (3, 10):
        print(f"Python {version} — подходит")
    else:
        print(f"Python {version} — старая версия, установи 3.10+!")


def in_venv():
    """Возвращает True, если запущено в виртуальном окружении."""
    return sys.prefix != sys.base_prefix


def check_packages(packages):
    """Проверяет, установлен ли каждый пакет."""
    for name in packages:
        if importlib.util.find_spec(name) is not None:
            print(f"{name} — установлен")
        else:
            print(f"{name} — НЕТ (pip install {name})")


def main():
    check_python()
    print("Виртуальное окружение:", "да" if in_venv() else "нет")
    check_packages(PACKAGES)


if __name__ == "__main__":
    main()
```

**Задание 8**
```python
from colorama import Fore, Style, init

QUESTIONS = [
    ("Команда создания виртуального окружения: python -m ...?", "venv"),
    ("Команда установки пакета: pip ...?", "install"),
    ("Файл, в котором хранится список зависимостей?", "requirements.txt"),
    ("Официальный репозиторий пакетов Python?", "pypi"),
    ("Клавиша открытия терминала в VS Code: Ctrl + ?", "`"),
]


def color_print(text, color):
    """Выводит текст заданным цветом."""
    print(color + text + Style.RESET_ALL)


def ask(question, answer):
    """Задаёт вопрос, возвращает True, если ответ верный."""
    reply = input(question + " ").strip().lower()
    if reply == answer.lower():
        color_print("Верно!", Fore.GREEN)
        return True
    color_print(f"Неверно. Правильный ответ: {answer}", Fore.RED)
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
    color_print(f"Результат: {score}/{len(QUESTIONS)} ({percent:.0f}%)", color)


if __name__ == "__main__":
    main()
```

**Задание 9** — `mytools/text_tools.py`:
```python
VOWELS = "аеёиоуыэюяaeiou"


def count_vowels(text):
    """Возвращает количество гласных букв в тексте."""
    return sum(1 for ch in text.lower() if ch in VOWELS)


def reverse_words(text):
    """Возвращает слова в обратном порядке."""
    return " ".join(reversed(text.split()))
```
Основной файл:
```python
from mytools.text_tools import count_vowels, reverse_words

print(count_vowels("Кружок Python"))
print(reverse_words("я изучаю Python"))
```
Модуль — один файл `.py`; пакет — папка с модулями и файлом `__init__.py`.

</details>
