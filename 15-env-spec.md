
منظور از env چیست؟

 در منیفست کوبرنتیز به شما امکان می‌دهد متغیرهای محیطی را برای کانتینرهای درون پاد تعریف کنید. این متغیرها می‌توانند برای پیکربندی اپلیکیشن‌ها، تنظیمات پایگاه داده، کلیدهای API و سایر مقادیر مورد نیاز استفاده شوند.


### مثال:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-container
        image: my-web-app:latest
        ports:
        - containerPort: 80
       env:
        - name: DB_HOST
          value: "mysql-service"
        - name: DB_PORT
          value: "3306"
        - name: DB_USER
          value: "root"
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
        - name: DB_NAME
          value: "mydatabase"
```

### توضیحات:

1. **نوع منیفست:** این منیفست مربوط به یک `Deployment` است که سه نمونه (پاد) از اپلیکیشن وب (`web-app`) را اجرا می‌کند.

2. **کانتینر:** کانتینر `web-container` از تصویری به نام `my-web-app:latest` استفاده می‌کند و روی پورت 80 اجرا می‌شود.

3. **متغیرهای محیطی:**
   - `DB_HOST`: آدرس سرویس MySQL را به عنوان `mysql-service` تنظیم کرده‌ایم.
   - `DB_PORT`: پورت MySQL روی `3306` تنظیم شده است.
   - `DB_USER`: نام کاربری پایگاه داده به عنوان `root` تنظیم شده است.
   - `DB_PASSWORD`: رمز عبور پایگاه داده از یک `Secret` به نام `mysql-secret` گرفته می‌شود. این کار برای حفاظت از رمز عبور در برابر نشت ناخواسته است.
   - `DB_NAME`: نام پایگاه داده به عنوان `mydatabase` تنظیم شده است.

### فایل `Secret` برای `DB_PASSWORD`:

اگر بخواهید `DB_PASSWORD` را به صورت امن ذخیره کنید، باید از یک `Secret` استفاده کنید. در اینجا یک مثال برای ساختن `Secret`:

```bash
kubectl create secret generic mysql-secret --from-literal=password='mysecretpassword'
```

این دستور یک `Secret` به نام `mysql-secret` ایجاد می‌کند که دارای یک کلید `password` با مقدار `mysecretpassword` است.

### نتیجه‌گیری:

در این مثال، ما از `env` برای پیکربندی کانتینر اپلیکیشن وب استفاده کردیم تا به یک پایگاه داده MySQL متصل شود. این کار باعث می‌شود که بدون نیاز به تغییر کد، تنظیمات محیطی را به راحتی تغییر دهید. علاوه بر این، با استفاده از `Secret`، امنیت داده‌های حساس نیز حفظ می‌شود. 

اگر نیاز به توضیحات بیشتر یا مثال‌های دیگری دارید، خوشحال می‌شوم کمک کنم!
