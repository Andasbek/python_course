# Урок 86. Практические задания: безопасный код и этика данных

## Базовый уровень

**Задание 1. Проверка надёжности пароля.** Функция `parol_baga(parol)` должна давать паролю 0–5 баллов (по 1 баллу за каждое условие): длина ≥ 12; есть строчная буква; есть заглавная буква; есть цифра; есть специальный символ (`!@#$%^&*()-_+=?`). Кроме того, если пароль есть в списке `["123456", "password", "qwerty", "111111", "admin"]` или содержит `"qwerty"`/`"1234"` — 0 баллов. Функция возвращает балл и список невыполненных условий.

```text
Пароль: qwerty123
Балл: 0/5 — слишком распространённый пароль!

Пароль: Almaty2027
Балл: 3/5
Советы: должно быть не менее 12 символов; добавьте специальный символ
```

**Задание 2. Безопасный генератор паролей.** Напишите с помощью модуля `secrets` функцию `generate(uzyndyq=14)`. Пароль **обязательно** должен содержать хотя бы одну строчную букву, одну заглавную, одну цифру и один специальный символ. Подсказка: возьмите по одному символу из каждой категории, остальное заполните из общего алфавита и перемешайте через `secrets.SystemRandom().shuffle()`. Проверьте функцией из задания 1, что все 100 сгенерированных паролей получают 5/5.

**Задание 3. Аудит безопасности.** В коде «Журнала учеников» ниже есть не менее 6 проблем безопасности и этики данных. Найдите каждую и напишите, чем она опасна и как её исправить.

```python
import logging
import random
import sqlite3

DB_PASSWORD = "admin2027"
TELEGRAM_TOKEN = "7012345678:AAH-fake-token-for-lesson"

logging.basicConfig(filename="journal.log", level=logging.INFO)
conn = sqlite3.connect("journal.db")
conn.execute("CREATE TABLE IF NOT EXISTS users (login TEXT, parol TEXT, zhsn TEXT, mekenzhai TEXT)")


def tirkelu(login, parol, zhsn, mekenzhai):
    conn.execute(f"INSERT INTO users VALUES ('{login}', '{parol}', '{zhsn}', '{mekenzhai}')")
    conn.commit()
    logging.info(f"Новый пользователь: {login}, пароль: {parol}")


def kiru(login, parol):
    row = conn.execute(f"SELECT parol FROM users WHERE login = '{login}'").fetchone()
    if row is None:
        return "Такого пользователя нет"
    if row[0] != parol:
        return "Неверный пароль"
    return "Добро пожаловать!"


def kod_zhiberu():
    return random.randint(1000, 9999)
```

**Задание 4. Функции маскирования.** Напишите:
- `email_maska("aliya.seitova@mail.kz")` → `"a***a@mail.kz"` (первая и последняя буквы логина);
- `karta_maska("4400 4301 2345 6789")` → `"**** **** **** 6789"`;
- `aty_maska("Сейітова Әлия")` → `"Сейітова Ә."`.

## Продвинутый уровень

**Задание 5. Система регистрации и входа.** Консольная программа, пользователи которой хранятся в файле `users.json`:
- регистрация: логин (3–20 латинских букв/цифр), пароль (не менее 4/5 по заданию 1), повтор пароля; пароль вводится через `getpass`;
- в файле хранится не пароль, а **соль и хэш PBKDF2**;
- вход: после 3 неудачных попыток программа останавливается с сообщением «Попытки исчерпаны»; при ошибке — только сообщение «Неверный логин или пароль»;
- `users.json` добавить в `.gitignore`.

**Задание 6. Закрываем SQL-инъекцию.** Переведите функцию ниже на параметризованный запрос и напишите тест `unittest`, проверяющий, что строка атаки (`"x' OR '1'='1"`) теперь ничего не возвращает.

```python
import sqlite3


def oqushy_izdeu(conn, aty):
    sql = f"SELECT aty, synyp FROM oqushylar WHERE aty = '{aty}'"
    return conn.execute(sql).fetchall()
```

**Задание 7. Анонимизация набора данных.** С помощью Pandas подготовьте таблицу учеников к публикации:

```python
import pandas as pd

df = pd.DataFrame({
    "aty_zhoni": ["Сейітова Әлия", "Ахметов Нұрлан", "Омарова Дана", "Қасымов Ерлан"],
    "zhsn": ["100315600123", "091120500456", "100701600789", "090505500321"],
    "telefon": ["+77015551267", "+77075550011", "+77475559876", "+77015553344"],
    "zhasy": [16, 17, 16, 17],
    "ball": [87, 92, 78, 65],
})
```
Требования: заменить ФИО псевдонимами `Ученик_001`, `Ученик_002`... (таблицу соответствия сохранить отдельно в файл `kod_kestesi.csv`); полностью удалить столбец `zhsn`; замаскировать телефон; превратить возраст в группу (`"15–16"`, `"17–18"`). Результат — `anonim.csv`.

