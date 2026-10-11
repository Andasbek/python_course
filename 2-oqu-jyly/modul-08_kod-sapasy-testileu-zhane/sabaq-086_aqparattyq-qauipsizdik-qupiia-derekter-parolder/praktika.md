# Сабақ 86. Практикалық тапсырмалар: қауіпсіз код және деректер этикасы

## Базалық деңгей

**1-тапсырма. Пароль мықтылығын тексеру.** `parol_baga(parol)` функциясы парольге 0–5 ұпай берсін (әр шартқа 1 ұпай): ұзындығы ≥ 12; кіші әріп бар; бас әріп бар; цифр бар; арнайы таңба бар (`!@#$%^&*()-_+=?`). Сонымен қатар, пароль `["123456", "password", "qwerty", "111111", "admin"]` тізімінде болса немесе ішінде `"qwerty"`/`"1234"` болса — 0 ұпай. Функция ұпай мен жетіспейтін шарттар тізімін қайтарсын.

```text
Пароль: qwerty123
Ұпай: 0/5 — тым танымал пароль!

Пароль: Almaty2027
Ұпай: 3/5
Кеңестер: кемінде 12 таңба болсын; арнайы таңба қосыңыз
```

**2-тапсырма. Қауіпсіз пароль генераторы.** `secrets` модулімен `generate(uzyndyq=14)` функциясын жазыңыз. Пароль **міндетті түрде** кемінде бір кіші әріп, бір бас әріп, бір цифр және бір арнайы таңбадан тұрсын. Кеңес: әр санаттан бір таңба алып, қалғанын жалпы алфавиттен толтырып, `secrets.SystemRandom().shuffle()` арқылы араластырыңыз. 1-тапсырмадағы функциямен 100 генерацияланған парольдің бәрі 5/5 алатынын тексеріңіз.

**3-тапсырма. Қауіпсіздік аудиті.** Төмендегі «Оқушылар журналы» кодында кемінде 6 қауіпсіздік және деректер этикасы мәселесі бар. Әрқайсысын тауып, неге қауіпті екенін және қалай түзету керегін жазыңыз.

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
    logging.info(f"Жаңа пайдаланушы: {login}, пароль: {parol}")


def kiru(login, parol):
    row = conn.execute(f"SELECT parol FROM users WHERE login = '{login}'").fetchone()
    if row is None:
        return "Мұндай пайдаланушы жоқ"
    if row[0] != parol:
        return "Пароль қате"
    return "Қош келдіңіз!"


def kod_zhiberu():
    return random.randint(1000, 9999)
```

**4-тапсырма. Маскалау функциялары.** Жазыңыз:
- `email_maska("aliya.seitova@mail.kz")` → `"a***a@mail.kz"` (логиннің бірінші және соңғы әрпі);
- `karta_maska("4400 4301 2345 6789")` → `"**** **** **** 6789"`;
- `aty_maska("Сейітова Әлия")` → `"Сейітова Ә."`.

## Кеңейтілген деңгей

**5-тапсырма. Тіркелу және кіру жүйесі.** Пайдаланушылар `users.json` файлында сақталатын консольдық бағдарлама:
- тіркелу: логин (3–20 латын әріп/цифр), пароль (1-тапсырма бойынша кемінде 4/5), парольді қайталау; пароль `getpass` арқылы енгізіледі;
- файлда пароль емес, **тұз бен PBKDF2 хэші** сақталады;
- кіру: 3 сәтсіз әрекеттен кейін «Әрекеттер саны бітті» деп тоқтайды; қате кезінде тек «Логин немесе пароль қате» хабары;
- `users.json` `.gitignore`-ға қосылсын.

**6-тапсырма. SQL инъекциясын жабу.** Төмендегі функцияны параметрлі сұранысқа көшіріңіз және шабуыл жолы (`"x' OR '1'='1"`) енді ештеңе қайтармайтынын тексеретін `unittest` тестін жазыңыз.

```python
import sqlite3


