# Урок 5. Практические задания: Git — init, add, commit, log

## Базовый уровень

### Задание 1. Настройка Git
1. Проверь версию Git в терминале.
2. Настрой своё имя (латиницей) и почту, проверь через `git config --list`.
3. Установи имя ветки по умолчанию `main`.

### Задание 2. Первый репозиторий
1. Создай папку `git_practice` и открой её в VS Code.
2. Выполни `git init`. Проверь, что появилась скрытая папка `.git` (`ls -Force` — Windows, `ls -a` — macOS/Linux).
3. Создай файл `about_me.py`:
```python
name = "Айгерим"
print("Меня зовут", name)
```
4. `git status` → `git add about_me.py` → `git status` → `git commit -m "Создан файл about_me.py"`.
5. После каждого шага кратко запиши в тетрадь, что показал `git status` (в каком состоянии файл).

### Задание 3. Три коммита
Измени файл `about_me.py` три раза и после каждого изменения сделай коммит:
1. Добавь строку, выводящую твой возраст → коммит «Добавлен возраст».
2. Добавь строку, выводящую твоё хобби → коммит «Добавлено хобби».
3. Собери данные в словарь и выведи их циклом → коммит «Данные собраны в словарь».

В конце выполни `git log --oneline` — должно быть видно 4 коммита:
```text
a81c3d0 (HEAD -> main) Данные собраны в словарь
5e2f911 Добавлено хобби
c09b7aa Добавлен возраст
1d4e6f2 Создан файл about_me.py
```

### Задание 4. git diff и git restore
1. Специально добавь в файл `about_me.py` «ошибочную» строку, например `print(10 / 0)`.
2. Выполни `git diff` — добавленная строка видна со знаком `+`.
3. Верни файл в состояние последнего коммита с помощью `git restore about_me.py`.
4. Открой файл и проверь, что ошибочная строка исчезла, `git status` — «working tree clean».

### Задание 5. .gitignore
1. Создай в папке виртуальное окружение (`python -m venv .venv`) и выполни `git status` — что видно?
2. Создай файл `.gitignore` и запиши в него `.venv/`, `__pycache__/`, `*.pyc`.
3. Снова `git status` — `.venv` должна исчезнуть, должен быть виден только `.gitignore`.
4. Закоммить `.gitignore`: «Добавлен .gitignore».

## Продвинутый уровень

### Задание 6. Проект «Калькулятор» — пошагово
Создай новый репозиторий `calculator` и разрабатывай проект, делая коммит после каждого шага:

| Шаг | Что сделать | Сообщение коммита |
|---|---|---|
| 1 | `.gitignore` | `Добавлен .gitignore` |
| 2 | Функции `add(a, b)`, `sub(a, b)` | `Функции сложения и вычитания` |
| 3 | `mul(a, b)`, `div(a, b)` (проверь деление на ноль) | `Умножение и деление, проверка деления на ноль` |
| 4 | `main()` — меню: ввод чисел и операции | `Добавлено меню пользователя` |
| 5 | Повторение в цикле, «0» — выход | `Калькулятор работает в цикле` |

В конце `git log --oneline` — минимум 5 коммитов. Покажи историю педагогу.

```text
Первое число: 12
Операция (+ - * /): /
Второе число: 0
Ошибка: делить на ноль нельзя!
```

### Задание 7. Путешествие по истории
1. Найди хеш 2-го коммита с помощью `git log --oneline`.
2. `git show <хеш>` — посмотри, что изменилось в этом коммите.
3. `git show <хеш>:calc.py` — посмотри полный текст файла на тот момент.
4. `git log --stat` — посмотри, какие файлы и сколько строк изменились в каждом коммите.

### Задание 8. Source Control в VS Code
Добавь в калькулятор функцию возведения в степень (`power`). На этот раз **без терминала**, только через панель Source Control в VS Code: посмотри изменения (diff), добавь в индекс через `+`, напиши сообщение и нажми ✓. Проверь в терминале через `git log --oneline`.

## ★ Олимпиадная / творческая задача

