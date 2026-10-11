# Урок 85. Практические задания: документирование проекта

## Базовый уровень

**Задание 1. Разминка в Markdown.** Создайте файл `markdown_test.md` и оформите следующий текст элементами Markdown: заголовок 1-го уровня, два заголовка 2-го уровня, маркированный и нумерованный списки, одно жирное слово, одно слово `кода`, ссылка, блок кода (`bash`), таблица из 3 строк. Проверьте результат в VS Code с помощью `Ctrl+Shift+V`.

```text
Конвертер валют
Программа переводит тенге в доллары и евро. Курс берётся с сайта open.er-api.com.
Возможности: получение курса, перевод суммы, сохранение истории.
Установка: клонировать репозиторий, создать venv, выполнить pip install -r requirements.txt.
Команда запуска: python converter.py
Валюты: USD — доллар США, EUR — евро, RUB — российский рубль.
```

**Задание 2. Исправь плохой README.** Ниже README вашего одноклассника. Выпишите не менее 6 недостатков, затем полностью перепишите README (проект — приложение Tkinter «To-Do List» с урока 32, данные хранятся в файле `tasks.json`).

```text
# project1

это мой проект. откройте app2_final_NEW.py. если не работает напишите мне.
нужно: python, tkinter, json, pandas
![](C:\Users\Erlan\Desktop\screen.png)
api_key = "sk-12345abcdef"
```

**Задание 3. Написание docstring.** Напишите для функций ниже полные docstring (описание, `Args`, `Returns`, при необходимости `Raises`). Проверьте через `help(funktsiya)`.

```python
def bmi(salmaq, boiy):
    if boiy <= 0:
        raise ValueError("Рост должен быть положительным")
    return round(salmaq / boiy ** 2, 1)


def bmi_sanaty(index):
    if index < 18.5:
        return "Недостаточный вес"
    if index < 25:
        return "Норма"
    if index < 30:
        return "Избыточный вес"
    return "Ожирение"


class Oqushy:
    def __init__(self, aty, synyp):
        self.aty = aty
        self.synyp = synyp
        self.bagalar = []

    def baga_qosu(self, baga):
        if baga not in (2, 3, 4, 5):
            raise ValueError("Оценка должна быть 2..5")
        self.bagalar.append(baga)

    def ortasha(self):
        return sum(self.bagalar) / len(self.bagalar) if self.bagalar else 0
```

**Задание 4. `requirements.txt`.** В новой папке:
1. создайте и активируйте venv;
2. выполните `pip install pandas matplotlib requests`;
3. выполните `pip freeze > requirements.txt`, откройте файл — сколько пакетов записано? Почему не 3?
4. создайте второй venv (`venv2`), установите в него пакеты через `pip install -r requirements.txt` и проверьте командой `python -c "import pandas, matplotlib, requests; print('OK')"`.

## Продвинутый уровень

**Задание 5. README своего проекта.** Выберите свой лучший проект за год (ООП, GUI, игра, SQLite, API или анализ). Напишите полный README.md по образцу и чек-листу из теории: описание, скриншот, возможности, требования, установка, запуск, структура проекта, тестирование, автор. Добавьте `requirements.txt`.

**Задание 6. Руководство пользователя.** Напишите к тому же проекту `docs/user_guide.md`: введение, запуск, не менее 3 сценариев («Как ...?») с нумерованными шагами и скриншотами, FAQ не менее чем из 3 вопросов. Представьте, что читатель — ваш 10-летний родственник или родители.

**Задание 7. Тест «чужими глазами».** Обменяйтесь с одноклассником ссылками на репозитории. Только по README (не задавая вопросов автору!) склонируйте, установите и запустите его проект. Заполните таблицу:

| Вопрос | Да/Нет | Примечание |
|---|---|---|
| Из первого абзаца понял, что делает проект | | |
| Команды установки сработали без изменений | | |
| Сразу нашёл, какой файл нужно запускать | | |
| Скриншот/изображение отображается | | |
| Смог выполнить один сценарий по руководству | | |
| Самый трудный момент: | | |