def oqushy_izdeu(conn, aty):
    sql = f"SELECT aty, synyp FROM oqushylar WHERE aty = '{aty}'"
    return conn.execute(sql).fetchall()
```

**7-тапсырма. Деректер жиынын анонимдеу.** Pandas арқылы оқушылар кестесін жариялауға дайындаңыз:

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
Талаптар: аты-жөнді `Оқушы_001`, `Оқушы_002`... псевдонимдерімен ауыстыру (сәйкестік кестесін бөлек `kod_kestesi.csv` файлына сақтау); `zhsn` бағанын толық жою; телефонды маскалау; жасты топқа айналдыру (`"15–16"`, `"17–18"`). Нәтиже — `anonim.csv`.

## ★ Олимпиадалық / шығармашылық тапсырма

**8-тапсырма. Неге баяу хэш?** Өз компьютеріңізде **өзіңіз жасаған** хэшпен тәжірибе: 4 цифрлы PIN-кодты (мысалы, `"4821"`) (а) қарапайым SHA-256 және (ә) PBKDF2 (100 000 қайталау) арқылы хэштеңіз. Содан кейін `0000`-ден `9999`-ға дейінгі барлық нұсқаны тексеріп, PIN-ді табуға кеткен уақытты `time.perf_counter()` арқылы өлшеңіз (PBKDF2 үшін алғашқы 50 нұсқаның уақытын өлшеп, 10 000-ға экстраполяциялаңыз). Қорытынды жазыңыз: неге PIN-ге/парольге ұзындық маңызды және неге баяу хэш қажет?

**9-тапсырма. Құпия іздегіш.** Берілген папкадағы барлық `.py`, `.md`, `.txt`, `.json` файлдарын қарап, ықтимал құпияларды (`api_key = "..."`, `password = "..."`, `token = "..."`, `sk-` деп басталатын жолдар, 30+ таңбалы hex жолдар) регулярлы өрнектер арқылы табатын `secret_scan.py` бағдарламасын жазыңыз. Табылған әр жол үшін: файл, жол нөмірі және маскаланған мән (`sk-l*****`). Өз жобаларыңызды тексеріңіз!

## Этикалық кейстер (топтық талқылау, 5 минут)

Әр топ бір кейсті талқылап, «Не істеу дұрыс және неге?» деген сұраққа жауап береді.

1. **Табылған кілт.** Сіз сыныптасыңыздың ашық GitHub репозиторийінде оның ата-анасының банк қосымшасының API кілтін көрдіңіз.
2. **«Пайдалы» сауалнама.** Мектеп сайтына сауалнама жасайсыз. Директордың орынбасары «ата-аналардың ЖСН-і мен жұмыс орнын да сұрай қой, кейін керек болар» дейді.
3. **Құпия сөз.** Досыңыз «Менің Instagram паролім ұмытылды, ол ескі ноутбугімде сақталған. Бір бағдарлама жазып, браузерден шығарып бер» дейді. Ноутбук — шынымен соның өзінікі.
4. **Скрапинг.** Сіз сыныптастардың әлеуметтік желідегі ашық фотоларын жинап, «Сыныптың ең әдемі оқушысы» рейтингін жасайтын бағдарлама жазғыңыз келеді.

## Үй тапсырмасы

1. Өз жобаларыңызды (GitHub-тағы барлық репозиторийлер) 9-тапсырмадағы немесе қолмен тексеріңіз: кодта құпия, README-де жеке дерек жоқ па? `.gitignore` толық па? Табылғанын түзетіңіз.
2. Өзіңіздің негізгі аккаунттарыңызда (пошта, әлеуметтік желі) екі факторлы аутентификацияны қосыңыз (ата-анаңыздың көмегімен).
3. Келесі сабақ — **code review**. Жыл ішіндегі бір жобаңыздан 50–100 жолдық бір файлды таңдап, GitHub-та дайын ұстаңыз.

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
TANYMAL = ["123456", "password", "qwerty", "111111", "admin"]
ARNAIY = "!@#$%^&*()-_+=?"


def parol_baga(parol):
    """Парольге 0..5 ұпай және жетіспейтін шарттар тізімін қайтарады."""
    tomen = parol.lower()
    if tomen in TANYMAL or "qwerty" in tomen or "1234" in tomen:
        return 0, ["тым танымал пароль!"]
    sharttar = [
        (len(parol) >= 12, "кемінде 12 таңба болсын"),
        (any(c.islower() for c in parol), "кіші әріп қосыңыз"),
        (any(c.isupper() for c in parol), "бас әріп қосыңыз"),
        (any(c.isdigit() for c in parol), "цифр қосыңыз"),
        (any(c in ARNAIY for c in parol), "арнайы таңба қосыңыз"),
    ]
    upai = sum(1 for oryndaldy, _ in sharttar if oryndaldy)
    kenester = [kenes for oryndaldy, kenes in sharttar if not oryndaldy]
    return upai, kenester


for p in ["qwerty123", "Almaty2027", "Tan-3-kese-shai!"]:
    upai, kenester = parol_baga(p)
    print(f"{p}: {upai}/5", "; ".join(kenester))
```
Нәтижесі: `qwerty123: 0/5 тым танымал пароль!`, `Almaty2027: 3/5 кемінде 12 таңба болсын; арнайы таңба қосыңыз`, `Tan-3-kese-shai!: 5/5`.

