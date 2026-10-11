# Сабақ 6. Практикалық тапсырмалар: GitHub, push, pull, портфолио

## Базалық деңгей

### 1-тапсырма. Аккаунтты дайындау
1. github.com-ға кір (аккаунт жоқ болса — педагогтің көмегімен тірке).
2. Профильге аватар қой (өз фотоң міндетті емес — сурет, логотип болады), **Name** және **Bio** өрістерін толтыр (мысалы: «Python үйірмесінің оқушысы, Алматы»).
3. Settings → Emails → «Keep my email addresses private» қос. Берілген `noreply` поштаны көшіріп, Git-ке бапта:
```bash
git config --global user.email "ID+username@users.noreply.github.com"
```
4. (Ұсынылады) Settings → Password and authentication → екі факторлы аутентификацияны қос.

### 2-тапсырма. Калькуляторды GitHub-қа жүктеу
Өткен сабақтағы `calculator` репозиторийін жүкте:
1. GitHub-та **бос** `calculator` репозиторийін құр (Public, README-сіз).
2. Терминалда:
```bash
git remote add origin https://github.com/USERNAME/calculator.git
git branch -M main
git push -u origin main
```
3. GitHub бетін жаңартып, файлдар мен коммиттер санын тексер.
4. `.venv` папкасы GitHub-та жоқ екенін тексер.

### 3-тапсырма. README.md жазу
Калькулятор жобасына `README.md` жаз (VS Code-та `Ctrl+Shift+V` — алдын ала қарау). Міндетті бөлімдер:
- жоба атауы (`#`);
- 1–2 сөйлемдік сипаттама;
- «Мүмкіндіктері» — тізім;
- «Іске қосу» — код блогымен;
- «Авторы».

Коммиттеп, push жаса: `git add README.md`, `git commit -m "README қосылды"`, `git push`. GitHub-та README әдемі көрінетінін тексер.

### 4-тапсырма. Браузерде өзгерту → pull
1. GitHub сайтында README.md файлын аш → ✏️ (Edit) → «Авторы» бөліміне «Python үйірмесі, 2026» жолын қос → **Commit changes**.
2. Жергілікті компьютерде README-де бұл жол әлі жоқ екенін тексер.
3. `git pull` орында — жол пайда болды ма?
4. `git log --oneline` — браузерде жасалған коммит көріне ме?

### 5-тапсырма. Clone
1. Басқа папкаға өт (мысалы, `Desktop`).
2. Көршіңнің `calculator` репозиторийін клонда: `git clone https://github.com/КӨРШІ/calculator.git korshi-calculator`.
3. Іске қосып көр. Көршіңнің репозиторийіне ⭐ қой.

## Кеңейтілген деңгей

### 6-тапсырма. Профиль README (портфолио беті)
1. GitHub-та пайдаланушы атыңмен **бірдей** аталатын репозиторий құр (мысалы, `aigerim-dev/aigerim-dev`), «Add a README file» белгісімен.
2. README-ге жаз: өзің туралы 2–3 сөйлем (жеке деректерсіз), «Үйреніп жатқаным», «Жобаларым» кестесі (калькуляторға сілтемемен), «Осы жылғы мақсаттарым».
3. Профиль бетіңді ашып, нәтижені тексер.

### 7-тапсырма. «Екі компьютер» сценарийі
Жұппен орында (немесе бір компьютерде екі папкамен):
1. А оқушы `shared-notes` репозиторийін құрып, B оқушыны **Settings → Collaborators** арқылы шақырады.
2. B репозиторийді клондайды, `ideas.md` файлына 3 жоба идеясын жазып, push жасайды.
3. А `git pull` орындап, B-ның идеяларын көреді, өзінікін қосып, push жасайды.
4. B қайта `git pull`.
5. Қосымша: екеуің бір мезгілде **әртүрлі** файлдарды өзгертіп push жасаңдар — біріншісі өтеді, екіншісі `rejected` алады. `git pull`, сосын `git push` арқылы шешіңдер.

### 8-тапсырма. Ескі жобаларды портфолиоға қосу
1-жылдағы ең жақсы жобаңды (қорытынды жоба, turtle-сурет, ойын) жаңа репозиторийге айналдыр: `.gitignore`, README (скриншотпен), кемінде 3 коммит, GitHub-қа push. Профиль README-дегі «Жобаларым» кестесіне қос.

