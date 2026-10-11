# Урок 81. Практические задания: составление аналитического отчёта (практикум)

## Задание практикума

Выберите один набор данных, выполните **полный цикл анализа** и оформите результат на GitHub в виде аналитического отчёта (README.md). Работа выполняется индивидуально (в паре — с разрешения педагога). На следующем уроке (82) вы защитите проект за 3–4 минуты.

**Обязательные требования (минимум):**
1. Не менее **3 исследовательских вопросов**.
2. **Очистка** данных (не менее 3 разных действий) и отчёт об очистке.
3. Не менее **2 анализов с `groupby`/`agg`/`pivot_table`**.
4. Не менее **3 графиков**, не менее **2 разных** типов диаграмм, все оформлены по чек-листу «профессиональный график».
5. Код разбит на функции, есть docstring и комментарии (PEP 8).
6. README.md — по образцу структуры, конкретные ответы на вопросы (с числами).

## Выбор набора данных

### Вариант А. «Образ жизни ученика» (базовый)

Данные образца проекта из теории. Не повторяйте образец, поставьте **свои вопросы** (например: разница между мальчиками и девочками, время в телефоне по классам, доля учеников, спящих меньше 7 часов).

```python
import numpy as np
import pandas as pd

rng = np.random.default_rng(2027)
n = 150
synyp = rng.integers(7, 12, n)
jynys = rng.choice(["Мальчик", "Девочка"], n)
uiqy = np.round(rng.normal(7.5, 1.0, n), 1)
telefon = np.round(rng.uniform(1, 7, n), 1)
sport = rng.choice(["Да", "Нет"], n, p=[0.45, 0.55])
ball = np.round(60 + 4 * (uiqy - 7) - 3 * (telefon - 4)
                + np.where(sport == "Да", 3, 0) + rng.normal(10, 7, n)).clip(20, 100)

df = pd.DataFrame({"synyp": synyp, "jynys": jynys, "uiqy": uiqy,
                   "telefon": telefon, "sport": sport, "ball": ball})
# специально "загрязняем"
df.loc[[5, 40, 77], "uiqy"] = np.nan
df.loc[[12, 90], "jynys"] = ["девочка ", "мальчик"]
df.loc[33, "ball"] = 450
df.loc[61, "telefon"] = -2
df = pd.concat([df, df.iloc[[3, 50, 100]]], ignore_index=True)
df.to_csv("oqushy_omiri.csv", index=False)
print("oqushy_omiri.csv создан:", len(df), "записей")
```

### Вариант Б. «Погода в моём городе» (продвинутый, API + анализ)

Получите из архива Open-Meteo (бесплатно, без ключа) ежедневную погоду своего города за год. Рекомендуемые вопросы: самый жаркий/холодный месяц; количество дней с осадками по месяцам; разница дневной и ночной температуры; сравнение двух городов.

```python
import requests
import pandas as pd

url = "https://archive-api.open-meteo.com/v1/archive"
params = {
    "latitude": 43.24,            # Алматы
    "longitude": 76.89,
    "start_date": "2025-01-01",
    "end_date": "2025-12-31",
    "daily": "temperature_2m_max,temperature_2m_min,precipitation_sum",
    "timezone": "Asia/Almaty",
}
response = requests.get(url, params=params, timeout=30)
response.raise_for_status()
daily = response.json()["daily"]

df = pd.DataFrame(daily)
df["time"] = pd.to_datetime(df["time"])
df.to_csv("almaty_2025.csv", index=False)
print(df.head())
print("Записей:", len(df))
```

Подсказка: группировка по месяцам — `df.groupby(df["time"].dt.month)`.

### Вариант В. «Книжные магазины» (базовый)

`kitap_dukeni.csv` с урока 77. Расширьте данные сами: добавьте вторую неделю (`pd.concat`), введите филиал «Онлайн», специально добавьте 3–4 ошибки и очистите их.

### Вариант Г. Собственный набор данных (★ творческий)

