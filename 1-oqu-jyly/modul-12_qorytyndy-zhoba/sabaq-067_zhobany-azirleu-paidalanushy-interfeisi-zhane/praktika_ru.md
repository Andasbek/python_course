# Урок 67. Практические задания: интерфейс и хранение данных

> Сегодня ты добавишь в свой проект **меню**, **проверку ввода** и **сохранение в файл** и объединишь все части через `main()`.

## Базовый уровень (обязательно)

**Шаг 1. Меню.** Напиши функции `show_menu()` и `main()`. В меню должно быть не менее 3 пунктов и «0 – Выход». При неверном выборе программа не должна падать, а должна спрашивать снова.

**Шаг 2. Вспомогательные функции.** Добавь в проект нужные функции ввода: `read_int(prompt, low, high)`, `read_choice(prompt, options)`, `read_text(prompt)`. Замени ими все строки `int(input(...))` в программе.

**Шаг 3. Файл данных.** Вынеси данные, записанные на 66-м уроке внутри кода (вопросы, слова, товары), в текстовый файл и читай их функцией `load_...()`. Если файла нет, программа не должна падать.

**Шаг 4. Сохранение результата.** Напиши функцию `save_...()`, которая записывает результат проекта (баллы, операцию, задание, состояние) в файл. Закрой программу, открой снова и проверь – сохранились ли данные?

**Шаг 5. Оформление.** Заголовок (`center`), разделительные линии, выравнивание, понятные сообщения («Сохранено!», «Не найдено»).

## Продвинутый уровень

**Шаг 6. Изменение данных.** Дай возможность **изменять/удалять** данные (например, отметить задание как «выполнено», удалить вопрос) – шаблон «загрузить → изменить → сохранить через `w`».

**Шаг 7. Экран статистики.** Выведи итоги по сохранённым данным: ТОП-5, среднее значение, всего, самое частое и т.д.

**Шаг 8. «Пользовательский тест».** Дай соседу попробовать твою программу **без каких-либо объяснений**. Запиши, где он запнулся, о чём спросил, – и улучши эти места.

## ★ Олимпиадная / творческая задача

**9. Файл настроек.** Создай файл `settings.txt` (например, `level=2`, `sound=да`, `questions=10`). При запуске программа читает настройки в словарь, в меню их можно изменять.

**10. Turtle-дополнение.** Покажи результат проекта графически: например, после викторины нарисуй с помощью `turtle` медаль по баллам (золотой/серебряный/бронзовый круг) или столбчатую диаграмму результатов.

## Карточки-помощники

**А. Выбор задания из меню (по номеру):**

```python
tasks = ["Математика: №125", "Английский: слова", "Python: проект"]
for i in range(len(tasks)):
    print(f"{i + 1}. {tasks[i]}")
num = input("Какое задание удалить? ")
if num.isdigit() and 1 <= int(num) <= len(tasks):
    removed = tasks.pop(int(num) - 1)
    print("Удалено:", removed)
else:
    print("Такого номера нет.")
```

**Б. Чтение настроек в виде `ключ=значение`:**

```python
settings = {"level": "1", "questions": "5"}     # значения по умолчанию
try:
    with open("settings.txt", encoding="utf-8") as f:
        for line in f:
            if "=" in line:
                key, value = line.strip().split("=", 1)
                settings[key] = value
except FileNotFoundError:
    pass
print(settings)
```

**В. Сохранение и чтение состояния тамагочи в одну строку:**

```python
pet = {"name": "Котик", "hunger": 40, "mood": 55}
with open("pet.txt", "w", encoding="utf-8") as f:
    f.write(f"{pet['name']};{pet['hunger']};{pet['mood']}\n")

with open("pet.txt", encoding="utf-8") as f:
    name, hunger, mood = f.readline().strip().split(";")
pet = {"name": name, "hunger": int(hunger), "mood": int(mood)}
print(pet)
```

## Домашнее задание

