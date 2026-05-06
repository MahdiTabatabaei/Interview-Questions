کالِژِن  یا  کالیشن **Collision** یعنی:

وقتی **دو شیء متفاوت، یک HashCode یکسان تولید کنند.**

مثلاً:
```markdown

Object A  → HashCode = 1234
Object B  → HashCode = 1234

```
در حالی که:
```markdown

A.Equals(B) == false

```
یعنی این دو شیء **برابر نیستند** ولی **HashCode آنها یکی شده**.

به این حالت می‌گویند **Hash Collision**.

---

## چرا Collision اتفاق می‌افتد؟

چون `GetHashCode()` فقط یک **عدد محدود (int)** برمی‌گرداند.

در .NET:
```markdown

int = 32 bit
≈ 4 میلیارد مقدار ممکن

```
اما تعداد اشیاء ممکن **بی‌نهایت بیشتر** از این است.

پس طبیعی است که بعضی اشیاء **hash یکسان** داشته باشند.

---

## مثال ساده

فرض کن این دو رشته hash یکسان بدهند:

```markdown

"FB"
"Ea"

```
ممکن است:
```markdown

"FB".GetHashCode() == "Ea".GetHashCode()

```
ولی:
```markdown

"FB".Equals("Ea") → false

```
این یعنی **collision**.

---

## در Dictionary چه اتفاقی می‌افتد؟

وقتی چیزی را در `Dictionary` جستجو می‌کنی:

### مرحله 1

ابتدا:
```markdown

GetHashCode

```
تا **bucket** را پیدا کند.

### مرحله 2

اگر چند آیتم hash یکسان داشتند:
```markdown

Equals

```
اجرا می‌شود تا شیء دقیق پیدا شود.

پس ترتیب:
```markdown

GetHashCode → برای سرعت
Equals → برای دقت

```
---

## مثال تصویری

فرض کن Dictionary این شکلی است:

```markdown

Bucket 1234
 ├── Object A
 ├── Object B
 └── Object C

```
همه اینها ممکن است **HashCode = 1234** داشته باشند.

پس Dictionary مجبور است:

```markdown

A.Equals(X)
B.Equals(X)
C.Equals(X)

```

را چک کند.

---

## نتیجه مهم

مفهوم Collision **اشتباه یا bug نیست**.

کاملاً طبیعی است.

به همین دلیل است که:
```markdown

HashCode فقط برای سرعت است
Equals برای تشخیص واقعی

```
