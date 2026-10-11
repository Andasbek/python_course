# Сабақ 32. Практикум: «Тапсырмалар тізімі (To-Do List)» қосымшасын құру.

## Тақырыптар

1. Талаптар және макет
2. Қабаттарға бөлу: деректер, логика, интерфейс
3. Деректерді файлда сақтау
4. Терезе жабылғанда сақтау
5. Listbox-пен жұмыс
6. Толық үлгі: «To-Do List» қосымшасы
7. Тестілеу чек-парағы
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Талаптар және макет

| № | Талап | Түрі |
|---|---|---|
| 1 | Жаңа тапсырманы енгізу өрісі арқылы қосу (батырма немесе Enter) | Міндетті |
| 2 | Таңдалған тапсырманы өшіру | Міндетті |
| 3 | Тапсырманы «орындалды» деп белгілеу (✔ белгісі) | Міндетті |
| 4 | Бос тапсырма қосылмайды, ескерту шығады | Міндетті |
| 5 | Бағдарлама жабылғанда тапсырмалар файлға сақталады, ашылғанда жүктеледі | Міндетті |
| 6 | Тапсырманы өңдеу (мәтінін өзгерту) | Қосымша |
| 7 | Орындалған тапсырмаларды тазалау, санауыш («3 / 5 орындалды») | Қосымша |

```text
+--------------------------------------------+
| [ Жаңа тапсырма енгізіңіз...      ] [Қосу]  |
+--------------------------------------------+
| ☐ Математикадан үй жұмысы              ▲   |
| ✔ Python-нан калькулятор жасау          █   |
| ☐ Кітапханаға бару                      ▼   |
+--------------------------------------------+
| [Орындалды] [Өңдеу] [Өшіру] [Тазалау]      |
| Орындалды: 1 / 3                            |
+--------------------------------------------+
```

## 2. Қабаттарға бөлу: деректер, логика, интерфейс

Үлкен қосымшада кодты үш бөлікке бөлу ыңғайлы:

| Қабат | Не істейді | Біздің жобада |
|---|---|---|
| Деректер (модель) | Ақпаратты сақтайды | `self.tasks` — сөздіктер тізімі |
| Логика | Деректерді өзгертеді | `add_task()`, `delete_task()`, `toggle_done()` |
| Интерфейс (көрініс) | Деректерді экранда көрсетеді | `refresh_list()` — Listbox-ты қайта толтырады |

**Басты ереже:** Listbox — тек «витрина». Шын деректер `self.tasks` тізімінде. Кез келген өзгерістен кейін тізімді өзгертіп, `refresh_list()` шақырамыз. Сонда экран мен деректер әрқашан сәйкес болады.

Бір тапсырманы сөздік ретінде сақтаймыз:

```python
tasks = [
    {"text": "Математикадан үй жұмысы", "done": False},
    {"text": "Python-нан калькулятор жасау", "done": True},
]

for i, task in enumerate(tasks, start=1):
    mark = "✔" if task["done"] else "☐"
    print(f"{i}. {mark} {task['text']}")
```

Нәтижесі:
```text
1. ☐ Математикадан үй жұмысы
2. ✔ Python-нан калькулятор жасау
```

## 3. Деректерді файлда сақтау

Деректерді қарапайым мәтіндік файлда сақтаймыз. Әр тапсырманы бір жолға жазамыз: алдымен күйі (`1` — орындалды, `0` — жоқ), содан кейін `|` белгісі және мәтін.

```text
0|Математикадан үй жұмысы
1|Python-нан калькулятор жасау
0|Кітапханаға бару
```

**Сақтау функциясы:**

```python
def save_tasks(tasks, filename="tasks.txt"):
    with open(filename, "w", encoding="utf-8") as f:
        for task in tasks:
            flag = "1" if task["done"] else "0"
            f.write(f"{flag}|{task['text']}\n")
```

**Жүктеу функциясы** (файл әлі жоқ болса, бос тізім қайтарамыз):

```python
def load_tasks(filename="tasks.txt"):
    tasks = []
    try:
        with open(filename, "r", encoding="utf-8") as f:
            for line in f:
                line = line.rstrip("\n")
                if "|" not in line:
                    continue
                flag, text = line.split("|", 1)
                tasks.append({"text": text, "done": flag == "1"})
    except FileNotFoundError:
        pass
    return tasks
```

`split("|", 1)` — тек бірінші `|` бойынша бөледі, сондықтан тапсырма мәтінінде `|` болса да, ол бұзылмайды. `encoding="utf-8"` — қазақ әріптері (ә, ғ, қ, ң, ө, ұ, ү, һ, і) дұрыс сақталуы үшін **міндетті**.

> Күрделірек деректер үшін JSON (`json` модулі) және SQLite (`sqlite3` модулі) форматтары ыңғайлы. Мәтіндік формат — ең қарапайым нұсқа.

## 4. Терезе жабылғанда сақтау

Пайдаланушы «Сақтау» батырмасын басуды ұмытуы мүмкін. Сондықтан терезенің «×» батырмасын ұстаймыз:

```python
import tkinter as tk

def on_close():
    print("Деректер сақталды")
    root.destroy()

root = tk.Tk()
root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

`protocol("WM_DELETE_WINDOW", func)` — терезені жабу әрекетін өз функциямызға бағыттайды. Функция соңында `root.destroy()` шақыру міндетті, әйтпесе терезе жабылмайды.

## 5. Listbox-пен жұмыс

| Әдіс | Не істейді |
|---|---|
| `lb.insert(tk.END, "мәтін")` | Соңына жол қосады |
| `lb.delete(0, tk.END)` | Барлық жолды өшіреді |
| `lb.curselection()` | Таңдалған жолдардың индекстерінің кортежі, мысалы `(2,)` немесе `()` |
| `lb.itemconfig(i, fg="gray")` | i-жолдың түсін өзгертеді |
| `lb.bind("<Double-Button-1>", f)` | Екі рет басуға функция байланыстырады |

Таңдалған индексті алу үлгісі:

```python
def get_selected_index(listbox):
    selection = listbox.curselection()
    if not selection:
        return None
    return selection[0]
```

## 6. Толық үлгі: «To-Do List» қосымшасы

```python
import tkinter as tk
from tkinter import messagebox, simpledialog

FILENAME = "tasks.txt"


class TodoApp:
    """Тапсырмалар тізімі: қосу, белгілеу, өңдеу, өшіру, файлға сақтау."""

    def __init__(self, root):
        self.root = root
        self.root.title("Тапсырмалар тізімі")
        self.root.geometry("420x420")
        self.tasks = self.load_tasks()
        self.create_widgets()
        self.refresh_list()
        self.root.protocol("WM_DELETE_WINDOW", self.on_close)

    # ---------- Интерфейс ----------
    def create_widgets(self):
        top = tk.Frame(self.root)
        top.pack(fill="x", padx=10, pady=10)
        self.entry = tk.Entry(top, font=("Arial", 12))
        self.entry.pack(side="left", fill="x", expand=True)
        self.entry.bind("<Return>", lambda event: self.add_task())
        tk.Button(top, text="Қосу", command=self.add_task).pack(side="left", padx=5)

        middle = tk.Frame(self.root)
        middle.pack(fill="both", expand=True, padx=10)
        scrollbar = tk.Scrollbar(middle)
        scrollbar.pack(side="right", fill="y")
        self.listbox = tk.Listbox(middle, font=("Arial", 12),
                                  yscrollcommand=scrollbar.set)
        self.listbox.pack(side="left", fill="both", expand=True)
        scrollbar.config(command=self.listbox.yview)
        self.listbox.bind("<Double-Button-1>", lambda event: self.toggle_done())

        bottom = tk.Frame(self.root)
        bottom.pack(fill="x", padx=10, pady=5)
        tk.Button(bottom, text="Орындалды", command=self.toggle_done).pack(side="left")
        tk.Button(bottom, text="Өңдеу", command=self.edit_task).pack(side="left", padx=5)
        tk.Button(bottom, text="Өшіру", command=self.delete_task).pack(side="left")
        tk.Button(bottom, text="Орындалғандарды тазалау",
                  command=self.clear_done).pack(side="left", padx=5)

        self.status = tk.Label(self.root, text="", anchor="w")
        self.status.pack(fill="x", padx=10, pady=(0, 10))

    def refresh_list(self):
        self.listbox.delete(0, tk.END)
        for i, task in enumerate(self.tasks):
            mark = "✔" if task["done"] else "☐"
            self.listbox.insert(tk.END, f"{mark} {task['text']}")
            if task["done"]:
                self.listbox.itemconfig(i, fg="gray")
        done = sum(1 for t in self.tasks if t["done"])
        self.status.config(text=f"Орындалды: {done} / {len(self.tasks)}")

    def selected_index(self):
        selection = self.listbox.curselection()
        if not selection:
            messagebox.showwarning("Ескерту", "Алдымен тапсырманы таңдаңыз")
            return None
        return selection[0]

    # ---------- Логика ----------
    def add_task(self):
        text = self.entry.get().strip()
        if not text:
            messagebox.showwarning("Ескерту", "Тапсырма мәтіні бос болмауы керек")
            return
        self.tasks.append({"text": text, "done": False})
        self.entry.delete(0, tk.END)
        self.refresh_list()

    def toggle_done(self):
        index = self.selected_index()
        if index is None:
            return
        self.tasks[index]["done"] = not self.tasks[index]["done"]
        self.refresh_list()

    def edit_task(self):
        index = self.selected_index()
        if index is None:
            return
        new_text = simpledialog.askstring(
            "Өңдеу", "Жаңа мәтін:", initialvalue=self.tasks[index]["text"])
        if new_text and new_text.strip():
            self.tasks[index]["text"] = new_text.strip()
            self.refresh_list()

    def delete_task(self):
        index = self.selected_index()
        if index is None:
            return
        if messagebox.askyesno("Растау", "Тапсырманы өшіресіз бе?"):
            self.tasks.pop(index)
            self.refresh_list()

    def clear_done(self):
        self.tasks = [t for t in self.tasks if not t["done"]]
        self.refresh_list()

    # ---------- Деректер ----------
    @staticmethod
    def load_tasks():
        tasks = []
        try:
            with open(FILENAME, "r", encoding="utf-8") as f:
                for line in f:
                    line = line.rstrip("\n")
                    if "|" in line:
                        flag, text = line.split("|", 1)
                        tasks.append({"text": text, "done": flag == "1"})
        except FileNotFoundError:
            pass
        return tasks

    def save_tasks(self):
        with open(FILENAME, "w", encoding="utf-8") as f:
            for task in self.tasks:
                flag = "1" if task["done"] else "0"
                f.write(f"{flag}|{task['text']}\n")

    def on_close(self):
        self.save_tasks()
        self.root.destroy()


