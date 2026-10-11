# Сабақ 25. Оқиғаларды өңдеу (events). Батырманы басуға функция байланыстыру.

## Тақырыптар

1. Оқиға, өңдеуші, оқиғалар циклі
2. `command` — батырманы басу
3. Аргументті функция: `lambda`
4. `bind` — кез келген оқиғаны байланыстыру
5. `event` объектісі
6. `after()` — уақыт оқиғалары (таймер)
7. ОБП стиліндегі оқиғалар
8. Жиі кездесетін қателер
9. Негізгі ұғымдар

## 1. Оқиға, өңдеуші, оқиғалар циклі

- **Оқиға (событие, event)** — бағдарламада болған әрекет: тінтуірді басу, пернені басу, курсорды жылжыту, терезе өлшемін өзгерту, таймер.
- **Оқиға өңдеуші (обработчик, event handler / callback)** — оқиға болғанда шақырылатын функция.
- **Оқиғалар циклі (event loop)** — `mainloop()`: оқиғаларды кезекпен алып, тиісті өңдеушілерді шақырады.

```text
Пайдаланушы ──► [Оқиғалар кезегі] ──► mainloop() ──► өңдеуші функция ──► интерфейс жаңарады
   басу            <Button-1>                          on_click()
```

Tkinter-де өңдеушіні байланыстырудың екі негізгі тәсілі бар:

| Тәсіл | Қайда | Функцияға не беріледі |
|---|---|---|
| `command=...` | `Button`, `Checkbutton`, `Radiobutton`, `Scale`, мәзір | Ештеңе (аргументсіз шақырылады) |
| `widget.bind("<оқиға>", func)` | Кез келген виджет | `event` объектісі |

## 2. `command` — батырманы басу

```python
import tkinter as tk


def on_click():
    label.config(text="Батырма басылды!")


root = tk.Tk()
root.title("command")
label = tk.Label(root, text="Күтіп тұрмын...", font=("Arial", 14))
label.pack(padx=20, pady=10)
tk.Button(root, text="Бас", command=on_click).pack(pady=10)
root.mainloop()
```

`command` функцияны **аргументсіз** шақырады. Бірақ көбіне функцияға мән беру керек (мысалы, қай сан басылды).

## 3. Аргументті функция: `lambda`

**`lambda`** — бір жолдық атаусыз функция:

```python
square = lambda x: x * x
print(square(5))

add = lambda a, b: a + b
print(add(2, 3))
```

Нәтижесі:
```text
25
5
```

Tkinter-де `lambda` аргументі бар функцияны «орап», аргументсіз функция жасайды:

```python
import tkinter as tk


def set_text(text, color):
    label.config(text=text, fg=color)


root = tk.Tk()
root.title("lambda")
label = tk.Label(root, text="Бағдаршам", font=("Arial", 18))
label.pack(pady=10, padx=40)
tk.Button(root, text="Қызыл", command=lambda: set_text("ТОҚТА", "red")).pack(fill="x")
tk.Button(root, text="Сары", command=lambda: set_text("ДАЙЫНДАЛ", "orange")).pack(fill="x")
tk.Button(root, text="Жасыл", command=lambda: set_text("ЖҮР", "green")).pack(fill="x")
root.mainloop()
```

`lambda: set_text("ТОҚТА", "red")` — «басқанда `set_text`-ті осы аргументтермен шақыр» дегенді білдіреді.

### Циклдегі lambda тұзағы

```python
funcs = []
for i in range(3):
    funcs.append(lambda: print("Батырма", i))

for f in funcs:
    f()
```

Нәтижесі:
```text
Батырма 2
Батырма 2
Батырма 2
```

Неге? `lambda` ішіндегі `i` шақырылған **сәттегі** мәнді алады, ал цикл біткенде `i = 2`. Шешімі — мәнді әдепкі параметр ретінде «бекіту»:

```python
funcs = []
for i in range(3):
    funcs.append(lambda n=i: print("Батырма", n))

for f in funcs:
    f()
```

Нәтижесі:
```text
Батырма 0
Батырма 1
Батырма 2
```

Tkinter-де:

```python
import tkinter as tk


def press(digit):
    display.config(text=display.cget("text") + digit)


root = tk.Tk()
root.title("Цифрлар")
display = tk.Label(root, text="", font=("Consolas", 20), width=12, anchor="e",
                   relief="sunken")
display.grid(row=0, column=0, columnspan=5, pady=5)
for i in range(10):
    tk.Button(root, text=str(i), width=3,
              command=lambda d=str(i): press(d)).grid(row=1 + i // 5, column=i % 5)
root.mainloop()
```

### Балама: `functools.partial`

```python
from functools import partial


def greet(name, greeting):
    print(f"{greeting}, {name}!")


hello_aru = partial(greet, "Ару", "Сәлем")   # аргументтері алдын ала бекітілген функция
hello_aru()
```

Нәтижесі:
```text
Сәлем, Ару!
```

Tkinter-де: `tk.Button(root, text="1", command=partial(press, "1"))`. Циклде де дұрыс жұмыс істейді.

