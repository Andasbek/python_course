# Урок 90. Практические задания: проектирование архитектуры проекта

## Базовый уровень

**Задание 1. Какой слой?** Отнесите каждую функцию/действие к слою: **И** — интерфейс, **Л** — логика, **Д** — данные.
1. Создание кнопки «Сохранить»
2. Вычисление месячной суммы расходов
3. Выполнение `INSERT INTO shygyndar ...`
4. Показ `messagebox.showwarning` при неверном вводе
5. Применение процента скидки
6. Получение JSON из API Open-Meteo
7. Проверка столкновения игрока (Pygame `colliderect`)
8. Запись рекордов в `records.json`
9. Размещение графика результатов в окне
10. Проверка хеша пароля
11. Чтение вопросов из CSV-файла
12. Выделение выбранной строки в таблице

**Задание 2. Анализ «существительное — глагол».** User story проекта «Семейный бюджет»:
- US-1. Как родитель, я хочу вводить расход с суммой, категорией и датой, чтобы знать, куда уходят деньги.
- US-2. Как родитель, я хочу устанавливать месячный лимит для каждой категории, чтобы не тратить лишнего.
- US-3. Как родитель, я хочу видеть предупреждение о категориях, превысивших лимит, чтобы вовремя остановиться.
- US-4. Как родитель, я хочу видеть график расходов по месяцам, чтобы планировать бюджет.

Выпишите существительные и глаголы, определите классы (с атрибутами), методы и решения «не класс» (атрибут, функция). Запишите результат в таблицу.

**Задание 3. CRC-карточки.** Заполните CRC-карточки для 3–5 основных классов своего проекта (на стикерах или в `docs/architecture.md`):
```text
Класс: ...
Ответственность:         Партнёры:
- ...                    - ...
```

**Задание 4. Раздели слои.** В приведённом ниже коде перемешаны логика, интерфейс и данные. Разделите его на три части: класс `Shygyn` и функция вычисления (логика), класс `JsonStorage` (данные), функция консольного интерфейса. Часть логики не должна использовать `input`, `print`, `json`, `open`.

```python
import json


def bastau():
    try:
        with open("budget.json", encoding="utf-8") as f:
            shygyndar = json.load(f)
    except FileNotFoundError:
        shygyndar = []
    soma = int(input("Сумма: "))
    sanat = input("Категория: ")
    if soma <= 0:
        print("Сумма должна быть положительной!")
        return
    shygyndar.append({"soma": soma, "sanat": sanat})
    with open("budget.json", "w", encoding="utf-8") as f:
        json.dump(shygyndar, f, ensure_ascii=False)
    zhalpy = 0
    for s in shygyndar:
        if s["sanat"] == sanat:
            zhalpy += s["soma"]
    print(f"Всего по категории «{sanat}»: {zhalpy} тенге")


bastau()
```

## Продвинутый уровень

**Задание 5. Архитектурный документ.** Напишите для своего проекта `docs/architecture.md`:
1. Таблица модулей (файл — слой — задача — кого вызывает);
2. Диаграмма классов (текстовая схема, Mermaid или рисунок draw.io) — не менее 3 классов и их связи;
3. Описание каждого класса в 1–2 предложениях;
4. «Путь в коде» одной Must user story (какая кнопка → какой метод → какой класс → данные);
5. Внешние библиотеки.

**Задание 6. Каркас проекта.** Создайте в репозитории структуру папок (по образцу из теории):
- все классы внутри `app/`, у каждого метода есть имя, параметры и docstring; в ещё не реализованных методах — `raise NotImplementedError`;
- `main.py` запускается без ошибок (использует хотя бы один класс логики и показывает короткую демонстрацию);
- в `tests/test_models.py` — не менее 1 проходящего теста;
- `.gitignore` (`venv/`, `__pycache__/`, `*.db`, `.env`, `*.log`);
- коммит: `git commit -m "Каркас проекта: архитектура"`.

**Задание 7. Диаграмма Mermaid.** Добавьте свою диаграмму классов в README в формате Mermaid и проверьте, что на GitHub она отображается как рисунок.

## ★ Олимпиадная / творческая задача

**Задание 8. Проверка зависимостей.** Напишите программу `deps.py`, которая с помощью `ast` находит импорты во всех `.py`-файлах проекта и выводит зависимости модулей. Она должна: (а) выводить список «модуль → импортируемые модули»; (б) проверять **правило слоёв**: если в `models.py` или `storage.py` есть импорт `tkinter`/`pygame` — предупреждение; (в) ★★ находить циклические импорты.

