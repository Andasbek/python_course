# Сабақ 97. Жобаны әзірлеу: пайдаланушы интерфейсін жүзеге асыру.

## Тақырыптар

1. Интеграция дегеніміз не?
2. Бір оқиғаның толық тізбегі
3. Логиканың интерфейсі (еске салу)
4. Жоба мысалы: `gui.py`
5. `main.py` — бәрін жинау
6. Ұзақ әрекеттер: терезе «қатып қалмасын»
7. Pygame жобасында біріктіру
8. Интеграциялық тексеру және MVP
9. Жиі кездесетін қателер
10. Негізгі ұғымдар

## 1. Интеграция дегеніміз не?

**Интеграция (интеграция, integration)** — бөлек жазылған бөліктерді бір жұмыс істейтін бағдарламаға біріктіру. Әр бөлік жеке жұмыс істегенімен, бірге қосқанда жаңа қателер шығуы мүмкін: функция басқа түрдегі мән күтеді, атаулар сәйкес келмейді, деректер жаңармайды.

**Аналогия:** велосипедтің дөңгелегі, рамасы, тізбегі бөлек тамаша болуы мүмкін, бірақ оларды жинағанда болттардың өлшемі сәйкес келуі керек. Біздің «болттар» — функциялардың **параметрлері мен қайтаратын мәндері** (интерфейстері).

Біріктіру реті:
```text
main.py
  │ құрады
  ├──> Database("qalta.db")            (деректер қабаты)
  ├──> Wallet(db)                      (логика)
  └──> App(wallet) ──> mainloop()      (интерфейс)
```

Әр қабат тек **төменгі** қабатты біледі: `App` → `Wallet` → `Database`. Керісінше емес.

## 2. Бір оқиғаның толық тізбегі

Пайдаланушы «Сақтау» батырмасын басқанда не болады:

```text
[Батырма басылды]
      │
      v
App.on_save()           1. өрістерден мәтінді алады
      │                 2. мәтінді санға түрлендіреді (қате -> хабарлама)
      v
Wallet.add_expense()    3. ережелерді тексереді (қате -> ValueError)
      │
      v
Database.add_operation() 4. ДҚ-ға жазады
      │
      v
App.refresh()           5. баланс, кесте, диаграмманы жаңартады
      │
      v
[Пайдаланушы нәтижені көрді: «Сақталды»]
```

**Ереже:** деректер өзгерген сайын экранның **бәрін бір жерден** жаңартамыз — `refresh()` әдісі. Әйтпесе бір жерде баланс жаңарып, кестеде ескі деректер қалады.

## 3. Логиканың интерфейсі (еске салу)

Интерфейс шақыратын логика әдістері (қысқаша):

```python
class Wallet:
    """Интерфейс қолданатын әдістер тізімі (қысқаша)."""

    def __init__(self, db):
        self.db = db

    def categories(self, kind):
        return self.db.categories(kind)

    def add_operation(self, category_id, amount, op_date, note=""):
        if amount <= 0:
            raise ValueError("Сома оң болуы керек")
        return self.db.add_operation(category_id, amount, op_date, note)

    def delete_operation(self, op_id):
        return self.db.delete_operation(op_id)

    def operations(self, month=None):
        return self.db.operations(month)

    def balance(self, month=None):
        return sum(op["amount"] if op["kind"] == "кіріс" else -op["amount"]
                   for op in self.db.operations(month))

    def expenses_by_category(self, month=None):
        totals = {}
        for op in self.db.operations(month):
            if op["kind"] == "шығыс":
                totals[op["category"]] = totals.get(op["category"], 0) + op["amount"]
        return totals
```

## 4. Жоба мысалы: `gui.py`

Интерфейсті **класс** түрінде жазамыз (ОБП): виджеттер — атрибуттар, оқиғаларды өңдеушілер — әдістер. Бұл ғаламдық айнымалылардан құтқарады.

