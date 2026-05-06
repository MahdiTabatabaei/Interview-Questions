درواقع **Throughput** یعنی مقدار کاری که یک سیستم در یک بازه زمانی مشخص انجام می‌دهد.

به زبان ساده:

> **چند واحد کار در هر ثانیه پردازش می‌شود.**

---

## مثال ساده

اگر یک Message Broker بتواند:

```
50,000 message / second
```

پردازش کند، می‌گوییم:

```
Throughput = 50k msg/s
```

---

## در سیستم‌های مختلف

### در Message Broker

```
Throughput = messages processed per second
```

مثال:

```
Kafka → 1,000,000 msg/s
RabbitMQ → 50,000 msg/s
```

---

### در شبکه

```
Throughput = data transferred per second
```

مثال:

```
1 Gbps
100 MB/s
```

---

### در دیتابیس

```
Throughput = queries per second
```

یا:

```
TPS (transactions per second)
```

---

## تفاوت Throughput و Latency

این دو اغلب با هم اشتباه گرفته می‌شوند.

### Latency

زمان انجام یک عملیات.

مثال:

```
Latency = 5 ms
```

یعنی یک پیام در ۵ میلی‌ثانیه پردازش می‌شود.

---

### Throughput

تعداد عملیات در زمان.

مثال:

```
Throughput = 10,000 msg/s
```

---

### مثال واقعی

یک سیستم ممکن است:

```
Latency = 100 ms
Throughput = 200k msg/s
```

یعنی:
- هر پیام 100ms طول می‌کشد
- ولی تعداد زیادی همزمان پردازش می‌شوند

---

## مثال صف سوپرمارکت

فرض کنید ۱۰ صندوق دارید.

Latency:

```
زمان پرداخت یک مشتری = 30 ثانیه
```

Throughput:

```
در هر دقیقه = 20 مشتری
```

اگر ۲۰ صندوق اضافه کنید:

```
Latency = همان 30 ثانیه
Throughput = 40 مشتری در دقیقه
```

---

✅ **خلاصه**

```
Throughput = حجم پردازش در واحد زمان
```

معمولاً با این واحدها بیان می‌شود:

- messages/sec
- requests/sec
- transactions/sec
- MB/sec

