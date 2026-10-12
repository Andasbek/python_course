# Задачник по Python: решения

Эталонные решения задач из [zadachnik_python.md](zadachnik_python.md). Каждое решение проверено на примере из условия. Решение — один из возможных вариантов; другие правильные решения тоже засчитываются.

## 1. Ввод и вывод данных, присваивание

**Begin1.**
```python
name = input()
print(f"Привет, {name}! Добро пожаловать в кружок.")
```

**Begin2.**
```python
a = int(input())
print(4 * a)
```

**Begin3.**
```python
a = float(input())
print(a * a)
```

**Begin4.**
```python
a, b = map(int, input().split())
print(a * b)
print(2 * (a + b))
```

**Begin5.**
```python
import math

d = float(input())
print(round(math.pi * d, 2))
```

**Begin6.**
```python
a = int(input())
print(a ** 3)
print(6 * a ** 2)
```

**Begin7.**
```python
a, b, c = map(int, input().split())
print(a * b * c / 1000)
print(2 * (a * b + b * c + a * c))
```

**Begin8.**
```python
import math

r = float(input())
print(round(2 * math.pi * r, 2))
print(round(math.pi * r ** 2, 2))
```

**Begin9.**
```python
a = int(input())
b = int(input())
print((a + b) / 2)
```

**Begin10.**
```python
a = float(input())
b = float(input())
print(round((a * b) ** 0.5, 3))
```

**Begin11.**
```python
a = int(input())
b = int(input())
print(a + b)
print(a - b)
print(a * b)
print(round(a / b, 2))
```

**Begin12.**
```python
birth = int(input())
now = int(input())
print(f"В этом году тебе исполнится {now - birth} лет")
```

**Begin13.**
```python
p, k = map(int, input().split())
cost = p * k
print(cost)
print(5000 - cost)
```

**Begin14.**
```python
a = float(input())
b = float(input())
c = (a ** 2 + b ** 2) ** 0.5
print(round(c, 2))
print(round(a + b + c, 2))
```

**Begin15.**
```python
import math

r1, r2 = map(float, input().split())
s1 = math.pi * r1 ** 2
s2 = math.pi * r2 ** 2
print(f"{s1:.2f}")
print(f"{s2:.2f}")
print(f"{s1 - s2:.2f}")
```

**Begin16.**
```python
import math

length = float(input())
r = length / (2 * math.pi)
print(f"{r:.2f}")
print(f"{math.pi * r ** 2:.2f}")
```

**Begin17.**
```python
x1, x2 = map(float, input().split())
print(abs(x2 - x1))
```

**Begin18.**
```python
a, b, c = map(int, input().split())
ac = abs(c - a)
cb = abs(b - c)
print(ac, cb, ac + cb)
```

**Begin19.**
```python
import math

x1, y1, x2, y2 = map(float, input().split())
d = math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2)
print(round(d, 1))
```

**Begin20.**
```python
x1, y1, x2, y2 = map(int, input().split())
w = abs(x2 - x1)
h = abs(y2 - y1)
print(2 * (w + h), w * h)
```

**Begin21.**
```python
import math

x1, y1, x2, y2, x3, y3 = map(float, input().split())
a = math.dist((x1, y1), (x2, y2))
b = math.dist((x2, y2), (x3, y3))
c = math.dist((x3, y3), (x1, y1))
p = (a + b + c) / 2
s = math.sqrt(p * (p - a) * (p - b) * (p - c))
print(f"{a + b + c:.2f}")
print(f"{s:.2f}")
```

**Begin22.**
```python
a = int(input())
b = int(input())
a, b = b, a
print(a, b)
```

**Begin23.**
```python
a, b, c = map(int, input().split())
a, b, c = c, a, b
print(a, b, c)
```

**Begin24.**
```python
a, b, c = map(int, input().split())
a, b, c = b, c, a
print(a, b, c)
```

**Begin25.**
```python
x = float(input())
y = 2 * x ** 3 - 5 * x ** 2 + x - 4
print(y)
```

**Begin26.**
```python
a = int(input())
a2 = a * a
a4 = a2 * a2
a8 = a4 * a4
print(a2, a4, a8)
```

**Begin27.**
```python
c = float(input())
f = c * 9 / 5 + 32
print(f"{f:.1f}")
```

**Begin28.**
```python
f = float(input())
c = (f - 32) * 5 / 9
print(f"{c:.1f}")
```

**Begin29.**
```python
import math

deg = float(input())
rad = deg * math.pi / 180
print(f"{rad:.4f}")
```

**Begin30.**
```python
v = int(input())
print(f"{v * 1000 / 3600:.2f}")
```

**Begin31.**
```python
s = int(input())
k = float(input())
print(round(s / k, 2))
```

**Begin32.**
```python
s, v1, v2 = map(float, input().split())
t = s / (v1 + v2)
print(f"{t:.2f}")
print(f"{v1 * t:.2f}")
```

**Begin33.**
```python
v1, v2 = map(float, input().split())
v = 2 * v1 * v2 / (v1 + v2)
print(f"{v:.2f}")
```

**Begin34.**
```python
a, b = map(float, input().split())
print(round(-b / a, 3))
```

**Begin35.**
```python
import math

a, b, c = map(float, input().split())
d = b ** 2 - 4 * a * c
x1 = (-b - math.sqrt(d)) / (2 * a)
x2 = (-b + math.sqrt(d)) / (2 * a)
print(f"{x1:.2f} {x2:.2f}")
```

**Begin36★.**
```python
a1, b1, c1 = map(int, input().split())
a2, b2, c2 = map(int, input().split())
d = a1 * b2 - a2 * b1
x = (c1 * b2 - c2 * b1) / d
y = (a1 * c2 - a2 * c1) / d
print(f"{x:.2f} {y:.2f}")
```

**Begin37★.**
```python
name = input()
price = float(input())
qty = int(input())
print(f"{name:<12}{qty:>3} x {price:>9.2f} = {price * qty:>9.2f}")
```

**Begin38★.**
```python
s = int(input())
p = float(input())
n = int(input())
total = s * (1 + p / 100) ** n
print(f"{total:,.2f}".replace(",", " "))
print(f"{total - s:,.2f}".replace(",", " "))
```

**Begin39★.**
```python
import math

line = input().split()
n = int(line[0])
a = float(line[1])
s = n * a ** 2 / (4 * math.tan(math.pi / n))
print(f"{s:.3f}")
```

**Begin40★.**
```python
import math

R = 6371
lat1, lon1 = map(float, input().split())
lat2, lon2 = map(float, input().split())
f1 = math.radians(lat1)
f2 = math.radians(lat2)
d_f = f2 - f1
d_l = math.radians(lon2 - lon1)
h = math.sin(d_f / 2) ** 2 + math.cos(f1) * math.cos(f2) * math.sin(d_l / 2) ** 2
d = 2 * R * math.asin(math.sqrt(h))
print(round(d))
```

---

## 2. Целые числа

**Integer1.**
```python
h = int(input())
print(f"{h // 100} м {h % 100} см")
```

**Integer2.**
```python
money = int(input())
print(f"{money // 100} тг {money % 100} тиын")
```

**Integer3.**
```python
n = int(input())
m, s = divmod(n, 60)
print(f"{m} мин {s} с")
```

**Integer4.**
```python
a, b = map(int, input().split())
print(a // b, a % b)
```

**Integer5.**
```python
n, k = map(int, input().split())
print((n + k - 1) // k)
```

**Integer6.**
```python
n = int(input())
print(n // 10, n % 10)
```

**Integer7.**
```python
n = int(input())
a = n // 10
b = n % 10
print(a + b, a * b)
```

**Integer8.**
```python
n = int(input())
print(n % 10 * 10 + n // 10)
```

**Integer9.**
```python
room = int(input())
print(room // 100)
```

**Integer10.**
```python
n = int(input())
print(n % 10, n // 10 % 10)
```

**Integer11.**
```python
n = int(input())
a = n // 100
b = n // 10 % 10
c = n % 10
print(a + b + c, a * b * c)
```

**Integer12.**
```python
n = int(input())
a = n // 100
b = n // 10 % 10
c = n % 10
print(c * 100 + b * 10 + a)
```

**Integer13.**
```python
n = int(input())
print(n % 100 * 10 + n // 100)
```

**Integer14.**
```python
n = int(input())
a = n // 100
b = n // 10 % 10
c = n % 10
print(a * 100 + c * 10 + b)
```

**Integer15.**
```python
n = int(input())
a = n // 100
b = n // 10 % 10
c = n % 10
print(b * 100 + a * 10 + c)
```

**Integer16.**
```python
n = int(input())
print(n // 100 % 10)
```

**Integer17.**
```python
n = int(input())
left = n // 1000
right = n % 1000
s1 = left // 100 + left // 10 % 10 + left % 10
s2 = right // 100 + right // 10 % 10 + right % 10
print(s1, s2)
```

**Integer18.**
```python
n = int(input())
print(n // 3600)
```

**Integer19.**
```python
n = int(input())
h = n // 3600
m = n % 3600 // 60
s = n % 60
print(f"{h:02}:{m:02}:{s:02}")
```

**Integer20.**
```python
n = int(input())
print((3600 - n % 3600) % 3600)
```

**Integer21.**
```python
k = int(input())
start = 8 * 60 + 30 + (k - 1) * 55
end = start + 45
print(f"{start // 60}:{start % 60:02}-{end // 60}:{end % 60:02}")
```

**Integer22.**
```python
k = int(input())
print((k - 1) % 7 + 1)
```

**Integer23.**
```python
d, k = map(int, input().split())
print((d - 1 + k - 1) % 7 + 1)
```

**Integer24.**
```python
k = int(input())
print((k - 1) // 7 + 1)
```

**Integer25.**
```python
h, m, t = map(int, input().split())
total = (h * 60 + m + t) % (24 * 60)
print(f"{total // 60:02}:{total % 60:02}")
```

**Integer26.**
```python
a, b, c = map(int, input().split())
print((a // c) * (b // c))
```

**Integer27.**
```python
y = int(input())
print((y - 1) // 100 + 1)
```

**Integer28★.**
```python
e, p, n = map(int, input().split())
k = n - 1
entrance = k // (e * p) + 1
floor = k % (e * p) // p + 1
print(entrance, floor)
```

**Integer29★.**
```python
n = int(input())
b3 = n // 1000
b2 = n // 100 % 10
b1 = n // 10 % 10
b0 = n % 10
print(b3 * 8 + b2 * 4 + b1 * 2 + b0)
```

**Integer30★.**
```python
a, b, k = map(int, input().split())
print(b // k - (a - 1) // k)
```

---

## 3. Логические выражения

**Boolean1.**
```python
t = int(input())
print(t > 0)
```

**Boolean2.**
```python
n = int(input())
print(n % 2 != 0)
```

**Boolean3.**
```python
s = int(input())
print(s % 5 == 0)
```

**Boolean4.**
```python
score = int(input())
print(score >= 50)
```

**Boolean5.**
```python
a, b = map(int, input().split())
print(a > 0 and b > 0)
```

**Boolean6.**
```python
a, b = map(int, input().split())
print(a % 2 == 0 or b % 2 == 0)
```

**Boolean7.**
```python
a, b = map(int, input().split())
print(a % 2 != b % 2)
```

**Boolean8.**
```python
n = int(input())
print(10 <= n <= 99)
```

**Boolean9.**
```python
a, b, c = map(int, input().split())
print(a < b < c)
```

**Boolean10.**
```python
a, b, c = map(int, input().split())
print(a < b < c or c < b < a)
```

**Boolean11.**
```python
a, b, c = map(int, input().split())
print(a != b and b != c and a != c)
```

**Boolean12.**
```python
n = int(input())
print(n % 2 == 0 and not (n % 4 == 0))
```

**Boolean13.**
```python
a, b, c = map(int, input().split())
print((a > 0) + (b > 0) + (c > 0) == 2)
```

**Boolean14.**
```python
n = int(input())
print(not (n % 7 == 0))
```

**Boolean15.**
```python
n = int(input())
print((n // 10 + n % 10) % 2 == 0)
```

**Boolean16.**
```python
n = int(input())
print(n // 10 > n % 10)
```

**Boolean17.**
```python
n = int(input())
a = n // 100
b = n // 10 % 10
c = n % 10
print(a != b and b != c and a != c)
```

**Boolean18.**
```python
n = int(input())
print(n // 100 == n % 10)
```

**Boolean19.**
```python
n = int(input())
a = n // 100
b = n // 10 % 10
c = n % 10
print(a < b < c)
```

**Boolean20.**
```python
n = int(input())
left = n // 1000
right = n % 1000
s1 = left // 100 + left // 10 % 10 + left % 10
s2 = right // 100 + right // 10 % 10 + right % 10
print(s1 == s2)
```

**Boolean21.**
```python
y = int(input())
print(y % 4 == 0 and y % 100 != 0 or y % 400 == 0)
```

**Boolean22.**
```python
a, b, c = map(int, input().split())
print(a < b + c and b < a + c and c < a + b)
```

**Boolean23.**
```python
a, b, c = map(int, input().split())
print(a * a + b * b == c * c or a * a + c * c == b * b or b * b + c * c == a * a)
```

**Boolean24.**
```python
a, b, c = map(int, input().split())
print(b * b - 4 * a * c >= 0)
```

**Boolean25.**
```python
x, y = map(int, input().split())
print(x < 0 and y > 0)
```

**Boolean26.**
```python
x, y = map(int, input().split())
print(x * y > 0)
```

**Boolean27.**
```python
x, y, r = map(float, input().split())
print(x * x + y * y <= r * r)
```

**Boolean28★.**
```python
x1, y1, x2, y2 = map(int, input().split())
print((x1 + y1) % 2 == (x2 + y2) % 2)
```

**Boolean29★.**
```python
x1, y1, x2, y2 = map(int, input().split())
print(x1 == x2 or y1 == y2 or abs(x1 - x2) == abs(y1 - y2))
```

**Boolean30★.**
```python
x1, y1, x2, y2 = map(int, input().split())
print(abs(x1 - x2) * abs(y1 - y2) == 2)
```

---

## 4. Условный оператор

**If1.**
```python
n = int(input())
if n % 2 == 0:
    n = n // 2
else:
    n = 3 * n + 1
print(n)
```

**If2.**
```python
age = int(input())
if age < 7:
    print("Бесплатно")
else:
    print("1500 тенге")
```

**If3.**
```python
a, b = map(int, input().split())
if a > b:
    print(a)
else:
    print(b)
```

**If4.**
```python
total = int(input())
if total >= 10000:
    pay = total * 0.9
else:
    pay = total
print(f"{pay:.2f}")
```

**If5.**
```python
n = int(input())
if n > 0:
    print("положительное")
elif n < 0:
    print("отрицательное")
else:
    print("ноль")
```

**If6.**
```python
t1, t2, t3 = map(int, input().split())
plus = 0
minus = 0
if t1 > 0:
    plus += 1
elif t1 < 0:
    minus += 1
if t2 > 0:
    plus += 1
elif t2 < 0:
    minus += 1
if t3 > 0:
    plus += 1
elif t3 < 0:
    minus += 1
print(plus, minus)
```

**If7.**
```python
a, b = map(float, input().split())
if a > b:
    a, b = b, a
print(a, b)
```

**If8.**
```python
a, b, c = map(int, input().split())
low = a
high = a
if b < low:
    low = b
if c < low:
    low = c
if b > high:
    high = b
if c > high:
    high = c
print(low, high)
```

**If9.**
```python
a, b, c = map(int, input().split())
if a <= b and a <= c:
    print(b + c)
elif b <= a and b <= c:
    print(a + c)
else:
    print(a + b)
```

**If10.**
```python
a, b, c = map(int, input().split())
if b < a < c or c < a < b:
    print(a)
elif a < b < c or c < b < a:
    print(b)
else:
    print(c)
```

**If11.**
```python
year = int(input())
if year % 4 == 0 and year % 100 != 0 or year % 400 == 0:
    print(366)
else:
    print(365)
```

**If12.**
```python
score = int(input())
if score >= 85:
    print(5)
elif score >= 65:
    print(4)
elif score >= 50:
    print(3)
else:
    print(2)
```

**If13.**
```python
a, b, c = map(int, input().split())
d = b * b - 4 * a * c
if d > 0:
    print(2)
elif d == 0:
    print(1)
else:
    print(0)
```

**If14.**
```python
x, y = map(int, input().split())
if x == 0 and y == 0:
    print("начало координат")
elif y == 0:
    print("ось X")
elif x == 0:
    print("ось Y")
elif x > 0:
    print(1 if y > 0 else 4)
else:
    print(2 if y > 0 else 3)
```

**If15.**
```python
a, b, c = map(int, input().split())
db = abs(b - a)
dc = abs(c - a)
if db < dc:
    print("B", db)
elif dc < db:
    print("C", dc)
else:
    print("одинаково", db)
```

**If16.**
```python
x = int(input())
if x < 0:
    f = -x
elif x < 3:
    f = x * x
else:
    f = 9
print(f)
```

**If17.**
```python
n = int(input())
print("чётное" if n % 2 == 0 else "нечётное")
```

**If18.**
```python
a, b = map(int, input().split())
diff = a - b if a > b else b - a
print(diff)
```

**If19.**
```python
a, b, c = map(int, input().split())
if a >= b + c or b >= a + c or c >= a + b:
    print("не существует")
elif a == b == c:
    print("равносторонний")
elif a == b or b == c or a == c:
    print("равнобедренный")
else:
    print("разносторонний")
```

**If20.**
```python
a, b, c = map(int, input().split())
if a > b:
    a, b = b, a
if b > c:
    b, c = c, b
if a > b:
    a, b = b, a
if c * c == a * a + b * b:
    print("прямоугольный")
elif c * c < a * a + b * b:
    print("остроугольный")
else:
    print("тупоугольный")
```

**If21.**
```python
a, b, c = map(int, input().split())
if a == b:
    print(3)
elif a == c:
    print(2)
else:
    print(1)
```

**If22.**
```python
a, b, c, d = map(int, input().split())
if a == b:
    if c == a:
        print(4)
    else:
        print(3)
elif a == c:
    print(2)
else:
    print(1)
```

**If23.**
```python
x1, y1, x2, y2, x3, y3 = map(int, input().split())
if x1 == x2:
    x4 = x3
elif x1 == x3:
    x4 = x2
else:
    x4 = x1
if y1 == y2:
    y4 = y3
elif y1 == y3:
    y4 = y2
else:
    y4 = y1
print(x4, y4)
```

**If24.**
```python
h = int(input())
if 5 <= h <= 11:
    print("Доброе утро")
elif 12 <= h <= 17:
    print("Добрый день")
elif 18 <= h <= 22:
    print("Добрый вечер")
else:
    print("Доброй ночи")
```

**If25.**
```python
km, hour = map(int, input().split())
if km <= 3:
    price = 600
else:
    price = 600 + (km - 3) * 150
if hour >= 23 or hour < 6:
    price = price * 1.2
print(round(price))
```

**If26.**
```python
n = int(input())
if n == 0:
    print("ноль")
else:
    sign = "положительное" if n > 0 else "отрицательное"
    m = abs(n)
    if m < 10:
        size = "однозначное"
    elif m < 100:
        size = "двузначное"
    else:
        size = "трёхзначное"
    print(sign, size, "число")
```

**If27★.**
```python
day, month = map(int, input().split())
if month == 2:
    days = 28
elif month in (4, 6, 9, 11):
    days = 30
elif 1 <= month <= 12:
    days = 31
else:
    days = 0
print("да" if 1 <= day <= days else "нет")
```

**If28★.**
```python
day, month, year = map(int, input().split())
leap = year % 4 == 0 and year % 100 != 0 or year % 400 == 0
if month == 2:
    days = 29 if leap else 28
elif month in (4, 6, 9, 11):
    days = 30
else:
    days = 31
day += 1
if day > days:
    day = 1
    month += 1
    if month > 12:
        month = 1
        year += 1
print(day, month, year)
```