1. Доведи проект до состояния, когда все его части объединены через `main()` и программа работает от начала до конца.
2. Заполни лист прогресса.
3. К следующему уроку: придумай 10 разных действий, которыми можно попытаться «сломать» твою программу (неверный ввод, пустой файл и т.д.) – это подготовка к тестированию.

## Критерии оценивания

| Критерий | Описание | Баллы |
|---|---|---|
| Правильная работа программы | Меню работает, данные сохраняются и загружаются | 40 |
| Эффективность алгоритма | Логика / интерфейс / хранение разделены, используются вспомогательные функции | 25 |
| Читаемость и оформление кода | Понятные сообщения, оформление, комментарии | 20 |
| Самостоятельная работа по плану | План выполнен, лист прогресса заполнен | 15 |
| **Итого** | | **100** |

## Для педагога: решения

<details><summary>Показать решения</summary>

**Демо-проект «Викторина о Казахстане» – полная версия на конец урока 67.**

`questions.txt`:

```text
# Викторина о Казахстане: вопрос|ответ
Какой город – столица Казахстана?|астана
Национальная валюта Казахстана?|тенге
В каком году Казахстан провозгласил независимость?|1991
Балхаш – это озеро или река?|озеро
Высокогорный каток в Алматы?|медеу
Автор романа-эпопеи «Путь Абая»?|мухтар ауэзов
В какой области находится космодром Байконур?|кызылординская
Какая птица изображена на флаге Казахстана?|беркут
```

`quiz.py`:

```python
"""Викторина о Казахстане – демо-версия итогового проекта (урок 67)."""
import random
from datetime import datetime

QUESTIONS_FILE = "questions.txt"
RESULTS_FILE = "results.txt"
LINE = "=" * 40


# ---------- Хранение данных ----------

def load_questions(file_name):
    """Читает вопросы из файла: в каждой строке 'вопрос|ответ'."""
    questions = {}
    try:
        with open(file_name, encoding="utf-8") as f:
            for line in f:
                line = line.strip()
                if line == "" or line.startswith("#") or "|" not in line:
                    continue
                question, answer = line.split("|", 1)
                questions[question.strip()] = answer.strip()
    except FileNotFoundError:
        print(f"Предупреждение: {file_name} не найден.")
    return questions


def add_question(file_name, question, answer):
    """Добавляет новый вопрос в конец файла."""
    with open(file_name, "a", encoding="utf-8") as f:
        f.write(f"{question}|{answer}\n")


def save_result(file_name, name, score, total):
    """Добавляет результат в файл: имя;баллы;всего;дата."""
    stamp = datetime.now().strftime("%d.%m.%Y %H:%M")
    with open(file_name, "a", encoding="utf-8") as f:
        f.write(f"{name};{score};{total};{stamp}\n")


def load_results(file_name):
    """Возвращает результаты в виде списка [процент, имя, баллы, всего, дата]."""
    results = []
    try:
        with open(file_name, encoding="utf-8") as f:
            for line in f:
                parts = line.strip().split(";")
                if len(parts) != 4:
                    continue
                try:
                    score = int(parts[1])
                    total = int(parts[2])
                except ValueError:
                    continue
                percent = round(score / total * 100)
                results.append([percent, parts[0], score, total, parts[3]])
    except FileNotFoundError:
        pass
    return results


# ---------- Основная логика ----------

def check_answer(user_answer, correct_answer):
    """Возвращает True, если ответ верный."""
    return user_answer.strip().lower() == correct_answer.strip().lower()


def grade(score, total):
    """Переводит баллы в оценку."""
    percent = score / total * 100
    if percent >= 85:
        return "5 – отлично!"
    elif percent >= 65:
        return "4 – хорошо"
    elif percent >= 40:
        return "3 – неплохо"
    return "2 – готовься ещё"


# ---------- Интерфейс ----------

def read_text(prompt):
    """Спрашивает, пока не будет введена непустая строка."""
    while True:
        text = input(prompt).strip()
        if text:
            return text
        print("Не должно быть пусто!")


def read_choice(prompt, options):
    """Принимает только одно из разрешённых значений."""
    while True:
        choice = input(prompt).strip()
        if choice in options:
            return choice
        print("Возможные варианты:", ", ".join(options))


def show_menu():
    print()
    print(LINE)
    print("ВИКТОРИНА О КАЗАХСТАНЕ".center(40))
    print(LINE)
    print("  1 – Играть")
    print("  2 – Рекорды (ТОП-5)")
    print("  3 – Добавить новый вопрос")
    print("  0 – Выход")
    print(LINE)


def play(questions):
    """Игра: задаёт вопросы в случайном порядке, возвращает баллы."""
    items = list(questions.items())
    random.shuffle(items)
    score = 0
    for i in range(len(items)):
        question, answer = items[i]
        user = input(f"\n[{i + 1}/{len(items)}] {question}\n> ")
        if check_answer(user, answer):
            print("✔ Верно!")
            score += 1
        else:
            print("✘ Неверно. Правильный ответ:", answer)
    return score


def show_records(results):
    if not results:
        print("Результатов пока нет. Сыграй первым!")
        return
    results.sort(reverse=True)
    print(f"{'№':<3}{'Имя':<12}{'Баллы':>6}{'%':>6}  Дата")
    place = 1
    for percent, name, score, total, stamp in results[:5]:
        print(f"{place:<3}{name:<12}{str(score) + '/' + str(total):>6}{percent:>5}%  {stamp}")
        place += 1


def main():
    while True:
        show_menu()
        choice = read_choice("Твой выбор: ", ["1", "2", "3", "0"])
        if choice == "1":
            questions = load_questions(QUESTIONS_FILE)
            if not questions:
                print("Вопросов нет. Сначала добавь вопрос (3).")
                continue
            name = read_text("Твоё имя: ")
            score = play(questions)
            total = len(questions)
            print(f"\n{name}, твой результат: {score}/{total}. Оценка: {grade(score, total)}")
            save_result(RESULTS_FILE, name, score, total)
            print("Результат сохранён.")
        elif choice == "2":
            show_records(load_results(RESULTS_FILE))
        elif choice == "3":
            question = read_text("Вопрос: ")
            answer = read_text("Правильный ответ: ")
            add_question(QUESTIONS_FILE, question, answer)
            print("Вопрос добавлен!")
        else:
            print("Пока! До новых встреч!")
            break


main()
```

