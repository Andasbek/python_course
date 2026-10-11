# Сабақ 100. Практикалық тапсырмалар: жобаны жетілдіру

## Базалық деңгей

### 1-тапсырма. Definition of Done бойынша өзін-өзі тексеру (міндетті, 10 мин)
Төмендегі тізімді жобаңызға толтырыңыз. «Жоқ» деген әр пункт — бүгінгі бірінші кезектегі жұмыс (жаңа мүмкіндіктерден бұрын).

| № | Критерий | Иә / Жоқ | Не істеу керек |
|---|---|---|---|
| 1 | Барлық Must-мүмкіндіктер жұмыс істейді | | |
| 2 | Сыни/маңызды қателер жоқ (Issues) | | |
| 3 | `python -m unittest discover -s tests` → OK | | |
| 4 | Қате енгізуде бағдарлама құламайды | | |
| 5 | Интернетсіз / файлсыз жағдай өңделген | | |
| 6 | Барлық мәтін қазақша, қатесіз | | |
| 7 | Батырмалар мен аралықтар біркелкі | | |
| 8 | Терезе атауы, нұсқасы, «Бағдарлама туралы» | | |
| 9 | Өлі код, `test2.py` сияқты артық файлдар жоқ | | |
| 10 | Таза ортада іске қосылады | | |

### 2-тапсырма. Мүмкіндік таңдау (міндетті)
MoSCoW тізіміңіздегі Should/Could мүмкіндіктерін теориядағы 2-бөлімнің кестесімен бағалап, ең жоғары ұпайлы **1–2 мүмкіндікті** таңдаңыз. Әрқайсысына issue ашыңыз (`feature` белгісі).

### 3-тапсырма. Мүмкіндікті тармақта жасау (міндетті)
Таңдалған мүмкіндікті жеке тармақта (`feature/...`) жасаңыз. Біріктірмес бұрын: барлық тесттер жасыл, негізгі сценарийлер өтеді, жаңа мүмкіндікке кемінде 1 тест немесе тест-кейс бар. Уақыт жетпесе — біріктірмеңіз, README-дегі «Болашақтағы жоспарлар»-ға жазыңыз.

### 4-тапсырма. Безендіру (міндетті)
Теориядағы 5-бөлімнің чек-парағы бойынша:
- түстер мен қаріптерді тұрақтыларға шығарып, `ttk.Style` (немесе Pygame-де бір `COLORS` сөздігі және бір қаріп) арқылы қолданыңыз;
- «Бағдарлама туралы» терезесін (немесе ойында «Авторлар» экранын) қосыңыз;
- терезе атауына нұсқаны жазыңыз (`v1.0`);
- барлық мәтінді дауыстап оқып, емле қателерін түзетіңіз.

## Кеңейтілген деңгей

### 5-тапсырма. Баптауларды сақтау
Пайдаланушы таңдайтын кемінде 2 параметрді (соңғы санат, терезе өлшемі, тақырып, дыбыс, қиындық деңгейі т.б.) JSON-да сақтап, келесі іске қосуда қалпына келтіріңіз. Терезе өлшемі: `root.geometry()` жабылар алдында сақталсын (`protocol("WM_DELETE_WINDOW", ...)`).

### 6-тапсырма. Есепті сурет ретінде сақтау
Диаграмманы PNG ретінде сақтайтын «Диаграмманы сақтау...» мәзір пунктін қосыңыз (`filedialog.asksaveasfilename` + `figure.savefig`). Ойын жобасы үшін — `pygame.image.save(screen, "screenshot.png")` арқылы скриншот (F12 пернесі).

### 7-тапсырма. Өлі кодты табу
`vulture` құралымен (`pip install vulture`, `vulture qalta/`) немесе қолмен жобаңыздағы қолданылмайтын функцияларды, импорттарды және айнымалыларды тауып, өшіріңіз. Әр өшіруден кейін тесттерді іске қосыңыз.

## ★ Олимпиадалық / шығармашылық тапсырма

### 8-тапсырма. Орындалатын файл және релиз
PyInstaller арқылы `.exe` (немесе macOS/Linux үшін орындалатын файл) жасап, оны Python орнатылмаған компьютерде тексеріңіз. Суреттер мен дыбыстар болса, `--add-data` параметрін қолданыңыз және кодта жолдарды `sys._MEIPASS` арқылы анықтаңыз.

### 9-тапсырма. Қолданылу статистикасы (жергілікті)
Бағдарламаға қай мүмкіндіктің қанша рет қолданылғанын жергілікті `usage.json`-ға жазатын шағын модуль қосыңыз (интернетке ештеңе жіберілмейді!). «Бағдарлама туралы» терезесінде «Сіз 37 операция қостыңыз, 5 рет экспорттадыңыз» деп көрсетіңіз. Жеке деректер этикасын (86-сабақ) ескеріп, README-де не сақталатынын ашық жазыңыз.