## 4. `bind` — кез келген оқиғаны байланыстыру

```text
виджет.bind("<оқиға_тізбегі>", өңдеуші_функция)
```

Өңдеуші функция **міндетті түрде бір параметр** қабылдайды — `event` объектісі.

```python
import tkinter as tk


def on_left_click(event):
    info.config(text=f"Сол жақ батырма: x={event.x}, y={event.y}")


def on_right_click(event):
    info.config(text="Оң жақ батырма басылды")


def on_double(event):
    info.config(text="Екі рет басылды!")


root = tk.Tk()
root.title("Тінтуір оқиғалары")
area = tk.Label(root, text="Осы аймақты басыңыз", bg="#dfe6e9", width=40, height=10)
area.pack(padx=10, pady=10)
info = tk.Label(root, text="")
info.pack(pady=5)

area.bind("<Button-1>", on_left_click)
area.bind("<Button-3>", on_right_click)     # macOS-та оң батырма кейде <Button-2>
area.bind("<Double-Button-1>", on_double)
area.bind("<Enter>", lambda e: area.config(bg="#ffeaa7"))
area.bind("<Leave>", lambda e: area.config(bg="#dfe6e9"))
root.mainloop()
```

### Негізгі оқиға тізбектері

| Тізбек | Оқиға |
|---|---|
| `<Button-1>` / `<Button-2>` / `<Button-3>` | Тінтуірдің сол / орта / оң батырмасы |
| `<Double-Button-1>` | Екі рет басу |
| `<ButtonRelease-1>` | Батырманы жіберу |
| `<B1-Motion>` | Сол батырманы басып тұрып жылжыту (сүйреу) |
| `<Motion>` | Курсор жылжуы |
| `<Enter>` / `<Leave>` | Курсор виджетке кірді / шықты |
| `<MouseWheel>` | Тінтуір дөңгелегі (Windows/macOS) |
| `<Key>` / `<KeyPress>` | Кез келген перне |
| `<Return>` | Enter пернесі |
| `<Escape>`, `<space>`, `<BackSpace>`, `<Tab>` | Тиісті пернелер |
| `<Up>`, `<Down>`, `<Left>`, `<Right>` | Бағыттауыш пернелер |
| `<a>`, `<A>` | Нақты әріп пернесі |
| `<Control-s>`, `<Alt-x>`, `<Shift-Up>` | Пернелер тіркесімі |
| `<FocusIn>` / `<FocusOut>` | Виджет фокусты алды / жоғалтты |
| `<Configure>` | Виджеттің өлшемі немесе орны өзгерді |

## 5. `event` объектісі

| Атрибут | Мағынасы |
|---|---|
| `event.x`, `event.y` | Курсордың виджет ішіндегі координаттары |
| `event.x_root`, `event.y_root` | Курсордың экрандағы координаттары |
| `event.widget` | Оқиға болған виджет |
| `event.keysym` | Перненің атауы: `"Return"`, `"a"`, `"Up"`, `"Escape"` |
| `event.char` | Басылған таңба (`"a"`, `"5"`); арнайы пернелерде бос |
| `event.num` | Тінтуір батырмасының нөмірі |
| `event.width`, `event.height` | `<Configure>` кезінде жаңа өлшем |
| `event.delta` | `<MouseWheel>` кезінде дөңгелектің бағыты |

### Пернетақта оқиғалары

```python
import tkinter as tk


def on_key(event):
    info.config(text=f"keysym: {event.keysym!r}, char: {event.char!r}")


def on_escape(event):
    root.destroy()


root = tk.Tk()
root.title("Пернетақта")
root.geometry("350x120")
info = tk.Label(root, text="Кез келген пернені басыңыз (Esc — шығу)", font=("Arial", 12))
info.pack(expand=True)
root.bind("<Key>", on_key)          # root.bind — бүкіл терезеге
root.bind("<Escape>", on_escape)
root.mainloop()
```

### Enter арқылы форманы жіберу

```python
import tkinter as tk


def search(event=None):          # event=None: command үшін де, bind үшін де жарайды
    text = entry.get().strip()
    result.config(text=f"Іздеу: {text}" if text else "Бос сұраныс")


root = tk.Tk()
root.title("Іздеу")
entry = tk.Entry(root, width=30)
entry.pack(side="left", padx=5, pady=10)
entry.focus()
tk.Button(root, text="Іздеу", command=search).pack(side="left", padx=5)
result = tk.Label(root, text="")
result.pack(side="left", padx=5)
entry.bind("<Return>", search)
root.mainloop()
```

`event=None` тәсілі өте пайдалы: бір функция `command` (аргументсіз) және `bind` (`event`-пен) арқылы да шақырыла алады.

## 6. `after()` — уақыт оқиғалары (таймер)

`widget.after(мс, функция)` — функцияны көрсетілген миллисекундтан кейін **бір рет** шақырады. Функция өзін-өзі қайта жоспарласа — таймер шығады.