**2-тапсырма**
```python
import secrets
import string

ARNAIY = "!@#$%^&*()-_+=?"


def generate(uzyndyq=14):
    """Барлық санаттан кемінде бір таңбасы бар қауіпсіз пароль жасайды."""
    if uzyndyq < 4:
        raise ValueError("Ұзындық кемінде 4 болуы керек")
    sanattar = [string.ascii_lowercase, string.ascii_uppercase, string.digits, ARNAIY]
    barlygy = "".join(sanattar)
    tanbalar = [secrets.choice(s) for s in sanattar]
    tanbalar += [secrets.choice(barlygy) for _ in range(uzyndyq - len(sanattar))]
    secrets.SystemRandom().shuffle(tanbalar)
    return "".join(tanbalar)


parol = generate()
print(parol, len(parol))
```
Тексеру: `all(parol_baga(generate())[0] == 5 for _ in range(100))` → `True` (сирек жағдайда генерацияланған парольде кездейсоқ «1234» болса, 0 шығуы мүмкін — оқушылармен талқылауға жақсы мысал).

**3-тапсырма — мәселелер:**
1. `DB_PASSWORD`, `TELEGRAM_TOKEN` кодта — `.env`/орта айнымалысына көшіру.
2. Пароль ашық түрде сақталады — тұзы бар PBKDF2 хэші.
3. Пароль логқа жазылады — тек логинді жазу.
4. f-жолды SQL — инъекция; параметрлі сұраныс `?`.
5. ЖСН мен мекенжай жиналады — журналға қажет емес (азайту қағидаты); қажет болса — келісіммен, қорғалған түрде.
6. «Мұндай пайдаланушы жоқ» / «Пароль қате» бөлек хабарлары — логиндерді анықтауға мүмкіндік береді; бір хабар.
7. Растау коды `random`-мен — `secrets.randbelow`.
8. Пароль салыстыру `!=` арқылы — `hmac.compare_digest`; кіру әрекеттерінің саны шектелмеген.
9. `journal.db`, `journal.log` `.gitignore`-ға қосылуы керек.

**4-тапсырма**
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

**5-тапсырма**
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
        print("Логин 3–20 латын әріп/цифрдан тұруы керек")
        return
    if login in users:
        print("Бұл логин бос емес")
        return
    parol = getpass("Пароль: ")
    if not mykty_ma(parol):
        print("Пароль әлсіз")
        return
    if parol != getpass("Парольді қайталаңыз: "):
        print("Парольдер сәйкес емес")
        return
    tuz = secrets.token_bytes(16)
    users[login] = {"tuz": tuz.hex(), "hash": hashtau(parol, tuz)}
    saqtau(users)
    print("Тіркелу сәтті!")


