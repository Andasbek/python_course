# Урок 33. Загрузка GUI-проекта на GitHub и его защита. Самостоятельная работа №2.

## Темы

1. Структура хорошего репозитория
2. Написание README.md
3. Создание скриншота
4. Осмысленные сообщения коммитов
5. Загрузка на GitHub: порядок команд
6. Проверка проекта перед публикацией
7. Шпаргалка по Tkinter
8. Частые ошибки
9. Основные понятия

## 1. Структура хорошего репозитория

Репозиторий на GitHub — это твоё **портфолио**. Его могут увидеть другие разработчики или работодатель. Аккуратно оформленный проект говорит: «этот человек уважает свою работу».

```text
todo-app/
├── todo_app.py          # основная программа
├── README.md            # описание проекта
├── .gitignore           # файлы, которые не попадают в репозиторий
└── screenshots/
    └── main_window.png  # скриншот интерфейса
```

**Пример `.gitignore`** — чтобы не добавлять в Git временные файлы и личные данные:

```text
__pycache__/
*.pyc
venv/
.vscode/
tasks.txt
```

## 2. Написание README.md

README — «визитная карточка» проекта. Он пишется на языке разметки Markdown.

**Образец README:**

```text
# To-Do List — список задач

Список задач, созданный на библиотеке Tkinter и сохраняющий данные в файле.

## Возможности
- добавление задачи (кнопка или Enter);
- отметка о выполнении (двойной щелчок);
- редактирование и удаление;
- автоматическое сохранение при закрытии окна.

## Скриншот
![Главное окно](screenshots/main_window.png)

## Запуск
python todo_app.py

## Использованные технологии
Python 3.12, Tkinter

## Автор
Алихан Серикулы, 2026
```

**5 признаков хорошего README:**
1. Уже из первого предложения понятно, что делает проект.
2. Есть скриншот.
3. Инструкция по запуску конкретная (с командой).
4. Возможности перечислены списком.
5. Указаны автор и технологии.

## 3. Создание скриншота

| ОС | Способ |
|---|---|
| Windows | `Win + Shift + S` → выделить область → вставить в Paint, сохранить в PNG |
| macOS | `Cmd + Shift + 4` → выделить область |
| Linux | `PrtSc` или приложение «Скриншот» |

Скриншот кладём в папку `screenshots/` и добавляем в README через `![описание](путь)`.

## 4. Осмысленные сообщения коммитов

| Плохо | Хорошо |
|---|---|
| `asdf` | `Создание сетки кнопок через grid` |
| `fix` | `Обработка ошибки деления на ноль` |
| `update` | `Добавление функции сохранения задач в файл` |
| `final final 2` | `Добавление README и скриншота` |

Правило: сообщение коммита — это краткий ответ на вопрос «что изменил этот commit?».

## 5. Загрузка на GitHub: порядок команд

Ситуация: локальный проект готов, на GitHub создан пустой репозиторий `todo-app`:

```bash
cd todo-app
git status
git add .
git commit -m "Добавление README, скриншота и .gitignore"
git branch -M main
git remote add origin https://github.com/username/todo-app.git
git push -u origin main
```

Если репозиторий уже связан, достаточно:

```bash
git add .
git commit -m "Добавление функции редактирования"
git push
```

**Проверка:** открыть репозиторий в браузере и убедиться, что README отображается правильно, скриншот открывается, а `tasks.txt` и `__pycache__` отсутствуют.

## 6. Проверка проекта перед публикацией

**Запуск из чистой копии.** Проект должен работать не только на вашем компьютере. Склонируйте репозиторий в другую папку и запустите программу оттуда:

```bash
git clone https://github.com/username/todo-app.git test-copy
cd test-copy
python todo_app.py
```

Если программа не открывается — какой-то файл не был отправлен (push) или по ошибке попал в `.gitignore`.

**Относительные пути к файлам.** Файл данных лучше искать не в «текущей папке», а в папке, где лежит сам скрипт, — тогда программа работает, из какой бы папки её ни запустили:

```python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent
DATA_FILE = BASE_DIR / "tasks.txt"

if DATA_FILE.exists():
    print(DATA_FILE.read_text(encoding="utf-8"))
```

**Список зависимостей (`requirements.txt`).** Tkinter входит в стандартную библиотеку, его устанавливать не нужно. Если же в проекте используются внешние библиотеки (`pip install ...`), их список сохраняют в файл:

```bash
pip freeze > requirements.txt     # создать список
pip install -r requirements.txt   # установить на другом компьютере
```

## 7. Шпаргалка по Tkinter

```python
import tkinter as tk
from tkinter import ttk, messagebox

root = tk.Tk()
root.title("Шпаргалка")
root.geometry("300x200")

name_var = tk.StringVar()
tk.Label(root, text="Ваше имя:").grid(row=0, column=0, padx=5, pady=5)
tk.Entry(root, textvariable=name_var).grid(row=0, column=1)
grade = ttk.Combobox(root, values=["7", "8", "9", "10"], state="readonly")
grade.grid(row=1, column=1)
agree = tk.BooleanVar()
tk.Checkbutton(root, text="Согласен", variable=agree).grid(row=2, column=1)


def greet():
    if not name_var.get().strip():
        messagebox.showwarning("Предупреждение", "Напишите своё имя")
        return
    messagebox.showinfo("Привет", f"Привет, {name_var.get()}! Класс: {grade.get()}")


tk.Button(root, text="Отправить", command=greet).grid(row=3, column=1, pady=10)
root.bind("<Return>", lambda event: greet())
root.mainloop()
```

## Частые ошибки

**1. «rejected» при `git push`** — в репозитории на GitHub уже создан README, а в локальной истории его нет. Решение:
```bash
git pull origin main --rebase
git push
```

**2. `venv/` или `__pycache__/` попали в репозиторий.** `.gitignore` нужно создавать **до** коммита. Если всё же попали:
```bash
git rm -r --cached __pycache__
git commit -m "Удаление __pycache__"
```

**3. Картинка в README не открывается** — неверный путь или файл не отправлен (push). Путь чувствителен к регистру: `Screenshots/Main.PNG` ≠ `screenshots/main.png`.

**4. Программа не запускается на другом компьютере.** Сделайте `git clone` репозитория в другую папку и попробуйте запустить оттуда — все ли файлы на месте, относительные ли пути?

**5. Код есть, но окно не открывается** — забыт `root.mainloop()`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Портфолио | Собрание лучших работ (профиль GitHub) |
| README.md | Описание проекта, отображается на главной странице репозитория |
| `.gitignore` | Список файлов, которые Git не отслеживает |
| Сообщение коммита | Краткое описание изменения |
| `remote` / `origin` | Внешний репозиторий (на GitHub) и его имя по умолчанию |
| `git clone` | Загрузка полной копии репозитория |
| `requirements.txt` | Список внешних библиотек, нужных проекту |

## Примечание

- `.gitignore` не действует на файлы, которые уже отслеживаются, — их сначала нужно убрать из индекса командой `git rm --cached`.
- С 2021 года GitHub не принимает пароль аккаунта при push по HTTPS: нужен personal access token, SSH-ключ или Git Credential Manager.
- Ветка по умолчанию в новом репозитории GitHub — `main`, а в старых версиях Git — `master`; команда `git branch -M main` делает название единым.
- Tkinter входит в стандартную библиотеку Python, но в некоторых дистрибутивах Linux устанавливается отдельно (например, `sudo apt install python3-tk`).
- Документация GitHub по синтаксису Markdown: https://docs.github.com/en/get-started/writing-on-github
