# reclaim policy

Reclaim Policy (سیاست بازیابی) یک ویژگی در تعریف `PersistentVolume` (PV) در Kubernetes است که تعیین می‌کند که پس از حذف یک `PersistentVolumeClaim` (PVC)، فضای ذخیره‌سازی متصل به PV چگونه مدیریت شود. سیاست بازیابی با استفاده از مقدار `reclaimPolicy` تعیین می‌شود و می‌تواند یکی از سه مقدار زیر را داشته باشد:

1. **Retain:**
   - در این حالت، PV تلاش می‌کند تا فضای ذخیره‌سازی را در اختیار نگه دارد پس از حذف PVC.
   - هیچ عملیات پاک‌سازی انجام نمی‌شود و PV همچنان به عنوان یک `PersistentVolume` در دسترس خواهد بود. این می‌تواند برای مواردی مناسب باشد که نیاز به نگه‌داری داده‌ها پس از حذف یک PVC دارید.

2. **Delete:**
   - در این حالت، هنگامی که PVC حذف می‌شود، تمام فضای ذخیره‌سازی متصل به PV نیز حذف می‌شود.
   - این مقدار مناسب برای مواردی است که بعد از حذف PVC، داده‌ها نیاز به حفظ ندارند و می‌توانید مقدار پیش‌فرض باشد.

3. **Recycle:**
   - توجه: Deprecated (پیشنهاد می‌شود از آن استفاده نشود)
   - در حالت Recycle، سیستم می‌کوشد تا فضای ذخیره‌سازی PV را بازیابی و پاک‌سازی کند. این سیاست در ورژن‌های جدید Kubernetes به دلیل نقاط ضعف و ایراداتی که داشته است، deprecated شده است.

### مثال:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  reclaimPolicy: Retain
  hostPath:
    path: "/mnt/data"
```

در این مثال، `reclaimPolicy` به `Retain` تنظیم شده است، که به معنای این است که PV تلاش می‌کند تا پس از حذف PVC، فضای ذخیره‌سازی را در اختیار نگه دارد.
