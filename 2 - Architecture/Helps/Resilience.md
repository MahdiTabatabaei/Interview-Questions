
> **توانایی سیستم برای ادامه‌دادن کار، حتی وقتی بخشی از سیستم دچار خطا، کندی، قطعی یا فشار زیاد می‌شود.**

یعنی سیستم با اولین مشکل **نمی‌خوابد**، بلکه سعی می‌کند:

- خطا را تحمل کند
- اثر آن را محدود کند
- سریع ریکاور شود
- کل سیستم را قربانی یک بخش خراب نکند

---

# تعریف خیلی ساده

فرض کن یک میکروسرویس داری که به `PaymentService` وصل می‌شود.

اگر `PaymentService`:
- کند شود
- و timeout بدهد
- موقتاً down شود

یک سیستم **غیر resilient** ممکن است:

- تمام requestها را معطل کند
- و threadها را پر کند
- خودش هم down شود

اما یک سیستم **resilient**:

- قابلیت timeout می‌گذارد
- و میتواند retry کنترل‌شده انجام دهد
- قابلیت circuit breaker دارد
- مکانیزم [[fallback]] دارد
- و [[Bulkhead]] دارد

یعنی با وجود مشکل، **تا جای ممکن سرویس‌دهی را حفظ می‌کند**.

---

# مثال ملموس

فرض کن یک فروشگاه اینترنتی داری:

- `OrderService`
- `PaymentService`
- `InventoryService`
- `NotificationService`

اگر `NotificationService` خراب شود:

## سیستم غیر resilient

کل ثبت سفارش fail می‌شود چون ایمیل ارسال نشد.

## سیستم resilient

سفارش ثبت می‌شود،  
فقط ارسال ایمیل بعداً retry می‌شود یا می‌رود در صف.

یعنی:
- مشکل رخ داده
- ولی کل business flow نخوابیده

---

# مفهوم Resilience چه چیزهایی را شامل می‌شود؟

معمولاً این الگوها زیرمجموعه resilience هستند:

- **Timeout**
- **Retry**
- **Circuit Breaker**
- **Bulkhead**
- **Fallback**
- **Rate Limiting**
- **Health Checks**
- **Queue-based load leveling**
- **Graceful degradation**

---

# تفاوت با Reliability

این دو نزدیک‌اند ولی یکی نیستند:

## Reliability
یعنی سیستم **درست کار کند** و خروجی قابل اعتماد بدهد.

## Resilience
یعنی اگر مشکل پیش آمد، **باز هم از کار نیفتد** و بتواند ادامه دهد.

### ساده‌تر:
- مفهوم Reliability = چقدر سیستم معمولاً درست کار می‌کند
- مفهوم Resilience = وقتی خرابکاری پیش آمد، چقدر خوب دوام می‌آورد

---

# یک تعریف کاربردی 

اگر بخواهی حرفه‌ای بگویی:

> Resilience is the ability of a distributed system to tolerate transient failures, partial outages, latency spikes, and resource exhaustion while continuing to provide an acceptable level of service.

---

# در معماری میکروسرویس چرا مهم است؟

چون در میکروسرویس:

- شبکه همیشه قابل اعتماد نیست
- سرویس‌ها مستقل‌اند
- میزان dependency زیاد است
- رخداد failure اجتناب‌ناپذیر است

پس باید بپذیریم که:
> **Failure is normal**

و سیستم را طوری طراحی کنیم که با failure زندگی کند، نه اینکه با هر failure نابود شود.

---

# خلاصه خیلی کوتاه

معنای **Resilience یعنی تاب‌آوری سیستم در برابر خطا و اختلال.**

یعنی:
- خطا پیش می‌آید
- ولی سیستم همچنان تا حد ممکن کار می‌کند
- و سریع به حالت عادی برمی‌گردد

