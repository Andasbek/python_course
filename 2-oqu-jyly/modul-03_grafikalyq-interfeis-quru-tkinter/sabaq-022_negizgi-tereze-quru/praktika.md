# Сабақ 22. Практикалық тапсырмалар: басты терезе және оның параметрлері

## Базалық деңгей

### 1-тапсырма. Визит-карточка терезесі
Тақырыбы «Визит-карточка», өлшемі 350×200, экранның сол жағынан 200, жоғарыдан 100 пиксель қашықтықта тұрған терезе жасаңыз. Фоны — ашық көк (`#e6f2ff`). Ішінде атыңыз, үйірме атауы және қалаңыз жазылсын (жазулардың фоны тереземен бірдей болсын).

### 2-тапсырма. Өзгермейтін терезе
400×300 терезе: өлшемін өзгертуге мүлдем болмайды. Терезе әрқашан басқа терезелердің үстінде тұрсын және мөлдірлігі 90% болсын.

### 3-тапсырма. Ортадағы терезе
Кез келген экранда дәл ортада ашылатын 500×300 терезе жасаңыз. Терезеге экранның өлшемін және терезенің координаттарын жазыңыз:
```text
Экран: 1920 x 1080
Терезе орны: x=710, y=390
```

### 4-тапсырма. Қазақстан туы түстері
Терезенің фоны `#00AFCA` (көгілдір), ортасында сары (`#FEC50C`) түсті «Қазақстан» жазуы болсын, шрифт — 28, қалың (`"bold"`).

## Кеңейтілген деңгей

### 5-тапсырма. Түс ауыстырғыш
Терезеде «Қызыл», «Жасыл», «Көк», «Кездейсоқ» батырмалары болсын. Батырмалар терезенің фон түсін өзгертсін. «Кездейсоқ» — кездейсоқ HEX түс (`"#%06x" % random.randint(0, 0xFFFFFF)`) және оның кодын тақырыпқа (`title`) жазсын.

### 6-тапсырма. Өлшем өзгерткіш
«Үлкейту» және «Кішірейту» батырмалары терезені 50 пиксельге үлкейтсін/кішірейтсін (ең кіші 200×150, ең үлкен 800×600). Ағымдағы өлшем жазуда көрсетілсін. Кеңес: өлшемді айнымалыда сақтап, `geometry(f"{w}x{h}")` шақырыңыз.

### 7-тапсырма. «Бағдарлама туралы» терезесі
Басты терезеде «Анықтама» батырмасы `Toplevel` терезесін ашсын (300×150, өлшемі өзгермейді). Онда бағдарлама нұсқасы, автор және «Жабу» батырмасы болсын. Басты терезенің «✕» батырмасы басылғанда консольге «Бағдарлама аяқталды» шықсын.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. ОБП стиліндегі қосымша
`tk.Tk`-тан мұра алатын `ThemeApp` класын жазыңыз. Конструктор `theme` параметрін қабылдасын (`"light"` немесе `"dark"`). Сөздікте екі тақырыптың түстері сақталсын (`bg`, `fg`). «Тақырыпты ауыстыру» батырмасы терезе мен барлық жазулардың түсін ауыстырсын. Терезе экран ортасында ашылсын.

## Үй тапсырмасы

1. 8-тапсырманы (немесе 5-ін) аяқтап, GitHub-қа `gui/lesson22_window.py` деп жүктеңіз.
2. Өзіңізге ұнайтын 5 түстің HEX кодын табыңыз (мысалы, colorhunt.co сайтынан) және оларды бір терезеде батырмалар арқылы көрсетіңіз.
3. Ойланыңыз: терезеге мәтін енгізу өрісін қалай қосуға болады?

## Педагогке: шешімдер

<details><summary>Шешімдерді көрсету</summary>

**1-тапсырма**
```python
import tkinter as tk

BG = "#e6f2ff"
root = tk.Tk()
root.title("Визит-карточка")
root.geometry("350x200+200+100")
root.config(bg=BG)
tk.Label(root, text="Ару Серікқызы", bg=BG, font=("Arial", 18, "bold")).pack(pady=(30, 5))
tk.Label(root, text="«Python класс Программирования» үйірмесі", bg=BG).pack()
tk.Label(root, text="Алматы", bg=BG).pack()
root.mainloop()
```

