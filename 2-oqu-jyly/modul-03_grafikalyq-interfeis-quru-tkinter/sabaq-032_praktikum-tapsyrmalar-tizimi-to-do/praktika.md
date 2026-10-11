# Сабақ 32. Практикалық тапсырмалар: «To-Do List» қосымшасы

> Мақсат — деректерін файлда сақтайтын тапсырмалар тізімін жасау. Әр қадамнан кейін іске қосып тексер, `git commit` жаса. Жоба папкасы: `todo-app`.

## Базалық деңгей

### 1-қадам. Интерфейс
Терезе (420×420, тақырыбы «Тапсырмалар тізімі») құр:
- жоғарыда — енгізу өрісі (`Entry`) және «Қосу» батырмасы;
- ортада — `Listbox` және оң жақта `Scrollbar`;
- төменде — «Орындалды», «Өшіру» батырмалары және санауыш `Label`.

**Кеңес:** әр жолды жеке `Frame` ішіне `pack` арқылы орналастыр.

### 2-қадам. Тапсырма қосу
Енгізілген мәтін `self.tasks` тізіміне `{"text": ..., "done": False}` түрінде қосылып, Listbox-та `☐ мәтін` болып көрінсін. Енгізу өрісі тазалансын. `Enter` пернесі де жұмыс істесін.
Бос мәтін қосылмайды → «Тапсырма мәтіні бос болмауы керек» ескертуі.

### 3-қадам. Белгілеу және өшіру
- «Орындалды» — таңдалған тапсырманың күйін ауыстырады (`☐` ↔ `✔`), орындалғаны сұр түспен көрсетіледі;
- «Өшіру» — растау сұрағынан кейін таңдалған тапсырманы өшіреді;
- ештеңе таңдалмаса — ескерту шығады, бағдарлама құламайды.

### 4-қадам. Санауыш
Төменгі жолда `Орындалды: 2 / 5` түрінде статистика әр өзгерістен кейін жаңарсын.

### 5-қадам. Файлға сақтау және жүктеу
- Бағдарлама іске қосылғанда `tasks.txt` файлынан тапсырмалар жүктеледі (файл жоқ болса — бос тізім);
- терезе жабылғанда (`WM_DELETE_WINDOW`) тапсырмалар файлға сақталады.

Файл мазмұнының мысалы:
```text
0|Ағылшын тілінен 20 сөз жаттау
1|Калькуляторды GitHub-қа жүктеу
```

## Кеңейтілген деңгей

### 6-тапсырма. Өңдеу
«Өңдеу» батырмасы: таңдалған тапсырманың мәтінін `simpledialog.askstring` арқылы өзгерту.

### 7-тапсырма. Басымдық (приоритет)
Енгізу өрісінің жанына `Combobox` қос: «Жоғары», «Орташа», «Төмен». Тізімде басымдыққа қарай түс: жоғары — қызыл, орташа — қызғылт сары, төмен — жасыл. Файл форматы: `0|Жоғары|Мәтін`.

### 8-тапсырма. Сұрыптау және іздеу
- «Сұрыптау» батырмасы: алдымен орындалмағандар, содан кейін орындалғандар (әр топ ішінде алфавит бойынша);
- іздеу өрісі: енгізілген сөзі бар тапсырмалар ғана көрсетілсін.

## ★ Олимпиадалық / шығармашылық тапсырма

**А. Мерзім (дедлайн).** Әр тапсырмаға мерзім қос (`КК.АА.ЖЖЖЖ`). `datetime` модулі арқылы мерзімі өткен тапсырмалар қызыл, бүгінгі — қалың қаріппен көрсетілсін. Қате күн енгізілсе — ескерту.

**Ә. Бірнеше тізім.** Мәзір (`Menu`) арқылы «Мектеп», «Үй», «Үйірме» тізімдерін ауыстыру; әр тізім жеке файлда сақталады.

## Үй тапсырмасы

1. Қосымшаны аяқтап, `todo-app` репозиторийіне жүкте. `tasks.txt` файлын `.gitignore`-ға қос (жеке деректер репозиторийге түспеуі керек).
2. `README.md`: сипаттама, мүмкіндіктер тізімі, скриншот, іске қосу нұсқаулығы.
3. Келесі сабақта жобаны 3 минут ішінде таныстыруға дайындал (қорғау регламенті 33-сабақта).

## Бағалау критерийлері

| Критерий | Сипаттама | Ұпай (%) |
|---|---|---|
| Дұрыс жұмыс және талапқа сай келуі | 1–5 қадам орындалған, тест чек-парағының барлық пункті өтеді | 40 |
| Шешімнің ұтымдылығы | Деректер тізімде, Listbox тек көрініс; қателер (бос таңдау, файл жоқ) өңделген | 25 |
| Кодтың оқылымдылығы | Класс құрылымы, әдістер атауы, docstring, PEP 8 | 20 |
| Өз бетінше және мерзімінде | Сабақ соңында жұмыс істейтін нұсқа, commit тарихы | 15 |
| **Барлығы** | | **100** |

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1–6 қадамдар.** Толық шешім теориядағы `TodoApp` класында (6-бөлім).

