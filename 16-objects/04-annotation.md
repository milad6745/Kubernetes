در Kubernetes، انوتیشن‌ها (Annotations) اطلاعات اضافی و سفارشی دربارهٔ منابع (مثل Podها یا Nodeها) را فراهم می‌کنند. انوتیشن‌ها به شما امکان می‌دهند اطلاعات توضیحی یا متا‌دیتاهای اضافی را به منابع افزوده و ذخیره کنید. این اطلاعات معمولاً توسط ابزارها و اسکریپت‌های خاص مورد استفاده قرار می‌گیرند.

### افزودن انوتیشن به یک منبع:

برای افزودن یک انوتیشن به یک منبع، می‌توانید از دستور `kubectl annotate` استفاده کنید. به عنوان مثال:

```bash
kubectl annotate pod <نام-پاد> key1=value1 key2=value2
```

در اینجا `<نام-پاد>` را با نام واقعی پاد خود جایگزین کنید و `key1=value1` و `key2=value2` را با انواع اطلاعات دلخواه خود جایگزین کنید.

### مشاهده انوتیشن‌ها:

برای مشاهده انوتیشن‌های یک منبع، می‌توانید از دستور `kubectl describe` استفاده کنید. به عنوان مثال:

```bash
kubectl describe pod <نام-پاد>
```

### مثال:

فرض کنید یک انوتیشن به یک Pod اضافه کرده‌ایم:

```bash
kubectl annotate pod mypod description="This is my pod" owner="John Doe"
```

حالا می‌توانیم این اطلاعات را با استفاده از `kubectl describe` بررسی کنیم:

```bash
kubectl describe pod mypod
```

در خروجی، بخشی از اطلاعات اضافی (انوتیشن‌ها) را خواهید دید.

توجه داشته باشید که انوتیشن‌ها به صورت متا‌دیتای اضافی ذخیره می‌شوند و برخی از ابزارها و مکانیزم‌های Kubernetes از این انواع اطلاعات برای ایجاد تصمیمات یا پردازش‌های خاص استفاده می‌کنند.