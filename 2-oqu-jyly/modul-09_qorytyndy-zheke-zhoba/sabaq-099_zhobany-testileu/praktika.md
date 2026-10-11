# Сабақ 99. Практикалық тапсырмалар: жобаны тестілеу және қателерді түзету

## Базалық деңгей

### 1-тапсырма. Тест жағдайларын жобалау
Өз жобаңыздың ең маңызды **екі** функциясы (кіріс деректерін қабылдайтын) үшін кесте толтырыңыз:

| Функция | Эквиваленттік класс | Тест мәні | Күтілетін нәтиже |
|---|---|---|---|
| ... | дұрыс | ... | ... |
| ... | шекара (төменгі) | ... | ... |
| ... | шекара (жоғарғы) | ... | ... |
| ... | қате түрі | ... | ... |

Жаттығу үшін: «Ойыншының аты» өрісі — 2-ден 15-ке дейін таңба, тек әріптер мен цифрлар, бос орынсыз. Эквиваленттік кластар мен шекаралық мәндерді жазыңыз.

### 2-тапсырма. Модульдік тесттер (міндетті)
`tests/test_logic.py` (немесе ойын үшін `tests/test_game.py`) файлында кемінде **8 тест** жазыңыз:
- кемінде 2 тест — шекаралық мәндер;
- кемінде 2 тест — `assertRaises` (қате енгізу);
- кемінде 1 тест — бос деректер (бос тізім, бос ДҚ);
- деректер қабаты бар болса — `setUp`-та `:memory:` ДҚ немесе уақытша файл.

Іске қосу: `python -m unittest discover -s tests -v`. Барлығы `OK` болғанша жалғастырыңыз.

### 3-тапсырма. Тест-кейстер және айқас тестілеу (міндетті)
1. Өз жобаңызға кемінде **8 тест-кейс** жазыңыз (`docs/test_cases.md`, теориядағы 6-бөлімнің кестесі): негізгі сценарийлер, қате енгізулер, интернетсіз/файлсыз жағдай, қайта ашу.
2. Құрбыңызбен тест-кейстерді алмастырып, оның бағдарламасын **оның** кестесі бойынша тексеріңіз. «Нақты нәтиже» мен «Күйі» бағандарын толтырыңыз.
3. Өтпеген әр кейсті GitHub Issue (қате туралы есеп үлгісімен) ретінде тіркеңіз.

### 4-тапсырма. Қатені түзету (міндетті)
Өзіңізге тіркелген қателердің кемінде **екеуін** 5 қадаммен түзетіңіз: қайталау → регрессиялық тест (қызыл) → орнын табу → түзету → барлық тесттер жасыл. Коммит: `Түзету: ... (Closes #N)`.

## Кеңейтілген деңгей

### 5-тапсырма. Қатені тестпен табыңыз
Төмендегі функцияда 3 қате бар. Алдымен қатені көрсететін тесттер жазыңыз (олар құлауы керек), содан кейін функцияны түзетіңіз.

```python
def grade(score, max_score):
    """Пайызға қарай баға: 85%+ -> 5, 65%+ -> 4, 40%+ -> 3, әйтпесе 2."""
    percent = score / max_score * 100
    if percent > 85:
        return 5
    elif percent > 65:
        return 4
    elif percent > 40:
        return 3
    return 2
```
*Кеңес:* шекаралық мәндерді (85%, 65%, 40%), `max_score = 0` және `score > max_score` жағдайларын тексеріңіз.

### 6-тапсырма. Mock: файл мен API
Жобаңыздағы сыртқы тәуелділікті (API, файл) `unittest.mock.patch` арқылы ауыстырып, кемінде 2 жағдайды тексеріңіз: сәтті жауап және қате (интернет жоқ / файл жоқ). API жоқ жобалар үшін — `open`-ді `mock.mock_open` арқылы ауыстырыңыз.

### 7-тапсырма. Таза ортада тексеру
Жобаңызды басқа папкаға `git clone` арқылы көшіріп, жаңа `venv` құрып, `pip install -r requirements.txt` және `python main.py` орындаңыз. Іске қосылмаса — себебін тауып, түзетіңіз (жиі себептер: `requirements.txt` толық емес, абсолют жол, файл `.gitignore`-да қалып қойған).

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Тестпен қамту (coverage)
`coverage` құралын орнатып (`pip install coverage`), тесттеріңіз кодтың қанша пайызын орындайтынын өлшеңіз:
```bash
coverage run -m unittest discover -s tests
coverage report -m
```
Логика модулінің қамтуын кемінде 80%-ға жеткізіңіз. Есептегі `Missing` бағанындағы жолдарды талдаңыз: оларды тексеру керек пе, әлде олар қолданылмайтын код па?

### 9-тапсырма. Кездейсоқ (fuzz) тестілеу
`parse_amount` (немесе өз валидация функцияңыз) үшін 10 000 кездейсоқ жол жасайтын тест жазыңыз (әріптер, цифрлар, бос орындар, `-`, `.`, қазақ әріптері). Шарт: функция **тек** сан қайтаруы немесе `ValueError` көтеруі керек — басқа ешқандай қате болмауы тиіс.

## Үй тапсырмасы
1. Барлық сыни және маңызды қателерді түзетіп, тесттерді жасыл күйге келтіріңіз. `push`.
2. `docs/test_cases.md` және тест нәтижелерін (терминалдағы `OK` скриншоты) сақтаңыз — қорғауда «тестілеу нәтижесі» ретінде көрсетіледі.
3. Келесі сабаққа: MoSCoW тізіміңізден Should/Could мүмкіндіктерін қарап, 100-сабақта қайсысын қосатыныңызды таңдаңыз (уақыты 1–1,5 сағат).

