# Сабақ 22. Негізгі терезе құру. Терезе параметрлері мен қасиеттері.

## Тақырыптар

1. Басты терезе — `Tk` класының объектісі
2. Экрандағы координаттар жүйесі
3. `geometry` — өлшем және орын
4. Өлшемді шектеу
5. Фон түсі және `config`
6. `attributes` — арнайы қасиеттер
7. Белгіше (иконка)
8. Терезені экран ортасына қою
9. Терезені жабу
10. Қосымша терезе — `Toplevel`
11. ОБП стилі: `tk.Tk` класынан мұра алу
12. Терезе әдістерінің жиынтық кестесі
13. Жиі кездесетін қателер
14. Негізгі ұғымдар

## 1. Басты терезе — `Tk` класының объектісі

```python
import tkinter as tk

root = tk.Tk()
root.title("Ауа райы")         # тақырып
root.geometry("400x300")       # ені x биіктігі (пиксель)
root.mainloop()
```

`root` — «түбір» деген мағына береді: барлық виджеттер осы терезенің ішінде «өседі».

## 2. Экрандағы координаттар жүйесі

Компьютер экранында координаттардың басы (0, 0) — **жоғарғы сол жақ бұрыш**. X оңға қарай, Y **төмен** қарай артады (математикадан айырмашылығы!).

```text
(0,0) ───────────────► X
  │
  │     ┌─────────────┐
  │     │  терезе     │  (X=100, Y=50) — терезенің сол жақ жоғарғы бұрышы
  │     └─────────────┘
  ▼
  Y
```

## 3. `geometry` — өлшем және орын

Формат: `"ЕНІxБИІКТІГІ+X+Y"` (x — латынның кіші әрпі).

| Жазба | Мағынасы |
|---|---|
| `"400x300"` | Өлшемі 400×300, орны — жүйе таңдайды |
| `"400x300+100+50"` | Өлшемі 400×300, экранның сол жағынан 100, жоғарыдан 50 пиксель |
| `"+0+0"` | Тек орын: жоғарғы сол жақ бұрыш |
| `"400x300-0-0"` | Минус — оң/төменгі шеттен санау: оң жақ төменгі бұрыш |

```python
import tkinter as tk

root = tk.Tk()
root.title("Орын")
root.geometry("300x200+100+50")
root.mainloop()
```

## 4. Өлшемді шектеу

```python
import tkinter as tk

root = tk.Tk()
root.title("Шектеулер")
root.geometry("400x300")
root.resizable(False, True)    # ені өзгермейді, биіктігі өзгереді
root.minsize(400, 200)         # ең кіші өлшем
root.maxsize(400, 600)         # ең үлкен өлшем
root.mainloop()
```

| Әдіс | Мағынасы |
|---|---|
| `resizable(w, h)` | Ені/биіктігі бойынша өзгертуге рұқсат (`True`/`False`) |
| `minsize(w, h)` | Ең кіші өлшем |
| `maxsize(w, h)` | Ең үлкен өлшем |

## 5. Фон түсі және `config`

Кез келген виджеттің (терезенің де) қасиетін `config()` (немесе `configure()`) әдісімен өзгертеміз:

```python
import tkinter as tk

root = tk.Tk()
root.title("Түстер")
root.geometry("300x150")
root.config(bg="#1e90ff")      # фон — көк

tk.Label(root, text="Көк аспан", bg="#1e90ff", fg="white",
         font=("Arial", 18)).pack(pady=40)
root.mainloop()
```

Түсті беру тәсілдері:
- атауы: `"red"`, `"white"`, `"lightgreen"`, `"gold"`, `"skyblue"`;
- HEX коды: `"#RRGGBB"` — қызыл, жасыл, көк үлестері 00–FF (16-лық санау жүйесі). Мысалы `"#00AFCA"` — Қазақстан туының көгілдір түсі, `"#FEC50C"` — сары.

| HEX | Түс |
|---|---|
| `#FF0000` | қызыл |
| `#00FF00` | жасыл |
| `#0000FF` | көк |
| `#FFFFFF` | ақ |
| `#000000` | қара |
| `#808080` | сұр |

## 6. `attributes` — арнайы қасиеттер