## ★ Олимпиадная / творческая задача

**Задание 8. Зачем медленный хэш?** Эксперимент на своём компьютере с хэшем, **созданным вами самими**: захэшируйте 4-значный PIN-код (например, `"4821"`) (а) простым SHA-256 и (б) PBKDF2 (100 000 повторений). Затем переберите все варианты от `0000` до `9999` и измерьте время поиска PIN через `time.perf_counter()` (для PBKDF2 измерьте время первых 50 вариантов и экстраполируйте на 10 000). Напишите вывод: почему для PIN/пароля важна длина и почему нужен медленный хэш?

**Задание 9. Поисковик секретов.** Напишите программу `secret_scan.py`, которая просматривает все файлы `.py`, `.md`, `.txt`, `.json` в заданной папке и с помощью регулярных выражений находит возможные секреты (`api_key = "..."`, `password = "..."`, `token = "..."`, строки, начинающиеся с `sk-`, hex-строки длиной 30+ символов). Для каждой найденной строки: файл, номер строки и замаскированное значение (`sk-l*****`). Проверьте свои проекты!

## Этические кейсы (групповое обсуждение, 5 минут)

Каждая группа обсуждает один кейс и отвечает на вопрос «Как правильно поступить и почему?».

1. **Найденный ключ.** В открытом GitHub-репозитории одноклассника вы увидели API-ключ банковского приложения его родителей.
2. **«Полезная» анкета.** Вы делаете анкету для школьного сайта. Заместитель директора говорит: «Спроси заодно ИИН и место работы родителей, потом пригодится».
3. **Секретное слово.** Друг говорит: «Я забыл пароль от своего Instagram, он сохранён на моём старом ноутбуке. Напиши программу и вытащи его из браузера». Ноутбук действительно его.
4. **Скрапинг.** Вы хотите написать программу, которая собирает открытые фото одноклассников из соцсетей и составляет рейтинг «Самый красивый ученик класса».

## Домашнее задание

1. Проверьте свои проекты (все репозитории на GitHub) программой из задания 9 или вручную: нет ли секретов в коде, персональных данных в README? Полон ли `.gitignore`? Исправьте найденное.
2. Включите двухфакторную аутентификацию в своих основных аккаунтах (почта, соцсети) (с помощью родителей).
3. Следующий урок — **code review**. Выберите один файл на 50–100 строк из какого-либо своего проекта за год и держите его наготове на GitHub.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
TANYMAL = ["123456", "password", "qwerty", "111111", "admin"]
ARNAIY = "!@#$%^&*()-_+=?"


def parol_baga(parol):
    """Возвращает 0..5 баллов за пароль и список невыполненных условий."""
    tomen = parol.lower()
    if tomen in TANYMAL or "qwerty" in tomen or "1234" in tomen:
        return 0, ["слишком распространённый пароль!"]
    sharttar = [
        (len(parol) >= 12, "должно быть не менее 12 символов"),
        (any(c.islower() for c in parol), "добавьте строчную букву"),
        (any(c.isupper() for c in parol), "добавьте заглавную букву"),
        (any(c.isdigit() for c in parol), "добавьте цифру"),
        (any(c in ARNAIY for c in parol), "добавьте специальный символ"),
    ]
    upai = sum(1 for oryndaldy, _ in sharttar if oryndaldy)
    kenester = [kenes for oryndaldy, kenes in sharttar if not oryndaldy]
    return upai, kenester


for p in ["qwerty123", "Almaty2027", "Tan-3-kese-shai!"]:
    upai, kenester = parol_baga(p)
    print(f"{p}: {upai}/5", "; ".join(kenester))
```
Результат: `qwerty123: 0/5 слишком распространённый пароль!`, `Almaty2027: 3/5 должно быть не менее 12 символов; добавьте специальный символ`, `Tan-3-kese-shai!: 5/5`.

**Задание 2**
```python
import secrets
import string

ARNAIY = "!@#$%^&*()-_+=?"


def generate(uzyndyq=14):
    """Создаёт безопасный пароль, содержащий хотя бы один символ каждой категории."""
    if uzyndyq < 4:
        raise ValueError("Длина должна быть не меньше 4")
    sanattar = [string.ascii_lowercase, string.ascii_uppercase, string.digits, ARNAIY]
    barlygy = "".join(sanattar)
    tanbalar = [secrets.choice(s) for s in sanattar]
    tanbalar += [secrets.choice(barlygy) for _ in range(uzyndyq - len(sanattar))]
    secrets.SystemRandom().shuffle(tanbalar)
    return "".join(tanbalar)


