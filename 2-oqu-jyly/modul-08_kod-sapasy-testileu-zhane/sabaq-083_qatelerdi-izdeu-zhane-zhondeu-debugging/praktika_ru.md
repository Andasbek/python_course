# Урок 83. Практические задания: «Охотник за ошибками» — debugging и logging

В каждом задании: (1) **воспроизведите** ошибку, (2) запишите **гипотезу**, (3) **проверьте** её (print / debugger / logging), (4) **исправьте**. Над исправленной строкой напишите `# ИСПРАВЛЕНИЕ: ...` — что изменилось.

## Базовый уровень

**Задание 1. Прочитай Traceback.** Программа вывела такое сообщение:

```text
Traceback (most recent call last):
  File "dukenn.py", line 18, in <module>
    esep = tusim_esepte(sebet)
  File "dukenn.py", line 11, in tusim_esepte
    zhalpy += tauar_bagasy(atau) * sany
  File "dukenn.py", line 5, in tauar_bagasy
    return bagalar[atau]
KeyError: 'яблоки'
```

Ответьте: а) каковы тип и причина ошибки? б) в какой именно функции и на какой строке произошла ошибка? в) каков порядок вызова функций? г) предложите два способа исправить ошибку.

**Задание 2. Синтаксические ошибки.** В коде ниже 5 синтаксических ошибок. Найдите их и напишите исправленный код (сначала запустите код и прочитайте, что скажет Python).

```text
def synyp_ortasha(bagalar)
    if len(bagalar) == 0:
        return 0
    zhalpy = 0
    for b in bagalar:
        zhalpy += b
    return zhalpy / len(bagalar

bagalar = [5, 4, 5, 3]
print("Средняя оценка: , synyp_ortasha(bagalar))
if synyp_ortasha(bagalar) = 5:
    print("Отлично!")
  print("Готово")
```

**Задание 3. Логические ошибки.** Программа работает без ошибок, но результат неверный. Функция должна находить самую высокую и самую низкую температуру, а также количество морозных дней (ниже 0 °C). Ожидаемые ответы:
```text
Макс: -2, Мин: -18, Морозных дней: 7
Макс: 4, Мин: -3, Морозных дней: 2
```
Найдите 3 ошибки (подсказка: поставьте в VS Code точку останова внутри `for` и следите за переменными).

```python
def temp_esep(temp):
    maks = 0
    mini = 0
    ayazdy = 0
    for i in range(1, len(temp)):
        t = temp[i]
        if t > maks:
            maks = t
        if t < mini:
            mini = t
        if t <= 0:
            ayazdy += 1
    print(f"Макс: {maks}, Мин: {mini}, Морозных дней: {ayazdy}")


temp_esep([-18, -12, -4, -15, -2, -9, -7])
temp_esep([4, -3, 0, 2, -1])
```

**Задание 4. Ошибка типа.** Программа, вычисляющая общую сумму корзины, выдаёт `TypeError`, а после «быстрого исправления» может выдать неверную сумму или новую ошибку. С помощью `print(f"{x!r}")` найдите причину и исправьте. Ожидаемый ответ: `Итого: 2150 тенге`.

```python
sebet = [
    {"atau": "Хлеб", "baga": "250", "sany": 2},
    {"atau": "Молоко", "baga": "480", "sany": 1},
    {"atau": "Яблоки", "baga": "1170", "sany": "1"},
]

zhalpy = 0
for tauar in sebet:
    zhalpy += tauar["baga"] * tauar["sany"]
print(f"Итого: {zhalpy} тенге")
```

## Продвинутый уровень

**Задание 5. Добавление логирования.** Программа ниже обрабатывает строки с баллами учеников. Добавьте в неё `logging`:
- `INFO` — обработка началась/завершилась, сколько строк обработано успешно;
- `WARNING` — повреждённая строка (балл не число или вне диапазона 0..100) — с номером строки;
- `ERROR` — формат строки полностью неверный (нет запятой);
- логи записываются в файл `ballar.log` в кодировке UTF-8, со временем. Программа **не должна останавливаться** на ошибочной строке, а переходить к следующей.

```python
zholdar = [
    "Әлия,87",
    "Нұрлан,92",
    "Дана,девяносто",
    "Ерлан 75",
    "Аружан,105",
    "Санжар,68",
]

ballar = {}
for zhol in zholdar:
    aty, ball = zhol.split(",")
    ballar[aty] = int(ball)
print(ballar)
```

**Задание 6. Метод деления пополам.** Функция должна возвращать список простых чисел от 1 до `n`, но в ответе есть ошибка: `jai_sandar(20)` → ожидается `[2, 3, 5, 7, 11, 13, 17, 19]`. Найдите ошибку с помощью `print` или отладчика и исправьте. Какое число оказывается лишним и почему?