## Үй тапсырмасы
1. Feature freeze: бүгіннен бастап жаңа мүмкіндік қоспаймыз. Аяқталмаған тармақтарды біріктірмеңіз.
2. Definition of Done тізімінің барлық пунктін «Иә» күйіне жеткізіңіз. `push`.
3. Келесі сабаққа: бағдарламаның 3–4 скриншотын жасаңыз (басты экран, енгізу, нәтиже/диаграмма, қате хабарламасы) және `docs/screenshots/` папкасына салыңыз.

## Бағалау критерийлері
| Критерий | Үлес | Не тексеріледі |
|---|---|---|
| Жоба аяқталған күйге жетті | 40% | Definition of Done тізімінің ≥ 8 пункті орындалды, тесттер жасыл |
| Мүмкіндік таңдау мен жүзеге асырудың ұтымдылығы | 25% | таңдау негізделген, тармақта жасалды, `main` бұзылмады |
| Безендіру мен код тазалығы | 20% | біркелкі стиль, «Бағдарлама туралы», өлі код жоқ |
| Өз бетінше жұмыс, тәртіп | 15% | feature freeze сақталды, коммиттер мағыналы |

## Педагогке: шешімдер
<details><summary>Шешімдерді көрсету</summary>

**1–4-тапсырмалар** жеке тексеріледі. Ең жиі кездесетін мәселелер: (1) тесттер ескі API-ды шақырады (кодты өзгерткенде тесттер жаңартылмаған); (2) `test.py`, `copy_of_gui.py` сияқты файлдар; (3) батырма атаулары әртүрлі («OK», «Сақтау», «Дайын»). Оқушы бір күрделі мүмкіндікке кірісіп кетсе, уақыттың 50%-ы өткенде оны тоқтатып, қарапайымырақ нұсқаға ауысуды ұсыныңыз.

**5-тапсырма.**
```python
import json
import tkinter as tk
from pathlib import Path

SETTINGS_FILE = Path("settings.json")


def load_settings():
    try:
        return json.loads(SETTINGS_FILE.read_text(encoding="utf-8"))
    except (FileNotFoundError, json.JSONDecodeError):
        return {"geometry": "600x400", "last_category": "Тамақ"}


def save_settings(data):
    SETTINGS_FILE.write_text(json.dumps(data, ensure_ascii=False, indent=2),
                             encoding="utf-8")


settings = load_settings()
root = tk.Tk()
root.title("Баптаулар мысалы")
root.geometry(settings["geometry"])


def on_close():
    settings["geometry"] = root.geometry()      # мысалы: '640x420+100+50'
    save_settings(settings)
    root.destroy()


root.protocol("WM_DELETE_WINDOW", on_close)
root.mainloop()
```

**6-тапсырма.**
```python
from tkinter import filedialog, messagebox


def save_chart(figure):
    """Matplotlib суретін PNG ретінде сақтайды."""
    path = filedialog.asksaveasfilename(defaultextension=".png",
                                        filetypes=[("PNG сурет", "*.png")],
                                        initialfile="qalta_chart.png")
    if path:
        figure.savefig(path, dpi=150, bbox_inches="tight")
        messagebox.showinfo("Сақталды", f"Диаграмма сақталды:\n{path}")
```
Pygame: оқиғалар циклінде `if event.type == pygame.KEYDOWN and event.key == pygame.K_F12: pygame.image.save(screen, "screenshot.png")`.

**7-тапсырма.** Тексеру: `vulture` есебіндегі «60% confidence» нәтижелерін автоматты өшірмей, әрқайсысын тексеру керек (мысалы, Tkinter `command=` арқылы шақырылатын әдістер «қолданылмайды» деп көрінуі мүмкін).

**8-тапсырма.**
```python
import sys
from pathlib import Path


def resource_path(relative):
    """Ресурс файлының жолы: әзірлеу кезінде де, PyInstaller-ден кейін де жұмыс істейді."""
    base = Path(getattr(sys, "_MEIPASS", Path(__file__).resolve().parent))
    return base / relative
```
```bash
pyinstaller --onefile --windowed --name QaltaAqshasy --add-data "assets;assets" main.py
```
(Windows-та `;`, macOS/Linux-та `:` бөлгіші: `--add-data "assets:assets"`.)

**9-тапсырма.**
```python
import json
from pathlib import Path


class Usage:
    """Мүмкіндіктердің қолданылу санауышы (тек жергілікті файлда)."""

    def __init__(self, path="usage.json"):
        self.path = Path(path)
        try:
            self.counts = json.loads(self.path.read_text(encoding="utf-8"))
        except (FileNotFoundError, json.JSONDecodeError):
            self.counts = {}

    def hit(self, feature):
        self.counts[feature] = self.counts.get(feature, 0) + 1
        self.path.write_text(json.dumps(self.counts, ensure_ascii=False), encoding="utf-8")

    def summary(self):
        return (f"Сіз {self.counts.get('add', 0)} операция қостыңыз, "
                f"{self.counts.get('export', 0)} рет экспорттадыңыз")


u = Usage("usage_demo.json")
for _ in range(3):
    u.hit("add")
u.hit("export")
print(u.summary())
Path("usage_demo.json").unlink()
```
Нәтижесі:
```text
Сіз 3 операция қостыңыз, 1 рет экспорттадыңыз
```
</details>