def kiru(users):
    for aleket in range(3):
        login = input("Логин: ").strip()
        parol = getpass("Пароль: ")
        user = users.get(login)
        if user and hmac.compare_digest(hashtau(parol, bytes.fromhex(user["tuz"])), user["hash"]):
            print(f"Қош келдіңіз, {login}!")
            return True
        print("Логин немесе пароль қате")
    print("Әрекеттер саны бітті")
    return False


def main():
    users = zhukteu()
    tandau = input("1 — тіркелу, 2 — кіру: ")
    if tandau == "1":
        tirkelu(users)
    elif tandau == "2":
        kiru(users)


if __name__ == "__main__":
    main()
```

**6-тапсырма**
```python
import sqlite3
import unittest


def oqushy_izdeu(conn, aty):
    """Оқушыны аты бойынша қауіпсіз іздейді."""
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

**7-тапсырма**
```python
import pandas as pd

df = pd.DataFrame({
    "aty_zhoni": ["Сейітова Әлия", "Ахметов Нұрлан", "Омарова Дана", "Қасымов Ерлан"],
    "zhsn": ["100315600123", "091120500456", "100701600789", "090505500321"],
    "telefon": ["+77015551267", "+77075550011", "+77475559876", "+77015553344"],
    "zhasy": [16, 17, 16, 17],
    "ball": [87, 92, 78, 65],
})

df["kod"] = [f"Оқушы_{i:03d}" for i in range(1, len(df) + 1)]
df[["kod", "aty_zhoni"]].to_csv("kod_kestesi.csv", index=False)   # бөлек, қорғалған жерде

anonim = df.drop(columns=["aty_zhoni", "zhsn"])
anonim["telefon"] = anonim["telefon"].str[:5] + "*****" + anonim["telefon"].str[-2:]
anonim["zhas_toby"] = pd.cut(anonim["zhasy"], bins=[14, 16, 18], labels=["15–16", "17–18"])
anonim = anonim.drop(columns=["zhasy"])[["kod", "zhas_toby", "telefon", "ball"]]
anonim.to_csv("anonim.csv", index=False)
print(anonim)
```

**8-тапсырма**
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
print(f"SHA-256: PIN {nusqa} табылды, {t_sha:.4f} с")

t0 = time.perf_counter()
for n in range(50):
    hashlib.pbkdf2_hmac("sha256", f"{n:04d}".encode(), TUZ, 100_000)
bir = (time.perf_counter() - t0) / 50
print(f"PBKDF2: бір тексеру ≈ {bir * 1000:.1f} мс, барлық 10 000 нұсқа ≈ {bir * 10000:.0f} с")
```
Қорытынды: SHA-256 4 цифрлы PIN-ді миллисекундтарда табады; PBKDF2 бір тексеруді мыңдаған есе баяулатады. Бірақ 10 000 нұсқа бәрібір аз — сондықтан ұзын пароль және кіру әрекеттерін шектеу қажет.

**9-тапсырма**
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
    print("Табылған ықтимал құпиялар:", tabyldy)


skaner(sys.argv[1] if len(sys.argv) > 1 else ".")
```

**Этикалық кейстер — талқылау бағыттары:**
1. Кілтті пайдаланбау, ешкімге көрсетпеу; сыныптасқа жеке хабарлау, кілтті дереу ауыстыру және репозиторийден алу керегін түсіндіру.
2. Азайту қағидаты: сауалнама мақсатына қажет емес деректерді жинамау; себебін сыпайы түсіндіру.
3. Тек иесінің өз құрылғысында, иесінің қатысуымен және браузердің ресми «Сақталған парольдер» мүмкіндігі арқылы; бөтен құрылғыға мұндай бағдарлама жазу — заңсыз және қауіпті.
4. Фото «ашық» болса да, мұндай рейтинг — адамдардың келісімінсіз жеке деректерін басқа мақсатта қолдану және кемсітушілік; кибербуллингке әкелуі мүмкін. Жасамау.

</details>
