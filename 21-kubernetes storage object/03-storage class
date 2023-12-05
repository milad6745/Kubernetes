# storage class
در Kubernetes، `StorageClass` یک منبع تخصیص داده به عنوان PersistentVolume (PV) را تعریف می‌کند. `StorageClass` به کاربران اجازه می‌دهد تا برای استفاده از ذخیره‌سازی دائمی بدون نیاز به تنظیمات مستقیم بر روی `PersistentVolume` استفاده کنند.

ویژگی‌های مهم `StorageClass` عبارتند از:

1. **Provisioner:** نحوه تأمین فضای ذخیره‌سازی. برای مثال، یک provisioner می‌تواند فضای ذخیره‌سازی را در یک Cloud Provider فراهم کند (برای مثال، در Azure یا AWS) یا از نوع محلی (بر اساس فایل‌های محلی) باشد.

2. **Parameters:** تنظیمات اختیاری که به provisioner ارسال می‌شوند.

3. **Reclaim Policy:** نحوه مدیریت `PersistentVolume` ها بعد از حذف `PersistentVolumeClaim`. می‌تواند به سه حالت "Retain" (نگهداری داده‌ها)، "Delete" (حذف داده‌ها) یا "Recycle" (پاکسازی داده‌ها) تنظیم شود.

### مثال ایجاد یک StorageClass:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
reclaimPolicy: Retain
```

در این مثال:

- `provisioner`: نشان‌دهنده provisioner مورد استفاده (در اینجا `kubernetes.io/aws-ebs` برای AWS EBS).
  
- `parameters`: تنظیمات اختیاری که به provisioner ارسال می‌شوند (در اینجا `type` با مقدار `gp2`).

- `reclaimPolicy`: نحوه مدیریت `PersistentVolume` بعد از حذف `PersistentVolumeClaim` (در اینجا `Retain`).

می‌توانید از `StorageClass` در `PersistentVolumeClaim` استفاده کنید تا به صورت خودکار `PersistentVolume` برای شما ایجاد شود. به عبارت دیگر، `StorageClass` به کاربران این امکان را می‌دهد که بدون نگرانی از جزئیات فیزیکی ذخیره‌سازی، از منابع تخصیص داده شده به آنها استفاده کنند.
