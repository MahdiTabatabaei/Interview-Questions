در SQL (به‌خصوص **SQL Server**) منظور از **Recursive** معمولاً **Recursive CTE** است.

درواقع Recursive CTE ساختاری است که به شما اجازه می‌دهد **داده‌های سلسله‌مراتبی (Hierarchy)** را از یک جدول بخوانید، مثل:

- ساختار درختی کارمند–مدیر  
- دسته‌بندی محصولات  
- ساختار پوشه‌ها  
- قابلیت Parent / Child

و کوئری به صورت **خودخوان (self-referencing)** اجرا می‌شود.

---

# تعریف Recursive CTE

یک **CTE بازگشتی** از دو بخش ساخته می‌شود:

1. **Anchor Query**  
   نقطه شروع (Root) داده‌ها

2. **Recursive Query**  
   قسمتی که دوباره به خود CTE ارجاع می‌دهد  
   و رکوردهای بعدی (Child) را پیدا می‌کند

ساختار:

```sql
WITH cte_name AS (
    -- 1. Anchor member
    SELECT ...

    UNION ALL

    -- 2. Recursive member
    SELECT ...
    FROM cte_name
    JOIN table t ON ...
)
SELECT * FROM cte_name;
```

---

# مثال ساده (ساختار سازمانی)

جدول Employees:

|Id|Name|ManagerId|
|--|----|---------|
|1|Ali|NULL|
|2|Sara|1|
|3|Reza|2|
|4|Mina|2|

می‌خواهیم از **ریشه (Ali)** شروع کنیم و همه زیرمجموعه‌ها را پیدا کنیم.

```sql
WITH EmployeeCTE AS (
    -- Anchor
    SELECT Id, Name, ManagerId, 0 AS Level
    FROM Employees
    WHERE ManagerId IS NULL

    UNION ALL

    -- Recursive member
    SELECT e.Id, e.Name, e.ManagerId, c.Level + 1
    FROM Employees e
    JOIN EmployeeCTE c ON e.ManagerId = c.Id
)
SELECT *
FROM EmployeeCTE;
```

خروجی:

|Id|Name|ManagerId|Level|
|--|----|---------|-----|
|1|Ali|NULL|0|
|2|Sara|1|1|
|3|Reza|2|2|
|4|Mina|2|2|

---

# کاربردهای Recursive CTE

### 1. پیمایش ساختار درختی (Hierarchy)

مثل:
- مدیر → کارمند  
- دسته‌بندی → زیردسته  
- کشور → استان → شهر  

### 2. محاسبه عمق یا Level

مثل مثال بالا.

### 3. پیدا کردن Root یا Leaf

مثل پیدا کردن بالاترین مدیر.

### 4. ساخت دنباله‌ها (Sequence)

مثلاً تولید اعداد 1 تا 100:

```sql
WITH nums AS (
   SELECT 1 AS n
   UNION ALL
   SELECT n + 1
   FROM nums
   WHERE n < 100
)
SELECT * FROM nums;
```

### 5. قابلیت Graph traversal

برای پیمایش Node → Node.