## ★ Олимпиадалық / шығармашылық тапсырма

### 9-тапсырма. README генераторы
Python бағдарламасын жаз: ол жоба атауын, сипаттамасын, мүмкіндіктер тізімін (бос жолға дейін), іске қосу командасын және авторды сұрап, дайын `README.md` файлын жасайды. Қосымша: егер папкада `requirements.txt` бар болса, «Орнату» бөліміне `pip install -r requirements.txt` командасын автоматты түрде қос.

### 10-тапсырма. GitHub API — профиль статистикасы
GitHub-тың ашық API-ы кілтсіз жұмыс істейді: `https://api.github.com/users/USERNAME`. Стандартты `urllib.request` және `json` модульдері арқылы пайдаланушының аты, жария репозиторийлер саны, жазылушылар саны және тіркелген күнін шығаратын бағдарлама жаз. (Интернет пен сұраныстарды VI модульде толық өтеміз — бұл алдын ала «дәм тату».)

```text
Пайдаланушы: aigerim-dev
Жария репозиторийлер: 3
Жазылушылар: 5
Тіркелген: 2026-09-14
```

## Үй тапсырмасы

1. Профиль README-ді жетілдір: өзің туралы, мақсаттар, жобалар кестесі.
2. Портфолиода кемінде 2 репозиторий болсын (калькулятор + 1-жылдың бір жобасы), әрқайсысында README.
3. Профиль сілтемесін педагогке жібер (үйірме тізіміне қосылады).

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**2–4-тапсырмалар (командалар)**
```bash
git remote add origin https://github.com/USERNAME/calculator.git
git remote -v
git branch -M main
git push -u origin main

git add README.md
git commit -m "README қосылды"
git push

git pull
git log --oneline
```

**3-тапсырма — README үлгісі** теорияның 7-бөлімінде.

**7-тапсырма** — `rejected` болғанда:
```bash
git pull
git push
```
Әртүрлі файлдар өзгерген болса, Git өзгерістерді автоматты біріктіреді (merge коммиті жасалуы мүмкін, редактор ашылса — сақтап жабу).

**9-тапсырма**
```python
import os

FENCE = "`" * 3  # Markdown код блогының белгісі


def ask_list(prompt):
    """Бос жол енгізілгенше тізім жинайды."""
    items = []
    print(prompt + " (бітіру үшін Enter):")
    while True:
        item = input("- ").strip()
        if not item:
            break
        items.append(item)
    return items


def build_readme(title, description, features, run_cmd, author, has_req):
    """README.md мәтінін құрастырады."""
    lines = [f"# {title}", "", description, "", "## Мүмкіндіктері"]
    lines += [f"- {feature}" for feature in features]
    if has_req:
        lines += ["", "## Орнату", FENCE + "bash", "pip install -r requirements.txt", FENCE]
    lines += ["", "## Іске қосу", FENCE + "bash", run_cmd, FENCE, "", "## Авторы", author, ""]
    return "\n".join(lines)


def main():
    title = input("Жоба атауы: ")
    description = input("Сипаттамасы: ")
    features = ask_list("Мүмкіндіктері")
    run_cmd = input("Іске қосу командасы: ")
    author = input("Авторы: ")
    has_req = os.path.exists("requirements.txt")
    text = build_readme(title, description, features, run_cmd, author, has_req)
    with open("README.md", "w", encoding="utf-8") as file:
        file.write(text)
    print("README.md жасалды!")


if __name__ == "__main__":
    main()
```

**10-тапсырма**
```python
import json
import urllib.request


def get_user(username):
    """GitHub API-дан пайдаланушы деректерін сөздік ретінде қайтарады."""
    url = f"https://api.github.com/users/{username}"
    request = urllib.request.Request(url, headers={"User-Agent": "python-club"})
    with urllib.request.urlopen(request, timeout=10) as response:
        return json.loads(response.read().decode("utf-8"))


def main():
    username = input("GitHub пайдаланушы аты: ").strip()
    try:
        user = get_user(username)
    except Exception as error:
        print("Деректерді алу мүмкін болмады:", error)
        return
    print("Пайдаланушы:", user["login"])
    print("Жария репозиторийлер:", user["public_repos"])
    print("Жазылушылар:", user["followers"])
    print("Тіркелген:", user["created_at"][:10])


if __name__ == "__main__":
    main()
```

</details>
