**Past tense** یعنی **زمان گذشته**.

یعنی وقتی درباره کاری حرف می‌زنیم که **قبلاً انجام شده / اتفاق افتاده**.

## مثال ساده در انگلیسی
- **Present:** `Order is created` → سفارش ایجاد می‌شود / شده است
- **Past:** `OrderCreated` / `Order was created` → سفارش ایجاد شد

## در بحث Event

می‌گوییم اسم Event بهتر است در **past tense** باشد، چون Event دارد یک **واقعیتِ رخ‌داده** را گزارش می‌کند، نه دستور می‌دهد.

### درست:
- `OrderCreated`
- `PaymentCompleted`
- `UserRegistered`

### نادرست برای Event:
- `CreateOrder`
- `CompletePayment`
- `RegisterUser`

چون این‌ها بیشتر شبیه **Command** هستند، یعنی:
> «این کار را انجام بده»

---

## فرق خیلی خلاصه
- مفهوم **Imperative** = حالت دستوری  
  `CreateOrder`
- مفهوم **Past tense** = حالت اتفاق‌افتاده در گذشته  
  `OrderCreated`

---

## یک فرمول ساده برای تشخیص

اگر بشود آخرش در فارسی گفت:

- **"... را انجام بده"** → احتمالاً Command
- **"... انجام شد / اتفاق افتاد"** → احتمالاً Event

مثال:
- `ShipOrder` → سفارش را ارسال کن ← Command
- `OrderShipped` → سفارش ارسال شد ← Event

