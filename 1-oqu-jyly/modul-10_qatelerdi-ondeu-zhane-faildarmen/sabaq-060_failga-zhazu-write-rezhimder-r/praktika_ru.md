# Урок 60. Практические задания: запись в файл, режимы

## Базовый уровень

**Задание 1. Визитная карточка.** Спроси у пользователя имя и фамилию, возраст, город и хобби и запиши их в файл `card.txt`, каждое значение – в отдельной строке (режим `w`). Затем прочитай файл и выведи на экран.

```text
Твоё имя: Ерасыл
Твой возраст: 13
Твой город: Алматы
Твоё хобби: футбол
--- содержимое card.txt ---
Имя: Ерасыл
Возраст: 13
Город: Алматы
Хобби: футбол
```

**Задание 2. Таблица умножения в файле.** Запиши таблицу умножения введённого пользователем числа от 1 до 10 в файл `table_N.txt` (N – число). Например, если введено 7 – `table_7.txt`.

```text
7 x 1 = 7
7 x 2 = 14
...
7 x 10 = 70
```

**Задание 3. Эксперимент с `w` и `a`.** Программа записывает в файл `log.txt` строку «Программа запущена». Запусти программу 3 раза в режиме `w`, а затем 3 раза в режиме `a`. Каждый раз читай и выводи содержимое файла. Запиши разницу в тетрадь.

**Задание 4. Дневник.** Пользователь вводит одну запись, программа добавляет её в конец файла `diary.txt` (режим `a`), а затем выводит все записи дневника с номерами.

```text
Запись на сегодня: Научился записывать в файл на Python
--- Мой дневник ---
1. Получил 5 по английскому
2. Научился записывать в файл на Python
```

**Задание 5. Список покупок.** Список: `["хлеб", "молоко", "яйца", "яблоки"]`. Запиши его тремя способами в три файла: `write()` + цикл (`list1.txt`), `writelines()` (`list2.txt`), `print(file=f)` (`list3.txt`). Все три файла должны получиться одинаковыми.

## Продвинутый уровень

**Задание 6. Сортировщик чётных и нечётных.** Создай файл `numbers.txt` (в каждой строке одно число, не менее 10 чисел). Программа читает его, записывает чётные числа в `even.txt`, нечётные – в `odd.txt`, а на экран выводит, сколько чисел в каждом файле. Строки, которые не превращаются в число, пропускай.

**Задание 7. Таблица рекордов.** Программа спрашивает имя игрока и его баллы и добавляет их в файл `scores.txt` в виде `имя;баллы`. Затем читает все результаты из файла, сортирует их по баллам в порядке убывания и выводит ТОП-3 игроков.

```text
Твоё имя: Дана
Твои баллы: 340
=== ТОП-3 ===
1. Дана – 340
2. Арман – 310
3. Айгерим – 250
```

**Задание 8. Телефонная книга.** Программа с меню: `1 – добавить контакт`, `2 – показать все`, `3 – поиск`, `0 – выход`. Контакты хранятся в файле `contacts.txt` в виде `имя;номер`. Контакты должны сохраняться, даже если программу закрыть и открыть снова. Если файла нет, программа не должна падать.

## ★ Олимпиадная / творческая задача

**Задание 9. Замена слова в файле.** Пользователь вводит имя файла, старое слово и новое слово. Программа заменяет все старые слова в файле на новое, сохраняет файл **под тем же именем** и сообщает, сколько замен сделано. (Помни: сначала полностью прочитать, закрыть, затем `w`!) Сохрани копию оригинала как `имя_файла.bak`.

**Задание 10. HTML-визитка.** Запиши данные из задания 1 в файл `card.html` в виде простого HTML (`<h1>` – имя, `<p>` – остальное). Открой файл в браузере! Это первый шаг к веб-темам следующего года.

## Домашнее задание

1. Программа «Список целей»: пользователь вводит 3 цели на эту неделю, программа записывает их в файл `goals.txt`. При повторном открытии программы сначала должны выводиться прежние цели.
2. Составь таблицу: для каждого из режимов `r`, `w`, `a` напиши, что происходит, «если файл есть» и «если файла нет».
3. Подготовься к практикуму на следующем уроке: повтори, что мы проходили о списках и словарях на уроках 41 и 49.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.**

