# replicaset

در Kubernetes، ReplicaSet یک نوع منبع است که برای تضمین تعداد مشخصی از پادها (Pods) که یک کاربرد (Application) را اجرا می‌کنند، مسئول است. ReplicaSet از Resource Controller (کنترل کننده منابع) استفاده می‌کند تا تعداد پادها را بر اساس تنظیمات مشخصی ایجاد و حفظ کند.

برخلاف Deployment که یک انتزاع بالاتر از ReplicaSet است و قابلیت‌های بیشتری از جمله امکان به‌روزرسانی نسخه و Rollback را فراهم می‌کند، ReplicaSet به طور اصلی برای مدیریت تعداد اجرای یک سرویس یا کاربرد خاص می‌آید.

تعداد پادها توسط ReplicaSet با تنظیم تعداد نمونه (replicas) کنترل می‌شود. اگر تعداد واقعی پادها کمتر از تعداد مشخص شده باشد (به علت خطا یا مشکلات دیگر)، ReplicaSet تلاش می‌کند تا تعداد مورد نظر را ایجاد کند. اگر تعداد واقعی پادها بیشتر از تعداد replicas باشد، ReplicaSet سعی می‌کند تا پادهای اضافه را حذف کند.

مثال ReplicaSet:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: example-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      labels:
        app: example-app
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
```

در این مثال، ReplicaSet `example-replicaset` تعداد 3 نمونه از پادها با برچسب `app=example-app` ایجاد می‌کند. Template مشخص می‌کند چگونه هر پاد باید ایجاد شود (در اینجا از تصویر `nginx:latest` استفاده شده است).

# Example
