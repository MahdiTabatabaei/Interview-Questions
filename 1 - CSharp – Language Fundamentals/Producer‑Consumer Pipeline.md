## تعریف Producer‑Consumer Pipeline چیست؟

درواقع Pipeline یعنی **چند مرحله پردازش داده پشت سر هم**.

هر مرحله:

- داده دریافت می‌کند
- پردازش می‌کند
- به مرحله بعد می‌فرستد

---

## مثال واقعی

فرض کنید یک سیستم پردازش تصویر داریم:
```markdown

Stage 1 → دریافت فایل
Stage 2 → resize تصویر
Stage 3 → ذخیره در دیتابیس

```
این می‌شود:
```markdown

Producer → Processor → Storage

```
---

## با Channel

هر مرحله یک Channel دارد.
```markdown

Producer -> Channel1 -> Processor -> Channel2 -> Storage

```
---

### مثال #C 

```csharp

var stage1 = Channel.CreateUnbounded<int>();
var stage2 = Channel.CreateUnbounded<int>();

// producer
Task.Run(async () =>
{
    for (int i = 1; i <= 5; i++)
        await stage1.Writer.WriteAsync(i);

    stage1.Writer.Complete();
});

// processor
Task.Run(async () =>
{
    await foreach (var item in stage1.Reader.ReadAllAsync())
    {
        int result = item * 10;
        await stage2.Writer.WriteAsync(result);
    }

    stage2.Writer.Complete();
});

// final consumer
await foreach (var item in stage2.Reader.ReadAllAsync())
{
    Console.WriteLine($"Final: {item}");
}

```

جریان داده:
```markdown

1 → 10 → output
2 → 20 → output
3 → 30 → output

```
این می‌شود **Pipeline processing**.
