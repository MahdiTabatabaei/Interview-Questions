درواقع **String Intern Pool** یک ناحیه خاص در **Heap** در ‎.NET‎ است که در آن **رشته‌های تکراری فقط یک‌بار در حافظه ذخیره می‌شوند** تا مصرف حافظه و زمان ایجاد object کمتر شود.

به زبان ساده:

اگر چند بار یک string یکسان ساخته شود، CLR بررسی می‌کند که آیا قبلاً در **pool** وجود دارد یا نه.

اگر وجود داشته باشد **همان object قبلی استفاده می‌شود**.

---

# مثال ساده
```csharp

string a = "Hello";
string b = "Hello";

```
در حافظه فقط **یک object** ساخته می‌شود.
```markdown

String Intern Pool
      "Hello"
       ↑   ↑
       a   b

```
هر دو متغیر به **یک آدرس حافظه** اشاره می‌کنند.

---

# تست با ReferenceEquals
```csharp

string a = "Hello";
string b = "Hello";

Console.WriteLine(Object.ReferenceEquals(a, b));

```
خروجی:
```markdown

True

```
چون هر دو به **یک object** اشاره می‌کنند.

---

# اما همیشه این اتفاق نمی‌افتد

اگر string در runtime ساخته شود ممکن است داخل pool نباشد.
```csharp

string a = "Hello";
string b = new string("Hello".ToCharArray());

Console.WriteLine(Object.ReferenceEquals(a, b));

```
خروجی:
```markdown

False

```
چون:

- `a` داخل **String Pool** است
- `b` یک object جدید در heap است

---

# اضافه کردن دستی به Pool

می‌توان یک string را به صورت دستی **intern** کرد.
```csharp

string a = "Hello";
string b = new string("Hello".ToCharArray());

b = string.Intern(b);

Console.WriteLine(Object.ReferenceEquals(a, b));

```
خروجی:
```markdown

True

```
# چرا String Pool وجود دارد؟

سه دلیل اصلی:

1️⃣ **صرفه‌جویی در حافظه**

به جای ذخیره 1000 بار `"Hello"` فقط یک object ساخته می‌شود.

2️⃣ **افزایش performance**

ساخت object کمتر → فشار کمتر روی GC.

3️⃣ **مقایسه سریع‌تر**

گاهی می‌توان با مقایسه reference تشخیص داد دو string برابرند.

---

# ارتباط مهم با Immutable بودن string

در واقع String Pool فقط به این دلیل ممکن است که **string immutable است**.

اگر mutable بود:

```markdown

a = "Hello"
b = "Hello"

```
و اگر `a` تغییر می‌کرد:
```markdown

a[0] = 'Y'

```
آن وقت `b` هم تغییر می‌کرد → که فاجعه است.

پس immutable بودن شرط اصلی **String Intern Pool** است.

---

✅ خلاصه کوتاه:

درنهایت **String Intern Pool = مکانی در Heap که رشته‌های یکسان را فقط یک بار نگه می‌دارد و بقیه reference ها به همان object اشاره می‌کنند.**