data.egov.kz, stat.gov.kz, Kaggle или собственный опрос в классе. Не менее 50 строк и 4 столбцов. Заранее согласуйте с педагогом. Удалите персональные данные (ФИО, телефон)!

## Шаги работы

**Шаг 1. Подготовка проекта (5 мин).**
```bash
mkdir taldau-zhobasy
cd taldau-zhobasy
mkdir data images
git init
```
Поместите данные в `data/raw.csv`.

**Шаг 2. Вопросы (5 мин).** Откройте файл README.md и запишите в раздел 1 3–5 исследовательских вопросов. Покажите педагогу и утвердите.

**Шаг 3. Исследование и очистка (20 мин).** Функции `load_data()` и `clean_data()` в `analysis.py`. Выведите на экран отчёт об очистке (сколько найдено пропусков, дубликатов, ошибок и что сделано) и скопируйте его в раздел 3 README. Сохраните `data/clean.csv`.

**Шаг 4. Анализ (20 мин).** Функция `analyze()`: на каждый вопрос — одна таблица или число. Запишите результаты в README.

**Шаг 5. Визуализация (20 мин).** Функция `make_charts()`: на каждый вопрос — один график, сохранить в папку `images/` с `dpi=150`. Проверьте по чек-листу.

**Шаг 6. Выводы и README (10 мин).** 3–5 конкретных выводов (с числами), 1–2 рекомендации, ограничения, инструкция по запуску.

**Шаг 7. GitHub (5 мин).**
```bash
git add .
git commit -m "Проект анализа данных: очистка, анализ, графики, отчёт"
git branch -M main
git remote add origin https://github.com/<логин>/taldau-zhobasy.git
git push -u origin main
```

## Шаблон README.md

```text
# Образ жизни и успеваемость учеников

## 1. Цель и исследовательские вопросы
1. Как время в телефоне связано с баллом?
2. ...

## 2. Данные
- Источник: синтетический опрос (oqushy_omiri.csv)
- Объём: 153 строки × 6 столбцов
- Столбцы: synyp — класс, jynys — пол, uiqy — сон (ч), ...

## 3. Очистка данных
| Проблема | Количество | Решение |
|---|---|---|
| Дубликаты | 3 | удалены |
| Пустой сон | 3 | заполнены медианой |
| ... | | |
Итог: осталось 148 записей.

## 4. Результаты анализа
### 4.1. Телефон и балл
![Телефон и балл](images/telefon_ball.png)
Средний балл тех, кто проводит в телефоне больше 4 часов в день, — 70,9,
у тех, кто проводит до 2 часов, — 81,4 (корреляция r = −0,49).

## 5. Выводы и рекомендации
## 6. Ограничения
## 7. Запуск
pip install pandas matplotlib
python analysis.py
```

## Чек-лист самопроверки (перед сдачей)

- [ ] `python analysis.py` работает без ошибок, в том числе в новом окружении (указаны `requirements`)
- [ ] `data/raw.csv` не изменён, `data/clean.csv` есть
- [ ] Не менее 3 вопросов, и на каждый есть ответ
- [ ] Не менее 3 графиков, 2 разных типа; заголовок, единицы на осях, легенда
- [ ] В выводах есть числа
- [ ] Код разбит на функции, есть docstring
- [ ] На GitHub README отображается правильно (картинки открываются)

## Домашнее задание

1. Завершите проект (если не успели на уроке) и отправьте ссылку на GitHub педагогу.
2. Подготовьте доклад на 3–4 минуты для защиты (структура — в теории урока 82): вопросы → данные → 2–3 главных графика → выводы.
3. Повторите уроки 76–80 — на следующем уроке самостоятельная работа №6.

## Критерии оценивания