```python
"""gui.py — «Қалта ақшасы» терезелік интерфейсі."""
import datetime
import tkinter as tk
from tkinter import ttk, messagebox

from matplotlib.figure import Figure
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg


class App(tk.Tk):
    """Басты терезе. Логиканы (wallet) сырттан алады."""

    def __init__(self, wallet):
        super().__init__()
        self.wallet = wallet
        self.title("Қалта ақшасы")
        self.geometry("820x480")
        self.minsize(700, 420)
        self._build_top()
        self._build_form()
        self._build_table()
        self._build_chart()
        self.refresh()

    # ---------- Виджеттерді құру ----------
    def _build_top(self):
        top = ttk.Frame(self, padding=10)
        top.grid(row=0, column=0, columnspan=2, sticky="ew")
        self.balance_label = ttk.Label(top, font=("Arial", 18, "bold"))
        self.balance_label.pack(side="left")

    def _build_form(self):
        form = ttk.LabelFrame(self, text="Жаңа операция", padding=10)
        form.grid(row=1, column=0, sticky="nw", padx=10)

        self.kind_var = tk.StringVar(value="шығыс")
        for i, kind in enumerate(("шығыс", "кіріс")):
            ttk.Radiobutton(form, text=kind.capitalize(), value=kind,
                            variable=self.kind_var,
                            command=self._update_categories).grid(row=0, column=i, sticky="w")

        ttk.Label(form, text="Сома (тг):").grid(row=1, column=0, sticky="w", pady=4)
        self.amount_entry = ttk.Entry(form, width=18)
        self.amount_entry.grid(row=1, column=1, pady=4)

        ttk.Label(form, text="Санат:").grid(row=2, column=0, sticky="w", pady=4)
        self.category_box = ttk.Combobox(form, state="readonly", width=16)
        self.category_box.grid(row=2, column=1, pady=4)

        ttk.Label(form, text="Түсініктеме:").grid(row=3, column=0, sticky="w", pady=4)
        self.note_entry = ttk.Entry(form, width=18)
        self.note_entry.grid(row=3, column=1, pady=4)

        ttk.Button(form, text="Сақтау", command=self.on_save).grid(
            row=4, column=1, sticky="e", pady=8)
        ttk.Button(form, text="Таңдалғанды жою", command=self.on_delete).grid(
            row=5, column=0, columnspan=2, sticky="ew")
        self.status = ttk.Label(form, foreground="#2e7d32")
        self.status.grid(row=6, column=0, columnspan=2, sticky="w", pady=6)

        self.amount_entry.bind("<Return>", lambda event: self.on_save())
        self._update_categories()

    def _build_table(self):
        self.table = ttk.Treeview(self, columns=("date", "cat", "sum", "note"),
                                  show="headings", height=10)
        for col, title, width in (("date", "Күні", 90), ("cat", "Санат", 110),
                                  ("sum", "Сома", 80), ("note", "Түсініктеме", 150)):
            self.table.heading(col, text=title)
            self.table.column(col, width=width)
        self.table.grid(row=2, column=0, sticky="nsew", padx=10, pady=10)
        self.rowconfigure(2, weight=1)
        self.columnconfigure(1, weight=1)

    def _build_chart(self):
        self.figure = Figure(figsize=(3.6, 3.2), dpi=90)
        self.axes = self.figure.add_subplot()
        self.canvas = FigureCanvasTkAgg(self.figure, master=self)
        self.canvas.get_tk_widget().grid(row=1, column=1, rowspan=2,
                                         sticky="nsew", padx=10, pady=10)

    # ---------- Оқиғаларды өңдеу ----------
    def _update_categories(self):
        self._categories = self.wallet.categories(self.kind_var.get())
        self.category_box["values"] = [c["title"] for c in self._categories]
        self.category_box.current(0)

    def on_save(self):
        text = self.amount_entry.get().strip().replace(" ", "")
        if not text.isdigit():
            messagebox.showwarning("Қате", "Соманы бүтін сан түрінде енгізіңіз, мысалы: 800")
            self.amount_entry.focus_set()
            return
        category = self._categories[self.category_box.current()]
        try:
            self.wallet.add_operation(category["id"], int(text),
                                      datetime.date.today().isoformat(),
                                      self.note_entry.get().strip())
        except ValueError as err:          # логика ережесі бұзылды
            messagebox.showwarning("Қате", str(err))
            return
        except Exception as err:           # күтпеген қате: бағдарлама құламасын
            messagebox.showerror("Қате", f"Сақтау мүмкін болмады:\n{err}")
            return
        self.amount_entry.delete(0, "end")
        self.note_entry.delete(0, "end")
        self.status.config(text=f"Сақталды: {text} тг, {category['title']}")
        self.refresh()

    def on_delete(self):
        selected = self.table.selection()
        if not selected:
            messagebox.showinfo("Жою", "Алдымен кестеден операцияны таңдаңыз.")
            return
        if messagebox.askyesno("Растау", "Таңдалған операцияны жоясыз ба?"):
            self.wallet.delete_operation(int(selected[0]))   # iid = операция id-і
            self.status.config(text="Жойылды")
            self.refresh()

    # ---------- Экранды жаңарту ----------
    def refresh(self):
        """Деректер өзгерген сайын барлық көріністі жаңартады."""
        month = datetime.date.today().strftime("%Y-%m")
        balance = self.wallet.balance()
        self.balance_label.config(text=f"Баланс: {balance:,} тг".replace(",", " "))

        self.table.delete(*self.table.get_children())
        for op in self.wallet.operations():
            sign = "+" if op["kind"] == "кіріс" else "−"
            self.table.insert("", "end", iid=str(op["id"]), values=(
                op["op_date"], op["category"], f"{sign}{op['amount']}", op["note"]))

        totals = self.wallet.expenses_by_category(month)
        self.axes.clear()
        if totals:
            self.axes.pie(list(totals.values()), labels=list(totals.keys()),
                          autopct="%1.0f%%")
            self.axes.set_title("Осы айдың шығыстары")
        else:
            self.axes.text(0.5, 0.5, "Осы айда шығыс жоқ", ha="center", va="center")
            self.axes.axis("off")
        self.canvas.draw()
```

