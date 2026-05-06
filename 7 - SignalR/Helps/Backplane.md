معنای **Backplane** در SignalR یعنی یک **سیستم واسطه برای انتقال پیام بین چند سرور**.

وقتی برنامهٔ SignalR فقط روی **یک سرور** اجرا شود، سرور مستقیماً پیام را به کلاینت‌های متصل به خودش می‌فرستد.  
اما وقتی برنامه روی **چند سرور** اجرا شود (برای Scale‑Out)، هر سرور فقط کلاینت‌های خودش را می‌شناسد.

در این حالت **Backplane** وارد عمل می‌شود.

### مفهوم ساده
درواقع Backplane یک **کانال ارتباطی مشترک بین سرورهای SignalR** است که پیام‌ها را بین آن‌ها پخش می‌کند.

### نحوهٔ کار
1. یک سرور پیام را تولید می‌کند.  
2. پیام را به **Backplane** ارسال می‌کند.  
3. در اینجا Backplane پیام را به **بقیهٔ سرورها** منتقل می‌کند.  
4. هر سرور پیام را به کلاینت‌های متصل به خودش می‌فرستد.

### مثال از Backplaneها

چند تکنولوژی که به عنوان Backplane استفاده می‌شوند:

- **Redis**
- **Azure SignalR Service**
- **SQL Server** (در نسخه‌های قدیمی‌تر)

### یک مثال ساده
فرض کن ۳ سرور SignalR داری:

- Server1  
- Server2  
- Server3  

اگر Server1 پیامی بفرستد:
- پیام به **Redis (Backplane)** می‌رود
- و Redis پیام را به Server2 و Server3 می‌دهد
- آن‌ها پیام را به کلاینت‌های خودشان می‌فرستند

### خلاصه

درواقع Backplane یعنی **سیستمی که پیام‌های SignalR را بین چند سرور هماهنگ و توزیع می‌کند** تا همهٔ کلاینت‌ها پیام‌های real‑time را دریافت کنند.

----

در ادامه یک **مثال کاملاً عملی و واقعی** از استفادهٔ SignalR با **Redis Backplane** می‌آورم.  
این مثال دقیقاً نشان می‌دهد اگر برنامه را روی چند سرور اجرا کنی، چگونه Redis پیام‌ها را بین سرورها هماهنگ می‌کند.

------------------------------------

## سناریو  
دو سرور داری (یا دو Instance از برنامه):
- Server A  
- Server B  

کلاینت‌ها ممکن است به هرکدام وصل شوند.

وقتی یک کلاینت روی Server A پیام ارسال کند، باید کلاینت‌های وصل‌شده به Server B هم پیام را بگیرند.  
بدون Backplane این اتفاق **نمی‌افتد**.

------------------------------------

# مثال عملی: SignalR + Redis Backplane

## 1. نصب پکیج‌ها در پروژه ASP.NET Core

در فایل csproj یا با NuGet:

```
dotnet add package Microsoft.AspNetCore.SignalR.StackExchangeRedis
```

------------------------------------

## 2. تنظیم Backplane در Program.cs

این قسمت مهم‌ترین بخش است:

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddSignalR()
    .AddStackExchangeRedis("localhost:6379", options =>
    {
        options.Configuration.ChannelPrefix = "myapp";
    });

var app = builder.Build();

app.MapHub<ChatHub>("/chat");

app.Run();
```

توضیح:
- اتصال Redis: `localhost:6379`
- پراپرتی `ChannelPrefix`: برای اینکه اگر چند برنامه از Redis استفاده می‌کنند، پیام‌ها اشتباه تداخل پیدا نکند.

------------------------------------

## 3. یک Hub ساده برای تست

```csharp
public class ChatHub : Hub
{
    public async Task SendMessage(string user, string message)
    {
        await Clients.All.SendAsync("ReceiveMessage", $"{user}: {message}");
    }
}
```

------------------------------------

## 4. کلاینت جاوااسکریپت برای تست

```html
<script src="/lib/signalr/signalr.js"></script>

<script>
   const connection = new signalR.HubConnectionBuilder()
       .withUrl("/chat")
       .build();

   connection.on("ReceiveMessage", msg => {
       console.log("Message from server:", msg);
   });

   connection.start().then(() => {
       console.log("Connected!");

       document.getElementById("sendBtn").onclick = () => {
           const user = "Ali";
           const message = document.getElementById("msg").value;
           connection.invoke("SendMessage", user, message);
       };
   });
</script>
```

------------------------------------

## 5. نحوه تست کردن Scale-Out

### 1) یک Redis اجرا کن  
مثلاً در Docker:

```
docker run -p 6379:6379 redis
```

### 2) برنامه را روی دو پورت اجرا کن

در Visual Studio یا CLI:

```
dotnet run --urls="http://localhost:5000"
dotnet run --urls="http://localhost:5001"
```

### 3) تست:

- یک مرورگر باز کن و برو به  
  http://localhost:5000  
- مرورگر دیگر باز کن و برو به  
  http://localhost:5001  

حالا یک پیام بفرست:

- اگر در صفحه 5000 پیام بفرستی  
  → **در صفحه 5001 هم نمایش داده می‌شود!**

این یعنی **Redis Backplane پیام را منتقل و هماهنگ کرده است.**

------------------------------------

## خلاصه

پس این یک اجرای کامل بود:

- SignalR بدون Backplane → پیام فقط بین کلاینت‌های همان سرور  
- SignalR با Redis Backplane → پیام بین کلاینت‌های همهٔ سرورها پخش می‌شود

این دقیقاً همان **Scale-Out واقعی** است.

