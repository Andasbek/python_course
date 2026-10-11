# Урок 22. Практические задания: главное окно и его параметры

## Базовый уровень

### Задание 1. Окно-визитка
Создайте окно с заголовком «Визитка», размером 350×200, расположенное в 200 пикселях от левого края экрана и в 100 сверху. Фон — светло-голубой (`#e6f2ff`). Внутри должны быть написаны ваше имя, название кружка и ваш город (фон надписей такой же, как у окна).

### Задание 2. Неизменяемое окно
Окно 400×300: изменять размер нельзя совсем. Окно всегда должно быть поверх других окон и иметь непрозрачность 90%.

### Задание 3. Окно по центру
Создайте окно 500×300, которое открывается точно по центру на любом экране. Выведите в окне размер экрана и координаты окна:
```text
Экран: 1920 x 1080
Положение окна: x=710, y=390
```

### Задание 4. Цвета флага Казахстана
Фон окна `#00AFCA` (голубой), в центре — надпись «Казахстан» жёлтого цвета (`#FEC50C`), шрифт — 28, жирный (`"bold"`).

## Продвинутый уровень

### Задание 5. Переключатель цвета
В окне должны быть кнопки «Красный», «Зелёный», «Синий», «Случайный». Кнопки меняют цвет фона окна. «Случайный» — случайный HEX-цвет (`"#%06x" % random.randint(0, 0xFFFFFF)`), и его код записывается в заголовок (`title`).

### Задание 6. Изменитель размера
Кнопки «Увеличить» и «Уменьшить» должны увеличивать/уменьшать окно на 50 пикселей (минимум 200×150, максимум 800×600). Текущий размер показывается в надписи. Подсказка: храните размер в переменной и вызывайте `geometry(f"{w}x{h}")`.

### Задание 7. Окно «О программе»
В главном окне кнопка «Справка» должна открывать окно `Toplevel` (300×150, размер не меняется). В нём — версия программы, автор и кнопка «Закрыть». При нажатии «✕» главного окна в консоль выводится «Программа завершена».

## ★ Олимпиадная / творческая задача

### Задание 8. Приложение в стиле ООП
Напишите класс `ThemeApp`, наследуемый от `tk.Tk`. Конструктор принимает параметр `theme` (`"light"` или `"dark"`). В словаре хранятся цвета двух тем (`bg`, `fg`). Кнопка «Сменить тему» должна менять цвет окна и всех надписей. Окно открывается по центру экрана.

## Домашнее задание

1. Завершите задание 8 (или 5) и загрузите на GitHub под именем `gui/lesson22_window.py`.
2. Найдите HEX-коды 5 понравившихся цветов (например, на сайте colorhunt.co) и покажите их в одном окне с помощью кнопок.
3. Подумайте: как добавить в окно поле для ввода текста?

## Для педагога: решения

<details><summary>Показать решения</summary>

**Задание 1**
```python
import tkinter as tk

BG = "#e6f2ff"
root = tk.Tk()
root.title("Визитка")
root.geometry("350x200+200+100")
root.config(bg=BG)
tk.Label(root, text="Ару Серікқызы", bg=BG, font=("Arial", 18, "bold")).pack(pady=(30, 5))
tk.Label(root, text="Кружок «Python класс Программирования»", bg=BG).pack()
tk.Label(root, text="Алматы", bg=BG).pack()
root.mainloop()
```

**Задание 2**
```python
import tkinter as tk

root = tk.Tk()
root.title("Неизменяемое окно")
root.geometry("400x300")
root.resizable(False, False)
root.attributes("-topmost", True)
root.attributes("-alpha", 0.9)
root.mainloop()
```

**Задание 3**
```python
import tkinter as tk

root = tk.Tk()
root.title("По центру")
w, h = 500, 300
sw, sh = root.winfo_screenwidth(), root.winfo_screenheight()
x, y = (sw - w) // 2, (sh - h) // 2
root.geometry(f"{w}x{h}+{x}+{y}")
tk.Label(root, text=f"Экран: {sw} x {sh}").pack(pady=(100, 5))
tk.Label(root, text=f"Положение окна: x={x}, y={y}").pack()
root.mainloop()
```

**Задание 4**
```python
import tkinter as tk

root = tk.Tk()
root.title("Казахстан")
root.geometry("400x200")
root.config(bg="#00AFCA")
tk.Label(root, text="Казахстан", bg="#00AFCA", fg="#FEC50C",
         font=("Arial", 28, "bold")).pack(expand=True)
root.mainloop()
```

**Задание 5**
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
for name, color in [("Красный", "red"), ("Зелёный", "green"), ("Синий", "blue")]:
    tk.Button(root, text=name, command=lambda c=color: set_bg(c)).pack(side="left", padx=10)
tk.Button(root, text="Случайный", command=random_bg).pack(side="left", padx=10)
root.mainloop()
```

**Задание 6**
```python
import tkinter as tk

size = [300, 200]


def resize(delta):
    w = min(800, max(200, size[0] + delta))
    h = min(600, max(150, size[1] + delta))
    size[0], size[1] = w, h
    root.geometry(f"{w}x{h}")
    info.config(text=f"Размер: {w} x {h}")


root = tk.Tk()
root.title("Размер")
root.geometry("300x200")
info = tk.Label(root, text="Размер: 300 x 200")
info.pack(pady=20)
tk.Button(root, text="Увеличить", command=lambda: resize(50)).pack()
tk.Button(root, text="Уменьшить", command=lambda: resize(-50)).pack()
root.mainloop()
```

**Задание 7**
```python
import tkinter as tk


def open_about():
    win = tk.Toplevel(root)
    win.title("Справка")
    win.geometry("300x150")
    win.resizable(False, False)
    tk.Label(win, text="Версия 1.0\nАвтор: Ару С.").pack(pady=20)
    tk.Button(win, text="Закрыть", command=win.destroy).pack()


def on_close():
    print("Программа завершена")
    root.destroy()


root = tk.Tk()
root.title("Главное окно")
root.geometry("300x200")
tk.Button(root, text="Справка", command=open_about).pack(pady=70)
root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

**Задание 8**
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
        self.title("Темы")
        self.center(400, 250)
        self.labels = [
            tk.Label(self, text="Свет и тьма", font=("Arial", 18)),
            tk.Label(self, text="Попробуйте нажать кнопку"),
        ]
        for lbl in self.labels:
            lbl.pack(pady=10)
        tk.Button(self, text="Сменить тему", command=self.toggle).pack(pady=10)
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
