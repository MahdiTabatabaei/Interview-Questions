درواقع **Back‑pressure** یعنی وقتی **مصرف‌کننده (consumer) کندتر از تولیدکننده (producer)** است، سیستم به تولیدکننده **فشار وارد می‌کند تا سرعت تولید را کم کند یا صبر کند**.

به زبان ساده:

```
Producer → Queue → Consumer
```

اگر Consumer کند باشد، صف پر می‌شود.  
Back‑pressure باعث می‌شود:

```
Producer صبر کند تا Consumer عقب‌ماندگی را جبران کند
```

---

# مثال ساده

فرض کن یک سیستم داریم که:

- Producer: 1000 پیام در ثانیه تولید می‌کند
- Consumer: فقط 100 پیام در ثانیه پردازش می‌کند

بدون Back‑pressure چه می‌شود؟

```
Queue
100
200
300
10000
100000
```

صف دائماً بزرگ‌تر می‌شود ⇒

- مصرف حافظه زیاد
- احتمال crash
- OutOfMemory

---

# با Back‑pressure

اگر ظرفیت صف مثلاً **100** باشد:

وقتی صف پر شد:

```
Producer → صبر می‌کند
```

تا Consumer چند آیتم را پردازش کند.

---

# مثال در .NET Channel

```csharp
var channel = Channel.CreateBounded<int>(5);
```

یعنی ظرفیت صف **۵ آیتم** است.

```csharp
await channel.Writer.WriteAsync(item);
```

اگر صف پر باشد:

```
WriteAsync
   ↓
await می‌شود
   ↓
Producer صبر می‌کند
```

تا Consumer بخواند.

---

# تصویر ذهنی خیلی ساده

مثل یک **رستوران**:

- آشپز = Producer
- گارسون = Consumer
- میز آماده = Queue

اگر میزها پر شوند:

```
آشپز باید صبر کند
```

تا گارسون غذاها را ببرد.

این همان **Back‑pressure** است.

---

# جاهایی که Back‑pressure خیلی مهم است

- **Channel**
- **Reactive Streams**
- **Kafka / Streaming**
- **Pipelines**
- **High‑throughput servers**
- **Networking**

---

# خلاصه کوتاه 

**Back‑pressure مکانیزمی است که وقتی مصرف‌کننده کندتر از تولیدکننده است، تولیدکننده را مجبور می‌کند سرعت تولید را کاهش دهد یا منتظر بماند تا سیستم از پر شدن بیش از حد صف و مصرف حافظه جلوگیری کند.**

