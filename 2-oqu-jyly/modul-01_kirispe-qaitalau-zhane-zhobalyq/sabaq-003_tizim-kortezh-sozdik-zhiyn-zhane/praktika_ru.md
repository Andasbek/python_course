# Урок 3. Практические задания: структуры данных, функции, структурирование кода

## Базовый уровень

### Задание 1. «Список покупок»
Создай пустой список. Пользователь вводит названия товаров, пока не напишет «стоп». Программа:
- не добавляет повторяющийся товар второй раз (сообщает «... уже есть в списке»);
- в конце выводит список с нумерацией по алфавиту.

```text
Товар: хлеб
Товар: молоко
Товар: хлеб
хлеб уже есть в списке
Товар: стоп
1. молоко
2. хлеб
```

### Задание 2. «Города и население»
Создай словарь: 5 городов Казахстана и их население (тыс. человек): Алматы — 2230, Астана — 1420, Шымкент — 1220, Караганда — 500, Актобе — 590. Программа:
1. спрашивает название города и выводит его население (если города нет — «Нет данных», через `get`);
2. выводит города с населением больше 1 миллиона;
3. вычисляет общее население всех городов.

### Задание 3. «Общие кружки»
Задай два множества: учащиеся шахматного кружка и кружка Python. Выведи:
- тех, кто ходит в оба кружка;
- тех, кто ходит только в один из них (`^` — симметрическая разность);
- общее количество уникальных учащихся.

### Задание 4. «Набор функций»
Напиши три функции и проверь их внутри `main()`:
- `is_even(n)` — `True`, если число чётное;
- `min_max(numbers)` — возвращает наименьший и наибольший элементы списка в виде кортежа;
- `celsius_to_fahrenheit(c)` — `F = C * 9 / 5 + 32`.

```text
is_even(10) → True
min_max([4, 9, 1, 7]) → (1, 9)
celsius_to_fahrenheit(25) → 77.0
```

## Продвинутый уровень

### Задание 5. «Классный журнал» (структурирование кода)
Перепиши приведённый ниже неструктурированный код, разделив его на функции: `add_grade`, `student_average`, `class_average`, `print_journal`, `main`. В функциях вычислений не должно быть `print`.

```python
journal = {}
journal["Алия"] = [5, 4]
journal["Бекзат"] = [3, 4, 4]
journal["Алия"].append(5)
for name in journal:
    s = 0
    for g in journal[name]:
        s += g
    print(name, round(s / len(journal[name]), 2))
total = 0
count = 0
for name in journal:
    for g in journal[name]:
        total += g
        count += 1
print("По классу:", round(total / count, 2))
```

Ожидаемый результат:
```text
Алия        4.67
Бекзат      3.67
По классу: 4.17
```

### Задание 6. «Разделение на модули»
Перенеси функции задания 5 в файл `journal_utils.py`, а в файле `main.py` используй их через `import`. При прямом запуске файла `journal_utils.py` должен выполняться небольшой тест, а при импорте — нет (`if __name__ == "__main__":`).

### Задание 7. «Частота слов»
Введи текст. Функция `word_frequency(text)` должна привести слова к нижнему регистру, удалить знаки препинания и вернуть словарь частот. Функция `top_words(freq, n)` должна вернуть `n` самых частых слов в виде списка кортежей `(слово, количество)`.

```text
Текст: Книга — друг. Книга учит, книга — знание!
книга: 3
друг: 1
учит: 1
```

## ★ Олимпиадная / творческая задача

### Задание 8. «Группы анаграмм»
Дан список слов. Объедини в группы слова, являющиеся анаграммами друг друга (состоящие из одних и тех же букв). Подсказка: в качестве ключа словаря используй кортеж или строку из отсортированных букв.

```text
Вход: ["листок", "столик", "кот", "ток", "слиток", "школа"]
Выход:
листок, столик, слиток
кот, ток
школа
```

### Задание 9. «Мини-склад»
Программа для склада магазина: словарь `{товар: [количество, цена]}`. Меню: 1 — добавить товар, 2 — продать (количество уменьшается, при нехватке — предупреждение), 3 — список склада, 4 — общая стоимость склада, 0 — выход. Каждый пункт меню — отдельная функция.

## Домашнее задание

1. Заверши задание 9 (или доработай начатое задание) — программа должна состоять минимум из 4 функций и `main()`.
2. К следующему уроку: попробуй установить VS Code на домашний компьютер (code.visualstudio.com) и проверь версию Python (`python --version`).

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
items = []
while True:
    item = input("Товар: ").strip().lower()
    if item == "стоп":
        break
    if item in items:
        print(item, "уже есть в списке")
    else:
        items.append(item)

for i, item in enumerate(sorted(items), start=1):
    print(f"{i}. {item}")
```

**Задание 2**
```python
population = {
    "Алматы": 2230,
    "Астана": 1420,
    "Шымкент": 1220,
    "Караганда": 500,
    "Актобе": 590,
}

city = input("Город: ").strip()
print(population.get(city, "Нет данных"))

print("Города с населением больше миллиона:")
for name, people in population.items():
    if people > 1000:
        print("-", name)