```python
def jai_ma(san):
    if san < 2:
        return False
    for d in range(2, int(san ** 0.5)):
        if san % d == 0:
            return False
    return True


def jai_sandar(n):
    return [s for s in range(1, n + 1) if jai_ma(s)]


print(jai_sandar(20))
```

**Задание 7. Предположения через `assert`.** Напишите `assert`-ы для исправленной функции `jai_ma` из задания 6: ожидаемые значения для `jai_ma(2)`, `jai_ma(9)`, `jai_ma(25)`, `jai_ma(29)`, `jai_ma(1)`. Если все `assert` пройдены — вывести «Все проверки пройдены». (Это первый шаг к тестам следующего урока.)

## ★ Олимпиадная / творческая задача

**Задание 8. Бесконечный цикл.** Функция двоичного (бинарного) поиска на некоторых значениях **не останавливается**. С помощью отладчика проследите за переменными `sol`, `on`, `orta`, найдите причину и исправьте. Проверка: поиск значений 1, 7, 11 и 4 (отсутствует) в `[1, 3, 5, 7, 9, 11]`.

```python
def ekilik_izdeu(tizim, izdeu):
    sol = 0
    on = len(tizim) - 1
    while sol <= on:
        orta = (sol + on) // 2
        if tizim[orta] == izdeu:
            return orta
        elif tizim[orta] < izdeu:
            sol = orta
        else:
            on = orta - 1
    return -1
```

**Задание 9. Анализатор логов.** Напишите программу `log_taldau.py`, которая по файлу `ballar.log` из задания 5 (или любому лог-файлу) подсчитывает, сколько раз встречается каждый уровень (`INFO`, `WARNING`, `ERROR`), и отдельно выводит все строки `ERROR`. ★★ Покажите результат столбчатой диаграммой Matplotlib.

## Домашнее задание

1. Добавьте `logging` в свой аналитический проект (урок 81): чтение данных и шаги очистки (сколько строк удалено) записываются с уровнем `INFO`/`WARNING` в файл `analysis.log`. Добавьте `analysis.log` в `.gitignore`.
2. Запишите одну ошибку, встретившуюся вам на этой неделе, в «Дневник ошибок»: признак → гипотеза → как нашёл → как исправил.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1.** а) `KeyError: 'яблоки'` — в словаре `bagalar` нет ключа `'яблоки'` (возможно, в словаре «Яблоки» записано с заглавной буквы или его нет вовсе). б) В функции `tauar_bagasy`, в строке 5. в) `<module>` (строка 18) → `tusim_esepte` (строка 11) → `tauar_bagasy` (строка 5). г) 1) привести ключ к единому виду: `bagalar[atau.capitalize()]`; 2) `bagalar.get(atau)` + запись предупреждения в лог, если ключа нет; 3) `try/except KeyError`.

**Задание 2** — 5 ошибок: в конце `def` нет `:`; не закрыта скобка `len(bagalar`; не закрыта кавычка `"Средняя оценка: `; вместо `if ... = 5` нужно `==`; неверный отступ у `print("Готово")`.
```python
def synyp_ortasha(bagalar):
    if len(bagalar) == 0:
        return 0
    zhalpy = 0
    for b in bagalar:
        zhalpy += b
    return zhalpy / len(bagalar)


bagalar = [5, 4, 5, 3]
print("Средняя оценка:", synyp_ortasha(bagalar))
if synyp_ortasha(bagalar) == 5:
    print("Отлично!")
print("Готово")
```

**Задание 3** — 3 ошибки: (1) `range(1, ...)` пропускает первый элемент (в первом наборе теряется −18, во втором — 4); (2) начальные значения `maks = 0`, `mini = 0` — если все температуры отрицательные, максимум ошибочно получится 0 (нужно начинать с первого элемента списка); (3) `t <= 0` — 0 °C не мороз, должно быть `t < 0`.
Ошибочный код выводит: `Макс: 0, Мин: -15, Морозных дней: 6` и `Макс: 2, Мин: -3, Морозных дней: 3`.
```python
def temp_esep(temp):
    maks = temp[0]
    mini = temp[0]
    ayazdy = 0
    for t in temp:
        if t > maks:
            maks = t
        if t < mini:
            mini = t
        if t < 0:
            ayazdy += 1
    print(f"Макс: {maks}, Мин: {mini}, Морозных дней: {ayazdy}")


temp_esep([-18, -12, -4, -15, -2, -9, -7])
temp_esep([4, -3, 0, 2, -1])
```
Педагогу: при проверке на одном наборе данных некоторые ошибки могут «уравновешивать» друг друга и оставаться скрытыми — поэтому даны два разных набора. Это мостик к идее «тестирования граничных случаев» на следующем уроке.

