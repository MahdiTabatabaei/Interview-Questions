
درواقع **Fencing یعنی:**

جلوگیری از این‌که **یک موجودیت قدیمی** (Leader قدیمی، Producer قدیمی، Session قدیمی)  
بعد از بازیابی یا reconnect دوباره وارد سیستم شود و **عملیات اشتباه** انجام دهد.

چون در سیستم‌های توزیع‌شده، ممکن است:

- یک نود (Leader قبلی) برای مدتی از شبکه جدا شود
- در این مدت Leader جدید انتخاب شود
- نود قدیمی دوباره برگردد ولی خودش فکر کند هنوز Leader است

اگر این اتفاق بدون کنترل شود → **Split Brain رخ می‌دهد**

پس Kafka باید «نود قدیمی» را **مسدود** کند.

این مسدودسازی = **Fencing**

---

# حالا نقش Epoch چیست؟

درواقع Kafka از **epoch** (شمارهٔ نسل) برای شناسایی قدیمی یا جدید بودن نقش‌ها استفاده می‌کند.

دو نوع epoch مهم:

- **Leader Epoch** → برای Partition Leaderها
- **Producer Epoch** → برای Producerهای Transactional/Idempotent

هر بار که یک Leader جدید انتخاب می‌شود:

```
leader_epoch = leader_epoch + 1
```

پس Leader جدید **epoch بزرگ‌تری** دارد.

---

# چطور Fencing با Epoch انجام می‌شود؟

## سناریو ۱: Leader تقسیم‌شده (split brain)

فرض کن:

- Broker1 Leader است → epoch = 5  
- Broker1 از شبکه قطع می‌شود
- Broker2 Leader جدید می‌شود → epoch = 6  
- Broker1 دوباره برمی‌گردد، اما هنوز فکر می‌کند Leader است!

اگر Kafka epoch نداشت، Broker1:

- درخواست Write دریافت می‌کرد
- داده خارج از ترتیب یا ناسازگار می‌نوشت
- باعث **Split Brain** می‌شد

### اما Kafka با epoch چه می‌کند؟

Leader جدید (Broker2) چون epoch = 6 دارد  
هر درخواست یا پیام Leader قدیمی (Broker1) با epoch = 5 را **رد می‌کند**.

این یعنی:

```
Broker1 (epoch 5) → fenced
Broker2 (epoch 6) → valid leader
```

پس Leader قدیمی دیگر نمی‌تواند:

- پیام دریافت کند
- replicate کند
- یا دستور متادیتا بدهد

**این یعنی "Fencing با Epoch".**

---

# سناریو ۲: Producer Idempotent یا Transactional

Kafka برای Producerها هم از **Producer Epoch** استفاده می‌کند.

مثال:

یک Producer transactional با این حالت‌ها:

```
ProducerId = 100
ProducerEpoch = 3
```

Producer از شبکه قطع می‌شود (Crash یا partition شبکه).

Kafka اجازه می‌دهد یک Producer جدید با **همان ProducerId** ولی epoch جدید ساخته شود:

```
ProducerEpoch = 4
```

وقتی Producer قدیمی برمی‌گردد:

- پیام‌هایی با Sequence Number یا epoch قدیمی می‌فرستد
- Broker آن‌ها را **reject** می‌کند

نتیجه:

❌ Producer قدیمی → fenced  
✔ Producer جدید → معتبر  

این یکی از پایه‌های Exactly-once semantics است.

---

# چرا Epoch بهترین روش جلوگیری از Split Brain است؟

چون:

- نیاز به زمان ندارد  
- نیاز به clock sync ندارد  
- مقیاس‌پذیر است  
- سریع است  
- قابل اثبات ریاضی (در KRaft و Raft)

به محض این‌که یک Leader جدید انتخاب می‌شود، با **epoch جدید**:

- Leader قدیمی دیگر نمی‌تواند کاری انجام دهد
- حتی اگر شبکه به شکل اشتباه دوباره وصل شود

این همان چیزی است که به سیستم‌های توزیع‌شده **safety** می‌دهد.

---

# خلاصهٔ ساده

```
Epoch = شماره نسخه یا نسل یک Leader یا Producer
Fencing = جلوگیری از استفاده نسخه‌های قدیمی
```

Kafka با افزایش epoch در Leader/Producer جدید:

- نسخه‌های قدیمی‌تر را بی‌اعتبار و بلاک می‌کند
- جلوی Split Brain را می‌گیرد
- از ناسازگاری داده جلوگیری می‌کند
- Exactly-once را ممکن می‌کند