### Задание 9. Управление Git из Python
Напиши скрипт «Помощник Git», который выполняет команды Git через модуль `subprocess`: он читает результат `git status --short`, подсчитывает изменённые файлы, запрашивает у пользователя сообщение коммита (пустое — не принимает, короче 5 символов — предупреждает), затем выполняет `git add .` и `git commit -m`.

```text
Изменённых файлов: 2
 M calc.py
?? notes.txt
Сообщение коммита: Исправлена функция деления
Коммит успешно создан!
```

### Задание 10. «Git-детектив»
В репозитории, подготовленном педагогом (или в репозитории соседа), программа работает неправильно. С помощью `git log` и `git show` найди, какой коммит внёс ошибку, и сделай новый коммит, исправляющий её. Дополнительно: познакомься с командой `git revert` и объясни её отличие от `git restore`.

## Домашнее задание

1. Преврати любую свою программу с прошлых уроков (например, игру «Угадай число») в отдельный репозиторий: `.gitignore`, минимум 4 осмысленных коммита.
2. К следующему уроку зарегистрируй аккаунт на сайте **github.com** (с 13 лет; младшие учащиеся — вместе с родителями или с помощью педагога). Продумай и выбери имя пользователя: оно станет адресом твоего портфолио (например, `aigerim-dev`).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```bash
git --version
git config --global user.name "Aigerim Nurlanqyzy"
git config --global user.email "aigerim@example.com"
git config --global init.defaultBranch main
git config --list
```

**Задание 2** — состояния: до `git add` — Untracked; после `git add` — Changes to be committed (staged); после коммита — nothing to commit, working tree clean.

**Задание 3** — итоговая версия:
```python
me = {"Имя": "Айгерим", "Возраст": 13, "Хобби": "шахматы"}
for key, value in me.items():
    print(f"{key}: {value}")
```
```bash
git add about_me.py
git commit -m "Данные собраны в словарь"
git log --oneline
```

**Задание 5**
```text
.venv/
__pycache__/
*.pyc
```
```bash
git add .gitignore
git commit -m "Добавлен .gitignore"
```

**Задание 6** — итоговая версия `calc.py`:
```python
def add(a, b):
    """Сумма."""
    return a + b


def sub(a, b):
    """Разность."""
    return a - b


def mul(a, b):
    """Произведение."""
    return a * b


def div(a, b):
    """Частное; при делении на ноль возвращает None."""
    if b == 0:
        return None
    return a / b


OPERATIONS = {"+": add, "-": sub, "*": mul, "/": div}


def main():
    while True:
        first = input("Первое число (0 — выход): ")
        if first == "0":
            break
        op = input("Операция (+ - * /): ")
        second = input("Второе число: ")
        if op not in OPERATIONS:
            print("Неизвестная операция!")
            continue
        result = OPERATIONS[op](float(first), float(second))
        if result is None:
            print("Ошибка: делить на ноль нельзя!")
        else:
            print("Результат:", result)


if __name__ == "__main__":
    main()
```

**Задание 7**
```bash
git log --oneline
git show c09b7aa
git show c09b7aa:calc.py
git log --stat
```

**Задание 9**
```python
import subprocess


def run_git(*args):
    """Выполняет команду Git и возвращает её вывод."""
    result = subprocess.run(["git", *args], capture_output=True, text=True)
    return result.returncode, result.stdout.strip()


def main():
    code, status = run_git("status", "--short")
    if code != 0:
        print("Эта папка не является репозиторием Git!")
        return
    if not status:
        print("Изменений нет — коммитить нечего.")
        return
    lines = status.splitlines()
    print("Изменённых файлов:", len(lines))
    for line in lines:
        print(line)

    while True:
        message = input("Сообщение коммита: ").strip()
        if not message:
            print("Сообщение не должно быть пустым!")
        elif len(message) < 5:
            print("Слишком коротко, напиши подробнее.")
        else:
            break

    run_git("add", ".")
    code, output = run_git("commit", "-m", message)
    print("Коммит успешно создан!" if code == 0 else output)


if __name__ == "__main__":
    main()
```

**Задание 10** — `git revert <хеш>` создаёт новый коммит, обратный изменениям указанного коммита (история сохраняется), а `git restore` возвращает только незакоммиченные изменения в рабочем каталоге.

</details>
