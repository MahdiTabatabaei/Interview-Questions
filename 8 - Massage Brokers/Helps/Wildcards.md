در دنیای **MQTT**، اصطلاح **Wildcard (کاراکتر جای‌گزین یا عام‌گیر)** به علامت‌هایی گفته می‌شود که به شما اجازه می‌دهد **تاپیک‌هایی با الگوهای مشابه** را در یک دستور Subscription سابسکرایب کنید، بدون آن‌که حتماً نام کامل همه تاپیک‌ها را بدانید.

این ویژگی مخصوص **Subscriber**هاست؛ یعنی فقط مشترکان می‌توانند در زمان subscription از wildcard استفاده کنند، ناشر (Publisher) موظف است همیشه یک تاپیک دقیق برای ارسال پیام انتخاب کند.

---

# انواع Wildcard در MQTT

در MQTT دو نوع wildcard دارد:

## 1. **Single-Level Wildcard (`+`)**
یک علامت `+` به معنی "هر چیزی در این سطح تاپیک".

**مثال:**
```
home/+/temperature
```
- دریافت پیام از:
    - `home/kitchen/temperature`
    - `home/living_room/temperature`
    - `home/balcony/temperature`
- دریافت نمی‌کند از:
    - `home/kitchen/humidity`
    - `home/kitchen/inside/temperature`

هر `+` فقط یکی از اجزای جداشده با `/` را می‌پوشاند.

---

## 2. **Multi-Level Wildcard (`#`)**

یک علامت `#` در انتهای Subscription به معنی "هر چیزی از اینجا به بعد (در هر تعداد سطح)" است.

**مثال:**
```
home/kitchen/# 
```

- دریافت پیام از:
    - `home/kitchen`
    - `home/kitchen/temp`
    - `home/kitchen/temp/inside`
    - `home/kitchen/devices/fridge`
- دریافت نمی‌کند از:
    - `home/livingroom`
    - `home`

**نکته مهم:**  
علامت `#` فقط در پایان subscription و بعد از `/` قابل استفاده است.

---

# کاربرد Wildcard  

**وقتی نمی‌دانیم اسم دقیق همه تاپیک‌ها چیست یا می‌خواهیم همه تاپیک‌های مشابه را یکجا گوش کنیم:**
- تمام سنسورهای دما یک ساختمان:  
  `building/+/temperature`
- همه پیام‌هایی که مربوط به یک طبقه‌اند:  
  `building/floor2/#`
- همه پیام‌ها:  
  `#`

---

# مثال خلاصه

| الگو                        | پیام‌هایی که Match می‌شوند           |
|-----------------------------|--------------------------------------|
| `home/+/humidity`           | `home/kitchen/humidity`              |
| `home/+/+`                  | `home/kitchen/humidity`, `home/living/temp`|
| `home/#`                    | `home/kitchen/temp`, `home/living`, ...|
| `#`                         | همه تاپیک‌ها                         |

---

**خلاصه:**  
درواقع Wildcardها در MQTT قدرت زیادی به Subscriber می‌دهند تا "چتری" از تاپیک‌ها را در یک دستور سابسکرایب کند  
و مدیریت پیام‌ها برای سیستم‌های پیچیده را ساده می‌کند.

