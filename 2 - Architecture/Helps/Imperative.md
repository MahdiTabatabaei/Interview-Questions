درواقع **Imperative** یعنی «امری/دستوری»؛ یعنی جمله یا پیام به شکل **دستور انجام یک کار** بیان می‌شود.

## در برنامه‌نویسی (Imperative programming)

سبکی که به کامپیوتر **می‌گوید چه کارهایی را قدم‌به‌قدم انجام بده** (چطور انجام بده):

- «این متغیر را مقدار بده»
- «این حلقه را اجرا کن»
- «اگر شرط برقرار بود این کار را بکن»

مثال:

```csharp
var sum = 0;
foreach (var x in numbers)
    sum += x;
```

اینجا دستورهای مرحله‌ای داریم.

## در معماری پیام‌ها (Command vs Event)

وقتی گفتم **Commandها Imperative هستند** یعنی پیام از جنس دستور است:

- **Command (Imperative):** «این کار را انجام بده»
  - `ChargePayment`
  - `ShipOrder`
  - `CreateInvoice`

- **Event (Declarative/Fact):** «این اتفاق افتاد»
  - `PaymentCharged`
  - `OrderShipped`
  - `InvoiceCreated`

پس:
- **Imperative = Do this**
- **Event = This happened**

اگر دوست داری، می‌تونم چند مثال واقعی از نام‌گذاری درست Command و Event در سیستم سفارش/پرداخت بزنم تا کاملاً جا بیفته.