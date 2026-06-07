# تعریف High Throughput چیست؟

مفهوم **Throughput** یعنی:
```markdown

چند عملیات در واحد زمان انجام می‌شود

```
مثلاً:
```markdown

5000 request / second

```
---

## High Throughput System

سیستمی که بتواند:
```markdown

تعداد زیادی پیام / عملیات
در زمان کم
پردازش کند

```
مثال‌ها:

- message queue processing
- logging systems
- streaming data
- trading systems
- telemetry systems

---

## مثال واقعی

فرض کنید:
```markdown

100,000 پیام در ثانیه

```
اگر:

- وضعیت lock زیاد باشد
- پردازش های thread زیاد block شود

در این حالت performance خراب می‌شود.

---

## چرا Channel برای High Throughput خوب است؟

چند دلیل مهم:

### 1️⃣ async based

باعث میشود thread block نشود
```markdown

await

```
---

### 2️⃣ lock-free design

استفاده از:
```markdown

atomic operations

```
به جای lock سنگین

---

### 3️⃣ allocation کم

memory pressure کم است.

---

### 4️⃣ batching داخلی

در برخی سناریوها Channel می‌تواند چند آیتم را بهینه مدیریت کند.

---

## مثال High Throughput Worker
```csharp

var channel = Channel.CreateUnbounded<int>();

// producer
Task.Run(async () =>
{
    for (int i = 0; i < 100000; i++)
    {
        await channel.Writer.WriteAsync(i);
    }

    channel.Writer.Complete();
});

// consumers
var workers = Enumerable.Range(0, 4)
    .Select(_ => Task.Run(async () =>
    {
        await foreach (var item in channel.Reader.ReadAllAsync())
        {
            // simulate work
        }
    }));

await Task.WhenAll(workers);

```
در اینجا:
```markdown

1 producer
4 consumer
100k message

```

سیستم با throughput بالا کار می‌کند.