**Задание 9. Заменяемое хранилище (полиморфизм).** Напишите два класса с одинаковым интерфейсом (`saqtau(zhazba)`, `barlygy()`): `JsonStorage` и `SqliteStorage`. Класс логики (`Budget`) должен получать хранилище через конструктор (`Budget(storage)`) и не знать тип хранилища. Запустите один тестовый класс `unittest` с обоими хранилищами и покажите, что тесты проходят в обоих случаях. Это идея «зависимости через интерфейс».

## Домашнее задание

1. Завершите задания 5–6 и загрузите на GitHub.
2. К следующему уроку (модель данных): составьте список того, **какие данные** хранятся в вашем проекте — какие атрибуты каждого класса должны сохраняться в файле/базе данных?

## Критерии оценивания

| Показатель | Баллы | Для полного балла |
|---|---|---|
| Разделение на слои | 0–5 | Интерфейс, логика, данные в отдельных модулях; направление зависимостей верное |
| Классы и обязанности | 0–6 | Классы выведены из ТЗ, у каждого одна ответственность, нет «божественного класса» |
| Диаграмма классов | 0–4 | ≥ 3 классов, атрибуты, методы, связи обозначены правильно |
| Каркас проекта | 0–6 | Структура папок, docstring, `main.py` запускается, ≥ 1 тест проходит |
| Документ и GitHub | 0–4 | `docs/architecture.md`, коммит, `.gitignore` |
| **Итого** | **25** | |

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1:** 1 — И; 2 — Л; 3 — Д; 4 — И; 5 — Л; 6 — Д (внешний источник данных); 7 — Л (правило игры; хотя используется объект `Rect` из Pygame, это логика); 8 — Д; 9 — И; 10 — Л (или служба безопасности); 11 — Д; 12 — И.

**Задание 2 — образец:**

| Существительное | Решение |
|---|---|
| Расход | Класс `Shygyn` (soma, sanat, kuni) |
| Категория | Атрибут `str` (MVP); в расширенной версии — класс `Sanat` (atau, limit) |
| Лимит | Значение словаря `Budget.limitter` `{sanat: soma}` |
| Предупреждение | Результат метода `Budget.limitten_asqandar()` (список); показ — в интерфейсе |
| График | Функция `stats.ailyq_grafik()` |
| Бюджет (скрытое существительное: все расходы и лимиты) | Класс `Budget` |
| Родитель | Роль, не класс (один пользователь) |

Глаголы → методы: вводить → `Budget.qosu(shygyn)`; устанавливать лимит → `Budget.limit_qoiu(sanat, soma)`; видеть предупреждение → `Budget.limitten_asqandar(ai)`; видеть график → `stats.ailyq_grafik(shygyndar)`; (скрыто) сохранить/загрузить → `Storage.saqtau()`, `Storage.zhukteu()`.

**Задание 4**
```python
import json


# ---------- ЛОГИКА (models.py) ----------
class Shygyn:
    """Одна запись о расходе."""

    def __init__(self, soma, sanat):
        if soma <= 0:
            raise ValueError("Сумма должна быть положительной")
        self.soma = soma
        self.sanat = sanat

    def to_dict(self):
        """Словарь для сохранения."""
        return {"soma": self.soma, "sanat": self.sanat}


def sanat_qosyndysy(shygyndar, sanat):
    """Сумма расходов по заданной категории."""
    return sum(s.soma for s in shygyndar if s.sanat == sanat)


# ---------- ДАННЫЕ (storage.py) ----------
class JsonStorage:
    """Хранит расходы в JSON-файле."""

    def __init__(self, path="budget.json"):
        self.path = path

    def zhukteu(self):
        """Читает из файла список объектов Shygyn."""
        try:
            with open(self.path, encoding="utf-8") as f:
                return [Shygyn(d["soma"], d["sanat"]) for d in json.load(f)]
        except FileNotFoundError:
            return []

    def saqtau(self, shygyndar):
        """Записывает список в файл."""
        with open(self.path, "w", encoding="utf-8") as f:
            json.dump([s.to_dict() for s in shygyndar], f, ensure_ascii=False)


# ---------- ИНТЕРФЕЙС (cli.py / main.py) ----------
def bastau(storage):
    """Консольный интерфейс: ввод, сохранение, вывод результата."""
    shygyndar = storage.zhukteu()
    try:
        shygyn = Shygyn(int(input("Сумма: ")), input("Категория: "))
    except ValueError as e:
        print("Ошибка:", e)
        return
    shygyndar.append(shygyn)
    storage.saqtau(shygyndar)
    zhalpy = sanat_qosyndysy(shygyndar, shygyn.sanat)
    print(f"Всего по категории «{shygyn.sanat}»: {zhalpy} тенге")


if __name__ == "__main__":
    bastau(JsonStorage())
```
Теперь `Shygyn` и `sanat_qosyndysy` тестируются без файлов и ввода, а `JsonStorage` потом можно заменить на `SqliteStorage`.