**7-тапсырма. Басымдық.** `TodoApp`-қа енгізілетін өзгерістер:

```python
import tkinter as tk
from tkinter import ttk, messagebox

COLORS = {"Жоғары": "red", "Орташа": "darkorange", "Төмен": "green"}


class PriorityTodo:
    """Басымдығы бар тапсырмалар тізімі (қысқартылған нұсқа)."""

    def __init__(self, root):
        self.root = root
        root.title("Басымдықпен тапсырмалар")
        self.tasks = []
        top = tk.Frame(root)
        top.pack(fill="x", padx=10, pady=10)
        self.entry = tk.Entry(top)
        self.entry.pack(side="left", fill="x", expand=True)
        self.priority = ttk.Combobox(top, values=list(COLORS), width=8,
                                     state="readonly")
        self.priority.current(1)
        self.priority.pack(side="left", padx=5)
        tk.Button(top, text="Қосу", command=self.add_task).pack(side="left")
        self.listbox = tk.Listbox(root, font=("Arial", 12))
        self.listbox.pack(fill="both", expand=True, padx=10, pady=5)

    def add_task(self):
        text = self.entry.get().strip()
        if not text:
            messagebox.showwarning("Ескерту", "Мәтін бос")
            return
        self.tasks.append({"text": text, "done": False,
                           "priority": self.priority.get()})
        self.entry.delete(0, tk.END)
        self.refresh_list()

    def refresh_list(self):
        self.listbox.delete(0, tk.END)
        for i, task in enumerate(self.tasks):
            mark = "✔" if task["done"] else "☐"
            self.listbox.insert(tk.END, f"{mark} [{task['priority']}] {task['text']}")
            self.listbox.itemconfig(i, fg=COLORS[task["priority"]])

    def save_line(self, task):
        flag = "1" if task["done"] else "0"
        return f"{flag}|{task['priority']}|{task['text']}\n"

    @staticmethod
    def parse_line(line):
        flag, priority, text = line.rstrip("\n").split("|", 2)
        return {"text": text, "done": flag == "1", "priority": priority}


if __name__ == "__main__":
    root = tk.Tk()
    PriorityTodo(root)
    root.mainloop()
```

**8-тапсырма. Сұрыптау және іздеу.** `TodoApp` класына қосылатын әдістер:

```python
def sort_tasks(self):
    self.tasks.sort(key=lambda t: (t["done"], t["text"].lower()))
    self.refresh_list()


def filter_tasks(self, word):
    """Іздеу сөзі бар тапсырмаларды ғана көрсетеді."""
    self.listbox.delete(0, "end")
    word = word.lower()
    for task in self.tasks:
        if word in task["text"].lower():
            mark = "✔" if task["done"] else "☐"
            self.listbox.insert("end", f"{mark} {task['text']}")
```

Іздеу өрісін байланыстыру: `search_entry.bind("<KeyRelease>", lambda e: self.filter_tasks(search_entry.get()))`.
Ескерту: сүзгіленген режимде Listbox индекстері `self.tasks` индекстерімен сәйкес келмейді, сондықтан сүзгі кезінде «Өшіру»/«Белгілеу» үшін көрсетілген тапсырмалардың нақты индекстерінің тізімін (`self.visible`) сақтау керек — бұл жақсы оқушыларға талдау сұрағы.

`(t["done"], t["text"].lower())` кілті: `False < True`, сондықтан орындалмағандар алдымен шығады.

**★ А. Мерзім.**

```python
from datetime import datetime, date


def parse_deadline(text):
    """'25.12.2026' → date; қате болса None."""
    try:
        return datetime.strptime(text, "%d.%m.%Y").date()
    except ValueError:
        return None


def deadline_color(deadline, done):
    if done or deadline is None:
        return "gray" if done else "black"
    today = date.today()
    if deadline < today:
        return "red"
    if deadline == today:
        return "blue"
    return "black"


print(parse_deadline("25.12.2026"))
print(parse_deadline("31.02.2026"))
print(deadline_color(date(2020, 1, 1), False))
```

Нәтижесі:
```text
2026-12-25
None
red
```

**★ Ә. Бірнеше тізім.** Файл атауын айнымалыда сақтау (`self.filename = "mektep.txt"`), мәзірдің әр пунктінде: ағымдағы тізімді сақтау → `self.filename` ауыстыру → `self.tasks = self.load_tasks()` → `refresh_list()`. Мәзір құру:

```python
import tkinter as tk


def build_menu(app, root):
    menubar = tk.Menu(root)
    lists_menu = tk.Menu(menubar, tearoff=0)
    for name, filename in [("Мектеп", "mektep.txt"), ("Үй", "ui.txt"),
                           ("Үйірме", "uiirme.txt")]:
        lists_menu.add_command(label=name,
                               command=lambda f=filename: app.switch_list(f))
    menubar.add_cascade(label="Тізімдер", menu=lists_menu)
    root.config(menu=menubar)
```

</details>