**If29★.**
```python
a, b, c, d = map(int, input().split())
left = a if a > c else c
right = b if b < d else d
if left > right:
    print("пусто")
elif left == right:
    print(left)
else:
    print(left, right)
```

**If30★.**
```python
x1, y1, x2, y2 = map(int, input().split())
dx = abs(x1 - x2)
dy = abs(y1 - y2)
rook = dx == 0 or dy == 0
bishop = dx == dy
knight = dx == 1 and dy == 2 or dx == 2 and dy == 1
if rook:
    print("ладья")
if bishop:
    print("слон")
if rook or bishop:
    print("ферзь")
if knight:
    print("конь")
if not (rook or bishop or knight):
    print("никто")
```

---

## 5. Оператор выбора match/case

**Match1.**
```python
day = int(input())
match day:
    case 1:
        print("понедельник")
    case 2:
        print("вторник")
    case 3:
        print("среда")
    case 4:
        print("четверг")
    case 5:
        print("пятница")
    case 6:
        print("суббота")
    case 7:
        print("воскресенье")
    case _:
        print("ошибка")
```

**Match2.**
```python
mark = int(input())
match mark:
    case 5:
        print("отлично")
    case 4:
        print("хорошо")
    case 3:
        print("удовлетворительно")
    case 2:
        print("неудовлетворительно")
    case _:
        print("нет такой оценки")
```

**Match3.**
```python
month = int(input())
match month:
    case 12 | 1 | 2:
        print("зима")
    case 3 | 4 | 5:
        print("весна")
    case 6 | 7 | 8:
        print("лето")
    case 9 | 10 | 11:
        print("осень")
    case _:
        print("ошибка")
```

**Match4.**
```python
month = int(input())
match month:
    case 2:
        print(28)
    case 4 | 6 | 9 | 11:
        print(30)
    case _:
        print(31)
```

**Match5.**
```python
a, op, b = input().split()
a = int(a)
b = int(b)
match op:
    case "+":
        print(a + b)
    case "-":
        print(a - b)
    case "*":
        print(a * b)
    case "/" if b == 0:
        print("деление на ноль")
    case "/":
        print(a / b)
    case _:
        print("неизвестная операция")
```

**Match6.**
```python
unit, length = input().split()
length = float(length)
match int(unit):
    case 1:
        k = 0.001
    case 2:
        k = 0.01
    case 3:
        k = 0.1
    case 4:
        k = 1
    case 5:
        k = 1000
print(round(length * k, 3))
```

**Match7.**
```python
direction, command = input().split()
match command:
    case "1":
        match direction:
            case "С":
                direction = "З"
            case "З":
                direction = "Ю"
            case "Ю":
                direction = "В"
            case "В":
                direction = "С"
    case "-1":
        match direction:
            case "С":
                direction = "В"
            case "В":
                direction = "Ю"
            case "Ю":
                direction = "З"
            case "З":
                direction = "С"
print(direction)
```

**Match8.**
```python
n = int(input())
match n:
    case 0:
        print("ноль")
    case n if n < 0:
        print("отрицательное")
    case n if n <= 10 and n % 2 == 0:
        print("маленькое чётное")
    case n if n > 100:
        print("большое")
    case _:
        print("обычное")
```

**Match9.**
```python
months = {
    1: "қаңтар", 2: "ақпан", 3: "наурыз", 4: "сәуір",
    5: "мамыр", 6: "маусым", 7: "шілде", 8: "тамыз",
    9: "қыркүйек", 10: "қазан", 11: "қараша", 12: "желтоқсан",
}
month = int(input())
print(months.get(month, "жоқ"))
```

**Match10.**
```python
first, second = input().split()
match (first, second):
    case (a, b) if a == b:
        print("ничья")
    case ("камень", "ножницы") | ("ножницы", "бумага") | ("бумага", "камень"):
        print("первый")
    case _:
        print("второй")
```

**Match11.**
```python
x, y = map(int, input().split())
match (x, y):
    case (0, 0):
        print("начало координат")
    case (_, 0):
        print("на оси X")
    case (0, _):
        print("на оси Y")
    case (a, b) if a == b:
        print("на биссектрисе")
    case (a, b):
        print(f"точка ({a}, {b})")
```

**Match12.**
```python
command = input()
match command.split():
    case ["go", steps]:
        print(f"еду {steps} м")
    case ["turn", "left"]:
        print("поворот налево")
    case ["turn", "right"]:
        print("поворот направо")
    case ["stop"]:
        print("стоп")
    case _:
        print("не понимаю")
```

**Match13.**
```python
year = int(input())
match (year - 4) % 12:
    case 0:
        print("Тышқан")
    case 1:
        print("Сиыр")
    case 2:
        print("Барыс")
    case 3:
        print("Қоян")
    case 4:
        print("Ұлу")
    case 5:
        print("Жылан")
    case 6:
        print("Жылқы")
    case 7:
        print("Қой")
    case 8:
        print("Мешін")
    case 9:
        print("Тауық")
    case 10:
        print("Ит")
    case 11:
        print("Доңыз")
```

**Match14★.**
```python
age = int(input())
tens = age // 10
ones = age % 10
match tens:
    case 2:
        words = "двадцать"
    case 3:
        words = "тридцать"
    case 4:
        words = "сорок"
    case 5:
        words = "пятьдесят"
    case 6:
        words = "шестьдесят"
match ones:
    case 0:
        print(words, "лет")
    case 1:
        print(words, "один год")
    case 2:
        print(words, "два года")
    case 3:
        print(words, "три года")
    case 4:
        print(words, "четыре года")
    case 5:
        print(words, "пять лет")
    case 6:
        print(words, "шесть лет")
    case 7:
        print(words, "семь лет")
    case 8:
        print(words, "восемь лет")
    case 9:
        print(words, "девять лет")
```

**Match15★.**
```python
n = int(input())
hundreds = {
    1: "сто", 2: "двести", 3: "триста", 4: "четыреста", 5: "пятьсот",
    6: "шестьсот", 7: "семьсот", 8: "восемьсот", 9: "девятьсот",
}
tens = {
    2: "двадцать", 3: "тридцать", 4: "сорок", 5: "пятьдесят",
    6: "шестьдесят", 7: "семьдесят", 8: "восемьдесят", 9: "девяносто",
}
teens = {
    10: "десять", 11: "одиннадцать", 12: "двенадцать", 13: "тринадцать",
    14: "четырнадцать", 15: "пятнадцать", 16: "шестнадцать",
    17: "семнадцать", 18: "восемнадцать", 19: "девятнадцать",
}
ones = {
    1: "один", 2: "два", 3: "три", 4: "четыре", 5: "пять",
    6: "шесть", 7: "семь", 8: "восемь", 9: "девять",
}
words = hundreds[n // 100]
rest = n % 100
match rest:
    case 0:
        pass
    case r if 10 <= r <= 19:
        words += " " + teens[r]
    case r if r < 10:
        words += " " + ones[r]
    case r if r % 10 == 0:
        words += " " + tens[r // 10]
    case r:
        words += " " + tens[r // 10] + " " + ones[r % 10]
print(words)
```

---

## 6. Цикл for и range

**For1.**
```python
a, b = map(int, input().split())
for i in range(a, b + 1):
    print(i, end=" ")
print()
```

**For2.**
```python
n = int(input())
for i in range(n, 0, -1):
    print(i)
print("Пуск!")
```

**For3.**
```python
price = int(input())
for kg in range(1, 11):
    print(f"{kg} кг — {kg * price} тенге")
```

**For4.**
```python
n = int(input())
for i in range(2, n + 1, 2):
    print(i, end=" ")
print()
```

**For5.**
```python
a, b = map(int, input().split())
total = 0
for i in range(a, b + 1):
    total += i
print(total)
```

**For6.**
```python
n = int(input())
total = 0
for i in range(1, n + 1):
    total += i * i
print(total)
```

**For7.**
```python
n = int(input())
fact = 1
for i in range(2, n + 1):
    fact *= i
print(fact)
```

**For8.**
```python
a, n = input().split()
a = float(a)
n = int(n)
result = 1.0
for _ in range(n):
    result *= a
print(result)
```

**For9.**
```python
n = int(input())
for i in range(1, 11):
    print(f"{n} x {i} = {n * i}")
```

**For10.**
```python
n = int(input())
p = 1
for _ in range(n):
    print(p, end=" ")
    p *= 2
print()
```

**For11.**
```python
n = int(input())
total = 0.0
for i in range(1, n + 1):
    total += 1 / i
print(f"{total:.4f}")
```

**For12.**
```python
n = int(input())
for d in range(1, n + 1):
    if n % d == 0:
        print(d, end=" ")
print()
```

**For13.**
```python
n = int(input())
count = 0
for d in range(1, n + 1):
    if n % d == 0:
        count += 1
print("простое" if count == 2 else "составное")
```

**For14.**
```python
n = int(input())
a, b = 1, 1
for _ in range(n):
    print(a, end=" ")
    a, b = b, a + b
print()
```

**For15.**
```python
s, p, n = input().split()
s = float(s)
p = float(p)
for year in range(1, int(n) + 1):
    s = s * (1 + p / 100)
    print(f"год {year}: {s:.2f}")
```

**For16.**
```python
n = int(input())
square = 0
for k in range(1, n + 1):
    square += 2 * k - 1
    print(square, end=" ")
print()
```

**For17.**
```python
n = int(input())
sign = 1
total = 0.0
for i in range(1, n + 1):
    total += sign / i
    sign = -sign
print(f"{total:.4f}")
```

**For18.**
```python
n = int(input())
fact = 1
total = 0
for i in range(1, n + 1):
    fact *= i
    total += fact
print(total)
```

**For19.**
```python
n = int(input())
result = 1
for i in range(n, 0, -2):
    result *= i
print(result)
```

**For20.**
```python
x, n = input().split()
x = float(x)
term = 1.0
total = 1.0
for k in range(1, int(n) + 1):
    term *= x / k
    total += term
print(f"{total:.6f}")
```

**For21.**
```python
w, h = map(int, input().split())
for _ in range(h):
    print("*" * w)
```

**For22.**
```python
n = int(input())
for i in range(1, n + 1):
    print("*" * i)
```

**For23.**
```python
n = int(input())
for i in range(n, 0, -1):
    print(" " * (n - i) + "#" * i)
```

**For24.**
```python
n = int(input())
for i in range(1, n + 1):
    print(" " * (n - i) + "*" * (2 * i - 1))
```

**For25.**
```python
w, h = map(int, input().split())
for row in range(h):
    if row == 0 or row == h - 1:
        print("*" * w)
    else:
        print("*" + " " * (w - 2) + "*")
```

**For26.**
```python
n = int(input())
for i in range(1, n + 1):
    for j in range(1, i + 1):
        print(j, end=" ")
    print()
```

**For27.**
```python
n = int(input())
for i in range(1, n + 1):
    for j in range(1, n + 1):
        print(f"{i * j:4}", end="")
    print()
```

**For28.**
```python
n = int(input())
for i in range(n):
    for j in range(n):
        print("#" if (i + j) % 2 == 0 else ".", end="")
    print()
```

**For29.**
```python
n, k = map(int, input().split())
total = 0
for i in range(1, n + 1):
    p = 1
    for _ in range(k):
        p *= i
    total += p
print(total)
```

**For30.**
```python
s = int(input())
for a in range(1, 10):
    for b in range(10):
        for c in range(10):
            if a + b + c == s:
                print(100 * a + 10 * b + c, end=" ")
print()
```

**For31.**
```python
s = int(input())
count = 0
for c50 in range(s // 50 + 1):
    for c20 in range(s // 20 + 1):
        rest = s - 50 * c50 - 20 * c20
        if rest >= 0:
            count += 1
print(count)
```

**For32.**
```python
n = int(input())
for a in range(1, n + 1):
    for b in range(a + 1, n + 1):
        for c in range(b + 1, n + 1):
            if a * a + b * b == c * c:
                print(a, b, c)
```

**For33.**
```python
n = int(input())
for k in range(2, n + 1):
    total = 0
    for d in range(1, k // 2 + 1):
        if k % d == 0:
            total += d
    if total == k:
        print(k, end=" ")
print()
```

**For34.**
```python
n = int(input())
for k in range(2, n + 1):
    count = 0
    for d in range(2, k):
        if k % d == 0:
            count += 1
    if count == 0:
        print(k, end=" ")
print()
```

**For35.**
```python
s = int(input())
found = False
for x in range(1, 100):
    for y in range(1, 100 - x):
        z = 100 - x - y
        if 50 * x + 10 * y + 5 * z == s:
            print(x, y, z)
            found = True
if not found:
    print("нет")
```

**For36.**
```python
n = int(input())
for i in range(1, n + 1):
    print(" " * (n - i) + "*" * (2 * i - 1))
for i in range(n - 1, 0, -1):
    print(" " * (n - i) + "*" * (2 * i - 1))
```

**For37★.**
```python
n = int(input())
for i in range(1, n + 1):
    print(" " * (n - i), end="")
    for j in range(1, i + 1):
        print(j, end="")
    for j in range(i - 1, 0, -1):
        print(j, end="")
    print()
```

**For38★.**
```python
days, first = map(int, input().split())
print(" Пн Вт Ср Чт Пт Сб Вс")
print("   " * (first - 1), end="")
for day in range(1, days + 1):
    print(f"{day:3}", end="")
    if (day + first - 1) % 7 == 0:
        print()
if (days + first - 1) % 7 != 0:
    print()
```

**For39★.**
```python
n = int(input())
for i in range(n):
    c = 1
    for k in range(i + 1):
        print(c, end=" ")
        c = c * (i - k) // (k + 1)
    print()
```

**For40★.**
```python
a, b = map(int, input().split())
best = a
best_count = 0
for k in range(a, b + 1):
    count = 0
    for d in range(1, k + 1):
        if k % d == 0:
            count += 1
    if count > best_count:
        best = k
        best_count = count
print(best, best_count)
```

---

## 7. Цикл while

**While1.**
```python
a, b = map(int, input().split())
count = 0
while a >= b:
    a -= b
    count += 1
print(count)
```

**While2.**
```python
a, b = map(int, input().split())
while a >= b:
    a -= b
print(a)
```

**While3.**
```python
n = int(input())
k = 0
p = 1
while p <= n:
    p *= 2
    k += 1
print(k, p)
```

**While4.**
```python
n = int(input())
while n % 3 == 0:
    n //= 3
print(n == 1)
```

**While5.**
```python
n = int(input())
count = 0
while n > 0:
    n //= 10
    count += 1
print(count)
```

**While6.**
```python
n = int(input())
total = 0
product = 1
while n > 0:
    digit = n % 10
    total += digit
    product *= digit
    n //= 10
print(total, product)
```

**While7.**
```python
n = int(input())
rev = 0
while n > 0:
    rev = rev * 10 + n % 10
    n //= 10
print(rev)
```

**While8.**
```python
n = int(input())
found = False
while n > 0:
    if n % 10 == 7:
        found = True
        break
    n //= 10
print("да" if found else "нет")
```

**While9.**
```python
a, b = map(int, input().split())
x, y = a, b
while y != 0:
    x, y = y, x % y
print(x, a * b // x)
```

**While10.**
```python
n = int(input())
a, b = 1, 1
while a <= n:
    a, b = b, a + b
print(a)
```

**While11.**
```python
n = int(input())
k = 0
total = 0
while total + (k + 1) <= n:
    k += 1
    total += k
print(k, total)
```

**While12.**
```python
p = float(input())
day = 1
run = 10.0
total = 10.0
while total <= 200:
    day += 1
    run *= 1 + p / 100
    total += run
print(day, f"{total:.1f}")
```

**While13.**
```python
count = 0
total = 0
mark = int(input())
while mark != 0:
    count += 1
    total += mark
    mark = int(input())
print(count, f"{total / count:.2f}")
```

**While14.**
```python
total = 0
while True:
    x = int(input())
    if x == 0:
        break
    if x < 0:
        continue
    total += x
print(total)
```

**While15.**
```python
attempts = 0
while attempts < 3:
    pin = input()
    attempts += 1
    if pin == "1234":
        print("Доступ разрешён")
        break
else:
    print("Карта заблокирована")
```

**While16.**
```python
n = int(input())
if n == 0:
    print(0)
else:
    bits = ""
    while n > 0:
        bits = str(n % 2) + bits
        n //= 2
    print(bits)
```

**While17.**
```python
n = int(input())
d = 2
while n % d != 0:
    d += 1
print(d)
```

**While18.**
```python
n = int(input())
d = 2
while d * d <= n:
    if n % d == 0:
        print("составное")
        break
    d += 1
else:
    print("простое")
```

**While19.**
```python
n = int(input())
d = 2
while n > 1:
    if n % d == 0:
        print(d, end=" ")
        n //= d
    else:
        d += 1
print()
```

**While20.**
```python
n = int(input())
m = n
rev = 0
while m > 0:
    rev = rev * 10 + m % 10
    m //= 10
print("да" if rev == n else "нет")
```

**While21.**
```python
n = int(input())
while n >= 10:
    total = 0
    while n > 0:
        total += n % 10
        n //= 10
    n = total
print(n)
```

**While22.**
```python
n = int(input())
steps = 0
peak = n
while n != 1:
    if n % 2 == 0:
        n //= 2
    else:
        n = 3 * n + 1
    if n > peak:
        peak = n
    steps += 1
print(steps, peak)
```

**While23.**
```python
h, a, b = map(int, input().split())
height = 0
day = 0
while True:
    day += 1
    height += a
    if height >= h:
        break
    height -= b
print(day)
```

**While24.**
```python
n = int(input())
high = 0
low = 9
while n > 0:
    digit = n % 10
    if digit > high:
        high = digit
    if digit < low:
        low = digit
    n //= 10
print(high, low)
```

**While25.**
```python
a, eps = map(float, input().split())
x = a
steps = 0
while True:
    new_x = (x + a / x) / 2
    steps += 1
    if abs(new_x - x) <= eps:
        break
    x = new_x
print(f"{new_x:.6f}", steps)
```

**While26.**
```python
eps = float(input())
term = 1.0
total = 0.0
count = 0
k = 0
while term >= eps:
    total += term
    count += 1
    k += 1
    term /= k
print(f"{total:.8f}", count)
```

**While27.**
```python
price, s, d = map(int, input().split())
months = 0
saved = 0
while saved < price:
    months += 1
    saved += s
    s += d
print(months, saved)
```

**While28★.**
```python
n = int(input())
a, b = 1, 1
index = 1
while a < n:
    a, b = b, a + b
    index += 1
print(index if a == n else "нет")
```

**While29★.**
```python
eps = float(input())
left = 0.0
right = 2.0
steps = 0
while right - left > eps:
    mid = (left + right) / 2
    if (left ** 3 + left - 3) * (mid ** 3 + mid - 3) <= 0:
        right = mid
    else:
        left = mid
    steps += 1
print(f"{(left + right) / 2:.6f}", steps)
```

**While30★.**
```python
n = int(input())
p = 2
while True:
    q = n - p
    d = 2
    while d * d <= p:
        if p % d == 0:
            break
        d += 1
    else:
        d = 2
        while d * d <= q:
            if q % d == 0:
                break
            d += 1
        else:
            print(p, q)
            break
    p += 1
```

---

## 8. Последовательности чисел

**Series1.**
```python
total = 0
for _ in range(7):
    total += float(input())
print(f"{total:.2f}")
```

**Series2.**
```python
n = int(input())
total = 0
for _ in range(n):
    total += int(input())
print(f"{total / n:.2f}")
```