```python
import tkinter as tk

root = tk.Tk()
root.title("Ерекше терезе")
root.geometry("300x150")
root.attributes("-alpha", 0.85)     # мөлдірлік: 0.0 (көрінбейді) ... 1.0 (толық)
root.attributes("-topmost", True)   # барлық терезелердің үстінде тұрады
# root.attributes("-fullscreen", True)  # толық экран (Esc-пен шығуды ойластырыңыз!)
tk.Label(root, text="Мен әрқашан үстіндемін").pack(pady=50)
root.mainloop()
```

## 7. Белгіше (иконка)

```python
import tkinter as tk

root = tk.Tk()
root.title("Белгіше")
try:
    icon = tk.PhotoImage(file="icon.png")   # PNG немесе GIF
    root.iconphoto(True, icon)
except tk.TclError:
    print("icon.png табылмады — әдепкі белгіше қалады")
root.mainloop()
```

Windows-та `.ico` файлы үшін `root.iconbitmap("icon.ico")` қолданылады. `iconphoto` барлық жүйеде жұмыс істейді.

## 8. Терезені экран ортасына қою

Экран өлшемін `winfo_screenwidth()` және `winfo_screenheight()` береді:

```python
import tkinter as tk

root = tk.Tk()
root.title("Ортада")
w, h = 400, 250
screen_w = root.winfo_screenwidth()
screen_h = root.winfo_screenheight()
x = (screen_w - w) // 2
y = (screen_h - h) // 2
root.geometry(f"{w}x{h}+{x}+{y}")
tk.Label(root, text=f"Экран: {screen_w}x{screen_h}").pack(pady=100)
root.mainloop()
```

Мысалы, экран 1920×1080 болса: x = (1920 − 400) // 2 = 760, y = (1080 − 250) // 2 = 415.

## 9. Терезені жабу

- `root.destroy()` — терезені жабады және `mainloop()` аяқталады.
- `root.protocol("WM_DELETE_WINDOW", func)` — пайдаланушы «✕» батырмасын басқанда `func` шақырылады.

```python
import tkinter as tk


def on_close():
    print("Бағдарлама жабылды. Сау болыңыз!")
    root.destroy()


root = tk.Tk()
root.title("Жабу")
root.geometry("250x120")
tk.Button(root, text="Шығу", command=root.destroy).pack(pady=40)
root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

`on_close` ішінде растау сұрауға болады: `if messagebox.askyesno("Шығу", "Шынымен шығасыз ба?"): root.destroy()` (`tkinter.messagebox` модулі).

## 10. Қосымша терезе — `Toplevel`

Бір бағдарламада бір ғана `Tk()` болады. Екінші терезе — `Toplevel`:

```python
import tkinter as tk


def open_about():
    win = tk.Toplevel(root)
    win.title("Бағдарлама туралы")
    win.geometry("250x100+150+150")
    tk.Label(win, text="Нұсқа 1.0\nАвторы: Python үйірмесі").pack(pady=10)
    tk.Button(win, text="Жабу", command=win.destroy).pack()


root = tk.Tk()
root.title("Басты терезе")
root.geometry("300x150")
tk.Button(root, text="Бағдарлама туралы", command=open_about).pack(pady=50)
root.mainloop()
```

## 11. ОБП стилі: `tk.Tk` класынан мұра алу

Үлкен қосымшаларда терезені класс ретінде жазу ыңғайлы — мұнда мұрагерлік пен `super()` қажет болады:

```python
import tkinter as tk


class App(tk.Tk):
    def __init__(self):
        super().__init__()                    # Tk конструкторы
        self.title("Менің қосымшам")
        self.geometry("350x200")
        self.config(bg="#f0f4f8")
        self.resizable(False, False)
        self.create_widgets()

    def create_widgets(self):
        self.label = tk.Label(self, text="Қош келдіңіз!", bg="#f0f4f8",
                              font=("Arial", 16))
        self.label.pack(pady=30)
        tk.Button(self, text="Түсті өзгерту", command=self.change_color).pack()

    def change_color(self):
        self.config(bg="lightyellow")
        self.label.config(bg="lightyellow")


if __name__ == "__main__":
    app = App()
    app.mainloop()