if __name__ == "__main__":
    root = tk.Tk()
    TodoApp(root)
    root.mainloop()
```

**Назар аударыңыз:**
- барлық өзгерістер `self.tasks`-та жасалады, содан кейін `refresh_list()` экранды жаңартады;
- `simpledialog.askstring()` — мәтін енгізуге арналған дайын диалог терезесі (`messagebox`-тың «туысы»);
- `[t for t in self.tasks if not t["done"]]` — тізім генераторы (list comprehension) арқылы сүзгілеу.

## 7. Тестілеу чек-парағы

| № | Әрекет | Нәтиже |
|---|---|---|
| 1 | Бос өріспен «Қосу» | Ескерту терезесі, тізім өзгермейді |
| 2 | «Кітап оқу» енгізіп Enter | Тізімде «☐ Кітап оқу» |
| 3 | Тапсырманы екі рет басу | «✔», сұр түс, санауыш өзгереді |
| 4 | Ештеңе таңдамай «Өшіру» | «Алдымен тапсырманы таңдаңыз» |
| 5 | Терезені жауып, қайта ашу | Барлық тапсырма мен күйлері сақталған |
| 6 | Қазақ әріптері бар тапсырма: «Әжеме қоңырау шалу» | Файлда да, экранда да дұрыс |

## Жиі кездесетін қателер

**1. Таңдау жоқ кезде индекс алу**
```python
def delete_task():
    index = listbox.curselection()[0]   # IndexError, егер ештеңе таңдалмаса
```
Дұрысы: алдымен `if not listbox.curselection(): return` тексеру.

**2. Деректерді тек Listbox-та сақтау.** Мәтінді Listbox-тан қайта «оқып», ✔ белгісін қиып алу — қате көзі. Деректер тізімде, Listbox — тек көрініс.

**3. Кодировканы көрсетпеу**
```python
f = open("tasks.txt", "w")   # Windows-та қазақ әріптері бұзылуы мүмкін
```
Дұрысы: `open("tasks.txt", "w", encoding="utf-8")`.

**4. `on_close` ішінде `destroy()` жоқ** — терезе жабылмайды, пайдаланушы «бағдарлама қатып қалды» деп ойлайды.

**5. Файл жоқ кезде құлау** — алғашқы іске қосуда `tasks.txt` жоқ. `FileNotFoundError` өңделмесе, бағдарлама ашылмайды.

## Негізгі ұғымдар

| Ұғым | Түсіндірме |
|---|---|
| Деректерді сақтау (персистенттілік) | Бағдарлама жабылғаннан кейін де деректердің сақталуы |
| Модель / көрініс | Деректер құрылымы / оның экрандағы бейнесі |
| `curselection()` | Listbox-та таңдалған жолдардың индекстері |
| `protocol("WM_DELETE_WINDOW")` | Терезені жабу оқиғасын ұстау |
| `simpledialog.askstring` | Мәтін сұрайтын стандарт диалог |
| `encoding="utf-8"` | Ұлттық әріптерді дұрыс сақтайтын кодтау |
| Тізім генераторы | `[x for x in ... if ...]` түріндегі қысқа жазба |

## Ескерту

- Файлды әрқашан `encoding="utf-8"` параметрімен ашыңыз: әдепкі кодтау операциялық жүйеге байланысты, Windows-та онсыз қазақ әріптері бұзылуы мүмкін.
- Тапсырма мәтінінің ішінде де `|` белгісі болуы мүмкін. Сондықтан жолды `line.split("|", 1)` деп тек бірінші `|` бойынша бөліңіз.
- `root.protocol("WM_DELETE_WINDOW", on_close)` орнатылса, терезе өздігінен жабылмайды — `on_close` ішінде міндетті түрде `root.destroy()` шақырыңыз.
- `Listbox.curselection()` кортеж (tuple) қайтарады; ештеңе таңдалмаса — бос кортеж `()`. Индексті алмас бұрын тексеріңіз.
- Сақтау кезінде файл бүлінбеуі үшін алдымен уақытша файлға жазып, кейін `os.replace(tmp, path)` арқылы ауыстыруға болады — бұл ауыстыру атомарлы түрде орындалады.
- `open()` функциясының құжаттамасы: https://docs.python.org/3/library/functions.html#open