print("Общее население:", sum(population.values()), "тыс. человек")
```

**Задание 3**
```python
chess = {"Айдар", "Мадина", "Тимур", "Асель"}
python = {"Мадина", "Ерлан", "Тимур", "Дана"}

print("В обоих кружках:", chess & python)
print("Только в одном:", chess ^ python)
print("Всего учащихся:", len(chess | python))
```

**Задание 4**
```python
def is_even(n):
    """Возвращает True, если число чётное."""
    return n % 2 == 0


def min_max(numbers):
    """Возвращает наименьший и наибольший элементы списка в виде кортежа."""
    return min(numbers), max(numbers)


def celsius_to_fahrenheit(c):
    """Переводит градусы Цельсия в градусы Фаренгейта."""
    return c * 9 / 5 + 32


def main():
    print("is_even(10) →", is_even(10))
    print("min_max([4, 9, 1, 7]) →", min_max([4, 9, 1, 7]))
    print("celsius_to_fahrenheit(25) →", celsius_to_fahrenheit(25))


if __name__ == "__main__":
    main()
```

**Задание 5**
```python
def add_grade(journal, name, grade):
    """Добавляет оценку ученику (если ученика нет — создаёт)."""
    journal.setdefault(name, []).append(grade)


def student_average(grades):
    """Возвращает средний балл одного ученика."""
    return sum(grades) / len(grades) if grades else 0


def class_average(journal):
    """Возвращает среднее значение всех оценок класса."""
    all_grades = [g for grades in journal.values() for g in grades]
    return student_average(all_grades)


def print_journal(journal):
    """Выводит журнал в виде таблицы."""
    for name, grades in journal.items():
        print(f"{name:<12}{student_average(grades):.2f}")
    print("По классу:", round(class_average(journal), 2))


def main():
    journal = {}
    add_grade(journal, "Алия", 5)
    add_grade(journal, "Алия", 4)
    add_grade(journal, "Бекзат", 3)
    add_grade(journal, "Бекзат", 4)
    add_grade(journal, "Бекзат", 4)
    add_grade(journal, "Алия", 5)
    print_journal(journal)


if __name__ == "__main__":
    main()
```

**Задание 6** — в `journal_utils.py` функции задания 5 и в конце:
```python
if __name__ == "__main__":
    test = {"А": [5, 5], "Б": [3]}
    print(class_average(test))  # 4.333...
```
`main.py`:
```python
from journal_utils import add_grade, print_journal


def main():
    journal = {}
    for name, grade in [("Алия", 5), ("Алия", 4), ("Бекзат", 3)]:
        add_grade(journal, name, grade)
    print_journal(journal)


if __name__ == "__main__":
    main()
```

**Задание 7**
```python
PUNCTUATION = ".,!?;:—-«»\"'()"


def word_frequency(text):
    """Возвращает словарь частот слов."""
    freq = {}
    for word in text.lower().split():
        word = word.strip(PUNCTUATION)
        if word:
            freq[word] = freq.get(word, 0) + 1
    return freq


def top_words(freq, n):
    """Возвращает n самых частых слов в виде кортежей (слово, количество)."""
    return sorted(freq.items(), key=lambda pair: pair[1], reverse=True)[:n]


def main():
    text = input("Текст: ")
    for word, count in top_words(word_frequency(text), 3):
        print(f"{word}: {count}")


if __name__ == "__main__":
    main()
```

**Задание 8**
```python
def group_anagrams(words):
    """Объединяет анаграммы в группы."""
    groups = {}
    for word in words:
        key = "".join(sorted(word))
        groups.setdefault(key, []).append(word)
    return list(groups.values())


words = ["листок", "столик", "кот", "ток", "слиток", "школа"]
for group in group_anagrams(words):
    print(", ".join(group))
```

**Задание 9**
```python
def add_item(store):
    name = input("Товар: ").strip()
    count = int(input("Количество: "))
    price = int(input("Цена: "))
    if name in store:
        store[name][0] += count
        store[name][1] = price
    else:
        store[name] = [count, price]


def sell_item(store):
    name = input("Товар: ").strip()
    if name not in store:
        print("Такого товара нет")
        return
    count = int(input("Количество: "))
    if count > store[name][0]:
        print("На складе недостаточно! Есть:", store[name][0])
    else:
        store[name][0] -= count
        print("Продано на сумму:", count * store[name][1], "₸")


def show_store(store):
    for name, (count, price) in store.items():
        print(f"{name:<15}{count:>5} шт. {price:>8} ₸")


def store_value(store):
    return sum(count * price for count, price in store.values())


def main():
    store = {"тетрадь": [100, 150], "ручка": [200, 120]}
    while True:
        choice = input("1-добавить 2-продать 3-список 4-стоимость 0-выход: ")
        if choice == "1":
            add_item(store)
        elif choice == "2":
            sell_item(store)
        elif choice == "3":
            show_store(store)
        elif choice == "4":
            print("Стоимость склада:", store_value(store), "₸")
        elif choice == "0":
            break


if __name__ == "__main__":
    main()
```

</details>