Түсініктемелер:
- `class App(tk.Tk)` — мұрагерлік: терезенің өзі — біздің класымыздың объектісі.
- `_build_...` әдістері (атауы `_`-пен басталады) — ішкі әдістер; терезені бөліктерге бөледі.
- Кестедегі әр жолдың `iid`-і — операцияның ДҚ-дағы `id`-і. Сондықтан таңдалған жолды жою оңай.
- `FigureCanvasTkAgg` — Matplotlib суретін Tkinter виджетіне айналдырады (диаграмма тікелей терезенің ішінде көрсетіледі).
- Екі деңгейлі қатені өңдеу: `ValueError` — пайдаланушыға түсінікті ереже; `Exception` — күтпеген қате, бірақ бағдарлама жабылмайды.

## 5. `main.py` — бәрін жинау

```python
"""main.py — «Қалта ақшасы» іске қосу нүктесі."""
from pathlib import Path

from qalta.database import Database
from qalta.logic import Wallet
from qalta.gui import App

DB_PATH = Path(__file__).resolve().parent / "qalta.db"


def main():
    db = Database(DB_PATH)
    wallet = Wallet(db)
    app = App(wallet)
    app.protocol("WM_DELETE_WINDOW", lambda: (db.close(), app.destroy()))
    app.mainloop()


if __name__ == "__main__":
    main()
```

`protocol("WM_DELETE_WINDOW", ...)` — терезенің «×» батырмасы басылғанда ДҚ қосылымын дұрыс жабады.

## 6. Ұзақ әрекеттер: терезе «қатып қалмасын»

API сұранысы 1–5 секунд алуы мүмкін. Осы уақытта `mainloop` тоқтайды да, терезе «жауап бермейді». Қарапайым шешім — сұранысты бағдарлама ашылғаннан кейін, `after()` арқылы бастау және нәтижені күткенше «Жүктелуде...» деп көрсету. Толығырақ шешім — бөлек ағын (`threading`):

```python
import threading
import tkinter as tk


def slow_request():
    """API сұранысының орнына: 2 секунд күтеміз."""
    import time
    time.sleep(2)
    return 512.3


class RateLabel(tk.Label):
    def __init__(self, master):
        super().__init__(master, text="Бағам: жүктелуде...")
        threading.Thread(target=self._load, daemon=True).start()

    def _load(self):
        rate = slow_request()                      # бөлек ағында
        self.after(0, lambda: self.config(text=f"1 USD = {rate} тг"))  # экранды тек басты ағын өзгертеді


if __name__ == "__main__":
    root = tk.Tk()
    RateLabel(root).pack(padx=20, pady=20)
    root.mainloop()
```

> **Маңызды:** Tkinter виджеттерін тек басты ағыннан өзгертуге болады — сондықтан нәтижені `after(0, ...)` арқылы береміз.

## 7. Pygame жобасында біріктіру

Ойында интерфейс — ойын циклі мен сахналар; логика — `Player`, `GameState`; деректер — рекордтар JSON-ы.

```python
import pygame


class Player:
    def __init__(self):
        self.lives, self.score = 3, 0


class Game:
    """Ойын циклі: оқиға -> логика -> салу."""

    def __init__(self):
        pygame.init()
        self.screen = pygame.display.set_mode((640, 400))
        pygame.display.set_caption("Жобаның ойыны")
        self.font = pygame.font.SysFont("arial", 24)
        self.clock = pygame.time.Clock()
        self.player = Player()
        self.state = "menu"

    def handle_events(self):
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                return False
            if event.type == pygame.KEYDOWN:
                if self.state == "menu" and event.key == pygame.K_RETURN:
                    self.state = "game"
                elif self.state == "game" and event.key == pygame.K_SPACE:
                    self.player.score += 10            # логика
        return True

    def draw(self):
        self.screen.fill((20, 24, 40))
        if self.state == "menu":
            text = "ENTER - бастау"
        else:
            text = f"Ұпай: {self.player.score}   Өмір: {self.player.lives}"
        self.screen.blit(self.font.render(text, True, (255, 255, 255)), (20, 20))
        pygame.display.flip()

    def run(self):
        running = True
        while running:
            running = self.handle_events()
            self.draw()
            self.clock.tick(60)
        pygame.quit()


if __name__ == "__main__":
    Game().run()
```