## Бағалау критерийлері
| Критерий | Үлес | Не тексеріледі |
|---|---|---|
| Тесттердің болуы және өтуі | 40% | кемінде 8 модульдік тест, `OK`; 8 тест-кейс, нақты нәтижелер толтырылған |
| Тест жағдайларының сапасы | 25% | шекаралық мәндер, қате енгізулер, бос деректер қамтылған |
| Қателерді ресімдеу және түзету | 20% | қате есептері толық, регрессиялық тесттер, `Closes #N` |
| Өз бетінше жұмыс, айқас тестілеуге қатысу | 15% | құрбысының жобасын тексерді, уақытында `push` |

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма (ойыншы аты).** Кластар: дұрыс (`Arman7`), тым қысқа (`A`), тым ұзын (16 таңба), рұқсат етілмеген таңба (`Arman!`), бос орын (`Ar man`), бос жол (`""`), қазақ әріптері (`Әлия` — ТТ-ға байланысты: `str.isalnum()` оны «әріп» деп қабылдайды). Шекаралық мәндер: 1, 2, 15, 16 таңба.

**5-тапсырма.** Қателер: (1) `>` орнына `>=` болуы керек — 85% дәл болса 5 емес, 4 шығады (65% және 40% үшін де); (2) `max_score = 0` → `ZeroDivisionError`; (3) `score > max_score` немесе теріс ұпай тексерілмейді.
```python
import unittest


def grade(score, max_score):
    """Пайызға қарай баға: 85%+ -> 5, 65%+ -> 4, 40%+ -> 3, әйтпесе 2."""
    if max_score <= 0:
        raise ValueError("Ең жоғары ұпай оң болуы керек")
    if not 0 <= score <= max_score:
        raise ValueError("Ұпай 0 мен ең жоғары ұпай аралығында болуы керек")
    percent = score / max_score * 100
    if percent >= 85:
        return 5
    if percent >= 65:
        return 4
    if percent >= 40:
        return 3
    return 2


class GradeTest(unittest.TestCase):
    def test_boundaries(self):
        self.assertEqual(grade(85, 100), 5)
        self.assertEqual(grade(84, 100), 4)
        self.assertEqual(grade(65, 100), 4)
        self.assertEqual(grade(40, 100), 3)
        self.assertEqual(grade(39, 100), 2)

    def test_zero_max(self):
        with self.assertRaises(ValueError):
            grade(5, 0)

    def test_out_of_range(self):
        for score in (-1, 101):
            with self.subTest(score=score):
                with self.assertRaises(ValueError):
                    grade(score, 100)


if __name__ == "__main__":
    unittest.main()
```
Ескерту: бөлшек сандармен есептеу дәл емес: `11 / 20 * 100` = `55.00000000000001`, ал `0.57 * 100` = `56.99999999999999`. Шекарада мұндай «құйрық» бағаны өзгертіп жіберуі мүмкін. Сенімдірек нұсқа — бөлшексіз салыстыру: `if score * 100 >= 85 * max_score`.

**6-тапсырма (файл үшін үлгі).**
```python
import json
import unittest
from unittest import mock


def load_records(path="records.json"):
    try:
        with open(path, encoding="utf-8") as f:
            return json.load(f)
    except FileNotFoundError:
        return []


class LoadTest(unittest.TestCase):
    def test_reads_file(self):
        fake = mock.mock_open(read_data='[{"name": "Аружан", "score": 340}]')
        with mock.patch("builtins.open", fake):
            self.assertEqual(load_records()[0]["score"], 340)

    def test_missing_file(self):
        with mock.patch("builtins.open", side_effect=FileNotFoundError):
            self.assertEqual(load_records(), [])


if __name__ == "__main__":
    unittest.main()
```

**7-тапсырма.** Жеке тексеріледі. Жиі кездесетін себептер мен түзетулер: `pip freeze > requirements.txt` орындалмаған; `C:\Users\...` абсолют жолдары → `Path(__file__).resolve().parent`; `assets/` немесе `data/sample.csv` кездейсоқ `.gitignore`-ға түскен.

**8-тапсырма.** Тексеру: `coverage report` нәтижесі ≥ 80% логика модулі үшін. Интерфейс (`gui.py`) қамтуы төмен болуы қалыпты — ол қолмен тестіленеді.

**9-тапсырма.**
```python
import random
import unittest

MAX_AMOUNT = 10_000_000


def parse_amount(text):
    cleaned = text.strip().replace(" ", "")
    if not cleaned.isdigit():
        raise ValueError("Соманы сан түрінде енгізіңіз")
    value = int(cleaned)
    if value <= 0:
        raise ValueError("Сома оң болуы керек")
    if value > MAX_AMOUNT:
        raise ValueError("Сома тым үлкен")
    return value


ALPHABET = "0123456789 -.,абвәғқңөүұһіAZ²٣"


class FuzzTest(unittest.TestCase):
    def test_random_strings(self):
        rnd = random.Random(42)
        for _ in range(10_000):
            text = "".join(rnd.choice(ALPHABET) for _ in range(rnd.randint(0, 12)))
            try:
                result = parse_amount(text)
            except ValueError:
                continue
            self.assertIsInstance(result, int, msg=repr(text))
            self.assertTrue(1 <= result <= MAX_AMOUNT, msg=repr(text))


if __name__ == "__main__":
    unittest.main()
```
Қызықты нәтиже: `"²".isdigit()` → `True`, бірақ `int("²")` → `ValueError`; ал араб цифры `"٣"` үшін `int("٣")` → `3`. Яғни `isdigit()` тексеруі «тек 0–9 цифрлары» дегенді білдірмейді. Fuzz-тест осындай күтпеген жағдайларды табады; қатаңырақ тексеру: `cleaned.isascii() and cleaned.isdigit()`.
</details>
