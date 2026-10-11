# Урок 6. Практические задания: GitHub, push, pull, портфолио

## Базовый уровень

### Задание 1. Подготовка аккаунта
1. Войди на github.com (если аккаунта нет — зарегистрируйся с помощью педагога).
2. Поставь аватар в профиле (своё фото не обязательно — можно рисунок, логотип), заполни поля **Name** и **Bio** (например: «Учащийся кружка Python, Алматы»).
3. Включи Settings → Emails → «Keep my email addresses private». Скопируй выданную почту `noreply` и укажи её в Git:
```bash
git config --global user.email "ID+username@users.noreply.github.com"
```
4. (Рекомендуется) Settings → Password and authentication → включи двухфакторную аутентификацию.

### Задание 2. Загрузка калькулятора на GitHub
Загрузи репозиторий `calculator` с прошлого урока:
1. Создай на GitHub **пустой** репозиторий `calculator` (Public, без README).
2. В терминале:
```bash
git remote add origin https://github.com/USERNAME/calculator.git
git branch -M main
git push -u origin main
```
3. Обнови страницу GitHub и проверь файлы и количество коммитов.
4. Проверь, что папки `.venv` нет на GitHub.

### Задание 3. Написание README.md
Напиши `README.md` для проекта калькулятора (в VS Code `Ctrl+Shift+V` — предпросмотр). Обязательные разделы:
- название проекта (`#`);
- описание в 1–2 предложения;
- «Возможности» — список;
- «Запуск» — с блоком кода;
- «Автор».

Закоммить и сделай push: `git add README.md`, `git commit -m "Добавлен README"`, `git push`. Проверь, что README красиво отображается на GitHub.

### Задание 4. Изменение в браузере → pull
1. На сайте GitHub открой файл README.md → ✏️ (Edit) → добавь в раздел «Автор» строку «Кружок Python, 2026» → **Commit changes**.
2. Проверь, что на локальном компьютере этой строки в README ещё нет.
3. Выполни `git pull` — появилась ли строка?
4. `git log --oneline` — виден ли коммит, сделанный в браузере?

### Задание 5. Clone
1. Перейди в другую папку (например, `Desktop`).
2. Клонируй репозиторий `calculator` соседа: `git clone https://github.com/СОСЕД/calculator.git sosed-calculator`.
3. Попробуй запустить. Поставь ⭐ репозиторию соседа.

## Продвинутый уровень

### Задание 6. Профильный README (страница портфолио)
1. Создай на GitHub репозиторий, название которого **совпадает** с твоим именем пользователя (например, `aigerim-dev/aigerim-dev`), с отметкой «Add a README file».
2. Напиши в README: 2–3 предложения о себе (без личных данных), «Что я изучаю», таблицу «Мои проекты» (со ссылкой на калькулятор), «Мои цели на этот год».
3. Открой страницу своего профиля и проверь результат.

### Задание 7. Сценарий «Два компьютера»
Выполни в паре (или на одном компьютере с двумя папками):
1. Учащийся A создаёт репозиторий `shared-notes` и приглашает учащегося B через **Settings → Collaborators**.
2. B клонирует репозиторий, записывает в файл `ideas.md` 3 идеи проектов и делает push.
3. A выполняет `git pull`, видит идеи B, добавляет свои и делает push.
4. B снова делает `git pull`.
5. Дополнительно: одновременно измените **разные** файлы и сделайте push — первый пройдёт, второй получит `rejected`. Решите это через `git pull`, затем `git push`.

### Задание 8. Добавление старых проектов в портфолио
Преврати свой лучший проект 1-го года (итоговый проект, рисунок turtle, игру) в новый репозиторий: `.gitignore`, README (со скриншотом), минимум 3 коммита, push на GitHub. Добавь его в таблицу «Мои проекты» в профильном README.

## ★ Олимпиадная / творческая задача

### Задание 9. Генератор README
Напиши программу на Python: она запрашивает название проекта, описание, список возможностей (до пустой строки), команду запуска и автора и создаёт готовый файл `README.md`. Дополнительно: если в папке есть `requirements.txt`, автоматически добавь в раздел «Установка» команду `pip install -r requirements.txt`.

### Задание 10. GitHub API — статистика профиля
Открытый API GitHub работает без ключа: `https://api.github.com/users/USERNAME`. С помощью стандартных модулей `urllib.request` и `json` напиши программу, выводящую логин пользователя, количество публичных репозиториев, количество подписчиков и дату регистрации. (Интернет и запросы подробно изучим в VI модуле — это предварительная «дегустация».)

```text
Пользователь: aigerim-dev
Публичных репозиториев: 3
Подписчиков: 5
Зарегистрирован: 2026-09-14
```

## Домашнее задание

1. Доработай профильный README: о себе, цели, таблица проектов.
2. В портфолио должно быть минимум 2 репозитория (калькулятор + один проект 1-го года), в каждом — README.
3. Отправь ссылку на профиль педагогу (она будет добавлена в список кружка).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задания 2–4 (команды)**
```bash
git remote add origin https://github.com/USERNAME/calculator.git
git remote -v
git branch -M main
git push -u origin main

git add README.md
git commit -m "Добавлен README"
git push

git pull
git log --oneline
```

**Задание 3 — образец README** приведён в разделе 7 теории.

**Задание 7** — при `rejected`:
```bash
git pull
git push
```
Если изменены разные файлы, Git автоматически объединит изменения (может быть создан merge-коммит; если откроется редактор — сохранить и закрыть).

**Задание 9**
```python
import os

FENCE = "`" * 3  # обозначение блока кода в Markdown


def ask_list(prompt):
    """Собирает список, пока не введена пустая строка."""
    items = []
    print(prompt + " (Enter — завершить):")
    while True:
        item = input("- ").strip()
        if not item:
            break
        items.append(item)
    return items


def build_readme(title, description, features, run_cmd, author, has_req):
    """Составляет текст README.md."""
    lines = [f"# {title}", "", description, "", "## Возможности"]
    lines += [f"- {feature}" for feature in features]
    if has_req:
        lines += ["", "## Установка", FENCE + "bash", "pip install -r requirements.txt", FENCE]
    lines += ["", "## Запуск", FENCE + "bash", run_cmd, FENCE, "", "## Автор", author, ""]
    return "\n".join(lines)


def main():
    title = input("Название проекта: ")
    description = input("Описание: ")
    features = ask_list("Возможности")
    run_cmd = input("Команда запуска: ")
    author = input("Автор: ")
    has_req = os.path.exists("requirements.txt")
    text = build_readme(title, description, features, run_cmd, author, has_req)
    with open("README.md", "w", encoding="utf-8") as file:
        file.write(text)
    print("README.md создан!")


if __name__ == "__main__":
    main()
```

**Задание 10**
```python
import json
import urllib.request


def get_user(username):
    """Возвращает данные пользователя из GitHub API в виде словаря."""
    url = f"https://api.github.com/users/{username}"
    request = urllib.request.Request(url, headers={"User-Agent": "python-club"})
    with urllib.request.urlopen(request, timeout=10) as response:
        return json.loads(response.read().decode("utf-8"))


def main():
    username = input("Имя пользователя GitHub: ").strip()
    try:
        user = get_user(username)
    except Exception as error:
        print("Не удалось получить данные:", error)
        return
    print("Пользователь:", user["login"])
    print("Публичных репозиториев:", user["public_repos"])
    print("Подписчиков:", user["followers"])
    print("Зарегистрирован:", user["created_at"][:10])


if __name__ == "__main__":
    main()
```

</details>