```

Мұнда `self` — терезенің өзі, сондықтан `self.title(...)`, `tk.Label(self, ...)`.

## 12. Терезе әдістерінің жиынтық кестесі

| Әдіс | Мысал | Мағынасы |
|---|---|---|
| `title()` | `root.title("Атау")` | Тақырып |
| `geometry()` | `root.geometry("400x300+100+50")` | Өлшем және орын |
| `resizable()` | `root.resizable(False, False)` | Өлшемін өзгертуге тыйым |
| `minsize()` / `maxsize()` | `root.minsize(200, 100)` | Шектік өлшемдер |
| `config(bg=...)` | `root.config(bg="white")` | Фон түсі |
| `attributes()` | `root.attributes("-alpha", 0.9)` | Мөлдірлік, үстінде тұру, толық экран |
| `iconphoto()` | `root.iconphoto(True, img)` | Белгіше |
| `winfo_screenwidth()` | `root.winfo_screenwidth()` | Экран ені |
| `protocol()` | `root.protocol("WM_DELETE_WINDOW", f)` | Жабу оқиғасы |
| `destroy()` | `root.destroy()` | Терезені жабу |

## Жиі кездесетін қателер

**1. `geometry`-де `x` орнына `*` немесе кириллица «х»**

```python
import tkinter as tk
root = tk.Tk()
# root.geometry("400*300")   -> TclError: bad geometry specifier
# root.geometry("400х300")   -> кириллица «х» — да қате!
root.geometry("400x300")      # латынның x әрпі
root.mainloop()
```

**2. Сандарды `geometry`-ге жол емес түрде беру** — `root.geometry(400, 300)` → `TypeError`. Дұрысы — f-жол: `root.geometry(f"{w}x{h}")`.

**3. Терезе фонын өзгертіп, жазулардың фонын өзгертпеу** — жазулар сұр «жамау» болып көрінеді. Әр виджетке де `bg` беріңіз.

**4. Екінші терезені `tk.Tk()` арқылы ашу** — суреттер мен айнымалылар дұрыс жұмыс істемеуі мүмкін. `Toplevel` қолданыңыз.

**5. `super().__init__()` ұмыту** (ОБП стилінде)

```python
import tkinter as tk

class App(tk.Tk):
    def __init__(self):
        # super().__init__() жоқ — self.title(...) шақыру AttributeError береді
        pass
```

**6. Толық экран режимінен шығу жолын қалдырмау** — `-fullscreen` қоссаңыз, жабу батырмасын немесе `Esc` пернесін қосуды ұмытпаңыз: `root.bind("<Escape>", lambda e: root.attributes("-fullscreen", False))`.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| Басты терезе (root) | `tk.Tk()` объектісі, бағдарламадағы жалғыз негізгі терезе |
| Экран координаттары | Басы — жоғарғы сол жақ бұрыш, Y төмен қарай өседі |
| `geometry` | Терезенің өлшемі мен орны: `"ЕНІxБИІКТІГІ+X+Y"` |
| HEX түс коды | `#RRGGBB` түріндегі түс жазбасы |
| `Toplevel` | Қосымша (бағынышты) терезе |
| Протокол | Терезе менеджерінің оқиғасы (мыс. `WM_DELETE_WINDOW`) |

## Ескерту

- Терезе алғаш сызылғанға дейін `winfo_width()` пен `winfo_height()` `1` қайтарады; нақты өлшемді алу үшін алдымен `root.update_idletasks()` шақырыңыз.
- `attributes` қолдауы ОЖ-ға байланысты: `-toolwindow` тек Windows-та бар, `-alpha` Linux-тың барлық терезе менеджерлерінде жұмыс істемейді.
- `tk.PhotoImage` PNG мен GIF форматтарын түсінеді (PNG — Tk 8.6-дан бастап); JPG және басқа форматтар үшін Pillow кітапханасы керек (`from PIL import ImageTk`).
- `PhotoImage` сілтемесін міндетті түрде айнымалыда немесе атрибутта сақтаңыз: сурет объектісін қоқыс жинағыш жойса, белгіше немесе сурет жоғалады.
- Windows-та жоғары DPI экрандарда терезе бұлыңғыр көрінуі мүмкін; оны `Tk()` құрмас бұрын `ctypes.windll.shcore.SetProcessDpiAwareness(1)` шақыру түзетеді (тек Windows).