parol = generate()
print(parol, len(parol))
```
Проверка: `all(parol_baga(generate())[0] == 5 for _ in range(100))` → `True` (в редком случае, если в сгенерированном пароле случайно окажется «1234», может получиться 0 — хороший пример для обсуждения с учениками).

**Задание 3 — проблемы:**
1. `DB_PASSWORD`, `TELEGRAM_TOKEN` в коде — перенести в `.env`/переменную окружения.
2. Пароль хранится в открытом виде — хэш PBKDF2 с солью.
3. Пароль записывается в лог — записывать только логин.
4. SQL через f-строку — инъекция; параметризованный запрос `?`.
5. Собираются ИИН и адрес — для журнала не нужны (принцип минимизации); если нужны — с согласия, в защищённом виде.
6. Раздельные сообщения «Такого пользователя нет» / «Неверный пароль» — позволяют выяснить существующие логины; одно сообщение.
7. Код подтверждения через `random` — `secrets.randbelow`.
8. Сравнение паролей через `!=` — `hmac.compare_digest`; количество попыток входа не ограничено.
9. `journal.db`, `journal.log` нужно добавить в `.gitignore`.

**Задание 4**
```python
def email_maska(email):
    login, domen = email.split("@")
    return f"{login[0]}***{login[-1]}@{domen}"


def karta_maska(karta):
    cifr = karta.replace(" ", "")
    return "**** **** **** " + cifr[-4:]


def aty_maska(aty_zhoni):
    tegi, aty = aty_zhoni.split()
    return f"{tegi} {aty[0]}."


print(email_maska("aliya.seitova@mail.kz"))
print(karta_maska("4400 4301 2345 6789"))
print(aty_maska("Сейітова Әлия"))
```

**Задание 5**
```python
import hashlib
import hmac
import json
import re
import secrets
from getpass import getpass

FAIL = "users.json"
QAITALAU = 200_000


def zhukteu():
    try:
        with open(FAIL, encoding="utf-8") as f:
            return json.load(f)
    except FileNotFoundError:
        return {}


def saqtau(users):
    with open(FAIL, "w", encoding="utf-8") as f:
        json.dump(users, f, ensure_ascii=False, indent=2)


def hashtau(parol, tuz):
    return hashlib.pbkdf2_hmac("sha256", parol.encode(), tuz, QAITALAU).hex()


def mykty_ma(parol):
    sharttar = [len(parol) >= 12, any(c.islower() for c in parol),
                any(c.isupper() for c in parol), any(c.isdigit() for c in parol),
                any(not c.isalnum() for c in parol)]
    return sum(sharttar) >= 4


def tirkelu(users):
    login = input("Логин: ").strip()
    if not re.fullmatch(r"[A-Za-z0-9]{3,20}", login):
        print("Логин должен состоять из 3–20 латинских букв/цифр")
        return
    if login in users:
        print("Этот логин занят")
        return
    parol = getpass("Пароль: ")
    if not mykty_ma(parol):
        print("Слабый пароль")
        return
    if parol != getpass("Повторите пароль: "):
        print("Пароли не совпадают")
        return
    tuz = secrets.token_bytes(16)
    users[login] = {"tuz": tuz.hex(), "hash": hashtau(parol, tuz)}
    saqtau(users)
    print("Регистрация прошла успешно!")


def kiru(users):
    for aleket in range(3):
        login = input("Логин: ").strip()
        parol = getpass("Пароль: ")
        user = users.get(login)
        if user and hmac.compare_digest(hashtau(parol, bytes.fromhex(user["tuz"])), user["hash"]):
            print(f"Добро пожаловать, {login}!")
            return True
        print("Неверный логин или пароль")
    print("Попытки исчерпаны")
    return False


def main():
    users = zhukteu()
    tandau = input("1 — регистрация, 2 — вход: ")
    if tandau == "1":
        tirkelu(users)
    elif tandau == "2":
        kiru(users)


if __name__ == "__main__":
    main()
```

**Задание 6**
```python
import sqlite3
import unittest


def oqushy_izdeu(conn, aty):
    """Безопасно ищет ученика по имени."""
    sql = "SELECT aty, synyp FROM oqushylar WHERE aty = ?"
    return conn.execute(sql, (aty,)).fetchall()


class TestIzdeu(unittest.TestCase):
    def setUp(self):
        self.conn = sqlite3.connect(":memory:")
        self.conn.execute("CREATE TABLE oqushylar (aty TEXT, synyp TEXT)")
        self.conn.executemany("INSERT INTO oqushylar VALUES (?, ?)",
                              [("Әлия", "9А"), ("Ерлан", "10Ә")])

    def tearDown(self):
        self.conn.close()

    def test_qalypty(self):
        self.assertEqual(oqushy_izdeu(self.conn, "Әлия"), [("Әлия", "9А")])

    def test_inektsiya(self):
        self.assertEqual(oqushy_izdeu(self.conn, "x' OR '1'='1"), [])