**Задание 4.** `baga` — текст (`'250'`), поэтому `"250" * 2` → `'250250'` (повторение строки!), а `0 + '250250'` → `TypeError`. Если ученик «исправит» только на `zhalpy = ""`, сумма станет бессмысленной строкой вроде `'250250480...'`; а если преобразует в число только `baga`, то `1170 * "1"` повторит `'1'` 1170 раз и снова будет `TypeError`. Правильное исправление — преобразовать в число оба поля:
```python
sebet = [
    {"atau": "Хлеб", "baga": "250", "sany": 2},
    {"atau": "Молоко", "baga": "480", "sany": 1},
    {"atau": "Яблоки", "baga": "1170", "sany": "1"},
]

zhalpy = 0
for tauar in sebet:
    print(f"{tauar['baga']!r} {tauar['sany']!r}")     # во время отладки
    zhalpy += int(tauar["baga"]) * int(tauar["sany"])
print(f"Итого: {zhalpy} тенге")
```
Ответ: 250·2 + 480 + 1170 = 2150.

**Задание 5**
```python
import logging

logging.basicConfig(
    filename="ballar.log",
    encoding="utf-8",
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(message)s",
)

zholdar = [
    "Әлия,87",
    "Нұрлан,92",
    "Дана,девяносто",
    "Ерлан 75",
    "Аружан,105",
    "Санжар,68",
]

logging.info("Обработка начата: %d строк", len(zholdar))
ballar = {}
for nomir, zhol in enumerate(zholdar, start=1):
    if "," not in zhol:
        logging.error("Строка %d: неверный формат (нет запятой): %r", nomir, zhol)
        continue
    aty, ball_str = zhol.split(",", 1)
    try:
        ball = int(ball_str)
    except ValueError:
        logging.warning("Строка %d: балл не является числом: %r", nomir, ball_str)
        continue
    if not 0 <= ball <= 100:
        logging.warning("Строка %d: балл вне диапазона: %d", nomir, ball)
        continue
    ballar[aty] = ball

logging.info("Обработка завершена: успешно %d строк", len(ballar))
print(ballar)
```
Результат: `{'Әлия': 87, 'Нұрлан': 92, 'Санжар': 68}`; в логе 2 WARNING, 1 ERROR, 2 INFO.

**Задание 6.** `range(2, int(san ** 0.5))` — верхняя граница не включается, поэтому для `san = 4` получается `range(2, 2)` — пусто → 4 оказывается «простым числом»; то же с 6, 8 (для них тоже `range(2, 2)`), а также с 9, 15, 25 и т.д. Ошибочный код выводит `[2, 3, 4, 5, 6, 7, 8, 9, 11, 13, 15, 17, 19]` — лишние 4, 6, 8, 9 и 15. Исправление — `+ 1`:
```python
def jai_ma(san):
    if san < 2:
        return False
    for d in range(2, int(san ** 0.5) + 1):
        if san % d == 0:
            return False
    return True


def jai_sandar(n):
    return [s for s in range(1, n + 1) if jai_ma(s)]


print(jai_sandar(20))
```

**Задание 7**
```python
def jai_ma(san):
    if san < 2:
        return False
    for d in range(2, int(san ** 0.5) + 1):
        if san % d == 0:
            return False
    return True


assert jai_ma(2) is True, "2 — простое число"
assert jai_ma(9) is False, "9 = 3·3"
assert jai_ma(25) is False, "25 = 5·5"
assert jai_ma(29) is True, "29 — простое число"
assert jai_ma(1) is False, "1 не является простым числом"
print("Все проверки пройдены")
```

**Задание 8.** `sol = orta` — когда `sol` и `on` соседние (`sol=4, on=5`), `orta = 4`, и `sol` снова становится 4 → бесконечный цикл (например, при поиске 11 или 4). Исправление: `sol = orta + 1`.
```python
def ekilik_izdeu(tizim, izdeu):
    sol = 0
    on = len(tizim) - 1
    while sol <= on:
        orta = (sol + on) // 2
        if tizim[orta] == izdeu:
            return orta
        elif tizim[orta] < izdeu:
            sol = orta + 1
        else:
            on = orta - 1
    return -1


t = [1, 3, 5, 7, 9, 11]
print(ekilik_izdeu(t, 1), ekilik_izdeu(t, 7), ekilik_izdeu(t, 11), ekilik_izdeu(t, 4))
```
Результат: `0 3 5 -1`.

**Задание 9**
```python
from collections import Counter

sanau = Counter()
qateler = []
try:
    with open("ballar.log", encoding="utf-8") as f:
        for zhol in f:
            for dengei in ("DEBUG", "INFO", "WARNING", "ERROR", "CRITICAL"):
                if f"[{dengei}]" in zhol:
                    sanau[dengei] += 1
                    if dengei == "ERROR":
                        qateler.append(zhol.strip())
except FileNotFoundError:
    print("Лог-файл не найден — сначала запустите задание 5")

for dengei, san in sanau.most_common():
    print(f"{dengei:<9} {san}")
print("Строки ERROR:")
for q in qateler:
    print(" ", q)
```

</details>