**Series3.**
```python
n = int(input())
total = 0
product = 1
for _ in range(n):
    x = int(input())
    total += x
    product *= x
print(total, product)
```

**Series4.**
```python
n = int(input())
total = 0
for _ in range(n):
    price = round(float(input()))
    print(price, end=" ")
    total += price
print()
print(total)
```

**Series5.**
```python
n = int(input())
count = 0
for _ in range(n):
    x = int(input())
    if x % 2 == 0:
        print(x, end=" ")
        count += 1
print()
print(count)
```

**Series6.**
```python
n = int(input())
count = 0
for i in range(n):
    t = int(input())
    if t < 0:
        print(i, end=" ")
        count += 1
print()
print(count)
```

**Series7.**
```python
n = int(input())
has_positive = False
for _ in range(n):
    if int(input()) > 0:
        has_positive = True
print(has_positive)
```

**Series8.**
```python
k = int(input())
n = int(input())
found = False
for _ in range(n):
    if int(input()) < k:
        found = True
print(found)
```

**Series9.**
```python
count = 0
x = int(input())
while x != 0:
    count += 1
    x = int(input())
print(count)
```

**Series10.**
```python
total = 0
while (x := int(input())) != 0:
    if x > 0 and x % 2 == 0:
        total += x
print(total)
```

**Series11.**
```python
k = int(input())
count = 0
while (x := int(input())) != 0:
    if x < k:
        count += 1
print(count)
```

**Series12.**
```python
k = int(input())
first = -1
i = 0
while (x := int(input())) != 0:
    if x > k and first == -1:
        first = i
    i += 1
print(first)
```

**Series13.**
```python
k = int(input())
last = -1
i = 0
while (x := int(input())) != 0:
    if x > k:
        last = i
    i += 1
print(last)
```

**Series14.**
```python
n = int(input())
count = 0
total = 0
for _ in range(n):
    x = int(input())
    if x % 3 == 0:
        count += 1
        total += x
print(count, total)
```

**Series15.**
```python
b = int(input())
n = int(input())
inserted = False
for _ in range(n):
    x = int(input())
    if not inserted and b <= x:
        print(b, end=" ")
        inserted = True
    print(x, end=" ")
if not inserted:
    print(b, end=" ")
print()
```

**Series16.**
```python
n = int(input())
prev = None
for _ in range(n):
    x = int(input())
    if x != prev:
        print(x, end=" ")
        prev = x
print()
```

**Series17.**
```python
n = int(input())
prev = int(input())
count = 0
for _ in range(n - 1):
    x = int(input())
    if x < prev:
        print(x, end=" ")
        count += 1
    prev = x
print()
print(count)
```

**Series18.**
```python
n = int(input())
prev = int(input())
count = 0
for _ in range(n - 1):
    x = int(input())
    if prev < x:
        print(prev, end=" ")
        count += 1
    prev = x
print()
print(count)
```

**Series19.**
```python
n = int(input())
prev = float(input())
increasing = True
for _ in range(n - 1):
    x = float(input())
    if x <= prev:
        increasing = False
    prev = x
print(increasing)
```

**Series20.**
```python
n = int(input())
prev = float(input())
bad = -1
for i in range(1, n):
    x = float(input())
    if x >= prev and bad == -1:
        bad = i
    prev = x
print(bad)
```

**Series21.**
```python
n = int(input())
a = int(input())
b = int(input())
bad = -1
for i in range(2, n):
    c = int(input())
    is_tooth = (b > a and b > c) or (b < a and b < c)
    if not is_tooth and bad == -1:
        bad = i - 1
    a, b = b, c
print(bad)
```

**Series22.**
```python
n = int(input())
current = 0      # сумма после последнего встреченного нуля
answer = 0       # сумма между двумя последними нулями
for _ in range(n):
    x = int(input())
    if x == 0:
        answer = current
        current = 0
    else:
        current += x
print(answer)
```

**Series23.**
```python
n = int(input())
seen_zero = False
running = 0    # сумма после первого нуля
answer = 0     # значение running на момент последнего нуля
for _ in range(n):
    x = int(input())
    if x == 0:
        if seen_zero:
            answer = running
        seen_zero = True
    elif seen_zero:
        running += x
print(answer)
```

**Series24.**
```python
k = int(input())
n = int(input())
for _ in range(n):
    x = int(input())
    power = 1
    for _ in range(k):
        power *= x
    print(power, end=" ")
print()
```

**Series25.**
```python
n = int(input())
for i in range(n):
    x = int(input())
    print(x ** (i + 1), end=" ")
print()
```

**Series26.**
```python
k = int(input())
n = int(input())
for _ in range(k):
    total = 0
    for _ in range(n):
        total += int(input())
    print(total)
```

**Series27.**
```python
k = int(input())
n = int(input())
for _ in range(k):
    first = -1
    for i in range(n):
        if int(input()) == 2 and first == -1:
            first = i
    print(first)
```

**Series28.**
```python
k = int(input())
total = 0
for _ in range(k):
    count = 0
    while int(input()) != 0:
        count += 1
    print(count)
    total += count
print(total)
```

**Series29.**
```python
k = int(input())
for _ in range(k):
    prev = int(input())
    up = down = True
    while (x := int(input())) != 0:
        if x <= prev:
            up = False
        if x >= prev:
            down = False
        prev = x
    if up:
        print(1)
    elif down:
        print(-1)
    else:
        print(0)
```

**Series30.**
```python
k = int(input())
saw_count = 0
for _ in range(k):
    a = int(input())
    b = int(input())
    is_saw = True
    while (c := int(input())) != 0:
        if not ((b > a and b > c) or (b < a and b < c)):
            is_saw = False
        a, b = b, c
    if is_saw:
        saw_count += 1
print(saw_count)
```

---

## 9. Функции

**Func1.**
```python
def cube(x):
    return x ** 3
```

**Func2.**
```python
def sign(x):
    if x > 0:
        return 1
    if x < 0:
        return -1
    return 0
```

**Func3.**
```python
def is_even(n):
    return n % 2 == 0
```

**Func4.**
```python
def rect_info(a, b):
    return 2 * (a + b), a * b


p, s = rect_info(3, 5)
```

**Func5.**
```python
import math


def circle(r):
    return round(2 * math.pi * r, 2), round(math.pi * r ** 2, 2)
```

**Func6.**
```python
def digit_count(n):
    n = abs(n)
    count = 1
    while n >= 10:
        n //= 10
        count += 1
    return count
```

**Func7.**
```python
def digits_sum(n):
    total = 0
    while n > 0:
        total += n % 10
        n //= 10
    return total
```

**Func8.**
```python
def reverse_number(n):
    result = 0
    while n > 0:
        result = result * 10 + n % 10
        n //= 10
    return result
```

**Func9.**
```python
def reverse_number(n):
    result = 0
    while n > 0:
        result = result * 10 + n % 10
        n //= 10
    return result


def is_palindrome(n):
    return n == reverse_number(n)
```

**Func10.**
```python
def is_prime(n):
    if n < 2:
        return False
    d = 2
    while d * d <= n:
        if n % d == 0:
            return False
        d += 1
    return True
```

**Func11.**
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a
```

**Func12.**
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a


def lcm(a, b):
    return a * b // gcd(a, b)
```

**Func13.**
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a


def reduce_fraction(a, b):
    g = gcd(a, b)
    return a // g, b // g
```

**Func14.**
```python
def power(a, n=2):
    result = 1
    for _ in range(n):
        result *= a
    return result
```

**Func15.**
```python
def greet(name, city="Алматы"):
    return f"Привет, {name} из {city}!"
```

**Func16.**
```python
def discount_price(price, percent=10):
    return round(price * (100 - percent) / 100)
```

**Func17.**
```python
def convert(amount, rate=500.0):
    return round(amount / rate, 2)
```

**Func18.**
```python
def min_max3(a, b, c):
    smallest = largest = a
    for x in (b, c):
        if x < smallest:
            smallest = x
        if x > largest:
            largest = x
    return smallest, largest
```

**Func19.**
```python
def sort3(a, b, c):
    if a > b:
        a, b = b, a
    if b > c:
        b, c = c, b
    if a > b:
        a, b = b, a
    return a, b, c
```

**Func20.**
```python
import math


def roots(a, b, c):
    d = b * b - 4 * a * c
    if d < 0:
        return ()
    if d == 0:
        return (-b / (2 * a),)
    x1 = (-b - math.sqrt(d)) / (2 * a)
    x2 = (-b + math.sqrt(d)) / (2 * a)
    return (min(x1, x2), max(x1, x2))
```

**Func21.**
```python
def to_hms(t):
    h, rest = divmod(t, 3600)
    m, s = divmod(rest, 60)
    return h, m, s
```

**Func22.**
```python
def to_seconds(h, m=0, s=0):
    return h * 3600 + m * 60 + s
```

**Func23.**
```python
def to_hms(t):
    h, rest = divmod(t, 3600)
    m, s = divmod(rest, 60)
    return h, m, s


def to_seconds(h, m=0, s=0):
    return h * 3600 + m * 60 + s


def add_time(h, m, s, t):
    return to_hms((to_seconds(h, m, s) + t) % 86400)
```

**Func24.**
```python
def is_leap(year):
    return year % 4 == 0 and year % 100 != 0 or year % 400 == 0
```

**Func25.**
```python
def is_leap(year):
    return year % 4 == 0 and year % 100 != 0 or year % 400 == 0


def month_days(month, year):
    if month == 2:
        return 29 if is_leap(year) else 28
    if month in (4, 6, 9, 11):
        return 30
    return 31
```

**Func26.**
```python
def is_leap(year):
    return year % 4 == 0 and year % 100 != 0 or year % 400 == 0


def month_days(month, year):
    if month == 2:
        return 29 if is_leap(year) else 28
    if month in (4, 6, 9, 11):
        return 30
    return 31


def next_day(d, m, y):
    d += 1
    if d > month_days(m, y):
        d, m = 1, m + 1
        if m > 12:
            m, y = 1, y + 1
    return d, m, y
```

**Func27.**
```python
import math


def dist(x1, y1, x2, y2):
    return math.hypot(x2 - x1, y2 - y1)
```

**Func28.**
```python
import math


def dist(x1, y1, x2, y2):
    return math.hypot(x2 - x1, y2 - y1)


def perimeter(x1, y1, x2, y2, x3, y3):
    p = dist(x1, y1, x2, y2) + dist(x2, y2, x3, y3) + dist(x3, y3, x1, y1)
    return round(p, 2)
```

**Func29.**
```python
import math


def dist(x1, y1, x2, y2):
    return math.hypot(x2 - x1, y2 - y1)


def area(x1, y1, x2, y2, x3, y3):
    a = dist(x1, y1, x2, y2)
    b = dist(x2, y2, x3, y3)
    c = dist(x3, y3, x1, y1)
    p = (a + b + c) / 2
    return round(math.sqrt(p * (p - a) * (p - b) * (p - c)), 2)
```

**Func30.**
```python
def total(*args):
    result = 0
    for x in args:
        result += x
    return result
```

**Func31.**
```python
def average(*args):
    if not args:
        return None
    return round(sum(args) / len(args), 2)
```

**Func32.**
```python
def biggest(first, *rest):
    result = first
    for x in rest:
        if x > result:
            result = x
    return result
```

**Func33.**
```python
def join_words(*words, sep=" "):
    return sep.join(words)
```

**Func34.**
```python
def apply_twice(f, x):
    return f(f(x))
```

**Func35.**
```python
def count_if(pred, *args):
    count = 0
    for x in args:
        if pred(x):
            count += 1
    return count
```

**Func36.**
```python
def sum_of(f, a, b):
    total = 0
    for i in range(a, b + 1):
        total += f(i)
    return total
```

**Func37.**
```python
def make_multiplier(k):
    return lambda x: x * k
```

**Func38.**
```python
def compose(f, g):
    return lambda x: f(g(x))
```

**Func39.**
```python
def sort_by_last_digit(*nums):
    return tuple(sorted(nums, key=lambda n: n % 10))
```

**Func40.**
```python
def bmi(mass, height):
    value = mass / height ** 2
    if value < 18.5:
        category = "дефицит"
    elif value < 25:
        category = "норма"
    else:
        category = "избыток"
    return round(value, 1), category
```

**Func41.**
```python
def coins(amount):
    count = 0
    for coin in (200, 100, 50, 20, 10, 5, 2, 1):
        count += amount // coin
        amount %= coin
    return count
```

**Func42.**
```python
def to_base(n, base=2):
    digits = "0123456789ABCDEF"
    if n == 0:
        return "0"
    result = ""
    while n > 0:
        result = digits[n % base] + result
        n //= base
    return result
```

**Func43.**
```python
def from_base(s, base=2):
    digits = "0123456789ABCDEF"
    result = 0
    for ch in s:
        result = result * base + digits.index(ch)
    return result
```

**Func44.**
```python
def table(f, a, b, step=1):
    lines = ""
    x = a
    while x <= b:
        if lines:
            lines += "\n"
        lines += f"{x} -> {f(x)}"
        x += step
    return lines