`results.txt` (после нескольких игр):

```text
Дана;7;8;05.05.2027 14:20
Арман;6;8;05.05.2027 14:35
```

**Задание 9 (настройки) – фрагмент изменения в меню:**

```python
def save_settings(file_name, settings):
    with open(file_name, "w", encoding="utf-8") as f:
        for key, value in settings.items():
            f.write(f"{key}={value}\n")


settings = {"level": "1", "questions": "5"}
key = input("Какую настройку изменить? ")
if key in settings:
    settings[key] = input("Новое значение: ")
    save_settings("settings.txt", settings)
    print("Сохранено.")
else:
    print("Такой настройки нет.")
```

**Задание 10 (медаль):**

```python
import turtle


def draw_medal(percent):
    if percent >= 85:
        color = "gold"
    elif percent >= 65:
        color = "silver"
    else:
        color = "peru"
    t = turtle.Turtle()
    t.hideturtle()
    t.penup()
    t.goto(0, -80)
    t.pendown()
    t.fillcolor(color)
    t.begin_fill()
    t.circle(80)
    t.end_fill()
    t.penup()
    t.goto(0, -15)
    t.write(f"{percent}%", align="center", font=("Arial", 24, "bold"))
    turtle.done()


draw_medal(88)
```

**Советы педагогу:**
- В первой половине урока проверь, что у всех учеников работает меню; во второй – файлы.
- Слабым ученикам покажи функции `load_questions` / `save_result` демо-проекта как образец и помоги адаптировать их к своим данным.
- После «пользовательского теста» каждый ученик должен внести хотя бы одно улучшение.

</details>