if __name__ == "__main__":
    unittest.main()
```

**Задание 7**
```python
import pandas as pd

df = pd.DataFrame({
    "aty_zhoni": ["Сейітова Әлия", "Ахметов Нұрлан", "Омарова Дана", "Қасымов Ерлан"],
    "zhsn": ["100315600123", "091120500456", "100701600789", "090505500321"],
    "telefon": ["+77015551267", "+77075550011", "+77475559876", "+77015553344"],
    "zhasy": [16, 17, 16, 17],
    "ball": [87, 92, 78, 65],
})

df["kod"] = [f"Ученик_{i:03d}" for i in range(1, len(df) + 1)]
df[["kod", "aty_zhoni"]].to_csv("kod_kestesi.csv", index=False)   # отдельно, в защищённом месте

anonim = df.drop(columns=["aty_zhoni", "zhsn"])
anonim["telefon"] = anonim["telefon"].str[:5] + "*****" + anonim["telefon"].str[-2:]
anonim["zhas_toby"] = pd.cut(anonim["zhasy"], bins=[14, 16, 18], labels=["15–16", "17–18"])
anonim = anonim.drop(columns=["zhasy"])[["kod", "zhas_toby", "telefon", "ball"]]
anonim.to_csv("anonim.csv", index=False)
print(anonim)
```

**Задание 8**
```python
import hashlib
import time

PIN = "4821"
TUZ = b"oz-tuzym"
sha = hashlib.sha256(PIN.encode()).hexdigest()
pbk = hashlib.pbkdf2_hmac("sha256", PIN.encode(), TUZ, 100_000)

t0 = time.perf_counter()
for n in range(10000):
    nusqa = f"{n:04d}"
    if hashlib.sha256(nusqa.encode()).hexdigest() == sha:
        break
t_sha = time.perf_counter() - t0
print(f"SHA-256: PIN {nusqa} найден, {t_sha:.4f} с")

t0 = time.perf_counter()
for n in range(50):
    hashlib.pbkdf2_hmac("sha256", f"{n:04d}".encode(), TUZ, 100_000)
bir = (time.perf_counter() - t0) / 50
print(f"PBKDF2: одна проверка ≈ {bir * 1000:.1f} мс, все 10 000 вариантов ≈ {bir * 10000:.0f} с")
```
Вывод: SHA-256 находит 4-значный PIN за миллисекунды; PBKDF2 замедляет одну проверку в тысячи раз. Но 10 000 вариантов — всё равно мало, поэтому нужны длинный пароль и ограничение попыток входа.

**Задание 9**
```python
import os
import re
import sys

ULGILER = [
    re.compile(r"(api[_-]?key|password|parol|token|secret)\s*[=:]\s*['\"]([^'\"]{6,})['\"]", re.I),
    re.compile(r"()(sk-[A-Za-z0-9_-]{8,})"),
    re.compile(r"()\b([0-9a-f]{32,})\b"),
]
KENEITU = (".py", ".md", ".txt", ".json")


def maska(mani):
    return mani[:4] + "*" * 5


def skaner(papka):
    tabyldy = 0
    for tubir, papkalar, faildar in os.walk(papka):
        papkalar[:] = [p for p in papkalar if p not in ("venv", ".git", "__pycache__")]
        for fail in faildar:
            if not fail.endswith(KENEITU):
                continue
            path = os.path.join(tubir, fail)
            with open(path, encoding="utf-8", errors="ignore") as f:
                for nomir, zhol in enumerate(f, start=1):
                    for ulgi in ULGILER:
                        m = ulgi.search(zhol)
                        if m:
                            print(f"{path}:{nomir}: {maska(m.group(2))}")
                            tabyldy += 1
                            break
    print("Найдено возможных секретов:", tabyldy)


skaner(sys.argv[1] if len(sys.argv) > 1 else ".")
```

**Этические кейсы — направления обсуждения:**
1. Не использовать ключ, никому не показывать; сообщить однокласснику лично, объяснить, что ключ нужно немедленно заменить и убрать из репозитория.
2. Принцип минимизации: не собирать данные, не нужные для цели анкеты; вежливо объяснить причину.
3. Только на собственном устройстве владельца, в его присутствии и через официальную функцию браузера «Сохранённые пароли»; писать такую программу для чужого устройства — незаконно и опасно.
4. Даже если фото «открытые», такой рейтинг — использование персональных данных людей без их согласия в другой цели и дискриминация; может привести к кибербуллингу. Не делать.

</details>