## ★ Олимпиадная / творческая задача

**Задание 8. Автоматический генератор документации.** Используя модуль `inspect`, напишите программу `docgen.py`, которая находит все функции и классы (с методами) любого модуля и создаёт из их docstring Markdown-документ `API.md`. Функции без docstring помечаются как «⚠ не задокументировано». Примените её к модулю из задания 3.

**Задание 9. Проверка README.** Напишите программу `readme_check.py`, которая читает файл README.md и проверяет наличие обязательных разделов (`Установка`, `Запуск`, `Требования`, `Автор`), хотя бы одного изображения (`![`) и одного блока кода, а также отсутствие абсолютных путей (`C:\`, `/Users/`). Результат — в виде чек-листа (✓/✗) и общий балл.

## Домашнее задание

1. Завершите задания 5–6 и загрузите на GitHub (`git commit -m "README, руководство пользователя и requirements.txt"`).
2. Доработайте README по отзыву одноклассника из задания 7.
3. На главной странице своего профиля GitHub (репозиторий `<логин>/<логин>` → README.md) напишите о себе «портфолио-визитку» из 5–7 строк: кто вы, какие проекты сделали (со ссылками).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1** — образец:
````markdown
# Конвертер валют

Программа переводит **тенге** в доллары и евро. Курс берётся с сайта [open.er-api.com](https://open.er-api.com).

## Возможности
- получение курса;
- перевод суммы;
- сохранение истории.

## Установка
1. Склонируйте репозиторий.
2. Создайте venv.
3. Установите зависимости:

```bash
pip install -r requirements.txt
python converter.py
```

| Код | Валюта |
|---|---|
| USD | Доллар США |
| EUR | Евро |
| RUB | Российский рубль |

Команда запуска: `python converter.py`
````

**Задание 2 — недостатки:** (1) бессмысленное название `project1`; (2) не сказано, что делает проект; (3) непонятное имя файла (`app2_final_NEW.py`) — файл нужно переименовать в `main.py`; (4) нет команд установки/запуска; (5) `tkinter`, `json` — стандартная библиотека, в `requirements` не записываются; `pandas`, если в проекте не используется, — лишний; (6) абсолютный путь Windows к изображению — на GitHub не отображается; (7) **API-ключ опубликован открыто** — самая опасная ошибка, ключ нужно немедленно заменить (отозвать); (8) контакт «напишите мне» — кому? Нет имени автора; (9) нет структуры (заголовков, списков). Переписанный README — по образцу из теории.

**Задание 3**
```python
def bmi(salmaq, boiy):
    """Вычисляет индекс массы тела (ИМТ, BMI).

    Args:
        salmaq (float): масса тела, кг.
        boiy (float): рост, метры (например, 1.65).

    Returns:
        float: значение ИМТ, округлённое до одного знака после запятой.

    Raises:
        ValueError: если рост равен 0 или отрицателен.
    """
    if boiy <= 0:
        raise ValueError("Рост должен быть положительным")
    return round(salmaq / boiy ** 2, 1)


def bmi_sanaty(index):
    """Возвращает категорию ВОЗ по значению ИМТ.

    Args:
        index (float): значение ИМТ.

    Returns:
        str: «Недостаточный вес», «Норма», «Избыточный вес» или «Ожирение».
    """
    if index < 18.5:
        return "Недостаточный вес"
    if index < 25:
        return "Норма"
    if index < 30:
        return "Избыточный вес"
    return "Ожирение"


class Oqushy:
    """Ученик и его оценки.

    Attributes:
        aty (str): имя ученика.
        synyp (str): класс, например «9А».
        bagalar (list[int]): полученные оценки (2..5).
    """

    def __init__(self, aty, synyp):
        """Создаёт нового ученика с пустым списком оценок."""
        self.aty = aty
        self.synyp = synyp
        self.bagalar = []

    def baga_qosu(self, baga):
        """Добавляет ученику оценку.

        Args:
            baga (int): 2, 3, 4 или 5.

        Raises:
            ValueError: если оценка не в диапазоне 2..5.
        """
        if baga not in (2, 3, 4, 5):
            raise ValueError("Оценка должна быть 2..5")
        self.bagalar.append(baga)

    def ortasha(self):
        """Возвращает среднюю оценку; если оценок нет — 0."""
        return sum(self.bagalar) / len(self.bagalar) if self.bagalar else 0


help(bmi)
```

**Задание 4.** Ответ: `pip freeze` покажет примерно 15–20 пакетов (numpy, python-dateutil, pytz, tzdata, contourpy, cycler, fonttools, kiwisolver, packaging, pillow, pyparsing, certifi, charset-normalizer, idna, urllib3, six и др.), потому что pandas, matplotlib и requests сами зависят от других пакетов — `pip` устанавливает и их.

**Задания 5–7** — оцениваются по чек-листу из 10 пунктов из теории (каждый пункт — 10%).

**Задание 8**
```python
import inspect


def modul_qujaty(modul):
    """Составляет Markdown-текст из функций и классов модуля."""
    zholdar = [f"# Документация модуля `{modul.__name__}`", ""]
    if modul.__doc__:
        zholdar += [inspect.cleandoc(modul.__doc__), ""]
    for atau, obj in inspect.getmembers(modul):
        if atau.startswith("_"):
            continue
        if inspect.isfunction(obj) and obj.__module__ == modul.__name__:
            zholdar += sipattama(f"## Функция `{atau}{inspect.signature(obj)}`", obj)
        elif inspect.isclass(obj) and obj.__module__ == modul.__name__:
            zholdar += sipattama(f"## Класс `{atau}`", obj)
            for a_atau, adis in inspect.getmembers(obj, inspect.isfunction):
                if not a_atau.startswith("_") or a_atau == "__init__":
                    zholdar += sipattama(f"### Метод `{a_atau}{inspect.signature(adis)}`", adis)
    return "\n".join(zholdar)


def sipattama(taqyryp, obj):
    """Возвращает заголовок и docstring одного объекта."""
    doc = inspect.getdoc(obj)
    return [taqyryp, "", doc if doc else "⚠ не задокументировано", ""]


if __name__ == "__main__":
    import json            # стандартный модуль для примера; импортируйте свой модуль
    with open("API.md", "w", encoding="utf-8") as f:
        f.write(modul_qujaty(json))
    print("API.md создан")
```

**Задание 9**
```python
import re


def readme_tekseru(path="README.md"):
    """Проверяет файл README.md на наличие обязательных элементов."""
    try:
        with open(path, encoding="utf-8") as f:
            matin = f.read()
    except FileNotFoundError:
        print("README.md не найден")
        return 0

    tekseruler = {
        "Раздел «Требования»": re.search(r"^##.*Требован", matin, re.M),
        "Раздел «Установка»": re.search(r"^##.*Установк", matin, re.M),
        "Раздел «Запуск»": re.search(r"^##.*Запуск", matin, re.M),
        "Раздел «Автор»": re.search(r"^##.*Автор", matin, re.M),
        "Хотя бы одно изображение": "![" in matin,
        "Хотя бы один блок кода": "`" * 3 in matin,     # три обратные кавычки
        "Нет абсолютных путей": not re.search(r"[A-Za-z]:\\|/Users/|/home/", matin),
    }
    ball = 0
    for atau, natije in tekseruler.items():
        belgi = "✓" if natije else "✗"
        ball += 1 if natije else 0
        print(f"{belgi} {atau}")
    print(f"Результат: {ball}/{len(tekseruler)}")
    return ball


readme_tekseru()
```

</details>
