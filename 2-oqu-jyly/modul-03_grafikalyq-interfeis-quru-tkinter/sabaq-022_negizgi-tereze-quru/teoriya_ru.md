# Урок 22. Создание главного окна. Параметры и свойства окна.

## Темы

1. Главное окно — объект класса `Tk`
2. Система координат экрана
3. `geometry` — размер и положение
4. Ограничение размера
5. Цвет фона и `config`
6. `attributes` — специальные свойства
7. Значок (иконка)
8. Размещение окна по центру экрана
9. Закрытие окна
10. Дополнительное окно — `Toplevel`
11. Стиль ООП: наследование от класса `tk.Tk`
12. Сводная таблица методов окна
13. Частые ошибки
14. Основные понятия

## 1. Главное окно — объект класса `Tk`

```python
import tkinter as tk

root = tk.Tk()
root.title("Погода")           # заголовок
root.geometry("400x300")       # ширина x высота (пиксели)
root.mainloop()
```

`root` означает «корень»: все виджеты «растут» внутри этого окна.

## 2. Система координат экрана

На экране компьютера начало координат (0, 0) — **левый верхний угол**. X растёт вправо, Y — **вниз** (в отличие от математики!).

```text
(0,0) ───────────────► X
  │
  │     ┌─────────────┐
  │     │  окно       │  (X=100, Y=50) — левый верхний угол окна
  │     └─────────────┘
  ▼
  Y
```

## 3. `geometry` — размер и положение

Формат: `"ШИРИНАxВЫСОТА+X+Y"` (x — строчная латинская буква).

| Запись | Смысл |
|---|---|
| `"400x300"` | Размер 400×300, положение выбирает система |
| `"400x300+100+50"` | Размер 400×300, 100 пикселей от левого края экрана, 50 сверху |
| `"+0+0"` | Только положение: левый верхний угол |
| `"400x300-0-0"` | Минус — отсчёт от правого/нижнего края: правый нижний угол |

```python
import tkinter as tk

root = tk.Tk()
root.title("Положение")
root.geometry("300x200+100+50")
root.mainloop()
```

## 4. Ограничение размера

```python
import tkinter as tk

root = tk.Tk()
root.title("Ограничения")
root.geometry("400x300")
root.resizable(False, True)    # ширина не меняется, высота меняется
root.minsize(400, 200)         # минимальный размер
root.maxsize(400, 600)         # максимальный размер
root.mainloop()
```

| Метод | Смысл |
|---|---|
| `resizable(w, h)` | Разрешение менять ширину/высоту (`True`/`False`) |
| `minsize(w, h)` | Минимальный размер |
| `maxsize(w, h)` | Максимальный размер |

## 5. Цвет фона и `config`

Свойство любого виджета (и окна тоже) меняем методом `config()` (или `configure()`):

```python
import tkinter as tk

root = tk.Tk()
root.title("Цвета")
root.geometry("300x150")
root.config(bg="#1e90ff")      # фон — синий

tk.Label(root, text="Синее небо", bg="#1e90ff", fg="white",
         font=("Arial", 18)).pack(pady=40)
root.mainloop()
```

Способы задания цвета:
- название: `"red"`, `"white"`, `"lightgreen"`, `"gold"`, `"skyblue"`;
- HEX-код: `"#RRGGBB"` — доли красного, зелёного, синего от 00 до FF (шестнадцатеричная система счисления). Например, `"#00AFCA"` — голубой цвет флага Казахстана, `"#FEC50C"` — жёлтый.

| HEX | Цвет |
|---|---|
| `#FF0000` | красный |
| `#00FF00` | зелёный |
| `#0000FF` | синий |
| `#FFFFFF` | белый |
| `#000000` | чёрный |
| `#808080` | серый |

## 6. `attributes` — специальные свойства

```python
import tkinter as tk

root = tk.Tk()
root.title("Особое окно")
root.geometry("300x150")
root.attributes("-alpha", 0.85)     # прозрачность: 0.0 (невидимо) ... 1.0 (полностью)
root.attributes("-topmost", True)   # находится поверх всех окон
# root.attributes("-fullscreen", True)  # полный экран (продумайте выход по Esc!)
tk.Label(root, text="Я всегда сверху").pack(pady=50)
root.mainloop()
```

## 7. Значок (иконка)

```python
import tkinter as tk

root = tk.Tk()
root.title("Значок")
try:
    icon = tk.PhotoImage(file="icon.png")   # PNG или GIF
    root.iconphoto(True, icon)
except tk.TclError:
    print("icon.png не найден — остаётся значок по умолчанию")
root.mainloop()
```

В Windows для файла `.ico` используется `root.iconbitmap("icon.ico")`. `iconphoto` работает во всех системах.

## 8. Размещение окна по центру экрана

Размер экрана дают `winfo_screenwidth()` и `winfo_screenheight()`:

```python
import tkinter as tk

root = tk.Tk()
root.title("По центру")
w, h = 400, 250
screen_w = root.winfo_screenwidth()
screen_h = root.winfo_screenheight()
x = (screen_w - w) // 2
y = (screen_h - h) // 2
root.geometry(f"{w}x{h}+{x}+{y}")
tk.Label(root, text=f"Экран: {screen_w}x{screen_h}").pack(pady=100)
root.mainloop()
```

Например, если экран 1920×1080: x = (1920 − 400) // 2 = 760, y = (1080 − 250) // 2 = 415.

## 9. Закрытие окна

- `root.destroy()` — закрывает окно, и `mainloop()` завершается.
- `root.protocol("WM_DELETE_WINDOW", func)` — при нажатии пользователем кнопки «✕» вызывается `func`.