Құрылымы Tkinter-дегідей: **оқиға → логиканы шақыру → экранды қайта салу**.

## 8. Интеграциялық тексеру және MVP

Біріктіргеннен кейін ТТ-дағы **әр негізгі сценарийді** басынан аяғына дейін қолмен өтеміз:

| № | Сценарий | Қадамдар | Күтілген нәтиже | ✓ |
|---|---|---|---|---|
| 1 | Шығыс қосу | 800, «Тамақ», Сақтау | кестеде жол, баланс −800, диаграмма жаңарды | |
| 2 | Қате сома | «абв», Сақтау | ескерту, ештеңе сақталмады | |
| 3 | Жою | жолды таңдау, Жою, Иә | жол жоқ, баланс қалпына келді | |
| 4 | Қайта ашу | жабу → ашу | барлық дерек орнында | |
| 5 | Бос ДҚ | ДҚ файлын өшіріп ашу | бос кесте, «шығыс жоқ» мәтіні | |

Барлық Must-сценарий өтсе — **MVP дайын**. Оны Git-те белгілеп қоямыз:
```bash
git add .
git commit -m "MVP: интерфейс логика мен ДҚ-ға қосылды"
git tag mvp
git push --tags
```

## Жиі кездесетін қателер

**1. `command=self.on_save()` — жақшамен.** Функция терезе құрылғанда бір рет шақырылады, батырма басқанда — ештеңе. Дұрысы: `command=self.on_save` (жақшасыз) немесе `lambda: ...`.

**2. Деректерді өзгертіп, экранды жаңартпау.** ДҚ-да жазба бар, бірақ кестеде жоқ. Дұрысы: әр өзгерістен кейін `self.refresh()`.

**3. Интерфейсте логиканы қайталау.** `gui.py` ішінде балансты қайта есептеу. Дұрысы: `self.wallet.balance()` шақыру — есептеу бір жерде.

**4. `Treeview`-ді тазаламай толтыру** — әр жаңартуда жолдар екі еселенеді. Дұрысы: `self.table.delete(*self.table.get_children())`.

**5. Импорт қатесі.** `ModuleNotFoundError: No module named 'qalta'` — `main.py`-ді жоба папкасынан тыс іске қосу немесе `qalta/__init__.py` жоқ. Дұрысы: жоба түбірінен `python main.py`.

**6. Бос кестеде/таңдаусыз әрекет** — `selection()[0]` → `IndexError`. Дұрысы: алдымен `if not selected:` тексеру.

## Негізгі ұғымдар
| Ұғым | Түсіндірме |
|---|---|
| Интеграция | бөлек модульдерді бір жұмыс істейтін жүйеге біріктіру |
| Оқиғаны өңдеуші | батырма, перне т.б. оқиғаға жауап беретін әдіс |
| `refresh()` | деректер өзгергенде барлық көріністі жаңартатын әдіс |
| `FigureCanvasTkAgg` | Matplotlib суретін Tkinter-ге орналастыратын класс |
| Ағын (thread) | бағдарламаның қатар орындалатын бөлігі |
| Интеграциялық тексеру | сценарийлерді бүкіл жүйе арқылы басынан аяғына дейін өту |
| MVP белгісі (`git tag`) | репозиторийдегі маңызды нүктенің атауы |

## Ескерту

- Tkinter ішінде `matplotlib`-тің `pyplot`-ын емес, `matplotlib.figure.Figure`-ді қолданыңыз: `plt.figure()` терезелері `pyplot`-тың ғаламдық күйінде өмір сүреді және сіздің терезеңіз жабылғанда жадты босатпайды.
- Matplotlib терезесі Tkinter-ден бөлек ашылса, бэкендті тексеріңіз: Tkinter-мен `matplotlib.backends.backend_tkagg` ішіндегі `FigureCanvasTkAgg` қолданылады.
- `ttk.Treeview` жол мәндерін жол (str) ретінде сақтайды: `table.item(iid, "values")` `('2027-04-27', 'Тамақ', '-900', '')` қайтарады — сандарды `int()` арқылы қайта түрлендіру керек.
- `widget.after(ms, func)` — Tkinter-дің негізгі ағынында әрекетті жоспарлаудың қауіпсіз тәсілі; өңдеушідегі `time.sleep()` бүкіл терезені қатырады.
- Ауыр импорттар (`matplotlib`) іске қосылуды 1–2 секундқа баяулатады — бұл қалыпты; қаласаңыз, оларды диаграмма салатын функцияның ішінде импорттаңыз.
- Құжаттама: https://docs.python.org/3/library/tkinter.html және https://matplotlib.org/stable/gallery/user_interfaces/embedding_in_tk_sgskip.html