```python
import tkinter as tk

seconds = 10


def tick():
    global seconds
    if seconds > 0:
        label.config(text=str(seconds))
        seconds -= 1
        root.after(1000, tick)       # 1 секундтан кейін қайта шақыр
    else:
        label.config(text="Уақыт бітті!", fg="red")


root = tk.Tk()
root.title("Кері санау")
label = tk.Label(root, text="", font=("Arial", 40))
label.pack(padx=40, pady=20)
tick()
root.mainloop()
```

**Маңызды:** GUI бағдарламасында `time.sleep()` қолданбаңыз — ол `mainloop`-ты тоқтатып, терезе «қатып қалады» («Не отвечает»). Әрқашан `after()` пайдаланыңыз.

Таймерді тоқтату: `job = root.after(1000, tick)` → `root.after_cancel(job)`.

## 7. ОБП стиліндегі оқиғалар

```python
import tkinter as tk


class ClickGame(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Кликер")
        self.score = 0
        self.label = tk.Label(self, text="Ұпай: 0", font=("Arial", 18))
        self.label.pack(pady=10, padx=40)
        self.button = tk.Button(self, text="Бас!", width=10, command=self.add_point)
        self.button.pack(pady=5)
        self.bind("<space>", self.add_point)
        self.bind("<Control-r>", self.reset)

    def add_point(self, event=None):
        self.score += 1
        self.label.config(text=f"Ұпай: {self.score}")

    def reset(self, event=None):
        self.score = 0
        self.label.config(text="Ұпай: 0")


if __name__ == "__main__":
    ClickGame().mainloop()
```

Әдістер (`self.add_point`) өңдеуші ретінде тамаша жұмыс істейді — глобал айнымалылар қажет емес.

## Жиі кездесетін қателер

**1. `bind` өңдеушісінде `event` параметрін ұмыту**

```python
import tkinter as tk

def on_click():                 # ҚАТЕ: event параметрі жоқ
    print("басылды")

root = tk.Tk()
lbl = tk.Label(root, text="Бас")
lbl.pack()
lbl.bind("<Button-1>", on_click)
# Басқанда: TypeError: on_click() takes 0 positional arguments but 1 was given
root.destroy()
```

Шешімі: `def on_click(event):` немесе `def on_click(event=None):`.

**2. `command=func(arg)`** — функция бірден шақырылады. Дұрысы: `command=lambda: func(arg)`.

**3. Циклдегі `lambda` бәріне соңғы мәнді береді** — `lambda x=i: ...` немесе `partial`.

**4. Оқиға атауын қате жазу** — `"<button-1>"` (кіші әріп), `"<Enter>"`-ді Enter пернесі деп ойлау (ол курсордың кіруі!). Enter пернесі — `"<Return>"`.

**5. Перне оқиғасын фокусы жоқ виджетке байлау** — `Label.bind("<Key>", ...)` жұмыс істемейді, себебі Label фокус алмайды. `root.bind(...)` немесе `Entry.bind(...)` қолданыңыз.

**6. `time.sleep()` арқылы күту** — терезе қатып қалады. `after()` қолданыңыз.

## Негізгі ұғымдар

| Ұғым | Түсіндірмесі |
|---|---|
| Оқиға (event) | Бағдарламада болған әрекет: басу, теру, жылжыту, таймер |
| Өңдеуші (handler, callback) | Оқиғаға жауап ретінде шақырылатын функция |
| `command` | Батырма тәрізді виджеттердің негізгі әрекетін байланыстыру |
| `bind()` | Кез келген оқиғаны функциямен байланыстыру |
| Оқиға тізбегі | `"<Button-1>"`, `"<Return>"` тәрізді оқиға сипаттамасы |
| `event` объектісі | Оқиға туралы ақпарат: координат, перне, виджет |
| `lambda` | Бір жолдық атаусыз функция |
| `functools.partial` | Аргументтері алдын ала бекітілген функция |
| `after()` | Функцияны уақыт өткен соң шақыру |

## Ескерту

- `bind` өңдеушісі `"break"` жолын қайтарса, оқиғаны әрі қарай өңдеу тоқтайды (мысалы, виджеттің стандартты әрекеті — `Entry`-ге таңба енгізу болдырылмайды).
- Сол оқиғаға қайта `bind` жасау бұрынғы өңдеушіні **ауыстырады**; тағы біреуін қосу үшін `widget.bind("<Button-1>", func, add="+")` қолданыңыз.
- `bind_all` оқиғаны бүкіл қосымшаға, `bind_class` бір кластың барлық виджеттеріне байланыстырады.
- Linux-та тінтуір дөңгелегі `<MouseWheel>` емес, `<Button-4>`/`<Button-5>` болып келеді; Windows-та `event.delta` 120-ға еселік.
- Өңдеуші ішіндегі ұзақ есептеулер терезені «қатырып» тастайды: жұмысты `after()` арқылы бөліктерге бөліңіз немесе бөлек ағынға (`threading`) шығарыңыз, виджеттерді тек негізгі ағыннан жаңартыңыз.
- `after(ms, func, *args)` аргументтерді бере алады: `root.after(1000, show, "мәтін")`.
