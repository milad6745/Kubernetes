# Access Mode

در Kubernetes، Access Mode یا حالت دسترسی نحوه ارتباط بین یک `PersistentVolume` (PV) و `PersistentVolumeClaim` (PVC) را مشخص می‌کند. این Access Modes به تعداد کلاینت‌های مختلفی که می‌توانند به یک PV متصل شوند، اشاره دارند. سه نوع Access Mode اصلی وجود دارد:

**ReadWriteOnce (RWO):**

فقط یک کلاینت (یک Pod) به صورت همزمان می‌تواند به PV دسترسی داشته باشد.
این Access Mode برای سناریوهایی که یک Pod به صورت انحصاری به یک دیسک نیاز دارد، مانند فایل‌سیستم‌هاست.

**ReadOnlyMany (ROX):**

چندین کلاینت (چندین Pod) به صورت همزمان می‌توانند از PV خواندن کنند، اما نمی‌توانند در آن نوشته کنند.
این Access Mode مناسب برای سناریوهایی است که چندین Pod نیاز به دسترسی به یک دیسک دائمی (مانند یک مجموعه اطلاعات تنها خواندنی) دارند.

**ReadWriteMany (RWX):**


چندین کلاینت (چندین Pod) به صورت همزمان می‌توانند به PV دسترسی داشته باشند و هم خواندن و هم نوشتن داشته باشند.
این Access Mode معمولاً برای سناریوهایی که چندین Pod به یک دیسک به صورت همزمان نیاز دارند (مثل شبکه‌های فایل) مناسب است.


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
  hostPath:
    path: "/mnt/data"
```

در این مثال، `accessModes` به `ReadWriteOnce` تنظیم شده است، که به معنای این است که تنها یک Pod به صورت همزمان می‌تواند به این `PersistentVolume` دسترسی داشته باشد. این Access Mode برای استفاده در سناریوهایی مانند استفاده انحصاری یک Pod از یک فایل‌سیستم مناسب است.
