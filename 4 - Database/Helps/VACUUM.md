**VACUUM** در PostgreSQL فرآیندی است که **نسخه‌های قدیمی و بلااستفاده ردیف‌ها (dead tuples)** را پاک می‌کند تا فضا آزاد شود و عملکرد دیتابیس خوب بماند.

دلیل وجود VACUUM به **MVCC** برمی‌گردد.

---

## چرا VACUUM لازم است؟

در PostgreSQL وقتی `UPDATE` یا `DELETE` انجام می‌شود:

- ردیف واقعاً حذف یا overwrite نمی‌شود
- یک **نسخه جدید** ساخته می‌شود
- نسخه قبلی به صورت **dead tuple** باقی می‌ماند

مثال:

```
UPDATE users SET name = 'Ali' WHERE id = 1
```

اتفاقی که می‌افتد:

```
version1 (old) → dead
version2 (new) → active
```

نسخه قدیمی در جدول باقی می‌ماند تا زمانی که VACUUM آن را پاک کند.

اگر VACUUM انجام نشود:

- جدول بزرگ‌تر می‌شود
- indexها بزرگ می‌شوند
- queryها کند می‌شوند

این مشکل را **table bloat** می‌گویند.

---

# VACUUM چه کارهایی انجام می‌دهد؟

VACUUM چند کار مهم انجام می‌دهد:

• حذف **dead tuples**  
• آزاد کردن فضای قابل استفاده در جدول  
• به‌روزرسانی **visibility map**  
• کمک به **query planner**

نکته مهم:

VACUUM معمولی **فضا را به سیستم‌عامل برنمی‌گرداند**، فقط آن را برای reuse آزاد می‌کند.

---

# انواع VACUUM

## 1️⃣ VACUUM (معمولی)

```
VACUUM table_name;
```

ویژگی‌ها:

- non‑blocking
- جدول همچنان قابل استفاده است
- فقط dead tupleها را پاک می‌کند

---

## 2️⃣ VACUUM FULL

```
VACUUM FULL table_name;
```

ویژگی‌ها:

- جدول را **کاملاً بازسازی می‌کند**
- فضای دیسک را واقعاً آزاد می‌کند
- **Table Lock** می‌گیرد

پس:

```
VACUUM FULL = blocking
```

معمولاً در production کم استفاده می‌شود.

---

## 3️⃣ AUTOVACUUM

PostgreSQL به صورت خودکار VACUUM اجرا می‌کند.

فرآیند:

```
Autovacuum daemon
```

زمان اجرا بر اساس:

- تعداد UPDATE
- تعداد DELETE
- اندازه جدول

---

# مثال مشکل بدون VACUUM

فرض کن:

```
UPDATE orders
SET status='done'
```

اگر این کوئری میلیون‌ها بار اجرا شود:

- نسخه‌های قدیمی باقی می‌مانند
- جدول چند برابر بزرگ می‌شود

این همان **bloat** است.

VACUUM آن‌ها را پاک می‌کند.

---

# تفاوت با SQL Server

در SQL Server:

- UPDATE معمولاً **نسخه قبلی را overwrite** می‌کند
- بنابراین dead row باقی نمی‌ماند
- VACUUM لازم نیست

اما در PostgreSQL به دلیل **MVCC** لازم است.

---

✅ **خلاصه کوتاه برای مصاحبه**

درواقع VACUUM در PostgreSQL فرآیندی است که **dead tuples ایجاد شده توسط MVCC (در اثر UPDATE و DELETE)** را پاک می‌کند تا از **table bloat** جلوگیری کند و عملکرد دیتابیس حفظ شود. PostgreSQL معمولاً این کار را با **Autovacuum** به صورت خودکار انجام می‌دهد.