```python
import tkinter as tk


def on_close():
    print("Программа закрыта. До свидания!")
    root.destroy()


root = tk.Tk()
root.title("Закрытие")
root.geometry("250x120")
tk.Button(root, text="Выход", command=root.destroy).pack(pady=40)
root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

Внутри `on_close` можно спросить подтверждение: `if messagebox.askyesno("Выход", "Вы действительно хотите выйти?"): root.destroy()` (модуль `tkinter.messagebox`).

## 10. Дополнительное окно — `Toplevel`

В одной программе бывает только один `Tk()`. Второе окно — `Toplevel`:

```python
import tkinter as tk


def open_about():
    win = tk.Toplevel(root)
    win.title("О программе")
    win.geometry("250x100+150+150")
    tk.Label(win, text="Версия 1.0\nАвтор: кружок Python").pack(pady=10)
    tk.Button(win, text="Закрыть", command=win.destroy).pack()


root = tk.Tk()
root.title("Главное окно")
root.geometry("300x150")
tk.Button(root, text="О программе", command=open_about).pack(pady=50)
root.mainloop()
```

## 11. Стиль ООП: наследование от класса `tk.Tk`

В больших приложениях окно удобно писать как класс — здесь пригодятся наследование и `super()`:

```python
import tkinter as tk


class App(tk.Tk):
    def __init__(self):
        super().__init__()                    # конструктор Tk
        self.title("Моё приложение")
        self.geometry("350x200")
        self.config(bg="#f0f4f8")
        self.resizable(False, False)
        self.create_widgets()

    def create_widgets(self):
        self.label = tk.Label(self, text="Добро пожаловать!", bg="#f0f4f8",
                              font=("Arial", 16))
        self.label.pack(pady=30)
        tk.Button(self, text="Изменить цвет", command=self.change_color).pack()

    def change_color(self):
        self.config(bg="lightyellow")
        self.label.config(bg="lightyellow")


if __name__ == "__main__":
    app = App()
    app.mainloop()
```

Здесь `self` — само окно, поэтому `self.title(...)`, `tk.Label(self, ...)`.

## 12. Сводная таблица методов окна

| Метод | Пример | Смысл |
|---|---|---|
| `title()` | `root.title("Название")` | Заголовок |
| `geometry()` | `root.geometry("400x300+100+50")` | Размер и положение |
| `resizable()` | `root.resizable(False, False)` | Запрет изменения размера |
| `minsize()` / `maxsize()` | `root.minsize(200, 100)` | Предельные размеры |
| `config(bg=...)` | `root.config(bg="white")` | Цвет фона |
| `attributes()` | `root.attributes("-alpha", 0.9)` | Прозрачность, поверх окон, полный экран |
| `iconphoto()` | `root.iconphoto(True, img)` | Значок |
| `winfo_screenwidth()` | `root.winfo_screenwidth()` | Ширина экрана |
| `protocol()` | `root.protocol("WM_DELETE_WINDOW", f)` | Событие закрытия |
| `destroy()` | `root.destroy()` | Закрытие окна |

## Частые ошибки

**1. `*` или кириллическая «х» вместо `x` в `geometry`**

```python
import tkinter as tk
root = tk.Tk()
# root.geometry("400*300")   -> TclError: bad geometry specifier
# root.geometry("400х300")   -> кириллическая «х» — тоже ошибка!
root.geometry("400x300")      # латинская буква x
root.mainloop()
```

**2. Передача чисел в `geometry` не в виде строки** — `root.geometry(400, 300)` → `TypeError`. Правильно — f-строка: `root.geometry(f"{w}x{h}")`.

**3. Изменили фон окна, но не изменили фон надписей** — надписи выглядят серыми «заплатками». Задавайте `bg` и каждому виджету.

**4. Открытие второго окна через `tk.Tk()`** — изображения и переменные могут работать неправильно. Используйте `Toplevel`.

**5. Забыли `super().__init__()`** (в стиле ООП)

```python
import tkinter as tk

class App(tk.Tk):
    def __init__(self):
        # нет super().__init__() — вызов self.title(...) даст AttributeError
        pass
```

**6. Не оставили способа выйти из полноэкранного режима** — если включаете `-fullscreen`, не забудьте добавить кнопку закрытия или клавишу `Esc`: `root.bind("<Escape>", lambda e: root.attributes("-fullscreen", False))`.

## Основные понятия

| Понятие | Объяснение |
|---|---|
| Главное окно (root) | Объект `tk.Tk()`, единственное основное окно программы |
| Координаты экрана | Начало — левый верхний угол, Y растёт вниз |
| `geometry` | Размер и положение окна: `"ШИРИНАxВЫСОТА+X+Y"` |
| HEX-код цвета | Запись цвета в виде `#RRGGBB` |
| `Toplevel` | Дополнительное (подчинённое) окно |
| Протокол | Событие оконного менеджера (напр. `WM_DELETE_WINDOW`) |

## Примечание

- До первой отрисовки окна `winfo_width()` и `winfo_height()` возвращают `1`; чтобы получить реальные размеры, сначала вызовите `root.update_idletasks()`.
- Поддержка `attributes` зависит от ОС: `-toolwindow` есть только в Windows, `-alpha` работает не во всех оконных менеджерах Linux.
- `tk.PhotoImage` понимает PNG и GIF (PNG — начиная с Tk 8.6); для JPG и других форматов нужна библиотека Pillow (`from PIL import ImageTk`).
- Ссылку на `PhotoImage` обязательно храните в переменной или атрибуте: если объект изображения удалит сборщик мусора, значок или картинка исчезнут.
- В Windows на экранах с высоким DPI окно может выглядеть размытым; это исправляет вызов `ctypes.windll.shcore.SetProcessDpiAwareness(1)` до создания `Tk()` (только Windows).
