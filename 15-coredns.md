##Core DNS
 
 یک سرویس DNS (Domain Name System) است که به طور پیش‌فرض به عنوان سیستم DNS داخلی در کلاسترهای Kubernetes استفاده می‌شود. این سرویس وظیفه دارد نام‌های دامنه (domain names) را به آدرس‌های IP ترجمه کند و برعکس، که این امر برای ارتباطات درونی بین سرویس‌ها و پادها در Kubernetes بسیار حیاتی است.

### وظایف و ویژگی‌های `CoreDNS` در Kubernetes:

1. **حل نام (Name Resolution)**:

   درخواست‌های DNS را از پادها دریافت می‌کند و نام‌های دامنه مانند `my-service.default.svc.cluster.local` را به آدرس‌های IP مربوط به پادها یا سرویس‌ها درون کلاستر Kubernetes ترجمه می‌کند. این امر باعث می‌شود که سرویس‌ها بتوانند به راحتی و بدون نیاز به دانستن آدرس IP همدیگر، با همدیگر ارتباط برقرار کنند.

3. **پیکربندی پویا**
   با استفاده از ConfigMapهای Kubernetes پیکربندی می‌شود. این امکان را فراهم می‌کند که تنظیمات DNS به راحتی و بدون نیاز به راه‌اندازی مجدد تغییر کنند.

5. **پلاگین‌پذیری**:
   
 از یک معماری پلاگین‌محور استفاده می‌کند که اجازه می‌دهد تا قابلیت‌های اضافی به آن اضافه شود. این پلاگین‌ها می‌توانند قابلیت‌های مختلفی از جمله کش کردن (caching)، فیلترینگ درخواست‌ها، و حتی تنظیمات پیشرفته‌تر شبکه را ارائه دهند.

6. **پشتیبانی از نام‌های DNS برای سرویس‌ها و پادها**:
   
   - در Kubernetes، هر سرویس و پاد یک نام DNS خاص دارد که `CoreDNS` آن را مدیریت می‌کند. برای مثال، یک سرویس در فضای نام `default` با نام `my-service` به صورت پیش‌فرض دارای نام DNS `my-service.default.svc.cluster.local` خواهد بود.

8. **Load Balancing داخلی**:
   - `CoreDNS` از قابلیت load balancing پشتیبانی می‌کند. این بدین معنی است که اگر یک سرویس چندین پاد پشت خود داشته باشد، `CoreDNS` می‌تواند درخواست‌های DNS را بین این پادها توزیع کند.

### نحوه کار `CoreDNS` در Kubernetes:
- وقتی یک پاد می‌خواهد با یک سرویس دیگر در کلاستر ارتباط برقرار کند، از نام DNS آن سرویس استفاده می‌کند. درخواست DNS از طریق پاد به `CoreDNS` ارسال می‌شود.
- `CoreDNS` این درخواست را پردازش کرده و نام DNS را به آدرس IP داخلی سرویس ترجمه می‌کند.
- سپس پاد می‌تواند از این آدرس IP برای برقراری ارتباط با سرویس مورد نظر استفاده کند.


به طور خلاصه، `CoreDNS` یک جزء اساسی در Kubernetes است که وظیفه مدیریت و حل نام‌های DNS داخلی کلاستر را بر عهده دارد و به پادها و سرویس‌ها امکان می‌دهد به راحتی و با استفاده از نام‌های دامنه یکدیگر را پیدا کنند و با هم ارتباط برقرار کنند.

![image](https://github.com/user-attachments/assets/22b1ab3b-a90b-44f6-8615-69affc306be2)