**2-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Өзгермейтін терезе")
root.geometry("400x300")
root.resizable(False, False)
root.attributes("-topmost", True)
root.attributes("-alpha", 0.9)
root.mainloop()
```

**3-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Ортада")
w, h = 500, 300
sw, sh = root.winfo_screenwidth(), root.winfo_screenheight()
x, y = (sw - w) // 2, (sh - h) // 2
root.geometry(f"{w}x{h}+{x}+{y}")
tk.Label(root, text=f"Экран: {sw} x {sh}").pack(pady=(100, 5))
tk.Label(root, text=f"Терезе орны: x={x}, y={y}").pack()
root.mainloop()
```

**4-тапсырма**
```python
import tkinter as tk

root = tk.Tk()
root.title("Қазақстан")
root.geometry("400x200")
root.config(bg="#00AFCA")
tk.Label(root, text="Қазақстан", bg="#00AFCA", fg="#FEC50C",
         font=("Arial", 28, "bold")).pack(expand=True)
root.mainloop()
```

**5-тапсырма**
```python
import random
import tkinter as tk


def set_bg(color):
    root.config(bg=color)
    root.title(color)


def random_bg():
    set_bg("#%06x" % random.randint(0, 0xFFFFFF))


root = tk.Tk()
root.geometry("400x200")
for name, color in [("Қызыл", "red"), ("Жасыл", "green"), ("Көк", "blue")]:
    tk.Button(root, text=name, command=lambda c=color: set_bg(c)).pack(side="left", padx=10)
tk.Button(root, text="Кездейсоқ", command=random_bg).pack(side="left", padx=10)
root.mainloop()
```

**6-тапсырма**
```python
import tkinter as tk

size = [300, 200]


def resize(delta):
    w = min(800, max(200, size[0] + delta))
    h = min(600, max(150, size[1] + delta))
    size[0], size[1] = w, h
    root.geometry(f"{w}x{h}")
    info.config(text=f"Өлшем: {w} x {h}")


root = tk.Tk()
root.title("Өлшем")
root.geometry("300x200")
info = tk.Label(root, text="Өлшем: 300 x 200")
info.pack(pady=20)
tk.Button(root, text="Үлкейту", command=lambda: resize(50)).pack()
tk.Button(root, text="Кішірейту", command=lambda: resize(-50)).pack()
root.mainloop()
```

**7-тапсырма**
```python
import tkinter as tk


def open_about():
    win = tk.Toplevel(root)
    win.title("Анықтама")
    win.geometry("300x150")
    win.resizable(False, False)
    tk.Label(win, text="Нұсқа 1.0\nАвторы: Ару С.").pack(pady=20)
    tk.Button(win, text="Жабу", command=win.destroy).pack()


def on_close():
    print("Бағдарлама аяқталды")
    root.destroy()


root = tk.Tk()
root.title("Басты терезе")
root.geometry("300x200")
tk.Button(root, text="Анықтама", command=open_about).pack(pady=70)
root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

**8-тапсырма**
```python
import tkinter as tk

THEMES = {
    "light": {"bg": "#ffffff", "fg": "#222222"},
    "dark": {"bg": "#1e1e2e", "fg": "#f5f5f5"},
}


class ThemeApp(tk.Tk):
    def __init__(self, theme="light"):
        super().__init__()
        self.theme = theme
        self.title("Тақырыптар")
        self.center(400, 250)
        self.labels = [
            tk.Label(self, text="Жарық пен қараңғы", font=("Arial", 18)),
            tk.Label(self, text="Батырманы басып көріңіз"),
        ]
        for lbl in self.labels:
            lbl.pack(pady=10)
        tk.Button(self, text="Тақырыпты ауыстыру", command=self.toggle).pack(pady=10)
        self.apply_theme()

    def center(self, w, h):
        x = (self.winfo_screenwidth() - w) // 2
        y = (self.winfo_screenheight() - h) // 2
        self.geometry(f"{w}x{h}+{x}+{y}")

    def apply_theme(self):
        colors = THEMES[self.theme]
        self.config(bg=colors["bg"])
        for lbl in self.labels:
            lbl.config(bg=colors["bg"], fg=colors["fg"])

    def toggle(self):
        self.theme = "dark" if self.theme == "light" else "light"
        self.apply_theme()


if __name__ == "__main__":
    ThemeApp("dark").mainloop()
```

</details>