| Критерий | Вес | Для полного балла |
|---|---|---|
| Правильная работа программы и соответствие требованиям задания | 40% | Код без ошибок; выполнены все 6 обязательных требований; есть ответы на вопросы |
| Эффективность алгоритма, рациональность решения | 25% | Решения по очистке обоснованы; groupby/agg уместны; типы диаграмм соответствуют вопросам |
| Читаемость кода: именование, комментарии, структура | 20% | Функции, docstring, PEP 8, понятные имена; README структурирован |
| Самостоятельность выполнения и своевременная сдача | 15% | На GitHub к концу урока/до следующего урока |

Уровни освоения: 85–100% — высокий, 60–84% — средний, 40–59% — начальный.

## Для педагога: решения

<details><summary>Показать решения</summary>

**Вариант А** — полное образцовое решение в разделе 4 теории. Образец для дополнительных вопросов:
```python
import pandas as pd

df = pd.read_csv("oqushy_omiri.csv").drop_duplicates()
df["jynys"] = df["jynys"].str.strip().str.capitalize()
df["uiqy"] = df["uiqy"].fillna(df["uiqy"].median())
df = df[df["ball"].between(0, 100) & (df["telefon"] >= 0)].copy()

print(df.groupby("jynys")["ball"].agg(["count", "mean"]).round(1))
print(df.groupby("synyp")["telefon"].mean().round(1))
az_uiqy = (df["uiqy"] < 7).mean() * 100
print(f"Учеников, спящих меньше 7 часов: {az_uiqy:.1f}%")
print(pd.pivot_table(df, values="ball", index="synyp", columns="sport", aggfunc="mean").round(1))
```
Ожидаемые результаты: после очистки 148 записей; мальчики 75,3, девочки 73,8 (разница небольшая); телефон–балл r ≈ −0,49; сон–балл r ≈ 0,29.

**Вариант Б** — образец анализа:
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("almaty_2025.csv", parse_dates=["time"])
df = df.dropna()
df["ai"] = df["time"].dt.month
df["airma"] = df["temperature_2m_max"] - df["temperature_2m_min"]
df["zhauyn"] = df["precipitation_sum"] > 0.1

aylyq = df.groupby("ai").agg(
    max_orta=("temperature_2m_max", "mean"),
    min_orta=("temperature_2m_min", "mean"),
    zhauyn_kun=("zhauyn", "sum"),
    airma=("airma", "mean"),
).round(1)
print(aylyq)
print("Самый жаркий месяц:", aylyq["max_orta"].idxmax())
print("Самый холодный месяц:", aylyq["min_orta"].idxmin())

fig, ax = plt.subplots(figsize=(9, 5))
ax.plot(aylyq.index, aylyq["max_orta"], marker="o", color="#E76F51", label="Дневной max")
ax.plot(aylyq.index, aylyq["min_orta"], marker="o", color="#264653", label="Ночной min")
ax.fill_between(aylyq.index, aylyq["min_orta"], aylyq["max_orta"], alpha=0.1)
ax.set_xticks(range(1, 13))
ax.set_title("Алматы, 2025: среднемесячная температура", loc="left", fontweight="bold")
ax.set_xlabel("Месяц")
ax.set_ylabel("°C")
ax.legend(frameon=False)
ax.spines[["top", "right"]].set_visible(False)
fig.savefig("images/temperatura.png", dpi=150, bbox_inches="tight")

fig, ax = plt.subplots(figsize=(9, 5))
ax.bar(aylyq.index, aylyq["zhauyn_kun"], color="#2A9D8F")
ax.set_xticks(range(1, 13))
ax.set_title("Количество дней с осадками", loc="left", fontweight="bold")
ax.set_xlabel("Месяц")
ax.set_ylabel("Дней")
fig.savefig("images/zhauyn.png", dpi=150, bbox_inches="tight")
plt.show()
```

**Варианты В и Г** — решение зависит от данных ученика; оценивание по таблице. Места, где чаще всего приходится делать замечания: (1) запись поверх исходного файла; (2) отсутствие чисел в выводах; (3) ось Y столбчатой диаграммы начинается не с 0; (4) графики не открываются в README (неверный относительный путь к картинке).

</details>