print(table(lambda x: x * x - 3, 0, 4, step=2))
```

**Func45.**
```python
def fib(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
    return a
```

**Func46.**
```python
def is_prime(n):
    if n < 2:
        return False
    d = 2
    while d * d <= n:
        if n % d == 0:
            return False
        d += 1
    return True


def goldbach(n):
    for p in range(2, n // 2 + 1):
        if is_prime(p) and is_prime(n - p):
            return p, n - p
```

**Func47.**
```python
def make_counter():
    count = 0

    def counter():
        nonlocal count
        count += 1
        return count

    return counter
```

**Func48.**
```python
def integrate(f, a, b, n=1000):
    h = (b - a) / n
    total = (f(a) + f(b)) / 2
    for i in range(1, n):
        total += f(a + i * h)
    return round(total * h, 3)
```

**Func49.**
```python
def find_root(f, a, b, eps=1e-6):
    while b - a > eps:
        mid = (a + b) / 2
        if f(a) * f(mid) <= 0:
            b = mid
        else:
            a = mid
    return round((a + b) / 2, 4)
```

**Func50.**
```python
def derivative(f, h=1e-6):
    return lambda x: (f(x + h) - f(x - h)) / (2 * h)
```

---

## 10. Минимумы и максимумы

**MinMax1.**
```python
n = int(input())
smallest = largest = int(input())
for _ in range(n - 1):
    x = int(input())
    if x < smallest:
        smallest = x
    if x > largest:
        largest = x
print(smallest, largest)
```

**MinMax2.**
```python
n = int(input())
best_area = -1
best_index = -1
for i in range(n):
    a, b = map(int, input().split())
    if a * b > best_area:
        best_area = a * b
        best_index = i
print(best_area, best_index)
```

**MinMax3.**
```python
n = int(input())
h = int(input())
max_h = min_h = h
max_i = min_i = 0
for i in range(1, n):
    h = int(input())
    if h > max_h:
        max_h, max_i = h, i
    if h < min_h:
        min_h, min_i = h, i
print(max_i, min_i)
```

**MinMax4.**
```python
n = int(input())
x = int(input())
min_x = max_x = x
min_i = max_i = 0
for i in range(1, n):
    x = int(input())
    if x < min_x:
        min_x, min_i = x, i
    if x >= max_x:
        max_x, max_i = x, i
print(min_i, max_i)
```

**MinMax5.**
```python
n = int(input())
min_x = int(input())
first = last = 0
for i in range(1, n):
    x = int(input())
    if x < min_x:
        min_x = x
        first = last = i
    elif x == min_x:
        last = i
print(first, last)
```

**MinMax6.**
```python
n = int(input())
x = int(input())
min_x = max_x = x
min_i = max_i = 0
for i in range(1, n):
    x = int(input())
    if x < min_x:
        min_x, min_i = x, i
    if x > max_x:
        max_x, max_i = x, i
print(min_i if min_i < max_i else max_i)
```

**MinMax7.**
```python
n = int(input())
best = None
for _ in range(n):
    x = float(input())
    if x > 0 and (best is None or x < best):
        best = x
print(best if best is not None else 0)
```

**MinMax8.**
```python
n = int(input())
best = None
best_i = -1
for i in range(n):
    x = int(input())
    if x % 2 != 0 and (best is None or x > best):
        best, best_i = x, i
print(best_i)
```

**MinMax9.**
```python
b = int(input())
n = int(input())
best = None
best_i = -1
for i in range(n):
    x = int(input())
    if x > b and (best is None or x < best):
        best, best_i = x, i
if best is None:
    print("нет")
else:
    print(best, best_i)
```

**MinMax10.**
```python
b = int(input())
c = int(input())
n = int(input())
best = None
best_i = -1
for i in range(n):
    x = int(input())
    if b < x < c and (best is None or x > best):
        best, best_i = x, i
if best is None:
    print("нет")
else:
    print(best, best_i)
```

**MinMax11.**
```python
n = int(input())
min_x = int(input())
min_i = 0
for i in range(1, n):
    x = int(input())
    if x < min_x:
        min_x, min_i = x, i
print(min_i)
```

**MinMax12.**
```python
n = int(input())
max_x = int(input())
first = last = 0
for i in range(1, n):
    x = int(input())
    if x > max_x:
        max_x = x
        first = last = i
    elif x == max_x:
        last = i
print(last - first - 1 if last > first else 0)
```

**MinMax13.**
```python
n = int(input())
min_x = int(input())
count = 1
for _ in range(n - 1):
    x = int(input())
    if x < min_x:
        min_x, count = x, 1
    elif x == min_x:
        count += 1
print(count)
```

**MinMax14.**
```python
n = int(input())
x = float(input())
total = min_x = max_x = x
for _ in range(n - 1):
    x = float(input())
    total += x
    if x < min_x:
        min_x = x
    if x > max_x:
        max_x = x
print(f"{(total - min_x - max_x) / (n - 2):.2f}")
```

**MinMax15.**
```python
n = int(input())
a = int(input())
b = int(input())
first, second = (a, b) if a <= b else (b, a)
for _ in range(n - 2):
    x = int(input())
    if x < first:
        first, second = x, first
    elif x < second:
        second = x
print(first, second)
```

**MinMax16.**
```python
n = int(input())
first = second = third = None
for _ in range(n):
    x = int(input())
    if first is None or x > first:
        first, second, third = x, first, second
    elif second is None or x > second:
        second, third = x, second
    elif third is None or x > third:
        third = x
print(first, second, third)
```

**MinMax17.**
```python
n = int(input())
prev = int(input())
best = None
for _ in range(n - 1):
    x = int(input())
    if best is None or prev + x > best:
        best = prev + x
    prev = x
print(best)
```

**MinMax18.**
```python
n = int(input())
city, temp = max((input().split() for _ in range(n)), key=lambda p: int(p[1]))
print(city)
```

**MinMax19.**
```python
n = int(input())
name, score = min((input().split() for _ in range(n)), key=lambda p: int(p[1]))
print(name, score)
```

**MinMax20.**
```python
words = input().split()
print(max(words, key=len))
print(min(words, key=len))
```

**MinMax21.**
```python
def digits_sum(n):
    total = 0
    while n > 0:
        total += n % 10
        n //= 10
    return total


numbers = map(int, input().split())
print(max(numbers, key=digits_sum))
```

**MinMax22.**
```python
n = int(input())
prev = int(input())
best = None
best_i = 0
for i in range(1, n):
    x = int(input())
    if best is None or prev * x < best:
        best = prev * x
        best_i = i - 1
    prev = x
print(best_i, best_i + 1)
```

**MinMax23.**
```python
n = int(input())
current = best = 0
for _ in range(n):
    if int(input()) % 2 == 0:
        current += 1
        if current > best:
            best = current
    else:
        current = 0
print(best)
```

**MinMax24.**
```python
n = int(input())
prev = int(input())
start = 0
length = 1
best_start, best_len = 0, 1
for i in range(1, n):
    x = int(input())
    if x == prev:
        length += 1
    else:
        start, length = i, 1
    if length > best_len:
        best_start, best_len = start, length
    prev = x
print(best_start, best_len)
```

**MinMax25.**
```python
n = int(input())
min_x = None
run = best = 0
prev = None
for _ in range(n):
    x = int(input())
    if min_x is None or x < min_x:
        min_x = x
        run = best = 1
    elif x == min_x:
        run = run + 1 if prev == min_x else 1
        if run > best:
            best = run
    prev = x
print(best)
```

---

## 11. Списки

**List1.**
```python
n = int(input())
evens = [2 * i for i in range(1, n + 1)]
print(*evens)
```

**List2.**
```python
n = int(input())
squares = [i * i for i in range(1, n + 1)]
print(squares)
```

**List3.**
```python
s, p, n = input().split()
s, p, n = float(s), float(p), int(n)
balances = []
for _ in range(n):
    s *= 1 + p / 100
    balances.append(s)
print(*(f"{x:.2f}" for x in balances))
```

**List4.**
```python
n, a, b = map(int, input().split())
plan = [a, b]
while len(plan) < n:
    plan.append(plan[-1] + plan[-2])
print(*plan)
```

**List5.**
```python
a = list(map(int, input().split()))
print(*a[::-1])
```

**List6.**
```python
temps = list(map(int, input().split()))
print(*temps[::2])
```

**List7.**
```python
a = list(map(int, input().split()))
print(*a[1::2][::-1])
```

**List8.**
```python
prices = list(map(int, input().split()))
new_prices = [p * 85 // 100 for p in prices]
print(*new_prices)
```

**List9.**
```python
a = list(map(int, input().split()))
k = int(input())
print(*a[:k])
print(*a[-k:])
```

**List10.**
```python
deposits = list(map(int, input().split()))
totals = []
current = 0
for d in deposits:
    current += d
    totals.append(current)
print(*totals)
```

**List11.**
```python
marks = list(map(int, input().split()))
total = sum(marks)
print(total)
print(f"{total / len(marks):.2f}")
```

**List12.**
```python
a = list(map(int, input().split()))
average = sum(a) / len(a)
count = len([x for x in a if x > average])
print(count)
```

**List13.**
```python
a = list(map(int, input().split()))
for i, x in enumerate(a):
    if x % 2 == 0:
        print(i)
        break
else:
    print(-1)
```

**List14.**
```python
a = list(map(int, input().split()))
x = int(input())
positions = [i for i, v in enumerate(a) if v == x]
if positions:
    print(*positions)
else:
    print("нет")
```

**List15.**
```python
a = list(map(int, input().split()))
print(all(x < y for x, y in zip(a, a[1:])))
```

**List16.**
```python
a = list(map(int, input().split()))
d = a[1] - a[0]
if all(y - x == d for x, y in zip(a, a[1:])):
    print(d)
else:
    print("не прогрессия")
```

**List17.**
```python
h = list(map(int, input().split()))
peaks = 0
for i in range(1, len(h) - 1):
    if h[i - 1] < h[i] > h[i + 1]:
        peaks += 1
print(peaks)
```

**List18.**
```python
a = list(map(int, input().split()))
answer = -1
for i in range(len(a) - 2, 0, -1):
    if a[i - 1] > a[i] < a[i + 1]:
        answer = i
        break
print(answer)
```

**List19.**
```python
a = list(map(int, input().split()))
changes = sum(1 for x, y in zip(a, a[1:]) if x * y < 0)
print(changes)
```

**List20.**
```python
sales = list(map(int, input().split()))
best = max(range(len(sales) - 1), key=lambda i: sales[i] + sales[i + 1])
print(best, sales[best] + sales[best + 1])
```

**List21.**
```python
a = list(map(int, input().split()))
r = int(input())
print(min(a, key=lambda x: abs(x - r)))
```

**List22.**
```python
a = list(map(int, input().split()))
distinct = 1 + sum(1 for x, y in zip(a, a[1:]) if x != y)
print(distinct)
```

**List23.**
```python
a = list(map(int, input().split()))
first = a.index(0)
last = len(a) - 1 - a[::-1].index(0)
print(sum(a[first + 1:last]))
```

**List24.**
```python
a = list(map(int, input().split()))
print("да" if a == a[::-1] else "нет")
```

**List25.**
```python
a = list(map(int, input().split()))
total = sum(a)
smallest = min(a)
a.insert(0, smallest)
a.append(total)
print(*a)
```

**List26.**
```python
a = list(map(int, input().split()))
x = int(input())
print(a.count(x))
print(a.index(x) if x in a else -1)
```

**List27.**
```python
data = list(map(int, input().split()))
data = [x for x in data if x != 0]
print(*data)
```

**List28.**
```python
a = list(map(int, input().split()))
k = int(input())
removed = a.pop(k)
print(removed)
print(*a)
```

**List29.**
```python
a = list(map(int, input().split()))
print(*sorted(a))
print(*sorted(a, reverse=True))
print(*a)
```

**List30.**
```python
queue = input().split()
name = input()
if name in queue:
    print(queue.index(name) + 1)
else:
    print("нет в очереди")
```

**List31.**
```python
a = list(map(int, input().split()))
b = list(map(int, input().split()))
print(*sorted(a + b))
```

**List32.**
```python
scores = list(map(int, input().split()))
print(*sorted(scores, reverse=True)[:3])
```

**List33.**
```python
a = sorted(map(int, input().split()))
n = len(a)
if n % 2 == 1:
    median = a[n // 2]
else:
    median = (a[n // 2 - 1] + a[n // 2]) / 2
print(f"{median:.1f}")
```

**List34.**
```python
values = sorted(set(map(int, input().split())))
print(values[-2] if len(values) >= 2 else "нет")
```

**List35.**
```python
a = list(map(int, input().split()))
a[0], a[-1] = a[-1], a[0]
print(*a)
```

**List36.**
```python
a = list(map(int, input().split()))
for i in range(0, len(a) - 1, 2):
    a[i], a[i + 1] = a[i + 1], a[i]
print(*a)
```

**List37.**
```python
a = list(map(int, input().split()))
left, right = 0, len(a) - 1
while left < right:
    a[left], a[right] = a[right], a[left]
    left += 1
    right -= 1
print(*a)
```

**List38.**
```python
a = list(map(int, input().split()))
k, l = map(int, input().split())
a[k:l + 1] = a[k:l + 1][::-1]
print(*a)
```

**List39.**
```python
a = list(map(int, input().split()))
i = a.index(min(a))
j = a.index(max(a))
a[i], a[j] = a[j], a[i]
print(*a)
```

**List40.**
```python
cabins = list(map(int, input().split()))
cabins.insert(0, cabins.pop())
print(*cabins)
```

**List41.**
```python
a = list(map(int, input().split()))
k = int(input()) % len(a)
a = a[k:] + a[:k]
print(*a)
```

**List42.**
```python
belt = list(map(int, input().split()))
k = int(input())
belt = [0] * k + belt[:len(belt) - k]
print(*belt)
```

**List43.**
```python
a = list(map(int, input().split()))
k = int(input())
n = len(a)
shifted = [a[(i - k) % n] for i in range(n)]
print(*shifted)
```

**List44.**
```python
a = list(map(int, input().split()))
i = a.index(min(a))
print(*(a[i:] + a[:i]))
```

**List45.**
```python
a = list(map(int, input().split()))
print(*(a[::2] + a[1::2]))
```

**List46.**
```python
cards = list(map(int, input().split()))
half = len(cards) // 2
shuffled = []
for x, y in zip(cards[:half], cards[half:]):
    shuffled.extend([x, y])
print(*shuffled)
```

**List47.**
```python
a = list(map(int, input().split()))
k = int(input())
del a[k]
print(*a)
```

**List48.**
```python
a = list(map(int, input().split()))
del a[1::2]
print(*a)
```

**List49.**
```python
a = list(map(int, input().split()))
biggest = max(a)
a = [x for x in a if x != biggest]
print(*a)
```

**List50.**
```python
a = list(map(int, input().split()))
unique = []
for x in a:
    if x not in unique:
        unique.append(x)
print(*unique)
```

**List51.**
```python
a = list(map(int, input().split()))
single = [x for x in a if a.count(x) == 1]
if single:
    print(*single)
else:
    print("пусто")
```

**List52.**
```python
a = list(map(int, input().split()))
i = 0
while i < len(a):
    if a[i] < 0:
        del a[i]
    else:
        i += 1
if a:
    print(*a)
else:
    print("пусто")
```

**List53.**
```python
a = list(map(int, input().split()))
k, x = map(int, input().split())
a.insert(k, x)
print(*a)
```

**List54.**
```python
a = list(map(int, input().split()))
result = []
for x in a:
    result.append(x)
    if x < 0:
        result.append(0)
print(*result)
```

**List55.**
```python
a = list(map(int, input().split()))
result = []
for x in a:
    result.append(x)
    if x % 2 == 0:
        result.append(x)
print(*result)
```

**List56.**
```python
a = list(map(int, input().split()))
result = [a[0]]
for x, y in zip(a, a[1:]):
    result.extend([x + y, y])
print(*result)
```

**List57.**
```python
a = list(map(int, input().split()))
indexes = list(map(int, input().split()))
for i in sorted(indexes, reverse=True):
    del a[i]
print(*a)
```

**List58.**
```python
codes = list(map(int, input().split()))
banned = list(map(int, input().split()))
codes = [c for c in codes if c not in banned]
if codes:
    print(*codes)
else:
    print("пусто")
```

**List59.**
```python
scored = list(map(int, input().split()))
missed = list(map(int, input().split()))
results = []
points = 0
for s, m in zip(scored, missed):
    if s > m:
        results.append("В")
        points += 3
    elif s == m:
        results.append("Н")
        points += 1
    else:
        results.append("П")
print(*results)
print(points)
```

**List60.**
```python
from itertools import zip_longest

a = list(map(int, input().split()))
b = list(map(int, input().split()))
print(*(x + y for x, y in zip_longest(a, b, fillvalue=0)))
```

**List61.**
```python
rates = list(map(int, input().split()))
growth = False
for i, rate in enumerate(rates[1:], start=1):
    if rate > rates[i - 1]:
        print(f"{i}: {rate}")
        growth = True
if not growth:
    print("нет")
```

**List62.**
```python
a = list(map(int, input().split()))
print(*[x * x for x in a if x % 2 != 0])
```

**List63.**
```python
cities = input().split()
population = list(map(int, input().split()))
x = int(input())
for city, people in zip(cities, population):
    if people > x:
        print(city)
```

**List64.**
```python
amounts = list(map(int, input().split()))
prices = list(map(int, input().split()))
print(sum(q * p for q, p in zip(amounts, prices)))
```

**List65.**
```python
marks = list(map(int, input().split()))
gaps = [y - x for x, y in zip(marks, marks[1:])]
print(*gaps)
print(max(gaps))
```

**List66.**
```python
a = list(map(int, input().split()))
smallest = min(a)
print(*[x - smallest for x in a])
```

**List67.**
```python
names = input().split()
scores = list(map(int, input().split()))
best = max(scores)
print(*[name for name, s in zip(names, scores) if s == best])
```

**List68.**
```python
visitors = list(map(int, input().split()))
averages = [sum(visitors[i:i + 3]) / 3 for i in range(len(visitors) - 2)]
print(*(f"{x:.2f}" for x in averages))
```

**List69.**
```python
a = list(map(int, input().split()))
n = len(a)
swaps = 0
for i in range(n - 1):
    for j in range(n - 1 - i):
        if a[j] > a[j + 1]:
            a[j], a[j + 1] = a[j + 1], a[j]
            swaps += 1
print(*a)
print(swaps)
```

**List70.**
```python
a = list(map(int, input().split()))
n = len(a)
for i in range(n - 1):
    best = i
    for j in range(i + 1, n):
        if a[j] > a[best]:
            best = j
    a[i], a[best] = a[best], a[i]
print(*a)
```

**List71.**
```python
a = list(map(int, input().split()))
x = int(input())
pos = 0
while pos < len(a) and a[pos] <= x:
    pos += 1
a.insert(pos, x)
print(*a)
```

**List72★.**
```python
a = list(map(int, input().split()))
print(*sorted(a, key=lambda x: (x % 10, x)))
```

**List73★.**
```python
a = list(map(int, input().split()))
evens = sorted(x for x in a if x % 2 == 0)
k = 0
for i in range(len(a)):
    if a[i] % 2 == 0:
        a[i] = evens[k]
        k += 1
print(*a)
```

**List74★.**
```python
a = list(map(int, input().split()))
best_len, best_val = 1, a[0]
cur_len = 1
for i in range(1, len(a)):
    if a[i] == a[i - 1]:
        cur_len += 1
    else:
        cur_len = 1
    if cur_len > best_len:
        best_len, best_val = cur_len, a[i]
print(best_len, best_val)
```

**List75★.**
```python
a = list(map(int, input().split()))
best_start, best_len = 0, 1
start = 0
for i in range(1, len(a)):
    if a[i] <= a[i - 1]:
        start = i
    if i - start + 1 > best_len:
        best_start, best_len = start, i - start + 1
print(*a[best_start:best_start + best_len])
```

**List76★.**
```python
a = list(map(int, input().split()))
pairs = []
count = 1
for i in range(1, len(a) + 1):
    if i < len(a) and a[i] == a[i - 1]:
        count += 1
    else:
        pairs.append(f"{a[i - 1]}:{count}")
        count = 1
print(*pairs)
```

**List77★.**
```python
a = list(map(int, input().split()))
best = current = a[0]
for x in a[1:]:
    current = max(x, current + x)
    best = max(best, current)
print(best)
```

**List78★.**
```python
prices = list(map(int, input().split()))
s = int(input())
seen = {}
pairs = 0
for p in prices:
    pairs += seen.get(s - p, 0)
    seen[p] = seen.get(p, 0) + 1
print(pairs)
```

**List79★.**
```python
a = list(map(int, input().split()))
b = list(map(int, input().split()))
i = j = 0
merged = []
while i < len(a) and j < len(b):
    if a[i] <= b[j]:
        merged.append(a[i])
        i += 1
    else:
        merged.append(b[j])
        j += 1
merged.extend(a[i:])
merged.extend(b[j:])
print(*merged)
```

**List80★.**
```python
n, k = map(int, input().split())
children = list(range(1, n + 1))
order = []
pos = 0
while children:
    pos = (pos + k - 1) % len(children)
    order.append(children.pop(pos))
print(*order)
```

---

## 12. Вложенные списки (матрицы)

**Matrix1.**
```python
m, n = map(int, input().split())
hall = [[i + 1] * n for i in range(m)]
for row in hall:
    print(*row)
```

**Matrix2.**
```python
m, n = map(int, input().split())
table = [[(i + 1) * (j + 1) for j in range(n)] for i in range(m)]
for row in table:
    print(*row)
```

**Matrix3.**
```python
m, n = map(int, input().split())
seats = [[i * n + j + 1 for j in range(n)] for i in range(m)]
for row in seats:
    print(*row)
```

**Matrix4.**
```python
m, n = map(int, input().split())
a = [[j * m + i + 1 for j in range(n)] for i in range(m)]
for row in a:
    print(*row)
```

**Matrix5.**
```python
m, n = map(int, input().split())
board = [[1 if (i + j) % 2 == 0 else 0 for j in range(n)] for i in range(m)]
for row in board:
    print(*row)
```

**Matrix6.**
```python
n = int(input())
e = [[1 if i == j else 0 for j in range(n)] for i in range(n)]
for row in e:
    print(*row)
```

**Matrix7.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
for row in a:
    print("".join(f"{x:5}" for x in row))
```

**Matrix8.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = int(input())
print(*a[k])
print(sum(a[k]))
```

**Matrix9.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = int(input())
column = [row[k] for row in a]
product = 1
for x in column:
    product *= x
print(*column)
print(product)
```

**Matrix10.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
print(*[sum(row) for row in a])
```

**Matrix11.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
averages = []
for j in range(n):
    total = 0
    for i in range(m):
        total += a[i][j]
    averages.append(f"{total / m:.1f}")
print(*averages)
```

**Matrix12.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
total = sum(sum(row) for row in a)
print(total)
print(f"{total / (m * n):.2f}")
```

**Matrix13.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
print(*[min(row) for row in a])
```

**Matrix14.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
result = []
for j in range(n):
    best = a[0][j]
    for i in range(1, m):
        if a[i][j] > best:
            best = a[i][j]
    result.append(best)
print(*result)
```

**Matrix15.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
pos = neg = zero = 0
for row in a:
    for x in row:
        if x > 0:
            pos += 1
        elif x < 0:
            neg += 1
        else:
            zero += 1
print(pos, neg, zero)
```

**Matrix16.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
print(*[sum(1 for x in row if x % 2 == 0) for row in a])
```

**Matrix17.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
best = 0
for i in range(1, m):
    if sum(a[i]) > sum(a[best]):
        best = i
print(best)
```

**Matrix18.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
bi, bj = 0, 0
for i in range(m):
    for j in range(n):
        if a[i][j] < a[bi][bj]:
            bi, bj = i, j
print(bi, bj)
```

**Matrix19.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
print(sum(1 for row in a if all(x > 0 for x in row)))
```

**Matrix20.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
cols = [j for j in range(n) if any(a[i][j] == 0 for i in range(m))]
if cols:
    print(*cols)
else:
    print("нет")
```

**Matrix21.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
count = 0
for row in a:
    if all(row[j] < row[j + 1] for j in range(n - 1)):
        count += 1
print(count)
```

**Matrix22.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
biggest = max(max(row) for row in a)
for i in range(m):
    for j in range(n):
        if a[i][j] == biggest:
            print(i, j)
```

**Matrix23.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
result = []
for i, row in enumerate(a):
    result.extend(row if i % 2 == 0 else row[::-1])
print(*result)
```

**Matrix24.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
for row in zip(*a):
    print(*row)
```

**Matrix25.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k1, k2 = map(int, input().split())
a[k1], a[k2] = a[k2], a[k1]
for row in a:
    print(*row)
```

**Matrix26.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k1, k2 = map(int, input().split())
for row in a:
    row[k1], row[k2] = row[k2], row[k1]
for row in a:
    print(*row)
```

**Matrix27.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
a = a[::-1]
for row in a:
    print(*row)
```

**Matrix28.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
for row in a:
    row.reverse()
for row in a:
    print(*row)
```

**Matrix29.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = 0
for i in range(m):
    if min(a[i]) < min(a[k]):
        k = i
a[0], a[k] = a[k], a[0]
for row in a:
    print(*row)
```

**Matrix30.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = int(input())
del a[k]
for row in a:
    print(*row)
```

**Matrix31.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = int(input())
for row in a:
    del row[k]
for row in a:
    print(*row)
```

**Matrix32.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = int(input())
a.insert(k + 1, [0] * n)
for row in a:
    print(*row)
```

**Matrix33.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = int(input()) % m
a = a[m - k:] + a[:m - k]
for row in a:
    print(*row)
```

**Matrix34.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
for row in a:
    row.append(sum(row))
a.append([sum(col) for col in zip(*a)])
for row in a:
    print(*row)
```

**Matrix35.**
```python
n = int(input())
a = [list(map(int, input().split())) for _ in range(n)]
print(sum(a[i][i] for i in range(n)))
print(sum(a[i][n - 1 - i] for i in range(n)))
```

**Matrix36.**
```python
n = int(input())
a = [list(map(int, input().split())) for _ in range(n)]
for i in range(n):
    for j in range(i + 1, n):
        a[i][j] = 0
for row in a:
    print(*row)
```

**Matrix37.**
```python
n = int(input())
a = [list(map(int, input().split())) for _ in range(n)]
print(all(a[i][j] == a[j][i] for i in range(n) for j in range(n)))
```

**Matrix38.**
```python
n = int(input())
a = [list(map(int, input().split())) for _ in range(n)]
rotated = [list(row) for row in zip(*a[::-1])]
for row in rotated:
    print(*row)
```

**Matrix39.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
a = sorted(a, key=sum)
for row in a:
    print(*row)
```

**Matrix40.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
columns = sorted(zip(*a), key=max, reverse=True)
for row in zip(*columns):
    print(*row)
```

**Matrix41.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
b = [list(map(int, input().split())) for _ in range(m)]
for row_a, row_b in zip(a, b):
    print(*[x + y for x, y in zip(row_a, row_b)])
```

**Matrix42.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = int(input())
answer = -1
for i in range(m):
    run = 0
    for x in a[i]:
        run = run + 1 if x == 0 else 0
        if run >= k:
            break
    if run >= k:
        answer = i
        break
print(answer)
```

**Matrix43.**
```python
n = int(input())
a = [list(map(int, input().split())) for _ in range(n)]
target = sum(a[0])
sums = [sum(row) for row in a]
sums += [sum(col) for col in zip(*a)]
sums.append(sum(a[i][i] for i in range(n)))
sums.append(sum(a[i][n - 1 - i] for i in range(n)))
print(all(s == target for s in sums))
```

**Matrix44★.**
```python
m, k, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
b = [list(map(int, input().split())) for _ in range(k)]
c = [[sum(a[i][t] * b[t][j] for t in range(k)) for j in range(n)]
     for i in range(m)]
for row in c:
    print(*row)
```

**Matrix45★.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
col_max = [max(col) for col in zip(*a)]
found = False
for i in range(m):
    row_min = min(a[i])
    for j in range(n):
        if a[i][j] == row_min and a[i][j] == col_max[j]:
            print(i, j)
            found = True
if not found:
    print("нет")
```

**Matrix46★.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
top, bottom, left, right = 0, m - 1, 0, n - 1
result = []
while top <= bottom and left <= right:
    for j in range(left, right + 1):
        result.append(a[top][j])
    for i in range(top + 1, bottom + 1):
        result.append(a[i][right])
    if top < bottom:
        for j in range(right - 1, left - 1, -1):
            result.append(a[bottom][j])
    if left < right:
        for i in range(bottom - 1, top, -1):
            result.append(a[i][left])
    top, bottom, left, right = top + 1, bottom - 1, left + 1, right - 1
print(*result)
```

**Matrix47★.**
```python
n = int(input())
a = [[0] * n for _ in range(n)]
di = [0, 1, 0, -1]
dj = [1, 0, -1, 0]
i = j = d = 0
for value in range(1, n * n + 1):
    a[i][j] = value
    ni, nj = i + di[d], j + dj[d]
    if not (0 <= ni < n and 0 <= nj < n) or a[ni][nj] != 0:
        d = (d + 1) % 4
        ni, nj = i + di[d], j + dj[d]
    i, j = ni, nj
for row in a:
    print("".join(f"{x:3}" for x in row))
```

**Matrix48★.**
```python
m, n = map(int, input().split())
field = [input() for _ in range(m)]
for i in range(m):
    line = ""
    for j in range(n):
        if field[i][j] == "*":
            line += "*"
            continue
        mines = 0
        for di in (-1, 0, 1):
            for dj in (-1, 0, 1):
                ni, nj = i + di, j + dj
                if 0 <= ni < m and 0 <= nj < n and field[ni][nj] == "*":
                    mines += 1
        line += str(mines)
    print(line)
```

**Matrix49★.**
```python
m, n = map(int, input().split())
grid = [input() for _ in range(m)]
new_grid = []
for i in range(m):
    line = ""
    for j in range(n):
        alive = 0
        for di in (-1, 0, 1):
            for dj in (-1, 0, 1):
                if di == dj == 0:
                    continue
                ni, nj = i + di, j + dj
                if 0 <= ni < m and 0 <= nj < n and grid[ni][nj] == "#":
                    alive += 1
        if grid[i][j] == "#":
            line += "#" if alive in (2, 3) else "."
        else:
            line += "#" if alive == 3 else "."
    new_grid.append(line)
print("\n".join(new_grid))
```

**Matrix50★.**
```python
m, n = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(m)]
k = int(input())
best = None
for i in range(m - k + 1):
    for j in range(n - k + 1):
        s = sum(sum(a[r][j:j + k]) for r in range(i, i + k))
        if best is None or s > best[0]:
            best = (s, i, j)
print(best[1], best[2])
print(best[0])
```

---

## 13. Строки

**String1.**
```python
c = input()
print(ord(c))
```

**String2.**
```python
n = int(input())
print(chr(n))
```

**String3.**
```python
c = input()
print(chr(ord(c) - 1), chr(ord(c) + 1))
```

**String4.**
```python
n = int(input())
print(*[chr(ord("A") + i) for i in range(n)])
```

**String5.**
```python
n = int(input())
print(*[chr(ord("z") - i) for i in range(n)])
```

**String6.**
```python
c = input()
low = c.lower()
if "0" <= c <= "9":
    print("цифра")
elif "a" <= low <= "z":
    print("латинская буква")
elif "а" <= low <= "я" or low == "ё":
    print("русская буква")
else:
    print("другой символ")
```

**String7.**
```python
s = input()
print(len(s))
print(s[0], s[-1])
```

**String8.**
```python
n = int(input())
c = input()
print(c * n)
print(((c + "-") * n)[:n])
```

**String9.**
```python
s = input()
print(" ".join(s))
```

**String10.**
```python
s = input()
print(s[::-1])
```

**String11.**
```python
s = input()
print(s[::2])
print(s[1::2])
```

**String12.**
```python
s = input()
n = int(input())
print(s[:n])
print(s[-n:])
```

**String13.**
```python
s = input()
print(sum(1 for c in s if c.isdigit()))
```

**String14.**
```python
s = input()
vowels = "аеёиоуыэюя"
print(sum(1 for c in s.lower() if c in vowels))
```

**String15.**
```python
s = input()
print(s.upper())
print(s.lower())
print(s.title())
```

**String16.**
```python
s = input()
result = ""
for c in s:
    if c.isupper():
        result += c.lower()
    elif c.islower():
        result += c.upper()
    else:
        result += c
print(result)
```

**String17.**
```python
day, month, year = input().split(".")
print(f"{year}-{month}-{day}")
```

**String18.**
```python
s = input()
print("".join(c for c in s if c.isdigit()))
```

**String19.**
```python
s = input()
digits_total = sum(1 for c in s if c.isdigit())
seen = 0
result = ""
for c in s:
    if c.isdigit():
        seen += 1
        result += c if seen > digits_total - 4 else "*"
    else:
        result += c
print(result)
```

**String20.**
```python
s = input()
print(len(s) == 12 and s.isdigit())
```

**String21.**
```python
s = input()
print(" ".join(s))
print(sum(int(c) for c in s))
```

**String22.**
```python
s = input()
c = input()
print(s.replace(c, c * 2))
```

**String23.**
```python
s = input()
t = input()
print(t in s)
print(s.count(t))
print(s.find(t))
```

**String24.**
```python
s = input()
t = input()
print(s.replace(t, ""))
```

**String25.**
```python
s = input()
a = input()
b = input()
print(s.replace(a, b, 1))
i = s.rfind(a)
print(s[:i] + b + s[i + len(a):])
```

**String26.**
```python
s = input()
print(len(s.split()))
```

**String27.**
```python
s = input()
print(" ".join(s.split()))
```

**String28.**
```python
words = input().split()
longest = words[0]
for w in words:
    if len(w) > len(longest):
        longest = w
print(longest)
```

**String29.**
```python
words = input().split()
print(" ".join(words[::-1]))
```

**String30.**
```python
words = input().split()
print(" ".join(w[::-1] for w in words))
```

**String31.**
```python
cities = input().split()
print(" ".join(sorted(cities, key=str.lower)))
```

**String32.**
```python
words = input().split()
print(" ".join(w[0].upper() + w[1:].lower() for w in words))
```

**String33.**
```python
words = input().split()
print(sum(1 for w in words if w[0].lower() == w[-1].lower()))
```

**String34.**
```python
parts = input().split(",")
print(sum(int(p) for p in parts))
```

**String35.**
```python
last, first, middle = input().split()
print(f"{last} {first[0]}. {middle[0]}.")
```

**String36.**
```python
path = input()
file_name = path.rsplit("/", 1)[-1]
name, ext = file_name.rsplit(".", 1)
print(name)
print(ext)
```

**String37.**
```python
s = input()
print(s == s[::-1])
```

**String38.**
```python
s = input()
clean = "".join(c.lower() for c in s if c.isalnum())
print(clean == clean[::-1])
```

**String39.**
```python
a = input().lower().replace(" ", "")
b = input().lower().replace(" ", "")
print(sorted(a) == sorted(b))
```

**String40.**
```python
p = input()
strong = (len(p) >= 8
          and any(c.isdigit() for c in p)
          and any(c.isupper() for c in p)
          and any(c.islower() for c in p))
print("надёжный" if strong else "слабый")
```

**String41.**
```python
hours, minutes = map(int, input().split(":"))
travel = int(input())
total = (hours * 60 + minutes + travel) % (24 * 60)
print(f"{total // 60:02}:{total % 60:02}")
```

**String42.**
```python
s = input()
ok = (len(s) == 8
      and s[:3].isdigit()
      and all("A" <= c <= "Z" for c in s[3:6])
      and s[6:].isdigit())
print(ok)
```

**String43.**
```python
s = input()
value = 0
for c in s:
    value = value * 2 + int(c)
print(value)
```

**String44.**
```python
n = int(input())
if n == 0:
    print("0")
else:
    bits = ""
    while n > 0:
        bits = str(n % 2) + bits
        n //= 2
    print(bits)
```

**String45.**
```python
s = input()
total = 0
number = 0
sign = 1
for c in s:
    if c.isdigit():
        number = number * 10 + int(c)
    else:
        total += sign * number
        number = 0
        sign = 1 if c == "+" else -1
total += sign * number
print(total)
```

**String46.**
```python
s = input()
groups = []
while len(s) > 3:
    groups.append(s[-3:])
    s = s[:-3]
groups.append(s)
print(" ".join(groups[::-1]))
```

**String47.**
```python
n = int(input())
total = 0
for _ in range(n):
    name, price = input().split()
    total += int(price)
    print(f"{name:<12}{int(price):>8}")
print("-" * 20)
print(f"{'ИТОГО':<12}{total:>8}")
```

**String48.**
```python
s = input()
k = int(input())
result = ""
for c in s:
    if "a" <= c <= "z":
        result += chr((ord(c) - ord("a") + k) % 26 + ord("a"))
    elif "A" <= c <= "Z":
        result += chr((ord(c) - ord("A") + k) % 26 + ord("A"))
    else:
        result += c
print(result)
```

**String49.**
```python
alphabet = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя"
s = input()
k = int(input())
result = ""
for c in s:
    pos = alphabet.find(c.lower())
    if pos == -1:
        result += c
    else:
        new = alphabet[(pos + k) % len(alphabet)]
        result += new.upper() if c.isupper() else new
print(result)
```

**String50.**
```python
a = [int(x) for x in input().split(".")]
b = [int(x) for x in input().split(".")]
length = max(len(a), len(b))
a += [0] * (length - len(a))
b += [0] * (length - len(b))
if a > b:
    print(">")
elif a < b:
    print("<")
else:
    print("=")
```

**String51.**
```python
s = input()
best_char, best_len = s[0], 1
run = 1
for i in range(1, len(s)):
    run = run + 1 if s[i] == s[i - 1] else 1
    if run > best_len:
        best_char, best_len = s[i], run
print(best_char, best_len)
```

**String52★.**
```python
s = input()
result = ""
i = 0
while i < len(s):
    j = i
    while j < len(s) and s[j] == s[i]:
        j += 1
    count = j - i
    result += s[i] + (str(count) if count > 1 else "")
    i = j
print(result)
```

**String53★.**
```python
s = input()
result = ""
i = 0
while i < len(s):
    letter = s[i]
    i += 1
    digits = ""
    while i < len(s) and s[i].isdigit():
        digits += s[i]
        i += 1
    result += letter * (int(digits) if digits else 1)
print(result)
```

**String54★.**
```python
s = input()
pairs = {")": "(", "]": "[", "}": "{"}
stack = []
ok = True
for c in s:
    if c in "([{":
        stack.append(c)
    elif not stack or stack.pop() != pairs[c]:
        ok = False
        break
print(ok and not stack)
```

**String55★.**
```python
text = input()
key = input().upper()
result = ""
k = 0
for c in text:
    if c.isascii() and c.isalpha():
        shift = ord(key[k % len(key)]) - ord("A")
        base = ord("A") if c.isupper() else ord("a")
        result += chr((ord(c) - base + shift) % 26 + base)
        k += 1
    else:
        result += c
print(result)
```

**String56★.**
```python
values = {"I": 1, "V": 5, "X": 10, "L": 50, "C": 100, "D": 500, "M": 1000}
s = input()
total = 0
for i, c in enumerate(s):
    if i + 1 < len(s) and values[c] < values[s[i + 1]]:
        total -= values[c]
    else:
        total += values[c]
print(total)
```

**String57★.**
```python
n = int(input())
table = [(1000, "M"), (900, "CM"), (500, "D"), (400, "CD"),
         (100, "C"), (90, "XC"), (50, "L"), (40, "XL"),
         (10, "X"), (9, "IX"), (5, "V"), (4, "IV"), (1, "I")]
result = ""
for value, symbol in table:
    while n >= value:
        result += symbol
        n -= value
print(result)
```

**String58★.**
```python
n, b = map(int, input().split())
digits = "0123456789ABCDEF"
if n == 0:
    print("0")
else:
    result = ""
    while n > 0:
        result = digits[n % b] + result
        n //= b
    print(result)
```

**String59★.**
```python
s = input()


def control_digit(digits):
    for weights in (range(1, 12), [3, 4, 5, 6, 7, 8, 9, 10, 11, 1, 2]):
        rest = sum(d * w for d, w in zip(digits, weights)) % 11
        if rest != 10:
            return rest
    return None


if len(s) == 12 and s.isdigit():
    digits = [int(c) for c in s]
    print(control_digit(digits[:11]) == digits[11])
else:
    print(False)
```

**String60★.**
```python
a = input()
b = input()
length = max(len(a), len(b))
a = a.zfill(length)
b = b.zfill(length)
result = ""
carry = 0
for i in range(length - 1, -1, -1):
    s = int(a[i]) + int(b[i]) + carry
    result = str(s % 10) + result
    carry = s // 10
if carry:
    result = str(carry) + result
print(result)
```

---

## 14. Словари и множества

**Dict1.**
```python
words = input().split()
print(len(set(words)))
```

**Dict2.**
```python
nums = input().split()
seen = set()
result = []
for x in nums:
    if x not in seen:
        seen.add(x)
        result.append(x)
print(' '.join(result))
```

**Dict3.**
```python
word = input().lower()
print(len(set(word)) == len(word))
```

**Dict4.**
```python
physics = set(map(int, input().split()))
math = set(map(int, input().split()))
both = sorted(physics & math)
if both:
    print(*both)
else:
    print('нет')
```

**Dict5.**
```python
chess = set(input().split())
football = set(input().split())
print(*sorted(chess ^ football))
print(len(chess | football))
```

**Dict6.**
```python
import string

text = set(input().lower())
missing = set(string.ascii_lowercase) - text
if missing:
    print(''.join(sorted(missing)))
else:
    print('pangram')
```

**Dict7.**
```python
a = set(input().replace(' ', ''))
b = set(input().replace(' ', ''))
print(a == b)
```

**Dict8.**
```python
need = set(input().split())
have = set(input().split())
if need.issubset(have):
    print('Можно готовить')
else:
    print('Купить:', *sorted(need - have))
```

**Dict9.**
```python
words = input().split()
lengths = {len(w) for w in words}
print(*sorted(lengths))
```

**Dict10.**
```python
n = int(input())
squares = {i: i * i for i in range(1, n + 1)}
print(squares)
```

**Dict11.**
```python
n = int(input())
prices = {}
for _ in range(n):
    name, price = input().split()
    prices[name] = int(price)
item = input()
print(prices.get(item, 'нет в продаже'))
```

**Dict12.**
```python
n = int(input())
capitals = {}
for _ in range(n):
    country, city = input().split()
    capitals[country] = city
country_of = {city: country for country, city in capitals.items()}
m = int(input())
for _ in range(m):
    print(country_of[input()])
```

**Dict13.**
```python
word = input()
count = {}
for ch in word:
    count[ch] = count.get(ch, 0) + 1
for ch, k in count.items():
    print(f'{ch}: {k}')
```

**Dict14.**
```python
nums = list(map(int, input().split()))
count = {}
for x in nums:
    count[x] = count.get(x, 0) + 1
dups = sorted(x for x, k in count.items() if k > 1)
print(*dups if dups else ['нет'])
```

**Dict15.**
```python
s = input()
count = {}
for ch in s:
    count[ch] = count.get(ch, 0) + 1
for ch in s:
    if count[ch] == 1:
        print(ch)
        break
else:
    print('нет')
```

**Dict16.**
```python
words = input().split()
freq = {}
for w in words:
    freq[w] = freq.get(w, 0) + 1
for w in sorted(freq):
    print(w, '-', freq[w])
```

**Dict17.**
```python
words = input().split()
freq = {}
for w in words:
    freq[w] = freq.get(w, 0) + 1
best = min(freq, key=lambda w: (-freq[w], w))
print(best, freq[best])
```

**Dict18.**
```python
from collections import Counter

n = int(input())
votes = Counter(input() for _ in range(n))
for name, k in votes.most_common():
    print(f'{name}: {k}')
```

**Dict19.**
```python
from collections import Counter

text = input().lower()
letters = Counter(ch for ch in text if ch.isalpha())
for ch, k in letters.most_common(3):
    print(ch, k)
```

**Dict20.**
```python
from collections import Counter

a = input().lower()
b = input().lower()
print(Counter(a) == Counter(b))
```

**Dict21.**
```python
keys = {'abc': '2', 'def': '3', 'ghi': '4', 'jkl': '5',
        'mno': '6', 'pqrs': '7', 'tuv': '8', 'wxyz': '9'}
digit = {}
for letters, d in keys.items():
    for ch in letters:
        digit[ch] = d
word = input()
print(''.join(digit[ch] for ch in word))
```

**Dict22.**
```python
value = {'I': 1, 'V': 5, 'X': 10, 'L': 50, 'C': 100, 'D': 500, 'M': 1000}
s = input()
total = 0
for i, ch in enumerate(s):
    if i + 1 < len(s) and value[ch] < value[s[i + 1]]:
        total -= value[ch]
    else:
        total += value[ch]
print(total)
```

**Dict23.**
```python
n = int(input())
vocab = {}
for _ in range(n):
    en, ru = input().split(' - ')
    vocab[en] = ru
sentence = input().split()
print(' '.join(vocab.get(w, w) for w in sentence))
```

**Dict24.**
```python
n = int(input())
prices = {}
for _ in range(n):
    name, price = input().split()
    prices[name] = int(price)
total = 0
not_found = []
for item in input().split():
    if item in prices:
        total += prices[item]
    elif item not in not_found:
        not_found.append(item)
print(total)
if not_found:
    print('Не найдено:', *not_found)
```

**Dict25.**
```python
total = {}
for _ in range(2):
    for pair in input().split():
        name, qty = pair.split(':')
        total[name] = total.get(name, 0) + int(qty)
for name in sorted(total):
    print(f'{name}: {total[name]}')
```

**Dict26.**
```python
n = int(input())
population = {}
for _ in range(n):
    city, people = input().split()
    population[city] = int(people)
top = sorted(population.items(), key=lambda p: p[1], reverse=True)[:3]
for city, people in top:
    print(city, people)
```

**Dict27.**
```python
n = int(input())
score = {}
for _ in range(n):
    name, points = input().split()
    score[name] = int(points)
best = max(score.values())
print(best)
print(*sorted(name for name, p in score.items() if p == best))
```

**Dict28.**
```python
n = int(input())
grades = {}
for _ in range(n):
    name, mark = input().split()
    grades.setdefault(name, []).append(int(mark))
for name in sorted(grades):
    marks = grades[name]
    print(f'{name} {sum(marks) / len(marks):.1f}')
```

**Dict29.**
```python
from collections import defaultdict

groups = defaultdict(list)
for w in input().split():
    groups[w[0]].append(w)
for letter in sorted(groups):
    print(f'{letter}: {", ".join(groups[letter])}')
```

**Dict30.**
```python
from collections import defaultdict

positions = defaultdict(list)
for i, w in enumerate(input().split()):
    positions[w].append(i)
for w, idx in positions.items():
    print(f'{w}:', *idx)
```

**Dict31.**
```python
n = int(input())
common = None
every = set()
for _ in range(n):
    _, langs = input().split(': ')
    langs = set(langs.split())
    every |= langs
    common = langs if common is None else common & langs
print(*sorted(common) if common else ['нет'])
print(len(every))
```

**Dict32.**
```python
from collections import Counter

paper = Counter(input().replace(' ', ''))
note = Counter(input().replace(' ', ''))
lack = note - paper
if not lack:
    print('Да')
else:
    print('Нет')
    print(''.join(ch * k for ch, k in lack.items()))
```

**Dict33.**
```python
n = int(input())
sales = {}
for _ in range(n):
    city, item, amount = input().split()
    city_sales = sales.setdefault(city, {})
    city_sales[item] = city_sales.get(item, 0) + int(amount)
for city in sorted(sales):
    print(city)
    for item in sorted(sales[city]):
        print(f'  {item}: {sales[city][item]}')
```

**Dict34.**
```python
from collections import defaultdict

groups = defaultdict(list)
for w in input().split():
    groups[''.join(sorted(w))].append(w)
for words in groups.values():
    if len(words) > 1:
        print(*words)
```

**Dict35.**
```python
n = int(input())
bank = {}
for _ in range(n):
    cmd = input().split()
    name = cmd[1]
    if cmd[0] == '+':
        bank[name] = bank.get(name, 0) + int(cmd[2])
    elif cmd[0] == '-':
        amount = int(cmd[2])
        if bank.get(name, 0) >= amount:
            bank[name] -= amount
        else:
            print('отказ', name)
    else:
        print(bank.get(name, 0))
for name in sorted(bank):
    print(f'{name}: {bank[name]}')
```

**Dict36.**
```python
a = input()
b = input()
forward = {}
backward = {}
ok = True
for x, y in zip(a, b):
    if forward.get(x, y) != y or backward.get(y, x) != x:
        ok = False
        break
    forward[x] = y
    backward[y] = x
print(ok)
```

**Dict37★.**
```python
nums = list(map(int, input().split()))
s = int(input())
index_of = {}
for j, x in enumerate(nums):
    if s - x in index_of:
        print(index_of[s - x], j)
        break
    index_of.setdefault(x, j)
else:
    print(-1)
```

**Dict38★.**
```python
s = input()
last = {}
start = 0
best_len, best_start = 0, 0
for i, ch in enumerate(s):
    if ch in last and last[ch] >= start:
        start = last[ch] + 1
    last[ch] = i
    if i - start + 1 > best_len:
        best_len, best_start = i - start + 1, start
print(best_len, s[best_start:best_start + best_len])
```

**Dict39★.**
```python
n = int(input())
points = {}
diff = {}
for _ in range(n):
    a, b, ga, gb = input().split()
    ga, gb = int(ga), int(gb)
    for team in a, b:
        points.setdefault(team, 0)
        diff.setdefault(team, 0)
    diff[a] += ga - gb
    diff[b] += gb - ga
    if ga > gb:
        points[a] += 3
    elif ga < gb:
        points[b] += 3
    else:
        points[a] += 1
        points[b] += 1
table = sorted(points, key=lambda t: (-points[t], -diff[t], t))
for team in table:
    print(team, points[team], diff[team])
```

**Dict40★.**
```python
from collections import defaultdict

n = int(input())
friends = defaultdict(set)
for _ in range(n):
    a, b = input().split()
    friends[a].add(b)
    friends[b].add(a)
x = input()
candidates = set()
for f in friends[x]:
    candidates |= friends[f]
candidates -= friends[x] | {x}
print(*sorted(candidates) if candidates else ['нет'])
```

---

## 15. Текстовые файлы

**Text1.**
```python
name = input()
city = input()
age = input()
with open('anketa.txt', 'w', encoding='utf-8') as f:
    f.write(f'Имя: {name}\n')
    f.write(f'Город: {city}\n')
    f.write(f'Возраст: {age}\n')
```

**Text2.**
```python
name = input()
n = int(input())
with open(name, 'w', encoding='utf-8') as f:
    for k in range(1, n + 1):
        print(f'{k} * {k} = {k * k}', file=f)
```

**Text3.**
```python
name = input()
with open(name, encoding='utf-8') as f:
    for i, line in enumerate(f, 1):
        print(f'{i}: {line.rstrip()}')
```

**Text4.**
```python
name = input()
lines = words = chars = 0
with open(name, encoding='utf-8') as f:
    for line in f:
        line = line.rstrip('\n')
        lines += 1
        words += len(line.split())
        chars += len(line)
print(lines, words, chars)
```

**Text5.**
```python
entry = input()
with open('diary.txt', 'a', encoding='utf-8') as f:
    f.write(entry + '\n')
```

**Text6.**
```python
name = input()
best_num, best = 0, ''
with open(name, encoding='utf-8') as f:
    for i, line in enumerate(f, 1):
        line = line.rstrip('\n')
        if len(line) > len(best) or best_num == 0:
            best_num, best = i, line
print(best_num)
print(best)
```

**Text7.**
```python
name = input()
word = input().lower()
found = False
with open(name, encoding='utf-8') as f:
    for i, line in enumerate(f, 1):
        if word in line.lower():
            print(f'{i}: {line.rstrip()}')
            found = True
if not found:
    print('не найдено')
```

**Text8.**
```python
src = input()
dst = input()
with open(src, encoding='utf-8') as fin, open(dst, 'w', encoding='utf-8') as fout:
    for line in fin:
        if line.strip():
            fout.write(line)
```

**Text9.**
```python
name = input()
title = input()
with open(name, encoding='utf-8') as f:
    text = f.read()
with open(name, 'w', encoding='utf-8') as f:
    f.write(title + '\n' + text)
```

**Text10.**
```python
src = input()
dst = input()
with open(src, encoding='utf-8') as f:
    lines = f.readlines()
with open(dst, 'w', encoding='utf-8') as f:
    for line in reversed(lines):
        f.write(line.rstrip('\n') + '\n')
```

**Text11.**
```python
total = 0
count = 0
with open('numbers.txt', encoding='utf-8') as f:
    for line in f:
        total += int(line)
        count += 1
print(total)
print(f'{total / count:.2f}')
```

**Text12.**
```python
name = input()
count = 0
low = high = None
with open(name, encoding='utf-8') as f:
    for line in f:
        for x in map(int, line.split()):
            count += 1
            if low is None or x < low:
                low = x
            if high is None or x > high:
                high = x
print(count, low, high)
```

**Text13.**
```python
with (open('all.txt', encoding='utf-8') as src,
      open('even.txt', 'w', encoding='utf-8') as even,
      open('odd.txt', 'w', encoding='utf-8') as odd):
    for line in src:
        x = int(line)
        print(x, file=even if x % 2 == 0 else odd)
```

**Text14.**
```python
frost = 0
total = 0.0
days = 0
with open('weather.txt', encoding='utf-8') as f:
    for line in f:
        _, temp = line.split()
        temp = float(temp)
        days += 1
        total += temp
        if temp < 0:
            frost += 1
print(frost)
print(f'{total / days:.1f}')
```

**Text15.**
```python
src = input()
dst = input()
with open(src, encoding='utf-8') as f:
    text = f.read()
with open(dst, 'w', encoding='utf-8') as f:
    f.write(text.upper())
```

**Text16.**
```python
name = input()
old = input()
new = input()
with open(name, encoding='utf-8') as f:
    text = f.read()
print(text.count(old))
with open(name, 'w', encoding='utf-8') as f:
    f.write(text.replace(old, new))
```

**Text17.**
```python
name = input()
vowels = 'аеёиоуыэюя'
counts = []
with open(name, encoding='utf-8') as f:
    for line in f:
        counts.append(sum(1 for ch in line.lower() if ch in vowels))
print(*counts)
print(sum(counts))
```

**Text18.**
```python
name = input()
freq = {}
with open(name, encoding='utf-8') as f:
    for line in f:
        for w in line.lower().split():
            w = w.strip('.,!?;:—')
            if w:
                freq[w] = freq.get(w, 0) + 1
best = max(freq, key=freq.get)
print(best, freq[best])
```

**Text19.**
```python
with (open('ru.txt', encoding='utf-8') as ru,
      open('kz.txt', encoding='utf-8') as kz,
      open('dict.txt', 'w', encoding='utf-8') as out):
    for a, b in zip(ru, kz):
        out.write(f'{a.rstrip()} — {b.rstrip()}\n')
```

**Text20.**
```python
name = input()
k = int(input())
with (open(name, encoding='utf-8') as src,
      open('part1.txt', 'w', encoding='utf-8') as p1,
      open('part2.txt', 'w', encoding='utf-8') as p2):
    for i, line in enumerate(src):
        (p1 if i < k else p2).write(line)
```

**Text21.**
```python
n = int(input())
with open('powers.txt', 'w', encoding='utf-8') as f:
    f.write(f'{"x":>6}{"x^2":>6}{"x^3":>6}\n')
    for x in range(1, n + 1):
        f.write(f'{x:>6}{x ** 2:>6}{x ** 3:>6}\n')
```

**Text22.**
```python
with (open('marks.txt', encoding='utf-8') as fin,
      open('report.txt', 'w', encoding='utf-8') as fout):
    for line in fin:
        name, *marks = line.split()
        marks = [int(m) for m in marks]
        avg = sum(marks) / len(marks)
        fout.write(f'{name.ljust(12, ".")}{avg:.1f}\n')
```

**Text23.**
```python
name = input()
removed = 0
with (open(name, encoding='utf-8') as fin,
      open('clean_' + name, 'w', encoding='utf-8') as fout):
    for line in fin:
        if line.lstrip().startswith('#'):
            removed += 1
        else:
            fout.write(line)
print(removed)
```

**Text24.**
```python
src = input()
dst = input()
with open(src, encoding='utf-8') as fin, open(dst, 'w', encoding='utf-8') as fout:
    for i, line in enumerate(fin, 1):
        fout.write(f'{i:>3}| {line}')
```

**Text25.**
```python
src = input()
dst = input()
k = int(input())


def shift(ch):
    if 'a' <= ch <= 'z':
        return chr((ord(ch) - ord('a') + k) % 26 + ord('a'))
    if 'A' <= ch <= 'Z':
        return chr((ord(ch) - ord('A') + k) % 26 + ord('A'))
    return ch


with open(src, encoding='utf-8') as f:
    text = f.read()
with open(dst, 'w', encoding='utf-8') as f:
    f.write(''.join(shift(ch) for ch in text))
```

**Text26.**
```python
name = input()
total = 0
with (open(name, encoding='utf-8') as fin,
      open('words_count.txt', 'w', encoding='utf-8') as fout):
    for line in fin:
        k = len(line.split())
        total += k
        print(k, file=fout)
    print(f'Итого: {total}', file=fout)
```

**Text27.**
```python
name = input()
found = False
with open(name, encoding='utf-8') as f:
    for line in f:
        line = line.rstrip('\n')
        s = line.replace(' ', '').lower()
        if s and s == s[::-1]:
            print(line)
            found = True
if not found:
    print('нет')
```

**Text28.**
```python
name = input()
k = int(input())
with open(name, encoding='utf-8') as f:
    lines = f.readlines()
if 1 <= k <= len(lines):
    del lines[k - 1]
    with open(name, 'w', encoding='utf-8') as f:
        f.writelines(lines)
else:
    print(f'нет строки {k}')
```

**Text29.**
```python
name = input()
w = int(input())
with (open(name, encoding='utf-8') as fin,
      open('centered.txt', 'w', encoding='utf-8') as fout):
    for line in fin:
        fout.write(line.rstrip('\n').center(w, '*') + '\n')
```

**Text30.**
```python
total = 0
best_item, best_sum = '', -1
with open('cheque.txt', encoding='utf-8') as f:
    for line in f:
        item, qty, price = line.rstrip('\n').split(';')
        cost = int(qty) * int(price)
        total += cost
        if cost > best_sum:
            best_item, best_sum = item, cost
print(total)
print(best_item, best_sum)
```

**Text31.**
```python
name = input()
k = int(input())
words = set()
with open(name, encoding='utf-8') as f:
    for line in f:
        for w in line.lower().split():
            w = w.strip('.,!?')
            if len(w) > k:
                words.add(w)
with open('long_words.txt', 'w', encoding='utf-8') as f:
    for w in sorted(words):
        f.write(w + '\n')
```

**Text32.**
```python
name1 = input()
name2 = input()
with open(name1, encoding='utf-8') as f1, open(name2, encoding='utf-8') as f2:
    lines1 = [s.rstrip('\n') for s in f1]
    lines2 = [s.rstrip('\n') for s in f2]
end = '<конец файла>'
for i in range(max(len(lines1), len(lines2))):
    a = lines1[i] if i < len(lines1) else end
    b = lines2[i] if i < len(lines2) else end
    if a != b:
        print(i + 1)
        print(a)
        print(b)
        break
else:
    print('Файлы совпадают')
```

**Text33.**
```python
name = input()
seen = set()
result = []
removed = 0
with open(name, encoding='utf-8') as f:
    for line in f:
        line = line.rstrip('\n')
        if line in seen:
            removed += 1
        else:
            seen.add(line)
            result.append(line)
with open(name, 'w', encoding='utf-8') as f:
    for line in result:
        f.write(line + '\n')
print(removed)
```

**Text34.**
```python
sums = []
with open('table.txt', encoding='utf-8') as f:
    for line in f:
        sums.append(sum(map(int, line.split())))
best = sums.index(max(sums)) + 1
with open('sums.txt', 'w', encoding='utf-8') as f:
    for s in sums:
        print(s, file=f)
    print(best, file=f)
```

**Text35.**
```python
per_hour = {}
with open('log.txt', encoding='utf-8') as f:
    for line in f:
        hour = line[:2]
        per_hour[hour] = per_hour.get(hour, 0) + 1
for hour in sorted(per_hour):
    print(f'{hour}:00 - {per_hour[hour]}')
```

**Text36.**
```python
name = input()
pattern = input()
count = 0
with open(name, encoding='utf-8') as f:
    for line in f:
        start = line.find(pattern)
        while start != -1:
            count += 1
            start = line.find(pattern, start + 1)
print(count)
```

**Text37★.**
```python
name = input()
w = int(input())
lines = []
current = ''
with open(name, encoding='utf-8') as f:
    for line in f:
        for word in line.split():
            if not current:
                current = word
            elif len(current) + 1 + len(word) <= w:
                current += ' ' + word
            else:
                lines.append(current)
                current = word
if current:
    lines.append(current)
with open('wrapped.txt', 'w', encoding='utf-8') as f:
    for line in lines:
        f.write(line + '\n')
```

**Text38★.**
```python
import re

name = input()
with open(name, encoding='utf-8') as f:
    numbers = [int(x) for x in re.findall(r'-?\d+', f.read())]
print(*numbers)
print(sum(numbers))
```

**Text39★.**
```python
with open('matrix.txt', encoding='utf-8') as f:
    n, m = map(int, f.readline().split())
    matrix = [list(map(int, f.readline().split())) for _ in range(n)]
with open('transposed.txt', 'w', encoding='utf-8') as f:
    for j in range(m):
        f.write(''.join(f'{matrix[i][j]:4}' for i in range(n)) + '\n')
```

**Text40★.**
```python
def read_num(f):
    line = f.readline()
    return int(line) if line else None


with (open('a.txt', encoding='utf-8') as fa,
      open('b.txt', encoding='utf-8') as fb,
      open('merged.txt', 'w', encoding='utf-8') as out):
    x = read_num(fa)
    y = read_num(fb)
    while x is not None or y is not None:
        if y is None or (x is not None and x <= y):
            print(x, file=out)
            x = read_num(fa)
        else:
            print(y, file=out)
            y = read_num(fb)
```

---

## 16. Форматы данных: CSV, JSON, двоичные файлы

**Data1.**
```python
import csv

with open('students.csv', encoding='utf-8', newline='') as f:
    reader = csv.reader(f)
    next(reader)
    for name, grade, score in reader:
        print(f'{name} ({grade}): {score}')
```

**Data2.**
```python
import csv

with open('students.csv', encoding='utf-8', newline='') as f:
    rows = list(csv.DictReader(f))
scores = [int(row['балл']) for row in rows]
print(f'{sum(scores) / len(scores):.1f}')
best = max(rows, key=lambda row: int(row['балл']))
print(best['имя'])
```

**Data3.**
```python
import csv

n = int(input())
with open('team.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['имя', 'возраст', 'город'])
    for _ in range(n):
        writer.writerow(input().split())
```

**Data4.**
```python
import csv

with open('products.csv', encoding='utf-8', newline='') as fin:
    reader = csv.DictReader(fin)
    cheap = [row for row in reader if int(row['цена']) < 1000]
    fields = reader.fieldnames
with open('cheap.csv', 'w', encoding='utf-8', newline='') as fout:
    writer = csv.DictWriter(fout, fieldnames=fields)
    writer.writeheader()
    writer.writerows(cheap)
print(len(cheap))
```

**Data5.**
```python
import csv

count = {}
with open('shops.csv', encoding='utf-8', newline='') as f:
    reader = csv.reader(f, delimiter=';')
    next(reader)
    for shop, city, address in reader:
        count[city] = count.get(city, 0) + 1
for city in sorted(count):
    print(city, count[city])
print(address)
```

**Data6.**
```python
import csv

with (open('grades.csv', encoding='utf-8', newline='') as fin,
      open('grades_avg.csv', 'w', encoding='utf-8', newline='') as fout):
    reader = csv.reader(fin)
    writer = csv.writer(fout)
    writer.writerow(next(reader) + ['среднее'])
    for name, *marks in reader:
        avg = sum(map(int, marks)) / len(marks)
        writer.writerow([name, *marks, f'{avg:.2f}'])
```

**Data7.**
```python
import csv


def seconds(t):
    h, m, s = map(int, t.split(':'))
    return h * 3600 + m * 60 + s


with open('marathon.csv', encoding='utf-8', newline='') as f:
    rows = list(csv.DictReader(f))
rows.sort(key=lambda row: seconds(row['время']))
for place, row in enumerate(rows[:3], 1):
    print(f"{place}. {row['участник']} {row['время']}")
```

**Data8.**
```python
import csv

total = {}
with open('sales.csv', encoding='utf-8', newline='') as f:
    for row in csv.DictReader(f):
        name = row['менеджер']
        total[name] = total.get(name, 0) + int(row['сумма'])
for name, s in sorted(total.items(), key=lambda p: p[1], reverse=True):
    print(f'{name}: {s}')
print(f'Всего: {sum(total.values())}')
```

**Data9.**
```python
import json

with open('config.json', encoding='utf-8') as f:
    config = json.load(f)
mode = 'да' if config['fullscreen'] else 'нет'
print(f'Окно "{config["title"]}": {config["width"]}x{config["height"]}, полный экран: {mode}')
```

**Data10.**
```python
import json

profile = {
    'name': input(),
    'age': int(input()),
    'hobbies': [h.strip() for h in input().split(',')],
}
with open('profile.json', 'w', encoding='utf-8') as f:
    json.dump(profile, f, ensure_ascii=False, indent=2)
```

**Data11.**
```python
import json

x = int(input())
with open('club.json', encoding='utf-8') as f:
    members = json.load(f)
count = 0
for m in members:
    if m['age'] >= x:
        print(f"{m['name']}, {m['city']}")
        count += 1
print(count)
```

**Data12.**
```python
import json

with open('todo.json', encoding='utf-8') as f:
    todo = json.load(f)
todo.append({'task': input(), 'done': False})
with open('todo.json', 'w', encoding='utf-8') as f:
    json.dump(todo, f, ensure_ascii=False, indent=2)
print(sum(1 for item in todo if not item['done']))
```

**Data13.**
```python
import csv
import json

with open('students.csv', encoding='utf-8', newline='') as f:
    rows = list(csv.DictReader(f))
for row in rows:
    row['балл'] = int(row['балл'])
with open('students.json', 'w', encoding='utf-8') as f:
    json.dump(rows, f, ensure_ascii=False, indent=1)
```

**Data14.**
```python
import json

with open('school.json', encoding='utf-8') as f:
    school = json.load(f)
print(school['name'])
total = 0
for grade in sorted(school['classes']):
    k = len(school['classes'][grade])
    total += k
    print(f'{grade}: {k}')
print(total)
```

**Data15.**
```python
import json

n = int(input())
data = {}
for _ in range(n):
    key, value = input().split()
    data[key] = int(value) if value.isdigit() else value
print(json.dumps(data, ensure_ascii=False, sort_keys=True))
```

**Data16.**
```python
import json

name = input()
freq = {}
with open(name, encoding='utf-8') as f:
    for line in f:
        for w in line.lower().split():
            w = w.strip('.,!?')
            if w:
                freq[w] = freq.get(w, 0) + 1
ordered = dict(sorted(freq.items(), key=lambda p: (-p[1], p[0])))
with open('freq.json', 'w', encoding='utf-8') as f:
    json.dump(ordered, f, ensure_ascii=False, indent=2)
```

**Data17.**
```python
from pathlib import PurePosixPath

p = PurePosixPath(input())
print(p.name)
print(p.stem)
print(p.suffix)
print(p.parent)
print(p.with_suffix('.txt'))
```

**Data18.**
```python
from pathlib import Path

n = int(input())
for _ in range(n):
    p = Path(input())
    if p.is_file():
        print('файл')
    elif p.is_dir():
        print('папка')
    else:
        print('нет')
```

**Data19.**
```python
from pathlib import Path

count = {}
for p in Path('photos').iterdir():
    if p.is_file():
        ext = p.suffix.lower().lstrip('.')
        count[ext] = count.get(ext, 0) + 1
for ext in sorted(count):
    print(f'{ext}: {count[ext]}')
```

**Data20.**
```python
from pathlib import Path

total = 0
for p in sorted(Path('notes').glob('*.txt')):
    k = len(p.read_text(encoding='utf-8').splitlines())
    total += k
    print(f'{p.name}: {k}')
print(total)
```

**Data21.**
```python
from pathlib import Path

backup = Path('backup')
backup.mkdir(exist_ok=True)
copied = []
for p in sorted(Path('docs').glob('*.txt')):
    (backup / p.name).write_bytes(p.read_bytes())
    copied.append(p.name)
print(*copied, sep='\n')
print(len(copied))
```

**Data22.**
```python
name = input()
with open(name, 'rb') as f:
    data = f.read()
print(len(data))
print(len(data.decode('utf-8')))
```

**Data23.**
```python
name = input()
with open(name, 'rb') as f:
    head = f.read(4)
print(head.hex(' '))
if head == b'\x89PNG':
    print('PNG')
elif head == b'%PDF':
    print('PDF')
elif head == b'PK\x03\x04':
    print('ZIP')
else:
    print('неизвестно')
```

**Data24.**
```python
import struct

nums = list(map(int, input().split()))
with open('nums.bin', 'wb') as f:
    for x in nums:
        f.write(struct.pack('<i', x))
print(len(nums) * struct.calcsize('<i'))
```

**Data25.**
```python
import struct

with open('data.bin', 'rb') as f:
    data = f.read()
n = len(data) // struct.calcsize('<i')
nums = struct.unpack(f'<{n}i', data)
print(*nums)
print(sum(nums), max(nums))
```

**Data26.**
```python
import pickle
from pathlib import Path

path = Path('save.pkl')
if path.exists():
    with open(path, 'rb') as f:
        save = pickle.load(f)
    print(f"С возвращением, {save['player']}! Уровень {save['level']}")
else:
    save = {'player': input(), 'level': 1, 'items': []}
    print('Новая игра')
save['items'].append(input())
save['level'] += 1
with open(path, 'wb') as f:
    pickle.dump(save, f)
print(', '.join(save['items']))
```

**Data27★.**
```python
import struct

with open('results.bin', 'rb') as f:
    data = f.read()
records = list(struct.iter_unpack('<Hf', data))
for num, score in records:
    print(f'{num}: {score:.2f}')
scores = [score for _, score in records]
print(f'{sum(scores) / len(scores):.2f}')
best = max(scores)
print(*sorted(num for num, score in records if score == best))
```

**Data28★.**
```python
src = input()
dst = input()
key = input().encode('utf-8')
with open(src, 'rb') as f:
    data = f.read()
result = bytes(b ^ key[i % len(key)] for i, b in enumerate(data))
with open(dst, 'wb') as f:
    f.write(result)
print(result[:4].hex(' '))
```

**Data29★.**
```python
import csv
import json

with open('pupils.csv', encoding='utf-8', newline='') as f:
    names = {row['id']: row['имя'] for row in csv.DictReader(f)}
report = {name: {} for name in names.values()}
skipped = 0
with open('scores.csv', encoding='utf-8', newline='') as f:
    for row in csv.DictReader(f):
        if row['id'] in names:
            report[names[row['id']]][row['предмет']] = int(row['балл'])
        else:
            skipped += 1
with open('report.json', 'w', encoding='utf-8') as f:
    json.dump(report, f, ensure_ascii=False, indent=2)
print(skipped)
```

**Data30★.**
```python
from pathlib import Path

stats = {}
for p in Path('project').rglob('*'):
    if p.is_file():
        ext = p.suffix or '(нет)'
        count, size = stats.get(ext, (0, 0))
        stats[ext] = (count + 1, size + p.stat().st_size)
for ext, (count, size) in sorted(stats.items(), key=lambda e: (-e[1][1], e[0])):
    print(ext, count, size)
print(sum(size for _, size in stats.values()))
```

---

## 17. Рекурсия

**Recur1.**
```python
def countdown(n):
    if n == 0:
        print("Пуск!")
        return
    print(n)
    countdown(n - 1)
```

**Recur2.**
```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)
```

**Recur3.**
```python
def power(a, n):
    if n == 0:
        return 1
    return a * power(a, n - 1)
```

**Recur4.**
```python
def digits_sum(n):
    if n < 10:
        return n
    return n % 10 + digits_sum(n // 10)
```

**Recur5.**
```python
def digits_count(n):
    if n < 10:
        return 1
    return 1 + digits_count(n // 10)
```

**Recur6.**
```python
def gcd(a, b):
    if b == 0:
        return a
    return gcd(b, a % b)
```

**Recur7.**
```python
def reverse(s):
    if len(s) <= 1:
        return s
    return reverse(s[1:]) + s[0]
```

**Recur8.**
```python
def check(s):
    if len(s) <= 1:
        return True
    if s[0] != s[-1]:
        return False
    return check(s[1:-1])


def is_palindrome(s):
    return check(s.replace(" ", "").lower())
```

**Recur9.**
```python
def list_sum(nums):
    if not nums:
        return 0
    return nums[0] + list_sum(nums[1:])
```

**Recur10.**
```python
def list_max(nums):
    if len(nums) == 1:
        return nums[0]
    rest = list_max(nums[1:])
    return nums[0] if nums[0] > rest else rest
```

**Recur11.**
```python
def to_binary(n):
    if n < 2:
        return str(n)
    return to_binary(n // 2) + str(n % 2)
```

**Recur12.**
```python
from functools import lru_cache


@lru_cache(maxsize=None)
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)
```

**Recur13.**
```python
def fib_calls(n):
    calls = 0

    def fib(k):
        nonlocal calls
        calls += 1
        if k < 2:
            return k
        return fib(k - 1) + fib(k - 2)

    return fib(n), calls
```

**Recur14.**
```python
from functools import lru_cache


@lru_cache(maxsize=None)
def stairs(n):
    if n < 0:
        return 0
    if n == 0:
        return 1
    return stairs(n - 1) + stairs(n - 2) + stairs(n - 3)
```

**Recur15.**
```python
from functools import lru_cache


@lru_cache(maxsize=None)
def binom(n, k):
    if k == 0 or k == n:
        return 1
    return binom(n - 1, k - 1) + binom(n - 1, k)
```

**Recur16.**
```python
def fast_power(a, n):
    if n == 0:
        return 1
    if n % 2 == 0:
        half = fast_power(a, n // 2)
        return half * half
    return a * fast_power(a, n - 1)
```

**Recur17.**
```python
def flatten(data):
    result = []
    for item in data:
        if isinstance(item, list):
            result.extend(flatten(item))
        else:
            result.append(item)
    return result
```

**Recur18.**
```python
def depth(data):
    inner = [depth(x) for x in data if isinstance(x, list)]
    return 1 + max(inner, default=0)
```

**Recur19.**
```python
def binary_search(nums, x, lo=0, hi=None):
    if hi is None:
        hi = len(nums) - 1
    if lo > hi:
        return -1
    mid = (lo + hi) // 2
    if nums[mid] == x:
        return mid
    if nums[mid] < x:
        return binary_search(nums, x, mid + 1, hi)
    return binary_search(nums, x, lo, mid - 1)
```

**Recur20.**
```python
def hanoi(n, src="A", dst="C", tmp="B"):
    if n == 0:
        return 0
    moves = hanoi(n - 1, src, tmp, dst)
    print(f"Диск {n}: {src} -> {dst}")
    moves += 1
    moves += hanoi(n - 1, tmp, dst, src)
    return moves
```

**Recur21.**
```python
def subsets(items):
    if not items:
        return [[]]
    rest = subsets(items[1:])
    return rest + [[items[0]] + s for s in rest]
```

**Recur22.**
```python
def permutations(s):
    if len(s) <= 1:
        return [s]
    result = []
    for i, ch in enumerate(s):
        for rest in permutations(s[:i] + s[i + 1:]):
            result.append(ch + rest)
    return result
```

**Recur23.**
```python
def no_double_ones(n, prefix=""):
    if len(prefix) == n:
        return [prefix]
    result = no_double_ones(n, prefix + "0")
    if not prefix.endswith("1"):
        result += no_double_ones(n, prefix + "1")
    return result
```

**Recur24.**
```python
from functools import lru_cache


@lru_cache(maxsize=None)
def change_ways(amount, coins):
    if amount == 0:
        return 1
    if amount < 0 or not coins:
        return 0
    return (change_ways(amount, coins[1:])
            + change_ways(amount - coins[0], coins))
```

**Recur25.**
```python
def merge(a, b):
    result = []
    i = j = 0
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            result.append(a[i])
            i += 1
        else:
            result.append(b[j])
            j += 1
    return result + a[i:] + b[j:]


def merge_sort(nums):
    if len(nums) <= 1:
        return nums[:]
    mid = len(nums) // 2
    return merge(merge_sort(nums[:mid]), merge_sort(nums[mid:]))
```

**Recur26.**
```python
def parts(n, limit):
    if n == 0:
        return [[]]
    result = []
    for first in range(min(n, limit), 0, -1):
        for rest in parts(n - first, first):
            result.append([first] + rest)
    return result


def partitions(n):
    return ["+".join(map(str, p)) for p in parts(n, n)]
```

**Recur27★.**
```python
def calc(expr):
    pos = 0

    def expression():
        nonlocal pos
        value = term()
        while pos < len(expr) and expr[pos] in "+-":
            op = expr[pos]
            pos += 1
            if op == "+":
                value += term()
            else:
                value -= term()
        return value

    def term():
        nonlocal pos
        value = factor()
        while pos < len(expr) and expr[pos] == "*":
            pos += 1
            value *= factor()
        return value

    def factor():
        nonlocal pos
        if expr[pos] == "(":
            pos += 1
            value = expression()
            pos += 1  # пропускаем ")"
            return value
        start = pos
        while pos < len(expr) and expr[pos].isdigit():
            pos += 1
        return int(expr[start:pos])

    return expression()
```

**Recur28★.**
```python
def queens(n):
    cols, diag1, diag2 = set(), set(), set()

    def place(row):
        if row == n:
            return 1
        count = 0
        for col in range(n):
            if col in cols or row - col in diag1 or row + col in diag2:
                continue
            cols.add(col)
            diag1.add(row - col)
            diag2.add(row + col)
            count += place(row + 1)
            cols.remove(col)
            diag1.remove(row - col)
            diag2.remove(row + col)
        return count

    return place(0)
```

**Recur29★.**
```python
def count_lakes(grid):
    rows, cols = len(grid), len(grid[0])
    seen = set()

    def fill(r, c):
        if not (0 <= r < rows and 0 <= c < cols):
            return
        if grid[r][c] != "#" or (r, c) in seen:
            return
        seen.add((r, c))
        fill(r + 1, c)
        fill(r - 1, c)
        fill(r, c + 1)
        fill(r, c - 1)

    lakes = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == "#" and (r, c) not in seen:
                lakes += 1
                fill(r, c)
    return lakes
```

**Recur30★.**
```python
def folder_size(folder):
    total = 0
    for item in folder.values():
        total += folder_size(item) if isinstance(item, dict) else item
    return total


def show_tree(folder, name, indent=0):
    size = folder_size(folder)
    print(" " * indent + f"{name}/ ({size} KB)")
    for key in sorted(folder):
        item = folder[key]
        if isinstance(item, dict):
            show_tree(item, key, indent + 2)
        else:
            print(" " * (indent + 2) + f"{key} ({item} KB)")
    return size
```

---

## 18. Классы и объекты

**OOP1.**
```python
class Student:
    def __init__(self, name, grade):
        self.name = name
        self.grade = grade

    def introduce(self):
        return (f"Привет! Меня зовут {self.name}, "
                f"я учусь в {self.grade} классе.")
```

**OOP2.**
```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

    def is_square(self):
        return self.width == self.height
```

**OOP3.**
```python
class ClickCounter:
    def __init__(self):
        self.value = 0

    def click(self):
        self.value += 1
        return self

    def reset(self):
        self.value = 0
```

**OOP4.**
```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        if amount > self.balance:
            return False
        self.balance -= amount
        return True
```

**OOP5.**
```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def distance_to(self, other):
        return ((self.x - other.x) ** 2 + (self.y - other.y) ** 2) ** 0.5

    def __str__(self):
        return f"({self.x}, {self.y})"
```

**OOP6.**
```python
class Clock:
    def __init__(self, hours, minutes):
        self.total = (hours * 60 + minutes) % (24 * 60)

    def add_minutes(self, m):
        self.total = (self.total + m) % (24 * 60)

    def __str__(self):
        return f"{self.total // 60:02}:{self.total % 60:02}"
```

**OOP7.**
```python
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price  # срабатывает сеттер

    @property
    def price(self):
        return self._price

    @price.setter
    def price(self, value):
        if value < 0:
            raise ValueError("Цена не может быть отрицательной")
        self._price = value
```

**OOP8.**
```python
import math


class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value <= 0:
            raise ValueError("Радиус должен быть положительным")
        self._radius = value

    @property
    def area(self):
        return round(math.pi * self._radius ** 2, 2)

    @property
    def length(self):
        return round(2 * math.pi * self._radius, 2)
```

**OOP9.**
```python
class Wallet:
    def __init__(self):
        self.__balance = 0
        self.__history = []

    def top_up(self, amount):
        self.__balance += amount
        self.__history.append(f"+{amount}")

    def pay(self, amount, shop):
        if amount > self.__balance:
            raise ValueError("Недостаточно средств")
        self.__balance -= amount
        self.__history.append(f"-{amount} {shop}")

    def balance(self):
        return self.__balance

    def history(self):
        return self.__history.copy()
```

**OOP10.**
```python
from math import gcd


class Fraction:
    def __init__(self, num, den=1):
        if den == 0:
            raise ZeroDivisionError("Знаменатель равен нулю")
        if den < 0:
            num, den = -num, -den
        g = gcd(num, den)
        self.num = num // g
        self.den = den // g

    def __str__(self):
        if self.den == 1:
            return str(self.num)
        return f"{self.num}/{self.den}"
```

**OOP11.**
```python
from math import gcd


class Fraction:
    def __init__(self, num, den=1):
        if den == 0:
            raise ZeroDivisionError("Знаменатель равен нулю")
        if den < 0:
            num, den = -num, -den
        g = gcd(num, den)
        self.num = num // g
        self.den = den // g

    def __str__(self):
        if self.den == 1:
            return str(self.num)
        return f"{self.num}/{self.den}"

    def __repr__(self):
        return f"Fraction({self.num}, {self.den})"

    def __add__(self, other):
        return Fraction(self.num * other.den + other.num * self.den,
                        self.den * other.den)

    def __sub__(self, other):
        return Fraction(self.num * other.den - other.num * self.den,
                        self.den * other.den)

    def __mul__(self, other):
        return Fraction(self.num * other.num, self.den * other.den)

    def __eq__(self, other):
        return self.num == other.num and self.den == other.den
```

**OOP12.**
```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other):
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, k):
        return Vector(self.x * k, self.y * k)

    def __rmul__(self, k):
        return self * k

    def __abs__(self):
        return (self.x ** 2 + self.y ** 2) ** 0.5

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"
```

**OOP13.**
```python
class Money:
    def __init__(self, tenge, tiyn=0):
        self.total = tenge * 100 + tiyn

    def __add__(self, other):
        return Money(0, self.total + other.total)

    def __eq__(self, other):
        return self.total == other.total

    def __lt__(self, other):
        return self.total < other.total

    def __str__(self):
        return f"{self.total // 100} тг {self.total % 100:02} тиын"
```

**OOP14.**
```python
class Runner:
    def __init__(self, name, seconds):
        self.name = name
        self.seconds = seconds

    def __lt__(self, other):
        return self.seconds < other.seconds

    def __repr__(self):
        return f"{self.name} ({self.seconds} с)"
```

**OOP15.**
```python
class Playlist:
    def __init__(self, title):
        self.title = title
        self.songs = []

    def add(self, song):
        self.songs.append(song)

    def __len__(self):
        return len(self.songs)

    def __contains__(self, song):
        return song in self.songs

    def __iter__(self):
        return iter(self.songs)

    def __getitem__(self, i):
        return self.songs[i]
```

**OOP16.**
```python
class Gradebook:
    def __init__(self):
        self._marks = {}

    def __setitem__(self, subject, mark):
        self._marks.setdefault(subject, []).append(mark)

    def __getitem__(self, subject):
        return self._marks.get(subject, [])

    def average(self, subject):
        marks = self[subject]
        return round(sum(marks) / len(marks), 2)
```

**OOP17.**
```python
class Countdown:
    def __init__(self, n):
        self.current = n

    def __iter__(self):
        return self

    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        value = self.current
        self.current -= 1
        return value
```

**OOP18.**
```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "..."

    def introduce(self):
        return f"{self.name} говорит: {self.speak()}"


class Dog(Animal):
    def speak(self):
        return "Гав!"


class Cat(Animal):
    def speak(self):
        return "Мяу!"


class Cow(Animal):
    def speak(self):
        return "Му!"
```

**OOP19.**
```python
import math


class Shape:
    def area(self):
        raise NotImplementedError

    def describe(self):
        return f"{type(self).__name__}: площадь {round(self.area(), 2)}"


class Circle(Shape):
    def __init__(self, r):
        self.r = r

    def area(self):
        return math.pi * self.r ** 2


class Rectangle(Shape):
    def __init__(self, a, b):
        self.a = a
        self.b = b

    def area(self):
        return self.a * self.b


class Square(Rectangle):
    def __init__(self, a):
        super().__init__(a, a)
```

**OOP20.**
```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def total_salary(self):
        return self.salary

    def info(self):
        return f"{self.name}: {self.total_salary()} тг"


class Manager(Employee):
    def __init__(self, name, salary, team=None):
        super().__init__(name, salary)
        self.team = team if team is not None else []

    def total_salary(self):
        return round(self.salary * (1 + 0.1 * len(self.team)))

    def info(self):
        return super().info() + f", команда: {len(self.team)} чел."
```

**OOP21.**
```python
class Ticket:
    count = 0

    def __init__(self, src, dst):
        Ticket.count += 1
        self.number = Ticket.count
        self.src = src
        self.dst = dst

    def __str__(self):
        return f"Билет №{self.number}: {self.src} — {self.dst}"
```

**OOP22.**
```python
class Person:
    def __init__(self, name, age, city):
        self.name = name
        self.age = age
        self.city = city

    @classmethod
    def from_string(cls, line):
        name, age, city = line.split(";")
        return cls(name, int(age), city)

    @staticmethod
    def is_adult(age):
        return age >= 18

    def __repr__(self):
        return f"Person({self.name!r}, {self.age}, {self.city!r})"
```

**OOP23.**
```python
from dataclasses import dataclass


@dataclass
class Book:
    title: str
    author: str
    year: int
    pages: int = 0

    def is_classic(self):
        return self.year < 1950
```

**OOP24.**
```python
from dataclasses import dataclass


@dataclass(order=True)
class Version:
    major: int
    minor: int
    patch: int

    @classmethod
    def parse(cls, s):
        return cls(*map(int, s.split(".")))

    def __str__(self):
        return f"{self.major}.{self.minor}.{self.patch}"
```

**OOP25.**
```python
from dataclasses import dataclass, field


@dataclass
class Team:
    name: str
    players: list = field(default_factory=list)

    def add(self, player):
        self.players.append(player)

    def remove(self, player):
        if player in self.players:
            self.players.remove(player)

    @property
    def size(self):
        return len(self.players)
```

**OOP26.**
```python
class Card:
    def __init__(self, rank, suit):
        self.rank = rank
        self.suit = suit

    def __repr__(self):
        return f"{self.rank}{self.suit}"

    def __eq__(self, other):
        return (self.rank, self.suit) == (other.rank, other.suit)

    def __hash__(self):
        return hash((self.rank, self.suit))
```

**OOP27★.**
```python
class Polynomial:
    def __init__(self, coeffs):
        coeffs = list(coeffs)
        while len(coeffs) > 1 and coeffs[-1] == 0:
            coeffs.pop()
        self.coeffs = coeffs or [0]

    def degree(self):
        return len(self.coeffs) - 1

    def __call__(self, x):
        result = 0
        for c in reversed(self.coeffs):
            result = result * x + c
        return result

    def __add__(self, other):
        n = max(len(self.coeffs), len(other.coeffs))
        a = self.coeffs + [0] * (n - len(self.coeffs))
        b = other.coeffs + [0] * (n - len(other.coeffs))
        return Polynomial([x + y for x, y in zip(a, b)])

    def __str__(self):
        terms = []
        for power in range(self.degree(), -1, -1):
            c = self.coeffs[power]
            if c == 0:
                continue
            sign = "-" if c < 0 else "+"
            c = abs(c)
            if power == 0:
                body = str(c)
            else:
                body = ("" if c == 1 else str(c)) + "x"
                if power > 1:
                    body += f"^{power}"
            terms.append((sign, body))
        if not terms:
            return "0"
        first_sign, first_body = terms[0]
        text = ("-" if first_sign == "-" else "") + first_body
        for sign, body in terms[1:]:
            text += f" {sign} {body}"
        return text
```

**OOP28★.**
```python
import math
from abc import ABC, abstractmethod


class Transport(ABC):
    name = "транспорт"

    @abstractmethod
    def cost(self, km):
        ...

    def __str__(self):
        return self.name


class Bus(Transport):
    name = "автобус"

    def cost(self, km):
        return 200 if km <= 30 else None


class Taxi(Transport):
    name = "такси"

    def cost(self, km):
        return 500 + 150 * math.ceil(km)


class Bike(Transport):
    name = "велосипед"

    def cost(self, km):
        return 300 * math.ceil(km / 5)


def cheapest(options, km):
    available = [t for t in options if t.cost(km) is not None]
    return min(available, key=lambda t: t.cost(km))
```

**OOP29★.**
```python
class InsufficientFunds(Exception):
    def __init__(self, need):
        super().__init__(f"не хватает {need} тг")
        self.need = need


class Bank:
    def __init__(self):
        self._accounts = {}

    def open(self, name, amount=0):
        if name in self._accounts:
            raise ValueError(f"Счёт {name} уже существует")
        self._accounts[name] = amount

    def balance(self, name):
        return self._accounts[name]

    def transfer(self, src, dst, amount):
        if src not in self._accounts or dst not in self._accounts:
            raise KeyError("Нет такого счёта")
        if self._accounts[src] < amount:
            raise InsufficientFunds(amount - self._accounts[src])
        self._accounts[src] -= amount
        self._accounts[dst] += amount

    def __len__(self):
        return len(self._accounts)
```

**OOP30★.**
```python
class Matrix:
    def __init__(self, rows):
        self.rows = [list(r) for r in rows]
        self.n = len(rows)

    @classmethod
    def identity(cls, n):
        return cls([[int(i == j) for j in range(n)] for i in range(n)])

    def __mul__(self, other):
        n = self.n
        return Matrix([[sum(self.rows[i][k] * other.rows[k][j]
                            for k in range(n))
                        for j in range(n)] for i in range(n)])

    def __pow__(self, p):
        if p == 0:
            return Matrix.identity(self.n)
        if p % 2 == 0:
            half = self ** (p // 2)
            return half * half
        return self * self ** (p - 1)

    def __eq__(self, other):
        return self.rows == other.rows

    def __str__(self):
        return "\n".join(" ".join(map(str, r)) for r in self.rows)
```

---

## 19. Структуры данных

**Struct1.**
```python
class Stack:
    def __init__(self):
        self._items = []

    def push(self, x):
        self._items.append(x)

    def pop(self):
        if not self._items:
            raise IndexError("Стек пуст")
        return self._items.pop()

    def peek(self):
        if not self._items:
            raise IndexError("Стек пуст")
        return self._items[-1]

    def is_empty(self):
        return not self._items

    def __len__(self):
        return len(self._items)
```

**Struct2.**
```python
def reverse_words(text):
    stack = []
    for word in text.split():
        stack.append(word)
    result = []
    while stack:
        result.append(stack.pop())
    return " ".join(result)
```

**Struct3.**
```python
def is_balanced(s):
    pairs = {")": "(", "]": "[", "}": "{"}
    stack = []
    for ch in s:
        if ch in "([{":
            stack.append(ch)
        elif ch in pairs:
            if not stack or stack.pop() != pairs[ch]:
                return False
    return not stack
```

**Struct4.**
```python
def eval_rpn(expr):
    stack = []
    for token in expr.split():
        if token in "+-*/":
            b = stack.pop()
            a = stack.pop()
            if token == "+":
                stack.append(a + b)
            elif token == "-":
                stack.append(a - b)
            elif token == "*":
                stack.append(a * b)
            else:
                stack.append(a // b)
        else:
            stack.append(int(token))
    return stack.pop()
```

**Struct5.**
```python
def editor(commands):
    text = ""
    history = []
    for cmd in commands:
        if cmd.startswith("type "):
            history.append(text)
            word = cmd[5:]
            text = f"{text} {word}" if text else word
        elif cmd == "undo" and history:
            text = history.pop()
    return text
```

**Struct6.**
```python
def next_warmer(temps):
    answer = [0] * len(temps)
    stack = []  # индексы дней, для которых ответ ещё не найден
    for i, t in enumerate(temps):
        while stack and temps[stack[-1]] < t:
            j = stack.pop()
            answer[j] = i - j
        stack.append(i)
    return answer
```

**Struct7.**
```python
class MinStack:
    def __init__(self):
        self._items = []
        self._mins = []

    def push(self, x):
        self._items.append(x)
        current = x if not self._mins else min(x, self._mins[-1])
        self._mins.append(current)

    def pop(self):
        self._mins.pop()
        return self._items.pop()

    def top(self):
        return self._items[-1]

    def get_min(self):
        return self._mins[-1]
```

**Struct8.**
```python
from collections import deque


class Queue:
    def __init__(self):
        self._items = deque()

    def enqueue(self, x):
        self._items.append(x)

    def dequeue(self):
        if not self._items:
            raise IndexError("Очередь пуста")
        return self._items.popleft()

    def front(self):
        return self._items[0]

    def __len__(self):
        return len(self._items)

    def __bool__(self):
        return bool(self._items)
```

**Struct9.**
```python
from collections import deque


def is_palindrome(s):
    d = deque(ch.lower() for ch in s if ch.isalpha())
    while len(d) > 1:
        if d.popleft() != d.pop():
            return False
    return True
```

**Struct10.**
```python
from collections import deque


class ChatWindow:
    def __init__(self, n):
        self._messages = deque(maxlen=n)

    def post(self, author, text):
        self._messages.append(f"{author}: {text}")

    def show(self):
        return list(self._messages)
```

**Struct11.**
```python
from collections import deque


def moving_average(values, k):
    window = deque()
    total = 0
    result = []
    for v in values:
        window.append(v)
        total += v
        if len(window) > k:
            total -= window.popleft()
        if len(window) == k:
            result.append(round(total / k, 1))
    return result
```

**Struct12.**
```python
from collections import deque


def counting_out(n, k):
    circle = deque(range(1, n + 1))
    order = []
    while circle:
        circle.rotate(-(k - 1))
        order.append(circle.popleft())
    return order
```

**Struct13.**
```python
from collections import deque


def waiting_times(customers):
    queue = deque(customers)
    free_at = 0  # момент, когда касса освободится
    result = []
    while queue:
        arrive, service = queue.popleft()
        start = max(arrive, free_at)
        result.append(start - arrive)
        free_at = start + service
    return result
```

**Struct14.**
```python
from collections import deque


def round_robin(tasks, q):
    queue = deque(tasks)
    clock = 0
    finished = []
    while queue:
        name, left = queue.popleft()
        step = min(q, left)
        clock += step
        if left > step:
            queue.append((name, left - step))
        else:
            finished.append((name, clock))
    return finished
```

**Struct15.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


def build(values):
    head = None
    for v in reversed(values):
        head = Node(v, head)
    return head


def to_list(head):
    result = []
    while head is not None:
        result.append(head.value)
        head = head.next
    return result
```

**Struct16.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


def build(values):
    head = None
    for v in reversed(values):
        head = Node(v, head)
    return head


def to_list(head):
    result = []
    while head is not None:
        result.append(head.value)
        head = head.next
    return result


def length(head):
    count = 0
    while head is not None:
        count += 1
        head = head.next
    return count


def total(head):
    s = 0
    while head is not None:
        s += head.value
        head = head.next
    return s


def get(head, index):
    i = 0
    while head is not None:
        if i == index:
            return head.value
        head = head.next
        i += 1
    raise IndexError("Нет узла с таким индексом")
```

**Struct17.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


class LinkedList:
    def __init__(self):
        self.head = None
        self.size = 0

    def push_front(self, x):
        self.head = Node(x, self.head)
        self.size += 1

    def append(self, x):
        node = Node(x)
        if self.head is None:
            self.head = node
        else:
            cur = self.head
            while cur.next is not None:
                cur = cur.next
            cur.next = node
        self.size += 1

    def __len__(self):
        return self.size

    def __iter__(self):
        cur = self.head
        while cur is not None:
            yield cur.value
            cur = cur.next

    def __str__(self):
        if self.head is None:
            return "пусто"
        return " -> ".join(str(v) for v in self)
```

**Struct18.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


def build(values):
    head = None
    for v in reversed(values):
        head = Node(v, head)
    return head


def to_list(head):
    result = []
    while head is not None:
        result.append(head.value)
        head = head.next
    return result


def index_of(head, x):
    i = 0
    while head is not None:
        if head.value == x:
            return i
        head = head.next
        i += 1
    return -1


def remove(head, x):
    if head is None:
        return None
    if head.value == x:
        return head.next
    cur = head
    while cur.next is not None:
        if cur.next.value == x:
            cur.next = cur.next.next
            break
        cur = cur.next
    return head
```

**Struct19.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


def build(values):
    head = None
    for v in reversed(values):
        head = Node(v, head)
    return head


def to_list(head):
    result = []
    while head is not None:
        result.append(head.value)
        head = head.next
    return result


def insert_at(head, index, x):
    if index == 0:
        return Node(x, head)
    cur = head
    for _ in range(index - 1):
        if cur is None:
            raise IndexError("Индекс вне списка")
        cur = cur.next
    if cur is None:
        raise IndexError("Индекс вне списка")
    cur.next = Node(x, cur.next)
    return head
```

**Struct20.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


def build(values):
    head = None
    for v in reversed(values):
        head = Node(v, head)
    return head


def to_list(head):
    result = []
    while head is not None:
        result.append(head.value)
        head = head.next
    return result


def reverse_list(head):
    prev = None
    cur = head
    while cur is not None:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    return prev
```

**Struct21.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


def build(values):
    head = None
    for v in reversed(values):
        head = Node(v, head)
    return head


def to_list(head):
    result = []
    while head is not None:
        result.append(head.value)
        head = head.next
    return result


def merge_sorted(a, b):
    dummy = Node(None)
    tail = dummy
    while a is not None and b is not None:
        if a.value <= b.value:
            tail.next = a
            a = a.next
        else:
            tail.next = b
            b = b.next
        tail = tail.next
    tail.next = a if a is not None else b
    return dummy.next
```

**Struct22.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


def build(values):
    head = None
    for v in reversed(values):
        head = Node(v, head)
    return head


def to_list(head):
    result = []
    while head is not None:
        result.append(head.value)
        head = head.next
    return result


def middle(head):
    slow = fast = head
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
    return slow.value
```

**Struct23.**
```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next


def build(values):
    head = None
    for v in reversed(values):
        head = Node(v, head)
    return head


def to_list(head):
    result = []
    while head is not None:
        result.append(head.value)
        head = head.next
    return result


def has_cycle(head):
    slow = fast = head
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False
```

**Struct24.**
```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert(root, value):
    if root is None:
        return TreeNode(value)
    if value < root.value:
        root.left = insert(root.left, value)
    elif value > root.value:
        root.right = insert(root.right, value)
    return root


def build_tree(values):
    root = None
    for v in values:
        root = insert(root, v)
    return root


def inorder(root):
    if root is None:
        return []
    return inorder(root.left) + [root.value] + inorder(root.right)


def contains(root, value):
    while root is not None:
        if value == root.value:
            return True
        root = root.left if value < root.value else root.right
    return False
```

**Struct25.**
```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert(root, value):
    if root is None:
        return TreeNode(value)
    if value < root.value:
        root.left = insert(root.left, value)
    elif value > root.value:
        root.right = insert(root.right, value)
    return root


def build_tree(values):
    root = None
    for v in values:
        root = insert(root, v)
    return root


def inorder(root):
    if root is None:
        return []
    return inorder(root.left) + [root.value] + inorder(root.right)


def tree_sort(values):
    return inorder(build_tree(values))


def tree_min(root):
    while root.left is not None:
        root = root.left
    return root.value


def tree_max(root):
    while root.right is not None:
        root = root.right
    return root.value
```

**Struct26.**
```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert(root, value):
    if root is None:
        return TreeNode(value)
    if value < root.value:
        root.left = insert(root.left, value)
    elif value > root.value:
        root.right = insert(root.right, value)
    return root


def build_tree(values):
    root = None
    for v in values:
        root = insert(root, v)
    return root


def inorder(root):
    if root is None:
        return []
    return inorder(root.left) + [root.value] + inorder(root.right)


def height(root):
    if root is None:
        return 0
    return 1 + max(height(root.left), height(root.right))


def count_leaves(root):
    if root is None:
        return 0
    if root.left is None and root.right is None:
        return 1
    return count_leaves(root.left) + count_leaves(root.right)
```

**Struct27★.**
```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert(root, value):
    if root is None:
        return TreeNode(value)
    if value < root.value:
        root.left = insert(root.left, value)
    elif value > root.value:
        root.right = insert(root.right, value)
    return root


def build_tree(values):
    root = None
    for v in values:
        root = insert(root, v)
    return root


def inorder(root):
    if root is None:
        return []
    return inorder(root.left) + [root.value] + inorder(root.right)


def delete(root, value):
    if root is None:
        return None
    if value < root.value:
        root.left = delete(root.left, value)
    elif value > root.value:
        root.right = delete(root.right, value)
    else:
        if root.left is None:
            return root.right
        if root.right is None:
            return root.left
        successor = root.right
        while successor.left is not None:
            successor = successor.left
        root.value = successor.value
        root.right = delete(root.right, successor.value)
    return root
```

**Struct28★.**
```python
from collections import deque


class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert(root, value):
    if root is None:
        return TreeNode(value)
    if value < root.value:
        root.left = insert(root.left, value)
    elif value > root.value:
        root.right = insert(root.right, value)
    return root


def build_tree(values):
    root = None
    for v in values:
        root = insert(root, v)
    return root


def inorder(root):
    if root is None:
        return []
    return inorder(root.left) + [root.value] + inorder(root.right)


def levels(root):
    if root is None:
        return []
    result = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.value)
            if node.left is not None:
                queue.append(node.left)
            if node.right is not None:
                queue.append(node.right)
        result.append(level)
    return result
```

**Struct29★.**
```python
from collections import deque


def shortest_path(maze):
    rows, cols = len(maze), len(maze[0])
    for r in range(rows):
        for c in range(cols):
            if maze[r][c] == "S":
                start = (r, c)
    queue = deque([(start, 0)])
    visited = {start}
    while queue:
        (r, c), dist = queue.popleft()
        if maze[r][c] == "F":
            return dist
        for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
            nr, nc = r + dr, c + dc
            if (0 <= nr < rows and 0 <= nc < cols
                    and maze[nr][nc] != "#" and (nr, nc) not in visited):
                visited.add((nr, nc))
                queue.append(((nr, nc), dist + 1))
    return -1
```

**Struct30★.**
```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


def insert(root, value):
    if root is None:
        return TreeNode(value)
    if value < root.value:
        root.left = insert(root.left, value)
    elif value > root.value:
        root.right = insert(root.right, value)
    return root


def build_tree(values):
    root = None
    for v in values:
        root = insert(root, v)
    return root


def inorder(root):
    if root is None:
        return []
    return inorder(root.left) + [root.value] + inorder(root.right)


def height(root):
    if root is None:
        return 0
    return 1 + max(height(root.left), height(root.right))


def balanced_tree(values):
    if not values:
        return None
    mid = (len(values) - 1) // 2
    root = TreeNode(values[mid])
    root.left = balanced_tree(values[:mid])
    root.right = balanced_tree(values[mid + 1:])
    return root
```