**Задание 8**
```python
import ast
import os
import sys

TYIM = {"models.py", "storage.py"}            # эти модули не должны знать об интерфейсе
UI_KITAPHANALAR = {"tkinter", "pygame"}


def importtar(path):
    """Возвращает множество имён импортированных в файле модулей."""
    with open(path, encoding="utf-8") as f:
        agash = ast.parse(f.read())
    ataular = set()
    for t in ast.walk(agash):
        if isinstance(t, ast.Import):
            ataular.update(a.name.split(".")[0] for a in t.names)
        elif isinstance(t, ast.ImportFrom) and t.module:
            ataular.add(t.module)
    return ataular


def tauyeldilik_grafy(papka):
    """Возвращает словарь {модуль: его импорты}."""
    graf = {}
    for tubir, papkalar, faildar in os.walk(papka):
        papkalar[:] = [p for p in papkalar if p not in ("venv", ".git", "__pycache__")]
        for fail in faildar:
            if fail.endswith(".py"):
                graf[os.path.join(tubir, fail)] = importtar(os.path.join(tubir, fail))
    return graf


def tekseru(papka):
    graf = tauyeldilik_grafy(papka)
    for modul, imp in sorted(graf.items()):
        print(f"{modul} → {', '.join(sorted(imp)) or '—'}")
        if os.path.basename(modul) in TYIM:
            for k in imp & UI_KITAPHANALAR:
                print(f"  Предупреждение: в {modul} импорт «{k}» — нарушено правило слоёв")


tekseru(sys.argv[1] if len(sys.argv) > 1 else ".")
```
Результат: список импортов каждого модуля; если в `models.py` или `storage.py` найден `tkinter`/`pygame` — предупреждение.

**Задание 9**
```python
import json
import os
import sqlite3
import unittest


class JsonStorage:
    """Хранит записи в JSON-файле."""

    def __init__(self, path):
        self.path = path

    def saqtau(self, zhazba):
        barlygy = self.barlygy()
        barlygy.append(zhazba)
        with open(self.path, "w", encoding="utf-8") as f:
            json.dump(barlygy, f, ensure_ascii=False)

    def barlygy(self):
        if not os.path.exists(self.path):
            return []
        with open(self.path, encoding="utf-8") as f:
            return json.load(f)


class SqliteStorage:
    """Хранит записи в SQLite."""

    def __init__(self, path):
        self.conn = sqlite3.connect(path)
        self.conn.execute("CREATE TABLE IF NOT EXISTS z (soma INTEGER, sanat TEXT)")

    def saqtau(self, zhazba):
        self.conn.execute("INSERT INTO z VALUES (?, ?)", (zhazba["soma"], zhazba["sanat"]))
        self.conn.commit()

    def barlygy(self):
        rows = self.conn.execute("SELECT soma, sanat FROM z").fetchall()
        return [{"soma": s, "sanat": n} for s, n in rows]


class Budget:
    """Логика бюджета: не знает тип хранилища."""

    def __init__(self, storage):
        self.storage = storage

    def qosu(self, soma, sanat):
        if soma <= 0:
            raise ValueError("Сумма должна быть положительной")
        self.storage.saqtau({"soma": soma, "sanat": sanat})

    def zhalpy(self):
        return sum(z["soma"] for z in self.storage.barlygy())


class BudgetTestMixin:
    """Общие тесты для любого хранилища."""

    def make_storage(self):
        raise NotImplementedError

    def setUp(self):
        self.budget = Budget(self.make_storage())

    def test_qosu_zhane_zhalpy(self):
        self.budget.qosu(500, "Еда")
        self.budget.qosu(300, "Транспорт")
        self.assertEqual(self.budget.zhalpy(), 800)

    def test_teris_soma(self):
        with self.assertRaises(ValueError):
            self.budget.qosu(-1, "Еда")


class TestJson(BudgetTestMixin, unittest.TestCase):
    PATH = "test_budget.json"

    def make_storage(self):
        if os.path.exists(self.PATH):
            os.remove(self.PATH)
        return JsonStorage(self.PATH)

    def tearDown(self):
        if os.path.exists(self.PATH):
            os.remove(self.PATH)


class TestSqlite(BudgetTestMixin, unittest.TestCase):
    def make_storage(self):
        return SqliteStorage(":memory:")


if __name__ == "__main__":
    unittest.main(verbosity=2)
```
Результат: 4 теста (по 2 на каждое хранилище) — OK.

</details>