```python
name = input("Твоё имя: ")
age = input("Твой возраст: ")
city = input("Твой город: ")
hobby = input("Твоё хобби: ")
with open("card.txt", "w", encoding="utf-8") as f:
    f.write(f"Имя: {name}\n")
    f.write(f"Возраст: {age}\n")
    f.write(f"Город: {city}\n")
    f.write(f"Хобби: {hobby}\n")
print("--- содержимое card.txt ---")
with open("card.txt", encoding="utf-8") as f:
    print(f.read(), end="")
```

**Задание 2.**

```python
n = int(input("Число: "))
file_name = f"table_{n}.txt"
with open(file_name, "w", encoding="utf-8") as f:
    for i in range(1, 11):
        print(f"{n} x {i} = {n * i}", file=f)
print("Файл", file_name, "создан.")
```

**Задание 3.** В режиме `w` в файле всегда остаётся одна строка, в режиме `a` при каждом запуске добавляется ещё одна строка.

```python
mode = "a"   # измени на "w" и проведи эксперимент
with open("log.txt", mode, encoding="utf-8") as f:
    f.write("Программа запущена\n")
with open("log.txt", encoding="utf-8") as f:
    print(f.read())
```

**Задание 4.**

```python
entry = input("Запись на сегодня: ")
with open("diary.txt", "a", encoding="utf-8") as f:
    f.write(entry + "\n")
print("--- Мой дневник ---")
with open("diary.txt", encoding="utf-8") as f:
    number = 1
    for line in f:
        print(f"{number}. {line.strip()}")
        number += 1
```

**Задание 5.**

```python
items = ["хлеб", "молоко", "яйца", "яблоки"]
with open("list1.txt", "w", encoding="utf-8") as f:
    for item in items:
        f.write(item + "\n")
with open("list2.txt", "w", encoding="utf-8") as f:
    f.writelines([item + "\n" for item in items])
with open("list3.txt", "w", encoding="utf-8") as f:
    for item in items:
        print(item, file=f)
```

**Задание 6.**

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
    print("numbers.txt не найден")

with open("even.txt", "w", encoding="utf-8") as f:
    for n in evens:
        print(n, file=f)
with open("odd.txt", "w", encoding="utf-8") as f:
    for n in odds:
        print(n, file=f)
print("Чётных чисел:", len(evens), "| Нечётных чисел:", len(odds))
```

**Задание 7.**

```python
name = input("Твоё имя: ")
points = int(input("Твои баллы: "))
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

**Задание 8.**

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
    choice = input("1-добавить, 2-все, 3-поиск, 0-выход: ")
    if choice == "1":
        name = input("Имя: ")
        phone = input("Номер: ")
        contacts[name] = phone
        save_contacts(contacts)
        print("Сохранено.")
    elif choice == "2":
        if not contacts:
            print("Книга пуста.")
        for name, phone in contacts.items():
            print(f"{name}: {phone}")
    elif choice == "3":
        name = input("Кого ищем? ")
        print(contacts.get(name, "Не найдено"))
    elif choice == "0":
        print("Пока!")
        break
    else:
        print("Выбери пункт меню.")
```

**Задание 9.**

```python
file_name = input("Имя файла: ")
old = input("Старое слово: ")
new = input("Новое слово: ")
try:
    with open(file_name, encoding="utf-8") as f:
        text = f.read()
except FileNotFoundError:
    print("Файл не найден.")
else:
    count = text.count(old)
    with open(file_name + ".bak", "w", encoding="utf-8") as f:
        f.write(text)
    with open(file_name, "w", encoding="utf-8") as f:
        f.write(text.replace(old, new))
    print("Количество замен:", count)
```

**Задание 10.**

```python
name = input("Твоё имя: ")
city = input("Твой город: ")
hobby = input("Твоё хобби: ")
with open("card.html", "w", encoding="utf-8") as f:
    print("<!DOCTYPE html>", file=f)
    print('<html><head><meta charset="utf-8"><title>Визитка</title></head><body>', file=f)
    print(f"<h1>{name}</h1>", file=f)
    print(f"<p>Город: {city}</p>", file=f)
    print(f"<p>Хобби: {hobby}</p>", file=f)
    print("</body></html>", file=f)
print("card.html создан – открой его в браузере!")
```

</details>
